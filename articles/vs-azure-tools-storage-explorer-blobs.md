---
title: Zarządzanie zasobami usługi Azure Blob Storage za pomocą Eksploratora usługi Storage | Dokumentacja firmy Microsoft
description: Zarządzanie kontenery obiektów Blob platformy Azure i obiektami blob za pomocą Eksploratora usługi Storage
services: storage
documentationcenter: na
author: cawaMS
manager: paulyuk
editor: ''
ms.assetid: 2f09e545-ec94-4d89-b96c-14783cc9d7a9
ms.service: storage
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/18/2016
ms.author: cawa
ms.openlocfilehash: f46467871a5ae0147b5dc60881bda4175eabac56
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60458561"
---
# <a name="manage-azure-blob-storage-resources-with-storage-explorer"></a>Zarządzanie zasobami usługi Azure Blob Storage za pomocą Eksploratora usługi Storage
## <a name="overview"></a>Omówienie
[Usługa Azure Blob Storage](storage/blobs/storage-dotnet-how-to-use-blobs.md) jest usługą służącą do przechowywania dużych ilości danych niestrukturalnych, takich jak dane tekstowe lub binarne, które są dostępne z dowolnego miejsca na świecie za pośrednictwem protokołu HTTP lub HTTPS.
Magazyn obiektów Blob może być użyty do udostępniania danych publicznie lub do przechowywania danych aplikacji prywatnie. W tym artykule dowiesz się, jak pracować z obiektu blob, kontenerów i obiektów blob za pomocą Eksploratora usługi Storage.

## <a name="prerequisites"></a>Wymagania wstępne
Do wykonania kroków opisanych w tym artykule konieczne jest wykonanie kroków znajdujących się w następujących artykułach:

* [Pobieranie i instalowanie Eksploratora usługi Storage](https://www.storageexplorer.com)
* [Nawiązać połączenie z usługą lub kontem magazynu platformy Azure](vs-azure-tools-storage-manage-with-storage-explorer.md#connect-to-a-storage-account-or-service)

## <a name="create-a-blob-container"></a>Tworzenie kontenera obiektów blob
Wszystkie obiekty BLOB muszą znajdować się w kontenerze obiektów blob, który jest po prostu powodują ustawienie logicznego grupowania obiektów blob. Konto może zawierać nieograniczoną liczbę kontenerów, a każdy kontener może przechowywać nieograniczoną liczbę obiektów blob.

Poniższe kroki ilustrują Tworzenie kontenera obiektów blob w Eksploratorze usługi Storage.

1. Otwórz Eksploratora usługi Storage.
2. W okienku po lewej stronie rozwiń konto magazynu, w którym chcesz utworzyć kontener obiektów blob.
3. Kliknij prawym przyciskiem myszy **kontenery obiektów Blob**, a następnie z menu kontekstowego — wybierz pozycję **Utwórz kontener obiektów Blob**.

   ![Menu kontekstowe — tworzenie kontenerów obiektów blob][0]
4. Pola tekstowego pojawi się pod **kontenery obiektów Blob** folderu. Wprowadź nazwę kontenera obiektów blob. Zobacz [Tworzenie kontenera i ustawianie uprawnień](storage/blobs/storage-quickstart-blobs-dotnet.md#create-the-container-and-set-permissions) informacji na temat reguł i ograniczeń dotyczących nazewnictwa kontenerów obiektów blob.

   ![Tworzenie pola tekstowego kontenery obiektów Blob][1]
5. Po zakończeniu naciśnij klawisz **Enter**, aby utworzyć kontener obiektów blob, lub klawisz **Esc**, aby anulować. Po pomyślnym utworzeniu kontener obiektów blob będzie wyświetlana w obszarze **kontenery obiektów Blob** folder dla wybranego konta magazynu.

   ![Kontener obiektów blob utworzone][2]

## <a name="view-a-blob-containers-contents"></a>Wyświetlanie zawartości kontenera obiektów blob
Kontenery obiektów blob zawierają obiekty BLOB i foldery (które mogą również zawierać obiektów blob).

Poniższe kroki ilustrują wyświetlanie zawartości kontenera obiektów blob w Eksploratorze usługi Storage:

1. Otwórz Eksploratora usługi Storage.
2. W okienku po lewej stronie rozwiń konto magazynu zawierające kontenera obiektów blob, który chcesz wyświetlić.
3. Rozwiń konto magazynu **kontenery obiektów Blob**.
4. Kliknij prawym przyciskiem myszy kontener obiektów blob, które chcesz wyświetlić, a następnie z menu kontekstowego — wybierz pozycję **Otwórz Edytor kontenera obiektów Blob**.
   Można również kliknąć dwukrotnie kontener obiektów blob, który chcesz wyświetlić.

   ![Menu kontekstowe edytora kontenera Otwórz obiektów blob][19]
5. W okienku głównym wyświetli zawartość kontenerów obiektów blob.

   ![Edytor kontenera obiektów blob][3]

## <a name="delete-a-blob-container"></a>Usuń kontener obiektów blob
Kontenery obiektów blob można łatwo tworzyć i usunięte zgodnie z potrzebami. (Aby zobaczyć, jak usunąć poszczególne obiekty BLOB, zapoznaj się z sekcją [Zarządzanie obiektami BLOB w kontenerze obiektów blob](#managing-blobs-in-a-blob-container).)

Poniższe kroki przedstawiają sposób usuwania kontenera obiektów blob w Eksploratorze usługi Storage:

1. Otwórz Eksploratora usługi Storage.
2. W okienku po lewej stronie rozwiń konto magazynu zawierające kontenera obiektów blob, który chcesz wyświetlić.
3. Rozwiń konto magazynu **kontenery obiektów Blob**.
4. Kliknij prawym przyciskiem myszy kontener obiektów blob, które chcesz usunąć, a następnie z menu kontekstowego — wybierz pozycję **Usuń**.
   Można również nacisnąć klawisz **Usuń** można usunąć kontenera aktualnie wybranego obiektu blob.

   ![Usuwanie menu kontekstowe kontenera obiektów blob][4]
5. Wybierz pozycję **Tak** w oknie dialogowym potwierdzenia.

   ![Usuwanie obiektu blob w kontenerze potwierdzenia][5]

## <a name="copy-a-blob-container"></a>Skopiuj kontener obiektów blob
Eksplorator usługi Storage umożliwia kopiowanie kontener obiektów blob do Schowka, a następnie wklej ten kontener obiektów blob do innego konta magazynu. (Aby zobaczyć, jak skopiować poszczególne obiekty BLOB, zapoznaj się z sekcją [Zarządzanie obiektami BLOB w kontenerze obiektów blob](#managing-blobs-in-a-blob-container).)

Poniższe kroki ilustrują kopiowanie kontener obiektów blob z jednego konta magazynu do innego.

1. Otwórz Eksploratora usługi Storage.
2. W okienku po lewej stronie rozwiń konto magazynu zawierające kontenera obiektów blob, który chcesz skopiować.
3. Rozwiń konto magazynu **kontenery obiektów Blob**.
4. Kliknij prawym przyciskiem myszy kontener obiektów blob, które chcesz skopiować, a z menu kontekstowego — wybierz pozycję **kontenera obiektów Blob kopiowania**.

   ![Menu kontekstowe kontenera obiektu blob kopiowania][6]
5. Kliknij prawym przyciskiem myszy żądane "docelowe" konto magazynu, do którego chcesz wkleić kontenera obiektów blob i — wybierz z menu kontekstowego - **Wklej kontenera obiektów Blob**.

   ![Menu kontekstowe kontenera obiektów blob wklejania][7]

## <a name="get-the-sas-for-a-blob-container"></a>Uzyskiwanie sygnatury dostępu współdzielonego dla kontenera obiektów blob
[Sygnatura dostępu współdzielonego (SAS, shared access signature)](storage/common/storage-dotnet-shared-access-signature-part-1.md) zapewnia delegowany dostęp do zasobów w ramach konta magazynu.
Oznacza to, że możliwe jest przyznanie klientowi ograniczonych uprawnień do obiektów w ramach konta magazynu na określony czas i z określonym zestawem uprawnień bez konieczności udostępniania kluczy dostępu do konta.

Poniższe kroki ilustrują tworzenie sygnatury dostępu Współdzielonego dla kontenera obiektów blob:

1. Otwórz Eksploratora usługi Storage.
2. W okienku po lewej stronie rozwiń konto magazynu zawierające kontenera obiektów blob, dla którego ma zostać uzyskana sygnatura dostępu Współdzielonego.
3. Rozwiń konto magazynu **kontenery obiektów Blob**.
4. Kliknij prawym przyciskiem myszy żądany blobcontainer i — wybierz z menu kontekstowego - **Uzyskaj sygnaturę dostępu współdzielonego**.

   ![Uzyskiwanie sygnatury dostępu Współdzielonego menu kontekstowe][8]
5. W oknie dialogowym **Sygnatura dostępu współdzielonego** określ zasady, daty rozpoczęcia i wygaśnięcia, strefę czasową oraz poziomy dostępu dla zasobu.

   ![Pobieranie opcji sygnatury dostępu Współdzielonego][9]
6. Po zakończeniu określania opcji sygnatury dostępu współdzielonego wybierz pozycję **Utwórz**.
7. Sekundy **sygnatura dostępu współdzielonego** okna dialogowego zostanie następnie wyświetlona zawierającego kontenera obiektów blob, a także adres URL i ciągami zapytań umożliwiającymi dostęp do zasobu magazynu.
   Wybierz pozycję **Kopiuj** obok adres URL, który chcesz skopiować do schowka.

   ![Skopiuj adresy URL sygnatury dostępu Współdzielonego][10]
8. Po zakończeniu wybierz pozycję **Zamknij**.

## <a name="manage-access-policies-for-a-blob-container"></a>Zarządzanie zasadami dostępu dla kontenera obiektów blob
Poniższe kroki ilustrują Zarządzanie (Dodawanie i usuwanie) zasadami dostępu dla kontenera obiektów blob:

1. Otwórz Eksploratora usługi Storage.
2. W okienku po lewej stronie rozwiń konto magazynu zawierające kontenera obiektów blob, którego zasadami dostępu chcesz zarządzać.
3. Rozwiń konto magazynu **kontenery obiektów Blob**.
4. Wybierz żądaną blobcontainer i — wybierz z menu kontekstowego - **Zarządzaj zasadami dostępu**.

   ![Menu kontekstowe Zarządzanie zasadami dostępu][11]
5. **Zasady dostępu** okna dialogowego zostanie wyświetlona lista wszystkich zasad dostępu, utworzonym w kontenerze wybranego obiektu blob.

   ![Opcje zasad dostępu][12]        
6. Wykonaj następujące kroki w zależności od zadania zarządzania zasadami dostępu:

   * **Dodawanie nowych zasad dostępu** — wybierz pozycję **Dodaj**. Po wygenerowaniu w oknie dialogowym **Zasady dostępu** będą wyświetlane nowo dodane zasady dostępu (przy użyciu ustawień domyślnych).
   * **Edytowanie zasad dostępu** — wprowadzić wszystkie żądane operacje edycji i wybierz **Zapisz**.
   * **Usuwanie zasad dostępu** — wybierz pozycję **Usuń** obok zasad dostępu, które chcesz usunąć.

## <a name="set-the-public-access-level-for-a-blob-container"></a>Ustaw poziom dostępu publicznego do kontenera obiektów blob
Domyślnie każdy kontener obiektów blob jest równa "Nie publicznego dostępu".

Poniższe kroki ilustrują sposób określić poziom dostępu publicznego do kontenera obiektów blob.

1. Otwórz Eksploratora usługi Storage.
2. W okienku po lewej stronie rozwiń konto magazynu zawierające kontenera obiektów blob, którego zasadami dostępu chcesz zarządzać.
3. Rozwiń konto magazynu **kontenery obiektów Blob**.
4. Wybierz żądaną blobcontainer i — wybierz z menu kontekstowego - **Ustaw poziom dostępu publicznego**.

   ![Menu kontekstowe poziom dostępu publicznego do zestawu][13]
5. W **Ustaw poziom dostępu publicznego do kontenera** okno dialogowe, określ poziom żądanego dostępu.

   ![Ustaw opcje poziom dostępu publicznego][14]
6. Wybierz przycisk **Zastosuj**.

## <a name="managing-blobs-in-a-blob-container"></a>Zarządzanie obiektami BLOB w kontenerze obiektów blob
Po utworzeniu kontenera obiektów blob można przekażesz obiekt blob do kontenera obiektów blob, pobierzesz obiekt blob do komputera lokalnego, otworzyć obiektu blob na komputerze lokalnym i wiele innych.

Poniższe kroki ilustrują Zarządzanie obiektów blob (i folderami) w kontenerze obiektów blob.

1. Otwórz Eksploratora usługi Storage.
2. W okienku po lewej stronie rozwiń konto magazynu zawierające kontenera obiektów blob, którą chcesz zarządzać.
3. Rozwiń konto magazynu **kontenery obiektów Blob**.
4. Kliknij dwukrotnie kontener obiektów blob, który chcesz wyświetlić.
5. W okienku głównym wyświetli zawartość kontenerów obiektów blob.

   ![Widok kontenera obiektów blob][3]
6. W okienku głównym wyświetli zawartość kontenerów obiektów blob.
7. Wykonaj następujące kroki w zależności od zadania, które chcesz wykonać:

   * **Przekazywanie plików do kontenera obiektów blob**

     1. Na pasku narzędzi okienka głównego wybierz pozycję **Przekaż**, a następnie z menu rozwijanego wybierz pozycję **Przekaż pliki**.

        ![Menu Przekaż pliki][15]
     2. W oknie dialogowym **Przekazywanie plików** wybierz przycisk wielokropka (**...**) po prawej stronie pola tekstowego **Pliki**, aby wybrać pliki do przekazania.

        ![Opcje plików przekazywania][16]
     3. Określ typ **typu Blob**. Zobacz [Tworzenie kontenera i ustawianie uprawnień](storage/blobs/storage-quickstart-blobs-dotnet.md#upload-blobs-to-the-container) Aby uzyskać więcej informacji.
     4. Opcjonalnie określ folder docelowy, w którym zaznaczone pliki zostaną przekazane. Jeśli folder docelowy nie istnieje, zostanie on utworzony.
     5. Wybierz pozycję **Przekaż**.
   * **Przekazywanie folderu do kontenera obiektów blob**

     1. Na pasku narzędzi okienka głównego wybierz pozycję **Przekaż**, a następnie z menu rozwijanego wybierz pozycję **Przekaż folder**.

        ![Menu Przekaż folder][17]
     2. W oknie dialogowym **Przekazywanie folderu** wybierz przycisk wielokropka (**...**) po prawej stronie pola tekstowego **Folder**, aby wybrać folder, którego zawartość chcesz przekazać.

        ![Przekaż Opcje folderów][18]
     3. Określ typ **typu Blob**. Zobacz [Tworzenie kontenera i ustawianie uprawnień](storage/blobs/storage-quickstart-blobs-dotnet.md#upload-blobs-to-the-container) Aby uzyskać więcej informacji.
     4. Opcjonalnie określ folder docelowy, do którego zawartość wybranego folderu zostanie przekazana. Jeśli folder docelowy nie istnieje, zostanie on utworzony.
     5. Wybierz pozycję **Przekaż**.
   * **Pobieranie obiektu blob na komputerze lokalnym**

     1. Wybierz obiekt blob, który chcesz pobrać.
     2. Na pasku narzędzi okienka głównego wybierz pozycję **Pobierz**.
     3. W **określ miejsce zapisania pobranych blob** okno dialogowe, określ lokalizację, w której pobrano obiekt blob i nazwę chcesz nadać mu.  
     4. Wybierz pozycję **Zapisz**.
   * **Otwórz obiekt blob na komputerze lokalnym**

     1. Wybierz obiekt blob, który chcesz otworzyć.
     2. Na pasku narzędzi okienka głównego wybierz pozycję **Otwórz**.
     3. Obiekt blob zostanie pobrany i otwarty przy użyciu aplikacji skojarzonej z typem pliku źródłowego obiektu blob.
   * **Skopiuj obiekt blob do Schowka**

     1. Wybierz obiekt blob, który chcesz skopiować.
     2. Na pasku narzędzi okienka głównego wybierz pozycję **Kopiuj**.
     3. W okienku po lewej stronie przejdź do innego kontenera obiektów blob i go dwukrotnie, aby go wyświetlić w okienku głównym.
     4. Na pasku narzędzi okienka głównego wybierz **Wklej** do utworzenia kopii obiektu blob.
   * **Usuwanie obiektu blob**

     1. Wybierz obiekt blob, który chcesz usunąć.
     2. Na pasku narzędzi okienka głównego wybierz pozycję **Usuń**.
     3. Wybierz pozycję **Tak** w oknie dialogowym potwierdzenia.

## <a name="next-steps"></a>Kolejne kroki
* Zobacz [najnowsze informacje o wersji i wideo dotyczące Eksploratora usługi Storage](https://www.storageexplorer.com).
* Dowiedz się, jak [tworzyć aplikacje przy użyciu obiektów Blob, tabel, kolejek i plików platformy Azure](https://azure.microsoft.com/documentation/services/storage/).

[0]: ./media/vs-azure-tools-storage-explorer-blobs/blob-containers-create-context-menu.png
[1]: ./media/vs-azure-tools-storage-explorer-blobs/blob-container-create.png
[2]: ./media/vs-azure-tools-storage-explorer-blobs/blob-container-create-done.png
[3]: ./media/vs-azure-tools-storage-explorer-blobs/blob-container-editor.png
[4]: ./media/vs-azure-tools-storage-explorer-blobs/blob-container-delete-context-menu.png
[5]: ./media/vs-azure-tools-storage-explorer-blobs/blob-container-delete-confirmation.png
[6]: ./media/vs-azure-tools-storage-explorer-blobs/blob-container-copy-context-menu.png
[7]: ./media/vs-azure-tools-storage-explorer-blobs/blob-containers-paste-context-menu.png
[8]: ./media/vs-azure-tools-storage-explorer-blobs/blob-container-get-sas-context-menu.png
[9]: ./media/vs-azure-tools-storage-explorer-blobs/blob-container-get-sas-options.png
[10]: ./media/vs-azure-tools-storage-explorer-blobs/blob-container-get-sas-urls.png
[11]: ./media/vs-azure-tools-storage-explorer-blobs/blob-container-manage-access-policies-context-menu.png
[12]: ./media/vs-azure-tools-storage-explorer-blobs/blob-container-manage-access-policies-options.png
[13]: ./media/vs-azure-tools-storage-explorer-blobs/blob-container-set-public-access-level-context-menu.png
[14]: ./media/vs-azure-tools-storage-explorer-blobs/blob-container-set-public-access-level-options.png
[15]: ./media/vs-azure-tools-storage-explorer-blobs/blob-upload-files-menu.png
[16]: ./media/vs-azure-tools-storage-explorer-blobs/blob-upload-files-options.png
[17]: ./media/vs-azure-tools-storage-explorer-blobs/blob-upload-folder-menu.png
[18]: ./media/vs-azure-tools-storage-explorer-blobs/blob-upload-folder-options.png
[19]: ./media/vs-azure-tools-storage-explorer-blobs/blob-container-open-editor-context-menu.png
