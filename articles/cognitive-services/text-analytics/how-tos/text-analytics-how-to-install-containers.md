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
ms.date: 11/21/2019
ms.author: dapine
ms.openlocfilehash: 6e05dc2136211bcd15a9f0583358b05ccbf96f5a
ms.sourcegitcommit: f523c8a8557ade6c4db6be12d7a01e535ff32f32
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/22/2019
ms.locfileid: "74383157"
---
# <a name="install-and-run-text-analytics-containers"></a>Instalowanie i uruchamianie kontenerów analizy tekstu

Kontenery umożliwiają uruchamianie interfejsów API analizy tekstu we własnym środowisku i są doskonałe dla konkretnych wymagań dotyczących zabezpieczeń i zarządzania danymi. Kontenery analiza tekstu zapewniają zaawansowane przetwarzanie języka naturalnego w przypadku nieprzetworzonego tekstu i zawierają trzy główne funkcje: tonacji Analysis, wyodrębnianie kluczowych fraz i wykrywanie języka. Łączenie jednostek nie jest obecnie obsługiwane w kontenerze.

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="prerequisites"></a>Wymagania wstępne

Aby uruchomić dowolny z kontenerów analiza tekstu, należy mieć komputer hosta i środowiska kontenerów.

## <a name="preparation"></a>Przygotowanie

Przed rozpoczęciem korzystania z kontenerów analizy tekstu, musi spełniać następujące wymagania wstępne:

|Wymagany|Przeznaczenie|
|--|--|
|Aparat platformy Docker| Aparat platformy Docker musi być zainstalowany na [komputerze-hoście](#the-host-computer). Platforma Docker zawiera pakiety, które konfigurują środowisko platformy Docker w systemach [macOS](https://docs.docker.com/docker-for-mac/), [Windows](https://docs.docker.com/docker-for-windows/)i [Linux](https://docs.docker.com/engine/installation/#supported-platforms). Podstawowe informacje dotyczące platformy Docker i kontenera można znaleźć w temacie [Omówienie platformy Docker](https://docs.docker.com/engine/docker-overview/).<br><br> Docker należy skonfigurować w taki sposób, aby umożliwić kontenerów, aby nawiązać połączenie z, a następnie wysyłać danych dotyczących rozliczeń do platformy Azure. <br><br> **W systemie Windows**program Docker musi być również skonfigurowany do obsługi kontenerów systemu Linux.<br><br>|
|Znajomość platformy Docker | Należy dysponować podstawową wiedzą na temat pojęć platformy Docker, takich jak rejestry, repozytoria, kontenery i obrazy kontenerów, a także znajomość podstawowych poleceń `docker`.| 
|Zasób analiza tekstu |Aby można było używać kontenera, musisz mieć:<br><br>[Zasób analiza tekstu](../../cognitive-services-apis-create-account.md) platformy Azure, aby pobrać skojarzony klucz interfejsu API i identyfikator URI punktu końcowego. Obie wartości są dostępne na stronie Przegląd analiza tekstu Azure Portal i klucze i są wymagane do uruchomienia kontenera.<br><br>**{API_KEY}** : jeden z dwóch dostępnych kluczy zasobów na stronie **kluczy**<br><br>**{ENDPOINT_URI}** : punkt końcowy określony na stronie **Przegląd**|

[!INCLUDE [Gathering required parameters](../../containers/includes/container-gathering-required-parameters.md)]

## <a name="the-host-computer"></a>Komputer-host

[!INCLUDE [Host Computer requirements](../../../../includes/cognitive-services-containers-host-computer.md)]

### <a name="container-requirements-and-recommendations"></a>Kontener wymagania i zalecenia

W poniższej tabeli przedstawiono minimalne i zalecane rdzeni procesora CPU, co najmniej 2,6 gigaherc (GHz) lub szybszy i pamięci w gigabajtach (GB) do przydzielenia dla każdego kontenera analizy tekstu.

# <a name="key-phrase-extractiontabkeyphrase"></a>[wyodrębnianie kluczowych fraz](#tab/keyphrase)

[!INCLUDE [key-phrase-extraction-container-requirements](../includes/key-phrase-extraction-container-requirements.md)]

# <a name="language-detectiontablanguage"></a>[wykrywanie języka](#tab/language)

[!INCLUDE [language-detection-container-requirements](../includes/language-detection-container-requirements.md)]

# <a name="sentiment-analysistabsentiment"></a>[analiza tonacji](#tab/sentiment)

[!INCLUDE [sentiment-analysis-container-requirements](../includes/sentiment-analysis-container-requirements.md)]

***

* Każdy rdzeń musi mieć co najmniej 2,6 gigaherca (GHz) lub szybszy.
* TPS — liczba transakcji na sekundę

Rdzeń i pamięć odpowiadają ustawieniom `--cpus` i `--memory`, które są używane jako część polecenia `docker run`.

## <a name="get-the-container-image-with-docker-pull"></a>Pobierz obraz kontenera za pomocą `docker pull`

Obrazy kontenerów dla analiza tekstu są dostępne w programie Microsoft Container Registry.

# <a name="key-phrase-extractiontabkeyphrase"></a>[wyodrębnianie kluczowych fraz](#tab/keyphrase)

[!INCLUDE [key-phrase-extraction-container-repository](../includes/key-phrase-extraction-container-repository.md)]

# <a name="language-detectiontablanguage"></a>[wykrywanie języka](#tab/language)

[!INCLUDE [language-detection-container-repository](../includes/language-detection-container-repository.md)]

# <a name="sentiment-analysistabsentiment"></a>[analiza tonacji](#tab/sentiment)

[!INCLUDE [sentiment-analysis-container-repository](../includes/sentiment-analysis-container-repository.md)]

***

[!INCLUDE [Tip for using docker list](../../../../includes/cognitive-services-containers-docker-list-tip.md)]

### <a name="docker-pull-for-the-text-analytics-containers"></a>Wypychanie platformy Docker dla kontenerów analiza tekstu

# <a name="key-phrase-extractiontabkeyphrase"></a>[wyodrębnianie kluczowych fraz](#tab/keyphrase)

[!INCLUDE [docker-pull-key-phrase-extraction-container](../includes/docker-pull-key-phrase-extraction-container.md)]

# <a name="language-detectiontablanguage"></a>[wykrywanie języka](#tab/language)

[!INCLUDE [docker-pull-language-detection-container](../includes/docker-pull-language-detection-container.md)]

# <a name="sentiment-analysistabsentiment"></a>[analiza tonacji](#tab/sentiment)

[!INCLUDE [docker-pull-sentiment-analysis-container](../includes/docker-pull-sentiment-analysis-container.md)]

***

## <a name="how-to-use-the-container"></a>Jak używać kontenera

Gdy kontener znajduje się na [komputerze hosta](#the-host-computer), użyj następującego procesu, aby współpracować z kontenerem.

1. [Uruchom kontener](#run-the-container-with-docker-run)z wymaganymi ustawieniami rozliczania. Więcej [przykładów](../text-analytics-resource-container-config.md#example-docker-run-commands) polecenia `docker run` są dostępne.
1. [Zbadaj punkt końcowy przewidywania kontenera](#query-the-containers-prediction-endpoint).

## <a name="run-the-container-with-docker-run"></a>Uruchom kontener za pomocą `docker run`

Użyj polecenia [Docker Run](https://docs.docker.com/engine/reference/commandline/run/) , aby uruchomić jeden z trzech kontenerów. Zapoznaj się z tematem [zbieranie wymaganych parametrów](#gathering-required-parameters) , aby uzyskać szczegółowe informacje na temat pobierania wartości `{ENDPOINT_URI}` i `{API_KEY}`.

[Przykłady](../text-analytics-resource-container-config.md#example-docker-run-commands) polecenia `docker run` są dostępne.

# <a name="key-phrase-extractiontabkeyphrase"></a>[wyodrębnianie kluczowych fraz](#tab/keyphrase)

[!INCLUDE [docker-run-key-phrase-extraction-container](../includes/docker-run-key-phrase-extraction-container.md)]

# <a name="language-detectiontablanguage"></a>[wykrywanie języka](#tab/language)

[!INCLUDE [docker-run-language-detection-container](../includes/docker-run-language-detection-container.md)]

# <a name="sentiment-analysistabsentiment"></a>[analiza tonacji](#tab/sentiment)

[!INCLUDE [docker-run-sentiment-analysis-container](../includes/docker-run-sentiment-analysis-container.md)]

***

> [!IMPORTANT]
> Aby można było uruchomić kontener, należy określić opcje `Eula`, `Billing`i `ApiKey`. w przeciwnym razie kontener nie zostanie uruchomiony.  Aby uzyskać więcej informacji, zobacz [rozliczenia](#billing).

[!INCLUDE [Running multiple containers on the same host](../../../../includes/cognitive-services-containers-run-multiple-same-host.md)]

## <a name="query-the-containers-prediction-endpoint"></a>Zbadaj punkt końcowy przewidywania kontenera

Kontener udostępnia interfejsy API punktu końcowego przewidywania zapytań.

Użyj hosta `http://localhost:5000`w przypadku interfejsów API kontenerów.

<!--  ## Validate container is running -->

[!INCLUDE [Container's API documentation](../../../../includes/cognitive-services-containers-api-documentation.md)]

## <a name="stop-the-container"></a>Zatrzymaj kontener

[!INCLUDE [How to stop the container](../../../../includes/cognitive-services-containers-stop.md)]

## <a name="troubleshooting"></a>Rozwiązywanie problemów

Jeśli uruchamiasz kontener z [instalacją wyjściową](../text-analytics-resource-container-config.md#mount-settings) i włączono rejestrowanie, kontener generuje pliki dziennika, które są przydatne do rozwiązywania problemów występujących podczas uruchamiania lub uruchamiania kontenera.

[!INCLUDE [Cognitive Services FAQ note](../../containers/includes/cognitive-services-faq-note.md)]

## <a name="billing"></a>Rozliczenia

Kontenery analiza tekstu wysyłają informacje o rozliczeniach do platformy Azure przy użyciu zasobu _Analiza tekstu_ na koncie platformy Azure. 

[!INCLUDE [Container's Billing Settings](../../../../includes/cognitive-services-containers-how-to-billing-info.md)]

Aby uzyskać więcej informacji na temat tych opcji, zobacz [Konfigurowanie kontenerów](../text-analytics-resource-container-config.md).

<!--blogs/samples/video course -->

[!INCLUDE [Discoverability of more container information](../../../../includes/cognitive-services-containers-discoverability.md)]

## <a name="summary"></a>Podsumowanie

W tym artykule przedstawiono pojęcia i przepływ pracy na potrzeby pobierania, instalowania i uruchamiania kontenerów analizy tekstu. Podsumowanie:

* Analiza tekstu oferuje trzy kontenery systemu Linux dla platformy Docker, hermetyzowając różne możliwości:
   * *wyodrębnianie kluczowych fraz*
   * *wykrywanie języka*
   * *analiza tonacji*
* Obrazy kontenerów są pobierane z rejestru kontenerów firmy Microsoft (MCR) na platformie Azure.
* Obrazy kontenera Uruchom na platformie Docker.
* Można użyć interfejsu API REST lub zestawu SDK do wywoływania operacji w kontenerach analizy tekstu, określając host identyfikatora URI kontenera.
* Należy określić informacje rozliczeniowe, podczas tworzenia wystąpienia kontenera.

> [!IMPORTANT]
> Kontenery usługi cognitive Services nie są licencjonowane do uruchomienia bez połączenia z platformy Azure do zbierania danych. Klienci muszą włączyć kontener, aby komunikować informacje rozliczeniowe usłudze zliczania przez cały czas. Kontenery usługi cognitive Services nie wysyłaj danych klientów (np. obraz lub tekst, który jest analizowana) do firmy Microsoft.

## <a name="next-steps"></a>Następne kroki

* Przegląd [konfigurowania kontenerów](../text-analytics-resource-container-config.md) dla ustawień konfiguracji
* Zapoznaj się z [często zadawanymi pytaniami](../text-analytics-resource-faq.md) , aby rozwiązać problemy związane z działaniem.
