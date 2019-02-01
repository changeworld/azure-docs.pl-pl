---
title: Korzystanie z analizy usługi Azure Storage w celu zbierania danych dzienników i metryk | Dokumentacja firmy Microsoft
description: Usługa Storage Analytics umożliwia śledzenie danych metryk dla wszystkich usług magazynu oraz w celu zbierania dzienników na potrzeby magazynu obiektów Blob, kolejek i tabel.
services: storage
author: roygara
ms.service: storage
ms.devlang: dotnet
ms.topic: article
ms.date: 03/03/2017
ms.author: rogarana
ms.subservice: common
ms.openlocfilehash: 9cd4845bcf107941f969255eb223567d4341ea41
ms.sourcegitcommit: fea5a47f2fee25f35612ddd583e955c3e8430a95
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/31/2019
ms.locfileid: "55508508"
---
# <a name="storage-analytics"></a>Analityka magazynu

Analityka usługi Azure Storage umożliwia rejestrowanie i dostarcza danych metrycznych dotyczących konta magazynu. Dane te mogą posłużyć do śledzenia żądań, analizy tendencji użycia oraz diagnozowania problemów z kontem magazynu.

Aby korzystać z usługi Storage Analytics, należy włączyć je osobno dla każdej usługi, które chcesz monitorować. Można je włączyć z [witryny Azure Portal](https://portal.azure.com). Aby uzyskać więcej informacji, zobacz [monitorowanie konta magazynu w witrynie Azure Portal](storage-monitor-storage-account.md). Można również włączyć analizy programowo za pośrednictwem interfejsu API REST lub biblioteka klienta usługi Storage. Użyj [pobrać właściwości usługi obiektów Blob](https://msdn.microsoft.com/library/hh452239.aspx), [pobrać właściwości usługi kolejki](https://msdn.microsoft.com/library/hh452243.aspx), [pobrać właściwości usługi tabeli](https://msdn.microsoft.com/library/hh452238.aspx), i [pobrać właściwości usługi plików](https://msdn.microsoft.com/library/mt427369.aspx)Procedura włączania analityka magazynu dla każdej usługi.

Zagregowane dane są przechowywane w dobrze znanych obiektów blob (w przypadku rejestrowania) i dobrze znanych tabelach (w przypadku metryk), które mogą być dostępne za pomocą usługi obiektów Blob i Table service API.

Usługa Storage Analytics obowiązuje limit 20 TB na ilości przechowywanych danych, która jest niezależna od całkowitego limitu konta magazynu. Aby uzyskać więcej informacji na temat rozliczeń i zasadami przechowywania danych, zobacz [analizy magazynu i rozliczeń oraz](https://msdn.microsoft.com/library/hh360997.aspx). Aby uzyskać więcej informacji na temat limitów konta magazynu, zobacz [usługi Azure Storage dotyczące skalowalności i cele wydajności](storage-scalability-targets.md).

Aby uzyskać szczegółowy przewodnik na temat korzystania z usługi Storage Analytics i inne narzędzia do identyfikowania, diagnozowanie i rozwiązywanie problemów związanych z usługi Azure Storage, zobacz [monitorowanie, diagnozowanie i rozwiązywanie problemów z usługi Microsoft Azure Storage](storage-monitoring-diagnosing-troubleshooting.md).

## <a name="about-storage-analytics-logging"></a>Temat Storage analytics rejestrowania
Usługa Storage Analytics rejestruje szczegółowe informacje na temat udane i nieudane żądania do usługi storage. Informacja ta może służyć do monitorowania poszczególnych żądań i diagnozowanie problemów z usługą magazynu. Żądania są rejestrowane na zasadzie największej staranności.

Wpisy dziennika są tworzone tylko wtedy, gdy działanie usługi magazynu. Na przykład jeśli konto magazynu ma działanie jego usługi obiektów Blob, ale nie w jego tabel lub kolejek usługi, zostanie utworzony tylko dzienniki odnoszących się do usługi obiektów Blob.

Rejestrowanie analizy magazynu nie jest dostępna dla usługi Azure Files.

### <a name="logging-authenticated-requests"></a>Uwierzytelnione rejestrowanie żądań
Rejestrowane są następujące typy żądań uwierzytelnionych:

* Żądania zakończone powodzeniem.
* Żądania zakończone niepowodzeniem oraz tym limit czasu, ograniczanie przepustowości, sieci, autoryzacji i inne błędy.
* Żądania przy użyciu sygnatury dostępu współdzielonego (SAS), w tym żądania nie powiodło się, jak i pomyślnie.
* Żądania do analizy danych.

Żądania wysyłane przez analityka magazynu, takie jak tworzenie dziennika lub usuwanie, nie są rejestrowane. Pełną listę zarejestrowanych danych jest udokumentowany w [operacji rejestrowane analizy magazynu i komunikaty o stanie](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages.md) i [Format dziennika analizy magazynu](/rest/api/storageservices/storage-analytics-log-format.md) tematów.

### <a name="logging-anonymous-requests"></a>Rejestrowanie żądań anonimowych
Rejestrowane są następujące typy anonimowe żądania:

* Żądania zakończone powodzeniem.
* Błędy serwera.
* Błędy przekroczenia limitu czasu dla klienta i serwera.
* Zakończone niepowodzeniem żądania GET z kodem błędu 304 (nie zmodyfikowano).

Inne zakończone niepowodzeniem żądania anonimowe nie są rejestrowane. Pełną listę zarejestrowanych danych jest udokumentowany w [operacji rejestrowane analizy magazynu i komunikaty o stanie](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages.md) i [Format dziennika analizy magazynu](/rest/api/storageservices/storage-analytics-log-format.md) tematów.

### <a name="how-logs-are-stored"></a>Jak są przechowywane dzienniki
Wszystkie dzienniki są przechowywane w blokowych obiektów blob w kontenerze o nazwie $logs, który jest tworzony automatycznie podczas analityka magazynu jest włączona dla konta magazynu. Kontener $logs znajduje się w przestrzeni nazw obiektów blob konta magazynu, na przykład: `http://<accountname>.blob.core.windows.net/$logs`. Ten kontener nie można usunąć po włączeniu usługa Storage Analytics, jednak można je usunąć jego zawartość.

> [!NOTE]
> Kontener $logs nie jest wyświetlany, gdy kontener listę operacji jest wykonywana, takich jak [ListContainers](https://msdn.microsoft.com/library/azure/dd179352.aspx) metody. Muszą być dostępne bezpośrednio. Na przykład, można użyć [ListBlobs](https://msdn.microsoft.com/library/azure/dd135734.aspx) metodę, aby uzyskać dostęp do obiektów blob w `$logs` kontenera.
> Jak żądania są rejestrowane, usługa Storage Analytics przekazać wyników pośrednich jako bloki. Okresowo usługa Storage Analytics zatwierdzić te bloki i udostępnić je jako obiekt blob.
>
>

Zduplikowane rekordy mogą istnieć dzienników utworzonych w tej samej godziny. Można określić, czy rekord jest duplikatem, sprawdzając **RequestId** i **operacji** numer.

### <a name="log-naming-conventions"></a>Zaloguj się konwencje nazewnictwa
Każdy dziennik zostanie zapisany w następującym formacie.

    <service-name>/YYYY/MM/DD/hhmm/<counter>.log

W poniższej tabeli opisano każdego atrybutu w parametrze nazwę dziennika.

| Atrybut | Opis |
| --- | --- |
| <service-name> |Nazwa usługi storage. Na przykład: obiektów blob, tabel lub kolejek. |
| RRRR |Czterocyfrowy rok dziennika. Na przykład: 2011. |
| MM |Dwucyfrowy miesiąc dziennika. Na przykład: 07. |
| DD |Dwucyfrowy miesiąc dziennika. Na przykład: 07. |
| hh |Godzina dwóch cyfr, który wskazuje początkowy godzinę dzienników, w 24-godzinnym formacie UTC. Na przykład: 18. |
| mm |Liczba dwóch cyfr, która określa początkowy minutę dzienników. Ta wartość nie jest obsługiwany w bieżącej wersji programu Storage Analytics, a jego wartość będzie zawsze 00. |
| <counter> |Liczony od zera licznik z sześć cyfr wskazującą liczbę obiektów blob dziennika wygenerowany dla usługi storage w ciągu godziny przedziale czasu. Ten licznik rozpoczyna się od 000000. Na przykład: 000001. |

Poniżej znajduje się pełny przykład nazwę dziennika, łączącą poprzednich przykładach.

    blob/2011/07/31/1800/000001.log

Oto przykładowy identyfikator URI, który można uzyskać dostępu do poprzedniego dziennika.

    https://<accountname>.blob.core.windows.net/$logs/blob/2011/07/31/1800/000001.log

Po zalogowaniu żądanie magazynu wynikowa nazwa dziennika skorelowany godzinę zakończenia żądanej operacji. Na przykład, jeśli żądanie GetBlob zostało zakończone o godzinie 6:30 na 7/31/2011 dziennik będzie zapisywany z prefiksem następujące: `blob/2011/07/31/1800/`

### <a name="log-metadata"></a>Metadane dziennika
Wszystkie obiekty BLOB dziennika są przechowywane przy użyciu metadanych, który może służyć do identyfikowania danych rejestrowania, jakie zawiera obiekt blob. W poniższej tabeli opisano każdego atrybutu metadanych.

| Atrybut | Opis |
| --- | --- |
| LogType |Opisuje, czy dziennik zawiera informacje dotyczące odczytu, zapisu lub operacje usuwania. Ta wartość może zawierać jeden typ lub kombinacji tych trzech miejsc, oddzielonych przecinkami. Przykład 1: zapisu; Przykład 2: Odczyt, zapis; Przykład 3: Odczyt, zapis, usuwanie. |
| Godzina rozpoczęcia |Najwcześniejsza godzina wpisu w dzienniku w postaci RRRR-MM-Ddtgg. Na przykład: 2011-07-31T18:21:46Z. |
| Godzina zakończenia |Ostatni czas zapisu w dzienniku w postaci RRRR-MM-Ddtgg. Na przykład: 2011-07-31T18:22:09Z. |
| LogVersion |Wersja format dziennika. Obecnie to jedyna obsługiwana wartość to 1.0. |

Poniższa lista zawiera pełny przykład metadanych przy użyciu poprzednich przykładach.

* LogType=write
* StartTime=2011-07-31T18:21:46Z
* EndTime=2011-07-31T18:22:09Z
* LogVersion=1.0

### <a name="accessing-logging-data"></a>Uzyskiwanie dostępu do danych rejestrowania
Wszystkie dane w `$logs` kontenera jest możliwy za pomocą interfejsów API usług obiektów Blob, w tym interfejsów API platformy .NET, udostępnianych przez platformę Azure zarządzanej biblioteki. Administrator konta magazynu można odczytywać i usunąć dzienniki, ale nie można utworzyć lub zaktualizować je. Zarówno metadane dziennika, jak i nazwę dziennika mogą służyć podczas wykonywania zapytania o przypadku dziennika. Istnieje możliwość, że dzienniki danej godziny będą widoczne poza kolejnością, ale metadane zawsze określa przedział czasu przyrostu wpisów dziennika w dzienniku. W związku z tym można użyć kombinacji nazwy dzienników i metadanych podczas wyszukiwania dziennika.

## <a name="about-storage-analytics-metrics"></a>O metrykach analityka magazynu
Usługa Storage Analytics może przechowywać metryki, które obejmują zagregowane transakcji statystyki oraz dane dyspozycyjności o żądaniach do usługi storage. Transakcje są zgłaszane zarówno na poziomie operacji interfejsu API, a także na poziomie usługi storage, a pojemność jest zgłaszany na poziomie usługi magazynu. Dane metryk można analizować użycie usługi magazynu, diagnozowanie problemów z żądania skierowanego do usługi storage i poprawić wydajność aplikacji, które używają usługi.

Aby korzystać z usługi Storage Analytics, należy włączyć je osobno dla każdej usługi, które chcesz monitorować. Można je włączyć z [witryny Azure Portal](https://portal.azure.com). Aby uzyskać więcej informacji, zobacz [monitorowanie konta magazynu w witrynie Azure Portal](storage-monitor-storage-account.md). Można również włączyć analizy programowo za pośrednictwem interfejsu API REST lub biblioteka klienta usługi Storage. Użyj **pobrać właściwości usługi** Procedura włączania analityka magazynu dla każdej usługi.

### <a name="transaction-metrics"></a>Metryki transakcji
Niezawodny zestaw danych jest rejestrowana w odstępach co godzinę i minutę dla każdej usługi storage i żądanej operacji interfejsu API, łącznie z ruchem przychodzącym/wychodzącym, dostępności, błędy i kategorii wartości procentowych żądania. Można wyświetlić pełną listę szczegółów transakcji w [schemat tabeli metryk usługi Analytics magazynu](https://msdn.microsoft.com/library/hh343264.aspx) tematu.

Dane transakcji są rejestrowane na dwóch poziomach — poziom usługi i poziom operacji interfejsu API. Na poziomie usługi statystyki podsumowujące wszystkie żądane operacje interfejsu API są zapisywane na jednostkę tabeli co godzinę nawet wtedy, gdy żadne żądania nie zostały wprowadzone do usługi. Na poziomie operacji interfejsu API statystyki są zapisywane tylko do jednostki, jeśli zażądano operacji w ciągu danej godziny.

Na przykład, jeśli wykonujesz **GetBlob** operacji w usłudze Blob Storage Analytics Metrics będzie rejestrować żądania i uwzględnić go w zagregowanych danych dla usługi Blob, jak również **GetBlob** Operacja. Jednakże jeśli nie **GetBlob** operacji jest wymagana w ciągu godziny, jednostki nie będą zapisywane do `$MetricsTransactionsBlob` dla tej operacji.

Metryki transakcji są rejestrowane dla żądań użytkowników i żądań wysyłanych przez analityka magazynu, sam. Na przykład żądania przez analityka magazynu do zapisywania dzienników i jednostek tablic są rejestrowane. Aby uzyskać więcej informacji na temat jak te żądania są rozliczane zobacz [analizy magazynu i rozliczeń oraz](https://msdn.microsoft.com/library/hh360997.aspx).

### <a name="capacity-metrics"></a>Metryki wydajności
> [!NOTE]
> Obecnie metryki pojemności są dostępne tylko dla usługi Blob.
>
>

Dane dotyczące pojemności są rejestrowane codziennie dla konta magazynu usługi Blob, a dwie jednostki z tabeli są zapisywane. Jednej jednostki zawiera dane statystyczne danych przez użytkownika, a drugi zawiera dane statystyczne dotyczące `$logs` kontenera obiektów blob używane przez analityka magazynu. `$MetricsCapacityBlob` Tabela zawiera następujące statystyki:

* **Pojemność**: Ilość miejsca używanego przez usługę obiektów Blob konta magazynu, w bajtach.
* **ContainerCount**: Liczba kontenerów obiektów blob w usłudze obiektów Blob konta magazynu.
* **ObjectCount**: Liczba zatwierdzone i niezatwierdzone blokowy lub stronicowy obiekty BLOB w usłudze obiektów Blob konta magazynu.

Aby uzyskać więcej informacji na temat metryki pojemności, zobacz [schemat tabeli metryk usługi Analytics magazynu](/rest/api/storageservices/storage-analytics-metrics-table-schema.md).

### <a name="how-metrics-are-stored"></a>Jak są przechowywane metryki
Wszystkie dane metryk dla wszystkich usług magazynu są przechowywane w trzech tabelach zarezerwowane dla usługi: jednej tabeli, aby uzyskać informacje o transakcji, jednej tabeli, aby uzyskać informacje o transakcji na minutę i informacje o pojemności z innej tabeli. Informacje o transakcji transakcji i minutowe składa się z danych żądań i odpowiedzi, i informacje o pojemności składa się z magazynu danych użycia. Metryki godzin, metryki minut i pojemności dla usługi obiektów Blob na koncie magazynu są dostępne w tabelach, które są nazwane zgodnie z opisem w poniższej tabeli.

| Poziom metryki | Nazwy tabel | Obsługiwane wersje |
| --- | --- | --- |
| Godzinowe metryki, Lokalizacja podstawowa |$MetricsTransactionsBlob <br/>$MetricsTransactionsTable <br/> $MetricsTransactionsQueue |Wersje przed 2013-08-15 tylko. Te nazwy są nadal obsługiwane, zaleca się przejście na poniższych tabel. |
| Godzinowe metryki, Lokalizacja podstawowa |$MetricsHourPrimaryTransactionsBlob <br/>$MetricsHourPrimaryTransactionsTable <br/>$MetricsHourPrimaryTransactionsQueue |Wszystkie wersje, łącznie z 2013-08-15. |
| Metryki minut lokalizacji głównej |$MetricsMinutePrimaryTransactionsBlob <br/>$MetricsMinutePrimaryTransactionsTable <br/>$MetricsMinutePrimaryTransactionsQueue |Wszystkie wersje, łącznie z 2013-08-15. |
| Godzinowe metryki, lokalizacja pomocnicza |$MetricsHourSecondaryTransactionsBlob <br/>$MetricsHourSecondaryTransactionsTable <br/>$MetricsHourSecondaryTransactionsQueue |Wszystkie wersje, łącznie z 2013-08-15. Musi być włączona replikacja geograficznie nadmiarowa geograficznie nadmiarowy. |
| Metryki minut dodatkowej lokalizacji |$MetricsMinuteSecondaryTransactionsBlob <br/>$MetricsMinuteSecondaryTransactionsTable <br/>$MetricsMinuteSecondaryTransactionsQueue |Wszystkie wersje, łącznie z 2013-08-15. Musi być włączona replikacja geograficznie nadmiarowa geograficznie nadmiarowy. |
| Pojemność (tylko w przypadku usługi obiektów Blob) |$MetricsCapacityBlob |Wszystkie wersje, łącznie z 2013-08-15. |

Te tabele są tworzone automatycznie, gdy analityka magazynu jest włączona dla konta magazynu. Są one używane za pośrednictwem obszaru nazw konta magazynu, na przykład: `https://<accountname>.table.core.windows.net/Tables("$MetricsTransactionsBlob")`

### <a name="accessing-metrics-data"></a>Uzyskiwanie dostępu do danych metryk
Wszystkie dane w tabelach metryk jest możliwy za pomocą interfejsów API usług tabeli, w tym interfejsów API platformy .NET, udostępnianych przez platformę Azure zarządzane biblioteki. Administrator konta magazynu można odczytu i usuwania jednostek tablic, ale nie można utworzyć lub zaktualizować je.

## <a name="billing-for-storage-analytics"></a>Rozliczenia dla analityka magazynu
Wszystkie dane metryk są zapisywane przez usługi na koncie magazynu. W rezultacie każdej operacji zapisu, wykonywane przez analityka magazynu jest płatne. Ponadto ilość miejsca używanego przez dane metryk są również jest płatne.

Następujące akcje wykonywane przez analityka magazynu są płatne:

* Utwórz obiekty BLOB do rejestrowania żądań.
* Żądań w celu utworzenia tabeli jednostki dla metryki.

Jeśli skonfigurowano zasady przechowywania danych nie są naliczane za transakcje usuwania gdy analityka magazynu usuwa stare dane rejestrowania i metryk. Jednak usunięcie transakcji od klienta są płatne. Aby uzyskać więcej informacji na temat zasad przechowywania, zobacz [ustawienie zasady przechowywania danych analizy magazynu](https://msdn.microsoft.com/library/azure/hh343263.aspx).

### <a name="understanding-billable-requests"></a>Opis płatnych żądań
Wszystkie żądania skierowane do kont usługi storage jest rozliczana lub niepodlegających rozliczaniu. Usługa Storage Analytics rejestruje każdego pojedynczego żądania do usługi, w tym komunikat o stanie, który wskazuje obsługi żądania. Podobnie usługa Storage Analytics przechowuje metryki dla usługi i operacje interfejsu API tej usługi, w tym wartości procentowych i liczbę niektórych komunikatów o stanie. Razem te funkcje mogą pomóc analizować płatnych żądań, ulepszenia w swojej aplikacji i diagnozowanie problemów z żądania do usługi. Aby uzyskać więcej informacji na temat rozliczeń, zobacz [opis rozliczeń platformy Azure Storage — przepustowość, transakcje i pojemność](https://blogs.msdn.com/b/windowsazurestorage/archive/2010/07/09/understanding-windows-azure-storage-billing-bandwidth-transactions-and-capacity.aspx).

Podczas przeglądania danych analizy magazynu, można użyć w tabelach w [operacji rejestrowane analizy magazynu i komunikaty o stanie](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages.md) tematu, aby ustalić, jakie żądania są płatne. Następnie możesz porównać swoje dzienniki i dane metryk do komunikatów o stanie, aby zobaczyć, jeśli naliczono dla określonego żądania. Umożliwia także tabele w poprzednim temacie, aby zbadać dostępność usługi magazynu lub indywidualnych operacji interfejsu API.

## <a name="next-steps"></a>Kolejne kroki
* [Monitorowanie konta magazynu w witrynie Azure Portal](storage-monitor-storage-account.md)
* [Rejestrowanie danych analizy magazynu](https://msdn.microsoft.com/library/hh343262.aspx)
* [Storage Analytics Metrics](https://msdn.microsoft.com/library/hh343258.aspx)
