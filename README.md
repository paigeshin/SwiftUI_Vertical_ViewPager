# TabContrller using UIViewRepresentable

```swift
//
//  ContentView.swift
//  VerticalPaginationView
//
//  Created by paige shin on 2023/07/26.
//

import SwiftUI

struct ContentView: View {
    @State private var currentPageIndex = 0
    
    var body: some View {
        VerticalPageViewController(pages(), currentPageIndex: self.$currentPageIndex)
            .frame(height: UIScreen.main.bounds.height)
            .ignoresSafeArea()
    }
    
    func pages() -> [AnyView] {
        [
            ZStack {
                Color.red
                Text("Page 1")
            }.frame(maxWidth: .infinity, maxHeight: .infinity)
                .ignoresSafeArea()
                .eraseToAnyView(),
            ZStack {
                Color.green
                Text("Page 2")
            }.frame(maxWidth: .infinity, maxHeight: .infinity)
                .ignoresSafeArea()
                .eraseToAnyView(),
            ZStack {
                Color.blue
                Text("Page 3")
            }.frame(maxWidth: .infinity, maxHeight: .infinity)
                .ignoresSafeArea()
                .eraseToAnyView()
            
        ]
        
    }
    
}

extension View {
    
    func eraseToAnyView() -> AnyView {
        AnyView(self)
    }
    
}


struct ContentView_Previews: PreviewProvider {
    static var previews: some View {
        ContentView()
    }
}

struct VerticalPageViewController<Page: View>: UIViewControllerRepresentable {
    var pages: [Page]
    @Binding var currentPageIndex: Int
    
    init(_ pages: [Page], currentPageIndex: Binding<Int>) {
        self.pages = pages
        self._currentPageIndex = currentPageIndex
    }
    
    func makeUIViewController(context: Context) -> UIPageViewController {
        let pageViewController = UIPageViewController(
            transitionStyle: .scroll,
            navigationOrientation: .vertical)
        
        pageViewController.dataSource = context.coordinator
        pageViewController.delegate = context.coordinator
        
        return pageViewController
    }
    
    func updateUIViewController(_ pageViewController: UIPageViewController, context: Context) {
        pageViewController.setViewControllers(
            [context.coordinator.controllers[currentPageIndex]],
            direction: .forward,
            animated: true)
    }
    
    func makeCoordinator() -> Coordinator {
        Coordinator(self)
    }
    
    class Coordinator: NSObject, UIPageViewControllerDataSource, UIPageViewControllerDelegate {
        var parent: VerticalPageViewController
        var controllers = [UIViewController]()
        
        init(_ pageViewController: VerticalPageViewController) {
            self.parent = pageViewController
            self.controllers = self.parent.pages.map { UIHostingController(rootView: $0) }
        }
        
        func pageViewController(_ pageViewController: UIPageViewController, viewControllerBefore viewController: UIViewController) -> UIViewController? {
            guard let index = self.controllers.firstIndex(of: viewController) else {
                return nil
            }
            if index == 0 {
                return self.controllers.last
            }
            return self.controllers[index - 1]
        }
        
        func pageViewController(_ pageViewController: UIPageViewController, viewControllerAfter viewController: UIViewController) -> UIViewController? {
            guard let index = controllers.firstIndex(of: viewController) else {
                return nil
            }
            if index + 1 == self.controllers.count {
                return self.controllers.first
            }
            return self.controllers[index + 1]
        }
        
        func pageViewController(_ pageViewController: UIPageViewController, didFinishAnimating finished: Bool, previousViewControllers: [UIViewController], transitionCompleted completed: Bool) {
            if completed,
               let visibleViewController = pageViewController.viewControllers?.first,
               let index = self.controllers.firstIndex(of: visibleViewController) {
                self.parent.currentPageIndex = index
            }
        }
    }
}

```


# TabView
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
