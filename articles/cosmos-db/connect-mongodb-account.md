---
title: Parametry połączenia bazy danych MongoDB dla konta bazy danych Azure rozwiązania Cosmos | Dokumentacja firmy Microsoft
description: Dowiedz się, jak łączenie aplikacji z bazy danych MongoDB konta bazy danych rozwiązania Cosmos Azure przy użyciu ciągu połączenia bazy danych MongoDB.
keywords: Parametry połączenia bazy danych mongodb
services: cosmos-db
author: AndrewHoh
manager: kfile
documentationcenter: ''
ms.assetid: e36f7375-9329-403b-afd1-4ab49894f75e
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/19/2017
ms.author: anhoh
ms.openlocfilehash: dfdcb458d0f81c0ddb9399ee8b89a52c4cbc6270
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2018
---
# <a name="connect-a-mongodb-application-to-azure-cosmos-db"></a>Łączenie aplikacji bazy danych MongoDB, do bazy danych Azure rozwiązania Cosmos
Dowiedz się, jak łączenie aplikacji z bazy danych MongoDB konta bazy danych rozwiązania Cosmos Azure przy użyciu ciągu połączenia bazy danych MongoDB. Następnie można użyć bazy danych Azure rozwiązania Cosmos bazy danych jako dane magazynu dla aplikacji bazy danych MongoDB. 

Ten samouczek zawiera można pobrać ciągu połączenia na dwa sposoby:

- [Szybki start — metoda](#QuickstartConnection), do użycia z programem .NET, Node.js, powłoka bazy danych MongoDB, Java i Python sterowniki
- [Metoda ciąg połączenia niestandardowe](#GetCustomConnection), do użycia z innych sterowników

## <a name="prerequisites"></a>Wymagania wstępne

- Konto platformy Azure. Jeśli nie masz konta platformy Azure, Utwórz [bezpłatne konto platformy Azure](https://azure.microsoft.com/free/) teraz. 
- Konto usługi Azure Cosmos DB. Aby uzyskać instrukcje, zobacz [tworzenie aplikacji sieci web interfejsu API bazy danych MongoDB z usług .NET i portalu Azure](create-mongodb-dotnet.md).

## <a id="QuickstartConnection"></a>Pobierz ciąg połączenia bazy danych MongoDB przy użyciu szybki start
1. W przeglądarce internetowej, zaloguj się do [portalu Azure](https://portal.azure.com).
2. W **bazy danych Azure rozwiązania Cosmos** bloku, wybierz interfejsu API dla konta bazy danych MongoDB. 
3. W lewym okienku blok konta, kliknij przycisk **szybki start**. 
4. Wybierz platformy (**.NET**, **Node.js**, **powłoki MongoDB**, **Java**, **Python**). Jeśli nie widzisz, Twoje sterownika lub narzędzia na liście, nie martw się — firma Microsoft stale dokumentu więcej wstawki kodu połączenia. Skomentuj poniżej na chcesz zobaczyć. Aby dowiedzieć się, jak sformułować własne połączenia, przeczytaj [uzyskać informacje o parametrach połączenia dla konta](#GetCustomConnection).
5. Skopiuj i Wklej fragmentu kodu w aplikacji bazy danych MongoDB.

    ![Bloku szybki start](./media/connect-mongodb-account/QuickStartBlade.png)

## <a id="GetCustomConnection"></a> Pobierz ciąg połączenia bazy danych MongoDB do dostosowania
1. W przeglądarce internetowej, zaloguj się do [portalu Azure](https://portal.azure.com).
2. W **bazy danych Azure rozwiązania Cosmos** bloku, wybierz interfejsu API dla konta bazy danych MongoDB. 
3. W lewym okienku blok konta, kliknij przycisk **ciąg połączenia**. 
4. **Ciąg połączenia** zostanie otwarty blok. Ma wszystkie informacje wymagane do nawiązania konta za pośrednictwem sterownika bazy danych mongodb, w tym ciągu połączenia preconstructed.

    ![Blok ciągu połączenia](./media/connect-mongodb-account/ConnectionStringBlade.png)

## <a name="connection-string-requirements"></a>Wymagania dotyczące ciąg połączenia
> [!Important]
> Azure DB rozwiązania Cosmos ma wymogi ograniczeniami zabezpieczeń. Azure konta DB rozwiązania Cosmos wymagają uwierzytelniania i zapewnienia bezpiecznej komunikacji za pośrednictwem *SSL*. 
>
>

Azure DB rozwiązania Cosmos obsługuje standardowe bazy danych MongoDB format ciągu połączenia identyfikator URI, za pomocą paru specyficznych wymagań: konta bazy danych Azure rozwiązania Cosmos wymagają uwierzytelniania i zapewnienia bezpiecznej komunikacji za pośrednictwem protokołu SSL. Tak format ciągu połączenia jest:

    mongodb://username:password@host:port/[database]?ssl=true

Wartości te parametry są dostępne w **ciąg połączenia** bloku przedstawiona wcześniej:

* Nazwa użytkownika (wymagane): Nazwa konta bazy danych Azure rozwiązania Cosmos.
* Hasło (wymagane): hasło konta bazy danych Azure rozwiązania Cosmos.
* Host (wymagane): nazwa FQDN konta bazy danych Azure rozwiązania Cosmos.
* Port (wymagane): 10255.
* Bazy danych (opcjonalnie): połączenie korzysta z bazy danych. Jeśli baza danych jest dostępne, domyślna baza danych jest "test".
* Protokół SSL = true (wymagane)

Rozważmy na przykład konto pokazano **ciąg połączenia** bloku. Jest prawidłowe parametry połączenia:

    mongodb://contoso123:0Fc3IolnL12312asdfawejunASDF@asdfYXX2t8a97kghVcUzcDv98hawelufhawefafnoQRGwNj2nMPL1Y9qsIr9Srdw==@contoso123.documents.azure.com:10255/mydatabase?ssl=true

## <a name="next-steps"></a>Kolejne kroki
* Dowiedz się, jak [Użyj Studio 3T (MongoChef)](mongodb-mongochef.md) z interfejsu API Azure rozwiązania Cosmos bazy danych dla konta bazy danych MongoDB.
* Eksploruj interfejsu API Azure rozwiązania Cosmos bazy danych dla bazy danych MongoDB, przeglądając [przykłady](mongodb-samples.md).
