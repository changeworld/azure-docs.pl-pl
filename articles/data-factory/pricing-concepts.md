---
title: Omówienie usługi Azure Data Factory ceny przykłady | Dokumentacja firmy Microsoft
description: W tym artykule opisano i przedstawiono usługi Azure Data Factory, model o szczegółowe przykłady cen
documentationcenter: ''
author: shlo
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 09/25/2018
ms.author: shlo
ms.openlocfilehash: 454899cd7cc592b87f96233d73ca8c4ed6ac333f
ms.sourcegitcommit: c53a800d6c2e5baad800c1247dce94bdbf2ad324
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/30/2019
ms.locfileid: "64935765"
---
# <a name="understanding-data-factory-pricing-through-examples"></a>Informacje o przykładach ceny usługi Data Factory

W tym artykule opisano i przedstawiono usługi Azure Data Factory, model o szczegółowe przykłady cen.

## <a name="copy-data-from-aws-s3-to-azure-blob-storage-hourly"></a>Kopiowanie danych z usługi AWS S3 do magazynu obiektów Blob platformy Azure co godzinę

W tym scenariuszu chcesz skopiować dane z usługi AWS S3 do usługi Azure Blob storage w przypadku harmonogramu co godzinę.

Do wykonywania scenariusz, należy utworzyć potok z następującymi elementami:

1. Działanie kopiowania, wejściowy zestaw danych dla danych, które mają być kopiowane z usługi AWS S3.

2. Wyjściowy zestaw danych dla danych w usłudze Azure Storage.

3. Wyzwalacz harmonogramu można wykonać potok co godzinę.

   ![Scenario1](media/pricing-concepts/scenario1.png)

| **Operacje** | **Typy i jednostki** |
| --- | --- |
| Tworzenie usługi połączonej | 2 podmiot odczyt/zapis  |
| Tworzenie zestawów danych | 4 jednostek odczytu/zapisu, (2 w celu utworzenia zestawu danych, 2 dla odwołania do połączonej usługi) |
| Tworzenie potoku | 3 jednostki odczytu/zapisu (1 w celu utworzenia potoku, 2 dla odwołań do zestawu danych) |
| Pobierz potoku | 1 jednostka odczytu/zapisu |
| Uruchamianie potoku | 2 uruchomień działań (1 dla wyzwalacza Uruchom 1 w przypadku uruchomienia działania) |
| Czas wykonywania kopii danych założeń: = 10 min | 10 \* 4 azure Integration Runtime (domyślne ustawienie DIU = 4) Aby uzyskać więcej informacji na temat jednostek integracji danych i optymalizowanie wydajności kopii, zobacz [w tym artykule](copy-activity-performance.md) |
| Monitorowanie potoku założenia: Tylko 1 uruchomić wystąpił | 2 rekordy monitorowanie Uruchom ponowione (1-uruchomienie potoku, 1 w przypadku uruchomienia działania) |

**Łączna liczba scenariusz kalkulacji cen: $0.16811**

- Operacje na danych fabryki = **0,0001 USD**
  - Odczyt/zapis = 10\*00001 0,0001 USD = [1 odczytu i zapisu = $ 0,50/50000 = 0,00001]
  - Monitorowanie = 2\*000005 = $0,00001 [monitorowania 1 = $ 0,25/50000 = 0.000005]
- Organizowanie potoku &amp; wykonywania = **0.168 $**
  - Uruchomienia działania = 001\*2 = 0,002 [1, uruchom = 1/1000 USD = 0,001]
  - Działania przenoszenia danych = $0.166 (Prorated przez 10 minut czasu wykonywania. 0,25 USD/godz. na platformie Azure Integration Runtime)

## <a name="copy-data-and-transform-with-azure-databricks-hourly"></a>Kopiowanie danych i transformacja dzięki usłudze Azure Databricks co godzinę

W tym scenariuszu, dla których chcesz skopiować dane z usługi AWS S3 do usługi Azure Blob storage i przekształcanie danych za pomocą usługi Azure Databricks w przypadku harmonogramu co godzinę.

Do wykonywania scenariusz, należy utworzyć potok z następującymi elementami:

1. Działanie kopiowania jeden wejściowy zestaw danych dla danych, które mają być kopiowane z usługi AWS S3 i wyjściowy zestaw danych dla danych w usłudze Azure storage.
2. Jedno działanie usługi Azure Databricks dla transformacji danych.
3. Wyzwalacz harmonogramu co do wykonania potok co godzinę.

![Scenario2](media/pricing-concepts/scenario2.png)

| **Operacje** | **Typy i jednostki** |
| --- | --- |
| Tworzenie usługi połączonej | 3 podmiot odczyt/zapis  |
| Tworzenie zestawów danych | 4 jednostek odczytu/zapisu, (2 w celu utworzenia zestawu danych, 2 dla odwołania do połączonej usługi) |
| Tworzenie potoku | 3 jednostki odczytu/zapisu (1 w celu utworzenia potoku, 2 dla odwołań do zestawu danych) |
| Pobierz potoku | 1 jednostka odczytu/zapisu |
| Uruchamianie potoku | Uruchomienia działania 3 (1 dla wyzwalacza, uruchom 2 w przypadku uruchomienia działania) |
| Czas wykonywania kopii danych założeń: = 10 min | 10 \* 4 azure Integration Runtime (domyślne ustawienie DIU = 4) Aby uzyskać więcej informacji na temat jednostek integracji danych i optymalizowanie wydajności kopii, zobacz [w tym artykule](copy-activity-performance.md) |
| Monitorowanie potoku założenia: Tylko 1 uruchomić wystąpił | 3 rekordy monitorowanie Uruchom ponowione (1-uruchomienie potoku, 2 w przypadku uruchomienia działania) |
| Wykonaj działania usługi Databricks założeń: czas wykonywania 10 min | Wykonania działania w potoku zewnętrzne 10 min |

**Łączna liczba scenariusz kalkulacji cen: $0.16916**

- Operacje na danych fabryki = **0.00012 $**
  - Odczyt/zapis = 11\*00001 = $0.00011 [1 odczytu i zapisu = $ 0,50/50000 = 0,00001]
  - Monitorowanie = 3\*000005 = $0,00001 [monitorowania 1 = $ 0,25/50000 = 0.000005]
- Organizowanie potoku &amp; wykonywania = **0.16904 $**
  - Uruchomienia działania = 001\*3 = 0,003 [1, uruchom = 1/1000 USD = 0,001]
  - Działania przenoszenia danych = $0.166 (Prorated przez 10 minut czasu wykonywania. 0,25 USD/godz. na platformie Azure Integration Runtime)
  - Działania potoku zewnętrznego = $0.000041 (Prorated przez 10 minut czasu wykonywania. $0.00025/godz. na platformie Azure Integration Runtime)

## <a name="copy-data-and-transform-with-dynamic-parameters-hourly"></a>Kopiowanie danych i przekształcać z parametrami dynamicznymi co godzinę

W tym scenariuszu chcesz skopiować dane z usługi AWS S3 do usługi Azure Blob storage i przekształcenia przy użyciu usługi Azure Databricks (przy użyciu parametrów dynamicznych w skrypcie) w przypadku harmonogramu co godzinę.

Do wykonywania scenariusz, należy utworzyć potok z następującymi elementami:

1. Jedno działanie kopiowania z wejściowego zestawu danych dla danych, które mają być kopiowane z usługi AWS S3, wyjściowy zestaw danych dla danych w usłudze Azure storage.
2. Jedno działanie wyszukiwania dla dynamicznie przekazywanie parametrów do skryptu przekształcania.
3. Jedno działanie usługi Azure Databricks dla transformacji danych.
4. Wyzwalacz harmonogramu co do wykonania potok co godzinę.

![Scenario3](media/pricing-concepts/scenario3.png)

| **Operacje** | **Typy i jednostki** |
| --- | --- |
| Tworzenie usługi połączonej | 3 podmiot odczyt/zapis  |
| Tworzenie zestawów danych | 4 jednostek odczytu/zapisu, (2 w celu utworzenia zestawu danych, 2 dla odwołania do połączonej usługi) |
| Tworzenie potoku | 3 jednostki odczytu/zapisu (1 w celu utworzenia potoku, 2 dla odwołań do zestawu danych) |
| Pobierz potoku | 1 jednostka odczytu/zapisu |
| Uruchamianie potoku | Uruchomienia działania 4 (1 dla wyzwalacza uruchomiony, 3 dla uruchomienia działania) |
| Czas wykonywania kopii danych założeń: = 10 min | 10 \* 4 azure Integration Runtime (domyślne ustawienie DIU = 4) Aby uzyskać więcej informacji na temat jednostek integracji danych i optymalizowanie wydajności kopii, zobacz [w tym artykule](copy-activity-performance.md) |
| Monitorowanie potoku założenia: Tylko 1 uruchomić wystąpił | 4 rekordów monitorowanie Uruchom ponowione (1-uruchomienie potoku, 3 dla uruchomienia działania) |
| Wykonaj działania Lookup założeń: czas wykonania = 1 min | 1 min wykonania działania potoku |
| Wykonaj działania usługi Databricks założeń: czas wykonywania 10 min | 10-minutowy materiał wykonania zewnętrznego działania potoku |

**Łączna liczba scenariusz kalkulacji cen: $0.17020**

- Operacje na danych fabryki = **0.00013 $**
  - Odczyt/zapis = 11\*00001 = $0.00011 [1 odczytu i zapisu = $ 0,50/50000 = 0,00001]
  - Monitorowanie = 4\*000005 = $0.00002 [monitorowania 1 = $ 0,25/50000 = 0.000005]
- Organizowanie potoku &amp; wykonywania = **0.17007 $**
  - Uruchomienia działania = 001\*4 = 0.004 [1, uruchom = 1/1000 USD = 0,001]
  - Działania przenoszenia danych = $0.166 (Prorated przez 10 minut czasu wykonywania. 0,25 USD/godz. na platformie Azure Integration Runtime)
  - Potok działania = $0.00003 (Prorated 1 minuty w czasie wykonywania. 0,002 USD za godzinę na platformie Azure Integration Runtime)
  - Działania potoku zewnętrznego = $0.000041 (Prorated przez 10 minut czasu wykonywania. $0.00025/godz. na platformie Azure Integration Runtime)

## <a name="using-mapping-data-flow-debug-for-a-normal-workday"></a>Za pomocą debugowania przepływu danych mapowania dla normalnych workday

Jako inżynier danych możesz odpowiadają za projektowanie, tworzenie i testowanie mapowanie przepływu danych każdego dnia. Zaloguj się do interfejsu użytkownika usługi ADF rano i włączyć tryb debugowania dla przepływu danych. Domyślny czas wygaśnięcia sesji debugowania jest 60 minut. Możesz pracować w ciągu dnia, przez 10 godzin, więc nigdy nie wygasa sesję debugowania. W związku z tym, Twoja opłata na ten dzień wyniesie:

**10 (godziny) x 8 (rdzenie) x $0.112 = 8.96 $**

## <a name="transform-data-in-blob-store-with-mapping-data-flows"></a>Przekształcanie danych w magazynie obiektów blob za pomocą mapowania przepływu danych

W tym scenariuszu chcesz przekształcić dane obiektów Blob Store wizualne w usłudze ADF mapowanie przepływu danych w przypadku harmonogramu co godzinę.

Do wykonywania scenariusz, należy utworzyć potok z następującymi elementami:

1. Przepływ danych działanie z logiką transformacji.

2. Wejściowy zestaw danych dla danych w usłudze Azure Storage.

3. Wyjściowy zestaw danych dla danych w usłudze Azure Storage.

4. Wyzwalacz harmonogramu można wykonać potok co godzinę.

| **Operacje** | **Typy i jednostki** |
| --- | --- |
| Tworzenie usługi połączonej | 2 podmiot odczyt/zapis  |
| Tworzenie zestawów danych | 4 jednostek odczytu/zapisu, (2 w celu utworzenia zestawu danych, 2 dla odwołania do połączonej usługi) |
| Tworzenie potoku | 3 jednostki odczytu/zapisu (1 w celu utworzenia potoku, 2 dla odwołań do zestawu danych) |
| Pobierz potoku | 1 jednostka odczytu/zapisu |
| Uruchamianie potoku | 2 uruchomień działań (1 dla wyzwalacza Uruchom 1 w przypadku uruchomienia działania) |
| Czas wykonywania założenia przepływu danych: = 10 min + 10-minutowy materiał czas wygaśnięcia | 10 \* 8 rdzeni obliczeniowych ogólne TTL 10 |
| Monitorowanie potoku założenia: Tylko 1 uruchomić wystąpił | 2 rekordy monitorowanie Uruchom ponowione (1-uruchomienie potoku, 1 w przypadku uruchomienia działania) |

**Łączna liczba scenariusz kalkulacji cen: $0.3011**

- Operacje na danych fabryki = **0,0001 USD**
  - Odczyt/zapis = 10\*00001 0,0001 USD = [1 odczytu i zapisu = $ 0,50/50000 = 0,00001]
  - Monitorowanie = 2\*000005 = $0,00001 [monitorowania 1 = $ 0,25/50000 = 0.000005]
- Organizowanie potoku &amp; wykonywania = **0.301 $**
  - Uruchomienia działania = 001\*2 = 0,002 [1, uruchom = 1/1000 USD = 0,001]
  - Działania przepływu danych = $0.299 Prorated przez 20 minut (10 minut czasu wykonywania + 10 minut. czas wygaśnięcia). obliczenia 0.112 $/ godz. na platformie Azure Integration Runtime z 8 rdzeni, ogólne

## <a name="next-steps"></a>Kolejne kroki

Teraz, gdy już rozumiesz cennika usługi Azure Data Factory, możesz rozpocząć pracę!

- [Tworzenie fabryki danych przy użyciu interfejsu użytkownika usługi Azure Data Factory](quickstart-create-data-factory-portal.md)

- [Wprowadzenie do usługi Azure Data Factory](introduction.md)

- [Wizualne Tworzenie usługi Azure Data Factory](author-visually.md)
