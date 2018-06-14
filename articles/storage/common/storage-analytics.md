---
title: Użyj analityka magazynu Azure do zbierania danych dzienników i metryki | Dokumentacja firmy Microsoft
description: Analityka magazynu umożliwia śledzenie danych metryki dla wszystkich usług magazynu i zbierania dzienników dla magazynu obiektów Blob, kolejki i tabeli.
services: storage
documentationcenter: ''
author: roygara
manager: jeconnoc
editor: tysonn
ms.assetid: 7894993b-ca42-4125-8f17-8f6dfe3dca76
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 03/03/2017
ms.author: rogarana
ms.openlocfilehash: edda01cbfe1b53d934f9f4a7bb01c645fa680873
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2018
ms.locfileid: "30243438"
---
# <a name="storage-analytics"></a>Analityka magazynu

Analityka usługi Azure Storage umożliwia rejestrowanie i dostarcza danych metrycznych dotyczących konta magazynu. Dane te mogą posłużyć do śledzenia żądań, analizy tendencji użycia oraz diagnozowania problemów z kontem magazynu.

Aby użyć analityka magazynu, należy włączyć ją osobno dla każdej usługi, które chcesz monitorować. Możesz je włączyć z poziomu [Azure Portal](https://portal.azure.com). Aby uzyskać więcej informacji, zobacz [monitorować konto magazynu w portalu Azure](storage-monitor-storage-account.md). Można również włączyć analityka magazynu programowo przy użyciu interfejsu API REST lub biblioteka klienta. Użyj [pobrać właściwości usługi Blob](https://msdn.microsoft.com/library/hh452239.aspx), [pobrać właściwości usługi kolejki](https://msdn.microsoft.com/library/hh452243.aspx), [pobrać właściwości usługi tabeli](https://msdn.microsoft.com/library/hh452238.aspx), i [pobrać właściwości usługi plików](https://msdn.microsoft.com/library/mt427369.aspx)Procedura włączania analityka magazynu dla każdej usługi.

Zagregowane dane są przechowywane w dobrze znany obiekt blob (w przypadku rejestrowania) i dobrze znane tabel (metryk), które mogą uzyskać dostęp za pomocą usługi obiektów Blob i usługa tabel interfejsów API.

Analityka magazynu ma limit 20 TB ilości przechowywanych danych, która jest niezależna od całkowitego limitu konta magazynu. Aby uzyskać więcej informacji dotyczących rozliczeń i zasad przechowywania danych, zobacz [analizy magazynu i rozliczeń](https://msdn.microsoft.com/library/hh360997.aspx). Aby uzyskać więcej informacji na temat limitów konta magazynu, zobacz [cele dotyczące wydajności i skalowalności magazynu Azure](storage-scalability-targets.md).

Aby uzyskać szczegółowy przewodnik przy użyciu analizy magazynu i innych narzędzi do identyfikacji, diagnozowanie i rozwiązywanie problemów związanych z usługą Azure Storage, zobacz [monitorowanie, diagnozowanie i rozwiązywanie problemów z usługi Magazyn Microsoft Azure](storage-monitoring-diagnosing-troubleshooting.md).

## <a name="about-storage-analytics-logging"></a>Temat rejestrowania analityka magazynu
Analityka magazynu rejestruje szczegółowe informacje na temat udane i nieudane żądania do usługi magazynu. Te informacje może służyć do monitorowania poszczególnych żądań i diagnozowanie problemów z usługą magazynu. Żądania są rejestrowane w sposób optymalny.

Wpisy dziennika są tworzone tylko w przypadku działania usługi magazynu. Na przykład jeśli konto magazynu ma działania, jego usługa Blob, ale nie w jej tabel lub kolejek usługi, zostanie utworzone tylko dzienniki odnoszących się do usługi Blob.

Rejestrowanie analityka magazynu jest niedostępna dla plików Azure.

### <a name="logging-authenticated-requests"></a>Rejestrowanie uwierzytelnić żądania
Rejestrowane są następujące typy żądań uwierzytelnionych:

* Liczba pomyślnych żądań.
* Nieudane żądania, w tym limitu czasu, ograniczania przepustowości sieci, autoryzacji i inne błędy.
* Żądania przy użyciu dostępu sygnatury dostępu Współdzielonego, włącznie z żądaniami nie powiodło się i powiodło się.
* Żądania do analizy danych.

Żądania wysyłane przez analityka magazynu, takich jak dziennika utworzeniu lub usunięciu, nie są rejestrowane. Pełną listę zarejestrowane dane są udokumentowane we [operacje rejestrowane analityka magazynu i komunikaty o stanie](https://msdn.microsoft.com/library/hh343260.aspx) i [Format dziennika analityka magazynu](https://msdn.microsoft.com/library/hh343259.aspx) tematów.

### <a name="logging-anonymous-requests"></a>Rejestrowanie żądań anonimowych
Rejestrowane są następujące typy anonimowe żądania:

* Liczba pomyślnych żądań.
* Błąd serwera.
* Błędy przekroczenia limitu czasu dla klienta i serwera.
* Nieudane żądania GET z kodem błędu 304 (nie jest modyfikowany).

Inne nieudanych żądań anonimowych nie są rejestrowane. Pełną listę zarejestrowane dane są udokumentowane we [operacje rejestrowane analityka magazynu i komunikaty o stanie](https://msdn.microsoft.com/library/hh343260.aspx) i [Format dziennika analityka magazynu](https://msdn.microsoft.com/library/hh343259.aspx) tematów.

### <a name="how-logs-are-stored"></a>Jak są przechowywane dzienniki
Wszystkie dzienniki są przechowywane w blokowych obiektów blob w kontenerze o nazwie $logs, który jest tworzony automatycznie podczas analityka magazynu jest włączona dla konta magazynu. Kontener $logs znajduje się w przestrzeni nazw konta magazynu obiektów blob, na przykład: `http://<accountname>.blob.core.windows.net/$logs`. Ten kontener nie można usunąć po włączeniu analityka magazynu, jeśli jego zawartość można usunąć.

> [!NOTE]
> Kontener $logs nie jest wyświetlane, gdy kontener wyświetlania operacja jest wykonywana, takich jak [ListContainers](https://msdn.microsoft.com/library/azure/dd179352.aspx) metody. Muszą być dostępne bezpośrednio. Na przykład można użyć [ListBlobs](https://msdn.microsoft.com/library/azure/dd135734.aspx) metodę dostępu do obiektów blob w `$logs` kontenera.
> Jak żądania są rejestrowane, analityka magazynu przekazać wyników pośrednich jako bloki. Okresowo analityka magazynu zatwierdzić te bloki i udostępnić je jako obiektu blob.
> 
> 

Dla dzienników utworzone w tę samą godzinę może istnieją zduplikowane rekordy. Można określić, czy rekord jest duplikatem sprawdzając **RequestId** i **operacji** numer.

### <a name="log-naming-conventions"></a>Zaloguj się konwencje nazewnictwa
Każdy dziennika zostaną zapisane w następującym formacie.

    <service-name>/YYYY/MM/DD/hhmm/<counter>.log

W poniższej tabeli opisano każdego atrybutu w parametrze nazwę dziennika.

| Atrybut | Opis |
| --- | --- |
| <service-name> |Nazwa usługi magazynu. Na przykład: obiektów blob, tabel lub kolejek. |
| RRRR |Czterocyfrowy rok dziennika. Na przykład: 2011. |
| MM |Miesiąc dwucyfrowe dziennika. Na przykład: 07. |
| DD |Miesiąc dwucyfrowe dziennika. Na przykład: 07. |
| hh |Godzina dwucyfrowe wskazuje godzinę rozpoczęcia dla dzienników, w formacie 24-godzinnym formacie UTC. Na przykład: 18. |
| mm |Numer dwucyfrowe wskazuje początkowy minutę dzienników. Ta wartość nie jest obsługiwana w bieżącej wersji analityka magazynu, a jego wartość zawsze będzie 00. |
| <counter> |Liczony od zera licznik z sześciu cyfr wskazującą liczbę obiektów blob dziennika wygenerowany dla usługi magazynu w okresie w ciągu godziny. Ten licznik rozpoczyna się od 000000. Na przykład: 000001. |

Poniżej znajduje się nazwa dziennika kompletnego przykładu, łączącą poprzednich przykładach.

    blob/2011/07/31/1800/000001.log

Poniżej przedstawiono przykładowy identyfikator URI, który może być używane do dostępu w poprzednim dzienniku.

    https://<accountname>.blob.core.windows.net/$logs/blob/2011/07/31/1800/000001.log

Podczas rejestrowania żądania magazynu wynikowa nazwa dziennika są powiązane z godzina zakończenia żądanej operacji. Na przykład, jeśli żądanie GetBlob zostało ukończone w 6:00. na 2011-7-31 będzie zapisany dziennik z prefiksem następujące: `blob/2011/07/31/1800/`

### <a name="log-metadata"></a>Metadane dziennika
Wszystkie obiekty BLOB dziennika są przechowywane metadane, który może służyć do identyfikowania co rejestrowanie zawiera obiektu blob. W poniższej tabeli opisano każdego atrybutu metadanych.

| Atrybut | Opis |
| --- | --- |
| LogType |Opisuje, czy dziennik zawiera informacje dotyczące odczytu, zapisu albo usuwania operacji. Ta wartość może zawierać jeden typ lub kombinację wszystkich trzech rozdzielonych przecinkami. Przykład 1: zapisu; Przykład 2: Odczyt, zapis; Przykład 3: Odczyt, zapis, Usuń. |
| Godzina rozpoczęcia |Najwcześniejsza godzina wpisu w dzienniku w postaci RRRR-MM-Ddtgg. Na przykład: 2011-07-31T18:21:46Z. |
| Godzina zakończenia |Najpóźniejsza godzina wpisu w dzienniku w postaci RRRR-MM-Ddtgg. Na przykład: 2011-07-31T18:22:09Z. |
| LogVersion |Wersja formatu dziennika. Obecnie jedyna obsługiwana wartość to 1.0. |

Poniższa lista zawiera kompletnego przykładu metadanych za pomocą w poprzednich przykładach.

* LogType=write
* StartTime=2011-07-31T18:21:46Z
* EndTime=2011-07-31T18:22:09Z
* LogVersion = 1.0

### <a name="accessing-logging-data"></a>Uzyskiwanie dostępu do danych rejestrowania
Wszystkie dane w `$logs` kontener jest możliwy za pomocą interfejsów API usługi obiektów Blob, łącznie z interfejsów API architektury .NET dostarczany przez platformę Azure biblioteki zarządzanej. Administratorem konta magazynu można odczytu i Usuń dzienniki, ale nie można utworzyć lub zaktualizować je. Metadane dziennika i nazwa dziennika można podczas badania dziennika. Istnieje możliwość, że dany godzina dzienniki będą widoczne poza kolejnością, ale metadane zawsze określa timespan wpisy dziennika w dzienniku. W związku z tym można użyć kombinacji nazwy dzienników i metadanych podczas wyszukiwania dla określonego dziennika.

## <a name="about-storage-analytics-metrics"></a>O metryki analityka magazynu
Analityka magazynu można przechowywać metryki, które obejmują statystyk i pojemności dane zagregowane transakcji dotyczące żądań do usługi magazynu. Transakcje są raportowane zarówno na poziomie operacji interfejsu API, a także na poziomie usługi magazynu, a pojemność jest zgłaszany na poziomie usługi magazynu. Dane metryk można analizować wykorzystanie usługi magazynu, diagnozowanie problemów z żądań wysyłanych z usługą magazynu i poprawić wydajność aplikacji, które używają usługi.

Aby użyć analityka magazynu, należy włączyć ją osobno dla każdej usługi, które chcesz monitorować. Możesz je włączyć z poziomu [Azure Portal](https://portal.azure.com). Aby uzyskać więcej informacji, zobacz [monitorować konto magazynu w portalu Azure](storage-monitor-storage-account.md). Można również włączyć analityka magazynu programowo przy użyciu interfejsu API REST lub biblioteka klienta. Użyj **pobrać właściwości usługi** Procedura włączania analityka magazynu dla każdej usługi.

### <a name="transaction-metrics"></a>Metryki transakcji
Niezawodny zestaw danych jest rejestrowany w odstępach czasu godzinowe i minutowe dla każdej usługi magazynu i żądanej operacji interfejsu API, takich jak wejście/wyjście, dostępności, błędów i skategoryzowane wartości procentowe żądania. Można wyświetlić pełną listę szczegółów transakcji w [schemat tabeli metryki analityka magazynu](https://msdn.microsoft.com/library/hh343264.aspx) tematu.

Dane transakcji są rejestrowane na dwa poziomy — poziomu usług i poziom interfejsu API operacji. Na poziomie usługi statystyki podsumowania wszystkie żądane operacje interfejsu API są zapisywane na jednostkę tabeli co godzinę, nawet jeśli nie wprowadzono żadnych żądań do usługi. Na poziomie operacji interfejsu API statystyki są zapisywane tylko do jednostki, jeśli zażądano operacji w ciągu tej godziny.

Na przykład, jeśli wykonywane **GetBlob** operacji w usłudze obiektów Blob, metryki analityka magazynu będzie żądania logowania i uwzględnić go w zagregowanych danych dla usługi obiektów Blob, jak również **GetBlob** Operacja. Jednak jeśli nie **GetBlob** operacji jest wymagane w ciągu godziny, jednostki nie zostaną zapisane do `$MetricsTransactionsBlob` dla tej operacji.

Metryki transakcji są rejestrowane dla żądań użytkowników i żądań wysyłanych przez analityka magazynu, samej siebie. Na przykład żądania przez analityka magazynu do zapisywania dzienników i jednostek tabeli są rejestrowane. Aby uzyskać więcej informacji o sposobie te żądania są rozliczane, zobacz [analizy magazynu i rozliczeń](https://msdn.microsoft.com/library/hh360997.aspx).

### <a name="capacity-metrics"></a>Metryki pojemności
> [!NOTE]
> Obecnie metryki pojemności są dostępne tylko dla usługi Blob. Metryki wydajności dla usługi tabel i kolejek usługi będą dostępne w przyszłych wersjach analityka magazynu.
> 
> 

Dane wydajności są rejestrowane codziennie dla konta magazynu obiektów Blob usługi, a dwie jednostki tabeli są zapisywane. Jedną jednostkę zawiera dane statystyczne dotyczące danych użytkowników, a drugi zawiera dane statystyczne dotyczące `$logs` kontenera obiektów blob używane przez analityka magazynu. `$MetricsCapacityBlob` Tabela zawiera następujące statystyki:

* **Pojemność**: ilości miejsca używanego przez usługę obiektu Blob na koncie magazynu, w bajtach.
* **ContainerCount**: liczba kontenerów obiektów blob w usłudze obiektów Blob na koncie magazynu.
* **ObjectCount**: liczba zatwierdzonej i niezatwierdzone bloku lub strona obiekty BLOB na konto magazynu obiektów Blob usługi.

Aby uzyskać więcej informacji na temat metryki pojemności, zobacz [schemat tabeli metryki analityka magazynu](https://msdn.microsoft.com/library/hh343264.aspx).

### <a name="how-metrics-are-stored"></a>Jak są przechowywane metryk
Wszystkie dane metryki dla każdej z usług magazynu są przechowywane w trzy tabele zastrzeżone dla tej usługi: jedną tabelę dla informacji o transakcji, jedną tabelę dla informacji o transakcji minute i pojemności informacji z innej tabeli. Transakcji i minutowe transakcji zawiera dane żądań i odpowiedzi, a informacje o pojemności składa się z magazynu danych użycia. Metryki godzina, minuta metryki i pojemności konta magazynu obiektów Blob usługi są dostępne w tabelach, które są nazywane zgodnie z opisem w poniższej tabeli.

| Metryki poziomu | Nazwy tabel | Obsługiwane wersje |
| --- | --- | --- |
| Metryki co godzinę, lokalizacji głównej |$MetricsTransactionsBlob <br/>$MetricsTransactionsTable <br/> $MetricsTransactionsQueue |Wersje przed 2013-08-15 tylko. Te nazwy nadal są obsługiwane, zaleca się przełączania przy użyciu tabel wymienionych poniżej. |
| Metryki co godzinę, lokalizacji głównej |$MetricsHourPrimaryTransactionsBlob <br/>$MetricsHourPrimaryTransactionsTable <br/>$MetricsHourPrimaryTransactionsQueue |Wszystkie wersje, w tym 2013-08-15. |
| Minuta metryki, lokalizacji głównej |$MetricsMinutePrimaryTransactionsBlob <br/>$MetricsMinutePrimaryTransactionsTable <br/>$MetricsMinutePrimaryTransactionsQueue |Wszystkie wersje, w tym 2013-08-15. |
| Metryki co godzinę, dodatkowej lokalizacji |$MetricsHourSecondaryTransactionsBlob <br/>$MetricsHourSecondaryTransactionsTable <br/>$MetricsHourSecondaryTransactionsQueue |Wszystkie wersje, w tym 2013-08-15. Musi być włączona replikacja geograficznie nadmiarowa dostęp do odczytu. |
| Minuta metryki, dodatkowej lokalizacji |$MetricsMinuteSecondaryTransactionsBlob <br/>$MetricsMinuteSecondaryTransactionsTable <br/>$MetricsMinuteSecondaryTransactionsQueue |Wszystkie wersje, w tym 2013-08-15. Musi być włączona replikacja geograficznie nadmiarowa dostęp do odczytu. |
| Wydajność (tylko usługa Blob) |$MetricsCapacityBlob |Wszystkie wersje, w tym 2013-08-15. |

Te tabele są tworzone automatycznie, gdy analityka magazynu jest włączona dla konta magazynu. Są one używane za pośrednictwem przestrzeni nazw konta magazynu, na przykład: `https://<accountname>.table.core.windows.net/Tables("$MetricsTransactionsBlob")`

### <a name="accessing-metrics-data"></a>Uzyskiwanie dostępu do danych metryk
Wszystkie dane w tabelach metryki jest możliwy za pomocą interfejsów API usługi tabel, łącznie z interfejsów API architektury .NET dostarczany przez platformę Azure biblioteki zarządzanej. Administratorem konta magazynu można odczytać i usuwania jednostek tabeli, ale nie można utworzyć lub zaktualizować je.

## <a name="billing-for-storage-analytics"></a>Rozliczeń analiz magazynu
Wszystkie dane metryki są zapisywane przez usługi konta magazynu. W związku z tym każdej operacji zapisu, wykonywane przez analityka magazynu jest rozliczeniowy. Ponadto ilości miejsca używanego przez dane metryk jest również rozliczeniowy.

Rozliczeniowy są następujące akcje wykonywane przez analityka magazynu:

* Żądań w celu utworzenia obiektów blob do rejestrowania. 
* Żądania utworzenia jednostek tabeli dla metryki.

Skonfigurowanie zasad przechowywania danych są nie naliczane dla transakcji delete podczas analizy magazynu usuwa stare dane rejestrowania i metryki. Usuń transakcji od klienta, są jednak rozliczeniowy. Aby uzyskać więcej informacji na temat zasad przechowywania, zobacz [ustawienie zasad przechowywania danych analizy magazynu](https://msdn.microsoft.com/library/azure/hh343263.aspx).

### <a name="understanding-billable-requests"></a>Opis rozliczeniowy żądań
Wszystkie żądania skierowane do konta usługi storage jest rozliczana lub niepodlegającego rozliczaniu. Magazynu analizy dzienników każdego poszczególnych żądania do usługi, w tym komunikat o stanie wskazujący obsługi żądania. Podobnie analityka magazynu przechowuje metryki dla usługi i operacje interfejsu API tej usługi, w tym wartości procentowe i liczba komunikatów stanu. Razem te funkcje ułatwiają analizowanie żądań rozliczeniowy, poprawiają w swojej aplikacji i diagnozowanie problemów z żądań do usługi. Aby uzyskać więcej informacji dotyczących rozliczeń, zobacz [opis Azure magazynu rozliczeń - przepustowość, transakcje i pojemności](http://blogs.msdn.com/b/windowsazurestorage/archive/2010/07/09/understanding-windows-azure-storage-billing-bandwidth-transactions-and-capacity.aspx).

Podczas przeglądania danych analizy magazynu, można użyć w tabelach w [operacje rejestrowane analityka magazynu i komunikaty o stanie](https://msdn.microsoft.com/library/azure/hh343260.aspx) tematu, aby ustalić, które żądania są rozliczeniowy. Następnie możesz porównać danych dzienników i metryki komunikatów o stanie aby zobaczyć, czy naliczono dla określonego żądania. Umożliwia także tabel w poprzedniej części tematu do badania dostępności usługi magazynu lub poszczególnych operacji interfejsu API.

## <a name="next-steps"></a>Kolejne kroki
### <a name="setting-up-storage-analytics"></a>Konfigurowanie magazynu analityka
* [Monitor konta magazynu w portalu Azure](storage-monitor-storage-account.md)
* [Włączanie i konfigurowanie analityka magazynu](https://msdn.microsoft.com/library/hh360996.aspx)

### <a name="storage-analytics-logging"></a>Rejestrowanie analityka magazynu
* [Temat rejestrowania analityka magazynu](https://msdn.microsoft.com/library/hh343262.aspx)
* [Format dziennika analityka magazynu](https://msdn.microsoft.com/library/hh343259.aspx)
* [Analityka magazynu rejestrowane operacje i komunikaty o stanie](https://msdn.microsoft.com/library/hh343260.aspx)

### <a name="storage-analytics-metrics"></a>Metryki analityka magazynu
* [O metryki analityka magazynu](https://msdn.microsoft.com/library/hh343258.aspx)
* [Schemat tabeli metryki analityka magazynu](https://msdn.microsoft.com/library/hh343264.aspx)
* [Analityka magazynu rejestrowane operacje i komunikaty o stanie](https://msdn.microsoft.com/library/hh343260.aspx)  

