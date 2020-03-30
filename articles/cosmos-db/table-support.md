---
title: Pomoc techniczna usługi Azure Table Storage w usłudze Azure Cosmos DB
description: Dowiedz się, jak interfejs API tabeli usługi Azure Cosmos DB i tabele usługi Azure Storage współpracują ze sobą, udostępniając ten sam model danych tabeli w operacjach
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.topic: overview
ms.date: 12/02/2019
author: sakash279
ms.author: akshanka
ms.reviewer: sngun
ms.openlocfilehash: 82397e49e473b8660dfada54a0e05cafe0da4120
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2020
ms.locfileid: "76770665"
---
# <a name="developing-with-azure-cosmos-db-table-api-and-azure-table-storage"></a>Programowanie za pomocą interfejsu API tabel usługi Azure Cosmos DB oraz usługi Azure Table Storage

Interfejs API tabel usługi Azure Cosmos DB oraz usługa Azure Table Storage mają ten sam model danych tabeli oraz udostępniają te same operacje tworzenia, usuwania, aktualizacji i zapytań w swoich zestawach SDK.

[!INCLUDE [storage-table-cosmos-comparison](../../includes/storage-table-cosmos-comparison.md)]

## <a name="developing-with-the-azure-cosmos-db-table-api"></a>Programowanie z użyciem interfejsu API tabel usługi Azure Cosmos DB

W tej chwili [interfejs API tabel usługi Azure Cosmos DB](table-introduction.md) ma cztery zestawy SDK dostępne do programowania: 

* [Microsoft.Azure.Cosmos.Table](https://www.nuget.org/packages/Microsoft.Azure.Cosmos.Table): .NET SDK. Elementem docelowym tej biblioteki jest platforma .NET Standard i ma ona takie same klasy i podpisy metod jak publiczny [zestaw SDK usługi Windows Azure Storage](https://www.nuget.org/packages/WindowsAzure.Storage), ale ma również możliwość łączenia się z kontami usługi Azure Cosmos DB przy użyciu interfejsu API tabel. Użytkownicy biblioteki platformy .NET Framework [Microsoft.Azure.CosmosDB.Table](https://www.nuget.org/packages/Microsoft.Azure.CosmosDB.Table/) są zalecane do uaktualnienia do [microsoft.Azure.Cosmos.Table,](https://www.nuget.org/packages/Microsoft.Azure.Cosmos.Table) ponieważ jest w trybie konserwacji i wkrótce zostanie przestarzały.

* [Zestaw SDK języka Python:](table-sdk-python.md)nowy zestaw SDK języka Python usługi Azure Cosmos DB DB jest jedynym zestawem SDK obsługującym magazyn tabel platformy Azure w języku Python. Ten zestaw SDK łączy się z usługą Azure Table Storage oraz interfejsem API tabel usługi Azure Cosmos DB.

* [Zestaw Java SDK:](table-sdk-java.md)Ten zestaw SDK usługi Azure Storage ma możliwość łączenia się z kontami usługi Azure Cosmos DB przy użyciu interfejsu API tabeli.

* [Node.js SDK:](table-sdk-nodejs.md)Ten zestaw SDK usługi Azure Storage ma możliwość łączenia się z kontami usługi Azure Cosmos DB przy użyciu interfejsu API tabeli.


Dodatkowe informacje dotyczące pracy z interfejsem API tabel są dostępne w artykule: [Często zadawane pytania: programowanie przy użyciu interfejsu API tabel](faq.md#table).

## <a name="developing-with-azure-table-storage"></a>Programowanie z wykorzystaniem usługi Azure Table Storage

Magazyn tabel Azure oferuje do programowania następujące zestawy SDK:

- [Zestaw SDK dla platformy .NET WindowsAzure.Storage](https://www.nuget.org/packages/WindowsAzure.Storage/). Ta biblioteka umożliwia pracę z usługą Table Storage.
- [Zestaw SDK dla języka Python](https://github.com/Azure/azure-cosmos-table-python). Zestaw SDK tabeli usługi Azure Cosmos DB dla języka Python obsługuje usługę usługi Magazyn tabel (ponieważ interfejs API tabel usługi Azure Table Storage i Usługi Cosmos DB mają te same funkcje i funkcje, a w celu scharakteryzowania naszych wysiłków związanych z tworzeniem zestawów SDK zalecamy użycie tego zestawu SDK).
- [Zestaw SDK usługi Azure Storage dla języka Java](https://github.com/azure/azure-storage-java). Ten zestaw SDK usługi Azure Storage zapewnia bibliotekę klienta w środowisku Java do pracy z usługą Azure Table Storage.
- [Node.js SDK](https://github.com/Azure/azure-storage-node). Zestaw SDK zawiera pakiet Node.js i bibliotekę klienta JavaScript zgodną z przeglądarką, aby korzystać z usługi Table Storage.
- [Moduł programu PowerShell AzureRmStorageTable ](https://www.powershellgallery.com/packages/AzureRmStorageTable). Ten moduł programu PowerShell zawiera polecenia cmdlet do pracy z tabelami usługi Storage.
- [Biblioteka klienta usługi Azure Storage dla języka C++](https://github.com/Azure/azure-storage-cpp/). Ta biblioteka umożliwia tworzenie aplikacji w usłudze Azure Storage.
- [Biblioteka klienta tabel usługi Azure Storage dla Ruby](https://github.com/azure/azure-storage-ruby/tree/master/table). Ten projekt zapewnia pakiet Ruby, który ułatwia dostęp do tabel usługi Azure Storage.
- [Biblioteka klienta PHP tabel usługi Azure Storage](https://github.com/Azure/azure-storage-php/tree/master/azure-storage-table). Ten projekt zapewnia bibliotekę klienta PHP, która ułatwia dostęp do tabel usługi Azure Storage.


   





