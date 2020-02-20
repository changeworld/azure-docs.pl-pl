---
title: Rozwiązywanie problemów z przepływami danych
description: Dowiedz się, jak rozwiązywać problemy z przepływem danych w Azure Data Factory.
services: data-factory
ms.author: makromer
author: kromerm
manager: anandsub
ms.service: data-factory
ms.topic: troubleshooting
ms.date: 02/04/2020
ms.openlocfilehash: e2e1ddd031041f49107545cd0b3d3de4eaebcd6d
ms.sourcegitcommit: 64def2a06d4004343ec3396e7c600af6af5b12bb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/19/2020
ms.locfileid: "77472132"
---
# <a name="troubleshoot-data-flows-in-azure-data-factory"></a>Rozwiązywanie problemów z przepływami danych w Azure Data Factory

W tym artykule przedstawiono typowe metody rozwiązywania problemów z przepływami danych w Azure Data Factory.

## <a name="common-errors-and-messages"></a>Typowe błędy i komunikaty

### <a name="error-code-df-executor-sourceinvalidpayload"></a>Kod błędu: DF-wykonawca-SourceInvalidPayload
- **Komunikat**: wykonywanie operacji podglądu danych, debugowania i przepływu danych potoku nie powiodło się, ponieważ kontener nie istnieje
- **Przyczyny**: gdy zestaw danych zawiera kontener, który nie istnieje w magazynie
- **Zalecenie**: Upewnij się, że kontener, do którego odwołuje się zestaw danych, istnieje lub jest dostępny.

### <a name="error-code-df-executor-systemimplicitcartesian"></a>Kod błędu: DF-wykonawca-SystemImplicitCartesian

- **Komunikat**: niejawny produkt kartezjańskiego dla sprzężenia wewnętrznego nie jest obsługiwany, należy zamiast tego użyć SPRZĘŻENIa krzyżowego. Kolumny używane w sprzężeniu powinny tworzyć unikatowe klucze dla wierszy.
- **Przyczyny**: niejawny produkt kartezjańskiego dla sprzężenia wewnętrznego między planami logicznymi nie jest obsługiwany. Jeśli kolumny używane w sprzężeniu tworzą unikatowy klucz, wymagana jest co najmniej jedna kolumna z obu stron relacji.
- **Zalecenie**: w przypadku sprzężeń opartych na nierówności należy wybrać opcję niestandardowego SPRZĘŻENIa krzyżowego.

### <a name="error-code-df-executor-systeminvalidjson"></a>Kod błędu: DF-wykonawca-SystemInvalidJson

- **Message**: błąd analizy JSON, nieobsługiwane kodowanie lub wiele wierszy
- **Przyczyny**: możliwe problemy dotyczące pliku JSON: nieobsługiwane kodowanie, uszkodzone bajty lub źródło JSON jako pojedynczy dokument w wielu zagnieżdżonych wierszach
- **Zalecenie**: Sprawdź, czy kodowanie pliku JSON jest obsługiwane. Na transformacji źródłowej, która używa zestawu danych JSON, rozwiń węzeł Ustawienia JSON i Włącz opcję "pojedynczy dokument".
 
### <a name="error-code-df-executor-broadcasttimeout"></a>Kod błędu: DF-wykonawca-BroadcastTimeout

- **Komunikat**: błąd limitu czasu sprzężenia emisji, upewnij się, że strumień emisji produkuje dane w ciągu 60 sekund podczas przebiegów debugowania i 300 s w uruchomieniach zadania
- **Przyczyny**: emisja ma domyślny limit czasu wynoszący 60 s podczas przebiegów debugowania i 300 s w przebiegach zadania. Strumień wybierany do emisji jest duży, aby można było utworzyć dane w ramach tego limitu.
- **Zalecenie**: Unikaj rozgłaszania dużych strumieni danych, gdy przetwarzanie może trwać ponad 60 sekund. Wybierz mniejszy strumień do emisji. Duże tabele SQL/DW i pliki źródłowe są zwykle nieprawidłowymi kandydatami.

### <a name="error-code-df-executor-conversion"></a>Kod błędu: DF-wykonawca-Conversion

- **Komunikat**: konwertowanie na datę lub godzinę nie powiodło się z powodu nieprawidłowego znaku
- **Przyczyny**: dane mają nieoczekiwany format
- **Zalecenie**: Użyj poprawnego typu danych

### <a name="error-code-df-executor-invalidcolumn"></a>Kod błędu: DF-wykonawca-InvalidColumn

- **Komunikat**: Nazwa kolumny musi być określona w zapytaniu, Ustawianie aliasu, jeśli używana jest funkcja SQL
- **Przyczyny**: nie określono nazwy kolumny
- **Zalecenie**: Ustaw alias, jeśli używana jest funkcja SQL, taka jak min ()/Max () itp.

## <a name="general-troubleshooting-guidance"></a>Ogólne wskazówki dotyczące rozwiązywania problemów

1. Sprawdź stan połączeń zestawu danych. W każdej transformacji źródłowej i ujścia należy odwiedzić połączoną usługę dla każdego zestawu danych, którego używasz, i przetestować połączenia.
1. Sprawdź stan połączeń plików i tabel z projektanta przepływów danych. Aby upewnić się, że masz dostęp do danych, przejdź na Debugowanie i kliknij pozycję Podgląd danych w obszarze przekształcenia źródłowe.
1. Jeśli wszystko wygląda dobrze z wersji zapoznawczej, przejdź do projektanta potoku i umieść przepływ danych w działaniu potoku. Debuguj potok na potrzeby kompleksowego testu.

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji dotyczących rozwiązywania problemów, wypróbuj następujące zasoby:
*  [Blog Data Factory](https://azure.microsoft.com/blog/tag/azure-data-factory/)
*  [Żądania funkcji Data Factory](https://feedback.azure.com/forums/270578-data-factory)
*  [Wideo na platformie Azure](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
*  [Forum MSDN](https://social.msdn.microsoft.com/Forums/home?sort=relevancedesc&brandIgnore=True&searchTerm=data+factory)
*  [Forum Stack Overflow dla Data Factory](https://stackoverflow.com/questions/tagged/azure-data-factory)
*  [Informacje o usłudze Twitter dotyczące Data Factory](https://twitter.com/hashtag/DataFactory)
*  [Przewodnik dotyczący wydajności przepływów danych dla mapowania ADF](concepts-data-flow-performance.md)
