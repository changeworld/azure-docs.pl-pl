---
title: Konfigurowanie kontenerów
titlesuffix: Text Analytics - Azure Cognitive Services
description: Analiza tekstu dostarcza każdego kontenera za pomocą wspólną platformę konfiguracji, można łatwo skonfigurować i zarządzać ustawieniami magazynu, rejestrowania i danych telemetrycznych i zabezpieczeń dla kontenerów.
services: cognitive-services
author: diberry
manager: cgronlun
ms.custom: seodec18
ms.service: cognitive-services
ms.component: text-analytics
ms.topic: conceptual
ms.date: 01/02/2019
ms.author: diberry
ms.openlocfilehash: acab20f7fa9594d6b86a2cc63a69e91759b57b38
ms.sourcegitcommit: 803e66de6de4a094c6ae9cde7b76f5f4b622a7bb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/02/2019
ms.locfileid: "53975563"
---
# <a name="configure-text-analytics-docker-containers"></a>Konfigurowanie analizy tekstu kontenerów platformy docker

Analiza tekstu dostarcza każdego kontenera za pomocą wspólną platformę konfiguracji, można łatwo skonfigurować i zarządzać ustawieniami magazynu, rejestrowania i danych telemetrycznych i zabezpieczeń dla kontenerów.

## <a name="configuration-settings"></a>Ustawienia konfiguracji

[!INCLUDE [Container shared configuration settings table](../../../includes/cognitive-services-containers-configuration-shared-settings-table.md)]

> [!IMPORTANT]
> [ `ApiKey` ](#apikey-setting), [ `Billing` ](#billing-setting), I [ `Eula` ](#eula-setting) ustawienia są używane razem. Ponadto należy podać prawidłowe wartości dla wszystkich trzech ich; w przeciwnym razie Nie można uruchomić kontener. Aby uzyskać więcej informacji na temat tworzenia wystąpienia kontenera za pomocą tych ustawień konfiguracji, zobacz [rozliczeń](how-tos/text-analytics-how-to-install-containers.md#billing).

## <a name="apikey-configuration-setting"></a>Ustawienie konfiguracji ApiKey

`ApiKey` Ustawienie umożliwia określenie klucza zasobów platformy Azure używane do śledzenia informacji rozliczeniowych dla kontenera. Należy określić wartość dla ApiKey i wartość musi być prawidłowy klucz dla _analizy tekstu_ zasób określony dla [ `Billing` ](#billing-setting) ustawienia konfiguracji.

To ustawienie można znaleźć w tym miejscu następujące:

* Azure Portal: **Analiza tekstu** zarządzanie zasobami w obszarze **kluczy**

## <a name="applicationinsights-setting"></a>Ustawienie dotycząca usługi Application Insights

[!INCLUDE [Container shared configuration ApplicationInsights settings](../../../includes/cognitive-services-containers-configuration-shared-settings-application-insights.md)]

## <a name="billing-configuration-setting"></a>Ustawienie konfiguracji rozliczeń

`Billing` Ustawienie określa identyfikator URI punktu końcowego z _analizy tekstu_ zasobów na platformie Azure używane do pomiarów informacji rozliczeniowych dla kontenera. Należy określić wartość dla tego ustawienia konfiguracji, a wartość musi być prawidłowy identyfikator URI punktu końcowego dla funkcji __analizy tekstu_ zasobów na platformie Azure.

To ustawienie można znaleźć w tym miejscu następujące:

* Azure Portal: **Analiza tekstu** Przegląd, etykietą `Endpoint`

|Wymagane| Name (Nazwa) | Typ danych | Opis |
|--|------|-----------|-------------|
|Yes| `Billing` | Ciąg | Identyfikator URI punktu końcowego rozliczeń<br><br>Przykład:<br>`Billing=https://westus.api.cognitive.microsoft.com/text/analytics/v2.0` |

## <a name="eula-setting"></a>Umowa licencyjna EULA ustawienie

[!INCLUDE [Container shared configuration eula settings](../../../includes/cognitive-services-containers-configuration-shared-settings-eula.md)]

## <a name="fluentd-settings"></a>Ustawienia Fluentd


[!INCLUDE [Container shared configuration fluentd settings](../../../includes/cognitive-services-containers-configuration-shared-settings-fluentd.md)]

## <a name="logging-settings"></a>Ustawienia rejestrowania
 
[!INCLUDE [Container shared configuration logging settings](../../../includes/cognitive-services-containers-configuration-shared-settings-logging.md)]

## <a name="mount-settings"></a>Ustawienia instalacji

Użyj powiązania instaluje do odczytu i zapisu danych do i z kontenera. Można określić instalacji danych wejściowych lub wyjściowych instalacji, określając `--mount` opcji [platformy docker, uruchom](https://docs.docker.com/engine/reference/commandline/run/) polecenia.

Kontenery Analiza tekstu nie używaj danych wejściowych lub danych wyjściowych instaluje do przechowywania szkolenie i usługi danych. 

Dokładna składnia lokalizację instalacji hosta różni się zależnie od systemu operacyjnego hosta. Ponadto [komputerze-hoście](how-tos/text-analytics-how-to-install-containers.md#the-host-computer)w lokalizacji instalacji może być niedostępna z powodu konfliktu między uprawnienia użyte dla konta usługi docker i hosta instalacji uprawnienia do lokalizacji. 

|Optional (Opcjonalność)| Name (Nazwa) | Typ danych | Opis |
|-------|------|-----------|-------------|
|Niedozwolone| `Input` | Ciąg | Kontenery Analiza tekstu nie należy używać tego.|
|Optional (Opcjonalność)| `Output` | Ciąg | Miejsce docelowe instalacji danych wyjściowych. Wartość domyślna to `/output`. Jest to Lokalizacja dzienników. W tym dzienniki kontenerów. <br><br>Przykład:<br>`--mount type=bind,src=c:\output,target=/output`|

## <a name="hierarchical-settings"></a>Ustawienia hierarchicznych

[!INCLUDE [Container shared configuration hierarchical settings](../../../includes/cognitive-services-containers-configuration-shared-hierarchical-settings.md)]

## <a name="example-docker-run-commands"></a>Przykład platformy docker, Uruchom polecenia 

W poniższych przykładach używane ustawienia konfiguracji, aby zilustrować, jak pisać i użyj `docker run` poleceń.  Po uruchomieniu kontenera będzie działać do momentu [zatrzymać](how-tos/text-analytics-how-to-install-containers.md#stop-the-container) go.

* **Znak kontynuacji wiersza**: Platformy docker w poniższych sekcjach użyto ukośnika, `\`, jako znak kontynuacji wiersza. Zamień lub Usuń ten na podstawie wymagań systemu operacyjnego hosta. 
* **Kolejność argumentów**: Nie należy zmieniać kolejność argumentów, jeśli nie znasz bardzo kontenerów platformy docker.

Zastąp {_argument_name_} własnymi wartościami:

| Symbol zastępczy | Wartość | Format lub przykład |
|-------------|-------|---|
|{BILLING_KEY} | Klucz punktu końcowego zasobu analizy tekstu, dostępny na stronie klucze analizy tekstu w witrynie Azure portal. |xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx|
|{BILLING_ENDPOINT_URI} | Rozliczeń wartości punktu końcowego jest dostępna na stronie Przegląd analizy tekstu witryny Azure portal.|`https://westus.api.cognitive.microsoft.com/text/analytics/v2.0`|

> [!IMPORTANT]
> `Eula`, `Billing`, I `ApiKey` opcje muszą być określone w celu uruchomienia kontenera; w przeciwnym razie nie uruchamia się kontener.  Aby uzyskać więcej informacji, zobacz [rozliczeń](how-tos/text-analytics-how-to-install-containers.md#billing).
> Wartość ApiKey **klucz** ze strony klucze w usłudze Azure Resource analizy tekstu. 

## <a name="keyphrase-extraction-container-docker-examples"></a>Keyphrase wyodrębniania kontenera platformy docker przykłady

W poniższych przykładach platformy docker są keyphrase kontenera wyodrębniania. 

### <a name="basic-example"></a>Podstawowy przykład 

  ```Docker
  docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 mcr.microsoft.com/azure-cognitive-services/keyphrase Eula=accept Billing={BILLING_ENDPOINT_URI} ApiKey={BILLING_KEY} 
  ```

### <a name="logging-example-with-command-line-arguments"></a>Przykład rejestrowania za pomocą argumentów wiersza polecenia.

  ```Docker
  docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 mcr.microsoft.com/azure-cognitive-services/keyphrase Eula=accept Billing={BILLING_ENDPOINT_URI} ApiKey={BILLING_KEY} Logging:Console:LogLevel=Information
  ```

### <a name="logging-example-with-environment-variable"></a>Przykład rejestrowania za pomocą zmiennej środowiskowej

  ```Docker
  SET Logging:Console:LogLevel=Information
  docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 mcr.microsoft.com/azure-cognitive-services/keyphrase  Eula=accept Billing={BILLING_ENDPOINT_URI} ApiKey={BILLING_KEY}
  ```

## <a name="language-detection-container-docker-examples"></a>Przykłady platformy docker container wykrywanie języka

Są w poniższych przykładach platformy docker dla kontenera wykrywanie języka. 

### <a name="basic-example"></a>Podstawowy przykład

  ```Docker
  docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 mcr.microsoft.com/azure-cognitive-services/language Eula=accept Billing={BILLING_ENDPOINT_URI} ApiKey={BILLING_KEY} Logging:Console:LogLevel=Information
  ```

### <a name="logging-example-with-command-line-arguments"></a>Przykład rejestrowania za pomocą argumentów wiersza polecenia.

  ```Docker
  docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 mcr.microsoft.com/azure-cognitive-services/language Eula=accept Billing={BILLING_ENDPOINT_URI} ApiKey={BILLING_KEY} Logging:Console:LogLevel=Information
  ```

### <a name="logging-example-with-environment-variable"></a>Przykład rejestrowania za pomocą zmiennej środowiskowej

  ```Docker
  SET Logging:Console:LogLevel=Information
  docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 mcr.microsoft.com/azure-cognitive-services/language  Eula=accept Billing={BILLING_ENDPOINT_URI} ApiKey={BILLING_KEY}
  ```
 
## <a name="sentiment-analysis-container-docker-examples"></a>Przykłady platformy docker container analizy tonacji

Są w poniższych przykładach platformy docker dla kontenera analizy tonacji. 

### <a name="basic-example"></a>Podstawowy przykład

  ```Docker
  docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 mcr.microsoft.com/azure-cognitive-services/sentiment Eula=accept Billing={BILLING_ENDPOINT_URI} ApiKey={BILLING_KEY} Logging:Console:LogLevel=Information
  ```

### <a name="logging-example-with-command-line-arguments"></a>Przykład rejestrowania za pomocą argumentów wiersza polecenia.

  ```Docker
  docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 mcr.microsoft.com/azure-cognitive-services/sentiment Eula=accept Billing={BILLING_ENDPOINT_URI} ApiKey={BILLING_KEY} Logging:Console:LogLevel=Information
  ```

### <a name="logging-example-with-environment-variable"></a>Przykład rejestrowania za pomocą zmiennej środowiskowej

  ```Docker
  SET Logging:Console:LogLevel=Information
  docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 mcr.microsoft.com/azure-cognitive-services/sentiment Eula=accept Billing={BILLING_ENDPOINT_URI} ApiKey={BILLING_KEY}
  ```

## <a name="next-steps"></a>Kolejne kroki

* Przegląd [sposobu instalowania i uruchamiania kontenerów](how-tos/text-analytics-how-to-install-containers.md)