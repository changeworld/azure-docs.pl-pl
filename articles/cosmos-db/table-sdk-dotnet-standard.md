---
title: Usługa Azure Cosmos DB Table API .NET Standard SDK i zasoby
description: Dowiedz się wszystkiego o interfejsu API tabeli usługi Azure Cosmos DB i zestawu .NET Standard SDK tym daty wydania, daty wycofania i zmiany między poszczególnymi wersjami.
author: wmengmsft
ms.author: wmeng
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.devlang: dotnet
ms.topic: reference
ms.date: 10/18/2018
ms.openlocfilehash: ce7cc489b107ce4bd95270b9a7f8cb560a2d2398
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/30/2019
ms.locfileid: "55249650"
---
# <a name="azure-cosmos-db-table-net-standard-api-download-and-release-notes"></a>Azure Cosmos DB tabel standardowego interfejsu API platformy .NET: Pobierz i informacje o wersji
> [!div class="op_single_selector"]

> * [.NET](table-sdk-dotnet.md)
> * [.NET Standard](table-sdk-dotnet-standard.md)
> * [Java](table-sdk-java.md)
> * [Node.js](table-sdk-nodejs.md)
> * [Python](table-sdk-python.md)

|   |   |
|---|---|
|**Zestaw SDK do pobrania**|[NuGet](https://www.nuget.org/packages/Microsoft.Azure.Cosmos.Table)|
|**Bieżącej struktury obsługiwanej**|[Microsoft .NET Standard 2.0](https://www.nuget.org/packages/NETStandard.Library)|

## <a name="release-notes"></a>Informacje o wersji

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

| Wersja | Data wydania | Data wygaśnięcia |
| --- | --- | --- |
| [0.10.1-Preview](#0.10.1-preview) |22 stycznia 2019 r. |--- |
| [0.10.0-Preview](#0.10.0-preview) |18 grudnia 2018 r. |--- |
| [0.9.1-Preview](#0.9.1-preview) |18 października 2018 r. |--- |


## <a name="faq"></a>Często zadawane pytania

[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="see-also"></a>Zobacz także
Aby dowiedzieć się więcej na temat interfejsu API tabeli usługi Azure Cosmos DB, zobacz [wprowadzenie do interfejsu API tabeli usługi Azure Cosmos DB](table-introduction.md). 
