---
title: Wskazówki dotyczące rozwiązywania problemów dla wyszukiwania kognitywnego — usługa Azure Search
description: Porady i rozwiązywanie problemów dotyczących konfigurowania cognitive Wyszukaj potoki w usłudze Azure Search.
services: search
manager: pablocas
author: luiscabrer
ms.service: search
ms.devlang: NA
ms.workload: search
ms.topic: conceptual
ms.date: 02/02/2019
ms.author: luisca
ms.custom: seodec2018
ms.openlocfilehash: c97ccd82a9c09e10572733040e238443cbf777da
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "64696593"
---
# <a name="troubleshooting-tips-for-cognitive-search"></a>Wskazówki dotyczące rozwiązywania problemów dla wyszukiwania kognitywnego

Ten artykuł zawiera listę porady i wskazówki, aby utrzymać przenoszenia, ponieważ wprowadzenie możliwości wyszukiwania kognitywnego w usłudze Azure Search. 

Jeśli jeszcze tego nie zrobiono, należy przejść przez [samouczka: Dowiedz się, jak wywołać wyszukiwania kognitywnego interfejsów API](cognitive-search-quickstart-blob.md) praktyki stosowania wzbogacenia wyszukiwania kognitywnego ze źródłem danych obiektów blob.

## <a name="tip-1-start-with-a-small-dataset"></a>Porada 1: Rozpocznij z małego zestawu danych
Najlepszym sposobem na szybkie znajdowanie problemów jest zwiększyć szybkość, z jaką można rozwiązać problemy. Najlepszym sposobem, aby skrócić czas indeksowania jest zmniejszenie liczby dokumentów, które mają być indeksowane. 

Rozpocznij od utworzenia źródła danych za pomocą tylko kilku dokumentów/rekordów. Przykład dokumentu powinny być dobrym reprezentacja różnych dokumentów, które będą indeksowane. 

Uruchom przykład dokumentu za pośrednictwem potoku end-to-end i sprawdź, czy wyniki spełniają Twoje potrzeby. Gdy jesteś zadowolony z wyników, możesz dodać więcej plików do źródła danych.

## <a name="tip-2-make-sure-your-data-source-credentials-are-correct"></a>Porada 2: Upewnij się, że Twoje poświadczenia źródła danych są poprawne
Połączenia ze źródłem danych nie zostanie zweryfikowana, dopóki nie można zdefiniować w indeksatorze, który korzysta z niego. Jeśli widzisz błędy wskazujące, że indeksator nie można pobrać danych, upewnij się, że:
- Parametry połączenia usługi jest poprawny. Specjalnie, podczas tworzenia tokenów sygnatur dostępu Współdzielonego, upewnij się, że używasz formatu oczekiwanego przez usługę Azure Search. Zobacz [sposobu określania poświadczeń sekcji](
https://docs.microsoft.com/azure/search/search-howto-indexing-azure-blob-storage#how-to-specify-credentials) Aby dowiedzieć się więcej o różnych formatach obsługiwane.
- Twoja nazwa kontenera w indeksatorze jest poprawna.

## <a name="tip-3-see-what-works-even-if-there-are-some-failures"></a>Porada 3: Zobacz, jakie rozwiązanie sprawdzi się nawet w przypadku niektórych błędów
Czasami małych błąd zatrzymuje indeksatora w jego ścieżki. Jest dobrym rozwiązaniem, jeśli planujesz rozwiązywać problemy pojedynczo. Jednak warto Ignoruj określonego typu wystąpienia błędu, umożliwiając indeksatora kontynuować, dzięki czemu można zobaczyć, co przepływy faktycznie pracy.

W takim przypadku można stwierdzić, indeksator, ignorowanie błędów. To zrobić, ustawiając *maxFailedItems* i *maxFailedItemsPerBatch* -1 jako część definicji indeksatora.

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
## <a name="tip-4-looking-at-enriched-documents-under-the-hood"></a>Porada 4: Patrząc wzbogaconego dokumenty kulisy 
Wzbogaconego dokumenty są struktury tymczasowych utworzonych podczas wzbogacania, a następnie usuwane po zakończeniu przetwarzania.

Aby przechwycić migawkę wzbogaconego dokumentu utworzoną podczas indeksowania, dodaj pole o nazwie ```enriched``` do indeksu. Indeksator automatycznie zrzuca do tego pola ciąg będący reprezentacją wszystkich wzbogaceń dokumentu.

Pole ```enriched``` będzie zawierać ciąg, który jest logiczną reprezentacją wzbogaconego dokumentu w pamięci w formacie JSON.  Wartość pola jest jednak prawidłowym dokumentem w formacie JSON. Cudzysłowy są umieszczane w sekwencji ucieczki, więc musisz zastąpić ciąg `\"` znakiem `"`, aby wyświetlić dokument w postaci sformatowanego kodu JSON. 

Pole wzbogaconego jest przeznaczona do debugowania, aby pomóc Ci zrozumieć układ logicznego wyrażenia są oceniane względem zawartości. Nie powinno zależeć od tego pola do indeksowania celów.

Dodaj ```enriched``` pola jako część definicji indeksu na potrzeby debugowania:

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

## <a name="tip-5-expected-content-fails-to-appear"></a>Porada 5: Oczekiwano zawartości nie są wyświetlane

Brak zawartości może być wynikiem dokumentów porzucane podczas indeksowania. Warstwy bezpłatna i podstawowa mają niski limity rozmiaru dokumentu. Każdy plik przekracza limit zostanie usunięte podczas indeksowania. Możesz sprawdzić, czy porzuconych dokumentów w witrynie Azure portal. Na pulpicie nawigacyjnym usługi wyszukiwania kliknij dwukrotnie Kafelek indeksatorów. Przejrzyj współczynnik pomyślnie zindeksowanych dokumentów. Jeśli nie jest 100%, możesz kliknąć współczynnik, aby uzyskać więcej informacji. 

Problem jest związany z rozmiar pliku, może zostać wyświetlony błąd taki jak ten: "Obiekt blob \<nazwa pliku >" ma rozmiar \<rozmiar pliku > bajtów, które przekracza maksymalny rozmiar do wyodrębnienia dokumentu w bieżącej warstwie usługi. " Aby uzyskać więcej informacji na temat limitów indeksatora, zobacz [limitów usług](search-limits-quotas-capacity.md).

Drugi Przyczyna, dla zawartości, które nie są wyświetlane, może być błędy powiązane mapowania wejścia/wyjścia. Na przykład nazwa docelowego dane wyjściowe to "Ludzie", ale nazwa pola indeksu to małe "ludzie". System może zwrócić 201 komunikaty sukces dla całego potoku, dlatego uważasz, że indeksowanie zakończyło się pomyślnie, gdy w rzeczywistości pole jest puste. 

## <a name="tip-6-extend-processing-beyond-maximum-run-time-24-hour-window"></a>Porada 6: Rozszerzenie przetwarzania po przekroczeniu maksymalnego dozwolonego czasu (24-godzinnego przedziału) wykonywania

Analiza obrazu jest intensywnie wymaga w przypadkach, nawet prostych, więc gdy obrazy są szczególnie duże lub zbyt złożone, czas przetwarzania może przekroczyć maksymalny dozwolony czas. 

Maksymalny czas wykonywania jest zależna od warstwy: kilka minut na bezpłatna warstwa indeksowania w warstwach płatnych 24-godzinnym. Jeśli przetwarzanie zakończy się niepowodzeniem do wykonania w okresie 24-godzinnym do przetworzenia na żądanie, przełącz się do harmonogramu, aby poczekać, aż indeksator przejmą przetwarzania tam, gdzie ją przerwaliśmy. 

Zaplanowane indeksatorów indeksowanie wznawia zgodnie z harmonogramem w ostatnich znanych dokumentu dobre. Za pomocą harmonogramu cyklicznego, indeksator może działać jego sposób za pomocą zaległości obraz ciągu kilku godzin lub dni, dopóki wszystkie obrazy nie przetworzono są przetwarzane. Aby uzyskać więcej informacji na temat Składnia harmonogramu, zobacz [krok 3: Utwórz wiadomość indeksatora](search-howto-indexing-azure-blob-storage.md#step-3-create-an-indexer).

Oparte na portalu indeksowania (zgodnie z opisem w przewodniku Szybki Start), wybierając indeksatora "uruchom raz" opcji limitów przetwarzania do 1 godziny (`"maxRunTime": "PT1H"`). Można rozszerzyć okna przetwarzania coś o nim więcej.

## <a name="tip-7-increase-indexing-throughput"></a>Porada 7: Zwiększ przepływność indeksowania

Dla [równoległe indeksowania](search-howto-large-index.md), umieść dane w wielu kontenerach lub wielu wirtualnych folderów, w tym samym kontenerze. Następnie utwórz wiele par źródło danych i indeksatora. Wszystkie indeksatory można użyć tego samego zestawu umiejętności i zapisu do tego samego docelowym indeksem wyszukiwania, więc aplikacji wyszukiwania nie musi wiedzieć, z tym partycjonowanie.
Aby uzyskać więcej informacji, zobacz [indeksowania dużych zestawów danych](search-howto-indexing-azure-blob-storage.md#indexing-large-datasets).

## <a name="see-also"></a>Zobacz także
+ [Szybki start: Tworzenie potoku usługa cognitive search w portalu](cognitive-search-quickstart-blob.md)
+ [Samouczek: Dowiedz się, usługa cognitive search interfejsów API REST](cognitive-search-tutorial-blob.md)
+ [Określanie poświadczeń źródła danych](search-howto-indexing-azure-blob-storage.md#how-to-specify-credentials)
+ [Indeksowanie dużych zestawów danych](search-howto-indexing-azure-blob-storage.md#indexing-large-datasets)
+ [Jak Definiowanie zestawu umiejętności](cognitive-search-defining-skillset.md)
+ [Sposób mapowania pól wzbogaconego do indeksu](cognitive-search-output-field-mapping.md)
