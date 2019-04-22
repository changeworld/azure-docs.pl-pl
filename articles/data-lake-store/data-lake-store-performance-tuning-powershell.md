---
title: Wskazówki dotyczące przy użyciu programu Powershell przy użyciu usługi Azure Data Lake Storage Gen1 dostrajania wydajności | Dokumentacja firmy Microsoft
description: Wskazówki dotyczące sposobu zwiększenia wydajności podczas korzystania z programu Azure PowerShell z usługą Azure Data Lake Storage Gen1
services: data-lake-store
documentationcenter: ''
author: stewu
manager: mtillman
editor: cgronlun
ms.service: data-lake-store
ms.devlang: na
ms.topic: article
ms.date: 01/09/2018
ms.author: stewu
ms.openlocfilehash: 1c554b0eee844a632e6412b6f8a285c7a2573326
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/18/2019
ms.locfileid: "58885319"
---
# <a name="performance-tuning-guidance-for-using-powershell-with-azure-data-lake-storage-gen1"></a>Wskazówki dotyczące przy użyciu programu PowerShell przy użyciu usługi Azure Data Lake Storage Gen1 dostrajania wydajności

W tym artykule wymieniono właściwości, które mogą być dostosowane do uzyskać lepszą wydajność podczas pracy z usługi Azure Data Lake Storage Gen1 przy użyciu programu PowerShell:

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="performance-related-properties"></a>Właściwości związane z wydajnością

| Właściwość            | Domyślne | Opis |
|---------------------|---------|-------------|
| PerFileThreadCount  | 10      | Ten parametr umożliwia wybór liczby wątków równoległych na potrzeby przekazywania lub pobierania każdego pliku. Ta wartość liczbowa określa maksymalną wątków, które można przydzielić na plik, ale mniejszej liczby wątków może wystąpić w zależności od scenariusza (na przykład podczas przekazywania pliku 1 KB, uzyskujesz jeden wątek, nawet jeśli poprosisz o 20 wątków).  |
| ConcurrentFileCount | 10      | Ten parametr jest przeznaczony konkretnie na potrzeby przekazywania lub pobierania folderów. Określa liczbę współbieżnych plików, które można przekazać lub pobrać. Ta wartość liczbowa określa maksymalną liczbę współbieżnych plików, które można przekazać lub pobrać w tym samym czasie, ale może zostać mniej współbieżności, zależnie od scenariusza (na przykład, jeśli przekazujesz dwa pliki, otrzymasz dwa przekazywania współbieżnych plików nawet jeśli poprosisz 15). |

**Przykład**

To polecenie pobiera pliki z Data Lake Storage Gen1 dysk lokalny użytkownika przy użyciu 20 wątków na plik i 100 plików współbieżnych.

    Export-AzDataLakeStoreItem -AccountName <Data Lake Storage Gen1 account name> -PerFileThreadCount 20-ConcurrentFileCount 100 -Path /Powershell/100GB/ -Destination C:\Performance\ -Force -Recurse

## <a name="how-do-i-determine-the-value-for-these-properties"></a>Jak określić wartości tych właściwości?

Następne pytanie, może być to sposób określania, jaka wartość do zapewnienia właściwości związane z wydajnością. Oto kilka użytecznych wskazówek.

* **Krok 1. Określenia łącznej liczby wątków** — należy rozpocząć od obliczenia łącznej liczby wątków do użycia. Ogólną wytyczną należy używać 6 wątków na każdy rdzeń fizyczny.

        Total thread count = total physical cores * 6

    **Przykład**

    Przy założeniu, że polecenia programu PowerShell są uruchamiane z poziomu maszyny wirtualnej D14 o 16 rdzeniach

        Total thread count = 16 cores * 6 = 96 threads


* **Krok 2. Obliczanie wartości parametru PerFileThreadCount** -perfilethreadcount obliczamy na podstawie rozmiaru plików. W przypadku plików mniejszych niż 2,5 GB nie ma potrzeby zmiany tego parametru, ponieważ domyślna wartość wynosząca 10 jest wystarczająca. W przypadku plików większych niż 2,5 GB należy użyć 10 wątków jako podstawy dla pierwszych 2,5 GB i dodać 1 wątek na każde dodatkowe zwiększenie 256 MB rozmiaru pliku. Podczas kopiowania folderu zawierającego pliki o szerokim zakresie rozmiarów warto podzielić je na grupy złożone z plików o podobnym rozmiarze. Różne rozmiary plików mogą spowodować utratę optymalnej wydajności. Jeśli pogrupowanie plików o podobnym rozmiarze jest niemożliwe, wartość parametru PerFileThreadCount należy ustawić na podstawie największego rozmiaru pliku.

        PerFileThreadCount = 10 threads for the first 2.5 GB + 1 thread for each additional 256 MB increase in file size

    **Przykład**

    Przy założeniu, że masz 100 plików o rozmiarach od 1 GB do 10 GB, 10 GB jako największego rozmiaru pliku dla używamy równania, w równaniu w taki sposób, jak pokazano poniżej.

        PerFileThreadCount = 10 + ((10 GB - 2.5 GB) / 256 MB) = 40 threads

* **Krok 3. Obliczanie wartości parametru ConcurrentFilecount** — użyć łącznej liczby wątków i wartości parametru PerFileThreadCount na obliczanie wartości parametru ConcurrentFileCount oparte na poniższego równania:

        Total thread count = PerFileThreadCount * ConcurrentFileCount

    **Przykład**

    Na podstawie użytych przez nas przykładowych wartości

        96 = 40 * ConcurrentFileCount

    Wartość parametru **ConcurrentFileCount** wynosi zatem **2,4**, co możemy zaokrąglić do **2**.

## <a name="further-tuning"></a>Dalsze dostosowywanie

Ze względu na zakres rozmiarów plików, z jakimi można pracować, może być konieczne dalsze dostosowanie. Obliczanie poprzedniego działa dobrze, jeśli wszystkie lub większość plików są większe i bliżej zakres 10 GB. Jeśli natomiast istnieje wiele różnych rozmiarów plików, z czego wiele plików jest mniejszych, można zmniejszyć wartość parametru PerFileThreadCount. Dzięki zmniejszeniu wartości parametru PerFileThreadCount można zwiększyć wartość parametru ConcurrentFileCount. Tak przy założeniu, że większość naszych plików jest mniejszych z zakresu od 5 GB, możemy ponownie nasze obliczenie:

    PerFileThreadCount = 10 + ((5 GB - 2.5 GB) / 256 MB) = 20

Dlatego **ConcurrentFileCount** staje się 96/20, czyli 4,8 zaokrąglone do **4**.

Te ustawienia można jeszcze bardziej dostosować, zwiększając lub zmniejszając wartość parametru **PerFileThreadCount** zależnie od rozkładu rozmiarów plików.

### <a name="limitation"></a>Ograniczenia

* **Liczba plików jest mniejsza niż ConcurrentFileCount**: Jeśli liczba przekazywanych plików jest mniejszy niż **ConcurrentFileCount** obliczane, a następnie należy zmniejszyć **ConcurrentFileCount** być równa liczbie plików. Przy użyciu pozostałych wątków można zwiększyć wartość parametru **PerFileThreadCount**.

* **Zbyt wiele wątków**: Jeśli zwiększysz liczbę wątków zbyt dużo bez zwiększenia rozmiaru klastra, istnieje ryzyko obniżonej wydajności. Podczas przełączania kontekstu na procesorze mogą wystąpić problemy z rywalizacją o zasoby.

* **Niewystarczająca współbieżność**: Jeśli współbieżność nie jest wystarczająca, klaster może być za mały. Możesz zwiększyć liczbę węzłów w klastrze, co pozwala uzyskać większą współbieżność.

* **Błędy ograniczania przepływności**: Mogą pojawić się błędy ograniczania przepływności, gdy współbieżność jest zbyt duży. W przypadku błędów ograniczania przepływności należy albo zmniejszyć współbieżność, albo skontaktować się z nami.

## <a name="next-steps"></a>Kolejne kroki
* [Użyj usługi Azure Data Lake Storage Gen1 dla wymagających danych big Data](data-lake-store-data-scenarios.md) 
* [Zabezpieczanie danych w usłudze Data Lake Storage 1. generacji](data-lake-store-secure-data.md)
* [Za pomocą usług Azure Data Lake Analytics Data Lake Storage Gen1](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
* [Usługa Azure HDInsight za pomocą programu Data Lake Storage Gen1](data-lake-store-hdinsight-hadoop-use-portal.md)

