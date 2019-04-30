---
title: Najważniejsze wskazówki dotyczące kosztów i obciążeń rozmiaru migracji na platformę Azure | Dokumentacja firmy Microsoft
description: Pobieranie najlepszych rozwiązań dla wyceny i rozmiary obciążeń migracji na platformę Azure.
author: rayne-wiselman
manager: carmonm
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 12/08/2018
ms.author: raynew
ms.openlocfilehash: 6f6440e12840538614b4092b173ab25ae37a68a6
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60790650"
---
# <a name="best-practices-for-costing-and-sizing-workloads-migrated-to-azure"></a>Najlepsze rozwiązania dotyczące obciążeń wyceny i zmiany rozmiaru migracji na platformę Azure

Jako plan i projektowanie pod kątem migracji skupiając się na kosztach zapewnia długoterminowym sukcesie migracji do platformy Azure. W czasie trwania projektu migracji, jest krytyczny, że wszystkie zespoły (finanse, zarządzania, aplikacji teams itp.) zrozumieć powiązanych z nimi kosztów.
- Przed migracją, Szacowanie migracji spędzają, przy użyciu planu bazowego co miesiąc, co kwartał, i roczne cele budżetu ma kluczowe znaczenie dla sukcesu.
- Po migracji należy zoptymalizować koszty, ciągłe monitorowanie obciążeń i planowanie wzorce użycia w przyszłości. Migrowanych zasobów może uruchomić jako jeden typ obciążenia, ale do innego typu z czasem ewoluować, na podstawie użycia, koszty i zmiany wymagań biznesowych.

W tym artykule opisano najlepsze rozwiązania dotyczące kosztów i zmiany rozmiaru przed i po migracji.  

> [!IMPORTANT]
> Najlepsze rozwiązania i opinie opisanych w tym artykule opierają się na platformie Azure oraz z funkcji dostępnych w momencie pisania. Funkcje i możliwości zmiany w czasie. Nie wszystkie zalecenia może być odpowiednie dla danego wdrożenia, więc wybierz, która Ci odpowiada.


## <a name="before-migration"></a>Przed migracją 

Przed przeniesieniem obciążeń do chmury, oszacować miesięczny koszt działające na platformie Azure. Proaktywne Zarządzanie kosztami chmury pomaga spełnić budżetu (OpEx) koszty operacyjne. Jeśli budżet jest ograniczona, weź to pod uwagę przed migracją.  Ile to możliwe zmniejszyć koszty, warto rozważyć przekonwertowanie obciążeń do technologii bezserwerowej platformy Azure.

Najlepsze rozwiązania w tej sekcji ułatwiają Szacowanie kosztów, wykonać odpowiedni rozmiar dla maszyn wirtualnych i magazynu, korzystać z korzyści użycia hybrydowego platformy Azure, korzystanie z zarezerwowanych maszyn wirtualnych i Oszacuj chmury wydatków w ramach subskrypcji.



## <a name="best-practice-estimate-monthly-workload-costs"></a>Najlepszym rozwiązaniem jest: Oszacuj miesięczne koszty używania obciążenia
 
Aby przeprowadzić prognozowanie, rachunek miesięczny dla migrowanych obciążeń, istnieje szereg narzędzi, których można użyć.

- **Kalkulator cen platformy Azure**: Możesz wybrać produkty, aby oszacować, na przykład maszyny wirtualne i magazyn. Koszty są wprowadzane do Kalkulatora cen, aby zbudować oszacowanie.

  ![Kalkulator cen platformy Azure](./media/migrate-best-practices-costs/pricing.png) *kalkulatora cen platformy Azure*

- **Usługa Azure Migrate**: Aby oszacować koszty, należy przejrzeć i kont dla wszystkich zasobów, które są wymagane do uruchamiania obciążeń na platformie Azure. Aby uzyskać te dane, należy utworzyć spis zasobów, w tym serwerów, maszyn wirtualnych, baz danych i magazynu. Usługa Azure Migrate służy do zebrania tych informacji.

  - Usługa Azure Migrate umożliwia odnalezienie i ocenia środowisku lokalnych w celu zapewnienia magazynu.
  - Usługa Azure Migrate można mapować i wyświetlić zależności między maszynami wirtualnymi, aby mieć pełny obraz.
  - Ocena usługi Azure Migrate zawiera szacowany koszt.
    - Koszty operacji obliczeniowych: Przy użyciu rozmiaru maszyny Wirtualnej platformy Azure, zalecany, jeśli tworzysz ocenę, usługa Azure Migrate używa interfejsu API rozliczeń do obliczania szacowany miesięczny koszt maszyny Wirtualnej. Oszacowanie uwzględnia systemu operacyjnego, programu software assurance, zarezerwowane wystąpienia, maszyny Wirtualnej przestojów, lokalizacji i ustawienia waluty. Agreguje koszt wszystkich maszyn wirtualnych do oceny i oblicza łączny koszt miesięczny obliczeń.
    - Koszt usługi Storage: Usługa Azure Migrate oblicza całkowite miesięczne koszty magazynu przez agregowanie koszty magazynowania wszystkich maszyn wirtualnych w ocenie. Miesięczny koszt przechowywania dla konkretnej maszyny można obliczyć przez agregowanie miesięcznych kosztów wszystkie dyski dołączone do niego. 

    ![Usługa Azure Migrate](./media/migrate-best-practices-costs/assess.png)
    *oceny usługa Azure Migrate*

**Dowiedz się więcej:**
- [Użyj](https://azure.microsoft.com/pricing/calculator/) kalkulatora cen platformy Azure.
- [Zapoznaj się z omówieniem](https://docs.microsoft.com/azure/migrate/migrate-overview) usługa Azure migrate.
- [Przeczytaj o](https://docs.microsoft.com/azure/migrate/concepts-assessment-calculation) oceny usługa Azure Migrate.
- [Dowiedz się więcej](https://docs.microsoft.com/azure/dms/dms-overview) o Database Migration Service (DMS).

## <a name="best-practice-right-size-vms"></a>Najlepszym rozwiązaniem jest: Odpowiedni rozmiar maszyn wirtualnych

Można wybrać kilka opcji, gdy wdrażanie maszyn wirtualnych platformy Azure w celu obsługi obciążeń. Każdy typ maszyny Wirtualnej ma określonych funkcji i różnymi kombinacjami procesora CPU, pamięci i dysków. Maszyny wirtualne są zgrupowane w następujący sposób.

**Typ** | **Szczegóły** | **Korzystanie**
--- | --- | ---
**Zastosowania ogólne** | Zrównoważona moc procesora CPU w stosunku do pamięci. | Nadaje się do testowania i programowania, małych i średnich baz danych, niskim lub średnim ruchu serwerów sieci web.
**Oferujące zoptymalizowane możliwości obliczeniowe** | Duża moc procesora CPU w stosunku do pamięci. | Dobre dla serwera sieci web lub średnim ruchu, urządzeń sieciowych, procesów wsadowych, serwery aplikacji.
**Zoptymalizowane pod kątem pamięci** | Wysokie pamięci do-użycie procesora CPU. | Dobre dla relacyjnych baz danych, średnich i dużych pamięci podręcznej, analizowania w pamięci.
**Optymalizacja pod kątem magazynu** | Wysoka przepływność dysku i duża liczba operacji we/wy. | Dobre dla danych big data, bazy danych SQL i NoSQL.
**Optymalizacja pod kątem procesora GPU** | Maszyny wirtualne wyspecjalizowane pod. Jednym lub wieloma procesorami GPU. | Duże grafiki i edytowania materiałów wideo.
**Wysoka wydajność** | Najszybszym i najbardziej wydajnymi procesorami CPU. Maszyny wirtualne z interfejsami opcjonalnej sieci o wysokiej przepływności (RDMA) | Krytyczne aplikacje o wysokiej wydajności.

- Należy zrozumieć różnicami cen tych maszyn wirtualnych i długoterminowe efekty budżetu.
- Każdy typ ma wiele serii maszyny Wirtualnej znajdujący się w nim.
- Ponadto po wybraniu maszyny Wirtualnej w ramach serii, można tylko skalować maszyny Wirtualnej i zmniejszana w tej serii. Na przykład, DSv2\_2 można skalować w górę do DSv2\_4, ale nie można zmienić na różnych serii, takich jak Fsv2\_2.

**Dowiedz się więcej:**
- [Dowiedz się więcej](https://docs.microsoft.com/azure/virtual-machines/windows/sizes) o typach maszyn wirtualnych i rozmiaru oraz rozmiary mapy do typów.
- [Planowanie](https://docs.microsoft.com/azure/cloud-services/cloud-services-sizes-specs) rozmiaru maszyny Wirtualnej.
- [Przegląd](https://docs.microsoft.com/azure/migrate/contoso-migration-assessment) przykładowa ocena dla fikcyjnej firmy Contoso.

## <a name="best-practice-select-the-right-storage"></a>Najlepszym rozwiązaniem jest: Wybierz odpowiednie Magazyn

Dostrajanie i zachowaniu magazynu lokalnego (sieci SAN i NAS) i sieci obsługujących je, może być kosztowne i czasochłonne. Do chmury, aby zmniejszyć operacyjne i zarządzania problemy najczęściej są migrowane dane plików (magazyn). Firma Microsoft oferuje kilka opcji przenoszenia danych na platformie Azure, a potrzebne do podejmowania decyzji o tych opcjach. Pobrania odpowiedni typ magazynu danych można zapisać organizacji kilka tysięcy dolarów każdego miesiąca. Kilka istotnych kwestii:

- Dane, które nie są dostępne w wielu i nie jest krytyczne dla prowadzonej działalności, nie musi być umieszczone w magazynie najbardziej kosztowne.
- Z drugiej strony ważne dane krytyczne dla działania firmy powinny się znajdować na wyższe opcje magazynu warstwy.
- Podczas planowania migracji należy pobrać magazynu danych i klasyfikowania je według ważności, w celu zamapowania go na najbardziej odpowiednim magazynu. Należy wziąć pod uwagę budżetu i koszty, a także wydajność. Koszt nie powinien być zawsze głównym czynnikiem podejmowania decyzji. Pobrania tańszych opcji może spowodować obciążenie na wydajność i dostępność zagrożenia. 

### <a name="storage-data-types"></a>Typy danych magazynu

System Azure oferuje różne typy magazynu danych.

| **Typ danych** | **Szczegóły** | **Użycie** |
|--- | --- |  --- |
|**Obiekty blob** | Zoptymalizowane pod kątem przechowywania dużych ilości pozbawionych struktury obiektów, takich jak dane tekstowe lub binarne<br/>Dostęp do danych z dowolnego miejsca za pośrednictwem protokołu HTTP/HTTPS. | Użycie dla scenariuszy dostępu losowa i przesyłania strumieniowego. Na przykład do udostępniania obrazów i dokumentów bezpośrednio w przeglądarce, przesyłanie strumieniowe audio i wideo i przechowywania danych odzyskiwania kopii zapasowych i odzyskiwanie po awarii.|
|**Pliki** | Zarządzane udziały plików udostępnianych za pośrednictwem protokołu SMB 3.0 | Podczas migrowania lokalnych udziałów plików i w celu zapewnienia wielu połączeń/dostępu do danych plików.|
|**Dyski** | Oparte na stronicowych obiektów blob.<br/><br/> Typ dysku (szybkość): Standardowa (HDD lub SSD) lub Premium (SSD).<br/><br/>Zarządzanie dyskami: Niezarządzane (zarządzasz ustawień dysku i magazynu) lub zarządzany (Wybierz typ dysku i platformy Azure zarządza dysku). | Używanie dysków w warstwie Premium dla maszyn wirtualnych. Używanie dysków zarządzanych dla proste zarządzanie i skalowania.|
|**kolejki** | Store i pobierania dużej liczby wiadomości, dostępne za pośrednictwem uwierzytelnionych połączeń (HTTP lub HTTPS) | Połącz składniki aplikacji za pomocą Kolejkowanie komunikatów asynchronicznych.|
|**Tabele** | Store tabel. | Teraz część interfejsu API tabeli usługi Azure Cosmos DB.|



### <a name="access-tiers"></a>Poziomy dostępu
Usługa Azure storage udostępnia różne opcje do uzyskiwania dostępu do danych obiektów blob bloku. Wybieranie warstwy odpowiednich uprawnień dostępu pomaga upewnić się, że blok danych obiektów blob w sposób najbardziej efektywny.

**Typ** | **Szczegóły** | **Użycie**
--- | --- | ---
**gorąca** | Magazyn wyższe koszty niż Superpaska. Niższe opłaty za dostęp niż Superpaska.<br/><br/>Jest to domyślna warstwa. | Użyj dla danych aktywnie używany, w których korzysta się często.
**chłodna** | Niższe koszty magazynowania niż gorąca. Wyższe opłaty za dostęp niż gorąca.<br/><br/> Store dla co najmniej 30 dni. | Store krótkoterminowej, dane są dostępne, ale używanych rzadko.
**Archiwizowanie** | Używane dla poszczególnych blokowych obiektów blob.<br/><br/> Najbardziej ekonomiczna opcja dla magazynu. Dostęp do danych jest droższe niż gorące i zimne. | Na użytek dane, które mogą tolerować serwera godzin opóźnienia w pobieraniu i pozostaną w warstwie, przez co najmniej 180 dni. 

### <a name="storage-account-types"></a>Typy kont magazynu

System Azure oferuje różne rodzaje kont magazynu i warstwy wydajności.

**Typ konta** | **Szczegóły** | **Użycie**
--- | --- | ---
**Ogólnego przeznaczenia v2 standardowa** | Obsługuje obiekty BLOB (Blokuj, strony, dołącz), plików, dysków, kolejek i tabel.<br/><br/> Obsługuje warstwy dostępu gorąca, chłodna i archiwum. Magazyn ZRS jest obsługiwane. | Użycie dla większości scenariuszy i większość typów danych. Konta magazynu w warstwie standardowa może być dysk twardy lub opartego na dyskach SSD.
**Ogólnego przeznaczenia w wersji 2 — wersja Premium** | Obsługuje dane z magazynu obiektów Blob (stronicowych obiektów blob). Obsługuje warstwy dostępu gorąca, chłodna i archiwum. Magazyn ZRS jest obsługiwane.<br/><br/> Przechowywane na dyskach SSD. | Firma Microsoft zaleca używanie dla wszystkich maszyn wirtualnych.
**Ogólnego przeznaczenia w wersji 1** | Obsługa warstw dostępu nie jest obsługiwane. Nie obsługuje magazynu ZRS | Użyj, jeśli aplikacje wymagają modelu klasycznym wdrożeniu platformy Azure.
**Obiekt blob** | Specjalne konto magazynu do przechowywania obiektów bez struktury. Udostępnia blokowych obiektów blob i uzupełnialnych obiektów blob w tylko (nie plik, kolejka, tabela lub dysku magazynu usługi). Zawiera ten sam trwałość, dostępność, skalowalność i wydajność jako ogólnego przeznaczenia w wersji 2. | Nie można przechowywać stronicowych obiektów blob w ramach tych kont i dlatego nie można przechowywać pliki wirtualnego dysku twardego. Można ustawić warstwy dostępu gorąca lub chłodna.

### <a name="storage-redundancy-options"></a>Opcje nadmiarowości magazynu

Konta magazynu można użyć różne rodzaje nadmiarowości w celu zapewnienia odporności i wysokiej dostępności.

**Typ** | **Szczegóły** | **Użycie**
--- | --- | ---
**Magazyn lokalnie nadmiarowy (LRS)** | Chroni przed awarią lokalnej replikacji w ramach danej jednostki pojedynczy magazyn do awarii oddzielne domeny i domeny aktualizacji. Zachowuje wiele kopii danych w jednym centrum danych. Zawiera co najmniej 99,999999999% (11 9\'s) trwałości obiektów w danym roku. | Należy wziąć pod uwagę, jeśli aplikacja przechowuje dane, które można łatwo odtworzone.
**Magazyn strefowo nadmiarowy (ZRS)** | Chroni ponownie awarii centrum danych poprzez replikację między trzy klastry magazynu w jednym regionie. Każdy klaster magazyn jest fizycznie oddzielone i znajduje się w jego własnej strefie dostępności. Zawiera co najmniej 99,9999999999% (12 9\'s) trwałości obiektów w danym roku przez przechowywanie wielu kopii danych w wielu centrach danych lub regionach. | Należy wziąć pod uwagę, jeśli potrzebujesz spójności, trwałości i wysokiej dostępności. Nie może chronić przed regionalnej awarii, gdy trwale dotyczy wiele stref.
**Magazyn geograficznie nadmiarowy (GRS)** | Chroni przed awarią cały region replikowanie danych do regionie pomocniczym oddalonym setki mil od podstawowego. Zawiera co najmniej 99,99999999999999% (16 9\'s) trwałości obiektów w danym roku. | Replika danych jest niedostępna, chyba że firma Microsoft zainicjuje tryb failover do regionu pomocniczego. W przypadku przejścia do trybu failover odczytu i zapisu jest dostępna.
**Magazyn geograficznie nadmiarowy do odczytu (RA-GRS)** | Podobnie jak magazyn GRS. Zawiera co najmniej 99,99999999999999% (16 9\'s) trwałości obiektów w danym roku | Zapewnia i dostępność przez 99,99% dostępności do odczytu, umożliwiając dostęp do odczytu z drugiego regionu magazynu grs.

**Dowiedz się więcej:**
- [Przegląd](https://azure.microsoft.com/pricing/details/storage/) cennik usługi Azure Storage.
- [Dowiedz się więcej o](https://docs.microsoft.com/azure/storage/common/storage-import-export-service) Azure Import/Export migracja dużych ilości danych obiektów blob platformy Azure i plików. 
- [Porównaj](https://docs.microsoft.com/azure/storage/common/storage-decide-blobs-files-disks?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) obiektów blob, pliki i typy danych magazynu dysku.
- [Dowiedz się więcej](https://docs.microsoft.com/azure/storage/blobs/storage-blob-storage-tiers) dotyczące warstwy dostępu.
- [Przegląd](https://docs.microsoft.com/azure/storage/common/storage-account-overview?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) różnych typów kont magazynu.
- Dowiedz się więcej o [nadmiarowości magazynu](https://docs.microsoft.com/azure/storage/common/storage-redundancy), [LRS](https://docs.microsoft.com/azure/storage/common/storage-redundancy-lrs?toc=%2fazure%2fstorage%2fqueues%2ftoc.json), [ZRS](https://docs.microsoft.com/azure/storage/common/storage-redundancy-zrs?toc=%2fazure%2fstorage%2fqueues%2ftoc.json), [GRS](https://docs.microsoft.com/azure/storage/common/storage-redundancy-grs?toc=%2fazure%2fstorage%2fqueues%2ftoc.json), i [geograficznie Nadmiarowy](https://docs.microsoft.com/azure/storage/common/storage-redundancy-grs?toc=%2fazure%2fstorage%2fqueues%2ftoc.json#read-access-geo-redundant-storage).
- [Dowiedz się więcej](https://docs.microsoft.com/azure/storage/files/storage-files-introduction) temat usługi Azure Files.



## <a name="best-practice-leverage-azure-hybrid-benefits"></a>Najlepszym rozwiązaniem jest: Korzystać z korzyści użycia hybrydowego platformy Azure

Z powodu lat inwestycji w oprogramowanie w systemach, takich jak system Windows Server i programu SQL Server firma Microsoft jest w stanie unikatowy zaoferować klientom wartości w chmurze, za pomocą znaczne rabaty, zapewniających innych dostawców rozwiązań w chmurze nie zawsze. 

Zintegrowany pakiet produktów na lokalnym/Azure Microsoft generuje zalety konkurencyjnym i kosztów. Jeśli masz obecnie system operacyjny lub inne licencjonowania oprogramowania za pośrednictwem programu software assurance (SA), możesz korzystać ze te licencje do chmury dzięki korzyści użycia hybrydowego platformy Azure.

**Dowiedz się więcej:**

- [Przyjrzyj się](https://azure.microsoft.com/pricing/hybrid-benefit/) Kalkulator korzyści użycia hybrydowego.
- [Dowiedz się więcej](https://azure.microsoft.com/pricing/hybrid-benefit/) o korzyści użycia hybrydowego dla systemu Windows Server.
- [Przegląd](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-pricing-guidance) ceny wskazówki dotyczące maszyn wirtualnych programu SQL Server Azure.


## <a name="best-practice-use-reserved-vm-instances"></a>Najlepszym rozwiązaniem jest: Użyj wystąpienia zarezerwowane maszyn wirtualnych

Większość platform w chmurze są konfigurowane jako płatność za rzeczywiste użycie. Będzie to przedstawia zalety modelu, a ponieważ nie zawsze wiadomo, jak dynamiczna obciążenia. Po określeniu wyczyść zamiarach w przypadku obciążeń, ułatwiają planowanie infrastruktury.

Korzystając z wystąpieniami zarezerwowanymi maszyn wirtualnych platformy Azure, przedpłaty dla nich lub trzy lata termin wystąpienia maszyny Wirtualnej. 

- Przedpłaty zawiera rabat w wysokości wykorzystane zasoby.
- Maszyny Wirtualnej, obliczeń bazy danych SQL, usługi Azure Cosmos DB i innych kosztów zasobów mogą znacznie zmniejszyć do 72% na ceny zgodnie z rzeczywistym użyciem. 
- Rezerwacje Podaj rozliczeń rabat, a nie wpływają na stan środowiska uruchomieniowego zasobów.
- Możesz anulować zarezerwowane wystąpienia.

![Zarezerwowane wystąpienia](./media/migrate-best-practices-costs/reserve.png)
*zarezerwowane maszyn wirtualnych na platformie Azure*

**Dowiedz się więcej:**
- [Dowiedz się więcej o](https://docs.microsoft.com/azure/billing/billing-save-compute-costs-reservations) rezerwacje platformy Azure.
- [Odczyt](https://azure.microsoft.com/pricing/reserved-vm-instances/#faq) wystąpień zarezerwowanych — często zadawane pytania.
- [Uzyskaj cen wskazówki](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-pricing-guidance) maszyn wirtualnych programu SQL Server platformy Azure.


## <a name="best-practice-aggregate-cloud-spend-across-subscriptions"></a>Najlepszym rozwiązaniem jest: Wydatki na chmurę agregacji w subskrypcjach

Jest nieuniknione, że po pewnym czasie będziesz mieć więcej niż jedną subskrypcję platformy Azure. Na przykład może być konieczne dodatkową subskrypcję do oddzielania granice rozwoju i produkcji lub może być to platforma, która wymaga oddzielnej subskrypcji dla każdego klienta. O możliwość agregowania danych raportowania we wszystkich subskrypcjach w pojedynczej platformy jest funkcją wartościowe.

Aby to zrobić, można użyć interfejsów API usługi Azure Cost Management. Następnie po agregowanie danych do pojedynczego źródła, takich jak Azure SQL, można użyć narzędzi, takich jak usługa Power BI pozwala przedstawić dane zagregowane. Można utworzyć raporty zagregowane subskrypcji i szczegółowe raporty. Na przykład dla użytkowników, którzy potrzebują aktywnego wgląd w koszty zarządzania, utworzeniem określonych widoków kosztów, oparte na działu, grupa zasobów itd. Nie ma potrzeby dostarczać pełny dostęp do danych dotyczących rozliczeń platformy Azure.

**Dowiedz się więcej:**

- [Zapoznaj się z omówieniem](https://docs.microsoft.com/azure/billing/billing-consumption-api-overview) interfejsu API użycia platformy Azure.
- [Dowiedz się więcej o](https://docs.microsoft.com/power-bi/desktop-connect-azure-consumption-insights) nawiązywania połączenia z usługi Azure Consumption Insights w programie Power BI Desktop.
- [Dowiedz się, jak](https://docs.microsoft.com/azure/billing/billing-manage-access) zarządzanie dostępem do informacji rozliczeniowych dla platformy Azure przy użyciu kontroli dostępu opartej na rolach (RBAC).
 
## <a name="after-migration"></a>Po migracji

Po zakończeniu pomyślną migrację obciążeń oraz kilku tygodni od zbierania danych użycia będziesz mieć dalsze kosztów zasobów.
- Jak możesz analizować dane, można uruchomić generowania budżetu punkt odniesienia dla grup zasobów platformy Azure i zasobów.
- Następnie jak poznać, gdzie jest poświęcony budżetu chmury, możesz analizować sposób jeszcze bardziej ograniczyć koszty.

Najlepsze rozwiązania w tej sekcji obejmują koszt budżetowanie i analizy, monitorowanie zasobów i budżetów grupy zasobów i wdrażaniu monitorowania magazynu i maszyn wirtualnych przy użyciu usługi Azure Cost Management.

## <a name="best-practice-use-azure-cost-management"></a>Najlepszym rozwiązaniem jest: Użyj usługi Azure Cost Management

Firma Microsoft udostępnia usługi Azure Cost Management, aby ułatwić śledzenie wydatków w następujący sposób:

- Ułatwia monitorowanie i kontrolowanie wydatków platformy Azure i optymalizacja zużycia zasobów.
- Przegląda cały subskrypcji i wszystkie jej zasoby i przygotowuje zalecenia.
- Zapewnia pełną interfejsu API do integracji z zewnętrznymi narzędziami i systemami finansowych dla raportowania.
- Śledzenie użycia zasobów i zarządzania kosztami chmury z jednym, ujednoliconym widoku.
- Udostępnia operacyjnymi i finansowymi szczegółowe informacje ułatwiające podejmowanie świadomych decyzji.

W Cost Management możesz wykonywać następujące czynności:


- **Tworzenie budżetu**: Tworzenie budżetu odpowiedzialności finansowej.
  - Użytkownik może uwzględnić services używają lub subskrybować dla określonego okresu (co miesiąc, co kwartał, co roku) i zakresu (subskrypcjach/grupach zasobów). Na przykład można utworzyć budżetu subskrypcji platformy Azure w okresie miesięcznym, kwartałów lub.
  - Po utworzeniu budżetu, jest wyświetlana na analizy kosztów. Przeglądanie budżetu względem bieżących wydatków jest jednym z pierwszych kroków w razie analizując koszty i wydatki.
  - Mogą być wysyłane powiadomienia e-mail, po osiągnięciu progów budżetu.
  - Możesz wyeksportować dane zarządzania kosztami, do usługi Azure storage do analizy.

    ![Budżet kosztów zarządzania](./media/migrate-best-practices-costs/budget.png)
    *budżetu w usłudze Azure Cost Management*

- **Wykonaj analizę kosztów**: Rozpoczynanie analizy kosztów, aby eksplorować i analizować koszty organizacji, które pomagają zrozumieć, jak są naliczane koszty i identyfikowania trendów wydatków.
  - Analiza kosztów jest dostępna dla użytkowników, umowy EA.
  - Możesz wyświetlić dane analizy kosztów liczbę zakresów, m.in. przez dział, konta, subskrypcji lub grupy zasobów.
  - Możesz uzyskać analizy kosztów, które przedstawia łączne koszty dla bieżącego miesiąca i skumulowany dziennych kosztów. 

    ![Zarządzanie analiza kosztów](./media/migrate-best-practices-costs/analysis.png)
    *analizy usługi Azure Cost Management*
- **Uzyskiwanie zaleceń dotyczących**: Uzyskuj zalecenia usługi Advisor, które pokazują, jak zoptymalizować i zwiększyć wydajność.


**Dowiedz się więcej:**

- [Zapoznaj się z omówieniem](https://docs.microsoft.com/azure/cost-management/overview) z usługi Azure Cost Management.
- [Dowiedz się, jak](https://docs.microsoft.com/azure/cost-management/cost-mgt-best-practices) optymalizacji inwestycji w chmurę dzięki usłudze Azure Cost Management.
- [Dowiedz się, jak](https://docs.microsoft.com/azure/cost-management/use-reports) korzystanie z raportów w usłudze Azure Cost Management.
- [Pobierz samouczek](https://docs.microsoft.com/azure/cost-management/tutorial-acm-opt-recommendations?toc=/azure/billing/TOC.json) dotyczące optymalizacji kosztów z zaleceń.
- [Przegląd](https://docs.microsoft.com/rest/api/consumption/budgets) użycie platformy Azure, interfejsu API.

## <a name="best-practice-monitor-resource-utilization"></a>Najlepszym rozwiązaniem jest: Monitorowanie wykorzystania zasobów

Na platformie Azure płacisz za to, czego używasz, gdy zasoby są używane i nie płać, gdy nie są one. W przypadku maszyn wirtualnych rozliczeń występuje, gdy maszyny Wirtualnej została przydzielona i nie są naliczane po cofnięciu przydziału maszyny Wirtualnej. Pamiętając o tym powinien monitorować maszyny wirtualne w użyciu i Sprawdź zmiany rozmiaru maszyny Wirtualnej.

- Stale ocenia obciążenia maszyny Wirtualnej do określenia podstawy.
- Na przykład jeśli obciążenie jest używany w dużym stopniu od poniedziałku do piątku, 8: 00 do 18: 00, jednak rzadko używana poza tymi godzinami, możesz obniżyć maszyn wirtualnych poza godziny szczytu. Może to oznaczać, zmienianie rozmiarów maszyn wirtualnych lub zestawów za pomocą skalowania maszyn wirtualnych do maszyn wirtualnych z funkcją automatycznego skalowania w górę lub w dół.
- Niektóre firmy "Odłóż", maszyn wirtualnych przez umieszczenie ich w kalendarzu, który określa kiedy powinna być dostępna, a jeśli one nie potrzebne. 
- Oprócz monitorowania maszyn wirtualnych, należy monitorować innych zasobów sieciowych, takich jak usługi ExpressRoute i bramy sieci wirtualnej dla niedostatecznie i nadmiernie użycie.
- Możesz monitorować użycie maszyny Wirtualnej przy użyciu narzędzi firmy Microsoft, takich jak usługi Azure Cost Management, Azure Monitor i Azure Advisor. Dostępne są również narzędzia innych producentów.  

**Dowiedz się więcej:**
- Zapoznaj się z omówieniem programu [usługi Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/overview) i [usługi Azure Advisor](https://docs.microsoft.com/azure/advisor/advisor-overview).
- [Pobierz](https://docs.microsoft.com/azure/advisor/advisor-cost-recommendations) Advisor koszt zalecenia.
- [Dowiedz się, jak [optymalizować koszty od zalecanych](https://docs.microsoft.com/azure/cost-management/tutorial-acm-opt-recommendations?toc=/azure/billing/TOC.json), i [uniknąć nieoczekiwanych opłat](https://docs.microsoft.com/azure/billing/billing-getting-started).
- [Dowiedz się więcej o](https://github.com/Azure/azure-quickstart-templates/tree/master/azure-resource-optimization-toolkit/) zestaw narzędzi do optymalizacji (ARO) zasobów platformy Azure

## <a name="best-practice-implement-resource-group-budgets"></a>Najlepszym rozwiązaniem jest: Implementowanie budżetów grupy zasobów

Często grupy zasobów są używane do reprezentowania granicach kosztów. Wraz z tego wzorca użycia zespołu platformy Azure nadal twórz nowe i ulepszone sposoby śledzić i analizować zasobów wydatków na różnych poziomach, w tym możliwość tworzenia budżet na grupę zasobów i zasoby.  

- Budżet grupy zasobów ułatwia śledzenie kosztów skojarzonych z grupą zasobów.
- Można wyzwalać alerty i uruchomić szeroką gamę elementów playbook, ponieważ osiągnięto lub przekroczono budżetu. 

**Dowiedz się więcej:**

- [Dowiedz się, jak](https://docs.microsoft.com/azure/billing/billing-cost-management-budget-scenario) Zarządzanie kosztami przy użyciu budżetów platformy Azure.
- [Ten samouczek](https://docs.microsoft.com/azure/cost-management/tutorial-acm-create-budgets?toc=/azure/billing/TOC.json) tworzenie i zarządzanie nimi budżetu na platformie Azure.


## <a name="best-practice-optimize-azure-monitor-retention"></a>Najlepszym rozwiązaniem jest: Optymalizowanie przechowywania usługi Azure Monitor

Przenoszenie zasobów na platformie Azure i włączyć rejestrowanie diagnostyczne dla nich, możesz wygenerować dużą ilość danych dziennika. Te dane dziennika jest typowo wysyłana do konta magazynu, który jest mapowany do obszaru roboczego usługi Log Analytics.

- Im dłużej okres przechowywania danych dziennika większej ilości danych należy.
- Nie wszystkie dane dzienników są równe, i niektóre zasoby powoduje generowanie większej ilości danych dziennika niż inne.
- Ze względu na przepisy i zgodności prawdopodobne jest, że konieczne będzie przechowywać dane dziennika dla niektórych zasobów, które są dłuższe niż inne.
- Staranne wiersz powinien Przeprowadź między optymalizacji kosztów magazynu dziennika i przechowywanie danych dziennika, które są potrzebne.
- Firma Microsoft zaleca, ocena i konfigurowanie rejestrowania natychmiast po zakończeniu migracji, aby nie pieniądze wydatków zachowywanie dzienników znaczenia.

**Dowiedz się więcej:**

- [Dowiedz się więcej o](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-usage-and-estimated-costs) monitorowanie użycia i szacowanych kosztów.
 
## <a name="best-practice-optimize-storage"></a>Najlepszym rozwiązaniem jest: Optymalizuj magazynowanie

Jeśli wykonano najlepsze rozwiązania dotyczące wybierania magazynu przed migracją są prawdopodobnie Owszem niektóre korzyści. Są jednak nadal można optymalizować koszty prawdopodobnie dodatkowego miejsca do magazynowania. Wraz z upływem czasu plików i obiektów blob stają się nieaktualne. Dane nie mogą być używane z już jednak wymagania prawne może oznaczać konieczność zapewnienia jego przez pewien okres. W efekcie nie może być konieczne zapisać go w używanym do migracji oryginalny magazyn o wysokiej wydajności.

Identyfikowanie i przeniesienie starych danych na tańsze obszary do przechowywania może mieć duży wpływ na budżetu miesięczny magazynu i oszczędności kosztów. System Azure oferuje wiele sposobów, aby pomóc w identyfikacji, a następnie zapisywania tej nieaktualnych danych.

- Korzystaj z warstwy dostępu dla magazynu ogólnego przeznaczenia w wersji 2, przenoszenie mniej ważnych danych z warstwy gorąca do warstwy chłodna i archiwizacji.
- Za pomocą usługi StorSimple można przenieść nieaktualnych danych na podstawie zasad niestandardowych. 

**Dowiedz się więcej:**
- [Dowiedz się więcej](https://docs.microsoft.com/azure/storage/blobs/storage-blob-storage-tiers) dotyczące warstwy dostępu.
- [Zapoznaj się z omówieniem](https://docs.microsoft.com/azure/azure-monitor/overview) magazynu storsimple, a [ceny usługi StorSimple](https://azure.microsoft.com/pricing/details/storsimple/).

## <a name="best-practice-automate-vm-optimization"></a>Najlepszym rozwiązaniem jest: Automatyzowanie optymalizacji maszyn wirtualnych

Ostatecznym celem uruchomioną Maszynę wirtualną w chmurze jest zmaksymalizować procesora CPU, pamięci i dysku, która jest używana. Jeśli odnajdywanie maszyn wirtualnych, które nie są zoptymalizowane pod kątem lub mają okresy częste, gdy maszyny wirtualne nie są używane, dobrym pomysłem będzie je wyłączyć lub skalować w dół je za pomocą zestawów skalowania maszyn wirtualnych.

Można zoptymalizować Maszynę wirtualną za pomocą usługi Azure Automation, zestawy skalowania maszyn wirtualnych, automatycznego zamykania i rozwiązania innych firm przy użyciu skryptu lub 3. 

**Dowiedz się więcej:**

- [Dowiedz się, jak](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-vertical-scale-reprovision) Użyj skalowania automatycznego w pionie.
- [Harmonogram](https://azure.microsoft.com/updates/azure-devtest-labs-schedule-vm-auto-start/) autostart maszyny Wirtualnej.
- [Dowiedz się, jak](https://docs.microsoft.com/azure/automation/automation-solution-vm-management) uruchamianiem lub zatrzymywaniem maszyn wirtualnych poza godzinami pracy w usłudze Azure Automation.
- [Uzyskaj więcej informacji] o [usługi Azure Advisor](https://docs.microsoft.com/azure/advisor/advisor-overview)i [zestaw narzędzi do optymalizacji zasobów platformy Azure (ARO)](https://github.com/Azure/azure-quickstart-templates/tree/master/azure-resource-optimization-toolkit/).

## <a name="best-practices-use-logic-apps-and-runbooks-with-budgets-api"></a>Najlepsze rozwiązania: Logic Apps i elementów runbook za pomocą interfejsu API budżetów

Platforma Azure udostępnia interfejs API REST, który ma dostęp do informacji rozliczeniowych dzierżawy.

- Interfejs API budżetów umożliwia integrowanie systemów zewnętrznych i przepływów pracy, które są wyzwalane przez metryk, który kompilujesz z danymi interfejsu API.
- Możesz ściągnąć dane dotyczące użycia i zasobów do narzędzia do analizy danych preferowany.
- Interfejsy API usługi RateCard i użycia zasobów platformy Azure mogą ułatwić dokładne przewidywanie kosztów i zarządzanie nimi.
- Interfejsy API są zaimplementowane jako dostawcy zasobów i są uwzględniane w interfejsach API udostępnianych przez usługę Azure Resource Manager.
- Interfejs API budżetów można zintegrować z usługi Azure Logic Apps i elementów Runbook.

**Dowiedz się więcej:**

- [Dowiedz się więcej](https://docs.microsoft.com/rest/api/consumption/budgets) dotyczących budżetów interfejsu API.
- [Uzyskiwanie szczegółowych informacji](https://docs.microsoft.com/azure/billing/billing-usage-rate-card-overview) do użycia platformy Azure przy użyciu interfejsu API rozliczeń.


## <a name="best-practice-implement-serverless-technologies"></a>Najlepszym rozwiązaniem jest: Implementowanie technologii bezserwerowych

Obciążenia maszyn wirtualnych są często migrowane "is" w celu uniknięcia przestojów. Często maszyny wirtualne mogą być hostowane zadania, które są tymczasowymi, biorąc krótki okres, aby uruchomić, lub też wiele godzin. Na przykład maszyny wirtualne, które są uruchamiane zaplanowane zadania, takie jak Windows zadań harmonogramu lub skryptów programu PowerShell. Podczas tych zadań nie są uruchomione, niemniej one przejmowania maszyny Wirtualnej i dysku koszty magazynowania.

Po zakończeniu migracji po szczegółowego przeglądu zadania tego typu warto rozważyć migrowanie ich do technologii bezserwerowych, takich jak zadania usługi Azure Functions lub Azure Batch. Dzięki temu rozwiązaniu nie potrzebujesz już zarządzanie i obsługa maszyn wirtualnych, dzięki temu zapisywanie dodatkowych kosztów. 

**Dowiedz się więcej:**
- [Dowiedz się więcej o](https://azure.microsoft.com/services/functions/) usługi Azure Functions
- [Dowiedz się więcej o](https://azure.microsoft.com/en-us/services/batch/) usługi Azure Batch
  
## <a name="next-steps"></a>Kolejne kroki 

Przejrzyj najlepsze rozwiązania:

- [Najlepsze praktyki](migrate-best-practices-security-management.md) zabezpieczeń i zarządzania po migracji.
- [Najlepsze praktyki](migrate-best-practices-networking.md) sieci po zakończeniu migracji.

