---
title: 2017 wersja interfejsu API REST usługi Azure Search-11-11-Preview | Dokumentacja firmy Microsoft
description: Azure Search usługi REST 2017 wersja interfejsu API-11-11-Preview obejmuje eksperymentalne funkcje, takie jak synonimy i moreLikeThis wyszukiwania.
services: search
author: HeidiSteen
manager: cgronlun
ms.service: search
ms.devlang: rest-api
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: search
ms.date: 05/01/2018
ms.author: HeidiSteen
ms.openlocfilehash: 3a9ff6697357dec443691b261ae6fc0477603a9c
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/07/2018
---
# <a name="azure-search-service-rest-api-version-2017-11-11-preview"></a>Interfejsu API REST usługi Azure Search: 2017 wersji-11-11-Preview
W tym artykule opisano `api-version=2017-11-11-Preview` wersji usługi Azure Search interfejsu API REST, podając następujące funkcje eksperymentalne:

+ [Wyszukiwanie kognitywnych](cognitive-search-concept-intro.md), nową możliwość wzbogacenia w usłudze Azure Search indeksowanie, która znajdzie informacje ukryty w źródłach nietekstowych i niesortowalne tekstu, przekształcania go w zawartości wyszukiwanie pełnotekstowe w usłudze Azure Search.

Podgląd interfejsu API jest odpowiednikiem ogólnie dostępna interfejsu API, z wyjątkiem dwie następujące czynności:

+ [Utwórz Skillset (interfejs api-version = 2017-11-11-Preview)](ref-create-skillset.md)
+ [Utwórz indeksator (interfejs api-version = 2017-11-11-Preview)](ref-create-indexer.md)

Pamiętaj docelowa wersja interfejsu API `api-version=2017-11-11-Preview` podczas obliczania funkcji wersji wstępnej. Następująca składnia przykład ilustruje wywołanie wersja interfejsu API.

    GET https://[service name].search.windows.net/indexes/[index name]/docs?search=*&api-version=2017-11-11-Preview

> [!NOTE]
> Funkcje w wersji zapoznawczej są dostępne do testowania i doświadczenia z celem zbierania opinii i mogą ulec zmianie. **Zdecydowanie odradzamy przy użyciu podglądu interfejsów API w aplikacji produkcyjnych.**

Usługa Azure Search jest dostępna w różnych wersjach. Zapoznaj się z [wersji interfejsu API](search-api-versions.md) szczegółowe informacje.
