---
title: Tworzenie kopii zapasowych maszyn wirtualnych funkcji Hyper-V za pomocą Azure Backup Server
description: Ten artykuł zawiera procedury tworzenia kopii zapasowych i odzyskiwania maszyn wirtualnych przy użyciu programu Microsoft Azure Backup Server.
author: dcurwin
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 07/18/2019
ms.author: dacurwin
ms.openlocfilehash: f15606c83c221e4591a2a1f6a71fc7141bdf3daf
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/14/2019
ms.locfileid: "74074962"
---
# <a name="back-up-hyper-v-virtual-machines-with-azure-backup-server"></a>Tworzenie kopii zapasowych maszyn wirtualnych funkcji Hyper-V za pomocą Azure Backup Server

W tym artykule opisano sposób tworzenia kopii zapasowych maszyn wirtualnych funkcji Hyper-V przy użyciu serwera Microsoft Azure Backup (serwera usługi MAB).

## <a name="supported-scenarios"></a>Obsługiwane scenariusze

SERWERA usługi MAB może tworzyć kopie zapasowe maszyn wirtualnych działających na serwerach hostów funkcji Hyper-V w następujących scenariuszach:

- **Maszyny wirtualne z magazynem lokalnym lub bezpośrednim** — tworzenie kopii zapasowych maszyn wirtualnych hostowanych na autonomicznych serwerach hosta funkcji Hyper-V z lokalnym lub bezpośrednio dołączonym magazynem. Na przykład: dysk twardy, urządzenie sieci magazynowania (SAN) lub urządzenie magazynu podłączonego do sieci (NAS). Na wszystkich hostach musi być zainstalowany agent ochrony serwera usługi MAB.

- **Maszyny wirtualne w klastrze z magazynem CSV** — tworzenie kopii zapasowych maszyn wirtualnych hostowanych w klastrze funkcji Hyper-V z magazynem udostępniony wolumin klastra (CSV). Agent ochrony serwera usługi MAB jest instalowany na każdym węźle klastra.

## <a name="host-versus-guest-backup"></a>Tworzenie kopii zapasowej hosta i gościa

SERWERA usługi MAB może wykonywać kopie zapasowe maszyn wirtualnych funkcji Hyper-V na poziomie hosta lub gościa. Na poziomie hosta agent ochrony serwera usługi MAB jest instalowany na serwerze lub klastrze hosta funkcji Hyper-V i chroni całe maszyny wirtualne i pliki danych uruchomione na tym hoście.   Na poziomie gościa Agent jest instalowany na każdej maszynie wirtualnej i chroni obciążenie obecne na tym komputerze.

Obie metody mają wady i zalety:

- Tworzenie kopii zapasowych na poziomie hosta jest elastyczne, ponieważ działają niezależnie od typu systemu operacyjnego działającego na maszynach gościa i nie wymagają instalacji agenta ochrony serwera usługi MAB na każdej maszynie wirtualnej. W przypadku wdrożenia kopii zapasowej na poziomie hosta możliwe jest odzyskanie całej maszyny wirtualnej lub plików i folderów (odzyskiwanie na poziomie elementu).

- Tworzenie kopii zapasowej na poziomie gościa jest przydatne, jeśli chcesz chronić określone obciążenia działające na maszynie wirtualnej. Na poziomie hosta możliwe jest odzyskanie całej maszyny wirtualnej lub określonych plików, ale nie zapewnia to odzyskiwania w kontekście określonej aplikacji. Na przykład w celu odzyskania określonych elementów programu SharePoint z kopii zapasowej maszyny wirtualnej należy wykonać kopię zapasową tej maszyny wirtualnej na poziomie gościa. Jeśli chcesz chronić dane przechowywane na dyskach przekazujących, użyj kopii zapasowej na poziomie gościa. Przekazywanie umożliwia maszynie wirtualnej bezpośredni dostęp do urządzenia magazynującego i nie przechowuje danych woluminu wirtualnego w pliku VHD.

## <a name="how-the-backup-process-works"></a>Jak działa proces tworzenia kopii zapasowej

SERWERA usługi MAB wykonuje kopię zapasową za pomocą usługi VSS w następujący sposób. Kroki opisane w tym opisie są numerowane w celu ułatwienia przejrzystości.

1. Aparat synchronizacji oparty na blokach serwera usługi MAB tworzy początkową kopię chronionej maszyny wirtualnej i gwarantuje, że kopia maszyny wirtualnej jest kompletna i spójna.

2. Po utworzeniu i sprawdzeniu kopii początkowej serwera usługi MAB używa składnika zapisywania usługi VSS funkcji Hyper-V do przechwytywania kopii zapasowych. Składnik zapisywania usługi VSS zawiera zestaw bloków dysków spójnych z danymi, które są synchronizowane z serwerem serwera usługi MAB. Takie podejście umożliwia korzystanie z "pełnej kopii zapasowej" przy użyciu serwera serwera usługi MAB, jednocześnie minimalizując dane kopii zapasowej, które muszą być przesyłane przez sieć.

3. Agent ochrony serwera usługi MAB na serwerze z uruchomioną funkcją Hyper-V korzysta z istniejących interfejsów API funkcji Hyper-V, aby określić, czy chroniona maszyna wirtualna również obsługuje usługę VSS.

   - Jeśli maszyna wirtualna spełnia wymagania kopii zapasowej online i ma zainstalowany składnik usług integracji funkcji Hyper-V, składnik zapisywania usługi VSS funkcji Hyper-V rekursywnie przekazuje żądanie VSS do wszystkich procesów obsługujących usługę VSS na maszynie wirtualnej. Ta operacja jest wykonywana bez instalacji agenta ochrony serwera usługi MAB na maszynie wirtualnej. Cykliczne żądanie usługi VSS umożliwia składnik zapisywania usługi VSS funkcji Hyper-V, aby zapewnić synchronizację operacji zapisu na dysku, co umożliwia przechwycenie migawki usługi VSS bez utraty danych.

     Składnik usług integracji funkcji Hyper-V wywołuje składnik zapisywania usługi VSS funkcji Hyper-V w ramach usług kopiowania woluminów w tle (VSS) na maszynach wirtualnych, aby upewnić się, że ich dane aplikacji są w spójnym stanie.

   - Jeśli maszyna wirtualna nie jest zgodna z wymaganiami dotyczącymi kopii zapasowych online, program serwera usługi MAB automatycznie używa interfejsów API funkcji Hyper-V, aby wstrzymać maszynę wirtualną przed przechwyceniem plików danych.

4. Po zsynchronizowaniu początkowej bazowej kopii maszyny wirtualnej z serwerem serwera usługi MAB wszystkie zmiany wprowadzone w zasobach maszyny wirtualnej są przechwytywane w nowym punkcie odzyskiwania. Punkt odzyskiwania przedstawia spójny stan maszyny wirtualnej w określonym czasie. Przechwytywanie w ramach punktu odzyskiwania może odbywać się co najmniej raz dziennie. Po utworzeniu nowego punktu odzyskiwania program serwera usługi MAB korzysta z replikacji na poziomie bloku w połączeniu ze składnikiem zapisywania usługi VSS funkcji Hyper-V, aby określić, które bloki zostały zmienione na serwerze z uruchomioną funkcją Hyper-V po utworzeniu ostatniego punktu odzyskiwania. Te bloki danych zostają następnie przetransferowane na serwer serwera usługi MAB i są stosowane do repliki chronionych danych.

5. Serwer serwera usługi MAB używa usługi VSS na woluminach, które obsługują dane odzyskiwania, aby można było korzystać z wielu kopii w tle. Każda z tych kopii w tle zapewnia osobne odzyskiwanie. Punkty odzyskiwania usługi VSS są przechowywane na serwerze serwera usługi MAB. Kopia tymczasowa wprowadzona na serwerze z uruchomioną funkcją Hyper-V jest przechowywana tylko na czas trwania synchronizacji serwera usługi MAB.

>[!NOTE]
>
>Począwszy od systemu Windows Server 2016 wirtualne dyski twarde funkcji Hyper-V mają wbudowane śledzenie zmian znane jako odporne śledzenie zmian (RCT). SERWERA usługi MAB korzysta z RCT (natywne śledzenie zmian w funkcji Hyper-V), co zmniejsza potrzebę czasochłonnych kontroli spójności w scenariuszach, takich jak awarie maszyn wirtualnych. RCT zapewnia lepszą odporność niż śledzenie zmian zapewniane przez kopie zapasowe oparte na migawce usługi VSS. SERWERA usługi MAB v3 optymalizuje użycie sieci i magazynu przez transfer tylko zmienionych danych podczas kontroli spójności.

## <a name="backup-prerequisites"></a>Wymagania wstępne dotyczące kopii zapasowych

Poniżej przedstawiono wymagania wstępne dotyczące tworzenia kopii zapasowych maszyn wirtualnych funkcji Hyper-V za pomocą serwera usługi MAB:

|Wymagania wstępne|Szczegóły|
|------------|-------|
|Wymagania wstępne serwera usługi MAB|— Jeśli chcesz przeprowadzić odzyskiwanie na poziomie elementu dla maszyn wirtualnych (odzyskanie plików, folderów, woluminów), musisz zainstalować rolę funkcji Hyper-V na serwerze serwera usługi MAB.  Jeśli chcesz tylko odzyskać maszynę wirtualną, a nie na poziomie elementu, rola nie jest wymagana.<br />— Można chronić maksymalnie 800 maszyn wirtualnych z 100 GB każdego na jednym serwerze serwera usługi MAB i zezwalać na wiele serwerów serwera usługi MAB obsługujących większe klastry.<br />-SERWERA usługi MAB wyklucza plik stronicowania z przyrostowych kopii zapasowych w celu zwiększenia wydajności kopii zapasowej maszyny wirtualnej.<br />-SERWERA usługi MAB może utworzyć kopię zapasową serwera lub klastra funkcji Hyper-V w tej samej domenie co serwer serwera usługi MAB lub w domenie podrzędnej lub zaufanej. Jeśli chcesz utworzyć kopię zapasową funkcji Hyper-V w grupie roboczej lub niezaufanej domenie, musisz skonfigurować uwierzytelnianie. W przypadku pojedynczego serwera funkcji Hyper-V można użyć uwierzytelniania NTLM lub certyfikatu. W przypadku klastra można używać tylko uwierzytelniania certyfikatów.<br />— Używanie kopii zapasowej na poziomie hosta do wykonywania kopii zapasowych danych maszyny wirtualnej na dyskach przekazujących nie jest obsługiwane. W tym scenariuszu zalecamy użycie kopii zapasowej na poziomie hosta do wykonywania kopii zapasowych plików VHD i kopii zapasowej na poziomie gościa w celu utworzenia kopii zapasowej innych danych, które nie są widoczne na hoście.<br />   — Można utworzyć kopię zapasową maszyn wirtualnych przechowywanych na deduplikowanych woluminach.|
|Wymagania wstępne dotyczące maszyn wirtualnych funkcji Hyper-V|-Wersja składników integracji uruchamiana na maszynie wirtualnej powinna być taka sama jak wersja hosta funkcji Hyper-V. <br />-Dla każdej kopii zapasowej maszyny wirtualnej będzie wymagane wolne miejsce na woluminie obsługującym pliki wirtualnego dysku twardego w celu umożliwienia funkcji Hyper-V wystarczającej ilości miejsca na dyski różnicowe (AVHD) podczas tworzenia kopii zapasowej. Przestrzeń musi być co najmniej równa **rozmiarowi początkowego rozmiaru dysku,\*współczynnik zmian\*czasu okna kopii zapasowej** . Jeśli uruchamiasz wiele kopii zapasowych w klastrze, potrzebujesz wystarczającej pojemności magazynu, aby obsłużyć magazynowania dysków AVHD dla każdej maszyny wirtualnej korzystającej z tego obliczenia.<br />— Aby utworzyć kopię zapasową maszyn wirtualnych znajdujących się na serwerach hosta funkcji Hyper-V z systemem Windows Server 2012 R2, maszyna wirtualna powinna mieć określony kontroler SCSI, nawet jeśli nie jest połączony z żadnymi elementami. (W usłudze Kopia zapasowa online systemu Windows Server 2012 R2, host funkcji Hyper-V instaluje nowy wirtualny dysk twardy w maszynie wirtualnej, a następnie Odinstalowuje go później. Może to obsługiwać tylko kontroler SCSI i dlatego jest wymagany do tworzenia kopii zapasowych online maszyny wirtualnej.  Bez tego ustawienia zostanie wygenerowane zdarzenie o IDENTYFIKATORze 10103 podczas próby utworzenia kopii zapasowej maszyny wirtualnej.|
|Wymagania wstępne systemu Linux|— Można utworzyć kopię zapasową maszyn wirtualnych z systemem Linux przy użyciu serwera usługi MAB 2012 R2. Obsługiwane są tylko migawki zgodne z plikami.|
|Tworzenie kopii zapasowych maszyn wirtualnych z magazynem CSV|-Dla magazynu CSV zainstaluj dostawcę sprzętowego usługi kopiowania woluminów w tle (VSS) na serwerze funkcji Hyper-V. Skontaktuj się z dostawcą sieci magazynowania (SAN) dla dostawcy sprzętowego usługi VSS.<br />— Jeśli pojedynczy węzeł zostanie nieoczekiwanie zamknięty w klastrze CSV, serwera usługi MAB przeprowadzi sprawdzanie spójności dla maszyn wirtualnych, które były uruchomione w tym węźle.<br />— Jeśli musisz ponownie uruchomić serwer funkcji Hyper-V, który ma szyfrowanie dysków funkcją BitLocker włączony w klastrze CSV, musisz uruchomić sprawdzanie spójności dla maszyn wirtualnych funkcji Hyper-V.|
|Tworzenie kopii zapasowych maszyn wirtualnych przy użyciu magazynu SMB|-Włącz funkcję automatycznego instalowania na serwerze z uruchomioną funkcją Hyper-V, aby włączyć ochronę maszyny wirtualnej.<br />   -Wyłącz odciążanie w technologii TCP Chimney.<br />— Upewnij się, że wszystkie konta komputera z funkcją Hyper-V mają pełne uprawnienia do określonych udziałów zdalnych plików SMB.<br />-Upewnij się, że ścieżka pliku dla wszystkich składników maszyny wirtualnej podczas odzyskiwania do lokalizacji alternatywnej jest krótsza niż 260 znaków. W przeciwnym razie odzyskiwanie może zakończyć się pomyślnie, ale funkcja Hyper-V nie będzie mogła zainstalować maszyny wirtualnej.<br />— Następujące scenariusze nie są obsługiwane:<br />     Wdrożenia, w których niektóre składniki maszyny wirtualnej znajdują się na woluminach lokalnych, a niektóre składniki znajdują się na woluminach zdalnych; adres IPv4 lub IPv6 dla serwera plików lokalizacji magazynu oraz odzyskiwanie maszyny wirtualnej na komputerze, który używa zdalnych udziałów SMB.<br />— Należy włączyć usługę agenta VSS serwera plików na każdym serwerze SMB — Dodaj ją w obszarze **Dodaj role i funkcje,**  > **wybierz role serwera** > usługi plików **i magazynowania** > usługi **PLIKOWE** ** > usługi plików > ** **usługi agenta VSS serwera plików**.|

## <a name="back-up-virtual-machines"></a>Tworzenie kopii zapasowych maszyn wirtualnych

1. Skonfiguruj [serwer serwera usługi MAB](backup-azure-microsoft-azure-backup.md) i [Magazyn](backup-mabs-add-storage.md). Podczas konfigurowania magazynu należy użyć tych wytycznych dotyczących pojemności magazynu.
   - Średni rozmiar maszyny wirtualnej — 100 GB
   - Liczba maszyn wirtualnych na serwer serwera usługi MAB — 800
   - Łączny rozmiar 800 maszyn wirtualnych — 80 TB
   - Wymagane miejsce dla magazynu kopii zapasowych — 80 TB

2. Skonfiguruj agenta ochrony serwera usługi MAB na serwerze funkcji Hyper-V lub w węzłach klastra funkcji Hyper-V. Jeśli wykonujesz kopię zapasową na poziomie gościa, Zainstaluj agenta na maszynach wirtualnych, dla których chcesz utworzyć kopię zapasową na poziomie gościa.

3. W konsoli administratora serwera usługi MAB kliknij pozycję **ochrona** > **Utwórz grupę ochrony** , aby otworzyć kreatora **tworzenia nowej grupy ochrony** .

4. Na stronie **Wybierz członków grupy** Wybierz Maszyny wirtualne, które mają być chronione przez serwery hosta funkcji Hyper-V, na których się znajdują. Zalecamy umieszczenie wszystkich maszyn wirtualnych, które będą miały te same zasady ochrony, w jednej grupie ochrony. Aby efektywnie wykorzystać miejsce, Włącz Współtworzenie lokalizacji. Współlokalizacja umożliwia lokalizowanie danych z różnych grup ochrony na tym samym dysku lub na taśmach, dzięki czemu wiele źródeł danych ma jedną replikę i wolumin punktu odzyskiwania.

5. Na stronie **Wybierz metodę ochrony danych** Podaj nazwę grupy ochrony. Wybierz opcję **Chcę uzyskać krótkoterminową ochronę za pomocą dysku** i wybierz opcję **Chcę chronić w trybie online** , jeśli chcesz utworzyć kopię zapasową danych na platformie Azure przy użyciu usługi Azure Backup.

6. W obszarze **Określ cele krótkoterminowe** > **Zakres przechowywania**Określ, jak długo mają być przechowywane dane dysku. W obszarze **częstotliwość synchronizacji**Określ, jak często mają być uruchamiane przyrostowe kopie zapasowe danych. Alternatywnie, zamiast wybierać interwał tworzenia przyrostowych kopii zapasowych, możesz włączyć **bezpośrednio przed punktem odzyskiwania**. Po włączeniu tego ustawienia serwera usługi MAB będzie uruchamiać ekspresową pełną kopię zapasową tuż przed każdym zaplanowanym punktem odzyskiwania.

    > [!NOTE]
    >
    >W przypadku ochrony obciążeń aplikacji punkty odzyskiwania są tworzone zgodnie z częstotliwością synchronizacji, pod warunkiem, że aplikacja obsługuje przyrostowe kopie zapasowe. Jeśli tak nie jest, serwera usługi MAB uruchamia ekspresową pełną kopię zapasową zamiast przyrostowej kopii zapasowej i tworzy punkty odzyskiwania zgodnie z harmonogramem ekspresowego tworzenia kopii zapasowych.

7. Na stronie **Przejrzyj przydział dysku** Przejrzyj miejsce na dysku w puli magazynów przydzielone danej grupie ochrony.

   **Łączny rozmiar danych** to rozmiar danych, których kopia zapasowa ma zostać utworzona, oraz miejsce na dysku, które ma **zostać zainicjowane w systemie serwera usługi MAB** , to miejsce, które serwera usługi MAB zaleca dla grupy ochrony. SERWERA usługi MAB wybiera idealny wolumin kopii zapasowej na podstawie ustawień. Można jednak edytować opcje woluminu kopii zapasowej w **szczegółach alokacji dysku**. W przypadku obciążeń wybierz preferowany magazyn z menu rozwijanego. Twoje zmiany zmieniają wartości **łącznego magazynu** i **wolnego magazynu** w okienku **dostępne Disk Storage** . Zajęte miejsce to ilość serwera usługi MAB magazynu sugerująca dodanie do woluminu, co pozwala na płynne wykonywanie kopii zapasowych w przyszłości.

8. Na stronie **Wybierz metodę tworzenia repliki** Określ, jak będzie wykonywana początkowa replikacja danych w grupie ochrony. Jeśli wybierzesz **automatyczną replikację za pośrednictwem sieci**, zalecamy wybranie czasu poza godzinami szczytu. W przypadku dużych ilości danych lub mniej niż optymalnych warunków sieciowych Rozważ **Ręczne**wybranie, które wymaga replikacji danych w trybie offline przy użyciu nośnika wymiennego.

9. Na stronie **Opcje sprawdzania spójności** wybierz sposób automatyzacji sprawdzania spójności. Uruchomienie sprawdzania można włączyć tylko wtedy, gdy dane repliki staną się niespójne lub zgodnie z harmonogramem. Jeśli nie chcesz konfigurować automatycznego sprawdzania spójności, w dowolnym momencie możesz uruchomić sprawdzanie ręczne, klikając prawym przyciskiem myszy grupę ochrony i wybierając polecenie **Przeprowadź sprawdzanie spójności**.

    Po utworzeniu grupy ochrony następuje Replikacja początkowa danych zgodnie z wybraną metodą. Po replikacji początkowej każda kopia zapasowa jest wykonywana zgodnie z ustawieniami grupy ochrony. Aby odzyskać kopię zapasową danych, należy pamiętać o następujących kwestiach:

## <a name="back-up-virtual-machines-configured-for-live-migration"></a>Tworzenie kopii zapasowych maszyn wirtualnych skonfigurowanych do migracji na żywo

Gdy maszyny wirtualne są związane z migracją na żywo, serwera usługi MAB nadal chronią maszyny wirtualne, o ile Agent ochrony serwera usługi MAB jest zainstalowany na hoście funkcji Hyper-V. Sposób, w jaki serwera usługi MAB chroni maszyny wirtualne, zależy od typu migracji na żywo.

**Migracja na żywo w ramach klastra** — w przypadku migrowania maszyny wirtualnej w klastrze serwera usługi MAB wykrywa migrację i tworzy kopię zapasową maszyny wirtualnej z nowego węzła klastra bez konieczności interwencji użytkownika. Ponieważ lokalizacja przechowywania nie została zmieniona, serwera usługi MAB kontynuuje Tworzenie ekspresowych pełnych kopii zapasowych.

**Migracja na żywo poza klastrem** — podczas migracji maszyny wirtualnej między serwerami autonomicznymi, różnymi klastrami lub między serwerem autonomicznym a klastrem program serwera usługi MAB wykrywa migrację i umożliwia utworzenie kopii zapasowej maszyny wirtualnej bez interwencji użytkownika.

### <a name="requirements-for-maintaining-protection"></a>Wymagania dotyczące utrzymania ochrony

Poniżej przedstawiono wymagania dotyczące konserwacji ochrony podczas migracji na żywo:

- Hosty funkcji Hyper-V dla maszyn wirtualnych muszą znajdować się w chmurze programu System Center VMM na serwerze VMM, na którym jest uruchomiony program System Center 2012 z dodatkiem SP1 lub nowszy.

- Agent ochrony serwera usługi MAB musi być zainstalowany na wszystkich hostach funkcji Hyper-V.

- Serwery serwera usługi MAB muszą być połączone z serwerem programu VMM. Wszystkie serwery hosta funkcji Hyper-V w chmurze programu VMM również muszą być podłączone do serwerów serwera usługi MAB. Pozwala to serwera usługi MAB na komunikowanie się z serwerem programu VMM, dlatego serwera usługi MAB może dowiedzieć się, na którym serwerze hosta funkcji Hyper-V maszyna wirtualna jest aktualnie uruchomiona, i utworzyć nową kopię zapasową z tego serwera funkcji Hyper-V. Jeśli nie można nawiązać połączenia z serwerem funkcji Hyper-V, kopia zapasowa kończy się niepowodzeniem z komunikatem, że Agent ochrony serwera usługi MAB jest nieosiągalny.

- Wszystkie serwery serwera usługi MAB, serwery VMM i serwery hosta funkcji Hyper-V muszą znajdować się w tej samej domenie.

### <a name="details-about-live-migration"></a>Szczegółowe informacje o migracji na żywo

Podczas migracji na żywo należy pamiętać o następujących kwestiach:

- Jeśli magazyn jest transferowany w ramach migracji na żywo, serwera usługi MAB wykonuje pełne sprawdzanie spójności maszyny wirtualnej, a następnie kontynuuje Tworzenie ekspresowych pełnych kopii zapasowych. Gdy odbywa się migracja magazynu na żywo, funkcja Hyper-V Reorganizuje wirtualny dysk twardy (VHD) lub VHDX, co powoduje jednorazowy wzrost rozmiaru danych kopii zapasowej serwera usługi MAB.

- Na hoście maszyny wirtualnej Włącz funkcję automatycznego instalowania, aby włączyć ochronę wirtualną, i wyłącz odciążanie przy użyciu technologii TCP Chimney.

- SERWERA usługi MAB używa portu 6070 jako domyślnego portu do hostowania usługi pomocnika DPM-VMM. Aby zmienić rejestr:

    1. Przejdź do **HKLM\Software\Microsoft\Microsoft Data Protection Manager\Configuration**.
    2. Utwórz 32-bitową wartość DWORD: DpmVmmHelperServicePort i Zapisz zaktualizowany numer portu w ramach klucza rejestru.
    3. Otwórz ```<Install directory>\Azure Backup Server\DPM\DPM\VmmHelperService\VmmHelperServiceHost.exe.config```i Zmień numer portu z 6070 na nowy port. Na przykład: ```<add baseAddress="net.tcp://localhost:6080/VmmHelperService/" />```
    4. Uruchom ponownie usługę pomocnika DPM-VMM i uruchom ponownie usługę DPM.

### <a name="set-up-protection-for-live-migration"></a>Konfigurowanie ochrony migracji na żywo

Aby skonfigurować ochronę migracji na żywo:

1. Skonfiguruj serwer serwera usługi MAB i jego magazyn, a następnie Zainstaluj agenta ochrony serwera usługi MAB na każdym serwerze hosta funkcji Hyper-V lub w węźle klastra w chmurze programu VMM. Jeśli używasz magazynu SMB w klastrze, Zainstaluj agenta ochrony serwera usługi MAB na wszystkich węzłach klastra.

2. Zainstaluj konsolę programu VMM jako składnik klienta na serwerze serwera usługi MAB, aby program serwera usługi MAB mógł komunikować się z serwerem programu VMM. Konsola powinna być taka sama jak wersja uruchomiona na serwerze programu VMM.

3. Przypisz konto MABSMachineName $ jako konto administratora z uprawnieniami tylko do odczytu na serwerze zarządzania programu VMM.

4. Podłącz wszystkie serwery hosta funkcji Hyper-V do wszystkich serwerów serwera usługi MAB za pomocą polecenia cmdlet programu `Set-DPMGlobalProperty` PowerShell. Polecenie cmdlet akceptuje wiele nazw serwerów serwera usługi MAB. Użyj formatu: `Set-DPMGlobalProperty -dpmservername <MABSservername> -knownvmmservers <vmmservername>`. Aby uzyskać więcej informacji, zobacz [Set-DPMGlobalProperty](https://technet.microsoft.com/library/hh881752.aspx).

5. Po odnalezieniu w programie VMM wszystkich maszyn wirtualnych uruchomionych na hostach funkcji Hyper-V w chmurach programu VMM Skonfiguruj grupę ochrony i Dodaj maszyny wirtualne, które mają być chronione. Należy włączyć automatyczne sprawdzanie spójności na poziomie grupy ochrony w celu ochrony w ramach scenariuszy mobilności maszyny wirtualnej.

6. Po skonfigurowaniu ustawień, gdy maszyna wirtualna jest migrowana z jednego klastra do innych kopii zapasowych, kontynuuje się zgodnie z oczekiwaniami. Możesz sprawdzić, czy migracja na żywo jest włączona zgodnie z oczekiwaniami, w następujący sposób:

   1. Sprawdź, czy usługa pomocnika DPM-VMM jest uruchomiona. Jeśli nie, uruchom ją.

   2. Otwórz Management Studio Microsoft SQL Server i nawiąż połączenie z wystąpieniem, które hostuje bazę danych serwera usługi MAB (DPMDB). Na DPMDB Uruchom następujące zapytanie: `SELECT TOP 1000 [PropertyName] ,[PropertyValue] FROM[DPMDB].[dbo].[tbl_DLS_GlobalSetting]`.

      To zapytanie zawiera właściwość o nazwie `KnownVMMServer`. Ta wartość powinna być taka sama jak wartość dostarczona z poleceniem cmdlet `Set-DPMGlobalProperty`.

   3. Uruchom następujące zapytanie, aby sprawdzić poprawność parametru *vmmidentifier znajduje* w `PhysicalPathXML` dla określonej maszyny wirtualnej. Zastąp `VMName` nazwą maszyny wirtualnej.

      ```sql
      select cast(PhysicalPath as XML) from tbl_IM_ProtectedObject where DataSourceId in (select datasourceid from tbl_IM_DataSource   where DataSourceName like '%<VMName>%')
      ```

   4. Otwórz plik. XML, który zwraca ta kwerenda, i sprawdź, czy pole *vmmidentifier znajduje* ma wartość.

### <a name="run-manual-migration"></a>Uruchom migrację ręczną

Po wykonaniu kroków opisanych w poprzednich sekcjach i zakończeniu zadania Menedżera podsumowania serwera usługi MAB zostanie włączona funkcja migracji. Domyślnie to zadanie zaczyna się o północy i jest uruchamiane co rano. Jeśli chcesz uruchomić migrację ręczną, aby sprawdzić, czy wszystko działa zgodnie z oczekiwaniami, wykonaj następujące czynności:

1. Otwórz SQL Server Management Studio i nawiąż połączenie z wystąpieniem, które hostuje bazę danych serwera usługi MAB.

2. Uruchom następujące zapytanie: `select * from tbl_SCH_ScheduleDefinition where JobDefinitionID='9B30D213-B836-4B9E-97C2-DB03C3EB39D7'`. To zapytanie zwraca **Identyfikator harmonogramu ScheduleID**. Zwróć uwagę na ten identyfikator, ponieważ będzie on używany w następnym kroku.

3. W SQL Server Management Studio rozwiń węzeł **SQL Server Agent**, a następnie rozwiń węzeł **zadania**. Kliknij prawym przyciskiem myszy **Identyfikator harmonogramu ScheduleID** , a następnie wybierz pozycję **Rozpocznij zadanie w kroku**.

Wydajność tworzenia kopii zapasowych zależy od tego, czy zadanie zostało uruchomione. Rozmiar i skala wdrożenia określają, ile czasu zadanie zajmie zakończenie.

## <a name="back-up-replica-virtual-machines"></a>Tworzenie kopii zapasowych maszyn wirtualnych repliki

Jeśli serwera usługi MAB jest uruchomiony w systemie Windows Server 2012 R2 lub nowszym, można utworzyć kopię zapasową maszyn wirtualnych repliki. Jest to przydatne z kilku powodów:

**Zmniejsza wpływ tworzenia kopii zapasowych na uruchomione obciążenie** — pobranie kopii zapasowej maszyny wirtualnej powoduje pewne obciążenie w miarę tworzenia migawki. Przeciążanie procesu tworzenia kopii zapasowej do dodatkowej lokacji zdalnej powoduje, że uruchomione obciążenie nie ma już znaczenia dla operacji tworzenia kopii zapasowej. Ma to zastosowanie tylko do wdrożeń, w których kopia zapasowa jest przechowywana w lokacji zdalnej. Można na przykład tworzyć codzienne kopie zapasowe i przechowywać dane lokalnie w celu zapewnienia szybkiego przywracania, ale co miesiąc lub co kwartał tworzyć kopie zapasowe z maszyn wirtualnych repliki przechowywanych zdalnie w celu długoterminowego przechowywania.

**Oszczędza przepustowość** — w typowym wdrożeniu w biurze oddziału, potrzebna jest odpowiednia przepustowość do przesyłania danych kopii zapasowych między lokacjami. Jeśli utworzysz strategię replikacji i trybu failover, oprócz strategii tworzenia kopii zapasowych danych możesz zmniejszyć ilość nadmiarowych danych wysyłanych przez sieć. Wykonując kopię zapasową danych maszyny wirtualnej repliki, a nie podstawowe, oszczędzasz obciążenie związane z wysyłaniem kopii zapasowych danych za pośrednictwem sieci.

**Umożliwia tworzenie kopii zapasowych dostawcy usług hostingowych** — można użyć hostowanego centrum danych jako lokacji repliki, bez potrzeby dodatkowego centrum danych. W takim przypadku umowa SLA dostawcy usług hostingowych wymaga spójnej kopii zapasowej maszyn wirtualnych repliki.

Maszyna wirtualna repliki jest wyłączona do momentu zainicjowania trybu failover, a usługa VSS nie może zagwarantować kopii zapasowej spójnej na poziomie aplikacji dla maszyny wirtualnej repliki. W ten sposób kopia zapasowa maszyny wirtualnej repliki będzie tylko spójna w razie awarii. Jeśli nie można zagwarantować spójności awaryjnej, wykonywanie kopii zapasowej zakończy się niepowodzeniem i może wystąpić w kilku warunkach:

- Maszyna wirtualna repliki nie jest w dobrej kondycji i jest w stanie krytycznym.

- Maszyna wirtualna repliki jest ponownie synchronizowana (w stanie "ponowna synchronizacja w toku" lub "wymagana ponowna synchronizacja").

- Replikacja początkowa między lokacją podstawową a dodatkową jest w toku lub oczekuje na maszynę wirtualną.

- Dzienniki. HRL są stosowane do maszyny wirtualnej repliki lub poprzednia czynność zastosowania dzienników. HRL na dysku wirtualnym nie powiodła się lub została anulowana lub przerwana.

- Migracja lub praca w trybie failover maszyny wirtualnej repliki jest w toku

## <a name="recover-backed-up-virtual-machines"></a>Odzyskiwanie kopii zapasowych maszyn wirtualnych

Aby można było odzyskać kopię zapasową maszyny wirtualnej, należy użyć Kreatora odzyskiwania, aby wybrać maszynę wirtualną i konkretny punkt odzyskiwania. Aby otworzyć Kreatora odzyskiwania i odzyskać maszynę wirtualną:

1. W konsoli administratora serwera usługi MAB wpisz nazwę maszyny wirtualnej lub rozwiń listę chronionych elementów i wybierz maszynę wirtualną, którą chcesz odzyskać.

2. W okienku **punkty odzyskiwania dla** w kalendarzu kliknij dowolną datę, aby wyświetlić dostępne punkty odzyskiwania. Następnie w okienku **ścieżka** wybierz punkt odzyskiwania, którego chcesz użyć w Kreatorze odzyskiwania.

3. W menu **Akcje** kliknij polecenie **Odzyskaj** , aby otworzyć Kreatora odzyskiwania.

    Wybrana maszyna wirtualna i punkt odzyskiwania są wyświetlane na ekranie **Przejrzyj wybrane opcje odzyskiwania** . Kliknij przycisk **Dalej**.

4. Na ekranie **Wybieranie typu odzyskiwania** wybierz miejsce, w którym chcesz przywrócić dane, a następnie kliknij przycisk **dalej**.

    - **Odzyskaj do oryginalnego wystąpienia**: gdy odzyskasz do oryginalnego wystąpienia, oryginalny dysk VHD zostanie usunięty. SERWERA usługi MAB odzyskuje dysk VHD i inne pliki konfiguracji do oryginalnej lokalizacji za pomocą składnika zapisywania usługi VSS funkcji Hyper-V. Po zakończeniu procesu odzyskiwania maszyny wirtualne są nadal wysoce dostępne.
        Aby odzyskiwanie było możliwe, musi być obecna Grupa zasobów. Jeśli nie jest dostępna, Odzyskaj do lokalizacji alternatywnej, a następnie ustaw maszynę wirtualną w wysokiej dostępności.

    - **Odzyskaj jako maszynę wirtualną do dowolnego hosta**: serwera usługi MAB obsługuje odzyskiwanie do lokalizacji alternatywnej (ALR), które zapewnia bezproblemowe odzyskiwanie chronionej maszyny wirtualnej funkcji Hyper-v do innego hosta funkcji Hyper-v niezależnie od architektury procesora. Maszyny wirtualne funkcji Hyper-V odzyskiwane do węzła klastra nie będą miały dużej dostępności. W przypadku wybrania tej opcji Kreator odzyskiwania przedstawia dodatkowy ekran służący do identyfikowania ścieżki docelowej i docelowej.

    - **Kopiuj do folderu sieciowego**: serwera usługi MAB obsługuje odzyskiwanie na poziomie elementu (ILR), które umożliwia odzyskiwanie plików, folderów, woluminów i wirtualnych dysków twardych (VHD) z kopii zapasowej na poziomie hosta maszyn wirtualnych funkcji Hyper-V do udziału sieciowego lub woluminu na serwerze chronionym serwera usługi MAB. Aby można było przeprowadzić odzyskiwanie na poziomie elementu, Agent ochrony serwera usługi MAB nie musi być zainstalowany w ramach gościa. W przypadku wybrania tej opcji Kreator odzyskiwania przedstawia dodatkowy ekran służący do identyfikowania ścieżki docelowej i docelowej.

5. W obszarze **Określ opcje odzyskiwania** Skonfiguruj opcje odzyskiwania i kliknij przycisk **dalej**:

    - W przypadku odzyskiwania maszyny wirtualnej z niską przepustowością kliknij przycisk **Modyfikuj** , aby włączyć **ograniczanie użycia przepustowości sieci**. Po włączeniu opcji ograniczania przepustowości możesz określić przepustowość, która ma być dostępna, oraz czas, w którym ta przepustowość jest dostępna.
    - Wybierz opcję **Włącz odzyskiwanie na podstawie sieci SAN, używając migawek sprzętowych** , jeśli skonfigurowano sieć.
    - Wybierz opcję **Wyślij wiadomość e-mail po zakończeniu odzyskiwania** , a następnie podaj adresy e-mail, jeśli chcesz otrzymywać powiadomienia pocztą e-mail po zakończeniu procesu odzyskiwania.

6. Na ekranie Podsumowanie upewnij się, że wszystkie szczegóły są poprawne. Jeśli szczegóły są nieprawidłowe lub chcesz wprowadzić zmiany, kliknij przycisk **Wstecz**. Jeśli ustawienia są zadowalające, kliknij przycisk **Odzyskaj** , aby rozpocząć proces odzyskiwania.

7. Ekran **stan odzyskiwania** zawiera informacje o zadaniu odzyskiwania.

## <a name="next-steps"></a>Następne kroki

[Recover data from Azure Backup Server (Odzyskiwanie danych z usługi Azure Backup Server)](https://docs.microsoft.com/azure/backup/backup-azure-alternate-dpm-server)
