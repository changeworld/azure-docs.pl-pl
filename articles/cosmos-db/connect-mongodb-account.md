---
title: Łączenie aplikacji MongoDB z usługą Azure Cosmos DB
description: Dowiedz się, jak połączyć aplikację MongoDB z Azure Cosmos DB, pobierając parametry połączenia z Azure Portal
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: conceptual
ms.date: 12/02/2019
ms.reviewer: sngun
ms.openlocfilehash: 0f2cd1b7228f2cc9cadb84232222f658a512a81f
ms.sourcegitcommit: 9405aad7e39efbd8fef6d0a3c8988c6bf8de94eb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/05/2019
ms.locfileid: "74872166"
---
# <a name="connect-a-mongodb-application-to-azure-cosmos-db"></a>Łączenie aplikacji MongoDB z usługą Azure Cosmos DB
Dowiedz się, jak połączyć aplikację MongoDB z Azure Cosmos DB przy użyciu parametrów połączenia MongoDB. Następnie możesz użyć bazy danych usługi Azure Cosmos jako magazynu danych dla aplikacji MongoDB. 

Ten samouczek zawiera dwa sposoby pobierania informacji o parametrach połączenia:

- [Metoda szybkiego startu](#QuickstartConnection), używana z sterownikami .NET, Node. js, MongoDB Shell, Java i Python
- [Niestandardowa metoda parametrów połączenia](#GetCustomConnection)do użycia z innymi sterownikami

## <a name="prerequisites"></a>Wymagania wstępne

- Konto platformy Azure. Jeśli nie masz konta platformy Azure, Utwórz teraz [bezpłatne konto platformy Azure](https://azure.microsoft.com/free/) . 
- Konto Cosmos. Aby uzyskać instrukcje, zobacz [Tworzenie aplikacji sieci Web przy użyciu interfejsu API Azure Cosmos DB dla MongoDB i zestawu .NET SDK](create-mongodb-dotnet.md).

## <a id="QuickstartConnection"></a>Pobieranie parametrów połączenia MongoDB za pomocą przewodnika Szybki Start
1. W przeglądarce internetowej Zaloguj się do [Azure Portal](https://portal.azure.com).
2. W bloku **Azure Cosmos DB** wybierz interfejs API. 
3. W lewym okienku bloku konta kliknij pozycję **Szybki Start**. 
4. Wybierz platformę ( **.NET**, **Node. js**, **MongoDB Shell**, **Java**, **Python**). Jeśli nie widzisz sterownika lub narzędzia na liście, nie martw się — ciągle dokumentuje więcej fragmentów kodu połączenia. Dodaj komentarz poniżej na temat tego, co chcesz zobaczyć. Aby dowiedzieć się, jak połączyć własne połączenie, przeczytaj temat [pobieranie informacji o parametrach połączenia konta](#GetCustomConnection).
5. Skopiuj i wklej fragment kodu do aplikacji MongoDB.

    ![Blok Szybki Start](./media/connect-mongodb-account/QuickStartBlade.png)

## <a id="GetCustomConnection"></a>Pobierz parametry połączenia MongoDB, aby dostosować
1. W przeglądarce internetowej Zaloguj się do [Azure Portal](https://portal.azure.com).
2. W bloku **Azure Cosmos DB** wybierz interfejs API. 
3. W lewym okienku bloku konta kliknij pozycję **Parametry połączenia**. 
4. Zostanie otwarty blok **parametrów połączenia** . Ma ona wszystkie informacje niezbędne do nawiązania połączenia z kontem przy użyciu sterownika dla MongoDB, łącznie z prebudowanymi parametrami połączenia.

    ![Blok Parametry połączenia](./media/connect-mongodb-account/ConnectionStringBlade.png)

## <a name="connection-string-requirements"></a>Wymagania dotyczące parametrów połączenia
> [!Important]
> usługa Azure Cosmos DB ma ścisłe wymagania i standardy dotyczące bezpieczeństwa. Konta Azure Cosmos DB wymagają uwierzytelniania i bezpiecznej komunikacji za pośrednictwem *protokołu SSL*. 
>
>

Azure Cosmos DB obsługuje format standardowego identyfikatora URI parametrów połączenia MongoDB z kilkoma określonymi wymaganiami: konta Azure Cosmos DB wymagają uwierzytelniania i bezpiecznej komunikacji za pośrednictwem protokołu SSL. W związku z tym format parametrów połączenia to:

    mongodb://username:password@host:port/[database]?ssl=true

Wartości tego ciągu są dostępne w bloku **parametrów połączenia** przedstawionym wcześniej:

* Username (wymagana): nazwa konta Cosmos.
* Hasło (wymagane): hasło do konta Cosmos.
* Host (wymagane): nazwa FQDN konta Cosmos.
* Port (wymagany): 10255.
* Baza danych (opcjonalnie): baza danych, z której korzysta połączenie. Jeśli baza danych nie zostanie dostarczona, domyślną bazą danych jest "test".
* SSL = true (wymagane)

Rozważmy na przykład konto pokazane w bloku **parametrów połączenia** . Prawidłowe parametry połączenia to:

    mongodb://contoso123:0Fc3IolnL12312asdfawejunASDF@asdfYXX2t8a97kghVcUzcDv98hawelufhawefafnoQRGwNj2nMPL1Y9qsIr9Srdw==@contoso123.documents.azure.com:10255/mydatabase?ssl=true

## <a name="next-steps"></a>Następne kroki

- Dowiedz się, jak [korzystać z programu Studio 3T](mongodb-mongochef.md) za pomocą interfejsu API usługi Azure Cosmos DB dla bazy danych MongoDB.
- Dowiedz się, jak [korzystać z programu Robo 3T](mongodb-robomongo.md) za pomocą interfejsu API usługi Azure Cosmos DB dla bazy danych MongoDB.
- Eksploruj [przykłady](mongodb-samples.md) bazy danych MongoDB za pomocą interfejsu API usługi Azure Cosmos DB dla bazy danych MongoDB.
