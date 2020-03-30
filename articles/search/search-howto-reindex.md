---
title: Odbudowywej indeksu wyszukiwania
titleSuffix: Azure Cognitive Search
description: Dodaj nowe elementy, zaktualizuj istniejące elementy lub dokumenty lub usuń przestarzałe dokumenty w pełnej przebudowie lub częściowej indeksowaniu, aby odświeżyć indeks usługi Azure Cognitive Search.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/14/2020
ms.openlocfilehash: 58b60a0eee8ab407709f33911d3c6b13ffbf301a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77498374"
---
# <a name="how-to-rebuild-an-index-in-azure-cognitive-search"></a>Jak odbudować indeks w usłudze Azure Cognitive Search

W tym artykule wyjaśniono, jak odbudować indeks usługi Azure Cognitive Search, okoliczności, w których przebudowy są wymagane, oraz zalecenia dotyczące łagodzenia wpływu odbudowy na bieżące żądania zapytań.

*Przebudowa* odnosi się do upuszczania i odtworzenia struktur danych fizycznych skojarzonych z indeksem, w tym wszystkich indeksów odwróconych opartych na polach. W usłudze Azure Cognitive Search nie można upuszczać i odtwarzać poszczególnych pól. Aby odbudować indeks, cały magazyn pól musi zostać usunięty, odtworzony na podstawie istniejącego lub poprawionego schematu indeksu, a następnie ponownie wypełniony danymi wypchniętymi do indeksu lub pobranymi ze źródeł zewnętrznych. 

Często przebudowywać indeksy podczas tworzenia, ale może być również konieczne odbudować indeks na poziomie produkcji, aby uwzględnić zmiany strukturalne, takie jak dodawanie złożonych typów lub dodawanie pól do sugestatorów.

## <a name="rebuild-conditions"></a>Warunki przebudowy

Upuść i ponownie utworzyć indeks, jeśli którykolwiek z następujących warunków są spełnione. 

| Warunek | Opis |
|-----------|-------------|
| Zmienianie definicji pola | Zmiana nazwy pola, typu danych lub określonych [atrybutów indeksu](https://docs.microsoft.com/rest/api/searchservice/create-index) (z których można przeszukiwać, filtrować, sortować, można go sortować) wymaga pełnej przebudowy. |
| Przypisywanie analizatora do pola | [Analizatory](search-analyzers.md) są definiowane w indeksie, a następnie przypisane do pól. W dowolnym momencie można dodać nową definicję analizatora do indeksu, ale analizator można *przypisać* tylko po utworzeniu pola. Dotyczy to właściwości **analizatora** i **indexanalyzer.** Właściwość **searchAnalyzer** jest wyjątkiem (tę właściwość można przypisać do istniejącego pola). |
| Aktualizowanie lub usuwanie definicji analizatora w indeksie | Nie można usunąć lub zmienić istniejącą konfigurację analizatora (analizator, tokenizator, filtr tokenu lub filtr char) w indeksie, chyba że odbudujesz cały indeks. |
| Dodawanie pola do sugestatora | Jeśli pole już istnieje i chcesz dodać je do konstrukcji [sugestatorów,](index-add-suggesters.md) należy odbudować indeks. |
| Usuwanie pola | Aby fizycznie usunąć wszystkie ślady pola, należy odbudować indeks. Gdy natychmiastowa przebudowa nie jest praktyczna, można zmodyfikować kod aplikacji, aby wyłączyć dostęp do pola "usunięte" lub użyć [parametru kwerendy $select,](search-query-odata-select.md) aby wybrać pola, które są reprezentowane w zestawie wyników. Fizycznie definicja pola i zawartość pozostają w indeksie do następnej przebudowy, po zastosowaniu schematu, który pomija dane pole. |
| Przełączanie warstw | Jeśli potrzebujesz więcej pojemności, nie ma żadnych uaktualnień w miejscu w witrynie Azure portal. Należy utworzyć nową usługę, a indeksy muszą być budowane od podstaw na nowej usłudze. Aby ułatwić automatyzację tego procesu, można użyć przykładowego kodu **przywracania kopii zapasowej indeksu** w tym [przykładowym repo usługi Azure Cognitive Search .NET](https://github.com/Azure-Samples/azure-search-dotnet-samples). Ta aplikacja będzie kopii zapasowej indeksu do serii plików JSON, a następnie ponownie utworzyć indeks w usłudze wyszukiwania, które określisz.|

## <a name="update-conditions"></a>Warunki aktualizacji

Wiele innych modyfikacji można wprowadzić bez wpływu na istniejące struktury fizyczne. W szczególności następujące zmiany *nie* wymagają przebudowy indeksu. W przypadku tych zmian można [zaktualizować definicję indeksu](https://docs.microsoft.com/rest/api/searchservice/update-index) ze zmianami.

+ Dodawanie nowego pola
+ Ustawianie atrybutu **możliwego do pobrania** w istniejącym polu
+ Ustawianie **analizy wyszukiwania** w istniejącym polu
+ Dodawanie nowej definicji analizatora w indeksie
+ Dodawanie, aktualizowanie lub usuwanie profilów oceniania
+ Dodawanie, aktualizowanie lub usuwanie ustawień cors
+ Dodawanie, aktualizowanie lub usuwanie synonimówMapy

Po dodaniu nowego pola istniejące dokumenty indeksowane otrzymują wartość null dla nowego pola. W przypadku przyszłego odświeżania danych wartości z zewnętrznych danych źródłowych zastępują wartości null dodane przez usługę Azure Cognitive Search. Aby uzyskać więcej informacji na temat aktualizowania zawartości indeksu, zobacz [Dodawanie, aktualizowanie lub usuwanie dokumentów](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents).

## <a name="how-to-rebuild-an-index"></a>Jak odbudować indeks

Podczas tworzenia schematu indeksu zmienia się często. Można zaplanować dla niego, tworząc indeksy, które mogą być usuwane, ponownie i szybko ponownie za pomocą małego reprezentatywnego zestawu danych.

W przypadku aplikacji już w produkcji zaleca się utworzenie nowego indeksu, który jest uruchamiany obok istniejącego indeksu, aby uniknąć przestojów kwerend. Kod aplikacji zapewnia przekierowanie do nowego indeksu.

Indeksowanie nie działa w tle, a usługa zrównoważy dodatkowe indeksowanie względem trwających zapytań. Podczas indeksowania można [monitorować żądania zapytań](search-monitor-queries.md) w portalu, aby upewnić się, że zapytania są wypełniane w odpowiednim czasie.

1. Określ, czy przebudowa jest wymagana. Jeśli po prostu dodajesz pola lub zmieniasz część indeksu, która nie jest związana z polami, możesz po prostu [zaktualizować definicję](https://docs.microsoft.com/rest/api/searchservice/update-index) bez usuwania, ponownego tworzenia i pełnego ponownego ładowania.

1. [Uzyskaj definicję indeksu](https://docs.microsoft.com/rest/api/searchservice/get-index) w przypadku, gdy jest to potrzebne do wykorzystania w przyszłości.

1. [Upuść istniejący indeks,](https://docs.microsoft.com/rest/api/searchservice/delete-index)przy założeniu, że nie są uruchomione nowe i stare indeksy obok siebie. 

   Wszystkie zapytania dotyczące kierowania tego indeksu są natychmiast usuwane. Należy pamiętać, że usunięcie indeksu jest nieodwracalne, niszcząc magazyn fizyczny dla kolekcji pól i innych konstrukcji. Zatrzymaj się, aby pomyśleć o konsekwencjach przed upuszczeniem go. 

1. [Utwórz poprawiony indeks,](https://docs.microsoft.com/rest/api/searchservice/create-index)w którym treść żądania zawiera zmienione lub zmodyfikowane definicje pól.

1. [Załaduj indeks z dokumentami](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents) ze źródła zewnętrznego.

Podczas tworzenia indeksu magazyn fizyczny jest przydzielany dla każdego pola w schemacie indeksu, z odwróconym indeksem utworzonym dla każdego pola z wyszukujalnym. Pola, które nie można przeszukiwać, mogą być używane w filtrach lub wyrażeniach, ale nie mają odwróconych indeksów i nie można przeszukiwać pełnotekstowego ani rozmytego. W przebudowie indeksu te odwrócone indeksy są usuwane i odtworzone na podstawie schematu indeksu, który podasz.

Po załadowaniu indeksu odwrócony indeks każdego pola jest wypełniany wszystkimi unikatowymi, tokenizowanymi wyrazami z każdego dokumentu, z mapą do odpowiednich identyfikatorów dokumentu. Na przykład podczas indeksowania zestawu danych hoteli odwrócony indeks utworzony dla pola Miasto może zawierać terminy dla Seattle, Portland i tak dalej. Dokumenty, które zawierają Seattle lub Portland w polu Miasto, będą miały swój identyfikator dokumentu wymieniony obok terminu. W każdej operacji [Dodaj, Aktualizuj lub Usuń,](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents) warunki i lista identyfikatorów dokumentów są odpowiednio aktualizowane.

> [!NOTE]
> Jeśli masz rygorystyczne wymagania umowy SLA, można rozważyć inicjowania obsługi administracyjnej nowej usługi specjalnie dla tej pracy, z rozwoju i indeksowania występujących w pełnej izolacji od indeksu produkcji. Oddzielna usługa działa na własnym sprzęcie, eliminując wszelkie możliwości rywalizacji o zasoby. Po zakończeniu tworzenia należy pozostawić nowy indeks w miejscu, przekierowywanie zapytań do nowego punktu końcowego i indeksu lub uruchomić gotowy kod, aby opublikować poprawiony indeks w oryginalnej usłudze Azure Cognitive Search. Obecnie nie ma mechanizmu przenoszenia indeksu gotowego do użycia do innej usługi.

## <a name="check-for-updates"></a>Sprawdź aktualizacje

Można rozpocząć kwerendy indeksu, jak tylko pierwszy dokument jest ładowany. Jeśli znasz identyfikator dokumentu, interfejs [API REST dokumentu odnośnego](https://docs.microsoft.com/rest/api/searchservice/lookup-document) zwraca określony dokument. W przypadku szerszych testów należy poczekać, aż indeks jest w pełni załadowany, a następnie użyć kwerend, aby zweryfikować kontekst, który ma zostać wyświetlony.

Aby sprawdzić, czy nie ma zaktualizowanej zawartości, można użyć [Eksploratora wyszukiwania](search-explorer.md) lub narzędzia do testowania sieci Web, takiego jak [Listonosz.](search-get-started-postman.md)

Jeśli pole zostało dodane lub zmieniono jego nazwę, użyj [$select,](search-query-odata-select.md) aby zwrócić to pole:`search=*&$select=document-id,my-new-field,some-old-field&$count=true`

## <a name="see-also"></a>Zobacz też

+ [Omówienie indeksatora](search-indexer-overview.md)
+ [Indeks dużych zbiorów danych na dużą skalę](search-howto-large-index.md)
+ [Indeksowanie w portalu](search-import-data-portal.md)
+ [Indeksator bazy danych SQL platformy Azure](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)
+ [Indeksator usługi Azure Cosmos DB](search-howto-index-cosmosdb.md)
+ [Indeksator usługi Azure Blob Storage](search-howto-indexing-azure-blob-storage.md)
+ [Indeksator usługi Azure Table Storage](search-howto-indexing-azure-tables.md)
+ [Zabezpieczenia w usłudze Azure Cognitive Search](search-security-overview.md)