---
title: Obsługa kontenerów
titleSuffix: Azure Cognitive Services
description: Dowiedz się, jak kontenery platformy Docker można bliżej Cognitive Services do swoich danych.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.topic: article
ms.date: 11/11/2019
ms.author: dapine
ms.openlocfilehash: fc4b9469d8634cbb46eccb842c9795a155384eb7
ms.sourcegitcommit: 39da2d9675c3a2ac54ddc164da4568cf341ddecf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/12/2019
ms.locfileid: "73961917"
---
# <a name="container-support-in-azure-cognitive-services"></a>Obsługa kontenerów w usługach Azure Cognitive Services

Obsługa kontenerów w usługach Azure Cognitive Services umożliwia deweloperom korzystanie z tych samych zaawansowanych interfejsów API, które są dostępne na platformie Azure i zapewnia elastyczność podczas wdrażania i obsługi usług, które są dostarczane z [kontenerów platformy Docker](https://www.docker.com/what-container). Obsługa kontenerów jest obecnie dostępna dla podzestawu Cognitive Services platformy Azure, w tym części:

> [!div class="checklist"]
> * [Wykrywacz anomalii][ad-containers]
> * [Przetwarzanie obrazów][cv-containers]
> * [Twarzy][fa-containers]
> * [Aparat rozpoznawania formularzy][fr-containers]
> * [Language Understanding (LUIS)][lu-containers]
> * [Interfejs API usługi rozpoznawania mowy][sp-containers]
> * [Analiza tekstu][ta-containers]

> [!VIDEO https://www.youtube.com/embed/hdfbn4Q8jbo]

Konteneryzacji to podejście do dystrybucji oprogramowania, w której aplikacja lub usługa, łącznie z jego zależności & konfiguracji, ma postać razem obrazu kontenera. Za pomocą niewielkie modyfikacje można wdrożyć obraz kontenera na hoście kontenera. Kontenery są odizolowane od siebie nawzajem i zasadniczego systemu operacyjnego za pomocą mniejszy wyświetlacz niż maszyny wirtualnej. Kontenery mogą utworzonych na podstawie obrazów kontenera do krótkoterminowych zadań i usuwane, gdy nie będą już potrzebne.

Zasoby Cognitive Services są dostępne na [Microsoft Azure](https://azure.microsoft.com). Zaloguj się do [witryny Azure portal](https://portal.azure.com/) do tworzenia i zapoznaj się z zasobami platformy Azure w przypadku tych usług.

## <a name="features-and-benefits"></a>Funkcje i korzyści

- **Kontrola nad danymi**: pozwalając użytkownikom na wybór, gdzie tych usług Cognitive Services przetwarzania ich danych. Jest to istotne w przypadku klientów, którzy nie mogą wysyłać dane do chmury, ale muszą mieć dostęp do technologii usług Cognitive Services. Obsługa spójności w środowiskach hybrydowych — między danymi, zarządzaniem, tożsamością i zabezpieczeniami.
- **Kontrolę nad aktualizacjami modelu**: zapewnić klientom elastyczność przechowywanie wersji i aktualizowanie modeli wdrożone w swoich rozwiązaniach.
- **Architektura przenośne**: umożliwiają tworzenie przenośnych architektura, które można wdrożyć na platformie Azure, lokalnie i urządzenia brzegowe. Kontenery, które mogą być wdrażane bezpośrednio do [usługi Azure Kubernetes Service](../aks/index.yml), [usługi Azure Container Instances](../container-instances/index.yml), lub [Kubernetes](https://kubernetes.io/) wdrażany klaster do [platformy Azure Stos](/azure-stack/operator). Aby uzyskać więcej informacji, zobacz [wdrażanie platformy Kubernetes w usłudze Azure Stack](/azure-stack/user/azure-stack-solution-template-kubernetes-deploy).
- **Wysoka przepływność / małych opóźnieniach**: zapewnia klientom możliwość skalowania dotyczące wysokiej przepływności i małego opóźnienia, włączając usług Cognitive Services uruchomić fizycznie blisko ich logiki aplikacji i danych. Kontenery nie limit transakcji na sekundę (TPS) i zyski, aby możliwe było skalowanie zarówno a obsłużyć żądanie, jeśli podasz zasoby sprzętowe niezbędne. 

## <a name="containers-in-azure-cognitive-services"></a>Kontenery w usługach Azure Cognitive Services

Kontenery usługi Azure Cognitive Services zapewniają następujący zestaw kontenerów platformy Docker, z których każdy zawiera podzbiór funkcji z usług w usługach Azure Cognitive Services:

| Usługa | Obsługiwana warstwa cenowa | Kontener | Opis |
|---------|----------|----------|-------------|
|[Narzędzie do wykrywania anomalii][ad-containers] |F0, S0|**Anomaly-Detector** |Interfejs API wykrywania anomalii umożliwia monitorowanie i wykrywanie anomalii w danych szeregów czasowych przy użyciu uczenia maszynowego.<br>[Poproś o dostęp](https://aka.ms/adcontainer)|
|[Przetwarzanie obrazów][cv-containers] |F0, S1|**Odczyt** |Wyodrębnia tekst drukowanego z obrazów różne obiekty na różnych powierzchniach i tłach, takich jak przychody oraz plakaty i wizytówki. Kontener odczytu wykrywa także *Tekst odręczny* w obrazach i zapewnia obsługę formatu PDF/TIFF/wielostronicowej.<br/><br/>**Ważne:** Kontener odczytu jest obecnie stosowany tylko w języku angielskim.|
|[Twarzy][fa-containers] |F0, S0|**Twarzy** |Wykrywa twarze osób na obrazach i identyfikuje atrybutów, w tym to punktów charakterystycznych (takie jak elementy ruchome i oczy), płeć, wiek i inne funkcje twarzy przewidzieć maszyny. Oprócz wykrywania twarzy można sprawdzić, czy dwie twarze w ten sam obraz lub różne obrazy są takie same, za pomocą współczynnik ufności lub porównywania twarzy względem bazy danych, aby sprawdzić, czy podobnie wyglądających twarzy identyczne już istnieje. Można również zorganizować podobnych twarzy w grupach, przy użyciu udostępnionego visual traits.<br>[Poproś o dostęp](Face/face-how-to-install-containers.md#request-access-to-the-private-container-registry) |
|[Aparat rozpoznawania formularzy][fr-containers] |F0, S0|**Aparat rozpoznawania formularzy** |Zrozumienie formularza stosuje technologię uczenia maszynowego do identyfikowania i wyodrębniania par klucz-wartość oraz tabel z formularzy.<br>[Poproś o dostęp](https://aka.ms/FormRecognizerContainerRequestAccess)|
|[LUIS][lu-containers] |F0, S0|**Usługa LUIS** ([obraz](https://go.microsoft.com/fwlink/?linkid=2043204&clcid=0x409))|Ładuje uczonego lub opublikowane Language Understanding modelu, nazywane również aplikacją usługi LUIS, do kontenera platformy docker i zapewnia dostęp do przewidywania zapytania z punktów końcowych interfejsu API kontenera. Możesz zbierać dzienniki zapytań z kontenera i przekazać te Wstecz, aby [portal usługi LUIS](https://www.luis.ai) w celu zwiększenia dokładności prognozy aplikacji.|
|[Interfejs API usługi rozpoznawania mowy][sp-containers-stt] |F0, S0|**Zamiana mowy na tekst** |Przekształca w czasie rzeczywistym ciągłą mowę na tekst.|
|[Interfejs API usługi rozpoznawania mowy][sp-containers-cstt] |F0, S0|**Custom Speech do tekstu** |Przekształca ciągłe używanie mowy w czasie rzeczywistym do tekstu przy użyciu modelu niestandardowego.|
|[Interfejs API usługi rozpoznawania mowy][sp-containers-tts] |F0, S0|**Zamiana tekstu na mowę** |Konwertuje tekst na naturalnie brzmiącą mowę.|
|[Interfejs API usługi rozpoznawania mowy][sp-containers-ctts] |F0, S0|**Niestandardowa Zamiana tekstu na mowę** |Konwertuje tekst na mowę dźwięku naturalnego przy użyciu modelu niestandardowego.|
|[Analiza tekstu][ta-containers] |F0, S|**Klucz frazy** ([obraz](https://go.microsoft.com/fwlink/?linkid=2018757&clcid=0x409)) |Wyodrębnianie kluczowych fraz można identyfikować jego główne punkty. Na przykład dla tekstu wejściowego „Jedzenie było pyszne, a serwowała je doskonała obsługa” interfejs API zwraca główne tematy wypowiedzi: „jedzenie” i „doskonała obsługa”. |
|[Analiza tekstu][ta-containers]|F0, S|**Wykrywanie języka** ([obraz](https://go.microsoft.com/fwlink/?linkid=2018759&clcid=0x409)) |Maksymalnie 120 języków wykrywa, które tekst wejściowy został napisany w języku i kod jeden język, dla każdego dokumentu Przesłano żądanie raportu. Kod języka jest powiązany z oceną, co wskazuje siłę oceny. |
|[Analiza tekstu][ta-containers]|F0, S|**Analiza tonacji** ([obraz](https://go.microsoft.com/fwlink/?linkid=2018654&clcid=0x409)) |Analizuje nieprzetworzony tekst dla wskazówek dotyczących opinii dodatnia lub ujemna. Ten interfejs API zwraca ocenę tonacji od 0 do 1 dla każdego dokumentu, przy czym 1 oznacza najbardziej pozytywną tonację. Modele analizy są wstępnie przeszkolonych, za pomocą rozbudowane treści, tekstu i języka naturalnego technologii firmy Microsoft. W przypadku [wybranych języków](./text-analytics/language-support.md) interfejs API może przeanalizować i ocenić dowolny podany nieprzetworzony tekst, zwracając wyniki bezpośrednio do aplikacji wywołującej. |

<!--
|[Personalizer](https://go.microsoft.com/fwlink/?linkid=2083923&clcid=0x409) |F0, S0|**Personalizer** ([image](https://go.microsoft.com/fwlink/?linkid=2083928&clcid=0x409))|Azure Personalizer is a cloud-based API service that allows you to choose the best experience to show to your users, learning from their real-time behavior.|
-->

Ponadto niektóre kontenery są obsługiwane w Cognitive Services wszystkich kluczy zasobów dostępnych [**w ramach jednej oferty**](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAllInOne) . Można utworzyć jeden Cognitive Services pojedynczym zasobem i korzystać z tego samego klucza rozliczania dla następujących usług:

* Przetwarzanie obrazów
* Rozpoznawanie twarzy
* LUIS
* Analiza tekstu

## <a name="container-availability-in-azure-cognitive-services"></a>Dostępność kontenera w usługach Azure Cognitive Services

Kontenery usługi Azure Cognitive Services są publicznie dostępne w ramach subskrypcji platformy Azure i mogą być ściągane obrazów kontenerów platformy Docker z rejestru kontenerów firmy Microsoft lub z usługi Docker Hub. Możesz użyć [polecenie docker pull](https://docs.docker.com/engine/reference/commandline/pull/) polecenie, aby pobrać obraz kontenera z rejestru odpowiednie.

> [!IMPORTANT]
> Obecnie należy zakończyć proces tworzenia konta, aby uzyskać dostęp do następujących kontenerów, w których wypełniasz i przesłać kwestionariusz z pytaniami dotyczącymi użytkownika, firmy i przypadku użycia, dla którego chcesz zaimplementować kontenery. Po uzyskaniu dostępu i uzyskaniu poświadczeń można ściągnąć obrazy kontenerów z prywatnego rejestru kontenerów hostowanych przez Azure Container Registry.
> * [Narzędzie do wykrywania anomalii](Anomaly-Detector/anomaly-detector-container-howto.md#request-access-to-the-container-registry)
> * [Twarzy](Face/face-how-to-install-containers.md)
> * [Aparat rozpoznawania formularzy](form-recognizer/form-recognizer-container-howto.md#request-access-to-the-container-registry)
> * [Odczyt](computer-vision/computer-vision-how-to-install-containers.md)
> * [Zamiana mowy na tekst i zamiany tekstu na mowę](Speech-Service/speech-container-howto.md#request-access-to-the-container-registry)

[!INCLUDE [Container repositories and images](containers/includes/cognitive-services-container-images.md)]

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem korzystania z kontenerów usług Azure Cognitive Services, muszą spełniać następujące wymagania wstępne:

**Aparat platformy docker**: konieczne jest posiadanie aparat platformy Docker zainstalowany lokalnie. Środowisko docker zawiera pakiety, które konfigurują środowisko platformy Docker na [macOS](https://docs.docker.com/docker-for-mac/), [Linux](https://docs.docker.com/engine/installation/#supported-platforms), i [Windows](https://docs.docker.com/docker-for-windows/). W Windows platformy Docker musi być skonfigurowany do obsługi kontenerów systemu Linux. Kontenery platformy docker można także bezpośrednio do wdrożyć [usługi Azure Kubernetes Service](../aks/index.yml) lub [usługi Azure Container Instances](../container-instances/index.yml).

Docker należy skonfigurować w taki sposób, aby umożliwić kontenerów, aby nawiązać połączenie z, a następnie wysyłać danych dotyczących rozliczeń do platformy Azure.

**Znajomość Microsoft Container Registry i Docker**: powinien mieć podstawową wiedzę na temat koncepcji Microsoft Container Registry i Docker, takich jak rejestrów, repozytoriów, kontenerów, a obrazy kontenerów, a także wiedzę na temat podstawowe `docker` poleceń.

Aby uzyskać podstawowe informacje na temat platformy Docker i kontenerów, zobacz [Docker — omówienie](https://docs.docker.com/engine/docker-overview/).

Poszczególnych kontenerów może mieć własne wymagania, jak również w tym serwera i wymagania dotyczące alokacji pamięci.

[!INCLUDE [Cognitive Services container security](containers/includes/cognitive-services-container-security.md)]

[!INCLUDE [Discoverability of more container information](../../includes/cognitive-services-containers-discoverability.md)]

## <a name="next-steps"></a>Następne kroki

Zapoznaj się z [przepisami kontenera](containers/container-reuse-recipe.md) , których można używać z Cognitive Services.

Zainstaluj i Poznaj funkcje udostępniane przez kontenery w usługach Azure Cognitive Services:

* [Kontenery detektorów anomalii][ad-containers]
* [Kontenery przetwarzanie obrazów][cv-containers]
* [Kontenery do rozpoznawania][fa-containers]
* [Kontenery aparatu rozpoznawania formularzy][fr-containers]
* [Kontenery Language Understanding (LUIS)][lu-containers]
* [Kontenery interfejsu API usługi Speech Service][sp-containers]
* [Kontenery analiza tekstu][ta-containers]

<!--* [Personalizer containers](https://go.microsoft.com/fwlink/?linkid=2083928&clcid=0x409)
-->

[ad-containers]: anomaly-Detector/anomaly-detector-container-howto.md
[cv-containers]: computer-vision/computer-vision-how-to-install-containers.md
[fa-containers]: face/face-how-to-install-containers.md
[fr-containers]: form-recognizer/form-recognizer-container-howto.md
[lu-containers]: luis/luis-container-howto.md
[sp-containers]: speech-service/speech-container-howto.md
[sp-containers-stt]: speech-service/speech-container-howto.md?tabs=stt
[sp-containers-cstt]: speech-service/speech-container-howto.md?tabs=cstt
[sp-containers-tts]: speech-service/speech-container-howto.md?tabs=tts
[sp-containers-ctts]: speech-service/speech-container-howto.md?tabs=ctts
[ta-containers]: text-analytics/how-tos/text-analytics-how-to-install-containers.md