+++
author = "Safayat Borhan"
title = "Angular Directives"
date = "2025-08-07"
description = "Angular Directives"
tags = [
    "Angular",
    "Typescript"
]
+++

Directives offers dynamic behavior into HTML component. The directives should be imported into component first. Below there are few examples of build in directives:

ngIf:
```html
<div *ngIf="false">
  <P>You should never see it.</P>
</div>
<div *ngIf="true">
  <P>You should always see it.</P>
</div>
```

ngIf with multiple condition:
```html
<div *ngIf="switchSelector == 'A'">Var is A</div>
<div *ngIf="switchSelector == 'B'">Var is B</div>
<div *ngIf="switchSelector != 'A' && switchSelector != 'B'">Var is something else</div>
```

ngSwitch: 
```html
<div [ngSwitch]="switchSelector">
  <div *ngSwitchCase="'A'">Var is A</div>
  <div *ngSwitchCase="'B'">Var is B</div>
  <div *ngSwitchDefault>Var is something else</div>
</div>
```

ngStyle: 
```html
<div [style.background-color]="'yellow'">
  Uses fixed yellow background
</div>

<div [ngStyle]="{ 'color': 'white', 'background-color': 'blue' }">
  Uses fixed white text on blue background
</div>
```

ngClass:
```html
<div [ngClass]="{bordered: isBordered}">
  Using object literal.
</div>
```