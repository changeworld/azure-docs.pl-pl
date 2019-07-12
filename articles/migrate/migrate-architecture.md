---
title: Architektura usługi Azure Migrate | Dokumentacja firmy Microsoft
description: Ten artykuł zawiera omówienie usługi Azure Migrate.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 07/01/2019
ms.author: raynew
ms.openlocfilehash: 16fbf8d3523ac2ab36447aa51a93a0bb5a9fad54
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/11/2019
ms.locfileid: "67811429"
---
# <a name="azure-migrate-architecture-and-processes"></a>Architektura usługi Azure Migrate i procesów

[Usługa Azure Migrate](migrate-overview.md) udostępnia Centrum narzędzi, które ułatwiają odnajdowanie, oceny i migracji aplikacji, infrastruktury i obciążeń w systemie Microsoft Azure. Centrum obejmuje usługę Azure Migrate narzędzi i ofert Niezależnym dostawcą oprogramowania niezależnie od innych firm. 

 Ten artykuł zawiera podsumowanie oceny i architekturę migracji oraz procesów dla oceny Server migracji platformy Azure i usługi Azure Migration migracji serwera.

## <a name="assessment-with-azure-migrate-server-assessment"></a>Oceny za pomocą usługi Azure migracji serwera oceny

Azure oceny migracji serwera można ocenić maszyny wirtualne funkcji Hyper-V i maszyn wirtualnych VMware do migracji na platformę Azure. Ocena działa w następujący sposób:

1. **Przygotowywanie oceny**: W środowisku lokalnym, możesz skonfigurować lekkie usługa Azure Migrate urządzenia lokalnego jako maszyna wirtualna VMware lub maszyny Wirtualnej funkcji Hyper-V i zarejestrować urządzenie z usługi Azure Migrate.
2. **Odnajdywanie maszyn wirtualnych**: Uruchamia urządzenie Azure Migrate do odnajdowania lokalnych maszyn wirtualnych. 
    - Niczego nie trzeba zainstalować na wykrytych maszynach wirtualnych.
    - Metadane maszyny Wirtualnej zawiera informacje dotyczące rdzeni, pamięć, dyski, rozmiary dysków i kart sieciowych.
    - Dane dotyczące wydajności, zawiera informacje dotyczące procesora CPU i pamięci, użycie dysku na SEKUNDĘ, przepływności dysku (MB/s) i sieci danych wyjściowych (MB/s)
- **Zbieranie i ocenić maszyny**: Po zakończeniu odnajdowania, w witrynie Azure portal, które zostały zebrane odnalezione maszyny wirtualne w grupach, które składają się zazwyczaj z maszyn wirtualnych, które będą migrowane razem. W grupie, możesz uruchomić ocenę.


## <a name="vmware-assessment"></a>Ocena oprogramowania VMware 

Diagram znajduje się podsumowanie sposobu działania oceny maszyn wirtualnych VMware przy użyciu oceny Server migracji platformy Azure.

![Architekturę do oceny programu VMware](./media/migrate-architecture/sas-architecture-vmware.png)

Proces przebiega w następujący sposób:

1. **Wdrażanie urządzenia usługi Azure Migrate**:

    a. Pobierz szablon (OVA) w witrynie Azure portal.

    b. Zaimportuj go na komputerze serwera vCenter, aby utworzyć maszynę Wirtualną.

    c. Nawiązać połączenie z tą maszyną Wirtualną, skonfigurowania podstawowych ustawień dla niej i zarejestruj je przy użyciu usługi Azure Migrate.

2. **Inicjowanie odnajdywania**:

    a. Nawiązać jest uruchomiona na urządzeniu, aby zainicjować odnajdywanie aplikacja modułu zbierającego.

    b. Urządzenie stale wysyła dane wydajności i metadanych z maszyn wirtualnych na platformie Azure.

    - Urządzenia zawsze jest połączony z usługą Azure Migrate.
    - Zmiany środowiska podczas odnajdywania ciągłego są przechwytywane. Na przykład dodając maszyny wirtualne w zakresie odnajdowania lub dodanie dysków maszyny Wirtualnej lub karty sieciowe.

3. **Ocenę maszyn**:

    a. Po zakończeniu odnajdowania, w witrynie Azure portal, które zostały zebrane odnalezione maszyny wirtualne do grupy.  Grupa zwykle składa się z maszyn wirtualnych, które będą migrowane razem.

    b. Uruchom ocenę dla każdej grupy.

4. **Przejrzyj oceny**: 

    a. Po zakończeniu oceny, można go wyświetlić w portalu.

    b. W celu dalszej analizy Pobierz oceny w formacie programu Excel.



### <a name="vmware-ports"></a>Porty programu VMware
Usługa Azure Migrate używa następujących portów połączeń dla oprogramowania VMware:

**Element źródłowy** | **Docelowy** | **Port** | **Szczegóły**
--- | --- | --- | ---
Urządzenie Azure Migrate | Usługa Azure Migrate | Target-TCP 443 | Wysyłanie danych metadanych i wydajności do usługi Azure Migrate.
Urządzenie Azure Migrate | Program vCenter Server | Target-TCP 443 | Nawiązać połączenia danych metadanych i wydajności programu vCenter Server. jest domyślnie 443, ale mogą być modyfikowane przy użyciu vCenter nasłuchuje na innym porcie. 
Klient protokołu RDP | Urządzenie Azure Migrate | Target-TCP3389 | Połączenie RDP z wyzwalacza odnajdywania przez urządzenie.

### <a name="collected-vmware-metadata"></a>Zebrano metadane programu VMware

Urządzenie wysyła metadane i dane dotyczące wydajności z maszyn wirtualnych na platformie Azure.

**Akcja** | **Szczegóły**
--- | ---
**Zebrano metadane** | Nazwa maszyny Wirtualnej vCenter<br/> Ścieżka maszyny Wirtualnej vCenter (host/klaster folder)<br/> Adresy IP i MAC<br/> System operacyjny<br/> Liczba rdzeni/dysków/kart sieciowych<br/> Rozmiar pamięci i dysku.
**Dane dotyczące wydajności zebrane** | Użycie Procesora i pamięci<br/> Na dysku danych (przepływność odczytu/zapisu na dysku, dysku operacji odczytu/zapisu na sekundę)<br/> Dane kart Sieciowych (network w sieci w poziomie).<br/><br/> Dane wydajności są zbierane, stale w przypadku, gdy urządzenie połączy się programem vCenter Server. Dane historyczne nie zostaną zebrane.

## <a name="hyper-v-assessment"></a>Ocena funkcji Hyper-V

Diagram zawiera podsumowanie, jak działa oceny funkcji Hyper-V odtwarzać oceny Server migracji platformy Azure.

![Architekturę do oceny funkcji Hyper-V](./media/migrate-architecture/sas-architecture-hyper-v.png)

Proces przebiega w następujący sposób:

1. **Utwórz urządzenie w usłudze Azure Migrate**:

    a. Pobierz maszyny Wirtualnej w formacie skompresowane w witrynie Azure portal.

    b. Zaimportuj go do hosta funkcji Hyper-V.

    c. Połącz urządzenia, dla maszyny Wirtualnej. Konfigurowanie ustawień podstawowych dla niej i zarejestruj je przy użyciu usługi Azure Migrate.

2. **Inicjowanie odnajdywania**:

    a. Połącz urządzenia usługi Azure Migrate, aby zainicjować odnajdywanie. Niczego nie trzeba zainstalować na wykrytych maszynach wirtualnych.

    b. Urządzenie ciągle wysyła dane wydajności i metadanych maszyny Wirtualnej do usługi Azure Migrate.

    - Urządzenie jest zawsze połączony z usługą Azure Migrate (przy użyciu sesji modelu wspólnych informacji).
    - Zmiany środowiska podczas odnajdywania ciągłego są przechwytywane. Na przykład dodając maszyny wirtualne w zakresie odnajdowania lub dodanie dysków maszyny Wirtualnej lub karty sieciowe.


3. **Ocenę maszyn**:

    a. Po zakończeniu odnajdowania, w witrynie Azure portal, które zostały zebrane odnalezione maszyny wirtualne do grupy.  Grupa zwykle składa się z maszyn wirtualnych, które będą migrowane razem.

    b. Uruchom ocenę dla każdej grupy.

4. **Przejrzyj oceny**:

    a. Po zakończeniu oceny, można go wyświetlić w portalu.

    b. W celu dalszej analizy Pobierz oceny w formacie programu Excel.

### <a name="hyper-v-ports"></a>Portów funkcji Hyper-V

Usługa Azure Migrate używa następujących portów połączeń dla funkcji Hyper-V:

**Element źródłowy** | **Docelowy** | **Port** | **Szczegóły**
--- | --- | --- | ---
Urządzenie Azure Migrate | Usługa Azure Migrate | Target-TCP 443 | Wysyłanie danych metadanych i wydajności do usługi Azure Migrate.
Urządzenie Azure Migrate | Klaster hostów funkcji Hyper-V | Target-Default WinRM ports-HTTP:5985; HTTPS:5986 | Połączyć się z hostem dla danych metadanych i wydajności. Sesji modelu wspólnych informacji używanego na potrzeby połączenia
Klient protokołu RDP | Urządzenie Azure Migrate | Target-TCP3389 | Połączenie RDP z wyzwalacza odnajdywania przez urządzenie.

## <a name="collected-hyper-v-vm-metadata"></a>Zebrane metadane maszyny Wirtualnej funkcji Hyper-V

Urządzenie wysyła metadane i dane dotyczące wydajności z maszyn wirtualnych na platformie Azure.


**Akcja** | **Szczegóły**
--- | ---
**Zebrano metadane** | Nazwa maszyny wirtualnej<br/> Ścieżka maszyny Wirtualnej (host/klaster folder)<br/> Adresy IP i MAC<br/> System operacyjny<br/> Liczba rdzeni/dysków/kart sieciowych<br/> Rozmiar pamięci i dysku<br/> Dysku na SEKUNDĘ, przepływności dysku (MB/s), dane wyjściowe sieci (MB/s)
**Dane dotyczące wydajności zebrane** |  Użycie Procesora i pamięci<br/> Na dysku danych (przepływność odczytu/zapisu na dysku, dysku operacji odczytu/zapisu na sekundę)<br/> Dane kart Sieciowych (network w sieci w poziomie).<br/><br/> Dane wydajności są zbierane, stale w przypadku, gdy urządzenie połączy się z hostem funkcji Hyper-V. Dane historyczne nie zostaną zebrane.




## <a name="migration-with-azure-migrate-server-migration"></a>Migracja z platformą Azure migracji serwera

Za pomocą usługi Azure Migration migracji serwera, można migrować następujące czynności na platformie Azure:

- Lokalne maszyny wirtualne VMware
- Maszyny wirtualne funkcji Hyper-V w środowisku lokalnym
- Lokalne serwery fizyczne
- Wystąpienia maszyn wirtualnych Windows usług AWS
- Wystąpienia maszyn wirtualnych Windows GCP

Proces migracji różni się nieznacznie, w zależności, które przenosisz.


## <a name="migrate-vmware-vms"></a>Migrowanie maszyn wirtualnych VMware

Migrowanie serwera migracji na platformę Azure udostępnia dwie metody do migrowania lokalnych maszyn wirtualnych programu VMware:

- **Bez wykorzystania agentów replikacji**: Migrowanie maszyn wirtualnych bez konieczności instalowania żadnych narzędzi na nich.
- **Replikacja oparta na agenta**. Zainstaluj agenta na maszynie Wirtualnej w celu replikacji.

Mimo że łatwiej z punktu widzenia wdrażania bez agentów replikacji obecnie ma kilka ograniczeń. [Dowiedz się więcej](server-migrate-overview.md) o tych ograniczeniach.
 

### <a name="agent-based-migration"></a>Migracji opartej o agentów

Oparte na agentach migracji maszyn wirtualnych programu VMware cyfrę składników do wdrożenia, zgodnie z opisem w tabeli.

**Składnik** | **Wymaganie** | **Szczegóły**
--- | --- | ---
**Konfiguracja komputera serwera** | Pojedynczy lokalnej maszyny Wirtualnej VMware, który jest wdrażany z pobranego szablonu OVF.<br/><br/> Komputer uruchamia wszystkie składniki usługi Site Recovery w środowisku lokalnym, które obejmują serwer konfiguracji i serwerem przetwarzania. | **Serwer konfiguracji**: Służy do koordynowania komunikacji między lokalną i platformą Azure oraz do zarządzania replikacją danych.<br/><br/> **Serwer przetwarzania**: Domyślnie instalowany na serwerze konfiguracji. Odbiera dane replikacji; optymalizuje je przy użyciu pamięci podręcznej, kompresji i szyfrowania; i wysyła je do platformy Azure. Również serwer przetwarzania instaluje usługę mobilności usługi Azure Site Recovery na maszynach wirtualnych i przeprowadza automatyczne odnajdywanie maszyn lokalnych.
**Usługa mobilności** | Usługa Mobility musi być zainstalowana na każdej maszynie Wirtualnej VMware są replikowane. | Zaleca się, że zezwalają na automatycznej instalacji z serwera przetwarzania. Można też ręcznie zainstalować usługę, lub użyć metody wdrażania automatycznego, takie jak System Center Configuration Manager.





### <a name="agent-based-replication-process"></a>Proces replikacji opartej o agentów

![Proces replikacji](./media/migrate-architecture/v2a-architecture-henry.png)

1. Po włączeniu replikacji dla maszyny Wirtualnej, rozpoczyna się Replikacja początkowa na platformie Azure. 
    - Replikacja jest na poziomie bloku, w pobliżu ciągłe, przy użyciu agenta usługi mobilności, które są uruchomione na maszynie Wirtualnej.
    - Ustawienia zasad replikacji są stosowane:
        - **Próg celu punktu odzyskiwania**. To ustawienie nie wpływa replikacji. Pomaga przy użyciu funkcji monitorowania. Zdarzenie jest zgłaszane, a opcjonalnie wysłana wiadomość e-mail, jeśli bieżący cel punktu odzyskiwania przekracza próg limitu, który określisz.
        - **Czas przechowywania punktu odzyskiwania**. To ustawienie określa, jak daleko wstecz w czasie, który ma nastąpić przejście po wystąpieniu przerwy w działaniu. Maksymalny czas przechowywania w magazynie premium storage wynosi 24 godziny. W magazynie standard storage to 72 godziny. 
        - **Migawki spójne z aplikacji**. Migawka spójności aplikacji może potrwać co 1 do 12 godzin, zależnie od potrzeb aplikacji. Migawki są migawki standardowych obiektów blob platformy Azure. Agenta mobilności uruchomionego na maszynie Wirtualnej żąda migawkę usługi VSS zgodnie z tego ustawienia i zakładki wskazujące punkt w czasie jako spójne na poziomie aplikacji w usłudze stream replikacji.

2. Ruch są replikowane do usługi Azure storage publicznych punktów końcowych za pośrednictwem Internetu.

    - Alternatywnie można użyć usługi Azure ExpressRoute za pomocą [komunikacji równorzędnej firmy Microsoft](../expressroute/expressroute-circuit-peerings.md#microsoftpeering).
    - Replikowanie ruchu za pośrednictwem lokacja lokacja wirtualnej sieci prywatnej (VPN) z lokacji lokalnej do platformy Azure nie jest obsługiwane.
3. Po zakończeniu replikacji początkowej rozpoczyna się replikacja zmian różnicowych do platformy Azure. Śledzone zmiany dla maszyny są wysyłane do serwera przetwarzania.
2. Komunikacja odbywa się w następujący sposób:

    - Maszyny wirtualne komunikować się z lokalnym serwerem konfiguracji na porcie HTTPS 443 dla ruchu przychodzącego na potrzeby zarządzania replikacją.
    - Serwer konfiguracji organizuje replikację za pomocą platformy Azure za pośrednictwem portu HTTPS 443 dla ruchu wychodzącego.
    - Maszyny wirtualne wysyłają dane replikacji do serwera przetwarzania (uruchomionego na komputerze serwera konfiguracji) na porcie HTTPS 9443 dla ruchu przychodzącego. Ten port może być modyfikowany.
    - Serwer przetwarzania odbiera dane replikacji, optymalizuje je szyfruje i wysyła je do usługi Azure storage za pośrednictwem portu 443 wychodzących.
5. Dane replikacji rejestruje pierwszy ziemi na koncie magazynu pamięci podręcznej na platformie Azure. Te dzienniki są przetwarzane i dane są przechowywane na platformie Azure dysku zarządzanego (dysk inicjatora usługi Azure Site Recovery). Punkty odzyskiwania są tworzone na tym dysku.







## <a name="next-steps"></a>Kolejne kroki

- [Przejrzyj często zadawane pytania](resources-faq.md) dotyczące usługi Azure Migrate.
- Aby uzyskać pomoc od społeczności, odwiedź stronę [forum MSDN usługi Azure migracji](https://social.msdn.microsoft.com/Forums/home?forum=AzureMigrate&filter=alltypes&sort=lastpostdesc) lub [Stack Overflow](https://stackoverflow.com/search?q=azure+migrate).

