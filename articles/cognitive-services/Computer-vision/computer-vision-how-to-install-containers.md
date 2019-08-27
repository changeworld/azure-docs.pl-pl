---
title: Jak instalować i uruchamiać kontenery — przetwarzanie obrazów
titleSuffix: Azure Cognitive Services
description: Jak pobrać, zainstalować i uruchamiaj kontenery dla przetwarzania obrazów w tym samouczku wskazówki.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 06/19/2019
ms.author: dapine
ms.custom: seodec18
ms.openlocfilehash: cbf199c391b49518bb595d7d1a0ed47147903a85
ms.sourcegitcommit: 94ee81a728f1d55d71827ea356ed9847943f7397
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/26/2019
ms.locfileid: "70034501"
---
# <a name="install-and-run-recognize-text-containers"></a>Instalowanie i uruchamianie kontenerów Rozpoznawanie tekstu

Rozpoznaj tekst część przetwarzania obrazów jest również dostępny jako kontener platformy Docker. Umożliwia ona wykrywanie i wyodrębnianie tekstu drukowanego z obrazów różne obiekty na różnych powierzchniach i tłach, takich jak przychody oraz plakaty i wizytówki.

> [!IMPORTANT]
> Kontener Rozpoznaj tekst obecnie działa tylko w języku angielskim.

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="prerequisites"></a>Wymagania wstępne

Przed użyciem kontenerów Rozpoznawanie tekstu należy spełnić następujące wymagania wstępne:

|Wymagane|Cel|
|--|--|
|Aparat platformy Docker| Aparat platformy Docker musi być zainstalowany na [komputerze-hoście](#the-host-computer). Platforma Docker zawiera pakiety, które konfigurują środowisko platformy Docker w systemach [macOS](https://docs.docker.com/docker-for-mac/), [Windows](https://docs.docker.com/docker-for-windows/)i [Linux](https://docs.docker.com/engine/installation/#supported-platforms). Aby uzyskać podstawowe informacje na temat platformy Docker i kontenerów, zobacz [Docker — omówienie](https://docs.docker.com/engine/docker-overview/).<br><br> Docker należy skonfigurować w taki sposób, aby umożliwić kontenerów, aby nawiązać połączenie z, a następnie wysyłać danych dotyczących rozliczeń do platformy Azure. <br><br> **W systemie Windows**program Docker musi być również skonfigurowany do obsługi kontenerów systemu Linux.<br><br>|
|Znajomość platformy Docker | Należy dysponować podstawową wiedzą na temat pojęć platformy Docker, takich jak rejestry, repozytoria, kontenery i obrazy kontenerów, a także znajomość `docker` podstawowych poleceń.| 
|Zasób przetwarzanie obrazów |Aby można było używać kontenera, musisz mieć:<br><br>Zasób usługi Azure **Przetwarzanie obrazów** i skojarzony klucz interfejsu API dla identyfikatora URI punktu końcowego. Obie wartości są dostępne na stronach przeglądów i kluczy dla zasobu i są wymagane do uruchomienia kontenera.<br><br>**{API_KEY}** : Jeden z dwóch dostępnych kluczy zasobów na stronie **klucze**<br><br>**{ENDPOINT_URI}** : Punkt końcowy zgodnie z opisem na stronie **Przegląd**|

## <a name="request-access-to-the-private-container-registry"></a>Poproś o dostęp do prywatnego rejestru kontenerów

[!INCLUDE [Request access to public preview](../../../includes/cognitive-services-containers-request-access.md)]

### <a name="the-host-computer"></a>Komputer-host

[!INCLUDE [Host Computer requirements](../../../includes/cognitive-services-containers-host-computer.md)]

### <a name="container-requirements-and-recommendations"></a>Kontener wymagania i zalecenia

[!INCLUDE [Container requirements and recommendations](includes/container-requirements-and-recommendations.md)]

## <a name="get-the-container-image-with-docker-pull"></a>Pobierz obraz kontenera za pomocą`docker pull`

Dostępne są obrazy kontenerów dla Rozpoznawanie tekstu. 

| Kontener | Repozytorium |
|-----------|------------|
|Rozpoznawanie tekstu | `containerpreview.azurecr.io/microsoft/cognitive-services-recognize-text:latest` |

Użyj polecenia [`docker pull`](https://docs.docker.com/engine/reference/commandline/pull/) , aby pobrać obraz kontenera.


### <a name="docker-pull-for-the-recognize-text-container"></a>Wypychanie platformy Docker dla kontenera Rozpoznawanie tekstu

```
docker pull containerpreview.azurecr.io/microsoft/cognitive-services-recognize-text:latest
```

[!INCLUDE [Tip for using docker list](../../../includes/cognitive-services-containers-docker-list-tip.md)]

## <a name="how-to-use-the-container"></a>Jak używać kontenera

Gdy kontener znajduje się na [komputerze hosta](#the-host-computer), użyj następującego procesu, aby współpracować z kontenerem.

1. [Uruchom kontener](#run-the-container-with-docker-run)z wymaganymi ustawieniami rozliczania. Więcej [przykładów](computer-vision-resource-container-config.md) `docker run` polecenia jest dostępnych. 
1. [Zbadaj punkt końcowy przewidywania kontenera](#query-the-containers-prediction-endpoint). 

## <a name="run-the-container-with-docker-run"></a>Uruchom kontener za pomocą`docker run`

Użyj polecenia [Docker Run](https://docs.docker.com/engine/reference/commandline/run/) , aby uruchomić kontener. Polecenie używa następujących parametrów:

| Symbol zastępczy | Value |
|-------------|-------|
|{API_KEY} | Ten klucz jest używany do uruchamiania kontenera i jest dostępny na stronie kluczy platformy Azure `Cognitive Services` .  |
|{ENDPOINT_URI} | Wartość identyfikatora URI punktu końcowego rozliczenia. Przykład:`https://westus.api.cognitive.microsoft.com/vision/v2.0`|

Należy dodać `vision/v2.0` Routing do identyfikatora URI punktu końcowego, jak pokazano w poniższym przykładzie BILLING_ENDPOINT_URI.

Zastąp te parametry własnymi wartościami w poniższym przykładowym `docker run` poleceniu.

```bash
docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 \
containerpreview.azurecr.io/microsoft/cognitive-services-recognize-text \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

To polecenie:

* Uruchamia kontener rozpoznawania z obrazu kontenera
* Przypisuje jedne rdzeń procesora CPU i 4 gigabajty (GB) pamięci
* Uwidacznia TCP port 5000 i przydziela pseudo-TTY kontenera
* Automatycznie usuwa kontener po zakończeniu. Obraz kontenera jest nadal dostępny na komputerze-hoście. 

Więcej [przykładów](./computer-vision-resource-container-config.md#example-docker-run-commands) `docker run` polecenia jest dostępnych. 

> [!IMPORTANT]
> `Eula`, `Billing`, I `ApiKey` opcje muszą być określone w celu uruchomienia kontenera; w przeciwnym razie nie uruchamia się kontener.  Aby uzyskać więcej informacji, zobacz [rozliczeń](#billing).

[!INCLUDE [Running multiple containers on the same host](../../../includes/cognitive-services-containers-run-multiple-same-host.md)]


## <a name="query-the-containers-prediction-endpoint"></a>Zbadaj punkt końcowy przewidywania kontenera

Kontener udostępnia interfejsy API punktu końcowego przewidywania zapytań. 

Użyj hosta, `http://localhost:5000`, dla interfejsów API kontenerów.

### <a name="asynchronous-text-recognition"></a>Rozpoznawanie tekstu asynchroniczne

Możesz użyć `POST /vision/v2.0/recognizeText` i `GET /vision/v2.0/textOperations/*{id}*` operacje z optymalizacją asynchronicznie, rozpoznawał drukowanego tekstu w obrazie, podobnie jak Usługa przetwarzania obrazów używa tych odpowiednich operacji REST. Kontener Rozpoznaj tekst rozpoznaje tylko drukowanego tekstu, tekst nie pisma odręcznego w tej chwili więc `mode` parametr zwykle określony dla operacji przetwarzania obrazów usługi jest ignorowana przez kontener rozpoznawanie tekstu.

### <a name="synchronous-text-recognition"></a>Rozpoznawanie tekstu synchroniczne

Możesz użyć `POST /vision/v2.0/recognizeTextDirect` operacji synchronicznie, rozpoznawał drukowanego tekstu w obrazie. Ponieważ ta operacja jest synchroniczna, treść żądania dla tej operacji jest taka sama jak `POST /vision/v2.0/recognizeText` operacja, ale treść odpowiedzi dla tej operacji jest taka sama jak wartość zwrócona `GET /vision/v2.0/textOperations/*{id}*` przez operację.

<!--  ## Validate container is running -->

[!INCLUDE [Container's API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]


## <a name="stop-the-container"></a>Zatrzymaj kontener

[!INCLUDE [How to stop the container](../../../includes/cognitive-services-containers-stop.md)]

## <a name="troubleshooting"></a>Rozwiązywanie problemów

Jeśli uruchamiasz kontener z instalacją wyjściową [](./computer-vision-resource-container-config.md#mount-settings) i włączono rejestrowanie, kontener generuje pliki dziennika, które są przydatne do rozwiązywania problemów występujących podczas uruchamiania lub uruchamiania kontenera. 


## <a name="billing"></a>Rozliczenia

Kontenery Rozpoznawanie tekstu wysyłają informacje o rozliczeniach do platformy Azure przy użyciu zasobu _rozpoznawanie tekstu_ na koncie platformy Azure. 

[!INCLUDE [Container's Billing Settings](../../../includes/cognitive-services-containers-how-to-billing-info.md)]

Aby uzyskać więcej informacji o tych opcjach, zobacz [skonfigurować kontenery](./computer-vision-resource-container-config.md).

<!--blogs/samples/video course -->

[!INCLUDE [Discoverability of more container information](../../../includes/cognitive-services-containers-discoverability.md)]

## <a name="summary"></a>Podsumowanie

W tym artykule przedstawiono koncepcje i przepływ pracy służące do pobierania, instalowania i uruchamiania kontenerów Rozpoznawanie tekstu. Podsumowanie:

* Rozpoznawanie tekstu udostępnia kontener systemu Linux dla platformy Docker, hermetyzując rozpoznawanie tekstu.
* Obrazy kontenerów są pobierane z rejestru kontenerów firmy Microsoft (MCR) na platformie Azure.
* Obrazy kontenera Uruchom na platformie Docker.
* Można użyć interfejsu API REST lub zestawu SDK do wywoływania operacji w kontenerach Rozpoznawanie tekstu, określając identyfikator URI hosta kontenera.
* Należy określić informacje rozliczeniowe, podczas tworzenia wystąpienia kontenera.

> [!IMPORTANT]
> Kontenery usługi cognitive Services nie są licencjonowane do uruchomienia bez połączenia z platformy Azure do zbierania danych. Klienci muszą włączyć kontener, aby komunikować informacje rozliczeniowe usłudze zliczania przez cały czas. Kontenery Cognitive Services nie wysyłają danych klienta (na przykład obrazu lub tekstu, który jest analizowany) do firmy Microsoft.

## <a name="next-steps"></a>Następne kroki

* Przegląd [skonfigurować kontenery](computer-vision-resource-container-config.md) ustawień konfiguracji
* Przegląd [omówienie wizualizacji komputerowej](Home.md) dowiedzieć się więcej o rozpoznawaniu tekstu drukowanego i pisma odręcznego  
* Zapoznaj się [interfejs API przetwarzania obrazów](//westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa) szczegółowe informacje na temat metod obsługiwanych przez kontener.
* Zapoznaj się [— często zadawane pytania (FAQ)](FAQ.md) Aby rozwiązać problemy związane z funkcji przetwarzania obrazów.
* Użyj więcej [kontenerów Cognitive Services](../cognitive-services-container-support.md)
