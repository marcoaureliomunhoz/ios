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

**NavigationBar**  

- Adicionando botões:

```swift 
import UIKit
class MinhaViewController: UIViewController {
    override func viewDidLoad() {
        super.viewDidLoad()

        //botões
        let btn1 = UIBarButtonItem(title: "BT1", style: UIBarButtonItemStyle.plain, target: self, action: #selector(MinhaViewControlle.btn1Action))
        let btn2 = UIBarButtonItem(image: UIImage(named: "bt2"), style: UIBarButtonItemStyle.plain, target: self, action: #selector(MinhaViewControlle.btn1Action))
        self.navigationItem.rightBarButtonItems = [btn1, btn2]
    }

    func btn1Action() {
        ...
    }

    func btn2Action() {
        ...
    }
}
```   

Referências:
- https://developer.apple.com/documentation/uikit/uinavigationbar
- https://developer.apple.com/documentation/uikit/uibarbuttonitem
- https://developer.apple.com/documentation/uikit/uinavigationitem/1624956-rightbarbuttonitems
- https://developer.apple.com/documentation/uikit/uinavigationitem/1624946-leftbarbuttonitems
- http://swiftdeveloperblog.com/customize-uinavigationbar-appearance-in-swift
- https://stackoverflow.com/questions/18929864/how-to-change-navigation-bar-color-in-ios-7
- https://www.appcoda.com/customize-navigation-status-bar-ios-7

**Threads**  

É uma regra em diversas plataformas que as views só devem ser modificadas na thread principal e nunca numa thread secundária em background. A thread principal é responsável por tratar os eventos do usuário e por atualizar a interface. É recomendável que trabalhos pesados/demorados sejam executados em threads separadas e somente ao final que a thread principal deve ser acessada. 

- Exemplo1 - usando **Grand Central Dispatch (GCD)**:

```swift 
let queue = DispatchQueue.global() //fila de threads secundárias 
let main = DispatchQueue.main //thread principal 
queue.async {
    //aqui você faz o trabalho pesado, por exemplo, download, acesso a api...
    //...
    //ao final do processamento podemos então acessar a thread principal para atualizar a tela do usuário
    main.async {
        //aqui você atualiza as views necessárias
        //...
    }
}
```

- Exemplo2 - usando **OperationQueue**: 

```swift 
let queue = OperationQueue() //fila de threads secundárias 
let main = OperationQueue.main //thread principal 
queue.addOperation({
    //aqui você faz o trabalho pesado, por exemplo, download, acesso a api...
    //...
    //ao final do processamento podemos então acessar a thread principal para atualizar a tela do usuário
    main.addOperation({
        //aqui você atualiza as views necessárias
        //...
    })
})
```

**Requisições HTTP com URLSession e URLRequest**  

```swift 
let http = URLSession.shared 
let url = URL(string:"https://seudominio/api/get")
let request = URLRequest(url: url!)
let task = http.dataTask(with: request, completionHandler: {(data, response, error) -> Void in
    if let error = error {
        print("Erro ao realizar requisição HTTP: \(error)")
        return
    }
    if let data = data {
        //Aqui você realiza o parse os dados recebidos 
        //...
        //Se for necessário atualizar a tela do usuário
        DispatchQueue.main.async {
            //Aqui você atualiza a tela do usuário 
            //...
        }
    }
})
task.resume()
```

Referências

- https://developer.apple.com/documentation/foundation/urlsession
- https://gist.github.com/cmoulton/7ddc3cfabda1facb040a533f637e74b8
- 

**JSON**  

Para obter um array de objetos não mapeados a partir de json: 

```swift 
class func parserJson(_ data: Data) -> Array<TipoQualquer> {
    var arrayTipo = Array<TipoQualquer> = []
    do {
        let arrayObj = try JSONSerialization.jsonObject(with: data, options: JSONSerialization.ReadingOptions.mutableContainers) as! NSArray
        for obj in arrayObj {
            let dict = obj as! NSDictionary
            let tipo = TipoQualquer()
            tipo.prop1 = dict["prop1"] as! String
            tipo.prop2 = dict["prop2"] as! String
            ...
            arrayTipo.append(tipo)
        }
    } catch let error as NSError {
        print("Erro ao ler JSON \(error)")
    }
    return arrayTipo
}
```

Para obter um array de objetos mapeados a partir de json: 
```swift 
class TipoQualquer: Codable {
    var prop1: String
    var prop2: String
    var prop3: Int
}
class func parserJson(_ data: Data) -> Array<TipoQualquer> {
    let decoder = JSONDecoder()
    do {
        return try decoder.decode([TipoQualquer].self, from: data)
    } catch {
        print("Erro ao ler JSON \(error)")
    }
    return nil
}
```


Referências
- https://developer.apple.com/documentation/foundation/jsonserialization
- https://developer.apple.com/documentation/foundation/jsonencoder
- https://developer.apple.com/documentation/foundation/jsondecoder 
- https://benscheirman.com/2017/06/swift-json
- https://grokswift.com/json-swift-4
- https://github.com/Hearst-DD/ObjectMapper

---

**Fontes**  

- http://www.livroandroid.com.br/site/pages/livro-iphone.php 
- https://github.com/livroios  
- https://developer.apple.com  
- https://swift.org  
- http://swiftdeveloperblog.com 
- https://thatthinginswift.com
- http://www.spotlessicode.com