---
title: Replikowanie maszyn wirtualnych platformy Azure Stack na platformie Azure przy użyciu usługi Azure Site Recovery | Dokumentacja firmy Microsoft
description: Dowiedz się, jak skonfigurować odzyskiwanie po awarii na platformie Azure maszyn wirtualnych platformy Azure Stack za pomocą usługi Azure Site Recovery.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.topic: conceptual
ms.service: site-recovery
ms.date: 05/30/2019
ms.author: raynew
ms.openlocfilehash: 11d409f904c43c0df4bbbd44fdb24531f2f989f6
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "66399595"
---
# <a name="replicate-azure-stack-vms-to-azure"></a>Replikowanie maszyn wirtualnych usługi Azure Stack na platformie Azure

W tym artykule pokazano, jak skonfigurować odzyskiwanie po awarii maszyn wirtualnych platformy Azure Stack na platformie Azure przy użyciu [usługi Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/site-recovery-overview).

Usługa Site Recovery przyczynia się do Twojej ciągłość działalności biznesowej i odzyskiwanie po awarii (BCDR) strategii odzyskiwania. Usługa zapewnia, że obciążeń maszyny Wirtualnej pozostaną dostępne, gdy oczekiwano i wystąpienia nieoczekiwanych awarii.

- Usługa Site Recovery organizuje replikację i zarządza maszyn wirtualnych do usługi Azure storage.
- W przypadku wystąpienia awarii w lokacji głównej, użyjesz Site Recovery do trybu failover na platformie Azure.
- W tryb failover maszyn wirtualnych platformy Azure są tworzone na podstawie przechowywanych danych maszyny Wirtualnej, a użytkownicy mogą nadal dostęp do obciążenia uruchomione na tych maszynach wirtualnych platformy Azure.
- Gdy wszystko jest uruchomione ponownie, możesz się nie powieść maszyn wirtualnych z powrotem do platformy Azure do lokacji głównej i rozpocząć replikowanie ich do usługi Azure storage ponownie.


W tym artykule omówiono sposób wykonywania następujących zadań:

> [!div class="checklist"]
> * **Krok 1. Przygotowywanie maszyn wirtualnych usługi Azure stack dla replikacji**. Upewnij się, że maszyny wirtualne spełniają wymogi Site Recovery i przygotowanie do instalacji usługi Site Recovery Mobility. Ta usługa jest zainstalowana na każdej maszynie Wirtualnej, którą chcesz replikować.
> * **Krok 2. Konfigurowanie magazynu usługi Recovery Services**. Konfigurowanie magazynu usługi Site Recovery i określ, co chcesz replikować. Składników usługi Site Recovery i działania są konfigurowane i zarządzane w magazynie.
> * **Krok 3. Konfigurowanie środowiska źródłowego replikacji**. Konfigurowanie serwera konfiguracji usługi Site Recovery. Serwer konfiguracji jest jednej usługi Azure Stack maszynę Wirtualną, która uruchamia wszystkie składniki, które są wymagane przez usługę Site Recovery. Po skonfigurowaniu serwera konfiguracji należy zarejestrować go w magazynie.
> * **Krok 4. Konfigurowanie środowiska docelowego replikacji**. Wybierz konta platformy Azure i konto usługi Azure storage i sieci, w której chcesz użyć. Podczas replikacji danych maszyny Wirtualnej jest kopiowana do usługi Azure storage. Po przejściu w tryb failover maszyn wirtualnych platformy Azure dołączonych do określonej sieci.
> * **Krok 5. Włącz replikację**. Konfigurowanie ustawień replikacji i włączanie replikacji maszyn wirtualnych. Usługa Mobility zostanie zainstalowana na maszynie Wirtualnej, po włączeniu replikacji. Usługa Site Recovery wykonuje replikacji początkowej maszyny wirtualnej, a następnie rozpoczyna trwającej replikacji.
> * **Krok 6: Uruchamianie próbnego odzyskiwania po awarii**: Po replikacji jest uruchomiona, czy tryb failover będą działać zgodnie z oczekiwaniami, uruchamiając testowania odzyskiwania po awarii. Aby zainicjować testowania odzyskiwania po awarii, należy uruchomić test trybu failover w usłudze Site Recovery. Testowanie trybu failover nie ma wpływu na środowisko produkcyjne.

Za pomocą tych kroków jest pełna następnie można uruchomić pełny przejścia w tryb failover na platformie Azure jako i konieczne.

## <a name="architecture"></a>Architektura

![Architektura](./media/azure-stack-site-recovery/architecture.png)

**Lokalizacja** | **Składnik** |**Szczegóły**
--- | --- | ---
**Serwer konfiguracji** | Działa na jednej maszyny Wirtualnej usługi Azure Stack. | W ramach każdej subskrypcji można skonfigurować serwer konfiguracji maszyny Wirtualnej. Ta maszyna wirtualna uruchamia się następujące składniki usługi Site Recovery:<br/><br/> — Konfiguracja serwera: Służy do koordynowania komunikacji między lokalną i platformą Azure oraz do zarządzania replikacją danych. -Process server: Działa jako brama replikacji. To odbiera dane replikacji, optymalizuje przy użyciu pamięci podręcznej, kompresji i szyfrowania; i wysyła je do usługi Azure storage.<br/><br/> Jeśli chcesz replikować maszyny wirtualne zostaną przekroczone podaną poniżej, możesz skonfigurować oddzielne autonomicznego serwera przetwarzania. [Dowiedz się więcej](https://docs.microsoft.com/azure/site-recovery/vmware-azure-set-up-process-server-scale).
**Usługa mobilności** | Zainstalowana na każdej maszynie Wirtualnej, którą chcesz replikować. | W krokach w tym artykule możemy przygotować konto tak, aby usługa mobilności jest zainstalowana automatycznie na maszynie Wirtualnej po włączeniu replikacji. Jeśli nie chcesz automatycznie zainstalować usługę, istnieje kilka innych metod, których można użyć. [Dowiedz się więcej](https://docs.microsoft.com/azure/site-recovery/vmware-azure-install-mobility-service).
**Azure** | Na platformie Azure konieczne jest magazyn usługi Recovery Services, konta magazynu i sieci wirtualnej. |  Replikowane dane są przechowywane na koncie magazynu. Maszyny wirtualne platformy Azure są dodawane do sieci platformy Azure, po przejściu do trybu failover. 


Replikacja działa w następujący sposób:

1. W magazynie należy określić źródło i cel replikacji, skonfigurować serwer konfiguracji, utworzyć zasady replikacji i Włącz replikację.
2. Usługa mobilności jest zainstalowana na komputerze (jeśli używano instalację wypychaną) i maszyny rozpocząć replikacji zgodnie z zasadami replikacji.
3. Tworzy początkową kopię danych serwera są replikowane do usługi Azure storage.
4. Po zakończeniu replikacji początkowej rozpoczyna się replikacja zmian różnicowych do platformy Azure. Śledzone zmiany dla maszyny są przechowywane w pliku hrl.
5. Serwer konfiguracji synchronizuje replikację z platformą Azure (port wychodzący 443 protokołu HTTPS).
6. Serwer przetwarzania odbiera dane z maszyn źródłowych, optymalizuje je szyfruje i wysyła je do usługi Azure storage (port wyjściowy 443).
7. Replikowane maszyny komunikują się z serwerem konfiguracji (portu HTTPS 443 dla ruchu przychodzącego, na potrzeby zarządzania replikacją. Maszyny wysyłają dane replikacji do serwera przetwarzania (port HTTPS 9443 dla ruchu przychodzącego — można modyfikować).
8. Ruch jest replikowany do publicznych punktów końcowych usługi Azure Storage za pośrednictwem Internetu. Alternatywnie możesz użyć usługi Azure ExpressRoute publicznej komunikacji równorzędnej. Replikowanie ruchu za pośrednictwem sieci VPN typu lokacja-lokacja z lokacji lokalnej platformy Azure nie jest obsługiwane.

## <a name="prerequisites"></a>Wymagania wstępne

Oto, co jest potrzebne do skonfigurowania tego scenariusza.

**Wymaganie** | **Szczegóły**
--- | ---
**Konto subskrypcji platformy Azure** | Jeśli nie masz subskrypcji platformy Azure, utwórz [bezpłatne konto](https://azure.microsoft.com/pricing/free-trial/).
**Uprawnienia konta platformy Azure** | Konto platformy Azure, którego używasz wymaga uprawnień do:<br/><br/> — Utwórz magazyn usługi Recovery Services<br/><br/> — Tworzenie maszyny wirtualnej w grupie zasobów i sieć wirtualną, używanych w przypadku scenariusza<br/><br/> -Zapisu do konta magazynu, które określisz<br/><br/> Należy pamiętać o następujących kwestiach:<br/><br/> — Jeśli utworzysz konto, jesteś administratorem subskrypcji i mogą wykonywać wszystkie akcje.<br/><br/> — Jeśli używasz istniejącej subskrypcji i nie jesteś administratorem, musisz skontaktować się z administratorem w celu uprawnień właściciela lub współautora.<br/><br/> — Jeśli potrzebujesz bardziej szczegółowych uprawnień, zapoznaj się z [w tym artykule](https://docs.microsoft.com/azure/site-recovery/site-recovery-role-based-linked-access-control). 
**Usługa Azure Stack maszyny Wirtualnej** | Potrzebujesz maszyny Wirtualnej platformy Azure Stack, w ramach subskrypcji dzierżawy, który zostanie wdrożony jako serwera konfiguracji usługi Site Recovery. 


### <a name="prerequisites-for-the-configuration-server"></a>Wymagania wstępne dotyczące serwera konfiguracji

[!INCLUDE [site-recovery-config-server-reqs-physical](../../includes/site-recovery-config-server-reqs-physical.md)]


 
## <a name="step-1-prepare-azure-stack-vms"></a>Krok 1: Przygotowywanie maszyn wirtualnych usługi Azure Stack

### <a name="verify-the-operating-system"></a>Sprawdź system operacyjny

Upewnij się, że maszyny wirtualne są uruchomiony jeden z systemów operacyjnych, podsumowane w tabeli.


**System operacyjny** | **Szczegóły**
--- | ---
**Windows 64-bitowych** | Windows Server 2016, Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 R2 (z dodatkiem SP1)
**CentOS** | 5.2 do 5.11, 6.1-6.9, 7.0, 7.3
**Ubuntu** | 14.04 server LTS, 16.04 LTS server. Przegląd [obsługiwane jądra](https://docs.microsoft.com/azure/site-recovery/vmware-physical-azure-support-matrix#ubuntu-kernel-versions)

### <a name="prepare-for-mobility-service-installation"></a>Przygotowanie do instalacji usługi mobilności

Każda maszyna wirtualna, którą chcesz replikować musi mieć zainstalowaną usługę mobilności. Aby serwer przetwarzania, aby zainstalować usługę na maszynie Wirtualnej po włączeniu replikacji Sprawdź ustawienia maszyny Wirtualnej.

#### <a name="windows-machines"></a>Windows maszyny

- Możesz potrzebne połączenie sieciowe między maszyny Wirtualnej, na którym chcesz włączyć replikację, a komputer z programem serwer przetwarzania (domyślnie jest to serwer konfiguracji maszyny Wirtualnej).
- Potrzebujesz konta z prawami administratora (domeny lub lokalnego) na komputerze, dla którego należy włączyć replikację.
    - To konto można określić podczas konfigurowania Site Recovery. Następnie serwer przetwarzania używa tego konta, aby zainstalować usługę mobilności, po włączeniu replikacji.
    - To konto będzie używane przez usługę Site Recovery tylko, dotyczące instalacji wypychanej i zaktualizować usługę mobilności.
    - Jeśli nie używasz konta domeny, należy wyłączyć kontrolę dostępu użytkowników zdalnych na maszynie Wirtualnej:
        - W rejestrze Utwórz wartość DWORD **LocalAccountTokenFilterPolicy** w obszarze HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System.
        - Ustaw wartość na 1.
        - W tym celu należy w wierszu polecenia wpisz następujące polecenie: **Rejestru HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System Dodaj /v LocalAccountTokenFilterPolicy /t REG_DWORD /d 1**.
- W Zaporze Windows na maszynie Wirtualnej, którą chcesz replikować Zezwalaj na udostępnianie plików i drukarek oraz usługi WMI.
    - Aby to zrobić, uruchom **wf.msc** aby otworzyć konsolę Zapora Windows. Kliknij prawym przyciskiem myszy **reguły ruchu przychodzącego** > **nową regułę**. Wybierz **wstępnie zdefiniowane**i wybierz polecenie **plików i drukarek udostępnianie** z listy. Ukończ pracę kreatora, zaznacz, aby umożliwić połączenie > **Zakończ**.
    - W przypadku komputerów domeny można użyć obiektu zasad grupy, w tym celu.

    
#### <a name="linux-machines"></a>Maszyny z systemem Linux

- Upewnij się, że nawiązano połączenie sieciowe między komputerem z systemem Linux a serwerem przetwarzania.
- Na komputerze, dla którego należy włączyć replikację potrzebujesz konta, które jest użytkownikiem głównym na serwerze źródłowym w systemie Linux:
    - To konto można określić podczas konfigurowania Site Recovery. Następnie serwer przetwarzania używa tego konta, aby zainstalować usługę mobilności, po włączeniu replikacji.
    - To konto będzie używane przez usługę Site Recovery tylko, dotyczące instalacji wypychanej i zaktualizować usługę mobilności.
- Sprawdź, czy plik /etc/hosts na źródłowym serwerze z systemem Linux zawiera wpisy mapujące lokalną nazwę hosta na adres IP skojarzony ze wszystkimi kartami sieciowymi.
- Zainstaluj najnowsze pakiety openssh, openssh-server i openssl na komputerze, który chcesz replikować.
- Upewnij się, że protokół Secure Shell (SSH) jest włączony i uruchomiony na porcie 22.
- Włącz podsystem SFTP i uwierzytelnianie hasłem w pliku sshd_config:
    1. Aby to zrobić, zaloguj się jako użytkownik główny.
    2. Znajdź wiersz, który rozpoczyna się od **PasswordAuthentication**, w pliku /etc/ssh/sshd_config. Usuń znaczniki komentarza dla wiersza i zmień wartość na **yes**.
    3. Znajdź wiersz zaczynający się od ciągu **Subsystem** i usuń znaczniki komentarza.

        ![Usługa mobilności w systemie Linux](./media/azure-stack-site-recovery/linux-mobility.png)

    4. Uruchom ponownie usługę sshd.


### <a name="note-the-vm-private-ip-address"></a>Należy pamiętać, prywatny adres IP maszyny Wirtualnej

Dla każdej maszyny, którą chcesz replikować, znaleźć adres IP:

1. W portalu usługi Azure Stack kliknij maszynę Wirtualną.
2. Na **zasobów** menu, kliknij przycisk **interfejsów sieciowych**.
3. Zanotuj prywatnego adresu IP.

    ![Prywatny adres IP](./media/azure-stack-site-recovery/private-ip.png)


## <a name="step-2-create-a-vault-and-select-a-replication-goal"></a>Krok 2: Utwórz magazyn i Wybieranie celu replikacji

1. W witrynie Azure portal wybierz **Utwórz zasób** > **narzędzia do zarządzania** > **Backup i Site Recovery**.
2. W polu **Nazwa** wprowadź przyjazną nazwę identyfikującą magazyn. 
3. W **grupy zasobów**, Utwórz lub wybierz grupę zasobów. Firma Microsoft korzysta z **contosoRG**.
4. W **lokalizacji**, wprowadź region platformy Azure. Użyj wartości **Europa Zachodnia**.
5. Aby szybko uzyskać dostęp do magazynu z poziomu pulpitu nawigacyjnego, wybierz pozycję **Przypnij do pulpitu nawigacyjnego** > **Utwórz**.

   ![Tworzenie nowego magazynu](./media/azure-stack-site-recovery/new-vault-settings.png)

   Nowy magazyn będzie wyświetlany w sekcji **Pulpit nawigacyjny** > **Wszystkie zasoby** oraz na stronie głównej **Magazyny usług Recovery Services**.

### <a name="select-a-replication-goal"></a>Wybieranie celu replikacji

1. W **Magazyny usługi Recovery Services** > określ nazwy magazynu. Firma Microsoft korzysta z **ContosoVMVault**.
2. W obszarze **Wprowadzenie** wybierz pozycję Site Recovery. Następnie wybierz pozycję **Przygotowywanie infrastruktury**.
3. W obszarze **Cel ochrony** > **Gdzie znajdują się maszyny** wybierz pozycję **Lokalne**.
4. W obszarze **Gdzie chcesz zreplikować maszyny** wybierz pozycję **Na platformę Azure**.
5. W **są maszynach zwirtualizowanych**, wybierz opcję **bez wirtualizacji/inne**. Następnie wybierz przycisk **OK**.

    ![Cel ochrony](./media/azure-stack-site-recovery/protection-goal.png)

## <a name="step-3-set-up-the-source-environment"></a>Krok 3: Konfigurowanie środowiska źródłowego

Skonfigurowanie komputera serwera konfiguracji, zarejestruj go w magazynie i odnajdowanie maszyn, które mają być replikowane.

1. Kliknij pozycję **Przygotowanie infrastruktury** > **Źródło**.
2. W obszarze **Przygotowywanie źródła** kliknij pozycję **+ Serwer konfiguracji**.

    ![Konfiguracja źródła](./media/azure-stack-site-recovery/plus-config-srv.png)

3. W **Dodaj serwer**, sprawdź, czy **serwera konfiguracji** pojawia się w **typ serwera**.
5. Pobierz plik instalacyjny Site Recovery Unified Setup.
6. Pobierz klucz rejestracji magazynu. Po uruchomieniu ujednoliconego Instalatora, potrzebujesz klucza rejestracji. Klucz jest ważny przez pięć dni po jego wygenerowaniu.

    ![Konfiguracja źródła](./media/azure-stack-site-recovery/set-source2.png)


### <a name="run-azure-site-recovery-unified-setup"></a>Uruchom usługę Azure Site Recovery ujednoliconej konfiguracji

Aby zainstalować i zarejestrować serwer konfiguracji, czy z połączeniem RDP z maszyną wirtualną, którego chcesz użyć dla serwera konfiguracji i uruchom ujednoliconego Instalatora.

Przed rozpoczęciem upewnij się, że zegar jest [zsynchronizowane z serwerem czasu](https://technet.microsoft.com/windows-server-docs/identity/ad-ds/get-started/windows-time-service/windows-time-service) na maszynie Wirtualnej przed rozpoczęciem. Instalacja kończy się niepowodzeniem, jeśli czas jest więcej niż pięć minut od czasu lokalnego.

Teraz można zainstalować na serwerze konfiguracji:

[!INCLUDE [site-recovery-add-configuration-server](../../includes/site-recovery-add-configuration-server.md)]

> [!NOTE]
> Serwer konfiguracji można również zainstalować z poziomu wiersza polecenia. [Dowiedz się więcej](physical-manage-configuration-server.md#install-from-the-command-line).
> 
> Zanim nazwa konta pojawi się w portalu, może minąć 15 minut lub więcej. Aby wykonać natychmiastową aktualizację, wybierz pozycje **Serwery konfiguracji** > ***nazwa serwera*** > **Odśwież serwer**.

## <a name="step-4-set-up-the-target-environment"></a>Krok 4: Konfigurowanie środowiska docelowego

Wybierz i zweryfikuj zasoby docelowe.

1. W **przygotowanie infrastruktury** > **docelowej**, wybierz subskrypcję platformy Azure, której chcesz użyć.
2. Określ docelowy model wdrażania.
3. Usługa Site Recovery sprawdza, czy masz co najmniej jedno zgodne konto magazynu Azure i co najmniej jedną sieć platformy Azure. Jeśli ich nie zostanie znaleziona, należy utworzyć co najmniej jedno konto magazynu i sieć wirtualną, aby zakończyć pracę kreatora.


## <a name="step-5-enable-replication"></a>Krok 5. Włączanie replikacji

### <a name="create-a-replication-policy"></a>Tworzenie zasad replikacji

1. Kliknij przycisk **przygotowanie infrastruktury** > **ustawień replikacji**.
2. W obszarze **Tworzenie zasad replikacji** określ nazwę zasad.
3. W obszarze **Wartość progowa celu punktu odzyskiwania** określ limit celu punktu odzyskiwania.
    - Punkty odzyskiwania dla replikowanych danych są tworzone zgodnie z ustawionym czasie.
    - To ustawienie nie wpływa na replikację, która jest ciągły. Generuje po prostu alert, jeśli osiągnięto limit progu bez tworzenia punktu odzyskiwania.
4. W **czas przechowywania punktu odzyskiwania**, określ, jak długo każdy punkt odzyskiwania jest przechowywana. Replikowane maszyny wirtualne można odzyskać do dowolnego punktu w określonym przedziale czasu.
5. W **częstotliwość migawek spójności aplikacji**, określ, jak często tworzone są migawki spójne z aplikacjami.

    - Migawka spójności aplikacji jest w momencie migawkę danych aplikacji wewnątrz maszyny Wirtualnej.
    - Usługa kopiowania woluminów w tle (VSS) zapewnia, że aplikacje, na maszynie Wirtualnej są w spójnym stanie, gdy wykonywana jest migawka.
6. Wybierz pozycję **OK**, aby utworzyć zasady.


### <a name="confirm-deployment-planning"></a>Potwierdzanie planowania wdrożenia

Teraz można pominąć ten krok. W **planowania wdrożenia** listy rozwijanej kliknij **tak, ma to zostało zrobione**.



### <a name="enable-replication"></a>Włączanie replikacji

Upewnij się, że ukończono wszystkie zadania w [krok 1: Przygotuj maszynę](#step-1-prepare-azure-stack-vms). Następnie Włącz replikację w następujący sposób:

1. Wybierz pozycje **Replikowanie aplikacji** > **Źródło**.
2. W obszarze **Źródło** wybierz serwer konfiguracji.
3. W **typ maszyny**, wybierz opcję **maszyn fizycznych**.
4. Wybierz serwer przetwarzania (serwer konfiguracji). Następnie kliknij przycisk **OK**.
5. W **docelowej**, wybierz subskrypcję i grupę zasobów, w której chcesz utworzyć maszyny wirtualne po włączeniu trybu failover. Wybierz model wdrażania, który chcesz użyć dla maszyn wirtualnych w trybie failed-over.
6. Wybierz na koncie magazynu platformy Azure, w którym chcesz do przechowywania replikowanych danych.
7. Wybierz sieć platformy Azure i podsieć, z którą nawiążą połączenie maszyny wirtualne platformy Azure, gdy zostaną uruchomione po przejściu do trybu failover.
8. Wybierz opcję **Konfiguruj teraz dla wybranych maszyn**, aby zastosować ustawienia sieci do wszystkich maszyn wybranych do ochrony. Wybierz **później skonfigurować** Jeśli chcesz wybrać sieć platformy Azure oddzielnie dla każdego komputera.
9. W **maszyn fizycznych**, kliknij przycisk **+ maszyna fizyczna**. Określ nazwę, adres IP i typ systemu operacyjnego w każdej maszynie, którą chcesz replikować.

    - Użyj wewnętrznego adresu IP maszyny.
    - Jeśli określisz publiczny adres IP, replikacja może nie działać zgodnie z oczekiwaniami.

10. W **właściwości** > **skonfigurować właściwości**, wybierz konto, którego serwer przetwarzania będzie używać do automatycznego instalowania usługi mobilności na maszynie.
11. W **ustawień replikacji** > **Konfigurowanie ustawień replikacji**, sprawdź, czy wybrano właściwe zasady replikacji.
12. Kliknij pozycję **Włącz replikację**.
13. Śledź postęp **włączania ochrony** zadanie **ustawienia** > **zadania** > **zadania usługi Site Recovery**. Po uruchomieniu zadania **Sfinalizuj ochronę** maszyna jest gotowa do przejścia w tryb failover.

> [!NOTE]
> Po włączeniu replikacji maszyny wirtualnej usługa Site Recovery instaluje usługę mobilności.
> 
> Zastosowanie zmian i wyświetlenie ich w portalu może potrwać 15 minut lub dłużej.
> 
> Aby monitorować dodawane maszyny wirtualne, możesz sprawdzić czas ostatniego odnalezienia maszyn wirtualnych w obszarze **Serwery konfiguracji** > **Ostatni kontakt**. Aby dodać maszyny wirtualne, nie czekając na zaplanowane odnajdywanie, wyróżnij serwer konfiguracji (nie wybieraj go), a następnie wybierz pozycję **Odśwież**.


## <a name="step-6-run-a-disaster-recovery-drill"></a>Krok 6: Uruchamianie próbnego odzyskiwania

Możesz uruchomić testowy tryb failover na platformie Azure, aby upewnić się, że wszystko działa zgodnie z oczekiwaniami. Ta praca awaryjna nie wpłyną na środowisko produkcyjne.

### <a name="verify-machine-properties"></a>Sprawdź właściwości maszyny

Przed uruchomieniem testu trybu failover Sprawdź właściwości maszyny i upewnij się, że spełniają one [wymagania dotyczące usługi Azure](https://docs.microsoft.com/azure/site-recovery/vmware-physical-azure-support-matrix#azure-vm-requirements). Można wyświetlać i modyfikować właściwości w następujący sposób:

1. W obszarze **Chronione elementy** kliknij kolejno pozycje **Zreplikowane elementy** > Maszyna wirtualna.
2. Okienko **Zreplikowany element** zawiera podsumowanie informacji na temat maszyny wirtualnej, jej kondycję oraz najnowsze dostępne punkty odzyskiwania. Kliknij przycisk **Właściwości**, aby wyświetlić więcej szczegółów.
3. W **obliczenia i sieć**, zmodyfikuj ustawienia zgodnie z potrzebami.

    - Można zmodyfikować nazwę maszyny Wirtualnej platformy Azure, grupę zasobów, rozmiar docelowy, [zestaw dostępności](../virtual-machines/windows/tutorial-availability-sets.md)i ustawienia dysku zarządzanego.
    - Można również wyświetlać i modyfikować ustawienia sieciowe. Obejmują one sieć/podsieć, do której maszyna wirtualna platformy Azure jest dołączony po pracy awaryjnej i adres IP, który zostanie przypisany do maszyny Wirtualnej.
1. W **dysków**, wyświetlić informacje dotyczące systemu operacyjnego i dysków z danymi na maszynie Wirtualnej.
   

### <a name="run-a-test-failover"></a>Wykonywanie próby przejścia w tryb failover

Po uruchomieniu próby przejścia w tryb failover wykonywane są następujące operacje:

1. Uruchamiane jest sprawdzanie wymagań wstępnych, aby upewnić się, że zostały spełnione wszystkie warunki przejścia w tryb failover.
2. Tryb failover przetwarza dane przy użyciu określony punkt odzyskiwania:
    - **Najnowszy przetworzony**: Maszyny wirtualnej za pośrednictwem do najnowszego punktu odzyskiwania przetworzonego przez usługę Site Recovery. Wyświetlana jest sygnatura czasowa. Ta opcja zapewnia niską wartość celu czasu odzyskiwania (RTO, Recovery Time Objective), ponieważ nie trzeba poświęcać czasu na przetwarzanie danych.
    - **Najnowszy spójny na poziomie aplikacji**: Maszyny wirtualnej za pośrednictwem do najnowszego punktu odzyskiwania spójnego na poziomie aplikacji.
    - **Niestandardowy**: Wybierz punkt odzyskiwania do trybu failover.

3. Maszynę wirtualną platformy Azure zostanie utworzona przetworzone dane.
4. Testowanie trybu failover można wyczyścić automatycznie utworzone podczas testowania odzyskiwania po awarii maszyn wirtualnych platformy Azure.

Uruchom testowanie trybu failover dla maszyny Wirtualnej w następujący sposób:

1. W obszarze **Ustawienia** > **Elementy zreplikowane** kliknij pozycje Maszyna wirtualna > **+Testowe przełączenie w tryb failover**.
2. W tym przewodniku wybierzemy użyj **najnowszy przetworzony** punktu odzyskiwania. 
3. W **testowy tryb Failover**, wybierz docelową sieć platformy Azure.
4. Kliknij przycisk **OK**, aby rozpocząć tryb failover.
5. Śledź postęp, klikając maszynę Wirtualną i otwierając jej właściwości. Ewentualnie kliknij przycisk **testowy tryb Failover** zadanie *nazwa magazynu* > **ustawienia** > **zadania**  > **Zadania usługi site Recovery**.
6. Po zakończeniu trybu failover w obszarze Azure Portal > **Maszyny wirtualne** będzie widoczna replika maszyny wirtualnej platformy Azure. Sprawdź, czy maszyna wirtualna ma prawidłowy rozmiar, łączność z odpowiednią siecią i działa.
7. Powinno być teraz możliwe nawiązanie połączenia ze zreplikowaną maszyną wirtualną na platformie Azure. [Dowiedz się więcej](https://docs.microsoft.com/azure/site-recovery/site-recovery-test-failover-to-azure#prepare-to-connect-to-azure-vms-after-failover).
8. Aby usunąć maszyny wirtualne platformy Azure utworzone podczas testowego przełączania w tryb failover, kliknij pozycję **Wyczyść test pracy w trybie failover** na maszynie wirtualnej. W **uwagi**, zapisz wszelkie obserwacje związane z testowym trybem failover.

## <a name="fail-over-and-fail-back"></a>Przechodzenie do trybu failover i powrót po awarii

Po skonfigurowaniu replikacji i uruchamianie próbnego, aby upewnić się, że wszystko działa, możesz przełączyć maszyn tryb failover na platformie Azure zgodnie z potrzebami.

Przed uruchomieniem trybu failover, jeśli chcesz połączyć się z maszyny na platformie Azure po przejściu w tryb failover następnie [przygotować się do połączenia](https://docs.microsoft.com/azure/site-recovery/site-recovery-test-failover-to-azure#prepare-to-connect-to-azure-vms-after-failover) przed rozpoczęciem.

Następnie uruchom tryb failover w następujący sposób:


1. W **ustawienia** > **zreplikowane elementy**, kliknij maszynę > **trybu Failover**.
2. Wybierz punkt odzyskiwania, którego chcesz używać.
3. W **testowy tryb Failover**, wybierz docelową sieć platformy Azure.
4. Wybierz pozycję **Zamknij maszynę przed rozpoczęciem pracy w trybie failover**. To ustawienie, Usługa Site Recovery próbuje zamknąć maszyny źródłowej, przed rozpoczęciem pracy w trybie failover. Jednak trybu failover będzie kontynuowane, nawet jeśli zamknięcie nie powiedzie się. 
5. Kliknij przycisk **OK**, aby rozpocząć tryb failover. Na stronie **Zadania** można śledzić postęp trybu failover.
6. Po zakończeniu trybu failover w obszarze Azure Portal > **Maszyny wirtualne** będzie widoczna replika maszyny wirtualnej platformy Azure. Jeśli przeprowadzono przygotowanie połączyć po włączeniu trybu failover, sprawdź, czy maszyna wirtualna ma prawidłowy rozmiar, połączona z odpowiednią siecią i uruchamianie.
7. Po zweryfikowaniu maszyny Wirtualnej, kliknij przycisk **zatwierdzić** na zakończenie pracy w trybie failover. Spowoduje to usunięcie wszystkich dostępnych punktów odzyskiwania.

> [!WARNING]
> Nie Anuluj trybu failover w toku: Przed rozpoczęciem pracy w trybie failover zatrzymywana jest replikacja maszyny wirtualnej. Jeśli anulujesz tryb failover po rozpoczęciu przełączania, zostanie ono zatrzymane, ale maszyna wirtualna nie zostanie ponownie zreplikowana.


### <a name="fail-back-to-azure-stack"></a>Powrót po awarii do usługi Azure Stack

Lokacja główna będzie ponownie uruchomiony, możesz może zakończyć się niepowodzeniem ponownie z platformy Azure do platformy Azure Stack. Aby to zrobić, należy pobrać wirtualnego dysku twardego dla maszyny Wirtualnej platformy Azure i przekaż go do usługi Azure Stack.

1. Zamknij maszyny Wirtualnej platformy Azure, więc, że wirtualny dysk twardy, który można pobrać. 
2. Aby rozpocząć pobieranie wirtualnego dysku twardego, należy zainstalować [Eksploratora usługi Azure Storage](https://azure.microsoft.com/features/storage-explorer/).
3. Przejdź do maszyny Wirtualnej w witrynie Azure Portal (przy użyciu nazwy maszyny Wirtualnej).
4. W **dysków**, a następnie kliknij nazwę dysku i Zbierz ustawienia.

    - Na przykład identyfikator URI dysku VHD używane w naszym teście: https://502055westcentralus.blob.core.windows.net/wahv9b8d2ceb284fb59287/copied-3676553984.vhd można podzielić na Pobierz następujące dane wejściowe parametry, które są używane do pobierania wirtualnego dysku twardego.
        - Konto magazynu: 502055westcentralus
        - Kontener: wahv9b8d2ceb284fb59287
        - Nazwa wirtualnego dysku twardego: 3676553984.vhd skopiowane

5. Teraz Użyj Eksploratora usługi Azure Storage, aby pobrać wirtualnego dysku twardego.
6. Przekazywanie wirtualnego dysku twardego do usługi Azure Stack przy użyciu [te kroki](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-manage-vm-disks#use-powershell-to-add-multiple-unmanaged-disks-to-a-vm).
7. W istniejącej maszyny Wirtualnej lub nowej maszyny Wirtualnej, dołączyć przekazanych dysków VHD.
8. Sprawdź, czy dysk systemu operacyjnego jest poprawna, a następnie uruchom maszynę Wirtualną.


Na tym etapie zakończeniu powrotu po awarii.


## <a name="conclusion"></a>Podsumowanie

W tym artykule będziemy replikowane maszyny wirtualne platformy Azure Stack na platformie Azure. Przy użyciu replikacji w miejscu uruchomiliśmy próbnego odzyskiwania po awarii, aby upewnić się, że tryb failover na platformie Azure zadziałała zgodnie z oczekiwaniami. Artykuł zawiera także kroki dotyczące uruchamiania pełnego przejścia w tryb failover na platformie Azure i powrót do usługi Azure Stack.

## <a name="next-steps"></a>Kolejne kroki

Po nieudanym Wstecz, można ponownie włączyć ochronę maszyny Wirtualnej i rozpocząć replikowanie na platformie Azure ponownie, aby to zrobić, powtórz kroki opisane w tym artykule.

