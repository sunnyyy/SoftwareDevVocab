# Android Development
(source: https://developer.android.com/courses/fundamentals-training/overview-v2)

## Android stack
1. system & user apps
  - system app = your phone's SMS app
  - user apps = e.g. Facebook
2. Java API framework
  - contains the entire feature-set of the Android OS
    - e.g. view class hierarchy to create UI screens
    - e.g. notification manager
    - e.g. activity manager for lifecycles
3. C/C++ libraries + Android runtime
  - each app has its own process & its own version of the Android runtime
  - C/C++ libraries allow access to core native Android system components
4. Hardware Abstraction Layer
  - interfaces that expose device hardware capabilities as libraries
5. Linux Kernel
  - threading
  - low-level memory management
  - security features
  - drivers

### An Android app is...
- 1+ interactive screens
- written using Java Programming Language and XML
- uses the Android Software Development Kit (SDK)
- uses Android libraries and Android Application Framework
- executed by Android Runtime Virtual machine (ART)
- made up of building blocks:
  - __resources__: layouts, images, strings, colors as XML and media files
  - __components__: activities, services, and helper classes as Java code
  - __manifest__: information about app for the runtime
  - __build configuration__: APK versions in Gradle config files

