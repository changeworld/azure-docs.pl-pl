---
title: Jak zainstalować i uruchomić kontenery - Computer Vision
titleSuffix: Azure Cognitive Services
description: Jak pobrać, zainstalować i uruchomić kontenery dla wizji komputerowej w tym samouczku.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 04/01/2020
ms.author: aahi
ms.custom: seodec18
ms.openlocfilehash: 5f36c429041a8182551d1f077f0a1229f520e8c1
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/08/2020
ms.locfileid: "80879347"
---
# <a name="install-and-run-read-containers-preview"></a>Instalowanie i uruchamianie kontenerów odczytu (wersja zapoznawcza)

Kontenery umożliwiają uruchamianie interfejsów API przetwarzania w środowisku. Kontenery są idealne dla określonych wymagań dotyczących zabezpieczeń i zarządzania danymi. W tym artykule dowiesz się, jak pobrać, zainstalować i uruchomić kontener przetwarzania.

Jeden kontener platformy Docker, *Odczyt,* jest dostępny dla przetwarzania obrazów komputerowych. Odczyt *Read* kontenera umożliwia wykrywanie i wyodrębnianie *drukowanego tekstu* z obrazów różnych obiektów o różnych powierzchniach i tłach, takich jak paragony, plakaty i wizytówki. Ponadto *odczyt* kontenera wykrywa *tekst odręczny* w obrazach i zapewnia obsługę plików PDF, TIFF i wielostronicowych. Aby uzyskać więcej informacji, zobacz [przeczytaj](concept-recognizing-text.md#read-api) dokumentację interfejsu API.

Jeśli nie masz subskrypcji platformy Azure, utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) przed rozpoczęciem.

## <a name="prerequisites"></a>Wymagania wstępne

Przed użyciem kontenerów należy spełnić następujące wymagania wstępne:

|Wymagany|Przeznaczenie|
|--|--|
|Aparat platformy Docker| Aparat platformy Docker jest zainstalowany na [komputerze-hoście.](#the-host-computer) Platforma Docker udostępnia pakiety, które konfigurują środowisko platformy Docker w systemach [macOS](https://docs.docker.com/docker-for-mac/), [Windows](https://docs.docker.com/docker-for-windows/) i [Linux](https://docs.docker.com/engine/installation/#supported-platforms). Aby uzyskać podstawowe informacje na temat platformy Docker i kontenerów, zapoznaj się z artykułem [Docker overview](https://docs.docker.com/engine/docker-overview/) (Przegląd platformy Docker).<br><br> Platforma Docker musi być skonfigurowana tak, aby zezwalała kontenerom na łączenie się z danymi rozliczeń i wysyłanie ich na platformę Azure. <br><br> **W systemie Windows**program Docker musi być również skonfigurowany do obsługi kontenerów systemu Linux.<br><br>|
|Znajomość platformy Docker | Powinieneś mieć podstawową wiedzę na temat pojęć platformy Docker, takich jak rejestry, repozytoria, kontenery i obrazy kontenerów, a także znajomość podstawowych `docker` poleceń.| 
|Zasób przetwarzania obrazów komputerowych |Aby korzystać z kontenera, musisz mieć:<br><br>Zasób usługi Azure **Computer Vision** i skojarzony klucz interfejsu API identyfikatora URI punktu końcowego. Obie wartości są dostępne na stronach Przegląd i Klucze dla zasobu i są wymagane do uruchomienia kontenera.<br><br>**{API_KEY}**: Jeden z dwóch dostępnych kluczy zasobów na stronie **Klucze**<br><br>**{ENDPOINT_URI}**: Punkt końcowy podany na stronie **Przegląd**|

## <a name="request-access-to-the-private-container-registry"></a>Żądanie dostępu do rejestru kontenerów prywatnych

[!INCLUDE [Request access to public preview](../../../includes/cognitive-services-containers-request-access.md)]

[!INCLUDE [Gathering required container parameters](../containers/includes/container-gathering-required-parameters.md)]

### <a name="the-host-computer"></a>Komputer-host

[!INCLUDE [Host Computer requirements](../../../includes/cognitive-services-containers-host-computer.md)]

### <a name="advanced-vector-extension-support"></a>Obsługa zaawansowanego rozszerzenia vector

**Komputer-host** to komputer, na który działa kontener platformy docker. Host *musi obsługiwać* [zaawansowane rozszerzenia wektorowe](https://en.wikipedia.org/wiki/Advanced_Vector_Extensions#CPUs_with_AVX2) (AVX2). Możesz sprawdzić, czy na hostach Systemu Linux nie ma obsługi AVX2 za pomocą następującego polecenia:

```console
grep -q avx2 /proc/cpuinfo && echo AVX2 supported || echo No AVX2 support detected
```
> [!WARNING]
> Komputer-host jest *wymagany* do obsługi avx2. Kontener *nie będzie* działać poprawnie bez obsługi AVX2.

### <a name="container-requirements-and-recommendations"></a>Wymagania i zalecenia dotyczące kontenerów

[!INCLUDE [Container requirements and recommendations](includes/container-requirements-and-recommendations.md)]

## <a name="get-the-container-image-with-docker-pull"></a>Pobierz obraz kontenera za pomocą`docker pull`

Obrazy kontenerów do odczytu są dostępne.

| Kontener | Rejestr kontenerów / repozytorium / nazwa obrazu |
|-----------|------------|
| Odczyt | `containerpreview.azurecr.io/microsoft/cognitive-services-read:latest` |

Użyj [`docker pull`](https://docs.docker.com/engine/reference/commandline/pull/) polecenia, aby pobrać obraz kontenera.

### <a name="docker-pull-for-the-read-container"></a>Ściąganie platformy Docker dla kontenera Odczyt

```bash
docker pull containerpreview.azurecr.io/microsoft/cognitive-services-read:latest
```

[!INCLUDE [Tip for using docker list](../../../includes/cognitive-services-containers-docker-list-tip.md)]

## <a name="how-to-use-the-container"></a>Jak korzystać z pojemnika

Gdy kontener znajduje się na [komputerze-hoście,](#the-host-computer)użyj następującego procesu do pracy z kontenerem.

1. [Uruchom kontener](#run-the-container-with-docker-run)z wymaganymi ustawieniami rozliczeń. Dostępnych jest więcej `docker run` [przykładów](computer-vision-resource-container-config.md) polecenia. 
1. [Kwerenda punktu końcowego przewidywania kontenera](#query-the-containers-prediction-endpoint). 

## <a name="run-the-container-with-docker-run"></a>Uruchom pojemnik za pomocą`docker run`

Użyj polecenia [uruchamiania platformy docker,](https://docs.docker.com/engine/reference/commandline/run/) aby uruchomić kontener. Szczegółowe informacje na temat sposobu uzyskania wartości `{ENDPOINT_URI}` `{API_KEY}` można znaleźć w przypadku [zbierania wymaganych parametrów.](#gathering-required-parameters)

[Dostępne](computer-vision-resource-container-config.md#example-docker-run-commands) są `docker run` przykłady polecenia.

```bash
docker run --rm -it -p 5000:5000 --memory 16g --cpus 8 \
containerpreview.azurecr.io/microsoft/cognitive-services-read \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

To polecenie:

* Uruchamia odczyt kontenera z obrazu kontenera.
* Przydziela 8 rdzeni procesora i 16 gigabajtów (GB) pamięci.
* Udostępnia port TCP 5000 i przydziela pseudo-TTY dla kontenera.
* Automatycznie usuwa kontener po jego zamknięciu. Obraz kontenera jest nadal dostępny na komputerze-hoście.

Dostępnych jest więcej `docker run` [przykładów](./computer-vision-resource-container-config.md#example-docker-run-commands) polecenia. 

> [!IMPORTANT]
> Aby `Eula` `Billing`uruchomić `ApiKey` kontener, należy określić opcje i opcje; w przeciwnym razie kontener nie zostanie uruchomiony.  Aby uzyskać więcej informacji, zobacz [Rozliczenia](#billing).

[!INCLUDE [Running multiple containers on the same host](../../../includes/cognitive-services-containers-run-multiple-same-host.md)]

<!--  ## Validate container is running -->

[!INCLUDE [Container's API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]

## <a name="query-the-containers-prediction-endpoint"></a>Kwerenda punktu końcowego przewidywania kontenera

Kontener zawiera interfejsy API punktu końcowego przewidywania zapytań oparte na rest. 

Użyj hosta, `http://localhost:5000`dla interfejsów API kontenera.

### <a name="asynchronous-read"></a>Odczyt asynchroniczne

Operacji `POST /vision/v2.0/read/core/asyncBatchAnalyze` i `GET /vision/v2.0/read/operations/{operationId}` operacji w porozumieniu asynchronicznie odczytać obraz, podobnie jak usługa przetwarzania obrazu używa tych odpowiednich operacji REST. Asynchroniczne POST metoda `operationId` zwróci, który jest używany jako identifer do żądania HTTP GET.

W interfejsie użytkownika swagger `asyncBatchAnalyze` wybierz, aby rozwinąć go w przeglądarce. Następnie wybierz pozycję **Wypróbuj go Wybierz** > **plik**. W tym przykładzie użyjemy następującego obrazu:

![karty a spacje](media/tabs-vs-spaces.png)

Po pomyślnym uruchomieniu asynchroniiowego postu zwraca kod stanu **HTTP 202.** Jako część odpowiedzi istnieje nagłówek, który przechowuje `operation-location` punkt końcowy wyniku dla żądania.

```http
 content-length: 0
 date: Fri, 13 Sep 2019 16:23:01 GMT
 operation-location: http://localhost:5000/vision/v2.0/read/operations/a527d445-8a74-4482-8cb3-c98a65ec7ef9
 server: Kestrel
```

Jest `operation-location` w pełni kwalifikowany adres URL i jest dostępny za pośrednictwem HTTP GET. Oto odpowiedź JSON z wykonywania `operation-location` adresu URL z poprzedniego obrazu:

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
          "boundingBox": [ 56, 39, 317, 50, 313, 134, 53, 123 ],
          "text": "Tabs VS",
          "words": [
            {
              "boundingBox": [ 90, 43, 243, 53, 243, 123, 94, 125 ],
              "text": "Tabs",
              "confidence": "Low"
            },
            {
              "boundingBox": [ 259, 55, 313, 62, 313, 122, 259, 123 ],
              "text": "VS"
            }
          ]
        },
        {
          "boundingBox": [ 221, 148, 417, 146, 417, 206, 227, 218 ],
          "text": "Spaces",
          "words": [
            {
              "boundingBox": [ 230, 148, 416, 141, 419, 211, 232, 218 ],
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

`POST /vision/v2.0/read/core/Analyze` Operacji można użyć do synchronicznie odczytać obraz. Gdy obraz jest odczytywany w całości, a następnie tylko wtedy nie interfejs API zwraca odpowiedź JSON. Jedynym wyjątkiem jest wystąpienie błędu. Po wystąpieniu błędu zwracany jest następujący JSON:

```json
{
    status: "Failed"
}
```

Obiekt odpowiedzi JSON ma ten sam wykres obiektu co wersja asynchroniza. Jeśli jesteś użytkownikiem JavaScript i chcesz bezpieczeństwa typu, następujące typy mogą `AnalyzeResult` służyć do rzutu odpowiedzi JSON jako obiektu.

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

export enum Confidence {
    High = 0,
    Low = 1
}

export interface Word {
  boundingBox?: number[] | null;
  text: string;
  confidence?: Confidence | null;
}
```

Przykładowy przypadek użycia można znaleźć w <a href="https://aka.ms/ts-read-api-types" target="_blank" rel="noopener noreferrer">obszarze <span class="docon docon-navigate-external x-hidden-focus"></span> izolowanym TypeScript</a> w tym miejscu i wybrać opcję **Uruchom,** aby zwizualizować jego łatwość użycia.

## <a name="stop-the-container"></a>Zatrzymywanie kontenera

[!INCLUDE [How to stop the container](../../../includes/cognitive-services-containers-stop.md)]

## <a name="troubleshooting"></a>Rozwiązywanie problemów

Jeśli uruchomisz kontener z [włączoną instalacją](./computer-vision-resource-container-config.md#mount-settings) wyjściową i rejestrowaniem, kontener generuje pliki dziennika, które są przydatne do rozwiązywania problemów, które występują podczas uruchamiania lub uruchamiania kontenera.

[!INCLUDE [Cognitive Services FAQ note](../containers/includes/cognitive-services-faq-note.md)]

## <a name="billing"></a>Rozliczenia

Kontenery usług Cognitive Services wysyłają informacje rozliczeniowe na platformę Azure przy użyciu odpowiedniego zasobu na koncie platformy Azure.

[!INCLUDE [Container's Billing Settings](../../../includes/cognitive-services-containers-how-to-billing-info.md)]

Aby uzyskać więcej informacji na temat tych opcji, zobacz [Konfigurowanie kontenerów](./computer-vision-resource-container-config.md).

<!--blogs/samples/video course -->

[!INCLUDE [Discoverability of more container information](../../../includes/cognitive-services-containers-discoverability.md)]

## <a name="summary"></a>Podsumowanie

W tym artykule poznaliście pojęcia i przepływ pracy do pobierania, instalowania i uruchamiania kontenerów przetwarzania. Podsumowanie:

* Usługa Przetwarzania Komputerów zawiera kontener systemu Linux dla platformy Docker, hermetyzujący odczyt.
* Obrazy kontenerów są pobierane z rejestru kontenerów "Container Preview" na platformie Azure.
* Obrazy kontenerów są uruchamiane w usłudze Docker.
* Można użyć interfejsu API REST lub SDK do wywoływania operacji w kontenerach Odczyt, określając identyfikator URI hosta kontenera.
* Podczas tworzenia wystąpienia kontenera należy określić informacje rozliczeniowe.

> [!IMPORTANT]
> Kontenery usług Cognitive Services nie są licencjonowane do uruchamiania bez połączenia z platformą Azure w celu pomiaru. Klienci muszą włączyć kontenery do przekazywania informacji rozliczeniowych z usługą pomiaru przez cały czas. Kontenery usług Cognitive Services nie wysyłają danych klientów (na przykład obrazu lub tekstu, który jest analizowany) do firmy Microsoft.

## <a name="next-steps"></a>Następne kroki

* Przeglądanie [Konfigurowanie kontenerów](computer-vision-resource-container-config.md) pod kątem ustawień konfiguracji
* Przejrzyj [omówienie wizji komputerowej,](Home.md) aby dowiedzieć się więcej o rozpoznawaniu tekstu drukowanego i odręcznego
* Szczegółowe informacje na temat metod obsługiwanych przez kontener można znaleźć w [interfejsie API przetwarzania.](//westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa)
* Zapoznaj się [z często zadawanymi pytaniami (FAQ),](FAQ.md) aby rozwiązać problemy związane z funkcją przetwarzania.
* Użyj większej liczby [kontenerów usług Cognitive Services](../cognitive-services-container-support.md)
