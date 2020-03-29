---
title: Jak przetestować aplikację na platformie Azure | Dokumenty firmy Microsoft
description: Dowiedz się, jak utworzyć udział plików w laboratorium i zainstalować go na komputerze lokalnym i maszynie wirtualnej w laboratorium, a następnie wdrożyć aplikacje pulpitu/sieci web w udziale plików i przetestować je.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/04/2018
ms.author: spelluru
ms.openlocfilehash: f8c57b9e1fabbd04a7d9c92484b0f52f074c2577
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "65872419"
---
# <a name="test-your-app-in-azure"></a>Testowanie aplikacji na platformie Azure 
Ten artykuł zawiera kroki testowania aplikacji na platformie Azure przy użyciu devtest labs. Najpierw należy skonfigurować udział plików w laboratorium i zainstalować go jako dysk na komputerze deweloperskim lokalnym i maszyny Wirtualnej w laboratorium. Następnie za pomocą programu Visual Studio 2019 można wdrożyć aplikację do udziału plików, dzięki czemu można uruchomić aplikację na maszynie Wirtualnej w laboratorium.  

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Wymagania wstępne 
1. [Utwórz subskrypcję platformy Azure,](https://azure.microsoft.com/free/) jeśli jeszcze jej nie masz, i zaloguj się do [witryny Azure portal](https://portal.azure.com).
2. Postępuj zgodnie z instrukcjami w [tym artykule,](devtest-lab-create-lab.md) aby utworzyć laboratorium przy użyciu laboratorium Azure DevTest Labs. Przypnij laboratorium do pulpitu nawigacyjnego, aby można było je łatwo znaleźć przy następnym logcie. Usługa Azure DevTest Labs umożliwia szybkie tworzenie zasobów na platformie Azure, minimalizując straty i kontrolując koszty. Aby dowiedzieć się więcej o Laboratoriach DevTest, zobacz [omówienie](devtest-lab-overview.md). 
3. Utwórz konto usługi Azure Storage w grupie zasobów laboratorium, postępując zgodnie z instrukcjami w artykule [Tworzenie konta magazynu.](../storage/common/storage-create-storage-account.md) Na stronie **Tworzenie konta magazynu** wybierz pozycję Użyj **istniejącej** **dla grupy zasobów**i wybierz **grupę zasobów laboratorium**. 
4. Utwórz udział plików w magazynie platformy Azure, postępując zgodnie z instrukcjami w artykule [Tworzenie udziału plików w usłudze Azure Files.](../storage/files/storage-how-to-create-file-share.md) 

## <a name="mount-the-file-share-on-your-local-machine"></a>Montowanie udziału plików na komputerze lokalnym
1. Na komputerze lokalnym użyj skryptu z [poświadczeń utrwalenia udziału plików platformy Azure w](../storage/files/storage-how-to-use-files-windows.md#persisting-azure-file-share-credentials-in-windows) sekcji System Windows Użyj udziału plików platformy Azure w artykule systemu [Windows.](../storage/files/storage-how-to-use-files-windows.md) 
2. Następnie użyj `net use` polecenia, aby zainstalować udział plików na komputerze. Oto przykładowe polecenie: Przed uruchomieniem polecenia określ nazwę magazynu platformy Azure i nazwę udziału plików. 

    `net use Z: \\<YOUR AZURE STORAGE NAME>.file.core.windows.net\<YOUR FILE SHARE NAME> /persistent:yes`

## <a name="create-a-vm-in-the-lab"></a>Tworzenie maszyny wirtualnej w laboratorium
1. Na stronie **Udostępnianie plików** wybierz **grupę zasobów** w menu nawładnia na górze. Zostanie wyświetlona strona **Grupa zasobów.** 
    
    ![Wybierz grupę zasobów z menu ikonki na grupy naliczania zasobów](media/test-app-in-azure/select-resource-group-bread-crump.png)
2. Na stronie **Grupa zasobów** wybierz **laboratorium** utworzone w DevTest Labs.

    ![Wybieranie laboratorium](media/test-app-in-azure/select-devtest-lab-in-resource-group.png)
3. Na stronie **Laboratorium DevTest** dla laboratorium wybierz pozycję **+ Dodaj** na pasku narzędzi. 

    ![Przycisk Dodaj dla laboratorium](media/test-app-in-azure/add-button-in-lab.png)
4. Na stronie **Wybieranie strony podstawowej** wyszukaj **mały dysk i**wybierz pozycję **[smalldisk] Centrum danych systemu Windows Server 2016**. 

    ![Wybieranie małego dysku serwera Windows](media/test-app-in-azure/choose-small-disk-windows-server.png)
5. Na stronie **Maszyna wirtualna** określ **nazwę maszyny wirtualnej,** **nazwę użytkownika,** **hasło**i wybierz pozycję **Utwórz**.    
    
    ![Tworzenie strony maszyny wirtualnej](media/test-app-in-azure/create-virtual-machine-page.png)    

## <a name="mount-the-file-share-on-your-vm"></a>Instalowanie udziału plików na maszynie wirtualnej
1. Po pomyślnym utworzeniu maszyny wirtualnej wybierz **maszynę wirtualną** z listy.    

    ![Wybierz maszynę wirtualną laboratorium](media/test-app-in-azure/select-lab-vm.png)
2. Wybierz **pozycję Połącz** na pasku narzędzi, aby połączyć się z maszyną wirtualną. 
3. [Zainstaluj program Azure PowerShell](/powershell/azure/install-az-ps).
4. Postępuj zgodnie z instrukcjami w sekcji Montowanie udziału plików. 

## <a name="publish-your-app-from-visual-studio"></a>Publikowanie aplikacji w programie Visual Studio
W tej sekcji można opublikować aplikację z programu Visual Studio do maszyny Wirtualnej test w chmurze.

1. Tworzenie aplikacji na pulpicie/sieci web przy użyciu programu Visual Studio 2019.
2. Skompiluj aplikację.
3. Aby opublikować aplikację, kliknij prawym przyciskiem myszy projekt w **Eksploratorze rozwiązań**i wybierz polecenie **Publikuj**. 
4. W **kreatorze publikowania**wprowadź **dysk,** który jest mapowany do udziału plików.

    **Aplikacja komputerowa:**

    ![Aplikacja klasyczna](media/test-app-in-azure/desktop-app.png)

    **Aplikacja internetowa:**

    ![Aplikacja internetowa](media/test-app-in-azure/web-app.png)

1. Wybierz **przycisk Dalej,** aby ukończyć przepływ pracy publikowania, a następnie wybierz pozycję **Zakończ**. Po zakończeniu kroków kreatora program Visual Studio tworzy aplikację i publikuje ją w udziale plików. 


## <a name="test-the-app-on-your-test-vm-in-the-lab"></a>Testowanie aplikacji na testowej maszynie wirtualnej w laboratorium

1. Przejdź do strony maszyny wirtualnej dla maszyny wirtualnej w laboratorium. 
2. Wybierz **przycisk Start** na pasku narzędzi, aby uruchomić maszynę wirtualną, jeśli jest w stanie zatrzymania. Można skonfigurować zasady automatycznego uruchamiania i automatycznego zamykania maszyny Wirtualnej, aby uniknąć uruchamiania i zatrzymywania się za każdym razem. 
3. Wybierz przycisk **Połącz**.

    ![Strona maszyny wirtualnej](media/test-app-in-azure/virtual-machine-page.png)
4. W obrębie maszyny wirtualnej uruchom **Eksploratora plików**i wybierz pozycję **Ten komputer,** aby znaleźć udział plików.

    ![Znajdowanie udziału na maszynie Wirtualnej](media/test-app-in-azure/find-share-on-vm.png)

    > [!NOTE]
    > Z jakiegokolwiek powodu, jeśli nie można znaleźć udziału plików na maszynie wirtualnej lub na `net use` komputerze lokalnym, można go ponownie zamontować, uruchamiając polecenie. Polecenie można `net use` znaleźć w Kreatorze **połączu** **udziału plików** w witrynie Azure portal.
1. Otwórz udział plików i upewnij się, że zostanie wyświetlona aplikacja wdrożona w programie Visual Studio. 

    ![Otwórz udział na maszynie Wirtualnej](media/test-app-in-azure/open-file-share.png)

    Teraz można uzyskać dostęp i przetestować aplikację w ramach testu maszyny Wirtualnej utworzonej na platformie Azure.

## <a name="next-steps"></a>Następne kroki
Zobacz poniższe artykuły, aby dowiedzieć się, jak używać maszyn wirtualnych w laboratorium. 

- [Dodawanie maszyny wirtualnej do laboratorium](devtest-lab-add-vm.md)
- [Ponowne uruchamianie maszyny wirtualnej w laboratorium](devtest-lab-restart-vm.md)
- [Ponowne rozmiary maszyny Wirtualnej w laboratorium](devtest-lab-resize-vm.md)
