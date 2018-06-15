---
title: Dodaj element runbook usługi Automatyzacja Azure do planów odzyskiwania w usłudze Azure Site Recovery | Dokumentacja firmy Microsoft
description: Dowiedz się, jak usługi Azure Site Recovery można rozszerzyć planów odzyskiwania przy użyciu usługi Automatyzacja Azure. Dowiedz się, jak wykonać złożone zadania podczas odzyskiwania do platformy Azure.
services: site-recovery
documentationcenter: ''
author: ruturaj
manager: gauravd
editor: ''
ms.assetid: ecece14d-5f92-4596-bbaf-5204addb95c2
ms.service: site-recovery
ms.devlang: powershell
ms.tgt_pltfrm: na
ms.topic: article
ms.workload: storage-backup-recovery
ms.date: 05/02/2018
ms.author: ruturajd@microsoft.com
ms.openlocfilehash: a0a57e4a604a8cadedd5d715acf5f76a147da6d1
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/08/2018
ms.locfileid: "33896024"
---
# <a name="add-azure-automation-runbooks-to-recovery-plans"></a>Dodaj element runbook usługi Automatyzacja Azure do planów odzyskiwania
W tym artykule opisano sposób Azure Site Recovery integruje się z automatyzacji Azure w celu umożliwienie wydłużenia planów odzyskiwania. Plany odzyskiwania można organizować odzyskiwania maszyn wirtualnych, które są chronione za pomocą usługi Site Recovery. Plany odzyskiwania działają zarówno dla replikacji do chmury dodatkowej, jak i dla replikacji do platformy Azure. Plany odzyskiwania również sprawić, że odzyskiwania **spójnie dokładne**, **powtarzalne**, i **automatyczne**. Jeśli w trybie Failover maszyny wirtualne na platformie Azure, integracja z usługi Automatyzacja Azure rozszerza planów odzyskiwania. Służy on do wykonywania elementów runbook, które oferują zaawansowane automatyzacji zadań.

Jeśli jesteś nowym użytkownikiem usługi Automatyzacja Azure, możesz [Zarejestruj](https://azure.microsoft.com/services/automation/) i [Pobierz przykładowe skrypty](https://azure.microsoft.com/documentation/scripts/). Aby uzyskać więcej informacji, a także informacje na temat organizowania odzyskiwania na platformie Azure przy użyciu [planów odzyskiwania](./site-recovery-create-recovery-plans.md), zobacz [usługi Azure Site Recovery](https://azure.microsoft.com/services/site-recovery/).

W tym artykule opisano sposób integrowania elementu runbook usługi Automatyzacja Azure do planów odzyskiwania. Przykłady możemy użyć do zautomatyzowania podstawowe zadania, które wcześniej wymagały ręcznej interwencji. Opisano również sposób konwertowania odzyskiwania wieloetapowych do akcji odzyskiwania jednym kliknięciem.

## <a name="customize-the-recovery-plan"></a>Dostosowywanie planu odzyskiwania
1. Przejdź do **usługi Site Recovery** bloku zasobów planu odzyskiwania. Na przykład plan odzyskiwania obejmuje dwie maszyny wirtualne dodane do niego, do odzyskania. Aby rozpocząć dodawanie elementu runbook, kliknij przycisk **Dostosuj** kartę.

    ![Kliknij przycisk Dostosuj](media/site-recovery-runbook-automation-new/essentials-rp.png)


2. Kliknij prawym przyciskiem myszy **Grupa 1: Uruchom**, a następnie wybierz **post dodać akcję**.

    ![Kliknij prawym przyciskiem myszy Grupa 1: Uruchom i dodać post akcji](media/site-recovery-runbook-automation-new/customize-rp.png)

3. Kliknij przycisk **wybierz skrypt**.

4. Na **Akcja aktualizacji** bloku, nazwa skryptu **Hello World**.

    ![Blok akcji aktualizacji](media/site-recovery-runbook-automation-new/update-rp.png)

5. Wprowadź nazwę konta automatyzacji.
    >[!NOTE]
    > Konto automatyzacji można w dowolnym regionie Azure. Konto automatyzacji musi być w tej samej subskrypcji co magazyn usługi Azure Site Recovery.

6. Na Twoim koncie automatyzacji wybierz element runbook. Ten element runbook jest skryptu uruchamianego podczas wykonywania planu odzyskiwania po odzyskaniu pierwszą grupę.

7. Aby zapisać skrypt, kliknij przycisk **OK**. Skrypt jest dodawany do **Grupa 1: kroki po**.

    ![1:Start grupy po akcji](media/site-recovery-runbook-automation-new/addedscript-rp.PNG)


## <a name="considerations-for-adding-a-script"></a>Zagadnienia dotyczące Dodawanie skryptu

* Opcje **usunąć krok** lub **zaktualizować ten skrypt**, kliknij prawym przyciskiem myszy skrypt.
* Można uruchomić skryptu na platformie Azure w trybie failover z komputera lokalnego do platformy Azure. Ponadto można go uruchomić na platformie Azure jako skrypt lokacji podstawowej zamknięcia systemu, podczas powrotu po awarii z platformy Azure na maszynie lokalnej.
* Po uruchomieniu skryptu injects kontekstu planu odzyskiwania. W poniższym przykładzie przedstawiono zmiennej kontekstu:

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

    W poniższej tabeli wymieniono nazwy i opisu każdej zmiennej w kontekście.

    | **Nazwa zmiennej** | **Opis** |
    | --- | --- |
    | RecoveryPlanName |Nazwa planu uruchomione. Ta zmienna pomaga wykonać różne operacje, na podstawie nazwy planu odzyskiwania. Możesz również użyć ponownie skrypt. |
    | FailoverType |Określa, czy tryb failover jest test, planowane lub nieplanowane. |
    | Element FailoverDirection |Określa, czy do podstawowej lub dodatkowej lokacji odzyskiwania. |
    | Identyfikator grupy |Identyfikuje numer grupy w planie odzyskiwania, gdy działa planu. |
    | VmMap |Tablica wszystkich maszyn wirtualnych w grupie. |
    | Klucz VMMap |Unikatowy klucz (GUID) dla każdej maszyny Wirtualnej. Jest taki sam jak identyfikator Azure Virtual Machine Manager (VMM) maszyny wirtualnej, jeśli to możliwe. |
    | SubscriptionId |Identyfikator subskrypcji platformy Azure, w którym utworzono maszynę Wirtualną. |
    | RoleName |Nazwa maszyny Wirtualnej platformy Azure, która jest przywracana. |
    | CloudServiceName |Nazwa usługi chmury Azure, pod którym utworzono maszynę Wirtualną. |
    | ResourceGroupName|Nazwa grupy zasobów platformy Azure, w którym utworzono maszynę Wirtualną. |
    | RecoveryPointId|Znacznik czasu po odzyskaniu maszyna wirtualna. |

* Upewnij się, że konto usługi Automatyzacja ma następujących modułów:
    * AzureRM.profile
    * AzureRM.Resources
    * AzureRM.Automation
    * AzureRM.Network
    * AzureRM.Compute

Wszystkie moduły muszą być zgodne wersje. Prosty sposób sprawdzić, czy wszystkie moduły są zgodne ma używać najnowszej wersji wszystkich modułów.

### <a name="access-all-vms-of-the-vmmap-in-a-loop"></a>Dostęp do wszystkich maszyn wirtualnych z VMMap w pętli
Pętli na wszystkich maszynach wirtualnych z VMMap firmy Microsoft, należy użyć poniższego kodu:

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
> Grupy nazwa i rola Nazwa wartości zasobów są puste, skrypt po wstępnej akcji do grupy rozruchu. Wartości są wypełniane tylko wtedy, gdy maszyna wirtualna danej grupy powiedzie się w tryb failover. Skrypt jest akcją po grupy rozruchu.

## <a name="use-the-same-automation-runbook-in-multiple-recovery-plans"></a>Użyj tego samego elementu runbook usługi Automatyzacja w wielu planów odzyskiwania

Możesz użyć jednego skryptu w wielu planów odzyskiwania za pomocą zmiennych zewnętrznych. Można użyć [zmienne automatyzacji Azure](../automation/automation-variables.md) do przechowywania parametrów, które można przekazać do wykonania planu odzyskiwania. Dodając Nazwa planu odzyskiwania jako prefiksu do zmiennej, można utworzyć indywidualne zmienne dla każdego planu odzyskiwania. Następnie należy użyć zmiennych jako parametry. Można zmienić parametru bez zmiany skryptu, ale nadal zmienić sposób działania skryptu.

### <a name="use-a-simple-string-variable-in-a-runbook-script"></a>Użyj zmiennej prostego ciągu w skrypcie elementu runbook

W tym przykładzie skrypt pobiera dane wejściowe z grupy zabezpieczeń sieci (NSG) i stosuje je do maszyn wirtualnych z planu odzyskiwania.

Dla skryptu do wykrywania odzyskiwania, które działa planu, użyj kontekstu planu odzyskiwania:

```
workflow AddPublicIPAndNSG {
    param (
          [parameter(Mandatory=$false)]
          [Object]$RecoveryPlanContext
    )

    $RPName = $RecoveryPlanContext.RecoveryPlanName
```

Aby zastosować istniejącej grupy NSG, musisz znać nazwę grupy NSG oraz nazwę grupy zasobów NSG. Użyj tych zmiennych jako dane wejściowe dla skryptów planu odzyskiwania. Aby to zrobić, należy utworzyć dwie zmienne w zasoby konta automatyzacji. Dodaj nazwę plan odzyskiwania, który tworzysz parametry jako prefiksu nazwy zmiennej.

1. Utwórz zmienną do przechowywania nazwy grupy NSG. Przy użyciu nazwy planu odzyskiwania, Dodaj prefiks do nazwy zmiennej.

    ![Utwórz zmienną Nazwa grupy NSG](media/site-recovery-runbook-automation-new/var1.png)

2. Utwórz zmienną do przechowywania nazwy grupy zasobów w grupie NSG. Przy użyciu nazwy planu odzyskiwania, Dodaj prefiks do nazwy zmiennej.

    ![Tworzenie grupy NSG Nazwa grupy zasobów](media/site-recovery-runbook-automation-new/var2.png)


3.  W skrypcie należy użyć poniższego kodu odwołania można pobrać wartości zmiennych:

    ```
    $NSGValue = $RecoveryPlanContext.RecoveryPlanName + "-NSG"
    $NSGRGValue = $RecoveryPlanContext.RecoveryPlanName + "-NSGRG"

    $NSGnameVar = Get-AutomationVariable -Name $NSGValue
    $RGnameVar = Get-AutomationVariable -Name $NSGRGValue
    ```

4.  Użyj zmiennych w elemencie runbook, aby zastosować grupy NSG do interfejsu sieciowego przełączona w tryb failover maszyny wirtualnej:

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

Dla każdego planu odzyskiwania należy utworzyć zmienne niezależne, dzięki czemu można ponownie użyć skryptu. Dodaj prefiks przy użyciu nazwy planu odzyskiwania. Pełne, end-to-end skryptu dla tego scenariusza, zobacz [Dodawanie publicznych adresów IP i grupy NSG do maszyn wirtualnych podczas testowania trybu failover planu odzyskiwania usługi Site Recovery](https://gallery.technet.microsoft.com/Add-Public-IP-and-NSG-to-a6bb8fee).


### <a name="use-a-complex-variable-to-store-more-information"></a>Użyj zmiennej złożone do przechowywania więcej informacji

Rozważmy scenariusz, w którym ma zostać jednego skryptu, aby włączyć publicznego adresu IP na określonych maszynach wirtualnych. W innym scenariuszu możesz zastosować różne grupy NSG na różnych maszynach wirtualnych (a nie na wszystkich maszynach wirtualnych). Możesz wprowadzić skrypt, który jest wielokrotnego użytku dla każdego planu odzyskiwania. Każdy plan odzyskiwania może mieć zmiennej liczbę maszyn wirtualnych. Na przykład odzyskiwania SharePoint, ma dwa końce frontonu. Aplikacja podstawowe — biznesowych (LOB) ma tylko jeden frontonu. Nie można utworzyć oddzielne zmienne dla każdego planu odzyskiwania.

W poniższym przykładzie, możemy użyć nowe techniki i Utwórz [zmiennej złożone](https://msdn.microsoft.com/library/dn913767.aspx?f=255&MSPPError=-2147217396) w zasobów konta usługi Automatyzacja Azure. W tym celu określania wielu wartości. Przy użyciu programu Azure PowerShell musi wykonać następujące czynności:

1. W programie PowerShell należy zalogować się do subskrypcji platformy Azure:

    ```
    Connect-AzureRmAccount
    $sub = Get-AzureRmSubscription -Name <SubscriptionName>
    $sub | Select-AzureRmSubscription
    ```

2. Aby zapisać parametry, Utwórz zmienną złożone przy użyciu nazwy planu odzyskiwania:

    ```
    $VMDetails = @{"VMGUID"=@{"ResourceGroupName"="RGNameOfNSG";"NSGName"="NameOfNSG"};"VMGUID2"=@{"ResourceGroupName"="RGNameOfNSG";"NSGName"="NameOfNSG"}}
        New-AzureRmAutomationVariable -ResourceGroupName <RG of Automation Account> -AutomationAccountName <AA Name> -Name <RecoveryPlanName> -Value $VMDetails -Encrypted $false
    ```

3. W tej zmiennej złożone **VMDetails** jest identyfikator maszyny Wirtualnej chronionej maszyny wirtualnej. Aby uzyskać identyfikator maszyny Wirtualnej w portalu Azure, należy wyświetlić właściwości maszyny Wirtualnej. Poniższy zrzut ekranu przedstawia zmiennej, która przechowuje dane dwóch maszyn wirtualnych:

    ![Użycie Identyfikatora VM jako identyfikator GUID](media/site-recovery-runbook-automation-new/vmguid.png)

4. Użyj tej zmiennej w elemencie runbook. Jeśli wskazany identyfikator GUID maszyny Wirtualnej został odnaleziony w kontekście planu odzyskiwania, należy zastosować grupy NSG na maszynie Wirtualnej:

    ```
    $VMDetailsObj = Get-AutomationVariable -Name $RecoveryPlanContext.RecoveryPlanName
    ```

4. W elemencie runbook pętli maszyn wirtualnych z kontekstu planu odzyskiwania. Sprawdź, czy maszyna wirtualna istnieje w **$VMDetailsObj**. Jeśli istnieje, należy mieć dostęp do właściwości zmiennej, aby zastosować grupy NSG:

    ```
        $VMinfo = $RecoveryPlanContext.VmMap | Get-Member | Where-Object MemberType -EQ NoteProperty | select -ExpandProperty Name
        $vmMap = $RecoveryPlanContext.VmMap

        foreach($VMID in $VMinfo) {
            Write-output $VMDetailsObj.value.$VMID

            if ($VMDetailsObj.value.$VMID -ne $Null) { #If the VM exists in the context, this will not b Null
                $VM = $vmMap.$VMID
                # Access the properties of the variable
                $NSGname = $VMDetailsObj.value.$VMID.'NSGName'
                $NSGRGname = $VMDetailsObj.value.$VMID.'NSGResourceGroupName'

                # Add code to apply the NSG properties to the VM
            }
        }
    ```

Możesz użyć tego samego skryptu dla planów odzyskiwania inny. Wprowadź różne parametry, przechowując wartość, która odpowiada planu odzyskiwania w różnych zmiennych.

## <a name="sample-scripts"></a>Przykładowe skrypty

Aby wdrożyć przykładowe skrypty Twoje konto usługi Automatyzacja, kliknij przycisk **wdrażanie na platformie Azure** przycisku.

[![Wdrażanie na platformie Azure](https://azurecomcdn.azureedge.net/mediahandler/acomblog/media/Default/blog/c4803408-340e-49e3-9a1f-0ed3f689813d.png)](https://aka.ms/asr-automationrunbooks-deploy)

Na przykład innego zobacz poniższe wideo. Go pokazano, jak odzyskać dwuwarstwowej aplikacji WordPress na platformie Azure:


> [!VIDEO https://channel9.msdn.com/Series/Azure-Site-Recovery/One-click-failover-of-a-2-tier-WordPress-application-using-Azure-Site-Recovery/player]


## <a name="additional-resources"></a>Zasoby dodatkowe
* [Azure automatyzacji usługi konta Uruchom jako](../automation/automation-create-runas-account.md)
* [Omówienie usługi Azure Automation](http://msdn.microsoft.com/library/azure/dn643629.aspx "Omówienie usługi Automatyzacja Azure")
* [Azure Automation przykładowe skrypty](http://gallery.technet.microsoft.com/scriptcenter/site/search?f\[0\].Type=User&f\[0\].Value=SC%20Automation%20Product%20Team&f\[0\].Text=SC%20Automation%20Product%20Team "usługi Automatyzacja Azure przykładowe skrypty")

## <a name="next-steps"></a>Kolejne kroki
[Dowiedz się więcej](site-recovery-failover.md) dotyczące uruchamiania trybu failover.
