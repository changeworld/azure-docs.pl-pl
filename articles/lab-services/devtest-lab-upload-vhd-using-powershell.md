---
title: Przekaż plik wirtualnego dysku twardego do usługi Azure DevTest Labs przy użyciu programu PowerShell | Dokumentacja firmy Microsoft
description: Przekazywanie pliku VHD do laboratorium konta magazynu przy użyciu programu PowerShell
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
ms.openlocfilehash: 56a66c3eb1dad93fad3ad1572989dc0c0aa14632
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60622773"
---
# <a name="upload-vhd-file-to-labs-storage-account-using-powershell"></a>Przekazywanie pliku VHD do laboratorium konta magazynu przy użyciu programu PowerShell

[!INCLUDE [devtest-lab-upload-vhd-selector](../../includes/devtest-lab-upload-vhd-selector.md)]

W usłudze Azure DevTest Labs pliki wirtualnego dysku twardego może służyć do tworzenia obrazów niestandardowych, które są używane do obsługi administracyjnej maszyn wirtualnych. W poniższych krokach objaśniono za pośrednictwem przy użyciu programu PowerShell, aby przesłać plik VHD do konta magazynu w laboratorium. Po przekazaniu pliku wirtualnego dysku twardego [następne kroki z sekcji](#next-steps) zawiera artykuły, które ilustrują sposób tworzenie obrazu niestandardowego z przekazanego pliku wirtualnego dysku twardego. Aby uzyskać więcej informacji o dyskach i wirtualnych dyskach twardych na platformie Azure, zobacz [wprowadzenie do usługi managed disks](../virtual-machines/linux/managed-disks-overview.md)

## <a name="step-by-step-instructions"></a>Instrukcje krok po kroku

W poniższych krokach objaśniono proces przekazywania pliku VHD do usługi Azure DevTest Labs przy użyciu programu PowerShell. 

1. Zaloguj się w witrynie [Azure Portal](https://go.microsoft.com/fwlink/p/?LinkID=525040).

1. Wybierz **wszystkich usług**, a następnie wybierz pozycję **DevTest Labs** z listy.

1. Z listy labs wybierz żądane laboratorium.  

1. W bloku laboratorium wybierz **konfiguracji**. 

1. W środowisku laboratoryjnym **konfiguracji** bloku wybierz **obrazów niestandardowych (VHD)** .

1. Na **niestandardowych obrazów** bloku wybierz **+ Dodaj**. 

1. Na **obraz niestandardowy** bloku wybierz **wirtualnego dysku twardego**.

1. Na **wirtualnego dysku twardego** bloku wybierz **przekazania dysku VHD za pomocą programu PowerShell**.

    ![Przekazywanie wirtualnego dysku twardego przy użyciu programu PowerShell](./media/devtest-lab-upload-vhd-using-powershell/upload-image-using-psh.png)

1. Na **przekazać obraz przy użyciu programu PowerShell** bloku, skopiuj wygenerowany skrypt programu PowerShell do edytora tekstów.

1. Modyfikowanie **LocalFilePath** parametru **Add-AzureVhd** polecenia cmdlet, aby wskazywała lokalizację pliku wirtualnego dysku twardego, który chcesz przekazać.

1. W wierszu polecenia programu PowerShell, uruchom **Add-AzureVhd** polecenia cmdlet (za pomocą zmodyfikowanego **LocalFilePath** parametru).

> [!WARNING] 
> 
> Proces przesyłania pliku VHD można długich w zależności od rozmiaru pliku wirtualnego dysku twardego i szybkość połączenia.

## <a name="next-steps"></a>Kolejne kroki

- [Tworzenie obrazu niestandardowego w usłudze Azure DevTest Labs z pliku VHD za pomocą witryny Azure portal](devtest-lab-create-template.md)
- [Tworzenie obrazu niestandardowego w usłudze Azure DevTest Labs z pliku VHD za pomocą programu PowerShell](devtest-lab-create-custom-image-from-vhd-using-powershell.md)
