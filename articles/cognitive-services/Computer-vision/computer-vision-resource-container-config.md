---
title: Konfigurowanie kontenerów — wizja komputerowa
titleSuffix: Azure Cognitive Services
description: W tym artykule pokazano, jak skonfigurować ustawienia wymagane i opcjonalne dla kontenerów rozpoznawania tekstu w wizji komputerowej.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 04/01/2020
ms.author: aahi
ms.custom: seodec18
ms.openlocfilehash: 3be302019c712c13bd29d7ed3781151a1648e847
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/08/2020
ms.locfileid: "80879313"
---
# <a name="configure-computer-vision-docker-containers"></a>Konfigurowanie kontenerów platformy Docker usługi Computer Vision

Środowisko wykonawcze kontenera przetwarzania obrazów komputerowych `docker run` można skonfigurować przy użyciu argumentów polecenia. Ten kontener ma kilka wymaganych ustawień, wraz z kilkoma ustawieniami opcjonalnymi. Dostępnych jest kilka [przykładów](#example-docker-run-commands) polecenia. Ustawienia specyficzne dla kontenera to ustawienia rozliczeń. 

## <a name="configuration-settings"></a>Ustawienia konfiguracji

[!INCLUDE [Container shared configuration settings table](../../../includes/cognitive-services-containers-configuration-shared-settings-table.md)]

> [!IMPORTANT]
> Ustawienia [`ApiKey`](#apikey-configuration-setting) [`Billing`](#billing-configuration-setting)i [`Eula`](#eula-setting) ustawienia są używane razem i należy podać prawidłowe wartości dla wszystkich trzech z nich; w przeciwnym razie kontener nie zostanie uruchomiony. Aby uzyskać więcej informacji na temat używania tych ustawień konfiguracji do tworzenia wystąpienia kontenera, zobacz [Rozliczenia](computer-vision-how-to-install-containers.md).

## <a name="apikey-configuration-setting"></a>Ustawienie konfiguracji apikey

Ustawienie `ApiKey` określa klucz `Cognitive Services` zasobów platformy Azure używany do śledzenia informacji rozliczeniowych dla kontenera. Należy określić wartość dla apikey i wartość musi być prawidłowy klucz [`Billing`](#billing-configuration-setting) dla zasobu usług Cognitive _Services_ określonych dla ustawienia konfiguracji.

To ustawienie można znaleźć w następującym miejscu:

* Portal Azure: Zarządzanie zasobami **usług Cognitive Services** w obszarze **Klucze**

## <a name="applicationinsights-setting"></a>Ustawienie ApplicationInsights

[!INCLUDE [Container shared configuration ApplicationInsights settings](../../../includes/cognitive-services-containers-configuration-shared-settings-application-insights.md)]

## <a name="billing-configuration-setting"></a>Ustawienie konfiguracji rozliczeń

Ustawienie `Billing` określa identyfikator URI punktu końcowego zasobu _usług Cognitive Services_ na platformie Azure używanego do pomiaru informacji rozliczeniowych dla kontenera. Należy określić wartość dla tego ustawienia konfiguracji, a wartość musi być prawidłowym identyfikatorem URI punktu końcowego dla zasobu _usług Cognitive Services_ na platformie Azure. Kontener raportuje użycie co 10 do 15 minut.

To ustawienie można znaleźć w następującym miejscu:

* Portal Azure: Omówienie **usług Cognitive Services,** oznaczone etykietą`Endpoint`

Pamiętaj, aby `vision/v1.0` dodać routing do identyfikatora URI punktu końcowego, jak pokazano w poniższej tabeli. 

|Wymagany| Nazwa | Typ danych | Opis |
|--|------|-----------|-------------|
|Tak| `Billing` | Ciąg | Identyfikator URI punktu końcowego rozliczeń<br><br>Przykład:<br>`Billing=https://westcentralus.api.cognitive.microsoft.com/vision/v1.0` |

## <a name="eula-setting"></a>Ustawienie Eula

[!INCLUDE [Container shared configuration eula settings](../../../includes/cognitive-services-containers-configuration-shared-settings-eula.md)]

## <a name="fluentd-settings"></a>Płynne ustawienia

[!INCLUDE [Container shared configuration fluentd settings](../../../includes/cognitive-services-containers-configuration-shared-settings-fluentd.md)]

## <a name="http-proxy-credentials-settings"></a>Ustawienia poświadczeń serwera proxy HTTP

[!INCLUDE [Container shared configuration HTTP proxy settings](../../../includes/cognitive-services-containers-configuration-shared-settings-http-proxy.md)]

## <a name="logging-settings"></a>Ustawienia logowania
 
[!INCLUDE [Container shared configuration logging settings](../../../includes/cognitive-services-containers-configuration-shared-settings-logging.md)]

## <a name="mount-settings"></a>Ustawienia instalacji

Użyj powiązania wierzchowce do odczytu i zapisu danych do i z kontenera. Można określić mocowanie wejściowe lub `--mount` mocowanie wyjściowe, określając opcję w poleceniu [uruchamiania platformy docker.](https://docs.docker.com/engine/reference/commandline/run/)

Kontenery przetwarzania nie używają instalacji wejściowych ani wyjściowych do przechowywania danych szkoleniowych lub usługowych. 

Dokładna składnia lokalizacji instalacji hosta różni się w zależności od systemu operacyjnego hosta. Ponadto lokalizacja instalacji [komputera-hosta](computer-vision-how-to-install-containers.md#the-host-computer)może być niedostępna z powodu konfliktu między uprawnieniami używanymi przez konto usługi Platformy Docker a uprawnieniami lokalizacji instalacji hosta. 

|Optional (Opcjonalność)| Nazwa | Typ danych | Opis |
|-------|------|-----------|-------------|
|Niedozwolone| `Input` | Ciąg | Kontenery przetwarzania nie używają tego.|
|Optional (Opcjonalność)| `Output` | Ciąg | Miejsce docelowe mocowania wyjściowego. Wartością domyślną jest `/output`. Jest to lokalizacja dzienników. Obejmuje to dzienniki kontenerów. <br><br>Przykład:<br>`--mount type=bind,src=c:\output,target=/output`|

## <a name="example-docker-run-commands"></a>Przykładowe polecenia uruchamiania platformy dok.

Poniższe przykłady używają ustawień konfiguracji, aby zilustrować sposób pisania i używania `docker run` poleceń.  Po uruchomieniu kontener będzie nadal działał, dopóki go nie [zatrzymasz.](computer-vision-how-to-install-containers.md#stop-the-container)

* **Znak kontynuacji wiersza:** Polecenia platformy Docker w poniższych `\`sekcjach używają ukośnika wstecznego, jako znaku kontynuacji wiersza. Zastąp lub usuń to na podstawie wymagań systemu operacyjnego hosta. 
* **Kolejność argumentów:** Nie należy zmieniać kolejności argumentów, chyba że są bardzo zaznajomieni z kontenerami platformy Docker.

Zamień {_argument_name_} własnymi wartościami:

| Symbol zastępczy | Wartość | Format lub przykład |
|-------------|-------|---|
| **{API_KEY}** | Klucz punktu końcowego `Computer Vision` zasobu `Computer Vision` na stronie Klucze platformy Azure. | `xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx` |
| **{ENDPOINT_URI}** | Wartość punktu końcowego rozliczeń jest `Computer Vision` dostępna na stronie Przegląd platformy Azure.| Zobacz [zbieranie wymaganych parametrów](computer-vision-how-to-install-containers.md#gathering-required-parameters) dla jawnych przykładów. |

[!INCLUDE [subdomains-note](../../../includes/cognitive-services-custom-subdomains-note.md)]

> [!IMPORTANT]
> Aby `Eula` `Billing`uruchomić `ApiKey` kontener, należy określić opcje i opcje; w przeciwnym razie kontener nie zostanie uruchomiony.  Aby uzyskać więcej informacji, zobacz [Rozliczenia](computer-vision-how-to-install-containers.md#billing).
> Wartość ApiKey jest **kluczem** `Cognitive Services` ze strony klucze zasobów platformy Azure.

## <a name="container-docker-examples"></a>Przykłady platformy Docker kontenera

Poniższe przykłady platformy Docker są dla odczytu kontenera.

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

* Sprawdź, [jak zainstalować i uruchomić kontenery](computer-vision-how-to-install-containers.md).
