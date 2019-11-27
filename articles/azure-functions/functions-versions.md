---
title: Przegląd wersji środowiska uruchomieniowego Azure Functions
description: Azure Functions obsługuje wiele wersji środowiska uruchomieniowego. Poznaj różnice między nimi i wybierz odpowiedni dla siebie.
ms.topic: conceptual
ms.date: 10/10/2019
ms.openlocfilehash: 53da5869b4768c95fd225fb15db60f4301e537d4
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/20/2019
ms.locfileid: "74226550"
---
# <a name="azure-functions-runtime-versions-overview"></a>Przegląd wersji środowiska uruchomieniowego Azure Functions

Główne wersje środowiska uruchomieniowego Azure Functions są powiązane z wersją platformy .NET, w której bazuje środowisko uruchomieniowe. W poniższej tabeli znajduje się bieżąca wersja środowiska uruchomieniowego, poziom wersji i powiązana wersja platformy .NET. 

| Wersja środowiska uruchomieniowego | Poziom wydania<sup>1</sup> | Wersja platformy .NET | 
| --------------- | ------------- | ------------ |
| wersji  | wersja zapoznawcza | .NET Core 3. x | 
| 2.x | Ogólna dostępność | .NET Core 2.2 |
| 1.x | GA<sup>2</sup> | .NET Framework 4,6<sup>3</sup> |

<sup>1</sup> Wersje GA są obsługiwane w scenariuszach produkcyjnych.   
<sup>2</sup> Wersja 1. x jest w trybie konserwacji. Ulepszenia są dostępne tylko w nowszych wersjach.   
<sup>3</sup> Program obsługuje tylko programowanie w Azure Portal lub lokalnie na komputerach z systemem Windows.

>[!NOTE]  
> Wersja 3. x środowiska uruchomieniowego usługi Functions jest w wersji zapoznawczej i nie jest obsługiwana w środowiskach produkcyjnych. Aby uzyskać więcej informacji na temat wypróbowania wersji 3. x, zobacz [ten anons](https://dev.to/azure/develop-azure-functions-using-net-core-3-0-gcm).

W tym artykule opisano niektóre różnice między różnymi wersjami, sposoby tworzenia poszczególnych wersji oraz zmiany wersji.

## <a name="languages"></a>Języki

Począwszy od wersji 2. x, środowisko uruchomieniowe używa modelu rozszerzalności języka, a wszystkie funkcje w aplikacji funkcji muszą korzystać z tego samego języka. Język funkcji w aplikacji funkcji jest wybierany podczas tworzenia aplikacji i jest przechowywany w usłudze [functions\_ustawienia środowiska uruchomieniowego\_Worker](functions-app-settings.md#functions_worker_runtime) . 

Azure Functions 1. x Języki eksperymentalne nie mogą używać nowego modelu, więc nie są obsługiwane w 2. x. Poniższa tabela wskazuje, które języki programowania są obecnie obsługiwane w poszczególnych wersjach środowiska uruchomieniowego.

[!INCLUDE [functions-supported-languages](../../includes/functions-supported-languages.md)]

Więcej informacji, zobacz [Obsługiwane języki](supported-languages.md).

## <a name="creating-1x-apps"></a>Uruchom w określonej wersji

Domyślnie aplikacje funkcji utworzone w Azure Portal i przez interfejs wiersza polecenia platformy Azure są ustawione na wersję 2. x. Jeśli to możliwe, należy użyć tej wersji środowiska uruchomieniowego. Jeśli zachodzi taka potrzeba, można nadal uruchamiać aplikację funkcji w środowisku uruchomieniowym w wersji 1. x. Wersję środowiska uruchomieniowego można zmienić tylko po utworzeniu aplikacji funkcji, ale przed dodaniem jakichkolwiek funkcji. Aby dowiedzieć się, jak przypiąć wersję środowiska uruchomieniowego do wersji 1. x, zobacz [Wyświetlanie i aktualizowanie bieżącej wersji środowiska uruchomieniowego](set-runtime-version.md#view-and-update-the-current-runtime-version).

Możesz również uaktualnić program do wersji 3. x środowiska uruchomieniowego, które jest z niego w wersji zapoznawczej. Zrób tak, jeśli musisz mieć możliwość uruchamiania funkcji w programie .NET Core 3. x. Aby dowiedzieć się, jak przeprowadzić uaktualnienie do wersji 3. x, zobacz [Wyświetlanie i aktualizowanie bieżącego środowiska uruchomieniowego](set-runtime-version.md#view-and-update-the-current-runtime-version).

## <a name="migrating-from-1x-to-later-versions"></a>Migrowanie z 1. x do nowszych wersji

Możesz zdecydować się na migrację istniejącej aplikacji w celu użycia wersji 1. x środowiska uruchomieniowego, aby zamiast tego użyć wersji 2. x. Większość zmian, które należy wprowadzić, dotyczy zmian w środowisku uruchomieniowym języka, takich jak C# zmiany interfejsu API między .NET Framework 4,7 i .NET Core 2. Należy również upewnić się, że kod i biblioteki są zgodne z wybranym środowiskiem uruchomieniowym języka. Na koniec pamiętaj o uwzględnieniu wszelkich zmian w wyzwalaczu, powiązaniach i funkcjach wyróżnionych poniżej. Aby uzyskać najlepsze wyniki migracji, należy utworzyć nową aplikację funkcji dla wersji 2. x i przenieść istniejący kod funkcji w wersji 1. x do nowej aplikacji.  

### <a name="changes-in-triggers-and-bindings"></a>Zmiany w wyzwalaczach i powiązaniach

Wersja 2. x wymaga zainstalowania rozszerzeń dla określonych wyzwalaczy i powiązań używanych przez funkcje w aplikacji. Jedyny wyjątek dla tych wyzwalaczy HTTP i Timer, które nie wymagają rozszerzenia.  Aby uzyskać więcej informacji, zobacz [Rejestrowanie i instalowanie rozszerzeń powiązań](./functions-bindings-register.md).

Wprowadzono również pewne zmiany w `function.json` lub atrybuty funkcji między wersjami. Na przykład właściwość `path` centrum zdarzeń jest teraz `eventHubName`. W [istniejącej tabeli powiązań](#bindings) znajdziesz linki do dokumentacji dla każdego powiązania.

### <a name="changes-in-features-and-functionality"></a>Zmiany w funkcjach i funkcjach

Kilka funkcji, które zostały również usunięte, zaktualizowane lub zastąpione w nowej wersji. Ta sekcja zawiera szczegółowe informacje o zmianach, które są widoczne w wersji 2. x po użyciu wersji 1. x.

W wersji 2. x wprowadzono następujące zmiany:

* Klucze do wywoływania punktów końcowych HTTP są zawsze przechowywane w postaci zaszyfrowanej w usłudze Azure Blob Storage. W wersji 1. x klucze były przechowywane w usłudze Azure File Storage. Podczas uaktualniania aplikacji z wersji 1. x do wersji 2. x istniejące klucze tajne w magazynie plików są resetowane.

* Środowisko uruchomieniowe w wersji 2. x nie obejmuje wbudowanej obsługi dostawców elementu webhook. Ta zmiana została wprowadzona w celu zwiększenia wydajności. Można nadal używać wyzwalaczy HTTP jako punktów końcowych dla elementów webhook.

* Plik konfiguracji hosta (host. JSON) powinien być pusty lub mieć ciąg `"version": "2.0"`.

* Aby poprawić monitorowanie, pulpit nawigacyjny zadań WebJob w portalu, który użył ustawienia [`AzureWebJobsDashboard`](functions-app-settings.md#azurewebjobsdashboard) , jest zastępowany Application Insights platformy Azure, który używa ustawienia [`APPINSIGHTS_INSTRUMENTATIONKEY`](functions-app-settings.md#appinsights_instrumentationkey) . Aby uzyskać więcej informacji, zobacz [Monitor Azure Functions](functions-monitoring.md).

* Wszystkie funkcje w aplikacji funkcji muszą korzystać z tego samego języka. Podczas tworzenia aplikacji funkcji należy wybrać stos środowiska uruchomieniowego dla aplikacji. Stos środowiska uruchomieniowego jest określany przez wartość [`FUNCTIONS_WORKER_RUNTIME`](functions-app-settings.md#functions_worker_runtime) w ustawieniach aplikacji. Ten wymóg został dodany w celu poprawy rozmiaru i czasu uruchamiania. Podczas programowania lokalnego należy również uwzględnić to ustawienie w [pliku Local. Settings. JSON](functions-run-local.md#local-settings-file).

* Domyślny limit czasu dla funkcji w planie App Service został zmieniony na 30 minut. Limit czasu można zmienić ręcznie na nieograniczony przy użyciu ustawienia [functionTimeout](functions-host-json.md#functiontimeout) w pliku host. JSON.

* Ograniczenia współbieżności HTTP są implementowane domyślnie dla funkcji planu zużycia, z wartością domyślną 100 współbieżnych żądań na wystąpienie. Można to zmienić w ustawieniach [`maxConcurrentRequests`](functions-host-json.md#http) w pliku host. JSON.

* Ze względu na [ograniczenia programu .NET Core](https://github.com/Azure/azure-functions-host/issues/3414), obsługa funkcji F# skryptów (. FSX) została usunięta. Skompilowane F# funkcje (. FS) są nadal obsługiwane.

* Format adresu URL elementu webhook wyzwalacza Event Grid został zmieniony na `https://{app}/runtime/webhooks/{triggerName}`.

### <a name="migrating-a-locally-developed-application"></a>Migrowanie aplikacji opracowanej lokalnie

Istnieje możliwość, że istniejące projekty aplikacji funkcji są opracowywane lokalnie przy użyciu wersji 1. x środowiska uruchomieniowego. Aby uaktualnić do wersji 2. x, należy utworzyć projekt aplikacji funkcji lokalnych dla wersji 2. x i przenieść istniejący kod do nowej aplikacji. Możesz ręcznie zaktualizować istniejący projekt i kod, sortując uaktualnienie "w miejscu". Istnieje jednak wiele innych ulepszeń między wersjami 1. x i 2. x, które nadal mogą być potrzebne. Na przykład, w C# obiekcie debugowania został zmieniony z `TraceWriter` na `ILogger`. Utworzenie nowego projektu w wersji 2. x, rozpoczyna się od zaktualizowania funkcji na podstawie najnowszych szablonów w wersji 2. x.

#### <a name="visual-studio-runtime-versions"></a>Wersje środowiska uruchomieniowego programu Visual Studio

W programie Visual Studio podczas tworzenia projektu wybierana jest wersja środowiska uruchomieniowego. Narzędzia Azure Functions Tools for Visual Studio obsługują zarówno główne wersje środowiska uruchomieniowego. Poprawna wersja jest używana podczas debugowania i publikowania na podstawie ustawień projektu. Ustawienia wersji są zdefiniowane w pliku `.csproj` w następujących właściwościach:

##### <a name="version-1x"></a>Wersja 1. x

```xml
<TargetFramework>net461</TargetFramework>
<AzureFunctionsVersion>v1</AzureFunctionsVersion>
```

##### <a name="version-2x"></a>Wersja 2. x

```xml
<TargetFramework>netcoreapp2.2</TargetFramework>
<AzureFunctionsVersion>v2</AzureFunctionsVersion>
```

Podczas debugowania lub publikowania projektu jest używana poprawna wersja środowiska uruchomieniowego.

#### <a name="vs-code-and-azure-functions-core-tools"></a>VS Code i Azure Functions Core Tools

[Azure Functions Core Tools](functions-run-local.md) jest używany do tworzenia wierszy poleceń, a także przez [rozszerzenie Azure Functions](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions) do Visual Studio Code. Aby opracowywać wersję 2. x, Zainstaluj wersję 2. x podstawowych narzędzi. Programowanie w wersji 1. x wymaga wersji 1. x podstawowych narzędzi. Aby uzyskać więcej informacji, zobacz [instalowanie Azure Functions Core Tools](functions-run-local.md#install-the-azure-functions-core-tools).

Na potrzeby tworzenia Visual Studio Code może być również konieczne zaktualizowanie ustawień użytkownika dla `azureFunctions.projectRuntime`, aby odpowiadały zainstalowanej wersji narzędzi.  To ustawienie powoduje także aktualizację szablonów i języków używanych podczas tworzenia aplikacji funkcji.

### <a name="changing-version-of-apps-in-azure"></a>Zmiana wersji aplikacji na platformie Azure

Wersja środowiska uruchomieniowego funkcji używanych przez opublikowane aplikacje na platformie Azure jest określana przez ustawienie aplikacji [`FUNCTIONS_EXTENSION_VERSION`](functions-app-settings.md#functions_extension_version) . Wartość `~2` jest przeznaczona dla środowiska uruchomieniowego w wersji 2. x i `~1` jako wersja 1. x środowiska uruchomieniowego. Nie zmieniaj arbitralnie tego ustawienia, ponieważ prawdopodobnie wymagane są inne zmiany ustawień aplikacji i zmiany kodu w Twoich funkcjach. Aby dowiedzieć się więcej o zalecanym sposobie migrowania aplikacji funkcji do innej wersji środowiska uruchomieniowego, zobacz [jak kierować Azure Functions wersjami środowiska uruchomieniowego](set-runtime-version.md).

## <a name="bindings"></a>Powiązania

Począwszy od wersji 2. x, środowisko uruchomieniowe używa nowego [modelu rozszerzalności powiązania](https://github.com/Azure/azure-webjobs-sdk-extensions/wiki/Binding-Extensions-Overview) , który oferuje następujące korzyści:

* Obsługa rozszerzeń powiązań innych firm.

* Rozdzielenie środowiska uruchomieniowego i powiązań. Ta zmiana umożliwia niezależne i wyłączanie rozszerzeń powiązań. Możesz na przykład wybrać opcję Uaktualnij do wersji rozszerzenia, która zależy od nowszej wersji bazowego zestawu SDK.

* Jaśniejsze środowisko wykonawcze, w którym tylko powiązania są znane i ładowane przez środowisko uruchomieniowe.

Z wyjątkiem wyzwalaczy HTTP i Timer, wszystkie powiązania muszą być jawnie dodane do projektu aplikacji funkcji lub zarejestrowane w portalu. Aby uzyskać więcej informacji, zobacz [Rejestrowanie rozszerzeń powiązań](./functions-bindings-expressions-patterns.md).

W poniższej tabeli przedstawiono powiązania obsługiwane w poszczególnych wersjach środowiska uruchomieniowego.

[!INCLUDE [Full bindings table](../../includes/functions-bindings.md)]

[!INCLUDE [Timeout Duration section](../../includes/functions-timeout-duration.md)]

## <a name="next-steps"></a>Następne kroki

Więcej informacji zawierają następujące zasoby:

* [Kodowanie i testowanie usługi Azure Functions lokalnie](functions-run-local.md)
* [Jak docelowa Azure Functions wersje środowiska uruchomieniowego](set-runtime-version.md)
* [Informacje o wersji](https://github.com/Azure/azure-functions-host/releases)
