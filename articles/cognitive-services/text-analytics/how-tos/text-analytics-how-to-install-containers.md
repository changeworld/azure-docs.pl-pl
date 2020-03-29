---
title: Instalowanie i uruchamianie kontenerów — analiza tekstu
titleSuffix: Azure Cognitive Services
description: Jak pobrać, zainstalować i uruchomić kontenery dla analizy tekstu w tym samouczku.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 03/10/2020
ms.author: dapine
ms.openlocfilehash: 65033f9b6599d690b1097b4b78aa01148a40fc39
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79037500"
---
# <a name="install-and-run-text-analytics-containers"></a>Instalowanie i uruchamianie kontenerów analizy tekstu

Kontenery umożliwiają uruchamianie interfejsów API analizy tekstu w twoim środowisku i są idealne dla określonych wymagań dotyczących zabezpieczeń i zarządzania danymi. Kontenery analizy tekstu zapewniają zaawansowane przetwarzanie języka naturalnego za pomocą tekstu nieprzetworzonego i zawierają trzy główne funkcje: analizę tonacji, wyodrębnianie fraz kluczowych i wykrywanie języka. Łączenie jednostek nie jest obecnie obsługiwane w kontenerze.

Jeśli nie masz subskrypcji platformy Azure, utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) przed rozpoczęciem.

> [!IMPORTANT]
> Bezpłatne konto jest ograniczone do 5000 transakcji miesięcznie i tylko <a href="https://azure.microsoft.com/pricing/details/cognitive-services/text-analytics" target="_blank">warstwy <span class="docon docon-navigate-external x-hidden-focus"></span> cenowe</a> **Wolne** i **Standardowe** są ważne dla kontenerów. Aby uzyskać więcej informacji na temat stawek żądań transakcji, zobacz [Limity danych](https://docs.microsoft.com/azure/cognitive-services/text-analytics/overview#data-limits).

## <a name="prerequisites"></a>Wymagania wstępne

Aby uruchomić dowolny kontener analizy tekstu, musisz mieć środowisko komputera-hosta i kontenera.

## <a name="preparation"></a>Przygotowywanie

Przed użyciem kontenerów analizy tekstu należy spełnić następujące wymagania wstępne:

|Wymagany|Przeznaczenie|
|--|--|
|Aparat platformy Docker| Aparat platformy Docker jest zainstalowany na [komputerze-hoście.](#the-host-computer) Platforma Docker udostępnia pakiety, które konfigurują środowisko platformy Docker w systemach [macOS](https://docs.docker.com/docker-for-mac/), [Windows](https://docs.docker.com/docker-for-windows/) i [Linux](https://docs.docker.com/engine/installation/#supported-platforms). Aby uzyskać podstawowe informacje na temat platformy Docker i kontenerów, zapoznaj się z artykułem [Docker overview](https://docs.docker.com/engine/docker-overview/) (Przegląd platformy Docker).<br><br> Platforma Docker musi być skonfigurowana tak, aby zezwalała kontenerom na łączenie się z danymi rozliczeń i wysyłanie ich na platformę Azure. <br><br> **W systemie Windows**program Docker musi być również skonfigurowany do obsługi kontenerów systemu Linux.<br><br>|
|Znajomość platformy Docker | Powinieneś mieć podstawową wiedzę na temat pojęć platformy Docker, takich jak rejestry, repozytoria, kontenery i obrazy kontenerów, a także znajomość podstawowych `docker` poleceń.| 
|Zasób analizy tekstu |Aby korzystać z kontenera, musisz mieć:<br><br>[Zasób](../../cognitive-services-apis-create-account.md) usługi Azure Text Analytics, aby uzyskać skojarzony klucz interfejsu API i identyfikator URI punktu końcowego. Obie wartości są dostępne na stronach Analiza tekstu i klucze usługi Azure portal i są wymagane do uruchomienia kontenera.<br><br>**{API_KEY}**: Jeden z dwóch dostępnych kluczy zasobów na stronie **Klucze**<br><br>**{ENDPOINT_URI}**: Punkt końcowy podany na stronie **Przegląd**|

[!INCLUDE [Gathering required parameters](../../containers/includes/container-gathering-required-parameters.md)]

## <a name="the-host-computer"></a>Komputer-host

[!INCLUDE [Host Computer requirements](../../../../includes/cognitive-services-containers-host-computer.md)]

### <a name="container-requirements-and-recommendations"></a>Wymagania i zalecenia dotyczące kontenerów

W poniższej tabeli opisano minimalne i zalecane rdzenie procesora CPU, co najmniej 2,6 gigaherca (GHz) lub szybsze oraz pamięć w gigabajtach (GB) do przydzielenia dla każdego kontenera analizy tekstu.

# <a name="key-phrase-extraction"></a>[Wyodrębnianie kluczowych fraz](#tab/keyphrase)

[!INCLUDE [key-phrase-extraction-container-requirements](../includes/key-phrase-extraction-container-requirements.md)]

# <a name="language-detection"></a>[Wykrywanie języka](#tab/language)

[!INCLUDE [language-detection-container-requirements](../includes/language-detection-container-requirements.md)]

# <a name="sentiment-analysis"></a>[Analiza tonacji](#tab/sentiment)

[!INCLUDE [sentiment-analysis-container-requirements](../includes/sentiment-analysis-container-requirements.md)]

***

* Każdy rdzeń musi mieć co najmniej 2,6 gigaherca (GHz) lub więcej.
* TPS - transakcje na sekundę

Rdzeń i pamięć `--cpus` `--memory` odpowiadają i ustawienia, które `docker run` są używane jako część polecenia.

## <a name="get-the-container-image-with-docker-pull"></a>Pobierz obraz kontenera za pomocą`docker pull`

Obrazy kontenerów dla analizy tekstu są dostępne w rejestrze kontenerów firmy Microsoft.

# <a name="key-phrase-extraction"></a>[Wyodrębnianie kluczowych fraz](#tab/keyphrase)

[!INCLUDE [key-phrase-extraction-container-repository](../includes/key-phrase-extraction-container-repository.md)]

# <a name="language-detection"></a>[Wykrywanie języka](#tab/language)

[!INCLUDE [language-detection-container-repository](../includes/language-detection-container-repository.md)]

# <a name="sentiment-analysis"></a>[Analiza tonacji](#tab/sentiment)

[!INCLUDE [sentiment-analysis-container-repository](../includes/sentiment-analysis-container-repository.md)]

***

[!INCLUDE [Tip for using docker list](../../../../includes/cognitive-services-containers-docker-list-tip.md)]

### <a name="docker-pull-for-the-text-analytics-containers"></a>Ściąganie platformy Docker dla kontenerów analizy tekstu

# <a name="key-phrase-extraction"></a>[Wyodrębnianie kluczowych fraz](#tab/keyphrase)

[!INCLUDE [docker-pull-key-phrase-extraction-container](../includes/docker-pull-key-phrase-extraction-container.md)]

# <a name="language-detection"></a>[Wykrywanie języka](#tab/language)

[!INCLUDE [docker-pull-language-detection-container](../includes/docker-pull-language-detection-container.md)]

# <a name="sentiment-analysis"></a>[Analiza tonacji](#tab/sentiment)

[!INCLUDE [docker-pull-sentiment-analysis-container](../includes/docker-pull-sentiment-analysis-container.md)]

***

## <a name="how-to-use-the-container"></a>Jak korzystać z pojemnika

Gdy kontener znajduje się na [komputerze-hoście,](#the-host-computer)użyj następującego procesu do pracy z kontenerem.

1. [Uruchom kontener](#run-the-container-with-docker-run)z wymaganymi ustawieniami rozliczeń. Dostępnych jest więcej `docker run` [przykładów](../text-analytics-resource-container-config.md#example-docker-run-commands) polecenia.
1. [Kwerenda punktu końcowego przewidywania kontenera](#query-the-containers-prediction-endpoint).

## <a name="run-the-container-with-docker-run"></a>Uruchom pojemnik za pomocą`docker run`

Użyj polecenia [uruchamiania platformy docker,](https://docs.docker.com/engine/reference/commandline/run/) aby uruchomić dowolny z trzech kontenerów. Szczegółowe informacje na temat sposobu uzyskania wartości `{ENDPOINT_URI}` `{API_KEY}` można znaleźć w polu [Zbieranie wymaganych parametrów.](#gathering-required-parameters)

[Dostępne](../text-analytics-resource-container-config.md#example-docker-run-commands) są `docker run` przykłady polecenia.

# <a name="key-phrase-extraction"></a>[Wyodrębnianie kluczowych fraz](#tab/keyphrase)

[!INCLUDE [docker-run-key-phrase-extraction-container](../includes/docker-run-key-phrase-extraction-container.md)]

# <a name="language-detection"></a>[Wykrywanie języka](#tab/language)

[!INCLUDE [docker-run-language-detection-container](../includes/docker-run-language-detection-container.md)]

# <a name="sentiment-analysis"></a>[Analiza tonacji](#tab/sentiment)

[!INCLUDE [docker-run-sentiment-analysis-container](../includes/docker-run-sentiment-analysis-container.md)]

***

> [!IMPORTANT]
> Aby `Eula` `Billing`uruchomić `ApiKey` kontener, należy określić opcje i opcje; w przeciwnym razie kontener nie zostanie uruchomiony.  Aby uzyskać więcej informacji, zobacz [Rozliczenia](#billing).

[!INCLUDE [Running multiple containers on the same host](../../../../includes/cognitive-services-containers-run-multiple-same-host.md)]

## <a name="query-the-containers-prediction-endpoint"></a>Kwerenda punktu końcowego przewidywania kontenera

Kontener zawiera interfejsy API punktu końcowego przewidywania zapytań oparte na rest.

Użyj hosta, `http://localhost:5000`dla interfejsów API kontenera.

<!--  ## Validate container is running -->

[!INCLUDE [Container's API documentation](../../../../includes/cognitive-services-containers-api-documentation.md)]

## <a name="stop-the-container"></a>Zatrzymywanie kontenera

[!INCLUDE [How to stop the container](../../../../includes/cognitive-services-containers-stop.md)]

## <a name="troubleshooting"></a>Rozwiązywanie problemów

Jeśli uruchomisz kontener z [włączoną instalacją](../text-analytics-resource-container-config.md#mount-settings) wyjściową i rejestrowaniem, kontener generuje pliki dziennika, które są przydatne do rozwiązywania problemów, które występują podczas uruchamiania lub uruchamiania kontenera.

[!INCLUDE [Cognitive Services FAQ note](../../containers/includes/cognitive-services-faq-note.md)]

## <a name="billing"></a>Rozliczenia

Kontenery analizy tekstu wysyłają informacje rozliczeniowe na platformę Azure przy użyciu zasobu _analizy tekstu_ na koncie platformy Azure. 

[!INCLUDE [Container's Billing Settings](../../../../includes/cognitive-services-containers-how-to-billing-info.md)]

Aby uzyskać więcej informacji na temat tych opcji, zobacz [Konfigurowanie kontenerów](../text-analytics-resource-container-config.md).

<!--blogs/samples/video course -->

[!INCLUDE [Discoverability of more container information](../../../../includes/cognitive-services-containers-discoverability.md)]

## <a name="summary"></a>Podsumowanie

W tym artykule poznaliście pojęcia i przepływ pracy do pobierania, instalowania i uruchamiania kontenerów analizy tekstu. Podsumowanie:

* Analiza tekstu udostępnia trzy kontenery systemu Linux dla platformy Docker, hermetyzując różne możliwości:
   * *Wyodrębnianie kluczowych fraz*
   * *Wykrywanie języka*
   * *Analiza tonacji*
* Obrazy kontenerów są pobierane z rejestru kontenerów firmy Microsoft (MCR) na platformie Azure.
* Obrazy kontenerów są uruchamiane w usłudze Docker.
* Za pomocą interfejsu API REST lub SDK można wywołać operacje w kontenerach analizy tekstu, określając identyfikator URI hosta kontenera.
* Podczas tworzenia wystąpienia kontenera należy określić informacje rozliczeniowe.

> [!IMPORTANT]
> Kontenery usług Cognitive Services nie są licencjonowane do uruchamiania bez połączenia z platformą Azure w celu pomiaru. Klienci muszą włączyć kontenery do przekazywania informacji rozliczeniowych z usługą pomiaru przez cały czas. Kontenery usług Cognitive Services nie wysyłają danych klientów (np. obrazu lub tekstu, który jest analizowany) do firmy Microsoft.

## <a name="next-steps"></a>Następne kroki

* Przeglądanie [Konfigurowanie kontenerów](../text-analytics-resource-container-config.md) pod kątem ustawień konfiguracji
* Zapoznaj się [z często zadawanymi pytaniami (FAQ),](../text-analytics-resource-faq.md) aby rozwiązać problemy związane z funkcjonalnością.
