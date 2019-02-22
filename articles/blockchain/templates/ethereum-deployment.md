---
title: Szablon rozwiązania konsorcjum dowód pracy Ethereum
description: Szablon rozwiązania konsorcjum Ethereum dowód elementu roboczego umożliwia wdrażanie i konfigurowanie sieci Ethereum konsorcjum zawierającym wiele elementów członkowskich
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 01/28/2019
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: coborn
manager: femila
ms.openlocfilehash: 6530e86f59124e324953e4ca082c247ebec3bc91
ms.sourcegitcommit: a4efc1d7fc4793bbff43b30ebb4275cd5c8fec77
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/21/2019
ms.locfileid: "56650924"
---
# <a name="ethereum-proof-of-work-consortium-solution-template"></a>Szablon rozwiązania konsorcjum dowód pracy Ethereum

Szablon rozwiązania konsorcjum Ethereum dowód elementu pracy jest przeznaczony do umożliwiają łatwiejsze i szybsze wdrażanie i konfigurowanie sieci Ethereum konsorcjum zawierającym wiele elementów członkowskich przy minimalnej znajomości platformy Azure i Ethereum.

Każdy element członkowski przy użyciu szablonu usługi Azure Resource Manager, można udostępnić sieciowych zużycia, za pomocą programu Microsoft Azure Compute, sieć i usługi magazynu. Każdy element członkowski sieciowych zużycia składa się z zestawu węzłów ze zrównoważonym obciążeniem transakcji za pomocą której aplikacji lub użytkownika wchodzi w interakcję można przesłać transakcji, zestaw węzłów wyszukiwania rekordu transakcji i bramy sieci VPN. Po wdrożeniu możesz łączyć z bramy, aby utworzyć sieć pełni skonfigurowanej zawierającym wiele elementów członkowskich łańcucha bloków.

## <a name="about-blockchain"></a>Temat łańcucha bloków

Dla osób, które są nowe w społeczności łańcucha bloków wydanie to rozwiązanie jest znakomita szansa, aby dowiedzieć się więcej o technologii w sposób łatwy i można je konfigurować na platformie Azure. Aby rozpocząć pracę, zalecamy wdrożenie prostsze autonomiczny Ethereum topologią i konfiguracją sieci z tym przewodnikiem krok po kroku, przed zbudowaniu sieci konsorcjum zawierającym wiele elementów członkowskich.

### <a name="mining-node-details"></a>Szczegóły węzła wyszukiwania

Węzły, które analizować transakcje są oddzielone od węzłów, które akceptują transakcji, aby upewnić się, że dwie akcje nie są rywalizując o tych samych zasobów.

Członek konsorcjum może aprowizować maksymalnie pięciu regionach zawierające jeden lub więcej węzłów wyszukiwania, wspierane przez dysk zarządzany. Co najmniej jeden węzeł w regionie są konfigurowane jako węzeł rozruchu do obsługi dynamicznej odnajdywania węzłów w sieci. Węzły wyszukiwania komunikować się z innymi węzłami wyszukiwania na osiągnięcie consensus na stan bazowego rejestru rozproszonego. Nie ma potrzeby dla swojej aplikacji, należy pamiętać o lub komunikowania się z tych węzłów. Skupienie się w sieciach prywatnych, węzły wyszukiwania są odizolowane od ruch przychodzący z publicznego Internetu można dodać dodatkowej warstwy zabezpieczeń. Ruch wychodzący jest dozwolony, ale nie do portu odnajdywania Ethereum.

Wszystkie węzły mają stabilną wersję klienta Przejdź Ethereum (Geth) i są skonfigurowane jako węzły wyszukiwania. Jeśli blok genesis niestandardowe nie podana, wszystkie węzły, skorzystaj z tego samego adresu Ethereum i parę kluczy, który jest chroniony przez Ethereum hasło do konta. Hasło Ethereum, które podano jest używany do generowania domyślnego konta (coinbase) dla każdego węzła wyszukiwania. Jako węzły wyszukiwania analizować, pobierają opłaty, które są dodawane do tego konta.

Liczba węzłów wyszukiwania poszczególnych członków konsorcjum zależy od rozmiaru sieci żądanego i ilości mocy wyznaczania wartości skrótu, które jest przeznaczone do każdego członka. Większe sieci wymaga większą liczbę węzłów do zostaną ujawnione w celu uzyskania nienależną przewagę. Szablon obsługuje maksymalnie 15 węzłami wyszukiwania na region aprowizowane za pomocą zestawów skalowania maszyn wirtualnych.

### <a name="transaction-node-details"></a>Szczegóły węzła transakcji

Element członkowski konsorcjum ma również zestaw węzłów transakcji ze zrównoważonym obciążeniem. Te węzły są dostępne z spoza sieci wirtualnej, dzięki czemu aplikacje mogą używać tych węzłów do przesyłania transakcji lub wykonania kontrakty inteligentne w ramach sieci łańcucha bloków. Wszystkie węzły mają stabilną wersję klienta Przejdź Ethereum (Geth) i są skonfigurowane do obsługi pełną kopię rejestru rozproszonego. Jeśli blok genesis niestandardowe nie zostanie podany, te węzły używają tego samego konta Ethereum, chronionych hasłem konta Ethereum.

Węzły transakcji są ze zrównoważonym obciążeniem w obrębie zestaw dostępności, aby zapewnić wysoką dostępność. Szablon obsługuje maksymalnie pięć węzłów transakcji aprowizowane za pomocą zestawów skalowania maszyn wirtualnych.

### <a name="azure-monitor-logs-details"></a>Usługa Azure Monitor rejestruje szczegółowe informacje

Każde wdrożenie również tworzy nowe wystąpienie dzienniki usługi Azure Monitor lub dołączyć do istniejącego wystąpienia. Dzienniki platformy Azure Monitor umożliwia monitorowanie różnych metryk wydajności poszczególnych maszyn wirtualnych, tworzącą sieć wdrożone.

## <a name="deployment-architecture"></a>Architektura wdrożenia

### <a name="description"></a>Opis

Ten szablon rozwiązania można wdrożyć jedno- lub wielowarstwowe wielu regionów na podstawie elementu członkowskiego Ethereum konsorcjum sieci. Sieć wirtualną każdego regionu jest podłączony do innego regionu w topologii łańcucha przy użyciu bram sieci wirtualnych i zasoby połączenia. Aprowizuje ona również rejestratora, który zawiera wymagane informacje o wszystkich węzłów transakcji i Miner wdrożone w każdym regionie.

### <a name="standalone-and-consortium-leader-overview"></a>Autonomiczne, jak i konsorcjum lidera — omówienie

![Autonomiczne i konsorcjum lidera omówienie](./media/ethereum-deployment/leader-overview.png)

### <a name="joining-consortium-member-overview"></a>Łączenie konsorcjum omówienie elementu członkowskiego

![Łączenie konsorcjum omówienie elementu członkowskiego](./media/ethereum-deployment/member-overview.png)

## <a name="getting-started"></a>Wprowadzenie

Ten proces wymaga subskrypcji platformy Azure obsługują wdrażanie kilka zestawów skalowania maszyn wirtualnych i dyski zarządzane. Jeśli to konieczne, Utwórz bezpłatne konto platformy Azure, aby rozpocząć.

Gdy subskrypcja jest zabezpieczony, przejdź do witryny Azure portal. Wybierz **+ Utwórz zasób**, witryny Marketplace (Zobacz wszystko) i wyszukaj **konsorcjum dowód pracy Ethereum**.

Wdrożenie szablonu przeprowadzi Konfigurowanie zużycie pierwszego elementu członkowskiego w sieci. Przepływ wdrożenia jest podzielona na pięć kroków: Podstawowe informacje dotyczące pakietu Operations Management Suite, wdrożenia regiony, Network Activity block size i wydajność, Ethereum ustawienia.

### <a name="basics"></a>Podstawy

W obszarze **podstawy**, określ wartości dla parametrów standardowe dla wszystkich wdrożeń, takich jak subskrypcji, grupy zasobów i właściwości podstawowe maszyny wirtualnej.

![Podstawy](./media/ethereum-deployment/sample-deployment.png)

Nazwa parametru|Opis| Dozwolone wartości|Wartości domyślne
---|---|---|---
Utwórz nową sieć lub dołączanie do istniejącej sieci?|Utwórz nową sieć lub przyłącz istniejące sieci konsorcjum|Utwórz nowy istniejące sprzężenia|Create New (Utwórz nowe)
Wdrażanie sieci, która będzie częścią konsorcjum?|Sieć konsorcjum umożliwia przyszłych wdrożeń do dołączenia do tej sieci (widoczna gdy *Utwórz nowy* zaznaczono powyżej)|Konsorcjum autonomiczny|Autonomiczna
Prefiks zasobów |Ciąg używany jako podstawa do nadawania nazw zasobów (2 – 4 znaków alfanumerycznych). Unikatowy skrót jest dołączany na początku ciągu znaków dla niektórych zasobów, gdy jest dołączany informacji specyficznych dla zasobów.|Alfanumerycznym o długości od 2 do 4|Nie dotyczy
Nazwa użytkownika maszyny Wirtualnej| Nazwa użytkownika administratora w każdej wdrożonej maszyny wirtualnej (tylko znaki alfanumeryczne)|1 - 64 znaki |gethadmin
Typ uwierzytelniania|Metoda uwierzytelniania do maszyny wirtualnej. |Klucz publiczny hasła lub protokołu SSH|Hasło
Hasło (typ uwierzytelniania = hasło)|Hasło dla konta administratora dla każdej z wdrożonych maszyn wirtualnych. Hasło musi zawierać 3 z następujących wymagań: 1 Wielka litera, 1 mała litera, 1 cyfra i 1 znak specjalny. <br />Gdy wszystkie maszyny wirtualne początkowo miały to samo hasło, możesz zmienić hasło po zainicjowaniu obsługi administracyjnej.|12 - 72 znaków|Nie dotyczy
Klucz SSH (typ uwierzytelniania = klucz publiczny)|Klucz protokołu secure shell, używany do logowania zdalnego.|| Nie dotyczy
Subskrypcja| Subskrypcja, dla której chcesz wdrożyć sieci konsorcjum||Nie dotyczy
Grupa zasobów| Grupa zasobów, dla której chcesz wdrożyć sieci konsorcjum.||Nie dotyczy
Lokalizacja| Region platformy Azure dla grupy zasobów. ||Nie dotyczy

### <a name="operations-management-suite"></a>Operations Management Suite

Operations Management Suite (OMS) umożliwia skonfigurowanie zasobów pakietu OMS w sieci. Pakiet OMS będzie zbierać i powierzchni przydatne metryki i dzienniki z Twojej sieci, umożliwiając szybkie sprawdzenie kondycji sieci lub debugowania problemów. Bezpłatne oferty pakietu OMS zostaną elegancko nie powieść się po przekroczeniu pojemności.

![Tworzenie nowego pakietu OMS](./media/ethereum-deployment/new-oms.png)

Nazwa parametru|Opis| Dozwolone wartości|Wartości domyślne
---|---|---|---
Łączenie do istniejącej usługi OMS|Utwórz nowe wystąpienie dzienniki usługi Azure Monitor, lub Dołącz do istniejącego wystąpienia|Utwórz nowy istniejące sprzężenia|Tworzenie nowej lokalizacji dzienników usługi Azure Monitor|Region, w którym zostanie wdrożone nowe dzienniki usługi Azure Monitor (widoczny, jeśli *Utwórz nową* jest zaznaczone)
Istniejący identyfikator obszaru roboczego pakietu OMS|Identyfikator obszaru roboczego istniejącego wystąpienia (widoczny, jeśli *Dołącz do istniejącego* wybrano) warstwy usługi OMS|Wybierz warstwę cenową dla nowego wystąpienia. Więcej informacji o https://azure.microsoft.com/pricing/details/log-analytics/ (widoczny, jeśli *Dołącz do istniejącego* jest zaznaczone)|Bezpłatne autonomicznego na węzeł|Bezpłatna
Istniejącym kluczem głównym pakietu OMS|Klucz podstawowy używany do łączenia z istniejącym wystąpieniem pakietu OMS (widoczny, jeśli *Dołącz do istniejącego* jest zaznaczone)

### <a name="deployment-regions"></a>Wdrożenia regiony

Następnie w obszarze **wdrożenia regiony**, określ dane wejściowe dla **liczba regiony** wdrażanie sieci konsorcjum i wybór regiony platformy Azure na podstawie liczby regionów, biorąc pod uwagę. Użytkownika można wdrożyć w maksymalnie pięciu regionach.

![Wdrożenia regiony](./media/ethereum-deployment/deployment-regions.png)

Nazwa parametru| Opis| Dozwolone wartości |Wartości domyślne
---|---|---|---
Liczba regiony| Liczbie regionów, aby wdrożyć sieć konsorcjum|1, 2, 3, 4, 5| 2
Pierwszym regionie| Pierwszy region, aby wdrożyć sieć konsorcjum|Dozwolone wszystkie regiony platformy Azure| Zachodnie stany USA
Drugi region |Drugi region, aby wdrożyć sieć konsorcjum (widoczne tylko wtedy, gdy liczba regionów jest wybrany jako 2)|Dozwolone wszystkie regiony platformy Azure| Wschodnie stany USA
Trzeciego regionu| Trzeciego regionu na wdrażanie sieci konsorcjum (widoczne tylko wtedy, gdy liczba regionów jest wybrany jako 3)|Dozwolone wszystkie regiony platformy Azure| Środkowe stany USA
Czwarty regionu| Czwarty region, aby wdrożyć sieć konsorcjum (widoczne tylko wtedy, gdy liczba regionów jest wybrany jako 4)|Dozwolone wszystkie regiony platformy Azure| Wschodnie stany USA 2
Piąty regionu| Piąty region, aby wdrożyć sieć konsorcjum (widoczne tylko wtedy, gdy liczba regionów jest wybrany jako 5)|Dozwolone wszystkie regiony platformy Azure| Zachodnie stany USA 2

### <a name="network-size-and-performance"></a>Network Activity block size i wydajności

Następnie w obszarze **rozmiaru i wydajność sieci** Określ dane wejściowe dla rozmiaru sieci konsorcjum. Takie jak liczba i rozmiar węzłów wyszukiwania i węzłów transakcji.

![Network Activity block size i wydajności](./media/ethereum-deployment/network-size-performance.png)

Nazwa parametru |Opis |Dozwolone wartości| Wartości domyślne
---|---|---|---
Liczba węzłów wyszukiwania|Liczba wdrożonych na region węzłów wyszukiwania|2 - 15| 2
Wydajność magazynu węzła wyszukiwania|Typ dysku zarządzanego kopii każdego z węzłów wdrożonego wyszukiwania.|Standardowa lub Premium|Standardowa (Standard)
Rozmiar maszyny wirtualnej węzła wyszukiwania|Rozmiar maszyny wirtualnej, używany do wyszukiwania węzłów.|Standardowa A <br />Standard D <br />Standardowa D-v2 <br />Standardowa serii F <br />Standard DS <br />i FS standardowe|Standardowa D1v2
Liczba węzłów transakcji o zrównoważonym obciążeniu|Liczba węzłów transakcji do aprowizowania w ramach sieci.|1 - 5| 2
Wydajność magazynu węzła transakcji|Typ dysku zarządzanego kopii każdego z węzłów wdrożonego transakcji.|Standardowa lub Premium|Standardowa (Standard)
Rozmiar maszyny wirtualnej węzła transakcji|Rozmiar maszyny wirtualnej, używany dla transakcji węzłów.|Standardowa A <br />Standard D <br />Standardowa D-v2 <br />Standardowa serii F <br />Standard DS <br />i FS standardowe|Standardowa D1v2

### <a name="ethereum-settings"></a>Ustawienia Ethereum

Następnie w obszarze **ustawienia Ethereum**, określ ustawienia konfiguracji odnoszące się do Ethereum, takie jak sieci ID i Ethereum konta hasła lub genesis bloku.

![Ustawienia Ethereum](./media/ethereum-deployment/standalone-leader-deployment.png)

Nazwa parametru |Opis |Dozwolone wartości|Wartości domyślne
---|---|---|---
Identyfikator ConsortiumMember|Identyfikator skojarzony z każdym elementem członkowskim należących do sieci konsorcjum używane do konfigurowania przestrzeni adresów IP w celu uniknięcia kolizji. <br /><br />Identyfikator elementu członkowskiego powinna być unikatowa w różne organizacje w tej samej sieci. Identyfikator unikatowy elementu członkowskiego jest potrzebny, nawet wtedy, gdy w tej samej organizacji wdraża do wielu regionów.<br /><br />Zanotuj wartość tego parametru, ponieważ musisz udostępnić go innym członkom przyłączany.|0 - 255
Identyfikator sieci Ethereum|Identyfikator sieci konsorcjum sieci Ethereum wdrażane. Każda sieć Ethereum ma swój własny identyfikator sieci 1 oznacza identyfikator sieci publicznej. Podczas dostępu do sieci jest ograniczone do węzłów wyszukiwania, nadal zaleca się użycie dużą liczbą, aby uniknąć kolizji.|5 - 999,999,999| 10101010
Blok genesis niestandardowe|Opcję, aby automatycznie wygenerować bloku genesis lub podaj niestandardowego.|Tak/Nie| Nie
Hasło do konta Ethereum (bloku genesis niestandardowe = No)|Hasło administratora, używany do zabezpieczania konta Ethereum zaimportowane do każdego węzła. Hasło musi zawierać: 1 Wielka litera, 1 małą literę i numer 1.|co najmniej 12 znaków.|Nie dotyczy
Hasło klucza prywatnego Ethereum (niestandardowe genesis bloku = No)|Hasło używane do generowania klucza prywatnego ECC skojarzone z kontem Ethereum domyślny, który jest generowany. Wcześniej wygenerowany klucz prywatny nie musi jawnie przekazać.<br /><br />Za pomocą losowości wystarczającą do zapewnienia silnego klucza prywatnego i bez nakładania się z innymi członkami konsorcjum, należy wziąć pod uwagę hasła. Hasło musi zawierać co najmniej: 1 Wielka litera, 1 małą literę i numer 1.<br /><br />Należy pamiętać o tym, jeśli dwa elementy członkowskie, użyj tego samego hasła konta generowane będą takie same. Hasło jest przydatne, jeśli jednej organizacji jest w trakcie wdrażania w różnych regionach i chce udostępnić na jednym koncie (monet podstawowej) we wszystkich węzłach.|co najmniej 12 znaków.|Nie dotyczy
Genesis block (Blokuj genesis niestandardowe = Yes)|Ciąg JSON reprezentujący niestandardowe genesis bloku. Szczegółowe informacje na temat formatu bloku genesis w tym miejscu można znaleźć w sekcji sieci niestandardowe.<br /><br />Nadal zostanie utworzone konto Ethereum podczas podawania bloku genesis niestandardowych. Należy wziąć pod uwagę, określając prefunded konta Ethereum w bloku genesis nie oczekiwania wyszukiwania.|Valid JSON |Nie dotyczy
Wspólny klucz połączenia|Klucz współużytkowany dla połączenia między bramami sieci Wirtualnej.| co najmniej 12 znaków.|Nie dotyczy
Adres URL danych konsorcjum|Adres URL wskazujący konsorcjum odpowiednie dane konfiguracji, dostarczone przez innego użytkownika wdrożenia. <br /><br />Te informacje, znajduje się już połączoną elementu członkowskiego, który ma wdrożenia. Jeśli wdrożono pozostałej części sieci, adres URL jest szablon wdrożenia danych wyjściowych o nazwie konsorcjum danych.||Nie dotyczy
Bramy sieci wirtualnej, aby nawiązać połączenie|Ścieżka zasobu bramy sieci wirtualnej, którym chcesz się połączyć.<br />Te informacje, znajduje się już połączoną elementu członkowskiego, który ma wdrożenia. Jeśli wdrożono pozostałej części sieci, adres URL jest w danych wyjściowych wdrożenia szablonu, o nazwie CONSORTIUM_MEMBER_GATEWAY_ID. Uwaga: Należy użyć tego samego członka konsorcjum adresu URL i bramy sieci wirtualnej zasobów danych.||Nie dotyczy
Punkt końcowy elementu równorzędnego informacji rejestratora|Punkt końcowy informacji elementu równorzędnego dostarczone przez innego użytkownika wdrożenia|Nieprawidłowy punkt końcowy pierwszego elementu członkowskiego w konsorcjum|Nie dotyczy
Klucz elementu równorzędnego informacji rejestratora|Klucz podstawowy informacji elementu równorzędnego dostarczone przez innego użytkownika wdrożenia|Nieprawidłowy klucz podstawowy pierwszego elementu członkowskiego w konsorcjum|Nie dotyczy

### <a name="summary"></a>Podsumowanie

Klikaj elementy podsumowanie, aby wyświetlić dane wejściowe określone, a także aby uruchomić podstawową walidację przed wdrożeniem.

![Podsumowanie](./media/ethereum-deployment/summary.png)

Przejrzyj postanowienia prawne i ochrona prywatności, a następnie kliknij przycisk **zakupu** do wdrożenia. Jeśli wdrożenie zawiera więcej niż jednym regionie lub dotyczy sieci konsorcjum, ten szablon wstępnie wdraża niezbędne bram sieci VPN do obsługi łączności sieciowej z innymi członkami. Wdrożenia bramy może potrwać do 45 minut 50.

## <a name="post-deployment-sanity-checks"></a>Po wdrożeniu wykonuje testów

### <a name="administrator-page"></a>Strona administratora

Po pomyślnym zakończeniu wdrożenia i wszystkie zasoby zostały udostępnione, możesz przejść do strony administratora, aby uzyskać wgląd w sieć łańcucha bloków i wykonuje sprawdzanie stanu wdrożenia. Adres URL strony administratora jest nazwa DNS modułu równoważenia obciążenia; jest obecny w sekcji danych wyjściowych o nazwie ADMIN_SITE wdrożenie szablonu.

Aby ją znaleźć, wybierz grupę zasobów, która została wdrożona. Następnie wybierz **Przegląd**i kliknij link bezpośrednio pod **wdrożeń** pokazujący liczbę, która zakończyła się pomyślnie.

![Omówienie grup zasobów](./media/ethereum-deployment/resource-overview.png)

Nowy ekran pokazuje historię wdrożenia. Wybierz pierwszy zasób wdrożenia (np. microsoft-azure-blockchain.azure — łańcuch bloków-dodatku servi...) i poszukaj **dane wyjściowe** sekcji w dolnej części strony. Zostanie wyświetlony adres URL strony administratora, parametr wyjściowy szablonu wdrożenia jako ADMIN_SITE na liście.

![Wdrożenia zasobów](./media/ethereum-deployment/resource-deployments.png)

![Dane wyjściowe wdrożenia](./media/ethereum-deployment/deployment-output.png)

Aby uzyskać dostęp do strony administratora, skopiuj **witryna administratora** danych wyjściowych, a następnie otwórz go w innej karty.

Na stronie administratora możesz uzyskać ogólne omówienie topologii, wdrożone, przeglądając sekcji Ethereum stan węzła. Zawiera wszystkie nazwy hostów w węźle, ich liczby elementów równorzędnych i najnowszych bloku widoczna. Liczba elementów równorzędnych dla każdego węzła jest między co najmniej jeden mniejsza niż *łączna liczba węzłów* i liczba skonfigurowanych maksymalna elementu równorzędnego. Należy pamiętać, że liczba elementów równorzędnych nie ogranicza liczbę węzłów, które mogą być wdrażane w ramach sieci.
Od czasu do czasu zostanie wyświetlony licznik elementów równorzędnych powodować wahania i być mniejsze niż (całkowita liczba węzłów — 1). Różnica w liczbie nie zawsze jest znakiem, że węzły są złej kondycji, ponieważ rozwidlenia w księdze może spowodować niewielkie zmiany w liczbie elementów równorzędnych. Na koniec można sprawdzić najnowsze bloku widoczne dla każdego węzła w sieci do ustalenia rozwidlenia lub spowolnienia w systemie.

![Stan węzła](./media/ethereum-deployment/node-status.png)

Stan węzła są odświeżane co 10 sekund. Załaduj ponownie stronę za pośrednictwem przeglądarki lub **Załaduj ponownie** przycisk, aby zaktualizować widoku.

### <a name="oms-portal"></a>Portal pakietu OMS

Portalu pakietu OMS można znaleźć, klikając łącze w danych wyjściowych wdrożenia (OMSPORTALURL) lub wybierając zasobów pakietu OMS w grupie wdrożonych zasobów.

![ADRES URL PAKIETU OMS](./media/ethereum-deployment/oms-url.png)

![Zasób pakietu OMS](./media/ethereum-deployment/oms-resource.png)

Najpierw portalu będą wyświetlane statystyki sieci wysokiego poziomu, jako Przegląd.

![Omówienie pakietu OMS](./media/ethereum-deployment/oms-overview.png)

Kliknięcie w obszarze Przegląd nastąpi bezpośrednie przekierowanie do portalu, aby wyświetlić statystyki każdego węzła.

![Statystyki na węzeł](./media/ethereum-deployment/per-node-stats.png)

### <a name="accessing-nodes"></a>Uzyskiwanie dostępu do węzłów

Możesz zdalnie nawiązać maszyn wirtualnych dla węzłów transakcji za pośrednictwem protokołu SSH przy użyciu klucza nazwy użytkownika i hasła/SSH podana administratora. Ponieważ maszyn wirtualnych węzła transakcji nie mają własnych publicznych adresów IP, należy przejść przez moduł równoważenia obciążenia i określ numer portu. Polecenie SSH, aby uruchomić dostępu do pierwszego węzła transakcji znajduje się w parametr wyjściowy szablonu wdrożenia jako **SSH_TO_FIRST_TX_NODE** (Aby uzyskać przykładowe wdrożenie: ssh -p 4000 gethadmin@leader4vb.eastus.cloudapp.azure.com). Aby przejść do transakcji dodatkowe węzły, należy zwiększyć numer portu za pomocą jednej (na przykład, pierwszy węzeł transakcji jest na porcie 4000).

Ponieważ maszyny wirtualne, na których uruchamianie węzłów wyszukiwania nie są dostępne z zewnątrz, musisz przejść za pomocą jednego z węzłów transakcji. Po utworzeniu sesję SSH z węzłem transakcji, należy zainstalować klucz prywatny w węźle transakcji, lub użyj hasła, aby uruchomić sesję SSH w każdym z węzłów wyszukiwania.

**Uwaga**

Nazwy hostów można uzyskać od administratora lokacji lub w witrynie Azure portal. W witrynie Azure portal, nazwy hostów węzłów obecny w zasobach zestawu (VMSS) skali maszyny wirtualnej znajduje się w obszarze **wystąpień**, który różni się od rzeczywistej nazwy hostów. Na przykład nazwy hosta w witrynie Azure portal może wyglądać jak **reg1_0-mn-asdfmv** , ale oznaczałoby faktyczną nazwą hosta **mn-asdfmv-reg**.

Na przykład:

Nazwa hosta na portalu Azure| Faktyczną nazwą hosta
---|---
reg1_0-Mn-ethwvu| mn-ethwvu-reg1000000
reg1_1-Mn-ethwvu |mn-ethwvu-reg1000001
reg1_2-Mn-ethwvu |reg1000002-Mn-ethwvu

## <a name="adding-a-new-consortium-member"></a>Dodawanie nowego członka konsorcjum

### <a name="sharing-data"></a>Udostępnianie danych

Jako pierwszy element członkowski (lub połączonych) konsorcjum musisz podać inni członkowie kilka rodzajów informacji, dzięki czemu mogą dołączyć i nawiązania połączenia. W szczególności:

1. **Udostępnione dane konfiguracyjne konsorcjum**: Istnieje zestaw danych, który jest używany do organizowania Ethereum połączenie między dwoma elementami. Niezbędne informacje, w tym bloku genesis, identyfikator sieci konsorcjum i węzłów rozruchowych są zapisywane do pliku na węzłach transakcji lidera lub innego członka wdrożone. Lokalizacja tego pliku znajduje się w parametr wyjściowy szablonu wdrożenia o nazwie **dane konsorcjum**.
2. **Komunikacja równorzędna informacje o punkcie końcowym**: Elementu równorzędnego informacje rejestratora punkt końcowy można pobrać informacji o wszystkich węzłów, które są już połączone z siecią Ethereum od liderów lub innego członka wdrożenia. Magazyny DB zbiór informacji dotyczących każdego węzła połączone w sieci, informacje, takie jak nazwa hosta węzła, prywatny adres IP itp. Jest to parametr wyjściowy szablonu wdrożenia o nazwie **PEER_INFO_ENDPOINT**.
3. **Komunikacja równorzędna informacje o kluczu podstawowym**: Klucz podstawowy Rejestrator informacji elementu równorzędnego służy do uzyskania dostępu do liderów lub innego członka równorzędnej informacji klucza podstawowego. Jest to parametr wyjściowy szablonu wdrożenia o nazwie **PEER_INFO_PRIMARY_KEY**.


4. **Bramy sieci Wirtualnej**: Każdy element członkowski ustanawia połączenie z siecią całego łańcucha bloków za pomocą istniejącego elementu członkowskiego. Aby połączyć sieć Wirtualną, należy ścieżka zasobu do bramy sieci Wirtualnej, elementu członkowskiego, z którym chcesz się połączyć. Jest to parametr wyjściowy szablonu wdrożenia o nazwie **CONSORTIUM_MEMBER_GATEWAY_ID**.
5. **Klucz wspólny**: Wstępnie ustaloną klucz tajny między dwoma składnikami sieci konsorcjum, które są nawiązywania połączenia. Jest ciągiem alfanumerycznym o długości (od 1 do 128 znaków), który został uzgodniony poza kontekstem wdrożenia. (Na przykład **MySharedKeyAbc123**)

### <a name="acceptance-of-new-member"></a>Akceptacja nowego elementu członkowskiego

W tym kroku powinna być podejmowana po przyłączany członek został wdrożony pomyślnie sieci. Zanim członka można dołączyć sieci oraz transakcji ruch jest widoczny, istniejącego posiadacza wykonać konfiguracji końcowej swojej bramy sieci VPN, aby akceptował połączenia. Oznacza to, że węzły Ethereum przyłączany elementu członkowskiego nie będzie działać, dopóki nie zostanie nawiązane połączenie. Ta konfiguracja może odbywać się za pośrednictwem programu PowerShell lub interfejsu wiersza polecenia xPlat. Skrypt programu PowerShell w module i interfejs xPlat CLI są także przechowywane w węźle transakcji oraz dane konsorcjum. Lokalizacja skryptu jest parametrów wyjściowych wdrożenia o nazwie **PS moduł, pary bramy w-** i **pary-BRAMA-AZURE-CLISCRIPT**, odpowiednio.

**Instalator programu PowerShell/interfejsu wiersza polecenia**

Dodatkowe informacje na temat sposobu rozpoczęcie korzystania z poleceń cmdlet programu Azure PowerShell i interfejs xPlat platformy Azure CLI można znaleźć w dokumentacji platformy Azure.

Konieczne będzie najnowszą wersję poleceń cmdlet platformy Azure zainstalowany lokalnie i w sesji Otwórz. Upewnij się zalogować się do sesji przy użyciu poświadczeń subskrypcji platformy Azure.

**PowerShell: Nawiąż połączenie**

Pobieranie modułu programu PowerShell i zapisz go lokalnie. Lokalizacja modułu programu PowerShell jest określona jako **PS moduł, pary bramy w-** parametr wyjściowy szablonu wdrożenia.

Jeśli nie jest jeszcze włączone, należy użyć **Set-ExecutionPolicy** polecenie cmdlet służące do sesji lokalnej zezwolić na uruchamianie niepodpisanych modułu.

**Set-ExecutionPolicy Unrestricted CurrentUser**

Następnie zaloguj się do Twojej subskrypcji platformy Azure, w którym została wdrożona przy użyciu wdrożenia lidera

**Login-AzureRmAccount**

Następnie zaimportuj moduł:

**Import-Module <filepath to downloaded file>**

Na koniec uruchom funkcję przy użyciu odpowiednich danych wejściowych:

- **MyGatewayResourceId:** Ścieżka zasobu bramy. Jest to parametr wyjściowy szablonu wdrożenia o nazwie **CONSORTIUM_MEMBER_GATEWAY_ID**.
- **OtherGatewayResourceId:** Ścieżka zasobu bramy przyłączany elementu członkowskiego. To jest świadczona przez przyłączany elementu członkowskiego i parametr wyjściowy szablonu wdrożenia programu o nazwie **CONSORTIUM_MEMBER_GATEWAY_ID**.
- **ConnectionName:** Nazwa do identyfikowania tego połączenia bramy.
- **Klucz współużytkowany:** Wstępnie ustaloną klucz tajny między członkami sieci konsorcjum, które są nawiązywania połączenia.

**CreateConnection** -MyGatewayResourceId <resource path of your Gateway> - OtherGatewayResourceId < ścieżka zasobu bramy przyłączany składowej > - ConnectionName Mojepołączenie - SharedKey "MySharedKeyAbc123"

**Interfejs wiersza polecenia xPlat: Nawiąż połączenie**

Pobierz skrypt wiersza polecenia platformy Azure, a następnie zapisz go lokalnie. Lokalizacja skryptu wiersza polecenia platformy Azure jest określona w parametrze wdrażania szablonu o nazwie **pary-BRAMA-AZURE-skrypt interfejsu wiersza polecenia —**.

Uruchom skrypt przy użyciu odpowiednich danych wejściowych:

- **MyGatewayResourceId:** Ścieżka zasobu bramy. Jest to parametr wyjściowy szablonu wdrożenia o nazwie **CONSORTIUM_MEMBER_GATEWAY_ID**.
- **OtherGatewayResourceId:** Ścieżka zasobu bramy przyłączany elementu członkowskiego. To jest dostarczany przez przyłączany elementu członkowskiego, a parametr szablonu wdrożenia ich wdrożenia o nazwie **CONSORTIUM_MEMBER_GATEWAY_ID**.
- **ConnectionName:** Nazwa do identyfikowania tego połączenia bramy.
- **Klucz współużytkowany:** Wstępnie ustaloną klucz tajny między członkami sieci konsorcjum, które są nawiązywania połączenia.
- **Lokalizacja:** Region platformy Azure, w której wdrożono zasób bramy.

``` powershell
az network vpn-connection create --name $ConnectionName --resource-group
$MyResourceGroup --vnet-gateway1 $MyGatewayResourceId --shared-key $SharedKey --vnet-
gateway2 $OtherGatewayResourceId --enable-bgp
```

Architektura będzie następująca po pomyślnym skonfigurowaniu połączenia między **lidera** i **elementu członkowskiego** wdrożeń.

![Architektura lidera i elementów członkowskich](./media/ethereum-deployment/leader-member.png)

## <a name="fund-new-member-account"></a>Nowe konto Członkowskie funduszami

### <a name="generated-genesis-block"></a>Wygenerowany genesis bloku

Jako pierwszy element członkowski konta Ethereum jest finansowane z jednego bilionów ether Jeśli wdrożenie generuje blok genesis (niestandardowe Genesis bloku = nie). Inne elementy członkowskie będą miały konta, które nie jest wstępnie finansowane i należy poczekać do Ether zgodnie z ich węzły wyszukiwania zacząć analizować bloków. Aby uniknąć konieczności oczekiwania przez nowe składowe dla Ether, należy jawnie shuttleworth przyłączany członków Ethereum konta.

Aby to zrobić, dołączenie do elementów członkowskich musi udostępnić konta Ethereum, na którym jest wyświetlana na ich administracyjnej witryny internetowej. Można następnie użyć administracyjnej witryny internetowej transferowania Ether z Twojego konta do swojego konta, wprowadzając podane konto.

### <a name="custom-genesis-block"></a>Blok genesis niestandardowe

Jeśli podano bloku genesis niestandardowych przy użyciu określonego konta Ethereum można użyć MetaMask lub innego narzędzia do przenoszenia ether przy jego użyciu określone konto kontu wstępnie wygenerowanego Ethereum widoczne w administracyjnej witrynie sieci Web. Aby uzyskać instrukcje dotyczące sposobu używania MetaMask, zobacz [tworzenia konta Ethereum](#create-ethereum-account).

Blok genesis niestandardowego jest dostarczane bez konta lub nie masz dostępu do wszelkich uprzednio przydzielony kont, należy poczekać, aż węzły wyszukiwania zacząć analizować do generowania Ether na koncie (monet podstawowej). Jak szybko są generowane środków zależy od poziomu trudności, określaną w bloku genesis niestandardowych.

## <a name="create-ethereum-account"></a>Utwórz konto Ethereum

Aby utworzyć dodatkowe konto, można użyć różnych rozwiązań. Jedno z rozwiązań jest MetaMask, rozszerzenie przeglądarki Chrome, zapewniająca magazynu tożsamości i połączenia z siecią Ethereum publicznych, test lub niestandardowy. MetaMask formulates transakcji, aby zarejestrować konto w sieci.

Ta transakcja, podobnie jak inne transakcje, przejdziesz na jednym z węzłów transakcji i ostatecznie reklamodawcach do bloku, jak pokazano poniżej.

![Węzeł transakcji](./media/ethereum-deployment/transaction-node.png)

Aby zainstalować rozszerzenia w przeglądarce Chrome, przejdź do dostosowania i kontrolowania Google Chrome (przycisk przepełnienie) > więcej narzędzi > Rozszerzenia > Uzyskaj więcej rozszerzeń i wyszukaj MetaMask.

![Rozszerzenie MetaMask](./media/ethereum-deployment/metamask-extension.png)

Po zainstalowaniu Otwórz MetaMask i Utwórz nowy magazyn. Domyślnie Magazyn zostaną podłączone do testowej sieci Morden. Zmień ten element, aby połączyć się z siecią wdrożonej konsorcjum prywatnych do modułu równoważenia obciążenia przed węzłów transakcji. Z danych wyjściowych szablonu, należy pobrać narażonych końcowych wywołań RPC Ethereum w porcie 8545, o nazwie `ETHEREUM-RPC-ENDPOINT`i wprowadź go na niestandardowych RPC, jak pokazano poniżej.

![Ustawienia MetaMask](./media/ethereum-deployment/metamask-settings.png)

Tworząc magazynu, możesz utworzyć portfela, zawierającą konto. Aby utworzyć dodatkowych kont, wybierz **kont o przełącznikiem** i następnie **+** przycisku.

![MetaMask Tworzenie konta](./media/ethereum-deployment/metamask-create-account.png)

### <a name="initiate-initial-ether-allocation"></a>Zainicjuj początkowej alokacji Ether

Za pomocą strony administratora można sformułować transakcji do przesłania Ether ze wstępnie przydzielić konta na inne konto Ethereum. To przeniesienie Ether jest transakcji, która jest wysyłany do węzła transakcji i reklamodawcach do bloku, jak pokazano poniżej.

![Węzeł transakcji](./media/ethereum-deployment/transaction-node-mined.png)

Za pomocą ikony Schowka w aplikacji wallet MetaMask skopiuj adres konta Ethereum, do którego chcesz przenieść ether i wróć do strony administratora. Wklej skopiowany konta do pola wejściowego do przesłania 1000 ether ze wstępnie przydzielić konto Ethereum do nowo utworzonego konta. Kliknij przycisk **przesyłania** i poczekaj na transakcji, aby być reklamodawcach do bloku.

![Stan węzła](./media/ethereum-deployment/node-status2.png)

Gdy transakcja zostaje zatwierdzona do bloku wyszukane, saldo konta w MetaMask konta będą odzwierciedlać transferu 1000 Ether.

![MetaMask transfer](./media/ethereum-deployment/metamask-transfer.png)

### <a name="transfer-of-ether-between-accounts"></a>Transfer Ether między kontami

W tym momencie można przystąpić do wykonania transakcji w ramach sieci prywatnej konsorcjum. Najprostsza transakcja jest do przeniesienia Ether z jednego konta. Aby sformułować tego typu transakcji, można użyć MetaMask jeszcze raz przesyłania pieniądze z pierwszego konta powyżej do drugiego konta.

Z **1 portfela** w MetaMask, kliknij przycisk Wyślij. Skopiuj adres drugiej aplikacji wallet utworzone do **adres odbiorcy** wejściowe pola i ilość Ether przeniesienie **kwota** pola wejściowego. Kliknij przycisk **wysyłania** i zaakceptuj transakcji.

![MetaMask wysyłania transakcji](./media/ethereum-deployment/metamask-send.png)

Jeszcze raz gdy transakcja jest reklamodawcach i zatwierdzone do bloku, salda konta są odzwierciedlane w związku z tym. Uwaga: **1 portfela**firmy saldo jest odejmowany Ether więcej niż 15, ponieważ było konieczne uiszczenie wyszukiwania do przetwarzania transakcji.

![Portfela 1](./media/ethereum-deployment/wallet1.png)

![Portfela 2](./media/ethereum-deployment/wallet2.png)

## <a name="next-steps"></a>Kolejne kroki

Teraz możesz przystąpić do skupić się na aplikacji i programowanie inteligentnych kontraktu względem sieci prywatnej konsorcjum łańcucha bloków.
