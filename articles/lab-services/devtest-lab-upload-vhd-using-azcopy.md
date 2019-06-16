---
title: Przekaż plik wirtualnego dysku twardego do usługi Azure DevTest Labs przy użyciu narzędzia AzCopy | Dokumentacja firmy Microsoft
description: Przekazywanie pliku VHD do laboratorium konta magazynu przy użyciu narzędzia AzCopy
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.assetid: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/17/2018
ms.author: spelluru
ms.openlocfilehash: 8cd778762bebf4a9dda3688292ac0a3674e446e1
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60634990"
---
# <a name="upload-vhd-file-to-labs-storage-account-using-azcopy"></a>Przekazywanie pliku VHD do laboratorium konta magazynu przy użyciu narzędzia AzCopy

[!INCLUDE [devtest-lab-upload-vhd-selector](../../includes/devtest-lab-upload-vhd-selector.md)]

W usłudze Azure DevTest Labs pliki wirtualnego dysku twardego może służyć do tworzenia obrazów niestandardowych, które są używane do obsługi administracyjnej maszyn wirtualnych. W poniższych krokach objaśniono za pomocą narzędzia wiersza polecenia AzCopy, aby przesłać plik VHD do konta magazynu w laboratorium. Po przekazaniu pliku wirtualnego dysku twardego [następne kroki z sekcji](#next-steps) zawiera artykuły, które ilustrują sposób tworzenie obrazu niestandardowego z przekazanego pliku wirtualnego dysku twardego. Aby uzyskać więcej informacji o dyskach i wirtualnych dyskach twardych na platformie Azure, zobacz [wprowadzenie do usługi managed disks](../virtual-machines/linux/managed-disks-overview.md)

> [!NOTE] 
>  
> AzCopy to narzędzie wiersza polecenia tylko do Windows.

## <a name="step-by-step-instructions"></a>Instrukcje krok po kroku

W poniższych krokach objaśniono proces przekazywania pliku VHD za pomocą usługi Azure DevTest Labs [AzCopy](https://aka.ms/downloadazcopy). 

1. Pobierz nazwę konta magazynu laboratorium przy użyciu witryny Azure portal:

1. Zaloguj się w witrynie [Azure Portal](https://go.microsoft.com/fwlink/p/?LinkID=525040).

1. Wybierz **wszystkich usług**, a następnie wybierz pozycję **DevTest Labs** z listy.

1. Z listy labs wybierz żądane laboratorium.  

1. W bloku laboratorium wybierz **konfiguracji**. 

1. W środowisku laboratoryjnym **konfiguracji** bloku wybierz **obrazów niestandardowych (VHD)** .

1. Na **niestandardowych obrazów** bloku wybierz **+ Dodaj**. 

1. Na **obraz niestandardowy** bloku wybierz **wirtualnego dysku twardego**.

1. Na **wirtualnego dysku twardego** bloku wybierz **przekazania dysku VHD za pomocą programu PowerShell**.

    ![Przekazywanie wirtualnego dysku twardego przy użyciu programu PowerShell](./media/devtest-lab-upload-vhd-using-azcopy/upload-image-using-psh.png)

1. **Przekazać obraz przy użyciu programu PowerShell** bloku wyświetlane są wywołania **Add-AzureVhd** polecenia cmdlet. Pierwszy parametr (*docelowy*) zawiera identyfikator URI kontenera obiektów blob (*przekazuje*) w następującym formacie:

    ```
    https://<STORAGE-ACCOUNT-NAME>.blob.core.windows.net/uploads/...
    ``` 

1. Zanotuj pełny identyfikator URI jest używany w kolejnych krokach.

1. Przekazywanie pliku VHD, za pomocą narzędzia AzCopy:
 
1. [Pobierz i zainstaluj najnowszą wersję programu AzCopy](https://aka.ms/downloadazcopy).

1. Otwórz okno polecenia i przejdź do katalogu instalacyjnego programu AzCopy. Opcjonalnie można dodać lokalizacji instalacji narzędzia AzCopy do ścieżki systemowej. Domyślnie narzędzie AzCopy jest zainstalowane do następującego katalogu:

    ```command-line
    %ProgramFiles(x86)%\Microsoft SDKs\Azure\AzCopy
    ```

1. Za pomocą kontenera konta magazynu klucz i obiektów blob identyfikatora URI, uruchom następujące polecenie w wierszu polecenia. *VhdFileName* wartość musi być w cudzysłowie. Proces przesyłania pliku VHD można długich w zależności od rozmiaru pliku wirtualnego dysku twardego i szybkość połączenia.   

    ```command-line
    AzCopy /Source:<sourceDirectory> /Dest:<blobContainerUri> /DestKey:<storageAccountKey> /Pattern:"<vhdFileName>" /BlobType:page
    ```

## <a name="next-steps"></a>Kolejne kroki

- [Tworzenie obrazu niestandardowego w usłudze Azure DevTest Labs z pliku VHD za pomocą witryny Azure portal](devtest-lab-create-template.md)
- [Tworzenie obrazu niestandardowego w usłudze Azure DevTest Labs z pliku VHD za pomocą programu PowerShell](devtest-lab-create-custom-image-from-vhd-using-powershell.md)