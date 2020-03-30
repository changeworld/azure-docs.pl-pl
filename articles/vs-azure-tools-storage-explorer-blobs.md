---
title: Zarządzanie zasobami usługi Azure Blob Storage za pomocą Eksploratora magazynu | Dokumenty firmy Microsoft
description: Zarządzanie kontenerami obiektów blob platformy Azure i obiektami blob za pomocą Eksploratora magazynu
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
ms.date: 05/21/2019
ms.author: cawa
ms.openlocfilehash: 56c20c995a95058b5039b7268c7b7b1426e900fa
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79244396"
---
# <a name="manage-azure-blob-storage-resources-with-storage-explorer"></a>Zarządzanie zasobami usługi Azure Blob Storage za pomocą Eksploratora usługi Storage

## <a name="overview"></a>Omówienie

[Usługa Azure Blob Storage](storage/blobs/storage-dotnet-how-to-use-blobs.md) to usługa do przechowywania dużych ilości danych nieustrukturyzowanych, takich jak dane tekstowe lub binarne, które są dostępne z dowolnego miejsca na świecie za pośrednictwem protokołu HTTP lub HTTPS.
Magazyn obiektów Blob może być użyty do udostępniania danych publicznie lub do przechowywania danych aplikacji prywatnie. W tym artykule dowiesz się, jak używać Eksploratora magazynu do pracy z kontenerami obiektów blob i obiektami blob.

## <a name="prerequisites"></a>Wymagania wstępne

Do wykonania kroków opisanych w tym artykule konieczne jest wykonanie kroków znajdujących się w następujących artykułach:

* [Pobieranie i instalowanie Eksploratora usługi Storage](https://www.storageexplorer.com)
* [Łączenie się z kontem lub usługą magazynu platformy Azure](vs-azure-tools-storage-manage-with-storage-explorer.md#connect-to-a-storage-account-or-service)

## <a name="create-a-blob-container"></a>Tworzenie kontenera obiektów blob

Wszystkie obiekty BLOB muszą znajdować się w kontenerze obiektów blob, który jest po prostu logicznym grupowanie obiektów blob. Konto może zawierać nieograniczoną liczbę kontenerów, a każdy kontener może przechowywać nieograniczoną liczbę obiektów blob.

W poniższych krokach pokazano, jak utworzyć kontener obiektów blob w Eksploratorze magazynu.

1. Otwórz Eksploratora usługi Storage.
2. W okienku po lewej stronie rozwiń konto magazynu, w którym chcesz utworzyć kontener obiektów blob.
3. Kliknij prawym przyciskiem myszy **pozycję Kontenery obiektów blob**i - z menu kontekstowego - wybierz polecenie **Utwórz kontener obiektów blob**.

   ![Menu kontekstowe — tworzenie kontenerów obiektów blob][0]
4. Pole tekstowe pojawi się pod folderem **Kontenery obiektów blob.** Wprowadź nazwę kontenera obiektów blob. Zobacz [Tworzenie kontenera, aby](storage/blobs/storage-quickstart-blobs-dotnet.md#create-a-container) uzyskać informacje na temat reguł i ograniczeń dotyczących nazewnictwa kontenerów obiektów blob.

   ![Pole tekstowe Tworzenie kontenerów obiektów blob][1]
5. Po zakończeniu naciśnij klawisz **Enter**, aby utworzyć kontener obiektów blob, lub klawisz **Esc**, aby anulować. Po pomyślnym utworzeniu kontenera obiektów blob zostanie on wyświetlony w folderze **Kontenery obiektów blob** dla wybranego konta magazynu.

   ![Utworzony kontener obiektów blob][2]

## <a name="view-a-blob-containers-contents"></a>Wyświetlanie zawartości kontenera obiektów blob

Kontenery obiektów blob zawierają obiekty BLOB i foldery (które mogą również zawierać obiekty BLOB).

W poniższych krokach pokazano sposób wyświetlania zawartości kontenera obiektów blob w Eksploratorze magazynu:

1. Otwórz Eksploratora usługi Storage.
2. W lewym okienku rozwiń konto magazynu zawierające kontener obiektów blob, który chcesz wyświetlić.
3. Rozwiń **kontenery obiektów blob**konta magazynu .
4. Kliknij prawym przyciskiem myszy kontener obiektów blob, który chcesz wyświetlić, i - z menu kontekstowego - wybierz pozycję **Otwórz Edytor kontenerów obiektów blob**.
   Można również kliknąć dwukrotnie kontener obiektów blob, który chcesz wyświetlić.

   ![Menu kontekstowe edytora kontenerów obiektów blob][19]
5. W głównym okienku zostanie wyświetlone zawartość kontenera obiektów blob.

   ![Edytor kontenerów obiektów blob][3]

## <a name="delete-a-blob-container"></a>Usuwanie kontenera obiektu blob

Kontenery obiektów blob można łatwo tworzyć i usuwać w razie potrzeby. (Aby zobaczyć, jak usunąć poszczególne obiekty blob, zapoznaj się z sekcją [Zarządzanie obiektami blob w kontenerze obiektów blob).](#managing-blobs-in-a-blob-container)

W poniższych krokach pokazano, jak usunąć kontener obiektów blob w Eksploratorze magazynu:

1. Otwórz Eksploratora usługi Storage.
2. W lewym okienku rozwiń konto magazynu zawierające kontener obiektów blob, który chcesz wyświetlić.
3. Rozwiń **kontenery obiektów blob**konta magazynu .
4. Kliknij prawym przyciskiem myszy kontener obiektów blob, który chcesz usunąć, a następnie - z menu kontekstowego - wybierz polecenie **Usuń**.
   Można również nacisnąć **klawisz Delete,** aby usunąć aktualnie zaznaczony kontener obiektów blob.

   ![Usuwanie menu kontekstowego kontenera obiektów blob][4]
5. Wybierz pozycję **Tak** w oknie dialogowym potwierdzenia.

   ![Usuwanie potwierdzenia kontenera obiektu blob][5]

## <a name="copy-a-blob-container"></a>Kopiowanie kontenera obiektów blob

Eksplorator magazynu umożliwia skopiowanie kontenera obiektów blob do schowka, a następnie wklejenie tego kontenera obiektu blob do innego konta magazynu. (Aby zobaczyć, jak skopiować poszczególne obiekty blob, zapoznaj się z sekcją [Zarządzanie obiektami blob w kontenerze obiektów blob).](#managing-blobs-in-a-blob-container)

Poniższe kroki ilustrują sposób kopiowania kontenera obiektów blob z jednego konta magazynu do innego.

1. Otwórz Eksploratora usługi Storage.
2. W lewym okienku rozwiń konto magazynu zawierające kontener obiektów blob, który chcesz skopiować.
3. Rozwiń **kontenery obiektów blob**konta magazynu .
4. Kliknij prawym przyciskiem myszy kontener obiektów blob, który chcesz skopiować, i - z menu kontekstowego - wybierz polecenie **Kopiuj kontener obiektów Blob**.

   ![Kopiowanie menu kontekstowego kontenera obiektów blob][6]
5. Kliknij prawym przyciskiem myszy żądane konto magazynu "docelowego", na które chcesz wkleić kontener obiektów blob, i - z menu kontekstowego - wybierz **pozycję Wklej kontener obiektów blob**.

   ![Wklej menu kontekstowe kontenera obiektów blob][7]

## <a name="get-the-sas-for-a-blob-container"></a>Uzyskiwanie sygnatury dostępu współdzielonego dla kontenera obiektów blob

[Sygnatura dostępu współdzielonego (SAS, shared access signature)](storage/common/storage-dotnet-shared-access-signature-part-1.md) zapewnia delegowany dostęp do zasobów w ramach konta magazynu.
Oznacza to, że możliwe jest przyznanie klientowi ograniczonych uprawnień do obiektów w ramach konta magazynu na określony czas i z określonym zestawem uprawnień bez konieczności udostępniania kluczy dostępu do konta.

Poniższe kroki ilustrują sposób tworzenia sygnatury dostępu Współdzielonego dla kontenera obiektów blob:

1. Otwórz Eksploratora usługi Storage.
2. W lewym okienku rozwiń konto magazynu zawierające kontener obiektów blob, dla którego chcesz uzyskać sygnaturę dostępu Współdzielonego.
3. Rozwiń **kontenery obiektów blob**konta magazynu .
4. Kliknij prawym przyciskiem myszy żądany kontener obiektów blob i - z menu kontekstowego - wybierz pozycję **Pobierz podpis dostępu współdzielonego**.

   ![Pobierz menu kontekstowe SAS][8]
5. W oknie dialogowym **Sygnatura dostępu współdzielonego** określ zasady, daty rozpoczęcia i wygaśnięcia, strefę czasową oraz poziomy dostępu dla zasobu.

   ![Pobierz opcje Sygnatury dostępu Współdzielon][9]
6. Po zakończeniu określania opcji sygnatury dostępu współdzielonego wybierz pozycję **Utwórz**.
7. Następnie zostanie wyświetlone drugie okno dialogowe **Podpis dostępu współdzielonego** z listą kontenera obiektów blob wraz z adresem URL i ciągami zapytania, których można użyć do uzyskania dostępu do zasobu magazynu.
   Wybierz pozycję **Kopiuj** obok adres URL, który chcesz skopiować do schowka.

   ![Kopiowanie adresów URL Sygnatury Dostępu Współdzielon][10]
8. Po zakończeniu wybierz pozycję **Zamknij**.

## <a name="manage-access-policies-for-a-blob-container"></a>Zarządzanie zasadami dostępu dla kontenera obiektów blob

Poniższe kroki ilustrują sposób zarządzania (dodawania i usuwania) zasad dostępu dla kontenera obiektów blob:

1. Otwórz Eksploratora usługi Storage.
2. W lewym okienku rozwiń konto magazynu zawierające kontener obiektów blob, którego zasady dostępu mają być zarządzane.
3. Rozwiń **kontenery obiektów blob**konta magazynu .
4. Wybierz żądany kontener obiektów blob i - z menu kontekstowego - wybierz pozycję **Zarządzaj zasadami dostępu**.

   ![Menu kontekstowe Zarządzanie zasadami dostępu][11]
5. W oknie dialogowym **Zasady dostępu** zostaną wyświetlone wszystkie zasady dostępu utworzone już dla wybranego kontenera obiektów blob.

   ![Opcje zasad dostępu][12]
6. Wykonaj następujące kroki w zależności od zadania zarządzania zasadami dostępu:

   * **Dodawanie nowych zasad dostępu** — wybierz pozycję **Dodaj**. Po wygenerowaniu w oknie dialogowym **Zasady dostępu** będą wyświetlane nowo dodane zasady dostępu (przy użyciu ustawień domyślnych).
   * **Edytowanie zasad dostępu** — wprowadzanie dowolnych pożądanych zmian i **wybieranie**opcji Zapisz .
   * **Usuwanie zasad dostępu** — wybierz pozycję **Usuń** obok zasad dostępu, które chcesz usunąć.

## <a name="set-the-public-access-level-for-a-blob-container"></a>Ustawianie publicznego poziomu dostępu dla kontenera obiektów blob

Domyślnie każdy kontener obiektów blob jest ustawiony na "Brak dostępu publicznego".

Poniższe kroki ilustrują sposób określania poziomu dostępu publicznego dla kontenera obiektów blob.

1. Otwórz Eksploratora usługi Storage.
2. W lewym okienku rozwiń konto magazynu zawierające kontener obiektów blob, którego zasady dostępu mają być zarządzane.
3. Rozwiń **kontenery obiektów blob**konta magazynu .
4. Wybierz żądany kontener obiektów blob i - z menu kontekstowego - wybierz pozycję **Ustaw publiczny poziom dostępu**.

   ![Ustawianie menu kontekstowego poziomu dostępu publicznego][13]
5. W oknie dialogowym **Ustawianie publicznego poziomu dostępu kontenera** określ żądany poziom dostępu.

   ![Ustawianie opcji poziomu dostępu publicznego][14]
6. Wybierz przycisk **Zastosuj**.

## <a name="managing-blobs-in-a-blob-container"></a>Zarządzanie obiektami blob w kontenerze obiektów blob

Po utworzeniu kontenera obiektów blob można przekazać obiekt blob do tego kontenera obiektów blob, pobrać obiekt blob na komputer lokalny, otworzyć obiekt blob na komputerze lokalnym i wiele więcej.

Poniższe kroki ilustrują sposób zarządzania obiektami blob (i folderami) w kontenerze obiektów blob.

1. Otwórz Eksploratora usługi Storage.
2. W lewym okienku rozwiń konto magazynu zawierające kontener obiektów blob, którym chcesz zarządzać.
3. Rozwiń **kontenery obiektów blob**konta magazynu .
4. Kliknij dwukrotnie kontener obiektów blob, który chcesz wyświetlić.
5. W głównym okienku zostanie wyświetlone zawartość kontenera obiektów blob.

   ![Wyświetl kontener obiektów blob][3]
6. W głównym okienku zostanie wyświetlone zawartość kontenera obiektów blob.
7. Wykonaj następujące kroki w zależności od zadania, które chcesz wykonać:

   * **Przekazywanie plików do kontenera obiektu blob**

     1. Na pasku narzędzi okienka głównego wybierz pozycję **Przekaż**, a następnie z menu rozwijanego wybierz pozycję **Przekaż pliki**.

        ![Menu Przekaż pliki][15]
     2. W oknie dialogowym **Przekazywanie plików** wybierz przycisk wielokropka (**...**) po prawej stronie pola tekstowego **Pliki**, aby wybrać pliki do przekazania.

        ![Opcje przekazywania plików][16]
     3. Określ typ **typu obiektu Blob**. Zobacz [Tworzenie kontenera, aby](storage/blobs/storage-quickstart-blobs-dotnet.md#create-a-container) uzyskać więcej informacji.
     4. Opcjonalnie określ folder docelowy, do którego zostaną przesłane wybrane pliki. Jeśli folder docelowy nie istnieje, zostanie on utworzony.
     5. Wybierz **pozycję Przekaż**.
   * **Przekazywanie folderu do kontenera obiektu blob**

     1. Na pasku narzędzi głównego okienka wybierz pozycję **Przekaż**, a następnie **przekaż folder** z menu rozwijanego.

        ![Menu Przekaż folder][17]
     2. W oknie dialogowym **Przekazywanie folderu** wybierz przycisk wielokropka (**...**) po prawej stronie pola tekstowego **Folder**, aby wybrać folder, którego zawartość chcesz przekazać.

        ![Opcje folderu przekazywania][18]
     3. Określ typ **typu obiektu Blob**. Zobacz [Tworzenie kontenera, aby](storage/blobs/storage-quickstart-blobs-dotnet.md#create-a-container) uzyskać więcej informacji.
     4. Opcjonalnie określ folder docelowy, do którego zawartość wybranego folderu zostanie przekazana. Jeśli folder docelowy nie istnieje, zostanie on utworzony.
     5. Wybierz **pozycję Przekaż**.
   * **Pobieranie obiektu blob na komputer lokalny**

     1. Wybierz obiekt blob, który chcesz pobrać.
     2. Na pasku narzędzi okienka głównego wybierz pozycję **Pobierz**.
     3. W oknie dialogowym **Określanie miejsca zapisywania pobranego obiektu blob** określ lokalizację, w której ma zostać pobrany obiekt blob, oraz nazwę, którą chcesz nadać.  
     4. Wybierz **pozycję Zapisz**.
   * **Otwieranie obiektu blob na komputerze lokalnym**

     1. Wybierz obiekt blob, który chcesz otworzyć.
     2. Na pasku narzędzi okienka głównego wybierz pozycję **Otwórz**.
     3. Obiekt blob zostanie pobrany i otwarty przy użyciu aplikacji skojarzonej z podstawowym typem pliku obiektu blob.
   * **Kopiowanie obiektu blob do schowka**

     1. Wybierz obiekt blob, który chcesz skopiować.
     2. Na pasku narzędzi okienka głównego wybierz pozycję **Kopiuj**.
     3. W lewym okienku przejdź do innego kontenera obiektów blob i kliknij go dwukrotnie, aby wyświetlić go w głównym okienku.
     4. Na pasku narzędzi głównego okienka wybierz pozycję **Wklej,** aby utworzyć kopię obiektu blob.
   * **Usuwanie obiektu blob**

     1. Wybierz obiekt blob, który chcesz usunąć.
     2. Na pasku narzędzi okienka głównego wybierz pozycję **Usuń**.
     3. Wybierz pozycję **Tak** w oknie dialogowym potwierdzenia.

## <a name="next-steps"></a>Następne kroki

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
