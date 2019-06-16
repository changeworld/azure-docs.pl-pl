---
title: Integracja usługi Azure DevTest Labs i metodyki DevOps | Dokumentacja firmy Microsoft
description: Dowiedz się, jak korzystanie z laboratoriów usługi Azure DevTest Labs w ramach ciągłej integracji (CI) / ciągłe dostarczanie (CD) potoki w środowisku przedsiębiorstwa.
services: devtest-lab
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/10/2019
ms.author: spelluru
ms.openlocfilehash: 62c44bfea28d47d7c32aa7ef440a40d45c314683
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "67078926"
---
# <a name="integration-of-azure-devtest-labs-and-azure-devops"></a>Integracja usługi Azure DevTest Labs i metodyki DevOps platformy Azure
DevOps to metodologii rozwoju oprogramowania, zintegrowane Wytwarzanie oprogramowania (Dev) z usługą operations (Ops) dla systemu. Ten system może dostarczać nowe funkcje, aktualizacje i poprawki w sposób zgodny z celami biznesowymi. Ta metodologia obejmuje wszystko — od projektowania nowe funkcje oparte na cele, wzorców użycia i opinie. do ustalania, odzyskiwanie i Ograniczanie funkcjonalności systemu, gdy wystąpią problemy. Łatwo wskazany składnik tej metody jest ciągłej integracji (CI) / ciągłe dostarczanie (CD) potoku. Potok CI/CD przyjmuje informacje, kod i zasoby z zatwierdzenia przez szereg czynności, które obejmują tworzenia, testowania i wdrażania do produkcji systemu. Ten artykuł koncentruje się na różne sposoby efektywne korzystanie z laboratoriów w ramach potoku w środowisku przedsiębiorstwa. 

## <a name="benefits-of-using-labs-in-devops-workflow"></a>Korzyści z używania labs w przepływie pracy DevOps 

### <a name="focused-access"></a>Ukierunkowany dostęp 
Za pomocą laboratorium jako składnik umożliwia określonych ekosystemu do skojarzenia z ograniczonej grupie osób. Zazwyczaj zespół lub grupę, która działa na platformie wspólnej obszar lub określoną funkcję mają przypisaną laboratorium.   

### <a name="infrastructure-replication-in-the-cloud"></a>Replikacja infrastruktury w chmurze 
Deweloper może szybko skonfigurować ekosystem programistyczny, która zawiera pole dev z kodu źródłowego i narzędziami. Deweloper można również utworzyć środowisko, które jest prawie identyczna z konfiguracją w środowisku produkcyjnym. Pomaga ono wraz z rozwojem szybciej wewnętrzną pętlę. 

### <a name="pre-production"></a>Produkcji wstępnej 
O laboratorium w potoku CI/CD sprawia, że łatwiej jest wiele różnych środowiskach produkcji wstępnej lub maszyn uruchomionych w tym samym czasie testowania asynchronicznego. Pomocy technicznej w różnych infrastrukturach, np. agentów kompilacji mogą być wdrażania i zarządzania nimi w ramach laboratorium. 

## <a name="devops-with-devtest-labs"></a>Metodyka DevOps z usługą DevTest Labs 

### <a name="development--operation"></a>Programowanie / operacji 
Laboratorium powinna zostać zwrócona na zespół, który działa na platformie obszaru funkcji. To typowe koncentracji uwagi pozwala na udostępnianie zasobów specyficznych dla obszaru, takich jak narzędzia, skryptów i szablonów usługi Resource Manager. Umożliwia to szybsze zmiany jednoczesnym negatywnych skutków do mniejszej grupy. Te zasoby udostępnione umożliwiają deweloperom tworzenie tworzenia maszyn wirtualnych przy użyciu wszystkich niezbędnych kodu, narzędzi i konfiguracji. One można tworzyć zarówno dynamiczne lub systemu, która tworzy obrazy podstawowe za pomocą dostosowań. Nie tylko Deweloper tworzyć maszyny wirtualne, ale również można utworzyć środowiska laboratorium na podstawie szablonów niezbędne, aby utworzyć odpowiednie zasoby platformy Azure w środowisku laboratoryjnym. Wszelkie zmiany i destrukcyjne pracy może odbywać się przed środowiska laboratoryjnego bez wpływu na inne osoby. Rozważmy scenariusz, gdy produkt jest zainstalowany na komputerze klienta systemu autonomicznego. W tym scenariuszu Usługa DevTest Labs ulepszono tworzenia maszyny Wirtualnej, zawierający instalowania dodatkowego oprogramowania przy użyciu artefaktów i wstępnie tworzenia konfiguracji klienta do testowania szybciej wewnętrzną pętlę kodu. 
  
## <a name="cicd-pipeline"></a>Potok ciągłej integracji/ciągłego wdrażania 
Potok ciągłej integracji/ciągłego Dostarczania jest jednym z krytycznych składników infrastruktury DevOps, która Przenieś kod z żądania ściągnięcia dewelopera, integruje się z istniejącym kodem i wdroży je z ekosystemem produkcji. Wszystkie zasoby nie muszą być w ramach laboratorium. Na przykład host usługi Jenkins można skonfigurowania poza laboratorium jako zasób więcej trwały. Poniżej przedstawiono kilka przykładów integrowanie laboratoriów z potokiem. 

### <a name="build"></a>Kompilacja 
Potok kompilacji koncentruje się na utworzenie pakietu składników, które będą sprawdzane razem do być przekazywane do potoku tworzenia wersji. Laboratoria mogą być częścią procesu kompilacji, jako lokalizację dla agentów kompilacji i inne zasoby pomocy technicznej. Masz możliwość dynamicznego tworzenia infrastruktury daje większą kontrolę. Umożliwia korzystanie z wielu środowisk w laboratorium każdej kompilacji mogą być uruchamiane asynchronicznie podczas przy użyciu Identyfikatora kompilacji jako część informacji o środowisku do unikatowego identyfikowania zasobów do konkretnej kompilacji.   

Dla agentów kompilacji laboratorium możliwość ograniczenia dostępu zwiększa bezpieczeństwo i zmniejsza prawdopodobieństwo przypadkowego uszkodzenia.  

### <a name="test"></a>Testowanie 
DevTest Labs umożliwia potoku ciągłej integracji/ciągłego wdrażania zautomatyzować tworzenie zasobów platformy Azure (maszyny wirtualne, środowiska), który może służyć do testowania automatycznego i ręcznego. Maszyny wirtualne zostałyby utworzone przy użyciu artefaktów lub formuł, które używają informacji z procesu kompilacji, można utworzyć różne konfiguracje niestandardowe niezbędnej do testowania.   

### <a name="release"></a>Release 
DevTest Labs jest powszechnie używany do weryfikacji w sekcji wydania, zanim kod jest wdrażany. Jest on podobny do testowania w sekcji kompilacji. Zasoby w środowisku produkcyjnym nie należy wdrożyć w usłudze DevTest Labs. 

### <a name="customization"></a>Dostosowywanie 
W DevOps platformy Azure są istniejące zadania umożliwiające manipulowanie maszyn wirtualnych i środowisk, w ramach określonych labs. Usługom DevOps platformy Azure jest jednym ze sposobów zarządzania potoku ciągłej integracji/ciągłego wdrażania, możesz zintegrować laboratorium każdy system, który obsługuje możliwość wywołania interfejsów API REST, wykonywanie skryptów programu PowerShell lub użyć wiersza polecenia platformy Azure. 

Podczas niektórych menedżerów potoku ciągłej integracji/ciągłego Dostarczania jest istniejącego typu open-source znajdują się wtyczki mogą zarządzać zasobami w ramach platformy Azure i usłudze DevTest Labs. Może być konieczne użyć niektórych niestandardowych skryptów do określonych potrzeb potoku.  Mając to na uwadze, podczas wykonywania zadania jednostka usługi jest używana z odpowiednią rolę do uzyskania dostępu do laboratorium. Jednostka usługi musi zazwyczaj dostępu roli współautora do laboratorium. Aby uzyskać więcej informacji, zobacz [integracji usługi Azure DevTests Labs z potokiem DevOps platformy Azure ciągłej integracji i dostarczania](devtest-lab-integrate-ci-cd-vsts.md). 
 