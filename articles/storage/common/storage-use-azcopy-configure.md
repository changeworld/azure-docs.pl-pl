---
title: Konfigurowanie, optymalizowanie i rozwiązywanie problemów z usługą AzCopy za pomocą usługi Azure Storage | Dokumenty firmy Microsoft
description: Konfigurowanie, optymalizowanie i rozwiązywanie problemów z programem AzCopy.
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 04/10/2020
ms.author: normesta
ms.subservice: common
ms.reviewer: dineshm
ms.openlocfilehash: 87a335f44a31436de735395adbee9035493cbbd2
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/13/2020
ms.locfileid: "81263424"
---
# <a name="configure-optimize-and-troubleshoot-azcopy"></a>Konfigurowanie, optymalizowanie i rozwiązywanie problemów z programem AzCopy

AzCopy to narzędzie wiersza polecenia, za pomocą którego można kopiować obiekty BLOB lub pliki do lub z konta magazynu. Ten artykuł ułatwia wykonywanie zaawansowanych zadań konfiguracyjnych i pomaga rozwiązywać problemy, które mogą pojawić się podczas korzystania z programu AzCopy.

> [!NOTE]
> Jeśli szukasz zawartości ułatwiającej rozpoczęcie pracy z AzCopy, zapoznaj się z następującymi artykułami:
> - [Wprowadzenie do narzędzia AzCopy](storage-use-azcopy-v10.md)
> - [Przesyłanie danych za pomocą pamięci masowej AzCopy i obiektów blob](storage-use-azcopy-blobs.md)
> - [Przesyłanie danych za pomocą AzCopy i przechowywania plików](storage-use-azcopy-files.md)
> - [Przesyłanie danych za pomocą wiader AzCopy i Amazon S3](storage-use-azcopy-s3.md)

## <a name="configure-proxy-settings"></a>Konfigurowanie ustawień serwera proxy

Aby skonfigurować ustawienia serwera proxy dla AzCopy, należy ustawić zmienną środowiskową. `https_proxy` Jeśli uruchomisz AzCopy w systemie Windows, AzCopy automatycznie wykryje ustawienia serwera proxy, więc nie musisz używać tego ustawienia w systemie Windows. Jeśli zdecydujesz się użyć tego ustawienia w systemie Windows, zastąpi automatyczne wykrywanie.

| System operacyjny | Polecenie  |
|--------|-----------|
| **Windows** | W wierszu polecenia użyj:`set https_proxy=<proxy IP>:<proxy port>`<br> W programie PowerShell użyj:`$env:https_proxy="<proxy IP>:<proxy port>"`|
| **Linux** | `export https_proxy=<proxy IP>:<proxy port>` |
| **Macos** | `export https_proxy=<proxy IP>:<proxy port>` |

Obecnie AzCopy nie obsługuje serwerów proxy, które wymagają uwierzytelniania za pomocą ntlm lub protokołu Kerberos.

## <a name="optimize-performance"></a>Optymalizowanie wydajności

Można porównać wydajność, a następnie użyć poleceń i zmiennych środowiskowych, aby znaleźć optymalny kompromis między wydajnością a zużyciem zasobów.

Ta sekcja ułatwia wykonywanie następujących zadań optymalizacji:

> [!div class="checklist"]
> * Przeprowadzaj testy porównawcze
> * Optymalizacja przepływności
> * Optymalizacja wykorzystania pamięci 
> * Optymalizacja synchronizacji plików

### <a name="run-benchmark-tests"></a>Przeprowadzaj testy porównawcze

Można uruchomić test porównawczy wydajności na określonych kontenerów obiektów blob, aby wyświetlić ogólne statystyki wydajności i wąskich gardeł wydajności tożsamości. 

Użyj następującego polecenia, aby uruchomić test testu porównawczego wydajności.

|    |     |
|--------|-----------|
| **Składni** | `azcopy bench 'https://<storage-account-name>.blob.core.windows.net/<container-name>'` |
| **Przykład** | `azcopy bench 'https://mystorageaccount.blob.core.windows.net/mycontainer/myBlobDirectory?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D'` |

> [!TIP]
> W tym przykładzie załącza argumenty ścieżki pojedynczymi cudzysłowami (''). Użyj pojedynczych cudzysłowów we wszystkich powłokach poleceń z wyjątkiem powłoki poleceń systemu Windows (cmd.exe). Jeśli używasz powłoki poleceń systemu Windows (cmd.exe), należy ująć argumenty ścieżki z podwójnymi cudzysłowami ("") zamiast pojedynczych cudzysłowów (').

To polecenie uruchamia wskaźnik wydajności, przekazując dane testowe do określonego miejsca docelowego. Dane testowe są generowane w pamięci, przekazywane do miejsca docelowego, a następnie usuwane z miejsca docelowego po zakończeniu testu. Można określić, ile plików ma zostać wygenerowanych i jaki ma być rozmiar, korzystając z opcjonalnych parametrów polecenia.

Szczegółowe dokumenty referencyjne można znaleźć [na ławce azcopy](storage-ref-azcopy-bench.md).

Aby wyświetlić szczegółowe wskazówki `azcopy bench -h` dotyczące pomocy dla tego polecenia, wpisz, a następnie naciśnij klawisz ENTER.

### <a name="optimize-throughput"></a>Optymalizacja przepływności

Flagę `cap-mbps` w poleceniach można użyć, aby umieścić pułap szybkości transmisji danych. Na przykład następujące polecenie wznawia zadanie i `10` ogranicza przepływność do megabitów (MB) na sekundę. 

```azcopy
azcopy jobs resume <job-id> --cap-mbps 10
```

Przepływność może zmniejszyć się podczas przesyłania małych plików. Można zwiększyć przepływność, ustawiając `AZCOPY_CONCURRENCY_VALUE` zmienną środowiskową. Ta zmienna określa liczbę równoczesnych żądań, które mogą wystąpić.  

Jeśli komputer ma mniej niż 5 procesorów, wartość tej `32`zmiennej jest ustawiona na . W przeciwnym razie wartość domyślna jest równa 16 pomnożona przez liczbę procesorów. Maksymalna wartość domyślna `3000`tej zmiennej to , ale można ręcznie ustawić tę wartość wyższą lub niższą. 

| System operacyjny | Polecenie  |
|--------|-----------|
| **Windows** | `set AZCOPY_CONCURRENCY_VALUE=<value>` |
| **Linux** | `export AZCOPY_CONCURRENCY_VALUE=<value>` |
| **Macos** | `export AZCOPY_CONCURRENCY_VALUE=<value>` |

Służy `azcopy env` do sprawdzania bieżącej wartości tej zmiennej. Jeśli wartość jest pusta, można odczytać, która wartość jest używana, patrząc na początku dowolnego pliku dziennika AzCopy. Wybrana wartość i przyczyna, dla którego została wybrana, są tam zgłaszane.

Przed ustawieniem tej zmiennej zaleca się uruchomienie testu porównawczego. Proces testu porównawczego zgłosi zalecaną wartość współbieżności. Alternatywnie, jeśli warunki sieciowe i ładunki różnią się, ustaw tę zmienną na słowo `AUTO` zamiast na określoną liczbę. Spowoduje to, że AzCopy zawsze będzie działać ten sam proces automatycznego strojenia, który używa w testach porównawczych.

### <a name="optimize-memory-use"></a>Optymalizacja wykorzystania pamięci

Ustaw `AZCOPY_BUFFER_GB` zmienną środowiskową, aby określić maksymalną ilość pamięci systemowej, której program AzCopy ma być używany podczas pobierania i przekazywania plików.
Wyrazić tę wartość w gigabajtach (GB).

| System operacyjny | Polecenie  |
|--------|-----------|
| **Windows** | `set AZCOPY_BUFFER_GB=<value>` |
| **Linux** | `export AZCOPY_BUFFER_GB=<value>` |
| **Macos** | `export AZCOPY_BUFFER_GB=<value>` |

### <a name="optimize-file-synchronization"></a>Optymalizacja synchronizacji plików

Polecenie [synchronizacja](storage-ref-azcopy-sync.md) identyfikuje wszystkie pliki w miejscu docelowym, a następnie porównuje nazwy plików i ostatnio zmodyfikowane sygnatury czasowe przed rozpoczęciem operacji synchronizacji. Jeśli masz dużą liczbę plików, możesz zwiększyć wydajność, eliminując to przetwarzanie z góry. 

Aby to osiągnąć, użyj polecenia [kopia azcopy](storage-ref-azcopy-copy.md) `--overwrite` zamiast `ifSourceNewer`i ustawić flagę na . AzCopy będzie porównywać pliki, ponieważ są one kopiowane bez wykonywania jakichkolwiek skanów z góry i porównań. Zapewnia to przewagę wydajności w przypadkach, gdy istnieje duża liczba plików do porównania.

Polecenie [kopiowania azkopii](storage-ref-azcopy-copy.md) nie usuwa plików z miejsca docelowego, więc jeśli chcesz usunąć pliki w miejscu docelowym, gdy nie `--delete-destination` istnieją już u `true` `prompt`źródła, użyj polecenia [synchronizacji azcopy](storage-ref-azcopy-sync.md) z flagą ustawioną na wartość lub . . 

## <a name="troubleshoot-issues"></a>Rozwiązywanie problemów

AzCopy tworzy pliki dziennika i planowania dla każdego zadania. Dzienniki można używać do badania i rozwiązywania ewentualnych problemów. 

Dzienniki będą zawierać stan awarii`UPLOADFAILED` `COPYFAILED`( `DOWNLOADFAILED`, i ), pełną ścieżkę i przyczynę błędu.

Domyślnie pliki dziennika i planu znajdują `%USERPROFILE%\.azcopy` się w `$HOME$\.azcopy` katalogu w systemie Windows lub katalogu na komputerach Mac i Linux, ale w razie potrzeby można zmienić tę lokalizację.

Odpowiedni błąd nie musi być pierwszym błędem pojawia się w pliku. W przypadku błędów, takich jak błędy sieciowe, limity czasu i błędy zajęty serwer, AzCopy ponowi próbę do 20 razy i zwykle proces ponawiania zakończy się pomyślnie.  Pierwszy błąd, który widzisz może być coś nieszkodliwego, który został pomyślnie ponowiony.  Więc zamiast patrzeć na pierwszy błąd w pliku, poszukaj `UPLOADFAILED` `COPYFAILED`błędów, które są w pobliżu , lub `DOWNLOADFAILED`. 

> [!IMPORTANT]
> Podczas przesyłania żądania do pomocy technicznej firmy Microsoft (lub rozwiązywania problemu z udziałem jakiejkolwiek strony trzeciej) udostępnij zredagowanej wersji polecenia, które chcesz wykonać. Dzięki temu sygnatura dostępu Współdzielonego nie jest przypadkowo udostępniana nikomu. Zredagowanej wersji można znaleźć na początku pliku dziennika.

### <a name="review-the-logs-for-errors"></a>Przejrzyj dzienniki pod kątem błędów

Następujące polecenie spowoduje uzyskanie wszystkich `UPLOADFAILED` błędów `04dc9ca9-158f-7945-5933-564021086c79` o stanie z dziennika:

**Windows (PowerShell)**

```
Select-String UPLOADFAILED .\04dc9ca9-158f-7945-5933-564021086c79.log
```

**Linux**

```
grep UPLOADFAILED .\04dc9ca9-158f-7945-5933-564021086c79.log
```

### <a name="view-and-resume-jobs"></a>Wyświetlanie i wznawianie zadań

Każda operacja transferu utworzy zadanie AzCopy. Aby wyświetlić historię zadań, użyj następującego polecenia:

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

Użyj następującego polecenia, aby wznowić zadanie, które nie powiodło się/anulowało. To polecenie używa jego identyfikatora wraz z tokenem sygnatury dostępu Współdzielonego, ponieważ nie jest trwały ze względów bezpieczeństwa:

```
azcopy jobs resume <job-id> --source-sas="<sas-token>"
azcopy jobs resume <job-id> --destination-sas="<sas-token>"
```

> [!TIP]
> Ująć argumenty ścieżki, takie jak token sygnatury dostępu Współdzielonego, za pomocą pojedynczych cudzysłowów (''). Użyj pojedynczych cudzysłowów we wszystkich powłokach poleceń z wyjątkiem powłoki poleceń systemu Windows (cmd.exe). Jeśli używasz powłoki poleceń systemu Windows (cmd.exe), należy ująć argumenty ścieżki z podwójnymi cudzysłowami ("") zamiast pojedynczych cudzysłowów (').

Po wznowieniu zadania AzCopy przyjrzy się plikowi planu pracy. Plik planu zawiera listę wszystkich plików, które zostały zidentyfikowane do przetworzenia podczas pierwszego utworzenia zadania. Po wznowieniu zadania AzCopy spróbuje przenieść wszystkie pliki wymienione w pliku planu, które nie zostały jeszcze przeniesione.

## <a name="change-the-location-of-the-plan-and-log-files"></a>Zmienianie lokalizacji plików planu i dziennika

Domyślnie pliki planów i dzienników znajdują się w `%USERPROFILE%\.azcopy` `$HOME$\.azcopy` katalogu w systemie Windows lub w katalogu na komputerach Mac i Linux. Możesz zmienić tę lokalizację.

### <a name="change-the-location-of-plan-files"></a>Zmienianie lokalizacji plików planu

Użyj dowolnego z tych poleceń.

| System operacyjny | Polecenie  |
|--------|-----------|
| **Windows** | `set AZCOPY_JOB_PLAN_LOCATION=<value>` |
| **Linux** | `export AZCOPY_JOB_PLAN_LOCATION=<value>` |
| **Macos** | `export AZCOPY_JOB_PLAN_LOCATION=<value>` |

Służy `azcopy env` do sprawdzania bieżącej wartości tej zmiennej. Jeśli wartość jest pusta, zaplanuj pliki są zapisywane w lokalizacji domyślnej.

### <a name="change-the-location-of-log-files"></a>Zmienianie lokalizacji plików dziennika

Użyj dowolnego z tych poleceń.

| System operacyjny | Polecenie  |
|--------|-----------|
| **Windows** | `set AZCOPY_LOG_LOCATION=<value>` |
| **Linux** | `export AZCOPY_LOG_LOCATION=<value>` |
| **Macos** | `export AZCOPY_LOG_LOCATION=<value>` |

Służy `azcopy env` do sprawdzania bieżącej wartości tej zmiennej. Jeśli wartość jest pusta, dzienniki są zapisywane w lokalizacji domyślnej.

## <a name="change-the-default-log-level"></a>Zmienianie domyślnego poziomu dziennika

Domyślnie poziom dziennika AzCopy `INFO`jest ustawiony na . Jeśli chcesz zmniejszyć szczegółowość dziennika, aby zaoszczędzić miejsce na dysku, należy ``--log-level`` zastąpić to ustawienie za pomocą tej opcji. 

Dostępne poziomy dziennika `NONE` `DEBUG`to: , `PANIC`, `FATAL` `INFO`, `WARNING`, `ERROR`, , i .

## <a name="remove-plan-and-log-files"></a>Usuwanie plików planu i dziennika

Jeśli chcesz usunąć wszystkie pliki planu i dziennika z komputera lokalnego, aby zaoszczędzić miejsce na `azcopy jobs clean` dysku, użyj polecenia.

Aby usunąć pliki planu i dziennika skojarzone `azcopy jobs rm <job-id>`tylko z jednym zadaniem, użyj programu . Zastąp symbol zastępczy `<job-id>` w tym przykładzie identyfikatorem zadania.


