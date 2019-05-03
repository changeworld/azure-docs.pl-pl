---
title: Zainstaluj i uruchom kontenera — aparat rozpoznawania formularza
titleSuffix: Azure Cognitive Services
description: Dowiedz się, jak analizować dane formularza i tabeli za pomocą kontenera aparatu rozpoznawania formularza.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: form-recognizer
ms.topic: overview
ms.date: 05/07/2019
ms.author: pafarley
ms.openlocfilehash: 5d4374b329049e2e55966a28567c5232be77abda
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/02/2019
ms.locfileid: "65027068"
---
# <a name="install-and-run-form-recognizer-containers"></a>Instalowanie i uruchamianie kontenerów aparatu rozpoznawania formularza
Aparat rozpoznawania formularza ma zastosowanie technologii uczenia maszynowego, aby zidentyfikować i prowadzenie formularzy pary klucz wartość i tabel. Go kojarzy wartości i wpisy tabeli, aby je, a następnie wysyła dane ze strukturą, który zawiera relacje w oryginalnym pliku. Możesz wywołać niestandardowy model rozpoznawania formularza przy użyciu prostego interfejsu API REST w celu zredukowania złożoności i łatwo zintegrować ją w proces automatyzacji przepływu pracy lub innych aplikacji. Tylko pięć dokumentów (lub pusty formularz) są potrzebne, więc można uzyskać wyniki, szybko, dokładnie i być dostosowane do określonych zawartości, bez dużych ręcznej interwencji lub wiele różnych danych do analizy doświadczenia. Nie wymaga danych etykietowania lub adnotacji danych.

|Funkcja|Funkcje|
|-|-|
|Rozpoznawanie formularzy| <li>Pliki procesów typu plików PDF, PNG i JPG.<li>Szkolenie modeli niestandardowych modeli za pomocą minimalnej 5 rodzaje taki sam układ. <li>Wyodrębnia pary klucz wartość i informacji z tabeli. <li>Używa Cognitive Service komputera Vision API RecognizeText, aby wykrywanie i wyodrębnianie tekstu drukowanego z obrazów na formularzach.<li>Nie wymaga adnotację lub etykietowania.|

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem korzystania z kontenerów aparatu rozpoznającego w formularzu, musi spełniać następujące wymagania wstępne:

|Wymagane|Przeznaczenie|
|--|--|
|Aparat platformy docker| Aparat platformy Docker zainstalowany na musisz [komputerze-hoście](#the-host-computer). Środowisko docker zawiera pakiety, które konfigurują środowisko platformy Docker na [macOS](https://docs.docker.com/docker-for-mac/), [Windows](https://docs.docker.com/docker-for-windows/), i [Linux](https://docs.docker.com/engine/installation/#supported-platforms). Aby uzyskać podstawowe informacje na temat platformy Docker i kontenerów, zobacz [Docker — omówienie](https://docs.docker.com/engine/docker-overview/).<br><br> Docker należy skonfigurować w taki sposób, aby umożliwić kontenerów, aby nawiązać połączenie z, a następnie wysyłać danych dotyczących rozliczeń do platformy Azure. <br><br> **Na Windows**, platformy Docker musi być również skonfigurowany do obsługi kontenerów systemu Linux.<br><br>|
|Znajomość platformy Docker | Należy mieć podstawową wiedzę na temat pojęć usługi Docker, takich jak rejestry, repozytoria, kontenery i obrazów kontenerów, a także wiedzę na temat basic `docker` poleceń.|
|Interfejs wiersza polecenia platformy Azure| Musisz zainstalować [wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) na hoście.|
|Zasób interfejsu API przetwarzania komputera| Aby przetwarzać zeskanowane dokumenty i obrazy, **zasobów przetwarzania obrazów** jest wymagana. Możesz uzyskać dostęp **Rozpoznaj tekst** funkcji jako dowolnego zasobu platformy Azure (interfejs API REST lub zestawu SDK) lub `cognitive-services-recognize-text` kontenera. Zwykle opłaty mają zastosowanie. <br><br>Należy przekazać klucz i punkt końcowy rozliczeń dla określonego zasobu przetwarzania obrazów (w chmurze platformy Azure lub kontener usług Cognitive Services). Użyj tego klucza i rozliczeń punktu końcowego jako {COMPUTER_VISION_API_KEY} i {COMPUTER_VISION_BILLING_ENDPOINT_URI}.<br><br> Jeśli używasz  **`cognitive-services-recognize-text` kontenera**, upewnij się, że:<br><br>* Klucz wizualizacji komputerowej dla kontenera aparatu rozpoznawania formularza jest klucz określony w nazwie przetwarzania obrazów `docker run` polecenie, aby uzyskać `cognitive-services-recognize-text` kontenera.<br>* Rozliczeń punktu końcowego jest punkt końcowy kontenera, takich jak `https://localhost:5000`. Jeśli używasz kontenerów systemów przetwarzania obrazów, jak i rozpoznawania formularza z ze sobą na tym samym hoście, one zarówno kończy się przy użyciu domyślnego portu `5000`.  |  
|Formularz rozpoznawania zasobu |Aby można było używać tych kontenerów, musisz mieć:<br><br>A _rozpoznawania formularza_ zasobów platformy Azure, aby uzyskać skojarzonego klucza rozliczenia i rozliczeń identyfikator URI punktu końcowego. Obie wartości są dostępne w witrynie Azure portal **rozpoznawania formularza** strony Przegląd i klucze i są wymagane do uruchomienia kontenera.<br><br>**{BILLING_KEY}** : klucz zasobu<br><br>**{BILLING_ENDPOINT_URI}** : przykład identyfikatora URI punktu końcowego: `https://westus.api.cognitive.microsoft.com/forms/v1.0`| 

## <a name="request-access-to-the-container-registry"></a>Żądanie dostępu do rejestru kontenerów

Najpierw należy wypełnić oraz przesłać [formularz żądania kontenerów aparatu rozpoznawania formularza Cognitive Services](https://aka.ms/FormRecognizerRequestAccess) Aby zażądać dostępu do kontenera. To spowoduje także zasubskrybować możesz przetwarzania obrazów. Nie musisz oddzielnie Zarejestruj się w formularzu żądania przetwarzania obrazów. 

[!INCLUDE [Request access to the container registry](../../../includes/cognitive-services-containers-request-access-only.md)]

[!INCLUDE [Authenticate to the container registry](../../../includes/cognitive-services-containers-access-registry.md)]

## <a name="the-host-computer"></a>Komputer-host

[!INCLUDE [Host Computer requirements](../../../includes/cognitive-services-containers-host-computer.md)]

### <a name="container-requirements-and-recommendations"></a>Kontener wymagania i zalecenia

W poniższej tabeli opisano minimalne i zalecane rdzeni procesora CPU i pamięci do przydzielenia dla każdego kontenera aparatu rozpoznawania formularza.

| Kontener | Minimalne | Zalecane |
|-----------|---------|-------------|
|cognitive-services-form-recognizer | 2 rdzeni, 4 GB pamięci RAM | 4 podstawowe, 8 GB pamięci RAM |

* Każdego rdzenia musi mieć co najmniej 2,6 gigaherc (GHz) lub szybszy.
* TPS - transakcji na sekundę

Rdzeni i pamięci odpowiadają `--cpus` i `--memory` ustawienia, które są używane jako część `docker run` polecenia.

> [!Note]
> Wartości minimalne i zalecane opierają się poza granice platformy Docker i *nie* host machine zasobów.

## <a name="get-the-container-image-with-docker-pull"></a>Pobierz obraz kontenera przy użyciu `docker pull`

Dostępne są obrazy kontenerów dla rozpoznawania formularza.

| Kontener | Repozytorium |
|-----------|------------|
| cognitive-services-form-recognizer | `containerpreview.azurecr.io/microsoft/cognitive-services-form-recognizer:latest` |

[!INCLUDE [Tip for using docker list](../../../includes/cognitive-services-containers-docker-list-tip.md)]


### <a name="docker-pull-for-the-form-recognizer-container"></a>Polecenie docker pull w kontenerze aparatu rozpoznawania formularza

#### <a name="form-recognizer"></a>Rozpoznawanie formularzy

```Docker
docker pull containerpreview.azurecr.io/microsoft/cognitive-services-form-recognizer:latest
```

## <a name="how-to-use-the-container"></a>Jak używać kontenera

Gdy kontener będzie na [komputerze-hoście](#the-host-computer), użyj następującego procesu do pracy z kontenerem.

1. [Uruchom kontener](#run-the-container-with-docker-run), przy użyciu ustawień rozliczeń wymagany, lecz nie jest używany. Więcej [przykłady](form-recognizer-container-configuration.md#example-docker-run-commands) z `docker run` polecenia są dostępne.
1. [Zapytanie do endpoint prognoz kontenera](#query-the-containers-prediction-endpoint).

## <a name="run-the-container-with-docker-run"></a>Uruchom kontener za pomocą `docker run`

Użyj [platformy docker, uruchom](https://docs.docker.com/engine/reference/commandline/run/) polecenie, aby uruchomić dowolne trzy kontenery. Polecenie używa następujących parametrów:

| Symbol zastępczy | Wartość |
|-------------|-------|
|{BILLING_KEY} | Ten klucz jest używany do uruchamiania kontenera i jest dostępny na stronie formularza rozpoznawania klucze w witrynie Azure portal.  |
|{BILLING_ENDPOINT_URI} | Punkt końcowy rozliczeń wartość identyfikatora URI jest dostępna na stronie Przegląd rozpoznawania formularza witryny Azure portal.|
|{COMPUTER_VISION_API_KEY}| Klucz jest dostępny na stronie klucze interfejsu API przetwarzania komputera w witrynie Azure portal.|
|{COMPUTER_VISION_ENDPOINT_URI}|Punkt końcowy rozliczeń. Jeśli używasz zasobów opartych na chmurze przetwarzania obrazów to wartość identyfikatora URI jest dostępny na stronie Przegląd interfejsu API przetwarzania komputera witryny Azure portal. Jeśli używasz `cognitive-services-recognize-text` kontener, użyj rozliczeń adresu URL punktu końcowego przekazany do kontenera w `docker run` polecenia.|

Zastąp parametry te wartości w poniższym przykładzie `docker run` polecenia.

### <a name="form-recognizer"></a>Rozpoznawanie formularzy

```bash
docker run --rm -it -p 5000:5000 --memory 8g --cpus 2 \
containerpreview.azurecr.io/microsoft/cognitive-services-form-recognizer \
--mount type=bind,source=c:\input,target=/input  \
--mount type=bind,source=c:\output,target=/output \
Eula=accept \
Billing={BILLING_ENDPOINT_URI} \
ApiKey={BILLING_KEY} \
FormRecognizer:ComputerVisionApiKey={COMPUTER_VISION_API_KEY} \
FormRecognizer:ComputerVisionEndpointUri={COMPUTER_VISION_ENDPOINT_URI}
```

To polecenie:

* Uruchamia kontener rozpoznawania formularza z obrazu kontenera
* Przydziela 2 rdzeni Procesora i 8 gigabajtów (GB) pamięci
* Uwidacznia TCP port 5000 i przydziela pseudo-TTY kontenera
* Automatycznie usuwa kontener po jej zakończenia. Obraz kontenera jest nadal dostępna na komputerze-hoście.
* Instaluje /input i wolumin/OUTPUT do kontenera

[!INCLUDE [Running multiple containers on the same host H2](../../../includes/cognitive-services-containers-run-multiple-same-host.md)]

### <a name="run-separate-containers-as-separate-docker-run-commands"></a>Uruchamiaj kontenery oddzielne jako osobne platformy docker, Uruchom polecenia

Dla kombinacji rozpoznawania formularza i rozpoznawania tekstu obsługiwana lokalnie na tym samym hoście należy wykonać dwa przykładowe polecenia interfejsu wiersza polecenia platformy Docker.

Uruchamianie pierwszego kontenera na porcie 5000. 

```bash 
docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 \
containerpreview.azurecr.io/microsoft/cognitive-services-form-recognizer \
Eula=accept \
Billing={BILLING_ENDPOINT_URI} \
ApiKey={BILLING_KEY}
FormRecognizer:ComputerVisionApiKey={COMPUTER_VISION_API_KEY} \
FormRecognizer:ComputerVisionEndpointUri={COMPUTER_VISION_ENDPOINT_URI}
```

Uruchom drugiego kontenera na porcie 5001.


```bash 
docker run --rm -it -p 5001:5000 --memory 4g --cpus 1 \
containerpreview.azurecr.io/microsoft/cognitive-services-recognize-text \
Eula=accept \
Billing={COMPUTER_VISION_ENDPOINT_URI} \
ApiKey={COMPUTER_VISION_API_KEY}
```
Każdy kontener kolejnych powinna być na innym porcie. 

### <a name="run-separate-containers-with-docker-compose"></a>Uruchamiaj osobne kontenery za pomocą narzędzia Docker Compose

Aparat rozpoznawania formularza i rozpoznawania tekstu kombinacji obsługiwana lokalnie na tym samym hoście następuje przykładowy plik Docker Compose YAML. Aparat rozpoznawania tekstu `{COMPUTER_VISION_API_KEY}` muszą być takie same dla obu `formrecognizer` i `ocr` kontenerów. `{COMPUTER_VISION_ENDPOINT_URI}` Jest używana tylko w `ocr` kontenera ponieważ `formrecognizer` kontenera używa `ocr` nazwy i portu. 

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
      apikey: {COMPUTER_VISION_API_KEY}  

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
      billing: "{BILLING_ENDPOINT_URI}"
      apikey: {BILLING_KEY}
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
> `Eula`, `Billing`, I `ApiKey` także `FormRecognizer:ComputerVisionApiKey` i `FormRecognizer:ComputerVisionEndpointUri` opcje muszą być określone w celu uruchomienia kontenera; w przeciwnym razie nie uruchamia się kontener.  Aby uzyskać więcej informacji, zobacz [rozliczeń](#billing).

## <a name="query-the-containers-prediction-endpoint"></a>Zapytanie do endpoint prognoz kontenera

|Kontener|Endpoint|
|--|--|
|aparat rozpoznawania formularza|http://localhost:5000


### <a name="form-recognizer"></a>Rozpoznawanie formularzy

Kontener udostępnia punkt końcowy zapytań w oparciu o websocket, interfejsów API, które są dostępne za pośrednictwem [rozpoznawania formularz usługi zawiera dokumentacja zestawu SDK](https://docs.microsoft.com/azure/cognitive-services/form-recognizer/).

Domyślnie zestaw SDK rozpoznawania formularz korzysta z usług online. Aby korzystać z kontenera, musisz zmienić metodę inicjowania. Zobacz przykłady poniżej.

#### <a name="for-c"></a>For C#

Zmień z przy użyciu tego wywołania inicjowania w chmurze platformy Azure:

```C#
var config = FormRecognizerConfig.FromSubscription("YourSubscriptionKey", "YourServiceRegion");
```

Aby to wywołanie, przy użyciu punktu końcowego kontenera:

```C#
var config = FormRecognizerConfig.FromEndpoint("ws://localhost:5000/formrecognizer/v1.0-preview/custom", "YourSubscriptionKey");
```

#### <a name="for-python"></a>Dla języka Python

Zmiana z przy użyciu tego wywołania inicjowania w chmurze platformy Azure

```python
formrecognizer_config = formrecognizersdk.FormRecognizerConfig(subscription=formrecognizer_key, region=service_region)
```

Aby to wywołanie, przy użyciu punktu końcowego kontenera:

```python
formrecognizer_config = formrecognizersdk.FormRecognizerConfig(subscription=formrecognizer_key, endpoint="ws://localhost:5000/formrecognizer/v1.0-preview/custom"
```

### <a name="form-recognizer"></a>Rozpoznawanie formularzy

Kontener zawiera punkt końcowy REST API, który można znaleźć [tutaj](https://docs.microsoft.com/azure/cognitive-services/formrecognizer-service/rest-apis#formrecognier-api) i przykładów można znaleźć [tutaj](https://azure.microsoft.com/resources/samples/cognitive-formrecognizer).


[!INCLUDE [Validate container is running - Container's API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]


## <a name="stop-the-container"></a>Zastavit kontejner

[!INCLUDE [How to stop the container](../../../includes/cognitive-services-containers-stop.md)]

## <a name="troubleshooting"></a>Rozwiązywanie problemów

Po uruchomieniu kontenera, korzysta z kontenera **stdout** i **stderr** o danych wyjściowych, które są przydatne do rozwiązywania problemów, które wystąpiło podczas uruchamianie kontenera.

## <a name="billing"></a>Rozliczenia

Wyślij kontenerów aparatu rozpoznawania formularza rozliczeń informacje na platformie Azure, przy użyciu _rozpoznawania formularza_ zasobów dla konta systemu Azure.

[!INCLUDE [Container's Billing Settings](../../../includes/cognitive-services-containers-how-to-billing-info.md)]

Aby uzyskać więcej informacji o tych opcjach, zobacz [skonfigurować kontenery](form-recognizer-container-configuration.md).

## <a name="summary"></a>Podsumowanie

W tym artykule przedstawiono pojęcia i przepływ pracy na potrzeby pobierania, instalowania i uruchamiania kontenerów aparatu rozpoznawania formularza. Podsumowanie:

* Aparat rozpoznawania formularza zapewnia jeden kontener systemu Linux dla platformy Docker.
* Obrazy kontenerów są pobierane z prywatnego rejestru kontenerów na platformie Azure.
* Obrazy kontenera Uruchom na platformie Docker.
* Można użyć interfejsu API REST lub zestawu SDK do wywoływania operacji w kontenerze aparatu rozpoznawania formularza, określając host identyfikatora URI kontenera.
* Należy określić informacje rozliczeniowe, podczas tworzenia wystąpienia kontenera.

> [!IMPORTANT]
>  Kontenery usługi cognitive Services nie są licencjonowane do uruchomienia bez połączenia z platformy Azure do zbierania danych. Klienci muszą włączyć kontener, aby komunikować informacje rozliczeniowe usłudze zliczania przez cały czas. Kontenery usługi cognitive Services nie wysyłaj danych klientów (np. obraz lub tekst, który jest analizowana) do firmy Microsoft.

## <a name="next-steps"></a>Kolejne kroki

* Przegląd [skonfigurować kontenery](form-recognizer-container-configuration.md) ustawień konfiguracji
* Użycie [kontenerów usługi Cognitive Services](../cognitive-services-container-support.md)
