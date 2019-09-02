---
title: Używanie Azure Data Factory do migrowania danych z lokalnego klastra Hadoop do usługi Azure Storage | Microsoft Docs
description: Użyj Azure Data Factory do migrowania danych z lokalnego klastra Hadoop do usługi Azure Storage.
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
ms.date: 8/30/2019
ms.openlocfilehash: 1b26b22fa9cdf9f6671702ceb9640aa39a6ecf17
ms.sourcegitcommit: 8fea78b4521921af36e240c8a92f16159294e10a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/02/2019
ms.locfileid: "70211600"
---
# <a name="use-azure-data-factory-to-migrate-data-from-on-premises-hadoop-cluster-to-azure-storage"></a>Używanie Azure Data Factory do migrowania danych z lokalnego klastra Hadoop do usługi Azure Storage 

Azure Data Factory zapewnia wydajny, niezawodny i ekonomiczny mechanizm migracji danych na dużą skalę z lokalnego systemu plików HDFS do usługi Azure Blob Storage lub Azure Data Lake Storage Gen2. Zasadniczo Azure Data Factory zawiera dwa podejścia do migracji danych z lokalnego systemu plików HDFS na platformę Azure. Każdą z nich można wybrać w oparciu o twój scenariusz. 

- Tryb pomocą distcp ADF. Obsługa podajnika APD przy użyciu programu [pomocą distcp](https://hadoop.apache.org/docs/current3/hadoop-distcp/DistCp.html) do kopiowania plików jako-do obiektów blob platformy Azure (w tym [kopii przygotowanej](https://docs.microsoft.com/azure/data-factory/copy-activity-performance#staged-copy)) lub Azure Data Lake Store, w którym to przypadku można w pełni wykorzystać możliwości klastra zamiast uruchamiać je w ramach samodzielnego środowiska Integration Runtime (IR). Zapewnia lepszą przepływność kopiowania zwłaszcza wtedy, gdy klaster jest bardzo wydajny. W oparciu o konfigurację w Azure Data Factory, działanie Copy automatycznie konstruuje polecenie pomocą distcp, przesyła do klastra usługi Hadoop i monitoruje stan kopiowania. Dzięki zastosowaniu funkcji ADF zintegrowanej z usługą pomocą distcp klient nie tylko wykorzystuje istniejący wydajny klaster, aby osiągnąć najlepszą przepływność kopiowania, ale również uzyskać korzyści wynikające z elastycznego planowania i ujednoliconego monitorowania w ramach funkcji ADF. Domyślnie tryb pomocą distcp ADF jest zalecanym sposobem migrowania danych z lokalnego klastra Hadoop na platformę Azure.
- Tryb natywnego środowiska IR. W niektórych scenariuszach pomocą distcp nie może obsłużyć swoich przypadków (na przykład w środowisku sieci wirtualnej, narzędzie pomocą distcp nie obsługuje prywatnej komunikacji równorzędnej i punktu końcowego usługi Azure Storage w sieci wirtualnej). Poza tym kiedyś nie chcesz używać istniejącego klastra usługi Hadoop jako aparatu do migracji danych, ponieważ spowoduje to znaczne obciążenie klastra, co może mieć wpływ na wydajność istniejących zadań ETL. W takim przypadku można użyć natywnej funkcji ADF, w której usługa ADF Integration Runtime (IR) może być aparatem do kopiowania danych z Premium HDFS na platformę Azure.

Ten artykuł zawiera następujące informacje o obu podejściach dla inżynierów danych i deweloperów:
> [!div class="checklist"]
> * Wydajność 
> * Odporność kopiowania
> * Bezpieczeństwo sieci
> * Architektura rozwiązania wysokiego poziomu 
> * Najlepsze rozwiązania w zakresie implementacji  

## <a name="performance"></a>Wydajność

W trybie pomocą distcp ADF przepływność jest taka sama jak w przypadku używania narzędzia pomocą distcp niezależnie od tego, co wykorzystuje pojemność istniejącego klastra usługi Hadoop. Pomocą distcp (kopia rozproszona) to narzędzie służące do dużego kopiowania między klastrami. Używa MapReduce, aby wpływać na dystrybucję, obsługę błędów i odzyskiwanie oraz raportowanie. Rozszerza listę plików i katalogów na dane wejściowe w celu zamapowania zadań, z których każdy skopiuje partycję plików określonych na liście źródłowej. Dzięki użyciu funkcji ADF zintegrowanej z programem pomocą distcp można kompilować potoki w celu pełnego wykorzystania przepustowości sieci, a także liczby operacji we/wy magazynu i przepustowości w celu zmaksymalizowania przepływności przenoszenia danych w środowisku.  

W trybie natywnego IR w usłudze ADF można także zapewnić równoległość na różnych poziomach, co pozwala w pełni wykorzystać przepustowość sieci, a także liczbę operacji wejścia/wyjścia magazynu i przepustowości w celu zmaksymalizowania przepływności przenoszenia danych przez ręczne skalowanie samodzielnej maszyny IR lub skalowanie do wewnątrz. samoobsługowe środowisko IR z wieloma maszynami.

- Pojedyncze działanie kopiowania może korzystać z skalowalnych zasobów obliczeniowych. Korzystając z własnego środowiska Integration Runtime, można ręcznie skalować maszynę w górę lub w poziomie do wielu maszyn ([maksymalnie 4 węzły](https://docs.microsoft.com/azure/data-factory/create-self-hosted-integration-runtime#high-availability-and-scalability)), a działanie pojedynczej kopii będzie dzielić zestaw plików na wszystkie węzły. 
- Pojedyncze działanie kopiowania odczytuje i zapisuje dane w magazynie danych przy użyciu wielu wątków. 
- Przepływ sterowania ADF można uruchomić równolegle wiele działań kopiowania, na przykład przy użyciu [dla każdej pętli](https://docs.microsoft.com/azure/data-factory/control-flow-for-each-activity). 

Więcej szczegółów można znaleźć w [przewodniku po wydajności działania kopiowania](https://docs.microsoft.com/azure/data-factory/copy-activity-performance)

## <a name="resilience"></a>Odporność

W trybie pomocą distcp ADF można korzystać z różnych parametrów wiersza polecenia pomocą distcp (na przykład-i, ignorować błędy;-Update, zapisywać dane, gdy plik źródłowy i docelowy są różne), aby osiągnąć różne poziomy odporności.

W trybie natywnego IR funkcji ADF w ramach pojedynczego przebiegu działania kopiowania moduł ADF ma wbudowany mechanizm ponawiania prób, dzięki czemu może obsłużyć określony poziom przejściowych błędów w magazynach danych lub w sieci podstawowej. Podczas kopiowania binarnego z lokalnego systemu plików HDFS do obiektu BLOB oraz z lokalnego systemu plików HDFS do ADLS Gen2, funkcja ADF automatycznie wykonuje punkt kontrolny w dużym zakresie. Jeśli uruchomienie działania kopiowania nie powiodło się lub upłynął limit czasu przy kolejnej ponowieniu próby (Pamiętaj o ponowieniu liczby > 1), kopiowanie zostanie wznowione od ostatniego punktu awarii zamiast od początku.

## <a name="network-security"></a>Bezpieczeństwo sieci 

Domyślnie usługa ADF przesyła dane z lokalnego systemu plików HDFS do platformy Azure Blob Storage lub Azure Data Lake Storage Gen2 przy użyciu szyfrowanego połączenia za pośrednictwem protokołu HTTPS.  Protokół HTTPS zapewnia szyfrowanie danych podczas przesyłania i uniemożliwia podsłuchiwanie i ataki typu man-in-the-middle. 

Alternatywnie, jeśli nie chcesz, aby dane były transferowane za pośrednictwem publicznej sieci Internet, możesz uzyskać wyższe zabezpieczenia, przekazując dane za pośrednictwem prywatnego linku komunikacji równorzędnej za pośrednictwem usługi Azure Express Route. Zapoznaj się z architekturą rozwiązania poniżej, w jaki sposób można to osiągnąć.

## <a name="solution-architecture"></a>Architektura rozwiązania

Migrowanie danych za pośrednictwem publicznej sieci Internet:

![Rozwiązanie — architektura — sieć publiczna](media/data-migration-guidance-hdfs-to-azure-storage/solution-architecture-public-network.png)

- W tej architekturze dane są przesyłane bezpiecznie przy użyciu protokołu HTTPS przez publiczny Internet. 
- Zaleca się używanie trybu pomocą distcp APD w środowisku sieci publicznej. Dzięki temu możesz nie tylko wykorzystać istniejący wydajny klaster, aby osiągnąć najlepszą przepustowość kopiowania, ale również uzyskać korzyści wynikające z elastycznego planowania i ujednoliconego monitorowania w ramach funkcji ADF.
- Aby przesłać polecenie pomocą distcp do klastra usługi Hadoop i monitorować stan kopii, należy zainstalować na komputerze z systemem Windows własne środowisko Integration Runtime. Na tym komputerze nie będzie można przenieść danych (tylko do celów kontroli), ponieważ pojemność maszyny nie ma wpływu na przepływność przenoszenia danych.
- Istniejące parametry z polecenia pomocą distcp są obsługiwane.


Migruj dane za pośrednictwem prywatnego linku: 

![Rozwiązanie — architektura — sieć prywatna](media/data-migration-guidance-hdfs-to-azure-storage/solution-architecture-private-network.png)

- W tej architekturze migracja danych odbywa się za pośrednictwem prywatnego linku komunikacji równorzędnej za pośrednictwem usługi Azure Express Route, tak że dane nigdy nie przechodzą przez publiczny Internet.
- Narzędzie pomocą distcp nie obsługuje punktu końcowego prywatnej komunikacji równorzędnej i usługi Azure Storage VNet, dlatego zaleca się użycie natywnej funkcji ADF za pośrednictwem środowiska Integration Runtime w celu przeprowadzenia migracji danych.
- Aby osiągnąć tę architekturę, należy zainstalować środowisko Integration Runtime (samodzielne) na maszynie wirtualnej z systemem Windows w sieci wirtualnej platformy Azure. Możesz ręcznie skalować maszynę wirtualną lub skalować ją do wielu maszyn wirtualnych, aby w pełni wykorzystać liczbę operacji we/wy na sekundę sieci i magazynu.
- Zalecana konfiguracja do uruchomienia dla każdej maszyny wirtualnej platformy Azure (z zainstalowaną funkcją automatycznej obsługi systemu APD) to Standard_D32s_v3 z 32 vCPU i 128 GB pamięci. Można monitorować użycie procesora i pamięci maszyny wirtualnej podczas migracji danych, aby sprawdzić, czy jest konieczne dalsze skalowanie maszyny wirtualnej w celu uzyskania lepszej wydajności lub skalowanie maszyny wirtualnej w dół w celu oszczędności kosztów.
- Możesz również skalować w poziomie, kojarząc maksymalnie 4 węzły maszyny wirtualnej za pomocą pojedynczego środowiska IR obsługiwanego przez siebie. Pojedyncze zadanie kopiowania uruchomione dla samodzielnego środowiska IR będzie automatycznie dzielić zestaw plików i korzystać ze wszystkich węzłów maszyny wirtualnej w celu równoległego kopiowania plików. Aby zapewnić wysoką dostępność, zaleca się Rozpoczynanie pracy z dwoma węzłami maszyny wirtualnej w celu uniknięcia single point of failure podczas migracji danych.
- W ramach tej architektury można osiągnąć zarówno migrację danych początkowej migawki, jak i migrację danych różnicowych.


## <a name="implementation-best-practices"></a>Najlepsze rozwiązania w zakresie implementacji 

### <a name="authentication-and-credential-management"></a>Zarządzanie uwierzytelnianiem i poświadczeniami 

- Aby przeprowadzić uwierzytelnianie w systemie plików HDFS, możesz użyć [systemu Windows (Kerberos) lub Anonymous](https://docs.microsoft.com/azure/data-factory/connector-hdfs#linked-service-properties). 
- Aby nawiązać połączenie z usługą Azure Blob Storage, obsługiwane są wiele typów uwierzytelniania.  Korzystanie z [tożsamości zarządzanych dla zasobów platformy Azure](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage#managed-identity) jest zdecydowanie zalecane: Wbudowana na podstawie automatycznie zarządzanego podajnika APD w usłudze Azure AD umożliwia konfigurowanie potoków bez podawania poświadczeń w definicji połączonej usługi.  Alternatywnie można uwierzytelnić się w usłudze Azure Blob Storage przy użyciu [nazwy głównej usługi](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage#service-principal-authentication), [sygnatury dostępu](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage#shared-access-signature-authentication)współdzielonego lub [klucza konta magazynu](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage#account-key-authentication). 
- Do łączenia się z Azure Data Lake Storage Gen2 są również obsługiwane wiele typów uwierzytelniania.  Korzystanie z [tożsamości zarządzanych dla zasobów platformy Azure](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage#managed-identity) jest zdecydowanie zalecane, ale można również użyć [nazwy głównej usługi](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage#service-principal-authentication) lub [klucza konta magazynu](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage#account-key-authentication) . 
- Jeśli nie używasz tożsamości zarządzanych dla zasobów platformy Azure, [przechowywanie poświadczeń w Azure Key Vault](https://docs.microsoft.com/azure/data-factory/store-credentials-in-key-vault) jest zdecydowanie zalecane, aby ułatwić centralne zarządzanie kluczami i ich obracanie bez konieczności modyfikowania połączonych usług ADF.  Jest to również jedno z [najlepszych rozwiązań dotyczących](https://docs.microsoft.com/azure/data-factory/continuous-integration-deployment#best-practices-for-cicd)ciągłej integracji/ciągłego wdrażania. 

### <a name="initial-snapshot-data-migration"></a>Migracja początkowej danych migawek 

W trybie pomocą distcp ADF można utworzyć jedno działanie kopiowania, aby przesłać polecenie pomocą distcp z innymi parametrami w celu sterowania zachowaniem początkowej migracji danych. 

W trybie natywnego IR w usłudze ADF partycja danych jest zalecana szczególnie w przypadku migrowania więcej niż 10 TB danych. Aby podzielić dane na partycje, Skorzystaj z nazw folderów w systemie plików HDFS, a następnie każde zadanie kopiowania ADF może skopiować jedną partycję folderu jednocześnie. Aby zapewnić lepszą przepływność, można uruchomić wiele zadań kopiowania ADF współbieżnie.
Jeśli którekolwiek z zadań kopiowania zakończy się niepowodzeniem z powodu przejściowego problemu z siecią lub magazynem danych, można ponownie uruchomić zadanie kopiowania zakończonego niepowodzeniem w celu ponownego załadowania określonej partycji z systemu plików HDFS. Nie wpłynie to na wszystkie inne zadania kopiowania ładujące inne partycje.

### <a name="delta-data-migration"></a>Migracja danych różnicowych 

W trybie pomocą distcp ADF można użyć parametrów wiersza polecenia pomocą distcp "-Update, Zapisz dane, gdy plik źródłowy i docelowy różnią się wielkością", aby uzyskać migrację danych różnicowych.

W trybie natywnego środowiska IR usługi ADF najbardziej wydajnym sposobem identyfikowania nowych lub zmienionych plików z systemu plików HDFS jest użycie konwencji nazewnictwa z podziałem czasu na partycje, gdy dane w systemie plików HDFS zostały podzielone na partycje z informacjami o wycinkach czasowych w nazwie pliku lub folderu (na przykład/yyyy/mm/dd/ plik. csv), a następnie potok może łatwo identyfikować, które pliki/foldery mają być kopiowane przyrostowo.
Alternatywnie, jeśli dane w systemie HDFS nie są podzielone na partycje, ADF może identyfikować nowe lub zmienione pliki przez ich LastModifiedDate. W ten sposób jest to, że zestaw ADF przeskanuje wszystkie pliki z systemu plików HDFS i skopiuje nowy i zaktualizowany plik, którego Ostatnia zmodyfikowana sygnatura czasowa jest większa niż określona wartość. Należy pamiętać, że jeśli masz dużą liczbę plików w systemie plików HDFS, początkowe skanowanie plików może zająć dużo czasu, niezależnie od liczby plików pasujących do warunku filtru. W takim przypadku sugerowane jest Partycjonowanie danych przy użyciu tej samej partycji na potrzeby migracji początkowej migawki, dzięki czemu skanowanie plików może być wykonywane równolegle.

### <a name="estimating-price"></a>Cena szacunkowa 

Rozważmy następujące potoki skonstruowane do migrowania danych z systemu plików HDFS do platformy Azure Blob Storage: 

![Cennik — potok](media/data-migration-guidance-hdfs-to-azure-storage/pricing-pipeline.png)

Załóżmy, że: 

- Całkowity wolumin danych to 1 PB
- Migrowanie danych przy użyciu drugiej architektury rozwiązania (tryb natywnego środowiska IR APD)
- 1 PB jest podzielony na partycje 1000, a każda kopia przenosi jedną partycję
- Każde działanie kopiowania jest skonfigurowane z użyciem jednego własnego środowiska IR skojarzonego z 4 maszynami i osiąga przepływność 500 MB/s.
- Ustawienie współbieżności ForEach jest ustawione na 4, a przepustowość zagregowana to 2 GB/s
- W sumie wykonanie migracji trwa 146 godzin.


Oto Szacowana cena oparta na powyższych założeniach: 

![Cennik — tabela](media/data-migration-guidance-hdfs-to-azure-storage/pricing-table.png)

> [!NOTE]
> Jest to hipotetyczny przykład cen.  Rzeczywiste ceny są uzależnione od rzeczywistej przepływności w danym środowisku.
> Cena maszyny wirtualnej z systemem Windows Azure (z zainstalowanym własnym środowiskiem Integration Runtime) nie jest uwzględniona.

### <a name="additional-references"></a>Dodatkowa dokumentacja 
- [Łącznik systemu plików HDFS](https://docs.microsoft.com/azure/data-factory/connector-hdfs)
- [Łącznik usługi Azure Blob Storage](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage)
- [Łącznik Azure Data Lake Storage Gen2](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage)
- [Przewodnik dostrajania wydajności działania kopiowania](https://docs.microsoft.com/azure/data-factory/copy-activity-performance)
- [Tworzenie i Konfigurowanie samoobsługowego Integration Runtime](https://docs.microsoft.com/azure/data-factory/create-self-hosted-integration-runtime)
- [Własne środowisko Integration Runtime HA i skalowalność](https://docs.microsoft.com/azure/data-factory/create-self-hosted-integration-runtime#high-availability-and-scalability)
- [Zagadnienia dotyczące zabezpieczeń przenoszenia danych](https://docs.microsoft.com/azure/data-factory/data-movement-security-considerations)
- [Przechowywanie poświadczeń w Azure Key Vault](https://docs.microsoft.com/azure/data-factory/store-credentials-in-key-vault)
- [Przyrostowo Kopiuj plik na podstawie nazwy pliku podzielonego na partycje](https://docs.microsoft.com/azure/data-factory/tutorial-incremental-copy-partitioned-file-name-copy-data-tool)
- [Kopiuj nowe i zmienione pliki w oparciu o LastModifiedDate](https://docs.microsoft.com/azure/data-factory/tutorial-incremental-copy-lastmodified-copy-data-tool)
- [Strona cennika ADF](https://azure.microsoft.com/pricing/details/data-factory/data-pipeline/)

## <a name="next-steps"></a>Następne kroki

- [Kopiowanie plików z wielu kontenerów za pomocą Azure Data Factory](solution-template-copy-files-multiple-containers.md)