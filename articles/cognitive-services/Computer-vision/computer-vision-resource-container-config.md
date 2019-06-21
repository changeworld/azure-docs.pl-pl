---
title: Konfigurowanie containers — przetwarzanie obrazów
titlesuffix: Azure Cognitive Services
description: Skonfigurować różne ustawienia dla kontenerów rozpoznawanie tekstu w wizualizacji komputerowej.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 06/19/2019
ms.author: dapine
ms.custom: seodec18
ms.openlocfilehash: 4613b576b444059d448cf1094284f2a68e6c31a8
ms.sourcegitcommit: a52d48238d00161be5d1ed5d04132db4de43e076
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/20/2019
ms.locfileid: "67275140"
---
# <a name="configure-recognize-text-docker-containers"></a>Konfigurowanie kontenerów Docker rozpoznawanie tekstu

**Rozpoznaj tekst** środowisko uruchomieniowe kontenera jest konfigurowana przy użyciu `docker run` argumenty polecenia. Ten kontener ma wymagane ustawienia, wraz z kilku ustawień opcjonalnych. Kilka [przykłady](#example-docker-run-commands) polecenia są dostępne. Ustawienia specyficzne dla kontenera są ustawienia rozliczeń. 

## <a name="configuration-settings"></a>Ustawienia konfiguracji

[!INCLUDE [Container shared configuration settings table](../../../includes/cognitive-services-containers-configuration-shared-settings-table.md)]

> [!IMPORTANT]
> [ `ApiKey` ](#apikey-configuration-setting), [ `Billing` ](#billing-configuration-setting), I [ `Eula` ](#eula-setting) ustawienia są używane razem. Ponadto należy podać prawidłowe wartości dla wszystkich trzech ich; w przeciwnym razie Nie można uruchomić kontener. Aby uzyskać więcej informacji na temat tworzenia wystąpienia kontenera za pomocą tych ustawień konfiguracji, zobacz [rozliczeń](computer-vision-how-to-install-containers.md).

## <a name="apikey-configuration-setting"></a>Ustawienie konfiguracji ApiKey

`ApiKey` Ustawienie umożliwia określenie Azure `Cognitive Services` klucz zasobu używane do śledzenia informacji rozliczeniowych dla kontenera. Należy określić wartość dla ApiKey i wartość musi być prawidłowy klucz dla _usług Cognitive Services_ zasób określony dla [ `Billing` ](#billing-configuration-setting) ustawienia konfiguracji.

To ustawienie można znaleźć w tym miejscu następujące:

* Witryna Azure Portal: **Usługi cognitive Services** zarządzanie zasobami w obszarze **kluczy**

## <a name="applicationinsights-setting"></a>Ustawienie dotycząca usługi Application Insights

[!INCLUDE [Container shared configuration ApplicationInsights settings](../../../includes/cognitive-services-containers-configuration-shared-settings-application-insights.md)]

## <a name="billing-configuration-setting"></a>Ustawienie konfiguracji rozliczeń

`Billing` Ustawienie określa identyfikator URI punktu końcowego z _usług Cognitive Services_ zasobów na platformie Azure używane do pomiarów informacji rozliczeniowych dla kontenera. Należy określić wartość dla tego ustawienia konfiguracji, a wartość musi być prawidłowy identyfikator URI punktu końcowego dla _usług Cognitive Services_ zasobów na platformie Azure. Kontener raportów użycia dotyczących co 10 do 15 minut.

To ustawienie można znaleźć w tym miejscu następujące:

* Witryna Azure Portal: **Usługi cognitive Services** Przegląd, etykietą `Endpoint`

Pamiętaj, aby dodać `vision/v1.0` routingu do identyfikator URI punktu końcowego, jak pokazano w poniższej tabeli. 

|Wymagane| Name (Nazwa) | Typ danych | Opis |
|--|------|-----------|-------------|
|Yes| `Billing` | Ciąg | Identyfikator URI punktu końcowego rozliczeń<br><br>Przykład:<br>`Billing=https://westcentralus.api.cognitive.microsoft.com/vision/v1.0` |

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

Kontenery przetwarzania obrazów, nie używaj danych wejściowych lub danych wyjściowych instaluje do przechowywania szkolenie i usługi danych. 

Dokładna składnia lokalizację instalacji hosta różni się zależnie od systemu operacyjnego hosta. Ponadto [komputerze-hoście](computer-vision-how-to-install-containers.md#the-host-computer)w lokalizacji instalacji może być niedostępna z powodu konfliktu między uprawnienia użyte dla konta usługi Docker i hosta instalacji uprawnienia do lokalizacji. 

|Optional (Opcjonalność)| Name (Nazwa) | Typ danych | Opis |
|-------|------|-----------|-------------|
|Niedozwolone| `Input` | String | Kontenery przetwarzania komputera należy używać tego.|
|Optional (Opcjonalność)| `Output` | String | Miejsce docelowe instalacji danych wyjściowych. Wartość domyślna to `/output`. Jest to Lokalizacja dzienników. W tym dzienniki kontenerów. <br><br>Przykład:<br>`--mount type=bind,src=c:\output,target=/output`|

## <a name="example-docker-run-commands"></a>Przykład platformy docker, Uruchom polecenia 

W poniższych przykładach używane ustawienia konfiguracji, aby zilustrować, jak pisać i użyj `docker run` poleceń.  Po uruchomieniu kontenera będzie działać do momentu [zatrzymać](computer-vision-how-to-install-containers.md#stop-the-container) go.

* **Znak kontynuacji wiersza**: Platformy Docker w poniższych sekcjach użyto ukośnika, `\`, jako znak kontynuacji wiersza. Zamień lub Usuń ten na podstawie wymagań systemu operacyjnego hosta. 
* **Kolejność argumentów**: Nie należy zmieniać kolejność argumentów, jeśli nie znasz bardzo kontenerów platformy Docker.

Pamiętaj, aby dodać `vision/v1.0` routingu do identyfikator URI punktu końcowego, jak pokazano w poniższej tabeli. 

Zastąp {_argument_name_} własnymi wartościami:

| Symbol zastępczy | Wartość | Format lub przykład |
|-------------|-------|---|
|{BILLING_KEY} | Klucz punktu końcowego zasobu usług Cognitive Services. |xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx|
|{BILLING_ENDPOINT_URI} | Rozliczeń wartości punktu końcowego, w tym regionie.|`https://westcentralus.api.cognitive.microsoft.com/vision/v1.0`|

> [!IMPORTANT]
> `Eula`, `Billing`, I `ApiKey` opcje muszą być określone w celu uruchomienia kontenera; w przeciwnym razie nie uruchamia się kontener.  Aby uzyskać więcej informacji, zobacz [rozliczeń](computer-vision-how-to-install-containers.md#billing).
> Wartość ApiKey **klucz** z platformy Azure `Cognitive Services` strony klucze zasobu. 

## <a name="recognize-text-container-docker-examples"></a>Rozpoznawanie tekstu kontenera platformy Docker przykłady

Są w poniższych przykładach platformy Docker dla kontenera, rozpoznawanie tekstu. 

### <a name="basic-example"></a>Podstawowy przykład 

  ```
  docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 \
  containerpreview.azurecr.io/microsoft/cognitive-services-recognize-text \
  Eula=accept \
  Billing={BILLING_ENDPOINT_URI} \
  ApiKey={BILLING_KEY} 
  ```

### <a name="logging-example"></a>Przykład rejestrowania 

  ```
  docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 \
  containerpreview.azurecr.io/microsoft/cognitive-services-recognize-text \
  Eula=accept \
  Billing={BILLING_ENDPOINT_URI} \
  ApiKey={BILLING_KEY} \
  Logging:Console:LogLevel:Default=Information
  ```

## <a name="next-steps"></a>Kolejne kroki

* Przegląd [sposobu instalowania i uruchamiania kontenerów](computer-vision-how-to-install-containers.md)
