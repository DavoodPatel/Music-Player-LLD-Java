# 🎵 Music Player Application

A **Java-based Low-Level Design (LLD) implementation of a Music Player Application** demonstrating how multiple object-oriented design patterns can be combined to build a modular, extensible, and maintainable system.

The application supports song management, playlist creation, multiple playback strategies, audio output devices, play/pause functionality, previous/next navigation, and custom song queuing.

> **Note:** This project focuses on **LLD and design patterns**. The audio APIs are mocked and simulate playback through console output rather than actually playing audio files.

---

## 🚀 Features

* 🎵 Create and manage songs in a music library
* 📋 Create playlists and add songs
* ▶️ Play individual songs
* ⏸️ Pause and resume songs
* ⏭️ Play next track
* ⏮️ Play previous track
* 🔀 Random/shuffle playback
* 📑 Sequential playlist playback
* 📌 Custom song queue
* 🔊 Support for multiple audio output devices

  * Bluetooth Speaker
  * Wired Speaker
  * Headphones
* 🔌 Easy integration of new output devices
* 🧩 Modular architecture using multiple design patterns
* 🔒 Singleton-based centralized managers
* 🎯 Strategy-based playback behavior

---

## 🏗️ System Architecture

The application is divided into several logical components:

```text
                         ┌──────────────────────────┐
                         │ MusicPlayerApplication   │
                         │       (Facade API)       │
                         └────────────┬─────────────┘
                                      │
                                      ▼
                         ┌──────────────────────────┐
                         │   MusicPlayerFacade      │
                         └───────┬─────────┬────────┘
                                 │         │
                 ┌───────────────┘         └────────────────┐
                 ▼                                           ▼
        ┌─────────────────┐                         ┌─────────────────┐
        │ PlaylistManager │                         │  DeviceManager  │
        └────────┬────────┘                         └────────┬────────┘
                 │                                           │
                 ▼                                           ▼
        ┌─────────────────┐                         ┌─────────────────┐
        │    Playlist     │                         │  DeviceFactory  │
        └────────┬────────┘                         └────────┬────────┘
                 │                                           │
                 ▼                              ┌────────────┼────────────┐
             ┌───────┐                         ▼            ▼            ▼
             │ Song  │                  Bluetooth      Wired        Headphones
             └───────┘                    Adapter       Adapter       Adapter
                                              │            │             │
                                              ▼            ▼             ▼
                                            API          API           API


                    ┌────────────────────────────┐
                    │      StrategyManager       │
                    └─────────────┬──────────────┘
                                  │
             ┌────────────────────┼────────────────────┐
             ▼                    ▼                    ▼
      SequentialStrategy    RandomStrategy    CustomQueueStrategy
```

---

## 📁 Project Structure

```text
MusicPlayerApplication/
│
├── core/
│   └── AudioEngine.java
│
├── device/
│   ├── IAudioOutputDevice.java
│   ├── BluetoothSpeakerAdapter.java
│   ├── WiredSpeakerAdapter.java
│   └── HeadphonesAdapter.java
│
├── enums/
│   ├── DeviceType.java
│   └── PlayStrategyType.java
│
├── external/
│   ├── BluetoothSpeakerAPI.java
│   ├── WiredSpeakerAPI.java
│   └── HeadphonesAPI.java
│
├── factories/
│   └── DeviceFactory.java
│
├── managers/
│   ├── DeviceManager.java
│   ├── PlaylistManager.java
│   └── StrategyManager.java
│
├── models/
│   ├── Song.java
│   └── Playlist.java
│
├── strategies/
│   ├── PlayStrategy.java
│   ├── SequentialPlayStrategy.java
│   ├── RandomPlayStrategy.java
│   └── CustomQueueStrategy.java
│
├── MusicPlayerApplication.java
├── MusicPlayerFacade.java
└── Main.java
```

---

# 🧩 Design Patterns Used

## 1. Singleton Pattern

Singleton is used to ensure that only one instance of important managers exists throughout the application.

### Classes

* `MusicPlayerApplication`
* `MusicPlayerFacade`
* `DeviceManager`
* `PlaylistManager`
* `StrategyManager`

Example:

```java
public static synchronized DeviceManager getInstance() {
    if (instance == null) {
        instance = new DeviceManager();
    }
    return instance;
}
```

### Why?

It provides a centralized point of access to shared application state such as:

* Current audio device
* Playlist collection
* Playback strategies
* Main music player interface

---

# 2. Facade Pattern

The `MusicPlayerFacade` provides a simplified interface to the internal components of the music player.

```text
Client
  │
  ▼
MusicPlayerApplication
  │
  ▼
MusicPlayerFacade
  │
  ├── AudioEngine
  ├── DeviceManager
  ├── PlaylistManager
  └── StrategyManager
```

Instead of the client directly interacting with multiple managers, it can simply call:

```java
application.playSingleSong("Zinda");
application.loadPlaylist("Bollywood Vibes");
application.playAllTracksInPlaylist();
```

### Benefit

The Facade reduces coupling between the client and the internal implementation.

---

# 3. Strategy Pattern

The application supports different ways of selecting the next song.

The common interface is:

```java
public interface PlayStrategy {
    void setPlaylist(Playlist playlist);
    Song next();
    boolean hasNext();
    Song previous();
    boolean hasPrevious();
    default void addToNext(Song song) {}
}
```

Different algorithms implement this interface:

```text
                 PlayStrategy
                      │
          ┌───────────┼────────────┐
          ▼           ▼            ▼
     Sequential     Random      CustomQueue
```

### SequentialPlayStrategy

Plays songs in playlist order.

```text
Song 1 → Song 2 → Song 3 → Song 4
```

### RandomPlayStrategy

Selects songs randomly while maintaining playback history.

```text
Song 1 → Song 4 → Song 2 → Song 3
```

### CustomQueueStrategy

Allows songs to be manually added to the next-play queue.

```text
Current Song
     │
     ▼
Next Queue
 ┌───────┐
 │ Song A│
 │ Song B│
 │ Song C│
 └───────┘
```

### Benefit

New playback algorithms can be added without modifying the existing player architecture.

---

# 4. Factory Pattern

`DeviceFactory` is responsible for creating the correct audio output device.

```java
DeviceFactory.createDevice(DeviceType.BLUETOOTH);
```

The factory returns an implementation of:

```java
IAudioOutputDevice
```

Supported devices:

```text
DeviceType
   │
   ├── BLUETOOTH
   ├── WIRED
   └── HEADPHONES
```

### Benefit

Object creation is separated from the classes that use those objects.

---

# 5. Adapter Pattern

The application defines its own common interface:

```java
public interface IAudioOutputDevice {
    void playAudio(Song song);
}
```

However, different external devices expose different APIs.

For example:

```java
BluetoothSpeakerAPI
    └── playSoundViaBluetooth()

WiredSpeakerAPI
    └── playSoundViaCable()

HeadphonesAPI
    └── playSoundViaJack()
```

Adapters convert these APIs into the common interface:

```text
IAudioOutputDevice
       │
       ├── BluetoothSpeakerAdapter
       │        │
       │        └── BluetoothSpeakerAPI
       │
       ├── WiredSpeakerAdapter
       │        │
       │        └── WiredSpeakerAPI
       │
       └── HeadphonesAdapter
                │
                └── HeadphonesAPI
```

### Benefit

The core music player does not need to know the implementation details of each audio device.

---

# 🧠 Data Structures Used

The project also demonstrates practical usage of common data structures.

### ArrayList

Used for:

* Song library
* Playlist songs
* Remaining songs during random playback

### Queue

Used in:

```java
CustomQueueStrategy
```

to store songs that should be played next.

```text
Queue:

Song A → Song B → Song C
 ↑
poll()
```

### Stack

Used for playback history.

```text
Stack:

Song C
Song B
Song A
 ↑
pop()
```

This allows previous-song functionality to be implemented.

---

# 🎵 Core Components

## Song

Represents a song in the music library.

Attributes:

```text
title
artist
filePath
```

Example:

```java
new Song(
    "Kesariya",
    "Arijit Singh",
    "/music/kesariya.mp3"
);
```

---

## Playlist

Represents a collection of songs.

Responsibilities:

* Store playlist name
* Store songs
* Add songs
* Return songs
* Return playlist size

---

## AudioEngine

Responsible for the actual playback state.

It maintains:

```text
currentSong
songIsPaused
```

Responsibilities:

* Play song
* Pause song
* Resume paused song
* Track current song

---

## DeviceManager

Manages the currently connected audio device.

Supported devices:

```text
Bluetooth Speaker
Wired Speaker
Headphones
```

It uses `DeviceFactory` to create the appropriate adapter.

---

## PlaylistManager

Maintains all playlists using:

```java
Map<String, Playlist>
```

Responsibilities:

* Create playlist
* Find playlist
* Add song to playlist

---

## StrategyManager

Maintains the available playback strategies:

```text
SequentialPlayStrategy
RandomPlayStrategy
CustomQueueStrategy
```

It returns the required strategy based on:

```java
PlayStrategyType
```

---

# 🔄 Example Flow

A typical application flow looks like this:

### 1. Create songs

```java
application.createSongInLibrary(
    "Kesariya",
    "Arijit Singh",
    "/music/kesariya.mp3"
);
```

### 2. Create playlist

```java
application.createPlaylist("Bollywood Vibes");
```

### 3. Add songs

```java
application.addSongToPlaylist(
    "Bollywood Vibes",
    "Kesariya"
);
```

### 4. Connect audio device

```java
application.connectAudioDevice(
    DeviceType.BLUETOOTH
);
```

### 5. Select playback strategy

```java
application.selectPlayStrategy(
    PlayStrategyType.SEQUENTIAL
);
```

### 6. Load playlist

```java
application.loadPlaylist(
    "Bollywood Vibes"
);
```

### 7. Play playlist

```java
application.playAllTracksInPlaylist();
```

---

# 🔀 Playback Strategies

## Sequential

```text
Kesariya
   ↓
Chaiyya Chaiyya
   ↓
Tum Hi Ho
   ↓
Jai Ho
```

---

## Random

The songs are selected randomly:

```text
Tum Hi Ho
   ↓
Jai Ho
   ↓
Kesariya
   ↓
Chaiyya Chaiyya
```

The random strategy also maintains a history stack for previous-track navigation.

---

## Custom Queue

Songs can be explicitly inserted into the next-play queue:

```java
application.queueSongNext("Kesariya");
application.queueSongNext("Tum Hi Ho");
```

The queued songs can then be prioritized during playback.

---

# 🖥️ Example

The included `Main.java` demonstrates:

```text
Create Music Library
        ↓
Create Playlist
        ↓
Add Songs
        ↓
Connect Bluetooth Device
        ↓
Play Single Song
        ↓
Pause
        ↓
Resume
        ↓
Sequential Playback
        ↓
Random Playback
        ↓
Custom Queue Playback
        ↓
Previous Track
```

Example console output:

```text
Bluetooth device connected

Playing song: Zinda
[BluetoothSpeaker] Playing: Zinda by Siddharth Mahadevan

Pausing song: Zinda

Resuming song: Zinda
[BluetoothSpeaker] Playing: Zinda by Siddharth Mahadevan

-- Sequential Playback --

Playing song: Kesariya
[BluetoothSpeaker] Playing: Kesariya by Arijit Singh

Playing song: Chaiyya Chaiyya
[BluetoothSpeaker] Playing: Chaiyya Chaiyya by Sukhwinder Singh
```

---

# 🛠️ Technologies Used

* **Java**
* Object-Oriented Programming
* Low-Level Design
* SOLID principles
* Design Patterns
* Collections Framework
* ArrayList
* HashMap
* Queue
* Stack

---

# ▶️ How to Run

## Prerequisites

Install:

* Java JDK 8 or later
* Git

Check Java installation:

```bash
java -version
javac -version
```

## Clone the Repository

```bash
git clone <your-repository-url>
```

```bash
cd MusicPlayerApplication
```

## Compile

From the project source directory:

```bash
javac MusicPlayerApplication/**/*.java
```

Or compile using your IDE such as IntelliJ IDEA or VS Code.

## Run

```bash
java MusicPlayerApplication.Main
```

---

# 📐 Design Principles

The project demonstrates several important LLD principles:

### Encapsulation

Classes hide their internal state and expose controlled methods.

### Abstraction

`IAudioOutputDevice` hides the implementation details of different audio devices.

### Polymorphism

Different playback strategies implement the same `PlayStrategy` interface.

### Loose Coupling

The core application interacts with abstractions instead of concrete device APIs.

### Open/Closed Principle

New playback strategies and audio devices can be introduced with minimal changes to existing code.

---

# 🔮 Possible Future Improvements

The current project focuses on LLD and design patterns. A production-level implementation could be extended with:

* Actual MP3/audio playback
* User accounts
* Persistent database storage
* Song search
* Album and artist management
* Favorites/liked songs
* Repeat mode
* Shuffle mode
* Volume control
* Audio progress tracking
* Song duration
* Multiple playlists
* Recently played songs
* Music recommendations
* REST API
* Spring Boot backend
* Concurrent playback/event handling
* Unit and integration tests

---

# ⚠️ Current Limitations

This implementation is primarily an **LLD demonstration**.

The external audio APIs:

```text
BluetoothSpeakerAPI
WiredSpeakerAPI
HeadphonesAPI
```

simulate playback using console messages rather than interacting with real hardware.

The project also keeps application state in memory, so playlists and songs are lost when the application terminates.

---

# 🎯 Learning Objectives

This project is useful for understanding:

* Object-Oriented Design
* Low-Level Design
* SOLID principles
* Singleton Pattern
* Facade Pattern
* Strategy Pattern
* Factory Pattern
* Adapter Pattern
* Composition and interfaces
* State management
* Queue and Stack based navigation
* Designing extensible systems


## ⭐ If you find this project useful

Consider giving the repository a ⭐ and exploring the implementation of each design pattern.
