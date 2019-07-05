---
title: Połączyć z usługą Azure Cosmos DB przy użyciu Compass
description: Dowiedz się, jak używać Compass bazy danych MongoDB do przechowywania danych i zarządzanie nimi w usłudze Azure Cosmos DB.
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: overview
ms.date: 06/24/2019
author: roaror
ms.author: roaror
ms.openlocfilehash: ad302df3144787fd231166c7052ecfde268f4086
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/28/2019
ms.locfileid: "67451038"
---
# <a name="use-compass-to-connect-to-your-cosmos-db-account"></a>Nawiązywanie połączenia z kontem usługi Cosmos DB za pomocą Compass

W tym samouczku przedstawiono sposób użycia [Compass bazy danych MongoDB](https://www.mongodb.com/products/compass) podczas przechowywania i/lub zarządzanie danymi w usłudze Cosmos DB. Używamy interfejsu API usługi Azure Cosmos DB dla bazy danych MongoDB na potrzeby tego przewodnika. Dla osób, nieznane Compass to graficzny interfejs użytkownika dla bazy danych MongoDB. Często służy do wizualizacji danych, uruchamianie zapytań ad hoc, oraz zarządzanie danymi. 

Usługa cosmos DB to usługa globalnie dystrybuowana, wielomodelowa baza danych firmy Microsoft. Dzięki dystrybucji globalnej i możliwości skalowania poziomego w usłudze Cosmos DB możesz szybko tworzyć i za pomocą zapytań badać bazy danych dokumentów, par klucz/wartość oraz grafów.


## <a name="pre-requisites"></a>Wymagania wstępne 
Aby nawiązać połączenie z kontem usługi Cosmos DB, za pomocą Robo z programu 3T, musisz mieć:

* Pobierz i zainstaluj [Compass](https://www.mongodb.com/download-center/compass?jmp=hero)
* Masz usługi Cosmos DB [parametry połączenia](connect-mongodb-account.md) informacji

## <a name="connect-to-cosmos-dbs-api-for-mongodb"></a>Łączenie do interfejsu API usługi Cosmos DB dla bazy danych MongoDB 
Aby połączyć konto usługi Cosmos DB Compass, wykonaj następujące czynności:

1. Pobierz informacje o połączeniu dla konta usługi Cosmos skonfigurowane za pomocą usługi Azure Cosmos DB interfejsu API MongoDB przy użyciu instrukcji [tutaj](connect-mongodb-account.md).

    ![Zrzut ekranu przedstawiający blok parametrów połączenia](./media/mongodb-compass/mongodb-compass-connection.png)

2. Kliknij przycisk z napisem **Kopiuj do Schowka** obok Twojej **podstawowe i pomocnicze parametry połączenia** w usłudze Cosmos DB. Kliknięcie tego przycisku skopiuj parametry połączenia całego do Schowka. 

    ![Zrzut ekranu przedstawiający Kopiuj do Schowka, przycisk](./media/mongodb-compass/mongodb-connection-copy.png)

3. Otwórz Compass na pulpicie/maszynie i kliknij **Connect** i następnie **nawiązać połączenie...** . 

4. Compass automatycznie wykryje połączenie ciągów w Schowku i będzie monitować o zapytaj, czy chcesz go użyć do połączenia. Kliknij pozycję **tak** jak pokazano na poniższym zrzucie ekranu.

    ![Zrzut ekranu przedstawiający monit dotyczący Compass połączyć](./media/mongodb-compass/mongodb-compass-detect.png)

5. Po kliknięciu **tak** w kroku powyżej swoje dane z parametrów połączenia zostaną wypełnione automatycznie. Usuń wartość automatycznie wypełnione w **Nazwa zestawu replik** pole, aby upewnić się, to znaczy pozostać puste. 

    ![Zrzut ekranu przedstawiający monit dotyczący Compass połączyć](./media/mongodb-compass/mongodb-compass-replica.png)

6. Kliknij pozycję **Connect** w dolnej części strony. Twoje konto usługi Cosmos DB i bazy danych powinno być teraz widoczny w obrębie Compass bazy danych MongoDB.

## <a name="next-steps"></a>Kolejne kroki

- Dowiedz się, jak [korzystać z programu Studio 3T](mongodb-mongochef.md) za pomocą interfejsu API usługi Azure Cosmos DB dla bazy danych MongoDB.
- Eksploruj [przykłady](mongodb-samples.md) bazy danych MongoDB za pomocą interfejsu API usługi Azure Cosmos DB dla bazy danych MongoDB.