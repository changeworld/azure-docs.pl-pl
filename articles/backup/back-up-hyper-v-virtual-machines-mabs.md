---
title: Czelokrotnianie kopii zapasowych maszyn wirtualnych funkcji Hyper-V za pomocą usługi MABS
description: Ten artykuł zawiera procedury tworzenia kopii zapasowych i odzyskiwania maszyn wirtualnych przy użyciu programu Microsoft Azure Backup Server (MABS).
ms.topic: conceptual
ms.date: 07/18/2019
ms.openlocfilehash: 71cf446472ef0cf4f50bf64e47d359ea08ccc087
ms.sourcegitcommit: 7581df526837b1484de136cf6ae1560c21bf7e73
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/31/2020
ms.locfileid: "80420406"
---
# <a name="back-up-hyper-v-virtual-machines-with-azure-backup-server"></a>Tworzenie kopii zapasowych maszyn wirtualnych funkcji Hyper-V za pomocą serwera kopii zapasowych platformy Azure

W tym artykule wyjaśniono, jak wykonać kopię zapasową maszyn wirtualnych funkcji Hyper-V przy użyciu programu Microsoft Azure Backup Server (MABS).

## <a name="supported-scenarios"></a>Obsługiwane scenariusze

Firma MABS może w następujących scenariuszach na łącze zapasowe maszyn wirtualnych działających na serwerach hosta funkcji Hyper-V:

- **Maszyny wirtualne z magazynem lokalnym lub podłączonym bezpośrednio** — wykonywanie kopii zapasowych maszyn wirtualnych hostowanych na autonomicznych serwerach hosta funkcji Hyper-V mających lokalną lub podłączoną bezpośrednio pamięć masową. Na przykład: dysk twardy, urządzenie sieciowej sieci magazynowej (SAN) lub urządzenie sieciowej pamięci masowej (NAS). Agent ochrony MABS musi być zainstalowany na wszystkich hostach.

- **Maszyny wirtualne w klastrze z magazynem CSV** — wykonywanie kopii zapasowych maszyn wirtualnych hostowanych w klastrze funkcji Hyper-V z woluminem udostępnionym klastra (CSV). Agent ochrony MABS jest zainstalowany w każdym węźle klastra.

## <a name="host-versus-guest-backup"></a>Kopia zapasowa hosta a gość

Firma MABS może wykonać kopię zapasową maszyn wirtualnych typu Hyper V na poziomie hosta lub gościa. Na poziomie hosta agent ochrony MABS jest zainstalowany na serwerze hosta lub klastrze hosta funkcji Hyper-V i chroni całe maszyny wirtualne i pliki danych uruchomione na tym hoście.   Na poziomie gościa agent jest zainstalowany na każdej maszynie wirtualnej i chroni obciążenie obecne na tym komputerze.

Obie metody mają swoje zalety i wady:

- Kopie zapasowe na poziomie hosta są elastyczne, ponieważ działają niezależnie od typu systemu operacyjnego uruchomionego na komputerach gościa i nie wymagają instalacji agenta ochrony MABS na każdej maszynie wirtualnej. Jeśli wdrożysz kopię zapasową na poziomie hosta, można odzyskać całą maszynę wirtualną lub pliki i foldery (odzyskiwanie na poziomie elementu).

- Kopia zapasowa na poziomie gościa jest przydatna, jeśli chcesz chronić określone obciążenia uruchomione na maszynie wirtualnej. Na poziomie hosta można odzyskać całą maszynę wirtualną lub określone pliki, ale nie zapewni odzyskiwania w kontekście określonej aplikacji. Na przykład, aby odzyskać określone elementy programu SharePoint z kopii zapasowej maszyny Wirtualnej, należy wykonać kopię zapasową na poziomie gościa tej maszyny Wirtualnej. Użyj kopii zapasowej na poziomie gościa, jeśli chcesz chronić dane przechowywane na dyskach przekazu. Przekazywanie umożliwia maszynie wirtualnej bezpośredni dostęp do urządzenia magazynującego i nie przechowuje danych woluminu wirtualnego w pliku VHD.

## <a name="how-the-backup-process-works"></a>Jak działa proces tworzenia kopii zapasowej

Mabs wykonuje kopię zapasową z usługą VSS w następujący sposób. Kroki znajdujące się w tym opisie są ponumerowane w celu zwiększenia przejrzystości.

1. Aparat synchronizacji bloków mabs sprawia, że początkowa kopia chronionej maszyny wirtualnej i zapewnia, że kopia maszyny wirtualnej jest kompletna i spójna.

2. Po wykonaniu i zweryfikowaniu kopii początkowej program MABS używa modułu zapisującego usługi VSS funkcji Hyper-V do przechwytywania kopii zapasowych. Moduł zapisujący usługi VSS udostępnia spójny z danymi zestaw bloków dysków, które są synchronizowane z serwerem MABS. Takie podejście zapewnia korzyści z "pełnej kopii zapasowej" z serwerem MABS, minimalizując jednocześnie dane kopii zapasowej, które muszą być przesyłane przez sieć.

3. Agent ochrony MABS na serwerze z uruchomionym funkcji Hyper-V używa istniejących interfejsów API funkcji Hyper-V w celu ustalenia, czy chroniona maszyna wirtualna obsługuje również usługi VSS.

   - Jeśli maszyna wirtualna spełnia wymagania kopii zapasowej online i ma zainstalowany składnik usług integracji funkcji Hyper-V, to element zapisujący usług VSS funkcji Hyper-V rekursywnie przekazuje żądania usług VSS do wszystkich procesów obsługujących takie żądania na maszynie wirtualnej. Ta operacja występuje bez agenta ochrony MABS jest zainstalowany na maszynie wirtualnej. Dzięki tym cyklicznym żądaniom składnik zapisywania usługi VSS funkcji Hyper-V może się upewnić, że operacje zapisu na dysku są zsynchronizowane, aby przechwycić migawkę usługi VSS bez utraty danych.

     Składnik usług integracji funkcji Hyper-V wywołuje składnik zapisywania usługi VSS funkcji Hyper-V w ramach usług kopiowania woluminów w tle (VSS) w maszynach wirtualnych, aby sprawdzić, czy dane aplikacji są spójne.

   - Jeśli maszyna wirtualna nie spełnia wymagań dotyczących tworzenia kopii zapasowych online, usługa MABS automatycznie używa interfejsów API funkcji Hyper-V do wstrzymania maszyny wirtualnej przed przechwyceniem plików danych.

4. Po początkowej kopii linii bazowej maszyny wirtualnej synchronizuje się z serwerem MABS, wszystkie zmiany, które są wprowadzane do zasobów maszyny wirtualnej są przechwytywane w nowym punkcie odzyskiwania. Punkt odzyskiwania przedstawia stan spójności maszyny wirtualnej w określonym czasie. Przechwytywanie w ramach punktu odzyskiwania może odbywać się co najmniej raz dziennie. Po utworzeniu nowego punktu odzyskiwania program MABS używa replikacji na poziomie bloku w połączeniu z modułem zapisującym usługi VSS funkcji Hyper-V, aby określić, które bloki zostały zmienione na serwerze z uruchomionym funkcją Hyper-V po utworzeniu ostatniego punktu odzyskiwania. Te bloki danych są następnie przesyłane do serwera MABS i są stosowane do repliki chronionych danych.

5. Serwer MABS używa usługi VSS na woluminach, które hostują dane odzyskiwania, dzięki czemu dostępnych jest wiele kopii w tle. Każda z tych kopii w tle zapewnia osobne zadania odzyskiwania. Punkty odzyskiwania usługi VSS są przechowywane na serwerze MABS. Tymczasowa kopia, która jest wykonana na serwerze z uruchomiona funkcja Hyper-V, jest przechowywana tylko przez czas trwania synchronizacji MABS.

>[!NOTE]
>
>Począwszy od systemu Windows Server 2016 wirtualne dyski twarde funkcji Hyper-V mają wbudowane śledzenie zmian znane jako odporne śledzenie zmian (RCT). Mabs używa RCT (natywne śledzenie zmian w funkcji Hyper-V), co zmniejsza potrzebę czasochłonnej kontroli spójności w scenariuszach, takich jak awarie maszyn wirtualnych. Śledzenie RCT zapewnia większą odporność niż śledzenie zmian w opartych na migawkach kopiach zapasowych usługi VSS. MABS V3 dodatkowo optymalizuje zużycie sieci i pamięci masowej, przesyłając tylko zmienione dane podczas sprawdzania spójności.

## <a name="backup-prerequisites"></a>Wymagania wstępne dotyczące kopii zapasowej

Są to warunki wstępne do tworzenia kopii zapasowych maszyn wirtualnych funkcji Hyper-V za pomocą usługi MABS:

|Wymagania wstępne|Szczegóły|
|------------|-------|
|Wymagania wstępne MABS|- Jeśli chcesz wykonać odzyskiwanie na poziomie elementu dla maszyn wirtualnych (odzyskać pliki, foldery, woluminy), musisz zainstalować rolę Funkcji Hyper-V na serwerze MABS.  Jeśli chcesz tylko odzyskać maszynę wirtualną, a nie na poziomie elementu, rola nie jest wymagana.<br />- Można chronić do 800 maszyn wirtualnych po 100 GB każdy na jednym serwerze MABS i zezwolić na wiele serwerów MABS, które obsługują większe klastry.<br />- MABS wyklucza plik stronicowania z przyrostowych kopii zapasowych w celu poprawy wydajności kopii zapasowych maszyny wirtualnej.<br />- Mabs może kopii zapasowej serwera hyper-V lub klastra w tej samej domenie co serwer MABS lub w domenie podrzędnej lub zaufanej. Jeśli chcesz wykonać wykonanie kopii zapasowej funkcji Hyper-V w grupie roboczej lub niezaufanej domenie, musisz skonfigurować uwierzytelnianie. W przypadku pojedynczego serwera funkcji Hyper-V można użyć uwierzytelniania NTLM lub certyfikatu. W przypadku klastra można używać tylko uwierzytelniania certyfikatów.<br />— Przy tworzeniu kopii zapasowej danych maszyny wirtualnej na dyskach przekazujących nie jest obsługiwane korzystanie z kopii zapasowych na poziomie hosta. W tym scenariuszu zaleca się użycie kopii zapasowej na poziomie hosta do tworzenia kopii zapasowych plików VHD i kopii zapasowej na poziomie gościa w celu utworzenia kopii zapasowej innych danych, które nie są widoczne na hoście.<br />   -Można kopii zapasowej maszyn wirtualnych przechowywanych na deduplikowanych woluminach.|
|Wymagania wstępne maszyny wirtualnej funkcji Hyper-V|- Wersja integration components, która jest uruchomiona na maszynie wirtualnej powinny być takie same jak wersja hosta funkcji Hyper-V. <br />— Dla każdej kopii zapasowej maszyny wirtualnej będzie wymagane wolne miejsce na woluminie obsługującym pliki wirtualnego dysku twardego, aby dostarczyć funkcji Hyper-V wystarczającą ilość miejsca na dyski różnicowe (AVHD) podczas wykonywania kopii zapasowej. Miejsce musi być co najmniej równe obliczeniu **Początkowy rozmiar dysku Współczynnik\*zmian Czas\*** okna kopii zapasowej. Jeśli używasz wielu kopii zapasowych w klastrze, konieczna będzie obliczona w ten sposób wystarczająca ilość miejsca do magazynowania dysków AVHD dla poszczególnych maszyn wirtualnych.<br />- Aby zrobić kopii zapasowej maszyn wirtualnych znajdujących się na serwerach hosta funkcji Hyper-V z systemem Windows Server 2012 R2, maszyna wirtualna powinna mieć określony kontroler SCSI, nawet jeśli nie jest do niczego połączona. (W kopii zapasowej online systemu Windows Server 2012 R2 host funkcji Hyper-V montuje nowy dysk VHD na maszynie wirtualnej, a następnie odinstalowuje go. Tylko kontroler SCSI może to obsługiwać i dlatego jest wymagany do tworzenia kopii zapasowych online maszyny wirtualnej.  Bez tego ustawienia identyfikator zdarzenia 10103 zostanie wystawiony podczas próby utworzenia kopii zapasowej maszyny wirtualnej.)|
|Wymagania wstępne systemu Linux|- Możesz kopii zapasowej maszyn wirtualnych Linuksa za pomocą MABS. Obsługiwane są tylko migawki zgodne z plikami.|
|Tworzenie kopii zapasowej maszyn wirtualnych z magazynem CSV|— Na potrzeby magazynu CSV należy zainstalować na serwerze funkcji Hyper-V dostawcę sprzętowego usługi kopiowania woluminów w tle (VSS). W sprawie dostawcy sprzętowego usługi VSS skontaktuj się z dostawcą swojej sieci magazynowania (SAN).<br />- Jeśli pojedynczy węzeł zostanie nieoczekiwanie zamknięty w klastrze CSV, mabs wykona sprawdzanie spójności względem maszyn wirtualnych, które były uruchomione w tym węźle.<br />— Jeśli potrzebujesz uruchomić ponownie serwer funkcji Hyper-V z włączoną funkcją szyfrowania dysków BitLocker w klastrze CSV, musisz uruchomić sprawdzenie spójności dla maszyn wirtualnych funkcji Hyper-V.|
|Tworzenie kopii zapasowej maszyn wirtualnych z magazynem SMB|— Aby włączyć ochronę maszyny wirtualnej, włącz funkcję automatycznej instalacji na serwerze z uruchomioną funkcją Hyper-V.<br />   — Wyłącz odciążanie przy użyciu technologii TCP Chimney.<br />— Upewnij się, że wszystkie konta machine$ funkcji Hyper-V mają pełne uprawnienia w określonych udziałach zdalnych plików SMB.<br />- Upewnij się, że ścieżka pliku dla wszystkich składników maszyny wirtualnej podczas odzyskiwania do lokalizacji alternatywnej jest mniejsza niż 260 znaków. Jeśli nie, odzyskiwanie może zakończyć się pomyślnie, ale funkcja Hyper-V nie będzie mogła zainstalować maszyny wirtualnej.<br />- Następujące scenariusze nie są obsługiwane:<br />     Wdrożenia, w których niektóre składniki maszyny wirtualnej znajdują się na woluminach lokalnych, a niektóre składniki znajdują się na woluminach zdalnych; adres IPv4 lub IPv6 dla serwera plików lokalizacji magazynu oraz odzyskiwanie maszyny wirtualnej na komputerze korzystającym ze zdalnych udziałów SMB.<br />- Musisz włączyć usługę Agent VSS serwera plików na każdym serwerze SMB — dodaj ją w **Dodaj role i funkcje** > **Wybierz role** > serwera Usługi plików**usługi** > plików usługi**plików** > usługi plików usługi**plików** > **USŁUGI PLIKÓW USŁUGI VSS Agent Service**.|

## <a name="back-up-virtual-machines"></a>Tworzenie kopii zapasowych maszyn wirtualnych

1. Skonfiguruj [serwer MABS](backup-azure-microsoft-azure-backup.md) i [pamięć masową](backup-mabs-add-storage.md). Podczas konfigurowania magazynu użyj następujących wytycznych dotyczących pojemności magazynu.
   - Średni rozmiar maszyny wirtualnej — 100 GB
   - Liczba maszyn wirtualnych na serwer MABS - 800
   - Całkowity rozmiar 800 maszyn wirtualnych — 80 TB
   - Wymagane miejsce na magazyn kopii zapasowych — 80 TB

2. Skonfiguruj agenta ochrony MABS na serwerze funkcji Hyper-V lub węzłach klastra funkcji Hyper-V. Jeśli wykonujesz kopię zapasową na poziomie gościa, zainstalujesz agenta na maszynach wirtualnych, których kopię zapasową chcesz utworzyć na poziomie gościa.

3. W konsoli Administrator systemu MABS kliknij pozycję **Grupa** > **Ochrony utwórz ochronę,** aby otworzyć **kreatora Tworzenie nowej grupy ochrony.**

4. Na stronie **Wybierz członków grupy** wybierz maszyny wirtualne, które chcesz chronić za pomocą serwerów hostów funkcji Hyper-V, na których się znajdują. Zalecane jest umieszczenie wszystkich maszyn wirtualnych, które będą miały te same zasady ochrony, w jednej grupie ochrony. W celu efektywnego wykorzystania miejsca włącz wspólną lokalizację. Przekazywanie danych do wspólnej lokalizacji umożliwia umieszczanie danych pochodzących z różnych grup ochrony w tym samym magazynie dyskowym lub taśmowym, tak aby różne źródła danych miały jedną replikę i wolumin punktu odzyskiwania.

5. Na stronie **Wybierz metodę ochrony danych** podaj nazwę grupy ochrony. Wybierz opcję **Chcę uzyskać krótkoterminową ochronę za pomocą dysku** oraz opcję **Chcę uzyskać ochronę online** , aby tworzyć kopie zapasowe danych na platformie Azure za pomocą usługi Kopia zapasowa Azure.

6. W obszarze Określanie**zakresu przechowywania** **celów** > krótkoterminowych określ, jak długo mają być przechowywane dane dysku. W **obszarze Częstotliwość synchronizacji**określ, jak często należy uruchamiać przyrostowe kopie zapasowe danych. Zamiast określać interwał tworzenia przyrostowych kopii zapasowych, możesz włączyć opcję **Bezpośrednio przed punktem odzyskiwania**. Po włączeniu tego ustawienia usługa MABS uruchomi ekspresową pełną kopię zapasową tuż przed każdym zaplanowanym punktem odzyskiwania.

    > [!NOTE]
    >
    >W przypadku ochrony obciążeń aplikacji punkty odzyskiwania są tworzone zgodnie z opcją Częstotliwość synchronizacji, przy założeniu, że aplikacja obsługuje przyrostowe kopie zapasowe. Jeśli tak nie jest, następnie mabs uruchamia ekspresowe pełnej kopii zapasowej, zamiast przyrostowej kopii zapasowej i tworzy punkty odzyskiwania zgodnie z harmonogramem ekspresowej kopii zapasowej.

7. Na stronie **Przeglądanie alokacji dysku** przejrzyj miejsce na dysku puli magazynów przydzielone dla grupy ochrony.

   **Całkowity rozmiar danych** to rozmiar danych, których kopii zapasowej chcesz uzyskać, a miejsce na dysku, które **ma być aprowizowane w u usługACH MABS,** jest miejscem zalecanym przez program MABS dla grupy ochrony. Mabs wybiera idealny wolumin kopii zapasowej, na podstawie ustawień. Opcje wielkości kopii zapasowej można jednak edytować w obszarze **Szczegóły przydziału dysku**. W przypadku obciążeń wybierz z menu rozwijanego preferowany magazyn. Edycja zmienia wartości pozycji **Całkowita ilość miejsca dla magazynu** i **Wolne miejsce w magazynie** w okienku **Dostępny magazyn dyskowy**. Niedostatecznie poparte dorozumie miejsca jest ilość pamięci masowej, którą MABS sugeruje dodanie do woluminu, aby kontynuować tworzenie kopii zapasowych płynnie w przyszłości.

8. Na stronie **Wybierz metodę tworzenia repliki** określ sposób, w jaki ma zostać wykonana początkowa replikacja danych w grupie ochrony. Jeśli **wybierzesz opcję Automatycznie replikuj przez sieć, zalecamy**wybranie godziny poza szczytem. W przypadku dużych ilości danych lub mniejszych niż optymalne warunki sieciowe należy rozważyć wybranie opcji **Ręczne**, które wymaga replikowania danych w trybie offline przy użyciu nośników wymiennych.

9. Na stronie **Opcje sprawdzania spójności** wybierz odpowiedni sposób automatyzacji sprawdzania spójności. Można włączyć uruchamianie sprawdzania tylko wtedy, gdy dane są niespójne, lub zgodnie z harmonogramem. Jeśli nie chcesz konfigurować automatycznego sprawdzania spójności, w dowolnym momencie możesz uruchomić sprawdzanie ręczne, klikając prawym przyciskiem myszy grupę ochrony i wybierając polecenie **Przeprowadź sprawdzanie spójności**.

    Po utworzeniu grupy ochrony następuje replikacja początkowa danych zgodnie z wybraną metodą. Po replikacji początkowej tworzone są poszczególne kopie zapasowe zgodnie z ustawieniami grupy ochrony. Jeśli chcesz odzyskać kopie zapasowe danych, zwróć uwagę na następujące kwestie:

## <a name="back-up-replica-virtual-machines"></a>Tworzenie kopii zapasowych maszyn wirtualnych repliki

Jeśli usługa MABS jest uruchomiona w systemie Windows Server 2012 R2 lub większej, można wyw. Jest to przydatne z kilku powodów:

**Ogranicza wpływ tworzenia kopii zapasowej na obciążenie pracą** — wykonywanie kopii zapasowej maszyny wirtualnej powoduje pewne nadmiarowe obciążenie podczas tworzenia migawki. Odciążając proces tworzenia kopii zapasowej do dodatkowej lokacji zdalnej, działanie nie jest już zagrożone operacją tworzenia kopii zapasowej. Ma to zastosowanie tylko do wdrożeń, w których kopia zapasowa jest przechowywana w lokalizacji zdalnej. Na przykład możesz korzystać z codziennych kopii zapasowych i przechowywać dane lokalnie w celu zapewnienia szybkiego przywracania, ale co miesiąc lub co kwartał tworzyć kopie zapasowe z maszyn wirtualnych replik przechowywanych zdalnie w celu przechowywania długoterminowego.

**Oszczędza przepustowość** — w typowych wdrożeniach w zdalnym oddziale lub siedzibie firmy potrzebne jest zapewnienie odpowiedniej przepustowości do transferu danych kopii zapasowych między lokacjami. Jeśli tworzysz strategię replikacji i pracy awaryjnej, oprócz opracowania strategii wykonywania kopii zapasowej danych możesz zmniejszyć ilość nadmiarowych danych przesyłanych w sieci. Tworząc kopię zapasową danych maszyny wirtualnej repliki, a nie podstawowego, można zapisać obciążenie związane z wysyłaniem kopii zapasowych danych za pośrednictwem sieci.

**Umożliwia wykonywanie kopii zapasowych dostawcy usług hostingowych** — hostowanego centrum danych można użyć jako lokalizacji repliki, co powoduje, że pomocnicze centrum danych nie jest potrzebne. W takim przypadku usługa SLA hostera wymaga spójnej kopii zapasowej maszyn wirtualnych replik.

Maszyna wirtualna repliki jest wyłączona do momentu zainicjowania trybu failover, a usługa VSS nie może zagwarantować kopii zapasowej maszyny wirtualnej repliki spójnej na poziomie aplikacji. W związku z tym kopia zapasowa repliki maszyny wirtualnej będzie tylko spójna na poziomie awarii. Jeśli nie można zagwarantować spójności na poziomie awarii, tworzenie kopii zapasowej zakończy się niepowodzeniem, a taka sytuacja może wystąpić w kilku przypadkach:

- Maszyna wirtualna repliki nie jest w dobrej kondycji i jest w stanie krytycznym.

- Maszyna wirtualna repliki jest ponownie synchronizowana (w stanie ponownej synchronizacji w toku lub w stanie konieczności wykonania ponownej synchronizacji).

- Początkowa replikacja maszyny wirtualnej między lokalizacją główną a pomocniczą jest w toku lub jest oczekiwana.

- Dzienniki hrl są stosowane do maszyny wirtualnej repliki lub poprzedniej akcji, aby zastosować .hrl dzienniki na dysku wirtualnym nie powiodło się lub został anulowany lub przerwany.

- Trwa migracja lub praca w trybie failover maszyny wirtualnej repliki

## <a name="recover-backed-up-virtual-machines"></a>Odzyskiwanie kopii zapasowych maszyn wirtualnych

Gdy możesz odzyskać kopię zapasową maszyny wirtualnej, użyj Kreatora odzyskiwania, aby wybrać maszynę wirtualną i określony punkt odzyskiwania. Aby otworzyć Kreatora odzyskiwania i odzyskać maszynę wirtualną:

1. W konsoli Administrator systemu MABS wpisz nazwę maszyny Wirtualnej lub rozwiń listę elementów chronionych i wybierz maszynę wirtualną, którą chcesz odzyskać.

2. W okienku **Punkty odzyskiwania dla** kliknij w kalendarzu dowolną datę, aby wyświetlić dostępne punkty odzyskiwania. Następnie w okienku **Ścieżka** wybierz punkt odzyskiwania, którego chcesz użyć w Kreatorze odzyskiwania.

3. W menu **Akcje** kliknij pozycję **Odzyskaj**, aby otworzyć Kreatora odzyskiwania.

    Wybrana maszyna wirtualna i punkt odzyskiwania są wyświetlane na ekranie **Przegląd wybranego elementu do odzyskania**. Kliknij przycisk **alej**.

4. Na ekranie **Wybieranie typu odzyskiwania** wybierz, dokąd chcesz przywrócić dane, a następnie kliknij przycisk **Dalej**.

    - **Odzyskiwanie do oryginalnego wystąpienia**: podczas odzyskiwania do oryginalnego wystąpienia oryginalny dysk VHD jest usuwany. Firma MABS odzyskuje dysk VHD i inne pliki konfiguracyjne do oryginalnej lokalizacji przy użyciu modułu zapisującego usługi VSS funkcji Hyper-V. Po zakończeniu procesu odzyskiwania maszyny wirtualne nadal zapewniają dużą dostępność.
        Aby odzyskiwanie mogło się odbyć, musi być dostępna grupa zasobów. Jeśli grupa nie jest dostępna, wykonaj odzyskiwanie do lokalizacji alternatywnej, a następnie przełącz maszynę wirtualną w tryb wysokiej dostępności.

    - **Odzyskaj jako maszynę wirtualną do dowolnego hosta:** MABS obsługuje alternatywne odzyskiwanie lokalizacji (ALR), które zapewnia bezproblemowe odzyskiwanie chronionej maszyny wirtualnej Funkcji Hyper-V do innego hosta funkcji Hyper-V, niezależnie od architektury procesora. Maszyny wirtualne funkcji Hyper-V, które są odzyskiwane do węzła klastra, nie będą mieć wysokiej dostępności. Jeśli wybierzesz tę opcję, Kreator odzyskiwania wyświetli dodatkowy ekran w celu identyfikacji miejsca docelowego i ścieżki docelowej.

    - **Kopiowanie do folderu sieciowego:** MABS obsługuje odzyskiwanie na poziomie elementu (ILR), które umożliwia odzyskiwanie plików, folderów, woluminów i wirtualnych dysków twardych (VHD) na poziomie hosta, z kopii zapasowej maszyn wirtualnych typu Hyper-V na poziomie elementu do udziału sieciowego lub woluminu na serwerze chronionym przez mabs. Agent ochrony MABS nie musi być zainstalowany wewnątrz gościa do wykonywania odzyskiwania na poziomie elementu. Jeśli wybierzesz tę opcję, Kreator odzyskiwania wyświetli dodatkowy ekran w celu identyfikacji miejsca docelowego i ścieżki docelowej.

5. W obszarze **Określanie opcji odzyskiwania** skonfiguruj opcje odzyskiwania i kliknij przycisk **Dalej**:

    - Jeśli odzyskujesz maszynę wirtualną w warunkach niskiej przepustowości, kliknij pozycję **Modyfikuj**, aby włączyć pozycję **Ograniczenie przepustowości sieci**. Po włączeniu opcji ograniczania przepustowości możesz określić przepustowość, którą chcesz udostępnić, i czas, kiedy ta przepustowość jest dostępna.
    - Jeśli sieć jest skonfigurowana, wybierz pozycję **Włącz odzyskiwanie na podstawie sieci SAN z użyciem migawek sprzętowych**.
    - Wybierz pozycję **Wyślij wiadomość e-mail po zakończeniu odzyskiwania**, a następnie wprowadź adresy e-mail, jeśli chcesz, aby powiadomienia e-mail były wysyłane po zakończeniu procesu odzyskiwania.

6. Na ekranie Podsumowanie upewnij się, że wszystkie szczegóły są poprawne. Jeśli szczegóły nie są poprawne lub chcesz wprowadzić zmiany, kliknij przycisk **Wstecz**. Jeśli ustawienia są poprawne, kliknij przycisk **Odzyskaj**, aby rozpocząć proces odzyskiwania.

7. Ekran **Stan odzyskiwania** zawiera informacje o zadaniu odzyskiwania.

## <a name="next-steps"></a>Następne kroki

[Recover data from Azure Backup Server (Odzyskiwanie danych z usługi Azure Backup Server)](https://docs.microsoft.com/azure/backup/backup-azure-alternate-dpm-server)
