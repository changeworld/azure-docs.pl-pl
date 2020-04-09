---
title: Jak skonfigurować kontener dla interfejsu API detektora anomalii
titleSuffix: Azure Cognitive Services
description: Środowisko wykonawcze kontenera kontenera detektora `docker run` anomalii jest konfigurowane przy użyciu argumentów polecenia. Ten kontener ma kilka wymaganych ustawień, wraz z kilkoma ustawieniami opcjonalnymi.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: conceptual
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: 569499002c5e047d7030575342790e9a074b9404
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/08/2020
ms.locfileid: "80875184"
---
# <a name="configure-anomaly-detector-containers"></a>Konfigurowanie kontenerów narzędzia do wykrywania anomalii

Środowisko wykonawcze **kontenera detektora anomalii** jest konfigurowane przy użyciu argumentów `docker run` polecenia. Ten kontener ma kilka wymaganych ustawień, wraz z kilkoma ustawieniami opcjonalnymi. Dostępnych jest kilka [przykładów](#example-docker-run-commands) polecenia. Ustawienia specyficzne dla kontenera to ustawienia rozliczeń. 

## <a name="configuration-settings"></a>Ustawienia konfiguracji

Ten kontener ma następujące ustawienia konfiguracji:

|Wymagany|Ustawienie|Przeznaczenie|
|--|--|--|
|Tak|[Klawisz ApiKey](#apikey-configuration-setting)|Służy do śledzenia informacji rozliczeniowych.|
|Nie|[ApplicationInsights](#applicationinsights-setting)|Umożliwia dodawanie obsługi telemetrii [usługi Azure Application Insights](https://docs.microsoft.com/azure/application-insights) do kontenera.|
|Tak|[Rozliczenia](#billing-configuration-setting)|Określa identyfikator URI punktu końcowego zasobu usługi na platformie Azure.|
|Tak|[Eula](#eula-setting)| Wskazuje, że licencja dla kontenera została zaakceptowana.|
|Nie|[Biegle](#fluentd-settings)|Zapis dziennika i, opcjonalnie, dane metryki na serwerze Fluentd.|
|Nie|[Http Proxy](#http-proxy-credentials-settings)|Konfigurowanie serwera proxy HTTP do wysyłania żądań wychodzących.|
|Nie|[Rejestrowanie](#logging-settings)|Zapewnia ASP.NET podstawową obsługę rejestrowania kontenera. |
|Nie|[Wierzchowce](#mount-settings)|Odczytywanie i zapisywanie danych z komputera-hosta do kontenera i z kontenera z powrotem do komputera hosta.|

> [!IMPORTANT]
> Ustawienia [`ApiKey`](#apikey-configuration-setting) [`Billing`](#billing-configuration-setting)i [`Eula`](#eula-setting) ustawienia są używane razem i należy podać prawidłowe wartości dla wszystkich trzech z nich; w przeciwnym razie kontener nie zostanie uruchomiony. Aby uzyskać więcej informacji na temat używania tych ustawień konfiguracji do tworzenia wystąpienia kontenera, zobacz [Rozliczenia](anomaly-detector-container-howto.md#billing).

## <a name="apikey-configuration-setting"></a>Ustawienie konfiguracji apikey

Ustawienie `ApiKey` określa klucz zasobów platformy Azure używany do śledzenia informacji rozliczeniowych dla kontenera. Należy określić wartość dla apikey i wartość musi być prawidłowy klucz dla [`Billing`](#billing-configuration-setting) zasobu _Detektor anomalii_ określony dla ustawienia konfiguracji.

To ustawienie można znaleźć w następującym miejscu:

* Portal Azure: Zarządzanie zasobami **detektora anomalii** w obszarze **Klucze**

## <a name="applicationinsights-setting"></a>Ustawienie ApplicationInsights

[!INCLUDE [Container shared configuration ApplicationInsights settings](../../../includes/cognitive-services-containers-configuration-shared-settings-application-insights.md)]

## <a name="billing-configuration-setting"></a>Ustawienie konfiguracji rozliczeń

Ustawienie `Billing` określa identyfikator URI punktu końcowego zasobu _Wykrywania anomalii_ na platformie Azure używanego do pomiaru informacji rozliczeniowych dla kontenera. Należy określić wartość dla tego ustawienia konfiguracji, a wartość musi być prawidłowym identyfikatorem URI punktu końcowego dla zasobu _detektora anomalii_ na platformie Azure.

To ustawienie można znaleźć w następującym miejscu:

* Portal Azure: Przegląd **detektora anomalii,** oznaczony etykietą`Endpoint`

|Wymagany| Nazwa | Typ danych | Opis |
|--|------|-----------|-------------|
|Tak| `Billing` | Ciąg | Identyfikator URI punktu końcowego rozliczeń. Aby uzyskać więcej informacji na temat uzyskiwania identyfikatora URI rozliczeń, zobacz [zbieranie wymaganych parametrów](anomaly-detector-container-howto.md#gathering-required-parameters). Aby uzyskać więcej informacji i pełną listę regionalnych punktów końcowych, zobacz [Niestandardowe nazwy poddomen dla usług Cognitive Services](../cognitive-services-custom-subdomains.md). |

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

Kontenery detektora anomalii nie używają instalacji wejściowych ani wyjściowych do przechowywania danych szkoleniowych lub usługowych. 

Dokładna składnia lokalizacji instalacji hosta różni się w zależności od systemu operacyjnego hosta. Ponadto lokalizacja instalacji [komputera-hosta](anomaly-detector-container-howto.md#the-host-computer)może być niedostępna z powodu konfliktu między uprawnieniami używanymi przez konto usługi Platformy Docker a uprawnieniami lokalizacji instalacji hosta. 

|Optional (Opcjonalność)| Nazwa | Typ danych | Opis |
|-------|------|-----------|-------------|
|Niedozwolone| `Input` | Ciąg | Kontenery detektora anomalii nie używają tego.|
|Optional (Opcjonalność)| `Output` | Ciąg | Miejsce docelowe mocowania wyjściowego. Wartością domyślną jest `/output`. Jest to lokalizacja dzienników. Obejmuje to dzienniki kontenerów. <br><br>Przykład:<br>`--mount type=bind,src=c:\output,target=/output`|

## <a name="example-docker-run-commands"></a>Przykładowe polecenia uruchamiania platformy dok. 

Poniższe przykłady używają ustawień konfiguracji, aby zilustrować sposób pisania i używania `docker run` poleceń.  Po uruchomieniu kontener będzie nadal działał, dopóki go nie [zatrzymasz.](anomaly-detector-container-howto.md#stop-the-container)

* **Znak kontynuacji wiersza:** Polecenia platformy Docker w poniższych `\`sekcjach używają ukośnika wstecznego, jako znaku kontynuacji wiersza dla powłoki bash. Zastąp lub usuń to na podstawie wymagań systemu operacyjnego hosta. Na przykład znakiem kontynuacji wiersza dla `^`okien jest cieszka, . Wymień ukośnik pleców na ciesznik. 
* **Kolejność argumentów:** Nie należy zmieniać kolejności argumentów, chyba że są bardzo zaznajomieni z kontenerami platformy Docker.

Zamień wartość `{}`w nawiasy , z własnymi wartościami:

| Symbol zastępczy | Wartość | Format lub przykład |
|-------------|-------|---|
| **{API_KEY}** | Klucz punktu końcowego `Anomaly Detector` zasobu `Anomaly Detector` na stronie Klucze platformy Azure. | `xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx` |
| **{ENDPOINT_URI}** | Wartość punktu końcowego rozliczeń jest `Anomaly Detector` dostępna na stronie Przegląd platformy Azure.| Zobacz [zbieranie wymaganych parametrów](anomaly-detector-container-howto.md#gathering-required-parameters) dla jawnych przykładów. |

[!INCLUDE [subdomains-note](../../../includes/cognitive-services-custom-subdomains-note.md)]

> [!IMPORTANT]
> Aby `Eula` `Billing`uruchomić `ApiKey` kontener, należy określić opcje i opcje; w przeciwnym razie kontener nie zostanie uruchomiony.  Aby uzyskać więcej informacji, zobacz [Rozliczenia](anomaly-detector-container-howto.md#billing).
> Wartość ApiKey jest **kluczem** ze strony kluczy zasobów usługi Azure Anomaly Detector. 

## <a name="anomaly-detector-container-docker-examples"></a>Przykłady kontenerów kontenerów anomalii

Poniższe przykłady platformy Docker są dla kontenera detektora anomalii. 

### <a name="basic-example"></a>Przykład podstawowy 

  ```Docker
  docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 \
  containerpreview.azurecr.io/microsoft/cognitive-services-anomaly-detector \
  Eula=accept \
  Billing={ENDPOINT_URI} \
  ApiKey={API_KEY} 
  ```

### <a name="logging-example-with-command-line-arguments"></a>Przykład rejestrowania z argumentami wiersza polecenia

  ```Docker
  docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 \
  containerpreview.azurecr.io/microsoft/cognitive-services-anomaly-detector \
  Eula=accept \
  Billing={ENDPOINT_URI} ApiKey={API_KEY} \
  Logging:Console:LogLevel:Default=Information
  ```

## <a name="next-steps"></a>Następne kroki

* [Wdrażanie kontenera detektora anomalii w wystąpieniach kontenerów platformy Azure](how-to/deploy-anomaly-detection-on-container-instances.md)
* [Dowiedz się więcej o usłudze INTERFEJSU API detektora anomalii](https://go.microsoft.com/fwlink/?linkid=2080698&clcid=0x409)
