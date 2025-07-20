+++
author = "Safayat Borhan"
title = "Move Data Between Components - @Input"
date = "2025-07-20"
description = "Move Data Between Components - @Input"
tags = [
    "Angular",
    "Typescript"
]
+++

@Input is the simplest way to communicate across components. Let's imagine, we have two components - user-list and user-item. Inside user-list, we want to show list of users and we want user-item to handle the showing mechanism. Here is the simplest way we can do it:

```typescript
// user-item.component.ts

import { Component, Input } from '@angular/core';

@Component({
  selector: 'app-user-item',
  standalone: true,
  imports: [],
  templateUrl: './user-item.component.html',
  styleUrl: './user-item.component.css'
})
export class UserItemComponent {
  @Input() name: string = '';

  constructor() {
  }
}

// user-item.component.html
<p>Hello {{ name }}</p>

// user-list.component.ts
import { NgFor } from '@angular/common';
import { Component } from '@angular/core';
import { UserItemComponent } from "../user-item/user-item.component";

@Component({
  selector: 'app-user-list',
  standalone: true,
  imports: [NgFor, UserItemComponent],
  templateUrl: './user-list.component.html',
  styleUrl: './user-list.component.css'
})
export class UserListComponent {
  names: string[];

  constructor() {
    this.names = ['Alice', 'Bob', 'Charlie'];
  }
}


// user-list.component.html
<ul>
    <li *ngFor="let name of names">
        <app-user-item [name]="name"></app-user-item> // Here, [name] means, it's expective an @Input variable from the app-user-item component.
    </li>
</ul>
```