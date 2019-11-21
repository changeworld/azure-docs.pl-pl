---
title: Connect privately to a storage account using Azure Private Endpoint
description: Learn how to connect privately to a storage account in Azure using a Private Endpoint.
services: private-link
author: asudbring
ms.service: private-link
ms.topic: article
ms.date: 09/16/2019
ms.author: allensu
ms.openlocfilehash: cfe0caaf199821358f8a66ac65ae75c38336c725
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/20/2019
ms.locfileid: "74228091"
---
# <a name="connect-privately-to-a-storage-account-using-azure-private-endpoint"></a>Connect privately to a storage account using Azure Private Endpoint
Azure Private Endpoint is the fundamental building block for Private Link in Azure. It enables Azure resources, like virtual machines (VMs), to communicate privately with Private Link resources.

In this Quickstart, you will learn how to create a VM on an Azure virtual network, a storage account with a Private Endpoint using the Azure portal. Then, you can securely access the storage account from the VM.


## <a name="sign-in-to-azure"></a>Zaloguj się w usłudze Azure

Zaloguj się do witryny Azure Portal pod adresem https://portal.azure.com.

## <a name="create-a-vm"></a>Tworzenie maszyny wirtualnej
In this section, you will create virtual network and the subnet to host the VM that is used to access your Private Link Resource (a storage account in this example).

### <a name="create-the-virtual-network"></a>Tworzenie sieci wirtualnej

In this section, you will create virtual network and the subnet to host the VM that is used to access your Private Link resource.

1. W lewym górnym rogu ekranu wybierz pozycję **Utwórz zasób** > **Sieć** > **Sieć wirtualna**.
1. W obszarze **Utwórz sieć wirtualną** wprowadź lub wybierz następujące informacje:

    | Ustawienie | Wartość |
    | ------- | ----- |
    | Nazwa | Enter *MyVirtualNetwork*. |
    | Przestrzeń adresowa | Wprowadź adres *10.1.0.0/16*. |
    | Subskrypcja | Wybierz subskrypcję.|
    | Grupa zasobów | Wybierz pozycję **Utwórz nową**, wprowadź nazwę *myResourceGroup*, a następnie wybierz przycisk **OK**. |
    | Lokalizacja | Select **WestCentralUS**.|
    | Podsieć — nazwa | Enter *mySubnet*. |
    | Zakres adresów podsieci: 10.41.0.0/24 | Wprowadź *10.1.0.0/24*. |
    |||
1. Leave the rest as default and select **Create**.


### <a name="create-virtual-machine"></a>Tworzenie maszyny wirtualnej

1. On the upper-left side of the screen in the Azure portal, select **Create a resource** > **Compute** > **Virtual machine**.

1. W obszarze **Tworzenie maszyny wirtualnej — ustawienia podstawowe** wprowadź lub wybierz następujące informacje:

    | Ustawienie | Wartość |
    | ------- | ----- |
    | **SZCZEGÓŁY PROJEKTU** | |
    | Subskrypcja | Wybierz subskrypcję. |
    | Grupa zasobów | Wybierz pozycję **myResourceGroup**. You created this in the previous section.  |
    | **SZCZEGÓŁY WYSTĄPIENIA** |  |
    | Nazwa maszyny wirtualnej | Enter *myVm*. |
    | Region | Select **WestCentralUS**. |
    | Opcje dostępności | Pozostaw wartość domyślną **Brak wymaganej nadmiarowości infrastruktury**. |
    | Obraz | Select **Windows Server 2019 Datacenter**. |
    | Rozmiar | Pozostaw wartość domyślną **Standardowy DS1, wersja 2**. |
    | **KONTO ADMINISTRATORA** |  |
    | Nazwa użytkownika | Enter a username of your choosing. |
    | Hasło | Wprowadź wybrane hasło. Hasło musi mieć co najmniej 12 znaków i spełniać [zdefiniowane wymagania dotyczące złożoności](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm).|
    | Potwierdź hasło | Ponownie wprowadź hasło. |
    | **REGUŁY PORTÓW WEJŚCIOWYCH** |  |
    | Publiczne porty wejściowe | Pozostaw wartość domyślną **Brak**. |
    | **OSZCZĘDZAJ PIENIĄDZE** |  |
    | Masz już licencję systemu Windows? | Pozostaw wartość domyślną **Nie**. |
    |||

1. Select **Next: Disks**.

1. In **Create a virtual machine - Disks**, leave the defaults and select **Next: Networking**.

1. W obszarze **Tworzenie maszyny wirtualnej — sieć** wybierz następujące informacje:

    | Ustawienie | Wartość |
    | ------- | ----- |
    | Sieć wirtualna | Leave the default **MyVirtualNetwork**.  |
    | Przestrzeń adresowa | Leave the default **10.1.0.0/24**.|
    | Podsieć | Leave the default **mySubnet (10.1.0.0/24)** .|
    | Publiczny adres IP | Pozostaw wartość domyślną **(nowy) myVm-ip**. |
    | Publiczne porty wejściowe | Wybierz pozycję **Zezwalaj na wybrane porty**. |
    | Wybierz porty wejściowe | Wybierz pozycje **HTTP** i **RDP**.|
    ||

1. Wybierz pozycję **Przegląd + utwórz**. You're taken to the **Review + create** page where Azure validates your configuration.

1. When you see the **Validation passed** message, select **Create**.

## <a name="create-your-private-endpoint"></a>Create your Private Endpoint
In this section, you will create a private storage account using a Private Endpoint to it. 

1. On the upper-left side of the screen in the Azure portal, select **Create a resource** > **Storage** > **Storage account**.

1. In **Create storage account - Basics**, enter or select this information:

    | Ustawienie | Wartość |
    | ------- | ----- |
    | **SZCZEGÓŁY PROJEKTU** | |
    | Subskrypcja | Wybierz subskrypcję. |
    | Grupa zasobów | Wybierz pozycję **myResourceGroup**. You created this in the previous section.|
    | **SZCZEGÓŁY WYSTĄPIENIA** |  |
    | Nazwa konta magazynu  | Enter *mystorageaccount*. If this name is taken, create a unique name. |
    | Region | Select **WestCentralUS**. |
    | Wydajność| Pozostaw wartość domyślną **Standardowa**. |
    | Rodzaj konta | Leave the default **Storage (general purpose v2)** . |
    | Replikacja | Select **Read-access geo-redundant storage (RA-GRS)** . |
    |||
  
3. Select **Next: Networking**.
4. In **Create a storage account - Networking**, connectivity method, select **Private Endpoint**.
5. In **Create a storage account - Networking**, select **Add Private Endpoint**. 
6. In **Create Private Endpoint**, enter or select this information:

    | Ustawienie | Wartość |
    | ------- | ----- |
    | **SZCZEGÓŁY PROJEKTU** | |
    | Subskrypcja | Wybierz subskrypcję. |
    | Grupa zasobów | Wybierz pozycję **myResourceGroup**. You created this in the previous section.|
    |Lokalizacja|Select **WestCentralUS**.|
    |Nazwa|Enter *myPrivateEndpoint*.  |
    |Storage sub-resource|Leave the default **Blob**. |
    | **NETWORKING** |  |
    | Sieć wirtualna  | Select *MyVirtualNetwork* from resource group *myResourceGroup*. |
    | Podsieć | Select *mySubnet*. |
    | **PRIVATE DNS INTEGRATION**|  |
    | Integrate with private DNS zone  | Leave the default **Yes**. |
    | Prywatna strefa DNS  | Leave the default ** (New) privatelink.blob.core.windows.net**. |
    |||
7. Kliknij przycisk **OK**. 
8. Wybierz pozycję **Przegląd + utwórz**. You're taken to the **Review + create** page where Azure validates your configuration. 
9. When you see the **Validation passed** message, select **Create**. 
10. Browse to the storage account resource that you just created.
11. Select **Access Keys** from the left content menu.
12. Select **Copy** on the connection string for key1.
 
## <a name="connect-to-a-vm-from-the-internet"></a>Nawiązywanie połączenia z maszyną wirtualną z Internetu

Connect to the VM *myVm* from the internet as follows:

1. In the portal's search bar, enter *myVm*.

1. Wybierz przycisk **Połącz**. Po wybraniu przycisku **Połącz** zostanie otwarta strona **Łączenie z maszyną wirtualną**.

1. Wybierz opcję **Pobierz plik RDP**. Plik Remote Desktop Protocol ( *.rdp*) zostanie utworzony na platformie Azure, a następnie pobrany na komputer.

1. Open the downloaded.rdp* file.

    1. Po wyświetleniu monitu wybierz pozycję **Połącz**.

    1. Enter the username and password you specified when creating the VM.

        > [!NOTE]
        > Może okazać się konieczne wybranie pozycji **Więcej opcji** > **Użyj innego konta**, aby podać poświadczenia wprowadzone podczas tworzenia maszyny wirtualnej.

1. Kliknij przycisk **OK**.

1. Podczas procesu logowania może pojawić się ostrzeżenie o certyfikacie. Jeśli zostanie wyświetlone ostrzeżenie o certyfikacie, wybierz opcję **Tak** lub **Kontynuuj**.

1. Po wyświetleniu pulpitu maszyny wirtualnej zminimalizuj ją i wróć z powrotem do pulpitu lokalnego.  

## <a name="access-storage-account-privately-from-the-vm"></a>Access storage account privately from the VM

In this section, you will connect privately to the storage account using the Private Endpoint.

> [!IMPORTANT]
> DNS configuration for storage needs a manual modification on the hosts file to include the FQDN of the specific account Please modify the following file using administrator permissions on Windows: c:\Windows\System32\Drivers\etc\hosts or Linux /etc/hosts Include the DNS information for the account from previous step in the following format [Private IP Address] myaccount.blob.core.windows.net

1. In the Remote Desktop of *myVM*, open PowerShell.
2. Enter `nslookup mystorageaccount.blob.core.windows.net` You'll receive a message similar to this:
    ```azurepowershell
    Server:  UnKnown
    Address:  168.63.129.16
    Non-authoritative answer:
    Name:    mystorageaccount123123.privatelink.blob.core.windows.net
    Address:  10.0.0.5
    Aliases:  mystorageaccount.blob.core.windows.net
    ```
3. Zainstaluj [Eksplorator usługi Microsoft Azure Storage](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer?toc=%2Fazure%2Fstorage%2Fblobs%2Ftoc.json&tabs=windows).
4. Select **Storage accounts** with the right-click.
5. Select **Connect to an azure storage**.
6. Select **Use a connection string**.
7. Wybierz opcję **Dalej**.
8. Enter the connection string by pasting the information previously copied.
9. Wybierz opcję **Dalej**.
10. Wybierz przycisk **Połącz**.
11. Browse the Blob containers from mystorageaccount 
12. (Optionally) Create folders and/or upload files to *mystorageaccount*. 
13. Close the remote desktop connection to *myVM*. 

Additional options to access the storage account:
- Microsoft Azure Storage Explorer is a standalone free app from Microsoft that enables you to work visually with Azure storage data on Windows, macOS, and Linux. You can install the application to browse privately the storage account content. 
 
- The AzCopy utility is another option for high-performance scriptable data transfer for Azure storage. Narzędzie AzCopy służy do transferowania danych do i z usług Blob, File i Table Storage. 


## <a name="clean-up-resources"></a>Oczyszczanie zasobów 
When you're done using the Private Endpoint, storage account and the VM, delete the resource group and all of the resources it contains: 
1. Enter *myResourceGroup* in the **Search** box at the top of the portal and select *myResourceGroup* from the search results. 
2. Wybierz pozycję **Usuń grupę zasobów**. 
3. Enter *myResourceGroup* for **TYPE THE RESOURCE GROUP NAME** and select **Delete**. 

## <a name="next-steps"></a>Następne kroki
In this Quickstart, you created a VM on a virtual network and storage account and a Private Endpoint. You connected to one VM from the internet and securely communicated to the storage account using Private Link. To learn more about Private Endpoint, see [What is Azure Private Endpoint?](private-endpoint-overview.md).
