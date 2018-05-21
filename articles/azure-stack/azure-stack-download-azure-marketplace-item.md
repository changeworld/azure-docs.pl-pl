---
title: Pobieranie elementów marketplace z platformy Azure | Dokumentacja firmy Microsoft
description: Operatorowi chmury można pobierać elementów marketplace z platformy Azure na mój wdrożenia stosu Azure.
services: azure-stack
documentationcenter: ''
author: brenduns
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 05/16/2018
ms.author: brenduns
ms.reviewer: jeffgo
ms.openlocfilehash: 69148a0ac9a5761eeee0ab47d83862724583619a
ms.sourcegitcommit: 96089449d17548263691d40e4f1e8f9557561197
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/17/2018
---
# <a name="download-marketplace-items-from-azure-to-azure-stack"></a>Pobieranie elementów marketplace z platformy Azure do stosu Azure

*Dotyczy: Azure stosu zintegrowanych systemów i Azure stosu Development Kit*

Pobieranie elementów z portalu Azure Marketplace operatorowi chmury i udostępnić je w stosie Azure. Elementy, które można wybrać pochodzą z wyselekcjonowanych listę elementów portalu Azure Marketplace przetestowanych i obsługiwane do pracy z stosu Azure. Dodatkowe elementy często są dodawane do tej listy, więc kontynuować sprawdzanie dostępności nową zawartość. 

Istnieją dwa scenariusze nawiązywania połączenia z portalu Azure Marketplace: 

- **Scenariusz połączonych** — która wymaga środowiska Azure stos, aby być połączony z Internetem. Zlokalizuj i pobieranie elementów za pomocą portalu Azure stosu. 
- **Scenariusz bez połączenia lub częściowo połączonych** — która wymaga dostępu do Internetu za pomocą narzędzia zespolonego marketplace można pobrać elementów marketplace. Następnie transferu plików do pobrania odłączonej instalacji stosu Azure. W tym scenariuszu programu PowerShell.

Zobacz [elementów portalu Azure Marketplace stosu Azure](azure-stack-marketplace-azure-items.md) listę elementów marketplace można pobrać.


## <a name="connected-scenario"></a>Scenariusz połączenia
Jeśli stosu Azure nawiązuje połączenie z Internetem, można korzystać z portalu administratora można pobrać elementów marketplace.

### <a name="prerequisites"></a>Wymagania wstępne
Wdrożenia stosu Azure musi mieć połączenie z Internetem i musi być [zarejestrowana w usłudze Azure](azure-stack-register.md).

### <a name="use-the-portal-to-download-marketplace-items"></a>Pobieranie elementów marketplace za pomocą portalu  
1. Zaloguj się do portalu administratora platformy Azure stosu.

2.  Przejrzyj dostępne miejsce, przed pobraniem elementów marketplace. Później po wybraniu elementów do pobrania, możesz porównać rozmiaru pobieranych z dostępnej pojemności. Jeśli pojemność ograniczona, należy rozważyć opcje [Zarządzanie dostępne miejsce](azure-stack-manage-storage-shares.md#manage-available-space). 

    Aby sprawdzić dostępne miejsce w **zarządzania Region** wybierz region, aby eksplorować, a następnie przejdź do **dostawców zasobów** > **magazynu**.

    ![Przejrzyj miejsce do magazynowania](media/azure-stack-download-azure-marketplace-item/storage.png)  

    
3. Otwórz w portalu Azure Marketplace stosu i połączenia z platformą Azure. Aby to zrobić, wybierz **zarządzania Marketplace**, a następnie wybierz **Dodaj z platformy Azure**.

    ![Dodaj z platformy Azure](media/azure-stack-download-azure-marketplace-item/marketplace.png)

    Portal Wyświetla listę elementów, które można pobrać z portalu Azure Marketplace. Możesz kliknąć na każdy element, aby wyświetlić jego opis i dodatkowe informacje o tym, w tym jej rozmiaru pobieranych plików. 

    ![Lista Marketplace](media/azure-stack-download-azure-marketplace-item/image03.png)

4. Wybierz element, a następnie wybierz **Pobierz**. Czasu pobierania różnią się.

    ![Pobieranie wiadomości](media/azure-stack-download-azure-marketplace-item/image04.png)

    Po zakończeniu pobierania, można wdrożyć nowy element marketplace jako operator stosu Azure lub użytkownika.

5. Aby wdrożyć pobrany element, wybierz **+ nowy**, a następnie wyszukaj między kategorie nowy element marketplace. Następnie wybierz element, aby rozpocząć proces wdrażania. Proces może być różna dla elementów marketplace różnych. 

## <a name="disconnected-or-a-partially-connected-scenario"></a>Odłączony lub częściowo połączonego scenariusza

Jeśli stos Azure jest w trybie rozłączonym, bez łączności z Internetem, należy użyć programu PowerShell i *marketplace zespolonego narzędzia* można pobrać elementów marketplace do komputera z połączeniem internetowym. Następnie elementy zostaną przeniesione do środowiska Azure stosu. W środowisku bez połączenia nie można pobrać elementów marketplace przy użyciu portalu Azure stosu. 

Narzędzie zespolonego marketplace można także w przypadku połączonych. 

Istnieją dwie części tego scenariusza:
- **Część 1:** pobrać z portalu Azure Marketplace. Na komputerze z dostępem do Internetu Konfigurowanie programu PowerShell, Pobierz narzędzie zespolonego i następnie Pobierz elementy formularza portalu Azure Marketplace.  
- **Część 2:** przekazywanie i publikowanie w portalu Azure Marketplace stosu. Przenieś pliki pobrane do środowiska Azure stosu zaimportować je do stosu Azure, a następnie opublikuj je w portalu Azure Marketplace stosu.  


### <a name="prerequisites"></a>Wymagania wstępne
- Wdrożenia stosu Azure musi być [zarejestrowana w usłudze Azure](azure-stack-register.md).  

- Na komputerze, który ma łączność z Internetem muszą być **modułu programu PowerShell Azure stosu wersji 1.2.11** lub nowszej. Jeśli jeszcze nie istnieje, [zainstalować określone moduły programu PowerShell Azure stosu](azure-stack-powershell-install.md).  

- Umożliwienie importowania elementu pobrany marketplace [środowiska PowerShell dla operatora stosu Azure](azure-stack-powershell-configure-admin.md) musi być skonfigurowany.  

- Musisz mieć konto magazynu w stosie Azure, z kontenerem publicznie (czyli obiektu blob magazynu). Kontener są używane jako magazyn tymczasowy plików Galeria elementów marketplace. Jeśli nie masz doświadczenia z konta magazynu i kontenerów, zobacz [pracy z obiektami blob - portalu Azure](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal) w dokumentacji platformy Azure.

- Narzędzie zespolonego marketplace jest pobierana podczas pierwszej procedury. 

### <a name="use-the-marketplace-syndication-tool-to-download-marketplace-items"></a>Narzędzie do pobierania elementów marketplace zespolonego marketplace

1. Na komputerze z połączeniem internetowym Otwórz konsolę programu PowerShell jako administrator.

2. Dodaj konto platformy Azure, używany do rejestrowania stosu Azure. Aby dodać konto, w programie PowerShell Uruchom `Add-AzureRmAccount` bez żadnych parametrów. Zostanie wyświetlony monit o wprowadzenie poświadczeń konta platformy Azure i może być konieczne użycie uwierzytelniania wieloskładnikowego 2 na podstawie konfiguracji Twoje konto.

3. Jeśli masz wiele subskrypcji, uruchom następujące polecenie, aby wybrać używanego do rejestracji:  

   ```PowerShell  
   Get-AzureRmSubscription -SubscriptionID '<Your Azure Subscription GUID>' | Select-AzureRmSubscription
   $AzureContext = Get-AzureRmContext
   ```

4. Pobierz najnowszą wersję narzędzia zespolonego marketplace przy użyciu następującego skryptu:  

   ```PowerShell
   # Download the tools archive.
   [Net.ServicePointManager]::SecurityProtocol = [Net.SecurityProtocolType]::Tls12 
   invoke-webrequest https://github.com/Azure/AzureStack-Tools/archive/master.zip `
     -OutFile master.zip

   # Expand the downloaded files.
   expand-archive master.zip `
     -DestinationPath . `
     -Force

   # Change to the tools directory.
   cd .\AzureStack-Tools-master

   ```

5. Zaimportuj moduł zespolonego, a następnie uruchom narzędzie, uruchamiając poniższy skrypt. Zastąp *ścieżkę folderu docelowego* z lokalizacją przechowywania plików, możesz pobrać z portalu Azure Marketplace.   

   ```PowerShell  
   Import-Module .\Syndication\AzureStack.MarketplaceSyndication.psm1

   Sync-AzSOfflineMarketplaceItem `
     -destination "Destination folder path" `
     -AzureTenantID $AzureContext.Tenant.TenantId `
     -AzureSubscriptionId $AzureContext.Subscription.Id  
   ```

6. Po uruchomieniu zostanie wyświetlony monit o podanie poświadczeń konta platformy Azure. Zaloguj się do konta platformy Azure, używany do rejestrowania stosu Azure. Po pomyślnym zainicjowaniu nazwy logowania, powinien zostać wyświetlony ekran, takich jak poniższy obraz, z listy elementów marketplace dostępne.  

   ![Azure podręcznego elementów Marketplace](media/azure-stack-download-azure-marketplace-item/image05.png)

7. Wybierz element, który chcesz pobrać i zanotuj *wersji*. (Możesz wybrać wiele obrazów, przytrzymując *Ctrl* klucza.) Będziesz odwoływać *wersji* podczas importowania elementu w następnej procedurze. 
   
   Można również filtrować listy obrazów za pomocą **Dodaj kryteria** opcji.

8. Wybierz **OK**, a następnie przejrzyj i zaakceptuj postanowienia prawne. 

9. Czas, który pobiera pobrania zależy od rozmiaru elementu. Po zakończeniu pobierania element jest dostępny w folderze określonego w skrypcie. Pobieranie zawiera plik wirtualnego dysku twardego (w przypadku maszyn wirtualnych) lub. Plik ZIP (dla rozszerzeń maszyny wirtualnej). Zawiera również pakietu galerii w *.azpkg* format. (A *.azpkg* pakiet jest *.zip* pliku.)
 

### <a name="import-the-download-and-publish-to-azure-stack-marketplace"></a>Importowanie pobieranie i publikowanie Azure stosu w portalu Marketplace
1. Pliki obrazów maszyn wirtualnych lub szablonów rozwiązania, które masz [wcześniej pobrano](#use-the-marketplace-syndication-tool-to-download-marketplace-items) muszą zostać udostępnione lokalnie do środowiska Azure stosu.  

2. Import. Pliki VHD stos Azure. Aby pomyślnie importować obraz maszyny wirtualnej (VM), musi mieć następujące informacje dotyczące maszyny Wirtualnej:
   - *Wersji*, zgodnie z opisem w kroku 7 poprzedniej procedury.
   - Wartości dla maszyn wirtualnych *wydawcy*, *oferują*, i *sku*. Aby uzyskać te wartości, zmień kopię **.azpkg** plik, aby zmienić rozszerzenie pliku do **.zip**. Następnie można edytorze tekstu Otwórz **DeploymentTemplates\CreateUiDefinition.json**. W pliku JSON, Znajdź *elementu imageReference* sekcji, która zawiera te wartości dla elementu portalu marketplace. W poniższym przykładzie pokazano, jak te informacje są wyświetlane:

     ```json  
     "imageReference": {  
        "publisher": "MicrosoftWindowsServer",  
        "offer": "WindowsServer",  
        "sku": "2016-Datacenter-Server-Core"  
      }
     ```  

   Zaimportuj obraz do stosu Azure za pomocą **AzsPlatformimage Dodaj** polecenia cmdlet. Korzystając z tego polecenia cmdlet, upewnij się zastąpić *wydawcy*, *oferują*i inne wartości parametru z wartościami właściwości obrazu, który jest importowany. Możesz uzyskać *wydawcy*, *oferują*, i *sku* wartości obrazu z pliku tekstowego, który jest pobierany wraz z pliku AZPKG i przechowywany w lokalizacji docelowej . 

   W poniższym skrypcie przykład są używane wartości dla systemu Windows Server 2016 Datacenter — instalacja Server Core maszyny wirtualnej. 

   ```PowerShell  
   Add-AzsPlatformimage `
    -publisher "MicrosoftWindowsServer" `
    -offer "WindowsServer" `
    -sku "2016-Datacenter-Server-Core" `
    -osType Windows `
    -Version "2016.127.20171215" `
    -OsDiskLocalPath "C:\AzureStack-Tools-master\Syndication\Windows-Server-2016-DatacenterCore-20171215-en.us-127GB.vhd" `
   ```
   **Informacje o szablonach rozwiązania:** niektóre szablony mogą zawierać małych 3 MB. Plik wirtualnego dysku twardego o nazwie **fixed3.vhd**. Nie trzeba zaimportować ten plik do stosu Azure. Fixed3.VHD.  Ten plik znajduje się niektóre szablony rozwiązania, aby spełnić wymagania publikowania dotyczące portalu Azure Marketplace.

   Przejrzyj opis szablonów i pobierać i importować wymagania dodatkowe, takie jak wirtualne dyski twarde, które są wymagane do pracy z szablon rozwiązania.

3. Korzystać z portalu administratora, aby przekazać pakiet elementów marketplace (plik .azpkg) do magazynu obiektów Blob platformy Azure stosu. Przekazywanie pakietu udostępnia je do stosu Azure Aby później można opublikować element Azure Marketplace stosu.

   Przekazywanie wymaga posiadania konta magazynu, z kontenerem publicznie (patrz wymagania wstępne dotyczące tego scenariusza)   
   1. W portalu administracyjnym stosu Azure, przejdź do **więcej usług** > **kont magazynu**.  
   
   2. Wybierz konto magazynu z subskrypcji, a następnie w obszarze **usługa BLOB**, wybierz pozycję **kontenery**.  
      ![Usługa blob](media/azure-stack-download-azure-marketplace-item/blob-service.png)  
   
   3. Wybierz kontener chcesz użyć, a następnie wybierz **przekazać** otworzyć **przekazywanie obiektu blob** okienka.  
      ![Kontener](media/azure-stack-download-azure-marketplace-item/container.png)  
   
   4. W okienku przekazywanie obiektu blob, przejdź do plików, które chcesz załadować do pamięci masowej, a następnie wybierz **przekazać**.  
      ![upload](media/azure-stack-download-azure-marketplace-item/upload.png)  

   5. Pliki, które można przekazać są widoczne w okienku kontenera. Wybierz plik, a następnie skopiuj adres URL z **właściwości obiektu Blob** okienka. Podczas importowania elementu portalu marketplace stos Azure będzie używany ten adres URL w następnym kroku.  Na poniższej ilustracji, kontener jest *magazynu obiektów blob-test* i plik jest *Microsoft.WindowsServer2016DatacenterServerCore ARM.1.0.801.azpkg*.  Adres URL jest plik *https://testblobstorage1.blob.local.azurestack.external/blob-test-storage/Microsoft.WindowsServer2016DatacenterServerCore-ARM.1.0.801.azpkg*.  
      ![Właściwości obiektu blob](media/azure-stack-download-azure-marketplace-item/blob-storage.png)  

4.  Publikowanie elementu portalu marketplace stos Azure przy użyciu przy użyciu programu PowerShell **AzsGalleryItem Dodaj** polecenia cmdlet. Na przykład:  
    ```PowerShell  
    Add-AzsGalleryItem `
     -GalleryItemUri "https://mystorageaccount.blob.local.azurestack.external/cont1/Microsoft.WindowsServer2016DatacenterServerCore-ARM.1.0.801.azpkg" `
     –Verbose
    ```
5. Po opublikowaniu elementu galerii, możesz je wyświetlić z **więcej usług** > **Marketplace**.  Jeśli pobieranie szablon rozwiązania, upewnij się, że można dodać żadnego zależnych obrazu wirtualnego dysku twardego dla szablonu rozwiązania.  
  ![Widok portalu marketplace](media/azure-stack-download-azure-marketplace-item/view-marketplace.png)  

> [!NOTE]
> Wraz z wydaniem programu Azure PowerShell stosu 1.3.0 można teraz dodawać rozszerzenia maszyny wirtualnej.

Na przykład:

````PowerShell
Add-AzsVMExtension -Publisher "Microsoft" -Type "MicroExtension" -Version "0.1.0" -ComputeRole "IaaS" -SourceBlob "https://github.com/Microsoft/PowerShell-DSC-for-Linux/archive/v1.1.1-294.zip" -SupportMultipleExtensions -VmOsType "Linux"
````

## <a name="next-steps"></a>Kolejne kroki
[Tworzenie i publikowanie elementu portalu Marketplace](azure-stack-create-and-publish-marketplace-item.md)