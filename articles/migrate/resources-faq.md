---
title: Często zadawane pytania dotyczące Azure Migrate
description: Uzyskaj odpowiedzi na często zadawane pytania dotyczące usługi Azure Migrate.
author: snehaamicrosoft
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 11/21/2019
ms.author: snehaa
ms.openlocfilehash: 8bfa9237d365636c0bdaa3af06c5af23b683231d
ms.sourcegitcommit: 5925df3bcc362c8463b76af3f57c254148ac63e3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/31/2019
ms.locfileid: "75563545"
---
# <a name="azure-migrate-common-questions"></a>Azure Migrate: typowe pytania

W tym artykule znajdują się odpowiedzi na często zadawane pytania dotyczące Azure Migrate. Jeśli po przeczytaniu tego artykułu masz dalsze zapytania, Opublikuj je na [forum Azure Migrate](https://aka.ms/AzureMigrateForum). Jeśli masz inne pytania, zapoznaj się z następującymi artykułami:

- [Pytania](common-questions-appliance.md) dotyczące urządzenia Azure Migrateowego.
- [Pytania](common-questions-discovery-assessment.md) dotyczące odnajdywania, oceny i wizualizacji zależności.


## <a name="what-is-azure-migrate"></a>Co to jest usługa Azure Migrate?

Azure Migrate udostępnia centralne centrum do śledzenia odnajdywania, oceny i migracji lokalnych aplikacji i obciążeń oraz maszyn wirtualnych chmur prywatnych/publicznych na platformie Azure. Centrum udostępnia Azure Migrate narzędzia do oceny i migracji, a także oferty niezależnych dostawców oprogramowania (ISV) innych firm. [Dowiedz się więcej](migrate-services-overview.md).


## <a name="what-can-i-do-with-azure-migrate"></a>Co mogę zrobić z Azure Migrate?

Użyj Azure Migrate, aby odnajdywać, oceniać i migrować lokalną infrastrukturę, aplikacje i dane na platformę Azure. Azure Migrate obsługuje ocenę i migrację lokalnych maszyn wirtualnych programu VMware, maszyn wirtualnych funkcji Hyper-V, serwerów fizycznych, innych zwirtualizowanych maszyn wirtualnych, baz danych, aplikacji sieci Web i pulpitów wirtualnych. 

## <a name="whats-the-difference-between-azure-migrate-and-site-recovery"></a>Jaka jest różnica między Azure Migrate i Site Recovery?

Azure Migrate udostępnia scentralizowane centrum oceny i migracji na platformę Azure. [Azure Site Recovery](../site-recovery/site-recovery-overview.md) to rozwiązanie odzyskiwania po awarii. Azure Migrate: Narzędzie do migracji serwera używa niektórych funkcji Site Recovery zaplecza na potrzeby migracji podnoszenia i przesunięcia niektórych maszyn lokalnych.

## <a name="whats-the-difference-between-azure-migrate-server-assessment-and-the-map-toolkit"></a>Jaka jest różnica między oceną Azure Migrate serwera a zestawem narzędzi mapy?

Ocena serwera zapewnia ocenę, aby pomóc w zagotowości do migracji oraz ocenę obciążeń związanych z migracją na platformę Azure. [Zestaw narzędzi firmy Microsoft do oceny i planowania (map)](https://www.microsoft.com/download/details.aspx?id=7826) ułatwia wykonywanie innych zadań, w tym planowanie migracji nowszych wersji klienta/serwera operacyjnego Windows i śledzenia użycia oprogramowania. W tych scenariuszach Kontynuuj korzystanie z narzędzia MAP Toolkit.

## <a name="whats-the-difference-between-server-assessment-and-the-site-recovery-deployment-planner"></a>Jaka jest różnica między oceną serwera a Site Recovery Planista wdrażania?

Ocena serwera to narzędzie do planowania migracji. Planista wdrażania Site Recovery jest narzędziem do planowania odzyskiwania po awarii.

- **Planowanie migracji lokalnej na platformę Azure**: Jeśli planujesz migrację serwerów lokalnych do platformy Azure, użyj oceny serwera na potrzeby planowania migracji. Ocenia obciążenia lokalne i udostępnia wskazówki i narzędzia ułatwiające Migrowanie. Po zakończeniu planowania migracji można użyć narzędzi, w tym migracji serwera Azure Migrate, aby przeprowadzić migrację maszyn na platformę Azure.
- **Planowanie odzyskiwania po awarii na platformie Azure**: Jeśli planujesz skonfigurować odzyskiwanie po awarii na platformie Azure przy użyciu Site Recovery, użyj Site Recovery planista wdrażania. Planista wdrażania zapewnia głębokiej, Site Recoveryowej oceny środowiska lokalnego na potrzeby odzyskiwania po awarii. Zawiera zalecenia dotyczące odzyskiwania po awarii, takie jak replikacja i tryb failover.

## <a name="how-does-server-migration-work-with-site-recovery"></a>Jak działa Migracja serwera z Site Recovery?

- Jeśli używasz Azure Migrate: Migracja serwera w celu przeprowadzenia migracji bez wykorzystania agentów lokalnych maszyn wirtualnych VMware, migracja jest natywna dla Azure Migrate i Site Recovery nie jest używana.
- W przypadku korzystania z Azure Migrate: Migracja serwera w celu przeprowadzenia migracji maszyn wirtualnych VMware lub migracji maszyn wirtualnych lub serwerów fizycznych funkcji Hyper-V, Migracja serwera Azure Migrate korzysta z aparatu replikacji Azure Site Recovery.


## <a name="which-geographies-are-supported"></a>Które lokalizacje geograficzne są obsługiwane?

Przejrzyj Azure Migrate obsługiwane lokalizacje geograficzne dla [maszyn wirtualnych VMware](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-vmware#azure-migrate-projects) i [maszyn wirtualnych funkcji Hyper-V](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-hyper-v#azure-migrate-projects).

## <a name="how-do-i-get-started"></a>Jak rozpocząć pracę?

Należy zidentyfikować potrzebne narzędzie i dodać je do projektu Azure Migrate. W przypadku dodawania narzędzia niezależnego dostawcy oprogramowania lub przenoszenia:
- Zacznij od uzyskania licencji lub zarejestrowania się w celu skorzystania z bezpłatnej wersji próbnej zgodnie z zasadami dotyczącymi narzędzi. Licencjonowanie dla narzędzi jest zgodne z modelem niezależnego dostawcy oprogramowania lub narzędzia.
- W każdym narzędziu jest dostępna opcja nawiązywania połączenia z Azure Migrate. Postępuj zgodnie z instrukcjami i dokumentacją narzędzia, aby połączyć narzędzie z Azure Migrate.
Możesz centralnie śledzić swoją drogę migracji z poziomu projektu Azure Migrate, na platformie Azure i w innych narzędziach.

### <a name="how-do-i-delete-a-project"></a>Jak mogę usunąć projekt?

[Dowiedz się, jak](how-to-delete-project.md) usunąć projekt. 






## <a name="next-steps"></a>Następne kroki
Zapoznaj się z [omówieniem Azure Migrate](migrate-services-overview.md).
