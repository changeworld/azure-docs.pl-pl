---
title: Informacje o usłudze Azure Migrate | Microsoft Docs
description: Ten artykuł zawiera omówienie usługi Azure Migrate.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: overview
ms.date: 11/05/2019
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: 8b1e716e482c49ceba0c600aaba30fe276bdb74d
ms.sourcegitcommit: 827248fa609243839aac3ff01ff40200c8c46966
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/07/2019
ms.locfileid: "73748007"
---
# <a name="about-azure-migrate"></a>Informacje o usłudze Azure Migrate

Ten artykuł zawiera krótkie omówienie Azure Migrate.

Azure Migrate ułatwia Migrowanie przedsiębiorstwa z zasobów lokalnych na platformę Azure. Azure Migrate udostępnia scentralizowany centrum do śledzenia odnajdywania, oceny i migracji lokalnej infrastruktury, aplikacji i danych na platformę Azure.  Azure Migrate zapewnia:

- **Ujednolicona platforma migracji**: pojedynczy Portal do uruchamiania, uruchamiania i śledzenia podróży migracji do platformy Azure.
- **Zakres narzędzi**: centrum udostępnia ocenę i migrację narzędzi Azure Migrate Tools oraz integruje się z innymi usługami platformy Azure, a także innymi narzędziami i ofertami niezależnych dostawców oprogramowania (ISV).
- **Obciążenia**: Azure Migrate zapewnia ocenę i migrację dla:
    - **Serwery**: Użyj oceny serwera Azure Migrate, migracji serwera Azure Migrate i innych narzędzi, aby ocenić i przeprowadzić migrację serwerów do maszyn wirtualnych platformy Azure.
    - **Bazy danych**: narzędzia firmy Microsoft i niezależnego dostawcy oprogramowania do oceny i migracji lokalnych baz danych do usługi Azure SQL DB lub wystąpienia zarządzanego Azure SQL.
    - **Aplikacje sieci Web**: Użyj asystenta Azure App Service, aby ocenić i zmigrować lokalne aplikacje sieci web do Azure App Service.
    - **Pulpity wirtualne**: Użyj narzędzi ISV do oceny i migracji lokalnej infrastruktury pulpitów wirtualnych (VDI) do pulpitu wirtualnego systemu Windows na platformie Azure.
    - **Dane**: korzystaj z Azure Data Box rodziny produktów, aby szybko i ekonomicznie migrować duże ilości danych na platformę Azure.

## <a name="azure-migrate-versions"></a>Wersje Azure Migrate

Obecnie istnieją dwie wersje usługi Azure Migrate:

- **Bieżąca wersja**: Użyj tej wersji do tworzenia projektów Azure Migrate, odnajdywania maszyn lokalnych i organizowania ocen i migracji. [Dowiedz się więcej](whats-new.md) na temat Nowości w tej wersji.
- **Poprzednia wersja**: Jeśli była używana Poprzednia wersja Azure Migrate (obsługiwana jest tylko Ocena lokalnych maszyn wirtualnych programu VMware), należy teraz używać bieżącej wersji. Nie można już tworzyć projektów Azure Migrate przy użyciu poprzedniej wersji, a firma Microsoft zaleca, aby nie wykonywać nowych odnajdywania. Aby uzyskać dostęp do istniejących projektów, w Azure Portal > **wszystkie usługi**, Wyszukaj **Azure Migrate**. Na pulpicie nawigacyjnym Azure Migrate istnieje powiadomienie i link umożliwiający dostęp do starych projektów Azure Migrate.



## <a name="isv-integration"></a>Integracja z niezależnego dostawcy oprogramowania

Oprócz natywnych narzędzi platformy Azure Azure Migrate integruje się z innymi ofertami niezależnych dostawców oprogramowania. 

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

## <a name="azure-tool-integration"></a>Integracja narzędzi platformy Azure

Tabela zawiera podsumowanie innych narzędzi, które są zintegrowane w Azure Migrate.

**Narzędzie** | **Szczegóły**
--- | ---
Azure Migrate: Ocena serwera | Ocenianie serwerów
Azure Migrate: Migracja serwera | Migrowanie serwerów
Asystent migracji bazy danych (DMA) | Ocenianie baz danych
Database Migration Service (DMS) | Migrowanie baz danych
Movere | Ocenianie serwerów
Asystent migracji aplikacji sieci Web | Ocenianie i Migrowanie aplikacji sieci Web



### <a name="selecting-a-tool"></a>Wybieranie narzędzia

Zidentyfikuj potrzebne narzędzie i Dodaj je do projektu Azure Migrate.

- W przypadku dodawania narzędzia niezależnego dostawcy oprogramowania lub przenoszenia:
    - Zacznij od uzyskania licencji lub zarejestrowania się w celu skorzystania z bezpłatnej wersji próbnej zgodnie z zasadami dotyczącymi narzędzi. Licencjonowanie dla narzędzi jest zgodne z modelem niezależnego dostawcy oprogramowania lub narzędzia.
    - W każdym narzędziu jest dostępna opcja nawiązywania połączenia z Azure Migrate. Postępuj zgodnie z instrukcjami i dokumentacją narzędzia, aby połączyć narzędzie z Azure Migrate.
- Możesz centralnie śledzić swoją drogę migracji z poziomu projektu Azure Migrate, na platformie Azure i w innych narzędziach.



## <a name="azure-migrate-server-assessment-tool"></a>Azure Migrate narzędzia do oceny serwera

Azure Migrate: Narzędzie do oceny serwera wykrywa i ocenia lokalne maszyny wirtualne programu VMware, maszyny wirtualne funkcji Hyper-V i serwery fizyczne do migracji na platformę Azure. Ułatwia on zidentyfikowanie następujących danych:

- **Gotowość platformy Azure:** Oceń, czy maszyny lokalne są gotowe do migracji na platformę Azure.
- **Wymiarowanie na platformie Azure:** Szacowany rozmiar maszyn wirtualnych platformy Azure po migracji.
- **Oszacowanie kosztów platformy Azure:** Szacowane koszty uruchamiania serwerów lokalnych na platformie Azure.
- **Wizualizacja zależności:** Zależności między serwerami (jeśli jest włączona Wizualizacja zależności) i optymalne sposoby przenoszenia serwerów zależnych na platformę Azure.

Ocena serwera korzysta z uproszczonego urządzenia wdrażanego lokalnie i rejestrowania z oceną serwera.

- Urządzenie umożliwia odnalezienie maszyn lokalnych.
- Łączy się on z oceną serwera i ciągle wysyła metadane maszyn i dane wydajności do Azure Migrate.
- Odnajdowanie urządzeń jest bez agentów. Niczego nie trzeba instalować na odnalezionych maszynach.
- Po odnajdywaniu można zbierać odnalezione maszyny w grupach. Zwykle zbierasz maszyny, które chcesz migrować ze sobą.
- Utwórz ocenę dla grupy. Następnie należy przeanalizować ocenę, aby ustalić strategię migracji.

## <a name="azure-migrate-server-migration-tool"></a>Narzędzie migracji serwera usługi Azure Migrate

Azure Migrate: Narzędzie do migracji serwera ułatwia Migrowanie lokalnych maszyn wirtualnych programu VMware, maszyn wirtualnych funkcji Hyper-V, serwerów fizycznych, innych maszyn wirtualnych i maszyn wirtualnych w chmurze publicznej na platformie Azure. Po dokonaniu oceny lub bez oceny można migrować maszyny.


## <a name="database-migration-assistant"></a>Asystent migracji bazy danych

Azure Migrate integruje się z usługą Microsoft Data Migration Assistant (DMA) do oceny lokalnych baz danych SQL Server na potrzeby migracji do usługi Azure SQL DB, wystąpienia zarządzanego usługi Azure SQL lub maszyn wirtualnych platformy Azure z systemem SQL Server. DMA zawiera informacje o potencjalnych problemach z blokowaniem migracji. Identyfikuje Nieobsługiwane funkcje, a także nowe funkcje, z których można skorzystać po migracji, i pomaga identyfikować właściwą ścieżkę do migracji bazy danych. [Dowiedz się więcej](https://docs.microsoft.com/sql/dma/dma-overview?view=sql-server-2017).

## <a name="database-migration-service"></a>Usługa migracji bazy danych

Azure Migrate integruje się z Azure Database Migration Service (DMS) w celu migrowania lokalnych baz danych na platformę Azure. Usługa DMS służy do migrowania lokalnych baz danych do maszyn wirtualnych platformy Azure z uruchomionymi usługami SQL, Azure SQL DB i Azure SQL. [Dowiedz się więcej](https://docs.microsoft.com/azure/dms/dms-overview).

## <a name="movere"></a>Movere

 
Środowisko przenoszenia jest platformą SaaS, która zwiększa analizę biznesową przez dokładne prezentowanie całego środowiska IT w jednym dniu. Gdy organizacje rosną, zmieniają i optymalizują cyfrowo, rozwiązanie zapewnia przedsiębiorstwom pewność, że muszą mieć wgląd i kontrolę nad swoimi środowiskami niezależnie od platformy, aplikacji lub lokalizacji geograficznej. Produkt przenoszenia został [uzyskany](https://azure.microsoft.com/blog/microsoft-acquires-movere-to-help-customers-unlock-cloud-innovation-with-seamless-migration-tools/) przez firmę Microsoft i nie jest już sprzedawany jako oferta samodzielna.  Program do przenoszenia jest dostępny w ramach oceny rozwiązań firmy Microsoft i programów ekonomii chmur. [Dowiedz się więcej](https://www.movere.io) o obszarze przenoszenia. Jeśli masz pytania, prześlij je do: movereq@microsoft.com lub skontaktuj się z przedstawicielem firmy Microsoft.

Zachęcamy również do Azure Migrate, naszej wbudowanej usługi migracji. Azure Migrate udostępnia centralne centrum upraszczające migrację do chmury. Funkcja centrum oferuje kompleksową obsługę różnych obciążeń, w tym serwerów fizycznych i wirtualnych, baz danych i aplikacji. Kompleksowa widoczność ułatwia śledzenie postępu w zakresie odnajdywania, oceny i migracji. W przypadku narzędzi platformy Azure i partnera niezależnego dostawcy oprogramowania Azure Migrate również zawiera szeroką gamę funkcji, w tym odnajdywanie serwerów wirtualnych i fizycznych, oparte na wydajności właściwe ustalanie rozmiarów, planowanie kosztów, oceny oparte na imporcie i aplikacja bez wykorzystania agentów analiza zależności. Jeśli szukasz pomocy eksperta, firma Microsoft ma wykwalifikowany [dostawca usług zarządzanych przez ekspertów platformy Azure](https://azure.microsoft.com/partners) , który poprowadzi Cię przez proces podróży. Zapoznaj się z [witryną sieci web Azure Migrate](https://azure.microsoft.com/services/azure-migrate/). 
 

## <a name="web-app-migration-assistant"></a>Asystent migracji aplikacji sieci Web

Azure Migrate integruje się z Asystent migracji Azure App Service. Z poziomu Centrum Azure Migrate można oceniać i migrować lokalne aplikacje sieci Web na platformę Azure przy użyciu Asystenta w następujący sposób:

- **Ocenianie aplikacji sieci Web w trybie online**: użyj Azure App Service Asystent migracji do oceny lokalnych witryn sieci Web w celu przeprowadzenia migracji do Azure App Service.
- **Migrowanie aplikacji sieci Web**: Migruj aplikacje sieci Web platformy .NET i php na platformę Azure przy użyciu Asystent migracji Azure App Service.

[Dowiedz się więcej](https://appmigration.microsoft.com/) o Asystencie.



## <a name="offline-data-migration"></a>Migracja danych w trybie offline

Za pomocą produktów Azure Data Box można przenieść duże ilości danych w tryb offline na platformę Azure. [Dowiedz się więcej](https://docs.microsoft.com/azure/databox/)

## <a name="next-steps"></a>Następne kroki

- Wypróbuj nasze samouczki, aby ocenić [maszyny wirtualne VMware](tutorial-assess-vmware.md) i [maszyny wirtualne funkcji Hyper-V](tutorial-assess-hyper-v.md).
- [Uzyskaj więcej informacji](https://azure.microsoft.com/pricing/details/azure-migrate/) o cenach usługi Azure Migrate.
- [Przejrzyj często zadawane pytania](resources-faq.md) dotyczące usługi Azure Migrate.
