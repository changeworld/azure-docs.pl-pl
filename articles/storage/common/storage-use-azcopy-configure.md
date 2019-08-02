---
title: Konfigurowanie, optymalizowanie i rozwiązywanie problemów z AzCopy za pomocą usługi Azure Storage | Microsoft Docs
description: Konfigurowanie, optymalizowanie i rozwiązywanie problemów z AzCopy.
services: storage
author: normesta
ms.service: storage
ms.topic: article
ms.date: 07/25/2019
ms.author: normesta
ms.subservice: common
ms.openlocfilehash: 3773f9a8464dc94436d6d2503b173d4674033ab1
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/26/2019
ms.locfileid: "68565036"
---
# <a name="configure-optimize-and-troubleshoot-azcopy"></a>Konfigurowanie, optymalizowanie i rozwiązywanie problemów z AzCopy

AzCopy to narzędzie wiersza polecenia, które służy do kopiowania obiektów blob lub plików do lub z konta magazynu. Ten artykuł ułatwia wykonywanie zaawansowanych zadań konfiguracyjnych i pomaga w rozwiązywaniu problemów, które mogą wystąpić podczas korzystania z AzCopy.

> [!NOTE]
> Jeśli szukasz zawartości ułatwiającej rozpoczęcie pracy z usługą AzCopy, zobacz dowolny z następujących artykułów:
> - [Wprowadzenie do AzCopy](storage-use-azcopy-v10.md)
> - [Transferowanie danych za pomocą AzCopy i magazynu obiektów BLOB](storage-use-azcopy-blobs.md)
> - [Transferowanie danych za pomocą AzCopy i magazynu plików](storage-use-azcopy-files.md)
> - [Transferowanie danych za pomocą zasobników AzCopy i Amazon S3](storage-use-azcopy-s3.md)

## <a name="configure-proxy-settings"></a>Konfigurowanie ustawień serwera proxy

Aby skonfigurować ustawienia serwera proxy dla AzCopy, należy ustawić `https_proxy` zmienną środowiskową. Jeśli uruchomisz program AzCopy w systemie Windows, AzCopy automatycznie wykryje ustawienia proxy, więc nie trzeba używać tego ustawienia w systemie Windows. Jeśli zdecydujesz się używać tego ustawienia w systemie Windows, spowoduje to przesłonięcie automatycznego wykrywania.

| System operacyjny | Polecenie  |
|--------|-----------|
| **Windows** | W wierszu polecenia Użyj:`set https_proxy=<proxy IP>:<proxy port>`<br> W programie PowerShell Użyj:`$env:https_proxy="<proxy IP>:<proxy port>"`|
| **Linux** | `export https_proxy=<proxy IP>:<proxy port>` |
| **MacOS** | `export https_proxy=<proxy IP>:<proxy port>` |

Obecnie AzCopy nie obsługuje serwerów proxy, które wymagają uwierzytelniania przy użyciu protokołu NTLM lub Kerberos.

## <a name="optimize-throughput"></a>Optymalizowanie przepływności

Możesz użyć `cap-mbps` flagi, aby umieścić górną granicę współczynnika danych przepływności. Na przykład następujące polecenie powoduje wypróbowanie przepływności do `10` megabitów (MB) na sekundę.

```azcopy
azcopy cap-mbps 10
```

Przepływność może ulec zmniejszeniu podczas przesyłania małych plików. Można zwiększyć przepływność przez ustawienie `AZCOPY_CONCURRENCY_VALUE` zmiennej środowiskowej. Ta zmienna określa liczbę równoczesnych żądań, które mogą wystąpić.  Jeśli komputer ma mniej niż 5 procesorów CPU, wartość tej zmiennej jest ustawiana na `32`. W przeciwnym razie wartość domyślna jest równa 16 pomnożona przez liczbę procesorów CPU. Maksymalna wartość domyślna tej zmiennej to `300`, ale można ręcznie ustawić tę wartość na wyższą lub niższą.

| System operacyjny | Polecenie  |
|--------|-----------|
| **Windows** | `set AZCOPY_CONCURRENCY_VALUE=<value>` |
| **Linux** | `export AZCOPY_CONCURRENCY_VALUE=<value>` |
| **MacOS** | `export AZCOPY_CONCURRENCY_VALUE=<value>` |

Użyj, `azcopy env` aby sprawdzić bieżącą wartość tej zmiennej.  Jeśli wartość jest pusta, `AZCOPY_CONCURRENCY_VALUE` zmienna jest ustawiona na `300`wartość domyślną.

## <a name="change-the-location-of-the-log-files"></a>Zmień lokalizację plików dziennika

Domyślnie pliki dziennika znajdują się w `%USERPROFILE\\.azcopy` katalogu w systemie Windows lub `$HOME\\.azcopy` w katalogu na komputerach Mac i Linux. Tę lokalizację można zmienić, jeśli zachodzi taka potrzeba przy użyciu tych poleceń.

| System operacyjny | Polecenie  |
|--------|-----------|
| **Windows** | `set AZCOPY_LOG_LOCATION=<value>` |
| **Linux** | `export AZCOPY_LOG_LOCATION=<value>` |
| **MacOS** | `export AZCOPY_LOG_LOCATION=<value>` |

Użyj, `azcopy env` aby sprawdzić bieżącą wartość tej zmiennej. Jeśli wartość jest pusta, dzienniki są zapisywane w domyślnej lokalizacji.

## <a name="change-the-default-log-level"></a>Zmień domyślny poziom dziennika

Domyślnie poziom dziennika AzCopy jest ustawiony na `INFO`. Jeśli chcesz zmniejszyć szczegółowość dziennika w celu zaoszczędzenia miejsca na dysku, Zastąp to ustawienie przy użyciu ``--log-level`` opcji. 

Dostępne poziomy dziennika to: `DEBUG`, `INFO`, `WARNING` `ERROR` `FATAL`, ,i.`PANIC`

## <a name="troubleshoot-issues"></a>Rozwiązywanie problemów

AzCopy tworzy dziennik i planowanie plików dla każdego zadania. Możesz użyć dzienników, aby zbadać i rozwiązać ewentualne potencjalne problemy. 

Dzienniki będą zawierać stan niepowodzeń (`UPLOADFAILED`, `COPYFAILED`, i `DOWNLOADFAILED`), pełną ścieżkę i przyczynę niepowodzenia.

Domyślnie pliki dziennika i planu znajdują się w `%USERPROFILE\\.azcopy` katalogu w systemie Windows lub `$HOME\\.azcopy` w katalogu na komputerach Mac i Linux.

> [!IMPORTANT]
> Podczas przesyłania żądania do pomoc techniczna firmy Microsoft (lub rozwiązywania problemu związanego z jakąkolwiek osobą trzecią) należy udostępnić redagowane wersję polecenia, które chcesz wykonać. Gwarantuje to, że SYGNATURa dostępu współdzielonego nie zostanie przypadkowo udostępniona z każdy. Wersję redagowane można znaleźć na początku pliku dziennika.

### <a name="review-the-logs-for-errors"></a>Przejrzyj dzienniki pod kątem błędów

Następujące polecenie spowoduje wyświetlenie wszystkich błędów o `UPLOADFAILED` stanie `04dc9ca9-158f-7945-5933-564021086c79` z dziennika:

**Windows (program PowerShell)**

```
Select-String UPLOADFAILED .\04dc9ca9-158f-7945-5933-564021086c79.log
```

**Linux**

```
grep UPLOADFAILED .\04dc9ca9-158f-7945-5933-564021086c79.log
```

### <a name="view-and-resume-jobs"></a>Wyświetlanie i wznawianie zadań

Każda operacja transferu spowoduje utworzenie zadania AzCopy. Aby wyświetlić historię zadań, użyj następującego polecenia:

```
azcopy jobs list
```

Aby wyświetlić statystyki zadań, użyj następującego polecenia:

```
azcopy jobs show <job-id>
```

Aby przefiltrować transfer według stanu, użyj następującego polecenia:

```
azcopy jobs show <job-id> --with-status=Failed
```

Użyj poniższego polecenia, aby wznowić zadanie zakończone niepowodzeniem lub anulowane. To polecenie używa jego identyfikatora wraz z tokenem SAS, ponieważ nie jest on trwały ze względów bezpieczeństwa:

```
azcopy jobs resume <job-id> --source-sas="<sas-token>"
azcopy jobs resume <job-id> --destination-sas="<sas-token>"
```

Po wznowieniu zadania AzCopy przegląda plik planu zadań. Plik planu zawiera listę wszystkich plików, które zostały zidentyfikowane do przetwarzania podczas pierwszego utworzenia zadania. Po wznowieniu zadania AzCopy podejmie próbę przetransferowania wszystkich plików wymienionych w pliku planu, który nie został jeszcze przeniesiony.