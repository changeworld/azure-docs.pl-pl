---
title: Włącz ponownie ochronę maszyn wirtualnych z platformy Azure do lokacji lokalnej | Dokumentacja firmy Microsoft
description: Po przełączeniu maszyn wirtualnych na platformie Azure można zainicjować powrotu po awarii, aby przywrócić maszyn wirtualnych do lokalnego. Dowiedz się, jak i włącz ponownie ochronę przed powrotu po awarii.
services: site-recovery
author: rajani-janaki-ram
manager: gauravd
ms.service: site-recovery
ms.topic: article
ms.date: 03/05/2018
ms.author: rajanaki
ms.openlocfilehash: 0946d5234292cfb69a7e9b5bc7846e6acf94dff4
ms.sourcegitcommit: c52123364e2ba086722bc860f2972642115316ef
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/11/2018
---
# <a name="reprotect-machines-from-azure-to-an-on-premises-site"></a>Włącz ponownie ochronę maszyny z platformy Azure do lokacji lokalnej

Po [pracy awaryjnej](site-recovery-failover.md) lokalnych maszyn wirtualnych VMware lub serwerów fizycznych do platformy Azure, pierwszym krokiem w przypadku której wystąpi błąd do lokacji sieci lokalnej jest Włącz ponownie ochronę maszyn wirtualnych platformy Azure, utworzony podczas pracy awaryjnej. W tym artykule opisano, jak to zrobić. 

Aby szybko zapoznać Obejrzyj film następujący temat do trybu failover z platformy Azure do lokacji lokalnej.
> [!VIDEO https://channel9.msdn.com/Series/Azure-Site-Recovery/VMware-to-Azure-with-ASR-Video5-Failback-from-Azure-to-On-premises/player]


## <a name="before-you-start"></a>Przed rozpoczęciem

Jeśli szablon jest używany do tworzenia maszyn wirtualnych, upewnij się, że każda maszyna wirtualna ma własny identyfikator UUID dysków. Identyfikator UUID na lokalnej maszynie wirtualnej powoduje konflikt z z głównego celu, ponieważ zarówno utworzone przy użyciu tego samego szablonu, ponowne włączenie ochrony kończy się niepowodzeniem. Wdróż innego główny cel, który nie został utworzony z tego samego szablonu.
- Jeśli próbujesz powrót po awarii do alternatywnego vCenter, upewnij się, że Twoje nowe vCenter i główny serwer docelowy są wykrywane. Typowe objaw jest, że datastores nie są dostępne lub widoczne w **Wznów** okno dialogowe.
- Aby zreplikować powrót do lokalnej, konieczne będzie zasady powrotu po awarii. Te zasady automatycznie tworzone podczas tworzenia zasady przodu. Należy pamiętać, że:
    - Ta zasada pobiera automatycznie skojarzone z serwerem konfiguracji podczas tworzenia.
    - Ta zasada nie jest możliwa.
    - Wartości zestaw zasad są (RPO próg = 15 minutach, przechowywania punktu odzyskiwania = 24 godziny i częstotliwość migawek spójności aplikacji = 60 min)
- Podczas zastosowania i powrotu po awarii lokalnego serwera konfiguracji powinna być uruchomiona i połączona.
- Jeśli serwera vCenter zarządza maszynami wirtualnymi, do której będzie powrócić upewnij się, czy masz [wymagane uprawnienia](vmware-azure-tutorial-prepare-on-premises.md#prepare-an-account-for-automatic-discovery) odnajdywania maszyn wirtualnych na serwer vCenter.
- Usuń migawki na głównym serwerze docelowym, zanim zostanie Włącz ponownie ochronę. Migawki są obecne na główny cel na lokalnym lub maszynie wirtualnej, ponowne włączenie ochrony kończy się niepowodzeniem. Migawki maszyny wirtualnej są scalane automatycznie podczas wykonywania zadania ponownej ochrony.
- Wszystkie maszyny wirtualne grupy replikacji powinny mieć tego samego typu systemu operacyjnego (wszystkie z systemem Windows lub Linux wszystkie). Grupy replikacji z mieszane systemy operacyjne nie jest obecnie obsługiwane w ponownej ochrony i powrotu po awarii do środowiska lokalnego. Jest to spowodowane główny cel powinna mieć ten sam system operacyjny, jako maszynę wirtualną i wszystkich maszyn wirtualnych do grupy replikacji powinny mieć tego samego głównego celu. 
- Serwer konfiguracji jest wymagana lokalnymi przechodzenia wstecz. Podczas powrotu po awarii maszyna wirtualna musi istnieć w bazie danych konfiguracji serwera. W przeciwnym razie powrotu po awarii zakończy się niepowodzeniem. Upewnij się, wykonanie zaplanowanego tworzenia kopii zapasowych serwera konfiguracji. W przypadku awarii, należy przywrócić działanie serwera przy użyciu tego samego adresu IP, dzięki czemu działa w przypadku powrotu po awarii.
- Ponowne włączenie ochrony i powrotu po awarii wymaga VPN lokacja lokacja, do replikacji danych. Podaj sieci, dzięki czemu maszyny wirtualne przełączona w tryb failover na platformie Azure ma dostęp (ping) konfiguracji lokalnego serwera. Można także wdrożyć serwera przetwarzania w sieci platformy Azure przełączona w tryb failover maszyny wirtualnej. Ten serwer przetwarzania również będą już mogli nawiązać połączenia z lokalnego serwera konfiguracji.
- Upewnij się, że należy otworzyć następujące porty dla trybu failover i powrotu po awarii.

    ![Porty dla trybu failover i powrotu po awarii](./media/vmware-azure-reprotect/failover-failback.png)

- Możesz przeczytać wszystkie wymagania wstępne na porty i listę dozwolonych adresów URL podobnej [tutaj](vmware-azure-deploy-configuration-server.md#prerequisites)

## <a name="deploy-a-process-server-in-azure"></a>Wdrażanie serwera przetwarzania na platformie Azure

Serwer przetwarzania na platformie Azure może być konieczne, zanim nie zostanie ponownie do lokacji lokalnej:
- Serwer przetwarzania] odbiera dane z chronioną maszynę wirtualną na platformie Azure i wysyła dane do lokacji lokalnej.
- Sieciach z małym opóźnieniami jest wymagany między serwerem przetwarzania i chronionej maszyny wirtualnej. Ogólnie rzecz biorąc należy wziąć pod uwagę czas oczekiwania przy podejmowaniu decyzji, czy potrzebujesz serwer przetwarzania na platformie Azure:
    - Jeśli masz Konfigurowanie połączenia ExpressRoute, można użyć serwera przetwarzania lokalnego można wysłać danych, ponieważ brakuje opóźnienia między maszyną wirtualną a serwerem przetwarzania.
    - Jednak jeśli VPN S2S, zalecamy wdrożenie serwera przetwarzania na platformie Azure.
    - Zalecamy używanie serwera opartego na platformie Azure podczas powrotu po awarii. Wydajność replikacji jest większe, jeśli serwer przetwarzania jest bliżej replikacji maszyny wirtualnej (maszyna przełączona w tryb failover na platformie Azure). Dla elementu koncepcji można użyć lokalnego serwera przetwarzania wraz z usługi ExpressRoute z prywatnej komunikacji równorzędnej.

Wdrożyć w następujący sposób:

1. Jeśli musisz wdrożyć serwer przetwarzania na platformie Azure, wykonaj [tych instrukcji](vmware-azure-set-up-process-server-azure.md)
2. Maszyny wirtualne platformy Azure będzie wysyłać dane replikacji na serwer przetwarzania. Skonfigurowanie sieci, dzięki czemu maszyny wirtualne Azure mogą uzyskać dostęp do serwera procesu.
3. Należy pamiętać, że replikacja z platformy Azure do środowiska lokalnego może się zdarzyć, tylko na połączenia VPN S2S lub prywatnej komunikacji równorzędnej ExpressRoute sieci. Upewnij się, że wystarczającą przepustowość jest dostępny za pośrednictwem tego kanału sieci.

## <a name="deploy-a-separate-master-target-server"></a>Wdrażanie oddzielne głównego serwera docelowego

Główny serwer docelowy odbiera dane podczas powrotu po awarii. Domyślnie główny serwer docelowy jest uruchamiany na lokalnym serwerze konfiguracji. Jednak w zależności od wielkości ruchu Wstecz nie powiodło się, konieczne może być tworzenie oddzielnych głównego serwera docelowego do powrotu po awarii. Poniżej przedstawiono sposób go utworzyć:

    * [Utwórz serwer główny cel Linux](vmware-azure-install-linux-master-target.md) powrotu po awarii maszyn wirtualnych systemu Linux. Jest to wymagane.
    * Opcjonalnie możesz utworzyć oddzielne głównego serwera docelowego do powrotu po awarii maszyny Wirtualnej systemu Windows. Aby to zrobić, uruchom ponownie Instalatora Unified i wybrać opcję utworzenia głównego serwera docelowego. [Dowiedz się więcej](physical-azure-set-up-source.md#run-azure-site-recovery-unified-setup).

Po utworzeniu głównego serwera docelowego, wykonaj następujące czynności:

- Jeśli maszyna wirtualna jest obecny lokalnie na serwerze vCenter, główny serwer docelowy wymaga dostępu do pliku VMDK na lokalnej maszynie wirtualnej. Wymagany jest dostęp do zapisu dysków maszyny wirtualnej replikowane dane. Upewnij się, że magazyn danych na lokalnej maszynie wirtualnej jest zainstalowany na hoście główny cel z dostępem do odczytu/zapisu.
- Jeśli maszyna wirtualna nie jest obecny lokalnie na serwerze vCenter, Usługa Site Recovery potrzebne do utworzenia nowej maszyny wirtualnej podczas zastosowania. Ta maszyna wirtualna jest tworzony na hoście ESX, na którym utworzyć głównego celu. Wybierz hosta ESX dokładnie, dzięki czemu powrotu po awarii maszyny wirtualnej jest tworzona na hoście, który ma.
- Nie można użyć narzędzia Storage vMotion dla serwera głównego celu *. Może to spowodować powrót po awarii zakończyć się niepowodzeniem. Nie można uruchomić maszyny wirtualnej, ponieważ dyski nie są dostępne. Aby zapobiec temu problemowi, należy wykluczyć z listy vMotion serwery docelowe głównej.
- Jeśli główny cel ulega zadań vMotion magazynu po przełączonej, dysków chronionych maszyn wirtualnych, które są dołączone do głównego celu migracji w miejscu docelowym zadania vMotion. Jeśli spróbujesz się niepowodzeniem po tym, oderwania dysku nie powiedzie się, ponieważ nie znaleziono dysków. Następnie staje się on trudne do znalezienia dyski kont magazynu. Należy je znaleźć ręcznie i dołącz je do maszyny wirtualnej. Następnie można uruchomić na lokalnej maszynie wirtualnej.
- Dodaj dysk przechowywania do istniejącego serwera głównego celu systemu Windows. Dodaj nowy dysk i sformatuj dysk. Dysk przechowywania służy do zatrzymania punkty w czasie, gdy maszyny wirtualnej są replikowane do lokacji lokalnej. Poniżej przedstawiono niektóre kryteria dysk przechowywania. Bez tych kryteriów napędu nie są wyświetlane na głównym serwerze docelowym.
    - Wolumin nie jest używany do innych celów, takich jak element docelowy replikacji.
    - Wolumin nie jest w trybie blokady.
    - Wolumin nie jest woluminem pamięci podręcznej. Główny cel nie należy zainstalować na tym woluminie. Wolumin instalacji niestandardowej dla docelowego serwera i głównego procesu nie jest uprawniony do woluminu przechowywania. Gdy serwer przetwarzania i główny serwer docelowy są zainstalowane na woluminie, wolumin jest woluminu pamięci podręcznej głównego elementu docelowego.
    - Typ systemu plików woluminu nie jest FAT lub FAT32.
    - Pojemność woluminu jest różna od zera.
    - Wolumin przechowywania domyślne dla systemu Windows jest R.
    - Wolumin przechowywania domyślne dla systemu Linux jest /mnt/retention.
- Należy dodać nowy dysk, jeśli używasz istniejącej maszyny serwera konfiguracji serwera/procesu lub skali lub proces serwera/główną maszynę docelową, na serwerze. Nowy dysk powinna spełniać wymagania poprzedniej. Jeśli nie ma dysk przechowywania, nie zostanie wyświetlone na liście rozwijanej wyboru w portalu. Po dodaniu dysku do głównego celu lokalnymi potrzebny do 15 minut dysku pojawią się w zaznaczenia w portalu. Jeśli dysk nie jest wyświetlany po 15 minutach, można również odświeżyć serwera konfiguracji.
- Zainstaluj narzędzia VMware lub open-vm-tools na głównym serwerze docelowym. Bez narzędzia nie można wykryć datastores na hoście ESXi głównego celu.
- Ustaw `disk.EnableUUID=true` ustawienie parametrów konfiguracji maszyny wirtualnej główny serwer docelowy w środowisku programu VMware. Jeśli ten wiersz nie istnieje, dodaj ją. To ustawienie jest wymagane do zapewnia spójny UUID dysku maszyny wirtualnej (VMDK), dzięki czemu jego instaluje poprawnie.
- Host ESX, na której została utworzona główny cel powinien mieć co najmniej jeden VMFS magazynu danych do niego dołączony. Jeśli brak, **Datastore** dane wejściowe na stronie ponownej ochrony jest pusta, i nie może kontynuować.
- Główny serwer docelowy nie może mieć migawek na dyskach. W przypadku migawki nie przełączonej i powrotu po awarii.
- Główny cel nie może mieć zdefiniowany kontroler Paravirtual SCSI. Kontroler może być tylko kontrolera LSI Logic. Bez kontrolera LSI Logic przełączonej kończy się niepowodzeniem.
- W danym przypadku główny cel może mieć atmst dyski 60 dołączone do niego. Jeśli liczba maszyn wirtualnych jest przełączona do trybu do głównego celu lokalnymi sumy całkowitej liczby więcej niż 60 dysków, a następnie reprotects do głównego celu rozpocznie się niepowodzeniem. Upewnij się, że masz wystarczająco dużo głównego dysku miejsc docelowych lub wdrożyć dodatkowe głównych serwerów docelowych.
    

## <a name="enable-reprotection"></a>Włącz przełączonej

Po uruchomieniu maszyny wirtualnej na platformie Azure, dopiero po pewnym czasie dla agenta zarejestrować serwer konfiguracji (maksymalnie 15 minut). W tym czasie nie będzie można włączyć ją ponownie, a komunikat o błędzie wskazuje, że agent nie jest zainstalowany. W takim przypadku poczekaj kilka minut, a następnie spróbuj ponownie zastosowania.


1. W **magazynu** > **elementy replikowane**, kliknij prawym przyciskiem myszy maszynę wirtualną, która jest Failover, a następnie wybierz **ponownego włączenia ochrony**. Możesz również kliknij maszynę i wybierz **ponownego włączenia ochrony** z przyciski poleceń.
2. Sprawdź kierunek ochrony, **Azure do środowiska lokalnego**, jest już wybrana.
3. W **główny serwer docelowy** i **serwera przetwarzania**, wybierz lokalny główny serwer docelowy i serwera przetwarzania.  
4. Aby uzyskać **Datastore**, wybierz magazyn danych, do której chcesz odzyskać dyski lokalną. Ta opcja jest używana, gdy na lokalnej maszynie wirtualnej zostanie usunięta, a następnie musisz utworzyć nowe dyski. Ta opcja jest ignorowana, jeśli dyski już istnieje, ale nadal należy określić wartość.
5. Wybierz dysk przechowywania.
6. Zasady powrotu po awarii są wybierane automatycznie.
7. Kliknij przycisk **OK**, aby rozpocząć ponowne włączanie ochrony. Zadanie zaczyna replikować maszynę wirtualną z platformy Azure do lokacji lokalnej. Na karcie **Zadania** można śledzić postęp. Po przełączonej zakończy się powodzeniem, maszyny wirtualnej przechodzą w stan chronionych.

Należy pamiętać, że:
- Jeśli chcesz odzyskać do lokalizacji alternatywnej (po usunięciu na lokalnej maszynie wirtualnej), wybierz dysk przechowywania i magazynu danych skonfigurowanego dla głównego serwera docelowego. Gdy użytkownik powrót po awarii do lokacji lokalnej, w przypadku powrotu po awarii plan ochrony maszyn wirtualnych VMware Użyj tego samego magazynu danych jako główny serwer docelowy. W programie vCenter zostanie utworzona nowa maszyna wirtualna.
- Jeśli chcesz odzyskać maszynę wirtualną na platformie Azure do istniejących na lokalnej maszynie wirtualnej Zainstaluj datastores na lokalnej maszynie wirtualnej z dostępem do odczytu/zapisu na hoście ESXi serwera głównego celu.
    ![Okno dialogowe ponownego włączenia ochrony](./media/vmware-azure-reprotect/reprotectinputs.png)

- Można również Obejmij ochroną na poziomie planu odzyskiwania. Grupa replikacji może przełączona do trybu tylko do planu odzyskiwania. Gdy użytkownik zacznij ponownie chronić przy użyciu planu odzyskiwania, musisz podać wartości dla każdego chronionego komputera.
- Użyj tego samego głównego serwera docelowego do ponownego włączenia ochrony grupy replikacji. Użycie innego głównego serwera docelowego do ponownego włączenia ochrony grupy replikacji spowoduje, że serwer nie będzie mógł zapewnić wspólnego punktu w czasie.
- Na lokalnej maszynie wirtualnej jest wyłączony podczas zastosowania. Pomaga to zapewnić spójność danych podczas replikacji. Nie należy włączać maszyny wirtualnej, po zakończeniu zastosowania.



## <a name="common-issues"></a>Typowe problemy


- Obecnie usługa Site Recovery obsługuje awaria, powrót tylko do systemu plików maszyny wirtualnej (VMFS) lub sieci vSAN magazynu danych. Magazyn danych systemu plików NFS nie jest obsługiwane. Z powodu tego ograniczenia wybór magazynu danych wejściowych na ponownej ochrony ekranu jest puste w przypadku datastores systemu plików NFS lub pokazuje vSAN magazynu danych, ale nie powiedzie się podczas wykonywania zadania. Jeśli zamierzasz wykonaj powrót po awarii, można tworzyć VMFS magazyn danych lokalnych i powrót po awarii do jej. Pełnego pobierania VMDK spowoduje, że ta powrotu po awarii.
- Jeśli odnajdywanie vCenter użytkownika tylko do odczytu i ochrony maszyn wirtualnych, ochrona zakończy się pomyślnie i tryb failover działa prawidłowo. Podczas zastosowania trybu failover nie powiedzie się, ponieważ nie można odnaleźć datastores. Objawem jest, że datastores nie są wyświetlane podczas zastosowania. Aby rozwiązać ten problem, można zaktualizować poświadczeń vCenter przy użyciu odpowiedniego konta, które ma uprawnienia i spróbuj ponownie wykonać zadanie. 
- Po wykonaj powrót po awarii maszyny wirtualnej systemu Linux i uruchomić ją lokalnie, można zobaczyć, że pakiet Menedżera sieci został odinstalowany z komputera. Ta dezinstalacja odbywa się, ponieważ pakiet Menedżera sieci jest usuwany po odzyskaniu maszyna wirtualna na platformie Azure.
- Maszyny wirtualnej systemu Linux jest skonfigurowane za pomocą statycznego adresu IP i przeszła w tryb failover na platformie Azure, adres IP są uzyskiwane z serwera DHCP. Gdy przełączyć się na lokalnym maszyny wirtualnej w dalszym ciągu używać protokołu DHCP w celu pobrania adresu IP. Ręcznie Zaloguj się do komputera, a ustawienie adresu IP do statycznego adresu w razie potrzeby. Maszyny wirtualnej systemu Windows można ponownie uzyskać jego statyczny adres IP.
- Jeśli używany jest bezpłatna wersja ESXi 5.5 lub bezpłatna wersja funkcji Hypervisor vSphere 6, trybu failover zakończy się pomyślnie, ale powrotu po awarii nie powiedzie się. Aby umożliwić powrót po awarii, uaktualnienie do licencji ewaluacyjnej albo program.
- Jeśli nie można osiągnąć serwera konfiguracji z serwera przetwarzania, należy użyć Telnet, aby sprawdzić połączenie z serwerem konfiguracji na porcie 443. Można też spróbować wykonać polecenie ping z serwera przetwarzania serwera konfiguracji. Serwer przetwarzania powinny mieć również pulsu, gdy jest podłączone do serwera konfiguracji.
- Serwer systemu Windows Server 2008 R2 z dodatkiem SP1, który jest chroniony jako serwer fizyczny lokalnymi nie nieudane platformy Azure z lokacjami lokalnymi.
- Nie można przerwać w następujących okolicznościach:
    - Zostały zmigrowane maszyny do platformy Azure. [Dowiedz się więcej](migrate-overview.md#what-do-we-mean-by-migration).
    - Maszynę wirtualną można przenieść do innej grupy zasobów.
    - Można usunąć maszyny Wirtualnej platformy Azure.
    - Można wyłączyć ochrony maszyny wirtualnej.
    - Maszyna wirtualna została utworzona ręcznie na platformie Azure. Komputer powinien zostały początkowo chronionych lokalnymi i Failover do platformy Azure przed ponownej ochrony.
    - Możesz tylko w trybie do hosta ESXi. Nie można wykonać powrotu po awarii maszyn wirtualnych VMware lub serwerów fizycznych na hostach Hyper-V, maszyn fizycznych lub stacji roboczych VMware.


## <a name="next-steps"></a>Kolejne kroki

Po wprowadzeniu chronionych stan maszyny wirtualnej można [zainicjować powrotu po awarii](vmware-azure-failback.md). Powrót po awarii Zamknij maszynę wirtualną na platformie Azure, a następnie uruchomić na lokalnej maszynie wirtualnej. Oczekiwane przestój dla aplikacji. Wybierz czas powrotu po awarii, gdy aplikacja może tolerować przestoju.

