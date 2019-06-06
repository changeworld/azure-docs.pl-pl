---
title: Zarządzanie sieci platformy Azure środowiska produkcyjnego — Microsoft Azure
description: W tym artykule opisano, jak firma Microsoft zarządza i działa sieci platformy Azure środowiska produkcyjnego, aby zabezpieczyć centrów danych platformy Azure.
services: security
documentationcenter: n
author: TerryLanfear
manager: barbkess
editor: TomSh
ms.assetid: 61e95a87-39c5-48f5-aee6-6f90ddcd336e
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/30/2019
ms.author: terrylan
ms.openlocfilehash: 515896851c5bf7b9c847416ebadedf1242e0eeb9
ms.sourcegitcommit: ef06b169f96297396fc24d97ac4223cabcf9ac33
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/31/2019
ms.locfileid: "66428267"
---
# <a name="management-and-operation-of-the-azure-production-network"></a>Zarządzanie i operacje sieci platformy Azure środowiska produkcyjnego    
W tym artykule opisano, jak firma Microsoft zarządza i działa sieci platformy Azure środowiska produkcyjnego, aby zabezpieczyć centrów danych platformy Azure.

## <a name="monitor-log-and-report"></a>Monitor, dzienników i raportów

Zarządzanie i działania sieci platformy Azure środowiska produkcyjnego jest wspólnym wysiłku między zespołom platformy Azure i usługi Azure SQL Database. Zespoły wykorzystują narzędzia kilka systemu i monitorowanie wydajności aplikacji w środowisku. I używają odpowiednich narzędzi do monitorowania urządzeń sieciowych, serwery, usług i procesów aplikacji.

W celu zapewnienia bezpiecznego wykonywania usług działających w środowisku platformy Azure, zespołów operacyjnych implementuje wiele poziomów monitorowania, rejestrowania i raportowania, w tym następujące czynności:

- Przede wszystkim Microsoft Monitoring Agent (MMA) zbiera informacje dziennika monitorowania i diagnostyki z wielu miejsc, w tym Kontroler sieci szkieletowej (FC) oraz główny systemu operacyjnego (OS) i zapisuje je w plikach dziennika. Agent ostatecznie wypycha Wytrawiony podzbioru informacji pod uwagę wstępnie skonfigurowanym magazynie platformy Azure. Ponadto niezależnych monitorowania i diagnostyki usługi odczytuje różne dane dziennika monitorowania i diagnostyki i zawiera podsumowanie informacji. Usługa monitorowania i diagnostyki zapisuje informacje w dzienniku zintegrowanego. Platforma Azure używa niestandardowej zabezpieczeń platformy Azure monitorowania, która jest rozszerzeniem monitorowania systemu platformy Azure. Zawiera składniki, które obserwowania, analizowania i sporządzić raport na temat zdarzeń dotyczących zabezpieczeń z różnych miejsc w platformie.

- Platforma Azure SQL Database Windows Fabric zapewnia, zarządzania, wdrażania, rozwój i nadzoru operacyjnej usług dla usługi Azure SQL Database. Platforma oferuje usługi rozproszonej, wieloma krokami wdrażania, monitorowania kondycji, automatycznej naprawy i zgodności wersji usługi. Oferuje ono następujące usługi:

   - Usługi, modelowanie za pomocą środowiska deweloperskiego o dużej wierności (klastry datacenter są kosztowne, a deficytowe).
   - Wdrażanie jednym kliknięciem i uaktualniania przepływy pracy dla usługi ładowania początkowego i konserwacji.
   - Kondycja raportowania za pomocą zautomatyzowane przepływy pracy, aby umożliwić samonaprawiania.
   - Monitorowanie w czasie rzeczywistym, alerty i debugowania urządzeń w węzłach systemu rozproszonego.
   - Scentralizowanego zbierania danych operacyjnych i metryk dla głównego rozproszonych spowodować insight analizy i usługi.
   - Operacyjne, narzędzia do wdrażania, zarządzanie zmianami i monitorowania.
   - Azure SQL Database Windows Fabric platformy i strażnika skryptów wykonuj ciągle i monitorowanie w czasie rzeczywistym.

Jeśli wystąpią wszelkich nieprawidłowości, procesu reagowania na zdarzenia następuje Azure zdarzenia zespół jest aktywowane. Personel odpowiedniej pomocy technicznej platformy Azure są powiadamiani, aby odpowiedzieć na zdarzenie. Śledzenie problemów i rozwiązania są udokumentowane i zarządzane w scentralizowanym systemie obsługi biletów. Metryki czasu działania systemu są dostępne w ramach umowę (o poufności NDA) i na żądanie.

## <a name="corporate-network-and-multi-factor-access-to-production"></a>Siecią firmową a Multi-Factor Authentication dostępu do środowiska produkcyjnego
Podstawowy użytkownik sieci firmowej obejmuje personel pomocy technicznej platformy Azure. W sieci firmowej obsługuje funkcje wewnętrzne firmy i obejmuje dostęp do aplikacji wewnętrznych, które są używane do obsługi klienta platformy Azure. W sieci firmowej jest logicznie i fizycznie oddzielony od sieci platformy Azure środowiska produkcyjnego. Azure personelu dostęp do sieci firmowej, korzystając z stacje robocze platformy Azure i przenośnych. Wszyscy użytkownicy muszą mieć konta usługi Azure Active Directory (Azure AD), w tym nazwę użytkownika i hasło, aby dostęp do zasobów sieci firmowej. Dostęp do sieci firmowej używa konta usługi Azure AD, które są wydawane do tego personel firmy Microsoft, kontrahentów i dostawców i zarządza technologii informatycznych firmy Microsoft. Unikatowe identyfikatory rozróżnia pracowników na podstawie ich stanu pracy w firmie Microsoft.

Dostęp do wewnętrznych aplikacji systemu Azure jest kontrolowany przy użyciu uwierzytelniania za pomocą usługi Active Directory Federation Services (AD FS). Usługi AD FS to usługa hostowana przez technologii informatycznych firmy Microsoft, który zapewnia uwierzytelnianie użytkowników sieci firmowej za pośrednictwem stosowanie bezpiecznego oświadczeń tokenu i użytkownika. Usługi AD FS umożliwia wewnętrzne aplikacje platformy Azure do uwierzytelniania użytkowników w usłudze Microsoft firmowej domeny usługi active directory. Dostęp do sieci produkcyjnych w środowisku sieci firmowej, użytkownicy muszą zostać uwierzytelnione przy użyciu usługi Multi-Factor authentication.

## <a name="next-steps"></a>Kolejne kroki
Aby dowiedzieć się więcej na temat firmy Microsoft jest zapewnienie infrastruktury platformy Azure, zobacz:

- [Urządzenia platformy Azure, lokalnie i zabezpieczenia fizyczne](azure-physical-security.md)
- [Dostępność infrastruktury platformy Azure](azure-infrastructure-availability.md)
- [Usługi Azure information składników systemu i granice](azure-infrastructure-components.md)
- [Architektura sieci platformy Azure](azure-infrastructure-network.md)
- [Sieć platformy Azure środowiska produkcyjnego](azure-production-network.md)
- [Funkcje zabezpieczeń w usłudze Azure SQL Database](azure-infrastructure-sql.md)
- [Monitorowanie infrastruktury platformy Azure](azure-infrastructure-monitoring.md)
- [Integralność infrastruktury platformy Azure](azure-infrastructure-integrity.md)
- [Ochrona danych klientów platformy Azure](azure-protection-of-customer-data.md)
