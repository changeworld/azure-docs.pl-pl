---
title: Jak zainstalować i uruchomić kontener dla aparatu rozpoznawania formularzy
titleSuffix: Azure Cognitive Services
description: W tym artykule wyjaśniono, jak używać kontenera rozpoznawania formularzy usług Wirtualnych usług Azure do analizowania danych formularza i tabeli.
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: 8cfa9114c5a5e57882cb84b604c1cf71be9acc52
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/08/2020
ms.locfileid: "80878344"
---
# <a name="install-and-run-form-recognizer-containers-preview"></a>Instalowanie i uruchamianie kontenerów aparatów rozpoznawania formularzy (wersja zapoznawcza)

Usługa Azure Form Recognizer stosuje technologię uczenia maszynowego w celu identyfikowania i wyodrębniania par kluczy i tabel z formularzy. Kojarzy wartości i wpisy tabeli z parami klucz-wartość, a następnie wyprowadza dane strukturalne, które zawierają relacje w oryginalnym pliku. 

Aby zmniejszyć złożoność i łatwo zintegrować niestandardowy model rozpoznawania formularzy z procesem automatyzacji przepływu pracy lub inną aplikacją, można wywołać model przy użyciu prostego interfejsu API REST. Potrzebne jest tylko pięć dokumentów formularza (lub jeden pusty formularz i dwa wypełnione formularze), dzięki czemu można szybko, dokładnie i dostosować je do określonych treści. Nie jest konieczna ciężka ręczna interwencja ani obszerna wiedza na temat nauki o danych. I nie wymaga etykietowania danych ani adnotacji danych.

> [!IMPORTANT]
> Kontenery aparat rozpoznawania formularzy obecnie używają wersji 1.0 interfejsu API aparatu rozpoznawania formularzy. Dostęp do najnowszej wersji interfejsu API można uzyskać za pomocą usługi zarządzanej.

| Funkcja | Funkcje |
|----------|----------|
| Rozpoznawanie formularzy | <li>Przetwarza pliki PDF, PNG i JPG<li>Trenuje modele niestandardowe z co najmniej pięcioma formami o tym samym układzie <li>Wyodrębnia pary klucz-wartość i informacje o tabeli <li>Używa funkcji Rozpoznawania tekstu tekstowej interfejsu API interfejsu API usługi Azure Cognitive Services do wykrywania i wyodrębniania drukowanego tekstu z obrazów wewnątrz formularzy<li>Nie wymaga adnotacji ani etykietowania |

Jeśli nie masz subskrypcji platformy Azure, utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) przed rozpoczęciem.

## <a name="prerequisites"></a>Wymagania wstępne

Przed użyciem kontenerów rozpoznawania formularzy należy spełnić następujące wymagania wstępne:

| Wymagany | Przeznaczenie |
|----------|---------|
| Aparat platformy Docker | Aparat platformy Docker jest zainstalowany na [komputerze-hoście.](#the-host-computer) Platforma Docker udostępnia pakiety, które konfigurują środowisko platformy Docker w systemach [macOS](https://docs.docker.com/docker-for-mac/), [Windows](https://docs.docker.com/docker-for-windows/) i [Linux](https://docs.docker.com/engine/installation/#supported-platforms). Aby uzyskać podstawowe informacje na temat platformy Docker i kontenerów, zapoznaj się z artykułem [Docker overview](https://docs.docker.com/engine/docker-overview/) (Przegląd platformy Docker).<br><br> Platforma Docker musi być skonfigurowana tak, aby zezwalała kontenerom na łączenie się z danymi rozliczeń i wysyłanie ich na platformę Azure. <br><br> W systemie Windows docker musi być również skonfigurowany do obsługi kontenerów systemu Linux.<br><br> |
| Znajomość platformy Docker | Powinien mieć podstawową wiedzę pojęcia platformy Docker, takie jak rejestry, repozytoria, kontenery `docker` i obrazy kontenerów oraz znajomość podstawowych poleceń. |
| Interfejs wiersza polecenia platformy Azure | Zainstaluj [interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) na hoście. |
| Zasób interfejsu API przetwarzania obrazów komputerowych | Do przetwarzania zeskanowanych dokumentów i obrazów potrzebny jest zasób przetwarzania obrazów. Dostęp do funkcji Rozpoznawanie tekstu można uzyskać jako zasób platformy Azure (interfejs API REST lub zestaw SDK) lub [kontener](../Computer-vision/computer-vision-how-to-install-containers.md#get-the-container-image-with-docker-pull) *cognitive-services-recognize-text* . Obowiązują zwykłe opłaty rozliczeniowe. <br><br>Przekaż zarówno klucz interfejsu API, jak i punkty końcowe dla zasobu przetwarzania (chmura Azure lub kontener usług Cognitive Services). Użyj tego klucza interfejsu API i punktu końcowego jako **{COMPUTER_VISION_API_KEY}** i **{COMPUTER_VISION_ENDPOINT_URI}**.<br><br> Jeśli używasz kontenera *cognitive-services-recognize-text,* upewnij się, że:<br><br>Twój klucz przetwarzania dla kontenera rozpoznawania formularzy jest `docker run` kluczem określonym w poleceniu Wizja komputera dla *kontenera cognitive-services-recognize-text.*<br>Punkt końcowy rozliczeń jest punktem końcowym kontenera `http://localhost:5000`(na przykład). Jeśli na tym samym hoście używasz kontenera przetwarzania i kontenera rozpoznawania formularzy, nie można ich uruchomić z domyślnym portem *5000*. |
| Zasób aparatu rozpoznawania formularzy | Aby korzystać z tych kontenerów, musisz mieć:<br><br>Zasób **aparatu rozpoznawania formularzy** platformy Azure, aby uzyskać skojarzony klucz interfejsu API i identyfikator URI punktu końcowego. Obie wartości są dostępne na stronach Azure Portal **Form Aparat rozpoznawania** i Klucze, a obie wartości są wymagane do uruchomienia kontenera.<br><br>**{FORM_RECOGNIZER_API_KEY}**: Jeden z dwóch dostępnych kluczy zasobów na stronie Klucze<br><br>**{FORM_RECOGNIZER_ENDPOINT_URI}**: Punkt końcowy podany na stronie Przegląd |

> [!NOTE]
> Nazwa zasobu Przetwarzania wizjuszu powinna `-` być pojedynczym wyrazem, bez łącznika lub innych znaków specjalnych. To ograniczenie jest w celu zapewnienia rozpoznawania formularzy i rozpoznawania zgodności kontenera tekstu.

## <a name="gathering-required-parameters"></a>Zbieranie wymaganych parametrów

Istnieją trzy podstawowe parametry dla wszystkich kontenerów usług Cognitive Services, które są wymagane. Umowa licencyjna użytkownika końcowego `accept`(EULA) musi mieć wartość . Ponadto potrzebny jest adres URL punktu końcowego i klucz interfejsu API.

### <a name="endpoint-uri-computer_vision_endpoint_uri-and-form_recognizer_endpoint_uri"></a>Identyfikator URI `{COMPUTER_VISION_ENDPOINT_URI}` punktu końcowego i`{FORM_RECOGNIZER_ENDPOINT_URI}`

Wartość identyfikatora URI **punktu końcowego** jest dostępna na stronie *Przegląd* portalu Azure odpowiedniego zasobu usługi Cognitive Service. Przejdź do strony *Przegląd,* umieść wskaźnik myszy `Copy to clipboard` <span class="docon docon-edit-copy x-hidden-focus"></span> na punkcie końcowym i pojawi się ikona. W razie potrzeby skopiuj i używaj.

![Zbierz punkt końcowy uri do późniejszego użycia](../containers/media/overview-endpoint-uri.png)

### <a name="keys-computer_vision_api_key-and-form_recognizer_api_key"></a>Klawisze `{COMPUTER_VISION_API_KEY}` i`{FORM_RECOGNIZER_API_KEY}`

Ten klucz jest używany do uruchamiania kontenera i jest dostępny na stronie Klucze portalu Azure odpowiedniego zasobu usługi Cognitive Service. Przejdź do strony *Klawisze* i `Copy to clipboard` <span class="docon docon-edit-copy x-hidden-focus"></span> kliknij ikonę.

![Pobierz jeden z dwóch kluczy do późniejszego użycia](../containers/media/keys-copy-api-key.png)

> [!IMPORTANT]
> Te klucze subskrypcji są używane do uzyskiwania dostępu do interfejsu API usługi Cognitive Service. Nie udostępniaj kluczy. Przechowuj je bezpiecznie, na przykład przy użyciu usługi Azure Key Vault. Zalecamy również regularne regenerowanie tych kluczy. Tylko jeden klucz jest niezbędny do wywołania interfejsu API. Podczas ponownego generowania pierwszego klucza, można użyć drugiego klucza do dalszego dostępu do usługi.

## <a name="request-access-to-the-container-registry"></a>Żądanie dostępu do rejestru kontenerów

Aby zażądać dostępu do kontenera, należy najpierw wypełnić i przesłać [formularz żądania dostępu kontenerów formularzy usług Cognitive Services,](https://aka.ms/FormRecognizerContainerRequestAccess) aby zażądać dostępu do kontenera. W ten sposób również rejestruje się na Wizji Komputerowej. Nie musisz osobno zapisywać się na formularz żądania wizji komputerowej. 

[!INCLUDE [Request access to the container registry](../../../includes/cognitive-services-containers-request-access-only.md)]

[!INCLUDE [Authenticate to the container registry](../../../includes/cognitive-services-containers-access-registry.md)]

## <a name="the-host-computer"></a>Komputer-host

[!INCLUDE [Host Computer requirements](../../../includes/cognitive-services-containers-host-computer.md)]

### <a name="container-requirements-and-recommendations"></a>Wymagania i zalecenia dotyczące kontenerów

Minimalne i zalecane rdzenie procesora CPU i pamięć do przydzielenia dla każdego kontenera rozpoznawania formularzy są opisane w poniższej tabeli:

| Kontener | Minimalne | Zalecane |
|-----------|---------|-------------|
| Rozpoznawanie formularzy | 2-rdzeniowa pamięć 4 GB | 4-rdzeniowa pamięć 8 GB |
| Rozpoznawanie tekstu | 1 rdzeń, 8 GB pamięci | 2 rdzenie, pamięć 8 GB |

* Każdy rdzeń musi mieć co najmniej 2,6 gigaherca (GHz) lub więcej.
* Rdzeń i pamięć `--cpus` `--memory` odpowiadają i ustawienia, które `docker run` są używane jako część polecenia.

> [!Note]
> Wartości minimalne i zalecane są oparte na limitach platformy Docker, a *nie* na zasobach komputera hosta.

## <a name="get-the-container-images-with-the-docker-pull-command"></a>Pobierz obrazy kontenerów za pomocą polecenia ściągania docker

Obrazy kontenerów zarówno dla **aparatu rozpoznawania formularzy,** jak i dla oferty **rozpoznawania tekstu** są dostępne w następującym rejestrze kontenerów:

| Kontener | W pełni kwalifikowana nazwa obrazu |
|-----------|------------|
| Rozpoznawanie formularzy | `containerpreview.azurecr.io/microsoft/cognitive-services-form-recognizer:latest` |
| Rozpoznawanie tekstu | `containerpreview.azurecr.io/microsoft/cognitive-services-recognize-text:latest` |

Będziesz potrzebować obu kontenerów, należy pamiętać, że aparat **rozpoznawania tekstu** kontenera jest szczegółowo poza [tym artykule.](../Computer-vision/computer-vision-how-to-install-containers.md#get-the-container-image-with-docker-pull)

[!INCLUDE [Tip for using docker list](../../../includes/cognitive-services-containers-docker-list-tip.md)]

### <a name="docker-pull-for-the-form-recognizer-container"></a>Ściąganie platformy Docker dla kontenera aparatu rozpoznawania formularzy

#### <a name="form-recognizer"></a>Rozpoznawanie formularzy

Aby uzyskać kontener rozpoznawania formularzy, użyj następującego polecenia:

```Docker
docker pull containerpreview.azurecr.io/microsoft/cognitive-services-form-recognizer:latest
```
### <a name="docker-pull-for-the-recognize-text-container"></a>Ściąganie platformy Docker dla kontenera Rozpoznawanie tekstu

#### <a name="recognize-text"></a>Rozpoznawanie tekstu

Aby uzyskać kontener Rozpoznawanie tekstu, użyj następującego polecenia:

```Docker
docker pull containerpreview.azurecr.io/microsoft/cognitive-services-recognize-text:latest
```

## <a name="how-to-use-the-container"></a>Jak korzystać z pojemnika

Po kontener znajduje się na [komputerze-hoście](#the-host-computer), użyj następującego procesu do pracy z kontenerem.

1. [Uruchom kontener](#run-the-container-by-using-the-docker-run-command)z wymaganymi ustawieniami rozliczeń. Dostępnych jest więcej `docker run` [przykładów](form-recognizer-container-configuration.md#example-docker-run-commands) polecenia.
1. [Kwerenda punktu końcowego przewidywania kontenera](#query-the-containers-prediction-endpoint).

## <a name="run-the-container-by-using-the-docker-run-command"></a>Uruchamianie kontenera przy użyciu polecenia uruchom docker

Użyj polecenia [uruchamiania platformy docker,](https://docs.docker.com/engine/reference/commandline/run/) aby uruchomić kontener. Szczegółowe informacje na temat sposobu uzyskania wartości `{COMPUTER_VISION_ENDPOINT_URI}` `{COMPUTER_VISION_API_KEY}` `{FORM_RECOGNIZER_ENDPOINT_URI}` i `{FORM_RECOGNIZER_API_KEY}` wartości można znaleźć w przypadku [zbierania wymaganych parametrów.](#gathering-required-parameters)

[Dostępne](form-recognizer-container-configuration.md#example-docker-run-commands) są `docker run` przykłady polecenia.

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

* Uruchamia kontener rozpoznawania formularzy z obrazu kontenera.
* Przydziela 2 rdzenie procesora i 8 gigabajtów (GB) pamięci.
* Udostępnia port TCP 5000 i przydziela pseudo-TTY dla kontenera.
* Automatycznie usuwa kontener po jego zamknięciu. Obraz kontenera jest nadal dostępny na komputerze-hoście.
* Montuje /input i /output volume do kontenera.

[!INCLUDE [Running multiple containers on the same host H2](../../../includes/cognitive-services-containers-run-multiple-same-host.md)]

### <a name="run-separate-containers-as-separate-docker-run-commands"></a>Uruchamianie oddzielnych kontenerów jako oddzielnych poleceń uruchamiania platformy dokowane

W przypadku kombinacji aparat rozpoznawania formularzy i aparat rozpoznawania tekstu, która jest hostowana lokalnie na tym samym hoście, należy użyć następujących dwóch przykładowych poleceń interfejsu wiersza polecenia platformy Docker:

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

### <a name="run-separate-containers-with-docker-compose"></a>Uruchamianie oddzielnych kontenerów za pomocą dokceny compose

Dla aparatu rozpoznawania formularzy i aparat rozpoznawania tekstu, który jest obsługiwany lokalnie na tym samym hoście, zobacz następujący przykład Docker Compose yaml pliku. Aparat rozpoznawania `{COMPUTER_VISION_API_KEY}` tekstu musi być taka `formrecognizer` `ocr` sama dla kontenerów i kontenerów. Jest `{COMPUTER_VISION_ENDPOINT_URI}` używany tylko `ocr` w kontenerze, ponieważ `formrecognizer` `ocr` kontener używa nazwy i portu. 

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
> `Billing`Aby `Eula`uruchomić `ApiKey`kontener, należy `FormRecognizer:ComputerVisionApiKey` określić , oraz opcje i `FormRecognizer:ComputerVisionEndpointUri` opcje; w przeciwnym razie kontener nie zostanie uruchomiony. Aby uzyskać więcej informacji, zobacz [Rozliczenia](#billing).

## <a name="query-the-containers-prediction-endpoint"></a>Kwerenda punktu końcowego przewidywania kontenera

|Kontener|Endpoint|
|--|--|
|aparat rozpoznawania formularzy|http://localhost:5000

### <a name="form-recognizer"></a>Rozpoznawanie formularzy

Kontener udostępnia interfejsy API punktu końcowego programu query oparte na websocket, do których uzyskujesz dostęp za pośrednictwem [dokumentacji SDK usług rozpoznawania formularzy.](https://docs.microsoft.com/azure/cognitive-services/form-recognizer/)

Domyślnie SDK rozpoznawania formularzy korzysta z usług online. Aby użyć kontenera, należy zmienić metodę inicjowania. Zobacz poniższe przykłady.

#### <a name="for-c"></a>Dla C #

Zmień z przy użyciu tego wywołania inicjowania chmury platformy Azure:

```csharp
var config =
    FormRecognizerConfig.FromSubscription(
        "YourSubscriptionKey",
        "YourServiceRegion");
```
do tego wywołania, który używa punktu końcowego kontenera:

```csharp
var config =
    FormRecognizerConfig.FromEndpoint(
        "ws://localhost:5000/formrecognizer/v1.0-preview/custom",
        "YourSubscriptionKey");
```

#### <a name="for-python"></a>Dla Pythona

Zmień z przy użyciu tego wywołania inicjowania chmury platformy Azure:

```python
formrecognizer_config =
    formrecognizersdk.FormRecognizerConfig(
        subscription=formrecognizer_key, region=service_region)
```

do tego wywołania, który używa punktu końcowego kontenera:

```python
formrecognizer_config = 
    formrecognizersdk.FormRecognizerConfig(
        subscription=formrecognizer_key,
        endpoint="ws://localhost:5000/formrecognizer/v1.0-preview/custom"
```

### <a name="form-recognizer"></a>Rozpoznawanie formularzy

Kontener udostępnia interfejsy API punktu końcowego REST, które można znaleźć na stronie [interfejsu API aparatu rozpoznawania formularzy.](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api/operations/AnalyzeWithCustomModel)


[!INCLUDE [Validate container is running - Container's API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]


## <a name="stop-the-container"></a>Zatrzymywanie kontenera

[!INCLUDE [How to stop the container](../../../includes/cognitive-services-containers-stop.md)]

## <a name="troubleshooting"></a>Rozwiązywanie problemów

Jeśli uruchomisz kontener z [włączoną instalacją](form-recognizer-container-configuration.md#mount-settings) wyjściową i rejestrowaniem, kontener generuje pliki dziennika, które są przydatne do rozwiązywania problemów, które występują podczas uruchamiania lub uruchamiania kontenera.

[!INCLUDE [Cognitive Services FAQ note](../containers/includes/cognitive-services-faq-note.md)]

## <a name="billing"></a>Rozliczenia

Kontenery aparatu rozpoznawania formularzy wysyłają informacje rozliczeniowe do platformy Azure przy użyciu zasobu _rozpoznawania formularzy_ na koncie platformy Azure.

[!INCLUDE [Container's Billing Settings](../../../includes/cognitive-services-containers-how-to-billing-info.md)]

Aby uzyskać więcej informacji na temat tych opcji, zobacz [Konfigurowanie kontenerów](form-recognizer-container-configuration.md).

<!--blogs/samples/video courses -->

[!INCLUDE [Discoverability of more container information](../../../includes/cognitive-services-containers-discoverability.md)]

## <a name="summary"></a>Podsumowanie

W tym artykule poznaliście pojęcia i przepływ pracy do pobierania, instalowania i uruchamiania kontenerów rozpoznawania formularzy. Podsumowanie:

* Aparat rozpoznawania formularzy udostępnia jeden kontener systemu Linux dla platformy Docker.
* Obrazy kontenerów są pobierane z rejestru kontenerów prywatnych na platformie Azure.
* Obrazy kontenerów są uruchamiane w usłudze Docker.
* Za pomocą interfejsu API REST lub rest SDK można wywołać operacje w kontenerze rozpoznawania formularzy, określając identyfikator URI hosta kontenera.
* Podczas tworzenia wystąpienia kontenera należy określić informacje rozliczeniowe.

> [!IMPORTANT]
>  Kontenery usług Cognitive Services nie są licencjonowane do uruchamiania bez połączenia z platformą Azure w celu pomiaru. Klienci muszą włączyć kontenery do przekazywania informacji rozliczeniowych z usługą pomiaru przez cały czas. Kontenery usług Cognitive Services nie wysyłają danych klientów (na przykład obrazu lub tekstu, który jest analizowany) do firmy Microsoft.

## <a name="next-steps"></a>Następne kroki

* Przejrzyj [Konfigurowanie kontenerów](form-recognizer-container-configuration.md) pod kątem ustawień konfiguracji.
* Użyj większej liczby [kontenerów usług Cognitive Services](../cognitive-services-container-support.md).
