---
title: Rozwiązywanie problemów z porady dotyczące wyszukiwania kognitywnych w usłudze Azure Search | Dokumentacja firmy Microsoft
description: Wskazówki i rozwiązywanie problemów dotyczących konfigurowania kognitywnych wyszukiwanie potoki w usłudze Azure Search.
services: search
manager: pablocas
author: luiscabrer
ms.service: search
ms.devlang: NA
ms.workload: search
ms.topic: conceptual
ms.date: 05/01/2018
ms.author: luisca
ms.openlocfilehash: 15fc879958bfd886210a90239e0247c60fe231f9
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/07/2018
---
# <a name="troubleshooting-tips-for-cognitive-search"></a>Porady dotyczące rozwiązywania problemów kognitywnych wyszukiwania

Ten artykuł zawiera listę porady i wskazówki, które pozwalają na przenoszenie jako Rozpoczynanie pracy z możliwości kognitywnych wyszukiwania w usłudze Azure Search. 

Jeśli jeszcze tego nie zrobiono, krokowo [samouczek: Dowiedz się, jak wywoływać interfejsy API wyszukiwania kognitywnych](cognitive-search-quickstart-blob.md) praktyki stosowania wzbogacenia kognitywnych wyszukiwania ze źródłem danych obiektów blob.

## <a name="tip-1-start-with-a-small-dataset"></a>Porada 1: Rozpoczynać się od małej zestawu danych
Najlepszy sposób, aby szybko znaleźć problemy jest przyspieszenie, w którym można rozwiązać problemy. Aby skrócić czas indeksowania najlepiej dzięki zmniejszeniu liczby dokumentów do indeksowania. 

Rozpocznij od utworzenia źródła danych z tylko niewielki podzbiór dokumentów/rekordów. Przykład dokumentu powinny być dobrym reprezentację różnych dokumenty zostaną zindeksowane. 

Uruchom przykładu dokumentu za pośrednictwem potoku end-to-end i sprawdź, czy wyniki odpowiadają Twoim potrzebom. Po zakończeniu wyniki można dodać więcej plików do źródła danych.

## <a name="tip-2-make-sure-your-data-source-credentials-are-correct"></a>Porada 2: Upewnij się, że Twoje poświadczenia źródła danych są poprawne
Połączenie ze źródłem danych nie została sprawdzona, dopiero po określeniu indeksatorze, który korzysta z niego. Jeśli są wyświetlane błędy zauważyć, że indeksator nie można pobrać danych, upewnij się, że:
- Ciąg połączenia jest poprawna. Specjalnie, podczas tworzenia tokeny sygnatury dostępu Współdzielonego, upewnij się użyć formatu oczekiwanego przez usługi Azure Search. Zobacz [sposobu określania poświadczeń sekcji](
https://docs.microsoft.com/en-us/azure/search/search-howto-indexing-azure-blob-storage#how-to-specify-credentials) Aby dowiedzieć się więcej o różnych formatach obsługiwane.
- Nazwę kontenera w indeksatorze jest poprawna.

## <a name="tip-3-see-what-works-even-if-there-are-some-failures"></a>Porada 3: Zobacz, co działa, nawet jeśli istnieją pewne awarie
Czasami małych błąd zatrzymuje indeksatora w jego ścieżki. To jest poprawnie, jeśli planujesz do rozwiązywania problemów z jeden po drugim. Jednak można zignorować określonego typu błędu, umożliwiając indeksatora kontynuować, tak aby były widoczne, jakie przepływy faktycznie pracy.

W takim przypadku można stwierdzić, indeksatora ignorowanie błędów. To zrobić przez ustawienie *maxFailedItems* i *maxFailedItemsPerBatch* -1 jako część definicji indeksatora.

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
## <a name="tip-4-looking-at-enriched-documents-under-the-hood"></a>Porada 4: Patrzeć wzbogaconego dokumenty kulisy 
Wzbogaconego dokumenty są struktury tymczasowych utworzonych podczas wzbogacenia, a następnie usuwane po zakończeniu przetwarzania.

Aby przechwycić migawkę wzbogaconego dokumentu utworzona podczas indeksowania, Dodaj pole o nazwie ```enriched``` Twojego indeksu. Indeksator zrzuty w polu automatycznie reprezentację ciągu wszystkich wzbogacenia dla tego dokumentu.

```enriched``` Pole zawiera ciąg, który jest logiczną reprezentacja w pamięci wzbogaconego dokumentu w formacie JSON.  Wartość pola jest prawidłowym dokumentem JSON, jednak. Cudzysłowy są anulowane, więc musisz zastąpić `\"` z `"` do wyświetlenia dokumentu formacie JSON. 

Pole wzbogaconego jest przeznaczony do debugowania, aby lepiej zrozumieć logicznej kształtu wyrażenia są oceniane pod względem zawartości. Nie należy uwzględniać w tym polu indeksowania celów.

Dodaj ```enriched``` pole jako część definicja indeksu na potrzeby debugowania:

#### <a name="request-body-syntax"></a>Składnia treść żądania
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

## <a name="tip-5-expected-content-fails-to-appear"></a>Porada 5: Oczekiwano zawartości nie są wyświetlane

Brak zawartości może być porzucane podczas indeksowania dokumentów. Warstwami bezpłatnymi i podstawowe mają niski limity rozmiaru dokumentu. Każdego pliku, co przekracza limit zostanie usunięte podczas indeksowania. Możesz sprawdzić porzuconych dokumentów w portalu Azure. Na pulpicie nawigacyjnym usługi wyszukiwania kliknij dwukrotnie Kafelek indeksatorów. Przejrzyj stosunek indeksowane dokumenty powiodło się. Jeśli nie jest 100%, możesz kliknąć stosunek, aby uzyskać więcej szczegółów. 

Jeśli problem jest związany z rozmiar pliku, mogą pojawić następujący błąd: "< nazwa pliku > blob" ma rozmiar < plik-> Liczba bajtów, którego rozmiar przekracza maksymalny rozmiar wyodrębniania dokumentów dla Twojej bieżącej warstwy usług. " Aby uzyskać więcej informacji na granicach indeksatora, zobacz [usługi limity](search-limits-quotas-capacity.md).

Drugi Przyczyna zawartości, które nie są wyświetlane może być błędów pokrewnych mapowania wejścia/wyjścia. Na przykład nazwa docelowego danych wyjściowych jest "Osoby", ale nazwa pola indeksu jest małe "osoby". System może zwrócić 201 wiadomości Powodzenie całego procesu, więc uważasz, że indeksowania zakończyło się pomyślnie, gdy w rzeczywistości pole jest puste. 

## <a name="tip-6-extend-processing-beyond-maximum-run-time-24-hour-window"></a>Porada 6: Rozszerzenie przetwarzania poza maksymalny czas (okno 24-godzinnym) działania

Obraz analizy intensywnie praktyce w przypadkach, nawet proste, dlatego gdy obrazy są szczególnie dużych lub złożonych, czas przetwarzania może przekraczać maksymalny dozwolony czas. 

Maksymalny czas wykonywania jest zależna od warstwy: warstwy kilka minut na bezpłatna, 24-godzinnym indeksowania w warstwach rozliczeniowy. Jeśli przetwarzanie nie powiedzie się w 24-godzinnym przedziale czasu przetwarzania na żądanie, przełącz się do harmonogramu ma indeksatora odebrania przetwarzania miejsca, w którym. 

Dla zaplanowanych indeksatorów indeksowania wznawia zgodnie z harmonogramem w ostatnich znanych dobrej dokumentu. Za pomocą harmonogramu cyklicznego, indeksatora może współpracować sposób zaległości obrazu w serii godzin lub dni, dopóki wszystkie obrazy nie przetworzono są przetwarzane. Aby uzyskać więcej informacji o składni harmonogramu, zobacz [krok 3: Tworzenie usługi indeksatora](search-howto-indexing-azure-blob-storage.md#step-3-create-an-indexer).

Oparte na portalu indeksowania (zgodnie z opisem w szybkiego startu), wybierając pozycję "uruchom raz" indeksatora opcję limity przetwarzania na 1 godzinę (`"maxRunTime": "PT1H"`). Można rozszerzyć okna przetwarzania na nieco dłużej.

## <a name="tip-7-increase-indexing-throughput"></a>Porada 7: Zwiększyć przepływność indeksowania

Dla [równoległych indeksowania](search-howto-reindex.md#parallel-indexing), umieść dane w wielu kontenerów lub wielu wirtualnych folderów wewnątrz tego samego kontenera. Następnie utwórz wiele par źródła danych i indeksatora. Wszystkie indeksatory można użyć tego samego skillset i zapisu do tego samego indeksu wyszukiwania docelowego dzięki aplikacji wyszukiwania nie trzeba znać tej partycji.
Aby uzyskać więcej informacji, zobacz [indeksowania dużych zestawów danych](search-howto-indexing-azure-blob-storage.md#indexing-large-datasets).

## <a name="see-also"></a>Zobacz także
+ [Szybki Start: Tworzenie potoku kognitywnych wyszukiwania w portalu](cognitive-search-quickstart-blob.md)
+ [Samouczek: Dowiedz się kognitywnych wyszukiwania interfejsów API REST](cognitive-search-tutorial-blob.md)
+ [Określanie poświadczeń źródła danych](search-howto-indexing-azure-blob-storage.md#how-to-specify-credentials)
+ [Indeksowanie dużych zestawów danych](search-howto-indexing-azure-blob-storage.md#indexing-large-datasets)
+ [Sposób definiowania skillset](cognitive-search-defining-skillset.md)
+ [Sposób mapowania pól wzbogaconego do indeksu](cognitive-search-output-field-mapping.md)