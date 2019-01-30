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
ms.date: 12/10/2018
ms.openlocfilehash: b709bbacce23a89b8c60b77a524018b50ca1ca5e
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/30/2019
ms.locfileid: "55245671"
---
# <a name="azure-sql-database-managed-instance-connectivity-architecture"></a>Usługa Azure SQL Database Managed architektura łączności wystąpienia

Ten artykuł zawiera omówienie komunikacji wystąpienia zarządzanego Azure SQL Database i architektura łączności także wyjaśnia, jak różnych składników funkcji do kierowania ruchu do wystąpienia zarządzanego.  

Azure SQL Database Managed Instance umieszcza się wewnątrz sieci wirtualnej platformy Azure i podsieć dedykowanego wystąpienia zarządzane przez usługę. To wdrożenie umożliwia następujące scenariusze: 
- Zabezpiecz prywatny adres IP.
- Połączenie do wystąpienia zarządzanego, bezpośrednio z siecią lokalną.
- Nawiązywanie połączenia z wystąpienia zarządzanego połączonego serwera lub innego lokalnego magazynu danych.
- Wystąpienie zarządzane nawiązywania połączenia z zasobami platformy Azure.

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
> Do poprawy jakości obsługi klienta i dostępności usług, Microsoft stosuje zasady intencji sieci elementy infrastruktury sieci wirtualnej platformy Azure, które mogą wpłynąć na działanie wystąpienia zarządzanego. Jest to mechanizm platformy do komunikowania się wymagania sieciowe w sposób niewidoczny dla użytkownika użytkownikom końcowym z głównym celem, aby zapobiec błędnej konfiguracji sieci i upewnij się, normalnej wystąpienia zarządzanego. Po usunięciu wystąpienia zarządzanego sieciowe przeznaczenie zasady zostaną usunięte także.

## <a name="virtual-cluster-connectivity-architecture"></a>Architektura łączności klaster wirtualny

Przyjrzyjmy się szczegółowo z wizualizacją w architekturze połączenia wystąpienia zarządzanego. Na poniższym diagramie przedstawiono koncepcyjny układ klaster wirtualny.

![klaster wirtualny diagram architektury łączności](./media/managed-instance-connectivity-architecture/connectivityarch003.png)

Klienci łączą się z wystąpieniem zarządzanym przy użyciu nazwy hosta, który ma postać z `<mi_name>.<dns_zone>.database.windows.net`. Ta nazwa hosta jest rozpoznawany jako prywatny adres IP jest zarejestrowany w publicznej strefie DNS, i jest rozpoznania publicznie. `zone-id` Jest generowana automatycznie po utworzeniu klastra. Jeśli nowo utworzonego klastra jest hostem dodatkowej wystąpienia zarządzanego, udostępnia Identyfikatora strefy za pomocą klastra podstawowego. Aby uzyskać więcej informacji, zobacz [grup automatyczny tryb failover](sql-database-auto-failover-group.md##enabling-geo-replication-between-managed-instances-and-their-vnets)

Ten prywatny adres IP należy do zarządzanego wystąpienia wewnętrznego obciążenia równoważenia (ILB) która kieruje ruch do bramy zarządzane wystąpienia (GW). Zgodnie z wewnątrz tego samego klastra, potencjalnie może uruchomić wiele wystąpień zarządzanych, GW używa nazwy hosta wystąpienia zarządzanego przekierowywanie ruchu na prawidłowe usługi aparatu programu SQL.

Połączenie usług zarządzania i wdrażania do wystąpienia zarządzanego przy użyciu [punkt końcowy zarządzania](#management-endpoint) mapuje zewnętrzny moduł równoważenia obciążenia. Ruch jest kierowany do węzłów, tylko wtedy, gdy odbierane na zestaw wstępnie zdefiniowanych portów, które są używane wyłącznie przez składniki zarządzania wystąpienia zarządzanego. Wbudowane zapory w węzłach jest skonfigurowana do zezwolenia na ruch tylko z określonych zakresów adresów IP firmy Microsoft. Cała komunikacja między składniki zarządzania i płaszczyzna zarządzania jest wzajemnie uwierzytelnienia certyfikatu.

## <a name="management-endpoint"></a>Punkt końcowy zarządzania

Klaster wirtualny wystąpienia zarządzanego Azure SQL Database zawiera punkt końcowy zarządzania, używane przez firmę Microsoft do zarządzania wystąpienia zarządzanego. Punkt końcowy zarządzania jest chroniony za pomocą wbudowanej zapory sieciowej poziomu i wzajemne certyfikat weryfikacji na poziomie aplikacji. Możesz [znaleźć adres ip punktu końcowego zarządzania](sql-database-managed-instance-find-management-endpoint-ip-address.md).

Gdy połączenia były inicjowane z wewnątrz wystąpienia zarządzanego (kopia zapasowa, dziennik inspekcji) wygląda na to, że ruch pochodzący z punktu końcowego zarządzania publicznego adresu IP. Można ograniczyć dostęp usług publicznych z wystąpieniem zarządzanym przez ustawienie reguły zapory zezwalające na tylko za pomocą adresu IP wystąpienia zarządzanego. Znajdź więcej informacji o metodzie, która może być [Sprawdź wbudowanej zapory wystąpienia zarządzanego](sql-database-managed-instance-management-endpoint-verify-built-in-firewall.md).

> [!NOTE]
> To nie ma zastosowania do ustawiania reguły zapory dla usług platformy Azure, które znajdują się w tym samym regionie, co wystąpienie zarządzane usługi platformy Azure ma optymalizacji dla ruchu, który przechodzi między usługami, które są zlokalizowana.

## <a name="network-requirements"></a>Wymagania dotyczące sieci

Wystąpienie zarządzane usługi można wdrażać w dedykowanej podsieci (podsieci wystąpienia zarządzanego) wewnątrz sieci wirtualnej, która spełnia następujące wymagania:
- **W wersji dedykowanej podsieci**: Podsieci wystąpienia zarządzanego nie może zawierać wszystkie inne usługi w chmurze skojarzone z nim, a nie może być podsieć bramy. Nie można utworzyć wystąpienie zarządzane w podsieci, która zawiera zasoby innych niż wystąpienia zarządzanego, a nie można później dodać innych zasobów w podsieci.
- **Sieciowa grupa zabezpieczeń (NSG) zgodnych**: Sieciowa grupa zabezpieczeń skojarzona z podsiecią wystąpienia zarządzanego musi zawierać reguły pokazano w poniższych tabelach (reguł zabezpieczeń ruchu przychodzącego obowiązkowe i reguły zabezpieczeń dla ruchu wychodzącego obowiązkowe) przed jakiekolwiek inne reguły. Sieciowa grupa zabezpieczeń można użyć w pełni kontrolować dostęp do endpoint danych wystąpienia zarządzanego, filtrując ruch na porcie 1433. 
- **Tabeli zgodnych tras zdefiniowanych przez użytkownika (UDR)**: Podsieci wystąpienia zarządzanego musi mieć tabelę tras użytkownika za pomocą **internetowej następnego przeskoku 0.0.0.0/0** jako obowiązkowe trasy zdefiniowanej przez użytkownika do niej przypisany. Ponadto możesz dodać trasy zdefiniowanej przez użytkownika tego kieruje ruch z zakresów IP prywatnych w środowisku lokalnym jako miejsce docelowe za pośrednictwem bramy sieci wirtualnej lub sieci wirtualne urządzenie sieciowe. 
- **Opcjonalne niestandardowe DNS**: Jeśli niestandardowe DNS jest określona w sieci wirtualnej, należy dodać adres IP platformy Azure cyklicznego programu rozpoznawania nazw (na przykład 168.63.129.16) do listy. Aby uzyskać więcej informacji, zobacz [Konfigurowanie niestandardowych serwerów DNS](sql-database-managed-instance-custom-dns.md). Niestandardowego serwera DNS musi być w stanie rozpoznać nazwy hostów w następujących domen i poddomen ich: *microsoft.com*, *windows.net*, *windows.com*, *msocsp.com*, *digicert.com*, *live.com*, *microsoftonline.com*, i *microsoftonline-p.com*. 
- **Brak punktów końcowych usługi**: Podsieci wystąpienia zarządzanego nie może mieć powiązany punktu końcowego usługi. Upewnij się, że opcji punktów końcowych usługi jest wyłączona podczas tworzenia sieci wirtualnej.
- **Wystarczającą liczbą adresów IP**: Podsieci wystąpienia zarządzanego jest posiadanie absolutnego minimum 16 adresów IP (zalecane są co najmniej 32 adresów IP). Aby uzyskać więcej informacji, zobacz [określi rozmiar podsieci wystąpienia zarządzanego](sql-database-managed-instance-determine-size-vnet-subnet.md). Można wdrożyć wystąpienia zarządzane przez usługę w [istniejąca sieć](sql-database-managed-instance-configure-vnet-subnet.md) po skonfigurowaniu go do zaspokojenia [wystąpienia zarządzanego, wymagania sieciowe](#network-requirements), lub Utwórz [nowej sieci i podsieci](sql-database-managed-instance-create-vnet-subnet.md).

> [!IMPORTANT]
> Nie można wdrożyć nowe wystąpienie zarządzane, jeśli podsieci docelowej nie jest zgodny ze wszystkimi te wymagania. Podczas tworzenia wystąpienia zarządzanego *sieciowe przeznaczenie zasady* została zastosowana w tej podsieci, aby uniemożliwić niezgodnych zmian do konfiguracji sieci. Po usunięciu ostatniego wystąpienia z podsieci, *sieciowe przeznaczenie zasady* zostanie także usunięta

### <a name="mandatory-inbound-security-rules"></a>Reguły zabezpieczeń ruchu przychodzącego obowiązkowe 

| Name       |Port                        |Protokół|Obiekt źródłowy           |Element docelowy|Akcja|
|------------|----------------------------|--------|-----------------|-----------|------|
|zarządzanie  |9000, 9003, 1438, 1440, 1452|TCP     |Dowolne              |Dowolne        |Zezwalaj |
|mi_subnet   |Dowolne                         |Dowolne     |PODSIECI WYSTĄPIENIA ZARZĄDZANEGO        |Dowolne        |Zezwalaj |
|health_probe|Dowolne                         |Dowolne     |AzureLoadBalancer|Dowolne        |Zezwalaj |

### <a name="mandatory-outbound-security-rules"></a>Reguły zabezpieczeń dla ruchu wychodzącego obowiązkowe 

| Name       |Port          |Protokół|Obiekt źródłowy           |Element docelowy|Akcja|
|------------|--------------|--------|-----------------|-----------|------|
|zarządzanie  |80, 443, 12000|TCP     |Dowolne              |Internet   |Zezwalaj |
|mi_subnet   |Dowolne           |Dowolne     |Dowolne              |PODSIECI WYSTĄPIENIA ZARZĄDZANEGO  |Zezwalaj |

  > [!Note]
  > PODSIECI wystąpienia Zarządzanego odnosi się do zakresu adresów IP dla podsieci w 10.x.x.x/y formularza. Te informacje można znaleźć w witrynie Azure portal (za pośrednictwem właściwości podsieci).
  
  > [!Note]
  > Chociaż zasady obowiązkowych zabezpieczeń dla ruchu przychodzącego zezwalającą na ruch z _wszelkie_ źródła na portach 9000, 9003, 1438, 1440, 1452 te porty są chronione przez zaporę wbudowanych. To [artykułu](sql-database-managed-instance-find-management-endpoint-ip-address.md) pokazuje, jak odnaleźć adres IP punktu końcowego zarządzania i sprawdź reguły zapory. 
  
  > [!Note]
  > Jeśli używasz replikacji transakcyjnej w wystąpieniu zarządzanym i każdej bazy danych w wystąpieniu zarządzanym usługi jest używany jako wydawca lub dystrybutora, port 445 (ruch wychodzący protokołu TCP) również musi być otwarte w regułach zabezpieczeń dla podsieci w celu uzyskania dostępu do udziału plików platformy Azure.
  
## <a name="next-steps"></a>Kolejne kroki

- Aby uzyskać przegląd, zobacz [co to jest wystąpienie zarządzane](sql-database-managed-instance.md)
- Dowiedz się, jak [skonfigurować nową sieć wirtualną](sql-database-managed-instance-create-vnet-subnet.md) lub [Konfigurowanie istniejącej sieci wirtualnej](sql-database-managed-instance-configure-vnet-subnet.md) którym można wdrożyć wystąpienia zarządzane przez usługę.
- [Oblicz się rozmiar podsieci](sql-database-managed-instance-determine-size-vnet-subnet.md) którym będą wdrażane wystąpienia zarządzane przez usługę. 
- Aby uzyskać szybki Start zobacz sposób tworzenia wystąpienia zarządzanego:
  - Z [witryny Azure portal](sql-database-managed-instance-get-started.md)
  - za pomocą [programu PowerShell](https://blogs.msdn.microsoft.com/sqlserverstorageengine/2018/06/27/quick-start-script-create-azure-sql-managed-instance-using-powershell/)
  - za pomocą [szablonu usługi Azure Resource Manager](https://azure.microsoft.com/resources/templates/101-sqlmi-new-vnet/)
  - za pomocą [szablonu usługi Azure Resource Manager (Rampa za pomocą programu SSMS uwzględnione)](https://portal.azure.com/)
