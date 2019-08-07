---
title: Wskazówki dotyczące rozwiązywania problemów z wyszukiwaniem poznawcze — Azure Search
description: Porady i rozwiązywanie problemów związanych z konfigurowaniem potoków wyszukiwania poznawczego w Azure Search.
services: search
manager: pablocas
author: luiscabrer
ms.service: search
ms.subservice: cognitive-search
ms.devlang: NA
ms.workload: search
ms.topic: conceptual
ms.date: 02/02/2019
ms.author: luisca
ms.openlocfilehash: b85fb5417b014041024dd83ca62572282c6edf81
ms.sourcegitcommit: bc3a153d79b7e398581d3bcfadbb7403551aa536
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/06/2019
ms.locfileid: "68841275"
---
# <a name="troubleshooting-tips-for-cognitive-search"></a>Wskazówki dotyczące rozwiązywania problemów z usługą wyszukiwania poznawczego

Ten artykuł zawiera listę porad i wskazówek ułatwiających przechodzenie po rozpoczęciu pracy z możliwościami wyszukiwania poznawczego w Azure Search. 

Jeśli jeszcze tego nie zrobiono, przejdź do [samouczka: Dowiedz się, jak wywoływać interfejsy](cognitive-search-quickstart-blob.md) API wyszukiwania poznawczego w celu zastosowania wzbogacania wyszukiwania poznawczego do źródła danych obiektów BLOB.

## <a name="tip-1-start-with-a-small-dataset"></a>Porada 1: Zacznij od małego zestawu danych
Najlepszym sposobem, aby szybko znaleźć problemy, jest zwiększenie szybkości, w której można rozwiązać problemy. Najlepszym sposobem zredukowania czasu indeksowania jest zmniejszenie liczby dokumentów do indeksowania. 

Zacznij od utworzenia źródła danych kilku dokumenty/rekordy. Przykładowy dokument powinien być dobrym reprezentacją różnorodnych dokumentów, które będą indeksowane. 

Uruchom próbkę dokumentu za pomocą kompleksowego potoku i sprawdź, czy wyniki spełniają Twoje potrzeby. Po spełnieniu wyników możesz dodać więcej plików do źródła danych.

## <a name="tip-2-make-sure-your-data-source-credentials-are-correct"></a>Porada 2: Upewnij się, że poświadczenia źródła danych są poprawne
Połączenie ze źródłem danych nie jest sprawdzane do momentu zdefiniowania indeksatora, który go używa. Jeśli zobaczysz jakiekolwiek błędy z informacją, że indeksator nie może uzyskać do danych, upewnij się, że:
- Parametry połączenia są poprawne. Szczególnie podczas tworzenia tokenów SAS upewnij się, że używasz formatu oczekiwanego przez Azure Search. Zobacz [sekcję](
https://docs.microsoft.com/azure/search/search-howto-indexing-azure-blob-storage#how-to-specify-credentials) jak określić poświadczenia, aby dowiedzieć się więcej na temat obsługiwanych formatów.
- Nazwa kontenera w indeksatorze jest poprawna.

## <a name="tip-3-see-what-works-even-if-there-are-some-failures"></a>Porada 3: Zobacz, co działa nawet w przypadku wystąpienia niektórych błędów
Czasami niewielka awaria powoduje zatrzymanie indeksatora w jego ścieżkach. Jest to konieczne, jeśli planujesz rozwiązać problemy po jednym z nich. Jednak może być konieczne ignorowanie określonego typu błędu, dzięki czemu indeksator może kontynuować, aby zobaczyć, jakie przepływy faktycznie działają.

W takim przypadku możesz chcieć poinstruować indeksator, aby ignorował błędy. W tym celu należy ustawić *maxFailedItems* i *maxFailedItemsPerBatch* jako-1 jako część definicji indeksatora.

```
{
  "// rest of your indexer definition
   "parameters":
   {
      "maxFailedItems":-1,
      "maxFailedItemsPerBatch":-1
   }
}
```
## <a name="tip-4-looking-at-enriched-documents-under-the-hood"></a>Porada 4: Spojrzenie na wzbogacone dokumenty pod okapem 
Wzbogacone dokumenty są tymczasowymi strukturami utworzonymi podczas wzbogacania, a następnie usuwane po zakończeniu przetwarzania.

Aby przechwycić migawkę wzbogaconego dokumentu utworzoną podczas indeksowania, dodaj pole o nazwie ```enriched``` do indeksu. Indeksator automatycznie zrzuca do tego pola ciąg będący reprezentacją wszystkich wzbogaceń dokumentu.

Pole ```enriched``` będzie zawierać ciąg, który jest logiczną reprezentacją wzbogaconego dokumentu w pamięci w formacie JSON.  Wartość pola jest jednak prawidłowym dokumentem w formacie JSON. Cudzysłowy są umieszczane w sekwencji ucieczki, więc musisz zastąpić ciąg `\"` znakiem `"`, aby wyświetlić dokument w postaci sformatowanego kodu JSON. 

Wzbogacone pole jest przeznaczone tylko do celów debugowania, aby ułatwić zrozumienie kształtu logicznego zawartości, względem której są oceniane wyrażenia. Nie należy zależeć od tego pola do indeksowania.

```enriched``` Dodaj pole jako część definicji indeksu na potrzeby debugowania:

#### <a name="request-body-syntax"></a>Składnia treści żądania
```json
{
  "fields": [
    // other fields go here.
    {
      "name": "enriched",
      "type": "Edm.String",
      "searchable": false,
      "sortable": false,
      "filterable": false,
      "facetable": false
    }
  ]
}
```

## <a name="tip-5-expected-content-fails-to-appear"></a>Porada 5: Nie można wyświetlić oczekiwanej zawartości

Brakująca zawartość może być wynikiem porzucenia dokumentów podczas indeksowania. Warstwy Bezpłatna i podstawowa mają niskie limity rozmiaru dokumentu. Każdy plik przekraczający limit jest usuwany podczas indeksowania. Możesz sprawdzić porzucone dokumenty w Azure Portal. Na pulpicie nawigacyjnym usługi wyszukiwania kliknij dwukrotnie kafelek indeksatory. Sprawdź stosunek pomyślnych dokumentów indeksowanych. Jeśli wartość nie jest równa 100%, możesz kliknąć współczynnik, aby uzyskać więcej szczegółów. 

Jeśli problem dotyczy rozmiaru pliku, może zostać wyświetlony następujący błąd: "Rozmiar \<pliku \<obiektu BLOB >" ma rozmiar > bajtów, który przekracza maksymalny rozmiar wyodrębniania dokumentu dla bieżącej warstwy usług ". Aby uzyskać więcej informacji na temat limitów indeksatora, zobacz [limity usługi](search-limits-quotas-capacity.md).

Druga przyczyna niepowodzenia wyświetlania zawartości może być związana z błędami mapowania operacji wejścia/wyjścia. Na przykład nazwa elementu docelowego danych wyjściowych to "ludzie", ale nazwa pola indeksu jest małymi literami "ludzie". System może zwrócić komunikaty o powodzeniu 201 dla całego potoku, aby podejrzewać, że indeksowanie powiedzie się, gdy w rzeczywistości pole jest puste. 

## <a name="tip-6-extend-processing-beyond-maximum-run-time-24-hour-window"></a>Porada 6: Rozszerzenie przetwarzania wykraczające poza maksymalny czas działania (okno 24-godzinne)

Analiza obrazów jest intensywnie czasochłonna dla nawet prostych przypadków, dlatego w przypadku, gdy obrazy są szczególnie duże lub złożone, czasy przetwarzania mogą przekroczyć maksymalny dozwolony czas. 

Maksymalny czas działania zależy od warstwy: kilka minut w warstwie Bezpłatna, indeksowanie 24-godzinne w warstwach płatnych. Jeśli przetwarzanie zakończy się niepowodzeniem w ciągu 24 godzin dla przetwarzania na żądanie, przejdź do harmonogramu, aby indeksator mógł pobrać, gdzie został pozostawiony. 

W przypadku zaplanowanych indeksatorów indeksowanie zostaje wznowione zgodnie z harmonogramem w ostatnim znanym dobrym dokumencie. Korzystając z harmonogramu cyklicznego, indeksator może wykonać swój sposób przez zaległości obrazu w ciągu kilku godzin lub dni, do momentu przetworzenia wszystkich nieprzetwarzanych obrazów. Aby uzyskać więcej informacji na temat składni harmonogramu [, zobacz krok 3. Utwórz indeksator](search-howto-indexing-azure-blob-storage.md#step-3-create-an-indexer) lub Zobacz, [jak zaplanować indeksatory dla Azure Search](search-howto-schedule-indexers.md).

> [!NOTE]
> Jeśli indeksator jest ustawiony na określony harmonogram, ale wielokrotnie powtarza się w tym samym dokumencie za każdym razem, gdy zostanie on uruchomiony, indeksator zacznie działać w krótszym interwale (maksymalnie co 24 godziny), aż do pomyślnego przekroczenia postępu Aga podczas.  Jeśli uważasz, że Rozwiązano problem, który spowodował zablokowanie indeksatora w określonym punkcie, można wykonać uruchomienie na żądanie indeksatora, a jeśli ten proces pomyślnie przejdzie, indeks ponownie powróci do jego ustawionego interwału harmonogramu.

W przypadku indeksowania opartego na portalu (zgodnie z opisem w przewodniku Szybki Start) wybranie opcji indeksatora "Uruchom raz" ogranicza przetwarzanie do`"maxRunTime": "PT1H"`1 godziny (). Możesz chcieć wydłużyć przedział czasu przetwarzania.

## <a name="tip-7-increase-indexing-throughput"></a>Porada 7: Zwiększ przepływność indeksowania

W przypadku [indeksowania równoległego](search-howto-large-index.md)należy umieścić dane w wielu kontenerach lub w wielu folderach wirtualnych w tym samym kontenerze. Następnie Utwórz wiele par DataSource i indeksatora. Wszystkie indeksatory mogą używać tego samego zestawu umiejętności i zapisywać w tym samym docelowym indeksie wyszukiwania, dzięki czemu aplikacja wyszukiwania nie musi znać tego partycjonowania.
Aby uzyskać więcej informacji, zobacz [indeksowanie dużych zestawów danych](search-howto-indexing-azure-blob-storage.md#indexing-large-datasets).

## <a name="see-also"></a>Zobacz także
+ [Szybki start: Tworzenie potoku wyszukiwania poznawczego w portalu](cognitive-search-quickstart-blob.md)
+ [Samouczek: Poznaj interfejsy API REST wyszukiwania poznawczego](cognitive-search-tutorial-blob.md)
+ [Określanie poświadczeń źródła danych](search-howto-indexing-azure-blob-storage.md#how-to-specify-credentials)
+ [Indeksowanie dużych zestawów danych](search-howto-indexing-azure-blob-storage.md#indexing-large-datasets)
+ [Jak zdefiniować zestawu umiejętności](cognitive-search-defining-skillset.md)
+ [Jak zmapować wzbogacone pola na indeks](cognitive-search-output-field-mapping.md)
