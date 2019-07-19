---
title: Jak skonfigurować kontener dla aparatu rozpoznawania formularzy
titleSuffix: Azure Cognitive Services
description: Dowiedz się, jak skonfigurować kontener aparatu rozpoznawania formularzy, aby analizować dane formularzy i tabel.
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 06/19/2019
ms.author: dapine
ms.openlocfilehash: 4a490e8a9f111985df9c9e8c9f73bc36d686cc2a
ms.sourcegitcommit: e72073911f7635cdae6b75066b0a88ce00b9053b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/19/2019
ms.locfileid: "68348691"
---
# <a name="configure-form-recognizer-containers"></a>Konfigurowanie kontenerów aparatów rozpoznawania formularzy

Korzystając z kontenerów usługi Azure form, można utworzyć architekturę aplikacji, która jest zoptymalizowana pod kątem korzystania z zarówno niezawodnej możliwości chmury, jak i lokalizacji brzegowej.

Można skonfigurować środowisko uruchomieniowe kontenera aparatu rozpoznawania formularzy przy użyciu `docker run` argumentów polecenia. Ten kontener ma kilka wymaganych ustawień i kilka opcjonalnych ustawień. Aby uzyskać kilka przykładów, zobacz sekcję ["Przykładowe polecenia uruchamiania platformy Docker"](#example-docker-run-commands) . Ustawienia dotyczące rozliczeń dotyczą tylko kontenera.

## <a name="configuration-settings"></a>Ustawienia konfiguracji

[!INCLUDE [Container shared configuration settings table](../../../includes/cognitive-services-containers-configuration-shared-settings-table.md)]

> [!IMPORTANT]
> Ustawienia [`ApiKey`](#apikey-configuration-setting), [`Billing`](#billing-configuration-setting) [i`Eula`](#eula-setting) są używane razem. Należy podać prawidłowe wartości dla wszystkich trzech ustawień; w przeciwnym razie kontener nie zostanie uruchomiony. Aby uzyskać więcej informacji na temat tworzenia wystąpienia kontenera za pomocą tych ustawień konfiguracji, zobacz [rozliczeń](form-recognizer-container-howto.md#billing).

## <a name="apikey-configuration-setting"></a>Ustawienie konfiguracji ApiKey

To `ApiKey` ustawienie określa klucz zasobów platformy Azure, który służy do śledzenia informacji rozliczeniowych dla kontenera. Wartość ApiKey musi być prawidłowym kluczem dla zasobu _aparatu rozpoznawania formularza_ , który jest określony `Billing` w sekcji "ustawienie konfiguracji rozliczeń".

To ustawienie można znaleźć w Azure Portal w obszarze **klucze**w obszarze **Zarządzanie zasobami aparatu rozpoznawania formularzy**.

## <a name="applicationinsights-setting"></a>Ustawienie dotycząca usługi Application Insights

[!INCLUDE [Container shared configuration ApplicationInsights settings](../../../includes/cognitive-services-containers-configuration-shared-settings-application-insights.md)]

## <a name="billing-configuration-setting"></a>Ustawienie konfiguracji rozliczeń

Ustawienie określa identyfikator URI punktu końcowego dla zasobu _aparatu rozpoznawania_ w systemie Azure, który służy do mierzenia informacji rozliczeniowych dla kontenera. `Billing` Wartość tego ustawienia konfiguracji musi być prawidłowym identyfikatorem URI punktu końcowego dla zasobu _aparatu rozpoznawania formularza_ na platformie Azure. Kontener zgłasza użycie co 10 do 15 minut.

To ustawienie można znaleźć w Azure Portal w sekcji **Omówienie aparatu rozpoznawania formularzy**w obszarze **punkt końcowy**.

|Wymagane| Name (Nazwa) | Typ danych | Opis |
|--|------|-----------|-------------|
|Yes| `Billing` | Ciąg | Identyfikator URI punktu końcowego rozliczeń<br><br>Przykład:<br>`Billing=https://westus2.api.cognitive.microsoft.com/` |

## <a name="eula-setting"></a>Umowa licencyjna EULA ustawienie

[!INCLUDE [Container shared configuration eula settings](../../../includes/cognitive-services-containers-configuration-shared-settings-eula.md)]

## <a name="fluentd-settings"></a>Ustawienia Fluentd

[!INCLUDE [Container shared configuration fluentd settings](../../../includes/cognitive-services-containers-configuration-shared-settings-fluentd.md)]

## <a name="http-proxy-credentials-settings"></a>Ustawienia poświadczeń serwera proxy HTTP

[!INCLUDE [Container shared configuration fluentd settings](../../../includes/cognitive-services-containers-configuration-shared-settings-http-proxy.md)]

## <a name="logging-settings"></a>Ustawienia rejestrowania

[!INCLUDE [Container shared configuration logging settings](../../../includes/cognitive-services-containers-configuration-shared-settings-logging.md)]


## <a name="mount-settings"></a>Ustawienia instalacji

Użyj powiązania instaluje do odczytu i zapisu danych do i z kontenera. Można określić instalację wejściową lub instalację wyjściową, określając `--mount` opcję [ `docker run` ](https://docs.docker.com/engine/reference/commandline/run/)w poleceniu.

Kontener rozpoznawania formularzy wymaga instalacji wejściowej i instalacji wyjściowej. Instalacja wejściowa może być tylko do odczytu i jest wymagana w celu uzyskania dostępu do danych używanych do szkolenia i oceniania. Instalacja wyjściowa musi być zapisywalna i służy do przechowywania modeli i danych tymczasowych.

Dokładna składnia lokalizację instalacji hosta różni się zależnie od systemu operacyjnego hosta. Ponadto lokalizacja instalacji [komputera hosta](form-recognizer-container-howto.md#the-host-computer) może być niedostępna z powodu konfliktu między uprawnieniami konta usługi platformy Docker a uprawnieniami do lokalizacji instalacji hosta.

|Optional| Name (Nazwa) | Typ danych | Opis |
|-------|------|-----------|-------------|
|Wymagane| `Input` | String | Miejsce docelowe instalacji danych wejściowych. Wartość domyślna to `/input`.    <br><br>Przykład:<br>`--mount type=bind,src=c:\input,target=/input`|
|Wymagane| `Output` | Ciąg | Miejsce docelowe instalacji danych wyjściowych. Wartość domyślna to `/output`.  <br><br>Przykład:<br>`--mount type=bind,src=c:\output,target=/output`|

## <a name="example-docker-run-commands"></a>Przykład platformy docker, Uruchom polecenia

W poniższych przykładach używane ustawienia konfiguracji, aby zilustrować, jak pisać i użyj `docker run` poleceń. Gdy jest uruchomiona, kontener kontynuuje działanie, dopóki nie zostanie [zatrzymany](form-recognizer-container-howto.md#stop-the-container).

* **Znak kontynuacji wiersza**: Polecenia platformy Docker w poniższych sekcjach używają ukośnika odwrotnego\\() jako znaku kontynuacji wiersza. Zastąp lub Usuń ten znak, w zależności od wymagań systemu operacyjnego hosta.
* **Kolejność argumentów**: Nie zmieniaj kolejności argumentów, chyba że znasz kontenery Docker.

Zastąp {_argument_name_} w poniższej tabeli własnymi wartościami:

| Symbol zastępczy | Value |
|-------------|-------|
|{FORM_RECOGNIZER_API_KEY} | Klucz, który służy do uruchamiania kontenera. Jest ona dostępna na stronie klucze aparatu rozpoznawania Azure Portal formularza.  |
|{FORM_RECOGNIZER_ENDPOINT_URI} | Wartość identyfikatora URI punktu końcowego rozliczenia jest dostępna na stronie Omówienie aparatu rozpoznawania Azure Portal formularza.|
|{COMPUTER_VISION_API_KEY}| Klucz jest dostępny na stronie klucze interfejs API przetwarzania obrazów Azure Portal.|
|{COMPUTER_VISION_ENDPOINT_URI}|Punkt końcowy rozliczeń. Jeśli używasz zasobu przetwarzanie obrazów opartego na chmurze, wartość identyfikatora URI jest dostępna na stronie Przegląd Azure Portal interfejs API przetwarzania obrazów. Jeśli używasz kontenera " *poznawcze-Services-rozpoznaje-Text* ", użyj adresu URL punktu końcowego rozliczenia, który jest przesyłany do kontenera `docker run` w poleceniu.|

> [!IMPORTANT]
> Aby uruchomić kontener, określ `Eula`opcje, `Billing`i `ApiKey` ; w przeciwnym razie kontener nie zostanie uruchomiony. Aby uzyskać więcej informacji, zobacz [rozliczeń](#billing-configuration-setting).

> [!NOTE] 
> Wartość ApiKey jest **kluczem** ze strony klucze zasobów aparatu rozpoznawania formularzy platformy Azure.

## <a name="form-recognizer-container-docker-examples"></a>Przykłady platformy Docker kontenera aparatu rozpoznawania formularzy

Poniższe przykłady platformy Docker dotyczą kontenera aparatów rozpoznawania formularzy.

### <a name="basic-example-for-form-recognizer"></a>Podstawowy przykład dla aparatu rozpoznawania formularza

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

### <a name="logging-example-for-form-recognizer"></a>Przykład rejestrowania dla aparatu rozpoznawania formularza

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

* Przejrzyj temat [Instalowanie i uruchamianie kontenerów](form-recognizer-container-howto.md).
