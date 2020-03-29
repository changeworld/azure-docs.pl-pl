---
title: Rozszerzanie laboratoriów testów deweloperskich platformy Azure przy użyciu funkcji platformy Azure | Dokumenty firmy Microsoft
description: Dowiedz się, jak rozszerzyć usługi Azure DevTest Labs przy użyciu funkcji platformy Azure.
services: devtest-lab,lab-services
documentationcenter: na
author: spelluru
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/22/2019
ms.author: spelluru
ms.openlocfilehash: dd1fc4c1076d89c12b25837db9fa6a0ac3e1f3a5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "70014362"
---
# <a name="use-azure-functions-to-extend-devtest-labs"></a>Rozszerzanie usługi DevTest Labs za pomocą usługi Azure Functions
Usługi azure usługi do obsługi dodatkowych scenariuszy poza te, które są już obsługiwane przez DevTest Labs. Usługa Azure Functions może służyć do rozszerzania wbudowanej funkcji usługi, aby spełnić twoje potrzeby biznesowe. Poniższa lista zawiera niektóre z możliwych scenariuszy. W tym artykule pokazano, jak zaimplementować jeden z tych przykładowych scenariuszy.

- Dostarczanie podsumowania najwyższego poziomu maszyn wirtualnych (maszyn wirtualnych) w laboratorium
- [Konfigurowanie laboratorium w celu użycia bramy pulpitu zdalnego](configure-lab-remote-desktop-gateway.md)
- Raportowanie zgodności na wewnętrznej stronie pomocy technicznej
- Umożliwianie użytkownikom wykonywania operacji wymagających zwiększonych uprawnień w ramach subskrypcji
- [Uruchamianie przepływów pracy na podstawie zdarzeń DevTest Labs](https://github.com/RogerBestMsft/DTL-SecureArtifactData)

## <a name="overview"></a>Omówienie
[Usługa Azure Functions](../azure-functions/functions-overview.md) to platforma obliczeniowa bezserwerowa na platformie Azure. Korzystanie z usługi Azure Functions w rozwiązaniu z DevTest Labs umożliwia nam rozszerzenie istniejących funkcji za pomocą własnego kodu niestandardowego. Aby uzyskać więcej informacji na temat usług Azure Functions, zobacz [dokumentację usługi Azure Functions](../azure-functions/functions-overview.md). Aby zilustrować, jak usługa Azure Functions może pomóc spełnić wymagania lub pełne scenariusze w DevTest Labs, w tym artykule użyto przykładu dostarczania podsumowania najwyższego poziomu maszyn wirtualnych w laboratorium w następujący sposób:

**Przykładowe wymaganie/scenariusz:** Użytkownicy mogą zobaczyć szczegółowe informacje o wszystkich maszynach wirtualnych w laboratorium, w tym system operacyjny, właściciel i wszelkie zastosowane artefakty.  Ponadto jeśli artefakt **Zastosuj aktualizacje systemu Windows** nie został niedawno zastosowany, istnieje prosty sposób jego zastosowania.

Aby ukończyć scenariusz, należy użyć dwóch funkcji, jak opisano na poniższym diagramie:  

![Całkowity przepływ](./media/extend-devtest-labs-azure-functions/flow.png)

Kod źródłowy dla tych przykładowych funkcji znajduje się w [repozytorium DevTest Labs GitHub](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/AzureFunctions) (dostępne są implementacje języka C# i PowerShell).

- **UpdateInternalSupportPage:** Ta funkcja wysyła zapytanie DevTest Labs i aktualizuje wewnętrzną stronę pomocy technicznej bezpośrednio ze szczegółami dotyczącymi maszyn wirtualnych.
- **ApplyWindowsUpdateArtifact:** Dla maszyny Wirtualnej w laboratorium ta funkcja stosuje artefakt **aktualizacji systemu Windows.**

## <a name="how-it-works"></a>Jak to działa
Gdy użytkownicy wybierają stronę **pomocy technicznej wewnętrznej** w DevTest Labs, mają wstępnie wypełnioną stronę z informacjami o maszynach wirtualnych, właścicielach laboratoriów i kontaktach pomocy technicznej.  

Po wybraniu przycisku **Wybierz tutaj, aby odświeżyć,** strona wywołuje pierwszą funkcję platformy Azure: **UpdateInternalSupportPage**. Funkcja wysyła zapytanie DevTest Labs o informacje, a następnie przepisuje stronę **pomocy technicznej wewnętrznej** z nowymi informacjami.

Istnieje dodatkowa akcja, która może zostać podjęta, dla wszystkich maszyn wirtualnych, na których artefakty usługi Windows Update nie zostały ostatnio zastosowane, będzie przycisk, aby zastosować aktualizacje systemu Windows do maszyny Wirtualnej. Po wybraniu przycisku ***Uruchom aktualizację systemu Windows** dla maszyny Wirtualnej, strona wywołuje drugą funkcję platformy Azure: **ApplyWindowsUpdateArtifact**. Ta funkcja sprawdza, czy maszyna wirtualna jest uruchomiona, a jeśli tak, bezpośrednio stosuje artefakt [usługi Windows Update.](https://github.com/Azure/azure-devtestlab/tree/master/Artifacts/windows-install-windows-updates)

## <a name="step-by-step-walkthrough"></a>Przewodnik krok po kroku
Ta sekcja zawiera instrukcje krok po kroku dotyczące konfigurowania zasobów platformy Azure potrzebnych do zaktualizowania strony **pomocy technicznej wewnętrznej.** Ten przewodnik zawiera jeden przykład rozszerzenia DevTest Labs. Tego wzorca można użyć w innych scenariuszach.

### <a name="step-1-create-a-service-principal"></a>Krok 1: Tworzenie jednostki usługi 
Pierwszym krokiem jest uzyskanie jednostki usługi z uprawnieniami do subskrypcji, która zawiera laboratorium. Podmiot usługi musi używać uwierzytelniania opartego na hasłach. Można to zrobić za pomocą [narzędzia Azure CLI,](/cli/azure/create-an-azure-service-principal-azure-cli?view=azure-cli-latest) [azure powershell](/powershell/azure/create-azure-service-principal-azureps?view=azps-2.5.0)lub [witryny Azure portal.](../active-directory/develop/howto-create-service-principal-portal.md) Jeśli masz już jednostkę usługi do użycia, można pominąć ten krok.

Zanotuj **identyfikator aplikacji,** **klucz**i **identyfikator dzierżawy** dla jednostki usługi. Będą one potrzebne w dalszej części tego przewodnika. 

### <a name="step-2-download-the-sample-and-open-in-visual-studio-2019"></a>Krok 2: Pobierz próbkę i otwórz w programie Visual Studio 2019
Pobierz kopię [przykładu usługi Azure Functions języka C#](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/AzureFunctions/CSharp) lokalnie (klonując repozytorium lub pobierając repozytorium z [tego miejsca).](https://github.com/Azure/azure-devtestlab/archive/master.zip)  

1. Otwórz przykładowe rozwiązanie za pomocą programu Visual Studio 2019.  
1. Zainstaluj obciążenie **deweloperskie platformy Azure** dla programu Visual Studio, jeśli nie masz go już zainstalowanego. Można go zainstalować za pomocą pozycji menu **Narzędzia** -> **Pobierz narzędzia i funkcje).**

    ![Obciążenie programistyczne platformy Azure](./media/extend-devtest-labs-azure-functions/azure-development-workload-vs.png)
1. Skompiluj rozwiązanie. Wybierz **pozycję Menu Kompilacja,** a następnie polecenie **Buduj rozwiązanie.**

### <a name="step-3-deploy-the-sample-to-azure"></a>Krok 3: Wdrażanie próbki na platformie Azure
W programie Visual Studio w oknie **Eksplorator rozwiązań** kliknij prawym przyciskiem myszy projekt **AzureFunctions,** a następnie wybierz polecenie **Publikuj**. Postępuj zgodnie z kreatorem, aby zakończyć publikowanie w nowej lub istniejącej aplikacji funkcji platformy Azure. Aby uzyskać szczegółowe informacje na temat tworzenia i wdrażania funkcji platformy Azure przy użyciu programu Visual Studio, zobacz [Tworzenie funkcji platformy Azure przy użyciu programu Visual Studio.](../azure-functions/functions-develop-vs.md)

![Okno dialogowe Publikowania](./media/extend-devtest-labs-azure-functions/publish-dialog.png)


### <a name="step-4--gather-application-settings"></a>Krok 4: Zbieranie ustawień aplikacji
Po opublikowaniu funkcji należy uzyskać adresy URL dla tych funkcji z witryny Azure portal. 

1. Przejdź do [witryny Azure portal](https://portal.azure.com). 
1. Znajdź aplikację funkcji.
1. Na stronie **Aplikacje funkcyjne** wybierz tę funkcję. 
1. Wybierz **pozycję Pobierz adres URL funkcji,** jak pokazano na poniższej ilustracji. 

    ![Adresy URL funkcji platformy Azure](./media/extend-devtest-labs-azure-functions/function-url.png)
4. Skopiuj i zapisz adres URL. Powtórz te kroki dla innej funkcji platformy Azure. 

Potrzebne będą również dodatkowe informacje na temat jednostki usługi, takie jak identyfikator aplikacji, klucz i identyfikator dzierżawy.


### <a name="step-5--update-application-settings"></a>Krok 5: Aktualizacja ustawień aplikacji
W programie Visual Studio po opublikowaniu funkcji platformy Azure wybierz pozycję **Edytuj ustawienia usługi Azure App Service** w obszarze **Akcje**. Zaktualizuj następujące ustawienia aplikacji (zdalne):

- AzureFunctionUrl_ApplyUpdates
- AzureFunctionUrl_UpdateSupportPage
- WindowsUpdateAllowedDays (domyślnie 7)
- ServicePrincipal_AppId
- ServicePrincipal_Key
- ServicePrincipal_Tenant

    ![Ustawienia aplikacji](./media/extend-devtest-labs-azure-functions/application-settings.png)

### <a name="step-6-test-the-azure-function"></a>Krok 6: Testowanie funkcji platformy Azure
Ostatnim krokiem w tym instruktażu jest przetestowanie funkcji platformy Azure.  

1. Przejdź do **UpdateInternalSupportPage** funkcji w aplikacji funkcji utworzonej w kroku 3. 
1. Wybierz **pozycję Testuj** po prawej stronie strony. 
1. Wprowadź we właściwościach trasy (LABNAME, RESOURCEGROUPNAME i SUBSCRIPTIONID).
1. Wybierz **uruchom,** aby wykonać funkcję.  

    Ta funkcja zaktualizuje wewnętrzną stronę pomocy technicznej określonego laboratorium. Zawiera również przycisk dla użytkowników, aby bezpośrednio wywołać funkcję następnym razem

    ![Funkcja testowa](./media/extend-devtest-labs-azure-functions/test-function.png)

## <a name="next-steps"></a>Następne kroki
Usługa Azure Functions może pomóc rozszerzyć funkcjonalność DevTest Labs poza to, co jest już wbudowane i pomóc klientom spełnić ich unikatowe wymagania dla swoich zespołów. Ten wzór można rozszerzyć & rozszerzyć, aby jeszcze bardziej objąć.  Aby dowiedzieć się więcej o DevTest Labs, zobacz następujące artykuły: 

- [Architektura referencyjna laboratorium DevTest Labs](devtest-lab-reference-architecture.md)
- [Często zadawane pytania](devtest-lab-faq.md)
- [Skalowanie w górę Laboratoriów DevTest](devtest-lab-guidance-scale.md)
- [Automatyzacja laboratoriów devtest za pomocą programu PowerShell](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/Modules/Library/Tests)








