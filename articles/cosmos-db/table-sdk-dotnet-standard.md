---
title: Azure Cosmos DB interfejs API tabel .NET Standard SDK & zasobów
description: Zapoznaj się ze wszystkimi Azure Cosmos DB interfejs API tabel i zestawem SDK .NET Standard, w tym datami wydania, datami wycofania i zmianami wprowadzonymi między każdą wersją.
author: wmengmsft
ms.author: wmeng
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.devlang: dotnet
ms.topic: reference
ms.date: 03/18/2019
ms.openlocfilehash: e92c2e58122556f90b25f070fff9e6e6616b0257
ms.sourcegitcommit: 62bd5acd62418518d5991b73a16dca61d7430634
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/13/2019
ms.locfileid: "68976848"
---
# <a name="azure-cosmos-db-table-net-standard-api-download-and-release-notes"></a>Interfejs API .NET Standard tabeli Azure Cosmos DB: Informacje dotyczące pobierania i zwalniania
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
|**Przykład**|[Przykład Cosmos DB interfejs API tabel .NET](https://github.com/Azure-Samples/azure-cosmos-table-dotnet-core-getting-started)|
|**Szybki start**|[Szybki start](create-table-dotnet.md)|
|**Samouczek**|[Samouczek](tutorial-develop-table-dotnet.md)|
|**Bieżącej struktury obsługiwanej**|[Microsoft .NET Standard 2,0](https://www.nuget.org/packages/NETStandard.Library)|
|**Zgłoś problem**|[Zgłoś problem](https://github.com/Azure/azure-cosmos-table-dotnet/issues)|

## <a name="release-notes"></a>Informacje o wersji

### <a name="a-name104104"></a><a name="1.0.4"/>1.0.4
* Poprawki błędów
* Podaj opcję HttpClientTimeout dla RestExecutorConfiguration.

### <a name="a-name104-preview104-preview"></a><a name="1.0.4-preview"/>1.0.4 — wersja zapoznawcza
* Poprawki błędów
* Podaj opcję HttpClientTimeout dla RestExecutorConfiguration.

### <a name="a-name101101"></a><a name="1.0.1"/>1.0.1
* Poprawki błędów

### <a name="a-name100100"></a><a name="1.0.0"/>1.0.0
* Wersji ogólnodostępnej

### <a name="a-name0110-preview0110-preview"></a><a name="0.11.0-preview"/>0.11.0 — wersja zapoznawcza
* Wprowadzono zmiany w sposobie konfigurowania CloudTableClient. Teraz pobiera obiekt TableClientConfiguration w trakcie konstruowania. TableClientConfiguration udostępnia różne właściwości do konfigurowania zachowania klienta w zależności od tego, czy docelowy punkt końcowy jest Cosmos DB interfejs API tabel czy interfejs API tabel usługi Azure Storage.
* Dodano obsługę do TableQuery w celu zwrócenia wyników w kolejności posortowanej w kolumnie niestandardowej. Ta funkcja jest obsługiwana tylko dla punktów końcowych tabeli Cosmos DB.
* Dodano obsługę w celu udostępnienia RequestCharges na różnych typach wyników. Ta funkcja jest obsługiwana tylko dla punktów końcowych tabeli Cosmos DB.

### <a name="a-name0101-preview0101-preview"></a><a name="0.10.1-preview"/>0.10.1 — wersja zapoznawcza
* Dodano obsługę tokenów SAS, operacji TablePermissions, serviceproperties i servicestatystyk względem punktów końcowych tabeli usługi Azure Storage. 
   > [!NOTE]
   > Niektóre funkcje w poprzednich zestawach SDK tabeli usługi Azure Storage nie są jeszcze obsługiwane, takie jak szyfrowanie po stronie klienta.

### <a name="a-name0100-preview0100-preview"></a><a name="0.10.0-preview"/>0.10.0 — wersja zapoznawcza
* Dodawanie obsługi podstawowych operacji CRUD, Batch i zapytań do punktów końcowych tabeli usługi Azure Storage. 
   > [!NOTE]
   > Niektóre funkcje w poprzednich zestawach SDK tabeli usługi Azure Storage nie są jeszcze obsługiwane, takie jak szyfrowanie po stronie klienta.

### <a name="a-name091-preview091-preview"></a><a name="0.9.1-preview"/>od 0.9.1 — wersja zapoznawcza
* Azure Cosmos DB Table .NET Standard SDK to międzyplatformowa biblioteka platformy .NET, która zapewnia wydajny dostęp do modelu danych tabeli w Cosmos DB. Ta wersja wstępna obsługuje pełny zestaw funkcji zapytania CRUD + Entity z podobnymi interfejsami API jako [zestaw SDK tabeli Cosmos DB dla .NET Framework](table-sdk-dotnet.md). 
   > [!NOTE]
   >  Punkty końcowe tabeli usługi Azure Storage nie są jeszcze obsługiwane w wersji od 0.9.1-Preview.

## <a name="release-and-retirement-dates"></a>Daty wydania i wycofania
Firma Microsoft zapewnia powiadomienie co najmniej **12 miesięcy** przed WYCOFANIEM zestawu SDK w celu zapewnienia sprawnego przejścia do nowszej/obsługiwanej wersji.

Ta międzyplatformowa biblioteka .NET Standard [Microsoft. Azure. Cosmos. Table](https://www.nuget.org/packages/Microsoft.Azure.Cosmos.Table) zastąpi bibliotekę .NET Framework [Microsoft. Azure. CosmosDB. Table](https://www.nuget.org/packages/Microsoft.Azure.CosmosDB.Table).

| Version | Data wydania | Data wygaśnięcia |
| --- | --- | --- |
| [1.0.4](#1.0.4) |Auguest 12, 2019 |--- |
| [1.0.4 — wersja zapoznawcza](#1.0.4-preview) |26 lipca 2019 |--- |
| 1.0.2 (wersja zapoznawcza) |2 maja 2019 |--- |
| [1.0.1](#1.0.1) |19 kwietnia 2019 |--- |
| [1.0.0](#1.0.0) |13 marca 2019 |--- |
| [0.11.0 — wersja zapoznawcza](#0.11.0-preview) |5 marca 2019 |--- |
| [0.10.1 — wersja zapoznawcza](#0.10.1-preview) |22 stycznia 2019 |--- |
| [0.10.0 — wersja zapoznawcza](#0.10.0-preview) |18 grudnia, 2018 |--- |
| [od 0.9.1 — wersja zapoznawcza](#0.9.1-preview) |18 października 2018 |--- |


## <a name="faq"></a>Często zadawane pytania

[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="see-also"></a>Zobacz także
Aby dowiedzieć się więcej na temat interfejs API tabel Azure Cosmos DB, zobacz [wprowadzenie do Azure Cosmos DB interfejs API tabel](table-introduction.md).
