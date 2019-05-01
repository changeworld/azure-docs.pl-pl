---
title: Najlepsze rozwiązania dotyczące korzystania z usługi Azure Data Lake Storage Gen2 | Dokumentacja firmy Microsoft
description: Poznaj najlepsze rozwiązania dotyczące pozyskiwania danych, Data zabezpieczeń i wydajności powiązane z użyciem usługi Azure Data Lake magazynu Gen2 (wcześniej znane jako usługi Azure Data Lake Store)
services: storage
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: article
ms.date: 12/06/2018
ms.author: normesta
ms.reviewer: sachins
ms.openlocfilehash: 8b39866b990812913924118c564a5e93f898b1cb
ms.sourcegitcommit: c53a800d6c2e5baad800c1247dce94bdbf2ad324
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/30/2019
ms.locfileid: "64939464"
---
# <a name="best-practices-for-using-azure-data-lake-storage-gen2"></a>Najlepsze rozwiązania dotyczące korzystania z usługi Azure Data Lake Storage Gen2

W tym artykule poznasz najlepsze praktyki i zagadnienia dotyczące pracy z usługi Azure Data Lake Storage Gen2. Ten artykuł zawiera informacje dotyczące zabezpieczeń, wydajność, odporność i monitorowanie Data Lake Storage Gen2. Przed Data Lake Storage Gen2 Praca z danymi naprawdę big data w usług, takich jak Azure HDInsight był zbyt złożony. Trzeba było fragmentować dane na wielu kontach magazynu obiektów Blob, dzięki czemu można osiągnąć petabajtów magazynu i uzyskania optymalnej wydajności tej skali. Za pomocą Data Lake Storage Gen2 większość stałych limitów rozmiaru i wydajności zostaną usunięte. Jednak nadal istnieją pewne zagadnienia, które w tym artykule opisano, dzięki czemu można uzyskać najlepszą wydajność dzięki Data Lake Storage Gen2.

## <a name="security-considerations"></a>Zagadnienia związane z zabezpieczeniami

Azure Data Lake magazynu Gen2 oferuje kontroli dostępu POSIX dla użytkowników, grupy i jednostki usługi Azure Active Directory (Azure AD). Można ustawić te kontroli dostępu do istniejących plików i katalogów. Kontroli dostępu można również utworzyć uprawnienia domyślne, które mogą być automatycznie stosowane do nowych plików lub katalogów. Szczegółowe informacje na temat Data Lake Storage Gen2 listy kontroli dostępu są dostępne pod adresem [kontrola dostępu w usługach Azure Data Lake Storage Gen2](storage-data-lake-storage-access-control.md).

### <a name="use-security-groups-versus-individual-users"></a>Przy użyciu grup zabezpieczeń i użytkowników indywidualnych

Praca z danymi big data w Data Lake Storage Gen2 WWhen, prawdopodobne jest, że nazwy głównej usługi jest stosowane do umożliwienia usług, takich jak Azure HDInsight, aby pracować z danymi. Jednak może być przypadki, w którym poszczególni użytkownicy muszą mieć dostęp do danych, jak również. We wszystkich przypadkach zdecydowanie należy wziąć pod uwagę przy użyciu usługi Azure Active Directory [grup zabezpieczeń](../common/storage-auth-aad.md) zamiast przypisywać poszczególnym użytkownikom do katalogów i plików.

Po przypisaniu grupie zabezpieczeń uprawnienia dodawania lub usuwania użytkowników z grupy nie wymaga żadnych aktualizacji Data Lake Storage Gen2. Pomaga to również, upewnij się, że nie przekraczają maksymalną liczbę wpisów kontroli dostępu na listy kontroli dostępu (ACL). Obecnie ta liczba jest 32, (w tym cztery listy ACL stylu POSIX, które zawsze są skojarzone z każdym plików i katalogów): użytkownik będący właścicielem, grupy będącej właścicielem, maski i inne. Każdy katalog może mieć dwa typy list ACL, listy ACL dostępu i domyślne listy ACL dla danych całkowitych 64 wpisów kontroli dostępu. Aby uzyskać więcej informacji na temat tych list ACL, zobacz [kontrola dostępu w usługach Azure Data Lake Storage Gen2](data-lake-storage-access-control.md).

### <a name="security-for-groups"></a>Zabezpieczenia dla grup

Gdy Ty lub Twoi użytkownicy muszą mieć dostęp do danych w ramach konta magazynu, z hierarchicznej przestrzeni nazw, włączone, najlepiej jest używać grup zabezpieczeń usługi Azure Active Directory. Niektóre zalecane grup zaczynać może być **ReadOnlyUsers**, **WriteAccessUsers**, i **FullAccessUsers** dla głównego systemu plików, a nawet oddzielić te dla podkatalogi klucza. Jeśli istnieją inne przewidywanego grup użytkowników, które mogą być dodawane później, ale nie zostały zidentyfikowane jeszcze, należy rozważyć, tworzenie grup fikcyjnego z rolą zabezpieczeń, które mają dostęp do niektórych folderów. Przy użyciu grupy zabezpieczeń gwarantuje, że można uniknąć, długi czas przetwarzania podczas przypisywania nowe uprawnienia do tysięcy plików.

### <a name="security-for-service-principals"></a>Zabezpieczenia dla jednostki usługi

Jednostki usługi w usłudze Azure Active Directory są zwykle używane przez usługi takie jak usługi Azure Databricks dostępu do danych w Data Lake Storage Gen2. Dla wielu klientów pojedynczej jednostki usługi Azure Active Directory może być odpowiednia i może mieć pełne uprawnienia w katalogu głównym systemu plików Data Lake Storage Gen2. Inni klienci mogą wymagać wielu klastrów z podmiotami innej usługi, w której jeden klaster ma pełny dostęp do danych i inny klaster z dostępem tylko do odczytu. 

### <a name="enable-the-data-lake-storage-gen2-firewall-with-azure-service-access"></a>Włącz zaporę Data Lake Storage Gen2 z dostępem do usług platformy Azure

Data Lake Storage Gen2 obsługuje opcję Włączanie zapory i ograniczając dostęp tylko do usług platformy Azure, co jest zalecane, aby ograniczyć wektor ataki zewnętrzne. Może być włączona Zapora na koncie magazynu w witrynie Azure portal, za pośrednictwem **zapory** > **włączenia zapory (dalej)** > **zezwolić na dostęp do usług platformy Azure** opcje.

Dodawania klastrów usługi Azure Databricks z siecią wirtualną, która może mogła uzyskać dostęp za pośrednictwem zapory magazynu wymagane jest użycie funkcji wersji zapoznawczej usługi Databricks. Aby włączyć tę funkcję, należy umieścić żądanie pomocy technicznej.

## <a name="resiliency-considerations"></a>Zagadnienia związane z odpornością

Po utworzeniu architektury systemu, Data Lake Storage Gen2 lub dowolnej usługi w chmurze, należy wziąć pod uwagę swoje wymagania dostępności i odpowiadania na potencjalnych przerw w działaniu usługi. Problem może być lokalizowana do konkretnego wystąpienia lub nawet obejmujących cały region, więc planu dla obu jest ważne. W zależności od celu czasu odzyskiwania i odzyskiwania wskazują cel umowy SLA dla obciążenia, możesz wybrać więcej lub mniej skuteczną strategię wysokiej dostępności i odzyskiwania po awarii.

### <a name="high-availability-and-disaster-recovery"></a>Wysoka dostępność i odzyskiwanie po awarii

Wysoka dostępność (HA) i odzyskiwanie po awarii (DR) czasami można łączyć ze sobą, mimo że każda ma nieco strategii, szczególnie w przypadku, gdy chodzi o dane. Data Lake Storage Gen2 już obsługuje 3 replikacji x kulisy, aby je przed nieprzewidzianymi awariami sprzętu zlokalizowanego. Ponadto inne opcje replikacji, takich jak magazyn ZRS zwiększenia wysokiej dostępności podczas GRS i RA-GRS poprawić odzyskiwania po awarii. Podczas tworzenia planu zapewnia wysoką dostępność, w przypadku przerwa w świadczeniu usługi obciążenia musi mieć dostęp do najnowszych danych tak szybko, jak to możliwe, przełączając się za pośrednictwem oddzielnie zreplikowanego wystąpienia, lokalnie lub w nowym regionie.

W strategii odzyskiwania po awarii Aby przygotować się do przypadku mało prawdopodobnego poważnej awarii w regionie, warto również znajdują się dane replikowane w innym regionie przy użyciu replikacji GRS lub RA-GRS. Należy również wziąć pod uwagę Twoje wymagania w zakresie przypadki brzegowe, takich jak uszkodzenie danych, których możesz chcieć utworzyć okresowe migawki wrócił do. W zależności od ważności i rozmiaru danych należy wziąć pod uwagę stopniowe migawki różnicowej 1 - 6- i okresy 24-godzinnym, zgodnie z tolerancji ryzyka.

Aby zachować odporność danych za pomocą Data Lake Storage Gen2 zaleca się replikacja geograficzna danych za pośrednictwem GRS lub RA-GRS, który spełnia Twoje wymagania wysokiej dostępności i odzyskiwania po awarii. Ponadto należy wziąć pod uwagę możliwości aplikacji przy użyciu Data Lake Storage Gen2 do automatycznego trybu failover do regionu pomocniczego poprzez monitorowanie wyzwalaczy lub długość nieudanych prób lub co najmniej wysłanie powiadomienia do administratorów i przeznaczone do ręcznej interwencji. Należy pamiętać, że jest kosztem przechodzenie w tryb failover i Oczekiwanie na usługę do trybu online.

### <a name="use-distcp-for-data-movement-between-two-locations"></a>W przypadku przenoszenia danych między dwiema lokalizacjami za pomocą narzędzia Distcp

Mała w przypadku kopiowania rozproszonej, DistCp jest narzędziem wiersza polecenia systemu Linux, dostarczaną z użyciem usługi Hadoop, która umożliwia przenoszenie danych rozproszonych między dwiema lokalizacjami. Data Lake Storage Gen2, system plików HDFS lub S3, może być dwóch lokalizacjach. To narzędzie używa zadań MapReduce w klastrze usługi Hadoop (np. HDInsight) do skalowania w poziomie na wszystkich węzłach. Narzędzia Distcp jest traktowany jako najszybszy sposób przenoszenia danych big data bez sieciowych specjalne kompresji urządzeń. Narzędzia Distcp także opcję, aby aktualizować tylko różnice między dwiema lokalizacjami, obsługuje automatyczne ponawianie prób, jak również dynamiczne skalowanie wystąpień obliczeniowych. To podejście jest niezwykle wydajne, gdy chodzi o replikowanie elementów, takich jak tabele programu Hive/Spark, które może mieć wielu dużych plików w jednym katalogu i chcesz skopiować zmodyfikowanych danych. Z tego względu Distcp jest najbardziej zalecanym narzędziem do kopiowania danych między magazynami danych big data.

Zadania kopiowania mogą być wywoływane przez przepływy pracy programu Apache Oozie przy użyciu częstotliwości lub wyzwalacze danych, jak również zadania cron w systemie Linux. W przypadku zadań intensywnie korzystających z replikacji zalecane jest oddzielny klaster HDInsight Hadoop, które mogą być dopasowane skalowanie specjalnie dla zadania kopiowania. Daje to gwarancję, że zadania kopiowania nie zakłócają krytyczne zadania. Jeśli z replikacji z częstotliwością dostatecznie szerokie, klaster nawet umożliwić wyłączenie między każdego zadania. Jeśli przechodzenie w tryb failover do regionu pomocniczego, upewnij się, że inny klaster zostanie również rozszerzona w regionie pomocniczym replikowanie nowych danych do konta usługi Data Lake Storage Gen2 podstawowego, po jego wróci do sprawności. Korzystanie z narzędzia Distcp, zobacz [Użyj Distcp do skopiowania danych między Azure Storage Blobs a Data Lake Storage Gen2](../blobs/data-lake-storage-use-distcp.md).

### <a name="use-azure-data-factory-to-schedule-copy-jobs"></a>Azure Data Factory służy do planowania zadań kopiowania

Usługa Azure Data Factory może również służyć do zaplanowania zadania kopiowania za pomocą działania kopiowania, a nawet można skonfigurować pod kątem od częstotliwości za pomocą Kreatora kopiowania. Należy pamiętać, ma limit jednostek przenoszenia danych w chmurze (DMUs) usługi Azure Data Factory, a ostatecznie caps przepływności/zasoby obliczeniowe na potrzeby obciążeń dużych ilości danych. Ponadto usługi Azure Data Factory obecnie nie oferuje aktualizacji przyrostowych między kontami Data Lake Storage Gen2, więc katalogi, takich jak tabele programu Hive wymagałoby to kompletna kopia do replikacji. Zapoznaj się [artykuł fabryki danych](../../data-factory/load-azure-data-lake-storage-gen2.md) więcej informacji na temat kopiowania z usługą Data Factory.

## <a name="monitoring-considerations"></a>Zagadnienia dotyczące monitorowania

Data Lake Storage Gen2 generuje dane pomiarowe w witrynie Azure portal w ramach konta Data Lake Storage Gen2 i w usłudze Azure Monitor. Dostępność usługi Data Lake Storage Gen2 jest wyświetlany w witrynie Azure portal. Aby uzyskać najbardziej aktualne dostępność konta Data Lake Storage Gen2, należy uruchomić syntetycznych testów do sprawdzania dostępności. Inne metryki, takie jak łączne wykorzystanie magazynu, odczytu/zapisu żądania transferu danych przychodzących i wychodzących dostępnych może być wykorzystywane przez monitorowanie aplikacji i mogą również wyzwalać alerty po przekroczeniu progów (na przykład średni czas oczekiwania lub liczba błędów na minutę).

## <a name="directory-layout-considerations"></a>Zagadnienia dotyczące układu katalogu

Po początkowej danych do usługi data lake, jest ważne, zatem potrzeby wcześniejszego zaplanowania struktury danych, tak aby zabezpieczeń, partycjonowanie i przetwarzania może być wykorzystywana skutecznie. Wiele poniższe zalecenia są odpowiednie dla wszystkich obciążeń dużymi ilościami danych. Obciążenia ma inne wymagania dotyczące danych jest używany, ale poniżej przedstawiono niektóre typowe układy wziąć pod uwagę podczas pracy z usługą IoT i usługi batch scenariuszy.

### <a name="iot-structure"></a>Struktura IoT

W obciążeń IoT mogą być dużym stopniem danych jest otwarta w magazynie danych, która obejmuje wiele produktów, urządzeń, organizacji i klientów. Należy zatem potrzeby wcześniejszego zaplanowania układu katalogu dla organizacji, zabezpieczeń i wydajnego przetwarzania danych dla konsumentów strumienia. Szablon ogólny, który należy wziąć pod uwagę mogą być następujące układu:

    {Region}/{SubjectMatter(s)}/{yyyy}/{mm}/{dd}/{hh}/

Na przykład docelowa danych telemetrycznych silnika w trybie samolotowym w Wielkiej Brytanii może wyglądać następującą strukturę:

    UK/Planes/BA1293/Engine1/2017/08/11/12/

Istnieje ważny powód, aby umieścić daty na końcu struktury katalogów. Jeśli chcesz zablokować określonych regionów lub kwestii do użytkowników i grup, następnie możesz łatwo to zrobić przy użyciu uprawnień POSIX. W przeciwnym razie jeśli wystąpił, aby ograniczyć grupę zabezpieczeń do wyświetlania tylko te dane, Wielkiej Brytanii lub niektórych płaszczyzn, ze strukturą daty z przodu oddzielne uprawnienie jest wymagana dla wielu katalogów w katalogu co godzinę. Ponadto o strukturze daty z przodu może wydłużyć liczba katalogów jako czas wystąpił.

### <a name="batch-jobs-structure"></a>Struktura zadania usługi Batch

Z ogólne podejście często używane w przetwarzaniu wsadowym jest dane znajdą się w katalogu "in". Następnie po przetworzeniu danych należy umieścić nowych danych do katalogu "limit" dla procesów podrzędnych korzystanie z. Ta struktura katalogów jest widoczny czasami dla zadań, które wymagają przetworzenia dla poszczególnych plików i może nie wymagać masowego przetwarzania równoległego w dużych zestawach danych. Podobnie jak struktury IoT, zaleca się powyżej struktura katalogów dobre ma katalogów poziomu nadrzędnego, w przypadku elementów, takich jak region i kwestii (na przykład organizacja, produktu i producentów). Ta struktura ułatwia zabezpieczanie danych w organizacji i lepszego zarządzania danymi w Twoich obciążeń. Ponadto należy wziąć pod uwagę Data i godzina w strukturze, aby umożliwić lepszą organizację, wyszukiwanie filtrowane, zabezpieczeń i automatyzacji w czasie przetwarzania. Poziom szczegółowości dla struktury Data jest określany przez interwał, w którym dane są przekazywane lub przetworzone, takich jak co godzinę, codziennie lub nawet co miesiąc.

Czasami pliku przetwarzania się niepowodzeniem z powodu uszkodzenia danych lub nieoczekiwany podzielony na fragmenty. W takich przypadkach strukturę katalogów organizowanych przez **/zły** folder, aby przenieść pliki do bardziej inspekcji. Zadanie wsadowe może również obsługiwać, raportowania lub powiadomienia o tych *zły* pliki ręcznej interwencji. Należy rozważyć następujące struktury szablonu:

    {Region}/{SubjectMatter(s)}/In/{yyyy}/{mm}/{dd}/{hh}/
    {Region}/{SubjectMatter(s)}/Out/{yyyy}/{mm}/{dd}/{hh}/
    {Region}/{SubjectMatter(s)}/Bad/{yyyy}/{mm}/{dd}/{hh}/

Na przykład marketingu firmy odbiera codzienne wyodrębnia dane klienta aktualizacji z klientów w Ameryce Północnej. Może wyglądać jak poniższy fragment kodu przed i po przetworzeniu:

    NA/Extracts/ACMEPaperCo/In/2017/08/14/updates_08142017.csv
    NA/Extracts/ACMEPaperCo/Out/2017/08/14/processed_updates_08142017.csv

W przypadku typowych dane przetwarzane bezpośrednio do bazy danych, takie jak gałąź lub tradycyjnych baz danych SQL, nie ma potrzeby **/in** lub **/out** folderu, ponieważ dane wyjściowe już przechodzi w stan Oddziel folderu dla tabeli programu Hive lub zewnętrznej bazy danych. Na przykład wyodrębnia codzienne od klientów spowoduje przejście do ich odpowiednich folderów i aranżacji, takich jak Azure Data Factory, Apache Oozie lub Apache powietrza powodowało codzienne zadania programu Hive lub Spark do przetwarzania i zapisać dane do tabeli programu Hive.
