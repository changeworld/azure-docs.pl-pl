---
title: Instalowanie i uruchamianie kontenerów
titlesuffix: Face - Azure Cognitive Services
description: Pobieranie, instalowanie i uruchamianie kontenerów twarzy, w tym samouczku wskazówki.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: article
ms.date: 04/16/2019
ms.author: diberry
ms.openlocfilehash: 9bc18157a6f60555ea18901796199a3fe9dc4c22
ms.sourcegitcommit: 67625c53d466c7b04993e995a0d5f87acf7da121
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/20/2019
ms.locfileid: "65891222"
---
# <a name="install-and-run-face-containers"></a>Instalowanie i uruchamianie kontenerów twarzy

Azure twarzy usług Cognitive udostępnia standardowy kontener systemu Linux dla platformy Docker, która wykrywa twarze osób na obrazach. Określa on atrybutów, które obejmują to punktów charakterystycznych, takich jak elementy ruchome i Ty masz dostęp, płeć, wiek i inne funkcje twarzy przewidzieć maszyny. Oprócz wykrywania twarzy można sprawdzić, jeśli dwie twarze w ten sam obraz lub różne obrazy są takie same, za pomocą współczynnik ufności. Twarzy można również porównać twarzy w bazie danych, aby zobaczyć, czy wyszukiwanie podobnych lub takich samych rozpoznawania twarzy, już istnieje. On również organizować podobnych twarzy w grupy za pomocą udostępnionego visual traits.

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem używania kontenerów interfejsu API rozpoznawania twarzy, musi spełniać następujące wymagania wstępne.

|Wymagane|Przeznaczenie|
|--|--|
|Aparat platformy docker| Aparat platformy Docker musi być zainstalowany na [komputerze-hoście](#the-host-computer). Środowisko docker zawiera pakiety, które konfigurują środowisko platformy Docker na [macOS](https://docs.docker.com/docker-for-mac/), [Windows](https://docs.docker.com/docker-for-windows/), i [Linux](https://docs.docker.com/engine/installation/#supported-platforms). Aby uzyskać podstawowe informacje na temat platformy Docker i kontenerów, zobacz [Docker — omówienie](https://docs.docker.com/engine/docker-overview/).<br><br> Docker należy skonfigurować w taki sposób, aby umożliwić kontenerów, aby nawiązać połączenie z, a następnie wysyłać danych dotyczących rozliczeń do platformy Azure. <br><br> W Windows Docker również musi być skonfigurowany do obsługi kontenerów systemu Linux.<br><br>|
|Znajomość platformy Docker | Konieczne jest podstawową wiedzę na temat pojęć usługi Docker, takich jak rejestry repozytoriów, kontenery i obrazów kontenerów. Należy także wiedzę na temat basic `docker` poleceń.| 
|Azure `Cognitive Services` zasobów |Aby korzystać z kontenera, musisz mieć:<br><br>Zasób usługi Azure Cognitive Services i skojarzonego klucza rozliczenia i rozliczeń identyfikator URI punktu końcowego. Obie wartości są dostępne na **Przegląd** i **klucze** stron dla zasobu. Są one wymagane do uruchomienia kontenera. Dodaj `face/v1.0` routingu do identyfikatora URI punktu końcowego, jak pokazano w poniższym przykładzie BILLING_ENDPOINT_URI: <br><br>**{BILLING_KEY}** : klucz zasobu<br><br>**{BILLING_ENDPOINT_URI}** : przykład identyfikatora URI punktu końcowego `https://westus.api.cognitive.microsoft.com/face/v1.0`|

## <a name="request-access-to-the-private-container-registry"></a>Poproś o dostęp do prywatnego rejestru kontenerów

[!INCLUDE [Request access to private container registry](../../../includes/cognitive-services-containers-request-access.md)]

### <a name="the-host-computer"></a>Komputer-host

[!INCLUDE [Host Computer requirements](../../../includes/cognitive-services-containers-host-computer.md)]


### <a name="container-requirements-and-recommendations"></a>Kontener wymagania i zalecenia

W poniższej tabeli opisano minimalne i zalecane rdzeni procesora CPU i pamięci do przydzielenia dla każdego kontenera interfejsu API rozpoznawania twarzy.

| Kontener | Minimalne | Zalecane | Transakcje na sekundę<br>(Minimum, maksimum)|
|-----------|---------|-------------|--|
|Rozpoznawanie twarzy | 1 rdzeń, 2 GB pamięci RAM | 1 rdzeń, 4 GB pamięci RAM |10, 20|

* Każdego rdzenia musi mieć co najmniej 2,6 GHz lub szybszy.
* Transakcje na sekundę (TPS).

Rdzeni i pamięci odpowiadają `--cpus` i `--memory` ustawienia, które są używane jako część `docker run` polecenia.

## <a name="get-the-container-image-with-docker-pull"></a>Pobierz obraz kontenera przy użyciu polecenie docker pull

Dostępne są obrazy kontenerów dla interfejsu API rozpoznawania twarzy. 

| Kontener | Repozytorium |
|-----------|------------|
| Rozpoznawanie twarzy | `containerpreview.azurecr.io/microsoft/cognitive-services-face:latest` |

[!INCLUDE [Tip for using docker list](../../../includes/cognitive-services-containers-docker-list-tip.md)]

### <a name="docker-pull-for-the-face-container"></a>Polecenie docker pull dla kontenera twarzy

```
docker pull containerpreview.azurecr.io/microsoft/cognitive-services-face:latest
```

## <a name="use-the-container"></a>Użyj kontenera

Po włączeniu kontenera [komputerze-hoście](#the-host-computer), użyj następującego procesu do pracy z kontenerem.

1. [Uruchom kontener](#run-the-container-with-docker-run) przy użyciu wymaganych rozliczeń ustawienia. Więcej [przykłady](./face-resource-container-config.md#example-docker-run-commands) z `docker run` polecenia są dostępne. 
1. [Zapytanie do endpoint prognoz kontenera](#query-the-containers-prediction-endpoint). 

## <a name="run-the-container-with-docker-run"></a>Uruchom kontener za pomocą platformy docker, uruchom

Użyj [platformy docker, uruchom](https://docs.docker.com/engine/reference/commandline/run/) polecenie, aby uruchomić dowolne trzy kontenery. Polecenie używa następujących parametrów.

| Symbol zastępczy | Wartość |
|-------------|-------|
|{BILLING_KEY} | Ten klucz jest używany do uruchamiania kontenera i jest dostępna na platformie Azure `Cognitive Services` **klucze** strony. |
|{BILLING_ENDPOINT_URI} | Punkt końcowy rozliczeń wartość identyfikatora URI jest dostępna na platformie Azure `Cognitive Services` **Przegląd** strony. Może to być na przykład `https://westus.api.cognitive.microsoft.com/face/v1.0`.|

Dodaj `face/v1.0` routingu do identyfikatora URI punktu końcowego, jak pokazano w powyższym przykładzie BILLING_ENDPOINT_URI. 

Zastąp parametry te wartości w następującym `docker run` przykładowe polecenie:

```bash
docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 \
containerpreview.azurecr.io/microsoft/cognitive-services-face \
Eula=accept \
Billing={BILLING_ENDPOINT_URI} \
ApiKey={BILLING_KEY}
```

To polecenie:

* Uruchamia kontener twarzy z obrazu kontenera.
* Przydziela jeden rdzeń procesora CPU i 4 GB pamięci.
* Uwidacznia TCP port 5000 i przydziela pseudo TTY dla kontenera.
* Automatycznie usuwa kontener po jej zakończenia. Obraz kontenera jest nadal dostępna na komputerze-hoście. 

Więcej [przykłady](./face-resource-container-config.md#example-docker-run-commands) z `docker run` polecenia są dostępne. 

> [!IMPORTANT]
> `Eula`, `Billing`, I `ApiKey` opcje muszą być określone w celu uruchomienia kontenera lub nie uruchamia się kontener. Aby uzyskać więcej informacji, zobacz [rozliczeń](#billing).

[!INCLUDE [Running multiple containers on the same host](../../../includes/cognitive-services-containers-run-multiple-same-host.md)]


## <a name="query-the-containers-prediction-endpoint"></a>Zapytanie do endpoint prognoz kontenera

Kontener udostępnia punkt końcowy usługi oparte na protokole REST zapytania prognozowania interfejsów API. 

Użyj hosta, `https://localhost:5000`, dla kontenera interfejsów API.


<!--  ## Validate container is running -->

[!INCLUDE [Container's API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]

## <a name="stop-the-container"></a>Zastavit kontejner

[!INCLUDE [How to stop the container](../../../includes/cognitive-services-containers-stop.md)]

## <a name="troubleshooting"></a>Rozwiązywanie problemów

Jeśli uruchamiasz kontener z danymi wyjściowymi [instalacji](./face-resource-container-config.md#mount-settings) i rejestrowanie jest włączone, kontener generuje pliki dziennika, które są przydatne do rozwiązywania problemów, które odbywa się podczas uruchamiania lub uruchamiania kontenera. 


## <a name="billing"></a>Informacje billingowe

Kontenery interfejsu API rozpoznawania twarzy wysyłanie informacji dotyczących rozliczeń na platformie Azure przy użyciu zasobów interfejsu API rozpoznawania twarzy na koncie platformy Azure. 

[!INCLUDE [Container's Billing Settings](../../../includes/cognitive-services-containers-how-to-billing-info.md)]

Aby uzyskać więcej informacji o tych opcjach, zobacz [skonfigurować kontenery](./face-resource-container-config.md).

## <a name="summary"></a>Podsumowanie

W tym artykule przedstawiono pojęcia i przepływ pracy, jak pobrać, zainstalować i uruchamiać kontenery interfejsu API rozpoznawania twarzy. Podsumowanie:

* Interfejs API rozpoznawania twarzy zawiera trzy kontenery systemu Linux dla platformy Docker, która zapewnia wyodrębnianie kluczowych fraz, wykrywanie języka i analiza opinii.
* Obrazy kontenerów są pobierane z usługi Azure Container Registry.
* Obrazy kontenera Uruchom na platformie Docker.
* Można użyć interfejsu API REST lub zestawu SDK do wywoływania operacji w kontenerach interfejsu API rozpoznawania twarzy, określając host identyfikatora URI kontenera.
* Należy określić informacje rozliczeniowe, podczas tworzenia wystąpienia kontenera.

> [!IMPORTANT]
> Kontenery usługi cognitive Services nie są licencjonowane do uruchomienia bez połączenia z platformy Azure do zbierania danych. Klienci, należy włączyć kontener, aby komunikować informacje rozliczeniowe usłudze zliczania przez cały czas. Kontenery usługi cognitive Services nie wysyłaj danych klienta, takich jak obraz lub tekst, który jest analizowana, do firmy Microsoft.

## <a name="next-steps"></a>Kolejne kroki

* Aby uzyskać ustawienia konfiguracji, zobacz [skonfigurować kontenery](face-resource-container-config.md).
* Aby dowiedzieć się więcej o tym, jak i wykrywanie twarzy, zobacz [Przegląd twarzy](Overview.md).
* Aby uzyskać informacji dotyczących metod obsługiwanych przez kontener, zobacz [interfejsu API rozpoznawania twarzy](//westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236).
* Aby korzystać z większej liczbie kontenerów usług Cognitive Services, zobacz [kontenerów usług Cognitive Services](../cognitive-services-container-support.md).
