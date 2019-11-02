# Android App Fundamentals

- apps can be written in Kotlin, Java, or C++
- Android SDK tools compiles code / data / resources files into an APK ("Android Package", `.apk`)
- each Android app lives in its own security sandbox
- security features:
  - Android OS = multiuser Linux system where each app is a user
  - by default, system assigns each app a unique Linux UID
    - ID is known only to the system, not the app
    - permissions for all files in that app are assigned only to that UID
  - each process has its own VM --> app code runs in isolation
  - every app runs its own Linux process
    - Android OS starts process when any app components need to be executed
    - process shuts down when not needed / when system needs that memory
- Android OS uses __principle of least privilege__
- how to share data with other apps?
  - it's possible for 2 apps to share the same Linux UID
    - they could access each others' files
    - could run in same Linux process & same VM
    - apps must be signed with same certificate
  - app can request permission to access device data (e.g. location, camera, Bluetooth)

# App components (4 types)

## 1. Activities
- entry point for interacting with user
- represents single screen with UI
  - ex: email app has 1 activity with list of emails, 1 activity for composing, 1 for reading
- each activity is independent
- a differnt app can start an activity
  - ex: camera app starts activity to compose email, to share pic
- facilitates interactions between system and app
  - keeps track of what's on screen
  - ensures system keeps running the process hosting the activity
  - knows that the previous used processes contain things the user may return to (stopped activities) & thus prioritizes keeping them
  - provides a way for apps to implement user flows between each other (ex: share)

## 2. Service
- general-purose entry point for keeping an app running on the background
- runs in the background for long-running operations or to perform work for remote processes
- NO UI
- ex: play music in the background even when user is in a different app
- ex: fetch data over network without blocking user interaction
- another component (e.g. Activity) can start service and let it run, or bind to it to interact with it
- 2 semantics for services to tell the system how to manage an app
  - __started services__
    - tell the system to keep them running until their work is completed
    - foreground service
      - ex: play music, with notification to tell user about it
    - background service
      - ex: sync data
    - system may allow it to be killed (& resurrected later) if it needs the RAM
  - __bound services__
    - some other app (or system) says it wants to make use of the service
    - acts as if service provides API to another process
    - system knows there's a dependency, so if A is bound to B, system knows to keep B running for A
      - if user cares about A, then system will treat B as important
    - ex: live wallpapers
    - ex: notification listeners
    - ex: screen savers
    - ex: input methods
    - ex: accessibility services
- if your app targets Android 5.0+, use `JobScheduler`

## 3. Broadcast Receivers
- enables system to deliver events to the app outside of a regular user flow
- allows app to respond to a system-wide broadcast announcemenets
- system can deliver broadcasts even to apps that aren't running
- ex: an app can scehdule alarm to post a notif to tell user about upcoming event
  - deliver that alarm to app's BroadcastReceiver
  - app doesn't have to stay running until alarm goes off
- many broadcasts originate from system
  - ex: screen turned off
  - ex: battery low
  - ex: picture captured
- apps can also imitate broadcasts
  - ex: data is now downloaded to device & available to other apps
- don't display a UI but can create a status bar notification
- most common: broadcast receiver is just a gateway to other components & is intended to do a very mininmal amount of work
  - ex: it might schedule a JobSergice to perform some work based on the event with JobScheduler
- implemented as a subclass of Broadcast Receiver & delivered as intent object

## 4. Content Providers
- manages a shared set of app data you can store into ...
  - the file system
  - a SQLite DB
  - on the web
  - any persistent sotrage location your app can access
- via content provider, other apps can query / modify the data if the content provider allows it
- ex: Android system provides a content provider for managing a user's contacts info
  - --> any app with permissions can query `ContactsContract.Data` to read / write info about a contact
- is kind of like (but not entirely) an abstraction on a DB
- to the system, a content provider is like an entry point into an app for publishing named data items (??), identified by a URI scheme
- pros
  - assigning a URI doesn't require the app to stay running
    - URIs can persist after their owning apps have stopped
    - system only needs to make sure an owning app is still running when it has to retrieve the app's data from the corresponding URI
  - these URI provide a fine-grained security model
    - ex: an app can place the URI for an image it has on the clipboard, but leave its content provider locked up
      - --> when a second app tries to get access to that URI, the system can allow that data via a temp URI permission for just that data
- must implement a standard set of APIs

---

- a unique part of AndroidOS = any app can start another app's component
  - ex: if you want the user to snap a pic with the device camera
    - you can just use an existing app that does that instead of developing an activity to capture a photo yourself
    - no need to incorporate or link code from camera app
    - instead just start the activity in camera app to capture photo
    - when done, photo is returned to your app to use
- when system starts a component, it starts the process for that app (if it's not already running) & instantiates the classes needed
- because system runs each app in a separate process with file permissions that restrict access to other apps, your app can't directly activate a component from another app
  - however, android OS can
  - so deliver a message to the system that specifies your intent to start a component
  - system activates it for you
