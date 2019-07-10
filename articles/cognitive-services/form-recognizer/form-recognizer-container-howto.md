---
title: Jak zainstalować i uruchomić kontenera dla rozpoznawania formularza
titleSuffix: Azure Cognitive Services
description: Dowiedz się, jak analizować dane formularzy i tabel za pomocą kontenera rozpoznawania formularzy.
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 06/19/2019
ms.author: dapine
ms.openlocfilehash: a251e97d671c4aad0aebb1d6c3349cdc09444308
ms.sourcegitcommit: dad277fbcfe0ed532b555298c9d6bc01fcaa94e2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/10/2019
ms.locfileid: "67718471"
---
# <a name="install-and-run-form-recognizer-containers"></a>Instalowanie i uruchamianie kontenerów aparatu rozpoznawania formularza

Azure rozpoznawania formularza ma zastosowanie technologii uczenia maszynowego, aby zidentyfikować i prowadzenie formularzy pary klucz wartość i tabel. Jej kojarzy wartości i wpisy tabeli z pary klucz wartość, a następnie wysyła dane ze strukturą, który zawiera relacje w oryginalnym pliku. 

Aby ograniczyć złożoność i łatwe integrowanie niestandardowy model rozpoznawania formularza proces automatyzacji przepływu pracy lub innych aplikacji, możesz wywołać model przy użyciu prostego interfejsu API REST. Tylko pięć formularzy dokumentów (lub jeden pusty formularz i dwie formy wypełniane) są potrzebne, więc można uzyskać wyniki, szybko, dokładnie i być dostosowane do określonej zawartości. Niezbędne jest nie mocno ręcznej interwencji lub wiele różnych danych do analizy wiedzę. I nie wymaga danych etykietowania lub adnotacji danych.

|Funkcja|Funkcje|
|-|-|
|Rozpoznawanie formularzy| <li>Przetwarza pliki PDF, PNG i JPG<li>Szkolenie modeli niestandardowych modeli za pomocą co najmniej 5 rodzaje taki sam układ <li>Wyodrębnia pary klucz wartość i informacji o tabeli <li>Używa funkcji usługi Azure Cognitive usług komputera przetwarzania tekstu interfejsu API rozpoznaje na wykrywanie i wyodrębnianie tekstu drukowanego z obrazów na formularzach<li>Nie wymaga adnotację lub etykietowania|

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem używania kontenerów aparatu rozpoznającego w formularzu, musi spełniać następujące wymagania wstępne:

|Wymagane|Cel|
|--|--|
|Aparat platformy docker| Aparat platformy Docker zainstalowany na musisz [komputerze-hoście](#the-host-computer). Środowisko docker zawiera pakiety, które konfigurują środowisko platformy Docker na [macOS](https://docs.docker.com/docker-for-mac/), [Windows](https://docs.docker.com/docker-for-windows/), i [Linux](https://docs.docker.com/engine/installation/#supported-platforms). Aby uzyskać podstawowe informacje na temat platformy Docker i kontenerów, zobacz [Docker — omówienie](https://docs.docker.com/engine/docker-overview/).<br><br> Docker należy skonfigurować w taki sposób, aby umożliwić kontenerów, aby nawiązać połączenie z, a następnie wysyłać danych dotyczących rozliczeń do platformy Azure. <br><br> W Windows platformy Docker musi być skonfigurowany do obsługi kontenerów systemu Linux.<br><br>|
|Znajomość platformy Docker | Należy mieć podstawową wiedzę na temat pojęć usługi Docker, takich jak rejestrów, repozytoriów, obrazów kontenerów i kontenery i wiedzę na temat basic `docker` poleceń.|
|Interfejs wiersza polecenia platformy Azure| Zainstaluj [wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) na hoście.|
|Zasób interfejsu API przetwarzania komputera| Aby przetwarzać zeskanowane dokumenty i obrazy, musisz mieć zasobów przetwarzania obrazów. Dostęp do funkcji Rozpoznawanie tekstu jako albo zasób platformy Azure (interfejs API REST lub zestawu SDK) lub *cognitive services — rozpoznawanie tekstu* [kontenera](../Computer-vision/computer-vision-how-to-install-containers.md##get-the-container-image-with-docker-pull). Zwykle opłaty mają zastosowanie. <br><br>Przekaż klucz i rozliczeń punktów końcowych dla zasobu przetwarzania obrazów (w chmurze platformy Azure lub kontener usług Cognitive Services). Użyj tego klucza i rozliczeń punktu końcowego jako {COMPUTER_VISION_API_KEY} i {COMPUTER_VISION_BILLING_ENDPOINT_URI}.<br><br> Jeśli używasz *cognitive services — rozpoznawanie tekstu* kontenera, upewnij się, że:<br><br>Klucz wizualizacji komputerowej dla kontenera aparatu rozpoznawania formularza jest klucz określony w nazwie przetwarzania obrazów `docker run` polecenie, aby uzyskać *cognitive services — rozpoznawanie tekstu* kontenera.<br>Punkt końcowy usługi rozliczeń jest punkt końcowy kontenera (na przykład `https://localhost:5000`). Jeśli używasz zarówno przetwarzania obrazów kontenera, jak i kontener formularz rozpoznawania razem na tym samym hoście, ich obu kończy się przy użyciu domyślnego portu *5000*.  |  
|Formularz rozpoznawania zasobu |Aby użyć tych kontenerów, musisz mieć:<br><br>A _rozpoznawania formularza_ zasobów platformy Azure, aby uzyskać skojarzonego klucza rozliczenia i rozliczeń identyfikator URI punktu końcowego. Obie wartości są dostępne w witrynie Azure portal **Przegląd rozpoznawania formularza** i **klucze Przegląd rozpoznawania formularza** stron i obie wartości są wymagane do uruchomienia kontenera.<br><br>**{BILLING_KEY}** : klucz zasobu<br><br>**{BILLING_ENDPOINT_URI}** : przykład identyfikatora URI punktu końcowego `https://westus.api.cognitive.microsoft.com/forms/v1.0`| 

## <a name="request-access-to-the-container-registry"></a>Żądanie dostępu do rejestru kontenerów

Najpierw należy wypełnić oraz przesłać [Cognitive Services formularza rozpoznawania kontenery dostępu formularz wniosku o](https://aka.ms/FormRecognizerRequestAccess) Aby zażądać dostępu do kontenera. Ten sposób również loguje Cię do przetwarzania obrazów. Nie musisz oddzielnie Zarejestruj się w formularzu żądania przetwarzania obrazów. 

[!INCLUDE [Request access to the container registry](../../../includes/cognitive-services-containers-request-access-only.md)]

[!INCLUDE [Authenticate to the container registry](../../../includes/cognitive-services-containers-access-registry.md)]

## <a name="the-host-computer"></a>Komputer-host

[!INCLUDE [Host Computer requirements](../../../includes/cognitive-services-containers-host-computer.md)]

### <a name="container-requirements-and-recommendations"></a>Kontener wymagania i zalecenia

Minimalne i zalecane rdzeni procesora CPU i pamięci do przydzielenia dla każdego kontenera aparatu rozpoznającego w formularzu zostały opisane w poniższej tabeli:

| Kontener | Minimalne | Zalecane |
|-----------|---------|-------------|
|cognitive-services-form-recognizer | 2 rdzeni, 4 GB pamięci RAM | 4 podstawowe, 8 GB pamięci RAM |

* Każdego rdzenia musi mieć co najmniej 2,6 gigaherc (GHz) lub szybszy.
* TPS - transakcji na sekundę
* Rdzeni i pamięci odpowiadają `--cpus` i `--memory` ustawienia, które są używane jako część `docker run` polecenia.

> [!Note]
> Wartości minimalne i zalecane są oparte na limity platformy Docker i *nie* host machine zasobów.

## <a name="get-the-container-image-with-the-docker-pull-command"></a>Pobierz obraz kontenera przy użyciu polecenia ściągania platformy docker

Obrazy kontenerów dla rozpoznawania formularza są dostępne w następujących repozytorium:

| Kontener | Repozytorium |
|-----------|------------|
| cognitive-services-form-recognizer | `containerpreview.azurecr.io/microsoft/cognitive-services-form-recognizer:latest` |

Jeśli zamierzasz używać `cognitive-services-recognize-text` [kontenera](../Computer-vision/computer-vision-how-to-install-containers.md##get-the-container-image-with-docker-pull), zamiast usługi rozpoznawania formularza, należy upewnić się, `docker pull` polecenia o nazwie poprawny kontener: 

```
docker pull containerpreview.azurecr.io/microsoft/cognitive-services-recognize-text:latest
```

[!INCLUDE [Tip for using docker list](../../../includes/cognitive-services-containers-docker-list-tip.md)]

### <a name="docker-pull-for-the-form-recognizer-container"></a>Polecenie docker pull w kontenerze aparatu rozpoznawania formularza

#### <a name="form-recognizer"></a>Rozpoznawanie formularzy

Aby uzyskać kontenera aparatu rozpoznawania formularza, użyj następującego polecenia:

```Docker
docker pull containerpreview.azurecr.io/microsoft/cognitive-services-form-recognizer:latest
```

## <a name="how-to-use-the-container"></a>Jak używać kontenera

Po włączeniu kontenera [komputerze-hoście](#the-host-computer), użyj następującego procesu do pracy z kontenerem.

1. [Uruchom kontener](#run-the-container-by-using-the-docker-run-command), przy użyciu ustawień rozliczeń wymagany, lecz nie jest używany. Więcej [przykłady](form-recognizer-container-configuration.md#example-docker-run-commands) z `docker run` polecenia są dostępne.
1. [Zapytanie do endpoint prognoz kontenera](#query-the-containers-prediction-endpoint).

## <a name="run-the-container-by-using-the-docker-run-command"></a>Uruchom kontener za pomocą platformy docker, uruchom polecenie

Użyj [platformy docker, uruchom](https://docs.docker.com/engine/reference/commandline/run/) polecenie, aby uruchomić dowolne trzy kontenery. Polecenie używa następujących parametrów:

| Symbol zastępczy | Wartość |
|-------------|-------|
|{BILLING_KEY} | Ten klucz jest używany do uruchomienia kontenera. Jest ona dostępna w witrynie Azure portal **klucze rozpoznawania formularza** strony.  |
|{BILLING_ENDPOINT_URI} | Punkt końcowy rozliczeń wartość identyfikatora URI jest dostępna w witrynie Azure portal **Przegląd rozpoznawania formularza** strony.|
|{COMPUTER_VISION_API_KEY}| Klucz jest dostępny w witrynie Azure portal **klucze interfejsu API przetwarzania komputera** strony.|
|{COMPUTER_VISION_ENDPOINT_URI}|Punkt końcowy rozliczeń. Jeśli używasz zasobów opartych na chmurze przetwarzania obrazów to wartość identyfikatora URI jest dostępna w witrynie Azure portal **omówienie interfejsu API przetwarzania komputera** strony. Jeśli używasz `cognitive-services-recognize-text` kontenera, użyj rozliczeń adresu URL punktu końcowego, który jest przekazywany do kontenera w `docker run` polecenia.|

Zastąp parametry te wartości w poniższym przykładzie `docker run` polecenia.

### <a name="form-recognizer"></a>Rozpoznawanie formularzy

```bash
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

To polecenie:

* Uruchamianie kontenera aparatu rozpoznawania formularz z poziomu obrazu kontenera.
* Przydziela 2 rdzeni Procesora i 8 gigabajtów (GB) pamięci.
* Uwidacznia TCP port 5000 i przydziela pseudo-TTY dla kontenera.
* Automatycznie usuwa kontener po jej zakończenia. Obraz kontenera jest nadal dostępna na komputerze-hoście.
* Instaluje /input i wolumin/OUTPUT do kontenera.

[!INCLUDE [Running multiple containers on the same host H2](../../../includes/cognitive-services-containers-run-multiple-same-host.md)]

### <a name="run-separate-containers-as-separate-docker-run-commands"></a>Uruchamiaj kontenery oddzielne jako osobne platformy docker, Uruchom polecenia

Dla kombinacji rozpoznawania formularza i rozpoznawania tekstu, która jest przechowywana lokalnie na tym samym hoście Użyj następujących poleceń interfejsu wiersza polecenia Docker dwa przykładowe:

Uruchamianie pierwszego kontenera na porcie 5000. 

```bash 
docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 \
--mount type=bind,source=c:\input,target=/input  \
--mount type=bind,source=c:\output,target=/output \
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

Aby uzyskać kombinacja rozpoznawania formularza i rozpoznawania tekstu, która jest przechowywana lokalnie na tym samym hoście zobacz poniższy przykładowy plik Docker Compose YAML. Aparat rozpoznawania tekstu `{COMPUTER_VISION_API_KEY}` muszą być takie same dla obu `formrecognizer` i `ocr` kontenerów. `{COMPUTER_VISION_ENDPOINT_URI}` Jest używana tylko w `ocr` kontenera, ponieważ `formrecognizer` kontenera używa `ocr` nazwy i portu. 

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
> `Eula`, `Billing`, I `ApiKey`, jak również `FormRecognizer:ComputerVisionApiKey` i `FormRecognizer:ComputerVisionEndpointUri` opcji, należy określić w celu uruchomienia kontenera; w przeciwnym razie nie uruchamia się kontener. Aby uzyskać więcej informacji, zobacz [rozliczeń](#billing).

## <a name="query-the-containers-prediction-endpoint"></a>Zapytanie do endpoint prognoz kontenera

|Kontener|Endpoint|
|--|--|
|aparat rozpoznawania formularza|http://localhost:5000


### <a name="form-recognizer"></a>Rozpoznawanie formularzy

Kontener udostępnia punkt końcowy zapytań w oparciu o websocket, interfejsów API, którego dostęp można uzyskać za pośrednictwem [rozpoznawania formularz usługi zawiera dokumentacja zestawu SDK](https://docs.microsoft.com/azure/cognitive-services/form-recognizer/).

Domyślnie zestaw SDK rozpoznawania formularz korzysta z usług online. Aby korzystać z kontenera, musisz zmienić metodę inicjowania. Zobacz przykłady poniżej.

#### <a name="for-c"></a>For C#

Zmień z przy użyciu tego wywołania inicjowania w chmurze platformy Azure:

```C#
var config = FormRecognizerConfig.FromSubscription("YourSubscriptionKey", "YourServiceRegion");
```

Aby to wywołanie, którego używa punkt końcowy kontenera:

```C#
var config = FormRecognizerConfig.FromEndpoint("ws://localhost:5000/formrecognizer/v1.0-preview/custom", "YourSubscriptionKey");
```

#### <a name="for-python"></a>Dla języka Python

Zmień z przy użyciu tego wywołania inicjowania w chmurze platformy Azure:

```python
formrecognizer_config = formrecognizersdk.FormRecognizerConfig(subscription=formrecognizer_key, region=service_region)
```

Aby to wywołanie, którego używa punkt końcowy kontenera:

```python
formrecognizer_config = formrecognizersdk.FormRecognizerConfig(subscription=formrecognizer_key, endpoint="ws://localhost:5000/formrecognizer/v1.0-preview/custom"
```

### <a name="form-recognizer"></a>Rozpoznawanie formularzy

Kontener zawiera punkt końcowy REST API, który można znaleźć na [interfejsu API rozpoznawania fragmentów](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api/operations/AnalyzeWithCustomModel) strony.


[!INCLUDE [Validate container is running - Container's API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]


## <a name="stop-the-container"></a>Zastavit kontejner

[!INCLUDE [How to stop the container](../../../includes/cognitive-services-containers-stop.md)]

## <a name="troubleshooting"></a>Rozwiązywanie problemów

Po uruchomieniu kontenera, korzysta z kontenera **stdout** i **stderr** o danych wyjściowych, które są przydatne do rozwiązywania problemów, które powstają, gdy uruchomiony kontener.

## <a name="billing"></a>Rozliczenia

Kontenery aparatu rozpoznawania formularza wysłać informacje rozliczeniowe na platformie Azure przy użyciu _rozpoznawania formularza_ zasobów dla konta systemu Azure.

[!INCLUDE [Container's Billing Settings](../../../includes/cognitive-services-containers-how-to-billing-info.md)]

Aby uzyskać więcej informacji o tych opcjach, zobacz [skonfigurować kontenery](form-recognizer-container-configuration.md).

<!--blogs/samples/video courses -->

[!INCLUDE [Discoverability of more container information](../../../includes/cognitive-services-containers-discoverability.md)]

## <a name="summary"></a>Podsumowanie

W tym artykule przedstawiono pojęcia i przepływ pracy na potrzeby pobierania, instalowania i uruchamiania kontenerów aparatu rozpoznawania formularza. Podsumowanie:

* Aparat rozpoznawania formularza zapewnia jeden kontener systemu Linux dla platformy Docker.
* Obrazy kontenerów są pobierane z prywatnego rejestru kontenerów na platformie Azure.
* Obrazy kontenera Uruchom na platformie Docker.
* Interfejs API REST lub zestawu SDK REST umożliwia wywoływanie operacji w kontenerze aparatu rozpoznawania formularza, określając host identyfikatora URI kontenera.
* Należy określić informacje rozliczeniowe, podczas tworzenia wystąpienia kontenera.

> [!IMPORTANT]
>  Kontenery usługi cognitive Services nie są licencjonowane do uruchomienia bez połączenia z platformy Azure do zbierania danych. Klienci muszą włączyć kontener, aby komunikować informacje rozliczeniowe usłudze zliczania przez cały czas. Kontenery usługi cognitive Services nie wysyłaj danych klienta (na przykład obraz lub tekst, który jest analizowana) do firmy Microsoft.

## <a name="next-steps"></a>Następne kroki

* Przegląd [skonfigurować kontenery](form-recognizer-container-configuration.md) ustawień konfiguracji.
* Użycie [kontenerów usługi Cognitive Services](../cognitive-services-container-support.md).
