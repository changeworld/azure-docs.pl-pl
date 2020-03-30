---
title: Dostrajanie wydajności usługi Azure Data Lake Storage Gen1 — program PowerShell
description: Porady dotyczące zwiększania wydajności podczas korzystania z usługi Azure PowerShell z usługą Azure Data Lake Storage Gen1.
author: stewu
ms.service: data-lake-store
ms.topic: conceptual
ms.date: 01/09/2018
ms.author: stewu
ms.openlocfilehash: c975af1799d427651b76bb9fde5ff765afed3f86
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73904580"
---
# <a name="performance-tuning-guidance-for-using-powershell-with-azure-data-lake-storage-gen1"></a>Wskazówki dotyczące dostrajania wydajności dotyczące korzystania z programu PowerShell z usługą Azure Data Lake Storage Gen1

W tym artykule opisano właściwości, które można dostroić, aby uzyskać lepszą wydajność podczas korzystania z programu PowerShell do pracy z usługą Data Lake Storage Gen1.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="performance-related-properties"></a>Właściwości związane z wydajnością

| Właściwość            | Domyślne | Opis |
|---------------------|---------|-------------|
| PerFileThreadCount  | 10      | Ten parametr umożliwia wybór liczby wątków równoległych na potrzeby przekazywania lub pobierania każdego pliku. Liczba ta reprezentuje maksymalną liczbę wątków, które można przydzielić na plik, ale może uzyskać mniej wątków w zależności od scenariusza (na przykład, jeśli przekazujesz plik 1 KB, otrzymasz jeden wątek, nawet jeśli poprosisz o 20 wątków).  |
| ConcurrentFileCount | 10      | Ten parametr jest przeznaczony konkretnie na potrzeby przekazywania lub pobierania folderów. Określa liczbę współbieżnych plików, które można przekazać lub pobrać. Liczba ta reprezentuje maksymalną liczbę równoczesnych plików, które mogą być przesyłane lub pobierane w tym samym czasie, ale może uzyskać mniejszą współbieżność w zależności od scenariusza (na przykład, jeśli przesyłasz dwa pliki, otrzymasz dwa równoczesne pliki przesłane, nawet jeśli poprosisz 15). |

**Przykład:**

To polecenie pobiera pliki z usługi Data Lake Storage Gen1 na dysk lokalny użytkownika przy użyciu 20 wątków na plik i 100 równoczesnych plików.

```PowerShell
Export-AzDataLakeStoreItem -AccountName "Data Lake Storage Gen1 account name" `
    -PerFileThreadCount 20 `
    -ConcurrentFileCount 100 `
    -Path /Powershell/100GB `
    -Destination C:\Performance\ `
    -Force `
    -Recurse
```

## <a name="how-to-determine-property-values"></a>Jak określić wartości właściwości

Następne pytanie, które może mieć, to jak określić, jaką wartość należy podać dla właściwości związanych z wydajnością. Oto kilka użytecznych wskazówek.

* **Krok 1: Określ całkowitą liczbę wątków** — rozpocznij od obliczenia całkowitej liczby wątków do użycia. Jako ogólne wytyczne należy użyć sześciu wątków dla każdego rdzenia fizycznego.

    `Total thread count = total physical cores * 6`

    **Przykład:**

    Przy założeniu, że polecenia programu PowerShell są uruchamiane z poziomu maszyny wirtualnej D14 o 16 rdzeniach

    `Total thread count = 16 cores * 6 = 96 threads`

* **Krok 2: Oblicz PerFileThreadCount** - Obliczamy nasze PerFileThreadCount na podstawie rozmiaru plików. W przypadku plików mniejszych niż 2,5 GB nie ma potrzeby zmiany tego parametru, ponieważ domyślnie 10 jest wystarczająca. W przypadku plików większych niż 2,5 GB należy użyć 10 wątków jako podstawy dla pierwszych 2,5 GB i dodać 1 wątek dla każdego dodatkowego zwiększenia rozmiaru pliku o 256 MB. Podczas kopiowania folderu zawierającego pliki o szerokim zakresie rozmiarów warto podzielić je na grupy złożone z plików o podobnym rozmiarze. Różne rozmiary plików mogą spowodować utratę optymalnej wydajności. Jeśli pogrupowanie plików o podobnym rozmiarze jest niemożliwe, wartość parametru PerFileThreadCount należy ustawić na podstawie największego rozmiaru pliku.

    `PerFileThreadCount = 10 threads for the first 2.5 GB + 1 thread for each additional 256 MB increase in file size`

    **Przykład:**

    Zakładając, że masz 100 plików od 1 GB do 10 GB, używamy 10 GB jako największego rozmiaru pliku dla równania, które brzmiełoby jak poniżej.

    `PerFileThreadCount = 10 + ((10 GB - 2.5 GB) / 256 MB) = 40 threads`

* **Krok 3: Oblicz liczbę concurrentFilecount** — obliczanie sumy wątków i sumy PerFileThreadCount na podstawie następującego równania:

    `Total thread count = PerFileThreadCount * ConcurrentFileCount`

    **Przykład:**

    Na podstawie użytych przez nas przykładowych wartości

    `96 = 40 * ConcurrentFileCount`

    Wartość parametru **ConcurrentFileCount** wynosi zatem **2,4**, co możemy zaokrąglić do **2**.

## <a name="further-tuning"></a>Dalsze dostosowywanie

Ze względu na zakres rozmiarów plików, z jakimi można pracować, może być konieczne dalsze dostosowanie. Poprzednie obliczenia działają dobrze, jeśli wszystkie lub większość plików są większe i bliżej zakresu 10 GB. Jeśli natomiast istnieje wiele różnych rozmiarów plików, z czego wiele plików jest mniejszych, można zmniejszyć wartość parametru PerFileThreadCount. Dzięki zmniejszeniu wartości parametru PerFileThreadCount można zwiększyć wartość parametru ConcurrentFileCount. Tak więc, jeśli założymy, że większość naszych plików jest mniejsza w zakresie 5 GB, możemy przerobić nasze obliczenia:

`PerFileThreadCount = 10 + ((5 GB - 2.5 GB) / 256 MB) = 20`

Tak, **ConcurrentFileCount** staje się 96/20, który jest 4.8, zaokrąglone do **4**.

Te ustawienia można jeszcze bardziej dostosować, zwiększając lub zmniejszając wartość parametru **PerFileThreadCount** zależnie od rozkładu rozmiarów plików.

### <a name="limitation"></a>Ograniczenia

* **Liczba plików jest mniejsza niż wartość parametru ConcurrentFileCount**: jeśli liczba przekazywanych plików jest mniejsza niż obliczona wartość parametru **ConcurrentFileCount**, należy zmniejszyć wartość parametru **ConcurrentFileCount** tak, aby była równa liczbie plików. Przy użyciu pozostałych wątków można zwiększyć wartość parametru **PerFileThreadCount**.

* **Zbyt wiele wątków**: jeśli liczba wątków zostanie nadmiernie zwiększona bez zwiększenia rozmiaru klastra, istnieje ryzyko obniżonej wydajności. Podczas przełączania kontekstu na procesorze mogą wystąpić problemy z rywalizacją o zasoby.

* **Niewystarczająca współbieżność**: jeśli współbieżność nie jest wystarczająca, to klaster może być za mały. Można zwiększyć liczbę węzłów w klastrze, co daje większą współbieżność.

* **Błędy ograniczania przepływności**: błędy ograniczania przepływności mogą wystąpić wówczas, gdy współbieżność jest zbyt wysoka. W przypadku błędów ograniczania przepływności należy albo zmniejszyć współbieżność, albo skontaktować się z nami.

## <a name="next-steps"></a>Następne kroki

* [Korzystanie z usługi Azure Data Lake Storage Gen1 dla wymagań dotyczących dużych zbiorów danych](data-lake-store-data-scenarios.md) 
* [Zabezpieczanie danych w usłudze Data Lake Storage 1. generacji](data-lake-store-secure-data.md)
* [Korzystanie z usługi Azure Data Lake Analytics z usługą Data Lake Storage Gen1](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
* [Korzystanie z usługi Azure HDInsight z usługą Data Lake Storage Gen1](data-lake-store-hdinsight-hadoop-use-portal.md)

