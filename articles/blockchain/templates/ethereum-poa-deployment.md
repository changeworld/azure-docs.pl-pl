---
title: Wdróż szablon rozwiązania typu "Weryfikacja urzędu certyfikacji Ethereum" na platformie Azure
description: Użyj rozwiązania Ethereum The-Authority Consortium do wdrożenia i skonfigurowania wieloskładnikowej sieci Ethereum konsorcjum na platformie Azure
ms.date: 04/08/2019
ms.topic: article
ms.reviewer: coborn
ms.openlocfilehash: c3e449c1d6ebaf7c6cb2c35dc9f91d55f569447a
ms.sourcegitcommit: b77e97709663c0c9f84d95c1f0578fcfcb3b2a6c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/22/2019
ms.locfileid: "74326169"
---
# <a name="deploy-ethereum-proof-of-authority-consortium-solution-template-on-azure"></a>Wdróż szablon rozwiązania typu "Weryfikacja urzędu certyfikacji Ethereum" na platformie Azure

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[Szablon rozwiązania Azure Ethereum-of-Authority Consortium](https://portal.azure.com/?pub_source=email&pub_status=success#create/microsoft-azure-blockchain.azure-blockchain-ethereumethereum-poa-consortium) został zaprojektowany, aby ułatwić wdrażanie, Konfigurowanie i nadzorowanie sieci Ethereumej z obsługą wieloskładnikowej organizacji z minimalnym użyciem platformy Azure i Ethereum.

W przypadku kilku danych wejściowych użytkownika i wdrożenia pojedynczego kliknięcia za pośrednictwem Azure Portal każdy członek może zainicjować obsługę sieci przy użyciu Microsoft Azure usług obliczeniowych, sieciowych i magazynowych na całym świecie. Zawartość sieci każdego członka składa się z zestawu węzłów modułu sprawdzania z równoważeniem obciążenia, za pomocą których aplikacja lub użytkownik może współdziałać w celu przesyłania transakcji Ethereum.

## <a name="concepts"></a>Pojęcia

### <a name="terminology"></a>Terminologia

-   **Konsensus** — czynność synchronizowania danych w sieci rozproszonej przez weryfikację i Tworzenie bloków.

-   **Członek konsorcjum** — jednostka, która uczestniczy w konsensusie w sieci łańcucha bloków.

-   **Administrator** — konto Ethereum, które jest używane do zarządzania uczestnictwem dla danego elementu członkowskiego konsorcjum.

-   **Moduł sprawdzania poprawności** — maszyna skojarzona z kontem Ethereum, która uczestniczy w konsensusie w imieniu administratora.

### <a name="proof-of-authority"></a>Potwierdzenie urzędu

W przypadku osób, które są nowe dla społeczności łańcucha bloków, wydanie tego rozwiązania jest świetną okazją do poznania się z technologią w łatwy i konfigurowalny sposób na platformie Azure. Test pracy jest mechanizmem odporności Sybil, który wykorzystuje koszty obliczeniowe do samodzielnego regulowania sieci i umożliwia uczciwe uczestnictwo. Dzięki temu można uzyskać doskonały dostęp do otwartych sieci łańcucha bloków, w których konkurs kryptowalucie podwyższa poziom bezpieczeństwa w sieci. Jednakże w sieciach prywatnych/konsorcjum podstawowy Eter nie ma wartości. Alternatywny protokół, potwierdzenie urzędu, jest bardziej odpowiedni dla dozwolonych sieci, w których wszyscy konsensusy są znani i wiarygodne. Bez konieczności wyszukiwania, potwierdzenie urzędu jest wydajniejsze i nadal utrzymują Byzantine odporność na uszkodzenia.

### <a name="consortium-governance"></a>Zarządzanie konsorcjum

Ponieważ potwierdzenie urzędu jest zależne od dozwolonej listy urzędów sieciowych w celu zapewnienia dobrej kondycji sieci, ważne jest, aby zapewnić sprawiedliwy mechanizm wprowadzania modyfikacji tej listy uprawnień. Każde wdrożenie jest dostarczane z zestawem inteligentnych kontraktów i portalu na potrzeby nadzoru w łańcuchu na tej liście dozwolonych. Gdy proponowana zmiana osiągnie większość głosów przez członków konsorcjum, zmiana zostanie wdrożona. Pozwala to na dodanie nowych i zagrożonych uczestników w przejrzysty sposób, który zachęca do uczciwej sieci.

### <a name="admin-account"></a>Konto administratora

Podczas wdrażania węzłów potwierdzenia urzędu zostanie wyświetlony monit o podanie adresu administratora Ethereum. Do wygenerowania i zabezpieczenia tego konta Ethereum można użyć kilku różnych mechanizmów. Po dodaniu tego adresu jako urzędu w sieci można użyć tego konta do uczestnictwa w ładu. To konto administratora służy również do delegowania jednomyślnego udziału w węzłach modułu sprawdzania poprawności, które są tworzone w ramach tego wdrożenia. Ponieważ używany jest tylko publiczny adres Ethereum, każdy administrator ma elastyczność do zabezpieczania swoich kluczy prywatnych w taki sposób, który jest zgodny z modelem zabezpieczeń.

### <a name="validator-node"></a>Węzeł modułu sprawdzania poprawności

W protokole dowodu uwierzytelniania węzły modułu sprawdzania poprawności przenoszą miejsce tradycyjnych węzłów Miner. Każdy moduł sprawdzania poprawności ma unikatową tożsamość Ethereum, która jest dodawana do listy uprawnień inteligentnych. Gdy moduł walidacji znajduje się na tej liście, może uczestniczyć w procesie tworzenia bloku. Aby dowiedzieć się więcej o tym procesie, zobacz dokumentację dotyczącej parzystości na [drodze do urzędu](https://wiki.parity.io/Aura). Każdy członek konsorcjum może zainicjować co najmniej dwa węzły modułu sprawdzania poprawności w pięciu regionach w celu zapewnienia nadmiarowości geograficznej. Węzły modułu sprawdzania poprawności komunikują się z innymi węzłami modułu sprawdzania poprawności w celu osiągnięcia konsensusu na stanie podstawowej księgi rozproszonej.
Aby zapewnić sprawiedliwy udział w sieci, każdy członek konsorcjum nie będzie mógł używać więcej modułów sprawdzania poprawności niż pierwszy element członkowski w sieci (jeśli pierwszy element członkowski wdraża trzy walidatory, każdy element członkowski może mieć maksymalnie trzy moduły walidacji).

### <a name="identity-store"></a>Magazyn tożsamości

Ponieważ każdy element członkowski będzie miał jednocześnie wiele węzłów modułu sprawdzania poprawności, a każdy węzeł musi mieć dozwoloną tożsamość, ważne jest, aby moduły walidacji mogły bezpiecznie uzyskać unikatową aktywną tożsamość w sieci. Aby to ułatwić, utworzyliśmy magazyn tożsamości, który jest wdrażany w ramach subskrypcji każdej składowej, która bezpiecznie przechowuje wygenerowane tożsamości Ethereum. Po wdrożeniu kontener aranżacji wygeneruje klucz prywatny Ethereum dla każdego modułu sprawdzania poprawności i zapisze go w Azure Key Vault. Zanim węzeł z parzystością zostanie uruchomiony, najpierw uzyska dzierżawę w nieużywanej tożsamości, aby upewnić się, że tożsamość nie jest pobrana przez inny węzeł. Tożsamość jest udostępniana Klientowi, który nadaje mu uprawnienia do rozpoczęcia tworzenia bloków. W przypadku wystąpienia awarii na maszynie wirtualnej zostanie wydana dzierżawa tożsamości, dzięki czemu węzeł zastępczy wznowi swoją tożsamość w przyszłości.

### <a name="bootnode-registrar"></a>Rejestrator Bootnode

Aby zapewnić łatwość łączności, każdy element członkowski będzie obsługiwał zestaw informacji o połączeniu w [punkcie końcowym interfejsu API danych](#data-api). Te dane obejmują listę bootnodes, które są udostępniane jako węzły komunikacji równorzędnej dla członka dołączającego. W ramach tego interfejsu API danych utrzymujemy aktualną listę bootnode

### <a name="bring-your-own-operator"></a>Przenoszenie własnego operatora

Często członek konsorcjum będzie chciał uczestniczyć w nadzorze sieci, ale nie chce działać i utrzymywać infrastruktury. W przeciwieństwie do tradycyjnych systemów, posiadanie jednego operatora w całej sieci działa w oparciu o zdecentralizowany model systemów łańcucha bloków. Zamiast zatrudniać scentralizowaną pośrednią do obsługi sieci, każdy członek konsorcjum może delegować zarządzanie infrastrukturą do operatora wyboru. Pozwala to na model hybrydowy, w którym każdy element członkowski może korzystać z własnej infrastruktury lub operacji delegowania do innego partnera. Przepływ pracy delegowanej operacji działa w następujący sposób:

1.  **Członek konsorcjum** generuje adres Ethereum (zawiera klucz prywatny)

2.  **Członek konsorcjum** udostępnia publiczny adres Ethereum do **operatora**

3.  Służy **do wdrażania i** konfigurowania węzłów modułu sprawdzania poprawności POA przy użyciu naszego rozwiązania Azure Resource Manager

4.  **Operator** udostępnia punkt końcowy RPC i zarządzania do **członka konsorcjum**

5.  **Członek konsorcjum** używa swojego klucza prywatnego do podpisywania żądania akceptującego **operator** węzłów modułu sprawdzania poprawności wdrożony do uczestnictwa w ich imieniu

### <a name="azure-monitor"></a>Azure Monitor

To rozwiązanie zawiera również Azure Monitor do śledzenia statystyk węzła i sieci. W przypadku deweloperów aplikacji zapewnia to wgląd w bazowe łańcucha bloków śledzenia statystyk generowania bloku. Operatorzy sieci mogą używać Azure Monitor, aby szybko wykrywać i zapobiegać awariom sieci za pomocą statystyk infrastruktury i dzienników queryable. Aby uzyskać więcej informacji, zobacz [monitorowanie usług](#service-monitoring).

### <a name="deployment-architecture"></a>Architektura wdrożenia

#### <a name="description"></a>Opis

To rozwiązanie może wdrożyć pojedynczą lub wieloregionową sieć wieloskładnikowej Ethereum. Domyślnie punkty końcowe wywołania RPC i komunikacji równorzędnej są dostępne za pośrednictwem publicznego adresu IP, aby umożliwić uproszczoną łączność między subskrypcjami i chmurami. Zalecamy użycie [kontraktów z uprawnieniami](https://wiki.parity.io/Permissioning) do kontroli dostępu na poziomie aplikacji. Obsługujemy również sieci, które są wdrożone za pośrednictwem sieci VPN, które wykorzystują bramy VNet do łączności między subskrypcjami. Te wdrożenia są bardziej skomplikowane, dlatego zaleca się najpierw zacząć od publicznego modelu IP.

#### <a name="consortium-member-overview"></a>Omówienie składowej konsorcjum

Każde wdrożenie składowe konsorcjum obejmuje:

-   Virtual Machines do uruchamiania modułów walidacji PoA

-   Azure Load Balancer do dystrybuowania żądań usługi RPC, komunikacji równorzędnej i ładu DApp

-   Azure Key Vault zabezpieczania tożsamości modułu sprawdzania poprawności

-   Usługa Azure Storage do hostowania trwałych informacji o sieci i koordynowania dzierżawy

-   Azure Monitor agregowania dzienników i statystyk wydajności

-   Brama sieci wirtualnej (opcjonalnie) do zezwalania na połączenia VPN między prywatnym sieci wirtualnych

![Architektura wdrożenia](./media/ethereum-poa-deployment/deployment-architecture.png)

Korzystamy z kontenerów platformy Docker w celu zapewnienia niezawodności i modularności. Używamy Azure Container Registry, aby hostować i obsługiwać obrazy z wersjami w ramach każdego wdrożenia. Obrazy kontenerów składają się z:

-   Orchestrator

    -   Uruchamiany raz podczas wdrażania

    -   Generuje tożsamości i kontrakty ładu

    -   Przechowuje tożsamości w magazynie tożsamości

-   Klient z parzystością

    -   Dzierżawcy tożsamości z magazynu tożsamości

    -   Odnajduje i nawiązuje połączenie z elementami równorzędnymi

-   Agent EthStats

    -   Zbiera dzienniki lokalne i statystyki za pośrednictwem usługi RPC i wypchnięcia do Azure Monitor

-   DApp ładu

    -   Interfejs sieci Web do współpracy z kontraktami ładu

## <a name="how-to-guides"></a>Przewodniki z instrukcjami
### <a name="governance-dapp"></a>DApp ładu

W celu potwierdzenia uwierzytelnienia jest zdecentralizowane ładu. DApp ładu to zestaw wstępnie wdrożonych, [inteligentnych kontraktów](https://github.com/Azure-Samples/blockchain/tree/master/ethereum-on-azure/) i aplikacji sieci Web, które są używane do zarządzania władzami w sieci.
Urzędy są podzielone na tożsamości administratora i węzły modułu sprawdzania poprawności.
Administratorzy mogą delegować jednomyślne uczestnictwo w zestawie węzłów modułu sprawdzania poprawności. Administratorzy mogą również zagłosować innych administratorów do sieci lub z niej.

![dapp ładu](./media/ethereum-poa-deployment/governance-dapp.png)

-   **Zdecentralizowane zarządzanie —** Zmiany w urzędach sieciowych są zarządzane za pomocą głosowania w łańcuchu przez wybieranie administratorów.

-   **Delegowanie modułu weryfikacji —** Urzędy mogą zarządzać węzłami modułu sprawdzania poprawności skonfigurowanymi w każdym wdrożeniu PoA.

-   **Historia zmian podlegających inspekcji —** Każda zmiana jest rejestrowana w łańcucha bloków, zapewniając przejrzystość i inspekcję.

#### <a name="getting-started-with-governance"></a>Wprowadzenie do ładu
Aby wykonać dowolny rodzaj transakcji za pomocą DApp ładu, należy skorzystać z portfela Ethereum.  Najbardziej proste podejście polega na użyciu portfela w przeglądarce, takiego jak [Dbmasking](https://metamask.io); Jednak ponieważ są to inteligentne kontrakty wdrożone w sieci, możesz również zautomatyzować interakcje z kontraktem ładu.

Po zainstalowaniu maski, przejdź do ładu DApp w przeglądarce.  Adres URL można znaleźć w wiadomości e-mail z potwierdzeniem wdrożenia lub za pośrednictwem Azure Portal w danych wyjściowych wdrożenia.  Jeśli nie masz zainstalowanej portfela w przeglądarce, nie będziesz w stanie wykonać żadnych akcji; jednak nadal można odczytać stan administratora.  

#### <a name="becoming-an-admin"></a>Stać się administratorem
Jeśli jesteś pierwszym członkiem, który został wdrożony w sieci, zostanie automatycznie wyświetlony administrator, a węzły parzystości będą wyświetlane jako moduły walidacji.  Jeśli dołączysz do sieci, musisz otrzymać głos jako administratora z większością (ponad 50%) istniejącego zestawu administracyjnego.  Jeśli nie chcesz stać się administratorem, węzły nadal będą synchronizowane i weryfikują łańcucha bloków; nie będą jednak uczestniczyć w procesie tworzenia bloku. Aby rozpocząć proces głosowania jako administrator, kliknij pozycję __nominacja__ i wprowadź adres Ethereum oraz Alias.

![Nominowanie](./media/ethereum-poa-deployment/governance-dapp-nominate.png)

#### <a name="candidates"></a>Sugesti
Wybranie karty __kandydatów__ spowoduje wyświetlenie bieżącego zestawu administratorów kandydujących.  Gdy kandydat osiągnie większość głosów od bieżących administratorów, kandydat zostanie podwyższony do administratora.  Aby zagłosować na kandydata, zaznacz wiersz i kliknij pozycję "Zagłosuj" u góry.  Jeśli zmienisz zdanie w głosowaniu, możesz wybrać kandydata i kliknąć pozycję "odwołaj głos".

![Sugesti](./media/ethereum-poa-deployment/governance-dapp-candidates.png)


#### <a name="admins"></a>Administratorzy
Na karcie __administratorzy__ zostanie wyświetlony bieżący zestaw administratorów i będzie można głosować.  Gdy administrator utraci więcej niż 50% obsługi, zostanie usunięty jako administrator w sieci.  Wszystkie węzły modułu sprawdzania poprawności, które są własnością tego administratora, utracą stan modułu sprawdzania poprawności i staną się węzłami transakcji w sieci.  Administrator może zostać usunięty z dowolnej liczby powodów; jednak przede wszystkim konsorcjum wyraża zgodę na zasady z góry.

![Administratorzy](./media/ethereum-poa-deployment/governance-dapp-admins.png)

#### <a name="validators"></a>Modułów sprawdzania
Po wybraniu karty __moduły sprawdzania poprawności__ w menu po lewej stronie zostaną wyświetlone bieżące wdrożone węzły parzystości dla tego wystąpienia i ich bieżący stan (typ węzła).  Każdy członek konsorcjum będzie miał inny zestaw modułów sprawdzania poprawności na tej liście, ponieważ ten widok reprezentuje aktualnie wdrożony element członkowski konsorcjum.  Jeśli jest to nowo wdrożone wystąpienie i nie dodano jeszcze modułów sprawdzania poprawności, zostanie wyświetlona opcja "Dodaj moduły sprawdzania poprawności".  Wybranie tej opcji spowoduje automatyczne wybranie zestawu węzłów parzystości o zrównoważonym regionie i przypisanie ich do zestawu modułów walidacji.  Jeśli wdrożono więcej węzłów niż dozwolona pojemność, pozostałe węzły staną się węzłami transakcji w sieci.

Adres każdego modułu sprawdzania poprawności jest automatycznie przypisywany za pośrednictwem [magazynu tożsamości](#identity-store) na platformie Azure.  Jeśli węzeł ulegnie awarii, nastąpi jego tożsamość, co umożliwi przejście do innego węzła w ramach wdrożenia.  Dzięki temu konsensus uczestnictwo jest wysoce dostępny.

![Modułów sprawdzania](./media/ethereum-poa-deployment/governance-dapp-validators.png)

#### <a name="consortium-name"></a>Nazwa konsorcjum
Każdy administrator może zaktualizować nazwę konsorcjum, która jest wyświetlana w górnej części strony.  Wybierz ikonę koła zębatego w lewym górnym rogu, aby zaktualizować nazwę konsorcjum.

#### <a name="account-menu"></a>Menu konto
W prawym górnym rogu jest alias konta Ethereum i identicon.  Jeśli jesteś administratorem, będziesz mieć możliwość zaktualizowania aliasu.

![Konto](./media/ethereum-poa-deployment/governance-dapp-account.png)

### <a name="deploy-ethereum-proof-of-authority"></a>Wdróż Ethereum potwierdzenie urzędu certyfikacji

Oto przykład przepływu wdrożenia wielostronnego:

1.  Trzy składowe generują konto Ethereum przy użyciu funkcji dbmask

2.  *Składowa A* wdraża Ethereum POA, dostarczając swój publiczny adres Ethereum

3.  *Członek A* zawiera adres URL konsorcjum do *składowej B* i *elementu członkowskiego C*

4.  *Składowa B* i *element członkowski C* Deploy, Ethereum POA, dostarczając swój adres publiczny Ethereum i adres URL konsorcjum *elementu członkowskiego*

5.  *Składowa* głosów w *członku B* jako administrator

6.  *Członek a* i *członek B* *zarówno prawo* głosujące jako administrator

Ten proces wymaga subskrypcji platformy Azure, która może obsługiwać wdrażanie kilku maszyn wirtualnych i dysków zarządzanych. W razie potrzeby [Utwórz bezpłatne konto platformy Azure](https://azure.microsoft.com/free/) , aby rozpocząć.

Po zabezpieczeniu subskrypcji przejdź do Azure Portal. Wybierz pozycję "+", Marketplace (Zobacz wszystko) i Wyszukaj pozycję Ethereum PoA Consortium.

Poniższa sekcja zawiera opis konfigurowania zasięgu pierwszego elementu członkowskiego w sieci. Przepływ wdrożenia jest podzielony na pięć kroków: podstawowe, regiony wdrożenia, rozmiar sieci i wydajność, Ethereum ustawienia, Azure Monitor.

#### <a name="basics"></a>Podstawy

W obszarze **podstawowe**Określ wartości parametrów standardowych dla dowolnego wdrożenia, takie jak subskrypcja, Grupa zasobów i podstawowe właściwości maszyny wirtualnej.

Szczegółowy opis każdego z parametrów jest następujący:

Nazwa parametru|Opis|Dozwolone wartości|Wartości domyślne
---|---|---|---
Utworzyć nową sieć lub dołączyć do istniejącej sieci?|Utwórz nową sieć lub Dołącz do istniejącej sieci konsorcjum|Utwórz nowe sprzężenie istniejące|Create New (Utwórz nowe)
Adres e-mail (opcjonalnie)|Po zakończeniu wdrożenia z informacjami o danym wdrożeniu otrzymasz powiadomienie e-mail.|Prawidłowy adres e-mail|Nie dotyczy
Nazwa użytkownika maszyny wirtualnej|Nazwa użytkownika administratora dla każdej wdrożonej maszyny wirtualnej (tylko znaki alfanumeryczne)|1-64 znaków|Nie dotyczy
Typ uwierzytelniania|Metoda do uwierzytelnienia na maszynie wirtualnej.|Hasło lub klucz publiczny SSH|Hasło
Hasło (typ uwierzytelniania = hasło)|Hasło dla konta administratora dla każdej wdrożonej maszyny wirtualnej.  Hasło musi zawierać 3 z następujących: 1 wielkie litery, 1 małe litery, cyfry 1 i 1 znak specjalny. Gdy wszystkie maszyny wirtualne mają początkowo to samo hasło, można zmienić hasło po aprowizacji.|12-72 znaków|Nie dotyczy
Klucz SSH (typ uwierzytelniania = klucz publiczny)|Bezpieczny klucz powłoki używany do logowania zdalnego.||Nie dotyczy
Subskrypcja|Subskrypcja, do której ma zostać wdrożona sieć konsorcjum||Nie dotyczy
Grupa zasobów|Grupa zasobów, w której ma zostać wdrożona sieć konsorcjum.||Nie dotyczy
Lokalizacja|Region świadczenia usługi Azure dla grupy zasobów.||Nie dotyczy

Poniżej przedstawiono przykładowe wdrożenie: ![](./media/ethereum-poa-deployment/basic-blade.png) w warstwie Podstawowa

#### <a name="deployment-regions"></a>Regiony wdrożenia

Następnie w obszarze regiony wdrożenia Określ dane wejściowe dla liczby regionów w celu wdrożenia sieci konsorcjum i wyboru regionów świadczenia usługi Azure na podstawie liczby podanych regionów. Użytkownik może wdrożyć w maksymalnie 5 regionach. Zalecamy wybranie pierwszego regionu w celu dopasowania do lokalizacji grupy zasobów z sekcji podstawowe. W przypadku sieci programistycznych lub testowych zaleca się pojedynczy region na członka. W przypadku produkcji zalecamy wdrożenie w co najmniej dwóch regionach w celu zapewnienia wysokiej dostępności.

Szczegółowy opis każdego z parametrów jest następujący:

  Nazwa parametru|Opis|Dozwolone wartości|Wartości domyślne
  ---|---|---|---
  Liczba regionów|Liczba regionów do wdrożenia sieci konsorcjum|1, 2, 3, 4, 5|1
  Pierwszy region|Pierwszy region wdrożenia sieci konsorcjum|Wszystkie dozwolone regiony platformy Azure|Nie dotyczy
  Drugi region|Drugi region do wdrożenia sieci konsorcjum (widoczne tylko wtedy, gdy wybrano liczbę regionów jako 2)|Wszystkie dozwolone regiony platformy Azure|Nie dotyczy
  Trzeci region|Trzeci region do wdrożenia sieci konsorcjum (widoczne tylko wtedy, gdy wybrano liczbę regionów jako 3)|Wszystkie dozwolone regiony platformy Azure|Nie dotyczy
  Czwarty region|Czwarty region do wdrożenia sieci konsorcjum (widoczne tylko wtedy, gdy wybrano liczbę regionów jako 4)|Wszystkie dozwolone regiony platformy Azure|Nie dotyczy
  Piąty region|Piąty region do wdrożenia sieci konsorcjum (widoczne tylko wtedy, gdy wybrano liczbę regionów jako 5)|Wszystkie dozwolone regiony platformy Azure|Nie dotyczy

Poniżej przedstawiono przykładowe wdrożenie: ![regionów wdrożenia](./media/ethereum-poa-deployment/deployment-regions.png)

#### <a name="network-size-and-performance"></a>Rozmiar i wydajność sieci

Następnie w obszarze "rozmiar sieci i wydajność" Określ dane wejściowe rozmiaru sieci konsorcjum, takie jak liczba i rozmiar węzłów modułu sprawdzania poprawności.
Rozmiar magazynu węzła modułu sprawdzania poprawności będzie określać potencjalny rozmiar łańcucha bloków. Tę zmianę można zmienić po wdrożeniu.

Szczegółowy opis każdego z parametrów jest następujący:

  Nazwa parametru|Opis|Dozwolone wartości|Wartości domyślne
  ---|---|---|---
  Liczba węzłów modułu sprawdzania z równoważeniem obciążenia|Liczba węzłów modułu sprawdzania poprawności do aprowizacji jako część sieci|2-15|2
  Wydajność magazynu węzłów modułu sprawdzania poprawności|Typ dysku zarządzanego wykonującego kopię zapasową każdego ze wdrożonych węzłów modułu sprawdzania poprawności.|SSD w warstwie Standardowa lub Premium|Standardowa, SSD
  Rozmiar maszyny wirtualnej węzła modułu sprawdzania poprawności|Rozmiar maszyny wirtualnej używany dla węzłów modułu sprawdzania poprawności.|Standard A, standard D, standard D-v2, standardowa F Series, standard DS i Standard FS|Standardowa D1 v2

[Szczegóły cennika magazynu](https://azure.microsoft.com/pricing/details/managed-disks/)

[Szczegóły cennika maszyny wirtualnej](https://azure.microsoft.com/pricing/details/virtual-machines/windows/)

Warstwa maszyny wirtualnej i magazynu będzie miała wpływ na wydajność sieci.  Zalecamy użycie następujących jednostek SKU na podstawie pożądanych kosztów:

  Jednostka SKU maszyny wirtualnej|Warstwa magazynowania|Cena|Przepływność|Opóźnienie
  ---|---|---|---|---
  F1|Standardowa, SSD|małą|małą|wysokowydajn
  D2_v3|Standardowa, SSD|Średniookresow|Średniookresow|Średniookresow
  F16s|Premium, SSD|wysokowydajn|wysokowydajn|małą

Poniżej przedstawiono przykładowe wdrożenie: ![rozmiaru i wydajności sieci](./media/ethereum-poa-deployment/network-size-and-performance.png)

#### <a name="ethereum-settings"></a>Ustawienia Ethereum

Następnie w obszarze Ustawienia Ethereum Określ ustawienia konfiguracji związane z Ethereum, takie jak identyfikator sieci i hasło konta Ethereum lub Genesis.

Szczegółowy opis każdego z parametrów jest następujący:

  Nazwa parametru|Opis|Dozwolone wartości|Wartości domyślne
  ---|---|---|---
Identyfikator członka konsorcjum|Identyfikator skojarzony z każdym członkiem uczestniczącym w sieci konsorcjum używany do konfigurowania przestrzeni adresów IP w celu uniknięcia kolizji. W przypadku sieci prywatnej identyfikator elementu członkowskiego musi być unikatowy w różnych organizacjach w tej samej sieci.  Unikatowy identyfikator elementu członkowskiego jest wymagany nawet wtedy, gdy ta sama organizacja jest wdrażana w wielu regionach. Zanotuj wartość tego parametru, ponieważ musisz udostępnić go innym członkom, aby upewnić się, że nie ma kolizji.|0-255|Nie dotyczy
Identyfikator sieci|Identyfikator sieci dla wdrażanej sieci Ethereum.  Każda sieć Ethereum ma swój własny identyfikator sieci, a 1 to identyfikator sieci publicznej.|5 – 999 999 999|10101010
Adres Ethereum administratora|Adres konta usługi Ethereum, który jest używany przez uczestnictwo w ładu PoA.  Zalecamy użycie elementubinding do generowania adresu Ethereum.|42 znaków alfanumerycznych zaczynających się od 0x|Nie dotyczy
Opcje zaawansowane|Opcje zaawansowane dla ustawień Ethereum|Włącz lub Wyłącz|Wyłączanie
Publiczny adres IP (Opcje zaawansowane = Włącz)|Wdraża sieć za bramą sieci wirtualnej i usuwa dostęp do komunikacji równorzędnej. Jeśli ta opcja jest zaznaczona, wszyscy członkowie muszą używać bramy sieci wirtualnej, aby połączenie było zgodne.|Prywatna sieć wirtualna IP|Publiczny adres IP
Limit liczby zablokowanych gazów (Opcje zaawansowane = Włącz)|Limit gazu bloku wyjściowego sieci|Wszystkie wartości liczbowe|50000000
Okres ponownego pieczętowania bloku (s)|Częstotliwość tworzenia pustych bloków, gdy nie ma żadnych transakcji w sieci. Wyższa częstotliwość będzie miała szybszy wzrost, ale zwiększy się koszty magazynowania.|Wszystkie wartości liczbowe|15
Kontrakt uprawnień transakcji (Opcje zaawansowane = Włącz)|Kod bajtowy dla kontraktu uprawniającego do transakcji. Ogranicza wdrożenie i wykonywanie kontraktu inteligentnego do listy dozwolonych kont Ethereum.|Kod bajtowy kontraktu|Nie dotyczy

Poniżej przedstawiono przykładowe wdrożenie: ![ustawienia ethereum](./media/ethereum-poa-deployment/ethereum-settings.png)

#### <a name="monitoring"></a>Monitorowanie

Blok monitorowanie umożliwia skonfigurowanie Azure Monitor zasobów dzienników dla sieci. Agent monitorowania będzie zbierać przydatne metryki i dzienniki z sieci, co zapewnia możliwość szybkiego sprawdzania kondycji sieci lub problemów z debugowaniem.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

  Nazwa parametru|Opis|Dozwolone wartości|Wartości domyślne
  ---|---|---|---
Monitorowanie|Opcja włączania monitorowania|Włącz lub Wyłącz|Włączanie
Nawiązywanie połączenia z istniejącymi dziennikami Azure Monitor|Utwórz nowe wystąpienie dzienników Azure Monitor lub Przyłącz istniejące wystąpienie|Utwórz nową lub Dołącz do istniejącej|Tworzenie nowego elementu
Monitoruj lokalizację (Połącz z istniejącymi dziennikami Azure Monitor = Utwórz nowe)|Region, w którym zostanie wdrożone nowe wystąpienie dzienników Azure Monitor|Wszystkie Azure Monitor regiony dzienników|Nie dotyczy
Istniejący identyfikator obszaru roboczego usługi log Analytics (Połącz z istniejącymi dziennikami Azure Monitor = Dołącz istniejący)|Identyfikator obszaru roboczego istniejącego wystąpienia dzienników Azure Monitor||Nie dotyczy
Istniejący klucz podstawowy usługi log Analytics (łączenie z istniejącymi dziennikami Azure Monitor = Połącz istniejące)|Klucz podstawowy używany do nawiązywania połączenia z istniejącym wystąpieniem dzienników Azure Monitor||Nie dotyczy


Poniżej przedstawiono przykładowe wdrożenie: ![usługi Azure monitor](./media/ethereum-poa-deployment/azure-monitor.png)

#### <a name="summary"></a>Podsumowanie

Kliknij blok podsumowania, aby przejrzeć określone dane wejściowe i uruchomić podstawową weryfikację przed wdrożeniem. Przed wdrożeniem programu można pobrać szablon i parametry.

Przejrzyj warunki prawne i zachowania poufności informacji, a następnie kliknij pozycję "Kup", aby przeprowadzić wdrożenie. Jeśli wdrożenie obejmuje bramy sieci wirtualnej, wdrożenie zajmie od 45 do 50 minut.

#### <a name="post-deployment"></a>Po wdrożeniu

##### <a name="deployment-output"></a>Dane wyjściowe wdrożenia

Po zakończeniu wdrażania możesz uzyskać dostęp do wymaganych parametrów za pośrednictwem wiadomości e-mail z potwierdzeniem lub za pośrednictwem Azure Portal. W tych parametrach znajdziesz następujące parametry:

-   Punkt końcowy RPC Ethereum

-   Adres URL pulpitu nawigacyjnego ładu

-   Adres URL Azure Monitor

-   Adres URL danych

-   Identyfikator zasobu bramy sieci wirtualnej (opcjonalnie)

##### <a name="confirmation-email"></a>Wiadomość e-mail z potwierdzeniem

Jeśli podano adres e-mail ([sekcja podstawy](#basics)), na adres e-mail zostanie wysłana wiadomość e-mail zawierająca informacje wyjściowe wdrożenia.

![adres e-mail wdrożenia](./media/ethereum-poa-deployment/deployment-email.png)

##### <a name="portal"></a>Portal

Po pomyślnym zakończeniu wdrożenia i udostępnieniu wszystkich zasobów można wyświetlić parametry wyjściowe w grupie zasobów.

1.  Lokalizowanie grupy zasobów w portalu

2.  Przejdź do *wdrożeń*

3.  Wybierz pierwsze wdrożenie o takiej samej nazwie jak grupa zasobów

4.  Wybierz dane *wyjściowe*

### <a name="growing-the-consortium"></a>Rosnąco konsorcjum

Aby rozszerzyć konsorcjum, należy najpierw połączyć sieć fizyczną.
Przy użyciu publicznego wdrożenia opartego na protokole IP ten pierwszy krok jest bezproblemowy. W przypadku wdrażania za pośrednictwem sieci VPN zapoznaj się z sekcją [łączenie bramy sieci wirtualnej](#connecting-vnet-gateways) w celu nawiązania połączenia sieciowego w ramach wdrożenia nowego elementu członkowskiego.  Po zakończeniu wdrażania Użyj [DApp ładu](#governance-dapp) , aby zostać administratorem sieci.

#### <a name="new-member-deployment"></a>Wdrożenie nowego elementu członkowskiego

1.  Udostępnij następujące informacje z przyłączanym członkiem. Te informacje można znaleźć w wiadomości e-mail po wdrożeniu lub w danych wyjściowych wdrożenia portalu.

    -  Adres URL danych konsorcjum

    -  Liczba wdrożonych węzłów

    -  Identyfikator zasobu bramy sieci wirtualnej (w przypadku korzystania z sieci VPN)

2.  Element członkowski wdrożenia powinien korzystać z tego [samego rozwiązania](https://portal.azure.com/?pub_source=email&pub_status=success#create/microsoft-azure-blockchain.azure-blockchain-ethereumethereum-poa-consortium) podczas wdrażania ich obecności w sieci z następującymi kwestiami:

    -  Wybierz *sprzężenie istniejące*

    -  Wybierz tę samą liczbę węzłów modułu sprawdzania poprawności jako resztę elementów członkowskich w sieci, aby zapewnić rzetelną reprezentację

    -  Użyj tego samego adresu Ethereum, który został podany w poprzednim kroku

    -  Przekaż podany *adres URL danych konsorcjum* na karcie *Ustawienia Ethereum*

    -  Jeśli pozostała część sieci należy do sieci VPN, wybierz pozycję *prywatna sieć wirtualna* w sekcji Zaawansowane.

#### <a name="connecting-vnet-gateways"></a>Łączenie bram sieci wirtualnej

Ten krok można zignorować, jeśli wdrożono go przy użyciu domyślnych ustawień publicznego adresu IP. W przypadku sieci prywatnej różne elementy członkowskie są połączone za pośrednictwem połączeń bramy sieci wirtualnej. Zanim członek będzie mógł dołączyć do sieci i wyświetlić ruch związany z transakcjami, istniejący element członkowski musi wykonać ostateczną konfigurację na swojej bramie sieci VPN, aby zaakceptować połączenie. Oznacza to, że węzły Ethereum należące do elementu członkowskiego sprzęgania nie będą działać, dopóki nie zostanie nawiązane połączenie. Zaleca się utworzenie nadmiarowych połączeń sieciowych (siatkę) do konsorcjum, aby zmniejszyć szanse single point of failure.

Po wdrożeniu nowego elementu członkowskiego istniejący element członkowski musi zakończyć połączenie dwukierunkowe przez skonfigurowanie połączenia bramy sieci wirtualnej z nowym członkiem. Aby to osiągnąć, istniejący element członkowski będzie potrzebować:

1.  Identyfikator zasobu bramy sieci wirtualnej łączący się z członkiem (Zobacz dane wyjściowe wdrożenia)

2.  Współużytkowany klucz połączenia

Istniejący element członkowski musi uruchomić następujący skrypt programu PowerShell, aby zakończyć połączenie. Zalecamy używanie Azure Cloud Shell znajdujących się w prawym górnym pasku nawigacyjnym w portalu.

![Usługa Cloud Shell](./media/ethereum-poa-deployment/cloud-shell.png)

```Powershell
$MyGatewayResourceId = "<EXISTING_MEMBER_RESOURCEID>"
$OtherGatewayResourceId = "<NEW_MEMBER_RESOURCEID]"
$ConnectionName = "Leader2Member"
$SharedKey = "<NEW_MEMBER_KEY>"

## $myGatewayResourceId tells me what subscription I am in, what ResourceGroup and the VNetGatewayName
$splitValue = $MyGatewayResourceId.Split('/')
$MySubscriptionid = $splitValue[2]
$MyResourceGroup = $splitValue[4]
$MyGatewayName = $splitValue[8]

## $otherGatewayResourceid tells me what the subscription and VNet GatewayName are
$OtherGatewayName = $OtherGatewayResourceId.Split('/')[8]
$Subscription=Select-AzSubscription -SubscriptionId $MySubscriptionid

## create a PSVirtualNetworkGateway instance for the gateway I want to connect to
$OtherGateway=New-Object Microsoft.Azure.Commands.Network.Models.PSVirtualNetworkGateway
$OtherGateway.Name = $OtherGatewayName
$OtherGateway.Id = $OtherGatewayResourceId
$OtherGateway.GatewayType = "Vpn"
$OtherGateway.VpnType = "RouteBased"

## get a PSVirtualNetworkGateway instance for my gateway
$MyGateway = Get-AzVirtualNetworkGateway -Name $MyGatewayName -ResourceGroupName $MyResourceGroup

## create the connection
New-AzVirtualNetworkGatewayConnection -Name $ConnectionName -ResourceGroupName $MyResourceGroup -VirtualNetworkGateway1 $MyGateway -VirtualNetworkGateway2 $OtherGateway -Location $MyGateway.Location -ConnectionType Vnet2Vnet -SharedKey $SharedKey -EnableBgp $True
```

### <a name="service-monitoring"></a>Monitorowanie usługi

Portal Azure Monitor można znaleźć, korzystając z linku w wiadomości e-mail dotyczącej wdrożenia lub lokalizowania parametru w obszarze dane wyjściowe wdrożenia \[portalu\_usługi OMS\_adres URL\].

W portalu będzie najpierw wyświetlana statystyka sieci wysokiego poziomu i Omówienie węzła.

![Kategorie monitora](./media/ethereum-poa-deployment/monitor-categories.png)

Wybranie **węzła przegląd** spowoduje przekierowanie do portalu w celu wyświetlenia statystyk infrastruktury poszczególnych węzłów.

![Statystyka węzła](./media/ethereum-poa-deployment/node-stats.png)

Wybranie pozycji **Statystyka sieci** spowoduje przekierowanie do wyświetlania statystyk sieci Ethereum.

![Statystyka sieci](./media/ethereum-poa-deployment/network-stats.png)

#### <a name="sample-kusto-queries"></a>Przykładowe zapytania Kusto

Za tymi pulpitami nawigacyjnymi jest zestaw Queryable dzienników RAW. Możesz użyć tych dzienników nieprzetworzonych, aby dostosować pulpity nawigacyjne, zbadać błędy lub alerty dotyczące progu konfiguracji. Poniżej znajdziesz zestaw przykładowych zapytań, które mogą być uruchamiane w narzędziu do przeszukiwania dzienników:

##### <a name="lists-blocks-that-have-been-reported-by-more-than-one-validator-useful-to-help-find-chain-forks"></a>Wyświetla listę bloków, które zostały zgłoszone przez więcej niż jeden moduł sprawdzania poprawności. Przydatne do znajdowania rozwidleniów łańcucha.

```sql
MinedBlock_CL
| summarize DistinctMiners = dcount(BlockMiner_s) by BlockNumber_d, BlockMiner_s
| where DistinctMiners > 1
```

##### <a name="get-average-peer-count-for-a-specified-validator-node-averaged-over-5-minute-buckets"></a>Pobierz średnią liczbę elementów równorzędnych dla określonego węzła modułu sprawdzania poprawności średniej z 5 minut.

```sql
let PeerCountRegex = @"Syncing with peers: (\d+) active, (\d+) confirmed, (\d+)";
ParityLog_CL
| where Computer == "vl-devn3lgdm-reg1000001"
| project RawData, TimeGenerated
| where RawData matches regex PeerCountRegex
| extend ActivePeers = extract(PeerCountRegex, 1, RawData, typeof(int))
| summarize avg(ActivePeers) by bin(TimeGenerated, 5m)
```

### <a name="ssh-access"></a>Dostęp SSH

Ze względów bezpieczeństwa dostęp do portu SSH jest domyślnie odrzucony przez regułę zabezpieczeń grupy sieciowej. Aby uzyskać dostęp do wystąpień maszyn wirtualnych w sieci PoA, należy zmienić tę regułę na \"Zezwalaj na\"

1.  Rozpocznij w sekcji Omówienie wdrożonej grupy zasobów z Azure Portal.

    ![Omówienie protokołu SSH](./media/ethereum-poa-deployment/ssh-overview.png)

2.  Wybierz grupę zabezpieczeń sieci dla regionu maszyny wirtualnej, do której chcesz uzyskać dostęp.

    ![sieciowej grupy zabezpieczeń SSH](./media/ethereum-poa-deployment/ssh-nsg.png)

3.  Wybierz regułę\" \"Zezwalaj na używanie protokołu SSH

    ![SSH — Zezwalaj](./media/ethereum-poa-deployment/ssh-allow.png)

4.  Zmień\" akcji \"na Zezwalaj

    ![Zezwalaj na włączanie protokołu SSH](./media/ethereum-poa-deployment/ssh-enable-allow.png)

5.  Kliknij pozycję \"Zapisz\" (zastosowanie zmian może potrwać kilka minut)

Teraz można zdalnie łączyć się z maszynami wirtualnymi dla węzłów modułu sprawdzania poprawności za pośrednictwem protokołu SSH z podaną nazwą użytkownika administratora i hasła/klucza SSH.
Polecenie SSH do uruchomienia w celu uzyskania dostępu do pierwszego węzła modułu sprawdzania poprawności znajduje się w parametrze wyjściowym wdrożenia szablonu jako "\_SSH, aby\_pierwszy\_licencji\_zbiorczej\_węzeł REGION1" (dla przykładowego wdrożenia: SSH-p 4000 poaadmin\@leader4vb.eastus.cloudapp.azure.com). Aby przejść do dodatkowych węzłów transakcji, Zwiększ numer portu o jeden (na przykład pierwszy węzeł transakcji znajduje się na porcie 4000).

Jeśli wdrożono w więcej niż jednym regionie, należy zmienić powyższe polecenie na nazwę DNS lub adres IP modułu równoważenia obciążenia w tym regionie. Aby znaleźć nazwę DNS lub adres IP innych regionów, Znajdź zasób z konwencją nazewnictwa \*\*\*\*\*-lbpip-reg\#i Wyświetl nazwę DNS i właściwości adresu IP.

### <a name="azure-traffic-manager-load-balancing"></a>Równoważenie obciążenia Traffic Manager platformy Azure

Usługa Azure Traffic Manager może pomóc w ograniczeniu przestoju i skrócić czas odpowiedzi sieci PoA przez kierowanie ruchu przychodzącego do wielu wdrożeń w różnych regionach. Wbudowane funkcje kontroli kondycji i automatycznego ponownego routingu pomagają zapewnić wysoką dostępność punktów końcowych RPC i zarządzanie DApp. Ta funkcja jest przydatna, jeśli wdrożono w wielu regionach i są gotowe do produkcji.

Zastosowania usługi Traffic Manager:

-   Zwiększ dostępność sieci PoA przy użyciu automatycznej pracy awaryjnej.

-   Zwiększaj czas odpowiedzi sieci przez kierowanie użytkowników końcowych do lokalizacji platformy Azure z najniższym opóźnieniem sieci.

Jeśli zdecydujesz się utworzyć profil Traffic Manager, możesz użyć nazwy DNS profilu, aby uzyskać dostęp do sieci. Po dodaniu innych członków konsorcjum do sieci Traffic Manager może również służyć do równoważenia obciążenia w ramach wdrożonych modułów walidacji.

#### <a name="creating-a-traffic-manager-profile"></a>Tworzenie profilu Traffic Manager

Wyszukaj i wybierz \"profilu Traffic Manager\" po kliknięciu przycisku \"Utwórz zasób\" w Azure Portal.

![Wyszukaj w usłudze Azure Traffic Manager](./media/ethereum-poa-deployment/traffic-manager-search.png)

Nadaj profilowi unikatową nazwę i wybierz grupę zasobów, która została utworzona podczas wdrażania PoA. Kliknij przycisk Utwórz, aby wdrożyć.

![Tworzenie Menedżera ruchu](./media/ethereum-poa-deployment/traffic-manager-create.png)

Po jego wdrożeniu wybierz wystąpienie w grupie zasobów. Nazwę DNS, aby uzyskać dostęp do usługi Traffic Manager, można znaleźć na karcie Przegląd

![Lokalizowanie usługi Traffic Manager DNS](./media/ethereum-poa-deployment/traffic-manager-dns.png)

Wybierz kartę punkty końcowe i kliknij przycisk Dodaj. Określ unikatową nazwę punktu końcowego. Zmień docelowy typ zasobu na publiczny adres IP. Następnie wybierz publiczny adres IP pierwszego regionu\'usługi równoważenia obciążenia.

![Routing Traffic Manager](./media/ethereum-poa-deployment/traffic-manager-routing.png)

Powtórz te czynności dla każdego regionu w wdrożonej sieci. Gdy punkty końcowe zostaną włączone \"stanie\", zostaną automatycznie załadowane i są bilansowane według nazwy DNS usługi Traffic Manager. Teraz można użyć tej nazwy DNS zamiast\_\[\_danych programu CONSORTIUM,\] parametr w innych krokach dokumentu.

### <a name="data-api"></a>Interfejs API danych

Każdy członek konsorcjum hostuje informacje niezbędne do nawiązania połączenia z siecią przez inne osoby. Istniejący element członkowski dostarczy [CONSORTIUM_DATA_URL] przed wdrożeniem elementu członkowskiego. Po wdrożeniu członek łączący będzie pobierał informacje z interfejsu JSON w następującym punkcie końcowym:

`<CONSORTIUM_DATA_URL>/networkinfo`

Odpowiedź będzie zawierać informacje przydatne do przyłączania elementów członkowskich (Genesis Block, walidator Set Contract ABI, bootnodes) i informacje przydatne dla istniejącego elementu członkowskiego (adresy walidatora). Zachęcamy do korzystania z tej standaryzacji w celu poszerzenia konsorcjum między dostawcami chmury. Ten interfejs API zwróci odpowiedź sformatowaną w formacie JSON o następującej strukturze:
```json
{
  "$id": "",
  "type": "object",
  "definitions": {},
  "$schema": "https://json-schema.org/draft-07/schema#",
  "properties": {
    "majorVersion": {
      "$id": "/properties/majorVersion",
      "type": "integer",
      "title": "This schema’s major version",
      "default": 0,
      "examples": [
        0
      ]
    },
    "minorVersion": {
      "$id": "/properties/minorVersion",
      "type": "integer",
      "title": "This schema’s minor version",
      "default": 0,
      "examples": [
        0
      ]
    },
    "bootnodes": {
      "$id": "/properties/bootnodes",
      "type": "array",
      "items": {
        "$id": "/properties/bootnodes/items",
        "type": "string",
        "title": "This member’s bootnodes",
        "default": "",
        "examples": [
          "enode://a348586f0fb0516c19de75bf54ca930a08f1594b7202020810b72c5f8d90635189d72d8b96f306f08761d576836a6bfce112cfb6ae6a3330588260f79a3d0ecb@10.1.17.5:30300",
          "enode://2d8474289af0bb38e3600a7a481734b2ab19d4eaf719f698fe885fb239f5d33faf217a860b170e2763b67c2f18d91c41272de37ac67386f80d1de57a3d58ddf2@10.1.17.4:30300"
        ]
      }
    },
    "valSetContract": {
      "$id": "/properties/valSetContract",
      "type": "string",
      "title": "The ValidatorSet Contract Source",
      "default": "",
      "examples": [
        "pragma solidity 0.4.21;\n\nimport \"./SafeMath.sol\";\nimport \"./Utils.sol\";\n\ncontract ValidatorSet …"
      ]
    },
    "adminContract": {
      "$id": "/properties/adminContract",
      "type": "string",
      "title": "The AdminSet Contract Source",
      "default": "",
      "examples": [
        "pragma solidity 0.4.21;\nimport \"./SafeMath.sol\";\nimport \"./SimpleValidatorSet.sol\";\nimport \"./Admin.sol\";\n\ncontract AdminValidatorSet is SimpleValidatorSet { …"
      ]
    },
    "adminContractABI": {
      "$id": "/properties/adminContractABI",
      "type": "string",
      "title": "The Admin Contract ABI",
      "default": "",
      "examples": [
        "[{\"constant\":false,\"inputs\":[{\"name\":\"proposedAdminAddress\",\"type\":\"address\"},…"
      ]
    },
    "paritySpec": {
      "$id": "/properties/paritySpec",
      "type": "string",
      "title": "The Parity client spec file",
      "default": "",
      "examples": [
        "\n{\n \"name\": \"PoA\",\n \"engine\": {\n \"authorityRound\": {\n \"params\": {\n \"stepDuration\": \"2\",\n \"validators\" : {\n \"safeContract\": \"0x0000000000000000000000000000000000000006\"\n },\n \"gasLimitBoundDivisor\": \"0x400\",\n \"maximumExtraDataSize\": \"0x2A\",\n \"minGasLimit\": \"0x2FAF080\",\n \"networkID\" : \"0x9a2112\"\n }\n }\n },\n \"params\": {\n \"gasLimitBoundDivisor\": \"0x400\",\n \"maximumExtraDataSize\": \"0x2A\",\n \"minGasLimit\": \"0x2FAF080\",\n \"networkID\" : \"0x9a2112\",\n \"wasmActivationTransition\": \"0x0\"\n },\n \"genesis\": {\n \"seal\": {\n \"authorityRound\": {\n \"step\": \"0x0\",\n \"signature\": \"0x0000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000\"\n }\n },\n \"difficulty\": \"0x20000\",\n \"gasLimit\": \"0x2FAF080\"\n },\n \"accounts\": {\n \"0x0000000000000000000000000000000000000001\": { \"balance\": \"1\", \"builtin\": { \"name\": \"ecrecover\", \"pricing\": { \"linear\": { \"base\": 3000, \"word\": 0 } } } },\n \"0x0000000000000000000000000000000000000002\": { \"balance\": \"1\", \"builtin\": { \"name\": \"sha256\", \"pricing\": { \"linear\": { \"base\": 60, \"word\": 12 } } } },\n \"0x0000000000000000000000000000000000000003\": { \"balance\": \"1\", \"builtin\": { \"name\": \"ripemd160\", \"pricing\": { \"linear\": { \"base\": 600, \"word\": 120 } } } },\n \"0x0000000000000000000000000000000000000004\": { \"balance\": \"1\", \"builtin\": { \"name\": \"identity\", \"pricing\": { \"linear\": { \"base\": 15, \"word\": 3 } } } },\n \"0x0000000000000000000000000000000000000006\": { \"balance\": \"0\", \"constructor\" : \"…\" }\n }\n}"
      ]
    },
    "errorMessage": {
      "$id": "/properties/errorMessage",
      "type": "string",
      "title": "Error message",
      "default": "",
      "examples": [
        ""
      ]
    },
    "addressList": {
      "$id": "/properties/addressList",
      "type": "object",
      "properties": {
        "addresses": {
          "$id": "/properties/addressList/properties/addresses",
          "type": "array",
          "items": {
            "$id": "/properties/addressList/properties/addresses/items",
            "type": "string",
            "title": "This member’s validator addresses",
            "default": "",
            "examples": [
              "0x00a3cff0dccc0ecb6ae0461045e0e467cff4805f",
              "0x009ce13a7b2532cbd89b2d28cecd75f7cc8c0727"
            ]
          }
        }
      }
    }
  }
}

```
## <a name="tutorials"></a>Samouczki

### <a name="programmatically-interacting-with-a-smart-contract"></a>Programistyczne współpracujące z kontraktem inteligentnym

> [!WARNING]
> Nigdy nie wysyłaj klucza prywatnego Ethereum przez sieć! Upewnij się, że każda transakcja jest podpisana lokalnie, a podpisana transakcja jest wysyłana za pośrednictwem sieci.

W poniższym przykładzie używamy *ethereumjs-portfel* do wygenerowania adresu Ethereum, *ethereumjs-TX* do lokalnego logowania, a *web3* do wysłania pierwotnej transakcji do punktu końcowego RPC Ethereum.

Na potrzeby tego przykładu będziemy używać prostego kontraktu usługi Hello World dla świata:

```javascript
pragma solidity ^0.4.11;
contract postBox {
    string message;
    function postMsg(string text) public {
        message = text;
    }
    function getMsg() public view returns (string) {
        return message;
    }
}
```

W tym przykładzie przyjęto założenie, że kontrakt został już wdrożony. Do wdrożenia kontraktu można użyć *solc* i *web3* . Najpierw zainstaluj następujące moduły węzła:
```
sudo npm install web3@0.20.2
sudo npm install ethereumjs-tx@1.3.6
sudo npm install ethereumjs-wallet@0.6.1
```
Ten skrypt nodeJS wykona następujące czynności:

-   Konstruowanie nieprzetworzonej transakcji: postMsg

-   Podpisz transakcję przy użyciu wygenerowanego klucza prywatnego

-   Prześlij podpisaną transakcję do sieci Ethereum

```javascript
var ethereumjs = require('ethereumjs-tx')
var wallet = require('ethereumjs-wallet')
var Web3 = require('web3')

// TODO Replace with your contract address
var address = "0xfe53559f5f7a77125039a993e8d5d9c2901edc58";
var abi = [{"constant": false,"inputs": [{"name": "text","type": "string"}],"name": "postMsg","outputs": [],"payable": false,"stateMutability": "nonpayable","type": "function"},{"constant": true,"inputs": [],"name": "getMsg","outputs": [{"name": "","type": "string"}],"payable": false,"stateMutability": "view","type": "function"}];

// Generate a new Ethereum account
var account = wallet.generate();
var accountAddress = account.getAddressString()
var privateKey = account.getPrivateKey();

// TODO Replace with your RPC endpoint
var web3 = new Web3(new Web3.providers.HttpProvider(
    "http://testzvdky-dns-reg1.eastus.cloudapp.azure.com:8545"));

// Get the current nonce of the account
web3.eth.getTransactionCount(accountAddress, function (err, nonce) {
   var data = web3.eth.contract(abi).at(address).postMsg.getData("Hello World");
   var rawTx = {
     nonce: nonce,
     gasPrice: '0x00',
     gasLimit: '0x2FAF080',
     to: address,
     value: '0x00',
     data: data
   }
   var tx = new ethereumjs(rawTx);

   tx.sign(privateKey);

   var raw = '0x' + tx.serialize().toString('hex');
   web3.eth.sendRawTransaction(raw, function (txErr, transactionHash) {
     console.log("TX Hash: " + transactionHash);
     console.log("Error: " + txErr);
   });
 });
```

### <a name="deploy-smart-contract-with-truffle"></a>Wdrażanie inteligentnego kontraktu z Truffle

-   Instalowanie niezbędnych bibliotek

```javascript
npm init

npm install truffle-hdwallet-provider --save
```
-   W Truffle. js Dodaj następujący kod, aby odblokować konto dbmaskę i skonfigurować węzeł PoA jako punkt wejścia, dostarczając frazę "symbol" (Maska/ustawienia/ujawnienie wyrazów)

```javascript
var HDWalletProvider = require("truffle-hdwallet-provider");

var rpc_endpoint = "XXXXXX";
var mnemonic = "twelve words you can find in metamask/settings/reveal seed words";

module.exports = {
  networks: {
    development: {
      host: "localhost",
      port: 8545,
      network_id: "*" // Match any network id
    },
    poa: {
      provider: new HDWalletProvider(mnemonic, rpc_endpoint),
      network_id: 3,
      gasPrice : 0
    }
  }
};

```

-   Wdróż w sieci PoA

```javascript
$ truffle migrate --network poa
```

### <a name="debug-smart-contract-with-truffle"></a>Debuguj kontrakt inteligentny przy użyciu Truffle

Truffle ma lokalną sieć projektową, która jest dostępna do debugowania kontraktu inteligentnego. Pełny samouczek można znaleźć [tutaj](https://truffleframework.com/tutorials/debugging-a-smart-contract).

### <a name="webassembly-wasm-support"></a>Obsługa zestawu webassembly (WASM)

Obsługa zestawu webassembly jest już włączona dla nowo wdrożonych sieci PoA. Umożliwia ona programowanie aplikacji inteligentnych w dowolnym języku, który transstertuje zestaw sieci Web (Rust, C, C++). Zobacz poniższe linki, aby uzyskać dodatkowe informacje

-   Przegląd parzystości zestawu webassembly-<https://wiki.parity.io/WebAssembly-Home>

-   Samouczek techniczny <https://github.com/paritytech/pwasm-tutorial> z parzystością

## <a name="reference"></a>Dokumentacja

### <a name="faq"></a>Często zadawane pytania

#### <a name="i-notice-there-are-many-transactions-on-the-network-that-i-didnt-send-where-are-these-coming-from"></a>Zauważ, że w sieci jest wiele transakcji, które są niezgodne\'t Send. Skąd pochodzą?

Odblokowanie [osobistego interfejsu API](https://web3js.readthedocs.io/en/v1.2.0/web3-eth-personal.html)jest niebezpieczne. Botów nasłuchuje odblokowanych kont Ethereum i próbuje opróżnić środki. Bot zakłada, że te konta zawierają rzeczywiste i próbują być pierwszym, aby Siphon saldo. Nie należy włączać osobistego interfejsu API w sieci. Zamiast tego Przedstaw transakcje ręcznie przy użyciu portfela, takiego jak dbmaskowanie, lub programowo, jak opisano w sekcji [programistyczne manipulowanie przy użyciu kontraktu inteligentnego](#programmatically-interacting-with-a-smart-contract).

#### <a name="how-to-ssh-onto-a-vm"></a>Jak SSH na maszynę wirtualną?

Port SSH nie jest narażony ze względów bezpieczeństwa. Postępuj zgodnie z [tym przewodnikiem, aby włączyć port SSH](#ssh-access).

#### <a name="how-do-i-set-up-an-audit-member-or-transaction-nodes"></a>Jak mogę skonfigurować członka lub węzły transakcji inspekcji?

Węzły transakcji to zbiór klientów z parzystością, które są połączone z siecią, ale nie uczestniczą w konsensusie. Te węzły mogą nadal służyć do przesyłania transakcji Ethereum i odczytywania stanu kontraktu inteligentnego.
Jest to również mechanizm zapewniający możliwość przeprowadzenia inspekcji do członków konsorcjum bez urzędu certyfikacji w sieci. Aby to osiągnąć, należy wykonać krok 2 od wzrostu konsorcjum.

#### <a name="why-are-metamask-transactions-taking-a-long-time"></a>Dlaczego transakcje związane z maską są długotrwałe?

Aby zapewnić, że transakcje są odbierane w odpowiedniej kolejności, każda transakcja Ethereum ma przyrostowy identyfikator jednorazowy. Jeśli w innej sieci użyto konta w usłudze maskującej, należy zresetować wartość nonce. Kliknij ikonę ustawień (3-paskowe), ustawienia, zresetuj konto. Historia transakcji zostanie wyczyszczona, a teraz można ponownie przesłać transakcję.

#### <a name="do-i-need-to-specify-gas-fee-in-metamask"></a>Czy muszę określić opłatę za gaz w ramach maskowania?

Eter nie jest celem w przypadku weryfikacji z urzędu certyfikacji. W związku z tym nie ma potrzeby określania opłaty za gaz podczas przesyłania transakcji w ramach maski.

#### <a name="what-should-i-do-if-my-deployment-fails-due-to-failure-to-provision-azure-oms"></a>Co należy zrobić, jeśli moje wdrożenie nie powiedzie się z powodu niepowodzenia aprowizacji pakietu OMS platformy Azure?

Monitorowanie jest funkcją opcjonalną. W niektórych rzadkich przypadkach, gdy wdrożenie nie powiedzie się z powodu niemożności pomyślnego udostępnienia Azure Monitor zasobów, można je wdrożyć ponownie bez Azure Monitor.

#### <a name="are-public-ip-deployments-compatible-with-private-network-deployments"></a>Czy publiczne wdrożenia adresów IP są zgodne z wdrożeniami w sieci prywatnej?

Nie, Komunikacja równorzędna wymaga komunikacji dwukierunkowej, dlatego cała sieć musi być publiczna lub prywatna.

#### <a name="what-is-the-expected-transaction-throughput-of-proof-of-authority"></a>Jaka jest oczekiwana przepływność transakcji dla potwierdzenia urzędu?

Przepływność transakcji będzie wysoce zależna od typów transakcji i topologii sieci.  Przy użyciu prostych transakcji firma Microsoft testuje średnio 400 transakcji na sekundę z siecią wdrożoną w wielu regionach.

#### <a name="how-do-i-subscribe-to-smart-contract-events"></a>Jak mogę subskrybować zdarzenia dotyczące kontraktu inteligentnego?

Ethereum potwierdzenie urzędu obsługuje teraz sieci Web-Sockets.  Sprawdź swój adres e-mail wdrożenia lub dane wyjściowe wdrożenia, aby znaleźć adres URL i port gniazda sieci Web.

## <a name="next-steps"></a>Następne kroki

Rozpocznij pracę, korzystając z rozwiązania [Ethereum-of-Authority Consortium](https://portal.azure.com/?pub_source=email&pub_status=success#create/microsoft-azure-blockchain.azure-blockchain-ethereumethereum-poa-consortium) .
