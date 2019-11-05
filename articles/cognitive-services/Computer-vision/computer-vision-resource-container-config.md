---
title: Konfigurowanie kontenerów — przetwarzanie obrazów
titleSuffix: Azure Cognitive Services
description: Skonfiguruj różne ustawienia kontenerów Rozpoznawanie tekstu w przetwarzanie obrazów.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: dapine
ms.custom: seodec18
ms.openlocfilehash: 1df8199abbbc195db873ab3da515cb1dd5fe9761
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73484090"
---
# <a name="configure-computer-vision-docker-containers"></a>Konfigurowanie przetwarzanie obrazów kontenerów platformy Docker

Środowisko uruchomieniowe kontenera przetwarzanie obrazów można skonfigurować za pomocą argumentów polecenia `docker run`. Ten kontener ma kilka wymaganych ustawień oraz kilka opcjonalnych ustawień. Kilka [przykładów](#example-docker-run-commands) polecenia jest dostępnych. Ustawienia dotyczące rozliczeń dotyczą tylko kontenera. 

## <a name="configuration-settings"></a>Ustawienia konfiguracji

[!INCLUDE [Container shared configuration settings table](../../../includes/cognitive-services-containers-configuration-shared-settings-table.md)]

> [!IMPORTANT]
> Ustawienia [`ApiKey`](#apikey-configuration-setting), [`Billing`](#billing-configuration-setting)i [`Eula`](#eula-setting) są używane razem i należy podać prawidłowe wartości dla wszystkich trzech z nich; w przeciwnym razie kontener nie zostanie uruchomiony. Aby uzyskać więcej informacji na temat tworzenia wystąpienia kontenera przy użyciu tych ustawień konfiguracji, zobacz [rozliczenia](computer-vision-how-to-install-containers.md).

## <a name="apikey-configuration-setting"></a>Ustawienie konfiguracji ApiKey

Ustawienie `ApiKey` określa klucz zasobów usługi Azure `Cognitive Services` używany do śledzenia informacji rozliczeniowych dla kontenera. Należy określić wartość ApiKey, a wartość musi być prawidłowym kluczem dla zasobu _Cognitive Services_ określonego dla ustawienia konfiguracji [`Billing`](#billing-configuration-setting) .

To ustawienie można znaleźć w następujących miejscach:

* Azure Portal: **Cognitive Services** zarządzania zasobami w obszarze **klucze**

## <a name="applicationinsights-setting"></a>Ustawienie ApplicationInsights

[!INCLUDE [Container shared configuration ApplicationInsights settings](../../../includes/cognitive-services-containers-configuration-shared-settings-application-insights.md)]

## <a name="billing-configuration-setting"></a>Ustawienie konfiguracji rozliczeń

Ustawienie `Billing` określa identyfikator URI punktu końcowego zasobu _Cognitive Services_ na platformie Azure używany do pomiaru informacji rozliczeniowych dla kontenera. Należy określić wartość tego ustawienia konfiguracji, a wartość musi być prawidłowym identyfikatorem URI punktu końcowego dla zasobu _Cognitive Services_ na platformie Azure. Kontener zgłasza użycie co 10 do 15 minut.

To ustawienie można znaleźć w następujących miejscach:

* Azure Portal: **Cognitive Services** Omówienie z etykietą `Endpoint`

Pamiętaj, aby dodać `vision/v1.0` Routing do identyfikatora URI punktu końcowego, jak pokazano w poniższej tabeli. 

|Wymagany| Nazwa | Typ danych | Opis |
|--|------|-----------|-------------|
|Tak| `Billing` | Ciąg | Identyfikator URI punktu końcowego rozliczenia<br><br>Przykład:<br>`Billing=https://westcentralus.api.cognitive.microsoft.com/vision/v1.0` |

## <a name="eula-setting"></a>Ustawienie umowy EULA

[!INCLUDE [Container shared configuration eula settings](../../../includes/cognitive-services-containers-configuration-shared-settings-eula.md)]

## <a name="fluentd-settings"></a>Ustawienia pozostały

[!INCLUDE [Container shared configuration fluentd settings](../../../includes/cognitive-services-containers-configuration-shared-settings-fluentd.md)]

## <a name="http-proxy-credentials-settings"></a>Ustawienia poświadczeń serwera proxy HTTP

[!INCLUDE [Container shared configuration HTTP proxy settings](../../../includes/cognitive-services-containers-configuration-shared-settings-http-proxy.md)]

## <a name="logging-settings"></a>Ustawienia rejestrowania
 
[!INCLUDE [Container shared configuration logging settings](../../../includes/cognitive-services-containers-configuration-shared-settings-logging.md)]

## <a name="mount-settings"></a>Ustawienia instalacji

Użyj instalacji powiązań, aby odczytywać i zapisywać dane w kontenerze i z niego. Można określić instalację wejściową lub instalację wyjściową, określając opcję `--mount` w poleceniu [Docker Run](https://docs.docker.com/engine/reference/commandline/run/) .

Kontenery przetwarzanie obrazów nie używają instalacji danych wejściowych lub wyjściowych do przechowywania danych szkoleniowych lub usług. 

Dokładna składnia lokalizacji instalacji hosta różni się w zależności od systemu operacyjnego hosta. Ponadto lokalizacja instalacji [komputera hosta](computer-vision-how-to-install-containers.md#the-host-computer)może być niedostępna z powodu konfliktu między uprawnieniami używanymi przez konto usługi platformy Docker i uprawnieniami lokalizacji instalacji hosta. 

|Optional (Opcjonalność)| Nazwa | Typ danych | Opis |
|-------|------|-----------|-------------|
|Niedozwolone| `Input` | Ciąg | Kontenery przetwarzanie obrazów nie używają tego.|
|Optional (Opcjonalność)| `Output` | Ciąg | Obiekt docelowy instalacji wyjściowej. Wartość domyślna to `/output`. Jest to lokalizacja dzienników. Dotyczy to również dzienników kontenerów. <br><br>Przykład:<br>`--mount type=bind,src=c:\output,target=/output`|

## <a name="example-docker-run-commands"></a>Przykładowe polecenia uruchamiania platformy Docker

W poniższych przykładach użyto ustawień konfiguracji, aby zilustrować sposób pisania i używania poleceń `docker run`.  Po uruchomieniu kontenera kontynuuje działanie, dopóki nie zostanie [zatrzymane](computer-vision-how-to-install-containers.md#stop-the-container) .

* **Znak kontynuacji wiersza**: polecenia platformy Docker w poniższych sekcjach używają ukośnika odwrotnego, `\`jako znaku kontynuacji wiersza. Zastąp lub usuń to w zależności od wymagań systemu operacyjnego hosta. 
* **Kolejność**argumentów: nie zmieniaj kolejności argumentów, o ile nie znasz już kontenerów platformy Docker.

Zastąp {_argument_name_} własnymi wartościami:

| Symbol zastępczy | Wartość | Format lub przykład |
|-------------|-------|---|
| **{API_KEY}** | Klucz punktu końcowego zasobu `Computer Vision` na stronie klucze `Computer Vision` platformy Azure. | `xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx` |
| **{ENDPOINT_URI}** | Wartość punktu końcowego rozliczenia jest dostępna na stronie Przegląd `Computer Vision` platformy Azure.| Zobacz [zbieranie wymaganych parametrów](computer-vision-how-to-install-containers.md#gathering-required-parameters) dla jawnych przykładów. |

[!INCLUDE [subdomains-note](../../../includes/cognitive-services-custom-subdomains-note.md)]

> [!IMPORTANT]
> Aby można było uruchomić kontener, należy określić opcje `Eula`, `Billing`i `ApiKey`. w przeciwnym razie kontener nie zostanie uruchomiony.  Aby uzyskać więcej informacji, zobacz [rozliczenia](computer-vision-how-to-install-containers.md#billing).
> Wartość ApiKey jest **kluczem** ze strony klucze zasobów usługi Azure `Cognitive Services`.

## <a name="container-docker-examples"></a>Przykłady kontenera Docker

Poniższe przykłady platformy Docker dotyczą kontenera odczyt.

### <a name="basic-example"></a>Przykład podstawowy

  ```docker
  docker run --rm -it -p 5000:5000 --memory 16g --cpus 8 \
  containerpreview.azurecr.io/microsoft/cognitive-services-read \
  Eula=accept \
  Billing={ENDPOINT_URI} \
  ApiKey={API_KEY} 
  ```

### <a name="logging-example"></a>Przykład rejestrowania 

  ```docker
  docker run --rm -it -p 5000:5000 --memory 16g --cpus 8 \
  containerpreview.azurecr.io/microsoft/cognitive-services-read \
  Eula=accept \
  Billing={ENDPOINT_URI} \
  ApiKey={API_KEY} \
  Logging:Console:LogLevel:Default=Information
  ```

## <a name="next-steps"></a>Następne kroki

* Zapoznaj [się z tematem Instalowanie i uruchamianie kontenerów](computer-vision-how-to-install-containers.md).
