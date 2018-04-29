---
title: Uaktualnianie do usługi Azure Search interfejsu API REST usługi wersji 2016-09-01 | Dokumentacja firmy Microsoft
description: Uaktualnianie do usługi Azure Search interfejsu API REST usługi wersji 2016-09-01
author: brjohnstmsft
manager: jlembicz
services: search
ms.service: search
ms.devlang: rest-api
ms.topic: conceptual
ms.date: 10/27/2016
ms.author: brjohnst
ms.openlocfilehash: ea901462677d42d90007a2130825bd3b382407f2
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2018
---
# <a name="upgrading-to-the-azure-search-service-rest-api-version-2016-09-01"></a>Uaktualnianie do usługi Azure Search interfejsu API REST usługi wersji 2016-09-01
Jeśli używasz wersji 2015-02-28 lub 2015-02-28-Preview z [interfejsu API REST usługi Azure Search](https://msdn.microsoft.com/library/azure/dn798935.aspx), ten artykuł pomoże Ci uaktualnienie aplikacji używać dalej ogólnie dostępna wersja interfejsu API 2016-09-01.

Wersja 2016-09-01 interfejsu API REST zawiera pewne zmiany z wcześniejszych wersji. Są to przede wszystkim zgodne z poprzednimi wersjami, tak więc zmiana kodu powinny wymagać tylko minimalnym wysiłku, w zależności od instalowanej wersji używanego wcześniej. Zobacz [kroki niezbędne do uaktualnienia](#UpgradeSteps) instrukcje dotyczące sposobu zmiany kodu do nowej wersji interfejsu API.

> [!NOTE]
> Wystąpienia usługi Azure Search obsługuje różne wersje interfejsu API REST, w tym najnowsze. Możesz użyć wersji, gdy nie jest już najnowszego, ale zaleca się przeprowadzenie migracji swój kod, aby używać najnowszej wersji.

<a name="WhatsNew"></a>

## <a name="whats-new-in-version-2016-09-01"></a>Nowości w wersji 2016-09-01
Wersja 2016-09-01 jest drugi ogólnie dostępna wersja interfejsu API REST usługi wyszukiwanie Azure. Nowe funkcje w tej wersji interfejsu API:

* [Niestandardowe analizatorów](https://aka.ms/customanalyzers), co pozwala użytkownikowi na przejęcie kontroli nad procesem konwersji tekstu na tokeny można indeksować i wyszukiwanie.
* [Magazyn obiektów Blob Azure](search-howto-indexing-azure-blob-storage.md) i [Azure Table Storage](search-howto-indexing-azure-tables.md) indeksatorów, które umożliwiają łatwe importowanie danych z magazynu Azure do usługi Azure Search na harmonogram lub na żądanie.
* [Pole — mapowanie](search-indexer-field-mappings.md), które pozwalają dostosować sposób indeksatory importowania danych do usługi Azure Search.
* Elementy etag, które pozwalają na aktualizację definicji indeksów, indeksatorów i źródeł danych w sposób bezpieczny współbieżności. 

<a name="UpgradeSteps"></a>

## <a name="steps-to-upgrade"></a>Kroki niezbędne do uaktualnienia
Jeśli uaktualniasz z wersji 2015-02-28, prawdopodobnie nie będziesz mieć wprowadzić zmiany w kodzie, inne niż zmiany numeru wersji. Tylko sytuacji, w których konieczne może być zmiana kodu są:

* Kod kończy się niepowodzeniem, nierozpoznany właściwości są zwracane w odpowiedzi interfejsu API. Domyślnie aplikacja ignorować właściwości, których nie zna.
* Kod będzie się powtarzał żądania interfejsu API i spróbuje ponownie wysłać je do nowej wersji interfejsu API. Na przykład może się zdarzyć, aplikacja będzie nadal występował tokeny kontynuacji zwrócony z interfejsu API Search (Aby uzyskać więcej informacji, poszukaj `@search.nextPageParameters` w [odwołanie wyszukiwania do interfejsu API](https://msdn.microsoft.com/library/azure/dn798927.aspx#Anchor_1)).

Jeśli jeden z tych sytuacji dotyczy użytkownika, następnie należy odpowiednio zmienić swój kod. W przeciwnym razie żadne zmiany jeśli są niezbędne, chyba że chcesz rozpocząć korzystanie z [nowe funkcje](#WhatsNew) wersji 2016-09-01.

Jeśli uaktualniasz wersji 2015-02-28-Preview powyższych ma również zastosowanie, ale należy wziąć pod uwagę, że niektóre funkcje wersji zapoznawczej nie są dostępne w wersji 2016-09-01:

* Magazyn obiektów Blob indeksatora pomocy technicznej platformy Azure dla plików CSV i obiektów blob zawierający tablice notacji JSON.
* Synonimy
* Zapytania "Więcej postać"

Jeśli kod korzysta z tych funkcji, nie można uaktualnić do wersji 2016-09-01 bez usuwania użycie z nich.

> [!IMPORTANT]
> Należy pamiętać, Podgląd interfejsy API są przeznaczone do testowania i ocenie, a nie powinna być używana w środowisku produkcyjnym.
> 
> 

## <a name="conclusion"></a>Podsumowanie
Aby uzyskać więcej informacji na temat używania interfejsu API REST usługi Azure Search Service, zobacz niedawno zaktualizowanego [dokumentacja interfejsu API](https://msdn.microsoft.com/library/azure/dn798935.aspx) w witrynie MSDN.

Chętnie poznamy Twoją opinię w usłudze Azure Search. Jeśli wystąpią problemy, możesz poprosić nas o pomoc na [forum usługi Azure Search w witrynie MSDN](https://social.msdn.microsoft.com/Forums/azure/home?forum=azuresearch) lub [StackOverflow](http://stackoverflow.com/). Jeśli pytania dotyczące usługi Azure Search w witrynie StackOverflow, upewnij się, że za pomocą tagów `azure-search`.

Dziękujemy za skorzystanie z usługi Azure Search!

