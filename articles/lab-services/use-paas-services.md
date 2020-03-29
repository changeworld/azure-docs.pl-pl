---
title: Korzystanie z usług Platformy jako usługi (PaaS) w laboratoriach DevTest Azure
description: Dowiedz się, jak korzystać z usług Platformy jako usługi (Pass) w laboratorium devtest platformy Azure.
services: devtest-lab,lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/16/2020
ms.author: spelluru
ms.openlocfilehash: 88bbf921fedae4bcdba2b6386ce6e08105206cd2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76169207"
---
# <a name="use-platform-as-a-service-paas-services-in-azure-devtest-labs"></a>Korzystanie z usług Platformy jako usługi (PaaS) w laboratoriach DevTest Azure
PaaS jest obsługiwany w DevTest Labs za pośrednictwem funkcji środowisk. Środowiska w laboratoriach DevTest są obsługiwane przez wstępnie skonfigurowane szablony usługi Azure Resource Manager w repozytorium Git. Środowiska mogą zawierać zasoby PaaS i IaaS. Umożliwiają one tworzenie złożonych systemów, które mogą zawierać zasoby platformy Azure, takie jak maszyny wirtualne, bazy danych, sieci wirtualne i aplikacje sieci Web, które są dostosowane do współpracy. Te szablony umożliwiają spójne wdrażanie i ulepszone zarządzanie środowiskami przy użyciu kontroli kodu źródłowego. 

Prawidłowo skonfigurowany system umożliwia następujące scenariusze: 

- Deweloperzy mają niezależne i wiele środowisk
- Testowanie różnych konfiguracji asynchronicznie
- Integracja z rurociągami przejściowymi i produkcyjnymi bez żadnych zmian szablonu
- Posiadanie zarówno maszyn programistów, jak i środowisk w tym samym laboratorium zwiększa łatwość zarządzania i raportowania kosztów.  

Dostawca zasobów DevTest Labs tworzy zasoby w imieniu użytkownika laboratorium, więc nie trzeba nadawać żadnych dodatkowych uprawnień użytkownikowi laboratorium, aby umożliwić korzystanie z zasobów PaaS. Na poniższej ilustracji przedstawiono klaster sieci szkieletowej usług jako środowisko w laboratorium.

![Klaster sieci szkieletowej usług jako środowisko](./media/create-environment-service-fabric-cluster/cluster-created.png)

Aby uzyskać więcej informacji na temat konfigurowania środowisk, zobacz [Tworzenie środowisk wielu maszyn wirtualnych i zasobów PaaS za pomocą szablonów usługi Azure Resource Manager](devtest-lab-create-environment-from-arm.md). DevTest Labs ma publiczne repozytorium szablonów usługi Azure Resource Manager, których można używać do tworzenia środowisk bez konieczności samodzielnego łączenia się z zewnętrznym źródłem usługi GitHub. Aby uzyskać więcej informacji na temat środowisk publicznych, zobacz [Konfigurowanie i używanie środowisk publicznych w laboratoriach DevTest Azure.](devtest-lab-configure-use-public-environments.md)

W dużych organizacjach zespoły programistyczne zazwyczaj zapewniają środowiska, takie jak dostosowane/izolowane środowiska testowe. Mogą istnieć środowiska, które mają być używane przez wszystkie zespoły w jednostce biznesowej lub dywizji. Grupa IT może chcieć udostępnić swoje środowiska, które mogą być używane przez wszystkie zespoły w organizacji.  

## <a name="customizations"></a>Dostosowania

#### <a name="sandbox"></a>Piaskownica 
Właściciel laboratorium można dostosować środowiska laboratoryjne, aby zmienić rolę użytkownika z **czytnika** do **współautora** w grupie zasobów. Ta funkcja znajduje się na stronie **Ustawienia laboratorium** w obszarze **Konfiguracja i zasady** laboratorium. Ta zmiana roli umożliwia użytkownikowi dodawanie lub usuwanie zasobów w tym środowisku. Jeśli chcesz ograniczyć dostęp dalej, użyj zasad platformy Azure. Ta funkcja umożliwia dostosowanie zasobów lub konfiguracji bez dostępu na poziomie subskrypcji.

#### <a name="custom-tokens"></a>Tokeny niestandardowe
Istnieje kilka niestandardowych informacji laboratoryjnych, które znajdują się poza grupą zasobów i są specyficzne dla środowisk, do których szablon może uzyskać dostęp. Oto niektóre z nich: 

- Identyfikacja sieci laboratoryjna
- Lokalizacja
- Konto magazynu, na którym przechowywane są pliki szablonów Menedżera zasobów. 
 
#### <a name="lab-virtual-network"></a>Sieć wirtualna laboratorium
Środowiska [łączące z siecią wirtualną laboratorium](connect-environment-lab-virtual-network.md) opisano sposób modyfikowania szablonu Menedżera zasobów w celu użycia tokenu. `$(LabSubnetId)` Po utworzeniu środowiska `$(LabSubnetId)` token jest zastępowany przez pierwszy znak podsieci, w którym opcja **tworzenia maszyny wirtualnej** jest ustawiona na **true**. Pozwala to naszemu środowisku na korzystanie z wcześniej utworzonych sieci. Jeśli chcesz używać tych samych szablonów Menedżera zasobów w środowiskach `$(LabSubnetId)` w teście jako przemieszczania i produkcji, użyj jako wartości domyślnej w parametrze szablonu Menedżera zasobów. 

#### <a name="environment-storage-account"></a>Konto magazynu środowiska
DevTest Labs obsługuje korzystanie z [zagnieżdżonych szablonów Menedżera zasobów](../azure-resource-manager/templates/linked-templates.md). W[artykule [ Wdrażanie zagnieżdżonych szablonów usługi Azure Resource Manager do testowania środowisk wyjaśniono,](deploy-nested-template-environments.md) jak używać `_artifactsLocation` i `_artifactsLocationSasToken` tokenów do tworzenia identyfikatora URI szablonu Menedżera zasobów w tym samym folderze co lub w zagnieżdżonym folderze szablonu głównego. Aby uzyskać więcej informacji na temat tych dwóch tokenów, zobacz sekcję **Artefakty wdrażania** [usługi Azure Resource Manager — Przewodnik po sprawdzonych praktykach.](https://github.com/Azure/azure-quickstart-templates/blob/master/1-CONTRIBUTION-GUIDE/best-practices.md)

## <a name="user-experience"></a>Doświadczenie użytkownika

## <a name="developer"></a>Developer
Deweloperzy używają tego samego przepływu pracy do tworzenia maszyny Wirtualnej do tworzenia określonego środowiska. Wybierają środowisko a obraz maszyny i wprowadź niezbędne informacje wymagane przez szablon. Każdy deweloper posiadający środowisko umożliwia wdrażanie zmian i ulepszone debugowanie w pętli wewnętrznej. Środowisko można utworzyć w dowolnym momencie przy użyciu najnowszego szablonu.  Ta funkcja umożliwia zniszczenie i odtworzenie środowisk w celu skrócenia przestojów z konieczności ręcznego tworzenia systemu lub odzyskiwania po testowaniu błędów.  

### <a name="testing"></a>Testowanie
Środowiska DevTest Labs umożliwiają niezależne testowanie określonego kodu i konfiguracji asynchronicznie. Powszechną praktyką jest skonfigurowanie środowiska z kodem z indywidualnego żądania ściągnięcia i rozpoczęcie wszelkich automatycznych testów. Po uruchomieniu automatycznego testowania do zakończenia, wszelkie ręczne testowanie mogą być wykonywane w określonym środowisku. Zazwyczaj ten proces odbywa się jako część potoku ciągłej integracji/ciągłego wdrażania. 

## <a name="management-experience"></a>Doświadczenie w zarządzaniu

### <a name="cost-tracking"></a>Śledzenie kosztów
Funkcja śledzenia kosztów obejmuje zasoby platformy Azure w różnych środowiskach jako część ogólnego trendu kosztów. Koszt zasobów nie rozbija różnych zasobów w środowisku, ale wyświetla środowisko jako pojedynczy koszt.

### <a name="security"></a>Zabezpieczenia
Prawidłowo skonfigurowana subskrypcja platformy Azure za pomocą laboratorium DevTest Labs może [ograniczyć dostęp do zasobów platformy Azure tylko za pośrednictwem laboratorium.](devtest-lab-add-devtest-user.md) W środowiskach właściciel laboratorium może zezwolić użytkownikom na dostęp do zasobów PaaS z zatwierdzonymi konfiguracjami bez zezwalania na dostęp do innych zasobów platformy Azure. W scenariuszu, w którym użytkownicy laboratorium dostosować środowiska, właściciel laboratorium może zezwolić na dostęp współautora. Dostęp współautora umożliwia użytkownikowi laboratorium dodawanie lub usuwanie zasobów platformy Azure tylko w ramach zarządzanej grupy zasobów. Umożliwia łatwiejsze śledzenie i zarządzanie, a także umożliwia dostęp współautora dostępu do subskrypcji.

### <a name="automation"></a>Automatyzacja
Automatyzacja jest kluczowym elementem dla dużego, efektywnego ekosystemu. Automatyzacja jest niezbędna do obsługi zarządzania lub śledzenia wielu środowisk w ramach subskrypcji i laboratoriów.

### <a name="cicd-pipeline"></a>Potok ciągłej integracji/dysku CD
Usługi PaaS w laboratoriach DevTest labs mogą pomóc w ulepszeniu potoku ciągłej integracji/ciągłego wdrażania, koncentrując się na wdrożeniach, w których dostęp jest kontrolowany przez laboratorium.

## <a name="next-steps"></a>Następne kroki
Szczegółowe informacje na temat środowisk można znaleźć w następujących artykułach: 

- 
- [Tworzenie środowisk z wieloma maszynami wirtualnymi i zasobów PaaS za pomocą szablonów usługi Azure Resource Manager](devtest-lab-create-environment-from-arm.md)
- [Konfigurowanie i używanie środowisk publicznych w laboratoriach devtest azure](devtest-lab-configure-use-public-environments.md)
- [Tworzenie środowiska z samodzielnym klastrem sieci szkieletowej usług w laboratoriach devtest azure](create-environment-service-fabric-cluster.md)
- [Łączenie środowiska z siecią wirtualną laboratorium w laboratorium Azure DevTest Labs](connect-environment-lab-virtual-network.md)
- [Integrowanie środowisk z potokami ciągłej integracji/dysku CD usługi Azure DevOps](integrate-environments-devops-pipeline.md)
 





