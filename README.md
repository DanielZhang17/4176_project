# Roarz

This project aims to develop a native Android fitness application. The project is implemented in Kotlin. The application is designed to generate a route for jogging on the map with distance as user input.  

## Group members

| Name        | Banner ID | Email              |
| ----------- | --------- | ------------------ |
| Anran Zhang | B00123456 | Anran.Zhang@dal.ca |
| Yahu Wang   | B00123456 | Yh288977@dal.ca    |
| Yuze Qu     | B00123456 | Yz438723@dal.ca    |

## Description

The design purpose of the application is to help the user to plan daily joggings.

The application can generate a path for jogging and provide tracking information that can guide them through the the way using active tactic feedback. The user is encouraged to interact with the others or show off what the user has achieved. The user will consider the application as a motivation to do exercise regularly. 

### Users

As a fitness application,our project targets to the users who wish to spend time on exercise by jogging. 

The software is intended to be used in the outdoor environment where  local traffic is not "packed" so that the use can run on the street freely. 

### Features

a. The application can randomly generating human-reachable waypoints on the map. 
b. The application can identify whether the user reaches the previously mentioned waypoints. 
c. The application can navigate the users to the waypoints on the map.

d. The application can measure the user’s travel distance. 
e. The device will vibrate once the user is not following a right direction in the path. 
f. The user can create an account and update their profiles. 
g. The application can took a picture through camera, which can be shared on the profile. 

h. The application provide a warm up activity, "Shaking coke".

Since a local/cloud database is not constructed, the following features are partially implemented:

a. The application is able to generate a QR code.

b. The application can provide a share link to other social media.


## Libraries

**google-gson:** Gson is a Java library that can be used to convert Java Objects into their JSON representation. It can also be used to convert a JSON string to an equivalent Java object. Source [here](https://github.com/google/gson)

**Glide:** an Loader library provide efficient and precise solution for managing media and images.

**OkHttp: **a library for exchanging the data, doing HTTP efficiently.

**Google Play Services:** used for automated location tracking, and location awareness. 

**Google Map Directions:** used for route generation and navigation when origin and destination are specified.


## Requirements

**Software:** Android 5.0

**Hardware:** The device must include the following features. The user should also granted the permission to the following features.

> Network Connection: used to check the location and route generations.
>
> GPS service: used to the location tracking as well as the navigation.
>
> Camera & Gallery: used to take photo and save the photo as local sharable media content. 
>
> Magnetometer & Accelerometer: used to help the user identify the right direction for the route.

**Permissions:** user must grant permission to the following module.

> Location
>
> Internal Storage
>
> Camera & Gallery


## Installation Notes

Install the Application using normal APK installing procedures. 


## Final Project Status

### Minimum Functionality
- Waypoints generation (Completed)
- Identifying whether the user reached the waypoints (Completed)
- Navigation to waypoints (Completed)

### Expected Functionality
- Tracking user with GPS, measuring distance (Completed)
- Detect whether the user is facing the wrong direction (Partially Completed)
- Using Camera to take pictures (Completed)
- User can update their profile, and the application save the profile in the local internal storage. (Completed)

### Bonus Functionality
- Sharing pictures on Social networks (Partially completed)
- Indoors activity (Partially completed)
- QR code (Partially completed)
- Viewing friends and leaderboard (Not Implemented)


## Code Examples

**Problem 1: ** The route generated by Direction API is not accurate enough. 

The following method improve precision of the route generated. 
```
    private fun decodePoly(encoded: String): List<LatLng> {
        val poly = ArrayList<LatLng>()
        var index = 0
        val len = encoded.length
        var lat = 0
        var lng = 0

        while (index < len) {
            var b: Int
            var shift = 0
            var result = 0
            do {
                b = encoded[index++].toInt() - 63
                result = result or (b and 0x1f shl shift)
                shift += 5
            } while (b >= 0x20)
            val dlat = if (result and 1 != 0) (result shr 1).inv() else result shr 1
            lat += dlat

            shift = 0
            result = 0
            do {
                b = encoded[index++].toInt() - 63
                result = result or (b and 0x1f shl shift)
                shift += 5
            } while (b >= 0x20)
            val dlng = if (result and 1 != 0) (result shr 1).inv() else result shr 1
            lng += dlng

            val p = LatLng(lat.toDouble() / 1E5, lng.toDouble() / 1E5)
            poly.add(p)
        }
        return poly
    }
    //reference: 
```

**Problem 2:** check if the user is on the path

We only partially solve this problem. We first planned to implement is method which constantly check if the user's location is on the path. Later in the actual implementation, we solve this problem using an alternative solution: providing checkpoints on the route and constantly remind the user the remaining distance to the checkpoint. 

```
    private fun OnthePath(UserLocation:LatLng,Checkpoint:LatLng,StepLength:Float):Boolean{
        var location1:Location  = android.location.Location("")
        var location2:Location  = android.location.Location("")

        location1.latitude = UserLocation.latitude
        location1.longitude = UserLocation.longitude
        location2.latitude = Checkpoint.latitude
        location2.longitude = Checkpoint.longitude

        var distance:Float = location1.distanceTo(location2)
        if(distance>StepLength){
            return false
        }
        return true
    }
    
        private fun Arrive(UserLocation:LatLng,Checkpoint:LatLng):Float{
        var location1:Location=android.location.Location("")
        var location2:Location=android.location.Location("")

        location1.latitude = UserLocation.latitude
        location1.longitude = UserLocation.longitude
        location2.latitude = Checkpoint.latitude
        location2.longitude = Checkpoint.longitude

        var dis:Float = location1.distanceTo(location2)

        return dis
    }
   
```

## Functional Decomposition

To breakdown the functionality of our application, it composes of three major modules. The first module is the navigation. The navigation can be further divided into two sub-modules, which are “general” navigation and the “quick response” navigation.

The general navigation makes uses of Google Map API to load the map into the application and establish a connection to the GPS services. This sub-module is responsible for generating random human-reachable waypoints, provide general guidance and routing for the user, as well as keep tracking user’s travel distance. Another sub-module is implemented with magnetometer and  accelerometer to help the user to check whether the he is facing the right direction. Also, we develop an algorithm to cooperate with the Google Map API during the navigation. 

The second major module are the social features. The team made uses of Camera API to add a camera functionality to the application. Furthermore, we implemented a QR code generator so that the users can add friends through scanning QR codes.

The third major module is the user interface, which is responsible for interconnecting all the functionality as well as helping the user to configure the system. 

The following figure provides a brief view of our current functional decompositions. 

![functional](Figures\functional.PNG)

## High-level Organization

There are three fragments placed in the UI resource file. They are DashBoard Fragment, HomeFragment, and Notification Fragment. These fragments can be called by the navigation bar in the Main Activity. Our major functionalities are all implemented in these three fragments. 

DashBoard Fragment contains our Bonus functionalities, such as QRcode generation and In door activity using Shake Listener. 

Home Fragment contains all the components that is needed for route generation, waypoints generation, location tracking, as well as updating the Map updating. 

Notification Fragment contains the necessary components for updating user's profile and data internal storage. Also, the user can also access the camera in this fragment. 

The following figure represents our High-level organization. 

![High level org](Figures\High level org.PNG)

## Clickstreams 

**Use Case: Get Location**

**Triggers:** 

​      The user enters the application and grant the permission to location tracking.

**Basic Flow:**

\1.    The system provides a map which indicates the current position of the user.

\3.    The user can update the current location in case collaboration is needed.

\4.    GPS services will be applied to keep track of the user’s current position.

 

**Use Case: Take Picture**

**Triggers:**

​      The user invokes a camera request.

**Basic flow:**

\1.    The application will first asked user’s permission for the camera.

\2.    The application will pass the request to the camera API.

\3.    The application will collect the returned result from the camera.

**Extension:**

​      The user can update their profile avatar with the photo. 



 **Use Case: Indoor Activity/Warm Up Activity**

**Trigger:**

​      The user invokes request of "Shake Coke" Warm Up Activity.

**Basic flow:**

\1. The system provides the user with dialog to show a “Shaking coke" animation .

\2. Once the user starts to shake the device, the application will show the percentage of completion on the screen.

\3\. Once the "Warming Up" is completed, the application will notify the user with a brief vibration as feedback.



 **Use Case: QR Code Generating**

**Trigger:**

​      The user invokes request of generating a QR code.

**Basic flow:**

​	   The system provides the user with dialog to show the code that is generated.



**Use Case: Profile Updating**

**Trigger:**

​      The user invokes request of updating the profile.

**Basic flow:**

\1.    The system provides the user with dialog for the user to fill in the required information.

\2.    The system then stores the information for future uses in the internal storage.



**Use Case: Generate Jogging Route**

**Trigger:** 

​		The user invokes the request to jogging.

**Basic flows:**

\1.    The system will first ask for user’s permission for the location listeners.

\2.    The system will show up a dialog asking the user’s preferred distance.

\3.    The system then will generate a route within the designated distance.

**Extension:**

\1.    The system will provide navigation once a user selects a specific waypoint on the map.

\2.    The user can ask the system to regenerate a route to a waypoint for collaboration.

\3.    During the navigation, the system will sound an alert if the user is facing the wrong direction.



## Layout

<img src="Figures\layout1.PNG" alt="layout1" style="zoom: 50%;" />

<img src="Figures\layout2.PNG" alt="layout2" style="zoom: 50%;" />

<img src="Figures\layout3.PNG" alt="layout3" style="zoom:50%;" />

<img src="Figures\signIn.PNG" alt="signIn" style="zoom:50%;" />

<img src="D:\Frank\MyFile\dal 2019\4176\project\final\Figures\signUp.PNG" alt="signUp" style="zoom:50%;" />

## Implementation

**Home Fragment:**

This fragment will show a map with user's current location. The marker on the map will rotate as the user's facing direction is changing. 

The user can move the camera to the user's current position by tapping the icon at the top-right corner. The user can request to generate a route for running by tapping the bottom-right icon . '

It then will invoke a dialogue for the user to enter the desired running distance. The user can ask the route to be a round-trip by ticking "Return to Origin" option. Once, the user hit the confirm button in the dialogue, a route with designated distance will show on the map. 

Each marker on the route indicating a checkpoint on the path.

Every time the user reaches a checkpoint, the "red cross" marker will replaced by a "green check mark" marker. 

A Toast message will show up regularly to the indicate the distance from the current position to the next check point.

<img src="Figures\homFrag.jpg" alt="homFrag" style="zoom: 25%;" />

The following figure shows the dialogue invoked by the fragment.

<img src="Figures\homeDia.jpg" alt="homeDia" style="zoom: 25%;" />

**Notification Fragment:**

This fragment will show the user's current profile information. 

The user can change their profile avatar using a photo they take from camera. In order to do that, the user need to press the avatar located at the center of the interface. Then the camera of the device will wake up and ready to take a picture. Once the user took a picture, the result will directly returned as the avatar of the profile. 

The users can also edit and update their profile by pressing the "pen" icon located in the center of the interface. A dialogue will be invoked and the user can enter the new information in the dialogue. By pressing the confirm button, the profile will be updated. 



<img src="Figures\navigationFrag.jpg" alt="navigationFrag" style="zoom:25%;" />

The following figure shows the dialogue invoked the user.

<img src="Figures\notifyDia.jpg" alt="notifyDia" style="zoom: 25%;" />

**Dashboard Fragment:**

This fragment contains two functionalities: the QR code generation as well as the "Warming Up" activity. The interface provide access to them through the two buttons located at the center of the interface. 

<img src="D:\Frank\MyFile\dal 2019\4176\project\final\Figures\dashfrag.jpg" alt="dashfrag" style="zoom:25%;" />

The following figures shows the dialogue invoked the user.

<img src="Figures\cokeShake.PNG" alt="cokeShake" style="zoom: 80%;" />



<img src="Figures\qrcode.jpg" alt="qrcode" style="zoom:25%;" />

## Future Work

- Local or Cloud data base for user data, which back up the "Adding Friend" Functionality.
- A friends leader board for the user to show off their achievements.
- The application should now able to share media content on the FaceBook or Twitter
- The user can register for an account or sign in.


## Sources

code references:

Sending simple data to other apps. Android Developers. Google. Retrieved from:

https://developer.android.com/training/sharing/send

Draw route between two locations in GoogleMap using Android Studio - Kotlin. CodeAndroid. Youtube. Retrieve from:

https://www.youtube.com/watch?v=o_XpQroYgsA

images:

https://icons8.com/web-app/23265/user

https://icons8.com/web-app/8209/hunt

https://icons8.com/web-app/9806/running