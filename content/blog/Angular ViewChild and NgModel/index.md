+++
author = "Safayat Borhan"
title = "ViewChild and NgModel, how are they different?"
date = "2024-12-22"
description = "ViewChild and NgModel, how are they different?"
tags = [
    "Angular",
    "Typescript"
]
+++

In Software Engineering world, everything is about moving data from one place to another. This place(!) can be a backend service or an angular component. If you think about it thoroughly, you might have observed that you have been doing this thousands of times. We have been doing the same thing again and again just using different approach with different algorithms or design patterns to solve different problems. But the main intention is to move data from one place to another. Today we will be doing the same thing again from angular perspective.  

What do we want?  
- We want to move data from angular HTML component to angular typescript component.  

And there can be more than one way to do this.  
- ViewChild 
- NgModel 

Let’s discuss on how ViewChild helps us to pass HTML data to Typescript component first. 
```html
<div class="row">
    <div class="col-xs-12">
        <form>
            <div class="row">
                <div class="col-sm-12 form-group">
                    <label for="name">Name</label>
                    <input type="text" id="name" class="form-control" [value]="platform?.name" #nameInput>
                </div>
                <div class="col-sm-12 form-group">
                    <label for="description">Description</label>
                    <input type="text" id="description" class="form-control" [value]="platform?.description" #descriptionInput>
                </div>
            </div>
            <div class="row">
                <div class="col-xs-12">
                    <button class="btn btn-success" type="button" (click)="onSavePlatform()">Save</button>
                </div>
            </div>
        </form>
    </div>        
</div>
```
Here you can see, I have put a reference for name and description. The reason behind this is, I want to have a reference in my typescript component, where I can get the data. My typescript component would look like this: 
```typescript
import { Component, ElementRef, OnInit, ViewChild } from '@angular/core';
import { Platform } from '../platforms.model';
import { ActivatedRoute } from '@angular/router';
import { PlatformService } from '../platforms.service';

@Component({
  selector: 'app-platform-upsert',
  standalone: true,
  imports: [],
  templateUrl: './platform-upsert.component.html',
  styleUrl: './platform-upsert.component.css'
})
export class PlatformUpsertComponent implements OnInit {
  platform!: Platform | undefined;
  id!: number;
  editMode = false;
  
  constructor(private route: ActivatedRoute, private platformService: PlatformService) { }

  ngOnInit(): void {
    this.route.params.subscribe(params => {
      this.id = +params['id'];
      this.editMode = params['id'] != null;
      this.platform = this.platformService.getPlatform(this.id);
    });
  }

  @ViewChild('nameInput', { static: false }) nameInputRef!: ElementRef;
  @ViewChild('descriptionInput', { static: false }) descriptionInputRef!: ElementRef;

  onSavePlatform() {
    var name = this.nameInputRef.nativeElement.value;
    var description = this.descriptionInputRef.nativeElement.value;
    
    if (this.editMode) 
    {
      var platform = new Platform(this.id, name, description);
      this.platformService.updatePlatform(platform);
    } 
    else 
    {
      this.platformService.addPlatform(name, description);
    }
  } 
  
}

```

As you can see here, in `OnSavePlatform` method, I am using the native element reference to get data. This is how ViewChild works. 

I could do this in a different way though. I can even control the data from typescript component. 

There is no right or wrong approach. Just a different way to solve a same problem. Let's discuss how `NgModel` would fit in here now.

The HTML will look like this: 
```html
<div class="row">
    <div class="col-xs-12">
        <form (ngSubmit)="onSavePlatform(platformForm)" #platformForm="ngForm">
            <div class="row">
                <div class="col-sm-12 form-group">
                    <label for="name">Name</label>
                    <input type="text" id="name" class="form-control" name="name" ngModel required>
                </div>
                <div class="col-sm-12 form-group">
                    <label for="description">Description</label>
                    <input type="text" id="description" class="form-control" name="description" ngModel required>
                </div>
            </div>
            <div class="row">
                <div class="col-xs-12">
                    <button class="btn btn-success" type="submit" [disabled]="!platformForm.valid">{{ editMode ? 'Update' : 'Add' }}</button>
                </div>
            </div>
        </form>
    </div>        
</div>
```
Just removed the html references and put `ngModel`. And the typescript component: 

```typescript
import { Component, OnInit, ViewChild } from '@angular/core';
import { Platform } from '../platforms.model';
import { ActivatedRoute } from '@angular/router';
import { PlatformService } from '../platforms.service';
import { FormsModule, NgForm } from '@angular/forms';

@Component({
  selector: 'app-platform-upsert',
  standalone: true,
  imports: [FormsModule],
  templateUrl: './platform-upsert.component.html',
  styleUrl: './platform-upsert.component.css'
})
export class PlatformUpsertComponent implements OnInit {
  platform!: Platform | undefined;
  id!: number;
  editMode = false;
  @ViewChild('platformForm', { static: false }) platformForm!: NgForm;
  
  constructor(private route: ActivatedRoute, private platformService: PlatformService) { }

  ngOnInit(): void {
    this.route.params.subscribe(params => {
      this.id = +params['id'];
      this.editMode = params['id'] != null;
      this.platform = this.platformService.getPlatform(this.id);
      this.platformForm.setValue({
        name: this.platform?.name,
        description: this.platform?.description
      });
    });
  }

  onSavePlatform(form: NgForm) {
    const formValue = form.value; 
    
    if (this.editMode) 
    {
      var platform = new Platform(this.id, formValue.name, formValue.description);
      this.platformService.updatePlatform(platform);
    } 
    else 
    {
      this.platformService.addPlatform(formValue.name, formValue.description);
    }

    form.reset();
    this.editMode = false;
  } 
  
}

```

So, here is the key takeway: 
- If we only need html native element data into our component, we can do this by `ViewChild`, but if we also want to update the data from component to html, we will need `NgModel` approach. 
