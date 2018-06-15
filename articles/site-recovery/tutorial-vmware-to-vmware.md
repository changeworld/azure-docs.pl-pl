---
title: Konfigurowanie odzyskiwania po awarii maszyn wirtualnych VMware lub serwerów fizycznych do lokacji dodatkowej z usługą Azure Site Recovery | Dokumentacja firmy Microsoft
description: Dowiedz się, jak skonfigurować odzyskiwania po awarii w przypadku maszyn wirtualnych VMware lub systemu Windows i Linux serwerów fizycznych, lokacji dodatkowej z usługą Azure Site Recovery.
services: site-recovery
documentationcenter: ''
author: nsoneji
manager: gauarvd
editor: ''
ms.assetid: 68616d15-398c-4f40-8323-17b6ae1e65c0
ms.service: site-recovery
ms.workload: backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/12/2017
ms.author: raynew
ms.openlocfilehash: 9de0cb3d22a4e500b66640f70f936ae955bbadb8
ms.sourcegitcommit: 088a8788d69a63a8e1333ad272d4a299cb19316e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/27/2018
ms.locfileid: "29677420"
---
# <a name="set-up-disaster-recovery-of-on-premises-vmware-virtual-machines-or-physical-servers-to-a-secondary-site"></a>Konfigurowanie odzyskiwania po awarii na lokalne maszyny wirtualne VMware lub serwerów fizycznych do lokacji dodatkowej

InMage Scout w [usługi Azure Site Recovery](site-recovery-overview.md) udostępnia w czasie rzeczywistym replikacji między lokalnymi witryn VMware. InMage Scout znajduje się w ramach subskrypcji usługi Azure Site Recovery. 


## <a name="prerequisites"></a>Wymagania wstępne

W celu ukończenia tego samouczka:

- [Przegląd](site-recovery-support-matrix-to-sec-site.md) wymagania dotyczące pomocy technicznej dla wszystkich składników.
- Upewnij się, że spełniają maszyn, które mają być replikowane [replikowane maszyny Obsługa](site-recovery-support-matrix-to-sec-site.md#support-for-replicated-machine-os-versions).


## <a name="create-a-vault"></a>Tworzenie magazynu

[!INCLUDE [site-recovery-create-vault](../../includes/site-recovery-create-vault.md)]

## <a name="choose-a-protection-goal"></a>Wybierz cel ochrony

Wybierz, co ma replicate i gdzie jej do replikacji.

1. Kliknij przycisk **usługi Site Recovery** > **przygotowanie infrastruktury** > **cel ochrony**.
2. Wybierz **do odzyskiwania lokacji** > **tak, z programem VMware vSphere Hypervisor**. Następnie kliknij przycisk **OK**.
3. W **Instalatora Scout**, Pobierz oprogramowanie GA InMage Scout 8.0.1 i klucz rejestracji. Pliki instalacyjne dla wszystkich składników znajdują się w pliku zip pobranego.

## <a name="download-and-install-component-updates"></a>Pobierz i zainstaluj aktualizacje składników

 Przejrzyj i zainstaluj najnowszą [aktualizacje](#updates). Aktualizacje powinny być zainstalowane na serwerach w następującej kolejności:

1. RX serwera (jeśli dotyczy)
2. Serwery konfiguracji
3. Serwery procesów
4. Główny cel serwerów
5. serwery vContinuum
6. Serwer źródłowy (z systemem Windows i serwerów z systemem Linux)

Zainstaluj aktualizacje w następujący sposób:

> [!NOTE]
>Wszystkie składniki Scout pliku zaktualizowanej wersji nie może być takie same, w pliku .zip aktualizacji. Starsza wersja wskazują, że nie została zmieniona w składniku od czasu poprzedniej aktualizacji tej aktualizacji.

Pobierz [aktualizacji](https://aka.ms/asr-scout-update6) pliku zip. Plik zawiera następujące składniki: 
  - RX_8.0.4.0_GA_Update_4_8725872_16Sep16.tar.gz
  - CX_Windows_8.0.6.0_GA_Update_6_13746667_18Sep17.exe
  - UA_Windows_8.0.5.0_GA_Update_5_11525802_20Apr17.exe
  - UA_RHEL6-64_8.0.4.0_GA_Update_4_9035261_26Sep16.tar.gz
  - vCon_Windows_8.0.6.0_GA_Update_6_11525767_21Sep17.exe
  - Usługa bits update4 UA systemem RHEL5 OL5, OL6, SUSE 10, SUSE 11: UA_<Linux OS>_8.0.4.0_GA_Update_4_9035261_26Sep16.tar.gz
1. Wyodrębnij pliki z rozszerzeniem .zip.
2. **Serwer RX**: kopiowania **RX_8.0.4.0_GA_Update_4_8725872_16Sep16.tar.gz** na serwerze RX i wyodrębnij go. W folderze wyodrębnionego Uruchom **/Install**.
3. **Serwer konfiguracji i serwera przetwarzania**: kopiowanie **CX_Windows_8.0.6.0_GA_Update_6_13746667_18Sep17.exe** do konfiguracji serwera i serwera przetwarzania. Kliknij dwukrotnie, aby go uruchomić.<br>
4. **Windows główny serwer docelowy**: Aby zaktualizować unified agent, skopiuj **UA_Windows_8.0.5.0_GA_Update_5_11525802_20Apr17.exe** do serwera. Kliknij go dwukrotnie, aby go uruchomić. Tę samą aktualizację unified agent jest również zastosowanie do serwera źródłowego. Źródło zostało zaktualizowane do aktualizacji 4, należy zaktualizować unified agent.
  Aktualizacja nie trzeba zastosować we wzorcu docelowy przygotowanym za pomocą **InMage_Scout_vContinuum_MT_8.0.1.0_Windows_GA_10Oct2017_release.exe** jest nowym Instalatorem GA o najnowsze zmiany.
5. **Serwer vContinuum**: kopiowanie **vCon_Windows_8.0.6.0_GA_Update_6_11525767_21Sep17.exe** do serwera.  Upewnij się, że zostało zamknięte kreatora vContinuum. Kliknij dwukrotnie plik, aby go uruchomić.
    Aktualizacja nie trzeba zastosować w docelowym elemencie głównym przygotowanym za pomocą **InMage_Scout_vContinuum_MT_8.0.1.0_Windows_GA_10Oct2017_release.exe** jest nowym Instalatorem GA o najnowsze zmiany.
6. **Linux główny serwer docelowy**: Aby zaktualizować unified agent, skopiuj **UA_RHEL6 64_8.0.4.0_GA_Update_4_9035261_26Sep16.tar.gz** do wzorca serwerze docelowym i wyodrębnij go. W folderze wyodrębnionego Uruchom **/Install**.
7. **Serwer źródłowy z systemem Windows**: Aby zaktualizować unified agent, skopiuj **UA_Windows_8.0.5.0_GA_Update_5_11525802_20Apr17.exe** do serwera źródłowego. Kliknij dwukrotnie plik, aby go uruchomić. 
    Nie trzeba zainstalować agenta aktualizacji 5 na serwerze źródłowym, jeśli został już zaktualizowany do aktualizacji w wersji 4 lub źródło agent został zainstalowany z najnowszą wersję Instalatora podstawowej **InMage_UA_8.0.1.0_Windows_GA_28Sep2017_release.exe**.
8. **Serwer źródłowy Linux**: Aby zaktualizować unified agent, skopiuj odpowiedniej wersji pliku unified agent na serwer z systemem Linux i wyodrębnij go. W folderze wyodrębnionego Uruchom **/Install**.  Przykład: RHEL 6,7 x 64 serwera, skopiować **UA_RHEL6 64_8.0.4.0_GA_Update_4_9035261_26Sep16.tar.gz** do serwera i wyodrębnij go. W folderze wyodrębnionego Uruchom **/Install**.

## <a name="enable-replication"></a>Włączanie replikacji

1. Konfigurowanie replikacji między źródłem i docelowych witryn VMware.
2. Można znaleźć w następujących dokumentach, aby dowiedzieć się więcej na temat instalacji, ochrony i odzyskiwania:

   * [Informacje o wersji](https://aka.ms/asr-scout-release-notes)
   * [Macierz zgodności](https://aka.ms/asr-scout-cm)
   * [Podręcznik użytkownika](https://aka.ms/asr-scout-user-guide)
   * [Podręcznik użytkownika RX](https://aka.ms/asr-scout-rx-user-guide)
   * [Przewodnik szybkiego instalacji](https://aka.ms/asr-scout-quick-install-guide)

## <a name="updates"></a>Aktualizacje

### <a name="site-recovery-scout-801-update-6"></a>Site Recovery Scout 8.0.1 Update 6 
Zaktualizowano: 12 października 2017 r

Pobierz [Scout aktualizacji 6](https://aka.ms/asr-scout-update6).

Aktualizacja Scout 6 jest aktualizacją zbiorczą. Zawiera wszystkie poprawki z zakresu od 1 aktualizacji do aktualizacji 5 oraz nowe poprawki i ulepszeń, które opisano poniżej. 

#### <a name="new-platform-support"></a>Nowa funkcja obsługi platformy
* Dodano obsługę dla źródła systemu Windows Server 2016
* Dodano obsługę dla następujących systemów operacyjnych Linux:
    - Red Hat Enterprise Linux (RHEL) 6,9
    - CentOS 6.9
    - Oracle Linux 5.11
    - Oracle Linux 6.8
* Dodano obsługę dla VMware Center 6.5

> [!NOTE]
> * Podstawowy Unified Agent(UA) Instalatora dla systemu Windows została odświeżona do obsługi systemu Windows Server 2016. Instalator nowe **InMage_UA_8.0.1.0_Windows_GA_28Sep2017_release.exe** jest dostarczana z pakietem Scout GA podstawowej (**InMage_Scout_Standard_8.0.1 GA-Oct17.zip**). Będzie można użyć tego samego Instalatora dla wszystkich wersji systemu Windows. 
> * Podstawowy Windows vContinuum & docelowego elementu głównego Instalatora została odświeżona w celu obsługi systemu Windows Server 2016. Instalator nowe **InMage_Scout_vContinuum_MT_8.0.1.0_Windows_GA_10Oct2017_release.exe** jest dostarczana z pakietem Scout GA podstawowej (**InMage_Scout_Standard_8.0.1 GA-Oct17.zip**). Tym samym instalatorze będzie służyć do wdrożenia docelowego elementu głównego 2016 systemu Windows i Windows 2012 R2 głównego celu.
> * Pobierz pakiet GA z portalu zgodnie z opisem w [Utwórz magazyn](#create-a-vault).
>

#### <a name="bug-fixes-and-enhancements"></a>Poprawki błędów i rozszerzenia
- Powrót po awarii ochrony kończy się niepowodzeniem dla maszyny Wirtualnej systemu Linux z listy dysków, które powinny być replikowane jest pusta, po zakończeniu konfiguracji.

### <a name="site-recovery-scout-801-update-5"></a>Site Recovery Scout 8.0.1 Update 5
Aktualizacja Scout 5 jest aktualizacją zbiorczą. Zawiera wszystkie poprawki z zakresu od 1 aktualizacji Update 4 oraz nowe poprawki opisane poniżej.
- Poprawki z lokacji odzyskiwania Scout aktualizacji 4 do 5 aktualizacji są przeznaczone dla obiekt docelowy i vContinuum składniki.
- Jeśli serwery źródłowe, główny cel, konfiguracji, proces i serwery RX jest już uruchomiona aktualizacji 4, a następnie zastosować je tylko na głównym serwerze docelowym. 

#### <a name="new-platform-support"></a>Nowa funkcja obsługi platformy
* SUSE Linux Enterprise Server 11 Service Pack 4(SP4)
* SLES 11 z dodatkiem SP4 64 bitowej **InMage_UA_8.0.1.0_SLES11-SP4-64_GA_13Apr2017_release.tar.gz** jest dostarczana z pakietem Scout GA podstawowej (**InMage_Scout_Standard_8.0.1 GA.zip**). Pobierz pakiet GA z portalu zgodnie z opisem w [Utwórz magazyn](#create-a-vault).


#### <a name="bug-fixes-and-enhancements"></a>Poprawki błędów i rozszerzenia

* Poprawki dla zwiększonej klastra systemu Windows obsługują niezawodności:
    * Stałe-niektóre P2V MSCS klastra dysków stają się RAW po odzyskaniu.
    * Odzyskiwanie klastra Fixed-P2V MSCS zakończy się niepowodzeniem z powodu niezgodności kolejności dysku.
    * Fixed — MSCS klastra operacji dodawania dysków kończy się niepowodzeniem z powodu błędu niezgodność rozmiaru dysku.
    * Sprawdź gotowość stałej klastrze MSCS z mapowaniem jednostek LUN RDM nie powiedzie się w zakresie weryfikacji rozmiar źródła.
    * Fixed-jednego węzła klastra ochrony kończy się niepowodzeniem z powodu problemu z niezgodność SCSI. 
    * Fixed-ponownego włączenia ochrony serwera klastra systemu Windows P2V kończy się niepowodzeniem, jeśli występują dyski klastra docelowego. 
    
* Stałe: Podczas ochrony powrotu po awarii, jeśli wybranego wzorca docelowego serwera nie jest na tym samym serwerze ESXi komputera chronionego źródła (podczas przekazywania ochrony), vContinuum przejmuje niewłaściwy głównego serwera docelowego podczas odzyskiwania powrotu po awarii i odzyskiwania, a następnie Operacja nie powiedzie się.

> [!NOTE]
> * Poprawki klastra P2V mają zastosowanie tylko w klastrach MSCS fizycznych, które nowo są chronione przy użyciu lokacji odzyskiwania Scout aktualizacji 5. Aby zainstalować poprawki klastra na chronionych klastry P2V MSCS starszych aktualizacji, wykonaj kroki uaktualniania wymienione w sekcji 12 [informacje o wersji lokacji odzyskiwania Scout](https://aka.ms/asr-scout-release-notes).
> * Jeśli w chwili ponownego włączenia ochrony ten sam zestaw dysków są aktywne na każdym z węzłów klastra, jakie były Jeśli pierwotnie chroniony, następnie ponownego włączenia ochrony klastra fizycznego MSCS tylko można wykorzystać istniejące dyski docelowe. Jeśli nie, użyj ręczne wykonanie czynności w sekcji 12 [informacje o wersji lokacji odzyskiwania Scout](https://aka.ms/asr-scout-release-notes), aby przenieść dyski po stronie docelowej ścieżce poprawne magazynu danych do ponownego użycia podczas ponownego włączenia ochrony. Jeśli w klastrze MSCS w trybie P2V jest Wznów bez zgodnie z krokami uaktualnienia, tworzy nowy dysk na serwerze docelowym ESXi. Należy ręcznie usunąć stary dysków z magazynu danych.
> * Po ponownym uruchomieniu poprawnego działania serwera źródłowego SLES11 lub SLES11 (za pomocą dowolnego z dodatkiem Service pack), następnie ręcznie oznaczyć **głównego** dysku pary replikacji ponownej synchronizacji. Nie jest prezentowane nie powiadomienie w interfejsie CX. Jeśli nie oznacza dysk głównego ponownej synchronizacji, może pojawić się problemy z integralnością danych.


### <a name="azure-site-recovery-scout-801-update-4"></a>Azure Site Recovery Scout 8.0.1 Update 4
Aktualizacja Scout 4 jest aktualizacją zbiorczą. Zawiera wszystkie poprawki z zakresu od 1 aktualizacji do wersji Update 3 i nowe poprawki opisane poniżej.

#### <a name="new-platform-support"></a>Nowa funkcja obsługi platformy

* Dodano obsługę dla vCenter/vSphere w wersji 6.0 i 6.1 6.2
* Dodano obsługę dla tych systemów operacyjnych Linux:
  * Red Hat Enterprise Linux (RHEL) 7.0, 7.1 i 7.2
  * CentOS 7.0, 7.1 i 7.2
  * Red Hat Enterprise Linux (RHEL) 6.8
  * CentOS 6.8

> [!NOTE]
> RHEL/CentOS 7 64-bitowym **InMage_UA_8.0.1.0_RHEL7-64_GA_06Oct2016_release.tar.gz** jest dostarczana z podstawowej pakietem Scout GA **InMage_Scout_Standard_8.0.1 GA.zip**. Pobierz pakiet Scout GA z portalu zgodnie z opisem w [Utwórz magazyn](#create-a-vault).

#### <a name="bug-fixes-and-enhancements"></a>Poprawki błędów i rozszerzenia

* Zamknięcie ulepszoną obsługę dla następujących systemów operacyjnych Linux i klonowanie się odbywa, aby zapobiec problemom niechciane ponownej synchronizacji:
    * Red Hat Enterprise Linux (RHEL) 6.x
    * Oracle Linux (OL) 6.x
* Dla systemu Linux wszystkie uprawnienia dostępu do folderu w katalogu instalacyjnym unified agent obecnie są ograniczone do tylko użytkownika lokalnego.
* W systemie Windows, poprawkę dotyczącą chronometrażu się problem, który wystąpił podczas wystawiania wspólnej zakładki spójności rozproszonych, na obciążonego aplikacji rozproszonych, takich jak klastry SQL Server i punktu udziału.
* Poprawka pokrewne dziennika w Instalatorze podstawowego serwera konfiguracji.
* Łącze pobierania do VMware vCLI 6.0 został dodany do Instalatora Windows podstawowej głównego celu.
* Dzienniki i dodatkowe testy zostały dodane do zmiany konfiguracji sieci podczas ćwiczeń odzyskiwania trybu failover i odzyskiwaniem po awarii.
* Poprawkę dotyczącą problemu, który spowodował informacje dotyczące przechowywania nie ma zostać podana dla serwera konfiguracji.  
* W przypadku fizycznego klastrów poprawkę dotyczącą problemu, który spowodował niepowodzenie w Kreatorze vContinuum podczas zmniejszania woluminu źródłowego zmiany rozmiaru woluminu.
* Poprawkę dotyczącą problemu ochrony klastra, które zakończyły się niepowodzeniem z powodu błędu: "Nie można odnaleźć podpisu dysku", gdy dysk klastrowy znajduje się na dysku PRDM.
* Poprawki dotyczące awarii serwera transportu cxps, spowodowane przez wyjątek out-of-range.
* Nazwa serwera i kolumny adresu IP, są teraz o zmiennym rozmiarze w **instalacja w trybie Push** kreatora vContinuum.
* Interfejs API ODBIERANIA ulepszenia:
  * Pięć najnowszych dostępnych wspólnej spójności teraz punktów dostępnych (gwarantowaną jedynie tagów).
  * Pojemność i wolnego miejsca szczegóły są wyświetlane dla wszystkich urządzeń chronionych.
  * Stan sterownika Scout na serwerze źródłowym jest dostępny.

> [!NOTE]
> * **InMage_Scout_Standard_8.0.1_GA.zip** pakiet podstawowy zawiera:
    * Instalator podstawowej zaktualizowanej konfiguracji serwera (**InMage_CX_8.0.1.0_Windows_GA_26Feb2015_release.exe**)
    * Instalator podstawowej główny serwer docelowy z systemem Windows (**InMage_Scout_vContinuum_MT_8.0.1.0_Windows_GA_26Feb2015_release.exe**).
    * Dla wszystkich nowych instalacji użyj nowego serwera konfiguracji i bits główny cel GA systemu Windows.
> * Aktualizacja 4 można zastosować bezpośrednio na 8.0.1 po
> * Serwer konfiguracji i aktualizacje ODBIERANIA, nie można obniżyć po zostały zastosowane.


### <a name="azure-site-recovery-scout-801-update-3"></a>Azure Site Recovery Scout 8.0.1 Update 3

Wszystkie aktualizacje usługi Site Recovery kumulują się. Aktualizacja 3 zawiera wszystkie poprawki z Update 1 i Update 2. Aktualizacja 3, które mogą być bezpośrednio stosowane w 8.0.1 po Serwer konfiguracji i aktualizacje ODBIERANIA, nie można obniżyć po zostały zastosowane.

#### <a name="bug-fixes-and-enhancements"></a>Poprawki błędów i rozszerzenia
Aktualizacja 3 rozwiązuje następujące problemy:

* Serwer konfiguracji i ODBIERANIA nie są zarejestrowane w magazynie, gdy są one za serwer proxy.
* Liczbę godzin, w których nie został osiągnięty cel punktu odzyskiwania (RPO) nie jest aktualizowana w raport o kondycji.
* Serwer konfiguracji nie jest synchronizowanie z RX gdy szczegóły sprzętu ESX lub szczegóły sieci zawierać żadnych znaków UTF-8.
* Kontrolery domeny systemu Windows Server 2008 R2 nie należy uruchamiać po odzyskaniu.
* Synchronizacja w trybie offline nie działa zgodnie z oczekiwaniami.
* Po maszyny Wirtualnej w tryb failover usunięcie pary replikacji nie postępu w konfiguracji serwera konsoli przez długi czas. Użytkownicy nie mogą Ukończ powrót po awarii lub wznowić operacje.
* Ogólne zostały zoptymalizowane operacji migawki przez zadanie spójności, aby pomóc w zmniejszeniu aplikacji rozłącza takich jak klienci programu SQL Server.
* Udoskonalono spójności wydajności narzędzia (VACP.exe). Użycie pamięci wymagane do tworzenia migawek w systemie Windows została zmniejszona.
* Instalacji wypychanej usługi awarii (Crash), jeśli pole hasło jest większa niż 16 znaków.
* vContinuum nie Sprawdź i Monituj o nowe poświadczenia vCenter, modyfikacji poświadczeń.
* W systemie Linux Menedżer pamięci podręcznej głównego celu (cachemgr) nie jest pobieranie plików z serwera przetwarzania. Powoduje to ograniczenie pary replikacji.
* Jeśli kolejność dysku fizycznego trybu failover klastra (MSCS) nie jest taka sama na wszystkich węzłach, replikacji nie jest ustawiona dla niektórych woluminów klastra. Klaster musi być przełączona do trybu mógł korzystać z tej poprawki.  
* Funkcje SMTP nie działa zgodnie z oczekiwaniami, po uaktualnieniu RX z Scout 7.1 do Scout 8.0.1.
* Statystyki więcej zostały dodane w dzienniku na śledzenie czas potrzebny na jego ukończenie operacji wycofywania.
* Dodano obsługę dla systemów operacyjnych Linux na serwerze źródłowym:
  * Red Hat Enterprise Linux (RHEL) 6 aktualizacji 7
  * CentOS 6 aktualizacji 7
* Serwer konfiguracji i ODBIERANIA konsole teraz Pokaż powiadomienia dla pary, który przechodzi do trybu mapy bitowej.
* RX zostały dodane następujące poprawki zabezpieczeń:
    * Obejście autoryzacji za pomocą parametru naruszeniu: ograniczony dostęp do innych niż odpowiednich użytkowników.
    * Sfałszowaniem żądań Cross-site: wprowadzono pojęcie token strony oraz generuje losowo dla każdej strony. Oznacza to tylko jednego logowania wystąpienia dla tego samego użytkownika, a odświeżania strony nie działa. Zamiast tego przekierowuje do pulpitu nawigacyjnego.
    * Przekazywanie pliku złośliwe: pliki są ograniczone do określonych rozszerzeń: z, aiff, asf, avi, bmp, csv, doc, docx, fla, flv, gif, gz, gzip, jpeg, jpg, dziennika środek mov, mp3, mp4, mpc, mpeg, mpg, ods odt, pdf, png, ppt, pptx, pxd, qt, pamięci ram, rar, rm, rmi, rmvb, rtf , sdc, sitd, swf, sxc, sxw, tar, tgz, tif, tiff, txt, vsd, wav, wma, wmv, xls, xlsx, xml i zip.
    * Trwałe skryptów krzyżowych: dodano sprawdzanie poprawności danych wejściowych.

### <a name="azure-site-recovery-scout-801-update-2-update-03dec15"></a>Usługi Azure Site Recovery Scout 8.0.1 aktualizacji 2 (aktualizacja, 03 będzie gru 15)

Poprawki w wersji Update 2 obejmują:

* **Serwer konfiguracji**: problemów, które uniemożliwiają 31-dniowej bezpłatnej pomiaru funkcji działać zgodnie z oczekiwaniami, gdy serwer konfiguracji został zarejestrowany w usłudze Site Recovery.
* **Unified agent**: Rozwiąż rozwiązuje problem w aktualizacji 1, które spowodowały aktualizacji nie są instalowane na głównym serwerze docelowym, podczas uaktualniania z wersji 8.0 lub 8.0.1.

### <a name="azure-site-recovery-scout-801-update-1"></a>Azure Site Recovery Scout 8.0.1 Update 1
Aktualizacja 1 obejmuje następujące poprawki i nowe funkcje:

* 31 dni wolne ochrony dla każdego wystąpienia serwera. Dzięki temu można przetestować funkcje lub skonfigurować Weryfikacja koncepcji.
* Wszystkie operacje na serwerze, w tym tryb failover i powrotu po awarii, mogą się w pierwszym 31 dni. Czas rozpoczyna się, gdy serwer najpierw jest chroniony za pomocą Scout odzyskiwania lokacji. Od 32 dnia każdego chronionego serwera jest pobierana w tempie standardowe wystąpienie do ochrony Site Recovery do lokacji należące do klientów.
* W dowolnym momencie liczba chronionych serwerów aktualnie pobieranych jest dostępna na **pulpitu nawigacyjnego** w magazynie.
* Dodano obsługę vSphere interfejsu wiersza polecenia (vCLI) 5.5 Update 2.
* Dodano obsługę dla tych systemów operacyjnych Linux na serwerze źródłowym:
    * RHEL 6 aktualizacji 6
    * RHEL 5 zaktualizować 11
    * CentOS 6 Update 6
    * CentOS 5 Update 11
* Wprowadzono poprawki błędów w celu rozwiązania następujących problemów:
  * Rejestracji magazynu w konfiguracji serwera lub serwera RX kończy się niepowodzeniem.
  * Wolumin klastra nie są wyświetlane jako oczekiwanego podczas klastrowane się, że maszyny wirtualne są przełączona do trybu zgodnie z ich wznowienia.
  * Powrót po awarii nie powiedzie się, gdy główny serwer docelowy jest hostowany na innym serwerze ESXi z produkcji lokalnych maszyn wirtualnych.
  * Uprawnienia do pliku konfiguracji są zmieniane po uaktualnieniu do 8.0.1. Ta zmiana wpływa na ochronę i operacji.
  * Próg ponownej synchronizacji nie jest wymuszana zgodnie z oczekiwaniami, powodując niespójne replikacją.
  * Ustawienia celu punktu odzyskiwania nie są wyświetlane poprawnie w konsoli serwera konfiguracji. Wartość kompresji danych niepoprawnie wyświetlana jest wartość skompresowane.
  * Operacji usuwania nie powoduje usunięcia, zgodnie z oczekiwaniami w Kreatorze vContinuum i replikacji nie jest usuwany z konsoli serwera konfiguracji.
  * W Kreatorze vContinuum dysk jest automatycznie usunięte po kliknięciu **szczegóły** w widoku dysku podczas ochrony MSCS maszyn wirtualnych.
  * W tym scenariuszu, fizyczna wirtualna (P2V) wymagane usługi HP (na przykład CIMnotify i CqMgHost) nie są przenoszone do ręcznego w odzyskiwania maszyny Wirtualnej. Ten problem powoduje dodatkowe rozruchu.
  * Ochrona maszyny Wirtualnej systemu Linux kończy się niepowodzeniem, jeśli istnieje więcej niż 26 dysków na głównym serwerze docelowym.

