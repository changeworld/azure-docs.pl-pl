# <a name="platform-supported-migration-of-iaas-resources-from-classic-to-azure-resource-manager"></a>Obsługiwane platformy migracji zasobów IaaS ze środowiska klasycznego do usługi Azure Resource Manager
W tym artykule opisano sposób w przypadku włączania migracji infrastruktura jako usługa (IaaS) zasoby z klasycznego, który to modele wdrażania Menedżera zasobów. Możesz przeczytać dodatkowe informacje [funkcje usługi Azure Resource Manager i korzyści](../articles/azure-resource-manager/resource-group-overview.md). Firma Microsoft szczegółowo sposób łączenie zasobów z modeli dwa wdrożenia, które są dostępne w ramach subskrypcji, przy użyciu bramy lokacja lokacja sieci wirtualnej.

## <a name="goal-for-migration"></a>Cel dla migracji
Menedżer zasobów umożliwia wdrażanie złożonych aplikacji za pomocą szablonów, konfiguruje maszyn wirtualnych przy użyciu rozszerzeń maszyny Wirtualnej i dołącza zarządzania dostępem i znakowanie. Usługa Azure Resource Manager obejmuje skalowalne, równoległe wdrażanie maszyn wirtualnych do zestawów dostępności. Nowy model wdrażania są także zarządzanie cyklem życia obliczeniowych, sieci i pamięci masowej niezależnie. Na koniec jest nacisk na włączenie zabezpieczeń domyślnie z wymuszania maszyn wirtualnych w sieci wirtualnej.

Prawie wszystkie funkcje dostępne w klasycznym modelu wdrażania są obsługiwane dla zasobów obliczeniowych, sieci i magazynu w obszarze usługi Azure Resource Manager. Aby korzystać z nowych funkcji usługi Azure Resource Manager, można migrować istniejące wdrożenia z klasycznego modelu wdrożenia.

## <a name="supported-resources-for-migration"></a>Obsługiwane zasobów do migracji
Te zasoby klasyczne IaaS są obsługiwane podczas migracji

* Maszyny wirtualne
* Zestawy dostępności
* Cloud Services
* Konta magazynu
* Sieci wirtualne
* Bramy VPN Gateway
* Express Route bram _(w tej samej subskrypcji co sieć wirtualna tylko)_
* Grupy zabezpieczeń sieci
* Tabele tras
* Zastrzeżone adresy IP

## <a name="supported-scopes-of-migration"></a>Obsługiwane zakresy migracji
Istnieją różne sposoby 4 do ukończenia migracji zasobów obliczeniowych, sieci i magazynu. Są to

* Migracja maszyn wirtualnych (nie w sieci wirtualnej)
* Migracja maszyn wirtualnych (w sieci wirtualnej)
* Migracja kont magazynu
* Odłączyć zasobów (grup zabezpieczeń sieci, tabele tras i zastrzeżonych adresów IP)

### <a name="migration-of-virtual-machines-not-in-a-virtual-network"></a>Migracja maszyn wirtualnych (nie w sieci wirtualnej)
W modelu wdrażania usługi Resource Manager zabezpieczenia są realizowane aplikacji domyślnie. Wszystkie maszyny wirtualne muszą znajdować się w sieci wirtualnej w modelu usługi Resource Manager. Ponowne uruchomienie platformy Azure (`Stop`, `Deallocate`, i `Start`) maszyn wirtualnych w ramach migracji. Masz dwie opcje dla sieci wirtualnych, które zostaną poddane migracji maszyn wirtualnych do:

* Możesz poprosić o platformie, aby utworzyć nową sieć wirtualną i migracji maszyny wirtualnej do nowej sieci wirtualnej.
* Należy przeprowadzić migrację maszyny wirtualnej w ramach istniejącej sieci wirtualnej w Menedżerze zasobów.

> [!NOTE]
> W tym zakresie migracji zarówno operacje zarządzania płaszczyzny, jak i operacje płaszczyzna danych niedozwolony przez pewien czas w trakcie migracji.
>
>

### <a name="migration-of-virtual-machines-in-a-virtual-network"></a>Migracja maszyn wirtualnych (w sieci wirtualnej)
W przypadku większości konfiguracji maszyny Wirtualnej jedynie metadane jest Migrowanie między modelami wdrażania klasycznego i Menedżera zasobów. Podstawowe maszyny wirtualne są uruchomione na tym samym sprzęcie, w tej samej sieci, a także z tego samego magazynu. Operacje zarządzania płaszczyzny niedozwolony przez wybrany okres czasu, podczas migracji. Jednak płaszczyzna danych pozostaje uruchomiona. Oznacza to, że aplikacje uruchomione na maszynach wirtualnych (klasyczne) nie pociągnąć za sobą przestoju podczas migracji.

Następujące konfiguracje nie są obecnie obsługiwane. Jeśli zostanie dodana jego obsługa w przyszłości, niektóre maszyny wirtualne w tej konfiguracji może pociągnąć za sobą przestoju (Przejdź do zatrzymania, deallocate i ponownego uruchamiania działania maszyny Wirtualnej).

* Masz więcej niż jeden zestaw w usłudze w chmurze jednej dostępności.
* Masz jeden lub więcej zestawów dostępności i maszyn wirtualnych, które nie są dostępne w zestawie w usłudze w chmurze jednej dostępności.

> [!NOTE]
> W tym zakresie migracji płaszczyzny zarządzania niedozwolony przez pewien czas w trakcie migracji. Dla niektórych konfiguracji opisanych wcześniej, płaszczyzna danych wystąpienia.
>
>

### <a name="storage-accounts-migration"></a>Migracja kont magazynu
Umożliwia bezproblemowe migracji, można wdrożyć maszyn wirtualnych Menedżera zasobów na koncie magazynu klasycznego. Dzięki tej możliwości obliczeniowych i zasobów sieciowych można i powinny być migrowane niezależnie od konta magazynu. Po migracji za pośrednictwem sieci maszyn wirtualnych i sieci wirtualnej, należy wykonać migrację konta magazynu, aby ukończyć proces migracji.

> [!NOTE]
> Model wdrażania Menedżera zasobów nie ma pojęcie Classic obrazy i dyski. Kiedy konto magazynu jest zmigrowane, klasycznym obrazy i dyski nie są widoczne w stosie usługi Resource Manager przy zachowaniu zapasowy wirtualne dyski twarde na koncie magazynu.
>
>

### <a name="unattached-resources-network-security-groups-route-tables--reserved-ips"></a>Odłączyć zasobów (grup zabezpieczeń sieci, tabele tras i zastrzeżonych adresów IP)
Grupy zabezpieczeń sieci, tabele tras i zastrzeżonych adresów IP, które nie są dołączone do maszyn wirtualnych i sieci wirtualne można migrować niezależnie.

<br>

## <a name="unsupported-features-and-configurations"></a>Nieobsługiwane funkcje i konfiguracji
Firma Microsoft aktualnie nie obsługują niektórych funkcjach i konfiguracjach. W poniższych sekcjach opisano Nasze zalecenia wokół nich.

### <a name="unsupported-features"></a>Nieobsługiwane funkcje
Następujące funkcje nie są obecnie obsługiwane. Można opcjonalnie usunąć te ustawienia, migracji maszyn wirtualnych i następnie ponownie Włącz ustawienia w modelu wdrażania usługi Resource Manager.

| Dostawca zasobów | Cecha | Zalecenie |
| --- | --- | --- |
| Wystąpienia obliczeniowe | Dyski nieskojarzone maszyny wirtualnej. | Obiekty BLOB dysków VHD za tych dyskach zostaną uzyskać migracji podczas migracji konta magazynu |
| Wystąpienia obliczeniowe | Obrazy maszyny wirtualnej. | Obiekty BLOB dysków VHD za tych dyskach zostaną uzyskać migracji podczas migracji konta magazynu |
| Sieć | Listy ACL punktu końcowego. | Usuń listy ACL punktu końcowego, a następnie ponów próbę wykonania migracji. |
| Sieć | Application Gateway | Usuń bramę aplikacji przed rozpoczęciem migracji, a następnie utwórz ponownie bramę aplikacji, po zakończeniu migracji. |
| Sieć | Sieci wirtualne przy użyciu sieci wirtualnej komunikacji równorzędnej. | Migracja sieci wirtualnej do Resource Manager, a następnie elementu równorzędnego. Dowiedz się więcej o [sieci wirtualnej komunikacji równorzędnej](../articles/virtual-network/virtual-network-peering-overview.md). |

### <a name="unsupported-configurations"></a>Nieobsługiwane konfiguracje
Następujące konfiguracje nie są obecnie obsługiwane.

| Usługa | Konfigurowanie | Zalecenie |
| --- | --- | --- |
| Resource Manager |Na podstawie dostępu kontroli ról (RBAC) w przypadku klasycznych zasobów |Ponieważ identyfikator URI zasobów jest zmodyfikowane po migracji, zaleca się zaplanowanie aktualizacji zasad RBAC, które wymagają po migracji. |
| Wystąpienia obliczeniowe |Wiele podsieci skojarzone z maszyny Wirtualnej |Zaktualizuj konfigurację podsieci, aby odwoływać się tylko jedną podsieć. Może to wymagać usunięcia dodatkowej karty Sieciowej (do którego odwołuje się do innej podsieci) z maszyny Wirtualnej i dołączyć go ponownie po zakończeniu migracji. |
| Wystąpienia obliczeniowe |Maszyny wirtualne, które należą do sieci wirtualnej, ale nie ma jawnego podsieci przypisane |Opcjonalnie można usunąć maszyny Wirtualnej. |
| Wystąpienia obliczeniowe |Maszyny wirtualne, które mają alertów, zasad automatycznego skalowania |Migracja przechodzi przez i te ustawienia są usuwane. Zdecydowanie zaleca się dokonanie oceny środowiska przed wykonaniem migracji. Alternatywnie po zakończeniu migracji można ponownie skonfigurować ustawienia alertów. |
| Wystąpienia obliczeniowe |Rozszerzenia maszyny Wirtualnej XML (BGInfo 1.*, debuger programu Visual Studio Web Deploy i zdalnego debugowania) |Jest to nieobsługiwane. Zalecane jest, usuń te rozszerzenia z maszyny wirtualnej, aby kontynuować migrację lub ich zostaną usunięte automatycznie podczas procesu migracji. |
| Wystąpienia obliczeniowe |Diagnostyki rozruchu z magazyn w warstwie Premium |Wyłącz funkcję diagnostyki rozruchu dla maszyn wirtualnych przed kontynuowaniem migracji. Po zakończeniu migracji, można ponownie włączyć diagnostykę rozruchu w stosie usługi Resource Manager. Ponadto obiektów blob, które są używane dla zrzut ekranu i serial dzienniki powinien zostać usunięty, więc nie są naliczane opłaty dla tych obiektów blob. |
| Wystąpienia obliczeniowe | Usługi w chmurze, zawierające role sieć web/proces roboczy. | To nie jest obecnie obsługiwane. |
| Wystąpienia obliczeniowe | Ustaw zawierające więcej niż jednej dostępności usługi w chmurze lub ustawia wiele dostępności. |To nie jest obecnie obsługiwane. Przenieś maszyny wirtualne na tym samym zestawie przed migracją dostępności. |
| Wystąpienia obliczeniowe | Maszyna wirtualna z rozszerzeniem Centrum zabezpieczeń Azure | Centrum zabezpieczeń Azure automatycznie instaluje rozszerzenia na maszynach wirtualnych, aby monitorować ich zabezpieczeń i zgłaszanie alertów. Rozszerzenia te zwykle uzyskać zainstalowana automatycznie, jeśli Centrum zabezpieczeń Azure jest włączona dla tej subskrypcji. Aby dokonać migracji maszyn wirtualnych, Wyłącz zasady Centrum zabezpieczeń dla subskrypcji, która spowoduje usunięcie monitorowania rozszerzenia z maszyny wirtualnej w Centrum zabezpieczeń. |
| Wystąpienia obliczeniowe | Maszyna wirtualna z rozszerzeniem kopii zapasowej lub migawki | Te rozszerzenia są zainstalowane na maszynie wirtualnej skonfigurowanych z usługą kopia zapasowa Azure. Aby przeprowadzić migrację tych maszyn wirtualnych, postępuj zgodnie ze wskazówkami [tutaj](https://docs.microsoft.com/azure/virtual-machines/windows/migration-classic-resource-manager-faq#vault).  |
| Sieć |Sieci wirtualne, które zawierają maszyny wirtualne i role sieć web/proces roboczy |To nie jest obecnie obsługiwane. Przed migracją Przenieś role sieć Web/proces roboczy do ich własnych sieci wirtualnej. Po migracji klasycznych sieci wirtualnych migrowanych sieci wirtualnej Menedżera zasobów Azure można można połączyć za pomocą z klasycznej sieci wirtualnej do osiągnięcia podobnej konfiguracji jak poprzednio.|
| Sieć | Klasycznym obwody Express Route |To nie jest obecnie obsługiwane. Te obwody należy migrować do usługi Azure Resource Manager przed rozpoczęciem migracji IaaS. Aby dowiedzieć się więcej na ten temat, zobacz [obwody przenoszenie usługi ExpressRoute z klasycznego modelu wdrażania usługi Resource Manager](../articles/expressroute/expressroute-move.md).|
| Azure App Service |Sieci wirtualne, które zawierają środowiska usługi aplikacji |To nie jest obecnie obsługiwane. |
| Azure HDInsight |Sieci wirtualne, które zawierają usługi HDInsight |To nie jest obecnie obsługiwane. |
| Usługi cyklu życia Microsoft Dynamics |Sieci wirtualne, które zawierają maszyny wirtualne, które są zarządzane przez usługi cyklu życia Dynamics |To nie jest obecnie obsługiwane. |
| Azure AD Domain Services |Sieci wirtualne, które zawierają usługi domenowe Azure AD |To nie jest obecnie obsługiwane. |
| Azure RemoteApp |Sieci wirtualne zawierające wdrożenia usługi Azure RemoteApp |To nie jest obecnie obsługiwane. |
| Usługa Azure API Management |Sieci wirtualne zawierające wdrożenia usługi Azure API Management |To nie jest obecnie obsługiwane. Aby przeprowadzić migrację sieci Wirtualnej IaaS, zmień sieci Wirtualnej wdrożenia usługi API Management, czyli żadna operacja przestoju. |
