---
title: Dodawanie elementów runbook usługi Azure Automation do planów odzyskiwania Site Recovery | Dokumentacja firmy Microsoft
description: Dowiedz się, jak rozszerzyć plany odzyskiwania z usługą Azure Automation na potrzeby odzyskiwania po awarii przy użyciu usługi Azure Site Recovery.
author: rajani-janaki-ram
manager: gauravd
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/27/2018
ms.author: rajanaki
ms.openlocfilehash: 26c3466080cb356ca3610d42eaaf5ee4975d3731
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "61471950"
---
# <a name="add-azure-automation-runbooks-to-recovery-plans"></a>Dodawanie elementów runbook usługi Azure Automation do planów odzyskiwania
W tym artykule opisano jak Azure Site Recovery integruje się z usługą Azure Automation do zwiększenia planów odzyskiwania. Plany odzyskiwania można organizować odzyskiwania maszyn wirtualnych, które są chronione przy użyciu usługi Site Recovery. Plany odzyskiwania działa zarówno na replikację do dodatkowej chmury i replikacji do platformy Azure. Plany odzyskiwania również sprawić, odzyskiwanie **spójnie dokładne**, **powtarzalne**, i **automatycznych**. W przypadku przejścia w tryb failover maszyn wirtualnych na platformie Azure, integracji z usługą Azure Automation rozszerza planów odzyskiwania. Służy do wykonywania elementów runbook, które oferują zaawansowanej automatyzacji zadań.

Jeśli jesteś nowym użytkownikiem usługi Azure Automation, możesz to zrobić [Zarejestruj](https://azure.microsoft.com/services/automation/) i [Pobierz przykładowe skrypty](https://azure.microsoft.com/documentation/scripts/). Aby uzyskać więcej informacji i dowiedzieć się, jak zorganizować odzyskiwanie na platformę Azure za pomocą [planów odzyskiwania](./site-recovery-create-recovery-plans.md), zobacz [usługi Azure Site Recovery](https://azure.microsoft.com/services/site-recovery/).

W tym artykule opisano sposób integrowania elementów runbook usługi Azure Automation do planów odzyskiwania. Przykłady są używane do zautomatyzowania podstawowe zadania, które wcześniej wymagały ręcznej interwencji. Opisano również sposób konwertowania odzyskiwania wieloetapowego akcji odzyskiwania jednym kliknięciem.

## <a name="customize-the-recovery-plan"></a>Dostosowywać plan odzyskiwania
1. Przejdź do **Site Recovery** bloku zasobów planu odzyskiwania. W tym przykładzie plan odzyskiwania obejmuje dwie maszyny wirtualne, które dodano do niego, do odzyskania. Aby rozpocząć dodawanie elementu runbook, kliknij przycisk **Dostosuj** kartę.

    ![Kliknij przycisk Dostosuj](media/site-recovery-runbook-automation-new/essentials-rp.png)


2. Kliknij prawym przyciskiem myszy **Grupa 1: Rozpocznij**, a następnie wybierz pozycję **Dodaj akcję po**.

    ![Kliknij prawym przyciskiem myszy Grupa 1: Uruchom i dodawanie akcji post](media/site-recovery-runbook-automation-new/customize-rp.png)

3. Kliknij przycisk **wybierz skrypt**.

4. Na **Akcja aktualizacji** bloku i nazwę skryptu **Witaj, świecie**.

    ![W bloku akcji aktualizacji](media/site-recovery-runbook-automation-new/update-rp.png)

5. Wprowadź nazwę konta usługi Automation.
    >[!NOTE]
    > Konto usługi Automation można w dowolnym regionie systemu Azure. Konto usługi Automation, musi być w tej samej subskrypcji co magazyn usługi Azure Site Recovery.

6. Na koncie usługi Automation wybierz element runbook. Ten element runbook to skrypt, który jest uruchamiany podczas wykonywania planu odzyskiwania po odzyskiwaniu pierwszą grupę.

7. Aby zapisać skrypt, kliknij przycisk **OK**. Skrypt jest dodawany do **Grupa 1: Kroki po operacji**.

    ![Akcja po uaktualnianiu grupy 1:Start](media/site-recovery-runbook-automation-new/addedscript-rp.PNG)


## <a name="considerations-for-adding-a-script"></a>Informacje dotyczące dodawania skryptu

* Dla opcji **usunąć krok** lub **zaktualizować ten skrypt**, kliknij prawym przyciskiem myszy skrypt.
* Można uruchomić skryptu na platformie Azure podczas pracy awaryjnej z maszyny lokalnej na platformę Azure. Również może działać na platformie Azure jako lokację podstawową skrypt przed zamknięciem, podczas powrotu po awarii z platformy Azure na maszynie lokalnej.
* Po uruchomieniu skryptu wprowadza kontekstu planu odzyskiwania. Poniższy przykład przedstawia zmienną kontekstową:

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

    Poniższa lista zawiera nazwę i opis każdej zmiennej w kontekście.

    | **Nazwa zmiennej** | **Opis** |
    | --- | --- |
    | RecoveryPlanName |Nazwa planu są uruchamiane. Ta zmienna pomaga wykonać różne operacje, w oparciu o nazwę planu odzyskiwania. Możesz także ponownie użyć skryptu. |
    | FailoverType |Określa, czy tryb failover jest testu, planowane lub nieplanowane. |
    | Element FailoverDirection |Określa, czy odzyskiwanie odbywa się lokacja główna lub dodatkowa. |
    | Identyfikator grupy |Określa numer grupy w planie odzyskiwania, gdy plan jest uruchomiony. |
    | VmMap |Tablica wszystkich maszyn wirtualnych w grupie. |
    | Klucz VMMap |Unikatowy klucz (GUID) dla każdej maszyny Wirtualnej. Jest taki sam jak identyfikator usługi Azure Virtual Machine Manager (VMM) maszyny wirtualnej, jeśli ma to zastosowanie. |
    | SubscriptionId |Identyfikator subskrypcji platformy Azure, w którym utworzono maszynę Wirtualną. |
    | RoleName |Nazwa maszyny Wirtualnej platformy Azure, który jest odzyskiwany. |
    | CloudServiceName |Nazwa usługi chmury platformy Azure w ramach której utworzono maszynę Wirtualną. |
    | ResourceGroupName|Nazwa grupy zasobów platformy Azure w ramach której utworzono maszynę Wirtualną. |
    | RecoveryPointId|Znacznik czasu po odzyskaniu maszyna wirtualna. |

* Upewnij się, że konto usługi Automation ma następujących modułów:
    * AzureRM.profile
    * AzureRM.Resources
    * AzureRM.Automation
    * AzureRM.Network
    * AzureRM.Compute

Wszystkie moduły muszą być zgodne wersje. Prosty sposób, aby upewnić się, że wszystkie moduły są zgodne, jest używanie najnowszych wersji wszystkich modułów.

### <a name="access-all-vms-of-the-vmmap-in-a-loop"></a>Dostęp do wszystkich maszyn wirtualnych z VMMap w pętli
Użyj poniższego kodu w pętli wszystkich maszyn wirtualnych z VMMap firmy Microsoft:

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

> [!NOTE]
> Nazwa i rola wartości nazw grup zasobów są puste, kiedy skrypt będzie akcję wstępną do grupy rozruchu. Wartości są wypełniane tylko wtedy, gdy maszyna wirtualna w tej grupie zakończy się pomyślnie w tryb failover. Skrypt jest akcja po uaktualnianiu grupy rozruchu.

## <a name="use-the-same-automation-runbook-in-multiple-recovery-plans"></a>Użyj tego samego elementu runbook usługi Automation w wielu planów odzyskiwania

Możesz użyć pojedynczego skryptu wiele planów odzyskiwania, za pomocą zmiennych zewnętrznych. Możesz użyć [zmiennych w usłudze Azure Automation](../automation/automation-variables.md) do przechowywania parametrów, które można przekazywać do wykonania planu odzyskiwania. Dodając Nazwa planu odzyskiwania jako prefiks do zmiennej, można utworzyć pojedyncze zmienne dla każdego planu odzyskiwania. Następnie należy użyć zmiennych jako parametry. Można zmienić parametru bez wprowadzania zmian w skrypcie, ale nadal zmienić sposób działania skryptu.

### <a name="use-a-simple-string-variable-in-a-runbook-script"></a>Użyj zmiennej prostego ciągu w skrypcie elementu runbook

W tym przykładzie skrypt pobiera dane wejściowe z grupy zabezpieczeń sieci (NSG) i stosuje je do maszyn wirtualnych w planie odzyskiwania.

Dla skryptu do wykrywania odzyskiwania, który plan jest uruchomiona, należy użyć kontekstu planu odzyskiwania:

```
workflow AddPublicIPAndNSG {
    param (
          [parameter(Mandatory=$false)]
          [Object]$RecoveryPlanContext
    )

    $RPName = $RecoveryPlanContext.RecoveryPlanName
```

Aby zastosować istniejącej sieciowej grupie zabezpieczeń, trzeba znać nazwę sieciową grupę zabezpieczeń i nazwy grupy zasobów sieciowych grup zabezpieczeń. Na użytek tych zmiennych jako dane wejściowe skrypty planu odzyskiwania. Aby to zrobić, należy utworzyć dwie zmienne w zasoby konta usługi Automation. Dodaj nazwę plan odzyskiwania, który tworzysz parametry jako prefiks do nazwy zmiennej.

1. Utwórz zmienną do przechowywania nazwy sieciowej grupy zabezpieczeń. Dodaj prefiks do nazwy zmiennej przy użyciu nazwy planu odzyskiwania.

    ![Utwórz zmienną nazwy sieciowej grupy zabezpieczeń](media/site-recovery-runbook-automation-new/var1.png)

2. Utwórz zmienną do przechowywania nazwy grupy zasobów sieciowych grup zabezpieczeń. Dodaj prefiks do nazwy zmiennej przy użyciu nazwy planu odzyskiwania.

    ![Utwórz nazwę grupy zasobów sieciowych grup zabezpieczeń](media/site-recovery-runbook-automation-new/var2.png)


3.  W skrypcie Użyj następującego kodu odwołania można pobrać wartości zmiennych:

    ```
    $NSGValue = $RecoveryPlanContext.RecoveryPlanName + "-NSG"
    $NSGRGValue = $RecoveryPlanContext.RecoveryPlanName + "-NSGRG"

    $NSGnameVar = Get-AutomationVariable -Name $NSGValue
    $RGnameVar = Get-AutomationVariable -Name $NSGRGValue
    ```

4.  Użyj zmiennych w elemencie runbook, aby zastosować sieciową grupę zabezpieczeń do interfejsu sieciowego maszyny Wirtualnej w trybie failed-over:

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

Dla każdego planu odzyskiwania należy utworzyć zmienne niezależne, aby ponownie użyć skryptu. Dodaj prefiks, przy użyciu nazwy planu odzyskiwania. Aby skrypt pełne, end-to-end dla tego scenariusza, zobacz [Dodawanie publicznego adresu IP i sieciowej grupy zabezpieczeń na maszynach wirtualnych podczas testowania trybu failover planu odzyskiwania Usługa Site Recovery](https://gallery.technet.microsoft.com/Add-Public-IP-and-NSG-to-a6bb8fee).


### <a name="use-a-complex-variable-to-store-more-information"></a>Użyj zmiennej złożone do przechowywania informacji

Rozważmy scenariusz, w którym chcesz jednego skryptu, aby włączyć publiczny adres IP na określonych maszyn wirtualnych. W innym scenariuszu można zastosować różne sieciowe grupy zabezpieczeń na różnych maszynach wirtualnych (a nie na wszystkich maszynach wirtualnych). Istnieje możliwość skrypt, który służy do wielokrotnego użytku dla dowolnego planu odzyskiwania. Każdy plan odzyskiwania może mieć zmienną liczbę maszyn wirtualnych. Na przykład odzyskiwanie programu SharePoint zawiera dwa Frontony. Aplikacja podstawowa line-of-business (LOB), zawiera tylko jeden serwer sieci Web. Nie można utworzyć oddzielne zmienne dla każdego planu odzyskiwania.

W poniższym przykładzie będziemy używać nowych technik i utworzyć [zmiennej złożone](https://docs.microsoft.com/powershell/module/servicemanagement/azure/set-azureautomationvariable) w zasoby konta usługi Azure Automation. Można to zrobić poprzez określenie wielu wartości. Za pomocą programu Azure PowerShell należy wykonać następujące czynności:

1. W programie PowerShell Zaloguj się do subskrypcji platformy Azure:

    ```
    Connect-AzureRmAccount
    $sub = Get-AzureRmSubscription -Name <SubscriptionName>
    $sub | Select-AzureRmSubscription
    ```

2. Aby przechowywać parametry, należy utworzyć zmienną złożone przy użyciu nazwy planu odzyskiwania:

    ```
    $VMDetails = @{"VMGUID"=@{"ResourceGroupName"="RGNameOfNSG";"NSGName"="NameOfNSG"};"VMGUID2"=@{"ResourceGroupName"="RGNameOfNSG";"NSGName"="NameOfNSG"}}
        New-AzureRmAutomationVariable -ResourceGroupName <RG of Automation Account> -AutomationAccountName <AA Name> -Name <RecoveryPlanName> -Value $VMDetails -Encrypted $false
    ```

3. W tej zmiennej złożone **VMDetails** jest identyfikator maszyny Wirtualnej dla chronionej maszyny Wirtualnej. Aby uzyskać identyfikator maszyny Wirtualnej w witrynie Azure portal, należy wyświetlić właściwości maszyny Wirtualnej. Poniższy zrzut ekranu przedstawia zmienną, która przechowuje szczegółowe informacje o dwóch maszyn wirtualnych:

    ![Użycie Identyfikatora maszyny Wirtualnej jako identyfikatora GUID](media/site-recovery-runbook-automation-new/vmguid.png)

4. Użyj tej zmiennej w elemencie runbook. Jeśli wskazany identyfikator GUID maszyny Wirtualnej znajduje się w ramach planu odzyskiwania, należy zastosować sieciową grupę zabezpieczeń na maszynie Wirtualnej:

    ```
    $VMDetailsObj = (Get-AutomationVariable -Name $RecoveryPlanContext.RecoveryPlanName).ToObject([hashtable])
    ```

4. W elemencie runbook w pętli poprzez maszyn wirtualnych w ramach planu odzyskiwania. Sprawdź, czy maszyna wirtualna istnieje w **$VMDetailsObj**. Jeśli istnieje, należy uzyskać dostęp do właściwości w zmiennej, aby zastosować sieciową grupę zabezpieczeń:

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

Ten sam skrypt służy do planów odzyskiwania inny. Dzięki przechowywaniu wartość, która odnosi się do planu odzyskiwania w różnych zmiennych, wprowadź różne parametry.

## <a name="sample-scripts"></a>Przykładowe skrypty

Aby wdrożyć przykładowe skrypty do konta usługi Automation, kliknij **Wdróż na platformie Azure** przycisku.

[![Wdrażanie na platformie Azure](https://azurecomcdn.azureedge.net/mediahandler/acomblog/media/Default/blog/c4803408-340e-49e3-9a1f-0ed3f689813d.png)](https://aka.ms/asr-automationrunbooks-deploy)

Inny przykład zobacz poniższy klip wideo. Pokazuje sposób odzyskiwania dwuwarstwowej aplikacji WordPress na platformie Azure:


> [!VIDEO https://channel9.msdn.com/Series/Azure-Site-Recovery/One-click-failover-of-a-2-tier-WordPress-application-using-Azure-Site-Recovery/player]


## <a name="additional-resources"></a>Dodatkowe zasoby
* [Usługa Uruchom jako konto usługi Azure Automation](../automation/automation-create-runas-account.md)
* [Omówienie usługi Azure Automation](https://msdn.microsoft.com/library/azure/dn643629.aspx "usługi Azure Automation — omówienie")
* [Usługa Azure Automation przykładowe skrypty](https://gallery.technet.microsoft.com/scriptcenter/site/search?f\[0\].Type=User&f\[0\].Value=SC%20Automation%20Product%20Team&f\[0\].Text=SC%20Automation%20Product%20Team "usługi Azure Automation przykładowe skrypty")

## <a name="next-steps"></a>Kolejne kroki
[Dowiedz się więcej](site-recovery-failover.md) o uruchamianiu przejścia w tryb failover.
