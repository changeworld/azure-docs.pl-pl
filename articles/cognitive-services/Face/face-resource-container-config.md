---
title: Konfigurowanie kontenerów
titlesuffix: Face - Cognitive Services - Azure
description: Ustawienia konfiguracji dla kontenerów.
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: text-analytics
ms.topic: conceptual
ms.date: 11/14/2018
ms.author: diberry
ms.openlocfilehash: 1009660df94eb145bbc37c27662411032bd87110
ms.sourcegitcommit: 5d837a7557363424e0183d5f04dcb23a8ff966bb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/06/2018
ms.locfileid: "52970947"
---
# <a name="configure-containers"></a>Konfigurowanie kontenerów

Kontener twarzy używane wspólną platformę konfiguracji, można łatwo skonfigurować i zarządzać ustawieniami magazynu, rejestrowania i danych telemetrycznych i zabezpieczeń dla kontenerów.

## <a name="configuration-settings"></a>Ustawienia konfiguracji

Ustawienia konfiguracji w kontenerze twarzy są hierarchiczne, a wszystkie kontenery, użyj udostępnionego hierarchii oparte na następującą strukturę najwyższego poziomu:

* [ApiKey](#apikey-configuration-setting)
* [ApplicationInsights](#applicationinsights-configuration-settings)
* [Uwierzytelnianie](#authentication-configuration-settings)
* [Rozliczenia](#billing-configuration-setting)
* [CloudAI](#cloudai-configuration-settings)
* [Umowa licencyjna](#eula-configuration-setting)
* [Fluentd](#fluentd-configuration-settings)
* [Rejestrowanie](#logging-configuration-settings)
* [Instaluje](#mounts-configuration-settings)

Można użyć dowolnego [zmienne środowiskowe](#configuration-settings-as-environment-variables) lub [argumenty wiersza polecenia](#configuration-settings-as-command-line-arguments) określenie ustawień konfiguracyjnych, podczas tworzenia wystąpienia kontenera twarzy.

Wartości zmiennych środowiskowych zastępują wartości argumentów wiersza polecenia, które z kolei przesłonić wartości domyślne dla obrazu kontenera. Innymi słowy Jeśli można określić różne wartości w zmiennej środowiskowej i argument wiersza polecenia dla tego samego ustawienia konfiguracji, takich jak `Logging:Disk:LogLevel`, tworzy kontener, zostanie użyta wartość zmiennej środowiskowej przez skonkretyzowanej kontener.

### <a name="configuration-settings-as-environment-variables"></a>Ustawienia konfiguracji jako zmienne środowiskowe

Możesz użyć [składni zmiennych środowiskowych platformy ASP.NET Core](https://docs.microsoft.com/aspnet/core/fundamentals/configuration/?view=aspnetcore-2.1&tabs=basicconfiguration#configuration-by-environment) określenie ustawień konfiguracyjnych.

Kontener odczytuje zmienne środowiskowe użytkownika podczas tworzenia wystąpienia kontenera. Jeśli zmienna środowiskowa istnieje, wartość zmiennej środowiskowej przesłania wartość domyślną ustawienia określonej konfiguracji. Zaletą używania zmiennych środowiskowych jest, że wiele ustawień konfiguracji można ustawić przed utworzeniem wystąpienia kontenerów, a wiele kontenerów może automatycznie używać tego samego zestawu ustawień konfiguracji.

Na przykład poniższe polecenia umożliwiają zmienną środowiskową Konfigurowanie poziom rejestrowania konsoli [LogLevel.Information](https://msdn.microsoft.com), następnie tworzy wystąpienie kontenera z twarzy obrazu kontenera. Wartość zmiennej środowiskowej zastępuje domyślne ustawienie konfiguracji.

  ```Docker
  SET Logging:Console:LogLevel=Information
  docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 containerpreview.azurecr.io/microsoft/cognitive-services-face Eula=accept Billing=https://westcentralus.api.cognitive.microsoft.com/face/v1.0 ApiKey=0123456789
  ```

### <a name="configuration-settings-as-command-line-arguments"></a>Ustawienia konfiguracji jako argumenty wiersza polecenia

Możesz użyć [składnię argumentu wiersza polecenia platformy ASP.NET Core](https://docs.microsoft.com/aspnet/core/fundamentals/configuration/?view=aspnetcore-2.1&tabs=basicconfiguration#arguments) określenie ustawień konfiguracyjnych.

Ustawienia konfiguracji można określić opcjonalny `ARGS` parametru [platformy docker, uruchom](https://docs.docker.com/engine/reference/commandline/run/) polecenie używane do tworzenia wystąpienia kontenera za pomocą obrazu kontenera pobrany. Zaletą używania argumentów wiersza polecenia jest każdy kontener może różnych, niestandardowy zestaw ustawień konfiguracji.

Na przykład następujące polecenie tworzy wystąpienie kontenera z obrazu kontenera twarzy i konfiguruje konsoli poziom rejestrowania na LogLevel.Information, zastępowanie ustawienia konfiguracji domyślnej.

  ```Docker
  docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 containerpreview.azurecr.io/microsoft/cognitive-services-face Eula=accept Billing=https://westcentralus.api.cognitive.microsoft.com/face/v1.0 ApiKey=0123456789 Logging:Console:LogLevel=Information
  ```

## <a name="apikey-configuration-setting"></a>Ustawienie konfiguracji ApiKey

`ApiKey` Ustawienie konfiguracji określa klucz konfiguracji zasobu twarzy na platformie Azure, używane do śledzenia informacji rozliczeniowych dla kontenera. Należy określić wartość dla tego ustawienia konfiguracji, a wartość musi być kluczem prawidłowej konfiguracji dla zasobu rozpoznawania twarzy, określony dla [ `Billing` ](#billing-configuration-setting) ustawienia konfiguracji.

> [!IMPORTANT]
> [ `ApiKey` ](#apikey-configuration-setting), [ `Billing` ](#billing-configuration-setting), I [ `Eula` ](#eula-configuration-setting) ustawienia konfiguracji są używane razem. Ponadto należy podać prawidłowe wartości dla wszystkich trzech ich; w przeciwnym razie nie uruchamia się kontener. Aby uzyskać więcej informacji na temat tworzenia wystąpienia kontenera za pomocą tych ustawień konfiguracji, zobacz [rozliczeń](face-how-to-install-containers.md#billing).

## <a name="applicationinsights-configuration-settings"></a>Ustawienia konfiguracji dotycząca usługi Application Insights

Ustawienia konfiguracji w `ApplicationInsights` sekcji pozwala na dodawanie [usługi Azure Application Insights](https://docs.microsoft.com/azure/application-insights) obsługi telemetrii do kontenera. Usługa Application Insights zapewnia szczegółowe monitorowanie kontenera do poziomu kodu. Możesz łatwo monitorować dostępność, wydajność i użycie kontenera. Możesz również szybko identyfikować i diagnozować błędy w kontenerze bez oczekiwania na zgłoszenie ich przez użytkownika.

W poniższej tabeli opisano ustawienia konfiguracji objęte `ApplicationInsights` sekcji.

| Name (Nazwa) | Typ danych | Opis |
|------|-----------|-------------|
| `InstrumentationKey` | Ciąg | Klucz Instrumentacji wystąpienie usługi Application Insights do telemetrii, które są wysyłane dane w kontenerze. Aby uzyskać więcej informacji, zobacz [usługi Application Insights dla platformy ASP.NET Core](https://docs.microsoft.com/azure/application-insights/app-insights-asp-net-core). |

## <a name="authentication-configuration-settings"></a>Ustawienia konfiguracji uwierzytelniania

`Authentication` Ustawienia konfiguracji umożliwiają opcje zabezpieczeń platformy Azure dla kontenera. Mimo że ustawienia konfiguracji w tej sekcji są dostępne, kontener twarzy nie korzysta z tej sekcji.

| Name (Nazwa) | Typ danych | Opis |
|------|-----------|-------------|
| `Storage` | Ciąg | Klucz Instrumentacji wystąpienie usługi Application Insights do telemetrii, które są wysyłane dane w kontenerze. Aby uzyskać więcej informacji, zobacz [usługi Application Insights dla platformy ASP.NET Core](https://docs.microsoft.com/azure/application-insights/app-insights-asp-net-core). |

## <a name="billing-configuration-setting"></a>Ustawienie konfiguracji rozliczeń

`Billing` Ustawienie konfiguracji określa punkt końcowy, identyfikator URI zasobu twarzy na platformie Azure używane do pomiarów informacji rozliczeniowych dla kontenera. Należy określić wartość dla tego ustawienia konfiguracji, a wartość musi być prawidłowy identyfikator URI punktu końcowego zasobu twarzy na platformie Azure.

> [!IMPORTANT]
> [ `ApiKey` ](#apikey-configuration-setting), [ `Billing` ](#billing-configuration-setting), I [ `Eula` ](#eula-configuration-setting) ustawienia konfiguracji są używane razem. Ponadto należy podać prawidłowe wartości dla wszystkich trzech ich; w przeciwnym razie nie uruchamia się kontener. Aby uzyskać więcej informacji na temat tworzenia wystąpienia kontenera za pomocą tych ustawień konfiguracji, zobacz [rozliczeń](face-how-to-install-containers.md#billing).

## <a name="cloudai-configuration-settings"></a>Ustawienia konfiguracji CloudAI

Ustawienia konfiguracji w `CloudAI` sekcji Podaj opcji specyficznych dla kontenera unikatowe dla kontenera. Następujące ustawienia i obiekty, które są obsługiwane dla kontenera twarzy w `CloudAI` sekcji

| Name (Nazwa) | Typ danych | Opis |
|------|-----------|-------------|
| `Storage` | Obiekt | Scenariusz magazynowania używane przez kontener twarzy. Aby uzyskać więcej informacji na temat scenariuszy magazynowania i skojarzone ustawienia `Storage` obiektu, zobacz [scenariuszu miejsca do magazynowania](#storage-scenario-settings) |

### <a name="storage-scenario-settings"></a>Ustawienia scenariusza magazynu

Kontener twarzy przechowuje, obiektów blob, pamięci podręcznej, metadane i dane w kolejce, w zależności od tego, co jest magazynowana. Na przykład indeksy szkolenia i wyniki dla osób dużych grup są przechowywane jako dane obiektów blob. Kontener twarzy zawiera dwa scenariusze innego magazynu podczas interakcji z i przechowywania tych typów danych:

* Memory (Pamięć)  
  Wszystkie cztery typy danych są przechowywane w pamięci. Nie są rozpowszechniane, nie są one trwałych. Jeśli kontener twarzy jest zatrzymana lub usunięta, wszystkie dane w magazynie dla tego kontenera zostanie zniszczony.  
  Jest to scenariusz magazynowania domyślnego kontenera twarzy.
* Azure  
  Kontener twarzy korzysta z usługi Azure Storage i Azure Cosmos DB, aby rozdystrybuować te cztery rodzaje danych z magazynu trwałego. Dane obiektów blob i kolejek jest obsługiwane przez usługę Azure Storage. Metadane i pamięci podręcznej danych jest obsługiwane przez usługi Azure Cosmos DB przy użyciu interfejsu API usługi MongoDB. Jeśli kontener twarzy jest zatrzymana lub usunięta, wszystkie dane w magazynie dla tego kontenera pozostaje przechowywanych w usłudze Azure Storage i Azure Cosmos DB.  
  Zasoby używane przez scenariusza magazynu platformy Azure mają następujące wymagania dodatkowe
  * Zasób usługi Azure Storage, należy użyć StorageV2 rodzaj konta
  * Zasób usługi Azure Cosmos DB, należy użyć interfejsu API usługi MongoDB

Scenariusze usługi storage i skojarzone ustawienia są zarządzane przez `Storage` obiekt w obszarze `CloudAI` sekcji konfiguracji. Następujące ustawienia konfiguracji są dostępne w `Storage` obiektu:

| Name (Nazwa) | Typ danych | Opis |
|------|-----------|-------------|
| `StorageScenario` | Ciąg | Scenariusz magazynowania obsługiwane przez kontener. Dostępne są następujące wartości<br/>`Memory` — Wartość domyślna. Kontener korzysta z magazynu nietrwałe, nierozpowszechniony i w pamięci, do użycia z jednym węzłem, tymczasowy. Jeśli kontener jest zatrzymana lub usunięta, jest niszczony magazynu dla tego kontenera.<br/>`Azure` -Container używa zasobów platformy Azure do przechowywania. Jeśli kontener jest zatrzymana lub usunięta, jest trwały magazyn dla tego kontenera.|
| `ConnectionStringOfAzureStorage` | Ciąg | Parametry połączenia dla zasobu usługi Azure Storage używany przez kontener.<br/>To ustawienie ma zastosowanie tylko wtedy, gdy `Azure` jest określona dla `StorageScenario` ustawienia konfiguracji. |
| `ConnectionStringOfCosmosMongo` | Ciąg | Parametry połączenia bazy danych MongoDB dla zasobu usługi Azure Cosmos DB używane przez kontener.<br/>To ustawienie ma zastosowanie tylko wtedy, gdy `Azure` jest określona dla `StorageScenario` ustawienia konfiguracji. |

Na przykład następujące polecenie Określa scenariusza magazynu platformy Azure i zawiera przykładowe parametry połączenia dla zasobów usługi Azure Storage i Cosmos DB używane do przechowywania danych dla kontenera twarzy.

  ```Docker
  docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 containerpreview.azurecr.io/microsoft/cognitive-services-face Eula=accept Billing=https://westcentralus.api.cognitive.microsoft.com/face/v1.0 ApiKey=0123456789 CloudAI:Storage:StorageScenario=Azure CloudAI:Storage:ConnectionStringOfCosmosMongo="mongodb://samplecosmosdb:0123456789@samplecosmosdb.documents.azure.com:10255/?ssl=true&replicaSet=globaldb" CloudAI:Storage:ConnectionStringOfAzureStorage="DefaultEndpointsProtocol=https;AccountName=sampleazurestorage;AccountKey=0123456789;EndpointSuffix=core.windows.net"
  ```

Scenariusz magazynowania są obsługiwane niezależnie od instalacji i wyjściowych instaluje. Można określić kombinacji tych funkcji do jednego kontenera. Na przykład następujące polecenie definiuje instalowania powiązania platformy Docker, aby `D:\Output` folderu na komputerze hosta jako dane wyjściowe instalacji, następnie tworzy wystąpienie kontenera z twarzy obraz kontenera, zapisywanie plików dziennika w formacie JSON w celu instalacji danych wyjściowych. Polecenie również określa scenariusza magazynu platformy Azure i zawiera przykładowe parametry połączenia dla zasobów usługi Azure Storage i Cosmos DB używane do przechowywania danych dla kontenera twarzy.

  ```Docker
  docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 --mount type=bind,source=D:\Output,destination=/output containerpreview.azurecr.io/microsoft/cognitive-services-face Eula=accept Billing=https://westcentralus.api.cognitive.microsoft.com/face/v1.0 ApiKey=0123456789 Logging:Disk:Format=json CloudAI:Storage:StorageScenario=Azure CloudAI:Storage:ConnectionStringOfCosmosMongo="mongodb://samplecosmosdb:0123456789@samplecosmosdb.documents.azure.com:10255/?ssl=true&replicaSet=globaldb" CloudAI:Storage:ConnectionStringOfAzureStorage="DefaultEndpointsProtocol=https;AccountName=sampleazurestorage;AccountKey=0123456789;EndpointSuffix=core.windows.net"
  ```

## <a name="eula-configuration-setting"></a>Ustawienie konfiguracji umowy licencyjnej

`Eula` Ustawienie konfiguracji wskazuje zaakceptowane licencji dla kontenera. Należy określić wartość dla tego ustawienia konfiguracji i musi być równa wartości `accept`.

> [!IMPORTANT]
> [ `ApiKey` ](#apikey-configuration-setting), [ `Billing` ](#billing-configuration-setting), I [ `Eula` ](#eula-configuration-setting) ustawienia konfiguracji są używane razem. Ponadto należy podać prawidłowe wartości dla wszystkich trzech ich; w przeciwnym razie nie uruchamia się kontener. Aby uzyskać więcej informacji na temat tworzenia wystąpienia kontenera za pomocą tych ustawień konfiguracji, zobacz [rozliczeń](face-how-to-install-containers.md#billing).

Kontenery usługi cognitive Services są licencjonowane w ramach umowy regulującej korzystanie z platformy Azure. Jeśli nie masz istniejącej umowy regulujących korzystanie z platformy Azure, zgadzasz się, czy umowy regulującej korzystanie z platformy Azure jest Microsoft Online Subscription — Umowa (co zawiera warunki usług Online). Dla wersji zapoznawczych wyrażasz zgodę również na dodatkowym warunkom użytkowania wersji zapoznawczych platformy Microsoft. Przy użyciu kontenera jest równoznaczne z wyrażeniem zgody na te warunki.

## <a name="fluentd-configuration-settings"></a>Ustawienia konfiguracji Fluentd

`Fluentd` Sekcji zarządza ustawieniami konfiguracji dla [Fluentd](https://www.fluentd.org), moduł zbierający dane "open source" do ujednoliconego rejestrowania. kontener twarzy zawiera dostawcę rejestrowania Fluentd umożliwiający kontenera do zapisywania dzienników i, opcjonalnie, metryki danych na serwerze Fluentd.

W poniższej tabeli opisano ustawienia konfiguracji objęte `Fluentd` sekcji.

| Name (Nazwa) | Typ danych | Opis |
|------|-----------|-------------|
| `Host` | Ciąg | Adres IP lub nazwa hosta DNS serwera Fluentd. |
| `Port` | Liczba całkowita | Port serwera Fluentd.<br/> Wartość domyślna to 24224. |
| `HeartbeatMs` | Liczba całkowita | Interwał pulsu (w milisekundach). Jeśli żaden ruch nie jest zdarzenie zostało wysłane przed upływem tego interwału, pulsu są wysyłane do serwera Fluentd. Wartość domyślna to 60000 milisekund (1 minuta). |
| `SendBufferSize` | Liczba całkowita | Sieć miejsca w buforze, w bajtach, przydzielany dla operacji wysyłania. Wartość domyślna to 32768 bajtów (32 kilobajtów). |
| `TlsConnectionEstablishmentTimeoutMs` | Liczba całkowita | Limit czasu w milisekundach, można ustanowić połączenia SSL/TLS z serwerem Fluentd. Wartość domyślna to 10000 MS (10 sekund).<br/> Jeśli `UseTLS` jest ustawiona na wartość FAŁSZ, ta wartość jest ignorowana. |
| `UseTLS` | Wartość logiczna | Wskazuje, czy do komunikacji z serwerem Fluentd kontenera należy używać protokołów SSL/TLS. Wartość domyślna to false. |

## <a name="logging-configuration-settings"></a>Ustawienia konfiguracji rejestrowania

`Logging` Ustawienia konfiguracji zarządzania obsługę rejestrowania platformy ASP.NET Core kontenera. Można użyć tych samych ustawień konfiguracji i wartości dla kontenera, można dla aplikacji ASP.NET Core. Następujących dostawców rejestrowania są obsługiwane przez kontener twarzy:

* [Console](https://docs.microsoft.com/aspnet/core/fundamentals/logging/?view=aspnetcore-2.1#console-provider)  
  ASP.NET Core `Console` dostawcy logowania. Obsługiwane są wszystkie ustawienia konfiguracji platformy ASP.NET Core i wartości domyślne dla tego dostawcy logowania.
* [Debugowanie](https://docs.microsoft.com/aspnet/core/fundamentals/logging/?view=aspnetcore-2.1#debug-provider)  
  ASP.NET Core `Debug` dostawcy logowania. Obsługiwane są wszystkie ustawienia konfiguracji platformy ASP.NET Core i wartości domyślne dla tego dostawcy logowania.
* Dysk  
  Dostawca logowania JSON. Ten dostawca rejestrowania zapisuje dane dziennika instalacji danych wyjściowych.  
  `Disk` Rejestrowania dostawca obsługuje następujące ustawienia konfiguracji:  

  | Name (Nazwa) | Typ danych | Opis |
  |------|-----------|-------------|
  | `Format` | Ciąg | Format danych wyjściowych dla plików dziennika.<br/> **Uwaga:** ta wartość musi być równa `json` do włączenia rejestrowania dostawcy usług. Jeśli ta wartość jest określona bez jednoczesnego określenia dane wyjściowe instalacji podczas tworzenia wystąpienia kontenera, wystąpi błąd. |
  | `MaxFileSize` | Liczba całkowita | Maksymalny rozmiar w megabajtach (MB), plik dziennika. Gdy rozmiar bieżącego pliku dziennika, spełnia lub przekracza tę wartość, nowy plik dziennika został uruchomiony przez dostawcę rejestrowania. Jeśli określono wartość -1, rozmiar pliku dziennika jest ograniczona tylko przez maksymalny rozmiar pliku, ewentualne instalacji danych wyjściowych. Wartość domyślna to 1. |

Aby uzyskać więcej informacji na temat konfigurowania obsługi rejestrowania platformy ASP.NET Core, zobacz [ustawień pliku konfiguracji](https://docs.microsoft.com/aspnet/core/fundamentals/logging/?view=aspnetcore-2.1#settings-file-configuration).

## <a name="mounts-configuration-settings"></a>Instaluje ustawień konfiguracji

Kontenery platformy Docker, dostarczone przez twarzy są przeznaczone do bezstanowych i niezmienne. Innymi słowy pliki utworzone w kontenerze są przechowywane na warstwie kontenerów z możliwością zapisu i będzie się powtarzać, tylko wtedy, gdy kontener jest uruchomiony i nie można łatwo uzyskać dostęp. Jeśli w danym kontenerze jest zatrzymana lub usunięta, pliki utworzone w tym kontenerze z nim zostaną zniszczone.

Jednak ponieważ są one kontenerów platformy Docker, możesz użyć opcji magazynu platformy Docker, takich jak woluminy i powiązać instaluje na odczytywanie i zapisywanie danych poza kontenerem, jeśli kontener obsługuje tę funkcję. Aby uzyskać więcej informacji o tym, jak określić i Zarządzaj opcjami magazynu platformy Docker, zobacz [zarządzania danymi na platformie Docker](https://docs.docker.com/storage/).

> [!NOTE]
> Zazwyczaj nie trzeba zmieniać wartości tych ustawień konfiguracji. Zamiast tego używane będzie wartości określone w te ustawienia konfiguracji jako miejsc docelowych, określając instaluje wejściowe i wyjściowe dla kontenera. Aby uzyskać więcej informacji na temat określania instaluje wejściowe i wyjściowe zobacz [danych wejściowych i wyjściowych instaluje](#input-and-output-mounts).

W poniższej tabeli opisano ustawienia konfiguracji objęte `Mounts` sekcji.

| Name (Nazwa) | Typ danych | Opis |
|------|-----------|-------------|
| `Input` | Ciąg | Miejsce docelowe instalacji danych wejściowych. Wartość domyślna to `/input`. |
| `Output` | Ciąg | Miejsce docelowe instalacji danych wyjściowych. Wartość domyślna to `/output`. |

### <a name="input-and-output-mounts"></a>Instaluje wejściowe i wyjściowe

Domyślnie każdy kontener może obsługiwać *wejściowych instalacji*, z którego kontener może odczytywać dane i *dane wyjściowe instalacji*, do którego kontener może zapisywać dane. Kontenery nie są wymagane do obsługi danych wejściowych lub wyjściowych instaluje, jednak i każdego kontenera służy instaluje wejściowe i wyjściowe dla celów specyficzne dla kontenera, oprócz opcji rejestrowania, obsługiwane przez kontener twarzy.

Kontener twarzy nie obsługuje danych wejściowych instaluje i opcjonalnie obsługuje dane wyjściowe instalacji.

Można określić instalacji danych wejściowych lub wyjściowych instalacji, określając `--mount` opcji [platformy docker, uruchom](https://docs.docker.com/engine/reference/commandline/run/) polecenie używane do tworzenia wystąpienia kontenera za pomocą obrazu kontenera pobrany. Domyślnie dane wejściowe instalacji używa `/input` jako miejsca docelowego i dane wyjściowe instalacji używa `/output` jako miejsca docelowego. Można określić dowolną opcję magazynu Docker dostępne na hoście kontenera platformy Docker w `--mount` opcji.

Na przykład następujące polecenie definiuje instalowania powiązania platformy Docker, aby `D:\Output` folderu na komputerze hosta jako dane wyjściowe instalacji, następnie tworzy wystąpienie kontenera z twarzy obraz kontenera, zapisywanie plików dziennika w formacie JSON w celu instalacji danych wyjściowych.

  ```Docker
  docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 --mount type=bind,source=D:\Output,destination=/output containerpreview.azurecr.io/microsoft/cognitive-services-face Eula=accept Billing=https://westcentralus.api.cognitive.microsoft.com/face/v1.0 ApiKey=0123456789 Logging:Disk:Format=json
  ```

Kontener twarzy nie korzysta z danych wejściowych lub danych wyjściowych instaluje do przechowywania danych szkoleniowych lub bazy danych. Zamiast tego kontenera twarzy zawiera scenariusze usługi storage zarządzania danych szkoleniowych i bazy danych. Aby uzyskać więcej informacji o używaniu scenariusze usługi storage, zobacz [ustawień scenariusza magazynu](#storage-scenario-settings).
