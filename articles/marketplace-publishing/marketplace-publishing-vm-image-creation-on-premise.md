---
title: Tworzenie obrazu maszyny wirtualnej w środowisku lokalnym, w portalu Azure Marketplace | Dokumentacja firmy Microsoft
description: Informacje i wykonaj kroki, aby utworzyć obraz maszyny Wirtualnej w środowisku lokalnym i wdrażania w portalu Azure Marketplace przez inne osoby do zakupu.
services: marketplace-publishing
documentationcenter: ''
author: HannibalSII
manager: hascipio
editor: ''
ms.assetid: 26dfbd5a-8685-4b19-987e-c20ca60540ec
ms.service: marketplace
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: Azure
ms.workload: na
ms.date: 04/29/2016
ms.author: hascipio; v-divte
ms.openlocfilehash: c1e6e67ba7a1321ce58fbd58c173fa63dfa385e0
ms.sourcegitcommit: d16b7d22dddef6da8b6cfdf412b1a668ab436c1f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/08/2018
ms.locfileid: "39715815"
---
# <a name="develop-an-on-premises-virtual-machine-image-for-the-azure-marketplace"></a>Tworzenie obrazu maszyny wirtualnej w środowisku lokalnym dla portalu Azure Marketplace
Zdecydowanie zaleca się tworzenie Azure wirtualne dyski twarde (VHD) bezpośrednio w chmurze przy użyciu protokołu Remote Desktop Protocol. Jednak jeśli trzeba, istnieje możliwość pobierania wirtualnego dysku twardego i Opracuj go za pomocą infrastruktury lokalnej.  

Do tworzenia aplikacji w środowisku lokalnym możesz pobrać system operacyjny dysku VHD utworzonej maszyny Wirtualnej. Te kroki będzie odbywać się jako część krok 3.3 powyżej.  

## <a name="download-a-vhd-image"></a>Pobierz obraz wirtualnego dysku twardego
### <a name="locate-a-blob-url"></a>Znajdź adres URL obiektu blob
Aby można było pobrać wirtualny dysk twardy, w pierwszej kolejności zlokalizować adresu URL obiektu blob dysku systemu operacyjnego.

Znajdź adres URL obiektu blob w ramach nowego [portalu Microsoft Azure](https://portal.azure.com):

1. Przejdź do **Przeglądaj** > **maszyn wirtualnych**, a następnie wybierz pozycję wdrożonej maszyny Wirtualnej.
2. W obszarze **Konfiguruj**, wybierz opcję **dysków** Kafelek, który spowoduje otwarcie bloku dysków.
   
   ![Rysowanie](media/marketplace-publishing-vm-image-creation-on-premise/img01.png)
3. Wybierz **dysku systemu operacyjnego**, który zostanie otwarty kolejny blok, który wyświetla właściwości dysku, w tym lokalizacja wirtualnego dysku twardego.
4. Skopiuj ten adres URL obiektu blob.
   
   ![Rysowanie](media/marketplace-publishing-vm-image-creation-on-premise/img02.png)
5. Teraz Usuń wdrożonej maszyny Wirtualnej bez usuwania dysków zapasowy. Można je również zatrzymać maszynę Wirtualną zamiast usuwania go. Nie pobieraj wirtualnego dysku twardego systemu operacyjnego, gdy maszyna wirtualna jest uruchomiona.
   
   ![Rysowanie](media/marketplace-publishing-vm-image-creation-on-premise/img03.png)

### <a name="download-a-vhd"></a>Pobieranie wirtualnego dysku twardego
Po określeniu adresu URL obiektu blob, możesz pobrać wirtualny dysk twardy za pomocą [witryny Azure portal](http://manage.windowsazure.com/) lub programu PowerShell.  

> [!NOTE]
> W momencie tworzenia tego przewodnika funkcje, które można pobrać wirtualnego dysku twardego nie jest jeszcze obecne w nowym portalu Microsoft Azure.  
> 
> 

**Pobieranie wirtualnego dysku twardego systemu operacyjnego za pośrednictwem bieżącego [witryny Azure portal](http://manage.windowsazure.com/)**

1. Zaloguj się do witryny Azure portal, jeśli nie zostało to jeszcze zrobione.
2. Kliknij przycisk **magazynu** kartę.
3. Wybierz konto magazynu, w którym znajduje się wirtualny dysk twardy.
   
   ![Rysowanie](media/marketplace-publishing-vm-image-creation-on-premise/img04.png)
4. Spowoduje to wyświetlenie właściwości konta magazynu. Wybierz **kontenery** kartę.
   
   ![Rysowanie](media/marketplace-publishing-vm-image-creation-on-premise/img05.png)
5. Wybierz kontener, w którym znajduje się wirtualny dysk twardy. Domyślnie podczas tworzenia z portalu, wirtualnego dysku twardego są przechowywane w kontenerze wirtualnych dysków twardych.
   
   ![Rysowanie](media/marketplace-publishing-vm-image-creation-on-premise/img06.png)
6. Wybierz poprawny system operacyjny dysku VHD, porównując adres URL, które zostały zapisane.
7. Kliknij pozycję **Pobierz**.
   
   ![Rysowanie](media/marketplace-publishing-vm-image-creation-on-premise/img07.png)

### <a name="download-a-vhd-by-using-powershell"></a>Pobieranie wirtualnego dysku twardego za pomocą programu PowerShell
Oprócz przy użyciu witryny Azure portal, można użyć [Save-AzureVhd](http://msdn.microsoft.com/library/dn495297.aspx) polecenia cmdlet do pobierania wirtualnego dysku twardego systemu operacyjnego.

        Save-AzureVhd –Source <storageURIOfVhd> `
        -LocalFilePath <diskLocationOnWorkstation> `
        -StorageKey <keyForStorageAccount>
Na przykład Save-AzureVhd-źródła "https://baseimagevm.blob.core.windows.net/vhds/BaseImageVM-6820cq00-BaseImageVM-os-1411003770191.vhd" - LocalFilePath "C:\Users\Administrator\Desktop\baseimagevm.vhd" - atrybutu StorageKey <String>

> [!NOTE]
> **Zapisz-AzureVhd** ma również **NumberOfThreads** opcja, która może służyć do zwiększyć równoległość topologii w celu najlepszego wykorzystania przepustowości dostępnych do pobrania.
> 
> 

## <a name="upload-vhds-to-an-azure-storage-account"></a>Przekaż wirtualnych dysków twardych do konta usługi Azure storage
Przygotowywania wirtualnych dysków twardych i lokalną, musisz przekazać je na konto magazynu na platformie Azure. Ten krok ma miejsce po utworzenie wirtualnego dysku twardego w środowisku lokalnym, ale przed uzyskiwanie certyfikacji obrazu maszyny Wirtualnej.

### <a name="create-a-storage-account-and-container"></a>Utwórz konto magazynu i kontener
Zaleca się, że wirtualne dyski twarde można przekazać na konto magazynu w regionie, w Stanach Zjednoczonych. Wszystkie wirtualne dyski twarde dla jednej jednostki SKU będzie umieszczona w jednym kontenerze, w ramach pojedynczego konta magazynu.

Aby utworzyć konto magazynu, można użyć [portalu Microsoft Azure](https://portal.azure.com/), programu PowerShell lub systemu Linux narzędzia wiersza polecenia.  

**Utwórz konto magazynu z poziomu portalu Microsoft Azure**

1. Kliknij pozycję **Utwórz zasób**.
2. Wybierz pozycję **Magazyn**.
3. Wprowadź nazwę konta magazynu, a następnie wybierz lokalizację.
   
   ![Rysowanie](media/marketplace-publishing-vm-image-creation-on-premise/img08.png)
4. Kliknij pozycję **Utwórz**.
5. Blok konto magazynu utworzone powinno być otwarte. Jeśli nie, wybierz **Przeglądaj** > **kont magazynu**. W bloku konta magazynu wybierz utworzone konto magazynu.
6. Wybierz **kontenery**.
   
   ![Rysowanie](media/marketplace-publishing-vm-image-creation-on-premise/img09.png) 
7. W bloku kontenery wybierz **Dodaj**, a następnie wprowadź nazwę kontenera i uprawnień kontenera. Wybierz **prywatnej** uprawnień kontenera.

> [!TIP]
> Zaleca się utworzenie jednego kontenera na jednostki SKU, które planujesz opublikować.
> 
> 

  ![Rysowanie](media/marketplace-publishing-vm-image-creation-on-premise/img10.png)

### <a name="create-a-storage-account-by-using-powershell"></a>Utwórz konto magazynu przy użyciu programu PowerShell
Przy użyciu programu PowerShell, Utwórz konto magazynu przy użyciu [New-AzureStorageAccount](http://msdn.microsoft.com/library/dn495115.aspx) polecenia cmdlet.

        New-AzureStorageAccount -StorageAccountName “mystorageaccount” -Location “West US”

Możesz utworzyć kontener w ramach tego konta magazynu przy użyciu [NewAzureStorageContainer](http://msdn.microsoft.com/library/dn495291.aspx) polecenia cmdlet.

        New-AzureStorageContainer -Name “containername” -Permission “Off”

> [!NOTE]
> Tych poleceniach założono, że bieżący kontekst konta magazynu został już ustawiony w programie PowerShell.   Zapoznaj się [Konfigurowanie programu Azure PowerShell](marketplace-publishing-powershell-setup.md) Aby uzyskać więcej informacji na temat instalacji programu PowerShell.  
> 
> ### <a name="create-a-storage-account-by-using-the-command-line-tool-for-mac-and-linux"></a>Utwórz konto magazynu przy użyciu narzędzia wiersza polecenia dla systemów Mac i Linux
> Z [narzędzie wiersza polecenia systemu Linux](../virtual-machines/linux/cli-manage.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json), Utwórz konto magazynu w następujący sposób.
> 
> 

        azure storage account create mystorageaccount --location "West US"

Utwórz kontener w następujący sposób.

        azure storage container create containername --account-name mystorageaccount --accountkey <accountKey>

## <a name="upload-a-vhd"></a>Przekazywanie wirtualnego dysku twardego
Po utworzeniu konto magazynu i kontener, możesz przekazać przygotowane dyski VHD. Można użyć programu PowerShell, narzędzia wiersza polecenia systemu Linux lub innych narzędzi do zarządzania usługi Azure Storage.

### <a name="upload-a-vhd-via-powershell"></a>Przekazywanie wirtualnego dysku twardego za pomocą programu PowerShell
Użyj [Add-AzureVhd](http://msdn.microsoft.com/library/dn495173.aspx) polecenia cmdlet.

        Add-AzureVhd –Destination “http://mystorageaccount.blob.core.windows.net/containername/vmsku.vhd” -LocalFilePath “C:\Users\Administrator\Desktop\vmsku.vhd”

### <a name="upload-a-vhd-by-using-the-command-line-tool-for-mac-and-linux"></a>Przekazywanie wirtualnego dysku twardego za pomocą narzędzia wiersza polecenia dla systemów Mac i Linux
Za pomocą [narzędzie wiersza polecenia systemu Linux](https://docs.microsoft.com/cli/azure/get-started-with-az-cli2), należy użyć następującego: Tworzenie obrazu maszyny wirtualnej platformy azure <image name> — lokalizacji <Location of the data center> — system operacyjny Linux <LocationOfLocalVHD>

## <a name="see-also"></a>Zobacz także
* [Tworzenie obrazu maszyny wirtualnej w portalu Marketplace](marketplace-publishing-vm-image-creation.md)
* [Konfigurowanie programu Azure PowerShell](marketplace-publishing-powershell-setup.md)

