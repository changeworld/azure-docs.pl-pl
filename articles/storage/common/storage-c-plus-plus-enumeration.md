---
title: Lista zasobami magazynu platformy Azure za pomocą biblioteki klienta usługi Storage dla języka C++ | Dokumentacja firmy Microsoft
description: Dowiedz się, jak wyliczyć kontenerów, obiektów blob, kolejek, tabel i jednostek za pomocą listy interfejsów API w Biblioteka klienta usługi Microsoft Azure Storage dla języka C++.
documentationcenter: .net
services: storage
author: dineshmurthy
manager: jahogg
editor: tysonn
ms.assetid: 33563639-2945-4567-9254-bc4a7e80698f
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: dineshm
ms.openlocfilehash: 9844412739f4f6f95416f81347f0f2eeeca62bea
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/11/2017
ms.locfileid: "23873870"
---
# <a name="list-azure-storage-resources-in-c"></a>Lista zasobów magazynu Azure w języku C++
Lista operacji są kluczem do wielu scenariuszy programowania z usługą Azure Storage. W tym artykule opisano, jak najbardziej efektywne wyliczyć obiektów w usłudze Azure Storage za pomocą listy interfejsów API dostarczonych w biblioteki klienta usługi Microsoft Azure Storage dla języka C++.

> [!NOTE]
> Ten przewodnik jest przeznaczony dla biblioteki klienta usługi Azure Storage dla języka C++ w wersji 2.x, który jest dostępny za pośrednictwem [NuGet](http://www.nuget.org/packages/wastorage) lub [GitHub](https://github.com/Azure/azure-storage-cpp).
> 
> 

Biblioteka klienta magazynu zapewnia różne metody do listy lub kwerendy obiektów w usłudze Azure Storage. W tym artykule opisano następujące scenariusze:

* Kontenery listy konta
* Listy BLOB w kontenerze lub katalogu wirtualnego obiektów blob
* Lista kolejek w ramach konta
* Listy tabel na koncie
* Jednostki zapytania w tabeli

Każda z tych metod jest wyświetlany za pomocą innego przeciążenia dla różnych scenariuszy.

## <a name="asynchronous-versus-synchronous"></a>Asynchroniczne i synchroniczne
Ponieważ biblioteki klienta usługi Storage dla języka C++ jest oparty na [biblioteki C++ REST](https://github.com/Microsoft/cpprestsdk), obsługujemy z założenia operacji asynchronicznych za pomocą [pplx::task](http://microsoft.github.io/cpprestsdk/classpplx_1_1task.html). Na przykład:

```cpp
pplx::task<list_blob_item_segment> list_blobs_segmented_async(continuation_token& token) const;
```

Operacje synchroniczne zawijać odpowiednich operacji asynchronicznych:

```cpp
list_blob_item_segment list_blobs_segmented(const continuation_token& token) const
{
    return list_blobs_segmented_async(token).get();
}
```

Jeśli pracujesz z wielu wątków aplikacji lub usług, zalecane jest użycie async interfejsów API bezpośrednio, zamiast tworzyć wątku do wywołania synchronizacji interfejsów API, które znacząco wpływa na wydajność.

## <a name="segmented-listing"></a>Lista wybranych
Skala magazynu w chmurze wymaga listy segmentu. Na przykład można mieć w milion BLOB w kontenerze obiektów blob platformy Azure lub za pośrednictwem miliarda podmiotów w tabeli platformy Azure. Nie są teoretycznego cyfr, ale odbiorcy rzeczywistych przypadków użycia.

W związku z tym jest niemożliwe do wyświetlenia wszystkich obiektów w jednej odpowiedzi. Zamiast tego można wyświetlić listę obiektów za pomocą stronicowania. Z listy interfejsów API *segmentowanych* przeciążenia.

Odpowiedź dla operacji segmentowanych lista zawiera:

* <i>_segment</i>, który zawiera zbiór wyników zwróconych jednego wywołania interfejsu API listy.
* *continuation_token*, który jest przekazywany do następnego wywołania w celu pobrania następnej strony wyników. Jeśli nie ma żadnych innych wyników do zwrócenia, token kontynuacji jest pusty.

Na przykład typowe wywołania, aby wyświetlić listę wszystkich obiektów blob w kontenerze może wyglądać jak poniższy fragment kodu. Kod jest dostępny w naszym [przykłady](https://github.com/Azure/azure-storage-cpp/blob/master/Microsoft.WindowsAzure.Storage/samples/BlobsGettingStarted/Application.cpp):

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
        process_diretory(it->as_directory());
    }
}

    token = segment.continuation_token();
}
while (!token.empty());
```

Należy pamiętać, że liczba wyników zwróconych w stronę można kontrolować przez parametr *max_results* w przeciążenia każdego interfejsu API, na przykład:

```cpp
list_blob_item_segment list_blobs_segmented(const utility::string_t& prefix, bool use_flat_blob_listing,
    blob_listing_details::values includes, int max_results, const continuation_token& token,
    const blob_request_options& options, operation_context context)
```

Jeśli nie określisz *max_results* parametr, domyślna wartość maksymalna maksymalnie 5000 wyników jest zwracany w jednej strony.

Należy również zauważyć, że zapytanie magazynu tabel Azure może zwracać żadnych rekordów i rekordów mniej niż wartość *max_results* parametr, który jest określony, nawet jeśli nie jest pusty token kontynuacji. Powodem może być zapytania nie można ukończyć w ciągu pięciu sekund. Tak długo, jak token kontynuacji nie jest pusta, zapytanie powinno być kontynuowane i kodu nie należy zakładać, rozmiar segmentu wyników.

Wzorcu kodowania zalecane dla większości scenariuszy składa się z wyświetlania, co umożliwia jawne postępu listy lub wyszukiwanie i jak usługa odpowiada na każde żądanie. Szczególnie w przypadku aplikacji C++ lub usług niższego poziomu formantu postępu listy może pomóc sterowania pamięcią i wydajnością.

## <a name="greedy-listing"></a>Zachłanne listy
Wcześniejszych wersji biblioteki klienta usługi Storage dla języka C++ (Preview wersji 0.5.0 i starszych) uwzględnione niepodzielony listę interfejsów API dla tabel i kolejek, jak w poniższym przykładzie:

```cpp
std::vector<cloud_table> list_tables(const utility::string_t& prefix) const;
std::vector<table_entity> execute_query(const table_query& query) const;
std::vector<cloud_queue> list_queues() const;
```

Te metody zostały zaimplementowane jako otoki segmentowanych interfejsów API. Dla każdej odpowiedzi listę segmentu kod dołączany wyniki do wektora i zwrócone wszystkie wyniki Po przeskanowaniu były pełne kontenerów.

Takie podejście może działać konta magazynu lub tabela zawiera niewielką liczbę obiektów. Jednak wraz ze wzrostem liczby obiektów pamięci wymaganej można zwiększyć bez ograniczeń, ponieważ wszystkie wyniki pozostaje w pamięci. Jedną operację wyświetlania listy może zająć bardzo dużo czasu, w którym obiekt wywołujący nie zawierała informacji dotyczących o postępie.

Te listy intensywnie interfejsów API zestawu SDK nie istnieją w C#, Java lub środowiska JavaScript Node.js. Aby uniknąć potencjalnych problemów z przy użyciu tych intensywnie interfejsów API, firma Microsoft usunęła je w wersji 0.6.0 podglądu.

Jeśli kod jest wywołanie te intensywnie interfejsów API:

```cpp
std::vector<azure::storage::table_entity> entities = table.execute_query(query);
for (auto it = entities.cbegin(); it != entities.cend(); ++it)
{
    process_entity(*it);
}
```

Następnie należy zmodyfikować kod, aby używał segmentowanych listę interfejsów API:

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

Określając *max_results* parametru segmentu, można równoważyć między liczby żądań i użycie pamięci, aby spełnić zagadnienia dotyczące wydajności aplikacji.

Ponadto, jeśli używasz segmentowanych listy interfejsów API, ale przechowywania danych w kolekcji lokalnej w stylu "zachłannego", również stanowczo zaleca się czy Refaktoryzuj swój kod obsługi, przechowywania danych w kolekcji lokalnej dokładnie na dużą skalę.

## <a name="lazy-listing"></a>Lista opóźnieniem
Chociaż intensywnie listę wywoływane potencjalne problemy, jest wygodne Jeśli nie ma zbyt wiele obiektów w kontenerze.

Jeśli używasz również języka C# lub Oracle Java SDK, należy zapoznać się z wyliczenia modelu programowania oferuje opóźnieniem styl — wyświetlanie, gdzie dane z przesunięciem niektórych jest pobierana tylko jeśli jest to wymagane. W języku C++ szablonu na podstawie iteratora także podejście podobne.

Typowy interfejsu API, listę opóźnieniem przy użyciu **list_blobs** na przykład wygląda podobnie do następującej:

```cpp
list_blob_item_iterator list_blobs() const;
```

Fragment kodu typowe, który korzysta ze wzorca opóźnieniem listy może wyglądać następująco:

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

Należy pamiętać, że lista opóźnieniem jest dostępna tylko w trybie synchronicznym.

W porównaniu z listą intensywnie opóźnieniem listę pobiera dane tylko wtedy, gdy jest to konieczne. W obszarze obejmuje on pobiera dane z usługi Azure Storage tylko wtedy, gdy następnym iteratora przenosi do następnego segmentu. W związku z tym pamięć jest określana za pomocą ograniczonego rozmiar, a operacja jest szybkie.

Lista opóźnieniem interfejsy API są objęte biblioteki klienta usługi Storage dla języka C++ w wersji 2.2.0.

## <a name="conclusion"></a>Podsumowanie
W tym artykule omówiono różne przeciążenia do wyświetlania listy interfejsów API dla różnych obiektów w bibliotece klienta usługi Storage dla języka C++. Podsumowując:

* Interfejsy API Async zdecydowanie zalecane jest używanie w wielu scenariuszach wątków.
* Lista segmentu jest zalecane dla większości scenariuszy.
* Listę opóźnieniem znajduje się w bibliotece jako otoka wygodny w scenariuszach synchroniczne.
* Zachłanne lista nie jest zalecane i została usunięta z biblioteki.

## <a name="next-steps"></a>Następne kroki
Aby uzyskać więcej informacji o bibliotece klienta i usługi Azure Storage dla języka C++ zobacz następujące zasoby.

* [Jak używać magazynu obiektów Blob w języku C++](../blobs/storage-c-plus-plus-how-to-use-blobs.md)
* [Jak używać magazynu tabel w języku C++](../../cosmos-db/table-storage-how-to-use-c-plus.md)
* [Jak używać magazynu kolejek w języku C++](../storage-c-plus-plus-how-to-use-queues.md)
* [Biblioteki klienta usługi Azure Storage dokumentacji interfejsu API języka C++.](http://azure.github.io/azure-storage-cpp/)
* [Blog zespołu odpowiedzialnego za usługę Azure Storage](http://blogs.msdn.com/b/windowsazurestorage/)
* [Dokumentacja usługi Azure Storage](https://azure.microsoft.com/documentation/services/storage/)

