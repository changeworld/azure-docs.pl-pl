---
title: Informacje o usłudze Azure Migrate | Microsoft Docs
description: Ten artykuł zawiera omówienie usługi Azure Migrate.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: overview
ms.date: 07/11/2019
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: 083eaaa5be35d515a477ce6286f226b267569e1e
ms.sourcegitcommit: 64798b4f722623ea2bb53b374fb95e8d2b679318
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/11/2019
ms.locfileid: "67840362"
---
# <a name="about-azure-migrate"></a>Informacje o usłudze Azure Migrate

Ten artykuł zawiera krótkie omówienie usługi Azure Migrate.

Usługa Azure Migrate ułatwia Migrowanie na platformę Azure. Usługa Azure Migrate oferuje scentralizowane koncentratora do śledzenia wykrywania, oceny i migracji na infrastrukturę lokalną, aplikacje i dane na platformie Azure. Centrum zapewnia narzędzia platformy Azure do oceny i migracji, a także oferty Niezależnym dostawcą oprogramowania niezależnie od innych firm. Oferuje ona następujące możliwości:

- **Ujednolicone migracji platformy**: Użyj jednego portalu, aby uruchomić, uruchom i Śledź swoją podróż po migracji na platformę Azure.
- **Różne narzędzia**: Usługa Azure Migrate oferuje natywnych narzędzi i integruje się z innymi usługami Azure, a także za pomocą narzędzi niezależnego dostawcy oprogramowania. Wybierz odpowiednie narzędzia do oceny i migracji, zgodnie z wymaganiami organizacji. 
- **Usługa Azure Migrate oceny Server**: Użyj narzędzia oceny Server do oceny lokalnych maszyn wirtualnych VMware i maszyn wirtualnych funkcji Hyper-V, dla migracji na platformę Azure.
- **Usługa Azure Migrate migracji serwera**: Użyj narzędzia migracji serwera, aby przeprowadzić migrację lokalnych maszyn wirtualnych VMware, maszyny wirtualne funkcji Hyper-V, maszynach wirtualnych w chmurze i serwerów fizycznych na platformę Azure.
- **Usługa Azure Migrate oceny bazy danych**: Ocena lokalnych baz danych do migracji na platformę Azure.
- **Usługa Azure Migrate migracji bazy danych**: Migrowanie lokalnych baz danych na platformę Azure.


## <a name="azure-migrate-versions"></a>Usługa Azure Migrate wersji

Istnieją dwie wersje usługi Azure Migrate:

- **Bieżąca wersja**: Tej wersji można używać do tworzenia projektów usługi Azure Migrate, odnajdowanie maszyn lokalnych i organizowania, oceny i migracji. [Dowiedz się więcej](whats-new.md) o nowościach w tej wersji.
- **Poprzednia wersja**: Jeśli używano poprzedniej wersji usługi Azure Migrate (obsługiwał tylko ocena lokalnych maszyn wirtualnych programu VMware), teraz należy używać bieżącej wersji. Można już tworzyć projekty usługi Azure Migrate, przy użyciu poprzedniej wersji lub wykonywać nowych operacji odnajdywania. Jednak użytkownik może nadal uzyskiwać dostęp do istniejących projektów. Aby to zrobić, w witrynie Azure portal > **wszystkich usług**, wyszukaj **usługi Azure Migrate**. Na pulpicie nawigacyjnym usługi Azure Migrate jest powiadomienie i łącza, dostęp do starych projektów usługi Azure Migrate.

## <a name="isv-integration"></a>Integracja niezależnego dostawcy oprogramowania

Oprócz natywnych narzędzi platformy Azure usługa Azure Migrate integruje się z liczbą oferty niezależnych dostawców oprogramowania. Możesz zidentyfikować narzędzia potrzebne i dodaj go do projektu Azure Migrate. Podróż migracyjną z w projekcie usługi Azure Migrate można śledzić centralnie w narzędziach platformy Azure i niezależnych dostawców oprogramowania.

**ISV** | **Funkcja**
--- | ---
[Cloudamize](https://www.cloudamize.com/platform) | Ocena
[Urządzenie 42](https://docs.device42.com/) | Ocena
[Turbonomic](https://learn.turbonomic.com/azure-migrate-portal-free-trial) | Ocena
[UnifyCloud](https://www.cloudatlasinc.com/cloudrecon/) | Ocena
[Firma corent Technology](https://www.corenttech.com/AzureMigrate/) | Ocena i migracja
[Carbonite](https://www.carbonite.com/globalassets/files/datasheets/carb-migrate4azure-microsoft-ds.pdf) | Migrate (Migracja)

### <a name="selecting-an-isv-tool"></a>Wybieranie narzędzia niezależnego dostawcy oprogramowania

Po dodaniu narzędziem niezależnego dostawcy oprogramowania do projektu Azure Migrate, Rozpocznij pracę z narzędziem, uzyskiwania licencji, lub rejestrowanie się w bezpłatnej wersji próbnej zgodnie z zasadami niezależnego dostawcy oprogramowania. Każde narzędzie ma opcję, aby nawiązać połączenie z usługi Azure Migrate. Postępuj zgodnie z instrukcjami narzędzia i dokumentację, Połącz narzędzie za pomocą usługi Azure Migrate.

## <a name="azure-migrate-server-assessment"></a>Usługa Azure Migrate oceny Server

Azure oceny migracji serwera umożliwia odnalezienie i ocenianie lokalnych maszyn wirtualnych z programu VMware i maszyn wirtualnych funkcji Hyper-V, dla migracji na platformę Azure. Pomaga określić następujące elementy:

- **Gotowość na platformę Azure:** Oceń, czy maszyny lokalne są gotowe do migracji na platformę Azure.
- **Ustalanie rozmiaru platformy Azure:** Po zakończeniu migracji, należy oszacować rozmiar maszyn wirtualnych platformy Azure.
- **Szacowanie kosztów platformy Azure:** Oszacowania kosztów działających na serwerach lokalnych na platformie Azure.
- **Wizualizacja zależności:** Określenie zależności między serwerami i najlepszy sposób przeniesienia serwerów zależnych na platformę Azure. 

Ocena Server korzysta z uproszczonego urządzenia, wdrożyć w środowisku lokalnym i zarejestrowanie serwera oceny.

- Urządzenie umożliwia odnalezienie maszyn lokalnych, nawiązanie połączenia z oceny Server i stale wysyła metadane i dane dotyczące wydajności do usługi Azure Migrate.
- Odnajdywanie jest bez wykorzystania agentów. Niczego nie trzeba zainstalować na wykrytych maszynach wirtualnych.
- Po odnalezieniu zbierać je w grupach, które składają się zazwyczaj z maszyn wirtualnych, czy chcesz migrowane razem.
- Utwórz ocenę dla grupy. Następnie można analizować oceny, aby ustalić strategię migracji.

## <a name="azure-migrate-server-migration"></a>Azure Migrate Server Migration

Maszyny wirtualne na platformie Azure w chmurze platformy Azure ułatwia Migrowanie migracji serwera należy przeprowadzić migrację lokalnych maszyn wirtualnych VMware, maszyny wirtualne funkcji Hyper-V, serwery fizyczne, innych zwirtualizowanych maszyn i publicznego. Można przeprowadzić migrację maszyn po ocenie je lub bez oceny. 

## <a name="azure-migrate-database-assessment"></a>Usługa Azure Migrate oceny bazy danych

Usługa Azure Migrate integruje się z Data Migration Assistant (DMA) do oceny pod kątem migracji do usługi Azure SQL DB, wystąpienia zarządzanego Azure SQL lub maszyn wirtualnych platformy Azure, programem SQL Server, bazy danych SQL Server w środowisku lokalnym. Program DMA informacje na temat potencjalnych problemów z blokowaniem dotyczące migracji. Określa on nieobsługiwane funkcje, a także nowe funkcje, może być korzystne po migracji i pomaga określić prawidłową ścieżkę do migracji bazy danych. [Dowiedz się więcej](https://docs.microsoft.com/sql/dma/dma-overview?view=sql-server-2017).


## <a name="azure-migrate-database-migration"></a>Usługa Azure Migrate migracji bazy danych

Usługa Azure Migrate integruje się z Azure bazy danych Migration Service (DMS), migrować lokalnych baz danych na platformę Azure. Usługa DMS umożliwia migracji lokalnych baz danych SQL, bazy danych SQL Azure i wystąpienia zarządzane SQL Azure maszynach wirtualnych platformy Azure. [Dowiedz się więcej](https://docs.microsoft.com/azure/dms/dms-overview).

## <a name="web-app-assessment-and-migration"></a>Ocena aplikacji sieci Web i migracji

Z Centrum usługi Azure Migrate może ocenić i Migrowanie lokalnych aplikacji sieci web na platformie Azure.

- **Ocena aplikacji sieci web w trybie online**: Użyj Asystent migracji usługi aplikacji Azure, aby ocenić lokalnych witryn sieci Web pod kątem migracji do usługi Azure App Service.
- **Przeprowadź migrację aplikacji sieci web**: Migrowanie aplikacji sieci web platformy .NET i PHP na platformie Azure przy użyciu usługi Azure App Service Migration Assistant.

[Dowiedz się więcej.](https://appmigration.microsoft.com/)

## <a name="offline-data-migration"></a>Migracja danych w trybie offline

Urządzenie Data Box w trybie offline rodziny produktów umożliwia przenoszenie dużych ilości danych na platformę Azure. [Dowiedz się więcej](https://docs.microsoft.com/azure/databox/)

## <a name="next-steps"></a>Następne kroki

- Spróbuj się z naszymi samouczkami, aby ocenić [maszyny wirtualne VMware](tutorial-assess-vmware.md) i [maszyn wirtualnych funkcji Hyper-V](tutorial-assess-hyper-v.md).
- [Uzyskaj więcej informacji](https://azure.microsoft.com/pricing/details/azure-migrate/) o cenach usługi Azure Migrate.
- [Przejrzyj często zadawane pytania](resources-faq.md) dotyczące usługi Azure Migrate.
