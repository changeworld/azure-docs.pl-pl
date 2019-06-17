---
title: Instalowanie i uruchamianie kontenerów — wykrywanie anomalii
titleSuffix: Azure Cognitive Services
description: Użyj zaawansowanych algorytmów API wykrywanie anomalii, aby identyfikować anomalie w danych szeregów czasowych.
services: cognitive-services
author: IEvangelist
ms.service: cognitive-services
ms.subservice: anomaly-detection
ms.topic: article
ms.date: 06/10/2019
ms.author: dapine
ms.openlocfilehash: 0369e27e7a260c50fc4de690dd3261421356799a
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "67073328"
---
# <a name="install-and-run-anomaly-detector-containers"></a>Instalowanie i uruchamianie kontenerów wykrywanie anomalii

Wykrywanie anomalii ma następujących kontenerów: 

|Funkcja|Funkcje|
|-|-|
|Narzędzie do wykrywania anomalii| <li> Wykrywa anomalie, w jakiej występują w czasie rzeczywistym. <li> Wykrywa anomalie przez cały zestaw danych jako zadania wsadowego. <li> Wnioskuje oczekiwanym zakresem normalne dane. <li> Obsługuje anomalii wykrywanie czułości korekta lepsze dopasowanie danych. |

Aby uzyskać szczegółowe informacje na temat interfejsów API Zobacz:
* [Dowiedz się więcej o usłudze API wykrywanie anomalii](https://go.microsoft.com/fwlink/?linkid=2080698&clcid=0x409)

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem korzystania z kontenerów wykrywanie anomalii, musi spełniać następujące wymagania wstępne:

|Wymagane|Przeznaczenie|
|--|--|
|Aparat platformy docker| Aparat platformy Docker zainstalowany na musisz [komputerze-hoście](#the-host-computer). Środowisko docker zawiera pakiety, które konfigurują środowisko platformy Docker na [macOS](https://docs.docker.com/docker-for-mac/), [Windows](https://docs.docker.com/docker-for-windows/), i [Linux](https://docs.docker.com/engine/installation/#supported-platforms). Aby uzyskać podstawowe informacje na temat platformy Docker i kontenerów, zobacz [Docker — omówienie](https://docs.docker.com/engine/docker-overview/).<br><br> Docker należy skonfigurować w taki sposób, aby umożliwić kontenerów, aby nawiązać połączenie z, a następnie wysyłać danych dotyczących rozliczeń do platformy Azure. <br><br> **Na Windows**, platformy Docker musi być również skonfigurowany do obsługi kontenerów systemu Linux.<br><br>|
|Znajomość platformy Docker | Należy mieć podstawową wiedzę na temat pojęć usługi Docker, takich jak rejestry, repozytoria, kontenery i obrazów kontenerów, a także wiedzę na temat basic `docker` poleceń.| 
|Zasób wykrywanie anomalii |Aby można było używać tych kontenerów, musisz mieć:<br><br>_Wykrywanie anomalii_ zasobów platformy Azure, aby uzyskać skojarzonego klucza rozliczenia i rozliczeń identyfikator URI punktu końcowego. Obie wartości są dostępne na stronach witryny Azure portal — omówienie wykrywanie anomalii i klucze i są wymagane do uruchomienia kontenera.<br><br>**{BILLING_KEY}** : klucz zasobu<br><br>**{BILLING_ENDPOINT_URI}** : przykład identyfikatora URI punktu końcowego: `https://westus2.api.cognitive.microsoft.com`|

## <a name="request-access-to-the-container-registry"></a>Żądanie dostępu do rejestru kontenerów

Najpierw należy wypełnić oraz przesłać [formularz wniosku kontenera wykrywanie anomalii](https://aka.ms/adcontainer) Aby zażądać dostępu do kontenera.

[!INCLUDE [Request access to the container registry](../../../includes/cognitive-services-containers-request-access-only.md)]

[!INCLUDE [Authenticate to the container registry](../../../includes/cognitive-services-containers-access-registry.md)]

## <a name="the-host-computer"></a>Komputer-host

[!INCLUDE [Host Computer requirements](../../../includes/cognitive-services-containers-host-computer.md)]

<!--* [Azure IoT Edge](https://docs.microsoft.com/azure/iot-edge/). For instructions of deploying Anomaly Detector module in IoT Edge, see [How to deploy Anomaly Detector module in IoT Edge](how-to-deploy-anomaly-detector-module-in-iot-edge.md).-->

### <a name="container-requirements-and-recommendations"></a>Kontener wymagania i zalecenia

W poniższej tabeli opisano minimalne i zalecane rdzeni procesora CPU i pamięci do przydzielenia dla kontenera wykrywanie anomalii.

| Liczby zapytań na Sekundę (zapytań na sekundę) | Minimalne | Zalecane |
|-----------|---------|-------------|
| 10 QPS | 4 podstawowe, 1GB pamięci | 8 rdzeni 2GB pamięci |
| 20 QPS | 8 rdzeni, 2GB pamięci | 16 rdzeni 4GB pamięci RAM |

Każdego rdzenia musi mieć co najmniej 2,6 gigaherc (GHz) lub szybszy.

Rdzeni i pamięci odpowiadają `--cpus` i `--memory` ustawienia, które są używane jako część `docker run` polecenia.

## <a name="get-the-container-image-with-docker-pull"></a>Pobierz obraz kontenera przy użyciu `docker pull`

Użyj [ `docker pull` ](https://docs.docker.com/engine/reference/commandline/pull/) polecenie, aby pobrać obraz kontenera.

| Kontener | Repozytorium |
|-----------|------------|
| cognitive-services-anomaly-detector | `containerpreview.azurecr.io/microsoft/cognitive-services-anomaly-detector:latest` |

<!--
For a full description of available tags, such as `latest` used in the preceding command, see [anomaly-detector](https://go.microsoft.com/fwlink/?linkid=2083827&clcid=0x409) on Docker Hub.
-->
[!INCLUDE [Tip for using docker list](../../../includes/cognitive-services-containers-docker-list-tip.md)]


### <a name="docker-pull-for-the-anomaly-detector-container"></a>Polecenie docker pull dla kontenera, wykrywanie anomalii

```Docker
docker pull containerpreview.azurecr.io/microsoft/cognitive-services-anomaly-detector:latest
```

## <a name="how-to-use-the-container"></a>Jak używać kontenera

Gdy kontener będzie na [komputerze-hoście](#the-host-computer), użyj następującego procesu do pracy z kontenerem.

1. [Uruchom kontener](#run-the-container-with-docker-run), za pomocą wymaganych rozliczeń ustawienia. Więcej [przykłady](anomaly-detector-container-configuration.md#example-docker-run-commands) z `docker run` polecenia są dostępne. 
1. [Zapytanie do endpoint prognoz kontenera](#query-the-containers-prediction-endpoint). 

## <a name="run-the-container-with-docker-run"></a>Uruchom kontener za pomocą `docker run`

Użyj [platformy docker, uruchom](https://docs.docker.com/engine/reference/commandline/run/) polecenie, aby uruchomić dowolne trzy kontenery. Polecenie używa następujących parametrów:

| Symbol zastępczy | Wartość |
|-------------|-------|
|{BILLING_KEY} | Ten klucz jest używany do uruchamiania kontenera i jest dostępny na stronie klucze wykrywanie anomalii w witrynie Azure portal.  |
|{BILLING_ENDPOINT_URI} | Rozliczeń wartość identyfikatora URI punktu końcowego jest dostępna na stronie Przegląd wykrywanie anomalii w witrynie Azure portal.|

Zastąp parametry te wartości w poniższym przykładzie `docker run` polecenia.

```bash
docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 \
containerpreview.azurecr.io/microsoft/cognitive-services-anomaly-detector:latest \
Eula=accept \
Billing={BILLING_ENDPOINT_URI} \
ApiKey={BILLING_KEY}
```

To polecenie:

* Uruchamia kontener usługi Wykrywanie anomalii z obrazu kontenera
* Przydziela jeden rdzeń procesora CPU i 4 gigabajty (GB) pamięci
* Uwidacznia TCP port 5000 i przydziela pseudo-TTY kontenera
* Automatycznie usuwa kontener po jej zakończenia. Obraz kontenera jest nadal dostępna na komputerze-hoście. 

> [!IMPORTANT]
> `Eula`, `Billing`, I `ApiKey` opcje muszą być określone w celu uruchomienia kontenera; w przeciwnym razie nie uruchamia się kontener.  Aby uzyskać więcej informacji, zobacz [rozliczeń](#billing).

### <a name="running-multiple-containers-on-the-same-host"></a>Uruchamianie wielu kontenerów na tym samym hoście

Jeśli zamierzasz uruchomić wiele kontenerów z uwidocznionymi portami, upewnij się, do uruchamiania każdego kontenera przy użyciu innego portu. Na przykład uruchomić pierwszego kontenera na porcie 5000 oraz drugi kontener na porcie 5001.

Zastąp `<container-registry>` i `<container-name>` wartościami kontenerów, możesz użyć. Te nie muszą być tego samego kontenera. Masz kontener wykrywanie anomalii i kontenerów usługi LUIS jednocześnie uruchomionych na HOŚCIE lub może mieć wiele kontenerów wykrywanie anomalii uruchomiona. 

Uruchamianie pierwszego kontenera na porcie 5000. 

```bash 
docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 \
<container-registry>/microsoft/<container-name> \
Eula=accept \
Billing={BILLING_ENDPOINT_URI} \
ApiKey={BILLING_KEY}
```

Uruchom drugiego kontenera na porcie 5001.


```bash 
docker run --rm -it -p 5000:5001 --memory 4g --cpus 1 \
<container-registry>/microsoft/<container-name> \
Eula=accept \
Billing={BILLING_ENDPOINT_URI} \
ApiKey={BILLING_KEY}
```

Każdy kontener kolejnych powinna być na innym porcie. 

## <a name="query-the-containers-prediction-endpoint"></a>Zapytanie do endpoint prognoz kontenera

Kontener udostępnia punkt końcowy usługi oparte na protokole REST zapytania prognozowania interfejsów API. 

Użyj hosta, https://localhost:5000, dla kontenera interfejsów API.

<!--  ## Validate container is running -->

[!INCLUDE [Container's API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]

## <a name="stop-the-container"></a>Zastavit kontejner

[!INCLUDE [How to stop the container](../../../includes/cognitive-services-containers-stop.md)]

## <a name="troubleshooting"></a>Rozwiązywanie problemów

Jeśli uruchamiasz kontener z danymi wyjściowymi [instalacji](anomaly-detector-container-configuration.md#mount-settings) i Rejestrowanie włączone, kontener generuje pliki dziennika, które są przydatne do rozwiązywania problemów, które wystąpiło podczas uruchamianie kontenera. 

## <a name="billing"></a>Rozliczenia

Wyślij kontenery wykrywanie anomalii w usłudze rozliczeń informacje na platformie Azure, przy użyciu _wykrywanie anomalii_ zasobów dla konta systemu Azure. 

[!INCLUDE [Container's Billing Settings](../../../includes/cognitive-services-containers-how-to-billing-info.md)]

Aby uzyskać więcej informacji o tych opcjach, zobacz [skonfigurować kontenery](anomaly-detector-container-configuration.md).

<!--blogs/samples/video coures -->

[!INCLUDE [Discoverability of more container information](../../../includes/cognitive-services-containers-discoverability.md)]

## <a name="summary"></a>Podsumowanie

W tym artykule przedstawiono pojęcia i przepływ pracy na potrzeby pobierania, instalowania i uruchamiania kontenerów wykrywanie anomalii. Podsumowanie:

* Wykrywanie anomalii zapewnia jeden kontener systemu Linux platformy Docker, zawieranie wykrywania anomalii za pomocą programu vs partii i przesyłania strumieniowego, wnioskowanie o oczekiwanym zakresem, dostrajanie ważność.
* Obrazy kontenerów są pobierane z prywatnej usługi Azure Container Registry dedykowane dla kontenerów (wersja zapoznawcza).
* Obrazy kontenera Uruchom na platformie Docker.
* Można użyć interfejsu API REST lub zestawu SDK do wywoływania operacji w kontenerach wykrywanie anomalii, określając host identyfikatora URI kontenera.
* Należy określić informacje rozliczeniowe, podczas tworzenia wystąpienia kontenera.

> [!IMPORTANT]
> Kontenery usługi cognitive Services nie są licencjonowane do uruchomienia bez połączenia z platformy Azure do zbierania danych. Klienci muszą włączyć kontener, aby komunikować informacje rozliczeniowe usłudze zliczania przez cały czas. Kontenery usługi cognitive Services nie wysyłaj danych klientów (np. danych szeregów czasowych analizowany) do firmy Microsoft.

## <a name="next-steps"></a>Kolejne kroki

* Przegląd [skonfigurować kontenery](anomaly-detector-container-configuration.md) ustawień konfiguracji
* [Dowiedz się więcej o usłudze API wykrywanie anomalii](https://go.microsoft.com/fwlink/?linkid=2080698&clcid=0x409)
