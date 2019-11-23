---
title: 'Running multiple dependent services: .NET Core & Visual Studio'
services: azure-dev-spaces
ms.custom: vs-azure
ms.workload: azure-vs
ms.date: 07/09/2018
ms.topic: tutorial
description: Szybkie tworzenie w środowisku Kubernetes za pomocą kontenerów i mikrousług na platformie Azure
keywords: Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, containers, Helm, service mesh, service mesh routing, kubectl, k8s
ms.openlocfilehash: 9fb6993c913454d67e2118cd3ff5a4b8fea6464b
ms.sourcegitcommit: b77e97709663c0c9f84d95c1f0578fcfcb3b2a6c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/22/2019
ms.locfileid: "74325702"
---
# <a name="running-multiple-dependent-services-net-core-and-visual-studio-with-azure-dev-spaces"></a>Running multiple dependent services: .NET Core and Visual Studio with Azure Dev Spaces

Z tego samouczka dowiesz się, jak programować aplikacje wielousługowe za pomocą usługi Azure Dev Spaces oraz poznasz niektóre dodatkowe korzyści zapewniane przez usługę Dev Spaces.

## <a name="call-another-container"></a>Wywoływanie innego kontenera
W tej sekcji utworzysz drugą usługę, `mywebapi`, a usługa `webfrontend` ją wywoła. Każda usługa będzie działać w osobnych kontenerach. Następnie przeprowadzisz debugowanie w obu kontenerach.

![](media/common/multi-container.png)

### <a name="download-sample-code-for-mywebapi"></a>Pobieranie przykładowego kodu aplikacji *mywebapi*
Aby nie tracić czasu, pobierzmy przykładowy kod z repozytorium GitHub. Przejdź do witryny https://github.com/Azure/dev-spaces i wybierz pozycję **Clone or Download** (Sklonuj lub pobierz), aby pobrać repozytorium GitHub. Kod dla tej sekcji znajduje się w folderze `samples/dotnetcore/getting-started/mywebapi`.

### <a name="run-mywebapi"></a>Uruchamianie aplikacji *mywebapi*
1. Otwórz projekt `mywebapi` w *osobnym oknie programu Visual Studio*.
1. Wybierz pozycję **Azure Dev Spaces** z listy rozwijanej ustawień uruchamiania, tak jak wcześniej w projekcie `webfrontend`. Zamiast tworzyć w tym momencie nowy klaster usługi AKS, wybierz ten, który został już utworzony. Tak samo jak wcześniej zostaw ustawienie domyślne `default` dla opcji Miejsce i kliknij przycisk **OK**. In the Output window, you may notice Visual Studio starts to "warm up" this new service in your dev space in order to speed up things when you start debugging.
1. Naciśnij klawisz F5 i zaczekaj na skompilowanie i wdrożenie usługi. Gdy wszystko będzie gotowe, pasek stanu programu Visual Studio zmieni kolor na pomarańczowy
1. Zanotuj adres URL punktu końcowego wyświetlany w okienku **Azure Dev Spaces for AKS** w oknie **danych wyjściowych**. Będzie on wyglądał mniej więcej tak: `http://localhost:<portnumber>`. Może się wydawać, że kontener działa lokalnie, ale faktycznie jest on uruchamiany w obszarze deweloperskim na platformie Azure.
2. Gdy aplikacja `mywebapi` będzie gotowa, otwórz przeglądarkę, wpisz adres hosta lokalnego i dołącz `/api/values` do adresu URL, aby wywołać domyślny interfejs API GET kontrolera `ValuesController`. 
3. Jeśli wszystkie kroki zostały wykonane pomyślnie, powinno być możliwe wyświetlenie odpowiedzi z usługi `mywebapi`, która wygląda podobnie do poniższej.

    ![](media/get-started-netcore-visualstudio/WebAPIResponse.png)

### <a name="make-a-request-from-webfrontend-to-mywebapi"></a>Tworzenie żądania z aplikacji *webfrontend* do aplikacji *mywebapi*
Napiszmy w aplikacji `webfrontend` kod, który będzie wysyłał żądanie do aplikacji `mywebapi`. Przełącz się na okno programu Visual Studio zawierające projekt `webfrontend`. W pliku `HomeController.cs` *zastąp* kod metody About następującym kodem:

   ```csharp
   public async Task<IActionResult> About()
   {
      ViewData["Message"] = "Hello from webfrontend";

      using (var client = new System.Net.Http.HttpClient())
            {
                // Call *mywebapi*, and display its response in the page
                var request = new System.Net.Http.HttpRequestMessage();
                request.RequestUri = new Uri("http://mywebapi/api/values/1");
                if (this.Request.Headers.ContainsKey("azds-route-as"))
                {
                    // Propagate the dev space routing header
                    request.Headers.Add("azds-route-as", this.Request.Headers["azds-route-as"] as IEnumerable<string>);
                }
                var response = await client.SendAsync(request);
                ViewData["Message"] += " and " + await response.Content.ReadAsStringAsync();
            }

      return View();
   }
   ```

W poprzednim przykładzie kodu nagłówek `azds-route-as` jest przekazywany z żądania przychodzącego do żądania wychodzącego. Później zobaczysz, jak wpływa to na większą efektywność środowiska programistycznego w [scenariuszach zespołu](team-development-netcore-visualstudio.md).

### <a name="debug-across-multiple-services"></a>Debugowanie w wielu usługach
1. W tym momencie aplikacja `mywebapi` powinna być nadal uruchomiona z dołączonym debugerem. Jeśli nie jest, naciśnij klawisz F5 w projekcie `mywebapi`.
1. Ustaw punkt przerwania w metodzie `Get(int id)` w pliku `Controllers/ValuesController.cs`, który obsługuje żądania GET `api/values/{id}`.
1. W projekcie `webfrontend`, w którym został wklejony powyższy kod, ustaw punkt przerwania tuż przed wysłaniem żądania GET do aplikacji `mywebapi/api/values`.
1. W projekcie `webfrontend` naciśnij klawisz F5. Program Visual Studio ponownie otworzy przeglądarkę na odpowiednim porcie hosta lokalnego i zostanie wyświetlona aplikacja internetowa.
1. Kliknij link „**Informacje**” w górnej części strony, aby wyzwolić punkt przerwania w projekcie `webfrontend`. 
1. Naciśnij klawisz F10, aby kontynuować. Punkt przerwania w projekcie `mywebapi` zostanie teraz wyzwolony.
1. Naciśnij klawisz F5, aby kontynuować. Wrócisz do kodu w projekcie `webfrontend`.
1. Naciśnięcie klawisza F5 jeszcze raz spowoduje wykonanie żądania i powrót na stronę w przeglądarce. W aplikacji internetowej na stronie Informacje zostanie wyświetlony połączony komunikat z dwóch usług: „Hello from webfrontend and Hello from mywebapi”.

### <a name="well-done"></a>Gotowe!
Teraz masz aplikację z wieloma kontenerami, z których każdy może być tworzony i wdrażany oddzielnie.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Dowiedz się więcej na temat programowania zespołowego w usłudze Dev Spaces](team-development-netcore-visualstudio.md)
