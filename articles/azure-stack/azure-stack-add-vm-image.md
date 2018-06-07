---
title: Dodawanie i usuwanie obraz maszyny Wirtualnej Azure stos | Dokumentacja firmy Microsoft
description: Dodaj lub usuń organizacji niestandardowego systemu Windows lub Linux VM obrazu dla dzierżawców.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: e5a4236b-1b32-4ee6-9aaa-fcde297a020f
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: PowerShell
ms.topic: get-started-article
ms.date: 05/24/2018
ms.author: mabrigg
ms.reviewer: kivenkat
ms.openlocfilehash: 309bd35eabe1d5c4af9a35d5f2b879bce225a402
ms.sourcegitcommit: 680964b75f7fff2f0517b7a0d43e01a9ee3da445
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34604373"
---
# <a name="make-a-virtual-machine-image-available-in-azure-stack"></a>Udostępnić obraz maszyny wirtualnej Azure stosu

*Dotyczy: Azure stosu zintegrowanych systemów i Azure stosu Development Kit*

W stosie Azure udostępnieniem obrazy maszyny wirtualnej dla użytkowników. Obrazy te można odwoływać się za pomocą szablonów usługi Azure Resource Manager lub dodać je do interfejsu użytkownika portalu Marketplace Azure jako element Marketplace. Możesz użyć albo obraz formularza globalnym rynku Azure lub Dodaj niestandardowy obraz. Można dodać maszyny Wirtualnej za pomocą portalu lub środowiska Windows PowerShell.

## <a name="add-a-vm-image-through-the-portal"></a>Dodaj obraz maszyny Wirtualnej za pośrednictwem portalu

> [!NOTE]
> Przy użyciu tej metody należy utworzyć portalu Marketplace oddzielnie.

Obrazy można musi odwoływać się do magazynu obiektów blob identyfikatora URI. Przygotuj obraz systemu operacyjnego Windows lub Linux, w formacie VHD (nie VHDX), a następnie Przekaż obraz na konto magazynu Azure lub stos Azure. Jeśli obraz jest już przekazany do magazynu obiektów blob Azure lub stos Azure, można pominąć krok 1.

1. [Przekaż obraz maszyny Wirtualnej systemu Windows Azure dla wdrożenia usługi Resource Manager](https://azure.microsoft.com/documentation/articles/virtual-machines-windows-upload-image/) lub obrazu systemu Linux, wykonaj instrukcje opisane w [wdrażanie maszyn wirtualnych systemu Linux na stosie Azure](azure-stack-linux.md). Przed przekazaniem obrazu, należy wziąć pod uwagę następujące czynniki:

   - Stos Azure obsługuje format wirtualnego dysku twardego dysku o stałym rozmiarze. Stały format konstrukcje dysku logicznego liniowo w pliku, więc ten dysk Przesunięcie X jest przechowywany przy przesunięciu obiektu blob X. Mała stopkę na końcu obiektu blob zawiera opis właściwości wirtualnego dysku twardego. Aby sprawdzić, czy dysk został rozwiązany, należy użyć [Get-VHD](https://docs.microsoft.com/powershell/module/hyper-v/get-vhd?view=win10-ps) polecenia programu PowerShell.  

    > [!IMPORTANT]
    >  Stos Azure nie obsługuje dysków dynamicznych wirtualnych dysków twardych. Zmiana rozmiaru dysku dynamicznego, który jest dołączony do maszyny Wirtualnej spowoduje zamknięcie maszyny Wirtualnej w stanie niepowodzenia. Aby zminimalizować ten problem, należy usunąć maszynę Wirtualną bez usuwania dysku maszyny Wirtualnej, obiektu blob dysku VHD na koncie magazynu. Konwertuj dysku VHD na dysku dynamicznym na dysku o stałym rozmiarze i ponownie utwórz maszynę wirtualną.

   * Jest bardziej wydajne, można przekazać do magazynu obiektów blob Azure stosu niż Azure magazynu obiektów blob ponieważ zajmuje mniej czasu na push obrazu do repozytorium obrazów Azure stosu obrazu.

   * Po przekazaniu [obrazu maszyny Wirtualnej systemu Windows](https://azure.microsoft.com/documentation/articles/virtual-machines-windows-upload-image/), upewnij się zastąpić **logowania do platformy Azure** krok z [konfigurowania środowiska PowerShell operator stosu Azure](azure-stack-powershell-configure-admin.md) kroku.  

   * Zanotuj magazynu obiektów blob identyfikatora URI, których przekazaniem obrazu. Magazyn obiektów blob identyfikatora URI ma następujący format: *&lt;storageAccount&gt;/&lt;blobContainer&gt;/&lt;targetVHDName&gt;* VHD.

   * Aby udostępnić obiektu blob anonimowo, przejdź do kontenera obiektów blob konta magazynu gdzie obrazu wirtualnego dysku twardego maszyny Wirtualnej został przekazany. Wybierz **obiektu Blob**, a następnie wybierz **zasad dostępu**. Opcjonalnie można zamiast tego Generowanie sygnatury dostępu współdzielonego dla kontenera i dołącz ją jako część identyfikator URI obiektu blob.

   ![Przejdź do obiektów blob z konta magazynu](./media/azure-stack-add-vm-image/image1.png)

   ![Zestaw obiektów blob dostęp do publicznych](./media/azure-stack-add-vm-image/image2.png)

2. Zaloguj się do stosu Azure jako operatora. Wybierz z menu **więcej usług** > **dostawców zasobów**. Następnie wybierz opcję **obliczeniowe** > **obrazów maszyn wirtualnych** > **Dodaj**.

3. W obszarze **Dodaj obraz maszyny Wirtualnej**, wprowadź wydawcy, oferty, jednostki SKU i wersji obrazu maszyny wirtualnej. Te segmenty nazwy odnoszą się do obrazu maszyny Wirtualnej w szablonach usługi Resource Manager. Upewnij się wybrać **osType** wartość poprawnie. Aby uzyskać **identyfikator URI obiektu Blob dysku systemu operacyjnego**, wprowadź identyfikator URI obiektu Blob, których obraz został załadowany. Następnie wybierz opcję **Utwórz** aby rozpocząć tworzenie obrazu maszyny Wirtualnej.

   ![Początkowy do tworzenia obrazu](./media/azure-stack-add-vm-image/image4.png)

   Po pomyślnym utworzeniu obrazu, stan obrazu maszyny Wirtualnej zmienia się na **zakończyło się pomyślnie**.

4. Aby obraz maszyny wirtualnej łatwiej dostępne do użycia przez użytkowników w interfejsie użytkownika, jest dobrym pomysłem jest [utworzyć element Marketplace](azure-stack-create-and-publish-marketplace-item.md).

## <a name="remove-a-vm-image-through-the-portal"></a>Usuń obraz maszyny Wirtualnej za pośrednictwem portalu

1. Otwieranie portalu administracyjnego na [ https://adminportal.local.azurestack.external ](https://adminportal.local.azurestack.external).

2. Wybierz **zarządzania Marketplace**, a następnie wybierz maszynę Wirtualną, które chcesz usunąć.

3. Kliknij polecenie **Usuń**.

## <a name="add-a-vm-image-to-the-marketplace-by-using-powershell"></a>Dodaj obraz maszyny Wirtualnej w portalu Marketplace przy użyciu programu PowerShell

1. [Instalowanie programu PowerShell dla usługi Azure stosu](azure-stack-powershell-install.md).  

2. Zaloguj się do stosu Azure jako operatora. Aby uzyskać instrukcje, zobacz [Zaloguj się do stosu Azure jako operator](azure-stack-powershell-configure-admin.md).

3. Otwórz program PowerShell z podniesionego wiersza, a następnie uruchom:

  ````PowerShell  
    Add-AzsPlatformimage -publisher "<publisher>" `
      -offer "<offer>" `
      -sku "<sku>" `
      -version "<#.#.#>” `
      -OSType "<ostype>" `
      -OSUri "<osuri>"
  ````

  **AzsPlatformimage Dodaj** polecenia cmdlet określa wartości używane przez Szablony usługi Azure Resource Manager, aby odwołać obrazu maszyny Wirtualnej. Wartości:
  - **publisher**  
    Na przykład: `Canonical`  
    Segment nazwy wydawcy obrazu maszyny Wirtualnej, wykorzystywanym przez użytkowników po wdrożeniu przez nich obrazu. Na przykład **Microsoft**. Nie dołączaj spację lub inne znaki specjalne w tym polu.  
  - **offer**  
    Na przykład: `UbuntuServer`  
    Oferta segment nazwę obrazu maszyny Wirtualnej, wykorzystywanym przez użytkowników po wdrożeniu przez nich obrazu maszyny Wirtualnej. Na przykład **Windows Server**. Nie dołączaj spację lub inne znaki specjalne w tym polu.  
  - **sku**  
    Na przykład: `14.04.3-LTS`  
    Segment nazwa jednostki SKU obrazu maszyny Wirtualnej, wykorzystywanym przez użytkowników po wdrożeniu przez nich obrazu maszyny Wirtualnej. Na przykład **Datacenter2016**. Nie dołączaj spację lub inne znaki specjalne w tym polu.  
  - **Wersja**  
    Na przykład: `1.0.0`  
    Wersja obrazu maszyny Wirtualnej, wykorzystywanym przez użytkowników po wdrożeniu przez nich obrazu maszyny Wirtualnej. Ta wersja jest w formacie *\#.\#.\#*. Na przykład **1.0.0**. Nie dołączaj spację lub inne znaki specjalne w tym polu.  
  - **osType**  
    Na przykład: `Linux`  
    OsType obrazu musi być równa albo **Windows** lub **Linux**.  
  - **OSUri**  
    Na przykład: `https://storageaccount.blob.core.windows.net/vhds/Ubuntu1404.vhd`  
    Można określić magazynu obiektów blob identyfikatora URI dla `osDisk`.  

    Aby uzyskać więcej informacji, zobacz dokumentację programu PowerShell dla [AzsPlatformimage Dodaj](https://docs.microsoft.com/powershell/module/azs.compute.admin/add-azsplatformimage) polecenia cmdlet i [DataDiskObject nowy](https://docs.microsoft.com/powershell/module/Azs.Compute.Admin/New-DataDiskObject) polecenia cmdlet.

## <a name="add-a-custom-vm-image-to-the-marketplace-by-using-powershell"></a>Dodawanie niestandardowego obrazu maszyny Wirtualnej w portalu Marketplace przy użyciu programu PowerShell

1. [Instalowanie programu PowerShell dla usługi Azure stosu](azure-stack-powershell-install.md).

  ```PowerShell  
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

  ```PowerShell
  # For Azure Stack Development Kit, this value is set to https://adminmanagement.local.azurestack.external. To get this value for Azure Stack integrated systems, contact your service provider.
  $ArmEndpoint = "<Resource Manager endpoint for your environment>"

  # For Azure Stack Development Kit, this value is set to https://graph.local.azurestack.external/. To get this value for Azure Stack integrated systems, contact your service provider.
  $GraphAudience = "<GraphAuidence endpoint for your environment>"

  # Create the Azure Stack operator's Azure Resource Manager environment by using the following cmdlet:
  Add-AzureRMEnvironment `
    -Name "AzureStackAdmin" `
    -ArmEndpoint $ArmEndpoint
    ```

3. Zaloguj się do stosu Azure jako operatora. Aby uzyskać instrukcje, zobacz [Zaloguj się do stosu Azure jako operator](azure-stack-powershell-configure-admin.md).

4. Utwórz konto magazynu w globalnej Azure lub stos Azure do przechowywania niestandardowego obrazu maszyny Wirtualnej. Instrukcje można znaleźć [Szybki Start: przekazywanie, pobieranie i listę obiektów blob przy użyciu portalu Azure](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal).

5. Przygotuj obraz systemu operacyjnego Windows lub Linux, w formacie VHD (nie VHDX), Przekaż obraz do swojego konta magazynu i Pobierz identyfikator URI, których można pobrać obrazu maszyny Wirtualnej przez programu PowerShell.  

  ````PowerShell  
    Add-AzureRmAccount `
      -EnvironmentName "AzureStackAdmin" `
      -TenantId $TenantID
  ````

6. (Opcjonalnie) Możesz przekazać tablicy dysków danych jako część obrazu maszyny Wirtualnej. Tworzenie dysków danych za pomocą polecenia cmdlet New-DataDiskObject. Otwórz program PowerShell z podniesionego wiersza, a następnie uruchom:

  ````PowerShell  
    New-DataDiskObject -Lun 2 `
    -Uri "https://storageaccount.blob.core.windows.net/vhds/Datadisk.vhd"
  ````

7. Otwórz program PowerShell z podniesionego wiersza, a następnie uruchom:

  ````PowerShell  
    Add-AzsPlatformimage -publisher "<publisher>" -offer "<offer>" -sku "<sku>" -version "<#.#.#>” -OSType "<ostype>" -OSUri "<osuri>"
  ````

    Aby uzyskać więcej informacji na temat polecenia cmdlet Add-AzsPlatformimage i polecenia cmdlet New-DataDiskObject Zobacz programu PowerShell Microsoft [dokumentacji modułu Azure stosu Operator](https://docs.microsoft.com/powershell/module/).

## <a name="remove-a-vm-image-by-using-powershell"></a>Usuń obraz maszyny Wirtualnej za pomocą programu PowerShell

Jeśli nie ma potrzeby obraz maszyny wirtualnej, który został przekazany, należy ją usunąć z witryny Marketplace, za pomocą następującego polecenia cmdlet:

1. [Instalowanie programu PowerShell dla usługi Azure stosu](azure-stack-powershell-install.md).

2. Zaloguj się do stosu Azure jako operatora.

3. Otwórz program PowerShell z podniesionego wiersza, a następnie uruchom:

  ````PowerShell  
  Remove-AzsPlatformImage `
    -publisher "<publisher>" `
    -offer "<offer>" `
    -sku "<sku>" `
    -version "<version>" `
  ````
  **AzsPlatformImage Usuń** polecenia cmdlet określa wartości używane przez Szablony usługi Azure Resource Manager, aby odwołać obrazu maszyny Wirtualnej. Wartości:
  - **publisher**  
    Na przykład: `Canonical`  
    Segment nazwy wydawcy obrazu maszyny Wirtualnej, wykorzystywanym przez użytkowników po wdrożeniu przez nich obrazu. Na przykład **Microsoft**. Nie dołączaj spację lub inne znaki specjalne w tym polu.  
  - **offer**  
    Na przykład: `UbuntuServer`  
    Oferta segment nazwę obrazu maszyny Wirtualnej, wykorzystywanym przez użytkowników po wdrożeniu przez nich obrazu maszyny Wirtualnej. Na przykład **Windows Server**. Nie dołączaj spację lub inne znaki specjalne w tym polu.  
  - **sku**  
    Na przykład: `14.04.3-LTS`  
    Segment nazwa jednostki SKU obrazu maszyny Wirtualnej, wykorzystywanym przez użytkowników po wdrożeniu przez nich obrazu maszyny Wirtualnej. Na przykład **Datacenter2016**. Nie dołączaj spację lub inne znaki specjalne w tym polu.  
  - **Wersja**  
    Na przykład: `1.0.0`  
    Wersja obrazu maszyny Wirtualnej, wykorzystywanym przez użytkowników po wdrożeniu przez nich obrazu maszyny Wirtualnej. Ta wersja jest w formacie *\#.\#.\#*. Na przykład **1.0.0**. Nie dołączaj spację lub inne znaki specjalne w tym polu.  
    
    Aby uzyskać więcej informacji na temat theRemove AzsPlatformImage polecenia cmdlet, zobacz programu PowerShell Microsoft [dokumentacji modułu Azure stosu Operator](https://docs.microsoft.com/powershell/module/).

## <a name="next-steps"></a>Kolejne kroki

[Inicjowanie obsługi administracyjnej maszyny wirtualnej](azure-stack-provision-vm.md)