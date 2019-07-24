---
title: Konfigurowanie kontenerów — przetwarzanie obrazów
titlesuffix: Azure Cognitive Services
description: Skonfiguruj różne ustawienia kontenerów Rozpoznawanie tekstu w przetwarzanie obrazów.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 06/19/2019
ms.author: dapine
ms.custom: seodec18
ms.openlocfilehash: 90358d54077a0c320e8d3186e806b8a61d951c82
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/18/2019
ms.locfileid: "68321337"
---
# <a name="configure-recognize-text-docker-containers"></a>Konfigurowanie Rozpoznawanie tekstu kontenerów platformy Docker

Środowisko uruchomieniowe kontenera **rozpoznawanie tekstu** jest konfigurowane przy użyciu `docker run` argumentów polecenia. Ten kontener ma kilka wymaganych ustawień oraz kilka opcjonalnych ustawień. Kilka [przykłady](#example-docker-run-commands) polecenia są dostępne. Ustawienia dotyczące rozliczeń dotyczą tylko kontenera. 

## <a name="configuration-settings"></a>Ustawienia konfiguracji

[!INCLUDE [Container shared configuration settings table](../../../includes/cognitive-services-containers-configuration-shared-settings-table.md)]

> [!IMPORTANT]
> [ `ApiKey` ](#apikey-configuration-setting), [ `Billing` ](#billing-configuration-setting), I [ `Eula` ](#eula-setting) ustawienia są używane razem. Ponadto należy podać prawidłowe wartości dla wszystkich trzech ich; w przeciwnym razie Nie można uruchomić kontener. Aby uzyskać więcej informacji na temat tworzenia wystąpienia kontenera za pomocą tych ustawień konfiguracji, zobacz [rozliczeń](computer-vision-how-to-install-containers.md).

## <a name="apikey-configuration-setting"></a>Ustawienie konfiguracji ApiKey

Ustawienie określa klucz zasobów platformy `Cognitive Services` Azure służący do śledzenia informacji rozliczeniowych dla kontenera. `ApiKey` Należy określić wartość ApiKey, a wartość musi być prawidłowym kluczem dla zasobu _Cognitive Services_ określonego dla [`Billing`](#billing-configuration-setting) ustawienia konfiguracji.

To ustawienie można znaleźć w następujących miejscach:

* Azure Portal: **Cognitive Services** Zarządzanie zasobami w obszarze **klucze**

## <a name="applicationinsights-setting"></a>Ustawienie dotycząca usługi Application Insights

[!INCLUDE [Container shared configuration ApplicationInsights settings](../../../includes/cognitive-services-containers-configuration-shared-settings-application-insights.md)]

## <a name="billing-configuration-setting"></a>Ustawienie konfiguracji rozliczeń

Ustawienie określa identyfikator URI punktu końcowego zasobu Cognitive Services na platformie Azure używany do mierzenia informacji rozliczeniowych dla kontenera.  `Billing` Należy określić wartość tego ustawienia konfiguracji, a wartość musi być prawidłowym identyfikatorem URI punktu końcowego dla zasobu _Cognitive Services_ na platformie Azure. Kontener zgłasza użycie co 10 do 15 minut.

To ustawienie można znaleźć w następujących miejscach:

* Azure Portal: **Cognitive Services** Omówienie, etykieta`Endpoint`

Pamiętaj, aby dodać `vision/v1.0` Routing do identyfikatora URI punktu końcowego, jak pokazano w poniższej tabeli. 

|Wymagane| Name (Nazwa) | Typ danych | Opis |
|--|------|-----------|-------------|
|Yes| `Billing` | Ciąg | Identyfikator URI punktu końcowego rozliczeń<br><br>Przykład:<br>`Billing=https://westcentralus.api.cognitive.microsoft.com/vision/v1.0` |

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

Kontenery przetwarzanie obrazów nie używają instalacji danych wejściowych lub wyjściowych do przechowywania danych szkoleniowych lub usług. 

Dokładna składnia lokalizację instalacji hosta różni się zależnie od systemu operacyjnego hosta. Ponadto lokalizacja instalacji [komputera hosta](computer-vision-how-to-install-containers.md#the-host-computer)może być niedostępna z powodu konfliktu między uprawnieniami używanymi przez konto usługi platformy Docker i uprawnieniami lokalizacji instalacji hosta. 

|Optional| Name (Nazwa) | Typ danych | Opis |
|-------|------|-----------|-------------|
|Niedozwolone| `Input` | Ciąg | Kontenery przetwarzanie obrazów nie używają tego.|
|Optional| `Output` | String | Miejsce docelowe instalacji danych wyjściowych. Wartość domyślna to `/output`. Jest to Lokalizacja dzienników. Dotyczy to również dzienników kontenerów. <br><br>Przykład:<br>`--mount type=bind,src=c:\output,target=/output`|

## <a name="example-docker-run-commands"></a>Przykład platformy docker, Uruchom polecenia 

W poniższych przykładach używane ustawienia konfiguracji, aby zilustrować, jak pisać i użyj `docker run` poleceń.  Po uruchomieniu kontenera będzie działać do momentu [zatrzymać](computer-vision-how-to-install-containers.md#stop-the-container) go.

* **Znak kontynuacji wiersza**: Polecenia platformy Docker w poniższych sekcjach używają ukośnika odwrotnego `\`, jako znaku kontynuacji wiersza. Zamień lub Usuń ten na podstawie wymagań systemu operacyjnego hosta. 
* **Kolejność argumentów**: Nie zmieniaj kolejności argumentów, o ile nie znasz już kontenerów platformy Docker.

Pamiętaj, aby dodać `vision/v1.0` Routing do identyfikatora URI punktu końcowego, jak pokazano w poniższej tabeli. 

Zastąp {_argument_name_} własnymi wartościami:

| Symbol zastępczy | Wartość | Format lub przykład |
|-------------|-------|---|
|{API_KEY} | Klucz punktu końcowego zasobu Cognitive Services. |xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx|
|{ENDPOINT_URI} | Wartość punktu końcowego rozliczenia włącznie z regionem.|`https://westcentralus.api.cognitive.microsoft.com/vision/v1.0`|

> [!IMPORTANT]
> `Eula`, `Billing`, I `ApiKey` opcje muszą być określone w celu uruchomienia kontenera; w przeciwnym razie nie uruchamia się kontener.  Aby uzyskać więcej informacji, zobacz [rozliczeń](computer-vision-how-to-install-containers.md#billing).
> Wartość ApiKey jest **kluczem** ze strony klucze zasobów platformy `Cognitive Services` Azure. 

## <a name="recognize-text-container-docker-examples"></a>Rozpoznaj przykłady dotyczące platformy Docker kontenera tekstu

Poniższe przykłady platformy Docker dotyczą kontenera rozpoznawania tekstu. 

### <a name="basic-example"></a>Podstawowy przykład 

  ```
  docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 \
  containerpreview.azurecr.io/microsoft/cognitive-services-recognize-text \
  Eula=accept \
  Billing={ENDPOINT_URI} \
  ApiKey={API_KEY} 
  ```

### <a name="logging-example"></a>Przykład rejestrowania 

  ```
  docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 \
  containerpreview.azurecr.io/microsoft/cognitive-services-recognize-text \
  Eula=accept \
  Billing={ENDPOINT_URI} \
  ApiKey={API_KEY} \
  Logging:Console:LogLevel:Default=Information
  ```

## <a name="next-steps"></a>Następne kroki

* Przegląd [sposobu instalowania i uruchamiania kontenerów](computer-vision-how-to-install-containers.md)
