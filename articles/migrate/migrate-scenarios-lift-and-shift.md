---
title: Migrację obciążeń lokalnych do platformy Azure i Azure DMS Site Recovery | Dokumentacja firmy Microsoft
description: Dowiedz się, jak przygotować Azure do migracji maszyn lokalnych przy użyciu usługi Azure migracji bazy danych i usługi Azure Site Recovery.
services: site-recovery
author: rayne-wiselman
ms.service: site-recovery
ms.topic: conceptual
ms.date: 04/21/2018
ms.author: raynew
ms.openlocfilehash: d6fc1af3c5a587ab62b86dcd4189165d96e6e3bc
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2018
---
# <a name="scenario-2-lift-and-shift-migration-to-azure"></a>Scenariusz 2: Przyrostu i shift migracji na platformie Azure

Firma Contoso rozważa migracji na platformie Azure. Aby wypróbować tę możliwość, chce ocenić, a następnie przeprowadzić migrację aplikacji podróży małych lokalnymi na platformie Azure. Jest to aplikacja dwuwarstwowa podróży z aplikacją sieci web działających na jedną maszynę Wirtualną i bazy danych programu SQL Server na Maszynie wirtualnej drugi. Aplikacja jest wdrożona w oprogramowaniu VMware, a środowisko jest zarządzane za pomocą programu vCenter Server. 

W [scenariusz 1: oceny migracji Azure](migrate-scenarios-assessment.md), dane Asystenta migracji (DMA) są używane do oceny bazy danych programu SQL Server dla aplikacji. Ponadto usługa Azure migracji one używane do oceny aplikacji maszyn wirtualnych. Teraz w tym scenariuszu po pomyślnym ukończeniu oceny, ich chcesz przeprowadzić migrację bazy danych do wystąpienia zarządzane SQL Azure przy użyciu usługi migracji bazy danych Azure (DMS) i maszyny lokalnymi maszynami wirtualnymi Azure przy użyciu usługi Azure Site Recovery.

Jeśli chcesz wypróbować [scenariusz 1](migrate-scenarios-assessment.md), a następnie ten scenariusz przy użyciu tej aplikacji przykładowy podróży, można go pobrać z [github](https://github.com/Microsoft/SmartHotel360).



**Usługa** | **Opis** | **Koszty**
--- | --- | ---
[Usługa zarządzania bazy danych](https://docs.microsoft.com/azure/dms/dms-overview) | Usługa DMS umożliwia bezproblemowe migracji z wielu źródeł bazy danych do platformy Azure danych, z minimalnym czasem przestojów. | Usługa jest obecnie w publicznej wersji zapoznawczej (kwiecień 2018) i może służyć bezpłatne bezpłatnie w wersji zapoznawczej.<br/><br/> Należy pamiętać, że z publicznej wersji zapoznawczej DMS jest ograniczona do [liczba regionów](https://docs.microsoft.com/azure/dms/dms-overview).
[Wystąpienia zarządzane SQL Azure](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance) | Wystąpienie zarządzanych jest zarządzana usługa bazy danych, który reprezentuje w pełni zarządzane wystąpienia programu SQL Server w chmurze Azure. Dzieli ten sam kod z najnowszą wersję aparatu bazy danych programu SQL Server, a ma najnowszych funkcji, wydajności i poprawki zabezpieczeń. | Za pomocą wystąpień zarządzane bazy danych SQL Azure działa na platformie Azure wiąże się z opłatami oparte na wydajność. [Dowiedz się więcej](https://azure.microsoft.com/pricing/details/sql-database/managed/). 
[Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/) | Usługa organizuje zarządza migracji i odzyskiwania po awarii dla maszyn wirtualnych platformy Azure i lokalnymi maszyn wirtualnych i serwerów fizycznych.  | Podczas replikacji do platformy Azure Azure Storage jest obciążany.  Maszyny wirtualne platformy Azure są tworzone i spowodować naliczenie opłat, podczas pracy awaryjnej. [Dowiedz się więcej](https://azure.microsoft.com/pricing/details/site-recovery/) dotyczące opłat i cenach.

W tym scenariuszu skonfigurujemy Twoje VPN lokacja lokacja, aby usługa DMS można nawiązać połączenia z lokalną bazą danych, utworzyć wystąpienia zarządzanego SQL Azure na platformie Azure i migrację bazy danych. Site Recovery firma Microsoft będzie przygotować środowisko VMware lokalnymi, replikacji i migracji maszyn wirtualnych na platformie Azure.  


> [!IMPORTANT]
> Muszą być zarejestrowane w zarządzane wystąpienia ograniczone publicznej wersji zapoznawczej usługi SQL. Potrzebujesz subskrypcji platformy Azure w celu [Zarejestruj](https://portal.azure.com#create/Microsoft.SQLManagedInstance). Tworzenia konta może zająć kilka dni, więc upewnij się, że można to zrobić przed rozpoczęciem wdrażania tego scenariusza.

## <a name="architecture"></a>Architektura

### <a name="site-recovery"></a>Site Recovery

![Site Recovery](media/migrate-scenarios-lift-and-shift/asr-architecture.png)  

### <a name="dms"></a>DMS
![DMS](media/migrate-scenarios-lift-and-shift/dms-architecture.png)  

W tym scenariuszu:

- Firma Contoso jest nazwą fikcyjnych reprezentujący organizacji typowego przedsiębiorstwa. Firma Contoso chce ocenić, a następnie przeprowadzić migrację aplikacji podróży dwuwarstwowa lokalnymi.
- Firma Contoso ma lokalnego datacenter (contoso-centrum danych), z lokalnym kontrolerem domeny (**contosodc1**).
- Na dwóch maszyn wirtualnych, WEBVM i SQLVM do warstwy aplikacji wewnętrznych podróży i znajduje się na hoście VMware ESXi (**contosohost1.contoso.com**).
- Środowisko VMware jest zarządzane przez program vCenter Server (**vcenter.contoso.com**) uruchomiony na maszynie wirtualnej.

## <a name="prerequisites"></a>Wymagania wstępne

Jeśli chcesz uruchomić ten scenariusz, w tym miejscu jest powinny mieć.

Wymagania | Szczegóły
--- | ---
**Subskrypcja platformy Azure** | Jeśli nie masz subskrypcji platformy Azure, utwórz [bezpłatne konto](https://azure.microsoft.com/pricing/free-trial/).<br/><br/> Jeśli bezpłatne konto właśnie zostało utworzone, jesteś administratorem subskrypcji i możesz wykonywać wszystkie akcje.<br/><br/> Jeśli używasz istniejącej subskrypcji i nie jesteś administratorem, należy do pracy z administratora przypisanie uprawnienia właściciela lub współautora.<br/><br/> Aby uzyskać bardziej szczegółowe uprawnienia, przejrzyj [w tym artykule](../site-recovery/site-recovery-role-based-linked-access-control.md). 
**Usługa Site recovery (lokalnego)** | Lokalny serwer vCenter systemem w wersji 5.5, 6.0 lub 6.5<br/><br/> Hosta ESXi z systemem w wersji 5.5, 6.0 lub 6.5<br/><br/> Jeden lub więcej maszyn wirtualnych VMware uruchomiona na hoście ESXi.<br/><br/> Maszyny wirtualne muszą spełniać [wymagania dotyczące usługi Azure](https://docs.microsoft.com/azure/site-recovery/vmware-physical-azure-support-matrix#azure-vm-requirements).<br/><br/> Obsługiwane [sieci](https://docs.microsoft.com/azure/site-recovery/vmware-physical-azure-support-matrix#network) i [magazynu](https://docs.microsoft.com/azure/site-recovery/vmware-physical-azure-support-matrix#storage) konfiguracji.
**USŁUGA DMS** | Usługa DMS mają być [urządzenia sieci VPN zgodne lokalnymi](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpn-devices).<br/><br/> Musi być możliwość konfigurowania lokalnego urządzenia sieci VPN. Musi mieć publiczny adres IPv4 połączonej zewnętrznie, a adres nie może znajdować się za urządzeniem NAT.<br/><br/> Upewnij się, że masz dostęp do lokalną bazą danych programu SQL Server.<br/><br/> Zapora systemu Windows powinna mieć możliwość dostępu aparatu bazy danych źródła. [Dowiedz się więcej](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access).<br/><br/> W przypadku zapory przed komputera bazy danych, Dodaj reguły, aby zezwolić na dostęp do bazy danych i do plików za pośrednictwem portu SMB 445.<br/><br/> Poświadczenia używane do łączenia się ze źródłem programu SQL Server i zarządzane wystąpienia docelowego musi być członkiem roli serwera sysadmin.<br/><br/> Potrzebujesz sieci udostępnionej w programie z lokalną bazą danych, którym DMS umożliwia utworzenie kopii zapasowej źródłowej bazy danych.<br/><br/> Upewnij się, że działa wystąpienie serwera SQL źródła konto usługi ma uprawnienia zapisu w udziale sieciowym.<br/><br/> Zwróć uwagę użytkownika systemu Windows (i hasło), który ma uprawnienia pełnej kontroli w udziale sieciowym. Usługa Azure bazy danych migracji personifikuje te poświadczenia użytkownika, aby przekazać pliki kopii zapasowej do kontenera magazynu systemu Azure.<br/><br/> Proces instalacji programu SQL Server Express ustawia protokołu TCP/IP na **wyłączone** domyślnie. Upewnij się, że jest włączone.


# <a name="scenario-overview"></a>Omówienie scenariusza

Oto, co chcemy zrobić:

> [!div class="checklist"]
> * **Krok 1: Konfigurowanie połączenia sieci VPN typu lokacja lokacja**: DMS łączy się z lokalną bazą danych przez połączenie VPN lokacja lokacja. Możemy utworzyć sieć wirtualną dla połączenia sieci VPN i nowsze, możemy ponownie użyć tej sieci usługi Site Recovery. Utworzone sieci muszą znajdować się w regionie, w którym można utworzyć magazyn usług odzyskiwania.
> * **Krok 2: Skonfiguruj wystąpienie serwera SQL Azure zarządzanych**: należy wstępnie utworzone wystąpienie zarządzanych, do której będą migrowane lokalną bazą danych programu SQL Server.
> * **Krok 3: Konfigurowanie SA identyfikator URI DMS**: sygnatury dostępu współdzielonego (SAS) identyfikator URI (Uniform Resource) umożliwiają dostęp delegowany do zasobów na koncie magazynu, dzięki czemu można przyznać dostęp jest ograniczony do magazynu obiektów. Identyfikator URI sygnatury dostępu Współdzielonego należy skonfigurować tak, aby usługa DMS mogą uzyskiwać dostęp do kontenera konta magazynu, do którego usługa przekazuje pliki kopii zapasowych programu SQL Server.
> * **Krok 4: Przygotowanie DMS**: Zarejestruj dostawcę migracja bazy danych lub utworzyć wystąpienia, a następnie utwórz projekt DMS.
> * **Krok 5: Przygotowanie usługi Azure Site Recovery**: Tworzenie konta magazynu Azure do przechowywania replikowanych danych.
> * **Krok 6: Przygotowanie VMware lokalnymi Site Recovery**: przygotowanie kont dla maszyny Wirtualnej odnajdywanie i zainstalować agenta i przygotowanie do połączenia z maszynami wirtualnymi Azure po pracy awaryjnej. 
> * **Krok 7: Replikowania maszyn wirtualnych**: Konfigurowanie środowiska źródłowego i docelowego odzyskiwania lokacji, skonfiguruj zasady replikacji i rozpoczęcia replikacji maszyn wirtualnych z magazynem platformy Azure.
> * **Krok 8: Migrowanie bazy danych o DMS**: Uruchom migrację bazy danych.
> * **Krok 9: Migrowanie maszyn wirtualnych z usługą Site Recovery**: tryb failover do migracji maszyn wirtualnych na platformie Azure.


## <a name="step-1-set-up-site-to-site-vpn-connection"></a>Krok 1: Konfigurowanie połączenia sieci VPN lokacja lokacja

Aby przygotować się do połączenia z siecią VPN lokacja lokacja, skonfiguruj sieć wirtualną i podsieci, Utwórz bramę sieci VPN dla sieci wirtualnej i skonfiguruj bramę sieci lokalnej, że Azure potrafi nawiązać połączenia z lokalną sieć VPN. Utwórz i sprawdzić połączenie lokacja lokacja.

### <a name="set-up-a-virtual-network"></a>Konfigurowanie sieci wirtualnej

Tworzenie sieci wirtualnej platformy Azure, aby zapewnić DMS połączenie lokacja lokacja do lokalnego serwera SQL.


1. W [portalu Azure](https://portal.azure.com), kliknij przycisk **Utwórz zasób**. Wybierz **sieci** > **sieci wirtualnej**.
2. W **sieci wirtualnej** > **wybierz model wdrożenia**, wybierz pozycję **Resource Manager** > **tworzenie**
3. W **Utwórz sieć wirtualną** > **nazwa**, wprowadź nazwę unikatową sieci. W naszym scenariuszu **ContosoVNET**.
4. W **przestrzeni adresów**, zakres adresów IP. Zakres nie może nakładać się z sieci lokalnej przestrzeni adresowej.
5. W **grupy zasobów**, Utwórz nową grupę, lub wybierz istniejący. W tym scenariuszu firma Microsoft korzysta z **ContosoRG**.
6. W **lokalizacji**, wybierz region, w którym można utworzyć sieci wirtualnej. Firma Microsoft korzysta z **wschodnie stany USA 2**.
7. W **podsieci**, Dodaj nazwę pierwszej podsieci i zakres adresów. Utworzyliśmy **aplikacji** podsieci.
8. Wyłączanie punktów końcowych usługi.

    ![Tworzenie sieci wirtualnej](media/migrate-scenarios-lift-and-shift/vnet-create-network.png)

9. Wybierz **Przypnij do pulpitu nawigacyjnego** tak, aby łatwiej znaleźć sieci w przyszłości, a następnie kliknij przycisk **Utwórz**.
10. Utworzenie sieci wirtualnej zajmuje kilka sekund. Po utworzeniu, należy ją zweryfikować w pulpicie nawigacyjnym portalu Azure.
11. Upewnij się, że te porty komunikacyjne są dozwolone w Twojej sieci wirtualnej: 443, 53, 9354, 445, 12000.


### <a name="set-up-subnets"></a>Konfigurowanie podsieci

Firma Microsoft będzie miał cztery podsieci w naszym sieć platformy Azure:

![Lista podsieci](media/migrate-scenarios-lift-and-shift/vnet-subnet-list.png)

Pierwszej podsieci (aplikacje) można skonfigurować podczas tworzenia sieci. Teraz utworzymy reszty podsieci. Podsieć bramy jest używany przez połączenie bramy sieci VPN platformy Azure do wysyłania ruchu przez połączenie VPN do lokacji lokalnej.

1. W sieci wirtualnej w obszarze **ustawienia**, kliknij przycisk **podsieci** > **+ podsieci**.
2. Skonfiguruj podsieci **Datamigration**, to zakres adresów, a następnie kliknij polecenie **OK**.

    ![Podsieci danych](media/migrate-scenarios-lift-and-shift/vnet-subnet-data.png)  


3. W **+ podsieci**, skonfiguruj podsieć **tożsamości**, to zakres adresów, a następnie kliknij polecenie **OK**.
    ![Tożsamość podsieci](media/migrate-scenarios-lift-and-shift/vnet-subnet-identity.png)

4. W **podsieci**, kliknij przycisk **podsieci bramy**, a następnie kliknij przycisk **OK**.
    - Ta podsieć zawiera adresy IP bramy sieci VPN będzie używany dla połączeń sieci VPN.
    - Nazwa tej podsieci jest automatycznie zestawu, tak że Azure można go rozpoznać.
    - Dostosuj zakres adresów automatycznie wypełnianej odpowiadające podsieci lokalnej. 

    ![Podsieć bramy](media/migrate-scenarios-lift-and-shift/vnet-subnet-gateway.png)
    
### <a name="set-up-a-virtual-network-gateway"></a>Konfigurowanie bramy sieci wirtualnej

1. Na stronie portalu po lewej kliknij pozycję **+** i wpisz tekst „brama sieci wirtualnej” w polu wyszukiwania. W **wyniki**, kliknij przycisk **Brama sieci wirtualnej**.
2. U dołu strony „Brama sieci wirtualnej” kliknij pozycję **Utwórz**.
3. W **Utwórz bramę sieci wirtualnej** >  **nazwa**, określ nazwę dla obiektu bramy.
4. W **typu bramy**, wybierz pozycję **VPN**.
5. W **typ sieci VPN**, wybierz pozycję **opartej na trasach**.
6. W **SKU**, wybierz z listy rozwijanej Jednostka SKU bramy. Jednostki SKU wymienione na liście rozwijanej zależą od wybranego typu sieci VPN. Nie włączaj trybie aktywny aktywny. [Dowiedz się więcej](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpn-gateway-settings#gwsku) o jednostki SKU.
7. W **sieci wirtualnej** > **wybierz sieć wirtualną**, Dodaj sieć został utworzony. Brama zostanie dodany do tej sieci.
8. W **konfiguracji pierwszy adres IP**, określ publiczny adres IP.  Brama sieci VPN musi mieć publiczny adres IP. Ten adres jest przypisywane dynamicznie do zasobu, podczas tworzenia bramy sieci VPN. Obecnie obsługiwane są tylko dynamiczne adresy, ale nie powoduje zmiany adresu IP, po przypisaniu.
    - Kliknij przycisk **konfiguracji IP bramy Utwórz**. W **wybierz publiczny adres IP**, kliknij przycisk **+ Utwórz nowy**.
    - W **tworzenie publicznego adresu IP**, określ nazwę publicznego adresu IP (Contoso-Gateway). Pozostaw SKU jako **podstawowe**i kliknij przycisk **OK** Aby zapisać zmiany.
        
    ![Brama sieci wirtualnej](media/migrate-scenarios-lift-and-shift/vnet-create-vpn-gateway2.png) 

9. Kliknij przycisk **Utwórz**, aby rozpocząć tworzenie bramy sieci VPN. Ustawienia zostaną sprawdzone, a "Wdrażanie bramy sieci wirtualnej" są wyświetlane na pulpicie nawigacyjnym.

    ![Sprawdzanie poprawności bramy sieci wirtualnej](media/migrate-scenarios-lift-and-shift/vnet-vpn-gateway-validate.png)
    
Tworzenie bramy może potrwać do 45 minut. Konieczne może być odświeżyć stronę portalu, aby wyświetlić stan zakończenia.

### <a name="set-up-a-local-network-gateway"></a>Skonfiguruj bramę sieci lokalnej

Konfigurowanie bramy sieci lokalnej na platformie Azure, do reprezentowania lokacji lokalnej.

1. W portalu kliknij pozycję **+Utwórz zasób**.
2. W polu wyszukiwania wpisz wartość **Brama sieci lokalnej**, a następnie naciśnij klawisz **Enter**, aby wyszukać. W wynikach, kliknij przycisk **bramy sieci lokalnej** > **Utwórz**.
3. W **Utwórz bramę sieci lokalnej** > **nazwa**, określ nazwę, która korzysta z platformy Azure dla obiekt bramy sieci lokalnej.
4. **Adres IP**, określ publiczny adres IP lokalnego urządzenia sieci VPN, z którą połączy się Azure. Adres IP nie powinien być NAT'ed i Azure musi być w stanie uzyskać do niej dostęp.
5. W **przestrzeni adresów**, typu w sieci lokalnej zakresów adresów, które zostaną przesłane za pośrednictwem bramy sieci VPN do urządzenia lokalnego. Upewnij się, że nie nakłada się na z zakresów w sieci wirtualnej platformy Azure.
6. **Skonfiguruj ustawienia protokołu BGP** nie jest odpowiedni dla tego scenariusza.
7. W **subskrypcji**, sprawdź, czy subskrypcja jest prawidłowa.
8. W **grupy zasobów**: Wybierz grupę zasobów, używany do tworzenia sieci (ContosoRG).
9. W **lokalizacji**, wybierz region, w której utworzono sieci wirtualnej.

    ![Bramy lokalnej](media/migrate-scenarios-lift-and-shift/vnet-create-local-gateway.png)

4. Kliknij przycisk **Utwórz** można utworzyć bramy lokalnej.

### <a name="configure-your-on-premises-vpn-device"></a>Konfigurowanie lokalnego urządzenia sieci VPN

Oto, co jest potrzebne do konfigurowania lokalnego urządzenia sieci VPN:

- Publiczny adres IPv4 bramy sieci wirtualnej platformy Azure, który został właśnie utworzony.
- Klucz wstępny (klucz używany podczas tworzenia połączenia z siecią VPN lokacja lokacja).  

Aby pomóc Ci skonfigurować urządzenie sieci VPN lokalnych:

- W zależności od lokalnego urządzenia sieci VPN istnieje można pobrać skryptu konfiguracji urządzenia sieci VPN. [Dowiedz się więcej](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-download-vpndevicescript).
- Przegląd [więcej przydatnych zasobów](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal#VPNDevice).

### <a name="create-the-vpn-connection"></a>Tworzenie połączenia sieci VPN

Teraz można utworzyć połączenia sieci VPN lokacja lokacja między bramą sieci wirtualnej i lokalnego urządzenia sieci VPN.

1. Otwórz stronę sieci wirtualnej w **sieci wirtualnych** > **omówienie** > **urządzeń podłączonych**. 
2. Kliknij przycisk **połączeń** > **+ Dodaj**.
3. W **Dodaj połączenie** > **nazwa**, określ nazwę połączenia.
4. W **typ połączenia**, wybierz pozycję **lokacja lokacja (IPSec)**.
5. **Brama sieci wirtualnej** wartość jest ustalona, ponieważ nawiązywane z tej bramy.
6. W **bramy sieci lokalnej**, określ utworzono bramę sieci lokalnej.
7. W **klucz wstępny**, należy określić klucz zgodna z wartością używaną na potrzeby lokalnego lokalnego urządzenia sieci VPN. W przykładzie użyto wartości „abc123”, ale można (i należy) użyć bardziej złożonej wartości. Pamiętaj o tym, że wartość podana w tym miejscu musi być taka sama, jak wartość podana podczas konfigurowania urządzenia sieci VPN.
8. Wartości **subskrypcji**, **grupy zasobów**, i **lokalizacji** zostały usunięte.

    ![Połączenie VPN](media/migrate-scenarios-lift-and-shift/vpn-connection.png) 

4. Kliknij przycisk **OK** do utworzenia połączenia. Zobaczysz "Tworzenia połączenia" flash na ekranie.
5. Po utworzeniu połączenia wyświetlania na **połączeń** strony bramy sieci wirtualnej. Stan zmieni się z nieznanej > Łączenie > zakończyło się pomyślnie.

### <a name="verify-the-vpn-connection"></a>Sprawdzenie połączenia sieci VPN

Przechodząc w witrynie Azure Portal do połączenia, można sprawdzić stan połączenia bramy VPN Gateway usługi Resource Manager. 

1. Kliknij przycisk **wszystkie zasoby**, a następnie przejdź do bramy sieci wirtualnej.
2. Na stronie bramy sieci wirtualnej, kliknij przycisk **połączenia**. Zostanie pokazany stan każdego połączenia.
3. Kliknij nazwę połączenia i wyświetlić więcej informacji w **Essentials**. Stan to "Powodzenie" i "Połączony" po pomyślnym połączeniu.

Teraz sprawdzić, czy można połączyć się z maszyną Wirtualną serwera SQL, za pośrednictwem połączenia VPN. Użyj połączenia pulpitu zdalnego i łączyć się z adresem IP maszyny Wirtualnej.



## <a name="step-2-prepare-an-azure-sql-managed-instance"></a>Krok 2: Przygotowanie zarządzanego wystąpienia Azure SQL

### <a name="set-up-a-virtual-network"></a>Konfigurowanie sieci wirtualnej

W tym scenariuszu należy utworzyć innej sieci wirtualnej, mający na celu wystąpienia zarządzane SQL Azure.  Należy uwzględnić następujące wymagania:

- Można utworzyć wystąpienia zarządzanego potrzebne dedykowane podsieci.
- Podsieć musi być pusta i nie zawierają inne usługi w chmurze i nie może być podsieć bramy. Po utworzeniu wystąpienia zarządzanego nie może dodać zasoby do tej podsieci.
- Podsieci nie może być grupy NSG skojarzonej z nim.
- Podsieć musi mieć użytkownika trasy tabeli przez z 0.0.0.0/0 następnego przeskoku Internet jako trasy tylko przypisane do niej. 
- Opcjonalne niestandardowe DNS: Jeśli niestandardowe DNS jest określona w sieci wirtualnej, adres IP platformy Azure cyklicznego rozpoznawania nazw (na przykład 168.63.129.16) należy dodać do listy. [Dowiedz się więcej](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-custom-dns).
- Podsieci nie może być punkt końcowy usługi (magazynu lub SQL) skojarzonych z nim. Punkty końcowe usługi powinny być wyłączone w sieci wirtualnej.
- Podsieć musi mieć co najmniej 16 adresów IP. [Dowiedz się więcej](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-vnet-configuration#determine-the-size-of-subnet-for-managed-instances) o zmiany rozmiaru podsieci zarządzanego wystąpienia.

Konfigurowanie sieci wirtualnej w następujący sposób:

1.  W witrynie Azure Portal kliknij pozycję **Utwórz zasób**. 
2. Wybierz **sieci** > **sieci wirtualnej**.
3. W **sieci wirtualnej** > **wybierz model wdrożenia**, wybierz pozycję **Resource Manager** > **Utwórz**.
4. W **Utwórz sieć wirtualną** > **nazwa**, wprowadź nazwę unikatową sieci. W naszym scenariuszu **ContosoVNETSQLMI**.
5. W **przestrzeni adresów**, Dodaj zakres adresów IP, pokazano poniżej. Zakres nie może nakładać się z lokalnymi i ContosoVNET przestrzeni adresowej.
6. W **grupy zasobów**, Utwórz nową grupę, lub wybierz istniejący. W tym scenariuszu firma Microsoft korzysta z **ContosoRG**.
7. W **lokalizacji**, wybierz region, w którym można utworzyć sieci wirtualnej. Firma Microsoft korzysta z **wschodnie stany USA 2**.
8. W **podsieci**, Dodaj nazwę pierwszej podsieci i zakres adresów. Utworzyliśmy **ManagedInstances** podsieci.
9. Wyłączanie punktów końcowych usługi.

    ![Sieci zarządzanej wystąpienia](media/migrate-scenarios-lift-and-shift/network-mi.png)

10. Po wdrożeniu sieci, należy zmienić ustawienia serwera DNS. W tym scenariuszu DNS najpierw wskazuje na kontrolerze domeny w podsieci tożsamości za pomocą statycznego prywatnego adresu IP (172.16.3.4), a następnie program rozpoznawania nazw Azure DNS 168.63.129.16.

    ![Sieci zarządzanej wystąpienia](media/migrate-scenarios-lift-and-shift/network-mi2.png)


### <a name="set-up-routing"></a>Konfigurowanie routingu

1. W lewym górnym rogu witryny Azure Portal kliknij przycisk **Utwórz zasób**. Kliknij przycisk **tabeli tras** > **Utwórz względem tabeli tras** strony.
2. W **Utwórz tabelę tras** > **nazwa**, określ nazwę tabeli.
3. Wybierz Twojej subskrypcji, grupy zasobów i lokalizacji. Pozostaw BGP wyłączone, a następnie kliknij przycisk **Utwórz**.
    ![Tabela tras](media/migrate-scenarios-lift-and-shift/route-table.png)

4. Po utworzeniu tabeli tras, otwórz go i kliknij przycisk **tras** > **+ Dodaj**.
5. W **Dodaj trasę** > **nazwa**, określ nazwę trasy.
6. W **prefiks adresu**, określ wartość 0.0.0.0/0.
7. W **następnego przeskoku typu**, wybierz pozycję **Internet**. Następnie kliknij przycisk **OK**.

     ![Tabela tras](media/migrate-scenarios-lift-and-shift/route-table2.png)


### <a name="apply-the-route-to-the-managed-instance-subnet"></a>Zastosuj trasy do podsieci zarządzanego wystąpienia

1. Otwórz sieci wirtualnej, który został utworzony. Kliknij przycisk **podsieci** > Twoja nazwa podsieci.
2. Kliknij przycisk **tabeli tras** > Twoja nazwa tabeli. Następnie kliknij przycisk **Save** (Zapisz).

     ![Tabela tras](media/migrate-scenarios-lift-and-shift/route-table3.png)

### <a name="create-a-managed-instance"></a>Utwórz wystąpienie zarządzanych

1. Kliknij przycisk **Utwórz zasób**, zlokalizuj **zarządzane wystąpienia**i wybierz **Azure wystąpienia bazy danych SQL zarządzane (wersja zapoznawcza)**.
2. Kliknij przycisk **Utwórz**.
3. W **zarządzane wystąpienia serwera SQL**, wybierz subskrypcję i upewnij się, że **Podgląd warunki** Pokaż **zaakceptowane**.
4. W **nazwa wystąpienia zarządzane**, określ nazwę. 
5. Określ nazwę użytkownika i hasło administratora dla tego wystąpienia.
6. Wybierz grupy zasobów, lokalizacji i sieć wirtualną dla tego wystąpienia.
7. Kliknij przycisk **warstwa cenowa** do rozmiaru obliczeniowej i pamięci masowej. Domyślnie wystąpienia pobiera 32 GB miejsca do magazynowania bezpłatne (kwiecień 2018).
8. Określić wielkość magazynu i liczba rdzeni wirtualnego.
9. Kliknij przycisk **Zastosuj** można zapisać ustawień, i **Utwórz** wdrażania zarządzanego wystąpienia. Aby wyświetlić stan wdrożenia, kliknij przycisk **powiadomienia**, i **wdrożenie w toku**.

    ![Wystąpienie zarządzane](media/migrate-scenarios-lift-and-shift/mi-1.png)

Po wystąpieniu zarządzane zakończeniu wdrożenia w grupie zasobów ContosoRG są wyświetlane dwa nowe zasoby: wirtualnego klastra na potrzeby zarządzanych wystąpień i zarządzane wystąpienia programu SQL. Zarówno znajdują się w lokalizacji wschodnie stany USA 2.

![Wystąpienie zarządzane](media/migrate-scenarios-lift-and-shift/managed-instance2.png)


## <a name="step-3-get-a-sas-uri-for-dms"></a>Krok 3: Pobierz identyfikator URI sygnatury dostępu Współdzielonego dla DMS

Pobierz identyfikator URI SAS, dzięki czemu usługa DMS dostęp z kontenera konta magazynu, aby przekazać pliki kopii zapasowych, które są używane do migracji baz danych do wystąpienia zarządzane bazy danych SQL Azure. 

1. Otwórz Eksploratora usługi Storage (wersja zapoznawcza).
2. W okienku po lewej stronie rozwiń konto magazynu zawierające kontenera obiektów blob, dla którego chcesz pobrać sygnatury dostępu Współdzielonego. Rozwiń konto magazynu **kontenerów obiektów Blob**.
3. Kliknij prawym przyciskiem myszy kontener, a następnie wybierz **Uzyskaj sygnaturę dostępu współdzielonego**.

     ![SAS](media/migrate-scenarios-lift-and-shift/sas-1.png)

4. W **sygnatura dostępu współdzielonego**Określ zasady, daty rozpoczęcia i wygaśnięcia, strefy czasowej, a dla zasobu poziomy dostępu. Następnie kliknij pozycję **Utwórz**.

    ![SAS](media/migrate-scenarios-lift-and-shift/sas-2.png)

5. Drugie okno dialogowe wyświetla kontenera obiektów blob, wraz z adresu URL i QueryStrings umożliwia dostęp do zasobu magazynu. Wybierz **kopiowania** można skopiować wartości. Przechowywać w bezpiecznym miejscu. Należy je po skonfigurowaniu DMS.

      ![SAS](media/migrate-scenarios-lift-and-shift/sas-3.png)  

## <a name="step-4-prepare-dms"></a>Krok 4: Przygotowanie DMS

Zarejestruj dostawcę migracja bazy danych i Utwórz wystąpienie.

### <a name="register-the-microsoftdatamigration-resource-provider"></a>Rejestrowanie dostawcy zasobów Microsoft.DataMigration

1. W ramach subskrypcji, wybierz **dostawców zasobów**. 
2. Wyszukaj "migracji", a następnie po prawej stronie Microsoft.DataMigration, wybierz **zarejestrować**. 


### <a name="create-an-instance"></a>Tworzenie wystąpienia

1. Wybierz **+ Utwórz zasób**, wyszukaj usługi systemu Azure bazy danych migracji, a następnie wybierz **usługi migracji bazy danych Azure** z listy rozwijanej.
2. Na ekranie usługa migracji bazy danych Azure (wersja zapoznawcza), wybierz **Utwórz**.
3. Określ nazwę usługi, subskrypcji, grupy zasobów, sieć wirtualną i warstwę cenową.
4. Wybierz **Utwórz** można utworzyć usługi.

    ![DMS](media/migrate-scenarios-lift-and-shift/dms-instance.png)  




## <a name="step-5-prepare-azure-for-the-site-recovery-service"></a>Krok 5: Przygotowanie Azure do usługi Site Recovery

### <a name="set-up-a-virtual-network"></a>Konfigurowanie sieci wirtualnej

Potrzebujesz sieci platformy Azure, w którym maszyny wirtualne Azure zostaną umieszczone podczas są tworzone po pracy awaryjnej i konto magazynu Azure, w której jest przechowywany replikowanych danych.

- Na potrzeby tego scenariusza użyjemy sieć platformy Azure utworzony wcześniej, aby uzyskać DMS.
- Należy pamiętać, że sieci, której używasz, musi być w tym samym regionie co magazyn usługi Site Recovery.


### <a name="create-an-azure-storage-account"></a>Tworzenie konta usługi Azure Storage

Usługa Site Recovery replikuje dane maszyny Wirtualnej do magazynu Azure. Maszyny wirtualne Azure są tworzone na podstawie magazynu w momencie przejścia w tryb failover ze środowiska lokalnego do platformy Azure.

1. W menu witryny [Azure Portal](https://portal.azure.com) wybierz pozycję **Nowy** > **Storage** > **Konto usługi Storage**.
2. W obszarze **Tworzenie konta magazynu** wprowadź nazwę konta. Na potrzeby tego samouczka użyj nazwy **contosovmsacct1910171607**. Nazwa musi mieć od 3 do 24 znaków długości, zawierać wyłącznie cyfry i małe litery oraz musi być unikatowa w obrębie platformy Azure.
3. W obszarze **Model wdrażania** wybierz opcję **Resource Manager**.
4. W obszarze **Rodzaj konta** wybierz opcję **Ogólnego przeznaczenia**. W obszarze **Wydajność** wybierz opcję **Standardowa**. Nie wybieraj magazynu obiektów blob.
5. W obszarze **Replikacja** wybierz domyślny **Magazyn geograficznie nadmiarowy z dostępem do odczytu** jako opcję nadmiarowości magazynu.
6. W obszarze **Subskrypcja** wybierz subskrypcję, w ramach której chcesz utworzyć nowe konto magazynu.
7. W obszarze **Grupa zasobów** wprowadź nową nazwę grupy zasobów. Grupa zasobów platformy Azure to logiczny kontener przeznaczony do wdrażania zasobów platformy Azure i zarządzania nimi. Na potrzeby tych samouczków użyj nazwy **ContosoRG**.
8. W obszarze **Lokalizacja** wybierz lokalizację geograficzną dla swojego konta magazynu. Konto magazynu musi znajdować się w tym samym regionie, co magazyn usługi Recovery Services. W tym scenariuszu firma Microsoft korzysta z **wschodnie stany USA 2** regionu.

   ![Tworzenie konta magazynu](media/migrate-scenarios-lift-and-shift/create-storageacct.png)

9. Wybierz pozycję **Utwórz**, aby utworzyć konto magazynu.

### <a name="create-a-recovery-services-vault"></a>Tworzenie magazynu usługi Recovery Services

1. W witrynie Azure Portal wybierz pozycję **Utwórz zasób** > **Monitorowanie i zarządzanie** > **Backup i Site Recovery**.
2. W polu **Nazwa** wprowadź przyjazną nazwę identyfikującą magazyn. Na potrzeby tego samouczka użyj nazwy **ContosoVMVault**.
3. W obszarze **Grupa zasobów** wybierz istniejącą grupę zasobów o nazwie **contosoRG**.
4. W **lokalizacji**, wprowadź region platformy Azure **wschodnie stany USA 2**.
5. Aby szybko uzyskać dostęp do magazynu z poziomu pulpitu nawigacyjnego, wybierz pozycję **Przypnij do pulpitu nawigacyjnego** > **Utwórz**.
Nowy magazyn będzie wyświetlany w sekcji **Pulpit nawigacyjny** > **Wszystkie zasoby** oraz na stronie głównej **Magazyny usług Recovery Services**.

## <a name="step-6-prepare-on-premises-vmware-for-site-recovery"></a>Krok 6: Przygotowanie VMware lokalnymi Site Recovery

Aby przygotować VMware Site Recovery, tutaj co należy zrobić:

- Przygotowywanie konta na serwerze vCenter lub hoście vSphere ESXi w celu zautomatyzowania odnajdowania maszyn wirtualnych
- Przygotowywanie konta do automatycznej instalacji usługi Mobility na maszynach wirtualnych VMware
- Przygotowanie lokalnych maszyn wirtualnych, jeśli chcesz nawiązać połączenia z maszynami wirtualnymi Azure po pracy awaryjnej.


### <a name="prepare-an-account-for-automatic-discovery"></a>Przygotowywanie konta do automatycznego odnajdowania

Usługa Site Recovery musi mieć dostęp do serwerów VMware w następujących celach:

- Automatyczne odnajdywanie maszyn wirtualnych. Wymagane jest co najmniej konto tylko do odczytu.
- Organizowanie replikacji, trybu failover i powrotu po awarii. Potrzebujesz konta, które można uruchomić operacji, takich jak tworzenie i usuwanie dysków i włączanie na maszynach wirtualnych.

Utwórz konto w następujący sposób:

1. Aby użyć dedykowanego konta, utwórz rolę na poziomie vCenter. Nadaj roli nazwę, taką jak **Azure_Site_Recovery**.
2. Przypisz do roli uprawnienia podsumowane w poniższej tabeli.
3. Utwórz użytkownika na serwerze vCenter lub hoście vSphere. Przypisz tę rolę temu użytkownikowi.

**Zadanie podrzędne** | **Rola/uprawnienia** | **Szczegóły**
--- | --- | ---
**Odnajdowanie maszyn wirtualnych** | Co najmniej użytkownik tylko do odczytu<br/><br/> Obiekt centrum danych –> propagacja do obiektu podrzędnego, rola = tylko do odczytu | Użytkownik przypisany na poziomie centrum danych, mający dostęp do wszystkich obiektów w centrum danych.<br/><br/> Aby ograniczyć dostęp, przypisz rolę **Bez dostępu** z obiektem **Propagacja do obiektu podrzędnego** do obiektów podrzędnych (hostów vSphere, magazynów danych, maszyn wirtualnych i sieci).
**Pełna replikacja, tryb failover i powrót po awarii** |  Utwórz rolę (Azure_Site_Recovery) z wymaganymi uprawnieniami, a następnie przypisz ją użytkownikowi lub grupie VMware<br/><br/> Obiekt centrum danych –> propagacja do obiektu podrzędnego, rola = Azure_Site_Recovery<br/><br/> Magazyn danych -> przydzielanie miejsca, przegląd magazynu danych, operacje na plikach niskiego poziomu, usuwanie pliku, aktualizowanie plików maszyn wirtualnych<br/><br/> Sieć -> przypisywanie sieci<br/><br/> Zasób -> przypisywanie maszyny wirtualnej do puli zasobów, migracja wyłączonej maszyny wirtualnej, migracja włączonej maszyny wirtualnej<br/><br/> Zadania -> tworzenie zadania, aktualizowanie zadania<br/><br/> Maszyna wirtualna -> konfiguracja<br/><br/> Maszyna wirtualna -> interakcja -> odpowiadanie na pytanie, połączenie z urządzeniem, konfigurowanie nośnika CD, konfigurowanie dyskietki, wyłączanie, włączanie, instalowanie narzędzi VMware<br/><br/> Maszyna wirtualna -> spis -> tworzenie, rejestrowanie, wyrejestrowywanie<br/><br/> Maszyna wirtualna -> aprowizowanie -> zezwalanie na pobieranie maszyny wirtualnej, zezwalanie na przekazywanie plików maszyny wirtualnej<br/><br/> Maszyna wirtualna -> migawki -> usuwanie migawek | Użytkownik przypisany na poziomie centrum danych, mający dostęp do wszystkich obiektów w centrum danych.<br/><br/> Aby ograniczyć dostęp, przypisz rolę **Bez dostępu** z obiektem **Propagacja do obiektu podrzędnego** do obiektów podrzędnych (hostów vSphere, magazynów danych, maszyn wirtualnych i sieci).

### <a name="prepare-an-account-for-mobility-service-installation"></a>Przygotowywanie konta do instalacji usługi Mobility

Usługa Mobility musi być zainstalowana na każdej maszynie wirtualnej, która ma być replikowana.

- Site Recovery może zrobić instalacji wypychanej automatyczne tego składnika, po włączeniu replikacji dla maszyny Wirtualnej.
- Dla instalacji wypychanej automatyczne należy przygotować konta, które uzyskują dostęp maszyny Wirtualnej odzyskiwania lokacji.
- To konto można określić po skonfigurowaniu replikacji w konsoli platformy Azure.
- Należy domeny lub lokalnego konta z uprawnieniami do zainstalowania na maszynie Wirtualnej.


### <a name="prepare-to-connect-to-azure-vms-after-failover"></a>Przygotowanie do połączenia z maszynami wirtualnymi Azure po przejściu do trybu failover

Po przejściu w tryb failover Azure można nawiązać połączenia z replikowanych maszyn wirtualnych na platformie Azure z sieci lokalnej.

Aby nawiązać połączenie z maszynami wirtualnymi z systemem Windows przy użyciu protokołu RDP po przejściu do trybu failover, wykonaj następujące czynności:

1. Aby uzyskać dostęp przez Internet, włącz protokół RDP na lokalnej maszynie wirtualnej przed włączeniem trybu failover. Upewnij się, że reguły TCP i UDP są dodawane do **publicznego** profilu oraz że RDP jest dozwolone w **zapory systemu Windows** > **dozwolone aplikacje** we wszystkich profilach.
2. Aby uzyskać dostęp za pośrednictwem połączenia VPN typu lokacja-lokacja, włącz protokół RDP na maszynie lokalnej. Używanie protokołu RDP powinno być dozwolone w pozycji **Zapora systemu Windows** -> **Dozwolone aplikacje i funkcje** dla sieci typu **Domena i prywatne**.
3. Upewnij się, że zasady sieci SAN systemu operacyjnego są ustawione na **OnlineAll**. [Dowiedz się więcej](https://support.microsoft.com/kb/3031135).
4. Podczas wyzwalania trybu failover na maszynie wirtualnej nie powinno być żadnych oczekujących aktualizacji systemu Windows. W przeciwnym razie nie będzie można zalogować się na maszynie wirtualnej do momentu ukończenia aktualizacji.
5. Na maszynie wirtualnej platformy Azure z systemem Windows po przejściu do trybu failover sprawdź **diagnostykę rozruchu**, aby wyświetlić zrzut ekranu maszyny wirtualnej. Jeśli nie możesz się połączyć, upewnij się, że maszyna wirtualna jest uruchomiona, i przejrzyj te [porady dotyczące rozwiązywania problemów](http://social.technet.microsoft.com/wiki/contents/articles/31666.troubleshooting-remote-desktop-connection-after-failover-using-asr.aspx).



## <a name="step-7-replicate-vms-with-site-recovery"></a>Krok 7: Replikowanie maszyn wirtualnych z usługą Site Recovery

### <a name="select-a-replication-goal"></a>Wybieranie celu replikacji

1. W obszarze **Magazyny usługi Recovery Services** wybierz nazwę magazynu: **ContosoVMVault**.
2. W obszarze **Wprowadzenie** wybierz pozycję Site Recovery. Następnie wybierz pozycję **Przygotowywanie infrastruktury**.
3. W obszarze **Cel ochrony** > **Gdzie znajdują się maszyny** wybierz pozycję **Lokalne**.
4. W obszarze **Gdzie chcesz zreplikować maszyny** wybierz pozycję **Na platformę Azure**.
5. W obszarze **Czy maszyny są zwirtualizowane** wybierz pozycję **Tak, z funkcją VMware vSphere Hypervisor**. Następnie wybierz przycisk **OK**.

    ![Szablon OVF](./media/migrate-scenarios-lift-and-shift/replication-goal.png)


### <a name="confirm-deployment-planning"></a>Upewnij się, planowanie wdrożenia

W **Planowanie wdrożenia**, kliknij przycisk **tak, już to zostało zrobione**, na liście rozwijanej.

### <a name="set-up-the-source-environment"></a>Konfigurowanie środowiska źródłowego


Aby skonfigurować środowisko źródłowe, potrzebna jest jedna maszyna lokalna o wysokiej dostępności, na której hostowane będą składniki lokalne usługi Site Recovery. Składniki zawierają serwer konfiguracji i serwerem przetwarzania.

- Serwer konfiguracji służy do koordynowania komunikacji między środowiskiem lokalnym i platformą Azure oraz do zarządzania replikacją danych.
- Serwer przetwarzania działa jako brama replikacji. Odbiera dane replikacji, optymalizuje je przy użyciu pamięci podręcznej, kompresji i szyfrowania, a następnie wysyła je do usługi Azure Storage.
- Serwer przetwarzania instaluje także usługę mobilności na maszynach wirtualnych, które będą replikowane, i automatycznie odnajduje lokalne maszyny wirtualne VMware.


Aby skonfigurować serwer konfiguracji jako wysokiej dostępności maszyny Wirtualnej VMware:

- Pobierz przygotować szablon Open Virtualization Format (OVF).
- Zaimportuj szablon do VMware można utworzyć maszyny Wirtualnej.
- Konfigurowanie serwera konfiguracji i zarejestruj go w magazynie. 

Po rejestracji usługa Site Recovery odnajduje lokalne maszyny wirtualne VMware.



#### <a name="download-the-vm-template"></a>Pobieranie szablonu maszyny wirtualnej

1. W magazynie przejdź do pozycji **Przygotowanie infrastruktury** > **Źródłowa**.
2. W obszarze **Przygotowywanie źródła** wybierz pozycję **+Serwer konfiguracji**.

    ![Pobieranie szablonu](./media/migrate-scenarios-lift-and-shift/new-cs.png)

4. W obszarze **Dodawanie serwera** sprawdź, czy w sekcji **Typ serwera** jest widoczna pozycja **Serwer konfiguracji dla oprogramowania VMware**.
2. Pobierz szablon OVF dla serwera konfiguracji.

    ![Pobieranie pakietu OVF](./media/migrate-scenarios-lift-and-shift/add-cs.png)

  > [!TIP]
  Najnowszą wersję szablonu serwera konfiguracji można pobrać bezpośrednio z [Centrum pobierania Microsoft](https://aka.ms/asrconfigurationserver).

#### <a name="import-the-template-in-vmware"></a>Importowanie szablonu do programu VMware

1. Zaloguj się przy użyciu VMWare vSphere Client VMware vCenter server.
2. W menu **File** (Plik) wybierz pozycję **Deploy OVF Template** (Wdróż szablon OVF), aby uruchomić kreatora wdrażania szablonu OVF. 

     ![Szablon OVF](./media/migrate-scenarios-lift-and-shift/vcenter-wizard.png)

3. Na stronie **Select source** (Wybierz źródło) wprowadź lokalizację pobranego szablonu OVF.
4. Na stronie **Review details** (Przejrzyj szczegóły) wybierz pozycję **Next** (Dalej).
5. Na stronach **Select name and folder** (Wybierz nazwę i folder) oraz **Select configuration** (Wybierz konfigurację) zaakceptuj ustawienia domyślne.
6. Aby uzyskać najlepszą wydajność, na stronie **Select storage** (Wybierz magazyn) wybierz pozycję **Thick Provision Eager Zeroed** w sekcji **Select virtual disk format** (Wybierz format dysku wirtualnego).
7. Na pozostałych stronach kreatora zaakceptuj ustawienia domyślne.
8. Na stronie **Ready to complete** (Gotowe do ukończenia):

    * Aby skonfigurować maszynę wirtualną przy użyciu ustawień domyślnych, wybierz pozycje **Włącz po wdrożeniu** > **Zakończ**.

    * Jeśli chcesz dodać więcej kart sieciowych, wyczyść pole **Power on after deployment** (Włącz po wdrożeniu). Następnie wybierz pozycję **Zakończ**. Domyślnie szablon serwera konfiguracji jest wdrażany z jedną kartą sieciową. Kolejne karty sieciowe można dodać po wdrożeniu.



#### <a name="register-the-configuration-server"></a>Rejestrowanie serwera konfiguracji 

1. Włącz maszynę wirtualną z poziomu konsoli klienta VMware vSphere.
2. Maszyna wirtualna zostanie uruchomiona do środowiska instalacji systemu Windows Server 2016. Zaakceptuj umowę licencyjną i wprowadź hasło administratora.
3. Po zakończeniu instalacji zaloguj się na maszynie wirtualnej jako administrator.
4. Po pierwszym zalogowaniu zostanie uruchomione narzędzie do konfiguracji usługi Azure Site Recovery.
5. Wprowadź nazwę używaną do zarejestrowania serwera konfiguracji w usłudze Site Recovery. Następnie wybierz przycisk **Dalej**.

    ![Szablon OVF](./media/migrate-scenarios-lift-and-shift/config-server-register1.png)

6. Narzędzie sprawdza, czy maszyna wirtualna może połączyć się z platformą Azure. Po nawiązaniu połączenia wybierz pozycję **Zaloguj się**, aby zalogować się do subskrypcji platformy Azure. Użyte poświadczenia muszą zapewniać dostęp do magazynu, w którym chcesz zarejestrować serwer konfiguracji.

    ![Szablon OVF](./media/migrate-scenarios-lift-and-shift/config-server-register2.png)

7. Narzędzie wykonuje pewne zadania konfiguracyjne, a następnie wywołuje ponowne uruchomienie.
8. Ponownie zaloguj się do maszyny. Zostanie automatycznie uruchomiony kreator zarządzania serwerem konfiguracji.

#### <a name="configure-settings-and-add-the-vmware-server"></a>Konfiguracja ustawień i dodawanie serwera VMware

1. W kreatorze zarządzania serwerem konfiguracji wybierz pozycję **Konfiguracja łączności**, a następnie wybierz kartę sieciową, która ma odbierać ruch związany z replikacją. Następnie wybierz pozycję **Zapisz**. Po skonfigurowaniu tego ustawienia nie można go zmienić.
2. Na stronie **Wybierz magazyn usługi Recovery Services** wybierz swoją subskrypcję platformy Azure, grupę zasobów i magazyn.

    ![Magazyn](./media/migrate-scenarios-lift-and-shift/cswiz1.png) 

3. Na stronie **Instalowanie oprogramowania innych firm** zaakceptuj umowę licencyjną. Wybierz pozycję **Pobierz i zainstaluj**, aby zainstalować program MySQL Server.
4. Wybierz pozycję **Zainstaluj interfejs PowerCLI programu VMware**. Przed wykonaniem tej czynności zamknij wszystkie okna przeglądarki. Następnie wybierz pozycję **Kontynuuj**.
5. W obszarze **Weryfikowanie konfiguracji urządzenia** zostaną zweryfikowane wymagania wstępne, a następnie będzie można kontynuować.
6. W obszarze **Skonfiguruj poświadczenia serwera vCenter Server/vSphere ESXi** wprowadź nazwę FQDN bądź adres IP serwera vCenter lub hosta vSphere, na którym znajdują się maszyny wirtualne, które chcesz replikować. Wprowadź port, na którym nasłuchuje serwer. Wprowadź przyjazną nazwę, która ma być używana dla serwera VMware w magazynie.
7. Wprowadź poświadczenia, za pomocą których serwer konfiguracji będzie łączył się z serwerem VMware. Przy użyciu tych poświadczeń usługa Site Recovery automatycznie odnajduje maszyny wirtualne VMware dostępne do replikacji. Wybierz pozycję **Dodaj**, a następnie wybierz pozycję **Kontynuuj**.

    ![vCenter](./media/migrate-scenarios-lift-and-shift/cswiz2.png)

8. W obszarze **Skonfiguruj poświadczenia maszyny wirtualnej** wprowadź nazwę użytkownika i hasło, które będą używane do automatycznego instalowania usługi mobilności na maszynach po włączeniu replikacji. W przypadku maszyn z systemem Windows konto wymaga uprawnień administratora lokalnego na replikowanych maszynach. 

    ![vCenter](./media/migrate-scenarios-lift-and-shift/cswiz2.png)

7. Aby ukończyć rejestrację, wybierz pozycję **Zakończ konfigurację**. 
8. Po zakończeniu rejestracji sprawdź w witrynie Azure Portal, czy serwer konfiguracji i serwer VMware są widoczne na stronie **Źródło** w magazynie. Następnie wybierz pozycję **OK**, aby skonfigurować ustawienia środowiska docelowego.
9. Usługa Site Recovery nawiąże połączenie z serwerami VMware przy użyciu podanych ustawień i odnajdzie maszyny wirtualne.

> [!NOTE]
> Zanim nazwa konta pojawi się w portalu, może minąć 15 minut lub więcej. Aby wykonać natychmiastową aktualizację, wybierz pozycje **Serwery konfiguracji** > ***nazwa serwera*** > **Odśwież serwer**.

### <a name="set-up-the-target-environment"></a>Konfigurowanie środowiska docelowego

Wybierz i zweryfikuj zasoby docelowe.

1. W **przygotowanie infrastruktury** > **docelowego**, wybierz subskrypcję platformy Azure, którego chcesz użyć.
2. Model wdrażania docelowy, wybierz **usługi Azure Resource Manager**.

3. Usługa Site Recovery sprawdza, czy masz co najmniej jedno zgodne konto magazynu Azure i co najmniej jedną sieć platformy Azure.


### <a name="create-a-replication-policy"></a>Tworzenie zasad replikacji

1. W **przygotowanie infrastruktury** > **ustawienia replikacji** > **zasad replikacji**, kliknij przycisk **Utwórz i skojarz** .
1. W obszarze **Tworzenie zasad replikacji** wprowadź wartość **VMwareRepPolicy** jako nazwę zasad replikacji.
2. W polu **Wartość progowa celu punktu odzyskiwania** użyj domyślnej wartości 60 minut. Ta wartość określa częstość tworzenia punktów odzyskiwania. Przekroczenie tego limitu przez replikację ciągłą spowoduje wygenerowanie alertu.
3. W obszarze **Przechowywanie punktu odzyskiwania**, umożliwiającym określenie, jak długie będzie okno przechowywania każdego punktu odzyskiwania, użyj domyślnej wartości 24 godzin. W tym samouczku użyj wartości 72 godziny. Replikowane maszyny wirtualne można odzyskać do dowolnego punktu w tym oknie.
4. W obszarze **Częstotliwość wykonywania migawek na poziomie aplikacji**, umożliwiającym określenie, jak często tworzone są migawki, użyj domyślnej wartości 60 minut. Wybierz pozycję **OK**, aby utworzyć zasady.

    ![Tworzenie zasad replikacji](./media/migrate-scenarios-lift-and-shift/replication-policy.png)

5. Zasady zostaną automatycznie skojarzone z serwerem konfiguracji. 

    ![Skojarz zasady replikacji](./media/migrate-scenarios-lift-and-shift/replication-policy2.png)



### <a name="enable-replication"></a>Włączanie replikacji

Teraz należy rozpocząć replikację maszyn wirtualnych. Usługa Site Recovery instaluje usługi mobilności na każdej maszynie Wirtualnej, po włączeniu replikacji dla niego. 


1. Wybierz pozycje **Replikowanie aplikacji** > **Źródło**.
2. W obszarze **Źródło** wybierz serwer konfiguracji.
3. W obszarze **Typ maszyny** wybierz pozycję **Maszyny wirtualne**.
4. W obszarze **vCenter/vSphere Hypervisor** wybierz serwer vCenter zarządzający hostem vSphere lub wybierz tego hosta.
5. Wybierz serwer przetwarzania (serwer konfiguracji). Następnie wybierz przycisk **OK**.

    ![Włączanie replikacji](./media/migrate-scenarios-lift-and-shift/enable-replication1.png)

1. W obszarze **Cel** wybierz subskrypcję i grupę zasobów, w której chcesz utworzyć maszyny wirtualne w trybie failover. Wybierz model wdrażania, którego chcesz użyć na platformie Azure (model usługi Resource Manager lub model klasyczny) dla maszyn wirtualnych w trybie failover.
2. Wybierz konto usługi Azure Storage, którego chcesz użyć na potrzeby replikacji danych.
3. Wybierz sieć platformy Azure i podsieć, z którą nawiążą połączenie maszyny wirtualne platformy Azure, gdy zostaną uruchomione po przejściu do trybu failover.
4. Wybierz opcję **Konfiguruj teraz dla wybranych maszyn**, aby zastosować ustawienia sieci do wszystkich maszyn wybranych do ochrony. Wybierz opcję **Konfiguruj później**, aby wybrać sieć platformy Azure dla poszczególnych maszyn.
5. Wybierz podsieć w sieci wirtualnej. Następnie wybierz przycisk **OK**.

    ![Włączanie replikacji](./media/migrate-scenarios-lift-and-shift/enable-replication2.png)

6. W **maszyn wirtualnych** > **wybierz maszyny wirtualne**, maszyny Wirtualnej (WEBVM), który chcesz wybrać. Możesz wybrać tylko te maszyny, dla których można włączyć replikację. 

    ![Włączanie replikacji](./media/migrate-scenarios-lift-and-shift/enable-replication3.png)

7. Aby monitorować dodawane maszyny wirtualne, możesz sprawdzić czas ostatniego odnalezienia maszyn wirtualnych w obszarze **Serwery konfiguracji** > **Ostatni kontakt**. Aby dodać maszyny wirtualne, nie czekając na zaplanowane odnajdywanie, wyróżnij serwer konfiguracji (nie wybieraj go), a następnie wybierz pozycję **Odśwież**. Zastosowanie zmian i wyświetlenie ich w portalu może potrwać 15 minut lub dłużej.
8. W obszarze **Właściwości** > **Konfigurowanie właściwości** wybierz konto, które będzie używane przez serwer przetwarzania w celu automatycznego zainstalowania usługi mobilności na maszynie. 

    ![Włączanie replikacji](./media/migrate-scenarios-lift-and-shift/enable-replication4.png)

9. W obszarze **Ustawienia replikacji** > **Konfigurowanie ustawień replikacji** sprawdź, czy wybrano właściwe zasady replikacji.
10. Wybierz pozycję **Włącz replikację**.

    ![Włączanie replikacji](./media/migrate-scenarios-lift-and-shift/enable-replication5.png)

11. Śledź postęp **Włącz ochronę** zadania w **ustawienia** > **zadania** > **zadania usługi Site Recovery**. 
12. Po **zakończenia ochrony** uruchomionych zadań, komputer jest gotowy do trybu failover i pojawia się w **omówienie** > **Essentials**.

    ![Podstawy](./media/migrate-scenarios-lift-and-shift/essentials.png)


## <a name="step-8-migrate-the-database-with-dms"></a>Krok 8: Migrowanie bazy danych o DMS

Tworzenie projektu DMS, a następnie uruchom migracji.


### <a name="create-a-database-migration-project"></a>Utwórz projekt bazy danych migracji

1. Zlokalizuj DMS zasobów w portalu Azure, a następnie otwórz go.
2. Wybierz **+ nowy projekt migracji**.
3. W **nowy projekt migracji**, określ nazwę dla projektu.
4. W obszarze **Source server type** (Typ serwera źródłowego) wybierz pozycję **SQL Server**. W **typ serwer docelowy**, wybierz pozycję **wystąpienia zarządzane bazy danych SQL Azure**.
5. Kliknij przycisk **Utwórz** Aby utworzyć projekt.
6. W **źródła szczegóły**, określ szczegóły połączenia lokalnej źródła programu SQL Server. Kliknij pozycję **Zapisz**.
7. W **docelowe szczegóły**, określ szczegóły połączenia dla obiektu docelowego, jest wstępnie przygotowany Azure wystąpienia bazy danych SQL zarządzanych do której zostaną zmigrowane lokalną bazą danych. Następnie kliknij przycisk **Save** (Zapisz).
8. W **podsumowanie projektu**, przejrzyj i sprawdź szczegóły skojarzone z projektem migracji.

    ![Usługa DMS projektu](./media/migrate-scenarios-lift-and-shift/dms-project.png)

### <a name="migrate-the-database"></a>Migrację bazy danych

1. Po utworzeniu projektu, zostanie wyświetlony Kreator migracji.
2. Określ poświadczenia dla serwera źródłowego i docelowego, a następnie kliknij przycisk **zapisać**. Kreator próbuje zalogować się do lokalnego serwera SQL.

    ![Usługa DMS Kreatora](./media/migrate-scenarios-lift-and-shift/dms-wiz1.png)

3. W **mapy w celu docelowymi bazami danych**, wybierz źródłowej bazy danych chcesz migrować. Następnie kliknij przycisk **Save** (Zapisz).

    ![Usługa DMS Kreatora](./media/migrate-scenarios-lift-and-shift/dms-wiz2.png)

4. W **docelowe szczegóły**, wybierz pozycję **sprawdzić Moje dane docelowej**. Podaj nazwę DNS wystąpień programu SQL zarządzanych, oraz poświadczenia. Następnie kliknij przycisk **Save** (Zapisz).

    ![Usługa DMS Kreatora](./media/migrate-scenarios-lift-and-shift/dms-wiz3.png)

5. W projekcie zapisane, wybierz **+ nowe działanie**. Następnie wybierz **uruchamiania migracji**.
6. Po wyświetleniu monitu wprowadź poświadczenia dla serwera źródłowego i docelowego. Następnie kliknij przycisk **Save** (Zapisz).
7. W **mapy w celu docelowymi bazami danych**, wybierz źródłowej bazy danych, które chcesz migrować. Następnie kliknij przycisk **Zapisz**
8. W **skonfigurować ustawień migracji** > **lokalizacji kopii zapasowej serwera**, określić udział sieciowy, utworzyć na maszynie lokalnej. Usługa DMS przyjmuje kopii zapasowych źródła do tego udziału.  Należy pamiętać, że konto usługi uruchomione wystąpienie programu SQL Server źródło musi mieć uprawnienia zapisu w tym udziale.
9. Określ nazwę użytkownika i hasło, w którym DMS mogą personifikować potrzeby przekazywania plików kopii zapasowej do kontenera magazynu Azure, do przywrócenia.
10. Określ identyfikator URI sygnatury dostępu Współdzielonego, który zapewnia dostęp do Twojego kontenera konta magazynu, do której usługa przekazywania plików kopii zapasowej, DMS i które są wykorzystywane do migracji do wystąpienia zarządzane bazy danych SQL Azure.  Następnie kliknij przycisk **Save** (Zapisz).
11. W **podsumowanie migracji**, określ nazwę działania migracji > **uruchamiania migracji**.
12. W projekcie > **omówienie**, monitorować stan migracji. Po zakończeniu migracji Sprawdź, czy z docelowymi bazami danych istnieją w wystąpieniu zarządzanych.


## <a name="step-9-migrate-vms-with-site-recovery"></a>Krok 9: Migrowanie maszyn wirtualnych z usługą Site Recovery

Zaleca się, że możesz uruchomić test trybu failover, aby upewnić się, że wszystko działa zgodnie z oczekiwaniami, przed uruchomieniem pełnej migracji. Po uruchomieniu próby przejścia w tryb failover wykonywane są następujące operacje:

1. Uruchamia się upewnić, że wszystkie warunki wymagane do migracji w miejscu Sprawdzanie wymagań wstępnych.
2. Tryb failover przetwarza dane, aby umożliwić utworzenie maszyny wirtualnej platformy Azure. Jeśli zostanie wybrany najnowszy punkt odzyskiwania, punkt odzyskiwania zostanie utworzony na podstawie danych.
3. Tworzona jest maszyna wirtualna platformy Azure przy użyciu danych przetworzonych w poprzednim kroku.

### <a name="run-a-test-failover"></a>Wykonywanie próby przejścia w tryb failover

1. W obszarze **Chronione elementy** kliknij kolejno pozycje **Zreplikowane elementy** > Maszyna wirtualna.
2. We właściwościach maszyny Wirtualnej, kliknij przycisk **+ testowy tryb Failover**.

    ![Testowanie trybu failover](./media/migrate-scenarios-lift-and-shift/test-failover.png)

3. Wybierz **najnowsze przetworzone**, aby przełączyć maszynę Wirtualną do najnowszego dostępnego punktu w czasie. Wyświetlana jest sygnatura czasowa. Ta opcja zapewnia niską wartość celu czasu odzyskiwania (RTO, Recovery Time Objective), ponieważ nie trzeba poświęcać czasu na przetwarzanie danych.
2. W obszarze **Test pracy w trybie failover** wybierz sieć platformy Azure, z którą maszyny wirtualne platformy Azure zostaną połączone po przejściu w tryb failover.
3. Kliknij przycisk **OK**, aby rozpocząć tryb failover. Możesz śledzić postęp bin magazynu > **ustawienia** > **zadania** > **testowy tryb Failover**.
4. Po zakończeniu trybu failover w obszarze Azure Portal > **Maszyny wirtualne** będzie widoczna replika maszyny wirtualnej platformy Azure. Upewnij się, że maszyna wirtualna ma prawidłowy rozmiar, jest połączona z odpowiednią siecią i jest uruchomiona. Powinno być teraz możliwe nawiązanie połączenia ze zreplikowaną maszyną wirtualną na platformie Azure.
5. Aby usunąć maszyny wirtualne platformy Azure utworzone podczas testowego przełączania w tryb failover, kliknij pozycję **Wyczyść test pracy w trybie failover** na maszynie wirtualnej. W obszarze **Uwagi** zarejestruj i zapisz wszelkie obserwacje związane z testem pracy w trybie failover.

### <a name="migrate-the-machines"></a>Dokonaj migracji maszyn

Po upewnieniu się, że testowy tryb failover działa zgodnie z oczekiwaniami, należy utworzyć plan odzyskiwania, aby przeprowadzić migrację do platformy Azure.

### <a name="create-a-recovery-plan"></a>Tworzenie planu odzyskiwania

1. W magazynie, wybierz **plany odzyskiwania (lokacji odzyskiwania)** > **+ planu odzyskiwania**.
2. W **Utwórz plan odzyskiwania**, określ nazwę planu.
3. Wybierz źródłowy serwer konfiguracji i Azure jako element docelowy. Wybierz **Resource Manager** modelu wdrażania. Lokalizacja źródłowa musi mieć maszyny, które są włączone dla trybu failover i odzyskiwania. 
4. W **wybierz elementy**, Dodaj maszyny (WEBVM) do planu. Następnie kliknij przycisk **OK**.
5. Kliknij przycisk **OK** Aby utworzyć plan.

    ![Plan odzyskiwania](./media/migrate-scenarios-lift-and-shift/recovery-plan1.png)

### <a name="run-a-failover"></a>Uruchamianie trybu failover

1. W **plany odzyskiwania**, kliknij przycisk plan > **pracy awaryjnej**.
2. W **pracy awaryjnej** > **punkt odzyskiwania**, wybierz najnowszy punkt odzyskiwania.
3. Ustawienie klucza szyfrowania nie ma znaczenia w przypadku tego scenariusza.
4. Wybierz pozycję **Zamknij maszynę przed rozpoczęciem pracy w trybie failover**. Usługa Site Recovery spróbuje przeprowadzić zamknięcie źródłowych maszyn wirtualnych przed wyzwoleniem trybu failover. Przełączanie do trybu failover będzie kontynuowane, nawet jeśli zamknięcie nie powiedzie się. Na stronie **Zadania** można śledzić postęp trybu failover.

    ![Tryb failover](./media/migrate-scenarios-lift-and-shift/failover1.png)

5. Sprawdź, czy maszyna wirtualna Azure jest wyświetlana na platformie Azure zgodnie z oczekiwaniami.

    ![Tryb failover](./media/migrate-scenarios-lift-and-shift/failover2.png)

6. W obszarze **Replikowane elementy** kliknij prawym przyciskiem myszy maszynę wirtualną > **Zakończ migrację**. Spowoduje to zakończenie procesu migracji, zatrzymanie replikacji maszyny wirtualnej oraz zatrzymanie naliczania opłat za usługę Site Recovery dla maszyny wirtualnej.

    ![Tryb failover](./media/migrate-scenarios-lift-and-shift/failover3.png)

### <a name="update-the-connection-string"></a>Zaktualizuj parametry połączenia

Ostatnim krokiem w procesie migracji jest aby zaktualizować parametry połączenia aplikacji, aby wskazywał migrowanych bazy danych, systemem MI programu SQL.

1. Nowe parametry połączenia można znaleźć w portalu Azure, klikając **ustawienia** > **parametry połączenia**.

    ![Tryb failover](./media/migrate-scenarios-lift-and-shift/failover4.png)  

2. Musisz zaktualizować te parametry przy użyciu nazwy użytkownika i hasła dla użytkownika MI SQL.
3. Po skonfigurowaniu ciąg, należy zastąpić bieżących parametrów połączenia w pliku web.config aplikacji.
4. Po aktualizacji plik i zapisać go, uruchom ponownie usługi IIS na WEBVM. Można to zrobić przy użyciu /RESTART IISRESET z wiersza polecenia.
5. Po ponownym uruchomieniu usług IIS aplikacji zostanie teraz można przy użyciu bazy danych, systemem MI programu SQL.
6. W tym momencie można zamknąć SQLVM maszyny lokalnej, a migracja została zakończona.



## <a name="conclusion"></a>Podsumowanie

W tym scenariuszu zostały wykonane następujące kroki:

> [!div class="checklist"]
> * Migracja naszych lokalną bazą danych do wystąpienia zarządzane SQL Azure z DMS.
> * Migracja naszych lokalnych maszyn wirtualnych do maszyn wirtualnych platformy Azure, z usługą Azure Site Recovery.
