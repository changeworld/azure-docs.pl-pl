---
title: Najlepsze rozwiązania dotyczące korzystania z usługi Azure Data Lake Store | Dokumentacja firmy Microsoft
description: Dowiedz się, najlepsze rozwiązania dotyczące wprowadzanie danych, Data zabezpieczeń i wydajności powiązane z użyciem usługi Azure Data Lake Store
services: data-lake-store
documentationcenter: ''
author: sachinsbigdata
manager: jhubbard
editor: cgronlun
ms.service: data-lake-store
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 03/02/2018
ms.author: sachins
ms.openlocfilehash: 7493c10407bfe83bdc7277c49dae1a7e9d7c39f2
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2018
---
# <a name="best-practices-for-using-azure-data-lake-store"></a>Najlepsze rozwiązania dotyczące używania usługi Azure Data Lake Store
W tym artykule opisano najważniejsze wskazówki i informacje dotyczące pracy z usługi Azure Data Lake Store. Ten artykuł zawiera informacje dotyczące zabezpieczeń, wydajności, odporności i monitorowania usługi Data Lake Store. Przed usługi Data Lake Store pracę z danymi big naprawdę w usługach, takich jak usługa Azure HDInsight był zbyt złożony. Konieczne było współdzielenie danych między wiele kont magazynu obiektów Blob, aby petabajt magazynu i optymalnej wydajności, które rozwijają się może zostać osiągnięty. Z usługi Data Lake Store większość stałych limitów rozmiaru i wydajności zostaną usunięte. Jednak nadal istnieją pewne kwestie, które w tym artykule omówiono tak, aby uzyskać najlepszą wydajność dzięki usłudze Data Lake Store. 

## <a name="security-considerations"></a>Zagadnienia związane z zabezpieczeniami

Azure Data Lake Store udostępnia dostępu POSIX formanty i szczegółową inspekcję dla użytkowników usługi Azure Active Directory (Azure AD), grupy i nazwy główne usług. Można ustawić tych metod kontroli dostępu do istniejących plików i folderów. Ustawienia kontroli dostępu można również tworzyć wartości domyślnych, które można stosować do nowych plików lub folderów. Gdy uprawnienia do istniejących folderów i obiektów podrzędnych, uprawnienia muszą być rekursywnie propagowany dla każdego obiektu. Jeśli istnieje wiele plików, propagowanie uprawnień może zająć dużo czasu. Czas poświęcony na mogą należeć do zakresu między obiektami 30 – 50 przetwarzanych na sekundę. W związku z tym odpowiednio zaplanować grupy folderów struktury i użytkownika. W przeciwnym razie może spowodować nieprzewidziane opóźnienia i problemy podczas pracy z danymi. 

Założono, że folder o 100 000 obiektów podrzędnych. Jeśli nie podejmiesz dolna granica 30 obiektów przetworzonych na sekundę, aby zaktualizować uprawnienia do całego folderu może zająć godzinę. Więcej informacji na temat listy ACL magazynu Lake danych są dostępne pod adresem [kontroli dostępu w usłudze Azure Data Lake Store](data-lake-store-access-control.md). Zwiększonej wydajności dla przypisywanie rekursywnie listy ACL można użyć narzędzie wiersza polecenia Azure Data Lake. Narzędzie tworzy wiele wątków i logikę nawigacji cykliczne Szybkie stosowanie listy kontroli dostępu do milionów plików. Narzędzie to jest dostępne dla systemów Linux i Windows i [dokumentacji](https://github.com/Azure/data-lake-adlstool) i [pobiera](http://aka.ms/adlstool-download) dla tego narzędzia można znaleźć w witrynie GitHub. Te tego samego ulepszenia wydajności można włączyć za pomocą własnych narzędzi napisany za pomocą usługi Data Lake Store [.NET](data-lake-store-data-operations-net-sdk.md) i [Java](data-lake-store-get-started-java-sdk.md) zestawów SDK.

### <a name="use-security-groups-versus-individual-users"></a>Przy użyciu grup zabezpieczeń lub pojedynczych użytkowników 

Podczas pracy z danymi big data w usłudze Data Lake Store, najprawdopodobniej nazwy głównej usługi jest stosowane do umożliwienia usług, takich jak Azure HDInsight do pracy z danymi. Jednak może być przypadkach, gdy muszą dostęp do danych, jak również przez poszczególnych użytkowników. W takiej sytuacji należy używać usługi Azure Active Directory [grup zabezpieczeń](data-lake-store-secure-data.md#create-security-groups-in-azure-active-directory) zamiast przypisywać poszczególnych użytkowników do plików i folderów. 

Gdy grupa zabezpieczeń są przypisywane uprawnienia, dodawanie lub usuwanie użytkowników z grupy nie wymaga żadnych aktualizacji do usługi Data Lake Store. Pomaga to także zapewnić nie przekraczają limit [32 dostępu i domyślne listy ACL](../azure-subscription-service-limits.md#data-lake-store-limits) (obejmuje cztery listy ACL stylu POSIX, których zawsze są skojarzone z każdym plików i folderów: [użytkownik będący właścicielem](data-lake-store-access-control.md#the-owning-user), [będący właścicielem grupy](data-lake-store-access-control.md#the-owning-group), [maska](data-lake-store-access-control.md#the-mask-and-effective-permissions)i innych).

### <a name="security-for-groups"></a>Zabezpieczeń dla grup 

Zgodnie z opisem, kiedy użytkownicy będą potrzebować dostępu do usługi Data Lake Store najlepiej użyć grup zabezpieczeń usługi Azure Active Directory. Niektóre zalecane grupy do może być **ReadOnlyUsers**, **WriteAccessUsers**, i **FullAccessUsers** dla elementu głównego konta, a nawet oddzielić te klucza podfoldery. Jeśli istnieją inne przewidywanego grup użytkowników, którzy mogą zostać później dodane, ale nie została zidentyfikowana jeszcze, należy rozważyć, tworzenie grup zabezpieczeń fikcyjny, które mają dostęp do określonych folderów. Używanie grupy zabezpieczeń zapewnia później konieczne długi czas przetwarzania przypisywanie nowe uprawnienia do tysięcy plików. 

### <a name="security-for-service-principals"></a>Zabezpieczenia dla nazwy główne usług 

Nazwy główne usług Azure Active Directory są zazwyczaj używane przez usługi, takie jak Azure HDInsight uzyskują dostęp do danych w usłudze Data Lake Store. W zależności od wymagań w zakresie dostępu przez wiele obciążeń może być pewne kwestie dotyczące zapewnienia bezpieczeństwa w organizacji i poza nią. Dla wielu klientów odpowiednie może być jednego podmiotu zabezpieczeń usługi Azure Active Directory i może mieć pełne uprawnienia w katalogu głównym usługi Data Lake Store. Innych klientów może wymagać wielu klastrów z innej usługi podmiotów zabezpieczeń, gdy jeden klaster ma pełny dostęp do danych i inny klaster z dostępem tylko do odczytu. Podobnie jak w przypadku grup zabezpieczeń, można rozważyć wprowadzenie nazwy głównej usługi dla każdego scenariusza (odczytu, zapisu, pełny) zakładano po utworzeniu konta usługi Data Lake Store. 

### <a name="enable-the-data-lake-store-firewall-with-azure-service-access"></a>Włącz zaporę usługi Data Lake Store z dostępem do usługi Azure 

Data Lake Store obsługuje opcję włączenia zapory i ograniczenie dostępu tylko do usług platformy Azure, co jest zalecane dla mniejszych wektor ataku przed atakami z zewnątrz. Zapory można włączyć na koncie usługi Data Lake Store w portalu Azure za pomocą **zapory** > **włączenia zapory (dalej)** > **zezwolić na dostęp do usług platformy Azure**  opcje.  

![Ustawienia w usłudze Data Lake Store zapory](./media/data-lake-store-best-practices/data-lake-store-firewall-setting.png "ustawienia w usłudze Data Lake Store zapory")

Po Zapora jest włączona, tylko Azure usług, takich jak usługi HDInsight, fabryki danych, Magazyn danych SQL, itp. mają dostęp do usługi Data Lake Store. Z powodu translatora adresów sieciowych wewnętrzny używany przez platformę Azure zapory usługi Data Lake Store nie obsługuje ograniczania określonej usługi za pomocą adresu IP i jest przeznaczona tylko dla ograniczenia punktów końcowych poza platformą Azure, takich jak lokalnie. 

## <a name="performance-and-scale-considerations"></a>Zagadnienia dotyczące wydajności i skali

Jedną z najbardziej zaawansowanych funkcji usługi Data Lake Store jest usuwa limity stałe na przepływność danych. Usuwanie limitów umożliwia klientom wzrostu ich rozmiar danych i wymagania dotyczące wydajności wraz z bez konieczności niezależnego fragmentu danych. Jest jednym z najważniejszych zagadnień do optymalizacji wydajności usługi Data Lake Store, wykonuje najlepiej po nadaniu równoległości.

### <a name="improve-throughput-with-parallelism"></a>Zwiększyć przepustowość z równoległości 

Należy wziąć pod uwagę, podając 8 12 wątków na podstawowe najbardziej optymalnej przepływności odczytu/zapisu. Jest to spowodowane blokowania odczyt/zapis w jednym wątku i więcej wątków można zezwolić na wyższe współbieżności na maszynie Wirtualnej. Aby upewnić się, czy poziomy są w dobrej kondycji i równoległości można zwiększyć, należy monitorować użycie procesora CPU maszyny Wirtualnej.   

### <a name="avoid-small-file-sizes"></a>Unikaj małe rozmiary plików

Uprawnień POSIX i inspekcji w usłudze Data Lake Store jest dostarczany z obciążenie stała się oczywista podczas pracy z wiele małych plików. Jako najlepsze rozwiązanie należy partii danych do większych plików i zapisywanie tysiące lub miliony małych plików w usłudze Data Lake Store. Unikanie małe rozmiary plików mają wiele korzyści, takich jak:

* Obniżenia uwierzytelniania kontroli przez wielu plików
* Zmniejszona połączenia otwartych plików
* Kopiowania/replikacji szybsze
* Mniejszą liczbę plików do przetworzenia podczas aktualizowania uprawnień POSIX magazynu Lake danych 

W zależności od tego, jakie usługi i obciążeń z danych dobrym zakres, należy wziąć pod uwagę rozmiary plików to 256 MB do 1 GB, najlepiej nie będą poniżej 100 MB lub ponad 2 GB. Jeśli rozmiary plików nie można umieścić w partii, gdy kierowanych do usługi Data Lake Store, może być zadanie oddzielne kompaktowanie, łączącą tych plików w nich większe. Aby uzyskać więcej informacji i zaleceń na rozmiary plików i organizowania danych w usłudze Data Lake Store, zobacz [struktury zestawu danych](data-lake-store-performance-tuning-guidance.md#structure-your-data-set). 

### <a name="large-file-sizes-and-potential-performance-impact"></a>Duże rozmiary plików i potencjalnego wpływu na wydajność 

Mimo że usługa Data Lake Store obsługuje dużych plików petabajtów rozmiar, aby zapewnić optymalną wydajność i w zależności od procesu odczytywanie danych, może nie być idealne rozwiązanie Przejdź średnio ponad 2 GB. Na przykład w przypadku korzystania z **narzędzia Distcp** kopiowanie danych między lokalizacjami lub różnych kont magazynu, pliki są najwyższy poziom szczegółowości używany do określenia zadania mapy. Tak Jeśli kopiujesz 10 plików, które są 1 TB, maksymalnie 10 mapowań są przydzielone. Ponadto jeśli masz wiele plików z mapowań przypisane początkowo mapowań działać równolegle przenoszenia dużych plików. Jednak po uruchomieniu zadania zakończyć w dół tylko kilka mapowań pozostają przydzielone i można zostać zatrzymane z jednym odwzorowaniem, przypisana do dużych plików. Microsoft zostało przesłane ulepszenia narzędzia Distcp, aby rozwiązać ten problem w przyszłych wersjach platformy Hadoop.  

Inny przykład wziąć pod uwagę to przy użyciu usługi Azure Data Lake Analytics z usługą Data Lake Store. W zależności od przetwarzania programach wyodrębnianie, niektóre pliki, których nie można podzielić (na przykład, XML, JSON) może niekorzystnie wpłynąć na wydajność, jeśli wartość jest większa niż 2 GB. W przypadkach, gdy pliki mogą być dzielone przez ekstraktor (na przykład, CSV) duże pliki są preferowane.

### <a name="capacity-plan-for-your-workload"></a>Planowanie pojemności dla obciążenia 

Azure Data Lake Store usuwa twardym we/wy ograniczenie, które są umieszczone na kontach magazynu obiektów Blob. Istnieją jednak limity nadal zmienne, które należy wziąć pod uwagę. Domyślne limity ograniczania przepustowości wejście/wyjście zaspokoić potrzeby większości scenariuszy. Jeśli obciążenie musi mieć zwiększenie limitów, współpracować z pomocy technicznej firmy Microsoft. Ponadto przyjrzeć się limitów fazie Weryfikacja koncepcji, aby limity ograniczania przepustowości We/Wy nie są osiągane w środowisku produkcyjnym. W takim przypadku konieczność oczekiwania Ręczne zwiększanie zespołu inżynieryjnego firmy Microsoft. W przypadku ograniczania przepustowości we/wy, usługi Azure Data Lake Store zwraca błąd o kodzie 429, a w idealnym przypadku należy wykonać ponownie przy użyciu zasad odpowiednich wykładniczego wycofywania. 

### <a name="optimize-writes-with-the-data-lake-store-driver-buffer"></a>Optymalizacja "zapisuje" z buforu sterownika usługi Data Lake Store 

Aby zoptymalizować wydajność i zmniejszyć liczbę IOPS podczas zapisywania do usługi Data Lake Store z platformy Hadoop, wykonywać operacje zapisu maksymalnie zbliżony rozmiar buforu sterownika usługi Data Lake Store, jak to możliwe. Nie należy przekracza rozmiar buforu przed opróżnianie, takich jak podczas przesyłania strumieniowego przy użyciu platformy Apache Storm i Spark przesyłania strumieniowego obciążeń. Zapisywanie do usługi Data Lake Store z HDInsight/Hadoop, jest ważne dowiedzieć się, że Data Lake Store ma sterownika z buforu 4 MB. Jak wiele plików sterowników systemu buforu można ręcznie opróżnionych przed osiągnięciem rozmiaru 4 MB. Jeśli nie, natychmiast są opróżnione do magazynu, jeśli kolejnego zapisu przekracza maksymalny rozmiar buforu. Jeśli to możliwe, należy unikać przekroczenie lub znaczących zabezpieczeń bufora podczas synchronizowania/opróżnianie zasad przez liczbę lub czas okna.

## <a name="resiliency-considerations"></a>Zagadnienia związane z odpornością 

Podczas projektowania systemu z usługi Data Lake Store lub usługi w chmurze, należy wziąć pod uwagę wymagania dostępności i odpowiadanie na potencjalne przerw w działaniu usługi. Problem może być lokalizowany do określonego wystąpienia lub nawet region całej, więc planu dla obu jest ważne. W zależności od **celu czasu odzyskiwania** i **cel punktu odzyskiwania** umowy SLA dla obciążenia, możesz wybrać mniej lub bardziej agresywną strategię wysokiej dostępności i odzyskiwania po awarii.

### <a name="high-availability-and-disaster-recovery"></a>Wysoka dostępność i odzyskiwanie po awarii 

Wysokiej dostępności (HA) i odzyskiwania awaryjnego (DR) czasami można łączyć ze sobą, mimo że każda ma nieco inne strategii, zwłaszcza, jeśli chodzi o dane. Data Lake Store obsługuje już 3 x replikacji pod maską chronią przed awariami sprzętu zlokalizowane. Jednak ponieważ replikacji w regionach nie jest wbudowana, należy zarządzać samodzielnie. Podczas tworzenia planu dla wysokiej dostępności, w przypadku przerwanie usługi Obciążenie musi mieć dostęp do najnowszych danych tak szybko, jak to możliwe przełączając za pośrednictwem do oddzielnie zreplikowanego wystąpienia lokalnie lub w regionie nowe.  

W strategii odzyskiwania po awarii Aby przygotować się do nieoczekiwanej awarii krytycznego regionu, należy również ma dane replikowane w innym regionie. Te dane początkowo może być taka sama jak replikowanych danych wysokiej dostępności. Jednak należy również rozważyć wymagania dotyczące krawędzi przypadkach, takich jak uszkodzenie danych, gdzie można utworzyć okresowe migawki wrócił do. W zależności od ważności i rozmiar danych należy wziąć pod uwagę wprowadzanie zmian migawki 1-, 6-i okresy 24-godzinnym w magazynie lokalnym i/lub dodatkowej zgodnie z tolerancji ryzyka. 

Aby zachować odporność danych z usługą Data Lake Store zaleca się replikacja geograficzna dane w oddzielnych region z częstotliwością, który spełnia wymagania wysokiej dostępności i odzyskiwania po awarii w idealnym przypadku co godzinę. Tej częstotliwości replikacji minimalizuje przesunięcia ogromną danych, które mogą mieć konkurencyjnych przepływności wymaga systemu głównego i lepsze cel punktu odzyskiwania (RPO). Ponadto należy rozważyć sposób dla aplikacji za pomocą usługi Data Lake Store automatycznie przełączenia awaryjnego do dodatkowej konta przy użyciu monitorowania wyzwalaczy lub długość nieudanych prób lub co najmniej wysłanie powiadomienia do administratorów ręcznej interwencji. Należy pamiętać, że istnieje zależnościami awarii i oczekiwania dla usługi do trybu online. Jeśli dane nie zakończono replikacji, trybu failover może spowodować ryzyko utraty danych, niespójności lub złożonych scalanie danych. 

Poniżej przedstawiono top trzy zalecane opcje organizowanie replikacji między kontami usługi Data Lake Store i podstawowe różnice między każdym z nich.


|  |Narzędzia Distcp  |Azure Data Factory  |AdlCopy  |
|---------|---------|---------|---------|
|**Limity skalowania**     | Ograniczone przez węzłów procesu roboczego        | Ograniczone przez przenoszenie danych w chmurze maksymalna liczba jednostek        | Związana z jednostki analizy        |
|**Obsługuje kopiowanie delty**     |   Yes      | Nie         | Nie         |
|**Wbudowane aranżacji**     |  Nie (należy użyć zadania powietrza Oozie lub cron)       | Yes        | Nie (Użyj usługi Automatyzacja Azure lub harmonogramu zadań systemu Windows)         |
|**Wielkie litery**     | ADL, HDFS, WASB, S3, GS, CFS        |Wiele, zobacz [łączniki](../data-factory/connector-azure-blob-storage.md).         | ADL do ADL, WASB do ADL (tylko w tym samym regionie)        |
|**Obsługa systemu operacyjnego**     |Dowolnego systemu operacyjnego działającej usługi Hadoop         | ND          | Windows 10         |
   

### <a name="use-distcp-for-data-movement-between-two-locations"></a>Za pomocą narzędzia Distcp do przenoszenia danych między dwiema lokalizacjami 

Skrót kopia rozproszonych, narzędzia Distcp jest narzędzie wiersza polecenia systemu Linux, które pochodzą z platformą Hadoop i umożliwia przenoszenie danych rozproszonych między dwiema lokalizacjami. Data Lake Store, system plików HDFS, WASB lub S3, może być dwóch lokalizacjach. To narzędzie korzysta zadań MapReduce na klastra usługi Hadoop (na przykład usługi HDInsight) do skalowania w poziomie na wszystkich węzłach. Narzędzia Distcp jest traktowany jako najszybszy sposób przenoszenia danych big data bez specjalnych sieci kompresji urządzenia. Narzędzia Distcp udostępnia opcję, aby aktualizować tylko różnice między dwiema lokalizacjami, Automatyczne ponawianie prób dojść, jak również dynamiczne skalowanie zasobów obliczeniowych. Ta metoda jest niezwykle wydajnych, gdy chodzi o replikowanie elementów, jak Hive/Spark tabel, które może mieć wielu dużych plików w jednym katalogu, i chcesz skopiować modyfikacji danych. Z tego względu narzędzia Distcp jest najbardziej zalecanym narzędziem do kopiowanie danych między magazynów danych big data. 

Zadania kopiowania może zostać wyzwolone przez Apache Oozie przepływów pracy za pomocą częstotliwość lub wyzwalacze danych, a także zadań cron systemu Linux. Dla zadań znacznym replikacji zalecane jest aż oddzielne klastra HDInsight Hadoop, które mogą być dopasowane i skalowanie specjalnie dla zadania kopiowania. Dzięki temu, że zadania kopiowania nie zakłóca krytyczne zadania. Jeśli z usługą Replikacja od częstotliwości dostatecznie szerokie, klastra nawet będzie to możliwe w dół między każdym zadaniu. Jeśli do trybu failover w regionie pomocniczym, upewnij się, że inny klaster jest również przejścia w regionie pomocniczym do replikacji danych nowego podstawowego konta usługi Data Lake Store, po jego wróci do sprawności. Przykłady za pomocą narzędzia Distcp, zobacz [Użyj narzędzia Distcp, aby skopiować dane między obiektach blob magazynu Azure i usługi Data Lake Store](data-lake-store-copy-data-wasb-distcp.md).

### <a name="use-azure-data-factory-to-schedule-copy-jobs"></a>Zaplanuj zadania kopiowania za pomocą fabryki danych Azure 

Fabryka danych Azure można również zaplanować zadania kopiowania za pomocą **działanie kopiowania**, a nawet można skonfigurować na częstotliwość za pośrednictwem **kreatora kopiowania**. Należy pamiętać fabryki danych Azure ma limit jednostki przepływu danych w chmurze (DMUs), a ostatecznie caps przepływności/obliczeniowe dla obciążeń dużej ilości danych. Ponadto fabryki danych Azure aktualnie nie oferuje aktualizacji przyrostowych między kontami usługi Data Lake Store, folderów, na przykład tabele programu Hive wymagałoby to kompletna kopia do replikacji. Zapoznaj się [przewodnik dostrajania działanie kopiowania](../data-factory/v1/data-factory-copy-activity-performance.md) Aby uzyskać więcej informacji o kopiowaniu z fabryką danych. 

### <a name="adlcopy"></a>AdlCopy

AdlCopy to narzędzie wiersza polecenia systemu Windows, które umożliwia kopiowanie danych między dwa konta usługi Data Lake Store tylko w obrębie tego samego regionu. Narzędzie AdlCopy udostępnia opcję autonomicznej lub do korzystania z konta usługi Azure Data Lake Analytics uruchamianie zadania kopiowania. Chociaż pierwotnie został skonstruowany na żądanie kopii w przeciwieństwie do replikacji niezawodne, zawiera inną opcją czy rozproszonej kopiowanie między kont usługi Data Lake Store w ramach tego samego regionu. Niezawodność zaleca się opcja Data Lake Analytics — wersja premium dla dowolnego obciążeń produkcyjnych. Autonomiczna wersja może zwrócić zajęty odpowiedzi i ma ograniczoną skalę i monitorowania. 

Podobnie jak narzędzia Distcp AdlCopy musi być zorkiestrowana przez coś, takich jak usługi Automatyzacja Azure lub harmonogramu zadań systemu Windows. Podobnie jak w przypadku fabryki danych AdlCopy nie obsługuje kopiowania tylko zaktualizowane pliki, ale powoduje ponowne skopiowanie i zastąpienie istniejących plików. Aby uzyskać więcej informacji i przykłady użycia AdlCopy, zobacz [skopiowanie danych z obiektów blob magazynu Azure do usługi Data Lake Store](data-lake-store-copy-data-azure-storage-blob.md).

## <a name="monitoring-considerations"></a>Zagadnienia dotyczące monitorowania 

Data Lake Store zapewnia szczegółowe dzienniki diagnostyczne i inspekcji. Data Lake Store zapewnia niektóre podstawowe metryki w portalu Azure w ramach konta usługi Data Lake Store i Azure Monitor. Dostępność usługi Data Lake Store jest wyświetlana w portalu Azure. Jednak ta metryka są odświeżane co siedem minut i nie można zbadać za pomocą publicznie ujawnionych interfejsu API. Aby uzyskać najbardziej aktualne dostępności konto usługi Data Lake Store, należy uruchomić testy syntetycznych do sprawdzania dostępności. Innych metryk, takich jak użycie pamięci masowej, żądań odczytu/zapisu i wejście/wyjście może potrwać do 24 godzin, aby odświeżyć. Tak więcej metryki aktualne muszą zostać obliczone ręcznie za pomocą narzędzia wiersza polecenia usługi Hadoop i agregację informacji dziennika. Najszybszym sposobem pobrania najnowszych wykorzystania magazynu jest wykonanie tego polecenia systemu plików HDFS z węzłem klastra usługi Hadoop (na przykład węzła głównego):   

    hdfs dfs -du -s -h adl://<adls_account_name>.azuredatalakestore.net:443/

### <a name="export-data-lake-store-diagnostics"></a>Diagnostyka eksportu Data Lake Store 

Najszybszym sposoby uzyskania dostępu do dzienników można wyszukiwać z usługi Data Lake Store jest umożliwienie wysyłania dziennika do **analizy dzienników** w obszarze **diagnostyki** bloku dla konta usługi Data Lake Store. Zapewnia natychmiastowy dostęp do dzienników przychodzące z czasem i filtry zawartości, oraz alerty opcje (e-mail/webhook) wyzwalane w ciągu 15 minut. Aby uzyskać instrukcje, zobacz [podczas uzyskiwania dostępu do dzienników diagnostycznych dla usługi Azure Data Lake Store](data-lake-store-diagnostic-logs.md). 

Więcej alertów w czasie rzeczywistym i większą kontrolę, skąd trafić w dziennikach należy wziąć pod uwagę eksportowanie dzienników do Centrum EventHub Azure, której zawartość można analizować pojedynczo lub za pośrednictwem przedział czasu w celu przesyłania powiadomień w czasie rzeczywistym do kolejki. Oddzielne aplikacji, takich jak [aplikacji logiki](../connectors/connectors-create-api-azure-event-hubs.md) można następnie używać i alerty odpowiedni kanał komunikacji, jak również przedstawia metryki do monitorowania takich narzędzi jak NewRelic, Datadog lub AppDynamics. Alternatywnie, jeśli używasz narzędzia innych firm, takich jak ElasticSearch, możesz wyeksportować dzienniki do magazynu obiektów Blob i użyj [wtyczkę Azure Logstash](https://github.com/Azure/azure-diagnostics-tools/tree/master/Logstash/logstash-input-azureblob) korzystającą z danych na stosie Elasticsearch, Kibana i Logstash (ŁOSI).

### <a name="turn-on-debug-level-logging-in-hdinsight"></a>Włączanie debugowania poziom rejestrowania w usłudze HDInsight 

Jeśli usługi Data Lake Store wysyłania dziennika nie jest włączona, Azure HDInsight udostępnia sposób, aby włączyć [klienta rejestrowania dla usługi Data Lake Store](data-lake-store-performance-tuning-mapreduce.md) za pomocą narzędzia log4j. Należy ustawić następującą właściwość w **Ambari** > **YARN** > **Config** > **zaawansowane narzędzia log4j yarn konfiguracje**: 

    log4j.logger.com.microsoft.azure.datalake.store=DEBUG 

Gdy właściwość jest ustawiona, i węzły są ponownie uruchamiane, diagnostyka usługi Data Lake Store jest zapisywane do dzienników YARN w węzłach (/tmp/<user>/yarn.log), a ważne informacje, takie jak błędy lub ograniczenia przepustowości (kod błędu HTTP 429) mogą być monitorowane. Tych informacji można również monitorować analizy dzienników lub wszędzie tam, gdzie dzienniki są wysyłane do w [diagnostyki](data-lake-store-diagnostic-logs.md) bloku konta usługi Data Lake Store. Zalecane jest co najmniej włączenia funkcji dziennika po stronie klienta lub korzystanie z opcji z usługą Data Lake Store operacyjne widoczność i ułatwiają debugowanie wysyłania dziennika.

### <a name="run-synthetic-transactions"></a>Uruchom transakcji syntetycznych 

Metryki dostępności usługi Data Lake Store w portalu Azure ma obecnie okna 7-minutowy odświeżania. Ponadto nie można zbadać, za pomocą publicznie ujawnionych interfejsu API. W związku z tym zaleca się tworzenia podstawowej aplikacji, która jest do usługi Data Lake Store można podać maksymalnie minuty dostępności transakcji syntetycznych. Przykładem może być tworzenie, zadania WebJob, aplikacji logiki lub aplikacji funkcji platformy Azure do wykonywania odczytu, tworzenie i zaktualizuj względem usługi Data Lake Store i wysyłać wyniki do rozwiązania do monitorowania. Operacje można wykonać w folderze tymczasowym i następnie jest usuwany po przeprowadzeniu testu, które mogą być uruchamiane co 30 – 60 sekund, w zależności od wymagań.

## <a name="directory-layout-considerations"></a>Zagadnienia dotyczące układu katalogu

Po początkowej danych do usługi data lake, ważne jest, aby wstępnie planować struktury danych, tak aby zabezpieczeń, partycjonowanie i przetwarzania może być wykorzystana efektywnie. Wiele poniższe zalecenia mogą służyć czy za pomocą usługi Azure Data Lake Store, magazynu obiektów Blob lub system plików HDFS. Obciążenia ma inne wymagania na jak danych jest używany, ale poniżej przedstawiono niektóre typowe układy wziąć pod uwagę podczas pracy z IoT i partii scenariuszy.

### <a name="iot-structure"></a>Struktura IoT 

W IoT obciążeń może być dużą ilość danych jest wyładowany w magazynie danych, obejmujący wiele produktów, urządzenia, organizacje i klientów. Należy wstępnie planowania układu katalogu dla organizacji, bezpieczeństwo i wydajność przetwarzania danych dla konsumentów strumienia. Szablon ogólnego wziąć pod uwagę mogą być następujące układu: 

    {Region}/{SubjectMatter(s)}/{yyyy}/{mm}/{dd}/{hh}/ 

Na przykład lądowanie dane telemetryczne dla aparatu samolotowy w Zjednoczone Królestwo może wyglądać następującej strukturze: 

    UK/Planes/BA1293/Engine1/2017/08/11/12/ 

Brak ważnych powód, aby umieścić Data na końcu tej struktury folderów. Jeśli chcesz zablokować określonych regionów lub kwestii do użytkowników i grup, następnie możesz łatwo to zrobić przy użyciu uprawnień POSIX. W przeciwnym razie jeśli istnieje potrzeba ograniczenia grupy zabezpieczeń do przeglądania tylko dane UK lub niektórych płaszczyzn, ze strukturą Data na wierzchu oddzielne uprawnienie jest wymagana dla wiele folderów w folderze co godzinę. Ponadto na wierzchu o strukturze Data może znacznie wydłużyć liczba folderów jako czas przejścia.

### <a name="batch-jobs-structure"></a>Struktura zadania wsadowego 

Z wysokiego poziomu podejście często używane w przetwarzaniu wsadowym jest trafić danych w folderze "w". Następnie po przetworzeniu danych Umieść nowe dane do folderu "out" procesy podrzędne korzystać. Ta struktura katalogów jest widoczna czasami dla zadania, które wymagają przetworzenia dla poszczególnych plików i nie może wymagać masowego przetwarzania równoległego na dużych zestawów danych. Jak struktura IoT zalecanych struktury katalogów dobrej ma foldery poziomu nadrzędnego dla czynności takich jak regionu i kwestii (na przykład, organizacji, produktu/producent). Ta struktura ułatwia zabezpieczanie danych w organizacji i lepsze zarządzanie danych w obciążeń. Ponadto należy wziąć pod uwagę daty i godziny w strukturze, aby umożliwić zapewnienia lepszej organizacji, filtrowane wyszukiwania, zabezpieczeń i automatyzacji do przetworzenia. Poziom szczegółowości dla struktury Data zależy od interwału, na którym dane są przekazywane lub przetwarzane, takich jak co godzinę, codziennie lub nawet co miesiąc. 

Czasami pliku przetwarzania zakończy się niepowodzeniem z powodu uszkodzenia danych lub nieoczekiwany format. W takich przypadkach struktura katalogów mogą korzystać z **/zły** folderu do przenoszenia plików do bardziej inspekcji. Zadanie wsadowe może również obsługiwać raportowania lub powiadomienia o tych *zły* pliki ręcznej interwencji. Należy wziąć pod uwagę następujące struktury szablonu: 

    {Region}/{SubjectMatter(s)}/In/{yyyy}/{mm}/{dd}/{hh}/ 
    {Region}/{SubjectMatter(s)}/Out/{yyyy}/{mm}/{dd}/{hh}/ 
    {Region}/{SubjectMatter(s)}/Bad/{yyyy}/{mm}/{dd}/{hh}/ 

Na przykład marketingu firmy odbiera codzienne wyodrębnia dane klienta aktualizacji z klientów w Ameryce Północnej. Może wyglądać podobnie jak poniższy fragment kodu przed i po przetworzeniu: 

    NA/Extracts/ACMEPaperCo/In/2017/08/14/updates_08142017.csv 
    NA/Extracts/ACMEPaperCo/Out/2017/08/14/processed_updates_08142017.csv 
 
W typowych przypadkach dane przetwarzane bezpośrednio do bazy danych, takich jak Hive lub tradycyjne bazy danych SQL, nie ma potrzeby **/in** lub **/out** folderu, ponieważ dane wyjściowe już przechodzi w stan oddzielne folderu tabelę programu Hive lub zewnętrznej bazy danych. Na przykład codzienne wyodrębnia od klientów spowoduje przejście do odpowiednich folderów i aranżacji przez ekran podobny do fabryki danych Azure, Apache Oozie lub powietrza Apache spowoduje wywołanie codzienne zadania Hive lub Spark do przetwarzania i zapisać dane w tabeli programu Hive.

## <a name="next-steps"></a>Kolejne kroki     

* [Omówienie usługi Azure Data Lake Store](data-lake-store-overview.md) 
* [Kontrola dostępu w usłudze Azure Data Lake Store](data-lake-store-access-control.md) 
* [Zabezpieczeń w usłudze Azure Data Lake Store](data-lake-store-security-overview.md)
* [Dostrajanie wydajności usługi Azure Data Lake Store](data-lake-store-performance-tuning-guidance.md)
* [Wskazówki dotyczące korzystania z usługi Azure Data Lake Store przy użyciu HDInsight Spark dostrajania wydajności](data-lake-store-performance-tuning-spark.md)
* [Wskazówki dotyczące korzystania z usługi Azure Data Lake Store przy użyciu HDInsight Hive dostrajania wydajności](data-lake-store-performance-tuning-hive.md)
* [Organizowanie danych za pomocą usługi Azure Data Factory dla usługi Azure Data Lake Store](https://mix.office.com/watch/1oa7le7t2u4ka)
* [Tworzenie klastrów usługi HDInsight z usługą Data Lake Store](data-lake-store-hdinsight-hadoop-use-portal.md) 
