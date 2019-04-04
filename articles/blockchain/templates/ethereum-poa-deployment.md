---
title: Dowód uwierzytelniania Ethereum konsorcjum - Azure
description: Korzystanie z odpowiedniego rozwiązania konsorcjum dowód uwierzytelniania Ethereum wdrażania i konfigurowania sieci Ethereum konsorcjum zawierającym wiele elementów członkowskich
services: azure-blockchain
keywords: ''
author: CodyBorn
ms.author: coborn
ms.date: 8/2/2018
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: brendal
manager: vamelech
ms.openlocfilehash: 5a693fef2f77471f799bec46f149ff19d6edca80
ms.sourcegitcommit: 9f4eb5a3758f8a1a6a58c33c2806fa2986f702cb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/03/2019
ms.locfileid: "58905923"
---
# <a name="ethereum-proof-of-authority-consortium"></a>Konsorcjum dowód uwierzytelniania Ethereum

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="overview"></a>Przegląd
[To rozwiązanie](https://portal.azure.com/?pub_source=email&pub_status=success#create/microsoft-azure-blockchain.azure-blockchain-ethereumethereum-poa-consortium) zaprojektowano, aby ułatwić wdrażanie, konfigurowanie i zarządzanie sieci konsorcjum zawierającym wiele elementów członkowskich dowód uwierzytelniania Ethereum przy minimalnej znajomości platformy Azure i Ethereum.

Za pomocą kliku dane wejściowe użytkownika i wdrożenia jednym kliknięciem w witrynie Azure portal każdy element członkowski można udostępnić zużycie sieci, za pomocą programu Microsoft Azure Compute, sieci i usługi storage na całym świecie. Każdy element członkowski sieciowych zużycia składa się z zestawu węzłów modułu sprawdzania poprawności z równoważeniem obciążenia za pomocą której aplikacji lub użytkownika mogą wchodzić w interakcje przesłać Ethereum transakcji.

## <a name="concepts"></a>Pojęcia

### <a name="terminology"></a>Terminologia

-   **Consensus** -czynność synchronizowanie danych między rozproszonej sieci za pośrednictwem weryfikacji blok i tworzenia.

-   **Element członkowski konsorcjum** — jednostka, która uczestniczy w consensus na sieć łańcucha bloków.

-   **Administrator** -Ethereum konto używane do zarządzania uczestnictwa konsorcjum danego członka.

-   **Moduł sprawdzania poprawności** -maszynę skojarzonych z kontem Ethereum, która uczestniczy w consensus w imieniu administratora.

### <a name="proof-of-authority"></a>Dowód uwierzytelniania

Dla osób, które są nowe w społeczności łańcucha bloków wydanie to rozwiązanie jest znakomita szansa, aby dowiedzieć się więcej o technologii w sposób łatwy i można je konfigurować na platformie Azure. Dowód pracy to mechanizm Sybil odporności, który wykorzystuje koszty obliczeń własnym regulacji sieci i zezwolić na udział podejście. Działa to świetnie w sieciach anonimowe, otwórz łańcucha bloków, gdzie konkurencję w odniesieniu do cryptocurrency podwyższa poziom zabezpieczeń w sieci. Jednak w sieciach prywatnych/konsorcjum Ether podstawowej nie ma wartości. Alternatywne protokołu, dowód uwierzytelniania jest bardziej odpowiednia dla dozwolonych sieci, gdzie wszyscy uczestnicy consensus są znane i wiarygodnych. Bez konieczności wyszukiwania dowód uwierzytelniania jest bardziej wydajne przy jednoczesnym zachowaniu Byzantine odporności na uszkodzenia.

### <a name="consortium-governance"></a>Konsorcjum nadzoru

Ponieważ dowód uwierzytelniania opiera się na listę dozwolonych urzędy sieci, aby zachować sieci zdrowe, ważne jest zapewnienie mechanizmu uczciwe w celu wprowadzenia zmian do tej listy uprawnień. Każde wdrożenie jest powiązana z zestawu kontraktów inteligentnych i portal o łańcuch nadzoru tej listy dozwolonych. Po proponowana zmiana osiągnie większością głosów przez konsorcjum członków, jest wprowadzone zmiany. Dzięki temu nowych uczestników consensus być dodane lub naruszony uczestników do usunięcia w przejrzysty sposób, który zachęca uczciwe sieci.

### <a name="admin-account"></a>Konto administratora

Podczas wdrażania węzłów dowód uwierzytelniania użytkownik zostanie zapytany o adres Ethereum administratora. Można użyć kilku różnych mechanizmów do generowania i zabezpieczyć konto Ethereum. Po dodaniu tego adresu jako urząd w sieci, można użyć tego konta do wzięcia udziału w nadzoru. To konto administratora zostanie również delegować uczestnictwa consensus węzłów modułu sprawdzania poprawności, które są tworzone w ramach tego wdrożenia. Ponieważ jest używany tylko publiczny adres Ethereum, poszczególni Administratorzy na tyle elastyczna, aby zabezpieczyć swoje klucze prywatne w sposób, który następuje po ich model zabezpieczeń odpowiedniej.

### <a name="validator-node"></a>Węzeł weryfikacji

W protokole dowód uwierzytelniania modułu sprawdzania poprawności węzłów zastępujący węzłami analizatora tradycyjnych. Każdy moduł sprawdzania poprawności ma unikatową tożsamość Ethereum, która pobiera dodany do listy uprawnień inteligentnych kontraktu. Po weryfikacji na tej liście mogą brać udział w procesie tworzenia bloku. Aby dowiedzieć się więcej o tym procesie, zobacz dokumentację firmy parzystości [urzędu Round consensus](https://wiki.parity.io/Aura). Każdy element członkowski konsorcjum można udostępnić co najmniej dwa węzły modułu sprawdzania poprawności w pięciu regionach nadmiarowości geograficznej. Moduł sprawdzania poprawności węzły komunikować się z innymi węzłami modułu sprawdzania poprawności na osiągnięcie na stan bazowego rejestru rozproszonego konsensu.
W celu zapewnienia uczciwe uczestnictwa w sieci, każdy element członkowski konsorcjum jest używać tychże więcej modułów weryfikacji od pierwszego elementu członkowskiego w sieci (jeśli pierwszego elementu członkowskiego wdraża trzy modułów sprawdzania poprawności, każdy element członkowski może mieć tylko trzy moduły weryfikacji).

### <a name="identity-store"></a>Magazyn tożsamości

Ponieważ każdy element członkowski ma wiele węzłów modułu sprawdzania poprawności działających jednocześnie, a każdy węzeł musi mieć dozwolonych tożsamości, ważne jest, moduły weryfikacji mogą bezpiecznie uzyskiwać unikatową tożsamość aktywne w sieci. Aby to ułatwić, utworzyliśmy Store tożsamości, które są wdrażane w subskrypcji każdego elementu członkowskiego, która bezpiecznie przechowuje tożsamości wygenerowanego Ethereum. Po wdrożeniu kontenera aranżacji wygeneruje Ethereum klucz prywatny dla każdego modułu weryfikacji i zapisz go w usłudze Azure Key Vault. Zanim uruchamiania węzła parzystości, najpierw uzyska dzierżawę na nieużywanych tożsamość, aby upewnić się, że tożsamość nie są pobierane przez inny węzeł. Tożsamość jest udostępniane na klienta, który nadaje jej uprawnienia, aby rozpocząć tworzenie bloków. Jeśli hostowania maszyny Wirtualnej ulegnie awarii, dzierżawy tożsamości zostaną wydane, dzięki czemu węzeł zastępczy, aby wznowić swoją tożsamość w przyszłości.

### <a name="bootnode-registrar"></a>Rejestrator Bootnode

Aby włączyć ułatwienia łączności, każdy element członkowski będzie obsługiwać zestaw informacji o połączeniu w [danych punktu końcowego interfejsu API](#data-api). Te dane zawierają listę bootnodes, które zostały udostępnione jako węzły komunikacji równorzędnej dla przyłączany elementu członkowskiego. W ramach tych danych interfejsu API przechowujemy tej listy bootnode aktualne

### <a name="bring-your-own-operator"></a>Przenieś swoje własne — operator

Często członka konsorcjum chcesz uczestniczyć w sieci zarządzania, ale nie chcesz obsługi i konserwacji infrastruktury. W przeciwieństwie do tradycyjnych systemów o jednym operator między działania sieci w modelu zdecentralizowanego systemów łańcucha bloków. Nie zatrudniać scentralizowane pośrednika działanie sieci, każdy element członkowski konsorcjum delegować zarządzanie infrastrukturą operatorowi atakującego. Dzięki temu z hybrydowego modelu, w której każdy element członkowski można wybrać do działania własnej infrastruktury lub delegowanie operacji do innego partnera. Operacja delegowanego przepływu pracy działa w następujący sposób:

1.  **Element członkowski konsorcjum** generuje adres Ethereum (przechowuje klucz prywatny)

2.  **Element członkowski konsorcjum** udostępnia publiczny adres Ethereum **— Operator**

3.  **Operator** wdraża i konfiguruje PoA modułu sprawdzania poprawności węzłów przy użyciu naszego rozwiązania usługi Azure Resource Manager

4.  **Operator** udostępnia punkt końcowy zarządzania i usługi RPC do **konsorcjum elementu członkowskiego**

5.  **Element członkowski konsorcjum** używa własnego klucza prywatnego do podpisywania żądania, akceptując węzłów modułu sprawdzania poprawności **Operator** została wdrożona do wzięcia udziału w ich imieniu

### <a name="azure-monitor"></a>Azure Monitor

To rozwiązanie również jest dostarczany z usługą Azure Monitor do śledzenia statystyki węźle i sieci. Dla deweloperów aplikacji zapewnia widoczność łańcucha bloków podstawowych do śledzenia statystyki generowania bloku. Operatorzy sieci można użyć usługi Azure Monitor do szybkiego wykrywania i zapobiegania awarii sieci za pośrednictwem infrastruktury statystyki i dzienniki z obsługą zapytań. Aby uzyskać więcej informacji, zobacz [monitorowanie usług](#service-monitoring).

### <a name="deployment-architecture"></a>Architektura wdrożenia

#### <a name="description"></a>Opis

To rozwiązanie można wdrożyć jeden lub wiele regionów na podstawie sieci konsorcjum Ethereum zawierającym wiele elementów członkowskich. Domyślnie RPC i komunikacji równorzędnej punkty końcowe są dostępne za pośrednictwem publicznego adresu IP w celu umożliwienia uproszczone łączności dla subskrypcji i chmury. Firma Microsoft zaleca korzystanie z [kontraktów udzielania do nich uprawnień firmy parzystości](https://wiki.parity.io/Permissioning) dla aplikacji z poziomu kontroli dostępu. Obsługujemy również sieci wdrożony za sieci VPN, które korzystać z bramy sieci wirtualnej dla łączności między subskrypcjami. Te wdrożenia są bardziej złożone, dlatego zaleca się najpierw uruchomić przy użyciu modelu publicznego adresu IP.

#### <a name="consortium-member-overview"></a>Omówienie elementu członkowskiego konsorcjum

Dla każdego wdrożenia elementu członkowskiego konsorcjum obejmuje:

-   Maszyny wirtualne na potrzeby uruchamiania PoA modułów sprawdzania poprawności

-   Usługa Azure Load Balancer dla dystrybucji RPC, komunikacji równorzędnej i nadzoru DApp żądań

-   Usługa Azure Key Vault do zabezpieczania tożsamości modułu sprawdzania poprawności

-   Usługa Azure Storage dla hostingu informacje o trwałych sieci i koordynowania działań dzierżawy

-   Usługa Azure Monitor do agregowania dzienników i statystyk wydajności

-   (Opcjonalnie) bramy sieci wirtualnej dla zezwala na połączenia sieci VPN między sieciami wirtualnymi w prywatnych

![Architektura wdrożenia](./media/ethereum-poa-deployment/deployment-architecture.png)

Będziemy korzystać z kontenerów platformy Docker w zakresie niezawodności i znikomym zakresie. Używamy usługi Azure Container Registry do hosta, a następnie przekazuje obrazy numerów wersji jako część każdego wdrożenia. Obrazy kontenera składają się:

-   Orchestrator

    -   Przebiegi raz podczas wdrażania

    -   Generuje tożsamości i nadzór nad umów

    -   Magazyny tożsamości w Store tożsamości

-   Klient parzystości

    -   Dzierżawi tożsamości z Store tożsamości

    -   Odnajduje i łączy do elementów równorzędnych

-   EthStats Agent

    -   Gromadzi informacje o lokalnym dzienników i statystyk za pośrednictwem usługi RPC, a następnie wypycha do usługi Azure Monitor

-   DApp nadzoru

    -   Interfejs sieci Web do interakcji z kontraktów rządów

## <a name="how-to-guides"></a>Przewodniki z instrukcjami
### <a name="governance-dapp"></a>DApp nadzoru

Serce dowód uwierzytelniania jest zdecentralizowana nadzoru. Nadzoru DApp jest zbiorem wstępnie wdrożyć [kontraktów inteligentnych](https://github.com/Azure-Samples/blockchain/tree/master/ethereum-on-azure/) oraz aplikacji sieci web używanej do zarządzania urzędy w sieci.
Urzędy są dzielone na tożsamości administratora i węzły modułu sprawdzania poprawności.
Administratorzy mają uprawnienia do delegowania uczestnictwa zgodne do zestawu węzłów modułu sprawdzania poprawności. Administratorzy również może głosować innych administratorów do lub z sieci.

![dapp nadzoru](./media/ethereum-poa-deployment/governance-dapp.png)

-   **Zdecentralizowana nadzoru —** zmiany w sieci urzędy są zarządzane za pośrednictwem-łańcuchowe głosu wybierz administratorów.

-   **Moduł sprawdzania poprawności delegowanie —** urzędy można zarządzać ich węzły modułu sprawdzania poprawności, które są zainstalowane w każdym wdrożeniu PoA.

-   **Historia inspekcji zmian -** każdej ze zmian są rejestrowane w zapewnieniu przejrzystości i sprawdzalność łańcucha bloków.

#### <a name="getting-started-with-governance"></a>Wprowadzenie do zarządzania
Aby wykonać dowolnego rodzaju transakcji za pośrednictwem DApp nadzoru, należy korzystać z aplikacji wallet Ethereum.  Najprostszym rozwiązaniem jest użycie portfela w przeglądarce, takie jak [MetaMask](https://metamask.io); Jednakże, ponieważ są one kontraktów inteligentnych wdrożony w sieci mogą też zautomatyzować interakcji z umową nadzoru.

Po zainstalowaniu MetaMask, przejdź do DApp nadzoru w przeglądarce.  Adres URL można znaleźć w wiadomości e-mail z potwierdzeniem wdrażania lub za pośrednictwem witryny Azure portal w danych wyjściowych wdrożenia.  Jeśli nie masz portfela w przeglądarce, zainstalowane nie będziesz w stanie wykonywać żadnych akcji; jednak nadal można odczytywać stanu administratora.  

#### <a name="becoming-an-admin"></a>Staje się administratorem
W przypadku pierwszego elementu członkowskiego, który jest wdrożony w sieci, następnie zapoznasz się automatycznie administratorem i węzły parzystości będzie wyświetlana jako modułów weryfikacji.  Jeśli dołączasz sieci musisz uzyskiwanie oceniane jako administrator większością (większe niż 50%) z istniejącego zestawu administracyjnego.  Jeśli nie chcesz stać się z administratorem, aby węzły będą nadal synchronizacji i sprawdzania poprawności łańcucha bloków; Jednakże nie będą używane w bloku procesu tworzenia. Aby rozpocząć proces głosu, aby zostać administratorem, kliknij przycisk __Nominate__ i wprowadź swój adres Ethereum i alias.

![Nominowanie](./media/ethereum-poa-deployment/governance-dapp-nominate.png)

#### <a name="candidates"></a>Kandydatów
Wybieranie __kandydatów__ kartę pokaże bieżącego zestawu Administratorzy Release candidate.  Po kandydat osiągnie większością głosów przez bieżący administratorów, kandydat będzie pobrać promowane do administratora.  Zagłosować na kandydat, wybierz wiersz i kliknij przycisk "Głos" u góry.  Jeśli zmienisz zdanie na głos, wybrać kandydat i kliknij przycisk "Rescind głosu".

![Kandydatów](./media/ethereum-poa-deployment/governance-dapp-candidates.png)


#### <a name="admins"></a>Administratorzy
__Administratorzy__ karta będzie Pokaż bieżącego zestawu Administratorzy i zapewniają możliwość głosowania względem.  Gdy administrator straci więcej niż 50% pomocy technicznej, zostaną one usunięte jako administrator w sieci.  Wszystkie węzły modułu sprawdzania poprawności, które należą do tego administratora spowoduje utratę stan weryfikacji i staną się węzłami transakcji w sieci.  Administrator może zostać usunięty na dowolną liczbę powodów; jednak jest konsorcjum uzgodnić zasady z wyprzedzeniem.

![Administratorzy](./media/ethereum-poa-deployment/governance-dapp-admins.png)

#### <a name="validators"></a>Moduły weryfikacji
Wybieranie __moduły weryfikacji__ kartę w menu po lewej stronie zostanie wyświetlona bieżące węzły parzystości wdrożonych dla tego wystąpienia i ich bieżącego stanu (typ węzła).  Każdy element członkowski konsorcjum będzie mieć inny zestaw modułów sprawdzania poprawności na tej liście, ponieważ ten widok przedstawia bieżący element członkowski konsorcjum wdrożone.  Jeśli to jest nowo wdrożonego wystąpienia, a jeszcze nie dodano usługi modułów sprawdzania poprawności, będzie wyświetlana opcja do "Dodawanie modułów sprawdzania poprawności".  Wybranie tej zostanie automatycznie wybierz regionalnie o zrównoważonym obciążeniu zestaw węzłów parzystości i przypisać je do zestawu modułu sprawdzania poprawności.  Jeśli wdrożono więcej węzłów niż dozwolonej pojemności, pozostałe węzły staną się węzłami transakcji w sieci.

Adres każdego modułu sprawdzania poprawności jest automatycznie przypisywany za pośrednictwem [magazynu tożsamości](#identity-store) na platformie Azure.  Jeśli węzeł ulegnie awarii, zostanie on zrzeka się swoją tożsamość, umożliwiając innego węzła w danym wdrożeniu jej wyznacz.  Dzięki temu uczestnictwo consensus o wysokiej dostępności.

![Moduły weryfikacji](./media/ethereum-poa-deployment/governance-dapp-validators.png)

#### <a name="consortium-name"></a>Nazwa konsorcjum
Każdy administrator może zaktualizować nazwę konsorcjum wyświetlane u góry strony.  Wybierz ikonę koła zębatego w lewym górnym rogu można zaktualizować nazwy konsorcjum.

#### <a name="account-menu"></a>Menu konta
W prawym górnym rogu jest alias konta Ethereum i identicon usługi.  Jeśli jesteś administratorem, będziesz mieć możliwość zaktualizowania aliasu.

![Konto](./media/ethereum-poa-deployment/governance-dapp-account.png)

### <a name="deploy-ethereum-proof-of-authority"></a>Wdrażanie dowód uwierzytelniania Ethereum

Oto przykład przepływu wieloosobowa wdrożenia:

1.  Trzech członków Generowanie Ethereum konta przy użyciu MetaMask

2.  *Element członkowski A* wdraża Ethereum PoA, zapewniając ich Ethereum publicznego adresu

3.  *Element członkowski A* zawiera adres URL konsorcjum *składnika B* i *elementu członkowskiego w języku C*

4.  *Element członkowski B* i *C elementu członkowskiego* wdrażanie Ethereum PoA, zapewniając ich Ethereum publiczny adres i *elementu członkowskiego, A*firmy konsorcjum adresu URL

5.  *Element członkowski A* głosów na *składnika B* jako administrator

6.  *Element członkowski* i *składnika B* zarówno głos *C elementu członkowskiego* jako administrator

Ten proces wymaga subskrypcji platformy Azure, która może obsługiwać wdrożenie wielu maszyn wirtualnych i dyski zarządzane. Jeśli to konieczne, [Utwórz bezpłatne konto platformy Azure](https://azure.microsoft.com/free/) do rozpoczęcia.

Gdy subskrypcja jest zabezpieczony, przejdź do witryny Azure portal. Wybierz pozycję "+", witryny Marketplace ("zobaczyć wszystkich") i poszukaj konsorcjum PoA Ethereum.

Poniższa sekcja przeprowadzi Konfigurowanie zużycie pierwszego elementu członkowskiego w sieci. Przepływ wdrożenia jest podzielona na pięć kroków: Podstawy, wdrożenia regiony, Network Activity block size i wydajności, ustawień Ethereum usługi Azure Monitor.

#### <a name="basics"></a>Podstawy

W obszarze **podstawy**, określ wartości dla parametrów standardowe dla wszystkich wdrożeń, takich jak subskrypcji, grupy zasobów i właściwości podstawowe maszyny wirtualnej.

Szczegółowy opis każdego parametru następująco:

Nazwa parametru|Opis|Dozwolone wartości|Wartości domyślne
---|---|---|---
Utwórz nową sieć lub dołączanie do istniejącej sieci?|Utwórz nową sieć lub przyłącz istniejące sieci konsorcjum|Utwórz nowy istniejące sprzężenia|Create New (Utwórz nowe)
Adres e-mail (opcjonalnie)|Otrzymasz wiadomość e-mail z powiadomieniem po zakończeniu wdrożenia, informacje o wdrożeniu.|Prawidłowy adres e-mail|Nie dotyczy
Nazwa użytkownika maszyny Wirtualnej|Nazwa użytkownika administratora w każdej wdrożonej maszyny wirtualnej (tylko znaki alfanumeryczne)|1 – 64 znaków|Nie dotyczy
Typ uwierzytelniania|Metoda uwierzytelniania do maszyny wirtualnej.|Klucz publiczny hasła lub protokołu SSH|Hasło
Hasło (typ uwierzytelniania = hasło)|Hasło dla konta administratora dla każdej z wdrożonych maszyn wirtualnych.  Hasło musi zawierać 3 z następujących czynności: 1 Wielka litera, 1 mała litera, 1 cyfra i 1 znak specjalny. Gdy wszystkie maszyny wirtualne początkowo miały to samo hasło, możesz zmienić hasło po zainicjowaniu obsługi administracyjnej.|12 do 72 znaków|Nie dotyczy
Klucz SSH (typ uwierzytelniania = klucz publiczny)|Klucz protokołu secure shell, używany do logowania zdalnego.||Nie dotyczy
Subskrypcja|Subskrypcja, dla której chcesz wdrożyć sieci konsorcjum||Nie dotyczy
Grupa zasobów|Grupa zasobów, dla której chcesz wdrożyć sieci konsorcjum.||Nie dotyczy
Lokalizacja|Region platformy Azure dla grupy zasobów.||Nie dotyczy

Poniżej przedstawiono przykładowe wdrożenie: ![blok podstawowe](./media/ethereum-poa-deployment/basic-blade.png)

#### <a name="deployment-regions"></a>Wdrożenia regiony

Następnie w obszarze wdrożenia regiony, określ dane wejściowe dla liczby regiony wdrażanie sieci konsorcjum i wybór regiony platformy Azure na podstawie liczby regionów, biorąc pod uwagę. Użytkownika można wdrożyć w maksymalnie 5 regionów. Firma Microsoft zaleca wybranie pierwszego regionu do dopasowania lokalizację grupy zasobów, w sekcji podstawy. Deweloperskie lub testowe sieci zaleca się jednym regionie poszczególnych członków. W środowisku produkcyjnym zaleca się wdrożenie co najmniej dwóch regionach dla wysokiej dostępności.

Szczegółowy opis każdego parametru następująco:

  Nazwa parametru|Opis|Dozwolone wartości|Wartości domyślne
  ---|---|---|---
  Liczba regiony|Liczbie regionów, aby wdrożyć sieć konsorcjum|1, 2, 3, 4, 5|1
  Pierwszym regionie|Pierwszy region, aby wdrożyć sieć konsorcjum|Dozwolone wszystkie regiony platformy Azure|Nie dotyczy
  Drugi region|Drugi region, aby wdrożyć sieć konsorcjum (widoczne tylko wtedy, gdy liczba regionów jest wybrany jako 2)|Dozwolone wszystkie regiony platformy Azure|Nie dotyczy
  Trzeciego regionu|Trzeciego regionu na wdrażanie sieci konsorcjum (widoczne tylko wtedy, gdy liczba regionów jest wybrany jako 3)|Dozwolone wszystkie regiony platformy Azure|Nie dotyczy
  Czwarty regionu|Czwarty region, aby wdrożyć sieć konsorcjum (widoczne tylko wtedy, gdy liczba regionów jest wybrany jako 4)|Dozwolone wszystkie regiony platformy Azure|Nie dotyczy
  Piąty regionu|Piąty region, aby wdrożyć sieć konsorcjum (widoczne tylko wtedy, gdy liczba regionów jest wybrany jako 5)|Dozwolone wszystkie regiony platformy Azure|Nie dotyczy

Poniżej przedstawiono przykładowe wdrożenie: ![wdrożenia regiony](./media/ethereum-poa-deployment/deployment-regions.png)

#### <a name="network-size-and-performance"></a>Network Activity block size i wydajności

Następnie w obszarze "Network Activity block size i wydajności" Określ dane wejściowe dla rozmiaru sieci konsorcjum, takie jak liczba i rozmiar węzłów modułu sprawdzania poprawności.
Rozmiar magazynu węzeł weryfikacji określają potencjalne rozmiar łańcucha bloków. Można to zmienić po wdrożeniu.

Szczegółowy opis każdego parametru następująco:

  Nazwa parametru|Opis|Dozwolone wartości|Wartości domyślne
  ---|---|---|---
  Liczba węzłów modułu weryfikacji o zrównoważonym obciążeniu|Liczba węzłów modułu sprawdzania poprawności do aprowizowania w ramach sieci|2-15|2
  Moduł sprawdzania poprawności dla wydajności przechowywania danych węzła|Typ dysku zarządzanego kopii każdego z węzłów wdrożonego modułu sprawdzania poprawności.|SSD w warstwie standardowa lub Premium|Standardowa, SSD
  Rozmiar maszyny wirtualnej węzła modułu sprawdzania poprawności|Rozmiar maszyny wirtualnej, używany dla węzłów modułu sprawdzania poprawności.|Standardowa A, standardową wartość d., standardowa D-v2, standardowa F serii, Standard DS i FS standardowe|Standardowa D1, wersja 2

[Storage — Szczegóły cennika](https://azure.microsoft.com/pricing/details/managed-disks/)

[Szczegóły cennika maszyny wirtualnej](https://azure.microsoft.com/pricing/details/virtual-machines/windows/)

Maszyna wirtualna i warstwy magazynowania będzie wpływają na wydajność sieci.  Zaleca się następujące jednostki SKU oparte na żądaną niskich kosztów:

  Numer SKU maszyny wirtualnej|Warstwy magazynowania|Cena|Przepływność|Opóźnienie
  ---|---|---|---|---
  F1|Standardowa, SSD|niska|niska|Wysoka
  D2_v3|Standardowa, SSD|średnie|średnie|średnie
  F16s|Premium, SSD|Wysoka|Wysoka|niska

Poniżej przedstawiono przykładowe wdrożenie: ![rozmiaru i wydajności sieci](./media/ethereum-poa-deployment/network-size-and-performance.png)

#### <a name="ethereum-settings"></a>Ustawienia Ethereum

Następnie w obszarze Ustawienia Ethereum Określ ustawienia konfiguracji odnoszące się do Ethereum, takie jak sieci ID i Ethereum konta hasła lub genesis bloku.

Szczegółowy opis każdego parametru następująco:

  Nazwa parametru|Opis|Dozwolone wartości|Wartości domyślne
  ---|---|---|---
Identyfikator elementu członkowskiego konsorcjum|Identyfikator skojarzony z każdym elementem członkowskim należących do sieci konsorcjum używane do konfigurowania przestrzeni adresów IP w celu uniknięcia kolizji. W przypadku sieci prywatnej przez różne organizacje w tej samej sieci powinien być unikatowy identyfikator elementu członkowskiego.  Identyfikator unikatowy elementu członkowskiego jest potrzebny, nawet wtedy, gdy w tej samej organizacji wdraża do wielu regionów. Zanotuj wartość tego parametru, ponieważ musisz udostępnić go innym członkom przyłączany do upewnij się, że nie ma żadnych kolizji.|0-255|Nie dotyczy
Identyfikator sieci|Identyfikator sieci konsorcjum sieci Ethereum wdrażane.  Każda sieć Ethereum ma swój własny identyfikator sieci 1 oznacza identyfikator sieci publicznej.|5 - 999,999,999|10101010
Adres Ethereum administratora|Adres konta Ethereum służy do uczestnictwa w PoA nadzoru.  Zalecamy używanie MetaMask podczas generowania adresu Ethereum.|42 znaki alfanumeryczne, począwszy od 0 x|Nie dotyczy
Opcje zaawansowane|Opcje zaawansowane ustawienia Ethereum|Włączanie lub wyłączanie|Wyłączanie
Publiczny adres IP (Zaawansowane opcje = włączone)|Wdraża sieci za bramą sieci wirtualnej, a następnie usuwa dostęp komunikacji równorzędnej. Jeśli ta opcja jest zaznaczona, wszystkie elementy Członkowskie musi być bramy sieci wirtualnej dla połączenia być zgodne.|Prywatne publicznego adresu IP w sieci wirtualnej|Publiczny adres IP
Blokuj gaz Limit (Zaawansowane opcje = włączone)|Limit gaz bloku od sieci|Wszelkie wartości numeryczne|50,000,00
Okres Reseal bloku (s)|Częstotliwość, z jaką zostaną utworzone bloki pusty, gdy nie ma żadnych transakcji w sieci. Wyższa częstotliwość będzie miał ustanawianych szybsze, ale koszty zasobów pamięci masowej.|Wszelkie wartości numeryczne|15
Kontrakt uprawnienie transakcji (Zaawansowane opcje = włączone)|Kod bajtowy dla kontraktu transakcji udzielania do nich uprawnień. Ogranicza inteligentne kontraktu, wdrażaniem i uruchamianiem do listy dozwolonych, Ethereum kont.|Kod bajtowy kontraktu|Nie dotyczy

Poniżej przedstawiono przykładowe wdrożenie: ![ethereum ustawienia](./media/ethereum-poa-deployment/ethereum-settings.png)

#### <a name="monitoring"></a>Monitorowanie

Blok monitorowanie umożliwia skonfigurowanie zasobów dzienniki usługi Azure Monitor sieci. Agent monitorowania będzie zbierać i powierzchni przydatne metryki i dzienniki z Twojej sieci, umożliwiając szybkie sprawdzenie kondycji sieci lub debugowania problemów.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

  Nazwa parametru|Opis|Dozwolone wartości|Wartości domyślne
  ---|---|---|---
Monitorowanie|Opcja włączania monitorowania|Włączanie lub wyłączanie|Włączanie
Nawiązać połączenie z istniejących dzienników usługi Azure Monitor|Utwórz nowe wystąpienie dzienniki usługi Azure Monitor, lub Dołącz do istniejącego wystąpienia|Tworzenie nowego elementu lub Dołącz istniejące|Tworzenie nowego elementu
Monitorowanie lokalizacji (Nawiązywanie połączenia z istniejących dzienników usługi Azure Monitor = Utwórz nowy)|Region, w którym nowe usługi Azure Monitor rejestruje wystąpienie zostanie wdrożony.|Wszystkie usługi Azure Monitor rejestruje regionów|Nie dotyczy
Identyfikator obszaru roboczego analizy dzienników istniejące (Nawiązywanie połączenia z istniejących dzienników usługi Azure Monitor = Dołącz istniejące)|Identyfikator obszaru roboczego z istniejącej usługi Azure Monitor rejestruje wystąpienie||Nie dotyczy
Klucz podstawowy istniejące log analytics (łączenie z istniejących dzienników usługi Azure Monitor = Dołącz istniejące)|Klucz podstawowy używany do łączenia z istniejącego wystąpienia dzienniki usługi Azure Monitor||Nie dotyczy


Poniżej przedstawiono przykładowe wdrożenie: ![usługa azure monitor](./media/ethereum-poa-deployment/azure-monitor.png)

#### <a name="summary"></a>Podsumowanie

Kliknij blok podsumowania, aby wyświetlić dane wejściowe określone, a także aby uruchomić podstawową walidację przed wdrożeniem. Przed wdrożeniem może pobrać szablon i parametry.

Przejrzyj postanowienia prawne i ochrona prywatności, a następnie kliknij przycisk zakupu, aby wdrożyć. Jeśli wdrożenie obejmuje bramy sieci wirtualnej, wdrożenia będzie potrwać 45 minut 50.

#### <a name="post-deployment"></a>Po wdrożeniu

##### <a name="deployment-output"></a>Dane wyjściowe wdrożenia

Po zakończeniu wdrażania możesz uzyskać dostęp niezbędne parametry za pomocą wiadomości e-mail z potwierdzeniem lub za pośrednictwem witryny Azure portal. W tych parametrów można znaleźć:

-   Punkt końcowy Ethereum RPC

-   Adres URL pulpitu nawigacyjnego nadzoru

-   Adres URL usługi Azure Monitor

-   Adres URL danych

-   Identyfikator zasobu bramy sieci wirtualnej (opcjonalnie)

##### <a name="confirmation-email"></a>Wiadomość e-mail z potwierdzeniem

Jeśli podasz adres e-mail ([sekcji podstawy](#basics)), będzie wysłana wiadomość e-mail na adres e-mail z informacjami o danych wyjściowych wdrożenia.

![Wdrażanie poczty e-mail](./media/ethereum-poa-deployment/deployment-email.png)

##### <a name="portal"></a>Portal

Po pomyślnym zakończeniu wdrożenia i wszystkie zasoby zostały udostępnione parametry wyjściowe można wyświetlić w grupie zasobów.

1.  Znajdź grupę zasobów w portalu

2.  Przejdź do *wdrożeń*

3.  Wybierz górny wdrożenie o takiej samej nazwie jak grupy zasobów

4.  Wybierz *danych wyjściowych*

### <a name="growing-the-consortium"></a>Rośnie konsorcjum

Aby rozszerzyć swoje konsorcjum, musisz najpierw połączyć sieci fizycznej.
Przy użyciu wdrożenia na podstawie publicznego adresu IP to pierwszym krokiem jest bezproblemowe. Jeśli wdrażana za sieci VPN, zobacz sekcję [połączenie bramy sieci wirtualnej](#connecting-vnet-gateways) celu połączenie sieciowe w ramach nowego wdrożenia elementu członkowskiego.  Po wdrożeniu ukończeniu użyj [DApp nadzoru](#governance-dapp) przestanie sieci administratora.

#### <a name="new-member-deployment"></a>Nowe wdrożenie elementu członkowskiego

1.  Poniższe informacje należy udostępnić przyłączany elementu członkowskiego. Te informacje można znaleźć w wiadomości e-mail po wdrożeniu, lub w danych wyjściowych wdrożenia w portalu.

    -  Adres Url danych konsorcjum

    -  Liczba węzłów, które udało Ci się wdrożyć

    -  Identyfikator zasobu bramy sieci wirtualnej (jeśli jest używana sieć VPN)

2.  Wdrażanie elementu członkowskiego, należy użyć [tego samego rozwiązania](https://portal.azure.com/?pub_source=email&pub_status=success#create/microsoft-azure-blockchain.azure-blockchain-ethereumethereum-poa-consortium) podczas wdrażania ich obecność sieci za pomocą pamiętając o tym, że:

    -  Wybierz *Dołącz istniejące*

    -  Wybierz taką samą liczbę węzłów modułu sprawdzania poprawności, jak w pozostałych elementów członkowskich w sieci, do zapewnienia właściwej reprezentacji

    -  Użyj tego samego adresu Ethereum, który został podany w poprzednim kroku

    -  Przekaż w określonych *adresu Url danych konsorcjum* na *ustawienia Ethereum* kartę

    -  W przypadku pozostałej części sieci poza sieć VPN wybierz *prywatnej sieci wirtualnej* w sekcji ustawień zaawansowanych

#### <a name="connecting-vnet-gateways"></a>Łączenie bramy sieci wirtualnej

Jeśli udało Ci się wdrożyć przy użyciu domyślnych ustawień publicznego adresu IP, możesz zignorować ten krok. W przypadku sieci prywatnej inne elementy członkowskie są połączone za pośrednictwem połączenia bramy sieci wirtualnej. Przed członka można dołączyć sieci oraz transakcji ruch jest widoczny, istniejącego elementu członkowskiego, należy wykonać konfiguracji końcowej swojej bramy sieci VPN, aby akceptował połączenia. Oznacza to, że węzły Ethereum przyłączany elementu członkowskiego nie będzie działać, dopóki nie zostanie nawiązane połączenie. Zaleca się tworzenie nadmiarowych połączeń sieciowych (sieć) do konsorcjum, aby zmniejszyć prawdopodobieństwo pojedynczy punkt awarii.

Po wdrożeniu nowego elementu członkowskiego, istniejący element członkowski, należy wykonać dwukierunkowe połączenie, konfigurując połączenia z bramą sieci wirtualnej do nowego członka. Aby to osiągnąć, istniejącego posiadacza będą potrzebne:

1.  Bramy sieci wirtualnej ResourceID łączącego się elementu członkowskiego (zobacz dane wyjściowe wdrożenia)

2.  Klucz połączenia udostępnionego

Istniejący element członkowski, należy uruchomić poniższy skrypt programu PowerShell, aby nawiązać połączenie. Zaleca się, że za pomocą usługi Azure Cloud Shell znajdujących się na pasku nawigacyjnym w prawym górnym rogu w portalu.

![Usługa cloud shell](./media/ethereum-poa-deployment/cloud-shell.png)

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

Możesz znaleźć, portalu usługi Azure Monitor poprzez łącze w wiadomości e-mail z wdrożenia lub lokalizowanie parametru w danych wyjściowych wdrożenia \[OMS\_PORTAL\_adresu URL\].

Portal wyświetli najpierw omówienie statystyk i węzeł sieci wysokiego poziomu.

![kategorie monitorowania](./media/ethereum-poa-deployment/monitor-categories.png)

Wybieranie **Przegląd węzła** nastąpi bezpośrednie przekierowanie do portalu, aby wyświetlić statystyki infrastruktury każdego węzła.

![statystyki węzła](./media/ethereum-poa-deployment/node-stats.png)

Wybieranie **statystyki sieci** nastąpi bezpośrednie przekierowanie do wyświetlania statystyk sieciowych Ethereum.

![statystyki sieci](./media/ethereum-poa-deployment/network-stats.png)

#### <a name="sample-kusto-queries"></a>Przykładowe zapytania Kusto

Za te pulpity nawigacyjne to zbiór odpytywalny nieprzetworzonych dzienników. Dostosuj pulpity nawigacyjne, zbadaj błędy lub Konfigurowanie alertów wartości progowej, można użyć tych nieprzetworzonych dzienników. Poniżej znajdziesz, że zestaw przykładowych zapytań, które mogą być uruchomione w narzędziu wyszukiwanie w dzienniku:

##### <a name="lists-blocks-that-have-been-reported-by-more-than-one-validator-useful-to-help-find-chain-forks"></a>Wyświetla bloki, które zostały zgłoszone przez więcej niż jeden moduł sprawdzania poprawności. Przydatne do znajdowania rozwidlenia łańcucha.

```sql
MinedBlock_CL
| summarize DistinctMiners = dcount(BlockMiner_s) by BlockNumber_d, BlockMiner_s
| where DistinctMiners > 1
```

##### <a name="get-average-peer-count-for-a-specified-validator-node-averaged-over-5-minute-buckets"></a>Zliczanie średni elementu równorzędnego dla węzła określonego modułu sprawdzania poprawności średniej ponad 5 minut zasobników.

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

Ze względów bezpieczeństwa dostępu do portu SSH zostanie odrzucona przez regułę sieciowej grupy zabezpieczeń domyślnie. Aby uzyskać dostęp do wystąpień maszyn wirtualnych w sieci PoA, należy zmienić tę regułę do \"Zezwalaj\"

1.  Uruchom w sekcji Przegląd zasobów wdrożonych grupy z witryny Azure portal.

    ![SSH — omówienie](./media/ethereum-poa-deployment/ssh-overview.png)

2.  Wybierz grupę zabezpieczeń sieci dla regionu maszynę Wirtualną, która chce uzyskać dostęp

    ![SSH sieciowej grupy zabezpieczeń](./media/ethereum-poa-deployment/ssh-nsg.png)

3.  Wybierz \"Zezwalaj-ssh\" reguły

    ![SSH — Zezwalaj na](./media/ethereum-poa-deployment/ssh-allow.png)

4.  Zmiana \"akcji\" umożliwia

    ![SSH Włącz Zezwalaj](./media/ethereum-poa-deployment/ssh-enable-allow.png)

5.  Kliknij przycisk \"Zapisz\" (zmian może potrwać kilka minut, aby zastosować)

Teraz można zdalnie podłączyć do maszyn wirtualnych dla węzłów weryfikacji za pośrednictwem protokołu SSH przy użyciu klucza nazwy użytkownika i hasła/SSH podana administratora.
Polecenie SSH, aby uruchomić dostępu do pierwszego węzła modułu sprawdzania poprawności jest wymieniony w parametr wyjściowy szablonu wdrożenia jako "SSH\_na\_pierwszy\_licencjonowania zbiorowego\_węzła\_REGION1" (Aby uzyskać przykładowe wdrożenie: ssh -p 4000 poaadmin\@leader4vb.eastus.cloudapp.azure.com). Aby przejść do transakcji dodatkowe węzły, należy zwiększyć numer portu za pomocą jednej (na przykład, pierwszy węzeł transakcji jest na porcie 4000).

Jeśli wdrożono więcej niż jednym regionie, zmienić powyższego polecenia do nazwy DNS lub adres IP modułu równoważenia obciążenia w danym regionie. Aby znaleźć nazwę DNS lub adres IP w innych regionach, Znajdź zasób z konwencji nazewnictwa \* \* \* \* \*reg - lbpip\#i wyświetlić jego nazwę DNS i adresów IP właściwości.

### <a name="azure-traffic-manager-load-balancing"></a>Równoważenie obciążenia Azure Traffic Manager

Usługa Azure Traffic Manager pomaga zredukować przestoje i zwiększyć szybkość reakcji sieci PoA, kierując ruch przychodzący na wiele wdrożeń w różnych regionach. Kontrole kondycji wbudowanych i automatyczne trasy pomagają zapewnić wysoką dostępność punktów końcowych RPC i DApp nadzoru. Ta funkcja jest przydatna, jeśli została wdrożona w wielu regionach i są już gotowe do produkcji.

Zastosowania usługi Traffic Manager:

-   Zwiększanie dostępności sieci PoA automatyczny tryb failover.

-   Zwiększyć szybkość reakcji Twojej sieci w routingu użytkowników końcowych do lokalizacji platformy Azure zapewnia najniższe opóźnienie sieci.

Jeśli zdecydujesz utworzyć profil usługi Traffic Manager, można użyć nazwy DNS profilu do uzyskania dostępu do sieci. Gdy inni członkowie konsorcjum zostały dodane do sieci, usługi Traffic Manager można również w celu zrównoważenia obciążenia w ich wdrożone moduły weryfikacji.

#### <a name="creating-a-traffic-manager-profile"></a>Tworzenie profilu usługi Traffic Manager

Wyszukaj i wybierz pozycję \"profilu usługi Traffic Manager\" po kliknięciu przycisku \"Utwórz zasób\" przycisku w witrynie Azure portal.

![Wyszukiwanie usługi azure traffic manager](./media/ethereum-poa-deployment/traffic-manager-search.png)

Nadaj unikatową nazwę profilu, a następnie wybierz grupę zasobów, który został utworzony podczas wdrażania PoA. Kliknij przycisk "Utwórz", aby wdrożyć.

![Tworzenie traffic manager](./media/ethereum-poa-deployment/traffic-manager-create.png)

Po wdrożeniu, wybierz wystąpienie, w grupie zasobów. Nazwy DNS na dostęp do usługi traffic manager można znaleźć na karcie Przegląd

![Znajdź usługi traffic manager DNS](./media/ethereum-poa-deployment/traffic-manager-dns.png)

Wybierz kartę punktów końcowych, a następnie kliknij przycisk Dodaj. Nadaj punktu końcowego unikatową nazwę. Zmień typ zasobu docelowego na publiczny adres IP. Następnie wybierz publiczny adres IP w pierwszym regionie\'s równoważenia obciążenia.

![Routingu usługi traffic manager](./media/ethereum-poa-deployment/traffic-manager-routing.png)

Należy powtórzyć dla każdego regionu, w wdrożone sieci. Gdy punktów końcowych, które znajdują się w \"włączone\" stanu, będzie można automatycznie załadować i regionu równoważenia nazwę DNS usługi traffic manager. Teraz można używać tej nazwy DNS zamiast \[konsorcjum\_danych\_adresu URL\] parametru w innych krokach dokumentu.

### <a name="data-api"></a>Interfejs API danych

Każdy element członkowski konsorcjum udostępnia informacje niezbędne do innych użytkowników nawiązać połączenie z siecią. Istniejącego posiadacza zapewni [CONSORTIUM_DATA_URL] przed przystąpieniem do wdrożenia elementu członkowskiego. Po wdrożeniu przyłączany elementu członkowskiego powoduje pobranie informacji z interfejsu JSON na następujący punkt końcowy:

`<CONSORTIUM_DATA_URL>/networkinfo`

Odpowiedź będzie zawierać informacje przydatne w przypadku dołączania elementów członkowskich (Genesis bloku, ustaw modułu sprawdzania poprawności kontrakt interfejsu ABI, bootnodes) i informacje przydatne do istniejącego elementu członkowskiego (modułu sprawdzania poprawności adresów). Zaleca się użycie tej normalizacji rozszerzenie konsorcjum różnych dostawców rozwiązań w chmurze. Ten interfejs API zwróci odpowiedź w formacie JSON w formacie o następującej strukturze:
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

### <a name="programmatically-interacting-with-a-smart-contract"></a>Programowej interakcji ze inteligentne kontraktu

> [!WARNING]
> Nigdy nie wysyłaj klucz prywatny Ethereum za pośrednictwem sieci! Upewnij się, że każda transakcja jest podpisany lokalnie najpierw i podpisem transakcji są przesyłane za pośrednictwem sieci.

W poniższym przykładzie użyto *portfela ethereumjs* do generowania adresu Ethereum *ethereumjs tx* się lokalnie, i *sieci Web 3* wysyłać nieprzetworzone transakcji Punkt końcowy Ethereum RPC.

Użyjemy tego prostego kontraktu inteligentne Witaj, świecie, w tym przykładzie:

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

W tym przykładzie przyjęto założenie, że Umowa została już wdrożona. Możesz użyć *solc* i *sieci Web 3* wdrażania programowego kontraktu. Najpierw należy zainstalować następujące moduły node.js:
```
sudo npm install web3@0.20.2
sudo npm install ethereumjs-tx@1.3.6
sudo npm install ethereumjs-wallet@0.6.1
```
Ten skrypt nodeJS będzie wykonywać następujące czynności:

-   Konstruowania pierwotne transakcji: postMsg

-   Zaloguj się transakcji przy użyciu wygenerowany klucz prywatny

-   Prześlij podpisem transakcji z siecią Ethereum

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

### <a name="deploy-smart-contract-with-truffle"></a>Wdrażanie inteligentne kontrakt o Truffle

-   Instalowanie wymaganych bibliotek

```javascript
npm init

npm install truffle-hdwallet-provider --save
```
-   W truffle.js, Dodaj następujący kod do odblokowania konta MetaMask i konfigurowania węzła PoA jako wpis punktu, zapewniając mnemoników frazy (MetaMask / ustawienia / ujawnić słów inicjatora)

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

-   Wdrażanie w sieci PoA

```javascript
$ truffle migrate --network poa
```

### <a name="debug-smart-contract-with-truffle"></a>Debugowanie inteligentne kontrakt o Truffle

Truffle ma sieci lokalne programowanie, która jest dostępna do debugowania inteligentne kontraktu. Możesz znaleźć pełnym samouczku dotyczącym [tutaj](https://truffleframework.com/tutorials/debugging-a-smart-contract).

### <a name="webassembly-wasm-support"></a>Obsługa format WebAssembly (WASM)

Format WebAssembly obsługi jest już włączony dla Ciebie w nowo wdrożonym PoA sieciach. Umożliwia ona tworzenie inteligentnych umowy w dowolnym języku tego transpiles do sieci Web — zestawu (Rust, C i C++). Zobacz linki poniżej, aby uzyskać dodatkowe informacje

-   Omówienie parzystości format WebAssembly- <https://wiki.parity.io/WebAssembly-Home>

-   Samouczek z parzystością Tech- <https://github.com/paritytech/pwasm-tutorial>

## <a name="reference"></a>Informacje ogólne

### <a name="faq"></a>Często zadawane pytania

#### <a name="i-notice-there-are-many-transactions-on-the-network-that-i-didnt-send-where-are-these-coming-from"></a>I Zwróć uwagę, istnieje wiele transakcji w sieci, są\'t wysyłania. Gdy te pochodzą z?

Jest niebezpieczne, aby odblokować [osobistych API](https://web3js.readthedocs.io/en/1.0/web3-eth-personal.html). Boty nasłuchiwanie odblokowane kont Ethereum i próbuje opróżnić środków. Bot przyjęto założenie, te konta zawiera rzeczywistą ether i próba Zostań pierwszym użytkownikiem Lewarek saldo. Nie należy włączać osobistych interfejsu API w sieci. Zamiast tego Utwórz wstępnie transakcji, albo ręcznie za pomocą aplikacji wallet, takich jak MetaMask lub programowo, zgodnie z opisem w sekcji [programowej interakcji ze kontrakt inteligentne](#programmatically-interacting-with-a-smart-contract).

#### <a name="how-to-ssh-onto-a-vm"></a>Jak SSH na maszynie Wirtualnej?

SSH port nie jest uwidaczniana, ze względów bezpieczeństwa. Postępuj zgodnie z [tego przewodnika, aby włączyć SSH port](#ssh-access).

#### <a name="how-do-i-set-up-an-audit-member-or-transaction-nodes"></a>Jak skonfigurować element członkowski lub transakcji węzłów inspekcji?

Węzły transakcji to zbiór klientów parzystości, które jest połączona z siecią, ale nie uczestniczą w consensus. Te węzły nadal może służyć do przesyłania transakcji Ethereum i odczytać stanu inteligentne kontraktu.
To działa również jako mechanizm do prezentowania sprawdzalność członkowie konsorcjum bez uwierzytelniania w sieci. Aby osiągnąć to po prostu postępuj zgodnie z kroku 2 nieograniczonemu konsorcjum.

#### <a name="why-are-metamask-transactions-taking-a-long-time"></a>Dlaczego są transakcje MetaMask zajmuje dużo czasu

Aby upewnić się, że transakcje są odbierane w odpowiedniej kolejności, każda transakcja Ethereum jest powiązana z zwiększającej się wartości (tymczasowo). Jeśli używano konta w MetaMask w innej sieci, należy zresetować nonce wartość. Kliknij ikonę ustawienia (pasków 3), ustawienia, Resetuj konto. Historia transakcji zostaną wyczyszczone, a teraz można ponownie przesłać transakcji.

#### <a name="do-i-need-to-specify-gas-fee-in-metamask"></a>Należy określić gaz opłaty w MetaMask?

Ether nie spełniają cel w konsorcjum dowód uwierzytelniania. Dlatego nie ma potrzeby określić gaz, opłata podczas przesyłania transakcji w MetaMask.

#### <a name="what-should-i-do-if-my-deployment-fails-due-to-failure-to-provision-azure-oms"></a>Co należy zrobić, jeśli Moje wdrożenie kończy się niepowodzeniem z powodu niepowodzenia aprowizacji Azure OMS?

Monitorowanie to opcjonalna funkcja. W rzadkich przypadkach gdy wdrożenie zakończy się niepowodzeniem z powodu braku możliwości pomyślnie aprowizować zasób usługi Azure Monitor, który można wdrożyć ponownie bez usługi Azure Monitor.

#### <a name="are-public-ip-deployments-compatible-with-private-network-deployments"></a>Publicznego adresu IP wdrożenia są zgodne z sieci prywatnej wdrożenia?

Nie, komunikacja równorzędna wymaga dwukierunkowej komunikacji, więc cała sieć musi być publiczny lub prywatny.

#### <a name="what-is-the-expected-transaction-throughput-of-proof-of-authority"></a>Co to jest przepływność oczekiwanej transakcji dowód uwierzytelniania?

Przepływność transakcji będzie bardzo zależne od typów transakcji i topologii sieci.  Za pomocą prostego transakcji, firma Microsoft została badany średnio 400 transakcji na sekundę z siecią wdrożonych w wielu regionach.

#### <a name="how-do-i-subscribe-to-smart-contract-events"></a>Jak subskrybować zdarzenia inteligentne kontraktu

Dowód uwierzytelniania Ethereum obsługuje teraz gniazda sieci web.  Sprawdź adres e-mail wdrożenia lub wdrożenia danych wyjściowych, aby zlokalizować gniazda sieci web adres URL i portów.

## <a name="next-steps"></a>Kolejne kroki

Rozpoczynanie pracy przy użyciu [konsorcjum dowód uwierzytelniania Ethereum](https://portal.azure.com/?pub_source=email&pub_status=success#create/microsoft-azure-blockchain.azure-blockchain-ethereumethereum-poa-consortium) rozwiązania.
