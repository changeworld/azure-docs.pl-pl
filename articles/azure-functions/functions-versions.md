---
title: Omówienie wersji środowiska uruchomieniowego usługi Azure Functions
description: Usługa Azure Functions obsługuje wiele wersji środowiska wykonawczego. Dowiedz się, jakie są różnice między nimi i jak wybrać ten, który jest odpowiedni dla Ciebie.
ms.topic: conceptual
ms.date: 12/09/2019
ms.openlocfilehash: 21a7b25087efd5d4adf2154c935636c263df9afd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79276558"
---
# <a name="azure-functions-runtime-versions-overview"></a>Omówienie wersji środowiska uruchomieniowego usługi Azure Functions

Główne wersje środowiska uruchomieniowego usługi Azure Functions są powiązane z wersją platformy .NET, na której opiera się środowisko wykonawcze. Poniższa tabela wskazuje bieżącą wersję środowiska wykonawczego, poziom wydania i powiązaną wersję .NET. 

| Wersja środowiska wykonawczego | Poziom uwalniania<sup>1</sup> | wersja .NET | 
| --------------- | ------------- | ------------ |
| 3.x | Ogólna dostępność | .NET Rdzeń 3.1 | 
| 2.x | Ogólna dostępność | .NET Core 2.2 |
| 1.x | <sup>2.</sup> | .NET Framework 4.6<sup>3</sup> |

<sup>1</sup> Wersje GA są obsługiwane dla scenariuszy produkcyjnych.   
<sup>2</sup> Wersja 1.x jest w trybie konserwacji. Ulepszenia są dostępne tylko w nowszych wersjach.   
<sup>3</sup> Obsługuje tylko rozwój w witrynie Azure portal lub lokalnie na komputerach z systemem Windows.

W tym artykule opisano niektóre różnice między różnymi wersjami, jak można utworzyć każdą wersję i jak zmienić wersje.

## <a name="languages"></a>Języki

Począwszy od wersji 2.x środowisko wykonawcze używa modelu rozszerzalności języka, a wszystkie funkcje w aplikacji funkcji muszą współużytkować ten sam język. Język funkcji w aplikacji funkcji jest wybierany podczas tworzenia aplikacji i jest obsługiwany w [ustawieniach FUNKCJI\_WORKER\_RUNTIME.](functions-app-settings.md#functions_worker_runtime) 

Eksperymentalne języki usługi Azure Functions 1.x nie mogą używać nowego modelu, więc nie są obsługiwane w języku 2.x. W poniższej tabeli przedstawiono, które języki programowania są obecnie obsługiwane w każdej wersji środowiska wykonawczego.

[!INCLUDE [functions-supported-languages](../../includes/functions-supported-languages.md)]

Więcej informacji, zobacz [Obsługiwane języki](supported-languages.md).

## <a name="run-on-a-specific-version"></a><a name="creating-1x-apps"></a>Uruchamianie w określonej wersji

Domyślnie aplikacje funkcji utworzone w witrynie Azure portal i przez platformę Azure CLI są ustawione na wersję 3.x. W razie potrzeby można zmodyfikować tę wersję. Wersję środowiska wykonawczego można zmienić tylko na 1.x po utworzeniu aplikacji funkcji, ale przed dodaniem jakichkolwiek funkcji.  Przechodzenie między 2.x i 3.x jest dozwolone nawet w aplikacjach, które mają funkcje, ale nadal zaleca się przetestowanie w nowej aplikacji.

## <a name="migrating-from-1x-to-later-versions"></a>Migracja z wersji 1.x do nowszych

Można przeprowadzić migrację istniejącej aplikacji napisanej w celu użycia środowiska wykonawczego w wersji 1.x, aby zamiast tego użyć nowszej wersji. Większość zmian, które należy wprowadzić są związane ze zmianami w czasie wykonywania języka, takich jak zmiany interfejsu API języka C# między .NET Framework 4.7 i .NET Core. Należy również upewnić się, że kod i biblioteki są zgodne z wybranego środowiska wykonawczego języka. Na koniec należy zwrócić uwagę na wszelkie zmiany w wyzwalaczu, powiązania i funkcje wyróżnione poniżej. Aby uzyskać najlepsze wyniki migracji, należy utworzyć nową aplikację funkcji w nowej wersji i przenieść istniejący kod funkcji w wersji 1.x do nowej aplikacji.  

Chociaż jest to możliwe do uaktualnienia "w miejscu" ręcznie aktualizując konfigurację aplikacji, przechodząc od 1.x do wyższej wersji zawiera pewne przełomowe zmiany. Na przykład w języku C#obiekt debugowania `TraceWriter` `ILogger`jest zmieniany z na . Tworząc nowy projekt 3.x, zaczynasz od zaktualizowanych funkcji opartych na najnowszych szablonach 3.x.

### <a name="changes-in-triggers-and-bindings-after-version-1x"></a>Zmiany w wyzwalaczach i powiązaniach po wersji 1.x

Począwszy od wersji 2.x, należy zainstalować rozszerzenia dla określonych wyzwalaczy i powiązań używanych przez funkcje w aplikacji. Jedyny wyjątek dla tego wyzwalacza HTTP i czasomierza, które nie wymagają rozszerzenia.  Aby uzyskać więcej informacji, zobacz [Rejestrowanie i instalowanie rozszerzeń powiązań](./functions-bindings-register.md).

Istnieje również kilka zmian w *function.json* lub atrybuty funkcji między wersjami. Na przykład właściwość `path` Centrum `eventHubName`zdarzeń jest teraz . Zobacz [istniejącą tabelę powiązania,](#bindings) aby uzyskać łącza do dokumentacji dla każdego powiązania.

### <a name="changes-in-features-and-functionality-after-version-1x"></a>Zmiany w funkcjach i funkcjach po wersji 1.x

Kilka funkcji zostało usuniętych, zaktualizowanych lub zastąpionych po wersji 1.x. W tej sekcji opisano zmiany widoczne w nowszych wersjach po użyciu wersji 1.x.

W wersji 2.x wprowadzono następujące zmiany:

* Klucze do wywoływania punktów końcowych HTTP są zawsze przechowywane zaszyfrowane w magazynie obiektów Blob platformy Azure. W wersji 1.x klucze były domyślnie przechowywane w magazynie plików platformy Azure. Podczas uaktualniania aplikacji z wersji 1.x do wersji 2.x istniejące wpisy tajne, które znajdują się w magazynie plików, są resetowane.

* Środowisko wykonawcze w wersji 2.x nie zawiera wbudowanej obsługi dostawców elementów webhook. Ta zmiana została wniesiena w celu poprawy wydajności. Wyzwalacze HTTP nadal można używać jako punktów końcowych dla elementów webhook.

* Plik konfiguracji hosta (host.json) powinien być `"version": "2.0"`pusty lub mieć ciąg .

* Aby usprawnić monitorowanie, pulpit nawigacyjny WebJobs [`AzureWebJobsDashboard`](functions-app-settings.md#azurewebjobsdashboard) w portalu, który użył tego ustawienia, jest zastępowany usługą Azure Application Insights, która używa tego [`APPINSIGHTS_INSTRUMENTATIONKEY`](functions-app-settings.md#appinsights_instrumentationkey) ustawienia. Aby uzyskać więcej informacji, zobacz [Monitorowanie funkcji platformy Azure](functions-monitoring.md).

* Wszystkie funkcje w aplikacji funkcji muszą współużytkuje ten sam język. Podczas tworzenia aplikacji funkcji, należy wybrać stos środowiska wykonawczego dla aplikacji. Stos środowiska wykonawczego jest [`FUNCTIONS_WORKER_RUNTIME`](functions-app-settings.md#functions_worker_runtime) określony przez wartość w ustawieniach aplikacji. To wymaganie zostało dodane w celu zwiększenia footprintu i czasu uruchamiania. Podczas tworzenia lokalnie należy również uwzględnić to ustawienie w [pliku local.settings.json](functions-run-local.md#local-settings-file).

* Domyślny limit czasu dla funkcji w planie usługi app service zostanie zmieniony na 30 minut. Limit czasu można ręcznie zmienić na nieograniczony, używając ustawienia [functionTimeout](functions-host-json.md#functiontimeout) w pliku host.json.

* Ograniczenia współbieżności HTTP są domyślnie implementowane dla funkcji planu zużycia, przy domyślnie 100 równoczesnych żądań na wystąpienie. Można to zmienić [`maxConcurrentRequests`](functions-host-json.md#http) w ustawieniu w pliku host.json.

* Z powodu [ograniczeń .NET Core](https://github.com/Azure/azure-functions-host/issues/3414)usunięto obsługę funkcji skryptu F# (.fsx). Skompilowane funkcje Języka F# (.fs) są nadal obsługiwane.

* Format adresu URL elementów webk wyzwalaczy `https://{app}/runtime/webhooks/{triggerName}`sieci web usługi Event Grid został zmieniony na .

## <a name="migrating-from-2x-to-3x"></a>Migracja z 2.x do 3.x

Usługa Azure Functions w wersji 3.x jest ściśle zgodna z wersją 2.x.  Wiele aplikacji powinno być w stanie bezpiecznie uaktualnić do 3.x bez żadnych zmian kodu.  Podczas przechodzenia do 3.x jest zachęcany, należy uruchomić rozbudowane testy przed zmianą głównej wersji w aplikacjach produkcyjnych.

### <a name="breaking-changes-between-2x-and-3x"></a>Przerywanie zmian między 2.x a 3.x

Poniżej przedstawiono zmiany, o których należy pamiętać przed uaktualnieniem aplikacji 2.x do 3.x.

#### <a name="javascript"></a>JavaScript

* Powiązania wyjściowe przypisane za pośrednictwem `context.done` lub zwracane `context.bindings`wartości zachowują się teraz tak samo jak ustawienie w pliku .

* Obiekt spustowy timera to camelCase zamiast PascalCase

* Usługa Event Hub `dataType` wyzwalane funkcje `binary` z binarny otrzyma tablicę zamiast `string`.

* Ładunek żądania HTTP nie jest `context.bindingData.req`już dostępny za pośrednictwem pliku .  Nadal można uzyskać do niego dostęp `context.req`jako `context.bindings`parametr wejściowy, i w .

* Node.js 8 nie jest już obsługiwany i nie będzie wykonywany w funkcjach 3.x.

#### <a name="net"></a>.NET

* [Operacje serwera synchronicznego są domyślnie wyłączone](https://docs.microsoft.com/dotnet/core/compatibility/2.2-3.0#http-synchronous-io-disabled-in-all-servers).

### <a name="changing-version-of-apps-in-azure"></a>Zmienianie wersji aplikacji na platformie Azure

Wersja środowiska wykonawczego funkcji używane przez opublikowane aplikacje na [`FUNCTIONS_EXTENSION_VERSION`](functions-app-settings.md#functions_extension_version) platformie Azure jest podyktowana ustawieniem aplikacji. Obsługiwane są następujące główne wartości wersji środowiska uruchomieniowego:

| Wartość | Miejsce docelowe środowiska uruchomieniowego |
| ------ | -------- |
| `~3` | 3.x |
| `~2` | 2.x |
| `~1` | 1.x |

>[!IMPORTANT]
> Nie zmieniaj dowolnie tego ustawienia, ponieważ mogą być wymagane inne zmiany ustawień aplikacji i zmiany w kodzie funkcji.

### <a name="locally-developed-application-versions"></a>Lokalnie opracowane wersje aplikacji

Można wprowadzić następujące aktualizacje do aplikacji funkcji lokalnie zmienić wersje docelowe.

#### <a name="visual-studio-runtime-versions"></a>Wersje środowiska wykonawczego programu Visual Studio

W programie Visual Studio należy wybrać wersję środowiska wykonawczego podczas tworzenia projektu. Narzędzia usługi Azure Functions dla programu Visual Studio obsługują trzy główne wersje środowiska wykonawczego. Poprawna wersja jest używana podczas debugowania i publikowania na podstawie ustawień projektu. Ustawienia wersji są zdefiniowane w `.csproj` pliku w następujących właściwościach:

##### <a name="version-1x"></a>Wersja 1.x

```xml
<TargetFramework>net461</TargetFramework>
<AzureFunctionsVersion>v1</AzureFunctionsVersion>
```

##### <a name="version-2x"></a>Wersja 2.x

```xml
<TargetFramework>netcoreapp2.1</TargetFramework>
<AzureFunctionsVersion>v2</AzureFunctionsVersion>
```

##### <a name="version-3x"></a>Wersja 3.x

```xml
<TargetFramework>netcoreapp3.1</TargetFramework>
<AzureFunctionsVersion>v3</AzureFunctionsVersion>
```

> [!NOTE]
> Usługi Azure Functions 3.x `Microsoft.NET.Sdk.Functions` i .NET `3.0.0`wymagają, aby rozszerzenie było co najmniej .

###### <a name="updating-2x-apps-to-3x-in-visual-studio"></a>Aktualizowanie aplikacji 2.x do wersji 3.x w programie Visual Studio

Można otworzyć istniejącą funkcję docelową 2.x i przejść do `.csproj` 3.x, edytując plik i aktualizując powyższe wartości.  Visual Studio zarządza wersjami środowiska wykonawczego automatycznie na podstawie metadanych projektu.  Jednak jest to możliwe, jeśli nigdy nie utworzono aplikacji 3.x przed tym Visual Studio nie ma jeszcze szablonów i środowiska wykonawczego dla 3.x na komputerze.  Może to przedstawić się z błędem, takich jak "nie dostępne funkcje środowiska wykonawczego, który pasuje do wersji określonej w projekcie."  Aby pobrać najnowsze szablony i środowisko uruchomieniowe, przejdź przez środowisko, aby utworzyć nowy projekt funkcji.  Po dojściu do wersji i szablonu wybierz ekran, poczekaj na visual studio, aby zakończyć pobieranie najnowszych szablonów.  Po udostępnieniu i wyświetleniu najnowszych szablonów .NET Core 3 powinieneś być w stanie uruchomić i debugować dowolny projekt skonfigurowany dla wersji 3.x.

> [!IMPORTANT]
> Funkcje w wersji 3.x można rozwijać tylko w programie Visual Studio, jeśli używasz programu Visual Studio w wersji 16.4 lub nowszej.

#### <a name="vs-code-and-azure-functions-core-tools"></a>Podstawowe narzędzia vs code i usługi Azure Functions

[Podstawowe narzędzia azure functions jest](functions-run-local.md) używany do tworzenia wiersza polecenia, a także przez rozszerzenie usługi Azure [Functions](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions) dla programu Visual Studio Code. Aby opracować wersję 3.x, zainstaluj wersję 3.x narzędzi podstawowych. Programowanie w wersji 2.x wymaga wersji 2.x narzędzi podstawowych itd. Aby uzyskać więcej informacji, zobacz [Instalowanie podstawowych narzędzi azure functions](functions-run-local.md#install-the-azure-functions-core-tools).

W przypadku tworzenia kodu programu Visual Studio może być `azureFunctions.projectRuntime` również konieczne zaktualizowanie ustawienia użytkownika, aby dopasować je do wersji zainstalowanych narzędzi.  To ustawienie aktualizuje również szablony i języki używane podczas tworzenia aplikacji funkcji.  Aby utworzyć `~3` aplikacje w `azureFunctions.projectRuntime` należy `~3`zaktualizować ustawienie użytkownika do .

![Ustawienie środowiska uruchomieniowego rozszerzenia usługi Azure Functions](./media/functions-versions/vs-code-version-runtime.png)

#### <a name="maven-and-java-apps"></a>Aplikacje Maven i Java

Aplikacje Java można migrować z wersji 2.x do 3.x, [instalując wersję 3.x podstawowych narzędzi wymaganych](functions-run-local.md#install-the-azure-functions-core-tools) do lokalnego uruchamiania.  Po sprawdzeniu, czy aplikacja działa poprawnie w wersji 3.x, zaktualizuj `POM.xml` plik aplikacji, aby zmodyfikować `FUNCTIONS_EXTENSION_VERSION` to ustawienie, `~3`tak jak w poniższym przykładzie:

```xml
<configuration>
    <resourceGroup>${functionResourceGroup}</resourceGroup>
    <appName>${functionAppName}</appName>
    <region>${functionAppRegion}</region>
    <appSettings>
        <property>
            <name>WEBSITE_RUN_FROM_PACKAGE</name>
            <value>1</value>
        </property>
        <property>
            <name>FUNCTIONS_EXTENSION_VERSION</name>
            <value>~3</value>
        </property>
    </appSettings>
</configuration>
```

## <a name="bindings"></a>Powiązania

Począwszy od wersji 2.x, środowisko wykonawcze używa nowego [modelu rozszerzalności powiązania,](https://github.com/Azure/azure-webjobs-sdk-extensions/wiki/Binding-Extensions-Overview) który oferuje następujące zalety:

* Obsługa rozszerzeń powiązań innych firm.

* Oddzielenie środowiska uruchomieniowego i powiązań. Ta zmiana umożliwia rozszerzenia wiązania, które mają być wersjonetowane i wydawane niezależnie. Można na przykład zdecydować się na uaktualnienie do wersji rozszerzenia, która opiera się na nowszej wersji podstawowego sdk.

* Środowisko wykonywania lżejsze, gdzie tylko powiązania w użyciu są znane i ładowane przez środowisko wykonawcze.

Z wyjątkiem wyzwalaczy HTTP i czasomierza wszystkie powiązania muszą być jawnie dodane do projektu aplikacji funkcji lub zarejestrowane w portalu. Aby uzyskać więcej informacji, zobacz [Rejestrowanie rozszerzeń powiązania](./functions-bindings-expressions-patterns.md).

W poniższej tabeli przedstawiono, które powiązania są obsługiwane w każdej wersji środowiska wykonawczego.

[!INCLUDE [Full bindings table](../../includes/functions-bindings.md)]

[!INCLUDE [Timeout Duration section](../../includes/functions-timeout-duration.md)]

## <a name="next-steps"></a>Następne kroki

Więcej informacji zawierają następujące zasoby:

* [Kodowanie i testowanie usługi Azure Functions lokalnie](functions-run-local.md)
* [Jak kierować reklamy na wersje środowiska uruchomieniowego usługi Azure Functions](set-runtime-version.md)
* [Informacje o wersji](https://github.com/Azure/azure-functions-host/releases)
