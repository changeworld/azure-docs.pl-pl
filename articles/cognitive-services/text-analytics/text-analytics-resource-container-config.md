---
title: Konfigurowanie kontenerów — analiza tekstu
titleSuffix: Azure Cognitive Services
description: Analiza tekstu dostarcza każdego kontenera za pomocą wspólną platformę konfiguracji, można łatwo skonfigurować i zarządzać ustawieniami magazynu, rejestrowania i danych telemetrycznych i zabezpieczeń dla kontenerów.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 08/21/2019
ms.author: dapine
ms.openlocfilehash: f1c42002343de1dd3b3ef6b9c9e35f458db925f4
ms.sourcegitcommit: bba811bd615077dc0610c7435e4513b184fbed19
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/27/2019
ms.locfileid: "70051128"
---
# <a name="configure-text-analytics-docker-containers"></a>Konfigurowanie analiza tekstu kontenerów platformy Docker

Analiza tekstu dostarcza każdego kontenera za pomocą wspólną platformę konfiguracji, można łatwo skonfigurować i zarządzać ustawieniami magazynu, rejestrowania i danych telemetrycznych i zabezpieczeń dla kontenerów.

## <a name="configuration-settings"></a>Ustawienia konfiguracji

[!INCLUDE [Container shared configuration settings table](../../../includes/cognitive-services-containers-configuration-shared-settings-table.md)]

> [!IMPORTANT]
> [ `ApiKey` ](#apikey-configuration-setting), [ `Billing` ](#billing-configuration-setting), I [ `Eula` ](#eula-setting) ustawienia są używane razem. Ponadto należy podać prawidłowe wartości dla wszystkich trzech ich; w przeciwnym razie Nie można uruchomić kontener. Aby uzyskać więcej informacji na temat tworzenia wystąpienia kontenera za pomocą tych ustawień konfiguracji, zobacz [rozliczeń](how-tos/text-analytics-how-to-install-containers.md#billing).

## <a name="apikey-configuration-setting"></a>Ustawienie konfiguracji ApiKey

`ApiKey` Ustawienie umożliwia określenie klucza zasobów platformy Azure używane do śledzenia informacji rozliczeniowych dla kontenera. Należy określić wartość ApiKey, a wartość musi być prawidłowym kluczem dla zasobu _Analiza tekstu_ określonego dla [`Billing`](#billing-configuration-setting) ustawienia konfiguracji.

To ustawienie można znaleźć w następujących miejscach:

* Azure Portal: Zarządzanie zasobami **Analiza tekstu** w obszarze **klucze**

## <a name="applicationinsights-setting"></a>Ustawienie dotycząca usługi Application Insights

[!INCLUDE [Container shared configuration ApplicationInsights settings](../../../includes/cognitive-services-containers-configuration-shared-settings-application-insights.md)]

## <a name="billing-configuration-setting"></a>Ustawienie konfiguracji rozliczeń

Ustawienie określa identyfikator URI punktu końcowego zasobu analiza tekstu na platformie Azure używany do mierzenia informacji rozliczeniowych dla kontenera. `Billing` Należy określić wartość dla tego ustawienia konfiguracji, a wartość musi być prawidłowym identyfikatorem URI punktu końcowego dla zasobu __Analiza tekstu_ na platformie Azure. Kontener zgłasza użycie co 10 do 15 minut.

To ustawienie można znaleźć w następujących miejscach:

* Azure Portal: **Analiza tekstu** Omówienie, etykieta`Endpoint`

|Wymagane| Name (Nazwa) | Typ danych | Opis |
|--|------|-----------|-------------|
|Yes| `Billing` | String | Wymagany identyfikator URI punktu końcowego rozliczenia |

## <a name="eula-setting"></a>Umowa licencyjna EULA ustawienie

[!INCLUDE [Container shared configuration eula settings](../../../includes/cognitive-services-containers-configuration-shared-settings-eula.md)]

## <a name="fluentd-settings"></a>Ustawienia Fluentd

[!INCLUDE [Container shared configuration fluentd settings](../../../includes/cognitive-services-containers-configuration-shared-settings-fluentd.md)]

## <a name="http-proxy-credentials-settings"></a>Ustawienia poświadczeń serwera proxy http

[!INCLUDE [Container shared configuration fluentd settings](../../../includes/cognitive-services-containers-configuration-shared-settings-http-proxy.md)]

## <a name="logging-settings"></a>Ustawienia rejestrowania
 
[!INCLUDE [Container shared configuration logging settings](../../../includes/cognitive-services-containers-configuration-shared-settings-logging.md)]

## <a name="mount-settings"></a>Ustawienia instalacji

Użyj powiązania instaluje do odczytu i zapisu danych do i z kontenera. Można określić instalacji danych wejściowych lub wyjściowych instalacji, określając `--mount` opcji [platformy docker, uruchom](https://docs.docker.com/engine/reference/commandline/run/) polecenia.

Kontenery analiza tekstu nie używają instalacji danych wejściowych lub wyjściowych do przechowywania danych szkoleniowych lub usług. 

Dokładna składnia lokalizację instalacji hosta różni się zależnie od systemu operacyjnego hosta. Ponadto [komputerze-hoście](how-tos/text-analytics-how-to-install-containers.md#the-host-computer)w lokalizacji instalacji może być niedostępna z powodu konfliktu między uprawnienia użyte dla konta usługi docker i hosta instalacji uprawnienia do lokalizacji. 

|Optional| Name | Typ danych | Opis |
|-------|------|-----------|-------------|
|Niedozwolone| `Input` | String | Kontenery analiza tekstu nie używają tego.|
|Optional| `Output` | String | Miejsce docelowe instalacji danych wyjściowych. Wartość domyślna to `/output`. Jest to Lokalizacja dzienników. Dotyczy to również dzienników kontenerów. <br><br>Przykład:<br>`--mount type=bind,src=c:\output,target=/output`|

## <a name="example-docker-run-commands"></a>Przykład platformy docker, Uruchom polecenia 

W poniższych przykładach używane ustawienia konfiguracji, aby zilustrować, jak pisać i użyj `docker run` poleceń.  Po uruchomieniu kontenera będzie działać do momentu [zatrzymać](how-tos/text-analytics-how-to-install-containers.md#stop-the-container) go.

* **Znak kontynuacji wiersza**: Polecenia platformy Docker w poniższych sekcjach używają ukośnika odwrotnego `\`, jako znaku kontynuacji wiersza. Zamień lub Usuń ten na podstawie wymagań systemu operacyjnego hosta. 
* **Kolejność argumentów**: Nie należy zmieniać kolejność argumentów, jeśli nie znasz bardzo kontenerów platformy docker.

Zastąp {_argument_name_} własnymi wartościami:

| Symbol zastępczy | Wartość | Format lub przykład |
|-------------|-------|---|
| **{API_KEY}** | Klucz `Text Analytics` punktu końcowego zasobu jest dostępny na stronie kluczy platformy `Text Analytics` Azure. |`xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx`|
| **{ENDPOINT_URI}** | Wartość punktu końcowego rozliczenia jest dostępna na stronie `Text Analytics` przegląd platformy Azure.| Zobacz [zbieranie wymaganych parametrów](how-tos/text-analytics-how-to-install-containers.md#gathering-required-parameters) dla jawnych przykładów. |

> [!IMPORTANT]
> `Eula`, `Billing`, I `ApiKey` opcje muszą być określone w celu uruchomienia kontenera; w przeciwnym razie nie uruchamia się kontener.  Aby uzyskać więcej informacji, zobacz [rozliczeń](how-tos/text-analytics-how-to-install-containers.md#billing).
> Wartość ApiKey jest **kluczem** ze strony klucze zasobów platformy `Text Analytics` Azure. 

#### <a name="key-phrase-extractiontabkeyphrase"></a>[Wyodrębnianie kluczowych fraz](#tab/keyphrase)

[!INCLUDE [key-phrase-extraction-docker-examples](includes/key-phrase-extraction-docker-examples.md)]

#### <a name="language-detectiontablanguage"></a>[Wykrywanie języka](#tab/language)

[!INCLUDE [language-detection-docker-examples](includes/language-detection-docker-examples.md)]

#### <a name="sentiment-analysistabsentiment"></a>[Analiza tonacji](#tab/sentiment)

[!INCLUDE [sentiment-analysis-docker-examples](includes/sentiment-analysis-docker-examples.md)]

***

## <a name="next-steps"></a>Następne kroki

* Przegląd [sposobu instalowania i uruchamiania kontenerów](how-tos/text-analytics-how-to-install-containers.md)
* Użyj więcej [kontenerów Cognitive Services](../cognitive-services-container-support.md)
