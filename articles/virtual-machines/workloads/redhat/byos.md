---
title: Obrazy platformy Azure z systemem Red Hat Enterprise Linux bring-your-own-subscription | Dokumenty firmy Microsoft
description: Dowiedz się więcej o obrazach subskrypcji typu bring-your-own dla systemu Red Hat Enterprise Linux na platformie Azure.
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
ms.date: 02/10/2020
ms.author: alsin
ms.openlocfilehash: 9ab578b4b688c02c9150dfb23fce53fbb82df405
ms.sourcegitcommit: 530e2d56fc3b91c520d3714a7fe4e8e0b75480c8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/14/2020
ms.locfileid: "81273175"
---
# <a name="red-hat-enterprise-linux-bring-your-own-subscription-gold-images-in-azure"></a>Red Hat Enterprise Linux bring-your-own-subscription Gold Images na platformie Azure

Obrazy Red Hat Enterprise Linux (RHEL) są dostępne na platformie Azure za pośrednictwem modelu płatności zgodnie z rzeczywistym lub płatnej subskrypcji (BYOS) (Red Hat Gold Image). Ten artykuł zawiera omówienie obrazów Red Hat Gold na platformie Azure.

>[!NOTE]
> Obrazy RHEL BYOS Gold są dostępne w chmurach publicznych platformy Azure (komercyjnych) i azure dla instytucji rządowych. Nie są one dostępne w chinach platformy Azure ani w chmurach usługi Azure Blackforest.

## <a name="important-points-to-consider"></a>Ważne kwestie, które należy wziąć pod uwagę

- Obrazy Red Hat Gold przedstawione w tym programie są gotowymi do użycia w produkcji obrazami RHEL podobnymi do obrazów rhel pay-as-you-go w portalu Azure Marketplace.
- Obrazy są zgodne z bieżącymi zasadami opisanymi w [obrazach systemu Linux red hat enterprise na platformie Azure](./redhat-images.md).
- Standardowe zasady pomocy technicznej mają zastosowanie do maszyn wirtualnych utworzonych na podstawie tych obrazów.
- Maszyny wirtualne aprowiowane z Red Hat Gold Images nie zawierają opłat RHEL związanych z obrazami rhel pay-as-you-go.
- Obrazy są bez tytułu. Musisz użyć Red Hat Subscription-Manager do rejestracji i subskrybowania maszyn wirtualnych, aby uzyskać aktualizacje z Red Hat bezpośrednio.
- Obecnie nie jest możliwe dynamiczne przełączanie między modelami rozliczeniowymi BYOS i pay-as-you-go dla obrazów Linuksa. Aby przełączyć model rozliczeń, należy ponownie wdrożyć maszynę wirtualną z odpowiedniego obrazu.

>[!NOTE]
> Obrazy RHEL BYOS generacji 2 nie są obecnie dostępne za pośrednictwem oferty marketplace. Jeśli potrzebujesz obrazu RHEL BYOS generacji 2, odwiedź pulpit nawigacyjny dostępu do chmury w zarządzaniu subskrypcjami Red Hat. Aby uzyskać więcej informacji, zobacz [dokumentację Red Hat](https://access.redhat.com/articles/4847681).

## <a name="requirements-and-conditions-to-access-the-red-hat-gold-images"></a>Wymagania i warunki dostępu do obrazów Red Hat Gold

1. Zapoznaj się z warunkami [programu Red Hat Cloud Access.](https://www.redhat.com/en/technologies/cloud-computing/cloud-access) Włącz subskrypcje Red Hat dla cloud access w [Red Hat Subscription-Manager](https://access.redhat.com/management/cloud). Musisz mieć pod ręką subskrypcje platformy Azure, które będą rejestrowane dla programu Cloud Access.

1. Jeśli subskrypcje Red Hat włączone dla usługi Cloud Access spełniają wymagania dotyczące uprawnień, subskrypcje platformy Azure są automatycznie włączane dla dostępu do obrazu Gold.

### <a name="expected-time-for-image-access"></a>Oczekiwany czas dostępu do obrazu

Po zakończeniu kroków włączania dostępu do chmury Red Hat sprawdza twoje uprawnienia do obrazów Red Hat Gold. Jeśli weryfikacja zakończy się pomyślnie, otrzymasz dostęp do złotych obrazów w ciągu trzech godzin.

## <a name="use-the-red-hat-gold-images-from-the-azure-portal"></a>Korzystanie z obrazów Red Hat Gold z witryny Azure portal

1. Po otrzymaniu przez subskrypcję platformy Azure dostępu do obrazów Red Hat Gold można je zlokalizować w [witrynie Azure portal](https://portal.azure.com). Przejdź do **tworzenia zasobu** > **Zobacz wszystkie**.

1. W górnej części strony zobaczysz, że masz prywatne oferty.

    ![Prywatne oferty na rynku](./media/rhel-byos-privateoffers.png)

1. Wybierz fioletowy link lub przewiń w dół do dołu strony, aby wyświetlić oferty prywatne.

1. Reszta inicjowania obsługi administracyjnej w interfejsie użytkownika nie różni się od innych istniejących obrazów Red Hat. Wybierz wersję RHEL i postępuj zgodnie z instrukcjami, aby aprowizować maszynę wirtualną. Ten proces pozwala również zaakceptować warunki obrazu w ostatnim kroku.

>[!NOTE]
>Te kroki do tej pory nie włączają czerwonego obrazu złota dla wdrożenia programowego. Dodatkowy krok jest wymagany zgodnie z opisem w sekcji "Dodatkowe informacje".

Reszta tego dokumentu koncentruje się na metodzie interfejsu wiersza polecenia, aby aprowizować i akceptować warunki na obrazie. Interfejs użytkownika i interfejs wiersza polecenia są w pełni wymienne, jeśli chodzi o wynik końcowy (aprowizowana maszyna wirtualna RHEL Gold Image).

## <a name="use-the-red-hat-gold-images-from-the-azure-cli"></a>Użyj obrazów Red Hat Gold z interfejsu wiersza polecenia platformy Azure

Poniższe instrukcje przebiegają przez proces wdrażania początkowego maszyny Wirtualnej RHEL przy użyciu interfejsu wiersza polecenia platformy Azure. W tych instrukcjach założono, że masz [zainstalowany wiersz polecenia platformy Azure.](https://docs.microsoft.com/cli/azure/install-azure-cli)

>[!IMPORTANT]
>Upewnij się, że używasz wszystkich małych liter w wydawcy, oferta, plan i odniesienia do obrazu dla wszystkich następujących poleceń.

1. Sprawdź, czy jesteś w żądanej subskrypcji.

    ```azurecli
    az account show -o=json
    ```

1. Utwórz grupę zasobów dla maszyny Wirtualnej złocią red hat.

    ```azurecli
    az group create --name <name> --location <location>
    ```

1. Zaakceptuj warunki obrazu.

    ```azurecli
    az vm image terms accept --publisher redhat --offer rhel-byos --plan <SKU value here> -o=jsonc

    # Example:
    az vm image terms accept --publisher redhat --offer rhel-byos --plan rhel-lvm75 -o=jsonc

    OR

    az vm image terms accept --urn RedHat:rhel-byos:rhel-lvm8:8.0.20190620
    ```

    >[!NOTE]
    >Warunki te muszą być akceptowane *raz na subskrypcję platformy Azure, na jednostkę SKU obrazu*.

1. (Opcjonalnie) Sprawdź poprawność wdrożenia maszyny Wirtualnej za pomocą następującego polecenia:

    ```azurecli
    az vm create -n <VM name> -g <resource group name> --image <image urn> --validate

    # Example:
    az vm create -n rhel-byos-vm -g rhel-byos-group --image RedHat:rhel-byos:rhel-lvm75:7.5.20190620
    ```

1. Aprowizuj maszynę wirtualną, uruchamiając to `--validate` samo polecenie, które pokazano w poprzednim przykładzie bez argumentu.

    ```azurecli
    az vm create -n <VM name> -g <resource group name> --image <image urn> --validate
    ```

1. SSH do maszyny Wirtualnej i sprawdź, czy masz obraz bez tytułu. Aby wykonać ten `sudo yum repolist`krok, uruchom plik . W przypadku RHEL `sudo dnf repolist`8 należy użyć . Dane wyjściowe prosi o użycie Menedżera subskrypcji do zarejestrowania maszyny Wirtualnej z Red Hat.

>[!NOTE]
>Na RHEL `dnf` 8 `yum` i są wymienne. Aby uzyskać więcej informacji, zobacz [RHEL 8 admin guide](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/8/html/packaging_and_distributing_software/index).

## <a name="use-the-red-hat-gold-images-from-powershell"></a>Użyj obrazów Red Hat Gold z programu PowerShell

Poniższy skrypt jest przykładem. Zastąp wybraną konfigurację grupy zasobów, lokalizacji, nazwy maszyny Wirtualnej, danych logowania i innych zmiennych. Informacje o wydawcy i planie muszą być małe.

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

## <a name="encrypt-red-hat-enterprise-linux-bring-your-own-subscription-gold-images"></a>Szyfruj Red Hat Enterprise Linux bring-your-own-subscription Gold Images

Red Hat Enterprise Linux BYOS Gold Obrazy mogą być zabezpieczone za pomocą [usługi Azure Disk Encryption](../../linux/disk-encryption-overview.md). Subskrypcja *musi* być zarejestrowana, zanim będzie można włączyć szyfrowanie. Aby uzyskać więcej informacji na temat rejestrowania obrazu RHEL BYOS Gold Image, zobacz [Jak zarejestrować i zasubskrybować system na portalu klienta Red Hat za pomocą Red Hat Subscription-Manager](https://access.redhat.com/solutions/253273). Jeśli masz aktywną subskrypcję Red Hat, możesz również przeczytać [Tworzenie kluczy aktywacyjnych portalu klienta Red Hat.](https://access.redhat.com/articles/1378093)

Szyfrowanie dysków platformy Azure nie jest obsługiwane w [obrazach niestandardowych Red Hat](../../linux/redhat-create-upload-vhd.md). Dodatkowe wymagania i wymagania wstępne dotyczące szyfrowania dysków platformy Azure są udokumentowane w [systemie szyfrowania dysków platformy Azure dla maszyn wirtualnych z systemem Linux.](../../linux/disk-encryption-overview.md#additional-vm-requirements)

Aby uzyskać kroki dotyczące stosowania szyfrowania dysków platformy Azure, zobacz [scenariusze szyfrowania dysków platformy Azure na maszynach wirtualnych z systemem Linux](../../linux/disk-encryption-linux.md) i powiązanych artykułach.

## <a name="additional-information"></a>Dodatkowe informacje

- Jeśli spróbujesz aprowizować maszynę wirtualną w ramach subskrypcji, która nie jest włączona dla tej oferty, zostanie wyświetlony następujący komunikat:

    ```
    "Offer with PublisherId: redhat, OfferId: rhel-byos, PlanId: rhel-lvm75 is private and can not be purchased by subscriptionId: GUID"
    ```

    W takim przypadku skontaktuj się z firmą Microsoft lub Red Hat, aby włączyć subskrypcję.

- Jeśli zmodyfikujesz migawkę z obrazu RHEL BYOS i spróbujesz opublikować ten obraz niestandardowy w [Galerii obrazów udostępnionych,](https://docs.microsoft.com/azure/virtual-machines/linux/shared-image-galleries)musisz podać informacje o planie zgodne z oryginalnym źródłem migawki. Na przykład polecenie może wyglądać następująco:

    ```azurecli
    az vm create –image \
    "/subscriptions/GUID/resourceGroups/GroupName/providers/Microsoft.Compute/galleries/GalleryName/images/ImageName/versions/1.0.0" \
    -g AnotherGroupName --location EastUS2 -n VMName \
    --plan-publisher redhat --plan-product rhel-byos --plan-name rhel-lvm75
    ```

    Zwróć uwagę na parametry planu w wierszu końcowym.

    [Szyfrowanie dysków platformy Azure](#encrypt-red-hat-enterprise-linux-bring-your-own-subscription-gold-images) nie jest obsługiwane w obrazach niestandardowych.

- Jeśli używasz automatyzacji do inicjowania obsługi administracyjnej z obrazów RHEL BYOS, należy podać parametry planu podobne do tego, co zostało pokazane w przykładowych poleceniach. Na przykład, jeśli używasz Terraform, podajesz informacje o planie w [bloku planu](https://www.terraform.io/docs/providers/azurerm/r/virtual_machine.html#plan).

## <a name="next-steps"></a>Następne kroki

- Więcej informacji na temat red hat cloud access można znaleźć w [dokumentacji chmury publicznej Red Hat](https://access.redhat.com/public-cloud)
- Aby uzyskać instrukcje krok po kroku i szczegóły programu dotyczące dostępu do chmury, zobacz [dokumentację programu Red Hat Cloud Access](https://access.redhat.com/documentation/en-us/red_hat_subscription_management/1/html/red_hat_cloud_access_reference_guide/index).
- Aby dowiedzieć się więcej o infrastrukturze aktualizacji red hat, zobacz [Infrastruktura aktualizacji czerwonego kapelusza platformy Azure](./redhat-rhui.md).
- Aby dowiedzieć się więcej o wszystkich obrazach Czerwonego Kapelusza na platformie Azure, zobacz [stronę dokumentacji](./redhat-images.md).
- Aby uzyskać informacje na temat zasad obsługi Red Hat dla wszystkich wersji RHEL, zobacz [stronę cyklu życia red hat enterprise linux.](https://access.redhat.com/support/policy/updates/errata)
- Dodatkowe informacje na temat obrazów RHEL Gold Images można znaleźć w [dokumentacji Red Hat](https://access.redhat.com/documentation/en-us/red_hat_subscription_management/1/html/red_hat_cloud_access_reference_guide/using_red_hat_gold_images#con-gold-image-azure).
