---
title: Konfigurowanie kontenerów — analiza tekstu
titleSuffix: Azure Cognitive Services
description: Usługa Text Analytics udostępnia każdemu kontenerowi wspólną strukturę konfiguracji, dzięki czemu można łatwo skonfigurować magazyn, rejestrowanie i dane telemetryczne oraz zarządzać nimi oraz ustawienia zabezpieczeń kontenerów.
services: cognitive-services
author: aahill
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: f062fb2f3a653bc1b2845b92e373fdb67ba583d8
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/08/2020
ms.locfileid: "80878686"
---
# <a name="configure-text-analytics-docker-containers"></a>Konfigurowanie kontenerów platformy dokowania analizy tekstu

Usługa Text Analytics udostępnia każdemu kontenerowi wspólną strukturę konfiguracji, dzięki czemu można łatwo skonfigurować magazyn, rejestrowanie i dane telemetryczne oraz zarządzać nimi oraz ustawienia zabezpieczeń kontenerów.

## <a name="configuration-settings"></a>Ustawienia konfiguracji

[!INCLUDE [Container shared configuration settings table](../../../includes/cognitive-services-containers-configuration-shared-settings-table.md)]

> [!IMPORTANT]
> Ustawienia [`ApiKey`](#apikey-configuration-setting) [`Billing`](#billing-configuration-setting)i [`Eula`](#eula-setting) ustawienia są używane razem i należy podać prawidłowe wartości dla wszystkich trzech z nich; w przeciwnym razie kontener nie zostanie uruchomiony. Aby uzyskać więcej informacji na temat używania tych ustawień konfiguracji do tworzenia wystąpienia kontenera, zobacz [Rozliczenia](how-tos/text-analytics-how-to-install-containers.md#billing).

## <a name="apikey-configuration-setting"></a>Ustawienie konfiguracji apikey

Ustawienie `ApiKey` określa klucz zasobów platformy Azure używany do śledzenia informacji rozliczeniowych dla kontenera. Należy określić wartość dla apikey i wartość musi być prawidłowy klucz [`Billing`](#billing-configuration-setting) dla zasobu analizy _tekstu_ określonego dla ustawienia konfiguracji.

To ustawienie można znaleźć w następującym miejscu:

* Portal Azure: Zarządzanie zasobami **analizy tekstu** w obszarze **Klucze**

## <a name="applicationinsights-setting"></a>Ustawienie ApplicationInsights

[!INCLUDE [Container shared configuration ApplicationInsights settings](../../../includes/cognitive-services-containers-configuration-shared-settings-application-insights.md)]

## <a name="billing-configuration-setting"></a>Ustawienie konfiguracji rozliczeń

Ustawienie `Billing` określa identyfikator URI punktu końcowego zasobu _analizy tekstu_ na platformie Azure używane do pomiaru informacji rozliczeniowych dla kontenera. Należy określić wartość dla tego ustawienia konfiguracji, a wartość musi być prawidłowym identyfikatorem URI punktu końcowego dla zasobu __analizy tekstu_ na platformie Azure. Kontener raportuje użycie co 10 do 15 minut.

To ustawienie można znaleźć w następującym miejscu:

* Portal Azure: Omówienie **analizy tekstu,** oznaczone etykietami`Endpoint`

|Wymagany| Nazwa | Typ danych | Opis |
|--|------|-----------|-------------|
|Tak| `Billing` | Ciąg | Identyfikator URI punktu końcowego rozliczeń. Aby uzyskać więcej informacji na temat uzyskiwania identyfikatora URI rozliczeń, zobacz [zbieranie wymaganych parametrów](how-tos/text-analytics-how-to-install-containers.md#gathering-required-parameters). Aby uzyskać więcej informacji i pełną listę regionalnych punktów końcowych, zobacz [Niestandardowe nazwy poddomen dla usług Cognitive Services](../cognitive-services-custom-subdomains.md). |

## <a name="eula-setting"></a>Ustawienie Eula

[!INCLUDE [Container shared configuration eula settings](../../../includes/cognitive-services-containers-configuration-shared-settings-eula.md)]

## <a name="fluentd-settings"></a>Płynne ustawienia

[!INCLUDE [Container shared configuration fluentd settings](../../../includes/cognitive-services-containers-configuration-shared-settings-fluentd.md)]

## <a name="http-proxy-credentials-settings"></a>Ustawienia poświadczeń serwera proxy http

[!INCLUDE [Container shared configuration fluentd settings](../../../includes/cognitive-services-containers-configuration-shared-settings-http-proxy.md)]

## <a name="logging-settings"></a>Ustawienia logowania
 
[!INCLUDE [Container shared configuration logging settings](../../../includes/cognitive-services-containers-configuration-shared-settings-logging.md)]

## <a name="mount-settings"></a>Ustawienia instalacji

Użyj powiązania wierzchowce do odczytu i zapisu danych do i z kontenera. Można określić mocowanie wejściowe lub `--mount` mocowanie wyjściowe, określając opcję w poleceniu [uruchamiania platformy docker.](https://docs.docker.com/engine/reference/commandline/run/)

Kontenery analizy tekstu nie używają instalacji wejściowych ani wyjściowych do przechowywania danych szkoleniowych lub usługowych. 

Dokładna składnia lokalizacji instalacji hosta różni się w zależności od systemu operacyjnego hosta. Ponadto lokalizacja instalacji [komputera-hosta](how-tos/text-analytics-how-to-install-containers.md#the-host-computer)może być niedostępna z powodu konfliktu między uprawnieniami używanymi przez konto usługi platformy docker a uprawnieniami lokalizacji instalacji hosta. 

|Optional (Opcjonalność)| Nazwa | Typ danych | Opis |
|-------|------|-----------|-------------|
|Niedozwolone| `Input` | Ciąg | Kontenery analizy tekstu nie używają tego.|
|Optional (Opcjonalność)| `Output` | Ciąg | Miejsce docelowe mocowania wyjściowego. Wartością domyślną jest `/output`. Jest to lokalizacja dzienników. Obejmuje to dzienniki kontenerów. <br><br>Przykład:<br>`--mount type=bind,src=c:\output,target=/output`|

## <a name="example-docker-run-commands"></a>Przykładowe polecenia uruchamiania platformy dok. 

Poniższe przykłady używają ustawień konfiguracji, aby zilustrować sposób pisania i używania `docker run` poleceń.  Po uruchomieniu kontener będzie nadal działał, dopóki go nie [zatrzymasz.](how-tos/text-analytics-how-to-install-containers.md#stop-the-container)

* **Znak kontynuacji wiersza:** Polecenia docker w poniższych sekcjach używają ukośnika wstecznego, `\`jako znaku kontynuacji wiersza. Zastąp lub usuń to na podstawie wymagań systemu operacyjnego hosta. 
* **Kolejność argumentów:** Nie należy zmieniać kolejności argumentów, chyba że są bardzo zaznajomieni z kontenerami docker.

Zamień {_argument_name_} własnymi wartościami:

| Symbol zastępczy | Wartość | Format lub przykład |
|-------------|-------|---|
| **{API_KEY}** | Klucz punktu końcowego `Text Analytics` zasobu dostępnego na stronie Klucze platformy Azure. `Text Analytics` |`xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx`|
| **{ENDPOINT_URI}** | Wartość punktu końcowego rozliczeń jest `Text Analytics` dostępna na stronie Przegląd platformy Azure.| Zobacz [zbieranie wymaganych parametrów](how-tos/text-analytics-how-to-install-containers.md#gathering-required-parameters) dla jawnych przykładów. |

> [!IMPORTANT]
> Aby `Eula` `Billing`uruchomić `ApiKey` kontener, należy określić opcje i opcje; w przeciwnym razie kontener nie zostanie uruchomiony.  Aby uzyskać więcej informacji, zobacz [Rozliczenia](how-tos/text-analytics-how-to-install-containers.md#billing).
> Wartość ApiKey jest **kluczem** `Text Analytics` ze strony klucze zasobów platformy Azure. 

#### <a name="key-phrase-extraction"></a>[Wyodrębnianie kluczowych fraz](#tab/keyphrase)

[!INCLUDE [key-phrase-extraction-docker-examples](includes/key-phrase-extraction-docker-examples.md)]

#### <a name="language-detection"></a>[Wykrywanie języka](#tab/language)

[!INCLUDE [language-detection-docker-examples](includes/language-detection-docker-examples.md)]

#### <a name="sentiment-analysis"></a>[Analiza tonacji](#tab/sentiment)

[!INCLUDE [sentiment-analysis-docker-examples](includes/sentiment-analysis-docker-examples.md)]

***

## <a name="next-steps"></a>Następne kroki

* Sprawdź, [jak zainstalować i uruchomić kontenery](how-tos/text-analytics-how-to-install-containers.md)
* Użyj większej liczby [kontenerów usług Cognitive Services](../cognitive-services-container-support.md)
