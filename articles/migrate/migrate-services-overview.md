---
title: Informacje o usłudze Azure Migrate
description: Dowiedz się więcej o usłudze Azure Migrate.
ms.topic: overview
ms.date: 12/29/2019
ms.custom: mvc
ms.openlocfilehash: c336b2f95a17896edcc7eaad2b2586c6859804f9
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/09/2020
ms.locfileid: "75772197"
---
# <a name="about-azure-migrate"></a>Informacje o usłudze Azure Migrate

Ten artykuł zawiera krótkie omówienie usługi Azure Migrate.

Użyj Azure Migrate, aby przeprowadzić migrację na platformę Azure. Azure Migrate zapewnia scentralizowany centrum do oceniania i migrowania lokalnej infrastruktury, aplikacji i danych do platformy Azure przy użyciu następujących funkcji:

- **Ujednolicona platforma migracji**: pojedynczy Portal do uruchamiania, uruchamiania i śledzenia podróży migracji do platformy Azure.
- **Zakres narzędzi**: szereg narzędzi do oceny i migracji. Centrum zawiera Azure Migrate: Ocena serwera i Azure Migrate: Migracja serwera. Integruje się z innymi usługami platformy Azure oraz z innymi narzędziami i ofertami niezależnych dostawców oprogramowania (ISV).
- **Ocena i migracja**: w centrum Azure Migrate można ocenić i zmigrować:
    - **Serwery**: ocenianie i Migrowanie serwerów lokalnych do maszyn wirtualnych platformy Azure.
    - **Bazy danych**: ocenianie i migrowanie lokalnych baz danych do usługi Azure SQL DB lub do wystąpienia zarządzanego Azure SQL.
    - **Aplikacje sieci Web**: ocenianie i migrowanie lokalnych aplikacji sieci web do Azure App Service przy użyciu asystenta Azure App Service.
    - **Pulpity wirtualne**: ocenianie i migrowanie lokalnej infrastruktury pulpitów wirtualnych (VDI) do pulpitu wirtualnego systemu Windows na platformie Azure.
    - **Dane**: szybkie i ekonomiczne Migrowanie dużych ilości danych na platformę Azure przy użyciu produktów Azure Data Box. 


## <a name="integrated-tools"></a>Zintegrowane narzędzia

Centrum Azure Migrate udostępnia następujące narzędzia.

**Narzędzie** | **Ocenianie/Migrowanie** | **Szczegóły**
--- | --- | ---
**Azure Migrate: Ocena serwera** | Ocenianie serwerów | Wykrywaj i oceniaj lokalne maszyny wirtualne programu VMware, maszyny wirtualne funkcji Hyper-V i serwery fizyczne, aby ustalić, czy są one gotowe do migracji na platformę Azure.
**Azure Migrate: Migracja serwera** | Migrowanie serwerów | Migruj maszyny wirtualne VMware, maszyny wirtualne funkcji Hyper-V, serwery fizyczne, a także inne maszyny wirtualne i maszyny wirtualne w chmurze publicznej na platformę Azure. 
**Asystent migracji bazy danych (DMA)** | Oceniaj lokalne bazy danych SQL Server, aby przeprowadzić migrację do usługi Azure SQL DB, wystąpienia zarządzanego Azure SQL lub maszyn wirtualnych platformy Azure z systemem SQL Server. | DMA zawiera informacje o potencjalnych problemach z blokowaniem migracji. Identyfikuje Nieobsługiwane funkcje, a także nowe funkcje, z których można skorzystać po migracji, i pomaga identyfikować właściwą ścieżkę do migracji bazy danych. [Dowiedz się więcej](https://docs.microsoft.com/sql/dma/dma-overview?view=sql-server-2017).
**Database Migration Service (DMS)** | Migrowanie lokalnych baz danych do maszyn wirtualnych platformy Azure z uruchomionymi usługami SQL, Azure SQL DB i Azure SQL. | [Dowiedz się więcej](https://docs.microsoft.com/azure/dms/dms-overview) o usłudze DMS.
**Przeniesienie** | Ocenianie serwerów | [Dowiedz się więcej](#movere) o obszarze przenoszenia.
**Asystent migracji aplikacji sieci Web** | Ocenianie i migrowanie lokalnych aplikacji sieci Web na platformę Azure. |  Użyj Asystent migracji Azure App Service do oceny lokalnych witryn sieci Web na potrzeby migracji do Azure App Service.<br/><br/> Migruj aplikacje sieci Web platformy .NET i PHP na platformę Azure przy użyciu Azure App Service Asystent migracji. [Dowiedz się więcej](https://appmigration.microsoft.com/) o Asystencie.
**Azure Data Box** | Migracja danych w trybie offline. | Użyj Azure Data Box produktów, aby przenieść duże ilości danych w tryb offline na platformę Azure. [Dowiedz się więcej](https://docs.microsoft.com/azure/databox/).

## <a name="isv-integration"></a>Integracja z niezależnego dostawcy oprogramowania

Azure Migrate integruje się z innymi ofertami niezależnych dostawców oprogramowania. 

**NIEZALEŻNEGO dostawcy oprogramowania** | **Funkcja**
--- | ---
[Carbonite](https://www.carbonite.com/globalassets/files/datasheets/carb-migrate4azure-microsoft-ds.pdf) | Migrowanie serwerów
[Cloudamize](https://www.cloudamize.com/platform) | Ocenianie serwerów
[Technologia najmu](https://www.corenttech.com/AzureMigrate/) | Ocenianie i Migrowanie serwerów
[Urządzenie 42](https://docs.device42.com/) | Ocenianie serwerów
[Lakeside](https://go.microsoft.com/fwlink/?linkid=2104908) | Ocena infrastruktury VDI
[Stojaki](https://go.microsoft.com/fwlink/?linkid=2102735) | Migrowanie serwerów
[Platforma turbonomic](https://learn.turbonomic.com/azure-migrate-portal-free-trial) | Ocenianie serwerów
[UnifyCloud](https://www.cloudatlasinc.com/cloudrecon/) | Ocenianie serwerów i baz danych


## <a name="azure-migrate-server-assessment-tool"></a>Azure Migrate: Narzędzie do oceny serwera

Azure Migrate: Narzędzie do oceny serwera wykrywa i ocenia lokalne maszyny wirtualne programu VMware, maszyny wirtualne funkcji Hyper-V i serwery fizyczne do migracji na platformę Azure. Ułatwia on zidentyfikowanie następujących danych:

- **Gotowość platformy Azure:** Oceń, czy maszyny lokalne są gotowe do migracji na platformę Azure.
- **Wymiarowanie na platformie Azure:** Szacowany rozmiar maszyn wirtualnych platformy Azure po migracji.
- **Oszacowanie kosztów platformy Azure:** Szacowane koszty uruchamiania serwerów lokalnych na platformie Azure.
- **Wizualizacja zależności:** Jeśli używasz oceny serwera z wizualizacją zależności, możesz skutecznie identyfikować zależności między serwerami i optymalne sposoby przenoszenia serwerów zależnych na platformę Azure.

Ocena serwera korzysta z uproszczonego urządzenia wdrażanego lokalnie i rejestrowania z oceną serwera.

- Urządzenie działa na serwerze fizycznym lub maszynie wirtualnej i jest łatwo instalowane przy użyciu pobranego szablonu.
- Urządzenie wykrywa maszyny lokalne i ciągle wysyła metadane maszyn i dane wydajności do Azure Migrate.
- Odnajdowanie urządzeń jest bez agentów. Niczego nie trzeba instalować na odnalezionych maszynach.
- Po przeprowadzeniu odnajdywania można zbierać odnalezione maszyny w grupach i oceniać grupy do migracji.


## <a name="azure-migrate-server-migration-tool"></a>Azure Migrate: Narzędzie do migracji serwera

Azure Migrate: Narzędzie do migracji serwera ułatwia Migrowanie lokalnych maszyn wirtualnych programu VMware, maszyn wirtualnych funkcji Hyper-V, serwerów fizycznych, innych maszyn wirtualnych i maszyn wirtualnych w chmurze publicznej na platformie Azure. Po dokonaniu oceny lub migracji maszyn można migrować je bez oceny.


## <a name="select-a-tool"></a>Wybór narzędzia

W centrum Azure Migrate wybierz narzędzie, którego chcesz użyć do oceny, i Dodaj je do projektu Azure Migrate. W przypadku dodania narzędzia niezależnego dostawcy oprogramowania lub przenoszenia:

- Zacznij od uzyskania licencji lub zarejestrowania się w celu skorzystania z bezpłatnej wersji próbnej, zgodnie z instrukcjami dotyczącymi narzędzi. Licencjonowanie narzędzi jest określane przez niezależnego dostawcę oprogramowania/narzędzia. 
- W każdym narzędziu jest dostępna opcja nawiązywania połączenia z Azure Migrate. Postępuj zgodnie z instrukcjami, aby nawiązać połączenie.
- Śledź swoją drogę migracji z poziomu projektu Azure Migrate, we wszystkich narzędziach.


## <a name="movere"></a>Movere

Środowisko przenoszenia jest platformą SaaS, która zwiększa analizę biznesową przez dokładne prezentowanie całego środowiska IT w jednym dniu. Gdy organizacje rosną, zmieniają i optymalizują cyfrowo, rozwiązanie zapewnia przedsiębiorstwom pewność, że muszą mieć wgląd i kontrolę nad swoimi środowiskami niezależnie od platformy, aplikacji lub lokalizacji geograficznej. Produkt przenoszenia został [uzyskany](https://azure.microsoft.com/blog/microsoft-acquires-movere-to-help-customers-unlock-cloud-innovation-with-seamless-migration-tools/) przez firmę Microsoft i nie jest już sprzedawany jako oferta samodzielna.  Program do przenoszenia jest dostępny w ramach oceny rozwiązań firmy Microsoft i programów ekonomii chmur. [Dowiedz się więcej](https://www.movere.io) o obszarze przenoszenia. Jeśli masz pytania, prześlij je do: movereq@microsoft.com lub skontaktuj się z przedstawicielem firmy Microsoft.

Zachęcamy również do Azure Migrate, naszej wbudowanej usługi migracji. Azure Migrate udostępnia centralne centrum upraszczające migrację do chmury. Funkcja centrum oferuje kompleksową obsługę różnych obciążeń, w tym serwerów fizycznych i wirtualnych, baz danych i aplikacji. Kompleksowa widoczność ułatwia śledzenie postępu w zakresie odnajdywania, oceny i migracji. W przypadku narzędzi platformy Azure i partnera niezależnego dostawcy oprogramowania Azure Migrate również zawiera szeroką gamę funkcji, w tym odnajdywanie serwerów wirtualnych i fizycznych, oparte na wydajności właściwe ustalanie rozmiarów, planowanie kosztów, oceny oparte na imporcie i aplikacja bez wykorzystania agentów analiza zależności. Jeśli szukasz pomocy eksperta, firma Microsoft ma wykwalifikowany [dostawca usług zarządzanych przez ekspertów platformy Azure](https://azure.microsoft.com/partners) , który poprowadzi Cię przez proces podróży. Zapoznaj się z [witryną sieci web Azure Migrate](https://azure.microsoft.com/services/azure-migrate/). 
 

## <a name="azure-migrate-versions"></a>Wersje Azure Migrate

Istnieją dwie wersje usługi Azure Migrate:

- **Bieżąca wersja**: Użyj tej wersji do tworzenia projektów Azure Migrate, odnajdywania maszyn lokalnych i organizowania ocen i migracji. [Dowiedz się więcej](whats-new.md) na temat Nowości w tej wersji.
- **Poprzednia wersja**: Jeśli była używana Poprzednia wersja Azure Migrate (obsługiwana jest tylko Ocena lokalnych maszyn wirtualnych programu VMware), należy teraz używać bieżącej wersji. Nie można już tworzyć projektów Azure Migrate przy użyciu poprzedniej wersji, a firma Microsoft zaleca, aby nie wykonywać nowych odnajdywania. Aby uzyskać dostęp do istniejących projektów, w Azure Portal Wyszukaj i wybierz pozycję **Azure Migrate**. Na pulpicie nawigacyjnym **Azure Migrate** istnieje powiadomienie i link umożliwiający dostęp do starych projektów Azure Migrate.



## <a name="next-steps"></a>Następne kroki

- Wypróbuj nasze samouczki, aby ocenić [maszyny wirtualne VMware](tutorial-prepare-vmware.md), [maszyny wirtualne funkcji Hyper-V](tutorial-prepare-hyper-v.md)i [serwery fizyczne](tutorial-prepare-physical.md).
- [Przejrzyj często zadawane pytania](resources-faq.md) dotyczące usługi Azure Migrate.
