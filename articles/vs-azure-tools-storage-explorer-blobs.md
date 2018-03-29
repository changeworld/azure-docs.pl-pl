---
title: Zarządzanie zasobami magazynu obiektów Blob Azure za pomocą Eksploratora usługi Storage (wersja zapoznawcza) | Dokumentacja firmy Microsoft
description: Zarządzanie kontenerów obiektów Blob platformy Azure i obiektów blob z Eksploratora usługi Storage (wersja zapoznawcza)
services: storage
documentationcenter: na
author: cawa
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
ms.openlocfilehash: 98a1016f78587b4139041f140634b98fa47edfd1
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2018
---
# <a name="manage-azure-blob-storage-resources-with-storage-explorer-preview"></a>Zarządzanie zasobami magazynu obiektów Blob Azure za pomocą Eksploratora usługi Storage (wersja zapoznawcza)
## <a name="overview"></a>Przegląd
[Magazyn obiektów Blob Azure](storage/blobs/storage-dotnet-how-to-use-blobs.md) to usługa do przechowywania dużych ilości danych bez struktury, takich jak dane tekstowe lub binarne, którego mogą uzyskać dostęp z dowolnego miejsca na świecie za pośrednictwem protokołu HTTP lub HTTPS.
Magazyn obiektów Blob może być użyty do udostępniania danych publicznie lub do przechowywania danych aplikacji prywatnie. W tym artykule dowiesz się, jak używać Eksploratora usługi Storage (wersja zapoznawcza) do pracy z kontenerów obiektów blob i obiektów blob.

## <a name="prerequisites"></a>Wymagania wstępne
Do wykonania kroków opisanych w tym artykule konieczne jest wykonanie kroków znajdujących się w następujących artykułach:

* [Pobieranie i instalowanie Eksploratora usługi Storage (wersja zapoznawcza)](http://www.storageexplorer.com)
* [Łączenie się z usługą lub kontem magazynu platformy Azure](vs-azure-tools-storage-manage-with-storage-explorer.md#connect-to-a-storage-account-or-service)

## <a name="create-a-blob-container"></a>Tworzenie kontenera obiektów blob
Wszystkie obiekty BLOB muszą znajdować się w kontenerze obiektów blob to po prostu logiczne grupowanie obiektów blob. Konto może zawierać nieograniczoną liczbę kontenerów, a każdy kontener może przechowywać nieograniczoną liczbę obiektów blob.

Poniższe kroki przedstawiają sposób tworzenia kontenera obiektów blob w ramach Eksploratora usługi Storage (wersja zapoznawcza).

1. Otwórz Eksploratora usługi Storage (wersja zapoznawcza).
2. W okienku po lewej stronie rozwiń konto magazynu, w którym chcesz utworzyć kontener obiektów blob.
3. Kliknij prawym przyciskiem myszy **kontenerów obiektów Blob**i z menu kontekstowego — wybierz pozycję **Tworzenie kontenera obiektów Blob**.

   ![Tworzenie menu kontekstowe kontenerów obiektów blob][0]
4. Pole tekstowe pojawi się pod **kontenerów obiektów Blob** folderu. Wprowadź nazwę kontenera obiektów blob. Zobacz [utworzyć kontener i ustawić uprawnienia](storage/blobs/storage-quickstart-blobs-dotnet.md#create-the-container-and-set-permissions) informacji na temat reguły i ograniczenia dotyczące nazewnictwa kontenerów obiektów blob.

   ![Tworzenie pola tekstowego kontenerów obiektów Blob][1]
5. Naciśnij klawisz **Enter** po zakończeniu, aby utworzyć kontener obiektów blob lub **Esc** Aby anulować. Po pomyślnym utworzeniu kontenera obiektów blob będzie wyświetlana w obszarze **kontenerów obiektów Blob** folder dla wybranego konta magazynu.

   ![Utworzyć kontener obiektów blob][2]

## <a name="view-a-blob-containers-contents"></a>Wyświetlanie zawartości kontenera obiektów blob
Kontenerów obiektów blob zawiera obiekty BLOB i folderów (które może również zawierać obiektów blob).

Następujące kroki ilustrują sposób wyświetlania zawartości kontener obiektów blob w ramach Eksploratora usługi Storage (wersja zapoznawcza):

1. Otwórz Eksploratora usługi Storage (wersja zapoznawcza).
2. W okienku po lewej stronie rozwiń konto magazynu zawierające kontenera obiektów blob czy chcesz wyświetlić.
3. Rozwiń konto magazynu **kontenerów obiektów Blob**.
4. Kliknij prawym przyciskiem myszy kontener obiektów blob chcesz wyświetlić i z menu kontekstowego — wybierz pozycję **Otwórz Edytor kontenera obiektów Blob**.
   Możesz również kliknąć dwukrotnie kontenera obiektów blob, który chcesz wyświetlić.

   ![Menu kontekstowe edytora kontenera obiektów blob Otwórz][19]
5. W okienku głównym zostaną wyświetlone zawartości kontenera obiektów blob.

   ![Edytor kontenera obiektów blob][3]

## <a name="delete-a-blob-container"></a>Usunięcie kontenera obiektów blob
Kontenerów obiektów blob można łatwo tworzyć i usunięte zgodnie z potrzebami. (Aby zobaczyć, jak usunąć poszczególne obiekty BLOB, zapoznaj się z sekcją [Zarządzanie obiekty BLOB w kontenerze obiektów blob](#managing-blobs-in-a-blob-container).)

Następujące kroki ilustrują sposób usuwania kontenera obiektów blob w ramach Eksploratora usługi Storage (wersja zapoznawcza):

1. Otwórz Eksploratora usługi Storage (wersja zapoznawcza).
2. W okienku po lewej stronie rozwiń konto magazynu zawierające kontenera obiektów blob czy chcesz wyświetlić.
3. Rozwiń konto magazynu **kontenerów obiektów Blob**.
4. Kliknij prawym przyciskiem myszy kontener obiektów blob chcesz usunąć i z menu kontekstowego — wybierz pozycję **usunąć**.
   Można również nacisnąć **usunąć** można usunąć kontenera aktualnie zaznaczonego obiektu blob.

   ![Usuwanie menu kontekstowe kontenera obiektów blob][4]
5. Wybierz pozycję **Tak** w oknie dialogowym potwierdzenia.

   ![Usuwanie obiektów blob kontenera potwierdzenia][5]

## <a name="copy-a-blob-container"></a>Skopiuj kontenera obiektów blob
Eksplorator usługi Storage (wersja zapoznawcza) umożliwia kopiowanie kontenera obiektów blob do Schowka, a następnie wklej kontenera obiektów blob na innym koncie magazynu. (Aby zobaczyć, jak skopiować poszczególne obiekty BLOB, zapoznaj się z sekcją [Zarządzanie obiekty BLOB w kontenerze obiektów blob](#managing-blobs-in-a-blob-container).)

Następujące kroki ilustrują sposób kopiowania kontenera obiektów blob z konta magazynu jednego do drugiego.

1. Otwórz Eksploratora usługi Storage (wersja zapoznawcza).
2. W okienku po lewej stronie rozwiń konto magazynu zawierające kontenera obiektów blob czy chcesz skopiować.
3. Rozwiń konto magazynu **kontenerów obiektów Blob**.
4. Kliknij prawym przyciskiem myszy kontener obiektów blob chcesz skopiować i — wybierz z menu kontekstowego - **kontenera obiektów Blob kopii**.

   ![Kopiowanie menu kontekstowe kontenera obiektów blob][6]
5. Kliknij prawym przyciskiem myszy konto magazynu "wartość", do którego chcesz wkleić kontenera obiektów blob i z menu kontekstowego — wybierz pozycję **Wklej kontenera obiektów Blob**.

   ![Menu kontekstowe kontenera obiektów blob wklejania][7]

## <a name="get-the-sas-for-a-blob-container"></a>Uzyskiwanie sygnatury dostępu współdzielonego dla kontenera obiektów blob
[Sygnatura dostępu współdzielonego (SAS, shared access signature)](storage/common/storage-dotnet-shared-access-signature-part-1.md) zapewnia delegowany dostęp do zasobów w ramach konta magazynu.
Oznacza to, że możliwe jest przyznanie klientowi ograniczonych uprawnień do obiektów w ramach konta magazynu na określony czas i z określonym zestawem uprawnień bez konieczności udostępniania kluczy dostępu do konta.

Następujące kroki ilustrują sposób tworzenia sygnatury dostępu Współdzielonego dla kontenera obiektów blob:

1. Otwórz Eksploratora usługi Storage (wersja zapoznawcza).
2. W okienku po lewej stronie rozwiń konto magazynu zawierające kontenera obiektów blob, dla którego chcesz uzyskać sygnatury dostępu Współdzielonego.
3. Rozwiń konto magazynu **kontenerów obiektów Blob**.
4. Kliknij prawym przyciskiem myszy kontener żądanego obiektu blob i — wybierz z menu kontekstowego - **Uzyskaj sygnaturę dostępu współdzielonego**.

   ![Uzyskiwanie sygnatury dostępu Współdzielonego menu kontekstowe][8]
5. W oknie dialogowym **Sygnatura dostępu współdzielonego** określ zasady, daty rozpoczęcia i wygaśnięcia, strefę czasową oraz poziomy dostępu dla zasobu.

   ![Opcje SAS][9]
6. Po zakończeniu określania opcji sygnatury dostępu współdzielonego wybierz pozycję **Utwórz**.
7. Drugi **sygnatura dostępu współdzielonego** okna dialogowego zostanie następnie wyświetlona wyświetlającym kontenera obiektów blob oraz adres URL i QueryStrings umożliwia dostęp do zasobu magazynu.
   Wybierz pozycję **Kopiuj** obok adres URL, który chcesz skopiować do schowka.

   ![Skopiuj adresy URL SAS][10]
8. Po zakończeniu wybierz pozycję **Zamknij**.

## <a name="manage-access-policies-for-a-blob-container"></a>Zarządzanie zasadami dostępu do kontenera obiektów blob
Poniższe kroki przedstawiają sposób zarządzania (Dodaj i Usuń) zasady dla kontenera obiektów blob dostępu:

1. Otwórz Eksploratora usługi Storage (wersja zapoznawcza).
2. W okienku po lewej stronie rozwiń konto magazynu zawierające kontenera obiektów blob zasad dostępu, których chcesz zarządzać.
3. Rozwiń konto magazynu **kontenerów obiektów Blob**.
4. Wybierz żądaną blobcontainer i — wybierz z menu kontekstowego - **Zarządzanie zasadami dostępu**.

   ![Menu kontekstowe Zarządzanie zasadami dostępu][11]
5. **Zasady dostępu** okno dialogowe wyświetla wszystkie zasady dostępu już utworzone dla wybranych obiektów blob kontenera.

   ![Opcje zasad dostępu][12]        
6. Wykonaj następujące kroki w zależności od zadania zarządzania zasadami dostępu:

   * **Dodawanie nowych zasad dostępu** — wybierz pozycję **Dodaj**. Po wygenerowaniu w oknie dialogowym **Zasady dostępu** będą wyświetlane nowo dodane zasady dostępu (przy użyciu ustawień domyślnych).
   * **Edytowanie zasad dostępu** — wprowadź żądane zmiany i wybierz **zapisać**.
   * **Usuwanie zasad dostępu** — wybierz pozycję **Usuń** obok zasad dostępu, które chcesz usunąć.

## <a name="set-the-public-access-level-for-a-blob-container"></a>Ustaw poziom dostępu publicznego kontenera obiektów blob
Domyślnie każdy kontener obiektów blob jest ustawiony na "Brak dostępu publicznego".

Poniższe kroki przedstawiają sposób określić poziom dostępu publicznego kontenera obiektów blob.

1. Otwórz Eksploratora usługi Storage (wersja zapoznawcza).
2. W okienku po lewej stronie rozwiń konto magazynu zawierające kontenera obiektów blob zasad dostępu, których chcesz zarządzać.
3. Rozwiń konto magazynu **kontenerów obiektów Blob**.
4. Wybierz żądaną blobcontainer i — wybierz z menu kontekstowego - **Ustaw poziom dostępu publicznego**.

   ![Menu kontekstowe poziomu dostępu publicznego zestawu][13]
5. W **Ustaw poziom dostępu publicznego kontenera** okna dialogowego, określić poziom dostępu do żądanego.

   ![Ustaw opcje poziomu dostępu publicznego][14]
6. Wybierz przycisk **Zastosuj**.

## <a name="managing-blobs-in-a-blob-container"></a>Zarządzanie obiekty BLOB w kontenerze obiektów blob
Po utworzeniu kontenera obiektów blob możesz przekazać obiekt blob do kontenera obiektów blob, pobieranie obiektu blob na komputerze lokalnym, otwórz obiektu blob na komputerze lokalnym i wiele innych.

Poniższe kroki przedstawiają sposób zarządzania obiektów blob (i foldery) w kontenerze obiektów blob.

1. Otwórz Eksploratora usługi Storage (wersja zapoznawcza).
2. W okienku po lewej stronie rozwiń konto magazynu zawierające kontenera obiektów blob się, że chcesz zarządzać.
3. Rozwiń konto magazynu **kontenerów obiektów Blob**.
4. Kliknij dwukrotnie kontenera obiektów blob, który chcesz wyświetlić.
5. W okienku głównym zostaną wyświetlone zawartości kontenera obiektów blob.

   ![Widok kontenera obiektów blob][3]
6. W okienku głównym zostaną wyświetlone zawartości kontenera obiektów blob.
7. Wykonaj następujące kroki w zależności od zadania, które chcesz wykonać:

   * **Przekazywanie plików do kontenera obiektów blob**

     1. Na pasku narzędzi okienka głównego wybierz pozycję **Przekaż**, a następnie z menu rozwijanego wybierz pozycję **Przekaż pliki**.

        ![Menu Przekaż pliki][15]
     2. W oknie dialogowym **Przekazywanie plików** wybierz przycisk wielokropka (**...**) po prawej stronie pola tekstowego **Pliki**, aby wybrać pliki do przekazania.

        ![Przekazywanie plików opcje][16]
     3. Określ typ **typu obiektu Blob**. Zobacz [utworzyć kontener i ustawić uprawnienia](storage/blobs/storage-quickstart-blobs-dotnet.md#upload-blobs-to-the-container) Aby uzyskać więcej informacji.
     4. Opcjonalnie określ folder docelowy, do którego zostanie przekazany wybranych plików. Jeśli folder docelowy nie istnieje, zostanie on utworzony.
     5. Wybierz pozycję **Przekaż**.
   * **Przekaż folder do kontenera obiektów blob**

     1. Na pasku narzędzi okienka głównego wybierz pozycję **Przekaż**, a następnie z menu rozwijanego wybierz pozycję **Przekaż folder**.

        ![Menu Przekaż folder][17]
     2. W oknie dialogowym **Przekazywanie folderu** wybierz przycisk wielokropka (**...**) po prawej stronie pola tekstowego **Folder**, aby wybrać folder, którego zawartość chcesz przekazać.

        ![Przekaż Opcje folderów][18]
     3. Określ typ **typu obiektu Blob**. Zobacz [utworzyć kontener i ustawić uprawnienia](storage/blobs/storage-quickstart-blobs-dotnet.md#upload-blobs-to-the-container) Aby uzyskać więcej informacji.
     4. Opcjonalnie określ folder docelowy, do którego zawartość wybranego folderu zostanie przekazana. Jeśli folder docelowy nie istnieje, zostanie on utworzony.
     5. Wybierz pozycję **Przekaż**.
   * **Pobieranie obiektu blob na komputerze lokalnym**

     1. Wybierz obiekt blob, który chcesz pobrać.
     2. Na pasku narzędzi okienka głównego wybierz pozycję **Pobierz**.
     3. W **Określ, gdzie chcesz zapisać pobrany obiekt blob** okna dialogowego, określ lokalizację, w którym ma być pobrany obiekt blob i chcesz nadać mu nazwę.  
     4. Wybierz pozycję **Zapisz**.
   * **Otwórz obiektu blob na komputerze lokalnym**

     1. Wybierz obiekt blob, który chcesz otworzyć.
     2. Na pasku narzędzi okienka głównego wybierz pozycję **Otwórz**.
     3. Obiekt blob zostaną pobrane i otworzyć przy użyciu aplikacji skojarzonej z typem pliku źródłowego obiektu blob.
   * **Kopiowanie obiektu blob do Schowka**

     1. Wybierz obiekt blob, który chcesz skopiować.
     2. Na pasku narzędzi okienka głównego wybierz pozycję **Kopiuj**.
     3. W okienku po lewej stronie przejść do innego kontenera obiektów blob, a następnie kliknij dwukrotnie, aby go wyświetlić w okienku głównym.
     4. W okienku głównym pasku narzędzi wybierz **Wklej** można utworzyć kopii obiektu blob.
   * **Usuwanie obiektu blob**

     1. Wybierz obiekt blob, który chcesz usunąć.
     2. Na pasku narzędzi okienka głównego wybierz pozycję **Usuń**.
     3. Wybierz pozycję **Tak** w oknie dialogowym potwierdzenia.

## <a name="next-steps"></a>Kolejne kroki
* Zobacz [najnowsze informacje o wersji i filmy wideo dotyczące programu Storage Explorer (wersja zapoznawcza)](http://www.storageexplorer.com).
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
