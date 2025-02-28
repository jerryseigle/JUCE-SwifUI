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
                   initializeJUCE()
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

6. Create the Main Application File (YourAppNameApp.swift)

Create a new Swift file called YourAppNameApp.swift. The content should look like this:

import SwiftUI

@main
struct YourAppNameApp: App {
    var body: some Scene {
        WindowGroup {
            ContentView()
        }
    }
}

7. Fix the Dual Entry Point Issue

In the JUCE Main.cpp file, there will be an issue with dual entry points since both SwiftUI and JUCE define their own entry points. To fix this:
	•	Remove this line from the JUCE Main.cpp file:

START_JUCE_APPLICATION (YourAppNameJUCApplication)



The updated Main.cpp should now look like this:

/*
  ==============================================================================

    This file contains the basic startup code for a JUCE application.

  ==============================================================================
*/

#include <JuceHeader.h>
#include "MainComponent.h"

class YourAppNameJUCApplication  : public juce::JUCEApplication
{
public:
    YourAppNameJUCApplication() {}

    const juce::String getApplicationName() override { return ProjectInfo::projectName; }
    const juce::String getApplicationVersion() override { return ProjectInfo::versionString; }
    bool moreThanOneInstanceAllowed() override { return true; }

    void initialise (const juce::String& commandLine) override
    {
        mainWindow.reset (new MainWindow (getApplicationName()));
    }

    void shutdown() override
    {
        mainWindow = nullptr; // (deletes our window)
    }

    void systemRequestedQuit() override
    {
        quit();
    }

    void anotherInstanceStarted (const juce::String& commandLine) override {}

    class MainWindow : public juce::DocumentWindow
    {
    public:
        MainWindow (juce::String name)
            : DocumentWindow (name, juce::Desktop::getInstance().getDefaultLookAndFeel().findColour(juce::ResizableWindow::backgroundColourId), DocumentWindow::allButtons)
        {
            setUsingNativeTitleBar(true);
            setContentOwned(new MainComponent(), true);

            #if JUCE_IOS || JUCE_ANDROID
            setFullScreen(true);
            #else
            setResizable(true, true);
            centreWithSize(getWidth(), getHeight());
            #endif

            setVisible(true);
        }

        void closeButtonPressed() override
        {
            JUCEApplication::getInstance()->systemRequestedQuit();
        }

    private:
        JUCE_DECLARE_NON_COPYABLE_WITH_LEAK_DETECTOR (MainWindow)
    };

private:
    std::unique_ptr<MainWindow> mainWindow;
};

// Remove this line to fix dual entry point issue
// START_JUCE_APPLICATION (YourAppNameJUCApplication)

8. Clean and Build Again

After making all these changes, clean and rebuild the project to ensure everything compiles properly.

Conclusion

By following these steps, you will have successfully integrated JUCE with SwiftUI for both iOS and macOS. This setup allows SwiftUI to manage the app’s lifecycle while JUCE handles backend functionality, including audio, MIDI, and other components. SwiftUI is now in charge of managing the app’s UI and lifecycle, while JUCE is initialized and running in the background.
