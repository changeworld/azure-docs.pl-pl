---
title: Zarządzanie zasobami usługi Data Lake Storage Gen1 — Eksplorator usługi Azure Storage
description: Dowiedz się, jak uzyskiwać dostęp do danych i zasobów usługi Azure Data Storage Gen1 w Eksploratorze usługi Azure Storage i zarządzać nimi
author: jejiang
ms.service: data-lake-store
ms.topic: conceptual
ms.date: 02/05/2018
ms.author: jejiang
ms.openlocfilehash: a5117f72f933e1e48dc471a75624a8d3921f55af
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73832259"
---
# <a name="manage-azure-data-lake-storage-gen1-resources-by-using-storage-explorer"></a>Zarządzanie zasobami usługi Azure Data Lake Storage Gen1 przy użyciu Eksploratora magazynu

[Usługa Azure Data Lake Storage Gen1](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-overview) to usługa do przechowywania dużych ilości danych nieustrukturyzowanych, takich jak dane tekstowe lub binarne. Możesz uzyskać dostęp do danych z dowolnego miejsca za pośrednictwem protokołu HTTP lub HTTPS. Data Lake Storage Gen1 w Eksploratorze usługi Azure Storage umożliwia dostęp do danych i zasobów usługi Data Lake Storage Gen1 oraz zarządzanie nimi, a także innych jednostek platformy Azure, takich jak obiekty blob i kolejki. Za pomocą jednego narzędzia można teraz centralnie zarządzać różnymi jednostkami platformy Azure.

Kolejną zaletą jest to, że nie musisz mieć uprawnień subskrypcji do zarządzania danymi Data Lake Storage Gen1. W Eksploratorze magazynu można dołączyć ścieżkę magazynu usługi Data Lake Gen1 do węzła **Lokalne i Dołączone,** o ile ktoś udzieli uprawnień.

## <a name="prerequisites"></a>Wymagania wstępne

Do wykonania kroków opisanych w tym artykule potrzebne jest spełnienie następujących wymagań wstępnych:

* Subskrypcja platformy Azure. Zobacz temat [Uzyskiwanie bezpłatnej wersji próbnej platformy Azure](https://azure.microsoft.com/pricing/free-trial).
* Konto Data Lake Storage Gen1. Aby uzyskać instrukcje dotyczące tworzenia jednego z nich, zobacz Wprowadzenie do [usługi Azure Data Lake Storage Gen1](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-get-started-portal).

## <a name="install-storage-explorer"></a>Instalowanie Eksploratora usługi Storage

Zainstaluj najnowszą wersję Eksploratora usługi Azure Storage ze [strony internetowej produktu](https://azure.microsoft.com/features/storage-explorer/). Instalacja obsługuje wersje dla systemów Windows i Linux oraz komputerów Mac.

## <a name="connect-to-an-azure-subscription"></a>Łączenie się z subskrypcją platformy Azure

1. W Eksploratorze usługi Storage wybierz ikonę wtyczki po lewej stronie.

   ![Ikona wtyczki](./media/data-lake-store-in-storage-explorer/plug-in-icon.png)

1. Wybierz pozycję **Dodaj konto platformy Azure**, a następnie wybierz pozycję **Zaloguj się**.

   ![Okno dialogowe Łączenie z usługą Azure Storage](./media/data-lake-store-in-storage-explorer/connect-to-azure-subscription.png)

1. W oknie dialogowym **Zaloguj się na swoje konto** wprowadź swoje poświadczenia platformy Azure.

    ![Okno dialogowe logowania do platformy Azure](./media/data-lake-store-in-storage-explorer/sign-in.png)

1. Wybierz subskrypcję z listy, a następnie wybierz przycisk **Zastosuj**.

    ![Informacje o subskrypcji i przycisk „Zastosuj”](./media/data-lake-store-in-storage-explorer/apply-subscription.png)

    Zawartość okienka **EKSPLORATOR** zostanie zaktualizowana i pojawią się konta w wybranej subskrypcji.

    ![Lista kont](./media/data-lake-store-in-storage-explorer/account-list.png)

Pomyślnie połączono usługę Data Lake Storage Gen1 z subskrypcją platformy Azure.

## <a name="connect-to-data-lake-storage-gen1"></a>Łączenie się z gen1 magazynu usługi Data Lake

Możesz uzyskać dostęp do zasobów, które nie istnieją w Twojej subskrypcji, jeśli ktoś poda Ci identyfikator URI zasobów. Następnie można połączyć się z usługi Data Lake Storage Gen1 przy użyciu identyfikatora URI po zalogowaniu się.

1. Otwórz Eksploratora usługi Storage.
2. W okienku po lewej stronie rozwiń pozycję **Lokalny i dołączony**.
3. Kliknij prawym przyciskiem myszy pozycję **Data Lake Store**, a następnie wybierz pozycję **Połącz z usługą Data Lake Store**.

      ![Pozycja „Połącz z usługą Data Lake Store” w menu kontekstowym](./media/data-lake-store-in-storage-explorer/storageexplorer-adls-uri-attach.png)

4. Wprowadź identyfikator URI. Narzędzie przejdzie do lokalizacji wprowadzonego adresu URL.

      ![Okno dialogowe „Nawiązywanie połączenia z usługą Data Lake Store” z polem tekstowym do wprowadzania identyfikatora URI](./media/data-lake-store-in-storage-explorer/storageexplorer-adls-uri-attach-dialog.png)

      ![Wynik połączenia z data lake storage gen1](./media/data-lake-store-in-storage-explorer/storageexplorer-adls-attach-finish.png)

## <a name="view-a-data-lake-storage-gen1-accounts-contents"></a>Wyświetlanie zawartości konta Usługi Data Lake Storage Gen1

Zasoby konta Data Lake Storage Gen1 zawierają foldery i pliki.

W poniższych krokach pokazano, jak wyświetlić zawartość konta Usługi Data Lake Storage Gen1 w Eksploratorze magazynu:

1. Otwórz Eksploratora usługi Storage.
2. W lewym okienku rozwiń subskrypcję zawierającą konto Data Lake Storage Gen1, które chcesz wyświetlić.
3. Rozwiń pozycję **Data Lake Store**.
4. Kliknij prawym przyciskiem myszy węzeł konta Data Lake Storage Gen1, który chcesz wyświetlić, a następnie wybierz polecenie **Otwórz**. Możesz również kliknąć dwukrotnie konto Data Lake Storage Gen1, aby je otworzyć.

   W głównym okienku jest wyświetlana zawartość konta Usługi Data Lake Storage Gen1.

   ![Okienko główne z listą folderów](./media/data-lake-store-in-storage-explorer/storageexplorer-adls-toolbar-mainpane.png)

## <a name="manage-resources-in-data-lake-storage-gen1"></a>Zarządzanie zasobami w udziale magazynowania usługi Data Lake Gen1

Zasoby usługi Data Lake Storage Gen1 można zarządzać, wykonując następujące operacje:

* Przeglądaj zasoby Usługi Data Lake Storage Gen1 na wielu kontach Usługi Data Lake Storage Gen1.  
* Użyj ciągu połączenia, aby bezpośrednio łączyć się z magazynem danych Data Lake Gen1 i zarządzać nim.
* Wyświetlanie zasobów Data Lake Storage Gen1 udostępnionych przez inne osoby za pośrednictwem listy ACL w obszarze **Lokalne i Dołączone**.
* Wykonywanie operacji CRUD na plikach i folderach: obsługa folderów cyklicznych i plików z wyborem wielokrotnym.
* Przeciąganie, upuszczanie i dodawanie folderu w celu szybkiego dostępu do ostatnich lokalizacji. Ta operacja odzwierciedla środowisko Eksploratora plików pulpitu.
* Skopiuj i otwórz hiperłącze Data Lake Storage Gen1 w Eksploratorze magazynu za pomocą jednego kliknięcia.
* Wyświetlanie dziennika aktywności w dolnym okienku po prawej stronie w celu wyświetlenia stanu działania.
* Wyświetlanie statystyk folderów i właściwości plików.

## <a name="manage-resources-in-azure-storage-explorer"></a>Zarządzanie zasobami w Eksploratorze usługi Azure Storage

Po utworzeniu konta Data Lake Storage Gen1 można:

* Przekazywanie plików i folderów, pobieranie plików i folderów oraz otwieranie zasobów na komputerze lokalnym.
* Przypinanie do paska **Szybki dostęp**, tworzenie nowego folderu, kopiowanie adresu URL i wybieranie wszystkiego.
* Kopiowanie i wklejanie, zmiana nazwy, usuwanie, uzyskiwanie statystyk folderów i odświeżanie.

Poniżej przedstawiono sposób zarządzania zasobami w ramach konta Usługi Data Lake Storage Gen1. Wykonaj kroki dla zadania, które chcesz wykonać.

### <a name="upload-files"></a>Przekazywanie plików

1. Na pasku narzędzi okienka głównego wybierz pozycję **Przekaż**, a następnie z menu rozwijanego wybierz pozycję **Przekaż pliki**.

   ![Element menu „Przekaż pliki”](./media/data-lake-store-in-storage-explorer/storageexplorer-adls-upload-files-menu.png)

2. W oknie dialogowym **Wybieranie plików do przekazania** wybierz pliki, które chcesz przekazać.

   ![Okno dialogowe przekazywania plików](./media/data-lake-store-in-storage-explorer/storageexplorer-adls-upload-files-dialog.png)

3. Wybierz pozycję **Otwórz**, aby rozpocząć przekazywanie.

### <a name="upload-a-folder"></a>Przekazywanie folderu

1. Na pasku narzędzi okienka głównego wybierz pozycję **Przekaż**, a następnie z menu rozwijanego wybierz pozycję **Przekaż folder**.

   ![Element menu „Przekaż folder”](./media/data-lake-store-in-storage-explorer/storageexplorer-adls-upload-folder-menu.png)

2. W oknie dialogowym **Wybieranie folderu do przekazania** wybierz folder, który chcesz przekazać. Następnie kliknij pozycję **Wybierz folder**.

   ![Okno dialogowe przekazywania folderów](./media/data-lake-store-in-storage-explorer/storageexplorer-adls-upload-folder-dialog.png)

   Rozpocznie się przekazywanie.

   ![Okno dialogowe z przekazywaniem w toku](./media/data-lake-store-in-storage-explorer/storageexplorer-adls-upload-folder-drag.png)

> [!NOTE]
> Możesz bezpośrednio przeciągać foldery i pliki na komputerze lokalnym, aby rozpocząć przekazywanie.

### <a name="download-folders-or-files-to-your-local-computer"></a>Pobieranie folderów lub plików na komputer lokalny

1. Wybierz foldery lub pliki, które chcesz pobrać.
2. Na pasku narzędzi okienka głównego wybierz pozycję **Pobierz**.
3. W oknie dialogowym **Wybieranie folderu do zapisania pobranych plików** określ lokalizację i nazwę folderu.
4. Wybierz **pozycję Zapisz**.

### <a name="open-a-folder-or-file-from-your-local-computer"></a>Otwieranie folderu lub pliku z komputera lokalnego

1. Wybierz folder lub plik, który chcesz otworzyć.
2. Na pasku narzędzi okienka głównego wybierz pozycję **Otwórz**. Możesz też kliknąć prawym przyciskiem myszy wybrany folder lub plik, a następnie w menu kontekstowym wybrać pozycję **Otwórz**.

Plik zostanie pobrany i otwarty przy użyciu aplikacji skojarzonej z typem pliku źródłowego. Folder może także zostać otwarty w okienku głównym.

![Otwarty plik](./media/data-lake-store-in-storage-explorer/storageexplorer-adls-open.png)

### <a name="copy-folders-or-files-to-the-clipboard"></a>Kopiowanie folderów lub plików do schowka

1. Wybierz foldery lub pliki, które chcesz skopiować.
2. Na pasku narzędzi okienka głównego wybierz pozycję **Kopiuj**. Możesz też kliknąć prawym przyciskiem myszy wybrane foldery lub pliki, a następnie w menu kontekstowym wybrać pozycję **Kopiuj**.
3. W lewym okienku przejdź do innego konta Data Lake Storage Gen1 i kliknij je dwukrotnie, aby wyświetlić je w głównym okienku.
4. Na pasku narzędzi okienka głównego wybierz pozycję **Wklej**, aby utworzyć kopię. Możesz też wybrać pozycję **Wklej** w menu skrótów.

![Opcje kopiowania folderu](./media/data-lake-store-in-storage-explorer/storageexplorer-adls-copy-paste.png)

> [!NOTE]
> Operacje kopiowania i wklejania między typami magazynów nie są obsługiwane. Można skopiować foldery lub pliki data lake storage gen1 i wkleić je do innego konta Data Lake Storage Gen1. Nie można jednak *kopiować* folderów lub plików usługi Data Lake Storage Gen1 ani wklejać ich do magazynu obiektów Blob platformy Azure ani na odwrót.
>
> Działanie operacji kopiowania/wklejania polega na pobraniu folderów lub plików na komputer lokalny, a następnie przekazaniu ich do miejsca docelowego. Narzędzie *nie wykonuje* akcji w zapleczu. Operacja kopiowania/wklejania dużych plików trwa długo. Optymalizacja kopiowania/przenoszenia plików o wysokiej wydajności jest w toku.

### <a name="delete-folders-or-files"></a>Usuwanie folderów lub plików

1. Wybierz foldery lub pliki, które chcesz usunąć.
2. Na pasku narzędzi okienka głównego wybierz pozycję **Usuń**. Możesz też kliknąć prawym przyciskiem myszy wybrane foldery lub pliki, a następnie w menu kontekstowym wybrać pozycję **Usuń**.
3. W oknie dialogowym potwierdzenia wybierz pozycję **Tak**.

![Opcje usuwania folderu](./media/data-lake-store-in-storage-explorer/storageexplorer-adls-delete.png)

### <a name="pin-to-quick-access"></a>Przypinanie do paska Szybki dostęp

1. Wybierz folder, który chcesz przypiąć.
2. Na pasku narzędzi okienka głównego wybierz pozycję **Przypnij do paska Szybki dostęp**.

   W okienku po lewej stronie wybrany folder zostanie dodany do węzła **Szybki dostęp**.

   ![Opcje przypinania folderu do węzła „Szybki dostęp”](./media/data-lake-store-in-storage-explorer/storageexplorer-adls-quick-access.png)

Po przypięciu folderu do węzła **Szybki dostęp** można łatwo uzyskać dostęp do zasobów.

### <a name="use-deep-links"></a>Korzystanie z linków bezpośrednich

Jeśli masz adres URL, możesz wprowadzić go do ścieżki adresu w Eksploratorze plików lub przeglądarce. Następnie automatycznie zostanie uruchomiony plik Storage Explorer.exe w celu przejścia do lokalizacji adresu URL.

![Link bezpośredni w Eksploratorze plików](./media/data-lake-store-in-storage-explorer/storageexplorer-adls-deep-link.png)

## <a name="next-steps"></a>Następne kroki

* Zobacz [najnowsze informacje o wersji i wideo dotyczące Eksploratora usługi Storage](https://www.storageexplorer.com).
* Dowiedz się, jak [zarządzać usługą Azure Cosmos DB w Eksploratorze usługi Azure Storage.](https://docs.microsoft.com/azure/cosmos-db/storage-explorer)
* [Wprowadzenie do Eksploratora usługi Storage](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer).
* [Wprowadzenie do usługi Azure Data Lake Storage Gen1](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-overview).
* Obejrzyj [wideo w serwisie YouTube dotyczące korzystania z usługi Azure Cosmos DB w Eksploratorze usługi Storage Azure](https://www.youtube.com/watch?v=iNIbg1DLgWo&feature=youtu.be).
