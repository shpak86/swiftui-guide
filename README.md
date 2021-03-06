# SwiftUI usage guide <!-- omit in toc -->

This document contains examples of SwiftUI components and views usage. This is a set of usage examples that I found useful to study SwuftyUI basics.

# Table of contents  <!-- omit in toc -->

- [Components](#components)
  - [TextField](#textfield)
  - [SecureField](#securefield)
  - [TextView](#textview)
  - [Image](#image)
  - [Button](#button)
  - [Toggle](#toggle)
  - [Picker](#picker)
  - [DatePicker](#datepicker)
  - [Slider](#slider)
  - [Stepper](#stepper)
- [Layout](#layout)
  - [Stacks](#stacks)
  - [Spacer](#spacer)
  - [Divider](#divider)
  - [Group](#group)
  - [List](#list)
  - [ForEach](#foreach)
  - [ScrollView](#scrollview)
  - [Form](#form)
  - [TabView](#tabview)
  - [NavigationView and NavigationLink](#navigationview-and-navigationlink)
  - [Alert](#alert)
  - [Modal](#modal)
  - [ActionSheet](#actionsheet)
- [Tips annd tricks](#tips-annd-tricks)
  - [Resize window when keybord is shown](#resize-window-when-keybord-is-shown)
  - [Hide keyboard](#hide-keyboard)

# Components

## TextField

Simple text field
```swift
struct ContentView: View {
    var body: some View {
        Text("Hello world")
            .italic()
            .font(.title)
            .lineLimit(1)
            .padding()
            .frame(maxWidth: .infinity, alignment: .center)
            .multilineTextAlignment(.center)
    }
}
```

Simple TextField with rounded border
```swift 
TextField("Text placeholder", text: $text)
                .textFieldStyle(RoundedBorderTextFieldStyle())
```

TextField with custom border
```swift
TextField("Text placeholder", text: $text)
    .background(
        RoundedRectangle(cornerRadius: 30)
            .strokeBorder(Color.blue, lineWidth: 5))
```

## SecureField

Secured input
```swift 
SecureField("Enter password", text: $password)
```

## TextView

If you need multiline text input you have to use UITextView from UIKit
```swift
struct TextView: UIViewRepresentable {
    @Binding var text: String
    var font:UIFont? = UIFont(name: "HelveticaNeue", size: 15)
    var background:UIColor? = nil

    func makeCoordinator() -> Coordinator {
        Coordinator(self)
    }

    func makeUIView(context: Context) -> UITextView {
        let textView = UITextView()
        textView.delegate = context.coordinator
        if let font=self.font {
            textView.font = font
        }
        if let background = self.background {
            textView.backgroundColor = background
        }
        return textView
    }

    func updateUIView(_ uiView: UITextView, context: Context) {
        uiView.text = text
    }

    class Coordinator : NSObject, UITextViewDelegate {
        var parent: TextView
        
        init(_ uiTextView: TextView) {
            self.parent = uiTextView
        }

        func textView(_ textView: UITextView, shouldChangeTextIn range: NSRange, replacementText text: String) -> Bool {
            return true
        }

        func textViewDidChange(_ textView: UITextView) {
            self.parent.text = textView.text
        }
    }
}
```

After that you can use it
```swift
struct ContentView: View {
    @State var text:String = ""
    var body: some View {
        TextView(text: $text, background: UIColor.red)
    }
}
```


## Image

System image fits and fills frame region

<img src="images/image0.fit.png" height="200">

<img src="images/image0.fill.png" height="200">

```swift
Image(systemName: "cloud.sun.fill")
    .resizable()
    .aspectRatio(contentMode: .fill)
    .foregroundColor(.red)
    .frame(width: 300, height: 300, alignment: .center)
    .border(Color.black,width: 5)
```

Filled and clipped image

<img src="images/image1.png" width="200">

```swift 
Image(systemName: "cloud.sun.fill")
    .resizable()
    .aspectRatio(contentMode: .fill)
    .foregroundColor(.red)
    .frame(width: 300, height: 300, alignment: .center)
    .border(Color.black,width: 5)
    .clipped()
```

Image from assets with round clipped with white corner and shadow

<img src="images/image2.png" width="200">

```swift 
Image("SalvadorDali")
    .resizable()
    .aspectRatio(contentMode: .fill)
    .frame(width: 300, height: 300, alignment: .center)
    .clipShape(Circle())
    .overlay(Circle().stroke(Color.white, lineWidth: 5))
    .shadow(radius: 10)
```

For add zoom to image use `MagnificationGesture` gesture and `scaleEffect` metod

```swift
struct ContentView: View {
    @State var scale: CGFloat = 1.0
    var body: some View {
        Image("SalvadorDali")
            .resizable()
            .scaledToFit()
            .scaleEffect(scale)
            .gesture(
                MagnificationGesture()
                    .onChanged { val in
                        self.scale = val
                }
                .onEnded { val in
                    self.scale = 1.0
                }
        )
    }
}
```

## Button

Simple counter exmaple with Text and Button components
```swift
struct ContentView: View {
    @State var counter:Int = 0
    var body: some View {
        VStack {
            Text("Clicked \(counter) times")
                .font(.title)
            Button(action:{
                self.counter += 1
            }){
                Text ("Click")
            }.padding()
        }
    }
}
```

Button with background color and round corners

<img src="images/button0.png" width="300">

```swift
Button(action:{
    self.counter += 1
}){
    Text("OK")
        .foregroundColor(Color.white)
}
.frame(width: 100)
.padding()
.background(Color.blue)
.cornerRadius(10)
```

FloatingActionButton

<img src="images/fab.png" width="50">

```swift
Button(action:{
    self.counter += 1
}){
    Image(systemName: "plus")
        .foregroundColor(Color.white)
}
.padding(20)
.background(Color.blue)
.clipShape(Circle())
```

## Toggle

<img src="images/image3.png" width="300">

```swift
struct ContentView: View {
    @State var status:Bool = true
    var body: some View {
        VStack {
            Toggle(isOn: $status) {
                Text ("Switch status")
            }
        }
    }
}
```

## Picker

Static values picker

<img src="images/image4.png" width="300">

```swift
struct ContentView: View {
    @State var index = 0
    var body: some View {
        Picker(selection: $index, label:Text("Picker"), content: {
                Text("Value 1").tag(0)
                Text("Value 2").tag(1)
                Text("Value 3").tag(2)
                Text("Value 4").tag(3)
        })
    }
}
```

Static picker from values list

```swift
struct ContentView: View {
    let values:[String] = ["Value 1","Value 2","Value 3","Value 4","Value 5"]
    @State var index = "Value 1"
    var body: some View {
        Picker(selection: $index, label:Text("Picker")){
            ForEach(values, id:\.self) { value in
                Text(value)
            }
        }
        .pickerStyle(WheelPickerStyle())
        .padding()
    }
}
```

To hide label use `.labelsHidden()` and `EmptyView()` in label

```swift
Picker(selection: $index, label: EmptyView()) {
    ForEach(values, id:\.self) { value in
        Text(value)
    }
}.labelsHidden()
```

You can use different styles. Default style is `WheelPickerStyle`, but another styles may be used in `.pickerStyle()`. For iOS only  `WheelPickerStyle` and `SegmentedPickerStyle` are available. 

SegmentedPickerStyle

<img src="images/image5.png" width="300">

## DatePicker

`DatePicker` allows to select date in range. the range can be set using dates range.

<img src="images/image6.png" width="300">

```swift
struct ContentView: View {
    @State var selectedDate = Date()
    var dateClosedRange: ClosedRange<Date> {
        let min = Calendar.current.date(byAdding: .day, value: -1, to: Date())!
        let max = Calendar.current.date(byAdding: .day, value: 1, to: Date())!
        return min...max
    }
    var body: some View {
        VStack{
            DatePicker(
                selection: $selectedDate,
                in: dateClosedRange,
                displayedComponents: .date,
                label: { Text("Due Date") }
            )
            .padding()
        }
    }
}
```

You can use half-open ranges. To change display formate use `displayedComponents`

```swift
DatePicker("Date",
        selection: $selectedDate,
        in: Date()...,
        displayedComponents: [.hourAndMinute, .date]
    )
```

## Slider

Slider selects values in range. By default range is 0.0...1.0 but you can change  it with `in` and `step` properties

<img src="images/slider.png" width="300">

```swift
struct ContentView: View {
    @State var progress = 0.0

    var body: some View {
        VStack {
            Slider(value: $progress, in: 0.0...100.0, step: 20.0)
        }
    }
}
```

## Stepper
Stepper allows increment and decrement value

<img src="images/stepper.png" width="300">

```swift
struct ContentView: View {
    @State var quantity = 0
    
    var body: some View {
        Stepper("Quantity \(quantity)", value: $quantity, in: 0...10)
    }
}
```

You can use something more complex in label

```swift
struct ContentView: View {
    @State var quantity = 0
    
    var body: some View {
        Stepper(value: $quantity, in: 0...10, label: { 
            HStack {
                Text("Quantity ")
                Text("\(quantity)")
            }
        }).padding()
    }
}
```

Custom increment and decrement

```swift
struct ContentView: View {
    @State var quantity = 0
    
    var body: some View {
        Stepper(onIncrement: {
            self.quantity += 1
        }, onDecrement: {
            if self.quantity >= 10 {
                self.quantity -= 10
            }
        }, label: { Text("Quantity \(quantity)") })
    }
}
```

# Layout

## Stacks

Use `VStack`, `HStack` and `ZStack`  to organize components vertically, horizontally or on the Z axis. Use `aligment` and `spacing` to set elements positions in the stack.

```swift
struct ContentView: View {
    var body: some View {
        VStack(alignment: .center) {
            HStack(alignment: .top, spacing: 0) {
                Text("Text 1")
                Text("Text 2")
            }
            Text("Text 3")
            Image(systemName: "cloud.sun.fill")
                .resizable()
                .aspectRatio(contentMode: .fill)
                .foregroundColor(.red)
                .frame(width: 300, height: 300, alignment: .center)
                .border(Color.black,width: 5)
        }
    }
}
```

## Spacer

`Spacer` expands all available space along the major axis of parent stack.

```swift
HStack {
    Text("Hello")
    Spacer()
    Text("world")
}
```

## Divider

UI component which separates UI elements

```swift
VStack {
    Text("Hello")
    Divider()
    Text("world")
}
```

## Group

Groups UI components

```swift
Group {
    Text("Hello world")
    Image(systemName: "cloud.sun.fill")
}
```

## List

List of elements arranged in a single column

```swift
List {
    Text("Item 1")
    Button(action:{}) {
            Text ("Item 2")
        }.padding()
}
```
`List` from range

<img src="images/list0.png" width="300">

```swift
List (1..<20) { index in
    Text("Item \(index)")
}
```

`List` from strings list

```swift
struct ContentView: View {
    let list = ["1", "2", "3"]
    var body: some View {
        List(list) { item in
            Text(item)
        }
    }
}
```

To use complex objects you need to set some field as `id`

<img src="images/list1.png" width="300">

```swift
struct Icon {
    let id = UUID()
    var name:String
}

struct ContentView: View {
    let icons = [
        Icon(name: "cloud.sun.fill"),
        Icon(name: "cloud.sun.rain.fill"),
        Icon(name: "cloud.sun.bolt.fill")
    ]
    var body: some View {
        List(icons, id:\.id) { icon in
            HStack {
                Image(systemName: icon.name)
                Text(icon.name)
            }
        }
    }
}
```

Use `.onDelete` for swipe to delete

<img src="images/list2.png" width="300">

```swift
struct ContentView: View {
    @State private var galaxies = ["Andromeda Galaxy", "Backward Galaxy", "Hockey Stick Galaxies", "Lindsay-Shapley Ring"]
    
    var body: some View {
        List {
            ForEach (galaxies, id:\.self) { galaxy in
                Text ("\(galaxy)")
            }.onDelete(perform: delete)
        }
    }
    
    func delete(at offsets: IndexSet) {
        galaxies.remove(atOffsets: offsets)
    }
    
}
```

## ForEach

Use `ForEach` to create list of views in a loop. It seems `List` but may be used more flexibly.

<img src="images/foreach.png" width="300">

```swift
struct Icon {
    let id = UUID()
    var name:String
}

struct ContentView: View {
    let icons = [
        Icon(name: "cloud.sun.fill"),
        Icon(name: "cloud.sun.rain.fill"),
        Icon(name: "cloud.sun.bolt.fill")
    ]
    var body: some View {
        VStack {
            ForEach(icons, id:\.id) { icon in
                HStack {
                    Image(systemName: icon.name)
                    Text(icon.name)
                }
                .frame(maxWidth: .infinity, alignment: .center)
            }
        }
    }
}
```

## ScrollView

Scrollable view

<img src="images/scrollview0.png" width="200">

```swift
var body: some View {
    ScrollView {
        VStack {
            ForEach(1..<50){ index in
                Text("\(index)")
                    .padding()
                    .frame(width: 100, height: 50, alignment: .center)
                    .border(Color.black,width: 5)
            }
        }
        ScrollView(.horizontal, showsIndicators: false) {
            HStack {
                ForEach(1..<50){ index in
                    Text("\(index)")
                        .padding()
                        .frame(width: 80, height: 80, alignment: .center)
                        .border(Color.black,width: 5)
                }
            }
        }
    }
}
```

## Form

Container which groups components for data entry. For example settings, user forms, etc.

<img src="images/form.png" width="300">

```swift
struct ContentView: View {
    @State var email = ""
    @State var address = ""
    
    var body: some View {
        Form {
            Section(header: Text("header").font(.caption), footer: Text("footer").font(.caption)) {
                TextField("email", text:$email)
                TextField("address", text:$address)
            }
        }
    }
}
```

## TabView

Allows switch between views

<img src="images/tabbar.png" width="300">

```swift
struct ContentView: View {
    @State var selection = 0
    var body: some View {
        TabView(selection: $selection) {
            VStack{Image("SalvadorDali")
                .resizable()
                .aspectRatio(contentMode: .fill)
                .frame(width: 300, height: 300, alignment: .center)
                .clipShape(Circle())
                .overlay(Circle().stroke(Color.white, lineWidth: 5))
                .shadow(radius: 10)}
                .tabItem({
                    Image(systemName: "star")
                    Text("First tab")
                })
                .tag(0)
            List(1..<30) { index in
                Text("Item \(index)")
            }
            .tabItem({
                Image(systemName: "star.fill")
                Text("Second tab")
            })
                .tag(1)
        }
    }
}
```

## NavigationView and NavigationLink

Allows represent views stack. To switch View use `NavigationLink` with view as destination. You need to use `.buttonStyle(PlainButtonStyle())` for images and buttons if you don't want to see blue overlay color. To set title use `.navigationBarTitle()`.

<img src="images/mainScreen.png" width="200">     <img src="images/ImageScreen.png" width="200">

```swift
struct ImageView:View {
    var image:String
    
    var body: some View {
        Image(image)
            .resizable()
            .scaledToFit()
    }
}

struct ContentView: View {
    private let images = ["image1","image2","image3","image4","image5","image6","image7"]
    var body: some View {
        NavigationView {
            ScrollView {
                VStack{
                    ForEach(images, id:\.self) { image in
                        NavigationLink(destination: ImageView(image: image)) {
                            Image(image)
                                .resizable()
                                .scaledToFill()
                                .frame(width: 300, height: 300, alignment: .center)
                                .clipped()
                        }
                        .buttonStyle(PlainButtonStyle())
                    }
                }
            }.navigationBarTitle("Images gallery")
        }
    }
}
```

If you want to see navigationBarTitle in old style, use `displayMode: .inline`

```swift
    ScrollView {
        //...
    }.navigationBarTitle("Images gallery", displayMode: .inline)
```

To add buttons at the top use `navigationBarItems` modifier. You can add several buttons with `HStack`. There is no standard method to pop view from the views stack but you may use next hack with `presentationMode` to go to previous view.

```swift
struct ContentView: View {
    var body: some View {
        NavigationView {
            VStack {
                Text("First view")
                    .font(.title)
                NavigationLink(destination: SecondView()) {
                    Text("Go to second view")
                }
                .padding()
            }
            
        }.navigationViewStyle(StackNavigationViewStyle())
    }
}

struct SecondView: View {
    @State var counter:Int = 0
    
    var body: some View {
        VStack {
            Text("Second view")
                .font(.title)
            
            Text("\(self.counter)")
                .font(.title)
                .padding()
        }
        .navigationBarItems(trailing: HStack {
            NavigationLink(destination: ThirdView()) {
                Text("Third view")
            }
            Image(systemName: "plus").onTapGesture {
                self.counter = self.counter + 1
            }
        })
    }
}

struct ThirdView: View {
    @Environment(\.presentationMode) var presentationMode: Binding<PresentationMode>
    
    var body: some View {
        VStack {
            Text("Third view")
                .font(.title)
        }.navigationBarItems(trailing:
            Text("Pop view")
                .onTapGesture {
                    self.presentationMode.wrappedValue.dismiss()
        })
    }
}

```


## Alert

Default alert

<img src="images/alert1.png" width="200">

```swift
@State var isAlert = false
    
var body: some View {
    VStack (alignment: .center) {
        Button(action: {
            self.isAlert = true
        }) {
            Text("Alert").padding().background(Color.red).foregroundColor(Color.white)
        }.alert(
            isPresented: $isAlert,
            content: {
                Alert(title: Text("Title"),
                        message: Text("Message"),
                        dismissButton: .default(Text("OK"))
                )
        })
    }
}
```

Custom buttons

<img src="images/alert2.png" width="200">

```swift
Alert(
    title: Text("Title"),
    message: Text("Message"),
    primaryButton: .default(Text("OK")){print("OK pressed")},
    secondaryButton: .cancel(){print("Cancel pressed")}
)
```

## Modal

Show modal activity

<img src="images/modal1.png" width="200"> <img src="images/modal2.png" width="200">

```swift
struct ContentView: View {
    private let images = ["image1","image2","image3","image4","image5","image6","image7"]
    @State var isModalActive:Bool = false
    @State var image = ""
    var body: some View {
        ScrollView {
            VStack{
                ForEach(images, id:\.self) { image in
                    Image(image)
                        .resizable()
                        .scaledToFill()
                        .frame(width: 300, height: 300, alignment: .center)
                        .clipped()
                        .onTapGesture {
                            self.isModalActive = true
                            self.image = image
                        }
                        .sheet(isPresented: self.$isModalActive, content: {
                            self.modal
                        })
                }
            }
        }
    }
    
    var modal: some View {
        Image(image)
        .resizable()
        .scaledToFit()
    }
}
```

## ActionSheet

Displays action sheet

<img src="images/actions.png" width="200">

```swift
struct ContentView: View {
    @State var isActionsActive:Bool = false
    
    var body: some View {
        VStack{
            Button("Action Sheet") {
                self.isActionsActive = true
            }.actionSheet(isPresented: $isActionsActive, content: {
                ActionSheet(title: Text("Title"),
                            message: Text("Message"),
                            buttons: [
                                .default(Text("OK"), action: {
                                    print("OK pressed")
                                }),
                                .destructive(Text("Delete"), action: {
                                    print("Delete pressed")
                                })
                    ]
                )
            })
        }
    }
}
```

# Tips annd tricks

## Resize window when keybord is shown

Usually need to change view size when keyboard is shown. To do this create observer to get keyboard notifications and change view bottom padding dynamically.

<img src="images/keyboard1.png" width="200"> <img src="images/keyboard2.png" width="200">

```swift
import Foundation
import SwiftUI
import Combine

class KeyboardObserver: ObservableObject {
    
    private var cancellable: AnyCancellable?
    
    @Published private(set) var keyboardHeight: CGFloat = 0
    
    let keyboardWillShow = NotificationCenter.default
        .publisher(for: UIResponder.keyboardWillShowNotification)
        .compactMap { ($0.userInfo?[UIResponder.keyboardFrameEndUserInfoKey] as? CGRect)?.height }
    
    let keyboardWillHide = NotificationCenter.default
        .publisher(for: UIResponder.keyboardWillHideNotification)
        .map { _ -> CGFloat in 0 }
    
    init() {
        cancellable = Publishers.Merge(keyboardWillShow, keyboardWillHide)
            .subscribe(on: RunLoop.main)
            .assign(to: \.keyboardHeight, on: self)
    }
}

struct ContentView: View {
    @ObservedObject var keyboardObserver = KeyboardObserver()
    @State var text = ""
    
    var body: some View {
        VStack (alignment: .center) {
            Spacer()
            TextField("Text", text: $text)
                .padding()
                .textFieldStyle(RoundedBorderTextFieldStyle())
        }
        .padding(.bottom, keyboardObserver.keyboardHeight)
        .animation(.easeInOut(duration:0.2))
    }
}
```

## Hide keyboard

To hide keyboard on component tap create `endEditing()` method in `View`

```swift
extension View {
    func endEditing() {
        UIApplication.shared.sendAction(#selector(UIResponder.resignFirstResponder), to: nil, from: nil, for: nil)
    }
}

struct ContentView: View {
    @State var text = ""
    
    var body: some View {
        VStack (alignment: .center) {
            TextField("Text", text: $text)
                .padding()
                .textFieldStyle(RoundedBorderTextFieldStyle())
                .onTapGesture { self.endEditing() }
        }
    }
}
```

