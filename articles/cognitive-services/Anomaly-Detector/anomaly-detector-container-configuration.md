---
title: Jak skonfigurować kontener dla interfejsu API wykrywania anomalii
titleSuffix: Azure Cognitive Services
description: Środowisko uruchomieniowe kontenera interfejsu API wykrywania anomalii jest konfigurowane przy `docker run` użyciu argumentów polecenia. Ten kontener ma kilka wymaganych ustawień oraz kilka opcjonalnych ustawień.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: conceptual
ms.date: 06/19/2019
ms.author: dapine
ms.openlocfilehash: a2f743b2b7e63df8b81a1024b20e3e27a3f247a9
ms.sourcegitcommit: 13a289ba57cfae728831e6d38b7f82dae165e59d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/09/2019
ms.locfileid: "68932991"
---
# <a name="configure-anomaly-detector-containers"></a>Konfigurowanie kontenerów wykrywania anomalii

Środowisko uruchomieniowe kontenera **wykrywania anomalii** jest konfigurowane przy użyciu `docker run` argumentów polecenia. Ten kontener ma kilka wymaganych ustawień oraz kilka opcjonalnych ustawień. Kilka [przykłady](#example-docker-run-commands) polecenia są dostępne. Ustawienia dotyczące rozliczeń dotyczą tylko kontenera. 

## <a name="configuration-settings"></a>Ustawienia konfiguracji

Ten kontener ma następujące ustawienia konfiguracji:

|Wymagane|Ustawienie|Przeznaczenie|
|--|--|--|
|Yes|[ApiKey](#apikey-configuration-setting)|Używane do śledzenia informacji dotyczących rozliczeń.|
|Nie|[ApplicationInsights](#applicationinsights-setting)|Umożliwia dodanie [usługi Azure Application Insights](https://docs.microsoft.com/azure/application-insights) obsługi telemetrii do kontenera.|
|Yes|[Billing](#billing-configuration-setting)|Określa identyfikator URI punktu końcowego zasobu usługi na platformie Azure.|
|Tak|[Eula](#eula-setting)| Wskazuje, że zaakceptowano licencję dla kontenera.|
|Nie|[Fluentd](#fluentd-settings)|Zapisać dziennik i, opcjonalnie, metryki danych na serwerze Fluentd.|
|Nie|[Serwer proxy http](#http-proxy-credentials-settings)|Skonfiguruj serwer proxy HTTP do wykonywania żądań wychodzących.|
|Nie|[Logging](#logging-settings)|Udostępnia obsługę rejestrowania platformy ASP.NET Core dla kontenera. |
|Nie|[Mounts](#mount-settings)|Odczytywanie i zapisywanie danych z komputera hosta do kontenera i z kontenera do komputera hosta.|

> [!IMPORTANT]
> [ `ApiKey` ](#apikey-configuration-setting), [ `Billing` ](#billing-configuration-setting), I [ `Eula` ](#eula-setting) ustawienia są używane razem. Ponadto należy podać prawidłowe wartości dla wszystkich trzech ich; w przeciwnym razie Nie można uruchomić kontener. Aby uzyskać więcej informacji na temat tworzenia wystąpienia kontenera za pomocą tych ustawień konfiguracji, zobacz [rozliczeń](anomaly-detector-container-howto.md#billing).

## <a name="apikey-configuration-setting"></a>Ustawienie konfiguracji ApiKey

`ApiKey` Ustawienie umożliwia określenie klucza zasobów platformy Azure używane do śledzenia informacji rozliczeniowych dla kontenera. Należy określić wartość dla ApiKey, a wartość musi być prawidłowym kluczem dla zasobu _wykrywania anomalii_ określonego dla [`Billing`](#billing-configuration-setting) ustawienia konfiguracji.

To ustawienie można znaleźć w następujących miejscach:

* Azure Portal: **Detektor anomalii** Zarządzanie zasobami w obszarze **klucze**

## <a name="applicationinsights-setting"></a>Ustawienie dotycząca usługi Application Insights

[!INCLUDE [Container shared configuration ApplicationInsights settings](../../../includes/cognitive-services-containers-configuration-shared-settings-application-insights.md)]

## <a name="billing-configuration-setting"></a>Ustawienie konfiguracji rozliczeń

Ustawienie określa identyfikator URI punktu końcowego zasobu _wykrywania anomalii_ na platformie Azure używany do pomiaru informacji rozliczeniowych dla kontenera. `Billing` Należy określić wartość tego ustawienia konfiguracji, a wartość musi być prawidłowym identyfikatorem URI punktu końcowego dla zasobu _wykrywania anomalii_ na platformie Azure.

To ustawienie można znaleźć w następujących miejscach:

* Azure Portal: **Detektor anomalii** Omówienie, etykieta`Endpoint`

|Wymagane| Name (Nazwa) | Typ danych | Opis |
|--|------|-----------|-------------|
|Yes| `Billing` | Ciąg | Identyfikator URI punktu końcowego rozliczeń<br><br>Przykład:<br>`Billing=https://westus2.api.cognitive.microsoft.com` |

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

Kontenery wykrywania anomalii nie używają instalacji danych wejściowych lub wyjściowych do przechowywania danych szkoleniowych lub usług. 

Dokładna składnia lokalizację instalacji hosta różni się zależnie od systemu operacyjnego hosta. Ponadto lokalizacja instalacji [komputera hosta](anomaly-detector-container-howto.md#the-host-computer)może być niedostępna z powodu konfliktu między uprawnieniami używanymi przez konto usługi platformy Docker i uprawnieniami lokalizacji instalacji hosta. 

|Optional| Name (Nazwa) | Typ danych | Opis |
|-------|------|-----------|-------------|
|Niedozwolone| `Input` | String | Kontenery wykrywania anomalii nie używają tego.|
|Optional| `Output` | String | Miejsce docelowe instalacji danych wyjściowych. Wartość domyślna to `/output`. Jest to Lokalizacja dzienników. Dotyczy to również dzienników kontenerów. <br><br>Przykład:<br>`--mount type=bind,src=c:\output,target=/output`|

## <a name="example-docker-run-commands"></a>Przykład platformy docker, Uruchom polecenia 

W poniższych przykładach używane ustawienia konfiguracji, aby zilustrować, jak pisać i użyj `docker run` poleceń.  Po uruchomieniu kontenera będzie działać do momentu [zatrzymać](anomaly-detector-container-howto.md#stop-the-container) go.

* **Znak kontynuacji wiersza**: Polecenia platformy Docker w poniższych sekcjach używają ukośnika odwrotnego `\`, jako znaku kontynuacji wiersza dla powłoki bash. Zamień lub Usuń ten na podstawie wymagań systemu operacyjnego hosta. Na przykład znak kontynuacji wiersza dla systemu Windows jest karetką `^`. Zastąp ukośnik odwrotny. 
* **Kolejność argumentów**: Nie zmieniaj kolejności argumentów, o ile nie znasz już kontenerów platformy Docker.

Zastąp wartość w nawiasach `{}`, z własnymi wartościami:

| Symbol zastępczy | Wartość | Format lub przykład |
|-------------|-------|---|
|{API_KEY} | Klucz punktu końcowego zasobu wykrywania anomalii. |xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx|
|{ENDPOINT_URI} | Wartość punktu końcowego rozliczenia włącznie z regionem.|`https://westus2.api.cognitive.microsoft.com`|

> [!IMPORTANT]
> `Eula`, `Billing`, I `ApiKey` opcje muszą być określone w celu uruchomienia kontenera; w przeciwnym razie nie uruchamia się kontener.  Aby uzyskać więcej informacji, zobacz [rozliczeń](anomaly-detector-container-howto.md#billing).
> Wartość ApiKey jest **kluczem** ze strony klucze zasobów wykrywania anomalii platformy Azure. 

## <a name="anomaly-detector-container-docker-examples"></a>Przykłady platformy Docker dotyczącej aparatu wykrywania anomalii

Poniższe przykłady platformy Docker dotyczą kontenera wykrywania anomalii. 

### <a name="basic-example"></a>Podstawowy przykład 

  ```Docker
  docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 \
  containerpreview.azurecr.io/microsoft/cognitive-services-anomaly-detector \
  Eula=accept \
  Billing={ENDPOINT_URI} \
  ApiKey={API_KEY} 
  ```

### <a name="logging-example-with-command-line-arguments"></a>Przykład rejestrowania za pomocą argumentów wiersza polecenia.

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
