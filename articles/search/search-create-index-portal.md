---
title: Tworzenie indeksu wyszukiwania w witrynie Azure portal
titleSuffix: Azure Cognitive Search
description: Dowiedz się, jak utworzyć indeks dla usługi Azure Cognitive Search przy użyciu wbudowanego projektanta indeksów portalu.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: f2e875c625431867e6e83cfd1e0b2c6d7a2781f7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74112848"
---
# <a name="create-an-azure-cognitive-search-index-in-the-portal"></a>Tworzenie indeksu usługi Azure Cognitive Search w portalu

Usługa Azure Cognitive Search zawiera wbudowany projektant indeksów w portalu przydatny dla prototypów lub tworzenia [indeksu wyszukiwania](search-what-is-an-index.md) hostowanego w usłudze Azure Cognitive Search. Narzędzie jest używane do budowy schematu. Po zapisaniu definicji pusty indeks staje się w pełni wyrażona w usłudze Azure Cognitive Search. To, jak go załadować z przeszukiwalną zawartością zależy od Ciebie.

Projektant indeksu jest tylko jedno podejście do tworzenia indeksu. Alternatywnie można utworzyć i załadować indeks za pomocą [Kreatora importu danych](search-get-started-portal.md). Kreator działa tylko z indeksami, które sam tworzy. Programowo można utworzyć indeks przy użyciu interfejsów API [.NET](search-create-index-dotnet.md) lub [REST.](search-create-index-rest-api.md)

## <a name="start-index-designer"></a>Rozpocznij projektanta indeksów

1. Zaloguj się do witryny [Azure Portal](https://portal.azure.com) i otwórz pulpit nawigacyjny usługi. Możesz kliknąć pozycję **Wszystkie usługi** na pasku dostępu, aby poszukać istniejących usług wyszukiwania w bieżącej subskrypcji. 

2. Kliknij łącze **Dodaj indeks** na pasku poleceń u góry strony.

   ![Dodawanie łącza indeksu na pasku poleceń](media/search-create-index-portal/add-index.png "Dodawanie łącza indeksu na pasku poleceń")

3. Nazwij swój indeks usługi Azure Cognitive Search. Nazwy indeksów są odwoływane w operacjach indeksowania i kwerend. Nazwa indeksu staje się częścią adresu URL punktu końcowego używanego w połączeniach z indeksem i do wysyłania żądań HTTP w interfejsie API REST usługi Azure Cognitive Search.

   * Wpisywanie zacznij od litery.
   * Użyj tylko małych liter, cyfr i łączników („-”).
   * Ogranicz długość nazwy do 60 znaków.

## <a name="add-fields"></a>Dodawanie pól

Kompozycja indeksu obejmuje *kolekcję pól*, która definiuje dane z możliwością wyszukiwania w indeksie. W sumie kolekcja pól określa strukturę dokumentów, które są przekazywane oddzielnie. Kolekcja Fields zawiera pola wymagane i opcjonalne, nazwane i wpisane, z atrybutami indeksu, które określają, jak pole może być używane.

1. Dodaj pola, aby w pełni określić dokumenty, które zostaną przekazane, ustawiając [typ danych](https://docs.microsoft.com/rest/api/searchservice/supported-data-types) dla każdego z nich. Na przykład, jeśli dokumenty składają się z *identyfikatora hotelu*, *nazwy hotelu,* *adresu,* *miasta*i *regionu,* utwórz odpowiednie pole dla każdego z nich w indeksie. Zapoznaj się ze [wskazówkami dotyczącymi projektu w poniższej sekcji,](#design) aby uzyskać pomoc dotyczącą ustawiania atrybutów.

1. Jeśli dane przychodzące mają charakter hierarchiczny, schemat powinien zawierać [typy złożone](search-howto-complex-data-types.md) reprezentujące struktury zagnieżdżone. Wbudowany przykładowy zestaw danych Hotele ilustruje złożone typy przy użyciu adresu (zawiera wiele podpola), który ma relację jeden do jednego z każdym hotelem, oraz kolekcji kompleksu Pokoje, w której z każdym hotelem jest skojarzonych wiele pokoi. 

1. Określ pole *klucza* typu Edm.String. Pole klucza jest obowiązkowe dla każdego indeksu usługi Azure Cognitive Search i musi być ciągiem. Wartości dla tego pola muszą jednoznacznie identyfikować każdy dokument. Domyślnie pole nosi nazwę *id*, ale można ją zmienić, o ile ciąg spełnia [reguły nazewnictwa](https://docs.microsoft.com/rest/api/searchservice/Naming-rules). Na przykład, jeśli kolekcja pól zawiera *identyfikator hotelu,* należy wybrać, że dla klucza. 

1. Ustaw atrybuty dla każdego pola. Projektant indeksu wyklucza wszystkie atrybuty, które są nieprawidłowe dla typu danych, ale nie sugeruje, co należy uwzględnić. Zapoznaj się ze wskazówkami w następnej sekcji, aby zrozumieć, do czego służą atrybuty.

    Dokumentacja interfejsu API usługi Azure Cognitive Search zawiera przykłady kodu zawierające prosty indeks *hoteli.* Na poniższym zrzucie ekranu można zobaczyć definicję indeksu, w tym analizator języka francuskiego określony podczas definiowania indeksu, który można odtworzyć jako ćwiczenie praktyczne w portalu.

    ![Indeks demonstracyjne hoteli](media/search-create-index-portal/field-definitions.png "Indeks demonstracyjne hoteli")

1. Po zakończeniu kliknij przycisk **Utwórz,** aby zapisać i utworzyć indeks.

<a name="design"></a>

## <a name="set-attributes"></a>Ustaw atrybuty

Chociaż możesz w dowolnym momencie dodać nowe pola, istniejące definicje pól są zablokowane przez cały czas istnienia indeksu. Z tego powodu deweloperzy zazwyczaj używają portalu do tworzenia prostych indeksów, testowania pomysłów lub używania stron portalu w celu wyszukania ustawień. Częsta iteracja po projekcie indeksu jest bardziej wydajna, jeśli stosujesz podejście oparte na kodzie, które pozwala na odbudowanie indeksu w prosty sposób.

Analizatory i sugestory są kojarzone z polami przed zapisaniem indeksu. Pamiętaj, aby dodać analizatory języka lub sugesty do definicji indeksu podczas tworzenia go.

Pola ciągów są często oznaczone jako **Możliwość sortowania** i **Możliwość pobierania**. Pola używane w celu zawężenia wyników wyszukiwania zawierają oznaczenia **Możliwość sortowania**, **Możliwość filtrowania** i **Możliwość tworzenia aspektów**.

Atrybuty pól określają, jak używane jest dane pole, np. czy jest używane w wyszukiwaniu pełnotekstowym, nawigacji aspektowej, operacjach sortowania i tak dalej. Następująca tabela zawiera opis każdego atrybutu.

|Atrybut|Opis|  
|---------------|-----------------|  
|**Przeszukiwania**|Wyszukiwanie pełnotekstowe, które można poddawać analizie leksykalnej, takiej jak dzielenie wyrazów podczas indeksowania. Ustawienie wartości „sunny day” dla pola z możliwością wyszukiwania spowoduje podział na dwa oddzielne tokeny „sunny” i „day”. Aby uzyskać więcej informacji, zobacz [Jak działa wyszukiwanie pełnotekstowe](search-lucene-query-architecture.md).|  
|**Filterable**|Odwołania do tego atrybutu znajdują się w zapytaniach **$filter**. Pola z możliwością filtrowania typu `Edm.String` lub `Collection(Edm.String)` nie są poddawane dzieleniu wyrazów, więc porównania dotyczą tylko dokładnych dopasowań. Ustawienie pola filtrowania na wartość „sunny day” spowoduje, że pole typu `$filter=f eq 'sunny'` nie wyświetli żadnych wyników, ale `$filter=f eq 'sunny day'` już tak. |  
|**Sortowania**|Domyślnie system sortuje pozycje według wyników, ale można również ustawić sortowanie według poszczególnych pól w dokumentach. Pola typu `Collection(Edm.String)` nie mogą być **sortowane**. |  
|**fasetowalne**|Zwykle używany w prezentacji wyników wyszukiwania, która zawiera liczbę trafień według kategorii (na przykład hotele znajdujące się w określonym mieście). Nie można używać tej opcji z polami typu `Edm.GeographyPoint`. Pola typu `Edm.String` z możliwością **filtrowania**, **sortowania** lub **tworzenia aspektów** mogą mieć maksymalnie 32 kilobajty długości. Aby uzyskać więcej informacji, zobacz [Create Index (REST API)](https://docs.microsoft.com/rest/api/searchservice/create-index) (Tworzenie indeksu [interfejs REST API]).|  
|**key**|Unikatowy identyfikator dokumentów w indeksie. Można wybrać tylko jedno pole klucza i musi ono być typu `Edm.String`.|  
|**Pobieranie**|Określa, czy pole może być zwracane w wynikach wyszukiwania. Jest przydatny, jeśli chcesz użyć pola (np. *marża zysku*) z możliwością filtrowania, sortowania lub oceniania, ale nie chcesz, aby to pole było widoczne dla użytkownika końcowego. Ten atrybut musi przyjmować wartość `true` dla pól typu `key`.|  

## <a name="next-steps"></a>Następne kroki

Po utworzeniu indeksu usługi Azure Cognitive Search można przejść do następnego kroku: [przekazywania danych z przeszukiwalnych do indeksu.](search-what-is-data-import.md)

Alternatywnie, można również [przyjrzeć](search-what-is-an-index.md)się bliżej indeksy . Oprócz kolekcji pól indeks określa również analizatory, sugestory, profile oceniania i ustawienia specyfikacji CORS. Portal zawiera strony z kartami umożliwiające definiowanie najczęściej występujących elementów: pól, analizatorów i sugestorów. Aby utworzyć lub zmodyfikować inne elementy, można użyć interfejsu API REST lub zestawu .NET SDK.

## <a name="see-also"></a>Zobacz też

 [Jak działa wyszukiwanie pełnotekstowe](search-lucene-query-architecture.md)  
 [Interfejs API REST usługi Search](https://docs.microsoft.com/rest/api/searchservice/) [Zestaw .NET SDK](https://docs.microsoft.com/dotnet/api/overview/azure/search?view=azure-dotnet)

