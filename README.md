# NestJS Context

Workaround for getting Request information from a non request-scoped service in NestJs. Initially taken from this [gist](https://gist.github.com/bengry/924a9b93c25d8a98bffdfc0a847f0dbe), and then changed to use [AsyncLocalStorage](https://nodejs.org/api/async_context.html#async_context_new_asynclocalstorage)

# Usage 

### Step 1: Create the Context Service
First, let's create the context service that will be responsible for providing request context information. Create a file called `context.service.ts` and add the following code:

```typescript
// context.service.ts

import { Injectable } from '@nestjs/common';
import { RequestContext } from 'nestjs-context';

@Injectable()
export class ContextService {
  request(): Request {
    return RequestContext.currentContext.req as unknown as Request;
  }

  response(): Response {
    return RequestContext.currentContext.res as unknown as Response;
  }
}
```

### Step 2: Create the Context Module
Now, let's create the `ContextModule` that will import and provide the context service. Create a file called `context.module.ts` and add the following code:
```typescript
// context.module.ts

import { DynamicModule, Module } from '@nestjs/common';
import { RequestContextModule } from 'nestjs-context';

import { ContextService } from './context.service';

@Module({
  imports: [RequestContextModule],
  providers: [ContextService],
  exports: [ContextService],
})
export class ContextModule {
  static forRoot(): DynamicModule {
    return {
      global: true,
      module: this,
    };
  }
}
```
Remember to import and configure the ContextModule correctly in the main application module or the modules where you want to use the context service:

```typescript	
import { Module } from '@nestjs/common';

import { ContextModule } from './context.module';

@Module({
  imports: [ContextModule.forRoot()],
})
export class AppModule {}
```

# Release Notes

### 10.0.0

Updated NestJs peer dependencies to `10.x`
