---
title: Informacje o usłudze Azure Migrate | Microsoft Docs
description: Ten artykuł zawiera omówienie usługi Azure Migrate.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: overview
ms.date: 07/11/2019
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: 5409ed799454a6bb64077ee884065fc518556142
ms.sourcegitcommit: 920ad23613a9504212aac2bfbd24a7c3de15d549
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/15/2019
ms.locfileid: "68227808"
---
# <a name="about-azure-migrate"></a>Informacje o usłudze Azure Migrate

Ten artykuł zawiera krótkie omówienie Azure Migrate.

Azure Migrate ułatwia Migrowanie do platformy Azure. Azure Migrate udostępnia scentralizowany centrum do śledzenia odnajdywania, oceny i migracji lokalnej infrastruktury, aplikacji i danych na platformę Azure. Centrum udostępnia narzędzia platformy Azure do oceny i migracji, a także oferty niezależnych dostawców oprogramowania (ISV) innych firm. Oferuje ona następujące możliwości:

- **Ujednolicona platforma migracji**: Użyj jednego portalu do uruchamiania, uruchamiania i śledzenia podróży migracji do platformy Azure.
- **Zakres narzędzi**: Azure Migrate udostępnia narzędzia natywne i integruje się z innymi usługami platformy Azure, a także z narzędziami niezależnych dostawców oprogramowania. Wybierz odpowiednie narzędzia do oceny i migracji, zgodnie z wymaganiami organizacji. 
- **Azure Migrate oceny serwera**: Użyj narzędzia do oceny serwera do oceny lokalnych maszyn wirtualnych programu VMware i maszyn wirtualnych funkcji Hyper-V na potrzeby migracji na platformę Azure.
- **Migracja serwera Azure Migrate**: Użyj narzędzia migracji serwera do migrowania lokalnych maszyn wirtualnych programu VMware, maszyn wirtualnych funkcji Hyper-V, maszyn wirtualnych w chmurze i serwerów fizycznych na platformę Azure.
- **Ocena bazy danych Azure Migrate**: Ocenianie lokalnych baz danych na potrzeby migracji na platformę Azure.
- **Azure Migrate migracji bazy danych**: Migrowanie lokalnych baz danych na platformę Azure.


## <a name="azure-migrate-versions"></a>Wersje Azure Migrate

Istnieją dwie wersje usługi Azure Migrate:

- **Bieżąca wersja**: Ta wersja umożliwia tworzenie projektów Azure Migrate, odnajdywanie maszyn lokalnych i organizowanie ocen i migracji. [Dowiedz się więcej](whats-new.md) na temat Nowości w tej wersji.
- **Poprzednia wersja**: Jeśli używasz wcześniejszej wersji Azure Migrate (obsługiwana jest tylko Ocena lokalnych maszyn wirtualnych programu VMware), należy teraz używać bieżącej wersji. Nie można już tworzyć projektów Azure Migrate przy użyciu poprzedniej wersji, a firma Microsoft zaleca, aby nie wykonywać nowych odnajdywania. Aby uzyskać dostęp do istniejących projektów, w Azure Portal > **wszystkie usługi**, Wyszukaj **Azure Migrate**. Na pulpicie nawigacyjnym Azure Migrate istnieje powiadomienie i link umożliwiający dostęp do starych projektów Azure Migrate.

## <a name="isv-integration"></a>Integracja z niezależnego dostawcy oprogramowania

Oprócz natywnych narzędzi platformy Azure Azure Migrate integruje się z innymi ofertami niezależnych dostawców oprogramowania. Należy zidentyfikować potrzebne narzędzie i dodać je do projektu Azure Migrate. Możesz centralnie śledzić swoją drogę migracji z poziomu projektu Azure Migrate, w ramach narzędzi platformy Azure i niezależnego dostawcy oprogramowania.

**NIEZALEŻNEGO DOSTAWCY OPROGRAMOWANIA** | **Funkcja**
--- | ---
[Cloudamize](https://www.cloudamize.com/platform) | Ocena
[Urządzenie 42](https://docs.device42.com/) | Ocena
[Platforma turbonomic](https://learn.turbonomic.com/azure-migrate-portal-free-trial) | Ocena
[UnifyCloud](https://www.cloudatlasinc.com/cloudrecon/) | Ocena
[Technologia najmu](https://www.corenttech.com/AzureMigrate/) | Ocenianie i migrowanie
[Carbonite](https://www.carbonite.com/globalassets/files/datasheets/carb-migrate4azure-microsoft-ds.pdf) | Migrate (Migracja)

### <a name="selecting-an-isv-tool"></a>Wybieranie narzędzia niezależnego dostawcy oprogramowania

Po dodaniu narzędzia niezależnego dostawcy oprogramowania do projektu Azure Migrate Zacznij korzystać z narzędzia, uzyskując licencję lub korzystając z bezpłatnej wersji próbnej, zgodnie z zasadami dotyczącymi niezależnych dostawców oprogramowania. W każdym narzędziu jest dostępna opcja nawiązywania połączenia z Azure Migrate. Postępuj zgodnie z instrukcjami i dokumentacją narzędzia, aby połączyć narzędzie z Azure Migrate.

## <a name="azure-migrate-server-assessment"></a>Azure Migrate oceny serwera

Azure Migrate oceny serwera umożliwia odnajdywanie i ocenianie lokalnych maszyn wirtualnych VMware oraz maszyn wirtualnych funkcji Hyper-V do migracji na platformę Azure. Ułatwia on zidentyfikowanie następujących danych:

- **Gotowość platformy Azure:** Oceń, czy maszyny lokalne są gotowe do migracji na platformę Azure.
- **Wymiarowanie na platformie Azure:** Oszacuj rozmiar maszyn wirtualnych platformy Azure po migracji.
- **Oszacowanie kosztów platformy Azure:** Szacuje koszty uruchamiania serwerów lokalnych na platformie Azure.
- **Wizualizacja zależności:** Zidentyfikuj zależności między serwerami i najlepszy sposób przenoszenia serwerów zależnych na platformę Azure. 

Ocena serwera korzysta z uproszczonego urządzenia wdrażanego lokalnie i rejestrowania z oceną serwera.

- Urządzenie wykrywa maszyny lokalne, nawiązuje połączenie z oceną serwera i ciągle wysyła metadane i dane związane z wydajnością do Azure Migrate.
- Odnajdywanie jest bez agentów. Niczego nie trzeba instalować na odnalezionych maszynach wirtualnych.
- Po odnalezieniu maszyn należy zebrać je do grup, które zwykle składają się z maszyn wirtualnych, które mają być migrowane ze sobą.
- Utwórz ocenę dla grupy. Następnie można przeanalizować ocenę, aby ustalić strategię migracji.

## <a name="azure-migrate-server-migration"></a>Migracja serwera Azure Migrate

Migracja serwera Azure Migrate ułatwia Migrowanie lokalnych maszyn wirtualnych programu VMware, maszyn wirtualnych funkcji Hyper-V, serwerów fizycznych, innych maszyn wirtualnych i maszyn wirtualnych w chmurze publicznej na platformie Azure. Po dokonaniu oceny lub bez oceny można migrować maszyny. 

## <a name="azure-migrate-database-assessment"></a>Azure Migrate oceny bazy danych

Azure Migrate integruje się z usługą Data Migration Assistant (DMA) do oceny lokalnych baz danych SQL Server na potrzeby migracji do usługi Azure SQL DB, wystąpienia zarządzanego usługi Azure SQL lub maszyn wirtualnych platformy Azure z systemem SQL Server. DMA zawiera informacje o potencjalnych problemach z blokowaniem migracji. Identyfikuje Nieobsługiwane funkcje, a także nowe funkcje, z których można skorzystać po migracji, i pomaga identyfikować właściwą ścieżkę do migracji bazy danych. [Dowiedz się więcej](https://docs.microsoft.com/sql/dma/dma-overview?view=sql-server-2017).


## <a name="azure-migrate-database-migration"></a>Migracja bazy danych Azure Migrate

Azure Migrate integruje się z Azure Database Migration Service (DMS) w celu migrowania lokalnych baz danych na platformę Azure. Usługa DMS służy do migrowania lokalnych baz danych do maszyn wirtualnych platformy Azure z uruchomionymi usługami SQL, Azure SQL DB i Azure SQL. [Dowiedz się więcej](https://docs.microsoft.com/azure/dms/dms-overview).

## <a name="web-app-assessment-and-migration"></a>Ocena i migracja aplikacji sieci Web

Z poziomu Centrum Azure Migrate można oceniać i migrować lokalne aplikacje sieci Web na platformę Azure.

- **Oceń aplikacje internetowe w trybie online**: Użyj Asystent migracji Azure App Service do oceny lokalnych witryn sieci Web na potrzeby migracji do Azure App Service.
- **Migrowanie aplikacji sieci Web**: Migruj aplikacje sieci Web platformy .NET i PHP na platformę Azure przy użyciu Azure App Service Asystent migracji.

[Dowiedz się więcej.](https://appmigration.microsoft.com/)

## <a name="offline-data-migration"></a>Migracja danych w trybie offline

Aby przenieść duże ilości danych na platformę Azure, możesz użyć rodziny produktów urządzenie Data Box w trybie offline. [Dowiedz się więcej](https://docs.microsoft.com/azure/databox/)

## <a name="next-steps"></a>Kolejne kroki

- Wypróbuj nasze samouczki, aby ocenić [maszyny wirtualne VMware](tutorial-assess-vmware.md) i [maszyny wirtualne funkcji Hyper-V](tutorial-assess-hyper-v.md).
- [Uzyskaj więcej informacji](https://azure.microsoft.com/pricing/details/azure-migrate/) o cenach usługi Azure Migrate.
- [Przejrzyj często zadawane pytania](resources-faq.md) dotyczące usługi Azure Migrate.
