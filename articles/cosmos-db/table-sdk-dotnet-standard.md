---
title: Usługa Azure Cosmos DB Table API .NET Standard SDK i zasoby
description: Dowiedz się wszystkiego o interfejsu API tabeli usługi Azure Cosmos DB i zestawu .NET Standard SDK tym daty wydania, daty wycofania i zmiany między poszczególnymi wersjami.
author: wmengmsft
ms.author: wmeng
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.devlang: dotnet
ms.topic: reference
ms.date: 03/18/2019
ms.openlocfilehash: 1c9894772f440a568cbc08890feb961471d84137
ms.sourcegitcommit: cababb51721f6ab6b61dda6d18345514f074fb2e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/04/2019
ms.locfileid: "66480217"
---
# <a name="azure-cosmos-db-table-net-standard-api-download-and-release-notes"></a>Azure Cosmos DB tabel standardowego interfejsu API platformy .NET: Pobierz i informacje o wersji
> [!div class="op_single_selector"]
> 
> * [.NET](table-sdk-dotnet.md)
> * [.NET Standard](table-sdk-dotnet-standard.md)
> * [Java](table-sdk-java.md)
> * [Node.js](table-sdk-nodejs.md)
> * [Python](table-sdk-python.md)

|   |   |
|---|---|
|**Zestaw SDK do pobrania**|[NuGet](https://www.nuget.org/packages/Microsoft.Azure.Cosmos.Table)|
|**Przykład**|[Przykład .NET interfejs API tabeli usługi cosmos DB](https://github.com/Azure-Samples/azure-cosmos-table-dotnet-core-getting-started)|
|**Szybki start**|[Szybki start](create-table-dotnet.md)|
|**Samouczek**|[Samouczek](tutorial-develop-table-dotnet.md)|
|**Bieżącej struktury obsługiwanej**|[Microsoft .NET Standard 2.0](https://www.nuget.org/packages/NETStandard.Library)|

## <a name="release-notes"></a>Informacje o wersji

### <a name="a-name101101"></a><a name="1.0.1"/>1.0.1
* Poprawki błędów

### <a name="a-name100100"></a><a name="1.0.0"/>1.0.0
* Wersji ogólnodostępnej

### <a name="a-name0110-preview0110-preview"></a><a name="0.11.0-preview"/>0.11.0-Preview
* Jak skonfigurować CloudTableClient zostały wprowadzone zmiany. Teraz przyjmuje obiekt TableClientConfiguration podczas konstruowania. TableClientConfiguration udostępnia różne właściwości do konfigurowania zachowania klienta w zależności od tego, czy docelowy punkt końcowy interfejsu API tabeli usługi Cosmos DB lub interfejsu API tabeli usługi Azure Storage.
* Dodano obsługę TableQuery w celu zwracania wyników w kolejności posortowanej według kolumny niestandardowej. Ta funkcja jest obsługiwana tylko w punktach końcowych Cosmos DB Table.
* Dodano obsługę do udostępnienia RequestCharges na różnych typach wyników. Ta funkcja jest obsługiwana tylko w punktach końcowych Cosmos DB Table.

### <a name="a-name0101-preview0101-preview"></a><a name="0.10.1-preview"/>0.10.1-Preview
* Dodano obsługę tokenu sygnatury dostępu Współdzielonego, operacje TablePermissions, ServiceProperties i ServiceStats względem punktów końcowych usługi Azure Table Storage. 
   > [!NOTE]
   > Niektóre funkcje w poprzednim SDK tabeli magazynu platformy Azure nie są jeszcze obsługiwane, takich jak szyfrowanie po stronie klienta.

### <a name="a-name0100-preview0100-preview"></a><a name="0.10.0-preview"/>0.10.0-Preview
* Dodanie obsługi podstawowych operacji CRUD, partii i operacje zapytań względem punktów końcowych usługi Azure Table Storage. 
   > [!NOTE]
   > Niektóre funkcje w poprzednim SDK tabeli magazynu platformy Azure nie są jeszcze obsługiwane, takich jak szyfrowanie po stronie klienta.

### <a name="a-name091-preview091-preview"></a><a name="0.9.1-preview"/>0.9.1-Preview
* Usługa Azure Cosmos DB Table .NET Standard SDK jest biblioteki .NET dla wielu platform, która umożliwia efektywne dostęp do tabeli modelu danych usługi Cosmos DB. Ta początkowa wersja obsługuje pełny zestaw tabeli i CRUD jednostki + funkcje zapytania podobne interfejsy API jako [Cosmos DB Table zestawu SDK dla .NET Framework](table-sdk-dotnet.md). 
   > [!NOTE]
   >  Punkty końcowe platformy Azure Storage Table nie są jeszcze obsługiwane w wersji 0.9.1-preview.

## <a name="release-and-retirement-dates"></a>Daty wydania i wycofania
Firma Microsoft zapewnia powiadomienie co najmniej **12 miesięcy** ewentualnej wycofanie zestawu SDK w celu złagodzenia przejścia do nowszych/obsługiwanych wersji.

Ta biblioteka .NET Standard dla wielu platform [Microsoft.Azure.Cosmos.Table](https://www.nuget.org/packages/Microsoft.Azure.Cosmos.Table) zastąpi biblioteki programu .NET Framework [Microsoft.Azure.CosmosDB.Table](https://www.nuget.org/packages/Microsoft.Azure.CosmosDB.Table).

| Wersja | Data wydania | Data wygaśnięcia |
| --- | --- | --- |
| [1.0.1](#1.0.1) |19 kwietnia 2019 r. |--- |
| [1.0.0](#1.0.0) |13 marca 2019 r. |--- |
| [0.11.0-Preview](#0.11.0-preview) |5 marca 2019 r. |--- |
| [0.10.1-Preview](#0.10.1-preview) |22 stycznia 2019 r. |--- |
| [0.10.0-Preview](#0.10.0-preview) |18 grudnia 2018 r. |--- |
| [0.9.1-Preview](#0.9.1-preview) |18 października 2018 r. |--- |


## <a name="faq"></a>Często zadawane pytania

[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="see-also"></a>Zobacz także
Aby dowiedzieć się więcej na temat interfejsu API tabeli usługi Azure Cosmos DB, zobacz [wprowadzenie do interfejsu API tabeli usługi Azure Cosmos DB](table-introduction.md). 
