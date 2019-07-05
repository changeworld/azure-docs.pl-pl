---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 04/14/2019
ms.author: glenga
ms.openlocfilehash: e319356d555f26354ea29dc7be068bf6168abb17
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/28/2019
ms.locfileid: "67455146"
---
## <a name="local-settings-file"></a>Plik ustawień lokalnych

Local.settings.json pliku przechowuje ustawienia aplikacji, parametry połączenia i ustawień używanych przez narzędzia programistyczne lokalnego. Ustawienia w pliku local.settings.json są używane tylko w sytuacji, gdy uruchamiane lokalnie. Plik ustawień lokalnych ma następującą strukturę:

```json
{
  "IsEncrypted": false,
  "Values": {
    "FUNCTIONS_WORKER_RUNTIME": "<language worker>",
    "AzureWebJobsStorage": "<connection-string>",
    "AzureWebJobsDashboard": "<connection-string>",
    "MyBindingConnection": "<binding-connection-string>"
  },
  "Host": {
    "LocalHttpPort": 7071,
    "CORS": "*",
    "CORSCredentials": false
  },
  "ConnectionStrings": {
    "SQLConnectionString": "<sqlclient-connection-string>"
  }
}
```

Podczas uruchamiania lokalnego, obsługiwane są następujące ustawienia:

| Ustawienie      | Opis                            |
| ------------ | -------------------------------------- |
| **`IsEncrypted`** | Po ustawieniu `true`, wszystkie wartości są szyfrowane za pomocą klucza komputera lokalnego. Używane z `func settings` poleceń. Wartość domyślna to `false`. |
| **`Values`** | Tablica ustawień aplikacji i parametry połączenia używane podczas uruchamiania lokalnego. Te pary klucz wartość (ciąg ciąg) odnoszą się do ustawień aplikacji w aplikacji funkcji na platformie Azure, takich jak [ `AzureWebJobsStorage` ]. Wiele wyzwalaczy i powiązań ma właściwość, która odwołuje się do aplikacji ustawienie parametrów połączenia, takich jak `Connection` dla [wyzwalacz usługi Blob storage](../articles/azure-functions/functions-bindings-storage-blob.md#trigger---configuration). W przypadku takich właściwości potrzebne ustawienie aplikacji zdefiniowane w `Values` tablicy. <br/>[`AzureWebJobsStorage`] Wymagana aplikacja jest ustawienie wyzwalaczy innych niż HTTP. <br/>W wersji 2.x środowisko uruchomieniowe funkcji wymaga [`FUNCTIONS_WORKER_RUNTIME`] ustawienie, które jest generowany dla projektu, podstawowe narzędzia. <br/> Jeśli masz [emulatora usługi Azure storage](../articles/storage/common/storage-use-emulator.md) zainstalowane lokalnie, możesz ustawić [ `AzureWebJobsStorage` ] do `UseDevelopmentStorage=true` i podstawowe narzędzia używa emulatora. Jest to przydatne podczas tworzenia aplikacji, ale należy przetestować za pomocą połączenia rzeczywisty magazyn, przed przystąpieniem do wdrożenia.<br/> Wartości muszą być ciągi znaków i nie obiektami JSON lub tablicami. Nazwa ustawienia nie może zawierać dwukropka (`:`) lub podwójne podkreślenie (`__`); są one zarezerwowane w czasie wykonywania.  |
| **`Host`** | Ustawienia w tej sekcji dostosować proces hosta funkcji podczas uruchamiania lokalnego. Są one niezależne od ustawień host.json, które mają zastosowanie również w przypadku uruchamiania na platformie Azure. |
| **`LocalHttpPort`** | Ustawia domyślny port używany podczas uruchamiania lokalnego hosta funkcji (`func host start` i `func run`). `--port` Opcji wiersza polecenia mają pierwszeństwo przed tę wartość. |
| **`CORS`** | Określa pochodzenia, które mogą uzyskać [współużytkowanie zasobów między źródłami (cors)](https://en.wikipedia.org/wiki/Cross-origin_resource_sharing). Źródła są dostarczane jako listę rozdzielaną przecinkami, bez spacji. Wartość symbolu wieloznacznego (\*) jest obsługiwany, co pozwala żądań z dowolnego źródła. |
| **`CORSCredentials`** |  Ustaw wartość "true", aby umożliwić `withCredentials` żądań. |
| **`ConnectionStrings`** | Nie używaj tej kolekcji parametry połączenia używane przez usługi powiązania funkcji. Ta kolekcja jest używana tylko przez struktur, które zazwyczaj pobierają parametry połączenia z `ConnectionStrings` pliku sekcji konfiguracji, takich jak [Entity Framework](https://msdn.microsoft.com/library/aa937723(v=vs.113).aspx). Parametry połączenia, w tym obiekcie są dodawane do środowiska z typem dostawcy [System.Data.SqlClient](https://msdn.microsoft.com/library/system.data.sqlclient(v=vs.110).aspx). Elementy w tej kolekcji nie są publikowane na platformie Azure z innymi ustawieniami aplikacji. Należy jawnie dodać tych wartości, aby `Connection strings` zbiór ustawień aplikacji funkcji. Jeśli tworzysz [ `SqlConnection` ](https://msdn.microsoft.com/library/system.data.sqlclient.sqlconnection(v=vs.110).aspx) w kodzie funkcji należy przechowywać wartość parametrów połączenia w **ustawienia aplikacji** w portalu przy użyciu innych połączeń. |

[`AzureWebJobsStorage`]: ../articles/azure-functions/functions-app-settings.md#azurewebjobsstorage
