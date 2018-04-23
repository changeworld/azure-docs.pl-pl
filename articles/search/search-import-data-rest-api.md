---
title: Przekazywanie danych (interfejs API REST — usługa Azure Search) | Microsoft Docs
description: Dowiedz się, jak przekazywać dane do indeksu w usłudze Azure Search przy użyciu interfejsu API REST.
author: brjohnstmsft
manager: jlembicz
ms.author: brjohnst
ms.service: search
ms.devlang: rest-api
ms.topic: quickstart
ms.date: 12/08/2016
ms.openlocfilehash: 5322faf04d29643bba2d1371cef23ab224675adb
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/18/2018
---
# <a name="upload-data-to-azure-search-using-the-rest-api"></a>Przekazywanie danych do usługi Azure Search przy użyciu interfejsu API REST
> [!div class="op_single_selector"]
>
> * [Omówienie](search-what-is-data-import.md)
> * [.NET](search-import-data-dotnet.md)
> * [REST](search-import-data-rest-api.md)
>
>

W tym artykule opisano, jak używać [interfejsu API REST usługi Azure Search](https://docs.microsoft.com/rest/api/searchservice/) w celu importowania danych do indeksu usługi Azure Search.

Przed rozpoczęciem pracy z tym przewodnikiem powinien zostać [utworzony indeks usługi Azure Search](search-what-is-an-index.md).

Aby wypchnąć dokumenty do indeksu za pomocą interfejsu API REST, należy wysłać żądanie HTTP POST do końcowego adresu URL indeksu. Treść żądania HTTP jest obiektem JSON, który zawiera dokumenty do dodania, zmodyfikowania lub usunięcia.

## <a name="identify-your-azure-search-services-admin-api-key"></a>Identyfikowanie klucza api-key administratora usługi Azure Search
*Wszystkie* żądania HTTP wysyłane do usługi przy użyciu interfejsu API REST muszą zawierać klucz api-key wygenerowany dla aprowizowanej usługi wyszukiwania. Prawidłowy klucz ustanawia relację zaufania dla danego żądania między aplikacją wysyłającą żądanie i usługą, która je obsługuje.

1. Aby znaleźć klucze api-key dla usługi, możesz zalogować się w witrynie [Azure Portal](https://portal.azure.com/)
2. Przejdź do bloku usługi Azure Search
3. Kliknij ikonę „Klucze”

Usługa będzie dysponować *kluczami administratora* i *kluczami zapytań*.

* Za pomocą podstawowego i pomocniczego *klucza administratora* przyznawane są pełne prawa do wszystkich operacji, łącznie z możliwością zarządzania usługą oraz tworzenia i usuwania indeksów, indeksatorów i źródeł danych. Dostępne są dwa klucze, dzięki czemu możesz nadal używać klucza pomocniczego, jeśli zdecydujesz się na ponowne wygenerowanie klucza podstawowego, i na odwrót.
* *Klucze zapytań* umożliwiają dostęp tylko do odczytu do indeksów oraz dokumentów i są zazwyczaj dystrybuowane do aplikacji klienckich, które wysyłają żądania wyszukiwania.

W celu zaimportowania danych do indeksu można użyć zarówno podstawowego, jak i pomocniczego klucza administratora.

## <a name="decide-which-indexing-action-to-use"></a>Wybieranie akcji indeksowania do użycia
Korzystanie z interfejsu API REST wymaga wysyłania żądań HTTP POST zawierających treść żądań JSON do adresu URL punktu końcowego indeksu usługi Azure Search. Obiekt JSON w treści żądania HTTP będzie zawierał pojedynczą tablicę danych JSON o nazwie „wartość” z obiektami JSON reprezentującymi dokumenty, które mają zostać dodane do indeksu, zaktualizowane lub usunięte.

Poszczególne obiekty JSON w tablicy „wartość” reprezentują dokumenty, które mają zostać umieszczone w indeksie. Każdy z tych obiektów zawiera klucz dokumentu i określa wymaganą akcję indeksowania (przekazanie, scalenie, usunięcie itp.). W zależności od tego, którą z poniższych akcji wybierzesz, tylko określone pola muszą być uwzględnione w danym dokumencie:

| @search.action | Opis | Wymagane pola dla każdego dokumentu | Uwagi |
| --- | --- | --- | --- |
| `upload` |Akcja `upload` jest podobna do akcji „upsert”, co oznacza, że dokument zostanie wstawiony, jeśli jest nowy, albo zaktualizowany/zastąpiony, jeśli już istnieje. |pole klucza oraz inne pola, które chcesz zdefiniować |Podczas aktualizowania/zastępowania istniejącego dokumentu każde pole, które nie jest określone w żądaniu, zostanie ustawione na wartość `null`. Dzieje się tak nawet wtedy, gdy pole było wcześniej ustawione na wartość inną niż null. |
| `merge` |Aktualizuje istniejący dokument o określone pola. Jeśli dokument nie istnieje w indeksie, scalanie zakończy się niepowodzeniem. |pole klucza oraz inne pola, które chcesz zdefiniować |Wszystkie pola, które określisz w żądaniu scalania, zastąpią istniejące pola w dokumencie. Obejmuje to również pola typu `Collection(Edm.String)`. Jeśli na przykład dokument zawiera pole `tags` o wartości `["budget"]` i wykonywane jest scalanie z wartością `["economy", "pool"]` dla pola `tags`, końcowa wartość pola `tags` będzie równa `["economy", "pool"]`. Nie będzie to `["budget", "economy", "pool"]`. |
| `mergeOrUpload` |Ta akcja działa jak akcja `merge`, jeśli dokument o danym kluczu już istnieje w indeksie. Jeśli dokument nie istnieje, działa jak akcja `upload` dla nowego dokumentu. |pole klucza oraz inne pola, które chcesz zdefiniować |- |
| `delete` |Usuwa określony dokument z indeksu. |tylko pole klucza |Wszystkie pola, które określisz oprócz pola klucza, zostaną zignorowane. Jeśli chcesz usunąć pojedyncze pole z dokumentu, zamiast tej akcji użyj akcji `merge` i po prostu jawnie ustaw dla pola wartość null. |

## <a name="construct-your-http-request-and-request-body"></a>Konstruowania żądania HTTP i treści żądania
Po zebraniu wartości pól wymaganych dla akcji indeksu można przystąpić do konstruowania rzeczywistego żądania HTTP i treści żądania JSON w celu zaimportowania danych.

#### <a name="request-and-request-headers"></a>Żądanie i nagłówki żądania
Zawartość adresu URL musi obejmować nazwę usługi, nazwę indeksu (w tym przypadku „hotels”) oraz odpowiednią wersję interfejsu API (w momencie publikowania tego dokumentu aktualna wersja interfejsu API to `2016-09-01`). Musisz zdefiniować nagłówki żądań `Content-Type` i `api-key`. W przypadku drugiego nagłówka użyj jednego z kluczy administratora usługi.

    POST https://[search service].search.windows.net/indexes/hotels/docs/index?api-version=2016-09-01
    Content-Type: application/json
    api-key: [admin key]

#### <a name="request-body"></a>Treść żądania
```JSON
{
    "value": [
        {
            "@search.action": "upload",
            "hotelId": "1",
            "baseRate": 199.0,
            "description": "Best hotel in town",
            "description_fr": "Meilleur hôtel en ville",
            "hotelName": "Fancy Stay",
            "category": "Luxury",
            "tags": ["pool", "view", "wifi", "concierge"],
            "parkingIncluded": false,
            "smokingAllowed": false,
            "lastRenovationDate": "2010-06-27T00:00:00Z",
            "rating": 5,
            "location": { "type": "Point", "coordinates": [-122.131577, 47.678581] }
        },
        {
            "@search.action": "upload",
            "hotelId": "2",
            "baseRate": 79.99,
            "description": "Cheapest hotel in town",
            "description_fr": "Hôtel le moins cher en ville",
            "hotelName": "Roach Motel",
            "category": "Budget",
            "tags": ["motel", "budget"],
            "parkingIncluded": true,
            "smokingAllowed": true,
            "lastRenovationDate": "1982-04-28T00:00:00Z",
            "rating": 1,
            "location": { "type": "Point", "coordinates": [-122.131577, 49.678581] }
        },
        {
            "@search.action": "mergeOrUpload",
            "hotelId": "3",
            "baseRate": 129.99,
            "description": "Close to town hall and the river"
        },
        {
            "@search.action": "delete",
            "hotelId": "6"
        }
    ]
}
```

W tym przypadku jako akcje wyszukiwania są używane akcje `upload`, `mergeOrUpload` i `delete`.

Załóżmy, że przedstawiony w przykładzie indeks „hotels” jest już wypełniony różnymi dokumentami. Zwróć uwagę na to, że w przypadku akcji `mergeOrUpload` nie było konieczne określenie wszystkich możliwych pól dokumentu. Klucz dokumentu (`hotelId`) został określony tylko w przypadku akcji `delete`.

Zauważ również, że pojedyncze żądanie indeksowania może zawierać maksymalnie 1000 dokumentów (lub 16 MB danych).

## <a name="understand-your-http-response-code"></a>Opisy kodów odpowiedzi HTTP
#### <a name="200"></a>200
Po pomyślnym przesłaniu żądania indeksowania zostanie zwrócona odpowiedź HTTP z kodem stanu `200 OK`. Treść kodu JSON odpowiedzi HTTP będzie wyglądać następująco:

```JSON
{
    "value": [
        {
            "key": "unique_key_of_document",
            "status": true,
            "errorMessage": null
        },
        ...
    ]
}
```

#### <a name="207"></a>207
Kod stanu `207` jest zwracany, gdy co najmniej jeden element nie został pomyślnie umieszczony w indeksie. Treść kodu JSON odpowiedzi HTTP będzie zawierać informacje dotyczące dokumentów, których indeksowanie nie powiodło się.

```JSON
{
    "value": [
        {
            "key": "unique_key_of_document",
            "status": false,
            "errorMessage": "The search service is too busy to process this document. Please try again later."
        },
        ...
    ]
}
```

> [!NOTE]
> Często oznacza to, że obciążenie usługi wyszukiwania wkrótce osiągnie punkt, w którym żądania indeksowania zaczną zwracać odpowiedzi `503`. W takim przypadku zdecydowanie zaleca się wycofanie kodu klienta i odczekanie przed ponownym wysłaniem żądania. Zapewni to systemowi dodatkowy czas na przetworzenie danych, co zwiększy prawdopodobieństwo pomyślnego wykonania przyszłych żądań. Szybkie ponawianie żądań tylko wydłuży czas trwania problemu.
>
>

#### <a name="429"></a>429
Kod stanu `429` jest zwracany w przypadku przekroczenia limitu przydziału liczby dokumentów w indeksie.

#### <a name="503"></a>503
Kod stanu `503` jest zwracany, jeśli żaden z elementów w żądaniu nie został pomyślnie umieszczony w indeksie. Ten błąd oznacza, że system jest mocno obciążony i w tej chwili nie można przetworzyć żądania.

> [!NOTE]
> W takim przypadku zdecydowanie zaleca się wycofanie kodu klienta i odczekanie przed ponownym wysłaniem żądania. Zapewni to systemowi dodatkowy czas na przetworzenie danych, co zwiększy prawdopodobieństwo pomyślnego wykonania przyszłych żądań. Szybkie ponawianie żądań tylko wydłuży czas trwania problemu.
>
>

Aby uzyskać więcej informacji na temat akcji dla dokumentów oraz odpowiedzi oznaczających powodzenie lub błąd, zobacz [Add, Update, or Delete Documents](https://docs.microsoft.com/rest/api/searchservice/AddUpdate-or-Delete-Documents) (Dodawanie, aktualizowanie lub usuwanie dokumentów). Aby uzyskać więcej informacji o innych kodach stanów HTTP, które mogą być zwracane w przypadku niepowodzenia, zobacz [HTTP status codes (Azure Search)](https://docs.microsoft.com/rest/api/searchservice/HTTP-status-codes) (Usługa Azure Search — kody stanów HTTP).

## <a name="next-steps"></a>Następne kroki
Po wypełnieniu indeksu usługi Azure Search możesz rozpocząć wykonywanie zapytań w celu wyszukania dokumentów. Aby uzyskać szczegóły, zobacz [Query Your Azure Search Index](search-query-overview.md) (Tworzenie zapytań względem indeksu usługi Azure Search).
