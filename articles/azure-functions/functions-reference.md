---
title: Wskazówki dotyczące tworzenia usługi Azure Functions | Dokumentacja firmy Microsoft
description: Poznaj pojęcia dotyczące usługi Azure Functions i techniki, które trzeba opracować funkcji na platformie Azure, we wszystkich językach programowania i powiązania.
services: functions
documentationcenter: na
author: tdykstra
manager: cfowler
editor: ''
tags: ''
keywords: Funkcje przewodnik azure Developer, funkcje, przetwarzania elementów webhook, dynamiczne obliczeń, architektura niekorzystającą zdarzeń
ms.assetid: d8efe41a-bef8-4167-ba97-f3e016fcd39e
ms.service: functions
ms.devlang: multiple
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 10/12/2017
ms.author: tdykstra
ms.openlocfilehash: 461557b415ec816860acb5308e7aeba34468f4ae
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/09/2018
ms.locfileid: "29121750"
---
# <a name="azure-functions-developers-guide"></a>Przewodnik dla deweloperów funkcji platformy Azure
W środowisku Azure Functions określonych funkcji udostępniania kilka podstawowe koncepcje techniczne i składników, niezależnie od języka i powiązanie, którego używasz. Przed możesz przejść do uczenia szczegóły specyficzne dla danego języka lub powiązanie, należy koniecznie zapoznaj się z artykułem ten przegląd, która ma zastosowanie do wszystkich z nich.

W tym artykule przyjęto założenie, że został już przeczytany [Azure Functions — omówienie](functions-overview.md) i zapoznaniu się z [zestaw SDK zadań Webjob pojęcia, takie jak wyzwalaczy, powiązania i środowiska uruchomieniowego JobHost](https://github.com/Azure/azure-webjobs-sdk/wiki). Środowisko Azure Functions opiera się na zestaw SDK zadań Webjob. 

## <a name="function-code"></a>Kod — funkcja
A *funkcja* to podstawowe pojęcie w usługi Azure Functions. Pisanie kodu dla funkcji w języku wybranym i zapisać pliki kodu i konfiguracji w tym samym folderze. Nosi nazwę konfiguracji `function.json`, który zawiera dane konfiguracyjne w formacie JSON. Są obsługiwane w różnych językach, a każda z nich ma nieco inne środowisko, zoptymalizowana pod kątem pracy najlepiej dla tego języka. 

Plik function.json definiuje, powiązania funkcji i innych ustawień konfiguracyjnych. Środowisko uruchomieniowe korzysta z tego pliku, aby określić zdarzenia do monitorowania oraz sposób przekazywania danych do i zwracać dane z wykonywania funkcji. Poniżej przedstawiono przykładowy plik function.json.

```json
{
    "disabled":false,
    "bindings":[
        // ... bindings here
        {
            "type": "bindingType",
            "direction": "in",
            "name": "myParamName",
            // ... more depending on binding
        }
    ]
}
```

Ustaw `disabled` właściwości `true` aby zapobiec wykonywana przez funkcję.

`bindings` Właściwość jest, którym możesz skonfigurować zarówno wyzwalaczy i powiązań. Każdego powiązania udostępnia kilka typowych ustawień i niektórych ustawień, które są specyficzne dla określonego typu powiązania. Każde powiązanie wymaga następujących ustawień:

| Właściwość | Wartości/typów | Komentarze |
| --- | --- | --- |
| `type` |ciąg |Typ wiązania. Na przykład `queueTrigger`. |
| `direction` |"in" "out" |Wskazuje, czy powiązanie jest odbieranie danych do funkcji lub wysyłanie danych z funkcji. |
| `name` |ciąg |Nazwa, która jest używana do powiązania danych w funkcji. Język C# jest to nazwa argumentu; dla języka JavaScript jest to klucz na liście klucza i wartości. |

## <a name="function-app"></a>Aplikacja funkcji
Aplikacja funkcji składa się z co najmniej jeden poszczególne funkcje, które są ze sobą zarządzane przez usługę Azure App Service. Wszystkie funkcje w aplikacji funkcji Udostępnianie tego samego planu cenowego, ciągłe wdrażanie i wersji środowiska wykonawczego. Funkcje zapisywane w wielu językach można udostępniać tej samej funkcji aplikacji. Aplikacja funkcji można traktować jako sposób organizowania i zbiorczo zarządzania funkcjami. 

## <a name="runtime-script-host-and-web-host"></a>Środowisko uruchomieniowe (host skryptów i hosta sieci web)
Środowisko uruchomieniowe lub host skryptów jest odpowiedniego hosta zestaw SDK zadań Webjob, która nasłuchuje zdarzeń, zbiera i wysyła dane, a ostatecznie uruchamia kod. 

W celu ułatwienia wyzwalaczy HTTP, jest również hostem sieci web, zaprojektowana w celu sit przed host skryptów w scenariuszach produkcji. Mając dwa hosty pozwala izolować host skryptów z przodu końcowy ruchu zarządzane przez hosta sieci web.

## <a name="folder-structure"></a>Struktura folderów
[!INCLUDE [functions-folder-structure](../../includes/functions-folder-structure.md)]

Podczas tworzenia projektu wdrażania funkcji w aplikacji funkcji w usłudze Azure App Service, możesz traktować tę strukturę folderów jako kodu lokacji. Możesz użyć istniejących narzędzi, takich jak ciągłej integracji i wdrażania lub skrypty niestandardowe wdrożenie może wdrożyć czasu pakietu instalacji lub kod transpilation.

> [!NOTE]
> Upewnij się, że do wdrożenia programu `host.json` plików i bezpośrednio do funkcji Foldery `wwwroot` folderu. Nie dołączaj `wwwroot` folderu wdrożeń. W przeciwnym razie na końcu `wwwroot\wwwroot` folderów. 
> 
> 

## <a id="fileupdate"></a>Jak zaktualizować pliki aplikacji, funkcja
Edytor funkcja wbudowana w portalu Azure umożliwia uaktualnienie *function.json* plików i pliku kodu dla funkcji. Przekazywanie lub inne pliki aktualizacji, takich jak *package.json* lub *project.json* lub zależności, należy użyć innych metod wdrażania.

Funkcja aplikacje są tworzone w usłudze aplikacji, więc wszystkie [dostępne opcje wdrażania aplikacji sieci web standard](../app-service/app-service-deploy-local-git.md) są także dostępne dla funkcji aplikacji. Poniżej przedstawiono niektóre metody używanej do przekazania lub zaktualizować pliki aplikacji funkcji. 

#### <a name="to-use-app-service-editor"></a>Aby użyć edytora usługi aplikacji
1. W portalu usługi Azure Functions kliknij **funkcji platformy**.
2. W **narzędzi PROGRAMISTYCZNYCH** kliknij **Edytor usług aplikacji**.   
   Po załadowaniu Edytor usług aplikacji, zobaczysz *host.json* plików i funkcja foldery znajdujące się w *wwwroot*. 
5. Otwieranie plików do ich edytowania lub przeciągnij i upuść z komputerze deweloperskim przekazywania plików.

#### <a name="to-use-the-function-apps-scm-kudu-endpoint"></a>Aby użyć aplikacji funkcja endpoint SCM (Kudu)
1. Przejdź do: `https://<function_app_name>.scm.azurewebsites.net`.
2. Kliknij przycisk **Konsola debugowania > CMD**.
3. Przejdź do `D:\home\site\wwwroot\` zaktualizować *host.json* lub `D:\home\site\wwwroot\<function_name>` do aktualizacji plików funkcji.
4. Przeciągnij i upuść plik, który chcesz przekazać do odpowiedniego folderu w siatce pliku. Istnieją dwa obszary siatki pliku, gdzie można usunąć pliku. Aby uzyskać *.zip* pliki, zostanie wyświetlone okno z etykietą "Przeciągnij tutaj aby przekazać i Rozpakuj." W przypadku innych typów plików porzucić w siatce pliku, ale poza pole "Rozpakuj".

<!--NOTE: I've removed documentation on FTP, because it does not sync triggers on the consumption plan --glenga -->

#### <a name="to-use-continuous-deployment"></a>Aby użyć ciągłe wdrażanie
Postępuj zgodnie z instrukcjami w temacie [ciągłego wdrażania usługi Azure Functions](functions-continuous-deployment.md).

## <a name="parallel-execution"></a>Wykonywanie równoległe
Gdy wiele wyzwalająca zdarzenia występują szybciej, niż może je przetwarzać runtime jednowątkowe funkcji, środowisko uruchomieniowe może wywołanie funkcji wielokrotnie równolegle.  Jeśli aplikacja funkcja używa [zużycie plan hostingu](functions-scale.md#how-the-consumption-plan-works), funkcja aplikacji może automatycznie skalować w poziomie.  Każdego wystąpienia aplikacji funkcji określa, czy aplikacja jest uruchamiana na zużycie hosting planu lub zwykły [usługi aplikacji — plan hostingu](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md), może przetwarzać funkcja współbieżnych wywołań z użyciem wielu wątków.  Maksymalna liczba funkcji współbieżnych wywołań w każdym wystąpieniu aplikacji funkcji zależy od typu wyzwalacza używany, a także zasoby używane przez inne funkcje w aplikacji funkcji.

## <a name="functions-runtime-versioning"></a>Funkcje wersji środowiska uruchomieniowego

Można skonfigurować przy użyciu środowiska uruchomieniowego funkcje wersji `FUNCTIONS_EXTENSION_VERSION` ustawienia aplikacji. Na przykład wartość "~ 1" wskazuje, czy Twoja aplikacja funkcja będzie używać 1 jako jego wersji głównej. Funkcja aplikacji są uaktualniane do każdej nowej wersji pomocniczej, po ich wydaniu. Aby uzyskać więcej informacji, łącznie ze sposobem wyświetlenia dokładnej wersji aplikacji funkcji, zobacz [jak korzystać z wersji środowiska uruchomieniowego usługi Azure Functions](set-runtime-version.md).

## <a name="repositories"></a>Repozytoria
Kod dla usługi Azure Functions to typu open source i przechowywane w repozytoriów GitHub:

* [Środowisko uruchomieniowe Functions Azure](https://github.com/Azure/azure-webjobs-sdk-script/)
* [Portalu Azure Functions](https://github.com/projectkudu/AzureFunctionsPortal)
* [Szablony funkcji platformy Azure](https://github.com/Azure/azure-webjobs-sdk-templates/)
* [Zestaw SDK zadań Webjob Azure](https://github.com/Azure/azure-webjobs-sdk/)
* [Zestaw SDK zadań Webjob Azure rozszerzeń](https://github.com/Azure/azure-webjobs-sdk-extensions/)

## <a name="bindings"></a>Powiązania
W tym miejscu znajduje się tabela wszystkie obsługiwane powiązania.

[!INCLUDE [dynamic compute](../../includes/functions-bindings.md)]

Masz problemy z błędami pochodzące z powiązania? Przegląd [kody błędów powiązanie funkcji Azure](functions-bindings-error-pages.md) dokumentacji.

## <a name="reporting-issues"></a>Zgłaszanie problemów
[!INCLUDE [Reporting Issues](../../includes/functions-reporting-issues.md)]

## <a name="next-steps"></a>Kolejne kroki
Więcej informacji zawierają następujące zasoby:

* [Najlepsze rozwiązania dotyczące usługi Azure Functions](functions-best-practices.md)
* [Azure dokumentacja dla deweloperów funkcje C#](functions-reference-csharp.md)
* [Azure dokumentacja dla deweloperów funkcje F #](functions-reference-fsharp.md)
* [Dokumentacja dla deweloperów NodeJS funkcji platformy Azure](functions-reference-node.md)
* [Azure funkcje wyzwalaczy i powiązań](functions-triggers-bindings.md)
* [Środowisko Azure Functions: Podróży](https://blogs.msdn.microsoft.com/appserviceteam/2016/04/27/azure-functions-the-journey/) na blogu zespołu usługi Azure App Service. Historia jak został opracowany usługi Azure Functions.

