---
title: Użyj Studio 3T (MongoChef) z rozwiązania Cosmos Azure DB | Dokumentacja firmy Microsoft
description: Dowiedz się, jak użyć Studio 3T przy użyciu konta interfejsu API Azure rozwiązania Cosmos bazy danych MongoDB
keywords: mongochef, studio 3T
services: cosmos-db
author: SnehaGunda
manager: kfile
ms.service: cosmos-db
ms.component: cosmosdb-mongo
ms.devlang: na
ms.topic: conceptual
ms.date: 01/18/2018
ms.author: sngun
ms.openlocfilehash: 9d00f260252e31ab9186c8aab8f07db848d1af29
ms.sourcegitcommit: 6116082991b98c8ee7a3ab0927cf588c3972eeaa
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/05/2018
ms.locfileid: "34794774"
---
# <a name="azure-cosmos-db-use-studio-3t-with-a-mongodb-api-account"></a>Azure DB rozwiązania Cosmos: Użyj Studio 3T przy użyciu konta bazy danych MongoDB interfejsu API

Aby połączyć konto interfejsu API Azure rozwiązania Cosmos bazy danych MongoDB, należy:

* Pobierz i zainstaluj [3T w Studio](https://studio3t.com/) (wcześniej znane jako MongoChef)
* Ma bazy danych programu Azure rozwiązania Cosmos [ciąg połączenia](connect-mongodb-account.md) informacji dotyczących Twojego konta bazy danych MongoDB

## <a name="create-the-connection-in-studio-3t"></a>Utwórz połączenie w 3T w Studio
Aby dodać konto bazy danych Azure rozwiązania Cosmos Menedżera połączeń 3T w Studio, wykonaj następujące czynności:

1. Pobrać informacji dotyczących połączenia bazy danych Azure rozwiązania Cosmos dla swojego konta bazy danych MongoDB interfejsu API przy użyciu instrukcji w [połączyć aplikację bazy danych MongoDB do bazy danych Azure rozwiązania Cosmos](connect-mongodb-account.md) artykułu.

    ![Zrzut ekranu strony ciąg połączenia](./media/mongodb-mongochef/ConnectionStringBlade.png)
2. Kliknij przycisk **Connect** aby otworzyć Menedżera połączeń, a następnie przycisk **nowego połączenia**

    ![Zrzut ekranu przedstawiający Menedżera połączeń 3T w Studio](./media/mongodb-mongochef/ConnectionManager.png)
3. W **nowe połączenie** okna na **serwera** wprowadź hosta (FQDN) konta bazy danych Azure rozwiązania Cosmos oraz numer portu.

    ![Zrzut ekranu przedstawiający kartę Studio 3T połączenie Menedżera serwera](./media/mongodb-mongochef/ConnectionManagerServerTab.png)
4. W **nowe połączenie** okna na **uwierzytelniania** , wybierz pozycję tryb uwierzytelniania **Basic (CR bazy danych MONGODB lub SCARM-SHA-1)** , a następnie wprowadź nazwę użytkownika i hasło.  Zaakceptuj domyślną db uwierzytelniania (Administrator) lub podać własne wartości.

    ![Zrzut ekranu: karta Studio 3T połączenie Menedżera uwierzytelniania](./media/mongodb-mongochef/ConnectionManagerAuthenticationTab.png)
5. W **nowe połączenie** okna na **SSL** karcie wyboru **protokołu SSL używany nawiązać** pole wyboru i **akceptować certyfikaty SSL z podpisem własnym serwera**  przycisk radiowy.

    ![Zrzut ekranu przedstawiający kartę Studio 3T połączenie Menedżera protokołu SSL](./media/mongodb-mongochef/ConnectionManagerSSLTab.png)
6. Kliknij przycisk **Testuj połączenie** przycisk, aby sprawdzić informacje o połączeniu, kliknij przycisk **OK** powrócić do okna nowe połączenie, a następnie kliknij przycisk **zapisać**.

    ![Zrzut ekranu przedstawiający okno Studio 3T testu połączenia](./media/mongodb-mongochef/TestConnectionResults.png)

## <a name="use-studio-3t-to-create-a-database-collection-and-documents"></a>Użyj Studio 3T, aby utworzyć bazę danych, kolekcji i dokumentów
Aby utworzyć bazę danych, kolekcji i dokumentów za pomocą Studio 3T, wykonaj następujące czynności:

1. W **Menedżera połączeń**, zaznacz połączenie i kliknij **Connect**.

    ![Zrzut ekranu przedstawiający Menedżera połączeń 3T w Studio](./media/mongodb-mongochef/ConnectToAccount.png)
2. Kliknij prawym przyciskiem myszy hosta, a następnie wybierz pozycję **dodawania bazy danych**.  Podaj nazwę bazy danych, a następnie kliknij przycisk **OK**.

    ![Zrzut ekranu opcji dodawania bazy danych 3T w Studio](./media/mongodb-mongochef/AddDatabase1.png)
3. Kliknij prawym przyciskiem myszy bazę danych i wybierz polecenie **Dodaj kolekcji**.  Podaj nazwę kolekcji, a następnie kliknij przycisk **Utwórz**.

    ![Zrzut ekranu opcji Dodaj kolekcji 3T w Studio](./media/mongodb-mongochef/AddCollection.png)
4. Kliknij przycisk **kolekcji** menu elementu, następnie kliknij przycisk **Dodawanie dokumentu**.

    ![Zrzut ekranu przedstawiający element menu Dodaj dokumentu 3T Studio](./media/mongodb-mongochef/AddDocument1.png)
5. W oknie dialogowym Dodawanie dokumentu, wklej następujący, a następnie kliknij przycisk **Dodawanie dokumentu**.

        {
        "_id": "AndersenFamily",
        "lastName": "Andersen",
        "parents": [
               { "firstName": "Thomas" },
               { "firstName": "Mary Kay"}
        ],
        "children": [
           {
               "firstName": "Henriette Thaulow", "gender": "female", "grade": 5,
               "pets": [{ "givenName": "Fluffy" }]
           }
        ],
        "address": { "state": "WA", "county": "King", "city": "seattle" },
        "isRegistered": true
        }
6. Dodawanie innego dokumentu teraz o następującej treści:

        {
        "_id": "WakefieldFamily",
        "parents": [
            { "familyName": "Wakefield", "givenName": "Robin" },
            { "familyName": "Miller", "givenName": "Ben" }
        ],
        "children": [
            {
                "familyName": "Merriam",
                 "givenName": "Jesse",
                "gender": "female", "grade": 1,
                "pets": [
                    { "givenName": "Goofy" },
                    { "givenName": "Shadow" }
                ]
            },
            {
                "familyName": "Miller",
                 "givenName": "Lisa",
                 "gender": "female",
                 "grade": 8 }
        ],
        "address": { "state": "NY", "county": "Manhattan", "city": "NY" },
        "isRegistered": false
        }
7. Wykonaj przykładowe zapytanie. Na przykład wyszukaj o nazwisko "Andersen" i zwróć pola stanu i elementów nadrzędnych.

    ![Zrzut ekranu przedstawiający Mongo Chef wyników zapytania](./media/mongodb-mongochef/QueryDocument1.png)

## <a name="next-steps"></a>Kolejne kroki
* Eksploruj interfejsu API Azure rozwiązania Cosmos bazy danych MongoDB [przykłady](mongodb-samples.md).
