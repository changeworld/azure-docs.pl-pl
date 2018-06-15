---
title: Zarządzanie współbieżności i obciążenia w usłudze SQL Data Warehouse | Dokumentacja firmy Microsoft
description: Zrozumienie zarządzania współbieżności i obciążenia w usłudze Azure SQL Data Warehouse związane z opracowywaniem rozwiązań.
services: sql-data-warehouse
documentationcenter: NA
author: sqlmojo
manager: jhubbard
editor: ''
ms.assetid: ef170f39-ae24-4b04-af76-53bb4c4d16d3
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: performance
ms.date: 08/23/2017
ms.author: joeyong;barbkess;kavithaj
ms.openlocfilehash: eaf2d43286dbaa52ada1430fbb7ce1e37f41c0d4
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/16/2018
ms.locfileid: "29959525"
---
# <a name="concurrency-and-workload-management-in-sql-data-warehouse"></a>Zarządzanie współbieżności i obciążenia w usłudze SQL Data Warehouse
Zapewnienie przewidywalnej wydajności na dużą skalę, Magazyn danych SQL Microsoft Azure pomaga kontrolować poziomy współbieżności i alokacji zasobów pamięci i procesora CPU priorytetyzacji. W tym artykule przedstawiono podstawowe pojęcia współbieżności i obciążenia zarządzania informacjami o tym, jak obie funkcje zostały wdrożone i sposób ich kontroli w magazynie danych. Zarządzania obciążenie SQL Data Warehouse ma na celu ułatwienia obsługi środowisk przez wielu użytkowników. Nie jest przeznaczony dla wielu dzierżawców obciążeń.

## <a name="concurrency-limits"></a>Limity współbieżności
Usługa SQL Data Warehouse pozwala maksymalnie 1024 równoczesnych połączeń. Wszystkie połączenia 1024 może przesłać kwerendy jednocześnie. Jednak w celu zoptymalizowania przepływności, SQL Data Warehouse może kolejka kilka zapytań, aby upewnić się, że każda kwerenda otrzyma grant minimalnej pamięci. Usługi kolejkowania wiadomości występuje w czasie wykonywania zapytania. W zapytaniach kolejkowania po osiągnięciu limitu współbieżności, SQL Data Warehouse może zwiększyć przepływność całkowita przez zapewnienie, że aktywnych kwerend uzyskać dostęp do zasobów wyczerpaniu potrzebnej pamięci.  

Limity współbieżności są regulowane przez dwa pojęcia: *zapytania jednoczesne* i *współbieżności miejsc*. Dla zapytania do wykonania należy wykonać w ramach zarówno limit współbieżności zapytania i alokację miejsca współbieżności.

* Zapytania jednoczesne są zapytań wykonywanych w tym samym czasie. Magazyn danych SQL obsługuje do 32 zapytania jednoczesne o większych rozmiarach DWU.
* Współbieżność gniazda są przydzielane oparte na wartości DWU. Każdy 100 DWU zawiera 4 gniazda współbieżności. Na przykład DW100 przydziela 4 gniazda współbieżności i DW1000 przydziela 40. Każde zapytanie zużywa jeden lub więcej współbieżności gniazda, zależy od [klasy zasobów](#resource-classes) zapytania. Zapytań wykonywanych w klasie zasobu smallrc korzystać z jednego gniazda współbieżności. Zapytań wykonywanych w klasie zasobu wyższej zużywać więcej miejsc współbieżności.

W poniższej tabeli opisano limity dla zapytania jednoczesne i gniazd współbieżność w różnych rozmiarach DWU.

### <a name="concurrency-limits"></a>Limity współbieżności
| DWU | Maksymalna liczba równoczesnych zapytań | Współbieżność gniazd przydzielonych |
|:--- |:---:|:---:|
| DW100 |4 |4 |
| DW200 |8 |8 |
| DW300 |12 |12 |
| DW400 |16 |16 |
| DW500 |20 |20 |
| DW600 |24 |24 |
| DW1000 |32 |40 |
| DW1200 |32 |48 |
| DW1500 |32 |60 |
| DW2000 |32 |80 |
| DW3000 |32 |120 |
| DW6000 |32 |240 |

Po spełnieniu jednego z tych progów, nowego zapytania są umieszczone w kolejce i wykonywane na podstawie FIFO pierwszy w.  Kończy zapytania i liczba zapytań i gniazda spadnie poniżej limitów, są wydawane w kolejce zapytań. 

> [!NOTE]  
> *Wybierz* kwerendy wykonywanie wyłącznie na dynamiczne zarządzanie widoków (widoków DMV) lub widoków wykazu nie podlegają za pomocą dowolnej z limitami współbieżności. Można monitorować system niezależnie od liczby zapytań wykonywanych na nim.
> 
> 

## <a name="resource-classes"></a>Klasy zasobów
Klasy zasobów ułatwiają sterowania alokacją pamięci i cyklami procesora CPU na potrzeby zapytania. Dwa typy klas zasobów można przypisać do użytkownika w postaci ról bazy danych. Dwa typy klas zasobów są następujące:
1. Dynamiczne klasy zasobu (**smallrc, mediumrc, largerc, xlargerc**) przydzielić zmiennej ilość pamięci w zależności od bieżącej wartości DWU. Oznacza to, że skalowanie w górę większej wartości dwu, zapytań automatycznie pobrać więcej pamięci. 
2. Klasy statyczne zasobów (**staticrc10, staticrc20, staticrc30, staticrc40, staticrc50, staticrc60, staticrc70, staticrc80**) Przydziel tej samej ilość pamięci, niezależnie od bieżącej wartości DWU (pod warunkiem, że wartość DWU sam ma za mało pamięci). Oznacza to, że na większą liczbę jednostek dwu, można uruchomić zapytania więcej w każdej klasie zasobów jednocześnie.

Użytkownicy w **smallrc** i **staticrc10** podano mniejszą ilość pamięci i można korzystać z wyższej współbieżności. Z kolei użytkownicy przypisani do **xlargerc** lub **staticrc80** podano duże ilości pamięci, i w związku z tym mniej kwerendy można uruchamiać jednocześnie.

Domyślnie każdy użytkownik jest członkiem klasy zasobu małych **smallrc**. Procedura `sp_addrolemember` używany w celu zwiększenia klasy zasobów i `sp_droprolemember` jest używana w celu zmniejszenia klasy zasobów. Na przykład to polecenie zwiększy klasy zasobu loaduser do **largerc**:

```sql
EXEC sp_addrolemember 'largerc', 'loaduser'
```


### <a name="queries-that-do-not-honor-resource-classes"></a>Zapytania, które nie uznają klasy zasobu

Istnieje kilka typów zapytań, które nie korzystają z większą alokacji pamięci. System ignoruje ich alokacji klasy zasobów i zawsze należy zamiast tego uruchomić te zapytania w klasie zasobu mała. Jeśli te zapytania są zawsze uruchamiane w klasie małych zasobu, można uruchomić po współbieżności miejsca są wykorzystanie i nie wykorzystują one więcej miejsca niż jest to potrzebne. Zobacz [wyjątki klasy zasobu](#query-exceptions-to-concurrency-limits) Aby uzyskać więcej informacji.

## <a name="details-on-resource-class-assignment"></a>Szczegółowe informacje o przypisanie klasy zasobu


Kilka więcej informacji na temat klasy zasobów:

* *Instrukcja ALTER roli* są wymagane uprawnienia, aby zmienić klasy zasobów użytkownika.
* Mimo że można dodać użytkownika do co najmniej jednej klas wyższych zasobów, zasobów dynamicznej klasy mają pierwszeństwo przed klas statycznych zasobów. Oznacza to, że jeśli użytkownik jest przypisany do obu **mediumrc**(dynamiczny) i **staticrc80**(statyczny), **mediumrc** jest klasa zasobów jest przestrzegana.
 * Gdy użytkownik jest przypisany do więcej niż jednej klasy zasobu w typie klasy określonego zasobu (więcej niż jedną klasę zasobu dynamicznego lub więcej niż jednej klasy statyczne zasobów), jest uznawane najwyższej klasy zasobów. Oznacza to jeśli użytkownik jest przypisany do mediumrc i largerc, jest uznawane wyższej klasy zasobu (largerc). A jeśli użytkownik jest przypisany do obu **staticrc20** i **statirc80**, **staticrc80** jest go uznać.
* Nie można zmienić klasy zasobu systemu użytkownika administracyjnego.

Na przykład szczegółowe, zobacz [przykład klasy zasobów użytkownika Zmiana](#changing-user-resource-class-example).

## <a name="memory-allocation"></a>Alokacja pamięci
Brak zalet i wad do zwiększenia klasy zasobów użytkownika. Zwiększanie zasobów klasy dla użytkownika, udostępnia ich zapytania więcej pamięci, co może oznaczać, że szybsze wykonywanie zapytań.  Jednak wyższych klas zasobów również zmniejszyć liczbę równoczesnych zapytań, które można uruchomić. Jest to kompromis między przydzielanie dużych ilości pamięci do pojedynczego zapytania lub dopuszczeniem inne zapytania, których alokacji pamięci równoczesne uruchamianie może też być konieczne. Jeśli jeden użytkownik otrzymuje wysokiej alokacji pamięci dla zapytania, inni użytkownicy nie mają dostęp do tej samej pamięci, aby uruchomić kwerendę.

Poniższa tabela mapuje pamięć przydzielona do każdej dystrybucji przez klasę DWU i zasobów.

### <a name="memory-allocations-per-distribution-for-dynamic-resource-classes-mb"></a>Alokacje pamięci na dystrybucji dla klasy zasobu dynamicznego (MB)
| DWU | smallrc | mediumrc | largerc | xlargerc |
|:--- |:---:|:---:|:---:|:---:|
| DW100 |100 |100 |200 |400 |
| DW200 |100 |200 |400 |800 |
| DW300 |100 |200 |400 |800 |
| DW400 |100 |400 |800 |1,600 |
| DW500 |100 |400 |800 |1,600 |
| DW600 |100 |400 |800 |1,600 |
| DW1000 |100 |800 |1,600 |3,200 |
| DW1200 |100 |800 |1,600 |3,200 |
| DW1500 |100 |800 |1,600 |3,200 |
| DW2000 |100 |1,600 |3,200 |6,400 |
| DW3000 |100 |1,600 |3,200 |6,400 |
| DW6000 |100 |3,200 |6,400 |12,800 |

Poniższa tabela mapuje pamięć przydzielona do każdej dystrybucji DWU i klasy statyczne zasobów. Należy zauważyć, że wyższych klas zasobów ich pamięci zmniejszona uwzględnić globalne limity DWU.

### <a name="memory-allocations-per-distribution-for-static-resource-classes-mb"></a>Alokacje pamięci na dystrybucji dla klas statycznych zasobów (MB)
| DWU | staticrc10 | staticrc20 | staticrc30 | staticrc40 | staticrc50 | staticrc60 | staticrc70 | staticrc80 |
|:--- |:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|
| DW100 |100 |200 |400 |400 |400 |400 |400 |400 |
| DW200 |100 |200 |400 |800 |800 |800 |800 |800 |
| DW300 |100 |200 |400 |800 |800 |800 |800 |800 |
| DW400 |100 |200 |400 |800 |1,600 |1,600 |1,600 |1,600 |
| DW500 |100 |200 |400 |800 |1,600 |1,600 |1,600 |1,600 |
| DW600 |100 |200 |400 |800 |1,600 |1,600 |1,600 |1,600 |
| DW1000 |100 |200 |400 |800 |1,600 |3,200 |3,200 |3,200 |
| DW1200 |100 |200 |400 |800 |1,600 |3,200 |3,200 |3,200 |
| DW1500 |100 |200 |400 |800 |1,600 |3,200 |3,200 |3,200 |
| DW2000 |100 |200 |400 |800 |1,600 |3,200 |6,400 |6,400 |
| DW3000 |100 |200 |400 |800 |1,600 |3,200 |6,400 |6,400 |
| DW6000 |100 |200 |400 |800 |1,600 |3,200 |6,400 |12,800 |

Z powyższej tabeli można stwierdzić, że zapytanie systemem DW2000 w **xlargerc** klasy zasobów będzie mieć dostęp do 6400 MB pamięci w każdej z 60 rozproszonej bazy danych.  W magazynie danych SQL istnieją dystrybucje 60. W związku z tym aby obliczyć przydział całkowitej ilości pamięci dla zapytań w klasie zasobu, powyższych wartości powinny mnożona przez 60.

### <a name="memory-allocations-system-wide-gb"></a>Pamięć alokacji systemowe (GB)
| DWU | smallrc | mediumrc | largerc | xlargerc |
|:--- |:---:|:---:|:---:|:---:|
| DW100 |6 |6 |12 |23 |
| DW200 |6 |12 |23 |47 |
| DW300 |6 |12 |23 |47 |
| DW400 |6 |23 |47 |94 |
| DW500 |6 |23 |47 |94 |
| DW600 |6 |23 |47 |94 |
| DW1000 |6 |47 |94 |188 |
| DW1200 |6 |47 |94 |188 |
| DW1500 |6 |47 |94 |188 |
| DW2000 |6 |94 |188 |375 |
| DW3000 |6 |94 |188 |375 |
| DW6000 |6 |188 |375 |750 |

Z tej tabeli alokacji pamięci systemowe widać, że zapytanie systemem DW2000 w klasie zasobu xlargerc został przydzielony łącznie 375 GB pamięci (MB 6400 * 60 dystrybucje / 1024 do przekonwertowania na GB) za pośrednictwem całości SQL Data Warehouse.

To samo obliczenie dotyczy klasy statyczne zasobów.
 
## <a name="concurrency-slot-consumption"></a>Użycie miejsca współbieżności  
Usługa SQL Data Warehouse przyznaje więcej pamięci do zapytań wykonywanych w wyższych klas zasobów. Ilość pamięci jest stały zasobów.  W związku z tym więcej pamięci przydzielona dla kwerendy, mniejszej liczby równoczesnych zapytań może zostać uruchomiony. Poniższa tabela powtórnie wszystkie poprzednie koncepcji w ramach jednego widoku, który pokazuje liczbę dostępnych współbieżności przez DWU i gniazd wykorzystanych w ramach każdej klasy zasobów.  

### <a name="allocation-and-consumption-of-concurrency-slots-for-dynamic-resource-classes"></a>Alokacja i użycie gniazd współbieżności dla klasy zasobu dynamicznego  
| DWU | Maksymalna liczba jednoczesnych kwerend | Współbieżność gniazd przydzielonych | Używane przez smallrc gniazd | Używane przez mediumrc gniazd | Używane przez largerc gniazd | Używane przez xlargerc gniazd |
|:--- |:---:|:---:|:---:|:---:|:---:|:---:|
| DW100 |4 |4 |1 |1 |2 |4 |
| DW200 |8 |8 |1 |2 |4 |8 |
| DW300 |12 |12 |1 |2 |4 |8 |
| DW400 |16 |16 |1 |4 |8 |16 |
| DW500 |20 |20 |1 |4 |8 |16 |
| DW600 |24 |24 |1 |4 |8 |16 |
| DW1000 |32 |40 |1 |8 |16 |32 |
| DW1200 |32 |48 |1 |8 |16 |32 |
| DW1500 |32 |60 |1 |8 |16 |32 |
| DW2000 |32 |80 |1 |16 |32 |64 |
| DW3000 |32 |120 |1 |16 |32 |64 |
| DW6000 |32 |240 |1 |32 |64 |128 |

### <a name="allocation-and-consumption-of-concurrency-slots-for-static-resource-classes"></a>Alokacja i użycie gniazd współbieżności dla klas statycznych zasobów  
| DWU | Maksymalna liczba jednoczesnych kwerend | Współbieżność gniazd przydzielonych |staticrc10 | staticrc20 | staticrc30 | staticrc40 | staticrc50 | staticrc60 | staticrc70 | staticrc80 |
|:--- |:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|
| DW100 |4 |4 |1 |2 |4 |4 |4 |4 |4 |4 |
| DW200 |8 |8 |1 |2 |4 |8 |8 |8 |8 |8 |
| DW300 |12 |12 |1 |2 |4 |8 |8 |8 |8 |8 |
| DW400 |16 |16 |1 |2 |4 |8 |16 |16 |16 |16 |
| DW500 | 20| 20| 1| 2| 4| 8| 16| 16| 16| 16|
| DW600 | 24| 24| 1| 2| 4| 8| 16| 16| 16| 16|
| DW1000 | 32| 40| 1| 2| 4| 8| 16| 32| 32| 32|
| DW1200 | 32| 48| 1| 2| 4| 8| 16| 32| 32| 32|
| DW1500 | 32| 60| 1| 2| 4| 8| 16| 32| 32| 32|
| DW2000 | 32| 80| 1| 2| 4| 8| 16| 32| 64| 64|
| DW3000 | 32| 120| 1| 2| 4| 8| 16| 32| 64| 64|
| DW6000 | 32| 240| 1| 2| 4| 8| 16| 32| 64| 128|

Z tych tabel widać z systemem SQL Data Warehouse jako DW1000 przydziela maksymalnie 32 zapytania jednoczesne i całkowitej 40 gniazd współbieżności. Jeśli wszyscy użytkownicy są uruchomione w smallrc, 32 zapytania jednoczesne będzie dozwolone, ponieważ każde zapytanie będzie używać 1 gniazdo współbieżności. Jeśli wszyscy użytkownicy na DW1000 były uruchomione w mediumrc, każdego zapytania będą przydzielone 800 MB na dystrybucji całkowitej ilości pamięci alokacji 47 GB dla kwerendy i współbieżności będzie ograniczony do użytkowników 5 (40 miejsc współbieżności 8 gniazd dla każdego użytkownika mediumrc /).

## <a name="selecting-proper-resource-class"></a>Wybieranie klasy odpowiednich zasobów  
Dobrym rozwiązaniem jest trwale przypisać użytkowników do klasy zasobów, zamiast zmieniać ich klasy zasobów. Na przykład obciążeń do tabel klastrowanego magazynu kolumn utworzyć indeksy wyższej jakości przy nadawaniu więcej pamięci. Aby zapewnić dostęp do pamięci wyższych obciążeń, Utwórz użytkownika specjalnie z myślą o podczas ładowania danych i trwale przypisać do tego użytkownika do wyższych klasy zasobów.
Istnieje kilka najlepszych rozwiązań, które należy wykonać w tym miejscu. Jak wspomniano powyżej, magazynu danych SQL obsługuje dwa rodzaje typu klasy zasobu: klasy statyczne zasobów i klasy zasobu dynamicznego.
### <a name="loading-best-practices"></a>Ładowanie najlepsze rozwiązania
1.  Oczekiwania w przypadku obciążeń z regularnych ilość danych, klasa statycznego zasobów jest dobrym rozwiązaniem. Później podczas skalowania, aby uzyskać więcej mocy obliczeniowej, magazynu danych będzie można uruchamiać więcej jednoczesnych kwerend out-of--box, jako użytkownik obciążenia nie korzystać z większej ilości pamięci.
2.  Jeśli oczekiwań większych obciążeń w niektórych przypadkach, klasa zasobu dynamicznego jest dobrym rozwiązaniem. Później, podczas skalowania, aby uzyskać więcej mocy obliczeniowej, obciążenia użytkownik otrzyma więcej pamięci out-of--box, dlatego stosowanie obciążenia mogą szybciej.

Pamięci niezbędnego do przetworzenia wydajne obciążenia zależy od charakteru tabeli załadowane i ilości danych przetwarzanych. Na przykład ładowanie danych do tabel WIK wymaga niektórych pamięci pozwala osiągnąć optymalizacji rowgroups CCI. Aby uzyskać więcej informacji zobacz indeksy magazynu kolumn — wskazówki dotyczące ładowania danych.

Jako najlepsze rozwiązanie zaleca się można użyć co najmniej 200MB pamięci w przypadku obciążeń.

### <a name="querying-best-practices"></a>Wykonywanie zapytania najlepsze rozwiązania
Zapytania mają różne wymagania w zależności od złożoności. Zwiększenie ilości pamięci na zapytanie, lub zwiększyć współbieżność to zarówno prawidłowe metody rozszerzyć ogólną przepustowość w zależności od potrzeb zapytania.
1.  Jeśli oczekiwania są regularnie, złożonych kwerend (na przykład do generowania raportów dziennego i tygodniowego) i nie trzeba korzystać z współbieżności, dobrym rozwiązaniem jest klasa zasobu dynamicznego. Jeśli system większej ilości danych do przetwarzania, skalowaniu w magazynie danych w związku z tym automatycznie zapewni większej ilości pamięci do użytkownika, uruchomić zapytanie.
2.  Jeżeli oczekiwania wzorców współbieżności dobowej lub zmienna (na przykład jeśli bazy danych jest poddawany kwerendzie za pośrednictwem interfejsu użytkownika szeroko dostępne w sieci web), klasy statyczne zasobów jest dobrym rozwiązaniem. Później przy skalowaniu w magazynie danych użytkownika skojarzonego z klasą zasobów statycznych automatycznie będzie można uruchamiać zapytania więcej jednoczesnych.

Wybór przydział pamięci odpowiednie w zależności od potrzeb zapytania jest nieuproszczony, zależy od wielu czynników, takich jak ilość danych pobieranych, rodzaj schematy tabeli i różnych sprzężenia, zaznaczenia i predykaty grupy. Z punktu widzenia ogólne, przydzielenie większej ilości pamięci będą zapytania, aby szybciej, ale może zmniejszyć ogólną współbieżności. Jeśli współbieżności nie stanowi to problemu, nadmierne przydzielanie pamięci nie uszkodzić. Można dopasować przepływności, w trakcie różnych odmian klasy zasobów mogą być wymagane.

Poniższe procedury składowanej służy do ustalenia grant współbieżności i pamięć dla każdej klasy zasobu w danym celu SLO i najlepsze najbliższej klasy zasobu operacje o znacznym wykorzystaniu WIK pamięci niepartycjonowany WIK tabeli w klasie zasobu:

#### <a name="description"></a>Opis:  
W tym miejscu jest celem tej procedury składowanej:  
1. Aby pomóc ustalić użytkownika współbieżności i pamięci przyznać na klasy zasobu w danym celu SLO. Użytkownik musi podać wartości NULL tablename i schematu dla tego, jak pokazano w poniższym przykładzie.  
2. Aby ułatwić użytkownika zorientować się najbliżej najlepsze klasy zasobu intensed pamięci operacje WIK (obciążenia, tabelę kopiowania odbudować indeksu, itp.) z systemem innym niż partycjonowanej tabeli WIK w klasie zasobu. Przechowywanej używa schematu tabeli, aby dowiedzieć się, grant wymaganej pamięci to.

#### <a name="dependencies--restrictions"></a>Zależności i ograniczeń:
- Ta przechowywanej nie jest przeznaczony do obliczenia wymagania dotyczące pamięci dla tabeli WIK podzielona na partycje.    
- To przechowywanej nie przyjmuje wymagania dotyczące pamięci pod uwagę dla części SELECT CTAS/INSERT-wybierz przyjęto założenie, aby wartość była proste wybierz.
- Ta przechowywanej używa tabeli tymczasowej, dlatego może być używany w sesji, której utworzono to przechowywanej.    
- Ta przechowywanej zależy od bieżącego ofert (Konfiguracja sprzętu, konfiguracji DMS) i w przypadku zmiany dowolnego tego następnie tego przechowywanej nie będzie działało poprawnie.  
- Ta przechowywanej zależy od istniejących limit oferowany współbieżności i jeśli zmieni się który następnie tego przechowywanej nie będzie działało poprawnie.  
- Ta przechowywanej zależy od istniejących ofert klasy zasobów i jeśli która zmieni się następnie to przechowywane bezzasadnie proces roboczy nie poprawnie.  

>  [!NOTE]  
>  Jeśli użytkownik nie otrzymuje dane wyjściowe po wykonaniu procedury składowanej z parametrami dostarczonego, może być dwa. <br />1. Parametr albo DW zawiera nieprawidłową wartość SLO <br />2. ALBO nie nie zgodnej klasy zasobu dla operacji WIK Jeśli podano nazwę tabeli. <br />Na przykład DW100, najwyższy dostępny przydział pamięci jest 400MB i czy schemat tabeli jest dostatecznie przetnie wymaganie 400MB.
      
#### <a name="usage-example"></a>Przykład użycia:
Składnia:  
`EXEC dbo.prc_workload_management_by_DWU @DWU VARCHAR(7), @SCHEMA_NAME VARCHAR(128), @TABLE_NAME VARCHAR(128)`  
1. @DWU: Należy podać parametr o wartości NULL do wyodrębnienia bieżącej wartości DWU z bazy danych magazynu danych lub podaj wszystkie obsługiwane DWU w postaci "DW100"
2. @SCHEMA_NAME: Podaj nazwę schematu tabeli
3. @TABLE_NAME: Podaj nazwę tabeli odsetek

Przykłady wykonywania tej procedury przechowywanej:  
```sql  
EXEC dbo.prc_workload_management_by_DWU 'DW2000', 'dbo', 'Table1';  
EXEC dbo.prc_workload_management_by_DWU NULL, 'dbo', 'Table1';  
EXEC dbo.prc_workload_management_by_DWU 'DW6000', NULL, NULL;  
EXEC dbo.prc_workload_management_by_DWU NULL, NULL, NULL;  
```

Tabela1 używane w powyższych przykładach można było utworzyć zgodnie z poniższymi instrukcjami:  
`CREATE TABLE Table1 (a int, b varchar(50), c decimal (18,10), d char(10), e varbinary(15), f float, g datetime, h date);`

#### <a name="heres-the-stored-procedure-definition"></a>Poniżej przedstawiono procedurę składowaną definicji:
```sql  
-------------------------------------------------------------------------------
-- Dropping prc_workload_management_by_DWU procedure if it exists.
-------------------------------------------------------------------------------
IF EXISTS (SELECT * FROM sys.objects WHERE type = 'P' AND name = 'prc_workload_management_by_DWU')
DROP PROCEDURE dbo.prc_workload_management_by_DWU
GO

-------------------------------------------------------------------------------
-- Creating prc_workload_management_by_DWU.
-------------------------------------------------------------------------------
CREATE PROCEDURE dbo.prc_workload_management_by_DWU
(   @DWU VARCHAR(7),
      @SCHEMA_NAME VARCHAR(128),
       @TABLE_NAME VARCHAR(128)
)
AS
IF @DWU IS NULL
BEGIN
-- Selecting proper DWU for the current DB if not specified.
SET @DWU = (
  SELECT 'DW'+CAST(COUNT(*)*100 AS VARCHAR(10))
  FROM sys.dm_pdw_nodes
  WHERE type = 'COMPUTE')
END

DECLARE @DWU_NUM INT
SET @DWU_NUM = CAST (SUBSTRING(@DWU, 3, LEN(@DWU)-2) AS INT)

-- Raise error if either schema name or table name is supplied but not both them supplied
--IF ((@SCHEMA_NAME IS NOT NULL AND @TABLE_NAME IS NULL) OR (@TABLE_NAME IS NULL AND @SCHEMA_NAME IS NOT NULL))
--     RAISEERROR('User need to supply either both Schema Name and Table Name or none of them')
       
-- Dropping temp table if exists.
IF OBJECT_ID('tempdb..#ref') IS NOT NULL
BEGIN
  DROP TABLE #ref;
END

-- Creating ref. temptable (CTAS) to hold mapping info.
-- CREATE TABLE #ref
CREATE TABLE #ref
WITH (DISTRIBUTION = ROUND_ROBIN)
AS 
WITH
-- Creating concurrency slots mapping for various DWUs.
alloc
AS
(
  SELECT 'DW100' AS DWU, 4 AS max_queries, 4 AS max_slots, 1 AS slots_used_smallrc, 1 AS slots_used_mediumrc,
        2 AS slots_used_largerc, 4 AS slots_used_xlargerc, 1 AS slots_used_staticrc10, 2 AS slots_used_staticrc20,
        4 AS slots_used_staticrc30, 4 AS slots_used_staticrc40, 4 AS slots_used_staticrc50,
        4 AS slots_used_staticrc60, 4 AS slots_used_staticrc70, 4 AS slots_used_staticrc80
  UNION ALL
    SELECT 'DW200', 8, 8, 1, 2, 4, 8, 1, 2, 4, 8, 8, 8, 8, 8
  UNION ALL
    SELECT 'DW300', 12, 12, 1, 2, 4, 8, 1, 2, 4, 8, 8, 8, 8, 8
  UNION ALL
    SELECT 'DW400', 16, 16, 1, 4, 8, 16, 1, 2, 4, 8, 16, 16, 16, 16
  UNION ALL
     SELECT 'DW500', 20, 20, 1, 4, 8, 16, 1, 2, 4, 8, 16, 16, 16, 16
  UNION ALL
    SELECT 'DW600', 24, 24, 1, 4, 8, 16, 1, 2, 4, 8, 16, 16, 16, 16
  UNION ALL
    SELECT 'DW1000', 32, 40, 1, 8, 16, 32, 1, 2, 4, 8, 16, 32, 32, 32
  UNION ALL
    SELECT 'DW1200', 32, 48, 1, 8, 16, 32, 1, 2, 4, 8, 16, 32, 32, 32
  UNION ALL
    SELECT 'DW1500', 32, 60, 1, 8, 16, 32, 1, 2, 4, 8, 16, 32, 32, 32
  UNION ALL
    SELECT 'DW2000', 32, 80, 1, 16, 32, 64, 1, 2, 4, 8, 16, 32, 64, 64
  UNION ALL
   SELECT 'DW3000', 32, 120, 1, 16, 32, 64, 1, 2, 4, 8, 16, 32, 64, 64
  UNION ALL
    SELECT 'DW6000', 32, 240, 1, 32, 64, 128, 1, 2, 4, 8, 16, 32, 64, 128
)
-- Creating workload mapping to their corresponding slot consumption and default memory grant.
,map
AS
(
  SELECT 'SloDWGroupC00' AS wg_name,1 AS slots_used,100 AS tgt_mem_grant_MB
  UNION ALL
    SELECT 'SloDWGroupC01',2,200
  UNION ALL
    SELECT 'SloDWGroupC02',4,400
  UNION ALL
    SELECT 'SloDWGroupC03',8,800
  UNION ALL
    SELECT 'SloDWGroupC04',16,1600
  UNION ALL
    SELECT 'SloDWGroupC05',32,3200
  UNION ALL
    SELECT 'SloDWGroupC06',64,6400
  UNION ALL
    SELECT 'SloDWGroupC07',128,12800
)
-- Creating ref based on current / asked DWU.
, ref
AS
(
  SELECT  a1.*
  ,       m1.wg_name          AS wg_name_smallrc
  ,       m1.tgt_mem_grant_MB AS tgt_mem_grant_MB_smallrc
  ,       m2.wg_name          AS wg_name_mediumrc
  ,       m2.tgt_mem_grant_MB AS tgt_mem_grant_MB_mediumrc
  ,       m3.wg_name          AS wg_name_largerc
  ,       m3.tgt_mem_grant_MB AS tgt_mem_grant_MB_largerc
  ,       m4.wg_name          AS wg_name_xlargerc
  ,       m4.tgt_mem_grant_MB AS tgt_mem_grant_MB_xlargerc
  ,       m5.wg_name          AS wg_name_staticrc10
  ,       m5.tgt_mem_grant_MB AS tgt_mem_grant_MB_staticrc10
  ,       m6.wg_name          AS wg_name_staticrc20
  ,       m6.tgt_mem_grant_MB AS tgt_mem_grant_MB_staticrc20
  ,       m7.wg_name          AS wg_name_staticrc30
  ,       m7.tgt_mem_grant_MB AS tgt_mem_grant_MB_staticrc30
  ,       m8.wg_name          AS wg_name_staticrc40
  ,       m8.tgt_mem_grant_MB AS tgt_mem_grant_MB_staticrc40
  ,       m9.wg_name          AS wg_name_staticrc50
  ,       m9.tgt_mem_grant_MB AS tgt_mem_grant_MB_staticrc50
  ,       m10.wg_name          AS wg_name_staticrc60
  ,       m10.tgt_mem_grant_MB AS tgt_mem_grant_MB_staticrc60
  ,       m11.wg_name          AS wg_name_staticrc70
  ,       m11.tgt_mem_grant_MB AS tgt_mem_grant_MB_staticrc70
  ,       m12.wg_name          AS wg_name_staticrc80
  ,       m12.tgt_mem_grant_MB AS tgt_mem_grant_MB_staticrc80
  FROM alloc a1
  JOIN map   m1  ON a1.slots_used_smallrc     = m1.slots_used
  JOIN map   m2  ON a1.slots_used_mediumrc    = m2.slots_used
  JOIN map   m3  ON a1.slots_used_largerc     = m3.slots_used
  JOIN map   m4  ON a1.slots_used_xlargerc    = m4.slots_used
  JOIN map   m5  ON a1.slots_used_staticrc10    = m5.slots_used
  JOIN map   m6  ON a1.slots_used_staticrc20    = m6.slots_used
  JOIN map   m7  ON a1.slots_used_staticrc30    = m7.slots_used
  JOIN map   m8  ON a1.slots_used_staticrc40    = m8.slots_used
  JOIN map   m9  ON a1.slots_used_staticrc50    = m9.slots_used
  JOIN map   m10  ON a1.slots_used_staticrc60    = m10.slots_used
  JOIN map   m11  ON a1.slots_used_staticrc70    = m11.slots_used
  JOIN map   m12  ON a1.slots_used_staticrc80    = m12.slots_used
-- WHERE   a1.DWU = @DWU
  WHERE   a1.DWU = UPPER(@DWU)
)
SELECT  DWU
,       max_queries
,       max_slots
,       slots_used
,       wg_name
,       tgt_mem_grant_MB
,       up1 as rc
,       (ROW_NUMBER() OVER(PARTITION BY DWU ORDER BY DWU)) as rc_id
FROM
(
    SELECT  DWU
    ,       max_queries
    ,       max_slots
    ,       slots_used
    ,       wg_name
    ,       tgt_mem_grant_MB
    ,       REVERSE(SUBSTRING(REVERSE(wg_names),1,CHARINDEX('_',REVERSE(wg_names),1)-1)) as up1
    ,       REVERSE(SUBSTRING(REVERSE(tgt_mem_grant_MBs),1,CHARINDEX('_',REVERSE(tgt_mem_grant_MBs),1)-1)) as up2
    ,       REVERSE(SUBSTRING(REVERSE(slots_used_all),1,CHARINDEX('_',REVERSE(slots_used_all),1)-1)) as up3
    FROM    ref AS r1
    UNPIVOT
    (
        wg_name FOR wg_names IN (wg_name_smallrc,wg_name_mediumrc,wg_name_largerc,wg_name_xlargerc,
        wg_name_staticrc10, wg_name_staticrc20, wg_name_staticrc30, wg_name_staticrc40, wg_name_staticrc50,
        wg_name_staticrc60, wg_name_staticrc70, wg_name_staticrc80)
    ) AS r2
    UNPIVOT
    (
        tgt_mem_grant_MB FOR tgt_mem_grant_MBs IN (tgt_mem_grant_MB_smallrc,tgt_mem_grant_MB_mediumrc,
        tgt_mem_grant_MB_largerc,tgt_mem_grant_MB_xlargerc, tgt_mem_grant_MB_staticrc10, tgt_mem_grant_MB_staticrc20,
        tgt_mem_grant_MB_staticrc30, tgt_mem_grant_MB_staticrc40, tgt_mem_grant_MB_staticrc50,
        tgt_mem_grant_MB_staticrc60, tgt_mem_grant_MB_staticrc70, tgt_mem_grant_MB_staticrc80)
    ) AS r3
    UNPIVOT
    (
        slots_used FOR slots_used_all IN (slots_used_smallrc,slots_used_mediumrc,slots_used_largerc,
        slots_used_xlargerc, slots_used_staticrc10, slots_used_staticrc20, slots_used_staticrc30,
        slots_used_staticrc40, slots_used_staticrc50, slots_used_staticrc60, slots_used_staticrc70,
        slots_used_staticrc80)
    ) AS r4
) a
WHERE   up1 = up2
AND     up1 = up3
;
-- Getting current info about workload groups.
WITH  
dmv  
AS  
(
  SELECT
          rp.name                                           AS rp_name
  ,       rp.max_memory_kb*1.0/1048576                      AS rp_max_mem_GB
  ,       (rp.max_memory_kb*1.0/1024)
          *(request_max_memory_grant_percent/100)           AS max_memory_grant_MB
  ,       (rp.max_memory_kb*1.0/1048576)
          *(request_max_memory_grant_percent/100)           AS max_memory_grant_GB
  ,       wg.name                                           AS wg_name
  ,       wg.importance                                     AS importance
  ,       wg.request_max_memory_grant_percent               AS request_max_memory_grant_percent
  FROM    sys.dm_pdw_nodes_resource_governor_workload_groups wg
  JOIN    sys.dm_pdw_nodes_resource_governor_resource_pools rp    ON  wg.pdw_node_id  = rp.pdw_node_id
                                                                  AND wg.pool_id      = rp.pool_id
  WHERE   rp.name = 'SloDWPool'
  GROUP BY
          rp.name
  ,       rp.max_memory_kb
  ,       wg.name
  ,       wg.importance
  ,       wg.request_max_memory_grant_percent
)
-- Creating resource class name mapping.
,names
AS
(
  SELECT 'smallrc' as resource_class, 1 as rc_id
  UNION ALL
    SELECT 'mediumrc', 2
  UNION ALL
    SELECT 'largerc', 3
  UNION ALL
    SELECT 'xlargerc', 4
  UNION ALL
    SELECT 'staticrc10', 5
  UNION ALL
    SELECT 'staticrc20', 6
  UNION ALL
    SELECT 'staticrc30', 7
  UNION ALL
    SELECT 'staticrc40', 8
  UNION ALL
    SELECT 'staticrc50', 9
  UNION ALL
    SELECT 'staticrc60', 10
  UNION ALL
    SELECT 'staticrc70', 11
  UNION ALL
    SELECT 'staticrc80', 12
)
,base AS
(   SELECT  schema_name
    ,       table_name
    ,       SUM(column_count)                   AS column_count
    ,       ISNULL(SUM(short_string_column_count),0)   AS short_string_column_count
    ,       ISNULL(SUM(long_string_column_count),0)    AS long_string_column_count
    FROM    (   SELECT  sm.name                                             AS schema_name
                ,       tb.name                                             AS table_name
                ,       COUNT(co.column_id)                                 AS column_count
                           ,       CASE    WHEN co.system_type_id IN (36,43,106,108,165,167,173,175,231,239) 
                                AND  co.max_length <= 32 
                                THEN COUNT(co.column_id) 
                        END                                                 AS short_string_column_count
                ,       CASE    WHEN co.system_type_id IN (165,167,173,175,231,239) 
                                AND  co.max_length > 32 and co.max_length <=8000
                                THEN COUNT(co.column_id) 
                        END                                                 AS long_string_column_count
                FROM    sys.schemas AS sm
                JOIN    sys.tables  AS tb   on sm.[schema_id] = tb.[schema_id]
                JOIN    sys.columns AS co   ON tb.[object_id] = co.[object_id]
                           WHERE tb.name = @TABLE_NAME AND sm.name = @SCHEMA_NAME
                GROUP BY sm.name
                ,        tb.name
                ,        co.system_type_id
                ,        co.max_length            ) a
GROUP BY schema_name
,        table_name
)
, size AS
(
SELECT  schema_name
,       table_name
,       75497472                                            AS table_overhead

,       column_count*1048576*8                              AS column_size
,       short_string_column_count*1048576*32                       AS short_string_size,       (long_string_column_count*16777216) AS long_string_size
FROM    base
UNION
SELECT CASE WHEN COUNT(*) = 0 THEN 'EMPTY' END as schema_name
         ,CASE WHEN COUNT(*) = 0 THEN 'EMPTY' END as table_name
         ,CASE WHEN COUNT(*) = 0 THEN 0 END as table_overhead
         ,CASE WHEN COUNT(*) = 0 THEN 0 END as column_size
         ,CASE WHEN COUNT(*) = 0 THEN 0 END as short_string_size

,CASE WHEN COUNT(*) = 0 THEN 0 END as long_string_size
FROM   base
)
, load_multiplier as 
(
SELECT  CASE 
                     WHEN FLOOR(8 * (CAST (@DWU_NUM AS FLOAT)/6000)) > 0 THEN FLOOR(8 * (CAST (@DWU_NUM AS FLOAT)/6000)) 
                     ELSE 1 
              END AS multipliplication_factor
) 
       SELECT  r1.DWU
       , schema_name
       , table_name
       , rc.resource_class as closest_rc_in_increasing_order
       , max_queries_at_this_rc = CASE
             WHEN (r1.max_slots / r1.slots_used > r1.max_queries)
                  THEN r1.max_queries
             ELSE r1.max_slots / r1.slots_used
                  END
       , r1.max_slots as max_concurrency_slots
       , r1.slots_used as required_slots_for_the_rc
       , r1.tgt_mem_grant_MB  as rc_mem_grant_MB
       , CAST((table_overhead*1.0+column_size+short_string_size+long_string_size)*multipliplication_factor/1048576    AS DECIMAL(18,2)) AS est_mem_grant_required_for_cci_operation_MB       
       FROM    size, load_multiplier, #ref r1, names  rc
       WHERE r1.rc_id=rc.rc_id
                     AND CAST((table_overhead*1.0+column_size+short_string_size+long_string_size)*multipliplication_factor/1048576    AS DECIMAL(18,2)) < r1.tgt_mem_grant_MB
       ORDER BY ABS(CAST((table_overhead*1.0+column_size+short_string_size+long_string_size)*multipliplication_factor/1048576    AS DECIMAL(18,2)) - r1.tgt_mem_grant_MB)
GO
```

## <a name="query-importance"></a>Znaczenie zapytania
Usługa SQL Data Warehouse implementuje klasy zasobów za pomocą grupy obciążenia. Składa się łącznie osiem grup obciążenia, które kontrolują zachowanie klasy zasobu w różnych rozmiarach DWU. Dla dowolnego DWU SQL Data Warehouse używa tylko czterech grup osiem obciążenia. To rozwiązanie ma sens, ponieważ każda grupa obciążenia jest przypisany do czterech grup zasobów: smallrc, mediumrc, largerc, lub xlargerc. Opis grupy obciążenia znaczenie jest, że niektóre z tych grup obciążenia są ustawione na wyższy *znaczenie*. Znaczenie jest używany dla Procesora planowania. Uruchom o wysokiej ważności zapytania pobierze trzy razy więcej cykli Procesora niż jednostki o średnim znaczeniu. W związku z tym mapowania miejsca współbieżności również określić priorytet procesora CPU. Jeśli zapytanie wykorzystuje co najmniej 16 miejsc, jest on uruchamiany jako wysokiej ważności.

W poniższej tabeli przedstawiono znaczenie mapowania dla każdej grupy obciążenia.

### <a name="workload-group-mappings-to-concurrency-slots-and-importance"></a>Mapowania grupy obciążenia do gniazda współbieżności i ważność
| Grupy obciążenia | Mapowanie miejsca współbieżności | MB / dystrybucji | Mapowanie znaczenie |
|:--- |:---:|:---:|:--- |
| SloDWGroupC00 |1 |100 |Medium |
| SloDWGroupC01 |2 |200 |Medium |
| SloDWGroupC02 |4 |400 |Medium |
| SloDWGroupC03 |8 |800 |Medium |
| SloDWGroupC04 |16 |1,600 |Wysoka |
| SloDWGroupC05 |32 |3,200 |Wysoka |
| SloDWGroupC06 |64 |6,400 |Wysoka |
| SloDWGroupC07 |128 |12,800 |Wysoka |

Z **alokacja i użycie gniazd współbieżności** wykresu, można sprawdzić, czy DW500 używa 1, 4, 8 i współbieżność 16 miejsc dla smallrc, mediumrc largerc i xlargerc, odpowiednio. Te wartości można wyszukiwać, na wykresie można znaleźć znaczenie dla każdej klasy zasobów.

### <a name="dw500-mapping-of-resource-classes-to-importance"></a>Mapowanie DW500 klasy zasobu znaczenia
| Klasa zasobów | Grupy obciążenia | Używać miejsc współbieżności | MB / dystrybucji | Ważność |
|:--- |:--- |:---:|:---:|:--- |
| smallrc |SloDWGroupC00 |1 |100 |Medium |
| mediumrc |SloDWGroupC02 |4 |400 |Medium |
| largerc |SloDWGroupC03 |8 |800 |Medium |
| xlargerc |SloDWGroupC04 |16 |1,600 |Wysoka |
| staticrc10 |SloDWGroupC00 |1 |100 |Medium |
| staticrc20 |SloDWGroupC01 |2 |200 |Medium |
| staticrc30 |SloDWGroupC02 |4 |400 |Medium |
| staticrc40 |SloDWGroupC03 |8 |800 |Medium |
| staticrc50 |SloDWGroupC03 |16 |1,600 |Wysoka |
| staticrc60 |SloDWGroupC03 |16 |1,600 |Wysoka |
| staticrc70 |SloDWGroupC03 |16 |1,600 |Wysoka |
| staticrc80 |SloDWGroupC03 |16 |1,600 |Wysoka |

Aby przyjrzeć się różnic w alokacji zasobów pamięci szczegółowo z punktu widzenia zarządcy zasobów lub do analizowania active i historyczne użycia grup obciążenia podczas rozwiązywania problemu, można użyć następującego zapytania DMV.

```sql
WITH rg
AS
(   SELECT  
     pn.name                        AS node_name
    ,pn.[type]                        AS node_type
    ,pn.pdw_node_id                    AS node_id
    ,rp.name                        AS pool_name
    ,rp.max_memory_kb*1.0/1024                AS pool_max_mem_MB
    ,wg.name                        AS group_name
    ,wg.importance                    AS group_importance
    ,wg.request_max_memory_grant_percent        AS group_request_max_memory_grant_pcnt
    ,wg.max_dop                        AS group_max_dop
    ,wg.effective_max_dop                AS group_effective_max_dop
    ,wg.total_request_count                AS group_total_request_count
    ,wg.total_queued_request_count            AS group_total_queued_request_count
    ,wg.active_request_count                AS group_active_request_count
    ,wg.queued_request_count                AS group_queued_request_count
    FROM    sys.dm_pdw_nodes_resource_governor_workload_groups wg
    JOIN    sys.dm_pdw_nodes_resource_governor_resource_pools rp    
            ON  wg.pdw_node_id  = rp.pdw_node_id
            AND wg.pool_id      = rp.pool_id
    JOIN    sys.dm_pdw_nodes pn
            ON    wg.pdw_node_id    = pn.pdw_node_id
    WHERE   wg.name like 'SloDWGroup%'
        AND     rp.name = 'SloDWPool'
)
SELECT    pool_name
,        pool_max_mem_MB
,        group_name
,        group_importance
,        (pool_max_mem_MB/100)*group_request_max_memory_grant_pcnt AS max_memory_grant_MB
,        node_name
,        node_type
,       group_total_request_count
,       group_total_queued_request_count
,       group_active_request_count
,       group_queued_request_count
FROM    rg
ORDER BY
    node_name
,    group_request_max_memory_grant_pcnt
,    group_importance
;
```

## <a name="queries-that-honor-concurrency-limits"></a>Zapytania, które uwzględnić limity współbieżności
Większość zapytań podlegają klasy zasobów. Te zapytania musi mieścić się w obu równoczesnych zapytań i współbieżność miejsca progów. Użytkownik nie Wyklucz zapytania z modelu miejsca współbieżności.

Aby przywołują, następujące instrukcje honorować klasy zasobu:

* WYBIERZ OPCJĘ WSTAWIANIA
* AKTUALIZACJA
* DELETE
* Wybierz (podczas wykonywania zapytania tabele użytkownika)
* ALTER INDEX REBUILD
* ALTER INDEX REORGANIZE
* ALTER TABLE REBUILD
* TWORZENIE INDEKSU
* UTWÓRZ KLASTROWANY INDEKS MAGAZYNU KOLUMN
* UTWÓRZ TABLE AS SELECT (CTAS)
* Ładowanie danych
* Operacje przenoszenia danych prowadzone przez usługi przenoszenia danych (DMS)

## <a name="query-exceptions-to-concurrency-limits"></a>Wyjątki zapytania limity współbieżności
Niektóre kwerendy, nie uznają klasy zasobów, do której jest przypisany użytkownik. Wyjątki te limity współbieżności są wysyłane w przypadku zasoby pamięci wymagane dla określonego polecenia jest niskie, często ponieważ polecenie jest operacja metadanych. Celem tych wyjątków jest aby uniknąć większych alokacji pamięci dla zapytań, które nigdy nie będzie je. W takich przypadkach domyślną klasę małych zasobów (smallrc) jest zawsze używane niezależnie od tego klasy zasobów rzeczywistych przypisane do użytkownika. Na przykład `CREATE LOGIN` zawsze będą uruchamiane w smallrc. Zasoby wymagane do wykonania tej operacji są bardzo niskie, więc go nie ma sensu uwzględnienie zapytania w modelu miejsca współbieżności.  Te zapytania nie są również ograniczone przez użytkownika 32 limit współbieżności, nieograniczoną liczbę tych zapytań można uruchomić limit sesji 1024 sesji.

Poniższe instrukcje, nie uznają klasy zasobu:

* Utwórz lub DROP TABLE
* INSTRUKCJA ALTER TABLE... PRZEŁĄCZNIK, podziału lub SCALENIA PARTYCJI
* ALTER INDEX WYŁĄCZ
* INDEKS
* Utwórz, aktualizacji lub DROP STATISTICS
* OBCIĄĆ TABELI
* INSTRUKCJA ALTER AUTORYZACJI
* UTWÓRZ DANE LOGOWANIA
* CREATE, ALTER i DROP USER
* CREATE, ALTER i DROP procedury
* Utwórz lub PORZUĆ widok
* WSTAW WARTOŚCI
* Wybierz z widoków systemowych i widoków DMV
* WYJAŚNIĆ
* DBCC

<!--
Removed as these two are not confirmed / supported under SQLDW
- CREATE REMOTE TABLE AS SELECT
- CREATE EXTERNAL TABLE AS SELECT
- REDISTRIBUTE
-->

##  <a name="changing-user-resource-class-example"></a> Zmiany w przykładzie klasa zasobów użytkownika
1. **Utworzyć danych logowania:** otworzyć połączenia z **wzorca** bazy danych programu SQL Server obsługującego bazę danych magazynu danych SQL i wykonaj następujące polecenia.
   
    ```sql
    CREATE LOGIN newperson WITH PASSWORD = 'mypassword';
    CREATE USER newperson for LOGIN newperson;
    ```
   
   > [!NOTE]
   > Jest dobrym rozwiązaniem jest utworzenie użytkownika w bazie danych master dla użytkowników usługi Azure SQL Data Warehouse. Tworzenie użytkownika w głównym umożliwia użytkownikom logowanie za pomocą takich narzędzi jak SSMS bez podawania nazwy bazy danych.  Umożliwia także użycie Eksplorator obiektów do wyświetlania wszystkich baz danych na serwerze SQL.  Aby uzyskać więcej informacji o tworzeniu i zarządzanie użytkownikami, zobacz [Zabezpieczanie bazy danych w usłudze SQL Data Warehouse][Secure a database in SQL Data Warehouse].
   > 
   > 
2. **Utwórz użytkownika usługi SQL Data Warehouse:** nawiązać połączenie z **SQL Data Warehouse** bazy danych, a następnie uruchom następujące polecenie.
   
    ```sql
    CREATE USER newperson FOR LOGIN newperson;
    ```
3. **Udzielanie uprawnień:** przyznaje w poniższym przykładzie `CONTROL` na **SQL Data Warehouse** bazy danych. `CONTROL` w bazie danych poziom jest odpowiednikiem db_owner w programie SQL Server.
   
    ```sql
    GRANT CONTROL ON DATABASE::MySQLDW to newperson;
    ```
4. **Zwiększ klasy zasobów:** Użyj następującego zapytania, aby dodać użytkownika do roli zarządzania wyższe obciążenie.
   
    ```sql
    EXEC sp_addrolemember 'largerc', 'newperson'
    ```
5. **Zmniejsz klasy zasobów:** Użyj następującego zapytania, aby usunąć użytkownika z roli zarządzania obciążenia.
   
    ```sql
    EXEC sp_droprolemember 'largerc', 'newperson';
    ```
   
   > [!NOTE]
   > Nie jest możliwe usuwanie użytkownika z smallrc.
   > 
   > 

## <a name="queued-query-detection-and-other-dmvs"></a>Zapytania w kolejce wykrywania i innych widoków DMV
Można użyć `sys.dm_pdw_exec_requests` DMV do identyfikacji zapytania, które czekają w kolejce współbieżności. Wysyła zapytanie oczekiwanie na gnieździe współbieżności będzie mieć stan **zawieszone**.

```sql
SELECT      r.[request_id]                 AS Request_ID
        ,r.[status]                 AS Request_Status
        ,r.[submit_time]             AS Request_SubmitTime
        ,r.[start_time]                 AS Request_StartTime
        ,DATEDIFF(ms,[submit_time],[start_time]) AS Request_InitiateDuration_ms
        ,r.resource_class                         AS Request_resource_class
FROM    sys.dm_pdw_exec_requests r;
```

Role związane z zarządzaniem obciążenia mogą być wyświetlane z `sys.database_principals`.

```sql
SELECT  ro.[name]           AS [db_role_name]
FROM    sys.database_principals ro
WHERE   ro.[type_desc]      = 'DATABASE_ROLE'
AND     ro.[is_fixed_role]  = 0;
```

Następujące zapytanie wyświetla każdy użytkownik jest przypisany do roli.

```sql
SELECT     r.name AS role_principal_name
        ,m.name AS member_principal_name
FROM    sys.database_role_members rm
JOIN    sys.database_principals AS r            ON rm.role_principal_id        = r.principal_id
JOIN    sys.database_principals AS m            ON rm.member_principal_id    = m.principal_id
WHERE    r.name IN ('mediumrc','largerc', 'xlargerc');
```

Magazyn danych SQL zawiera następujące typy oczekiwania:

* **LocalQueriesConcurrencyResourceType**: zapytania, które znajdują się poza framework miejsca współbieżności. Zapytania DMV i systemu, takich jak funkcje `SELECT @@VERSION` przedstawiono przykłady lokalnego zapytań.
* **UserConcurrencyResourceType**: zapytania, które znajdują się w ramach miejsca współbieżności. Zapytania dotyczące tabel użytkownika końcowego przedstawiają przykłady używających tego typu zasobu.
* **DmsConcurrencyResourceType**: czeka, wynikające z operacji przenoszenia danych.
* **BackupConcurrencyResourceType**: ten oczekiwania wskazuje, że bazy danych jest tworzona kopia zapasowa. Maksymalna wartość dla tego typu zasobu jest 1. Jeśli wiele kopii zapasowych żądano w tym samym czasie, Dodaj pozostałe kolejki.

`sys.dm_pdw_waits` DMV może służyć do wyświetlania zasobów, które oczekuje żądanie.

```sql
SELECT  w.[wait_id]
,       w.[session_id]
,       w.[type]            AS Wait_type
,       w.[object_type]
,       w.[object_name]
,       w.[request_id]
,       w.[request_time]
,       w.[acquire_time]
,       w.[state]
,       w.[priority]
,    SESSION_ID()            AS Current_session
,    s.[status]            AS Session_status
,    s.[login_name]
,    s.[query_count]
,    s.[client_id]
,    s.[sql_spid]
,    r.[command]            AS Request_command
,    r.[label]
,    r.[status]            AS Request_status
,    r.[submit_time]
,    r.[start_time]
,    r.[end_compile_time]
,    r.[end_time]
,    DATEDIFF(ms,r.[submit_time],r.[start_time])        AS Request_queue_time_ms
,    DATEDIFF(ms,r.[start_time],r.[end_compile_time])    AS Request_compile_time_ms
,    DATEDIFF(ms,r.[end_compile_time],r.[end_time])        AS Request_execution_time_ms
,    r.[total_elapsed_time]
FROM    sys.dm_pdw_waits w
JOIN    sys.dm_pdw_exec_sessions s  ON w.[session_id] = s.[session_id]
JOIN    sys.dm_pdw_exec_requests r  ON w.[request_id] = r.[request_id]
WHERE    w.[session_id] <> SESSION_ID();
```

`sys.dm_pdw_resource_waits` DMV pokazuje tylko czeka zasobów, które są używane przez określonego zapytania. Czas oczekiwania zasobu tylko mierzy czas oczekiwania na zasoby wymagane do zrealizowania, a nie czas oczekiwania sygnał, który określa czas potrzebny dla podstawowych serwerów SQL, można zaplanować zapytania na Procesor.

```sql
SELECT  [session_id]
,       [type]
,       [object_type]
,       [object_name]
,       [request_id]
,       [request_time]
,       [acquire_time]
,       DATEDIFF(ms,[request_time],[acquire_time])  AS acquire_duration_ms
,       [concurrency_slots_used]                    AS concurrency_slots_reserved
,       [resource_class]
,       [wait_id]                                   AS queue_position
FROM    sys.dm_pdw_resource_waits
WHERE    [session_id] <> SESSION_ID();
```

`sys.dm_pdw_wait_stats` DMV może służyć do analizy trendów historycznych oczekiwania.

```sql
SELECT    w.[pdw_node_id]
,        w.[wait_name]
,        w.[max_wait_time]
,        w.[request_count]
,        w.[signal_time]
,        w.[completed_count]
,        w.[wait_time]
FROM    sys.dm_pdw_wait_stats w;
```

## <a name="next-steps"></a>Kolejne kroki
Aby uzyskać więcej informacji o zarządzaniu bazy danych użytkowników i zabezpieczeń, zobacz [Zabezpieczanie bazy danych w usłudze SQL Data Warehouse][Secure a database in SQL Data Warehouse]. Aby uzyskać więcej informacji na temat sposobu większe grupy zasobów może zwiększyć jakość indeksu klastrowanego magazynu kolumn, zobacz [ponowne tworzenie indeksów do poprawy jakości segmentu].

<!--Image references-->

<!--Article references-->
[Secure a database in SQL Data Warehouse]: ./sql-data-warehouse-overview-manage-security.md
[ponowne tworzenie indeksów do poprawy jakości segmentu]: ./sql-data-warehouse-tables-index.md#rebuilding-indexes-to-improve-segment-quality
[Secure a database in SQL Data Warehouse]: ./sql-data-warehouse-overview-manage-security.md

<!--MSDN references-->
[Managing Databases and Logins in Azure SQL Database]:https://msdn.microsoft.com/library/azure/ee336235.aspx

<!--Other Web references-->
