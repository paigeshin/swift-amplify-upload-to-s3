# swift-amplify-upload-to-s3

# Swift Amplify S3 Upload

### Dependency

[https://github.com/aws-amplify/amplify-ios](https://github.com/aws-amplify/amplify-ios)

- Amplify
    - AWSCognitoAuthPlugin
    - Amplify
    - AWSS3StroagePlugin

### Command lines

```shell
amplify init 
amplify add storage 

# Select Auth and guest users

# Permission to users
# CTRL + A 
# ✔ create/update
# ✔ read
# ✔ delete

# No lambda trigger 

amplify push -y 
```

### Configure

```swift
import SwiftUI
import Amplify
import AWSCognitoAuthPlugin
import AWSS3StoragePlugin

@main
struct AmplifyS3SampleApp: App {
    
    init() {
        self.configureAmplify()
    }
    
    var body: some Scene {
        WindowGroup {
            ContentView()
        }
    }
    
    private func configureAmplify() {
        do {
            try Amplify.add(plugin: AWSCognitoAuthPlugin())
            try Amplify.add(plugin: AWSS3StoragePlugin())
            try Amplify.configure()
            print("Successfully configured")
        } catch {
            print(error.localizedDescription)
        }
    }
    
    
}
```

### Upload & Download

```swift
//
//  ContentView.swift
//  AmplifyS3Sample
//
//  Created by paige shin on 2022/09/17.
//

import SwiftUI
import Amplify

struct ContentView: View {
    
    let imageKey: String = "house-icon"
    @State var image: UIImage?
    
    var body: some View {
        VStack(spacing: 40) {
            if let image = self.image {
                Image(uiImage: image)
                    .resizable()
                    .aspectRatio(1, contentMode: .fit)
                    .frame(width: 100, height: 100)
            }
            Button("Upload", action: uploadImage)
            Button("Download", action: downloadImage)
        }
    }
    
    func uploadImage() {
        let houseImage = UIImage(systemName: "house")!
        let houseImageData = houseImage.jpegData(compressionQuality: 1)!
        Amplify.Storage.uploadData(key: imageKey,
                                   data: houseImageData) { result in
            switch result {
            case .success(let uploadedData):
                print(uploadedData)
            case .failure(let error):
                print(error)
            }
        }
        
    }
    
    func downloadImage() {
        Amplify.Storage.downloadData(key: imageKey) { result in
            switch result {
            case .success(let data):
                DispatchQueue.main.async {
                    self.image = UIImage(data: data)
                }
            case .failure(let error):
                print(error)
            }
        }
    }
}

struct ContentView_Previews: PreviewProvider {
    static var previews: some View {
        ContentView()
    }
}
```

x
```
