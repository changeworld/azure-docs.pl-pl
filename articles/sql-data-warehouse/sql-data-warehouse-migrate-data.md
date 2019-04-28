---
title: Migruj dane do usługi SQL Data Warehouse | Dokumentacja firmy Microsoft
description: Wskazówki dotyczące migrowania danych do usługi Azure SQL Data Warehouse związane z opracowywaniem rozwiązań.
services: sql-data-warehouse
author: jrowlandjones
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: implement
ms.date: 04/17/2018
ms.author: jrj
ms.reviewer: igorstan
ms.openlocfilehash: 6a2acf602252ee4319f9a5eccef53a25d8e2dd7f
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60748190"
---
# <a name="migrate-your-data"></a>Migrowanie danych
Dane mogą zostać przeniesione z różnych źródeł do usługi SQL Data Warehouse za pomocą różnych narzędzi.  Kopiuj ADF, SSIS i bcp wszystkie można na osiągnięcie tego celu. Jak ilość danych zwiększa należy myśleć o potężne proces migracji danych do kroków. Zapewnia możliwość optymalizacji każdego kroku na wydajność i odporność upewnić się, migrację danych smooth.

W tym artykule omówiono najpierw scenariuszy migracji prostego kopiowania usługi ADF, SSIS i bcp. Go następnie głębszą trochę jak można optymalizować migracji.

## <a name="azure-data-factory-adf-copy"></a>Usługa Azure Data Factory (ADF) kopiowania
[Kopiowania usługi ADF] [ ADF Copy] jest częścią [usługi Azure Data Factory][Azure Data Factory]. Można użyć kopiowania usługi ADF, aby wyeksportować dane do plików prostych znajdujących się w magazynie lokalnym, do zdalnego plików prostych przechowywanych w usłudze Azure blob storage lub bezpośrednio do usługi SQL Data Warehouse.

Jeśli danych jest uruchamiany w pliki proste, a następnie należy najpierw przenieść do usługi Azure storage blob przed zainicjowaniem obciążenia go do usługi SQL Data Warehouse. Gdy dane są przesyłane do magazynu obiektów blob platformy Azure możesz używać [kopiowania usługi ADF] [ ADF Copy] ponownie, aby wypchnąć dane do usługi SQL Data Warehouse.

Program PolyBase jest także to rozwiązanie o wysokiej wydajności dla ładowania danych. Oznacza jednak, że za pomocą dwóch narzędzi, zamiast jednego. Jeśli potrzebujesz uzyskać najlepszą wydajność, przy użyciu technologii PolyBase. Jeśli chcesz, aby w środowisku jednego narzędzia (i dane nie są ogromne) usługi ADF jest odpowiedź.

Postępuj zgodnie z [w tym samouczku](../data-factory/load-azure-sql-data-warehouse.md) dowiesz się, jak załadować dane do magazynu danych za pomocą usługi ADF.

## <a name="integration-services"></a>Usługi integracji
Integration Services (SSIS) jest wydajny i elastyczny wyodrębniania, przekształcania i ładowania (ETL) narzędzie, które obsługuje złożone przepływy pracy, przekształcania danych i kilka opcji podczas ładowania danych. Po prostu przesyłanie danych, na platformie Azure lub w ramach szerszej migracji za pomocą usług SSIS.

> [!NOTE]
> SSIS można eksportować do formatu UTF-8 bez znacznika kolejności bajtów w pliku. Aby skonfigurować to należy najpierw użyć składnika kolumny pochodnej można przekonwertować danych znakowych przepływu danych, aby użyć strona kodowa 65001 UTF-8. Po konwersji kolumny zapisać dane adapter miejsca docelowego pliku prostego, zapewniając, że 65001 została również wybrana jako stronę kodową dla pliku.
> 
> 

SSIS łączy z usługą SQL Data Warehouse, tak, jak może zostać nawiązane połączenie wdrożenie programu SQL Server. Jednak połączenia należy korzystać z Menedżera połączeń ADO.NET. Możesz powinien również uważać, aby skonfigurować "Użyj Wstawianie zbiorcze dostępne" ustawienie w celu zmaksymalizowania wydajności. Zapoznaj się [adapter miejsca docelowego ADO.NET] [ ADO.NET destination adapter] artykuł, aby dowiedzieć się więcej na temat tej właściwości

> [!NOTE]
> Łączenie z usługą Azure SQL Data Warehouse przy użyciu OLEDB jest nieobsługiwane.
> 
> 

Ponadto zawsze istnieje możliwość, że pakiet może zakończyć się niepowodzeniem ze względu na problemy ograniczania przepływności lub sieci. Projekt pakietów, więc one może być wznowione punkcie niepowodzenia, bez ponownego wykonywania pracy, który zakończone przed awarią.

Aby uzyskać więcej informacji, zapoznaj się [dokumentacji usług SSIS][SSIS documentation].

## <a name="bcp"></a>bcp
Narzędzie bcp jest narzędziem wiersza polecenia, które jest przeznaczone do plików prostych import i Eksport danych. Niektóre przekształcania może odbywać się podczas eksportowania danych. Aby przeprowadzić proste transformacje, należy użyć zapytania można wybierać i przekształcać dane. Po wyeksportowaniu plików prostych może zostać załadowana bezpośrednio w docelowej bazie danych SQL Data Warehouse.

> [!NOTE]
> Często jest dobry pomysł, aby hermetyzować przekształcenia użyte podczas eksportowania danych w widoku w systemie źródłowym. Gwarantuje to, że logika jest zachowywana, a proces jest powtarzalne.
> 
> 

Zalety programu bcp są następujące:

* Prostota. polecenia BCP są proste tworzenie i wykonywanie
* Proces ładowania będzie można ponownie uruchomić systemu. Raz wyeksportowanego obciążenia mogą być wykonywane dowolną liczbę razy

Ograniczenia bcp są:

* narzędzia BCP w programach tylko tabelaryczne pliki proste. Nie działa z plików, takich jak JSON lub xml
* Możliwości przekształcania danych są ograniczone do tylko przemieszczanie eksportu i łatwo z natury
* Narzędzie bcp nie została dostosowana do być niezawodne, podczas ładowania danych za pośrednictwem Internetu. Niestabilności sieci może spowodować błąd ładowania.
* Narzędzie bcp opiera się na schemat w docelowej bazie danych przed załadowaniem

Aby uzyskać więcej informacji, zobacz [ładowanie danych do usługi SQL Data Warehouse przy użyciu narzędzia bcp][Use bcp to load data into SQL Data Warehouse].

## <a name="optimizing-data-migration"></a>Optymalizacja migracji danych
Proces migracji danych SQLDW mogą skutecznie podzielone na trzy osobne kroki:

1. Eksportowanie danych źródłowych
2. Transfer danych do platformy Azure
3. Ładowanie do docelowej bazy danych SQLDW

Każdy krok można indywidualnie zoptymalizować można utworzyć procesu migracji niezawodne, będzie można ponownie uruchomić systemu i odporne na błędy, które pozwala zmaksymalizować wydajność na każdym kroku.

## <a name="optimizing-data-load"></a>Optymalizowanie ładowania danych
Spojrzenie na te w odwrotnej kolejności na chwilę; jest to najszybszy sposób ładowania danych za pomocą programu PolyBase. Optymalizowanie procesu obciążenia funkcji PolyBase umieszcza wymagań wstępnych w poprzednich krokach, zaleca się to zrozumieć ponoszonych z góry. Oto one:

1. Kodowanie plików danych
2. Format plików danych
3. Lokalizacja plików danych

### <a name="encoding"></a>Kodowanie
Program PolyBase wymaga danych plików UTF-8 lub UTF-16FE. 



### <a name="format-of-data-files"></a>Format plików danych
Program PolyBase określającemu terminatora wiersza stałej z \n lub nowym wierszem. Pliki danych musi być zgodna z tym standardem. Nie ma ograniczeń terminatory parametry lub kolumny.

Należy zdefiniować każda kolumna w pliku jako część swojej tabeli zewnętrznej w technologii PolyBase. Upewnij się, że wymagane są wszystkie kolumny wyeksportowanego i czy typy są zgodne ze standardami wymagane.

Można wrócić do [Migrowanie schematu] artykuł dotyczący szczegółowych informacji dotyczących obsługiwanych typów danych.

### <a name="location-of-data-files"></a>Lokalizacja plików danych
SQL Data Warehouse używa programu PolyBase do ładowania danych z usługi Azure Blob Storage wyłącznie. W związku z tym dane muszą najpierw przeniesiono do magazynu obiektów blob.

## <a name="optimizing-data-transfer"></a>Optymalizacja transfer danych
Jedna z części najwolniejsze migracji danych jest przesyłanie danych do platformy Azure. Nie tylko przepustowość sieci może być problemem, ale także niezawodność sieci naszych użytkowników bardzo poważnie mogą utrudniać postępy. Domyślnie migrację danych do platformy Azure, dzięki czemu jest prawdopodobnie prawdopodobieństwo wystąpienia błędów transferu jest za pośrednictwem Internetu. Jednak te błędy mogą wymagać danych do ponownego wysłania w całości lub części.

Na szczęście dostępnych jest kilka zwiększenie szybkości i odporności na błędy tego procesu:

### <a name="expressrouteexpressroute"></a>[ExpressRoute][ExpressRoute]
Warto rozważyć użycie [ExpressRoute] [ ExpressRoute] aby przyspieszyć transfer. [Usługa ExpressRoute] [ ExpressRoute] zapewnia nawiązane połączenie prywatne na platformie Azure, połączenie nie przechodzi przez publiczny internet. W żadnym wypadku nie jest to krok obowiązkowy. Jednak go może zwiększyć przepustowość podczas wypychania danych do platformy Azure ze środowiska lokalnego lub kolokacji.

Korzyści z używania [ExpressRoute] [ ExpressRoute] są:

1. Większa niezawodność
2. Szybsze sieci
3. Mniejsze opóźnienie sieci
4. lepsze zabezpieczenia sieci

[Usługa ExpressRoute] [ ExpressRoute] jest korzystne dla różnych scenariuszy; nie tylko migracji.

Chcesz spróbować? Więcej informacji i cenach, odwiedź stronę [dokumentacja usługi ExpressRoute][ExpressRoute documentation].

### <a name="azure-import-and-export-service"></a>Usługa Azure Import i eksport usługi
Azure importu i eksportu usługi to proces transferu danych przeznaczona dla dużych (GB ++) do ogromnych (TB ++) transferu danych na platformę Azure. Obejmuje to zapisywanie danych na dyskach i wysyłania ich do centrum danych platformy Azure. Zawartość dysku zostaną następnie załadowane do obiektów blob usługi Azure Storage w Twoim imieniu.

Ogólny widok procesu eksportu importowania jest następująca:

1. Konfigurowanie kontenera magazynu obiektów Blob Azure do odbierania danych
2. Eksportuj dane do magazynu lokalnego
3. Kopiuj dane do 3,5 cala SATA II/III dysków twardych za pomocą [narzędziem Azure Import/Export]
4. Tworzenie zadania importu za pomocą usługi Azure importu i eksportu usługi, podając pliki dziennika produkowane przez [narzędziem Azure Import/Export]
5. Odeślij dyski z centrum danych platformy Azure wyznaczone
6. Dane są przekazywane do kontenera usługi Azure Blob Storage
7. Załaduj dane do SQLDW przy użyciu technologii PolyBase

### <a name="azcopyazcopy-utility"></a>[Narzędzie AZCopy][narzędzie azcopy] narzędzia
[narzędzie azcopy][narzędzie azcopy] narzędzie to doskonałe narzędzie do pobierania danych przesyłanych do obiektów blob usługi Azure Storage. Jest przeznaczona dla małych (MB ++) do bardzo dużych transferów danych (GB ++). [Narzędzie AZCopy] również została zaprojektowana w celu zapewnienia dobrej przepływności odporne na błędy podczas przesyłania danych do platformy Azure i dlatego jest to dobry wybór dla kroku transferu danych. Raz przeniesione, można załadować danych do usługi SQL Data Warehouse przy użyciu technologii PolyBase. Narzędzia AZCopy można także dołączać do pakietów SSIS za pomocą zadania "Wykonania procesu".

Korzystać z AZCopy najpierw musisz pobrać i zainstalować go. Brak [wersja produkcyjna] [ production version] i [w wersji zapoznawczej] [ preview version] dostępne.

Aby przekazać plik z systemu plików są potrzebne polecenia podobnego do poniższego:

```
AzCopy /Source:C:\myfolder /Dest:https://myaccount.blob.core.windows.net/mycontainer /DestKey:key /Pattern:abc.txt
```

Podsumowanie wysokiego poziomu procesu może być:

1. Konfigurowanie kontenera obiektów blob usługi Azure storage do odbierania danych
2. Eksportuj dane do magazynu lokalnego
3. Narzędzie AZCopy dane w kontenerze usługi Azure Blob Storage
4. Ładowanie danych do usługi SQL Data Warehouse przy użyciu technologii PolyBase

Pełną dokumentację dostępnych: [Narzędzie AZCopy][narzędzie azcopy].

## <a name="optimizing-data-export"></a>Optymalizacja Eksport danych
Oprócz zapewniania, że Eksport jest zgodny z wymogami rozmieszczony w programie PolyBase można również wyszukać zoptymalizować eksportowania danych do usprawniania procesu dalsze.



### <a name="data-compression"></a>Kompresja danych
Program PolyBase może odczytywać dane skompresowane w formacie gzip. Jeśli jest to możliwe kompresować dane, aby pliki gzip będzie zminimalizować ilość danych wypychania za pośrednictwem sieci.

### <a name="multiple-files"></a>Wiele plików
Podzielenie dużych tabel do wielu plików nie tylko pomaga zwiększyć szybkość eksportu, ale także z re-startability transferu i ogólne możliwości zarządzania danych raz w usłudze Azure blob storage. Jest jedną z wielu funkcji nieuprzywilejowany PolyBase, że odczytuje wszystkie pliki znajdujące się w folderze i jej traktowała jako jedna tabela. W związku z tym jest dobry pomysł, aby odizolować pliki dla każdej tabeli w jego własnym folderze.

Aparat PolyBase obsługuje również funkcje znane jako "Przechodzenie folderu cyklicznego". Tej funkcji można użyć w celu dodatkowego zwiększenia organizacji wyeksportowane dane, aby poprawić zarządzanie danych.

Aby dowiedzieć się więcej na temat ładowania danych przy użyciu technologii PolyBase, zobacz [przy użyciu technologii PolyBase do ładowania danych do usługi SQL Data Warehouse][Use PolyBase to load data into SQL Data Warehouse].

## <a name="next-steps"></a>Kolejne kroki
Aby uzyskać więcej informacji o migracji, zobacz [Migrowanie rozwiązania do usługi SQL Data Warehouse][Migrate your solution to SQL Data Warehouse].
Aby uzyskać więcej porad programistycznych, zobacz [omówienie programowania w usłudze][development overview].

<!--Image references-->

<!--Article references-->
[Narzędzie AzCopy]: ../storage/common/storage-use-azcopy.md
[ADF Copy]: ../data-factory/load-azure-sql-data-warehouse.md 
[ADF Copy examples]: ../data-factory/quickstart-create-data-factory-dot-net.md
[development overview]: sql-data-warehouse-overview-develop.md
[Migrowanie schematu]: sql-data-warehouse-migrate-schema.md
[Migrate your solution to SQL Data Warehouse]: sql-data-warehouse-overview-migrate.md
[SQL Data Warehouse development overview]: sql-data-warehouse-overview-develop.md
[Use bcp to load data into SQL Data Warehouse]: /sql/tools/bcp-utility
[Use PolyBase to load data into SQL Data Warehouse]: load-data-wideworldimportersdw.md


<!--MSDN references-->

<!--Other Web references-->
[Azure Data Factory]: https://azure.microsoft.com/services/data-factory/
[ExpressRoute]: https://azure.microsoft.com/services/expressroute/
[ExpressRoute documentation]: https://azure.microsoft.com/documentation/services/expressroute/

[production version]: https://aka.ms/downloadazcopy/
[preview version]: https://aka.ms/downloadazcopypr/
[ADO.NET destination adapter]: https://msdn.microsoft.com/library/bb934041.aspx
[SSIS documentation]: https://msdn.microsoft.com/library/ms141026.aspx
