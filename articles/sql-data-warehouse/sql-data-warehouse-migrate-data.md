---
title: Migrowanie danych do usługi SQL Data Warehouse | Dokumentacja firmy Microsoft
description: Wskazówki dotyczące migrowania danych Azure SQL Data Warehouse związane z opracowywaniem rozwiązań.
services: sql-data-warehouse
author: jrowlandjones
manager: craigg-msft
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: implement
ms.date: 04/17/2018
ms.author: jrj
ms.reviewer: igorstan
ms.openlocfilehash: ca467ae5fbe784399e4e046c47c920ff7dec638e
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2018
ms.locfileid: "31796007"
---
# <a name="migrate-your-data"></a>Migrowanie danych
Można przenieść dane z różnych źródeł do magazynu danych SQL przy użyciu różnych narzędzi.  Kopiuj ADF, SSIS i bcp wszystkie umożliwia osiągnięcie tego celu. Jako ilość danych zwiększa należy traktować o podziału na etapy procesu migracji danych. Daje to możliwość optymalizacji każdy krok, wydajności i odporności zapewnić migrację danych smooth.

W tym artykule omówiono najpierw scenariusze migracji prostego kopiowania ADF SSIS i bcp. Następnie wygląda bardziej szczegółowe w sposób mogą być optymalizowane migracji.

## <a name="azure-data-factory-adf-copy"></a>Azure kopiowania fabryki danych (ADF)
[Kopiuj ADF] [ ADF Copy] jest częścią [fabryki danych Azure][Azure Data Factory]. Kopiuj ADF służy do eksportowania danych do plików prostych znajdującej się w magazynie lokalnym do zdalnego plików prostych przechowywane w magazynie obiektów blob Azure lub bezpośrednio do usługi SQL Data Warehouse.

Jeśli dane rozpoczyna się w płaskiej plików, a następnie należy najpierw przenieść do obiektu blob magazynu Azure przed zainicjowaniem obciążenia go do usługi SQL Data Warehouse. Gdy dane są przesyłane do magazynu obiektów blob platformy Azure mogą być używane [kopiowania ADF] [ ADF Copy] ponownie, aby wypchnąć dane do usługi SQL Data Warehouse.

Program PolyBase zapewnia również opcję wysokiej wydajności dla ładowania danych. Która jednak oznaczają za pomocą dwóch narzędzi, zamiast jednego. Jeśli należy uzyskać najlepszą wydajność, użyj programu PolyBase. Jeśli chcesz, aby w środowisku jednego narzędzia (i danych nie jest bardzo dużej) ADF jest odpowiedź.


> 
> 

HEAD za pośrednictwem w następującym artykule o niektórych dużej [przykłady ADF][ADF samples].

## <a name="integration-services"></a>Usługi integracji
Integration Services (SSIS) to wydajny i elastyczny wyodrębniania, przekształcania i ładowania (ETL) Narzędzie obsługującego złożone przepływy pracy, przekształcania danych i kilka opcji ładowania danych. Umożliwia po prostu transferu danych do platformy Azure lub w ramach szerszych migracji SSIS.

> [!NOTE]
> SSIS można wyeksportować UTF-8 bez znacznika kolejności bajtów w pliku. Aby skonfigurować to należy najpierw użyć składnika kolumny pochodnej można przekonwertować danych znakowych w przepływ danych do użycia strona kodowa 65001 UTF-8. Po konwersji kolumny zapisać danych do pliku prostego adapter miejsca docelowego, zapewniając, że 65001 została również wybrana jako strony kodowej w pliku.
> 
> 

SSIS łączy do usługi SQL Data Warehouse tak samo, jak może zostać nawiązane połączenie z wdrożeniem programu SQL Server. Jednak połączenia należy używać Menedżera połączeń ADO.NET. Możesz również należy zwrócić uwagę, aby skonfigurować "Użyj wstawiania zbiorczego dostępne" ustawienie, aby zmaksymalizować przepustowość. Zapoznaj się z [ADO.NET docelowy adapter] [ ADO.NET destination adapter] artykuł, aby dowiedzieć się więcej o tej właściwości

> [!NOTE]
> Dołączanie do usługi Azure SQL Data Warehouse przy użyciu OLEDB nie jest obsługiwane.
> 
> 

Ponadto istnieje możliwość, że pakiet może zakończyć się niepowodzeniem ze względu na problemy z ograniczania lub sieci. Projekt pakietów, ich można wznawiać w punkcie awarii bez ponawianie pracy, który zakończył się przed awarią.

Aby uzyskać więcej informacji, zapoznaj się [dokumentacji SSIS][SSIS documentation].

## <a name="bcp"></a>bcp
Narzędzie bcp jest narzędziem wiersza polecenia, które jest przeznaczone do pliku prostego importowania i eksportowania danych. Niektóre przekształcania może odbywać się podczas eksportowania danych. Do wykonywania prostych transformacji umożliwia zapytania wybierz i przekształcenia danych. Po wyeksportowane, plików prostych może zostać załadowana bezpośrednio do docelowej bazy danych magazynu danych SQL.

> [!NOTE]
> Często jest warto Hermetyzowanie transformacje używane podczas eksportowania danych w widoku w systemie źródłowym. Dzięki temu logika jest zachowywana, a proces jest powtarzalne.
> 
> 

Zalety programu bcp są następujące:

* Prostota. Tworzenie i wykonywanie prostych są polecenia BCP
* Proces ładowania jej ponownie uruchomić. Raz wyeksportowanego obciążenia mogą być wykonywane dowolną liczbę razy

Ograniczenia programu bcp są:

* Narzędzie bcp działa tylko tabelaryczne plików prostych. Nie działa z plikami, takie jak xml lub JSON
* Funkcje przekształcania danych są ograniczone do etapu eksportu tylko i są z natury
* Narzędzie bcp nie został zainstalowany być niezawodne podczas ładowania danych za pośrednictwem Internetu. Niestabilności sieci może spowodować błąd ładowania.
* Narzędzie bcp zależy od schematu jest obecny w docelowej bazie danych przed obciążenia

Aby uzyskać więcej informacji, zobacz [bcp umożliwia ładowanie danych do usługi SQL Data Warehouse][Use bcp to load data into SQL Data Warehouse].

## <a name="optimizing-data-migration"></a>Optymalizacja migracji danych
Proces migracji danych SQLDW mogą skutecznie podzielone na trzy osobne kroki:

1. Eksport źródła danych
2. Transfer danych do platformy Azure
3. Ładowanie do docelowej bazy danych SQLDW

Każdy krok mogą być optymalizowane indywidualnie można utworzyć procesu migracji niezawodny, będzie można ponownie uruchomić systemu i elastyczne, które pozwala zmaksymalizować wydajność w każdym kroku.

## <a name="optimizing-data-load"></a>Optymalizacja ładowania danych
Spojrzenie na ich w kolejności odwrotnej do chwili; jest to najszybszy sposób, aby załadować dane przy użyciu programu PolyBase. Optymalizacja dla proces ładowania PolyBase umieszcza wymagań wstępnych na powyższych kroków, najlepiej to zrozumieć góry. Oto one:

1. Kodowanie plików danych
2. Format plików danych
3. Lokalizacja plików danych

### <a name="encoding"></a>Encoding
Program PolyBase wymaga danych plików UTF-8 lub UTF-16FE. 



### <a name="format-of-data-files"></a>Format plików danych
Program PolyBase ma terminatora stałym wiersza \n lub nowym wierszem. Pliki danych musi być zgodna z tym standard. Nie ma ograniczeń terminatory parametry lub kolumny.

Należy zdefiniować każdej kolumny w pliku jako część tabeli zewnętrznej w PolyBase. Upewnij się, że wymagane są wszystkie kolumny wyeksportowany i że typów ze standardami wymagane.

Sprawdź odwołują się do [migracji schemat] artykułu dla szczegółów na obsługiwane typy danych.

### <a name="location-of-data-files"></a>Lokalizacja plików danych
Usługa SQL Data Warehouse używa PolyBase wyłącznie ładowania danych z magazynu obiektów Blob Azure. W rezultacie dane musi najpierw przekazane do magazynu obiektów blob.

## <a name="optimizing-data-transfer"></a>Optymalizacja transferu danych
Jedna z części najwolniejsze migracji danych jest przesyłanie danych do platformy Azure. Nie tylko przepustowość sieci może być problemem, ale również niezawodność sieci może poważnie utrudniać postępu. Domyślnie migrację danych do platformy Azure jest za pośrednictwem Internetu, więc rozsądnych prawdopodobnie prawdopodobieństwo wystąpienia błędów transferu. Jednak te błędy mogą wymagać danych ponownego wysyłania w całości lub częściowo.

Na szczęście masz kilka opcji zwiększenie szybkości i odporności tego procesu:

### <a name="expressrouteexpressroute"></a>[ExpressRoute][ExpressRoute]
Warto rozważyć użycie [ExpressRoute] [ ExpressRoute] celu przyspieszenia transferu. [ExpressRoute] [ ExpressRoute] zawiera nawiązane połączenie prywatne do platformy Azure, połączenie nie przechodzi przez publicznej sieci internet. W żadnym wypadku nie jest to krok obowiązkowe. Jednak go może zwiększyć przepustowość podczas przekazywanie danych do platformy Azure z lokalnymi lub wspólnej lokalizacji.

Korzyści wynikające ze stosowania [ExpressRoute] [ ExpressRoute] są:

1. Zwiększenie niezawodności
2. Szybsze sieci
3. Mniejsze opóźnienia sieci
4. wyższy poziom zabezpieczeń sieci

[ExpressRoute] [ ExpressRoute] jest przydatne w przypadku różnych scenariuszach; nie tylko migracji.

Chcesz spróbować? Więcej informacji i cenach należy znaleźć [dokumentacja usługi ExpressRoute][ExpressRoute documentation].

### <a name="azure-import-and-export-service"></a>Azure importu i eksportu usługi
Azure importowanie i eksportowanie usługi to proces transferu danych, przeznaczony dla dużych (GB ++) do dużego (TB ++) transferów danych na platformie Azure. Obejmuje ona zapisywania danych na dyskach i wysyłania ich do centrum danych platformy Azure. Zawartość dysku zostaną następnie załadowane na obiektach blob magazynu Azure w Twoim imieniu.

Ogólny widok procesu eksportowania importu jest następujący:

1. Skonfiguruj kontener magazynu obiektów Blob Azure do odbioru danych
2. Eksportuj dane do lokalnego magazynu
3. Kopiuj dane do 3,5 cala SATA II/III dysków twardych za pomocą [narzędzie importu/eksportu Azure]
4. Utwórz zadanie importu za pomocą usługi Azure importu i eksportu usługi, podając pliki dziennika utworzone przez [narzędzie importu/eksportu Azure]
5. Wyślij dyski centrum danych Azure nominalnym
6. Dane są przekazywane do Twojej kontenera magazynu obiektów Blob Azure
7. Ładowanie danych do SQLDW przy użyciu programu PolyBase

### <a name="azcopyazcopy-utility"></a>[Narzędzie AZCopy][Narzędzie AZCopy] narzędzia
[Narzędzie AZCopy][Narzędzie AZCopy] narzędzie to doskonałe narzędzie do pobierania danych przesyłanych w obiektach blob magazynu Azure. Jest on przeznaczony dla małych (MB ++) do bardzo dużych transferów danych (GB ++). [Narzędzie AZCopy] została opracowana również w celu zapewnienia dobrej przepływności elastyczne podczas transferu danych do platformy Azure i dlatego jest doskonałym wyborem kroku transferu danych. Raz przeniesione, można załadować danych do usługi SQL Data Warehouse przy użyciu programu PolyBase. Narzędzia AZCopy można również zastosować w pakietów SSIS za pomocą zadania "Wykonania procesu".

Można użyć narzędzia AZCopy najpierw należy pobrać i zainstalować go. Brak [wersji produkcyjnej] [ production version] i [wersji zapoznawczej] [ preview version] dostępne.

Aby przekazać plik z systemu plików są potrzebne polecenie podobny do poniższego:

```
AzCopy /Source:C:\myfolder /Dest:https://myaccount.blob.core.windows.net/mycontainer /DestKey:key /Pattern:abc.txt
```

Podsumowanie procesu może być:

1. Skonfiguruj kontener obiektu blob magazynu Azure do odbioru danych
2. Eksportuj dane do lokalnego magazynu
3. Narzędzie AZCopy dane w kontenerze magazynu obiektów Blob Azure
4. Ładowanie danych do usługi SQL Data Warehouse przy użyciu programu PolyBase

Pełna dokumentacja: [Narzędzie AZCopy][Narzędzie AZCopy].

## <a name="optimizing-data-export"></a>Optymalizowanie eksportowanie danych
Oprócz zapewniania, że Eksport spełnia wymagania, którego układ określa się w programie PolyBase mogą również wyszukiwać zoptymalizować eksportowania danych w celu zwiększenia procesu dalsze.



### <a name="data-compression"></a>Kompresja danych
Program PolyBase mogą odczytywać dane skompresowane gzip. Jeśli będziesz mieć możliwość kompresowania danych do plików gzip będzie zminimalizować ilość danych przekazywanej za pośrednictwem sieci.

### <a name="multiple-files"></a>Wiele plików
Podzielenie dużych tabel do wielu plików nie tylko pomaga w celu zwiększenia szybkości eksportu, ale także z re-startability transferu, a także ogólne możliwości zarządzania danych raz w magazynie obiektów blob Azure. Jest jedną z wielu funkcji nieuprzywilejowany PolyBase, że wszystkie pliki w folderze odczytuje i traktować go jako jedna tabela. W związku z tym jest dobrym rozwiązaniem, aby wyodrębnić pliki dla każdej tabeli do własnego folderu.

Aparat PolyBase obsługuje również funkcją znana jako "Przechodzenie folderu cykliczne". Ta funkcja służy do zwiększenia organizacji wyeksportowane dane, aby poprawić zarządzanie danych.

Aby dowiedzieć się więcej na temat ładowanie danych przy użyciu programu PolyBase, zobacz [Użyj PolyBase, aby załadować dane do usługi SQL Data Warehouse][Use PolyBase to load data into SQL Data Warehouse].

## <a name="next-steps"></a>Kolejne kroki
Aby uzyskać więcej informacji na temat migracji, zobacz [migracja rozwiązania do usługi SQL Data Warehouse][Migrate your solution to SQL Data Warehouse].
Aby uzyskać więcej porad programistycznych, zobacz [omówienie tworzenia][development overview].

<!--Image references-->

<!--Article references-->
[Narzędzie AZCopy]: ../storage/common/storage-use-azcopy.md
[ADF Copy]: ../data-factory/v1/data-factory-data-movement-activities.md 
[ADF samples]: ../data-factory/v1/data-factory-samples.md
[ADF Copy examples]: ../data-factory/v1/data-factory-copy-activity-tutorial-using-visual-studio.md
[development overview]: sql-data-warehouse-overview-develop.md
[migracji schemat]: sql-data-warehouse-migrate-schema.md
[Migrate your solution to SQL Data Warehouse]: sql-data-warehouse-overview-migrate.md
[SQL Data Warehouse development overview]: sql-data-warehouse-overview-develop.md
[Use bcp to load data into SQL Data Warehouse]: /sql/tools/bcp-utility
[Use PolyBase to load data into SQL Data Warehouse]: load-data-wideworldimportersdw.md


<!--MSDN references-->

<!--Other Web references-->
[Azure Data Factory]: http://azure.microsoft.com/services/data-factory/
[ExpressRoute]: http://azure.microsoft.com/services/expressroute/
[ExpressRoute documentation]: http://azure.microsoft.com/documentation/services/expressroute/

[production version]: http://aka.ms/downloadazcopy/
[preview version]: http://aka.ms/downloadazcopypr/
[ADO.NET destination adapter]: https://msdn.microsoft.com/library/bb934041.aspx
[SSIS documentation]: https://msdn.microsoft.com/library/ms141026.aspx
