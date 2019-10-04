---
title: Jak instalować i uruchamiać kontenery — przetwarzanie obrazów
titleSuffix: Azure Cognitive Services
description: Jak pobrać, zainstalować i uruchomić kontenery dla przetwarzanie obrazów w tym samouczku instruktażu.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 10/03/2019
ms.author: dapine
ms.custom: seodec18
ms.openlocfilehash: 7c137572fadd07254343b7b4c34b5a63534b9d88
ms.sourcegitcommit: f2d9d5133ec616857fb5adfb223df01ff0c96d0a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/03/2019
ms.locfileid: "71936999"
---
# <a name="install-and-run-computer-vision-containers"></a>Instalowanie i uruchamianie kontenerów przetwarzanie obrazów

Kontenery umożliwiają uruchamianie przetwarzanie obrazów interfejsów API we własnym środowisku. Kontenery doskonale nadaje się do określonych wymagań w zakresie zabezpieczeń i zarządzania danymi. W tym artykule dowiesz się, jak pobrać, zainstalować i uruchomić kontener przetwarzanie obrazów.

Istnieją dwa kontenery Docker dostępne dla przetwarzanie obrazów: *rozpoznawanie tekstu* i *Read*. Kontener *rozpoznawanie tekstu* umożliwia wykrywanie i wyodrębnianie *drukowanych tekstów* z obrazów różnych obiektów z różnymi powierzchniami i tłem, takimi jak paragony, plakaty i karty biznesowe. Kontener *odczytu* ; wykrywa także *Tekst odręczny* w obrazach i zapewnia obsługę formatu PDF/TIFF/wielostronicowej. Aby uzyskać więcej informacji, zobacz dokumentację [interfejsu API odczytu](concept-recognizing-text.md#read-api) .

> [!IMPORTANT]
> Kontener Rozpoznawanie tekstu jest przestarzały na rzecz kontenera odczytu. Kontener odczytu jest nadzbiorem Rozpoznawanie tekstu kontenera, a konsumenci powinni migrować do korzystania z kontenera Read. Oba kontenery działają tylko w języku angielskim.

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="prerequisites"></a>Wymagania wstępne

Przed użyciem kontenerów należy spełnić następujące wymagania wstępne:

|Wymagane|Przeznaczenie|
|--|--|
|Aparat platformy Docker| Aparat platformy Docker musi być zainstalowany na [komputerze-hoście](#the-host-computer). Platforma Docker zawiera pakiety, które konfigurują środowisko platformy Docker w systemach [macOS](https://docs.docker.com/docker-for-mac/), [Windows](https://docs.docker.com/docker-for-windows/)i [Linux](https://docs.docker.com/engine/installation/#supported-platforms). Podstawowe informacje dotyczące platformy Docker i kontenera można znaleźć w temacie [Omówienie platformy Docker](https://docs.docker.com/engine/docker-overview/).<br><br> Program Docker musi być skonfigurowany tak, aby umożliwić kontenerom łączenie się z danymi rozliczeń i wysyłanie ich do platformy Azure. <br><br> **W systemie Windows**program Docker musi być również skonfigurowany do obsługi kontenerów systemu Linux.<br><br>|
|Znajomość platformy Docker | Należy dysponować podstawową wiedzą na temat pojęć platformy Docker, takich jak rejestry, repozytoria, kontenery i obrazy kontenerów, a także znajomość podstawowych poleceń `docker`.| 
|Zasób przetwarzanie obrazów |Aby można było używać kontenera, musisz mieć:<br><br>Zasób usługi Azure **Przetwarzanie obrazów** i skojarzony klucz interfejsu API dla identyfikatora URI punktu końcowego. Obie wartości są dostępne na stronach przeglądów i kluczy dla zasobu i są wymagane do uruchomienia kontenera.<br><br>**{API_KEY}** : jeden z dwóch dostępnych kluczy zasobów na stronie **kluczy**<br><br>**{ENDPOINT_URI}** : punkt końcowy określony na stronie **Przegląd**|

[!INCLUDE [Gathering required container parameters](../containers/includes/container-gathering-required-parameters.md)]

## <a name="request-access-to-the-private-container-registry"></a>Zażądaj dostępu do rejestru kontenera prywatnego

[!INCLUDE [Request access to public preview](../../../includes/cognitive-services-containers-request-access.md)]

### <a name="the-host-computer"></a>Komputer-host

[!INCLUDE [Host Computer requirements](../../../includes/cognitive-services-containers-host-computer.md)]

### <a name="container-requirements-and-recommendations"></a>Wymagania i zalecenia dotyczące kontenera

[!INCLUDE [Container requirements and recommendations](includes/container-requirements-and-recommendations.md)]

## <a name="get-the-container-image-with-docker-pull"></a>Pobierz obraz kontenera z `docker pull`

# <a name="readtabread"></a>[Odczyt](#tab/read)

Dostępne są obrazy kontenerów do odczytu.

| Kontener | Container Registry/repozytorium/nazwa obrazu |
|-----------|------------|
| Odczyt | `containerpreview.azurecr.io/microsoft/cognitive-services-read:latest` |

# <a name="recognize-texttabrecognize-text"></a>[Rozpoznawanie tekstu](#tab/recognize-text)

Dostępne są obrazy kontenerów dla Rozpoznawanie tekstu.

| Kontener | Container Registry/repozytorium/nazwa obrazu |
|-----------|------------|
| Rozpoznawanie tekstu | `containerpreview.azurecr.io/microsoft/cognitive-services-recognize-text:latest` |

***

Użyj polecenia [`docker pull`](https://docs.docker.com/engine/reference/commandline/pull/) , aby pobrać obraz kontenera.

# <a name="readtabread"></a>[Odczyt](#tab/read)

### <a name="docker-pull-for-the-read-container"></a>Wypychanie platformy Docker dla kontenera odczytu

```bash
docker pull containerpreview.azurecr.io/microsoft/cognitive-services-read:latest
```

# <a name="recognize-texttabrecognize-text"></a>[Rozpoznawanie tekstu](#tab/recognize-text)

### <a name="docker-pull-for-the-recognize-text-container"></a>Wypychanie platformy Docker dla kontenera Rozpoznawanie tekstu

```bash
docker pull containerpreview.azurecr.io/microsoft/cognitive-services-recognize-text:latest
```

***

[!INCLUDE [Tip for using docker list](../../../includes/cognitive-services-containers-docker-list-tip.md)]

## <a name="how-to-use-the-container"></a>Jak używać kontenera

Gdy kontener znajduje się na [komputerze hosta](#the-host-computer), użyj następującego procesu, aby współpracować z kontenerem.

1. [Uruchom kontener](#run-the-container-with-docker-run)z wymaganymi ustawieniami rozliczania. Więcej [przykładów](computer-vision-resource-container-config.md) polecenia `docker run` jest dostępnych. 
1. [Zbadaj punkt końcowy przewidywania kontenera](#query-the-containers-prediction-endpoint). 

## <a name="run-the-container-with-docker-run"></a>Uruchom kontener z `docker run`

Użyj polecenia [Docker Run](https://docs.docker.com/engine/reference/commandline/run/) , aby uruchomić kontener. Zapoznaj się z tematem [zbieranie wymaganych parametrów](#gathering-required-parameters) , aby uzyskać szczegółowe informacje na temat pobierania wartości `{ENDPOINT_URI}` i `{API_KEY}`.

[Przykłady](computer-vision-resource-container-config.md#example-docker-run-commands) `docker run` polecenia są dostępne.

# <a name="readtabread"></a>[Odczyt](#tab/read)

```bash
docker run --rm -it -p 5000:5000 --memory 16g --cpus 8 \
containerpreview.azurecr.io/microsoft/cognitive-services-read \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

To polecenie:

* Uruchamia kontener odczytu z obrazu kontenera.
* Przypisuje 8 rdzeni procesora i 16 gigabajtów (GB) pamięci.
* Udostępnia port TCP 5000 i przydziela pseudo-TTY dla kontenera.
* Automatycznie usuwa kontener po zakończeniu. Obraz kontenera jest nadal dostępny na komputerze-hoście.

# <a name="recognize-texttabrecognize-text"></a>[Rozpoznawanie tekstu](#tab/recognize-text)

```bash
docker run --rm -it -p 5000:5000 --memory 16g --cpus 8 \
containerpreview.azurecr.io/microsoft/cognitive-services-recognize-text \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

To polecenie:

* Uruchamia kontener Rozpoznawanie tekstu z obrazu kontenera.
* Przypisuje 8 rdzeni procesora i 16 gigabajtów (GB) pamięci.
* Udostępnia port TCP 5000 i przydziela pseudo-TTY dla kontenera.
* Automatycznie usuwa kontener po zakończeniu. Obraz kontenera jest nadal dostępny na komputerze-hoście.

***

Więcej [przykładów](./computer-vision-resource-container-config.md#example-docker-run-commands) polecenia `docker run` jest dostępnych. 

> [!IMPORTANT]
> Aby można było uruchomić kontener, należy określić opcje `Eula`, `Billing` i `ApiKey`. w przeciwnym razie kontener nie zostanie uruchomiony.  Aby uzyskać więcej informacji, zobacz [rozliczenia](#billing).

[!INCLUDE [Running multiple containers on the same host](../../../includes/cognitive-services-containers-run-multiple-same-host.md)]

<!--  ## Validate container is running -->

[!INCLUDE [Container's API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]

## <a name="query-the-containers-prediction-endpoint"></a>Zbadaj punkt końcowy przewidywania kontenera

Kontener udostępnia interfejsy API punktu końcowego przewidywania zapytań. 

Użyj hosta `http://localhost:5000` w przypadku interfejsów API kontenerów.

# <a name="readtabread"></a>[Odczyt](#tab/read)

### <a name="asynchronous-read"></a>Odczyt asynchroniczny

Możesz użyć operacji `POST /vision/v2.0/read/core/asyncBatchAnalyze` i `GET /vision/v2.0/read/operations/{operationId}` w połączeniu z asynchroniczne odczytywanie obrazu, podobnie jak usługa przetwarzanie obrazów używa tych odpowiednich operacji REST. Asynchroniczna Metoda POST zwróci `operationId`, który jest używany jako identyfikator do żądania HTTP GET.

W interfejsie użytkownika programu Swagger wybierz `asyncBatchAnalyze`, aby rozwinąć go w przeglądarce. Następnie wybierz pozycję **wypróbuj** > **Wybierz plik**. W tym przykładzie użyjemy poniższej ilustracji:

![karty vs Spaces](media/tabs-vs-spaces.png)

Po pomyślnym uruchomieniu asynchronicznego wpisu zwraca kod stanu **HTTP 202** . W ramach odpowiedzi istnieje nagłówek `operation-location`, który zawiera punkt końcowy wyniku żądania.

```http
 content-length: 0
 date: Fri, 13 Sep 2019 16:23:01 GMT
 operation-location: http://localhost:5000/vision/v2.0/read/operations/a527d445-8a74-4482-8cb3-c98a65ec7ef9
 server: Kestrel
```

@No__t-0 to w pełni kwalifikowany adres URL i jest dostępny za pośrednictwem protokołu HTTP GET. Oto odpowiedź JSON z wykonywania `operation-location` URL z poprzedniego obrazu:

```json
{
  "status": "Succeeded",
  "recognitionResults": [
    {
      "page": 1,
      "clockwiseOrientation": 2.42,
      "width": 502,
      "height": 252,
      "unit": "pixel",
      "lines": [
        {
          "boundingBox": [
            56,
            39,
            317,
            50,
            313,
            134,
            53,
            123
          ],
          "text": "Tabs VS",
          "words": [
            {
              "boundingBox": [
                90,
                43,
                243,
                53,
                243,
                123,
                94,
                125
              ],
              "text": "Tabs",
              "confidence": "Low"
            },
            {
              "boundingBox": [
                259,
                55,
                313,
                62,
                313,
                122,
                259,
                123
              ],
              "text": "VS"
            }
          ]
        },
        {
          "boundingBox": [
            221,
            148,
            417,
            146,
            417,
            206,
            227,
            218
          ],
          "text": "Spaces",
          "words": [
            {
              "boundingBox": [
                230,
                148,
                416,
                141,
                419,
                211,
                232,
                218
              ],
              "text": "Spaces"
            }
          ]
        }
      ]
    }
  ]
}
```

### <a name="synchronous-read"></a>Odczyt synchroniczne

Do synchronicznego odczytu obrazu można użyć operacji `POST /vision/v2.0/read/core/Analyze`. Gdy obraz jest odczytywany w całości, wówczas i tylko wtedy, gdy interfejs API zwróci odpowiedź JSON. Jedynym wyjątkiem jest to, że wystąpi błąd. Gdy wystąpi błąd, zwracany jest następujący kod JSON:

```json
{
    status: "Failed"
}
```

Obiekt odpowiedzi JSON ma ten sam Graf obiektów co wersja asynchroniczna. Jeśli jesteś użytkownikiem języka JavaScript i chcesz mieć bezpieczeństwo typu, można użyć następujących typów do rzutowania odpowiedzi JSON jako obiektu `AnalyzeResult`.

```typescript
export interface AnalyzeResult {
    status: Status;
    recognitionResults?: RecognitionResult[] | null;
}

export enum Status {
    NotStarted = 0,
    Running = 1,
    Failed = 2,
    Succeeded = 3
}

export enum Unit {
    Pixel = 0,
    Inch = 1
}

export interface RecognitionResult {
    page?: number | null;
    clockwiseOrientation?: number | null;
    width?: number | null;
    height?: number | null;
    unit?: Unit | null;
    lines?: Line[] | null;
}

export interface Line {
    boundingBox?: number[] | null;
    text: string;
    words?: Word[] | null;
}

export interface Word {
  boundingBox?: number[] | null;
  text: string;
  confidence?: string | null;
}
```

Aby zapoznać się z przykładowym przypadkiem użycia, zobacz [piaskownicę TypeScript tutaj](https://aka.ms/ts-read-api-types) i wybierz pozycję "Uruchom", aby wizualizować jej łatwość użytkowania.

# <a name="recognize-texttabrecognize-text"></a>[Rozpoznawanie tekstu](#tab/recognize-text)

### <a name="asynchronous-text-recognition"></a>Asynchroniczne rozpoznawanie tekstu

Możesz użyć operacji `POST /vision/v2.0/recognizeText` i `GET /vision/v2.0/textOperations/*{id}*` w sposób asynchroniczny rozpoznawać drukowany tekst w obrazie, podobnie jak usługa przetwarzanie obrazów używa tych odpowiednich operacji REST. Kontener Rozpoznawanie tekstu rozpoznaje tylko drukowany tekst, a nie tekst odręczny, więc parametr `mode` określony dla operacji usługi przetwarzanie obrazów jest ignorowany przez kontener Rozpoznawanie tekstu.

### <a name="synchronous-text-recognition"></a>Rozpoznawanie tekstu synchronicznego

Za pomocą operacji `POST /vision/v2.0/recognizeTextDirect` można synchronicznie rozpoznać drukowany tekst w obrazie. Ponieważ ta operacja jest synchroniczna, treść żądania dla tej operacji jest taka sama jak operacja `POST /vision/v2.0/recognizeText`, ale treść odpowiedzi dla tej operacji jest taka sama jak zwracana przez operację `GET /vision/v2.0/textOperations/*{id}*`.

***

## <a name="stop-the-container"></a>Zatrzymaj kontener

[!INCLUDE [How to stop the container](../../../includes/cognitive-services-containers-stop.md)]

## <a name="troubleshooting"></a>Rozwiązywanie problemów

Jeśli uruchamiasz kontener z [instalacją wyjściową](./computer-vision-resource-container-config.md#mount-settings) i włączono rejestrowanie, kontener generuje pliki dziennika, które są przydatne do rozwiązywania problemów występujących podczas uruchamiania lub uruchamiania kontenera.

[!INCLUDE [Cognitive Services FAQ note](../containers/includes/cognitive-services-faq-note.md)]

## <a name="billing"></a>Rozliczenia

Kontenery Cognitive Services wysyłają informacje o rozliczeniach do platformy Azure przy użyciu odpowiedniego zasobu na koncie platformy Azure.

[!INCLUDE [Container's Billing Settings](../../../includes/cognitive-services-containers-how-to-billing-info.md)]

Aby uzyskać więcej informacji na temat tych opcji, zobacz [Konfigurowanie kontenerów](./computer-vision-resource-container-config.md).

<!--blogs/samples/video course -->

[!INCLUDE [Discoverability of more container information](../../../includes/cognitive-services-containers-discoverability.md)]

## <a name="summary"></a>Podsumowanie

W tym artykule przedstawiono koncepcje i przepływ pracy służące do pobierania, instalowania i uruchamiania kontenerów przetwarzanie obrazów. Podsumowanie:

* Przetwarzanie obrazów udostępnia kontener systemu Linux dla platformy Docker, hermetyzując oba Rozpoznawanie tekstu i odczytu.
* Obrazy kontenerów są pobierane z rejestru kontenerów "Podgląd kontenera" na platformie Azure.
* Obrazy kontenerów są uruchamiane w platformie Docker.
* Można użyć interfejsu API REST lub zestawu SDK do wywoływania operacji w Rozpoznawanie tekstu lub odczytywania kontenerów przez określenie identyfikatora URI hosta kontenera.
* Podczas tworzenia wystąpienia kontenera należy określić informacje o rozliczeniach.

> [!IMPORTANT]
> Kontenery Cognitive Services nie są licencjonowane do uruchamiania bez połączenia z platformą Azure w celu pomiaru. Klienci muszą włączyć kontenery do przekazywania informacji rozliczeniowych za pomocą usługi pomiarowej przez cały czas. Kontenery Cognitive Services nie wysyłają danych klienta (na przykład obrazu lub tekstu, który jest analizowany) do firmy Microsoft.

## <a name="next-steps"></a>Następne kroki

* Przegląd [konfigurowania kontenerów](computer-vision-resource-container-config.md) dla ustawień konfiguracji
* Przejrzyj [przegląd przetwarzanie obrazów](Home.md) , aby dowiedzieć się więcej o rozpoznawaniu tekstu napisanego i odręcznego
* Zapoznaj się z [interfejs API przetwarzania obrazów](//westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa) , aby uzyskać szczegółowe informacje na temat metod obsługiwanych przez kontener.
* Zapoznaj się z [często zadawanymi pytaniami](FAQ.md) , aby rozwiązać problemy związane z funkcjami przetwarzanie obrazów.
* Użyj więcej [kontenerów Cognitive Services](../cognitive-services-container-support.md)
