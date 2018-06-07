---
title: Dodawanie elementu stosu Azure marketplace z lokalnego źródła | Dokumentacja firmy Microsoft
description: Opisuje sposób dodawania obrazu systemu operacyjnego lokalnego w portalu Azure Marketplace stosu.
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: PowerShell
ms.topic: tutorial
ms.custom: mvc
ms.date: 04/20/2018
ms.author: jeffgilb
ms.reviewer: misainat
ms.openlocfilehash: a093e60718881b2fe9ca70df7596e8963dc55d9f
ms.sourcegitcommit: 6cf20e87414dedd0d4f0ae644696151e728633b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/06/2018
ms.locfileid: "34808047"
---
# <a name="tutorial-add-an-azure-stack-marketplace-item-from-a-local-source"></a>Samouczek: Dodawanie elementu stosu Azure marketplace z lokalnego źródła

Jako Operator stosu Azure w pierwszej kolejności należy wykonać, aby użytkownicy mogli wdrażać maszyny wirtualnej (VM) jest dodanie obrazu maszyny Wirtualnej do stosu Azure marketplace. Domyślnie nic nie jest opublikowana w stosie Azure marketplace, ale może przekazywać obrazy ISO maszyny Wirtualnej, aby udostępnić je użytkownikom. Ta opcja została wdrożona stosu Azure w scenariuszu odłączonego ani w zastosowaniach z ograniczona łączność.

W tym samouczku, Pobierz obraz maszyny Wirtualnej systemu Windows Server 2016 z oceny systemu Windows Server strony i przekaż go do stosu Azure marketplace.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Dodaj obraz maszyny Wirtualnej systemu Windows Server 2016
> * Sprawdź, czy obraz maszyny Wirtualnej jest dostępny 
> * Testowanie obrazu maszyny Wirtualnej

## <a name="prerequisites"></a>Wymagania wstępne

W celu ukończenia tego samouczka:

- Zainstaluj [modułów programu Azure PowerShell platformy Azure zgodnego stosu](asdk-post-deploy.md#install-azure-stack-powershell)
- Pobierz [narzędzia Azure stosu](asdk-post-deploy.md#download-the-azure-stack-tools)
- Pobierz [obrazu ISO maszyny wirtualnej systemu Windows Server 2106](https://www.microsoft.com/en-us/evalcenter/evaluate-windows-server-2016) ze strony systemu Windows Server ocen

## <a name="add-a-windows-server-vm-image"></a>Dodaj obraz maszyny Wirtualnej systemu Windows Server
Obraz systemu Windows Server 2016 stosu Azure marketplace można publikować przez dodanie pobrane wcześniej obrazu ISO za pomocą programu PowerShell. 

Ta opcja została wdrożona stosu Azure w scenariuszu odłączonego ani w zastosowaniach z ograniczona łączność.

1. [Instalowanie programu PowerShell dla usługi Azure stosu](../azure-stack-powershell-install.md).

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

3. Zaloguj się do stosu Azure jako operatora. Aby uzyskać instrukcje, zobacz [Zaloguj się do stosu Azure jako operator](../azure-stack-powershell-configure-admin.md).

   ````PowerShell  
    Add-AzureRmAccount `
      -EnvironmentName "AzureStackAdmin" `
      -TenantId $TenantID
  ````

4. Dodaj obraz systemu Windows Server 2016 do stosu Azure marketplace.

    **AzsPlatformimage Dodaj** polecenia cmdlet umożliwiają dodanie obrazu określa wartości używane przez Szablony usługi Azure Resource Manager, aby odwołać obrazu maszyny Wirtualnej.
    
    Wartości:
    
  - **publisher**  
    Na przykład: `Microsoft`  
    Segment nazwy wydawcy obrazu maszyny Wirtualnej, wykorzystywanym przez użytkowników po wdrożeniu przez nich obrazu. Na przykład **Microsoft**. Nie dołączaj spację lub inne znaki specjalne w tym polu.  
  - **offer**  
    Na przykład: `WindowsServer`  
    Oferta segment nazwę obrazu maszyny Wirtualnej, wykorzystywanym przez użytkowników po wdrożeniu przez nich obrazu maszyny Wirtualnej. Na przykład **Windows Server**. Nie dołączaj spację lub inne znaki specjalne w tym polu.  
  - **sku**  
    Na przykład: `Datacenter2016`  
    Segment nazwa jednostki SKU obrazu maszyny Wirtualnej, wykorzystywanym przez użytkowników po wdrożeniu przez nich obrazu maszyny Wirtualnej. Na przykład **Datacenter2016**. Nie dołączaj spację lub inne znaki specjalne w tym polu.  
  - **Wersja**  
    Na przykład: `1.0.0`  
    Wersja obrazu maszyny Wirtualnej, wykorzystywanym przez użytkowników po wdrożeniu przez nich obrazu maszyny Wirtualnej. Ta wersja jest w formacie *\#.\#.\#*. Na przykład **1.0.0**. Nie dołączaj spację lub inne znaki specjalne w tym polu.  
  - **osType**  
    Na przykład: `Windows`  
    OsType obrazu musi być równa albo **Windows** lub **Linux**. Zastąp *fully_qualified_path_to_ISO* ze ścieżką do ISO systemu Windows Server 2016, pobrany. 
  - **OSUri**  
    Na przykład: `https://storageaccount.blob.core.windows.net/vhds/Ubuntu1404.vhd`  
    Można określić magazynu obiektów blob identyfikatora URI dla `osDisk`. W przypadku jego będzie określić miejsce przechowywania obrazu, który został pobrany.

    Aby uzyskać więcej informacji, zobacz dokumentację programu PowerShell dla [AzsPlatformimage Dodaj](https://docs.microsoft.com/powershell/module/azs.compute.admin/add-azsplatformimage) polecenia cmdlet.

    Otwórz program PowerShell z podniesionego wiersza, a następnie uruchom:

      ````PowerShell  
        Add-AzsPlatformimage -publisher "Microsoft" -offer "WindowsServer" -sku "Datacenter2016" -version "1.0.0” -OSType "Windows" -OSUri "<fully_qualified_path_to_ISO>"
      ````  

## <a name="verify-the-marketplace-item-is-available"></a>Sprawdź, czy element marketplace jest dostępny
Wykonaj te kroki, aby Sprawdź, czy nowy obraz maszyny Wirtualnej jest dostępna w witrynie marketplace stosu Azure.

1. Zaloguj się do [portalu administracyjnego ASDK](https://adminportal.local.azurestack.external).

2. Wybierz **więcej usług** > **zarządzania Marketplace**. 

3. Sprawdź, czy obraz maszyny Wirtualnej systemu Windows Server 2016 centrum danych został pomyślnie dodany.

   ![Pobrany obraz z platformy Azure](media/asdk-marketplace-item/azs-marketplace-ws2016.png)

## <a name="test-the-vm-image"></a>Testowanie obrazu maszyny Wirtualnej
Operator stosu Azure, można użyć [portalu administratora](https://adminportal.local.azurestack.external) do utworzenia testu maszyny Wirtualnej, aby sprawdzić poprawność obraz został udostępniony pomyślnie. 

1. Zaloguj się do [portalu administracyjnego ASDK](https://adminportal.local.azurestack.external).

2. Kliknij przycisk **nowe** > **obliczeniowe** > **systemu Windows Server 2016 Datacenter** > **utworzyć**.  
 ![Tworzenie maszyny Wirtualnej z obrazu witryny marketplace](media/asdk-marketplace-item/new-compute.png)

3. W **podstawy** bloku, wprowadź następujące informacje, a następnie kliknij przycisk **OK**:
  - **Nazwa**: test-vm-1
  - **Nazwa użytkownika**: AdminTestUser
  - **Hasło**: AzS TestVM01
  - **Subskrypcja**: Zaakceptuj domyślny dostawca subskrypcji
  - **Grupa zasobów**: test-vm-zarządcy zasobów
  - **Lokalizacja**: lokalne

4. W **wybierz rozmiar** bloku, kliknij przycisk **A1 standardowe**, a następnie kliknij przycisk **wybierz**.  

5. W **ustawienia** bloku, zaakceptuj ustawienia domyślne i kliknij przycisk **OK**

6. W bloku **Podsumowanie** kliknij przycisk **OK**, aby utworzyć maszynę wirtualną.  
> [!NOTE]
> Wdrożenie maszyny wirtualnej może zająć kilka minut.

7. Aby wyświetlić do nowej maszyny Wirtualnej, kliknij przycisk **maszyn wirtualnych**, następnie wyszukaj **test-vm-1** i kliknij jego nazwę.
    ![Pierwszy obraz maszyny Wirtualnej testu wyświetlany](media/asdk-marketplace-item/first-test-vm.png)

## <a name="clean-up-resources"></a>Oczyszczanie zasobów
Po pomyślnie zalogowany do maszyny Wirtualnej i zweryfikować, że obraz test działa prawidłowo, należy usunąć grupy zasobów testu. Spowoduje to zwolnienie ograniczone zasoby dostępne do instalacji ASDK jednym węzłem.

Gdy nie są już potrzebne, Usuń grupę zasobów, maszyny Wirtualnej i wszystkie powiązane zasoby wykonaj następujące czynności:

1. Zaloguj się do [portalu administracyjnego ASDK](https://adminportal.local.azurestack.external).
2. Kliknij przycisk **grup zasobów** > **test-vm-zarządcy zasobów** > **Usuń grupę zasobów**.
3. Typ **test-vm-zarządcy zasobów** Nazwa grupy zasobów, a następnie kliknij przycisk **usunąć**.

## <a name="next-steps"></a>Kolejne kroki

W niniejszym samouczku zawarto informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Dodaj obraz maszyny Wirtualnej systemu Windows Server 2016
> * Sprawdź, czy obraz maszyny Wirtualnej jest dostępny 
> * Testowanie obrazu maszyny Wirtualnej

Przejdź do następnego samouczkiem, aby dowiedzieć się, jak utworzyć ofertę stosu Azure i plan.

> [!div class="nextstepaction"]
> [Oferty usług Azure IaaS stosu](asdk-offer-services.md)
