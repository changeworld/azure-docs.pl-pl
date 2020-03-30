---
title: Dodawanie umowoś jako umnażnika usługi Azure Automation do planów odzyskiwania usługi Site Recovery
description: Dowiedz się, jak rozszerzyć plany odzyskiwania za pomocą usługi Azure Automation w celu odzyskiwania po awarii przy użyciu usługi Azure Site Recovery.
author: rajani-janaki-ram
manager: gauravd
ms.service: site-recovery
ms.topic: conceptual
ms.date: 09/18/2019
ms.author: rajanaki
ms.openlocfilehash: ecfe993a137ca63c84438870ec54ac1e6d6707da
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79257487"
---
# <a name="add-azure-automation-runbooks-to-recovery-plans"></a>Dodawanie elementów Runbook usługi Azure Automation do planów odzyskiwania

W tym artykule opisano sposób integrowania śmięty uruchomieniu usługi Azure Automation w celu rozszerzenia planów odzyskiwania [usługi Azure Site Recovery.](site-recovery-overview.md) Pokażemy Ci, jak zautomatyzować podstawowe zadania, które w przeciwnym razie wymagałyby ręcznej interwencji, oraz jak przekonwertować odzyskiwanie wieloetapowe na akcję pojedynczego kliknięcia.

## <a name="recovery-plans"></a>Plany odzyskiwania 

Planów odzyskiwania można używać podczas pracy awaryjnej na komputerach lokalnych lub maszyn wirtualnych platformy Azure. Plany odzyskiwania ułatwiają zdefiniowanie systematycznego procesu odzyskiwania, który definiuje sposób pracy maszyn w pracy awaryjnej oraz sposób uruchamiania i odzyskiwania po pracy awaryjnej. 

Odzyskiwanie dużych aplikacji może być skomplikowane. Plany odzyskiwania pomagają narzucać porządek, dzięki czemu odzyskiwanie jest stale dokładne, powtarzalne i zautomatyzowane. Można zautomatyzować zadania w ramach planu odzyskiwania przy użyciu skryptów, a także uruchomieniu usługi Azure Automation. Typowymi przykładami może być konfigurowanie ustawień na maszynie Wirtualnej platformy Azure po przekładniu awaryjnym lub ponowne konfigurowanie aplikacji uruchomionej na maszynie wirtualnej.

- [Dowiedz się więcej](recovery-plan-overview.md) o planach odzyskiwania.
- [Dowiedz się więcej](../automation/automation-runbook-types.md) o podręcznikach uruchomieniu usługi Azure Automation.



## <a name="runbooks-in-recovery-plans"></a>Programy runbook w planach odzyskiwania

Dodaj konto usługi Azure Automation i elementy runbook do planu odzyskiwania. Projekt runbook jest wywoływany po uruchomieniu planu odzyskiwania.

- Konto automatyzacji może znajdować się w dowolnym regionie platformy Azure i musi znajdować się w tej samej subskrypcji co magazyn odzyskiwania witryny. 
- Projekt runbook można uruchomić w planie odzyskiwania podczas pracy awaryjnej z lokalizacji podstawowej do pomocniczego lub podczas powrotu po awarii z lokalizacji pomocniczej do podstawowego.
- Księgi runbook w planie odzyskiwania są uruchamiane szeregowo, jeden po drugim, w ustalonej kolejności.
- Jeśli programy runbook w planie odzyskiwania skonfigurować maszyny wirtualne, aby rozpocząć w różnych grupach, plan odzyskiwania będzie kontynuowana tylko wtedy, gdy platforma Azure raportuje wszystkie maszyny wirtualne jako uruchomione.
- Plany odzyskiwania nadal działać, nawet jeśli skrypt nie powiedzie się.

### <a name="recovery-plan-context"></a>Kontekst planu odzyskiwania

Po uruchomieniu skryptu wstrzykuje kontekst planu odzyskiwania do elementów runbook. Kontekst zawiera zmienne podsumowane w tabeli.

| **Nazwa zmiennej** | **Opis** |
| --- | --- |
| Nazwa odzyskiwania |Nazwa planu odzyskiwania. Używane w akcjach opartych na nazwie. |
| Typ pracy awaryjnej |Określa, czy jest to test lub produkcja pracy awaryjnej. 
| Kierunek pracy w podróży awaryjnej | Określa, czy odzyskiwanie jest do lokalizacji podstawowej lub pomocniczej. |
| Groupid |Identyfikuje numer grupy w planie odzyskiwania, gdy plan jest uruchomiony. |
| Vmmap |Tablica wszystkich maszyn wirtualnych w grupie. |
| Klucz VMMap |Unikatowy klucz (GUID) dla każdej maszyny Wirtualnej. |
| SubscriptionId |Identyfikator subskrypcji platformy Azure, w którym utworzono maszynę wirtualną. |
| ResourceGroupName | Nazwa grupy zasobów, w której znajduje się maszyna wirtualna.
| Nazwa usługi w chmurze |Nazwa usługi w chmurze platformy Azure, pod którą utworzono maszynę wirtualną. |
| RoleName |Nazwa maszyny Wirtualnej platformy Azure. |
| RecoveryPointId (ida programu RecoveryPointId)|Sygnatura czasowa odzyskiwania maszyny Wirtualnej. |

W poniższym przykładzie przedstawiono zmienną kontekstową:

```
{"RecoveryPlanName":"hrweb-recovery",
"FailoverType":"Test",
"FailoverDirection":"PrimaryToSecondary",
"GroupId":"1",
"VmMap":{"7a1069c6-c1d6-49c5-8c5d-33bfce8dd183":
    { "SubscriptionId":"7a1111111-c1d6-49c5-8c5d-111ce8dd183",
    "ResourceGroupName":"ContosoRG",
    "CloudServiceName":"pod02hrweb-Chicago-test",
    "RoleName":"Fabrikam-Hrweb-frontend-test",
    "RecoveryPointId":"TimeStamp"}
    }
}
```

Jeśli chcesz uzyskać dostęp do wszystkich maszyn wirtualnych w VMMap w pętli, możesz użyć następującego kodu:

```
$VMinfo = $RecoveryPlanContext.VmMap | Get-Member | Where-Object MemberType -EQ NoteProperty | select -ExpandProperty Name
$vmMap = $RecoveryPlanContext.VmMap
    foreach($VMID in $VMinfo)
    {
        $VM = $vmMap.$VMID                
            if( !(($VM -eq $Null) -Or ($VM.ResourceGroupName -eq $Null) -Or ($VM.RoleName -eq $Null))) {
            #this check is to ensure that we skip when some data is not available else it will fail
    Write-output "Resource group name ", $VM.ResourceGroupName
    Write-output "Rolename " = $VM.RoleName
            }
        }
```


Blog Aman Sharma na co [Harvesting Clouds](http://harvestingclouds.com) ma przydatny przykład [skryptu kontekstowego planu odzyskiwania.](http://harvestingclouds.com/post/script-sample-azure-automation-runbook-for-asr-recovery-plan/)



## <a name="before-you-start"></a>Przed rozpoczęciem

- Jeśli jesteś nowym użytkownikiem usługi Azure Automation, możesz [zarejestrować się](https://azure.microsoft.com/services/automation/) i [pobrać przykładowe skrypty.](https://azure.microsoft.com/documentation/scripts/)
- Upewnij się, że konto automatyzacji ma następujące moduły:
    - AzureRM.profile
    - AzureRM.Resources
    - AzureRM.Automation
    - AzureRM.Network
    - AzureRM.Compute

    Wszystkie moduły powinny być zgodne wersje. Najprostszym sposobem jest zawsze korzystać z najnowszych wersji wszystkich modułów.



## <a name="customize-the-recovery-plan"></a>Dostosowywanie planu odzyskiwania

1. W przechowalni wybierz plany **odzyskiwania (Odzyskiwanie witryny)**
2. Aby utworzyć plan odzyskiwania, kliknij **+Plan odzyskiwania**. [Dowiedz się więcej](site-recovery-create-recovery-plans.md). Jeśli masz już plan odzyskiwania, wybierz, aby go otworzyć.
3. Na stronie planu odzyskiwania kliknij pozycję **Dostosuj**.

    ![Kliknij przycisk Dostosuj](media/site-recovery-runbook-automation-new/custom-rp.png)

2. Kliknij wielokropek (...) obok **pozycji Grupa 1: Rozpocznij** > **akcję Dodaj post**.
3. W **obszarze Wstaw akcję**sprawdź, czy **skrypt** jest zaznaczony, i określ nazwę skryptu ( Hello**World**).
4. Określ konto automatyzacji i wybierz element runbook. Aby zapisać skrypt, kliknij przycisk **OK**. Skrypt jest dodawany do **grupy 1: Post-steps**.


## <a name="reuse-a-runbook-script"></a>Ponowne używanie skryptu żytnika

Za pomocą zmiennych zewnętrznych można użyć pojedynczego skryptu żytnika w wielu planach odzyskiwania. 

- Zmiennych [usługi Azure Automation](../automation/automation-variables.md) służy do przechowywania parametrów do uruchamiania planu odzyskiwania.
- Dodając nazwę planu odzyskiwania jako prefiks do zmiennej, można utworzyć poszczególne zmienne dla każdego planu odzyskiwania. Następnie użyj zmiennych jako parametrów.
- Można zmienić parametr bez zmiany skryptu, ale nadal zmieniać sposób działania skryptu.

### <a name="use-a-simple-string-variable-in-a-runbook-script"></a>Używanie prostej zmiennej ciągu w skrypcie życiorysu

W tym przykładzie skrypt pobiera dane wejściowe sieciowej grupy zabezpieczeń (NSG) i stosuje go do maszyn wirtualnych w planie odzyskiwania. 

1. Aby skrypt mógł wykryć, który plan odzyskiwania jest uruchomiony, użyj tego kontekstu planu odzyskiwania:

    ```
    workflow AddPublicIPAndNSG {
        param (
              [parameter(Mandatory=$false)]
              [Object]$RecoveryPlanContext
        )

        $RPName = $RecoveryPlanContext.RecoveryPlanName
    ```

2. Zanotuj nazwę grupy zasobów płciowych i zasobów. Te zmienne są używane jako dane wejściowe dla skryptów planu odzyskiwania. 
1. W zasobach kont automatyzacji. utworzyć zmienną do przechowywania nazwy grupy ndg. Dodaj prefiks do nazwy zmiennej o nazwie planu odzyskiwania.

    ![Tworzenie zmiennej nazwy grupy nsg](media/site-recovery-runbook-automation-new/var1.png)

2. Utwórz zmienną do przechowywania nazwy grupy zasobów zasobu grupy ndg. Dodaj prefiks do nazwy zmiennej o nazwie planu odzyskiwania.

    ![Tworzenie nazwy grupy zasobów grupy zasobów grupy zasobów grupy nsg](media/site-recovery-runbook-automation-new/var2.png)


3.  W skrypcie użyj tego kodu referencyjnego, aby uzyskać wartości zmiennych:

    ```
    $NSGValue = $RecoveryPlanContext.RecoveryPlanName + "-NSG"
    $NSGRGValue = $RecoveryPlanContext.RecoveryPlanName + "-NSGRG"

    $NSGnameVar = Get-AutomationVariable -Name $NSGValue
    $RGnameVar = Get-AutomationVariable -Name $NSGRGValue
    ```

4.  Użyj zmiennych w biełliku runbook, aby zastosować sieciowej grupy ściągniętej do interfejsu sieciowego maszyny wirtualnej, która uległa awarii:

    ```
    InlineScript {
    if (($Using:NSGname -ne $Null) -And ($Using:NSGRGname -ne $Null)) {
            $NSG = Get-AzureRmNetworkSecurityGroup -Name $Using:NSGname -ResourceGroupName $Using:NSGRGname
            Write-output $NSG.Id
            #Apply the NSG to a network interface
            #$vnet = Get-AzureRmVirtualNetwork -ResourceGroupName TestRG -Name TestVNet
            #Set-AzureRmVirtualNetworkSubnetConfig -VirtualNetwork $vnet -Name FrontEnd `
            #  -AddressPrefix 192.168.1.0/24 -NetworkSecurityGroup $NSG
        }
    }
    ```


Dla każdego planu odzyskiwania należy utworzyć niezależne zmienne, aby można było ponownie użyć skryptu. Dodaj prefiks przy użyciu nazwy planu odzyskiwania. 

Aby uzyskać kompletny, kompleksowy skrypt dla tego [scenariusza,](https://gallery.technet.microsoft.com/Add-Public-IP-and-NSG-to-a6bb8fee)przejrzyj ten skrypt .


### <a name="use-a-complex-variable-to-store-more-information"></a>Przechowywanie większej ilości informacji za pomocą zmiennej złożonej

W niektórych scenariuszach może nie być możliwe utworzenie oddzielnych zmiennych dla każdego planu odzyskiwania. Należy wziąć pod uwagę scenariusz, w którym chcesz pojedynczy skrypt przypisać publiczny adres IP na określonych maszynach wirtualnych. W innym scenariuszu można zastosować różne sieciowe sieciowe na różnych maszynach wirtualnych (nie na wszystkich maszynach wirtualnych). Należy pamiętać, że:

- Można wykonać skrypt, który jest wielokrotnego użytku dla dowolnego planu odzyskiwania.
- Każdy plan odzyskiwania może mieć zmienną liczbę maszyn wirtualnych.
- Na przykład odzyskiwanie programu SharePoint ma dwa frontowe. Podstawowa aplikacja biznesowa (LOB) ma tylko jeden front-end.
- W tym scenariuszu nie można utworzyć oddzielnych zmiennych dla każdego planu odzyskiwania.

W poniższym przykładzie utworzymy [złożoną zmienną](https://docs.microsoft.com/powershell/module/servicemanagement/azure/set-azureautomationvariable) na koncie usługi Azure Automation.

Robimy to, określając wiele wartości przy użyciu programu Azure PowerShell.

1. W programie PowerShell zaloguj się do subskrypcji platformy Azure:

    ```
    Connect-AzureRmAccount
    $sub = Get-AzureRmSubscription -Name <SubscriptionName>
    $sub | Select-AzureRmSubscription
    ```

2. Aby zapisać parametry, utwórz złożoną zmienną przy użyciu nazwy planu odzyskiwania:

    ```
    $VMDetails = @{"VMGUID"=@{"ResourceGroupName"="RGNameOfNSG";"NSGName"="NameOfNSG"};"VMGUID2"=@{"ResourceGroupName"="RGNameOfNSG";"NSGName"="NameOfNSG"}}
        New-AzureRmAutomationVariable -ResourceGroupName <RG of Automation Account> -AutomationAccountName <AA Name> -Name <RecoveryPlanName> -Value $VMDetails -Encrypted $false
    ```

3. W tej złożonej zmiennej **VMDetails** jest identyfikatorem maszyny Wirtualnej dla chronionej maszyny Wirtualnej. Aby uzyskać identyfikator maszyny Wirtualnej, w witrynie Azure portal, zobacz właściwości maszyny Wirtualnej. Poniższy zrzut ekranu przedstawia zmienną, która przechowuje szczegóły dwóch maszyn wirtualnych:

    ![Używanie identyfikatora maszyny Wirtualnej jako identyfikatora GUID](media/site-recovery-runbook-automation-new/vmguid.png)

4. Użyj tej zmiennej w biełgodniku. Jeśli wskazany identyfikator GUID maszyny Wirtualnej znajduje się w kontekście planu odzyskiwania, zastosuj sieciowej sieciowej na maszynie Wirtualnej:

    ```
    $VMDetailsObj = (Get-AutomationVariable -Name $RecoveryPlanContext.RecoveryPlanName).ToObject([hashtable])
    ```

4. W uruchomieniu pętli za pośrednictwem maszyn wirtualnych kontekstu planu odzyskiwania. Sprawdź, czy maszyna wirtualna istnieje w **$VMDetailsObj**. Jeśli istnieje, należy uzyskać dostęp do właściwości zmiennej, aby zastosować sieciowej grupy sieciowej:

    ```
        $VMinfo = $RecoveryPlanContext.VmMap | Get-Member | Where-Object MemberType -EQ NoteProperty | select -ExpandProperty Name
        $vmMap = $RecoveryPlanContext.VmMap

        foreach($VMID in $VMinfo) {
            $VMDetails = $VMDetailsObj[$VMID].ToObject([hashtable]);
            Write-output $VMDetails
            if ($VMDetails -ne $Null) { #If the VM exists in the context, this will not be Null
                $VM = $vmMap.$VMID
                # Access the properties of the variable
                $NSGname = $VMDetails.NSGName
                $NSGRGname = $VMDetails.NSGResourceGroupName

                # Add code to apply the NSG properties to the VM
            }
        }
    ```

Można użyć tego samego skryptu dla różnych planów odzyskiwania. Wprowadź różne parametry, przechowując wartość odpowiadającą planowi odzyskiwania w różnych zmiennych.

## <a name="sample-scripts"></a>Przykładowe skrypty

Aby wdrożyć przykładowe skrypty na koncie automatyzacji, kliknij przycisk Wdrażanie na **platformie Azure.**

[![Wdrażanie na platformie Azure](https://azurecomcdn.azureedge.net/mediahandler/acomblog/media/Default/blog/c4803408-340e-49e3-9a1f-0ed3f689813d.png)](https://aka.ms/asr-automationrunbooks-deploy)

Ten film wideo zawiera inny przykład. Pokazuje, jak odzyskać dwuwarstwową aplikację WordPress na platformie Azure:


> [!VIDEO https://channel9.msdn.com/Series/Azure-Site-Recovery/One-click-failover-of-a-2-tier-WordPress-application-using-Azure-Site-Recovery/player]


## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o [koncie Uruchamianie automatyzacji platformy Azure jako](../automation/automation-create-runas-account.md)
- Przejrzyj [przykładowe skrypty usługi Azure Automation](https://gallery.technet.microsoft.com/scriptcenter/site/search?f%5B0%5D.Type=User&f%5B0%5D.Value=SC%20Automation%20Product%20Team&f%5B0%5D.Text=SC%20Automation%20Product%20Team).
- [Dowiedz się więcej](site-recovery-failover.md) o uruchamianiu pracy w pracy w pracy awaryjnej.



