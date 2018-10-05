---
title: Wersje środowiska uruchomieniowego usługi Azure Functions — omówienie
description: Usługa Azure Functions obsługuje wiele wersji środowiska uruchomieniowego. Dowiedz się, różnice między nimi i sposobu wybierz ten, który jest odpowiedni dla Ciebie.
services: functions
documentationcenter: ''
author: ggailey777
manager: jeconnoc
ms.service: azure-functions
ms.topic: conceptual
ms.date: 10/03/2018
ms.author: glenga
ms.openlocfilehash: 372cf445e518ccdb287ce23ade6a3d92ddc5bc2b
ms.sourcegitcommit: 4edf9354a00bb63082c3b844b979165b64f46286
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/04/2018
ms.locfileid: "48784903"
---
# <a name="azure-functions-runtime-versions-overview"></a>Wersje środowiska uruchomieniowego usługi Azure Functions — omówienie

 Istnieją dwie wersje główne środowiska uruchomieniowego usługi Azure Functions: wersji 1.x i 2.x. Bieżącą wersję, w których wprowadzono nowe pracy nad funkcjami i ulepszenia jest 2.x, jeśli obie są obsługiwane w przypadku scenariuszy produkcyjnych.  Następujące szczegóły, niektóre różnice między nimi, jak można utworzyć każdej wersji i uaktualnić wersję 1.x do 2.x.

> [!NOTE] 
> Ten artykuł odnosi się do usługi w chmurze usługi Azure Functions. Aby uzyskać informacji na temat produktu w wersji zapoznawczej, który pozwala na uruchamianie usługi Azure Functions w środowisku lokalnym, zobacz [Przegląd środowiska uruchomieniowego usługi Azure Functions](functions-runtime-overview.md).

## <a name="creating-1x-apps"></a>Tworzenie aplikacji 1.x

Nowe aplikacje utworzone w witrynie Azure Portal są domyślnie 2.x, jak jest to najbardziej aktualną wersję i gdzie zostaną wprowadzone nowe inwestycje funkcji.  Można jednak nadal tworzyć aplikacje v1.x, wykonując następujące czynności.

1. Tworzenie funkcji platformy Azure w witrynie Azure Portal
1. Otwórz utworzoną aplikację, a gdy jest on pusty **ustawienia funkcji**
1. Zmiana wersji od 2 ~ do ~ 1.  *Ten przełącznik zostanie wyłączona, jeśli w Twojej aplikacji funkcji*.
1. Kliknij pozycję Zapisz i uruchom ponownie aplikację.  Wszystkie szablony powinny teraz tworzyć i uruchamiać w 1.x.

## <a name="cross-platform-development"></a>Programowanie dla wielu platform

Środowisko uruchomieniowe 1.x obsługuje programowanie i hosting tylko w portalu lub Windows. Środowisko uruchomieniowe 2.x uruchamiany .NET Core 2, co oznacza, że można uruchomić na wszystkich platformach obsługiwanych przez platformę .NET Core, w tym w systemach macOS i Linux. Dzięki temu programowanie dla wielu platform i scenariuszach hostingu.

## <a name="languages"></a>Języki

Środowisko uruchomieniowe 2.x używa nowego modelu rozszerzalności języka. Ponadto w celu poprawy wydajności i narzędzia każdej aplikacji w 2.x jest ograniczona do mieć tylko funkcje w jednym języku. Obecnie obsługiwane języki w 2.x są C#, F #, JavaScript i Java. Usługa Azure języków eksperymentalnych 1.x funkcje nie zostały zaktualizowane do użycia nowego modelu, dzięki czemu nie są obsługiwane w 2.x. Poniższa tabela wskazuje, które języki programowania są obsługiwane w każdej wersji środowiska uruchomieniowego.

[!INCLUDE [functions-supported-languages](../../includes/functions-supported-languages.md)]

Aby uzyskać więcej informacji, zobacz [obsługiwane języki](supported-languages.md).

## <a name="migrating-from-1x-to-2x"></a>Migrowanie z 1.x do 2.x

Możesz przenieść istniejącą aplikację w 1.x do 2.x.  Większość zagadnień wymagane podczas przenoszenia między wersjami odnoszą się do zmiany środowiska uruchomieniowego języka, które są wymienione powyżej (na przykład C# przechodzenia z programu .NET Framework 4.7 na .NET Core 2).  Należy upewnić się, że Twój kod i biblioteki są zgodne ze środowisk uruchomieniowych języków, które są używane.  Należy także zauważyć wszelkie zmiany w wyzwalacza, powiązania i funkcje, które przedstawiono poniżej.

### <a name="changes-in-triggers-and-bindings"></a>Zmiany w wyzwalaczy i powiązań

Chociaż większość wyzwalacz i powiązania właściwości i konfiguracje pozostają bez zmian między wersjami, w 2.x będzie musisz zainstalować wszelkie wyzwalaczem lub powiązaniem do aplikacji. Jedynym wyjątkiem od tego jest wyzwalaczy HTTP oraz czasomierzem.  Zobacz [rejestru i instalowanie rozszerzeń powiązania](./functions-triggers-bindings.md#register-binding-extensions).  Należy zauważyć, że można również zmiany w `function.json` lub atrybutów funkcji między wersjami (na przykład CosmosDB `connection` właściwość jest obecnie `ConnectionStringSetting`).  Odwołanie [istniejącej tabeli powiązania](#bindings) linki do dokumentacji dla każdego powiązania.

### <a name="changes-in-features-available"></a>Zmiany w funkcjach dostępnych

Oprócz zmian w języków i powiązań istnieją pewne funkcje, które zostały usunięte, zaktualizowane lub zamienione między wersjami.  Poniżej przedstawiono niektóre najważniejsze kwestie, aby upewnić się, gdy po użyciu 1.x począwszy od 2.x.  W v2.x wprowadzono następujące zmiany:

* Klucze dla wywołanie funkcji zawsze będą przechowywane w magazynie obiektów blob zaszyfrowany. W 1.x domyślnie zostały w usłudze file storage i można jej przenieść do obiektów blob, jeśli włączenie funkcji, takich jak gniazda.  W przypadku uaktualniania aplikacji 1.x do 2.x i wpisów tajnych w usłudze file storage aktualnie one zostaną zresetowane.
* Aby zwiększyć wydajność, wyzwalaczy typu "element webhook" są usunięte i zastąpione przez wyzwalacze "HTTP".
* Konfiguracja hosta (`host.json`), należy być pusty ani zawierać `version` z `2.0` dla jednej właściwości.
* W celu monitorowania i observability, pulpitu nawigacyjnego zadań Webjob (`AzureWebJobsDashboard`) jest zastępowany [usługi Azure Application Insights](functions-monitoring.md) (`APPINSIGHTS_INSTRUMENTATIONKEY`)
* Ustawienia aplikacji (`local.settings.json`) wymaga wartości dla właściwości `FUNCTIONS_WORKER_RUNTIME` mapujący do języka aplikacji `dotnet | node | java | python`.
    * Aby poprawić czas zużycia i uruchamiania, aplikacje mogą zawierać maksymalnie jeden język. Możesz opublikować wielu aplikacjom funkcji w różnych językach dla tego samego rozwiązania.
* Domyślna wartość limitu czasu dla funkcji w planie usługi app service to 30 minut.  Można nadal ją ręcznie ustawić bez ograniczeń.
* Ograniczenia współbieżności HTTP są implementowane przez domyślną dla funkcji planu zużycie (100 równoczesnych żądań na wystąpienie).  Te ustawienia można modyfikować za pomocą `host.json` pliku.
* [Ze względu na .NET core ograniczenia](https://github.com/Azure/azure-functions-host/issues/3414), `.fsx` skrypty dla F # funkcje zostały usunięte. Skompilowane funkcje F # są nadal obsługiwane.
* Format Wyzwalacze oparte na elementach webhook (np. usługi Event Grid) została zmieniona na `https://{app}/runtime/webhooks/{triggerName}`

### <a name="upgrading-a-locally-developed-application"></a>Uaktualnianie aplikacji opracowanych lokalnie

Jeśli aplikacja v1.x została opracowana lokalnie, możesz wprowadzać zmiany, do aplikacji lub projekt, aby był zgodny z v2.  Zalecane jest, aby utworzyć nową aplikację i port nad kodem w nowej aplikacji.  Gdy istnieją zmiany, które można ustanowić do istniejących aplikacji do wykonania od razu uaktualnienia, istnieje kilka innych ulepszeń między v1 i v2, które starszego kodu, prawdopodobnie nie korzystając z zalet (na przykład w języku C# zmiany z `TraceWriter` do `ILogger`) .  

Ścieżka zalecane jest rozpoczęcie od jednego z szablonów w wersji 2 i przenieść kod do nowego projektu lub aplikacji.

#### <a name="visual-studio-runtime-versions"></a>Wersje środowiska uruchomieniowego usługi Visual Studio

W programie Visual Studio wybranej wersji środowiska uruchomieniowego podczas tworzenia projektu.  Visual Studio ma usługi bits dla obu wersji głównych i dynamicznie mogą korzystać z właściwy dla projektu.  Te ustawienia są uzyskiwane z `.csproj` pliku.  W przypadku aplikacji 1.x projekt ma właściwości

```xml
<TargetFramework>net461</TargetFramework>
<AzureFunctionsVersion>v1</AzureFunctionsVersion>
```

W wersji 2 są właściwości projektu

```xml
<TargetFramework>netstandard2.0</TargetFramework>
<AzureFunctionsVersion>v2</AzureFunctionsVersion>
```

Klikając debugowania lub publikowania zostanie prawidłowo ustawione właściwą wersję dla ustawień projektu.

#### <a name="vs-code-and-azure-functions-core-tools"></a>Program VS Code i podstawowych narzędzi usługi Azure Functions

Inne narzędzia lokalne opiera się na podstawowych narzędzi usługi Azure Functions.  Te narzędzia są instalowane z maszyną, i zazwyczaj tylko jedna wersja jest instalowana na komputerze deweloperskim w tym samym czasie.  Zobacz [instrukcje dotyczące sposobu instalowania określone wersje podstawowych narzędzi](./functions-run-local.md).

Dla programu VS Code może również należy zaktualizować ustawienia użytkownika dla `azureFunctions.projectRuntime` wersją zainstalowane narzędzia.  Spowoduje to również zaktualizowanie szablonów i języków wyświetlone podczas tworzenia nowych aplikacji.

### <a name="changing-version-of-apps-in-azure"></a>Zmiana wersji aplikacji na platformie Azure

Wersji opublikowanej aplikacji są ustawiane za pomocą ustawienia aplikacji `FUNCTIONS_EXTENSION_VERSION`.  Jest ono ustawione na `~2` dla aplikacji w wersji 2 i `~1` dla aplikacji w wersji 1.  Jest zdecydowanie odradzane, aby zmienić wersję środowiska uruchomieniowego aplikacji, która zawiera istniejących funkcji publikowane do niego bez również zmianę kodu z tych funkcji.  Ścieżka zalecane jest tworzenie nowej aplikacji funkcji i ustaw odpowiednią wersję, testować zmiany, a następnie wyłączenie lub usunięcie poprzedniej aplikacji.

## <a name="bindings"></a>Powiązania 

Środowisko uruchomieniowe 2.x używa nowego [powiązanie modelu rozszerzalności](https://github.com/Azure/azure-webjobs-sdk-extensions/wiki/Binding-Extensions-Overview) , oferuje następujące korzyści:

* Obsługa rozszerzeń powiązania innych firm.
* Rozdzielenie środowiska uruchomieniowego i powiązania. Dzięki temu rozszerzeń powiązania jako numerów wersji i wydania niezależnie. Można na przykład zdecydować się na uaktualnienie do wersji rozszerzenia, która opiera się na nowszą wersję zestawu SDK bazowego.
* Jaśniejszy środowiska wykonania, gdy tylko powiązania używane są znane i ładowane w czasie wykonywania.

Wszystkie wbudowane powiązania usługi Azure Functions zdecydowali się tego modelu, a nie jest już znajdują się domyślnie, z wyjątkiem wyzwalacza czasomierza i wyzwalacza HTTP. Te rozszerzenia są instalowane automatycznie podczas tworzenia funkcji za pomocą narzędzi, takich jak Visual Studio lub za pośrednictwem portalu.

Poniższa tabela wskazuje, które powiązania są obsługiwane w każdej wersji środowiska uruchomieniowego.

[!INCLUDE [Full bindings table](../../includes/functions-bindings.md)]

## <a name="next-steps"></a>Kolejne kroki

Więcej informacji zawierają następujące zasoby:

* [Kodowanie i testowanie usługi Azure Functions lokalnie](functions-run-local.md)
* [Jak odwoływać się wersje środowiska uruchomieniowego usługi Azure Functions](set-runtime-version.md)
* [Informacje o wersji](https://github.com/Azure/azure-functions-host/releases)
