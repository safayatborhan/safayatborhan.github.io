+++
author = "Safayat Borhan"
title = "Angular Forms"
date = "2025-08-12"
description = "Angular Forms"
tags = [
    "Angular",
    "Typescript"
]
+++

Angular offers two types of form behaviors. 

Template driven: where forms are created into HTML file and then values are passed to component via ngModel. Uses ngForm and ngModel.

Reactive: where forms are created into component and passed to HTML file using a special directive - [formGroup]. Uses formGroup and formControl.


Template Driven:

sku-template-driven.component.html
```html
<div class="ui raise segment">
    <h3 class="ui header">Demo Form: SKU</h3>
    <form #f="ngForm" (ngSubmit)="onSubmit(f.value)" class="ui form">
        <div class="field">
            <label for="skuInput">SKU</label>
            <input type="text" id="skuInput" placeholder="SKU" name="sku" ngModel>
        </div>

        <button type="submit" class="ui button">Submit</button>
    </form>
</div>
```

sku-template-driven-component.ts
```typescript
import { Component } from '@angular/core';
import { FormsModule } from '@angular/forms';

@Component({
  selector: 'app-sku-template-driven',
  standalone: true,
  imports: [FormsModule],
  templateUrl: './sku-template-driven.component.html',
  styleUrl: './sku-template-driven.component.css'
})
export class SkuTemplateDrivenComponent {
  onSubmit(form: any): void {
    console.log(form);
  }
}
```

Reactive Form:

sku-reactive-form.component.html
```html
<div class="ui raise segment">
    <h3 class="ui header">Demo Form: SKU</h3>
    <!--Angular provider directive [formGroup] to use form created by component-->
    <form [formGroup] = "myForm" (ngSubmit)="onSubmit(myForm.value)" class="ui form">
        <div class="field">
            <label for="skuInput">SKU</label>
            <input type="text" id="skuInput" placeholder="SKU" formControlName="sku">
        </div>

        <button type="submit" class="ui button">Submit</button>
    </form>
</div>
```

sku-reactive-form-component.ts
```typescript
import { Component } from '@angular/core';
import { FormBuilder, FormGroup, ReactiveFormsModule } from '@angular/forms';

@Component({
  selector: 'app-sku-reactive-form',
  standalone: true,
  imports: [ReactiveFormsModule],
  templateUrl: './sku-reactive-form.component.html',
  styleUrl: './sku-reactive-form.component.css'
})
export class SkuReactiveFormComponent {
  myForm: FormGroup;

  constructor(fb: FormBuilder) {
    this.myForm = fb.group({
      'sku': ''
    });
  }

  onSubmit(value: string): void {
    console.log(value);
  }
}

```