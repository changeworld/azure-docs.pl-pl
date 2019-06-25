---
author: diberry
ms.author: diberry
ms.service: cognitive-services
ms.topic: include
ms.date: 04/02/2019
ms.openlocfilehash: d1c880ddc90ae3ce18dfde7e1983b45ac239de85
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/18/2019
ms.locfileid: "67183502"
---
`Logging` Ustawienia zarządzania obsługę rejestrowania platformy ASP.NET Core kontenera. Można użyć tych samych ustawień konfiguracji oraz wartości kontenera używaną dla aplikacji ASP.NET Core. 

Następujących dostawców rejestrowania są obsługiwane przez kontener:

|Dostawca|Przeznaczenie|
|--|--|
|[Console](https://docs.microsoft.com/aspnet/core/fundamentals/logging/?view=aspnetcore-2.1#console-provider)|ASP.NET Core `Console` dostawcy logowania. Obsługiwane są wszystkie ustawienia konfiguracji platformy ASP.NET Core i wartości domyślne dla tego dostawcy logowania.|
|[Debugowanie](https://docs.microsoft.com/aspnet/core/fundamentals/logging/?view=aspnetcore-2.1#debug-provider)|ASP.NET Core `Debug` dostawcy logowania. Obsługiwane są wszystkie ustawienia konfiguracji platformy ASP.NET Core i wartości domyślne dla tego dostawcy logowania.|
|[Dysk](#disk-logging)|Dostawca logowania JSON. Ten dostawca rejestrowania zapisuje dane dziennika instalacji danych wyjściowych.|

To polecenie kontenera przechowuje informacje o rejestrowaniu w formacie JSON w celu instalacji dane wyjściowe:

```bash
docker run --rm -it -p 5000:5000 \
--memory 2g --cpus 1 \
--mount type=bind,src=/home/azureuser/output,target=/output \
<registry-location>/<image-name> \
Eula=accept \
Billing=<billing-endpoint> \
ApiKey=<api-key> \
Logging:Disk:Format=json
```

Ten kontener polecenie wyświetla informacje o debugowaniu, poprzedzony znakiem `dbug`, podczas gdy kontener jest uruchomiony:

```bash
docker run --rm -it -p 5000:5000 \
--memory 2g --cpus 1 \
<registry-location>/<image-name> \
Eula=accept \
Billing=<billing-endpoint> \
ApiKey=<api-key> \
Logging:Console:LogLevel:Default=Debug
```

### <a name="disk-logging"></a>Rejestrowanie dysku

`Disk` Rejestrowania dostawca obsługuje następujące ustawienia konfiguracji:  

| Name (Nazwa) | Typ danych | Opis |
|------|-----------|-------------|
| `Format` | Ciąg | Format danych wyjściowych dla plików dziennika.<br/> **Uwaga:** Ta wartość musi być równa `json` do włączenia rejestrowania dostawcy usług. Jeśli ta wartość jest określona bez jednoczesnego określenia dane wyjściowe instalacji podczas tworzenia wystąpienia kontenera, wystąpi błąd. |
| `MaxFileSize` | Liczba całkowita | Maksymalny rozmiar w megabajtach (MB), plik dziennika. Gdy rozmiar bieżącego pliku dziennika, spełnia lub przekracza tę wartość, nowy plik dziennika został uruchomiony przez dostawcę rejestrowania. Jeśli określono wartość -1, rozmiar pliku dziennika jest ograniczona tylko przez maksymalny rozmiar pliku, ewentualne instalacji danych wyjściowych. Wartość domyślna to 1. |

Aby uzyskać więcej informacji na temat konfigurowania obsługi rejestrowania platformy ASP.NET Core, zobacz [ustawień pliku konfiguracji](https://docs.microsoft.com/aspnet/core/fundamentals/logging/?view=aspnetcore-2.1).

