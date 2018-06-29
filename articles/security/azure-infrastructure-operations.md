---
title: Operacje Azure środowiska produkcyjnego i zarządzania
description: Ten artykuł zawiera ogólny opis zarządzania i działania sieci Azure środowiska produkcyjnego.
services: security
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: TomSh
ms.assetid: 61e95a87-39c5-48f5-aee6-6f90ddcd336e
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/28/2018
ms.author: terrylan
ms.openlocfilehash: dc389f5f5c155555deb860f041b15b0ea49ee416
ms.sourcegitcommit: d7725f1f20c534c102021aa4feaea7fc0d257609
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/29/2018
ms.locfileid: "37102537"
---
# <a name="azure-production-operations-and-management"></a>Operacje Azure środowiska produkcyjnego i zarządzania    
Zarządzanie i działania sieci produkcyjnych Azure jest wspólnym wysiłku między zespołami operacji systemu Azure i bazy danych SQL Azure. Wiele aplikacji i systemu narzędzi do monitorowania wydajności są używane w środowisku. Urządzenia sieciowe, serwery, usług i procesów aplikacji są monitorowane przy użyciu odpowiednich narzędzi.

Wiele poziomów monitorowania, rejestrowania i raportowania są wdrożone w celu zapewnienia bezpiecznego wykonania usługi działające w środowisku Microsoft Azure, w tym następujące akcje:

- Przede wszystkim agenta monitorowania Azure firmy Microsoft (MA) zbiera informacje dziennika monitorowania i diagnostyki w wielu miejscach, w tym FC i katalog główny systemu operacyjnego i zapisuje je w plikach dziennika. Wypycha ostatecznie porządkowanej podzbioru informacji do wstępnie skonfigurowane konto magazynu Azure. Ponadto monitorowania i diagnostyki usługi (MDS) jest niezależnych usługi, która odczytuje dane dziennika różnych monitorowania i diagnostyki i zawiera podsumowanie informacji. MDS zapisuje informacje dziennika zintegrowanego. Azure używa niestandardowej Azure zabezpieczeń monitorowania (ASM), która jest rozszerzeniem monitorowania systemu Azure. Ma ona składników, które obserwować, analiza i raport dotyczący zabezpieczeń odpowiednich zdarzeń z różnych punktach w platformie.
- Platforma WinFabric bazy danych SQL Azure Microsoft udostępnia zarządzania, wdrażania, rozwoju i usług nadzoru operacyjnej bazy danych SQL Azure firmy Microsoft. Oferuje usługi wdrażania rozproszonego, wieloetapowych, monitorowanie kondycji automatycznej naprawy i zgodności wersji usługi. Udostępnia ona następujące usługi:

   - Usługa modelowanie z środowisko projektowe o wysokiej wierności (klastrów centrum danych jest kosztowne i ograniczonych).
   - Jednym kliknięciem wdrożenia i uaktualnienia przepływów pracy dla usługi ładowania początkowego i konserwacji.
   - Raportowania zautomatyzowane przepływów pracy, aby włączyć Samonaprawianie kondycji.
   - W czasie rzeczywistym monitorowania, alertów i debugowanie urządzeń w węzłach Rozproszony system.
   - Scentralizowane zbieranie danych operacyjnych i metryki dla elementu głównego rozproszonej spowodować wglądu analizy i usługi.
   - Operational narzędzi dla wdrożenia, zarządzanie zmianami i monitorowania.
   - Skrypty platformy i programu alarmowego WinFabric bazy danych SQL Azure Microsoft uruchamiaj stale i monitorowanie w czasie rzeczywistym.

Jeśli wystąpi wszelkich nieprawidłowości, proces reagowania na zdarzenia zostały wykonane przez zespół Azure klasyfikacji zdarzenia jest aktywowane. Odpowiednie Azure pomocy technicznej jest powiadamiany o konieczności odpowiadanie na zdarzenia. Śledzenie problemów i rozwiązania są udokumentowane i zarządzane w scentralizowanej systemu obsługi biletów. Metryki czas działania systemu są dostępne w obszarze umowy Non-ujawnienie (NDA) i na żądanie.

## <a name="corporate-network-and-multi-factor-access-to-production"></a>Sieci firmowej i wieloskładnikowego dostęp do środowiska produkcyjnego
Podstawowy użytkownik sieci firmowej zawiera personel pomocy technicznej firmy Microsoft Azure. Sieci firmowej obsługuje funkcje firmowej wewnętrznej i obejmuje dostęp do wewnętrznych aplikacji, które są używane do obsługi klienta usługi Azure. Sieci firmowej jest logicznie i fizycznie oddzielona od sieci produkcyjnej Azure. Personel firmy Microsoft Azure dostęp do sieci firmowej, przy użyciu stacje robocze Microsoft Azure i przenośnych. Wszyscy użytkownicy muszą mieć konta usługi Active Directory (AD), w tym nazwę użytkownika i hasło, aby uzyskać dostęp do zasobów sieci firmowej. Dostęp CorpNet używa kont usługi AD, które są wydawane do tego personel firmy Microsoft, kontrahentów, dostawców i zarządzane przez MSIT. Unikatowe identyfikatory rozróżnienia pracowników na podstawie ich stanu zatrudnienia w firmie Microsoft.

Dostęp do wewnętrznych aplikacji Azure jest kontrolowany za pomocą uwierzytelniania z Active Directory Federation Services (ADFS). Usługi AD FS to usługa hostowana przez MSIT, który umożliwia uwierzytelnianie użytkowników CorpNet przez zastosowanie bezpiecznego oświadczeń token i użytkownika. Usługi AD FS umożliwia wewnętrznych aplikacji Microsoft Azure do uwierzytelniania użytkowników w domenie AD firmy Microsoft. Dostęp do sieci produkcyjnych ze środowiska CorpNet, użytkownik musi uwierzytelnić się przy użyciu usługi Multi-Factor authentication.

## <a name="next-steps"></a>Kolejne kroki
Aby dowiedzieć się więcej na temat firmy Microsoft jest zapewnienie infrastruktury platformy Azure, zobacz:

- [Urządzenia platformy Azure, lokalne i zabezpieczenia fizyczne](azure-physical-security.md)
- [Dostępność infrastruktury platformy Azure](azure-infrastructure-availability.md)
- [Składniki systemu Azure informacji i granice](azure-infrastructure-components.md)
- [Architektura sieci platformy Azure](azure-infrastructure-network.md)
- [Sieci Azure środowiska produkcyjnego](azure-production-network.md)
- [Funkcje zabezpieczeń bazy danych SQL Azure firmy Microsoft](azure-infrastructure-sql.md)
- [Monitorowanie infrastruktury platformy Azure](azure-infrastructure-monitoring.md)
- [Integralność infrastruktury platformy Azure](azure-infrastructure-integrity.md)
- [Ochrona danych klienta na platformie Azure](azure-protection-of-customer-data.md)
