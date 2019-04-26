---
title: Contoso skali migracji na platformę Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak Contoso obsługuje skalowanych migracji na platformę Azure.
services: azure-migrate
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 10/08/2018
ms.author: raynew
ms.openlocfilehash: 9253051d907a811ffedad3a714112c9b25543a35
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60667451"
---
# <a name="contoso---scale-a-migration-to-azure"></a>Contoso — skalowanie migracji na platformę Azure

W tym artykule Contoso wykonuje migrację w dużej skali na platformie Azure. Uznają, jak planować i przeprowadzić migrację obciążeń ponad 3000, 8000 baz danych i ponad 10 000 maszyn wirtualnych.


W tym artykule jest jednym z serii artykułów, które dokumentują sposób fikcyjnej firmy Contoso migruje swoje zasoby lokalne do chmury Microsoft Azure. Seria zawiera tła i informacje na temat planowania i scenariusze wdrażania, które ilustrują sposób konfigurowania infrastruktury migracji, ocenić przydatność, korzystając z zasobów lokalnych do migracji i uruchom różnego rodzaju migracji. Scenariusze zwiększanie się stopnia skomplikowania. Dodamy artykuły serii wraz z upływem czasu.


**Artykuł** | **Szczegóły** | **Stan**
--- | --- | ---
[Artykuł 1: Przegląd](contoso-migration-overview.md) | Omówienie serię artykułów, strategia migracji do firmy Contoso i przykładowe aplikacje, które są używane w tej serii. | Dostępne
[Artykuł 2: Wdrażanie infrastruktury platformy Azure](contoso-migration-infrastructure.md) | Contoso przygotowuje swoją infrastrukturę lokalną i jej infrastruktury platformy Azure do migracji. Tej samej infrastruktury jest używany dla wszystkich artykułów migracji w serii. | Jest dostępna.
[Artykuł 3: Ocena zasobów lokalnych do migracji na platformę Azure](contoso-migration-assessment.md)  | Contoso uruchamia oceny aplikacji rozwiązania SmartHotel360 jej w środowisku lokalnym, działające w programie VMware. Contoso ocenia aplikacji maszyn wirtualnych przy użyciu usługi Azure Migrate bazy danych oraz aplikacji programu SQL Server przy użyciu Data Migration Assistant. | Dostępne
[Artykuł 4: Ponowne hostowanie aplikacji na maszynie Wirtualnej platformy Azure oraz wystąpienie zarządzane usługi SQL Database](contoso-migration-rehost-vm-sql-managed-instance.md) | Firmy Contoso jest uruchamiana lift-and-shift migracja na platformę Azure dla swoich aplikacji rozwiązania SmartHotel360 w środowisku lokalnym. Firma Contoso jest migrowana aplikację frontonu maszyn wirtualnych za pomocą [usługi Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/site-recovery-overview). Contoso bazy danych aplikacji jest migrowana do wystąpienia zarządzanego Azure SQL Database przy użyciu [Azure Database Migration Service](https://docs.microsoft.com/azure/dms/dms-overview). | Dostępne   
[Artykuł 5: Ponowne hostowanie aplikacji na maszynach wirtualnych platformy Azure](contoso-migration-rehost-vm.md) | Contoso migruje swojej aplikacji rozwiązania SmartHotel360 maszyn wirtualnych do maszyn wirtualnych platformy Azure przy użyciu usługi Site Recovery. | Dostępne
[Artykuł 6: Ponowne hostowanie aplikacji na maszynach wirtualnych platformy Azure i w grupie dostępności AlwaysOn programu SQL Server](contoso-migration-rehost-vm-sql-ag.md) | Contoso przeprowadza migrację aplikacji przy użyciu Site Recovery do migracji maszyn wirtualnych aplikacji i Database Migration Service migrację bazy danych aplikacji do klastra programu SQL Server, który jest chroniony przez grupy dostępności AlwaysOn. | Dostępne
[Artykuł 7: Ponowne hostowanie aplikacji systemu Linux na maszynach wirtualnych platformy Azure](contoso-migration-rehost-linux-vm.md) | Contoso zostanie ukończona lift-and-shift migrację swoich aplikacji osTicket systemu Linux na maszynach wirtualnych platformy Azure przy użyciu usługi Site Recovery. | Dostępne
[Artykuł 8: Ponowne hostowanie aplikacji systemu Linux na maszynach wirtualnych platformy Azure i usługa Azure Database for MySQL](contoso-migration-rehost-linux-vm-mysql.md) | Contoso swoją aplikację osTicket systemu Linux jest migrowana do maszyn wirtualnych platformy Azure przy użyciu usługi Site Recovery. Jej zmigrowaniu bazy danych aplikacji do usługi Azure Database for MySQL za pomocą aplikacji MySQL Workbench. | Dostępne
[Artykuł 9: Refaktoryzuj aplikacji w aplikacji internetowej platformy Azure i usługi Azure SQL Database](contoso-migration-refactor-web-app-sql.md) | Contoso wykonuje migrację swoich aplikacji rozwiązania SmartHotel360 na aplikację internetową platformy Azure i wykonuje migrację bazy danych aplikacji na wystąpienie serwera SQL Azure przy użyciu Asystenta migracji bazy danych. | Dostępne    
[Artykuł 10: Refaktoryzuj aplikacji systemu Linux w aplikacji internetowej platformy Azure i usługi Azure Database for MySQL](contoso-migration-refactor-linux-app-service-mysql.md) | Contoso przeprowadza migrację swoich aplikacji osTicket systemu Linux do aplikacji sieci web platformy Azure w wielu lokacjach. Aplikacja sieci web jest zintegrowana z usługą GitHub ciągłego dostarczania. Baza danych aplikacji jest migrowana do usługi Azure Database for MySQL — wystąpienia. | Dostępne
[Artykuł 11: Refaktoryzuj Team Foundation Server w usługach Azure DevOps](contoso-migration-tfs-vsts.md) | Contoso migruje jego lokalnego wdrożenia Team Foundation Server do usługi Azure DevOps Services na platformie Azure. | Dostępne
[Artykuł 12: Ponowne Ustalanie architektury aplikacji kontenerów platformy Azure i usługi Azure SQL Database](contoso-migration-rearchitect-container-sql.md) | Contoso migruje jego SmartHotel aplikacji na platformie Azure. Następnie rearchitects warstwy sieci web aplikacji jako kontener Windows w usłudze Azure Service Fabric i bazy danych aplikacji przy użyciu usługi Azure SQL Database. | Dostępne    
[Artykuł 13: Ponownie skompiluj aplikację na platformie Azure](contoso-migration-rebuild.md) | Contoso odbudowuje swojej aplikacji SmartHotel, korzystając z możliwości platformy Azure i usług, w tym usługi Azure App Service, Azure Kubernetes Service (AKS), usługi Azure Functions, Azure Cognitive Services i Azure Cosmos DB. | Dostępne 
Artykuł 14: Skalowanie migracji na platformę Azure | Po wypróbowanie kombinacje migracji, Contoso przygotowuje aby możliwe było skalowanie pełną migrację na platformę Azure. | W tym artykule

## <a name="business-drivers"></a>Czynniki biznesowe

Zespół kierowniczy IT ściśle podjęła współpracę z partnerami biznesowymi, aby zrozumieć, czego chcą osiągnąć za pomocą tej migracji:

- **Adres rozwój**: Contoso rośnie, co powoduje wykorzystanie w systemach lokalnych i infrastruktury.
- **Zwiększenie wydajności**: Firmy Contoso musi usunąć niepotrzebne procedur i usprawniać procesy dla deweloperów i użytkowników. Potrzeby biznesowe IT szybkie i nie odpadów czasu lub pieniądze, w związku z tym dostarczania szybciej na wymagania klientów.
- **Zwiększenie elastyczności**: Contoso IT trzeba zwiększyć szybkość reakcji na potrzeby biznesowe. Musi być w stanie szybciej niż zmiany w witrynie marketplace, aby umożliwić sukces w globalnej gospodarki reagować. Nie może pobrać w taki sposób, lub stają się blocker biznesowych.
- **Skala**: W miarę rozwoju firmy pomyślnie, zespół IT firmy Contoso, musisz podać systemów, które można rozwijać w tym samym tempie.
- **Poprawa modeli kosztów**: Firma Contoso chce zmniejszyć wymogi kapitałowe budżetu IT.  Firma Contoso chce korzystać z możliwości chmury do skalowania i zmniejszyć zapotrzebowanie na drogiego sprzętu.
- **Niższe koszty licencjonowania**: Firma Contoso chce, aby zminimalizować koszty chmury.


## <a name="migration-goals"></a>Cele migracji

Zespół chmury firmy Contoso ma przypięte dół cele tej migracji. Te cele zostały użyte w celu ustalenia najlepszej metody migracji.

**Wymagania** | **Szczegóły**
--- | --- 
**Szybkie przenoszenie na platformę Azure** | Firma Contoso chce, aby rozpocząć przenoszenie maszyn wirtualnych i aplikacji na platformie Azure tak szybko, jak to możliwe.
**Kompiluj pełny spis** | Firma Contoso chce pełny spis wszystkich aplikacji, baz danych i maszyn wirtualnych w organizacji.
**Ocena i klasyfikowanie aplikacji** | Firma Contoso chce, aby w pełni korzystać z chmury. Domyślnie Contoso przyjęto założenie, że wszystkie usługi będzie działać jako usługi PaaS. IaaS będzie używany, której PaaS nie jest odpowiednie. 
**Szkolenie i przejście do metodyki DevOps** | Firma Contoso chce przenieść do modelu metodyki DevOps. Contoso przeszkolenie platformy Azure i infrastrukturą DevOps i reorganizacja zespołów zgodnie z potrzebami. 


Po przypięciu dół celami i wymaganiami, Contoso przeglądy przestrzeni IT przedsiębiorstwa i identyfikuje proces migracji.

## <a name="current-deployment"></a>Bieżące wdrożenie

Po planowaniu i konfigurowaniu [infrastruktury platformy Azure](contoso-migration-infrastructure.md) i Wypróbowując różne kombinacje migracji zgodnie z opisem w powyższej tabeli weryfikacji koncepcji (POC), Contoso jest gotowy do rozpoczęcia pełnej migracji na platformę Azure na dużą skalę. Oto, co firma Contoso chce, aby przeprowadzić migrację.

**Element** | **Wolumin** | **Szczegóły**
--- | --- | ---
**Obciążenia** | Ponad 3000 oferowanych produktów aplikacji | Aplikacje są uruchamiane na maszynach wirtualnych.<br/><br/>  Aplikacje to Windows, oparte na SQL i OSS LAMP.
**Bazy danych** | Około 8,500 | Bazy danych obejmują program SQL Server, MySQL, PostgreSQL.
**Maszyny wirtualne** | Ponad 10 000 operacji | Maszyny wirtualne uruchamiane na hostach VMware i zarządzane przez serwery vCenter.


## <a name="migration-process"></a>Proces migracji

Teraz, gdy Contoso zostały przypięte czynniki biznesowe i celów migracji, określa czterowtykowe podejście do procesu migracji:

- **Faza 1 — ocena**: Odnajdywanie zasobów bieżącego i ustalić, czy są one odpowiednie dla migracji na platformę Azure.
- **Faza 2 — migracja**: Przenieś zasoby na platformie Azure. Jak są one przenoszone aplikacji i obiektów na platformie Azure będzie zależała od aplikacji, a co chcą osiągnąć.
- **Faza 3 — Optymalizacja**: Po przeniesieniu zasobów na platformie Azure, firma Contoso potrzebuje poprawić i uprościć ich wydajność i maksymalną wydajność.
- **Faza 4 — Zabezpieczanie i zarządzanie**: Wszystko w miejscu Contoso używa teraz zabezpieczeniom i operacjom zarządzania zasobami platformy Azure i usługi do zarządzania, zabezpieczania i Monitoruj swoje aplikacje w chmurze na platformie Azure.


Tych faz nie szeregowego w całej organizacji. Każda część projektu migracji firmy Contoso będzie na różnych etapach procesu oceny i migracji. Optymalizacja, zabezpieczeń i zarządzania będą trwającą wraz z upływem czasu.


## <a name="phase-1-assess"></a>Faza 1: Ocena

Contoso dotyczącego proces od wykrywania i oceny aplikacji w środowisku lokalnym, danych i infrastruktury. Oto, co będzie wykonywać firma Contoso:

- Firmy Contoso musi odnajdywanie aplikacji, mapy zależności między aplikacjami, a następnie wybierz kolejność migracji i priorytetu.
- Jak Contoso ocenia, jego kompilacja się pełny spis aplikacji i zasobów. Wraz z nowego magazynu Contoso użycia i aktualizowanie istniejącej konfiguracji zarządzania bazy danych (CMDB) i katalogu usług.
    - CMDB przechowuje techniczne konfiguracje dla aplikacji firmy Contoso.
    - Katalog usług dokumenty, informacje o operacjach aplikacji, w tym skojarzone partnerzy i umowy dotyczące poziomu usług (SLA)

### <a name="discover-apps"></a>Odnajdywanie aplikacji

Contoso uruchamia tysięcy aplikacji w całym szeregu serwerów. Oprócz CMDB i katalogu usług firma Contoso potrzebuje narzędzia odnajdywania i oceny. 

- Narzędzia należy podać mechanizm, który może przekazywać dane oceny do procesu migracji.
- Narzędzia do oceny należy podać dane, które pomagają, tworząc inteligentne spis zasobów fizycznych i wirtualnych firmy Contoso. Dane powinny zawierać informacje o profilu i metryki wydajności.
- Po zakończeniu odnajdywania firmy Contoso powinien mieć kompletny spis zasobów, a metadane skojarzone z nimi. Tego magazynu będzie służyć do zdefiniowania planu migracji.

### <a name="identify-classifications"></a>Identyfikowanie klasyfikacji

Contoso identyfikuje niektóre typowe kategorie do klasyfikowania zasoby w magazynie. Te klasyfikacje są krytyczne dla działalności firmy Contoso decyzje dotyczące migracji. Lista klasyfikacji pomaga ustalenia priorytetów migracji oraz zidentyfikować złożonych zagadnień.

**Kategoria** | **Przypisaną wartością** | **Szczegóły**
--- | --- | ---
Grupie biznesowej | Lista nazw grup biznesowych | Grupę, do której jest odpowiedzialny za element spisu?
Weryfikacja Koncepcji Release candidate | T/N | Aplikacja umożliwia jako Weryfikacja Koncepcji lub wczesnego nabywcy migracji do chmury?
Dług techniczny | Brak/niektóre/poważny | Jest element spisu uruchomiona lub przy użyciu produktu obsługa wyjścia, platformy i systemu operacyjnego
Implikacje zapory | T/N | Aplikacji komunikację z Internetem/poza ruchu?  Można je zintegrować z zaporą?
Problemy z zabezpieczeniami | T/N | Są znane problemy dotyczące zabezpieczeń przy użyciu aplikacji?  Aplikacja używa niezaszyfrowanych danych lub nieaktualne platform?


### <a name="discover-app-dependencies"></a>Odkryj zależności aplikacji

W ramach procesu oceny firma Contoso potrzebuje do identyfikacji, gdzie aplikacje są uruchomione i zorientować się, zależności i połączenia między serwerami aplikacji. Contoso mapowania środowiska w krokach.

1. Pierwszym krokiem Contoso odnajduje sposób mapowania serwerów i maszyn do poszczególnych aplikacji, lokalizacjami sieciowymi i grup.
2. Dzięki tym informacjom firmy Contoso jednoznacznie zidentyfikować aplikacje, które mają kilka zależności i dlatego są odpowiednie na potrzeby szybkiej migracji.
3. Contoso umożliwia mapowanie pomocne w identyfikacji bardziej złożonych zależności i komunikacji między serwerami aplikacji. Contoso można grupować tych serwerów logicznie do reprezentowania aplikacji i Planowanie strategii migracji na podstawie tych grup.


Za pomocą mapowania ukończone, Contoso może upewnij się, że wszystkie składniki aplikacji zostały zidentyfikowane i są rozliczane podczas tworzenia planu migracji. 

![Mapowanie zależności](./media/contoso-migration-scale/dependency-map.png)


### <a name="evaluate-apps"></a>Ocena aplikacji

Jako ostatni krok w procesie odnajdywania i oceny firma Contoso może służyć do oceny mapowanie i ocena wyników do zorientować się, jak przeprowadzić migrację każdej aplikacji w katalogu usług. 

Aby przechwycić tego procesu oceny, co zwiększa kilka dodatkowych klasyfikacje magazynu.

**Kategoria** | **Przypisaną wartością** | **Szczegóły**
--- | --- | ---
Grupie biznesowej | Lista nazw grup biznesowych | Grupę, do której jest odpowiedzialny za element spisu?
Weryfikacja Koncepcji Release candidate | T/N | Aplikacja umożliwia jako Weryfikacja Koncepcji lub wczesnego nabywcy migracji do chmury?
Dług techniczny | Brak/niektóre/poważny | Jest element spisu uruchomiona lub przy użyciu produktu obsługa wyjścia, platformy i systemu operacyjnego
Implikacje zapory | T/N | Aplikacji komunikację z Internetem/poza ruchu?  Można je zintegrować z zaporą?
Problemy z zabezpieczeniami | T/N | Są znane problemy dotyczące zabezpieczeń przy użyciu aplikacji?  Aplikacja używa niezaszyfrowanych danych lub nieaktualne platform?
Strategia migracji | Rehost/Zrefaktoryzuj/ustalenie/Rebuild | Jakiego rodzaju migracji jest wymagana dla aplikacji? Jak aplikacji będzie można wdrożyć na platformie Azure? [Dowiedz się więcej](contoso-migration-overview.md#migration-strategies).
Złożoność Technical Preview | 1-5 | Jak złożona jest migracji? Ta wartość powinna być zdefiniowana przez DevOps firmy Contoso i odpowiednich partnerów.
Poziom zagrożenia w procesie biznesowych | 1-5 | Jak ważne jest aplikacją firmy? Na przykład aplikacji małej grupy roboczej może zostać przypisana wynik, gdy aplikacji krytycznych, używane w całej organizacji, mogą zostać przypisani wynik do 5. Ten wynik będzie miało wpływ na poziom priorytetu migracji.
Priorytet migracji | 1/2/3 | Jakie migracji priorytet dla aplikacji?
Migracja o podwyższonym ryzyku  | 1-5 | Co to jest poziom ryzyka dla migracji aplikacji? Ta wartość powinna uzgodnione przez DevOps firmy Contoso i odpowiednich partnerów.




### <a name="figure-out-costs"></a>Ustalenie kosztów

Aby ustalić, koszty i potencjalne oszczędności migracji na platformę Azure, można użyć Contoso [Kalkulator całkowity koszt posiadania (TCO)](https://azure.microsoft.com/pricing/tco/calculator/) do obliczania i porównaj całkowity koszt posiadania dla platformy Azure do wdrożenia lokalnego porównywalny.

### <a name="identify-assessment-tools"></a>Identyfikowanie narzędzi do oceny

Contoso decyduje, które narzędzie do wykrywania, oceny i tworzenie spisu. Contoso identyfikuje różnych narzędzi platformy Azure i usług, skrypty i narzędzia do aplikacji natywnych i narzędziami partnerów. W szczególności Contoso jest zainteresowany jak usługa Azure Migrate może służyć do oceny w odpowiedniej skali.


#### <a name="azure-migrate"></a>Azure Migrate


Usługa Azure Migrate pomaga odnajdywać i oceniać maszyny wirtualne programu VMware w środowisku lokalnym, w ramach przygotowania do migracji na platformę Azure. Poniżej przedstawiono, jak usługa Azure Migrate działa:

1. Odnajdywanie: Odnajdywanie lokalnych maszyn wirtualnych z programu VMware.
    - Usługa Azure Migrate obsługuje odnajdywania z wielu serwerów programu vCenter (szeregowo) i mogą być uruchamiane operacje odnajdywania w oddzielnych projektów usługi Azure Migrate.
    - Usługa Azure Migrate odnajduje za pomocą środków na maszynę Wirtualną VMware z systemem Migrate Collector. Tego samego modułu zbierającego można odnalezienia maszyn wirtualnych na różnych vCenter Server i wysyłania danych do różnych projektów.
1. Ocena gotowości: Oceń, czy maszyny lokalne są odpowiednie na potrzeby działających na platformie Azure. Ocena zawiera:
    - Zalecenia dotyczące rozmiaru: Uzyskiwanie zaleceń dotyczących rozmiarów maszyn wirtualnych platformy Azure, w oparciu o historii wydajności lokalnych maszyn wirtualnych.
    - Szacowany miesięczny koszt: oblicz szacowane koszty uruchamiania maszyn lokalnych na platformie Azure.
2. Określenie zależności:  Wizualizacja zależności maszyn lokalnych, aby utworzyć grupy optymalnych maszyn na potrzeby oceny i migracji.


![Azure Migrate](./media/contoso-migration-scale/azure-migrate.png)


##### <a name="migrate-at-scale"></a>Migruj na dużą skalę

Firmy Contoso musi używać usługi Azure Migrate poprawnie zapewniają skalowania tej migracji. 

- Firma Contoso będzie wykonywać ocenę przez aplikację za pomocą usługi Azure Migrate. Daje to gwarancję, że usługa Azure Migrate zwraca aktualnych danych do witryny Azure portal.
- Administratorzy firmy Contoso, przeczytaj temat [wdrażania usługi Azure Migrate na dużą skalę](how-to-scale-assessment.md)
- Contoso — informacje o limity usługi Azure Migrate, podsumowane w poniższej tabeli.


**Akcja** | **Limit**
--- | ---
Tworzenie projektu usługi Azure Migrate | 1500 maszyn wirtualnych
Odnajdowanie | 1500 maszyn wirtualnych
Ocena | 1500 maszyn wirtualnych

Firma Contoso będzie używać usługi Azure Migrate w następujący sposób:

- W programie vCenter Contoso będzie organizowanie maszyn wirtualnych w folderach. Umożliwi to łatwe ich do fokus podczas ich uruchamiania oceny dla maszyn wirtualnych w określonym folderze.
- Usługa Azure Migrate Azure rozwiązania Service map do oceny zależności między maszynami. Wymaga to agenci przeznaczeni do zainstalowania na maszynach wirtualnych do oceny. 
    - Contoso użyje zautomatyzowanych skryptów do zainstalowania wymaganych agentów Windows lub Linux.
    - Za pomocą skryptu, Contoso wypychać instalacji do maszyn wirtualnych w folderze vCenter.


#### <a name="database-tools"></a>Narzędzia bazy danych

Oprócz usługi Azure Migrate Contoso koncentruje się na korzystanie z narzędzi specjalnie dla oceny bazy danych. Narzędzia takie jak [Asystenta migracji bazy danych](https://docs.microsoft.com/sql/dma/dma-overview?view=sql-server-2017) pomoże ocenić bazy danych SQL Server do migracji.

Narzędzia Data Migration Assistant (DMA) może pomóc firmy Contoso, aby ustalić, czy lokalnych baz danych są zgodne z rozwiązaniami do bazy danych platformy Azure, takich jak Azure SQL Database, SQL Server uruchomiony na maszynie Wirtualnej IaaS platformy Azure, a wystąpienia zarządzanego Azure SQL. 

Oprócz usługi DMS firma Contoso ma kilka skryptów, ich użyć do odnalezienia i dokumentowania baz danych programu SQL Server. Te znajdują się w repozytorium GitHub.

#### <a name="partner-tools"></a>Narzędzia partnerów

Istnieje kilka innych narzędzi partnera, które mogą ułatwić firmy Contoso w ocenie w lokalnym środowisku pod kątem migracji do platformy Azure. [Dowiedz się więcej](https://azure.microsoft.com/migration/partners/) o partnerzy migracji platformy Azure.  

## <a name="phase-2-migrate"></a>Faza 2: Migrate (Migracja)

Wraz z oceną ich pełną firma Contoso potrzebuje do identyfikowania narzędzia do przenoszenia ich aplikacji, danych i infrastruktury na platformie Azure. 




### <a name="migration-strategies"></a>Strategie migracji

Istnieją cztery Strategie migracji szerokie, firma Contoso może wziąć pod uwagę. 

**Strategia** | **Szczegóły** | **Użycie**
--- | --- | ---
**Rehost**  | Często określane jako migracja "metodą lift and shift", jest to opcja bez użycia kodu, w przypadku migrowania istniejących aplikacji na platformę Azure szybko.<br/><br/> Aplikacja jest migrowana jako — z zalet chmury bez zagrożeń lub kosztów związanych z zmian w kodzie. | Contoso może rehost mniej strategiczne aplikacje wymagające bez zmian w kodzie.
**Refaktoryzacja** |  Również określane jako "ponowne pakowanie", ta strategia wymaga kodu minimalną aplikację lub zmiany konfiguracji trzeba połączyć aplikację do usługi Azure PaaS i lepiej wykorzystać możliwości chmury. | Contoso refaktoryzować strategiczne aplikacje zachowują te same funkcje podstawowe, ale przenieść je do uruchomienia na platformie Azure, takich jak usługi Azure App Services.<br/><br/> Wymaga to zmian w kodzie minimalnej.<br/><br/> Z drugiej strony firma Contoso będzie musiał Obsługa platformy maszyny Wirtualnej, ponieważ to nie będzie zarządzany przez firmę Microsoft.
**Przekształcanie** | Ta strategia, modyfikuje lub rozszerza podstawowy do optymalizacji architektury aplikacji możliwości chmury i skalowanie kodu aplikacji.<br/><br/> Jego modernizes aplikację w architekturę odporne na błędy, o wysokim stopniu skalowalności, niezależnie do wdrożenia.<br/><br/> Usługi platformy Azure można przyspieszyć ten proces, skalować aplikacje bez obaw i zarządzanie aplikacjami z łatwością.
**Ponowna kompilacja** | Ta strategia ponownie kompiluje aplikację od podstaw przy użyciu technologii natywnych dla chmury.<br/><br/> Platforma Azure jako usługa (PaaS) zapewnia kompletne środowisko programowania i wdrażania w chmurze. Eliminuje niektóre kosztownego i skomplikowanego licencji na oprogramowanie i eliminuje potrzebę podstawowej infrastruktury aplikacji, oprogramowanie pośredniczące i innych zasobów. | Contoso można napisać ponownie aplikacji o krytycznym znaczeniu od podstaw, aby móc korzystać z technologii chmury, takich jak komputer bez użycia serwera lub mikrousług.<br/><br/> Firma Contoso będzie zarządzać aplikacji i usług, który rozwija, a platforma Azure zarządza się całą resztą.


Dane muszą być uwzględnione, szczególnie w przypadku ilości baz danych, które firma Contoso ma. Podejście domyślna firmy Contoso jest umożliwiają pełne wykorzystywanie zalet chmury funkcji przy użyciu usług PaaS, takich jak Azure SQL Database. Dzięki przeniesieniu do usługi PaaS dla baz danych, Contoso będzie miał tylko do przechowywania danych, pozostawiając podstawowej platformy firmy Microsoft.

### <a name="evaluate-migration-tools"></a>Oceń narzędzia migracji

Firma Contoso jest używany przede wszystkim kilka usług platformy Azure i narzędzia do migracji:

- [Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/site-recovery-overview): Organizuje odzyskiwanie po awarii i umożliwia migrowanie lokalnych maszyn wirtualnych na platformie Azure.
- [Azure Database Migration Service](https://docs.microsoft.com/azure/dms/dms-overview): Umożliwia migrowanie lokalnych baz danych, takich jak SQL Server, MySQL i Oracle na platformie Azure.


#### <a name="azure-site-recovery"></a>Azure Site Recovery

Usługa Azure Site Recovery jest podstawowej usługi platformy Azure, przez organizowanie odzyskiwania po awarii i migrację z w obrębie platformy Azure i z lokacji lokalnej do platformy Azure.

1. Usługa Site Recovery udostępnia i organizuje replikację z witryn sieci lokalnych na platformę Azure.
2. Podczas replikacji jest skonfigurowany i uruchomiony, że lokalne maszyny można przełączyć na platformie Azure, Kończenie migracji.

Contoso już [ukończyć weryfikacji Koncepcji](contoso-migration-rehost-vm.md) aby zobaczyć, jak Usługa Site Recovery może pomóc im przeprowadzić migrację do chmury.

##### <a name="using-site-recovery-at-scale"></a>Za pomocą Site Recovery w odpowiedniej skali

Contoso plany dotyczące uruchamiania wielu migracji lift-and-shift. Aby upewnić się, że to działa, Usługa Site Recovery będzie można replikowanie partie około 100 maszyn wirtualnych w danym momencie. Aby ustalić, jak będzie to możliwe, firma Contoso potrzebuje planowania pojemności dla proponowanych migracji Usługa Site Recovery.

- Firma Contoso potrzebuje do zbierania informacji o ich ilościach ruchu. W szczególności:
    - Firma Contoso potrzebuje określić stopień zmian chce, aby replikować maszyny wirtualne.
    - Firmy Contoso musi uwzględniać łączność sieciową z lokacji lokalnej do platformy Azure.
- W odpowiedzi na pojemności i wymagań woluminu Contoso należy przydzielić wystarczającą przepustowość, zgodnie z częstotliwością dziennych zmian danych dla maszyn wirtualnych wymagana, aby spełnić cel punktu odzyskiwania (RPO).
- Ponadto muszą oni ustalić, ile serwerów są potrzebne do uruchomienia, które są potrzebne do wdrożenia składników usługi Site Recovery.

###### <a name="gather-on-premises-information"></a>Zbieranie informacji w środowisku lokalnym
Można użyć contoso [planista wdrażania usługi Site Recovery](https://docs.microsoft.com/azure/site-recovery/site-recovery-deployment-planner) narzędzia, aby wykonać te kroki:

- Contoso służy narzędzie do zdalnie profilować maszyny wirtualne bez wpływu na środowisko produkcyjne. Ułatwia to wykrywanie przepustowości i magazynu wymagania dotyczące replikacji i trybu failover.
- Narzędzie można uruchomić bez instalowania dowolnych Site Recovery składników lokalnych firmy Contoso.
- Narzędzie gromadzi informacje o zgodnych i niezgodnych maszyn wirtualnych, dysków na maszynę Wirtualną, a współczynnik zmian danych na dysku. Identyfikuje również wymagania dotyczące przepustowości sieci i infrastruktury platformy Azure potrzebne do pomyślnej replikacji i trybu failover.
- Firma Contoso potrzebuje upewnić się, że na komputerach systemu Windows Server, które odpowiada minimalne wymagania dotyczące serwera konfiguracji usługi Site Recovery, następnie uruchom narzędzie planista. Serwer konfiguracji jest maszyną Site Recovery, która jest potrzebne w celu replikowania maszyn wirtualnych VMware w środowisku lokalnym.


###### <a name="identify-site-recovery-requirements"></a>Określenie wymagań związanych z Site Recovery

Oprócz replikowane maszyny wirtualne Usługa Site Recovery wymaga wiele składników do migracji z programu VMware.

**Składnik** | **Szczegóły**
--- | ---
**Serwer konfiguracji** | Zazwyczaj maszyny Wirtualnej VMware konfigurowane przy użyciu szablonu OVF.<br/><br/> Składnik serwera konfiguracji służy do koordynowania komunikacji między środowiskiem lokalnym i platformą Azure oraz do zarządzania replikacją danych.
**Serwer przetwarzania** | Domyślnie instalowany na serwerze konfiguracji.<br/><br/> Składnik serwera przetwarzania odbiera dane replikacji; optymalizuje je przy użyciu pamięci podręcznej, kompresji i szyfrowania; i wysyła je do usługi Azure storage.<br/><br/> Serwer przetwarzania instaluje także usługę mobilności usługi Azure Site Recovery na maszynach wirtualnych, którą chcesz replikować, i przeprowadza automatyczne odnajdywanie maszyn lokalnych.<br/><br/> Dodatkowe, potrzeba wdrożenia skalowanego autonomicznych serwerów przetwarzania do obsługi dużych ilości ruchu związanego z replikacją.
**Mobility Service** | Agent usługi mobilności jest instalowany na każdej maszynie Wirtualnej VMware, zostaną zmigrowane z usługą Site Recovery.  

Firma Contoso potrzebuje ustalenie sposobu wdrażania tych składników, w oparciu o zagadnienia dotyczące wydajności.

**Składnik** | **Wymagania dotyczące pojemności**
--- | ---
**Maksymalna dziennych zmian** | Serwer pojedynczego procesu może obsługiwać dzienny zmiany szybkości do 2 TB. Ponieważ maszyny Wirtualnej można używać tylko jednego serwera przetwarzania, maksymalny Dzienny współczynnik zmian danych obsługiwanym zreplikowanej maszyny Wirtualnej jest 2 TB.
**Maksymalna przepływność** | Konto magazynu platformy Azure w warstwie standardowa może obsługiwać maksymalnie 20 000 żądań na sekundę i operacji wejścia/wyjścia na sekundę (IOPS) w replikacji maszyn wirtualnych powinna być w ramach tego limitu. Na przykład jeśli maszyna wirtualna ma dyski 5, a każdy dysk generuje 120 na SEKUNDĘ (8K rozmiar) na maszynie Wirtualnej, następnie będzie w obrębie platformy Azure limit operacji We/Wy dysku 500.<br/><br/> Pamiętaj, że liczba kont magazynu, potrzebne jest taki sam, na maszynie źródłowej łączna liczba operacji We/Wy, podzielona przez 20 000. Replikowanej maszyny mogą należeć tylko do jednego konta magazynu na platformie Azure.
**Serwer konfiguracji** | Na podstawie szacunku firmy Contoso replikacji 100 = 200 maszyn wirtualnych razem i [określania rozmiaru wymagania dotyczące serwera konfiguracji](../site-recovery/site-recovery-plan-capacity-vmware.md#size-recommendations-for-the-configuration-server-and-inbuilt-process-server), Contoso szacowaną potrzeb maszyny serwera konfiguracji w następujący sposób:<br/><br/> Procesor CPU: 16 procesorów wirtualnych Vcpu (2 sockets * 8 rdzeni @ 2,5 GHz)<br/><br/> Pamięć: 32 GB<br/><br/> Dysk pamięci podręcznej: 1 TB<br/><br/> Współczynnik zmian danych: 1 TB do 2 TB.<br/><br/> Oprócz wymagania dotyczące rozmiaru Contoso należy upewnij się, że serwer konfiguracji znajduje się optymalnie, w tej samej sieci i segment sieci LAN jako maszyny wirtualne, które będą migrowane.
**Serwer przetwarzania** | Contoso wdroży autonomiczny serwer dedykowanego procesu o możliwość replikowania maszyn wirtualnych 100 – 200:<br/><br/> Procesor CPU: 16 procesorów wirtualnych Vcpu (2 sockets * 8 rdzeni @ 2,5 GHz)<br/><br/> Pamięć: 32 GB<br/><br/> Dysk pamięci podręcznej: 1 TB<br/><br/> Współczynnik zmian danych: 1 TB do 2 TB.<br/><br/> Serwer przetwarzania będzie ciężko i jako takie powinien znajdować się na hoście ESXi, która może obsłużyć We/Wy dysku, ruch sieciowy i procesora CPU wymagane dla replikacji. Firma Contoso będzie wziąć pod uwagę dedykowanego hosta, w tym celu. 
**Sieć** | Firmy Contoso ma przeglądowi bieżącej infrastruktury sieci VPN typu lokacja lokacja i decyzję o implementacji usługi Azure ExpressRoute. Implementacja znaczenie krytyczne, ponieważ wówczas zmniejszyć czas oczekiwania i zwiększyć przepustowość do regionu wschodnie stany USA 2 Azure podstawowego firmy Contoso.<br/><br/> **Monitorowanie**: Firma Contoso będzie musiał uważnie monitorować danych napływających z serwera przetwarzania. Jeśli dane przeciążenia przepustowość sieci, firma Contoso będzie należy wziąć pod uwagę [ograniczanie przepustowości serwera przetwarzania](../site-recovery/site-recovery-plan-capacity-vmware.md#control-network-bandwidth).
**Magazyn platformy Azure** | W przypadku migracji Contoso musi zidentyfikować właściwego typu i liczbę kont usługi Azure storage.  Usługa Site Recovery replikuje dane maszyny Wirtualnej do usługi Azure storage.<br/><br/> Usługa Site Recovery można replikować na standardowy lub premium (SSD) konta usługi storage.<br/><br/> Aby zdecydować, magazynu, należy przejrzeć Contoso [limity przestrzeni dyskowej](../virtual-machines/windows/disks-types.md)i wziąć pod uwagę przewidywanego wzrostu i zwiększone użycie wraz z upływem czasu. Biorąc pod uwagę szybkość i priorytetów migracji, Contoso scrumowy zdecydował się korzystać z dysków SSD w warstwie premium<br/><br/>

Firma Contoso dokonała decyzję, aby korzystać z dysków zarządzanych dla wszystkich maszyn wirtualnych, które są wdrażane na platformie Azure.  Operacje We/Wy wymagane określi, jeśli dyski będzie standardowych dysków Twardych, SSD w warstwie standardowa lub Premium (SSD).<br/><br/>

#### <a name="data-migration-service"></a>Data Migration Service

Azure Database Migration Service (DMS) jest w pełni zarządzana usługa, która umożliwia bezproblemową migrację z wielu źródłowych baz danych na danych na platformie Azure, przy minimalnych przestojach.

- Usługa DMS łączy w sobie funkcje istniejących narzędzi i usług. Używa Data Migration Assistant (DMA), aby wygenerować raporty z oceny zawierające wykrywanie zalecenia dotyczące zgodności bazy danych i wszystkie wymagane modyfikacje.
- Usługa DMS używa procesu prostemu, przeprowadzanemu samodzielnie procesowi migracji, wraz z oceną inteligentnego, który ułatwia rozwiązywania potencjalnych problemów przed migracją.
- Usługa DMS można migrować na dużą skalę z wielu źródeł do docelowej bazy danych platformy Azure.
- Usługa DMS zapewnia obsługę programu SQL Server 2005 do programu SQL Server 2017.

Usługa DMS nie jest jedynym narzędziem migracji bazy danych firmy Microsoft. Pobierz [porównanie narzędzi i usług](https://blogs.msdn.microsoft.com/datamigration/2017/10/13/differentiating-microsofts-database-migration-tools-and-services/).

###### <a name="using-dms-at-scale"></a>Przy użyciu usługi DMS na dużą skalę

Firma Contoso będzie używać usługa DMS, podczas migracji z programu SQL Server.  

- Podczas aprowizowania usługi DMS, firma Contoso potrzebuje upewnić się, że ma użyłeś pojemników i ustawić w celu optymalizacji wydajności w przypadku migracji danych. Contoso wybierze opcję "krytyczne dla prowadzonej działalności warstwy z 4 rdzenie wirtualne", dzięki czemu usługa może korzystać z wielu procesorów wirtualnych dla przetwarzania równoległego i szybszy transfer danych.

    ![Skalowanie usługi DMS](./media/contoso-migration-scale/dms.png)

- Inny taktyką skalowania dla Contoso jest tymczasowo skalowania w górę usługi Azure SQL lub MySQL obiektu docelowego do jednostki SKU warstwy Premium podczas migracji danych. Pozwala to zmniejszyć bazy danych, ograniczanie przepustowości, która może mieć wpływ na działania transferu danych podczas korzystania z jednostki SKU niższego poziomu.



##### <a name="using-other-tools"></a>Przy użyciu innych narzędzi

Oprócz usługi DMS Contoso można użyć innych narzędzi i usług do identyfikowania informacji o maszynie Wirtualnej.

- Mają one Skrypty pomagające w migracji ręcznie. Są one dostępne w repozytorium GitHub.
- Szereg [partnera narzędzia](https://azure.microsoft.com/migration/partners/) może również służyć do migracji.


## <a name="phase-3-optimize"></a>Faza 3: Optymalizacja

Po Contoso przenosi zasoby na platformie Azure, muszą one usprawnić je, aby zwiększyć wydajność i zmaksymalizować zwrot z inwestycji za pomocą narzędzi do zarządzania kosztami. Biorąc pod uwagę, że Azure jest usługą płatności — od rzeczywistego użycia, ważne jest "contoso", aby zrozumieć, jak działają systemy i upewnij się, że masz wielkość.


### <a name="azure-cost-management"></a>Usługa Azure cost management firmy

Aby maksymalnie wykorzystać swoich inwestycji w chmurze, firma Contoso będzie korzystać bezpłatne narzędzie do usługi Azure Cost Management.

- To rozwiązanie licencjonowane oparta na platformie Cloudyn, podmiot zależny firmy Microsoft, umożliwia zarządzanie chmurą wydatków z możliwości przejrzystego i dokładnego firmy Contoso.  Zapewnia narzędzia umożliwiające monitorowanie, przydzielanie i trim kosztów chmury.
- Usługa Azure Cost Management zawiera raporty prostego pulpitu nawigacyjnego ułatwiające wykonywanie przewidywanych alokacji kosztów i obciążeń zwrotnych.
- Usługa Cost Management można zoptymalizować chmury wydatków przez identyfikowanie niedostatecznie używanych zasobów, które Contoso można następnie zarządzać oraz je dostosowywać.
- [Dowiedz się więcej](https://docs.microsoft.com/azure/cost-management/overview) o usłudze Azure Cost Management.

    
![Zarządzanie kosztami](./media/contoso-migration-scale/cost-management.png)  
    
 
### <a name="native-tools"></a>Native Tools

Firma Contoso będzie również korzystać ze skryptów do zlokalizowania nieużywane zasoby.

- Podczas migracji dużej są często pozostawionych fragmentów danych, takie jak wirtualne dyski twarde (VHD), które powoduje naliczenie opłaty, ale zapewnia żadnej wartości do firmy. Skrypty są dostępne w repozytorium GitHub.
- Firma Contoso będzie korzystać w pracy wykonanej przez firmę Microsoft przez dział IT i rozważ zaimplementowanie Toolkit optymalizacji zasobów platformy Azure (ARO).
- Contoso wdrożyć konto usługi Azure Automation za pomocą wstępnie skonfigurowanych elementów runbook i harmonogramów do swojej subskrypcji i zacznij oszczędzać pieniądze. Optymalizacja zasobów platformy Azure odbywa się automatycznie w ramach subskrypcji po harmonogramu jest włączone, czy utworzony, w tym optymalizacji w nowych zasobów.
- Zapewnia możliwości automatyzacji zdecentralizowanego, obniżyć koszty. Funkcje obejmują:
    - Odłóż automatycznie maszyn wirtualnych platformy Azure oparte na procesorze CPU niski.
    - Planowanie maszyn wirtualnych platformy Azure, aby stan czuwania i unsnooze.
    - Planowanie maszyn wirtualnych platformy Azure, Odłóż lub unsnooze w kolejności rosnącej i malejącej, za pomocą tagów platformy Azure.
    - Zbiorcze usuwanie zasobów grup na żądanie.
- Rozpoczynanie pracy z zestawem narzędzi ARO, w tym [repozytorium GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/azure-resource-optimization-toolkit).

### <a name="partner-tools"></a>Narzędzia partnerów

Partner narzędzi, takich jak [Hanu](https://hanu.com/insight/) i [Scalr]( https://www.scalr.com/cost-optimization/) nadającego się.


## <a name="phase-4-secure--manage"></a>Faza 4: Zabezpieczenia i zarządzanie nimi

Na tym etapie firma Contoso używa zasobów platformy Azure zabezpieczeniom i operacjom zarządzania do zarządzania, zabezpieczania i monitorować aplikacje w chmurze na platformie Azure. Wspomniane zasoby pomogą do uruchomienia bezpiecznego i dobrze zarządzanego środowiska podczas korzystania z produktów dostępnych w witrynie Azure portal. Contoso rozpoczyna się korzystać z tych usług podczas migracji i z obsługą hybrydowej platformy Azure jest kontynuowane przy użyciu wielu z nich spójne środowisko pracy w chmurze hybrydowej.


### <a name="security"></a>Bezpieczeństwo
Firma Contoso będzie zależeć usługi Azure Security Center, ujednolicone zarządzanie zabezpieczeniami i zaawansowaną ochronę przed zagrożeniami w obciążeniach chmury hybrydowej.

- Usługa Security Center zapewnia pełny wgląd w i kontrolę nad, bezpieczeństwo aplikacji w chmurze na platformie Azure.
- Contoso można szybko wykryć i podjąć działania w odpowiedzi na zagrożenia i zmniejszyć zagrożenie bezpieczeństwa, należy włączyć adaptacyjną ochronę przed zagrożeniami.

[Dowiedz się więcej](https://azure.microsoft.com/services/security-center/) temat Centrum zabezpieczeń. 


### <a name="monitoring"></a>Monitorowanie

Firma Contoso potrzebuje wgląd w kondycję i wydajność, nowo zmigrowaną aplikacji, infrastruktury i danych obecnie uruchomionych na platformie Azure. Firma Contoso będzie korzystać wbudowanych chmury platformy Azure, narzędzia, takich jak Azure Monitor, obszar roboczy usługi Log Analytics i usługi Application Insights do monitorowania.
 
- Za pomocą tych narzędzi Contoso można łatwo zbierać dane ze źródeł i Uzyskaj szczegółowe informacje. Na przykład Contoso można mierzyć dysku i pamięci przez procesor CPU dla maszyn wirtualnych, przeglądanie zależności aplikacji i sieci na wielu maszynach wirtualnych i śledzić wydajność aplikacji.
- Contoso użyje tych chmury, narzędzia do monitorowania możesz podejmować działania i integracji z rozwiązaniami usługi.
- [Dowiedz się więcej](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview) informacji na temat monitorowania platformy Azure.

### <a name="bcdr"></a>BCDR 

Contoso należy ciągłość działalności biznesowej i odzyskiwanie po awarii (BCDR) odzyskiwania strategii biznesowej dla swoich zasobów platformy Azure.

- Platforma Azure udostępnia [wbudowane funkcje BCDR](https://docs.microsoft.com/azure/architecture/resiliency/disaster-recovery-azure-applications) Aby zachować bezpieczeństwo danych i aplikacji/usługi, działanie. 
- Oprócz wbudowanych funkcji Contoso chce mieć pewność, że go można odzyskać sprawność po awarii, uniknąć firm kosztownych przerw w działaniu, cele zapewniania zgodności i chronić dane przed oprogramowaniem wymuszającym okup i błędami ludzkimi. Aby to zrobić
    - Jako ekonomiczne rozwiązanie do tworzenia kopii zapasowych zasobów platformy Azure zostanie wdrożona usługa Azure Backup firmy Contoso. Ponieważ jest ona wbudowanych, Contoso można skonfigurować kopie zapasowe w chmurze w kilku prostych krokach.
    - Firma Contoso będzie Konfigurowanie odzyskiwania po awarii maszyn wirtualnych platformy Azure przy użyciu usługi Azure Site Recovery do replikacji, trybu failover i powrotu po awarii między regionami platformy Azure, które określa.  Daje to gwarancję, że aplikacje działające na maszynach wirtualnych platformy Azure nadal będą dostępne w regionie pomocniczym oddalonym firmy Contoso Wybieranie w przypadku wystąpienia awarii w regionie podstawowym. [Dowiedz się więcej](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-quickstart).

 
## <a name="conclusion"></a>Podsumowanie

W tym artykule Contoso planowanej migracji na platformę Azure na dużą skalę.  Proces migracji są podzielone na czterech etapach. Z oceny i migracji za pomocą optymalizacji, zabezpieczeń i zarządzania po migracji zostało ukończenia. Przede wszystkim ważne jest, zaplanować projekt jako całego procesu migracji, ale migracja systemów w obrębie organizacji dzieląc zestawów do klasyfikacji i liczb, które mają znaczenie dla działalności. Ocena danych i stosując klasyfikacje, a projektu można podzielić na serię mniejszych migracji, które mogą być uruchamiane szybko i bezpiecznie.  Suma tych mniejszych migracje szybko jest przekształcany dużych pomyślną migrację na platformę Azure.
