---
title: Manage Private Endpoints in Azure
description: Learn how to create a Private Endpoint using the Azure portal
services: private-link
author: asudbring
ms.service: private-link
ms.topic: quickstart
ms.date: 09/16/2019
ms.author: allensu
ms.openlocfilehash: 50e358684ebef82e96c4c21e6139434f8581595a
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/20/2019
ms.locfileid: "74224797"
---
# <a name="create-a-private-endpoint-using-azure-portal"></a>Create a Private Endpoint using Azure portal

A Private Endpoint is the fundamental building block for private link in Azure. It enables Azure resources, like Virtual Machines (VMs), to communicate privately with private link resources. In this Quickstart, you will learn how to create a VM on an Azure Virtual Network, a  SQL Database Server with an Azure private endpoint using the Azure Portal. Then, you can securely access the SQL Database Server from the VM.

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).


> [!NOTE]
> Private endpoint(s) are not permitted in conjunction with service endpoints in the same subnet!

## <a name="sign-in-to-azure"></a>Zaloguj się w usłudze Azure

Zaloguj się do witryny Azure Portal pod adresem https://portal.azure.com.

## <a name="create-a-vm"></a>Tworzenie maszyny wirtualnej
In this section, you will create virtual network and the subnet to host the VM that is used to access your Private Link resource (a SQL server in Azure in this example).

### <a name="create-the-virtual-network"></a>Tworzenie sieci wirtualnej


In this section, you will create a Virtual Network and the subnet to host the VM that is used to access your Private Link resource.

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


### <a name="create-virtual-machine"></a>Utwórz maszynę wirtualną

1. On the upper-left side of the screen in the Azure portal, select **Create a resource** > **Compute** > **Virtual Machine**.

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
    |||


1. Wybierz pozycję **Przegląd + utwórz**. You're taken to the **Review + create** page where Azure validates your configuration.

1. When you see the **Validation passed** message, select **Create**.

## <a name="create-a-sql-database-server"></a>Create a SQL database server
In this section, you will create a SQL database server in Azure. 

1. On the upper-left side of the screen in the Azure portal, select **Create a resource** > **Databases** > **SQL database**.

1. In **Create SQL database - Basics**, enter or select this information:

    | Ustawienie | Wartość |
    | ------- | ----- |
    | **Database details** | |
    | Subskrypcja | Wybierz subskrypcję. |
    | Grupa zasobów | Wybierz pozycję **myResourceGroup**. You created this in the previous section.|
    | **SZCZEGÓŁY WYSTĄPIENIA** |  |
    | Nazwa bazy danych  | Enter *mydatabase*. If this name is taken, create a unique name. |
    |||
5. In **Server**, select **Create new**. 
6. In **New server**, enter or select this information:

    | Ustawienie | Wartość |
    | ------- | ----- |
    |Nazwa serwera  | Enter *myserver*. If this name is taken, create a unique name.|
    | Identyfikator logowania administratora serwera| Enter an administrator name of your choosing. |
    | Hasło | Wprowadź wybrane hasło. The password must be at least 8 characters long and meet the defined requirements. |
    | Lokalizacja | Select an Azure region where you want to want your SQL Server to reside. |
    
7. Kliknij przycisk **OK**. 
8. Wybierz pozycję **Przegląd + utwórz**. You're taken to the **Review + create** page where Azure validates your configuration. 
9. When you see the Validation passed message, select **Create**. 
10. When you see the Validation passed message, select Create. 

## <a name="create-a-private-endpoint"></a>Create a private endpoint

In this section, you will create a SQL server and add a private endpoint to it. 

1. On the upper-left side of the screen in the Azure portal, select **Create a resource** > **Networking** > **Private Link Center (Preview)** .
2. In **Private Link Center - Overview**, on the option to **Build a private connection to a service**, select **Start**.
1. In **Create a private endpoint (Preview) - Basics**, enter or select this information:

    | Ustawienie | Wartość |
    | ------- | ----- |
    | **Project details** | |
    | Subskrypcja | Wybierz subskrypcję. |
    | Grupa zasobów | Wybierz pozycję **myResourceGroup**. You created this in the previous section.|
    | **SZCZEGÓŁY WYSTĄPIENIA** |  |
    | Nazwa | Enter * myPrivateEndpoint*. If this name is taken, create a unique name. |
    |Region|Select **WestCentralUS**.|
    |||
5. Select **Next: Resource**.
6. In **Create a private endpoint - Resource**, enter or select this information:

    | Ustawienie | Wartość |
    | ------- | ----- |
    |Metoda połączenia  | Select connect to an Azure resource in my directory.|
    | Subskrypcja| Wybierz subskrypcję. |
    | Typ zasobu | Select **Microsoft.Sql/servers**. |
    | Zasób |Select *myServer*|
    |Target sub-resource |Select *sqlServer*|
    |||
7. Select **Next: Configuration**.
8. In **Create a private endpoint (Preview) - Configuration**, enter or select this information:

    | Ustawienie | Wartość |
    | ------- | ----- |
    |**NETWORKING**| |
    | Sieć wirtualna| Select *MyVirtualNetwork*. |
    | Podsieć | Select *mySubnet*. |
    |**PRIVATE DNS INTEGRATION**||
    |Integrate with private DNS zone |Wybierz pozycję **Tak**. |
    |Private DNS Zone |Select *(New)privatelink.database.windows.net* |
    |||

1. Wybierz pozycję **Przegląd + utwórz**. You're taken to the **Review + create** page where Azure validates your configuration. 
2. When you see the **Validation passed** message, select **Create**. 
 
## <a name="connect-to-a-vm-using-remote-desktop-rdp"></a>Connect to a VM using Remote Desktop (RDP)


After you've created **myVm*, connect to it from the internet as follows: 

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

## <a name="access-the-sql-database-server-privately-from-the-vm"></a>Access the SQL database server privately from the VM

1. In the Remote Desktop of *myVM*, open PowerShell.

2. Enter `nslookup myserver.database.windows.net`. 

    You'll receive a message similar to this:
    ```azurepowershell
    Server:  UnKnown
    Address:  168.63.129.16
    Non-authoritative answer:
    Name:    myserver.privatelink.database.windows.net
    Address:  10.0.0.5
    Aliases:   myserver.database.windows.net
    ```
3. Install [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms?view=sql-server-2017).

4. In **Connect to server**, enter or select this information:

    | Ustawienie | Wartość |
    | ------- | ----- |
    | Typ serwera| Wybierz pozycję **Aparat bazy danych**.|
    | Nazwa serwera| Select *myserver.database.windows.net* |
    | Nazwa użytkownika | Enter username as username@servername which is provided during the SQL server creation. |
    |Hasło |Enter a password provided during the SQL server creation. |
    |Remember password|Wybierz pozycję **Tak**.|
    |||
1. Wybierz przycisk **Połącz**.
2. Browse databases from left menu.
3. (Optionally) Create or query information from mydatabase.
4. Close the remote desktop connection to *myVm*. 

## <a name="clean-up-resources"></a>Oczyszczanie zasobów 
When you're done using the private endpoint, SQL server, and the VM, delete the resource group and all of the resources it contains: 
1. Enter *myResourceGroup* in the **Search** box at the top of the portal and select *myResourceGroup* from the search results. 
2. Wybierz pozycję **Usuń grupę zasobów**. 
3. Enter myResourceGroup for **TYPE THE RESOURCE GROUP NAME** and select **Delete**.

## <a name="next-steps"></a>Następne kroki

In this quickstart, you created a VM on a virtual network, a SQL database server, and a private endpoint for private access. You connected to one VM from the internet and securely communicated to the SQL database server using Private Link. To learn more about private endpoints, see [What is Azure private endpoint?](private-endpoint-overview.md).

