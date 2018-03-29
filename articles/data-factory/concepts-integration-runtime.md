---
title: Infrastruktura Integration Runtime w usłudze Azure Data Factory | Microsoft Docs
description: Więcej informacji na temat infrastruktury Integration Runtime w usłudze Azure Data Factory.
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 01/22/2018
ms.author: shlo
ms.openlocfilehash: 88475c169bd6cc8fc4d653801ec1af58ebc7620e
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/23/2018
---
# <a name="integration-runtime-in-azure-data-factory"></a>Infrastruktura Integration Runtime w usłudze Azure Data Factory
Integration Runtime (IR) to infrastruktura obliczeniowa używana przez usługę Azure Data Factory do zapewnienia następujących możliwości integracji danych w różnych środowiskach sieciowych:

- **Przenoszenie danych**: przenoszenie danych między magazynami danych w sieci publicznej i magazynami danych w sieci prywatnej (lokalnej lub prywatnej sieci wirtualnej). Zapewnia obsługę wbudowanych łączników, konwersji formatów i mapowania kolumn oraz wydajne i skalowalne przenoszenie danych.
- **Wysyłanie działań**: wysyłanie i monitorowanie działań przekształcania uruchamianych w różnych usługach obliczeniowych, takich jak Azure HDInsight, Azure Machine Learning, Azure SQL Database, SQL Server i nie tylko.
- **Wykonanie pakietów SSIS**: natywne wykonywanie pakietów SQL Server Integration Services (SSIS) w zarządzanym środowisku obliczeniowym platformy Azure.


> [!NOTE]
> Ten artykuł dotyczy wersji 2 usługi Data Factory, która jest obecnie dostępna w wersji zapoznawczej. Jeśli używasz dostępnej ogólnie wersji 1 usługi Data Factory, zobacz [Data Factory version 1 documentation (Dokumentacja usługi Data Factory w wersji 1)](v1/data-factory-introduction.md).

W usłudze Data Factory działanie definiuje akcję do wykonania. Połączona usługa definiuje docelowy magazyn danych lub usługę obliczeniową. Infrastruktura Integration Runtime zapewnia połączenie między działaniem i połączonymi usługami.  Odwołuje się do niej połączona usługa i zapewnia środowisko obliczeniowe, w którym działanie jest uruchamiane lub z którego jest wysyłane.  Dzięki temu działanie można wykonać w regionie najbliższym docelowemu magazynowi danych lub usłudze obliczeniowej, w sposób najbardziej wydajny, jednocześnie spełniając wymagania dotyczące zabezpieczeń i zgodności.

## <a name="integration-runtime-types"></a>Typy infrastruktury Integration Runtime
Usługa Data Factory oferuje trzy typy infrastruktury Integration Runtime. Należy wybrać typ najlepiej odpowiadający poszukiwanym możliwościom integracji danych i potrzebom środowiska sieciowego.  Te trzy typy to:

- Azure
- Samodzielny hosting
- Azure-SSIS

W poniższej tabeli opisano możliwości i obsługę sieci dla każdego typu infrastruktury Integration Runtime:

Typ IR | Sieć publiczna | Sieć prywatna
------- | -------------- | ---------------
Azure | Przenoszenie danych<br/>Wysyłanie działania | &nbsp;
Samodzielny hosting | Przenoszenie danych<br/>Wysyłanie działania | Przenoszenie danych<br/>Wysyłanie działania
Azure-SSIS | Wykonanie pakietu SSIS | Wykonanie pakietu SSIS

Na poniższym diagramie przedstawiono, jak różnych infrastruktur Integration Runtime można użyć w sposób połączony, aby zapewnić bogate możliwości integracji danych i obsługę sieci:

![Różne typy infrastruktur Integration Runtime](media\concepts-integration-runtime\different-integration-runtimes.png)


## <a name="azure-integration-runtime"></a>Środowisko uruchomieniowe integracji Azure
Infrastruktura Azure Integration Runtime zapewnia następujące funkcje:

- Uruchamianie działania kopiowania między magazynami danych w chmurze
- Wysyłanie następujących działań przekształcania w sieci publicznej: działanie HDInsight Hive, działanie HDInsight Pig, działanie HDInsight MapReduce, działanie HDInsight Spark, działanie HDInsight Streaming, działanie Machine Learning Batch Execution, działanie Machine Learning Update Resource, działanie Stored Procedure, działanie Data Lake Analytics U-SQL, działanie niestandardowe platformy .Net, działanie internetowe, działanie Lookup i działanie Get Metadata.

### <a name="network-environment"></a>Środowisko sieciowe
Infrastruktura Azure Integration Runtime obsługuje łączenie z magazynami danych i usługami obliczeniowymi w sieci publicznej przy użyciu dostępnych publicznie punktów końcowych. Zastosowanie infrastruktury Integration Runtime (Self-hosted) w środowisku sieci wirtualnej Azure.

### <a name="compute-resource-and-scaling"></a>Zasoby obliczeniowe i skalowanie
Infrastruktura Integration Runtime zapewnia w pełni zarządzane obliczenia bez serwera na platformie Azure.  Nie musisz przejmować się udostępnianiem infrastruktury, instalacją oprogramowania, poprawkami i skalowaniem możliwości.  Dodatkowo płacisz tylko za czas rzeczywistego wykorzystania.

Produkt Azure Integration Runtime zapewnia natywne możliwości obliczeniowe przenoszenia danych między magazynami danych w chmurze w sposób bezpieczny, niezawodny i wydajny.  Możesz określić liczbę jednostek przenoszenia danych używanych w działaniu kopiowania, a rozmiar obliczeń infrastruktury Azure IR jest skalowany elastycznie bez konieczności wyraźnego regulowania rozmiaru infrastruktury Azure Integration Runtime.

Wysyłanie działania to lekka operacja, kierująca działanie do docelowej usługi obliczeniowej, dzięki czemu nie występuje konieczności skalowania rozmiaru obliczeń dla tego scenariusza.

Informacje na temat tworzenia i konfigurowania infrastruktury Azure IR można znaleźć w przewodniku How to create and configure Azure IR (Jak utworzyć i skonfigurować infrastrukturę Azure IR). 

## <a name="self-hosted-integration-runtime"></a>Infrastruktura Integration Runtime (Self-hosted)
Infrastruktura IR (Self-hosted) oferuje następujące możliwości:

- Uruchamianie działania kopiowania między magazynami danych w chmurze i magazynem danych w sieci prywatnej.
- Wysyłanie następujących działań przekształcania dla zasobów obliczeniowych w sieci lokalnej lub sieci wirtualnej platformy Azure: działanie HDInsight Hive (BYOC), działanie HDInsight Pig (BYOC), działanie HDInsight MapReduce (BYOC), działanie HDInsight Spark (BYOC), działanie HDInsight Streaming (BYOC), działanie Machine Learning Batch Execution, działanie Machine Learning Update Resource, działanie Stored Procedure, działanie Data Lake Analytics U-SQL, działanie niestandardowe platformy .Net, działanie Lookup i działanie Get Metadata.

> [!NOTE] 
> Infrastruktura Integration Runtime (Self-hosted) umożliwia obsługę magazynów danych wymagających własnych sterowników, takich jak SAP Hana, MySQL itp.  Więcej informacji można znaleźć w sekcji dotyczącej [obsługiwanych magazynów danych](copy-activity-overview.md#supported-data-stores-and-formats).

### <a name="network-environment"></a>Środowisko sieciowe
Jeśli chcesz przeprowadzić integrację danych bezpiecznie w środowisku sieci prywatnej, która nie ma bezpośredniego połączenia ze środowiskiem chmury publicznej, możesz zainstalować hostowane samodzielnie, lokalne środowisko IR za zaporą firmową lub w wirtualnej sieci prywatnej.  Infrastruktura Integration Runtime (Self-hosted) wykonuje tylko wychodzące połączenia HTTP do otwartej sieci Internet.

### <a name="compute-resource-and-scaling"></a>Zasoby obliczeniowe i skalowanie
Infrastrukturę Integration Runtime (Self-hosted) należy zainstalować na maszynie lokalnej lub maszynie wirtualnej w sieci prywatnej. Aktualnie obsługujemy uruchamianie środowisk IR (Self-hosted) w systemie operacyjnym Windows.  

W celu zapewnienia wysokiej dostępności i skalowalności można zmienić skalowanie środowiska IR (Self-hosted), łącząc wystąpienie logiczne z wieloma maszynami lokalnymi w trybie aktywny-aktywny.  Więcej informacji można znaleźć w artykule przewodnika How to create and configure self-hosted IR (Jak utworzyć i skonfigurować samodzielnie hostowane środowisko IR).

## <a name="azure-ssis-integration-runtime"></a>Infrastruktura Azure SSIS Integration Runtime
Aby zmniejszyć i przenieść obecne obciążenie SSIS, można utworzyć środowisko IR Azure-SSIS w celu natywnego wykonywania pakietów SSIS.

### <a name="network-environment"></a>Środowisko sieciowe
Środowisko IR Azure-SSIS można udostępnić w sieci publicznej lub prywatnej.  Dostęp do danych lokalnych jest obsługiwany przez połączenie środowiska IR Azure-SSIS z siecią wirtualną połączoną z siecią lokalną.  

### <a name="compute-resource-and-scaling"></a>Zasoby obliczeniowe i skalowanie
Azure-SSIS IR to w pełni zarządzany klaster maszyn wirtualnych platformy Azure, których przeznaczeniem jest uruchamianie pakietów SSIS. Można użyć własnego serwera bazy danych Azure SQL Database lub wystąpienia zarządzanego (prywatna wersja zapoznawcza) do hostowania katalogu projektów SSIS/pakietów (SSISDB), który zostanie do niego przyłączony. Możesz skalować moc obliczeniową, określając rozmiar węzłów, a także liczbę węzłów w klastrze. Kosztem pracy produktu Azure-SSIS Integration Runtime można zarządzać, uruchamiając go i zatrzymując zgodnie z potrzebami.

Więcej informacji można znaleźć w artykule przewodnika How to create and configure Azure-SSIS IR (Jak utworzyć i skonfigurować środowisko Azure-SSIS IR).  Po utworzeniu możesz wdrażać istniejące pakiety SSIS i zarządzać nimi praktycznie bez zmian, przy użyciu znanych narzędzi, takich jak SQL Server Data Tools (SSDT) i SQL Server Management Studio (SSMS), co przypomina lokalne zastosowanie SSIS.

Aby uzyskać więcej informacji o środowisku uruchomieniowym Azure-SSIS, zobacz następujące artykuły: 

- [Samouczek: Wdrażanie pakietów usług SSIS na platformie Azure](tutorial-create-azure-ssis-runtime-portal.md). Ten artykuł zawiera szczegółowe instrukcje dotyczące tworzenia środowiska Azure-SSIS IR i używania bazy danych Azure SQL do hostowania wykazu usług SSIS. 
- [How to: Create an Azure-SSIS integration runtime (Jak: Tworzenie środowiska Azure SSIS Integration Runtime)](create-azure-ssis-integration-runtime.md). Ten artykuł stanowi rozszerzenie samouczka i zawiera instrukcje dotyczące używania wystąpienia zarządzanego Azure SQL (prywatna wersja zapoznawcza) i dołączania środowiska IR do sieci wirtualnej. 
- [Monitor an Azure-SSIS IR (Monitorowanie środowiska Azure-SSIS IR)](monitor-integration-runtime.md#azure-ssis-integration-runtime). W tym artykule przedstawiono sposób pobierania informacji o środowisku Azure-SSIS IR i opisów stanów w pobranych informacjach. 
- [Manage an Azure-SSIS IR (Zarządzanie środowiskiem Azure-SSIS IR)](manage-azure-ssis-integration-runtime.md). W tym artykule przedstawiono sposób zatrzymywania, uruchamiania lub usuwania środowiska Azure-SSIS IR. Zawiera on również instrukcje skalowania środowiska Azure-SSIS IR do wewnątrz za pomocą dodawania do niego węzłów. 
- [Join an Azure-SSIS IR to a VNet (Dołączanie środowiska Azure-SSIS IR do sieci wirtualnej)](join-azure-ssis-integration-runtime-virtual-network.md). Ten artykuł zawiera podstawowe informacje na temat dołączania środowiska Azure-SSIS IR do sieci wirtualnej platformy Azure. Opisano w nim kroki konfigurowania sieci wirtualnej za pomocą witryny Azure Portal tak, aby umożliwić dołączanie środowiska Azure-SSIS IR do sieci wirtualnej. 

## <a name="determining-which-ir-to-use"></a>Wybór środowiska IR do użycia
Każde działanie przekształcania zawiera docelową obliczeniową usługę połączoną, która wskazuje infrastrukturę Integration Runtime. To wystąpienie infrastruktury Integration Runtime jest miejscem, z którego wysyłane jest działanie przekształcania.

Działanie kopiowania wymaga połączonych usług źródła i ujścia w celu zdefiniowania kierunku przepływu danych. Poniższa logika jest stosowana do określenia, które wystąpienie środowiska IR jest używane do wykonania kopii: 

- **Kopiowanie między dwoma źródłami danych w chmurze**: gdy połączone usługi źródła i ujścia korzystają ze środowiska Azure IR, środowisko uruchomieniowe integracji używane przez połączoną usługę ujścia jest używane do wykonania działania kopiowania.
- **Kopiowanie między źródłem danych w chmurze i źródłem danych w sieci prywatnej**: jeśli połączona usługa źródła lub ujścia wskazuje środowisko IR (Self-hosted), działanie kopiowania jest wykonywane w tym środowisku IT (Self-hosted).
- **Kopiowanie między dwoma źródłami danych w sieci prywatnej**: usługa połączona źródła i ujścia musi wskazywać to samo wystąpienie produktu Integration Runtime, które jest także wykorzystywane do wykonania działania kopiowania.

Na poniższym diagramie przedstawiono dwa przykłady działania kopiowania:

- W przypadku działania kopiowania 1 źródłem jest połączona usługa programu SQL Server, odwołująca się do samodzielnie hostowanego środowiska IR A, a jego ujściem jest połączona usługa magazynu Azure Storage, odwołująca się do środowiska Azure IR B. Po uruchomieniu działania kopiowania jest ono wykonywane na samodzielnie hostowanym środowisku IR A.
- W przypadku działania kopiowania 2 źródłem jest połączona usługa bazy danych Azure SQL Database, odwołująca się do środowiska IR Azure C, a ujściem jest połączona usługa Azure Storage odwołująca się do środowiska IR Azure B. Po uruchomieniu działania kopiowania jest ono wykonywane na środowisku IR Azure B, ponieważ jest to środowisko uruchomieniowe integracji używane przez połączoną usługę ujścia.

![Które środowisko IR wybrać](media/concepts-integration-runtime/which-integration-runtime-to-use.png)

## <a name="integration-runtime-location"></a>Lokalizacja środowiska Integration Runtime
Lokalizacja usługi Data Factory to miejsce, w którym przechowywane są metadane fabryki danych i gdzie inicjowane jest wyzwalanie potoku. Obecnie obsługiwane lokalizacje usługi Data Factory to Wschodnie stany USA, Wschodnie stany USA 2, Azja Południowo-Wschodnia i Europa Zachodnia. Jednak fabryka danych może mieć dostęp do magazynów danych i usług obliczeniowych w innych regionach świadczenia usługi Azure, aby przenosić dane między magazynami danych lub przetwarzać dane przy użyciu usług obliczeniowych. Zachowanie to jest wykonywane przez środowisko IR dostępne globalnie w wielu regionach, co zapewnia zgodność danych, wydajność i niższe koszty wyjścia z sieci.

Lokalizacja IR definiuje lokalizację zaplecza obliczeniowego i w praktyce lokalizację, w której wykonywane jest przenoszenie danych, wysyłanie działania i wykonywanie pakietu SSIS. Lokalizacja IR może różnić się od lokalizacji fabryki danych, do której należy. Na poniższym diagramie przedstawiono ustawienia lokalizacji usługi Data Factory i jej czasy uruchomienia integracji:

![Lokalizacja środowiska Integration Runtime](media/concepts-integration-runtime/integration-runtime-location.png)

### <a name="azure-ir"></a>Azure IR
Usługa Data Factory używa środowiska Azure IR w regionie najbliższym ujścia w tej samej lokalizacji geograficznej do przeniesienia danych. Mapę można znaleźć w poniższej tabeli:

Lokalizacja geograficzna magazynu danych ujścia | Lokalizacja magazynu danych ujścia | Lokalizacja używana dla środowiska Azure Integration Runtime
-------------------------------| ----------------| ------------------
Stany Zjednoczone | Wschodnie stany USA | Wschodnie stany USA
&nbsp; | Wschodnie stany USA 2 | Wschodnie stany USA 2
&nbsp; | Środkowe stany USA | Środkowe stany USA
&nbsp; | Środkowo-północne stany USA | Środkowo-północne stany USA
&nbsp; | Środkowo-południowe stany USA | Środkowo-południowe stany USA
&nbsp; | Środkowo-zachodnie stany USA | Środkowo-zachodnie stany USA
&nbsp; | Zachodnie stany USA | Zachodnie stany USA
&nbsp; | Zachodnie stany USA 2 | Zachodnie stany USA 2
Kanada | Kanada Wschodnia | Kanada Środkowa
&nbsp; | Kanada Środkowa | Kanada Środkowa
Brazylia | Brazylia Południowa | Brazylia Południowa
Europa | Europa Północna | Europa Północna
&nbsp; | Europa Zachodnia | Europa Zachodnia
Wielka Brytania | Zachodnie Zjednoczone Królestwo | Południowe Zjednoczone Królestwo
&nbsp; | Południowe Zjednoczone Królestwo | Południowe Zjednoczone Królestwo
Azja i Pacyfik | Azja Południowo-Wschodnia | Azja Południowo-Wschodnia
&nbsp; | Azja Wschodnia | Azja Południowo-Wschodnia
Australia | Australia Wschodnia | Australia Wschodnia
&nbsp; | Australia Południowo-Wschodnia | Australia Południowo-Wschodnia
Japonia | Japonia Wschodnia | Japonia Wschodnia
&nbsp; | Japonia Zachodnia | Japonia Wschodnia
Korea | Korea Środkowa | Korea Środkowa
&nbsp; | Korea Południowa | Korea Środkowa
Indie | Indie Środkowe | Indie Środkowe
&nbsp; | Indie Zachodnie | Indie Środkowe
&nbsp; | Indie Południowe | Indie Środkowe

Można również ustawień lokalizację środowiska Azure IR na automatyczne określanie. W takim przypadku usługa Data Factory stara się automatycznie wykryć najlepszą lokalizację do użycia, na podstawie definicji usługi połączonej.

> [!NOTE] 
> Jeśli region docelowego magazynu danych nie znajduje się na liście lub nie można go wykryć, ze względu na wymagania zgodności działanie kończy się niepowodzeniem, zamiast przejść przez inny region. W takim przypadku należy wyraźnie wskazać alternatywną lokalizację do użycia w celu wykonania kopii.
 
Na poniższym rysunku przedstawiono przykład lokalizacji wynikowej, jeśli lokalizacja środowiska Azure IR została ustawiona na określanie automatyczne. W przypadku wykonania działania kopiowania wykrywa ona lokalizację miejsca docelowego danych, w tym przypadku jest to Japonia Zachodnia.  Zgodnie z tabelą do wykonania rzeczywistego kopiowania danych używane jest środowisko Azure IR w regionie Japonia Wschodnia. W przypadku użycia tego samego środowiska IR do połączenia z usługą HDInsight dla działania Spark, przesłanie aplikacji Spark następuje z lokalizacji usługi Data Factory (w tym przypadku są to Wschodnie stanu USA), a rzeczywiste wykonanie aplikacji Spark następuje w lokalizacji serwera HDInsight. 

![Lokalizacja wynikowa](media/concepts-integration-runtime/effective-location.png)

### <a name="self-hosted-ir"></a>Środowisko IR (Self-hosted)
Środowisko IR (Self-hosted) jest rejestrowane logicznie w usłudze Data Factory, a zasoby obliczeniowe wspierające jego funkcjonalność zapewnia użytkownik. W związku z tym nie istnieje wyraźna właściwość lokalizacji środowiska IR (Self-hosted). 

W przypadku zastosowania do wykonania przenoszenia danych, środowisko IR (Self-hosted) pobiera dane ze źródła i zapisuje je w miejscu docelowym.

### <a name="azure-ssis-ir"></a>Środowisko IR Azure-SSIS
Wybór odpowiedniej lokalizacji dla środowiska IR Azure-SSIS jest kluczowy dla osiągnięcia wysokiej wydajności obciążeń wyodrębnianie-przekształcanie-ładowanie (ETL).  Wersja zapoznawcza jest początkowo dostępna w sześciu lokalizacjach (Wschodnie stany USA, Wschodnie stany USA 2, Środkowe stany USA, Australia Wschodnia, Europa Północna i Europa Zachodnia).

- Lokalizacja środowiska IR Azure-SSIS nie musi być identyczna z lokalizacją fabryki danych, ale powinna być identyczna z lokalizacją serwera bazy danych Azure SQL Database/wystąpienia zarządzanego (prywatna wersja zapoznawcza), na którym będzie hostowana baza SSISDB. Dzięki temu środowisko IR Azure-SSIS może z łatwością uzyskać dostęp do bazy SSISDB bez wytwarzania dużego ruchu między różnymi lokalizacjami.
- Jeśli nie masz istniejącego serwera bazy danych Azure SQL Database/wystąpienia zarządzanego (prywatna wersja zapoznawcza) do hostowania bazy SSISDB, ale dysponujesz lokalnymi źródłami/miejscami docelowymi danych, należy utworzyć nowy serwer bazy danych Azure SQL Database/wystąpienia zarządzanego (prywatna wersja zapoznawcza) w tej samej lokalizacji sieci wirtualnej podłączonej do sieci lokalnej.  Dzięki temu możesz utworzyć swoje środowisko IR Azure-SSIS przy użyciu nowego serwera bazy danych Azure SQL Database/wystąpienia zarządzanego (prywatna wersja zapoznawcza) i dołączyć do tej sieci wirtualnej w jednej lokalizacji, co minimalizuje przenoszenie danych między różnymi lokalizacjami.
- Jeśli lokalizacja istniejącego serwera bazy danych Azure SQL Database/wystąpienia zarządzanego (prywatna wersja zapoznawcza), na którym hostowana jest baza SSISDB, nie jest identyczna z lokalizacją sieci wirtualnej połączonej z siecią lokalną, należy najpierw utworzyć środowisko IR Azure-SSIS przy użyciu istniejącego serwera bazy danych Azure SQL Database/wystąpienia zarządzanego (prywatna wersja zapoznawcza) i połączyć się z inną siecią wirtualną w tej samej lokalizacji, a następnie skonfigurować połączenie między sieciami wirtualnymi w różnych lokalizacjach.


## <a name="next-steps"></a>Następne kroki
Zobacz następujące artykuły:

- [Create self-hosted integration runtime (Tworzenie środowiska Integration Runtime (Self-hosted)](create-self-hosted-integration-runtime.md)
- [Create an Azure-SSIS integration runtime (Tworzenie środowiska Azure-SSIS Integration Runtime)](create-azure-ssis-integration-runtime.md) Ten artykuł stanowi rozszerzenie samouczka i zawiera instrukcje dotyczące używania wystąpienia zarządzanego Azure SQL (prywatna wersja zapoznawcza) i dołączania środowiska IR do sieci wirtualnej. 
