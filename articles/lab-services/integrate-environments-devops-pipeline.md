---
title: Integrowanie środowisk z potokami platformy Azure w laboratoriach devtest azure
description: Dowiedz się, jak zintegrować środowiska Azure DevTest Labs z potokami ciągłej integracji usługi Azure DevOps (CI) i ciągłego dostarczania (CD).
services: devtest-lab,virtual-machines,lab-services
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
ms.openlocfilehash: 3d7e481879326ac30093bd116222bddc28640398
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76169424"
---
# <a name="integrate-environments-into-your-azure-devops-cicd-pipelines"></a>Integrowanie środowisk z potokami ciągłej integracji/dysku CD usługi Azure DevOps
Można użyć rozszerzenia zadań laboratoriów programu Azure DevTest Labs zainstalowanego w usługach Azure DevOps (wcześniej nazywanych usługami zespołu programu Visual Studio), aby łatwo zintegrować potok kompilacji i wydania ciągłej integracji (CI)/ ciągłego dostarczania (CD) z platformą Azure DevTest Labs. Te rozszerzenia ułatwiają szybkie [wdrażanie środowiska](devtest-lab-test-env.md) dla określonego zadania testowego, a następnie usuwanie go po zakończeniu testu. 

W tym artykule pokazano, jak utworzyć i wdrożyć środowisko, a następnie usunąć środowisko, wszystko w jednym kompletnym potoku. Zwykle można wykonać każde z tych zadań indywidualnie w potoku niestandardowego kompilacji-testu-wdrożenia. Rozszerzenia używane w tym artykule są dodatkiem do tych [zadań tworzenia/usuwania maszyn wirtualnych DTL:](devtest-lab-integrate-ci-cd-vsts.md)

- Tworzenie środowiska
- Usuwanie środowiska

## <a name="before-you-begin"></a>Przed rozpoczęciem
Aby zintegrować potok ciągłej integracji/ciągłego wdrażania z laboratorium devtest azure, zainstaluj rozszerzenie [zadań laboratorium programu Azure DevTest Labs](https://marketplace.visualstudio.com/items?itemName=ms-azuredevtestlabs.tasks) z witryny Visual Studio Marketplace. 

## <a name="create-and-configure-the-lab-for-environments"></a>Tworzenie i konfigurowanie laboratorium dla środowisk
W tej sekcji opisano sposób tworzenia i konfigurowania laboratorium, w którym zostanie wdrożone środowisko platformy Azure.

1. [Utwórz laboratorium,](devtest-lab-create-lab.md) jeśli jeszcze go nie masz. 
2. Skonfiguruj laboratorium i utwórz szablon środowiska, postępując zgodnie z instrukcjami z tego artykułu: [Tworzenie środowisk wielu maszyn wirtualnych i zasobów PaaS za pomocą szablonów usługi Azure Resource Manager](devtest-lab-create-environment-from-arm.md).
3. W tym przykładzie użyj istniejącego [https://azure.microsoft.com/resources/templates/201-web-app-redis-cache-sql-database/](https://azure.microsoft.com/resources/templates/201-web-app-redis-cache-sql-database/)szablonu szybkiego startu platformy Azure .
4. Skopiuj folder **201-web-app-redis-cache-sql-database** do folderu **ArmTemplate** w repozytorium skonfigurowanym w kroku 2.

## <a name="create-a-release-definition"></a>Tworzenie definicji wydania
Aby utworzyć definicję wydania, wykonaj następujące czynności:

1.  Na karcie **Zwalnia** centrum **Kompilacja & Release**wybierz przycisk znak plus **(+).**
2.  W oknie **Tworzenie definicji wydania** zaznacz **pusty** szablon, a następnie wybierz pozycję **Dalej**.
3.  Wybierz **pozycję Wybierz później**, a następnie wybierz pozycję **Utwórz,** aby utworzyć nową definicję wersji z jednym środowiskiem domyślnym i bez połączonych artefaktów.
4.  Aby otworzyć menu skrótów, w nowej definicji wersji wybierz **wielokropek (...)** obok nazwy środowiska, a następnie wybierz pozycję **Konfiguruj zmienne**.
5.  W oknie **Konfiguruj — środowisko** dla zmiennych używanych w zadaniach definicji wersji wprowadź następujące wartości:
1.  W przypadku **danych administratorlogin**wprowadź nazwę logowania administratora SQL.
2.  Dla **administratorLoginPassword**wprowadź hasło, które ma być używane przez użytkownika SQL Administrator login. Użyj ikony "kłódka", aby ukryć i zabezpieczyć hasło.
3.  W przypadku **pliku databaseName**wprowadź nazwę bazy danych SQL.
4.  Te zmienne są specyficzne dla przykładowych środowisk, różne środowiska mogą mieć różne zmienne.

## <a name="create-an-environment"></a>Tworzenie środowiska
Następnym etapem wdrożenia jest utworzenie środowiska, które ma być używane do celów programisty lub testowania.

1. W definicji wersji wybierz pozycję **Dodaj zadania**.
2. Na karcie **Zadania** dodaj zadanie tworzenia środowiska przez laboratoria DevTest Azure. Skonfiguruj zadanie w następujący sposób:
    1. W przypadku **subskrypcji usługi Azure RM**wybierz połączenie na liście **Dostępne połączenia usługi Azure** lub utwórz bardziej ograniczone uprawnienia do subskrypcji platformy Azure. Aby uzyskać więcej informacji, zobacz [punkt końcowy usługi Azure Resource Manager](/azure/devops/pipelines/library/service-endpoints).
2. W obszarze **Nazwa laboratorium**wybierz nazwę wystąpienia utworzonego wcześniej*.
3. W przypadku **nazwy repozytorium**wybierz repozytorium, do którego został wypchnięty szablon Menedżera zasobów (201).
4. W obszarze **Nazwa szablonu**wybierz nazwę środowiska zapisanego w repozytorium kodu źródłowego*. 
5. **Nazwa laboratorium,** **nazwa repozytorium**i **nazwa szablonu** są przyjaznymi reprezentacjami identyfikatorów zasobów platformy Azure. Ręczne wprowadzenie przyjaznej nazwy spowoduje błędy, użyj list rozwijanych, aby wybrać informacje.
6. W przypadku **nazwy środowiska**wprowadź nazwę, aby jednoznacznie zidentyfikować wystąpienie środowiska w laboratorium.  Musi być unikatowy w laboratorium.
7. **Plik parametrów** i **parametry**umożliwiają przekazywanie parametrów niestandardowych do środowiska. Jeden lub oba mogą służyć do ustawiania wartości parametrów. W tym przykładzie zostanie użyta sekcja Parametry. Użyj nazw zmiennych zdefiniowanych w środowisku, na przykład:`-administratorLogin "$(administratorLogin)" -administratorLoginPassword "$(administratorLoginPassword)" -databaseName "$(databaseName)" -cacheSKUCapacity 1`
8. Informacje w szablonie środowiska mogą być przekazywane w sekcji danych wyjściowych szablonu. Zaznacz **pozycję Utwórz zmienne wyjściowe na podstawie danych wyjściowych szablonu środowiska,** aby inne zadania mogły używać danych. `$(Reference name.Output Name)`jest wzorem do naśladowania. Na przykład, jeśli nazwa odwołania była DTL i nazwa wyjściowa `$(DTL.location)`w szablonie była lokalizacja zmienną będzie .

## <a name="delete-the-environment"></a>Usuwanie środowiska
Ostatnim etapem jest usunięcie środowiska, które zostały wdrożone w wystąpieniu usługi Azure DevTest Labs. Zwykle można usunąć środowisko po wykonaniu zadań deweloperskich lub uruchomić testy, które są potrzebne w wdrożonych zasobach.

W definicji wersji wybierz pozycję **Dodaj zadania**, a następnie na karcie **Wdrażanie** dodaj zadanie **usuwania środowiska w laboratoriach devtest** azure. Skonfiguruj go w następujący sposób:

1. Aby usunąć maszynę wirtualną, zobacz [Zadania laboratoriów devtest platformy Azure:](https://marketplace.visualstudio.com/items?itemName=ms-azuredevtestlabs.tasks)
    1. W przypadku **subskrypcji usługi Azure RM**wybierz połączenie na liście **Dostępne połączenia usługi Azure** lub utwórz bardziej ograniczone uprawnienia do subskrypcji platformy Azure. Aby uzyskać więcej informacji, zobacz [punkt końcowy usługi Azure Resource Manager](/azure/devops/pipelines/library/service-endpoints).
    2. W przypadku **nazwy laboratorium**wybierz laboratorium, w którym istnieje środowisko.
    3. W przypadku **nazwy środowiska**wprowadź nazwę środowiska, które ma zostać usunięte.
2. Wprowadź nazwę definicji wersji, a następnie zapisz ją.

## <a name="next-steps"></a>Następne kroki
Zobacz następujące artykuły: 
- [Tworzenie środowisk wielu maszyn wirtualnych za pomocą szablonów Menedżera zasobów](devtest-lab-create-environment-from-arm.md).
- Szablony Menedżera zasobów szybki start dla automatyzacji DevTest Labs z [repozytorium DevTest Labs GitHub](https://github.com/Azure/azure-quickstart-templates).
- [Strona rozwiązywania problemów z programem VSTS](/azure/devops/pipelines/troubleshooting)

