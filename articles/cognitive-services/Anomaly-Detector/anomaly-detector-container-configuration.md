---
title: Jak skonfigurować kontener dla interfejsu API wykrywanie anomalii
titleSuffix: Azure Cognitive Services
description: Środowisko uruchomieniowe kontenera interfejsu API wykrywanie anomalii jest konfigurowana przy użyciu `docker run` argumenty polecenia. Ten kontener ma wymagane ustawienia, wraz z kilku ustawień opcjonalnych.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: conceptual
ms.date: 06/19/2019
ms.author: dapine
ms.openlocfilehash: cb0a12df6696e76050d4c53bd75e07134b3dc27c
ms.sourcegitcommit: dad277fbcfe0ed532b555298c9d6bc01fcaa94e2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/10/2019
ms.locfileid: "67721717"
---
# <a name="configure-anomaly-detector-containers"></a>Skonfiguruj wykrywanie anomalii kontenerów

**Wykrywanie anomalii** środowisko uruchomieniowe kontenera jest konfigurowana przy użyciu `docker run` argumenty polecenia. Ten kontener ma wymagane ustawienia, wraz z kilku ustawień opcjonalnych. Kilka [przykłady](#example-docker-run-commands) polecenia są dostępne. Ustawienia specyficzne dla kontenera są ustawienia rozliczeń. 

# <a name="configuration-settings"></a>Ustawienia konfiguracji

Ten kontener ma następujące ustawienia konfiguracji:

|Wymagane|Ustawienie|Przeznaczenie|
|--|--|--|
|Yes|[ApiKey](#apikey-configuration-setting)|Używane do śledzenia informacji dotyczących rozliczeń.|
|Nie|[ApplicationInsights](#applicationinsights-setting)|Umożliwia dodanie [usługi Azure Application Insights](https://docs.microsoft.com/azure/application-insights) obsługi telemetrii do kontenera.|
|Yes|[Billing](#billing-configuration-setting)|Określa identyfikator URI punktu końcowego zasobu usługi na platformie Azure.|
|Tak|[Eula](#eula-setting)| Wskazuje, że zaakceptowano licencję dla kontenera.|
|Nie|[Fluentd](#fluentd-settings)|Zapisać dziennik i, opcjonalnie, metryki danych na serwerze Fluentd.|
|Nie|[Serwer Proxy http](#http-proxy-credentials-settings)|Skonfiguruj serwer proxy HTTP dla żądania wychodzącego.|
|Nie|[Logging](#logging-settings)|Udostępnia obsługę rejestrowania platformy ASP.NET Core dla kontenera. |
|Nie|[Mounts](#mount-settings)|Odczytywanie i zapisywanie danych z komputera hosta do kontenera i z kontenera do komputera hosta.|

> [!IMPORTANT]
> [ `ApiKey` ](#apikey-configuration-setting), [ `Billing` ](#billing-configuration-setting), I [ `Eula` ](#eula-setting) ustawienia są używane razem. Ponadto należy podać prawidłowe wartości dla wszystkich trzech ich; w przeciwnym razie Nie można uruchomić kontener. Aby uzyskać więcej informacji na temat tworzenia wystąpienia kontenera za pomocą tych ustawień konfiguracji, zobacz [rozliczeń](anomaly-detector-container-howto.md#billing).

## <a name="apikey-configuration-setting"></a>Ustawienie konfiguracji ApiKey

`ApiKey` Ustawienie umożliwia określenie klucza zasobów platformy Azure używane do śledzenia informacji rozliczeniowych dla kontenera. Należy określić wartość dla ApiKey i wartość musi być prawidłowy klucz dla _wykrywanie anomalii_ zasób określony dla [ `Billing` ](#billing-configuration-setting) ustawienia konfiguracji.

To ustawienie można znaleźć w tym miejscu następujące:

* Azure Portal: **Wykrywanie anomalii** zarządzanie zasobami w obszarze **kluczy**

## <a name="applicationinsights-setting"></a>Ustawienie dotycząca usługi Application Insights

[!INCLUDE [Container shared configuration ApplicationInsights settings](../../../includes/cognitive-services-containers-configuration-shared-settings-application-insights.md)]

## <a name="billing-configuration-setting"></a>Ustawienie konfiguracji rozliczeń

`Billing` Ustawienie określa identyfikator URI punktu końcowego z _wykrywanie anomalii_ zasobów na platformie Azure używane do pomiarów informacji rozliczeniowych dla kontenera. Należy określić wartość dla tego ustawienia konfiguracji, a wartość musi być prawidłowy identyfikator URI punktu końcowego dla _wykrywanie anomalii_ zasobów na platformie Azure.

To ustawienie można znaleźć w tym miejscu następujące:

* Azure Portal: **Wykrywanie anomalii** Przegląd, etykietą `Endpoint`

|Wymagane| Name (Nazwa) | Typ danych | Opis |
|--|------|-----------|-------------|
|Yes| `Billing` | Ciąg | Identyfikator URI punktu końcowego rozliczeń<br><br>Przykład:<br>`Billing=https://westus2.api.cognitive.microsoft.com` |

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

Kontenery wykrywanie anomalii, nie używaj danych wejściowych lub danych wyjściowych instaluje do przechowywania szkolenie i usługi danych. 

Dokładna składnia lokalizację instalacji hosta różni się zależnie od systemu operacyjnego hosta. Ponadto [komputerze-hoście](anomaly-detector-container-howto.md#the-host-computer)w lokalizacji instalacji może być niedostępna z powodu konfliktu między uprawnienia użyte dla konta usługi Docker i hosta instalacji uprawnienia do lokalizacji. 

|Optional| Name (Nazwa) | Typ danych | Opis |
|-------|------|-----------|-------------|
|Niedozwolone| `Input` | Ciąg | Kontenery wykrywanie anomalii, nie należy używać tego.|
|Optional| `Output` | String | Miejsce docelowe instalacji danych wyjściowych. Wartość domyślna to `/output`. Jest to Lokalizacja dzienników. W tym dzienniki kontenerów. <br><br>Przykład:<br>`--mount type=bind,src=c:\output,target=/output`|

## <a name="example-docker-run-commands"></a>Przykład platformy docker, Uruchom polecenia 

W poniższych przykładach używane ustawienia konfiguracji, aby zilustrować, jak pisać i użyj `docker run` poleceń.  Po uruchomieniu kontenera będzie działać do momentu [zatrzymać](anomaly-detector-container-howto.md#stop-the-container) go.

* **Znak kontynuacji wiersza**: Platformy Docker w poniższych sekcjach użyto ukośnika, `\`, jako znak kontynuacji wiersza dla powłoki bash. Zamień lub Usuń ten na podstawie wymagań systemu operacyjnego hosta. Na przykład znak kontynuacji wiersza dla systemu windows jest znak daszka `^`. Zastąp kreski ułamkowej odwróconej karetki. 
* **Kolejność argumentów**: Nie należy zmieniać kolejność argumentów, jeśli nie znasz bardzo kontenerów platformy Docker.

Zastąp wartości w nawiasach, `{}`, przy użyciu własnych wartości:

| Symbol zastępczy | Wartość | Format lub przykład |
|-------------|-------|---|
|{BILLING_KEY} | Klucz punktu końcowego zasobu wykrywanie anomalii. |xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx|
|{BILLING_ENDPOINT_URI} | Rozliczeń wartości punktu końcowego, w tym regionie.|`https://westus2.api.cognitive.microsoft.com`|

> [!IMPORTANT]
> `Eula`, `Billing`, I `ApiKey` opcje muszą być określone w celu uruchomienia kontenera; w przeciwnym razie nie uruchamia się kontener.  Aby uzyskać więcej informacji, zobacz [rozliczeń](anomaly-detector-container-howto.md#billing).
> Wartość ApiKey **klucz** ze strony klucze w usłudze Azure Resource wykrywanie anomalii. 

## <a name="anomaly-detector-container-docker-examples"></a>Przykłady platformy Docker container wykrywanie anomalii

Są w poniższych przykładach platformy Docker dla kontenera, wykrywanie anomalii. 

### <a name="basic-example"></a>Podstawowy przykład 

  ```Docker
  docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 \
  containerpreview.azurecr.io/microsoft/cognitive-services-anomaly-detector \
  Eula=accept \
  Billing={BILLING_ENDPOINT_URI} \
  ApiKey={BILLING_KEY} 
  ```

### <a name="logging-example-with-command-line-arguments"></a>Przykład rejestrowania za pomocą argumentów wiersza polecenia.

  ```Docker
  docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 \
  containerpreview.azurecr.io/microsoft/cognitive-services-anomaly-detector \
  Eula=accept \
  Billing={BILLING_ENDPOINT_URI} ApiKey={BILLING_KEY} \
  Logging:Console:LogLevel:Default=Information
  ```
