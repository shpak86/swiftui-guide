# swiftui-guide

## Simple text field
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

## TextField
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

FloatingActionButton example

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

Static picker values

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

Static from values list.

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

You can use different styles. Default style is `WheelPickerStyle`, but another styles may be used in `.pickerStyle()`. For iOS only  `WheelPickerStyle` and `SegmentedPickerStyle` are available. 

SegmentedPickerStyle

<img src="images/image5.png" width="300">

## DatePicker

DatePicker allows to select date in range. the range can be set using dates range.

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
        Stepper(value: $quantity, in: 0...10, label: { HStack{
            Text("Quantity ")
            Text("\(quantity)")
            }
        }).padding()
    }
}
```

God mode is on

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