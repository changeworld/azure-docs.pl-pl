---
title: Prywatny link do Azure Database for MySQL (wersja zapoznawcza) Metoda instalacji interfejsu wiersza polecenia
description: Dowiedz się, jak skonfigurować link prywatny dla Azure Database for MySQL z interfejsu wiersza polecenia platformy Azure
author: kummanish
ms.author: manishku
ms.service: mysql
ms.topic: conceptual
ms.date: 01/09/2020
ms.openlocfilehash: b3162486740ca8cadbe8610c0ddfe5053955bb59
ms.sourcegitcommit: 8e9a6972196c5a752e9a0d021b715ca3b20a928f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/11/2020
ms.locfileid: "75897899"
---
# <a name="create-and-manage-private-link-for-azure-database-for-mysql-preview-using-cli"></a>Tworzenie prywatnego linku do Azure Database for MySQL (wersja zapoznawcza) i zarządzanie nim za pomocą interfejsu wiersza polecenia

Prywatny punkt końcowy to podstawowy blok konstrukcyjny dla prywatnego linku na platformie Azure. Umożliwia ona korzystanie z zasobów platformy Azure, takich jak Virtual Machines (VM), w celu komunikacji z prywatnymi zasobami łączy prywatnych. W tym artykule dowiesz się, jak za pomocą interfejsu wiersza polecenia platformy Azure utworzyć maszynę wirtualną w usłudze Azure Virtual Network i na serwerze Azure Database for MySQL za pomocą prywatnego punktu końcowego platformy Azure.

> [!NOTE]
> Ta funkcja jest dostępna we wszystkich regionach świadczenia usługi Azure, w których Azure Database for MySQL obsługuje warstwy cenowe Ogólnego przeznaczenia i zoptymalizowane pod kątem pamięci.

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Jeśli zdecydujesz się zainstalować interfejs wiersza polecenia platformy Azure i korzystać z niego lokalnie, ten przewodnik Szybki start wymaga interfejsu wiersza polecenia platformy Azure w wersji 2.0.28 lub nowszej. Aby dowiedzieć się, jaka wersja jest zainstalowana, uruchom polecenie `az --version`. Aby uzyskać informacje na temat instalacji i uaktualnienia, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli).

## <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

Przed utworzeniem dowolnego zasobu należy utworzyć grupę zasobów, która będzie hostować Virtual Network. Utwórz grupę zasobów za pomocą polecenia [az group create](/cli/azure/group). Ten przykład tworzy grupę zasobów o nazwie Moja *zasobów* w lokalizacji *westeurope* :

```azurecli-interactive
az group create --name myResourceGroup --location westeurope
```

## <a name="create-a-virtual-network"></a>Tworzenie sieci wirtualnej
Utwórz Virtual Network za pomocą [AZ Network VNET Create](/cli/azure/network/vnet). W tym przykładzie tworzony jest domyślny Virtual Network o nazwie *myVirtualNetwork* z jedną podsiecią o nazwie Moja *podsieć*:

```azurecli-interactive
az network vnet create \
 --name myVirtualNetwork \
 --resource-group myResourceGroup \
 --subnet-name mySubnet
```

## <a name="disable-subnet-private-endpoint-policies"></a>Wyłącz zasady prywatnego punktu końcowego podsieci 
Platforma Azure wdraża zasoby w podsieci w sieci wirtualnej, dlatego należy utworzyć lub zaktualizować podsieć w celu wyłączenia zasad sieci prywatnych punktów końcowych. Zaktualizuj konfigurację podsieci o nazwie Moja *podsieć* za pomocą elementu [AZ Network VNET Subnet Update](https://docs.microsoft.com/cli/azure/network/vnet/subnet?view=azure-cli-latest#az-network-vnet-subnet-update):

```azurecli-interactive
az network vnet subnet update \
 --name mySubnet \
 --resource-group myResourceGroup \
 --vnet-name myVirtualNetwork \
 --disable-private-endpoint-network-policies true
```
## <a name="create-the-vm"></a>Tworzenie maszyny wirtualnej 
Utwórz maszynę wirtualną za pomocą AZ VM Create. Po wyświetleniu monitu podaj hasło, które będzie używane jako poświadczenia logowania dla maszyny wirtualnej. Ten przykład tworzy maszynę wirtualną o nazwie *myVm*: 
```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVm \
  --image Win2019Datacenter
```
Zanotuj publiczny adres IP maszyny wirtualnej. Użyjesz tego adresu do nawiązania połączenia z maszyną wirtualną z Internetu w następnym kroku.

## <a name="create-an-azure-database-for-mysql-server"></a>Tworzenie serwera usługi Azure Database for MySQL 
Utwórz Azure Database for MySQL za pomocą polecenia AZ MySQL Server Create. Należy pamiętać, że nazwa serwera MySQL musi być unikatowa w obrębie platformy Azure, więc Zastąp wartość symbolu zastępczego w nawiasach własnym unikatowymi wartościami: 

```azurecli-interactive
# Create a logical server in the resource group 
az mysql server create \
--name mydemoserver \
--resource-group myResourcegroup \
--location westeurope \
--admin-user mylogin \
--admin-password <server_admin_password> \
--sku-name GP_Gen5_2
```

Zwróć uwagę, że identyfikator serwera MySQL jest podobny do ```/subscriptions/subscriptionId/resourceGroups/myResourceGroup/providers/Microsoft.DBforMySQL/servers/servername.``` w następnym kroku zostanie użyty identyfikator serwera MySQL. 

## <a name="create-the-private-endpoint"></a>Tworzenie prywatnego punktu końcowego 
Utwórz prywatny punkt końcowy dla serwera MySQL w Virtual Network: 
```azurecli-interactive
az network private-endpoint create \  
    --name myPrivateEndpoint \  
    --resource-group myResourceGroup \  
    --vnet-name myVirtualNetwork  \  
    --subnet mySubnet \  
    --private-connection-resource-id "<MySQL Server ID>" \  
    --group-ids mysqlServer \  
    --connection-name myConnection  
 ```

## <a name="configure-the-private-dns-zone"></a>Konfigurowanie strefy Prywatna strefa DNS 
Utwórz strefę Prywatna strefa DNS dla domeny serwera MySQL i Utwórz link powiązania z Virtual Network. 
```azurecli-interactive
az network private-dns zone create --resource-group myResourceGroup \ 
   --name  "privatelink.database.azure.com" 
az network private-dns link vnet create --resource-group myResourceGroup \ 
   --zone-name  "privatelink.database.azure.com"\ 
   --name MyDNSLink \ 
   --virtual-network myVirtualNetwork \ 
   --registration-enabled false 

#Query for the network interface ID  
networkInterfaceId=$(az network private-endpoint show --name myPrivateEndpoint --resource-group myResourceGroup --query 'networkInterfaces[0].id' -o tsv)
 
 
az resource show --ids $networkInterfaceId --api-version 2019-04-01 -o json 
# Copy the content for privateIPAddress and FQDN matching the Azure database for MySQL name 
 
 
#Create DNS records 
az network private-dns record-set a create --name myserver --zone-name privatelink.database.azure.com --resource-group myResourceGroup  
az network private-dns record-set a add-record --record-set-name myserver --zone-name privatelink.database.windows.net --resource-group myResourceGroup -a <Private IP Address>
```

## <a name="connect-to-a-vm-from-the-internet"></a>Nawiązywanie połączenia z maszyną wirtualną z Internetu

Połącz się z maszyną wirtualną *myVm* z Internetu w następujący sposób:

1. Na pasku wyszukiwania portalu wprowadź *myVm*.

1. Wybierz przycisk **Połącz**. Po wybraniu przycisku **Połącz** zostanie otwarta strona **Łączenie z maszyną wirtualną**.

1. Wybierz opcję **Pobierz plik RDP**. Na platformie Azure zostanie utworzony plik Remote Desktop Protocol (*rdp*), który zostanie pobrany na komputer.

1. Otwórz pobrany plik RDP *.

    1. Po wyświetleniu monitu wybierz pozycję **Połącz**.

    1. Wprowadź nazwę użytkownika i hasło określone podczas tworzenia maszyny wirtualnej.

        > [!NOTE]
        > Może okazać się konieczne wybranie pozycji **Więcej opcji** > **Użyj innego konta**, aby podać poświadczenia wprowadzone podczas tworzenia maszyny wirtualnej.

1. Kliknij przycisk **OK**.

1. Podczas procesu logowania może pojawić się ostrzeżenie o certyfikacie. Jeśli zostanie wyświetlone ostrzeżenie o certyfikacie, wybierz opcję **Tak** lub **Kontynuuj**.

1. Po wyświetleniu pulpitu maszyny wirtualnej zminimalizuj ją i wróć z powrotem do pulpitu lokalnego.  

## <a name="access-the-mysql-server-privately-from-the-vm"></a>Dostęp do serwera MySQL prywatnie z maszyny wirtualnej

1. W Pulpit zdalny *myVM*Otwórz program PowerShell.

2. Wprowadź polecenie  `nslookup mydemomysqlserver.mysql.privatelink.database.azure.com`. 

    Zostanie wyświetlony komunikat podobny do tego:
    ```azurepowershell
    Server:  UnKnown
    Address:  168.63.129.16
    Non-authoritative answer:
    Name:    mydemomysqlserver.mysql.privatelink.database.azure.com
    Address:  10.1.3.4

3. Test the private link connection for the MySQL server using any available client. In the example below I have used [MySQL Workbench](https://dev.mysql.com/doc/workbench/wb-installing-windows.html) to do the operation.


4. In **New connection**, enter or select this information:

    | Setting | Value |
    | ------- | ----- |
    | Connection Name| Select the connection name of your choice.|
    | Hostname | Select *mydemoserver.mysql.privatelink.database.azure.com* |
    | Username | Enter username as *username@servername* which is provided during the MySQL server creation. |
    | Password | Enter a password provided during the MySQL server creation. |
    ||

5. Select Connect.

6. Browse databases from left menu.

7. (Optionally) Create or query information from the MySQL database.

8. Close the remote desktop connection to myVm.

## Clean up resources 
When no longer needed, you can use az group delete to remove the resource group and all the resources it has: 

```azurecli-interactive
az group delete --name myResourceGroup --yes 
```

## <a name="next-steps"></a>Następne kroki
- Dowiedz się więcej o tym, [co to jest prywatny punkt końcowy platformy Azure](https://docs.microsoft.com/azure/private-link/private-endpoint-overview)