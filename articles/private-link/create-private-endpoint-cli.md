---
title: Tworzenie prywatnego punktu końcowego platformy Azure przy użyciu interfejsu wiersza polecenia platformy Azure | Microsoft Docs
description: Informacje o prywatnym punkcie końcowym platformy Azure
services: private-link
author: KumudD
ms.service: private-link
ms.topic: article
ms.date: 09/16/2019
ms.author: kumud
ms.openlocfilehash: 30394ba7b71d7dcb4233e5dca341dda47fd9ffa7
ms.sourcegitcommit: 0576bcb894031eb9e7ddb919e241e2e3c42f291d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/15/2019
ms.locfileid: "72376314"
---
# <a name="create-a-private-endpoint-using-azure-cli"></a>Tworzenie prywatnego punktu końcowego przy użyciu interfejsu wiersza polecenia platformy Azure
Prywatny punkt końcowy to podstawowy blok konstrukcyjny dla prywatnego linku na platformie Azure. Dzięki temu zasoby platformy Azure, takie jak maszyny wirtualne, mogą komunikować się prywatnie z zasobami łączy prywatnych. W tym przewodniku szybki start dowiesz się, jak utworzyć MASZYNę wirtualną w sieci wirtualnej, SQL Database serwerze z prywatnym punktem końcowym przy użyciu interfejsu wiersza polecenia platformy Azure. Następnie można uzyskać dostęp do maszyny wirtualnej i bezpiecznie uzyskać dostęp do prywatnego zasobu linku (w tym przykładzie do prywatnego serwera Azure SQL Database). 

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Jeśli zdecydujesz się zainstalować interfejs wiersza polecenia platformy Azure i korzystać z niego lokalnie, ten przewodnik Szybki start wymaga interfejsu wiersza polecenia platformy Azure w wersji 2.0.28 lub nowszej. Aby dowiedzieć się, jaka wersja jest zainstalowana, uruchom polecenie `az --version`. Aby uzyskać informacje na temat instalacji i uaktualnienia, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli).

## <a name="create-a-resource-group"></a>Utwórz grupę zasobów

Przed utworzeniem dowolnego zasobu należy utworzyć grupę zasobów, która będzie hostować Virtual Network. Utwórz grupę zasobów za pomocą polecenia [az group create](/cli/azure/group). Ten przykład tworzy grupę zasobów o nazwie Moja *zasobów* w lokalizacji *westcentralus* :

```azurecli-interactive
az group create --name myResourceGroup --location westcentralus
```

## <a name="create-a-virtual-network"></a>Tworzenie Virtual Network
Utwórz Virtual Network za pomocą [AZ Network VNET Create](/cli/azure/network/vnet). W tym przykładzie tworzony jest domyślny Virtual Network o nazwie *myVirtualNetwork* z jedną podsiecią o nazwie Moja *podsieć*:

```azurecli-interactive
az network vnet create \
 --name myVirtualNetwork \
 --resource-group myResourceGroup \
 --subnet-name mySubnet
```
## <a name="disable-subnet-private-endpoint-policies"></a>Wyłącz zasady prywatnego punktu końcowego podsieci 
Platforma Azure wdraża zasoby w podsieci w sieci wirtualnej, dlatego należy utworzyć lub zaktualizować podsieć w celu wyłączenia zasad sieci prywatnych punktów końcowych. Zaktualizuj konfigurację podsieci o nazwie Moja *podsieć* with [AZ Network VNET Subnet Update](https://docs.microsoft.com/cli/azure/network/vnet/subnet?view=azure-cli-latest#az-network-vnet-subnet-update):

```azurecli-interactive
az network vnet subnet update \
 --name mySubnet \
 --resource-group myResourceGroup \
 --vnet-name myVirtualNetwork \
 --disable-private-endpoint-network-policies true
```
## <a name="create-the-vm"></a>Tworzenie maszyny wirtualnej 
Utwórz maszynę wirtualną za pomocą AZ VM Create. Po wyświetleniu monitu podaj hasło, które będzie używane jako poświadczenia logowania dla maszyny wirtualnej. Ten przykład tworzy maszynę wirtualną o nazwie *myVm*: 
```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVm \
  --image Win2019Datacenter
```
 Zanotuj publiczny adres IP maszyny wirtualnej. Użyjesz tego adresu do nawiązania połączenia z maszyną wirtualną z Internetu w następnym kroku.

## <a name="create-a-sql-database-server"></a>Utwórz serwer SQL Database 
Utwórz serwer SQL Database za pomocą polecenia AZ SQL Server Create. Należy pamiętać, że nazwa SQL Server musi być unikatowa w obrębie platformy Azure, więc Zastąp wartość symbolu zastępczego w nawiasach własnym unikatowymi wartościami: 

```azurecli-interactive
# Create a logical server in the resource group 
az sql server create \ 
    --name "myserver"\ 
    --resource-group myResourceGroup \ 
    --location WestUS \ 
    --admin-user "sqladmin" \ 
    --admin-password "CHANGE_PASSWORD_1" 
 
# Create a database in the server with zone redundancy as false 
az sql db create \ 
    --resource-group myResourceGroup  \ 
    --server myserver \ 
    --name mySampleDatabase \ 
    --sample-name AdventureWorksLT \ 
    --edition GeneralPurpose \ 
    --family Gen4 \ 
    --capacity 1 
```

Zwróć uwagę, że identyfikator SQL Server jest podobny do @ no__t-0 spowoduje użycie identyfikatora SQL Server w następnym kroku. 

## <a name="create-the-private-endpoint"></a>Tworzenie prywatnego punktu końcowego 
Utwórz prywatny punkt końcowy dla serwera SQL Database w Virtual Network: 
```azurecli-interactive
az network private-endpoint create \  
    --name myPrivateEndpoint \  
    --resource-group myResourceGroup \  
    --vnet-name myVirtualNetwork  \  
    --subnet mySubnet \  
    --private-connection-resource-id "<SQL Server ID>" \  
    --group-ids sqlServer \  
    --connection-name myConnection  
 ```
## <a name="configure-the-private-dns-zone"></a>Konfigurowanie strefy Prywatna strefa DNS 
Utwórz strefę Prywatna strefa DNS dla domeny serwera SQL Database i Utwórz link powiązania z Virtual Network. 
```azurecli-interactive
az network private-dns zone create --resource-group myResourceGroup \ 
   --name  "privatelink.database.windows.net" 
az network private-dns link vnet create --resource-group myResourceGroup \ 
   --zone-name  "privatelink.database.windows.net"\ 
   --name MyDNSLink \ 
   --virtual-network myVirtualNetwork \ 
   --registration-enabled false 

#Query for the network interface ID  
networkInterfaceId=$(az network private-endpoint show --name myPrivateEndpoint --resource-group myResourceGroup --query 'networkInterfaces[0].id' -o tsv)
 
 
az resource show --ids $networkInterfaceId --api-version 2019-04-01 -o json 
# Copy the content for privateIPAddress and FQDN matching the SQL server name 
 
 
#Create DNS records 
az network private-dns record-set a create --name myserver --zone-name privatelink.database.windows.net --resource-group myResourceGroup  
az network private-dns record-set a add-record --record-set-name myserver --zone-name privatelink.database.windows.net --resource-group myResourceGroup -a <Private IP Address>
```

## <a name="connect-to-a-vm-from-the-internet"></a>Nawiązywanie połączenia z maszyną wirtualną z Internetu

Połącz się z maszyną wirtualną *myVm* z Internetu w następujący sposób:

1. Na pasku wyszukiwania portalu wprowadź *myVm*.

1. Wybierz przycisk **Połącz**. Po wybraniu przycisku **Połącz** zostanie otwarta strona **Łączenie z maszyną wirtualną**.

1. Wybierz opcję **Pobierz plik RDP**. Plik Remote Desktop Protocol ( *.rdp*) zostanie utworzony na platformie Azure, a następnie pobrany na komputer.

1. Otwórz pobrany plik RDP *.

    1. Po wyświetleniu monitu wybierz pozycję **Połącz**.

    1. Wprowadź nazwę użytkownika i hasło określone podczas tworzenia maszyny wirtualnej.

        > [!NOTE]
        > Może okazać się konieczne wybranie pozycji **Więcej opcji** > **Użyj innego konta**, aby podać poświadczenia wprowadzone podczas tworzenia maszyny wirtualnej.

1. Kliknij przycisk **OK**.

1. Podczas procesu logowania może pojawić się ostrzeżenie o certyfikacie. Jeśli zostanie wyświetlone ostrzeżenie o certyfikacie, wybierz opcję **Tak** lub **Kontynuuj**.

1. Po wyświetleniu pulpitu maszyny wirtualnej zminimalizuj ją i wróć z powrotem do pulpitu lokalnego.  

## <a name="access-sql-database-server-privately-from-the-vm"></a>Dostęp do serwera SQL Database prywatnie z poziomu maszyny wirtualnej

W tej sekcji nawiążesz połączenie z serwerem SQL Database z maszyny wirtualnej przy użyciu prywatnego punktu końcowego.

 1. W Pulpit zdalny *myVM*Otwórz program PowerShell.
 2. Wprowadź polecenie nslookup no__t. Database. Windows. NET @-0 spowoduje to wyświetlenie komunikatu podobnego do tego: 

```
      Server:  UnKnown 
      Address:  168.63.129.16 
      Non-authoritative answer: 
      Name:    myserver.privatelink.database.windows.net 
      Address:  10.0.0.5 
      Aliases:  myserver.database.windows.net 
```
 3. Zainstaluj SQL Server Management Studio 
 4. W obszarze Połącz z serwerem wprowadź lub wybierz następujące informacje: typ serwera: wybierz pozycję aparat bazy danych.
 Nazwa serwera: wybierz pozycję Nazwa użytkownika myserver.database.windows.net: Wprowadź nazwę użytkownika podaną podczas tworzenia.
 Hasło: wprowadź hasło podane podczas tworzenia.
 Zapamiętaj hasło: wybierz pozycję tak.
 
 5. Wybierz pozycję **Połącz**.
 6. Przeglądaj **bazy danych** z menu po lewej stronie.
 7. Zdefiniować Utwórz lub zapytaj informacje z *bazy danych*
 8. Zamknij połączenie pulpitu zdalnego z *myVm*.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów 
Gdy nie jest już potrzebne, można użyć polecenie AZ Group Delete, aby usunąć grupę zasobów i wszystkie jej zasoby: 

```azurecli-interactive
az group delete --name myResourceGroup --yes 
```

## <a name="next-steps"></a>Następne kroki
- Dowiedz się więcej o [prywatnym łączu platformy Azure](private-link-overview.md)
 
