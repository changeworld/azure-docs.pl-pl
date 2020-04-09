---
title: Instalowanie kontenerów mowy — usługa mowy
titleSuffix: Azure Cognitive Services
description: Instalowanie i uruchamianie kontenerów mowy. Zamiana mowy na tekst umożliwia transkrypcję strumieni audio na tekst w czasie rzeczywistym, które mogą wykorzystywać lub wyświetlać aplikacje, narzędzia lub urządzenia. Zamiana tekstu na mowę konwertuje tekst wejściowy na mowę syntetyzowaną podobną do człowieka.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: 2caae4fecdf13a1833f23cf9423cf3ded67f6f72
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/08/2020
ms.locfileid: "80879029"
---
# <a name="install-and-run-speech-service-containers-preview"></a>Instalowanie i uruchamianie kontenerów usługi mowy (wersja zapoznawcza)

Kontenery umożliwiają uruchamianie niektórych interfejsów API usługi mowy w swoim własnym środowisku. Kontenery są idealne dla określonych wymagań dotyczących zabezpieczeń i zarządzania danymi. W tym artykule dowiesz się, jak pobrać, zainstalować i uruchomić kontener mowy.

Kontenery mowy umożliwiają klientom tworzenie architektury aplikacji mowy, która jest zoptymalizowana pod kątem zarówno niezawodnych funkcji chmury, jak i lokalizacji brzegowej. Dostępne są cztery różne kontenery. Dwa standardowe kontenery to **Zamiana mowy na tekst** i **Zamiana tekstu na mowę.** Dwa kontenery niestandardowe to **Niestandardowa zamiana mowy na tekst** i **Niestandardowy tekst na mowę.**

> [!IMPORTANT]
> Wszystkie kontenery mowy są obecnie oferowane w ramach [publicznego podglądu "Gated".](../cognitive-services-container-support.md#public-gated-preview-container-registry-containerpreviewazurecrio) Ogłoszenie zostanie dokonane, gdy kontenery mowy postępu do ogólnej dostępności (GA).

| Funkcja | Funkcje | Najnowsza |
|--|--|--|
| Zamiana mowy na tekst | Transkrybuje ciągłe nagrania mowy w czasie rzeczywistym lub wsadowe nagrania audio na tekst z wynikami pośrednimi. | 2.1.1 |
| Niestandardowa zamiana mowy na tekst | Korzystając z niestandardowego modelu z [portalu Mowy niestandardowej,](https://speech.microsoft.com/customspeech)transkrybuje ciągłe nagrania mowy w czasie rzeczywistym lub wsadowe nagrania audio na tekst z wynikami pośrednimi. | 2.1.1 |
| Zamiana tekstu na mowę | Konwertuje tekst na mowę o naturalnym brzmieniu za pomocą zwykłego tekstu lub języka SSML (Speech Synthesis Markup Language). | 1.3.0 |
| Niestandardowy tekst na mowę | Korzystając z niestandardowego modelu z [portalu Niestandardowy głos,](https://aka.ms/custom-voice-portal)konwertuje tekst na naturalnie brzmiącą mowę z wprowadzaniem zwykłego tekstu lub językiem znaczników syntezy mowy (SSML). | 1.3.0 |

Jeśli nie masz subskrypcji platformy Azure, utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) przed rozpoczęciem.

## <a name="prerequisites"></a>Wymagania wstępne

Następujące wymagania wstępne przed użyciem kontenerów mowy:

| Wymagany | Przeznaczenie |
|--|--|
| Aparat platformy Docker | Aparat platformy Docker jest zainstalowany na [komputerze-hoście.](#the-host-computer) Platforma Docker udostępnia pakiety, które konfigurują środowisko platformy Docker w systemach [macOS](https://docs.docker.com/docker-for-mac/), [Windows](https://docs.docker.com/docker-for-windows/) i [Linux](https://docs.docker.com/engine/installation/#supported-platforms). Aby uzyskać podstawowe informacje na temat platformy Docker i kontenerów, zapoznaj się z artykułem [Docker overview](https://docs.docker.com/engine/docker-overview/) (Przegląd platformy Docker).<br><br> Platforma Docker musi być skonfigurowana tak, aby zezwalała kontenerom na łączenie się z danymi rozliczeń i wysyłanie ich na platformę Azure. <br><br> **W systemie Windows**program Docker musi być również skonfigurowany do obsługi kontenerów systemu Linux.<br><br> |
| Znajomość platformy Docker | Powinieneś mieć podstawową wiedzę na temat pojęć platformy Docker, takich jak rejestry, repozytoria, kontenery i obrazy kontenerów, a także znajomość podstawowych `docker` poleceń. |
| Zasób mowy | Aby korzystać z tych pojemników, musisz mieć:<br><br>_Zasób rozpoznawania mowy_ platformy Azure w celu uzyskania skojarzonego klucza interfejsu API i identyfikatora URI punktu końcowego. Obie wartości są dostępne na stronach **Omówienie mowy** i klucze witryny azure portal. Oba są wymagane do uruchomienia kontenera.<br><br>**{API_KEY}**: Jeden z dwóch dostępnych kluczy zasobów na stronie **Klucze**<br><br>**{ENDPOINT_URI}**: Punkt końcowy podany na stronie **Przegląd** |

## <a name="request-access-to-the-container-registry"></a>Żądanie dostępu do rejestru kontenerów

Wypełnij i prześlij [formularz żądania kontenerów mowy usług Cognitive Services,](https://aka.ms/speechcontainerspreview/) aby zażądać dostępu do kontenera. 

[!INCLUDE [Request access to the container registry](../../../includes/cognitive-services-containers-request-access-only.md)]

[!INCLUDE [Authenticate to the container registry](../../../includes/cognitive-services-containers-access-registry.md)]

[!INCLUDE [Gathering required parameters](../containers/includes/container-gathering-required-parameters.md)]

## <a name="the-host-computer"></a>Komputer-host

[!INCLUDE [Host Computer requirements](../../../includes/cognitive-services-containers-host-computer.md)]

### <a name="advanced-vector-extension-support"></a>Obsługa zaawansowanego rozszerzenia vector

**Host** jest komputerem, który uruchamia kontener platformy docker. Host *musi obsługiwać* [zaawansowane rozszerzenia wektorowe](https://en.wikipedia.org/wiki/Advanced_Vector_Extensions#CPUs_with_AVX2) (AVX2). Możesz sprawdzić, czy na hostach Systemu Linux nie ma obsługi AVX2 za pomocą następującego polecenia:

```console
grep -q avx2 /proc/cpuinfo && echo AVX2 supported || echo No AVX2 support detected
```
> [!WARNING]
> Komputer-host jest *wymagany* do obsługi avx2. Kontener *nie będzie* działać poprawnie bez obsługi AVX2.

### <a name="container-requirements-and-recommendations"></a>Wymagania i zalecenia dotyczące kontenerów

W poniższej tabeli opisano minimalną i zalecaną alokację zasobów dla każdego kontenera mowy.

# <a name="speech-to-text"></a>[Zamiana mowy na tekst](#tab/stt)

| Kontener | Minimalne | Zalecane |
|-----------|---------|-------------|
| Zamiana mowy na tekst | 2-rdzeniowa pamięć 2 GB | 4-rdzeniowa pamięć 4 GB |

# <a name="custom-speech-to-text"></a>[Niestandardowa zamiana mowy na tekst](#tab/cstt)

| Kontener | Minimalne | Zalecane |
|-----------|---------|-------------|
| Niestandardowa zamiana mowy na tekst | 2-rdzeniowa pamięć 2 GB | 4-rdzeniowa pamięć 4 GB |

# <a name="text-to-speech"></a>[Zamiana tekstu na mowę](#tab/tts)

| Kontener | Minimalne | Zalecane |
|-----------|---------|-------------|
| Zamiana tekstu na mowę | 1 rdzeń, 2 GB pamięci | 2-rdzeniowa pamięć 3 GB |

# <a name="custom-text-to-speech"></a>[Niestandardowy tekst na mowę](#tab/ctts)

| Kontener | Minimalne | Zalecane |
|-----------|---------|-------------|
| Niestandardowy tekst na mowę | 1 rdzeń, 2 GB pamięci | 2-rdzeniowa pamięć 3 GB |

***

* Każdy rdzeń musi mieć co najmniej 2,6 gigaherca (GHz) lub więcej.

Rdzeń i pamięć `--cpus` `--memory` odpowiadają i ustawienia, które `docker run` są używane jako część polecenia.

> [!NOTE]
> Minimalne i zalecane są oparte na limitach platformy Docker, a *nie* zasobów komputera hosta. Na przykład kontenery mowy na tekst mapują części dużego modelu języka i *zaleca się,* aby cały plik mieścił się w pamięci, co stanowi dodatkowe 4-6 GB. Ponadto pierwsze uruchomienie albo kontenera może potrwać dłużej, ponieważ modele są stronicowwane w pamięci.

## <a name="get-the-container-image-with-docker-pull"></a>Pobierz obraz kontenera za pomocą`docker pull`

Obrazy kontenerów dla mowy są dostępne w następującym rejestrze kontenerów.

# <a name="speech-to-text"></a>[Zamiana mowy na tekst](#tab/stt)

| Kontener | Repozytorium |
|-----------|------------|
| Zamiana mowy na tekst | `containerpreview.azurecr.io/microsoft/cognitive-services-speech-to-text:latest` |

# <a name="custom-speech-to-text"></a>[Niestandardowa zamiana mowy na tekst](#tab/cstt)

| Kontener | Repozytorium |
|-----------|------------|
| Niestandardowa zamiana mowy na tekst | `containerpreview.azurecr.io/microsoft/cognitive-services-custom-speech-to-text:latest` |

# <a name="text-to-speech"></a>[Zamiana tekstu na mowę](#tab/tts)

| Kontener | Repozytorium |
|-----------|------------|
| Zamiana tekstu na mowę | `containerpreview.azurecr.io/microsoft/cognitive-services-text-to-speech:latest` |

# <a name="custom-text-to-speech"></a>[Niestandardowy tekst na mowę](#tab/ctts)

| Kontener | Repozytorium |
|-----------|------------|
| Niestandardowy tekst na mowę | `containerpreview.azurecr.io/microsoft/cognitive-services-custom-text-to-speech:latest` |

***

[!INCLUDE [Tip for using docker list](../../../includes/cognitive-services-containers-docker-list-tip.md)]

### <a name="docker-pull-for-the-speech-containers"></a>Ściąganie platformy Docker dla kontenerów mowy

# <a name="speech-to-text"></a>[Zamiana mowy na tekst](#tab/stt)

#### <a name="docker-pull-for-the-speech-to-text-container"></a>Ściąganie platformy Docker dla kontenera Zamiana mowy na tekst

Polecenie [ściągania platformy docker](https://docs.docker.com/engine/reference/commandline/pull/) służy do pobierania obrazu kontenera z rejestru podglądu kontenera.

```Docker
docker pull containerpreview.azurecr.io/microsoft/cognitive-services-speech-to-text:latest
```

> [!IMPORTANT]
> Tag `latest` pobiera `en-US` ustawienia regionalne. Aby uzyskać dodatkowe ustawienia regionalne, zobacz [Ustawienia regionalne zamiany mowy na tekst](#speech-to-text-locales).

#### <a name="speech-to-text-locales"></a>Ustawienia regionalne zamiany mowy na tekst

Wszystkie tagi, `latest` z wyjątkiem są w następującym formacie i są rozróżniane wielkość liter:

```
<major>.<minor>.<patch>-<platform>-<locale>-<prerelease>
```

Przykładem formatu jest następujący tag:

```
2.1.1-amd64-en-us-preview
```

W przypadku wszystkich obsługiwanych ustawień regionalnych **kontenera zamiany mowy na tekst** zobacz [Znaczniki obrazu zamiany mowy na tekst](../containers/container-image-tags.md#speech-to-text).

# <a name="custom-speech-to-text"></a>[Niestandardowa zamiana mowy na tekst](#tab/cstt)

#### <a name="docker-pull-for-the-custom-speech-to-text-container"></a>Ściąganie platformy Docker dla kontenera Niestandardowa zamiana mowy na tekst

Polecenie [ściągania platformy docker](https://docs.docker.com/engine/reference/commandline/pull/) służy do pobierania obrazu kontenera z rejestru podglądu kontenera.

```Docker
docker pull containerpreview.azurecr.io/microsoft/cognitive-services-custom-speech-to-text:latest
```

> [!NOTE]
> `locale` Kontenery `voice` mowy niestandardowe i niestandardowe są określane przez model niestandardowy pozyskiwania przez kontener.

# <a name="text-to-speech"></a>[Zamiana tekstu na mowę](#tab/tts)

#### <a name="docker-pull-for-the-text-to-speech-container"></a>Ściąganie platformy Docker dla kontenera zamiany tekstu na mowę

Polecenie [ściągania platformy docker](https://docs.docker.com/engine/reference/commandline/pull/) służy do pobierania obrazu kontenera z rejestru podglądu kontenera.

```Docker
docker pull containerpreview.azurecr.io/microsoft/cognitive-services-text-to-speech:latest
```

> [!IMPORTANT]
> Tag `latest` pobiera `en-US` ustawienia regionalne `jessarus` i głos. Aby uzyskać dodatkowe ustawienia regionalne, zobacz [Ustawienia regionalne zamiany tekstu na mowę](#text-to-speech-locales).

#### <a name="text-to-speech-locales"></a>Ustawienia regionalne zamiany tekstu na mowę

Wszystkie tagi, `latest` z wyjątkiem są w następującym formacie i są rozróżniane wielkość liter:

```
<major>.<minor>.<patch>-<platform>-<locale>-<voice>-<prerelease>
```

Przykładem formatu jest następujący tag:

```
1.3.0-amd64-en-us-jessarus-preview
```

W przypadku wszystkich obsługiwanych ustawień regionalnych i odpowiadających im głosów **kontenera zamiany tekstu na mowę** zobacz [Znaczniki obrazów zamiany tekstu na mowę](../containers/container-image-tags.md#text-to-speech).

> [!IMPORTANT]
> Podczas konstruowania *standardowego text-to-speech* HTTP POST, [komunikat SSML (Speech Synthesis Markup Language)](speech-synthesis-markup.md) wymaga `voice` elementu z atrybutem. `name` Wartość jest odpowiednimi ustawieniami regionalnymi kontenera i głosem, znanymi również jako ["krótka nazwa".](language-support.md#standard-voices) Na przykład `latest` tag będzie miał nazwę `en-US-JessaRUS`głosową .

# <a name="custom-text-to-speech"></a>[Niestandardowy tekst na mowę](#tab/ctts)

#### <a name="docker-pull-for-the-custom-text-to-speech-container"></a>Ściąganie platformy Docker dla kontenera Niestandardowy tekst na mowę

Polecenie [ściągania platformy docker](https://docs.docker.com/engine/reference/commandline/pull/) służy do pobierania obrazu kontenera z rejestru podglądu kontenera.

```Docker
docker pull containerpreview.azurecr.io/microsoft/cognitive-services-custom-text-to-speech:latest
```

> [!NOTE]
> `locale` Kontenery `voice` mowy niestandardowe i niestandardowe są określane przez model niestandardowy pozyskiwania przez kontener.

***

## <a name="how-to-use-the-container"></a>Jak korzystać z pojemnika

Gdy kontener znajduje się na [komputerze-hoście,](#the-host-computer)użyj następującego procesu do pracy z kontenerem.

1. [Uruchom kontener](#run-the-container-with-docker-run)z wymaganymi ustawieniami rozliczeń. Dostępnych jest więcej `docker run` [przykładów](speech-container-configuration.md#example-docker-run-commands) polecenia.
1. [Kwerenda punktu końcowego przewidywania kontenera](#query-the-containers-prediction-endpoint).

## <a name="run-the-container-with-docker-run"></a>Uruchom pojemnik za pomocą`docker run`

Użyj polecenia [uruchamiania platformy docker,](https://docs.docker.com/engine/reference/commandline/run/) aby uruchomić kontener. Szczegółowe informacje na temat sposobu uzyskania wartości `{Endpoint_URI}` `{API_Key}` można znaleźć w przypadku [zbierania wymaganych parametrów.](#gathering-required-parameters) Dostępne są również `docker run` dodatkowe [przykłady](speech-container-configuration.md#example-docker-run-commands) polecenia.

# <a name="speech-to-text"></a>[Zamiana mowy na tekst](#tab/stt)

Aby uruchomić kontener *zamiany mowy na tekst,* wykonaj następujące `docker run` polecenie.

```bash
docker run --rm -it -p 5000:5000 --memory 4g --cpus 4 \
containerpreview.azurecr.io/microsoft/cognitive-services-speech-to-text \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

To polecenie:

* Uruchamia kontener *zamiany mowy na tekst* z obrazu kontenera.
* Przydziela 4 rdzenie procesora i 4 gigabajty (GB) pamięci.
* Udostępnia port TCP 5000 i przydziela pseudo-TTY dla kontenera.
* Automatycznie usuwa kontener po jego zamknięciu. Obraz kontenera jest nadal dostępny na komputerze-hoście.

# <a name="custom-speech-to-text"></a>[Niestandardowa zamiana mowy na tekst](#tab/cstt)

*Kontener Niestandardowy zamiany mowy na tekst* opiera się na niestandardowym modelu mowy. Model niestandardowy musi zostać [przeszkolony](how-to-custom-speech-train-model.md) przy użyciu [niestandardowego portalu mowy](https://speech.microsoft.com/customspeech).

> [!IMPORTANT]
> Model mowy niestandardowej musi być przeszkolony z jednej z następujących wersji modelu:
> * **20181201 (v3.3 Ujednolicony)**
> * **20190520 (v4.14 Zunifikowany)**
> * **20190701 (v4.17 Zunifikowany)**<br>
> ![Niestandardowy model kontenera pociągu mowy](media/custom-speech/custom-speech-train-model-container-scoped.png)

Identyfikator **modelu** mowy niestandardowej jest wymagany do uruchomienia kontenera. Można go znaleźć na stronie **Szkolenia** niestandardowego portalu mowy. Z niestandardowego portalu mowy przejdź do strony **Szkolenia** i wybierz model.
<br>

![Niestandardowa strona szkolenia mowy](media/custom-speech/custom-speech-model-training.png)

Uzyskaj **identyfikator modelu,** aby użyć go `ModelId` jako `docker run` argumentu do parametru polecenia.
<br>

![Niestandardowe szczegóły modelu mowy](media/custom-speech/custom-speech-model-details.png)

Poniższa tabela `docker run` przedstawia różne parametry i odpowiadające im opisy:

| Parametr | Opis |
|---------|---------|
| `{VOLUME_MOUNT}` | [Instalacja woluminu](https://docs.docker.com/storage/volumes/)komputera hosta , którego docker używa do utrwalenia modelu niestandardowego. Na przykład *C:\CustomSpeech,* gdzie *dysk C* znajduje się na komputerze-hoście. |
| `{MODEL_ID}` | Identyfikator **niestandardowego modelu** mowy z strony **Szkolenia** niestandardowego portalu mowy. |
| `{ENDPOINT_URI}` | Punkt końcowy jest wymagany do pomiaru i rozliczeń. Aby uzyskać więcej informacji, zobacz [zbieranie wymaganych parametrów](#gathering-required-parameters). |
| `{API_KEY}` | Wymagany jest klucz interfejsu API. Aby uzyskać więcej informacji, zobacz [zbieranie wymaganych parametrów](#gathering-required-parameters). |

Aby uruchomić kontener *Mowy niestandardowej na tekst,* wykonaj następujące `docker run` polecenie:

```bash
docker run --rm -it -p 5000:5000 --memory 4g --cpus 4 \
-v {VOLUME_MOUNT}:/usr/local/models \
containerpreview.azurecr.io/microsoft/cognitive-services-custom-speech-to-text \
ModelId={MODEL_ID} \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

To polecenie:

* Uruchamia *niestandardowy* kontener zamiany mowy na tekst z obrazu kontenera.
* Przydziela 4 rdzenie procesora i 4 gigabajty (GB) pamięci.
* Ładuje *niestandardowy* model zamiany mowy na tekst z mocowania wejściowego woluminu, na przykład *C:\CustomSpeech*.
* Udostępnia port TCP 5000 i przydziela pseudo-TTY dla kontenera.
* Pobiera model podany `ModelId` (jeśli nie znajduje się na górze woluminu).
* Jeśli model niestandardowy został wcześniej `ModelId` pobrany, jest ignorowany.
* Automatycznie usuwa kontener po jego zamknięciu. Obraz kontenera jest nadal dostępny na komputerze-hoście.

# <a name="text-to-speech"></a>[Zamiana tekstu na mowę](#tab/tts)

Aby uruchomić kontener *zamiany tekstu na mowę,* wykonaj następujące `docker run` polecenie.

```bash
docker run --rm -it -p 5000:5000 --memory 2g --cpus 1 \
containerpreview.azurecr.io/microsoft/cognitive-services-text-to-speech \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

To polecenie:

* Uruchamia kontener *zamiany tekstu na mowę* z obrazu kontenera.
* Przydziela 2 rdzenie procesora i jeden gigabajt (GB) pamięci.
* Udostępnia port TCP 5000 i przydziela pseudo-TTY dla kontenera.
* Automatycznie usuwa kontener po jego zamknięciu. Obraz kontenera jest nadal dostępny na komputerze-hoście.

# <a name="custom-text-to-speech"></a>[Niestandardowy tekst na mowę](#tab/ctts)

*Kontener Niestandardowy tekst na mowę* opiera się na niestandardowym modelu głosowym. Model niestandardowy musi zostać [przeszkolony](how-to-custom-voice-create-voice.md) przy użyciu [niestandardowego portalu głosowego](https://aka.ms/custom-voice-portal). Identyfikator **modelu** niestandardowego głosu jest wymagany do uruchomienia kontenera. Można go znaleźć na stronie **Szkolenia** niestandardowego portalu głosowego. W niestandardowym portalu głosowym przejdź do strony **Szkolenia** i wybierz model.
<br>

![Niestandardowa strona szkolenia głosowego](media/custom-voice/custom-voice-model-training.png)

Uzyskaj **identyfikator modelu,** aby użyć go `ModelId` jako argumentu do parametru polecenia uruchamiania platformy docker.
<br>

![Niestandardowe szczegóły modelu głosu](media/custom-voice/custom-voice-model-details.png)

Poniższa tabela `docker run` przedstawia różne parametry i odpowiadające im opisy:

| Parametr | Opis |
|---------|---------|
| `{VOLUME_MOUNT}` | [Instalacja woluminu](https://docs.docker.com/storage/volumes/)komputera hosta , którego docker używa do utrwalenia modelu niestandardowego. Na przykład *C:\CustomSpeech,* gdzie *dysk C* znajduje się na komputerze-hoście. |
| `{MODEL_ID}` | Identyfikator **niestandardowego modelu** mowy na stronie **Szkolenia** niestandardowego portalu głosowego. |
| `{ENDPOINT_URI}` | Punkt końcowy jest wymagany do pomiaru i rozliczeń. Aby uzyskać więcej informacji, zobacz [zbieranie wymaganych parametrów](#gathering-required-parameters). |
| `{API_KEY}` | Wymagany jest klucz interfejsu API. Aby uzyskać więcej informacji, zobacz [zbieranie wymaganych parametrów](#gathering-required-parameters). |

Aby uruchomić kontener *Niestandardowy tekst na mowę,* wykonaj następujące `docker run` polecenie:

```bash
docker run --rm -it -p 5000:5000 --memory 2g --cpus 1 \
-v {VOLUME_MOUNT}:/usr/local/models \
containerpreview.azurecr.io/microsoft/cognitive-services-custom-text-to-speech \
ModelId={MODEL_ID} \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

To polecenie:

* Uruchamia *kontener niestandardowy tekst na mowę* z obrazu kontenera.
* Przydziela 2 rdzenie procesora i jeden gigabajt (GB) pamięci.
* Ładuje *niestandardowy model zamiany tekstu na mowę* z mocowania danych wejściowych woluminu, na przykład *C:\CustomVoice*.
* Udostępnia port TCP 5000 i przydziela pseudo-TTY dla kontenera.
* Pobiera model podany `ModelId` (jeśli nie znajduje się na górze woluminu).
* Jeśli model niestandardowy został wcześniej `ModelId` pobrany, jest ignorowany.
* Automatycznie usuwa kontener po jego zamknięciu. Obraz kontenera jest nadal dostępny na komputerze-hoście.

***

> [!IMPORTANT]
> Aby `Eula` `Billing`uruchomić `ApiKey` kontener, należy określić opcje i opcje; w przeciwnym razie kontener nie zostanie uruchomiony.  Aby uzyskać więcej informacji, zobacz [Rozliczenia](#billing).

## <a name="query-the-containers-prediction-endpoint"></a>Kwerenda punktu końcowego przewidywania kontenera

| Containers | Adres URL hosta SDK | Protocol (Protokół) |
|--|--|--|
| Zamiana mowy na tekst i niestandardowa zamiana mowy na tekst | `ws://localhost:5000` | WS |
| Zamiana tekstu na mowę i niestandardowy tekst na mowę | `http://localhost:5000` | HTTP |

Aby uzyskać więcej informacji na temat korzystania z protokołów WSS i HTTPS, zobacz [zabezpieczenia kontenera](../cognitive-services-container-support.md#azure-cognitive-services-container-security).

[!INCLUDE [Query Speech-to-text container endpoint](includes/speech-to-text-container-query-endpoint.md)]

### <a name="text-to-speech-or-custom-text-to-speech"></a>Zamiana tekstu na mowę lub niestandardowy tekst na mowę

[!INCLUDE [Query Text-to-speech container endpoint](includes/text-to-speech-container-query-endpoint.md)]

### <a name="run-multiple-containers-on-the-same-host"></a>Uruchamianie wielu kontenerów na tym samym hoście

Jeśli zamierzasz uruchomić wiele kontenerów z narażonymi portami, upewnij się, że każdy kontener z innym portem narażonym. Na przykład uruchom pierwszy kontener na porcie 5000 i drugi kontener na porcie 5001.

Możesz mieć ten kontener i inny kontener usług Azure Cognitive Services uruchomiony na hoście razem. Można również mieć wiele kontenerów tego samego kontenera usług Cognitive Services uruchomione.

[!INCLUDE [Validate container is running - Container's API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]

## <a name="stop-the-container"></a>Zatrzymywanie kontenera

[!INCLUDE [How to stop the container](../../../includes/cognitive-services-containers-stop.md)]

## <a name="troubleshooting"></a>Rozwiązywanie problemów

Podczas uruchamiania lub uruchamiania kontenera mogą wystąpić problemy. Użyj [instalacji](speech-container-configuration.md#mount-settings) wyjściowej i włącz rejestrowanie. W ten sposób pozwoli kontenerowi na generowanie plików dziennika, które są przydatne podczas rozwiązywania problemów.

[!INCLUDE [Cognitive Services FAQ note](../containers/includes/cognitive-services-faq-note.md)]

## <a name="billing"></a>Rozliczenia

Kontenery mowy wysyłają informacje rozliczeniowe do platformy Azure przy użyciu zasobu *mowy* na koncie platformy Azure.

[!INCLUDE [Container's Billing Settings](../../../includes/cognitive-services-containers-how-to-billing-info.md)]

Aby uzyskać więcej informacji na temat tych opcji, zobacz [Konfigurowanie kontenerów](speech-container-configuration.md).

<!--blogs/samples/video courses -->

[!INCLUDE [Discoverability of more container information](../../../includes/cognitive-services-containers-discoverability.md)]

## <a name="summary"></a>Podsumowanie

W tym artykule poznaliście pojęcia i przepływ pracy do pobierania, instalowania i uruchamiania kontenerów mowy. Podsumowanie:

* Speech udostępnia cztery kontenery systemu Linux dla platformy Docker, hermetyzując różne możliwości:
  * *Zamiana mowy na tekst*
  * *Niestandardowa zamiana mowy na tekst*
  * *Zamiana tekstu na mowę*
  * *Niestandardowy tekst na mowę*
* Obrazy kontenerów są pobierane z rejestru kontenerów na platformie Azure.
* Obrazy kontenerów są uruchamiane w usłudze Docker.
* Niezależnie od tego, czy używasz interfejsu API REST (tylko zamiana tekstu na mowę), czy SDK (zamiana mowy na tekst lub Zamiana tekstu na mowę), należy określić identyfikator URI hosta kontenera. 
* Podczas tworzenia wystąpienia kontenera musisz podać informacje rozliczeniowe.

> [!IMPORTANT]
>  Kontenery usług Cognitive Services nie są licencjonowane do uruchamiania bez połączenia z platformą Azure w celu pomiaru. Klienci muszą włączyć kontenery do przekazywania informacji rozliczeniowych z usługą pomiaru przez cały czas. Kontenery usług Cognitive Services nie wysyłają danych klientów (np. obrazu lub tekstu, który jest analizowany) do firmy Microsoft.

## <a name="next-steps"></a>Następne kroki

* Przeglądanie [konfigurowania kontenerów](speech-container-configuration.md) pod kątem ustawień konfiguracji
* Dowiedz się, jak [korzystać z kontenerów usługi mowy z kubernetami i helmem](speech-container-howto-on-premises.md)
* Użyj większej liczby [kontenerów usług Cognitive Services](../cognitive-services-container-support.md)
