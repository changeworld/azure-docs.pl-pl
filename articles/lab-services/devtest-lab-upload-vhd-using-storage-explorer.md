---
title: Przekaż plik wirtualnego dysku twardego na usłudze Azure DevTest Labs za pomocą Eksploratora usługi Microsoft Azure Storage | Dokumentacja firmy Microsoft
description: Przekaż plik wirtualnego dysku twardego na konto magazynu w laboratorium, przy użyciu Eksploratora magazynu Microsoft Azure
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
ms.openlocfilehash: cafb3ec2da335165a3b66c18d83930008c6d72e5
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/07/2018
ms.locfileid: "33788046"
---
# <a name="upload-vhd-file-to-labs-storage-account-using-microsoft-azure-storage-explorer"></a>Przekaż plik wirtualnego dysku twardego na konto magazynu w laboratorium, przy użyciu Eksploratora magazynu Microsoft Azure

[!INCLUDE [devtest-lab-upload-vhd-selector](../../includes/devtest-lab-upload-vhd-selector.md)]

W usłudze Azure DevTest Labs pliki wirtualnego dysku twardego może służyć do tworzenia niestandardowych obrazów, które są używane do obsługi administracyjnej maszyn wirtualnych. W tym artykule przedstawiono sposób użycia [Eksploratora usługi Microsoft Azure Storage](../vs-azure-tools-storage-manage-with-storage-explorer.md) można przekazać pliku dysku VHD do konta magazynu laboratorium. Po przesłaniu pliku wirtualnego dysku twardego [następne kroki sekcji](#next-steps) wymieniono niektóre artykuły, które przedstawiają sposób tworzenia niestandardowego obrazu z przekazanego pliku wirtualnego dysku twardego. Aby uzyskać więcej informacji o dyskach i wirtualne dyski twarde na platformie Azure, zobacz [o dyskach i wirtualne dyski twarde dla maszyn wirtualnych](../virtual-machines/linux/about-disks-and-vhds.md)

## <a name="step-by-step-instructions"></a>Instrukcje krok po kroku

W poniższych krokach objaśniono za pośrednictwem przekazywania pliku wirtualnego dysku twardego za pomocą DevTest Labs [Eksploratora usługi Microsoft Azure Storage](../vs-azure-tools-storage-manage-with-storage-explorer.md).

1. [Pobierz i zainstaluj najnowszą wersję Eksploratora magazynu Microsoft Azure](http://www.storageexplorer.com).

1. Pobierz nazwę konta magazynu laboratorium należy utworzyć przy użyciu portalu Azure:

    1. Zaloguj się w witrynie [Azure Portal](http://go.microsoft.com/fwlink/p/?LinkID=525040).
    
    1. Wybierz **wszystkie usługi**, a następnie wybierz **DevTest Labs** z listy.
    
    1. Z listy labs wybierz żądany laboratorium.  
    
    1. W bloku laboratorium, wybierz **konfiguracji**. 
    
    1. W środowisku laboratoryjnym **konfiguracji** bloku, wybierz opcję **niestandardowych obrazów (VHD)**.
    
    1. Na **niestandardowych obrazów** bloku, wybierz **+ Dodaj**. 
    
    1. Na **obraz niestandardowy** bloku, wybierz opcję **wirtualnego dysku twardego**.
    
    1. Na **wirtualnego dysku twardego** bloku, wybierz opcję **przekazania dysku VHD za pomocą programu PowerShell**.
    
        ![Przekaż plik VHD za pomocą programu PowerShell][0]
    
    1. **Przekazywanie obrazu za pomocą programu PowerShell** bloku Wyświetla wywołanie **Add-AzureVhd** polecenia cmdlet. Pierwszy parametr (*docelowego*) zawiera nazwę konta magazynu dla laboratorium w następującym formacie:
    
        https://<STORAGE-ACCOUNT-NAME>.blob.core.windows.net/uploads/... 

    1. Zwróć uwagę na nazwy konta magazynu, ponieważ jest używana w dalszych krokach.
    
1. Podłącz do konta subskrypcji platformy Azure przy użyciu Eksploratora usługi Storage.

    > [!TIP] 
    > 
    > Eksplorator usługi Storage obsługuje kilka opcji połączeń. W tej części przedstawiono nawiązywania połączenia z kontem magazynu skojarzone z subskrypcją platformy Azure. Aby wyświetlić inne opcje połączenia obsługiwane przez Eksploratora usługi Storage, zapoznaj się z artykułem [wprowadzenie do Eksploratora usługi Storage](../vs-azure-tools-storage-manage-with-storage-explorer.md).
 
    1. Otwórz Eksploratora usługi Storage.
    
    1. W Eksploratorze usługi Storage, wybierz **ustawienia konta Azure**. 
    
        ![Ustawienia konta platformy Azure][1]
    
    1. W okienku po lewej stronie wyświetlane kont firmy Microsoft, do których logujesz się do. Aby połączyć się z innym kontem, wybierz pozycję **Dodaj konto** i postępuj zgodnie z instrukcjami wyświetlanymi w oknach dialogowych, aby zalogować się przy użyciu konta Microsoft skojarzonego z co najmniej jedną aktywną subskrypcją platformy Azure.
    
        ![Dodaj konto][2]
    
    1. Po pomyślnym zalogowaniu się przy użyciu konta Microsoft w okienku po lewej stronie zostaną wyświetlone wszystkie subskrypcje platformy Azure skojarzone z tym kontem. Wybierz subskrypcję platformy Azure, przy użyciu której chcesz pracować, a następnie wybierz przycisk **Zastosuj**. (Wybieranie **wszystkie subskrypcje** przełącza wybór wszystkich lub żadna z wymienionych subskrypcji platformy Azure.)
    
        ![Wybieranie subskrypcji platformy Azure][3]
    
    1. W okienku po lewej stronie są wyświetlane wszystkie konta magazynu skojarzone z wybranymi subskrypcjami platformy Azure.
    
        ![Wybrane subskrypcje platformy Azure][4]

1. Znajdź laboratorium należy utworzyć konta magazynu:

    1. W okienku po lewej stronie Eksploratora usługi Storage Znajdź i rozwiń węzeł subskrypcji platformy Azure, który jest właścicielem laboratorium.
    
    1. W węźle subskrypcji, rozwiń węzeł **kont magazynu**.

    1. Rozwiń węzeł konta magazynu laboratorium tak, aby ujawnić węzły **kontenerów obiektów Blob**, **udziałów plików**, **kolejek**, i **tabel**.
    
    1. Rozwiń węzeł **kontenerów obiektów Blob** węzła.
    
    1. Wybierz kontener obiektów blob przekazywania, aby wyświetlić jego zawartość w okienku po prawej stronie.
        
        ![Przekaż katalogu][5]

1. Przekaż plik VHD za pomocą Eksploratora usługi Storage:

    1. W okienku po prawej stronie Eksploratora usługi Storage, powinna zostać wyświetlona lista obiektów blob w **przekazuje** kontenera obiektów blob laboratorium należy utworzyć konta magazynu. Na pasku narzędzi edytora obiektów blob, wybierz **Przekaż** 
        
        ![Przycisk Przekaż][6]
    
    1. Z **przekazać** menu rozwijanego wybierz **przekazywania plików...** .
    
    1. Na **przekazać pliki** okno dialogowe, wybierz wielokropek.
        
        ![Wybierz plik][8]  

    1. Na **Wybieranie plików do przekazania** okno dialogowe, przejdź do żądanego pliku VHD, zaznacz go, a następnie wybierz **Otwórz**.
    
    1. Gdy zwrócony do **przekazać pliki** okna dialogowego, zmień **typu obiektu Blob** do **stronicowych obiektów Blob**.
    
    1. Wybierz pozycję **Przekaż**.

        ![Wybierz plik][9]  
    
    1. Eksplorator magazynu **dziennik aktywności** w okienku zostaną wyświetlone stan pobierania (wraz z łącza anulować to przekazywanie). Proces przekazywania pliku wirtualnego dysku twardego może być długi w zależności od rozmiaru pliku wirtualnego dysku twardego i szybkość połączenia. 

        ![Stan przekazywania pliku][10]  

## <a name="next-steps"></a>Kolejne kroki

- [Utworzyć obraz niestandardowy w usłudze Azure DevTest Labs z pliku VHD za pomocą portalu Azure](devtest-lab-create-template.md)
- [Utworzyć obraz niestandardowy w usłudze Azure DevTest Labs z pliku VHD za pomocą programu PowerShell](devtest-lab-create-custom-image-from-vhd-using-powershell.md)

[0]: ./media/devtest-lab-upload-vhd-using-storage-explorer/upload-image-using-psh.png
[1]: ./media/devtest-lab-upload-vhd-using-storage-explorer/settings-icon.png
[2]: ./media/devtest-lab-upload-vhd-using-storage-explorer/add-account-link.png
[3]: ./media/devtest-lab-upload-vhd-using-storage-explorer/subscriptions-list.png
[4]: ./media/devtest-lab-upload-vhd-using-storage-explorer/storage-accounts-list.png
[5]: ./media/devtest-lab-upload-vhd-using-storage-explorer/upload-dir.png
[6]: ./media/devtest-lab-upload-vhd-using-storage-explorer/upload-button.png
[7]: ./media/devtest-lab-upload-vhd-using-storage-explorer/upload-files.png
[8]: ./media/devtest-lab-upload-vhd-using-storage-explorer/select-file.png
[9]: ./media/devtest-lab-upload-vhd-using-storage-explorer/upload-file.png
[10]: ./media/devtest-lab-upload-vhd-using-storage-explorer/upload-status.png
