---
title: Wskazówki dotyczące projektowania usługi Azure Functions | Dokumentacja firmy Microsoft
description: Poznaj pojęcia dotyczące usługi Azure Functions i technik, które są potrzebne do opracowania funkcji na platformie Azure we wszystkich językach programowania i powiązania.
services: functions
documentationcenter: na
author: ggailey777
manager: jeconnoc
keywords: Funkcje przewodnik platformy azure dla deweloperów, funkcje, przetwarzanie zdarzeń, elementy webhook, obliczanie dynamiczne, architektura bez serwera
ms.assetid: d8efe41a-bef8-4167-ba97-f3e016fcd39e
ms.service: azure-functions
ms.devlang: multiple
ms.topic: reference
ms.date: 10/12/2017
ms.author: glenga
ms.openlocfilehash: d2b05c83f77a58e224760d90d111b270d71a6514
ms.sourcegitcommit: af60bd400e18fd4cf4965f90094e2411a22e1e77
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/07/2018
ms.locfileid: "44092431"
---
# <a name="azure-functions-developers-guide"></a>Przewodnik dla deweloperów usługi Azure Functions
W usłudze Azure Functions określonych funkcji udostępniania kilka podstawowych pojęć technicznych i składniki, niezależnie od języka lub powiązania, którego używasz. Zanim przejdziesz do nauki szczegóły specyficzne dla danego języka lub powiązania, koniecznie zapoznaj się z tego omówienia, która ma zastosowanie do wszystkich z nich.

W tym artykule założono, że zostały już przeczytane [omówienia usługi Azure Functions](functions-overview.md) i znasz [zestawu SDK usługi WebJobs pojęć, takich jak wyzwalacze, powiązania i środowiska uruchomieniowego JobHost](https://github.com/Azure/azure-webjobs-sdk/wiki). Usługa Azure Functions opiera się na zestaw SDK zadań Webjob. 

## <a name="function-code"></a>Kod — funkcja
A *funkcja* to podstawowe pojęcie w usłudze Azure Functions. Pisanie kodu dla funkcji w wybranym języku i Zapisz kod i pliki konfiguracyjne w tym samym folderze. Nosi nazwę konfiguracji `function.json`, który zawiera dane konfiguracyjne w formacie JSON. Różne języki są obsługiwane, a każda ma nieco doświadczenia, zoptymalizowana pod kątem pracy najlepsze dla tego języka. 

Plik function.json definiuje powiązań funkcji i innych ustawień konfiguracyjnych. Środowisko wykonawcze używa tego pliku, aby określić zdarzenia do monitorowania oraz sposób przekazywania danych do i zwrócić dane z wykonywania funkcji. Oto przykładowy plik function.json.

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

Ustaw `disabled` właściwość `true` aby zapobiec wykonywane przez funkcję.

`bindings` Właściwość jest którym skonfigurować zarówno wyzwalaczy i powiązań. Każde powiązanie udostępnia kilka typowych ustawień i niektóre ustawienia, które są specyficzne dla danego typu powiązania. Każde powiązanie wymaga następujących ustawień:

| Właściwość | Wartości/typy | Komentarze |
| --- | --- | --- |
| `type` |ciąg |Typ powiązania. Na przykład `queueTrigger`. |
| `direction` |"in" "out" |Wskazuje, czy powiązanie jest dla odbierania danych do funkcji lub wysyłanie danych przez funkcję. |
| `name` |ciąg |Nazwa która jest używana do powiązanych danych w funkcji. Dla języka C# jest to nazwa argumentu; dla języka JavaScript to klucz na liście kluczy/wartości. |

## <a name="function-app"></a>Aplikacja funkcji
Aplikacja funkcji składa się z co najmniej jeden poszczególnych funkcji, które są ze sobą zarządzane przez usługę Azure App Service. Wszystkie funkcje w aplikacji funkcji Udostępnianie tego samego planu cenowego i ciągłe Wdrażanie wersji środowiska uruchomieniowego. Funkcje napisanych w wielu językach mogą udostępniać ta sama aplikacja funkcji. Aplikacja funkcji można traktować jako sposób organizowania i zbiorczo zarządzania funkcjami. 

## <a name="runtime-script-host-and-web-host"></a>Środowisko uruchomieniowe (host skryptów i hosta sieci web)
Środowisko uruchomieniowe lub host skryptów jest odpowiedniego hosta zestaw SDK zadań Webjob, która nasłuchuje zdarzeń, zbiera i przesyła dane i ostatecznie uruchamia kod. 

W celu ułatwienia wyzwalaczy HTTP, jest również hosta sieci web, który jest przeznaczony do siedzieć przed host skryptów w scenariuszach produkcyjnych. Masz dwa hosty pomaga wyizolować host skryptów z przodu kończy ruch zarządzana przez hosta sieci web.

## <a name="folder-structure"></a>Struktura folderów
[!INCLUDE [functions-folder-structure](../../includes/functions-folder-structure.md)]

Podczas tworzenia projektu wdrażania funkcji do aplikacji funkcji w usłudze Azure App Service, możesz traktować tej struktury folderów, jako kod lokacji. Możesz użyć istniejących narzędzi, np. ciągłej integracji i ciągłego wdrażania lub skrypty wdrażania niestandardowego może wdrożyć instalacji pakietu aktualizacji w czasie lub kodu transpilation.

> [!NOTE]
> Upewnij się wdrożyć swoje `host.json` plików i funkcja folderów bezpośrednio do `wwwroot` folderu. Nie dołączaj `wwwroot` folderu we wdrożeniach. W przeciwnym razie na końcu `wwwroot\wwwroot` folderów. 
> 
> 

## <a id="fileupdate"></a> Jak zaktualizować pliki aplikacji — funkcja
Edytor funkcji wbudowanych w witrynie Azure portal umożliwia uaktualnienie *function.json* plik oraz plik kodu dla funkcji. Przekazywanie lub inne pliki aktualizacji, takich jak *package.json* lub *project.json* lub zależności, trzeba użyć innych metod wdrażania.

Aplikacje funkcji są tworzone w usłudze App Service, więc wszystkie [opcje wdrażania dostępne dla aplikacji sieci web standard](../app-service/app-service-deploy-local-git.md) są również dostępne dla aplikacji funkcji. Poniżej przedstawiono niektóre metody, które służy do przekazywania lub aktualizowanie plików aplikacji funkcji. 

#### <a name="to-use-app-service-editor"></a>Aby użyć Edytor usługi App Service
1. W portalu usługi Azure Functions kliknij **funkcje platformy**.
2. W **narzędzia PROGRAMISTYCZNE** kliknij **Edytor usługi App Service**.   
   Po załadowaniu Edytor usługi App Service, zobaczysz *host.json* plików i funkcja foldery w *wwwroot*. 
5. Otwórz pliki do ich edytowania lub przeciągnij i upuść z komputera deweloperskiego do przekazywania plików.

#### <a name="to-use-the-function-apps-scm-kudu-endpoint"></a>Aby używać punktu końcowego aplikacji funkcji SCM (Kudu)
1. Przejdź do: `https://<function_app_name>.scm.azurewebsites.net`.
2. Kliknij przycisk **Debug Console > CMD**.
3. Przejdź do `D:\home\site\wwwroot\` można zaktualizować *host.json* lub `D:\home\site\wwwroot\<function_name>` do aktualizacji plików funkcji.
4. Przeciąganie i upuszczanie plików, które chcesz przekazać do odpowiedniego folderu w siatce pliku. Istnieją dwa obszary, w siatce pliku, gdy usuniesz plik. Aby uzyskać *zip* pliki, zostanie wyświetlone okno z etykietą "Przeciągnij tutaj, aby przekazać i Rozpakuj go." Dla innych typów plików należy porzucić w siatce pliku, ale poza pole "rozpakowywania".

<!--NOTE: I've removed documentation on FTP, because it does not sync triggers on the consumption plan --glenga -->

#### <a name="to-use-continuous-deployment"></a>Przy użyciu ciągłego wdrażania
Postępuj zgodnie z instrukcjami w temacie [ciągłe wdrażanie dla usługi Azure Functions](functions-continuous-deployment.md).

## <a name="parallel-execution"></a>Wykonywanie równoległe
Gdy wiele wyzwalająca zdarzenia występują szybciej niż jednowątkowe funkcji środowiska uruchomieniowego mogły je przetwarzać, środowisko uruchomieniowe może wywołania funkcji wiele razy w sposób równoległy.  Jeśli aplikacja funkcji używa [planu hostingu zużycie](functions-scale.md#how-the-consumption-plan-works), aplikacja funkcji może automatycznego skalowania w poziomie.  Każde wystąpienie aplikacji funkcji, czy aplikacja jest uruchamiana na zużycie hosting plan lub wyrażenie [usługi App Service plan hostingu](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md), może przetwarzać wywołania funkcji współbieżnych równolegle przy użyciu wielu wątków.  Maksymalna liczba wywołań funkcji współbieżnych w każdym wystąpieniu aplikacji funkcji zależy od typu wyzwalacza są używane, a także zasoby używane przez inne funkcje w ramach aplikacji funkcji.

## <a name="functions-runtime-versioning"></a>Przechowywanie wersji środowiska uruchomieniowego funkcji

Można skonfigurować wersję przy użyciu środowiska uruchomieniowego funkcji `FUNCTIONS_EXTENSION_VERSION` ustawienia aplikacji. Na przykład wartość "~ 1" wskazuje, że aplikacja funkcji będzie używać 1 jako jego wersji głównej. Aplikacje funkcji są uaktualniane do każda nowa wersja pomocnicza, po ich wydaniu. Aby uzyskać więcej informacji, jak wyświetlić dokładną wersję aplikacji funkcji, w tym temacie [sposobu kierowania wersje środowiska uruchomieniowego usługi Azure Functions](set-runtime-version.md).

## <a name="repositories"></a>Repozytoria
Kod dla usługi Azure Functions jest typu open source i przechowywane w repozytoriach usługi GitHub:

* [Środowisko uruchomieniowe usługi Azure Functions](https://github.com/Azure/azure-webjobs-sdk-script/)
* [Portal usługi Azure Functions](https://github.com/projectkudu/AzureFunctionsPortal)
* [Szablony usługi Azure Functions](https://github.com/Azure/azure-webjobs-sdk-templates/)
* [Zestawu Azure WebJobs SDK](https://github.com/Azure/azure-webjobs-sdk/)
* [Rozszerzenia zestawu Azure WebJobs SDK](https://github.com/Azure/azure-webjobs-sdk-extensions/)

## <a name="bindings"></a>Powiązania
W tym miejscu znajduje się tabela wszystkie obsługiwane powiązania.

[!INCLUDE [dynamic compute](../../includes/functions-bindings.md)]

Masz problemy z błędami pochodzące z powiązaniami? Przegląd [kody błędów powiązania funkcji Azure](functions-bindings-error-pages.md) dokumentacji.

## <a name="reporting-issues"></a>Raportowanie problemów
[!INCLUDE [Reporting Issues](../../includes/functions-reporting-issues.md)]

## <a name="next-steps"></a>Kolejne kroki
Więcej informacji zawierają następujące zasoby:

* [Najlepsze rozwiązania dotyczące usługi Azure Functions](functions-best-practices.md)
* [Usługa Azure Functions dla deweloperów odwołanie w C#](functions-reference-csharp.md)
* [Dokumentacja usługi Azure Functions F # dla deweloperów](functions-reference-fsharp.md)
* [Dokumentacja dla deweloperów w usłudze Azure Functions NodeJS](functions-reference-node.md)
* [Wyzwalacze w usłudze Azure Functions i powiązania](functions-triggers-bindings.md)
* [Usługa Azure Functions: Podróży](https://blogs.msdn.microsoft.com/appserviceteam/2016/04/27/azure-functions-the-journey/) na blogu zespołu usługi Azure App Service. Historia jak został opracowany, usługi Azure Functions.

