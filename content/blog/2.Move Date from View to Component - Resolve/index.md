+++
author = "Safayat Borhan"
title = "Move Data from View to Component - Resolve"
date = "2025-07-20"
description = "Move Data Between Components - @Input"
tags = [
    "Angular",
    "Typescript"
]
+++

Resolve is the simplest way to pass parameter from html to typescript component file. Example: 

app.component.html:
```html
<form class="ui large form segment">
    <h3 class="ui header">Add a link</h3>

    <div class="field">
        <label for="title">Title: </label>
        <input name="title" id="title" #newtitle>
    </div>
    <div class="field">
        <label for="link">Link: </label>
        <input name="link" id="link" #newlink>
    </div>

    <button (click)="addArticle(newtitle, newlink)" class="ui positive right floated button">Submit link</button>
</form>
```

Here, resolves are: #newtitle, #newlink. We are passing this by method parameter. 

app.component.ts:
```typescript
import { Component } from '@angular/core';

@Component({
  selector: 'app-root',
  standalone: true,
  templateUrl: './app.component.html',
  styleUrl: './app.component.css'
})
export class AppComponent {
  title = 'reddit-clone';

  addArticle(title: HTMLInputElement, link: HTMLInputElement): boolean {
    console.log(`${title.value}, ${link.value}`);
    return false;
  }
}

```