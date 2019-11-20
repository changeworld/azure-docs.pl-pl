---
title: Błędy i ostrzeżenia indeksatora
titleSuffix: Azure Cognitive Search
description: Ten artykuł zawiera informacje i rozwiązania typowych błędów i ostrzeżeń, które mogą wystąpić podczas wzbogacania danych AI na platformie Azure Wyszukiwanie poznawcze.
manager: nitinme
author: amotley
ms.author: abmotley
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 4a0a005d096702b864c770675a427184547a2b44
ms.sourcegitcommit: dbde4aed5a3188d6b4244ff7220f2f75fce65ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/19/2019
ms.locfileid: "74185701"
---
# <a name="troubleshooting-common-indexer-errors-and-warnings-in-azure-cognitive-search"></a>Rozwiązywanie problemów z typowymi błędami indeksatora i ostrzeżeniami w usłudze Azure Wyszukiwanie poznawcze

Ten artykuł zawiera informacje i rozwiązania typowych błędów i ostrzeżeń, które mogą wystąpić podczas indeksowania i wzbogacenia AI na platformie Azure Wyszukiwanie poznawcze.

Indeksowanie jest przerywane, gdy licznik błędów przekracza wartość ["maxFailedItems"](cognitive-search-concept-troubleshooting.md#tip-3-see-what-works-even-if-there-are-some-failures). 

Jeśli chcesz, aby indeksatory ignorował te błędy (i pominąć "dokumenty nieudane"), rozważ zaktualizowanie `maxFailedItems` i `maxFailedItemsPerBatch` zgodnie z opisem w [tym miejscu](https://docs.microsoft.com/rest/api/searchservice/create-indexer#general-parameters-for-all-indexers).

> [!NOTE]
> Każdy uszkodzony dokument wraz z jego kluczem dokumentu (jeśli jest dostępny) będzie wyświetlany jako błąd w stanie wykonywania indeksatora. Możesz użyć [interfejsu API indeksu](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents) , aby ręcznie przekazać dokumenty w późniejszym momencie, jeśli ustawiono indeksator, aby tolerował błędy.

Informacje o błędzie w tym artykule mogą pomóc w rozwiązaniu błędów, co umożliwia kontynuowanie indeksowania.

Ostrzeżenia nie zatrzymują indeksowania, ale wskazują warunki, które mogą spowodować nieoczekiwane wyniki. Niezależnie od tego, czy podejmujesz akcję, czy nie zależą od danych i Twojego scenariusza.

Począwszy od wersji interfejsu API `2019-05-06`, błędy indeksatora na poziomie elementu i ostrzeżenia są uporządkowane w celu zapewnienia większej przejrzystości wokół przyczyn i następnych kroków. Zawierają one następujące właściwości:

| Właściwość | Opis | Przykład |
| --- | --- | --- |
| key | Identyfikator dokumentu dokumentu, którego dotyczy błąd lub ostrzeżenie. | https://coromsearch.blob.core.windows.net/jfk-1k/docid-32112954.pdf |
| name | Nazwa operacji opisująca miejsce wystąpienia błędu lub ostrzeżenia. Ta wartość jest generowana przez następującą strukturę: [Kategoria]. [Podkategoria]. [ResourceType]. Source | DocumentExtraction. azureblob. myBlobContainerName wzbogacanie. WebApiSkill. Moja umiejętność projekcji. SearchIndex. OutputFieldMapping. myOutputFieldName projekcji. SearchIndex. MergeOrUpload. Indeksname Projekcja. KnowledgeStore. Table. webtablename |
| message | Ogólny opis błędu lub ostrzeżenia. | Nie można wykonać umiejętności, ponieważ żądanie interfejsu API sieci Web nie powiodło się. |
| details informacje | Wszelkie dodatkowe szczegóły, które mogą być pomocne w diagnozowaniu problemu, takie jak odpowiedź WebApi w przypadku niepowodzenia wykonywania niestandardowej umiejętności. | `link-cryptonyms-list - Error processing the request record : System.ArgumentNullException: Value cannot be null. Parameter name: source at System.Linq.Enumerable.All[TSource](IEnumerable`1 Źródło, Func`2 predicate) at Microsoft.CognitiveSearch.WebApiSkills.JfkWebApiSkills.`... Pozostałe ślady stosu... |
| documentationLink | Link do odpowiedniej dokumentacji ze szczegółowymi informacjami na temat debugowania i rozwiązywania problemu. Ten link będzie często wskazywał jedną z poniższych sekcji na tej stronie. | https://go.microsoft.com/fwlink/?linkid=2106475 |

<a name="could-not-read-document"/>

## <a name="error-could-not-read-document"></a>Błąd: nie można odczytać dokumentu

Indeksator nie mógł odczytać dokumentu ze źródła danych. Przyczyną może być:

| Przyczyna | Szczegóły/przykład | Rozwiązanie |
| --- | --- | --- |
| niespójne typy pól w różnych dokumentach | Typ wartości jest niezgodny z typem kolumny. Nie można zapisać `'{47.6,-122.1}'` w kolumnie autorów.  Oczekiwany typ to JArray. | Upewnij się, że typ każdego pola jest taki sam w różnych dokumentach. Na przykład jeśli pierwszy dokument `'startTime'` pole jest datą i w drugim dokumencie jest ciągiem, ten błąd zostanie trafiony. |
| błędy usługi źródłowej źródła danych | (z Cosmos DB) `{"Errors":["Request rate is large"]}` | Sprawdź wystąpienie magazynu, aby upewnić się, że jest w dobrej kondycji. Może być konieczne dostosowanie skalowania/partycjonowania. |
| problemy przejściowe | Wystąpił błąd poziomu transportu podczas otrzymywania wyników z serwera. (Dostawca: Dostawca TCP, błąd: 0 — istniejące połączenie zostało wymuszone przez hosta zdalnego | Sporadycznie występują nieoczekiwane problemy z łącznością. Spróbuj ponownie uruchomić dokument za pomocą indeksatora później. |

<a name="could-not-extract-document-content"/>

## <a name="error-could-not-extract-document-content"></a>Błąd: nie można wyodrębnić zawartości dokumentu
Indeksator ze źródłem danych obiektu BLOB nie mógł wyodrębnić zawartości z dokumentu (na przykład pliku PDF). Przyczyną może być:

| Przyczyna | Szczegóły/przykład | Rozwiązanie |
| --- | --- | --- |
| rozmiar obiektu BLOB przekracza limit. | Dokument ma `'150441598'` bajtów, który przekracza maksymalny rozmiar `'134217728'` bajtów na potrzeby wyodrębniania dokumentu dla bieżącej warstwy usług. | [Błędy indeksowania obiektów BLOB](search-howto-indexing-azure-blob-storage.md#dealing-with-errors) |
| Obiekt BLOB ma nieobsługiwany typ zawartości | Dokument zawiera nieobsługiwany typ zawartości `'image/png'` | [Błędy indeksowania obiektów BLOB](search-howto-indexing-azure-blob-storage.md#dealing-with-errors) |
| Obiekt BLOB jest zaszyfrowany | Nie można przetworzyć dokumentu — może on być zaszyfrowany lub chroniony hasłem. | Możesz pominąć obiekt BLOB za pomocą [ustawień obiektu BLOB](search-howto-indexing-azure-blob-storage.md#controlling-which-parts-of-the-blob-are-indexed). |
| problemy przejściowe | Wystąpił błąd podczas przetwarzania obiektu BLOB: żądanie zostało przerwane: żądanie zostało anulowane. | Sporadycznie występują nieoczekiwane problemy z łącznością. Spróbuj ponownie uruchomić dokument za pomocą indeksatora później. |

<a name="could-not-parse-document"/>

## <a name="error-could-not-parse-document"></a>Błąd: nie można przeanalizować dokumentu
Indeksator odczytuje dokument ze źródła danych, ale wystąpił problem podczas konwertowania zawartości dokumentu do określonego schematu mapowania pól. Przyczyną może być:

| Przyczyna | Szczegóły/przykład | Rozwiązanie |
| --- | --- | --- |
| Brak klucza dokumentu | Brak klucza dokumentu lub jest on pusty | Upewnij się, że wszystkie dokumenty mają prawidłowe klucze dokumentu |
| Klucz dokumentu jest nieprawidłowy | Klucz dokumentu nie może mieć więcej niż 1024 znaków | Zmodyfikuj klucz dokumentu, aby spełniał wymagania dotyczące weryfikacji. |
| Nie można zastosować mapowania pola do pola | Nie można zastosować funkcji mapowania `'functionName'` do `'fieldName'`pól. Tablica nie może mieć wartości null. Nazwa parametru: bajty | Dokładnie sprawdź [mapowania pól](search-indexer-field-mappings.md) zdefiniowane w indeksatorze i porównaj z danymi określonego pola dokumentu, który się nie powiódł. Może być konieczne zmodyfikowanie mapowań pól lub danych dokumentu. |
| Nie można odczytać wartości pola | Nie można odczytać wartości `'fieldName'` kolumny w `'fieldIndex'`indeksu. Wystąpił błąd poziomu transportu podczas otrzymywania wyników z serwera. (Dostawca: Dostawca TCP, błąd: 0 — istniejące połączenie zostało wymuszone przez hosta zdalnego). | Te błędy są zwykle spowodowane nieoczekiwanymi problemami z łącznością z usługą źródłową źródła danych. Spróbuj ponownie uruchomić dokument za pomocą indeksatora później. |

<a name="could-not-execute-skill"/>

## <a name="error-could-not-execute-skill"></a>Błąd: nie można wykonać umiejętności
Indeksator nie mógł uruchomić umiejętności w zestawu umiejętności.

| Przyczyna | Szczegóły/przykład | Rozwiązanie |
| --- | --- | --- |
| Przejściowe problemy z łącznością | Wystąpił błąd przejściowy. Spróbuj ponownie później. | Sporadycznie występują nieoczekiwane problemy z łącznością. Spróbuj ponownie uruchomić dokument za pomocą indeksatora później. |
| Potencjalna usterka produktu | Wystąpił nieoczekiwany błąd. | Oznacza to nieznaną klasę błędu i może oznaczać, że występuje usterka produktu. Zapoznaj się z [biletem pomocy technicznej](https://ms.portal.azure.com/#create/Microsoft.Support) , aby uzyskać pomoc. |
| Umiejętność napotkała błąd podczas wykonywania | (Z poziomu umiejętności scalania) Co najmniej jedna wartość przesunięcia była nieprawidłowa i nie można jej przeanalizować. Wstawiono elementy na końcu tekstu | Aby rozwiązać ten problem, użyj informacji w komunikacie o błędzie. Ten rodzaj błędu będzie wymagał działania do rozwiązania. |

<a name="could-not-execute-skill-because-the-web-api-request-failed"/>

## <a name="error-could-not-execute-skill-because-the-web-api-request-failed"></a>Błąd: nie można wykonać umiejętności, ponieważ żądanie internetowego interfejsu API nie powiodło się
Wykonanie kwalifikacji nie powiodło się, ponieważ wywołanie interfejsu API sieci Web nie powiodło się. Zazwyczaj ta klasa awarii występuje, gdy są używane niestandardowe umiejętności, w takim przypadku konieczne będzie debugowanie niestandardowego kodu w celu rozwiązania problemu. Jeśli zamiast tego błąd pochodzi z wbudowanej umiejętności, zapoznaj się z komunikatem o błędzie, aby uzyskać pomoc w rozwiązywaniu problemu.

<a name="could-not-execute-skill-because-web-api-skill-response-is-invalid"/>

## <a name="error-could-not-execute-skill-because-web-api-skill-response-is-invalid"></a>Błąd: nie można wykonać umiejętności, ponieważ odpowiedź dotycząca umiejętności interfejsu API sieci Web jest nieprawidłowa
Wykonanie kwalifikacji nie powiodło się, ponieważ wywołanie interfejsu API sieci Web zwróciło nieprawidłową odpowiedź. Zazwyczaj ta klasa awarii występuje, gdy są używane niestandardowe umiejętności, w takim przypadku konieczne będzie debugowanie niestandardowego kodu w celu rozwiązania problemu. Jeśli zamiast tego niepowodzenie pochodzi z wbudowanej umiejętności, Utwórz [bilet pomocy technicznej](https://ms.portal.azure.com/#create/Microsoft.Support) , aby uzyskać pomoc.

<a name="skill-did-not-execute-within-the-time-limit"/>

## <a name="error-skill-did-not-execute-within-the-time-limit"></a>Błąd: umiejętność nie została wykonana w ramach limitu czasu
Istnieją dwa przypadki, w których może wystąpić ten komunikat o błędzie, z których każdy powinien być traktowany inaczej. Postępuj zgodnie z poniższymi instrukcjami, w zależności od tego, jaką umiejętność zwróciła ten błąd.

### <a name="built-in-cognitive-service-skills"></a>Wbudowane umiejętności usługi poznawczej
Wiele wbudowanych umiejętności poznawczych, takich jak wykrywanie języka, rozpoznawanie jednostek lub OCR, są obsługiwane przez punkt końcowy interfejsu API usługi poznawczej. Czasami występują przejściowe problemy z tymi punktami końcowymi i upłynął limit czasu żądania. W przypadku problemów przejściowych nie ma żadnych naprawienia, z wyjątkiem oczekiwania i spróbuj ponownie. Jako środek zaradczy należy rozważyć skonfigurowanie indeksatora do [uruchamiania zgodnie z harmonogramem](search-howto-schedule-indexers.md). Zaplanowane indeksowanie jest odbierane w miejscu, w którym zostało pozostawione. Przy założeniu, że przejściowe problemy są rozwiązywane, indeksowanie i przetwarzanie umiejętności poznawcze powinny mieć możliwość kontynuowania przy następnym zaplanowanym uruchomieniu.

Jeśli ten błąd będzie nadal występować w tym samym dokumencie dla wbudowanej umiejętności poznawczej, Utwórz [bilet pomocy technicznej](https://ms.portal.azure.com/#create/Microsoft.Support) , aby uzyskać pomoc, ponieważ nie jest to oczekiwane.

### <a name="custom-skills"></a>Umiejętności niestandardowe
Jeśli wystąpi błąd przekroczenia limitu czasu z utworzoną niestandardową umiejętnością, istnieje kilka rzeczy, które można wypróbować. Najpierw przejrzyj swoją niestandardową umiejętność i upewnij się, że nie jest ona zablokowana w pętli nieskończonej i że zwraca wynik konsekwentnie. Po potwierdzeniu, że jest to przypadek, ustal, jaki jest czas wykonywania danej umiejętności. Jeśli wartość `timeout` nie została jawnie ustawiona w niestandardowej definicji umiejętności, domyślna `timeout` wynosi 30 sekund. Jeśli 30 sekund nie jest wystarczająco długi, aby można było wykonać swoją umiejętność, możesz określić wyższą `timeout` wartość w niestandardowej definicji umiejętności. Oto przykład niestandardowej definicji umiejętności, w której limit czasu jest ustawiony na 90 sekund:

```json
  {
        "@odata.type": "#Microsoft.Skills.Custom.WebApiSkill",
        "uri": "<your custom skill uri>",
        "batchSize": 1,
        "timeout": "PT90S",
        "context": "/document",
        "inputs": [
          {
            "name": "input",
            "source": "/document/content"
          }
        ],
        "outputs": [
          {
            "name": "output",
            "targetName": "output"
          }
        ]
      }
```

Maksymalna wartość, którą można ustawić dla parametru `timeout` to 230 sekund.  Jeśli niestandardowa umiejętność nie będzie działać w ciągu 230 sekund, można rozważyć zmniejszenie `batchSize` niestandardowej umiejętności, dzięki czemu będzie ona mogła przetwarzać mniejsze dokumenty w ramach jednego wykonania.  Jeśli ustawiono już `batchSize` na 1, należy ponownie napisać umiejętność, aby można było wykonać ją w mniej niż 230 sekund lub w inny sposób podzielić ją na wiele umiejętności niestandardowych, tak aby czas wykonywania dla każdej pojedynczej umiejętności niestandardowej wynosił maksymalnie 230 sekund. Zapoznaj się z dokumentacją dotyczącą [niestandardowych umiejętności](cognitive-search-custom-skill-web-api.md) , aby uzyskać więcej informacji.

<a name="could-not-mergeorupload--delete-document-to-the-search-index"/>

## <a name="error-could-not-mergeorupload--delete-document-to-the-search-index"></a>Błąd: nie można "`MergeOrUpload`" | dokument "`Delete`" w indeksie wyszukiwania

Dokument został odczytany i przetworzony, ale indeksator nie mógł go dodać do indeksu wyszukiwania. Przyczyną może być:

| Przyczyna | Szczegóły/przykład | Rozwiązanie |
| --- | --- | --- |
| Pole zawiera termin, który jest zbyt duży | Termin w dokumencie jest większy niż [limit 32 KB](search-limits-quotas-capacity.md#api-request-limits) | Można uniknąć tego ograniczenia, upewniając się, że pole nie jest skonfigurowane jako możliwe do filtrowania, tworzenia i sortowania.
| Dokument jest zbyt duży, aby można go było zindeksować | Dokument jest większy niż [Maksymalny rozmiar żądania interfejsu API](search-limits-quotas-capacity.md#api-request-limits) | [Jak indeksować duże zestawy danych](search-howto-large-index.md)
| Dokument zawiera zbyt wiele obiektów w kolekcji | Kolekcja w dokumencie przekracza [maksymalną liczbę elementów we wszystkich złożonych kolekcjach kolekcji](search-limits-quotas-capacity.md#index-limits) | Zalecamy zmniejszenie rozmiaru kolekcji złożonej w dokumencie do wartości poniżej limitu i uniknięcie dużego użycia magazynu.
| Problem z nawiązaniem połączenia z docelowym indeksem (który utrzymuje się po ponownych próbach), ponieważ usługa jest w innym załadowaniu, na przykład w przypadku wykonywania zapytań lub indeksowania. | Nie można ustanowić połączenia w celu zaktualizowania indeksu. Usługa wyszukiwania jest w dużym obciążeniu. | [Skalowanie w górę usługi wyszukiwania](search-capacity-planning.md)
| Trwa poprawianie usługi wyszukiwania w ramach aktualizacji usługi lub jest ona w trakcie ponownej konfiguracji topologii. | Nie można ustanowić połączenia w celu zaktualizowania indeksu. Usługa wyszukiwania jest obecnie wyłączona/usługa wyszukiwania przechodzi do przejścia. | Skonfiguruj usługę z co najmniej 3 replikami na 99,9% dostępności na potrzeby [dokumentacji umowy SLA](https://azure.microsoft.com/support/legal/sla/search/v1_0/)
| Niepowodzenie w źródłowym zasobów obliczeniowych/sieciowych (rzadkich) | Nie można ustanowić połączenia w celu zaktualizowania indeksu. Wystąpił nieznany błąd. | Skonfiguruj indeksatory do [uruchomienia zgodnie z harmonogramem](search-howto-schedule-indexers.md) , aby przeprowadzić pobieranie z niepowodzenia.
| Żądanie indeksowania wprowadzone do indeksu docelowego nie zostało potwierdzone w przedziale czasu z powodu problemów z siecią. | Nie można nawiązać połączenia z indeksem wyszukiwania w odpowiednim czasie. | Skonfiguruj indeksatory do [uruchomienia zgodnie z harmonogramem](search-howto-schedule-indexers.md) , aby przeprowadzić pobieranie z niepowodzenia. Ponadto spróbuj zmniejszyć [rozmiar wsadu](https://docs.microsoft.com/rest/api/searchservice/create-indexer#parameters) indeksatora, jeśli ten błąd będzie się utrzymywał.

<a name="could-not-index-document-because-the-indexer-data-to-index-was-invalid"/>

## <a name="error-could-not-index-document-because-the-indexer-data-to-index-was-invalid"></a>Błąd: nie można indeksować dokumentu, ponieważ dane indeksatora są nieprawidłowe

Dokument został odczytany i przetworzony, ale z powodu niezgodności konfiguracji pól indeksu i charakteru danych wyodrębnionych przez indeksator, nie można go dodać do indeksu wyszukiwania. Przyczyną może być:

| Przyczyna | Szczegóły/przykład
| --- | ---
| Typ danych pól wyodrębnionych przez indeksator jest niezgodny z modelem danych odpowiedniego docelowego pola indeksu. | Pole danych "_Data_" w dokumencie z kluczem "_Data_" ma nieprawidłową wartość "typu EDM. String" ". Oczekiwany typ to "Kolekcja (EDM. String)". |
| Nie można wyodrębnić żadnej jednostki JSON z wartości ciągu. | Nie można przeanalizować wartości "typu" EDM. String "" dla pola "_Data_" jako obiektu JSON. Błąd: "po przeanalizowaniu wartości napotkano nieoczekiwany znak:" ". Ścieżka "_Path_", wiersz 1, pozycja 3162. " |
| Nie można wyodrębnić kolekcji jednostek JSON z wartości ciągu.  | Nie można przeanalizować wartości "typu" EDM. String "" dla pola "_Data_" jako tablicy JSON. Błąd: "po przeanalizowaniu wartości napotkano nieoczekiwany znak:" ". Ścieżka ' [0] ', wiersz 1, pozycja 27. ' |
| W dokumencie źródłowym odnaleziono nieznany typ. | Nieznany typ "_nieznany_" nie może być indeksowany |
| W dokumencie źródłowym użyto niezgodnej notacji punktów geograficznych. | Literały ciągu punktu WKT nie są obsługiwane. Zamiast tego użyj literałów punktu GEOJSON |

We wszystkich tych przypadkach należy zapoznać się z [obsługiwanymi typami danych](https://docs.microsoft.com/rest/api/searchservice/supported-data-types) i [mapą typów danych dla indeksatorów](https://docs.microsoft.com/rest/api/searchservice/data-type-map-for-indexers-in-azure-search) , aby upewnić się, że schemat indeksu jest prawidłowo skonstruowany i skonfigurowano odpowiednie [mapowania pól indeksatora](search-indexer-field-mappings.md). Komunikat o błędzie będzie zawierać szczegóły, które mogą pomóc w śledzeniu źródła niezgodności.

<a name="could-not-process-document-within-indexer-max-run-time"/>

## <a name="error-could-not-process-document-within-indexer-max-run-time"></a>Błąd: nie można przetworzyć dokumentu w maksymalnym czasie wykonywania indeksatora

Ten błąd występuje, gdy indeksator nie może zakończyć przetwarzania pojedynczego dokumentu ze źródła danych w dozwolonym czasie wykonywania. [Maksymalny czas działania](search-limits-quotas-capacity.md#indexer-limits) jest krótszy, gdy są używane umiejętności. W przypadku wystąpienia tego błędu, jeśli maxFailedItems ustawiono wartość inną niż 0, Indeksator pomija dokument w przyszłych uruchomieniach, tak aby indeksowanie mogło postępować. Jeśli nie możesz mieć możliwości pominięcia jakiegokolwiek dokumentu lub jeśli ten błąd występuje w sposób ciągły, rozważ przerwanie dokumentów w mniejszych dokumentach, aby można było wykonać częściowe postępy w ramach jednego wykonywania indeksatora.

<a name="could-not-execute-skill-because-a-skill-input-was-invalid"/>

## <a name="warning-could-not-execute-skill-because-a-skill-input-was-invalid"></a>Ostrzeżenie: nie można wykonać umiejętności, ponieważ dane wejściowe dotyczące kwalifikacji były nieprawidłowe
Indeksator nie mógł uruchomić umiejętności w zestawu umiejętności, ponieważ brakuje w nim danych wejściowych, niewłaściwego typu lub w inny sposób.

Umiejętności poznawcze mają wymagane dane wejściowe i opcjonalne dane wejściowe. Na przykład [umiejętność wyodrębniania frazy klucza](cognitive-search-skill-keyphrases.md) ma dwa wymagane dane wejściowe `text`, `languageCode`i brak opcjonalnych danych wejściowych. Jeśli jakiekolwiek wymagane dane wejściowe są nieprawidłowe, umiejętność zostanie pominięta i wygeneruje ostrzeżenie. Pominięte umiejętności nie generują żadnych danych wyjściowych, więc jeśli inne umiejętności korzystają z wyników pominiętych umiejętności, mogą generować dodatkowe ostrzeżenia.

Jeśli chcesz podać wartość domyślną w przypadku braku danych wejściowych, możesz użyć [umiejętności warunkowej](cognitive-search-skill-conditional.md) do wygenerowania wartości domyślnej, a następnie użyć danych wyjściowych z [umiejętności warunkowej](cognitive-search-skill-conditional.md) jako danych wejściowych umiejętności.


```json
{
    "@odata.type": "#Microsoft.Skills.Util.ConditionalSkill",
    "context": "/document",
    "inputs": [
        { "name": "condition", "source": "= $(/document/language) == null" },
        { "name": "whenTrue", "source": "= 'en'" },
        { "name": "whenFalse", "source": "= $(/document/language)" }
    ],
    "outputs": [ { "name": "output", "targetName": "languageWithDefault" } ]
}
```

| Przyczyna | Szczegóły/przykład | Rozwiązanie |
| --- | --- | --- |
| Dane wejściowe umiejętności są niewłaściwego typu | Wymagane `X` danych wejściowych kwalifikacji nie ma oczekiwanego typu `String`. Wymagane `X` danych wejściowych kwalifikacji nie ma oczekiwanego formatu. | Pewne umiejętności oczekują danych wejściowych określonych typów, na przykład [umiejętność tonacji](cognitive-search-skill-sentiment.md) oczekuje, że `text` być ciągiem. Jeśli dane wejściowe określają wartość różną od ciągu, wówczas umiejętność nie zostanie wykonana i nie wygeneruje żadnych danych wyjściowych. Upewnij się, że zestaw danych zawiera wartości wejściowe, które są jednorodne w typie, lub Użyj [niestandardowej umiejętności interfejsu API sieci Web](cognitive-search-custom-skill-web-api.md) , aby wstępnie przetworzyć dane wejściowe. Jeśli Iteracja jest przeprowadzana przez tablicę, sprawdź kontekst umiejętności i wprowadź `*` w poprawnych pozycjach. Zwykle zarówno kontekst, jak i źródło danych wejściowych powinny kończyć się `*` dla tablic. |
| Brak danych wejściowych kwalifikacji | Brak wymaganego `X` wejściowego umiejętności. | Jeśli wszystkie dokumenty otrzymają to ostrzeżenie, prawdopodobnie występuje literówka w ścieżkach wejściowych i należy dokładnie sprawdzić wielkość liter nazwy właściwości, dodatkowe lub brakujące `*` w ścieżce, a dokumenty ze źródła danych definiują wymagane dane wejściowe. |
| Dane wejściowe kodu języka umiejętności są nieprawidłowe | `languageCode` danych wejściowych umiejętności zawiera następujące kody języka `X,Y,Z`, co najmniej jeden z nich jest nieprawidłowy. | Zobacz więcej szczegółów [poniżej](cognitive-search-common-errors-warnings.md#skill-input-languagecode-has-the-following-language-codes-xyz-at-least-one-of-which-is-invalid) |

<a name="skill-input-languagecode-has-the-following-language-codes-xyz-at-least-one-of-which-is-invalid"/>

## <a name="warning--skill-input-languagecode-has-the-following-language-codes-xyz-at-least-one-of-which-is-invalid"></a>Ostrzeżenie: dane wejściowe umiejętności "languageCode" zawierają następujące kody języka: "X, Y, Z", co najmniej jeden z nich jest nieprawidłowy.
Co najmniej jedna z wartości przeniesiona do opcjonalnego `languageCode` danych wejściowych poziomu umiejętności podrzędnej nie jest obsługiwana. Taka sytuacja może wystąpić, jeśli przekazujesz dane wyjściowe [LanguageDetectionSkill](cognitive-search-skill-language-detection.md) do kolejnych umiejętności, a dane wyjściowe składają się z większej liczby języków niż jest to obsługiwane w tych umiejętnościach podrzędnych.

Jeśli wiesz, że zestaw danych znajduje się w jednym języku, należy usunąć [LanguageDetectionSkill](cognitive-search-skill-language-detection.md) `languageCode` i dane wejściowe dotyczące umiejętności, a zamiast tego użyć parametru umiejętności `defaultLanguageCode` dla tej umiejętności, przy założeniu, że język jest obsługiwany dla tej umiejętności.

Jeśli wiesz, że zestaw danych zawiera wiele języków, a więc potrzebujesz [LanguageDetectionSkill](cognitive-search-skill-language-detection.md) i `languageCode` dane wejściowe, rozważ dodanie [ConditionalSkill](cognitive-search-skill-conditional.md) w celu odfiltrowania tekstu w językach, które nie są obsługiwane przed przekazaniem w tekst na kwalifikacje podrzędne.  Oto przykład tego, co może wyglądać jak w przypadku EntityRecognitionSkill:

```json
{
    "@odata.type": "#Microsoft.Skills.Util.ConditionalSkill",
    "context": "/document",
    "inputs": [
        { "name": "condition", "source": "= $(/document/language) == 'de' || $(/document/language) == 'en' || $(/document/language) == 'es' || $(/document/language) == 'fr' || $(/document/language) == 'it'" },
        { "name": "whenTrue", "source": "/document/content" },
        { "name": "whenFalse", "source": "= null" }
    ],
    "outputs": [ { "name": "output", "targetName": "supportedByEntityRecognitionSkill" } ]
}
```

Poniżej przedstawiono niektóre odwołania do obecnie obsługiwanych języków dla każdej z umiejętności, które mogą generować ten komunikat o błędzie:
* [Obsługiwane języki analiza tekstu](https://docs.microsoft.com/azure/cognitive-services/text-analytics/text-analytics-supported-languages) (dla [KeyPhraseExtractionSkill](cognitive-search-skill-keyphrases.md), [EntityRecognitionSkill](cognitive-search-skill-entity-recognition.md)i [SentimentSkill](cognitive-search-skill-sentiment.md))
* [Języki obsługiwane przez translatora](https://docs.microsoft.com/azure/cognitive-services/translator/language-support) (dla [tekstu TranslationSkill](cognitive-search-skill-text-translation.md))
* [SplitSkill tekstu](cognitive-search-skill-textsplit.md) Obsługiwane języki: `da, de, en, es, fi, fr, it, ko, pt`

<a name="skill-input-was-truncated"/>

## <a name="warning-skill-input-was-truncated"></a>Ostrzeżenie: dane wejściowe dotyczące umiejętności zostały obcięte
Umiejętności poznawcze mają ograniczone długość tekstu, który można analizować jednocześnie. Jeśli wprowadzanie tekstu tych umiejętności przekracza ten limit, spowoduje to obcinanie tekstu w celu spełnienia limitu, a następnie przeprowadzenie wzbogacania tego tekstu. Oznacza to, że umiejętność jest wykonywana, ale nie na wszystkich Twoich danych.

W poniższym przykładzie LanguageDetectionSkill pole wejściowe `'text'` może wyzwolić to ostrzeżenie, jeśli przekracza limit liczby znaków. Limity danych wejściowych kwalifikacji można znaleźć w dokumentacji dotyczącej [umiejętności](cognitive-search-predefined-skills.md).

```json
 {
    "@odata.type": "#Microsoft.Skills.Text.LanguageDetectionSkill",
    "inputs": [
      {
        "name": "text",
        "source": "/document/text"
      }
    ],
    "outputs": [...]
  }
```

Jeśli chcesz upewnić się, że cały tekst jest analizowany, rozważ użycie opcji [Podziel umiejętność](cognitive-search-skill-textsplit.md).

<a name="web-api-skill-response-contains-warnings"/>

## <a name="warning-web-api-skill-response-contains-warnings"></a>Ostrzeżenie: odpowiedź na umiejętność interfejsu API sieci Web zawiera ostrzeżenia
Indeksator mógł uruchomić umiejętność w zestawu umiejętności, ale odpowiedź z żądania internetowego interfejsu API wskazywała, że wystąpiły ostrzeżenia podczas wykonywania. Zapoznaj się z ostrzeżeniami, aby zrozumieć, w jaki sposób wpływają dane oraz czy nie są wymagane akcje.

<a name="the-current-indexer-configuration-does-not-support-incremental-progress"/>

## <a name="warning-the-current-indexer-configuration-does-not-support-incremental-progress"></a>Ostrzeżenie: Bieżąca konfiguracja indeksatora nie obsługuje przyrostowego postępu

To ostrzeżenie występuje tylko w przypadku Cosmos DB źródeł danych.

Przyrostowy postęp podczas indeksowania zapewnia, że jeśli wykonywanie indeksatora zostanie przerwane przez przejściowe błędy lub limit czasu wykonywania, indeksator może zostać pobrany w miejscu, w którym zostanie pozostawiony po następnym uruchomieniu, zamiast konieczności ponownego indeksowania całej kolekcji od podstaw. Jest to szczególnie ważne podczas indeksowania dużych kolekcji.

Możliwość wznowienia nieukończonego zadania indeksowania jest predykatem według dokumentów uporządkowanych według kolumny `_ts`. Indeksator używa sygnatury czasowej, aby określić, który dokument powinien zostać pobrany dalej. Jeśli brakuje kolumny `_ts` lub jeśli indeksator nie może określić, czy kwerenda niestandardowa jest uporządkowana, indeksator rozpoczyna się od początku i zobaczysz to ostrzeżenie.

Możliwe jest przesłonięcie tego zachowania, co umożliwia stopniowe postęp i pomijanie tego ostrzeżenia przy użyciu właściwości konfiguracja `assumeOrderByHighWatermarkColumn`.

Aby uzyskać więcej informacji, zobacz [przyrostowy postęp i zapytania niestandardowe](search-howto-index-cosmosdb.md#IncrementalProgress).

<a name="some-data-was-lost-during projection-row-x-in-table-y-has-string-property-z-which-was-too-long"/>

## <a name="warning-some-data-was-lost-during-projection-row-x-in-table-y-has-string-property-z-which-was-too-long"></a>Ostrzeżenie: niektóre dane zostały utracone podczas projekcji. Wiersz "X" w tabeli "Y" ma właściwość ciągu "Z", która jest zbyt długa.

[Usługa Table Storage](https://azure.microsoft.com/services/storage/tables) ma limity dotyczące sposobu, w jaki mogą być [Właściwości dużych jednostek](https://docs.microsoft.com/rest/api/storageservices/understanding-the-table-service-data-model#property-types) . Ciągi mogą zawierać co najwyżej 32 000 znaków. Jeśli wiersz z właściwością ciągu dłuższą niż 32 000 znaków jest rzutowany, zachowywane są tylko pierwsze znaki 32 000. Aby obejść ten problem, należy unikać tworzenia projektów wierszy z właściwościami ciągu dłuższymi niż 32 000 znaków.

<a name="truncated-extracted-text-to-x-characters"/>

## <a name="warning-truncated-extracted-text-to-x-characters"></a>Ostrzeżenie: obcięto wyodrębniony tekst do X znaków
Indeksatory ograniczają ilość tekstu, który można wyodrębnić z dowolnego dokumentu. Ten limit zależy od warstwy cenowej: 32 000 znaków dla warstwy Bezpłatna, 64 000 dla podstawowych i 4 000 000 dla warstw standardowa, standardowa S2 i Standardowa S3. Tekst, który został obcięty, nie będzie indeksowany. Aby uniknąć tego ostrzeżenia, spróbuj rozdzielić dokumenty z dużymi ilościami tekstu na kilka mniejszych dokumentów. 

Aby uzyskać więcej informacji, zobacz [limity indeksatora](search-limits-quotas-capacity.md#indexer-limits).

<a name="could-not-map-output-field-x-to-search-index"/>

## <a name="warning-could-not-map-output-field-x-to-search-index"></a>Ostrzeżenie: nie można zmapować pola wyjściowego "X" na indeks wyszukiwania
Mapowania pól wyjściowych, które odwołują się do nieistniejących/niepustych danych, będą generować ostrzeżenia dla każdego dokumentu i powodować puste pole indeksu. Aby obejść ten problem, należy dokładnie sprawdzić ścieżki źródłowe mapowania pól wyjściowych dla możliwych błędów lub ustawić wartość domyślną przy użyciu [umiejętności warunkowej](cognitive-search-skill-conditional.md#sample-skill-definition-2-set-a-default-value-for-a-value-that-doesnt-exist).

<a name="the-data-change-detection-policy-is-configured-to-use-key-column-x"/>

## <a name="warning-the-data-change-detection-policy-is-configured-to-use-key-column-x"></a>Ostrzeżenie: zasady wykrywania zmian danych są skonfigurowane do korzystania z kolumny klucza "X"
[Zasady wykrywania zmian danych](https://docs.microsoft.com/rest/api/searchservice/create-data-source#data-change-detection-policies) mają określone wymagania dotyczące kolumn, których używają do wykrywania zmian. Jedno z tych wymagań polega na tym, że ta kolumna jest aktualizowana za każdym razem, gdy element źródłowy zostanie zmieniony. Innym wymaganiem jest to, że nowa wartość dla tej kolumny jest większa niż Poprzednia wartość. Kolumny kluczy nie spełniają tego wymagania, ponieważ nie zmieniają w każdej aktualizacji. Aby obejść ten problem, wybierz inną kolumnę dla zasad wykrywania zmian.