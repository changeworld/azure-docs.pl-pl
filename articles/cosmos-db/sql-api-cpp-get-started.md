---
title: Samouczek języka C++ dla usługi Azure Cosmos DB | Microsoft Docs
description: Samouczek języka C++, który pokazuje tworzenie bazy danych w języku C++ i aplikacji konsolowej przy użyciu zatwierdzonego dla usługi Azure Cosmos DB zestawu SDK języka C++. Azure Cosmos DB to skalowana w skali globalnej usługa bazy danych.
services: cosmos-db
documentationcenter: cpp
author: asthana86
manager: jhubbard
editor: ''
ms.assetid: b8756b60-8d41-4231-ba4f-6cfcfe3b4bab
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: cpp
ms.topic: article
ms.date: 12/25/2016
ms.author: aasthan
ms.openlocfilehash: 86bf4fbc683efde60765b14b379a2b88c77d3103
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2018
---
# <a name="azure-cosmos-db-c-console-application-tutorial-for-the-sql-api"></a>Azure DB rozwiązania Cosmos: Samouczek aplikacji konsoli C++ dla interfejsu API SQL
> [!div class="op_single_selector"]
> * [.NET](sql-api-get-started.md)
> * [.NET Core](sql-api-dotnetcore-get-started.md)
> * [Node.js dla MongoDB](mongodb-samples.md)
> * [Node.js](sql-api-nodejs-get-started.md)
> * [Java](sql-api-java-get-started.md)
> * [C++](sql-api-cpp-get-started.md)
>  
> 

Witamy w samouczku C++ dla interfejsu API Azure rozwiązania Cosmos bazy danych SQL zatwierdzone zestawu SDK dla języka C++! W ramach tego samouczka utworzysz aplikację konsolową, która tworzy zasoby usługi Azure Cosmos DB, w tym bazę danych w języku C++, oraz wykonuje względem nich zapytania.

Obejmuje to szybki start:

* Tworzenie konta usługi Azure Cosmos DB i łączenie się z nim
* Instalowanie aplikacji
* Tworzenie bazy danych usługi Azure Cosmos DB dla języka C++
* Tworzenie kolekcji
* Tworzenie dokumentów JSON
* Wykonywanie zapytań względem kolekcji
* Zastępowanie dokumentu
* Usuwanie dokumentu
* Usuwanie bazy danych usługi Azure Cosmos DB dla języka C++

Nie masz czasu? Nie martw się! Kompletne rozwiązanie jest dostępne w witrynie [GitHub](https://github.com/stalker314314/sql-apiCpp). Przejdź do sekcji [Pobieranie kompletnego rozwiązania](#GetSolution), aby uzyskać krótkie instrukcje.

Teraz do dzieła!

## <a name="prerequisites-for-the-c-tutorial"></a>Wymagania wstępne dla samouczka języka C++
Upewnij się, że masz następujące zasoby:

* Aktywne konto platformy Azure. Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F). 

  [!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

* [Visual Studio 2017](https://www.visualstudio.com/downloads/), z zainstalowanymi składnikami języka C++. Jeśli nie masz jeszcze zainstalowanego programu Visual Studio 2017, możesz pobrać program [Visual Studio 2017 Community Edition](https://www.visualstudio.com/downloads/) i używać go **bezpłatnie**. Podczas instalacji programu Visual Studio upewnij się, że jest włączona opcja **Programowanie na platformie Azure**.

## <a name="step-1-create-an-azure-cosmos-db-account"></a>Krok 1. Tworzenie konta usługi Azure Cosmos DB
Utwórzmy konto usługi Azure Cosmos DB. Jeśli masz już konto, którego chcesz użyć, możesz przejść od razu do kroku [Konfigurowanie aplikacji języka C++](#SetupC++).

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

## <a id="SetupC++"></a>Krok 2. Konfigurowanie aplikacji języka C++
1. Otwórz program Visual Studio, w menu **Plik** kliknij pozycję **Nowy**, a następnie kliknij pozycję **Projekt**. 
2. W oknie **Nowy projekt** w okienku **Zainstalowane** rozwiń węzeł **Visual C++**, kliknij pozycję **Win32**, a następnie kliknij pozycję **Aplikacja konsolowa Win32**. Nadaj projektowi nazwę hellodocumentdb, a następnie kliknij przycisk **OK**. 
   
    ![Zrzut ekranu przedstawiający kreatora nowego projektu](media/sql-api-cpp-get-started/hello.png)
3. Po uruchomieniu Kreatora aplikacji Win32 kliknij przycisk **Zakończ**.
4. Po utworzeniu projektu otwórz Menedżera pakietów NuGet, klikając prawym przyciskiem myszy projekt **hellodocumentdb** w **Eksploratorze rozwiązań**, a następnie klikając pozycję **Zarządzaj pakietami NuGet**. 
   
    ![Zrzut ekranu przedstawiający pozycję Zarządzaj pakietami NuGet w menu projektu](media/sql-api-cpp-get-started/nuget.png)
5. Na karcie **NuGet: hellodocumentdb** kliknij pozycję **Przeglądaj**, a następnie wyszukaj ciąg *documentdbcpp*. W wynikach wybierz DocumentDbCPP, jak pokazano na poniższym zrzucie ekranu:   
   
    ![Zrzut ekranu przedstawiający wyróżniony pakiet DocumentDbCpp](media/sql-api-cpp-get-started/cpp.png)
   
    Ten pakiet instaluje odwołania do zestawu SDK REST języka C++, który jest zależnością dla pakietu DocumentDbCPP. Po pakiety zostały dodane do projektu, masz wszystko gotowe, aby rozpocząć pisanie kodu.   

## <a id="Config"></a>Krok 3. Kopiowanie szczegółów połączenia z witryny Azure Portal dla bazy danych Azure Cosmos DB
Wywołaj [portalu Azure](https://portal.azure.com) i przejdź do utworzonego konta bazy danych Azure rozwiązania Cosmos. Potrzebujesz identyfikatora URI i klucz podstawowy z portalu Azure w następnym kroku do nawiązania połączenia z fragment kodu w języku C++. 

![Identyfikator URI usługi Azure Cosmos DB i klucze w witrynie Azure Portal](media/sql-api-cpp-get-started/nosql-tutorial-keys.png)

## <a id="Connect"></a>Krok 4. Łączenie się z kontem usługi Azure Cosmos DB
1. Po wierszu `#include "stdafx.h"` dodaj następujące nagłówki i przestrzenie nazw do kodu źródłowego.
   
        #include <cpprest/json.h>
        #include <documentdbcpp\DocumentClient.h>
        #include <documentdbcpp\exceptions.h>
        #include <documentdbcpp\TriggerOperation.h>
        #include <documentdbcpp\TriggerType.h>
        using namespace documentdb;
        using namespace std;
        using namespace web::json;
2. Następnie dodaj następujący kod do funkcji main oraz zastąp konfigurację konta i klucz podstawowy, aby dopasować ustawienia usługi Azure Cosmos DB z kroku 3. 
   
        DocumentDBConfiguration conf (L"<account_configuration_uri>", L"<primary_key>");
        DocumentClient client (conf);
   
    Teraz, gdy masz kod do zainicjowania klienta Spójrzmy w pracy z zasobami Azure DB rozwiązania Cosmos.

## <a id="CreateDBColl"></a>Krok 5. Tworzenie bazy danych i kolekcji w języku C++
Przed wykonaniem tego kroku, przejdź na interakcje bazy danych, kolekcji i dokumentów dla osób, które kto dopiero zaczynasz korzystać z bazy danych Azure rozwiązania Cosmos. [Baza danych](sql-api-resources.md#databases) jest kontenerem logicznym magazynu dokumentów podzielonym na kolekcje. [Kolekcja](sql-api-resources.md#collections) jest kontenerem dokumentów JSON i skojarzonej logiki aplikacji JavaScript. Więcej informacji o modelu zasobów hierarchicznych i pojęciach usługi Azure Cosmos DB znajduje się w temacie [Pojęcia i hierarchiczny model zasobów bazy danych Azure Cosmos DB](sql-api-resources.md).

Aby utworzyć bazę danych i odpowiednią kolekcję, dodaj następujący kod na końcu Twojej funkcji main. Spowoduje to utworzenie bazy danych o nazwie „FamilyRegistry” i kolekcji o nazwie „FamilyCollection” przy użyciu konfiguracji klienta zadeklarowanej w poprzednim kroku.

    try {
      shared_ptr<Database> db = client.CreateDatabase(L"FamilyRegistry");
      shared_ptr<Collection> coll = db->CreateCollection(L"FamilyCollection");
    } catch (DocumentDBRuntimeException ex) {
      wcout << ex.message();
    }


## <a id="CreateDoc"></a>Krok 6. Tworzenie dokumentu
[Dokumenty](sql-api-resources.md#documents) są zawartością JSON zdefiniowaną przez użytkownika (dowolną). Teraz można wstawić dokument do usługi Azure Cosmos DB. Dokument można utworzyć, kopiując następujący kod i wklejając go na końcu funkcji main. 

    try {
      value document_family;
      document_family[L"id"] = value::string(L"AndersenFamily");
      document_family[L"FirstName"] = value::string(L"Thomas");
      document_family[L"LastName"] = value::string(L"Andersen");
      shared_ptr<Document> doc = coll->CreateDocumentAsync(document_family).get();

      document_family[L"id"] = value::string(L"WakefieldFamily");
      document_family[L"FirstName"] = value::string(L"Lucy");
      document_family[L"LastName"] = value::string(L"Wakefield");
      doc = coll->CreateDocumentAsync(document_family).get();
    } catch (ResourceAlreadyExistsException ex) {
      wcout << ex.message();
    }

Podsumowując, ten kod tworzy bazy danych Azure rozwiązania Cosmos bazy danych, kolekcji i dokumentów, które można zbadać w Eksploratorze danych w portalu Azure. 

![Samouczek języka C++ — diagram pokazujący hierarchiczną relację między kontem, bazą danych, kolekcją i dokumentami](media/sql-api-cpp-get-started/docs.png)

## <a id="QueryDB"></a>Krok 7. Wykonanie zapytania względem zasobów usługi Azure Cosmos DB
Usługa Azure Cosmos DB obsługuje [zaawansowane zapytania](sql-api-sql-query.md) względem dokumentów JSON przechowywanych w każdej kolekcji. Następujący przykładowy kod przedstawia zapytanie zostało nawiązane przy użyciu składni SQL, który można uruchomić względem dokumentów utworzonej w poprzednim kroku.

Funkcja przyjmuje jako argumenty unikatowy identyfikator lub identyfikator zasobu dla bazy danych i kolekcji wraz z klientem dokumentu. Dodaj następujący kod przed funkcją main.

    void executesimplequery(const DocumentClient &client,
                            const wstring dbresourceid,
                            const wstring collresourceid) {
      try {
        client.GetDatabase(dbresourceid).get();
        shared_ptr<Database> db = client.GetDatabase(dbresourceid);
        shared_ptr<Collection> coll = db->GetCollection(collresourceid);
        wstring coll_name = coll->id();
        shared_ptr<DocumentIterator> iter =
            coll->QueryDocumentsAsync(wstring(L"SELECT * FROM " + coll_name)).get();
        wcout << "\n\nQuerying collection:";
        while (iter->HasMore()) {
          shared_ptr<Document> doc = iter->Next();
          wstring doc_name = doc->id();
          wcout << "\n\t" << doc_name << "\n";
          wcout << "\t"
                << "[{\"FirstName\":"
                << doc->payload().at(U("FirstName")).as_string()
                << ",\"LastName\":" << doc->payload().at(U("LastName")).as_string()
                << "}]";
        }
      } catch (DocumentDBRuntimeException ex) {
        wcout << ex.message();
      }
    }

## <a id="Replace"></a>Krok 8. Zastępowanie dokumentu
Usługa Azure Cosmos DB obsługuje zastępowanie dokumentów JSON, jak pokazano w poniższym kodzie. Dodaj ten kod po funkcji executesimplequery.

    void replacedocument(const DocumentClient &client, const wstring dbresourceid,
                         const wstring collresourceid,
                         const wstring docresourceid) {
      try {
        client.GetDatabase(dbresourceid).get();
        shared_ptr<Database> db = client.GetDatabase(dbresourceid);
        shared_ptr<Collection> coll = db->GetCollection(collresourceid);
        value newdoc;
        newdoc[L"id"] = value::string(L"WakefieldFamily");
        newdoc[L"FirstName"] = value::string(L"Lucy");
        newdoc[L"LastName"] = value::string(L"Smith Wakefield");
        coll->ReplaceDocument(docresourceid, newdoc);
      } catch (DocumentDBRuntimeException ex) {
        throw;
      }
    }

## <a id="Delete"></a>Krok 9. Usuwanie dokumentu
Usługa Azure Cosmos DB obsługuje usuwanie dokumentów JSON. W tym celu możesz skopiować następujący kod i wkleić go po funkcji replacedocument. 

    void deletedocument(const DocumentClient &client, const wstring dbresourceid,
                        const wstring collresourceid, const wstring docresourceid) {
      try {
        client.GetDatabase(dbresourceid).get();
        shared_ptr<Database> db = client.GetDatabase(dbresourceid);
        shared_ptr<Collection> coll = db->GetCollection(collresourceid);
        coll->DeleteDocumentAsync(docresourceid).get();
      } catch (DocumentDBRuntimeException ex) {
        wcout << ex.message();
      }
    }

## <a id="DeleteDB"></a>Krok 10. Usuwanie bazy danych
Usunięcie utworzonej bazy danych usunie bazę danych i wszystkie zasoby podrzędne (kolekcje, dokumenty itd.).

Skopiuj poniższy fragment kodu (funkcja cleanup) i wklej go po funkcji deletedocument, aby usunąć bazę danych i wszystkie zasoby podrzędne.

    void deletedb(const DocumentClient &client, const wstring dbresourceid) {
      try {
        client.DeleteDatabase(dbresourceid);
      } catch (DocumentDBRuntimeException ex) {
        wcout << ex.message();
      }
    }

## <a id="Run"></a>Krok 11. Uruchamianie całej aplikacji w języku C++!
Teraz można dodać kod, aby utworzyć, zapytania, modyfikowanie i usuwanie różnymi zasobami Azure DB rozwiązania Cosmos.  Teraz okablować to się przez dodanie wywołań tych różne funkcje z głównych funkcji hellodocumentdb.cpp oraz niektóre komunikaty diagnostyczne.

Możesz to zrobić, zastępując funkcję main Twojej aplikacji poniższym kodem. Nadpisuje to parametry account_configuration_uri i primary_key, które zostały skopiowane do kodu w kroku 3, więc zapisz ten wiersz lub ponownie skopiuj wartości z portalu. 

    int main() {
        try {
            // Start by defining your account's configuration
            DocumentDBConfiguration conf (L"<account_configuration_uri>", L"<primary_key>");
            // Create your client
            DocumentClient client(conf);
            // Create a new database
            try {
                shared_ptr<Database> db = client.CreateDatabase(L"FamilyDB");
                wcout << "\nCreating database:\n" << db->id();
                // Create a collection inside database
                shared_ptr<Collection> coll = db->CreateCollection(L"FamilyColl");
                wcout << "\n\nCreating collection:\n" << coll->id();
                value document_family;
                document_family[L"id"] = value::string(L"AndersenFamily");
                document_family[L"FirstName"] = value::string(L"Thomas");
                document_family[L"LastName"] = value::string(L"Andersen");
                shared_ptr<Document> doc =
                    coll->CreateDocumentAsync(document_family).get();
                wcout << "\n\nCreating document:\n" << doc->id();
                document_family[L"id"] = value::string(L"WakefieldFamily");
                document_family[L"FirstName"] = value::string(L"Lucy");
                document_family[L"LastName"] = value::string(L"Wakefield");
                doc = coll->CreateDocumentAsync(document_family).get();
                wcout << "\n\nCreating document:\n" << doc->id();
                executesimplequery(client, db->resource_id(), coll->resource_id());
                replacedocument(client, db->resource_id(), coll->resource_id(),
                    doc->resource_id());
                wcout << "\n\nReplaced document:\n" << doc->id();
                executesimplequery(client, db->resource_id(), coll->resource_id());
                deletedocument(client, db->resource_id(), coll->resource_id(),
                    doc->resource_id());
                wcout << "\n\nDeleted document:\n" << doc->id();
                deletedb(client, db->resource_id());
                wcout << "\n\nDeleted db:\n" << db->id();
                cin.get();
            }
            catch (ResourceAlreadyExistsException ex) {
                wcout << ex.message();
            }
        }
        catch (DocumentDBRuntimeException ex) {
            wcout << ex.message();
        }
        cin.get();
    }

Teraz powinna być możliwość skompilowania i uruchomienia kodu w programie Visual Studio po naciśnięciu klawisza F5 lub alternatywnie w oknie terminala, lokalizując aplikację, a następnie uruchamiając plik wykonywalny. 

Powinny zostać wyświetlone dane wyjściowe aplikacji rozpoczynania pracy. Dane wyjściowe powinny odpowiadać Poniższy zrzut ekranu:

![Dane wyjściowe aplikacji usługi Azure Cosmos DB w języku C++](media/sql-api-cpp-get-started/console.png)

Gratulacje! Udało Ci się ukończyć samouczek języka C++ i utworzyć swoją pierwszą aplikację konsolową usługi Azure Cosmos DB!

## <a id="GetSolution"></a>Pobieranie kompletnego rozwiązania samouczka języka C++
Aby skompilować rozwiązanie GetStarted, które zawiera wszystkie przykłady z tego artykułu, będą potrzebne następujące elementy:

* [Konto usługi Azure Cosmos DB][create-account].
* Rozwiązanie [GetStarted](https://github.com/stalker314314/DocumentDBCpp) dostępne w witrynie GitHub.

## <a name="next-steps"></a>Kolejne kroki
* Dowiedz się, jak [monitorować konto usługi Azure Cosmos DB](monitor-accounts.md).
* Wykonywania kwerend do przykładowego zestawu danych w [Plac zabaw dla zapytań](https://www.documentdb.com/sql/demo).
* Dowiedz się więcej o modelu programowania w sekcji Dla deweloperów [strony dokumentacji usługi Azure Cosmos DB](https://azure.microsoft.com/documentation/services/cosmos-db/).

[create-account]: create-sql-api-dotnet.md#create-account


