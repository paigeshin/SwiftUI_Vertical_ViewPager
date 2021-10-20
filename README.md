```swift
//
//  ContentView.swift
//  VerticalPager
//
//  Created by paige on 2021/10/20.
//

import SwiftUI
import AVKit

struct ContentView: View {
    var body: some View {
        NavigationView {
            Home()
                // color scheme is working inside navigation bar may be it's a bug..
                .preferredColorScheme(.dark)
                .navigationTitle("")
                .navigationBarHidden(true)
        }
    }
}

struct ContentView_Previews: PreviewProvider {
    static var previews: some View {
        ContentView()
    }
}

struct Home: View {
    @State var index = 0
    var edges = UIApplication.shared.windows.first?.safeAreaInsets
    
    var body: some View {
        TabView(selection: $index)
        {
            ForEach(1...10, id: \.self) { i in
                VStack {
                    HStack {
                        Spacer()
                        Text("Hello")
                    }
                    Spacer()
                    Text("Bye\(i)")
                }
                .padding()
                .rotationEffect(.init(degrees: -90))
                // setting width...
                .frame(width: UIScreen.main.bounds.width)
                .tag(i)
            }
        }
        .rotationEffect(.init(degrees: 90))
        // if view is rotated means width will be equal to height
        // removing edges values
        .frame(width: UIScreen.main.bounds.height - (edges!.top + edges!.bottom))
        .tabViewStyle(PageTabViewStyle(indexDisplayMode: .never))
        .background(Color.black.ignoresSafeArea(.all, edges: .all))
        
        // Simple Logic...
        // rotating views and changing width and height

    }
}
```
