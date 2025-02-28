# JUCE-SwifUI
How to add SwiftUI to JUCE and let SwifUI manage lifecycles 

# Integrating JUCE with SwiftUI for iOS and macOS

This guide demonstrates how to integrate **JUCE** with **SwiftUI** for **iOS** and **macOS** applications. By following these steps, you will be able to use **SwiftUI** to manage the app's lifecycle while leveraging **JUCE** for backend functionality such as audio, MIDI, and other JUCE features.

The integration allows **SwiftUI** to control the application's UI and lifecycle, while **JUCE** handles the necessary backend processes, like managing the audio device and other JUCE components.

### Steps:

## 1. **Create JUCE Project in JUCE App**
   - Start by creating a **JUCE** project in the **JUCE app**.
   - Once the **JUCE** project is created, you’ll want to go to **Build Settings** and configure the C++ compiler.

## 2. **Set the C++ Compiler Dialect**
   - In **Xcode**, navigate to **Build Settings** and set the **C++ Language Dialect** to **GNU++20**.

## 3. **Clean and Build the Project**
   - After making the adjustments in **Build Settings**, clean the project (`Product > Clean Build Folder`) and rebuild it to ensure everything is set up correctly.

## 4. **Create a SwiftUI File (`ContentView.swift`)**
   - Next, create a new **Swift file** called `ContentView.swift` by choosing **File > New > New File** from the menu.
   - Xcode will ask if you want to create a **bridging header**. Choose **no**, as **Xcode 15** and newer can bridge **Swift** and **C++** automatically.

## 5. **Set Up `ContentView.swift`**
   The content of `ContentView.swift` should look like this:

   ```swift
   import SwiftUI

   // Call JUCE initialization from SwiftUI when ContentView appears
   struct ContentView: View {
       var body: some View {
           Text("Hello, JUCE + SwiftUI!")
               .padding()
               .onAppear {
                   // Call JUCE initialization function here
                   initializeJUCE() // Defined in main.mm via bridging header
               }
       }

       private func initializeJUCE() {
           // Call any JUCE-related logic
           print("JUCE is initialized!")
           // Example: Initialize JUCE components, e.g., AudioDeviceManager
       }
   }

   // Preview setup for SwiftUI
   struct ContentView_Previews: PreviewProvider {
       static var previews: some View {
           ContentView()
               .previewLayout(.sizeThatFits)  // Optional: Makes the preview fit the content
               .padding()
       }
   }
