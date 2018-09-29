---
title: Programowanie zespołowe w usłudze Azure Dev Spaces z użyciem środowiska .NET Core i programu VS Code | Microsoft Docs
titleSuffix: Azure Dev Spaces
services: azure-dev-spaces
ms.service: azure-dev-spaces
ms.component: azds-kubernetes
author: ghogen
ms.author: ghogen
ms.date: 07/09/2018
ms.topic: tutorial
description: Szybkie tworzenie w środowisku Kubernetes za pomocą kontenerów i mikrousług na platformie Azure
keywords: Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, containers
manager: douge
ms.openlocfilehash: 1448acf9a9e45b23b714a3a314526c5e6bb7752b
ms.sourcegitcommit: b7e5bbbabc21df9fe93b4c18cc825920a0ab6fab
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/27/2018
ms.locfileid: "47404589"
---
# <a name="team-development-with-azure-dev-spaces"></a>Programowanie zespołowe w usłudze Azure Dev Spaces

Z tego samouczka dowiesz się, jak pracować jednocześnie w różnych środowiskach deweloperskich, używając wielu oddzielnych przestrzeni deweloperskich w ramach jednego klastra do przechowywania poszczególnych projektów.

## <a name="call-a-service-running-in-a-separate-container"></a>Wywoływanie usługi uruchomionej w oddzielnym kontenerze

W tej sekcji utworzysz drugą usługę, `mywebapi`, a usługa `webfrontend` ją wywoła. Każda usługa będzie działać w osobnych kontenerach. Następnie przeprowadzisz debugowanie w obu kontenerach.

![Wiele kontenerów](media/common/multi-container.png)

### <a name="download-sample-code-for-mywebapi"></a>Pobieranie przykładowego kodu aplikacji *mywebapi*
Aby nie tracić czasu, pobierzmy przykładowy kod z repozytorium GitHub. Przejdź do witryny https://github.com/Azure/dev-spaces i wybierz pozycję **Clone or Download** (Sklonuj lub pobierz), aby pobrać repozytorium GitHub. Kod dla tej sekcji znajduje się w folderze `samples/dotnetcore/getting-started/mywebapi`.

### <a name="run-mywebapi"></a>Uruchamianie aplikacji *mywebapi*
1. Otwórz aplikację `mywebapi` w *osobnym oknie programu VS Code*.
1. Otwórz okno **Paleta poleceń** (za pomocą menu **Widok | Paleta poleceń**) i przy użyciu autouzupełniania wpisz i wybierz to polecenie: `Azure Dev Spaces: Prepare configuration files for Azure Dev Spaces`. Nie należy mylić tego polecenia z poleceniem `azds prep`, które umożliwia skonfigurowanie projektu na potrzeby wdrożenia.
1. Naciśnij klawisz F5 i zaczekaj na skompilowanie i wdrożenie usługi. Gdy wszystko będzie gotowe, zostanie wyświetlony pasek debugowania programu VS Code.
1. Adres URL punktu końcowego będą wyglądał mniej więcej tak: http://localhost:\<portnumber\>. **Porada: na pasku stanu programu VS Code będzie wyświetlany adres URL, który można kliknąć.** Może się wydawać, że kontener działa lokalnie, ale faktycznie jest on uruchamiany w naszym obszarze deweloperskim na platformie Azure. Adres hosta lokalnego jest tworzony, ponieważ w aplikacji `mywebapi` nie zdefiniowano żadnych publicznych punktów końcowych i dostęp do niej można uzyskać wyłącznie z poziomu wystąpienia w środowisku Kubernetes. Dla Twojej wygody i ułatwienia interakcji z usługą prywatną z komputera lokalnego usługa Azure Dev Spaces tworzy tymczasowy tunel SSH do kontenera uruchomionego na platformie Azure.
1. Gdy aplikacja `mywebapi` jest gotowa, otwórz w przeglądarce adres hosta lokalnego. Dołącz do adresu URL część `/api/values`, aby wywołać domyślny interfejs API GET dla kontrolera `ValuesController`. 
1. Jeśli wszystkie kroki zostały wykonane pomyślnie, powinno być możliwe wyświetlenie odpowiedzi z usługi `mywebapi`.

### <a name="make-a-request-from-webfrontend-to-mywebapi"></a>Tworzenie żądania z aplikacji *webfrontend* do aplikacji *mywebapi*
Napiszmy w aplikacji `webfrontend` kod, który będzie wysyłał żądanie do aplikacji `mywebapi`.
1. Przełącz się na okno programu VS Code, aby uzyskać dostęp do aplikacji `webfrontend`.
1. *Zastąp* kod metody About:

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

W poprzednim przykładzie kodu nagłówek `azds-route-as` jest przekazywany z żądania przychodzącego do żądania wychodzącego. Później zobaczysz, jak ułatwia to zespołom programowanie zespołowe.

### <a name="debug-across-multiple-services"></a>Debugowanie w wielu usługach
1. W tym momencie aplikacja `mywebapi` powinna być nadal uruchomiona z dołączonym debugerem. Jeśli nie jest, naciśnij klawisz F5 w projekcie `mywebapi`.
1. Ustaw punkt przerwania w metodzie `Get(int id)` obsługującej żądania GET `api/values/{id}`.
1. W projekcie `webfrontend` ustaw punkt przerwania tuż przed wysłaniem żądania GET do aplikacji `mywebapi/api/values`.
1. W projekcie `webfrontend` naciśnij klawisz F5.
1. Wywołaj aplikację internetową i wykonaj kod w obu usługach.
1. W aplikacji internetowej na stronie Informacje zostanie wyświetlony połączony komunikat z dwóch usług: „Hello from webfrontend and Hello from mywebapi”.


Gotowe! Teraz masz aplikację z wieloma kontenerami, z których każdy może być tworzony i wdrażany oddzielnie.

## <a name="learn-about-team-development"></a>Więcej informacji na temat programowania zespołowego

[!INCLUDE [](../../includes/team-development-1.md)]

Zobaczmy, jak to działa. Przejdź do okna programu VS Code, aby uzyskać dostęp do aplikacji `mywebapi`, i zmodyfikuj kod metody `string Get(int id)`, na przykład:

```csharp
[HttpGet("{id}")]
public string Get(int id)
{
    return "mywebapi now says something new";
}
```


[!INCLUDE [](../../includes/team-development-2.md)]

### <a name="well-done"></a>Gotowe!
Przewodnik Wprowadzenie został ukończony! W tym samouczku omówiono:

> [!div class="checklist"]
> * Konfigurowanie usługi Azure Dev Spaces za pomocą zarządzanego klastra Kubernetes na platformie Azure.
> * Iteracyjne tworzenie kodu w kontenerach.
> * Niezależne tworzenie dwóch oddzielnych usług i wywoływanie innej usługi przy użyciu funkcji odnajdywania usług DNS w środowisku Kubernetes.
> * Efektywne tworzenie i testowanie kodu w środowisku zespołu.

Teraz, gdy znasz już usługę Azure Dev Spaces, [udostępnij swoją przestrzeń deweloperów członkowi zespołu](how-to/share-dev-spaces.md) i pokaż, jak łatwo możecie ze sobą współpracować.

## <a name="clean-up"></a>Czyszczenie
Aby całkowicie usunąć z klastra wystąpienie usługi Azure Dev Spaces, w tym wszystkie przestrzenie deweloperów i usługi działające w ich obrębie, należy użyć polecenia `az aks remove-dev-spaces`. Pamiętaj, że ta akcja jest nieodwracalna. Możesz ponownie dodać obsługę usługi Azure Dev Spaces w klastrze, ale wszystko trzeba będzie zacząć od początku. Stare usługi i przestrzenie nie zostaną przywrócone.

Poniższy przykład wyświetla listę kontrolerów usługi Azure Dev Spaces w Twojej aktywnej subskrypcji, a następnie usuwa kontroler usługi Azure Dev Spaces, który jest skojarzony z klastrem usługi AKS „myaks” w grupie zasobów „myaks-rg”.

```cmd
    azds controller list
    az aks remove-dev-spaces --name myaks --resource-group myaks-rg
```
