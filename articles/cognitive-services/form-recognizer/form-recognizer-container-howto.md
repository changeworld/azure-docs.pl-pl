---
title: Jak zainstalować i uruchomić kontener dla aparatu rozpoznawania formularzy
titleSuffix: Azure Cognitive Services
description: Dowiedz się, jak analizować dane formularzy i tabel za pomocą kontenera rozpoznawania formularzy.
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 06/19/2019
ms.author: dapine
ms.openlocfilehash: 3c0129275ecf78e6a4e6b9286f975ded2b6f9ae3
ms.sourcegitcommit: bba811bd615077dc0610c7435e4513b184fbed19
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/27/2019
ms.locfileid: "70051209"
---
# <a name="install-and-run-form-recognizer-containers"></a>Instalowanie i uruchamianie kontenerów aparatów rozpoznawania formularzy

Aparat rozpoznawania formularzy platformy Azure stosuje technologię uczenia maszynowego do identyfikowania i wyodrębniania par klucz-wartość oraz tabel z formularzy. Kojarzy wartości i wpisy tabeli z parami klucz-wartość, a następnie wyprowadza dane ze strukturą, które zawierają relacje w oryginalnym pliku. 

Aby zmniejszyć złożoność i łatwo zintegrować niestandardowy model aparatu rozpoznawania formularzy w procesie automatyzacji przepływu pracy lub innej aplikacji, można wywołać model przy użyciu prostego interfejsu API REST. Potrzebne są tylko pięć dokumentów formularzy (lub jednej pustej postaci i dwóch wypełnionych formularzy), dzięki czemu można szybko uzyskać wyniki, dokładnie i dostosować je do określonej zawartości. Nie jest konieczna intensywna interwencja ręczna ani Obszerna wiedza naukowa o danych. Nie wymaga to etykietowania danych ani adnotacji danych.

|Funkcja|Funkcje|
|-|-|
|Rozpoznawanie formularzy| <li>Przetwarza pliki PDF, PNG i JPG<li>Pociąga za siebie modele niestandardowe z co najmniej pięcioma formami tego samego układu <li>Wyodrębnia pary klucz-wartość i informacje o tabeli <li>Używa funkcji Rozpoznawanie tekstu platformy Azure Cognitive Services interfejs API przetwarzania obrazów do wykrywania i wyodrębniania wydrukowanych tekstu z obrazów wewnątrz formularzy<li>Nie wymaga adnotacji ani etykietowania|

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="prerequisites"></a>Wymagania wstępne

Przed użyciem kontenerów aparatów rozpoznawania formularzy należy spełnić następujące wymagania wstępne:

|Wymagane|Cel|
|--|--|
|Aparat platformy Docker| Aparat platformy Docker musi być zainstalowany na [komputerze-hoście](#the-host-computer). Platforma Docker zawiera pakiety, które konfigurują środowisko platformy Docker w systemach [macOS](https://docs.docker.com/docker-for-mac/), [Windows](https://docs.docker.com/docker-for-windows/)i [Linux](https://docs.docker.com/engine/installation/#supported-platforms). Aby uzyskać podstawowe informacje na temat platformy Docker i kontenerów, zobacz [Docker — omówienie](https://docs.docker.com/engine/docker-overview/).<br><br> Docker należy skonfigurować w taki sposób, aby umożliwić kontenerów, aby nawiązać połączenie z, a następnie wysyłać danych dotyczących rozliczeń do platformy Azure. <br><br> W systemie Windows program Docker musi być również skonfigurowany do obsługi kontenerów systemu Linux.<br><br>|
|Znajomość platformy Docker | Należy dysponować podstawową wiedzą na temat pojęć platformy Docker, takich jak rejestry, repozytoria, kontenery i obrazy kontenerów oraz znajomość podstawowych `docker` poleceń.|
|Interfejs wiersza polecenia platformy Azure| Zainstaluj [interfejs wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) na hoście.|
|Zasób interfejs API przetwarzania obrazów| Do przetworzenia zeskanowanych dokumentów i obrazów potrzebny jest zasób przetwarzanie obrazów. Możesz uzyskać dostęp do funkcji Rozpoznawanie tekstu jako zasobu platformy Azure (interfejsu API REST lub zestawu SDK) lub kontenera *usług poznawczego rozpoznawania tekstu* [](../Computer-vision/computer-vision-how-to-install-containers.md##get-the-container-image-with-docker-pull). Obowiązują standardowe opłaty za rozliczanie. <br><br>Przekaż zarówno klucz interfejsu API, jak i punkty końcowe dla zasobu przetwarzanie obrazów (Azure Cloud lub Cognitive Services Container). Użyj tego klucza interfejsu API i punktu końcowego jako **{COMPUTER_VISION_API_KEY}** i **{COMPUTER_VISION_ENDPOINT_URI}** .<br><br> Jeśli używasz kontenera *poznawcze-Services-rozpoznaje-Text* , upewnij się, że:<br><br>Klucz przetwarzanie obrazów dla kontenera aparatu rozpoznawania formularzy jest kluczem określonym w przetwarzanie obrazów `docker run` polecenie dla kontenera " *poznawcze-Services-rozpoznaje-Text* ".<br>Punkt końcowy rozliczeniowy jest punktem końcowym kontenera (na przykład `http://localhost:5000`). Jeśli używasz kontenera przetwarzanie obrazów kontenerów i aparatu rozpoznawania formularzy razem na tym samym hoście, nie można uruchomić obu tych elementów przy użyciu domyślnego portu *5000*.  |
|Zasób aparatu rozpoznawania formularza |Aby korzystać z tych kontenerów, musisz mieć:<br><br>Zasób **aparatu rozpoznawania formularza** platformy Azure, aby pobrać skojarzony klucz interfejsu API i identyfikator URI punktu końcowego. Obie wartości są dostępne na stronie Omówienie i klucze **aparatu rozpoznawania Azure Portal formularzy** , a obie wartości są wymagane do uruchomienia kontenera.<br><br>**{FORM_RECOGNIZER_API_KEY}** : Jeden z dwóch dostępnych kluczy zasobów na stronie klucze<br><br>**{FORM_RECOGNIZER_ENDPOINT_URI}** : Punkt końcowy zgodnie z opisem na stronie Przegląd|

## <a name="request-access-to-the-container-registry"></a>Zażądaj dostępu do rejestru kontenerów

Musisz najpierw zakończyć i przesłać formularz [żądania dostępu kontenerów rozpoznawania Cognitive Services formularzy](https://aka.ms/FormRecognizerRequestAccess) , aby zażądać dostępu do kontenera. Spowoduje to również zalogowanie się do przetwarzanie obrazów. Nie musisz oddzielnie rejestrować się w celu przetwarzanie obrazów formularza żądania. 

[!INCLUDE [Request access to the container registry](../../../includes/cognitive-services-containers-request-access-only.md)]

[!INCLUDE [Authenticate to the container registry](../../../includes/cognitive-services-containers-access-registry.md)]

## <a name="the-host-computer"></a>Komputer-host

[!INCLUDE [Host Computer requirements](../../../includes/cognitive-services-containers-host-computer.md)]

### <a name="container-requirements-and-recommendations"></a>Kontener wymagania i zalecenia

Minimalna i zalecana liczba rdzeni procesora CPU i pamięci do przydzielenia dla każdego kontenera aparatu rozpoznawania formularzy są opisane w poniższej tabeli:

| Kontener | Minimalne | Zalecane |
|-----------|---------|-------------|
|cognitive-services-form-recognizer | 2 rdzeń, 4 GB pamięci | 4 rdzenie, 8 GB pamięci |

* Każdy rdzeń musi mieć co najmniej 2,6 gigaherca (GHz) lub szybszy.
* TPS — liczba transakcji na sekundę
* Rdzeń i pamięć odpowiadają `--cpus` ustawieniom i `--memory` , które są `docker run` używane jako część polecenia.

> [!Note]
> Wartości minimalne i zalecane są zależne od limitów platformy Docker, a *nie* zasobów maszyn hosta.

## <a name="get-the-container-image-with-the-docker-pull-command"></a>Pobierz obraz kontenera za pomocą polecenia docker pull

Obrazy kontenerów dla aparatu rozpoznawania formularzy są dostępne w następującym repozytorium:

| Kontener | Repozytorium |
|-----------|------------|
| cognitive-services-form-recognizer | `containerpreview.azurecr.io/microsoft/cognitive-services-form-recognizer:latest` |

Jeśli zamierzasz używać `cognitive-services-recognize-text` [kontenera](../Computer-vision/computer-vision-how-to-install-containers.md##get-the-container-image-with-docker-pull)zamiast usługi aparat rozpoznawania formularzy, `docker pull` upewnij się, że używasz polecenia z poprawną nazwą kontenera: 

```
docker pull containerpreview.azurecr.io/microsoft/cognitive-services-recognize-text:latest
```

[!INCLUDE [Tip for using docker list](../../../includes/cognitive-services-containers-docker-list-tip.md)]

### <a name="docker-pull-for-the-form-recognizer-container"></a>Wypychanie platformy Docker dla kontenera aparatów rozpoznawania formularzy

#### <a name="form-recognizer"></a>Rozpoznawanie formularzy

Aby uzyskać kontener aparatu rozpoznawania formularzy, użyj następującego polecenia:

```Docker
docker pull containerpreview.azurecr.io/microsoft/cognitive-services-form-recognizer:latest
```

## <a name="how-to-use-the-container"></a>Jak używać kontenera

Gdy kontener znajduje się na [komputerze hosta](#the-host-computer), użyj następującego procesu, aby współpracować z kontenerem.

1. [Uruchom kontener](#run-the-container-by-using-the-docker-run-command)z wymaganymi ustawieniami rozliczania. Więcej [przykładów](form-recognizer-container-configuration.md#example-docker-run-commands) `docker run` polecenia jest dostępnych.
1. [Zbadaj punkt końcowy przewidywania kontenera](#query-the-containers-prediction-endpoint).

## <a name="run-the-container-by-using-the-docker-run-command"></a>Uruchamianie kontenera przy użyciu polecenia Docker Run

Użyj polecenia [Docker Run](https://docs.docker.com/engine/reference/commandline/run/) , aby uruchomić jeden z trzech kontenerów. Polecenie używa następujących parametrów:

| Symbol zastępczy | Value |
|-------------|-------|
|{FORM_RECOGNIZER_API_KEY} | Ten klucz jest używany do uruchamiania kontenera. Jest ona dostępna na stronie **klucze aparatu rozpoznawania Azure Portal formularza** .  |
|{FORM_RECOGNIZER_ENDPOINT_URI} | Wartość identyfikatora URI punktu końcowego rozliczenia jest dostępna na stronie **Omówienie aparatu rozpoznawania Azure Portal formularza** .|
|{COMPUTER_VISION_API_KEY}| Klucz jest dostępny na stronie **klucze interfejs API przetwarzania obrazów** Azure Portal.|
|{COMPUTER_VISION_ENDPOINT_URI}|Punkt końcowy rozliczeń. Jeśli używasz zasobu przetwarzanie obrazów opartego na chmurze, wartość identyfikatora URI jest dostępna na stronie **przegląd Azure Portal interfejs API przetwarzania obrazów** . Jeśli używasz `cognitive-services-recognize-text` kontenera, użyj adresu URL punktu końcowego rozliczenia, który jest przesyłany do kontenera `docker run` w poleceniu.|

Zastąp te parametry własnymi wartościami w poniższym przykładowym `docker run` poleceniu.

### <a name="form-recognizer"></a>Rozpoznawanie formularzy

```bash
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

To polecenie:

* Uruchamia kontener aparatu rozpoznawania formularza z obrazu kontenera.
* Przydziela 2 rdzenie procesora CPU i 8 gigabajtów (GB) pamięci.
* Udostępnia port TCP 5000 i przydziela pseudo-TTY dla kontenera.
* Automatycznie usuwa kontener po zakończeniu. Obraz kontenera jest nadal dostępny na komputerze-hoście.
* Instaluje/Input i wolumin/Output do kontenera.

[!INCLUDE [Running multiple containers on the same host H2](../../../includes/cognitive-services-containers-run-multiple-same-host.md)]

### <a name="run-separate-containers-as-separate-docker-run-commands"></a>Uruchamianie oddzielnych kontenerów jako oddzielnych poleceń uruchomienia platformy Docker

W przypadku aparatu rozpoznawania formularzy i aparatu rozpoznawania tekstu, które są hostowane lokalnie na tym samym hoście, użyj następujących dwóch przykładowych poleceń interfejsu wiersza polecenia platformy Docker:

Uruchom pierwszy kontener na porcie 5000. 

```bash 
docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 \
--mount type=bind,source=c:\input,target=/input  \
--mount type=bind,source=c:\output,target=/output \
containerpreview.azurecr.io/microsoft/cognitive-services-form-recognizer \
Eula=accept \
Billing={FORM_RECOGNIZER_ENDPOINT_URI} \
ApiKey={FORM_RECOGNIZER_API_KEY}
FormRecognizer:ComputerVisionApiKey={COMPUTER_VISION_API_KEY} \
FormRecognizer:ComputerVisionEndpointUri={COMPUTER_VISION_ENDPOINT_URI}
```

Uruchom drugi kontener na porcie 5001.

```bash 
docker run --rm -it -p 5001:5000 --memory 4g --cpus 1 \
containerpreview.azurecr.io/microsoft/cognitive-services-recognize-text \
Eula=accept \
Billing={COMPUTER_VISION_ENDPOINT_URI} \
ApiKey={COMPUTER_VISION_API_KEY}
```
Każdy kolejny kontener powinien znajdować się na innym porcie. 

### <a name="run-separate-containers-with-docker-compose"></a>Uruchamianie oddzielnych kontenerów za pomocą Docker Compose

W przypadku aparatu rozpoznawania formularzy i aparatu rozpoznawania tekstu, które są hostowane lokalnie na tym samym hoście, zapoznaj się z poniższym przykładem Docker Compose pliku YAML. Aparat rozpoznawania `{COMPUTER_VISION_API_KEY}` tekstu musi być taki sam dla `formrecognizer` kontenerów i `ocr` . Jest używana tylko `ocr` w kontenerze, `ocr` ponieważ `formrecognizer` kontener używa nazwy i portu. `{COMPUTER_VISION_ENDPOINT_URI}` 

```docker
version: '3.3'
services:   
  ocr:
    image: "containerpreview.azurecr.io/microsoft/cognitive-services-recognize-text"
    deploy:
      resources:
        limits:
          cpus: '2'
          memory: 8g
        reservations:
          cpus: '1'
          memory: 4g
    environment:
      eula: accept
      billing: "{COMPUTER_VISION_ENDPOINT_URI}"
      apikey: "{COMPUTER_VISION_API_KEY}"

  formrecognizer:
    image: "containerpreview.azurecr.io/microsoft/cognitive-services-form-recognizer"
    deploy:
      resources:
        limits:
          cpus: '2'
          memory: 8g
        reservations:
          cpus: '1'
          memory: 4g
    environment:
      eula: accept
      billing: "{FORM_RECOGNIZER_ENDPOINT_URI}"
      apikey: "{FORM_RECOGNIZER_API_KEY}"
      FormRecognizer__ComputerVisionApiKey: {COMPUTER_VISION_API_KEY}
      FormRecognizer__ComputerVisionEndpointUri: "http://ocr:5000"
      FormRecognizer__SyncProcessTaskCancelLimitInSecs: 75
    links:
      - ocr
    volumes:
      - type: bind
        source: c:\output
        target: /output
      - type: bind
        source: c:\input
        target: /input
    ports:
      - "5000:5000"
```

> [!IMPORTANT]
> `FormRecognizer:ComputerVisionApiKey` `Billing` `ApiKey`Aby można było uruchomić kontener `FormRecognizer:ComputerVisionEndpointUri` , należy określić,, i, a także opcje i. w przeciwnym razie kontener nie zostanie uruchomiony. `Eula` Aby uzyskać więcej informacji, zobacz [rozliczeń](#billing).

## <a name="query-the-containers-prediction-endpoint"></a>Zbadaj punkt końcowy przewidywania kontenera

|Kontener|Endpoint|
|--|--|
|formularz-aparat rozpoznawania|http://localhost:5000

### <a name="form-recognizer"></a>Rozpoznawanie formularzy

Kontener zawiera interfejsy API punktu końcowego zapytania opartego na protokole WebSocket, do których można uzyskać dostęp za pośrednictwem [dokumentacji zestawu SDK usług aparatu rozpoznawania formularzy](https://docs.microsoft.com/azure/cognitive-services/form-recognizer/).

Domyślnie zestaw SDK aparatu rozpoznawania formularzy używa Usługi online. Aby użyć kontenera, należy zmienić metodę inicjacji. Zapoznaj się z poniższymi przykładami.

#### <a name="for-c"></a>DlaC#

Zmień użycie tego wywołania inicjalizacji Azure-Cloud:

```csharp
var config =
    FormRecognizerConfig.FromSubscription(
        "YourSubscriptionKey",
        "YourServiceRegion");
```
do tego wywołania, które używa punktu końcowego kontenera:

```csharp
var config =
    FormRecognizerConfig.FromEndpoint(
        "ws://localhost:5000/formrecognizer/v1.0-preview/custom",
        "YourSubscriptionKey");
```

#### <a name="for-python"></a>Dla języka Python

Zmień użycie tego wywołania inicjalizacji Azure-Cloud:

```python
formrecognizer_config =
    formrecognizersdk.FormRecognizerConfig(
        subscription=formrecognizer_key, region=service_region)
```

do tego wywołania, które używa punktu końcowego kontenera:

```python
formrecognizer_config = 
    formrecognizersdk.FormRecognizerConfig(
        subscription=formrecognizer_key,
        endpoint="ws://localhost:5000/formrecognizer/v1.0-preview/custom"
```

### <a name="form-recognizer"></a>Rozpoznawanie formularzy

Kontener zawiera interfejsy API punktu końcowego REST, które można znaleźć na stronie [interfejsu API rozpoznawania formularzy](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api/operations/AnalyzeWithCustomModel) .


[!INCLUDE [Validate container is running - Container's API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]


## <a name="stop-the-container"></a>Zatrzymaj kontener

[!INCLUDE [How to stop the container](../../../includes/cognitive-services-containers-stop.md)]

## <a name="troubleshooting"></a>Rozwiązywanie problemów

Po uruchomieniu kontenera kontener używa **stdout** i **stderr** do danych wyjściowych, które są przydatne do rozwiązywania problemów występujących podczas uruchamiania lub uruchamiania kontenera.

## <a name="billing"></a>Rozliczenia

Kontenery aparatu rozpoznawania formularzy wysyłają informacje o rozliczeniach do platformy Azure przy użyciu zasobu _aparatu rozpoznawania formularzy_ na koncie platformy Azure.

[!INCLUDE [Container's Billing Settings](../../../includes/cognitive-services-containers-how-to-billing-info.md)]

Aby uzyskać więcej informacji o tych opcjach, zobacz [skonfigurować kontenery](form-recognizer-container-configuration.md).

<!--blogs/samples/video courses -->

[!INCLUDE [Discoverability of more container information](../../../includes/cognitive-services-containers-discoverability.md)]

## <a name="summary"></a>Podsumowanie

W tym artykule przedstawiono koncepcje i przepływ pracy służące do pobierania, instalowania i uruchamiania kontenerów aparatów rozpoznawania. Podsumowanie:

* Aparat rozpoznawania formularzy udostępnia jeden kontener systemu Linux dla platformy Docker.
* Obrazy kontenerów są pobierane z prywatnego rejestru kontenerów na platformie Azure.
* Obrazy kontenera Uruchom na platformie Docker.
* Można użyć interfejsu API REST lub zestawu SDK REST do wywołania operacji w kontenerze aparatu rozpoznawania formularzy, określając identyfikator URI hosta kontenera.
* Podczas tworzenia wystąpienia kontenera należy określić informacje o rozliczeniach.

> [!IMPORTANT]
>  Kontenery usługi cognitive Services nie są licencjonowane do uruchomienia bez połączenia z platformy Azure do zbierania danych. Klienci muszą włączyć kontener, aby komunikować informacje rozliczeniowe usłudze zliczania przez cały czas. Kontenery Cognitive Services nie wysyłają danych klienta (na przykład obrazu lub tekstu, który jest analizowany) do firmy Microsoft.

## <a name="next-steps"></a>Następne kroki

* Zapoznaj się z tematem [Konfigurowanie kontenerów](form-recognizer-container-configuration.md) dla ustawień konfiguracji.
* Użyj więcej [kontenerów Cognitive Services](../cognitive-services-container-support.md).
