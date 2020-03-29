---
title: Łączenie aplikacji bazy danych MongoDB z usługą Azure Cosmos DB
description: Dowiedz się, jak połączyć aplikację MongoDB z usługą Azure Cosmos DB, uzyskując ciąg połączenia z witryny Azure portal
author: timsander1
ms.author: tisande
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: conceptual
ms.date: 03/19/2020
ms.reviewer: sngun
ms.openlocfilehash: e3ab6282a3c61e12dce5dd17bc0859c0d73a7724
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80051709"
---
# <a name="connect-a-mongodb-application-to-azure-cosmos-db"></a>Łączenie aplikacji bazy danych MongoDB z usługą Azure Cosmos DB

Dowiedz się, jak połączyć aplikację MongoDB z usługą Azure Cosmos DB przy użyciu parametrów połączenia bazy danych MongoDB. Następnie możesz użyć bazy danych usługi Azure Cosmos jako magazynu danych dla aplikacji MongoDB.

Ten samouczek opisuje dwa sposoby pobierania informacji o parametrach połączenia:

- [Metoda szybkiego startu](#get-the-mongodb-connection-string-by-using-the-quick-start), do użytku ze sterownikami .NET, Node.js, MongoDB Shell, Java i Python
- [Metoda niestandardowego ciągu połączenia](#get-the-mongodb-connection-string-to-customize)do użytku z innymi sterownikami

## <a name="prerequisites"></a>Wymagania wstępne

- Konto platformy Azure. Jeśli nie masz konta platformy Azure, utwórz [bezpłatne konto platformy Azure](https://azure.microsoft.com/free/) teraz.
- Konto usługi Cosmos. Aby uzyskać instrukcje, zobacz [Tworzenie aplikacji sieci web przy użyciu interfejsu API usługi Azure Cosmos DB dla mongodb i .NET SDK](create-mongodb-dotnet.md).

## <a name="get-the-mongodb-connection-string-by-using-the-quick-start"></a>Pobierz ciąg połączenia MongoDB za pomocą szybkiego startu

1. W przeglądarce internetowej zaloguj się do [witryny Azure portal](https://portal.azure.com).
2. W bloku **usługi Azure Cosmos DB** wybierz interfejs API.
3. W lewym okienku bloku konta kliknij pozycję **Szybki start**.
4. Wybierz swoją platformę (**.NET**, **Node.js**, **MongoDB Shell**, **Java**, **Python**). Jeśli nie widzisz sterownika lub narzędzia na liście, nie martw się - stale dokumentujemy więcej fragmentów kodu połączenia. Proszę skomentować poniżej, co chcesz zobaczyć. Aby dowiedzieć się, jak tworzyć własne połączenie, przeczytaj artykuł [Uzyskaj informacje o ciągu połączenia konta.](#get-the-mongodb-connection-string-to-customize)
5. Skopiuj i wklej fragment kodu do aplikacji MongoDB.

    ![Ostrza szybkiego startu](./media/connect-mongodb-account/QuickStartBlade.png)

## <a name="get-the-mongodb-connection-string-to-customize"></a>Pobierz ciąg połączenia MongoDB, aby dostosować

1. W przeglądarce internetowej zaloguj się do [witryny Azure portal](https://portal.azure.com).
2. W bloku **usługi Azure Cosmos DB** wybierz interfejs API.
3. W lewym okienku bloku konta kliknij pozycję **Parametry połączenia**.
4. Zostanie otwarte bloku **Ciąg połączenia.** Posiada wszystkie informacje niezbędne do połączenia się z kontem za pomocą sterownika dla MongoDB, w tym wstępnie skonfigurowany ciąg połączenia.

   [![Ostrze](./media/connect-mongodb-account/ConnectionStringBlade.png) ciągu połączenia](./media/connect-mongodb-account/ConnectionStringBlade.png#lightbox)

## <a name="connection-string-requirements"></a>Wymagania dotyczące ciągu połączenia

> [!Important]
> usługa Azure Cosmos DB ma ścisłe wymagania i standardy dotyczące bezpieczeństwa. Konta usługi Azure Cosmos DB wymagają uwierzytelniania i bezpiecznej komunikacji za pośrednictwem *protokołu SSL.* 
>
>

Usługa Azure Cosmos DB obsługuje standardowy format identyfikatora URI ciągu połączenia MongoDB z kilkoma określonymi wymaganiami: konta usługi Azure Cosmos DB wymagają uwierzytelniania i bezpiecznej komunikacji za pośrednictwem protokołu SSL. Tak więc format ciągu połączenia jest:

    mongodb://username:password@host:port/[database]?ssl=true

Wartości tego ciągu są dostępne w **bloku Parametry połączenia** pokazane wcześniej:

* Nazwa użytkownika (wymagana): nazwa konta usługi Cosmos.
* Hasło (wymagane): hasło do konta usługi Cosmos.
* Host (wymagane): FQDN konta usługi Cosmos.
* Port (wymagany): 10255.
* Baza danych (opcjonalnie): baza danych używana przez połączenie. Jeśli nie ma bazy danych, domyślną bazą danych jest "test".
* ssl=true (wymagane)

Rozważmy na przykład konto wyświetlane w **bloku Parametry połączenia.** Prawidłowy ciąg połączenia to:

    mongodb://contoso123:0Fc3IolnL12312asdfawejunASDF@asdfYXX2t8a97kghVcUzcDv98hawelufhawefafnoQRGwNj2nMPL1Y9qsIr9Srdw==@contoso123.documents.azure.com:10255/mydatabase?ssl=true

## <a name="next-steps"></a>Następne kroki

- Dowiedz się, jak [korzystać z programu Studio 3T](mongodb-mongochef.md) za pomocą interfejsu API usługi Azure Cosmos DB dla bazy danych MongoDB.
- Dowiedz się, jak [korzystać z programu Robo 3T](mongodb-robomongo.md) za pomocą interfejsu API usługi Azure Cosmos DB dla bazy danych MongoDB.
- Eksploruj [przykłady](mongodb-samples.md) bazy danych MongoDB za pomocą interfejsu API usługi Azure Cosmos DB dla bazy danych MongoDB.
