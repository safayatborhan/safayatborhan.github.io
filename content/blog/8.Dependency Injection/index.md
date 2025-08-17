+++
author = "Safayat Borhan"
title = "Angular Dependency Injection"
date = "2025-08-17"
description = "Angular Dependency Injection"
tags = [
    "Angular",
    "Typescript"
]
+++

We will be implementing DI in angular today. 

user.service.ts
```typescript
import { Injectable } from "@angular/core";

@Injectable({
    providedIn: 'root'
})
export class UserService {
    user: any;

    setUser(newUser: any) {
        this.user = newUser;
    }

    getUser(): any {
        return this.user;
    }
}
```

Take a look at `@Injectable()` decorator and `providedIn: 'root'`. This is a new way to inject depedency globally. Previously we used to inject the class we want to use into other classes into app.module.ts. But from angular 14, this feature came in. We do not need to add this class into app module provider. But if don't want a class to be injectable globally, we don't need `providedIn: 'root'`. We can add that class into the corresponding component provider.

di-playground.component.ts
```typescript
export class DiPlaygroundComponent {
  userName!: string;
  
  constructor(private userService: UserService) {
  }

  signIn(): void {
    this.userService.setUser({
      name: 'Safayat Borhan'
    });
    
    this.userName = this.userService.getUser().name;
    console.log(this.userName);
  }
}
```

di-playground.component.html
```html
<div>
    <p *ngIf="userName" class="welcome">Welcome:  {{ userName }}</p>
    <button (click)="signIn()" class="ui button">Sign In</button>
</div>
```