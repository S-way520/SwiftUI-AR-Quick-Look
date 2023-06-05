# SwiftUI-AR-Quick-Look
This is a way to open a .usdz or .reality file, where you only need one line of code to open the AR model.
1. You need to rewrite this code through Xcode
2. To open this code in Swift playground, it needs to be published to TestFlight for normal use.
# The first part
![IMG_0029](https://github.com/S-way520/SwiftUI-AR-Quick-Look/assets/95877651/6961afa8-02c8-46f7-a9aa-7d580209421e)
# The second part
Code file 1
```swift
import SwiftUI
@main
struct MyApp: App {
    var body: some Scene {
        WindowGroup {
            ContentView()
        }
    }
}
```
Code file 2
```swift
import SwiftUI
struct ContentView: View {
    @State var ARshow = false
    var body: some View {
        ARShow(ARImage: "tutorial", ARText1: "1.1", ARText2: "tutorial", ARName: "tutorial", ARshow: ARshow)
        //你只需要一行代码即可打开AR模型
        //You only need one line of code to open the AR model
        //ARShow(
    }
}
```
Code file 3
```swift
import SwiftUI
import QuickLook
import ARKit
struct QLModel: UIViewControllerRepresentable {
    var name: String
    var allowScaling: Bool = true
    func makeCoordinator() -> QLModel.Coordinator {
        Coordinator(self)
    }
    func makeUIViewController(context: Context) -> QLPreviewController {
        let controller = QLPreviewController()
        controller.dataSource = context.coordinator
        return controller
    }
    func updateUIViewController(_ controller: QLPreviewController, context: Context) { }
    class Coordinator: NSObject, QLPreviewControllerDataSource {
        let parent: QLModel
        private lazy var fileURL: URL = Bundle.main.url(forResource: parent.name, withExtension: "reality")!//usdz
        init(_ parent: QLModel) {
            self.parent = parent
            super.init()
        }
        func numberOfPreviewItems(in controller: QLPreviewController) -> Int { return 1 }
        func previewController(_ controller: QLPreviewController, previewItemAt index: Int) -> QLPreviewItem {
            guard let fileURL = Bundle.main.url(forResource: parent.name, withExtension: "reality") else {
                fatalError("Unable to load \(parent.name).reality from main bundle")}//usdz
            let item = ARQuickLookPreviewItem(fileAt: fileURL)
            item.allowsContentScaling = parent.allowScaling
            return item
        }
    }
}
struct ARShow: View {
    @Environment(\.colorScheme) var colorScheme//暗黑模式
    var ARImage: String
    var ARText1: String
    var ARText2: String
    var ARName: String
    @State var ARshow = false
    func simpleSuccess() {UINotificationFeedbackGenerator().notificationOccurred(.success)}
    var body: some View {
        HStack{
            Button(action: {self.ARshow.toggle()}){
                Image(ARImage)
                    .resizable()
                    .aspectRatio(contentMode: .fit)
                    .frame(width: 80, height: 80)
                    .background(Color.gray.opacity(0.1))
                    .cornerRadius(10, antialiased: true)
                Text(ARText1)
                    .foregroundColor(.orange)
                Text(ARText2)
                    .multilineTextAlignment(.leading)
                    .foregroundColor(colorScheme == .light ? .black : .white)
            }
            .fullScreenCover(isPresented: $ARshow){
                ZStack{
                    QLModel(name: ARName).edgesIgnoringSafeArea(.all)
                    VStack{
                        Button(action: {self.ARshow.toggle(); simpleSuccess()}, label: {
                            HStack{
                                Image(systemName: "chevron.backward.circle")
                                    .font(.title3)
                                Spacer()
                                
                            }
                        })
                        .padding()
                        Spacer()
                    }
                }
            }
            Spacer()
        }
    }
}
```
