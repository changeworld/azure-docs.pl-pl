---
title: Przekaż plik wirtualnego dysku twardego do usługi Azure DevTest Labs przy użyciu Eksploratora usługi Microsoft Azure Storage | Dokumentacja firmy Microsoft
description: Przekazywanie pliku VHD do laboratorium konta magazynu za pomocą Eksploratora usługi Microsoft Azure Storage
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
ms.openlocfilehash: 3c187d104334fe75ec9e0ce41a3fdc14b508dfb2
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60623427"
---
# <a name="upload-vhd-file-to-labs-storage-account-using-microsoft-azure-storage-explorer"></a>Przekazywanie pliku VHD do laboratorium konta magazynu za pomocą Eksploratora usługi Microsoft Azure Storage

[!INCLUDE [devtest-lab-upload-vhd-selector](../../includes/devtest-lab-upload-vhd-selector.md)]

W usłudze Azure DevTest Labs pliki wirtualnego dysku twardego może służyć do tworzenia obrazów niestandardowych, które są używane do obsługi administracyjnej maszyn wirtualnych. W tym artykule przedstawiono sposób użycia [Microsoft Azure Storage Explorer](../vs-azure-tools-storage-manage-with-storage-explorer.md) Aby przesłać plik VHD do konta magazynu w laboratorium. Po przekazaniu pliku wirtualnego dysku twardego [następne kroki z sekcji](#next-steps) zawiera artykuły, które ilustrują sposób tworzenie obrazu niestandardowego z przekazanego pliku wirtualnego dysku twardego. Aby uzyskać więcej informacji o dyskach i wirtualnych dyskach twardych na platformie Azure, zobacz [wprowadzenie do usługi managed disks](../virtual-machines/linux/managed-disks-overview.md)

## <a name="step-by-step-instructions"></a>Instrukcje krok po kroku

W poniższych krokach objaśniono proces przekazywania pliku VHD za pomocą usługi DevTest Labs [Microsoft Azure Storage Explorer](../vs-azure-tools-storage-manage-with-storage-explorer.md).

1. [Pobierz i zainstaluj najnowszą wersję programu Microsoft Azure Storage Explorer](https://www.storageexplorer.com).

1. Pobierz nazwę konta magazynu laboratorium przy użyciu witryny Azure portal:

    1. Zaloguj się w witrynie [Azure Portal](https://go.microsoft.com/fwlink/p/?LinkID=525040).
    
    1. Wybierz **wszystkich usług**, a następnie wybierz pozycję **DevTest Labs** z listy.
    
    1. Z listy labs wybierz żądane laboratorium.  
    
    1. W bloku laboratorium wybierz **konfiguracji**. 
    
    1. W środowisku laboratoryjnym **konfiguracji** bloku wybierz **obrazów niestandardowych (VHD)**.
    
    1. Na **niestandardowych obrazów** bloku wybierz **+ Dodaj**. 
    
    1. Na **obraz niestandardowy** bloku wybierz **wirtualnego dysku twardego**.
    
    1. Na **wirtualnego dysku twardego** bloku wybierz **przekazania dysku VHD za pomocą programu PowerShell**.
    
        ![Przekazywanie wirtualnego dysku twardego przy użyciu programu PowerShell][0]
    
    1. **Przekazać obraz przy użyciu programu PowerShell** bloku wyświetlane są wywołania **Add-AzureVhd** polecenia cmdlet. Pierwszy parametr (*docelowy*) zawiera nazwę konta magazynu w środowisku laboratoryjnym w następującym formacie:
    
        `https://<STORAGE-ACCOUNT-NAME>.blob.core.windows.net/uploads/...`

    1. Zanotuj nazwę konta magazynu jest używany w kolejnych krokach.
    
1. Łączenie konta subskrypcji platformy Azure przy użyciu Eksploratora usługi Storage.

    > [!TIP] 
    > 
    > Eksplorator usługi Storage obsługuje kilka opcji połączenia. W tej sekcji przedstawiono nawiązywania połączenia z konta magazynu skojarzonego z subskrypcją platformy Azure. Aby wyświetlić inne opcje połączenia obsługiwane przez Eksploratora usługi Storage, zapoznaj się z artykułem [wprowadzenie do Eksploratora usługi Storage](../vs-azure-tools-storage-manage-with-storage-explorer.md).
 
    1. Otwórz Eksploratora usługi Storage.
    
    1. W Eksploratorze usługi Storage wybierz **ustawienia konta platformy Azure**. 
    
        ![Ustawienia konta platformy Azure][1]
    
    1. Okienka po lewej stronie wyświetlane konta Microsoft, którego zalogowano się. Aby połączyć się z innym kontem, wybierz pozycję **Dodaj konto** i postępuj zgodnie z instrukcjami wyświetlanymi w oknach dialogowych, aby zalogować się przy użyciu konta Microsoft skojarzonego z co najmniej jedną aktywną subskrypcją platformy Azure.
    
        ![Dodaj konto][2]
    
    1. Po pomyślnym zalogowaniu się przy użyciu konta Microsoft w okienku po lewej stronie zostaną wyświetlone wszystkie subskrypcje platformy Azure skojarzone z tym kontem. Wybierz subskrypcję platformy Azure, przy użyciu której chcesz pracować, a następnie wybierz przycisk **Zastosuj**. (Zaznaczenie **wszystkie subskrypcje** przełącza wybór wszystkich lub żadnej z wymienionych subskrypcji platformy Azure.)
    
        ![Wybieranie subskrypcji platformy Azure][3]
    
    1. W okienku po lewej stronie są wyświetlane wszystkie konta magazynu skojarzone z wybranymi subskrypcjami platformy Azure.
    
        ![Wybrane subskrypcje platformy Azure][4]

1. Znajdź konto magazynu laboratorium:

    1. W lewym okienku programu Storage Explorer Znajdź i rozwiń węzeł dla subskrypcji platformy Azure, który jest właścicielem laboratorium.
    
    1. W węźle subskrypcji rozwiń **kont magazynu**.

    1. Rozwiń węzeł konta magazynu w laboratorium, aby wyświetlić węzły **kontenery obiektów Blob**, **udziałów plików**, **kolejek**, i **tabel**.
    
    1. Rozwiń **kontenery obiektów Blob** węzła.
    
    1. Wybierz kontener obiektów blob przekazywania, aby wyświetlić jego zawartość w okienku po prawej stronie.
        
        ![Przekazać katalogu][5]

1. Przekazywanie pliku VHD, za pomocą Eksploratora usługi Storage:

    1. W okienku po prawej stronie Eksploratora usługi Storage, należy wyświetlić listę obiektów blob w **przekazuje** kontenera obiektów blob konta magazynu w laboratorium. Na pasku narzędzi edytora obiektów blob, wybierz **przekazywania** 
        
        ![Przycisk Przekaż][6]
    
    1. Z **przekazywanie** menu rozwijanego wybierz opcję **Przekaż pliki...** .
    
    1. Na **przekazywanie plików** okno dialogowe, wybierz wielokropek.
        
        ![Wybierz plik][8]  

    1. Na **Wybieranie plików do przekazania** okno dialogowe, przejdź do żądanego pliku wirtualnego dysku twardego, zaznacz go, a następnie wybierz **Otwórz**.
    
    1. Po zwróceniu do **przekazywanie plików** okno dialogowe, zmiana **typu Blob** do **stronicowych obiektów Blob**.
    
    1. Wybierz pozycję **Przekaż**.

        ![Wybierz plik][9]  
    
    1. Program Storage Explorer **dziennika aktywności** okienko zawiera stan pobierania (wraz z linkami do anulować przekazywanie). Proces przesyłania pliku VHD można długich w zależności od rozmiaru pliku wirtualnego dysku twardego i szybkość połączenia. 

        ![Stan przekazywania pliku][10]  

## <a name="next-steps"></a>Kolejne kroki

- [Tworzenie obrazu niestandardowego w usłudze Azure DevTest Labs z pliku VHD za pomocą witryny Azure portal](devtest-lab-create-template.md)
- [Tworzenie obrazu niestandardowego w usłudze Azure DevTest Labs z pliku VHD za pomocą programu PowerShell](devtest-lab-create-custom-image-from-vhd-using-powershell.md)

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
