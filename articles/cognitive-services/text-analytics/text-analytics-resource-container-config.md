---
title: Konfigurowanie kontenerów
titlesuffix: Text Analytics - Azure Cognitive Services
description: Analiza tekstu dostarcza każdego kontenera za pomocą wspólną platformę konfiguracji, można łatwo skonfigurować i zarządzać ustawieniami magazynu, rejestrowania i danych telemetrycznych i zabezpieczeń dla kontenerów.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 06/20/2019
ms.author: dapine
ms.openlocfilehash: 604b17bc3d4696ff0bf2be1759a53927a531fe13
ms.sourcegitcommit: 2d3b1d7653c6c585e9423cf41658de0c68d883fa
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/20/2019
ms.locfileid: "67296020"
---
# <a name="configure-text-analytics-docker-containers"></a>Konfigurowanie analizy tekstu kontenerów platformy docker

Analiza tekstu dostarcza każdego kontenera za pomocą wspólną platformę konfiguracji, można łatwo skonfigurować i zarządzać ustawieniami magazynu, rejestrowania i danych telemetrycznych i zabezpieczeń dla kontenerów.

## <a name="configuration-settings"></a>Ustawienia konfiguracji

[!INCLUDE [Container shared configuration settings table](../../../includes/cognitive-services-containers-configuration-shared-settings-table.md)]

> [!IMPORTANT]
> [ `ApiKey` ](#apikey-configuration-setting), [ `Billing` ](#billing-configuration-setting), I [ `Eula` ](#eula-setting) ustawienia są używane razem. Ponadto należy podać prawidłowe wartości dla wszystkich trzech ich; w przeciwnym razie Nie można uruchomić kontener. Aby uzyskać więcej informacji na temat tworzenia wystąpienia kontenera za pomocą tych ustawień konfiguracji, zobacz [rozliczeń](how-tos/text-analytics-how-to-install-containers.md#billing).

## <a name="apikey-configuration-setting"></a>Ustawienie konfiguracji ApiKey

`ApiKey` Ustawienie umożliwia określenie klucza zasobów platformy Azure używane do śledzenia informacji rozliczeniowych dla kontenera. Należy określić wartość dla ApiKey i wartość musi być prawidłowy klucz dla _usług Cognitive Services_ zasób określony dla [ `Billing` ](#billing-configuration-setting) ustawienia konfiguracji.

To ustawienie można znaleźć w tym miejscu następujące:

* Witryna Azure Portal: **Usługi cognitive Services** zarządzanie zasobami w obszarze **kluczy**

## <a name="applicationinsights-setting"></a>Ustawienie dotycząca usługi Application Insights

[!INCLUDE [Container shared configuration ApplicationInsights settings](../../../includes/cognitive-services-containers-configuration-shared-settings-application-insights.md)]

## <a name="billing-configuration-setting"></a>Ustawienie konfiguracji rozliczeń

`Billing` Ustawienie określa identyfikator URI punktu końcowego z _usług Cognitive Services_ zasobów na platformie Azure używane do pomiarów informacji rozliczeniowych dla kontenera. Należy określić wartość dla tego ustawienia konfiguracji, a wartość musi być prawidłowy identyfikator URI punktu końcowego dla funkcji __usług Cognitive Services_ zasobów na platformie Azure. Kontener raportów użycia dotyczących co 10 do 15 minut.

To ustawienie można znaleźć w tym miejscu następujące:

* Witryna Azure Portal: **Usługi cognitive Services** Przegląd, etykietą `Endpoint`

Musisz dodać `text/analytics/v2.0` routingu do identyfikator URI punktu końcowego, jak pokazano w poniższym przykładzie BILLING_ENDPOINT_URI.

|Wymagane| Name (Nazwa) | Typ danych | Opis |
|--|------|-----------|-------------|
|Yes| `Billing` | Ciąg | Identyfikator URI punktu końcowego rozliczeń<br><br>Przykład:<br>`Billing=https://westus.api.cognitive.microsoft.com/text/analytics/v2.1` |

## <a name="eula-setting"></a>Umowa licencyjna EULA ustawienie

[!INCLUDE [Container shared configuration eula settings](../../../includes/cognitive-services-containers-configuration-shared-settings-eula.md)]

## <a name="fluentd-settings"></a>Ustawienia Fluentd

[!INCLUDE [Container shared configuration fluentd settings](../../../includes/cognitive-services-containers-configuration-shared-settings-fluentd.md)]

## <a name="http-proxy-credentials-settings"></a>Ustawienia poświadczeń serwera proxy HTTP

[!INCLUDE [Container shared configuration fluentd settings](../../../includes/cognitive-services-containers-configuration-shared-settings-http-proxy.md)]

## <a name="logging-settings"></a>Ustawienia rejestrowania
 
[!INCLUDE [Container shared configuration logging settings](../../../includes/cognitive-services-containers-configuration-shared-settings-logging.md)]

## <a name="mount-settings"></a>Ustawienia instalacji

Użyj powiązania instaluje do odczytu i zapisu danych do i z kontenera. Można określić instalacji danych wejściowych lub wyjściowych instalacji, określając `--mount` opcji [platformy docker, uruchom](https://docs.docker.com/engine/reference/commandline/run/) polecenia.

Kontenery Analiza tekstu nie używaj danych wejściowych lub danych wyjściowych instaluje do przechowywania szkolenie i usługi danych. 

Dokładna składnia lokalizację instalacji hosta różni się zależnie od systemu operacyjnego hosta. Ponadto [komputerze-hoście](how-tos/text-analytics-how-to-install-containers.md#the-host-computer)w lokalizacji instalacji może być niedostępna z powodu konfliktu między uprawnienia użyte dla konta usługi docker i hosta instalacji uprawnienia do lokalizacji. 

|Optional (Opcjonalność)| Name (Nazwa) | Typ danych | Opis |
|-------|------|-----------|-------------|
|Niedozwolone| `Input` | String | Kontenery Analiza tekstu nie należy używać tego.|
|Optional (Opcjonalność)| `Output` | String | Miejsce docelowe instalacji danych wyjściowych. Wartość domyślna to `/output`. Jest to Lokalizacja dzienników. W tym dzienniki kontenerów. <br><br>Przykład:<br>`--mount type=bind,src=c:\output,target=/output`|

## <a name="example-docker-run-commands"></a>Przykład platformy docker, Uruchom polecenia 

W poniższych przykładach używane ustawienia konfiguracji, aby zilustrować, jak pisać i użyj `docker run` poleceń.  Po uruchomieniu kontenera będzie działać do momentu [zatrzymać](how-tos/text-analytics-how-to-install-containers.md#stop-the-container) go.

* **Znak kontynuacji wiersza**: Platformy docker w poniższych sekcjach użyto ukośnika, `\`, jako znak kontynuacji wiersza. Zamień lub Usuń ten na podstawie wymagań systemu operacyjnego hosta. 
* **Kolejność argumentów**: Nie należy zmieniać kolejność argumentów, jeśli nie znasz bardzo kontenerów platformy docker.

Musisz dodać `text/analytics/v2.0` routingu do identyfikator URI punktu końcowego, jak pokazano w poniższym przykładzie BILLING_ENDPOINT_URI.

Zastąp {_argument_name_} własnymi wartościami:

| Symbol zastępczy | Wartość | Format lub przykład |
|-------------|-------|---|
|{BILLING_KEY} | Klucz punktu końcowego `Cognitive Services` zasobów dostępnych na platformie Azure `Cognitive Services` strony klucze. |xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx|
|{BILLING_ENDPOINT_URI} | Rozliczeń wartość punktu końcowego jest dostępna na platformie Azure `Cognitive Services` strona przeglądu.|`https://westus.api.cognitive.microsoft.com/text/analytics/v2.1`|

> [!IMPORTANT]
> `Eula`, `Billing`, I `ApiKey` opcje muszą być określone w celu uruchomienia kontenera; w przeciwnym razie nie uruchamia się kontener.  Aby uzyskać więcej informacji, zobacz [rozliczeń](how-tos/text-analytics-how-to-install-containers.md#billing).
> Wartość ApiKey **klucz** z platformy Azure `Cognitive Services` strony klucze zasobu. 

## <a name="key-phrase-extraction-container-docker-examples"></a>Kluczowe frazy wyodrębniania kontenera platformy docker przykłady

Są w poniższych przykładach platformy docker dla kontenera wyodrębnianie fraz kluczowych. 

### <a name="basic-example"></a>Podstawowy przykład 

  ```
  docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 mcr.microsoft.com/azure-cognitive-services/keyphrase Eula=accept Billing={BILLING_ENDPOINT_URI} ApiKey={BILLING_KEY} 
  ```

### <a name="logging-example"></a>Przykład rejestrowania 

  ```
  docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 mcr.microsoft.com/azure-cognitive-services/keyphrase Eula=accept Billing={BILLING_ENDPOINT_URI} ApiKey={BILLING_KEY} Logging:Console:LogLevel:Default=Information
  ```

## <a name="language-detection-container-docker-examples"></a>Przykłady platformy docker container wykrywanie języka

Są w poniższych przykładach platformy docker dla kontenera wykrywanie języka. 

### <a name="basic-example"></a>Podstawowy przykład

  ```
  docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 mcr.microsoft.com/azure-cognitive-services/language Eula=accept Billing={BILLING_ENDPOINT_URI} ApiKey={BILLING_KEY} 
  ```

### <a name="logging-example"></a>Przykład rejestrowania

  ```
  docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 mcr.microsoft.com/azure-cognitive-services/language Eula=accept Billing={BILLING_ENDPOINT_URI} ApiKey={BILLING_KEY} Logging:Console:LogLevel:Default=Information
  ```
 
## <a name="sentiment-analysis-container-docker-examples"></a>Przykłady platformy docker container analizy tonacji

Są w poniższych przykładach platformy docker dla kontenera analizy tonacji. 

### <a name="basic-example"></a>Podstawowy przykład

  ```
  docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 mcr.microsoft.com/azure-cognitive-services/sentiment Eula=accept Billing={BILLING_ENDPOINT_URI} ApiKey={BILLING_KEY} 
  ```

### <a name="logging-example"></a>Przykład rejestrowania

  ```
  docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 mcr.microsoft.com/azure-cognitive-services/sentiment Eula=accept Billing={BILLING_ENDPOINT_URI} ApiKey={BILLING_KEY} Logging:Console:LogLevel:Default=Information
  ```

## <a name="next-steps"></a>Kolejne kroki

* Przegląd [sposobu instalowania i uruchamiania kontenerów](how-tos/text-analytics-how-to-install-containers.md)
* Użycie [kontenerów usługi Cognitive Services](../cognitive-services-container-support.md)
