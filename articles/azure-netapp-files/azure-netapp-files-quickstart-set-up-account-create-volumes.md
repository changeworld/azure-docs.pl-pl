---
title: 'Szybki start: konfigurowanie plików NetApp platformy Azure i woluminu NFS'
description: Szybki start — w tym artykule opisano, jak szybko skonfigurować pliki NetApp platformy Azure i utworzyć wolumin.
author: b-juche
ms.author: b-juche
ms.service: azure-netapp-files
ms.workload: storage
ms.topic: quickstart
ms.date: 12/01/2019
ms.openlocfilehash: fc7f13fb7ffe1667aaeaa4a3cc1916c6049a98c1
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2020
ms.locfileid: "75551662"
---
# <a name="quickstart-set-up-azure-netapp-files-and-create-an-nfs-volume"></a>Szybki start: konfigurowanie plików NetApp platformy Azure i tworzenie woluminu NFS 

W tym artykule pokazano, jak szybko skonfigurować pliki NetApp platformy Azure i utworzyć wolumin. 

W tym przewodniku Szybki start skonfigurujesz następujące elementy:

- Rejestracja dla usługi Azure NetApp Files i Dostawcy zasobów NetApp
- Konto NetApp
- Pula pojemności
- Wolumin NFS dla plików NetApp platformy Azure

Jeśli nie masz subskrypcji platformy Azure, utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) przed rozpoczęciem.

## <a name="before-you-begin"></a>Przed rozpoczęciem 

> [!IMPORTANT] 
> Musisz uzyskać dostęp do usługi Azure NetApp Files.  Aby zażądać dostępu do usługi, zobacz [stronę przesyłania listy oczekujących plików usługi Azure NetApp](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR8cq17Xv9yVBtRCSlcD_gdVUNUpUWEpLNERIM1NOVzA5MzczQ0dQR1ZTSS4u).  Przed kontynuowaniem należy poczekać na oficjalną wiadomość e-mail z potwierdzeniem od zespołu plików usługi Azure NetApp Files. 

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

---

## <a name="register-for-azure-netapp-files-and-netapp-resource-provider"></a>Zarejestruj się dla usługi Azure NetApp Files i Dostawcy zasobów NetApp

> [!NOTE]
> Proces rejestracji może trochę potrwać.
>

# <a name="portal"></a>[Portal](#tab/azure-portal)

Aby wykonać kroki rejestracji przy użyciu portalu, otwórz sesję usługi Cloud Shell, jak wskazano powyżej, i wykonaj następujące kroki interfejsu wiersza polecenia platformy Azure:

[!INCLUDE [azure-netapp-files-cloudshell-include](../../includes/azure-netapp-files-azure-cloud-shell-window.md)]

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)

Ten artykuł in how wymaga modułu Azure PowerShell Az w wersji 2.6.0 lub nowszej. Uruchom polecenie `Get-Module -ListAvailable Az`, aby określić bieżącą wersję. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie modułu Azure PowerShell](/powershell/azure/install-Az-ps). Jeśli wolisz, możesz użyć konsoli Cloud Shell w sesji programu PowerShell.

1. W wierszu polecenia programu PowerShell (lub sesji powłoki chmury programu PowerShell) określ subskrypcję, która została na białej liście dla plików NetApp platformy Azure:
    ```powershell-interactive
    Select-AzSubscription -Subscription <subscriptionId>
    ```

2. Zarejestruj dostawcę zasobów platformy Azure:
    ```powershell-interactive
    Register-AzResourceProvider -ProviderNamespace Microsoft.NetApp
    ```

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

[!INCLUDE [azure-netapp-files-cloudshell-include](../../includes/azure-netapp-files-azure-cloud-shell-window.md)]

---

## <a name="create-a-netapp-account"></a>Tworzenie konta usługi NetApp

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. W polu wyszukiwania witryny Azure portal wprowadź pliki **usługi Azure NetApp,** a następnie wybierz pozycję **Pliki netapp platformy Azure** z wyświetlona lista.

      ![Wybieranie plików NetApp platformy Azure](../media/azure-netapp-files/azure-netapp-files-select-azure-netapp-files.png)

2. Kliknij pozycję **+ Dodaj**, aby utworzyć nowe konto usługi NetApp.

     ![Tworzenie nowego konta NetApp](../media/azure-netapp-files/azure-netapp-files-create-new-netapp-account.png)

3. W oknie Nowe konto NetApp podaj następujące informacje: 
   1. Wprowadź **myaccount1** dla nazwy konta. 
   2. Wybierz subskrypcję.
   3. Wybierz **pozycję Utwórz nowy,** aby utworzyć nową grupę zasobów. Wprowadź **myRG1** dla nazwy grupy zasobów. Kliknij przycisk **OK**. 
   4. Wybierz lokalizację konta.  

      ![Nowe okno konta NetApp](../media/azure-netapp-files/azure-netapp-files-new-account-window.png)  

      ![Okno grupy zasobów](../media/azure-netapp-files/azure-netapp-files-resource-group-window.png)

4. Kliknij **przycisk Utwórz,** aby utworzyć nowe konto NetApp.

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)

1. Zdefiniuj niektóre zmienne, abyśmy mogli odwoływać się do nich w pozostałych przykładach:

    ```powershell-interactive
    $resourceGroup = "myRG1"
    $location = "eastus"
    $anfAccountName = "myaccount1"
    ``` 

    > [!NOTE]
    > Aby uzyskać listę obsługiwanych regionów, zapoznaj [się](https://azure.microsoft.com/global-infrastructure/services/?products=netapp&regions=all) z listą obsługiwanych regionów w regionie.
    > Aby uzyskać nazwę regionu obsługiwana przez nasze narzędzia wiersza polecenia, użyj`Get-AzLocation | select Location`
    >

1. Utwórz nową grupę zasobów za pomocą polecenia [New-AzResourceGroup:](/powershell/module/az.resources/new-azresourcegroup)

    ```powershell-interactive
    New-AzResourceGroup -Name $resourceGroup -Location $location
    ```

2. Utwórz konto usługi Azure NetApp Files za pomocą polecenia [New-AzNetAppFilesAccount:](/powershell/module/az.netappfiles/New-AzNetAppFilesAccount)
   
    ```powershell-interactive
    New-AzNetAppFilesAccount -ResourceGroupName $resourceGroup -Location $location -Name $anfAccountName
    ```

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

1. Zdefiniuj niektóre zmienne, abyśmy mogli odwoływać się do nich w pozostałych przykładach:

    ```azurecli-interactive
    RESOURCE_GROUP="myRG1"
    LOCATION="eastus"
    ANF_ACCOUNT_NAME="myaccount1"
    ``` 

    > [!NOTE]
    > Aby uzyskać listę obsługiwanych regionów, zapoznaj [się](https://azure.microsoft.com/global-infrastructure/services/?products=netapp&regions=all) z listą obsługiwanych regionów w regionie.
    > Aby uzyskać nazwę regionu obsługiwana przez nasze narzędzia wiersza polecenia, użyj`az account list-locations -query "[].{Region:name}" --out table`
    >

2. Utwórz nową grupę zasobów za pomocą polecenia [utwórz grupę az:](/cli/azure/group#az-group-create)

    ```azurecli-interactive
    az group create \
        --name $RESOURCE_GROUP \
        --location $LOCATION
    ```

3. Utwórz konto usługi Azure NetApp Files za pomocą [polecenia tworzenia konta az netappfiles:](/cli/azure/netappfiles/account#az-netappfiles-account-create)
   
    ```azurecli-interactive
    az netappfiles account create \
        --resource-group $RESOURCE_GROUP \
        --location $LOCATION \
        --account-name $ANF_ACCOUNT_NAME
    ```
---

## <a name="set-up-a-capacity-pool"></a>Konfigurowanie puli pojemności

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. W bloku zarządzania plikami NetApp platformy Azure wybierz swoje konto NetApp (**myaccount1**).

    ![Wybierz konto NetApp](../media/azure-netapp-files/azure-netapp-files-select-netapp-account.png)  

2. W bloku zarządzania plikami NetApp platformy Azure na koncie NetApp kliknij pozycję **Pule pojemności**.

    ![Kliknij pule pojemności](../media/azure-netapp-files/azure-netapp-files-click-capacity-pools.png)  

3. Kliknij **pozycję + Dodaj pule**. 

    ![Kliknij pozycję Dodaj pule](../media/azure-netapp-files/azure-netapp-files-click-add-pools.png)  

4. Podaj informacje dotyczące puli pojemności: 
    1. Wprowadź **mypool1** jako nazwę puli.
    2. Wybierz **premium** dla poziomu usług. 
    3. Określ **4 (TiB)** jako rozmiar puli. 

5. Kliknij przycisk **OK**.

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)

1. Definiowanie nowych zmiennych do wykorzystania w przyszłości

    ```powershell-interactive
    $poolName = "mypool1"
    $poolSizeBytes = 4398046511104 # 4TiB
    $serviceLevel = "Premium" # Valid values are Standard, Premium and Ultra
    ```

1. Tworzenie nowej puli pojemności przy użyciu [puli plików New-AzNetAppFilesPool](/powershell/module/az.netappfiles/new-aznetappfilespool)

    ```powershell-interactive
    New-AzNetAppFilesPool -ResourceGroupName $resourceGroup -Location $location -AccountName $anfAccountName -Name $poolName -PoolSize $poolSizeBytes -ServiceLevel $serviceLevel
    ```

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

1. Definiowanie nowych zmiennych do wykorzystania w przyszłości

    ```azurecli-interactive
    POOL_NAME="mypool1"
    POOL_SIZE_TiB=4 # Size in Azure CLI needs to be in TiB unit (minimum 4 TiB)
    SERVICE_LEVEL="Premium" # Valid values are Standard, Premium and Ultra
    ```

2. Tworzenie nowej puli pojemności przy użyciu [puli az netappfiles](/cli/azure/netappfiles/pool#az-netappfiles-pool-create) 

    ```azurecli-interactive
    az netappfiles pool create \
        --resource-group $RESOURCE_GROUP \
        --location $LOCATION \
        --account-name $ANF_ACCOUNT_NAME \
        --pool-name $POOL_NAME \
        --size $POOL_SIZE_TiB \
        --service-level $SERVICE_LEVEL
    ```

---

## <a name="create-nfs-volume-for-azure-netapp-files"></a>Tworzenie woluminu NFS dla plików NetApp platformy Azure

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. W bloku zarządzania plikami NetApp platformy Azure na koncie NetApp kliknij pozycję **Woluminy**.

    ![Klikanie pozycji Woluminy](../media/azure-netapp-files/azure-netapp-files-click-volumes.png)  

2. Kliknij pozycję **+ Dodaj wolumin**.

    ![Kliknij pozycję Dodaj woluminy](../media/azure-netapp-files/azure-netapp-files-click-add-volumes.png)  

3. W oknie Tworzenie woluminu podaj informacje dotyczące woluminu: 
   1. Wprowadź **myvol1** jako nazwę woluminu. 
   2. Wybierz pulę pojemności **(mypool1**).
   3. Użyj wartości domyślnej dla przydziału. 
   4. W obszarze sieć wirtualna kliknij pozycję **Utwórz nowy,** aby utworzyć nową sieć wirtualną platformy Azure.Under virtual network, click Create new to create a new Azure virtual network (Vnet).  Następnie wypełnij następujące informacje:
       * Wprowadź **myvnet1** jako nazwę sieci wirtualnej.
       * Określ przestrzeń adresową dla ustawienia, na przykład 10.7.0.0/16
       * Wprowadź **myANFsubnet** jako nazwę podsieci.
       * Określ zakres adresów podsieci, na przykład 10.7.0.0/24. Nie można udostępnić dedykowanej podsieci innym zasobom.
       * Wybierz **pozycję Microsoft.NetApp/volumes** dla delegowania podsieci.
       * Kliknij **przycisk OK,** aby utworzyć sieć wirtualną.
   5. W podsieci wybierz nowo utworzoną w sieci wirtualnej **(myvnet1)** jako podsieć delegata.

      ![Tworzenie okna woluminu](../media/azure-netapp-files/azure-netapp-files-create-volume-window.png)  

      ![Tworzenie okna sieci wirtualnej](../media/azure-netapp-files/azure-netapp-files-create-virtual-network-window.png)  

4. Kliknij **pozycję Protokół**, a następnie wykonaj następujące czynności: 
    * Wybierz **nfs** jako typ protokołu dla woluminu.  
    * Wprowadź **myfilepath1** jako ścieżkę pliku, która będzie używana do tworzenia ścieżki eksportu woluminu.  
    * Wybierz wersję NFS **(NFSv3** lub **NFSv4.1**) dla woluminu.  
      Zobacz [zagadnienia](azure-netapp-files-create-volumes.md#considerations) i [najlepsze rozwiązania dotyczące](azure-netapp-files-create-volumes.md#best-practice) wersji systemu plików NFS. 
      
  ![Określanie protokołu NFS dla szybkiego startu](../media/azure-netapp-files/azure-netapp-files-quickstart-protocol-nfs.png)

5. Kliknij pozycję **Przegląd + utwórz**.

    ![Przeglądanie i tworzenie okna](../media/azure-netapp-files/azure-netapp-files-review-and-create-window.png)  

6. Przejrzyj informacje dotyczące woluminu, a następnie kliknij przycisk **Utwórz**.  
    Utworzony wolumin pojawi się w bloku Woluminy.

    ![Utworzony wolumin](../media/azure-netapp-files/azure-netapp-files-create-volume-created.png)  

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)

1. Utwórz delegowanie podsieci do "Microsoft.NetApp/volumes" za pomocą polecenia [New-AzDelegation.](/powershell/module/az.network/new-azdelegation)

    ```powershell-interactive
    $anfDelegation = New-AzDelegation -Name ([guid]::NewGuid().Guid) -ServiceName "Microsoft.NetApp/volumes"
    ```

2. Utwórz konfigurację podsieci za pomocą polecenia [New-AzVirtualNetworkSubnetConfig.](/powershell/module/az.network/new-azvirtualnetworksubnetconfig)

    ```powershell-interactive
    $subnet = New-AzVirtualNetworkSubnetConfig -Name "myANFSubnet" -AddressPrefix "10.7.0.0/24" -Delegation $anfDelegation
    ```

3. Utwórz sieć wirtualną za pomocą polecenia [New-AzVirtualNetwork.](/powershell/module/az.network/new-azvirtualnetwork)
    
    ```powershell-interactive
    $vnet = New-AzVirtualNetwork -Name "myvnet1" -ResourceGroupName $resourceGroup -Location $location -AddressPrefix "10.7.0.0/16" -Subnet $subnet
    ```

4. Utwórz wolumin za pomocą polecenia [New-AzNetAppFilesVolume.](/powershell/module/az.netappfiles/new-aznetappfilesvolume)
   
    ```powershell-interactive
    $volumeSizeBytes = 1099511627776 # 100GiB
    $subnetId = $vnet.Subnets[0].Id

    New-AzNetAppFilesVolume -ResourceGroupName $resourceGroup `
        -Location $location `
        -AccountName $anfAccountName `
        -PoolName $poolName `
        -Name "myvol1" `
        -UsageThreshold $volumeSizeBytes `
        -SubnetId $subnetId `
        -CreationToken "myfilepath1" `
        -ServiceLevel $serviceLevel `
        -ProtocolType NFSv3
    ```

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

1. Definiowanie niektórych zmiennych do późniejszego użycia.
    
    ```azurecli-interactive
    VNET_NAME="myvnet1"
    SUBNET_NAME="myANFSubnet"
    ```

1. Utwórz sieć wirtualną bez podsieci za pomocą polecenia [tworzenie sieci az.](/cli/azure/network/vnet#az-network-vnet-create)
    
    ```azurecli-interactive
    az network vnet create \
        --resource-group $RESOURCE_GROUP \
        --name $VNET_NAME \
        --location $LOCATION \
        --address-prefix "10.7.0.0/16"

    ```

2. Utwórz delegowaną podsieć za pomocą polecenia tworzenia podsieci [sieci az.](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-create)

    ```azurecli-interactive
    az network vnet subnet create \
        --resource-group $RESOURCE_GROUP \
        --vnet-name $VNET_NAME \
        --name $SUBNET_NAME \
        --address-prefixes "10.7.0.0/24" \
        --delegations "Microsoft.NetApp/volumes"
    ```

3. Utwórz wolumin za pomocą polecenia [tworzenia woluminu az netappfiles.](/cli/azure/netappfiles/volume#az-netappfiles-volume-create)
   
    ```azurecli-interactive
    VNET_ID=$(az network vnet show --resource-group $RESOURCE_GROUP --name $VNET_NAME --query "id" -o tsv)
    SUBNET_ID=$(az network vnet subnet show --resource-group $RESOURCE_GROUP --vnet-name $VNET_NAME --name $SUBNET_NAME --query "id" -o tsv)
    VOLUME_SIZE_GiB=100 # 100 GiB
    UNIQUE_FILE_PATH="myfilepath2" # Please note that creation token needs to be unique within subscription and region

    az netappfiles volume create \
        --resource-group $RESOURCE_GROUP \
        --location $LOCATION \
        --account-name $ANF_ACCOUNT_NAME \
        --pool-name $POOL_NAME \
        --name "myvol1" \
        --service-level $SERVICE_LEVEL \
        --vnet $VNET_ID \
        --subnet $SUBNET_ID \
        --usage-threshold $VOLUME_SIZE_GiB \
        --file-path $UNIQUE_FILE_PATH \
        --protocol-types "NFSv3"
    ```

---

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

# <a name="portal"></a>[Portal](#tab/azure-portal)

Po zakończeniu i jeśli chcesz, możesz usunąć grupę zasobów. Akcja usuwania grupy zasobów jest nieodwracalna.  

> [!IMPORTANT]
> Wszystkie zasoby w grupach zasobów zostaną trwale usunięte i nie można ich cofnąć. 

1. W polu wyszukiwania witryny Azure portal wprowadź pliki **usługi Azure NetApp,** a następnie wybierz pozycję **Pliki netapp platformy Azure** z wyświetlona lista.

2. Na liście subskrypcji kliknij grupę zasobów (myRG1), którą chcesz usunąć. 

    ![Przechodzenie do grup zasobów](../media/azure-netapp-files/azure-netapp-files-azure-navigate-to-resource-groups.png)


3. Na stronie grupy zasobów kliknij pozycję **Usuń grupę zasobów**.

    ![Usuwanie grupy zasobów](../media/azure-netapp-files/azure-netapp-files-azure-delete-resource-group.png) 

    Zostanie otwarte okno zawierające ostrzeżenie dotyczące zasobów, które zostaną usunięte razem z grupą zasobów.

4. Wprowadź nazwę grupy zasobów (myRG1), aby potwierdzić, że chcesz trwale usunąć grupę zasobów i wszystkie zasoby w niej, a następnie kliknij przycisk **Usuń**.

    ![Usuwanie grupy zasobów](../media/azure-netapp-files/azure-netapp-files-azure-confirm-resource-group-deletion.png ) 

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)

Po zakończeniu i jeśli chcesz, możesz usunąć grupę zasobów. Akcja usuwania grupy zasobów jest nieodwracalna.  

> [!IMPORTANT]
> Wszystkie zasoby w grupach zasobów zostaną trwale usunięte i nie można ich cofnąć.

1. Usuń grupę zasobów za pomocą polecenia [Usuń-AzResourceGroup.](/powershell/module/az.resources/remove-azresourcegroup)
   
    ```powershell-interactive
    Remove-AzResourceGroup -Name $resourceGroup
    ```

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

Po zakończeniu i jeśli chcesz, możesz usunąć grupę zasobów. Akcja usuwania grupy zasobów jest nieodwracalna.  

> [!IMPORTANT]
> Wszystkie zasoby w grupach zasobów zostaną trwale usunięte i nie można ich cofnąć.

1. Usuń grupę zasobów za pomocą polecenia [usuń grupę az.](/cli/azure/group#az-group-delete)
   
    ```azurecli-interactive
    az group delete \
        --name $RESOURCE_GROUP
    ```
---

## <a name="next-steps"></a>Następne kroki  

> [!div class="nextstepaction"]
> [Zarządzanie woluminami przy użyciu plików NetApp platformy Azure](azure-netapp-files-manage-volumes.md)  
