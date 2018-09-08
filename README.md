# iOS  

**Antes de Tudo** 

Depois de criar uma aplicação **Single View Application**, temos que seguir alguns passos:

- Primeiro temos que apagar a ViewController e a Main.storybord criadas.
- Depois temos que configurar a propriedade de projeto **Main interface** na guia **General**. Por padrão o projeto vem configurado para ser carregado via **Main.storybord**, mas em projetos grandes usar storybord pode atrapalhar na produtividade. Temos então que deixar essa propriedade em branco.
- Depois temos que alterar a função *application* da classe **AppDelegate** presente no arquivo **AppDelegate.swift** para criar uma window e definir a root view controller.

```swift  
import UIKit
@UIApplicationMain
class AppDelegate: UIResponder, UIApplicationDelegate {

    var window: UIWindow?

    func application(
            _ application:UIAplication,
            didFinishLaunchingWithOptions launchOptions:[UIApplicationLaunchOptionsKey:Any]?
        ) -> Bool  
    {
        //primeiro criamos a window
        self.window = UIWindow(frame: UIScreen.main.bounds)

        //podemos configurar a window
        self.window!.backgroundColor = UIColor.white

        //depois temos que indicar a root view controller
        self.window!.rootViewController = <ui-view-controller>

        //depois de tudo feito podemos então construir e visualizar
        self.window!.makeKeyAndVisible()
        return true
    }

}
```  

**TabBar**  

A TabBar no iPhone fica posicionada na parte inferior da tela. As classes responsáveis por nos fornecer uma TabBar é a **UITabBarController** e a **UINavigationController**.  

- **Arquivo AppDelegate.swift**

```swift  
import UIKit
@UIApplicationMain
class AppDelegate: UIResponder, UIApplicationDelegate {

    var window: UIWindow?

    func application(
            _ application:UIAplication,
            didFinishLaunchingWithOptions launchOptions:[UIApplicationLaunchOptionsKey:Any]?
        ) -> Bool  
    {
        self.window = UIWindow(frame: UIScreen.main.bounds)
        self.window!.backgroundColor = UIColor.white

        //primeiro temos que criar e configurar as navigations, indicando para cada navbar a sua respectiva viewcontroller
        let navBar1 = UINavigationController()
        navBar1.pushViewController(<aqui-vai-a-uiviewcontroller1>, animated:false)
        navBar1.tabBarItem.title = "Tab1"
        navBar1.tabBarItem.image = UIImage(named:"tab1.png")

        let navBar2 = UINavigationController()
        navBar2.pushViewController(<aqui-vai-a-uiviewcontroller2>, animated:false)
        navBar2.tabBarItem.title = "Tab2"
        navBar2.tabBarItem.image = UIImage(named:"tab2.png")

        //agora podemos criar a tabbar e definir as navbars que a tabbar deve renderizar através de um array
        let tabBar = UITabBarController()
        tabBar.viewControllers = [navBar1, navBar2]

        //agora temos que indicar para a aplicação que a root view controller será a tab bar controller
        self.window!.rootViewController = tabBar

        self.window!.makeKeyAndVisible()
        return true
    }

}
````  

- **WebView**  

Para carregar uma URL:
```swift  
@IBOutlet var webView: UIWebView!
...
let url = URL(string: "https://www.platelo.com.br")
let request = URLRequest(url: url!)
self.webView.loadRequest(request)
```  

Para carregar uma página local:
```swift  
@IBOutlet var webView: UIWebView!
...
let path = Bundle.main.path(forResource: "index", ofType: "html")
let url = URL(fileURLWithPath: path!)
let request = URLRequest(url: url!)
self.webView.loadRequest(request)
```

Para interceptar eventos (delegates) temos que implementar o protocolo **UIWebViewDelegate** que nos fornece os seguintes métodos opcionais: 
- webView(_ webView:UIWebView, request:URLRequest, navigationType:UIWebNavigationType) -> Bool: através deste método podemos interceptar a requisição, verificar a url da requisição e confirmar ou não a navegação.
- webViewDidStartLoad(_ webView:UIWebView): método chamado antes da requisição.
- webViewDidFinishLoad(_ webView:UIWebView): método chamado após requisição com sucesso.
- webView(_ webView:UIWebView, didFailLoadWithError): método chamado após requsição com erro.

> Por padrão, a partir do iOS 9, requisições HTTP não são permitidas, pois não são seguras. Podemos configurar o projeto para que requisições HTTP sejam aceitas. Para isso temos que alterar o arquivo **Info.plist** e adicionar o item **App Transport Security Settings** e neste um subitem **Allow Arbitrary Loads** com valor **YES**.

---

**Fontes**  

- http://www.livroandroid.com.br/site/pages/livro-iphone.php 
- https://github.com/livroios  
- https://developer.apple.com  
- https://swift.org  