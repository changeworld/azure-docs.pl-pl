---
title: Konfigurowanie kontenerów
titlesuffix: Computer Vision - Cognitive Services - Azure
description: Ustawienia konfiguracji dla kontenerów w przetwarzania obrazów.
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: text-analytics
ms.topic: conceptual
ms.date: 11/14/2018
ms.author: diberry
ms.openlocfilehash: 865300f74a74b9768d2d5f5fd532ce9e851b7afb
ms.sourcegitcommit: 5d837a7557363424e0183d5f04dcb23a8ff966bb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/06/2018
ms.locfileid: "52964104"
---
# <a name="configure-containers"></a>Konfigurowanie kontenerów

Przetwarzanie obrazów dostarcza kontener rozpoznawanie tekstu za pomocą wspólną platformę konfiguracji, można łatwo skonfigurować i zarządzać ustawieniami magazynu, rejestrowania i danych telemetrycznych i zabezpieczeń dla kontenerów.

## <a name="configuration-settings"></a>Ustawienia konfiguracji

Ustawienia konfiguracji w kontenerach przetwarzania obrazów są hierarchiczne, a wszystkie kontenery używają udostępnionego hierarchii oparte na następującą strukturę najwyższego poziomu:

* [ApiKey](#apikey-configuration-setting)
* [ApplicationInsights](#applicationinsights-configuration-settings)
* [Uwierzytelnianie](#authentication-configuration-settings)
* [Rozliczenia](#billing-configuration-setting)
* [Umowa licencyjna](#eula-configuration-setting)
* [Fluentd](#fluentd-configuration-settings)
* [Rejestrowanie](#logging-configuration-settings)
* [Instaluje](#mounts-configuration-settings)

Można użyć dowolnego [zmienne środowiskowe](#configuration-settings-as-environment-variables) lub [argumenty wiersza polecenia](#configuration-settings-as-command-line-arguments) określenie ustawień konfiguracyjnych, podczas tworzenia wystąpienia kontenera z przetwarzania obrazów kontenerów.

Wartości zmiennych środowiskowych zastępują wartości argumentów wiersza polecenia, które z kolei przesłonić wartości domyślne dla obrazu kontenera. Innymi słowy Jeśli można określić różne wartości w zmiennej środowiskowej i argument wiersza polecenia dla tego samego ustawienia konfiguracji, takich jak `Logging:Disk:LogLevel`, tworzy kontener, zostanie użyta wartość zmiennej środowiskowej przez skonkretyzowanej kontener.

### <a name="configuration-settings-as-environment-variables"></a>Ustawienia konfiguracji jako zmienne środowiskowe

Możesz użyć [składni zmiennych środowiskowych platformy ASP.NET Core](https://docs.microsoft.com/aspnet/core/fundamentals/configuration/?view=aspnetcore-2.1&tabs=basicconfiguration#configuration-by-environment) określenie ustawień konfiguracyjnych.

Kontener odczytuje zmienne środowiskowe użytkownika podczas tworzenia wystąpienia kontenera. Jeśli zmienna środowiskowa istnieje, wartość zmiennej środowiskowej przesłania wartość domyślną ustawienia określonej konfiguracji. Zaletą używania zmiennych środowiskowych jest, że wiele ustawień konfiguracji można ustawić przed utworzeniem wystąpienia kontenerów, a wiele kontenerów może automatycznie używać tego samego zestawu ustawień konfiguracji.

Na przykład poniższe polecenia umożliwiają zmienną środowiskową Konfigurowanie poziom rejestrowania konsoli [LogLevel.Information](https://msdn.microsoft.com), następnie tworzy wystąpienie kontenera z obrazu kontenera rozpoznawanie tekstu. Wartość zmiennej środowiskowej zastępuje domyślne ustawienie konfiguracji.

  ```Docker
  SET Logging:Console:LogLevel=Information
  docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 containerpreview.azurecr.io/microsoft/cognitive-services-recognize-text Eula=accept Billing=https://westcentralus.api.cognitive.microsoft.com/vision/v1.0 ApiKey=0123456789
  ```

### <a name="configuration-settings-as-command-line-arguments"></a>Ustawienia konfiguracji jako argumenty wiersza polecenia

Możesz użyć [składnię argumentu wiersza polecenia platformy ASP.NET Core](https://docs.microsoft.com/aspnet/core/fundamentals/configuration/?view=aspnetcore-2.1&tabs=basicconfiguration#arguments) określenie ustawień konfiguracyjnych.

Ustawienia konfiguracji można określić opcjonalny `ARGS` parametru [platformy docker, uruchom](https://docs.docker.com/engine/reference/commandline/run/) polecenie używane do tworzenia wystąpienia kontenera za pomocą obrazu kontenera pobrany. Zaletą używania argumentów wiersza polecenia jest każdy kontener może różnych, niestandardowy zestaw ustawień konfiguracji.

Na przykład następujące polecenie tworzy wystąpienie kontenera z obrazu kontenera rozpoznawanie tekstu i konfiguruje konsoli poziom rejestrowania na LogLevel.Information, zastępowanie ustawienia konfiguracji domyślnej.

  ```Docker
  docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 containerpreview.azurecr.io/microsoft/cognitive-services-recognize-text Eula=accept Billing=https://westcentralus.api.cognitive.microsoft.com/vision/v1.0 ApiKey=0123456789 Logging:Console:LogLevel=Information
  ```

## <a name="apikey-configuration-setting"></a>Ustawienie konfiguracji ApiKey

`ApiKey` Ustawienie konfiguracji określa klucz konfiguracji zasobu przetwarzania obrazów na platformie Azure, używane do śledzenia informacji rozliczeniowych dla kontenera. Należy określić wartość dla tego ustawienia konfiguracji, a wartość musi być kluczem prawidłowej konfiguracji dla zasobu przetwarzania obrazów, określony dla [ `Billing` ](#billing-configuration-setting) ustawienia konfiguracji.

> [!IMPORTANT]
> [ `ApiKey` ](#apikey-configuration-setting), [ `Billing` ](#billing-configuration-setting), I [ `Eula` ](#eula-configuration-setting) ustawienia konfiguracji są używane razem. Ponadto należy podać prawidłowe wartości dla wszystkich trzech ich; w przeciwnym razie nie uruchamia się kontener. Aby uzyskać więcej informacji na temat tworzenia wystąpienia kontenera za pomocą tych ustawień konfiguracji, zobacz [rozliczeń](computer-vision-how-to-install-containers.md#billing).

## <a name="applicationinsights-configuration-settings"></a>Ustawienia konfiguracji dotycząca usługi Application Insights

Ustawienia konfiguracji w `ApplicationInsights` sekcji pozwala na dodawanie [usługi Azure Application Insights](https://docs.microsoft.com/azure/application-insights) obsługi telemetrii do kontenera. Usługa Application Insights zapewnia szczegółowe monitorowanie kontenera do poziomu kodu. Możesz łatwo monitorować dostępność, wydajność i użycie kontenera. Możesz również szybko identyfikować i diagnozować błędy w kontenerze bez oczekiwania na zgłoszenie ich przez użytkownika.

W poniższej tabeli opisano ustawienia konfiguracji objęte `ApplicationInsights` sekcji.

| Name (Nazwa) | Typ danych | Opis |
|------|-----------|-------------|
| `InstrumentationKey` | Ciąg | Klucz Instrumentacji wystąpienie usługi Application Insights do telemetrii, które są wysyłane dane w kontenerze. Aby uzyskać więcej informacji, zobacz [usługi Application Insights dla platformy ASP.NET Core](https://docs.microsoft.com/azure/application-insights/app-insights-asp-net-core). |

## <a name="authentication-configuration-settings"></a>Ustawienia konfiguracji uwierzytelniania

`Authentication` Ustawienia konfiguracji umożliwiają opcje zabezpieczeń platformy Azure dla kontenera. Mimo że w tej sekcji są dostępne ustawienia konfiguracji, kontener Rozpoznaj tekst nie korzysta z tej sekcji.

## <a name="billing-configuration-setting"></a>Ustawienie konfiguracji rozliczeń

`Billing` Ustawienie konfiguracji określa punkt końcowy, identyfikator URI zasobu przetwarzania obrazów na platformie Azure używane do pomiarów informacji rozliczeniowych dla kontenera. Należy określić wartość dla tego ustawienia konfiguracji, a wartość musi być prawidłowy identyfikator URI punktu końcowego zasobu przetwarzania obrazów na platformie Azure.

> [!IMPORTANT]
> [ `ApiKey` ](#apikey-configuration-setting), [ `Billing` ](#billing-configuration-setting), I [ `Eula` ](#eula-configuration-setting) ustawienia konfiguracji są używane razem. Ponadto należy podać prawidłowe wartości dla wszystkich trzech ich; w przeciwnym razie nie uruchamia się kontener. Aby uzyskać więcej informacji na temat tworzenia wystąpienia kontenera za pomocą tych ustawień konfiguracji, zobacz [rozliczeń](computer-vision-how-to-install-containers.md#billing).

## <a name="eula-configuration-setting"></a>Ustawienie konfiguracji umowy licencyjnej

`Eula` Ustawienie konfiguracji wskazuje zaakceptowane licencji dla kontenera. Należy określić wartość dla tego ustawienia konfiguracji i musi być równa wartości `accept`.

> [!IMPORTANT]
> [ `ApiKey` ](#apikey-configuration-setting), [ `Billing` ](#billing-configuration-setting), I [ `Eula` ](#eula-configuration-setting) ustawienia konfiguracji są używane razem. Ponadto należy podać prawidłowe wartości dla wszystkich trzech ich; w przeciwnym razie nie uruchamia się kontener. Aby uzyskać więcej informacji na temat tworzenia wystąpienia kontenera za pomocą tych ustawień konfiguracji, zobacz [rozliczeń](computer-vision-how-to-install-containers.md#billing).

Kontenery usługi cognitive Services są licencjonowane w ramach umowy regulującej korzystanie z platformy Azure. Jeśli nie masz istniejącej umowy regulujących korzystanie z platformy Azure, zgadzasz się, czy umowy regulującej korzystanie z platformy Azure jest Microsoft Online Subscription — Umowa (co zawiera warunki usług Online). Dla wersji zapoznawczych wyrażasz zgodę również na dodatkowym warunkom użytkowania wersji zapoznawczych platformy Microsoft. Przy użyciu kontenera jest równoznaczne z wyrażeniem zgody na te warunki.

## <a name="fluentd-configuration-settings"></a>Ustawienia konfiguracji Fluentd

`Fluentd` Sekcji zarządza ustawieniami konfiguracji dla [Fluentd](https://www.fluentd.org), moduł zbierający dane "open source" do ujednoliconego rejestrowania. Kontenery przetwarzania komputera zawiera dostawcę rejestrowania Fluentd umożliwiający kontenera do zapisywania dzienników i, opcjonalnie, metryki danych na serwerze Fluentd.

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

`Logging` Ustawienia konfiguracji zarządzania obsługę rejestrowania platformy ASP.NET Core kontenera. Można użyć tych samych ustawień konfiguracji i wartości dla kontenera, można dla aplikacji ASP.NET Core. Następujących dostawców rejestrowania są obsługiwane przez przetwarzania obrazów kontenerów:

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

Kontenery platformy Docker, dostarczone przez przetwarzania obrazów są przeznaczone do bezstanowych i niezmienne. Innymi słowy pliki utworzone w kontenerze są przechowywane na warstwie kontenerów z możliwością zapisu i będzie się powtarzać, tylko wtedy, gdy kontener jest uruchomiony i nie można łatwo uzyskać dostęp. Jeśli w danym kontenerze jest zatrzymana lub usunięta, pliki utworzone w tym kontenerze z nim zostaną zniszczone.

Jednak ponieważ są one kontenerów platformy Docker, możesz użyć opcji magazynu platformy Docker, takich jak woluminy i powiązać instaluje na odczytywanie i zapisywanie danych poza kontenerem, jeśli kontener obsługuje tę funkcję. Aby uzyskać więcej informacji o tym, jak określić i Zarządzaj opcjami magazynu platformy Docker, zobacz [zarządzania danymi na platformie Docker](https://docs.docker.com/storage/).

> [!NOTE]
> Zazwyczaj nie trzeba zmieniać wartości tych ustawień konfiguracji. Zamiast tego używane będzie wartości określone w te ustawienia konfiguracji jako miejsc docelowych, określając instaluje wejściowe i wyjściowe dla kontenera. Aby uzyskać więcej informacji na temat określania instaluje wejściowe i wyjściowe zobacz [danych wejściowych i wyjściowych instaluje](#input-and-output-mounts).

W poniższej tabeli opisano ustawienia konfiguracji objęte `Mounts` sekcji.

| Name (Nazwa) | Typ danych | Opis |
|------|-----------|-------------|
| `Input` | Ciąg | Miejsce docelowe instalacji danych wejściowych. Wartość domyślna to `/input`. |
| `Output` | Ciąg | Miejsce docelowe instalacji danych wyjściowych. Wartość domyślna to `/output`. |

### <a name="input-and-output-mounts"></a>Instaluje wejściowe i wyjściowe

Domyślnie każdy kontener może obsługiwać *wejściowych instalacji*, z którego kontener może odczytywać dane i *dane wyjściowe instalacji*, do którego kontener może zapisywać dane. Kontenery nie są wymagane do obsługi danych wejściowych lub wyjściowych instaluje, jednak i każdego kontenera służy instaluje wejściowe i wyjściowe dla celów specyficzne dla kontenera, oprócz opcji rejestrowania, obsługiwanych przez przetwarzania obrazów kontenerów.

Kontener Rozpoznaj tekst nie obsługuje danych wejściowych instaluje i opcjonalnie obsługuje dane wyjściowe instalacji.

Można określić instalacji danych wejściowych lub wyjściowych instalacji, określając `--mount` opcji [platformy docker, uruchom](https://docs.docker.com/engine/reference/commandline/run/) polecenie używane do tworzenia wystąpienia kontenera za pomocą obrazu kontenera pobrany. Domyślnie dane wejściowe instalacji używa `/input` jako miejsca docelowego i dane wyjściowe instalacji używa `/output` jako miejsca docelowego. Można określić dowolną opcję magazynu Docker dostępne na hoście kontenera platformy Docker w `--mount` opcji.

Na przykład następujące polecenie definiuje instalowania powiązania platformy Docker, aby `D:\Output` folderu na komputerze hosta jako dane wyjściowe instalacji, następnie tworzy wystąpienie kontenera z obrazu kontenera rozpoznawanie tekstu, zapisywanie plików dziennika w formacie JSON w celu instalacji danych wyjściowych.

  ```Docker
  docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 --mount type=bind,source=D:\Output,destination=/output containerpreview.azurecr.io/microsoft/cognitive-services-recognize-text Eula=accept Billing=https://westcentralus.api.cognitive.microsoft.com/vision/v1.0 ApiKey=0123456789 Logging:Disk:Format=json
  ```
