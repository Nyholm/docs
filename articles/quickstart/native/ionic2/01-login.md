---
title: Login
default: true
description: This tutorial demonstrates how to add authentication and authorization to an Ionic 2+ app.
budicon: 448
github:
  path: 01-Login
---
<%= include('../_includes/_ionic_setup') %>

## Set Up URL Redirects

Use the `onRedirectUri` method from **auth0-cordova** when your app loads to properly handle redirects after authentication.

```js
// app.component.ts

import { Component } from '@angular/core';
import { Platform } from 'ionic-angular';
import { StatusBar } from '@ionic-native/status-bar';
import { SplashScreen } from '@ionic-native/splash-screen';

import { HomePage } from '../pages/home/home';

// Import Auth0Cordova
import Auth0Cordova from '@auth0/cordova';

@Component({
  templateUrl: 'app.html'
})
export class MyApp {
  rootPage:any = HomePage;

  constructor(platform: Platform, statusBar: StatusBar, splashScreen: SplashScreen) {
    platform.ready().then(() => {
      statusBar.styleDefault();
      splashScreen.hide();

      // Add this function
      (<any>window).handleOpenURL = (url) => {
        Auth0Cordova.onRedirectUri(url);
      };

    });
  }
}
```

## Create an Authentication Service and Configure Auth0

To coordinate authentication tasks, it's best to set up an injectable service that can be reused across the application. This service needs methods for logging users in and out, as well as checking their authentication state. Be sure to replace `YOUR_PACKAGE_ID` with the identifier for your app in the configuration block.

${snippet(meta.snippets.use)}

## Add a Login Control

Add a control to your app to allow users to log in. The control should call the `login` method from the `AuthService`. Start by injecting the `AuthService` in a component.

```js
// src/pages/home/home.ts

import { Component } from '@angular/core';

import { AuthService } from '../../services/auth.service';

@Component({
  selector: 'page-home',
  templateUrl: 'home.html'
})
export class HomePage {

  constructor(public auth: AuthService) {}

}
```

The `AuthService` is now accessible in the view and its `login` method can be called.

```html
<!-- src/pages/home/home.html -->

<div *ngIf="!auth.isAuthenticated()">
  <button ion-button block color="primary" (click)="auth.login()">Log In</button>
</div>
```

## Display Profile Data

Your application will likely require some kind of profile area for users to see their information. Depending on your needs, this can also serve as the place for them to log out of the app.

The `login` method in the `AuthService` includes a call to Auth0 for the authenticated user's profile. This profile information can now be used in a template.

```html
<!-- src/pages/home/home.html -->

<ion-header>
  <ion-navbar>
    <ion-title>
      Home Page
    </ion-title>
  </ion-navbar>
</ion-header>

<ion-content padding>

  <!-- a card with the user's picture and name, plus a logout button -->
  <div *ngIf="auth.isAuthenticated()">
    <ion-card>
      <img [src]="auth.user.picture" />
      <ion-card-content>
        <ion-card-title>{{ auth.user.name }}</ion-card-title>
      </ion-card-content>
    </ion-card>
    <button ion-button block color="primary" (click)="auth.logout()">Logout</button>
  </div>

</ion-content>
```

### Troubleshooting

#### Cannot read property 'isAvailable' of undefined

This means that you're attempting to test this in a browser. At this time you'll need to run this either in an emulator or on a device.

#### Completely blank page when launching the app

This could either mean that you've built the seed project using Ionic 1, or that the device where you are testing it isn't entirely supported by Ionic 2 yet. Be sure to check the console for errors.
