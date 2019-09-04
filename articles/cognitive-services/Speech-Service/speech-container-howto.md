---
title: Instalowanie kontenerów mowy — usługa mowy
titleSuffix: Azure Cognitive Services
description: Instalowanie i uruchamianie kontenerów mowy. Funkcja zamiany mowy na tekst przekształca strumienie audio do tekstu w czasie rzeczywistym, które mogą być używane przez aplikacje, narzędzia lub urządzenia. Zamiana tekstu na mowę polega na konwertowaniu tekstu wejściowego na mowę, przypominającą człowieka.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 06/19/2019
ms.author: dapine
ms.openlocfilehash: 7708133fcba0d594ecd420afd8da1b2881055aa7
ms.sourcegitcommit: 6794fb51b58d2a7eb6475c9456d55eb1267f8d40
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/04/2019
ms.locfileid: "70241014"
---
# <a name="install-and-run-speech-service-containers"></a>Instalowanie i uruchamianie kontenerów usługi mowy

Kontenery mowy umożliwiają klientom tworzenie jednej architektury aplikacji mowy, która jest zoptymalizowana pod kątem wykorzystania zarówno niezawodnej możliwości chmury, jak i lokalizacji brzegowej. 

Dwa kontenery mowy to **Zamiana mowy na tekst** i **Zamiana tekstu na mowę**. 

|Funkcja|Funkcje|Najnowsze|
|-|-|--|
|Zamiany mowy na tekst| <li>Przekształca ciągłe nagrywanie mowy w czasie rzeczywistym lub nagrania audio w trybie wsadowym do tekstu z wynikami pośrednimi.|1.2.0|
|Zamiana tekstu na mowę| <li>Konwertuje tekst na naturalnie brzmiącą mowę. za pomocą danych wejściowych tekstu lub języka Markup syntezy mowy (SSML). |1.2.0|

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="prerequisites"></a>Wymagania wstępne

Przed użyciem kontenerów mowy należy spełnić następujące wymagania wstępne:

|Wymagane|Cel|
|--|--|
|Aparat platformy Docker| Aparat platformy Docker musi być zainstalowany na [komputerze-hoście](#the-host-computer). Platforma Docker zawiera pakiety, które konfigurują środowisko platformy Docker w systemach [macOS](https://docs.docker.com/docker-for-mac/), [Windows](https://docs.docker.com/docker-for-windows/)i [Linux](https://docs.docker.com/engine/installation/#supported-platforms). Aby uzyskać podstawowe informacje na temat platformy Docker i kontenerów, zobacz [Docker — omówienie](https://docs.docker.com/engine/docker-overview/).<br><br> Docker należy skonfigurować w taki sposób, aby umożliwić kontenerów, aby nawiązać połączenie z, a następnie wysyłać danych dotyczących rozliczeń do platformy Azure. <br><br> **W systemie Windows**program Docker musi być również skonfigurowany do obsługi kontenerów systemu Linux.<br><br>|
|Znajomość platformy Docker | Należy dysponować podstawową wiedzą na temat pojęć platformy Docker, takich jak rejestry, repozytoria, kontenery i obrazy kontenerów, a także znajomość `docker` podstawowych poleceń.| 
|Zasób mowy |Aby można było korzystać z tych kontenerów, musisz mieć:<br><br>Zasób usługi Azure _Speech_ do pobrania skojarzonego klucza interfejsu API i identyfikatora URI punktu końcowego. Obie wartości są dostępne na stronach przeglądów **mowy** i kluczy Azure Portal. Są one wymagane do uruchomienia kontenera.<br><br>**{API_KEY}** : Jeden z dwóch dostępnych kluczy zasobów na stronie **klucze**<br><br>**{ENDPOINT_URI}** : Punkt końcowy zgodnie z opisem na stronie **Przegląd**|

## <a name="request-access-to-the-container-registry"></a>Zażądaj dostępu do rejestru kontenerów

Musisz najpierw zakończyć i przesłać [Cognitive Services formularz żądania kontenerów mowy](https://aka.ms/speechcontainerspreview/) , aby zażądać dostępu do kontenera. 

[!INCLUDE [Request access to the container registry](../../../includes/cognitive-services-containers-request-access-only.md)]

[!INCLUDE [Authenticate to the container registry](../../../includes/cognitive-services-containers-access-registry.md)]

## <a name="the-host-computer"></a>Komputer-host

[!INCLUDE [Host Computer requirements](../../../includes/cognitive-services-containers-host-computer.md)]

### <a name="advanced-vector-extension-support"></a>Obsługa zaawansowanych rozszerzeń wektora

**Host** to komputer, na którym jest uruchomiony kontener platformy Docker. Host musi obsługiwać [Zaawansowane rozszerzenia wektorów](https://en.wikipedia.org/wiki/Advanced_Vector_Extensions#CPUs_with_AVX2) (AVX2). Tę obsługę można sprawdzić na hostach z systemem Linux przy użyciu następującego polecenia: 

```console
grep -q avx2 /proc/cpuinfo && echo AVX2 supported || echo No AVX2 support detected
```

### <a name="container-requirements-and-recommendations"></a>Kontener wymagania i zalecenia

W poniższej tabeli opisano minimalną i zalecaną liczbę rdzeni procesora CPU oraz ilość pamięci do przydzielenia dla każdego kontenera mowy.

| Kontener | Minimalne | Zalecane |
|-----------|---------|-------------|
|poznawcze — usługi — Zamiana mowy na tekst | 2 rdzeń<br>2 GB pamięci  | 4 rdzeni<br>4 GB pamięci  |
|poznawcze — usługi — Zamiana tekstu na mowę | 1 rdzeń, 0,5 GB pamięci| 2 rdzeń, 1 GB pamięci |

* Każdy rdzeń musi mieć co najmniej 2,6 gigaherca (GHz) lub szybszy.

Rdzeń i pamięć odpowiadają `--cpus` ustawieniom i `--memory` , które są `docker run` używane jako część polecenia.

**Uwaga**; Minimalne i zalecane są oparte na limitach platformy Docker, a *nie* na zasobach maszyn hosta. Na przykład kontenera zamiany mowy na tekst mapują fragmenty modelu dużego języka i _zaleca_ się, aby cały plik mieścił się w pamięci, czyli dodatkowej 4-6 GB. Ponadto pierwsze uruchomienie jednego kontenera może trwać dłużej, ponieważ modele są stronicowane w pamięci.

## <a name="get-the-container-image-with-docker-pull"></a>Pobierz obraz kontenera za pomocą`docker pull`

Obrazy kontenerów dla mowy są dostępne.

| Kontener | Repozytorium |
|-----------|------------|
| poznawcze — usługi — Zamiana mowy na tekst | `containerpreview.azurecr.io/microsoft/cognitive-services-speech-to-text:latest` |
| poznawcze — usługi — Zamiana tekstu na mowę | `containerpreview.azurecr.io/microsoft/cognitive-services-text-to-speech:latest` |

[!INCLUDE [Tip for using docker list](../../../includes/cognitive-services-containers-docker-list-tip.md)]

### <a name="language-locale-is-in-container-tag"></a>Ustawienia regionalne języka są w znaczniku kontenera

Tag ściąga `en-us` ustawienia regionalne i `jessarus` głos. `latest`

#### <a name="speech-to-text-locales"></a>Zamiana mowy na tekst lokalne

Wszystkie Tagi, z wyjątkiem `latest` programu, są w następującym formacie, `<culture>` gdzie wskazuje Kontener ustawień regionalnych:

```
<major>.<minor>.<patch>-<platform>-<culture>-<prerelease>
```

Następujący tag jest przykładem formatu:

```
1.2.0-amd64-en-us-preview
```

Poniższa tabela zawiera listę obsługiwanych ustawień regionalnych dla **zamiany mowy na tekst** w 1.2.0 wersji kontenera:

|Ustawienia regionalne języka|`Tags`|
|--|--|
|Chiński|`zh-cn`|
|Angielski |`en-us`<br>`en-gb`<br>`en-au`<br>`en-in`|
|Francuski |`fr-ca`<br>`fr-fr`|
|Niemiecki|`de-de`|
|Włoski|`it-it`|
|Japoński|`ja-jp`|
|Koreański|`ko-kr`|
|Portugalski|`pt-br`|
|Hiszpański|`es-es`<br>`es-mx`|

#### <a name="text-to-speech-locales"></a>Tekst na ustawienia regionalne mowy

Wszystkie Tagi, z wyjątkiem `latest` programu, są w następującym formacie, `<culture>` gdzie `<voice>` wskazuje ustawienia regionalne i wskazuje głos kontenera:

```
<major>.<minor>.<patch>-<platform>-<culture>-<voice>-<prerelease>
```

Następujący tag jest przykładem formatu:

```
1.2.0-amd64-en-us-jessarus-preview
```

Poniższa tabela zawiera listę obsługiwanych ustawień regionalnych dla **zamiany tekstu na mowę** w wersji 1.2.0 kontenera:

|Ustawienia regionalne języka|`Tags`|Obsługiwane głosy|
|--|--|--|
|Chiński|`zh-cn`|huihuirus<br>kangkang-apollo<br>yaoyao-apollo|
|Angielski |`en-au`|catherine<br>hayleyrus|
|Angielski |`en-gb`|George — Apollo<br>hazelrus<br>Susan — Apollo|
|Angielski |`en-in`|heera-apollo<br>priyarus<br>ravi-apollo<br>|
|Angielski |`en-us`|jessarus<br>benjaminrus<br>jessa24krus<br>zirarus<br>guy24krus|
|Francuski|`fr-ca`|caroline<br>harmonierus|
|Francuski|`fr-fr`|hortenserus<br>julie-apollo<br>Paul-Apollo|
|niemiecki|`de-de`|hedda<br>heddarus<br>Stefan-Apollo|
|Włoski|`it-it`|cosimo-apollo<br>luciarus|
|Japoński|`ja-jp`|ayumi-apollo<br>harukarus<br>ichiro-apollo|
|Koreański|`ko-kr`|heamirus|
|Portugalski|`pt-br`|Daniel — Apollo<br>heloisarus|
|Hiszpański|`es-es`|elenarus<br>Laura — Apollo<br>pablo-apollo<br>|
|Hiszpański|`es-mx`|hildarus<br>raul-apollo|

### <a name="docker-pull-for-the-speech-containers"></a>Wypychanie platformy Docker dla kontenerów mowy

#### <a name="speech-to-text"></a>Zamiany mowy na tekst

```Docker
docker pull containerpreview.azurecr.io/microsoft/cognitive-services-speech-to-text:latest
```

#### <a name="text-to-speech"></a>Zamiana tekstu na mowę

```Docker
docker pull containerpreview.azurecr.io/microsoft/cognitive-services-text-to-speech:latest
```

## <a name="how-to-use-the-container"></a>Jak używać kontenera

Gdy kontener znajduje się na [komputerze hosta](#the-host-computer), użyj następującego procesu, aby współpracować z kontenerem.

1. [Uruchom kontener](#run-the-container-with-docker-run)z wymaganymi ustawieniami rozliczania. Więcej [przykładów](speech-container-configuration.md#example-docker-run-commands) `docker run` polecenia jest dostępnych.
1. [Zbadaj punkt końcowy przewidywania kontenera](#query-the-containers-prediction-endpoint).

## <a name="run-the-container-with-docker-run"></a>Uruchom kontener za pomocą`docker run`

Użyj polecenia [Docker Run](https://docs.docker.com/engine/reference/commandline/run/) , aby uruchomić jeden z trzech kontenerów. Polecenie używa następujących parametrów:

W trakcie korzystania z **wersji zapoznawczej**ustawienia rozliczeń muszą być prawidłowe, aby można było uruchomić kontener, ale nie są naliczane opłaty za użycie.

| Symbol zastępczy | Value |
|-------------|-------|
|{API_KEY} | Ten klucz jest używany do uruchamiania kontenera i jest dostępny na stronie klucze mowy Azure Portal.  |
|{ENDPOINT_URI} | Wartość identyfikatora URI punktu końcowego rozliczenia jest dostępna na stronie Przegląd mowy Azure Portal.|

Zastąp te parametry własnymi wartościami w poniższym przykładowym `docker run` poleceniu.

### <a name="text-to-speech"></a>Zamiana tekstu na mowę

```bash
docker run --rm -it -p 5000:5000 --memory 2g --cpus 1 \
containerpreview.azurecr.io/microsoft/cognitive-services-text-to-speech \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

### <a name="speech-to-text"></a>Zamiany mowy na tekst

```bash
docker run --rm -it -p 5000:5000 --memory 2g --cpus 2 \
containerpreview.azurecr.io/microsoft/cognitive-services-speech-to-text \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

To polecenie:

* Uruchamia kontener mowy z obrazu kontenera
* Przydziela 2 rdzenie procesora CPU i 2 gigabajty (GB) pamięci
* Uwidacznia TCP port 5000 i przydziela pseudo-TTY kontenera
* Automatycznie usuwa kontener po zakończeniu. Obraz kontenera jest nadal dostępny na komputerze-hoście.

> [!IMPORTANT]
> `Eula`, `Billing`, I `ApiKey` opcje muszą być określone w celu uruchomienia kontenera; w przeciwnym razie nie uruchamia się kontener.  Aby uzyskać więcej informacji, zobacz [rozliczeń](#billing).

## <a name="query-the-containers-prediction-endpoint"></a>Zbadaj punkt końcowy przewidywania kontenera

|Kontener|Endpoint|
|--|--|
|Zamiany mowy na tekst|ws://localhost:5000/speech/recognition/dictation/cognitiveservices/v1|
|Zamiana tekstu na mowę|http://localhost:5000/speech/synthesize/cognitiveservices/v1|

### <a name="speech-to-text"></a>Zamiany mowy na tekst

Kontener udostępnia interfejsy API punktu końcowego zapytania opartego na protokole WebSocket, do których można uzyskać dostęp za pośrednictwem [zestawu Speech SDK](index.yml).

Domyślnie zestaw Speech SDK używa usług online Speech Services. Aby użyć kontenera, należy zmienić metodę inicjacji. Zapoznaj się z poniższymi przykładami.

#### <a name="for-c"></a>DlaC#

Zmień użycie tego wywołania inicjalizacji Azure-Cloud:

```csharp
var config = SpeechConfig.FromSubscription("YourSubscriptionKey", "YourServiceRegion");
```

do tego wywołania przy użyciu punktu końcowego kontenera:

```csharp
var config = SpeechConfig.FromEndpoint(
    new Uri("ws://localhost:5000/speech/recognition/dictation/cognitiveservices/v1"),
    "YourSubscriptionKey");
```

#### <a name="for-python"></a>Dla języka Python

Zmień użycie tego wywołania inicjalizacji Azure-Cloud

```python
speech_config = speechsdk.SpeechConfig(
    subscription=speech_key, region=service_region)
```

do tego wywołania przy użyciu punktu końcowego kontenera:

```python
speech_config = speechsdk.SpeechConfig(
    subscription=speech_key, endpoint="ws://localhost:5000/speech/recognition/dictation/cognitiveservices/v1")
```

### <a name="text-to-speech"></a>Zamiana tekstu na mowę

Kontener zawiera interfejsy API punktu końcowego REST, które można znaleźć [tutaj](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-text-to-speech) , a przykłady można znaleźć [tutaj](https://azure.microsoft.com/resources/samples/cognitive-speech-tts/).

[!INCLUDE [Validate container is running - Container's API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]

## <a name="stop-the-container"></a>Zatrzymaj kontener

[!INCLUDE [How to stop the container](../../../includes/cognitive-services-containers-stop.md)]

## <a name="troubleshooting"></a>Rozwiązywanie problemów

Po uruchomieniu kontenera kontener używa **stdout** i **stderr** do informacji wyjściowych, które są przydatne do rozwiązywania problemów występujących podczas uruchamiania lub uruchamiania kontenera.

## <a name="billing"></a>Rozliczenia

Kontenery mowy wysyłają informacje o rozliczeniach do platformy Azure przy użyciu zasobu _mowy_ na koncie platformy Azure.

[!INCLUDE [Container's Billing Settings](../../../includes/cognitive-services-containers-how-to-billing-info.md)]

Aby uzyskać więcej informacji o tych opcjach, zobacz [skonfigurować kontenery](speech-container-configuration.md).

<!--blogs/samples/video coures -->

[!INCLUDE [Discoverability of more container information](../../../includes/cognitive-services-containers-discoverability.md)]

## <a name="summary"></a>Podsumowanie

W tym artykule przedstawiono koncepcje i przepływ pracy służące do pobierania, instalowania i uruchamiania kontenerów mowy. Podsumowanie:

* Funkcja rozpoznawania mowy oferuje dwa kontenery systemu Linux dla platformy Docker, hermetyzując mowę na tekst i tekst na mowę.
* Obrazy kontenerów są pobierane z prywatnego rejestru kontenerów na platformie Azure.
* Obrazy kontenera Uruchom na platformie Docker.
* Można użyć interfejsu API REST lub zestawu SDK do wywoływania operacji w kontenerach mowy przez określenie identyfikatora URI hosta kontenera.
* Podczas tworzenia wystąpienia kontenera wymagane jest podanie informacji dotyczących rozliczeń.

> [!IMPORTANT]
>  Kontenery usługi cognitive Services nie są licencjonowane do uruchomienia bez połączenia z platformy Azure do zbierania danych. Klienci muszą włączyć kontener, aby komunikować informacje rozliczeniowe usłudze zliczania przez cały czas. Kontenery usługi cognitive Services nie wysyłaj danych klientów (np. obraz lub tekst, który jest analizowana) do firmy Microsoft.

## <a name="next-steps"></a>Kolejne kroki

* Przegląd [skonfigurować kontenery](speech-container-configuration.md) ustawień konfiguracji
* Użyj więcej [kontenerów Cognitive Services](../cognitive-services-container-support.md)
