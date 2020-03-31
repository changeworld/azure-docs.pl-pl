---
title: Odzyskiwanie po awarii maszyn wirtualnych VMware/serwerów fizycznych do lokacji dodatkowej za pomocą usługi Azure Site Recovery
description: Dowiedz się, jak skonfigurować odzyskiwanie po awarii maszyn wirtualnych VMware lub serwerów fizycznych systemu Windows i Linux w lokacji dodatkowej za pomocą usługi Azure Site Recovery.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
services: site-recovery
ms.topic: conceptual
ms.date: 11/05/2019
ms.author: raynew
ms.openlocfilehash: 71d230c9fea25edfbf0ca4ea40f15b69779ad060
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79256811"
---
# <a name="set-up-disaster-recovery-of-on-premises-vmware-virtual-machines-or-physical-servers-to-a-secondary-site"></a>Konfigurowanie odzyskiwania po awarii lokalnych maszyn wirtualnych programu VMware lub serwerów fizycznych do lokacji dodatkowej

InMage Scout w [usłudze Azure Site Recovery](site-recovery-overview.md) zapewnia replikację w czasie rzeczywistym między lokalnymi lokacjami VMware. InMage Scout jest uwzględniony w subskrypcjach usługi Azure Site Recovery.

## <a name="end-of-support-announcement"></a>Ogłoszenie końca wsparcia

Scenariusz odzyskiwania usługi Azure site recovery dla replikacji między lokalnymi vmware lub fizycznych centrów danych osiąga koniec pomocy technicznej.

-   Od sierpnia 2018 r. scenariusza nie można skonfigurować w magazynie usług odzyskiwania, a oprogramowania InMage Scout nie można pobrać z magazynu. Obsługiwane będą istniejące wdrożenia. 
-   Od 31 grudnia 2020 r. scenariusz nie będzie obsługiwany.
- Obecni partnerzy mogą dołączać nowych klientów do scenariusza do momentu zakończenia pomocy technicznej.

W 2018 i 2019 roku zostaną wydane dwie aktualizacje: 

-   Aktualizacja 7: Rozwiązuje problemy z konfiguracją sieci i zgodnością i zapewnia obsługę protokołu TLS 1.2.
-   Aktualizacja 8: Dodaje obsługę systemów operacyjnych Linux RHEL/CentOS 7.3/7.4/7.5 oraz dla SUSE 12

Po aktualizacji 8 nie zostaną wydane żadne dalsze aktualizacje. Obsługa poprawek dla systemów operacyjnych dodanych w aktualizacji 8 będzie ograniczona, a poprawki błędów będą oparte na najlepszych działaniach.

Usługa Azure Site Recovery nadal wprowadza innowacje, zapewniając klientom VMware i Hyper-V bezproblemowe i najlepsze w swojej klasie rozwiązanie DRaaS z platformą Azure jako lokacją odzyskiwania po awarii. Firma Microsoft zaleca, aby istniejący klienci Programu InMage / ASR Scout rozważyli użycie scenariusza VMware usługi Azure Site Recovery na platformie Azure dla ich potrzeb ciągłości biznesowej. Scenariusz VMware do platformy Azure firmy Azure w usłudze Azure Site Recovery to rozwiązanie odzyskiwania po awarii klasy korporacyjnej dla aplikacji VMware, które oferuje liczbę minut RPO i RTO, obsługę replikacji i odzyskiwania aplikacji wielu maszyn wirtualnych, bezproblemowe dołączanie, kompleksowe monitorowanie i znaczną przewagę 2000 000 000

### <a name="scenario-migration"></a>Migracja scenariusza
Alternatywnie zaleca się skonfigurowanie odzyskiwania po awarii dla lokalnych maszyn wirtualnych VMware i komputerów fizycznych przez replikowanie ich na platformie Azure. W tym celu wykonaj następujące czynności:

1.  Przejrzyj szybkie porównanie poniżej. Aby można było replikować maszyny lokalne, należy sprawdzić, czy spełniają one [wymagania dotyczące](./vmware-physical-azure-support-matrix.md#replicated-machines) replikacji na platformie Azure. W przypadku replikowania maszyn wirtualnych VMware zaleca się zapoznanie się z [wytycznymi dotyczącymi planowania pojemności](./site-recovery-plan-capacity-vmware.md)i uruchomienie narzędzia [Planowanie wdrażania](./site-recovery-deployment-planner.md) do wymagań dotyczących pojemności tożsamości i sprawdzenie zgodności.
2.  Po uruchomieniu Planisty wdrażania można skonfigurować replikację: o Dla maszyn wirtualnych VMware, wykonaj te samouczki, aby [przygotować platformę Azure,](./tutorial-prepare-azure.md) [przygotować lokalne środowisko VMware](./vmware-azure-tutorial-prepare-on-premises.md)i [skonfigurować odzyskiwanie po awarii](./vmware-azure-tutorial-prepare-on-premises.md).
o W przypadku maszyn fizycznych wykonaj ten [samouczek](./physical-azure-disaster-recovery.md).
3.  Po maszynach są replikowane na platformie Azure, można uruchomić [wiertło odzyskiwania po awarii,](./site-recovery-test-failover-to-azure.md) aby upewnić się, że wszystko działa zgodnie z oczekiwaniami.

### <a name="quick-comparison"></a>Szybkie porównanie

**Funkcja** | **Replikacja do platformy Azure** |**Replikacja między centrami danych VMware**
--|--|--
**Wymagane komponenty** |Usługa mobilności na replikowanych maszynach. Lokalny serwer konfiguracji, serwer przetwarzania, główny serwer docelowy. Tymczasowy serwer procesów na platformie Azure dla powrotu po awarii.|Usługa mobilności, serwer przetwarzania, serwer konfiguracji i główny cel
**Konfiguracja i aranżacja** |Magazyn usług odzyskiwania w witrynie Azure portal | Korzystanie z funkcji vContinuum 
**Replikowane** |Dysk (Windows i Linux) |System Tom-Windows<br> Dysk-Linux
**Klaster dysków udostępnionych** |Nieobsługiwane|Obsługiwane
**Limity zmian danych (średnia)** |10 MB/s danych na dysk<br> 25 MB/s danych na maszynę wirtualną<br> [Dowiedz się więcej](./site-recovery-vmware-deployment-planner-analyze-report.md#azure-site-recovery-limits) | > 10 MB/s danych na dysk  <br> > danych 25 MB/s na maszynę wirtualną
**Monitorowania** |Z witryny Azure portal|Z CX (Serwer konfiguracji)
**Macierz wsparcia** | [Kliknij tutaj, aby uzyskać szczegółowe informacje](./vmware-physical-azure-support-matrix.md)|[Pobierz matrycę kompatybilną z ASR Scout](https://aka.ms/asr-scout-cm)


## <a name="prerequisites"></a>Wymagania wstępne
W celu ukończenia tego samouczka:

- [Przejrzyj](vmware-physical-secondary-support-matrix.md) wymagania dotyczące obsługi technicznej dla wszystkich składników.
- Upewnij się, że maszyny, które chcesz replikować, są zgodne z [obsługą replikowanych maszyn](vmware-physical-secondary-support-matrix.md#replicated-vm-support).


## <a name="download-and-install-component-updates"></a>Pobieranie i instalowanie aktualizacji składników

 Przejrzyj i zainstaluj najnowsze [aktualizacje](#updates). Aktualizacje powinny być instalowane na serwerach w następującej kolejności:

1. Serwer RX (jeśli dotyczy)
2. Serwery konfiguracyjne
3. Serwery przetwarzania
4. Główne serwery docelowe
5. Serwery vContinuum
6. Serwer źródłowy (zarówno serwery Windows, jak i Linux)

Zainstaluj aktualizacje w następujący sposób:

> [!NOTE]
>Wersja aktualizacji pliku wszystkich składników Scout może nie być taka sama w pliku .zip aktualizacji. Starsza wersja wskazuje, że nie ma żadnych zmian w składniku od poprzedniej aktualizacji do tej aktualizacji.

Pobierz [plik .zip aktualizacji](https://aka.ms/asr-scout-update7) i [pliki konfiguracyjne uaktualnienia MySQL i PHP.](https://aka.ms/asr-scout-u7-mysql-php-manualupgrade) Plik .zip aktualizacji zawiera wszystkie podstawowe pliki binarne i zbiorcze pliki binarne uaktualnienia następujących składników: 
- InMage_ScoutCloud_RX_8.0.1.0_RHEL6-64_GA_02Mar2015.tar.gz
- RX_8.0.7.0_GA_Update_7_2965621_28Dec18.tar.gz
- InMage_CX_8.0.1.0_Windows_GA_26Feb2015_release.exe
- InMage_CX_TP_8.0.1.0_Windows_GA_26Feb2015_release.exe
- CX_Windows_8.0.7.0_GA_Update_7_2965621_28Dec18.exe
- InMage_PI_8.0.1.0_Windows_GA_26Feb2015_release.exe
- InMage_Scout_vContinuum_MT_8.0.7.0_Windows_GA_27Dec2018_release.exe
- InMage_UA_8.0.7.0_Windows_GA_27Dec2018_release.exe
- InMage_UA_8.0.7.0_OL5-32_GA_03Dec2018_release.tar.gz
- InMage_UA_8.0.7.0_OL5-64_GA_03Dec2018_release.tar.gz
- InMage_UA_8.0.7.0_OL6-32_GA_03Dec2018_release.tar.gz
- InMage_UA_8.0.7.0_OL6-64_GA_03Dec2018_release.tar.gz
- InMage_UA_8.0.7.0_RHEL5-32_GA_03Dec2018_release.tar.gz
- InMage_UA_8.0.7.0_RHEL5-64_GA_03Dec2018_release.tar.gz
- InMage_UA_8.0.7.0_RHEL6-32_GA_03Dec2018_release.tar.gz
- InMage_UA_8.0.7.0_RHEL6-64_GA_03Dec2018_release.tar.gz
- InMage_UA_8.0.7.0_RHEL7-64_GA_03Dec2018_release.tar.gz
- InMage_UA_8.0.7.0_SLES10-32_GA_03Dec2018_release.tar.gz
- InMage_UA_8.0.7.0_SLES10-64_GA_03Dec2018_release.tar.gz
- InMage_UA_8.0.7.0_SLES10-SP1-32_GA_03Dec2018_release.tar.gz
- InMage_UA_8.0.7.0_SLES10-SP1-64_GA_03Dec2018_release.tar.gz
- InMage_UA_8.0.7.0_SLES10-SP2-32_GA_03Dec2018_release.tar.gz
- InMage_UA_8.0.7.0_SLES10-SP2-64_GA_03Dec2018_release.tar.gz
- InMage_UA_8.0.7.0_SLES10-SP3-32_GA_03Dec2018_release.tar.gz
- InMage_UA_8.0.7.0_SLES10-SP3-64_GA_03Dec2018_release.tar.gz
- InMage_UA_8.0.7.0_SLES10-SP4-32_GA_03Dec2018_release.tar.gz
- InMage_UA_8.0.7.0_SLES10-SP4-64_GA_03Dec2018_release.tar.gz
- InMage_UA_8.0.7.0_SLES11-32_GA_03Dec2018_release.tar.gz
- InMage_UA_8.0.7.0_SLES11-64_GA_04Dec2018_release.tar.gz
- InMage_UA_8.0.7.0_SLES11-SP1-32_GA_03Dec2018_release.tar.gz
- InMage_UA_8.0.7.0_SLES11-SP1-64_GA_04Dec2018_release.tar.gz
- InMage_UA_8.0.7.0_SLES11-SP2-32_GA_03Dec2018_release.tar.gz
- InMage_UA_8.0.7.0_SLES11-SP2-64_GA_03Dec2018_release.tar.gz
- InMage_UA_8.0.7.0_SLES11-SP3-32_GA_03Dec2018_release.tar.gz
- InMage_UA_8.0.7.0_SLES11-SP3-64_GA_03Dec2018_release.tar.gz
- InMage_UA_8.0.7.0_SLES11-SP4-64_GA_03Dec2018_release.tar.gz
  1. Wyodrębnij pliki .zip.
  2. **Serwer RX**: Skopiuj **RX_8.0.7.0_GA_Update_7_2965621_28Dec18.tar.gz** na serwer RX i wyodrębnij go. W wyodrębnionym folderze uruchom **program /Install**.
  3. **Serwer konfiguracji i serwer przetwarzania:** Kopiuj **CX_Windows_8.0.7.0_GA_Update_7_2965621_28Dec18.exe** na serwer konfiguracji i serwer przetwarzania. Kliknij dwukrotnie, aby go uruchomić.<br>
  4. **Główny serwer docelowy systemu Windows**: Aby zaktualizować ujednoliconego agenta, skopiuj **InMage_UA_8.0.7.0_Windows_GA_27Dec2018_release.exe** na serwer. Kliknij go dwukrotnie, aby go uruchomić. Ten sam plik może być również używany do świeżej instalacji. Ta sama aktualizacja ujednoliconego agenta ma również zastosowanie do serwera źródłowego.
  Aktualizacja nie musi mieć zastosowania do głównego obiektu docelowego przygotowanego z **InMage_Scout_vContinuum_MT_8.0.7.0_Windows_GA_27Dec2018_release.exe,** ponieważ jest to nowy instalator GA ze wszystkimi najnowszymi zmianami.
  5. **vContinuum server**: Kopiuj **InMage_Scout_vContinuum_MT_8.0.7.0_Windows_GA_27Dec2018_release.exe** na serwer.  Upewnij się, że kreator vContinuum został zamknięty. Kliknij dwukrotnie plik, aby go uruchomić.
  6. **Główny serwer docelowy systemu Linux**: Aby zaktualizować ujednoliconego agenta, skopiuj **InMage_UA_8.0.7.0_RHEL6-64_GA_03Dec2018_release.tar.gz** na serwer linux Master Target i wyodrębnij go. W wyodrębnionym folderze uruchom **program /Install**.
  7. **Serwer źródłowy systemu Windows**: Aby zaktualizować ujednoliconego agenta, skopiuj **InMage_UA_8.0.7.0_Windows_GA_27Dec2018_release.exe** na serwer źródłowy. Kliknij dwukrotnie plik, aby go uruchomić. 
  8. **Serwer źródłowy Systemu Linux**: Aby zaktualizować ujednoliconego agenta, skopiuj odpowiednią wersję pliku ujednoliconego agenta na serwer Linux i wyodrębnij go. W wyodrębnionym folderze uruchom **program /Install**.  Przykład: W przypadku serwera 6.7 64-bitowego RHEL skopiuj **InMage_UA_8.0.7.0_RHEL6-64_GA_03Dec2018_release.tar.gz** na serwer i wyodrębnij go. W wyodrębnionym folderze uruchom **program /Install**.
  9. Po uaktualnieniu serwera konfiguracyjnego, serwera przetwarzania i serwera RX z wyżej wymienionymi instalatorami, biblioteki PHP i MySQL muszą zostać uaktualnione ręcznie o kroki wymienione w sekcji 7.4 [przewodnika szybkiej instalacji.](https://aka.ms/asr-scout-quick-install-guide)

## <a name="enable-replication"></a>Włączanie replikacji

1. Konfigurowanie replikacji między źródłowymi i docelowymi lokacjami VMware.
2. Więcej informacji na temat instalacji, ochrony i odzyskiwania można znaleźć w następujących dokumentach:

   * [Informacje o wersji](https://aka.ms/asr-scout-release-notes)
   * [Macierz zgodności](https://aka.ms/asr-scout-cm)
   * [Instrukcja obsługi](https://aka.ms/asr-scout-user-guide)
   * [Instrukcja obsługi RX](https://aka.ms/asr-scout-rx-user-guide)
   * [Przewodnik szybkiej instalacji](https://aka.ms/asr-scout-quick-install-guide)
   * [Uaktualnianie bibliotek MYSQL i PHP](https://aka.ms/asr-scout-u7-mysql-php-manualupgrade)

## <a name="updates"></a>Aktualizacje

### <a name="site-recovery-scout-801-update-7"></a>Aktualizacja 7 Site Recovery Scout 8.0.1 
Aktualizacja: 31 grudnia 2018 Pobierz [aktualizację Scout 7](https://aka.ms/asr-scout-update7).
Scout Update 7 to pełny instalator, który może być używany do świeżej instalacji, a także do uaktualniania istniejących agentów/MT, które są na poprzednich aktualizacjach (od aktualizacji 1 do aktualizacji 6). Zawiera wszystkie poprawki od aktualizacji 1 do aktualizacji 6 oraz nowe poprawki i ulepszenia opisane poniżej.
 
#### <a name="new-features"></a>Nowe funkcje
* Zgodność z PCI
* Obsługa protokołu TLS v1.2

#### <a name="bug-and-security-fixes"></a>Poprawki błędów i zabezpieczeń
* Poprawiono: Klaster systemu Windows/komputery autonomiczne mają nieprawidłową konfigurację IP podczas odzyskiwania/dr-drill.
* Naprawiono: Czasami dodawanie operacji dysku kończy się niepowodzeniem dla klastra V2V.
* Naprawiono: Kreator vContinuum utknie podczas fazy odzyskiwania, jeśli głównym celem jest Windows Server 2016
* Naprawiono: Problemy z zabezpieczeniami MySQL są ograniczane przez uaktualnienie MySQL do wersji 5.7.23

#### <a name="manual-upgrade-for-php-and-mysql-on-csps-and-rx"></a>Ręczna aktualizacja dla PHP i MySQL na CS, PS i RX
Platforma skryptów PHP powinna zostać uaktualniona do wersji 7.2.10 na serwerze konfiguracyjnym, serwerze przetwarzania i serwerze RX.
System zarządzania bazami danych MySQL powinien zostać uaktualniony do wersji 5.7.23 na serwerze konfiguracyjnym, serwerze przetwarzania i serwerze RX.
Postępuj zgodnie z instrukcjami podanymi w [przewodniku szybkiej instalacji,](https://aka.ms/asr-scout-quick-install-guide) aby uaktualnić wersje PHP i MySQL.

### <a name="site-recovery-scout-801-update-6"></a>Aktualizacja 6 Programu Site Recovery Scout 8.0.1 
Updated: 12 października 2017

Pobierz [aktualizację Scout 6](https://aka.ms/asr-scout-update6).

Aktualizacja Skauta 6 to zbiorcza aktualizacja. Zawiera wszystkie poprawki od aktualizacji 1 do aktualizacji 5 oraz nowe poprawki i ulepszenia opisane poniżej. 

#### <a name="new-platform-support"></a>Nowa obsługa platformy
* Dodano pomoc techniczną dla źródła systemu Windows Server 2016
* Dodano obsługę następujących systemów operacyjnych Linux:
    - Red Hat Enterprise Linux (RHEL) 6.9
    - CentOS 6,9
    - Oracle Linux 5.11
    - Oracle Linux 6.8
* Dodano obsługę VMware Center 6.5

Zainstaluj aktualizacje w następujący sposób:

> [!NOTE]
>Wersja aktualizacji pliku wszystkich składników Scout może nie być taka sama w pliku .zip aktualizacji. Starsza wersja wskazuje, że nie ma żadnych zmian w składniku od poprzedniej aktualizacji do tej aktualizacji.

Pobierz [plik .zip aktualizacji.](https://aka.ms/asr-scout-update6) Plik zawiera następujące składniki: 
- RX_8.0.4.0_GA_Update_4_8725872_16Sep16.tar.gz
- CX_Windows_8.0.6.0_GA_Update_6_13746667_18Sep17.exe
- UA_Windows_8.0.5.0_GA_Update_5_11525802_20Apr17.exe
- UA_RHEL6-64_8.0.4.0_GA_Update_4_9035261_26Sep16.tar.gz
- vCon_Windows_8.0.6.0_GA_Update_6_11525767_21Sep17.exe
- Aktualizacja UA4 bity dla RHEL5, OL5, OL6, SUSE 10, SUSE 11: UA_\<Linux OS>_8.0.4.0_GA_Update_4_9035261_26Sep16.tar.gz
  1. Wyodrębnij pliki .zip.
  2. **Serwer RX**: Skopiuj **RX_8.0.4.0_GA_Update_4_8725872_16Sep16.tar.gz** na serwer RX i wyodrębnij go. W wyodrębnionym folderze uruchom **program /Install**.
  3. **Serwer konfiguracji i serwer przetwarzania:** Kopiuj **CX_Windows_8.0.6.0_GA_Update_6_13746667_18Sep17.exe** na serwer konfiguracji i serwer przetwarzania. Kliknij dwukrotnie, aby go uruchomić.<br>
  4. **Główny serwer docelowy systemu Windows**: Aby zaktualizować ujednoliconego agenta, skopiuj **UA_Windows_8.0.5.0_GA_Update_5_11525802_20Apr17.exe** na serwer. Kliknij go dwukrotnie, aby go uruchomić. Ta sama aktualizacja ujednoliconego agenta ma również zastosowanie do serwera źródłowego. Jeśli źródło nie zostało zaktualizowane do aktualizacji 4, należy zaktualizować ujednoliconego agenta.
  Aktualizacja nie musi mieć zastosowania do głównego obiektu docelowego przygotowanego z **InMage_Scout_vContinuum_MT_8.0.1.0_Windows_GA_10Oct2017_release.exe,** ponieważ jest to nowy instalator GA ze wszystkimi najnowszymi zmianami.
  5. **vContinuum server**: Kopiuj **vCon_Windows_8.0.6.0_GA_Update_6_11525767_21Sep17.exe** na serwer.  Upewnij się, że kreator vContinuum został zamknięty. Kliknij dwukrotnie plik, aby go uruchomić.
  Aktualizacja nie musi być stosowana w głównym celu przygotowanym z **InMage_Scout_vContinuum_MT_8.0.1.0_Windows_GA_10Oct2017_release.exe,** ponieważ jest to nowy instalator GA ze wszystkimi najnowszymi zmianami.
  6. **Główny serwer docelowy systemu Linux**: Aby zaktualizować ujednoliconego agenta, skopiuj **UA_RHEL6-64_8.0.4.0_GA_Update_4_9035261_26Sep16.tar.gz** do głównego serwera docelowego i wyodrębnij go. W wyodrębnionym folderze uruchom **program /Install**.
  7. **Serwer źródłowy systemu Windows**: Aby zaktualizować ujednoliconego agenta, skopiuj **UA_Windows_8.0.5.0_GA_Update_5_11525802_20Apr17.exe** na serwer źródłowy. Kliknij dwukrotnie plik, aby go uruchomić. 
  Nie trzeba instalować agenta aktualizacji 5 na serwerze źródłowym, jeśli został on już zaktualizowany do aktualizacji 4 lub agent źródłowy jest zainstalowany z najnowszym instalatorem podstawowym **InMage_UA_8.0.1.0_Windows_GA_28Sep2017_release.exe**.
  8. **Serwer źródłowy Systemu Linux**: Aby zaktualizować ujednoliconego agenta, skopiuj odpowiednią wersję pliku ujednoliconego agenta na serwer Linux i wyodrębnij go. W wyodrębnionym folderze uruchom **program /Install**.  Przykład: W przypadku serwera 6.7 64-bitowego RHEL skopiuj **UA_RHEL6-64_8.0.4.0_GA_Update_4_9035261_26Sep16.tar.gz** na serwer i wyodrębnij go. W wyodrębnionym folderze uruchom **program /Install**.


> [!NOTE]
> * Podstawowy instalator unified agenta (UA) dla systemu Windows został odświeżony w celu obsługi systemu Windows Server 2016. Nowy instalator **InMage_UA_8.0.1.0_Windows_GA_28Sep2017_release.exe** jest dostarczany z podstawowym pakietem Scout GA **(InMage_Scout_Standard_8.0.1 GA-Oct17.zip**). Ten sam instalator będzie używany dla wszystkich obsługiwanych wersji systemu Windows. 
> * Podstawowy instalator docelowy systemu Windows vContinuum & Master target został odświeżony w celu obsługi systemu Windows Server 2016. Nowy instalator **InMage_Scout_vContinuum_MT_8.0.1.0_Windows_GA_10Oct2017_release.exe** jest dostarczany z podstawowym pakietem Scout GA **(InMage_Scout_Standard_8.0.1 GA-Oct17.zip**). Ten sam instalator będzie używany do wdrażania głównego obiektu docelowego systemu Windows 2016 i głównego obiektu docelowego systemu Windows 2012R2.
> * System Windows Server 2016 na serwerze fizycznym nie jest obsługiwany przez program ASR Scout. Obsługuje tylko maszynę wirtualną VMware systemu Windows Server 2016. 
>

#### <a name="bug-fixes-and-enhancements"></a>Poprawki i ulepszenia błędów
- Ochrona po awarii nie powiedzie się dla maszyny Wirtualnej systemu Linux z listą dysków do replikacji jest pusta na końcu konfiguracji.

### <a name="site-recovery-scout-801-update-5"></a>Aktualizacja 5 Programu Site Recovery Scout 8.0.1
Aktualizacja Skauta 5 to zbiorcza aktualizacja. Zawiera wszystkie poprawki od aktualizacji 1 do aktualizacji 4 oraz nowe poprawki opisane poniżej.
- Poprawki z aktualizacji 4 do aktualizacji 5 programu Site Recovery Scout są przeznaczone specjalnie dla głównych składników docelowych i vContinuum.
- Jeśli serwery źródłowe, główne serwery docelowe, konfiguracja, proces i serwery RX są już uruchomione jako aktualizacja 4, a następnie zastosuj ją tylko na głównym serwerze docelowym. 

#### <a name="new-platform-support"></a>Nowa obsługa platformy
* Dodatek Service Pack 4(SP4) dla systemu SUSE Linux Enterprise Server 11
* SLES 11 SP4 64 bit **InMage_UA_8.0.1.0_SLES11-SP4-64_GA_13Apr2017_release.tar.gz** jest pakowany z podstawowym pakietem Scout GA (**InMage_Scout_Standard_8.0.1 GA.zip**). Pobierz pakiet GA z portalu, zgodnie z opisem w create a vault.


#### <a name="bug-fixes-and-enhancements"></a>Poprawki i ulepszenia błędów

* Poprawki zwiększające niezawodność obsługi klastra systemu Windows:
    * Naprawiono- Niektóre dyski klastrowe P2V MSCS stają się raw po odzyskaniu.
    * Naprawiono- Odzyskiwanie klastra P2V MSCS kończy się niepowodzeniem z powodu niezgodności kolejności dysków.
    * Naprawiono — operacja klastra MSCS w celu dodania dysków kończy się niepowodzeniem z błędem niezgodności rozmiaru dysku.
    * Naprawiono — sprawdzanie gotowości źródłowego klastra MSCS z mapowaniem LUN rdm kończy się niepowodzeniem w weryfikacji rozmiaru.
    * Naprawiono— ochrona klastra z jednym węzłem nie powiedzie się z powodu problemu z niezgodnością SCSI. 
    * Stała — ponowna ochrona serwera klastra P2V windows kończy się niepowodzeniem, jeśli znajdują się docelowe dyski klastrowe. 
    
* Naprawiono: Podczas ochrony po awarii, jeśli wybrany główny serwer docelowy nie jest na tym samym serwerze ESXi co chroniony komputer źródłowy (podczas ochrony do przodu), vContinuum odbiera niewłaściwy główny serwer docelowy podczas odzyskiwania po awarii i odzyskiwania operacja nie powiedzie się.

> [!NOTE]
> * Poprawki klastra P2V mają zastosowanie tylko do fizycznych klastrów MSCS, które są nowo chronione za pomocą aktualizacji 5 programu Site Recovery Scout. Aby zainstalować poprawki klastra w chronionych klastrach P2V MSCS ze starszymi aktualizacjami, wykonaj kroki uaktualniania wymienione w sekcji 12 [informacji o wersji programu Site Recovery Scout](https://aka.ms/asr-scout-release-notes).
> * jeśli w momencie ponownej ochrony ten sam zestaw dysków jest aktywny w każdym z węzłów klastra, jak wtedy, gdy były początkowo chronione, a następnie ponownie chronić fizyczny klaster MSCS może ponownie używać tylko istniejących dysków docelowych. Jeśli nie, użyj ręcznych kroków w sekcji 12 [site recovery scout release notes](https://aka.ms/asr-scout-release-notes), aby przenieść dyski po stronie docelowej do właściwej ścieżki magazynu danych, aby ponownie użyć podczas ponownej ochrony. Jeśli ponownie skrócenie klastra MSCS w trybie P2V bez postępuje zgodnie z instrukcjami uaktualniania, tworzy nowy dysk na docelowym serwerze ESXi. Należy ręcznie usunąć stare dyski z magazynu danych.
> * When a source SLES11 or SLES11 (with any service pack) server is rebooted gracefully, then manually mark the **root** disk replication pairs for re-synchronization. W interfejsie CX nie ma powiadomienia.Jeśli dysk główny nie zostanie oznaczony do ponownej synchronizyzacji, możesz zauważyć problemy z integralnością danych.


### <a name="azure-site-recovery-scout-801-update-4"></a>Usługa Azure Site Recovery Scout 8.0.1 Aktualizacja 4
Aktualizacja Skauta 4 to zbiorcza aktualizacja. Zawiera wszystkie poprawki od aktualizacji 1 do aktualizacji 3 oraz nowe poprawki opisane poniżej.

#### <a name="new-platform-support"></a>Nowa obsługa platformy

* Dodano obsługę vCenter/vSphere 6.0, 6.1 i 6.2
* Dodano obsługę tych systemów operacyjnych Linux:
  * Red Hat Enterprise Linux (RHEL) 7.0, 7.1 i 7.2
  * CentOS 7,0, 7,1 i 7,2
  * Red Hat Enterprise Linux (RHEL) 6.8
  * CentOS 6,8

> [!NOTE]
> RHEL/CentOS 7 64 bit **InMage_UA_8.0.1.0_RHEL7-64_GA_06Oct2016_release.tar.gz** jest pakowany z pakietem base Scout GA **InMage_Scout_Standard_8.0.1 GA.zip**. Pobierz pakiet Scout GA z portalu zgodnie z opisem w utworzeniu przechowalni.

#### <a name="bug-fixes-and-enhancements"></a>Poprawki i ulepszenia błędów

* Ulepszona obsługa zamykania dla następujących systemów operacyjnych Linux i klonów, aby zapobiec niepożądanym problemom z ponowną synchronizacją:
    * Red Hat Enterprise Linux (RHEL) 6.x
    * Oracle Linux (OL) 6.x
* W systemie Linux wszystkie uprawnienia dostępu do folderów w katalogu instalacji ujednoliconego agenta są teraz ograniczone tylko do użytkownika lokalnego.
* W systemie Windows poprawka dotycząca problemu z limitem czasu, który wystąpił podczas wystawiania wspólnych zakładek spójności rozproszonej, w mocno załadowanych aplikacjach rozproszonych, takich jak SQL Server i klastry share point.
* Poprawka związana z dziennikiem w instalatorze podstawowym serwera konfiguracji.
* Do głównego docelowego instalatora podstawowego systemu Windows dodano łącze do pobrania do vMware vCLI 6.0.
* Dodano dodatkowe kontrole i dzienniki dla zmian konfiguracji sieci podczas pracy awaryjnej i odzyskiwania po awarii.
* Poprawka dotycząca problemu, który powodował, że informacje o przechowywania nie były zgłaszane do serwera konfiguracji.  
* W przypadku klastrów fizycznych poprawka problemu, który powodował niepowodzenie zmiany rozmiaru woluminu w kreatorze vContinuum podczas zmniejszania woluminu źródłowego.
* Poprawka problemu z ochroną klastra, który nie powiódł się z powodu błędu: "Nie można znaleźć podpisu dysku", gdy dysk klastrowy jest dyskiem PRDM.
* Poprawka awarii serwera transportu cxps, spowodowane przez wyjątek poza zakresem.
* Kolumny nazwy serwera i adresu IP można teraz zamiecić na stronie **Instalacji wypychania** kreatora vContinuum.
* Ulepszenia interfejsu API RX:
  * Pięć najnowszych dostępnych wspólnych punktów spójności jest już dostępnych (tylko gwarantowane tagi).
  * Pojemność i wolne miejsce są wyświetlane dla wszystkich chronionych urządzeń.
  * Stan sterownika Scout na serwerze źródłowym jest dostępny.

> [!NOTE]
> * **InMage_Scout_Standard_8.0.1_GA.zip** pakiet bazowy posiada:
>     * Zaktualizowany instalator podstawowy serwera konfiguracji (**InMage_CX_8.0.1.0_Windows_GA_26Feb2015_release.exe**)
>     * Główny podstawowy instalator podstawowy systemu Windows (**InMage_Scout_vContinuum_MT_8.0.1.0_Windows_GA_26Feb2015_release.exe**).
>     * W przypadku wszystkich nowych instalacji należy użyć nowego serwera konfiguracji i głównych bitów GA głównego systemu Windows.
> * Aktualizacja 4 może być stosowana bezpośrednio na 8.0.1 GA.
> * Nie można wycofać serwera konfiguracji i aktualizacji RX po ich zastosowaniu.


### <a name="azure-site-recovery-scout-801-update-3"></a>Usługa Azure Site Recovery Scout 8.0.1 Aktualizacja 3

Wszystkie aktualizacje usługi Site Recovery są zbiorcze. Aktualizacja 3 zawiera wszystkie poprawki z aktualizacji 1 i aktualizacji 2. Aktualizacja 3 może być stosowana bezpośrednio w 8.0.1 GA. Nie można wycofać serwera konfiguracji i aktualizacji RX po ich zastosowaniu.

#### <a name="bug-fixes-and-enhancements"></a>Poprawki i ulepszenia błędów
Aktualizacja 3 rozwiązuje następujące problemy:

* Serwer konfiguracji i RX nie są zarejestrowane w przechowalni, gdy są one za serwerem proxy.
* Liczba godzin, w których cel punktu odzyskiwania (RPO) nie został osiągnięty, nie jest aktualizowana w raporcie kondycji.
* Serwer konfiguracji nie jest synchronizowany z rx, gdy szczegóły sprzętu ESX lub szczegóły sieci zawierają żadnych znaków UTF-8.
* Kontrolery domeny systemu Windows Server 2008 R2 nie uruchamiają się po odzyskaniu.
* Synchronizacja w trybie offline nie działa zgodnie z oczekiwaniami.
* Po przejściu w tryb failover maszyny Wirtualnej usunięcie pary replikacji nie postępuje w konsoli serwera konfiguracji przez długi czas. Użytkownicy nie mogą ukończyć operacji powrotu po awarii ani wznowić.
* Ogólne operacje migawki przez zadanie spójności zostały zoptymalizowane, aby zmniejszyć rozłączenia aplikacji, takich jak klienci programu SQL Server.
* Poprawiono wydajność narzędzia spójność (VACP.exe). Zmniejszono użycie pamięci wymagane do tworzenia migawek w systemie Windows.
* Usługa instalacji wypychanej ulega awarii, gdy hasło jest większe niż 16 znaków.
* VContinuum nie sprawdza i nie monituje o nowe poświadczenia vCenter, gdy poświadczenia są modyfikowane.
* W systemie Linux główny menedżer docelowej pamięci podręcznej (cachemgr) nie pobiera plików z serwera przetwarzania. Powoduje to ograniczanie pary replikacji.
* Jeśli kolejność dysków klastra trybu failover (MSCS) nie jest taka sama we wszystkich węzłach, replikacja nie jest ustawiona dla niektórych woluminów klastra. Aby skorzystać z tej poprawki, należy ponownie przekazać plik klastra.  
* Funkcjonalność SMTP nie działa zgodnie z oczekiwaniami, po uaktualnieniu RX z Scout 7.1 do Scout 8.0.1.
* Więcej statystyk zostały dodane w dzienniku dla operacji wycofywania, aby śledzić czas, który został ukończony.
* Dodano obsługę systemów operacyjnych Linux na serwerze źródłowym:
  * Red Hat Enterprise Linux (RHEL) 6 aktualizacja 7
  * Aktualizacja CentOS 6 7
* Serwer konfiguracji i konsole RX wyświetlają teraz powiadomienia o parze, która przechodzi w tryb bitmapy.
* W rx dodano następujące poprawki zabezpieczeń:
    * Pomijanie autoryzacji poprzez manipulowanie parametrami: Ograniczony dostęp do niestosujących się użytkowników.
    * Fałszowanie żądań między witrynami: zaimplementowano koncepcję tokenu strony i generuje ona losowo dla każdej strony. Oznacza to, że istnieje tylko jednokrotne zalogowanie się na przykład dla tego samego użytkownika, a odświeżanie strony nie działa. Zamiast tego przekierowuje do pulpitu nawigacyjnego.
    * Złośliwe przesyłanie plików: Pliki są ograniczone do określonych rozszerzeń: z, aiff, asf, avi, bmp, csv, doc, docx, fla, flv, gif, gz, gzip, jpeg, jpg, log, mid, mov, mp3, mp4, mpc, mpeg, mpg, ods, odt, pdf, png, ppt, pptx, pxd, qt, ram, rar, rm, rmi, rmvb, rtf , sdc, sitd, swf, sxc, sxw, tar, tgz, tif, tiff, txt, vsd, wav, wma, wmv, xls, xlsx, xml i zip.
    * Trwałe skrypty między lokacjami: Dodano weryfikacje danych wejściowych.

### <a name="azure-site-recovery-scout-801-update-2-update-03dec15"></a>Usługa Azure Site Recovery Scout 8.0.1 Aktualizacja 2 (aktualizacja 03Dec15)

Poprawki w aktualizacji 2 obejmują:

* **Serwer konfiguracji:** Problemy, które uniemożliwiały działanie funkcji bezpłatnego pomiaru 31 dni zgodnie z oczekiwaniami, gdy serwer konfiguracji został zarejestrowany w magazynie usługi Azure Site Recovery.
* **Ujednolicony agent: Naprawiono**problem w aktualizacji 1, który powodował, że aktualizacja nie była instalowana na głównym serwerze docelowym podczas uaktualniania z wersji 8.0 do 8.0.1.

### <a name="azure-site-recovery-scout-801-update-1"></a>Usługa Azure Site Recovery Scout 8.0.1 Aktualizacja 1
Aktualizacja 1 zawiera następujące poprawki błędów i nowe funkcje:

* 31 dni bezpłatnej ochrony na wystąpienie serwera. Dzięki temu można przetestować funkcjonalność lub skonfigurować proof-of-concept.
* Wszystkie operacje na serwerze, w tym pracy awaryjnej i powrotu po awarii, są bezpłatne przez pierwsze 31 dni. Czas rozpoczyna się, gdy serwer jest najpierw chroniony za pomocą programu Site Recovery Scout. Od 32 dnia każdy chroniony serwer jest naliczany według standardowej stawki za wystąpienie dla ochrony usługi Site Recovery do witryny należącej do klienta.
* W dowolnym momencie liczba serwerów chronionych, które są obecnie naliczane, jest dostępna na **pulpicie nawigacyjnym** w przechowalni.
* Dodano obsługę vSphere Command-Line Interface (vCLI) 5.5 Update 2.
* Dodano obsługę tych systemów operacyjnych Linux na serwerze źródłowym:
    * RHEL 6 Aktualizacja 6
    * RHEL 5 Aktualizacja 11
    * Aktualizacja CentOS 6
    * Aktualizacja CentOS 5 11
* Poprawki błędów w celu rozwiązania następujących problemów:
  * Rejestracja przechowalni kończy się niepowodzeniem dla serwera konfiguracji lub serwera RX.
  * Woluminy klastra nie są wyświetlane zgodnie z oczekiwaniami, gdy klastrowane maszyny wirtualne są ponownie zwiększane po ich wznowieniu.
  * Powrót po awarii kończy się niepowodzeniem, gdy główny serwer docelowy jest obsługiwany na innym serwerze ESXi z lokalnych maszyn wirtualnych produkcyjnych.
  * Uprawnienia do plików konfiguracji są zmieniane podczas uaktualniania do 8.0.1. Ta zmiana wpływa na ochronę i operacje.
  * Próg ponownej synchronizacji nie jest wymuszany zgodnie z oczekiwaniami, co powoduje niespójne zachowanie replikacji.
  * Ustawienia RPO nie są wyświetlane poprawnie w konsoli serwera konfiguracji. Wartość nieskompresowanych danych niepoprawnie pokazuje wartość skompresowaną.
  * Operacja Usuń nie usuwa zgodnie z oczekiwaniami w kreatorze vContinuum, a replikacja nie jest usuwana z konsoli serwera konfiguracji.
  * W kreatorze vContinuum dysk jest automatycznie niezaznaczony po kliknięciu **przycisku Szczegóły** w widoku dysku podczas ochrony maszyn wirtualnych MSCS.
  * W scenariuszu fizyczno-wirtualnym (P2V) wymagane usługi HP (takie jak CIMnotify i CqMgHost) nie są przenoszone do ręcznego odzyskiwania maszyn wirtualnych. Ten problem powoduje dodatkowy czas rozruchu.
  * Ochrona maszyn wirtualnych systemu Linux kończy się niepowodzeniem, gdy na głównym serwerze docelowym znajduje się więcej niż 26 dysków.

