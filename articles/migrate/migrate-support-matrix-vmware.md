---
title: Obsługa oceny VMware w Azure Migrate
description: Dowiedz się więcej o obsłudze oceny VMware w Azure Migrate.
ms.topic: conceptual
ms.date: 01/08/2020
ms.openlocfilehash: 2a9c5504d99f439723a250b619b9f9b660ea9c59
ms.sourcegitcommit: dbcc4569fde1bebb9df0a3ab6d4d3ff7f806d486
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/15/2020
ms.locfileid: "76029000"
---
# <a name="support-matrix-for-vmware-assessment"></a>Macierz obsługi dla oceny oprogramowania VMware 

Ten artykuł podsumowuje ustawienia i ograniczenia dotyczące oceny maszyn wirtualnych VMware za pomocą [Azure Migrate: Ocena serwera](migrate-services-overview.md#azure-migrate-server-migration-tool). Jeśli szukasz informacji na temat migrowania maszyn wirtualnych VMware na platformę Azure, zapoznaj się z [macierzą obsługi migracji](migrate-support-matrix-vmware-migration.md).

## <a name="overview"></a>Przegląd

Aby ocenić maszyny lokalne na potrzeby migracji na platformę Azure z tego artykułu, należy dodać Azure Migrate: Narzędzie do oceny serwera do projektu Azure Migrate. Należy wdrożyć [urządzenie Azure Migrate](migrate-appliance.md). Urządzenie stale odnajduje maszyny lokalne i wysyła dane dotyczące konfiguracji i wydajności na platformę Azure. Po odnajdywaniu maszyn można zebrać odnalezione maszyny do grup i uruchomić ocenę dla grupy.


## <a name="limitations"></a>Ograniczenia

**Pomoc techniczna** | **Szczegóły**
--- | ---
**Limity oceny**| Odkrywaj i oceniaj do 35 000 maszyn wirtualnych VMware w jednym [projekcie](migrate-support-matrix.md#azure-migrate-projects).
**Limity projektu** | Możesz utworzyć wiele projektów w ramach subskrypcji platformy Azure. Projekt może obejmować maszyny wirtualne VMware, maszyny wirtualne funkcji Hyper-V i serwery fizyczne oraz limity oceny.
**Discovery** (Odnajdywanie) | Urządzenie Azure Migrate może wykryć do 10 000 maszyn wirtualnych VMware na vCenter Server.
**Ocena** | Można dodać do 35 000 maszyn w jednej grupie.<br/><br/> W ramach jednej oceny można ocenić do 35 000 maszyn wirtualnych.

[Dowiedz się więcej](concepts-assessment-calculation.md) na temat ocen.


## <a name="application-discovery"></a>Odnajdywanie aplikacji

Oprócz odnajdywania maszyn Azure Migrate: Ocena serwera może wykrywać aplikacje, role i funkcje działające na maszynach. Odnajdywanie spisu aplikacji umożliwia zidentyfikowanie i zaplanowanie ścieżki migracji dopasowanej do obciążeń lokalnych. 

**Pomoc techniczna** | **Szczegóły**
--- | ---
Odnajdywanie | Odnajdywanie jest bezagentem, przy użyciu poświadczeń gościa komputera i zdalnie uzyskuje dostęp do maszyn przy użyciu usług WMI i wywołań SSH.
Obsługiwane maszyny | Lokalne maszyny wirtualne programu VMware.
System operacyjny maszyny | Wszystkie wersje systemów Windows i Linux
Poświadczenia | Obecnie obsługuje użycie jednego poświadczenia dla wszystkich serwerów z systemem Windows i jedno poświadczenie dla wszystkich serwerów z systemem Linux.<br/><br/> Tworzysz konto użytkownika-gościa dla maszyn wirtualnych z systemem Windows oraz normalne/normalne konto użytkownika (dostęp sudo) dla wszystkich maszyn wirtualnych z systemem Linux.
Limity | W przypadku odnajdywania aplikacji można odkryć do 10000 na urządzenie. 

## <a name="vmware-requirements"></a>Wymagania dotyczące oprogramowania VMware

**VMware** | **Szczegóły**
--- | ---
**vCenter Server** | Komputery, które mają być odnajdywane i oceniane, muszą być zarządzane przez vCenter Server w wersji 5,5, 6,0, 6,5 lub 6,7.
**Uprawnienia (ocena)** | vCenter Server konto tylko do odczytu.
**Uprawnienia (Odnajdywanie aplikacji)** | konto vCenter Server z dostępem tylko do odczytu i przywileje włączone dla maszyn wirtualnych > operacji gościa.
**Uprawnienia (Wizualizacja zależności)** | Konto serwera centrum z dostępem tylko do odczytu oraz uprawnienia włączone dla **maszyn wirtualnych** > **operacji gościa**.


## <a name="azure-migrate-appliance-requirements"></a>Wymagania dotyczące urządzenia Azure Migrate

Azure Migrate używa [urządzenia Azure Migrate](migrate-appliance.md) do odnajdowania i oceny. Urządzenie dla oprogramowania VMware jest wdrażane przy użyciu szablonu komórki jajowe zaimportowanego do vCenter Server. 

- Dowiedz się więcej o [wymaganiach dotyczących urządzeń](migrate-appliance.md#appliance---vmware) w oprogramowaniu VMware.
- Informacje o [adresach URL](migrate-appliance.md#url-access) , do których urządzenie musi uzyskać dostęp.

## <a name="port-access"></a>Dostęp do portu

**urządzenia** | **Połączenie**
--- | ---
Wprowadzony | Połączenia przychodzące na porcie TCP 3389, aby zezwolić na połączenia pulpitu zdalnego z urządzeniem.<br/><br/> Połączenia przychodzące na porcie 44368 do zdalnego dostępu do aplikacji do zarządzania urządzeniami przy użyciu adresu URL: ```https://<appliance-ip-or-name>:44368``` <br/><br/>Połączenia wychodzące na porcie 443, 5671 i 5672 do wysyłania metadanych odnajdywania i wydajności do Azure Migrate.
Serwer vCenter | Połączenia przychodzące na porcie TCP 443 umożliwiające urządzeniu zbieranie metadanych dotyczących konfiguracji i wydajności dla ocen. <br/><br/> Urządzenie domyślnie łączy się z programem vCenter na porcie 443. Jeśli serwer vCenter nasłuchuje na innym porcie, można zmodyfikować port podczas konfigurowania odnajdywania.

## <a name="dependency-visualization"></a>Wizualizacja zależności

Wizualizacja zależności ułatwia wizualizację zależności między maszynami, które mają zostać poddane ocenie i przeprowadzeniem migracji. Mapowanie zależności zazwyczaj jest stosowane, gdy chcesz ocenić komputery z wyższym poziomem zaufania. W przypadku maszyn wirtualnych VMware, Wizualizacja zależności jest obsługiwana w następujący sposób:

- **Wizualizacja zależności bez agenta**: Ta opcja jest obecnie dostępna w wersji zapoznawczej. Nie wymaga to instalowania żadnych agentów na komputerach.
    - Działa przez przechwytywanie danych połączenia TCP z maszyn, dla których jest włączona. Po rozpoczęciu odnajdywania zależności urządzenie zbiera dane z maszyn z interwałem sondowania równym pięć minut.
    - Zbierane są następujące dane:
        - Połączenia protokołu TCP
        - Nazwy procesów, które mają aktywne połączenia
        - Nazwy zainstalowanych aplikacji, które uruchamiają powyższe procesy
        - Nie. wykrytych połączeń podczas każdego interwału sondowania
- **Wizualizacja zależności oparta na agentach**: Aby użyć wizualizacji zależności opartej na agentach, należy pobrać i zainstalować następujących agentów na każdej maszynie lokalnej, która ma zostać przeanalizowana.
    - Zainstaluj program Microsoft Monitoring Agent (MMA) na każdym komputerze. [Dowiedz się więcej](how-to-create-group-machine-dependencies.md#install-the-mma) na temat sposobu instalowania agenta MMA.
    - Zainstaluj agenta zależności na każdym komputerze. [Dowiedz się więcej](how-to-create-group-machine-dependencies.md#install-the-dependency-agent) na temat sposobu instalowania agenta zależności.
    - Ponadto w przypadku maszyn, które nie są połączone z Internetem, należy pobrać i zainstalować na nich bramę usługi Log Analytics.


## <a name="next-steps"></a>Następne kroki

- [Zapoznaj](best-practices-assessment.md) się z najlepszymi rozwiązaniami dotyczącymi tworzenia ocen.
- [Przygotowanie do](tutorial-prepare-vmware.md) oceny oprogramowania VMware.
