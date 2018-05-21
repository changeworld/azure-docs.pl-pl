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
ms.openlocfilehash: e5cd7fcd0c853f03dbafb4d95b8459dcc83aecdf
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/20/2018
---
# <a name="azure-search-service-rest-api-version-2016-09-01-preview"></a>Interfejsu API REST usługi Azure Search: Wersji 2016-09-01-Preview
W tym artykule wymieniono funkcje w wersji zapoznawczej `api-version=2016-09-01-Preview`. Ta wersja zapoznawcza rozszerza poprzedniej wersji ogólnie dostępna, [interfejsu api-version = 2016-09-01](https://docs.microsoft.com/rest/api/searchservice), podając następujące funkcje eksperymentalne:

* [`moreLikeThis` parametr zapytania](search-more-like-this.md) można znaleźć dokumenty, które mają zastosowanie do określonego dokumentu. Ta funkcja została w wcześniejszych wersji zapoznawczych. W przypadku wywołujący ten interfejs API przy użyciu starszej wersji interfejsu api, można użyć tej wersji.


## <a name="how-to-call-a-preview-api"></a>Wywoływanie interfejsu API w wersji zapoznawczej

Poniższy przykład przedstawia sposób Podgląd wersja interfejsu api jest określona w podejmowaniu więcej — podobne tego zapytania.

    GET https://[service name].search.windows.net/indexes/[index name]/docs?moreLikeThis=a1&api-version=2016-09-01-Preview

> [!NOTE]
> Funkcje w wersji zapoznawczej są dostępne do testowania i doświadczenia z celem zbierania opinii i mogą ulec zmianie. **Zdecydowanie odradzamy przy użyciu podglądu interfejsów API w aplikacji produkcyjnych.**

Usługa Azure Search jest dostępna w różnych wersjach. Zapoznaj się z [przechowywanie wersji usługi wyszukiwania](https://docs.microsoft.com/azure/search/search-api-versions) szczegółowe informacje.
