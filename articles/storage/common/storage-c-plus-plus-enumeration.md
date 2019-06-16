---
title: Lista zasobów usługi Azure Storage przy użyciu biblioteki klienta usługi Storage dla języka C++ | Dokumentacja firmy Microsoft
description: Dowiedz się, jak używać listy interfejsów API w bibliotece klienta usługi Microsoft Azure Storage dla języka C++ można wyliczyć kontenerów, obiektów blob, kolejek, tabel i jednostek.
services: storage
author: mhopkins-msft
ms.service: storage
ms.topic: article
ms.date: 01/23/2017
ms.author: mhopkins
ms.reviewer: dineshm
ms.subservice: common
ms.openlocfilehash: edf50b97ff25a67b41bad266df9236145f288409
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "65146878"
---
# <a name="list-azure-storage-resources-in-c"></a>Wyświetla listę zasobów usługi Azure Storage w języku C++
Lista operacji są kluczem do wielu scenariuszy programowania za pomocą usługi Azure Storage. W tym artykule opisano, jak najbardziej wydajny sposób wyliczenie obiektów w usłudze Azure Storage przy użyciu listy interfejsów API dostarczonych w bibliotece klienta usługi Microsoft Azure Storage dla języka C++.

> [!NOTE]
> Ten przewodnik jest przeznaczony dla biblioteki klienta usługi Azure Storage dla języka C++ w wersji 2.x, która jest dostępna za pośrednictwem [NuGet](https://www.nuget.org/packages/wastorage) lub [GitHub](https://github.com/Azure/azure-storage-cpp).
> 
> 

Biblioteka klienta magazynu zapewnia różne metody zapytania lub listy obiektów w usłudze Azure Storage. W tym artykule opisano w następujących scenariuszach:

* Lista kontenerów na koncie usługi
* Wyświetlanie listy obiektów blob w ramach kontenera lub obiektu blob wirtualnego katalogu
* Wyświetl listę kolejek na koncie usługi
* Lista tabel na koncie
* Wykonywanie zapytań dotyczących jednostek w tabeli

Każda z tych metod jest wyświetlana przy użyciu innego przeciążenia dla różnych scenariuszy.

## <a name="asynchronous-versus-synchronous"></a>Asynchroniczne i synchroniczne
Ponieważ biblioteki klienta usługi Storage dla języka C++ został opracowany w górnej części [biblioteki języka C++ REST](https://github.com/Microsoft/cpprestsdk), obsługujemy założenia asynchroniczne operacje przy użyciu [pplx::task](https://microsoft.github.io/cpprestsdk/classpplx_1_1task.html). Na przykład:

```cpp
pplx::task<list_blob_item_segment> list_blobs_segmented_async(continuation_token& token) const;
```

Operacje synchroniczne opakować odpowiednich operacji asynchronicznych:

```cpp
list_blob_item_segment list_blobs_segmented(const continuation_token& token) const
{
    return list_blobs_segmented_async(token).get();
}
```

Jeśli pracujesz z wieloma aplikacjami lub usługami wątkowości, zaleca się używać asynchronicznych interfejsów API bezpośrednio, zamiast tworzenia wątku do wywoływania interfejsów API, co znacznie wpływa na wydajność synchronizacji.

## <a name="segmented-listing"></a>Lista segmentowane
Skalowanie magazynu w chmurze wymaga listy podzielonej na segmenty. Na przykład można mieć za pośrednictwem milion liczba bloków BLOB w kontenerze obiektów blob platformy Azure lub miliard jednostek w tabeli platformy Azure. Nie są teoretycznych cyfr, ale przypadki użycia rzeczywiste.

Dlatego jest to niepraktyczne, aby wyświetlić listę wszystkich obiektów w pojedynczą odpowiedź. Zamiast tego możesz wyświetlić listę obiektów za pomocą stronicowania. Każdy z listy interfejsów API ma *segmentowanych* przeciążenia.

Odpowiedź dla operacji lista segmenty zawiera:

* <i>_segment</i>, który zawiera zestaw wyników zwrócony dla jednego wywołania interfejsu API listy.
* *continuation_token*, która jest przekazywana do następnego wywołania w celu pobrania następnej strony wyników. Jeśli nie ma żadnych innych wyników do zwrócenia, token kontynuacji ma wartość null.

Na przykład typowe wywołanie, aby wyświetlić listę wszystkich obiektów blob w kontenerze może wyglądać podobnie jak poniższy fragment kodu. Kod jest dostępny w naszym [przykłady](https://github.com/Azure/azure-storage-cpp/blob/master/Microsoft.WindowsAzure.Storage/samples/BlobsGettingStarted/Application.cpp):

```cpp
// List blobs in the blob container
azure::storage::continuation_token token;
do
{
    azure::storage::list_blob_item_segment segment = container.list_blobs_segmented(token);
    for (auto it = segment.results().cbegin(); it != segment.results().cend(); ++it)
{
    if (it->is_blob())
    {
        process_blob(it->as_blob());
    }
    else
    {
        process_directory(it->as_directory());
    }
}

    token = segment.continuation_token();
}
while (!token.empty());
```

Należy pamiętać, że liczba wyników zwróconych na stronie może być sterowana przez parametr *max_results* w przeciążenia każdego interfejsu API, na przykład:

```cpp
list_blob_item_segment list_blobs_segmented(const utility::string_t& prefix, bool use_flat_blob_listing,
    blob_listing_details::values includes, int max_results, const continuation_token& token,
    const blob_request_options& options, operation_context context)
```

Jeśli nie określisz *max_results* parametr, domyślna wartość maksymalna maksymalnie 5000 wyników jest zwracany w jednej strony.

Należy również zauważyć, że zapytanie do usługi Azure Table storage mogą zwracać żadnych rekordów lub mniejszą liczbę rekordów niż wartość *max_results* parametr, który określiłeś, nawet jeśli token kontynuacji nie jest pusty. Musi być jednym z powodów zapytania nie można ukończyć w ciągu pięciu sekund. Tak długo, jak token kontynuacji nie jest pusta, zapytanie powinno być kontynuowane, a Twój kod nie należy zakładać, rozmiar segmentu wyników.

Zalecany wzorzec pisania kodu w przypadku większości scenariuszy składa się z listą, zapewniającą postępu jawną listę lub tworzenia zapytań i jak usługa będzie odpowiadać na każde żądanie. Szczególnie w przypadku aplikacji w języku C++ lub usług niższego poziomu kontrolki postępu listy może pomóc w kontroli pamięć i wydajność.

## <a name="greedy-listing"></a>Zachłanne listy
Wcześniejszych wersji biblioteki klienta usługi Storage dla języka C++ (wersje wersję 0.5.0 w wersji zapoznawczej i starszych) uwzględnione niepodzielony listę interfejsów API dla tabel i kolejek, jak w poniższym przykładzie:

```cpp
std::vector<cloud_table> list_tables(const utility::string_t& prefix) const;
std::vector<table_entity> execute_query(const table_query& query) const;
std::vector<cloud_queue> list_queues() const;
```

Te metody zostały zaimplementowane jako otoki segmentowanych interfejsów API. Dla każdej odpowiedzi podzielonej na segmenty listy kod dołączany wyniki do wektora i zwrócone wszystkie wyniki po pełnej kontenerów zostały zeskanowane.

Takie podejście może działać, gdy konto magazynu lub tabela zawiera niewielka liczba obiektów. Jednak wraz ze wzrostem liczby obiektów pamięci wymaganej można zwiększyć bez ograniczenia, ponieważ wszystkie wyniki pozostaje w pamięci. Jedną operację wyświetlania listy może potrwać bardzo długo, w którym obiekt wywołujący ma żadnych informacji o postępie.

Te listy zachłanne interfejsów API w zestawie SDK nie istnieją w C#, Java lub środowisko JavaScript Node.js. Aby uniknąć potencjalnych problemów z przy użyciu tych zachłanne interfejsów API, firma Microsoft usunęła je w wersji 0.6.0 (wersja zapoznawcza).

Jeśli Twój kod wywołuje te zachłanne interfejsów API:

```cpp
std::vector<azure::storage::table_entity> entities = table.execute_query(query);
for (auto it = entities.cbegin(); it != entities.cend(); ++it)
{
    process_entity(*it);
}
```

Następnie należy zmodyfikować kodu, aby użyć segmentowanych listę interfejsów API:

```cpp
azure::storage::continuation_token token;
do
{
    azure::storage::table_query_segment segment = table.execute_query_segmented(query, token);
    for (auto it = segment.results().cbegin(); it != segment.results().cend(); ++it)
    {
        process_entity(*it);
    }

    token = segment.continuation_token();
} while (!token.empty());
```

Określając *max_results* parametru segmentu, można równoważyć między liczby żądań i użycie pamięci w celu spełnienia zagadnienia związane z wydajnością aplikacji.

Ponadto, jeśli używasz segmentu, listy interfejsów API, ale przechowywanie danych w kolekcji lokalnej w stylu "zachłannego", również zdecydowanie zalecamy wykonać refaktoryzację kodu do obsługi, przechowywania danych w kolekcji lokalnej dokładnie na dużą skalę.

## <a name="lazy-listing"></a>Lista z opóźnieniem
Chociaż zachłanne listy wywoływane potencjalnych problemów, jest wygodne Jeśli zbyt wiele obiektów w kontenerze.

Jest również za pomocą języka C# lub zestawów SDK języka Java, Oracle, należy zapoznać się z Wyliczalny modelu programowania oferuje z opóźnieniem styl — Lista, gdzie dane na określone przesunięcie jest pobierana tylko w razie potrzeby. W języku C++ szablon na podstawie iteratora zapewnia podejście podobne.

Typowe interfejsu API, listę z opóźnieniem przy użyciu **list_blobs** przykład wygląda następująco:

```cpp
list_blob_item_iterator list_blobs() const;
```

Fragment kodu typowe używającego wzorca z opóźnieniem listy może wyglądać następująco:

```cpp
// List blobs in the blob container
azure::storage::list_blob_item_iterator end_of_results;
for (auto it = container.list_blobs(); it != end_of_results; ++it)
{
    if (it->is_blob())
    {
        process_blob(it->as_blob());
    }
    else
    {
        process_directory(it->as_directory());
    }
}
```

Należy zauważyć, że lista z opóźnieniem jest dostępna tylko w trybie synchronicznym.

Lista z opóźnieniem, w porównaniu z listą zachłannego, pobiera dane, tylko wtedy, gdy jest to konieczne. Dzieje się w tle narzędzie pobiera informacje o danych z usługi Azure Storage tylko wtedy, gdy następnego iteratora przechodzi do następnego segmentu. W związku z tym użycie pamięci jest kontrolowany przy użyciu ograniczony rozmiar i operacji jest szybkie.

Lista z opóźnieniem interfejsy API są objęte biblioteki klienta usługi Storage dla języka C++ w wersji 2.2.0.

## <a name="conclusion"></a>Podsumowanie
W tym artykule omówiono różne przeciążenia do wyświetlania listy interfejsów API dla różnych obiektów w bibliotece klienta usługi Storage dla języka C++. Podsumowując:

* Asynchroniczne interfejsy API zdecydowanie zaleca się w wielu scenariuszach wątków.
* Segmenty listy jest zalecane w przypadku większości scenariuszy.
* Lista z opóźnieniem znajduje się w bibliotece jako wygodny otoka w scenariusze synchroniczne z.
* Zachłanne lista nie jest zalecane i została usunięta z biblioteki.

## <a name="next-steps"></a>Kolejne kroki
Aby uzyskać więcej informacji dotyczących usługi Azure Storage i Biblioteka kliencka dla języka C++ zobacz następujące zasoby.

* [Jak używać magazynu obiektów Blob w języku C++](../blobs/storage-c-plus-plus-how-to-use-blobs.md)
* [Jak używać magazynu tabel w języku C++](../../cosmos-db/table-storage-how-to-use-c-plus.md)
* [Jak używać magazynu kolejek w języku C++](../storage-c-plus-plus-how-to-use-queues.md)
* [Biblioteki klienta usługi Azure Storage dokumentacji interfejsu API języka C++.](https://azure.github.io/azure-storage-cpp/)
* [Blog zespołu odpowiedzialnego za usługę Azure Storage](https://blogs.msdn.com/b/windowsazurestorage/)
* [Dokumentacja usługi Azure Storage](https://azure.microsoft.com/documentation/services/storage/)

