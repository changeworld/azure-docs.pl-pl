---
title: Red Hat Enterprise Linux Przenieś własne obrazy platformy Azure do własnych subskrypcji | Microsoft Docs
description: Dowiedz się więcej na temat przenoszenia własnych obrazów subskrypcji dla Red Hat Enterprise Linux na platformie Azure
services: virtual-machines-linux
documentationcenter: ''
author: asinn826
manager: BorisB2015
editor: ''
ms.assetid: f495f1b4-ae24-46b9-8d26-c617ce3daf3a
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 1/14/2020
ms.author: alsin
ms.openlocfilehash: afda502bcd89423ecdd008c0297c85dd8a5b61fb
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/04/2020
ms.locfileid: "76989845"
---
# <a name="red-hat-enterprise-linux-bring-your-own-subscription-gold-images-in-azure"></a>Red Hat Enterprise Linux Przenieś własne obrazy z subskrypcji na platformę Azure

Obrazy Red Hat Enterprise Linux (RHEL) są dostępne na platformie Azure za pośrednictwem modelu "płatność zgodnie z rzeczywistym użyciem" (PAYG) lub "Przenieś własną subskrypcję" (Red Hat Gold Image). Ten dokument zawiera omówienie obrazów Red Hat Gold na platformie Azure.

## <a name="important-points-to-consider"></a>Ważne kwestie, które należy wziąć pod uwagę

- Obrazy Red Hat Gold dostępne w tym programie to obrazy RHEL gotowe do produkcji podobne do obrazów RHEL PAYG w galerii platformy Azure/witrynie Marketplace.

- Obrazy są zgodne z naszymi bieżącymi zasadami opisanymi w [Red Hat Enterprise Linux obrazów na platformie Azure](./redhat-images.md)

- Standardowe zasady pomocy technicznej mają zastosowanie do maszyn wirtualnych utworzonych na podstawie tych obrazów

- Maszyny wirtualne udostępnione z obrazów Red Hat Gold nie mają opłat RHEL związanych z obrazami RHEL PAYG

- Obrazy są nieuprawnione, dlatego należy użyć Menedżera subskrypcji do zarejestrowania i subskrybowania maszyn wirtualnych, aby uzyskać aktualizacje z rozwiązania Red Hat bezpośrednio

- Nie jest obecnie możliwe przełączenie dynamicznie między modelami rozliczeń BYOS i PAYG dla obrazów systemu Linux. Ponowne wdrożenie maszyny wirtualnej z odpowiedniego obrazu jest wymagane do przełączenia modelu rozliczania

## <a name="requirements-and-conditions-to-access-the-red-hat-gold-images"></a>Wymagania i warunki dostępu do obrazów Red Hat Gold

1. Zapoznaj się z postanowieniami [programu Red Hat Cloud Access](https://www.redhat.com/en/technologies/cloud-computing/cloud-access) , a następnie Włącz subskrypcje Red Hat dla dostępu do chmury w programie [Red Hat Subscription Manager](https://access.redhat.com/management/cloud). Musisz mieć dostęp do subskrypcji platformy Azure, które mają zostać zarejestrowane na potrzeby dostępu do chmury.

1. Jeśli włączono subskrypcje Red Hat dla dostępu w chmurze, które spełniają odpowiednie wymagania dotyczące uprawnień, Twoje subskrypcje platformy Azure zostaną automatycznie włączone dla dostępu z obrazu Gold.

### <a name="expected-time-for-image-access"></a>Oczekiwany czas dostępu do obrazu

Po zakończeniu czynności związanych z włączeniem dostępu do chmury Red Hat sprawdzi Twoje uprawnienia do obrazów Red Hat Gold. Jeśli sprawdzanie poprawności zakończy się pomyślnie, w ciągu trzech godzin uzyskasz dostęp do obrazów Gold.

## <a name="use-the-red-hat-gold-images-from-the-azure-portal"></a>Użyj obrazów Red Hat Gold z Azure Portal

1. Po otrzymaniu przez subskrypcję platformy Azure dostępu do obrazów Red Hat Gold można je zlokalizować w [Azure Portal](https://portal.azure.com) , przechodząc do sekcji **Tworzenie zasobu** , a następnie klikając pozycję **wszystkie**.

1. W górnej części strony zobaczysz, że masz prywatne oferty.

    ![Oferty prywatne w witrynie Marketplace](./media/rhel-byos-privateoffers.png)

1. Możesz kliknąć link purpurowy lub przewinąć w dół do dolnej części strony, aby wyświetlić oferty prywatne.

1. Dalsze Inicjowanie obsługi w interfejsie użytkownika nie będzie inne niż w żadnym innym istniejącym obrazie Red Hat. Wybierz wersję RHEL i postępuj zgodnie z monitami, aby zainicjować obsługę administracyjną maszyny wirtualnej. Ten proces umożliwia również zaakceptowanie warunków obrazu w ostatnim kroku.

>[!NOTE]
>Te kroki do tej pory nie spowodują włączenia obrazu z systemem Red Hat Gold na potrzeby wdrażania programistycznego — należy wykonać dodatkowy krok zgodnie z opisem w sekcji "dodatkowe informacje" poniżej.

Pozostała część tego dokumentu koncentruje się na metodzie interfejsu wiersza polecenia w celu aprowizacji i akceptowania warunków na obrazie. Interfejs użytkownika i interfejsu wiersza polecenia są w pełni wymienne w miarę jak końcowy wynik (zainicjowana RHEL Gold Image VM).

## <a name="use-the-red-hat-gold-images-from-the-azure-cli"></a>Korzystanie z obrazów Red Hat Gold z poziomu interfejsu wiersza polecenia platformy Azure
Poniższy zestaw instrukcji przeprowadzi Cię przez proces wdrażania wstępnego dla maszyny wirtualnej RHEL przy użyciu interfejsu wiersza polecenia platformy Azure. W tych instrukcjach przyjęto założenie, że masz [zainstalowany interfejs wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/install-azure-cli).

>[!IMPORTANT]
>Upewnij się, że używasz wszystkich małych liter w odwołaniach do wydawcy, oferty, planu i obrazu dla wszystkich następujących poleceń

1. Sprawdź, czy jesteś w żądanej subskrypcji:
    ```azurecli
    az account show -o=json
    ```

1. Utwórz grupę zasobów dla maszyny wirtualnej z systemem Red Hat Gold Image:
    ```azurecli
    az group create --name <name> --location <location>
    ```

1. Zaakceptuj warunki obrazu:
    ```azurecli
    az vm image terms accept --publisher redhat --offer rhel-byos --plan <SKU value here> -o=jsonc

    # Example:
    az vm image terms accept --publisher redhat --offer rhel-byos --plan rhel-lvm75 -o=jsonc

    OR

    az vm image terms accept --urn RedHat:rhel-byos:rhel-lvm8:8.0.20190620
    ```
    >[!NOTE]
    >Te postanowienia muszą zostać zaakceptowane *raz dla każdej subskrypcji platformy Azure, dla jednostki SKU obrazu*.

1. Obowiązkowe Sprawdź poprawność wdrożenia maszyny wirtualnej za pomocą następującego polecenia:
    ```azurecli
    az vm create -n <VM name> -g <resource group name> --image <image urn> --validate

    # Example:
    az vm create -n rhel-byos-vm -g rhel-byos-group --image RedHat:rhel-byos:rhel-lvm75:7.5.20190620
    ```

1. Inicjowanie obsługi administracyjnej maszyny wirtualnej przez uruchomienie tego samego polecenia jak powyżej bez argumentu `--validate`:
    ```azurecli
    az vm create -n <VM name> -g <resource group name> --image <image urn> --validate
    ```

1. Użyj protokołu SSH do maszyny wirtualnej i sprawdź, czy masz nieuprawniony obraz. W tym celu należy uruchomić `sudo yum repolist` (w przypadku używania `sudo dnf repolist`RHEL 8). W danych wyjściowych zostanie wyświetlony zapytanie o użycie Menedżera subskrypcji w celu zarejestrowania maszyny wirtualnej przy użyciu rozwiązania Red Hat.

>[!NOTE]
>W RHEL 8 `dnf` i `yum` są zamienne. więcej informacji na ten temat znajduje się w [podręczniku administratora RHEL 8](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/8/html/configuring_basic_system_settings/installing-software-with-yum_configuring-basic-system-settings).


## <a name="use-the-red-hat-gold-images-from-powershell"></a>Korzystanie z obrazów Red Hat Gold z programu PowerShell
Poniżej znajduje się przykładowy skrypt. Należy zastąpić grupę zasobów, lokalizację, nazwę maszyny wirtualnej, informacje o logowaniu i inne zmienne z wybraną konfiguracją. Informacje o wydawcy i planie muszą być pisane małymi literami.

```powershell-interactive
    # Variables for common values
    $resourceGroup = "testbyos"
    $location = "canadaeast"
    $vmName = "test01"

    # Define user name and blank password
    $securePassword = ConvertTo-SecureString 'TestPassword1!' -AsPlainText -Force
    $cred = New-Object System.Management.Automation.PSCredential("azureuser",$securePassword)
    Get-AzureRmMarketplaceTerms -Publisher RedHat -Product rhel-byos -Name rhel-lvm75 | SetAzureRmMarketplaceTerms -Accept

    # Create a resource group
    New-AzureRmResourceGroup -Name $resourceGroup -Location $location

    # Create a subnet configuration
    $subnetConfig = New-AzureRmVirtualNetworkSubnetConfig -Name mySubnet -AddressPrefix 192.168.1.0/24

    # Create a virtual network
    $vnet = New-AzureRmVirtualNetwork -ResourceGroupName $resourceGroup -Location
    $location `-Name MYvNET -AddressPrefix 192.168.0.0/16 -Subnet $subnetConfig

    # Create a public IP address and specify a DNS name
    $pip = New-AzureRmPublicIpAddress -ResourceGroupName $resourceGroup -Location
    $location `-Name "mypublicdns$(Get-Random)" -AllocationMethod Static -IdleTimeoutInMinutes 4

    # Create an inbound network security group rule for port 22
    $nsgRuleSSH = New-AzureRmNetworkSecurityRuleConfig -Name
    myNetworkSecurityGroupRuleSSH -Protocol Tcp `
    -Direction Inbound -Priority 1000 -SourceAddressPrefix * -SourcePortRange * -
    DestinationAddressPrefix * `-DestinationPortRange 22 -Access Allow

    # Create a network security group
    $nsg = New-AzureRmNetworkSecurityGroup -ResourceGroupName $resourceGroup -Location
    $location `-Name myNetworkSecurityGroup -SecurityRules $nsgRuleSSH

    # Create a virtual network card and associate with public IP address and NSG
    $nic = New-AzureRmNetworkInterface -Name myNic -ResourceGroupName $resourceGroup -
    Location $location `-SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id -NetworkSecurityGroupId $nsg.Id

    # Create a virtual machine configuration
    $vmConfig = New-AzureRmVMConfig -VMName $vmName -VMSize Standard_D3_v2 |
    Set-AzureRmVMOperatingSystem -Linux -ComputerName $vmName -Credential $cred |
    Set-AzureRmVMSourceImage -PublisherName redhat -Offer rhel-byos -Skus rhel-lvm75 -Version latest | Add-     AzureRmVMNetworkInterface -Id $nic.Id
    Set-AzureRmVMPlan -VM $vmConfig -Publisher redhat -Product rhel-byos -Name "rhel-lvm75"

    # Configure SSH Keys
    $sshPublicKey = Get-Content "$env:USERPROFILE\.ssh\id_rsa.pub"
    Add-AzureRmVMSshPublicKey -VM $vmconfig -KeyData $sshPublicKey -Path "/home/azureuser/.ssh/authorized_keys"

    # Create a virtual machine
    New-AzureRmVM -ResourceGroupName $resourceGroup -Location $location -VM $vmConfig
```

## <a name="encrypt-red-hat-enterprise-linux-bring-your-own-subscription-gold-images"></a>Szyfrowanie Red Hat Enterprise Linux dołączenia do własnych złotych obrazów subskrypcji

Red Hat Enterprise Linux można zabezpieczyć obrazy ze stosu do własnych subskrypcji, korzystając z [Azure Disk Encryption](../../linux/disk-encryption-overview.md). Jednak przed włączeniem szyfrowania **należy** zarejestrować subskrypcję.  Szczegółowe informacje na temat rejestrowania obrazu RHEL BYOS Gold są dostępne w witrynie Red Hat. Zobacz [, jak zarejestrować i subskrybować system w portalu Red Hat Customer przy użyciu usługi Red Hat Subscription-Manager](https://access.redhat.com/solutions/253273). Jeśli masz aktywną subskrypcję Red Hat, możesz również przeczytać temat [Tworzenie kluczy aktywacji w portalu Red Hat Customer Portal](https://access.redhat.com/articles/1378093).

Azure Disk Encryption nie są obsługiwane w [obrazach niestandardowych Red Hat](/linux/redhat-create-upload-vhd). Dodatkowe wymagania dotyczące programu ADE i wymagania wstępne są udokumentowane w [Azure Disk Encryption dla maszyn wirtualnych z systemem Linux](../../linux/disk-encryption-overview.md#additional-vm-requirements).

Kroki dotyczące stosowania Azure Disk Encryption są dostępne w [Azure Disk Encryption scenariuszach na maszynach wirtualnych z systemem Linux i w](../../linux/disk-encryption-linux.md) powiązanych artykułach.  

## <a name="additional-information"></a>Dodatkowe informacje

- Jeśli spróbujesz zainicjować obsługę administracyjną maszyny wirtualnej w ramach subskrypcji, która nie jest włączona dla tej oferty, zostanie wyświetlony następujący błąd:

    ```
    "Offer with PublisherId: redhat, OfferId: rhel-byos, PlanId: rhel-lvm75 is private and can not be purchased by subscriptionId: GUID"
    ```
    
    W takim przypadku skontaktuj się z firmą Microsoft lub Red Hat, aby włączyć subskrypcję.

- Jeśli zmodyfikujesz migawkę z obrazu RHEL BYOS i spróbujesz opublikować ten obraz niestandardowy w [galerii obrazów udostępnionych](https://docs.microsoft.com/azure/virtual-machines/linux/shared-image-galleries), musisz podać informacje o planie zgodne z oryginalnym źródłem migawki. Na przykład polecenie może wyglądać następująco:

    ```azurecli
    az vm create –image \
    "/subscriptions/GUID/resourceGroups/GroupName/providers/Microsoft.Compute/galleries/GalleryName/images/ImageName/versions/1.0.0" \
    -g AnotherGroupName --location EastUS2 -n VMName \
    --plan-publisher redhat --plan-product rhel-byos --plan-name rhel-lvm75
    ```
    Zanotuj parametry planu w ostatnim wierszu powyżej.

    [Azure Disk Encryption](#encrypt-red-hat-enterprise-linux-bring-your-own-subscription-gold-images) nie są obsługiwane w obrazach niestandardowych.

- Jeśli używasz automatyzacji do inicjowania obsługi maszyn wirtualnych z obrazów RHEL BYOS, musisz dostarczyć parametry planu podobne do tego, co zostało pokazane powyżej. Na przykład jeśli używasz Terraform, podaj informacje o planie w [bloku planu](https://www.terraform.io/docs/providers/azurerm/r/virtual_machine.html#plan).

## <a name="next-steps"></a>Następne kroki
- Przewodnik krok po kroku i szczegółowe informacje o programie dla dostępu do chmury są dostępne w [dokumentacji usługi Red Hat Cloud Access.](https://access.redhat.com/documentation/en-us/red_hat_subscription_management/1/html/red_hat_cloud_access_reference_guide/index)
- Dowiedz się więcej o [infrastrukturze aktualizacji usługi Azure Red Hat](./redhat-rhui.md).
- Aby dowiedzieć się więcej na temat wszystkich obrazów Red Hat na platformie Azure, przejdź do [strony dokumentacji](./redhat-images.md).
- Informacje o zasadach obsługi systemu Red Hat dla wszystkich wersji programu RHEL można znaleźć na stronie [cykl życia Red Hat Enterprise Linux](https://access.redhat.com/support/policy/updates/errata) .
- Dodatkową dokumentację dotyczącą obrazów RHEL Gold można znaleźć w [dokumentacji Red Hat](https://access.redhat.com/documentation/en-us/red_hat_subscription_management/1/html/red_hat_cloud_access_reference_guide/using_red_hat_gold_images#con-gold-image-azure).
