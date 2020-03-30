---
title: Wyświetlanie listy zasobów usługi Azure Storage za pomocą biblioteki klienta języka C++
description: Dowiedz się, jak używać wyświetlania wyświetlania interfejsów API listy w bibliotece klienta usługi Microsoft Azure Storage dla języka C++ do wyliczanie kontenerów, obiektów blob, kolejek, tabel i encji.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 01/23/2017
ms.service: storage
ms.subservice: common
ms.topic: conceptual
ms.reviewer: dineshm
ms.openlocfilehash: 0f9e80aff20c1b2663491f6d6ceb99aaec58230f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74269447"
---
# <a name="list-azure-storage-resources-in-c"></a>Generowanie listy zasobów usługi Azure Storage w języku C++

Operacje wyświetlania są kluczem do wielu scenariuszy rozwoju za pomocą usługi Azure Storage. W tym artykule opisano, jak najbardziej efektywnie wyliczyć obiekty w usłudze Azure Storage przy użyciu listy interfejsów API dostarczonych w bibliotece klienta usługi Microsoft Azure Storage dla języka C++.

> [!NOTE]
> Ten przewodnik dotyczy biblioteki klienta usługi Azure Storage dla języka C++ w wersji 2.x, która jest dostępna za pośrednictwem [NuGet](https://www.nuget.org/packages/wastorage) lub [GitHub.](https://github.com/Azure/azure-storage-cpp)

Biblioteka klienta magazynu zawiera wiele metod do listy lub zapytania obiektów w usłudze Azure Storage. W tym artykule omiń następujące scenariusze:

* Wyświetlanie kontenerów na koncie
* Lista obiektów blob w katalogu kontenerów lub wirtualnych obiektów blob
* Lista kolejek na koncie
* Wyświetlanie tabel na koncie
* Encje kwerend w tabeli

Każda z tych metod jest pokazana przy użyciu różnych przeciążeń dla różnych scenariuszy.

## <a name="asynchronous-versus-synchronous"></a>Asynchroniczne a synchroniczne

Ponieważ biblioteka klienta magazynu dla języka C++ jest zbudowana na [podstawie biblioteki REST języka C++,](https://github.com/Microsoft/cpprestsdk)z natury obsługujemy operacje asynchroniczne przy użyciu [pplx::task](https://microsoft.github.io/cpprestsdk/classpplx_1_1task.html). Przykład:

```cpp
pplx::task<list_blob_item_segment> list_blobs_segmented_async(continuation_token& token) const;
```

Operacje synchroniczne zawijają odpowiednie operacje asynchroniczne:

```cpp
list_blob_item_segment list_blobs_segmented(const continuation_token& token) const
{
    return list_blobs_segmented_async(token).get();
}
```

Jeśli pracujesz z wieloma aplikacjami lub usługami wątkowymi, zaleca się używanie asynchronizowanych interfejsów API bezpośrednio zamiast tworzenia wątku do wywoływania interfejsów API synchronizacji, co znacząco wpływa na wydajność.

## <a name="segmented-listing"></a>Aukcja segmentowa

Skala magazynu w chmurze wymaga listy podzielonej na segmenty. Na przykład można mieć ponad milion obiektów blob w kontenerze obiektów blob platformy Azure lub ponad miliard jednostek w tabeli platformy Azure. Nie są to liczby teoretyczne, ale rzeczywiste przypadki użycia klienta.

Dlatego niepraktyczne jest wyświetlanie listy wszystkich obiektów w jednej odpowiedzi. Zamiast tego można wyświetlić listę obiektów przy użyciu stronicowania. Każdy z interfejsów API aukcji ma *przeciążenie segmentowe.*

Odpowiedź dla operacji aukcji segmentacji obejmuje:

* *_segment*, który zawiera zestaw wyników zwróconych dla pojedynczego wywołania do zestawu API listy.
* *continuation_token*, który jest przekazywany do następnego połączenia w celu uzyskania następnej strony wyników. Gdy nie ma więcej wyników do zwrócenia, token kontynuacji jest null.

Na przykład typowe wywołanie listy wszystkich obiektów blob w kontenerze może wyglądać jak poniższy fragment kodu. Kod jest dostępny w naszych [przykładach:](https://github.com/Azure/azure-storage-cpp/blob/master/Microsoft.WindowsAzure.Storage/samples/BlobsGettingStarted/Application.cpp)

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

Należy zauważyć, że liczba wyników zwracanych na stronie może być kontrolowana przez parametr *max_results* w przeciążeniu każdego interfejsu API, na przykład:

```cpp
list_blob_item_segment list_blobs_segmented(const utility::string_t& prefix, bool use_flat_blob_listing,
    blob_listing_details::values includes, int max_results, const continuation_token& token,
    const blob_request_options& options, operation_context context)
```

Jeśli parametr *max_results* nie zostanie określony, domyślna maksymalna wartość maksymalnie 5000 wyników zostanie zwrócona na jednej stronie.

Należy również zauważyć, że kwerenda względem usługi Azure Table Storage może zwracać żadnych rekordów lub mniej rekordów niż wartość *parametru max_results,* który został określony, nawet jeśli token kontynuacji nie jest pusty. Jednym z powodów może być, że kwerenda nie może zakończyć się w ciągu pięciu sekund. Tak długo, jak token kontynuacji nie jest pusty, kwerenda powinna być kontynuowana, a kod nie należy zakładać rozmiar wyników segmentu.

Zalecany wzorzec kodowania dla większości scenariuszy jest podzielony na segmenty, który zapewnia wyraźny postęp aukcji lub kwerendy i jak usługa reaguje na każde żądanie. Szczególnie w przypadku aplikacji lub usług języka C++ kontrola niższego poziomu postępu listy może pomóc kontrolować pamięć i wydajność.

## <a name="greedy-listing"></a>Chciwy aukcji

Starsze wersje biblioteki klienta magazynu dla języka C++ (wersje 0.5.0 Wersja zapoznawcza i starsze) zawierały niesegmentowane interfejsy API aukcji dla tabel i kolejek, jak w poniższym przykładzie:

```cpp
std::vector<cloud_table> list_tables(const utility::string_t& prefix) const;
std::vector<table_entity> execute_query(const table_query& query) const;
std::vector<cloud_queue> list_queues() const;
```

Metody te zostały zaimplementowane jako otoki segmentowanych interfejsów API. Dla każdej odpowiedzi na listę segmentów kod dołączał wyniki do wektora i zwracał wszystkie wyniki po zeskanowaniu pełnych kontenerów.

Takie podejście może działać, gdy konto magazynu lub tabela zawiera niewielką liczbę obiektów. Jednak wraz ze wzrostem liczby obiektów wymagana pamięć może wzrosnąć bez ograniczeń, ponieważ wszystkie wyniki pozostały w pamięci. Jedna operacja aukcji może zająć bardzo dużo czasu, podczas którego rozmówca nie miał informacji o jego postępach.

Te chciwi wyświetlania interfejsów API listy w zestawie SDK nie istnieją w środowisku C#, Java lub JavaScript Node.js. Aby uniknąć potencjalnych problemów z używaniem tych chciwych interfejsów API, usunęliśmy je w wersji 0.6.0 Preview.

Jeśli kod wywołuje te chciwi interfejsów API:

```cpp
std::vector<azure::storage::table_entity> entities = table.execute_query(query);
for (auto it = entities.cbegin(); it != entities.cend(); ++it)
{
    process_entity(*it);
}
```

Następnie należy zmodyfikować kod, aby użyć interfejsów API aukcji segmentowej:

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

Określając parametr *max_results* segmentu, można zrównoważyć liczbę żądań i użycie pamięci, aby spełnić wymagania dotyczące wydajności aplikacji.

Ponadto jeśli używasz segmentowych wyświetlania wyświetlania interfejsów API, ale przechowujesz dane w kolekcji lokalnej w stylu "chciwy", zdecydowanie zalecamy również refaktoryzowanie kodu do obsługi przechowywania danych w lokalnej kolekcji ostrożnie na dużą skalę.

## <a name="lazy-listing"></a>Aukcja leniwa

Chociaż chciwy aukcji podniesione potencjalne problemy, jest to wygodne, jeśli nie ma zbyt wiele obiektów w kontenerze.

Jeśli używasz również C# lub Oracle Java SDK, należy zapoznać się z modelu programowania wyliczanego, który oferuje leniwy styl aukcji, gdzie dane w określonym przesunięcie jest pobierana tylko wtedy, gdy jest to wymagane. W języku C++ szablon oparty na iteratorze również zapewnia podobne podejście.

Typowy interfejs API z leniwą listą, **używający list_blobs** jako przykładu, wygląda następująco:

```cpp
list_blob_item_iterator list_blobs() const;
```

Typowy fragment kodu, który używa wzorca aukcji z opóźnieniem może wyglądać następująco:

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

Należy pamiętać, że z opóźnieniem aukcji jest dostępna tylko w trybie synchroniczowym.

W porównaniu z chciwą aukcją, leniwa aukcja pobiera dane tylko wtedy, gdy jest to konieczne. W ramach okładek pobiera dane z usługi Azure Storage tylko wtedy, gdy następny iterator przenosi się do następnego segmentu. W związku z tym użycie pamięci jest kontrolowane z ograniczonym rozmiarem, a operacja jest szybka.

Interfejsy API z listą z opóźnieniem są zawarte w bibliotece klienta magazynu dla języka C++ w wersji 2.2.0.

## <a name="conclusion"></a>Podsumowanie

W tym artykule omówiliśmy różne przeciążenia do wyświetlania wyświetlania interfejsów API dla różnych obiektów w bibliotece klienta magazynu dla języka C++ . Podsumowując:

* Interfejsy API asynchronii są zdecydowanie zalecane w wielu scenariuszach wątków.
* Lista segmentowa jest zalecana w większości scenariuszy.
* Z opóźnieniem aukcji znajduje się w bibliotece jako wygodne otoki w scenariuszach synchronicznych.
* Chciwy aukcji nie jest zalecane i został usunięty z biblioteki.

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat usługi Azure Storage i biblioteki klienta dla języka C++, zobacz następujące zasoby.

* [Jak korzystać z magazynu obiektów Blob z języka C++](../blobs/storage-c-plus-plus-how-to-use-blobs.md)
* [Jak korzystać z magazynu tabel z języka C++](../../cosmos-db/table-storage-how-to-use-c-plus.md)
* [Jak używać usługi Queue Storage z poziomu języka C++](../storage-c-plus-plus-how-to-use-queues.md)
* [Biblioteka klienta usługi Azure Storage dla dokumentacji interfejsu API języka C++.](https://azure.github.io/azure-storage-cpp/)
* [Blog zespołu usługi Azure Storage](https://blogs.msdn.com/b/windowsazurestorage/)
* [Dokumentacja usługi Azure Storage](https://azure.microsoft.com/documentation/services/storage/)
