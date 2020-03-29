---
title: Przekazywanie pliku VHD do laboratoriów devtest platformy Azure przy użyciu AzCopy | Dokumenty firmy Microsoft
description: Ten artykuł zawiera przewodnik do korzystania z narzędzia wiersza polecenia AzCopy do przekazywania pliku VHD do konta magazynu laboratorium w usłudze Azure DevTest Labs.
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
ms.date: 01/24/2020
ms.author: spelluru
ms.openlocfilehash: 49dc70788bf2a44b6925c5f3f8226fdadab8768c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76757426"
---
# <a name="upload-vhd-file-to-labs-storage-account-using-azcopy"></a>Prześlij plik VHD na konto magazynu laboratorium za pomocą AzCopy

[!INCLUDE [devtest-lab-upload-vhd-selector](../../includes/devtest-lab-upload-vhd-selector.md)]

W laboratoriach DevTest Azure pliki VHD mogą być używane do tworzenia obrazów niestandardowych, które są używane do inicjowania obsługi administracyjnej maszyn wirtualnych. Poniższe kroki umożliwiają przejście przez za pomocą narzędzia wiersza polecenia AzCopy do przekazywania pliku VHD do konta magazynu w laboratorium. Po przesłaniu pliku VHD w [sekcji Następne kroki](#next-steps) wymieniono kilka artykułów ilustrujących sposób tworzenia niestandardowego obrazu z przesłanego pliku VHD. Aby uzyskać więcej informacji na temat dysków i dysków VHD na platformie Azure, zobacz [Wprowadzenie do dysków zarządzanych](../virtual-machines/linux/managed-disks-overview.md)

> [!NOTE] 
>  
> AzCopy jest narzędziem wiersza polecenia tylko dla systemu Windows.

## <a name="step-by-step-instructions"></a>Instrukcje krok po kroku

Poniższe kroki przebiegają przez przekazywanie pliku VHD do usługi Azure DevTest Labs przy użyciu [programu AzCopy](https://aka.ms/downloadazcopy). 

1. Pobierz nazwę konta magazynu laboratorium przy użyciu witryny Azure portal:

1. Zaloguj się do [Portalu Azure](https://go.microsoft.com/fwlink/p/?LinkID=525040).

1. Wybierz **pozycję Wszystkie usługi**, a następnie wybierz z listy pozycję **DevTest Labs.**

1. Z listy laboratoriów wybierz żądane laboratorium.  

1. Na bloku laboratorium wybierz opcję **Konfiguracja**. 

1. W bloku **Konfiguracji** laboratorium wybierz opcję **Obrazy niestandardowe (VED)**.

1. W bloku **Obrazy niestandardowe** wybierz **pozycję +Dodaj**. 

1. Na **bloku Obraz niestandardowy** wybierz pozycję **VHD**.

1. Na bloku **VHD** wybierz **pozycję Przekaż dysk VHD za pomocą programu PowerShell**.

    ![Przekazywanie dysku VHD za pomocą programu PowerShell](./media/devtest-lab-upload-vhd-using-azcopy/upload-image-using-psh.png)

1. Przekaż obraz przy użyciu bloku **programu PowerShell** wyświetla wywołanie polecenia cmdlet **Add-AzureVhd.** Pierwszy parametr (*Miejsce docelowe*) zawiera identyfikator URI dla kontenera obiektów blob *(uploads)* w następującym formacie:

    ```
    https://<STORAGE-ACCOUNT-NAME>.blob.core.windows.net/uploads/...
    ``` 

1. Zanotuj pełny identyfikator URI, ponieważ jest on używany w późniejszych krokach.

1. Prześlij plik VHD za pomocą AzCopy:
 
1. [Pobierz i zainstaluj najnowszą wersję AzCopy](https://aka.ms/downloadazcopy).

1. Otwórz okno polecenia i przejdź do katalogu instalacyjnego AzCopy. Opcjonalnie można dodać lokalizację instalacji AzCopy do ścieżki systemowej. Domyślnie AzCopy jest instalowany w następującym katalogu:

    ```command-line
    %ProgramFiles(x86)%\Microsoft SDKs\Azure\AzCopy
    ```

1. Korzystając z klucza konta magazynu i identyfikatora URI kontenera obiektów blob, uruchom następujące polecenie w wierszu polecenia. Wartość *vhdFileName* musi być w cudzysłowie. Proces przesyłania pliku VHD może być długi w zależności od rozmiaru pliku VHD i szybkości połączenia.   

    ```command-line
    AzCopy /Source:<sourceDirectory> /Dest:<blobContainerUri> /DestKey:<storageAccountKey> /Pattern:"<vhdFileName>" /BlobType:page
    ```

## <a name="next-steps"></a>Następne kroki

- [Tworzenie niestandardowego obrazu w laboratoriach devtest azure z pliku VHD przy użyciu portalu Azure](devtest-lab-create-template.md)
- [Tworzenie niestandardowego obrazu w laboratoriach devtest platformy Azure na podstawie pliku VHD przy użyciu programu PowerShell](devtest-lab-create-custom-image-from-vhd-using-powershell.md)