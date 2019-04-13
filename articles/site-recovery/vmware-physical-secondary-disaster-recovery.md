---
title: Konfigurowanie odzyskiwania po awarii maszyn wirtualnych VMware lub serwerów fizycznych do lokacji dodatkowej za pomocą usługi Azure Site Recovery | Dokumentacja firmy Microsoft
description: Dowiedz się, jak skonfigurować odzyskiwanie po awarii w przypadku maszyn wirtualnych VMware lub Windows i Linux serwerów fizycznych do lokacji dodatkowej za pomocą usługi Azure Site Recovery.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
services: site-recovery
ms.topic: conceptual
ms.date: 12/31/2018
ms.author: raynew
ms.openlocfilehash: 1a61a825ce223282e85c9262c613814530ba130f
ms.sourcegitcommit: 1c2cf60ff7da5e1e01952ed18ea9a85ba333774c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/12/2019
ms.locfileid: "59527306"
---
# <a name="set-up-disaster-recovery-of-on-premises-vmware-virtual-machines-or-physical-servers-to-a-secondary-site"></a>Konfigurowanie odzyskiwania po awarii maszyn wirtualnych programu VMware w środowisku lokalnym lub serwerów fizycznych do lokacji dodatkowej

InMage Scout w [usługi Azure Site Recovery](site-recovery-overview.md) udostępnia w czasie rzeczywistym replikacji między lokalną witrynami programu VMware. InMage Scout znajduje się w subskrypcji usługi Azure Site Recovery.

## <a name="end-of-support-announcement"></a>Koniec okresu objęcia wsparciem anonsu

Scenariusz replikacji między lokalnych zasobów programu VMware lub fizycznych centrów danych usługi Azure Site Recovery zbliża się koniec okresu objęcia wsparciem.

-   Od sierpnia 2018 r. scenariusza, nie można skonfigurować w magazynie usługi Recovery Services, a oprogramowanie InMage Scout, nie można pobrać z magazynu. Istniejące wdrożenia będą obsługiwane. 
-   Z 2020 r. do 31 grudnia nie będą obsługiwane scenariusza.
- Istniejących partnerów można dołączania nowych klientów do scenariusza, aż do zakończenia pomocy technicznej.

Podczas 2018 r. i 2019 dwie aktualizacje zostaną zwolnione: 

-   Aktualizacja 7: Rozwiązuje problemy z konfiguracją i zgodnością sieciowym i zapewnia obsługę protokołu TLS 1.2.
-   Aktualizacja 8: Dodaje obsługę 7.3/7.4/7.5 RHEL/CentOS systemów operacyjnych Linux i SUSE 12

Po aktualizacji 8 żadne dodatkowe aktualizacje zostaną wydane. Będzie obsługa ograniczona poprawek dla systemów operacyjnych, dodane w aktualizacji 8 i poprawki błędów w oparciu o najlepszy nakład pracy.

Usługa Azure Site Recovery w dalszym ciągu wprowadzać innowacje, zapewniając bezproblemowe oraz najlepsze w swojej klasie rozwiązania DRaaS klientów korzystających z programu VMware i funkcji Hyper-V za pomocą platformy Azure jako lokacji odzyskiwania po awarii. Firma Microsoft zaleca, aby istniejący InMage / klientów ASR Scout należy wziąć pod uwagę przy użyciu usługi Azure Site Recovery z programu VMware na platformę Azure, potrzeby ich ciągłość prowadzenia działalności biznesowej. Usługa Site Recovery platformy Azure z programu VMware na platformę Azure to rozwiązanie odzyskiwania po awarii klasy korporacyjnej dla aplikacji programu VMware, które oferuje RPO i RTO minut, obsługa wielu maszyn wirtualnych aplikacji replikację i odzyskiwanie, bezproblemowe dołączania kompleksowe monitorowanie i znaczącą korzyścią całkowity koszt posiadania.

### <a name="scenario-migration"></a>Scenariusz migracji
Jako alternatywę zaleca się konfigurowania odzyskiwania po awarii dla lokalnych maszyn wirtualnych z programu VMware i maszyn fizycznych przez replikowanie ich na platformę Azure. To w następujący sposób:

1.  Przejrzyj poniższe porównanie szybkie. Można replikować maszyn lokalnych, musisz upewnij się, że spełniają one [wymagania](./vmware-physical-azure-support-matrix.md#replicated-machines) replikacji do platformy Azure. Jeśli replikujesz maszyny wirtualne VMware, firma Microsoft zaleca przejrzenie [wytyczne dotyczące planowania pojemności na potrzeby](./site-recovery-plan-capacity-vmware.md)i uruchom [narzędzia planista wdrażania](./site-recovery-deployment-planner.md) na wymagania dotyczące pojemności tożsamości i sprawdź zgodność.
2.  Po uruchomieniu Planisty wdrożenia, można skonfigurować replikacji: o dla maszyn wirtualnych VMware, postępuj zgodnie z tymi samouczkami, aby [przygotowywanie platformy Azure](./tutorial-prepare-azure.md), [przygotowanie środowiska lokalnego VMware](./vmware-azure-tutorial-prepare-on-premises.md), i [Konfigurowanie odzyskiwanie po awarii](./vmware-azure-tutorial-prepare-on-premises.md).
o maszyn fizycznych, postępuj zgodnie z tym [samouczek](./physical-azure-disaster-recovery.md).
3.  Po maszyny replikowania na platformę Azure, możesz uruchomić [próbnego odzyskiwania po awarii](./site-recovery-test-failover-to-azure.md) się upewnić, że wszystko działa zgodnie z oczekiwaniami.

### <a name="quick-comparison"></a>Szybkie porównania

**Funkcja** | **Replikacja do platformy Azure** |**Replikacja między centrami danych firmy VMware**
--|--|--
**Wymagane składniki** |Usługa mobilności na replikowanych maszynach. Lokalny serwer konfiguracji, serwer przetwarzania i główny serwer docelowy. Serwer tymczasowy przetwarzania na platformie Azure do powrotu po awarii.|Usługa mobilności, serwer przetwarzania, serwer konfiguracji i główny element docelowy
**Konfiguracji i aranżacji** |Magazyn usługi Recovery Services w witrynie Azure portal | Za pomocą vContinuum 
**Replikowane** |Dysk (Windows i Linux) |Volume-Windows<br> Disk-Linux
**Udostępniony klaster dysków** |Nieobsługiwane|Obsługiwane
**Współczynnik zmian danych limitów (średnia)** |10 danych w MB/s na dysk<br> Dane 25MB/s na maszynę Wirtualną<br> [Dowiedz się więcej](./site-recovery-vmware-deployment-planner-analyze-report.md#azure-site-recovery-limits) | Dane > 10 MB/s na dysk  <br> Dane > 25 MB/s na maszynę Wirtualną
**Monitorowanie** |Z witryny Azure portal|Z CX (serwer konfiguracji)
**Matryca obsługi replikacji serwera** | [Kliknij tutaj, aby uzyskać szczegółowe informacje](./vmware-physical-azure-support-matrix.md)|[Pobierz macierzy zgodne Scout usługi ASR](https://aka.ms/asr-scout-cm)


## <a name="prerequisites"></a>Wymagania wstępne
W celu ukończenia tego samouczka:

- [Przegląd](vmware-physical-secondary-support-matrix.md) wymaganiami dotyczącymi obsługi dla wszystkich składników.
- Upewnij się, że maszyn, które mają być replikowane są zgodne z [replikowane maszyny obsługi](vmware-physical-secondary-support-matrix.md#replicated-vm-support).


## <a name="download-and-install-component-updates"></a>Pobierz i zainstaluj aktualizacje składników

 Przejrzyj i zainstaluj najnowszą wersję [aktualizacje](#updates). Aktualizacje, należy zainstalować na serwerach w następującej kolejności:

1. RX serwera (jeśli dotyczy)
2. Serwery konfiguracji
3. Serwerów przetwarzania
4. Serwery głównego serwera docelowego
5. serwery vContinuum
6. Serwer źródłowy (zarówno Windows, jak i serwerów z systemem Linux)

Zainstaluj następujące aktualizacje:

> [!NOTE]
>Wszystkie składniki programu Scout pliku zaktualizowanej wersji nie może być taka sama w pliku zip aktualizacji. Starsza wersja oznacza, że nie wprowadzono zmian w składniku od czasu poprzedniej aktualizacji tej aktualizacji.

Pobierz [aktualizacji](https://aka.ms/asr-scout-update7) pliku zip i [MySQL i PHP uaktualniania](https://aka.ms/asr-scout-u7-mysql-php-manualupgrade) plików konfiguracyjnych. Aktualizuj plik zip zawiera wszystkie bazy danych binarnych i zbiorcze uaktualnienia plików binarnych w następujących składników: 
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
  1. Wyodrębnij pliki z rozszerzeniem .zip.
  2. **Serwer RX**: Kopiuj **RX_8.0.7.0_GA_Update_7_2965621_28Dec18.tar.gz** serwerowi RX i Wyodrębnij jego zawartość. W folderze wyodrębnione Uruchom **/Install**.
  3. **Serwer konfiguracji i serwerem przetwarzania**: Kopiuj **CX_Windows_8.0.7.0_GA_Update_7_2965621_28Dec18.exe** do serwera konfiguracji i serwerem przetwarzania. Kliknij dwukrotnie, aby go uruchomić.<br>
  4. **Windows główny serwer docelowy**: Aby zaktualizować program unified agent, skopiuj **InMage_UA_8.0.7.0_Windows_GA_27Dec2018_release.exe** do serwera. Go dwukrotnie, aby go uruchomić. Ten sam plik może również dla nowej instalacji. Ta sama aktualizacja program unified agent jest również stosowana do serwera źródłowego.
  Aktualizacja nie jest konieczne stosowanie we wzorcu docelowej przygotowanym za pomocą **InMage_Scout_vContinuum_MT_8.0.7.0_Windows_GA_27Dec2018_release.exe** , jak jest to nowy Instalator GA z najnowszymi zmianami.
  5. **Serwer vContinuum**:  Kopiuj **InMage_Scout_vContinuum_MT_8.0.7.0_Windows_GA_27Dec2018_release.exe** do serwera.  Upewnij się, że zostało zamknięte kreatora vContinuum. Kliknij dwukrotnie plik, aby go uruchomić.
  6. **Linux główny serwer docelowy**: Aby zaktualizować program unified agent, skopiuj **InMage_UA_8.0.7.0_RHEL6-64_GA_03Dec2018_release.tar.gz** na serwer docelowy z węzłem głównym systemu Linux i Wyodrębnij jego zawartość. W folderze wyodrębnione Uruchom **/Install**.
  7. **Serwer źródłowy Windows**: Aby zaktualizować program unified agent, skopiuj **InMage_UA_8.0.7.0_Windows_GA_27Dec2018_release.exe** na serwerze źródłowym. Kliknij dwukrotnie plik, aby go uruchomić. 
  8. **Serwer źródłowy z systemem Linux**: Aby zaktualizować program unified agent, skopiuj odpowiedniej wersji pliku program unified agent na serwerze z systemem Linux i Wyodrębnij jego zawartość. W folderze wyodrębnione Uruchom **/Install**.  Przykład: Systemu RHEL 6.7 64-bitowego serwera, kopia **InMage_UA_8.0.7.0_RHEL6-64_GA_03Dec2018_release.tar.gz** z serwerem i Wyodrębnij jego zawartość. W folderze wyodrębnione Uruchom **/Install**.
  9. Po uaktualnieniu serwera konfiguracji, serwer przetwarzania i serwera RX z wyżej wymienionych instalatory bibliotek języka PHP i MySQL musi zostać uaktualniony ręcznie przy użyciu czynności wymienionych w sekcji 7.4 [Przewodniku szybkiej instalacji](https://aka.ms/asr-scout-quick-install-guide).

## <a name="enable-replication"></a>Włączanie replikacji

1. Konfigurowanie replikacji między źródłem a celem witrynami programu VMware.
2. Zapoznaj się z następującymi dokumentami, aby dowiedzieć się więcej na temat instalacji, ochrony i odzyskiwania:

   * [Informacje o wersji](https://aka.ms/asr-scout-release-notes)
   * [Macierz zgodności](https://aka.ms/asr-scout-cm)
   * [Podręcznik użytkownika](https://aka.ms/asr-scout-user-guide)
   * [Podręcznik użytkownika RX](https://aka.ms/asr-scout-rx-user-guide)
   * [Przewodniku szybkiej instalacji](https://aka.ms/asr-scout-quick-install-guide)
   * [Uaktualnianie biblioteki MYSQL i PHP](https://aka.ms/asr-scout-u7-mysql-php-manualupgrade)

## <a name="updates"></a>Aktualizacje

### <a name="site-recovery-scout-801-update-7"></a>Aktualizacja usługi Site Recovery Scout 8.0.1 7 
Aktualizacja: Do 31 grudnia 2018 r. Pobierz [Scout update 7](https://aka.ms/asr-scout-update7).
Aktualizacja programu Scout 7 jest pełna Instalatora, który może służyć do nowej instalacji, a także aby uaktualnić istniejący agenci/MT, które znajdują się na poprzednie aktualizacje (z wersją Update 1 do 6 aktualizacji). Zawiera ona wszystkie poprawki z wersją Update 1 do aktualizacji 6 plus nowych usprawnień i ulepszeń, które opisano poniżej.
 
#### <a name="new-features"></a>Nowe funkcje
* Zgodności ze standardami PCI
* Protokół TLS 1.2 w pomocy technicznej

#### <a name="bug-and-security-fixes"></a>Usterka i poprawki zabezpieczeń
* Poprawiono: Windows klastra/autonomicznych maszyn ma niepoprawną konfigurację adresu IP podczas odzyskiwania /-odzyskiwania po awarii.
* Poprawiono: Czasami Dodaj dysk operacja zakończy się niepowodzeniem dla klastra V2V.
* Poprawiono: vContinuum kreatora zablokowania podczas fazy odzyskiwania Jeśli główny element docelowy jest system Windows Server 2016
* Poprawiono: Problemy z zabezpieczeniami MySQL zostaną skorygowane przez uaktualnienie MySQL do wersji 5.7.23

#### <a name="manual-upgrade-for-php-and-mysql-on-csps-and-rx"></a>Ręczne uaktualnienie dla języka PHP i MySQL na CS, PS i ODBIERANIA
Platformy obsługi skryptów PHP powinny zostać uaktualnione do wersji 7.2.10 na serwerze konfiguracji, serwer przetwarzania i ODBIERANIA.
System zarządzania bazami danych MySQL, powinny zostać uaktualnione do wersji 5.7.23 na serwerze konfiguracji, serwer przetwarzania i ODBIERANIA.
Wykonaj poniższe kroki ręczne, podany w [Przewodniku szybkiej instalacji](https://aka.ms/asr-scout-quick-install-guide) uaktualnienia wersji języka PHP i MySQL.

### <a name="site-recovery-scout-801-update-6"></a>Aktualizacja usługi Site Recovery Scout 8.0.1 6 
Aktualizacja: 12 października 2017 r.

Pobierz [Scout update 6](https://aka.ms/asr-scout-update6).

Aktualizacja programu Scout 6 jest aktualizacją zbiorczą. Zawiera ona wszystkie poprawki z zakresu od 1 do aktualizacji Update 5, oraz nowych usprawnień i ulepszeń, które opisano poniżej. 

#### <a name="new-platform-support"></a>Obsługa nowych platform
* Dodano obsługę źródła systemu Windows Server 2016
* Dodano obsługę w następujących systemach operacyjnych Linux:
    - Red Hat Enterprise Linux (RHEL) 6.9
    - CentOS 6.9
    - Oracle Linux 5.11
    - Oracle Linux 6.8
* Dodano obsługę dla 6.5 Centrum oprogramowania VMware

Zainstaluj następujące aktualizacje:

> [!NOTE]
>Wszystkie składniki programu Scout pliku zaktualizowanej wersji nie może być taka sama w pliku zip aktualizacji. Starsza wersja oznacza, że nie wprowadzono zmian w składniku od czasu poprzedniej aktualizacji tej aktualizacji.

Pobierz [aktualizacji](https://aka.ms/asr-scout-update6) pliku zip. Plik zawiera następujące składniki: 
- RX_8.0.4.0_GA_Update_4_8725872_16Sep16.tar.gz
- CX_Windows_8.0.6.0_GA_Update_6_13746667_18Sep17.exe
- UA_Windows_8.0.5.0_GA_Update_5_11525802_20Apr17.exe
- UA_RHEL6-64_8.0.4.0_GA_Update_4_9035261_26Sep16.tar.gz
- vCon_Windows_8.0.6.0_GA_Update_6_11525767_21Sep17.exe
- Usługa bits update4 UA systemem RHEL5, OL5, OL6, SUSE 10, SUSE 11: UA_\<Linux OS>_8.0.4.0_GA_Update_4_9035261_26Sep16.tar.gz
  1. Wyodrębnij pliki z rozszerzeniem .zip.
  2. **Serwer RX**: Kopiuj **RX_8.0.4.0_GA_Update_4_8725872_16Sep16.tar.gz** serwerowi RX i Wyodrębnij jego zawartość. W folderze wyodrębnione Uruchom **/Install**.
  3. **Serwer konfiguracji i serwerem przetwarzania**: Kopiuj **CX_Windows_8.0.6.0_GA_Update_6_13746667_18Sep17.exe** do serwera konfiguracji i serwerem przetwarzania. Kliknij dwukrotnie, aby go uruchomić.<br>
  4. **Windows główny serwer docelowy**: Aby zaktualizować program unified agent, skopiuj **UA_Windows_8.0.5.0_GA_Update_5_11525802_20Apr17.exe** do serwera. Go dwukrotnie, aby go uruchomić. Ta sama aktualizacja program unified agent jest również stosowana do serwera źródłowego. Źródło zostało zaktualizowane do wersji Update 4, należy zaktualizować program unified agent.
  Aktualizacja nie jest konieczne stosowanie we wzorcu docelowej przygotowanym za pomocą **InMage_Scout_vContinuum_MT_8.0.1.0_Windows_GA_10Oct2017_release.exe** , jak jest to nowy Instalator GA z najnowszymi zmianami.
  5. **Serwer vContinuum**:  Kopiuj **vCon_Windows_8.0.6.0_GA_Update_6_11525767_21Sep17.exe** do serwera.  Upewnij się, że zostało zamknięte kreatora vContinuum. Kliknij dwukrotnie plik, aby go uruchomić.
  Aktualizacja nie trzeba stosować na główny element docelowy przygotowanym za pomocą **InMage_Scout_vContinuum_MT_8.0.1.0_Windows_GA_10Oct2017_release.exe** , jak jest to nowy Instalator GA z najnowszymi zmianami.
  6. **Linux główny serwer docelowy**: Aby zaktualizować program unified agent, skopiuj **UA_RHEL6 64_8.0.4.0_GA_Update_4_9035261_26Sep16.tar.gz** do poziomu głównego serwer docelowy i Wyodrębnij jego zawartość. W folderze wyodrębnione Uruchom **/Install**.
  7. **Serwer źródłowy Windows**: Aby zaktualizować program unified agent, skopiuj **UA_Windows_8.0.5.0_GA_Update_5_11525802_20Apr17.exe** na serwerze źródłowym. Kliknij dwukrotnie plik, aby go uruchomić. 
  Nie trzeba zainstalować agenta z aktualizacją 5 na serwerze źródłowym, jeśli został już zaktualizowany do wersji Update 4 lub źródła agent jest instalowany z najnowszą wersję Instalatora podstawowy **InMage_UA_8.0.1.0_Windows_GA_28Sep2017_release.exe**.
  8. **Serwer źródłowy z systemem Linux**: Aby zaktualizować program unified agent, skopiuj odpowiedniej wersji pliku program unified agent na serwerze z systemem Linux i Wyodrębnij jego zawartość. W folderze wyodrębnione Uruchom **/Install**.  Przykład: Systemu RHEL 6.7 64-bitowego serwera, kopia **UA_RHEL6 64_8.0.4.0_GA_Update_4_9035261_26Sep16.tar.gz** z serwerem i Wyodrębnij jego zawartość. W folderze wyodrębnione Uruchom **/Install**.


> [!NOTE]
> * Podstawowy Unified Agent(UA) Instalator Windows została odświeżona do działu pomocy technicznej systemu Windows Server 2016. Nowy Instalator **InMage_UA_8.0.1.0_Windows_GA_28Sep2017_release.exe** jest spakowana ze pakiet podstawowy Scout GA (**InMage_Scout_Standard_8.0.1 GA-Oct17.zip**). Ten sam Instalator będzie używany dla wszystkich obsługiwanych wersji Windows. 
> * Podstawowy Windows vContinuum & główny element docelowy Instalatora została odświeżona do działu pomocy technicznej systemu Windows Server 2016. Nowy Instalator **InMage_Scout_vContinuum_MT_8.0.1.0_Windows_GA_10Oct2017_release.exe** jest spakowana ze pakiet podstawowy Scout GA (**InMage_Scout_Standard_8.0.1 GA-Oct17.zip**). Ten sam Instalator będzie służyć do wdrażania systemu Windows 2016 głównego serwera docelowego i systemu Windows 2012 R2 głównego serwera docelowego.
> * Windows server 2016 na serwerze fizycznym, nie jest obsługiwana przez program ASR Scout. Obsługuje ona tylko systemu Windows Server 2016 maszyny Wirtualnej VMware. 
>

#### <a name="bug-fixes-and-enhancements"></a>Poprawki błędów i ulepszenia
- Powrót po awarii ochrony nie powiodło się dla maszyny Wirtualnej systemu Linux z listy dysków do replikacji jest pusty po zakończeniu konfiguracji.

### <a name="site-recovery-scout-801-update-5"></a>Aktualizacja usługi Site Recovery Scout 8.0.1 5
Aktualizacja programu Scout 5 jest aktualizacją zbiorczą. Zawiera on wszystkie poprawki z zakresu od 1 do aktualizacji Update 4 i nowe poprawki opisane poniżej.
- Poprawki z witryny odzyskiwania Scout Update 4 do aktualizacji 5 są specjalnie dla obiektu docelowego i vContinuum składniki.
- Jeśli serwery źródłowe, główny serwer docelowy, konfiguracji, procesy i RX serwery są już uruchomione Update 4, a następnie zastosować je tylko na głównym serwerze docelowym. 

#### <a name="new-platform-support"></a>Obsługa nowych platform
* SUSE Linux Enterprise Server 11 Service Pack 4(SP4)
* SLES 11 z dodatkiem SP4 64 bitowych **InMage_UA_8.0.1.0_SLES11-SP4-64_GA_13Apr2017_release.tar.gz** jest spakowana ze pakiet podstawowy Scout GA (**InMage_Scout_Standard_8.0.1 GA.zip**). Pobieranie pakietu wersji ogólnie dostępnej z poziomu portalu, zgodnie z opisem w Utwórz magazyn.


#### <a name="bug-fixes-and-enhancements"></a>Poprawki błędów i ulepszenia

* Poprawki dla zwiększenia klastra Windows obsługuje niezawodności:
    * Naprawiono-niektóre P2V MSCS klastra dysków stają się RAW po odzyskaniu danych.
    * Fixed-P2V MSCS klastra odzyskiwania kończy się niepowodzeniem z powodu niezgodności zamówienia dysku.
    * Naprawiono - MSCS klastra operacji dodawania dysków kończy się niepowodzeniem z błędem niezgodność rozmiaru dysku.
    * Naprawiono kontroli gotowości dla źródła, w klastrze MSCS za pomocą mapowania jednostki LUN RDM nie powiedzie się w zakresie rozmiaru weryfikacji.
    * Fixed-jednego węzła klastra ochrony kończy się niepowodzeniem z powodu problemu z niezgodności SCSI. 
    * Fixed-ponownej ochrony serwera klastra Windows P2V kończy się niepowodzeniem, jeśli dyski klastra docelowego. 
    
* Poprawiono: Podczas ochrony podczas powrotu po awarii jeśli wybrany główny serwer docelowy nie jest na tym samym serwerze ESXi komputera chronionego źródła (w ramach ochrony do przodu) vContinuum przejmuje niewłaściwego głównego serwera docelowego podczas odzyskiwania podczas powrotu po awarii i odzyskiwania, a następnie Operacja nie powiedzie się.

> [!NOTE]
> * Poprawki klastra P2V dotyczą tylko klastry fizyczne MSCS nowo chronionych za pomocą witryny odzyskiwania Scout aktualizacja Update 5. Aby zainstalować poprawki klastra klastrach P2V MSCS chronionych za pomocą aktualizacji w starszych, postępuj zgodnie z uaktualnienia kroki opisane w sekcji 12 [informacje o wersji programu Scout odzyskiwania lokacji](https://aka.ms/asr-scout-release-notes).
> * Jeśli podczas ponownej ochrony, ten sam zestaw dysków są aktywne na każdym z węzłów klastra, jak podczas początkowego chronione, następnie ponownej ochrony klastrze MSCS fizycznym tylko ich ponownie istniejące dyski docelowe. Jeśli nie, użyj ręczne wykonanie czynności w sekcji 12 [informacje o wersji programu Scout odzyskiwania lokacji](https://aka.ms/asr-scout-release-notes), aby przenieść dyski po stronie docelowej do ścieżki poprawny magazyn danych, do ponownego użycia podczas ponownej ochrony. Jeśli możesz ponownie włączyć ochronę klastrze MSCS P2V w trybie bez zgodnie z instrukcjami uaktualnienia, tworzy nowy dysk na docelowym serwerze ESXi. Należy ręcznie usunąć stare dyski z magazynu danych.
> * Gdy serwer źródłowy SLES11 lub SLES11 (z dowolny dodatek service pack) jest uruchamiany ponownie bez problemu zmieniała, następnie ręcznie oznaczyć **głównego** dysku par replikacji ponownej synchronizacji. Nie ma żadnych powiadomień w interfejsie CX. Jeśli nie dysku głównego do ponownej synchronizacji, można zauważyć problemy z integralnością danych.


### <a name="azure-site-recovery-scout-801-update-4"></a>Azure Site Recovery Scout 8.0.1 Update 4
Scout Update 4 jest aktualizacją zbiorczą. Zawiera wszystkie poprawki z wersją Update 1 do wersji Update 3 i nowe poprawki opisane poniżej.

#### <a name="new-platform-support"></a>Obsługa nowych platform

* Dodano obsługę dla vCenter/vSphere w wersji 6.0 i 6.1 6.2
* Dodano obsługę dla tych systemów operacyjnych Linux:
  * Red Hat Enterprise Linux (RHEL) 7.0, 7.1 i 7.2
  * CentOS 7.0, 7.1 i 7.2
  * Red Hat Enterprise Linux (RHEL) 6.8
  * CentOS 6.8

> [!NOTE]
> RHEL/CentOS 7 64-bitowy **InMage_UA_8.0.1.0_RHEL7-64_GA_06Oct2016_release.tar.gz** jest spakowana ze pakiet podstawowy Scout GA **InMage_Scout_Standard_8.0.1 GA.zip**. Pobieranie pakietu programu Scout GA z portalu zgodnie z opisem w Utwórz magazyn.

#### <a name="bug-fixes-and-enhancements"></a>Poprawki błędów i ulepszenia

* Zamknięcie Ulepszona obsługa dla następujących systemów operacyjnych Linux i klonów, aby zapobiec problemom niechciane ponownej synchronizacji:
    * Red Hat Enterprise Linux (RHEL) 6.x
    * Oracle Linux (OL) 6.x
* Dla systemu Linux wszystkie uprawnienia dostępu do folderu w katalogu instalacyjnym program unified agent są obecnie ograniczone do tylko użytkownika lokalnego.
* W Windows, poprawkę rozwiązującą chronometrażu się problem, który wystąpił podczas wystawiania wspólnej zakładek rozproszonych spójności, w silnie obciążony aplikacji rozproszonych, takich jak klastry SQL Server i SharePoint.
* Dziennik poprawka powiązane w Instalatorze podstawowego serwera konfiguracji.
* Link pobierania do programu VMware vCLI 6.0 został dodany do Windows Instalator bazowej głównego elementu docelowego.
* Dodatkowe czynności kontrolne i dzienników zostały dodane do zmiany konfiguracji sieci, podczas trybu failover i odzyskiwanie po awarii próbne operacje odzyskiwania.
* Poprawkę rozwiązującą problem powodujący, przechowywania informacji o zgłaszane do serwera konfiguracji.  
* W przypadku klastrów fizycznych poprawkę rozwiązującą problem, który spowodował niepowodzenie kreatora vContinuum podczas zmniejszania woluminu źródłowego zmianę rozmiaru woluminu.
* Poprawkę rozwiązującą problem ochrony klastra, który uległ awarii z powodu błędu: "Nie można odnaleźć podpisu dysku", gdy dysk klastrowy znajduje się dysk PRDM.
* Poprawki dotyczące awarii serwera transportu cxps, spowodowane przez wyjątek spoza zakresu.
* Nazwa serwera i kolumny adresu IP są teraz o zmiennym rozmiarze w **wypychana instalacja** strona kreatora vContinuum.
* Ulepszenia interfejsu API RX:
  * Pięć najnowszych dostępnych wspólnej spójność teraz punktów dostępnych (gwarantowane tylko znaczniki).
  * Pojemność i wolnego miejsca na szczegóły są wyświetlane dla wszystkich chronionych urządzeń.
  * Stan sterownika Scout na serwerze źródłowym jest dostępna.

> [!NOTE]
> * **InMage_Scout_Standard_8.0.1_GA.zip** pakiet podstawowy zawiera:
>     * Zaktualizowanej konfiguracji Instalatora serwera podstawowego systemu operacyjnego (**InMage_CX_8.0.1.0_Windows_GA_26Feb2015_release.exe**)
>     * Windows installer bazowej głównego elementu docelowego (**InMage_Scout_vContinuum_MT_8.0.1.0_Windows_GA_26Feb2015_release.exe**).
>     * We wszystkich instalacjach nowych Użyj nowego serwera konfiguracji i bitów głównego elementu docelowego GA Windows.
> * Update 4 można zastosować bezpośrednio na 8.0.1 GA.
> * Serwer konfiguracji i aktualizacji poza funkcjami RX, nie można obniżyć po zostały zastosowane.


### <a name="azure-site-recovery-scout-801-update-3"></a>Azure Site Recovery Scout 8.0.1 Update 3

Kumulują wszystkich aktualizacji usługi Site Recovery. Update 3 zawiera wszystkie poprawki z aktualizacją Update 1 i Update 2. Update 3 mogą być stosowane bezpośrednio na 8.0.1 GA. Serwer konfiguracji i aktualizacji poza funkcjami RX, nie można obniżyć po zostały zastosowane.

#### <a name="bug-fixes-and-enhancements"></a>Poprawki błędów i ulepszenia
Aktualizacja 3 rozwiązuje następujące problemy:

* Serwer konfiguracji i RX nie są zarejestrowane w magazynie, gdy są one się za serwerem proxy.
* Liczba godzin, w których nie został osiągnięty cel punktu odzyskiwania (RPO) nie jest aktualizowana w raporcie kondycji.
* Serwer konfiguracji nie jest synchronizowany z RX po szczegóły sprzętu ESX lub szczegóły sieci zawierać żadnych znaków UTF-8.
* Kontrolery domeny systemu Windows Server 2008 R2, nie uruchamiaj po odzyskaniu danych.
* Synchronizacja w trybie offline nie działa zgodnie z oczekiwaniami.
* Po włączeniu trybu failover maszyny Wirtualnej usunięcie pary replikacji nie postępu w konsoli programu configuration server przez długi czas. Użytkownicy nie może wykonać powrotu po awarii lub wznowić operacje.
* Ogólna zostały zoptymalizowane operacji migawki przez zadanie spójności, aby pomóc w zmniejszeniu aplikacji odłącza takich jak klienci programu SQL Server.
* Została zwiększona wydajność narzędzia (VACP.exe) spójność. Użycie pamięci wymagane do tworzenia migawek na Windows został zmniejszony.
* Wypychania zainstalować awarii usługi, gdy hasło jest większa niż 16 znaków.
* vContinuum nie Sprawdź i wyświetlenie monitu o nowe poświadczenia vCenter, po zmodyfikowaniu poświadczeń.
* W systemie Linux Menedżer pamięci podręcznej głównego elementu docelowego (cachemgr) nie jest pobieranie plików z serwera przetwarzania. Powoduje to ograniczenie pary replikacji.
* Gdy zamówienie dysku fizycznego w tryb failover klastra (MSCS) nie jest taka sama we wszystkich węzłach, replikacji nie jest ustawiona dla niektórych woluminów klastra. Klaster musi być przełączona do trybu z zalet tej poprawki.  
* Funkcje SMTP nie działa zgodnie z oczekiwaniami po uaktualnieniu z programu Scout 7.1 RX do programu Scout 8.0.1.
* Dodano więcej statystyki w dzienniku dla operacji wycofywania, aby śledzić czas potrzebny do jego wykonania.
* Dodano obsługę dla systemów operacyjnych Linux, na serwerze źródłowym:
  * Aktualizacja usługi Red Hat Enterprise Linux (RHEL) 6, 7
  * CentOS 6 update 7
* Serwer konfiguracji i RX teraz konsole Pokaż powiadomienia dla pary, który przechodzi w tryb mapy bitowej.
* Dodano następujące poprawki zabezpieczeń w RX:
    * Autoryzacja obejścia za pośrednictwem modyfikowaniu parametru: Ograniczony dostęp do innych odpowiednich użytkowników.
    * Fałszerstwo żądania międzywitrynowego: Koncepcja token strony została zaimplementowana i generuje losowo dla każdej strony. Oznacza to, tylko jednego logowania wystąpienia dla tego samego użytkownika, a odświeżania strony nie działa. Zamiast tego zostanie przekierowany do pulpitu nawigacyjnego.
    * Przekazywanie złośliwego pliku: Pliki są ograniczone do określonych rozszerzeń: z, aiff, asf, avi, bmp, csv, doc, docx, flag, flv, gif, gz, gzip, jpeg, jpg, dziennika połowy mov, mp3, w formacie mp4, mpc, mpeg, mpg, ods, odt, pdf, png, ppt, pptx, pxd, qt, pamięci ram, rar, Menedżera zasobów, rmi, rmvb, rtf, sdc, sitd, swf , sxc, sxw, tar tgz, tymczasowych plików internetowych, tiff, txt, vsd, wav, wma, wmv, xls, xlsx, xml i pliku zip.
    * Trwałe skryptów między witrynami: Dodano walidacji danych wejściowych.

### <a name="azure-site-recovery-scout-801-update-2-update-03dec15"></a>Aktualizacja usługi Azure Site Recovery Scout 8.0.1 2 (aktualizacja, 03 będzie 15 grudnia)

Poprawki w wersji Update 2 obejmują:

* **Serwer konfiguracji**: Problemy, które uniemożliwiały bezpłatną funkcją zliczania 31 dni roboczych zgodnie z oczekiwaniami, gdy serwer konfiguracji został zarejestrowany w magazynie usługi Azure Site Recovery.
* **Program Unified agent**: Rozwiązywanie problemu z Update 1, które spowodowały aktualizacji nie są instalowane na głównym serwerze docelowym, podczas uaktualniania z wersji 8.0 lub 8.0.1.

### <a name="azure-site-recovery-scout-801-update-1"></a>Azure Site Recovery Scout 8.0.1 Update 1
Aktualizacja Update 1 obejmuje następujące poprawki i nowe funkcje:

* 31 dni bezpłatnej ochrony każdego wystąpienia serwera. Dzięki temu można przetestować funkcję, lub skonfiguruj weryfikacji koncepcji.
* Wszystkie operacje na serwerze, w tym tryb failover i powrotu po awarii są bezpłatne przez pierwsze 31 dni. Czas rozpoczyna się, gdy serwer najpierw jest chroniony za pomocą programu Scout odzyskiwania lokacji. Od 32. dnia każdy chroniony serwer jest rozliczane zgodnie ze stawką wystąpienia standard ochrony odzyskiwania lokacji do lokacji należących do klienta.
* W dowolnym momencie liczba chronionych serwerów aktualnie pobieranych jest dostępna na **pulpit nawigacyjny** w magazynie.
* Dodano obsługę interfejsu wiersza polecenia (vCLI) vSphere 5.5 Update 2.
* Dodano obsługę dla tych systemów operacyjnych Linux, na serwerze źródłowym:
    * RHEL 6 aktualizacji 6
    * RHEL 5 aktualizacja Update 11
    * CentOS 6 Update 6
    * CentOS 5 Update 11
* Poprawki błędów rozwiązać następujące problemy:
  * Rejestracja w magazynie nie powiedzie się na serwerze konfiguracji lub serwerze RX.
  * Woluminy klastra nie pojawiają się jak oczekiwano podczas klastrowanych maszyn wirtualnych z ponownym włączeniu ochrony zgodnie z ich wznowić.
  * Powrót po awarii nie powiedzie się, gdy główny serwer docelowy znajduje się na innym serwerze ESXi z produkcji lokalnych maszyn wirtualnych.
  * Uprawnienia do pliku konfiguracji są zmieniane po uaktualnieniu do 8.0.1. Ta zmiana dotyczy ochrony i operacji.
  * Próg ponownej synchronizacji nie są wymuszane, zgodnie z oczekiwaniami, powodując niespójne replikacją.
  * Ustawienia celu punktu odzyskiwania nie są poprawnie wyświetlane w konsoli serwera konfiguracji. Wartość kompresji danych zawiera nieprawidłowo skompresowane wartość.
  * Operacja usuwania nie powoduje usunięcia, zgodnie z oczekiwaniami kreatora vContinuum i replikacji nie jest usuwany z konsoli serwera konfiguracji.
  * W Kreatorze vContinuum dysku nie jest automatycznie wybrany po kliknięciu **szczegóły** w widoku dysku podczas ochrony MSCS maszyn wirtualnych.
  * W tym scenariuszu fizyczna wirtualna (P2V) wymagane usługi HP (na przykład CIMnotify i CqMgHost) nie są przenoszone do ręcznego w maszynie Wirtualnej odzyskiwania. Ten problem powoduje dodatkowe rozruchu.
  * Ochrona maszyny Wirtualnej systemu Linux kończy się niepowodzeniem, gdy istnieją więcej niż 26 dysków na głównym serwerze docelowym.

