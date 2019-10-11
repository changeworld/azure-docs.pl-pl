---
title: Rozwiązywanie problemów z przepływami danych Azure Data Factory | Microsoft Docs
description: Dowiedz się, jak rozwiązywać problemy z przepływem danych w Azure Data Factory.
services: data-factory
author: kromerm
ms.service: data-factory
ms.topic: troubleshooting
ms.date: 10/08/2019
ms.author: makromer
ms.openlocfilehash: b5895b061426066d265d3ff68dc948014e641322
ms.sourcegitcommit: 824e3d971490b0272e06f2b8b3fe98bbf7bfcb7f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/10/2019
ms.locfileid: "72242266"
---
# <a name="troubleshoot-azure-data-factory-data-flows"></a>Rozwiązywanie problemów z przepływami danych Azure Data Factory

W tym artykule przedstawiono typowe metody rozwiązywania problemów z przepływami danych w Azure Data Factory.

## <a name="common-errors-and-messages"></a>Typowe błędy i komunikaty

### <a name="error-message-df-sys-01-shadeddatabricksorgapachehadoopfsazureazureexception-commicrosoftazurestoragestorageexception-the-specified-container-does-not-exist"></a>Komunikat o błędzie: DF-SYS-01: zacieniony. datakosteks. org. Apache. Hadoop. FS. Azure. Azureexception: com. Microsoft. Azure. Storage. Storageexception: określony kontener nie istnieje.

- **Objawy**: wykonywanie operacji podglądu danych, debugowania i przepływu danych potoku nie powiodło się, ponieważ kontener nie istnieje

- **Przyczyna**: gdy zestaw danych zawiera kontener, który nie istnieje w magazynie

- **Rozwiązanie**: Upewnij się, że kontener, do którego odwołuje się zestaw danych, istnieje

### <a name="error-message-df-sys-01-javalangassertionerror-assertion-failed-conflicting-directory-structures-detected-suspicious-paths"></a>Komunikat o błędzie: DF-SYS-01: Java. lang. AssertionError: potwierdzenie nie powiodło się: wykryto sprzeczne struktury katalogów. Podejrzane ścieżki

- **Objawy**: w przypadku używania symboli wieloznacznych w transformacji źródłowej z plikami Parquet

- **Przyczyna**: nieprawidłowa lub nieprawidłowa składnia symbolu wieloznacznego

- **Rozwiązanie**: Sprawdź składnię symboli wieloznacznych, które są używane w opcjach przekształcania źródłowego

### <a name="error-message-df-src-002-container-container-name-is-required"></a>Komunikat o błędzie: DF-SRC-002: "Container" (nazwa kontenera) jest wymagany

- **Objawy**: wykonywanie operacji podglądu danych, debugowania i przepływu danych potoku nie powiodło się, ponieważ kontener nie istnieje

- **Przyczyna**: gdy zestaw danych zawiera kontener, który nie istnieje w magazynie

- **Rozwiązanie**: Upewnij się, że kontener, do którego odwołuje się zestaw danych, istnieje

### <a name="error-message-df-uni-001-primarykeyvalue-has-incompatible-types-integertype-and-stringtype"></a>Komunikat o błędzie: DF-UNI-001: PrimaryKeyValue ma niezgodne typy integer i StringType

- **Objawy**: wykonywanie operacji podglądu danych, debugowania i przepływu danych potoku nie powiodło się, ponieważ kontener nie istnieje

- **Przyczyna**: występuje podczas próby wstawienia nieprawidłowego typu klucza podstawowego w ujściach baz danych

- **Rozwiązanie**: Użyj kolumny pochodnej do rzutowania kolumny, która jest używana jako klucz podstawowy w przepływie danych w celu dopasowania do typu danych docelowej bazy danych

### <a name="error-message-df-sys-01-commicrosoftsqlserverjdbcsqlserverexception-the-tcpip-connection-to-the-host-xxxxxdatabasewindowsnet-port-1433-has-failed-error-xxxxdatabasewindowsnet-verify-the-connection-properties-make-sure-that-an-instance-of-sql-server-is-running-on-the-host-and-accepting-tcpip-connections-at-the-port-make-sure-that-tcp-connections-to-the-port-are-not-blocked-by-a-firewall"></a>Komunikat o błędzie: DF-SYS-01: com. Microsoft. SqlServer. JDBC. sqlserverexception: połączenie TCP/IP hosta xxxxx.database.windows.net port 1433 nie powiodło się. Błąd: "xxxx.database.windows.net. Sprawdź właściwości połączenia. Upewnij się, że na hoście jest uruchomione wystąpienie SQL Server i zaakceptowanie połączeń TCP/IP na porcie. Upewnij się, że połączenia TCP z portem nie są blokowane przez zaporę ".

- **Objawy**: nie można wyświetlić podglądu danych ani wykonać potoku ze źródłem bazy danych lub obiektem sink

- **Przyczyna**: baza danych jest chroniona przez zaporę

- **Rozwiązanie**: otwieranie dostępu przez zaporę do bazy danych

### <a name="error-message-df-sys-01-commicrosoftsqlserverjdbcsqlserverexception-there-is-already-an-object-named-xxxxxx-in-the-database"></a>Komunikat o błędzie: DF-SYS-01: com. Microsoft. SqlServer. JDBC. sqlserverexception: w bazie danych istnieje już obiekt o nazwie "XXXXXX".

- **Objawy**: nie można utworzyć tabeli w zlewie

- **Przyczyna**: istnieje już istniejąca nazwa tabeli w docelowej bazie danych o tej samej nazwie zdefiniowanej w źródle lub w zestawie danych

- **Rozwiązanie**: Zmień nazwę tabeli, którą próbujesz utworzyć

## <a name="general-troubleshooting-guidance"></a>Ogólne wskazówki dotyczące rozwiązywania problemów

1. Sprawdź stan połączeń zestawu danych. W każdej transformacji źródłowej i ujścia należy odwiedzić połączoną usługę dla każdego zestawu danych, którego używasz, i przetestować połączenia.
2. Sprawdź stan połączeń plików i tabel z projektanta przepływów danych. Aby upewnić się, że masz dostęp do danych, przejdź na Debugowanie i kliknij pozycję Podgląd danych w obszarze przekształcenia źródłowe.
3. Jeśli wszystko wygląda dobrze z wersji zapoznawczej, przejdź do projektanta potoku i umieść przepływ danych w działaniu potoku. Debuguj potok na potrzeby kompleksowego testu.

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji dotyczących rozwiązywania problemów, wypróbuj następujące zasoby:

*  [Blog Data Factory](https://azure.microsoft.com/blog/tag/azure-data-factory/)
*  [Żądania funkcji Data Factory](https://feedback.azure.com/forums/270578-data-factory)
*  [Wideo na platformie Azure](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
*  [Forum MSDN](https://social.msdn.microsoft.com/Forums/home?sort=relevancedesc&brandIgnore=True&searchTerm=data+factory)
*  [Forum Stack Overflow dla Data Factory](https://stackoverflow.com/questions/tagged/azure-data-factory)
*  [Informacje o usłudze Twitter dotyczące Data Factory](https://twitter.com/hashtag/DataFactory)
*  [Przewodnik dotyczący wydajności przepływów danych dla mapowania ADF](concepts-data-flow-performance.md)
