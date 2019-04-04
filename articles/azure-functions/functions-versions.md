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
ms.openlocfilehash: 6988fb547b07f81891efea3caad8bf34f4c8a476
ms.sourcegitcommit: a60a55278f645f5d6cda95bcf9895441ade04629
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/03/2019
ms.locfileid: "58889757"
---
# <a name="azure-functions-runtime-versions-overview"></a>Wersje środowiska uruchomieniowego usługi Azure Functions — omówienie

 Istnieją dwie wersje główne środowiska uruchomieniowego usługi Azure Functions: w wersji 1.x i 2.x. Bieżącą wersję, w których wprowadzono nowe pracy nad funkcjami i ulepszenia jest 2.x, jeśli obie są obsługiwane w przypadku scenariuszy produkcyjnych.  Następujące szczegóły, niektóre różnice między nimi, jak można utworzyć każdej wersji i uaktualnić wersję 1.x do 2.x.

> [!NOTE]
> Ten artykuł odnosi się do usługi w chmurze usługi Azure Functions. Aby uzyskać informacji na temat produktu w wersji zapoznawczej, który pozwala na uruchamianie usługi Azure Functions w środowisku lokalnym, zobacz [Przegląd środowiska uruchomieniowego usługi Azure Functions](functions-runtime-overview.md).

## <a name="cross-platform-development"></a>Programowanie dla wielu platform

Środowisko uruchomieniowe 2.x wersji działa na .NET Core 2, co umożliwia jego uruchomienie na wszystkich platformach obsługiwanych przez platformę .NET Core, w tym w systemach macOS i Linux. Uruchamianie na platformie .NET Core umożliwia programowanie dla wielu platform i scenariuszach hostingu.

Dla porównania środowisko uruchomieniowe 1.x wersji obsługuje tylko programowania i hostowania w witrynie Azure portal lub na komputerach z Windows.

## <a name="languages"></a>Languages

Środowisko uruchomieniowe 2.x wersji używa nowego modelu rozszerzalności języka. W wersji 2.x, wszystkie funkcje w aplikacji funkcji muszą współużytkować ten sam język. Język funkcji w aplikacji funkcji jest wybierany podczas tworzenia aplikacji.

Usługa Azure języków eksperymentalnych 1.x funkcji nie zostaną zaktualizowane do użycia nowego modelu, dzięki czemu nie są one obsługiwane w 2.x. Poniższa tabela wskazuje, które języki programowania są obecnie obsługiwane w każdej wersji środowiska uruchomieniowego.

[!INCLUDE [functions-supported-languages](../../includes/functions-supported-languages.md)]

Więcej informacji, zobacz [Obsługiwane języki](supported-languages.md).

## <a name="creating-1x-apps"></a>Działa w wersji 1.x

Domyślnie funkcja aplikacje utworzone w witrynie Azure portal są ustawione na wersji 2.x. Jeśli to możliwe, należy użyć tej wersji środowiska uruchomieniowego, gdzie zostaną wprowadzone nowe inwestycje funkcji. Jeśli zachodzi potrzeba, nadal można uruchomić aplikacji funkcji na środowisko uruchomieniowe 1.x wersji. Wersja środowiska uruchomieniowego można zmienić tylko po utworzeniu aplikacji funkcji, ale przed dodaniem wszystkie funkcje. Aby dowiedzieć się, jak przypiąć wersji 1.x środowiska uruchomieniowego, zobacz [Podgląd i aktualizacja bieżącą wersję środowiska uruchomieniowego](set-runtime-version.md#view-and-update-the-current-runtime-version).

## <a name="migrating-from-1x-to-2x"></a>Migrowanie z 1.x do 2.x

Możesz przeprowadzić migrację istniejącej aplikacji napisanej na potrzeby zamiast tego użyj wersji przez środowisko uruchomieniowe 1.x wersji 2.x. Większość zmian, które należy wprowadzić odnoszą się do zmian w środowisku uruchomieniowym języka, takich jak interfejs API języka C# zmian między wersjami programu .NET Framework 4.7 i .NET Core 2. Należy także upewnić się, że Twój kod i biblioteki są zgodne ze środowiskiem uruchomieniowym języka, możesz wybrać. Na koniec upewnij się, należy pamiętać, wszelkie zmiany w wyzwalacza, powiązania i funkcje, które przedstawiono poniżej. Aby uzyskać najlepsze wyniki migracji, należy utworzyć nową aplikację funkcji w wersji 2.x i port istniejących funkcji w wersji 1.x kodu w nowej aplikacji.  

### <a name="changes-in-triggers-and-bindings"></a>Zmiany w wyzwalaczy i powiązań

W wersji 2.x, musisz zainstalować rozszerzenia dla określonych wyzwalaczy i powiązań, które korzystają z funkcji w aplikacji. Jedynym wyjątkiem to HTTP oraz czasomierzem wyzwalacze, które nie wymagają rozszerzenia.  Aby uzyskać więcej informacji, zobacz [rejestru i instalowanie rozszerzeń powiązania](./functions-bindings-register.md).

Wprowadzono również drobne zmiany w `function.json` lub atrybutów funkcji między wersjami. Na przykład Centrum zdarzeń `path` właściwość jest obecnie `eventHubName`. Zobacz [istniejącej tabeli powiązania](#bindings) linki do dokumentacji dla każdego powiązania.

### <a name="changes-in-features-and-functionality"></a>Zmiany w funkcji i możliwości

Kilka funkcji, które również zostaną usunięte, zaktualizowane lub zamienione w nowej wersji. Tej sekcji opisano szczegółowo zmian zostanie wyświetlony w wersji 2.x po o używane wersji 1.x.

W wersji 2.x, wprowadzono następujące zmiany:

* Klucze dla wywoływanie punktów końcowych HTTP zawsze są przechowywane w postaci zaszyfrowanej w usłudze Azure Blob storage. W wersji 1.x, klucze były przechowywane w usłudze Azure File storage jest domyślnie. Podczas uaktualniania aplikacji z wersji 1.x do wersji 2.x, istniejące wpisów tajnych, które znajdują się w pliku magazynu zostaną zresetowane.

* Środowisko uruchomieniowe 2.x wersja nie zawiera wbudowaną obsługę dostawców elementu webhook. Ta zmiana została wprowadzona w celu zwiększenia wydajności. Można nadal używać wyzwalaczy HTTP jako punkty końcowe, dla elementów webhook.

* Plik konfiguracji hosta (host.json) powinna być pusta ani mieć ciąg `"version": "2.0"`.

* Aby ulepszyć monitorowanie pulpitu nawigacyjnego zadań Webjob w portalu i użyć [ `AzureWebJobsDashboard` ](functions-app-settings.md#azurewebjobsdashboard) zastępuje ustawienie przy użyciu usługi Azure Application Insights, który używa [ `APPINSIGHTS_INSTRUMENTATIONKEY` ](functions-app-settings.md#appinsights_instrumentationkey) ustawienie. Aby uzyskać więcej informacji, zobacz [monitora usługi Azure Functions](functions-monitoring.md).

* Wszystkie funkcje w aplikacji funkcji muszą współużytkować ten sam język. Podczas tworzenia aplikacji funkcji, musisz wybrać stosu środowiska uruchomieniowego aplikacji. Stos środowiska uruchomieniowego jest określona przez [ `FUNCTIONS_WORKER_RUNTIME` ](functions-app-settings.md#functions_worker_runtime) wartość w ustawieniach aplikacji. To wymaganie zostało dodane do poprawić czas zużycia i uruchamiania. Podczas tworzenia lokalnie, należy również uwzględnić to ustawienie w [pliku local.settings.json](functions-run-local.md#local-settings-file).

* Domyślna wartość limitu czasu dla funkcji w ramach planu usługi App Service jest zmieniany na 30 minut. Można ręcznie zmienić limit czasu na nieograniczony, za pomocą [functionTimeout](functions-host-json.md#functiontimeout) ustawienie w host.json.

* Ograniczenia współbieżności HTTP są implementowane przez domyślną dla funkcji planu zużycie, z domyślną 100 równoczesnych żądań dla każdego wystąpienia. Tę wartość można zmienić w [ `maxConcurrentRequests` ](functions-host-json.md#http) ustawienia w pliku host.json.

* Z powodu [.NET core ograniczenia](https://github.com/Azure/azure-functions-host/issues/3414), obsługa F# skrypt (.fsx) funkcje zostały usunięte. Skompilowany F# funkcji (.fs) są nadal obsługiwane.

* Format adresu URL elementów webhook wyzwalacza usługi Event Grid została zmieniona na `https://{app}/runtime/webhooks/{triggerName}`.

### <a name="migrating-a-locally-developed-application"></a>Migrowanie aplikacji opracowanych lokalnie

Masz istniejące projekty aplikacji opracowanych lokalnie przy użyciu środowisko uruchomieniowe 1.x wersji funkcji. Aby uaktualnić do wersji 2.x, należy utworzyć projekt aplikacji funkcji lokalnej wersji 2.x i port istniejący kod w nowej aplikacji. Można ręcznie zaktualizować istniejącego projektu i kodu, rodzaj uaktualnienia "w miejscu". Jednak istnieje kilka innych ulepszeń między wersji 1.x i wersji 2.x, które nadal może zajść potrzeba wprowadzenia. Na przykład w języku C# debugowania obiektu został zmieniony z `TraceWriter` do `ILogger`. Tworząc nowy projekt w wersji 2.x, należy rozpocząć zaktualizowane funkcje oparte na najnowszych szablonów w wersji 2.x.

#### <a name="visual-studio-runtime-versions"></a>Wersje środowiska uruchomieniowego usługi Visual Studio

W programie Visual Studio wybranej wersji środowiska uruchomieniowego podczas tworzenia projektu. Narzędzia usługi Azure Functions dla programu Visual Studio obsługuje obie wersje główne środowiska uruchomieniowego. Poprawna wersja jest używany podczas debugowania i publikowania zgodnie z ustawieniami projektu. Ustawienia wersji są zdefiniowane w `.csproj` pliku w następujących właściwościach:

##### <a name="version-1x"></a>W wersji 1.x

```xml
<TargetFramework>net461</TargetFramework>
<AzureFunctionsVersion>v1</AzureFunctionsVersion>
```

##### <a name="version-2x"></a>W wersji 2.x

```xml
<TargetFramework>netcoreapp2.2</TargetFramework>
<AzureFunctionsVersion>v2</AzureFunctionsVersion>
```

Podczas debugowania lub opublikować projekt poprawną wersję środowiska uruchomieniowego jest używany.

#### <a name="vs-code-and-azure-functions-core-tools"></a>Program VS Code i podstawowych narzędzi usługi Azure Functions

[Podstawowe narzędzia usługi Azure Functions](functions-run-local.md) służy do tworzenia aplikacji wiersza polecenia, a także przez [rozszerzenia usługi Azure Functions](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions) dla programu Visual Studio Code. Aby można było wprowadzać zmiany w wersji 2.x, zainstaluj w wersji 2.x podstawowych narzędzi. Narzędzia deweloperskie dla wersji 1.x wymagają wersji 1.x podstawowych narzędzi. Aby uzyskać więcej informacji, zobacz [instalowanie podstawowych narzędzi usługi Azure Functions](functions-run-local.md#install-the-azure-functions-core-tools).

Do tworzenia aplikacji programu Visual Studio Code, konieczne może być zaktualizować ustawienia użytkownika dla `azureFunctions.projectRuntime` wersją zainstalowane narzędzia.  To ustawienie, aktualizuje również szablony i języki używane podczas tworzenia aplikacji funkcji.

### <a name="changing-version-of-apps-in-azure"></a>Zmiana wersji aplikacji na platformie Azure

Wersja środowisko uruchomieniowe usługi Functions używana przez opublikowane aplikacje na platformie Azure jest zależna od [ `FUNCTIONS_EXTENSION_VERSION` ](functions-app-settings.md#functions_extension_version) ustawienia aplikacji. Wartość `~2` jest przeznaczony dla środowiska uruchomieniowego w wersji 2.x i `~1` elementy docelowe środowisko uruchomieniowe 1.x wersji. Arbitralnie nie zmieniaj tego ustawienia, ponieważ prawdopodobnie wymagane są inne zmiany w ustawieniach aplikacji i zmiany kodu w funkcji. Aby dowiedzieć się o zalecanym sposobem migracji aplikacji funkcji do środowiskiem uruchomieniowym w różnych wersji, zobacz [sposobu kierowania wersje środowiska uruchomieniowego usługi Azure Functions](set-runtime-version.md).

## <a name="bindings"></a>Powiązania

Środowisko uruchomieniowe 2.x wersji używa nowego [powiązanie modelu rozszerzalności](https://github.com/Azure/azure-webjobs-sdk-extensions/wiki/Binding-Extensions-Overview) , oferuje następujące korzyści:

* Obsługa rozszerzeń powiązania innych firm.

* Rozdzielenie środowiska uruchomieniowego i powiązania. Ta zmiana umożliwia rozszerzeń powiązania jako numerów wersji i wydania niezależnie. Można na przykład zdecydować się na uaktualnienie do wersji rozszerzenia, która opiera się na nowszą wersję zestawu SDK bazowego.

* Jaśniejszy środowiska wykonania, gdy tylko powiązania używane są znane i ładowane w czasie wykonywania.

Z wyjątkiem wyzwalaczy HTTP oraz czasomierzem wszystkie powiązania musi być jawnie dodane do projektu aplikacji funkcji lub zarejestrowane w portalu. Aby uzyskać więcej informacji, zobacz [zarejestrować rozszerzeń powiązania](./functions-bindings-expressions-patterns.md).

W poniższej tabeli przedstawiono, które powiązania są obsługiwane w każdej wersji środowiska uruchomieniowego.

[!INCLUDE [Full bindings table](../../includes/functions-bindings.md)]

[!INCLUDE [Timeout Duration section](../../includes/functions-timeout-duration.md)]

## <a name="next-steps"></a>Kolejne kroki

Więcej informacji zawierają następujące zasoby:

* [Kodowanie i testowanie usługi Azure Functions lokalnie](functions-run-local.md)
* [Jak odwoływać się wersje środowiska uruchomieniowego usługi Azure Functions](set-runtime-version.md)
* [Informacje o wersji](https://github.com/Azure/azure-functions-host/releases)
