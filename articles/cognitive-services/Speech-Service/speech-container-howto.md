---
title: Zainstaluj kontenery mowy
titleSuffix: Azure Cognitive Services
description: Instalowanie i uruchamianie kontenerów mowy. Zamiany mowy na tekst transcribes strumieni audio na tekst w czasie rzeczywistym, które aplikacje, narzędzia lub urządzenia używają lub wyświetlić. Zamiana tekstu na mowę konwertuje tekst wejściowy przypominającej ludzką syntezatora mowy.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 06/19/2019
ms.author: dapine
ms.openlocfilehash: 0fe30bc6bdd1bba03df349305d8846e78063bd26
ms.sourcegitcommit: a12b2c2599134e32a910921861d4805e21320159
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/24/2019
ms.locfileid: "67342209"
---
# <a name="install-and-run-speech-service-containers"></a>Instalowanie i uruchamianie kontenerów usługi mowy

Kontenery mowy Umożliwiaj klientom tworzenie jednego Architektura aplikacji mowy, zoptymalizowane pod kątem wykorzystać możliwości chmury niezawodne i lokalizacja krawędzi. 

Kontenery mowy dwa są **mowy na tekst** i **zamiany tekstu na mowę**. 

|Funkcja|Funkcje|najnowsze|
|-|-|--|
|Zamiany mowy na tekst| <li>Transcribes ciągłej w czasie rzeczywistym mowy lub partii nagrania audio na tekst za pomocą wyników pośrednich.|1.1.1|
|Zamiana tekstu na mowę| <li>Konwertuje tekst na naturalnie brzmiącą mowę. dane wejściowe w postaci zwykłego tekstu lub mowy syntezy Markup Language (SSML). |1.1.0|

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem korzystania z kontenerów mowy, musi spełniać następujące wymagania wstępne:

|Wymagane|Przeznaczenie|
|--|--|
|Aparat platformy docker| Aparat platformy Docker zainstalowany na musisz [komputerze-hoście](#the-host-computer). Środowisko docker zawiera pakiety, które konfigurują środowisko platformy Docker na [macOS](https://docs.docker.com/docker-for-mac/), [Windows](https://docs.docker.com/docker-for-windows/), i [Linux](https://docs.docker.com/engine/installation/#supported-platforms). Aby uzyskać podstawowe informacje na temat platformy Docker i kontenerów, zobacz [Docker — omówienie](https://docs.docker.com/engine/docker-overview/).<br><br> Docker należy skonfigurować w taki sposób, aby umożliwić kontenerów, aby nawiązać połączenie z, a następnie wysyłać danych dotyczących rozliczeń do platformy Azure. <br><br> **Na Windows**, platformy Docker musi być również skonfigurowany do obsługi kontenerów systemu Linux.<br><br>|
|Znajomość platformy Docker | Należy mieć podstawową wiedzę na temat pojęć usługi Docker, takich jak rejestry, repozytoria, kontenery i obrazów kontenerów, a także wiedzę na temat basic `docker` poleceń.| 
|Zasób mowy |Aby można było używać tych kontenerów, musisz mieć:<br><br>A _mowy_ zasobów platformy Azure, aby uzyskać skojarzonego klucza rozliczenia i rozliczeń identyfikator URI punktu końcowego. Obie wartości są dostępne w witrynie Azure portal **mowy** strony Przegląd i klucze i są wymagane do uruchomienia kontenera.<br><br>**{BILLING_KEY}** : klucz zasobu<br><br>**{BILLING_ENDPOINT_URI}** : przykład identyfikatora URI punktu końcowego: `https://westus.api.cognitive.microsoft.com/sts/v1.0`|

## <a name="request-access-to-the-container-registry"></a>Żądanie dostępu do rejestru kontenerów

Najpierw należy wypełnić oraz przesłać [formularz wniosku kontenery mowy w usłudze Cognitive Services](https://aka.ms/speechcontainerspreview/) Aby zażądać dostępu do kontenera. 

[!INCLUDE [Request access to the container registry](../../../includes/cognitive-services-containers-request-access-only.md)]

[!INCLUDE [Authenticate to the container registry](../../../includes/cognitive-services-containers-access-registry.md)]

## <a name="the-host-computer"></a>Komputer-host

[!INCLUDE [Host Computer requirements](../../../includes/cognitive-services-containers-host-computer.md)]

### <a name="advanced-vector-extension-support"></a>Zaawansowana obsługa rozszerzenia wektorów

**Hosta** komputer, który uruchamia kontener platformy docker. Host musi obsługiwać [Advanced Vector Extensions](https://en.wikipedia.org/wiki/Advanced_Vector_Extensions#CPUs_with_AVX2) (AVX2). Możesz sprawdzić tej obsługi na hostach z systemem Linux za pomocą następującego polecenia: 

```console
grep -q avx2 /proc/cpuinfo && echo AVX2 supported || echo No AVX2 support detected
```

### <a name="container-requirements-and-recommendations"></a>Kontener wymagania i zalecenia

W poniższej tabeli opisano minimalne i zalecane rdzeni procesora CPU i pamięci do przydzielenia dla każdego kontenera mowy.

| Kontener | Minimalne | Zalecane |
|-----------|---------|-------------|
|cognitive-services-speech-to-text | 2 rdzenie<br>2 GB pamięci  | 4 rdzenie<br>4 GB pamięci RAM  |
|cognitive-services-text-to-speech | 1 rdzeń, 0,5 GB pamięci RAM| 2 rdzeni, 1 GB pamięci |

* Każdego rdzenia musi mieć co najmniej 2,6 gigaherc (GHz) lub szybszy.

Rdzeni i pamięci odpowiadają `--cpus` i `--memory` ustawienia, które są używane jako część `docker run` polecenia.

**Uwaga**; Minimalne i zalecane opierają się poza granice Docker *nie* host machine zasobów. Na przykład kontenery mowy na tekst pamięci mapy fragmentów modelu języka dużych, a to _zalecane_ cały plik mieści się w pamięci, co jest dodatkowe 4 – 6 GB. Ponadto przy pierwszym uruchomieniu dowolnego kontenera może trwać dłużej, ponieważ modele są są stronicowane do pamięci.

## <a name="get-the-container-image-with-docker-pull"></a>Pobierz obraz kontenera przy użyciu `docker pull`

Dostępne są obrazy kontenerów dla rozpoznawania mowy.

| Kontener | Repozytorium |
|-----------|------------|
| cognitive-services-speech-to-text | `containerpreview.azurecr.io/microsoft/cognitive-services-speech-to-text:latest` |
| cognitive-services-text-to-speech | `containerpreview.azurecr.io/microsoft/cognitive-services-text-to-speech:latest` |

[!INCLUDE [Tip for using docker list](../../../includes/cognitive-services-containers-docker-list-tip.md)]

### <a name="language-locale-is-in-container-tag"></a>Ustawienia regionalne język znajduje się w tagu kontenera

`latest` Tag ściąga `en-us` ustawień regionalnych i `jessarus` głosu.

#### <a name="speech-to-text-locales"></a>Zamiana mowy na tekst w ustawieniach regionalnych

Wszystkie tagi, z wyjątkiem `latest` znajdują się w następującym formacie, gdzie `<culture>` wskazuje kontener ustawień regionalnych:

```
<major>.<minor>.<patch>-<platform>-<culture>-<prerelease>
```

Następujący tag znajduje się przykład formatu:

```
1.0.0-amd64-en-us-preview
```

Poniższa tabela zawiera listę obsługiwanych ustawień regionalnych dla **mowy na tekst** 1.1.1 wersję kontenera:

|Językowe ustawienia regionalne|`Tags`|
|--|--|
|Chiński|`zh-cn`|
|Polski |`en-us`<br>`en-gb`<br>`en-au`<br>`en-in`|
|Francuski |`fr-ca`<br>`fr-fr`|
|Niemiecki|`de-de`|
|Włoski|`it-it`|
|Japoński|`ja-jp`|
|Koreański|`ko-kr`|
|Portugalski|`pt-br`|
|Hiszpański|`es-es`<br>`es-mx`|

#### <a name="text-to-speech-locales"></a>Ustawienia regionalne zamiany tekstu na mowę

Wszystkie tagi, z wyjątkiem `latest` znajdują się w następującym formacie, gdzie `<culture>` wskazuje ustawień regionalnych i `<voice>` wskazuje głosu kontenera:

```
<major>.<minor>.<patch>-<platform>-<culture>-<voice>-<prerelease>
```

Następujący tag znajduje się przykład formatu:

```
1.0.0-amd64-en-us-jessarus-preview
```

Poniższa tabela zawiera listę obsługiwanych ustawień regionalnych dla **zamiany tekstu na mowę** w 1.1.0 wersję kontenera:

|Językowe ustawienia regionalne|`Tags`|Obsługiwane głosów|
|--|--|--|
|Chiński|`zh-cn`|huihuirus<br>kangkang-apollo<br>yaoyao-apollo|
|Polski |`en-au`|catherine<br>hayleyrus|
|Polski |`en-gb`|george-apollo<br>hazelrus<br>susan apollo|
|Polski |`en-in`|heera apollo<br>priyarus<br>ravi apollo<br>|
|Polski |`en-us`|jessarus<br>benjaminrus<br>jessa24krus<br>zirarus<br>guy24krus|
|Francuski|`fr-ca`|caroline<br>harmonierus|
|Francuski|`fr-fr`|hortenserus<br>julie-apollo<br>paul-apollo|
|Niemiecki|`de-de`|hedda<br>heddarus<br>Stefan apollo|
|Włoski|`it-it`|cosimo apollo<br>luciarus|
|Japoński|`ja-jp`|ayumi apollo<br>harukarus<br>ichiro apollo|
|Koreański|`ko-kr`|heamirus|
|Portugalski|`pt-br`|daniel-apollo<br>heloisarus|
|Hiszpański|`es-es`|elenarus<br>Laura apollo<br>urządzenia Pablo apollo<br>|
|Hiszpański|`es-mx`|hildarus<br>raul-apollo|

### <a name="docker-pull-for-the-speech-containers"></a>Polecenie docker pull dla kontenerów mowy

#### <a name="speech-to-text"></a>Zamiany mowy na tekst

```Docker
docker pull containerpreview.azurecr.io/microsoft/cognitive-services-speech-to-text:latest
```

#### <a name="text-to-speech"></a>Zamiana tekstu na mowę

```Docker
docker pull containerpreview.azurecr.io/microsoft/cognitive-services-text-to-speech:latest
```

## <a name="how-to-use-the-container"></a>Jak używać kontenera

Gdy kontener będzie na [komputerze-hoście](#the-host-computer), użyj następującego procesu do pracy z kontenerem.

1. [Uruchom kontener](#run-the-container-with-docker-run), przy użyciu ustawień rozliczeń wymagany, lecz nie jest używany. Więcej [przykłady](speech-container-configuration.md#example-docker-run-commands) z `docker run` polecenia są dostępne.
1. [Zapytanie do endpoint prognoz kontenera](#query-the-containers-prediction-endpoint).

## <a name="run-the-container-with-docker-run"></a>Uruchom kontener za pomocą `docker run`

Użyj [platformy docker, uruchom](https://docs.docker.com/engine/reference/commandline/run/) polecenie, aby uruchomić dowolne trzy kontenery. Polecenie używa następujących parametrów:

**W trakcie okresu zapoznawczego**, ustawienia rozliczeń musi być ważny do uruchamianie kontenera, ale nie są naliczane za użycie.

| Symbol zastępczy | Wartość |
|-------------|-------|
|{BILLING_KEY} | Ten klucz jest używany do uruchamiania kontenera i jest dostępny w witrynie Azure portal, strona klucze mowy.  |
|{BILLING_ENDPOINT_URI} | Punkt końcowy rozliczeń wartość identyfikatora URI jest dostępna na stronie Przegląd rozpoznawania mowy w witrynie Azure portal.|

Zastąp parametry te wartości w poniższym przykładzie `docker run` polecenia.

### <a name="text-to-speech"></a>Zamiana tekstu na mowę

```bash
docker run --rm -it -p 5000:5000 --memory 2g --cpus 1 \
containerpreview.azurecr.io/microsoft/cognitive-services-text-to-speech \
Eula=accept \
Billing={BILLING_ENDPOINT_URI} \
ApiKey={BILLING_KEY}
```

### <a name="speech-to-text"></a>Zamiany mowy na tekst

```bash
docker run --rm -it -p 5000:5000 --memory 2g --cpus 2 \
containerpreview.azurecr.io/microsoft/cognitive-services-speech-to-text \
Eula=accept \
Billing={BILLING_ENDPOINT_URI} \
ApiKey={BILLING_KEY}
```

To polecenie:

* Uruchamia kontener mowy z obrazu kontenera
* Przydziela 2 rdzeni procesora CPU i 2 gigabajty (GB) pamięci
* Uwidacznia TCP port 5000 i przydziela pseudo-TTY kontenera
* Automatycznie usuwa kontener po jej zakończenia. Obraz kontenera jest nadal dostępna na komputerze-hoście.

> [!IMPORTANT]
> `Eula`, `Billing`, I `ApiKey` opcje muszą być określone w celu uruchomienia kontenera; w przeciwnym razie nie uruchamia się kontener.  Aby uzyskać więcej informacji, zobacz [rozliczeń](#billing).

## <a name="query-the-containers-prediction-endpoint"></a>Zapytanie do endpoint prognoz kontenera

|Kontener|Endpoint|
|--|--|
|Zamiany mowy na tekst|ws://localhost:5000/speech/recognition/dictation/cognitiveservices/v1|
|Zamiana tekstu na mowę|http://localhost:5000/speech/synthesize/cognitiveservices/v1|

### <a name="speech-to-text"></a>Zamiany mowy na tekst

Kontener udostępnia punkt końcowy zapytań w oparciu o websocket, interfejsów API, które są dostępne za pośrednictwem [zestaw SDK rozpoznawania mowy](index.yml).

Domyślnie zestaw SDK rozpoznawania mowy używa usług rozpoznawania mowy w trybie online. Aby korzystać z kontenera, musisz zmienić metodę inicjowania. Zobacz przykłady poniżej.

#### <a name="for-c"></a>For C#

Zmień z przy użyciu tego wywołania inicjowania w chmurze platformy Azure:

```C#
var config = SpeechConfig.FromSubscription("YourSubscriptionKey", "YourServiceRegion");
```

Aby to wywołanie, przy użyciu punktu końcowego kontenera:

```C#
var config = SpeechConfig.FromEndpoint(
    new Uri("ws://localhost:5000/speech/recognition/dictation/cognitiveservices/v1"),
    "YourSubscriptionKey");
```

#### <a name="for-python"></a>Dla języka Python

Zmiana z przy użyciu tego wywołania inicjowania w chmurze platformy Azure

```python
speech_config = speechsdk.SpeechConfig(
    subscription=speech_key, region=service_region)
```

Aby to wywołanie, przy użyciu punktu końcowego kontenera:

```python
speech_config = speechsdk.SpeechConfig(
    subscription=speech_key, endpoint="ws://localhost:5000/speech/recognition/dictation/cognitiveservices/v1")
```

### <a name="text-to-speech"></a>Zamiana tekstu na mowę

Kontener zawiera punkt końcowy REST API, który można znaleźć [tutaj](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-text-to-speech) i przykładów można znaleźć [tutaj](https://azure.microsoft.com/resources/samples/cognitive-speech-tts/).

[!INCLUDE [Validate container is running - Container's API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]

## <a name="stop-the-container"></a>Zastavit kontejner

[!INCLUDE [How to stop the container](../../../includes/cognitive-services-containers-stop.md)]

## <a name="troubleshooting"></a>Rozwiązywanie problemów

Po uruchomieniu kontenera, korzysta z kontenera **stdout** i **stderr** o danych wyjściowych, które są przydatne do rozwiązywania problemów, które wystąpiło podczas uruchamianie kontenera.

## <a name="billing"></a>Rozliczenia

Wyślij kontenery mowy, rozliczeń, informacje na platformie Azure, przy użyciu _mowy_ zasobów dla konta systemu Azure.

[!INCLUDE [Container's Billing Settings](../../../includes/cognitive-services-containers-how-to-billing-info.md)]

Aby uzyskać więcej informacji o tych opcjach, zobacz [skonfigurować kontenery](speech-container-configuration.md).

<!--blogs/samples/video coures -->

[!INCLUDE [Discoverability of more container information](../../../includes/cognitive-services-containers-discoverability.md)]

## <a name="summary"></a>Podsumowanie

W tym artykule przedstawiono pojęcia i przepływ pracy na potrzeby pobierania, instalowania i uruchamiania kontenerów mowy. Podsumowanie:

* Mowy udostępnia dwóch kontenerów systemu Linux dla platformy Docker, zawieranie zamiana mowy na tekst i zamiany tekstu na mowę.
* Obrazy kontenerów są pobierane z prywatnego rejestru kontenerów na platformie Azure.
* Obrazy kontenera Uruchom na platformie Docker.
* Można użyć interfejsu API REST lub zestawu SDK do wywoływania operacji w kontenerach mowy, określając host identyfikatora URI kontenera.
* Należy określić informacje rozliczeniowe, podczas tworzenia wystąpienia kontenera.

> [!IMPORTANT]
>  Kontenery usługi cognitive Services nie są licencjonowane do uruchomienia bez połączenia z platformy Azure do zbierania danych. Klienci muszą włączyć kontener, aby komunikować informacje rozliczeniowe usłudze zliczania przez cały czas. Kontenery usługi cognitive Services nie wysyłaj danych klientów (np. obraz lub tekst, który jest analizowana) do firmy Microsoft.

## <a name="next-steps"></a>Kolejne kroki

* Przegląd [skonfigurować kontenery](speech-container-configuration.md) ustawień konfiguracji
* Użycie [kontenerów usługi Cognitive Services](../cognitive-services-container-support.md)
