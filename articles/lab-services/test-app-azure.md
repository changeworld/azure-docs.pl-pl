---
title: Jak testować swoją aplikację na platformie Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak utworzyć udział plików w laboratorium i zainstalować go na komputerze lokalnym i maszynę wirtualną w środowisku laboratoryjnym, a następnie wdrażanie aplikacji sieci web i pulpitu do udziału plików i Testuj je.
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
ms.sourcegitcommit: 4c2b9bc9cc704652cc77f33a870c4ec2d0579451
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/17/2019
ms.locfileid: "65872419"
---
# <a name="test-your-app-in-azure"></a>Przetestuj swoją aplikację na platformie Azure 
Ten artykuł zawiera instrukcje testowania aplikacji na platformie Azure za pomocą usługi DevTest Labs. Najpierw należy skonfigurować udział plików w ramach laboratorium i zainstalować go jako dysk na lokalnej maszynie do programowania i maszyny Wirtualnej w laboratorium. Następnie przy użyciu programu Visual Studio 2019 r do wdrożenia aplikacji w udziale plików, tak aby aplikację można uruchomić na maszynie Wirtualnej w laboratorium.  

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Wymagania wstępne 
1. [Utwórz subskrypcję platformy Azure](https://azure.microsoft.com/free/) Jeśli nie już masz i zaloguj się do [witryny Azure portal](https://portal.azure.com).
2. Postępuj zgodnie z instrukcjami wyświetlanymi w [w tym artykule](devtest-lab-create-lab.md) do tworzenia laboratorium przy użyciu usługi Azure DevTest Labs. Przypiąć laboratorium do pulpitu nawigacyjnego, można łatwo znaleźć ją podczas następnego logowania. Usługa Azure DevTest Labs umożliwia szybkie tworzenie zasobów na platformie Azure, minimalizując równocześnie straty i kontrolując koszty. Aby dowiedzieć się więcej o usłudze DevTest Labs, zobacz [Przegląd](devtest-lab-overview.md). 
3. Utwórz konto usługi Azure Storage w grupie zasobów laboratorium, wykonując instrukcje przedstawione w [Tworzenie konta magazynu](../storage/common/storage-create-storage-account.md) artykułu. Na **Tworzenie konta magazynu** wybierz opcję **Użyj istniejącej** dla **grupy zasobów**i wybierz **grupy zasobów laboratorium**. 
4. Utwórz udział plików w usłudze Azure storage, wykonując instrukcje przedstawione w [Utwórz udział plików w usłudze Azure Files](../storage/files/storage-how-to-create-file-share.md) artykułu. 

## <a name="mount-the-file-share-on-your-local-machine"></a>Instalowanie udziału plików na komputerze lokalnym
1. Na komputerze lokalnym, użyj skryptu z [poświadczenia udziału plików Azure utrwalanie w Windows](../storage/files/storage-how-to-use-files-windows.md#persisting-azure-file-share-credentials-in-windows) części [udziału plików platformy Azure za pomocą Windows](../storage/files/storage-how-to-use-files-windows.md) artykułu. 
2. Następnie należy użyć `net use` polecenie, aby zainstalować udział plików na komputerze. Oto przykładowe polecenie: Określ nazwę usługi Azure storage i nazwa udziału plików przed uruchomieniem polecenia. 

    `net use Z: \\<YOUR AZURE STORAGE NAME>.file.core.windows.net\<YOUR FILE SHARE NAME> /persistent:yes`

## <a name="create-a-vm-in-the-lab"></a>Tworzenie maszyny wirtualnej w laboratorium
1. Na **udziału plików** wybierz opcję **grupy zasobów** w menu linków do stron nadrzędnych u góry. Zostanie wyświetlony **grupy zasobów** strony. 
    
    ![Wybierz grupę zasobów z menu nawigacji](media/test-app-in-azure/select-resource-group-bread-crump.png)
2. Na **grupy zasobów** wybierz opcję **laboratorium** utworzone w usłudze DevTest Labs.

    ![Wybieranie laboratorium](media/test-app-in-azure/select-devtest-lab-in-resource-group.png)
3. Na **laboratorium DevTest Lab** strony dla laboratorium, wybierz opcję **+ Dodaj** na pasku narzędzi. 

    ![Dodawanie przycisku do laboratorium](media/test-app-in-azure/add-button-in-lab.png)
4. Na **wybierz podstawowej** strony, wyszukaj **smalldisk**i wybierz **[smalldisk] centrum danych systemu Windows Server 2016**. 

    ![Wybierz serwer Windows małego dysku](media/test-app-in-azure/choose-small-disk-windows-server.png)
5. Na **maszyny wirtualnej** określ **nazwę maszyny wirtualnej**, **nazwy użytkownika**, **hasło**i wybierz **Create** .    
    
    ![Utwórz stronę maszyny wirtualnej](media/test-app-in-azure/create-virtual-machine-page.png)    

## <a name="mount-the-file-share-on-your-vm"></a>Instalowanie udziału plików na maszynie Wirtualnej
1. Po pomyślnym utworzeniu maszyny wirtualnej wybierz **maszyny wirtualnej** z listy.    

    ![Wybierz maszynę Wirtualną laboratorium](media/test-app-in-azure/select-lab-vm.png)
2. Wybierz **Connect** na pasku narzędzi, aby nawiązać połączenie z maszyną Wirtualną. 
3. [Zainstalowanie programu Azure PowerShell](/powershell/azure/install-az-ps).
4. Postępuj zgodnie z instrukcjami instalacji sekcji udziału plików. 

## <a name="publish-your-app-from-visual-studio"></a>Publikowanie aplikacji w programie Visual Studio
W tej sekcji możesz opublikować aplikację z programu Visual Studio do testowej maszyny Wirtualnej w chmurze.

1. Utwórz aplikację sieci web i pulpitu za pomocą programu Visual Studio 2019 r.
2. Skompiluj aplikację.
3. Aby opublikować aplikację, kliknij prawym przyciskiem myszy projekt w **Eksploratora rozwiązań**i wybierz **Publikuj**. 
4. W **Kreator publikowania**, wprowadź **dysku** który jest mapowany do udziału plików.

    **Aplikacja klasyczna:**

    ![Aplikacja klasyczna](media/test-app-in-azure/desktop-app.png)

    **Aplikacja sieci Web:**

    ![Aplikacja internetowa](media/test-app-in-azure/web-app.png)

1. Wybierz **dalej** do ukończenia przepływu pracy publikowania, a następnie wybierz pozycję **Zakończ**. Po zakończeniu kroków kreatora programu Visual Studio tworzy aplikację i publikuje go do udziału plików. 


## <a name="test-the-app-on-your-test-vm-in-the-lab"></a>Testowanie aplikacji na testowej maszyny Wirtualnej w laboratorium

1. Przejdź do strony maszyny wirtualnej dla maszyny Wirtualnej w środowisku laboratoryjnym. 
2. Wybierz **Start** na pasku narzędzi, aby uruchomić maszynę Wirtualną, jeśli w przestała stanu. Można skonfigurować zasady automatycznego uruchamiania i automatyczne zamknięcie maszyny wirtualnej uniknąć uruchamianie i zatrzymywanie każdorazowo. 
3. Wybierz przycisk **Połącz**.

    ![Strona maszyny wirtualnej](media/test-app-in-azure/virtual-machine-page.png)
4. Na maszynie wirtualnej Uruchom **Eksploratora plików**i wybierz **ten komputer** można znaleźć udziału plików.

    ![Znaleźć udziału na maszynie Wirtualnej](media/test-app-in-azure/find-share-on-vm.png)

    > [!NOTE]
    > Z jakiegokolwiek powodu, jeśli nie można odnaleźć udziału plików na maszynie wirtualnej lub na komputerze lokalnym, można ponownie zainstalować ją, uruchamiając `net use` polecenia. Możesz znaleźć `net use` na polecenia **Connect** kreatora z Twojej **udziału plików** w witrynie Azure portal.
1. Otwórz udziału plików i upewnij się, że aplikacja została wdrożona z programu Visual Studio. 

    ![Otwieranie udziału na maszynie Wirtualnej](media/test-app-in-azure/open-file-share.png)

    Teraz możesz uzyskać dostęp i przetestuj swoją aplikację w ramach testu maszyny Wirtualnej utworzonej na platformie Azure.

## <a name="next-steps"></a>Kolejne kroki
Zobacz następujące artykuły, aby dowiedzieć się, jak używać maszyny wirtualne w laboratorium. 

- [Dodawanie maszyny wirtualnej do laboratorium](devtest-lab-add-vm.md)
- [Uruchom ponownie maszynę Wirtualną laboratorium](devtest-lab-restart-vm.md)
- [Zmienianie rozmiaru maszyn wirtualnych laboratorium](devtest-lab-resize-vm.md)
