---
title: Obsługa kontenerów
titleSuffix: Azure Cognitive Services
description: Dowiedz się, jak kontenery platformy Docker można bliżej Cognitive Services do swoich danych.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.topic: article
ms.date: 04/16/2019
ms.author: diberry
ms.openlocfilehash: 172774c90633c96c3a8e2c128df050fedeb8b52b
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "62109838"
---
# <a name="container-support-in-azure-cognitive-services"></a>Obsługa kontenerów w usługach Azure Cognitive Services

Obsługa kontenerów w usługach Azure Cognitive Services umożliwia deweloperom korzystanie z tych samych zaawansowanych interfejsów API, które są dostępne na platformie Azure i zapewnia elastyczność podczas wdrażania i obsługi usług, które są dostarczane z [kontenerów platformy Docker](https://www.docker.com/what-container). Obsługa kontenerów jest obecnie dostępna w wersji zapoznawczej dla podzbioru usług Azure Cognitive Services, łącznie z części [komputerowej](Computer-vision/Home.md), [twarzy](Face/Overview.md), [analizy tekstu](text-analytics/overview.md)i [ Language Understanding](LUIS/luis-container-howto.md) (LUIS).

Konteneryzacji to podejście do dystrybucji oprogramowania, w której aplikacja lub usługa, łącznie z jego zależności & konfiguracji, ma postać razem obrazu kontenera. Za pomocą niewielkie modyfikacje można wdrożyć obraz kontenera na hoście kontenera. Kontenery są odizolowane od siebie nawzajem i zasadniczego systemu operacyjnego za pomocą mniejszy wyświetlacz niż maszyny wirtualnej. Kontenery mogą utworzonych na podstawie obrazów kontenera do krótkoterminowych zadań i usuwane, gdy nie będą już potrzebne.

Poniższy klip wideo pokazuje, za pomocą kontenera usług Cognitive Services.

[![Pokaz kontener dla usług Cognitive Services](./media/index/containers-video-image.png)](https://azure.microsoft.com/resources/videos/containers-support-of-cognitive-services)

[Komputerowej](Computer-vision/Home.md), [twarzy](Face/Overview.md), [analizy tekstu](text-analytics/overview.md), i [Language Understanding (LUIS)](LUIS/what-is-luis.md) usługi są dostępne w [Platformy Microsoft Azure](https://azure.microsoft.com). Zaloguj się do [witryny Azure portal](https://portal.azure.com/) do tworzenia i zapoznaj się z zasobami platformy Azure w przypadku tych usług.

## <a name="features-and-benefits"></a>Funkcje i korzyści

- **Kontrola nad danymi**: Pozwalając użytkownikom na wybór gdzie tych usług Cognitive Services przetwarzania ich danych. Jest to istotne w przypadku klientów, którzy nie mogą wysyłać dane do chmury, ale muszą mieć dostęp do technologii usług Cognitive Services. Obsługa spójności w środowiskach hybrydowych — wielu danych, zarządzania, tożsamości i zabezpieczeń.
- **Kontrolę nad aktualizacjami modelu**: Umożliwiają klientom elastyczność przechowywanie wersji i aktualizowanie modeli wdrożone w swoich rozwiązaniach.
- **Architektura przenośne**: Umożliwiają tworzenie przenośnych architektura, które można wdrożyć na platformie Azure, lokalnie i urządzenia brzegowe. Kontenery, które mogą być wdrażane bezpośrednio do [usługi Azure Kubernetes Service](../aks/index.yml), [usługi Azure Container Instances](../container-instances/index.yml), lub [Kubernetes](https://kubernetes.io/) wdrażany klaster do [platformy Azure Stos](/azure-stack/operator). Aby uzyskać więcej informacji, zobacz [wdrażanie platformy Kubernetes w usłudze Azure Stack](/azure-stack/user/azure-stack-solution-template-kubernetes-deploy).
- **Wysoka przepływność / małych opóźnieniach**: Zapewnia klientom możliwość skalowania dotyczące wysokiej przepływności i małego opóźnienia, włączając usług Cognitive Services uruchomić fizycznie blisko ich logiki aplikacji i danych. Kontenery nie limit transakcji na sekundę (TPS) i zyski, aby możliwe było skalowanie zarówno a obsłużyć żądanie, jeśli podasz zasoby sprzętowe niezbędne. 


## <a name="containers-in-azure-cognitive-services"></a>Kontenery w usługach Azure Cognitive Services

Kontenery usługi Azure Cognitive Services zapewniają następujący zestaw kontenerów platformy Docker, z których każdy zawiera podzbiór funkcji z usług w usługach Azure Cognitive Services:

| Usługa | Obsługiwane warstwy cenowej | Kontener | Opis |
|---------|----------|----------|-------------|
|[Przetwarzanie obrazów](Computer-vision/computer-vision-how-to-install-containers.md) |F0, S1|**Rozpoznawanie tekstu** |Wyodrębnia tekst drukowanego z obrazów różne obiekty na różnych powierzchniach i tłach, takich jak przychody oraz plakaty i wizytówki.<br/><br/>**Ważne:** Kontener Rozpoznaj tekst obecnie działa tylko w języku angielskim.<br>[Poproś o dostęp](Computer-vision/computer-vision-how-to-install-containers.md#request-access-to-the-private-container-registry)|
|[Twarzy](Face/face-how-to-install-containers.md) |F0, S0|**Twarzy** |Wykrywa twarze osób na obrazach i identyfikuje atrybutów, w tym to punktów charakterystycznych (takie jak elementy ruchome i oczy), płeć, wiek i inne funkcje twarzy przewidzieć maszyny. Oprócz wykrywania twarzy można sprawdzić, czy dwie twarze w ten sam obraz lub różne obrazy są takie same, za pomocą współczynnik ufności lub porównywania twarzy względem bazy danych, aby sprawdzić, czy podobnie wyglądających twarzy identyczne już istnieje. Można również zorganizować podobnych twarzy w grupach, przy użyciu udostępnionego visual traits.<br>[Poproś o dostęp](Face/face-how-to-install-containers.md#request-access-to-the-private-container-registry) |
|[LUIS](LUIS/luis-container-howto.md) |F0, S0|**Usługa LUIS** ([obraz](https://go.microsoft.com/fwlink/?linkid=2043204))|Ładuje uczonego lub opublikowane Language Understanding modelu, nazywane również aplikacją usługi LUIS, do kontenera platformy docker i zapewnia dostęp do przewidywania zapytania z punktów końcowych interfejsu API kontenera. Możesz zbierać dzienniki zapytań z kontenera i przekazać te Wstecz, aby [portal usługi LUIS](https://www.luis.ai) w celu zwiększenia dokładności prognozy aplikacji.|
|[Analiza tekstu](text-analytics/how-tos/text-analytics-how-to-install-containers.md) |F0, S|**Klucz frazy** ([obraz](https://go.microsoft.com/fwlink/?linkid=2018757)) |Wyodrębnianie kluczowych fraz można identyfikować jego główne punkty. Na przykład dla tekstu wejściowego „Jedzenie było pyszne, a serwowała je doskonała obsługa” interfejs API zwraca główne tematy wypowiedzi: „jedzenie” i „doskonała obsługa”. |
|[Analiza tekstu](text-analytics/how-tos/text-analytics-how-to-install-containers.md)|F0, S|**Wykrywanie języka** ([obraz](https://go.microsoft.com/fwlink/?linkid=2018759)) |Maksymalnie 120 języków wykrywa, które tekst wejściowy został napisany w języku i kod jeden język, dla każdego dokumentu Przesłano żądanie raportu. Kod języka jest powiązany z oceną, co wskazuje siłę oceny. |
|[Analiza tekstu](text-analytics/how-tos/text-analytics-how-to-install-containers.md)|F0, S|**Analiza tonacji** ([obraz](https://go.microsoft.com/fwlink/?linkid=2018654)) |Analizuje nieprzetworzony tekst dla wskazówek dotyczących opinii dodatnia lub ujemna. Ten interfejs API zwraca ocenę tonacji od 0 do 1 dla każdego dokumentu, przy czym 1 oznacza najbardziej pozytywną tonację. Modele analizy są wstępnie przeszkolonych, za pomocą rozbudowane treści, tekstu i języka naturalnego technologii firmy Microsoft. W przypadku [wybranych języków](./text-analytics/language-support.md) interfejs API może przeanalizować i ocenić dowolny podany nieprzetworzony tekst, zwracając wyniki bezpośrednio do aplikacji wywołującej. |

Ponadto, kontenery są obsługiwane w usługach Cognitive Services [oferty w jednym](https://azure.microsoft.com/pricing/details/cognitive-services/). Można tworzyć jeden pojedynczy zasób w jednym usług Cognitive Services i używać tego samego klucza rozliczenia dla wszystkich typów kontenera wymienionych powyżej.

## <a name="container-availability-in-azure-cognitive-services"></a>Dostępność kontenera w usługach Azure Cognitive Services

Kontenery usługi Azure Cognitive Services są publicznie dostępne w ramach subskrypcji platformy Azure i mogą być ściągane obrazów kontenerów platformy Docker z rejestru kontenerów firmy Microsoft lub z usługi Docker Hub. Możesz użyć [polecenie docker pull](https://docs.docker.com/engine/reference/commandline/pull/) polecenie, aby pobrać obraz kontenera z rejestru odpowiednie.

> [!IMPORTANT]
> Obecnie musisz ukończyć procesu rejestracji w celu uzyskania dostępu do [twarzy](Face/face-how-to-install-containers.md) i [Rozpoznaj tekst](Computer-vision/computer-vision-how-to-install-containers.md) kontenery, w których Wypełnij i Prześlij kwestionariusza w razie pytań dotyczących, firmy i przypadek użycia dla którego chcesz zaimplementować kontenerów. Gdy jesteś uzyskuje dostęp i podano poświadczenia, wtedy mogła ściągać obrazy kontenerów dla kontenerów twarzy i rozpoznawanie tekstu z prywatnego rejestru kontenerów hostowanej przez usługę Azure Container Registry.

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem korzystania z kontenerów usług Azure Cognitive Services, muszą spełniać następujące wymagania wstępne:

**Aparat platformy docker**: Konieczne jest posiadanie aparat platformy Docker zainstalowany lokalnie. Środowisko docker zawiera pakiety, które konfigurują środowisko platformy Docker na [macOS](https://docs.docker.com/docker-for-mac/), [Linux](https://docs.docker.com/engine/installation/#supported-platforms), i [Windows](https://docs.docker.com/docker-for-windows/). W Windows platformy Docker musi być skonfigurowany do obsługi kontenerów systemu Linux. Kontenery platformy docker można także bezpośrednio do wdrożyć [usługi Azure Kubernetes Service](../aks/index.yml) lub [usługi Azure Container Instances](../container-instances/index.yml).

Docker należy skonfigurować w taki sposób, aby umożliwić kontenerów, aby nawiązać połączenie z, a następnie wysyłać danych dotyczących rozliczeń do platformy Azure.

**Znajomość Microsoft Container Registry i Docker**: Należy mieć podstawową wiedzę na temat koncepcji Microsoft Container Registry i Docker, takich jak rejestry, repozytoria, kontenery i obrazów kontenerów, a także wiedzę na temat basic `docker` poleceń.

Aby uzyskać podstawowe informacje na temat platformy Docker i kontenerów, zobacz [Docker — omówienie](https://docs.docker.com/engine/docker-overview/).

Poszczególnych kontenerów może mieć własne wymagania, jak również w tym serwera i wymagania dotyczące alokacji pamięci.

## <a name="developer-samples"></a>Przykłady dla deweloperów

Przykłady dla deweloperów są dostępne pod adresem naszych [repozytorium GitHub](https://github.com/Azure-Samples/cognitive-services-containers-samples).

## <a name="next-steps"></a>Kolejne kroki

Zainstaluj i Poznaj funkcje udostępniane przez kontenery w usługach Azure Cognitive Services:

* [Instalowanie i używanie przetwarzania obrazów kontenerów](Computer-vision/computer-vision-how-to-install-containers.md)
* [Instalowanie i używanie kontenerów twarzy](Face/face-how-to-install-containers.md)
* [Instalowanie i używanie kontenerów analizy tekstu](text-analytics/how-tos/text-analytics-how-to-install-containers.md)
* [Instalowanie i używanie kontenerów Language Understanding (LUIS)](LUIS/luis-container-howto.md)
