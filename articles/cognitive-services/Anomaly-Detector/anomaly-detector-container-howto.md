---
title: Jak zainstalować i uruchomić kontenery do korzystania z interfejsu API detektora anomalii
titleSuffix: Azure Cognitive Services
description: Zaawansowane algorytmy interfejsu API detektora anomalii umożliwia identyfikowanie anomalii w danych szeregów czasowych.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: conceptual
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: fa25d27e99a9516d461a84dde184e2a6412baa0b
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/08/2020
ms.locfileid: "80875055"
---
# <a name="install-and-run-anomaly-detector-containers-preview"></a>Instalowanie i uruchamianie kontenerów detektora anomalii (wersja zapoznawcza)

Detektor anomalii posiada następującą funkcję kontenera:

| Funkcja | Funkcje |
|--|--|
| Wykrywacz anomalii | <li> Wykrywa anomalie, które występują w czasie rzeczywistym. <li> Wykrywa anomalie w całym zestawie danych jako partia. <li> Wywnioskować oczekiwany normalny zakres danych. <li> Obsługuje regulację czułości wykrywania anomalii, aby lepiej dopasować dane. |

Szczegółowe informacje na temat interfejsów API można znaleźć na:
* [Dowiedz się więcej o usłudze INTERFEJSU API detektora anomalii](https://go.microsoft.com/fwlink/?linkid=2080698&clcid=0x409)

Jeśli nie masz subskrypcji platformy Azure, utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) przed rozpoczęciem.

## <a name="prerequisites"></a>Wymagania wstępne

Przed użyciem kontenerów detektora anomalii należy spełnić następujące wymagania wstępne:

|Wymagany|Przeznaczenie|
|--|--|
|Aparat platformy Docker| Aparat platformy Docker jest zainstalowany na [komputerze-hoście.](#the-host-computer) Platforma Docker udostępnia pakiety, które konfigurują środowisko platformy Docker w systemach [macOS](https://docs.docker.com/docker-for-mac/), [Windows](https://docs.docker.com/docker-for-windows/) i [Linux](https://docs.docker.com/engine/installation/#supported-platforms). Aby uzyskać podstawowe informacje na temat platformy Docker i kontenerów, zapoznaj się z artykułem [Docker overview](https://docs.docker.com/engine/docker-overview/) (Przegląd platformy Docker).<br><br> Platforma Docker musi być skonfigurowana tak, aby zezwalała kontenerom na łączenie się z danymi rozliczeń i wysyłanie ich na platformę Azure. <br><br> **W systemie Windows**program Docker musi być również skonfigurowany do obsługi kontenerów systemu Linux.<br><br>|
|Znajomość platformy Docker | Powinieneś mieć podstawową wiedzę na temat pojęć platformy Docker, takich jak rejestry, repozytoria, kontenery i obrazy kontenerów, a także znajomość podstawowych `docker` poleceń.| 
|Zasób detektora anomalii |Aby korzystać z tych pojemników, musisz mieć:<br><br>Zasób usługi Azure _Anomaly Detector,_ aby uzyskać skojarzony klucz interfejsu API i identyfikator URI punktu końcowego. Obie wartości są dostępne na stronie **Detektor anomalii** i klucze usługi Azure portal i są wymagane do uruchomienia kontenera.<br><br>**{API_KEY}**: Jeden z dwóch dostępnych kluczy zasobów na stronie **Klucze**<br><br>**{ENDPOINT_URI}**: Punkt końcowy podany na stronie **Przegląd**|

[!INCLUDE [Gathering required container parameters](../containers/includes/container-gathering-required-parameters.md)]

## <a name="request-access-to-the-container-registry"></a>Żądanie dostępu do rejestru kontenerów

Najpierw należy wypełnić i przesłać [formularz żądania kontenera detektora anomalii,](https://aka.ms/adcontainer) aby zażądać dostępu do kontenera.

[!INCLUDE [Request access to the container registry](../../../includes/cognitive-services-containers-request-access-only.md)]

[!INCLUDE [Authenticate to the container registry](../../../includes/cognitive-services-containers-access-registry.md)]

## <a name="the-host-computer"></a>Komputer-host

[!INCLUDE [Host Computer requirements](../../../includes/cognitive-services-containers-host-computer.md)]

<!--* [Azure IoT Edge](https://docs.microsoft.com/azure/iot-edge/). For instructions of deploying Anomaly Detector module in IoT Edge, see [How to deploy Anomaly Detector module in IoT Edge](how-to-deploy-anomaly-detector-module-in-iot-edge.md).-->

### <a name="container-requirements-and-recommendations"></a>Wymagania i zalecenia dotyczące kontenerów

W poniższej tabeli opisano minimalne i zalecane rdzenie procesora CPU i pamięć do przydzielenia dla kontenera detektora anomalii.

| QPS (zapytania na sekundę) | Minimalne | Zalecane |
|-----------|---------|-------------|
| 10 k.p.m. | 4-rdzeniowa pamięć 1 GB | 8-rdzeniowa pamięć 2 GB |
| 20 qps | 8-rdzeniowa pamięć 2 GB | 16-rdzeniowa pamięć 4 GB |

Każdy rdzeń musi mieć co najmniej 2,6 gigaherca (GHz) lub więcej.

Rdzeń i pamięć `--cpus` `--memory` odpowiadają i ustawienia, które `docker run` są używane jako część polecenia.

## <a name="get-the-container-image-with-docker-pull"></a>Pobierz obraz kontenera za pomocą`docker pull`

Użyj [`docker pull`](https://docs.docker.com/engine/reference/commandline/pull/) polecenia, aby pobrać obraz kontenera.

| Kontener | Repozytorium |
|-----------|------------|
| cognitive-services-anomaly-detector | `containerpreview.azurecr.io/microsoft/cognitive-services-anomaly-detector:latest` |

<!--
For a full description of available tags, such as `latest` used in the preceding command, see [anomaly-detector](https://go.microsoft.com/fwlink/?linkid=2083827&clcid=0x409) on Docker Hub.
-->
[!INCLUDE [Tip for using docker list](../../../includes/cognitive-services-containers-docker-list-tip.md)]

### <a name="docker-pull-for-the-anomaly-detector-container"></a>Ściąganie docker dla kontenera detektora anomalii

```Docker
docker pull containerpreview.azurecr.io/microsoft/cognitive-services-anomaly-detector:latest
```

## <a name="how-to-use-the-container"></a>Jak korzystać z pojemnika

Gdy kontener znajduje się na [komputerze-hoście,](#the-host-computer)użyj następującego procesu do pracy z kontenerem.

1. [Uruchom kontener](#run-the-container-with-docker-run)z wymaganymi ustawieniami rozliczeń. Dostępnych jest więcej `docker run` [przykładów](anomaly-detector-container-configuration.md#example-docker-run-commands) polecenia.
1. [Kwerenda punktu końcowego przewidywania kontenera](#query-the-containers-prediction-endpoint).

## <a name="run-the-container-with-docker-run"></a>Uruchom pojemnik za pomocą`docker run`

Użyj polecenia [uruchamiania platformy docker,](https://docs.docker.com/engine/reference/commandline/run/) aby uruchomić kontener. Szczegółowe informacje na temat sposobu uzyskania wartości `{ENDPOINT_URI}` `{API_KEY}` można znaleźć w przypadku [zbierania wymaganych parametrów.](#gathering-required-parameters)

[Dostępne](anomaly-detector-container-configuration.md#example-docker-run-commands) są `docker run` przykłady polecenia.

```bash
docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 \
containerpreview.azurecr.io/microsoft/cognitive-services-anomaly-detector:latest \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

To polecenie:

* Uruchamia kontener detektora anomalii z obrazu kontenera
* Przydziela jeden rdzeń procesora i 4 gigabajty (GB) pamięci
* Udostępnia port TCP 5000 i przydziela pseudo-TTY dla kontenera
* Automatycznie usuwa kontener po jego zamknięciu. Obraz kontenera jest nadal dostępny na komputerze-hoście. 

> [!IMPORTANT]
> Aby `Eula` `Billing`uruchomić `ApiKey` kontener, należy określić opcje i opcje; w przeciwnym razie kontener nie zostanie uruchomiony.  Aby uzyskać więcej informacji, zobacz [Rozliczenia](#billing).

### <a name="running-multiple-containers-on-the-same-host"></a>Uruchamianie wielu kontenerów na tym samym hoście

Jeśli zamierzasz uruchomić wiele kontenerów z narażonymi portami, upewnij się, że każdy kontener jest uruchamiany z innym portem. Na przykład uruchom pierwszy kontener na porcie 5000 i drugi kontener na porcie 5001.

`<container-registry>` Wymień `<container-name>` i z wartościami kontenerów, których używasz. Nie muszą to być ten sam pojemnik. Kontener detektora anomalii i kontener usługi LUIS uruchomiony na hoście razem lub można uruchomić wiele kontenerów wykrywania anomalii. 

Uruchom pierwszy kontener na porcie 5000. 

```bash 
docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 \
<container-registry>/microsoft/<container-name> \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

Uruchom drugi kontener na porcie 5001.


```bash 
docker run --rm -it -p 5000:5001 --memory 4g --cpus 1 \
<container-registry>/microsoft/<container-name> \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

Każdy kolejny kontener powinien znajdować się na innym porcie. 

## <a name="query-the-containers-prediction-endpoint"></a>Kwerenda punktu końcowego przewidywania kontenera

Kontener zawiera interfejsy API punktu końcowego przewidywania zapytań oparte na rest. 

Użyj hosta, http://localhost:5000dla interfejsów API kontenera.

<!--  ## Validate container is running -->

[!INCLUDE [Container's API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]

## <a name="stop-the-container"></a>Zatrzymywanie kontenera

[!INCLUDE [How to stop the container](../../../includes/cognitive-services-containers-stop.md)]

## <a name="troubleshooting"></a>Rozwiązywanie problemów

Jeśli uruchomisz kontener z [włączoną instalacją](anomaly-detector-container-configuration.md#mount-settings) wyjściową i rejestrowaniem, kontener generuje pliki dziennika, które są przydatne do rozwiązywania problemów, które występują podczas uruchamiania lub uruchamiania kontenera.

[!INCLUDE [Cognitive Services FAQ note](../containers/includes/cognitive-services-faq-note.md)]

## <a name="billing"></a>Rozliczenia

Kontenery detektora anomalii wysyłają informacje rozliczeniowe na platformę Azure przy użyciu _zasobu Wykrywania anomalii_ na koncie platformy Azure. 

[!INCLUDE [Container's Billing Settings](../../../includes/cognitive-services-containers-how-to-billing-info.md)]

Aby uzyskać więcej informacji na temat tych opcji, zobacz [Konfigurowanie kontenerów](anomaly-detector-container-configuration.md).

<!--blogs/samples/video coures -->

[!INCLUDE [Discoverability of more container information](../../../includes/cognitive-services-containers-discoverability.md)]

## <a name="summary"></a>Podsumowanie

W tym artykule poznaliście pojęcia i przepływ pracy do pobierania, instalowania i uruchamiania kontenerów detektora anomalii. Podsumowanie:

* Anomaly Detector zapewnia jeden kontener systemu Linux dla platformy Docker, hermetyzując wykrywanie anomalii za pomocą partii vs przesyłania strumieniowego, wnioskowanie oczekiwanego zakresu i dostrajanie czułości.
* Obrazy kontenerów są pobierane z prywatnego rejestru kontenerów platformy Azure dedykowanego dla kontenerów w wersji zapoznawczej.
* Obrazy kontenerów są uruchamiane w usłudze Docker.
* Można użyć interfejsu API REST lub SDK do wywoływania operacji w kontenerach detektora anomalii, określając identyfikator URI hosta kontenera.
* Podczas tworzenia wystąpienia kontenera należy określić informacje rozliczeniowe.

> [!IMPORTANT]
> Kontenery usług Cognitive Services nie są licencjonowane do uruchamiania bez połączenia z platformą Azure w celu pomiaru. Klienci muszą włączyć kontenery do przekazywania informacji rozliczeniowych z usługą pomiaru przez cały czas. Kontenery usług Cognitive Services nie wysyłają danych klientów (np. analizowanych danych szeregów czasowych) do firmy Microsoft.

## <a name="next-steps"></a>Następne kroki

* Przeglądanie [Konfigurowanie kontenerów](anomaly-detector-container-configuration.md) pod kątem ustawień konfiguracji
* [Wdrażanie kontenera detektora anomalii w wystąpieniach kontenerów platformy Azure](how-to/deploy-anomaly-detection-on-container-instances.md)
* [Dowiedz się więcej o usłudze INTERFEJSU API detektora anomalii](https://go.microsoft.com/fwlink/?linkid=2080698&clcid=0x409)
