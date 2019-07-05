---
title: Architektura replikacji Azure – Azure w usłudze Azure Site Recovery | Dokumentacja firmy Microsoft
description: Ten artykuł zawiera omówienie składników i architektury używanych podczas konfigurowania odzyskiwania po awarii między regionami platformy Azure dla maszyn wirtualnych platformy Azure, za pomocą usługi Azure Site Recovery.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 05/30/2019
ms.author: raynew
ms.openlocfilehash: 9f985260175e5f54a17799ef07b3a280f42b716e
ms.sourcegitcommit: ac1cfe497341429cf62eb934e87f3b5f3c79948e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/01/2019
ms.locfileid: "67491886"
---
# <a name="azure-to-azure-disaster-recovery-architecture"></a>Architektura odzyskiwania po awarii z platformy Azure do platformy Azure


W tym artykule opisano architektury, składników i procesów związanych z wdrożeniem odzyskiwanie po awarii dla maszyn wirtualnych (VM) przy użyciu [usługi Azure Site Recovery](site-recovery-overview.md) usługi. Za pomocą Konfigurowanie odzyskiwania po awarii maszyn wirtualnych platformy Azure stale replikować z inną docelową w regionie. Jeśli wystąpi awaria, można przełączać awaryjnie maszyny wirtualne w regionie pomocniczym i uzyskiwać do nich dostęp z tego miejsca. Gdy wszystko działa normalnie ponownie, możesz powrotu po awarii i kontynuować pracę w lokalizacji podstawowej.



## <a name="architectural-components"></a>Składniki architektury

Składniki biorące udział w odzyskiwanie po awarii dla maszyn wirtualnych platformy Azure są podsumowane w poniższej tabeli.

**Składnik** | **Wymagania**
--- | ---
**Maszyny wirtualne w regionie źródłowym** | Jeden więcej maszyn wirtualnych platformy Azure w [obsługiwany region źródła](azure-to-azure-support-matrix.md#region-support).<br/><br/> Maszyn wirtualnych może działać dowolny [obsługiwanym systemie operacyjnym](azure-to-azure-support-matrix.md#replicated-machine-operating-systems).
**Źródło magazynu maszyny Wirtualnej** | Maszyny wirtualne platformy Azure mogą być zarządzane lub mieć — managed disks rozprzestrzeniające się na kontach magazynu.<br/><br/>[Dowiedz się więcej o](azure-to-azure-support-matrix.md#replicated-machines---storage) obsługiwane usługi Azure storage.
**Źródłowej sieci maszyny Wirtualnej** | Maszyny wirtualne mogą znajdować się w co najmniej jednej podsieci w sieci wirtualnej (VNet) w regionie źródłowym. [Dowiedz się więcej](azure-to-azure-support-matrix.md#replicated-machines---networking) o wymagania sieciowe.
**Konto magazynu pamięci podręcznej** | Wymagane jest konto magazynu pamięci podręcznej w źródłowej sieci. Podczas replikacji zmian dotyczących maszyn wirtualnych są przechowywane w pamięci podręcznej przed wysłaniem do docelowego konta magazynu.  Konta magazynu pamięci podręcznej musi być Standard.<br/><br/> Używanie pamięci podręcznej zapewnia minimalny wpływ na aplikacje produkcyjne, które są uruchomione na maszynie Wirtualnej.<br/><br/> [Dowiedz się więcej](azure-to-azure-support-matrix.md#cache-storage) o wymaganiach dotyczących magazynu pamięci podręcznej. 
**Zasoby docelowe** | Zasoby docelowe są używane podczas replikacji, a po przejściu do trybu failover. Usługa Site Recovery można skonfigurować domyślne zasobu docelowego lub można utworzyć/je dostosować.<br/><br/> W regionie docelowym Sprawdź, czy jesteś w stanie tworzyć maszyny wirtualne i czy Twoja subskrypcja ma za mało zasobów do obsługi rozmiarów maszyn wirtualnych, które będą potrzebne w regionie docelowym. 

![Źródło i cel replikacji](./media/concepts-azure-to-azure-architecture/enable-replication-step-1.png)

## <a name="target-resources"></a>Zasoby docelowe

Po włączeniu replikacji dla maszyny Wirtualnej Usługa Site Recovery oferuje opcję tworzenia zasobów docelowych automatycznie. 

**Zasób docelowy** | **Ustawienie domyślne**
--- | ---
**Subskrypcja docelowa** | Taka sama jak subskrypcja źródłowa.
**Docelowa grupa zasobów** | Grupa zasobów, do której maszyny wirtualne należą po włączeniu trybu failover.<br/><br/> Może być w dowolnym regionie systemu Azure, z wyjątkiem regionu źródłowego.<br/><br/> Usługa Site Recovery tworzy nową grupę zasobów w regionie docelowym, sufiks "asr".<br/><br/>
**Docelowej sieci wirtualnej** | Sieć wirtualna (VNet) w którym replikowane maszyny wirtualne znajdują się po włączeniu trybu failover. Mapowanie sieci jest tworzona między sieciami wirtualnymi na źródłowym i docelowym i na odwrót.<br/><br/> Site Recovery tworzy sieć wirtualną i podsieć, nowy sufiks "asr".
**Docelowe konto magazynu** |  Jeśli maszyna wirtualna nie używa dysku zarządzanego, jest to konto magazynu, do którego dane są replikowane.<br/><br/> Usługa Site Recovery tworzy nowe konto magazynu w regionie docelowym w celu utworzenia duplikatów źródłowego konta magazynu.
**Dyski zarządzane repliki** | Jeśli maszyna wirtualna używa dysku zarządzanego, jest to dysków zarządzanych, do których dane są replikowane.<br/><br/> Usługa Site Recovery tworzy dyski zarządzane repliki w regionie magazynu, które jest duplikatem źródłowego.
**Docelowe zestawy dostępności** |  Zestaw dostępności, w którą replikowane maszyny wirtualne znajdują się po włączeniu trybu failover.<br/><br/> Usługa Site Recovery tworzy zestaw dostępności w regionie docelowym sufiks "asr" dla maszyn wirtualnych, które znajdują się w zestaw dostępności w lokalizacji źródłowej. Jeśli zestaw dostępności, jest używany i nie jest tworzony nowy.
**Strefy dostępności docelowego** | Jeśli region docelowy obsługuje stref dostępności, Usługa Site Recovery przypisuje ten sam numer strefy, który jest używany w regionie źródłowym.

### <a name="managing-target-resources"></a>Zarządzanie zasobami docelowego

Zasoby docelowe można zarządzać w następujący sposób:

- Jak włączyć replikację, można zmodyfikować ustawienia obiektu docelowego.
- Po już działa replikacja, można zmodyfikować ustawienia obiektu docelowego. Wyjątkiem jest typ dostępności (SIS, zestaw lub strefy). Aby zmienić to ustawienie, należy wyłączyć z replikacji, zmodyfikować ustawienia, a następnie ponownie włączyć.



## <a name="replication-policy"></a>Zasady replikacji 

Po włączeniu replikacji maszyny Wirtualnej platformy Azure, domyślnie Usługa Site Recovery tworzy nowe zasady replikacji z ustawieniami domyślnymi podsumowane w tabeli.

**Ustawienie zasad** | **Szczegóły** | **Domyślne**
--- | --- | ---
**Czas przechowywania punktu odzyskiwania** | Określa, jak długo usługa Site Recovery przechowuje punkty odzyskiwania | 24 godziny
**Częstotliwość migawek spójności aplikacji** | Jak często Site Recovery tworzy migawkę spójną z aplikacji. | Co 60 minut.

### <a name="managing-replication-policies"></a>Zarządzanie zasadami replikacji

Możesz zarządzać i zmodyfikuj domyślne ustawienia zasad replikacji w następujący sposób:
- Można zmodyfikować ustawienia, jak włączyć replikację.
- Możesz utworzyć zasady replikacji w dowolnym momencie i zastosować je po włączeniu replikacji.

### <a name="multi-vm-consistency"></a>Spójność wielu maszyn wirtualnych

Jeśli chcesz, aby maszyny wirtualne replikowane wspólnie i udostępnione spójnego na poziomie awarii i punktów odzyskiwania zapewniających spójność aplikacji w trybie failover, można gromadzić je razem w grupie replikacji. Spójność wielu maszyn wirtualnych ma wpływ na wydajność obciążenia i należy używać tylko dla maszyn wirtualnych uruchomionych obciążeń wymagających spójności na wszystkich maszynach. 



## <a name="snapshots-and-recovery-points"></a>Migawki i punktów odzyskiwania

Punkty odzyskiwania są tworzone na podstawie migawki dysków maszyny Wirtualnej pod kątem określonego punktu w czasie. Po przełączeniu w tryb failover maszyny Wirtualnej umożliwia punkt odzyskiwania Przywróć maszynę Wirtualną w lokalizacji docelowej.

Podczas przechodzenia w tryb failover, zazwyczaj chcemy upewnić się, że maszyna wirtualna zacznie bez uszkodzenia lub utraty danych i że dane maszyny Wirtualnej są spójne, systemu operacyjnego i aplikacji działających na maszynie Wirtualnej. To zależy od typu migawek.

Usługa Site Recovery wykonuje migawki w następujący sposób:

1. Site Recovery tworzy migawki spójne na poziomie awarii danych domyślnie i migawek spójności aplikacji, jeśli określisz częstotliwość ich.
2. Punkty odzyskiwania są tworzone na podstawie migawki, a także przechowywane zgodnie z ustawieniami przechowywania w zasadach replikacji.

### <a name="consistency"></a>Spójność

W poniższej tabeli opisano różne typy spójności.

### <a name="crash-consistent"></a>Spójne na poziomie awarii

**Opis** | **Szczegóły** | **Zalecenie**
--- | --- | ---
Migawki spójne awarii przechwytuje dane na dysku podczas migawka została utworzona. Nie zawiera żadnych w pamięci.<br/><br/> Zawiera ona odpowiednikiem dane na dysku, które będą obecne, jeśli maszyna wirtualna wystąpiła awaria lub przewód zasilający została ściągnięta z serwera na moment, że migawka została utworzona.<br/><br/> Spójne na poziomie awarii nie gwarantuje spójności danych dla systemu operacyjnego lub aplikacji na maszynie Wirtualnej. | Usługa Site Recovery tworzy punkty odzyskiwania spójne na poziomie awarii co pięć minut domyślnie. Nie można zmodyfikować to ustawienie.<br/><br/>  | Obecnie większość aplikacji może odzyskać dobrze punktów spójnych awaryjnie.<br/><br/> Punkty odzyskiwania spójne na poziomie awarii są zwykle wystarczające do replikacji systemów operacyjnych i aplikacji, takich jak serwery DHCP i serwery wydruku.

### <a name="app-consistent"></a>App-consistent

**Opis** | **Szczegóły** | **Zalecenie**
--- | --- | ---
Punkty odzyskiwania spójne na poziomie aplikacji są tworzone na podstawie migawek spójności aplikacji.<br/><br/> Migawka spójności aplikacji zawierają wszystkie informacje w migawce spójnej na poziomie awarii, a także wszystkie dane w pamięci i transakcje w toku. | Migawki spójne z aplikacji należy użyć usługi kopiowania woluminów w tle (VSS):<br/><br/>   (1) po zainicjowaniu migawki VSS operacja kopii przy zapisie (krowy) na woluminie.<br/><br/>   (2) przed wykonaniem krowy, VSS informuje o każdej aplikacji na komputerze który musi opróżniania swoje dane rezydentny na dysku.<br/><br/>   (3) Usługa VSS następnie umożliwia aplikacji odzyskiwania kopii zapasowej/po awarii (w tym przypadku Usługa Site Recovery) odczytuje dane migawki i kontynuować. | Migawki spójne z aplikacji są pobierane zgodnie z częstotliwością, które określisz. Tę częstotliwość zawsze powinna być mniejsza niż ustawiony przechowywania punktów odzyskiwania. Na przykład możesz zachować punkty odzyskiwania, za pomocą domyślne ustawienie 24 godziny, należy ustawić częstotliwość mniej niż 24 godziny.<br/><br/>Są one bardziej złożonych i potrwać dłużej niż migawki spójne na poziomie awarii.<br/><br/> Wpływają na wydajność aplikacji działających na maszynie Wirtualnej włączona replikacja. 

## <a name="replication-process"></a>Proces replikacji

Po włączeniu replikacji dla maszyny Wirtualnej platformy Azure, są następujące konsekwencje:

1. Rozszerzenie usługi Site Recovery Mobility jest automatycznie instalowany na maszynie Wirtualnej.
2. Rozszerzenie rejestruje maszyny Wirtualnej z usługą Site Recovery.
3. Ciągłej replikacji rozpoczyna się dla maszyny Wirtualnej.  Zapisy na dysku, od razu są przenoszone do konta magazynu pamięci podręcznej w lokalizacji źródłowej.
4. Usługa Site Recovery przetwarza dane w pamięci podręcznej i wysyła je do docelowego konta magazynu lub do repliki usługi managed disks.
5. Po przetworzeniu danych punkty odzyskiwania spójne na poziomie awarii są generowane co pięć minut. Punkty odzyskiwania spójne na poziomie aplikacji są generowane, zgodnie z ustawieniem określonym w zasadach replikacji.

![Włączanie replikacji procesu, krok 2](./media/concepts-azure-to-azure-architecture/enable-replication-step-2.png)

**Proces replikacji**

## <a name="connectivity-requirements"></a>Wymagania dotyczące łączności

 Maszyny wirtualne platformy Azure, które są replikowane muszą łączności wychodzącej. Usługa Site Recovery nigdy nie wymaga łączności dla ruchu przychodzącego do maszyny Wirtualnej. 

### <a name="outbound-connectivity-urls"></a>Połączenia ruchu wychodzącego (URL)

Jeśli dostęp ruchu wychodzącego dla maszyn wirtualnych jest kontrolowany przy użyciu adresów URL, Zezwól na te adresy URL.

| **Adres URL** | **Szczegóły** |
| ------- | ----------- |
| *.blob.core.windows.net | Umożliwia zapisanie danych z maszyny wirtualnej na koncie magazynu pamięci podręcznej znajdującym się w regionie źródłowym. |
| login.microsoftonline.com | Umożliwia autoryzację i uwierzytelnianie przy użyciu adresów URL usługi Site Recovery. |
| *.hypervrecoverymanager.windowsazure.com | Umożliwia komunikację między maszyną wirtualną a usługą Site Recovery. |
| *.servicebus.windows.net | Umożliwia maszynie wirtualnej zapisywanie danych monitorowania i danych diagnostycznych usługi Site Recovery. |

### <a name="outbound-connectivity-for-ip-address-ranges"></a>Połączenia ruchu wychodzącego dla zakresów adresów IP

Do sterowania ruchem wychodzącym dla maszyn wirtualnych przy użyciu adresów IP, należy zezwolić na te adresy.

#### <a name="source-region-rules"></a>Reguły region źródła

**Reguły** |  **Szczegóły** | **Tag usługi**
--- | --- | --- 
Zezwala na wychodzące połączenia HTTPS: port 443 | Zezwól na użycie zakresów, które odpowiadają na kontach magazynu w regionie źródłowym | Magazyn. \<region-name >.
Zezwala na wychodzące połączenia HTTPS: port 443 | Zezwalaj na zakresy, które odnoszą się do usługi Azure Active Directory (Azure AD).<br/><br/> Jeśli adresy usługi Azure AD zostaną dodane w przyszłości, musisz utworzyć nowe reguły sieciowej grupy zabezpieczeń (NSG).  | AzureActiveDirectory
Zezwala na wychodzące połączenia HTTPS: port 443 | Zezwalaj na dostęp do [punktów końcowych Site Recovery](https://aka.ms/site-recovery-public-ips) odnoszą się do lokalizacji docelowej. 

#### <a name="target-region-rules"></a>Docelowy region reguły

**Reguły** |  **Szczegóły** | **Tag usługi**
--- | --- | --- 
Zezwala na wychodzące połączenia HTTPS: port 443 | Zezwalaj na zakresy, które odnoszą się do konta magazynu w regionie docelowym. | Magazyn. \<region-name >.
Zezwala na wychodzące połączenia HTTPS: port 443 | Zezwalaj na zakresy, które odnoszą się do usługi Azure AD.<br/><br/> Jeśli adresy usługi Azure AD zostaną dodane w przyszłości, musisz utworzyć nowe reguły sieciowej grupy zabezpieczeń.  | AzureActiveDirectory
Zezwala na wychodzące połączenia HTTPS: port 443 | Zezwalaj na dostęp do [punktów końcowych Site Recovery](https://aka.ms/site-recovery-public-ips) odnoszą się do lokalizacji źródłowej. 


#### <a name="control-access-with-nsg-rules"></a>Kontrola dostępu przy użyciu reguł sieciowej grupy zabezpieczeń

Jeśli kontrolowania łączność maszyn wirtualnych przez filtrowanie ruchu sieciowego do i z sieci platformy Azure/podsieci za pomocą [reguły sieciowej grupy zabezpieczeń](https://docs.microsoft.com/azure/virtual-network/security-overview), należy uwzględnić następujące wymagania:

- Reguły sieciowej grupy zabezpieczeń dla źródła regionu świadczenia usługi Azure powinien zezwalać na dostęp ruchu wychodzącego dla ruchu związanego z replikacją.
- Zalecane jest tworzenie reguł w środowisku testowym przed umieszczeniem ich w środowisku produkcyjnym.
- Użyj [tagów usług](https://docs.microsoft.com/azure/virtual-network/security-overview#service-tags) zamiast poszczególnych adresów IP.
    - Tagi usługi reprezentuje grupę prefiksów adresów IP zebrane ze sobą, aby zminimalizować złożoność podczas tworzenia reguł zabezpieczeń.
    - Firma Microsoft automatycznie aktualizuje tagi usługi wraz z upływem czasu. 
 
Dowiedz się więcej o [łączności wychodzącej](azure-to-azure-about-networking.md#outbound-connectivity-for-ip-address-ranges) usługi Site Recovery i [kontrolowania łączność z sieciowymi grupami zabezpieczeń](concepts-network-security-group-with-site-recovery.md).


### <a name="connectivity-for-multi-vm-consistency"></a>Łączność w celu zachowania spójności wielu maszyn wirtualnych

Jeśli włączono spójność między wieloma maszynami wirtualnymi, maszyny z grupy replikacji komunikują się między sobą przez port 20004.
- Upewnij się, że żadne urządzenie zapory nie blokuje wewnętrznej komunikacji między maszynami wirtualnymi za pośrednictwem portu 20004.
- Jeśli do grupy replikacji chcesz dodać maszyny wirtualne z systemem Linux, musisz ręcznie otworzyć port 20004 dla ruchu wychodzącego zgodnie ze wskazówkami dla konkretnej wersji systemu Linux.




## <a name="failover-process"></a>Procesu pracy awaryjnej

Po zainicjowaniu przejścia w tryb failover maszyny wirtualne są utworzone w docelowej grupie zasobów, docelowa sieć wirtualna, podsieć docelowa, a następnie w zestawie dostępności docelowego. Podczas pracy w trybie failover można używać dowolnego punktu odzyskiwania.

![Procesu pracy awaryjnej](./media/concepts-azure-to-azure-architecture/failover.png)

## <a name="next-steps"></a>Kolejne kroki

[Szybkie replikowanie](azure-to-azure-quickstart.md) Maszynie wirtualnej platformy Azure do regionu pomocniczego.
