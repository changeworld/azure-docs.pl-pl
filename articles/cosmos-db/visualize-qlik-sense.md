---
title: Usługa Qlik Sense nawiązać połączenie z usługi Azure Cosmos DB i wizualizuj dane bazy danych
description: W tym artykule opisano kroki wymagane do nawiązywania połączenia usługi Azure Cosmos DB, aby usługa Qlik Sense i wizualizuj dane bazy danych.
ms.service: cosmos-db
author: SnehaGunda
ms.author: sngun
ms.topic: conceptual
ms.date: 05/23/2019
ms.reviewer: sngun
ms.openlocfilehash: eea4366ca19c01af1f79566a5ca6bcae8805b6c3
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/27/2019
ms.locfileid: "66242492"
---
# <a name="connect-qlik-sense-to-azure-cosmos-db-and-visualize-your-data"></a>Usługa Qlik Sense nawiązać połączenie z usługi Azure Cosmos DB i wizualizuj dane bazy danych

Usługa Qlik Sense jest narzędzie do wizualizacji danych, które łączy dane z różnych źródeł w jednym widoku. Usługa Qlik Sense indeksuje co możliwych relacji w danych, dzięki czemu możesz uzyskać natychmiastowy wgląd w dane. Możesz wizualizować dane usługi Azure Cosmos DB przy użyciu usługa Qlik Sense. W tym artykule opisano kroki wymagane do nawiązywania połączenia usługi Azure Cosmos DB, aby usługa Qlik Sense i wizualizuj dane bazy danych. 

> [!NOTE]
> Usługa Qlik Sense nawiązywania połączenia z usługi Azure Cosmos DB jest obecnie obsługiwana dla interfejsu API SQL i Azure Cosmos DB interfejsu API usługi MongoDB tylko dla kont.

Możesz połączyć usługa Qlik Sense do usługi Azure Cosmos DB przy użyciu:

* Cosmos DB interfejsu API SQL za pomocą łącznika ODBC.

* Interfejs API danych usługi Azure Cosmos DB dla bazy danych MongoDB przy użyciu łącznika Qlik Sense bazy danych MongoDB (obecnie w wersji zapoznawczej).

* Interfejs API danych usługi Azure Cosmos DB dla bazy danych MongoDB i interfejsu API SQL przy użyciu łącznika interfejsu API REST w usługa Qlik Sense.

* Mongo DB interfejsu API cosmos DB przy użyciu łącznika gRPC za rdzeń Qlik.
W tym artykule opisano szczegóły dotyczące nawiązywania połączenia z interfejsem API SQL usługi Cosmos DB przy użyciu łącznika ODBC.

W tym artykule opisano szczegóły dotyczące nawiązywania połączenia z interfejsem API SQL usługi Cosmos DB przy użyciu łącznika ODBC.

## <a name="prerequisites"></a>Wymagania wstępne

Przed wykonaniem instrukcji zawartych w tym artykule, upewnij się, że gotowy następujące zasoby:

* Pobierz [Qlik Sense pulpitu](https://www.qlik.com/us/try-or-buy/download-qlik-sense) lub skonfigurować usługa Qlik Sense na platformie Azure dzięki [instalowanie elementu portalu marketplace usługa Qlik Sense](https://azuremarketplace.microsoft.com/marketplace/apps/qlik.qlik-sense).

* Pobierz [dane gry wideo](https://www.kaggle.com/gregorut/videogamesales), te przykładowe dane są w formacie CSV. Będzie przechowywać te dane w ramach konta usługi Cosmos DB i zwizualizowana w usługa Qlik Sense.

* Tworzenie konta interfejsu API SQL usługi Azure Cosmos DB przy użyciu kroków opisanych w [Tworzenie konta usługi](create-sql-api-dotnet.md#create-account) sekcji z artykułem Szybki Start.

* [Utwórz bazę danych i kolekcji](create-sql-api-dotnet.md#create-collection-database) — możesz Użyj wartość można ustawić kolekcji przepływności do 1000 jednostek RU/s. 

* Ładowanie przykładowych danych sprzedaży gry wideo do swojego konta usługi Cosmos DB. Możesz importować dane przy użyciu narzędzia migracji danych usługi Azure Cosmos DB, możesz zrobić [sekwencyjne](import-data.md#SQLSeqTarget) lub [zbiorczego importu](import-data.md#SQLBulkTarget) danych. Trwa około 3 – 5 minut, zanim dane do zaimportowania do konta usługi Cosmos DB.

* Pobieranie, instalowanie i konfigurowanie sterownika ODBC wykonując kroki opisane w [połączyć z usługą Cosmos DB przy użyciu sterownika ODBC](odbc-driver.md) artykułu. Dane gry wideo jest prostym zestawem danych i nie trzeba edytować schemat, wystarczy użyć domyślny schemat mapowanie kolekcji.

## <a name="connect-qlik-sense-to-cosmos-db"></a>Usługa Qlik Sense nawiązać połączenie z usługi Cosmos DB

1. Usługa Qlik Sense Otwórz i wybierz **Utwórz nową aplikację**. Podaj nazwę aplikacji i wybierz **Utwórz**.

   ![Utwórz nową aplikację usługa Qlik Sense](./media/visualize-qlik-sense/create-new-qlik-sense-app.png)

2. Po pomyślnym utworzeniu nowej aplikacji wybierz **Otwórz aplikację** i wybierz polecenie **Dodawanie danych z plików i innych źródeł**. 

3. Ze źródeł danych wybierz **ODBC** otworzyć nowe okno Ustawienia połączenia. 

4. Przełącz się do **DSN użytkownika** i wybierz polecenie połączenia ODBC została utworzona wcześniej. Podaj nazwę połączenia i wybierz pozycję **Utwórz**. 

   ![Tworzenie nowego połączenia](./media/visualize-qlik-sense/create-new-connection.png)

5. Po utworzeniu połączenia, można wybrać bazę danych, kolekcji, w którym znajdują się dane gry wideo i Wyświetl jej podgląd.

   ![Wybierz bazę danych i kolekcję](./media/visualize-qlik-sense/choose-database-and-collection.png) 

6. Następnie wybierz pozycję **Dodawanie danych** ładowanie danych do usługa Qlik Sense. Po załadowaniu danych, aby usługa Qlik Sense można wygenerować szczegółowe informacje i wykonywanie analizy danych. Możesz używać szczegółowych danych lub utworzyć własną aplikację Eksplorowanie sprzedaży gry wideo. Na poniższej ilustracji przedstawiono 

   ![Wizualizowanie danych](./media/visualize-qlik-sense/visualize-data.png)

### <a name="limitations-when-connecting-with-odbc"></a>Ograniczenia w przypadku łączenia z ODBC 

Usługa cosmos DB jest rozproszoną bazę danych bez schematu ze sterownikami modelowane wokół potrzeb dla deweloperów. Sterownik ODBC wymaga bazę danych przy użyciu schematu w celu wnioskowanie kolumn, ich typy danych i inne właściwości. Regularne zapytanie SQL i składnia DML możliwości relacyjnych nie ma zastosowania do interfejsu API SQL usługi Cosmos DB, ponieważ interfejs API SQL nie jest ANSI SQL. Z tego powodu instrukcji SQL, wystawiane przez sterownik ODBC są tłumaczone na składni SQL specyficzne dla Cosmos DB, która nie ma odpowiedników dla wszystkich konstrukcji. Aby uniknąć tych problemów dotyczących tłumaczenia, należy najpierw zastosować schematu podczas konfigurowania połączenia ODBC. [Połączyć ze sterownikiem ODBC](odbc-driver.md) artykuł zawiera sugestie i metody, aby skonfigurował schemat. Upewnij się utworzyć to mapowanie dla każdej bazy danych/kolekcji w ramach konta usługi Cosmos DB.

## <a name="next-steps"></a>Następne kroki

Jeśli używasz narzędzia inny typ wizualizacji, takie jak Power BI można połączyć do niego zgodnie z instrukcjami podanymi w poniższej dokumentacji:

* [Wizualizacja danych usługi Cosmos DB za pomocą łącznika usługi Power BI](powerbi-visualize.md)
