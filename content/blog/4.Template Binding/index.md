+++
author = "Safayat Borhan"
title = "Angular Template Binding"
date = "2025-08-04"
description = "Angular Template Binding"
tags = [
    "Angular",
    "Typescript"
]
+++

Template binding is the simplest way to show typescript component value into html page: 

product.model.ts:
```typescript
export class Product {
    constructor(
        public sku: string,
        public name: string,
        public imageUrl: string,
        public department: string[],
        public price: number
    ) {

    }
}
```

app.component.ts:
```typescript
export class AppComponent {
  product: Product;

  constructor() {
    this.product = new Product(
      "NICEHAT",
      "A nice black hat",
      "/resources/images/products/black-hat.jpg",
      ["Men", "Accessories", "Hats"],
      29.99
    );
  }

  title = 'inventory-app';
}
```

app.component.html
```html
<div class="inventory-app">
  <h1>{{ product.name }}</h1>
  <span>{{ product.sku }}</span>
</div>
```
