---
title: Pobieranie elementów portalu marketplace na platformie Azure | Dokumentacja firmy Microsoft
description: Operator chmury można pobrać elementów portalu marketplace z platformy Azure do mojego wdrożenia usługi Azure Stack.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 02/14/2019
ms.author: sethm
ms.reviewer: unknown
ms.lastreviewed: 12/10/2018
ms.openlocfilehash: 9bf261918bdbdf3ee06ad28a037d5bb8a3631a20
ms.sourcegitcommit: 0dd053b447e171bc99f3bad89a75ca12cd748e9c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2019
ms.locfileid: "58487638"
---
# <a name="download-marketplace-items-from-azure-to-azure-stack"></a>Pobieranie elementów portalu marketplace z platformy Azure do usługi Azure Stack

*Dotyczy: Zintegrowane usługi Azure Stack, systemy i usługi Azure Stack Development Kit*

Operator chmury można pobrać elementów w portalu Azure Marketplace i udostępnić je w usłudze Azure Stack. Elementy, które można wybrać pochodzą z nadzorowanej liście elementów portalu Azure Marketplace, które są wstępnie przetestowane i jest obsługiwane do pracy z usługą Azure Stack. Dodatkowe elementy często są dodawane do tej listy, więc w dalszym ciągu sprawdzaj nowej zawartości. 

Istnieją dwa scenariusze dotyczące nawiązywania połączenia w portalu Azure Marketplace: 

- **Scenariusz połączonych** -wymagającym środowiskiem usługi Azure Stack połączenia z Internetem. Portal usługi Azure Stack umożliwia zlokalizuj i pobieranie elementów. 
- **Scenariusz odłączone lub częściowo połączone** — która wymaga dostępu do Internetu za pomocą narzędzia syndykacji portalu marketplace na pobieranie elementów portalu marketplace. Następnie transferu plików do pobrania odłączonej instalacji usługi Azure Stack. W tym scenariuszu programu PowerShell.

Zobacz [elementów portalu Azure Marketplace dla usługi Azure Stack](azure-stack-marketplace-azure-items.md) listę elementów portalu marketplace, możesz pobrać.


## <a name="connected-scenario"></a>Połączone scenariusza
Jeśli usługi Azure Stack nawiązuje połączenie z Internetem, możesz korzystać z portalu administratora do pobieranie elementów portalu marketplace.

### <a name="prerequisites"></a>Wymagania wstępne
Wdrożenia usługi Azure Stack musi mieć połączenie z Internetem i musi być [zarejestrowane w usłudze Azure](azure-stack-register.md).

### <a name="use-the-portal-to-download-marketplace-items"></a>Pobieranie elementów portalu marketplace za pomocą portalu  
1. Zaloguj się do portalu administratora usługi Azure Stack.

2.  Przejrzyj dostępne miejsce przed pobraniem elementów portalu marketplace. Później po wybraniu elementów do pobrania, możesz porównać rozmiar pobieranych elementów z dostępnej pojemności. Jeśli wydajność jest ograniczona, należy rozważyć opcje [Zarządzanie dostępnego miejsca](azure-stack-manage-storage-shares.md#manage-available-space). 

    Aby sprawdzić dostępne miejsce w **zarządzanie regionami** wybierz region, aby eksplorować, a następnie przejdź do **dostawców zasobów** > **magazynu**.

    [![Przejrzyj miejsce do magazynowania](media/azure-stack-download-azure-marketplace-item/storagesm.png "Przejrzyj miejsce do magazynowania")](media/azure-stack-download-azure-marketplace-item/storage.png#lightbox)

    
3. Otwórz w portalu Azure Marketplace stosu i łączenie z platformą Azure. Aby to zrobić, wybierz **Marketplace zarządzania**, a następnie wybierz pozycję **Dodaj na platformie Azure**.

    [![Dodaj na platformie Azure](media/azure-stack-download-azure-marketplace-item/marketplacesm.png "dodać z platformy Azure")](media/azure-stack-download-azure-marketplace-item/marketplace.png#lightbox)

    Portal Wyświetla listę elementów, które można pobrać z witryny Azure Marketplace. Kliknięcie każdego elementu, aby wyświetlić jego opis i dodatkowe informacje o tym, w tym jej rozmiaru pobierania. 

    [![Lista Marketplace](media/azure-stack-download-azure-marketplace-item/image03sm.png "listy witryny Marketplace")](media/azure-stack-download-azure-marketplace-item/image03.png#lightbox)

4. Wybierz element, a następnie wybierz **Pobierz**. Czasu pobierania różnią się.

    [![Pobierz wiadomość](media/azure-stack-download-azure-marketplace-item/image04.png "pobierania komunikatów")](media/azure-stack-download-azure-marketplace-item/image04.png#lightbox)

    Po ukończeniu pobierania, można wdrożyć nowy element marketplace jako operatora infrastruktury Azure Stack lub użytkownika.

5. Aby wdrożyć pobrany element, wybierz **+ Utwórz zasób**, a następnie Wyszukaj wśród kategorii dla nowego elementu portalu marketplace. Następnie wybierz element, aby rozpocząć proces wdrażania. Proces różni się dla elementów innego portalu marketplace. 

## <a name="disconnected-or-a-partially-connected-scenario"></a>Odłączony lub częściowo połączone scenariusza

W przypadku usługi Azure Stack w trybie rozłączonym i bez łączności z Internetem, użyj programu PowerShell i *narzędzie syndykacji marketplace* na pobieranie elementów portalu marketplace do maszyny z łącznością z Internetem. Następnie przenieść elementy środowiskiem usługi Azure Stack. W środowisku bez połączenia nie można pobrać elementów portalu marketplace, korzystając z portalu usługi Azure Stack. 

Narzędzie syndykacji marketplace można również w przypadku połączonych. 

Istnieją dwie części do tego scenariusza:
- **Część 1:** Pobierz z witryny Azure Marketplace. Na komputerze z dostępem do Internetu Konfigurowanie programu PowerShell, Pobierz narzędzie syndykacji i następnie pobrać formularz elementów w portalu Azure Marketplace.  
- **Część 2:** Przekazywania i publikowania w portalu Azure Marketplace stosu. Przenieś pliki pobrane do środowiska usługi Azure Stack, zaimportuj je do usługi Azure Stack, a następnie publikować je w portalu Azure Marketplace stosu.  


### <a name="prerequisites"></a>Wymagania wstępne
- Wdrożenia usługi Azure Stack musi być [zarejestrowane w usłudze Azure](azure-stack-register.md).  

- Na komputerze, który ma łączność z Internetem muszą być **moduł PowerShell programu Azure Stack wersji 1.2.11** lub nowszej. Jeśli jeszcze nie istnieje, [Zainstaluj określone moduły programu PowerShell usługi Azure Stack](azure-stack-powershell-install.md).  

- Aby włączyć importowanie elementu portalu marketplace pobranych [Środowisko PowerShell dla operatora infrastruktury Azure Stack](azure-stack-powershell-configure-admin.md) musi być skonfigurowany.  

- Konieczne jest posiadanie [konta magazynu](azure-stack-manage-storage-accounts.md) w usłudze Azure Stack, który jest publicznie dostępny kontener (czyli magazynu obiektów blob). Korzystasz z kontenera jako tymczasowego magazynu plików galerii elementów portalu marketplace. Jeśli nie jesteś zaznajomiony z kontami magazynu i kontenerów, zobacz [Praca z obiektami blob — witryna Azure portal](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal) w dokumentacji platformy Azure.

- Narzędzie syndykacji portalu marketplace jest pobierany podczas pierwszej procedurze. 

- Możesz zainstalować [AzCopy](../storage/common/storage-use-azcopy.md) do pobrania optymalną wydajność, ale nie jest wymagane.

### <a name="use-the-marketplace-syndication-tool-to-download-marketplace-items"></a>Pobieranie elementów portalu marketplace za pomocą narzędzia syndykacji witryny marketplace

1. Na komputerze z połączeniem internetowym Otwórz konsolę programu PowerShell jako administrator.

2. Dodaj konto platformy Azure, których użyto do zarejestrowania w usłudze Azure Stack. Aby dodać konto, w programie PowerShell Uruchom `Add-AzureRmAccount` bez żadnych parametrów. Zostanie wyświetlony monit o wprowadzenie poświadczeń konta platformy Azure i może być konieczne użycie uwierzytelnianie 2-etapowe, na podstawie konfiguracji konta.

3. Jeśli masz wiele subskrypcji, uruchom następujące polecenie, aby wybrać, które mają być używane do rejestracji:  

   ```powershell  
   Get-AzureRmSubscription -SubscriptionID '<Your Azure Subscription GUID>' | Select-AzureRmSubscription
   $AzureContext = Get-AzureRmContext
   ```

4. Pobierz najnowszą wersję narzędzia syndykacji marketplace za pomocą następującego skryptu:  

   ```powershell
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

5. Zaimportuj moduł syndykacji, a następnie uruchom narzędzie, uruchamiając następujące polecenia. Zastąp `Destination folder path` lokalizację do przechowywania plików, możesz pobrać z witryny Azure Marketplace.   

   ```powershell  
   Import-Module .\Syndication\AzureStack.MarketplaceSyndication.psm1

   Export-AzSOfflineMarketplaceItem -Destination "Destination folder path in quotes" 
   ```

6. Po uruchomieniu narzędzia powinien zostać wyświetlony ekran podobny do poniższej ilustracji, z listą elementów portalu marketplace dostępne:

   [![Azure Marketplace elementów popup](media/azure-stack-download-azure-marketplace-item/image05.png "elementów portalu Azure Marketplace")](media/azure-stack-download-azure-marketplace-item/image05.png#lightbox)

7. Wybierz element, który chcesz pobrać i zanotuj *wersji*. Naciśnij i przytrzymaj *Ctrl* klawisz, aby wybrać wiele obrazów. Możesz odwoływać się *wersji* podczas importowania elementu w następnej procedurze. 
   
   Można również filtrować listę obrazów, za pomocą **Dodaj kryteria** opcji.

8. Wybierz **OK**, a następnie zaakceptuj postanowienia prawne. 

9. Czas pobierania zależy od rozmiaru elementu. Po zakończeniu pobierania element jest dostępny w folderze, który określiłeś w skrypcie. Pliki do pobrania zawiera plik wirtualnego dysku twardego (w przypadku maszyn wirtualnych) lub plik zip (dla rozszerzeń maszyny wirtualnej). Może to również obejmować pakietu galerii w *.azpkg* formatu, który jest po prostu plikiem zip.

10. Jeśli pobieranie nie powiedzie się, możesz spróbować ponownie, ponownie uruchamiając następujące polecenie cmdlet programu PowerShell:

    ```powershell
    Export-AzSOfflineMarketplaceItem -Destination "Destination folder path in quotes”
    ```

    Przed ponowieniem próby wykonania, usuń folder produktu, w którym pobieranie nie powiodło się. Na przykład, jeśli skrypt pobierania kończy się niepowodzeniem, podczas pobierania na `D:\downloadFolder\microsoft.customscriptextension-arm-1.9.1`, Usuń `D:\downloadFolder\microsoft.customscriptextension-arm-1.9.1` folder, a następnie ponownie uruchom polecenie cmdlet.
 
### <a name="import-the-download-and-publish-to-azure-stack-marketplace-1811-and-higher"></a>Importowanie, pobieranie i publikowanie w portalu Marketplace dla usługi Azure Stack (1811 i nowsze)

1. Musisz przenieść pliki, do których masz [wcześniej pobrano](#use-the-marketplace-syndication-tool-to-download-marketplace-items) lokalnie, aby były dostępne dla środowiska Azure Stack. Narzędzie syndykacji marketplace również musi być dostępne do środowiska usługi Azure Stack, ponieważ należy użyć narzędzia do wykonania tej operacji importowania.

   Na poniższej ilustracji przedstawiono przykład struktury folderów. `D:\downloadfolder` zawiera wszystkie elementy portalu marketplace pobranych. Każdy podfolder jest elementu portalu marketplace (na przykład `microsoft.custom-script-linux-arm-2.0.3`) o nazwie według identyfikatora produktu. Wewnątrz każdego podfolder jest pobranej zawartości elementu portalu marketplace.

   [![Struktura katalogów pobierania witryny Marketplace](media/azure-stack-download-azure-marketplace-item/mp1sm.png "strukturę katalogów pobierania witryny Marketplace")](media/azure-stack-download-azure-marketplace-item/mp1.png#lightbox)

2. Postępuj zgodnie z instrukcjami w [w tym artykule](azure-stack-powershell-configure-admin.md) do skonfigurowania sesji usługi Azure Stack — Operator w programie PowerShell. 

3. Zaimportuj moduł syndykacji, a następnie uruchom narzędzie syndykacji portalu marketplace, uruchamiając następujący skrypt:

   ```powershell
   $credential = Get-Credential -Message "Enter the azure stack operator credential:"
   Import-AzSOfflineMarketplaceItem -origin "marketplace content folder" -AzsCredential $credential
   ```

   `-origin` Parametr określa folder najwyższego poziomu, który zawiera wszystkie produkty pobrane; na przykład `"D:\downloadfolder"`.

   `-AzsCredential` Parametr jest opcjonalny. Służy do odnowienia tokenu dostępu, jeśli wygasł. Jeśli `-AzsCredential` nie określono parametru i token jest ważny, zostanie wyświetlony monit o podanie poświadczeń operatora.

    > [!Note]  
    > Usługi AD FS obsługuje tylko uwierzytelnianie interakcyjne przy użyciu tożsamości użytkowników. Jeśli wymagany jest obiekt poświadczeń należy użyć nazwy głównej usługi (SPN). Aby uzyskać więcej informacji na temat konfigurowania jednostki usługi przy użyciu usługi Azure Stack i usług AD FS, co usługa zarządzania tożsamościami, zobacz [nazwy głównej usługi zarządzania dla usług AD FS](azure-stack-create-service-principals.md#manage-service-principal-for-ad-fs).

4. Po pomyślnym ukończeniu działania skryptu pole powinno być dostępne w witrynie Azure Marketplace stosu.

### <a name="import-the-download-and-publish-to-azure-stack-marketplace-1809-and-lower"></a>Importowanie, pobieranie i publikowanie w portalu Marketplace dla usługi Azure Stack (1809 i niższych)

1. Pliki obrazów maszyn wirtualnych lub szablonów rozwiązań, które masz [wcześniej pobrano](#use-the-marketplace-syndication-tool-to-download-marketplace-items) muszą być dostępne lokalnie do środowiska usługi Azure Stack.  

2. Przekazywanie pakietu elementu portalu marketplace (plik .azpkg) oraz obraz wirtualnego dysku twardego (plik VHD) do magazynu obiektów Blob platformy Azure Stack, należy korzystać z portalu administratora. Przekazywanie pakietu i pliki dysku udostępnia je do usługi Azure Stack, aby później można opublikować elementu portalu Marketplace usługi Azure Stack.

   Przekazywanie wymaga posiadania konta magazynu przy użyciu publicznie kontenera (patrz wymagania wstępne dotyczące tego scenariusza).  
   1. W portalu administracyjnym usługi Azure Stack, przejdź do **wszystkich usług** a następnie w obszarze **dane + magazyn** kategorii, wybierz opcję **kont magazynu**.  
   
   2. Wybierz konto magazynu z subskrypcji, a następnie w obszarze **usługę BLOB SERVICE**, wybierz opcję **kontenery**.  
      [![Usługi blob service](media/azure-stack-download-azure-marketplace-item/blob-service.png "usługi Blob service")](media/azure-stack-download-azure-marketplace-item/blob-service.png#lightbox)  
   
   3. Wybierz kontener, o których chcesz użyć, a następnie wybierz pozycję **przekazywanie** otworzyć **przekazywanie obiektu blob** okienka.  
      [![Kontener](media/azure-stack-download-azure-marketplace-item/container.png "kontenera")](media/azure-stack-download-azure-marketplace-item/container.png#lightbox)  
   
   4. W okienku przekazywania obiektów blob, przejdź do plików pakietu lub dysku, aby załadować do pamięci masowej, a następnie wybierz pozycję **przekazywanie**: [![Przekaż](media/azure-stack-download-azure-marketplace-item/uploadsm.png "przekazywania")](media/azure-stack-download-azure-marketplace-item/upload.png#lightbox)  

   5. Pliki, które są przekazywane są wyświetlane w okienku kontenera. Wybierz plik, a następnie skopiuj adres URL z **właściwości obiektu Blob** okienka. Użyjesz tego adresu URL w następnym kroku podczas importowania elementu portalu marketplace do usługi Azure Stack.  Na poniższym obrazie kontenera jest *magazynu obiektów blob-test* i plik jest *Microsoft.WindowsServer2016DatacenterServerCore ARM.1.0.801.azpkg*.  Plik, adres URL jest *https://testblobstorage1.blob.local.azurestack.external/blob-test-storage/Microsoft.WindowsServer2016DatacenterServerCore-ARM.1.0.801.azpkg*.  
      [![Właściwości obiektu blob](media/azure-stack-download-azure-marketplace-item/blob-storagesm.png "właściwości obiektu Blob")](media/azure-stack-download-azure-marketplace-item/blob-storage.png#lightbox)  

3. Importuj obraz wirtualnego dysku twardego do usługi Azure Stack przy użyciu **AzsPlatformimage Dodaj** polecenia cmdlet. Korzystając z tego polecenia cmdlet, Zastąp *wydawcy*, *oferują*i inne wartości parametru z wartościami obraz, który importujesz. 

   Możesz uzyskać *wydawcy*, *oferują*, i *jednostki sku* wartości obrazu z pliku tekstowego zawierającego pliki do pobrania przy użyciu pliku AZPKG. Plik tekstowy znajduje się w lokalizacji docelowej. *Wersji* wartość wersji, podczas pobierania elementu z platformy Azure w ramach poprzedniej procedury. 
 
   W poniższej przykładowy skrypt są używane wartości dla systemu Windows Server 2016 Datacenter — instalacja Server Core maszyny wirtualnej. Wartość *- Osuri* jest przykładowa ścieżka do lokalizacji magazynu obiektów blob dla elementu.

   Zamiast tego skryptu, można użyć [procedury opisanej w tym artykule](azure-stack-add-vm-image.md#add-a-vm-image-through-the-portal) do zaimportowania. Obraz wirtualnego dysku twardego za pomocą witryny Azure portal.

   ```powershell  
   Add-AzsPlatformimage `
    -publisher "MicrosoftWindowsServer" `
    -offer "WindowsServer" `
    -sku "2016-Datacenter-Server-Core" `
    -osType Windows `
    -Version "2016.127.20171215" `
    -OsUri "https://mystorageaccount.blob.local.azurestack.external/cont1/Microsoft.WindowsServer2016DatacenterServerCore-ARM.1.0.801.vhd"  
   ```

   **Informacje o szablonach rozwiązania:** Niektóre szablony mogą zawierać małych 3 MB. Plik wirtualnego dysku twardego o nazwie **fixed3.vhd**. Nie trzeba zaimportować ten plik do usługi Azure Stack. Fixed3.vhd.  Ten plik jest dołączone do niektórych szablony rozwiązań, aby spełniać wymagania publikacji w portalu Azure Marketplace.

   Przejrzyj opis szablonów i Pobierz, a następnie zaimportować dodatkowe wymagania, takie jak wirtualne dyski twarde, które są wymagane do pracy za pomocą szablonu rozwiązania.  
   
   **O rozszerzeniach:** Podczas pracy z rozszerzeniami obrazu maszyny wirtualnej, należy użyć następujących parametrów:
   - *Wydawca*
   - *Typ*
   - *Wersja*  

   Nie używaj *oferują* rozszerzeń.   


4.  Publikowanie elementu portalu marketplace do usługi Azure Stack przy użyciu za pomocą programu PowerShell **AzsGalleryItem Dodaj** polecenia cmdlet. Na przykład:  
    ```powershell  
    Add-AzsGalleryItem `
     -GalleryItemUri "https://mystorageaccount.blob.local.azurestack.external/cont1/Microsoft.WindowsServer2016DatacenterServerCore-ARM.1.0.801.azpkg" `
     –Verbose
    ```
5. Po opublikowaniu elementu galerii jest teraz dostępny do użycia. Aby upewnić się, zostanie opublikowany element galerii, przejdź do **wszystkich usług**, a następnie w obszarze **ogólne** kategorii, wybierz opcję **Marketplace**.  Jeżeli pobieranie jest szablon rozwiązania, upewnij się, że możesz dodać wszelkie zależne obraz wirtualnego dysku twardego dla tego szablonu rozwiązania.  
  [![Wyświetl w portalu marketplace](media/azure-stack-download-azure-marketplace-item/view-marketplacesm.png "widok w witrynie marketplace")](media/azure-stack-download-azure-marketplace-item/view-marketplace.png#lightbox)  

Wersja programu Azure Stack PowerShell 1.3.0 może teraz dodawać rozszerzenia maszyny wirtualnej. Na przykład:

```powershell
Add-AzsVMExtension -Publisher "Microsoft" -Type "MicroExtension" -Version "0.1.0" -ComputeRole "IaaS" -SourceBlob "https://github.com/Microsoft/PowerShell-DSC-for-Linux/archive/v1.1.1-294.zip" -SupportMultipleExtensions -VmOsType "Linux"
```

## <a name="next-steps"></a>Kolejne kroki

[Tworzenie i publikowanie elementu portalu Marketplace](azure-stack-create-and-publish-marketplace-item.md)