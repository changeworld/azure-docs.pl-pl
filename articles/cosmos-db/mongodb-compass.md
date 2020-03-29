---
title: Łączenie się z usługą Azure Cosmos DB przy użyciu kompasu
description: Dowiedz się, jak używać kompasu MongoDB do przechowywania danych i zarządzania nimi w usłudze Azure Cosmos DB.
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: conceptual
ms.date: 03/20/2020
author: LuisBosquez
ms.author: lbosq
ms.openlocfilehash: 5a3ec79b27231f781b5e4104922993de38c7fe4f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80063673"
---
# <a name="use-mongodb-compass-to-connect-to-azure-cosmos-dbs-api-for-mongodb"></a>Łączenie się z interfejsem API usługi Azure Cosmos DB dla usługi MongoDB za pomocą kompasu MongoDB

W tym samouczku pokazano, jak używać [kompasu MongoDB](https://www.mongodb.com/products/compass) podczas przechowywania i/lub zarządzania danymi w usłudze Cosmos DB. Używamy interfejsu API usługi Azure Cosmos DB dla mongodb dla tego przejścia. Dla tych z was nieznanych, Compass jest GUI dla MongoDB. Jest często używany do wizualizacji danych, uruchamiania zapytań ad hoc, wraz z zarządzaniem danymi.

Cosmos DB to globalnie rozproszona wielomodelowa usługa bazy danych firmy Microsoft. Dzięki dystrybucji globalnej i możliwości skalowania poziomego w usłudze Cosmos DB możesz szybko tworzyć i za pomocą zapytań badać bazy danych dokumentów, par klucz/wartość oraz grafów.

## <a name="pre-requisites"></a>Wymagania wstępne

Aby połączyć się z kontem usługi Cosmos DB przy użyciu programu Robo 3T, należy:

* Pobierz i zainstaluj [kompas](https://www.mongodb.com/download-center/compass?jmp=hero)
* Mieć informacje o [ciągu połączenia usługi](connect-mongodb-account.md) Cosmos DB

## <a name="connect-to-cosmos-dbs-api-for-mongodb"></a>Połącz się z interfejsem API usługi Cosmos DB dla MongoDB

Aby połączyć swoje konto Usługi Cosmos DB z kompasem, wykonaj poniższe czynności:

1. Pobierz informacje o połączeniu dla konta usługi Cosmos skonfigurowane za pomocą usługi Azure Cosmos DB interfejsu API MongoDB przy użyciu instrukcji [tutaj](connect-mongodb-account.md).

    ![Zrzut ekranu przedstawiający blok ciągu połączenia](./media/mongodb-compass/mongodb-compass-connection.png)

2. Kliknij przycisk z napisem **Kopiuj do schowka** obok **ciągu połączenia Podstawowe/Pomocnicze** w usłudze Cosmos DB. Kliknięcie tego przycisku spowoduje skopiowanie całego ciągu połączenia do schowka.

    ![Zrzut ekranu przedstawiający przycisk kopiowania do schowka](./media/mongodb-compass/mongodb-connection-copy.png)

3. Otwórz Compass na pulpicie / komputerze i kliknij **Połącz,** a następnie **połącz się z ...**.

4. Kompas automatycznie wykryje ciąg połączenia w schowku i wyświetli monit o pytanie, czy chcesz go użyć do połączenia. Kliknij na **Tak,** jak pokazano na poniższym zrzucie ekranu.

    ![Zrzut ekranu przedstawiający monit kompasu o nawiązaniu połączenia](./media/mongodb-compass/mongodb-compass-detect.png)

5. Po kliknięciu **przycisku Tak** w powyższym kroku twoje dane z ciągu połączenia zostaną automatycznie wypełnione. Usuń wartość automatycznie wypełnioną w polu **Nazwa zestawu replik,** aby upewnić się, że pozostanie puste.

    ![Zrzut ekranu przedstawiający monit kompasu o nawiązaniu połączenia](./media/mongodb-compass/mongodb-compass-replica.png)

6. Kliknij **pozycję Połącz** u dołu strony. Twoje konto i bazy danych Usługi Cosmos DB powinny być teraz widoczne w kompasie MongoDB.

## <a name="next-steps"></a>Następne kroki

- Dowiedz się, jak [korzystać z programu Studio 3T](mongodb-mongochef.md) za pomocą interfejsu API usługi Azure Cosmos DB dla bazy danych MongoDB.
- Eksploruj [przykłady](mongodb-samples.md) bazy danych MongoDB za pomocą interfejsu API usługi Azure Cosmos DB dla bazy danych MongoDB.