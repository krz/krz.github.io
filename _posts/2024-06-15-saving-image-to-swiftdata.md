---
layout: post
title: Saving an Image to Swift Data
categories: [swift]
---

In the rapidly evolving landscape of app development, managing and persisting data efficiently is crucial. Whether you are building a photo gallery app, a note-taking tool with image attachments, or any application that involves handling images, the way you store and retrieve those images can significantly impact performance and user experience.

Enter SwiftData, a powerful framework introduced by Apple to streamline data management in Swift applications. SwiftData not only simplifies the process of saving and retrieving various types of data but also integrates seamlessly with SwiftUI, allowing developers to create robust and scalable apps with minimal effort.

In this blog post, we will delve into a practical example of using SwiftData to save images selected by the user through a `PhotosPicker`. We will walk through the entire process, from setting up your data model to displaying a list of saved images with descriptions. By the end of this guide, you'll have a solid understanding of how to leverage SwiftData to enhance your app's functionality and provide a smooth user experience. Let's get started!

### Requirements for Swift Data

Using SwiftCode requires the following steps:

1. Define the class
The class has to use the `@Model` macro and the `@Attribute(.externalStorage)` to save the image. Don't forget to import `SwiftData`.
In our app, we want to save the image and a description of the image. The class looks like this:

   
```swift
import Foundation
import SwiftData
import SwiftUI

@Model
class Item {
    
    var descript: String
    
    @Attribute(.externalStorage)
    var image: Data?
    
    init(descript: String = "", image: Data? = nil) {
        self.descript = descript
        self.image = image
    }
}
```

2. Add a modelContainer
You need to add a `modelContainer` to the `WindowGroup` of your app:

```swift
import SwiftData
import SwiftUI

@main
struct PhotoRememberApp: App {
    var body: some Scene {
        WindowGroup {
            ContentView()
        }
        .modelContainer(for: Item.self)
    }
}
```

3. Add the modelContext Environment to your ContentView
```swift
@Environment(\.modelContext) private var modelContext
```

To select a Photo, we define a `PhotosPicker`:
```swift
                PhotosPicker(
                    selection: $selectedPhoto,
                    matching: .images,
                    photoLibrary: .shared()
                ) {
                    Text("Select a Photo")
                }
                .onChange(of: selectedPhoto) {
                    Task {
                        await loadImageData(from: selectedPhoto)
                    }
                }
```

Then we build a save Button that calls a function so save this image to SwiftData:

```swift
    private func saveImage(_ descript: String, _ data: Data) {
        let newItem = Item(descript: descript, image: data)
        modelContext.insert(newItem)
        
        do {
            try modelContext.save()
        } catch {
            print("Failed to save context: \(error)")
        }
        // Reset selected photo and imageData after saving
        selectedPhoto = nil
        imageData = nil
        imageDescription = ""
    }
```

Let's put everything together:

```swift
import SwiftUI
import PhotosUI
import SwiftData


struct ContentView: View {
    @Environment(\.modelContext) private var modelContext
    @Query private var items: [Item]
    @State private var selectedPhoto: PhotosPickerItem?
    @State private var imageData: Data?
    
    @State private var imageDescription: String = ""

    var body: some View {
        NavigationView {
            VStack {
                List(items) { item in
                    HStack {
                        if let imageData = item.image, let uiImage = UIImage(data: imageData) {
                            Image(uiImage: uiImage)
                                .resizable()
                                .scaledToFill()
                                .frame(width: 50, height: 50)
                                .clipShape(RoundedRectangle(cornerRadius: 8))
                        } else {
                            Rectangle()
                                .fill(Color.gray)
                                .frame(width: 50, height: 50)
                                .clipShape(RoundedRectangle(cornerRadius: 8))
                        }
                        Text(item.descript)
                    }
                }
                .navigationTitle("Photo Selector")

                if let imageData = imageData, let uiImage = UIImage(data: imageData) {
                    Image(uiImage: uiImage)
                        .resizable()
                        .scaledToFit()
                        .frame(height: 200)
                } else {
                    Text("No Image Selected")
                        .frame(height: 200)
                }

                PhotosPicker(
                    selection: $selectedPhoto,
                    matching: .images,
                    photoLibrary: .shared()
                ) {
                    Text("Select a Photo")
                }
                .onChange(of: selectedPhoto) {
                    Task {
                        await loadImageData(from: selectedPhoto)
                    }
                }

                if imageData != nil {
                    TextField("Enter image description", text: $imageDescription)
                                            .textFieldStyle(RoundedBorderTextFieldStyle())
                                            .padding()
                    
                    Button("Save Image") {
                        if let imageData = imageData {
                            saveImage(imageDescription, imageData)
                        }
                    }
                    .padding()
                }
            }
            .padding()
        }
    }

    private func loadImageData(from item: PhotosPickerItem?) async {
        if let data = try? await item?.loadTransferable(type: Data.self) {
            self.imageData = data
        }
    }

    private func saveImage(_ descript: String, _ data: Data) {
        let newItem = Item(descript: descript, image: data)
        modelContext.insert(newItem)
        
        do {
            try modelContext.save()
        } catch {
            print("Failed to save context: \(error)")
        }
        // Reset selected photo and imageData after saving
        selectedPhoto = nil
        imageData = nil
        imageDescription = ""
    }
}

struct ContentView_Previews: PreviewProvider {
    static var previews: some View {
        ContentView()
    }
}
```





