---
title: Łączenie się z interfejsem API usługi Azure Cosmos DB za pomocą programu Studio 3T dla usługi MongoDB
description: Dowiedz się, jak połączyć się z interfejsem API usługi Azure Cosmos DB dla mongodb przy użyciu studio 3T.
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: conceptual
ms.date: 03/20/2020
author: timsander1
ms.author: tisande
ms.custom: seodec18
ms.openlocfilehash: 84b703cceeb130b177b8ab32281ef616b1ec632b
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/02/2020
ms.locfileid: "80548836"
---
# <a name="connect-to-an-azure-cosmos-account-using-studio-3t"></a>Łączenie się z kontem usługi Azure Cosmos przy użyciu programu Studio 3T

Aby połączyć się z interfejsem API usługi Azure Cosmos DB dla mongodb przy użyciu studio 3T, należy:

* Pobierz i zainstaluj [Studio 3T](https://studio3t.com/).
* Mieć informacje o ciągu [połączenia](connect-mongodb-account.md) konta usługi Azure Cosmos.

## <a name="create-the-connection-in-studio-3t"></a>Tworzenie połączenia w Studio 3T

Aby dodać konto usługi Azure Cosmos do menedżera połączeń Studio 3T, należy wykonać następujące kroki:

1. Pobierz informacje o połączeniu dla interfejsu API usługi Azure Cosmos DB dla konta MongoDB przy użyciu instrukcji w [artykule Połącz aplikację MongoDB z usługą Azure Cosmos DB.](connect-mongodb-account.md)

    ![Zrzut ekranu przedstawiający stronę ciągu połączenia](./media/mongodb-mongochef/ConnectionStringBlade.png)

2. Kliknij **pozycję Połącz,** aby otworzyć Menedżera połączeń, a następnie kliknij pozycję **Nowe połączenie**

    ![Zrzut ekranu przedstawiający menedżera połączeń Studio 3T](./media/mongodb-mongochef/ConnectionManager.png)
3. W oknie **Nowe połączenie** na karcie **Serwer** wprowadź host (FQDN) konta usługi Azure Cosmos i port.

    ![Zrzut ekranu przedstawiający kartę serwera menedżera połączeń Studio 3T](./media/mongodb-mongochef/ConnectionManagerServerTab.png)
4. W oknie **Nowe połączenie** na karcie **Uwierzytelnianie** wybierz pozycję Tryb uwierzytelniania **podstawowy (MONGODB-CR lub SCARM-SHA-1)** i wprowadź nazwę użytkownika i hasło.  Zaakceptuj domyślną bazę danych uwierzytelniania (admin) lub podaj własną wartość.

    ![Zrzut ekranu przedstawiający kartę uwierzytelniania menedżera połączeń Studio 3T](./media/mongodb-mongochef/ConnectionManagerAuthenticationTab.png)
5. W oknie **Nowe połączenie** na karcie **SSL** zaznacz pole wyboru **Użyj protokołu SSL do połączenia** i przycisk opcji Akceptuj **certyfikaty SSL z podpisem serwera.**

    ![Zrzut ekranu przedstawiający kartę SSL menedżera połączeń Studio 3T](./media/mongodb-mongochef/ConnectionManagerSSLTab.png)
6. Kliknij przycisk **Testuj połączenie,** aby sprawdzić poprawność informacji o połączeniu, kliknij przycisk **OK,** aby powrócić do okna Nowe połączenie, a następnie kliknij przycisk **Zapisz**.

    ![Zrzut ekranu przedstawiający okno połączenia testowego Studio 3T](./media/mongodb-mongochef/TestConnectionResults.png)

## <a name="use-studio-3t-to-create-a-database-collection-and-documents"></a>Tworzenie bazy danych, kolekcji i dokumentów za pomocą programu Studio 3T
Aby utworzyć bazę danych, kolekcję i dokumenty przy użyciu programu Studio 3T, wykonaj następujące czynności:

1. W **Menedżerze połączeń**wyróżnij połączenie i kliknij przycisk **Połącz**.

    ![Zrzut ekranu przedstawiający menedżera połączeń Studio 3T](./media/mongodb-mongochef/ConnectToAccount.png)
2. Kliknij prawym przyciskiem myszy hosta i wybierz polecenie **Dodaj bazę danych**.  Podaj nazwę bazy danych i kliknij przycisk **OK**.

    ![Zrzut ekranu przedstawiający opcję Dodaj bazę danych w Studio 3T](./media/mongodb-mongochef/AddDatabase1.png)
3. Kliknij prawym przyciskiem myszy bazę danych i wybierz polecenie **Dodaj kolekcję**.  Podaj nazwę kolekcji i kliknij przycisk **Utwórz**.

    ![Zrzut ekranu przedstawiający opcję Dodaj kolekcję Studio 3T](./media/mongodb-mongochef/AddCollection.png)
4. Kliknij element menu **Kolekcja,** a następnie kliknij pozycję **Dodaj dokument**.

    ![Zrzut ekranu przedstawiający element menu Dodaj dokument w studio 3T](./media/mongodb-mongochef/AddDocument1.png)
5. W oknie dialogowym Dodawanie dokumentu wklej następujące elementy, a następnie kliknij pozycję **Dodaj dokument**.

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
6. Dodaj kolejny dokument, tym razem z następującą zawartością:

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
7. Wykonywanie przykładowej kwerendy. Na przykład wyszukaj rodziny o nazwisku "Andersen" i zwróć pola rodziców i państwa.

    ![Zrzut ekranu przedstawiający wyniki zapytań mongo chef](./media/mongodb-mongochef/QueryDocument1.png)

## <a name="next-steps"></a>Następne kroki

- Dowiedz się, jak [korzystać z programu Robo 3T](mongodb-robomongo.md) za pomocą interfejsu API usługi Azure Cosmos DB dla bazy danych MongoDB.
- Eksploruj [przykłady](mongodb-samples.md) bazy danych MongoDB za pomocą interfejsu API usługi Azure Cosmos DB dla bazy danych MongoDB.
