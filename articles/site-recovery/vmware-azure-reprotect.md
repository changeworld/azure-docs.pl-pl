---
title: Ponowne włączanie ochrony maszyn wirtualnych platformy Azure do lokacji lokalnej podczas odzyskiwania po awarii maszyn wirtualnych VMware i serwerów fizycznych | Dokumentacja firmy Microsoft
description: Po przejściu w tryb failover na platformie Azure podczas odzyskiwania po awarii maszyn wirtualnych VMware i serwerów fizycznych Dowiedz się, jak się nie powieść powrót po awarii z platformy Azure do lokacji lokalnej.
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 3/12/2019
ms.author: mayg
ms.openlocfilehash: 4202d95b540efb98b526f8a8abd17da22a908ebe
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60482935"
---
# <a name="reprotect-and-fail-back-machines-to-an-on-premises-site-after-failover-to-azure"></a>Ponowne włączanie ochrony i zakończyć się niepowodzeniem maszynami wstecz do lokacji lokalnej po włączeniu trybu failover na platformie Azure

Po [trybu failover](site-recovery-failover.md) lokalnych maszyn wirtualnych z programu VMware lub serwery fizyczne do platformy Azure, pierwszym krokiem przechodzenia do lokacji sieci lokalnej jest do ponownego włączenia ochrony maszyn wirtualnych platformy Azure, które zostały utworzone podczas pracy awaryjnej. W tym artykule opisano, jak to zrobić. 

Aby uzyskać szybki przegląd Obejrzyj poniższy film o tym, jak do trybu failover z platformy Azure do lokacji lokalnej.<br /><br />
> [!VIDEO https://channel9.msdn.com/Series/Azure-Site-Recovery/VMware-to-Azure-with-ASR-Video5-Failback-from-Azure-to-On-premises/player]


## <a name="before-you-begin"></a>Przed rozpoczęciem

Jeśli szablon jest używany do tworzenia maszyn wirtualnych, upewnij się, że każda maszyna wirtualna ma swój własny identyfikator UUID dysków. UUID lokalnej maszyny wirtualnej jest niezgodna ze UUID głównego elementu docelowego, ponieważ oba zostały utworzone przy użyciu tego samego szablonu, ponownego włączania ochrony kończy się niepowodzeniem. Wdróż innego głównego elementu docelowego, który nie został utworzony na podstawie tego samego szablonu. Zanotuj następujące informacje:
- Jeśli chcesz powrócić po awarii do alternatywnego vCenter, upewnij się, że są odnajdywane nowego serwera vCenter i główny serwer docelowy. Typowym symptomem jest, że magazynów danych jest niedostępna lub nie są widoczne w **ponownie włączyć ochronę** okno dialogowe.
- Replikacja w środowisku lokalnym, potrzebne są zasady powrotu po awarii. Te zasady są tworzone automatycznie podczas tworzenia zasad kierunku do przodu. Zanotuj następujące informacje:
    - Te zasady są skojarzone z serwerem konfiguracji podczas tworzenia automatycznie.
    - Ta zasada nie jest edytowalny.
    - Ustawianie wartości zasad są (próg celu punktu odzyskiwania = 15 minut, czas przechowywania punktu odzyskiwania = 24 godziny i częstotliwość migawek spójności aplikacji = 60 minut).
- Podczas ponownego włączania ochrony i powrotu po awarii na lokalnym serwerze konfiguracji musi być uruchomiona i połączona.
- Jeśli serwer vCenter zarządza maszynami wirtualnymi, do których można będzie wykonać powrotu po awarii, upewnij się, że masz [wymagane uprawnienia](vmware-azure-tutorial-prepare-on-premises.md#prepare-an-account-for-automatic-discovery) odnajdowania maszyn wirtualnych na serwerach vCenter.
- Przed można ponownie włączyć ochronę, należy usunąć migawki na głównym serwerze docelowym. W przypadku migawki głównego elementu docelowego w środowisku lokalnym lub maszynie wirtualnej ponownego włączania ochrony kończy się niepowodzeniem. Migawki maszyny wirtualnej są automatycznie łączone podczas wykonywania zadania ponownego włączania ochrony.
- Wszystkie maszyny wirtualne grupy replikacji musi być tego samego typu systemu operacyjnego (wszystkie Windows lub Linux wszystkie). Grupy replikacji przy użyciu aktualnie mieszane systemy operacyjne nie jest obsługiwane dla ponowne włączanie ochrony i powrotu po awarii do środowiska lokalnego. Jest to spowodowane głównego elementu docelowego musi być tego samego systemu operacyjnego maszyny wirtualnej. Wszystkie maszyny wirtualne w grupie replikacji musi mieć ten sam główny element docelowy. 
- Serwer konfiguracji jest wymagane w środowisku lokalnym, gdy możesz wykonać powrotu po awarii. Podczas powrotu po awarii maszyny wirtualnej musi istnieć w bazie danych serwera konfiguracji. W przeciwnym razie podczas powrotu po awarii zakończy się niepowodzeniem. Upewnij się, że przesłać regularnie zaplanowane kopie zapasowe serwera konfiguracji. W przypadku awarii, należy przywrócić na serwerze przy użyciu tego samego adresu IP tak, aby działa w przypadku powrotu po awarii.
- Ponownego włączania ochrony i powrotu po awarii wymaga sieci VPN lokacja lokacja (S2S) do replikacji danych. Podaj sieci tak, aby w trybie failed-over maszyn wirtualnych na platformie Azure ma dostęp (ping) — lokalna konfiguracja serwera. Można także wdrożyć serwer przetwarzania na platformie Azure sieci maszyny wirtualnej w trybie failed-over. Należy również może komunikować się z lokalnym serwerem konfiguracji tego serwera przetwarzania.
- Upewnij się, że otworzyć następujące porty dla trybu failover i powrotu po awarii:

    ![Porty dla trybu failover i powrotu po awarii](./media/vmware-azure-reprotect/failover-failback.png)

- Odczyt wszystkich [wymagania wstępne dotyczące portów i adresów URL umieszczania na białej liście](vmware-azure-deploy-configuration-server.md#prerequisites).

## <a name="deploy-a-process-server-in-azure"></a>Wdrażanie serwera przetwarzania na platformie Azure

Serwer przetwarzania na platformie Azure może być konieczne przed powrotu po awarii do lokacji lokalnej:
- Serwer przetwarzania odbiera dane z chronionej maszyny wirtualnej na platformie Azure, a następnie wysyła dane do lokacji lokalnej.
- Sieci o małych opóźnieniach jest wymagany między serwerem przetwarzania i chronionej maszyny wirtualnej. Ogólnie rzecz biorąc należy wziąć pod uwagę czas oczekiwania podczas podejmowania decyzji, czy należy serwer przetwarzania na platformie Azure:
    - Jeśli masz połączenie Azure ExpressRoute skonfigurować, można użyć lokalnego serwera przetwarzania do przesyłania danych, ponieważ brakuje opóźnienia między maszyną wirtualną a serwerem przetwarzania.
    - Jednak w przypadku sieci VPN S2S zalecamy wdrożenie serwera przetwarzania na platformie Azure.
    - Firma Microsoft zaleca korzystanie z serwerem przetwarzania opartych na platformie Azure podczas powrotu po awarii. Wydajność replikacji jest większe, jeśli serwer przetwarzania jest bliżej replikacji maszyny wirtualnej (maszyny Failover na platformie Azure). Weryfikację koncepcji można użyć serwera przetwarzania w środowisku lokalnym i usługi ExpressRoute za pomocą prywatnej komunikacji równorzędnej.

Aby wdrożyć serwer przetwarzania na platformie Azure:

1. Jeśli musisz wdrożyć serwer przetwarzania na platformie Azure, zobacz [skonfigurować serwer przetwarzania na platformie Azure do powrotu po awarii](vmware-azure-set-up-process-server-azure.md).
2. Maszyny wirtualne platformy Azure wysyłać dane replikacji do serwera przetwarzania. Skonfigurowanie sieci, tak aby maszyny wirtualne platformy Azure można uzyskać dostęp do serwera przetwarzania.
3. Należy pamiętać, replikacja z platformy Azure do serwera lokalnego, może się zdarzyć tylko za pośrednictwem sieci VPN S2S lub prywatną komunikację równorzędną sieci usługi ExpressRoute. Upewnij się, że wystarczającą przepustowość jest dostępna za pośrednictwem tego kanału sieci.

## <a name="deploy-a-separate-master-target-server"></a>Wdrożyć oddzielny główny serwer docelowy

Główny serwer docelowy odbiera dane podczas powrotu po awarii. Domyślnie główny serwer docelowy jest uruchamiany na lokalnym serwerze konfiguracji. W zależności od ilości ruchu do tyłu, może być konieczne do utworzenia osobny główny serwer docelowy do powrotu po awarii. Poniżej przedstawiono instrukcje tworzenia takiego:

* [Tworzenie serwera głównego elementu docelowego Linux](vmware-azure-install-linux-master-target.md) powrotu po awarii maszyn wirtualnych systemu Linux. Jest to wymagane. Należy zauważyć, że główna serwera docelowego na LVM nie jest obsługiwana.
* Opcjonalnie utwórz osobny główny serwer docelowy do powrotu po awarii maszyn wirtualnych Windows. Aby to zrobić, uruchom ponownie ujednoliconego Instalatora i wybierz, aby utworzyć główny serwer docelowy. [Dowiedz się więcej](site-recovery-plan-capacity-vmware.md#deploy-additional-master-target-servers). 

Po utworzeniu głównego serwera docelowego, wykonaj następujące czynności:

- Jeśli maszyna wirtualna jest obecny w środowisku lokalnym na serwerze vCenter, główny serwer docelowy wymaga dostępu do pliku dysku maszyny wirtualnej (VMDK) maszyny wirtualnej w środowisku lokalnym. Wymagany jest dostęp do zapisu dysków maszyn wirtualnych replikowanych danych. Upewnij się, że magazyn danych lokalnej maszyny wirtualnej jest zainstalowany na hoście głównego serwera docelowego z dostępem do odczytu/zapisu.
- Jeśli maszyna wirtualna nie jest obecny w środowisku lokalnym na serwerze vCenter, Usługa Site Recovery musi utworzyć nową maszynę wirtualną podczas ponownego włączania ochrony. Ta maszyna wirtualna jest tworzona na hoście ESX, na którym utworzono główny cel. Rozważnych hosta ESX, tak aby maszyny wirtualnej powrotu po awarii jest tworzona na hoście, który chcesz.
- Nie można używać narzędzia Storage vMotion na głównym serwerze docelowym. Przy użyciu narzędzia Storage vMotion dla głównego serwera docelowego może spowodować powrót po awarii nie powiedzie się. Nie można uruchomić maszyny wirtualnej, ponieważ dyski nie są dostępne do niego. Aby temu zapobiec, Wyklucz główne serwery docelowe z listy vMotion.
- Główny element docelowy ulega Storage vMotion zadania po ponownego włączania ochrony, dyski chronionej maszyny wirtualnej, które są dołączone do głównego elementu docelowego migrować do obiektu docelowego zadania vMotion. Jeśli zostanie podjęta próba się nie powieść po to, odłączenie dysku nie powiedzie się, ponieważ nie znajdują się dyski. Następnie staje się trudne do znalezienia dyski na kontach usługi storage. Musisz ręcznie znaleźć i dołącz je do maszyny wirtualnej. Po tym można uruchomić maszyny wirtualnej w środowisku lokalnym.
- Dodaj dysk przechowywania do istniejącego serwera głównego elementu docelowego Windows. Dodaj nowy dysk i sformatuj dysk. Dysk przechowywania jest używana do zatrzymywania punkty w czasie, gdy maszyna wirtualna ponownie będzie replikowana na lokacji lokalnej. Poniżej podano niektóre kryteria dotyczące dysku przechowywania. Jeśli nie są spełnione poniższe kryteria, dysku nie ma na liście dla głównego serwera docelowego:
    - Wolumin nie jest używana do innych celów, takich jak elementu docelowego replikacji.
    - Wolumin nie jest w trybie blokady.
    - Wolumin nie jest woluminem pamięci podręcznej. Instalacja głównego elementu docelowego nie może istnieć na tym woluminie. Wolumin instalacji niestandardowej dla elementu docelowego serwera i wzorzec procesu nie kwalifikuje się wolumin przechowywania. Gdy serwer przetwarzania i główny serwer docelowy są zainstalowane na woluminie, wolumin jest woluminem pamięci podręcznej głównego elementu docelowego.
    - Typ systemu plików woluminu nie jest w systemie FAT lub FAT32.
    - Pojemność woluminu jest różna od zera.
    - Domyślnym woluminem przechowywania dla Windows jest wolumin R.
    - Domyślnym woluminem przechowywania dla systemu Linux jest /mnt/retention.
- Jeśli używasz istniejącej maszyny serwera konfiguracji/serwera przetwarzania lub skalowania lub proces maszyny serwera docelowego serwera/główną, należy dodać nowy dysk. Nowy dysk musi spełniać wymagania poprzedniej. Jeśli dysk przechowywania nie jest obecny, nie są wyświetlane na liście rozwijanej wyboru w portalu. Po dodaniu dysku do głównego elementu docelowego w środowisku lokalnym, trwa maksymalnie 15 minut na dysku, które pojawią się w zaznaczenia w portalu. Możesz też odświeżyć serwera konfiguracji, jeśli dysk nie pojawia się po 15 minutach.
- Zainstaluj narzędzia VMware lub narzędzia w przypadku maszyn wirtualnych Otwórz na głównym serwerze docelowym. Bez narzędzi nie można wykryć magazynów danych na hoście ESXi głównego serwera docelowego.
- Ustaw `disk.EnableUUID=true` ustawienie za pomocą parametrów konfiguracji maszyny wirtualnej główny serwer docelowy w środowisku VMware. Jeśli ten wiersz nie istnieje, należy go dodać. To ustawienie jest wymagane w celu zapewnienia spójnego UUID VMDK tak, aby go na komputerze instalująca poprawnie.
- Host ESX, na którym tworzony jest głównego elementu docelowego musi mieć co najmniej jedną maszynę wirtualną pliku system (VMFS) magazynu danych dołączono do niego. Jeśli są dołączone nie VMFS magazynów danych, **Datastore** danych wejściowych na stronie ponownej ochrony jest pusta i nie można kontynuować.
- Główny serwer docelowy nie może mieć migawek na dyskach. W przypadku migawki ponownego włączania ochrony i powrotu po awarii się nie powieść.
- Głównego elementu docelowego nie może mieć zdefiniowany kontroler Paravirtual SCSI. Kontroler może składać się z kontrolera LSI logiki. Bez kontrolera LSI Logic ponownego włączania ochrony kończy się niepowodzeniem.
- Dla każdego wystąpienia głównego elementu docelowego może mieć co najwyżej 60 dysków podłączone do niego. Jeśli liczba maszyn wirtualnych, które jest przełączona do trybu do głównego elementu docelowego w środowisku lokalnym ma więcej niż łączna liczba 60 dysków, reprotects do głównego elementu docelowego się nie powiedzie się. Upewnij się, że masz wystarczająco dużo wzorzec docelowe miejsca na dysku albo wdrożyć dodatkowe główne serwery docelowe.
    

## <a name="enable-reprotection"></a>Włączanie ponownego włączania ochrony

Po uruchomieniu maszyny wirtualnej na platformie Azure, dopiero po pewnym czasie dla agenta zarejestrować serwer konfiguracji (maksymalnie 15 minut). W tym czasie nie będzie można ponownie włączyć ochronę, a komunikat o błędzie wskazuje, że agent nie jest zainstalowany. W takim przypadku poczekaj kilka minut, a następnie spróbuj ponownie ponownego włączania ochrony:


1. Wybierz **magazynu** > **zreplikowane elementy**. Kliknij prawym przyciskiem myszy maszynę wirtualną, która przełączone w tryb failover, a następnie wybierz **ponownego włączenia ochrony**. Alternatywnie dla przycisku polecenia, wybierz maszynę, a następnie wybierz **ponownego włączenia ochrony**.
2. Upewnij się, że **platformy Azure do środowiska lokalnego** wybrano kierunek ochrony.
3. W **główny serwer docelowy** i **serwera przetwarzania**, wybierz serwer główny serwer docelowy w środowisku lokalnym i serwera przetwarzania.  
4. Aby uzyskać **Datastore**, wybierz magazyn danych, do której chcesz odzyskać dyski w środowisku lokalnym. Ta opcja jest używana w środowisku lokalnym maszyna wirtualna zostanie usunięta, gdy trzeba utworzyć nowe dyski. Ta opcja jest ignorowana, jeśli dyski już istnieją. Nadal należy określić wartość.
5. Wybierz dysk przechowywania.
6. Zasady powrotu po awarii są wybierane automatycznie.
7. Wybierz **OK** można rozpocząć ponowne włączanie ochrony. Zadanie zaczyna replikować maszynę wirtualną z platformy Azure do lokacji lokalnej. Na karcie **Zadania** można śledzić postęp. Po pomyślnym zakończeniu ponownego włączania ochrony, maszyny wirtualne przechodzą w stanie chronionym.

Zanotuj następujące informacje:
- Jeśli chcesz odzyskać do alternatywnej lokalizacji (w przypadku usunięcia lokalnej maszyny wirtualnej), wybierz dysk przechowywania i magazynu danych, które są skonfigurowane dla głównego serwera docelowego. Podczas powrotu po awarii do lokacji lokalnej, maszyn wirtualnych VMware w planie ochrony podczas powrotu po awarii będzie używać tego samego magazynu danych jako główny serwer docelowy. Nowa maszyna wirtualna jest tworzona w programie vCenter.
- Jeśli chcesz odzyskać maszynę wirtualną na platformie Azure do istniejącej maszyny wirtualnej w środowisku lokalnym, należy zainstalować maszyny wirtualnej lokalnych magazynów danych, z dostępem do odczytu/zapisu na hoście ESXi głównego serwera docelowego.

    ![Ponowne włączanie ochrony okno dialogowe](./media/vmware-azure-reprotect/reprotectinputs.png)

- Użytkownik może również ponowne włączanie ochrony na poziomie planu odzyskiwania. Grupy replikacji może być przełączona do trybu wyłącznie za pośrednictwem planu odzyskiwania. Gdy możesz ponownie włączyć ochronę za pomocą planu odzyskiwania, należy podać wartości dla każdego chronionego komputera.
- Użyj tego samego głównego serwera docelowego do ponownego włączenia ochrony grupy replikacji. Jeśli używasz innego głównego serwera docelowego do ponownego włączenia ochrony grupy replikacji, serwer nie może zapewnić wspólnego punktu w czasie.
- Maszyna wirtualna w środowisku lokalnym jest wyłączona podczas ponownego włączania ochrony. Pomaga to zapewnić spójność danych podczas replikacji. Nie włączać maszyny wirtualnej po zakończeniu ponownego włączania ochrony.



## <a name="common-issues"></a>Typowe problemy

- Jeśli odnajdywanie vCenter użytkownika tylko do odczytu, a ochrona maszyn wirtualnych, ochrona zakończy się powodzeniem, a tryb failover działa prawidłowo. Podczas ponownego włączania ochrony trybu failover kończy się niepowodzeniem, ponieważ nie można odnaleźć magazynów danych. Objawem to, że podczas ponownego włączania ochrony nie ma na liście magazynów danych. Aby rozwiązać ten problem, należy zaktualizować poświadczenia vCenter przy użyciu odpowiedniego konta, które ma uprawnienia, a następnie ponów zadanie. 
- Podczas powrotu po awarii maszyny wirtualnej systemu Linux i uruchomić go lokalnie, zobaczysz, że pakietu Menedżera sieci został odinstalowany z komputera. Ta dezinstalacja występuje, ponieważ pakiet Menedżer sieci jest usuwany, gdy maszyna wirtualna jest odzyskiwana na platformie Azure.
- W przypadku maszyny wirtualnej systemu Linux jest skonfigurowana ze statycznym adresem IP i jest w trybie Failover na platformie Azure, adres IP jest uzyskiwany z serwera DHCP. Podczas przejścia w tryb failover do serwera lokalnego, maszyna wirtualna w dalszym ciągu używać protokołu DHCP w celu pobrania adresu IP. Ręcznie Zaloguj się do maszyny, a następnie ustaw adres statyczny adres, jeśli to konieczne. Maszynę wirtualną Windows można ponownie pobrać jego statyczny adres IP.
- Jeśli używasz wersji bezpłatnej ESXi 5.5 lub vSphere 6 Hypervisor bezpłatna wersja trybu failover zakończy się pomyślnie, ale podczas powrotu po awarii nie powiodła się. Aby włączyć powrót po awarii, uaktualnienie do licencji ewaluacyjnej albo program.
- Jeśli nie można nawiązać połączenia serwera konfiguracji z serwera przetwarzania, użyj Telnet, aby sprawdzić połączenie z serwerem konfiguracji na porcie 443. Możesz również spróbować wykonać polecenie ping do serwera konfiguracji z serwera przetwarzania. Serwer przetwarzania powinny mieć również pulsu, gdy jest podłączone do serwera konfiguracji.
- Serwer Windows Server 2008 R2 z dodatkiem SP1, który jest chroniony jako serwera fizycznego w środowisku lokalnym, nie może powrót po awarii z platformy Azure do lokacji lokalnej.
- Nie można przerwać w następujących okolicznościach:
    - Przeprowadzono migrację maszyn na platformę Azure. [Dowiedz się więcej](migrate-overview.md#what-do-we-mean-by-migration).
    - Maszyny Wirtualnej został przeniesiony do innej grupy zasobów.
    - Można usunąć maszyny Wirtualnej platformy Azure.
    - Możesz wyłączyć ochrony maszyny Wirtualnej.
    - Maszyna wirtualna została utworzona ręcznie na platformie Azure. Maszyny powinny zostały pierwotnie chroniony w środowisku lokalnym i przełączone w tryb failover Azure przed ponownego włączania ochrony.
    - Tylko na hoście ESXi może zakończyć się niepowodzeniem. Można wykonać powrót po awarii maszyn wirtualnych z programu VMware lub serwerów fizycznych na hostach funkcji Hyper-V, maszyn fizycznych lub stacji roboczych VMware.


## <a name="next-steps"></a>Kolejne kroki

Po wprowadzeniu chroniony stan maszyny wirtualnej możesz [zainicjować powrotu po awarii](vmware-azure-failback.md). Powrót po awarii zamykania maszyny wirtualnej na platformie Azure i wykonuje rozruch maszyny wirtualnej w środowisku lokalnym. Oczekiwać, że przestój dla aplikacji. Wybierz czas w przypadku powrotu po awarii, jeśli aplikacja może tolerować przestojów.


