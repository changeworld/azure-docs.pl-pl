---
title: Używanie Azure Data Factory do migrowania danych z lokalnego serwera Netezza na platformę Azure | Microsoft Docs
description: Użyj Azure Data Factory do migrowania danych z lokalnego serwera Netezza na platformę Azure.
services: data-factory
documentationcenter: ''
author: dearandyxu
ms.author: yexu
ms.reviewer: ''
manager: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 9/03/2019
ms.openlocfilehash: 4690fd81247035267861b06c204c6db7a052eba5
ms.sourcegitcommit: 267a9f62af9795698e1958a038feb7ff79e77909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/04/2019
ms.locfileid: "70259563"
---
# <a name="use-azure-data-factory-to-migrate-data-from-on-premises-netezza-server-to-azure"></a>Używanie Azure Data Factory do migrowania danych z lokalnego serwera Netezza na platformę Azure 

Azure Data Factory zapewnia wydajny, niezawodny i ekonomiczny mechanizm migracji danych na dużą skalę z lokalnego serwera Netezza do usługi Azure Storage lub Azure SQL Data Warehouse. Ten artykuł zawiera następujące informacje dotyczące inżynierów danych i deweloperów:

> [!div class="checklist"]
> * Wydajność 
> * Odporność kopiowania
> * Bezpieczeństwo sieci
> * Architektura rozwiązania wysokiego poziomu 
> * Najlepsze rozwiązania w zakresie implementacji  

## <a name="performance"></a>Wydajność

Azure Data Factory oferuje architekturę bezserwerową, która umożliwia równoległość na różnych poziomach, dzięki czemu deweloperzy mogą tworzyć potoki, aby w pełni wykorzystać przepustowość sieci, a także przepustowość bazy danych w celu zmaksymalizowania przepływności przenoszenia danych dla naturalne.

![wydajność](media/data-migration-guidance-netezza-azure-sqldw/performance.png)

- Pojedyncze działanie kopiowania może korzystać z skalowalnych zasobów obliczeniowych: w przypadku korzystania z Azure Integration Runtime można określić [maksymalnie 256 DIUs](https://docs.microsoft.com/azure/data-factory/copy-activity-performance#data-integration-units) dla każdego działania kopiowania w sposób bezserwerowy. w przypadku korzystania z samoobsługowego Integration Runtime można ręcznie skalować maszynę w górę lub w poziomie na wielu maszynach ([maksymalnie 4 węzły](https://docs.microsoft.com/azure/data-factory/create-self-hosted-integration-runtime#high-availability-and-scalability)), a pojedyncze działanie kopiowania będzie dystrybuować swoją partycję we wszystkich węzłach. 
- Pojedyncze działanie kopiowania odczytuje i zapisuje dane w magazynie danych przy użyciu wielu wątków. 
- Przepływ sterowania Azure Data Factory może uruchamiać wiele działań kopiowania równolegle, na przykład przy użyciu [dla każdej pętli](https://docs.microsoft.com/azure/data-factory/control-flow-for-each-activity). 

Więcej szczegółów można znaleźć w [przewodniku po wydajności działania kopiowania](https://docs.microsoft.com/azure/data-factory/copy-activity-performance)

## <a name="resilience"></a>Odporność

W ramach pojedynczego uruchomienia działania kopiowania Azure Data Factory ma wbudowany mechanizm ponawiania prób, dlatego może obsłużyć określony poziom przejściowych błędów w magazynach danych lub w sieci źródłowej.

Azure Data Factory działanie kopiowania oferuje również dwa sposoby obsługi niezgodnych wierszy podczas kopiowania danych między źródłami i ujściami danych. Działanie kopiowania można przerwać i zakończyć, gdy napotkano niezgodne dane lub kontynuuje kopiowanie danych REST, pomijając niezgodne wiersze danych. Ponadto można rejestrować niezgodne wiersze w usłudze Azure Blob Storage lub Azure Data Lake Store, aby poznać przyczynę niepowodzenia, naprawić dane w źródle danych, a następnie ponownie wykonać działanie kopiowania.

## <a name="network-security"></a>Bezpieczeństwo sieci 

Domyślnie Azure Data Factory transferuje dane z lokalnego serwera Netezza do usługi Azure Storage lub Azure SQL Data Warehouse przy użyciu szyfrowanego połączenia za pośrednictwem protokołu HTTPS. Zapewnia szyfrowanie danych podczas przesyłania i uniemożliwia podsłuchiwanie i ataki typu man-in-the-middle.

Alternatywnie, jeśli nie chcesz, aby dane były transferowane za pośrednictwem publicznej sieci Internet, możesz uzyskać wyższe zabezpieczenia, przekazując dane za pośrednictwem prywatnego linku komunikacji równorzędnej za pośrednictwem usługi Azure Express Route. Zapoznaj się z architekturą rozwiązania poniżej, w jaki sposób można to osiągnąć.

## <a name="solution-architecture"></a>Architektura rozwiązania

Migrowanie danych za pośrednictwem publicznej sieci Internet:

![Rozwiązanie — architektura — sieć publiczna](media/data-migration-guidance-netezza-azure-sqldw/solution-architecture-public-network.png)

- W tej architekturze dane są przesyłane bezpiecznie przy użyciu protokołu HTTPS przez publiczny Internet.
- Aby osiągnąć tę architekturę, należy zainstalować Azure Data Factory własne środowisko Integration Runtime na komputerze z systemem Windows za zaporą firmową. Upewnij się, że Azure Data Factory własne środowisko Integration Runtime na komputerze z systemem Windows może bezpośrednio uzyskać dostęp do serwera Netezza. Możesz ręcznie skalować maszynę lub skalować ją do wielu maszyn, aby w pełni wykorzystać przepustowość sieci i magazyn danych do kopiowania danych.
- W ramach tej architektury można osiągnąć zarówno migrację danych początkowej migawki, jak i migrację danych różnicowych.

Migruj dane za pośrednictwem prywatnego linku: 

![Rozwiązanie — architektura — sieć prywatna](media/data-migration-guidance-netezza-azure-sqldw/solution-architecture-private-network.png)

- W tej architekturze migracja danych odbywa się za pośrednictwem prywatnego linku komunikacji równorzędnej za pośrednictwem usługi Azure Express Route, tak że dane nigdy nie przechodzą przez publiczny Internet. 
- Aby osiągnąć tę architekturę, należy zainstalować Azure Data Factory własne środowisko Integration Runtime na maszynie wirtualnej z systemem Windows w sieci wirtualnej platformy Azure. Możesz ręcznie skalować maszyny wirtualne lub skalować je do wielu maszyn wirtualnych, aby w pełni wykorzystać przepustowość sieci i magazyn danych do kopiowania danych.
- W ramach tej architektury można osiągnąć zarówno migrację danych początkowej migawki, jak i migrację danych różnicowych.

## <a name="implementation-best-practices"></a>Najlepsze rozwiązania w zakresie implementacji 

### <a name="authentication-and-credential-management"></a>Zarządzanie uwierzytelnianiem i poświadczeniami 

- Aby przeprowadzić uwierzytelnianie w Netezza, można użyć [uwierzytelniania ODBC za pośrednictwem parametrów połączenia](https://docs.microsoft.com/azure/data-factory/connector-netezza#linked-service-properties). 
- Aby nawiązać połączenie z usługą Azure Blob Storage, obsługiwane są wiele typów uwierzytelniania.  Korzystanie z [tożsamości zarządzanych dla zasobów platformy Azure](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage#managed-identity) jest zdecydowanie zalecane: utworzone na podstawie automatycznie zarządzanej Azure Data Factory w usłudze Azure AD można skonfigurować potoki bez dostarczania poświadczeń w definicji połączonej usługi.  Alternatywnie można uwierzytelnić się w usłudze Azure Blob Storage przy użyciu [nazwy głównej usługi](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage#service-principal-authentication), [sygnatury dostępu współdzielonego](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage#shared-access-signature-authentication)lub [klucza konta magazynu](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage#account-key-authentication). 
- Do łączenia się z Azure Data Lake Storage Gen2 są również obsługiwane wiele typów uwierzytelniania.  Korzystanie z [tożsamości zarządzanych dla zasobów platformy Azure](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage#managed-identity) jest zdecydowanie zalecane, ale można również użyć [nazwy głównej usługi](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage#service-principal-authentication) lub [klucza konta magazynu](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage#account-key-authentication) . 
- Do łączenia się z Azure SQL Data Warehouse są również obsługiwane wiele typów uwierzytelniania. Korzystanie z [tożsamości zarządzanych dla zasobów platformy Azure](https://docs.microsoft.com/azure/data-factory/connector-azure-sql-data-warehouse#managed-identity) jest zdecydowanie zalecane, ale można również użyć [nazwy głównej usługi](https://docs.microsoft.com/azure/data-factory/connector-azure-sql-data-warehouse#service-principal-authentication) lub [uwierzytelniania SQL](https://docs.microsoft.com/azure/data-factory/connector-azure-sql-data-warehouse#sql-authentication) .
- Jeśli nie używasz tożsamości zarządzanych dla zasobów platformy Azure, [przechowywanie poświadczeń w Azure Key Vault](https://docs.microsoft.com/azure/data-factory/store-credentials-in-key-vault) jest zdecydowanie zalecane, aby ułatwić centralne zarządzanie kluczami i ich obracanie bez konieczności modyfikowania Azure Data Factory połączonych usług.  Jest to również jedno z [najlepszych rozwiązań dotyczących](https://docs.microsoft.com/azure/data-factory/continuous-integration-deployment#best-practices-for-cicd)ciągłej integracji/ciągłego wdrażania. 

### <a name="initial-snapshot-data-migration"></a>Migracja początkowej danych migawek 

W przypadku małych tabel rozmiar woluminu jest mniejszy niż 100 GB lub można przeprowadzić migrację na platformę Azure w ciągu 2 godzin, aby każde zadanie kopiowania załadowało dane na tabelę. Można uruchomić wiele zadań kopiowania Azure Data Factory, aby jednocześnie ładować różne tabele w celu zapewnienia lepszej przepływności. 

W ramach każdego zadania kopiowania można także uzyskać dostęp do pewnego poziomu równoległości przy użyciu [Ustawienia parallelCopies](https://docs.microsoft.com/azure/data-factory/copy-activity-performance#parallel-copy) z opcją partycji danych w celu uruchamiania zapytań równoległych i kopiowania danych przez partycje. Dostępne są dwie opcje partycji danych z poniższymi szczegółami.
- Zalecamy rozpoczęcie od wycinka danych, ponieważ jest to bardziej wydajne.  Upewnij się, że liczba równoległości w ustawieniu parallelCopies jest niższa od całkowitej liczby partycji wycinków danych w tabeli na serwerze Netezza.  
- Jeśli rozmiar woluminu dla każdej partycji wycinka danych jest nadal duży (na przykład większy niż 10 GB), zaleca się przełączenie na dynamiczną partycję zakresu, w której można zdefiniować liczbę partycji i rozmiar woluminu dla każdej partycji. według kolumny partycji, Górna granica i dolna granica.

W przypadku dużych tabel, gdy rozmiar woluminu jest większy niż 100 GB lub nie można przeprowadzić migracji na platformę Azure w ciągu 2 godzin, zaleca się Partycjonowanie danych według zapytania niestandardowego, a następnie każde zadanie kopiowania Kopiuje jedną partycję w danym momencie. Aby zapewnić lepszą przepływność, można uruchomić wiele zadań kopiowania Azure Data Factory jednocześnie. Należy pamiętać, że dla każdego obiektu docelowego zadania kopiowania w celu załadowania jednej partycji za pomocą zapytania niestandardowego można nadal włączyć równoległość za pomocą wycinka danych lub dynamicznego zakresu, aby zwiększyć przepływność. 

Jeśli którekolwiek z zadań kopiowania zakończy się niepowodzeniem z powodu przejściowego problemu z siecią lub magazynem danych, można ponownie uruchomić zadanie kopiowania zakończonego niepowodzeniem w celu ponownego załadowania określonej partycji z tabeli. Nie wpłynie to na wszystkie inne zadania kopiowania ładujące inne partycje.

Podczas ładowania danych do Azure SQL Data Warehouse jest sugerowany do włączenia w ramach zadania kopiowania z magazynem obiektów blob platformy Azure jako przemieszczania.

### <a name="delta-data-migration"></a>Migracja danych różnicowych 

Sposób identyfikacji nowych lub zaktualizowanych wierszy z tabeli polega na użyciu kolumny znacznika czasu lub przyrostu klucza w schemacie, a następnie przechowania najnowszej wartości jako górnego limitu w tabeli zewnętrznej, która może służyć do filtrowania danych różnicowych podczas następnego ładowania danych. 

Różne tabele mogą używać różnych kolumn limitu, aby identyfikować nowe lub zaktualizowane wiersze. Sugerujemy utworzenie tabeli formantów zewnętrznych, w której każdy wiersz reprezentuje jedną tabelę na serwerze Netezza z nazwą kolumny określonego znaku wodnego i wartością górnego limitu. 

### <a name="self-hosted-integration-runtime-configuration-on-azure-vm-or-machine"></a>Samodzielna konfiguracja środowiska Integration Runtime na maszynie wirtualnej lub maszynie platformy Azure

W przypadku migrowania danych z serwera Netezza na platformę Azure bez względu na to, że Netezza serwer jest w ramach firmy Microsoft w środowisku sieci wirtualnej, należy zainstalować własne środowisko Integration Runtime na komputerze lub maszynie wirtualnej, która jest aparatem do przeniesienia Data.

- Zalecaną konfiguracją początkową dla każdego komputera lub maszyny wirtualnej jest 32 vCPU i 128 GB pamięci. Możesz zachować monitorowanie użycia procesora i pamięci przez maszynę podczerwieni podczas migracji danych, aby zobaczyć, czy konieczne jest dalsze skalowanie maszyny w górę w celu uzyskania lepszej wydajności lub skalowanie maszyny w dół w celu zaoszczędzenia kosztów.
- Możesz również skalować w poziomie, kojarząc do 4 węzłów za pomocą jednego samodzielnego środowiska IR. Pojedyncze zadanie kopiowania uruchomione dla samodzielnego środowiska IR automatycznie będzie korzystać ze wszystkich węzłów maszyny wirtualnej w celu równoległego kopiowania danych. Aby zapewnić wysoką dostępność, zaleca się Rozpoczynanie pracy z 2 węzłami maszyn wirtualnych w celu uniknięcia single point of failure podczas migracji danych.

### <a name="rate-limiting"></a>Ograniczanie szybkości

Najlepszym rozwiązaniem jest przeprowadzenie koncepcji oceny wydajności z reprezentatywnym przykładowym zestawem danych, dzięki czemu można określić odpowiedni rozmiar partycji dla każdego działania kopiowania. Sugerujemy, aby każda partycja została załadowana na platformę Azure w ciągu 2 godzin.  

Rozpocznij pracę z pojedynczym działaniem kopiowania z pojedynczym własnym maszyną podczerwieni, aby skopiować tabelę. Stopniowo Zwiększ ustawienie parallelCopies na podstawie liczby partycji wycinków danych w tabeli i sprawdź, czy cała tabela może zostać załadowana do platformy Azure w ciągu 2 godzin zgodnie z przepływem pracy w ramach zadania kopiowania. 

Jeśli nie można jej osiągnąć i w tym samym czasie pojemność węzła środowiska IR i magazynu danych nie jest w pełni wykorzystywana, stopniowo Zwiększ liczbę współbieżnych działań kopiowania do momentu osiągnięcia limitów sieci lub przepustowości magazynów danych. 

Monitoruj użycie procesora CPU/pamięci na samoobsługowej maszynie IR i przygotuj się do skalowania maszyny w górę lub w poziomie do wielu maszyn, gdy widzisz, że procesor CPU/pamięć są w pełni wykorzystywane. 

W przypadku wystąpienia błędów ograniczania wydajności raportowanych przez działanie Azure Data Factory kopiowania należy zmniejszyć ustawienie współbieżności lub parallelCopies w Azure Data Factory lub rozważyć zwiększenie limitów przepustowości/liczby operacji we/wy sieci i magazynów danych. 


### <a name="estimating-price"></a>Cena szacunkowa 

Rozważmy następujący potok skonstruowany do migrowania danych z lokalnego serwera Netezza do usługi Azure SQL Data Warehouse:

![Cennik — potok](media/data-migration-guidance-netezza-azure-sqldw/pricing-pipeline.png)

Załóżmy, że: 

- Całkowity wolumin danych to 50 TB. 
- Migrowanie danych przy użyciu architektury pierwszego rozwiązania (serwer Netezza działa lokalnie za zaporą)
- 50 TB jest podzielony na partycje 500, a każda czynność kopiowania przenosi jedną partycję.
- Każde działanie kopiowania jest skonfigurowane z użyciem jednego własnego środowiska IR z 4 maszynami i osiąga przepustowość 20 MB/s. (W ramach działania Copy parallelCopies ma wartość 4, a każdy wątek ładowania danych z tabeli uzyskuje przepływność 5 MB/s)
- Ustawienie współbieżności ForEach jest ustawione na 3, a przepustowość zagregowana to 60 MB/s.
- W sumie wykonanie migracji trwa 243 godzin.

Oto Szacowana cena oparta na powyższych założeniach: 

![Cennik — tabela](media/data-migration-guidance-netezza-azure-sqldw/pricing-table.png)

> [!NOTE]
> Jest to hipotetyczny przykład cen. Rzeczywiste ceny są uzależnione od rzeczywistej przepływności w danym środowisku. Cena maszyny z systemem Windows (z zainstalowanym własnym środowiskiem Integration Runtime) nie jest uwzględniona. 

### <a name="additional-references"></a>Dodatkowa dokumentacja 
- [Migracja danych z lokalnego magazynu danych relacyjnych na platformę Azure przy użyciu Azure Data Factory](https://azure.microsoft.com/mediahandler/files/resourcefiles/data-migration-from-on-premise-relational-data-warehouse-to-azure-data-lake-using-azure-data-factory/Data_migration_from_on-prem_RDW_to_ADLS_using_ADF.pdf)
- [Łącznik Netezza](https://docs.microsoft.com/azure/data-factory/connector-netezza)
- [Łącznik ODBC](https://docs.microsoft.com/azure/data-factory/connector-odbc)
- [Łącznik usługi Azure Blob Storage](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage)
- [Łącznik Azure Data Lake Storage Gen2](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage)
- [Łącznik Azure SQL Data Warehouse](https://docs.microsoft.com/azure/data-factory/connector-azure-sql-data-warehouse)
- [Przewodnik dostrajania wydajności działania kopiowania](https://docs.microsoft.com/azure/data-factory/copy-activity-performance)
- [Tworzenie i Konfigurowanie samoobsługowego Integration Runtime](https://docs.microsoft.com/azure/data-factory/create-self-hosted-integration-runtime)
- [Własne środowisko Integration Runtime HA i skalowalność](https://docs.microsoft.com/azure/data-factory/create-self-hosted-integration-runtime#high-availability-and-scalability)
- [Zagadnienia dotyczące zabezpieczeń przenoszenia danych](https://docs.microsoft.com/azure/data-factory/data-movement-security-considerations)
- [Przechowywanie poświadczeń w Azure Key Vault](https://docs.microsoft.com/azure/data-factory/store-credentials-in-key-vault)
- [Przyrostowe kopiowanie danych z jednej tabeli](https://docs.microsoft.com/azure/data-factory/tutorial-incremental-copy-portal)
- [Przyrostowe kopiowanie danych z wielu tabel](https://docs.microsoft.com/azure/data-factory/tutorial-incremental-copy-multiple-tables-portal)
- [Strona cennika Azure Data Factory](https://azure.microsoft.com/pricing/details/data-factory/data-pipeline/)

## <a name="next-steps"></a>Następne kroki

- [Kopiowanie plików z wielu kontenerów za pomocą Azure Data Factory](solution-template-copy-files-multiple-containers.md)