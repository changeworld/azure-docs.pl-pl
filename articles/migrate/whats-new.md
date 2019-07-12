---
title: Co nowego w usłudze Azure Migrate | Dokumentacja firmy Microsoft
description: Ten artykuł zawiera omówienie usługi Azure Migrate.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: overview
ms.date: 06/10/2019
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: 2c3bc596076f3ec4f9d41f0da819ddd386fee63c
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/11/2019
ms.locfileid: "67809995"
---
# <a name="whats-new-in-azure-migrate"></a>Co nowego w usłudze Azure Migrate

[Usługa Azure Migrate](migrate-services-overview.md) ułatwia odnajdywanie, ocena i migracja serwerów, aplikacji i danych do chmury Microsoft Azure. Ten artykuł zawiera podsumowanie nowych funkcji w usłudze Azure Migrate.



## <a name="azure-migrate-new-version"></a>Usługa Azure Migrate nowej wersji

Nowa wersja usługi Azure Migrate została wydana w lipcu 2019 r. 

- **Bieżąca wersja (nowy)** : Tej wersji można używać do tworzenia projektów usługi Azure Migrate, odnajdowanie maszyn lokalnych i organizowania, oceny i migracji. 
- **Poprzednia wersja**: Dla klientów przy użyciu poprzedniej wersji usługi Azure Migrate (obsługiwał tylko ocena lokalnych maszyn wirtualnych programu VMware) teraz należy używać bieżącej wersji. W poprzedniej wersji można już tworzyć nowych projektów usługi Azure Migrate lub wykonywać nowych operacji odnajdywania. Możesz nadal uzyskiwać dostęp do istniejących projektów. Aby to zrobić w witrynie Azure portal > wszystkich usług wyszukiwania dla usługi Azure Migrate. W powiadomieniach usługi Azure Migrate znajduje się link dostęp do starych projektów usługi Azure Migrate.


## <a name="azure-migrate-features"></a>Funkcje usługi Azure Migrate

Nowa wersja usługi Azure Migrate oferuje wiele nowych funkcji:


- **Ujednolicone migracji platformy**: Usługa Azure Migrate udostępnia teraz jednemu portalowi scentralizować, zarządzanie i śledzić podróż migracyjną na platformę Azure za pomocą przepływu ulepszone wdrażanie i środowisko portalu.
- **Narzędzia do oceny i migracji**: Usługa Azure Migrate oferuje natywnych narzędzi i integruje się z innymi usługami Azure, a także za pomocą narzędzi Niezależnym dostawcą oprogramowania. [Dowiedz się więcej](migrate-services-overview.md#isv-integration) informacji na temat integracji niezależnego dostawcy oprogramowania.
- **Usługa Azure Migrate oceny**: Za pomocą narzędzia do oceny migracji serwera w Azure, możesz ocenić maszyny wirtualne VMware i maszyny wirtualne funkcji Hyper-V pod kątem migracji do platformy Azure. Możesz również oceny pod kątem migracji przy użyciu innych usług platformy Azure i narzędzi niezależnego dostawcy oprogramowania.
- **Migracja na platformę Azure Migrate**: Narzędzie migracji serwera migracji platformy Azure, można przeprowadzić migrację lokalnych maszyn wirtualnych z programu VMware i maszyn wirtualnych funkcji Hyper-V Azure, a także serwerów fizycznych, innych serwerów zwirtualizowanych i publicznego i prywatnego chmury maszyn wirtualnych. Ponadto można migrować na platformę Azure za pomocą narzędzi niezależnego dostawcy oprogramowania.
- **Urządzenie Azure Migrate**: Usługa Azure Migrate wdraża uproszczone urządzenia do odnajdywania i oceny lokalnych maszyn wirtualnych VMware i maszyny wirtualne funkcji Hyper-V.
    - Tego urządzenia jest używane przez oceny Server migracji platformy Azure i usługi Azure Migration migracji serwera bez wykorzystania agentów migracji.
    - Urządzenie ciągle odnajduje danych metadanych i wydajności serwera, do celów oceny i migracji.  
- **Migracja maszyn wirtualnych VMware**:  Migrowanie serwera migracji na platformę Azure udostępnia kilka metod migracji maszyn wirtualnych VMware na platformę Azure lokalnie.  Migracja bez agenta przy użyciu usługi Azure Migrate urządzenia i oparte na agentach migracji, która korzysta z urządzenia replikacji i wdraża agenta na każdej maszynie Wirtualnej, do których użytkownik chce migrować. [Dowiedz się więcej](server-migrate-overview.md)
 - **Bazy danych do oceny i migracji**: Usługi Azure Migrate można oceniać gotowość lokalnych baz danych pod kątem migracji do platformy Azure, korzystając z Asystenta migracji bazy danych Azure. Można przeprowadzić migrację baz danych przy użyciu usługi Azure Database Migration Service.
- **Migracja aplikacji Web**: Możesz ocenić aplikacje internetowe przy użyciu adresu URL punktu końcowego publicznego w usłudze Azure App Service. W przypadku migracji wewnętrznych aplikacji .NET można pobrać i uruchomić Asystenta migracji usługi aplikacji. 
- **Urządzenie Data Box**: Importuj duże ilości danych w trybie offline na platformę Azure za pomocą usługi Azure Data Box w usłudze Azure Migrate.


## <a name="next-steps"></a>Następne kroki

- [Uzyskaj więcej informacji](https://azure.microsoft.com/pricing/details/azure-migrate/) o cenach usługi Azure Migrate.
- [Przejrzyj często zadawane pytania](resources-faq.md) dotyczące usługi Azure Migrate.
- Spróbuj się z naszymi samouczkami, aby ocenić [maszyny wirtualne VMware](tutorial-assess-vmware.md) i [maszyn wirtualnych funkcji Hyper-V](tutorial-assess-hyper-v.md).
