---
title: Architektura odzyskiwania po awarii platformy Azure na platformie Azure w usłudze Azure Site Recovery
description: Omówienie architektury używanej podczas konfigurowania odzyskiwania po awarii między regionami platformy Azure dla maszyn wirtualnych platformy Azure przy użyciu usługi Azure Site Recovery.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 3/13/2020
ms.author: raynew
ms.openlocfilehash: 94da1639b5398a03b36fba3ff88877468a97ec36
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80294115"
---
# <a name="azure-to-azure-disaster-recovery-architecture"></a>Architektura odzyskiwania po awarii z platformy Azure do platformy Azure


W tym artykule opisano architekturę, składniki i procesy używane podczas wdrażania odzyskiwania po awarii dla maszyn wirtualnych platformy Azure przy użyciu usługi [Azure Site Recovery.](site-recovery-overview.md) Po skonfigurowaniu odzyskiwania po awarii maszyny wirtualne platformy Azure stale replikują się z innego regionu docelowego. Jeśli wystąpi awaria, można awaryjnie maszyn wirtualnych do regionu pomocniczego i uzyskać do nich dostęp z tego miejsca. Gdy wszystko działa normalnie ponownie, można cofnąć po awarii i kontynuować pracę w lokalizacji podstawowej.



## <a name="architectural-components"></a>Składniki architektury

Składniki zaangażowane w odzyskiwanie po awarii dla maszyn wirtualnych platformy Azure są podsumowane w poniższej tabeli.

**Składnik** | **Wymagania**
--- | ---
**Maszyny wirtualne w regionie źródłowym** | Jedna z kolejnych maszyn wirtualnych platformy Azure w [obsługiwanym regionie źródłowym.](azure-to-azure-support-matrix.md#region-support)<br/><br/> Maszyny wirtualne mogą być uruchomione dowolny [obsługiwany system operacyjny](azure-to-azure-support-matrix.md#replicated-machine-operating-systems).
**Źródło magazynu maszyn wirtualnych** | Maszyny wirtualne platformy Azure mogą być zarządzane lub mają dyski niezarządzane rozłożone na konta magazynu.<br/><br/>[Dowiedz się więcej o](azure-to-azure-support-matrix.md#replicated-machines---storage) obsługiwanym magazynie platformy Azure.
**Źródłowe sieci maszyn wirtualnych** | Maszyny wirtualne mogą znajdować się w co najmniej jednej podsieci w sieci wirtualnej (VNet) w regionie źródłowym. [Dowiedz się więcej](azure-to-azure-support-matrix.md#replicated-machines---networking) o wymaganiach dotyczących sieci.
**Konto magazynu pamięci podręcznej** | Potrzebne jest konto magazynu pamięci podręcznej w sieci źródłowej. Podczas replikacji zmiany maszyny Wirtualnej są przechowywane w pamięci podręcznej przed wysłaniem do magazynu docelowego.  Konta magazynu pamięci podręcznej muszą być standardowe.<br/><br/> Korzystanie z pamięci podręcznej zapewnia minimalny wpływ na aplikacje produkcyjne, które są uruchomione na maszynie wirtualnej.<br/><br/> [Dowiedz się więcej](azure-to-azure-support-matrix.md#cache-storage) o wymaganiach dotyczących przechowywania pamięci podręcznej. 
**Zasoby docelowe** | Zasoby docelowe są używane podczas replikacji i po wystąpieniu pracy awaryjnej. Odzyskiwanie witryny można domyślnie skonfigurować zasób docelowy lub można je utworzyć/dostosować.<br/><br/> W regionie docelowym sprawdź, czy można tworzyć maszyny wirtualne i czy subskrypcja ma wystarczającą ilość zasobów do obsługi rozmiarów maszyn wirtualnych, które będą potrzebne w regionie docelowym. 

![Replikacja źródłowa i docelowa](./media/concepts-azure-to-azure-architecture/enable-replication-step-1.png)

## <a name="target-resources"></a>Zasoby docelowe

Po włączeniu replikacji dla maszyny Wirtualnej usługa Site Recovery umożliwia automatyczne tworzenie zasobów docelowych. 

**Zasób docelowy** | **Ustawienie domyślne**
--- | ---
**Subskrypcja docelowa** | Tak samo jak subskrypcja źródłowa.
**Docelowa grupa zasobów** | Grupa zasobów, do której należą maszyny wirtualne po przemienniu awaryjnym.<br/><br/> Może być w dowolnym regionie platformy Azure z wyjątkiem regionu źródłowego.<br/><br/> Odzysk witryny tworzy nową grupę zasobów w regionie docelowym z sufiksem "asr".<br/><br/>
**Docelowa sieci wirtualnej** | Sieć wirtualna (VNet), w której replikowane maszyny wirtualne znajdują się po przemiń awaryjnych. Mapowanie sieci jest tworzony między źródłowych i docelowych sieci wirtualnych i odwrotnie.<br/><br/> Site Recovery tworzy nową sieć wirtualną i podsieć z sufiksem "asr".
**Docelowe konto magazynu** |  Jeśli maszyna wirtualna nie używa dysku zarządzanego, jest to konto magazynu, na którym są replikowane dane.<br/><br/> Usługa Site Recovery tworzy nowe konto magazynu w regionie docelowym, aby odzwierciedlić konto magazynu źródłowego.
**Replika dysków zarządzanych** | Jeśli maszyna wirtualna używa dysku zarządzanego, są to dyski zarządzane, na które są replikowane dane.<br/><br/> Usługa Site Recovery tworzy dyski zarządzane repliki w regionie magazynu w celu dublowania źródła.
**Docelowe zestawy dostępności** |  Zestaw dostępności, w którym replikowane maszyny wirtualne znajdują się po przemijanie w błąd.<br/><br/> Usługa Site Recovery tworzy zestaw dostępności w regionie docelowym z sufiksem "asr" dla maszyn wirtualnych, które znajdują się w dostępności ustawionej w lokalizacji źródłowej. Jeśli istnieje zestaw dostępności, jest on używany, a nowy nie jest tworzony.
**Docelowe strefy dostępności** | Jeśli region docelowy obsługuje strefy dostępności, usługa Site Recovery przypisuje ten sam numer strefy, który jest używany w regionie źródłowym.

### <a name="managing-target-resources"></a>Zarządzanie zasobami docelowymi

Zasoby docelowe można zarządzać w następujący sposób:

- Ustawienia docelowe można modyfikować po włączeniu replikacji.
- Można zmodyfikować ustawienia docelowe po replikacji już działa. Wyjątkiem jest typ dostępności (pojedyncze wystąpienie, zestaw lub strefa). Aby zmienić to ustawienie, należy wyłączyć replikację, zmodyfikować to ustawienie, a następnie ponownie zmodyfikować.



## <a name="replication-policy"></a>Zasady replikacji 

Po włączeniu replikacji maszyny Wirtualnej platformy Azure domyślnie usługa Site Recovery tworzy nową zasadę replikacji z ustawieniami domyślnymi podsumowanym w tabeli.

**Ustawienie zasad** | **Szczegóły** | **Domyślny**
--- | --- | ---
**Retencja punktów odzyskiwania** | Określa, jak długo odzyskiwanie lokacji przechowuje punkty odzyskiwania | 24 godziny
**Częstotliwość migawek spójna z aplikacją** | Jak często funkcja Odzysk witryny wykonuje migawkę spójną z aplikacją. | Co cztery godziny

### <a name="managing-replication-policies"></a>Zarządzanie zasadami replikacji

Domyślne ustawienia zasad replikacji można zarządzać i modyfikować w następujący sposób:
- Ustawienia można modyfikować po włączeniu replikacji.
- Zasady replikacji można utworzyć w dowolnym momencie, a następnie zastosować ją po włączeniu replikacji.

### <a name="multi-vm-consistency"></a>Spójność wielu maszyn wirtualnych

Jeśli chcesz, aby maszyny wirtualne replikować razem i mają wspólne punkty odzyskiwania spójne z awariami i spójne z aplikacjami w pracy awaryjnej, można zebrać je razem do grupy replikacji. Spójność wielu maszyn wirtualnych wpływa na wydajność obciążenia i powinna być używana tylko dla maszyn wirtualnych z uruchomionymi obciążeniami, które wymagają spójności na wszystkich komputerach. 



## <a name="snapshots-and-recovery-points"></a>Migawki i punkty odzyskiwania

Punkty odzyskiwania są tworzone na podstawie migawek dysków maszyn wirtualnych wykonanych w określonym momencie w czasie. Po przełączeniu maszyny wirtualnej w stan fail/a punkt odzyskiwania służy do przywracania maszyny wirtualnej w lokalizacji docelowej.

W przypadku awarii, zazwyczaj chcemy upewnić się, że maszyna wirtualna rozpoczyna się bez uszkodzenia lub utraty danych i że dane maszyny Wirtualnej jest spójne dla systemu operacyjnego i dla aplikacji, które działają na maszynie Wirtualnej. To zależy od typu wykonanych migawek.

Funkcja Site Recovery wykonuje migawki w następujący sposób:

1. Usługa Site Recovery domyślnie wykonuje migawki danych zgodne ze crash-consistent i migawki spójne z aplikacjami, jeśli określisz częstotliwość dla nich.
2. Punkty odzyskiwania są tworzone na podstawie migawek i przechowywane zgodnie z ustawieniami przechowywania w zasadach replikacji.

### <a name="consistency"></a>Spójność

W poniższej tabeli wyjaśniono różne typy spójności.

### <a name="crash-consistent"></a>Ujmuje się w crash

**Opis** | **Szczegóły** | **Zalecenie**
--- | --- | ---
Migawka spójna z awarią przechwytuje dane, które znajdowały się na dysku podczas robienia migawki. Nie zawiera niczego w pamięci.<br/><br/> Zawiera odpowiednik danych na dysku, które byłyby obecne, jeśli maszyna wirtualna uległa awarii lub kabel zasilania został pobrany z serwera w momencie, gdy migawka została podjęta.<br/><br/> Spójne awarie nie gwarantuje spójności danych dla systemu operacyjnego lub dla aplikacji na maszynie Wirtualnej. | Odzyskiwanie witryny domyślnie tworzy punkty odzyskiwania zgodne z awariami co pięć minut. Tego ustawienia nie można zmodyfikować.<br/><br/>  | Obecnie większość aplikacji może odzyskać również z punktów spójnych z awariami.<br/><br/> Punkty odzyskiwania zgodne z awariami są zwykle wystarczające do replikacji systemów operacyjnych i aplikacji, takich jak serwery DHCP i serwery wydruku.

### <a name="app-consistent"></a>Spójne z aplikacjami

**Opis** | **Szczegóły** | **Zalecenie**
--- | --- | ---
Punkty odzyskiwania spójne z aplikacjami są tworzone na podstawie migawek spójnych z aplikacjami.<br/><br/> Migawka spójna z aplikacją zawiera wszystkie informacje w migawce zgodnej z awariami oraz wszystkie dane w pamięci i transakcje w toku. | Migawki spójne z aplikacjami korzystają z usługi kopiowania woluminów w tle (VSS):<br/><br/>   1) Po zainicjowaniu migawki usługa VSS wykonuje operację kopiowania przy zapisie (COW) na woluminie.<br/><br/>   2) Przed wykonaniem COW, VSS informuje każdą aplikację na komputerze, że musi opróżnić swoje dane rezydentne pamięci na dysku.<br/><br/>   3) NASTĘPNIE VSS umożliwia kopii zapasowej / odzyskiwania po awarii aplikacji (w tym przypadku Site Recovery) do odczytu danych migawki i kontynuować. | Migawki spójne z aplikacjami są pobierane zgodnie z określoną częstotliwością. Częstotliwość ta powinna być zawsze mniejsza niż ustawiona dla zachowania punktów odzyskiwania. Na przykład jeśli punkty odzyskiwania są zachowywane przy użyciu domyślnego ustawienia 24 godzin, należy ustawić częstotliwość na mniej niż 24 godziny.<br/><br/>Są one bardziej złożone i trwać dłużej niż migawki spójne z awariami.<br/><br/> Wpływają one na wydajność aplikacji uruchomionych na maszynie Wirtualnej włączonej do replikacji. 

## <a name="replication-process"></a>Proces replikacji

Po włączeniu replikacji dla maszyny Wirtualnej platformy Azure dzieje się tak:

1. Rozszerzenie usługi Mobilności odzyskiwania lokacji jest automatycznie instalowane na maszynie wirtualnej.
2. Rozszerzenie rejestruje maszynę wirtualną z odzyskiwaniem witryny.
3. Rozpoczyna się replikacja ciągła dla maszyny Wirtualnej.  Zapisy dysków są natychmiast przenoszone na konto magazynu pamięci podręcznej w lokalizacji źródłowej.
4. Usługa Site Recovery przetwarza dane w pamięci podręcznej i wysyła je do docelowego konta magazynu lub do dysków zarządzanych repliki.
5. Po przetworzeniu danych punkty odzyskiwania zgodne z awariami są generowane co pięć minut. Punkty odzyskiwania spójne z aplikacjami są generowane zgodnie z ustawieniem określonym w zasadach replikacji.

![Włącz proces replikacji, krok 2](./media/concepts-azure-to-azure-architecture/enable-replication-step-2.png)

**Proces replikacji**

## <a name="connectivity-requirements"></a>Wymagania dotyczące łączności

 Maszyny wirtualne platformy Azure, które replikujesz, wymagają łączności wychodzącej. Odzyskiwanie lokacji nigdy nie wymaga połączenia przychodzącego z maszyną wirtualną. 

### <a name="outbound-connectivity-urls"></a>Łączność wychodząca (adresy URL)

Jeśli dostęp wychodzący dla maszyn wirtualnych jest kontrolowany za pomocą adresów URL, zezwalaj na te adresy URL.

| **Adres URL** | **Szczegóły** |
| ------- | ----------- |
| *.blob.core.windows.net | Umożliwia zapisanie danych z maszyny wirtualnej na koncie magazynu pamięci podręcznej znajdującym się w regionie źródłowym. |
| login.microsoftonline.com | Umożliwia autoryzację i uwierzytelnianie przy użyciu adresów URL usługi Site Recovery. |
| *.hypervrecoverymanager.windowsazure.com | Umożliwia komunikację między maszyną wirtualną a usługą Site Recovery. |
| *.servicebus.windows.net | Umożliwia maszynie wirtualnej zapisywanie danych monitorowania i danych diagnostycznych usługi Site Recovery. |
| *.vault.azure.net | Umożliwia dostęp do włączania replikacji dla maszyn wirtualnych z obsługą ADE za pośrednictwem portalu
| *.automation.ext.azure.com | Umożliwia automatyczne uaktualnianie agenta mobilności dla zreplikowanego elementu za pośrednictwem portalu

### <a name="outbound-connectivity-for-ip-address-ranges"></a>Połączenia ruchu wychodzącego dla zakresów adresów IP

Aby kontrolować łączność wychodzącą dla maszyn wirtualnych przy użyciu adresów IP, należy zezwolić na te adresy.
Należy pamiętać, że szczegółowe informacje na temat wymogów dotyczących łączności [sieciowej](azure-to-azure-about-networking.md#outbound-connectivity-using-service-tags) można znaleźć w 

#### <a name="source-region-rules"></a>Reguły regionu źródłowego

**Reguła** |  **Szczegóły** | **Tag usługi**
--- | --- | --- 
Zezwalaj na wychodzące usługi HTTPS: port 443 | Zezwalaj na zakresy odpowiadające kontom magazynu w regionie źródłowym | Magazynu. \<nazwa regionu>
Zezwalaj na wychodzące usługi HTTPS: port 443 | Zezwalaj na zakresy odpowiadające usłudze Azure Active Directory (Azure AD)  | Usługa AzureActiveDirectory
Zezwalaj na wychodzące usługi HTTPS: port 443 | Zezwalaj na zakresy, które odpowiadają Centrum zdarzeń w regionie docelowym. | EventsHub. \<nazwa regionu>
Zezwalaj na wychodzące usługi HTTPS: port 443 | Zezwalaj na zakresy odpowiadające usłudze Azure Site Recovery  | Usługa AzureSiteRecovery
Zezwalaj na wychodzące usługi HTTPS: port 443 | Zezwalaj na zakresy odpowiadające usługi Azure Key Vault (jest to wymagane tylko do włączania replikacji maszyn wirtualnych z obsługą usługi ADE za pośrednictwem portalu) | Usługa AzureKeyVault
Zezwalaj na wychodzące usługi HTTPS: port 443 | Zezwalaj na zakresy odpowiadające kontrolerowi automatyzacji usługi Azure (jest to wymagane tylko do automatycznego uaktualniania agenta mobilności dla zreplikowanego elementu za pośrednictwem portalu) | GuestAndHybridManagement

#### <a name="target-region-rules"></a>Reguły regionu docelowego

**Reguła** |  **Szczegóły** | **Tag usługi**
--- | --- | --- 
Zezwalaj na wychodzące usługi HTTPS: port 443 | Zezwalaj na zakresy odpowiadające kontom magazynu w regionie docelowym | Magazynu. \<nazwa regionu>
Zezwalaj na wychodzące usługi HTTPS: port 443 | Zezwalaj na zakresy odpowiadające usłudze Azure AD  | Usługa AzureActiveDirectory
Zezwalaj na wychodzące usługi HTTPS: port 443 | Zezwalaj na zakresy, które odpowiadają Centrum zdarzeń w regionie źródłowym. | EventsHub. \<nazwa regionu>
Zezwalaj na wychodzące usługi HTTPS: port 443 | Zezwalaj na zakresy odpowiadające usłudze Azure Site Recovery  | Usługa AzureSiteRecovery
Zezwalaj na wychodzące usługi HTTPS: port 443 | Zezwalaj na zakresy odpowiadające usługi Azure Key Vault (jest to wymagane tylko do włączania replikacji maszyn wirtualnych z obsługą usługi ADE za pośrednictwem portalu) | Usługa AzureKeyVault
Zezwalaj na wychodzące usługi HTTPS: port 443 | Zezwalaj na zakresy odpowiadające kontrolerowi automatyzacji usługi Azure (jest to wymagane tylko do automatycznego uaktualniania agenta mobilności dla zreplikowanego elementu za pośrednictwem portalu) | GuestAndHybridManagement


#### <a name="control-access-with-nsg-rules"></a>Kontroluj dostęp za pomocą reguł sieciowych sieciowych

Jeśli kontrolujesz łączność maszyn wirtualnych, filtrując ruch sieciowy do i z sieci/podsieci platformy Azure przy użyciu [reguł sieciowej sieciowej,](https://docs.microsoft.com/azure/virtual-network/security-overview)należy pamiętać o następujących wymaganiach:

- Reguły sieciowej grupy sieciowej dla źródłowego regionu platformy Azure powinny zezwalać na dostęp wychodzący dla ruchu replikacji.
- Zaleca się tworzenie reguł w środowisku testowym przed ich wprowadzeniem do środowiska produkcyjnego.
- Używaj [tagów usług](https://docs.microsoft.com/azure/virtual-network/security-overview#service-tags) zamiast zezwalać na poszczególne adresy IP.
    - Tagi usługi reprezentują grupę prefiksów adresów IP zebranych razem, aby zminimalizować złożoność podczas tworzenia reguł zabezpieczeń.
    - Firma Microsoft automatycznie aktualizuje tagi usług w czasie. 
 
Dowiedz się więcej o [łączności wychodzącej](azure-to-azure-about-networking.md#outbound-connectivity-using-service-tags) dla usługi Site Recovery i [kontrolowaniu łączności z sieciami sieciowymi.](concepts-network-security-group-with-site-recovery.md)


### <a name="connectivity-for-multi-vm-consistency"></a>Łączność dla spójności wielu maszyn wirtualnych

Jeśli włączono spójność między wieloma maszynami wirtualnymi, maszyny z grupy replikacji komunikują się między sobą przez port 20004.
- Upewnij się, że żadne urządzenie zapory nie blokuje wewnętrznej komunikacji między maszynami wirtualnymi za pośrednictwem portu 20004.
- Jeśli do grupy replikacji chcesz dodać maszyny wirtualne z systemem Linux, musisz ręcznie otworzyć port 20004 dla ruchu wychodzącego zgodnie ze wskazówkami dla konkretnej wersji systemu Linux.




## <a name="failover-process"></a>Proces pracy awaryjnej

Podczas inicjowania pracy awaryjnej maszyny wirtualne są tworzone w docelowej grupie zasobów, docelowej sieci wirtualnej, podsieci docelowej i w zestawie dostępności docelowej. Podczas pracy awaryjnej można użyć dowolnego punktu odzyskiwania.

![Proces pracy awaryjnej](./media/concepts-azure-to-azure-architecture/failover.png)

## <a name="next-steps"></a>Następne kroki

[Szybkie replikowanie](azure-to-azure-quickstart.md) maszyny Wirtualnej platformy Azure do regionu pomocniczego.
