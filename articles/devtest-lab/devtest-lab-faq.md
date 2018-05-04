---
title: Azure DevTest Labs — często zadawane pytania | Dokumentacja firmy Microsoft
description: Odpowiedzi na często zadawane pytania dotyczące usługi Azure DevTest Labs.
services: devtest-lab,virtual-machines
documentationcenter: na
author: craigcaseyMSFT
manager: douge
editor: ''
ms.assetid: afe83109-b89f-4f18-bddd-b8b4a30f11b4
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/22/2017
ms.author: v-craic
ms.openlocfilehash: 7a65ba691b9cf8b0ce989f0c54bd0b74edd43b85
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2018
---
# <a name="azure-devtest-labs-faq"></a>Azure DevTest Labs — często zadawane pytania
Odpowiedzi na niektóre często zadawane pytania dotyczące usługi Azure DevTest Labs.

**Ogólne**
## <a name="what-if-my-question-isnt-answered-here"></a>Co zrobić, jeśli mojego pytania nie ma odpowiedzi w tym miejscu?
Jeśli Twoje pytanie nie ma na liście, poinformować nas, dlatego firma Microsoft pomoże Ci znaleźć odpowiedź.

* Zadaj pytanie na końcu tych często zadawanych PYTAŃ. Skontaktuj się z team pamięć podręczna Azure i innymi członkami społeczności informacje w tym artykule.
* Aby uzyskać dostęp do większej liczby osób, Zadaj pytanie na [forum Azure DevTest Labs w witrynie MSDN](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureDevTestLabs). Skontaktuj się z team Azure DevTest Labs i innych członków społeczności.
* Dla żądania funkcji przesłać żądania i pomysłami, które [Azure DevTest Labs User Voice](https://feedback.azure.com/forums/320373-azure-devtest-labs).

## <a name="why-should-i-use-azure-devtest-labs"></a>Dlaczego warto używać usługi Azure DevTest Labs?
Azure DevTest Labs można zapisać Twojego zespołu czas i pieniądze. Deweloperzy mogą tworzyć własne środowisk przy użyciu kilku różnych typów podstawowych. One również używać artefaktów można szybko wdrożyć i skonfigurować aplikacji. Przy użyciu niestandardowych obrazów i formuły, Zapisz jako szablon maszynach wirtualnych (VM) i łatwo odtworzyć między elementami zespołu. DevTest Labs oferuje również kilka zasad można skonfigurować tego laboratorium, które Administratorzy mogą używać, aby zmniejszyć odpady i zarządzać środowiskami zespołu. Zasady te obejmują automatyczne zamykanie, próg koszt maksymalną maszyn wirtualnych na użytkownika, a maksymalny rozmiar maszyny Wirtualnej. Aby uzyskać więcej informacji na temat szczegółowy opis DevTest Labs, zobacz [omówienie](devtest-lab-overview.md) lub [wprowadzenie wideo](https://channel9.msdn.com/Blogs/Azure/what-is-azure-devtest-labs).

## <a name="what-does-worry-free-self-service-mean"></a>Co to znaczy "obaw samoobsługi"?
Obaw samoobsługi oznacza, że deweloperów i testerów utworzyć własne środowisk zgodnie z potrzebami. Administratorzy mają bezpieczeństwo i pewność, że DevTest Labs można zminimalizować odpady i kontrolę kosztów. Administratorzy mogą określić rozmiarów maszyn wirtualnych, które są dozwolone, maksymalna liczba maszyn wirtualnych, a podczas pracy i zamknąć maszyn wirtualnych. DevTest Labs również ułatwia monitorowanie kosztów i Ustaw alerty, zwiększające pamiętać o jak są używane zasoby laboratorium.

## <a name="how-can-i-use-devtest-labs"></a>Jak używać DevTest Labs?
DevTest Labs jest przydatne w dowolnym momencie wymagane deweloperów lub test środowisk, a chcesz odtworzyć je szybko i zarządzać nimi za pomocą zasad oszczędności.

Poniżej przedstawiono kilka scenariuszy, które naszym klientom używają DevTest Labs dla:

* Zarządzanie deweloperów i przetestuj środowisk w jednym miejscu. Zasady Użyj zmniejszyć koszty i tworzenia niestandardowych obrazów, aby udostępnić kompilacje dla zespołu.
* Opracowywanie aplikacji przy użyciu niestandardowych obrazów można zapisać stan dysku etapach rozwoju.
* Śledzenie kosztów w toku.
* Tworzenie środowisk testowych masowego do testowania zapewnienia jakości.
* Użyj artefakty i formuły, aby łatwo skonfigurować i odtworzyć aplikacji w różnych środowiskach.
* Dystrybucji maszyn wirtualnych dla hackathons (programistycznych lub test współpracy), a następnie łatwo anulowanie zastrzeżenia je po zakończeniu zdarzenia.

## <a name="how-am-i-billed-for-devtest-labs"></a>Jak m opłaty naliczane za DevTest Labs?
DevTest Labs jest bezpłatnej usługi. Tworzenie labs i konfigurowania zasad, szablonów i artefaktów w usłudze DevTest Labs jest bezpłatna. Płaci się tylko dla zasobów platformy Azure, używane w laboratorium, na przykład maszyny wirtualne, konta magazynu i sieci wirtualnych. Aby uzyskać więcej informacji o koszt zasobów laboratorium, zobacz [cennik usługi Azure DevTest Labs](https://azure.microsoft.com/pricing/details/devtest-lab/).


**Bezpieczeństwo**
## <a name="what-are-the-different-security-levels-in-devtest-labs"></a>Co to są różnych poziomów ochrony w usłudze DevTest Labs?
Dostęp zabezpieczeń jest określany przez [kontroli dostępu opartej na rolach (RBAC)](../role-based-access-control/built-in-roles.md). Aby dowiedzieć się, jak działa dostęp, ułatwia naukę różnice między uprawnienia roli i zakresu, zgodnie z definicją RBAC.

* **Uprawnienie**: uprawnienie jest zdefiniowane dostępu do określonej akcji. Uprawnienia można na przykład dostęp do odczytu do wszystkich maszyn wirtualnych.
* **Rola**: Rola to zestaw uprawnień, które mogą być grupowane i przypisana do użytkownika. Na przykład użytkownik mający rolę właściciela subskrypcji ma dostęp do wszystkich zasobów w ramach subskrypcji.
* **Zakres**: zakres jest poziom w hierarchii zasobów platformy Azure. Na przykład zakres może być grupę zasobów, pojedynczy laboratorium lub całej subskrypcji.

W zakresie DevTest Labs istnieją dwa rodzaje ról, które określają uprawnienia użytkownika:

* **Właściciela laboratorium**: właściciela laboratorium ma dostęp do wszystkich zasobów w środowisku laboratoryjnym. Właściciela laboratorium można modyfikować zasady, odczytu i zapisu do żadnej maszyny wirtualnej, zmień sieci wirtualnej itd.
* **Użytkownik laboratorium**: użytkownik laboratorium można wyświetlić wszystkie zasoby laboratorium, takich jak maszyny wirtualne, zasady i sieci wirtualnych. Jednak użytkownik laboratorium nie można zmodyfikować zasady lub wszystkie maszyny wirtualne, które zostały utworzone przez innych użytkowników. 

Można również tworzyć role niestandardowe w usłudze DevTest Labs. Aby dowiedzieć się, jak tworzyć role niestandardowe w usłudze DevTest Labs, zobacz [udzielić użytkownikowi uprawnień do laboratorium określonych zasad](devtest-lab-grant-user-permissions-to-specific-lab-policies.md).

Ponieważ zakresy są hierarchiczne, jeśli użytkownik ma uprawnienia w zakresie niektórych, użytkownik automatycznie otrzymuje tych uprawnień w zakresie niższego poziomu, co w zakresie. Na przykład jeśli użytkownik przypisany do roli właściciela subskrypcji, użytkownik ma dostęp do wszystkich zasobów w ramach subskrypcji. Te zasoby obejmują wszystkie maszyny wirtualne, wszystkie sieci wirtualne i laboratoriów wszystkie. Właściciel subskrypcji automatycznie przejmuje rolę właściciela laboratorium. Jednak przeciwieństwem nie jest prawdziwe. Właściciela laboratorium ma dostęp do laboratorium jest zakresem niższym niż poziom subskrypcji. W związku z tym właściciela laboratorium nie widzi maszyn wirtualnych, sieci wirtualnych lub innych zasobów, które są poza laboratorium.

## <a name="how-do-i-create-a-role-to-allow-users-to-perform-a-specific-task"></a>Jak utworzyć rolę, aby umożliwić użytkownikom do wykonywania określonych zadań?
Kompleksowe artykułu o tym, jak tworzyć role niestandardowe i przypisać uprawnienia do roli, zobacz [udzielić użytkownikowi uprawnień do laboratorium określonych zasad](devtest-lab-grant-user-permissions-to-specific-lab-policies.md). Poniżej przedstawiono przykładowy skrypt, który tworzy rolę *DevTest Labs zaawansowane użytkownika*, który ma uprawnienia do uruchamiania i zatrzymywania wszystkich maszyn wirtualnych w laboratorium:

    $policyRoleDef = Get-AzureRmRoleDefinition "DevTest Labs User"
    $policyRoleDef.Actions.Remove('Microsoft.DevTestLab/Environments/*')
    $policyRoleDef.Id = $null
    $policyRoleDef.Name = "DevTest Labs Advanced User"
    $policyRoleDef.IsCustom = $true
    $policyRoleDef.AssignableScopes.Clear()
    $policyRoleDef.AssignableScopes.Add("subscriptions/<subscription Id>")
    $policyRoleDef.Actions.Add("Microsoft.DevTestLab/labs/virtualMachines/Start/action")
    $policyRoleDef.Actions.Add("Microsoft.DevTestLab/labs/virtualMachines/Stop/action")
    $policyRoleDef = New-AzureRmRoleDefinition -Role $policyRoleDef  


**Automatyzacja i integracja CI/CD**
## <a name="does-devtest-labs-integrate-with-my-cicd-toolchain"></a>Czy DevTest Labs można zintegrować z łańcuchem narzędzi Moje CI/CD?
Jeśli używasz programu Visual Studio Team Services, możesz użyć [rozszerzenie DevTest Labs zadania](https://marketplace.visualstudio.com/items?itemName=ms-azuredevtestlabs.tasks) można zautomatyzować planowaną wersji w usłudze DevTest Labs. Oto niektóre z zadań, które można wykonywać z tym rozszerzeniem:

* Tworzenie i wdrażanie maszyny Wirtualnej automatycznie. Można także skonfigurować maszynę Wirtualną z ostatniej kompilacji za pomocą zadania kopiowania plików na platformę Azure lub programu PowerShell Team Services.
* Automatycznie przechwycony stan maszyny wirtualnej po zakończeniu testowania do odtworzenia usterki w tej samej maszyny Wirtualnej w celu dokładniejszego zbadania.
* Usuń maszynę Wirtualną na końcu potoku wersji nie jest już potrzebne.

Następującym wpisie ogłoszeń oferta wskazówki i informacje o przy użyciu rozszerzenia usługi Team Services:

* [DevTest Labs i rozszerzenia programu Visual Studio Team Services](https://blogs.msdn.microsoft.com/devtestlab/2016/06/15/azure-devtest-labs-vsts-extension/)
* [Wdrożenie nowej maszyny Wirtualnej w istniejących laboratorium DevTest Labs z usługi Team Services](http://www.visualstudiogeeks.com/blog/DevOps/Deploy-New-VM-To-Existing-AzureDevTestLab-From-VSTS)
* [Za pomocą zarządzania zleceniami Team Services do ciągłego wdrożeń na DevTest Labs](http://www.visualstudiogeeks.com/blog/DevOps/Use-VSTS-ReleaseManagement-to-Deploy-and-Test-in-AzureDevTestLabs)

Dla innych ciągłej integracji (CI) / toolchains ciągłego dostarczania (CD), można osiągnąć tych samych operacji przez wdrażanie [szablonów usługi Azure Resource Manager](https://aka.ms/dtlquickstarttemplate) za pomocą [poleceń cmdlet programu Azure PowerShell](../azure-resource-manager/resource-group-template-deploy.md) i [.NET SDK](https://www.nuget.org/packages/Microsoft.Azure.Management.DevTestLabs/). Można też użyć [interfejsów API REST dla DevTest Labs](http://aka.ms/dtlrestapis) do integracji z z łańcuchem narzędzi.  


**Maszyny wirtualne**
## <a name="why-cant-i-see-vms-on-the-virtual-machines-blade-that-i-see-in-devtest-labs"></a>Dlaczego nie widzę maszyn wirtualnych w bloku maszyny wirtualnej, wyświetlanego w usłudze DevTest Labs?
Po utworzeniu maszyny Wirtualnej w usłudze DevTest Labs są podane uprawnienia dostępu do tej maszyny Wirtualnej. Możesz wyświetlić wirtualna zarówno w bloku labs i na **maszyn wirtualnych** bloku. Użytkownicy przypisani do roli użytkownika laboratorium DevTest Labs można wyświetlić wszystkich maszyn wirtualnych, które zostały utworzone w laboratorium w laboratorium **wszystkich maszyn wirtualnych** bloku. Użytkownicy, którzy mają roli użytkownik laboratorium DevTest Labs nie są jednak automatycznie przyznawane dostęp do odczytu do zasobów maszyny Wirtualnej, które zostały utworzone przez innych użytkowników. W związku z tym te maszyny wirtualne nie są wyświetlane na **maszyn wirtualnych** bloku.

## <a name="what-is-the-difference-between-a-custom-image-and-a-formula"></a>Jaka jest różnica między niestandardowego obrazu oraz formułę?
Obraz niestandardowy jest wirtualny dysk twardy (VHD). Formuła jest obraz, który można skonfigurować z użyciem ustawień dodatkowych, a następnie zapisz i odtworzenia. Obraz niestandardowy może być pożądane, aby szybko utworzyć przy użyciu tego samego obrazu podstawowego, niezmienne kilku środowiskach. Formuła może być lepsze, jeśli chcesz odtworzyć konfiguracji maszyny Wirtualnej przy użyciu najnowszych bitów, jako część podsieci lub sieci wirtualnej lub maszyny Wirtualnej o określonym rozmiarze. Aby uzyskać szczegółowe informacje, zobacz [porównanie niestandardowych obrazów i formuły w usłudze DevTest Labs](devtest-lab-comparing-vm-base-image-types.md).

## <a name="how-do-i-create-multiple-vms-from-the-same-template-at-once"></a>Jak utworzyć wiele maszyn wirtualnych z tego samego szablonu jednocześnie?
Masz dwie opcje do jednoczesnego tworzenia wiele maszyn wirtualnych z tego samego szablonu:
* Można użyć [rozszerzenia programu Visual Studio Team Services zadania](https://marketplace.visualstudio.com/items?itemName=ms-azuredevtestlabs.tasks). 
* Możesz [Generowanie szablonu usługi Resource Manager](devtest-lab-add-vm.md#save-azure-resource-manager-template) podczas tworzenia maszyny Wirtualnej, i [wdrożyć szablon usługi Resource Manager przy użyciu programu Windows PowerShell](../azure-resource-manager/resource-group-template-deploy.md).

## <a name="how-do-i-move-my-existing-azure-vms-into-my-devtest-labs-lab"></a>Jak przenieść Mój istniejący maszynach wirtualnych platformy Azure do mojego laboratorium DevTest Labs?
Aby skopiować istniejących maszyn wirtualnych do DevTest Labs:

1. Skopiuj plik VHD istniejącej maszyny wirtualnej za pomocą [skrypt programu Windows PowerShell](https://github.com/Azure/azure-devtestlab/blob/master/Scripts/CopyVHDFromVMToLab.ps1).
2. [Tworzenie niestandardowego obrazu](devtest-lab-create-template.md) wewnątrz laboratorium DevTest Labs.
3. Utwórz maszynę Wirtualną w środowisku laboratoryjnym z niestandardowego obrazu.

## <a name="can-i-attach-multiple-disks-to-my-vms"></a>Do maszyn wirtualnych można podłączyć wiele dysków?
Tak, aby maszyny wirtualne można dołączyć wiele dysków.  

## <a name="if-i-want-to-use-a-windows-os-image-for-my-testing-do-i-have-to-purchase-an-msdn-subscription"></a>Aby użyć obrazu systemu operacyjnego Windows Moje testowanie, czy muszę kupić subskrypcję MSDN?
Aby używać obrazów systemu operacyjnego klienta systemu Windows (Windows 7 lub nowszy) dla programowania i testowania na platformie Azure, wykonaj jedną z następujących czynności:

- [Kup subskrypcję MSDN](https://www.visualstudio.com/products/how-to-buy-vs).
- Jeśli masz umowy Enterprise Agreement, Utwórz subskrypcję platformy Azure z [oferta przedsiębiorstwa i testowania](https://azure.microsoft.com/offers/ms-azr-0148p).

Aby uzyskać więcej informacji na temat kredytów systemu Azure dla każdego oferty MSDN, zobacz [Azure miesięczne środki dla subskrybentów programu Visual Studio](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/).

## <a name="how-do-i-automate-the-process-of-uploading-vhd-files-to-create-custom-images"></a>Jak zautomatyzować proces przekazywania plików wirtualnego dysku twardego do tworzenia niestandardowych obrazów?
Aby zautomatyzować przekazywania plików VHD, Utwórz niestandardowe obrazy, masz dwie opcje:

* Użyj [AzCopy](../storage/common/storage-use-azcopy.md#upload-blobs-to-blob-storage) kopiowania lub przekazać pliki wirtualnego dysku twardego do konta magazynu, który został skojarzony z laboratorium.
* Użyj [Eksploratora usługi Azure Storage](../vs-azure-tools-storage-manage-with-storage-explorer.md). Eksplorator usługi Storage jest aplikacją autonomiczną, która działa w systemie Windows, OS X i Linux.   

Aby znaleźć konto magazynu docelowego, który został skojarzony z laboratorium:

1. Zaloguj się w witrynie [Azure Portal](http://go.microsoft.com/fwlink/p/?LinkID=525040).
2. W menu po lewej stronie wybierz **grup zasobów**.
3. Znajdź i wybierz grupę zasobów, która jest skojarzona z laboratorium.
4. W obszarze **omówienie**, wybierz jedno z kont magazynu.
5. Wybierz **obiekty BLOB**.
6. Poszukaj przekazywania na liście. Jeśli żaden nie istnieje, wróć do kroku 4 i spróbuj innego konta magazynu.
7. Użyj **adres URL** jako miejsce docelowe polecenia AzCopy.

## <a name="how-do-i-automate-the-process-of-deleting-all-the-vms-in-my-lab"></a>Jak zautomatyzować proces usuwania wszystkich maszyn wirtualnych w laboratorium Moje?
Maszyny wirtualne można usunąć z laboratorium w portalu Azure. Ponadto można usunąć wszystkich maszyn wirtualnych w laboratorium za pomocą skryptu programu PowerShell. W poniższym przykładzie w obszarze **wartości w celu zmiany** komentarz, zmodyfikuj wartości parametrów. Możesz pobrać `subscriptionId`, `labResourceGroup`, i `labName` wartości z okienka laboratorium w portalu Azure.

    # Delete all the VMs in a lab.

    # Values to change:
    $subscriptionId = "<Enter Azure subscription ID here>"
    $labResourceGroup = "<Enter lab's resource group here>"
    $labName = "<Enter lab name here>"

    # Sign in to your Azure account.
    Connect-AzureRmAccount

    # Select the Azure subscription that has the lab. This step is optional
    # if you have only one subscription.
    Select-AzureRmSubscription -SubscriptionId $subscriptionId

    # Get the lab that has the VMs that you want to delete.
    $lab = Get-AzureRmResource -ResourceId ('subscriptions/' + $subscriptionId + '/resourceGroups/' + $labResourceGroup + '/providers/Microsoft.DevTestLab/labs/' + $labName)

    # Get the VMs from that lab.
    $labVMs = Get-AzureRmResource | Where-Object {
              $_.ResourceType -eq 'microsoft.devtestlab/labs/virtualmachines' -and
              $_.ResourceName -like "$($lab.ResourceName)/*"}

    # Delete the VMs.
    foreach($labVM in $labVMs)
    {
        Remove-AzureRmResource -ResourceId $labVM.ResourceId -Force
    }

**Artefakty**
## <a name="what-are-artifacts"></a>Co to są artefakty?
Artefakty są elementy dostosowywalne, które można wdrożyć z najnowszą bitów lub wdrożyć narzędziami deweloperów do maszyny Wirtualnej. Dołącz artefakty do maszyny Wirtualnej, podczas tworzenia maszyny Wirtualnej. Po zainicjowaniu obsługi maszyny Wirtualnej, artefakty wdrażanie i konfigurowanie maszyny Wirtualnej. Dostępne są różne artefakty istniejących naszych [publicznego repozytorium GitHub](https://github.com/Azure/azure-devtestlab/tree/master/Artifacts). Możesz również [tworzyć własne artefakty](devtest-lab-artifact-author.md).


**Konfiguracja laboratorium**
## <a name="how-do-i-create-a-lab-from-a-resource-manager-template"></a>Jak utworzyć laboratorium z szablonem usługi Resource Manager?
Oferujemy [repozytorium GitHub szablonów usługi Azure Resource Manager laboratorium](https://aka.ms/dtlquickstarttemplate) wdrażanego jako- lub zmodyfikować do utworzenia szablonów niestandardowych w laboratoriach sieci. Każdy szablon zawiera łącza do wdrożenia jako laboratorium — w ramach własnej subskrypcji platformy Azure. Lub możesz dostosować szablon i [wdrażania przy użyciu programu PowerShell lub interfejsu wiersza polecenia Azure](../azure-resource-manager/resource-group-template-deploy.md).

## <a name="why-are-my-vms-created-in-different-resource-groups-with-arbitrary-names-can-i-rename-or-modify-these-resource-groups"></a>Dlaczego moja maszyny wirtualne są tworzone w różnych grupach zasobów, z dowolnych nazw Można zmienić nazwy lub zmodyfikować te grupy zasobów?
Grupy zasobów są tworzone w ten sposób, aby zarządzać DevTest Labs uprawnienia użytkowników i dostęp do maszyn wirtualnych. Mimo że można przenieść Maszynę wirtualną do innej grupy zasobów, a następnie użyj nazwy, która ma, zaleca się nie wprowadzenia zmian do grupy zasobów. Pracujemy nad poprawą tego działania, aby umożliwić większą elastyczność.   

## <a name="how-many-labs-can-i-create-under-the-same-subscription"></a>Ile labs można tworzyć w ramach tej samej subskrypcji?
Nie ma określonego ograniczenia liczby labs, które mogą być tworzone na subskrypcję. Jednak ilość zasobów używanych dla subskrypcji jest ograniczona. Informacje o [ograniczenia i limity przydziału dla subskrypcji platformy Azure](../azure-subscription-service-limits.md) i [jak zwiększyć te limity](https://azure.microsoft.com/blog/azure-limits-quotas-increase-requests).

## <a name="how-many-vms-can-i-create-per-lab"></a>Jak wiele maszyn wirtualnych można utworzyć dla laboratorium?
Nie ma żadnego określonego limitu liczby maszyn wirtualnych, które mogą być tworzone dla laboratorium. Jednak zasobów (rdzenie maszyny Wirtualnej, publicznych adresów IP i tak dalej), które są używane są ograniczone na subskrypcję. Informacje o [ograniczenia i limity przydziału dla subskrypcji platformy Azure](../azure-subscription-service-limits.md) i [jak zwiększyć te limity](https://azure.microsoft.com/blog/azure-limits-quotas-increase-requests).

## <a name="how-do-i-share-a-direct-link-to-my-lab"></a>Jak udostępnić link bezpośredni do mojego laboratorium?

1. W portalu Azure przejdź do laboratorium.
2. Skopiuj adres URL laboratorium z przeglądarki, a następnie udostępnić go z użytkownikami laboratorium.

> [!NOTE]
> Jeśli użytkownik laboratorium jest użytkownika zewnętrznego, który ma [konta Microsoft](#what-is-a-microsoft-account), ale który nie jest elementem członkowskim wystąpienia usługi Active Directory w organizacji, użytkownik może wyświetlać komunikat o błędzie, gdy próbują uzyskać dostępu do udostępnionych łącza. Jeśli zewnętrznych użytkownik zobaczy następujący komunikat o błędzie, należy poprosić użytkownika o najpierw wybierz nazwy w prawym górnym rogu portalu Azure. Następnie w **katalogu** części menu, użytkownik może wybierz katalog, w której istnieje laboratorium.
>
>

## <a name="what-is-a-microsoft-account"></a>Co to jest konto Microsoft?
Konto Microsoft jest konto używane dla prawie wszystko, co zrobić z Microsoft urządzeń i usług. Adres e-mail i hasło, którego używasz do logowania do usługi Skype, Outlook.com, OneDrive, Windows phone i Xbox Live. Jednego konta oznacza, że pliki, zdjęć, kontakty i ustawienia wykonać należy na dowolnym urządzeniu.

> [!NOTE]
> Używane konto Microsoft ma być wywoływana *identyfikatora Windows Live ID*.
>
>


**Rozwiązywanie problemów**
## <a name="my-artifact-failed-during-vm-creation-how-do-i-troubleshoot-it"></a>Moje artefaktu nie powiodło się podczas tworzenia maszyny Wirtualnej. Jak rozwiązać go
Aby dowiedzieć się, jak pobrać dzienniki dla Twojego artefaktów nie powiodło się, zobacz [jak diagnozować awarie artefaktów w usłudze DevTest Labs](devtest-lab-troubleshoot-artifact-failure.md).

## <a name="why-isnt-my-existing-virtual-network-saving-properly"></a>Dlaczego nie jest Mój istniejący wirtualny sieci zapisywanie prawidłowo?
Jedną z możliwości to, że nazwy sieci wirtualnej zawiera kropki. Jeśli tak, spróbuj Usuwanie okresów lub zastępowania ich łączniki. Następnie spróbuj ponownie zapisać sieci wirtualnej.

## <a name="why-do-i-get-a-parent-resource-not-found-error-when-i-provision-a-vm-from-powershell"></a>Dlaczego, kiedy I udostępnić Maszynę wirtualną z programu PowerShell, jest zgłaszany błąd "Nie znaleziono zasobu nadrzędnego"?
Jeśli jeden zasób jest nadrzędny do innego zasobu, zasobu nadrzędnego musi istnieć przed utworzeniem zasobu podrzędnego. Jeśli zasobu nadrzędnego nie istnieje, zostanie wyświetlony **ParentResourceNotFound** wiadomości. Jeśli nie określisz zależność od zasobu nadrzędnego zasobu podrzędnego może wdrożyć przed nadrzędnego.

Maszyny wirtualne są zasoby podrzędne w laboratorium w grupie zasobów. Korzystając z szablonów usługi Resource Manager do wdrażania maszyn wirtualnych przy użyciu programu PowerShell, nazwa grupy zasobów, które są udostępniane w skrypcie programu PowerShell powinna być nazwa grupy zasobów w laboratorium. Aby uzyskać więcej informacji, zobacz [Rozwiąż typowe błędy wdrożenia usługi Azure](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-common-deployment-errors#parentresourcenotfound).

## <a name="where-can-i-find-more-error-information-if-a-vm-deployment-fails"></a>Gdzie można znaleźć więcej informacji o błędzie w przypadku wdrożenia maszyny Wirtualnej nie powiedzie się?
Błędy wdrożenia maszyny Wirtualnej są przechwytywane Dzienniki aktywności. Możesz znaleźć laboratorium Dzienniki aktywności maszyny Wirtualnej w obszarze **dzienniki inspekcji** lub **diagnostyki maszyny wirtualnej** menu zasobów w bloku maszyny Wirtualnej laboratorium (bloku pojawia się po wybraniu maszyny Wirtualnej z **Moje wirtualnego maszyny** listy).

Czasami błąd wdrażania występuje przed rozpoczęciem wdrażania maszyny Wirtualnej. Przykładem jest po przekroczeniu limitu subskrypcji dla zasobu, który został utworzony z maszyny Wirtualnej. W takim przypadku szczegóły błędu są przechwytywane Dzienniki aktywności poziom laboratorium. Dzienniki aktywności znajdują się w dolnej części **konfiguracji i zasadach** ustawienia. Aby uzyskać więcej informacji na temat używania działania logowania na platformie Azure, zobacz [wyświetlać dzienniki aktywności inspekcji akcje na zasoby](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-audit).

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]
