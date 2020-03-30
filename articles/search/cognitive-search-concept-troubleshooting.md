---
title: Wskazówki dotyczące projektowania wzbogacania SI
titleSuffix: Azure Cognitive Search
description: Porady i rozwiązywanie problemów z konfigurowaniem potoków wzbogacania sztucznej inteligencji w usłudze Azure Cognitive Search.
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 3fef5db90c3ae63a8fa48835646e09f9dfe6f023
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79245488"
---
# <a name="tips-for-ai-enrichment-in-azure-cognitive-search"></a>Wskazówki dotyczące wzbogacania sztucznej inteligencji w usłudze Azure Cognitive Search

Ten artykuł zawiera listę wskazówek i wskazówek, które pomogą Ci w ruchu podczas rozpoczynania pracy z możliwościami wzbogacania sztucznej inteligencji w usłudze Azure Cognitive Search. 

Jeśli jeszcze tego nie zrobiono, po kroku [w samouczku: Dowiedz się, jak wywołać interfejsy API wzbogacania sieci SI](cognitive-search-quickstart-blob.md) w celu praktycznego stosowania wzbogacenia ai do źródła danych obiektu blob.

## <a name="tip-1-start-with-a-small-dataset"></a>Wskazówka 1: Zacznij od małego zestawu danych
Najlepszym sposobem szybkiego znajdowania problemów jest zwiększenie szybkości, z jaką można rozwiązywać problemy. Najlepszym sposobem skrócenia czasu indeksowania jest zmniejszenie liczby dokumentów, które mają być indeksowane. 

Zacznij od utworzenia źródła danych z zaledwie kilkoma dokumentami/rekordami. Przykład dokumentu powinien być dobrą reprezentacją różnych dokumentów, które będą indeksowane. 

Uruchom próbkę dokumentu za pośrednictwem potoku end-to-end i sprawdź, czy wyniki spełniają Twoje potrzeby. Po uzyskaniu zadowalających wyników można dodać więcej plików do źródła danych.

## <a name="tip-2-make-sure-your-data-source-credentials-are-correct"></a>Wskazówka 2: Upewnij się, że poświadczenia źródła danych są poprawne
Połączenie ze źródłem danych nie jest sprawdzane, dopóki nie zdefiniujesz indeksatora, który go używa. Jeśli widzisz błędy informujące, że indeksator nie może uzyskać dostępu do danych, upewnij się, że:
- Twój ciąg połączenia jest poprawny. Szczególnie podczas tworzenia tokenów sygnatury dostępu Współdzielonego upewnij się, że używasz formatu oczekiwanego przez usługę Azure Cognitive Search. Zobacz [Jak określić sekcję poświadczeń,](
https://docs.microsoft.com/azure/search/search-howto-indexing-azure-blob-storage#how-to-specify-credentials) aby dowiedzieć się więcej o różnych obsługiwanych formatach.
- Nazwa kontenera w indeksatorze jest poprawna.

## <a name="tip-3-see-what-works-even-if-there-are-some-failures"></a>Wskazówka 3: Zobacz, co działa, nawet jeśli występują pewne błędy
Czasami mała awaria zatrzymuje indeksatora w jego utworów. To jest w porządku, jeśli planujesz rozwiązać problemy jeden po drugim. Jednak można zignorować określonego typu błędu, dzięki czemu indeksator, aby kontynuować, dzięki czemu można zobaczyć, jakie przepływy faktycznie działają.

W takim przypadku można powiedzieć indeksatorowi, aby zignorował błędy. Zrób to, ustawiając *maxFailedItems* i *maxFailedItemsPerBatch* jako -1 jako część definicji indeksatora.

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
## <a name="tip-4-looking-at-enriched-documents-under-the-hood"></a>Wskazówka 4: Patrzenie na wzbogacone dokumenty pod maską 
Wzbogacone dokumenty są strukturami tymczasowymi utworzonymi podczas wzbogacania, a następnie usuniętymi po zakończeniu przetwarzania.

Aby przechwycić migawkę wzbogaconego dokumentu utworzoną podczas indeksowania, dodaj pole o nazwie ```enriched``` do indeksu. Indeksator automatycznie zrzuca do tego pola ciąg będący reprezentacją wszystkich wzbogaceń dokumentu.

Pole ```enriched``` będzie zawierać ciąg, który jest logiczną reprezentacją wzbogaconego dokumentu w pamięci w formacie JSON.  Wartość pola jest jednak prawidłowym dokumentem w formacie JSON. Cudzysłowy są umieszczane w sekwencji ucieczki, więc musisz zastąpić ciąg `\"` znakiem `"`, aby wyświetlić dokument w postaci sformatowanego kodu JSON. 

Wzbogacone pole jest przeznaczone tylko do celów debugowania, aby ułatwić zrozumienie logicznego kształtu zawartości, dla której są oceniane wyrażenia. Nie należy polegać na tym polu do celów indeksowania.

Dodaj ```enriched``` pole jako część definicji indeksu do celów debugowania:

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

## <a name="tip-5-expected-content-fails-to-appear"></a>Wskazówka 5: Oczekiwana zawartość nie pojawia się

Brak zawartości może być wynikiem dokumentów coraz porzucone podczas indeksowania. Warstwy bezpłatne i podstawowe mają niskie limity rozmiaru dokumentu. Każdy plik przekraczający limit jest odrzucany podczas indeksowania. Możesz sprawdzić, czy porzucone dokumenty są usuwane w witrynie Azure portal. Na pulpicie nawigacyjnym usługi wyszukiwania kliknij dwukrotnie kafelek Indeksatory. Przejrzyj stosunek pomyślnie indeksowanych dokumentów. Jeśli nie jest to 100%, możesz kliknąć współczynnik, aby uzyskać więcej szczegółów. 

Jeśli problem jest związany z rozmiarem pliku, może pojawić się \<błąd w ten sposób: "Nazwa \<pliku obiektu blob>" ma rozmiar> bajtów o rozmiarze pliku, który przekracza maksymalny rozmiar wyodrębniania dokumentów dla bieżącej warstwy usług." Aby uzyskać więcej informacji na temat limitów indeksatora, zobacz [Limity usług](search-limits-quotas-capacity.md).

Drugą przyczyną nieujawniania się zawartości mogą być powiązane błędy mapowania danych wejściowych/wyjściowych. Na przykład wyjściowa nazwa docelowa to "Ludzie", ale nazwa pola indeksu to małe litery "people". System może zwrócić komunikaty o sukcesie 201 dla całego potoku, więc uważasz, że indeksowanie zakończyło się pomyślnie, gdy w rzeczywistości pole jest puste. 

## <a name="tip-6-extend-processing-beyond-maximum-run-time-24-hour-window"></a>Wskazówka 6: Wydłużenie przetwarzania poza maksymalny czas wykonywania (okno 24-godzinne)

Analiza obrazu jest intensywna obliczeniowo nawet w prostych przypadkach, więc gdy obrazy są szczególnie duże lub złożone, czasy przetwarzania mogą przekraczać maksymalny dozwolony czas. 

Maksymalny czas wykonywania zależy od warstwy: kilka minut w warstwie bezpłatna, indeksowanie 24-godzinne w warstwach podlegających rozliczaniu. Jeśli przetwarzanie nie powiedzie się w ciągu 24 godzin dla przetwarzania na żądanie, przełącz się do harmonogramu, aby indeksator odebrał przetwarzanie, w którym zostało przerwane. 

W przypadku zaplanowanych indeksatorów indeksowanie wznawia się zgodnie z harmonogramem w ostatnim znanym dobrym dokumencie. Za pomocą harmonogramu cyklicznego, indeksator może pracować przez zaległości obrazu w ciągu serii godzin lub dni, aż wszystkie nieprzetworzene obrazy są przetwarzane. Aby uzyskać więcej informacji na temat składni harmonogramu, zobacz [Krok 3: Tworzenie indeksatora](search-howto-indexing-azure-blob-storage.md#step-3-create-an-indexer) lub zobacz [Jak zaplanować indeksatory dla usługi Azure Cognitive Search](search-howto-schedule-indexers.md).

> [!NOTE]
> Jeśli indeksator jest ustawiony na określony harmonogram, ale wielokrotnie kończy się niepowodzeniem w tym samym dokumencie za każdym razem, gdy działa, indeksator zacznie działać w rzadszym interwału (maksymalnie raz na 24 godziny), dopóki nie poniesie postępu. Ponownie.  Jeśli uważasz, że zostały ustalone niezależnie od problemu, który był przyczyną indeksatora utknąć w pewnym momencie, można wykonać na żądanie uruchomić indeksatora, a jeśli to pomyślnie sprawia, że postęp, indeksator powróci do ustawionego interwału harmonogramu ponownie.

W przypadku indeksowania opartego na portalu (zgodnie z opisem w przewodniku Szybki start)`"maxRunTime": "PT1H"`wybranie opcji indeksatora "uruchom raz" ogranicza przetwarzanie do 1 godziny ( ). Można rozszerzyć okno przetwarzania do czegoś dłuższego.

## <a name="tip-7-increase-indexing-throughput"></a>Wskazówka 7: Zwiększenie przepływności indeksowania

W przypadku [indeksowania równoległego](search-howto-large-index.md)umieść dane w wielu kontenerach lub wielu folderach wirtualnych wewnątrz tego samego kontenera. Następnie utwórz wiele par źródła danych i indeksatora. Wszystkie indeksatory można użyć tego samego zestawu umiejętności i zapisać w tym samym indeksie wyszukiwania docelowego, więc aplikacja wyszukiwania nie musi być świadomy tego partycjonowania.
Aby uzyskać więcej informacji, zobacz [Indeksowanie dużych zestawów danych](search-howto-indexing-azure-blob-storage.md#indexing-large-datasets).

## <a name="see-also"></a>Zobacz też
+ [Szybki start: tworzenie potoku wzbogacania si w portalu](cognitive-search-quickstart-blob.md)
+ [Samouczek: Dowiedz się, że interfejsy API wzbogacenia SI](cognitive-search-tutorial-blob.md)
+ [Określanie poświadczeń źródła danych](search-howto-indexing-azure-blob-storage.md#how-to-specify-credentials)
+ [Indeksowanie dużych zestawów danych](search-howto-indexing-azure-blob-storage.md#indexing-large-datasets)
+ [Jak zdefiniować zestaw umiejętności](cognitive-search-defining-skillset.md)
+ [Jak mapować wzbogacone pola do indeksu](cognitive-search-output-field-mapping.md)
