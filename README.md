# WalkthroughPageAnimation
SwiftUI App Intro Animation's - Walkthrough Page Animation's - OnBoarding Screen's - Login Page UI

https://www.youtube.com/watch?v=9Ztm5ePwY4k&list=TLGGql6YhFJnsjowMjA0MjAyMw

## Set up Project
<img width="300" alt="スクリーンショット 2023-04-03 10 09 49" src="https://user-images.githubusercontent.com/47273077/229390425-cfb91bd4-fe91-41bd-b442-302a3b88f9b2.gif">

PageIntro.swift
```swift
import SwiftUI

/// Page Intro Model
struct PageIntro: Identifiable, Hashable {
    var id: UUID = .init()
    var introAssetImage: String
    var title: String
    var subTitle: String
    var displaysAction: Bool = false
}

var pageIntros: [PageIntro] = [
    .init(introAssetImage: "Page 1", title: "Connect With\nCreators Easily", subTitle: "Thank you for choosing us, we can save your lovely time."),
    .init(introAssetImage: "Page 2", title: "Get Inspiration\nFrom Creators", subTitle: "Find your favourite creator and get inspired by them."),
    .init(introAssetImage: "Page 3", title: "Let's\nGet Started", subTitle: "To register for an account, kindly enter your details.", displaysAction: true),
]
```

CustomIndicatorView.swift
```swift
import SwiftUI

struct CustomIndicatorView: View {
    /// View Properties
    var totalPages: Int
    var currentPage: Int
    var activeTint: Color = .black
    var inActiveTint: Color = .gray.opacity(0.5)
    
    var body: some View {
        HStack(spacing: 8) {
            ForEach(0..<totalPages, id: \.self) {
                Circle()
                    .fill(currentPage == $0 ? activeTint : inActiveTint)
                    .frame(width: 4, height: 4)
            }
        }
    }
}

struct CustomIndicatorView_Previews: PreviewProvider {
    static var previews: some View {
        ContentView()
    }
}

```

Home.swift
```swift
import SwiftUI

struct Home: View {
    /// View Properties
    @State private var activeIntro: PageIntro = pageIntros[0]
    var body: some View {
        GeometryReader {
            let size = $0.size
            
            IntroView(intro: $activeIntro, size: size)
        }
        .padding(15)
    }
}

struct Home_Previews: PreviewProvider {
    static var previews: some View {
        ContentView()
    }
}

/// Intro View
struct IntroView: View {
    @Binding var intro: PageIntro
    var size: CGSize
    
    var body: some View {
        VStack {
            /// Image View
            GeometryReader {
                let size = $0.size
                
                Image(intro.introAssetImage)
                    .resizable()
                    .aspectRatio(contentMode: .fit)
                    .padding(15)
                    .frame(width: size.width, height: size.height)
            }
            
            /// Title & Action's
            VStack(alignment: .leading, spacing: 10) {
                Spacer(minLength: 0)
                
                Text(intro.title)
                    .font(.system(size: 40))
                    .fontWeight(.black)
                
                Text(intro.subTitle)
                    .font(.caption)
                    .foregroundColor(.gray)
                
                if !intro.displaysAction {
                    Group {
                        Spacer(minLength: 25)
                        
                        /// Custom Indicator View
                        CustomIndicatorView(totalPages: filteredPages.count, currentPage: filteredPages.firstIndex(of: intro) ?? 0)
                            .frame(maxWidth: .infinity)
                        
                        Spacer(minLength: 10)
                        
                        Button {
                            changeIntro()
                        } label: {
                            Text("Next")
                                .fontWeight(.semibold)
                                .foregroundColor(.white)
                                .frame(width: size.width * 0.4)
                                .padding(.vertical, 15)
                                .background {
                                    Capsule()
                                        .fill(.black)
                                }
                        }
                        .frame(maxWidth: .infinity)
                    }
                } else {
                    
                }
            }
            .frame(maxWidth: .infinity, alignment: .leading)
        }
    }
    
    /// Updating Page Intro's
    func changeIntro() {
        if let index = pageIntros.firstIndex(of: intro), index != pageIntros.count - 1 {
            intro = pageIntros[index + 1]
        } else {
            intro = pageIntros[pageIntros.count - 1]
        }
    }
    
    var filteredPages: [PageIntro] {
        return pageIntros.filter { !$0.displaysAction }
    }
}
```

## Set Cutom TextField
<img width="300" alt="スクリーンショット 2023-04-03 10 32 31" src="https://user-images.githubusercontent.com/47273077/229392290-b0fafc68-40f4-4bc1-a0f6-166f9dc90235.png">

CustomTextField.swift
```swift

import SwiftUI

struct CustomTextField: View {
    @Binding var text: String
    var hint: String
    var leadingIcon: Image
    var isPassword: Bool = false
    var body: some View {
        HStack(spacing: -10) {
            leadingIcon
                .font(.callout)
                .foregroundColor(.gray)
                .frame(width: 40, alignment: .leading)
            
            if isPassword {
                SecureField(hint, text: $text)
            } else {
                TextField(hint, text: $text)
            }
        }
        .padding(.horizontal, 15)
        .padding(.vertical, 15)
        .background {
            RoundedRectangle(cornerRadius: 12, style: .continuous)
                .fill(.gray.opacity(0.1))
        }
    }
}
```

Home.swift
```swift
import SwiftUI

struct Home: View {
    /// View Properties
    @State private var activeIntro: PageIntro = pageIntros[0]
    @State private var emailID: String = ""
    @State private var password: String = ""
    var body: some View {
        GeometryReader {
            let size = $0.size
            
            IntroView(intro: $activeIntro, size: size) {
                /// User Login/Signup View
                VStack(spacing: 10) {
                    /// Custom TextField
                    CustomTextField(text: $emailID, hint: "Email Address", leadingIcon: Image(systemName: "envelope"))
                }
            }
        }
        .padding(15)
    }
}

struct Home_Previews: PreviewProvider {
    static var previews: some View {
        ContentView()
    }
}

/// Intro View
struct IntroView<ActionView: View>: View {
    @Binding var intro: PageIntro
    var size: CGSize
    var actionView: ActionView
    
    init(intro: Binding<PageIntro>, size: CGSize, @ViewBuilder actionView: @escaping () -> ActionView) {
        self._intro = intro
        self.size = size
        self.actionView = actionView()
    }
    
    var body: some View {
        VStack {
            /// Image View
            GeometryReader {
                let size = $0.size
                
                Image(intro.introAssetImage)
                    .resizable()
                    .aspectRatio(contentMode: .fit)
                    .padding(15)
                    .frame(width: size.width, height: size.height)
            }
            
            /// Title & Action's
            VStack(alignment: .leading, spacing: 10) {
                Spacer(minLength: 0)
                
                Text(intro.title)
                    .font(.system(size: 40))
                    .fontWeight(.black)
                
                Text(intro.subTitle)
                    .font(.caption)
                    .foregroundColor(.gray)
                
                if !intro.displaysAction {
                    Group {
                        Spacer(minLength: 25)
                        
                        /// Custom Indicator View
                        CustomIndicatorView(totalPages: filteredPages.count, currentPage: filteredPages.firstIndex(of: intro) ?? 0)
                            .frame(maxWidth: .infinity)
                        
                        Spacer(minLength: 10)
                        
                        Button {
                            changeIntro()
                        } label: {
                            Text("Next")
                                .fontWeight(.semibold)
                                .foregroundColor(.white)
                                .frame(width: size.width * 0.4)
                                .padding(.vertical, 15)
                                .background {
                                    Capsule()
                                        .fill(.black)
                                }
                        }
                        .frame(maxWidth: .infinity)
                    }
                } else {
                    actionView
                }
            }
            .frame(maxWidth: .infinity, alignment: .leading)
        }
    }
    
    /// Updating Page Intro's
    func changeIntro() {
        if let index = pageIntros.firstIndex(of: intro), index != pageIntros.count - 1 {
            intro = pageIntros[index + 1]
        } else {
            intro = pageIntros[pageIntros.count - 1]
        }
    }
    
    var filteredPages: [PageIntro] {
        return pageIntros.filter { !$0.displaysAction }
    }
}
```

