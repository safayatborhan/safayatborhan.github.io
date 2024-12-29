+++
author = "Safayat Borhan"
title = "Angular Loosely Coupled Architecture"
date = "2024-12-29"
description = "Angular Loosely Coupled Architecture"
tags = [
    "Angular",
    "Typescript",
    "Subscription",
    "Subject"
]
+++

When we write software, we tend to make our architecture as loosely coupled as possible. Sometimes we do overengineer due to some other tradeoffs. But we will be discussing today on very basic publisher subscriber pattern in angular and how it can help in real life. Let's jump into the problem. 

Let's consider a scenario when we add item in a typescript array under an angular component and we have another component to view the list items. When we are adding to typescript array, at the same time we would like to show that item into the list component. A cowboy solution can be getting the items from the list in constructor and make it available for HTML. But you already can get the impression that, this is not a robust solution as it has a direct between these components. And the list view constructor will not get invoked untill we render the view. And as this is SPA, the list view can be under same DOM. So, basically it won't work as expected. How can we solve this problem? 

We can publish an event right away after adding the element into typescript array. Here is an example: 

```typescript
import { Subject } from "rxjs";
import { Platform } from "./platforms.model";
import { Injectable } from "@angular/core";

@Injectable({providedIn: 'root'})
export class PlatformService {
    private platforms: Platform[] = [
        new Platform(1, 'FSMOne', 'Singapore based broker'),
        new Platform(2, 'Moomo', 'US based broker'),
        new Platform(3, 'Interactive Broker', 'US based broker')
    ];

    platformsChanged = new Subject<Platform[]>();

    addPlatform(name: string, description: string) {
        var lastId = this.platforms.length === 0 ? 1 : this.platforms[this.platforms.length - 1].id;
        var platform = new Platform(++lastId, name, description);
        this.platforms.push(platform);
        this.platformsChanged.next(this.platforms.slice());
    }

    getPlatforms() {
        return this.platforms;
    }

    getPlatform(id: number): Platform | undefined {
        var platform: Platform | undefined = this.platforms.find(p => {
            return p.id === id;
        });

        return platform;
    }

    updatePlatform(platform: Platform) {
        var platformToUpdate: Platform | undefined = this.platforms.find(p => {
            return p.id === platform.id;
        });

        if (platformToUpdate != undefined)
        {
            platformToUpdate.name = platform.name;
            platformToUpdate.description = platform.description;
        }
        
        this.platformsChanged.next(this.platforms.slice());
    }

    deletePlatform(id: number) {
        var platform: Platform | undefined = this.platforms.find(p => {
            return p.id === id;
        });

        if (platform != undefined)
        {
            this.platforms.splice(this.platforms.indexOf(platform), 1);
        }
        
        this.platformsChanged.next(this.platforms.slice());
    }
}
```

In angular we use `Subject` to raise an event. As you can see in `addPlatform` method, after pushing the element into array, we are invoking `next` method for the `platformsChanged` subject. It raised an event and this can be used by any clients. 

In our list component, we can just subscribe to that event and react whenever there is a new event: 

```typescript
import { Component, OnDestroy, OnInit } from '@angular/core';
import { PlatformItemComponent } from "./platform-item/platform-item.component";
import { Platform } from '../platforms.model';
import { PlatformService } from '../platforms.service';
import { NgFor } from '@angular/common';
import { Subscription } from 'rxjs';

@Component({
  selector: 'app-platforms-list',
  standalone: true,
  imports: [PlatformItemComponent, NgFor],
  templateUrl: './platforms-list.component.html',
  styleUrl: './platforms-list.component.css'
})
export class PlatformsListComponent implements OnInit, OnDestroy {
  platforms: Platform[] = [];
  private platformChangedSubscription!: Subscription;

  constructor(private platformService: PlatformService) { }

  ngOnInit(): void {
    this.platforms = this.platformService.getPlatforms();
    this.platformChangedSubscription = this.platformService.platformsChanged.subscribe((platforms: Platform[]) => {
      this.platforms = platforms;
    });
  }

  ngOnDestroy(): void {
    this.platformChangedSubscription.unsubscribe();
  }
}

```

Don't forget the unsubsribe it in `ngOnDestroy`. 

This way, we have a loosely coupled solution and the list component is always up to date with the changes done by some other component.