---
title: Łączenie się z interfejsem API Azure Cosmos DB w usłudze MongoDB przy użyciu programu Studio 3T
description: Dowiedz się, jak nawiązać połączenie z interfejsem API Azure Cosmos DB MongoDB przy użyciu programu Studio 3T.
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: conceptual
ms.date: 11/01/2019
author: sivethe
ms.author: sivethe
ms.custom: seodec18
ms.openlocfilehash: 533917e4cc39a1f4885a1604c11480fe09870c61
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75441611"
---
# <a name="connect-to-an-azure-cosmos-account-using-studio-3t"></a>Nawiązywanie połączenia z kontem usługi Azure Cosmos za pomocą programu Studio 3T

Aby nawiązać połączenie z interfejsem API Azure Cosmos DB MongoDB za pomocą programu Studio 3T, należy:

* Pobierz i zainstaluj program [Studio 3T](https://studio3t.com/).
* Zawierają informacje o [parametrach połączenia](connect-mongodb-account.md) konta usługi Azure Cosmos.

> [!NOTE]
> Obecnie Robo 3T v 1.2 i Lower są obsługiwane z interfejsem API Cosmos DB dla MongoDB.

## <a name="create-the-connection-in-studio-3t"></a>Tworzenie połączenia w z programu Studio 3T

Aby dodać konto usługi Azure Cosmos do Menedżera połączeń programu Studio 3T, wykonaj następujące czynności:

1. Pobierz informacje o połączeniu dla interfejsu API Azure Cosmos DB dla konta MongoDB, korzystając z instrukcji w temacie [Connect a MongoDB Application to Azure Cosmos DB](connect-mongodb-account.md) article.

    ![Zrzut ekranu strony parametrów połączenia](./media/mongodb-mongochef/ConnectionStringBlade.png)

2. Kliknij przycisk **Connect** , aby otworzyć Menedżera połączeń, a następnie przycisk **nowe połączenie**

    ![Zrzut ekranu Menedżera połączeń programu Studio 3T](./media/mongodb-mongochef/ConnectionManager.png)
3. W oknie **nowe połączenie** na karcie **serwer** wprowadź nazwę hosta (FQDN) konta usługi Azure Cosmos i port.

    ![Zrzut ekranu przedstawiający kartę serwer Menedżera połączeń programu Studio 3T](./media/mongodb-mongochef/ConnectionManagerServerTab.png)
4. W **nowe połączenie** okna na **uwierzytelniania** karty, wybierz tryb uwierzytelniania **Basic (CR bazy danych MONGODB lub SCARM-SHA-1)** i wprowadź nazwę użytkownika i hasło.  Zaakceptuj uwierzytelniania domyślnej bazy danych (Administrator) lub podać własne wartości.

    ![Zrzut ekranu karty uwierzytelnianie Menedżera połączeń programu Studio 3T](./media/mongodb-mongochef/ConnectionManagerAuthenticationTab.png)
5. W **nowe połączenie** okna na **SSL** karcie wyboru **Użyj protokołu SSL do łączenia z** pole wyboru i **akceptować certyfikaty SSL z podpisem własnym serwera**  przycisku radiowego.

    ![Zrzut ekranu przedstawiający kartę SSL Menedżera połączeń programu Studio 3T](./media/mongodb-mongochef/ConnectionManagerSSLTab.png)
6. Kliknij przycisk **Testuj połączenie** przycisk, aby zweryfikować informacje o połączeniu, kliknij przycisk **OK** wróć do okna nowe połączenie, a następnie kliknij przycisk **Zapisz**.

    ![Zrzut ekranu przedstawiający okno połączenia testowego programu Studio 3T](./media/mongodb-mongochef/TestConnectionResults.png)

## <a name="use-studio-3t-to-create-a-database-collection-and-documents"></a>Umożliwia tworzenie bazy danych, kolekcji i dokumentów z programu Studio 3T
Aby utworzyć bazę danych, kolekcję i dokumenty przy użyciu z programu Studio 3T, wykonaj następujące czynności:

1. W **Menedżera połączeń**, zaznacz połączenie i kliknij **Connect**.

    ![Zrzut ekranu Menedżera połączeń programu Studio 3T](./media/mongodb-mongochef/ConnectToAccount.png)
2. Kliknij prawym przyciskiem myszy hosta, a następnie wybierz **Dodaj bazę danych**.  Podaj nazwę bazy danych, a następnie kliknij przycisk **OK**.

    ![Zrzut ekranu opcji Dodaj bazę danych programu Studio 3T](./media/mongodb-mongochef/AddDatabase1.png)
3. Kliknij prawym przyciskiem myszy bazę danych, a następnie wybierz **Dodaj kolekcję**.  Podaj nazwę kolekcji, a następnie kliknij przycisk **Utwórz**.

    ![Zrzut ekranu przedstawiający opcję dodawania kolekcji programu Studio 3T](./media/mongodb-mongochef/AddCollection.png)
4. Kliknij przycisk **kolekcji** menu elementów, następnie kliknij przycisk **Dodawanie dokumentu**.

    ![Zrzut ekranu przedstawiający element menu Dodawanie dokumentu programu Studio 3T](./media/mongodb-mongochef/AddDocument1.png)
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

    ![Zrzut ekranu przedstawiający wyniki zapytania Mongo Chef](./media/mongodb-mongochef/QueryDocument1.png)

## <a name="next-steps"></a>Następne kroki

- Dowiedz się, jak [korzystać z programu Robo 3T](mongodb-robomongo.md) za pomocą interfejsu API usługi Azure Cosmos DB dla bazy danych MongoDB.
- Eksploruj [przykłady](mongodb-samples.md) bazy danych MongoDB za pomocą interfejsu API usługi Azure Cosmos DB dla bazy danych MongoDB.
