---
title: Obsługa kontenerów w usługach Azure Cognitive Services
titleSuffix: Azure Cognitive Services
description: Dowiedz się, jak kontenery platformy Docker można bliżej Cognitive Services do swoich danych.
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.topic: article
ms.date: 11/14/2018
ms.author: diberry
ms.openlocfilehash: b66d6bc93f9d2df3eccabf0f0f9edb0384bab70f
ms.sourcegitcommit: 0b7fc82f23f0aa105afb1c5fadb74aecf9a7015b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/14/2018
ms.locfileid: "51635022"
---
# <a name="container-support-in-azure-cognitive-services"></a>Obsługa kontenerów w usługach Azure Cognitive Services

Obsługa kontenerów w usługach Azure Cognitive Services umożliwia deweloperom korzystanie z tych samych zaawansowanych interfejsów API, które są dostępne na platformie Azure, ale dzięki elastyczności, który jest dostarczany z [kontenerów platformy Docker](https://www.docker.com/what-container). Obsługa kontenerów jest obecnie dostępna w wersji zapoznawczej dla podzbioru usług Azure Cognitive Services, łącznie z części [komputerowej](Computer-vision/Home.md), [twarzy](Face/Overview.md), i [analizy tekstu](text-analytics/overview.md).

Konteneryzacji to podejście do dystrybucji oprogramowania, w której aplikacja lub usługa, łącznie z jego zależności & konfiguracji, ma postać razem obrazu kontenera. Za pomocą niewielkie modyfikacje można wdrożyć obraz kontenera na hoście kontenera. Kontenery są odizolowane od siebie nawzajem i zasadniczego systemu operacyjnego za pomocą mniejszy wyświetlacz niż maszyny wirtualnej. Kontenery mogą utworzonych na podstawie obrazów kontenera do krótkoterminowych zadań i usuwane, gdy nie będą już potrzebne.

Obejrzyj ten [krótkie wideo](https://azure.microsoft.com/resources/videos/containers-support-of-cognitive-services) demonstracyjne.

[Komputerowej](Computer-vision/Home.md), [twarzy](Face/Overview.md), i [analizy tekstu](text-analytics/overview.md) usługi są dostępne w [Microsoft Azure](https://azure.microsoft.com). Zaloguj się do [witryny Azure portal](https://portal.azure.com/) do tworzenia i zapoznaj się z zasobami platformy Azure w przypadku tych usług.

## <a name="features-and-benefits"></a>Funkcje i korzyści

- **Kontrola nad danymi**: umożliwiają klientom za pomocą usług Cognitive Services pełną kontrolę nad ich danymi.  Jest to istotne w przypadku klientów, którzy nie mogą wysyłać dane do chmury, ale muszą mieć dostęp do technologii usług Cognitive Services. Obsługa spójności w środowiskach hybrydowych — wielu danych, zarządzania, tożsamości i zabezpieczeń.
- **Kontrolę nad aktualizacjami modelu**: zapewnić klientom elastyczność przechowywanie wersji i aktualizowanie modeli wdrożone w swoich rozwiązaniach.
- **Architektura przenośne**: umożliwiają tworzenie przenośnych architektura wdrożoną w chmurze, lokalnych i urządzenia brzegowe. Kontenery — można również wdrożyć bezpośrednio do [usługi Azure Kubernetes Service](/azure/aks/), [usługi Azure Container Instances](/azure/container-instances/), lub [Kubernetes](https://kubernetes.io/) wdrażany klaster do [platformy Azure Stos](/azure/azure-stack/). Aby uzyskać więcej informacji, zobacz [wdrażanie platformy Kubernetes w usłudze Azure Stack](/azure/azure-stack/user/azure-stack-solution-template-kubernetes-deploy).
- **Wysoka przepływność / małych opóźnieniach**: zapewnia klientom możliwość skalowania dotyczące wysokiej przepływności i małego opóźnienia, włączając usług Cognitive Services do uruchamiania w usłudze Azure Kubernetes Service fizycznie blisko ich logiki aplikacji i danych.


## <a name="containers-in-azure-cognitive-services"></a>Kontenery w usługach Azure Cognitive Services

Kontenery usługi Azure Cognitive Services zapewniają następujący zestaw kontenerów platformy Docker, z których każdy zawiera podzbiór funkcji z usług w usługach Azure Cognitive Services:

| Usługa | Kontener| Opis |
|---------|----------|-------------|
|[Przetwarzanie obrazów](Computer-vision/computer-vision-how-to-install-containers.md) |**Rozpoznawanie tekstu** |Wyodrębnia tekst drukowanego z obrazów różne obiekty na różnych powierzchniach i tłach, takich jak przychody oraz plakaty i wizytówki.<br/><br/>**Ważne:** kontenerze rozpoznawanie tekstu obecnie działa tylko w języku angielskim.<br>[Poproś o dostęp](Computer-vision/computer-vision-how-to-install-containers.md#request-access-to-the-private-container-registry)|
|[Twarzy](Face/face-how-to-install-containers.md) |**Twarzy** |Wykrywa twarze osób na obrazach i identyfikuje atrybutów, w tym to punktów charakterystycznych (takie jak elementy ruchome i oczy), płeć, wiek i inne funkcje twarzy przewidzieć maszyny. Oprócz wykrywania twarzy można sprawdzić, czy dwie twarze w ten sam obraz lub różne obrazy są takie same, za pomocą współczynnik ufności lub porównywania twarzy względem bazy danych, aby sprawdzić, czy podobnie wyglądających twarzy identyczne już istnieje. Można również zorganizować podobnych twarzy w grupach, przy użyciu udostępnionego visual traits.<br>[Poproś o dostęp](Face/face-how-to-install-containers.md#request-access-to-the-private-container-registry) |
|[Analiza tekstu](text-analytics/how-tos/text-analytics-how-to-install-containers.md) |**Klucz frazy** ([obraz](https://go.microsoft.com/fwlink/?linkid=2018757)) |Wyodrębnianie kluczowych fraz można identyfikować jego główne punkty. Na przykład dla tekstu wejściowego „Jedzenie było pyszne, a serwowała je doskonała obsługa” interfejs API zwraca główne tematy wypowiedzi: „jedzenie” i „doskonała obsługa”. |
|[Analiza tekstu](text-analytics/how-tos/text-analytics-how-to-install-containers.md)|**Wykrywanie języka** ([obraz](https://go.microsoft.com/fwlink/?linkid=2018759)) |Maksymalnie 120 języków wykrywa, które tekst wejściowy został napisany w języku i kod jeden język, dla każdego dokumentu Przesłano żądanie raportu. Kod języka jest powiązany z oceną, co wskazuje siłę oceny. |
|[Analiza tekstu](text-analytics/how-tos/text-analytics-how-to-install-containers.md)|**Analiza tonacji** ([obraz](https://go.microsoft.com/fwlink/?linkid=2018654)) |Analizuje nieprzetworzony tekst dla wskazówek dotyczących opinii dodatnia lub ujemna. Ten interfejs API zwraca ocenę tonacji od 0 do 1 dla każdego dokumentu, przy czym 1 oznacza najbardziej pozytywną tonację. Modele analizy są wstępnie przeszkolonych, za pomocą rozbudowane treści, tekstu i języka naturalnego technologii firmy Microsoft. W przypadku [wybranych języków](https://docs.microsoft.com/azure/cognitive-services/text-analytics/text-analytics-supported-languages.md) interfejs API może przeanalizować i ocenić dowolny podany nieprzetworzony tekst, zwracając wyniki bezpośrednio do aplikacji wywołującej. |

## <a name="container-availability-in-azure-cognitive-services"></a>Dostępność kontenera w usługach Azure Cognitive Services

Kontenery usługi Azure Cognitive Services są publicznie dostępne w ramach subskrypcji platformy Azure i mogą być ściągane obrazów kontenerów platformy Docker z rejestru kontenerów firmy Microsoft lub z usługi Docker Hub. Możesz użyć [polecenie docker pull](https://docs.docker.com/engine/reference/commandline/pull/) polecenie, aby pobrać obraz kontenera z rejestru odpowiednie.

> [!IMPORTANT]
> Obecnie musisz ukończyć procesu rejestracji w celu uzyskania dostępu do [twarzy](Face/face-how-to-install-containers.md) i [Rozpoznaj tekst](Computer-vision/computer-vision-how-to-install-containers.md) kontenery, w których Wypełnij i Prześlij kwestionariusza w razie pytań dotyczących, firmy i przypadek użycia dla którego chcesz zaimplementować kontenerów. Gdy jesteś uzyskuje dostęp i podano poświadczenia, wtedy mogła ściągać obrazy kontenerów dla kontenerów twarzy i rozpoznawanie tekstu z prywatnego rejestru kontenerów hostowanej przez usługę Azure Container Registry.

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem korzystania z kontenerów usług Azure Cognitive Services, muszą spełniać następujące wymagania wstępne:

**Aparat platformy docker**: konieczne jest posiadanie aparat platformy Docker zainstalowany lokalnie. Środowisko docker zawiera pakiety, które konfigurują środowisko platformy Docker na [macOS](https://docs.docker.com/docker-for-mac/), [Linux](https://docs.docker.com/engine/installation/#supported-platforms), i [Windows](https://docs.docker.com/docker-for-windows/). W Windows platformy Docker musi być skonfigurowany do obsługi kontenerów systemu Linux. Kontenery platformy docker można także bezpośrednio do wdrożyć [usługi Azure Kubernetes Service](/azure/aks/) lub [usługi Azure Container Instances](/azure/container-instances/).

Docker należy skonfigurować w taki sposób, aby umożliwić kontenerów, aby nawiązać połączenie z, a następnie wysyłać danych dotyczących rozliczeń do platformy Azure.

**Znajomość Microsoft Container Registry i Docker**: powinien mieć podstawową wiedzę na temat koncepcji Microsoft Container Registry i Docker, takich jak rejestrów, repozytoriów, kontenerów, a obrazy kontenerów, a także wiedzę na temat podstawowe `docker` poleceń.  

Aby uzyskać podstawowe informacje na temat platformy Docker i kontenerów, zobacz [Docker — omówienie](https://docs.docker.com/engine/docker-overview/).

Poszczególnych kontenerów może mieć własne wymagania, jak również w tym serwera i wymagania dotyczące alokacji pamięci.

## <a name="developer-samples"></a>Przykłady dla deweloperów

Przykłady dla deweloperów są dostępne pod adresem naszych [repozytorium Github](https://github.com/Azure-Samples/cognitive-services-containers-samples). 

## <a name="next-steps"></a>Kolejne kroki

Zainstaluj i Poznaj funkcje udostępniane przez kontenery w usługach Azure Cognitive Services:

* [Instalowanie i używanie przetwarzania obrazów kontenerów](Computer-vision/computer-vision-how-to-install-containers.md)
* [Instalowanie i używanie kontenerów twarzy](Face/face-how-to-install-containers.md)
* [Instalowanie i używanie kontenerów analizy tekstu](text-analytics/how-tos/text-analytics-how-to-install-containers.md)