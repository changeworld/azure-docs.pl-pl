---
title: Ustawienia kontenera platformy docker Language Understanding (LUIS)
titleSuffix: Azure Cognitive Services
description: Środowisko uruchomieniowe kontenera usługi LUIS jest konfigurowana przy użyciu `docker run` argumenty polecenia. Usługa LUIS ma wymagane ustawienia, wraz z kilku ustawień opcjonalnych.
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: conceptual
ms.date: 12/04/2018
ms.author: diberry
ms.openlocfilehash: 020c623f881dd806cbc42b72596a2cc87e29045b
ms.sourcegitcommit: 5d837a7557363424e0183d5f04dcb23a8ff966bb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/06/2018
ms.locfileid: "52965057"
---
# <a name="configure-containers"></a>Konfigurowanie kontenerów

Środowisko uruchomieniowe kontenera Language Understanding (LUIS) jest skonfigurowany przy użyciu `docker run` argumenty polecenia. Usługa LUIS ma wymagane ustawienia, wraz z kilku ustawień opcjonalnych. Kilka [przykłady](#example-docker-run-commands) polecenia są dostępne. Ustawienia specyficzne dla kontenera są dane wejściowe [ustawienia instalacji](#mount-settings) i ustawienia rozliczeń. 

Ustawienia kontenera są [hierarchiczne](#hierarchical-settings) i można ustawić za pomocą [zmienne środowiskowe](#environment-variable-settings) lub rozwiązania docker [argumenty wiersza polecenia](#command-line-argument-settings).

## <a name="configuration-settings"></a>Ustawienia konfiguracji

Ten kontener ma następujące ustawienia konfiguracji:

|Wymagane|Ustawienie|Przeznaczenie|
|--|--|--|
|Yes|[ApiKey](#apikey-setting)|Używane do śledzenia informacji dotyczących rozliczeń.|
|Nie|[ApplicationInsights](#applicationinsights-setting)|Umożliwia dodanie [usługi Azure Application Insights](https://docs.microsoft.com/azure/application-insights) obsługi telemetrii do kontenera.|
|Yes|[Rozliczenia](#billing-setting)|Określa identyfikator URI punktu końcowego z _Language Understanding_ zasobów na platformie Azure.|
|Yes|[Umowa licencyjna](#eula-setting)| Wskazuje, zaakceptowane licencji dla kontenera.|
|Nie|[Fluentd](#fluentd-settings)|Zapisać dziennik i, opcjonalnie, metryki danych na serwerze Fluentd.|
|Nie|[Rejestrowanie](#logging-settings)|Udostępnia platformy ASP.NET Core rejestrowania pomocy technicznej dla usługi kontenera. |
|Yes|[Instaluje](#mount-settings)|Odczytywanie i zapisywanie danych z [komputerze-hoście](luis-container-howto.md#the-host-computer) do kontenera i z kontenera do komputera-hosta.|

> [!IMPORTANT]
> [ `ApiKey` ](#apikey-setting), [ `Billing` ](#billing-setting), I [ `Eula` ](#eula-setting) ustawienia są używane razem. Ponadto należy podać prawidłowe wartości dla wszystkich trzech ich; w przeciwnym razie Nie można uruchomić kontener. Aby uzyskać więcej informacji na temat tworzenia wystąpienia kontenera za pomocą tych ustawień konfiguracji, zobacz [rozliczeń](luis-container-howto.md#billing).

## <a name="apikey-setting"></a>Ustawienie ApiKey

`ApiKey` Ustawienie umożliwia określenie klucza zasobów platformy Azure używane do śledzenia informacji rozliczeniowych dla kontenera. Należy określić wartość dla ApiKey i wartość musi być prawidłowy klucz dla _Language Understanding_ zasób określony dla [ `Billing` ](#billing-setting) ustawienia konfiguracji.

To ustawienie można znaleźć w dwóch miejscach:

* Witryna Azure portal: **Language Understanding** zarządzanie zasobami w obszarze **kluczy**
* Portal usługi LUIS: **kluczy i punktu końcowego ustawienia** strony. 

Nie należy używać klucza starter lub tworzenia klucza. 

## <a name="applicationinsights-setting"></a>Ustawienie dotycząca usługi Application Insights

`ApplicationInsights` Ustawienie pozwala na dodawanie [usługi Azure Application Insights](https://docs.microsoft.com/azure/application-insights) obsługi telemetrii do kontenera. Usługa Application Insights zapewnia szczegółowe monitorowanie kontenera. Możesz łatwo monitorować dostępność, wydajność i użycie kontenera. Możesz również szybko identyfikować i diagnozować błędy w kontenerze.

W poniższej tabeli opisano ustawienia konfiguracji objęte `ApplicationInsights` sekcji.

|Wymagane| Name (Nazwa) | Typ danych | Opis |
|--|------|-----------|-------------|
|Nie| `InstrumentationKey` | Ciąg | Klucz Instrumentacji wystąpienie usługi Application Insights do telemetrii, które są wysyłane dane w kontenerze. Aby uzyskać więcej informacji, zobacz [usługi Application Insights dla platformy ASP.NET Core](https://docs.microsoft.com/azure/application-insights/app-insights-asp-net-core). <br><br>Przykład:<br>`InstrumentationKey=123456789`|


## <a name="billing-setting"></a>Ustawienie rozliczeń

`Billing` Ustawienie określa identyfikator URI punktu końcowego z _Language Understanding_ zasobów na platformie Azure używane do pomiarów informacji rozliczeniowych dla kontenera. Należy określić wartość dla tego ustawienia konfiguracji, a wartość musi być prawidłowy identyfikator URI punktu końcowego dla _Language Understanding_ zasobów na platformie Azure.

To ustawienie można znaleźć w dwóch miejscach:

* Witryna Azure portal: **Language Understanding** Przegląd, etykietą `Endpoint`
* Portal usługi LUIS: **kluczy i punktu końcowego ustawienia** stronie jako część identyfikatora URI punktu końcowego.

|Wymagane| Name (Nazwa) | Typ danych | Opis |
|--|------|-----------|-------------|
|Yes| `Billing` | Ciąg | Identyfikator URI punktu końcowego rozliczeń<br><br>Przykład:<br>`Billing=https://westus.api.cognitive.microsoft.com/luis/v2.0` |

## <a name="eula-setting"></a>Umowa licencyjna EULA ustawienie

`Eula` Ustawienie wskazuje zaakceptowane licencji dla kontenera. Należy określić wartość dla tego ustawienia konfiguracji i musi być równa wartości `accept`.

|Wymagane| Name (Nazwa) | Typ danych | Opis |
|--|------|-----------|-------------|
|Yes| `Eula` | Ciąg | Akceptacja licencji<br><br>Przykład:<br>`Eula=accept` |

Kontenery usługi cognitive Services są licencjonowane w ramach umowy regulującej korzystanie z platformy Azure. Jeśli nie masz istniejącej umowy regulujących korzystanie z platformy Azure, zgadzasz się, czy umowy regulującej korzystanie z platformy Azure jest Microsoft Online Subscription — Umowa (co zawiera warunki usług Online). Dla wersji zapoznawczych wyrażasz zgodę również na dodatkowym warunkom użytkowania wersji zapoznawczych platformy Microsoft. Przy użyciu kontenera jest równoznaczne z wyrażeniem zgody na te warunki.

## <a name="fluentd-settings"></a>Ustawienia Fluentd

Fluentd jest moduł zbierający dane typu open source, ujednolicone rejestrowania. `Fluentd` Ustawienia zarządzania połączenie kontenera [Fluentd](https://www.fluentd.org) serwera. Kontener usługi LUIS obejmuje Fluentd dostawcy logowania, co pozwala na zapisywanie dzienników kontenera i, opcjonalnie, metryki danych na serwerze Fluentd.

W poniższej tabeli opisano ustawienia konfiguracji objęte `Fluentd` sekcji.

| Name (Nazwa) | Typ danych | Opis |
|------|-----------|-------------|
| `Host` | Ciąg | Adres IP lub nazwa hosta DNS serwera Fluentd. |
| `Port` | Liczba całkowita | Port serwera Fluentd.<br/> Wartość domyślna to 24224. |
| `HeartbeatMs` | Liczba całkowita | Interwał pulsu (w milisekundach). Jeśli żaden ruch nie jest zdarzenie zostało wysłane przed upływem tego interwału, pulsu są wysyłane do serwera Fluentd. Wartość domyślna to 60000 milisekund (1 minuta). |
| `SendBufferSize` | Liczba całkowita | Sieć miejsca w buforze, w bajtach, przydzielany dla operacji wysyłania. Wartość domyślna to 32768 bajtów (32 kilobajtów). |
| `TlsConnectionEstablishmentTimeoutMs` | Liczba całkowita | Limit czasu w milisekundach, można ustanowić połączenia SSL/TLS z serwerem Fluentd. Wartość domyślna to 10000 MS (10 sekund).<br/> Jeśli `UseTLS` jest ustawiona na wartość FAŁSZ, ta wartość jest ignorowana. |
| `UseTLS` | Wartość logiczna | Wskazuje, czy do komunikacji z serwerem Fluentd kontenera należy używać protokołów SSL/TLS. Wartość domyślna to false. |

## <a name="logging-settings"></a>Ustawienia rejestrowania

`Logging` Ustawienia zarządzania obsługę rejestrowania platformy ASP.NET Core kontenera. Można użyć tych samych ustawień konfiguracji oraz wartości kontenera używaną dla aplikacji ASP.NET Core. 

Następujących dostawców rejestrowania są obsługiwane przez kontener usługi LUIS:

|Dostawca|Przeznaczenie|
|--|--|
|[Console](https://docs.microsoft.com/aspnet/core/fundamentals/logging/?view=aspnetcore-2.1#console-provider)|ASP.NET Core `Console` dostawcy logowania. Obsługiwane są wszystkie ustawienia konfiguracji platformy ASP.NET Core i wartości domyślne dla tego dostawcy logowania.|
|[Debugowanie](https://docs.microsoft.com/aspnet/core/fundamentals/logging/?view=aspnetcore-2.1#debug-provider)|ASP.NET Core `Debug` dostawcy logowania. Obsługiwane są wszystkie ustawienia konfiguracji platformy ASP.NET Core i wartości domyślne dla tego dostawcy logowania.|
|[Dysk](#disk-logging)|Dostawca logowania JSON. Ten dostawca rejestrowania zapisuje dane dziennika instalacji danych wyjściowych.|

### <a name="disk-logging"></a>Rejestrowanie dysku
  
`Disk` Rejestrowania dostawca obsługuje następujące ustawienia konfiguracji:  

| Name (Nazwa) | Typ danych | Opis |
|------|-----------|-------------|
| `Format` | Ciąg | Format danych wyjściowych dla plików dziennika.<br/> **Uwaga:** ta wartość musi być równa `json` do włączenia rejestrowania dostawcy usług. Jeśli ta wartość jest określona bez jednoczesnego określenia dane wyjściowe instalacji podczas tworzenia wystąpienia kontenera, wystąpi błąd. |
| `MaxFileSize` | Liczba całkowita | Maksymalny rozmiar w megabajtach (MB), plik dziennika. Gdy rozmiar bieżącego pliku dziennika, spełnia lub przekracza tę wartość, nowy plik dziennika został uruchomiony przez dostawcę rejestrowania. Jeśli określono wartość -1, rozmiar pliku dziennika jest ograniczona tylko przez maksymalny rozmiar pliku, ewentualne instalacji danych wyjściowych. Wartość domyślna to 1. |

Aby uzyskać więcej informacji na temat konfigurowania obsługi rejestrowania platformy ASP.NET Core, zobacz [ustawień pliku konfiguracji](https://docs.microsoft.com/aspnet/core/fundamentals/logging/?view=aspnetcore-2.1#settings-file-configuration).

## <a name="mount-settings"></a>Ustawienia instalacji

Użyj powiązania instaluje do odczytu i zapisu danych do i z kontenera. Można określić instalacji danych wejściowych lub wyjściowych instalacji, określając `--mount` opcji [platformy docker, uruchom](https://docs.docker.com/engine/reference/commandline/run/) polecenia. 

Kontener usługi LUIS nie korzysta z danych wejściowych lub danych wyjściowych instaluje do przechowywania szkolenie i usługi danych. 

Dokładna składnia lokalizację instalacji hosta różni się zależnie od systemu operacyjnego hosta. Ponadto [komputerze-hoście](luis-container-howto.md#the-host-computer)w lokalizacji instalacji może być niedostępna z powodu konfliktu między uprawnienia użyte dla konta usługi docker i hosta instalacji uprawnienia do lokalizacji. 

W poniższej tabeli opisano ustawienia obsługiwane.

|Wymagane| Name (Nazwa) | Typ danych | Opis |
|-------|------|-----------|-------------|
|Yes| `Input` | Ciąg | Miejsce docelowe instalacji danych wejściowych. Wartość domyślna to `/input`. To jest lokalizacja plików pakietu usługi LUIS. <br><br>Przykład:<br>`--mount type=bind,src=c:\input,target=/input`|
|Nie| `Output` | Ciąg | Miejsce docelowe instalacji danych wyjściowych. Wartość domyślna to `/output`. Jest to Lokalizacja dzienników. Obejmuje to usługi LUIS dzienniki zapytań i dzienniki kontenerów. <br><br>Przykład:<br>`--mount type=bind,src=c:\output,target=/output`|

## <a name="hierarchical-settings"></a>Ustawienia hierarchicznych

Ustawienia dla kontenera usługi LUIS są hierarchiczne i wszystkich kontenerów na [komputerze-hoście](luis-container-howto.md#the-host-computer) używania udostępnionego hierarchii.

Aby określić ustawienia, można użyć jednej z następujących pozycji:

* [Zmienne środowiskowe](#environment-variable-settings)
* [Argumenty wiersza polecenia](#command-line-argument-settings)

Wartości zmiennych środowiskowych zastępują wartości argumentów wiersza polecenia, które z kolei przesłonić wartości domyślne dla obrazu kontenera. Jeśli określisz różne wartości w zmiennej środowiskowej i argument wiersza polecenia dla tego samego ustawienia konfiguracji, wartość w zmiennej środowiskowej jest używany przez kontener wystąpień.

|Pierwszeństwo|Ustawianie lokalizacji|
|--|--|
|1|Zmienna środowiskowa| 
|2|Wiersz polecenia|
|3|Wartość domyślna obrazu kontenera|

### <a name="environment-variable-settings"></a>Ustawień zmiennych środowiskowych

Korzyści z używania zmiennych środowiskowych są:

* Można skonfigurować wiele ustawień.
* Wiele kontenerów, można użyć tych samych ustawień.

### <a name="command-line-argument-settings"></a>Ustawienia argument wiersza polecenia

Zaletą używania argumentów wiersza polecenia jest, że każdego kontenera za pomocą różnych ustawień.

## <a name="example-docker-run-commands"></a>Przykład platformy docker, Uruchom polecenia

W poniższych przykładach używane ustawienia konfiguracji, aby zilustrować, jak pisać i użyj `docker run` poleceń.  Po uruchomieniu kontenera będzie działać do momentu [zatrzymać](luis-container-howto.md#stop-the-container) go.


* **Znak kontynuacji wiersza**: poleceń docker w poniższych sekcjach użyć ukośnika, `\`, jako znak kontynuacji wiersza. Zamień lub Usuń ten na podstawie wymagań systemu operacyjnego hosta. 
* **Kolejność argumentów**: nie należy zmieniać kolejność argumentów, jeśli nie znasz bardzo kontenerów platformy docker.

Zastąp {_argument_name_} własnymi wartościami:

| Symbol zastępczy | Wartość | Format lub przykład |
|-------------|-------|---|
|{ENDPOINT_KEY} | Klucz punktu końcowego uczonego aplikacji usługi LUIS. |xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx|
|{BILLING_ENDPOINT} | Rozliczeń wartości punktu końcowego jest dostępna na stronie Przegląd interpretacji języka w witrynie Azure portal.|https://westus.api.cognitive.microsoft.com/luis/v2.0|

> [!IMPORTANT]
> `Eula`, `Billing`, I `ApiKey` opcje muszą być określone w celu uruchomienia kontenera; w przeciwnym razie nie uruchamia się kontener.  Aby uzyskać więcej informacji, zobacz [rozliczeń](luis-container-howto.md#billing).
> Wartość ApiKey **klucz** z kluczy i punktów końcowych strony w portalu usługi LUIS, a także jest dostępny na stronie klucze zasobów interpretacji języka platformy Azure. 

### <a name="basic-example"></a>Podstawowy przykład

Poniższy przykład zawiera najmniejszą liczbą argumentów, można uruchomić kontener:

```bash
docker run --rm -it -p 5000:5000 --memory 4g --cpus 2 \
--mount type=bind,src=c:\input,target=/input \
--mount type=bind,src=c:\output,target=/output \
mcr.microsoft.com/azure-cognitive-services/luis:latest \
Eula=accept \
Billing={BILLING_ENDPOINT} \
ApiKey={ENDPOINT_KEY}
```

> [!Note] 
> Poprzednie polecenie korzysta z katalogu, wyłącz `c:` dysku, aby uniknąć konfliktów uprawnień na Windows. Jeśli musisz użyć określonego katalogu jako katalog wejściowy może być konieczne przyznanie platformy docker usługi uprawnienia. Poprzednie polecenie docker używa ukośnik, `\`, jako znak kontynuacji wiersza. Zamień lub Usuń na podstawie Twojej [komputerze-hoście](luis-container-howto.md#the-host-computer) wymagania dotyczące systemu operacyjnego. Nie należy zmieniać kolejność argumentów, jeśli nie znasz bardzo kontenerów platformy docker.


### <a name="applicationinsights-example"></a>Przykład dotycząca usługi Application Insights

W poniższym przykładzie ustawiono argument dotycząca usługi Application Insights do wysyłania telemetrii do usługi Application Insights, gdy kontener jest uruchomiony:

```bash
docker run --rm -it -p 5000:5000 --memory 6g --cpus 2 \
--mount type=bind,src=c:\input,target=/input \
--mount type=bind,src=c:\output,target=/output \
mcr.microsoft.com/azure-cognitive-services/luis:latest \
Eula=accept \
Billing={BILLING_ENDPOINT} \
ApiKey={ENDPOINT_KEY}
InstrumentationKey={INSTRUMENTATION_KEY}
```

### <a name="logging-example-with-command-line-arguments"></a>Przykład rejestrowania za pomocą argumentów wiersza polecenia.

Następujące polecenie ustawia poziom rejestrowania `Logging:Console:LogLevel`, aby skonfigurować poziom rejestrowania, aby [ `Information` ](https://msdn.microsoft.com). 

```bash
docker run --rm -it -p 5000:5000 --memory 6g --cpus 2 \
--mount type=bind,src=c:\input,target=/input \
--mount type=bind,src=c:\output,target=/output \
mcr.microsoft.com/azure-cognitive-services/luis:latest \
Eula=accept \
Billing={BILLING_ENDPOINT} \
ApiKey={ENDPOINT_KEY} \
Logging:Console:LogLevel=Information
```

### <a name="logging-example-with-environment-variable"></a>Przykład rejestrowania za pomocą zmiennej środowiskowej

Użycie polecenia zmienną środowiskową o nazwie `Logging:Console:LogLevel` poziom rejestrowania, aby skonfigurować [ `Information` ](https://msdn.microsoft.com). 

```bash
SET Logging:Console:LogLevel=Information
docker run --rm -it -p 5000:5000 --memory 6g --cpus 2 \
--mount type=bind,src=c:\input,target=/input \
--mount type=bind,src=c:\output,target=/output \
mcr.microsoft.com/azure-cognitive-services/luis:latest \
Eula=accept \
Billing={BILLING_ENDPOINT} \
ApiKey={APPLICATION_ID} \
```

## <a name="next-steps"></a>Kolejne kroki

* Przegląd [sposobu instalowania i uruchamiania kontenerów](luis-container-howto.md)
* Zapoznaj się [— często zadawane pytania (FAQ)](luis-resources-faq.md) Aby rozwiązać problemy związane z działaniem usługi LUIS.