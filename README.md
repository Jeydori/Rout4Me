
### **Rout4Me: Real-Time GPS Tracking Application for PUV Passengers (Flutter)**

![image](https://github.com/user-attachments/assets/206a492e-ff5e-4902-bde7-fe5093f79602)

![image](https://github.com/user-attachments/assets/7af4cab5-c8d5-4fd7-8c9f-7ac99bf4cbef)

### Process Flow Chart for Commuter’s Route4Me Mobile Application

![image](https://github.com/user-attachments/assets/d19c8568-46c3-4ca5-8022-2982add9578c)

### Process Flow Chart for Driver’s Route4Me Mobile Application

![image](https://github.com/user-attachments/assets/5fa25323-6431-4033-9828-73f1fe813a62)

## PUV Passengers' app source code:
https://github.com/Jeydori/flutterApp

## PUV drivers' app source code:
https://github.com/Jeydori/route4me_driver

#### **Prerequisites**
- **Flutter SDK** installed on your machine.
- A **code editor** like VS Code or Android Studio.
- **Android Studio or Xcode** for running your Flutter apps on Android or iOS.
- Basic knowledge of Flutter and Dart.
- **Firebase** account for backend services (authentication, real-time database, etc.)

### **1. Setting Up the Passenger App**
This app is for the passengers who will track the PUV's real-time location.

#### Step 1: **Clone the Passenger App Repository**
- Open your terminal and clone the repository for the passenger app:
  ```bash
  git clone https://github.com/Jeydori/flutterApp.git
  ```

#### Step 2: **Install Dependencies**
- Navigate to the project folder:
  ```bash
  cd flutterApp
  ```
- Install the required dependencies by running:
  ```bash
  flutter pub get
  ```

#### Step 3: **Configure Firebase for the App**
- Go to Firebase Console and create a new project (or use an existing one).
- Add your Android or iOS app to Firebase.
- Download the `google-services.json` (for Android) or `GoogleService-Info.plist` (for iOS) and add it to your project.
- Update `pubspec.yaml` to include Firebase packages, such as `firebase_core`, `firebase_database`, and `firebase_auth`.

#### Step 4: **Implement GPS Tracking**
- Use the `geolocator` package for real-time GPS tracking. Add the following in your `pubspec.yaml`:
  ```yaml
  dependencies:
    geolocator: ^8.0.0
  ```

- In your Dart code, use the `Geolocator` class to get the user's current location. Example:
  ```dart
  import 'package:geolocator/geolocator.dart';

  Future<void> _getCurrentLocation() async {
    Position position = await Geolocator.getCurrentPosition(
        desiredAccuracy: LocationAccuracy.high);
    print(position.latitude);
    print(position.longitude);
  }
  ```

#### Step 5: **Display Map**
- Use the `google_maps_flutter` package to display the map and the location of the PUV. Add the following to `pubspec.yaml`:
  ```yaml
  dependencies:
    google_maps_flutter: ^2.1.1
  ```

- Initialize the map and add a marker for the PUV:
  ```dart
  import 'package:google_maps_flutter/google_maps_flutter.dart';

  GoogleMapController? mapController;

  void _onMapCreated(GoogleMapController controller) {
    mapController = controller;
  }

  Set<Marker> _createMarkers() {
    return {
      Marker(
        markerId: MarkerId("puv"),
        position: LatLng(12.9716, 77.5946), // Example coordinates
        infoWindow: InfoWindow(title: 'PUV Location'),
      ),
    };
  }
  ```

#### Step 6: **Integrate with Firebase for Real-Time Updates**
- Use Firebase to store and update the real-time location of the PUV.
- In the passenger app, listen to location updates from the Firebase Realtime Database:
  ```dart
  FirebaseDatabase.instance
      .reference()
      .child('puv_location')
      .onValue
      .listen((event) {
    var locationData = event.snapshot.value;
    // Update the PUV location on the map
  });
  ```

---

### **2. Setting Up the Driver App**
This app is for the PUV drivers to send their real-time GPS location to the passenger app.

#### Step 1: **Clone the Driver App Repository**
- Clone the driver’s app repository:
  ```bash
  git clone https://github.com/Jeydori/route4me_driver.git
  ```

#### Step 2: **Install Dependencies**
- Navigate to the project folder:
  ```bash
  cd route4me_driver
  ```
- Run:
  ```bash
  flutter pub get
  ```

#### Step 3: **Configure Firebase for the App**
- Follow the same Firebase setup as for the passenger app to link the driver app with Firebase.

#### Step 4: **Driver GPS Tracking**
- Use the `geolocator` package to get the driver’s location. Use `Geolocator.getPositionStream` to continuously update the driver's location:
  ```dart
  StreamSubscription<Position>? positionStream;
  void _startTracking() {
    positionStream = Geolocator.getPositionStream(
      locationSettings: LocationSettings(
        accuracy: LocationAccuracy.high,
        distanceFilter: 10,
      ),
    ).listen((Position position) {
      FirebaseDatabase.instance
          .reference()
          .child('puv_location')
          .set({
        'latitude': position.latitude,
        'longitude': position.longitude,
      });
    });
  }
  ```

#### Step 5: **Sending Location to Firebase**
- Send the driver’s real-time GPS location to Firebase Realtime Database in the same manner as shown in the passenger app, and continuously update the position.

---

### **3. Connect the Passenger and Driver Apps**
- **Passenger app** listens for updates from Firebase and updates the map.
- **Driver app** sends real-time location to Firebase.

### **4. Handle Authentication**
- Use Firebase Authentication to allow drivers to log in to their account, and ensure passengers don’t need to log in (since they're tracking PUVs anonymously).

---

### **5. Testing the Apps**
- Run the passenger app and the driver app on different devices (or simulators).
- Verify that the passenger app correctly displays the driver’s location in real-time.
- Ensure that both apps correctly update locations in Firebase and reflect the changes on the map.

---
I hope this tutorial gave you a comprehensive guide to building and understanding the app. :)
