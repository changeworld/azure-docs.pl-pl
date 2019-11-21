---
title: Quickstart - Create an Azure private DNS zone using the Azure portal
description: In this quickstart, you create and test a private DNS zone and record in Azure DNS. This is a step-by-step guide to create and manage your first private DNS zone and record using the Azure portal.
services: dns
author: asudbring
ms.service: dns
ms.topic: quickstart
ms.date: 10/11/2019
ms.author: allensu
ms.openlocfilehash: 48c5684dd491a91b6da212f78c535433ac2aecf7
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/20/2019
ms.locfileid: "74210751"
---
# <a name="quickstart-create-an-azure-private-dns-zone-using-the-azure-portal"></a>Quickstart: Create an Azure private DNS zone using the Azure portal

This quickstart walks you through the steps to create your first private DNS zone and record using the Azure portal.

Strefa DNS służy do hostowania rekordów systemu DNS dla określonej domeny. Aby rozpocząć hostowanie domeny w usłudze Azure DNS, musisz utworzyć strefę DNS dla tej nazwy domeny. Każdy rekord DNS domeny zostanie utworzony w tej strefie DNS. Aby opublikować prywatną strefę DNS w sieci wirtualnej, musisz określić listę sieci wirtualnych, które mogą rozpoznawać rekordy w strefie.  These are called *linked* virtual networks. When autoregistration is enabled, Azure DNS also updates the zone records whenever a virtual machine is created, changes its' IP address, or is deleted.

W tym przewodniku Szybki start zawarto informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Create a private DNS zone
> * Tworzenie sieci wirtualnej
> * Link the virtual network
> * Tworzenie testowych maszyn wirtualnych
> * Tworzenie dodatkowego rekordu DNS
> * Testowanie strefy prywatnej

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

If you prefer, you can complete this quickstart using [Azure PowerShell](private-dns-getstarted-powershell.md) or [Azure CLI](private-dns-getstarted-cli.md).

## <a name="create-a-private-dns-zone"></a>Create a private DNS zone

The following example creates a DNS zone called **private.contoso.com** in a resource group called **MyAzureResourceGroup**.

Strefa DNS zawiera wpisy DNS dla domeny. Aby rozpocząć hostowanie domeny w usłudze Azure DNS, należy utworzyć strefę DNS dla tej nazwy domeny.

![Private DNS zones search](media/private-dns-portal/search-private-dns.png)

1. On the portal search bar, type **private dns zones** in the search text box and press **Enter**.
1. Select **Private DNS zone**.
2. Select **Create private dns zone**.

1. On the **Create Private DNS zone** page, type or select the following values:

   - **Resource group**: Select **Create new**, enter *MyAzureResourceGroup*, and select **OK**. Nazwa grupy zasobów musi być unikatowa w ramach subskrypcji platformy Azure. 
   -  **Name**: Type *private.contoso.com* for this example.
1. For **Resource group location**, select **West Central US**.

1. Wybierz pozycję **Przegląd + utwórz**.

1. Wybierz pozycję **Utwórz**.

Tworzenie strefy może potrwać kilka minut.

## <a name="create-a-virtual-network"></a>Tworzenie sieci wirtualnej

1. On the portal page upper left, select **Create a resource**, then **Networking**, then select **Virtual network**.
2. For **Name**, type **myAzureVNet**.
3. For **Resource group**, select **MyAzureResourceGroup**.
4. For **Location**, select **West Central US**.
5. Accept the other default values and select **Create**.

## <a name="link-the-virtual-network"></a>Link the virtual network

To link the private DNS zone to a virtual network, you create a virtual network link.

![Add virtual network link](media/private-dns-portal/dns-add-virtual-network-link.png)

1. Open the **MyAzureResourceGroup** resource group and select the **private.contoso.com** private zone.
2. On the left pane, select **Virtual network links**.
3. Wybierz pozycję **Dodaj**.
4. Type **myLink** for the **Link name**.
5. For **Virtual network**, select **myAzureVNet**.
6. Select the **Enable auto registration** check box.
7. Kliknij przycisk **OK**.

## <a name="create-the-test-virtual-machines"></a>Tworzenie testowych maszyn wirtualnych

Teraz utworzysz dwie maszyny wirtualne, aby umożliwić przetestowanie strefy prywatnej DNS:

1. On the portal page upper left, select **Create a resource**, and then select **Windows Server 2016 Datacenter**.
1. Select **MyAzureResourceGroup** for the resource group.
1. Type **myVM01** - for the name of the virtual machine.
1. Select **West Central US** for the **Region**.
1. Type **azureadmin** for the administrator user name.
2. Type **Azure12345678** for the password and confirm the password.

5. For **Public inbound ports**, select **Allow selected ports**, and then select **RDP (3389)** for **Select inbound ports**.
10. Accept the other defaults for the page and then click **Next: Disks >** .
11. Accept the defaults on the **Disks** page, then click **Next: Networking >** .
1. Make sure that **myAzureVNet** is selected for the virtual network.
1. Accept the other defaults for the page, and then click **Next: Management >** .
2. For **Boot diagnostics**, select **Off**, accept the other defaults, and then select **Review + create**.
1. Review the settings and then click **Create**.

Repeat these steps and create another virtual machine named **myVM02**.

It will take a few minutes for both virtual machines to complete.

## <a name="create-an-additional-dns-record"></a>Tworzenie dodatkowego rekordu DNS

 The following example creates a record with the relative name **db** in the DNS Zone **private.contoso.com**, in resource group **MyAzureResourceGroup**. The fully qualified name of the record set is **db.private.contoso.com**. The record type is "A", with the IP address of **myVM01**.

1. Open the **MyAzureResourceGroup** resource group and select the **private.contoso.com** private zone.
2. Wybierz pozycję **+ Zestaw rekordów**.
3. For **Name**, type **db**.
4. For **IP Address**, type the IP address you see for **myVM01**. This should be auto registered when the virtual machine started.
5. Kliknij przycisk **OK**.

## <a name="test-the-private-zone"></a>Testowanie strefy prywatnej

Now you can test the name resolution for your **private.contoso.com** private zone.

### <a name="configure-vms-to-allow-inbound-icmp"></a>Konfigurowanie maszyn wirtualnych w celu zezwolenia na ruch przychodzący protokołu ICMP

Do przetestowania rozpoznawania nazw możesz użyć polecenia ping. W tym celu skonfiguruj zaporę na obydwu maszynach wirtualnych, aby zezwolić na przychodzące pakiety protokołu ICMP.

1. Połącz się z maszyną wirtualną myVM01 i otwórz okno programu Windows PowerShell, używając uprawnień administratora.
2. Uruchom następujące polecenie:

   ```powershell
   New-NetFirewallRule –DisplayName "Allow ICMPv4-In" –Protocol ICMPv4
   ```

Powtórz dla maszyny wirtualnej myVM02.

### <a name="ping-the-vms-by-name"></a>Wysyłanie polecenia ping do maszyn wirtualnych według nazwy

1. W wierszu polecenia programu Windows PowerShell maszyny wirtualnej myVM02 wyślij polecenie ping do maszyny wirtualnej myVM01 przy użyciu automatycznie zarejestrowanej nazwy hosta:
   ```
   ping myVM01.private.contoso.com
   ```
   Powinny pojawić się dane wyjściowe podobne do następujących:
   ```
   PS C:\> ping myvm01.private.contoso.com

   Pinging myvm01.private.contoso.com [10.2.0.4] with 32 bytes of data:
   Reply from 10.2.0.4: bytes=32 time<1ms TTL=128
   Reply from 10.2.0.4: bytes=32 time=1ms TTL=128
   Reply from 10.2.0.4: bytes=32 time<1ms TTL=128
   Reply from 10.2.0.4: bytes=32 time<1ms TTL=128

   Ping statistics for 10.2.0.4:
       Packets: Sent = 4, Received = 4, Lost = 0 (0% loss),
   Approximate round trip times in milli-seconds:
       Minimum = 0ms, Maximum = 1ms, Average = 0ms
   PS C:\>
   ```
2. Teraz wyślij polecenie ping do utworzonej wcześniej nazwy **db**:
   ```
   ping db.private.contoso.com
   ```
   Powinny pojawić się dane wyjściowe podobne do następujących:
   ```
   PS C:\> ping db.private.contoso.com

   Pinging db.private.contoso.com [10.2.0.4] with 32 bytes of data:
   Reply from 10.2.0.4: bytes=32 time<1ms TTL=128
   Reply from 10.2.0.4: bytes=32 time<1ms TTL=128
   Reply from 10.2.0.4: bytes=32 time<1ms TTL=128
   Reply from 10.2.0.4: bytes=32 time<1ms TTL=128

   Ping statistics for 10.2.0.4:
       Packets: Sent = 4, Received = 4, Lost = 0 (0% loss),
   Approximate round trip times in milli-seconds:
       Minimum = 0ms, Maximum = 0ms, Average = 0ms
   PS C:\>
   ```

## <a name="delete-all-resources"></a>Usuwanie wszystkich zasobów

When no longer needed, delete the **MyAzureResourceGroup** resource group to delete the resources created in this quickstart.


## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Azure DNS Private Zones scenarios](private-dns-scenarios.md)

