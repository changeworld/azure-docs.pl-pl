---
title: Wskazówki dotyczące korzystania z usługi Data Lake Store przy użyciu programu Powershell dostrajania wydajności | Dokumentacja firmy Microsoft
description: Porady na temat sposobu zwiększenia wydajności w przypadku korzystania z programu Azure PowerShell z usługą Data Lake Store
services: data-lake-store
documentationcenter: ''
author: stewu
manager: jhubbard
editor: cgronlun
ms.service: data-lake-store
ms.devlang: na
ms.topic: article
ms.date: 01/09/2018
ms.author: stewu
ms.openlocfilehash: 7b19972ed4a75ac899a4b78b28ab36ba305a5a64
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/16/2018
---
# <a name="performance-tuning-guidance-for-using-powershell-with-azure-data-lake-store"></a>Wskazówki dotyczące korzystania z usługi Azure Data Lake Store przy użyciu programu PowerShell dostrajania wydajności

W tym artykule wymieniono właściwości, które można przedstawić Aby uzyskać lepszą wydajność podczas pracy z usługą Data Lake Store za pomocą środowiska PowerShell:

## <a name="performance-related-properties"></a>Właściwości związane z wydajnością

| Właściwość            | Domyślne | Opis |
|---------------------|---------|-------------|
| PerFileThreadCount  | 10      | Ten parametr umożliwia wybór liczby wątków równoległych na potrzeby przekazywania lub pobierania każdego pliku. Liczba ta reprezentuje maksymalny wątków, które mogą być przydzielone dla każdego pliku, ale może zostać mniejszą liczbę wątków w zależności od danego scenariusza (na przykład podczas przekazywania pliku 1 KB, możesz uzyskać jeden wątek nawet wtedy, gdy poprosić o wątków 20).  |
| ConcurrentFileCount | 10      | Ten parametr jest przeznaczony konkretnie na potrzeby przekazywania lub pobierania folderów. Określa liczbę współbieżnych plików, które można przekazać lub pobrać. Liczba ta reprezentuje maksymalną liczbę równoczesnych plików, które można przekazać lub pobrana w tym samym czasie, ale może zostać mniej współbieżności, w zależności od danego scenariusza (na przykład można przekazać dwóch plików, otrzymasz dwa przekazywania plików równoczesnych nawet wtedy, gdy żądanie użytkownika 15). |

**Przykład**

To polecenie pobiera pliki z usługi Azure Data Lake Store na dysk lokalny użytkownika przy użyciu 20 wątków na plik i 100 plików współbieżnych.

    Export-AzureRmDataLakeStoreItem -AccountName <Data Lake Store account name> -PerFileThreadCount 20-ConcurrentFileCount 100 -Path /Powershell/100GB/ -Destination C:\Performance\ -Force -Recurse

## <a name="how-do-i-determine-the-value-for-these-properties"></a>Jak ustalić wartości tych właściwości?

Następne pytanie, które mogą mieć jest sposób określania, jaka wartość do zapewnienia właściwości związanych z wydajnością. Oto kilka użytecznych wskazówek.

* **Krok 1. Określanie łącznej liczby wątków** — należy rozpocząć od obliczenia łącznej liczby wątków do użycia. Generalnie należy użyć sześciu wątków dla każdego rdzeni fizycznych.

        Total thread count = total physical cores * 6

    **Przykład**

    Przy założeniu, że polecenia programu PowerShell są uruchamiane z poziomu maszyny wirtualnej D14 o 16 rdzeniach

        Total thread count = 16 cores * 6 = 96 threads


* **Krok 2. Obliczanie wartości parametru PerFileThreadCount** — wartość parametru PerFileThreadCount obliczamy na podstawie rozmiaru plików. W przypadku plików mniejsza niż 2,5 GB jest niepotrzebna zmienić tego parametru, ponieważ domyślnie 10 jest wystarczająca. W przypadku plików większych niż 2,5 GB powinien użyć 10 wątków jako podstawa dla pierwszego 2,5 GB i dodawać 1 wątków dla każdego dodatkowego zwiększenia 256 MB rozmiar pliku. Podczas kopiowania folderu zawierającego pliki o szerokim zakresie rozmiarów warto podzielić je na grupy złożone z plików o podobnym rozmiarze. Różne rozmiary plików mogą spowodować utratę optymalnej wydajności. Jeśli pogrupowanie plików o podobnym rozmiarze jest niemożliwe, wartość parametru PerFileThreadCount należy ustawić na podstawie największego rozmiaru pliku.

        PerFileThreadCount = 10 threads for the first 2.5 GB + 1 thread for each additional 256 MB increase in file size

    **Przykład**

    Zakładając, że masz 100 plików z zakresu od 1 GB do 10 GB, używamy 10 GB jako największy rozmiar pliku dla równanie, które będzie odczytywać podobne do następujących.

        PerFileThreadCount = 10 + ((10 GB - 2.5 GB) / 256 MB) = 40 threads

* **Krok 3: Oblicz ConcurrentFilecount** — Użyj liczby całkowitej wątku i PerFileThreadCount do obliczenia ConcurrentFileCount oparte na następujące równanie:

        Total thread count = PerFileThreadCount * ConcurrentFileCount

    **Przykład**

    Na podstawie użytych przez nas przykładowych wartości

        96 = 40 * ConcurrentFileCount

    Wartość parametru **ConcurrentFileCount** wynosi zatem **2,4**, co możemy zaokrąglić do **2**.

## <a name="further-tuning"></a>Dalsze dostosowywanie

Ze względu na zakres rozmiarów plików, z jakimi można pracować, może być konieczne dalsze dostosowanie. Obliczanie poprzedniego sprawdza się, gdy wszystkie lub większość plików są większe i bliżej zakresu 10 GB. Jeśli natomiast istnieje wiele różnych rozmiarów plików, z czego wiele plików jest mniejszych, można zmniejszyć wartość parametru PerFileThreadCount. Dzięki zmniejszeniu wartości parametru PerFileThreadCount można zwiększyć wartość parametru ConcurrentFileCount. Dlatego jeśli przyjęto założenie, że większość naszego plików są mniejsze w zakresie od 5 GB, możemy wykonaj ponownie naszych obliczenie:

    PerFileThreadCount = 10 + ((5 GB - 2.5 GB) / 256 MB) = 20

Tak **ConcurrentFileCount** staje się 96/20, czyli 4.8 zaokrąglony do **4**.

Te ustawienia można jeszcze bardziej dostosować, zwiększając lub zmniejszając wartość parametru **PerFileThreadCount** zależnie od rozkładu rozmiarów plików.

### <a name="limitation"></a>Ograniczenia

* **Liczba plików jest mniejsza niż wartość parametru ConcurrentFileCount**: jeśli liczba przekazywanych plików jest mniejsza niż obliczona wartość parametru **ConcurrentFileCount**, należy zmniejszyć wartość parametru **ConcurrentFileCount** tak, aby była równa liczbie plików. Przy użyciu pozostałych wątków można zwiększyć wartość parametru **PerFileThreadCount**.

* **Zbyt wiele wątków**: jeśli liczba wątków zostanie nadmiernie zwiększona bez zwiększenia rozmiaru klastra, istnieje ryzyko obniżonej wydajności. Podczas przełączania kontekstu na procesorze mogą wystąpić problemy z rywalizacją o zasoby.

* **Niewystarczająca współbieżność**: jeśli współbieżność nie jest wystarczająca, to klaster może być za mały. Można zwiększyć liczbę węzłów w klastrze, co daje więcej współbieżności.

* **Błędy ograniczania przepływności**: błędy ograniczania przepływności mogą wystąpić wówczas, gdy współbieżność jest zbyt wysoka. W przypadku błędów ograniczania przepływności należy albo zmniejszyć współbieżność, albo skontaktować się z nami.

## <a name="next-steps"></a>Kolejne kroki
* [Używanie usługi Azure Data Lake Store w zastosowaniach wymagających danych big data](data-lake-store-data-scenarios.md) 
* [Zabezpieczanie danych w usłudze Data Lake Store](data-lake-store-secure-data.md)
* [Korzystanie z usługi Azure Data Lake Analytics z usługą Data Lake Store](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
* [Korzystanie z usługi Azure HDInsight z usługą Data Lake Store](data-lake-store-hdinsight-hadoop-use-portal.md)

