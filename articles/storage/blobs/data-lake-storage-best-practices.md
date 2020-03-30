---
title: Najważniejsze wskazówki dotyczące korzystania z usługi Azure Data Storage Gen2 | Dokumenty firmy Microsoft
description: Poznaj najważniejsze wskazówki dotyczące pozyskiwania danych, zabezpieczeń dat i wydajności związanych z korzystaniem z usługi Azure Data Lake Storage Gen2 (wcześniej znanej jako Usługa Azure Data Lake Store)
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: normesta
ms.reviewer: sachins
ms.openlocfilehash: ac4e126c7ecbd1fc781db74e5b19635b273bbb34
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "72299660"
---
# <a name="best-practices-for-using-azure-data-lake-storage-gen2"></a>Najważniejsze wskazówki dotyczące korzystania z usługi Azure Data Storage Gen2

W tym artykule dowiesz się o najlepszych rozwiązaniach i zagadnieniach dotyczących pracy z usługą Azure Data Lake Storage Gen2. Ten artykuł zawiera informacje dotyczące zabezpieczeń, wydajności, odporności i monitorowania usługi Data Lake Storage Gen2. Przed data lake storage gen2, praca z naprawdę dużych danych w usługach, takich jak Azure HDInsight było skomplikowane. Trzeba było shard danych na wielu kontach magazynu obiektów Blob, tak aby można było osiągnąć magazyn petabajtów i optymalną wydajność w tej skali. Data Lake Storage Gen2 obsługuje poszczególne rozmiary plików o pojemności nawet 5 TB, a większość twardych limitów wydajności została usunięta. Jednak nadal istnieją pewne zagadnienia, które obejmuje ten artykuł, dzięki czemu można uzyskać najlepszą wydajność z Data Lake Storage Gen2.

## <a name="security-considerations"></a>Zagadnienia dotyczące bezpieczeństwa

Usługa Azure Data Lake Storage Gen2 oferuje kontrolki dostępu POSIX dla użytkowników, grup i podmiotów korzystających z usługi Azure Active Directory (Azure AD). Te formanty dostępu można ustawić na istniejące pliki i katalogi. Formanty dostępu mogą być również używane do tworzenia domyślnych uprawnień, które mogą być automatycznie stosowane do nowych plików lub katalogów. Więcej informacji na temat list ACL Data Lake Storage Gen2 można znaleźć w [obszarze Kontrola dostępu w usłudze Azure Data Lake Storage Gen2.](storage-data-lake-storage-access-control.md)

### <a name="use-security-groups-versus-individual-users"></a>Używanie grup zabezpieczeń w porównaniu z poszczególnymi użytkownikami

Podczas pracy z dużych danych w usłudze Data Lake Storage Gen2, jest prawdopodobne, że podmiot usługi jest używany do zezwalania usługom, takich jak Usługi Azure HDInsight do pracy z danymi. Mogą jednak wystąpić przypadki, w których poszczególni użytkownicy również potrzebują dostępu do danych. We wszystkich przypadkach należy zdecydowanie rozważyć użycie [grup zabezpieczeń](../common/storage-auth-aad.md) usługi Azure Active Directory zamiast przypisywania poszczególnych użytkowników do katalogów i plików.

Po przypisaniu uprawnień do grupy zabezpieczeń dodawanie lub usuwanie użytkowników z grupy nie wymaga żadnych aktualizacji usługi Data Lake Storage Gen2. Pomaga to również upewnić się, że nie przekraczasz maksymalnej liczby wpisów kontroli dostępu na listę kontroli dostępu (ACL). Obecnie liczba ta wynosi 32 (w tym cztery listy ACL w stylu POSIX, które są zawsze skojarzone z każdym plikiem i katalogiem): użytkownikiem właścicielem, grupą właścicielską, maską i innymi. Każdy katalog może mieć dwa typy listy ACL, akces dostępu i domyślną listy ACL, w sumie 64 wpisy kontroli dostępu. Aby uzyskać więcej informacji na temat tych list ACL, zobacz [Kontrola dostępu w usłudze Azure Data Lake Storage Gen2](data-lake-storage-access-control.md).

### <a name="security-for-groups"></a>Zabezpieczenia dla grup

Jeśli ty lub twoi użytkownicy potrzebujecie dostępu do danych na koncie magazynu z włączoną hierarchiczną przestrzenią nazw, najlepiej użyć grup zabezpieczeń usługi Azure Active Directory. Niektóre zalecane grupy, aby rozpocząć może być **ReadOnlyUsers**, **WriteAccessUsers**i **FullAccessUsers** dla katalogu głównego kontenera, a nawet oddzielne dla podkatalogów klucza. Jeśli istnieją inne przewidywane grupy użytkowników, które mogą zostać dodane później, ale nie zostały jeszcze zidentyfikowane, można rozważyć utworzenie fikcyjnych grup zabezpieczeń, które mają dostęp do niektórych folderów. Korzystanie z grupy zabezpieczeń zapewnia, że można uniknąć długiego czasu przetwarzania podczas przypisywania nowych uprawnień do tysięcy plików.

### <a name="security-for-service-principals"></a>Zabezpieczenia dla podmiotów świadczących usługi

Jednostki usługi Azure Active Directory są zazwyczaj używane przez usługi, takie jak Azure Databricks, aby uzyskać dostęp do danych w usłudze Data Lake Storage Gen2. Dla wielu klientów jeden podmiot usługi Azure Active Directory może być odpowiedni i może mieć pełne uprawnienia w katalogu głównym kontenera Usługi Data Lake Storage Gen2. Inni klienci mogą wymagać wielu klastrów z różnymi jednostkami usługi, gdzie jeden klaster ma pełny dostęp do danych, a inny klaster z dostępem tylko do odczytu. 

### <a name="enable-the-data-lake-storage-gen2-firewall-with-azure-service-access"></a>Włączanie zapory data lake storage gen2 z dostępem do usługi Azure

Usługa Data Lake Storage Gen2 obsługuje opcję włączania zapory i ograniczania dostępu tylko do usług platformy Azure, co jest zalecane w celu ograniczenia wektora ataków zewnętrznych. Zapora może być włączona na koncie magazynu w witrynie Azure portal za pośrednictwem **zapory włącz zapory** > **(ON)** > Zezwalaj na dostęp do opcji usług platformy**Azure.**

Aby uzyskać dostęp do konta magazynu z usługi Azure Databricks, należy wdrożyć usługę Azure Databricks w sieci wirtualnej, a następnie dodać tę sieć wirtualną do zapory. Zobacz [Konfigurowanie zapór usługi Azure Storage i sieci wirtualnych](https://docs.microsoft.com/azure/storage/common/storage-network-security).

## <a name="resiliency-considerations"></a>Zagadnienia związane z odpornością

Podczas owego owego zaprojektowywania systemu z usługą Data Lake Storage Gen2 lub dowolnej usługi w chmurze należy wziąć pod uwagę wymagania dotyczące dostępności i sposobu reagowania na potencjalne przerwy w usłudze. Problem może być zlokalizowany do określonego wystąpienia lub nawet w całym regionie, więc posiadanie planu dla obu jest ważne. W zależności od celu czasu odzyskiwania i ław sla celu punktu odzyskiwania dla obciążenia można wybrać mniej lub bardziej agresywną strategię wysokiej dostępności i odzyskiwania po awarii.

### <a name="high-availability-and-disaster-recovery"></a>Wysoka dostępność i odzyskiwanie po awarii

Wysoka dostępność (HA) i odzyskiwanie po awarii (DR) mogą być czasami łączone ze sobą, chociaż każdy ma nieco inną strategię, zwłaszcza jeśli chodzi o dane. Data Lake Storage Gen2 obsługuje już replikację 3x pod maską, aby chronić przed zlokalizowanych awarii sprzętu. Ponadto inne opcje replikacji, takie jak ZRS lub GZRS (wersja zapoznawcza), poprawiają ha, podczas gdy GRS & RA-GRS poprawiają dr. Podczas tworzenia planu dla wysokiej analizy, w przypadku przerwania usługi obciążenie wymaga dostępu do najnowszych danych tak szybko, jak to możliwe, przełączając się na oddzielnie replikowane wystąpienie lokalnie lub w nowym regionie.

W strategii odzyskiwania po awarii, aby przygotować się na mało prawdopodobne zdarzenie katastrofalnej awarii regionu, ważne jest również, aby dane replikowane do innego regionu przy użyciu replikacji GRS lub RA-GRS. Należy również wziąć pod uwagę wymagania dotyczące przypadków krawędzi, takich jak uszkodzenie danych, gdzie można utworzyć okresowe migawki, aby wrócić do. W zależności od ważności i rozmiaru danych należy wziąć pod uwagę stopniowe migawki delta okresów 1-, 6- i 24-godzinnych, zgodnie z tolerancjami ryzyka.

Aby zapewnić odporność danych za pomocą usługi Data Lake Storage Gen2, zaleca się replikowanie danych geograficznie za pośrednictwem grs lub RA-GRS, który spełnia wymagania dotyczące wysokiej dostępności/odzyskiwania po awarii. Ponadto należy rozważyć sposoby dla aplikacji przy użyciu usługi Data Lake Storage Gen2, aby automatycznie awaryjnie do regionu pomocniczego za pomocą monitorowania wyzwalaczy lub długość nieudanych prób lub przynajmniej wysłać powiadomienie do administratorów do ręcznej interwencji. Należy pamiętać, że istnieje kompromis w przypadku awarii w porównaniu do oczekiwania na usługę wrócić do trybu online.

### <a name="use-distcp-for-data-movement-between-two-locations"></a>Użyj Distcp do przenoszenia danych między dwiema lokalizacjami

Skrót od kopii rozproszonej, DistCp jest narzędziem wiersza polecenia Linuksa, które jest wyposażone w Hadoop i zapewnia rozproszony transfer danych między dwiema lokalizacjami. Dwie lokalizacje mogą być Data Lake Storage Gen2, HDFS lub S3. To narzędzie używa mapReduce zadań w klastrze Hadoop (na przykład HDInsight) do skalowania w poziomie na wszystkich węzłów. Distcp jest uważany za najszybszy sposób przenoszenia dużych zbiorów danych bez specjalnych urządzeń do kompresji sieciowej. Distcp udostępnia również opcję aktualizowania tylko różnic między dwiema lokalizacjami, obsługuje automatyczne próby prób, a także dynamiczne skalowanie obliczeń. Takie podejście jest niezwykle wydajne, jeśli chodzi o replikowanie rzeczy, takich jak hive/spark tabel, które mogą mieć wiele dużych plików w jednym katalogu i chcesz skopiować tylko za pomocą zmodyfikowanych danych. Z tych powodów Distcp jest najbardziej zalecanym narzędziem do kopiowania danych między magazynami dużych zbiorów danych.

Zadania kopiowania mogą być wyzwalane przez przepływy pracy Apache Oozie przy użyciu wyzwalaczy częstotliwości lub danych, a także zadania cron linuksa. W przypadku zadań replikacji intensywnej zaleca się wyodrębnienie oddzielnego klastra hdinsight Hadoop, który można dostroić i skalować specjalnie dla zadań kopiowania. Dzięki temu zadania kopiowania nie kolidują z zadaniami krytycznymi. Jeśli jest uruchomiona replikacja na wystarczająco szerokiej częstotliwości, klaster może nawet zostać zdjęty między każdym zadaniem. Jeśli w przypadku niepowodzenia w poprzek do regionu pomocniczego, upewnij się, że inny klaster jest również spun w regionie pomocniczym do replikacji nowych danych z powrotem do podstawowego konta magazynu usługi Data Lake Gen2 po jego powrocie do pracy. Aby zapoznać się z przykładami używania protokołu Distcp, zobacz [Kopiowanie danych między obiektami blob usługi Azure Storage i Data Lake Storage Gen2 za pomocą](../blobs/data-lake-storage-use-distcp.md)protokołu Distcp.

### <a name="use-azure-data-factory-to-schedule-copy-jobs"></a>Planowanie zadań kopiowania za pomocą usługi Azure Data Factory

Usługa Azure Data Factory może również służyć do planowania zadań kopiowania przy użyciu działania kopiowania, a nawet można skonfigurować na częstotliwości za pośrednictwem Kreatora kopiowania. Należy pamiętać, że usługa Azure Data Factory ma limit jednostek przenoszenia danych w chmurze (DMU), a ostatecznie ogranicza przepływność/obliczenie dla dużych obciążeń danych. Ponadto usługa Azure Data Factory obecnie nie oferuje aktualizacji różnicowych między kontami Usługi Data Lake Storage Gen2, więc katalogi takie jak tabele hive wymagałyby pełnej kopii do replikacji. Więcej informacji na temat kopiowania za pomocą usługi Data Factory można znaleźć w artykule fabryka [danych.](../../data-factory/load-azure-data-lake-storage-gen2.md)

## <a name="monitoring-considerations"></a>Zagadnienia związane z monitorowaniem

Usługa Data Lake Storage Gen2 udostępnia metryki w witrynie Azure portal w obszarze konta Data Lake Storage Gen2 i w usłudze Azure Monitor. Dostępność usługi Data Lake Storage Gen2 jest wyświetlana w witrynie Azure portal. Aby uzyskać najbardziej aktualną dostępność konta Data Lake Storage Gen2, należy uruchomić własne testy syntetyczne, aby sprawdzić dostępność. Inne metryki, takie jak całkowite wykorzystanie magazynu, żądania odczytu/zapisu i ruch przychodzący/wychodzący są dostępne do wykorzystania przez monitorowanie aplikacji i może również wyzwalać alerty, gdy progi (na przykład średnie opóźnienie lub liczba błędów na minutę) są przekroczone.

## <a name="directory-layout-considerations"></a>Zagadnienia dotyczące układu katalogów

Podczas wyładunku danych do źródła danych, ważne jest, aby wstępnie zaplanować strukturę danych, tak aby zabezpieczenia, partycjonowanie i przetwarzanie mogły być skutecznie wykorzystywane. Wiele z poniższych zaleceń ma zastosowanie do wszystkich obciążeń dużych zbiorów danych. Każde obciążenie ma różne wymagania dotyczące sposobu korzystania z danych, ale poniżej znajdują się niektóre typowe układy, które należy wziąć pod uwagę podczas pracy z IoT i scenariuszami wsadowymi.

### <a name="iot-structure"></a>Struktura IoT

W obciążeniach IoT może istnieć wiele danych wyładowywanych w magazynie danych, który obejmuje wiele produktów, urządzeń, organizacji i klientów. Ważne jest, aby wstępnie zaplanować układ katalogów dla organizacji, zabezpieczeń i wydajnego przetwarzania danych dla konsumentów w dół strumienia. Ogólny szablon do rozważenia może być następujący układ:

    {Region}/{SubjectMatter(s)}/{yyyy}/{mm}/{dd}/{hh}/

Na przykład dane telemetryczne lądowania dla silnika samolotu w Wielkiej Brytanii może wyglądać następującą strukturę:

    UK/Planes/BA1293/Engine1/2017/08/11/12/

Istnieje ważny powód, aby umieścić datę na końcu struktury katalogów. Jeśli chcesz zablokować niektóre regiony lub tematy dla użytkowników/grup, możesz to łatwo zrobić za pomocą uprawnień POSIX. W przeciwnym razie, jeśli istnieje potrzeba ograniczenia określonej grupy zabezpieczeń do wyświetlania tylko danych w Wielkiej Brytanii lub niektórych płaszczyzn, ze strukturą dat z przodu wymagane byłoby oddzielne uprawnienie dla wielu katalogów w katalogu co godzinę. Ponadto posiadanie struktury daty z przodu gwałtownie zwiększyłoby liczbę katalogów w miarę upływu czasu.

### <a name="batch-jobs-structure"></a>Struktura zadań wsadowych

Od wysokiego poziomu, powszechnie stosowane podejście w przetwarzaniu wsadowym jest do gruntów danych w katalogu "w". Następnie po przetworzeniu danych umieść nowe dane w katalogu "out" dla procesów podrzędnych do wykorzystania. Ta struktura katalogów jest czasami postrzegana dla zadań, które wymagają przetwarzania na poszczególnych plikach i może nie wymagać masowo równoległego przetwarzania przez duże zestawy danych. Podobnie jak w przypadku struktury IoT zalecane powyżej, dobra struktura katalogów ma katalogi na poziomie nadrzędnym dla rzeczy, takich jak region i tematy (na przykład organizacja, produkt/producent). Ta struktura pomaga w zabezpieczaniu danych w całej organizacji i lepszym zarządzaniu danymi w obciążeniach. Ponadto należy wziąć pod uwagę datę i godzinę w strukturze, aby umożliwić lepszą organizację, filtrowane wyszukiwania, zabezpieczenia i automatyzację w przetwarzaniu. Poziom szczegółowości struktury daty zależy od interwału, w którym dane są przekazywane lub przetwarzane, takich jak co godzinę, codziennie lub nawet co miesiąc.

Czasami przetwarzanie plików kończy się niepowodzeniem z powodu uszkodzenia danych lub nieoczekiwanych formatów. W takich przypadkach struktura katalogów może korzystać z **/bad** folder, aby przenieść pliki do dalszej inspekcji. Zadanie wsadowe może również obsługiwać raportowanie lub powiadamianie o tych *złych* plikach do ręcznej interwencji. Należy wziąć pod uwagę następującą strukturę szablonu:

    {Region}/{SubjectMatter(s)}/In/{yyyy}/{mm}/{dd}/{hh}/
    {Region}/{SubjectMatter(s)}/Out/{yyyy}/{mm}/{dd}/{hh}/
    {Region}/{SubjectMatter(s)}/Bad/{yyyy}/{mm}/{dd}/{hh}/

Na przykład firma marketingowa otrzymuje codzienne wyciągi danych z aktualizacji klientów od swoich klientów w Ameryce Północnej. Może wyglądać jak następujący fragment kodu przed i po przetworzeniu:

    NA/Extracts/ACMEPaperCo/In/2017/08/14/updates_08142017.csv
    NA/Extracts/ACMEPaperCo/Out/2017/08/14/processed_updates_08142017.csv

W typowym przypadku dane wsadowe przetwarzane bezpośrednio do baz danych, takich jak Hive lub tradycyjnych baz danych SQL, nie ma potrzeby **/in** lub **/out** folderu, ponieważ dane wyjściowe już przechodzi do oddzielnego folderu dla tabeli Hive lub zewnętrznej bazy danych. Na przykład codzienne wyciągi od klientów wyląduje do ich odpowiednich folderów, a aranżacja przez coś takiego jak Azure Data Factory, Apache Oozie lub Apache Airflow wyzwoli codzienne zadanie hive lub spark do przetwarzania i zapisywania danych w tabeli hive.
