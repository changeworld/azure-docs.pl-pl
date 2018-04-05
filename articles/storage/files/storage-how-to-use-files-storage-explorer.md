---
title: Zarządzanie udziałami plików platformy Azure za pomocą Eksploratora usługi Azure Storage
description: Dowiedz się, jak zarządzać usługą Azure Files za pomocą Eksploratora usługi Azure Storage.
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
ms.date: 02/27/2018
ms.author: wgries
ms.openlocfilehash: 0c16d3b0ef0b178f99eaafecd74eabb00cca5af9
ms.sourcegitcommit: c3d53d8901622f93efcd13a31863161019325216
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2018
---
# <a name="managing-azure-file-shares-with-azure-storage-explorer"></a>Zarządzanie udziałami plików platformy Azure za pomocą Eksploratora usługi Azure Storage 
[Azure Files](storage-files-introduction.md) to łatwy w użyciu system plików w chmurze firmy Microsoft. W tym przewodniku przedstawiono podstawowe informacje dotyczące pracy z udziałami plików platformy Azure przy użyciu [Eksploratora usługi Azure Storage](https://azure.microsoft.com/features/storage-explorer/). Eksplorator usługi Azure Storage to popularne narzędzie klienta dostępne dla systemów Windows, macOS i Linux służące do zarządzania udziałami plików platformy Azure i innymi zasobami magazynu.

Dla tego przewodnika Szybki start wymagane jest, aby Eksplorator usługi Azure Storage był zainstalowany. Jeśli musisz go zainstalować, odwiedź stronę [Eksploratora usługi Azure Storage](https://azure.microsoft.com/features/storage-explorer/), aby go pobrać.

W tym artykule omówiono sposób wykonywania następujących zadań:

> [!div class="checklist"]
> * Tworzenie grupy zasobów i konta magazynu
> * Tworzenie udziału plików platformy Azure 
> * Tworzenie katalogu
> * Przekazywanie pliku
> * Pobieranie pliku
> * Tworzenie i używanie migawki udziału

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem możesz utworzyć [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="create-a-storage-account"></a>Tworzenie konta magazynu
Eksplorator usługi Azure Storage nie ma możliwości tworzenia nowych zasobów, dlatego na potrzeby tej demonstracji utwórz konto magazynu za pomocą [witryny Azure Portal](https://portal.azure.com/). 

[!INCLUDE [storage-files-create-storage-account-portal](../../../includes/storage-files-create-storage-account-portal.md)]

## <a name="connecting-azure-storage-explorer-to-azure-resources"></a>Łączenie Eksploratora usługi Azure Storage z zasobami Azure
Podczas pierwszego uruchomienia wyświetlane jest okno **Eksplorator usługi Microsoft Azure Storage — nawiązywanie połączenia**. Eksplorator usługi Azure Storage oferuje kilka sposobów nawiązywania połączenia z kontami magazynu: 

- **Logowanie za pośrednictwem konta platformy Azure**: umożliwia logowanie się przy użyciu poświadczeń użytkownika dla danej organizacji lub konta Microsoft. 
- **Nawiązywanie połączenia z określonym kontem magazynu przy użyciu parametrów połączenia lub tokenu sygnatury dostępu współdzielonego**: parametry połączenia to specjalny ciąg zawierający nazwę konta magazynu i klucz/token SAS konta magazynu umożliwiający Eksploratorowi usługi Azure Storage bezpośredni dostęp do konta magazynu (a nie po prostu wyświetlanie wszystkich kont magazynu w ramach konta platformy Azure). Aby dowiedzieć się więcej na temat parametrów połączenia, zobacz [Konfiguracja parametrów połączenia usługi Azure Storage](../common/storage-configure-connection-string.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).
- **Nawiązywanie połączenia z konkretnym kontem magazynu przy użyciu nazwy i klucza konta magazynu**: użyj nazwy konta magazynu i klucza konta magazynu do nawiązania połączenia z usługą Azure Storage.

Na potrzeby tego przewodnika Szybki start zaloguj się za pomocą swojego konta platformy Azure. Wybierz pozycję **Dodaj konto platformy Azure**, a następnie kliknij pozycję **Zaloguj się...**. Postępuj zgodnie z monitami wyświetlanymi na ekranie, aby zalogować się do konta platformy Azure.

![Okno Eksplorator usługi Microsoft Azure Storage — nawiązywanie połączenia](./media/storage-how-to-use-files-storage-explorer/connect-to-azure-storage-1.png)

### <a name="create-a-file-share"></a>Tworzenie udziału plików
Aby utworzyć pierwszy udział plików platformy Azure w ramach konta magazynu *storageacct<random number>*:

1. Rozwiń węzeł utworzonego konta magazynu.
2. Kliknij prawym przyciskiem myszy pozycję **Udziały plików** i wybierz polecenie **Utwórz udział plików**.  
    ![Zrzut ekranu przedstawiający folder udziałów plików i menu kontekstowe w kontekście](media/storage-how-to-use-files-storage-explorer/create-file-share-1.png)

3. Wpisz *myshare* jako nazwę pliku udziału i naciśnij klawisz **Enter**.

> [!Important]  
> Nazwy udziałów muszą składać się z małych liter, cyfr i pojedynczych łączników, ale nie mogą zaczynać się od łącznika. Szczegółowe informacje o nazwach plików i udziałów plików można znaleźć w temacie [Naming and Referencing Shares, Directories, Files, and Metadata](https://docs.microsoft.com/rest/api/storageservices/Naming-and-Referencing-Shares--Directories--Files--and-Metadata) (Nazywanie i odwoływanie się do udziałów, katalogów, plików i metadanych).

Po utworzeniu udziału plików w okienku po prawej stronie zostanie otwarta karta udziału plików. 

## <a name="manipulating-the-contents-of-the-azure-file-share"></a>Manipulowanie zawartością udziału plików platformy Azure
Po utworzeniu udziału plików platformy Azure możesz zainstalować ten udział plików w systemie [Windows](storage-how-to-use-files-windows.md), [Linux](storage-how-to-use-files-linux.md) lub [macOS](storage-how-to-use-files-mac.md) za pomocą protokołu SMB. Możesz też manipulować udziałem plików platformy Azure przy użyciu witryny Azure Portal. Wszystkie żądania przekazywane za pośrednictwem witryny Azure Portal są przekazywane przy użyciu interfejsu API REST plików, co umożliwia tworzenie, modyfikowanie oraz usuwanie plików i katalogów na klientach bez dostępu do protokołu SMB.

### <a name="create-a-directory"></a>Tworzenie katalogu
Dodanie katalogu zapewnia strukturę hierarchiczną na potrzeby zarządzania udziałem plików. Możesz utworzyć wiele poziomów, ale przed utworzeniem podkatalogu musisz się upewnić, że wszystkie katalogi nadrzędne istnieją. Na przykład dla ścieżki myDirectory/mySubDirectory musisz najpierw utworzyć katalog *myDirectory*, a następnie podkatalog *mySubDirectory*. 

1. Na karcie udziału plików w górnym menu kliknij przycisk **+ Nowy folder**. Zostanie otwarta strona **Tworzenie nowego katalogu**.
    ![Zrzut ekranu przedstawiający przycisk Nowy folder w kontekście](media/storage-how-to-use-files-storage-explorer/create-directory-1.png)

2. Wpisz *myDirectory* jako nazwę, a następnie kliknij przycisk **OK**. 

Katalog *myDirectory* zostanie wyświetlony na karcie udziału plików *myshare*.

### <a name="upload-a-file"></a>Przekazywanie pliku 
Przekaż plik z komputera lokalnego do nowego katalogu w udziale plików. Możesz przekazać cały folder lub pojedynczy plik.

1. W menu u góry wybierz pozycję **Przekaż**. Ta operacja udostępnia opcję przekazania folderu lub pliku.

2. Wybierz opcję **Przekaż plik**, a następnie wybierz plik do przekazania z komputera lokalnego.

3. W polu **Przekaż do katalogu** wpisz wartość *myDirectory*, a następnie kliknij pozycję **Przekaż**. 

Po zakończeniu plik powinien być widoczny na liście na stronie **myDirectory**.

### <a name="download-a-file"></a>Pobieranie pliku
Aby pobrać kopię pliku z udziału plików, kliknij plik prawym przyciskiem myszy i wybierz polecenie **Pobierz**. Wskaż, gdzie chcesz go umieścić na komputerze lokalnym, a następnie kliknij przycisk **Zapisz**.

Postęp pobierania będzie wyświetlany w okienku **Działania** u dołu okna.

## <a name="create-and-modify-share-snapshots"></a>Tworzenie i modyfikowanie migawek udziałów
Migawka zachowuje kopię udziału plików Azure w określonym momencie w czasie. Migawki udziału plików są podobne do innych technologii, które być może już znasz, takich jak:
- [Usługa kopiowania woluminów w tle (VSS)](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/ee923636) dla systemów plików systemu Windows, takich jak NTFS i ReFS
- Migawki [Menedżera woluminów logicznych (LVM)](https://en.wikipedia.org/wiki/Logical_Volume_Manager_(Linux)#Basic_functionality) dla systemu Linux
- Migawki [Systemu plików firmy Apple (APFS)](https://developer.apple.com/library/content/documentation/FileManagement/Conceptual/APFS_Guide/Features/Features.html) dla systemu macOS 

Aby utworzyć migawkę udziału:

1. Otwórz kartę udziału plików *myshare*.
2. W menu u góry karty kliknij pozycję **Utwórz migawkę** (może być ona ukryta za pozycją **... Więcej**, w zależności od wymiarów okna Eksploratora usługi Azure Storage).  
    ![Zrzut ekranu przedstawiający przycisk tworzenia migawki w kontekście](media/storage-how-to-use-files-storage-explorer/create-share-snapshot-1.png)

### <a name="list-and-browse-share-snapshots"></a>Wyświetlanie listy i przeglądanie migawek udziałów
Po utworzeniu migawki możesz kliknąć opcję **Wyświetl migawki dla udziału plików** (może być ona ukryta za pozycją **... Więcej**, w zależności od wymiarów okna Eksploratora usługi Azure Storage), aby wyświetlić migawki dla udziału. Kliknij dwukrotnie migawkę udziału, aby ją przeglądać.

![Zrzut ekranu przedstawiający ekran przeglądania migawek](media/storage-how-to-use-files-storage-explorer/list-browse-snapshots-1.png)

### <a name="restore-from-a-share-snapshot"></a>Przywracanie na podstawie migawki udziału
Aby zademonstrować przywracanie pliku na podstawie migawki udziału, najpierw trzeba usunąć plik z dynamicznego udziału plików platformy Azure. Przejdź do folderu *myDirectory*, kliknij prawym przyciskiem myszy przekazany plik, a następnie kliknij pozycję **Usuń**. Następnie, aby przywrócić ten plik na podstawie migawki udziału:

1. Kliknij polecenie **Wyświetl migawki dla udziału plików** (może być ono ukryte za pozycją **... Więcej**, w zależności od wymiarów okna Eksploratora usługi Azure Storage).
2. Wybierz migawkę udziału z listy, a następnie kliknij dwukrotnie, aby po niej nawigować.
3. Przeglądaj migawkę do momentu, aż znajdziesz usunięty plik. Zaznacz go, a następnie kliknij polecenie **Przywróć migawkę** (może być ono ukryte za pozycją **... Więcej**, w zależności od wymiarów okna Eksploratora usługi Azure Storage). Zostanie wyświetlone ostrzeżenie, że przywrócenie pliku spowoduje zastąpienie zawartości udziału plików i nie można cofnąć tej operacji. Kliknij przycisk **OK**.
4. Ten plik powinien teraz znajdować się w swojej pierwotnej lokalizacji w udziale plików platformy Azure.

### <a name="delete-a-share-snapshot"></a>Usuwanie migawki udziału
Aby usunąć migawkę udziału, [przejdź do listy migawek udziałów](#list-and-browse-share-snapshots). Kliknij prawym przyciskiem myszy migawkę udziału, którą chcesz usunąć, i wybierz polecenie Usuń.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów
Eksplorator usługi Azure Storage nie ma możliwości usuwania zasobów, dlatego zasoby z tego przewodnika Szybki start możesz usunąć za pomocą [witryny Azure Portal](https://portal.azure.com/). 

[!INCLUDE [storage-files-clean-up-portal](../../../includes/storage-files-clean-up-portal.md)]

## <a name="next-steps"></a>Następne kroki
- [Zarządzanie udziałami plików za pomocą witryny Azure Portal](storage-how-to-use-files-portal.md)
- [Zarządzanie udziałami plików przy użyciu programu Azure PowerShell](storage-how-to-use-files-powershell.md)
- [Zarządzanie udziałami plików za pomocą interfejsu wiersza polecenia platformy Azure](storage-how-to-use-files-cli.md)
- [Planowanie wdrożenia usługi Azure Files](storage-files-planning.md)