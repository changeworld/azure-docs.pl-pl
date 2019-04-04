---
title: Dodaj obraz maszyny Wirtualnej do usługi Azure Stack | Dokumentacja firmy Microsoft
description: Dodaj obraz maszyny Wirtualnej lub Usuń obraz niestandardowy Windows w organizacji lub obraz maszyny Wirtualnej systemu Linux dla dzierżawców.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: PowerShell
ms.topic: conceptual
ms.date: 04/02/2019
ms.author: mabrigg
ms.reviewer: kivenkat
ms.lastreviewed: 06/08/2018
ms.openlocfilehash: 9e20abfde8a4524b00e60651bbe71135d357a237
ms.sourcegitcommit: a60a55278f645f5d6cda95bcf9895441ade04629
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/03/2019
ms.locfileid: "58881464"
---
# <a name="add-a-vm-image-to-offer-in-azure-stack"></a>Dodawanie obrazu maszyny Wirtualnej, aby oferować się w usłudze Azure Stack

*Dotyczy Zintegrowane usługi Azure Stack, systemy i usługi Azure Stack Development Kit*

W usłudze Azure Stack możesz dodać obraz maszyny wirtualnej (VM) w portalu Marketplace, aby udostępnić użytkownikom. Możesz dodać obrazy maszyn wirtualnych przy użyciu szablonów usługi Azure Resource Manager dla usługi Azure Stack. Obrazy maszyn wirtualnych można również dodać do interfejsu użytkownika usługi Azure Marketplace, jako elementu portalu Marketplace. Użyj obrazu, który na globalnym rynku platformy Azure lub własnego niestandardowego obrazu maszyny Wirtualnej. Możesz dodać obraz maszyny Wirtualnej przy użyciu portalu administracyjnego lub środowiska Windows PowerShell.

## <a name="add-a-vm-image-through-the-portal"></a>Dodaj obraz maszyny Wirtualnej za pośrednictwem portalu

> [!NOTE]  
> Przy użyciu tej metody należy utworzyć osobno elementu portalu Marketplace.

Obrazy muszą dawać mogą być przywoływane przez identyfikator URI magazynu obiektów blob. Przygotowywanie obrazu systemu operacyjnego Windows lub Linux w formacie VHD (nie VHDX), a następnie przekazać obraz na konto magazynu na platformie Azure lub usługi Azure Stack. Jeśli obraz został już przekazany do magazynu obiektów blob na platformie Azure lub usługi Azure Stack, możesz pominąć krok 1.

1. [Przekazywanie obrazu maszyny Wirtualnej Windows Azure dla wdrożeń usługi Resource Manager](https://azure.microsoft.com/documentation/articles/virtual-machines-windows-upload-image/) lub obrazu systemu Linux, wykonaj instrukcje opisane w [wdrażanie maszyn wirtualnych systemu Linux w usłudze Azure Stack](azure-stack-linux.md). Przed przekazaniem obrazu, należy wziąć pod uwagę następujące czynniki:

   - Usługa Azure Stack, tylko obsługuje generowanie jeden (1) maszyna wirtualna w dysku stałego wirtualnego dysku twardego formatu. Stałym formacie struktury dysku logicznego liniowo w pliku, tak że Przesunięcie X na dysku jest przechowywany w obiekcie blob z przesunięciem X. Małe stopka na końcu obiektu blob opisuje właściwości wirtualnego dysku twardego. Aby upewnić się, jeśli dysk jest stała, należy użyć [Get-VHD](https://docs.microsoft.com/powershell/module/hyper-v/get-vhd?view=win10-ps) polecenia programu PowerShell.  

     > [!IMPORTANT]  
     >  Usługa Azure Stack nie obsługuje dysków dynamicznych wirtualnych dysków twardych. Zmiana rozmiaru dysku dynamicznego, który jest dołączony do maszyny Wirtualnej spowoduje, że maszyna wirtualna w stanie niepowodzenia. Aby rozwiązać ten problem, należy usunąć maszynę Wirtualną bez usuwania dysku maszyny Wirtualnej, obiektu blob dysku VHD na koncie magazynu. Konwertowania wirtualnego dysku twardego z dysk dynamiczny na dysk stały i ponownie utworzyć maszynę wirtualną.

   - Jest bardziej wydajne do przekazania obrazu do magazynu obiektów blob usługi Azure Stack, niż na platformie Azure blob storage ponieważ zajmuje mniej czasu, aby wypchnąć obraz do repozytorium obrazów usługi Azure Stack.

   - Podczas przekazywania [obrazu maszyny Wirtualnej Windows](https://azure.microsoft.com/documentation/articles/virtual-machines-windows-upload-image/), upewnij się zastąpić **logowania do platformy Azure** krok z [konfigurowania środowiska PowerShell operatora infrastruktury Azure Stack](azure-stack-powershell-configure-admin.md) kroku.  

   - Zanotuj identyfikator URI, gdzie możesz przekazać obraz magazynu obiektów blob. Identyfikator URI magazynu obiektów blob ma następujący format: *&lt;storageAccount&gt;/&lt;blobContainer&gt;/&lt;targetVHDName&gt;* VHD.

   - Aby udostępnić obiekt blob anonimowo, przejdź do kontenera obiektów blob konta magazynu gdzie został przekazany obraz maszyny Wirtualnej wirtualnego dysku twardego. Wybierz **Blob**, a następnie wybierz pozycję **zasady dostępu**. Opcjonalnie możesz wygenerować sygnaturę dostępu współdzielonego dla kontenera i dołączyć go jako część identyfikatora URI obiektu blob. Ten krok zapewnia, że obiekt blob jest dostępna do użytku z Dodawanie tego elementu jako obraz. Jeśli obiekt blob nie jest dostępne anonimowo, obraz maszyny Wirtualnej zostanie utworzony się w stanie niepowodzenia.

     ![Przejdź do obiektów blob konta magazynu](./media/azure-stack-add-vm-image/image1.png)

     ![Określ dostęp do obiektów blob na publiczną](./media/azure-stack-add-vm-image/image2.png)

2. Zaloguj się do usługi Azure Stack jako operator. Wybierz z menu **wszystkich usług** > **obrazów** w obszarze **obliczenia** > **Dodaj**.

3. W obszarze **Tworzenie obrazu**, wprowadź nazwy, subskrypcji, grupy zasobów, lokalizację, dysk systemu operacyjnego, typ systemu operacyjnego, identyfikator URI obiektu blob magazynu, typ konta i obsługi pamięci podręcznej. Następnie wybierz **Utwórz** umożliwiającą utworzenie obrazu maszyny Wirtualnej.

   ![Początkowy do tworzenia obrazu](./media/azure-stack-add-vm-image/image4.png)

   Po pomyślnym utworzeniu obrazu, stan obrazu maszyny Wirtualnej zmieni się na **Powodzenie**.

4. Aby wprowadzić obraz maszyny wirtualnej jest bardziej dostępny do użycia przez użytkowników w interfejsie użytkownika, jest dobrym rozwiązaniem [Tworzenie elementu portalu Marketplace](azure-stack-create-and-publish-marketplace-item.md).

## <a name="remove-a-vm-image-through-the-portal"></a>Usuń obraz maszyny Wirtualnej za pośrednictwem portalu

1. Otwórz w portalu administratora w [ https://adminportal.local.azurestack.external ](https://adminportal.local.azurestack.external).

2. Wybierz **zarządzania Marketplace**, a następnie wybierz maszynę Wirtualną, czy chcesz usunąć.

3. Kliknij polecenie **Usuń**.

## <a name="add-a-vm-image-to-the-marketplace-by-using-powershell"></a>Dodawanie obrazu maszyny Wirtualnej w portalu Marketplace przy użyciu programu PowerShell

> [!Note]  
> Po dodaniu obraz tylko będą dostępne dla usługi Azure Resource Manager, na podstawie szablonów i wdrożeń programu PowerShell. Aby udostępnić obraz użytkowników jako elementu portalu marketplace publikowanie elementu portalu marketplace, wykonując kroki opisane w artykule, [tworzenie i publikowanie elementu portalu Marketplace](https://docs.microsoft.com/azure/azure-stack/azure-stack-create-and-publish-marketplace-item)

1. [Instalowanie programu PowerShell dla usługi Azure Stack](azure-stack-powershell-install.md).  

2. Zaloguj się do usługi Azure Stack jako operator. Aby uzyskać instrukcje, zobacz [Zaloguj się do usługi Azure Stack jako operator](azure-stack-powershell-configure-admin.md).

3. Otwórz program PowerShell z podwyższonym poziomem uprawnień wiersza i uruchom:

   ```powershell
    Add-AzsPlatformimage -publisher "<publisher>" `
      -offer "<offer>" `
      -sku "<sku>" `
      -version "<#.#.#>” `
      -OSType "<ostype>" `
      -OSUri "<osuri>"
   ```

   **AzsPlatformimage Dodaj** polecenia cmdlet określa wartości używane przez Szablony usługi Azure Resource Manager, aby odwoływać się do obrazu maszyny Wirtualnej. Wartości:
   - **Wydawcy**  
     Na przykład: `Canonical`  
     Segment nazwy wydawcy obrazu maszyny Wirtualnej, używanego przez użytkowników po wdrożeniu przez nich obraz. Na przykład **Microsoft**. Nie dołączaj spacji ani innych znaków specjalnych w tym polu.  
   - **oferty**  
     Na przykład: `UbuntuServer`  
     Segment nazwę oferty obrazu maszyny Wirtualnej, używanego przez użytkowników po wdrożeniu przez nich obrazu maszyny Wirtualnej. Na przykład **WindowsServer**. Nie dołączaj spacji ani innych znaków specjalnych w tym polu.  
   - **sku**  
     Na przykład: `14.04.3-LTS`  
     Segment nazwy jednostki SKU obrazu maszyny Wirtualnej, używanego przez użytkowników po wdrożeniu przez nich obrazu maszyny Wirtualnej. Na przykład **Datacenter2016**. Nie dołączaj spacji ani innych znaków specjalnych w tym polu.  
   - **version**  
     Na przykład: `1.0.0`  
     Wersja obrazu maszyny Wirtualnej, używanego przez użytkowników po wdrożeniu przez nich obrazu maszyny Wirtualnej. Ta wersja jest w formacie *\#.\#.\#*. Na przykład **1.0.0**. Nie dołączaj spacji ani innych znaków specjalnych w tym polu.  
   - **osType**  
     Na przykład: `Linux`  
     OsType obrazu musi być albo **Windows** lub **Linux**.  
   - **OSUri**  
     Na przykład: `https://storageaccount.blob.core.windows.net/vhds/Ubuntu1404.vhd`  
     Możesz określić identyfikator URI magazynu obiektów blob dla `osDisk`.  

     Aby uzyskać więcej informacji, zobacz informacje dotyczące w programie PowerShell [AzsPlatformimage Dodaj](https://docs.microsoft.com/powershell/module/azs.compute.admin/add-azsplatformimage) polecenia cmdlet i [New DataDiskObject](https://docs.microsoft.com/powershell/module/Azs.Compute.Admin/New-DataDiskObject) polecenia cmdlet.

## <a name="add-a-custom-vm-image-to-the-marketplace-by-using-powershell"></a>Dodawanie niestandardowego obrazu maszyny Wirtualnej w portalu Marketplace przy użyciu programu PowerShell
 
1. [Instalowanie programu PowerShell dla usługi Azure Stack](azure-stack-powershell-install.md).

   ```powershell
    # Create the Azure Stack operator's Azure Resource Manager environment by using the following cmdlet:
    Add-AzureRMEnvironment `
      -Name "AzureStackAdmin" `
      -ArmEndpoint $ArmEndpoint

    Set-AzureRmEnvironment `
      -Name "AzureStackAdmin" `
      -GraphAudience $GraphAudience

    $TenantID = Get-AzsDirectoryTenantId `
      -AADTenantName "<myDirectoryTenantName>.onmicrosoft.com" `
      -EnvironmentName AzureStackAdmin

    Add-AzureRmAccount `
      -EnvironmentName "AzureStackAdmin" `
      -TenantId $TenantID
   ```

2. Jeśli przy użyciu **Active Directory Federation Services**, użyj następującego polecenia cmdlet:

   ```powershell
   # For Azure Stack Development Kit, this value is set to https://adminmanagement.local.azurestack.external. To get this value for Azure Stack integrated systems, contact your service provider.
   $ArmEndpoint = "<Resource Manager endpoint for your environment>"

   # For Azure Stack Development Kit, this value is set to https://graph.local.azurestack.external/. To get this value for Azure Stack integrated systems, contact your service provider.
   $GraphAudience = "<GraphAudience endpoint for your environment>"

   # Create the Azure Stack operator's Azure Resource Manager environment by using the following cmdlet:
   Add-AzureRMEnvironment `
    -Name "AzureStackAdmin" `
    -ArmEndpoint $ArmEndpoint
    ```

3. Zaloguj się do usługi Azure Stack jako operator. Aby uzyskać instrukcje, zobacz [Zaloguj się do usługi Azure Stack jako operator](azure-stack-powershell-configure-admin.md).

4. Utwórz konto magazynu w globalnym platformy Azure lub usługi Azure Stack, do przechowywania Twojego niestandardowego obrazu maszyny Wirtualnej. Aby uzyskać instrukcje zobacz [Szybki Start: przekazywanie, pobieranie i wyświetlanie listy obiektów blob za pomocą witryny Azure Portal](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal).

5. Przygotowywanie obrazu systemu operacyjnego Windows lub Linux w formacie VHD (nie VHDX), przekazania obrazu do swojego konta magazynu i Pobierz identyfikator URI, do której można pobrać obrazu maszyny Wirtualnej programu PowerShell.  

   ```powershell
    Add-AzureRmAccount `
      -EnvironmentName "AzureStackAdmin" `
      -TenantId $TenantID
   ```

6. (Opcjonalnie) Możesz przekazać tablicę dysków z danymi w ramach obrazu maszyny Wirtualnej. Tworzenie dysków danych za pomocą polecenia cmdlet New-DataDiskObject. Otwórz program PowerShell w wierszu polecenia z podwyższonym poziomem uprawnień i uruchom:

   ```powershell
    New-DataDiskObject -Lun 2 `
    -Uri "https://storageaccount.blob.core.windows.net/vhds/Datadisk.vhd"
   ```

7. Otwórz program PowerShell z podwyższonym poziomem uprawnień wiersza i uruchom:

   ```powershell
    Add-AzsPlatformimage -publisher "<publisher>" -offer "<offer>" -sku "<sku>" -version "<#.#.#>” -OSType "<ostype>" -OSUri "<osuri>"
   ```

    Aby uzyskać więcej informacji na temat polecenia cmdlet Add-AzsPlatformimage i polecenia cmdlet New-DataDiskObject zobacz Microsoft PowerShell [Dokumentacja modułu usługi Azure Stack operatora](https://docs.microsoft.com/powershell/module/).

## <a name="remove-a-vm-image-by-using-powershell"></a>Usuń obraz maszyny Wirtualnej przy użyciu programu PowerShell

Jeśli nie potrzebujesz już obraz maszyny wirtualnej, który został przekazany, możesz usunąć ją z witryny Marketplace za pomocą następującego polecenia cmdlet:

1. [Instalowanie programu PowerShell dla usługi Azure Stack](azure-stack-powershell-install.md).

2. Zaloguj się do usługi Azure Stack jako operator.

3. Otwórz program PowerShell z podwyższonym poziomem uprawnień wiersza i uruchom:

   ```powershell  
   Remove-AzsPlatformImage `
    -publisher "<publisher>" `
    -offer "<offer>" `
    -sku "<sku>" `
    -version "<version>" `
   ```
   **AzsPlatformImage Usuń** polecenia cmdlet określa wartości używane przez Szablony usługi Azure Resource Manager, aby odwoływać się do obrazu maszyny Wirtualnej. Wartości:
   - **Wydawcy**  
     Na przykład: `Canonical`  
     Segment nazwy wydawcy obrazu maszyny Wirtualnej, używanego przez użytkowników po wdrożeniu przez nich obraz. Na przykład **Microsoft**. Nie dołączaj spacji ani innych znaków specjalnych w tym polu.  
   - **oferty**  
     Na przykład: `UbuntuServer`  
     Segment nazwę oferty obrazu maszyny Wirtualnej, używanego przez użytkowników po wdrożeniu przez nich obrazu maszyny Wirtualnej. Na przykład **WindowsServer**. Nie dołączaj spacji ani innych znaków specjalnych w tym polu.  
   - **sku**  
     Na przykład: `14.04.3-LTS`  
     Segment nazwy jednostki SKU obrazu maszyny Wirtualnej, używanego przez użytkowników po wdrożeniu przez nich obrazu maszyny Wirtualnej. Na przykład **Datacenter2016**. Nie dołączaj spacji ani innych znaków specjalnych w tym polu.  
   - **version**  
     Na przykład: `1.0.0`  
     Wersja obrazu maszyny Wirtualnej, używanego przez użytkowników po wdrożeniu przez nich obrazu maszyny Wirtualnej. Ta wersja jest w formacie *\#.\#.\#*. Na przykład **1.0.0**. Nie dołączaj spacji ani innych znaków specjalnych w tym polu.  
    
     Aby uzyskać więcej informacji na temat polecenia cmdlet Remove-AzsPlatformImage zobacz Microsoft PowerShell [Dokumentacja modułu usługi Azure Stack operatora](https://docs.microsoft.com/powershell/module/).

## <a name="next-steps"></a>Kolejne kroki

[Inicjowanie obsługi administracyjnej maszyny wirtualnej](azure-stack-provision-vm.md)
