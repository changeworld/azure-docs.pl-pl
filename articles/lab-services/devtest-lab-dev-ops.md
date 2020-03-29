---
title: Integracja laboratoriów i devops platformy Azure DevTest | Dokumenty firmy Microsoft
description: Dowiedz się, jak korzystać z laboratoriów platformy Azure DevTest Labs w ramach potoków ciągłej integracji (CI)/ ciągłego dostarczania (CD) w środowisku przedsiębiorstwa.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "67078926"
---
# <a name="integration-of-azure-devtest-labs-and-azure-devops"></a>Integracja laboratoriów Azure DevTest i Azure DevOps
DevOps to metodologia tworzenia oprogramowania, która integruje tworzenie oprogramowania (Dev) z operacjami (Ops) dla systemu. Ten system może dostarczać nowe funkcje, aktualizacje i poprawki w zgodzie z celami biznesowymi. Metodologia ta obejmuje wszystko, od projektowania nowych funkcji w oparciu o cele, wzorce użycia i opinie klientów; do mocowania, odzyskiwania i hartowania systemu w przypadku wystąpienia problemów. Łatwo identyfikowanym elementem tej metodologii jest rurociąg ciągłej integracji (CI)/ ciągłego dostarczania (CD). Potok ciągłej integracji/ciągłego wdrażania pobiera informacje, kod i zasoby z zatwierdzenia za pomocą serii kroków, które obejmują tworzenie, testowanie i wdrażanie w celu wytworzenia systemu. W tym artykule koncentruje się na różnych sposobów efektywnego korzystania z laboratoriów w potoku w środowisku przedsiębiorstwa. 

## <a name="benefits-of-using-labs-in-devops-workflow"></a>Korzyści z używania laboratoriów w obiegu pracy DevOps 

### <a name="focused-access"></a>Ukierunkowany dostęp 
Używanie laboratorium jako składnika umożliwia określonemu ekosystemowi powiązanie z ograniczoną grupą osób. Zazwyczaj zespół lub grupa, która pracuje w obszarze wspólnym lub określonej funkcji, będzie miała przypisane laboratorium.   

### <a name="infrastructure-replication-in-the-cloud"></a>Replikacja infrastruktury w chmurze 
Deweloper może szybko skonfigurować ekosystem deweloperski, który zawiera pole deweloperskie z kodem źródłowym i narzędziami. Deweloper może również utworzyć środowisko, które jest prawie identyczne z konfiguracją produkcji. Pomaga w szybszym rozwoju pętli wewnętrznej. 

### <a name="pre-production"></a>Przedprodukcyjne 
Posiadanie laboratorium w potoku ciągłej integracji/ciągłego wdrażania ułatwia uruchamianie wielu różnych środowisk przedprodukcyjnych lub maszyn działających w tym samym czasie w celu testowania asynchronisty. Różne infrastruktury pomocy technicznej, takie jak agentów kompilacji można wdrożyć i zarządzać w laboratorium. 

## <a name="devops-with-devtest-labs"></a>DevOps z Laboratoriami DevTest 

### <a name="development--operation"></a>Rozwój / Operacja 
Laboratorium powinno koncentrować się na zespole, który pracuje w obszarze funkcji. Ten wspólny fokus umożliwia udostępnianie zasobów specyficznych dla obszaru, takich jak narzędzia, skrypty lub szablony Menedżera zasobów. Umożliwia szybsze zmiany przy jednoczesnym ograniczeniu negatywnych skutków do mniejszej grupy. Te zasoby udostępnione umożliwiają deweloperowi tworzenie maszyn wirtualnych deweloperów przy pomocą wszystkich niezbędnych kodów, narzędzi i konfiguracji. Można je tworzyć dynamicznie lub mieć system, który tworzy obrazy podstawowe z dostosowaniami. Deweloper nie tylko może tworzyć maszyny wirtualne, ale także może tworzyć środowiska Laboratoriów DevTest na podstawie szablonów niezbędnych do tworzenia odpowiednich zasobów platformy Azure w laboratorium. Wszelkie zmiany lub destrukcyjne prace można wykonać w środowisku laboratoryjnym bez wpływu na nikogo innego. Należy wziąć pod uwagę scenariusz, w którym produkt jest autonomiczny system zainstalowany na komputerze klienta. W tym scenariuszu DevTest Labs ma ulepszone tworzenie maszyn wirtualnych, która obejmuje instalowanie dodatkowego oprogramowania przy użyciu artefaktów i wstępnego tworzenia konfiguracji klienta dla szybszego testowania wewnętrznej pętli kodu. 
  
## <a name="cicd-pipeline"></a>Potok ciągłej integracji/ciągłego wdrażania 
Potok ciągłej integracji/ciągłego wdrażania jest jednym z kluczowych składników w devops, które przenoszą kod z żądania ściągnięcia dewelopera, integruje go z istniejącym kodem i wdraża go w ekosystemie produkcyjnym. Wszystkie zasoby nie muszą znajdować się w laboratorium. Na przykład hosta usługi Jenkins można skonfigurować poza laboratorium jako bardziej trwały zasób. Oto kilka konkretnych przykładów integracji laboratoriów z potoku. 

### <a name="build"></a>Kompilacja 
Potok kompilacji koncentruje się na tworzeniu pakietu składników, które zostaną przetestowane razem, które zostaną przekazane do potoku wydania. Laboratoria mogą być częścią potoku kompilacji jako lokalizacji dla agentów kompilacji i innych zasobów pomocy technicznej. Możliwość dynamicznego budowania infrastruktury pozwala na większą kontrolę. Dzięki możliwości wielu środowisk w laboratorium, każda kompilacja może być uruchamiana asynchronicznie podczas korzystania z identyfikatora kompilacji jako części informacji o środowisku, aby jednoznacznie zidentyfikować zasoby do określonej kompilacji.   

W przypadku agentów kompilacji możliwość ograniczenia dostępu przez laboratorium zwiększa bezpieczeństwo i zmniejsza możliwość przypadkowego uszkodzenia.  

### <a name="test"></a>Testowanie 
DevTest Labs umożliwia potoku ciągłej integracji/ciągłego wdrażania do automatyzacji tworzenia zasobów platformy Azure (maszyny wirtualne, środowiska), które mogą służyć do automatycznego i ręcznego testowania. Maszyny wirtualne zostaną utworzone przy użyciu artefaktów lub formuł, które używają informacji z procesu kompilacji do tworzenia różnych konfiguracji niestandardowych niezbędnych do testowania.   

### <a name="release"></a>Release 
DevTest Labs jest często używany do weryfikacji w sekcji wydania przed wdrożeniem kodu. Jest podobny do testowania w sekcji Kompilacja. Zasoby produkcyjne nie powinny być wdrażane w laboratoriach DevTest. 

### <a name="customization"></a>Dostosowywanie 
W usłudze Azure DevOps istnieją istniejące zadania, które umożliwiają manipulowanie maszynami wirtualnymi i środowiskami w określonych laboratoriach. Usługi DevOps platformy Azure są jednym ze sposobów zarządzania potokiem ciągłej integracji/ciągłego wdrażania, można zintegrować laboratorium z dowolnym systemem, który obsługuje możliwość wywoływania interfejsów API REST, wykonywania skryptów programu PowerShell lub korzystania z interfejsu wiersza polecenia platformy Azure. 

Podczas gdy niektórzy menedżerowie potoku ciągłej integracji/ciągłego wdrażania mają istniejące wtyczki typu open source, które mogą zarządzać zasobami w laboratoriach Azure i DevTest Labs. Może być konieczne użycie niektórych skryptów niestandardowych, aby dopasować do określonych potrzeb potoku.  Mając to na uwadze, podczas wykonywania zadania jednostki usługi jest używany z odpowiednią rolą, aby uzyskać dostęp do laboratorium. Podmiot usługi zwykle potrzebuje dostępu roli współautora do laboratorium. Aby uzyskać więcej informacji, zobacz [Integrowanie laboratoriów azure devtests z potokiem ciągłej integracji i dostarczania usługi Azure DevOps.](devtest-lab-integrate-ci-cd-vsts.md) 
 