---
title: Najważniejsze wskazówki dotyczące korzystania z usługi Azure Data Storage Gen1 | Dokumenty firmy Microsoft
description: Poznaj najważniejsze wskazówki dotyczące pozyskiwania danych, zabezpieczeń dat i wydajności związanych z używaniem usługi Azure Data Lake Storage Gen1 (wcześniej znanej jako Usługa Azure Data Lake Store)
services: data-lake-store
documentationcenter: ''
author: sachinsbigdata
manager: mtillman
ms.service: data-lake-store
ms.devlang: na
ms.topic: article
ms.date: 06/27/2018
ms.author: sachins
ms.openlocfilehash: a8ca67d1ff3100aee02ed473c9cc2180de3973b8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75638939"
---
# <a name="best-practices-for-using-azure-data-lake-storage-gen1"></a>Najważniejsze wskazówki dotyczące korzystania z usługi Azure Data Storage Gen1

[!INCLUDE [data-lake-storage-gen1-rename-note.md](../../includes/data-lake-storage-gen1-rename-note.md)]

W tym artykule dowiesz się o najlepszych rozwiązaniach i zagadnieniach dotyczących pracy z usługą Azure Data Lake Storage Gen1. Ten artykuł zawiera informacje dotyczące zabezpieczeń, wydajności, odporności i monitorowania usługi Data Lake Storage Gen1. Przed data lake storage gen1, praca z naprawdę dużych danych w usługach, takich jak Azure HDInsight było skomplikowane. Trzeba było shard danych na wielu kontach magazynu obiektów Blob, tak aby można było osiągnąć magazyn petabajtów i optymalną wydajność w tej skali. W przypadku usługi Data Lake Storage Gen1 większość twardych limitów rozmiaru i wydajności jest usuwana. Jednak nadal istnieją pewne zagadnienia, które obejmuje ten artykuł, dzięki czemu można uzyskać najlepszą wydajność z Data Lake Storage Gen1.

## <a name="security-considerations"></a>Zagadnienia dotyczące bezpieczeństwa

Usługa Azure Data Lake Storage Gen1 oferuje kontrolki dostępu POSIX i szczegółową inspekcję dla użytkowników, grup i podmiotów korzystających z usługi Azure Active Directory (Azure AD). Te kontrolki dostępu można ustawić na istniejące pliki i foldery. Formanty dostępu mogą być również używane do tworzenia wartości domyślnych, które mogą być stosowane do nowych plików lub folderów. Gdy uprawnienia są ustawione na istniejące foldery i obiekty podrzędne, uprawnienia muszą być propagowane rekursywnie na każdym obiekcie. Jeśli istnieje duża liczba plików, propagowanie uprawnień może zająć dużo czasu. Czas zajęty może wynosić od 30 do 50 obiektów przetwarzanych na sekundę. W związku z tym należy odpowiednio zaplanować strukturę folderów i grupy użytkowników. W przeciwnym razie może to spowodować nieoczekiwane opóźnienia i problemy podczas pracy z danymi.

Załóżmy, że masz folder z 100 000 obiektów podrzędnych. Jeśli weźmiesz dolną granicę 30 obiektów przetworzonych na sekundę, zaktualizowanie uprawnień dla całego folderu może potrwać godzinę. Więcej szczegółów na temat list ACL Data Lake Storage Gen1 można znaleźć w [obszarze Kontrola dostępu w usłudze Azure Data Lake Storage Gen1.](data-lake-store-access-control.md) Aby zwiększyć wydajność przypisywania list ACL cyklicznie, można użyć narzędzia wiersza polecenia usługi Azure Data Lake. Narzędzie tworzy wiele wątków i cykliczną logikę nawigacji, aby szybko zastosować listy ACL do milionów plików. Narzędzie jest dostępne dla systemów Linux i Windows, a [dokumentację](https://github.com/Azure/data-lake-adlstool) i [pliki do pobrania](https://aka.ms/adlstool-download) dla tego narzędzia można znaleźć na GitHub. Te same ulepszenia wydajności można włączyć za pomocą własnych narzędzi napisanych za pomocą zestawu SDK data lake storage Gen1 [.NET](data-lake-store-data-operations-net-sdk.md) i [Java](data-lake-store-get-started-java-sdk.md) SDK.

### <a name="use-security-groups-versus-individual-users"></a>Używanie grup zabezpieczeń w porównaniu z poszczególnymi użytkownikami

Podczas pracy z dużych danych w usłudze Data Lake Storage Gen1, najprawdopodobniej jednostki usługi jest używany do umożliwienia usług, takich jak Usługi Azure HDInsight do pracy z danymi. Mogą jednak wystąpić przypadki, w których poszczególni użytkownicy również potrzebują dostępu do danych. W takich przypadkach należy użyć [grup zabezpieczeń](data-lake-store-secure-data.md#create-security-groups-in-azure-active-directory) usługi Azure Active Directory zamiast przypisywania poszczególnych użytkowników do folderów i plików.

Po przypisaniu uprawnień do grupy zabezpieczeń dodawanie lub usuwanie użytkowników z grupy nie wymaga żadnych aktualizacji usługi Data Lake Storage Gen1. Pomaga to również zapewnić, że nie przekroczysz limitu [32 list dostępu i domyślnych list ACL](../azure-resource-manager/management/azure-subscription-service-limits.md#data-lake-store-limits) (dotyczy to czterech list ACL w stylu POSIX, które są zawsze skojarzone z każdym plikiem i folderem: [użytkownika](data-lake-store-access-control.md#the-owning-user) [posiadającego, grupy właścicielskiej,](data-lake-store-access-control.md#the-owning-group) [maski](data-lake-store-access-control.md#the-mask)i innych).

### <a name="security-for-groups"></a>Zabezpieczenia dla grup

Jak wspomniano w omówiono, gdy użytkownicy potrzebują dostępu do usługi Data Lake Storage Gen1, najlepiej jest używać grup zabezpieczeń usługi Azure Active Directory. Niektóre zalecane grupy, aby rozpocząć może być **ReadOnlyUsers**, **WriteAccessUsers**i **FullAccessUsers** dla katalogu głównego konta, a nawet oddzielne dla kluczowych podfolderów. Jeśli istnieją inne przewidywane grupy użytkowników, które mogą zostać dodane później, ale nie zostały jeszcze zidentyfikowane, można rozważyć utworzenie fikcyjnych grup zabezpieczeń, które mają dostęp do niektórych folderów. Użycie grupy zabezpieczeń gwarantuje, że później nie trzeba długiego czasu przetwarzania do przypisywania nowych uprawnień do tysięcy plików.

### <a name="security-for-service-principals"></a>Zabezpieczenia dla podmiotów świadczących usługi

Jednostki usługi Azure Active Directory są zazwyczaj używane przez usługi, takie jak Usługa Azure HDInsight, aby uzyskać dostęp do danych w usłudze Data Lake Storage Gen1. W zależności od wymagań dotyczących dostępu w wielu obciążeniach mogą istnieć pewne kwestie, aby zapewnić bezpieczeństwo wewnątrz i na zewnątrz organizacji. Dla wielu klientów jeden podmiot usługi Azure Active Directory może być odpowiedni i może mieć pełne uprawnienia w katalogu głównym konta Usługi Data Lake Storage Gen1. Inni klienci mogą wymagać wielu klastrów z różnymi jednostkami usługi, gdzie jeden klaster ma pełny dostęp do danych, a inny klaster z dostępem tylko do odczytu. Podobnie jak w przypadku grup zabezpieczeń, można rozważyć utworzenie jednostki usługi dla każdego przewidywanego scenariusza (odczyt, zapis, pełna) po utworzeniu konta Usługi Magazyn danych Gen1.

### <a name="enable-the-data-lake-storage-gen1-firewall-with-azure-service-access"></a>Włączanie zapory usługi Data Lake Storage Gen1 z dostępem do usługi Azure

Usługa Data Lake Storage Gen1 obsługuje opcję włączania zapory i ograniczania dostępu tylko do usług platformy Azure, co jest zalecane w przypadku mniejszych wektorów ataku z zewnętrznych włamań. Zapora może być włączona na koncie Data Lake Storage Gen1 w witrynie Azure portal za pośrednictwem **zapory włącz zapory** > **(ON)** > **Zezwalaj na dostęp do** opcji usług platformy Azure.

![Ustawienia zapory w umiar pamięci masowej usługi Data Lake Gen1](./media/data-lake-store-best-practices/data-lake-store-firewall-setting.png "Ustawienia zapory w umiar pamięci masowej usługi Data Lake Gen1")

Po włączeniu zapory tylko usługi platformy Azure, takie jak HDInsight, Data Factory, SQL Data Warehouse itp. Ze względu na wewnętrzne translacji adresów sieciowych używane przez platformę Azure, Data Lake Storage Gen1 firewall nie obsługuje ograniczania określonych usług przez adres IP i jest przeznaczony tylko dla ograniczeń punktów końcowych poza platformą Azure, takich jak lokalnie.

## <a name="performance-and-scale-considerations"></a>Zagadnienia dotyczące wydajności i skali

Jedną z najpotężniejszych funkcji usługi Data Lake Storage Gen1 jest usunięcie twardych limitów przepustowości danych. Usunięcie limitów umożliwia klientom zwiększenie ich rozmiaru danych i towarzyszących im wymagań dotyczących wydajności bez konieczności dzielenia na fragmenty danych. Jedną z najważniejszych kwestii optymalizacji wydajności magazynu usługi Data Lake Gen1 jest to, że działa najlepiej, gdy dany równoległości.

### <a name="improve-throughput-with-parallelism"></a>Zwiększ przepustowość dzięki równoległości

Należy rozważyć podanie 8-12 wątków na rdzeń dla najbardziej optymalnej przepływności odczytu/zapisu. Jest to spowodowane blokowanie odczytów/zapisów w jednym wątku i więcej wątków może zezwolić na wyższą współbieżność na maszynie Wirtualnej. Aby upewnić się, że poziomy są w dobrej kondycji i równoległości można zwiększyć, należy monitorować wykorzystanie procesora CPU maszyny Wirtualnej.

### <a name="avoid-small-file-sizes"></a>Unikaj małych rozmiarów plików

Uprawnienia POSIX i inspekcji w usługi Data Lake Storage Gen1 pochodzi z obciążeniem, które staje się widoczne podczas pracy z wielu małych plików. Najlepszym rozwiązaniem jest wprowadzenie danych w większych plikach w porównaniu z zapisywaniem tysięcy lub milionów małych plików do usługi Data Lake Storage Gen1. Unikanie małych rozmiarów plików może mieć wiele zalet, takich jak:

* Obniżanie kontroli uwierzytelniania w wielu plikach
* Zredukowane połączenia otwartych plików
* Szybsze kopiowanie/replikacja
* Mniej plików do przetworzenia podczas aktualizowania uprawnień PoSIX magazynu usługi Data Lake Storage Gen1

W zależności od tego, jakie usługi i obciążenia używają danych, dobry rozmiar do rozważenia dla plików wynosi 256 MB lub więcej. Jeśli rozmiary plików nie mogą być wsadowe podczas lądowania w umia dla magazynu usługi Data Lake Gen1, można mieć oddzielne zadanie zagęszczania, które łączy te pliki w większe. Aby uzyskać więcej informacji i zalecenia dotyczące rozmiarów plików i organizowania danych w programie Data Lake Storage Gen1, zobacz [Struktura zestawu danych](data-lake-store-performance-tuning-guidance.md#structure-your-data-set).

### <a name="large-file-sizes-and-potential-performance-impact"></a>Duże rozmiary plików i potencjalny wpływ na wydajność

Mimo że Data Lake Storage Gen1 obsługuje duże pliki o rozmiarze do petabajtów, aby uzyskać optymalną wydajność i w zależności od procesu odczytu danych, może nie być idealnym rozwiązaniem, aby przejść powyżej 2 GB średnio. Na przykład podczas kopiowania danych między lokalizacjami lub różnymi kontami magazynu przy użyciu **protokołu Distcp** pliki są najlepszym poziomem szczegółowości używanym do określania zadań mapy. Tak więc, jeśli kopiujesz 10 plików, które są 1 TB każdy, co najwyżej 10 maperów są przydzielane. Ponadto, jeśli masz wiele plików z przypisanymi maperami, początkowo mapery działają równolegle, aby przenieść duże pliki. Jednak, jak zadanie zaczyna się likwidować tylko kilka maperów pozostają przydzielone i można utknąć z jednym mapera przypisane do dużego pliku. Firma Microsoft przedstawiła ulepszenia do Distcp, aby rozwiązać ten problem w przyszłych wersjach Hadoop.

Innym przykładem do rozważenia jest podczas korzystania z usługi Azure Data Lake Analytics z data lake storage gen1. W zależności od przetwarzania wykonane przez ekstraktor, niektóre pliki, które nie mogą być podzielone (na przykład XML, JSON) może cierpieć w wydajności, gdy większa niż 2 GB. W przypadkach, gdy pliki mogą być podzielone przez ekstraktor (na przykład CSV), preferowane są duże pliki.

### <a name="capacity-plan-for-your-workload"></a>Plan pojemności dla obciążenia pracą

Usługa Azure Data Lake Storage Gen1 usuwa limity ograniczania przepustowości twardych we/wy, które są umieszczane na kontach magazynu obiektów Blob. Nadal jednak istnieją miękkie limity, które należy wziąć pod uwagę. Domyślne limity ograniczania transferu danych przychodzących/wychodzących spełniają potrzeby większości scenariuszy. Jeśli obciążenie musi zwiększyć limity, należy pracować z pomocą techniczną firmy Microsoft. Ponadto, spójrz na limity na etapie weryfikacji koncepcji, tak aby limity ograniczania IO nie zostały trafione podczas produkcji. Jeśli tak się stanie, może to wymagać oczekiwania na ręczne zwiększenie od zespołu inżynierów firmy Microsoft. Jeśli wystąpi ograniczanie we/wy, usługa Azure Data Lake Storage Gen1 zwraca kod błędu 429 i najlepiej powinny być ponowione przy odpowiedniej wykładniczej zasady wycofywania.

### <a name="optimize-writes-with-the-data-lake-storage-gen1-driver-buffer"></a>Optymalizacja "zapisów" za pomocą buforu sterownika Data Lake Storage Gen1

Aby zoptymalizować wydajność i zmniejszyć operacje We/Wy podczas zapisywania do usługi Data Lake Storage Gen1 z usługi Hadoop, należy wykonać operacje zapisu tak blisko rozmiaru buforu sterownika magazynu danych Lake Gen1, jak to możliwe. Staraj się nie przekraczać rozmiaru buforu przed opróżnianiem, na przykład podczas przesyłania strumieniowego przy użyciu obciążeń przesyłania strumieniowego Apache Storm lub Spark. Podczas pisania do data lake storage gen1 z HDInsight/Hadoop, ważne jest, aby wiedzieć, że Data Lake Storage Gen1 ma sterownik z buforem 4 MB. Podobnie jak wiele sterowników systemu plików, ten bufor można ręcznie opróżnić przed osiągnięciem rozmiaru 4 MB. Jeśli nie, jest natychmiast opróżniane do magazynu, jeśli następny zapis przekracza maksymalny rozmiar buforu. Jeśli to możliwe, należy unikać przekroczenia lub znacznego niedopełnienia buforu podczas synchronizowania/opróżniania zasad według liczby lub przedziału czasu.

## <a name="resiliency-considerations"></a>Zagadnienia związane z odpornością

Podczas owego zaprojektowywania systemu z usługą Data Lake Storage Gen1 lub dowolnej usługi w chmurze należy wziąć pod uwagę wymagania dotyczące dostępności i sposobu reagowania na potencjalne przerwy w usłudze. Problem może być zlokalizowany do określonego wystąpienia lub nawet w całym regionie, więc posiadanie planu dla obu jest ważne. W zależności od **celu czasu odzyskiwania** i **ław** sla celu punktu odzyskiwania dla obciążenia można wybrać mniej lub bardziej agresywną strategię wysokiej dostępności i odzyskiwania po awarii.

### <a name="high-availability-and-disaster-recovery"></a>Wysoka dostępność i odzyskiwanie po awarii

Wysoka dostępność (HA) i odzyskiwanie po awarii (DR) mogą być czasami łączone ze sobą, chociaż każdy ma nieco inną strategię, zwłaszcza jeśli chodzi o dane. Data Lake Storage Gen1 obsługuje już replikację 3x pod maską, aby chronić przed zlokalizowanych awarii sprzętu. Jednak ponieważ replikacja między regionami nie jest wbudowana, należy zarządzać tym samodzielnie. Podczas tworzenia planu dla wysokiej analizy, w przypadku przerwania usługi obciążenie wymaga dostępu do najnowszych danych tak szybko, jak to możliwe, przełączając się na oddzielnie replikowane wystąpienie lokalnie lub w nowym regionie.

W strategii odzyskiwania po awarii, aby przygotować się na mało prawdopodobne zdarzenie katastrofalnej awarii regionu, ważne jest również, aby dane replikowane do innego regionu. Te dane mogą początkowo być takie same jak zreplikowane dane wysokiej źródła wygody. Należy jednak również wziąć pod uwagę wymagania dotyczące przypadków brzegowych, takich jak uszkodzenie danych, w których można utworzyć okresowe migawki, do których można wrócić. W zależności od ważności i rozmiaru danych należy wziąć pod uwagę stopniowe migawki delta okresów 1-, 6- i 24-godzinnych w magazynie lokalnym i/lub wtórnym, zgodnie z tolerancjami ryzyka.

Aby zapewnić odporność danych za pomocą usługi Data Lake Storage Gen1, zaleca się replikowanie danych geograficznie do oddzielnego regionu z częstotliwością, która spełnia wymagania wysokiej dostępności/odzyskiwania po awarii, najlepiej co godzinę. Ta częstotliwość replikacji minimalizuje ogromne ruchy danych, które mogą mieć konkurencyjne potrzeby przepustowości z głównym systemem i lepszym celem punktu odzyskiwania (RPO). Ponadto należy rozważyć sposoby dla aplikacji przy użyciu usługi Data Lake Storage Gen1, aby automatycznie awaryjnie do konta pomocniczego za pośrednictwem wyzwalaczy monitorowania lub długości nieudanych prób lub przynajmniej wysłać powiadomienie do administratorów do ręcznej interwencji. Należy pamiętać, że istnieje kompromis w przypadku awarii w porównaniu do oczekiwania na usługę wrócić do trybu online. Jeśli dane nie zostały zakończone replikacji, pracy awaryjnej może spowodować potencjalne utraty danych, niespójności lub złożonych scalania danych.

Poniżej znajdują się trzy najlepsze zalecane opcje aranżacji replikacji między kontami Usługi Data Lake Storage Gen1 i kluczowe różnice między poszczególnymi z nich.

|  |Distcp ( distcp )  |Azure Data Factory  |AdlCopy (Polski)  |
|---------|---------|---------|---------|
|**Limity skali**     | Ograniczone przez węzły procesu roboczego        | Ograniczone przez jednostki Max Cloud Data Movement        | Powiązani jednostkami Analytics        |
|**Obsługuje kopiowanie delt**     |   Tak      | Nie         | Nie         |
|**Wbudowana aranżacja**     |  Nie (użyj Oozie Airflow lub cron zadania)       | Tak        | Nie (użyj usługi Azure Automation lub Harmonogramu zadań systemu Windows)         |
|**Obsługiwane systemy plików**     | ADL, HDFS, WASB, S3, GS, CFS        |Liczne, patrz [Złącza](../data-factory/connector-azure-blob-storage.md).         | ADL do ADL, WASB do ADL (tylko w tym samym regionie)        |
|**Obsługa systemu operacyjnego**     |Każdy system operacyjny z systemem Hadoop         | Nie dotyczy          | Windows 10         |

### <a name="use-distcp-for-data-movement-between-two-locations"></a>Użyj Distcp do przenoszenia danych między dwiema lokalizacjami

Skrót od kopii rozproszonej, Distcp jest narzędziem wiersza polecenia Linuksa, które jest wyposażone w Hadoop i zapewnia rozproszony transfer danych między dwiema lokalizacjami. Dwie lokalizacje mogą być Data Lake Storage Gen1, HDFS, WASB lub S3. To narzędzie używa mapReduce zadań w klastrze Hadoop (na przykład HDInsight) do skalowania w poziomie na wszystkich węzłów. Distcp jest uważany za najszybszy sposób przenoszenia dużych zbiorów danych bez specjalnych urządzeń do kompresji sieciowej. Distcp udostępnia również opcję aktualizowania tylko różnic między dwiema lokalizacjami, obsługuje automatyczne próby prób, a także dynamiczne skalowanie obliczeń. Takie podejście jest niezwykle wydajne, jeśli chodzi o replikowanie rzeczy, takich jak hive/spark tabel, które mogą mieć wiele dużych plików w jednym katalogu i chcesz skopiować tylko za pomocą zmodyfikowanych danych. Z tych powodów Distcp jest najbardziej zalecanym narzędziem do kopiowania danych między magazynami dużych zbiorów danych.

Zadania kopiowania mogą być wyzwalane przez przepływy pracy Apache Oozie przy użyciu wyzwalaczy częstotliwości lub danych, a także zadania cron linuksa. W przypadku zadań replikacji intensywnej zaleca się wyodrębnienie oddzielnego klastra hdinsight Hadoop, który można dostroić i skalować specjalnie dla zadań kopiowania. Dzięki temu zadania kopiowania nie kolidują z zadaniami krytycznymi. Jeśli jest uruchomiona replikacja na wystarczająco szerokiej częstotliwości, klaster może nawet zostać zdjęty między każdym zadaniem. Jeśli w przypadku niepowodzenia w poprzek do regionu pomocniczego, upewnij się, że inny klaster jest również spun w regionie pomocniczym do replikacji nowych danych z powrotem do podstawowego konta magazynu usługi Data Lake Gen1 gen1 po jego powrocie do pracy. Aby zapoznać się z przykładami używania protokołu Distcp, zobacz [Kopiowanie danych między obiektami blob usługi Azure Storage i Gen1 magazynu usługi Data Lake.](data-lake-store-copy-data-wasb-distcp.md)

### <a name="use-azure-data-factory-to-schedule-copy-jobs"></a>Planowanie zadań kopiowania za pomocą usługi Azure Data Factory

Usługa Azure Data Factory może być również używana do planowania zadań kopiowania przy użyciu **działania kopiowania,** a nawet można ją skonfigurować z częstotliwością za pomocą **Kreatora kopiowania.** Należy pamiętać, że usługa Azure Data Factory ma limit jednostek przenoszenia danych w chmurze (DMU), a ostatecznie ogranicza przepływność/obliczenie dla dużych obciążeń danych. Ponadto usługa Azure Data Factory obecnie nie oferuje aktualizacji różnicowych między kontami Usługi Data Lake Storage Gen1, więc foldery, takie jak tabele hive, wymagałyby pełnej kopii do replikacji. Więcej informacji na temat kopiowania za pomocą usługi Data Factory można znaleźć w [przewodniku dostrajania aktywności](../data-factory/copy-activity-performance.md) kopiowania.

### <a name="adlcopy"></a>AdlCopy (Polski)

AdlCopy to narzędzie wiersza polecenia systemu Windows, które umożliwia kopiowanie danych między dwoma kontami Data Lake Storage Gen1 tylko w tym samym regionie. Narzędzie AdlCopy udostępnia autonomiczną opcję lub opcję uruchamiania zadania kopiowania za pomocą konta usługi Azure Data Lake Analytics. Chociaż został pierwotnie zbudowany dla kopii na żądanie, w przeciwieństwie do niezawodnej replikacji, zapewnia inną opcję kopiowania rozproszonego na kontach Usługi Data Lake Storage Gen1 w tym samym regionie. Aby uzyskać niezawodność, zaleca się użycie opcji usługi Data Lake Analytics w wersji premium dla dowolnego obciążenia produkcyjnego. Wersja autonomiczna może zwracać zajęty odpowiedzi i ma ograniczoną skalę i monitorowania.

Podobnie jak Distcp, AdlCopy musi być aranżowane przez coś takiego jak Usługa Azure Automation lub Harmonogram zadań systemu Windows. Podobnie jak w przypadku usługi Data Factory, AdlCopy nie obsługuje kopiowania tylko zaktualizowanych plików, ale kopiuje i zastępuje istniejące pliki. Aby uzyskać więcej informacji i przykładów korzystania z AdlCopy, zobacz [Kopiowanie danych z obiektów blob usługi Azure Storage do usługi Data Lake Storage Gen1](data-lake-store-copy-data-azure-storage-blob.md).

## <a name="monitoring-considerations"></a>Zagadnienia związane z monitorowaniem

Data Lake Storage Gen1 zapewnia szczegółowe dzienniki diagnostyczne i inspekcje. Usługa Data Lake Storage Gen1 udostępnia podstawowe metryki w witrynie Azure portal w obszarze konta Data Lake Storage Gen1 i w usłudze Azure Monitor. Dostępność usługi Data Lake Storage Gen1 jest wyświetlana w witrynie Azure portal. Jednak ta metryka jest odświeżany co siedem minut i nie można zbadać za pośrednictwem publicznie udostępnianego interfejsu API. Aby uzyskać najbardziej aktualną dostępność konta Usługi Data Lake Storage Gen1, należy uruchomić własne testy syntetyczne, aby sprawdzić dostępność. Inne metryki, takie jak całkowite wykorzystanie magazynu, żądania odczytu/zapisu i ruch przychodzący/wychodzący może potrwać do 24 godzin, aby odświeżyć. Tak więc bardziej aktualne metryki muszą być obliczane ręcznie za pomocą narzędzi wiersza polecenia Hadoop lub agregowania informacji dziennika. Najszybszym sposobem uzyskania najnowszego wykorzystania magazynu jest uruchomienie tego polecenia HDFS z węzła klastra Hadoop (na przykład węzła głównego):

    hdfs dfs -du -s -h adl://<adlsg1_account_name>.azuredatalakestore.net:443/

### <a name="export-data-lake-storage-gen1-diagnostics"></a>Eksportowanie diagnostyki gen1 magazynu danych lake

Jednym z najszybszych sposobów uzyskania dostępu do dzienników z przeszukiwania z usługi Data Lake Storage Gen1 jest włączenie wysyłania dzienników do **usługi Log Analytics** w obszarze **diagnostyki** bloku dla konta Data Lake Storage Gen1. Zapewnia to natychmiastowy dostęp do dzienników przychodzących z filtrami czasu i zawartości, a także opcje alertów (e-mail/webhook) wyzwalane w odstępach 15-minutowych. Aby uzyskać instrukcje, zobacz [Uzyskiwanie dostępu do dzienników diagnostycznych dla usługi Azure Data Lake Storage Gen1](data-lake-store-diagnostic-logs.md).

Aby uzyskać więcej alertów w czasie rzeczywistym i większą kontrolę nad miejscem, w którym należy wylądować dzienniki, należy rozważyć eksportowanie dzienników do usługi Azure EventHub, gdzie zawartość może być analizowana indywidualnie lub w ciągu przedziału czasu w celu przesyłania powiadomień w czasie rzeczywistym do kolejki. Oddzielna aplikacja, taka jak [aplikacja logiki,](../connectors/connectors-create-api-azure-event-hubs.md) może następnie korzystać z alertów i przekazywać je do odpowiedniego kanału, a także przesyłać metryki do narzędzi monitorowania, takich jak NewRelic, Datadog lub AppDynamics. Alternatywnie, jeśli używasz narzędzia innej firmy, takich jak ElasticSearch, można wyeksportować dzienniki do usługi Blob Storage i użyć [wtyczki Azure Logstash](https://github.com/Azure/azure-diagnostics-tools/tree/master/Logstash/logstash-input-azureblob) do korzystania z danych do elastycznego wyszukiwania, Kibana i Logstash (ELK) stosu.

### <a name="turn-on-debug-level-logging-in-hdinsight"></a>Włączanie rejestrowania na poziomie debugowania w programie HDInsight

Jeśli wysyłka dziennika usługi Data Lake Storage Gen1 nie jest włączona, usługa Azure HDInsight umożliwia również włączenie [rejestrowania po stronie klienta dla usługi Data Lake Storage Gen1](data-lake-store-performance-tuning-mapreduce.md) za pośrednictwem log4j. W **konfiguracjach Ambari** > **YARN** > **Config** > Advanced**yarn-log4j**należy ustawić następującą właściwość:

    log4j.logger.com.microsoft.azure.datalake.store=DEBUG

Po ustawieniu właściwości i ponownym uruchomieniu węzłów diagnostyka Data Lake Storage Gen1 jest zapisywana w dziennikach YARN w węzłach (/tmp/\<user\>/yarn.log), a ważne szczegóły, takie jak błędy lub ograniczanie przepustowości (kod błędu HTTP 429) mogą być monitorowane. Te same informacje mogą być również monitorowane w dziennikach usługi Azure Monitor lub wszędzie tam, gdzie dzienniki są wysyłane do bloku [diagnostyki](data-lake-store-diagnostic-logs.md) konta Gen1 magazynu usługi Data Lake. Zaleca się, aby co najmniej mieć włączone rejestrowanie po stronie klienta lub korzystać z opcji wysyłki dziennika z Data Lake Storage Gen1 dla widoczności operacyjnej i łatwiejsze debugowanie.

### <a name="run-synthetic-transactions"></a>Uruchamianie transakcji syntetycznych

Obecnie metryka dostępności usługi dla usługi Data Lake Storage Gen1 w witrynie Azure portal ma 7-minutowe okno odświeżania. Ponadto nie można zbadać przy użyciu publicznie udostępnianego interfejsu API. W związku z tym zaleca się, aby utworzyć podstawową aplikację, która wykonuje transakcje syntetyczne do usługi Data Lake Storage Gen1, które mogą zapewnić do minutowej dostępności. Przykładem może być utworzenie usługi WebJob, logic app lub aplikacji funkcji platformy Azure w celu wykonania odczytu, utworzenia i aktualizacji względem usługi Data Lake Storage Gen1 i wysłania wyników do rozwiązania do monitorowania. Operacje można wykonać w folderze tymczasowym, a następnie usunąć po teście, które mogą być uruchamiane co 30-60 sekund, w zależności od wymagań.

## <a name="directory-layout-considerations"></a>Zagadnienia dotyczące układu katalogów

Podczas wyładunku danych do źródła danych, ważne jest, aby wstępnie zaplanować strukturę danych, tak aby zabezpieczenia, partycjonowanie i przetwarzanie mogły być skutecznie wykorzystywane. Wiele z poniższych zaleceń można użyć, czy to z usługi Azure Data Lake Storage Gen1, magazyn obiektów blob lub usługi HDFS. Każde obciążenie ma różne wymagania dotyczące sposobu korzystania z danych, ale poniżej znajdują się niektóre typowe układy, które należy wziąć pod uwagę podczas pracy z IoT i scenariuszami wsadowymi.

### <a name="iot-structure"></a>Struktura IoT

W obciążeniach IoT może istnieć wiele danych wyładowywanych w magazynie danych, który obejmuje wiele produktów, urządzeń, organizacji i klientów. Ważne jest, aby wstępnie zaplanować układ katalogów dla organizacji, zabezpieczeń i wydajnego przetwarzania danych dla konsumentów w dół strumienia. Ogólny szablon do rozważenia może być następujący układ:

    {Region}/{SubjectMatter(s)}/{yyyy}/{mm}/{dd}/{hh}/

Na przykład dane telemetryczne lądowania dla silnika samolotu w Wielkiej Brytanii może wyglądać następującą strukturę:

    UK/Planes/BA1293/Engine1/2017/08/11/12/

Istnieje ważny powód, aby umieścić datę na końcu struktury folderów. Jeśli chcesz zablokować niektóre regiony lub tematy dla użytkowników/grup, możesz to łatwo zrobić za pomocą uprawnień POSIX. W przeciwnym razie, jeśli istniała potrzeba ograniczenia określonej grupy zabezpieczeń do wyświetlania tylko danych w Wielkiej Brytanii lub niektórych płaszczyznach, ze strukturą dat z przodu wymagane byłoby oddzielne uprawnienie dla wielu folderów w folderze co godzinę. Ponadto posiadanie struktury daty z przodu gwałtownie zwiększyłoby liczbę folderów w miarę upływu czasu.

### <a name="batch-jobs-structure"></a>Struktura zadań wsadowych

Od wysokiego poziomu, powszechnie stosowane podejście w przetwarzaniu wsadowym jest do gruntów danych w folderze "w". Następnie po przetworzeniu danych umieść nowe dane w folderze "out", aby procesy podrzędne można wykorzystać. Ta struktura katalogów jest czasami postrzegana dla zadań, które wymagają przetwarzania na poszczególnych plikach i może nie wymagać masowo równoległego przetwarzania przez duże zestawy danych. Podobnie jak zalecana powyżej struktura IoT, dobra struktura katalogów ma foldery na poziomie nadrzędnym dla takich rzeczy, jak region i tematy (na przykład organizacja, produkt/producent). Ta struktura pomaga w zabezpieczaniu danych w całej organizacji i lepszym zarządzaniu danymi w obciążeniach. Ponadto należy wziąć pod uwagę datę i godzinę w strukturze, aby umożliwić lepszą organizację, filtrowane wyszukiwania, zabezpieczenia i automatyzację w przetwarzaniu. Poziom szczegółowości struktury daty zależy od interwału, w którym dane są przekazywane lub przetwarzane, takich jak co godzinę, codziennie lub nawet co miesiąc.

Czasami przetwarzanie plików kończy się niepowodzeniem z powodu uszkodzenia danych lub nieoczekiwanych formatów. W takich przypadkach struktura katalogów może korzystać z **/bad** folder, aby przenieść pliki do dalszej inspekcji. Zadanie wsadowe może również obsługiwać raportowanie lub powiadamianie o tych *złych* plikach do ręcznej interwencji. Należy wziąć pod uwagę następującą strukturę szablonu:

    {Region}/{SubjectMatter(s)}/In/{yyyy}/{mm}/{dd}/{hh}/
    {Region}/{SubjectMatter(s)}/Out/{yyyy}/{mm}/{dd}/{hh}/
    {Region}/{SubjectMatter(s)}/Bad/{yyyy}/{mm}/{dd}/{hh}/

Na przykład firma marketingowa otrzymuje codzienne wyciągi danych z aktualizacji klientów od swoich klientów w Ameryce Północnej. Może wyglądać jak następujący fragment kodu przed i po przetworzeniu:

    NA/Extracts/ACMEPaperCo/In/2017/08/14/updates_08142017.csv
    NA/Extracts/ACMEPaperCo/Out/2017/08/14/processed_updates_08142017.csv

W typowym przypadku dane wsadowe przetwarzane bezpośrednio do baz danych, takich jak Hive lub tradycyjnych baz danych SQL, nie ma potrzeby **/in** lub **/out** folderu, ponieważ dane wyjściowe już przechodzi do oddzielnego folderu dla tabeli Hive lub zewnętrznej bazy danych. Na przykład codzienne wyciągi od klientów wyląduje do ich odpowiednich folderów, a aranżacja przez coś takiego jak Azure Data Factory, Apache Oozie lub Apache Airflow wyzwoli codzienne zadanie hive lub spark do przetwarzania i zapisywania danych w tabeli hive.

## <a name="next-steps"></a>Następne kroki

* [Omówienie usługi Azure Data Lake Storage Gen1](data-lake-store-overview.md)
* [Kontrola dostępu w usłudze Azure Data Storage Gen1](data-lake-store-access-control.md)
* [Zabezpieczenia w usłudze Azure Data Storage Gen1](data-lake-store-security-overview.md)
* [Dostrajanie usługi Azure Data Lake Storage Gen1 pod kątem wydajności](data-lake-store-performance-tuning-guidance.md)
* [Wskazówki dotyczące dostrajania wydajności dotyczące korzystania z usługi HDInsight Spark w usłudze Azure Data Lake Storage Gen1](data-lake-store-performance-tuning-spark.md)
* [Wskazówki dotyczące dostrajania wydajności dotyczące korzystania z gałęzi usługi HDInsight w usłudze Azure Data Lake Storage Gen1](data-lake-store-performance-tuning-hive.md)
* [Tworzenie klastrów usługi HDInsight przy 1](data-lake-store-hdinsight-hadoop-use-portal.md)
