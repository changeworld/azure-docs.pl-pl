---
title: Konfigurowanie kontenera — aparat rozpoznawania formularza
titleSuffix: Azure Cognitive Services
description: Dowiedz się, jak skonfigurować kontener rozpoznawania formularza, aby przeanalizować dane formularza i tabeli.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: form-recognizer
ms.topic: overview
ms.date: 05/07/2019
ms.author: pafarley
ms.openlocfilehash: 3f6cc32f6fb2a9fb11220ac1a3134fb3ae4d2f84
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/06/2019
ms.locfileid: "65147465"
---
# <a name="configure-form-recognizer-containers"></a>Konfigurowanie aparatu rozpoznającego w postaci kontenerów

Kontenery aparatu rozpoznawania formularza Umożliwiaj klientom tworzenie architektury aplikacji, która jest zoptymalizowana, aby móc korzystać z możliwości chmury niezawodne i lokalizacja krawędzi.

**Rozpoznawania formularza** środowisko uruchomieniowe kontenera jest konfigurowana przy użyciu `docker run` argumenty polecenia. Ten kontener ma wymagane ustawienia, wraz z kilku ustawień opcjonalnych. Kilka [przykłady](#example-docker-run-commands) polecenia są dostępne. Ustawienia specyficzne dla kontenera są ustawienia rozliczeń.

## <a name="configuration-settings"></a>Ustawienia konfiguracji

[!INCLUDE [Container shared configuration settings table](../../../includes/cognitive-services-containers-configuration-shared-settings-table.md)]

> [!IMPORTANT]
> [ `ApiKey` ](#apikey-configuration-setting), [ `Billing` ](#billing-configuration-setting), I [ `Eula` ](#eula-setting) ustawienia są używane razem. Ponadto należy podać prawidłowe wartości dla wszystkich trzech ich; w przeciwnym razie Nie można uruchomić kontener. Aby uzyskać więcej informacji na temat tworzenia wystąpienia kontenera za pomocą tych ustawień konfiguracji, zobacz [rozliczeń](form-recognizer-container-howto.md#billing).

## <a name="apikey-configuration-setting"></a>Ustawienie konfiguracji ApiKey

`ApiKey` Ustawienie umożliwia określenie klucza zasobów platformy Azure używane do śledzenia informacji rozliczeniowych dla kontenera. Należy określić wartość dla ApiKey i wartość musi być prawidłowy klucz dla _rozpoznawania formularza_ zasób określony dla [ `Billing` ](#billing-configuration-setting) ustawienia konfiguracji.

To ustawienie można znaleźć w tym miejscu następujące:

* Witryna Azure Portal: **Formularz aparatu rozpoznawania** zarządzanie zasobami w obszarze **kluczy**

## <a name="applicationinsights-setting"></a>Ustawienie dotycząca usługi Application Insights

[!INCLUDE [Container shared configuration ApplicationInsights settings](../../../includes/cognitive-services-containers-configuration-shared-settings-application-insights.md)]

## <a name="billing-configuration-setting"></a>Ustawienie konfiguracji rozliczeń

`Billing` Ustawienie określa identyfikator URI punktu końcowego z _rozpoznawania formularza_ zasobów na platformie Azure używane do pomiarów informacji rozliczeniowych dla kontenera. Należy określić wartość dla tego ustawienia konfiguracji, a wartość musi być prawidłowy identyfikator URI punktu końcowego dla _rozpoznawania formularza_ zasobów na platformie Azure. Kontener raportów użycia dotyczących co 10 do 15 minut.

To ustawienie można znaleźć w tym miejscu następujące:

* Witryna Azure Portal: **Formularz aparatu rozpoznawania** Przegląd, etykietą `Endpoint`

|Wymagane| Name (Nazwa) | Typ danych | Opis |
|--|------|-----------|-------------|
|Yes| `Billing` | Ciąg | Identyfikator URI punktu końcowego rozliczeń<br><br>Przykład:<br>`Billing=https://westus2.api.cognitive.microsoft.com/` |

## <a name="eula-setting"></a>Umowa licencyjna EULA ustawienie

[!INCLUDE [Container shared configuration eula settings](../../../includes/cognitive-services-containers-configuration-shared-settings-eula.md)]

## <a name="fluentd-settings"></a>Ustawienia Fluentd

[!INCLUDE [Container shared configuration fluentd settings](../../../includes/cognitive-services-containers-configuration-shared-settings-fluentd.md)]

## <a name="logging-settings"></a>Ustawienia rejestrowania

[!INCLUDE [Container shared configuration logging settings](../../../includes/cognitive-services-containers-configuration-shared-settings-logging.md)]


## <a name="mount-settings"></a>Ustawienia instalacji

Użyj powiązania instaluje do odczytu i zapisu danych do i z kontenera. Można określić instalacji danych wejściowych lub wyjściowych instalacji, określając `--mount` opcji [platformy docker, uruchom](https://docs.docker.com/engine/reference/commandline/run/) polecenia.

Kontener formularz rozpoznawania wymaga instalacji wejściowych i wyjściowych. Wejściowy instalacji może być tylko do odczytu i jest wymagany dostęp do danych, która będzie służyć do szkolenia i oceniania. Dane wyjściowe instalacji musi być zapisywalny i będzie służyć do przechowywania danych tymczasowych i modeli.

Dokładna składnia lokalizację instalacji hosta różni się zależnie od systemu operacyjnego hosta. Ponadto [komputerze-hoście](form-recognizer-container-howto.md#the-host-computer)w lokalizacji instalacji może być niedostępna z powodu konfliktu między uprawnienia użyte dla konta usługi Docker i hosta instalacji uprawnienia do lokalizacji.

|Optional (Opcjonalność)| Name (Nazwa) | Typ danych | Opis |
|-------|------|-----------|-------------|
|Wymagane| `Input` | String | Miejsce docelowe instalacji danych wejściowych. Wartość domyślna to `/input`.    <br><br>Przykład:<br>`--mount type=bind,src=c:\input,target=/input`|
|Wymagane| `Output` | String | Miejsce docelowe instalacji danych wyjściowych. Wartość domyślna to `/output`.  <br><br>Przykład:<br>`--mount type=bind,src=c:\output,target=/output`|

## <a name="example-docker-run-commands"></a>Przykład platformy docker, Uruchom polecenia

W poniższych przykładach używane ustawienia konfiguracji, aby zilustrować, jak pisać i użyj `docker run` poleceń.  Po uruchomieniu kontenera będzie działać do momentu [zatrzymać](form-recognizer-container-howto.md#stop-the-container) go.

* **Znak kontynuacji wiersza**: Platformy Docker w poniższych sekcjach użyto ukośnika, `\`, jako znak kontynuacji wiersza. Zamień lub Usuń ten na podstawie wymagań systemu operacyjnego hosta.
* **Kolejność argumentów**: Nie należy zmieniać kolejność argumentów, jeśli nie znasz bardzo kontenerów platformy Docker.

Zastąp {_argument_name_} własnymi wartościami:

| Symbol zastępczy | Wartość |
|-------------|-------|
|{BILLING_KEY} | Ten klucz jest używany do uruchamiania kontenera i jest dostępny na stronie formularza rozpoznawania klucze w witrynie Azure portal.  |
|{BILLING_ENDPOINT_URI} | Punkt końcowy rozliczeń wartość identyfikatora URI jest dostępna na stronie Przegląd rozpoznawania formularza witryny Azure portal.|
|{COMPUTER_VISION_API_KEY}| Klucz jest dostępny na stronie klucze interfejsu API przetwarzania komputera w witrynie Azure portal.|
|{COMPUTER_VISION_ENDPOINT_URI}|Punkt końcowy rozliczeń. Jeśli używasz zasobów opartych na chmurze przetwarzania obrazów to wartość identyfikatora URI jest dostępny na stronie Przegląd interfejsu API przetwarzania komputera witryny Azure portal. Jeśli używasz `cognitive-services-recognize-text` kontener, użyj rozliczeń adresu URL punktu końcowego przekazany do kontenera w `docker run` polecenia.|

> [!IMPORTANT]
> `Eula`, `Billing`, I `ApiKey` opcje muszą być określone w celu uruchomienia kontenera; w przeciwnym razie nie uruchamia się kontener.  Aby uzyskać więcej informacji, zobacz [rozliczeń](#billing-configuration-setting).
> Wartość ApiKey **klucz** ze strony klucze zasobów rozpoznawania formularza platformy Azure.

## <a name="form-recognizer-container-docker-examples"></a>Formularz rozpoznawania kontenera platformy Docker przykłady

W poniższych przykładach platformy Docker są w kontenerze aparatu rozpoznawania formularza.

### <a name="basic-example-for-form-recognizer"></a>Podstawowy przykład rozpoznawania formularza

```Docker
docker run --rm -it -p 5000:5000 --memory 8g --cpus 2 \
containerpreview.azurecr.io/microsoft/cognitive-services-form-recognizer \
Eula=accept \
--mount type=bind,source=c:\input,target=/input  \
--mount type=bind,source=c:\output,target=/output \
Billing={BILLING_ENDPOINT_URI} \
ApiKey={BILLING_KEY} \
FormRecognizer:ComputerVisionApiKey={COMPUTER_VISION_API_KEY} \
FormRecognizer:ComputerVisionEndpointUri={COMPUTER_VISION_ENDPOINT_URI}
```

### <a name="logging-example-for-form-recognizer"></a>Przykład rejestrowania dla rozpoznawania formularza

```Docker
docker run --rm -it -p 5000:5000 --memory 8g --cpus 2 \
containerpreview.azurecr.io/microsoft/cognitive-services-form-recognizer \
Eula=accept \
--mount type=bind,source=c:\input,target=/input  \
--mount type=bind,source=c:\output,target=/output \
Billing={BILLING_ENDPOINT_URI} \
ApiKey={BILLING_KEY} \
FormRecognizer:ComputerVisionApiKey={COMPUTER_VISION_API_KEY} \
FormRecognizer:ComputerVisionEndpointUri={COMPUTER_VISION_ENDPOINT_URI}
Logging:Console:LogLevel:Default=Information
```


## <a name="next-steps"></a>Kolejne kroki

* Przegląd [sposobu instalowania i uruchamiania kontenerów](form-recognizer-container-howto.md)
