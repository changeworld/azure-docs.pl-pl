---
title: Uwierzytelnianie na platformie Azure za pomocą usługi Azure AD w usłudze Azure Automation
description: Dowiedz się, jak używać usługi Azure AD w ramach usługi Azure Automation jako dostawcy uwierzytelniania na platformie Azure.
services: automation
ms.date: 03/30/2020
ms.topic: conceptual
ms.openlocfilehash: 90338a1ffa79e6c2347832cb2e74633db02ec72d
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/02/2020
ms.locfileid: "80548336"
---
# <a name="use-azure-ad-in-azure-automation-to-authenticate-to-azure"></a>Uwierzytelnianie na platformie Azure za pomocą usługi Azure AD w usłudze Azure Automation

Usługa [Azure Active Directory (AD)](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-whatis) umożliwia wykonywanie wielu zadań administracyjnych, takich jak zarządzanie użytkownikami, zarządzanie domeną i konfiguracja logowania jednokrotnego. W tym artykule opisano sposób używania usługi Azure AD w ramach usługi Azure Automation jako dostawcy uwierzytelniania na platformie Azure. 

>[!NOTE]
>Ten artykuł został zaktualizowany o korzystanie z nowego modułu Azure PowerShell Az. Nadal możesz używać modułu AzureRM, który będzie nadal otrzymywać poprawki błędów do co najmniej grudnia 2020 r. Aby dowiedzieć się więcej na temat nowego modułu Az i zgodności z modułem AzureRM, zobacz [Wprowadzenie do nowego modułu Az programu Azure PowerShell](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Aby uzyskać instrukcje instalacji modułu Az w hybrydowym usłudze Runbook Worker, zobacz [Instalowanie modułu programu Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). Dla konta automatyzacji można zaktualizować moduły do najnowszej wersji przy użyciu [jak zaktualizować moduły programu Azure PowerShell w usłudze Azure Automation.](automation-update-azure-modules.md)

## <a name="installing-azure-ad-modules"></a>Instalowanie modułów usługi Azure AD

Usługę Azure AD można włączyć za pomocą następujących modułów programu PowerShell:

* Usługi Azure Active Directory PowerShell for Graph (moduły AzureRM i Az). Usługa Azure Automation jest dostarczana z modułem AzureRM i jego niedawnym uaktualnieniem, modułem Az. Funkcja obejmuje uwierzytelnianie nieinterakcyjne na platformie Azure przy użyciu uwierzytelniania opartego na poświadczeniach usługi Azure AD (OrgId). Zobacz [usługi Azure AD 2.0.2.76](https://www.powershellgallery.com/packages/AzureAD/2.0.2.76).

* Usługa Microsoft Azure Active Directory dla programu Windows PowerShell (moduł MSOnline). Ten moduł umożliwia interakcje z usługą Microsoft Online, w tym z usługą Office 365.

>[!NOTE]
>Program PowerShell Core nie obsługuje modułu MSOnline. Aby użyć poleceń cmdlet modułu, należy uruchomić je z programu Windows PowerShell. Zachęcamy do używania nowszych modułów programu Azure Active Directory PowerShell for Graph zamiast modułu MSOnline. 

### <a name="preinstallation"></a>Preinstalacji

Przed zainstalowaniem modułów usługi Azure AD na komputerze:

* Odinstaluj wszystkie poprzednie wersje modułu AzureRM/Az i modułu MSOnline. 

* Odinstaluj Asystenta logowania usług online firmy Microsoft, aby zapewnić poprawne działanie nowych modułów programu PowerShell.  

### <a name="install-the-azurerm-and-az-modules"></a>Instalowanie modułów AzureRM i Az

>[!NOTE]
>Aby pracować z tymi modułami, należy użyć programu PowerShell w wersji 5.1 lub nowszej z 64-bitową wersją systemu Windows. 

1. Zainstaluj platformę Windows Management Framework (WMF) 5.1. Zobacz [Instalowanie i konfigurowanie WMF 5.1](https://docs.microsoft.com/powershell/scripting/wmf/setup/install-configure?view=powershell-7).

2. Zainstaluj usługę AzureRM i/lub Az, korzystając z instrukcji w [programie Zainstaluj program Azure PowerShell w systemie Windows za pomocą programu PowerShellGet](https://docs.microsoft.com/powershell/azure/azurerm/install-azurerm-ps?view=azurermps-6.13.0).

### <a name="install-the-msonline-module"></a>Instalowanie modułu MSOnline

>[!NOTE]
>Aby zainstalować moduł MSOnline, musisz być członkiem roli administratora usługi Office 365. Zobacz [Informacje o rolach administratora](https://docs.microsoft.com/microsoft-365/admin/add-users/about-admin-roles?view=o365-worldwide).

1. Upewnij się, że na komputerze jest włączona funkcja microsoft .NET Framework 3.5.x. Jest prawdopodobne, że komputer ma zainstalowaną nowszą wersję, ale zgodność wsteczna ze starszymi wersjami programu .NET Framework może być włączona lub wyłączona. 

2. Zainstaluj 64-bitową wersję [Asystenta logowania usług Online Services](https://www.microsoft.com/download/details.aspx?id=41950)firmy Microsoft .

3. Uruchom program Windows PowerShell jako administrator, aby utworzyć wiersz polecenia programu Windows PowerShell z podwyższonym poziomem uprawnień.

4. Wdrażanie usługi Azure Active Directory z [usługi MSOnline 1.0](http://www.powershellgallery.com/packages/MSOnline/1.0).

5. Jeśli zostanie wyświetlony monit o zainstalowanie dostawcy NuGet, wpisz Y i naciśnij klawisz ENTER.

6. Jeśli zostanie wyświetlony monit o zainstalowanie modułu z [PSGallery,](https://www.powershellgallery.com/)wpisz Y i naciśnij klawisz ENTER.

### <a name="install-support-for-pscredential"></a>Zainstaluj obsługę pscredential

Usługa Azure Automation używa klasy [PSCredential](https://docs.microsoft.com/dotnet/api/system.management.automation.pscredential?view=pscore-6.2.0) do reprezentowania zasobu poświadczeń. Skrypty pobierają `PSCredential` obiekty przy `Get-AutomationPSCredential` użyciu polecenia cmdlet. Aby uzyskać więcej informacji, zobacz [Zasoby poświadczeń w usłudze Azure Automation](shared-resources/credentials.md).

## <a name="assigning-a-subscription-administrator"></a>Przypisywanie administratora subskrypcji

Należy przypisać administratora dla subskrypcji platformy Azure. Ta osoba ma rolę właściciela dla zakresu subskrypcji. Zobacz [kontrola dostępu oparta na rolach w usłudze Azure Automation.](automation-role-based-access-control.md) 

## <a name="changing-the-azure-ad-users-password"></a>Zmiana hasła użytkownika usługi Azure AD

Aby zmienić hasło użytkownika usługi Azure AD:

1. Wyloguj się z platformy Azure.

2. Niech administrator zaloguje się na platformę Azure jako użytkownik usługi Azure AD właśnie utworzony, przy użyciu pełnej nazwy użytkownika (w tym domeny) i hasła tymczasowego. 

3. Poproś administratora o zmianę hasła po wyświetleniu monitu.

## <a name="configuring-azure-automation-to-use-the-azure-ad-user-to-manage-the-azure-subscription"></a>Konfigurowanie usługi Azure Automation do używania użytkownika usługi Azure AD do zarządzania subskrypcją platformy Azure

Aby usługa Azure Automation komunikowała się z usługą Azure AD, należy pobrać poświadczenia skojarzone z połączeniem platformy Azure z usługą Azure AD. Przykładami tych poświadczeń są identyfikator dzierżawy, identyfikator subskrypcji i tym podobne. Aby uzyskać więcej informacji na temat połączenia między platformą Azure a usługą Azure AD, zobacz [Łączenie organizacji z usługą Azure Active Directory](https://docs.microsoft.com/azure/devops/organizations/accounts/connect-organization-to-azure-ad?view=azure-devops).

## <a name="creating-a-credential-asset"></a>Tworzenie zasobu poświadczeń

Dzięki dostępności poświadczeń platformy Azure dla usługi Azure AD nadszedł czas, aby utworzyć zasób poświadczeń usługi Azure Automation, aby bezpiecznie przechowywać poświadczenia usługi Azure AD, aby skrypty konfiguracji stanu rozwiązania chcę i skrypty konfiguracji stanu desire (DSC) mogły do nich uzyskać dostęp. Można to zrobić za pomocą portalu Azure lub poleceń cmdlet programu PowerShell.

### <a name="create-the-credential-asset-in-azure-portal"></a>Tworzenie zasobu poświadczeń w witrynie Azure portal

Za pomocą witryny Azure Portal można utworzyć zasób poświadczeń. Wykonaj tę operację z konta automatyzacji przy użyciu **poświadczeń** w obszarze **Zasoby udostępnione**. Zobacz [Zasoby poświadczeń w usłudze Azure Automation](shared-resources/credentials.md).

### <a name="create-the-credential-asset-with-windows-powershell"></a>Tworzenie zasobu poświadczeń za pomocą programu Windows PowerShell

Aby przygotować nowy zasób poświadczeń w `PSCredential` programie Windows PowerShell, skrypt najpierw tworzy obiekt przy użyciu przypisanej nazwy użytkownika i hasła. Skrypt następnie używa tego obiektu do utworzenia zasobu za pośrednictwem wywołania polecenia cmdlet [New-AzureAutomationCredential.](https://docs.microsoft.com/powershell/module/servicemanagement/azure/new-azureautomationcredential?view=azuresmps-4.0.0) Alternatywnie skrypt może wywołać polecenie cmdlet [Get-Credential,](https://docs.microsoft.com/powershell/module/microsoft.powershell.security/get-credential?view=powershell-7) aby poprosić użytkownika o wpisanie nazwy i hasła. Zobacz [Zasoby poświadczeń w usłudze Azure Automation](shared-resources/credentials.md). 

## <a name="managing-azure-resources-from-an-azure-automation-runbook"></a>Zarządzanie zasobami platformy Azure za pomocą systemu runbook usługi Azure Automation

Zasoby platformy Azure można zarządzać z uruchomieniu usługi Azure Automation przy użyciu zasobu poświadczeń. Poniżej znajduje się przykładowy element runbook programu PowerShell, który zbiera zasób poświadczeń do użycia do zatrzymywania i uruchamiania maszyn wirtualnych w ramach subskrypcji platformy Azure. Ten system runbook `Get-AutomationPSCredential` używa najpierw do pobrania poświadczeń do uwierzytelnienia na platformie Azure. Następnie wywołuje polecenie cmdlet [Connect-AzAccount,](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount?view=azps-3.6.1) aby połączyć się z platformą Azure przy użyciu poświadczeń. Skrypt używa polecenia cmdlet [Select-AzureSubscription,](https://docs.microsoft.com/powershell/module/servicemanagement/azure/select-azuresubscription?view=azuresmps-4.0.0) aby wybrać subskrypcję do pracy. 

```azurepowershell
Workflow Stop-Start-AzureVM 
{ 
    Param 
    (    
        [Parameter(Mandatory=$true)][ValidateNotNullOrEmpty()] 
        [String] 
        $AzureSubscriptionId, 
        [Parameter(Mandatory=$true)][ValidateNotNullOrEmpty()] 
        [String] 
        $AzureVMList="All", 
        [Parameter(Mandatory=$true)][ValidateSet("Start","Stop")] 
        [String] 
        $Action 
    ) 
     
    $credential = Get-AutomationPSCredential -Name 'AzureCredential' 
    Connect-AzAccount -Credential $credential 
    Select-AzureSubscription -SubscriptionId $AzureSubscriptionId 
 
    if($AzureVMList -ne "All") 
    { 
        $AzureVMs = $AzureVMList.Split(",") 
        [System.Collections.ArrayList]$AzureVMsToHandle = $AzureVMs 
    } 
    else 
    { 
        $AzureVMs = (Get-AzVM).Name 
        [System.Collections.ArrayList]$AzureVMsToHandle = $AzureVMs 
 
    } 
 
    foreach($AzureVM in $AzureVMsToHandle) 
    { 
        if(!(Get-AzVM | ? {$_.Name -eq $AzureVM})) 
        { 
            throw " AzureVM : [$AzureVM] - Does not exist! - Check your inputs " 
        } 
    } 
 
    if($Action -eq "Stop") 
    { 
        Write-Output "Stopping VMs"; 
        foreach -parallel ($AzureVM in $AzureVMsToHandle) 
        { 
            Get-AzVM | ? {$_.Name -eq $AzureVM} | Stop-AzVM -Force 
        } 
    } 
    else 
    { 
        Write-Output "Starting VMs"; 
        foreach -parallel ($AzureVM in $AzureVMsToHandle) 
        { 
            Get-AzVM | ? {$_.Name -eq $AzureVM} | Start-AzVM 
        } 
    } 
}
```  

## <a name="next-steps"></a>Następne kroki

* Informacje o zasobach poświadczeń automatyzacji można znaleźć w [zasobach poświadczeń w usłudze Azure Automation](shared-resources/credentials.md).
* Zobacz [Zarządzanie modułami w usłudze Azure Automation,](shared-resources/modules.md) aby dowiedzieć się, jak pracować z modułami automatyzacji.
* Aby dowiedzieć się więcej na temat metod, które mogą służyć do uruchamiania uruchomieniu w usłudze Azure Automation, zobacz [Uruchamianie uruchomieniu w usłudze Azure Automation.](automation-starting-a-runbook.md)
* Aby uzyskać więcej informacji na temat programu PowerShell, w tym modułów referencyjnych i modułów szkoleniowych, zobacz [Dokumenty programu PowerShell](https://docs.microsoft.com/powershell/scripting/overview).