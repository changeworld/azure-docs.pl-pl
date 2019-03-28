---
title: Jak zainstalować i uruchamiać kontenery - przetwarzania obrazów
titlesuffix: Azure Cognitive Services
description: Jak pobrać, zainstalować i uruchamiaj kontenery dla przetwarzania obrazów w tym samouczku wskazówki.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: article
ms.date: 3/22/2019
ms.author: diberry
ms.custom: seodec18
ms.openlocfilehash: b7e60b4fbdf076c50a7d9a29092de9ab1c32b210
ms.sourcegitcommit: 6da4959d3a1ffcd8a781b709578668471ec6bf1b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2019
ms.locfileid: "58520653"
---
# <a name="install-and-run-recognize-text-containers"></a>Instalowanie i uruchamianie kontenerów rozpoznawanie tekstu

Rozpoznaj tekst część przetwarzania obrazów jest również dostępny jako kontener platformy Docker. Umożliwia ona wykrywanie i wyodrębnianie tekstu drukowanego z obrazów różne obiekty na różnych powierzchniach i tłach, takich jak przychody oraz plakaty i wizytówki.  
> [!IMPORTANT]
> Kontener Rozpoznaj tekst obecnie działa tylko w języku angielskim.

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem korzystania z kontenerów rozpoznawanie tekstu, musi spełniać następujące wymagania wstępne:

|Wymagane|Przeznaczenie|
|--|--|
|Aparat platformy docker| Aparat platformy Docker zainstalowany na musisz [komputerze-hoście](#the-host-computer). Środowisko docker zawiera pakiety, które konfigurują środowisko platformy Docker na [macOS](https://docs.docker.com/docker-for-mac/), [Windows](https://docs.docker.com/docker-for-windows/), i [Linux](https://docs.docker.com/engine/installation/#supported-platforms). Aby uzyskać podstawowe informacje na temat platformy Docker i kontenerów, zobacz [Docker — omówienie](https://docs.docker.com/engine/docker-overview/).<br><br> Docker należy skonfigurować w taki sposób, aby umożliwić kontenerów, aby nawiązać połączenie z, a następnie wysyłać danych dotyczących rozliczeń do platformy Azure. <br><br> **Na Windows**, platformy Docker musi być również skonfigurowany do obsługi kontenerów systemu Linux.<br><br>|
|Znajomość platformy Docker | Należy mieć podstawową wiedzę na temat pojęć usługi Docker, takich jak rejestry, repozytoria, kontenery i obrazów kontenerów, a także wiedzę na temat basic `docker` poleceń.| 
|Rozpoznawanie tekstu zasobów |Aby można było używać kontenera, musisz mieć:<br><br>A [ _Rozpoznaj tekst_ ](vision-api-how-to-topics/howtosubscribe.md) zasobów platformy Azure, aby uzyskać skojarzonego klucza rozliczenia i rozliczeń identyfikator URI punktu końcowego. Obie wartości są dostępne na stronach witryny Azure portal rozpoznaje Przegląd tekstu i klucze i są wymagane do uruchomienia kontenera.<br><br>**{BILLING_KEY}** : klucz zasobu<br><br>**{BILLING_ENDPOINT_URI}** : przykład identyfikatora URI punktu końcowego: `https://westus.api.cognitive.microsoft.com/vision/v2.0`|


## <a name="request-access-to-the-private-container-registry"></a>Poproś o dostęp do prywatnego rejestru kontenerów

[!INCLUDE [Request access to private preview](../../../includes/cognitive-services-containers-request-access.md)]

### <a name="the-host-computer"></a>Komputer-host

[!INCLUDE [Host Computer requirements](../../../includes/cognitive-services-containers-host-computer.md)]


### <a name="container-requirements-and-recommendations"></a>Kontener wymagania i zalecenia

W poniższej tabeli opisano minimalne i zalecane rdzeni procesora CPU i pamięci do przydzielenia dla każdego kontenera rozpoznawanie tekstu.

| Kontener | Minimalne | Zalecane |TPS<br>(Minimum, maksimum)|
|-----------|---------|-------------|--|
|Rozpoznawanie tekstu|1 core, 8 GB memory, 0.5 TPS|2 rdzenie, 8 GB pamięci 1 TPS|0.5, 1|

* Każdego rdzenia musi mieć co najmniej 2,6 gigaherc (GHz) lub szybszy.
* TPS - transakcji na sekundę

Rdzeni i pamięci odpowiadają `--cpus` i `--memory` ustawienia, które są używane jako część `docker run` polecenia.

## <a name="get-the-container-image-with-docker-pull"></a>Pobierz obraz kontenera przy użyciu `docker pull`

Dostępne są obrazy kontenerów dla rozpoznawanie tekstu. 

| Kontener | Repozytorium |
|-----------|------------|
|Rozpoznawanie tekstu | `containerpreview.azurecr.io/microsoft/cognitive-services-recognize-text:latest` |

Użyj [ `docker pull` ](https://docs.docker.com/engine/reference/commandline/pull/) polecenie, aby pobrać obraz kontenera.


### <a name="docker-pull-for-the-recognize-text-container"></a>Polecenie docker pull dla kontenera rozpoznawanie tekstu

```
docker pull containerpreview.azurecr.io/microsoft/cognitive-services-recognize-text:latest
```

[!INCLUDE [Tip for using docker list](../../../includes/cognitive-services-containers-docker-list-tip.md)]

## <a name="how-to-use-the-container"></a>Jak używać kontenera

Gdy kontener będzie na [komputerze-hoście](#the-host-computer), użyj następującego procesu do pracy z kontenerem.

1. [Uruchom kontener](#run-the-container-with-docker-run), za pomocą wymaganych rozliczeń ustawienia. Więcej [przykłady](computer-vision-resource-container-config.md) z `docker run` polecenia są dostępne. 
1. [Zapytanie do endpoint prognoz kontenera](#query-the-containers-prediction-endpoint). 

## <a name="run-the-container-with-docker-run"></a>Uruchom kontener za pomocą `docker run`

Użyj [platformy docker, uruchom](https://docs.docker.com/engine/reference/commandline/run/) polecenie do uruchomienia kontenera. Polecenie używa następujących parametrów:

| Symbol zastępczy | Wartość |
|-------------|-------|
|{BILLING_KEY} | Ten klucz jest używany do uruchamiania kontenera i jest dostępny na stronie rozpoznawanie tekstu klucze w witrynie Azure portal.  |
|{BILLING_ENDPOINT_URI} | Rozliczeń punkt końcowy wartość identyfikatora URI.|

Zastąp parametry te wartości w poniższym przykładzie `docker run` polecenia.

```bash
docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 \
containerpreview.azurecr.io/microsoft/cognitive-services-recognize-text \
Eula=accept \
Billing={BILLING_ENDPOINT_URI} \
ApiKey={BILLING_KEY}
```

To polecenie:

* Uruchamia kontener rozpoznawaj z obrazu kontenera
* Przydziela jeden rdzeń procesora CPU i 4 gigabajty (GB) pamięci
* Uwidacznia TCP port 5000 i przydziela pseudo-TTY kontenera
* Automatycznie usuwa kontener po jej zakończenia. Obraz kontenera jest nadal dostępna na komputerze-hoście. 

Więcej [przykłady](./computer-vision-resource-container-config.md#example-docker-run-commands) z `docker run` polecenia są dostępne. 

> [!IMPORTANT]
> `Eula`, `Billing`, I `ApiKey` opcje muszą być określone w celu uruchomienia kontenera; w przeciwnym razie nie uruchamia się kontener.  Aby uzyskać więcej informacji, zobacz [rozliczeń](#billing).

[!INCLUDE [Running multiple containers on the same host](../../../includes/cognitive-services-containers-run-multiple-same-host.md)]


## <a name="query-the-containers-prediction-endpoint"></a>Zapytanie do endpoint prognoz kontenera

Kontener udostępnia punkt końcowy usługi oparte na protokole REST zapytania prognozowania interfejsów API. 

Użyj hosta, `https://localhost:5000`, dla kontenera interfejsów API.

### <a name="asynchronous-text-recognition"></a>Rozpoznawanie tekstu asynchroniczne

Możesz użyć `POST /vision/v2.0/recognizeText` i `GET /vision/v2.0/textOperations/*{id}*` operacje z optymalizacją asynchronicznie, rozpoznawał drukowanego tekstu w obrazie, podobnie jak Usługa przetwarzania obrazów używa tych odpowiednich operacji REST. Kontener Rozpoznaj tekst rozpoznaje tylko drukowanego tekstu, tekst nie pisma odręcznego w tej chwili więc `mode` parametr zwykle określony dla operacji przetwarzania obrazów usługi jest ignorowana przez kontener rozpoznawanie tekstu.

### <a name="synchronous-text-recognition"></a>Rozpoznawanie tekstu synchroniczne

Możesz użyć `POST /vision/v2.0/recognizeTextDirect` operacji synchronicznie, rozpoznawał drukowanego tekstu w obrazie. Ponieważ ta operacja jest synchroniczne, treść żądania dla tej operacji jest taka sama, jak dla `POST /vision/v2.0/recognizeText` operacja, ale odpowiedź treści dla tej operacji jest taka sama jak zwracany przez `GET /vision/v2.0/textOperations/*{id}*` operacji.

<!--  ## Validate container is running -->

[!INCLUDE [Container's API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]


## <a name="stop-the-container"></a>Zastavit kontejner

[!INCLUDE [How to stop the container](../../../includes/cognitive-services-containers-stop.md)]

## <a name="troubleshooting"></a>Rozwiązywanie problemów

Jeśli uruchamiasz kontener z danymi wyjściowymi [instalacji](./computer-vision-resource-container-config.md#mount-settings) i Rejestrowanie włączone, kontener generuje pliki dziennika, które są przydatne do rozwiązywania problemów, które wystąpiło podczas uruchamianie kontenera. 


## <a name="billing"></a>Rozliczenia

Wyślij kontenery Rozpoznaj tekst rozliczeń informacje na platformie Azure, przy użyciu _Rozpoznaj tekst_ zasobów dla konta systemu Azure. 

[!INCLUDE [Container's Billing Settings](../../../includes/cognitive-services-containers-how-to-billing-info.md)]

Aby uzyskać więcej informacji o tych opcjach, zobacz [skonfigurować kontenery](./computer-vision-resource-container-config.md).

## <a name="summary"></a>Podsumowanie

W tym artykule przedstawiono pojęcia i przepływ pracy na potrzeby pobierania, instalowania i uruchamiania kontenerów rozpoznawanie tekstu. Podsumowanie:

* Rozpoznawanie tekstu zapewnia kontener systemu Linux dla platformy Docker, zawieranie rozpoznawanie tekstu.
* Obrazy kontenerów są pobierane z rejestru kontenerów firmy Microsoft (MCR) na platformie Azure.
* Obrazy kontenera Uruchom na platformie Docker.
* Można użyć interfejsu API REST lub zestawu SDK do wywoływania operacji w kontenerach rozpoznawanie tekstu, określając host identyfikatora URI kontenera.
* Należy określić informacje rozliczeniowe, podczas tworzenia wystąpienia kontenera.

> [!IMPORTANT]
> Kontenery usługi cognitive Services nie są licencjonowane do uruchomienia bez połączenia z platformy Azure do zbierania danych. Klienci muszą włączyć kontener, aby komunikować informacje rozliczeniowe usłudze zliczania przez cały czas. Kontenery usługi cognitive Services nie wysyłaj danych klienta (na przykład obraz lub tekst, który jest analizowana) do firmy Microsoft.

## <a name="next-steps"></a>Kolejne kroki

* Przegląd [skonfigurować kontenery](computer-vision-resource-container-config.md) ustawień konfiguracji
* Przegląd [omówienie wizualizacji komputerowej](Home.md) dowiedzieć się więcej o rozpoznawaniu tekstu drukowanego i pisma odręcznego  
* Zapoznaj się [interfejs API przetwarzania obrazów](//westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa) szczegółowe informacje na temat metod obsługiwanych przez kontener.
* Zapoznaj się [— często zadawane pytania (FAQ)](FAQ.md) Aby rozwiązać problemy związane z funkcji przetwarzania obrazów.
* Użycie [kontenerów usługi Cognitive Services](../cognitive-services-container-support.md)
