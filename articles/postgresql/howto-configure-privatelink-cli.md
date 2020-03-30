---
title: Łącze prywatne — interfejsu wiersza polecenia platformy Azure — usługa Azure Database for PostgreSQL — pojedynczy serwer
description: Dowiedz się, jak skonfigurować łącze prywatne dla usługi Azure Database dla postgreSQL — pojedynczy serwer z interfejsu wiersza polecenia platformy Azure
author: kummanish
ms.author: manishku
ms.service: postgresql
ms.topic: conceptual
ms.date: 01/09/2020
ms.openlocfilehash: d4288b901a0e6e132e32f8391d108e79861fc331
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79371039"
---
# <a name="create-and-manage-private-link-for-azure-database-for-postgresql---single-server-using-cli"></a>Tworzenie łącza prywatnego dla usługi Azure Database dla postgreSQL i zarządzanie nim — pojedynczy serwer przy użyciu interfejsu wiersza polecenia

Prywatny punkt końcowy jest podstawowym blokiem konstrukcyjnym dla łącza prywatnego na platformie Azure. Umożliwia zasoby platformy Azure, takie jak maszyny wirtualne (maszyny wirtualne), do komunikowania się prywatnie z zasobami łączy prywatnych. W tym artykule dowiesz się, jak używać interfejsu wiersza polecenia platformy Azure do tworzenia maszyny wirtualnej w sieci wirtualnej platformy Azure i usługi Azure Database dla pojedynczego serwera PostgreSQL z prywatnym punktem końcowym platformy Azure.

> [!NOTE]
> Ta funkcja jest dostępna we wszystkich regionach platformy Azure, gdzie usługa Azure Database for PostgreSQL — pojedynczy serwer obsługuje warstwy cenowe ogólnego przeznaczenia i zoptymalizowane pod kątem pamięci.

## <a name="prerequisites"></a>Wymagania wstępne

Aby przejść przez ten przewodnik, potrzebujesz:

- [Usługa Azure Database dla serwera i bazy danych PostgreSQL](quickstart-create-server-database-azure-cli.md).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Jeśli zdecydujesz się zainstalować interfejs wiersza polecenia platformy Azure i korzystać z niego lokalnie, ten przewodnik Szybki start wymaga interfejsu wiersza polecenia platformy Azure w wersji 2.0.28 lub nowszej. Aby dowiedzieć się, jaka wersja jest zainstalowana, uruchom polecenie `az --version`. Aby uzyskać informacje na temat instalacji i uaktualnienia, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli).

## <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

Przed utworzeniem dowolnego zasobu należy utworzyć grupę zasobów do obsługi sieci wirtualnej. Utwórz grupę zasobów za pomocą polecenia [az group create](/cli/azure/group). W tym przykładzie utworzy grupę zasobów o nazwie *myResourceGroup* w lokalizacji *westeurope:*

```azurecli-interactive
az group create --name myResourceGroup --location westeurope
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

## <a name="create-an-azure-database-for-postgresql---single-server"></a>Tworzenie bazy danych platformy Azure dla postgreSql — pojedynczy serwer 
Utwórz usługę Azure Database for PostgreSQL za pomocą polecenia tworzenia serwera az postgres. Należy pamiętać, że nazwa serwera PostgreSQL musi być unikatowa na całej platformie Azure, więc zastąp wartość zastępczą w nawiasach własną unikatową wartością: 

```azurecli-interactive
# Create a logical server in the resource group 
az postgres server create \
--name mydemoserver \
--resource-group myresourcegroup \
--location westeurope \
--admin-user mylogin \
--admin-password <server_admin_password> \
--sku-name GP_Gen5_2
```

Należy zauważyć, że identyfikator serwera PostgreSQL jest podobny do ```/subscriptions/subscriptionId/resourceGroups/myResourceGroup/providers/Microsoft.DBforPostgreSQL/servers/servername.``` Użyjesz identyfikatora serwera PostgreSQL w następnym kroku. 

## <a name="create-the-private-endpoint"></a>Tworzenie prywatnego punktu końcowego 
Utwórz prywatny punkt końcowy dla serwera PostgreSQL w sieci wirtualnej: 
```azurecli-interactive
az network private-endpoint create \  
    --name myPrivateEndpoint \  
    --resource-group myResourceGroup \  
    --vnet-name myVirtualNetwork  \  
    --subnet mySubnet \  
    --private-connection-resource-id "<PostgreSQL Server ID>" \  
    --group-ids postgresqlServer \  
    --connection-name myConnection  
 ```

## <a name="configure-the-private-dns-zone"></a>Konfigurowanie prywatnej strefy DNS 
Utwórz prywatną strefę DNS dla domeny serwera PostgreSQL i utwórz łącze skojarzenia z siecią wirtualną. 
```azurecli-interactive
az network private-dns zone create --resource-group myResourceGroup \ 
   --name  "privatelink.postgres.database.azure.com" 
az network private-dns link vnet create --resource-group myResourceGroup \ 
   --zone-name  "privatelink.postgres.database.azure.com"\ 
   --name MyDNSLink \ 
   --virtual-network myVirtualNetwork \ 
   --registration-enabled false 

#Query for the network interface ID  
networkInterfaceId=$(az network private-endpoint show --name myPrivateEndpoint --resource-group myResourceGroup --query 'networkInterfaces[0].id' -o tsv)
 
 
az resource show --ids $networkInterfaceId --api-version 2019-04-01 -o json 
# Copy the content for privateIPAddress and FQDN matching the Azure database for PostgreSQL name 
 
 
#Create DNS records 
az network private-dns record-set a create --name myserver --zone-name privatelink.postgres.database.azure.com --resource-group myResourceGroup  
az network private-dns record-set a add-record --record-set-name myserver --zone-name privatelink.postgres.database.windows.net --resource-group myResourceGroup -a <Private IP Address>
```

> [!NOTE] 
> Sieć FQDN w ustawieniach DNS klienta nie jest rozpoznawana jako prywatny adres IP skonfigurowany. Musisz skonfigurować strefę DNS dla skonfigurowanego FQDN, jak pokazano [tutaj](../dns/dns-operations-recordsets-portal.md).

## <a name="connect-to-a-vm-from-the-internet"></a>Nawiązywanie połączenia z maszyną wirtualną z Internetu

Połącz się z maszyną wirtualną *myVm* z Internetu w następujący sposób:

1. W pasku wyszukiwania portalu wpisz *myVm*.

1. Wybierz przycisk **Połącz**. Po wybraniu przycisku **Połącz** zostanie otwarta strona **Łączenie z maszyną wirtualną**.

1. Wybierz opcję **Pobierz plik RDP**. Na platformie Azure zostanie utworzony plik Remote Desktop Protocol (*rdp*), który zostanie pobrany na komputer.

1. Otwórz *pobrany plik rdp.*

    1. Po wyświetleniu monitu wybierz pozycję **Połącz**.

    1. Wprowadź nazwę użytkownika i hasło określone podczas tworzenia maszyny Wirtualnej.

        > [!NOTE]
        > Może być konieczne **wybranie opcji Więcej opcji** > **Użyj innego konta,** aby określić poświadczenia wprowadzone podczas tworzenia maszyny Wirtualnej.

1. Kliknij przycisk **OK**.

1. Podczas procesu logowania może pojawić się ostrzeżenie o certyfikacie. Jeśli zostanie wyświetlone ostrzeżenie o certyfikacie, wybierz opcję **Tak** lub **Kontynuuj**.

1. Po wyświetleniu pulpitu maszyny wirtualnej zminimalizuj ją i wróć z powrotem do pulpitu lokalnego.  

## <a name="access-the-postgresql-server-privately-from-the-vm"></a>Dostęp do serwera PostgreSQL prywatnie z maszyny Wirtualnej

1. W pulpicie zdalnym *myVM*otwórz program PowerShell.

2. Wprowadź polecenie  `nslookup mydemopostgresserver.privatelink.postgres.database.azure.com`. 

    Otrzymasz wiadomość podobną do tej:
    ```azurepowershell
    Server:  UnKnown
    Address:  168.63.129.16
    Non-authoritative answer:
    Name:    mydemopostgresserver.privatelink.postgres.database.azure.com
    Address:  10.1.3.4
    ```

3. Przetestuj połączenie łącza prywatnego dla serwera PostgreSQL przy użyciu dowolnego dostępnego klienta. W poniższym przykładzie użyłem [usługi Azure Data studio](https://docs.microsoft.com/sql/azure-data-studio/download?view=sql-server-ver15) do wykonania operacji.

4. W **obszarze Nowe połączenie**wprowadź lub wybierz te informacje:

    | Ustawienie | Wartość |
    | ------- | ----- |
    | Typ serwera| Wybierz **postgresql**.|
    | Nazwa serwera| Wybierz *mydemopostgresserver.privatelink.postgres.database.azure.com* |
    | Nazwa użytkownika | Wprowadź nazwę username@servername użytkownika, która jest podana podczas tworzenia serwera PostgreSQL. |
    |Hasło |Wprowadź hasło podane podczas tworzenia serwera PostgreSQL. |
    |Protokół SSL|Wybierz **opcję Wymagane**.|
    ||

5. Wybierz przycisk Połącz.

6. Przeglądaj bazy danych z lewego menu.

7. (Opcjonalnie) Tworzenie lub wykonywanie zapytań z serwera postgreSQL.

8. Zamknij połączenie pulpitu zdalnego do myVm.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów 
Gdy nie jest już potrzebne, można użyć az group delete, aby usunąć grupę zasobów i wszystkie zasoby, które posiada: 

```azurecli-interactive
az group delete --name myResourceGroup --yes 
```

## <a name="next-steps"></a>Następne kroki
- Dowiedz się więcej o [prywatnym punkcie końcowym platformy Azure](https://docs.microsoft.com/azure/private-link/private-endpoint-overview)
