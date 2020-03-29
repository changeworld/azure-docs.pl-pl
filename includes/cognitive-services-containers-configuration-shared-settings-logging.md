---
author: IEvangelist
ms.author: dapine
ms.date: 06/25/2019
ms.service: cognitive-services
ms.topic: include
ms.openlocfilehash: 873fd8cbc211f098c93b8fb3fbe701e4a34d8487
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "68320513"
---
Ustawienia `Logging` zarządzają ASP.NET obsługą rejestrowania core dla kontenera. Można użyć tych samych ustawień konfiguracji i wartości dla kontenera, który jest używany dla aplikacji ASP.NET Core. 

Kontener obsługuje następujących dostawców rejestrowania:

|Dostawca|Przeznaczenie|
|--|--|
|[Console](https://docs.microsoft.com/aspnet/core/fundamentals/logging/?view=aspnetcore-2.1#console-provider)|Dostawca rejestrowania core `Console` ASP.NET. Obsługiwane są wszystkie ASP.NET podstawowe ustawienia konfiguracji i wartości domyślne dla tego dostawcy rejestrowania.|
|[Debugowania](https://docs.microsoft.com/aspnet/core/fundamentals/logging/?view=aspnetcore-2.1#debug-provider)|Dostawca rejestrowania core `Debug` ASP.NET. Obsługiwane są wszystkie ASP.NET podstawowe ustawienia konfiguracji i wartości domyślne dla tego dostawcy rejestrowania.|
|[Disk](#disk-logging)|Dostawca rejestrowania JSON. Ten dostawca rejestrowania zapisuje dane dziennika do instalacji wyjściowej.|

To polecenie kontenera przechowuje informacje rejestrowania w formacie JSON do instalacji wyjściowej:

```bash
docker run --rm -it -p 5000:5000 \
--memory 2g --cpus 1 \
--mount type=bind,src=/home/azureuser/output,target=/output \
<registry-location>/<image-name> \
Eula=accept \
Billing=<endpoint> \
ApiKey=<api-key> \
Logging:Disk:Format=json
```

To polecenie kontenera pokazuje informacje debugowania, poprzedzone `dbug`, gdy kontener jest uruchomiony:

```bash
docker run --rm -it -p 5000:5000 \
--memory 2g --cpus 1 \
<registry-location>/<image-name> \
Eula=accept \
Billing=<endpoint> \
ApiKey=<api-key> \
Logging:Console:LogLevel:Default=Debug
```

### <a name="disk-logging"></a>Rejestrowanie dysku

Dostawca `Disk` rejestrowania obsługuje następujące ustawienia konfiguracji:

| Nazwa | Typ danych | Opis |
|------|-----------|-------------|
| `Format` | Ciąg | Format wyjściowy dla plików dziennika.<br/> **Uwaga:** Ta wartość musi `json` być ustawiona, aby włączyć dostawcę rejestrowania. Jeśli ta wartość jest określona bez określania również mount danych wyjściowych podczas tworzenia wystąpienia kontenera, występuje błąd. |
| `MaxFileSize` | Liczba całkowita | Maksymalny rozmiar pliku dziennika w megabajtach (MB). Gdy rozmiar bieżącego pliku dziennika spełnia lub przekracza tę wartość, nowy plik dziennika jest uruchamiany przez dostawcę rejestrowania. Jeśli określono wartość -1, rozmiar pliku dziennika jest ograniczony tylko przez maksymalny rozmiar pliku, jeśli istnieje, dla instalacji wyjściowej. Wartość domyślna to 1. |

Aby uzyskać więcej informacji na temat konfigurowania obsługi rejestrowania ASP.NET Core, zobacz [Ustawienia konfiguracji pliku](https://docs.microsoft.com/aspnet/core/fundamentals/logging/?view=aspnetcore-2.1).

