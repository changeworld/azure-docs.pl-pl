---
title: Jak skonfigurować kontener dla aparatu rozpoznawania formularzy
titleSuffix: Azure Cognitive Services
description: Dowiedz się, jak skonfigurować kontener aparatu rozpoznawania formularzy do analizowania danych formularza i tabeli.
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 11/07/2019
ms.author: dapine
ms.openlocfilehash: 5439ec0c0aab5b8c127b651147e4b25d27c58390
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "75379627"
---
# <a name="configure-form-recognizer-containers"></a>Konfigurowanie kontenerów aparatu rozpoznawania formularzy

Za pomocą kontenerów azure rozpoznawania formularzy, można utworzyć architekturę aplikacji, który jest zoptymalizowany, aby korzystać z możliwości chmury niezawodne i lokalizacji krawędzi.

Środowisko wykonywania kontenera rozpoznawania formularzy można skonfigurować przy użyciu argumentów `docker run` polecenia. Ten kontener ma kilka wymaganych ustawień i kilka ustawień opcjonalnych. Aby zapoznać się z kilkoma przykładami, zobacz sekcję ["Przykładowe polecenia uruchamiania platformy dok.](#example-docker-run-commands) Ustawienia specyficzne dla kontenera to ustawienia rozliczeń.

> [!IMPORTANT]
> Kontenery aparat rozpoznawania formularzy obecnie używają wersji 1.0 interfejsu API aparatu rozpoznawania formularzy. Dostęp do najnowszej wersji interfejsu API można uzyskać za pomocą usługi zarządzanej.

## <a name="configuration-settings"></a>Ustawienia konfiguracji

[!INCLUDE [Container shared configuration settings table](../../../includes/cognitive-services-containers-configuration-shared-settings-table.md)]

> [!IMPORTANT]
> Używane są [`Eula`](#eula-setting) ustawienia i ustawienia. [`ApiKey`](#apikey-configuration-setting) [`Billing`](#billing-configuration-setting) Należy podać prawidłowe wartości dla wszystkich trzech ustawień; w przeciwnym razie kontener nie zostanie uruchomiony. Aby uzyskać więcej informacji na temat używania tych ustawień konfiguracji do tworzenia wystąpienia kontenera, zobacz [Rozliczenia](form-recognizer-container-howto.md#billing).

## <a name="apikey-configuration-setting"></a>Ustawienie konfiguracji apikey

Ustawienie `ApiKey` określa klucz zasobu platformy Azure, który jest używany do śledzenia informacji rozliczeniowych dla kontenera. Wartość apiKey musi być prawidłowym kluczem dla zasobu rozpoznawania `Billing` _formularzy,_ który jest określony w sekcji "Ustawienie konfiguracji rozliczeń".

To ustawienie można znaleźć w witrynie Azure portal w obszarze **Zarządzanie zasobami aparatem rozpoznawania formularzy**w obszarze **Klucze**.

## <a name="applicationinsights-setting"></a>Ustawienie ApplicationInsights

[!INCLUDE [Container shared configuration ApplicationInsights settings](../../../includes/cognitive-services-containers-configuration-shared-settings-application-insights.md)]

## <a name="billing-configuration-setting"></a>Ustawienie konfiguracji rozliczeń

Ustawienie `Billing` określa identyfikator URI punktu końcowego zasobu _rozpoznawania formularzy_ na platformie Azure, który jest używany do pomiaru informacji rozliczeniowych dla kontenera. Wartość dla tego ustawienia konfiguracji musi być prawidłowym identyfikatorem URI punktu końcowego dla zasobu _rozpoznawania formularzy_ na platformie Azure. Kontener raportuje użycie co 10 do 15 minut.

To ustawienie można znaleźć w witrynie Azure portal, w **omówieniu rozpoznawania formularzy**w obszarze **Punkt końcowy**.

|Wymagany| Nazwa | Typ danych | Opis |
|--|------|-----------|-------------|
|Tak| `Billing` | Ciąg | Identyfikator URI punktu końcowego rozliczeń. Aby uzyskać więcej informacji na temat uzyskiwania identyfikatora URI rozliczeń, zobacz [zbieranie wymaganych parametrów](form-recognizer-container-howto.md#gathering-required-parameters). Aby uzyskać więcej informacji i pełną listę regionalnych punktów końcowych, zobacz [Niestandardowe nazwy poddomen dla usług Cognitive Services](../cognitive-services-custom-subdomains.md). |

## <a name="eula-setting"></a>Ustawienie Eula

[!INCLUDE [Container shared configuration eula settings](../../../includes/cognitive-services-containers-configuration-shared-settings-eula.md)]

## <a name="fluentd-settings"></a>Płynne ustawienia

[!INCLUDE [Container shared configuration fluentd settings](../../../includes/cognitive-services-containers-configuration-shared-settings-fluentd.md)]

## <a name="http-proxy-credentials-settings"></a>Ustawienia poświadczeń serwera proxy HTTP

[!INCLUDE [Container shared configuration fluentd settings](../../../includes/cognitive-services-containers-configuration-shared-settings-http-proxy.md)]

## <a name="logging-settings"></a>Ustawienia logowania

[!INCLUDE [Container shared configuration logging settings](../../../includes/cognitive-services-containers-configuration-shared-settings-logging.md)]


## <a name="mount-settings"></a>Ustawienia instalacji

Użyj powiązania wierzchowce do odczytu i zapisu danych do i z kontenera. Można określić mocowanie wejściowe lub mocowanie wyjściowe, określając `--mount` opcję w [ `docker run` poleceniu](https://docs.docker.com/engine/reference/commandline/run/).

Kontener aparatu rozpoznawania formularzy wymaga mocowania wejściowego i mocowania wyjściowego. Instalacja wejściowa może być tylko do odczytu i jest wymagana do uzyskania dostępu do danych, które są używane do szkolenia i oceniania. Mocowanie danych wyjściowych musi być zapisywalne i służy do przechowywania modeli i danych tymczasowych.

Dokładna składnia lokalizacji instalacji hosta różni się w zależności od systemu operacyjnego hosta. Ponadto lokalizacja instalacji [komputera-hosta](form-recognizer-container-howto.md#the-host-computer) może nie być dostępna z powodu konfliktu między uprawnieniami konta usługi Platformy Docker a uprawnieniami lokalizacji instalacji hosta.

|Optional (Opcjonalność)| Nazwa | Typ danych | Opis |
|-------|------|-----------|-------------|
|Wymagany| `Input` | Ciąg | Miejsce docelowe instalacji wejściowej. Wartością domyślną jest `/input`.    <br><br>Przykład:<br>`--mount type=bind,src=c:\input,target=/input`|
|Wymagany| `Output` | Ciąg | Miejsce docelowe mocowania wyjściowego. Wartością domyślną jest `/output`.  <br><br>Przykład:<br>`--mount type=bind,src=c:\output,target=/output`|

## <a name="example-docker-run-commands"></a>Przykładowe polecenia uruchamiania platformy dok.

Poniższe przykłady używają ustawień konfiguracji, aby zilustrować sposób pisania i używania `docker run` poleceń. Po uruchomieniu kontener będzie działał do momentu [jego zatrzymania.](form-recognizer-container-howto.md#stop-the-container)

* **Znak kontynuacji wiersza:** Polecenia platformy Docker w poniższych\\sekcjach używają ukośnika wstecznego ( ) jako znaku kontynuacji wiersza. Zastąp lub usuń tę postać, w zależności od wymagań systemu operacyjnego hosta.
* **Kolejność argumentów:** Nie zmieniaj kolejności argumentów, chyba że znasz kontenery platformy Docker.

Zastąp {_argument_name_} w poniższej tabeli własnymi wartościami:

| Symbol zastępczy | Wartość |
|-------------|-------|
| **{FORM_RECOGNIZER_API_KEY}** | Klucz, który jest używany do uruchamiania kontenera. Jest on dostępny na stronie Klucze rozpoznawania formularzy witryny Azure portal. |
| **{FORM_RECOGNIZER_ENDPOINT_URI}** | Wartość identyfikatora URI punktu końcowego rozliczeń jest dostępna na stronie Omówienie rozpoznawania formularzy portalu Azure.|
| **{COMPUTER_VISION_API_KEY}** | Klucz jest dostępny na stronie Klucze interfejsu API usługi Azure portal.|
| **{COMPUTER_VISION_ENDPOINT_URI}** | Punkt końcowy rozliczeń. Jeśli używasz zasobu przetwarzania w chmurze, wartość URI jest dostępna na stronie Przegląd interfejsu API usługi Azure portal Computer Vision. Jeśli używasz kontenera *cognitive-services-recognize-text,* użyj adresu URL punktu końcowego rozliczeń, `docker run` który jest przekazywany do kontenera w poleceniu. |

Zobacz [zbieranie wymaganych parametrów,](form-recognizer-container-howto.md#gathering-required-parameters) aby uzyskać szczegółowe informacje na temat uzyskiwania tych wartości.

[!INCLUDE [cognitive-services-custom-subdomains-note](../../../includes/cognitive-services-custom-subdomains-note.md)]

> [!IMPORTANT]
> Aby uruchomić kontener, `Eula`określ `Billing` `ApiKey` opcję , i opcje; w przeciwnym razie kontener nie zostanie uruchomiony. Aby uzyskać więcej informacji, zobacz [Rozliczenia](#billing-configuration-setting).

## <a name="form-recognizer-container-docker-examples"></a>Przykłady platformy dokowania kontenera aparatu rozpoznawania formularzy

Poniższe przykłady platformy Docker są dla kontenera aparat rozpoznawania formularzy.

### <a name="basic-example-for-form-recognizer"></a>Podstawowy przykład aparatu rozpoznawania formularzy

```Docker
docker run --rm -it -p 5000:5000 --memory 8g --cpus 2 \
--mount type=bind,source=c:\input,target=/input  \
--mount type=bind,source=c:\output,target=/output \
containerpreview.azurecr.io/microsoft/cognitive-services-form-recognizer \
Eula=accept \
Billing={FORM_RECOGNIZER_ENDPOINT_URI} \
ApiKey={FORM_RECOGNIZER_API_KEY} \
FormRecognizer:ComputerVisionApiKey={COMPUTER_VISION_API_KEY} \
FormRecognizer:ComputerVisionEndpointUri={COMPUTER_VISION_ENDPOINT_URI}
```

### <a name="logging-example-for-form-recognizer"></a>Rejestrowanie przykładu dla aparatu rozpoznawania formularzy

```Docker
docker run --rm -it -p 5000:5000 --memory 8g --cpus 2 \
--mount type=bind,source=c:\input,target=/input  \
--mount type=bind,source=c:\output,target=/output \
containerpreview.azurecr.io/microsoft/cognitive-services-form-recognizer \
Eula=accept \
Billing={FORM_RECOGNIZER_ENDPOINT_URI} \
ApiKey={FORM_RECOGNIZER_API_KEY} \
FormRecognizer:ComputerVisionApiKey={COMPUTER_VISION_API_KEY} \
FormRecognizer:ComputerVisionEndpointUri={COMPUTER_VISION_ENDPOINT_URI}
Logging:Console:LogLevel:Default=Information
```

## <a name="next-steps"></a>Następne kroki

* Przejrzyj [instalowanie i uruchamianie kontenerów](form-recognizer-container-howto.md).
