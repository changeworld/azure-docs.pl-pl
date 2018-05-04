---
title: 2016 wersja interfejsu API REST usługi Azure Search-09-01-Preview | Dokumentacja firmy Microsoft
description: Azure wyszukiwania usługi REST wersja interfejsu API 2016-09-01-Preview obejmuje eksperymentalne funkcje, takie jak moreLikeThis wyszukiwania.
author: mhko
manager: jlembicz
services: search
ms.service: search
ms.devlang: rest-api
ms.topic: reference
ms.date: 04/18/2018
ms.author: nateko
ms.openlocfilehash: 8eae54c912711a11c015737903b6898b98fd5159
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2018
---
# <a name="azure-search-service-rest-api-version-2016-09-01-preview"></a>Interfejsu API REST usługi Azure Search: Wersji 2016-09-01-Preview
W tym artykule jest dokumentacji dla `api-version=2016-09-01-Preview`. Ta wersja zapoznawcza rozszerza bieżącej wersji ogólnie dostępna, [interfejsu api-version = 2016-09-01](https://docs.microsoft.com/rest/api/searchservice), podając następujące funkcje eksperymentalne:

* [`moreLikeThis` parametr zapytania](search-more-like-this.md) można znaleźć dokumenty, które mają zastosowanie do określonego dokumentu.

Sprawdź, czy docelowy wersja interfejsu API `api-version=2016-09-01-Preview` próby te funkcje eksperymentalne. Poniższy przykład przedstawia sposób Podgląd wersja interfejsu api jest określona w podejmowaniu więcej — podobne tego zapytania.

    GET https://[service name].search.windows.net/indexes/[index name]/docs?moreLikeThis=a1&api-version=2016-09-01-Preview

> [!NOTE]
> Funkcje w wersji zapoznawczej są dostępne do testowania i doświadczenia z celem zbierania opinii i mogą ulec zmianie. **Zdecydowanie odradzamy przy użyciu podglądu interfejsów API w aplikacji produkcyjnych.**

Usługa Azure Search jest dostępna w różnych wersjach. Zapoznaj się z [przechowywanie wersji usługi wyszukiwania](https://docs.microsoft.com/azure/search/search-api-versions) szczegółowe informacje.
