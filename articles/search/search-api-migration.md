---
title: Uaktualnianie do najnowszej wersji interfejsu API REST usługi Azure Search | Dokumentacja firmy Microsoft
description: Uaktualnianie do najnowszej wersji interfejsu API REST usługi Azure Search
author: brjohnstmsft
manager: jlembicz
services: search
ms.service: search
ms.devlang: rest-api
ms.topic: conceptual
ms.date: 04/20/2018
ms.author: brjohnst
ms.openlocfilehash: 2efe7769f68988f3c0d52c8806b78c1b96d8c639
ms.sourcegitcommit: 35ceadc616f09dd3c88377a7f6f4d068e23cceec
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/08/2018
ms.locfileid: "39620233"
---
# <a name="upgrading-to-the-latest-azure-search-service-rest-api-version"></a>Uaktualnianie do najnowszej wersji interfejsu API REST usługi Azure Search
Jeśli używasz poprzedniej wersji [interfejsu API REST usługi Azure Search](https://docs.microsoft.com/rest/api/searchservice/), ten artykuł pomoże Ci uaktualnienia aplikacji w taki sposób, aby użyć najnowszej wersji ogólnie dostępnej wersji interfejsu API, 2017-11-11.

Wersja 2017-11-11 interfejsu API REST zawiera pewne zmiany z wcześniejszych wersji. Są to przede wszystkim zgodne z poprzednimi wersjami, więc zmiana kodu należy wymagać tylko minimalnym nakładzie pracy, w zależności od instalowanej wersji używanego wcześniej. Zobacz [kroki niezbędne do uaktualnienia](#UpgradeSteps) zawiera instrukcje dotyczące sposobu zmiany kodu do nowej wersji interfejsu API.

> [!NOTE]
> Wystąpienia usługi Azure Search obsługuje kilka wersji interfejsu API REST, w tym najnowsze. Można użyć wersji, gdy nie jest już najnowsze, ale zaleca się przeprowadzenie migracji kodu, aby użyć najnowszej wersji.

<a name="WhatsNew"></a>

## <a name="whats-new-in-version-2017-11-11"></a>Co nowego w wersji 2017-11-11
Wersja 2017-11-11 jest najnowszą jest ogólnie dostępna wersja interfejsu API REST usługi Azure Search Service. Nowe funkcje w tej wersji interfejsu API:

* [Synonimy](search-synonyms.md). Nowa funkcja synonimów umożliwia definiowanie równoważnych terminów i rozszerzyć zakres kwerendy.
* [Obsługa efektywnie indeksowanie obiektów blob tekstu](https://docs.microsoft.com/azure/search/search-howto-indexing-azure-blob-storage#IndexingPlainText). Nowy `text` analizowania tryb indeksatory obiektów Blob platformy Azure w znacznie zwiększa wydajność indeksowania.
* [Statystyka interfejsu API usługi](https://docs.microsoft.com/rest/api/searchservice/get-service-statistics). Wyświetlanie bieżącego użycia i limitów zasobów w usłudze Azure Search przy użyciu tego nowego interfejsu API.

<a name="UpgradeSteps"></a>

## <a name="steps-to-upgrade"></a>Kroki niezbędne do uaktualnienia
Jeśli uaktualniasz z wersją GA 2015-02-28 lub 2016-09-01, prawdopodobnie nie musisz dokonać zmian w kodzie, inny niż numer wersji. Tylko sytuacji, w których konieczne może być zmiana kodu są:

* Twój kod nie powiedzie się, nierozpoznany właściwości są zwracane w odpowiedzi interfejsu API. Domyślnie aplikacja powinien ignorować właściwości, których nie rozumie.
* Twój kod będzie się powtarzał żądań interfejsu API i próbuje ponownie wysłać je do nowej wersji interfejsu API. Na przykład, to może się zdarzyć, jeśli aplikacja będzie nadal występować, tokeny kontynuacji zwrócony z interfejsu API wyszukiwania (Aby uzyskać więcej informacji, poszukaj `@search.nextPageParameters` w [dokumentacja interfejsu API wyszukiwania](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)).

Jeśli jedno z tych sytuacji odnoszą się do Ciebie, następnie konieczne może być odpowiednio zmień swój kod. W przeciwnym razie żadne zmiany nie powinny być wymagane, jeśli nie chcesz rozpocząć korzystanie z [nowe funkcje](#WhatsNew) wersji 2017-11-11.

Jeśli uaktualniasz z interfejsu api — wersja zapoznawcza powyższych ma również zastosowanie, ale należy również pamiętać, że niektóre funkcje w wersji zapoznawczej nie są dostępne w wersji 2017-11-11:

* Azure Blob Storage indeksatorze obsługi pliki CSV i obiektów blob zawierający tablice notacji JSON.
* Zapytania "Więcej następująco"

Jeśli Twój kod korzysta z tych funkcji, nie można uaktualnić do 2017-11-11 bez usuwania swojej użytkowania.

> [!IMPORTANT]
> Należy pamiętać, w wersji zapoznawczej interfejsy API są przeznaczone do testowania i oceny i nie powinny być używane w środowiskach produkcyjnych.
> 
> 

## <a name="conclusion"></a>Podsumowanie
Jeśli potrzebujesz, aby uzyskać więcej informacji na temat korzystania z interfejsu API REST usługi Azure Search Service, zobacz ostatnio zaktualizowane [dokumentacja interfejsu API](https://docs.microsoft.com/rest/api/searchservice/) w witrynie MSDN.

Chętnie poznamy Twoją opinię w usłudze Azure Search. Jeśli napotkasz problemy, możesz poprosić nas o pomoc na [forum usługi Azure Search w witrynie MSDN](https://social.msdn.microsoft.com/Forums/azure/home?forum=azuresearch) lub [StackOverflow](http://stackoverflow.com/). Jeśli masz zadać pytanie o usłudze Azure Search w witrynie StackOverflow, upewnij się, że oznaczyć go za pomocą `azure-search`.

Dziękujemy za pomocą usługi Azure Search.

