---
title: Tworzenie indeksu usługi Azure Search w witrynie Azure portal — usługa Azure Search
description: Dowiedz się, jak utworzyć indeks usługi Azure Search przy użyciu projektantów wbudowanych indeks portalu.
manager: cgronlun
author: heidisteen
services: search
ms.service: search
ms.devlang: NA
ms.topic: conceptual
ms.date: 02/16/2019
ms.author: heidist
ms.custom: seodec2018
ms.openlocfilehash: 6a2bac71c37cc750eb24e3492ecdcdf0b2333cce
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60817316"
---
# <a name="create-an-azure-search-index-in-the-portal"></a>Tworzenie indeksu usługi Azure Search w portalu

Usługa Azure Search zawiera indeks wbudowanego projektanta w portalu jest przydatne w przypadku prototypy lub tworzenia [indeksu wyszukiwania](search-what-is-an-index.md) hostowanych w usłudze Azure Search. To narzędzie służy do tworzenia schematów. Po zapisaniu definicji pusty indeks staje się całkowicie wyrażone w usłudze Azure Search. Jak załadować je przy użyciu danych z możliwością wyszukiwania zależy od użytkownika.

Projektant indeksu jest tylko jedno podejście do tworzenia indeksu. Programowe tworzenie indeksu przy użyciu [.NET](search-create-index-dotnet.md) lub [REST](search-create-index-rest-api.md) interfejsów API.

## <a name="start-index-designer"></a>Uruchom program designer indeksu

1. Zaloguj się do witryny [Azure Portal](https://portal.azure.com) i otwórz pulpit nawigacyjny usługi. Możesz kliknąć pozycję **Wszystkie usługi** na pasku dostępu, aby poszukać istniejących usług wyszukiwania w bieżącej subskrypcji. 

2. Kliknij przycisk **Dodaj indeks** łączy na pasku poleceń u góry strony.

   ![Dodaj indeks link na pasku poleceń](media/search-create-index-portal/add-index.png "Dodaj indeks link na pasku poleceń")

3. Nadaj nazwę indeksowi usługi Azure Search. Operacje indeksowania i zapytanie odwołuje się ich nazw. Nazwa indeksu staje się częścią adresu URL punktu końcowego używanego w przypadku połączeń z indeksem i wysyłania żądań HTTP za pomocą interfejsu API REST usługi Azure Search.

   * Wpisywanie zacznij od litery.
   * Użyj tylko małych liter, cyfr i łączników („-”).
   * Ogranicz długość nazwy do 60 znaków.

## <a name="add-fields"></a>Dodawanie pól

Kompozycja indeksu obejmuje *kolekcję pól*, która definiuje dane z możliwością wyszukiwania w indeksie. Cała kolekcji pól określa strukturę dokumentów, które są przekazywane oddzielnie. Kolekcja pól zawiera wymagane i opcjonalne pola nazwane i wpisane z atrybutami indeksu, które określają, jak można używać pola.

1. Dodaj pola, aby w pełni określić dokumenty, możesz przekazać, ustawienie [— typ danych](https://docs.microsoft.com/rest/api/searchservice/supported-data-types) dla każdej z nich. Na przykład, jeśli dokumenty składają się z *identyfikator hotelu*, *Nazwa hotelu*, *adres*, *Miasto*, i *region*, utwórz odpowiednie pole dla każdego z nich w indeksie. Przegląd [wskazówki w dalszej części tego artykułu projektowania](#design) Aby uzyskać pomoc dotyczącą ustawiania atrybutów.

2. Określ *klucz* pole typu Edm.String. Wartości dla tego pola musi jednoznacznego zidentyfikowania poszczególnych dokumentów. Domyślnie pole nosi nazwę *id*, ale można ją zmienić, o ile ciąg spełnia [reguły nazewnictwa](https://docs.microsoft.com/rest/api/searchservice/Naming-rules). Na przykład, jeśli Twoja kolekcja pól zawiera *identyfikator hotelu*, należy wybrać, związane z kluczem. Pole klucza jest wymagane dla każdego indeksu usługi Azure Search i musi mieć postać ciągu.

3. Ustaw atrybuty dla każdego pola. Projektanta indeksów wyłącza wszelkie atrybuty, które są nieprawidłowe dla typu danych, ale nie sugerują elementów do uwzględnienia. Zapoznaj się ze wskazówkami w następnej sekcji, aby zrozumieć, jakie są atrybuty.

    Dokumentacja interfejsu API usługi Azure Search zawiera przykłady kodu korzystającego z prostego indeksu *hotele*. W poniższym zrzucie ekranu, można zobaczyć definicję indeksu, w tym analizator języka francuskiego określony podczas definiowania indeksu, który można odtworzyć jako ćwiczenie praktyczne w portalu.

    ![Indeks wersji demonstracyjnej hotele](media/search-create-index-portal/field-definitions.png "hotele pokaz indeksu")

4. Po zakończeniu kliknij przycisk **Utwórz** Aby zapisać i utworzyć indeks.

<a name="design"></a>

## <a name="set-attributes"></a>Ustaw atrybuty

Chociaż możesz w dowolnym momencie dodać nowe pola, istniejące definicje pól są zablokowane przez cały czas istnienia indeksu. Z tego powodu deweloperzy zazwyczaj używają portalu do tworzenia prostych indeksów, testowania pomysłów lub używania stron portalu w celu wyszukania ustawień. Częsta iteracja po projekcie indeksu jest bardziej wydajna, jeśli stosujesz podejście oparte na kodzie, które pozwala na odbudowanie indeksu w prosty sposób.

Analizatory i sugestory są kojarzone z polami przed zapisaniem indeksu. Pamiętaj dodać analizatory lub sugestory języka do definicji indeksu, podczas jego tworzenia.

Pola ciągów są często oznaczone jako **Możliwość sortowania** i **Możliwość pobierania**. Pola używane w celu zawężenia wyników wyszukiwania zawierają oznaczenia **Możliwość sortowania**, **Możliwość filtrowania** i **Możliwość tworzenia aspektów**.

Atrybuty pól określają, jak używane jest dane pole, np. czy jest używane w wyszukiwaniu pełnotekstowym, nawigacji aspektowej, operacjach sortowania i tak dalej. Następująca tabela zawiera opis każdego atrybutu.

|Atrybut|Opis|  
|---------------|-----------------|  
|**Wyszukiwanie**|Wyszukiwanie pełnotekstowe, które można poddawać analizie leksykalnej, takiej jak dzielenie wyrazów podczas indeksowania. Ustawienie wartości „sunny day” dla pola z możliwością wyszukiwania spowoduje podział na dwa oddzielne tokeny „sunny” i „day”. Aby uzyskać więcej informacji, zobacz [Jak działa wyszukiwanie pełnotekstowe](search-lucene-query-architecture.md).|  
|**Filtrowanie**|Odwołania do tego atrybutu znajdują się w zapytaniach **$filter**. Pola z możliwością filtrowania typu `Edm.String` lub `Collection(Edm.String)` nie są poddawane dzieleniu wyrazów, więc porównania dotyczą tylko dokładnych dopasowań. Ustawienie pola filtrowania na wartość „sunny day” spowoduje, że pole typu `$filter=f eq 'sunny'` nie wyświetli żadnych wyników, ale `$filter=f eq 'sunny day'` już tak. |  
|**Sortowanie**|Domyślnie system sortuje pozycje według wyników, ale można również ustawić sortowanie według poszczególnych pól w dokumentach. Pola typu `Collection(Edm.String)` nie mogą być **sortowane**. |  
|**Tworzenie aspektów**|Zwykle używany w prezentacji wyników wyszukiwania, która zawiera liczbę trafień według kategorii (na przykład hotele znajdujące się w określonym mieście). Nie można używać tej opcji z polami typu `Edm.GeographyPoint`. Pola typu `Edm.String` z możliwością **filtrowania**, **sortowania** lub **tworzenia aspektów** mogą mieć maksymalnie 32 kilobajty długości. Aby uzyskać więcej informacji, zobacz [Create Index (REST API)](https://docs.microsoft.com/rest/api/searchservice/create-index) (Tworzenie indeksu [interfejs REST API]).|  
|**Klucz**|Unikatowy identyfikator dokumentów w indeksie. Można wybrać tylko jedno pole klucza i musi ono być typu `Edm.String`.|  
|**Pobieranie**|Określa, czy pole może być zwracane w wynikach wyszukiwania. Jest przydatny, jeśli chcesz użyć pola (np. *marża zysku*) z możliwością filtrowania, sortowania lub oceniania, ale nie chcesz, aby to pole było widoczne dla użytkownika końcowego. Ten atrybut musi przyjmować wartość `true` dla pól typu `key`.|  

## <a name="next-steps"></a>Kolejne kroki

Po utworzeniu indeksu usługi Azure Search można przejść do następnego kroku: [przekazywanie danych z możliwością wyszukiwania do indeksu](search-what-is-data-import.md).

Alternatywnie można również wykonać [lepiej poznać indeksów](search-what-is-an-index.md). Oprócz kolekcji pól indeks określa również analizatory, sugestory, profile oceniania i ustawienia specyfikacji CORS. Portal zawiera strony z kartami Definiowanie najczęściej występujących elementów: Pól, analizatorów i sugestorów. Aby utworzyć lub zmodyfikować inne elementy, można użyć interfejsu API REST lub zestawu .NET SDK.

## <a name="see-also"></a>Zobacz także

 [Jak działa wyszukiwanie pełnotekstowe](search-lucene-query-architecture.md)  
 [Interfejs API REST usługi Search](https://docs.microsoft.com/rest/api/searchservice/) [Zestaw .NET SDK](https://docs.microsoft.com/dotnet/api/overview/azure/search?view=azure-dotnet)

