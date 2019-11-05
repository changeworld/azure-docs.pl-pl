---
title: Informacje o usłudze Azure Migrate | Microsoft Docs
description: Ten artykuł zawiera omówienie usługi Azure Migrate.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: overview
ms.date: 10/22/2019
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: def0832898170e0a278c403349eab1bd89050a57
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73498750"
---
# <a name="about-azure-migrate"></a>Informacje o usłudze Azure Migrate

Ten artykuł zawiera krótkie omówienie Azure Migrate.

Azure Migrate ułatwia Migrowanie ze swojego miejsca lokalnego na platformę Azure. Azure Migrate udostępnia scentralizowany centrum do śledzenia odnajdywania, oceny i migracji lokalnej infrastruktury, aplikacji i danych na platformę Azure. Centrum udostępnia narzędzia i usługi platformy Azure do oceny i migracji, a także oferty niezależnych dostawców oprogramowania (ISV) innych firm. Azure Migrate zapewnia:

- **Ujednolicona platforma migracji**: pojedynczy Portal do uruchamiania, uruchamiania i śledzenia podróży migracji do platformy Azure.
- **Zakres narzędzi**: narzędzia natywne i integracja z innymi usługami platformy Azure, a także z narzędziami niezależnych dostawców oprogramowania. Wybierz odpowiednie narzędzia do oceny i migracji, zgodnie z wymaganiami organizacji.
- **Obciążenia**: Azure Migrate oferuje narzędzia do oceny i migracji dla:
    - **Serwery**: Użyj narzędzi firmy Microsoft lub niezależnych dostawców oprogramowania do oceny i migracji serwerów do maszyn wirtualnych platformy Azure.
    - **Bazy danych**: narzędzia firmy Microsoft i niezależnego dostawcy oprogramowania do oceny i migracji lokalnych baz danych do usługi Azure SQL DB lub wystąpienia zarządzanego Azure SQL.
    - **Aplikacje sieci Web**: Użyj asystenta Azure App Service, aby ocenić i zmigrować lokalne aplikacje sieci web do Azure App Service.
    - **Pulpity wirtualne**: Użyj narzędzi ISV do oceny i migracji lokalnej infrastruktury pulpitów wirtualnych (VDI) do pulpitu wirtualnego systemu Windows na platformie Azure.
    - **Dane**: korzystaj z Azure Data Box rodziny produktów, aby szybko i ekonomicznie migrować dane na platformę Azure.

## <a name="azure-migrate-versions"></a>Wersje Azure Migrate

Obecnie istnieją dwie wersje usługi Azure Migrate:

- **Bieżąca wersja**: Użyj tej wersji do tworzenia projektów Azure Migrate, odnajdywania maszyn lokalnych i organizowania ocen i migracji. [Dowiedz się więcej](whats-new.md) na temat Nowości w tej wersji.
- **Poprzednia wersja**: Jeśli była używana Poprzednia wersja Azure Migrate (obsługiwana jest tylko Ocena lokalnych maszyn wirtualnych programu VMware), należy teraz używać bieżącej wersji. Nie można już tworzyć projektów Azure Migrate przy użyciu poprzedniej wersji, a firma Microsoft zaleca, aby nie wykonywać nowych odnajdywania. Aby uzyskać dostęp do istniejących projektów, w Azure Portal > **wszystkie usługi**, Wyszukaj **Azure Migrate**. Na pulpicie nawigacyjnym Azure Migrate istnieje powiadomienie i link umożliwiający dostęp do starych projektów Azure Migrate.

## <a name="isv-integration"></a>Integracja z niezależnego dostawcy oprogramowania

Oprócz natywnych narzędzi firmy Microsoft Azure Migrate integruje się z innymi ofertami niezależnych dostawców oprogramowania. 

**NIEZALEŻNEGO dostawcy oprogramowania** | **Funkcja**
--- | ---
[Cloudamize](https://www.cloudamize.com/platform) | Ocena
[Urządzenie 42](https://docs.device42.com/) | Ocena
[Platforma turbonomic](https://learn.turbonomic.com/azure-migrate-portal-free-trial) | Ocena
[UnifyCloud](https://www.cloudatlasinc.com/cloudrecon/) | Ocena
[Technologia najmu](https://www.corenttech.com/AzureMigrate/) | Ocenianie i migrowanie
[Carbonite](https://www.carbonite.com/globalassets/files/datasheets/carb-migrate4azure-microsoft-ds.pdf) | Migrate (Migracja)
[Lakeside](https://go.microsoft.com/fwlink/?linkid=2104908) | Ocena



### <a name="selecting-an-isv-tool"></a>Wybieranie narzędzia niezależnego dostawcy oprogramowania

Należy zidentyfikować potrzebne narzędzie i dodać je do projektu Azure Migrate.

- Po dodaniu narzędzia niezależnego dostawcy oprogramowania Zacznij od uzyskania licencji lub rejestracji w celu skorzystania z bezpłatnej wersji próbnej zgodnie z zasadami niezależnych dostawców oprogramowania. Licencjonowanie narzędzi niezależnych dostawców oprogramowania jest zgodne z modelem licencjonowania niezależnego dostawcy oprogramowania.
- W każdym narzędziu jest dostępna opcja nawiązywania połączenia z Azure Migrate. Postępuj zgodnie z instrukcjami i dokumentacją narzędzia, aby połączyć narzędzie z Azure Migrate. L
- Możesz centralnie śledzić swoją drogę migracji z poziomu projektu Azure Migrate, w ramach narzędzi platformy Azure i niezależnych dostawców oprogramowania.


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


## <a name="database-assessment"></a>Ocena bazy danych

Azure Migrate integruje się z usługą Microsoft Data Migration Assistant (DMA) do oceny lokalnych baz danych SQL Server na potrzeby migracji do usługi Azure SQL DB, wystąpienia zarządzanego usługi Azure SQL lub maszyn wirtualnych platformy Azure z systemem SQL Server. DMA zawiera informacje o potencjalnych problemach z blokowaniem migracji. Identyfikuje Nieobsługiwane funkcje, a także nowe funkcje, z których można skorzystać po migracji, i pomaga identyfikować właściwą ścieżkę do migracji bazy danych. [Dowiedz się więcej](https://docs.microsoft.com/sql/dma/dma-overview?view=sql-server-2017).

## <a name="database-migration"></a>Migracja bazy danych

Azure Migrate integruje się z Azure Database Migration Service (DMS) w celu migrowania lokalnych baz danych na platformę Azure. Usługa DMS służy do migrowania lokalnych baz danych do maszyn wirtualnych platformy Azure z uruchomionymi usługami SQL, Azure SQL DB i Azure SQL. [Dowiedz się więcej](https://docs.microsoft.com/azure/dms/dms-overview).

## <a name="web-app-migration"></a>Migracja aplikacji sieci Web

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
