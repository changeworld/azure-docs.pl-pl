---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 04/14/2019
ms.author: glenga
ms.openlocfilehash: 1c2196f1f834002b76dbea555b54a5162655ec1c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77205707"
---
## <a name="local-settings-file"></a>Plik ustawień lokalnych

Plik local.settings.json przechowuje ustawienia aplikacji, parametry połączenia i ustawienia używane przez lokalne narzędzia programistyczne. Ustawienia w pliku local.settings.json są używane tylko podczas uruchamiania projektów lokalnie. Plik ustawień lokalnych ma tę strukturę:

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

Te ustawienia są obsługiwane podczas uruchamiania projektów lokalnie:

| Ustawienie      | Opis                            |
| ------------ | -------------------------------------- |
| **`IsEncrypted`** | Gdy to ustawienie `true`jest ustawione na , wszystkie wartości są szyfrowane za pomocą lokalnego klucza komputera. Używany `func settings` z poleceniami. Wartością `false`domyślną jest . |
| **`Values`** | Tablica ustawień aplikacji i parametry połączenia używane, gdy projekt jest uruchomiony lokalnie. Te pary klucz-wartość (ciąg-ciąg) odpowiadają ustawieniami aplikacji w [`AzureWebJobsStorage`]aplikacji funkcji na platformie Azure, takimi jak . Wiele wyzwalaczy i powiązań ma właściwość, która odwołuje `Connection` się do ustawienia aplikacji ciągu połączenia, na przykład dla [wyzwalacza magazynu obiektów Blob.](../articles/azure-functions/functions-bindings-storage-blob-trigger.md#configuration) W przypadku tych właściwości potrzebne jest ustawienie `Values` aplikacji zdefiniowane w tablicy. <br/>[`AzureWebJobsStorage`]jest wymaganym ustawieniem aplikacji dla wyzwalaczy innych niż HTTP. <br/>Wersja 2.x i wyższa środowiska wykonawczego`FUNCTIONS_WORKER_RUNTIME`funkcji wymaga [ ] ustawienie, które jest generowane dla projektu przez narzędzia podstawowe. <br/> Jeśli [emulator magazynu platformy Azure](../articles/storage/common/storage-use-emulator.md) jest zainstalowany [`AzureWebJobsStorage`] `UseDevelopmentStorage=true`lokalnie i ustawiono na program , Narzędzia podstawowe używają emulatora. Emulator jest przydatne podczas tworzenia, ale należy przetestować z rzeczywistego połączenia magazynu przed wdrożeniem.<br/> Wartości muszą być ciągami znaków, a nie obiektami ani tablicami JSON. Nazwy ustawień nie mogą zawierać`:`dwukropka`__`( ) lub podwójnego podkreślenia ( ). Te znaki są zarezerwowane przez środowisko wykonawcze.  |
| **`Host`** | Ustawienia w tej sekcji dostosować proces hosta funkcji podczas uruchamiania projektów lokalnie. Te ustawienia są niezależne od ustawień host.json, które mają również zastosowanie podczas uruchamiania projektów na platformie Azure. |
| **`LocalHttpPort`** | Ustawia domyślny port używany podczas uruchamiania lokalnego hosta funkcji (`func host start` i `func run`). Opcja `--port` wiersza polecenia ma pierwszeństwo przed tym ustawieniem. |
| **`CORS`** | Definiuje źródła dozwolone dla [współużytkowego udostępniania zasobów (CORS)](https://en.wikipedia.org/wiki/Cross-origin_resource_sharing). Początki są dostarczane jako lista oddzielona przecinkami bez spacji. Wartość symbolu\*wieloznacznego ( ) jest obsługiwana, co pozwala na żądania z dowolnego źródła. |
| **`CORSCredentials`** |  Po ustawieniu `true` `withCredentials` na , zezwala na żądania. |
| **`ConnectionStrings`** | Kolekcja. Nie należy używać tej kolekcji dla ciągów połączeń używanych przez powiązania funkcji. Ta kolekcja jest używana tylko przez struktury, które `ConnectionStrings` zazwyczaj otrzymują parametry połączenia z sekcji pliku konfiguracji, takie jak [Entity Framework](https://msdn.microsoft.com/library/aa937723(v=vs.113).aspx). Parametry połączenia w tym obiekcie są dodawane do środowiska za pomocą typu dostawcy [System.Data.SqlClient](https://msdn.microsoft.com/library/system.data.sqlclient(v=vs.110).aspx). Elementy w tej kolekcji nie są publikowane na platformie Azure z innymi ustawieniami aplikacji. Należy jawnie dodać te `Connection strings` wartości do kolekcji ustawień aplikacji funkcji. Jeśli tworzysz [`SqlConnection`](https://msdn.microsoft.com/library/system.data.sqlclient.sqlconnection(v=vs.110).aspx) w kodzie funkcji, należy przechowywać wartość ciągu połączenia z innymi połączeniami w **ustawieniach aplikacji** w portalu. |

['AzureWebJobsStorage']: ../articles/azure-functions/functions-app-settings.md#azurewebjobsstorage
