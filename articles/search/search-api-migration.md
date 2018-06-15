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
ms.openlocfilehash: 3848f317fd6d760961756f132edf9cbcb5f431ee
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2018
ms.locfileid: "32181974"
---
# <a name="upgrading-to-the-latest-azure-search-service-rest-api-version"></a>Uaktualnianie do najnowszej wersji interfejsu API REST usługi Azure Search
Jeśli używasz wcześniejszej wersji programu [interfejsu API REST usługi Azure Search](https://docs.microsoft.com/rest/api/searchservice/), ten artykuł pomoże Ci uaktualnienie aplikacji używać najnowszej wersji interfejsu API ogólnie dostępna, 2017-11-11.

Wersja 2017-11-11 interfejsu API REST zawiera pewne zmiany z wcześniejszych wersji. Są to przede wszystkim zgodne z poprzednimi wersjami, tak więc zmiana kodu powinny wymagać tylko minimalnym wysiłku, w zależności od instalowanej wersji używanego wcześniej. Zobacz [kroki niezbędne do uaktualnienia](#UpgradeSteps) instrukcje dotyczące sposobu zmiany kodu do nowej wersji interfejsu API.

> [!NOTE]
> Wystąpienia usługi Azure Search obsługuje różne wersje interfejsu API REST, w tym najnowsze. Możesz użyć wersji, gdy nie jest już najnowszego, ale zaleca się przeprowadzenie migracji swój kod, aby używać najnowszej wersji.

<a name="WhatsNew"></a>

## <a name="whats-new-in-version-2017-11-11"></a>Nowości w wersji 2017-11-11
Wersja 2017-11-11 to numer najnowszej wersji interfejsu API REST usługi wyszukiwanie Azure ogólnie dostępna. Nowe funkcje w tej wersji interfejsu API:

* [Synonimy](search-synonyms.md). Nowa funkcja synonimy umożliwia definiowanie równoważnego i zwiększa zakresu zapytania.
* [Obsługa wydajnie indeksowania obiekty BLOB tekstu](https://docs.microsoft.com/azure/search/search-howto-indexing-azure-blob-storage#IndexingPlainText). Nowe `text` analizowania tryb dla indeksatorów obiektów Blob platformy Azure w znacznie poprawia wydajność indeksowania.
* [Usługi interfejsu API statystyk](https://aka.ms/azure-search-stats). Wyświetl bieżące użycie i limity zasobów w usłudze Azure Search z tego nowego interfejsu API.

<a name="UpgradeSteps"></a>

## <a name="steps-to-upgrade"></a>Kroki niezbędne do uaktualnienia
Jeśli uaktualniasz z wersją GA 2015-02-28 lub 2016-09-01, prawdopodobnie nie należy dokonać zmian w kodzie, innych niż numer wersji. Tylko sytuacji, w których konieczne może być zmiana kodu są:

* Kod kończy się niepowodzeniem, nierozpoznany właściwości są zwracane w odpowiedzi interfejsu API. Domyślnie aplikacja ignorować właściwości, których nie zna.
* Kod będzie się powtarzał żądania interfejsu API i spróbuje ponownie wysłać je do nowej wersji interfejsu API. Na przykład może się zdarzyć, aplikacja będzie nadal występował tokeny kontynuacji zwrócony z interfejsu API Search (Aby uzyskać więcej informacji, poszukaj `@search.nextPageParameters` w [odwołanie wyszukiwania do interfejsu API](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)).

Jeśli jeden z tych sytuacji dotyczy użytkownika, następnie należy odpowiednio zmienić swój kod. W przeciwnym razie żadne zmiany jeśli są niezbędne, chyba że chcesz rozpocząć korzystanie z [nowe funkcje](#WhatsNew) wersji 2017-11-11.

Jeśli uaktualniasz interfejsu api — wersja zapoznawcza powyższych ma również zastosowanie, ale należy wziąć pod uwagę, że niektóre funkcje wersji zapoznawczej nie są dostępne w wersji 2017-11-11:

* Magazyn obiektów Blob indeksatora pomocy technicznej platformy Azure dla plików CSV i obiektów blob zawierający tablice notacji JSON.
* Zapytania "Więcej postać"

Jeśli kod korzysta z tych funkcji, nie można uaktualnić do wersji 2017-11-11 bez usuwania użycie z nich.

> [!IMPORTANT]
> Należy pamiętać, Podgląd interfejsy API są przeznaczone do testowania i ocenie, a nie powinna być używana w środowisku produkcyjnym.
> 
> 

## <a name="conclusion"></a>Podsumowanie
Aby uzyskać więcej informacji na temat używania interfejsu API REST usługi Azure Search Service, zobacz niedawno zaktualizowanego [dokumentacja interfejsu API](https://docs.microsoft.com/rest/api/searchservice/) w witrynie MSDN.

Chętnie poznamy Twoją opinię w usłudze Azure Search. Jeśli wystąpią problemy, możesz poprosić nas o pomoc na [forum usługi Azure Search w witrynie MSDN](https://social.msdn.microsoft.com/Forums/azure/home?forum=azuresearch) lub [StackOverflow](http://stackoverflow.com/). Jeśli pytania dotyczące usługi Azure Search w witrynie StackOverflow, upewnij się, że za pomocą tagów `azure-search`.

Dziękujemy za skorzystanie z usługi Azure Search!

