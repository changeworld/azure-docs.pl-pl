---
title: Konfigurowanie optymalizacji i rozwiązywanie problemów z narzędzia AzCopy z usługą Azure Storage | Dokumentacja firmy Microsoft
description: Konfigurowanie optymalizacji i rozwiązywanie problemów z narzędzia AzCopy.
services: storage
author: normesta
ms.service: storage
ms.topic: article
ms.date: 05/14/2019
ms.author: normesta
ms.subservice: common
ms.openlocfilehash: 1a67846889b43d582a7a7d477a33f0e2168fd760
ms.sourcegitcommit: 72f1d1210980d2f75e490f879521bc73d76a17e1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/14/2019
ms.locfileid: "67147870"
---
# <a name="configure-optimize-and-troubleshoot-azcopy"></a>Konfigurowanie optymalizacji i rozwiązywanie problemów z narzędzia AzCopy

AzCopy to narzędzie wiersza polecenia używanej do kopiowania obiektów blob lub plików, do lub z konta magazynu. Ten artykuł ułatwia wykonywanie zadań zaawansowanej konfiguracji i pomaga rozwiązywać problemy, które mogą wystąpić, jak używać narzędzia AzCopy.

> [!NOTE]
> Jeśli szukasz zawartości ułatwić Ci rozpoczęcie pracy za pomocą narzędzia AzCopy, zobacz dowolny z następujących artykułów:
> - [Rozpoczynanie pracy za pomocą narzędzia AzCopy](storage-use-azcopy-v10.md)
> - [Transferowanie danych za pomocą narzędzia AzCopy i blob storage](storage-use-azcopy-blobs.md)
> - [Transferowanie danych za pomocą narzędzia AzCopy oraz plików magazynu](storage-use-azcopy-files.md)
> - [Transferowanie danych za pomocą narzędzia AzCopy i Amazon S3 przedziałów](storage-use-azcopy-s3.md)

## <a name="configure-proxy-settings"></a>Skonfiguruj ustawienia serwera proxy

Aby skonfigurować ustawienia serwera proxy dla narzędzia AzCopy, ustaw `https_proxy` zmiennej środowiskowej.

| System operacyjny | Polecenie  |
|--------|-----------|
| **Windows** | Użycie wiersza polecenia: `set https_proxy=<proxy IP>:<proxy port>`<br> W użyciu środowiska PowerShell: `$env:https_proxy="<proxy IP>:<proxy port>"`|
| **Linux** | `export https_proxy=<proxy IP>:<proxy port>` |
| **MacOS** | `export https_proxy=<proxy IP>:<proxy port>` |

Narzędzie AzCopy nie obsługuje obecnie, serwery proxy, które wymagają uwierzytelniania przy użyciu protokołu NTLM lub Kerberos.

## <a name="optimize-throughput"></a>Optymalizowanie przepływności

Ustaw `AZCOPY_CONCURRENCY_VALUE` zmiennej środowiskowej, aby skonfigurować liczbę współbieżnych żądań, a także do kontrolowania użycia przepustowości wydajności i zasobów. Jeśli komputer ma mniej niż 5 procesorów CPU, a następnie wartość tej zmiennej jest równa `32`. W przeciwnym razie wartość domyślna jest równa 16 pomnożona przez liczbę procesorów CPU. Domyślna maksymalna wartość tej zmiennej jest `300`, ale można ręcznie ustawić tę wartość wyższą lub niższą.

| System operacyjny | Polecenie  |
|--------|-----------|
| **Windows** | `set AZCOPY_CONCURRENCY_VALUE=<value>` |
| **Linux** | `export AZCOPY_CONCURRENCY_VALUE=<value>` |
| **MacOS** | `export AZCOPY_CONCURRENCY_VALUE=<value>` |

Użyj `azcopy env` do sprawdzania bieżącej wartości tej zmiennej.  Jeśli wartość jest pusta, a następnie `AZCOPY_CONCURRENCY_VALUE` zmienna jest ustawiona wartość domyślna `300`.

## <a name="change-the-location-of-the-log-files"></a>Zmień lokalizację plików dziennika

Domyślnie pliki dziennika znajdują się w `%USERPROFILE\\.azcopy` katalogu na Windows lub w `$HOME\\.azcopy` katalogu na komputerach Mac i Linux. Jeśli potrzebujesz, za pomocą poniższych poleceń, można zmienić tę lokalizację.

| System operacyjny | Polecenie  |
|--------|-----------|
| **Windows** | `set AZCOPY_LOG_LOCATION=<value>` |
| **Linux** | `export AZCOPY_LOG_LOCATION=<value>` |
| **MacOS** | `export AZCOPY_LOG_LOCATION=<value>` |

Użyj `azcopy env` do sprawdzania bieżącej wartości tej zmiennej. Jeśli wartość jest pusta, dzienniki są zapisywane w domyślnej lokalizacji.

## <a name="change-the-default-log-level"></a>Zmień domyślny poziom rejestrowania

Domyślnie ustawiono poziom dziennika narzędzia AzCopy `INFO`. Jeśli chcesz zmniejszyć poziom szczegółowości dziennika, aby zaoszczędzić miejsce na dysku, należy zastąpić to ustawienie przy użyciu ``--log-level`` opcji. 

Poziomy rejestrowania dostępne są: `DEBUG`, `INFO`, `WARNING`, `ERROR`, `PANIC`, i `FATAL`.

## <a name="troubleshoot-issues"></a>Rozwiązywanie problemów

Narzędzie AzCopy utworzy pliki dziennika i plan dla każdego zadania. Dzienniki można użyć, aby zbadać i rozwiązać wszelkie potencjalne problemy. 

Dzienniki będą zawierać stan błędu (`UPLOADFAILED`, `COPYFAILED`, i `DOWNLOADFAILED`), pełną ścieżkę i przyczynę błędu.

Domyślnie pliki dziennika i plan znajdują się w `%USERPROFILE\\.azcopy` katalogu Windows lub `$HOME\\.azcopy` katalogu na komputerach Mac i Linux.

> [!IMPORTANT]
> Podczas przesyłania żądania do firmy Microsoft Support (lub tego problemu, obejmujące żadnym podmiotom trzecim), udostępnianie zostały zredagowane wersję polecenie, które chcesz wykonać. Gwarantuje to, że sygnatury dostępu Współdzielonego przypadkowo nie są udostępniane nikomu. Możesz znaleźć zostały zredagowane wersji na początku pliku dziennika.

### <a name="review-the-logs-for-errors"></a>Przejrzyj dzienniki błędów

Następujące polecenie umożliwia uzyskanie wszystkich błędów przy użyciu `UPLOADFAILED` stan `04dc9ca9-158f-7945-5933-564021086c79` dziennika:

**Windows**

```
Select-String UPLOADFAILED .\04dc9ca9-158f-7945-5933-564021086c79.log
```

**Linux**

```
grep UPLOADFAILED .\04dc9ca9-158f-7945-5933-564021086c79.log
```

### <a name="view-and-resume-jobs"></a>Wyświetlanie i wznawianie prac

Każda operacja przeniesienia utworzy zadanie narzędzia AzCopy. Aby wyświetlić historię zadań, użyj następującego polecenia:

```
azcopy jobs list
```

Aby wyświetlić statystyki zadania, użyj następującego polecenia:

```
azcopy jobs show <job-id>
```

Aby filtrować transfery według stanu, użyj następującego polecenia:

```
azcopy jobs show <job-id> --with-status=Failed
```

Użyj następującego polecenia, aby wznowić zadanie nie powiodło się/anulowane. To polecenie używa identyfikatora wraz z tokenu sygnatury dostępu Współdzielonego, ponieważ nie jest trwały ze względów bezpieczeństwa:

```
azcopy jobs resume <job-id> --source-sas="<sas-token>"
azcopy jobs resume <job-id> --destination-sas="<sas-token>"
```

Po wznowieniu zadania narzędzia AzCopy analizuje plik planu zadania. Plik plan zawiera listę wszystkich plików, które zostały zidentyfikowane dla przetwarzania w chwili utworzenia zadania. Po wznowieniu zadania narzędzia AzCopy spróbuje przenieść wszystkie pliki, które są wymienione w pliku planu, który już nie zostały przeniesione.