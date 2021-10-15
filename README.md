# Combining Drawer, Tab and Stack navigators in React Navigation 6

Today we will be making use of the Drawer, Tab and Stack navigators. We will cover two cases:

- a simpler scenario where we utilize the Tab Navigator in a single Drawer route 
- a more complicated flow where we want the Tab bar to be visible and accessible inside *all* our Drawer routes. 

In this second example, we will try to overcome a design restriction of React Navigation - the different Navigators, if used together, can only be nested inside one another, and therefore can't be intertwined.

### Introduction

Adding navigation to a React Native application is greatly helped by using React Navigation library. It provides different types of navigators, with plenty of customization power. In some simple cases we can get by with using just one navigator, but often times we are presented with a challenge to combine multiple types in an app. 

The example chosen is to build an app for a Hotel chain. Some of the features include booking a room at one of the hotels, browsing the different locations and using reward points. Here is a preview of what we will be building:

<img src="https://github.com/anyamiletic/rn_navigation/blob/main/assets/navigationFinished.gif" alt="nav finished" width="25%" height="25%" />

We can see right away the use of Drawer and Tab navigators. We will also implement each of the routes as a Stack Navigator, since we now that, for instance, the Book flow will contain multiple screens.

### Getting started

*(if this is your first React Native project, please read the official [getting started guide](https://reactnative.dev/docs/getting-startedqa) before continuing)*

Let's initialize a new project. In your terminal, navigate to en ampty directory and run the following command:

`$ npx react-native init NavigationDemo --version 0.64.2`

The react version installed at the time of writing was 17.0.2, while the react-native version was 0.64.2.

Next, let's install react navigation and its dependencies:

```
$ npm install @react-navigation/native react-native-screens react-native-safe-area-context react-native-gesture-handler react-native-reanimated @react-navigation/stack @react-navigation/drawer @react-navigation/bottom-tabs
```

If you're developing for IOS, you also need to install the pods:

`$ cd ios; npx pod install; cd ..`

Replace the contents of your `App.js` file with the following code:

```javascript
import React from 'react'
import { SafeAreaView, View, StatusBar, StyleSheet, Text } from 'react-native'

const App = () => {
  return (
    <SafeAreaView style={styles.safeArea}>
      <StatusBar barStyle="dark-content" />
      <View>
        <Text>Hello navigation!</Text>
      </View>
    </SafeAreaView>
  )
}

const styles = StyleSheet.create({
  safeArea: {
    flex: 1,
    overflow: 'hidden',
  },
})

export default App

```

### Stack and Drawer navigators

Now we can go about adding the different navigators to our app. Remember, for this first example we want the DrawerNavigator to be the main (always visible) navigator in our app, with the BottomTabNavigator visible if the Home route is focused in the Drawer. Let's begin by adding the following file structure in our project (all the files remain empty for now):

![folder structure](https://github.com/anyamiletic/rn_navigation/blob/main/assets/file_structure.png?raw=true)

You can download the `hotel_logo` from the github repo provided at the end of this tutorial, or use your own. Next, we will create our Drawer Navigator that contains three routes (our Stack Navigators). For now, the stacks will contain a single screen defined directly in the stack file. In a real app, the stack can contain many screens, but it's important to have at least one. Following are the contents of the stack files:

###### HomeStackNavigator.js:

```javascript
import React from 'react'
import { View, Text } from 'react-native'
import { createStackNavigator } from '@react-navigation/stack'

const Stack = createStackNavigator()

const Home = () => (
  <View style={{ flex: 1, justifyContent: 'center', alignItems: 'center' }}>
    <Text>Home screen!</Text>
  </View>
)

const HomeStackNavigator = () => {
  return (
    <Stack.Navigator screenOptions={{
      headerShown: false,
    }}>
      <Stack.Screen name="Home" component={Home} />
    </Stack.Navigator>
  )
}

export default HomeStackNavigator
```

###### MyRewardsStackNavigator.js:

```javascript
import React from 'react'
import { View, Text } from 'react-native'
import { createStackNavigator } from '@react-navigation/stack'

const Stack = createStackNavigator()

const MyRewards = () => (
  <View style={{ flex: 1, justifyContent: 'center', alignItems: 'center' }}>
    <Text>MyRewards screen!</Text>
  </View>
)

const MyRewardsStackNavigator = () => {
  return (
    <Stack.Navigator screenOptions={{
      headerShown: false,
    }}>
      <Stack.Screen name="MyRewards" component={MyRewards} />
    </Stack.Navigator>
  )
}

export default MyRewardsStackNavigator
```

###### LocationsStackNavigator.js:

```javascript
import React from 'react'
import { View, Text } from 'react-native'
import { createStackNavigator } from '@react-navigation/stack'

const Stack = createStackNavigator()

const Locations = () => (
  <View style={{ flex: 1, justifyContent: 'center', alignItems: 'center' }}>
    <Text>Locations screen!</Text>
  </View>
)

const LocationsStackNavigator = () => {
  return (
    <Stack.Navigator screenOptions={{
      headerShown: false,
    }}>
      <Stack.Screen name="Locations" component={Locations} />
    </Stack.Navigator>
  )
}

export default LocationsStackNavigator
```

We will explain the screenOptions in a moment. Now that we have defined our drawer stack navigators, we can create the DrawerNavigator:

###### DrawerNavigator.js:

```javascript
import * as React from 'react'
import { createDrawerNavigator } from '@react-navigation/drawer'

import HomeStackNavigator from './stack-navigators/HomeStackNavigator'
import MyRewardsStackNavigator from './stack-navigators/MyRewardsStackNavigator'
import LocationsStackNavigator from './stack-navigators/LocationsStackNavigator'

const Drawer = createDrawerNavigator()

const DrawerNavigator = () => {
  return (
    <Drawer.Navigator>
      <Drawer.Screen name="HomeStack" component={HomeStackNavigator} />
      <Drawer.Screen name="MyRewardsStack" component={MyRewardsStackNavigator} />
      <Drawer.Screen name="LocationsStack" component={LocationsStackNavigator} />
    </Drawer.Navigator>
  )
}

export default DrawerNavigator
```

And add it to our NavigationContainer in `App.js`

```javascript
...

import DrawerNavigator from './src/navigation/DrawerNavigator'

const App = () => {
  return (
    <SafeAreaView style={styles.safeArea}>
      <StatusBar barStyle="dark-content" />
      <NavigationContainer>
          <DrawerNavigator />
      </NavigationContainer>
    </SafeAreaView>
  )
}
...

```

We can see the result now. We have React Navigation's default header, an icon to open the drawer, and our stacks in the drawer menu. We can navigate freely between those stacks. 

<img src="https://github.com/anyamiletic/rn_navigation/blob/main/assets/drawerNavInitial.gif" alt="folder structure" width="25%" height="25%" />

Now let's circle back to the `screenOptions` we defined in the stack navigators. Try setting `headerShown: true` in `HomeStackNavigator` and observe what happens:

<img src="https://github.com/anyamiletic/rn_navigation/blob/main/assets/drawerNavigatorWithoutScreenOptions.png" alt="double header" width="25%" height="25%" />

The Home component's header is rendered below the Drawer Navigator's. This is because the parent navigator's UI is rendered on top of child navigator. Since we obviously want only one header, specifying `headerShown: false` for each of the stack navigator's `screenOptions` hides the default stack header. Note that the title displayed in the drawer header is `HomeStack`, not `Home`. If we were to navigate to another screen in HomeStack, the title would not change. Could we have kept the Stack header and hidden the Drawer header? Yes! But for now, we want the default Drawer header as it provides us with an easy way to open the drawer - by pressing the menu icon in the header.

### Tab navigator

We have added Drawer navigation to our app, and defined stack navigators with screens to add to our drawer menu. Now we need to add tab navigation to our Home Route. Firstly, lets define Book and Contact stack navigators in the same way as before:

###### BookStackNavigator.js:

```javascript
import React from 'react'
import { View, Text } from 'react-native'
import { createStackNavigator } from '@react-navigation/stack'

const Stack = createStackNavigator()

const Book = () => (
  <View style={{ flex: 1, justifyContent: 'center', alignItems: 'center' }}>
    <Text>Book screen!</Text>
  </View>
)

const BookStackNavigator = () => {
  return (
    <Stack.Navigator screenOptions={{
      headerShown: false,
    }}>
      <Stack.Screen name="Book" component={Book} />
    </Stack.Navigator>
  )
}

export default BookStackNavigator
```

###### ContactStackNavigator.js:

```javascript
import React from 'react'
import { View, Text } from 'react-native'
import { createStackNavigator } from '@react-navigation/stack'

const Stack = createStackNavigator()

const Contact = () => (
  <View style={{ flex: 1, justifyContent: 'center', alignItems: 'center' }}>
    <Text>Contact screen!</Text>
  </View>
)

const ContactStackNavigator = () => {
  return (
    <Stack.Navigator screenOptions={{
      headerShown: false,
    }}>
      <Stack.Screen name="Contact" component={Contact} />
    </Stack.Navigator>
  )
}

export default ContactStackNavigator
```

Now let's create our Tab Navigator. 

###### BottomTabNavigator

```javascript
import * as React from 'react'
import { createBottomTabNavigator } from '@react-navigation/bottom-tabs'

import HomeStackNavigator from './stack-navigators/HomeStackNavigator'
import BookStackNavigator from './stack-navigators/BookStackNavigator'
import ContactStackNavigator from './stack-navigators/ContactStackNavigator'

const Tab = createBottomTabNavigator()

const BottomTabNavigator = () => {
  return (
    <Tab.Navigator screenOptions={{
      headerShown: false,
    }}>
      <Tab.Screen name="HomeStack" component={HomeStackNavigator} />
      <Tab.Screen name="BookStack" component={BookStackNavigator} />
      <Tab.Screen name="ContactStack" component={ContactStackNavigator} />
    </Tab.Navigator>
  )
}

export default BottomTabNavigator
```

Notice how the first tab screen we added is the HomeStack, which we have already added in DrawerNavigator. In fact, you can think of BottomTabNavigator as a container of stacks, with the inital stack being HomeStack. Since in HomeStack we have a Home screen, the inital screen being rendered in the Tab navigator is the Home screen. And because we want to show this when the user is on the Home route in the drawer navigaton, we will simply replace the HomeStackNavigator component in DrawerNavigator with BottomTabNavigator:

###### DrawerNavigator.js:

```javascript
...

const DrawerNavigator = () => {
  return (
    <Drawer.Navigator>
      <Drawer.Screen name="HomeTabs" component={BottomTabNavigator} />
      <Drawer.Screen name="MyRewardsStack" component={MyRewardsStackNavigator} />
      <Drawer.Screen name="LocationsStack" component={LocationsStackNavigator} />
    </Drawer.Navigator>
  )
}

...
```

Let's look at what we get:

<img src="https://github.com/anyamiletic/rn_navigation/blob/main/assets/bottomTabNavInitial.gif" alt="folder structure" width="25%" height="25%" />

When we are in the first route in DrawerNavigator, we can see the bottom tabs and navigate between them. If we move to another route in the Drawer, the tabs are no longer visible (since the tab navigator is just one of the drawer screens). We have again used `headerShown: false` to avoid rendering a double header. 


### Header and Tab design

We have implemented all our stacks, now we want to implement a few common requirements. Firstly, let's add icons to our tabs. For this project we will use the `react-native-vector-icons` package to access FontAwesome icons. The full installation guide can be found at https://www.npmjs.com/package/react-native-vector-icons. Once the installation process is complete, we can edit our `BottomTabNavigator.js` as follows:

```javascript
import * as React from 'react'
import { createBottomTabNavigator } from '@react-navigation/bottom-tabs'
import { Text, StyleSheet } from 'react-native'
import Icon from 'react-native-vector-icons/FontAwesome'

import HomeStackNavigator from './stack-navigators/HomeStackNavigator'
import BookStackNavigator from './stack-navigators/BookStackNavigator'
import ContactStackNavigator from './stack-navigators/ContactStackNavigator'

const Tab = createBottomTabNavigator()

const BottomTabNavigator = () => {
  return (
    <Tab.Navigator screenOptions={{ headerShown: false }}>
      <Tab.Screen name="HomeStack" component={HomeStackNavigator} options={{
        tabBarIcon: ({ focused }) => (
          <Icon name="home" size={30} color={focused ? '#551E18' : '#000'} />
        ),
        tabBarLabel: () => <Text style={styles.tabBarLabel}>Home</Text>
      }}
      />
      <Tab.Screen name="BookStack" component={BookStackNavigator} options={{
        tabBarIcon: ({ focused }) => (
          <Icon name="bed" size={30} color={focused ? '#551E18' : '#000'} />
        ),
        tabBarLabel: () => <Text style={styles.tabBarLabel}>Book Room</Text>
      }}
      />
      <Tab.Screen name="ContactStack" component={ContactStackNavigator} options={{
        tabBarIcon: ({ focused }) => (
          <Icon name="phone" size={30} color={focused ? '#551E18' : '#000'} />
        ),
        tabBarLabel: () => <Text style={styles.tabBarLabel}>Contact Us</Text>
      }}
      />
    </Tab.Navigator>
  )
}

const styles = StyleSheet.create({
  tabBarLabel: {
    color: '#292929',
    fontSize: 12,
  },
})

export default BottomTabNavigator

```

<img src="https://github.com/anyamiletic/rn_navigation/blob/main/assets/bottomNavFinished.png" alt="bottom tab navigator" width="25%" height="25%" />

For each stack we have specified an icon and a tab label. `tabBarIcon` recieves the `focused` prop, which we can use to highlight the current route (`tabBarLabel` can also recieve this prop). There are many possibilities with `options` and `screenOptions` properties, some of which are explored at https://reactnavigation.org/docs/screen-options/.
Let's use `screenOptions` in Drawer Navigator to change the header and route names in the drawer menu:

###### DrawerNavigator.js:

```javascript
import * as React from 'react'
import { View, StyleSheet, Image, Text, TouchableOpacity } from 'react-native'
import { createDrawerNavigator, DrawerContentScrollView, DrawerItem } from '@react-navigation/drawer'
import Icon from 'react-native-vector-icons/FontAwesome'

import MyRewardsStackNavigator from './stack-navigators/MyRewardsStackNavigator'
import LocationsStackNavigator from './stack-navigators/LocationsStackNavigator'
import BottomTabNavigator from './BottomTabNavigator'

const Drawer = createDrawerNavigator()

const CustomDrawerContent = (props) => {
  return (
    <DrawerContentScrollView {...props}>
      {
        Object.entries(props.descriptors).map(([key, descriptor], index) => {
          const focused = index === props.state.index
          return (
            <DrawerItem
              key={key}
              label={() => (
                <Text style={focused ? styles.drawerLabelFocused : styles.drawerLabel}>
                  {descriptor.options.title}
                </Text>
              )}
              onPress={() => descriptor.navigation.navigate(descriptor.route.name)}
              style={[styles.drawerItem, focused ? styles.drawerItemFocused : null]}
            />
          )
        })
      }
    </DrawerContentScrollView>
  )
}

const DrawerNavigator = () => {
  return (
    <Drawer.Navigator
      screenOptions={({ navigation }) => ({
        headerStyle: {
          backgroundColor: '#551E18',
          height: 50,
        },
        headerLeft: () => (
          <TouchableOpacity onPress={() => navigation.toggleDrawer()} style={styles.headerLeft}>
            <Icon name="bars" size={20} color="#fff" />
          </TouchableOpacity>
        ),
      })}
      drawerContent={(props) => <CustomDrawerContent {...props} />}
    >
      <Drawer.Screen name="HomeTabs" component={BottomTabNavigator} options={{
        title: 'Home',
        headerTitle: () => <Image source={require('../assets/hotel_logo.jpg')} />,
        headerRight: () => (
          <View style={styles.headerRight}>
            <Icon name="bell" size={20} color="#fff" />
          </View>
        ),
      }}/>
      <Drawer.Screen name="MyRewardsStack" component={MyRewardsStackNavigator} options={{
        title: 'My Rewards',
        headerTitle: () => <Text style={styles.headerTitle}>My Rewards</Text>,
      }}/>
      <Drawer.Screen name="LocationsStack" component={LocationsStackNavigator} options={{
        title: 'Locations',
        headerTitle: () => <Text style={styles.headerTitle}>Our Locations</Text>,
      }}/>
    </Drawer.Navigator>
  )
}

const styles = StyleSheet.create({
  headerLeft: {
    marginLeft: 15,
  },
  headerTitle: {
    color: 'white',
    fontSize: 18,
    fontWeight: '500',
  },
  headerRight: {
    marginRight: 15,
  },
  // drawer content
  drawerLabel: {
    fontSize: 14,
  },
  drawerLabelFocused: {
    fontSize: 14,
    color: '#551E18',
    fontWeight: '500',
  },
  drawerItem: {
    height: 50,
    justifyContent: 'center'
  },
  drawerItemFocused: {
    backgroundColor: '#ba9490',
  },
})

export default DrawerNavigator
```

<img src="https://github.com/anyamiletic/rn_navigation/blob/main/assets/navigationFinished.gif" alt="nav finished" width="25%" height="25%" />

Let's breakdown all of the changes. First of all, looking at the Drawer Screens, we can change the header of each drawer item separately. You might not want to display a title when the user is in the Tab navigator, but maybe show the company's logo instead. The `headerTitle` prop accepts a string as well as a function - giving us a lot of posibilites for customization. Furthemore, the title shown in the header can be different than the one shown in the drawer menu.

Next, we want to change the look of the header to fit better with our client's brand. We can do this by passing a function to DrawerNavigator's `screenOptions` and specifying header style and components. `ScreenOptions` also receives the `route` prop. We are passing a function to `headerLeft` that renders our menu icon, and toggles the drawer - this toggle function is available in the `navigation` object.

Finally, let's customize the Drawer menu. We only want to change the route item styles for now, and unfortunately there isn't a simple DrawerNavigation prop that enables us to do this. Instead, we must pass a custom drawerContent function that enables us to render a completely custom component for each item. We are using the passed props to iterate through these items, but we could also render more routes using `<DrawerItem>`, or add an image component at the top of `<DrawerContentScrollView>`, or any number of other options.

### Conclusion

In this tutorial, we have combined Drawer, Tab and Stack navigators to create a simple navigation flow. We have then, through `screenOptions`, supplied customization to get the look and feel we needed. In the next section, we will explore the problem of having both the Drawer and Tab navigations always visible and connected.

The complete project can be found on [github](https://github.com/anyamiletic/rn_navigation_project)

## Part 2

*This is part 2 of a 2-part react navigation tutorial **Combining Drawer, Tab and Stack navigators in React Navigation 6**. If you haven't read that yet, please read it first [here](https://github.com/anyamiletic/rn_navigation/blob/main/README.md)*

<img src="https://github.com/anyamiletic/rn_navigation/blob/main/assets/navigationFinal.gif" alt="nav final" width="25%" height="25%" />

Implementing navigation such that the Drawer and Tab navigators are visible in every screen isn't a simple task. Simply put, the react navigation library is not designed in a way that this functionality is ready out-of-the-box.

When working with nested navigators, the navigation UI of the child navigator is present *only* in the screens which it contains. Because of this, in order to have BottomTabNavigator in every screen, it must contain every screen.

Since TabNavigator will contain all of our stacks, the only screen present in DrawerNavigator now becomes TabNavigator. But we still want to render 'Home', 'My Rewards' and 'Location' routes in the drawer. We'll refactor `CustomDrawerContent` to render a custom list of items. To get the focused route, we'll use a reference to the navigation object defined in `App.js`. Let's begin!

### Route items

For each screen we'll have a configuraton object that we'll store in an array. Remeber that TabNavigator is a screen as well, contained within DrawerNavigator as a `Drawer.Screen`:

###### navigation/RouteItems.js
```javascript
import * as React from 'react'
import Icon from 'react-native-vector-icons/FontAwesome'

export const screens = {
  HomeTab: 'HomeTab',
  HomeStack: 'HomeStack',
  Home: 'Home',
  BookStack: 'BookStack',
  Book: 'Book',
  ContactStack: 'ContactStack',
  Contact: 'Contact',
  MyRewardsStack: 'MyRewardsStack',
  MyRewards: 'MyRewards',
  LocationsStack: 'LocationsStack',
  Locations: 'Locations',
}

export const routes = [
  {
    name: screens.HomeTab,
    focusedRoute: screens.HomeTab,
    title: 'Home',
    showInTab: false,
    showInDrawer: false,
    icon: (focused) =>
      <Icon name="home" size={30} color={focused ? '#551E18' : '#000'} />,
  },
  {
    name: screens.HomeStack,
    focusedRoute: screens.HomeStack,
    title: 'Home',
    showInTab: true,
    showInDrawer: true,
    icon: (focused) =>
      <Icon name="home" size={30} color={focused ? '#551E18' : '#000'} />,
  },
  {
    name: screens.Home,
    focusedRoute: screens.HomeStack,
    title: 'Home',
    showInTab: true,
    showInDrawer: false,
    icon: (focused) =>
      <Icon name="home" size={30} color={focused ? '#551E18' : '#000'} />,
  },

  {
    name: screens.BookStack,
    focusedRoute: screens.BookStack,
    title: 'Book Room',
    showInTab: true,
    showInDrawer: false,
    icon: (focused) =>
      <Icon name="bed" size={30} color={focused ? '#551E18' : '#000'} />,
  },

  {
    name: screens.Book,
    focusedRoute: screens.BookStack,
    title: 'Book Room',
    showInTab: true,
    showInDrawer: false,
    icon: (focused) =>
      <Icon name="bed" size={30} color={focused ? '#551E18' : '#000'} />,
  },

  {
    name: screens.ContactStack,
    focusedRoute: screens.ContactStack,
    title: 'Contact Us',
    showInTab: true,
    showInDrawer: false,
    icon: (focused) =>
      <Icon name="phone" size={30} color={focused ? '#551E18' : '#000'} />,
  },
  {
    name: screens.Contact,
    focusedRoute: screens.ContactStack,
    title: 'Contact Us',
    showInTab: false,
    showInDrawer: false,
    icon: (focused) =>
      <Icon name="phone" size={30} color={focused ? '#551E18' : '#000'} />,
  },

  {
    name: screens.MyRewardsStack,
    focusedRoute: screens.MyRewardsStack,
    title: 'My Rewards',
    showInTab: false,
    showInDrawer: true,
    icon: (focused) =>
      <Icon name="star" size={30} color={focused ? '#551E18' : '#000'} />,
  },
  {
    name: screens.MyRewards,
    focusedRoute: screens.MyRewardsStack,
    title: 'My Rewards',
    showInTab: false,
    showInDrawer: false,
    icon: (focused) =>
      <Icon name="star" size={30} color={focused ? '#551E18' : '#000'} />,
  },

  {
    name: screens.LocationsStack,
    focusedRoute: screens.LocationsStack,
    title: 'Locations',
    showInTab: false,
    showInDrawer: true,
    icon: (focused) =>
      <Icon name="map-marker" size={30} color={focused ? '#551E18' : '#000'} />,
  },
  {
    name: screens.Locations,
    focusedRoute: screens.LocationsStack,
    title: 'Locations',
    showInTab: false,
    showInDrawer: false,
    icon: (focused) =>
      <Icon name="map-marker" size={30} color={focused ? '#551E18' : '#000'} />,
  },
]

```

Regardless of the navigation style I always use `screens` and `routes` to have a centralized place to make changes. Let's jump to `BottomTabNavigator`:

###### BottomTabNavigator.js
```javascript
...

const Tab = createBottomTabNavigator()

const tabOptions = ({ route }) => {
  const item = routes.find(routeItem => routeItem.name === route.name) // get the route config object

  if (!item.showInTab) { // hide this tab
    return {
      tabBarButton: () => <View style={{ width: 0 }} />,
      headerShown: false,
      tabBarStyle: styles.tabContainer,
      title: item.title,
    }
  }

  return {
    tabBarIcon: ({ focused }) => item.icon(focused),
    tabBarLabel: () => (
      <Text style={styles.tabBarLabel}>{item.title || ''}</Text>
    ),
    headerShown: false,
    tabBarStyle: styles.tabContainer,
    title: item.title,
  }
}

const BottomTabNavigator = () => {
  return (
    <Tab.Navigator screenOptions={tabOptions}>
      <Tab.Screen name={screens.HomeStack} component={HomeStackNavigator} />
      <Tab.Screen name={screens.BookStack} component={BookStackNavigator} />
      <Tab.Screen name={screens.ContactStack} component={ContactStackNavigator} />

      {/* new stacks */}
      <Tab.Screen name={screens.MyRewardsStack} component={MyRewardsStackNavigator} /> 
      <Tab.Screen name={screens.LocationsStack} component={LocationsStackNavigator} />
    </Tab.Navigator>
  )
}

const styles = StyleSheet.create({
  tabBarLabel: {
    color: '#292929',
    fontSize: 12,
  },
  tabContainer: {
    height: 60,
  }
})

...
```

We've added 'MyRewardsStack' and 'LocationsStack' as tab screens. Only routes with `showInTab: true` will render a tab. If you comment-out the `if (!item.showInTab)` section, you'll get all of the tabs rendered:

<img src="https://github.com/anyamiletic/rn_navigation/blob/main/assets/bottomTabAllRoutes.png" alt="nav finished" width="25%" height="25%" />

With the full code, the page looks the same as before:

<img src="https://github.com/anyamiletic/rn_navigation/blob/main/assets/bottomTabHiddenRoutes.png" alt="nav finished" width="25%" height="25%" />

Note also that now the screen names aren't hardcoded, we're using the `screens` object to supply the names.

Let's jump to DrawerNavigator:

###### DrawerNavigator.js
```javascript
...

const Drawer = createDrawerNavigator()

const CustomDrawerContent = (props) => {
  return (
    <DrawerContentScrollView {...props}>
      {
        routes.filter(route => route.showInDrawer).map((route) => {
          const focused = index === props.state.index
          return (
            <DrawerItem
              key={route.name}
              label={() => (
                <Text style={focused ? styles.drawerLabelFocused : styles.drawerLabel}>
                  {route.title}
                </Text>
              )}
              onPress={() => props.navigation.navigate(route.name)}
              style={[styles.drawerItem, focused ? styles.drawerItemFocused : null]}
            />
          )
        })
      }
    </DrawerContentScrollView>
  )
}

const DrawerNavigator = () => {
  return (
    <Drawer.Navigator
      screenOptions={({ navigation }) => ({
        headerStyle: {
          backgroundColor: '#551E18',
          height: 50,
        },
        headerLeft: () => (
          <TouchableOpacity onPress={() => navigation.toggleDrawer()} style={styles.headerLeft}>
            <Icon name="bars" size={20} color="#fff" />
          </TouchableOpacity>
        ),
      })}
      drawerContent={(props) => <CustomDrawerContent {...props} />}
    >
      <Drawer.Screen name={screens.HomeTab} component={BottomTabNavigator} options={{
        title: 'Home',
        headerTitle: () => <Image source={require('../assets/hotel_logo.jpg')} />,
        headerRight: () => (
          <View style={styles.headerRight}>
            <Icon name="bell" size={20} color="#fff" />
          </View>
        ),
      }}/>
    </Drawer.Navigator>
  )
}

...
```

Now we've removed 'MyRewardsStack' and 'LocationsStack', and are rendering selected routes (in the previous code we rendered all of the `Drawer.Screen`s, which in this case would be only `HomeTabs` screen). We have an issue right now - the `focused` check will not work since `props.state.index` will always return `0`, we're always in `BottomTabNavigator` screen:

<img src="https://github.com/anyamiletic/rn_navigation/blob/main/assets/navigationFocusedRoute.gif" alt="nav finished" width="25%" height="25%" />

As a workaround, we need to find out the current route, and we'll do that using a reference to the navigation object.

###### App.js
```javascript
import React, { createRef } from 'react'
import { SafeAreaView, StatusBar, StyleSheet } from 'react-native'
import { NavigationContainer } from '@react-navigation/native'

import DrawerNavigator from './src/navigation/DrawerNavigator'

// store reference to navigation object
const navigationRef = createRef()
const nav = () => navigationRef.current

const App = () => {
  return (
    <SafeAreaView style={styles.safeArea}>
      <StatusBar barStyle="dark-content" />
      <NavigationContainer ref={navigationRef}>
          <DrawerNavigator nav={nav} />
      </NavigationContainer>
    </SafeAreaView>
  )
}
```

We're sending this reference as a prop to `DrawerNavigator` where we can use it to check the focused route:

###### DrawerNavigator.js
```javascript
const DrawerNavigator = ({ nav }) => {
  return (
    <Drawer.Navigator
      ...
      drawerContent={(props) => <CustomDrawerContent {...props} nav={nav} />}
      ...
```

```javascript
const CustomDrawerContent = (props) => {
  const currentRouteName = props.nav()?.getCurrentRoute().name // get focused route name
  return (
    <DrawerContentScrollView {...props}>
      {
        routes.filter(route => route.showInDrawer).map((route) => {
          const focusedRouteItem = routes.find(r => r.name === currentRouteName) // get route item config object
          const focused = focusedRoute ?
            route.name === focusedRouteItem?.focusedRoute :
            route.name === screens.HomeStack
          return (
            <DrawerItem
              key={route.name}
              label={() => (
                <Text style={focused ? styles.drawerLabelFocused : styles.drawerLabel}>
                  {route.title}
                </Text>
              )}
              onPress={() => props.navigation.navigate(route.name)}
              style={[styles.drawerItem, focused ? styles.drawerItemFocused : null]}
            />
          )
        })
      }
    </DrawerContentScrollView>
  )
}
```

In the first render the `getCurrentRoute()` will return `undefined`, in that case we know that the focused route is `HomeStack`. We then, for each Drawer route, check if its name matches the `focusedRouteItem.focusedRoute`. For example, if we are on the `MyRewards` screen (or any other screen we would define in that stack), its `focusedRoute` would be `MyRewardsStack`. We get the desired result:

<img src="https://github.com/anyamiletic/rn_navigation/blob/main/assets/navigationFinal.gif" alt="nav final" width="25%" height="25%" />

### Conclusion

Using react navigation, we have implemented Drawer, Tab and Stack navigation such that the drawer and bottom tab UI is visible in every app route. We've added custom styles and components for Tabs, Headers and Drawer Items. We have also centralized our configuration for each route.

#### What's next?

For further customization, you can start by exploring the `screenOptions` and `options` props. Perhaps add a `HeaderRight` component to Drawer's `screenOptions`, or add a `tabBarBadge` to Tab Navigators screen options. 

When adding a new screen to any stack (or adding a new stack) make sure to add that screen's config to `routes` to make sure our navigators access all of the required information. Happy coding!


The complete project can be found on [github](https://github.com/anyamiletic/rn_navigation_project)
