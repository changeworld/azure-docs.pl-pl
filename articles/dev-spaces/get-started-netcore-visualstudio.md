---
title: Tworzenie środowiska deweloperskiego Kubernetes w chmurze przy użyciu platformy .NET Core i programu Visual Studio | Microsoft Docs
titleSuffix: Azure Dev Spaces
services: azure-dev-spaces
ms.service: azure-dev-spaces
ms.component: azds-kubernetes
author: ghogen
ms.author: ghogen
ms.date: 05/11/2018
ms.topic: tutorial
description: Szybkie tworzenie w środowisku Kubernetes za pomocą kontenerów i mikrousług na platformie Azure
keywords: Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, containers
manager: douge
ms.openlocfilehash: 012efcbd3fa87268f3a68fdac524ce8310d10120
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/20/2018
ms.locfileid: "34362060"
---
# <a name="get-started-on-azure-dev-spaces-with-net-core-and-visual-studio"></a>Rozpoczęcie pracy w usłudze Azure Dev Spaces za pomocą platformy .NET Core i programu Visual Studio

Niniejszy przewodnik zawiera informacje na temat wykonywania następujących czynności:

- Tworzenie na platformie Azure środowiska Kubernetes zoptymalizowanego pod kątem tworzenia.
- Iteracyjne tworzenie kodu w kontenerach przy użyciu programu Visual Studio.
- Niezależne tworzenie dwóch oddzielnych usług i wywoływanie innej usługi przy użyciu funkcji odnajdywania usług DNS w środowisku Kubernetes.
- Efektywne tworzenie i testowanie kodu w środowisku zespołu.

[!INCLUDE[](includes/see-troubleshooting.md)]

[!INCLUDE[](includes/portal-aks-cluster.md)]

## <a name="get-the-visual-studio-tools"></a>Pobieranie narzędzi Visual Studio
1. Instalowanie najnowszej wersji programu [Visual Studio 2017](https://www.visualstudio.com/vs/)
1. W instalatorze programu Visual Studio upewnij się, że wybrano następujące obciążenie:
    * Tworzenie aplikacji na platformie ASP.NET i aplikacji internetowych
1. Zainstaluj [rozszerzenie programu Visual Studio dla usługi Azure Dev Spaces](https://aka.ms/get-azds-visualstudio)

Teraz możesz przystąpić do tworzenia aplikacji internetowej ASP.NET za pomocą programu Visual Studio.

## <a name="create-an-aspnet-web-app"></a>Tworzenie aplikacji sieci Web platformy ASP.NET

W programie Visual Studio 2017 utwórz nowy projekt. Obecnie projekt musi być **aplikacją internetową ASP.NET Core**. Nazwij projekt „**webfrontend**”.

![](media/get-started-netcore-visualstudio/NewProjectDialog1.png)

Wybierz szablon **Aplikacja internetowa (Model-View-Controller)** i upewnij się, że na dwóch listach rozwijanych w górnej części okna dialogowego zostały wybrane pozycje **.NET Core** i **ASP.NET Core 2.0**. Kliknij przycisk **OK**, aby utworzyć projekt.

![](media/get-started-netcore-visualstudio/NewProjectDialog2.png)


## <a name="create-a-dev-environment-in-azure"></a>Tworzenie środowiska deweloperskiego na platformie Azure

W usłudze Azure Dev Spaces możesz tworzyć środowiska deweloperskie oparte na środowisku Kubernetes, które są w pełni zarządzane przez usługę Azure i zoptymalizowane na potrzeby tworzenia. Po otwarciu utworzonego przed chwilą projektu wybierz usługę **Azure Dev Spaces** z listy rozwijanej ustawień uruchamiania, jak pokazano poniżej.

![](media/get-started-netcore-visualstudio/LaunchSettings.png)

W wyświetlonym następnie oknie dialogowym upewnij się, że logujesz się przy użyciu odpowiedniego konta, a następnie wybierz istniejący klaster Kubernetes.

![](media/get-started-netcore-visualstudio/Azure-Dev-Spaces-Dialog.PNG)

Na liście rozwijanej **Miejsce** zostaw na razie ustawienie domyślne `default`. Później dowiesz się więcej na temat tej opcji. Zaznacz pole wyboru **Publicznie dostępne**, aby aplikacja internetowa była dostępna za pośrednictwem publicznego punktu końcowego. To ustawienie nie jest wymagane, ale przyda się do zademonstrowania pewnych pojęć w dalszej części tego przewodnika. Nie przejmuj się — w każdym przypadku będzie można debugować witrynę internetową przy użyciu programu Visual Studio.

![](media/get-started-netcore-visualstudio/Azure-Dev-Spaces-Dialog2.png)

Kliknij przycisk **OK**, aby wybrać lub utworzyć klaster.

Jeśli wybierzesz klaster, który nie został aktywowany do pracy z usługą Azure Dev Spaces, zobaczysz komunikat z pytaniem, czy chcesz go skonfigurować.

![](media/get-started-netcore-visualstudio/Add-Azure-Dev-Spaces-Resource.png)

Wybierz pozycję **OK**.

 W celu wykonania tej czynności zostanie uruchomione zadanie w tle. Ukończenie procedury zajmie kilka minut. Aby zobaczyć, czy zadanie jest w toku, umieść kursor myszy na ikonie **Zadania w tle** w lewym dolnym rogu paska stanu, jak pokazano na poniższej ilustracji.

![](media/get-started-netcore-visualstudio/BackgroundTasks.PNG)

> [!Note]
> Dopóki środowisko deweloperskie nie zostanie pomyślnie utworzone, nie można debugować aplikacji.

## <a name="look-at-the-files-added-to-project"></a>Przyglądanie się plikom dodanym do projektu
Czekając na utworzenie środowiska deweloperskiego, przyjrzyj się plikom, które zostały dodane do projektu podczas wybierania opcji korzystania ze środowiska deweloperskiego.

Najpierw możesz zobaczyć, że został dodany folder o nazwie `charts`, a w tym folderze został utworzony szkielet [planu narzędzia Helm](https://docs.helm.sh) dla Twojej aplikacji. Te pliki służą do wdrażania aplikacji w środowisku deweloperskim.

Zobaczysz, że został dodany plik o nazwie `Dockerfile`. Ten plik zawiera informacje potrzebne do spakowania aplikacji w standardowym formacie Docker. Został także utworzony plik `HeaderPropagation.cs`, który omówimy w dalszej części przewodnika. 

Na koniec zobaczysz plik o nazwie `azds.yaml`, który zawiera informacje o konfiguracji wymagane przez środowisko deweloperskie, na przykład informacje o tym, czy aplikacja powinna być dostępna za pośrednictwem publicznego punktu końcowego.

![](media/get-started-netcore-visualstudio/ProjectFiles.png)

## <a name="debug-a-container-in-kubernetes"></a>Debugowanie kontenera w środowisku Kubernetes
Po pomyślnym utworzeniu środowiska deweloperskiego możesz debugować aplikację. Określ punkt przerwania w kodzie, na przykład w wierszu 20 w pliku `HomeController.cs`, w którym jest ustawiona zmienna `Message`. Naciśnij klawisz **F5**, aby rozpocząć debugowanie. 

Program Visual Studio będzie komunikować się ze środowiskiem deweloperskim w celu skompilowania i wdrożenia aplikacji, a następnie otworzy przeglądarkę z uruchomioną aplikacją internetową. Może się wydawać, że kontener działa lokalnie, ale faktycznie jest on uruchamiany w środowisku deweloperskim na platformie Azure. Przyczyną utworzenia adresu hosta lokalnego jest utworzenie przez usługę Azure Dev Spaces tymczasowego tunelu SSH w kontenerze uruchomionym na platformie Azure.

Kliknij link **Informacje** w górnej części strony, aby wyzwolić punkt przerwania. Masz pełny dostęp do informacji debugowania, takich jak stos wywołań, zmienne lokalne, informacje o wyjątkach itd., zupełnie jakby kod był wykonywany lokalnie.

## <a name="call-another-container"></a>Wywoływanie innego kontenera
W tej sekcji utworzysz drugą usługę, `mywebapi`, a usługa `webfrontend` ją wywoła. Każda usługa będzie działać w osobnych kontenerach. Następnie przeprowadzisz debugowanie w obu kontenerach.

![](media/common/multi-container.png)

## <a name="download-sample-code-for-mywebapi"></a>Pobieranie przykładowego kodu aplikacji *mywebapi*
Aby nie tracić czasu, pobierzmy przykładowy kod z repozytorium GitHub. Przejdź do witryny https://github.com/Azure/dev-spaces i wybierz pozycję **Clone or Download** (Sklonuj lub pobierz), aby pobrać repozytorium GitHub. Kod dla tej sekcji znajduje się w folderze `samples/dotnetcore/getting-started/mywebapi`.

## <a name="run-mywebapi"></a>Uruchamianie aplikacji *mywebapi*
1. Otwórz projekt `mywebapi` w *osobnym oknie programu Visual Studio*.
1. Wybierz pozycję **Azure Dev Spaces** z listy rozwijanej ustawień uruchamiania, tak jak wcześniej w projekcie `webfrontend`. Zamiast tworzyć w tym momencie nowe środowisko deweloperskie, wybierz to, które zostało już utworzone. Tak samo jak wcześniej zostaw ustawienie domyślne `default` dla opcji Miejsce i kliknij przycisk **OK**. W oknie danych wyjściowych możesz zauważyć, że program Visual Studio zaczyna „rozgrzewkę” nowej usługi w środowisku deweloperskim, aby przyspieszyć procesy zachodzące po rozpoczęciu debugowania.
1. Naciśnij klawisz F5 i zaczekaj na skompilowanie i wdrożenie usługi. Gdy wszystko będzie gotowe, pasek stanu programu Visual Studio zmieni kolor na pomarańczowy
1. Zanotuj adres URL punktu końcowego wyświetlany w okienku **Azure Dev Spaces for AKS** w oknie **danych wyjściowych**. Będzie on wyglądał mniej więcej tak: http://localhost:\<portnumber\>. Może się wydawać, że kontener działa lokalnie, ale faktycznie jest on uruchamiany w środowisku deweloperskim na platformie Azure.
2. Gdy aplikacja `mywebapi` będzie gotowa, otwórz przeglądarkę, wpisz adres hosta lokalnego i dołącz `/api/values` do adresu URL, aby wywołać domyślny interfejs API GET kontrolera `ValuesController`. 
3. Jeśli wszystkie kroki zostały wykonane pomyślnie, powinno być możliwe wyświetlenie odpowiedzi z usługi `mywebapi`, która wygląda podobnie do poniższej.

    ![](media/get-started-netcore-visualstudio/WebAPIResponse.png)

## <a name="make-a-request-from-webfrontend-to-mywebapi"></a>Tworzenie żądania z aplikacji *webfrontend* do aplikacji *mywebapi*
Napiszmy w aplikacji `webfrontend` kod, który będzie wysyłał żądanie do aplikacji `mywebapi`. Przełącz się na okno programu Visual Studio zawierające projekt `webfrontend`. W pliku `HomeController.cs` *zastąp* kod metody Informacje następującym kodem:

   ```csharp
   public async Task<IActionResult> About()
   {
      ViewData["Message"] = "Hello from webfrontend";

      // Use HeaderPropagatingHttpClient instead of HttpClient so we can propagate
      // headers in the incoming request to any outgoing requests
      using (var client = new HeaderPropagatingHttpClient(this.Request))
      {
          // Call *mywebapi*, and display its response in the page
          var response = await client.GetAsync("http://mywebapi/api/values/1");
          ViewData["Message"] += " and " + await response.Content.ReadAsStringAsync();
      }

      return View();
   }
   ```

Zwróć uwagę, że do odwoływania się do usługi `http://mywebapi` jest używana funkcja odnajdywania usług DNS w środowisku Kubernetes. **Kod w środowisku deweloperskim działa tak samo, jakby był uruchamiany w środowisku produkcyjnym**.

W powyższym przykładzie kodu jest również używana klasa `HeaderPropagatingHttpClient`. Ta klasa pomocy to plik `HeaderPropagation.cs`, który został dodany do projektu podczas konfigurowania go na potrzeby korzystania z usługi Azure Dev Spaces. Klasa `HeaderPropagatingHttpClient` pochodzi od dobrze znanej klasy `HttpClient` i dodaje funkcję propagowania określonych nagłówków z istniejącego obiektu HttpRequest platformy ASP.NET na obiekt wychodzący HttpRequestMessage. Później zobaczysz, jak wpływa to na większą efektywność środowiska programistycznego w scenariuszach zespołu.

## <a name="debug-across-multiple-services"></a>Debugowanie w wielu usługach
1. W tym momencie aplikacja `mywebapi` powinna być nadal uruchomiona z dołączonym debugerem. Jeśli nie jest, naciśnij klawisz F5 w projekcie `mywebapi`.
1. Ustaw punkt przerwania w metodzie `Get(int id)` w pliku `ValuesController.cs`, który obsługuje żądania GET `api/values/{id}`.
1. W projekcie `webfrontend`, w którym został wklejony powyższy kod, ustaw punkt przerwania tuż przed wysłaniem żądania GET do aplikacji `mywebapi/api/values`.
1. W projekcie `webfrontend` naciśnij klawisz F5. Program Visual Studio ponownie otworzy przeglądarkę na odpowiednim porcie hosta lokalnego i zostanie wyświetlona aplikacja internetowa.
1. Kliknij link „**Informacje**” w górnej części strony, aby wyzwolić punkt przerwania w projekcie `webfrontend`. 
1. Naciśnij klawisz F10, aby kontynuować. Punkt przerwania w projekcie `mywebapi` zostanie teraz wyzwolony.
1. Naciśnij klawisz F5, aby kontynuować. Wrócisz do kodu w projekcie `webfrontend`.
1. Naciśnięcie klawisza F5 jeszcze raz spowoduje wykonanie żądania i powrót na stronę w przeglądarce. W aplikacji internetowej na stronie Informacje zostanie wyświetlony połączony komunikat z dwóch usług: „Hello from webfrontend and Hello from mywebapi”.

Gotowe! Teraz masz aplikację z wieloma kontenerami, z których każdy może być tworzony i wdrażany oddzielnie.

## <a name="learn-about-team-development"></a>Więcej informacji na temat programowania zespołowego

Do tej pory kod aplikacji był uruchamiany tak, jakby był tylko jeden deweloper pracujący na aplikacji. W tej sekcji dowiesz się, jak usługa Azure Dev Spaces upraszcza programowanie zespołowe:
* Pozwól zespołowi deweloperów na pracę w tym samym środowisku deweloperskim.
* Środowisko umożliwia każdemu deweloperowi iterację na jego kodzie w izolacji i bez obaw o przeszkadzanie pozostałym.
* Przed zatwierdzeniem kodu można go kompleksowo przetestować bez konieczności tworzenia makiet czy symulowania zależności.

### <a name="challenges-with-developing-microservices"></a>Problemy z tworzeniem mikrousług
W tej chwili przykładowa aplikacja nie jest zbyt skomplikowana. Jednak w rzeczywistym środowisku deweloperskim w miarę dodawania kolejnych usług i rozwoju zespołu programistycznego szybko pojawiają się problemy.

Wyobraź sobie, że pracujesz nad usługą, która współpracuje z dziesiątkami innych usług.

- Uruchomienie ich wszystkich lokalnie na potrzeby programowania może okazać się nierealne. Twoja maszyna deweloperska może nie mieć wystarczającej ilości zasobów, aby uruchomić całą aplikację. A może aplikacja ma punkty końcowe, które muszą być publicznie osiągalne (na przykład aplikacja reaguje na element webhook z aplikacji SaaS).
- Możesz podjąć próbę uruchomienia tylko usług, od których zależysz, ale oznacza to, że musisz znać pełny zestaw zależności (na przykład zależności zależności). Być może jest to po prostu kwestia niewiedzy, jak kompilować i uruchamiać zależności, ponieważ jeszcze nie były one przez Ciebie używane.
- Niektórzy deweloperzy uciekają się do symulowania lub budowania makiet wielu zależności swoich usług. Czasami może to pomóc, ale zarządzanie tymi makietami może szybko zabrać czas potrzebny na programowanie. Ponadto środowisko deweloperskie będzie wówczas wyglądało inaczej niż produkcyjne, co może doprowadzić do pojawienia się drobnych błędów.
- Z tego względu dowolny kompleksowy test staje się trudny. Testowanie integracji realnie może nastąpić po zatwierdzeniu, co oznacza, że problemy pojawią się na dalszych etapach cyklu programowania.

    ![](media/common/microservices-challenges.png)

### <a name="work-in-a-shared-development-environment"></a>Praca w udostępnionym środowisku deweloperskim
W usłudze Azure Dev Spaces możesz skonfigurować *udostępnione* środowisko deweloperskie na platformie Azure. Każdy deweloper może skupić się wyłącznie na swojej części aplikacji oraz iteracyjnie tworzyć *wstępnie zatwierdzony kod* w środowisku zawierającym już wszystkie pozostałe usługi i zasoby w chmurze, od których zależą jego scenariusze. Zależności są zawsze aktualne, a deweloperzy pracują w sposób odzwierciedlający środowisko produkcyjne.

### <a name="work-in-your-own-space"></a>Praca we własnym obszarze
Kiedy tworzysz kod dla swojej usługi i nie jest on jeszcze gotowy do zaewidencjonowania, często znajduje się on w niezbyt dobrym stanie. Wciąż w sposób iteracyjny go kształtujesz, testujesz i eksperymentujesz z rozwiązaniami. Usługa Azure Dev Spaces wprowadza pojęcie **miejsca**, które umożliwia pracę w izolacji i bez obaw o przeszkadzanie członkom zespołu.

Wykonaj poniższe czynności, aby upewnić się, że obie usługi — `webfrontend` i `mywebapi` — są uruchomione w środowisku deweloperskim **i w miejscu `default`**.
1. Zamknij wszystkie sesje F5/debugowania dla obu usług, ale nie zamykaj projektów w oknach programu Visual Studio.
2. Przełącz się na okno programu Visual Studio z projektem `mywebapi`, a następnie naciśnij klawisze Ctrl+F5, aby uruchomić usługę bez dołączonego debugera.
3. Przełącz się na okno programu Visual Studio z projektem `webfrontend`, a następnie naciśnij klawisze Ctrl+F5, aby uruchomić go również.

> [!Note]
> Czasami po pierwszym wyświetleniu strony internetowej w wyniku naciśnięcia klawiszy Ctrl+F5 trzeba odświeżyć przeglądarkę.

Każda osoba otwierająca publiczny adres URL i przechodząca do aplikacji internetowej wywoła napisaną przez Ciebie ścieżkę kodu, która wykona obie usługi, używając domyślnego miejsca `default`. Teraz załóżmy, że chcesz kontynuować tworzenie aplikacji `mywebapi`. Jak możesz to zrobić, nie przerywając pracy innych deweloperów, którzy korzystają ze środowiska deweloperskiego? W tym celu skonfigurujesz własne miejsce.

### <a name="create-a-new-space"></a>Tworzenie nowego miejsca
W programie Visual Studio możesz utworzyć dodatkowe miejsca, które będą używane po naciśnięciu klawiszy F5 lub Ctrl+F5 w usłudze. Miejscu możesz nadać dowolnie wybraną nazwę, która może mieć dowolne znaczenie (np. `sprint4` lub `demo`).

Wykonaj następujące czynności, aby utworzyć nowe miejsce:
1. Przełącz się na okno programu Visual Studio z projektem `mywebapi`.
2. Kliknij prawym przyciskiem myszy projekt w **Eksploratorze rozwiązań** i wybierz pozycję **Właściwości**.
3. Wybierz kartę **Debugowanie** po lewej stronie, aby wyświetlić ustawienia usługi Azure Dev Spaces.
4. W tym miejscu możesz zmienić lub utworzyć klaster i/lub miejsce, które będzie używane po naciśnięciu klawiszy F5 lub Ctrl+F5. *Upewnij się, że jest wybrane utworzone wcześniej miejsce usługi Azure Dev Spaces*.
5. Z listy rozwijanej Miejsce wybierz pozycję **<Utwórz nowe miejsce>**.

    ![](media/get-started-netcore-visualstudio/Settings.png)

6. W oknie dialogowym **Dodawanie miejsca** wpisz nazwę miejsca i kliknij przycisk **OK**. Jako nazwy nowego miejsca możesz użyć swojego imienia (na przykład „scott”), aby Twoi współpracownicy mogli łatwo zidentyfikować miejsce, w którym pracujesz.

    ![](media/get-started-netcore-visualstudio/AddSpace.png)

7. Teraz powinno zostać wyświetlone środowisko deweloperskie i nowe miejsce wybrane na stronie właściwości projektu.

    ![](media/get-started-netcore-visualstudio/Settings2.png)

### <a name="update-code-for-mywebapi"></a>Zaktualizuj kod dla aplikacji *mywebapi*

1. W projekcie `mywebapi` wprowadź zmianę kodu metody `string Get(int id)` w pliku `ValuesController.cs` w następujący sposób:
 
    ```csharp
    [HttpGet("{id}")]
    public string Get(int id)
    {
        return "mywebapi now says something new";
    }
    ```

2. Ustaw punkt przerwania w tym zaktualizowanym bloku kodu (być może masz już ustawiony wcześniej).
3. Naciśnij klawisz F5, aby uruchomić usługę `mywebapi`. Spowoduje to uruchomienie usługi w środowisku deweloperskim przy użyciu wybranego miejsca, czyli w tym przypadku `scott`.

Poniżej przedstawiono diagram, który pomoże Ci zrozumieć, jak działają różne miejsca. Niebieska ścieżka przedstawia żądanie za pośrednictwem miejsca `default`, które jest domyślną ścieżką używaną, gdy do adresu URL nie jest dołączone żadne miejsce. Zielona ścieżka przedstawia żądanie za pośrednictwem miejsca `scott`.

![](media/common/Space-Routing.png)

Ta wbudowana funkcja usługi Azure Dev Spaces umożliwia kompleksowe testowanie kodu w udostępnionym środowisku bez konieczności ponownego tworzenia pełnych stosów usług przez wszystkich deweloperów w ich miejscach. Taki routing wymaga przekazywania nagłówków propagowania w kodzie aplikacji, jak pokazano w poprzednim kroku tego przewodnika.

### <a name="test-code-running-in-the-scott-space"></a>Testowanie kodu uruchamianego w miejscu `scott`
Aby przetestować nową wersję aplikacji `mywebapi` w połączeniu z aplikacją `webfrontend`, otwórz w przeglądarce adres URL publicznego punktu dostępu dla aplikacji `webfrontend` (na przykład http://webfrontend-teamenv.123456abcdef.eastus.aksapp.io) i przejdź do strony Informacje. Powinien zostać wyświetlony pierwotny komunikat „Hello from webfrontend and Hello from mywebapi”.

Teraz dodaj część „scott.s.” do adresu URL, aby wyglądał on mniej więcej tak: http://scott.s.webfrontend-teamenv.123456abcdef.eastus.aksapp.io i odśwież przeglądarkę. Powinien zostać osiągnięty punkt przerwania ustawiony w Twoim projekcie `mywebapi`. Naciśnij klawisz F5, aby kontynuować. W przeglądarce powinien zostać wyświetlony nowy komunikat „Hello from webfrontend and mywebapi now says something new”. Jest to spowodowane tym, że ścieżka do zaktualizowanego kodu w aplikacji `mywebapi` jest uruchomiona w miejscu `scott`.

[!INCLUDE[](includes/well-done.md)]

[!INCLUDE[](includes/clean-up.md)]
