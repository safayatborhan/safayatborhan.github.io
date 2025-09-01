+++
author = "Safayat Borhan"
title = "Angular RxJs"
date = "2025-09-01"
description = "Angular RxJs"
tags = [
    "Angular",
    "Typescript"
]
+++

To convert an array to a stream: 

array: 
```typescript
users = [
    { id: '1', name: 'John', isActive: true },
    { id: '2', name: 'Jack', isActive: true },
    { id: '3', name: 'Mike', isActive: true }
  ];  
```
stream:
```typescript
users$ = of(this.users);
```

To use this stream, the client needs to subscribe to it. Anyone can do it and get notified whenever any change happens to this stream. But if only UI component needs to subscribe it, it can be done easily by `async pipe`. 
```html
<div *ngFor="let user of users$ | async">
    {{ user.name }}
</div>
```

If we want to get only names from this stream, we have two options. We can either subscribe to the stream and use raw js to get the values from stream or we can use built in functions that can process data stream. We should always avoid subscribing since this is expensive. 
Using Pipe: 

```typescript
usernames$ = this.users$.pipe(map((users) => 
    users.map((user) => 
      user.name)));
```
Pipe support multiple functions. Example:
```typescript
filteredUsers$ = this.users$.pipe(
    filter((users) => 
      users.every((user) => 
        user.isActive)), 
    map((users) => 
      users.map((user) => 
        user.name)));
```

We can merge multiple stream result into single stream: 
```typescript
dataCombined$ = combineLatest([
      this.users$,
      this.usernames$,
      this.filteredUsers$
    ]).pipe(map(([users, usernames, filteredusers]) => ({
      users,
      usernames,
      filteredusers
    })));
```
```html
<div *ngIf="dataCombined$ | async as data">
    <div *ngFor="let user of data.users">
        Data from users stream: {{ user.name }}
    </div>
    <div *ngFor="let user of data.usernames">
        Data from usernames stream: {{ user }}
    </div>
    <div *ngFor="let user of data.filteredusers">
        Data from filtered users stream: {{ user }}
    </div>
</div>
```

BehaviorSubject: BehaviorSubject is a stream where we can update the stream. Typically, we set null to a stream before getting values from backend API. After getting values, we set those values to stream. 

```typescript
usersBehaviorSubject$ = new BehaviorSubject<{ id: string; name: string } | null>(null);
```
```typescript
ngOnInit(): void {
      // This will wait for 5 second mimicing the API call. Then set value to usersBehaviorSubject.
      setTimeout(() => {
        this.usersBehaviorSubject$.next({id: '1', name: 'Safayat'});
      }, 5000);

      // As we subscribed the usersBehaviorSubject, we will get value into console when there are any data and it will come after 2 second of bootstraping.
      this.usersBehaviorSubject$.subscribe((user) => console.log(user));      
    }
```
```html
<div *ngIf="usersBehaviorSubject$ | async as user">{{ user.name }}</div>
```