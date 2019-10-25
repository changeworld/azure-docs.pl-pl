---
title: Ponowne włączanie ochrony maszyn wirtualnych z platformy Azure do lokacji lokalnej podczas odzyskiwania po awarii maszyn wirtualnych VMware i serwerów fizycznych | Microsoft Docs
description: Po przejściu w tryb failover na platformę Azure podczas odzyskiwania po awarii maszyn wirtualnych i serwerów fizycznych programu VMware należy dowiedzieć się, jak wrócić po awarii z platformy Azure do lokacji lokalnej.
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 10/22/2019
ms.author: mayg
ms.openlocfilehash: cf1ccdf953781ca9b9bd17152f2cf32677997d12
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/23/2019
ms.locfileid: "72791798"
---
# <a name="reprotect-and-fail-back-machines-to-an-on-premises-site-after-failover-to-azure"></a>Ponowne włączanie ochrony maszyn wirtualnych i ich przywracanie do lokacji lokalnej po przejściu do trybu failover na platformie Azure

Po przejściu do [trybu failover](site-recovery-failover.md) lokalnych maszyn wirtualnych VMware lub serwerów fizycznych na platformie Azure pierwszy krok powrotu po awarii do lokacji lokalnej polega na ponownej ochronie maszyn wirtualnych platformy Azure, które zostały utworzone podczas pracy w trybie failover. W tym artykule opisano, jak to zrobić. 

Aby zapoznać się z krótkim omówieniem, Obejrzyj poniższe wideo dotyczące sposobu przełączenia w tryb failover z platformy Azure do lokacji lokalnej.<br /><br />
> [!VIDEO https://channel9.msdn.com/Series/Azure-Site-Recovery/VMware-to-Azure-with-ASR-Video5-Failback-from-Azure-to-On-premises/player]


## <a name="before-you-begin"></a>Przed rozpoczęciem

Jeśli używasz szablonu do tworzenia maszyn wirtualnych, upewnij się, że każda maszyna wirtualna ma własny identyfikator UUID dla dysków. Jeśli wartość UUID lokalnego maszyny wirtualnej koliduje z identyfikatorem UUID głównego elementu docelowego, ponieważ obie zostały utworzone na podstawie tego samego szablonu, ponownej ochrony nie powiedzie się. Wdróż inny główny serwer docelowy, który nie został utworzony na podstawie tego samego szablonu. Zanotuj następujące informacje:
- Jeśli próbujesz wrócić do alternatywnego programu vCenter, upewnij się, że zostały wykryte nowe serwery vCenter i główny serwer docelowy. Typowy objaw polega na tym, że magazyny danych nie są dostępne lub nie są widoczne w oknie dialogowym Ponowne **Włączanie ochrony** .
- Aby przeprowadzić replikację z powrotem do lokalnego, potrzebne są zasady powrotu po awarii. Te zasady są tworzone automatycznie podczas tworzenia zasad kierunku do przodu. Zanotuj następujące informacje:
    - Te zasady są kojarzone z serwerem konfiguracji podczas jego tworzenia.
    - Te zasady nie są edytowalne.
    - Ustawienia wartości zasad to (próg RPO = 15 minut, czas przechowywania punktu odzyskiwania = 24 godziny, częstotliwość migawek spójności aplikacji = 60 min).
- Podczas ponownej ochrony i powrotu po awarii lokalny serwer konfiguracji musi być uruchomiony i połączony.
- Jeśli serwer vCenter zarządza maszynami wirtualnymi, do których nastąpi powrót po awarii, upewnij się, że masz [wymagane uprawnienia](vmware-azure-tutorial-prepare-on-premises.md#prepare-an-account-for-automatic-discovery) do odnajdywania maszyn wirtualnych na serwerach vCenter.
- Przed ponownym włączeniem ochrony usuń migawki na głównym serwerze docelowym. Jeśli migawki znajdują się na lokalnym serwerze docelowym lub na maszynie wirtualnej, ponownej ochrony kończy się niepowodzeniem. Migawki na maszynie wirtualnej są automatycznie scalane podczas zadania ponownego włączania ochrony.
- Wszystkie maszyny wirtualne grupy replikacji muszą mieć ten sam typ systemu operacyjnego (wszystkie Windows lub wszystkie systemy Linux). Grupa replikacji z mieszanymi systemami operacyjnymi nie jest obecnie obsługiwana w przypadku ponownego włączania ochrony i powrotu po awarii do lokacji lokalnej. Dzieje się tak, ponieważ główny element docelowy musi być tym samym systemem operacyjnym co maszyna wirtualna. Wszystkie maszyny wirtualne grupy replikacji muszą mieć ten sam główny element docelowy. 
- Główny element docelowy musi mieć taką samą lub nowszą wersję systemu operacyjnego niż wersje systemu operacyjnego zreplikowanych elementów.
- Serwer konfiguracji jest wymagany lokalnie w przypadku powrotu po awarii. Podczas powrotu po awarii maszyna wirtualna musi znajdować się w bazie danych serwera konfiguracji. W przeciwnym razie powrót po awarii nie powiedzie się. Upewnij się, że tworzysz regularnie zaplanowane kopie zapasowe serwera konfiguracji. Jeśli wystąpi awaria, przywróć serwer o tym samym adresie IP, aby działanie powrotu po awarii zadziałało. 
- Ponownej ochrony i powrotu po awarii wymagają sieci VPN typu lokacja-lokacja (S2S) lub prywatnej komunikacji równorzędnej ExpressRoute w celu replikowania danych. Zapewnianie sieci, aby maszyny wirtualne w trybie failover na platformie Azure mogły nawiązać połączenie (ping) na lokalnym serwerze konfiguracji. Należy wdrożyć serwer przetwarzania w sieci platformy Azure dla maszyn wirtualnych zakończonych niepowodzeniem. Ten serwer przetwarzania musi być również w stanie komunikować się z lokalnym serwerem konfiguracji i główny serwer docelowy.
- W przypadku, gdy adresy IP replikowanych elementów zostały zachowane w trybie failover, należy nawiązać połączenie S2S lub ExpressRoute między maszynami wirtualnymi platformy Azure i kartą sieciową powrotu po awarii serwera konfiguracji. Należy pamiętać, że przechowywanie adresów IP wymaga, aby serwer konfiguracji miał dwie karty sieciowe — jedną dla łączności z maszynami źródłowymi i jedną dla łączności z powrotem po awarii platformy Azure. Dzieje się tak, aby uniknąć nakładania się zakresów adresów podsieci źródła i maszyn wirtualnych w trybie failover.
- Upewnij się, że następujące porty zostały otwarte w celu przełączenia w tryb failover i powrotu po awarii:

    ![Porty trybu failover i powrotu po awarii](./media/vmware-azure-reprotect/failover-failback.png)

- Przeczytaj wszystkie [wymagania wstępne dotyczące portów i adresów URL listy dozwolonych](vmware-azure-deploy-configuration-server.md#prerequisites).

## <a name="deploy-a-process-server-in-azure"></a>Wdrażanie serwera przetwarzania na platformie Azure

Serwer przetwarzania na platformie Azure jest potrzebny przed powrotem po awarii do lokacji lokalnej:

- Serwer przetwarzania otrzymuje dane z chronionych maszyn wirtualnych na platformie Azure, a następnie wysyła dane do lokacji lokalnej.
- Między serwerem przetwarzania a chronioną maszyną wirtualną wymagana jest sieć o małym opóźnieniu. W związku z tym zaleca się wdrożenie serwera przetwarzania na platformie Azure. Wydajność replikacji jest wyższa, jeśli serwer przetwarzania jest bliższy replikacji maszyny wirtualnej (maszyna przełączona w tryb failover na platformie Azure). 
- Aby sprawdzić koncepcję, można użyć lokalnego serwera przetwarzania i ExpressRoute z prywatną komunikację równorzędną.

Aby wdrożyć serwer przetwarzania na platformie Azure:

1. Jeśli musisz wdrożyć serwer przetwarzania na platformie Azure, zobacz [Konfigurowanie serwera przetwarzania na platformie Azure na potrzeby powrotu po awarii](vmware-azure-set-up-process-server-azure.md).
2. Maszyny wirtualne platformy Azure wysyłają dane replikacji do serwera przetwarzania. Skonfiguruj sieci, aby maszyny wirtualne platformy Azure mogły nawiązać połączenie z serwerem przetwarzania.
3. Należy pamiętać, że replikacja z platformy Azure do lokalnego może nastąpić tylko za pośrednictwem sieci VPN S2S lub prywatnej komunikacji równorzędnej sieci ExpressRoute. Upewnij się, że za pośrednictwem tego kanału sieciowego jest dostępna wystarczająca przepustowość.

## <a name="deploy-a-separate-master-target-server"></a>Wdróż oddzielny główny serwer docelowy

Główny serwer docelowy odbiera dane podczas powrotu po awarii. Domyślnie główny serwer docelowy jest uruchamiany na lokalnym serwerze konfiguracji. Jednak w zależności od ilości ruchu z powrotem do tyłu może być konieczne utworzenie oddzielnego głównego serwera docelowego na potrzeby powrotu po awarii. Oto jak utworzyć:

* [Utwórz główny serwer docelowy z systemem Linux](vmware-azure-install-linux-master-target.md) na potrzeby powrotu po awarii maszyn wirtualnych z systemem Linux. Jest to wymagane. Należy pamiętać, że główny serwer docelowy w systemie LVM nie jest obsługiwany.
* Opcjonalnie można utworzyć oddzielny główny serwer docelowy dla powrotu po awarii maszyny wirtualnej z systemem Windows. W tym celu należy ponownie uruchomić ujednoliconą konfigurację i wybrać opcję utworzenia głównego serwera docelowego. [Dowiedz się więcej](site-recovery-plan-capacity-vmware.md#deploy-additional-master-target-servers). 

Po utworzeniu głównego serwera docelowego wykonaj następujące zadania:

- Jeśli maszyna wirtualna jest obecna lokalnie na serwerze vCenter, główny serwer docelowy musi mieć dostęp do pliku dysku maszyny wirtualnej (VMDK) lokalnej maszyny wirtualnej. Dostęp jest wymagany do zapisania replikowanych danych na dyskach maszyny wirtualnej. Upewnij się, że magazyn danych lokalnej maszyny wirtualnej jest zainstalowany na hoście głównego elementu docelowego z dostępem do odczytu i zapisu.
- Jeśli maszyna wirtualna nie jest obecna w środowisku lokalnym na serwerze vCenter, Usługa Site Recovery musi utworzyć nową maszynę wirtualną podczas ponownej ochrony. Ta maszyna wirtualna jest tworzona na hoście ESX, na którym tworzysz główny element docelowy. Należy uważnie wybrać hosta ESX, aby maszyna wirtualna powrotu po awarii została utworzona na żądanym hoście.
- Nie można użyć vMotion magazynu dla głównego serwera docelowego. Użycie vMotion magazynu dla głównego serwera docelowego może spowodować niepowodzenie powrotu po awarii. Nie można uruchomić maszyny wirtualnej, ponieważ dyski nie są dostępne. Aby temu zapobiec, należy wykluczyć główne serwery docelowe z listy vMotion.
- Jeśli główny obiekt docelowy przeprowadzi zadanie vMotion magazynu po ponownej ochronie, chronione dyski maszyny wirtualnej, które są dołączone do głównego elementu docelowego, są migrowane do obiektu docelowego zadania vMotion. Próba powrotu z powrotem po awarii spowoduje niepowodzenie odłączenia dysku, ponieważ nie znaleziono dysków. Następnie trudno znaleźć dyski na kontach magazynu. Należy je znaleźć ręcznie i dołączyć do maszyny wirtualnej. Następnie można uruchomić lokalną maszynę wirtualną.
- Dodaj dysk przechowywania do istniejącego głównego serwera docelowego systemu Windows. Dodaj nowy dysk i sformatuj dysk. Dysk przechowywania służy do zatrzymania punktu w czasie, gdy maszyna wirtualna jest replikowana z powrotem do lokacji lokalnej. Poniżej przedstawiono niektóre kryteria dysku przechowywania. Jeśli te kryteria nie są spełnione, dysk nie jest wyświetlany na liście dla głównego serwera docelowego:
    - Wolumin nie jest używany do żadnego innego celu, takiego jak element docelowy replikacji.
    - Wolumin nie jest w trybie blokady.
    - Wolumin nie jest woluminem pamięci podręcznej. Na tym woluminie nie można przeprowadzić instalacji głównego elementu docelowego. Niestandardowy wolumin instalacyjny serwera przetwarzania i głównego elementu docelowego nie kwalifikuje się do woluminu przechowywania. Gdy serwer przetwarzania i główny element docelowy są zainstalowane w woluminie, wolumin jest woluminem pamięci podręcznej głównego serwera docelowego.
    - Typ systemu plików woluminu nie jest w systemie FAT lub FAT32.
    - Pojemność woluminu jest różna od zera.
    - Domyślnym woluminem przechowywania dla systemu Windows jest wolumin R.
    - Domyślnym woluminem przechowywania dla systemu Linux jest/mnt/Retention.
- Nowy dysk należy dodać w przypadku korzystania z istniejącej maszyny serwera przetwarzania/serwera konfiguracji lub skali lub serwera przetwarzania/głównego serwera docelowego. Nowy dysk musi spełniać powyższe wymagania. Jeśli dysk przechowywania nie istnieje, nie jest wyświetlany na liście rozwijanej wyboru w portalu. Po dodaniu dysku do lokalnego głównego serwera docelowego potrwa to 15 minut, zanim dysk zostanie wyświetlony w wybranym portalu. Można również odświeżyć serwer konfiguracji, jeśli dysk nie jest wyświetlany po 15 minutach.
- Zainstaluj narzędzia VMware lub narzędzia Otwórz maszynę wirtualną na głównym serwerze docelowym. Bez narzędzi nie można wykryć magazynów danych na hoście ESXi głównego elementu docelowego.
- Ustaw ustawienie `disk.EnableUUID=true` w parametrach konfiguracji głównej docelowej maszyny wirtualnej w oprogramowaniu VMware. Jeśli ten wiersz nie istnieje, Dodaj go. To ustawienie jest wymagane, aby zapewnić spójny identyfikator UUID w VMDK, tak aby był poprawnie instalowany.
- Na hoście ESX, na którym tworzony jest główny element docelowy, musi być dołączony co najmniej jeden magazyn danych maszyny wirtualnej (VMFS). Jeśli nie są dołączone żadne magazyny danych VMFS, dane wejściowe ze **sklepu datastore** na stronie ponownej ochrony są puste i nie będzie można ich wykonać.
- Główny serwer docelowy nie może mieć migawek na dyskach. Jeśli istnieją migawki, reochrona i powrót po awarii, zakończy się niepowodzeniem.
- Główny element docelowy nie może mieć kontrolera SCSI Paravirtual. Kontroler może być tylko kontrolerem logiki LSI. W przypadku braku kontrolera logiki LSI nie jest przeprowadzana ochrona.
- Dla każdego wystąpienia główny element docelowy może mieć co najwyżej 60 dysków. Jeśli liczba maszyn wirtualnych, które są ponownie chronione w lokalnym głównym systemie docelowym, ma więcej niż łączna liczba dysków 60, ponowne włączenie ochrony do głównego celu nie powiedzie się. Upewnij się, że masz wystarczającą ilość głównych docelowych gniazd dysku lub Wdróż dodatkowe główne serwery docelowe.
    

## <a name="enable-reprotection"></a>Włącz ponownie ochronę

Po uruchomieniu maszyny wirtualnej na platformie Azure program Agent może zarejestrować się ponownie na serwerze konfiguracji (do 15 minut). W tym czasie nie będzie można ponownie włączyć ochrony i zostanie wyświetlony komunikat o błędzie informujący, że Agent nie jest zainstalowany. W takim przypadku poczekaj kilka minut, a następnie spróbuj ponownie wykonać ponowną ochronę:


1. Wybierz **magazyn** > **zreplikowane elementy**. Kliknij prawym przyciskiem myszy maszynę wirtualną, która przełączona w tryb failover, a następnie wybierz pozycję **Włącz ponownie ochronę**. Lub z przycisków poleceń wybierz maszynę, a następnie wybierz pozycję **Włącz ponownie ochronę**.
2. Upewnij się, że wybrano kierunek ochrony na **platformie Azure do lokalnego** .
3. Na **głównym serwerze docelowym** i **serwerze przetwarzania**wybierz lokalny główny serwer docelowy i serwer przetwarzania.  
4. W obszarze **Magazyn**danych wybierz magazyn danych, do którego chcesz odzyskać dyski lokalnie. Ta opcja jest używana, gdy lokalna maszyna wirtualna jest usuwana i konieczne jest utworzenie nowych dysków. Ta opcja jest ignorowana, jeśli dyski już istnieją. Nadal trzeba określić wartość.
5. Wybierz dysk przechowywania.
6. Zasady powrotu po awarii są wybierane automatycznie.
7. Wybierz **przycisk OK** , aby rozpocząć reochronę. Zadanie zaczyna replikować maszynę wirtualną z platformy Azure do lokacji lokalnej. Postęp można śledzić na karcie **zadania** . Po pomyślnym zakończeniu ochrony maszyna wirtualna przechodzi w stan chroniony.

Zanotuj następujące informacje:
- Jeśli chcesz odzyskać do lokalizacji alternatywnej (po usunięciu lokalnej maszyny wirtualnej), wybierz dysk przechowywania i magazyn danych, które są skonfigurowane dla głównego serwera docelowego. Po powrocie po awarii do lokacji lokalnej maszyny wirtualne VMware w planie ochrony powrotu po awarii używają tego samego magazynu danych co główny serwer docelowy. Utworzona zostanie nowa maszyna wirtualna w programie vCenter.
- Jeśli chcesz odzyskać maszynę wirtualną na platformie Azure do istniejącej lokalnej maszyny wirtualnej, zainstaluj magazyny danych lokalnych maszyn wirtualnych z dostępem do odczytu/zapisu na hoście ESXi głównego serwera docelowego.

    ![Okno dialogowe Ponowne włączanie ochrony](./media/vmware-azure-reprotect/reprotectinputs.png)

- Możesz również ponownie włączyć ochronę na poziomie planu odzyskiwania. Grupę replikacji można ponownie chronić tylko za pomocą planu odzyskiwania. Po ponownym włączeniu ochrony przy użyciu planu odzyskiwania należy podać wartości dla każdej chronionej maszyny.
- Użyj tego samego głównego serwera docelowego do ponownego włączenia ochrony grupy replikacji. W przypadku użycia innego głównego serwera docelowego do ponownego włączenia ochrony grupy replikacji serwer nie może zapewnić typowego punktu w czasie.
- Lokalna maszyna wirtualna jest wyłączona podczas ponownej ochrony. Pomaga to zapewnić spójność danych podczas replikacji. Nie włączaj maszyny wirtualnej po zakończeniu ponownej ochrony.



## <a name="common-issues"></a>Typowe problemy

- W przypadku przeprowadzania odnajdywania vCenter użytkownika tylko do odczytu i ochrony maszyn wirtualnych ochrona kończy się powodzeniem, a tryb failover działa. W trakcie ochrony tryb failover nie powiedzie się, ponieważ nie można odnaleźć magazynów danych. Objaw polega na tym, że magazyny danych nie są wyświetlane na liście podczas jej ochrony. Aby rozwiązać ten problem, możesz zaktualizować poświadczenia vCenter przy użyciu odpowiedniego konta z uprawnieniami, a następnie ponowić zadanie. 
- W przypadku powrotu po awarii maszyny wirtualnej z systemem Linux i uruchomienia jej w środowisku lokalnym można zobaczyć, że pakiet Menedżera sieci został odinstalowany z komputera. Ta Dezinstalacja jest wykonywana, ponieważ pakiet Menedżera sieci jest usuwany podczas odzyskiwania maszyny wirtualnej na platformie Azure.
- W przypadku skonfigurowania maszyny wirtualnej z systemem Linux przy użyciu statycznego adresu IP i przełączeniu w tryb failover na platformę Azure adres IP jest pobierany z serwera DHCP. Po przełączeniu w tryb failover do lokalnego, maszyna wirtualna będzie w dalszym ciągu używać protokołu DHCP w celu uzyskania adresu IP. Zaloguj się ręcznie na maszynie, a następnie w razie potrzeby ustaw adres IP z powrotem na adres statyczny. Maszyna wirtualna z systemem Windows może ponownie uzyskać swój statyczny adres IP.
- Jeśli używasz wersji bezpłatnej programu ESXi 5,5 lub vSphere 6 funkcji hypervisor, tryb failover zakończy się pomyślnie, ale powrót po awarii nie powiedzie się. Aby włączyć powrót po awarii, Uaktualnij do wersji ewaluacyjnej programu.
- Jeśli nie można skontaktować się z serwerem konfiguracji z serwera przetwarzania, należy użyć programu Telnet do sprawdzenia łączności z serwerem konfiguracji na porcie 443. Możesz również spróbować wysłać polecenie ping do serwera konfiguracji z serwera przetwarzania. Serwer przetwarzania powinien również mieć puls, gdy jest połączony z serwerem konfiguracji.
- Serwer z systemem Windows Server 2008 R2 z dodatkiem SP1, który jest chroniony jako fizyczny serwer lokalny, nie może powrócić z powrotem z platformy Azure do lokacji lokalnej.
- Nie można przeprowadzić powrotu po awarii w następujących okolicznościach:
    - Maszyny wirtualne zostały zmigrowane na platformie Azure. [Dowiedz się więcej](migrate-overview.md#what-do-we-mean-by-migration).
    - Maszyna wirtualna została przeniesiona do innej grupy zasobów.
    - Maszyna wirtualna platformy Azure została usunięta.
    - Ochrona maszyny wirtualnej została wyłączona.
    - Maszyna wirtualna została utworzona ręcznie na platformie Azure. Maszyna powinna być początkowo chroniona lokalnie i przełączona w tryb failover na platformę Azure przed ponowną ochroną.
    - Można się nie powieść tylko dla hosta ESXi. Nie można przeprowadzić powrotu po awarii maszyn wirtualnych VMware lub serwerów fizycznych do hostów funkcji Hyper-V, maszyn fizycznych ani stacji roboczych VMware.


## <a name="next-steps"></a>Następne kroki

Po wprowadzeniu stanu chronionego przez maszynę wirtualną można [zainicjować powrót po awarii](vmware-azure-failback.md). Powrót po awarii zamyka maszynę wirtualną na platformie Azure i uruchamia lokalną maszynę wirtualną. Oczekiwano pewnego przestoju dla aplikacji. Wybierz godzinę powrotu po awarii, gdy aplikacja będzie mogła tolerować przestoje.


