---
title: Zarządzanie udziałami plików platformy Azure za pomocą Eksploratora usługi Azure Storage
description: Dowiedz się, jak zarządzać usługą Azure Files za pomocą Eksploratora usługi Azure Storage.
services: storage
author: wmgries
ms.service: storage
ms.topic: get-started-article
ms.date: 02/27/2018
ms.author: wgries
ms.component: files
ms.openlocfilehash: 949d96bb1b5ffdc948737d4a47ffa14b2e344b5e
ms.sourcegitcommit: e2ea404126bdd990570b4417794d63367a417856
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/14/2018
ms.locfileid: "45574735"
---
# <a name="manage-azure-file-shares-with-azure-storage-explorer"></a>Zarządzanie udziałami plików platformy Azure za pomocą Eksploratora usługi Azure Storage 
Usługa [Azure Files](storage-files-introduction.md) to łatwy w użyciu system plików w chmurze firmy Microsoft. W tym przewodniku przedstawiono podstawowe informacje dotyczące pracy z udziałami plików platformy Azure przy użyciu [Eksploratora usługi Azure Storage](https://azure.microsoft.com/features/storage-explorer/). Eksplorator usługi Storage to popularne narzędzie klienckie dostępne dla systemów Windows, macOS i Linux. Eksplorator usługi Storage służy do zarządzania udziałami plików platformy Azure i innymi zasobami magazynu.

Ten przewodnik Szybki start wymaga zainstalowanego Eksploratora usługi Azure Storage. Aby pobrać i zainstalować go, przejdź na stronę [Eksploratora usługi Storage Azure](https://azure.microsoft.com/features/storage-explorer/).

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
Nie możesz używać Eksploratora usługi Storage do tworzenia nowych zasobów. Na potrzeby tego pokazu utwórz konto magazynu w [witrynie Azure Portal](https://portal.azure.com/). 

[!INCLUDE [storage-files-create-storage-account-portal](../../../includes/storage-files-create-storage-account-portal.md)]

## <a name="connect-storage-explorer-to-azure-resources"></a>Łączenie Eksploratora usługi Storage z zasobami platformy Azure
Przy pierwszym uruchomieniu Eksploratora usługi Storage jest wyświetlane okno **Eksplorator usługi Microsoft Azure Storage — nawiązywanie połączenia**. Eksplorator usługi Storage oferuje kilka sposobów nawiązywania połączenia z kontami magazynu: 

- **Logowanie za pomocą konta platformy Azure**: możesz zalogować się przy użyciu poświadczeń użytkownika organizacji lub konta Microsoft. 
- **Łączenie z określonym kontem magazynu za pomocą parametrów połączenia lub tokenu sygnatury dostępu współdzielonego**: parametry połączenia to specjalny ciąg zawierający nazwę konta magazynu oraz klucz konta magazynu lub token sygnatury dostępu współdzielonego. Dzięki tokenowi Eksplorator usługi Storage uzyskuje dostęp do konta magazynu bezpośrednio (zamiast wyświetlania wszystkich kont magazynu na koncie platformy Azure). Aby dowiedzieć się więcej na temat parametrów połączenia, zobacz [Konfiguracja parametrów połączenia usługi Azure Storage](../common/storage-configure-connection-string.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).
- **Nawiązywanie połączenia z konkretnym kontem magazynu za pomocą nazwy i klucza konta magazynu**: użyj nazwy i klucza konta magazynu do nawiązania połączenia z usługą Azure Storage.

Na potrzeby tego przewodnika Szybki start zaloguj się przy użyciu konta platformy Azure. Wybierz pozycję **Dodaj konto platformy Azure**, a następnie wybierz pozycję **Zaloguj się**. Postępuj zgodnie z monitami, aby zalogować się na koncie platformy Azure.

![Zrzut ekranu okna Eksplorator usługi Microsoft Azure Storage — nawiązywanie połączenia](./media/storage-how-to-use-files-storage-explorer/connect-to-azure-storage-1.png)

### <a name="create-a-file-share"></a>Tworzenie udziału plików
Aby utworzyć Twój pierwszy udział plików platformy Azure w ramach konta magazynu *storageacct<random number>*:

1. Rozwiń element utworzonego konta magazynu.
2. Kliknij prawym przyciskiem myszy pozycję **Udziały plików**, a następnie wybierz polecenie **Utwórz udział plików**.  
    ![Zrzut ekranu przedstawiający folder udziałów plików i menu kontekstowe w kontekście](media/storage-how-to-use-files-storage-explorer/create-file-share-1.png)

3. Dla udziału plików podaj wartość *myshare*, a następnie naciśnij klawisz Enter.

> [!IMPORTANT]  
> Nazwy udziałów mogą zawierać tylko małe litery, cyfry i pojedyncze łączniki (ale nie mogą zaczynać się łącznikiem). Szczegółowe informacje o nazwach plików i udziałów plików można znaleźć w temacie [Naming and referencing shares, directories, files, and metadata (Nazywanie i przywoływanie udziałów, katalogów, plików i metadanych)](https://docs.microsoft.com/rest/api/storageservices/Naming-and-Referencing-Shares--Directories--Files--and-Metadata).

Po utworzeniu udziału plików w okienku po prawej zostanie otwarta karta dla udziału plików. 

## <a name="work-with-the-contents-of-an-azure-file-share"></a>Praca z zawartością udziału plików platformy Azure
Po utworzeniu udziału plików platformy Azure możesz zainstalować ten udział plików w systemie [Windows](storage-how-to-use-files-windows.md), [Linux](storage-how-to-use-files-linux.md) lub [macOS](storage-how-to-use-files-mac.md) za pomocą protokołu SMB. Z udziałem plików platformy Azure możesz też pracować przy użyciu interfejsu wiersza polecenia platformy Azure. Zaletą korzystania z interfejsu wiersza polecenia platformy Azure w porównaniu z instalowaniem udziału plików przy użyciu protokołu SMB jest to, że wszystkie żądania wprowadzone w ramach interfejsu wiersza polecenia platformy Azure są wykonywane za pomocą interfejsu API REST usługi Files. Interfejs API REST usługi Files służy do tworzenia, modyfikowania i usuwania plików i katalogów na klientach bez dostępu przez protokół SMB.

### <a name="create-a-directory"></a>Tworzenie katalogu
Dodanie katalogu zapewnia strukturę hierarchiczną na potrzeby zarządzania udziałem plików. W katalogu możesz utworzyć wiele poziomów. Jednak przed utworzeniem podkatalogów musisz upewnić się, że katalogi nadrzędne istnieją. Na przykład w przypadku ścieżki myDirectory/mySubDirectory należy najpierw utworzyć katalog *myDirectory*. Dopiero wtedy można utworzyć katalog *mySubDirectory*. 

1. Na karcie udziału plików w górnym menu wybierz przycisk **Nowy folder**. Zostanie otwarte okienko **Tworzenie nowego katalogu**.
    ![Zrzut ekranu przedstawiający przycisk Nowy folder w kontekście](media/storage-how-to-use-files-storage-explorer/create-directory-1.png)

2. Podaj nazwę katalogu — wpisz ciąg *myDirectory*, a następnie wybierz pozycję **OK**. 

Katalog *myDirectory* zostanie wyświetlony na karcie udziału plików *myshare*.

### <a name="upload-a-file"></a>Przekazywanie pliku 
Istnieje możliwość przekazania pliku z maszyny lokalnej do nowego katalogu w udziale plików. Przekazywanie może obejmować cały folder lub pojedynczy plik.

1. Wybierz z górnego menu polecenie **Przekaż**. Ta operacja udostępnia opcję przekazania folderu lub pliku.
2. Wybierz pozycję **Przekaż plik**, a następnie wybierz plik do przekazania z maszyny lokalnej.
3. W polu **Przekaż do katalogu** wpisz ciąg *myDirectory*, a następnie wybierz pozycję **Przekaż**. 

Gdy skończysz, plik zostanie wyświetlony na liście w okienku *myDirectory*.

### <a name="download-a-file"></a>Pobieranie pliku
Aby pobrać kopię pliku z udziału plików, kliknij prawym przyciskiem myszy plik, a następnie wybierz polecenie **Pobierz**. Wybierz, gdzie chcesz umieścić plik na maszynie lokalnej, a następnie wybierz polecenie **Zapisz**.

Postęp pobierania będzie wyświetlany w okienku **Działania** na dole okna.

## <a name="create-and-modify-share-snapshots"></a>Tworzenie i modyfikowanie migawek udziałów
Migawka zachowuje kopię udziału plików Azure w określonym momencie w czasie. Migawki udziału plików są podobne do innych technologii, które być może już znasz:
- [Usługa kopiowania woluminów w tle (VSS)](https://docs.microsoft.com/windows/desktop/VSS/volume-shadow-copy-service-portal) dla systemów plików systemu Windows, takich jak NTFS i ReFS
- Migawki [Menedżera woluminów logicznych (LVM)](https://en.wikipedia.org/wiki/Logical_Volume_Manager_(Linux)#Basic_functionality) dla systemu Linux
- Migawki [systemu plików firmy Apple (APFS)](https://developer.apple.com/library/content/documentation/FileManagement/Conceptual/APFS_Guide/Features/Features.html) dla systemu macOS

Aby utworzyć migawkę udziału:

1. Wybierz kartę udziału plików *myshare*.
2. Wybierz pozycję **Utwórz migawkę** z górnego menu. (Wyświetlenie tej opcji może wymagać wcześniejszego wybrania pozycji **Więcej** w zależności od wymiarów okna Eksploratora usługi Storage).  
    ![Zrzut ekranu przycisku Utwórz migawkę w kontekście](media/storage-how-to-use-files-storage-explorer/create-share-snapshot-1.png)

### <a name="list-and-browse-share-snapshots"></a>Wyświetlanie listy i przeglądanie migawek udziałów
Aby wyświetlić listę migawek udziału po utworzeniu migawki, wybierz pozycję **Wyświetl migawki udziału plików**. (Wyświetlenie tej opcji może wymagać wcześniejszego wybrania pozycji **Więcej** w zależności od wymiarów okna Eksploratora usługi Storage). Aby przeglądać migawkę udziału, kliknij ją dwukrotnie.

![Zrzut ekranu okna przeglądania migawek](media/storage-how-to-use-files-storage-explorer/list-browse-snapshots-1.png)

### <a name="restore-from-a-share-snapshot"></a>Przywracanie na podstawie migawki udziału
Aby przedstawić sposób przywracania pliku na podstawie migawki udziału, najpierw musisz usunąć plik z działającego udziału plików platformy Azure. Przejdź do folderu *myDirectory*, kliknij prawym przyciskiem myszy przekazany plik, a następnie wybierz pozycję **Usuń**. Aby przywrócić ten plik z migawki udziału:

1. Wybierz pozycję **Wyświetl migawki udziału plików**. (Wyświetlenie tej opcji może wymagać wcześniejszego wybrania pozycji **Więcej** w zależności od wymiarów okna Eksploratora usługi Storage).
2. Na liście migawek udziału kliknij dwukrotnie migawkę udziału.
3. Przeglądaj migawkę do momentu znalezienia usuniętego pliku. Wybierz udział plików, a następnie wybierz pozycję **Przywróć migawkę**. (Wyświetlenie tej opcji może wymagać wcześniejszego wybrania pozycji **Więcej** w zależności od wymiarów okna Eksploratora usługi Storage). Zostanie otwarte okno z ostrzeżeniem informującym o tym, że przywrócenie pliku spowoduje zastąpienie zawartości udziału plików i że nie można cofnąć tej operacji. Kliknij przycisk **OK**.
4. Plik teraz powinien znajdować się w swojej oryginalnej lokalizacji w udziale plików platformy Azure.

### <a name="delete-a-share-snapshot"></a>Usuwanie migawki udziału
Aby usunąć migawkę udziału, przejdź do [listy migawek udziału](#list-and-browse-share-snapshots). Kliknij migawkę udziału do usunięcia prawym przyciskiem myszy, a następnie wybierz polecenie **Usuń**.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów
Nie możesz użyć Eksploratora usługi Storage do usunięcia zasobów. Aby wyczyścić wyniki działań wykonanych w ramach tego przewodnika Szybki start, użyj [witryny Azure Portal](https://portal.azure.com/). 

[!INCLUDE [storage-files-clean-up-portal](../../../includes/storage-files-clean-up-portal.md)]

## <a name="next-steps"></a>Następne kroki
- [Zarządzanie udziałami plików za pomocą witryny Azure Portal](storage-how-to-use-files-portal.md)
- [Zarządzanie udziałami plików przy użyciu programu Azure PowerShell](storage-how-to-use-files-powershell.md)
- [Zarządzanie udziałami plików za pomocą interfejsu wiersza polecenia platformy Azure](storage-how-to-use-files-cli.md)
- [Planowanie wdrożenia usługi Azure Files](storage-files-planning.md)