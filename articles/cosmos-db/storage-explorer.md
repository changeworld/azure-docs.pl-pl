---
title: Zarządzanie usługą Azure Cosmos DB w Eksploratorze usługi Azure Storage
description: Dowiedz się, jak zarządzać usługą Azure Cosmos DB w Eksploratorze usługi Azure Storage.
Keywords: Azure Cosmos DB, Azure Storage Explorer, MongoDB
services: cosmos-db
documentationcenter: ''
author: Jejiang
manager: omafnan
editor: ''
tags: Azure Cosmos DB
ms.assetid: ''
ms.service: cosmos-db
ms.custom: Azure Cosmos DB active
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/20/2018
ms.author: jejiang
ms.openlocfilehash: 18f580f1eae31c9bf3626e100217467bb48ca881
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2018
---
# <a name="manage-azure-cosmos-db-in-azure-storage-explorer-preview"></a>Zarządzanie usługą Azure Cosmos DB w Eksploratorze usługi Azure Storage (wersja zapoznawcza)

Korzystanie z usługi Azure Cosmos DB w Eksploratorze usługi Azure Storage pozwala użytkownikom zarządzać jednostkami usługi Azure Cosmos DB, wykonywać operacje na danych oraz aktualizować procedury składowane i wyzwalacze, a także inne jednostki platformy Azure, takie jak obiekty blob i kolejki usługi Storage. Za pomocą jednego narzędzia można teraz centralnie zarządzać różnymi jednostkami platformy Azure. Aktualnie Eksplorator usługi Azure Storage obsługuje konta baz danych SQL i MongoDB, programu Graph oraz tabel.

Ten artykuł zawiera informacje dotyczące zarządzania usługą Azure Cosmos DB za pomocą Eksploratora usługi Storage.


## <a name="prerequisites"></a>Wymagania wstępne

Konto usługi Azure Cosmos DB dla interfejsu API SQL <!--or MongoDB API-->. Jeśli nie masz konta, możesz je utworzyć w witrynie Azure Portal, wykonując instrukcje podane w temacie [Azure Cosmos DB: Tworzenie aplikacji internetowej interfejsu API SQL za pomocą platformy .NET i witryny Azure Portal](create-sql-api-dotnet.md).

## <a name="installation"></a>Instalacja

Zainstaluj najnowszą wersję Eksploratora usługi Azure Storage. Program [Eksplorator usługi Azure Storage](https://azure.microsoft.com/features/storage-explorer/) jest dostępny w wersji dla systemu Windows i Linux oraz dla komputerów MAC.

## <a name="connect-to-an-azure-subscription"></a>Łączenie się z subskrypcją platformy Azure

1. Po zainstalowaniu **Eksploratora usługi Azure Storage** kliknij ikonę **wtyczki** widoczną po lewej stronie, jak pokazano na poniższym obrazie:
       
   ![Ikona wtyczki](./media/storage-explorer/plug-in-icon.png)
 
2. Wybierz pozycję **Dodaj konto platformy Azure**, a następnie kliknij pozycję **Zaloguj się**.

   ![Łączenie się z subskrypcją platformy Azure](./media/storage-explorer/connect-to-azure-subscription.png)

2. W oknie dialogowym **Logowanie do platformy Azure** wybierz pozycję **Zaloguj**, a następnie wprowadź poświadczenia platformy Azure.

    ![Logowanie](./media/storage-explorer/sign-in.png)

3. Wybierz subskrypcję z listy, a następnie kliknij przycisk **Zastosuj**.

    ![Zastosuj](./media/storage-explorer/apply-subscription.png)

    Zawartość okienka Eksploratora zostanie zaktualizowana i pojawią się konta w wybranej subskrypcji.

    ![Lista kont](./media/storage-explorer/account-list.png)

    Pomyślnie nawiązano połączenie z **kontem usługi Cosmos DB** w ramach subskrypcji platformy Azure.

## <a name="connect-to-azure-cosmos-db-by-using-a-connection-string"></a>Łączenie się z usługą Azure Cosmos DB przy użyciu parametrów połączenia

Alternatywna metoda połączenia się z usługą Azure Cosmos DB polega na użyciu parametrów połączenia. Aby nawiązać połączenie przy użyciu parametrów połączenia, wykonaj następujące kroki.

1. Znajdź pozycję **Lokalne i dołączone** w drzewie po lewej stronie, kliknij prawym przyciskiem myszy pozycję **Konta usługi Cosmos DB**, a następnie wybierz pozycję **Połącz z usługą Cosmos DB...**

    ![Nawiązywanie połączenia z usługą Cosmos DB za pomocą parametrów połączenia](./media/storage-explorer/connect-to-db-by-connection-string.png)

2. Aktualnie obsługiwana jest baza danych SQL i interfejs API tabel. Wybierz interfejs API, wklej **parametry połączenia**, wprowadź wartość **Etykieta konta**, kliknij przycisk **Dalej**, aby sprawdzić podsumowanie, a następnie kliknij pozycję **Połącz**, aby nawiązać połączenie z kontem usługi Azure Cosmos DB. Aby uzyskać informacje dotyczące pobierania parametrów połączenia, zobacz [Get the connection string (Pobieranie parametrów połączenia)](https://docs.microsoft.com/azure/cosmos-db/manage-account#get-the--connection-string).

    ![Parametry połączenia](./media/storage-explorer/connection-string.png)

## <a name="connect-to-azure-cosmos-db-by-using-local-emulator"></a>Nawiązywanie połączenia z usługą Azure Cosmos DB za pomocą emulatora lokalnego
Wykonaj następujące kroki, aby nawiązać połączenie z usługą Azure Cosmos DB za pomocą emulatora. Obecnie obsługiwane jest tylko konto bazy danych SQL.
1. Zainstaluj emulator i uruchom go. Aby uzyskać informacje dotyczące instalowania emulatora, zobacz [Emulator usługi Cosmos DB](https://docs.microsoft.com/en-us/azure/cosmos-db/local-emulator)
2. Znajdź pozycję **Lokalne i dołączone** w drzewie po lewej stronie, kliknij prawym przyciskiem myszy pozycję **Konta usługi Cosmos DB**, a następnie wybierz pozycję **Połącz z emulatorem usługi Cosmos DB...**

    ![Nawiązywanie połączenia z usługą Cosmos DB za pomocą emulatora](./media/storage-explorer/emulator-entry.png)

3. Aktualnie obsługiwany jest tylko interfejs API SQL. Wklej **parametry połączenia**, wprowadź wartość **Etykieta konta**, kliknij przycisk **Dalej**, aby sprawdzić podsumowanie, a następnie kliknij pozycję **Połącz**, aby nawiązać połączenie z kontem usługi Azure Cosmos DB. Aby uzyskać informacje dotyczące pobierania parametrów połączenia, zobacz [Get the connection string (Pobieranie parametrów połączenia)](https://docs.microsoft.com/azure/cosmos-db/manage-account#get-the--connection-string).

    ![Nawiązywanie połączenia z usługą Cosmos DB za pomocą okna dialogowego emulatora](./media/storage-explorer/emulator-dialog.png)



## <a name="azure-cosmos-db-resource-management"></a>Zarządzanie zasobami usługi Azure Cosmos DB

W ramach zarządzania kontem usługi Azure Cosmos DB można wykonywać następujące operacje:
* Otwieranie konta w witrynie Azure Portal
* Dodawanie zasobu do listy szybkiego dostępu
* Wyszukiwanie i odświeżanie zasobów
* Tworzenie i usuwanie baz danych
* Tworzenie i usuwanie kolekcji
* Tworzenie, edytowanie, usuwanie i filtrowanie dokumentów
* Zarządzanie procedurami składowanymi, wyzwalaczami i funkcjami zdefiniowanymi przez użytkownika

### <a name="quick-access-tasks"></a>Zadania szybkiego dostępu

Po kliknięciu prawym przyciskiem myszy subskrypcji w okienku Eksploratora pojawią się polecenia, które pozwalają wykonywać wiele szybkich działań:

* Po kliknięciu prawym przyciskiem myszy konta lub bazy danych usługi Azure Cosmos DB można wybrać polecenie **Otwórz w portalu**, aby zarządzać zasobem w przeglądarce w witrynie Azure Portal.

     ![Otwórz w portalu](./media/storage-explorer/open-in-portal.png)

* Do paska **Szybki dostęp** można także dodać konto, bazę danych lub kolekcję usługi Azure Cosmos DB.
* Opcja **Wyszukaj od tego miejsca** umożliwia wyszukiwanie słów kluczowych w obrębie wybranej ścieżki.

    ![Wyszukaj od tego miejsca](./media/storage-explorer/search-from-here.png) 

### <a name="database-and-collection-management"></a>Zarządzanie bazą danych i kolekcją
#### <a name="create-a-database"></a>Tworzenie bazy danych 
-   Kliknij prawym przyciskiem myszy konto usługi Azure Cosmos DB, wybierz polecenie **Utwórz bazę danych**, wprowadź nazwę bazy danych, a następnie naciśnij klawisz **Enter**, aby zakończyć procedurę.
       
    ![Tworzenie bazy danych](./media/storage-explorer/create-database.png) 

#### <a name="delete-a-database"></a>Usuwanie bazy danych
- Kliknij prawym przyciskiem myszy bazę danych, kliknij polecenie **Usuń bazę danych** i kliknij przycisk **Tak** w oknie podręcznym. Węzeł bazy danych zostanie usunięty, a konto usługi Azure Cosmos DB zostanie odświeżone automatycznie.

    ![Usuwanie bazy danych 1](./media/storage-explorer/delete-database1.png)  

    ![Usuwanie bazy danych 2](./media/storage-explorer/delete-database2.png) 

#### <a name="create-a-collection"></a>Tworzenie kolekcji
1. Kliknij prawym przyciskiem myszy bazę danych, wybierz polecenie **Utwórz kolekcję**, a następnie podaj odpowiednie informacje w polach, takich jak **Identyfikator kolekcji**, **Pojemność magazynu** itd. Kliknij przycisk **OK**, aby zakończyć. 

    ![Tworzenie kolekcji 1](./media/storage-explorer/create-collection.png)

    ![Tworzenie kolekcji 2](./media/storage-explorer/create-collection2.png) 

2. Wybierz wartość **Bez ograniczeń**, aby możliwe było określenie klucza partycji, a następnie kliknij przycisk **OK**, aby zakończyć.

    Jeśli podczas tworzenia kolekcji jest używany klucz partycji, to po jej utworzeniu nie można zmienić wartości klucza partycji. Aby uzyskać informacje na temat ustawień klucza partycji, zobacz [Design for partitioning (Projektowanie pod kątem partycjonowania)](partition-data.md#designing-for-partitioning).

    ![Klucz partycji](./media/storage-explorer/partitionkey.png)

#### <a name="delete-a-collection"></a>Usuwanie kolekcji
- Kliknij prawym przyciskiem myszy kolekcję, kliknij polecenie **Usuń kolekcję**, a następnie kliknij przycisk **Tak** w oknie podręcznym. 

    Węzeł kolekcji zostanie usunięty, a baza danych zostanie odświeżona automatycznie.

    ![Usuwanie kolekcji](./media/storage-explorer/delete-collection.png) 

### <a name="document-management"></a>Zarządzanie dokumentami

#### <a name="create-and-modify-documents"></a>Tworzenie i modyfikowanie dokumentów
- Aby utworzyć nowy dokument, otwórz węzeł **Dokumenty** w lewym oknie, kliknij pozycję **Nowy dokument**, zmodyfikuj zawartość dokumentu w prawym okienku, a następnie kliknij przycisk **Zapisz**. Możesz również zaktualizować istniejący dokument, a następnie kliknąć przycisk **Zapisz**. Aby odrzucić zmiany, kliknij przycisk **Odrzuć**.

    ![Dokument](./media/storage-explorer/document.png)

#### <a name="delete-a-document"></a>Usuwanie dokumentu
- Aby usunąć wybrany dokument, kliknij przycisk **Usuń**.

#### <a name="query-for-documents"></a>Wykonywanie zapytań dotyczących dokumentów
- Aby zmodyfikować filtr dokumentu, wprowadź [zapytanie SQL](sql-api-sql-query.md), a następnie kliknij przycisk **Zastosuj**.

    ![Filtr dokumentu](./media/storage-explorer/document-filter.png)



### <a name="graph-management"></a>Zarządzanie programem Graph

#### <a name="create-and-modify-vertex"></a>Tworzenie i modyfikowanie wierzchołków
1. Aby utworzyć nowy wierzchołek, otwórz program **Graph** w oknie po lewej stronie, kliknij pozycję **Nowy wierzchołek**, dokonaj edycji zawartości, a następnie kliknij przycisk **OK**.    
2. Aby zmodyfikować istniejący wierzchołek, kliknij ikonę pióra w okienku po prawej stronie.   

    ![Graph](./media/storage-explorer/vertex.png)

#### <a name="delete-a-graph"></a>Usuwanie wykresu
- Aby usunąć wierzchołek, kliknij ikonę kosza obok nazwy wierzchołka.

#### <a name="filter-for-graph"></a>Filtrowanie wykresów
- Edytuj filtr wykresu, wprowadzając [zapytanie w języku gremlin](gremlin-support.md), a następnie klikając pozycję **Zastosuj filtr**.

    ![Filtr wykresu](./media/storage-explorer/graph-filter.png)

### <a name="table-management"></a>Zarządzanie regułami

#### <a name="create-and-modify-table"></a>Tworzenie i modyfikowanie tabeli
1. Aby utworzyć nową tabelę, otwórz obszar **Jednostki** w oknie po lewej stronie, kliknij pozycję **Dodaj**, edytuj zawartość w oknie dialogowym **Dodawanie jednostki**, dodaj właściwość, klikając przycisk **Dodaj Właściwość**, a następnie kliknij pozycję **Wstaw**.
2. Aby zmodyfikować tabelę, kliknij pozycję **Edytuj**, zmodyfikuj zawartość, a następnie kliknij pozycję **Aktualizuj**.

    ![Tabela](./media/storage-explorer/table.png)

#### <a name="import-and-export-table"></a>Importowanie i eksportowanie tabeli
1. Aby zaimportować tabelę, kliknij przycisk **Importuj**, a następnie wybierz istniejącą tabelę.
2. Aby wyeksportować tabelę, kliknij przycisk **Eksportuj**, a następnie wybierz miejsce docelowe.

    ![Importowanie i eksportowanie tabeli](./media/storage-explorer/table-import-export.png)

#### <a name="delete-entities"></a>Usuwanie jednostek
- Zaznacz jednostki, a następnie kliknij przycisk **Usuń**.

    ![Usuwanie tabeli](./media/storage-explorer/table-delete.png)

#### <a name="query-table"></a>Wykonywanie zapytań dotyczących tabeli
- Kliknij przycisk **Zapytanie**, wprowadź warunki zapytania, a następnie kliknij przycisk **Wykonaj zapytanie**. Zamknij okienko Zapytanie, klikając **Zamknij zapytanie**.

    ![Zapytanie dotyczące tabeli](./media/storage-explorer/table-query.png)

### <a name="manage-stored-procedures-triggers-and-udfs"></a>Zarządzanie procedurami składowanymi, wyzwalaczami i funkcjami definiowanymi przez użytkownika (UDF)
* Aby utworzyć procedurę składowaną, w drzewie po lewej stronie kliknij prawym przyciskiem myszy pozycję **Procedura składowana**, wybierz polecenie **Utwórz procedurę składowaną**, wprowadź nazwę po lewej stronie, wpisz skrypty procedury składowanej w prawym okienku, a następnie kliknij przycisk **Utwórz**. 
* Możesz również edytować istniejące procedury składowane. W tym celu kliknij dwukrotnie procedurę, wprowadź zmiany, a następnie kliknij pozycję **Aktualizuj**, aby zapisać zmiany, lub pozycję **Odrzuć**, aby je anulować.

    ![Procedura składowana](./media/storage-explorer/stored-procedure.png)
* Operacje dotyczące **wyzwalaczy** i **funkcji definiowanych przez użytkownika** są podobne do **procedur składowanych**.

## <a name="next-steps"></a>Następne kroki

* Obejrzyj ten film wideo, aby dowiedzieć się, jak używać usługi Azure Cosmos DB w Eksploratorze usługi Azure Storage: [Use Azure Cosmos DB in Azure Storage Explorer (Używanie usługi Azure Cosmos DB w Eksploratorze usługi Azure Storage)](https://www.youtube.com/watch?v=iNIbg1DLgWo&feature=youtu.be).
* Aby dowiedzieć się więcej o Eksploratorze usługi Storage i sposobach łączenia się z dodatkowymi usługami, zobacz [Wprowadzenie do programu Storage Explorer (wersja zapoznawcza)](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer).

