---
title: Tworzenie obrazu maszyny wirtualnej lokalnej do portalu Azure Marketplace | Dokumentacja firmy Microsoft
description: Zrozumienie i wykonaj kroki, aby utworzyć lokalnej obrazu maszyny Wirtualnej i wdrażanie w portalu Azure Marketplace innym osobom do zakupu.
services: marketplace-publishing
documentationcenter: ''
author: msmbaldwin
manager: mbaldwin
editor: ''
ms.assetid: 26dfbd5a-8685-4b19-987e-c20ca60540ec
ms.service: marketplace
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: Azure
ms.workload: na
ms.date: 04/29/2016
ms.author: mbaldwin
ms.openlocfilehash: 6b927ce6032092ce258eeebca49da0571439dbfb
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/16/2018
ms.locfileid: "29944435"
---
# <a name="develop-an-on-premises-virtual-machine-image-for-the-azure-marketplace"></a>Tworzenie obrazu maszyny wirtualnej lokalnej dla portalu Azure Marketplace
Zdecydowanie zaleca się tworzenie Azure wirtualnych dysków twardych (VHD) bezpośrednio w chmurze przy użyciu protokołu Remote Desktop Protocol. Jeśli musisz, prawdopodobnie Pobierz wirtualnego dysku twardego i opracowanie go za pomocą infrastruktury lokalnej.  

Dla rozwoju lokalnych możesz pobrać systemu operacyjnego wirtualnego dysku twardego utworzonego maszyny wirtualnej. Te kroki nastąpiłoby w ramach kroku 3.3 powyżej.  

## <a name="download-a-vhd-image"></a>Pobranie obrazu wirtualnego dysku twardego
### <a name="locate-a-blob-url"></a>Znajdź adres URL obiektu blob
W celu pobrania dysku VHD, w pierwszej kolejności zlokalizować adresu URL obiektu blob dla dysku systemu operacyjnego.

Znajdź adres URL obiektu blob w ramach nowego [portalu Microsoft Azure](https://portal.azure.com):

1. Przejdź do **Przeglądaj** > **maszyn wirtualnych**, a następnie wybierz wdrożonej maszyny Wirtualnej.
2. W obszarze **Konfiguruj**, wybierz pozycję **dysków** kafelka, która otwiera blok dysków.
   
   ![Rysowanie](media/marketplace-publishing-vm-image-creation-on-premise/img01.png)
3. Wybierz **dysk systemu operacyjnego**, który zostanie otwarty kolejny blok zawierający właściwości dysku, w tym lokalizacja wirtualnego dysku twardego.
4. Skopiuj ten adres URL obiektu blob.
   
   ![Rysowanie](media/marketplace-publishing-vm-image-creation-on-premise/img02.png)
5. Teraz Usuń wdrożonej maszyny Wirtualnej bez usuwania dysków zapasowego. Można również zatrzymać maszynę Wirtualną zamiast usuwania. Nie pobieraj wirtualnego dysku twardego systemu operacyjnego, gdy maszyna wirtualna jest uruchomiona.
   
   ![Rysowanie](media/marketplace-publishing-vm-image-creation-on-premise/img03.png)

### <a name="download-a-vhd"></a>Pobieranie wirtualnego dysku twardego
Po określeniu adresu URL obiektu blob dysku VHD można pobrać za pomocą [portalu Azure](http://manage.windowsazure.com/) lub programu PowerShell.  

> [!NOTE]
> Podczas tworzenia tego przewodnika funkcji do pobrania dysku VHD nie ma jeszcze w nowego portalu Microsoft Azure.  
> 
> 

**Pobierz system operacyjny dysku VHD za pomocą bieżącego [portalu Azure](http://manage.windowsazure.com/)**

1. Zaloguj się do portalu Azure, jeśli nie zostało to jeszcze zrobione.
2. Kliknij przycisk **magazynu** kartę.
3. Wybierz konto magazynu, w którym znajduje się wirtualny dysk twardy.
   
   ![Rysowanie](media/marketplace-publishing-vm-image-creation-on-premise/img04.png)
4. Spowoduje to wyświetlenie właściwości konta magazynu. Wybierz **kontenery** kartę.
   
   ![Rysowanie](media/marketplace-publishing-vm-image-creation-on-premise/img05.png)
5. Wybierz kontener, w którym znajduje się wirtualny dysk twardy. Domyślnie jeśli utworzone w portalu wirtualny dysk twardy jest przechowywany w kontenerze wirtualne dyski twarde.
   
   ![Rysowanie](media/marketplace-publishing-vm-image-creation-on-premise/img06.png)
6. Wybierz poprawny system operacyjny dysku VHD, porównując adres URL do tego, który został zapisany.
7. Kliknij pozycję **Pobierz**.
   
   ![Rysowanie](media/marketplace-publishing-vm-image-creation-on-premise/img07.png)

### <a name="download-a-vhd-by-using-powershell"></a>Pobierz dysku VHD za pomocą programu PowerShell
Oprócz przy użyciu portalu Azure, możesz użyć [Save-AzureVhd](http://msdn.microsoft.com/library/dn495297.aspx) polecenia cmdlet, aby pobierać system operacyjny dysku VHD.

        Save-AzureVhd –Source <storageURIOfVhd> `
        -LocalFilePath <diskLocationOnWorkstation> `
        -StorageKey <keyForStorageAccount>
Na przykład Zapisz-AzureVhd-źródła "https://baseimagevm.blob.core.windows.net/vhds/BaseImageVM-6820cq00-BaseImageVM-os-1411003770191.vhd" - LocalFilePath "C:\Users\Administrator\Desktop\baseimagevm.vhd" - atrybutu StorageKey <String>

> [!NOTE]
> **Zapisz-AzureVhd** ma również **NumberOfThreads** opcja, która może służyć do zwiększenia równoległości, aby jak najlepiej wykorzystać dostępnej przepustowości dla pobierania.
> 
> 

## <a name="upload-vhds-to-an-azure-storage-account"></a>Przekaż wirtualne dyski twarde do konta magazynu platformy Azure
Przygotowane wirtualne dyski twarde lokalnej, należy przekazać je do konta magazynu na platformie Azure. Ten krok ma miejsce po tworzenie dysk VHD lokalnie, ale przed uzyskaniem certyfikacji dla obrazu maszyny Wirtualnej.

### <a name="create-a-storage-account-and-container"></a>Utwórz konto magazynu i kontener
Zaleca się, że wirtualne dyski twarde można przekazać do konta magazynu w regionie w Stanach Zjednoczonych. Wszystkie wirtualne dyski twarde dla jednej jednostki SKU powinna zostać umieszczona w jeden kontener w ramach konta pojedynczy magazyn.

Aby utworzyć konto magazynu, można użyć [portalu Microsoft Azure](https://portal.azure.com/), programu PowerShell lub systemu Linux narzędzia wiersza polecenia.  

**Utwórz konto magazynu z portalu Microsoft Azure**

1. Kliknij pozycję **Utwórz zasób**.
2. Wybierz **magazynu**.
3. Wprowadź nazwę konta magazynu, a następnie wybierz lokalizację.
   
   ![Rysowanie](media/marketplace-publishing-vm-image-creation-on-premise/img08.png)
4. Kliknij przycisk **Utwórz**.
5. Blok dla konta magazynu utworzone powinien być otwarty. Jeśli nie, wybierz **Przeglądaj** > **kont magazynu**. W bloku konta magazynu wybierz utworzone konto magazynu.
6. Wybierz **kontenery**.
   
   ![Rysowanie](media/marketplace-publishing-vm-image-creation-on-premise/img09.png) 
7. W bloku kontenery wybierz **Dodaj**, a następnie wprowadź nazwę kontenera i uprawnień kontenera. Wybierz **prywatnej** uprawnienia do kontenera.

> [!TIP]
> Firma Microsoft zaleca utworzenie jednego kontenera na jednostka SKU, które planujesz opublikować.
> 
> 

  ![Rysowanie](media/marketplace-publishing-vm-image-creation-on-premise/img10.png)

### <a name="create-a-storage-account-by-using-powershell"></a>Utwórz konto magazynu przy użyciu programu PowerShell
Przy użyciu programu PowerShell, Utwórz konto magazynu przy użyciu [AzureStorageAccount nowy](http://msdn.microsoft.com/library/dn495115.aspx) polecenia cmdlet.

        New-AzureStorageAccount -StorageAccountName “mystorageaccount” -Location “West US”

Następnie można utworzyć kontener w ramach tego konta magazynu przy użyciu [NewAzureStorageContainer](http://msdn.microsoft.com/library/dn495291.aspx) polecenia cmdlet.

        New-AzureStorageContainer -Name “containername” -Permission “Off”

> [!NOTE]
> Tych poleceniach założono, że bieżący kontekst konta magazynu został już ustawiony w programie PowerShell.   Zapoznaj się [Konfigurowanie programu Azure PowerShell](marketplace-publishing-powershell-setup.md) uzyskać więcej informacji dotyczących instalacji programu PowerShell.  
> 
> ### <a name="create-a-storage-account-by-using-the-command-line-tool-for-mac-and-linux"></a>Utwórz konto magazynu przy użyciu narzędzia wiersza polecenia dla komputerów Mac i Linux
> Z [narzędzia wiersza polecenia systemu Linux](../virtual-machines/linux/cli-manage.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json), Utwórz konto magazynu w następujący sposób.
> 
> 

        azure storage account create mystorageaccount --location "West US"

Utwórz kontener w następujący sposób.

        azure storage container create containername --account-name mystorageaccount --accountkey <accountKey>

## <a name="upload-a-vhd"></a>Przekazywanie wirtualnego dysku twardego
Po utworzeniu konta magazynu i kontener, możesz przekazać go przygotować dyski VHD. Można użyć programu PowerShell, narzędzie wiersza polecenia systemu Linux lub innych narzędzi do zarządzania usługi Azure Storage.

### <a name="upload-a-vhd-via-powershell"></a>Przekazywanie wirtualnego dysku twardego za pomocą programu PowerShell
Użyj [Add-AzureVhd](http://msdn.microsoft.com/library/dn495173.aspx) polecenia cmdlet.

        Add-AzureVhd –Destination “http://mystorageaccount.blob.core.windows.net/containername/vmsku.vhd” -LocalFilePath “C:\Users\Administrator\Desktop\vmsku.vhd”

### <a name="upload-a-vhd-by-using-the-command-line-tool-for-mac-and-linux"></a>Przekazywanie wirtualnego dysku twardego za pomocą narzędzia wiersza polecenia dla komputerów Mac i Linux
Z [narzędzia wiersza polecenia systemu Linux](https://docs.microsoft.com/cli/azure/get-started-with-az-cli2), należy użyć następującego: Tworzenie obrazu maszyny wirtualnej azure <image name> — lokalizacji <Location of the data center> — system operacyjny Linux <LocationOfLocalVHD>

## <a name="see-also"></a>Zobacz także
* [Tworzenie obrazu maszyny wirtualnej dla witryny Marketplace](marketplace-publishing-vm-image-creation.md)
* [Konfigurowanie programu Azure PowerShell](marketplace-publishing-powershell-setup.md)

