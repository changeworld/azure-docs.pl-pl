---
title: Instalowanie i uruchamianie kontenerów — analiza tekstu
titleSuffix: Azure Cognitive Services
description: Jak pobrać, zainstalować i uruchamiaj kontenery analizy tekstu, w tym samouczku wskazówki.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 07/30/2019
ms.author: dapine
ms.openlocfilehash: ff0be2e9dada758cce96ba7c5eebbf03b00f56c6
ms.sourcegitcommit: 47b00a15ef112c8b513046c668a33e20fd3b3119
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/22/2019
ms.locfileid: "69971443"
---
# <a name="install-and-run-text-analytics-containers"></a>Instalowanie i uruchamianie kontenerów analiza tekstu

Kontenery umożliwiają uruchamianie interfejsów API analizy tekstu we własnym środowisku i są doskonałe dla konkretnych wymagań dotyczących zabezpieczeń i zarządzania danymi. Kontenery analiza tekstu zapewniają zaawansowane przetwarzanie języka naturalnego w przypadku nieprzetworzonego tekstu i zawierają trzy główne funkcje: tonacji Analysis, wyodrębnianie kluczowych fraz i wykrywanie języka. Łączenie jednostek nie jest obecnie obsługiwane w kontenerze.

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="prerequisites"></a>Wymagania wstępne

Aby uruchomić dowolny z kontenerów analiza tekstu, należy mieć komputer hosta i środowiska kontenerów.

## <a name="preparation"></a>Przygotowanie

Przed rozpoczęciem korzystania z kontenerów analizy tekstu, musi spełniać następujące wymagania wstępne:

|Wymagane|Cel|
|--|--|
|Aparat platformy Docker| Aparat platformy Docker musi być zainstalowany na [komputerze-hoście](#the-host-computer). Platforma Docker zawiera pakiety, które konfigurują środowisko platformy Docker w systemach [macOS](https://docs.docker.com/docker-for-mac/), [Windows](https://docs.docker.com/docker-for-windows/)i [Linux](https://docs.docker.com/engine/installation/#supported-platforms). Aby uzyskać podstawowe informacje na temat platformy Docker i kontenerów, zobacz [Docker — omówienie](https://docs.docker.com/engine/docker-overview/).<br><br> Docker należy skonfigurować w taki sposób, aby umożliwić kontenerów, aby nawiązać połączenie z, a następnie wysyłać danych dotyczących rozliczeń do platformy Azure. <br><br> **W systemie Windows**program Docker musi być również skonfigurowany do obsługi kontenerów systemu Linux.<br><br>|
|Znajomość platformy Docker | Należy dysponować podstawową wiedzą na temat pojęć platformy Docker, takich jak rejestry, repozytoria, kontenery i obrazy kontenerów, a także znajomość `docker` podstawowych poleceń.| 
|Zasób analiza tekstu |Aby można było używać kontenera, musisz mieć:<br><br>[Zasób analiza tekstu](../../cognitive-services-apis-create-account.md) platformy Azure, aby pobrać skojarzony klucz interfejsu API i identyfikator URI punktu końcowego. Obie wartości są dostępne na stronie Przegląd analiza tekstu Azure Portal i klucze i są wymagane do uruchomienia kontenera.<br><br>**{API_KEY}** : Jeden z dwóch dostępnych kluczy zasobów na stronie **klucze**<br><br>**{ENDPOINT_URI}** : Punkt końcowy zgodnie z opisem na stronie **Przegląd**|

[!INCLUDE [Gathering required parameters](../../containers/includes/container-gathering-required-parameters.md)]

### <a name="the-host-computer"></a>Komputer-host

[!INCLUDE [Host Computer requirements](../../../../includes/cognitive-services-containers-host-computer.md)]

### <a name="container-requirements-and-recommendations"></a>Kontener wymagania i zalecenia

W poniższej tabeli przedstawiono minimalne i zalecane rdzeni procesora CPU, co najmniej 2,6 gigaherc (GHz) lub szybszy i pamięci w gigabajtach (GB) do przydzielenia dla każdego kontenera analizy tekstu.

| Kontener | Minimalne | Zalecane | TPS<br>(Minimum, maksimum)|
|-----------|---------|-------------|--|
|Wyodrębnianie kluczowych fraz | 1 rdzeń, 2 GB pamięci | 1 rdzeń, 4 GB pamięci |15, 30|
|Wykrywanie języka | 1 rdzeń, 2 GB pamięci | 1 rdzeń, 4 GB pamięci |15, 30|
|Analiza tonacji | 1 rdzeń, 2 GB pamięci | 1 rdzeń, 4 GB pamięci |15, 30|

* Każdy rdzeń musi mieć co najmniej 2,6 gigaherca (GHz) lub szybszy.
* TPS — liczba transakcji na sekundę

Rdzeń i pamięć odpowiadają `--cpus` ustawieniom i `--memory` , które są `docker run` używane jako część polecenia.

## <a name="get-the-container-image-with-docker-pull"></a>Pobierz obraz kontenera za pomocą`docker pull`

Obrazy kontenerów dla analizy tekstu są dostępne z rejestru kontenerów firmy Microsoft.

| Kontener | Repozytorium |
|-----------|------------|
|Wyodrębnianie kluczowych fraz | `mcr.microsoft.com/azure-cognitive-services/keyphrase` |
|Wykrywanie języka | `mcr.microsoft.com/azure-cognitive-services/language` |
|Analiza tonacji| `mcr.microsoft.com/azure-cognitive-services/sentiment` |

Użyj polecenia [`docker pull`](https://docs.docker.com/engine/reference/commandline/pull/) , aby pobrać obraz kontenera z programu Microsoft Container Registry.

Pełny opis dostępnych tagów dla kontenerów analizy tekstu zobacz następujące kontenery w usłudze Docker Hub:

* [Wyodrębnianie kluczowych fraz](https://go.microsoft.com/fwlink/?linkid=2018757)
* [Wykrywanie języka](https://go.microsoft.com/fwlink/?linkid=2018759)
* [Analiza tonacji](https://go.microsoft.com/fwlink/?linkid=2018654)

Użyj polecenia [`docker pull`](https://docs.docker.com/engine/reference/commandline/pull/) , aby pobrać obraz kontenera.

### <a name="docker-pull-for-the-key-phrase-extraction-container"></a>Wypychanie platformy Docker dla kontenera wyodrębniania fraz klucza

```
docker pull mcr.microsoft.com/azure-cognitive-services/keyphrase:latest
```

### <a name="docker-pull-for-the-language-detection-container"></a>Wypychanie platformy Docker dla kontenera wykrywania języka

```
docker pull mcr.microsoft.com/azure-cognitive-services/language:latest
```

### <a name="docker-pull-for-the-sentiment-container"></a>Wypychanie platformy Docker dla kontenera tonacji

```
docker pull mcr.microsoft.com/azure-cognitive-services/sentiment:latest
```

[!INCLUDE [Tip for using docker list](../../../../includes/cognitive-services-containers-docker-list-tip.md)]

## <a name="how-to-use-the-container"></a>Jak używać kontenera

Gdy kontener znajduje się na [komputerze hosta](#the-host-computer), użyj następującego procesu, aby współpracować z kontenerem.

1. [Uruchom kontener](#run-the-container-with-docker-run)z wymaganymi ustawieniami rozliczania. Więcej [przykładów](../text-analytics-resource-container-config.md#example-docker-run-commands) `docker run` polecenia jest dostępnych.
1. [Zbadaj punkt końcowy przewidywania kontenera](#query-the-containers-prediction-endpoint).

## <a name="run-the-container-with-docker-run"></a>Uruchom kontener za pomocą`docker run`

Użyj polecenia [Docker Run](https://docs.docker.com/engine/reference/commandline/run/) , aby uruchomić jeden z trzech kontenerów. Zapoznaj się z tematem [zbieranie wymaganych parametrów](#gathering-required-parameters) `{Endpoint_URI}` , aby uzyskać szczegółowe `{API_Key}` informacje na temat sposobu pobierania wartości i.

Przykłady polecenia są dostępne. [](../text-analytics-resource-container-config.md#example-docker-run-commands) `docker run`

### <a name="run-container-example-of-docker-run-command"></a>Przykład uruchomienia kontenera dla polecenia Docker Run

```bash
docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 \
mcr.microsoft.com/azure-cognitive-services/keyphrase \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

To polecenie:

* Uruchamia kontener klucza frazy z obrazu kontenera
* Przypisuje jedne rdzeń procesora CPU i 4 gigabajty (GB) pamięci
* Uwidacznia TCP port 5000 i przydziela pseudo-TTY kontenera
* Automatycznie usuwa kontener po zakończeniu. Obraz kontenera jest nadal dostępny na komputerze-hoście.


> [!IMPORTANT]
> `Eula`, `Billing`, I `ApiKey` opcje muszą być określone w celu uruchomienia kontenera; w przeciwnym razie nie uruchamia się kontener.  Aby uzyskać więcej informacji, zobacz [rozliczeń](#billing).

[!INCLUDE [Running multiple containers on the same host](../../../../includes/cognitive-services-containers-run-multiple-same-host.md)]

## <a name="query-the-containers-prediction-endpoint"></a>Zbadaj punkt końcowy przewidywania kontenera

Kontener udostępnia interfejsy API punktu końcowego przewidywania zapytań.

Użyj hosta, `http://localhost:5000`, dla interfejsów API kontenerów.

<!--  ## Validate container is running -->

[!INCLUDE [Container's API documentation](../../../../includes/cognitive-services-containers-api-documentation.md)]

## <a name="stop-the-container"></a>Zatrzymaj kontener

[!INCLUDE [How to stop the container](../../../../includes/cognitive-services-containers-stop.md)]

## <a name="troubleshooting"></a>Rozwiązywanie problemów

Jeśli uruchamiasz kontener z instalacją wyjściową [](../text-analytics-resource-container-config.md#mount-settings) i włączono rejestrowanie, kontener generuje pliki dziennika, które są przydatne do rozwiązywania problemów występujących podczas uruchamiania lub uruchamiania kontenera.

## <a name="billing"></a>Rozliczenia

Kontenery analiza tekstu wysyłają informacje o rozliczeniach do platformy Azure przy użyciu zasobu _Analiza tekstu_ na koncie platformy Azure. 

[!INCLUDE [Container's Billing Settings](../../../../includes/cognitive-services-containers-how-to-billing-info.md)]

Aby uzyskać więcej informacji o tych opcjach, zobacz [skonfigurować kontenery](../text-analytics-resource-container-config.md).

<!--blogs/samples/video course -->

[!INCLUDE [Discoverability of more container information](../../../../includes/cognitive-services-containers-discoverability.md)]

## <a name="summary"></a>Podsumowanie

W tym artykule przedstawiono pojęcia i przepływ pracy na potrzeby pobierania, instalowania i uruchamiania kontenerów analizy tekstu. Podsumowanie:

* Analiza tekstu zawiera trzy kontenery systemu Linux dla platformy Docker, zawieranie, wyodrębnianie kluczowych fraz, wykrywanie języka i analiza opinii.
* Obrazy kontenerów są pobierane z rejestru kontenerów firmy Microsoft (MCR) na platformie Azure.
* Obrazy kontenera Uruchom na platformie Docker.
* Można użyć interfejsu API REST lub zestawu SDK do wywoływania operacji w kontenerach analizy tekstu, określając host identyfikatora URI kontenera.
* Należy określić informacje rozliczeniowe, podczas tworzenia wystąpienia kontenera.

> [!IMPORTANT]
> Kontenery usługi cognitive Services nie są licencjonowane do uruchomienia bez połączenia z platformy Azure do zbierania danych. Klienci muszą włączyć kontener, aby komunikować informacje rozliczeniowe usłudze zliczania przez cały czas. Kontenery usługi cognitive Services nie wysyłaj danych klientów (np. obraz lub tekst, który jest analizowana) do firmy Microsoft.

## <a name="next-steps"></a>Kolejne kroki

* Przegląd [skonfigurować kontenery](../text-analytics-resource-container-config.md) ustawień konfiguracji
* Zapoznaj się z [często](../text-analytics-resource-faq.md) zadawanymi pytaniami, aby rozwiązać problemy związane z działaniem.
