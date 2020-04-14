---
title: Standardowy zestaw SDK interfejsu USI usługi Azure Cosmos DB .NET & zasoby
description: Dowiedz się wszystkiego o interfejsie API tabeli usługi Azure Cosmos DB i standardowym sdku .NET, w tym datach wydania, datach wycofania i zmianach wprowadzonych między każdą wersją.
author: sakash279
ms.author: akshanka
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.devlang: dotnet
ms.topic: reference
ms.date: 03/18/2019
ms.openlocfilehash: f7c6829d858e36b55f079f8ee03e431f67995e66
ms.sourcegitcommit: 530e2d56fc3b91c520d3714a7fe4e8e0b75480c8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/14/2020
ms.locfileid: "81272580"
---
# <a name="azure-cosmos-db-table-net-standard-api-download-and-release-notes"></a>Standardowy interfejs API tabeli usługi Azure Cosmos DB .NET: informacje o pobieraniu i wydawaniu
> [!div class="op_single_selector"]
> 
> * [.NET](table-sdk-dotnet.md)
> * [.NET Standard](table-sdk-dotnet-standard.md)
> * [Java](table-sdk-java.md)
> * [Node.js](table-sdk-nodejs.md)
> * [Python](table-sdk-python.md)

|   |   |
|---|---|
|**Pobieranie SDK**|[Nuget](https://www.nuget.org/packages/Microsoft.Azure.Cosmos.Table)|
|**Przykładowe**|[Przykładowa tabela programu Cosmos DB .NET](https://github.com/Azure-Samples/azure-cosmos-table-dotnet-core-getting-started)|
|**Szybki start**|[Szybki start](create-table-dotnet.md)|
|**Samouczek**|[Samouczek](tutorial-develop-table-dotnet.md)|
|**Obecne obsługiwane ramy**|[Microsoft .NET Standard 2.0](https://www.nuget.org/packages/NETStandard.Library)|
|**Problem z raportem**|[Problem z raportem](https://github.com/Azure/azure-cosmos-table-dotnet/issues)|

## <a name="release-notes-for-200-series"></a>Informacje o wersji dla serii 2.0.0
Seria 2.0.0 przyjmuje zależność od [microsoft.Azure.Cosmos,](https://www.nuget.org/packages/Microsoft.Azure.Cosmos/)z poprawą wydajności i konsolidacją obszaru nazw do punktu końcowego usługi Cosmos DB.

### <a name="200-preview"></a><a name="2.0.0-preview"/>2.0.0-podgląd
* początkowy podgląd zestawu SDK tabeli 2.0.0, który przyjmuje zależność od [witryny Microsoft.Azure.Cosmos,](https://www.nuget.org/packages/Microsoft.Azure.Cosmos/)z poprawą wydajności i konsolidacją obszaru nazw do punktu końcowego usługi Cosmos DB. Publiczny interfejs API pozostaje taki sam.

## <a name="release-notes-for-100-series"></a>Informacje o wersji dla serii 1.0.0
Seria 1.0.0 uwzględnia zależność od [witryny Microsoft.Azure.DocumentDB.Core](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB.Core/).

### <a name="107"></a><a name="1.0.7"/>1.0.7
* Poprawa wydajności przez ustawienie domyślnego poziomu śledzenia tabeli SDK na SourceLevels.Off, który można wywnić za pośrednictwem app.config

### <a name="105"></a><a name="1.0.5"/>1.0.5
* Wprowadzenie nowej konfiguracji w obszarze Konfiguracja tableclientconfiguration w celu używania executora odpoczynku do komunikowania się z interfejsem API tabeli usługi Cosmos DB

### <a name="105-preview"></a><a name="1.0.5-preview"/>1.0.5-podgląd
* Poprawki błędów

### <a name="104"></a><a name="1.0.4"/>1.0.4
* Poprawki błędów
* Podaj opcję HttpClientTimeout dla RestExecutorConfiguration.

### <a name="104-preview"></a><a name="1.0.4-preview"/>1.0.4-podgląd
* Poprawki błędów
* Podaj opcję HttpClientTimeout dla RestExecutorConfiguration.

### <a name="101"></a><a name="1.0.1"/>1.0.1
* Poprawki błędów

### <a name="100"></a><a name="1.0.0"/>1.0.0
* Ogólna wersja dostępności

### <a name="0110-preview"></a><a name="0.11.0-preview"/>0.11.0-podgląd
* Wprowadzono zmiany w sposobie konfigurowania cloudtableclient. Teraz trwa TableClientConfiguration obiektu podczas budowy. Konfiguracja tableClientConfiguration udostępnia różne właściwości, aby skonfigurować zachowanie klienta w zależności od tego, czy docelowy punkt końcowy jest interfejsem API tabeli usługi Cosmos DB lub interfejsu API tabeli usługi Azure Storage.
* Dodano obsługę TableQuery, aby zwracać wyniki w kolejności posortowane w kolumnie niestandardowej. Ta funkcja jest obsługiwana tylko w punktach końcowych tabeli usługi Cosmos DB.
* Dodano obsługę, aby uwidaczniać requestcharges na różnych typach wyników. Ta funkcja jest obsługiwana tylko w punktach końcowych tabeli usługi Cosmos DB.

### <a name="0101-preview"></a><a name="0.10.1-preview"/>0.10.1-podgląd
* Dodaj obsługę tokenu sygnatury dostępu Współdzielonego, operacji TablePermissions, ServiceProperties i ServiceStats względem punktów końcowych tabeli usługi Azure Storage. 
   > [!NOTE]
   > Niektóre funkcje w poprzednich zestawach SDK tabel usługi Azure Storage nie są jeszcze obsługiwane, takie jak szyfrowanie po stronie klienta.

### <a name="0100-preview"></a><a name="0.10.0-preview"/>0.10.0-podgląd
* Dodaj obsługę podstawowych operacji CRUD, batch i query dla punktów końcowych tabeli usługi Azure Storage. 
   > [!NOTE]
   > Niektóre funkcje w poprzednich zestawach SDK tabel usługi Azure Storage nie są jeszcze obsługiwane, takie jak szyfrowanie po stronie klienta.

### <a name="091-preview"></a><a name="0.9.1-preview"/>0.9.1-podgląd
* Standardowy zestaw SDK usługi Azure Cosmos DB Table .NET to wieloplatformowa biblioteka .NET, która zapewnia wydajny dostęp do modelu danych tabeli w usłudze Cosmos DB. Ta początkowa wersja obsługuje pełny zestaw funkcji Table and Entity CRUD + Query z podobnymi interfejsami API jak [zestaw SDK tabeli usługi Cosmos DB dla programu .NET Framework](table-sdk-dotnet.md). 
   > [!NOTE]
   >  Punkty końcowe tabeli usługi Azure Storage nie są jeszcze obsługiwane w wersji preview 0.9.1.

## <a name="release-and-retirement-dates"></a>Daty wydania i przejścia na emeryturę
Firma Microsoft powiadamia o wycofaniu sdk sdk z co najmniej **12-miesięcznym** wyprzedzeniem w celu usprawnienia przejścia do nowszej/obsługiwanej wersji.

Ta wieloplatformowa biblioteka .NET Standard [Microsoft.Azure.Cosmos.Table](https://www.nuget.org/packages/Microsoft.Azure.Cosmos.Table) zastąpi bibliotekę .NET Framework [Microsoft.Azure.CosmosDB.Table](https://www.nuget.org/packages/Microsoft.Azure.CosmosDB.Table).

### <a name="200-series"></a>Seria 2.0.0
| Wersja | Data wydania | Data przejścia na emeryturę |
| --- | --- | --- |
| [2.0.0-podgląd](#2.0.0-preview) |Auguest 22, 2019 |--- |

### <a name="100-series"></a>Seria 1.0.0
| Wersja | Data wydania | Data przejścia na emeryturę |
| --- | --- | --- |
| [1.0.5](#1.0.5) |13 września 2019 r. |--- |
| [1.0.5-podgląd](#1.0.5-preview) |Auguest 20, 2019 |--- |
| [1.0.4](#1.0.4) |Auguest 12, 2019 |--- |
| [1.0.4-podgląd](#1.0.4-preview) |26 lipca 2019 r. |--- |
| 1.0.2-podgląd |2 maja 2019 r. |--- |
| [1.0.1](#1.0.1) |19 kwietnia 2019 r. |--- |
| [1.0.0](#1.0.0) |13 marca 2019 r. |--- |
| [0.11.0-podgląd](#0.11.0-preview) |5 marca 2019 r. |--- |
| [0.10.1-podgląd](#0.10.1-preview) |22 stycznia 2019 r. |--- |
| [0.10.0-podgląd](#0.10.0-preview) |18 grudnia 2018 r. |--- |
| [0.9.1-podgląd](#0.9.1-preview) |18 października 2018 r. |--- |


## <a name="faq"></a>Często zadawane pytania

[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="see-also"></a>Zobacz też
Aby dowiedzieć się więcej o interfejsie API tabeli usługi Azure Cosmos DB, zobacz [Wprowadzenie do interfejsu API tabeli usługi Azure Cosmos DB](table-introduction.md).
