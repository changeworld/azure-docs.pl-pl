---
title: Jak skonfigurować kontener dla interfejsu API wykrywania anomalii
titleSuffix: Azure Cognitive Services
description: Środowisko uruchomieniowe kontenera interfejsu API wykrywania anomalii jest konfigurowane przy użyciu argumentów polecenia `docker run`. Ten kontener ma kilka wymaganych ustawień oraz kilka opcjonalnych ustawień.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: conceptual
ms.date: 11/07/2019
ms.author: dapine
ms.openlocfilehash: f7e04a16fa35d492b8e5e6c53a05220e8b96a38a
ms.sourcegitcommit: 018e3b40e212915ed7a77258ac2a8e3a660aaef8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/07/2019
ms.locfileid: "73795847"
---
# <a name="configure-anomaly-detector-containers"></a>Konfigurowanie kontenerów narzędzia do wykrywania anomalii

Środowisko uruchomieniowe kontenera **wykrywania anomalii** jest konfigurowane przy użyciu argumentów polecenia `docker run`. Ten kontener ma kilka wymaganych ustawień oraz kilka opcjonalnych ustawień. Kilka [przykładów](#example-docker-run-commands) polecenia jest dostępnych. Ustawienia dotyczące rozliczeń dotyczą tylko kontenera. 

## <a name="configuration-settings"></a>Ustawienia konfiguracji

Ten kontener ma następujące ustawienia konfiguracji:

|Wymagany|Ustawienie|Przeznaczenie|
|--|--|--|
|Tak|[ApiKey](#apikey-configuration-setting)|Służy do śledzenia informacji dotyczących rozliczeń.|
|Nie|[ApplicationInsights](#applicationinsights-setting)|Umożliwia dodanie obsługi telemetrii [usługi Azure Application Insights](https://docs.microsoft.com/azure/application-insights) do kontenera.|
|Tak|[Rozliczenia](#billing-configuration-setting)|Określa identyfikator URI punktu końcowego zasobu usługi na platformie Azure.|
|Tak|[Umowy](#eula-setting)| Wskazuje, że licencja dla kontenera została zaakceptowana.|
|Nie|[Pozostało](#fluentd-settings)|Zapisz dziennik i, opcjonalnie, dane metryki na serwerze z systemem.|
|Nie|[Serwer proxy http](#http-proxy-credentials-settings)|Skonfiguruj serwer proxy HTTP do wykonywania żądań wychodzących.|
|Nie|[Rejestrować](#logging-settings)|Zapewnia obsługę rejestrowania ASP.NET Core dla kontenera. |
|Nie|[Instaluje](#mount-settings)|Odczytaj i Zapisz dane z komputera hosta do kontenera oraz z kontenera z powrotem do komputera hosta.|

> [!IMPORTANT]
> Ustawienia [`ApiKey`](#apikey-configuration-setting), [`Billing`](#billing-configuration-setting)i [`Eula`](#eula-setting) są używane razem i należy podać prawidłowe wartości dla wszystkich trzech z nich; w przeciwnym razie kontener nie zostanie uruchomiony. Aby uzyskać więcej informacji na temat tworzenia wystąpienia kontenera przy użyciu tych ustawień konfiguracji, zobacz [rozliczenia](anomaly-detector-container-howto.md#billing).

## <a name="apikey-configuration-setting"></a>Ustawienie konfiguracji ApiKey

Ustawienie `ApiKey` określa klucz zasobów platformy Azure służący do śledzenia informacji rozliczeniowych dla kontenera. Należy określić wartość dla ApiKey, a wartość musi być prawidłowym kluczem dla zasobu _wykrywania anomalii_ określonego dla ustawienia konfiguracji [`Billing`](#billing-configuration-setting) .

To ustawienie można znaleźć w następujących miejscach:

* Azure Portal: zarządzanie zasobami **wykrywania anomalii** w obszarze **klucze**

## <a name="applicationinsights-setting"></a>Ustawienie ApplicationInsights

[!INCLUDE [Container shared configuration ApplicationInsights settings](../../../includes/cognitive-services-containers-configuration-shared-settings-application-insights.md)]

## <a name="billing-configuration-setting"></a>Ustawienie konfiguracji rozliczeń

Ustawienie `Billing` określa identyfikator URI punktu końcowego zasobu _wykrywania anomalii_ na platformie Azure używany do pomiaru informacji rozliczeniowych dla kontenera. Należy określić wartość tego ustawienia konfiguracji, a wartość musi być prawidłowym identyfikatorem URI punktu końcowego dla zasobu _wykrywania anomalii_ na platformie Azure.

To ustawienie można znaleźć w następujących miejscach:

* Azure Portal: Omówienie narzędzia **wykrywania anomalii** z etykietą `Endpoint`

|Wymagany| Nazwa | Typ danych | Opis |
|--|------|-----------|-------------|
|Tak| `Billing` | Ciąg | Identyfikator URI punktu końcowego rozliczenia. Aby uzyskać więcej informacji na temat uzyskiwania identyfikatora URI rozliczeń, zobacz [zbieranie wymaganych parametrów](anomaly-detector-container-howto.md#gathering-required-parameters). Aby uzyskać więcej informacji i pełną listę regionalnych punktów końcowych, zobacz [niestandardowe nazwy domen poddomen dla Cognitive Services](../cognitive-services-custom-subdomains.md). |

## <a name="eula-setting"></a>Ustawienie umowy EULA

[!INCLUDE [Container shared configuration eula settings](../../../includes/cognitive-services-containers-configuration-shared-settings-eula.md)]

## <a name="fluentd-settings"></a>Ustawienia pozostały

[!INCLUDE [Container shared configuration fluentd settings](../../../includes/cognitive-services-containers-configuration-shared-settings-fluentd.md)]

## <a name="http-proxy-credentials-settings"></a>Ustawienia poświadczeń serwera proxy http

[!INCLUDE [Container shared configuration fluentd settings](../../../includes/cognitive-services-containers-configuration-shared-settings-http-proxy.md)]

## <a name="logging-settings"></a>Ustawienia rejestrowania
 
[!INCLUDE [Container shared configuration logging settings](../../../includes/cognitive-services-containers-configuration-shared-settings-logging.md)]


## <a name="mount-settings"></a>Ustawienia instalacji

Użyj instalacji powiązań, aby odczytywać i zapisywać dane w kontenerze i z niego. Można określić instalację wejściową lub instalację wyjściową, określając opcję `--mount` w poleceniu [Docker Run](https://docs.docker.com/engine/reference/commandline/run/) .

Kontenery wykrywania anomalii nie używają instalacji danych wejściowych lub wyjściowych do przechowywania danych szkoleniowych lub usług. 

Dokładna składnia lokalizacji instalacji hosta różni się w zależności od systemu operacyjnego hosta. Ponadto lokalizacja instalacji [komputera hosta](anomaly-detector-container-howto.md#the-host-computer)może być niedostępna z powodu konfliktu między uprawnieniami używanymi przez konto usługi platformy Docker i uprawnieniami lokalizacji instalacji hosta. 

|Optional (Opcjonalność)| Nazwa | Typ danych | Opis |
|-------|------|-----------|-------------|
|Niedozwolone| `Input` | Ciąg | Kontenery wykrywania anomalii nie używają tego.|
|Optional (Opcjonalność)| `Output` | Ciąg | Obiekt docelowy instalacji wyjściowej. Wartość domyślna to `/output`. Jest to lokalizacja dzienników. Dotyczy to również dzienników kontenerów. <br><br>Przykład:<br>`--mount type=bind,src=c:\output,target=/output`|

## <a name="example-docker-run-commands"></a>Przykładowe polecenia uruchamiania platformy Docker 

W poniższych przykładach użyto ustawień konfiguracji, aby zilustrować sposób pisania i używania poleceń `docker run`.  Po uruchomieniu kontenera kontynuuje działanie, dopóki nie zostanie [zatrzymane](anomaly-detector-container-howto.md#stop-the-container) .

* **Znak kontynuacji wiersza**: polecenia platformy Docker w poniższych sekcjach używają ukośnika odwrotnego, `\`jako znaku kontynuacji wiersza dla powłoki bash. Zastąp lub usuń to w zależności od wymagań systemu operacyjnego hosta. Na przykład znak kontynuacji wiersza dla systemu Windows jest karetką `^`. Zastąp ukośnik odwrotny. 
* **Kolejność**argumentów: nie zmieniaj kolejności argumentów, o ile nie znasz już kontenerów platformy Docker.

Zastąp wartość w nawiasach, `{}`z własnymi wartościami:

| Symbol zastępczy | Wartość | Format lub przykład |
|-------------|-------|---|
| **{API_KEY}** | Klucz punktu końcowego zasobu `Anomaly Detector` na stronie klucze `Anomaly Detector` platformy Azure. | `xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx` |
| **{ENDPOINT_URI}** | Wartość punktu końcowego rozliczenia jest dostępna na stronie Przegląd `Anomaly Detector` platformy Azure.| Zobacz [zbieranie wymaganych parametrów](anomaly-detector-container-howto.md#gathering-required-parameters) dla jawnych przykładów. |

[!INCLUDE [subdomains-note](../../../includes/cognitive-services-custom-subdomains-note.md)]

> [!IMPORTANT]
> Aby można było uruchomić kontener, należy określić opcje `Eula`, `Billing`i `ApiKey`. w przeciwnym razie kontener nie zostanie uruchomiony.  Aby uzyskać więcej informacji, zobacz [rozliczenia](anomaly-detector-container-howto.md#billing).
> Wartość ApiKey jest **kluczem** ze strony klucze zasobów wykrywania anomalii platformy Azure. 

## <a name="anomaly-detector-container-docker-examples"></a>Przykłady platformy Docker dotyczącej aparatu wykrywania anomalii

Poniższe przykłady platformy Docker dotyczą kontenera wykrywania anomalii. 

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

* [Wdróż kontener wykrywania anomalii w Azure Container Instances](how-to/deploy-anomaly-detection-on-container-instances.md)
* [Dowiedz się więcej o usłudze interfejsu API wykrywania anomalii](https://go.microsoft.com/fwlink/?linkid=2080698&clcid=0x409)
