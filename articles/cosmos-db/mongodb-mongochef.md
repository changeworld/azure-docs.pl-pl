---
title: Korzystać z programu Studio 3T (z programu MongoChef) przy użyciu usługi Azure Cosmos DB | Dokumentacja firmy Microsoft
description: Dowiedz się, jak korzystać z programu Studio 3T przy użyciu konta z interfejsem API MongoDB usługi Azure Cosmos DB
keywords: mongochef, studio 3T
services: cosmos-db
author: slyons
manager: kfile
ms.service: cosmos-db
ms.component: cosmosdb-mongo
ms.devlang: na
ms.topic: conceptual
ms.date: 01/18/2018
ms.author: sclyon
ms.openlocfilehash: af66f413055c01d9a4c3e94c7d3b9c5f1d5917b0
ms.sourcegitcommit: 387d7edd387a478db181ca639db8a8e43d0d75f7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/10/2018
ms.locfileid: "42055497"
---
# <a name="azure-cosmos-db-use-studio-3t-with-a-mongodb-api-account"></a>Azure Cosmos DB: Z programu użyj programu Studio 3T przy użyciu konta interfejsu API usługi MongoDB

Aby połączyć konto z interfejsem API MongoDB usługi Azure Cosmos DB, musisz mieć:

* Pobierz i zainstaluj [z programu Studio 3T](https://studio3t.com/) (wcześniej znane jako z programu MongoChef)
* Masz usługi Azure Cosmos DB [parametry połączenia](connect-mongodb-account.md) informacje dotyczące Twojego konta bazy danych MongoDB

## <a name="create-the-connection-in-studio-3t"></a>Tworzenie połączenia w z programu Studio 3T
Aby dodać konto usługi Azure Cosmos DB do Menedżera połączeń z programu 3T Studio, wykonaj następujące czynności:

1. Pobierz informacje o połączeniu usługi Azure Cosmos DB dla swojego konta interfejsu API usługi MongoDB, zgodnie z instrukcjami przedstawionymi w [połączyć aplikację MongoDB w usłudze Azure Cosmos DB](connect-mongodb-account.md) artykułu.

    ![Zrzut ekranu strony parametrów połączenia](./media/mongodb-mongochef/ConnectionStringBlade.png)
2. Kliknij przycisk **Connect** , aby otworzyć Menedżera połączeń, a następnie przycisk **nowe połączenie**

    ![Zrzut ekranu przedstawiający Menedżera połączeń z programu 3T Studio](./media/mongodb-mongochef/ConnectionManager.png)
3. W **nowe połączenie** okna na **serwera** wprowadź hosta (FQDN) konta usługi Azure Cosmos DB i numer portu.

    ![Zrzut ekranu przedstawiający kartę Studio z programu 3T połączenie Menedżera serwera](./media/mongodb-mongochef/ConnectionManagerServerTab.png)
4. W **nowe połączenie** okna na **uwierzytelniania** karty, wybierz tryb uwierzytelniania **Basic (CR bazy danych MONGODB lub SCARM-SHA-1)** i wprowadź nazwę użytkownika i hasło.  Zaakceptuj uwierzytelniania domyślnej bazy danych (Administrator) lub podać własne wartości.

    ![Zrzut ekranu przedstawiający kartę uwierzytelnianie Menedżera połączeń z programu 3T Studio](./media/mongodb-mongochef/ConnectionManagerAuthenticationTab.png)
5. W **nowe połączenie** okna na **SSL** karcie wyboru **Użyj protokołu SSL do łączenia z** pole wyboru i **akceptować certyfikaty SSL z podpisem własnym serwera**  przycisku radiowego.

    ![Zrzut ekranu: karta SSL Menedżera połączeń programu Studio z programu 3T](./media/mongodb-mongochef/ConnectionManagerSSLTab.png)
6. Kliknij przycisk **Testuj połączenie** przycisk, aby zweryfikować informacje o połączeniu, kliknij przycisk **OK** wróć do okna nowe połączenie, a następnie kliknij przycisk **Zapisz**.

    ![Zrzut ekranu przedstawiający okno połączenia testowego z programu 3T Studio](./media/mongodb-mongochef/TestConnectionResults.png)

## <a name="use-studio-3t-to-create-a-database-collection-and-documents"></a>Umożliwia tworzenie bazy danych, kolekcji i dokumentów z programu Studio 3T
Aby utworzyć bazę danych, kolekcję i dokumenty przy użyciu z programu Studio 3T, wykonaj następujące czynności:

1. W **Menedżera połączeń**, zaznacz połączenie i kliknij **Connect**.

    ![Zrzut ekranu przedstawiający Menedżera połączeń z programu 3T Studio](./media/mongodb-mongochef/ConnectToAccount.png)
2. Kliknij prawym przyciskiem myszy hosta, a następnie wybierz **Dodaj bazę danych**.  Podaj nazwę bazy danych, a następnie kliknij przycisk **OK**.

    ![Zrzut ekranu przedstawiający opcję Dodaj bazę danych z programu 3T Studio](./media/mongodb-mongochef/AddDatabase1.png)
3. Kliknij prawym przyciskiem myszy bazę danych, a następnie wybierz **Dodaj kolekcję**.  Podaj nazwę kolekcji, a następnie kliknij przycisk **Utwórz**.

    ![Zrzut ekranu przedstawiający opcję Dodaj kolekcję z programu 3T Studio](./media/mongodb-mongochef/AddCollection.png)
4. Kliknij przycisk **kolekcji** menu elementów, następnie kliknij przycisk **Dodawanie dokumentu**.

    ![Zrzut ekranu przedstawiający element menu Dodaj dokument z programu 3T programu Studio](./media/mongodb-mongochef/AddDocument1.png)
5. W oknie dialogowym Dodawanie dokumentu, wklej następujący kod, a następnie kliknij przycisk **Dodawanie dokumentu**.

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
6. Dodawanie innego dokumentu, tym razem z następującą zawartością:

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
7. Wykonywanie przykładowego zapytania. Na przykład wyszukaj rodziny o nazwisku "Andersen" i zwrócić pól Stan i elementy nadrzędne.

    ![Zrzut ekranu przedstawiający wyniki zapytania programu Chef Mongo](./media/mongodb-mongochef/QueryDocument1.png)

## <a name="next-steps"></a>Kolejne kroki
* Zapoznaj się z interfejsem API MongoDB usługi Azure Cosmos DB [przykłady](mongodb-samples.md).
