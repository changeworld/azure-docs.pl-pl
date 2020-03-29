---
title: Instalowanie i uruchamianie kontenerów - Face
titleSuffix: Azure Cognitive Services
description: W tym artykule pokazano, jak pobrać, zainstalować i uruchomić kontenery dla twarzy w tym samouczku.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 11/21/2019
ms.author: dapine
ms.openlocfilehash: e467b195ab1e2124286bfef74d7d1b71a4d99dd6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "76165993"
---
# <a name="install-and-run-face-containers-preview"></a>Instalowanie i uruchamianie kontenerów łajdaszka (wersja zapoznawcza)

Usługa Azure Cognitive Services Face udostępnia standardowy kontener systemu Linux dla platformy Docker, który wykrywa ludzkie twarze na obrazach. Identyfikuje również atrybuty, które obejmują punkty orientacyjne twarzy, takie jak nosy i oczy, płeć, wiek i inne cechy twarzy przewidywane maszynowo. Oprócz wykrywania face może sprawdzić, czy dwie ściany na tym samym obrazie lub różnych obrazach są takie same, używając wyniku zaufania. Ściana może również porównywać twarze z bazą danych, aby sprawdzić, czy podobna lub identyczna twarz już istnieje. Może również organizować podobne twarze w grupy przy użyciu wspólnych cech wizualnych.

Jeśli nie masz subskrypcji platformy Azure, utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) przed rozpoczęciem.

## <a name="prerequisites"></a>Wymagania wstępne

Przed użyciem kontenerów usługi Face należy spełnić następujące wymagania wstępne.

|Wymagany|Przeznaczenie|
|--|--|
|Aparat platformy Docker| Aparat platformy Docker musi być zainstalowany na [komputerze-hoście](#the-host-computer). Platforma Docker udostępnia pakiety, które konfigurują środowisko platformy Docker w systemach [macOS](https://docs.docker.com/docker-for-mac/), [Windows](https://docs.docker.com/docker-for-windows/) i [Linux](https://docs.docker.com/engine/installation/#supported-platforms). Aby uzyskać podstawowe informacje na temat platformy Docker i kontenerów, zapoznaj się z artykułem [Docker overview](https://docs.docker.com/engine/docker-overview/) (Przegląd platformy Docker).<br><br> Platforma Docker musi być skonfigurowana tak, aby zezwalała kontenerom na łączenie się z danymi rozliczeń i wysyłanie ich na platformę Azure. <br><br> W systemie Windows docker również musi być skonfigurowany do obsługi kontenerów systemu Linux.<br><br>|
|Znajomość platformy Docker | Potrzebne jest podstawowe zrozumienie pojęć platformy Docker, takich jak rejestry, repozytoria, kontenery i obrazy kontenerów. Potrzebna jest również `docker` znajomość podstawowych poleceń.| 
|Zasób twarzy |Aby korzystać z kontenera, musisz mieć:<br><br>**Zasób** usługi Azure Face i skojarzony klucz interfejsu API i identyfikator URI punktu końcowego. Obie wartości są dostępne na stronach **Przegląd** i **Klucze** dla zasobu. Są one wymagane do uruchomienia kontenera.<br><br>**{API_KEY}**: Jeden z dwóch dostępnych kluczy zasobów na stronie **Klucze**<br><br>**{ENDPOINT_URI}**: Punkt końcowy podany na stronie **Przegląd**

[!INCLUDE [Gathering required container parameters](../containers/includes/container-gathering-required-parameters.md)]

## <a name="request-access-to-the-private-container-registry"></a>Żądanie dostępu do rejestru kontenerów prywatnych

[!INCLUDE [Request access to private container registry](../../../includes/cognitive-services-containers-request-access.md)]

### <a name="the-host-computer"></a>Komputer-host

[!INCLUDE [Host Computer requirements](../../../includes/cognitive-services-containers-host-computer.md)]

### <a name="container-requirements-and-recommendations"></a>Wymagania i zalecenia dotyczące kontenerów

W poniższej tabeli opisano minimalne i zalecane rdzenie procesora CPU i pamięć do przydzielenia dla każdego kontenera usługi Face.

| Kontener | Minimalne | Zalecane | Transakcje na sekundę<br>(Minimum, maksimum)|
|-----------|---------|-------------|--|
|Rozpoznawanie twarzy | 1 rdzeń, 2 GB pamięci | 1 rdzeń, 4 GB pamięci |10, 20|

* Każdy rdzeń musi mieć co najmniej 2,6 GHz lub więcej.
* Transakcje na sekundę (TPS).

Rdzeń i pamięć `--cpus` `--memory` odpowiadają i ustawienia, które `docker run` są używane jako część polecenia.

## <a name="get-the-container-image-with-docker-pull"></a>Pobierz obraz kontenera za pomocą pociągnięcia docker

Dostępne są obrazy kontenerów dla usługi Twarz. 

| Kontener | Repozytorium |
|-----------|------------|
| Rozpoznawanie twarzy | `containerpreview.azurecr.io/microsoft/cognitive-services-face:latest` |

[!INCLUDE [Tip for using docker list](../../../includes/cognitive-services-containers-docker-list-tip.md)]

### <a name="docker-pull-for-the-face-container"></a>Ciągnięcie docker dla kontenera Ściana

```
docker pull containerpreview.azurecr.io/microsoft/cognitive-services-face:latest
```

## <a name="use-the-container"></a>Użyj pojemnika

Po kontener znajduje się na [komputerze-hoście](#the-host-computer), użyj następującego procesu do pracy z kontenerem.

1. [Uruchom kontener z](#run-the-container-with-docker-run) wymaganymi ustawieniami rozliczeń. Dostępnych jest więcej `docker run` [przykładów](./face-resource-container-config.md#example-docker-run-commands) polecenia. 
1. [Kwerenda punktu końcowego przewidywania kontenera](#query-the-containers-prediction-endpoint). 

## <a name="run-the-container-with-docker-run"></a>Uruchamianie kontenera z uruchomieniem platformy docker

Użyj polecenia [uruchamiania platformy docker,](https://docs.docker.com/engine/reference/commandline/run/) aby uruchomić kontener. Szczegółowe informacje na temat sposobu uzyskania wartości `{ENDPOINT_URI}` `{API_KEY}` można znaleźć w przypadku [zbierania wymaganych parametrów.](#gathering-required-parameters)

[Dostępne](face-resource-container-config.md#example-docker-run-commands) są `docker run` przykłady polecenia.

```bash
docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 \
containerpreview.azurecr.io/microsoft/cognitive-services-face \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

To polecenie:

* Uruchamia kontener twarzy z obrazu kontenera.
* Przydziela jeden rdzeń procesora i 4 GB pamięci.
* Udostępnia port TCP 5000 i przydziela pseudo TTY dla kontenera.
* Automatycznie usuwa kontener po jego zamknięciu. Obraz kontenera jest nadal dostępny na komputerze-hoście. 

Dostępnych jest więcej `docker run` [przykładów](./face-resource-container-config.md#example-docker-run-commands) polecenia. 

> [!IMPORTANT]
> Aby `Eula` `Billing`uruchomić `ApiKey` kontener lub kontener nie zostanie uruchomiony, należy określić opcje i opcje. Aby uzyskać więcej informacji, zobacz [Rozliczenia](#billing).

[!INCLUDE [Running multiple containers on the same host](../../../includes/cognitive-services-containers-run-multiple-same-host.md)]


## <a name="query-the-containers-prediction-endpoint"></a>Kwerenda punktu końcowego przewidywania kontenera

Kontener zawiera interfejsy API punktu końcowego przewidywania zapytań oparte na rest. 

Użyj hosta, `http://localhost:5000`dla interfejsów API kontenera.


<!--  ## Validate container is running -->

[!INCLUDE [Container's API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]

## <a name="stop-the-container"></a>Zatrzymywanie kontenera

[!INCLUDE [How to stop the container](../../../includes/cognitive-services-containers-stop.md)]

## <a name="troubleshooting"></a>Rozwiązywanie problemów

Jeśli uruchomisz kontener z [instalacją](./face-resource-container-config.md#mount-settings) wyjściową i rejestrowanie jest włączone, kontener generuje pliki dziennika, które są przydatne do rozwiązywania problemów, które występują podczas uruchamiania lub uruchamiania kontenera.

[!INCLUDE [Cognitive Services FAQ note](../containers/includes/cognitive-services-faq-note.md)]

## <a name="billing"></a>Rozliczenia

Kontenery usługi Face wysyłają informacje rozliczeniowe do platformy Azure przy użyciu zasobu face na koncie platformy Azure. 

[!INCLUDE [Container's Billing Settings](../../../includes/cognitive-services-containers-how-to-billing-info.md)]

Aby uzyskać więcej informacji na temat tych opcji, zobacz [Konfigurowanie kontenerów](./face-resource-container-config.md).

<!--blogs/samples/video coures -->

[!INCLUDE [Discoverability of more container information](../../../includes/cognitive-services-containers-discoverability.md)]

## <a name="summary"></a>Podsumowanie

W tym artykule poznaliście pojęcia i przepływ pracy dotyczące pobierania, instalowania i uruchamiania kontenerów usługi Face. Podsumowanie:

* Obrazy kontenerów są pobierane z rejestru kontenerów platformy Azure.
* Obrazy kontenerów są uruchamiane w usłudze Docker.
* Można użyć interfejsu API REST lub SDK do wywoływania operacji w kontenerach usługi Face, określając identyfikator URI hosta kontenera.
* Podczas tworzenia wystąpienia kontenera należy określić informacje rozliczeniowe.

> [!IMPORTANT]
> Kontenery usług Cognitive Services nie są licencjonowane do uruchamiania bez połączenia z platformą Azure w celu pomiaru. Klienci muszą włączyć kontenery do przekazywania informacji rozliczeniowych z usługą pomiaru przez cały czas. Kontenery usług Cognitive Services nie wysyłają do firmy Microsoft danych klientów, takich jak analizowany obraz lub tekst.

## <a name="next-steps"></a>Następne kroki

* Aby zapoznać się z ustawieniami konfiguracji, zobacz [Konfigurowanie kontenerów](face-resource-container-config.md).
* Aby dowiedzieć się więcej o wykrywaniu i identyfikowaniu twarzy, zobacz [Omówienie twarzy](Overview.md).
* Aby uzyskać informacje na temat metod obsługiwanych przez kontener, zobacz [interfejs API rozpoznawania twarzy](//westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236).
* Aby użyć większej liczby kontenerów usług Cognitive Services, zobacz [kontenery usług Cognitive Services](../cognitive-services-container-support.md).
