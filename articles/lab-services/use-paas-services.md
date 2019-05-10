---
title: Korzystania z usług Platform-as-a-Service (PaaS) w usłudze Azure DevTest Labs | Dokumentacja firmy Microsoft
description: Dowiedz się, jak używać usługi Platform-as-a-Service (Pass) w usłudze Azure DevTest Labs.
services: devtest-lab,lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/02/2019
ms.author: spelluru
ms.openlocfilehash: 7cdd185cddbd2403b72ff0e06530913af0b031de
ms.sourcegitcommit: 2ce4f275bc45ef1fb061932634ac0cf04183f181
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/07/2019
ms.locfileid: "65233127"
---
# <a name="use-platform-as-a-service-paas-services-in-azure-devtest-labs"></a>Korzystania z usług Platform-as-a-Service (PaaS) w usłudze Azure DevTest Labs
PaaS jest obsługiwane w usłudze DevTest Labs przy użyciu funkcji środowiska. Środowisk w usłudze DevTest Labs są obsługiwane przez wstępnie skonfigurowane szablony usługi Azure Resource Manager w repozytorium Git. Środowiska mogą zawierać zasoby usługi PaaS i IaaS. Umożliwiają one tworzenie złożonych systemów, które mogą obejmować zasoby platformy Azure, takich jak maszyny wirtualne, bazy danych, sieci wirtualnych i aplikacji sieci Web, które są dostosowane do współpracują ze sobą. Te szablony umożliwiają spójne wdrażanie i ulepszone Zarządzanie środowiskami przy użyciu kontroli kodu źródłowego. 

A poprawnie skonfigurować system zezwala na następujące scenariusze: 

- Deweloperzy mają niezależne i wielu środowisk
- Testowanie w różnych konfiguracjach asynchronicznie
- Integracja z tymczasowych i produkcyjnych potoków bez konieczności wprowadzania zmian w szablonie
- O komputerach deweloperskich i środowisk, w tym samym laboratorium zwiększa łatwość zarządzania i raportowania kosztów.  

Dostawca zasobów usługi DevTest Labs tworzy zasobów w imieniu użytkownika laboratorium, więc żadne dodatkowe uprawnienia, które należy nadać użytkownikowi laboratorium umożliwia korzystanie z zasobów PaaS. Na poniższej ilustracji przedstawiono klaster usługi Service Fabric jako środowiska w laboratorium.

![Klaster usługi Service Fabric, ponieważ środowisko](./media/create-environment-service-fabric-cluster/cluster-created.png)

Aby uzyskać więcej informacji na temat konfigurowania środowiska, zobacz [tworzenie środowisk z wieloma Maszynami wirtualnymi i zasobów PaaS za pomocą szablonów usługi Azure Resource Manager](devtest-lab-create-environment-from-arm.md). DevTest Labs ma publicznego repozytorium szablonów usługi Azure Resource Manager, których można użyć do tworzenia środowisk bez konieczności nawiązywania połączenia z zewnętrznym źródłem GitHub samodzielnie. Aby uzyskać więcej informacji o środowiskach publicznych, zobacz [Konfigurowanie i używanie środowiskach publicznych w usłudze Azure DevTest Labs](devtest-lab-configure-use-public-environments.md).

W dużych organizacjach zespoły deweloperów zwykle zapewniają środowiska, takie jak dostosować izolowane środowiska testowe. Może to być środowiska, które mają być używane przez wszystkich zespołów w obrębie wydziału lub jednostki biznesowej. Grupa IT może chcieć zapewnić w swoich środowiskach, które mogą być używane przez wszystkie zespoły w organizacji.  

## <a name="customizations"></a>Dostosowania

#### <a name="sandbox"></a>Piaskownica 
Właściciel laboratorium można dostosować środowisk laboratoryjnych, aby zmienić rolę użytkownika z **czytnika** do **Współautor** w grupie zasobów. Ta funkcja jest w **ustawienia Lab** strony w obszarze **konfiguracji i zasad** laboratorium. Ta zmiana w roli zezwala użytkownikowi na dodawanie lub usuwanie zasobów w tym środowisku. Jeśli chcesz ograniczyć dostęp do dalszego Użyj zasad platformy Azure. Ta funkcja umożliwia dostosowanie zasobów lub konfiguracji bez dostępu na poziomie subskrypcji.

#### <a name="custom-tokens"></a>Tokeny niestandardowe
Brak niektórych informacji laboratorium niestandardowego, będzie znajdować się poza grupę zasobów, która jest specyficzne dla środowisk, w których mogą uzyskiwać dostęp do szablonu. Poniżej przedstawiono niektóre z nich: 

- Identyfikator sieci laboratorium
- Lokalizacja
- Konto magazynu, w którym są przechowywane pliki szablonów usługi Resource Manager. 
 
#### <a name="lab-virtual-network"></a>Sieci wirtualne laboratorium
[Środowisk nawiązywania połączenia z sieci wirtualnej laboratorium](connect-environment-lab-virtual-network.md) artykuł opisuje sposób modyfikowania szablon usługi Resource Manager, aby użyć `$(LabSubnetId)` tokenu. Po utworzeniu środowiska `$(LabSubnetId)` token jest zastępowany przez pierwszy znak podsieci gdzie **maszynie wirtualnej Utwórz** ustawiono opcję **true**. Umożliwia ona naszego środowiska użyć wcześniej utworzone sieci. Jeśli chcesz użyć tego samego szablonów usługi Resource Manager w środowiskach w teście jako środowisk przejściowych i produkcyjnych, użyj `$(LabSubnetId)` jako wartość domyślna parametru szablonu usługi Resource Manager. 

#### <a name="environment-storage-account"></a>Konto magazynu w środowisku
DevTest Labs obsługuje korzystanie z [zagnieżdżonych szablonów usługi Resource Manager](../azure-resource-manager/resource-group-linked-templates.md). [Jak usługi Azure DevTest Labs ułatwia zagnieżdżonych usługi Resource Manager Szablon wdrożenia dla środowisk testowych](https://azure.microsoft.com/updates/azure-devtest-labs-streamlined-nested-arm-template-deployment-support-for-arm-template-based-environments) artykule wyjaśniono, jak używać `_artifactsLocation` i `_artifactsLocationSasToken` tokenów do utworzenia identyfikatora URI do szablonu usługi Resource Manager w tym samym folderze co lub w zagnieżdżonej folderu głównego szablonu. Aby uzyskać więcej informacji na temat tych dwóch tokenów, zobacz **artefaktów wdrożenia** części [usługi Azure Resource Manager — Przewodnik dobre praktyki](https://github.com/Azure/azure-quickstart-templates/blob/master/1-CONTRIBUTION-GUIDE/best-practices.md).

## <a name="user-experience"></a>Środowisko użytkownika

## <a name="developer"></a>Deweloper
Deweloperzy Użyj tego samego przepływu pracy do tworzenia maszyny Wirtualnej, aby utworzyć w danym środowisku. One wybierz środowisko, a obraz maszyny i wprowadź wymagane informacje, które są wymagane przez szablon. Każdy deweloper o środowisku umożliwia wdrażanie zmian i ulepszone wewnętrzną pętlę debugowania. Środowiska mogą być tworzone w dowolnym momencie przy użyciu najnowszych szablonu.  Ta funkcja umożliwia środowisk zniszczone i ponownie w celu zmniejszyć przestoje z konieczności ręcznego tworzenia systemu lub odzyskiwanie z Testowanie błędów.  

### <a name="testing"></a>Testowanie
Środowiska usługi DevTest Labs umożliwia niezależne testowania określonego kodu i konfiguracje asynchronicznie. Częstą praktyką jest konfiguracji środowiska, podając mu kod z danym żądaniem ściągnięcia poszczególnych i uruchomić zautomatyzowane testy. Po uruchomieniu automatycznych testów do zakończenia, ręczne testy mogą być wykonywane względem określonego środowiska. Zazwyczaj ten proces odbywa się jako część potoku ciągłej integracji/ciągłego wdrażania. 

## <a name="management-experience"></a>Możliwości zarządzania

### <a name="cost-tracking"></a>Śledzenie kosztów
Funkcja śledzenie kosztów obejmuje zasobów platformy Azure w różnych środowiskach jako część ogólnej trendu kosztów. Koszt w przeliczeniu na zasoby nie rozbicie różnych zasobów w środowisku, ale wyświetla środowiska jako pojedynczy koszt.

### <a name="security"></a>Bezpieczeństwo
Można prawidłowo skonfigurowane subskrypcji platformy Azure z usługą DevTest Labs [ograniczyć dostęp do zasobów platformy Azure tylko za pośrednictwem laboratorium](devtest-lab-add-devtest-user.md). W środowiskach właściciel laboratorium można zezwolić użytkownikom na dostęp do zasobów PaaS przy użyciu konfiguracji zatwierdzonych bez zezwalania na dostęp do innych zasobów platformy Azure. W tym scenariuszu, w której użytkownicy laboratorium Dostosowywanie środowiska właściciel laboratorium można zezwolić na dostęp współautora. Dostęp współautora umożliwia użytkownikowi laboratorium Dodawanie lub usuwanie zasobów platformy Azure tylko w zarządzanej grupie zasobów. Umożliwia prostsze śledzenie i zarządzanie w porównaniu z umożliwia użytkownikowi dostęp współautora do subskrypcji.

### <a name="automation"></a>Automatyzacja
Usługa Automation jest kluczowym składnikiem na dużą skalę, od ekosystemu. Usługa Automation jest niezbędne do obsługi śledzenia wielu środowisk w subskrypcji i laboratoriów lub zarządzania nimi.

### <a name="cicd-pipeline"></a>Potok ciągłej integracji/ciągłego wdrażania
Usługi PaaS w usłudze DevTest Labs może zwiększyć potoku ciągłej integracji/ciągłego wdrażania, mające fokus wdrożeń, w których dostęp jest kontrolowany przez laboratorium.

## <a name="next-steps"></a>Kolejne kroki
Zobacz następujące artykuły, aby uzyskać szczegółowe informacje o środowiskach: 

- 
- [Tworzenie środowisk z wieloma maszynami wirtualnymi i zasobów PaaS za pomocą szablonów usługi Azure Resource Manager](devtest-lab-create-environment-from-arm.md)
- [Konfigurowanie i używanie środowiskach publicznych w usłudze Azure DevTest Labs](devtest-lab-configure-use-public-environments.md)
- [Utwórz środowisko przy użyciu niezależna klastra usługi Service Fabric w usłudze Azure DevTest Labs](create-environment-service-fabric-cluster.md)
- [Środowisko nawiązać połączenie z sieci wirtualnej w środowisku laboratoryjnym w usłudze Azure DevTest Labs](connect-environment-lab-virtual-network.md)
- [Integrowanie środowiska potoków usługi Azure DevOps ciągłej integracji/ciągłego wdrażania](integrate-environments-devops-pipeline.md)
 





