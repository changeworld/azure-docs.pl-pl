---
title: Instalowanie i uruchamianie kontenerów — Personalizer
titleSuffix: Azure Cognitive Services
description: Jak pobrać, zainstalować i uruchamiać kontenery dla Personalizer.
author: edjez
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: overview
ms.date: 05/09/2019
ms.author: edjez
ms.openlocfilehash: b591a8b5c738fd7a23e95fd04af3cc92b6d7d226
ms.sourcegitcommit: f6c85922b9e70bb83879e52c2aec6307c99a0cac
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/11/2019
ms.locfileid: "65538335"
---
# <a name="install-and-run-personalizer-containers"></a>Instalowanie i uruchamianie kontenerów Personalizer

Usługa Personalizer ma następujących kontenerów: 

|Funkcja|Funkcje|
|-|-|
|personalizer|Określić najlepsze działanie z bieżącego kontekstu, zawartość i użytkownika.|

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem korzystania z kontenerów usług Personalizer, musi spełniać następujące wymagania wstępne:

|Wymagane|Przeznaczenie|
|--|--|
|Aparat platformy docker| Aparat platformy Docker zainstalowany na musisz [komputerze-hoście](#the-host-computer). Środowisko docker zawiera pakiety, które konfigurują środowisko platformy Docker na [macOS](https://docs.docker.com/docker-for-mac/), [Windows](https://docs.docker.com/docker-for-windows/), i [Linux](https://docs.docker.com/engine/installation/#supported-platforms). Aby uzyskać podstawowe informacje na temat platformy Docker i kontenerów, zobacz [Docker — omówienie](https://docs.docker.com/engine/docker-overview/).<br><br> Docker należy skonfigurować w taki sposób, aby umożliwić kontenerów, aby nawiązać połączenie z, a następnie wysyłać danych dotyczących rozliczeń do platformy Azure. <br><br> **Na Windows**, platformy Docker musi być również skonfigurowany do obsługi kontenerów systemu Linux.<br><br>|
|Znajomość platformy Docker | Należy mieć podstawową wiedzę na temat pojęć usługi Docker, takich jak rejestry, repozytoria, kontenery i obrazów kontenerów, a także wiedzę na temat basic `docker` poleceń.| 
|Zasób usługi personalizer |Aby można było używać tych kontenerów, musisz mieć:<br><br>A _usługi Personalizer_ zasobów platformy Azure, aby uzyskać skojarzonego klucza rozliczenia i rozliczeń identyfikator URI punktu końcowego. Obie wartości są dostępne na stronach witryny Azure portal Personalizer Omówienie usługi i klucze i są wymagane do uruchomienia kontenera.<br><br>**{BILLING_KEY}** : klucz zasobu<br><br>**{BILLING_ENDPOINT_URI}** : przykład identyfikatora URI punktu końcowego: `https://westus.api.cognitive.microsoft.com/`|

### <a name="the-host-computer"></a>Komputer-host

**Hosta** komputer, który uruchamia kontener platformy docker. Może być komputer w środowisku lokalnym lub docker obsługującego usługę z platformy Azure, w tym:

* [Azure Kubernetes Service](https://docs.microsoft.com/azure/aks/)
* [Azure Container Instances](https://docs.microsoft.com/azure/container-instances/index)
* [Kubernetes](https://kubernetes.io/) wdrażany klaster do [usługi Azure Stack](https://docs.microsoft.com/azure-stack/operator/). Aby uzyskać więcej informacji, zobacz [wdrażanie platformy Kubernetes w usłudze Azure Stack](https://docs.microsoft.com/azure-stack/user/azure-stack-solution-template-kubernetes-deploy).

### <a name="container-requirements-and-recommendations"></a>Kontener wymagania i zalecenia

W poniższej tabeli opisano minimalne i zalecane rdzeni procesora CPU i pamięci do przydzielenia dla każdego kontenera usługi Personalizer.

| Kontener | Minimalne | Zalecane |
|-----------|---------|-------------|
|personalizer | 1 rdzeń, 4 GB pamięci RAM | 2 rdzeni, 8 GB pamięci RAM |

Każdego rdzenia musi mieć co najmniej 2,6 gigaherc (GHz) lub szybszy.

Rdzeni i pamięci odpowiadają `--cpus` i `--memory` ustawienia, które są używane jako część `docker run` polecenia.

Kontener musi być w stanie połączyć się z Centrum zdarzeń platformy Azure w celu przekazywania informacji w wywołaniach rangi i nagrody serwerowi Personalizer na platformie Azure, a musi być w stanie połączyć się z interfejsu API Personalizer aby można było załadować wymaganej konfiguracji i najnowszych maszyny modele uczenia.

## <a name="get-the-container-image-with-docker-pull"></a>Pobierz obraz kontenera przy użyciu `docker pull`

Dostępne są obrazy kontenerów dla usługi Personalizer. 

| Kontener | Repozytorium |
|-----------|------------|
| personalizer | `mcr.microsoft.com/azure-cognitive-services/personalizer:latest` |

> [!TIP]
> Możesz użyć [obrazów platformy docker](https://docs.docker.com/engine/reference/commandline/images/) polecenie, aby wyświetlić listę obrazów kontenerów pobrany. Na przykład następujące polecenie wyświetla identyfikator, repozytorium i tag każdy obraz kontenera pobrany, sformatowane jako tabela:
>
>  ```Docker
>  docker images --format "table {{.ID}}\t{{.Repository}}\t{{.Tag}}"
>
>  IMAGE ID            REPOSITORY                                                                TAG
>  ebbee78a6baa        mcr.microsoft.com/azure-cognitive-services/personalizer                 latest
>  ``` 

### <a name="docker-pull-for-the-personalizer-service-container"></a>Polecenie docker pull dla kontenera usługi Personalizer

```Docker
docker pull mcr.microsoft.com/azure-cognitive-services/personalizer:latest
```

## <a name="how-the-container-works"></a>Sposób działania kontenera

Kontener Personalizer reprezentuje część kliencką usługi Personalizer. Po uruchomieniu kontenera otrzymuje modeli i ustawień konfiguracji z Personalizer usługi w chmurze platformy Azure. Usługi container service używa tych informacji, aby odpowiadały na żądania **ranga** i **nagradzania**. Kontener wysyła również te żądania do zasobu Personalizer w chmurze platformy Azure. Te informacje jest następnie używane do uczenia modelu Personalizer pętli, na podstawie bieżącego ustawienia częstotliwości aktualizacji modelu. Aktualizowanego modelu jest wysyłane z powrotem do kontenera. 

![W środowisku lokalnym, lokalnego architekturę kontener Personalizer klienta](./media/personalizer-container-howto/personalizer-container-architecture.png)

## <a name="how-to-use-the-container"></a>Jak używać kontenera

Gdy kontener będzie na [komputerze-hoście](#the-host-computer), użyj następującego procesu do pracy z kontenerem.

1. [Uruchom kontener](#run-the-container-with-docker-run), za pomocą wymaganych rozliczeń ustawienia. Więcej [przykłady](personalizer-container-configuration.md#example-docker-run-commands) z `docker run` polecenia są dostępne. 
1. [Zapytanie do endpoint prognoz kontenera](#query-the-containers-prediction-endpoint). 

## <a name="run-the-container-with-docker-run"></a>Uruchom kontener za pomocą `docker run`

Użyj [platformy docker, uruchom](https://docs.docker.com/engine/reference/commandline/run/) polecenie, aby uruchomić dowolne trzy kontenery. Polecenie używa następujących parametrów:

| Symbol zastępczy | Wartość |
|-------------|-------|
|{BILLING_KEY} | Ten klucz jest używany do uruchamiania kontenera i jest dostępny na stronie klucze usługi Personalizer witryny Azure portal.  |
|{BILLING_ENDPOINT_URI} | Punkt końcowy rozliczeń wartość identyfikatora URI jest dostępna na stronie przeglądu usługi Personalizer witryny Azure portal.|

Zastąp parametry te wartości w poniższym przykładzie `docker run` polecenia.

```bash
docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 \
mcr.microsoft.com/azure-cognitive-services/personalizer\
Eula=accept \
Billing={BILLING_ENDPOINT_URI} \
ApiKey={BILLING_KEY}
```

To polecenie:

* Uruchamia kontener usługi Personalizer z obrazu kontenera
* Przydziela jeden rdzeń procesora CPU i 4 gigabajty (GB) pamięci
* Uwidacznia TCP port 5000 i przydziela pseudo-TTY kontenera
* Automatycznie usuwa kontener po jej zakończenia. Obraz kontenera jest nadal dostępna na komputerze-hoście. 

> [!IMPORTANT]
> `Eula`, `Billing`, I `ApiKey` opcje muszą być określone w celu uruchomienia kontenera; w przeciwnym razie nie uruchamia się kontener.  Aby uzyskać więcej informacji, zobacz [rozliczeń](#billing).

### <a name="run-multiple-containers-on-the-same-host"></a>Uruchamianie wielu kontenerów na tym samym hoście

Jeśli zamierzasz uruchomić wiele kontenerów z uwidocznionymi portami, upewnij się, do uruchamiania każdego kontenera przy użyciu innego portu. Na przykład uruchomić pierwszego kontenera na porcie 5000 oraz drugi kontener na porcie 5001.

Zastąp `<container-registry>` i `<container-name>` wartościami kontenerów, możesz użyć. Te nie muszą być tego samego kontenera. Masz kontener Personalizer i kontenerów usługi LUIS jednocześnie uruchomionych na HOŚCIE lub może mieć wiele kontenerów Personalizer uruchomione. 

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

## <a name="stop-the-container"></a>Zastavit kontejner

Do zamykania w kontenerze, w środowisko wiersza polecenia, na którym działa kontener, naciśnij klawisz **klawisze Ctrl + C**.

## <a name="troubleshoot"></a>Rozwiązywanie problemów

Jeśli uruchamiasz kontener z danymi wyjściowymi [instalacji](personalizer-container-configuration.md#mount-settings) i Rejestrowanie włączone, kontener generuje pliki dziennika, które są przydatne do rozwiązywania problemów, które wystąpiło podczas uruchamianie kontenera. 

## <a name="container-api-documentation"></a>Dokumentacja usługi container interfejsu API

Kontener zawiera pełny zestaw dokumentacji dla punktów końcowych, a także `Try it now` funkcji. Ta funkcja umożliwia wprowadzanie ustawień do formularza HTML, oparte na sieci web i utworzyć zapytanie bez konieczności pisania kodu. Gdy zapytanie zwraca przykład polecenia CURL jest dostarczany w celu pokazują nagłówków HTTP i treści w formacie wymaganym. 

> [!TIP]
> Odczyt [specyfikacji interfejsu OpenAPI](https://swagger.io/docs/specification/about/), opisujący operacje interfejsu API, obsługiwane przez kontener z `/swagger` względnym identyfikatorem URI. Na przykład:
>
>  ```http
>  http://localhost:5000/swagger
>  ```

## <a name="billing"></a>Informacje billingowe

Wyślij kontenerów usługi Personalizer rozliczeń informacje na platformie Azure, przy użyciu _usługi Personalizer_ zasobów dla konta systemu Azure. 

Kontenery usługi cognitive Services nie są licencjonowane do uruchomienia bez połączenia z platformy Azure do zbierania danych. Klienci muszą włączyć kontener, aby komunikować informacje rozliczeniowe usłudze zliczania przez cały czas.  

`docker run` Polecenie używa następujących argumentów na potrzeby rozliczeń:

| Opcja | Opis |
|--------|-------------|
| `ApiKey` | Klucz interfejsu API _usługi Personalizer_ zasoby używane do śledzenia informacji dotyczących rozliczeń. |
| `Billing` | Punkt końcowy _usługi Personalizer_ zasoby używane do śledzenia informacji dotyczących rozliczeń.|
| `Eula` | Wskazuje, że zaakceptowano licencję dla kontenera.<br/>Wartość ta opcja musi być równa `accept`. |

> [!IMPORTANT]
> Prawidłowe wartości należy określić wszystkie trzy opcje, lub uruchomić kontenera.

Aby uzyskać więcej informacji o tych opcjach, zobacz [skonfigurować kontenery](personalizer-container-configuration.md).

## <a name="summary"></a>Podsumowanie

W tym artykule przedstawiono pojęcia i przepływ pracy na potrzeby pobierania, instalowania i uruchamiania usługi Personalizer kontenerów. Podsumowanie:

* Usługa personalizer zapewnia kontenerów systemu Linux platformy Docker, zawieranie personalizacji.
* Obrazy kontenerów są pobierane z rejestru kontenerów firmy Microsoft (MCR) na platformie Azure.
* Obrazy kontenera Uruchom na platformie Docker.
* Można użyć interfejsu API REST lub zestawu SDK do wywoływania operacji w kontenerach usługi Personalizer, określając host identyfikatora URI kontenera.
* Należy określić informacje rozliczeniowe, podczas tworzenia wystąpienia kontenera.

> [!IMPORTANT]
> Kontenery usługi cognitive Services nie są licencjonowane do uruchomienia bez połączenia z platformy Azure do zbierania danych. Klienci muszą włączyć kontener, aby komunikować informacje rozliczeniowe usłudze zliczania przez cały czas. Personalizer kontenerów wysyła również dane żądania do odpowiedniej usługi na platformie Azure na potrzeby szkoleń online i zostanie wyświetlony zaktualizowany modeli okresowo z platformy Azure.

## <a name="next-steps"></a>Kolejne kroki

[Jak skonfigurować kontener platformy Docker dla `Docker Run` polecenia](personalizer-container-configuration.md)