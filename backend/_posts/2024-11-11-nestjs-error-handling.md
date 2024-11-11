---
layout: post
title: "Implementing Custom Error Handling  in NestJS"
sitemap: true
image: /assets/img/backend/nestjs-error-handling/thumbnail.png
hide_last_modified: true
---

- toc
{:toc}

Error handling is essential for system maintainability. In my experience as a front-end developer, I frequently relied on backend developers to investigate specific errors, especially in mobile applications where detailed developer consoles are less accessible. To address this, I created a custom error-handling method with the following three objectives:

1. Enable handling by error code (distinct from status code).
2. Provide a quick understanding of the error context.
3. Facilitate backend error tracking via structured logs.

![Full-width image](/assets/img/backend/nestjs-error-handling/error-handling-architecture.png "Error Handling")
{:.lead width="100%" height="auto" loading="lazy"}

## Custom Exception Implementation

### Base Exception

I wanted the base exception to include an error code (separate from the status code) that consists of a section identifier and a unique code, like `AUTH01`. Additionally, I embedded the API path, timestamp, and a predefined message for streamlined logging.

```tsx
// custom.base.exception.ts

export abstract class CustomBaseException extends HttpException {
  protected constructor(
    errorCode: string,
    statusCode: number,
    message: string,
  ) {
    super(message, statusCode);
    this.errorCode = errorCode;
    this.timestamp = new Date().toISOString();
  }

  errorCode: string;

  timestamp: string;
}

```

`CustomBaseException` extends `HttpException` (NestJS’s default error handler) and includes error-related data fields such as `errorCode`, `statusCode`, `timestamp`, `path`, and `message`. The `abstract` and `protected` modifiers prevent instantiation of `CustomBaseException` directly, enforcing the use of subclass constructors only.

### Custom Error Codes

To manage error codes systematically, I used enums organized by domain (e.g., authentication). Each error within a domain has a unique code, making it easy to identify the origin and type of error at a glance.

```tsx
// error-constant/error.code.ts

export enum AuthErrorCodeEnum {
  UserNotFound = 'AUTH01',
  PasswordNotMatch = 'AUTH02',
  PasswordHashFailed = 'AUTH03',
  EmailAlreadyExists = 'AUTH04',
	
	// ...etc
}
```

### Custom Error Message

Error messages are mapped to error codes, providing a consistent message for each error. This approach minimizes risks of typographical errors and eliminates the need to define messages dynamically each time an error is thrown.

```tsx
// error-constant/error.message.ts

type AuthErrorMessageType = {
  readonly [K in AuthErrorCodeEnum]: string;
};

export const AuthErrorMessage: AuthErrorMessageType = {
  [AuthErrorCodeEnum.LogoutFailed]: 'Logout failed',
  [AuthErrorCodeEnum.UserNotFound]: 'User not found',
  [AuthErrorCodeEnum.PasswordNotMatch]: 'Password not match',
  [AuthErrorCodeEnum.PasswordHashFailed]: 'Password hash failed',
  [AuthErrorCodeEnum.EmailAlreadyExists]: 'Email already exists',

	// ...etc
};
```

### Custom Exception

With the base exception, error codes, and messages defined, we can now create custom exception classes by extending the base class and passing specific data.

```tsx
// custom-exception/auth.exception.ts

export class UserNotFoundException extends CustomBaseException {
  constructor() {
    super(
      AuthErrorCodeEnum.UserNotFound,
      HttpStatus.NOT_FOUND,
      AuthErrorMessage[AuthErrorCodeEnum.UserNotFound],
    );
  }
}

// ..other exceptions
```

Using this setup, custom exceptions can be instantiated by simply throwing a new exception, with clear error codes, messages, and HTTP status mappings for each case. Please check beneath to use appropriate status.

```tsx
export declare enum HttpStatus {
    CONTINUE = 100,
    SWITCHING_PROTOCOLS = 101,
    PROCESSING = 102,
    EARLYHINTS = 103,
    OK = 200,
    CREATED = 201,
    ACCEPTED = 202,
    NON_AUTHORITATIVE_INFORMATION = 203,
    NO_CONTENT = 204,
    RESET_CONTENT = 205,
    PARTIAL_CONTENT = 206,
    AMBIGUOUS = 300,
    MOVED_PERMANENTLY = 301,
    FOUND = 302,
    SEE_OTHER = 303,
    NOT_MODIFIED = 304,
    TEMPORARY_REDIRECT = 307,
    PERMANENT_REDIRECT = 308,
    BAD_REQUEST = 400,
    UNAUTHORIZED = 401,
    PAYMENT_REQUIRED = 402,
    FORBIDDEN = 403,
    NOT_FOUND = 404,
    METHOD_NOT_ALLOWED = 405,
    NOT_ACCEPTABLE = 406,
    PROXY_AUTHENTICATION_REQUIRED = 407,
    REQUEST_TIMEOUT = 408,
    CONFLICT = 409,
    GONE = 410,
    LENGTH_REQUIRED = 411,
    PRECONDITION_FAILED = 412,
    PAYLOAD_TOO_LARGE = 413,
    URI_TOO_LONG = 414,
    UNSUPPORTED_MEDIA_TYPE = 415,
    REQUESTED_RANGE_NOT_SATISFIABLE = 416,
    EXPECTATION_FAILED = 417,
    I_AM_A_TEAPOT = 418,
    MISDIRECTED = 421,
    UNPROCESSABLE_ENTITY = 422,
    FAILED_DEPENDENCY = 424,
    PRECONDITION_REQUIRED = 428,
    TOO_MANY_REQUESTS = 429,
    INTERNAL_SERVER_ERROR = 500,
    NOT_IMPLEMENTED = 501,
    BAD_GATEWAY = 502,
    SERVICE_UNAVAILABLE = 503,
    GATEWAY_TIMEOUT = 504,
    HTTP_VERSION_NOT_SUPPORTED = 505
}
```

### Custom Exception Filter

To centralize error handling, I implemented a custom exception filter. This filter catches errors, formats the response, and logs necessary details to the console.

```tsx
// custom.exception.filter.ts

@Catch()
export class CustomExceptionFilter implements ExceptionFilter {
  private readonly logger = new Logger(CustomExceptionFilter.name);

  catch(exception: Error | CustomBaseException, host: ArgumentsHost): void {
    const ctx = host.switchToHttp();
    const request = ctx.getRequest();
    const response = ctx.getResponse();

    const res =
      exception instanceof CustomBaseException
        ? exception
        : new UnexpectedException();

    const errorResponse = {
      errorCode: res.errorCode,
      statusCode: res.getStatus(),
      message: res.message,
      timestamp: res.timestamp,
      path: request.url,
    };

    this.logger.error({
      ...errorResponse,
      ...(!(exception instanceof CustomBaseException) && {
        stack: exception.stack,
        originalError: exception,
      }),
    });

    response.status(res.getStatus()).json(errorResponse);
  }
}
```

The `CustomExceptionFilter` retrieves request and response data and logs all error information for easy review. Non-custom errors are wrapped in a generic `UnexpectedException` to ensure unhandled errors are managed consistently.

Finally, in `main.ts`, I register the custom exception filter globally:

```tsx
// main.ts

async function bootstrap() {
  const app = await NestFactory.create(AppModule);
  app.useGlobalFilters(new CustomExceptionFilter());
  await app.listen(3000);
}
bootstrap();
```

### Usage in Code

Here’s an example of how to use the custom exceptions in a service method:

```tsx
async validateAuthentication(loginDto: LocalLoginRequestDto) {
  const user = await this.usersService.findUserForAuth(loginDto.email);
	if (!user) {
     throw new UserNotFoundException();
  }
  
  // ...other logic
}
```

## Error Response

![Full-width image](/assets/img/backend/nestjs-error-handling/error-response.png "Error Response")
{:.lead width="100%" height="auto" loading="lazy"}

Upon encountering an error, the response includes details such as the error code, message, path, and timestamp, making it easy to identify and resolve issues without memorizing specific error codes.

## Considerations

Using error codes can improve error tracking without communication overhead, but if working within a team, be mindful of sharing error codes promptly to maintain clarity.