---
title: Usługa Azure SQL Database Managed architektura łączności wystąpienia | Dokumentacja firmy Microsoft
description: Ten artykuł zawiera omówienie komunikacji wystąpienia zarządzanego Azure SQL Database i architektura łączności także wyjaśnia, jak różnych składników funkcji do kierowania ruchu do wystąpienia zarządzanego.
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: bonova, carlrab
manager: craigg
ms.date: 08/16/2018
ms.openlocfilehash: d0747e9e54a48ecccb7051ad4fe9998d86599ffe
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/04/2018
ms.locfileid: "52840745"
---
# <a name="azure-sql-database-managed-instance-connectivity-architecture"></a>Usługa Azure SQL Database Managed architektura łączności wystąpienia 

Ten artykuł zawiera omówienie komunikacji wystąpienia zarządzanego Azure SQL Database i architektura łączności także wyjaśnia, jak różnych składników funkcji do kierowania ruchu do wystąpienia zarządzanego.  

## <a name="communication-overview"></a>Omówienie komunikacji 

Na poniższym diagramie przedstawiono jednostki, łączących się z wystąpieniem zarządzanym, a także zasoby, które ma Dotrzyj do prawidłowego wystąpienia zarządzanego. 

![jednostki architektura łączności](./media/managed-instance-connectivity-architecture/connectivityarch001.png)

Komunikat, który jest przedstawiony w dolnej części diagramu reprezentuje aplikacji klientów i narzędzia do nawiązywania połączenia z wystąpienia zarządzanego jako źródło danych.  

Jak wystąpienie zarządzane to platformy jako a usługi (PaaS) z oferty firmy Microsoft zarządza tę usługę przy użyciu zautomatyzowanych agentów (zarządzania, wdrażania i konserwacji) oparte na strumieniach danych telemetrycznych. Wystąpienie zarządzane usługi zarządzania jest wyłączną odpowiedzialność firmy Microsoft, klienci nie będą mogli dostęp do maszyn wirtualnych klastra wystąpienie zarządzane za pośrednictwem protokołu RDP. 

Niektóre operacje programu SQL Server, inicjowane przez użytkowników końcowych lub aplikacje mogą wymagać wystąpienia zarządzanego do interakcji z platformą. Jeden przypadek to wystąpienie zarządzane bazy danych — z zasobem, który jest dostępna za pośrednictwem portalu, programu PowerShell i wiersza polecenia platformy Azure. 

Wystąpienie zarządzane jest zależny od innych usług platformy Azure do ich prawidłowego funkcjonowania (np. usługi Azure Storage do przechowywania kopii zapasowych dla telemetrii usługi Azure Service Bus, usługa Azure AD do uwierzytelniania, usługi Azure Key Vault dla funkcji TDE i tak dalej) i inicjuje połączenia z tymi odpowiednio. 

Cała komunikacja z wymienionych powyżej, są szyfrowane i podpisany przy użyciu certyfikatów. Aby upewnić się, że komunikujące się strony są zaufane, wystąpienia zarządzanego stale sprawdza te certyfikaty, kontaktując się z urzędu certyfikacji. Jeśli certyfikaty zostaną odwołane lub wystąpienia zarządzanego nie można zweryfikować je, zamyka połączenia, aby chronić dane. 

## <a name="high-level-connectivity-architecture"></a>Architektura wysokiego poziomu łączności 

Na wysokim poziomie wystąpienie zarządzane to zestaw składników usługi w serwisie to dedykowany zestaw izolowane maszyny wirtualne uruchamiane w podsieci sieci wirtualnej klienta, które tworzą klaster wirtualny. 

Wiele wystąpień zarządzanych może być realizowany w jednym klastrem wirtualnym. Klaster jest automatycznie rozszerzony lub nabytej w razie potrzeby zmianie liczby wystąpień aprowizowane w podsieci klienta. 

Aplikacje klienta można połączyć się z wystąpieniem zarządzanym, zapytań i aktualizacji bazy danych tylko wtedy, gdy są uruchamiane wewnątrz sieci wirtualnej lub równorzędnej sieci wirtualnej lub sieci VPN / Expressroute połączone sieci przy użyciu punktu końcowego za pomocą prywatnego adresu IP.  

![diagram architektury łączności](./media/managed-instance-connectivity-architecture/connectivityarch002.png)

Zarządzanie i wdrażanie usług firmy Microsoft jest wykonywany poza siecią wirtualną, dzięki połączenia między wystąpieniem zarządzanym i usług firmy Microsoft przechodzi przez punkty końcowe z publicznych adresów IP. Gdy wystąpienie zarządzane tworzy połączenie wychodzące, po stronie odbierającej wygląda ona pochodzi ten publiczny adres IP z powodu translacji adresów sieciowych (NAT). 

Ruch zarządzania odbywa się za pośrednictwem sieci wirtualnej klienta. Oznacza to, że wpływają na elementy infrastruktury sieci wirtualnej i może potencjalnie szkód ruch związany z zarządzaniem powodują, że wystąpienia wejść w stan uszkodzony i staną się niedostępne. 

> [!IMPORTANT]
> W celu zwiększenia dostępności expirience i usługą klienta firma Microsoft stosuje zasady intencji sieci elementy infrastruktury sieci wirtualnej platformy Azure, które mogą wpłynąć na działanie wystąpienia zarządzanego. Jest to mechanizm platformy do komunikowania się wymagania sieciowe w sposób niewidoczny dla użytkownika użytkownikom końcowym z głównym celem, aby zapobiec błędnej konfiguracji sieci i upewnij się, normalnej wystąpienia zarządzanego. Po usunięciu wystąpienia zarządzanego sieciowe przeznaczenie zasady zostaną usunięte także. 

## <a name="virtual-cluster-connectivity-architecture"></a>Architektura łączności klaster wirtualny 

Przyjrzyjmy się szczegółowo z wizualizacją w architekturze połączenia wystąpienia zarządzanego. Na poniższym diagramie przedstawiono koncepcyjny układ klaster wirtualny. 

![klaster wirtualny diagram architektury łączności](./media/managed-instance-connectivity-architecture/connectivityarch003.png)

Klienci połączyć się z wystąpieniem zarządzanym przy użyciu nazwy hosta, który ma postać < mi_name >. <clusterid>. database.windows.net. Ta nazwa hosta jest rozpoznawany jako prywatny adres IP jest zarejestrowany w publicznej strefie DNS, i jest rozpoznania publicznie. 

Ten prywatny adres IP należy do zarządzanego wystąpienia wewnętrznego obciążenia równoważenia (ILB) która kieruje ruch do bramy zarządzane wystąpienia (GW). Zgodnie z wewnątrz tego samego klastra, potencjalnie może uruchomić wiele wystąpień zarządzanych, GW używa nazwy hosta wystąpienia zarządzanego przekierowywanie ruchu na prawidłowe usługi aparatu programu SQL. 

Połączenie usług zarządzania i wdrażania do wystąpienia zarządzanego przy użyciu [punkt końcowy zarządzania](sql-database-managed-instance-management-endpoint.md) mapuje zewnętrzny moduł równoważenia obciążenia. Ruch jest kierowany do węzłów, tylko wtedy, gdy odbierane na zestaw wstępnie zdefiniowanych portów, które są używane wyłącznie przez składniki zarządzania wystąpienia zarządzanego. Wbudowane zapory w węzłach jest skonfigurowana do zezwolenia na ruch tylko z określonych zakresów adresów IP firmy Microsoft. Cała komunikacja między składniki zarządzania i płaszczyzna zarządzania jest wzajemnie uwierzytelnienia certyfikatu. 

## <a name="next-steps"></a>Kolejne kroki 

- Aby uzyskać przegląd, zobacz [co to jest wystąpienie zarządzane](sql-database-managed-instance.md) 
- Aby uzyskać więcej informacji o konfiguracji sieci wirtualnej, zobacz [konfiguracja sieci wirtualnej wystąpienia zarządzanego](sql-database-managed-instance-vnet-configuration.md). 
- Aby uzyskać szybki Start zobacz sposób tworzenia wystąpienia zarządzanego: 
  - Z [witryny Azure portal](sql-database-managed-instance-get-started.md) 
  - za pomocą [programu PowerShell](https://blogs.msdn.microsoft.com/sqlserverstorageengine/2018/06/27/quick-start-script-create-azure-sql-managed-instance-using-powershell/) 
  - za pomocą [szablonu usługi Azure Resource Manager](https://azure.microsoft.com/resources/templates/101-sqlmi-new-vnet/) 
  - za pomocą [szablonu usługi Azure Resource Manager (Rampa za pomocą programu SSMS uwzględnione)](https://portal.azure.com/) 

