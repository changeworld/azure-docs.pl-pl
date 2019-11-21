---
title: Connect to an Azure Cosmos account with Azure Private Link
description: Learn how to securely access the Azure Cosmos account from a VM by creating a Private Endpoint.
author: asudbring
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: allensu
ms.openlocfilehash: 90710176ec16d1c565e24ff7df56b0b838f2699e
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/20/2019
ms.locfileid: "74229424"
---
# <a name="connect-privately-to-an-azure-cosmos-account-using-azure-private-link"></a>Connect privately to an Azure Cosmos account using Azure Private Link

Azure Private Endpoint is the fundamental building block for Private Link in Azure. It enables Azure resources, like virtual machines (VMs), to communicate privately with Private Link resources.

In this article, you will learn how to create a VM on an Azure virtual network and an Azure Cosmos account with a Private Endpoint using the Azure portal. Then, you can securely access the Azure Cosmos account from the VM.

## <a name="sign-in-to-azure"></a>Zaloguj się w usłudze Azure

Sign in to the [Azure portal.](https://portal.azure.com)

## <a name="create-a-vm"></a>Tworzenie maszyny wirtualnej

### <a name="create-the-virtual-network"></a>Tworzenie sieci wirtualnej

In this section, you will create a virtual network and the subnet to host the VM that is used to access your Private Link resource (an Azure Cosmos account in this example).

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

### <a name="create-the-virtual-machine"></a>Tworzenie maszyny wirtualnej

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
    | Nazwa użytkownika | Enter a username of your choice. |
    | Hasło | Enter a password of your choice. Hasło musi mieć co najmniej 12 znaków i spełniać [zdefiniowane wymagania dotyczące złożoności](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm).|
    | Potwierdź hasło | Reenter the password. |
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

## <a name="create-an-azure-cosmos-account"></a>Tworzenie konta usługi Azure Cosmos

Create an [Azure Cosmos SQL API account](../cosmos-db/create-cosmosdb-resources-portal.md#create-an-azure-cosmos-db-account). For simplicity, you can create the Azure Cosmos account in the same region as the other resources (that is "WestCentralUS").

## <a name="create-a-private-endpoint-for-your-azure-cosmos-account"></a>Create a Private Endpoint for your Azure Cosmos account

Create a Private Link for your Azure Cosmos account as described in the [Create a Private Link using the Azure portal](../cosmos-db/how-to-configure-private-endpoints.md#create-a-private-endpoint-by-using-the-azure-portal) section of the linked article.

## <a name="connect-to-a-vm-from-the-internet"></a>Nawiązywanie połączenia z maszyną wirtualną z Internetu

Connect to the VM *myVm* from the internet as follows:

1. In the portal's search bar, enter *myVm*.

1. Wybierz przycisk **Połącz**. Po wybraniu przycisku **Połącz** zostanie otwarta strona **Łączenie z maszyną wirtualną**.

1. Wybierz opcję **Pobierz plik RDP**. Plik Remote Desktop Protocol ( *.rdp*) zostanie utworzony na platformie Azure, a następnie pobrany na komputer.

1. Otwórz pobrany plik *rdp*.

    1. Po wyświetleniu monitu wybierz pozycję **Połącz**.

    1. Enter the username and password you specified when creating the VM.

        > [!NOTE]
        > Może okazać się konieczne wybranie pozycji **Więcej opcji** > **Użyj innego konta**, aby podać poświadczenia wprowadzone podczas tworzenia maszyny wirtualnej.

1. Kliknij przycisk **OK**.

1. Podczas procesu logowania może pojawić się ostrzeżenie o certyfikacie. Jeśli zostanie wyświetlone ostrzeżenie o certyfikacie, wybierz opcję **Tak** lub **Kontynuuj**.

1. Po wyświetleniu pulpitu maszyny wirtualnej zminimalizuj ją i wróć z powrotem do pulpitu lokalnego.  

## <a name="access-the-azure-cosmos-account-privately-from-the-vm"></a>Access the Azure Cosmos account privately from the VM

In this section, you will connect privately to the Azure Cosmos account using the Private Endpoint. 

> [!IMPORTANT]
> The DNS configuration for the Azure Cosmos account needs a manual modification on the hosts file to include the FQDN of the specific account. In production scenarios you will configure the DNS server to use the private IP addresses. However for the demo purpose, you can use administrator permissions on the VM and modify the `c:\Windows\System32\Drivers\etc\hosts` file (on Windows) or `/etc/hosts` file (on Linux) to include the IP address and DNS mapping.

1. To include the IP address and DNS mapping, sign into your Virtual machine *myVM*, open the `c:\Windows\System32\Drivers\etc\hosts` file and include the DNS information from previous step in the following format:

   [Private IP Address] [Account endpoint].documents.azure.com

   **Przykład:**

   10.1.255.13 mycosmosaccount.documents.azure.com

   10.1.255.14 mycosmosaccount-eastus.documents.azure.com


1. In the Remote Desktop of *myVM*, install [Microsoft Azure Storage Explorer](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer?toc=%2Fazure%2Fstorage%2Fblobs%2Ftoc.json&tabs=windows).

1. Select **Cosmos DB Accounts (Preview)** with the right-click.

1. Select **Connect to Cosmos DB**.

1. Wybierz pozycję **Interfejs API**.

1. Enter the connection string by pasting the information previously copied.

1. Wybierz opcję **Dalej**.

1. Wybierz przycisk **Połącz**.

1. Browse the Azure Cosmos databases and containers from *mycosmosaccount*.

1. (Optionally) add new items to *mycosmosaccount*.

1. Close the remote desktop connection to *myVM*.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

When you're done using the Private Endpoint, Azure Cosmos account and the VM, delete the resource group and all of the resources it contains: 

1. Enter *myResourceGroup* in the **Search** box at the top of the portal and select *myResourceGroup* from the search results.

1. Wybierz pozycję **Usuń grupę zasobów**.

1. Enter *myResourceGroup* for **TYPE THE RESOURCE GROUP NAME** and select **Delete**.

## <a name="next-steps"></a>Następne kroki

In this article, you created a VM on a virtual network, an Azure Cosmos account and a Private Endpoint. You connected to the VM from the internet and securely communicated to the Azure Cosmos account using Private Link.

* To learn more about Private Endpoint, see [What is Azure Private Endpoint?](private-endpoint-overview.md).

* To learn more about limitation of Private Endpoint when using with Azure Cosmos DB, see [Azure Private Link with Azure Cosmos DB](../cosmos-db/how-to-configure-private-endpoints.md) article.