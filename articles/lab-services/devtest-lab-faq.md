---
title: Usługa Azure DevTest Labs — często zadawane pytania | Dokumentacja firmy Microsoft
description: Znajdź odpowiedzi na często zadawane pytania dotyczące usługi Azure DevTest Labs.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.assetid: afe83109-b89f-4f18-bddd-b8b4a30f11b4
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/21/2019
ms.author: spelluru
ms.openlocfilehash: d8fc929b21bedcb3e7e2bd3f5ed1d6c867bca3c8
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/18/2019
ms.locfileid: "58803378"
---
# <a name="azure-devtest-labs-faq"></a>Azure DevTest Labs — często zadawane pytania
Uzyskaj odpowiedzi na niektóre często zadawane pytania dotyczące usługi Azure DevTest Labs.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

**Ogólne**

## <a name="blog-post"></a>Wpis na blogu
Naszym blogu zespołu laboratoria DevTest został wycofany z 20 marca 2019 r. 

### <a name="where-can-i-track-feature-updates-going-forward"></a>Gdzie można śledzić aktualizacji funkcji w przyszłości?
Idąc dalej, firma Microsoft będzie można publikowanie aktualizacji dotyczących funkcji i/lub wpisów w blogu informacyjne na blogu platformy Azure i aktualizacje platformy Azure. Wpisy na blogu również połączy się z naszą dokumentację, wszędzie tam, gdzie wymagane.

Subskrybuj [DevTest Labs — Blog dotyczący platformy Azure](https://azure.microsoft.com/blog/tag/azure-devtest-labs/) i [aktualizacji usługi DevTest Labs Azure](https://azure.microsoft.com/updates/?product=devtest-lab) uzyskiwania informacji o nowych funkcjach w usłudze DevTest Labs.

### <a name="what-happens-to-the-existing-blog-posts"></a>Co się dzieje z istniejących wpisów w blogu?
Obecnie pracujemy nad Migrowanie istniejących wpisów w blogu (z wyjątkiem aktualizacji awarii) do naszych [dokumentacja usługi DevTest Labs](devtest-lab-overview.md). Gdy na blogu MSDN jest przestarzały, zostaną przekierowani do Przegląd dokumentacji dla usługi DevTest Labs. Gdy przekierowanie, możesz wyszukać artykuł, którego szukasz w tytule "Filtrowanie według". Należy pamiętać, firma Microsoft nie zostały jeszcze zmigrowane wszystkie wpisy, ale powinna być podejmowana z końcem tego miesiąca. 


### <a name="where-do-i-see-outage-updates"></a>Gdzie mogę zobaczyć aktualizacje awarii?
Firma Microsoft będzie publikowanie aktualizacji awarii za pomocą naszych uchwyt Twitter w przyszłości. Obserwuj nas w serwisie Twitter w celu uzyskania najnowszych aktualizacji na awarie i znanych błędów.

### <a name="twitter"></a>Twitter 
Nasze uchwyt Twitter: [@azlabservices](https://twitter.com/azlabservices)

## <a name="what-if-my-question-isnt-answered-here"></a>Co zrobić, jeśli moje pytanie nie ma tutaj odpowiedzi?
Jeśli Twoje pytanie nie ma na liście, dać nam znać, a pomożemy Ci znaleźć odpowiedzi.

* Zadaj je na końcu tego — często zadawane pytania. Skontaktuj się z zespołem usługi Azure Cache i inni członkowie społeczności, informacje o tym artykule.
* Aby uzyskać dostęp do większej liczby osób, należy zadać pytanie na [forum usługi Azure DevTest Labs MSDN](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureDevTestLabs). Skontaktuj się z zespołem usługi Azure DevTest Labs i inni członkowie społeczności.
* Dla żądania funkcji przesłać żądania i pomysłów dotyczących [Azure DevTest Labs User Voice](https://feedback.azure.com/forums/320373-azure-devtest-labs).

## <a name="why-should-i-use-azure-devtest-labs"></a>Dlaczego warto używać usługi Azure DevTest Labs?
Usługa Azure DevTest Labs można zapisać Twojego zespołu, czas i pieniądze. Deweloperzy mogą tworzyć własnych środowisk przy użyciu kilku różnych podstaw. Mogą również użyć artefaktów można szybko wdrożyć i skonfigurować aplikacje. Za pomocą niestandardowych obrazów i formuł, można zapisać maszyny wirtualne (VM) jako szablonów i łatwo odtworzyć je w zespół. DevTest Labs oferuje także kilka zasad można skonfigurować tego laboratorium, które Administratorzy mogą używać zmniejszeniu strat i zarządzanie środowiskami zespołu. Zasady te obejmują automatycznego zamykania, próg koszt maksymalna maszyn wirtualnych na użytkownika, a maksymalny rozmiar maszyny Wirtualnej. Aby uzyskać szczegółowe informacje o usłudze DevTest Labs, zobacz [Przegląd](devtest-lab-overview.md) lub [klip wideo z wprowadzeniem](https://channel9.msdn.com/Blogs/Azure/what-is-azure-devtest-labs).

## <a name="what-does-worry-free-self-service-mean"></a>Co oznacza "obaw samoobsługi"?
Samoobsługa bez zmartwień oznacza, że deweloperom i testerom tworzyć własne środowiska zgodnie z potrzebami. Administratorzy mają bezpieczeństwa, wiedząc, że usługa DevTest Labs może pomóc zminimalizować straty i kontrolować koszty. Administratorzy mogą określić rozmiary maszyn wirtualnych, które są dozwolone, maksymalna liczba maszyn wirtualnych, a podczas pracy maszyny wirtualne i zamknij. DevTest Labs ułatwia także monitorowanie kosztów i Ustawiaj alerty zwiększające wiedzieć, jak są używane zasoby laboratorium.

## <a name="how-can-i-use-devtest-labs"></a>Jak używać usługi DevTest Labs?
DevTest Labs jest przydatne w dowolnym momencie wymagają deweloperów lub środowisk testowych i chcesz odtworzyć je szybko ani zarządzać nimi za pomocą zasad oszczędności.

Poniżej przedstawiono kilka scenariuszy, w których nasi klienci będą korzystać usługi DevTest Labs dla:

* Zarządzanie deweloperów i środowisk testowych w jednym miejscu. Używanie zasad do zmniejszenia kosztów i tworzenie niestandardowych obrazów, aby udostępnić opiera się na zespół.
* Tworzenie aplikacji przy użyciu obrazów niestandardowych można zapisać stan dysku etapach tworzenia.
* Śledzenie kosztów względem postępu.
* Twórz środowiska testowe pamięci masowej dla testów zapewnienia jakości.
* Użyj artefaktów i formuły można łatwo konfigurować i odtworzenia aplikacji w różnych środowiskach.
* Rozpraszanie maszyn wirtualnych na potrzeby hackathonów (pracy zespołowej deweloperskie lub testowe), a następnie łatwo cofnąć inicjowanie ich obsługi po zakończeniu zdarzenia.

## <a name="how-am-i-billed-for-devtest-labs"></a>Jak są rozliczane DevTest Labs?
DevTest Labs to bezpłatna usługa. Tworzenie laboratoriów i konfigurowanie zasad, szablonów i artefaktów w usłudze DevTest Labs jest bezpłatna. Płacisz tylko za zasoby platformy Azure używane w laboratorium, takie jak maszyny wirtualne, konta magazynu i sieci wirtualnych. Aby uzyskać więcej informacji o koszcie zasoby laboratorium, zobacz [cennika usługi Azure DevTest Labs](https://azure.microsoft.com/pricing/details/devtest-lab/).


**Bezpieczeństwo**
## <a name="what-are-the-different-security-levels-in-devtest-labs"></a>Co to są poziomy zabezpieczeń w usłudze DevTest Labs?
Dostęp zabezpieczeń jest określany przez [kontroli dostępu opartej na rolach (RBAC)](../role-based-access-control/built-in-roles.md). Aby dowiedzieć się, jak działa dostęp, ułatwia naukę różnice między uprawnienia, roli i zakresu, zgodnie z definicją RBAC.

* **Uprawnienie**: Uprawnienie jest zdefiniowany dostępu do określonej akcji. Uprawnienia można na przykład dostęp do odczytu do wszystkich maszyn wirtualnych.
* **Rola**: Rola to zestaw uprawnień, które mogą być grupowane i przypisane do użytkownika. Na przykład użytkownik mający rolę właściciela subskrypcji ma dostęp do wszystkich zasobów w ramach subskrypcji.
* **Zakres**: Zakres jest poziom w hierarchii zasobu platformy Azure. Na przykład zakres może być grupę zasobów, jednym laboratorium lub całej subskrypcji.

W zakresie usługi DevTest Labs istnieją dwa typy ról, które definiują uprawnienia użytkownika:

* **Właściciel laboratorium**: Właściciel laboratorium ma dostęp do wszystkich zasobów w środowisku laboratoryjnym. Właściciel laboratorium można modyfikować zasady, odczytu i zapisu do maszyn wirtualnych, zmień sieć wirtualną i tak dalej.
* **Użytkownik laboratorium**: Użytkownik laboratorium można wyświetlić wszystkie zasoby laboratorium, takie jak maszyny wirtualne, zasad i sieciami wirtualnymi. Jednak użytkownik laboratorium nie można zmodyfikować zasady lub maszyn wirtualnych, które zostały utworzone przez innych użytkowników. 

Możesz również utworzyć niestandardowe role w usłudze DevTest Labs. Aby dowiedzieć się, jak utworzyć niestandardowe role w usłudze DevTest Labs, zobacz [udzielić użytkownikowi uprawnień do zasad określonych laboratorium](devtest-lab-grant-user-permissions-to-specific-lab-policies.md).

Ponieważ zakresy są hierarchiczne, jeśli użytkownik ma uprawnienia w określonym zakresie, użytkownik automatycznie otrzymuje te uprawnienia w każdym zakresie niższego poziomu w zakresie. Na przykład jeśli użytkownik przypisany do roli właściciela subskrypcji, użytkownik ma dostęp do wszystkich zasobów w ramach subskrypcji. Te zasoby obejmują wszystkie maszyny wirtualne, wszystkie sieci wirtualne i wszystkie labs. Właściciel subskrypcji automatycznie dziedziczy roli właściciel laboratorium. Odwrotny jest true. Właściciel laboratorium ma dostęp do laboratorium, która jest zakresem niższym niż poziom subskrypcji. W związku z tym nie zobaczą właściciel laboratorium, maszyny wirtualne, sieci wirtualne lub inne zasoby, które wykraczają poza laboratorium.

## <a name="how-do-i-create-a-role-to-allow-users-to-perform-a-specific-task"></a>Jak utworzyć rolę, aby umożliwić użytkownikom do wykonywania określonych zadań?
Aby uzyskać kompleksowy o tym, jak utworzyć niestandardowe role i przypisać uprawnienia do roli, zobacz [udzielić użytkownikowi uprawnień do zasad określonych laboratorium](devtest-lab-grant-user-permissions-to-specific-lab-policies.md). Poniżej przedstawiono przykładowy skrypt, który tworzy rolę *użytkownik usługi DevTest Labs zaawansowane*, który ma uprawnienia do uruchamiania i zatrzymania wszystkich maszyn wirtualnych w laboratorium:

    $policyRoleDef = Get-AzRoleDefinition "DevTest Labs User"
    $policyRoleDef.Actions.Remove('Microsoft.DevTestLab/Environments/*')
    $policyRoleDef.Id = $null
    $policyRoleDef.Name = "DevTest Labs Advanced User"
    $policyRoleDef.IsCustom = $true
    $policyRoleDef.AssignableScopes.Clear()
    $policyRoleDef.AssignableScopes.Add("subscriptions/<subscription Id>")
    $policyRoleDef.Actions.Add("Microsoft.DevTestLab/labs/virtualMachines/Start/action")
    $policyRoleDef.Actions.Add("Microsoft.DevTestLab/labs/virtualMachines/Stop/action")
    $policyRoleDef = New-AzRoleDefinition -Role $policyRoleDef  


**Automatyzacja i integracja ciągła Integracja/ciągłe dostarczanie**
## <a name="does-devtest-labs-integrate-with-my-cicd-toolchain"></a>Czy usługa DevTest Labs można zintegrować z Mój łańcuch narzędzi ciągłej integracji/ciągłego Dostarczania?
Jeśli używasz DevOps platformy Azure, możesz użyć [rozszerzenie usługi DevTest Labs zadania](https://marketplace.visualstudio.com/items?itemName=ms-azuredevtestlabs.tasks) zautomatyzować potok wydania w usłudze DevTest Labs. Oto niektóre z zadań, które można wykonać za pomocą tego rozszerzenia:

* Tworzenie i wdrażanie maszyny Wirtualnej automatycznie. Można także skonfigurować maszynę Wirtualną przy użyciu najnowszej kompilacji za pomocą zadania kopiowania plików na platformę Azure lub programu PowerShell usługi Azure DevOps Services.
* Automatyczne Przechwytywanie stan maszyny Wirtualnej po przetestowaniu do odtworzenia błędu w tej samej maszyny Wirtualnej w celu bliższego zbadania problemu.
* Gdy nie jest już potrzebny, należy usunąć maszynę Wirtualną na końcu potoku tworzenia wersji.

Wpisy na blogu następujące wskazówki oferty i informacje na temat przy użyciu rozszerzenia usługom DevOps platformy Azure:

* [Rozszerzenie DevOps platformy Azure i usłudze DevTest Labs](https://blogs.msdn.microsoft.com/devtestlab/2016/06/15/azure-devtest-labs-vsts-extension/)
* [Wdrożenie nowej maszyny Wirtualnej w istniejącej laboratorium DevTest Labs z usługom DevOps platformy Azure](https://www.visualstudiogeeks.com/blog/DevOps/Deploy-New-VM-To-Existing-AzureDevTestLab-From-VSTS)
* [Za pomocą usługi DevOps platformy Azure release management dla ciągłych wdrożeń użytkownik usługi DevTest Labs](https://www.visualstudiogeeks.com/blog/DevOps/Use-VSTS-ReleaseManagement-to-Deploy-and-Test-in-AzureDevTestLabs)

Dla innych ciągłej integracji (CI) / kompilatorach ciągłe dostarczanie (CD) możesz uzyskać te same scenariusze przez wdrażanie [szablonów usługi Azure Resource Manager](https://aka.ms/dtlquickstarttemplate) przy użyciu [poleceń cmdlet programu Azure PowerShell](../azure-resource-manager/resource-group-template-deploy.md) i [Zestawy SDK platformy .NET](https://www.nuget.org/packages/Microsoft.Azure.Management.DevTestLabs/). Możesz również użyć [interfejsy API REST dla usługi DevTest Labs](https://aka.ms/dtlrestapis) do integracji z łańcucha narzędzi.  


**Maszyny wirtualne**
## <a name="why-cant-i-see-vms-on-the-virtual-machines-page-that-i-see-in-devtest-labs"></a>Dlaczego nie widzę maszyn wirtualnych na stronie maszyny wirtualne, które widzę w usłudze DevTest Labs?
Po utworzeniu maszyny Wirtualnej w usłudze DevTest Labs są podane uprawnienia dostępu do tej maszyny Wirtualnej. Możesz wyświetlić maszyny Wirtualnej na stronie labs i na **maszyn wirtualnych** strony. Użytkownicy przypisani do roli użytkownika laboratorium DevTest Labs widoczne wszystkie maszyny wirtualne, które zostały utworzone w laboratorium w laboratorium **wszystkie maszyny wirtualne** strony. Jednak użytkownicy, którzy mają roli laboratorium DevTest Labs, nie są automatycznie przyznawane dostęp do odczytu do zasobów maszyny Wirtualnej, które zostały utworzone przez innych użytkowników. W związku z tym, te maszyny wirtualne nie są wyświetlane na **maszyn wirtualnych** strony.

## <a name="what-is-the-difference-between-a-custom-image-and-a-formula"></a>Jaka jest różnica między niestandardowego obrazu i formuły?
Obraz niestandardowy jest wirtualny dysk twardy (VHD). Formuła jest obraz, który można skonfigurować z użyciem ustawień dodatkowych, a następnie zapisz i odtworzenia. Niestandardowy obraz może być korzystniejsze, jeśli chcesz szybko utworzyć kilka środowisk przy użyciu tego samego obrazu podstawowego, niezmienne. Formuła może być lepiej, jeśli chcesz odtworzyć konfiguracji maszyny wirtualnej za pomocą najnowsze elementy w ramach sieci wirtualnej lub podsieci lub jako Maszynę wirtualną o określonym rozmiarze. Aby uzyskać szczegółowe informacje, zobacz [porównanie niestandardowych obrazów i formuł w usłudze DevTest Labs](devtest-lab-comparing-vm-base-image-types.md).

## <a name="how-do-i-create-multiple-vms-from-the-same-template-at-once"></a>Jak utworzyć wiele maszyn wirtualnych z tego samego szablonu jednocześnie?
Masz dwie opcje jednocześnie tworzenia wielu maszyn wirtualnych z tego samego szablonu:
* Możesz użyć [rozszerzenie Azure DevOps zadania](https://marketplace.visualstudio.com/items?itemName=ms-azuredevtestlabs.tasks). 
* Możesz [Generowanie szablonu usługi Resource Manager](devtest-lab-add-vm.md#save-azure-resource-manager-template) podczas tworzenia maszyny Wirtualnej, a [wdrażanie szablonu usługi Resource Manager za pomocą programu Windows PowerShell](../azure-resource-manager/resource-group-template-deploy.md).

## <a name="how-do-i-move-my-existing-azure-vms-into-my-devtest-labs-lab"></a>Jak przenieść Mój istniejący maszyn wirtualnych platformy Azure do mojego laboratorium DevTest Labs?
Aby skopiować istniejących maszyn wirtualnych, użytkownik usługi DevTest Labs:

1. Skopiuj plik wirtualnego dysku twardego istniejącej maszyny wirtualnej przy użyciu skryptu programu PowerShell:
   * Menedżer zasobów: [CopyRmVHDFromVMToLab.ps1](https://github.com/Azure/azure-devtestlab/blob/master/Scripts/CopyRmVHDFromVMToLab.ps1)
   * Klasyczne: [CopyClassicVHDFromVMToLab.ps1](https://github.com/Azure/azure-devtestlab/blob/master/Scripts/CopyClassicVHDFromVMToLab.ps1)
2. [Tworzenie obrazu niestandardowego](devtest-lab-create-template.md) wewnątrz laboratorium DevTest Labs.
3. Tworzenie maszyny Wirtualnej w środowisku laboratoryjnym za pomocą obrazu niestandardowego.

## <a name="can-i-attach-multiple-disks-to-my-vms"></a>Można dołączyć kilka dysków maszyn wirtualnych?
Tak, można dołączyć kilka dysków do maszyn wirtualnych.  

## <a name="if-i-want-to-use-a-windows-os-image-for-my-testing-do-i-have-to-purchase-an-msdn-subscription"></a>Aby korzystać z obrazu systemu operacyjnego Windows na etapach testowania, czy muszę kupić subskrypcję MSDN?
Aby używać obrazów systemu operacyjnego klienta Windows (Windows 7 lub nowszy) dla rozwoju lub testowania na platformie Azure, wykonaj jedną z następujących czynności:

- [Kup subskrypcję MSDN](https://www.visualstudio.com/products/how-to-buy-vs).
- Jeśli masz umowę Enterprise Agreement, Utwórz subskrypcję platformy Azure za pomocą [przedsiębiorstwa: tworzenie i testowanie](https://azure.microsoft.com/offers/ms-azr-0148p).

Aby uzyskać więcej informacji na temat środki na korzystanie z platformy Azure dla każdej oferty MSDN, zobacz [Azure miesięczne środki dla subskrybentów programu Visual Studio](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/).

## <a name="how-do-i-automate-the-process-of-uploading-vhd-files-to-create-custom-images"></a>Jak automatyzować proces przekazywania plików wirtualnego dysku twardego do tworzenia obrazów niestandardowych?
Aby zautomatyzować przekazywanie plików wirtualnego dysku twardego do tworzenia niestandardowych obrazów, masz dwie opcje:

* Użyj [AzCopy](../storage/common/storage-use-azcopy.md#upload-blobs-to-blob-storage) do kopiowania lub przekazywania plików VHD do konta magazynu, która jest skojarzona z laboratorium.
* Użyj [Eksplorator usługi Azure Storage](../vs-azure-tools-storage-manage-with-storage-explorer.md). Eksplorator usługi Storage jest aplikacją autonomiczną, z systemem Windows, OS X i Linux.   

Aby znaleźć docelowe konto magazynu, która jest skojarzona z laboratorium:

1. Zaloguj się w witrynie [Azure Portal](https://go.microsoft.com/fwlink/p/?LinkID=525040).
2. W menu po lewej stronie wybierz **grup zasobów**.
3. Znajdź i wybierz grupę zasobów, która jest skojarzona z laboratorium.
4. W obszarze **Przegląd**, wybierz jedno z kont magazynu.
5. Wybierz pozycję **Obiekty blob**.
6. Wygląd na potrzeby przekazywania na liście. Jeśli żaden nie istnieje, wróć do kroku 4 i spróbuj użyć innego konta magazynu.
7. Użyj **adresu URL** jako miejsce docelowe w poleceniu narzędzia AzCopy.

## <a name="how-do-i-automate-the-process-of-deleting-all-the-vms-in-my-lab"></a>Jak zautomatyzować proces usuwania wszystkich maszyn wirtualnych w mojej laboratorium?
Działanie w środowisku laboratoryjnym w witrynie Azure portal, można usunąć maszyny wirtualne. Możesz również usunąć wszystkie maszyny wirtualne w środowisku laboratoryjnym przy użyciu skryptu programu PowerShell. W poniższym przykładzie w obszarze **wartości w celu zmiany** komentarz, zmodyfikuj wartości parametrów. Możesz pobrać `subscriptionId`, `labResourceGroup`, i `labName` wartości z poziomu okienka laboratorium w witrynie Azure portal.

    # Delete all the VMs in a lab.

    # Values to change:
    $subscriptionId = "<Enter Azure subscription ID here>"
    $labResourceGroup = "<Enter lab's resource group here>"
    $labName = "<Enter lab name here>"

    # Sign in to your Azure account.
    Connect-AzAccount

    # Select the Azure subscription that has the lab. This step is optional
    # if you have only one subscription.
    Select-AzSubscription -SubscriptionId $subscriptionId

    # Get the lab that has the VMs that you want to delete.
    $lab = Get-AzResource -ResourceId ('subscriptions/' + $subscriptionId + '/resourceGroups/' + $labResourceGroup + '/providers/Microsoft.DevTestLab/labs/' + $labName)

    # Get the VMs from that lab.
    $labVMs = Get-AzResource | Where-Object {
              $_.ResourceType -eq 'microsoft.devtestlab/labs/virtualmachines' -and
              $_.Name -like "$($lab.Name)/*"}

    # Delete the VMs.
    foreach($labVM in $labVMs)
    {
        Remove-AzResource -ResourceId $labVM.ResourceId -Force
    }

**Artefakty**
## <a name="what-are-artifacts"></a>Co to są artefaktów?
Artefakty są możliwe do dostosowania elementy, które można użyć, aby wdrożyć swoje najnowsze elementy lub wdrożyć narzędzia programistyczne na maszynie Wirtualnej. Podczas tworzenia maszyny Wirtualnej, dołączyć artefaktów z maszyną wirtualną. Po aprowizacji maszyny Wirtualnej artefakty wdrażanie i konfigurowanie maszyny Wirtualnej. Różne istniejące artefaktów dostępnych w naszej [publicznego repozytorium GitHub](https://github.com/Azure/azure-devtestlab/tree/master/Artifacts). Możesz również [tworzyć własne artefaktów](devtest-lab-artifact-author.md).


**Konfiguracja laboratorium**
## <a name="how-do-i-create-a-lab-from-a-resource-manager-template"></a>Jak utworzyć laboratorium za pomocą szablonu usługi Resource Manager?
Firma Microsoft oferuje [repozytorium GitHub z szablonów usługi Azure Resource Manager w laboratorium](https://aka.ms/dtlquickstarttemplate) , którą można wdrożyć jako — lub zmodyfikować do tworzenia niestandardowych szablonów laboratorium. Każdy szablon zawiera link do wdrożenia laboratorium jako — w ramach własnej subskrypcji platformy Azure. Alternatywnie można dostosować szablon i [wdrażanie przy użyciu programu PowerShell lub wiersza polecenia platformy Azure](../azure-resource-manager/resource-group-template-deploy.md).

## <a name="why-are-my-vms-created-in-different-resource-groups-with-arbitrary-names-can-i-rename-or-modify-these-resource-groups"></a>Dlaczego moje maszyny wirtualne są tworzone w różnych grupach zasobów, o dowolnych nazwach? Czy mogę zmienić nazwę lub zmodyfikować te grupy zasobów?
Grupy zasobów są tworzone w ten sposób, tak aby DevTest Labs można zarządzać uprawnieniami użytkowników i dostępu do maszyn wirtualnych. Przenoszenie maszyny Wirtualnej do innej grupy zasobów i nazwę, która ma używać, zaleca się, nie wprowadzaj zmian do grup zasobów. Pracujemy nad ulepszaniem tego środowiska, aby umożliwić większą elastyczność.   

## <a name="how-many-labs-can-i-create-under-the-same-subscription"></a>Ile labs można tworzyć w ramach tej samej subskrypcji?
Nie ma określony limit liczby laboratoria, które mogą być tworzone na subskrypcję. Jednak ilość zasobów używanych w ramach jednej subskrypcji jest ograniczona. Informacje o [limity przydziału dla subskrypcji platformy Azure i](../azure-subscription-service-limits.md) i [jak zwiększyć te limity](https://azure.microsoft.com/blog/azure-limits-quotas-increase-requests).

## <a name="how-many-vms-can-i-create-per-lab"></a>Ile maszyn wirtualnych można utworzyć na laboratorium?
Nie ma żadnych określonych limitu liczby maszyn wirtualnych, które mogą być tworzone na laboratorium. Zasobów (rdzeni maszyn wirtualnych, publiczne adresy IP itd.), które są używane, są jednak ograniczone na subskrypcję. Informacje o [limity przydziału dla subskrypcji platformy Azure i](../azure-subscription-service-limits.md) i [jak zwiększyć te limity](https://azure.microsoft.com/blog/azure-limits-quotas-increase-requests).

## <a name="how-do-i-share-a-direct-link-to-my-lab"></a>Jak udostępnić bezpośredni link do mojego laboratorium?

1. W witrynie Azure portal przejdź do laboratorium.
2. Skopiuj adres URL laboratorium z przeglądarki, a następnie udostępniać je innym użytkownikom laboratorium.

> [!NOTE]
> Jeśli użytkownik laboratorium jest użytkownikowi zewnętrznemu, który ma [konta Microsoft](#what-is-a-microsoft-account), ale który nie jest członkiem wystąpienia usługi Active Directory w Twojej organizacji, użytkownik może być wyświetlony komunikat o błędzie podczas próby uzyskania dostępu link udostępniony do. Jeśli użytkownik zewnętrzny zobaczy następujący komunikat o błędzie, należy poprosić użytkownika o najpierw wybierz jego nazwę w prawym górnym rogu witryny Azure portal. Następnie w **katalogu** części menu, a użytkownik może wybierz katalog, w której istnieje laboratorium.
>
>

## <a name="what-is-a-microsoft-account"></a>Co to jest konto Microsoft?
Konto Microsoft jest konto, którego używasz dla prawie wszystko, co zrobić z firmy Microsoft, urządzeń i usług. Jest adres e-mail i hasło, którego używasz do logowania do usługi Skype, Outlook.com, OneDrive, Windows phone i Xbox Live. Jedno konto oznacza, że pliki, zdjęcia, kontaktów i ustawienia wykonać użytkownik na dowolnym urządzeniu.

> [!NOTE]
> Konto Microsoft użyte do wywołania *identyfikatora Windows Live ID*.
>
>


**Rozwiązywanie problemów**
## <a name="my-artifact-failed-during-vm-creation-how-do-i-troubleshoot-it"></a>Moje artefaktu nie powiodło się podczas tworzenia maszyny Wirtualnej. Jak rozwiązywać je
Aby dowiedzieć się, jak pobrać dzienniki dla Twojego artefaktu nie powiodło się, zobacz [jak diagnozowanie błędów artefaktów w usłudze DevTest Labs](devtest-lab-troubleshoot-artifact-failure.md).

## <a name="why-isnt-my-existing-virtual-network-saving-properly"></a>Dlaczego nie jest Mój istniejący wirtualny sieci zapisywanie prawidłowo?
Jedną z możliwości jest, że Twoja nazwa sieci wirtualnej zawiera kropek. Jeśli tak, spróbuj usuwanie okresy lub zastąpieniu łączników. Następnie spróbuj ponownie zapisać sieci wirtualnej.

## <a name="why-do-i-get-a-parent-resource-not-found-error-when-i-provision-a-vm-from-powershell"></a>Dlaczego otrzymuję błąd "Nie znaleziono zasobu nadrzędnego" podczas aprowizowania maszyny Wirtualnej za pomocą programu PowerShell?
Jeśli jeden zasób jest nadrzędny do innego zasobu, zasób nadrzędny musi istnieć przed przystąpieniem do tworzenia zasobów podrzędnych. Jeśli zasób nadrzędny nie istnieje, zostanie wyświetlony **ParentResourceNotFound** wiadomości. Jeśli nie określisz zależność od zasobu nadrzędnego, przed nadrzędnego może wdrożyć zasobów podrzędnych.

Maszyny wirtualne są zasobami podrzędnymi w ramach laboratorium w grupie zasobów. Korzystając z szablonów usługi Resource Manager do wdrażania maszyn wirtualnych przy użyciu programu PowerShell, nazwa grupy zasobów, które są udostępniane w skrypcie programu PowerShell powinna być nazwa grupy zasobów w środowisku laboratoryjnym. Aby uzyskać więcej informacji, zobacz [Rozwiązywanie typowych problemów z wdrożeniem platformy](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-common-deployment-errors).

## <a name="where-can-i-find-more-error-information-if-a-vm-deployment-fails"></a>Gdzie można znaleźć więcej informacji o błędzie w przypadku niepowodzenia wdrożenia maszyny Wirtualnej?
Błędy wdrożenia maszyny Wirtualnej są przechwytywane w dziennikach aktywności. Laboratorium można znaleźć w dziennikach aktywności maszyn wirtualnych w ramach **dzienniki inspekcji** lub **diagnostyki maszyny wirtualnej** w menu zasobów na stronie maszyny Wirtualnej w laboratorium (zostanie wyświetlona po wybraniu maszyny Wirtualnej z **Moje wirtualny maszyny** listy).

Czasami błąd wdrożenia występuje przed rozpoczęciem wdrażania maszyny Wirtualnej. Przykładem jest, gdy zostanie przekroczony limit subskrypcji dla zasobu, który został utworzony z maszyną Wirtualną. W tym przypadku szczegóły błędu są przechwytywane w dziennikach aktywności lab poziomie. Dzienniki aktywności znajdują się w dolnej części **konfiguracji i zasad** ustawienia. Więcej informacji na temat używania działania dzienników na platformie Azure, zobacz [wyświetlanie dzienników aktywności do inspekcji akcje na zasobach](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-audit).

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]
