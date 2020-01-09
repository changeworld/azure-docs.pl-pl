---
title: Konfigurowanie, optymalizowanie i rozwiązywanie problemów z AzCopy za pomocą usługi Azure Storage | Microsoft Docs
description: Konfigurowanie, optymalizowanie i rozwiązywanie problemów z AzCopy.
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 10/16/2019
ms.author: normesta
ms.subservice: common
ms.reviewer: dineshm
ms.openlocfilehash: 6a1dcd2d8734d7701dab6d913beb8af0ad4e35ab
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75371398"
---
# <a name="configure-optimize-and-troubleshoot-azcopy"></a>Konfigurowanie, optymalizowanie i rozwiązywanie problemów z AzCopy

AzCopy to narzędzie wiersza polecenia, które służy do kopiowania obiektów blob lub plików do lub z konta magazynu. Ten artykuł ułatwia wykonywanie zaawansowanych zadań konfiguracyjnych i pomaga w rozwiązywaniu problemów, które mogą wystąpić podczas korzystania z AzCopy.

> [!NOTE]
> Jeśli szukasz zawartości ułatwiającej rozpoczęcie pracy z usługą AzCopy, zobacz dowolny z następujących artykułów:
> - [Wprowadzenie do narzędzia AzCopy](storage-use-azcopy-v10.md)
> - [Transferowanie danych za pomocą AzCopy i magazynu obiektów BLOB](storage-use-azcopy-blobs.md)
> - [Transferowanie danych za pomocą AzCopy i magazynu plików](storage-use-azcopy-files.md)
> - [Transferowanie danych za pomocą zasobników AzCopy i Amazon S3](storage-use-azcopy-s3.md)

## <a name="configure-proxy-settings"></a>Konfigurowanie ustawień serwera proxy

Aby skonfigurować ustawienia serwera proxy dla AzCopy, należy ustawić zmienną środowiskową `https_proxy`. Jeśli uruchomisz program AzCopy w systemie Windows, AzCopy automatycznie wykryje ustawienia proxy, więc nie trzeba używać tego ustawienia w systemie Windows. Jeśli zdecydujesz się używać tego ustawienia w systemie Windows, spowoduje to przesłonięcie automatycznego wykrywania.

| System operacyjny | Polecenie  |
|--------|-----------|
| **Windows** | W wierszu polecenia Użyj: `set https_proxy=<proxy IP>:<proxy port>`<br> W programie PowerShell Użyj: `$env:https_proxy="<proxy IP>:<proxy port>"`|
| **Linux** | `export https_proxy=<proxy IP>:<proxy port>` |
| **MacOS** | `export https_proxy=<proxy IP>:<proxy port>` |

Obecnie AzCopy nie obsługuje serwerów proxy, które wymagają uwierzytelniania przy użyciu protokołu NTLM lub Kerberos.

## <a name="optimize-performance"></a>Optymalizowanie wydajności

Możesz przeprowadzić test wydajności, a następnie użyć poleceń i zmiennych środowiskowych, aby znaleźć optymalną kompromis między wydajnością i użyciem zasobów.

### <a name="run-benchmark-tests"></a>Uruchamianie testów porównawczych

Test testu porównawczego wydajności dla określonych kontenerów obiektów BLOB można uruchomić w celu wyświetlenia ogólnych statystyk wydajności i problemów z wąskimi gardłami wydajności. 

> [!NOTE]
> W bieżącej wersji ta funkcja jest dostępna tylko dla kontenerów Blob Storage.

Użyj poniższego polecenia, aby uruchomić test porównawczy wydajności.

|    |     |
|--------|-----------|
| **Składnia** | `azcopy bench 'https://<storage-account-name>.blob.core.windows.net/<container-name>'` |
| **Przykład** | `azcopy bench 'https://mystorageaccount.blob.core.windows.net/mycontainer/myBlobDirectory?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D'` |

> [!TIP]
> Ten przykład obejmuje argumenty ścieżki z pojedynczym cudzysłowem (' '). Używaj pojedynczych cudzysłowów we wszystkich powłokach poleceń z wyjątkiem powłoki poleceń systemu Windows (cmd. exe). Jeśli używasz powłoki poleceń systemu Windows (cmd. exe), ujmij argumenty ścieżki z podwójnymi cudzysłowami ("") zamiast pojedynczego cudzysłowu ("").

To polecenie uruchamia wzorzec wydajności przez przekazywanie danych testowych do określonego miejsca docelowego. Dane testowe są generowane w pamięci, przekazane do miejsca docelowego, a następnie usuwane z lokalizacji docelowej po zakończeniu testu. Można określić, ile plików ma być generowanych i jakie rozmiary mają być używane przez opcjonalne parametry polecenia.

Aby uzyskać szczegółowe dokumenty referencyjne, zobacz [AzCopy kanapie](storage-ref-azcopy-bench.md).

Aby wyświetlić szczegółowe wskazówki dotyczące pomocy dla tego polecenia, wpisz `azcopy bench -h` a następnie naciśnij klawisz ENTER.

### <a name="optimize-throughput"></a>Optymalizowanie przepływności

Możesz użyć flagi `cap-mbps` w poleceniach, aby umieścić górny limit szybkości danych przepływności. Na przykład następujące polecenie wznawia przepływność zadań i `10` megabitów (MB) na sekundę. 

```azcopy
azcopy jobs resume <job-id> --cap-mbps 10
```

Przepływność może ulec zmniejszeniu podczas przesyłania małych plików. Przepustowość można zwiększyć, ustawiając zmienną środowiskową `AZCOPY_CONCURRENCY_VALUE`. Ta zmienna określa liczbę równoczesnych żądań, które mogą wystąpić.  

Jeśli komputer ma mniej niż 5 procesorów CPU, wartość tej zmiennej jest ustawiana na `32`. W przeciwnym razie wartość domyślna jest równa 16 pomnożona przez liczbę procesorów CPU. Maksymalna wartość domyślna tej zmiennej to `3000`, ale można ręcznie ustawić tę wartość na wyższą lub niższą. 

| System operacyjny | Polecenie  |
|--------|-----------|
| **Windows** | `set AZCOPY_CONCURRENCY_VALUE=<value>` |
| **Linux** | `export AZCOPY_CONCURRENCY_VALUE=<value>` |
| **MacOS** | `export AZCOPY_CONCURRENCY_VALUE=<value>` |

Użyj `azcopy env`, aby sprawdzić bieżącą wartość tej zmiennej. Jeśli wartość jest pusta, można odczytać, która wartość jest używana, przeglądając początek dowolnego pliku dziennika AzCopy. W tym miejscu są raportowane wybrane wartości i powód, w którym została wybrana.

Przed ustawieniem tej zmiennej zalecamy uruchomienie testu porównawczego. Proces testu porównawczego zgłosi zalecaną wartość współbieżności. Alternatywnie, jeśli warunki i ładunki sieciowe różnią się, należy ustawić tę zmienną na słowo `AUTO` zamiast na określoną liczbę. Spowoduje to, że AzCopy będzie zawsze uruchamiać ten sam proces dostrajania automatycznego, którego używa w testach porównawczych.

### <a name="optimize-memory-use"></a>Optymalizuj użycie pamięci

Ustaw zmienną środowiskową `AZCOPY_BUFFER_GB`, aby określić maksymalną ilość pamięci systemowej, która ma być używana podczas pobierania i przekazywania plików.
Ta wartość jest wyrażana w gigabajtach (GB).

| System operacyjny | Polecenie  |
|--------|-----------|
| **Windows** | `set AZCOPY_BUFFER_GB=<value>` |
| **Linux** | `export AZCOPY_BUFFER_GB=<value>` |
| **MacOS** | `export AZCOPY_BUFFER_GB=<value>` |

## <a name="troubleshoot-issues"></a>Rozwiązywanie problemów

AzCopy tworzy dziennik i planowanie plików dla każdego zadania. Możesz użyć dzienników, aby zbadać i rozwiązać ewentualne potencjalne problemy. 

Dzienniki będą zawierać stan niepowodzeń (`UPLOADFAILED`, `COPYFAILED`i `DOWNLOADFAILED`), pełną ścieżkę i przyczynę niepowodzenia.

Domyślnie pliki dzienników i planów znajdują się w katalogu `%USERPROFILE%\.azcopy` w katalogu systemu Windows lub `$HOME$\.azcopy` na komputerach Mac i Linux, ale można je zmienić w razie potrzeby.

> [!IMPORTANT]
> Podczas przesyłania żądania do pomoc techniczna firmy Microsoft (lub rozwiązywania problemu związanego z jakąkolwiek osobą trzecią) należy udostępnić redagowane wersję polecenia, które chcesz wykonać. Gwarantuje to, że SYGNATURa dostępu współdzielonego nie zostanie przypadkowo udostępniona z każdy. Wersję redagowane można znaleźć na początku pliku dziennika.

### <a name="review-the-logs-for-errors"></a>Przejrzyj dzienniki pod kątem błędów

Następujące polecenie spowoduje wyświetlenie wszystkich błędów o stanie `UPLOADFAILED` w dzienniku `04dc9ca9-158f-7945-5933-564021086c79`:

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

> [!TIP]
> Argumenty ścieżki otaczającej, takie jak token SAS, z pojedynczym cudzysłowem (' '). Używaj pojedynczych cudzysłowów we wszystkich powłokach poleceń z wyjątkiem powłoki poleceń systemu Windows (cmd. exe). Jeśli używasz powłoki poleceń systemu Windows (cmd. exe), ujmij argumenty ścieżki z podwójnymi cudzysłowami ("") zamiast pojedynczego cudzysłowu ("").

Po wznowieniu zadania AzCopy przegląda plik planu zadań. Plik planu zawiera listę wszystkich plików, które zostały zidentyfikowane do przetwarzania podczas pierwszego utworzenia zadania. Po wznowieniu zadania AzCopy podejmie próbę przetransferowania wszystkich plików wymienionych w pliku planu, który nie został jeszcze przeniesiony.

## <a name="change-the-location-of-the-plan-and-log-files"></a>Zmień lokalizację planu i plików dziennika

Domyślnie pliki planu i dziennika znajdują się w katalogu `%USERPROFILE%\.azcopy` w systemie Windows lub w katalogu `$HOME$\.azcopy` na komputerach Mac i Linux. Można zmienić tę lokalizację.

### <a name="change-the-location-of-plan-files"></a>Zmień lokalizację plików planu

Użyj dowolnego z tych poleceń.

| System operacyjny | Polecenie  |
|--------|-----------|
| **Windows** | `set AZCOPY_JOB_PLAN_LOCATION=<value>` |
| **Linux** | `export AZCOPY_JOB_PLAN_LOCATION=<value>` |
| **MacOS** | `export AZCOPY_JOB_PLAN_LOCATION=<value>` |

Użyj `azcopy env`, aby sprawdzić bieżącą wartość tej zmiennej. Jeśli wartość jest pusta, Zaplanuj pliki są zapisywane w domyślnej lokalizacji.

### <a name="change-the-location-of-log-files"></a>Zmień lokalizację plików dziennika

Użyj dowolnego z tych poleceń.

| System operacyjny | Polecenie  |
|--------|-----------|
| **Windows** | `set AZCOPY_LOG_LOCATION=<value>` |
| **Linux** | `export AZCOPY_LOG_LOCATION=<value>` |
| **MacOS** | `export AZCOPY_LOG_LOCATION=<value>` |

Użyj `azcopy env`, aby sprawdzić bieżącą wartość tej zmiennej. Jeśli wartość jest pusta, dzienniki są zapisywane w domyślnej lokalizacji.

## <a name="change-the-default-log-level"></a>Zmień domyślny poziom dziennika

Domyślnie poziom dziennika AzCopy jest ustawiony na `INFO`. Jeśli chcesz zmniejszyć szczegółowość dziennika w celu zaoszczędzenia miejsca na dysku, Zastąp to ustawienie przy użyciu opcji ``--log-level``. 

Dostępne poziomy dzienników to: `NONE`, `DEBUG`, `INFO`, `WARNING`, `ERROR`, `PANIC`i `FATAL`.

## <a name="remove-plan-and-log-files"></a>Usuń pliki planu i dziennika

Jeśli chcesz usunąć wszystkie pliki planu i dziennika z komputera lokalnego, aby zaoszczędzić miejsce na dysku, użyj polecenia `azcopy jobs clean`.

Aby usunąć pliki planu i dziennika skojarzone tylko z jednym zadaniem, użyj `azcopy jobs rm <job-id>`. Zastąp symbol zastępczy `<job-id>` w tym przykładzie identyfikatorem zadania zadania.


