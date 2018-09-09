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

**WebView**  

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

**TableView**  

O componente TableView é usado para listar conteúdos em uma aplicação iOS. 

- Para usar a TableView temos que implementar os protocolos **UITableViewDataSource** e **UITableViewDelegate** e no método *viewDidLoad()* da ViewContoller temos que informar para a TableView que o fornecedor de dados é a própria ViewController e que o delegate da table view também é própria ViewController. Temos também que informar qual será o formato das linhas/células.

```swift  
import UIKit
class ListaViewController: UIViewController, UITableDataSource, UITableViewDelegate {

    @IBOutlet var tableView: UITableView!

    var lista: Array<TipoQualquer> = []

    override func viewDidLoad() {
        //aqui estamos informando para a table view que o fornecedor de dados é a própria view controller
        self.tableView.dataSource = self

        //aqui estamos informando para a table view que o delegate está na própria controller
        self.tableView.delegate = self

        //aqui estamos informando para a table view que o formato das células é o formato padrão UITableViewCell
        self.tableView.register(UITableViewCell.self, forCellReuseIdentifier: "cell")  

        //aqui estamos acionando um serviço que tem como responsabilidade nos fornecer uma lista de TipoQualquer
        self.lista = TipoQualquerService.getLista()
    }

    //aqui estamos implementando os métodos do protocolo UITableDataSource

    //este método deve retornar a quantidade de registros que serão listados
    func tableView(_ tableView: UITableView, numberOfRowsInSection section: Int) -> Int {
        return self.lista.count
    }

    //este método deve retornar a célula com os dados
    func tableView(_ tableView: UITableView, cellForRowAt indexPath: IndexPath) -> UITableViewCell {
        let cell = self.tableView.dequeueReusableCell(withIdentifier: "cell")
        let linha = indexPath.row 
        let item = self.lista[linha] 
        cell.textLabel!.text = item.texto 
        cell.imageView!.image = UIImage(named: item.url)
        return cell
    }

    //aqui estamos implementando os métodos do protocolo UITableViewDelegate

    //este método será chamado quando o usuário tocar sobre uma linha/célula
    func tableView(_ tableView: UITableView, didSelectRowAt indexPath: IndexPath) {
        let linha = indexPath.row 
        let item = self.lista[linha] 

        let alert = UIAlertController(title: "Alerta", message: "Clicou na linha \(linha)", preferredStyle: UIAlertControllerStyle.alert)
        alert.addAction(UIAlertAction(title: "OK", style: UIAlertActionStyle.default, handler: nil))
        self.present(alert, animated: true, completion: nil)
    }

}
```

No exemplo acima utilizamos a célula padrão da TableView, mas é possível customizar a célula de acordo com a nossa necessidade. Para isso temos que criar uma classe do tipo **UITableViewCell**, temos que criar um arquivo de visão (xib) para a célula onde então customizados sobre o componente **TableViewCell**. Depois de realizada a customização temos que informar para a table view que ela deve renderizar a célula customizada.

```swift  
let xib = UINib(nibName: "NomeDaMinhaClasseTableViewCell", bundle: nil)
self.tableView.register(xib, forCellReuseIdentifier: "cell")
```  

---

**Fontes**  

- http://www.livroandroid.com.br/site/pages/livro-iphone.php 
- https://github.com/livroios  
- https://developer.apple.com  
- https://swift.org  