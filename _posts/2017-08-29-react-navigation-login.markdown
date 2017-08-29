---
layout: post
title:  "React Navigation Authentication"
date:   2017-08-29 07:54:00 -0600
categories: blog
tags: about me
---
## Handling Authentication With React Navigation

A hot topic that is relatively common in the world of react native/react navigation that I have encountered while hanging out in #reactnative on freenode is that a lot of apps need different routes when signed in vs signed out.

In this short blog post, I will be outlining the process to do so.

`Navigators.js`
```javascript
  import { StackNavigator, TabNavigator } from 'react-navigation'
  import { SplashScreen } from '../screens/'

  exports.rootNavigator = StackNavigator({
    splashScreen: { screen: SplashScreen },
    loggedOut: { screen: loggedOutNavigation },
    loggedIn: { screen: loggedInNavigation },
  }, {
    headerMode: 'none',
  })

  exports.LoggedOutNavigation = TabNavigator({
    // Authentication routes (E.g Login/Registration)
  })

  exports.LoggedInNavigation = TabNavigator({
    // Routes requiring login
  })
```

`SplashScreen.js`

```javascript
import React, { Component } from 'react';
import { View } from 'react-native'
import * as firebase from 'firebase';
import { NavigationActions } from 'react-navigation';
import { LoggedInNavigation, LoggedOutNavigation } from '../Navigation';

export class SplashScreen extends Component {

  constructor(props) {
    super(props);
  }

  reset(route) {
    resetAction = NavigationActions.reset({
      index: 0,
      actions: [
        NavigationActions.navigate({ routeName: route})
      ]
    })
    this.props.navigation.dispatch(resetAction)
  }

  componentWillMount() {
    var user = firebase.auth().currentUser;
    if (user) {
      this.reset("LoggedIn");
    }
    firebase.auth().onAuthStateChanged((user) => {
      if (user) {
        this.reset("LoggedIn");
      } else {
        this.reset("LoggedOut");
      }
    });


  }

  render() {
    return (<View></View>)
  }
}


module.exports = SplashScreen ;
```

This example uses firebase to handle authentication, but you can use whatever method you would like. Just check the result from componentWillMount and then use the reset function to reset the stack navigator to prevent a user accidentally going back to the login page.
