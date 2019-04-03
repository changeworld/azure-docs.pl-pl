---
title: Integrowanie środowiska Azure potoki w usłudze Azure DevTest Labs | Dokumentacja firmy Microsoft
description: Dowiedz się, jak zintegrować środowiska usługi Azure DevTest Labs w sieci Web usługi Azure DevOps ciągłej integracji (CI) i potoków ciągłego dostarczania (polecenie CD).
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/02/2019
ms.author: spelluru
ms.openlocfilehash: 24391e9e8541f12c434ade837b8f4944711ae375
ms.sourcegitcommit: a60a55278f645f5d6cda95bcf9895441ade04629
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/03/2019
ms.locfileid: "58887236"
---
# <a name="integrate-environments-into-your-azure-devops-cicd-pipelines"></a>Integrowanie środowiska potoków usługi Azure DevOps ciągłej integracji/ciągłego wdrażania
Można użyć rozszerzenia usługi Azure DevTest Labs zadania, które jest zainstalowany w usługom DevOps platformy Azure (dawniej Visual Studio Team Services), aby w łatwy sposób zintegrować swoje ciągłej integracji (CI) / ciągłe dostarczanie (CD) kompilacji i wydania potoku za pomocą platformy Azure Usługa DevTest Labs. Rozszerzenia te ułatwiają szybkie wdrożenie [środowiska](devtest-lab-test-env.md) dla określonego testu zadania i usunąć go po zakończeniu testu. 

W tym artykule pokazano, jak utworzyć i wdrożyć środowiska, a następnie usunąć środowisko, wszystko w jednym kompletny potok. Zazwyczaj przeprowadza każdego z tych zadań indywidualnie w własnej niestandardowej kompilacji, testowania i wdrażania potoku. Rozszerzenia używane w tym artykule w niniejszym dokumencie stanowią one [Tworzenie/usuwanie zadań maszyn wirtualnych DTL](devtest-lab-integrate-ci-cd-vsts.md):

- Tworzenie środowiska
- Usuwanie środowiska

## <a name="before-you-begin"></a>Przed rozpoczęciem
Przed potokiem ciągłej integracji/ciągłego Dostarczania można zintegrować z usługą Azure DevTest Labs, zainstaluj [Azure DevTest Labs zadania](https://marketplace.visualstudio.com/items?itemName=ms-azuredevtestlabs.tasks) rozszerzeń w Visual Studio Marketplace. 

## <a name="create-and-configure-the-lab-for-environments"></a>Tworzenie i Konfigurowanie laboratorium dla środowisk
W tej sekcji opisano, jak utworzyć i skonfigurować laboratorium wdrożonym środowiska platformy Azure do.

1. [Tworzenie laboratorium](devtest-lab-create-lab.md) Jeśli nie masz jeszcze jeden. 
2. Konfigurowanie laboratorium i utworzyć szablon środowiska, wykonując instrukcje przedstawione w tym artykule: [Tworzenie środowisk z wieloma Maszynami wirtualnymi i zasobów PaaS za pomocą szablonów usługi Azure Resource Manager](devtest-lab-create-environment-from-arm.md).
3. W tym przykładzie użyj istniejącego szablonu szybkiego startu platformy Azure [ https://azure.microsoft.com/resources/templates/201-web-app-redis-cache-sql-database/ ](https://azure.microsoft.com/resources/templates/201-web-app-redis-cache-sql-database/).
4. Kopiuj **201-web-app-redis-cache-sql-database** do folderu **ArmTemplate** folderu w repozytorium skonfigurowane w kroku 2.

## <a name="create-a-release-definition"></a>Tworzenie definicji wydania
Aby utworzyć definicję wydania, należy wykonać następujące czynności:

1.  Na **wersji** karcie **Centrum kompilacja i wydanie**, wybierz opcję **znak plus (+)** przycisku.
2.  W **Tworzenie definicji wydania** wybierz **pusty** szablonu, a następnie wybierz **dalej**.
3.  Wybierz **Wybierz później**, a następnie wybierz pozycję **Utwórz** do utworzenia nowych definicji wydania z jednym domyślnym środowisku i nie połączonych artefaktów.
4.  Aby otworzyć menu skrótów w nowych definicji wydania, należy wybrać **wielokropek (...)**  dalej, aby nazwa środowiska, a następnie wybierz **skonfigurować zmienne**.
5.  W **Konfiguruj - środowiska** okno zmiennych, których używasz w zadaniach definicji wydania, wprowadź następujące wartości:
1.  Aby uzyskać **administratorLogin**, wprowadź nazwę logowania administratora SQL.
2.  Aby uzyskać **administratorLoginPassword**, wprowadź hasło do użycia przez identyfikator logowania administratora SQL. Użyj ikony "kłódki", aby ukryć i bezpieczeństwo hasła.
3.  Aby uzyskać **databaseName**, wprowadź nazwę bazy danych SQL.
4.  Te zmienne są specyficzne dla środowiska, w przykładzie, różnych środowisk może mieć różne zmienne.

## <a name="create-an-environment"></a>Tworzenie środowiska
Następny etap wdrożenia to aby utworzyć środowisko, które ma być używany dla programowania i testowania.

1. W definicji wydania wybierz **dodawać zadania**.
2. Na **zadania** kartę, Dodaj zadanie programu tworzenie środowiska usługi Azure DevTest Labs. Skonfiguruj zadania w następujący sposób:
    1. Dla **subskrypcji Menedżera zasobów platformy Azure**, wybierając pozycję Podłączanie w **dostępne połączenia usługi Azure Service** listy lub utworzyć połączenie bardziej ograniczone uprawnienia do subskrypcji platformy Azure. Aby uzyskać więcej informacji, zobacz [punktu końcowego usługi Azure Resource Manager](/devops/pipelines/library/service-endpoints).
2. Aby uzyskać **Nazwa laboratorium**, wybierz nazwę wystąpienia, który został utworzony wcześniej *.
3. Aby uzyskać **nazwę repozytorium**, wybierz repozytorium, w której zostało wypchnięte do szablonu usługi Resource Manager (201) *.
4. Aby uzyskać **Nazwa szablonu**, wybierz nazwę środowisko, w którym został zapisany do Twojego źródła kodu repozytorium *. 
5. **Nazwa laboratorium**, **nazwę repozytorium**, i **Nazwa szablonu** są przyjazne reprezentacje identyfikatorów zasobów platformy Azure. Ręcznie wprowadź przyjazną nazwę powodować błędy, użyj listy rozwijanej, aby wybrać informacje.
6. Aby uzyskać **Nazwa środowiska**, wprowadź nazwę, aby jednoznacznie identyfikują wystąpienie środowiska w laboratorium.  Musi być unikatowa w ramach laboratorium.
7. **Plik parametrów** i **parametry**, Zezwalaj na niestandardowe parametry do przekazania do środowiska. Jeden lub oba może służyć do ustawiania wartości parametru. W tym przykładzie posłuży sekcji parametrów. Użyj nazwy zmiennych, które zostały zdefiniowane w środowisku, na przykład: `-administratorLogin “$(administratorLogin)” -administratorLoginPassword “$(administratorLoginPassword)” -databaseName “$(databaseName)” -cacheSKUCapacity 1`
8. Informacje w ramach szablonu środowiska mogą być przekazywane za pośrednictwem w sekcji danych wyjściowych szablonu. Sprawdź **zmienne wyjściowe Utwórz oparte na danych wyjściowych szablonu środowiska** dzięki innych zadań za pomocą danych. `$(Reference name.Output Name)` to wzorzec do wykonania. Na przykład, jeśli nazwa odwołania został DTL i Nazwa wyjściowego w szablonie został lokalizacji zmienna będzie `$(DTL.location)`.

## <a name="delete-the-environment"></a>Usuń środowisko
Końcowy etap polega na usunięciu środowiska, która została wdrożona w wystąpieniu usługi Azure DevTest Labs. Zazwyczaj będzie usunąć środowisko, po wykonywanie zadań deweloperskich lub uruchomić testy, które należy wdrożonych zasobów.

W definicji wydania wybierz **dodawać zadania**, a następnie na **Wdróż** kartę, należy dodać **usuwania środowiska Azure DevTest Labs** zadania. Skonfiguruj w następujący sposób:

1. Aby usunąć maszynę Wirtualną, zobacz [Azure DevTest Labs zadania](https://marketplace.visualstudio.com/items?itemName=ms-azuredevtestlabs.tasks):
    1. Dla **subskrypcji Menedżera zasobów platformy Azure**, wybierając pozycję Podłączanie w **dostępne połączenia usługi Azure Service** listy lub utworzyć połączenie bardziej ograniczone uprawnienia do subskrypcji platformy Azure. Aby uzyskać więcej informacji, zobacz [punktu końcowego usługi Azure Resource Manager](/devops/pipelines/library/service-endpoints).
    2. Aby uzyskać **Nazwa laboratorium**, wybierz laboratorium, w której istnieje środowisko.
    3. Aby uzyskać **Nazwa środowiska**, wprowadź nazwę środowiska, które ma zostać usunięty.
2. Wprowadź nazwę definicji wydania, a następnie zapisz go.

## <a name="next-steps"></a>Kolejne kroki
Zobacz następujące artykuły: 
- [Tworzenie środowisk z wieloma Maszynami wirtualnymi za pomocą szablonów usługi Resource Manager](devtest-lab-create-environment-from-arm.md).
- Szablony szybkiego startu Resource Manager dla usługi DevTest Labs automatyzacji z [repozytorium GitHub laboratoria DevTest](https://github.com/Azure/azure-quickstart-templates).
- [Rozwiązywanie problemów z usługi VSTS strony](/devops/pipelines/troubleshooting)

