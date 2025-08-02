+++
author = "Safayat Borhan"
title = "Reusable Component to Add and Show List"
date = "2025-08-02"
description = "Reusable Component to Add and Show List"
tags = [
    "Angular",
    "Typescript"
]
+++

We will try to create few components that can be used to create and article and show them into a different reusable component. For data communication, we will use both Resolve and Input. Here is the model: 

article.component.ts:
```typescript
export class ArticleComponent implements OnInit {
  @HostBinding('attr.class') cssClass = 'row';
  @Input() article!: Article;

  constructor() {    
  }
  
  ngOnInit(): void {    
  }

  voteUp(): boolean {
    this.article.voteUp();
    return false;
  }

  voteDown(): boolean {
    this.article.voteDown();
    return false;
  }

}
```

article.component.ts:
```typescript
export class ArticleComponent implements OnInit {
  @HostBinding('attr.class') cssClass = 'row';
  @Input() article!: Article;

  constructor() {    
  }
  
  ngOnInit(): void {    
  }

  voteUp(): boolean {
    this.article.voteUp();
    return false;
  }

  voteDown(): boolean {
    this.article.voteDown();
    return false;
  }
}
```
Take a look at `HostBinding`. This is a dynamic way to add css class globally for this component. And `@Input` is added here to take the input data from parent component. A very important concept of returning false from the boolean method is to prevent reloading the page after click event. If we do not pass boolean, the click event propagates all the way up to parent component and page refreshes. 

article.component.html
```html
<div class="four wide column center aligned votes">
    <div class="ui statistics">
        <div class="value">
            {{ article.votes }}
        </div>
        <div class="label">
            Points
        </div>
    </div>
</div>

<div class="twelve wide column">
    <a class="ui large header" href="{{article.link}}">
        {{ article.title }}
    </a>

    <div class="meta">({{article.domain()}})</div>

    <ul class="ui big horizontal list voters">
        <li class="item">
            <a href (click)="voteUp()">
                <i class="arrow up icon">
                    upvote
                </i>
            </a>
        </li>
        <li class="item">
            <a href (click)="voteDown()">
                <i class="arrow down icon">
                    downvote
                </i>
            </a>
        </li>
    </ul>
</div>
```

Now from the callr side that means, 
app.component.ts:
```typescript
export class AppComponent {
  title = 'reddit-clone';
  articles: Article[];

  constructor() {
    this.articles = [
      new Article(
      'Angular',
      'http://angular.io',
      3),
      new Article(
      'Fullstack',
      'http://angular.io',
      2),
      new Article(
      'Angular Homepage',
      'http://angular.io',
      1)
  ];
  }

  addArticle(title: HTMLInputElement, link: HTMLInputElement): boolean {
    this.articles.push(new Article(title.value, link.value, 0));
    title.value = '';
    link.value = '';
    return false;
  }

  sortedArticles(): Article[] {
    return this.articles.sort((a: Article, b: Article) => b.votes - a.votes);
  }
}
```

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

<div class="ui grid posts">    
    <app-article *ngFor="let article of sortedArticles()" [article]="article"></app-article>
</div>
```