---
title: 'Samouczek: Migracja danych lokalnych do usługi Azure Storage za pomocą narzędzia AzCopy | Microsoft Docs'
description: W tym samouczku narzędzie AzCopy zostanie użyte do migracji lub kopiowania danych z lub do obiektu blob, tabeli i zawartości pliku. W łatwy sposób przeprowadź migrację danych z magazynu lokalnego do usługi Azure Storage.
services: storage
author: normesta
ms.service: storage
ms.topic: tutorial
ms.date: 05/14/2019
ms.author: normesta
ms.reviewer: seguler
ms.subservice: common
ms.openlocfilehash: 193c00354b6222152e26476d0b06cfb1555c207e
ms.sourcegitcommit: 45e4466eac6cfd6a30da9facd8fe6afba64f6f50
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/07/2019
ms.locfileid: "66754880"
---
#  <a name="tutorial-migrate-on-premises-data-to-cloud-storage-by-using-azcopy"></a>Samouczek: Migracja danych lokalnych do magazynu w chmurze za pomocą narzędzia AzCopy

Narzędzie AzCopy to narzędzie wiersza polecenia służące do kopiowania danych do lub z magazynu Azure Blob Storage, Azure Files lub Azure Table przy użyciu prostych poleceń. Polecenia te zostały zaprojektowane w celu uzyskania optymalnej wydajności. Za pomocą narzędzia AzCopy można kopiować dane między systemem plików i kontem magazynu lub między kontami magazynu. Narzędzia AzCopy można użyć do skopiowania danych lokalnych do konta magazynu.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Utwórz konto magazynu. 
> * Używanie narzędzia AzCopy do przekazania wszystkich danych
> * Modyfikowanie danych do celów testowych
> * Tworzenie zaplanowanego zadania lub usługi cron do identyfikowania nowych plików do przekazania

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="prerequisites"></a>Wymagania wstępne

Aby ukończyć ten samouczek, Pobierz najnowszą wersję narzędzia AzCopy. Zobacz [Rozpoczynanie pracy za pomocą narzędzia AzCopy](storage-use-azcopy-v10.md).

W systemie Windows konieczne będzie użycie polecenia [Schtasks](https://msdn.microsoft.com/library/windows/desktop/bb736357(v=vs.85).aspx), ponieważ w tym samouczku jest ono używane w celu zaplanowania zadania. Użytkownicy systemu Linux zamiast tego użyją polecenia crontab.

[!INCLUDE [storage-create-account-portal-include](../../../includes/storage-create-account-portal-include.md)]

## <a name="create-a-container"></a>Tworzenie kontenera

Pierwszym krokiem jest utworzenie kontenera, ponieważ obiekty blob należy zawsze przekazywać do kontenera. Kontenery są używane jako sposób organizowania grup obiektów blob w taki sposób, jak pliki w folderach na komputerze.

Wykonaj następujące kroki, aby utworzyć kontener:

1. Wybierz przycisk **Konta magazynu** ze strony głównej, a następnie wybierz utworzone konto magazynu.
2. Wybierz pozycję **Obiekty blob** w obszarze **Usługi**, a następnie wybierz pozycję **Kontener**.

   ![Tworzenie kontenera](media/storage-azcopy-migrate-on-premises-data/CreateContainer.png)
 
Nazwy kontenerów muszą zaczynać się literą lub cyfrą. Mogą one zawierać tylko litery, cyfry i znaki łącznika (-). Aby uzyskać dodatkowe informacje o regułach nazewnictwa kontenerów i obiektów blob, zobacz temat [Naming and Referencing Containers, Blobs, and Metadata (Nazewnictwo i odwoływanie się do kontenerów, obiektów blob i metadanych)](/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata).

## <a name="download-azcopy"></a>Pobierz narzędzia AzCopy

Pobierz plik wykonywalny V10 narzędzia AzCopy.

- [Windows](https://aka.ms/downloadazcopy-v10-windows) (zip)
- [Linux](https://aka.ms/downloadazcopy-v10-linux) (docelowy)
- [System MacOS](https://aka.ms/downloadazcopy-v10-mac) (zip)

Umieść plik narzędzia AzCopy w dowolnym miejscu na komputerze. Dodaj lokalizację pliku do zmiennej path systemu, więc, że możesz zapoznać się z tego pliku wykonywalnego z dowolnego folderu na komputerze.

## <a name="authenticate-with-azure-ad"></a>Uwierzytelnianie za pomocą usługi Azure AD

Najpierw przypisz [Współautor danych obiektu Blob magazynu](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-queue-data-contributor) roli do Twojej tożsamości. Zobacz [udzielić dostępu do obiektów blob i kolejek danych Azure przy użyciu funkcji RBAC w witrynie Azure portal](https://docs.microsoft.com/azure/storage/common/storage-auth-aad-rbac-portal).

Następnie otwórz wiersz polecenia, wpisz następujące polecenie i naciśnij klawisz ENTER.

```azcopy
azcopy login
```

To polecenie zwraca kod uwierzytelniania i adres URL witryny sieci Web. Otwórz witrynę sieci Web, należy podać kod, a następnie wybierz **dalej** przycisku.

![Tworzenie kontenera](media/storage-use-azcopy-v10/azcopy-login.png)

Zostanie wyświetlone okno logowania. W tym oknie należy zalogować się do konta platformy Azure przy użyciu poświadczeń konta platformy Azure. Po pomyślnym zalogowaniu, możesz zamknąć okno przeglądarki i rozpocząć korzystanie z narzędzia AzCopy.

## <a name="upload-contents-of-a-folder-to-blob-storage"></a>Przekazywanie zawartości folderu do magazynu obiektów blob

Za pomocą narzędzia AzCopy możesz przekazać wszystkie pliki w folderze do magazynu obiektów blob w systemie [Windows](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy) lub [Linux](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-linux). Aby przekazać wszystkie obiekty blob w folderze, wprowadź następujące polecenie narzędzia AzCopy:

```AzCopy
azcopy copy "<local-folder-path>" "https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>" --recursive=true
```

* Zastąp `<local-folder-path>` zastępczego ścieżkę do folderu, który zawiera pliki (na przykład: `C:\myFolder` lub `/mnt/myFolder`).

* Zastąp symbol zastępczy `<storage-account-name>` nazwą konta magazynu.

* Zastąp `<container-name>` zastępczego nazwę kontenera, który został utworzony.

Aby przekazać zawartość określonego katalogu do lokalizacji magazynu obiektów Blob, należy określić `--recursive` opcji. Po uruchomieniu narzędzia AzCopy przy użyciu tej opcji wszystkie podfoldery i pliki również zostaną przekazane.

## <a name="upload-modified-files-to-blob-storage"></a>Przekazywanie zmodyfikowanych plików do magazynu obiektów blob

Narzędzia AzCopy można użyć, aby przekazać pliki, w oparciu o czas ostatniej modyfikacji. 

Aby z tego skorzystać, zmodyfikuj pliki lub utwórz nowe pliki w katalogu źródłowym do celów testowych. Następnie należy użyć narzędzia AzCopy `sync` polecenia.

```AzCopy
azcopy sync "<local-folder-path>" "https://<storage-account-name>.blob.core.windows.net/<container-name>" --recursive=true
```

* Zastąp `<local-folder-path>` zastępczego ścieżkę do folderu, który zawiera pliki (na przykład: `C:\myFolder` lub `/mnt/myFolder`.

* Zastąp symbol zastępczy `<storage-account-name>` nazwą konta magazynu.

* Zastąp `<container-name>` zastępczego nazwę kontenera, który został utworzony.

Aby dowiedzieć się więcej na temat `sync` polecenia, zobacz [synchronizować pliki](storage-use-azcopy-blobs.md#synchronize-files).

## <a name="create-a-scheduled-task"></a>Tworzenie zaplanowanego zadania

Możliwe jest utworzenie zaplanowanego zadania lub zadania cron służącego do uruchamiania skryptu polecenia narzędzia AzCopy. Skrypt identyfikuje i przekazuje nowe dane lokalne do magazynu w chmurze zgodnie z określonym interwałem.

Skopiuj polecenia narzędzia AzCopy do edytora tekstu. Zaktualizuj wartości parametrów polecenia narzędzia AzCopy na odpowiednie wartości. Zapisz plik jako `script.sh` (system Linux) lub `script.bat` (system Windows) na potrzeby narzędzia AzCopy. 

W poniższych przykładach założono, że nosi nazwę folderu `myFolder`, nazwę konta magazynu jest `mystorageaccount` i Twoja nazwa kontenera jest `mycontainer`.

> [!NOTE]
> W przykładzie Linux dołącza token sygnatury dostępu Współdzielonego. Należy podać jedną w poleceniu. Bieżąca wersja programu AzCopy V10 nie obsługuje uwierzytelniania usługi Azure AD w zadania cron.

# <a name="linuxtablinux"></a>[Linux](#tab/linux)

    azcopy sync "/mnt/myfiles" "https://mystorageaccount.blob.core.windows.net/mycontainer?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-05-30T06:57:40Z&st=2019-05-29T22:57:40Z&spr=https&sig=BXHippZxxx54hQn%2F4tBY%2BE2JHGCTRv52445rtoyqgFBUo%3D" --recursive=true

# <a name="windowstabwindows"></a>[Windows](#tab/windows)

    azcopy sync "C:\myFolder" "https://mystorageaccount.blob.core.windows.net/mycontainer" --recursive=true

---

W tym samouczku polecenie [Schtasks](https://msdn.microsoft.com/library/windows/desktop/bb736357(v=vs.85).aspx) jest używane do utworzenia zaplanowanego zadania w systemie Windows. Polecenie [Crontab](http://crontab.org/) służy do tworzenia zadania cron w systemie Linux.

 Polecenie **Schtasks** umożliwia administratorowi tworzenie, usuwanie, zmienianie, uruchamianie i kończenie zaplanowanych zadań oraz wykonywanie względem nich zapytań na komputerze lokalnym lub zdalnym. Zadanie **cron** umożliwia użytkownikom systemów Linux i Unix uruchamianie poleceń lub skryptów w określonym dniu i godzinie za pomocą [wyrażeń cron](https://en.wikipedia.org/wiki/Cron#CRON_expression).

# <a name="linuxtablinux"></a>[Linux](#tab/linux)

Aby utworzyć zadanie cron w systemie Linux, wprowadź następujące polecenie z poziomu terminalu:

```bash
crontab -e
*/5 * * * * sh /path/to/script.sh
```

Określenie wyrażenia cron `*/5 * * * *` w poleceniu wskazuje, że skrypt powłoki `script.sh` powinien być uruchamiany co pięć minut. Możesz zaplanować uruchamianie skryptu codziennie, co miesiąc lub co rok o określonej godzinie. Aby dowiedzieć się więcej na temat ustawiania daty i godziny wykonywania zadań, zobacz [wyrażenia cron](https://en.wikipedia.org/wiki/Cron#CRON_expression).

# <a name="windowstabwindows"></a>[Windows](#tab/windows)

Aby utworzyć zaplanowane zadanie w systemie Windows, wpisz następujące polecenie w wierszu polecenia lub w programie PowerShell:

W tym przykładzie przyjęto założenie, skrypt znajduje się na dysku głównym komputera, że skrypt mogą znajdować się gdziekolwiek zamiaru.

```cmd
schtasks /CREATE /SC minute /MO 5 /TN "AzCopy Script" /TR C:\script.bat
```

Polecenie używa:
- Parametru `/SC` w celu określenia harmonogramu minutowego.
- Parametru `/MO` w celu określenia interwału wynoszącego 5 minut.
- Parametru `/TN` w celu określenia nazwy zadania.
- Parametru `/TR` w celu określenia ścieżki do pliku `script.bat`.

Aby dowiedzieć się więcej o tworzeniu zaplanowanego zadania w systemie Windows, zobacz [Schtasks](https://technet.microsoft.com/library/cc772785(v=ws.10).aspx#BKMK_minutes).

---

Aby sprawdzić, czy zaplanowane zadanie lub zadanie cron jest działa poprawnie, utwórz nowe pliki w katalogu `myFolder`. Poczekaj pięć minut, aby sprawdzić, czy nowe pliki zostały przekazane do konta magazynu. Przejdź do katalogu dzienników, aby wyświetlić dzienniki danych wyjściowych zaplanowanego zadania lub zadania cron.

## <a name="next-steps"></a>Kolejne kroki

Aby dowiedzieć się więcej o sposobach przenoszenia danych lokalnych do usługi Azure Storage i na odwrót, kliknij następujący link:

* [Przenoszenie danych do i z usługi Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-moving-data?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).  

Aby uzyskać więcej informacji na temat narzędzia AzCopy Zobacz dowolnego z poniższych artykułów:

* [Rozpoczynanie pracy za pomocą narzędzia AzCopy](storage-use-azcopy-v10.md)

* [Transferowanie danych za pomocą narzędzia AzCopy i blob storage](storage-use-azcopy-blobs.md)

* [Transferowanie danych za pomocą narzędzia AzCopy oraz plików magazynu](storage-use-azcopy-files.md)

* [Transferowanie danych za pomocą narzędzia AzCopy i Amazon S3 przedziałów](storage-use-azcopy-s3.md)
 
* [Konfigurowanie optymalizacji i rozwiązywanie problemów z narzędzia AzCopy](storage-use-azcopy-configure.md)
