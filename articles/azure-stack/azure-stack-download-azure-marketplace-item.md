---
title: Pobieranie elementów marketplace z platformy Azure | Dokumentacja firmy Microsoft
description: Można pobrać elementów marketplace z platformy Azure do mojego wdrożenia stosu Azure.
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
ms.date: 05/08/2018
ms.author: brenduns
ms.reviewer: jeffgo
ms.openlocfilehash: 2e92dc96a69400f689e49b70d1b855c955084362
ms.sourcegitcommit: fc64acba9d9b9784e3662327414e5fe7bd3e972e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/12/2018
---
# <a name="download-marketplace-items-from-azure-to-azure-stack"></a>Pobieranie elementów marketplace z platformy Azure do stosu Azure

*Dotyczy: Azure stosu zintegrowanych systemów i Azure stosu Development Kit*


Jak zdecydujesz zawartość do uwzględnienia w Twojej stosu Azure marketplace, należy wziąć pod uwagę zawartości z portalu Azure marketplace. Możesz pobrać wyselekcjonowanych listy elementów witrynę Azure marketplace, które zostały przetestowane na stosie Azure. Nowe elementy często są dodawane do tej listy, dlatego upewnij się, że sprawdzanie dostępności nową zawartość.

## <a name="download-marketplace-items-in-a-connected-scenario-with-internet-connectivity"></a>Pobieranie elementów marketplace w przypadku połączonych (z połączeniem internetowym)

1. Aby pobrać elementów portalu marketplace, należy najpierw [zarejestrować stosu Azure w usłudze Azure](azure-stack-register.md).
2. Zaloguj się do portalu administratora stosu Azure (dla ASDK, użyj https://portal.local.azurestack.external).
3. Niektóre elementy marketplace mogą być duże. Upewnij się, że masz wystarczającą ilość miejsca w systemie, klikając **dostawców zasobów** > **magazynu**.

    ![](media/azure-stack-download-azure-marketplace-item/image01.png)

4. Kliknij przycisk **więcej usług** > **zarządzania Marketplace**.

    ![](media/azure-stack-download-azure-marketplace-item/image02.png)

4. Kliknij przycisk **Dodaj z platformy Azure** umożliwia wyświetlenie listy dostępnych do pobrania elementów. Możesz kliknąć na każdy element na liście, aby wyświetlić jej opis i rozmiar pliku.

    ![](media/azure-stack-download-azure-marketplace-item/image03.png)

5. Wybierz element na liście, a następnie kliknij przycisk **Pobierz**. Obraz maszyny Wirtualnej dla wybranego elementu rozpocznie pobieranie. Czasu pobierania różnią się.

    ![](media/azure-stack-download-azure-marketplace-item/image04.png)

6. Po zakończeniu pobierania, można wdrożyć z nowy element marketplace jako operator stosu Azure lub użytkownika. Kliknij przycisk **+ nowy**, przeszukiwanie kategorie nowy element marketplace, a następnie wybierz element.
7. Kliknij przycisk **Utwórz** otwarcie środowisko tworzenia dla nowo pobrany element. Postępuj zgodnie z instrukcjami krok po kroku w celu wdrożenia tego elementu.

## <a name="download-marketplace-items-in-a-disconnected-or-a-partially-connected-scenario-with-limited-internet-connectivity"></a>Pobieranie elementów marketplace w odłączone lub częściowo połączonego scenariusza (ograniczone z łącznością z Internetem)

Podczas wdrażania usługi Azure stosu w trybie rozłączonym (bez żadnych łączności z Internetem), nie można pobrać elementów marketplace przy użyciu portalu Azure stosu. Jednak można pobrać elementów marketplace na komputerze, na którym ma połączenie z Internetem za pomocą narzędzia zespolonego marketplace i przesyła je do środowiska Azure stosu.

### <a name="prerequisites"></a>Wymagania wstępne
Przed użyciem narzędzia zespolonego marketplace, upewnij się, że masz [zarejestrowany stosu Azure w ramach subskrypcji Azure](azure-stack-register.md).  

Na komputerze, który ma łączność z Internetem wykonaj następujące kroki, aby pobrać elementów marketplace wymagane:

1. Otwórz konsolę programu PowerShell jako administrator i [zainstalować określone moduły programu PowerShell Azure stosu](azure-stack-powershell-install.md). Upewnij się, że instalujesz program **modułu programu PowerShell Azure stosu wersji 1.2.11 lub nowszej**.  

2. Dodaj konto platformy Azure, używany do rejestrowania stosu Azure. Aby dodać konta, uruchom **Add-AzureRmAccount** polecenia cmdlet bez parametrów. Zostanie wyświetlony monit o wprowadzenie poświadczeń konta platformy Azure i może być konieczne użycie uwierzytelniania wieloskładnikowego 2 na podstawie konfiguracji Twoje konto.  

3. Jeśli masz wiele subskrypcji, uruchom następujące polecenie, aby wybrać używanego do rejestracji:  

   ```powershell
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
   cd \AzureStack-Tools-master

   ```

5. Zaimportuj moduł zespolonego i uruchom narzędzie, uruchamiając następujące polecenia:  

   ```powershell
   Import-Module .\Syndication\AzureStack.MarketplaceSyndication.psm1

   Sync-AzSOfflineMarketplaceItem `
     -destination "<Destination folder path>" `
     -AzureTenantID $AzureContext.Tenant.TenantId `
     -AzureSubscriptionId $AzureContext.Subscription.Id  
   ```

6. Po uruchomieniu zostanie wyświetlony monit o podanie poświadczeń konta platformy Azure. Zaloguj się do konta platformy Azure, używany do rejestrowania stosu Azure. Po pomyślnym zainicjowaniu nazwy logowania, powinien zostać wyświetlony następujący ekran z listy elementów marketplace dostępne.  

   ![Azure podręcznego elementów Marketplace](./media/azure-stack-download-azure-marketplace-item/image05.png)

7. Wybierz obraz, który chcesz pobrać i zanotuj wersję obrazu. Możesz wybrać wiele obrazów, przytrzymując klawisz Ctrl. Wersja obrazu służy do importowania obrazu w następnej sekcji.  Następnie kliknij przycisk **Ok**, a następnie zaakceptuj postanowienia prawne, klikając **tak**. Można również filtrować listy obrazów za pomocą **Dodaj kryteria** opcji. 

   Pobieranie zajmie trochę czasu w zależności od rozmiaru obrazu. Raz do pobrania obrazu jest dostępny w ścieżce docelowej podane wcześniej. Pobieranie zawiera plik wirtualnego dysku twardego (w przypadku maszyn wirtualnych) lub. Plik ZIP (dla rozszerzeń maszyny wirtualnej) i elementu galerii w formacie Azpkg.

### <a name="import-the-image-and-publish-it-to-azure-stack-marketplace"></a>Importowanie obrazu i opublikować ją w stosie Azure marketplace
Istnieją trzy różne typy elementów w witrynie marketplace: maszyny wirtualne, rozszerzenia maszyn wirtualnych i szablony rozwiązań. Szablony rozwiązań omówiono poniżej.
> [!NOTE]
> Rozszerzenia maszyn wirtualnych nie można dodać do stosu Azure w tej chwili.

1. Po pobraniu pakietu obrazu i galerii, zapisz je i zawartość w folderze AzureStack Narzędzia główne, aby dysk wymienny i skopiować go do środowiska Azure stosu (można go skopiować lokalnie do dowolnej lokalizacji takich jak: "C:\MarketplaceImages").     

2. Przed zaimportowaniem obrazu, musisz połączyć środowiska operator stosu Azure przy użyciu procedury opisanej w [konfigurowania środowiska PowerShell Azure stosu operator](azure-stack-powershell-configure-admin.md).  

3. Jeśli pobieranie się o nazwie fixed3.vhd mały plik wirtualnego dysku twardego 3MB, jest to szablon rozwiązania. Ten plik nie jest wymagane; Przejdź do kroku 5. Upewnij się, że możesz pobrać wszystkie elementy zależne, jak wskazano w opisie do pobrania.

4. Zaimportuj obraz do stosu Azure za pomocą polecenia cmdlet Add-AzsVMImage. Korzystając z tego polecenia cmdlet, upewnij się zastąpić *wydawcy*, *oferują*i inne wartości parametru z wartościami właściwości obrazu, który jest importowany. Możesz uzyskać *wydawcy*, *oferują*, i *sku* wartości obrazu z elementu imageReference obiektu pobranego wcześniej pliku Azpkg i  *Wersja* wartość z kroku 6 w poprzedniej sekcji.

Można znaleźć elementu imageReference, musisz zmienić nazwę pliku AZPKG z. Rozszerzenie ZIP, wyodrębnij go do tymczasowej lokalizacji, a następnie otwórz plik DeploymentTemplates\CreateUiDefinition.json za pomocą edytora tekstu. Znajdź w tej sekcji:

   ```json
   "imageReference": {
      "publisher": "MicrosoftWindowsServer",
      "offer": "WindowsServer",
      "sku": "2016-Datacenter-Server-Core"
    }
   ```

   Zastąp wartości parametru, a następnie uruchom następujące polecenie:

   ```powershell
   Import-Module .\ComputeAdmin\AzureStack.ComputeAdmin.psm1

   Add-AzsVMImage `
    -publisher "MicrosoftWindowsServer" `
    -offer "WindowsServer" `
    -sku "2016-Datacenter-Server-Core" `
    -osType Windows `
    -Version "2016.127.20171215" `
    -OsDiskLocalPath "C:\AzureStack-Tools-master\Syndication\Windows-Server-2016-DatacenterCore-20171215-en.us-127GB.vhd" `
    -CreateGalleryItem $False `
    -Location Local
   ```

5. Użyj portalu można przekazać elementu z witryny Marketplace (. Azpkg) do magazynu obiektów Blob platformy Azure stosu. Można przekazać do lokalnego magazynu Azure stosu lub Przekaż do magazynu Azure. (Jest tymczasowej lokalizacji pakietu). Upewnij się, że obiekt blob jest publicznie i Zanotuj identyfikator URI.  

6. Publikowanie elementu portalu marketplace stos Azure przy użyciu **AzsGalleryItem Dodaj**. Na przykład:

   ```powershell
   Add-AzsGalleryItem `
     -GalleryItemUri "https://mystorageaccount.blob.local.azurestack.external/cont1/Microsoft.WindowsServer2016DatacenterServerCore-ARM.1.0.2.azpkg" `
     –Verbose
   ```

7. Po opublikowaniu elementu galerii, możesz je wyświetlić z **nowy** > **Marketplace** okienka. Jeśli pobieranie się szablon rozwiązania, upewnij się, że pobrano zależnych obrazu wirtualnego dysku twardego.

   ![Portal Marketplace](./media/azure-stack-download-azure-marketplace-item/image06.png)

## <a name="next-steps"></a>Kolejne kroki

[Tworzenie i publikowanie elementu portalu Marketplace](azure-stack-create-and-publish-marketplace-item.md)
