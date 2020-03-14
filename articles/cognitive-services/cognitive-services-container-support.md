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
ms.date: 01/10/2020
ms.author: dapine
ms.openlocfilehash: d75962b98543991a065f6b165279215614175925
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/13/2020
ms.locfileid: "79219413"
---
# <a name="container-support-in-azure-cognitive-services"></a>Obsługa kontenerów w usługach Azure Cognitive Services

Obsługa kontenerów w usłudze Azure Cognitive Services pozwala deweloperom korzystać z tych samych bogatych interfejsów API, które są dostępne na platformie Azure, i umożliwia elastyczność w przypadku wdrażania i hostowania usług, które są dostarczane z [kontenerami platformy Docker](https://www.docker.com/what-container). Obsługa kontenerów jest obecnie dostępna dla podzestawu Cognitive Services platformy Azure, w tym części:

> [!div class="checklist"]
> * [Wykrywacz anomalii][ad-containers]
> * [Przetwarzanie obrazów][cv-containers]
> * [Stoi][fa-containers]
> * [Aparat rozpoznawania formularzy][fr-containers]
> * [Language Understanding (LUIS)][lu-containers]
> * [Interfejs API usługi rozpoznawania mowy][sp-containers]
> * [Analiza tekstu][ta-containers]

> [!VIDEO https://www.youtube.com/embed/hdfbn4Q8jbo]

Konteneryzacji to podejście do dystrybucji oprogramowania, w której aplikacja lub usługa, łącznie z jego zależności & konfiguracji, ma postać razem obrazu kontenera. Za pomocą niewielkie modyfikacje można wdrożyć obraz kontenera na hoście kontenera. Kontenery są odizolowane od siebie nawzajem i zasadniczego systemu operacyjnego za pomocą mniejszy wyświetlacz niż maszyny wirtualnej. Kontenery mogą utworzonych na podstawie obrazów kontenera do krótkoterminowych zadań i usuwane, gdy nie będą już potrzebne.

Zasoby Cognitive Services są dostępne na [Microsoft Azure](https://azure.microsoft.com). Zaloguj się do [Azure Portal](https://portal.azure.com/) , aby utworzyć i poznać zasoby platformy Azure dla tych usług.

## <a name="features-and-benefits"></a>Funkcje i korzyści

- **Niezmienna infrastruktura**: Włącz zespoły DevOps ", aby korzystały z spójnego i niezawodnego zestawu znanych parametrów systemowych, jednocześnie umożliwiając dostosowanie się do zmian. Kontenery zapewniają elastyczność przestawiania w przewidywalnym ekosystemie i unikają dryfowania konfiguracji.
- **Kontrola nad danymi**: Zezwól klientom na wybór, gdzie te Cognitive Services przetwarzać dane. Jest to istotne w przypadku klientów, którzy nie mogą wysyłać dane do chmury, ale muszą mieć dostęp do technologii usług Cognitive Services. Obsługa spójności w środowiskach hybrydowych — między danymi, zarządzaniem, tożsamością i zabezpieczeniami.
- **Kontrolę nad aktualizacjami modelu**: zapewniają klientom elastyczność w zakresie przechowywania wersji i aktualizowania modeli wdrożonych w ich rozwiązaniach.
- **Architektura przenośna**: umożliwia tworzenie architektury aplikacji przenośnej, którą można wdrożyć na platformie Azure, lokalnie i na krawędzi. Kontenery można wdrażać bezpośrednio w [usłudze Azure Kubernetes Service](../aks/index.yml), [Azure Container Instances](../container-instances/index.yml)lub do klastra [Kubernetes](https://kubernetes.io/) wdrożonego w [Azure Stack](/azure-stack/operator). Aby uzyskać więcej informacji, zobacz [wdrażanie Kubernetes do Azure Stack](/azure-stack/user/azure-stack-solution-template-kubernetes-deploy).
- **Wysoka przepływność/małe opóźnienia**: umożliwiają klientom skalowanie w celu uzyskania wysokiej przepływności i małych opóźnień przez umożliwienie Cognitive Services uruchamiania w pobliżu ich logiki i danych aplikacji. Kontenery nie limit transakcji na sekundę (TPS) i zyski, aby możliwe było skalowanie zarówno a obsłużyć żądanie, jeśli podasz zasoby sprzętowe niezbędne.
- **Skalowalność**: dzięki coraz większej popularności oprogramowania kontenerach i aranżacji kontenerów, takich jak Kubernetes; skalowalność jest w programie Forefront z góry postępów technologicznych. Tworzenie aplikacji na skalowalnym serwerze klastra zapewnia wysoką dostępność.

## <a name="containers-in-azure-cognitive-services"></a>Kontenery w usługach Azure Cognitive Services

Kontenery usługi Azure Cognitive Services zapewniają następujący zestaw kontenerów platformy Docker, z których każdy zawiera podzbiór funkcji z usług w usługach Azure Cognitive Services:

| Usługa | Obsługiwana warstwa cenowa | Kontener | Opis |
|---------|----------|----------|-------------|
|[Wykrywacz anomalii][ad-containers] |F0, S0|**Anomalia — detektor** |Interfejs API wykrywania anomalii umożliwia monitorowanie i wykrywanie anomalii w danych szeregów czasowych przy użyciu uczenia maszynowego.<br>[Żądaj dostępu](https://aka.ms/adcontainer)|
|[Przetwarzanie obrazów][cv-containers] |F0, S1|**Odczyt** |Wyodrębnia tekst drukowanego z obrazów różne obiekty na różnych powierzchniach i tłach, takich jak przychody oraz plakaty i wizytówki. Kontener odczytu wykrywa także *Tekst odręczny* w obrazach i zapewnia obsługę formatu PDF/TIFF/wielostronicowej.<br/><br/>**Ważne:** Kontener odczytu jest obecnie stosowany tylko w języku angielskim.|
|[Stoi][fa-containers] |F0, S0|**Stoi** |Wykrywa twarze osób na obrazach i identyfikuje atrybutów, w tym to punktów charakterystycznych (takie jak elementy ruchome i oczy), płeć, wiek i inne funkcje twarzy przewidzieć maszyny. Oprócz wykrywania twarzy można sprawdzić, czy dwie twarze w ten sam obraz lub różne obrazy są takie same, za pomocą współczynnik ufności lub porównywania twarzy względem bazy danych, aby sprawdzić, czy podobnie wyglądających twarzy identyczne już istnieje. Można również zorganizować podobnych twarzy w grupach, przy użyciu udostępnionego visual traits.<br>[Żądaj dostępu](Face/face-how-to-install-containers.md#request-access-to-the-private-container-registry) |
|[Aparat rozpoznawania formularzy][fr-containers] |F0, S0|**Aparat rozpoznawania formularzy** |Zrozumienie formularza stosuje technologię uczenia maszynowego do identyfikowania i wyodrębniania par klucz-wartość oraz tabel z formularzy.<br>[Żądaj dostępu](https://aka.ms/FormRecognizerContainerRequestAccess)|
|[LUIS][lu-containers] |F0, S0|**Luis** ([obraz](https://go.microsoft.com/fwlink/?linkid=2043204&clcid=0x409))|Ładuje uczonego lub opublikowane Language Understanding modelu, nazywane również aplikacją usługi LUIS, do kontenera platformy docker i zapewnia dostęp do przewidywania zapytania z punktów końcowych interfejsu API kontenera. Można zebrać dzienniki zapytań z kontenera i przekazać je z powrotem do [portalu Luis](https://www.luis.ai) , aby zwiększyć dokładność przewidywania aplikacji.|
|[Interfejs API usługi rozpoznawania mowy][sp-containers-stt] |F0, S0|**Zamiana mowy na tekst** |Przekształca w czasie rzeczywistym ciągłą mowę na tekst.|
|[Interfejs API usługi rozpoznawania mowy][sp-containers-cstt] |F0, S0|**Custom Speech do tekstu** |Przekształca ciągłe używanie mowy w czasie rzeczywistym do tekstu przy użyciu modelu niestandardowego.|
|[Interfejs API usługi rozpoznawania mowy][sp-containers-tts] |F0, S0|**Zamiana tekstu na mowę** |Konwertuje tekst na naturalnie brzmiącą mowę.|
|[Interfejs API usługi rozpoznawania mowy][sp-containers-ctts] |F0, S0|**Niestandardowa Zamiana tekstu na mowę** |Konwertuje tekst na mowę dźwięku naturalnego przy użyciu modelu niestandardowego.|
|[Analiza tekstu][ta-containers-keyphrase] |F0, S|**Wyodrębnianie kluczowych fraz** ([obraz](https://go.microsoft.com/fwlink/?linkid=2018757&clcid=0x409)) |Wyodrębnianie kluczowych fraz można identyfikować jego główne punkty. Na przykład dla tekstu wejściowego „Jedzenie było pyszne, a serwowała je doskonała obsługa” interfejs API zwraca główne tematy wypowiedzi: „jedzenie” i „doskonała obsługa”. |
|[Analiza tekstu][ta-containers-language]|F0, S|**Wykrywanie języka** ([obraz](https://go.microsoft.com/fwlink/?linkid=2018759&clcid=0x409)) |Maksymalnie 120 języków wykrywa, które tekst wejściowy został napisany w języku i kod jeden język, dla każdego dokumentu Przesłano żądanie raportu. Kod języka jest powiązany z oceną, co wskazuje siłę oceny. |
|[Analiza tekstu][ta-containers-sentiment]|F0, S|**Analiza tonacji** ([obraz](https://go.microsoft.com/fwlink/?linkid=2018654&clcid=0x409)) |Analizuje nieprzetworzony tekst dla wskazówek dotyczących opinii dodatnia lub ujemna. Ten interfejs API zwraca ocenę tonacji od 0 do 1 dla każdego dokumentu, przy czym 1 oznacza najbardziej pozytywną tonację. Modele analizy są wstępnie przeszkolonych, za pomocą rozbudowane treści, tekstu i języka naturalnego technologii firmy Microsoft. W przypadku [wybranych języków](./text-analytics/language-support.md) interfejs API może przeanalizować i ocenić dowolny podany nieprzetworzony tekst, zwracając wyniki bezpośrednio do aplikacji wywołującej. |

<!--
|[Personalizer](https://go.microsoft.com/fwlink/?linkid=2083923&clcid=0x409) |F0, S0|**Personalizer** ([image](https://go.microsoft.com/fwlink/?linkid=2083928&clcid=0x409))|Azure Personalizer is a cloud-based API service that allows you to choose the best experience to show to your users, learning from their real-time behavior.|
-->

Ponadto niektóre kontenery są obsługiwane w Cognitive Services wszystkich kluczy zasobów dostępnych [**w ramach jednej oferty**](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAllInOne) . Można utworzyć jeden Cognitive Services pojedynczym zasobem i korzystać z tego samego klucza rozliczania dla następujących usług:

* Przetwarzanie obrazów
* Rozpoznawanie twarzy
* LUIS
* Analiza tekstu

## <a name="container-availability-in-azure-cognitive-services"></a>Dostępność kontenera w usługach Azure Cognitive Services

Kontenery usługi Azure Cognitive Services są publicznie dostępne w ramach subskrypcji platformy Azure i mogą być ściągane obrazów kontenerów platformy Docker z rejestru kontenerów firmy Microsoft lub z usługi Docker Hub. Za pomocą polecenia [Docker pull](https://docs.docker.com/engine/reference/commandline/pull/) można pobrać obraz kontenera z odpowiedniego rejestru.

> [!IMPORTANT]
> Obecnie należy zakończyć proces tworzenia konta, aby uzyskać dostęp do następujących kontenerów, w których wypełniasz i przesłać kwestionariusz z pytaniami dotyczącymi użytkownika, firmy i przypadku użycia, dla którego chcesz zaimplementować kontenery. Po uzyskaniu dostępu i uzyskaniu poświadczeń można ściągnąć obrazy kontenerów z prywatnego rejestru kontenerów hostowanych przez Azure Container Registry.
> * [Wykrywacz anomalii](Anomaly-Detector/anomaly-detector-container-howto.md#request-access-to-the-container-registry)
> * [Stoi](Face/face-how-to-install-containers.md)
> * [Aparat rozpoznawania formularzy](form-recognizer/form-recognizer-container-howto.md#request-access-to-the-container-registry)
> * [Odczyt](computer-vision/computer-vision-how-to-install-containers.md)
> * [Zamiana mowy na tekst i zamiany tekstu na mowę](Speech-Service/speech-container-howto.md#request-access-to-the-container-registry)

[!INCLUDE [Container repositories and images](containers/includes/cognitive-services-container-images.md)]

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem korzystania z kontenerów usług Azure Cognitive Services, muszą spełniać następujące wymagania wstępne:

**Aparat platformy Docker**: aparat platformy Docker musi być zainstalowany lokalnie. Platforma Docker zawiera pakiety, które konfigurują środowisko platformy Docker w systemach [macOS](https://docs.docker.com/docker-for-mac/), [Linux](https://docs.docker.com/engine/installation/#supported-platforms)i [Windows](https://docs.docker.com/docker-for-windows/). W Windows platformy Docker musi być skonfigurowany do obsługi kontenerów systemu Linux. Kontenery platformy Docker można także wdrażać bezpośrednio w [usłudze Azure Kubernetes Service](../aks/index.yml) lub [Azure Container Instances](../container-instances/index.yml).

Docker należy skonfigurować w taki sposób, aby umożliwić kontenerów, aby nawiązać połączenie z, a następnie wysyłać danych dotyczących rozliczeń do platformy Azure.

**Znajomość programu microsoft Container Registry i platformy Docker**: należy mieć podstawową wiedzę na temat koncepcji firmy Microsoft Container Registry i platformy Docker, takich jak rejestry, repozytoria, kontenery i obrazy kontenerów, a także znajomość podstawowych poleceń `docker`.

Podstawowe informacje dotyczące platformy Docker i kontenera można znaleźć w temacie [Omówienie platformy Docker](https://docs.docker.com/engine/docker-overview/).

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
[ta-containers-keyphrase]: text-analytics/how-tos/text-analytics-how-to-install-containers.md?tabs=keyphrase
[ta-containers-language]: text-analytics/how-tos/text-analytics-how-to-install-containers.md?tabs=language
[ta-containers-sentiment]: text-analytics/how-tos/text-analytics-how-to-install-containers.md?tabs=sentiment