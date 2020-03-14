---
title: Co nowego w Azure Migrate
description: Dowiedz się więcej na temat Nowości i ostatnich aktualizacji w usłudze Azure Migrate.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: overview
ms.date: 11/30/2019
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: 63b6783a2f36d5bc9e84ce8291e7025b27359b6c
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/13/2020
ms.locfileid: "79241094"
---
# <a name="whats-new-in-azure-migrate"></a>Co nowego w Azure Migrate

[Azure Migrate](migrate-services-overview.md) ułatwia odnajdywanie, ocenianie i migrowanie lokalnych serwerów, aplikacji i danych do chmury Microsoft Azure. Ten artykuł podsumowuje nowe funkcje w Azure Migrate.



## <a name="update-november-2019"></a>Aktualizacja (listopad 2019)

Dodano kilka nowych funkcji do Azure Migrate:

- **Ocena serwera fizycznego**. Teraz jest obsługiwana Ocena lokalnych serwerów fizycznych, oprócz migracji serwera fizycznego, która jest już obsługiwana.
- **Ocena na podstawie importu**. Teraz obsługiwana jest ocena maszyn przy użyciu metadanych i danych wydajności dostępnych w pliku CSV.
- **Odnajdywanie aplikacji**: Azure Migrate obsługuje teraz odnajdywanie aplikacji, ról i funkcji na poziomie aplikacji przy użyciu urządzenia Azure Migrate. Jest to obecnie obsługiwane tylko w przypadku maszyn wirtualnych VMware i jest ograniczone tylko do odnajdowania (ocena nie jest obecnie obsługiwana). [Dowiedz się więcej](how-to-discover-applications.md)
- **Wizualizacja zależności bez agenta**: nie trzeba już jawnie instalować agentów w celu wizualizacji zależności. Teraz obsługiwane są zarówno Agent, jak i oparte na agentach.
- **Pulpit wirtualny**: Użyj narzędzi niezależnych dostawców oprogramowania, aby ocenić i zmigrować lokalną infrastrukturę pulpitu wirtualnego (VDI) do pulpitu wirtualnego systemu Windows na platformie Azure.
- **Aplikacja internetowa**: Asystent migracji Azure App Service używany do oceniania i migracji aplikacji sieci Web, jest teraz zintegrowana z Azure Migrate.

Dodano nowe narzędzia do oceny i migracji do Azure Migrate:

- **Stojak**: zapewnianie migracji do chmury.
- Produkt **przenoszenia**: Ocena oferty.

[Dowiedz się więcej](migrate-services-overview.md) o korzystaniu z narzędzi i ofert niezależnych dostawców oprogramowania na potrzeby oceny i migracji w programie Azure Migrate.

## <a name="release-version-july-2019"></a>Wydanie wersji (lipiec 2019)

Bieżąca wersja Azure Migrate została wydana w lipcu 2019.

- **Bieżąca wersja**: Użyj tej wersji do tworzenia projektów Azure Migrate, odnajdywania maszyn lokalnych i organizowania ocen i migracji.
- **Poprzednia wersja**: dla klienta korzystającego z poprzedniej wersji Azure Migrate (obsługiwana jest tylko Ocena lokalnych maszyn wirtualnych programu VMware). teraz należy używać bieżącej wersji. W poprzedniej wersji nie można już tworzyć nowych projektów Azure Migrate ani wykonywać nowych odkrycia. Nadal możesz uzyskiwać dostęp do istniejących projektów. Aby to zrobić, w Azure Portal > **wszystkie usługi**, Wyszukaj **Azure Migrate**. W Azure Migrate powiadomieniach jest dostępny link umożliwiający dostęp do starych projektów Azure Migrate.


### <a name="azure-migrate-features"></a>Funkcje Azure Migrate

Bieżąca wersja Azure Migrate zawiera wiele nowych funkcji:


- **Ujednolicona platforma migracji**: Azure Migrate teraz oferuje pojedynczy Portal, który pozwala na scentralizowanie i monitorowanie podróży migracji do platformy Azure oraz zarządzanie nią przy użyciu ulepszonego przepływu wdrożenia i środowiska portalu.
- **Narzędzia do oceny i migracji**: usługa Azure Migrate udostępnia narzędzia natywne i integruje się z innymi usługami platformy Azure, a także z narzędziami niezależnych dostawców oprogramowania (ISV). [Dowiedz się więcej](migrate-services-overview.md#isv-integration) o integracji niezależnego dostawcy oprogramowania.
- **Azure Migrate oceny**: za pomocą narzędzia do oceny serwera Azure Migrate można ocenić maszyny wirtualne VMware i maszyny wirtualne funkcji Hyper-V do migracji na platformę Azure. Możesz również ocenić migrację przy użyciu innych usług platformy Azure i narzędzi niezależnych dostawców oprogramowania.
- **Azure Migrate migracji**: za pomocą narzędzia migracji serwera Azure Migrate można migrować lokalne maszyny wirtualne VMware i maszyny wirtualne funkcji Hyper-V do platformy Azure, a także serwery fizyczne, inne serwery zwirtualizowane oraz maszyny wirtualne w chmurze prywatnej/publicznej. Ponadto można migrować do platformy Azure za pomocą narzędzi niezależnych dostawców oprogramowania.
- **Urządzenie Azure Migrate**: Azure Migrate wdraża lekkim urządzeniem do odnajdywania i oceniania lokalnych maszyn wirtualnych VMware i maszyn wirtualnych funkcji Hyper-V.
    - To urządzenie jest używane przez Azure Migrate oceny serwera i migracji serwera Azure Migrate na potrzeby migracji bez agentów.
    - Urządzenie stale odnajduje metadane serwera i dane wydajności na potrzeby oceny i migracji.  
- **Migracja maszyny wirtualnej VMware**: migracja serwera Azure Migrate zapewnia kilka metod migrowania lokalnych maszyn wirtualnych programu VMware na platformę Azure.  Migracja bez agentów przy użyciu urządzenia Azure Migrate oraz migracja oparta na agentach, która korzysta z urządzenia replikacji, i wdraża agenta na każdej maszynie wirtualnej, która ma zostać zmigrowana. [Dowiedz się więcej](server-migrate-overview.md)
 - **Ocena i migracja bazy danych**: w Azure Migrate można ocenić lokalne bazy danych na potrzeby migracji na platformę Azure przy użyciu usługi azure Database Asystent migracji. Bazę danych można migrować przy użyciu Azure Database Migration Service.
- **Migracja aplikacji sieci Web**: możesz ocenić aplikacje sieci Web przy użyciu publicznego adresu URL punktu końcowego z Azure App Service. W przypadku migracji wewnętrznych aplikacji .NET można pobrać i uruchomić App Service Asystent migracji.
- **Urządzenie Data Box**: zaimportuj duże ilości danych w trybie offline na platformę Azure przy użyciu Azure Data Box w Azure Migrate.


## <a name="next-steps"></a>Następne kroki

- [Uzyskaj więcej informacji](https://azure.microsoft.com/pricing/details/azure-migrate/) o cenach usługi Azure Migrate.
- [Przejrzyj często zadawane pytania](resources-faq.md) dotyczące usługi Azure Migrate.
- Wypróbuj nasze samouczki, aby ocenić [maszyny wirtualne VMware](tutorial-assess-vmware.md) i [maszyny wirtualne funkcji Hyper-V](tutorial-assess-hyper-v.md).
