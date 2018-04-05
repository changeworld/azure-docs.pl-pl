---
title: Zarządzanie udziałami plików platformy Azure za pomocą witryny Azure Portal
description: Dowiedz się, jak zarządzać usługą Azure Files za pomocą witryny Azure Portal.
services: storage
documentationcenter: ''
author: wmgries
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 03/26/2018
ms.author: wgries
ms.openlocfilehash: 588d260bb939c8f6439ca66828296ea455f1524a
ms.sourcegitcommit: c3d53d8901622f93efcd13a31863161019325216
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2018
---
# <a name="managing-azure-file-shares-with-the-azure-portal"></a>Zarządzanie udziałami plików platformy Azure za pomocą witryny Azure Portal 
[Azure Files](storage-files-introduction.md) to łatwy w użyciu system plików w chmurze firmy Microsoft. Udziały plików platformy Azure można instalować w systemach Windows, Linux i macOS. W tym przewodniku przedstawiono podstawowe informacje dotyczące pracy z udziałami plików platformy Azure przy użyciu witryny [Azure Portal](https://portal.azure.com/). Instrukcje:

> [!div class="checklist"]
> * Tworzenie grupy zasobów i konta magazynu
> * Tworzenie udziału plików platformy Azure 
> * Tworzenie katalogu
> * Przekazywanie pliku 
> * Pobieranie pliku
> * Tworzenie i używanie migawki udziału

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem możesz utworzyć [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="create-a-storage-account"></a>Tworzenie konta magazynu
[!INCLUDE [storage-files-create-storage-account-portal](../../../includes/storage-files-create-storage-account-portal.md)]

## <a name="create-a-file-share"></a>Tworzenie udziału plików
Aby utworzyć udział plików:

1. Wybierz konto magazynu na pulpicie nawigacyjnym.
2. Na stronie konta magazynu w sekcji **Usługi** wybierz pozycję **Pliki**.
    ![Zrzut ekranu przedstawiający sekcję usług konta magazynu; wybieranie usługi Pliki](media/storage-how-to-use-files-portal/create-file-share-1.png)

3. W menu w górnej części strony **Usługa plików** kliknij pozycję **+ Udział plików**. Zostanie rozwinięta strona **Nowy udział plików**.
4. W polu **Nazwa** wpisz ciąg *myshare*.
5. Kliknij przycisk **OK**, aby utworzyć udział plików platformy Azure.

## <a name="manipulating-the-contents-of-the-azure-file-share"></a>Manipulowanie zawartością udziału plików platformy Azure
Po utworzeniu udziału plików platformy Azure możesz zainstalować ten udział plików w systemie [Windows](storage-how-to-use-files-windows.md), [Linux](storage-how-to-use-files-linux.md) lub [macOS](storage-how-to-use-files-mac.md) za pomocą protokołu SMB. Możesz też manipulować udziałem plików platformy Azure przy użyciu witryny Azure Portal. Wszystkie żądania przekazywane za pośrednictwem witryny Azure Portal są przekazywane przy użyciu interfejsu API REST plików, co umożliwia tworzenie, modyfikowanie oraz usuwanie plików i katalogów na klientach bez dostępu do protokołu SMB.

### <a name="create-directory"></a>Tworzenie katalogu
Aby utworzyć nowy katalog o nazwie *myDirectory* w katalogu głównym udziału plików platformy Azure:

1. Na stronie **Usługa plików** wybierz udział plików **myshare**. Zostanie otwarta strona udziału plików.
2. W menu w górnej części strony wybierz pozycję **+ Dodaj katalog**. Zostanie rozwinięta strona **Nowy katalog**.
3. Wpisz nazwę *myDirectory*, a następnie kliknij przycisk **OK**.

### <a name="upload-a-file"></a>Przekazywanie pliku 
Aby można było zademonstrować przekazywanie pliku, najpierw musisz utworzyć lub wybrać plik do przekazania. Możesz to zrobić w dowolny sposób. Po wybraniu pliku do przekazania:

1. Kliknij katalog **myDirectory**. Zostanie otwarty panel **myDirectory**.
2. W menu u góry kliknij pozycję **Przekaż**. Zostanie otwarty panel **Przekazywanie plików**.  
    ![Zrzut ekranu panelu przekazywania plików](media/storage-how-to-use-files-portal/upload-file-1.png)

3. Kliknij ikonę folderu, aby otworzyć okno w celu przeglądania plików lokalnych. 
4. Wybierz plik, a następnie kliknij pozycję **Otwórz**. 
5. Na stronie **Przekazywanie plików** sprawdź nazwę pliku, a następnie kliknij pozycję **Przekaż**.
6. Po zakończeniu plik powinien być widoczny na liście na stronie **myDirectory**.

### <a name="download-a-file"></a>Pobieranie pliku
Aby pobrać kopię przekazanego pliku, kliknij prawym przyciskiem myszy ten plik. Szczegóły środowiska wyświetlanego po kliknięciu przycisku pobierania będą zależeć od używanego systemu operacyjnego i używanej przeglądarki.

## <a name="create-and-modify-share-snapshots"></a>Tworzenie i modyfikowanie migawek udziałów
Jedną z dodatkowych przydatnych czynności, które można wykonywać na udziałach plików platformy Azure, jest tworzenie migawek udziałów. Migawka zachowuje określony moment w czasie dla udziału plików platformy Azure. Migawki udziałów są podobne do technologii systemów operacyjnych, które być może już znasz, takich jak:
- [Usługa kopiowania woluminów w tle (VSS)](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/ee923636) dla systemów plików systemu Windows, takich jak NTFS i ReFS
- Migawki [Menedżera woluminów logicznych (LVM)](https://en.wikipedia.org/wiki/Logical_Volume_Manager_(Linux)#Basic_functionality) dla systemu Linux
- Migawki [Systemu plików firmy Apple (APFS)](https://developer.apple.com/library/content/documentation/FileManagement/Conceptual/APFS_Guide/Features/Features.html) dla systemu macOS 

Aby utworzyć migawkę udziału:

1. Otwórz stronę udziału plików, otwierając konto magazynu z poziomu pulpitu nawigacyjnego i wybierając pozycję **Pliki** > **myshare**. 
2. Na stronie udziału plików kliknij przycisk **Migawka** w menu w górnej części strony, a następnie wybierz polecenie **Utwórz migawkę**.  
    ![Zrzut ekranu pokazujący, jak znaleźć przycisk tworzenia migawki](media/storage-how-to-use-files-portal/create-snapshot-1.png)

### <a name="list-and-browse-share-snapshots"></a>Wyświetlanie listy i przeglądanie migawek udziałów
Po utworzeniu migawki możesz ponownie kliknąć pozycję **Migawka** i wybrać pozycję **Wyświetl migawki**, aby utworzyć listę migawek dla danego udziału. Wyświetlone okienko będzie zawierać migawki dla określonego udziału. Kliknij migawkę udziału, aby ją przejrzeć.

### <a name="restore-from-a-share-snapshot"></a>Przywracanie na podstawie migawki udziału
Aby zademonstrować przywracanie pliku na podstawie migawki udziału, najpierw trzeba usunąć plik z dynamicznego udziału plików platformy Azure. Przejdź do folderu *myDirectory*, kliknij prawym przyciskiem myszy przekazany plik, a następnie kliknij pozycję **Usuń**. Następnie, aby przywrócić ten plik na podstawie migawki udziału:

1. Kliknij pozycję **Migawki** w menu u góry i wybierz polecenie **Wyświetl migawki**. 
2. Kliknij utworzoną wcześniej migawkę. Jej zawartość zostanie otworzona na nowej stronie. 
3. Kliknij pozycję **myDirectory** w migawce — powinien zostać wyświetlony usunięty plik. 
4. Kliknij prawym przyciskiem myszy usunięty plik i wybierz polecenie **Przywróć**.
5. Zostanie wyświetlone wyskakujące okienko, umożliwiające wybór między opcją przywrócenia pliku jako kopii a opcją zastąpienia oryginalnego pliku. Ponieważ oryginalny plik został usunięty, możesz wybrać opcję **Zastąp oryginalny plik**, aby przywrócić plik do stanu, w jakim był, zanim został usunięty. Kliknij przycisk **OK**, aby przywrócić plik w udziale plików platformy Azure.  
    ![Zrzut ekranu przedstawiający okno dialogowe przywracania pliku](media/storage-how-to-use-files-portal/restore-snapshot-1.png)

6. Po zakończeniu przywracania pliku zamknij stronę migawki i wróć do strony **myshare** > **myDirectory**. Plik powinien znajdować się na swoim oryginalnym miejscu.

### <a name="delete-a-share-snapshot"></a>Usuwanie migawki udziału
Aby usunąć migawkę udziału, [przejdź do listy migawek udziałów](#list-and-browse-a-share-snapshot). Kliknij pole wyboru obok nazwy odpowiedniej migawki udziału, a następnie wybierz przycisk **Usuń**.

![Zrzut ekranu przedstawiający usuwanie migawki udziału](media/storage-how-to-use-files-portal/delete-snapshot-1.png)

## <a name="clean-up-resources"></a>Oczyszczanie zasobów
[!INCLUDE [storage-files-clean-up-portal](../../../includes/storage-files-clean-up-portal.md)]

## <a name="next-steps"></a>Następne kroki
- [Zarządzanie udziałami plików przy użyciu programu Azure PowerShell](storage-how-to-use-files-powershell.md)
- [Zarządzanie udziałami plików za pomocą interfejsu wiersza polecenia platformy Azure](storage-how-to-use-files-cli.md)
- [Zarządzanie udziałami plików za pomocą Eksploratora usługi Azure Storage](storage-how-to-use-files-storage-explorer.md)
- [Planowanie wdrożenia usługi Azure Files](storage-files-planning.md)