---
title: Dodaj obraz maszyny Wirtualnej Azure stos | Dokumentacja firmy Microsoft
description: Dodaj organizacji niestandardowych systemu Windows lub Linux VM obraz dla dzierżawców.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: e5a4236b-1b32-4ee6-9aaa-fcde297a020f
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 04/05/2018
ms.author: mabrigg
ms.openlocfilehash: 3680777439678a93b1283cf94b9f8a173965feeb
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/19/2018
---
# <a name="make-a-custom-virtual-machine-image-available-in-azure-stack"></a>Udostępnić obraz niestandardowy maszyny wirtualnej Azure stosu

*Dotyczy: Azure stosu zintegrowanych systemów i Azure stosu Development Kit*

W stosie Azure operatory można udostępnić obrazy niestandardowe maszyny wirtualnej dla użytkowników. Obrazy te można odwoływać się za pomocą szablonów usługi Azure Resource Manager lub dodać je do interfejsu użytkownika portalu Marketplace Azure jako element Marketplace.

## <a name="add-a-vm-image-to-marketplace-by-using-powershell"></a>Dodaj obraz maszyny Wirtualnej w portalu Marketplace przy użyciu programu PowerShell

Uruchom następujące wymagania wstępne, albo z [zestaw deweloperski](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-remote-desktop) lub z systemu zewnętrznego klienta, w przypadku [połączone za pośrednictwem sieci VPN](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-vpn):

1. [Instalowanie programu PowerShell dla usługi Azure stosu](azure-stack-powershell-install.md).  

2. Pobierz [narzędzia niezbędne do pracy z stosu Azure](azure-stack-powershell-download.md).  

3. Przygotuj obraz wirtualnego dysku twardego systemu operacyjnego Windows lub Linux w formacie VHD (nie używaj w formacie VHDX).

   * Dla obrazów systemu Windows, aby uzyskać instrukcje dotyczące przygotowanie obrazu, zobacz [przekazać obraz maszyny Wirtualnej systemu Windows Azure dla wdrożenia usługi Resource Manager](../virtual-machines/windows/upload-generalized-managed.md).

   * W przypadku obrazów systemu Linux, zobacz [wdrażanie maszyn wirtualnych systemu Linux na stosie Azure](azure-stack-linux.md). Wykonaj kroki, aby przygotować obraz lub użyć istniejącego obrazu systemu Linux stosu Azure, zgodnie z opisem w artykule.    

   Stos Azure obsługuje format wirtualnego dysku twardego dysku o stałym rozmiarze. Stały format konstrukcje dysku logicznego liniowo w pliku, więc ten dysk Przesunięcie X jest przechowywany przy przesunięciu obiektu blob X. Mała stopkę na końcu obiektu blob zawiera opis właściwości wirtualnego dysku twardego. Aby sprawdzić, czy dysk został rozwiązany, należy użyć [Get-VHD](https://docs.microsoft.com/powershell/module/hyper-v/get-vhd?view=win10-ps) polecenia programu PowerShell.  

   > [!IMPORTANT]
   >  Stos Azure nie obsługuje dysków dynamicznych wirtualnych dysków twardych. Zmiana rozmiaru dysku dynamicznego, który jest dołączony do maszyny Wirtualnej spowoduje zamknięcie maszyny Wirtualnej w stanie niepowodzenia. Aby zminimalizować ten problem, należy usunąć maszynę Wirtualną bez usuwania dysku maszyny Wirtualnej, obiektu blob dysku VHD na koncie magazynu. Następnie Konwertuj wirtualny dysk twardy z dysk dynamiczny na dysk stały i Utwórz ponownie maszynę wirtualną.

Aby dodać obraz do portalu Azure Marketplace stosu, wykonaj następujące czynności:

1. Zaimportuj moduły Connect i ComputeAdmin:

   ```powershell
   Set-ExecutionPolicy RemoteSigned

   # Import the Connect and ComputeAdmin modules.
   Import-Module .\Connect\AzureStack.Connect.psm1
   Import-Module .\ComputeAdmin\AzureStack.ComputeAdmin.psm1
   ```

2. Zaloguj się do środowiska Azure stosu. Uruchom jedno z poniższych skryptów, w zależności od tego, czy wdrożyć środowiska Azure stosu przy użyciu usługi Azure Active Directory (Azure AD) lub usługi Active Directory Federation Services (AD FS). (Zastąp usługi Azure AD `tenantName`, `GraphAudience` punktu końcowego, a `ArmEndpoint` wartości w celu uwzględnienia konfiguracji środowiska.)

    * **Usługa Azure Active Directory**. Użyj następującego polecenia cmdlet:

      ```PowerShell
      # For Azure Stack Development Kit, this value is set to https://adminmanagement.local.azurestack.external. To get this value for Azure Stack integrated systems, contact your service provider.
      $ArmEndpoint = "<Resource Manager endpoint for your environment>"

      # For Azure Stack Development Kit, this value is set to https://graph.windows.net/. To get this value for Azure Stack integrated systems, contact your service provider.
      $GraphAudience = "<GraphAuidence endpoint for your environment>"

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

      Connect-AzureRmAccount `
        -EnvironmentName "AzureStackAdmin" `
        -TenantId $TenantID
      ```

   * **Usługi federacyjne Active Directory**. Użyj następującego polecenia cmdlet:

        ```PowerShell
        # For Azure Stack Development Kit, this value is set to https://adminmanagement.local.azurestack.external. To get this value for Azure Stack integrated systems, contact your service provider.
        $ArmEndpoint = "<Resource Manager endpoint for your environment>"

        # For Azure Stack Development Kit, this value is set to https://graph.local.azurestack.external/. To get this value for Azure Stack integrated systems, contact your service provider.
        $GraphAudience = "<GraphAuidence endpoint for your environment>"

        # Create the Azure Stack operator's Azure Resource Manager environment by using the following cmdlet:
        Add-AzureRMEnvironment `
          -Name "AzureStackAdmin" `
          -ArmEndpoint $ArmEndpoint

        Set-AzureRmEnvironment `
          -Name "AzureStackAdmin" `
          -GraphAudience $GraphAudience `
          -EnableAdfsAuthentication:$true

        $TenantID = Get-AzsDirectoryTenantId `
          -ADFS `
          -EnvironmentName AzureStackAdmin

        Connect-AzureRmAccount `
          -EnvironmentName "AzureStackAdmin" `
          -TenantId $TenantID
        ```

3. Dodawanie obrazu maszyny Wirtualnej za pomocą `Add-AzsVMImage` polecenia cmdlet. W `Add-AzsVMImage` polecenia cmdlet, określ `osType` jako systemu Windows lub Linux. Obejmują wydawcy, oferty, jednostki SKU i wersji dla obrazu maszyny Wirtualnej. Informacje o parametrach dozwolonych, zobacz [parametry](#parameters). Parametry są używane przez szablonów usługi Azure Resource Manager, aby odwołać obrazu maszyny Wirtualnej. Poniższy przykład przedstawia Wywoływanie skryptu:

  ```powershell
  Add-AzsVMImage `
    -publisher "Canonical" `
    -offer "UbuntuServer" `
    -sku "14.04.3-LTS" `
    -version "1.0.0" `
    -osType Linux `
    -osDiskLocalPath 'C:\Users\AzureStackAdmin\Desktop\UbuntuServer.vhd' `
  ```


Polecenie wykonuje następujące czynności:

* Uwierzytelnia się w środowisku Azure stosu.
* Przekazuje lokalnego pliku VHD na konto nowo utworzonego magazynu tymczasowego.
* Dodaje obraz maszyny Wirtualnej do repozytorium obrazów maszyn wirtualnych.
* Tworzy element Marketplace.

Aby zweryfikować, że polecenie zostało wykonane pomyślnie, w portalu przejdź do witryny Marketplace. Sprawdź, czy obraz maszyny Wirtualnej jest dostępna w **obliczeniowe** kategorii.

![Pomyślnie dodano obrazu maszyny Wirtualnej](./media/azure-stack-add-vm-image/verify-vm.png)

## <a name="remove-a-vm-image-by-using-powershell"></a>Usuń obraz maszyny Wirtualnej za pomocą programu PowerShell

Jeśli nie ma potrzeby obraz maszyny wirtualnej, który został przekazany, należy ją usunąć z witryny Marketplace, za pomocą następującego polecenia cmdlet:

```powershell
Remove-AzsVMImage `
  -publisher "Canonical" `
  -offer "UbuntuServer" `
  -sku "14.04.3-LTS" `
  -version "1.0.0" `
```

## <a name="parameters"></a>Parametry

| Parametr | Opis |
| --- | --- |
| **publisher** |Segment nazwy wydawcy obrazu maszyny Wirtualnej, wykorzystywanym przez użytkowników po wdrożeniu przez nich obrazu. Na przykład **Microsoft**. Nie dołączaj spację lub inne znaki specjalne w tym polu. |
| **offer** |Oferta segment nazwę obrazu maszyny Wirtualnej, wykorzystywanym przez użytkowników po wdrożeniu przez nich obrazu maszyny Wirtualnej. Na przykład **Windows Server**. Nie dołączaj spację lub inne znaki specjalne w tym polu. |
| **sku** |Segment nazwa jednostki SKU obrazu maszyny Wirtualnej, wykorzystywanym przez użytkowników po wdrożeniu przez nich obrazu maszyny Wirtualnej. Na przykład **Datacenter2016**. Nie dołączaj spację lub inne znaki specjalne w tym polu. |
| **Wersja** |Wersja obrazu maszyny Wirtualnej, wykorzystywanym przez użytkowników po wdrożeniu przez nich obrazu maszyny Wirtualnej. Ta wersja jest w formacie *\#.\#.\#*. Na przykład **1.0.0**. Nie dołączaj spację lub inne znaki specjalne w tym polu. |
| **osType** |OsType obrazu musi być równa albo **Windows** lub **Linux**. |
| **osDiskLocalPath** |Ścieżka lokalna do wirtualnego dysku twardego, czy przekazywany jako obraz maszyny Wirtualnej Azure stos dysk systemu operacyjnego. |
| **dataDiskLocalPaths** |Opcjonalną tablicę ścieżki lokalne dla dysków z danymi, które mogą być przekazywane jako część obrazu maszyny Wirtualnej. |
| **CreateGalleryItem** |Flaga wartości logicznej, która określa, czy utworzyć nowy element w witrynie Marketplace. Domyślnie jest ustawiona **true**. |
| **Tytuł** |Nazwa wyświetlana elementu portalu Marketplace. Domyślnie jest ustawiona `Publisher-Offer-Sku` wartość obrazu maszyny Wirtualnej. |
| **Opis elementu** |Opis elementu portalu Marketplace. |
| **location** |Lokalizacja, w którym powinien zostać opublikowany obrazu maszyny Wirtualnej. Domyślnie ta wartość jest równa **lokalnego**.|
| **osDiskBlobURI** |(Opcjonalnie) Ten skrypt akceptuje również magazynu obiektów Blob identyfikatora URI dla `osDisk`. |
| **dataDiskBlobURIs** |(Opcjonalnie) Ten skrypt akceptuje również tablicy magazynu obiektów Blob identyfikatorów URI dodawania dysków z danymi w obrazie. |

## <a name="add-a-vm-image-through-the-portal"></a>Dodaj obraz maszyny Wirtualnej za pośrednictwem portalu

> [!NOTE]
> Przy użyciu tej metody należy utworzyć portalu Marketplace oddzielnie.

Obrazy można musi odwoływać się do magazynu obiektów Blob identyfikatora URI. Przygotuj obraz systemu operacyjnego Windows lub Linux, w formacie VHD (nie VHDX), a następnie Przekaż obraz na konto magazynu Azure lub stos Azure. Jeśli obraz jest już przekazany do magazynu obiektów Blob Azure lub stos Azure, można pominąć krok 1.

1. [Przekaż obraz maszyny Wirtualnej systemu Windows Azure dla wdrożenia usługi Resource Manager](https://azure.microsoft.com/documentation/articles/virtual-machines-windows-upload-image/) lub obrazu systemu Linux, wykonaj instrukcje opisane w [wdrażanie maszyn wirtualnych systemu Linux na stosie Azure](azure-stack-linux.md). Przed przekazaniem obrazu, należy wziąć pod uwagę następujące czynniki:

   * Stos Azure obsługuje format wirtualnego dysku twardego dysku o stałym rozmiarze. Stały format konstrukcje dysku logicznego liniowo w pliku, więc ten dysk Przesunięcie X jest przechowywany przy przesunięciu obiektu blob X. Mała stopkę na końcu obiektu blob zawiera opis właściwości wirtualnego dysku twardego. Aby sprawdzić, czy dysk został rozwiązany, należy użyć [Get-VHD](https://docs.microsoft.com/powershell/module/hyper-v/get-vhd?view=win10-ps) polecenia programu PowerShell.  

    > [!IMPORTANT]
   >  Stos Azure nie obsługuje dysków dynamicznych wirtualnych dysków twardych. Zmiana rozmiaru dysku dynamicznego, który jest dołączony do maszyny Wirtualnej spowoduje zamknięcie maszyny Wirtualnej w stanie niepowodzenia. Aby zminimalizować ten problem, należy usunąć maszynę Wirtualną bez usuwania dysku maszyny Wirtualnej, obiektu blob dysku VHD na koncie magazynu. Konwertuj dysku VHD na dysku dynamicznym na dysku o stałym rozmiarze i ponownie utwórz maszynę wirtualną.

   * Jest bardziej wydajne, można przekazać obrazu do magazynu obiektów Blob platformy Azure stosu niż do magazynu obiektów Blob platformy Azure, ponieważ zajmuje mniej czasu na push obrazu do repozytorium obrazów Azure stosu.

   * Po przekazaniu [obrazu maszyny Wirtualnej systemu Windows](https://azure.microsoft.com/documentation/articles/virtual-machines-windows-upload-image/), upewnij się zastąpić **logowania do platformy Azure** krok z [konfigurowania środowiska PowerShell operator stosu Azure](azure-stack-powershell-configure-admin.md) kroku.  

   * Zanotuj magazynu obiektów Blob identyfikatora URI, których przekazaniem obrazu. Magazyn obiektów Blob identyfikatora URI ma następujący format: *&lt;storageAccount&gt;/&lt;blobContainer&gt;/&lt;targetVHDName&gt;* VHD.

   * Aby udostępnić obiektu blob anonimowo, przejdź do kontenera obiektów blob konta magazynu gdzie obrazu wirtualnego dysku twardego maszyny Wirtualnej został przekazany. Wybierz **obiektu Blob**, a następnie wybierz **zasad dostępu**. Opcjonalnie można zamiast tego Generowanie sygnatury dostępu współdzielonego dla kontenera i dołącz ją jako część identyfikator URI obiektu blob.

   ![Przejdź do obiektów blob z konta magazynu](./media/azure-stack-add-vm-image/image1.png)

   ![Zestaw obiektów blob dostęp do publicznych](./media/azure-stack-add-vm-image/image2.png)

2. Zaloguj się do stosu Azure jako operatora. Wybierz z menu **więcej usług** > **dostawców zasobów**. Następnie wybierz opcję **obliczeniowe** > **obrazów maszyn wirtualnych** > **Dodaj**.

3. W obszarze **Dodaj obraz maszyny Wirtualnej**, wprowadź wydawcy, oferty, jednostki SKU i wersji obrazu maszyny wirtualnej. Te segmenty nazwy odnoszą się do obrazu maszyny Wirtualnej w szablonach usługi Resource Manager. Upewnij się wybrać **osType** wartość poprawnie. Aby uzyskać **identyfikator URI obiektu Blob dysku systemu operacyjnego**, wprowadź identyfikator URI obiektu Blob, których obraz został załadowany. Następnie wybierz opcję **Utwórz** aby rozpocząć tworzenie obrazu maszyny Wirtualnej.

   ![Początkowy do tworzenia obrazu](./media/azure-stack-add-vm-image/image4.png)

   Po pomyślnym utworzeniu obrazu, stan obrazu maszyny Wirtualnej zmienia się na **zakończyło się pomyślnie**.

4. Aby obraz maszyny wirtualnej łatwiej dostępne do użycia przez użytkowników w interfejsie użytkownika, jest dobrym pomysłem jest [utworzyć element Marketplace](azure-stack-create-and-publish-marketplace-item.md).

## <a name="next-steps"></a>Kolejne kroki

[Inicjowanie obsługi administracyjnej maszyny wirtualnej](azure-stack-provision-vm.md)
