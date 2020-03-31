---
title: Szybki start — tworzenie prywatnego punktu końcowego platformy Azure przy użyciu interfejsu wiersza polecenia platformy Azure
description: Dowiedz się więcej o prywatnym punkcie końcowym platformy Azure w tym przewodniku Szybki start
services: private-link
author: malopMSFT
ms.service: private-link
ms.topic: quickstart
ms.date: 09/16/2019
ms.author: allensu
ms.openlocfilehash: dbcb833e6f8b90cebd3d013e58168558bcd96827
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2020
ms.locfileid: "75459975"
---
# <a name="quickstart-create-a-private-endpoint-using-azure-cli"></a>Szybki start: tworzenie prywatnego punktu końcowego przy użyciu interfejsu wiersza polecenia platformy Azure
Prywatny punkt końcowy jest podstawowym budulcem dla łącza prywatnego na platformie Azure. Umożliwia zasoby platformy Azure, takie jak maszyny wirtualne (maszyny wirtualne), do komunikowania się prywatnie z zasobów łączy prywatnych. W tym przewodniku Szybki start dowiesz się, jak utworzyć maszynę wirtualną w sieci wirtualnej, serwer bazy danych SQL z prywatnym punktem końcowym przy użyciu interfejsu wiersza polecenia platformy Azure. Następnie można uzyskać dostęp do maszyny Wirtualnej i bezpiecznie uzyskać dostęp do zasobu łącza prywatnego (prywatny serwer bazy danych SQL azure w tym przykładzie). 

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Jeśli zdecydujesz się zainstalować interfejs wiersza polecenia platformy Azure i korzystać z niego lokalnie, ten przewodnik Szybki start wymaga interfejsu wiersza polecenia platformy Azure w wersji 2.0.28 lub nowszej. Aby dowiedzieć się, jaka wersja jest zainstalowana, uruchom polecenie `az --version`. Aby uzyskać informacje na temat instalacji i uaktualnienia, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli).

## <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

Przed utworzeniem dowolnego zasobu należy utworzyć grupę zasobów do obsługi sieci wirtualnej. Utwórz grupę zasobów za pomocą polecenia [az group create](/cli/azure/group). W tym przykładzie tworzy grupę zasobów o nazwie *myResourceGroup* w *lokalizacji westcentralus:*

```azurecli-interactive
az group create --name myResourceGroup --location westcentralus
```

## <a name="create-a-virtual-network"></a>Tworzenie sieci wirtualnej
Utwórz sieć wirtualną za pomocą [sieci wirtualnej az .](/cli/azure/network/vnet) W tym przykładzie tworzy domyślną sieć wirtualną o nazwie *myVirtualNetwork* z jedną podsiecią o nazwie *mySubnet:*

```azurecli-interactive
az network vnet create \
 --name myVirtualNetwork \
 --resource-group myResourceGroup \
 --subnet-name mySubnet
```
## <a name="disable-subnet-private-endpoint-policies"></a>Wyłączanie zasad prywatnego punktu końcowego podsieci 
Platforma Azure wdraża zasoby w podsieci w sieci wirtualnej, dlatego należy utworzyć lub zaktualizować podsieć, aby wyłączyć zasady sieci prywatnych punktów końcowych. Aktualizowanie konfiguracji podsieci o nazwie *mySubnet* za pomocą [aktualizacji podsieci sieci AZ:](https://docs.microsoft.com/cli/azure/network/vnet/subnet?view=azure-cli-latest#az-network-vnet-subnet-update)

```azurecli-interactive
az network vnet subnet update \
 --name mySubnet \
 --resource-group myResourceGroup \
 --vnet-name myVirtualNetwork \
 --disable-private-endpoint-network-policies true
```
## <a name="create-the-vm"></a>Tworzenie maszyny wirtualnej 
Utwórz maszynę wirtualną z az vm create. Po wyświetleniu monitu podaj hasło, które ma być używane jako poświadczenia logowania dla maszyny Wirtualnej. W tym przykładzie tworzy maszynę wirtualną o nazwie *myVm:* 
```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVm \
  --image Win2019Datacenter
```
 Zanotuj publiczny adres IP maszyny Wirtualnej. Użyjesz tego adresu do nawiązania połączenia z maszyną wirtualną z Internetu w następnym kroku.

## <a name="create-a-sql-database-server"></a>Tworzenie serwera bazy danych SQL 
Utwórz serwer bazy danych SQL za pomocą polecenia az sql server create. Należy pamiętać, że nazwa programu SQL Server musi być unikatowa na platformie Azure, więc zastąp wartość symbolu zastępczego w nawiasach własną unikatową wartością: 

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

Należy zauważyć, że identyfikator ```/subscriptions/subscriptionId/resourceGroups/myResourceGroup/providers/Microsoft.Sql/servers/myserver.``` programu SQL Server jest podobny do identyfikatora programu SQL Server w następnym kroku. 

## <a name="create-the-private-endpoint"></a>Tworzenie prywatnego punktu końcowego 
Tworzenie prywatnego punktu końcowego dla serwera bazy danych SQL w sieci wirtualnej: 
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
## <a name="configure-the-private-dns-zone"></a>Konfigurowanie prywatnej strefy DNS 
Utwórz prywatną strefę DNS dla domeny serwera bazy danych SQL i utwórz łącze skojarzenia z siecią wirtualną. 
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

1. W pasku wyszukiwania portalu wpisz *myVm*.

1. Wybierz przycisk **Połącz**. Po wybraniu przycisku **Połącz** zostanie otwarta strona **Łączenie z maszyną wirtualną**.

1. Wybierz opcję **Pobierz plik RDP**. Na platformie Azure zostanie utworzony plik Remote Desktop Protocol (*rdp*), który zostanie pobrany na komputer.

1. Otwórz pobrany plik.rdp*.

    1. Po wyświetleniu monitu wybierz pozycję **Połącz**.

    1. Wprowadź nazwę użytkownika i hasło określone podczas tworzenia maszyny Wirtualnej.

        > [!NOTE]
        > Może być konieczne **wybranie opcji Więcej opcji** > **Użyj innego konta,** aby określić poświadczenia wprowadzone podczas tworzenia maszyny Wirtualnej.

1. Kliknij przycisk **OK**.

1. Podczas procesu logowania może pojawić się ostrzeżenie o certyfikacie. Jeśli zostanie wyświetlone ostrzeżenie o certyfikacie, wybierz opcję **Tak** lub **Kontynuuj**.

1. Po wyświetleniu pulpitu maszyny wirtualnej zminimalizuj ją i wróć z powrotem do pulpitu lokalnego.  

## <a name="access-sql-database-server-privately-from-the-vm"></a>Dostęp do serwera bazy danych SQL prywatnie z maszyny Wirtualnej

W tej sekcji połączysz się z serwerem bazy danych SQL z maszyny Wirtualnej przy użyciu prywatnego punktu końcowego.

 1. W pulpicie zdalnym *myVM*otwórz program PowerShell.
 2. Wprowadź nslookup myserver.database.windows.net  Otrzymasz wiadomość podobną do tej: 

```
      Server:  UnKnown 
      Address:  168.63.129.16 
      Non-authoritative answer: 
      Name:    myserver.privatelink.database.windows.net 
      Address:  10.0.0.5 
      Aliases:  myserver.database.windows.net 
```
 3. Instalowanie programu SQL Server Management Studio 
 4. W obszarze Połącz z serwerem wprowadź lub wybierz te informacje: Typ serwera: Wybierz aparat bazy danych.
 Nazwa serwera: Wybierz myserver.database.windows.net nazwa użytkownika: Wprowadź nazwę użytkownika podana podczas tworzenia.
 Hasło: Wprowadź hasło podane podczas tworzenia.
 Zapamiętaj hasło: wybierz tak.
 
 5. Wybierz przycisk **Połącz**.
 6. Przeglądaj **bazy danych** z lewego menu.
 7. (Opcjonalnie) Tworzenie lub wykonywanie zapytań z *mydatabase*
 8. Zamknij połączenie pulpitu zdalnego do *myVm*.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów 
Gdy nie jest już potrzebne, można użyć az group delete, aby usunąć grupę zasobów i wszystkie zasoby, które posiada: 

```azurecli-interactive
az group delete --name myResourceGroup --yes 
```

## <a name="next-steps"></a>Następne kroki
- Dowiedz się więcej o [łączu prywatnym platformy Azure](private-link-overview.md)
