---
title: Przekazywanie pliku VHD do laboratoriów devtest platformy Azure przy użyciu Eksploratora magazynu
description: Przekazywanie pliku VHD do konta magazynu laboratorium przy użyciu Eksploratora usługi Microsoft Azure Storage Explorer
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
ms.date: 01/16/2020
ms.author: spelluru
ms.openlocfilehash: defafdd5809b7e537b3b9abb78f8cb63d0033c16
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76170373"
---
# <a name="upload-vhd-file-to-labs-storage-account-using-microsoft-azure-storage-explorer"></a>Przekazywanie pliku VHD do konta magazynu laboratorium przy użyciu Eksploratora usługi Microsoft Azure Storage Explorer

[!INCLUDE [devtest-lab-upload-vhd-selector](../../includes/devtest-lab-upload-vhd-selector.md)]

W laboratoriach DevTest Azure pliki VHD mogą być używane do tworzenia obrazów niestandardowych, które są używane do inicjowania obsługi administracyjnej maszyn wirtualnych. W tym artykule pokazano, jak użyć [Eksploratora usługi Microsoft Azure Storage](../vs-azure-tools-storage-manage-with-storage-explorer.md) do przekazania pliku VHD na konto magazynu w laboratorium. Po przesłaniu pliku VHD w [sekcji Następne kroki](#next-steps) wymieniono kilka artykułów ilustrujących sposób tworzenia niestandardowego obrazu z przesłanego pliku VHD. Aby uzyskać więcej informacji na temat dysków i dysków VHD na platformie Azure, zobacz [Wprowadzenie do dysków zarządzanych](../virtual-machines/linux/managed-disks-overview.md)

## <a name="step-by-step-instructions"></a>Instrukcje krok po kroku

Poniższe kroki przebiegają przez przekazywanie pliku VHD do DevTest Labs przy użyciu [Eksploratora Microsoft Azure Storage Explorer](../vs-azure-tools-storage-manage-with-storage-explorer.md).

1. [Pobierz i zainstaluj najnowszą wersję Eksploratora magazynu Microsoft Azure](https://www.storageexplorer.com).

1. Pobierz nazwę konta magazynu laboratorium przy użyciu witryny Azure portal:

    1. Zaloguj się do [Portalu Azure](https://go.microsoft.com/fwlink/p/?LinkID=525040).
    
    1. Wybierz **pozycję Wszystkie usługi**, a następnie wybierz z listy pozycję **DevTest Labs.**
    
    1. Z listy laboratoriów wybierz żądane laboratorium.  
    
    1. Na bloku laboratorium wybierz opcję **Konfiguracja**. 
    
    1. W bloku **Konfiguracji** laboratorium wybierz opcję **Obrazy niestandardowe (VED)**.
    
    1. W bloku **Obrazy niestandardowe** wybierz **pozycję +Dodaj**. 
    
    1. Na **bloku Obraz niestandardowy** wybierz pozycję **VHD**.
    
    1. Na bloku **VHD** wybierz **pozycję Przekaż dysk VHD za pomocą programu PowerShell**.
    
        ![Przekazywanie dysku VHD za pomocą programu PowerShell][0]
    
    1. Przekaż obraz przy użyciu bloku **programu PowerShell** wyświetla wywołanie polecenia cmdlet **Add-AzureVhd.** Pierwszy parametr (*Miejsce docelowe*) zawiera nazwę konta magazynu dla laboratorium w następującym formacie:
    
        `https://<STORAGE-ACCOUNT-NAME>.blob.core.windows.net/uploads/...`

    1. Zanotuj nazwę konta magazynu, która jest używana w późniejszych krokach.
    
1. Połącz się z kontem subskrypcji platformy Azure przy użyciu Eksploratora magazynu.

    > [!TIP] 
    > 
    > Eksplorator magazynu obsługuje kilka opcji połączenia. W tej sekcji przedstawiono łączenie się z kontem magazynu skojarzonym z subskrypcją platformy Azure. Aby wyświetlić inne opcje połączenia obsługiwane przez Eksploratora magazynu, zapoznaj się z tym artykułem [Wprowadzenie do Eksploratora magazynu](../vs-azure-tools-storage-manage-with-storage-explorer.md).
 
    1. Otwórz Eksploratora usługi Storage.
    
    1. W Eksploratorze magazynu wybierz **pozycję Ustawienia konta platformy Azure**. 
    
        ![Ustawienia konta platformy Azure][1]
    
    1. W lewym okienku są wyświetlane konta Microsoft, do których się zalogowano. Aby połączyć się z innym kontem, wybierz pozycję **Dodaj konto** i postępuj zgodnie z instrukcjami wyświetlanymi w oknach dialogowych, aby zalogować się przy użyciu konta Microsoft skojarzonego z co najmniej jedną aktywną subskrypcją platformy Azure.
    
        ![Dodaj konto][2]
    
    1. Po pomyślnym zalogowaniu się przy użyciu konta Microsoft w okienku po lewej stronie zostaną wyświetlone wszystkie subskrypcje platformy Azure skojarzone z tym kontem. Wybierz subskrypcję platformy Azure, przy użyciu której chcesz pracować, a następnie wybierz przycisk **Zastosuj**. (Wybranie **opcji Wszystkie subskrypcje** powoduje przełączenie wyboru wszystkich lub żadnej z wymienionych subskrypcji platformy Azure).
    
        ![Wybieranie subskrypcji platformy Azure][3]
    
    1. W okienku po lewej stronie są wyświetlane wszystkie konta magazynu skojarzone z wybranymi subskrypcjami platformy Azure.
    
        ![Wybrane subskrypcje platformy Azure][4]

1. Znajdź konto magazynu laboratorium:

    1. W lewym okienku Eksploratora magazynu znajdź i rozwiń węzeł subskrypcji platformy Azure, która jest właścicielem laboratorium.
    
    1. W obszarze węzła subskrypcji rozwiń węzeł **Konta magazynu**.

    1. Rozwiń węzeł konta magazynu laboratorium, aby wyświetlić węzły **kontenerów obiektów Blob,** **udziałów plików,** **kolejek**i **tabel.**
    
    1. Rozwiń węzeł **Kontenery obiektów blob.**
    
    1. Wybierz kontener obiektów blob przekazywania, aby wyświetlić jego zawartość w prawym okienku.
        
        ![Prześlij katalog][5]

1. Przekaż plik VHD za pomocą Eksploratora magazynu:

    1. W prawym okienku Eksplorator magazynu powinna zostać wyświetleni lista obiektów blob w kontenerze obiektów blob **przekazywania** konta magazynu laboratorium. Na pasku narzędzi edytora obiektów blob wybierz pozycję **Przekaż** 
        
        ![Przycisk Przekaż][6]
    
    1. Z menu rozwijanego **Przesyłanie** wybierz pozycję **Przekaż pliki...**.
    
    1. W oknie dialogowym **Przekazywanie plików** wybierz wielokropek.
        
        ![Wybierz plik][8]  

    1. W oknie dialogowym **Wybieranie plików do przekazania** przejdź do żądanego pliku VHD, wybierz go, a następnie wybierz pozycję **Otwórz**.
    
    1. Po powrocie do okna dialogowego **Przekazywanie plików** zmień **typ obiektu Blob** na **Obiekt Blob strony**.
    
    1. Wybierz **pozycję Przekaż**.

        ![Wybierz plik][9]  
    
    1. W okienku **Dziennik aktywności** Eksploratora magazynu jest wyświetlany stan pobierania (wraz z łączami do anulowania przekazywania). Proces przesyłania pliku VHD może być długi w zależności od rozmiaru pliku VHD i szybkości połączenia. 

        ![Stan pliku przekazywania][10]  

## <a name="next-steps"></a>Następne kroki

- [Tworzenie niestandardowego obrazu w laboratoriach devtest azure z pliku VHD przy użyciu portalu Azure](devtest-lab-create-template.md)
- [Tworzenie niestandardowego obrazu w laboratoriach devtest platformy Azure na podstawie pliku VHD przy użyciu programu PowerShell](devtest-lab-create-custom-image-from-vhd-using-powershell.md)

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
