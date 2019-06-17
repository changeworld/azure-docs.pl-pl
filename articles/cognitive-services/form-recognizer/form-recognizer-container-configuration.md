---
title: Konfigurowanie kontenera — aparat rozpoznawania formularza
titleSuffix: Azure Cognitive Services
description: Dowiedz się, jak skonfigurować kontener rozpoznawania formularza, aby przeanalizować dane formularza i tabeli.
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: form-recognizer
ms.topic: overview
ms.date: 06/10/2019
ms.author: dapine
ms.openlocfilehash: bdff74be8578bb862974479b3151b0d922f00dd9
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "67063990"
---
# <a name="configure-form-recognizer-containers"></a>Konfigurowanie aparatu rozpoznającego w postaci kontenerów

Przy użyciu kontenerów aparatu rozpoznawania formularza platformy Azure, możesz tworzyć architekturę aplikacji, która jest zoptymalizowana, aby móc korzystać z możliwości chmury niezawodne i lokalizacja krawędzi.

Można skonfigurować za pomocą środowiska wykonawczego kontenera aparatu rozpoznawania formularza `docker run` argumenty polecenia. Ten kontener ma kilka wymagane ustawienia i kilka ustawień opcjonalnych. Aby uzyskać kilka przykładów, zobacz ["Przykład docker, Uruchom polecenia"](#example-docker-run-commands) sekcji. Ustawienia specyficzne dla kontenera są ustawienia rozliczeń.

## <a name="configuration-settings"></a>Ustawienia konfiguracji

[!INCLUDE [Container shared configuration settings table](../../../includes/cognitive-services-containers-configuration-shared-settings-table.md)]

> [!IMPORTANT]
> [ `ApiKey` ](#apikey-configuration-setting), [ `Billing` ](#billing-configuration-setting), I [ `Eula` ](#eula-setting) ustawienia są używane razem. Należy podać prawidłowe wartości dla wszystkich trzech ustawień; w przeciwnym razie nie uruchamia się kontener. Aby uzyskać więcej informacji na temat tworzenia wystąpienia kontenera za pomocą tych ustawień konfiguracji, zobacz [rozliczeń](form-recognizer-container-howto.md#billing).

## <a name="apikey-configuration-setting"></a>Ustawienie konfiguracji ApiKey

`ApiKey` Ustawienie umożliwia określenie klucza zasobów platformy Azure, które jest używane do śledzenia informacji rozliczeniowych dla kontenera. Wartość ApiKey musi być prawidłowy klucz dla _rozpoznawania formularza_ zasobu, który jest określony dla `Billing` w sekcji "Rozliczenia ustawienie konfiguracji".

Możesz znaleźć tego ustawienia w witrynie Azure portal w **zarządzania zasobami rozpoznawania formularza**w obszarze **klucze**.

## <a name="applicationinsights-setting"></a>Ustawienie dotycząca usługi Application Insights

[!INCLUDE [Container shared configuration ApplicationInsights settings](../../../includes/cognitive-services-containers-configuration-shared-settings-application-insights.md)]

## <a name="billing-configuration-setting"></a>Ustawienie konfiguracji rozliczeń

`Billing` Ustawienie określa identyfikator URI punktu końcowego z _rozpoznawania formularza_ zasobów na platformie Azure, który służy do pomiaru informacji rozliczeniowych dla kontenera. Wartość tego ustawienia konfiguracji musi być prawidłowy identyfikator URI punktu końcowego dla _rozpoznawania formularza_ zasobów na platformie Azure. Kontener raportów użycia dotyczących co 10 do 15 minut.

Możesz znaleźć tego ustawienia w witrynie Azure portal w **Przegląd rozpoznawania formularza**w obszarze **punktu końcowego**.

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

Użyj powiązania instaluje do odczytu i zapisu danych do i z kontenera. Możesz określić wejściowych instalacji lub instalacji w danych wyjściowych, określając `--mount` opcji [ `docker run` polecenia](https://docs.docker.com/engine/reference/commandline/run/).

Kontener formularz rozpoznawania wymaga instalacji wejściowe i dane wyjściowe instalacji. Wejściowy instalacji może być tylko do odczytu i jest to wymagane do uzyskiwania dostępu do danych, który służy do szkolenia i oceniania. Ma instalacji danych wyjściowych można było zapisywać i można go użyć do przechowywania modeli i danych tymczasowych.

Dokładna składnia lokalizację instalacji hosta różni się zależnie od systemu operacyjnego hosta. Ponadto lokalizacja instalacji programu [komputerze-hoście](form-recognizer-container-howto.md#the-host-computer) nie mogą być niedostępne z powodu konfliktu między uprawnienia kontu usługi Docker i uprawnienia do lokalizacji instalacji hosta.

|Optional (Opcjonalność)| Name (Nazwa) | Typ danych | Opis |
|-------|------|-----------|-------------|
|Wymagane| `Input` | String | Miejsce docelowe instalacji danych wejściowych. Wartość domyślna to `/input`.    <br><br>Przykład:<br>`--mount type=bind,src=c:\input,target=/input`|
|Wymagane| `Output` | String | Miejsce docelowe instalacji danych wyjściowych. Wartość domyślna to `/output`.  <br><br>Przykład:<br>`--mount type=bind,src=c:\output,target=/output`|

## <a name="example-docker-run-commands"></a>Przykład platformy docker, Uruchom polecenia

W poniższych przykładach używane ustawienia konfiguracji, aby zilustrować, jak pisać i użyj `docker run` poleceń. Gdy jest uruchomiona, kontener będzie kontynuował działanie aż [zatrzymać](form-recognizer-container-howto.md#stop-the-container).

* **Znak kontynuacji wiersza**: Platformy Docker w poniższych sekcjach użyto ukośnika (\\) jako znak kontynuacji wiersza. Zamień lub Usuń ten znak, w zależności od wymagań systemu operacyjnego hosta.
* **Kolejność argumentów**: Nie należy zmieniać kolejność argumentów, jeśli nie jesteś zaznajomiony z kontenerami aparatu Docker.

Zastąp {_argument_name_} w tabeli poniżej własnymi wartościami:

| Symbol zastępczy | Wartość |
|-------------|-------|
|{BILLING_KEY} | Klucz, który jest używany do uruchamiania kontenera. Jest ona dostępna w witrynie Azure portal, strona klucze rozpoznawania formularza.  |
|{BILLING_ENDPOINT_URI} | Rozliczeń wartość identyfikatora URI punktu końcowego jest dostępna w witrynie Azure portal, strona przeglądu rozpoznawania formularza.|
|{COMPUTER_VISION_API_KEY}| Klucz jest dostępny w witrynie Azure portal, strona klucze interfejsu API przetwarzania komputera.|
|{COMPUTER_VISION_ENDPOINT_URI}|Punkt końcowy rozliczeń. Jeśli używasz zasobów opartych na chmurze przetwarzania obrazów to wartość identyfikatora URI jest dostępna w witrynie Azure portal, strona omówienie interfejsu API przetwarzania komputera. Jeśli używasz *cognitive services — rozpoznawanie tekstu* kontenera, użyj rozliczeń adresu URL punktu końcowego, który jest przekazywany do kontenera w `docker run` polecenia.|

> [!IMPORTANT]
> Aby uruchomić kontener, określ `Eula`, `Billing`, i `ApiKey` opcje; w przeciwnym razie nie uruchamia się kontener. Aby uzyskać więcej informacji, zobacz [rozliczeń](#billing-configuration-setting).

> [!NOTE] 
> Wartość ApiKey **klucz** ze strony klucze zasobów rozpoznawania formularza platformy Azure.

## <a name="form-recognizer-container-docker-examples"></a>Formularz rozpoznawania kontenera platformy Docker przykłady

W poniższych przykładach platformy Docker są w kontenerze aparatu rozpoznawania formularza.

### <a name="basic-example-for-form-recognizer"></a>Podstawowy przykład rozpoznawania formularza

```Docker
docker run --rm -it -p 5000:5000 --memory 8g --cpus 2 \
--mount type=bind,source=c:\input,target=/input  \
--mount type=bind,source=c:\output,target=/output \
containerpreview.azurecr.io/microsoft/cognitive-services-form-recognizer \
Eula=accept \
Billing={BILLING_ENDPOINT_URI} \
ApiKey={BILLING_KEY} \
FormRecognizer:ComputerVisionApiKey={COMPUTER_VISION_API_KEY} \
FormRecognizer:ComputerVisionEndpointUri={COMPUTER_VISION_ENDPOINT_URI}
```

### <a name="logging-example-for-form-recognizer"></a>Przykład rejestrowania dla rozpoznawania formularza

```Docker
docker run --rm -it -p 5000:5000 --memory 8g --cpus 2 \
--mount type=bind,source=c:\input,target=/input  \
--mount type=bind,source=c:\output,target=/output \
containerpreview.azurecr.io/microsoft/cognitive-services-form-recognizer \
Eula=accept \
Billing={BILLING_ENDPOINT_URI} \
ApiKey={BILLING_KEY} \
FormRecognizer:ComputerVisionApiKey={COMPUTER_VISION_API_KEY} \
FormRecognizer:ComputerVisionEndpointUri={COMPUTER_VISION_ENDPOINT_URI}
Logging:Console:LogLevel:Default=Information
```


## <a name="next-steps"></a>Kolejne kroki

* Przegląd [Zainstaluj i uruchom kontenery](form-recognizer-container-howto.md).
