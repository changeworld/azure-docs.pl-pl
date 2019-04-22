---
title: Najlepsze rozwiązania dotyczące korzystania z usługi Azure Data Lake Storage Gen1 | Dokumentacja firmy Microsoft
description: Poznaj najlepsze rozwiązania dotyczące pozyskiwania danych, Data zabezpieczeń i wydajności powiązane z użyciem usługi Azure Data Lake magazynu Gen1 (wcześniej znane jako usługi Azure Data Lake Store)
services: data-lake-store
documentationcenter: ''
author: sachinsbigdata
manager: mtillman
ms.service: data-lake-store
ms.devlang: na
ms.topic: article
ms.date: 06/27/2018
ms.author: sachins
ms.openlocfilehash: 50d0ed644b5afa744e8bce478199079fd4fb7432
ms.sourcegitcommit: c3d1aa5a1d922c172654b50a6a5c8b2a6c71aa91
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/17/2019
ms.locfileid: "59684161"
---
# <a name="best-practices-for-using-azure-data-lake-storage-gen1"></a>Najlepsze rozwiązania dotyczące korzystania z usługi Azure Data Lake Storage Gen1

[!INCLUDE [data-lake-storage-gen1-rename-note.md](../../includes/data-lake-storage-gen1-rename-note.md)]

W tym artykule poznasz najlepsze praktyki i zagadnienia dotyczące pracy z usługi Azure Data Lake Storage Gen1. Ten artykuł zawiera informacje dotyczące zabezpieczeń, wydajność, odporność i monitorowanie Data Lake Storage Gen1. Przed Data Lake Storage Gen1 Praca z danymi naprawdę big data w usług, takich jak Azure HDInsight był zbyt złożony. Trzeba było fragmentować dane na wielu kontach magazynu obiektów Blob, dzięki czemu można osiągnąć petabajtów magazynu i uzyskania optymalnej wydajności tej skali. Za pomocą Data Lake Storage Gen1 większość stałych limitów rozmiaru i wydajności zostaną usunięte. Jednak nadal istnieją pewne zagadnienia, które w tym artykule opisano, dzięki czemu można uzyskać najlepszą wydajność dzięki Data Lake Storage Gen1.

## <a name="security-considerations"></a>Zagadnienia związane z zabezpieczeniami

Usługa Azure Data Lake Storage Gen1 oferty POSIX kontrole dostępu i szczegółową inspekcję dla użytkowników usługi Azure Active Directory (Azure AD), grupy i jednostki usługi. Można ustawić te kontroli dostępu do istniejących plików i folderów. Kontroli dostępu można również tworzyć wartości domyślnych, które można stosować do nowych plików lub folderów. Gdy uprawnienia do istniejących folderów i obiektów podrzędnych, uprawnienia muszą być rekursywnie propagowany dla każdego obiektu. W przypadku dużej liczby plików propagowanie uprawnień może zająć dużo czasu. Czas poświęcony może wynosić między obiektami 30 – 50 przetwarzanych na sekundę. W związku z tym odpowiednio planować grup folder struktury i użytkownika. W przeciwnym razie może to spowodować nieprzewidziane opóźnienia i problemy podczas pracy z danymi.

Załóżmy, że masz folder z 100 000 obiektów podrzędnych. Jeśli nie podejmiesz dolna granica 30 obiektów przetwarzanych na sekundę, aby zaktualizować uprawnienia do całego folderu może potrwać godzinę. Szczegółowe informacje na temat Data Lake Storage Gen1 listy kontroli dostępu są dostępne pod adresem [kontrola dostępu w usługach Azure Data Lake Storage Gen1](data-lake-store-access-control.md). W celu zwiększenia wydajności dotyczące przypisywania rekursywnie list ACL służy narzędzie wiersza polecenia do usługi Azure Data Lake. Narzędzie tworzy wiele wątków i logiki nawigacji cykliczne Szybkie stosowanie list ACL do milionów plików. Narzędzie to jest dostępne dla systemów Linux i Windows, a [dokumentacji](https://github.com/Azure/data-lake-adlstool) i [pliki do pobrania](https://aka.ms/adlstool-download) dla tego narzędzia można znaleźć w witrynie GitHub. Ten sam usprawnienia dotyczące wydajności może być włączana przy własnych narzędzi napisany w języku Data Lake Storage Gen1 [.NET](data-lake-store-data-operations-net-sdk.md) i [Java](data-lake-store-get-started-java-sdk.md) zestawów SDK.

### <a name="use-security-groups-versus-individual-users"></a>Przy użyciu grup zabezpieczeń i użytkowników indywidualnych

Podczas pracy z danymi big data w Data Lake Storage Gen1, najprawdopodobniej jednostki usługi służy do usług, takich jak Azure HDInsight, aby pracować z danymi. Jednak może być przypadki, w którym poszczególni użytkownicy muszą mieć dostęp do danych, jak również. W takich przypadkach należy użyć usługi Azure Active Directory [grup zabezpieczeń](data-lake-store-secure-data.md#create-security-groups-in-azure-active-directory) zamiast przypisywanie poszczególnych użytkowników do plików i folderów.

Po przypisaniu grupie zabezpieczeń uprawnienia dodawania lub usuwania użytkowników z grupy nie wymaga żadnych aktualizacji Data Lake Storage Gen1. Ułatwia to również, upewnij się, nie może przekraczać limit [32 dostępu i domyślnej listy ACL](../azure-subscription-service-limits.md#data-lake-store-limits) (obejmuje to cztery listy ACL stylu POSIX, które zawsze są skojarzone z każdego pliku i folderu: [użytkownik będący właścicielem](data-lake-store-access-control.md#the-owning-user), [grupa będąca właścicielem](data-lake-store-access-control.md#the-owning-group), [maska](data-lake-store-access-control.md#the-mask)i inne).

### <a name="security-for-groups"></a>Zabezpieczenia dla grup

Zgodnie z opisem, kiedy użytkownicy muszą mieć dostęp do Data Lake Storage Gen1, najlepiej jest używać grup zabezpieczeń usługi Azure Active Directory. Niektóre zalecane grup zaczynać może być **ReadOnlyUsers**, **WriteAccessUsers**, i **FullAccessUsers** dla głównego konta, a nawet oddzielić te klucza podfoldery. Jeśli istnieją inne przewidywanego grup użytkowników, które mogą być dodawane później, ale nie zostały zidentyfikowane jeszcze, należy rozważyć, tworzenie grup fikcyjnego z rolą zabezpieczeń, które mają dostęp do niektórych folderów. Przy użyciu grupy zabezpieczeń gwarantuje, że później nie trzeba długo czas przetwarzania do przypisywania nowe uprawnienia do tysięcy plików.

### <a name="security-for-service-principals"></a>Zabezpieczenia dla jednostki usługi

Jednostki usługi w usłudze Azure Active Directory są zwykle używane przez usługi takie jak Azure HDInsight na dostęp do danych w Data Lake Storage Gen1. W zależności od wymagań dostępu przez wiele obciążeń może być pewne zagadnienia w celu zapewnienia bezpieczeństwa wewnątrz lub na zewnątrz organizacji. Dla wielu klientów pojedynczej jednostki usługi Azure Active Directory może być odpowiednia i może mieć pełne uprawnienia w katalogu głównym konta Data Lake Storage Gen1. Inni klienci mogą wymagać wielu klastrów z podmiotami innej usługi, w której jeden klaster ma pełny dostęp do danych i inny klaster z dostępem tylko do odczytu. Podobnie jak w przypadku grup zabezpieczeń, można rozważyć wprowadzania nazwy głównej usługi dla każdego scenariusza (odczytu, zapisu, pełny) przewidywanym po utworzeniu konta Data Lake Storage Gen1.

### <a name="enable-the-data-lake-storage-gen1-firewall-with-azure-service-access"></a>Włącz zaporę Data Lake Storage Gen1 z dostępem do usług platformy Azure

Data Lake Storage Gen1 obsługuje możliwość Włączanie zapory i ograniczanie dostępu tylko do usług platformy Azure, co jest zalecane w przypadku mniejszych ataku, przed atakami z zewnątrz. Zapory można włączyć w witrynie Azure portal za pomocą konta Data Lake Storage Gen1 **zapory** > **włączenia zapory (dalej)** > **zezwolić na dostęp do platformy Azure usługi** opcje.

![Ustawienia w Data Lake Storage Gen1 zapory](./media/data-lake-store-best-practices/data-lake-store-firewall-setting.png "ustawienia w Data Lake Storage Gen1 zapory")

Gdy Zapora jest włączona, tylko platforma Azure usług, takich jak HDInsight, Data Factory, usługa SQL Data Warehouse, itp., mają dostęp do Data Lake Storage Gen1. Z powodu translatora adresów sieciowych wewnętrzne używane przez platformę Azure Data Lake Storage Gen1 zapory nie obsługuje ograniczanie określonych usług według adresów IP i jest przeznaczona wyłącznie dla ograniczenia punkty końcowe poza platformą Azure, takich jak w środowisku lokalnym.

## <a name="performance-and-scale-considerations"></a>Zagadnienia dotyczące wydajności i skali

Jest jednym z najbardziej zaawansowanych funkcji Data Lake Storage Gen1, usuwa twardych limity przepływność danych. Usuwanie ograniczenia umożliwia klientom i zwiększanie ich rozmiar danych i wraz z wymaganiami dotyczącymi wydajności bez konieczności fragmentów danych. Jest jedną z najważniejszych zagadnień do optymalizacji wydajności Data Lake Storage Gen1, wykonuje najlepiej po nadaniu równoległości.

### <a name="improve-throughput-with-parallelism"></a>Zwiększyć przepływność przy użyciu równoległości

Należy rozważyć nadanie 8 12 wątków na każdy rdzeń, aby uzyskać najbardziej optymalną przepływności odczytu/zapisu. Jest to spowodowane blokowania operacji odczytu/zapisu w jednym wątku, a więcej wątków umożliwia zapewnienie większej współbieżności na maszynie Wirtualnej. Aby upewnić się, że poziomy są w dobrej kondycji i można zwiększyć równoległość, pamiętaj monitorować użycie procesora CPU maszyny Wirtualnej.

### <a name="avoid-small-file-sizes"></a>Należy unikać małe rozmiary plików

Uprawnień POSIX i inspekcji w Data Lake Storage Gen1 jest dostarczany z obciążeniem, który staje się jasne, pracując z wielu małych plików. Najlepszym rozwiązaniem należy partii danych do większych plików i zapisywanie tysięcy lub milionów małych plików Data Lake Storage Gen1. Unikanie małe rozmiary plików mogą mieć takich jak wiele korzyści:

* Obniżanie kontroli uwierzytelniania na wiele plików
* Zmniejszona połączenia otwartego pliku
* Kopiowanie/replikacji szybciej
* Mniej plików do przetworzenia podczas aktualizowania uprawnień Data Lake Storage Gen1 POSIX

W zależności od tego, jakie usługi i obciążenia są przy użyciu danych, dobrym rozmiar wziąć pod uwagę w przypadku plików to 256 MB lub większy. Jeśli rozmiary plików nie partii, gdy kierowanych do Data Lake Storage Gen1, może mieć zadanie kompaktowania oddzielne, które łączy te pliki w większymi. Aby uzyskać więcej informacji i zalecenia dotyczące rozmiarów plików i organizowania danych w Data Lake Storage Gen1, zobacz [struktury zestawu danych](data-lake-store-performance-tuning-guidance.md#structure-your-data-set).

### <a name="large-file-sizes-and-potential-performance-impact"></a>Rozmiary plików o dużym rozmiarze i potencjalnego wpływu na wydajność

Mimo że program Data Lake Storage Gen1 obsługuje duże pliki do poziomu petabajtów rozmiar, aby uzyskać optymalną wydajność i w zależności od procesu, Odczyt danych, może nie być idealne do ponad 2 GB średnio. Na przykład w przypadku korzystania z **Distcp** do skopiowania danych między lokalizacjami lub różnych kont magazynu, pliki są najwyższym poziomem szczegółowości umożliwiają ustalenie zadań mapy. Tak Jeśli kopiujesz 10 plików, które są 1 TB, co najwyżej 10 liczby maperów są przydzielone. Ponadto jeśli masz wiele plików za pomocą liczby maperów przypisane początkowo liczby maperów działać równolegle w celu przenoszenia dużych plików. Jednak ponieważ zadanie zostało uruchomione na wiatru w dół jedynie niewielkiej liczby maperów pozostają przydzielone i może być zapoznaniu się z jednego mapowania, przypisany do dużych plików. Microsoft została przesłana ulepszenia narzędzia Distcp, aby rozwiązać ten problem w przyszłych wersjach usługi Hadoop.

Inny przykład, który należy wziąć pod uwagę to, podczas korzystania z usługi Azure Data Lake Analytics z usługą Data Lake Storage Gen1. W zależności od przetwarzania wykonywane przez ekstraktor, niektóre pliki, które nie mogą być podziału (na przykład, XML, JSON) może niekorzystnie wpłynąć na wydajność, jeśli wartość jest większa niż 2 GB. W przypadkach, w którym plików może zostać podzielony przez ekstraktor (np. CSV) duże pliki są preferowane.

### <a name="capacity-plan-for-your-workload"></a>Planowanie pojemności programu obciążenia

Azure Data Lake Storage Gen1 usuwa twardych we/wy, ograniczenie, które są umieszczone na konta usługi Blob storage. Jednak istnieją limity nadal zmienne, które należy wziąć pod uwagę. Domyślne limity ograniczania przepływności danych przychodzących i wychodzących potrzeb większości scenariuszy. Jeśli obciążenie wymaga mają limity wzrosła, pracować z pomocy technicznej firmy Microsoft. Ponadto sprawdź limity na etapie weryfikacji koncepcji tak, aby limity ograniczania operacji We/Wy nie są osiągane podczas produkcji. Jeśli tak się stanie, wymagać oczekiwania Ręczne zwiększanie od zespołu inżynierów firmy Microsoft. Sytuacji ograniczania operacji We/Wy, Azure Data Lake Storage Gen1 zwraca kod błędu 429, a najlepiej prób przy użyciu zasad odpowiednich wykładniczego wycofywania.

### <a name="optimize-writes-with-the-data-lake-storage-gen1-driver-buffer"></a>Optymalizowanie "zapisuje" Data Lake Storage Gen1 buforu sterownika

Aby zoptymalizować wydajność i zmniejszyć operacje We/Wy podczas zapisywania Data Lake Storage Gen1 z usługi Hadoop, wykonywać operacje zapisu zbliżony rozmiar buforu Data Lake Storage Gen1 sterownika, jak to możliwe. Staraj się nie przekracza rozmiar buforu przed opróżnianie, takich jak podczas przesyłania strumieniowego systemu Apache STORM lub Spark streaming obciążeń. Podczas zapisywania Data Lake Storage Gen1 z usługi HDInsight/Hadoop, jest ważne, aby dowiedzieć się, że Data Lake Storage Gen1 ma sterownika z buforu 4 MB. Podobnie jak wiele sterowników systemu plików tego buforu można było ręcznie opróżnić przed osiągnięciem rozmiaru 4 MB. W przeciwnym razie natychmiast są opróżniany do magazynu, jeśli następnego zapisu przekracza maksymalny rozmiar buforu. W przypadku, gdy jest to możliwe, należy unikać przekroczenia i znaczące zabezpieczeń buforu podczas synchronizowania/opróżnianie zasad przez okno count lub czas.

## <a name="resiliency-considerations"></a>Zagadnienia związane z odpornością

Po utworzeniu architektury systemu, Data Lake Storage Gen1 lub dowolnej usługi w chmurze, należy wziąć pod uwagę swoje wymagania dostępności i odpowiadania na potencjalnych przerw w działaniu usługi. Problem może być lokalizowana do konkretnego wystąpienia lub nawet obejmujących cały region, więc planu dla obu jest ważne. W zależności od **cel czasu odzyskiwania** i **cel punktu odzyskiwania** umowy SLA dla obciążenia, możesz wybrać więcej lub mniej skuteczną strategię wysokiej dostępności i odzyskiwania po awarii.

### <a name="high-availability-and-disaster-recovery"></a>Wysoka dostępność i odzyskiwanie po awarii

Wysoka dostępność (HA) i odzyskiwanie po awarii (DR) czasami można łączyć ze sobą, mimo że każda ma nieco strategii, szczególnie w przypadku, gdy chodzi o dane. Data Lake Storage Gen1 już obsługuje 3 replikacji x kulisy, aby je przed nieprzewidzianymi awariami sprzętu zlokalizowanego. Jednak ponieważ replikacji między regionami nie jest wbudowany, muszą zarządzać to samodzielnie. Podczas tworzenia planu zapewnia wysoką dostępność, w przypadku przerwa w świadczeniu usługi obciążenia musi mieć dostęp do najnowszych danych tak szybko, jak to możliwe, przełączając się za pośrednictwem oddzielnie zreplikowanego wystąpienia, lokalnie lub w nowym regionie.

W strategii odzyskiwania po awarii Aby przygotować się do przypadku mało prawdopodobnego poważnej awarii w regionie, warto również znajdują się dane replikowane w innym regionie. Te dane początkowo może być taka sama jak replikowanych danych o wysokiej dostępności. Jednakże należy również skonfigurować Twoje wymagania w zakresie przypadki brzegowe, takich jak uszkodzenie danych, których możesz chcieć utworzyć okresowe migawki wrócił do. W zależności od ważności i rozmiaru danych należy wziąć pod uwagę stopniowe migawki różnicowej 1 - 6- i 24-godzinny okresy w magazynie lokalnym i/lub dodatkowej zgodnie z tolerancji ryzyka.

Aby zachować odporność danych za pomocą programu Data Lake Storage Gen1 zaleca się replikacja geograficzna danych osobnym regionie z częstotliwością spełniającą Twoje wymagania wysokiej dostępności i odzyskiwania po awarii, najlepiej co godzinę. Tę częstotliwość replikacji minimalizuje przesunięcia duża ilość danych, które mogą mieć potrzeby związane z przepływnością konkurencyjnych za pomocą systemu głównego i lepsze cel punktu odzyskiwania (RPO). Ponadto należy wziąć pod uwagę możliwości aplikacji przy użyciu Data Lake Storage Gen1 automatycznie awaryjnie do pomocniczego konta poprzez monitorowanie wyzwalaczy lub długość nieudanych prób lub co najmniej wysłanie powiadomienia do administratorów i przeznaczone do ręcznej interwencji. Należy pamiętać, że jest kosztem przechodzenie w tryb failover i Oczekiwanie na usługę do trybu online. Jeśli dane nie zakończono replikacji, pracy w trybie failover może spowodować ryzyko utraty danych, niespójności lub złożonych scalanie danych.

Poniżej przedstawiono najważniejsze trzy zalecane opcje organizowanie replikacji między kontami Data Lake Storage Gen1 i podstawowe różnice między każdym z nich.

|  |Narzędzia Distcp  |Azure Data Factory  |Narzędzia AdlCopy  |
|---------|---------|---------|---------|
|**Limity skalowania**     | Ograniczone przez węzły procesu roboczego        | Ograniczone przez przenoszenie danych w chmurze maksymalna liczba jednostek        | Związana z jednostki analizy        |
|**Obsługuje kopiowanie różnic**     |   Yes      | Nie         | Nie         |
|**Wbudowane aranżacji**     |  Nie (używanie zadań programu Oozie powietrza lub usługi cron)       | Yes        | Nie (należy użyć usługi Azure Automation lub harmonogramu zadań Windows)         |
|**Obsługiwanych systemów plików**     | ADL, HDFS, WASB, S3, GS, CFS        |Zobacz wiele, [łączników](../data-factory/connector-azure-blob-storage.md).         | ADL do systemu plików ADL, WASB do systemu plików ADL (tylko w tym samym regionie)        |
|**Obsługa systemu operacyjnego**     |Dowolny system operacyjny uruchomiony usługi Hadoop         | ND          | Windows 10         |

### <a name="use-distcp-for-data-movement-between-two-locations"></a>W przypadku przenoszenia danych między dwiema lokalizacjami za pomocą narzędzia Distcp

Mała w przypadku kopiowania rozproszonej, Distcp jest narzędziem wiersza polecenia systemu Linux, dostarczaną z użyciem usługi Hadoop, która umożliwia przenoszenie danych rozproszonych między dwiema lokalizacjami. Data Lake Storage Gen1, system plików HDFS, WASB lub S3, może być dwóch lokalizacjach. To narzędzie używa zadań MapReduce w klastrze usługi Hadoop (np. HDInsight) do skalowania w poziomie na wszystkich węzłach. Narzędzia Distcp jest traktowany jako najszybszy sposób przenoszenia danych big data bez sieciowych specjalne kompresji urządzeń. Narzędzia Distcp także opcję, aby aktualizować tylko różnice między dwiema lokalizacjami, obsługuje automatyczne ponawianie prób, jak również dynamiczne skalowanie wystąpień obliczeniowych. To podejście jest niezwykle wydajne, gdy chodzi o replikowanie elementów, takich jak tabele programu Hive/Spark, które może mieć wielu dużych plików w jednym katalogu i chcesz skopiować zmodyfikowanych danych. Z tego względu Distcp jest najbardziej zalecanym narzędziem do kopiowania danych między magazynami danych big data.

Zadania kopiowania mogą być wywoływane przez przepływy pracy programu Apache Oozie przy użyciu częstotliwości lub wyzwalacze danych, jak również zadania cron w systemie Linux. W przypadku zadań intensywnie korzystających z replikacji zalecane jest oddzielny klaster HDInsight Hadoop, które mogą być dopasowane skalowanie specjalnie dla zadania kopiowania. Daje to gwarancję, że zadania kopiowania nie zakłócają krytyczne zadania. Jeśli z replikacji z częstotliwością dostatecznie szerokie, klaster nawet umożliwić wyłączenie między każdego zadania. Jeśli przechodzenie w tryb failover do regionu pomocniczego, upewnij się, że inny klaster zostanie również rozszerzona w regionie pomocniczym replikowanie nowych danych do konta usługi Data Lake Storage Gen1 podstawowego, po jego wróci do sprawności. Korzystanie z narzędzia Distcp, zobacz [Użyj Distcp do skopiowania danych między Azure Storage Blobs a Data Lake Storage Gen1](data-lake-store-copy-data-wasb-distcp.md).

### <a name="use-azure-data-factory-to-schedule-copy-jobs"></a>Azure Data Factory służy do planowania zadań kopiowania

Usługa Azure Data Factory można również zaplanować zadania kopiowania przy użyciu **działania kopiowania**, a nawet można skonfigurować pod kątem od częstotliwości za pomocą **kreatora kopiowania**. Należy pamiętać, ma limit jednostek przenoszenia danych w chmurze (DMUs) usługi Azure Data Factory, a ostatecznie caps przepływności/zasoby obliczeniowe na potrzeby obciążeń dużych ilości danych. Ponadto usługi Azure Data Factory obecnie nie oferuje aktualizacji przyrostowych między kontami Data Lake Storage Gen1, więc folderów, na przykład tabele programu Hive wymagałoby to kompletna kopia do replikacji. Zapoznaj się [Przewodnik dostosowywania działania kopiowania](../data-factory/copy-activity-performance.md) więcej informacji na temat kopiowania z usługą Data Factory.

### <a name="adlcopy"></a>Narzędzia AdlCopy

AdlCopy jest narzędziem wiersza polecenia Windows, która umożliwia kopiowanie danych między dwa konta Data Lake Storage Gen1 tylko w obrębie tego samego regionu. Narzędzia AdlCopy udostępnia opcję autonomiczny lub można użyć konta usługi Azure Data Lake Analytics, aby uruchomić zadanie kopiowania. Jeśli został on pierwotnie utworzony na żądanie kopii w przeciwieństwie do niezawodnego replikacji oferuje inną opcję, aby wykonać kopiowanie rozproszone na kontach Data Lake Storage Gen1 w tym samym regionie. Niezawodność zaleca się użyć opcji usługi Data Lake Analytics — wersja premium dla wszystkich obciążeń produkcyjnych. Autonomiczna wersja może zwracać odpowiedzi zajęty i ma ograniczoną skalowania i monitorowania.

Podobnie jak Distcp AdlCopy musi można zarządzać, takich jak usługa Azure Automation lub harmonogram zadań Windows. Podobnie jak w przypadku usługi Data Factory AdlCopy nie obsługuje kopiowania tylko zaktualizowane pliki, ale powoduje ponowne skopiowanie i Nadpisz istniejące pliki. Aby uzyskać więcej informacji i przykłady korzystanie z narzędzia AdlCopy zobacz [kopiowanie danych z obiektów blob usługi Azure Storage do Data Lake Storage Gen1](data-lake-store-copy-data-azure-storage-blob.md).

## <a name="monitoring-considerations"></a>Zagadnienia dotyczące monitorowania

Data Lake Storage Gen1 zapewnia szczegółowe dzienniki diagnostyki i inspekcji. Data Lake Storage Gen1 zawiera niektóre podstawowe metryki w witrynie Azure portal w ramach konta Data Lake Storage Gen1 i w usłudze Azure Monitor. Dostępność usługi Data Lake Storage Gen1 jest wyświetlany w witrynie Azure portal. Jednak ta metryka jest odświeżane co siedem minut i nie może być odpytywana za pomocą publicznie ujawnionych interfejsu API. Aby uzyskać najbardziej aktualne dostępność konta Data Lake Storage Gen1, należy uruchomić syntetycznych testów do sprawdzania dostępności. Inne metryki, takie jak łączne wykorzystanie magazynu, żądań odczytu/zapisu i ruchem przychodzącym/wychodzącym może potrwać do 24 godzin, aby odświeżyć. Dlatego więcej metryk aktualne muszą zostać obliczone ręcznie za pomocą narzędzia wiersza polecenia usługi Hadoop lub agregacji informacji dziennika. Najszybszym sposobem, aby uzyskać najbardziej aktualną wykorzystania pamięci masowej jest uruchamiania tego polecenia systemu plików HDFS z węzłem klastra usługi Hadoop (na przykład węzła głównego):

    hdfs dfs -du -s -h adl://<adlsg1_account_name>.azuredatalakestore.net:443/

### <a name="export-data-lake-storage-gen1-diagnostics"></a>Eksportuj diagnostykę Data Lake Storage Gen1

Najszybszy sposób uzyskiwania dostępu do dzienników można wyszukiwać z Data Lake Storage Gen1 jest włączenie wysyłania dziennika do **usługi Log Analytics** w obszarze **diagnostyki** bloku konta Data Lake Storage Gen1. To zapewnia natychmiastowy dostęp do dzienników przychodzące z czasem i filtrów zawartości, oraz alerty opcje (adres e-mail/element webhook) wyzwolony w ciągu 15-minutowych interwałach. Aby uzyskać instrukcje, zobacz [uzyskiwania dostępu do dzienników diagnostycznych do usługi Azure Data Lake Storage Gen1](data-lake-store-diagnostic-logs.md).

Aby uzyskać więcej alertów w czasie rzeczywistym i większą kontrolę nad gdzie można znaleźć w dziennikach należy wziąć pod uwagę eksportowanie dzienników do Centrum zdarzeń Azure, której zawartość można analizować pojedynczo lub w przedziale czasu w celu przesyłania powiadomień w czasie rzeczywistym do kolejki. Innej aplikacji, takich jak [aplikacji logiki](../connectors/connectors-create-api-azure-event-hubs.md) mogą następnie korzystać i alerty do odpowiedniego kanału komunikacji, jak również przedstawia metryki do monitorowania narzędzi, takich jak usługi NewRelic pomocą usługi Datadog i AppDynamics. Alternatywnie Jeśli używasz narzędzia innych firm, takiego jak usługa ElasticSearch, możesz wyeksportować dzienniki do magazynu obiektów Blob i użyj [wtyczkę Azure Logstash](https://github.com/Azure/azure-diagnostics-tools/tree/master/Logstash/logstash-input-azureblob) korzystającą z danych w stosie usługi Elasticsearch, Kibana i Logstash (ELK).

### <a name="turn-on-debug-level-logging-in-hdinsight"></a>Włącz debugowanie na poziomie rejestrowania w HDInsight

Jeśli Gen1 magazynu jeziora danych wysyłania dziennika nie jest włączona, Azure HDInsight udostępnia sposób, aby włączyć [klienta rejestrowania Data Lake Storage Gen1](data-lake-store-performance-tuning-mapreduce.md) za pośrednictwem mechanizmu log4j. Należy ustawić następującą właściwość w **Ambari** > **YARN** > **Config** > **zaawansowane yarn log4j konfiguracje**:

    log4j.logger.com.microsoft.azure.datalake.store=DEBUG

Gdy właściwość jest ustawiona, i węzły zostaną ponownie uruchomione, Data Lake Storage Gen1 diagnostyki są zapisywane dzienniki usługi YARN w węzłach (/tmp/\<użytkownika\>/yarn.log) oraz ważne szczegóły, takie jak błędy lub ograniczenia przepustowości (kod błędu HTTP 429) może być monitorowane. Informacje te można również monitorować w dzienniki usługi Azure Monitor lub wszędzie tam, gdzie dzienniki są wysyłane do w [diagnostyki](data-lake-store-diagnostic-logs.md) bloku konta Data Lake Storage Gen1. Zalecane jest co najmniej rejestrowanie po stronie klienta lub korzystanie z opcji z Data Lake Storage Gen1 widoczność operacyjną i ułatwiają debugowanie wysyłania dziennika.

### <a name="run-synthetic-transactions"></a>Uruchamianie transakcji syntetycznych

Obecnie metryk dostępności usługi Data Lake Storage Gen1 w witrynie Azure portal ma odświeżania 7-minutowym oknie. Ponadto nie można zbadać, za pomocą publicznie ujawnionych interfejsu API. Dlatego zalecane jest tworzenie podstawowej aplikacji, która używa transakcji syntetycznych do Gen1 magazynu jeziora danych, który oferuje maksymalnie minutę dostępności. Przykładem może być tworzenie zadania WebJob, aplikacja logiki lub aplikacja funkcji platformy Azure można wykonać Odczyt, tworzenie, aktualizowanie względem Data Lake Storage Gen1 i wysyłać wyniki do rozwiązania do monitorowania. Operacje można wykonać w folderze tymczasowym i następnie jest usuwany po testu, który może być uruchamiane co 30 – 60 sekund, w zależności od wymagań.

## <a name="directory-layout-considerations"></a>Zagadnienia dotyczące układu katalogu

Po początkowej danych do usługi data lake, jest ważne, zatem potrzeby wcześniejszego zaplanowania struktury danych, tak aby zabezpieczeń, partycjonowanie i przetwarzania może być wykorzystywana skutecznie. Wiele z następujących zaleceń, należy używać czy za pomocą usługi Azure Data Lake Storage Gen1, magazynu obiektów Blob lub systemu plików HDFS. Obciążenia ma inne wymagania dotyczące danych jest używany, ale poniżej przedstawiono niektóre typowe układy wziąć pod uwagę podczas pracy z usługą IoT i usługi batch scenariuszy.

### <a name="iot-structure"></a>Struktura IoT

W obciążeń IoT mogą być dużym stopniem danych jest otwarta w magazynie danych, która obejmuje wiele produktów, urządzeń, organizacji i klientów. Należy zatem potrzeby wcześniejszego zaplanowania układu katalogu dla organizacji, zabezpieczeń i wydajnego przetwarzania danych dla konsumentów strumienia. Szablon ogólny, który należy wziąć pod uwagę mogą być następujące układu:

    {Region}/{SubjectMatter(s)}/{yyyy}/{mm}/{dd}/{hh}/

Na przykład docelowa danych telemetrycznych silnika w trybie samolotowym w Wielkiej Brytanii może wyglądać następującą strukturę:

    UK/Planes/BA1293/Engine1/2017/08/11/12/

Istnieje ważny powód, aby umieścić daty na końcu struktury folderów. Jeśli chcesz zablokować określonych regionów lub kwestii do użytkowników i grup, następnie możesz łatwo to zrobić przy użyciu uprawnień POSIX. W przeciwnym razie, jeśli było, aby ograniczyć grupę zabezpieczeń do wyświetlania tylko te dane, Wielkiej Brytanii lub niektórych płaszczyzn, ze strukturą daty z przodu oddzielne uprawnienie jest wymagana dla wiele folderów w folderze co godzinę. Ponadto o strukturze daty z przodu może wydłużyć liczba folderów jako czas wystąpił.

### <a name="batch-jobs-structure"></a>Struktura zadania usługi Batch

Z ogólne podejście często używane w przetwarzaniu wsadowym jest dane znajdą się w folderze "in". Następnie po przetworzeniu danych należy umieścić nowe dane do folderu "limit" dla procesów podrzędnych korzystanie z. Ta struktura katalogów jest widoczny czasami dla zadań, które wymagają przetworzenia dla poszczególnych plików i może nie wymagać masowego przetwarzania równoległego w dużych zestawach danych. Podobnie jak struktury IoT, zaleca się powyżej struktura katalogów dobre ma foldery poziomu nadrzędnego, w przypadku elementów, takich jak region i kwestii (na przykład organizacja, produktu i producentów). Ta struktura ułatwia zabezpieczanie danych w organizacji i lepszego zarządzania danymi w Twoich obciążeń. Ponadto należy wziąć pod uwagę Data i godzina w strukturze, aby umożliwić lepszą organizację, wyszukiwanie filtrowane, zabezpieczeń i automatyzacji w czasie przetwarzania. Poziom szczegółowości dla struktury Data jest określany przez interwał, w którym dane są przekazywane lub przetworzone, takich jak co godzinę, codziennie lub nawet co miesiąc.

Czasami pliku przetwarzania się niepowodzeniem z powodu uszkodzenia danych lub nieoczekiwany podzielony na fragmenty. W takich przypadkach strukturę katalogów organizowanych przez **/zły** folder, aby przenieść pliki do bardziej inspekcji. Zadanie wsadowe może również obsługiwać, raportowania lub powiadomienia o tych *zły* pliki ręcznej interwencji. Należy rozważyć następujące struktury szablonu:

    {Region}/{SubjectMatter(s)}/In/{yyyy}/{mm}/{dd}/{hh}/
    {Region}/{SubjectMatter(s)}/Out/{yyyy}/{mm}/{dd}/{hh}/
    {Region}/{SubjectMatter(s)}/Bad/{yyyy}/{mm}/{dd}/{hh}/

Na przykład marketingu firmy odbiera codzienne wyodrębnia dane klienta aktualizacji z klientów w Ameryce Północnej. Może wyglądać jak poniższy fragment kodu przed i po przetworzeniu:

    NA/Extracts/ACMEPaperCo/In/2017/08/14/updates_08142017.csv
    NA/Extracts/ACMEPaperCo/Out/2017/08/14/processed_updates_08142017.csv

W przypadku typowych dane przetwarzane bezpośrednio do bazy danych, takie jak gałąź lub tradycyjnych baz danych SQL, nie ma potrzeby **/in** lub **/out** folderu, ponieważ dane wyjściowe już przechodzi w stan Oddziel folderu dla tabeli programu Hive lub zewnętrznej bazy danych. Na przykład wyodrębnia codzienne od klientów spowoduje przejście do ich odpowiednich folderów i aranżacji, takich jak Azure Data Factory, Apache Oozie lub Apache powietrza powodowało codzienne zadania programu Hive lub Spark do przetwarzania i zapisać dane do tabeli programu Hive.

## <a name="next-steps"></a>Kolejne kroki

* [Omówienie usługi Azure Data Lake Storage Gen1](data-lake-store-overview.md)
* [Kontrola dostępu w usługi Azure Data Lake Storage Gen1](data-lake-store-access-control.md)
* [Zabezpieczenia w usługi Azure Data Lake Storage Gen1](data-lake-store-security-overview.md)
* [Dostosowywanie usługi Azure Data Lake Storage Gen1 wydajności](data-lake-store-performance-tuning-guidance.md)
* [Wskazówki dotyczące korzystania z platformy HDInsight Spark przy użyciu usługi Azure Data Lake Storage Gen1 dostrajania wydajności](data-lake-store-performance-tuning-spark.md)
* [Wskazówki dotyczące korzystania z programu Hive z HDInsight przy użyciu usługi Azure Data Lake Storage Gen1 dostrajania wydajności](data-lake-store-performance-tuning-hive.md)
* [Tworzenie klastrów HDInsight za pomocą programu Data Lake Storage Gen1](data-lake-store-hdinsight-hadoop-use-portal.md)
