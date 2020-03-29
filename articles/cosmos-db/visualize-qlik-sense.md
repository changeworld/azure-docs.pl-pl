---
title: Łączenie aplikacji Qlik Sense z usługą Azure Cosmos DB i wizualizowanie danych
description: W tym artykule opisano kroki wymagane do połączenia usługi Azure Cosmos DB z Qlik Sense i wizualizacji danych.
ms.service: cosmos-db
author: SnehaGunda
ms.author: sngun
ms.topic: conceptual
ms.date: 05/23/2019
ms.reviewer: sngun
ms.openlocfilehash: 3a955060eb5f19544860c1c97abe1577084bef24
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "67985542"
---
# <a name="connect-qlik-sense-to-azure-cosmos-db-and-visualize-your-data"></a>Łączenie aplikacji Qlik Sense z usługą Azure Cosmos DB i wizualizowanie danych

Qlik Sense to narzędzie do wizualizacji danych, które łączy dane z różnych źródeł w jeden widok. Qlik Sense indeksuje wszystkie możliwe relacje w danych, dzięki czemu można uzyskać natychmiastowy wgląd w dane. Możesz wizualizować dane usługi Azure Cosmos DB przy użyciu Qlik Sense. W tym artykule opisano kroki wymagane do połączenia usługi Azure Cosmos DB z Qlik Sense i wizualizacji danych. 

> [!NOTE]
> Łączenie qlik sense do usługi Azure Cosmos DB jest obecnie obsługiwane dla interfejsu API SQL i interfejsu API usługi Azure Cosmos DB tylko dla kont MongoDB.

Program Qlik Sense można połączyć z usługą Azure Cosmos DB za pomocą:

* Interfejs API SQL usługi Cosmos DB przy użyciu łącznika ODBC.

* Interfejs API usługi Azure Cosmos DB dla bazy danych MongoDB przy użyciu łącznika Qlik Sense MongoDB (obecnie w wersji zapoznawczej).

* Interfejs API usługi Azure Cosmos DB dla mongodb i interfejsu API SQL przy użyciu łącznika interfejsu API REST w języku Qlik Sense.

* Interfejs API bazy danych Cosmos DB Mongo przy użyciu złącza gRPC dla Qlik Core.
W tym artykule opisano szczegóły łączenia się z interfejsem API SQL usługi Cosmos DB przy użyciu łącznika ODBC.

W tym artykule opisano szczegóły łączenia się z interfejsem API SQL usługi Cosmos DB przy użyciu łącznika ODBC.

## <a name="prerequisites"></a>Wymagania wstępne

Przed postępem w tym artykule upewnij się, że masz gotowe następujące zasoby:

* Pobierz [qlik sense desktop](https://www.qlik.com/us/try-or-buy/download-qlik-sense) lub skonfiguruj Qlik Sense na platformie [Azure, instalując element qlik sense marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/qlik.qlik-sense).

* Pobierz [dane z gier wideo](https://www.kaggle.com/gregorut/videogamesales), te przykładowe dane są w formacie CSV. Dane te będą przechowywane na koncie usługi Cosmos DB i wizualizować w Qlik Sense.

* Utwórz konto interfejsu API sql usługi Azure Cosmos DB przy użyciu kroków opisanych w sekcji [tworzenia konta](create-sql-api-dotnet.md#create-account) w artykule szybki start.

* [Tworzenie bazy danych i kolekcji](create-sql-api-java.md#add-a-container) — można użyć ustawić wartość przepływności kolekcji do 1000 RU/s. 

* Załaduj przykładowe dane sprzedaży gier wideo do konta Usługi Cosmos DB. Dane można zaimportować za pomocą narzędzia do migracji danych usługi Azure Cosmos DB, można wykonać [sekwencyjny](import-data.md#SQLSeqTarget) lub [zbiorczy import](import-data.md#SQLBulkTarget) danych. Trwa około 3-5 minut dla danych do zaimportowania do konta usługi Cosmos DB.

* Pobierz, zainstaluj i skonfiguruj sterownik ODBC, wykonując kroki opisane w [artykule "Łącząc się z bazą danych usługi Cosmos DB" ze sterownikiem ODBC.](odbc-driver.md) Dane gier wideo to prosty zestaw danych i nie trzeba edytować schematu, wystarczy użyć domyślnego schematu mapowania kolekcji.

## <a name="connect-qlik-sense-to-cosmos-db"></a>Łączenie Qlik Sense z cosmos DB

1. Otwórz Qlik Sense i wybierz **pozycję Utwórz nową aplikację**. Podaj nazwę aplikacji i wybierz pozycję **Utwórz**.

   ![Tworzenie nowej aplikacji Qlik Sense](./media/visualize-qlik-sense/create-new-qlik-sense-app.png)

2. Po pomyślnym utworzeniu nowej aplikacji wybierz pozycję **Otwórz aplikację** i wybierz pozycję Dodaj dane z plików i **innych źródeł**. 

3. Ze źródeł danych wybierz **odbc,** aby otworzyć nowe okno konfiguracji połączenia. 

4. Przełącz się do **nazwy DSN użytkownika** i wybierz połączenie ODBC utworzone wcześniej. Podaj nazwę połączenia i wybierz pozycję **Utwórz**. 

   ![Tworzenie nowego połączenia](./media/visualize-qlik-sense/create-new-connection.png)

5. Po utworzeniu połączenia można wybrać bazę danych, kolekcję, w której znajdują się dane gry wideo, a następnie wyświetlić podgląd.

   ![Wybierz bazę danych i kolekcję](./media/visualize-qlik-sense/choose-database-and-collection.png) 

6. Następnie wybierz **pozycję Dodaj dane,** aby załadować dane do Qlik Sense. Po załadowaniu danych do Qlik Sense można generować szczegółowe informacje i przeprowadzać analizę danych. Możesz użyć szczegółowych informacji lub utworzyć własną aplikację eksplorującą sprzedaż gier wideo. Na poniższej ilustracji przedstawiono 

   ![Wizualizowanie danych](./media/visualize-qlik-sense/visualize-data.png)

### <a name="limitations-when-connecting-with-odbc"></a>Ograniczenia podczas łączenia się z ODBC 

Cosmos DB to rozproszona baza danych bez schematu ze sterownikami modelowanymi wokół potrzeb deweloperów. Sterownik ODBC wymaga bazy danych ze schematem do wnioskowania kolumn, ich typów danych i innych właściwości. Zwykła kwerenda SQL lub składnia DML z możliwością relacyjną nie ma zastosowania do interfejsu API SQL usługi Cosmos DB, ponieważ interfejs API SQL nie jest ansi SQL. Z tego powodu instrukcje SQL wydane za pośrednictwem sterownika ODBC są tłumaczone na składnię SQL specyficzną dla usługi Cosmos DB, która nie ma odpowiedników dla wszystkich konstrukcji. Aby zapobiec tym problemom z tłumaczeniem, należy zastosować schemat podczas konfigurowania połączenia ODBC. Połącz [się ze sterownikiem ODBC](odbc-driver.md) artykuł zawiera sugestie i metody, które pomogą Ci skonfigurować schemat. Upewnij się, aby utworzyć to mapowanie dla każdej bazy danych/kolekcji w ramach konta usługi Cosmos DB.

## <a name="next-steps"></a>Następne kroki

Jeśli używasz innego narzędzia do wizualizacji, takiego jak usługa Power BI, możesz się z nim połączyć, korzystając z instrukcji podanych w poniższym doc:

* [Wizualizuj dane usługi Cosmos DB przy użyciu łącznika usługi Power BI](powerbi-visualize.md)
