---
title: Infrastruktura Integration Runtime w usłudze Azure Data Factory
description: Więcej informacji na temat infrastruktury Integration Runtime w usłudze Azure Data Factory.
services: data-factory
documentationcenter: ''
author: nabhishek
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 05/31/2019
ms.author: abnarain
ms.openlocfilehash: 0b137edbfb5ca439d4ba15614225ec0973511763
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/20/2019
ms.locfileid: "74218808"
---
# <a name="integration-runtime-in-azure-data-factory"></a>Infrastruktura Integration Runtime w usłudze Azure Data Factory
Integration Runtime (IR) to infrastruktura obliczeniowa używana przez usługę Azure Data Factory do zapewnienia następujących możliwości integracji danych w różnych środowiskach sieciowych:

- **Przepływ danych**: wykonywanie [przepływu danych](concepts-data-flow-overview.md) w zarządzanym środowisku obliczeniowym platformy Azure.  
- **Przenoszenie danych**: Skopiuj dane między magazynami danych w sieci publicznej i magazynami danych w sieci prywatnej (lokalnej lub wirtualnej sieci prywatnej). Zapewnia obsługę wbudowanych łączników, konwersji formatów i mapowania kolumn oraz wydajne i skalowalne przenoszenie danych.
- **Wysyłanie działań**: wysyłanie i monitorowanie działań przekształcania działających na różnych usługach obliczeniowych, takich jak Azure Databricks, Azure HDInsight, Azure Machine Learning, Azure SQL Database, SQL Server i innych.
- **Wykonanie pakietów SSIS**: natywne wykonywanie pakietów SQL Server Integration Services (SSIS) w zarządzanym środowisku obliczeniowym platformy Azure.

W usłudze Data Factory działanie definiuje akcję do wykonania. Połączona usługa definiuje docelowy magazyn danych lub usługę obliczeniową. Infrastruktura Integration Runtime zapewnia połączenie między działaniem i połączonymi usługami.  Jest przywoływana przez połączoną usługę lub działanie i udostępnia środowisko obliczeniowe, w którym działanie jest uruchamiane lub wysyłane z programu. Dzięki temu działanie można wykonać w regionie najbliższym docelowemu magazynowi danych lub usłudze obliczeniowej, w sposób najbardziej wydajny, jednocześnie spełniając wymagania dotyczące zabezpieczeń i zgodności.

## <a name="integration-runtime-types"></a>Typy infrastruktury Integration Runtime
Usługa Data Factory oferuje trzy typy infrastruktury Integration Runtime. Należy wybrać typ najlepiej odpowiadający poszukiwanym możliwościom integracji danych i potrzebom środowiska sieciowego.  Te trzy typy to:

- Azure
- Samodzielny hosting
- Azure-SSIS

W poniższej tabeli opisano możliwości i obsługę sieci dla każdego typu infrastruktury Integration Runtime:

Typ IR | Sieć publiczna | Sieć prywatna
------- | -------------- | ---------------
Azure | Przepływ danych<br/>Przenoszenie danych<br/>Wysyłanie działania | &nbsp;
Samodzielny hosting | Przenoszenie danych<br/>Wysyłanie działania | Przenoszenie danych<br/>Wysyłanie działania
Azure-SSIS | Wykonanie pakietu SSIS | Wykonanie pakietu SSIS

Na poniższym diagramie przedstawiono, jak różnych infrastruktur Integration Runtime można użyć w sposób połączony, aby zapewnić bogate możliwości integracji danych i obsługę sieci:

![Różne typy infrastruktur Integration Runtime](media/concepts-integration-runtime/different-integration-runtimes.png)

## <a name="azure-integration-runtime"></a>Azure Integration Runtime
Infrastruktura Azure Integration Runtime zapewnia następujące funkcje:

- Uruchamianie przepływów danych na platformie Azure 
- Uruchamianie działania kopiowania między magazynami danych w chmurze
- Wysyłanie następujących działań przekształcania w sieci publicznej: działania notesu/jar/Python, działania Hive usługi HDInsight, działania w usłudze HDInsight, działanie usługi HDInsight MapReduce, działanie usługi HDInsight Spark, działanie usługi HDInsight streaming, maszyna Uczenie działania wykonywania wsadowego, Machine Learning aktualizowania działań związanych z zasobami, działania procedury składowanej, Data Lake Analytics działania U-SQL, działania niestandardowego platformy .NET, działania sieci Web, działania wyszukiwania oraz działania pobierania metadanych.

### <a name="azure-ir-network-environment"></a>Środowisko sieciowe IR Azure
Azure Integration Runtime obsługuje łączenie z magazynami danych i usługami obliczeniowymi za pomocą dostępnych publicznie punktów końcowych. Zastosowanie infrastruktury Integration Runtime (Self-hosted) w środowisku sieci wirtualnej Azure.

### <a name="azure-ir-compute-resource-and-scaling"></a>Zasoby obliczeniowe i skalowanie środowiska IR Azure
Infrastruktura Integration Runtime zapewnia w pełni zarządzane obliczenia bez serwera na platformie Azure.  Nie musisz przejmować się udostępnianiem infrastruktury, instalacją oprogramowania, poprawkami i skalowaniem możliwości.  Dodatkowo płacisz tylko za czas rzeczywistego wykorzystania.

Produkt Azure Integration Runtime zapewnia natywne możliwości obliczeniowe przenoszenia danych między magazynami danych w chmurze w sposób bezpieczny, niezawodny i wydajny.  Możesz określić liczbę jednostek integracji danych używanych w działaniu kopiowania, a rozmiar obliczeniowy infrastruktury Azure IR jest skalowany elastycznie bez konieczności jawnego regulowania rozmiaru infrastruktury Azure Integration Runtime. 

Wysyłanie działania to lekka operacja, kierująca działanie do docelowej usługi obliczeniowej, dzięki czemu nie występuje konieczności skalowania rozmiaru obliczeniowego dla tego scenariusza.

Informacje na temat tworzenia i konfigurowania infrastruktury Azure IR można znaleźć w przewodniku How to create and configure Azure IR (Jak utworzyć i skonfigurować infrastrukturę Azure IR). 

> [!NOTE] 
> Środowisko Azure Integration Runtime ma właściwości dotyczące środowiska uruchomieniowego przepływu danych, które definiuje podstawową infrastrukturę obliczeniową, która będzie używana do uruchamiania przepływów danych. 

## <a name="self-hosted-integration-runtime"></a>Infrastruktura Integration Runtime (Self-hosted)
Infrastruktura IR (Self-hosted) oferuje następujące możliwości:

- Uruchamianie działania kopiowania między magazynami danych w chmurze i magazynem danych w sieci prywatnej.
- Wysyłanie następujących działań transformacji do zasobów obliczeniowych w środowisku lokalnym lub Virtual Network platformy Azure: działanie programu Hive w usłudze HDInsight (BYOC — przenoszenie własnego klastra), działanie w usłudze HDInsight (BYOC), Usługa HDInsight MapReduce (BYOC), Usługa HDInsight Spark działanie (BYOC), działanie HDInsight przesyłania strumieniowego (BYOC), Machine Learning działania wykonywania wsadowego, Machine Learning aktualizowania zasobów, działania procedury składowanej, Data Lake Analytics działania U-SQL, działanie niestandardowe (uruchomione na Azure Batch), wyszukiwanie działanie i Pobieranie metadanych.

> [!NOTE] 
> Korzystaj z własnego środowiska Integration Runtime, aby obsługiwać magazyny danych, które wymagają sterownika przystosowanego do oprogramowania, takiego jak SAP HANA, MySQL itp.  Aby uzyskać więcej informacji, zobacz [obsługiwane magazyny danych](copy-activity-overview.md#supported-data-stores-and-formats).

> [!NOTE] 
> Java Runtime Environment (JRE) to zależność środowiska IR obsługiwanego przez siebie. Upewnij się, że środowisko JRE jest zainstalowane na tym samym hoście.

### <a name="self-hosted-ir-network-environment"></a>Własne środowisko sieciowe IR
Jeśli chcesz przeprowadzić integrację danych bezpiecznie w środowisku sieci prywatnej, która nie ma bezpośredniego połączenia ze środowiskiem chmury publicznej, możesz zainstalować hostowane samodzielnie, lokalne środowisko IR za zaporą firmową lub w wirtualnej sieci prywatnej.  Infrastruktura Integration Runtime (Self-hosted) wykonuje tylko wychodzące połączenia HTTP do otwartej sieci Internet.

### <a name="self-hosted-ir-compute-resource-and-scaling"></a>Zasoby obliczeniowe i skalowanie własnego środowiska IR
Infrastrukturę Integration Runtime (Self-hosted) należy zainstalować na maszynie lokalnej lub maszynie wirtualnej w sieci prywatnej. Aktualnie obsługujemy uruchamianie środowisk IR (Self-hosted) w systemie operacyjnym Windows.  

W celu zapewnienia wysokiej dostępności i skalowalności można zmienić skalowanie środowiska IR (Self-hosted), łącząc wystąpienie logiczne z wieloma maszynami lokalnymi w trybie aktywny-aktywny.  Aby uzyskać więcej informacji, zobacz artykuł jak [utworzyć i skonfigurować samoobsługowe środowisko IR](create-self-hosted-integration-runtime.md) w sekcji jak przewodniki dotyczące szczegółów.

## <a name="azure-ssis-integration-runtime"></a>Infrastruktura Azure SSIS Integration Runtime
Aby zmniejszyć i przenieść obecne obciążenie SSIS, można utworzyć środowisko IR Azure-SSIS w celu natywnego wykonywania pakietów SSIS.

### <a name="azure-ssis-ir-network-environment"></a>Środowisko sieciowe IR Azure-SSIS
Środowisko IR Azure-SSIS można udostępnić w sieci publicznej lub prywatnej.  Dostęp do danych lokalnych jest obsługiwany przez połączenie środowiska IR Azure-SSIS z siecią wirtualną połączoną z siecią lokalną.  

### <a name="azure-ssis-ir-compute-resource-and-scaling"></a>Zasoby obliczeniowe i skalowanie środowiska IR Azure-SSIS
Azure-SSIS IR to w pełni zarządzany klaster maszyn wirtualnych platformy Azure, których przeznaczeniem jest uruchamianie pakietów SSIS. Możesz przenieść własne Azure SQL Database lub serwer wystąpienia zarządzanego, aby hostować wykaz projektów usług SSIS/pakietów (SSISDB), które mają zostać do niego dołączone. Możesz skalować moc obliczeniową, określając rozmiar węzłów, a także liczbę węzłów w klastrze. Kosztem pracy produktu Azure-SSIS Integration Runtime można zarządzać, uruchamiając go i zatrzymując zgodnie z potrzebami.

Więcej informacji można znaleźć w artykule przewodnika How to create and configure Azure-SSIS IR (Jak utworzyć i skonfigurować środowisko Azure-SSIS IR).  Po utworzeniu możesz wdrażać istniejące pakiety SSIS i zarządzać nimi praktycznie bez zmian, przy użyciu znanych narzędzi, takich jak SQL Server Data Tools (SSDT) i SQL Server Management Studio (SSMS), co przypomina lokalne zastosowanie SSIS.

Aby uzyskać więcej informacji o środowisku uruchomieniowym Azure-SSIS, zobacz następujące artykuły: 

- [Samouczek: Wdrażanie pakietów usług SSIS na platformie Azure](tutorial-create-azure-ssis-runtime-portal.md). Ten artykuł zawiera szczegółowe instrukcje dotyczące tworzenia środowiska Azure-SSIS IR i używania bazy danych Azure SQL Database do hostowania wykazu usług SSIS. 
- [How to: Create an Azure-SSIS integration runtime (Jak: Tworzenie środowiska Azure SSIS Integration Runtime)](create-azure-ssis-integration-runtime.md). Ten artykuł rozszerza się w samouczku i zawiera instrukcje dotyczące używania wystąpienia zarządzanego Azure SQL Database i dołączania do sieci wirtualnej. 
- [Monitor an Azure-SSIS IR (Monitorowanie środowiska Azure-SSIS IR)](monitor-integration-runtime.md#azure-ssis-integration-runtime). W tym artykule przedstawiono sposób pobierania informacji o środowisku Azure-SSIS IR i opisów stanów w pobranych informacjach. 
- [Manage an Azure-SSIS IR (Zarządzanie środowiskiem Azure-SSIS IR)](manage-azure-ssis-integration-runtime.md). W tym artykule przedstawiono sposób zatrzymywania, uruchamiania lub usuwania środowiska Azure-SSIS IR. Zawiera on również instrukcje skalowania środowiska Azure-SSIS IR do wewnątrz za pomocą dodawania do niego węzłów. 
- [Join an Azure-SSIS IR to a virtual network](join-azure-ssis-integration-runtime-virtual-network.md) (Dołączanie środowiska IR Azure SSIS do sieci wirtualnej). Ten artykuł zawiera podstawowe informacje na temat dołączania środowiska IR Azure-SSIS do sieci wirtualnej platformy Azure. Opisano w nim kroki konfigurowania sieci wirtualnej za pomocą witryny Azure Portal tak, aby umożliwić dołączanie środowiska IR Azure-SSIS do sieci wirtualnej. 

## <a name="integration-runtime-location"></a>Lokalizacja środowiska Integration Runtime
Lokalizacja usługi Data Factory to miejsce, w którym przechowywane są metadane fabryki danych i gdzie inicjowane jest wyzwalanie potoku. Jednak fabryka danych może mieć dostęp do magazynów danych i usług obliczeniowych w innych regionach świadczenia usługi Azure, aby przenosić dane między magazynami danych lub przetwarzać dane przy użyciu usług obliczeniowych. To zachowanie jest wykonywane przez [dostępne globalnie środowisko IR](https://azure.microsoft.com/global-infrastructure/services/), co zapewnia zgodność danych, wydajność i niższe koszty wyjścia z sieci.

Lokalizacja IR definiuje lokalizację zaplecza obliczeniowego i w praktyce lokalizację, w której wykonywane jest przenoszenie danych, wysyłanie działania i wykonywanie pakietu SSIS. Lokalizacja IR może różnić się od lokalizacji fabryki danych, do której należy. 

### <a name="azure-ir-location"></a>Lokalizacja środowiska IR Azure
Dla środowiska IR Azure można ustawić konkretną lokalizację. W takim przypadku przenoszenie danych lub wysyłanie działania nastąpi w tym określonym regionie. 

Jeśli zdecydujesz się użyć **automatycznie rozwiązywania Azure IR** , który jest domyślnym, 

- W przypadku działania kopiowania usługa ADF automatycznie spróbuje jak najlepiej wykryć magazyn danych ujścia i źródła w celu wybrania najlepszej lokalizacji w tym samym regionie (jeśli jest dostępna) lub w najbliższym regionie w obrębie tego samego obszaru geograficznego. Jeśli tych obiektów nie da się wykryć, zostanie użyty region fabryki danych.

- W przypadku wyszukiwania/GetMetadata/usuwania działania (znanego również jako działania potokowe), wysyłania działań transformacji (nazywanych również działaniami zewnętrznymi) oraz operacji tworzenia (połączenie testowe, lista folderów przeglądania i lista tabel, Podgląd danych), ADF program użyje środowiska IR w regionie fabryki danych.

- W przypadku przepływu danych funkcja ADF będzie używać środowiska IR w regionie usługi Data Factory. 

  > [!TIP] 
  > Dobrym sposobem zapewnienia przepływu danych w tym samym regionie co odpowiednie magazyny danych (jeśli to możliwe). Można to osiągnąć przez automatyczne rozwiązanie Azure IR (Jeśli lokalizacja magazynu danych jest taka sama jak lokalizacja Data Factory) lub przez utworzenie nowego wystąpienia Azure IR w tym samym regionie, w którym znajdują się magazyny danych, a następnie wykonanie na nim przepływu danych. 

Istnieje możliwość monitorowania, która lokalizacja IR jest używana podczas wykonywania działania w widoku monitorowania działania potoku w interfejsie użytkownika lub w ładunku monitorowania działania.

>[!TIP]
>Jeśli obowiązują rygorystyczne wymagania dotyczące zgodności danych i konieczne jest zapewnienie, że dane nie opuszczą konkretnego obszaru geograficznego, można jawnie utworzyć środowisko IR Azure w konkretnym regionie i wskazać to środowisko IR dla połączonej usługi przy użyciu właściwości ConnectVia. Jeśli na przykład chcesz skopiować dane z obiektu Blob z regionu Południowe Zjednoczone Królestwo do magazynu danych SQL w tym samym regionie i chcesz mieć pewność, że dane nie opuszczą Zjednoczonego Królestwa, utwórz środowisko IR Azure w regionie Południowe Zjednoczone Królestwo i połącz obie połączone usługi z tym środowiskiem IR.

### <a name="self-hosted-ir-location"></a>Lokalizacja własnego środowiska IR
Środowisko IR (Self-hosted) jest rejestrowane logicznie w usłudze Data Factory, a zasoby obliczeniowe wspierające jego funkcjonalność zapewnia użytkownik. W związku z tym nie istnieje wyraźna właściwość lokalizacji środowiska IR (Self-hosted). 

W przypadku zastosowania do wykonania przenoszenia danych, środowisko IR (Self-hosted) pobiera dane ze źródła i zapisuje je w miejscu docelowym.

### <a name="azure-ssis-ir-location"></a>Lokalizacja środowiska IR Azure-SSIS
Wybór odpowiedniej lokalizacji dla środowiska IR Azure-SSIS jest kluczowy dla osiągnięcia wysokiej wydajności obciążeń wyodrębnianie-przekształcanie-ładowanie (ETL).

- Lokalizacja Azure-SSIS IR nie musi być taka sama jak lokalizacja fabryki danych, ale powinna być taka sama jak lokalizacja własnego serwera Azure SQL Database/Managed instance, gdzie SSISDB ma być hostowana. Dzięki temu środowisko IR Azure-SSIS może z łatwością uzyskać dostęp do bazy SSISDB bez wytwarzania dużego ruchu między różnymi lokalizacjami.
- Jeśli nie masz istniejącego serwera wystąpienia Azure SQL Database/zarządzanego do hostowania SSISDB, ale masz lokalne źródła danych/miejsca docelowe, należy utworzyć nowy serwer wystąpienia Azure SQL Database/zarządzanego w tej samej lokalizacji sieci wirtualnej połączonej z usługą Sieć lokalna.  W ten sposób można utworzyć Azure-SSIS IR przy użyciu nowego serwera wystąpienia Azure SQL Database/zarządzanego i dołączenia do tej sieci wirtualnej, a wszystko to w tej samej lokalizacji, co skutecznie minimalizuje ruchy danych w różnych lokalizacjach.
- Jeśli lokalizacja istniejącego serwera Azure SQL Database/zarządzanego wystąpienia, gdzie SSISDB jest hostowana, nie jest taka sama jak lokalizacja sieci wirtualnej podłączonej do sieci lokalnej, najpierw utwórz Azure-SSIS IR przy użyciu istniejącej Azure SQL Database/ Serwer wystąpienia zarządzanego i przyłączanie innej sieci wirtualnej w tej samej lokalizacji, a następnie Konfigurowanie sieci wirtualnej do połączenia sieci wirtualnej między różnymi lokalizacjami.

Na poniższym diagramie przedstawiono ustawienia lokalizacji usługi Data Factory i jej czasy uruchomienia integracji:

![Lokalizacja środowiska Integration Runtime](media/concepts-integration-runtime/integration-runtime-location.png)

## <a name="determining-which-ir-to-use"></a>Wybór środowiska IR do użycia

### <a name="copy-activity"></a>Działanie kopiowania

Działanie kopiowania wymaga połączonych usług źródła i ujścia w celu zdefiniowania kierunku przepływu danych. Poniższa logika jest stosowana do określenia, które wystąpienie środowiska IR jest używane do wykonania kopii: 

- **Kopiowanie między dwoma źródłami danych w chmurze**: jeśli zarówno połączona usługa źródła, jak i ujścia używają środowiska IR Azure, usługa ADF użyje regionalnego środowiska IR Azure (jeśli zostało określone) lub automatycznie określi lokalizację środowiska IR Azure, jeśli wybrano opcję automatycznego określania środowiska IR, co jest ustawieniem domyślnym. Opisano to w sekcji [Lokalizacja środowiska Integration Runtime](#integration-runtime-location).
- **Kopiowanie między źródłem danych w chmurze i źródłem danych w sieci prywatnej**: jeśli połączona usługa źródła lub ujścia wskazuje środowisko IR (Self-hosted), działanie kopiowania jest wykonywane w tym środowisku IT (Self-hosted).
- **Kopiowanie między dwoma źródłami danych w sieci prywatnej**: obie połączone usługi (źródła i ujścia) muszą wskazywać to samo wystąpienie środowiska Integration Runtime, które jest także wykorzystywane do wykonania działania kopiowania.

### <a name="lookup-and-getmetadata-activity"></a>Działanie wyszukiwania i uzyskiwania metadanych

Działanie wyszukiwania i uzyskiwania metadanych jest wykonywane w środowisku Integration Runtime skojarzonym z połączoną usługą magazynu danych.

### <a name="transformation-activity"></a>Działanie przekształcania

Każde działanie przekształcania zawiera docelową obliczeniową usługę połączoną, która wskazuje infrastrukturę Integration Runtime. To wystąpienie infrastruktury Integration Runtime jest miejscem, z którego wysyłane jest działanie przekształcania.

### <a name="data-flow-activity"></a>Działanie przepływu danych

Działanie przepływu danych jest wykonywane na skojarzonym z nim środowisku Integration Runtime. 

## <a name="next-steps"></a>Następne kroki
Zobacz następujące artykuły:

- [Tworzenie środowiska Azure Integration Runtime](create-azure-integration-runtime.md)
- [Create self-hosted integration runtime (Tworzenie środowiska Integration Runtime (Self-hosted)](create-self-hosted-integration-runtime.md)
- [Create an Azure-SSIS integration runtime (Tworzenie środowiska Azure-SSIS Integration Runtime)](create-azure-ssis-integration-runtime.md) Ten artykuł rozszerza się w samouczku i zawiera instrukcje dotyczące używania wystąpienia zarządzanego Azure SQL Database i dołączania do sieci wirtualnej. 
