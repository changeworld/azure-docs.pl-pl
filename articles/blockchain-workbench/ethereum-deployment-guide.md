---
title: Szablon rozwiązania Ethereum konsorcjum dowód pracy
description: Szablon rozwiązania konsorcjum dowód pracy Etherereum umożliwia wdrażanie i konfigurowanie sieci Ethereum konsorcjum wiele elementów członkowskich
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 5/21/2018
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: zeyadr
manager: femila
ms.openlocfilehash: 0ea9bd2c7d23aa227e62858983e7170b771751da
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34655469"
---
# <a name="ethereum-proof-of-work-consortium-solution-template"></a>Szablon rozwiązania Ethereum konsorcjum dowód pracy

Szablon rozwiązania konsorcjum dowód pracy Ethereum zaprojektowano w celu umożliwiają łatwiejsze i szybsze wdrażanie i konfigurowanie sieci Ethereum konsorcjum wiele elementów członkowskich przy minimalnej znajomości Azure i Ethereum.

Niewielki podzbiór danych wprowadzonych przez użytkownika i wdrożenia jednym kliknięciem za pośrednictwem portalu Azure każdy element członkowski mogą zapewnić obsługę administracyjną rozmiaru sieci, przy użyciu rozwiązań usługi obliczenia Azure firmy Microsoft, sieci i usługi magazynu na całym świecie. Każdy element członkowski rozmiaru sieci zawiera zestaw węzłów transakcji z równoważeniem obciążenia, z którego aplikacji lub użytkownika mogą współdziałać przesłać transakcje, zestaw węzłów wyszukiwania do transakcji rekordów i bramy sieci VPN. Krok kolejne połączenie nawiązuje połączenie bramy, aby utworzyć sieć pełni skonfigurowany blockchain wiele elementów członkowskich.

## <a name="about-blockchain"></a>O blockchain

Dla tych osób, które są nowe społeczności blockchain wersji tego rozwiązania jest doskonałym możliwość Dowiedz się więcej o technologii w sposób łatwy i można skonfigurować na platformie Azure. Aby rozpocząć, zalecamy wdrożenie prostsze topologii sieci Ethereum autonomiczny z ten przewodnik krok po kroku, przed rozpoczęciem budowania limit sieci konsorcjum wiele elementów członkowskich.

### <a name="mining-node-details"></a>Szczegóły węzła wyszukiwania

Węzły, które Moje transakcje są oddzielone od węzłów, które akceptują transakcji, aby upewnić się, że dwie akcje są nie rywalizują o tych samych zasobów.

Członek konsorcjum można udostępnić do pięciu regionów, zawierający co najmniej jeden węzeł wyszukiwania przez dysków zarządzanych. Co najmniej jeden węzeł w regionie są skonfigurowane jako węzeł rozruchu do obsługi dynamiczne odnajdowanie węzłów w sieci. Węzły wyszukiwania komunikować się z innych węzłów wyszukiwania przejdzie do konsensu na stan podstawowej księgi rozproszonych. Nie istnieje potrzeba aplikacji należy pamiętać o lub komunikowania się z tych węzłów. Koncentrujące się w sieciach prywatnych, węzły wyszukiwania są odizolowane od ruchu przychodzącego publicznego Internetu można dodać dodatkowej warstwę ochrony. Ruch wychodzący jest dozwolony, ale nie do portu odnajdywania Ethereum.

Wszystkie węzły mają stabilną wersję klienta Przejdź Ethereum (Geth) i są skonfigurowane jako węzły wyszukiwania. Nie dostarczyła bloku genesis niestandardowych, we wszystkich węzłach należy użyć tego samego adresu Ethereum i parę kluczy, która jest chroniona przez Ethereum hasło konta. Podane hasło Ethereum służy do generowania domyślnego konta (coinbase) dla każdego węzła wyszukiwania. Jako węzły wyszukiwania, min, pobierają opłaty, które są dodawane do tego konta.

Liczba węzłów wyszukiwania dla każdego elementu członkowskiego konsorcjum zależy od rozmiaru sieci potrzeby i ilości mocy wyznaczania wartości skrótu przeznaczony do każdego członka. Im większa sieci, więcej węzłów, które muszą zostać złamane uzyskanie korzyści nieuczciwej. Szablon obsługuje maksymalnie 15 węzłów wyszukiwania dla regionu udostępniane za pomocą zestawów skali maszyny wirtualnej.

### <a name="transaction-node-details"></a>Szczegóły transakcji węzła

Członek konsorcjum ma również zestaw węzłów transakcji z równoważeniem obciążenia. Te węzły są dostępne z spoza sieci wirtualnej, dzięki czemu aplikacje mogą używać tych węzłów można przesłać transakcji lub wykonać inteligentne umów w ramach sieci blockchain. Wszystkie węzły mają stabilną wersję klienta Przejdź Ethereum (Geth) i są skonfigurowane do obsługi pełną kopię księgi rozproszonych. Jeśli nie podano bloku genesis niestandardowych, te węzły używać tego samego konta Ethereum, chroniony hasłem konta Ethereum.

Węzły transakcji są równoważeniem obciążenia w ramach zestawu do obsługi wysokiej dostępności dostępności. Szablon obsługuje do pięciu węzłów transakcji udostępniane za pomocą zestawów skali maszyny wirtualnej.

### <a name="log-analytics-details"></a>Szczegóły analizy dziennika

Dla każdego wdrożenia także tworzy nowe wystąpienie analizy dzienników lub dołączyć do istniejącego wystąpienia. Umożliwia monitorowanie różnych metryk wydajności każdej maszyny wirtualnej, tworzącą sieć wdrożone.

## <a name="deployment-architecture"></a>Architektura wdrażania

### <a name="description"></a>Opis

Ten szablon rozwiązania można wdrożyć na jednym lub wieloma wiele zależności od regionu elementu członkowskiego Ethereum konsorcjum sieci. Sieć wirtualna każdego regionu jest podłączona do innego regionu w topologii łańcucha przy użyciu bramy sieci Wirtualnej i połączenia. Udostępnia również rejestratora, który zawiera informacje wymagane wszystkich węzłów transakcji i analizatora wdrożone w poszczególnych regionach.

### <a name="standalone-and-consortium-leader-overview"></a>Autonomiczne i konsorcjum wiodące — omówienie

![Omówienie wiodące konsorcjum i autonomiczne](./media/ethereum-deployment-guide/leader-overview.png)

### <a name="joining-consortium-member-overview"></a>Sprzęganie konsorcjum omówienie elementu członkowskiego

![Sprzęganie konsorcjum omówienie elementu członkowskiego](./media/ethereum-deployment-guide/member-overview.png)

## <a name="getting-started"></a>Wprowadzenie

Ten proces wymaga subskrypcji Azure, która może obsługiwać wdrażanie kilka zestawów skalowania maszyn wirtualnych i dyskach zarządzanych. Jeśli to konieczne, Utwórz bezpłatnego konta platformy Azure, aby rozpocząć.

Po zabezpieczeniu subskrypcję, przejdź do portalu Azure. Wybierz **+ Utwórz zasób**, witryny Marketplace (Zobacz wszystkie) i wyszukaj **konsorcjum dowód pracy Ethereum**.

Wdrożenie szablonu przeprowadzi Cię przez konfigurację wpływ pierwszego elementu członkowskiego w sieci. Przepływ wdrożenia jest podzielony na pięć kroków: podstawy, Operations Management Suite wdrożenia regionów, rozmiar sieci i wydajności, Ethereum ustawienia.

### <a name="basics"></a>Podstawy

W obszarze **podstawy**, podaj wartości parametrów standardowe wszystkie wdrożenia, takich jak subskrypcji, grupy zasobów i właściwości podstawowej maszyny wirtualnej.

![Podstawy](./media/ethereum-deployment-guide/sample-deployment.png)

Nazwa parametru|Opis| Dozwolone wartości|Wartości domyślne
---|---|---|---
Utwórz nową sieć lub sprzężenia istniejącej sieci?|Utwórz nową sieć lub przyłącz istniejącej sieci konsorcjum|Utwórz nowy istniejących sprzężenia|Create New (Utwórz nowe)
Wdrażanie sieci, która będzie częścią konsorcjum?|Sieć konsorcjum umożliwia przyszłych wdrożeń do dołączenia do tej sieci (widoczne po wybraniu *Utwórz nowy* wybrano powyżej)|Konsorcjum autonomiczny|Autonomiczna
Prefiks zasobów |Parametry używane jako podstawa dla nazw zasobów (2 do 4 znaki alfanumeryczne). Unikatowy skrót jest dołączany na początku ciąg dla niektórych zasobów, gdy informacje specyficzne dla zasobów jest dołączona.|Znaki alfanumeryczne o długości od 2 do 4|Nie dotyczy
Nazwa użytkownika maszyny Wirtualnej| Nazwa użytkownika administratora każdej wdrożonej maszyny wirtualnej (tylko znaki alfanumeryczne)|1 - 64 znaków |gethadmin
Typ uwierzytelniania|Metoda uwierzytelniania do maszyny wirtualnej. |Hasło lub SSH klucza publicznego|Hasło
Hasło (typ uwierzytelniania = hasło)|Hasło dla konta administratora dla każdego z wdrożonych maszyn wirtualnych. Hasło musi zawierać 3 następujących wymagań: 1 wielką literą, 1 małe litery, liczby 1 i 1 znak specjalny. <br />Gdy wszystkie maszyny wirtualne mają początkowo to samo hasło, należy zmienić hasło po zainicjowaniu obsługi administracyjnej.|12 - 72 znaków|Nie dotyczy
Klucz SSH (typ uwierzytelniania = klucz publiczny)|Klucz bezpiecznego powłoki używane do logowania zdalnego.|| Nie dotyczy
Subskrypcja| Subskrypcja, dla której chcesz wdrożyć sieci konsorcjum||Nie dotyczy
Grupa zasobów| Grupy zasobów, dla której chcesz wdrożyć konsorcjum sieci.||Nie dotyczy
Lokalizacja| Region platformy Azure dla grupy zasobów. ||Nie dotyczy



### <a name="operations-management-suite"></a>Operations Management Suite

Blok Operations Management Suite (OMS) pozwala na skonfigurowanie OMS zasobów w sieci. OMS będzie zbierać i powierzchni przydatne metryki i dzienników z sieci, umożliwiając szybkie sprawdzenie kondycji sieci lub debugowania problemów. Bezpłatnej ofercie OMS zakończy się niepowodzeniem bezpiecznie po osiągnięciu pojemności.

![Tworzenie nowego pakietu OMS](./media/ethereum-deployment-guide/new-oms.png)

Nazwa parametru|Opis| Dozwolone wartości|Wartości domyślne
---|---|---|---
Nawiązać połączenia z istniejącego pakietu OMS|Utwórz nowe wystąpienie analizy dzienników lub Połącz z istniejącym wystąpieniem|Utwórz nowy istniejących sprzężenia|Utwórz nową lokalizację analizy dzienników|Region, w którym zostanie wdrożony nowy analizy dzienników (widoczny, jeśli *Utwórz nowy* jest zaznaczone)
Istniejący identyfikator obszaru roboczego OMS|Identyfikator obszaru roboczego z istniejącego wystąpienia (widoczny, jeśli *dołączyć istniejącą* jest zaznaczone) OMS warstwy usług|Wybierz warstwę cenową dla nowego wystąpienia. Więcej informacji na https://azure.microsoft.com/pricing/details/log-analytics/ (widoczny, jeśli *dołączyć istniejącą* jest zaznaczone)|Autonomiczny wolnego na węzeł|Bezpłatna
Istniejący klucz podstawowy OMS|Klucz podstawowy używany do nawiązania połączenia z istniejącym wystąpieniem OMS (widoczny, jeśli *dołączyć istniejącą* jest zaznaczone)

### <a name="deployment-regions"></a>Regiony wdrożenia

Następnie w obszarze **regionów wdrożenia**, określ dane wejściowe dla **liczba regionów** do wdrażania sieci konsorcjum i wybór regiony platformy Azure na podstawie liczby podane regionów. Użytkownika można wdrożyć w maksymalnie pięć regionów.

![Regiony wdrożenia](./media/ethereum-deployment-guide/deployment-regions.png)

Nazwa parametru| Opis| Dozwolone wartości |Wartości domyślne
---|---|---|---
Liczba regionów| Liczba regionów do wdrożenia sieci konsorcjum|1, 2, 3, 4, 5| 2
Pierwszy region| Pierwszy region do wdrożenia sieci konsorcjum|Dozwolone wszystkie regiony platformy Azure| Zachodnie stany USA
Drugi regionu |Drugi region do wdrożenia sieci konsorcjum (widoczny tylko wtedy, gdy liczba regionów został wybrany jako 2)|Dozwolone wszystkie regiony platformy Azure| Wschodnie stany USA
Trzeci regionu| Trzeci region do wdrożenia sieci konsorcjum (widoczny tylko wtedy, gdy liczba regionów został wybrany jako 3)|Dozwolone wszystkie regiony platformy Azure| Środkowe stany USA
Czwarty regionu| Czwarty region do wdrożenia sieci konsorcjum (widoczny tylko wtedy, gdy liczba regionów został wybrany jako 4)|Dozwolone wszystkie regiony platformy Azure| Wschodnie stany USA 2
Piąty regionu| Region piątego do wdrożenia sieci konsorcjum (widoczny tylko wtedy, gdy liczba regionów został wybrany jako 5)|Dozwolone wszystkie regiony platformy Azure| Zachodnie stany USA 2

### <a name="network-size-and-performance"></a>Rozmiar sieci i wydajności

Następnie w obszarze **sieci rozmiaru i wydajności** Określ dane wejściowe dla rozmiaru konsorcjum sieci, takie jak liczba i rozmiar węzłów wyszukiwania i węzłów transakcji.

![Rozmiar sieci i wydajności](./media/ethereum-deployment-guide/network-size-performance.png)

Nazwa parametru |Opis |Dozwolone wartości| Wartości domyślne
---|---|---|---
Liczba węzłów wyszukiwania|Liczba węzłów wyszukiwania wdrożone na region|2 - 15| 2
Wydajność magazynu węzła wyszukiwania|Typ dysków zarządzanych w węzłach wyszukiwania wdrożonych kopii.|Standard lub Premium|Standardowa (Standard)
Rozmiar maszyny wirtualnej węzła wyszukiwania|Rozmiar maszyny wirtualnej, używany dla węzłów wyszukiwania.|Standard A <br />Standardowa D <br />Standardowa D-v2 <br />Standardowa serii F, <br />Standardowa DS <br />i standardowe FS|Standardowe D1v2
Liczba węzłów transakcji ze zrównoważonym obciążeniem|Liczba węzłów transakcji do udostępniania w ramach sieci.|1 - 5| 2
Wydajność magazynu węzła transakcji|Typ dysków zarządzanych w kopii każdego z węzłów wdrożonej transakcji.|Standard lub Premium|Standardowa (Standard)
Rozmiar maszyny wirtualnej w węźle transakcji|Rozmiar maszyny wirtualnej, używany dla transakcji węzłów.|Standard A <br />Standardowa D <br />Standardowa D-v2 <br />Standardowa serii F, <br />Standardowa DS <br />i standardowe FS|Standardowe D1v2

### <a name="ethereum-settings"></a>Ustawienia Ethereum

Następnie w obszarze **ustawienia Ethereum**, określ ustawienia konfiguracji odnoszące się do Ethereum, takie jak identyfikator i Ethereum konta hasła lub genesis bloku siecią.

![Ustawienia Ethereum](./media/ethereum-deployment-guide/standalone-leader-deployment.png)

Nazwa parametru |Opis |Dozwolone wartości|Wartości domyślne
---|---|---|---
Identyfikator ConsortiumMember|Identyfikator skojarzony z każdego członka należących do sieci konsorcjum służąca do konfigurowania przestrzeni adresów IP, aby uniknąć kolizji. <br /><br />Identyfikator elementu członkowskiego powinna być unikatowa w różnych organizacjach w tej samej sieci. Identyfikator unikatowy element członkowski jest potrzebne, nawet wtedy, gdy wdraża tej samej organizacji w wielu regionach.<br /><br />Zwróć uwagę na wartość tego parametru, ponieważ konieczne będzie udostępniać innym członkom łącząca.|0 - 255
Identyfikator sieci Ethereum|Identyfikator sieci dla sieci Ethereum konsorcjum wdrażany. Każda sieć Ethereum ma własny identyfikator sieci, z 1 jest identyfikator sieci publicznej. Podczas dostępu do sieci jest ograniczona do węzłów wyszukiwania, nadal zalecamy używanie wielu Aby uniknąć kolizji.|5 - 999,999,999| 10101010
Blok genesis niestandardowych|Opcja automatycznie wygenerować bloku genesis lub podaj niestandardowego.|Tak/Nie| Nie
Hasło konta Ethereum (niestandardowe genesis bloku = nie)|Hasło administratora, używane do zabezpieczania konta Ethereum zaimportowane do każdego węzła. Hasło musi zawierać następujące czynności: 1 wielką literą, 1 małe litery oraz numer 1.|12 znaków|Nie dotyczy
Hasło klucza prywatnego Ethereum (niestandardowe genesis bloku = nie)|Hasło używane do generowania klucza prywatnego ECC skojarzonego z domyślnego konta Ethereum, który jest generowany. Wstępnie wygenerowany klucz prywatny nie trzeba jawnie przekazano.<br /><br />Rozważ losowości wystarczającą do zapewnienia silnego klucza prywatnego i bez nakładania się z innymi członkami konsorcjum hasło. Hasło musi zawierać co najmniej: 1 wielką literą, 1 małe litery oraz numer 1.<br /><br />Należy zwrócić uwagę, jeśli dwa elementy członkowskie używane to samo hasło konta generowane będą takie same. To samo hasło jest przydatna, jeśli jest w trakcie wdrażania w regionach jednej organizacji i chce udostępnić jednego konta (monety podstawowej) we wszystkich węzłach.|12 znaków|Nie dotyczy
Blok Genesis (niestandardowe genesis bloku = Yes)|Ciąg JSON reprezentujący niestandardowe genesis bloku. Więcej informacji o formacie bloku genesis w tym miejscu można znaleźć w obszarze sieci niestandardowe.<br /><br />Konto Ethereum nadal jest tworzone podczas dostarczania bloku genesis niestandardowych. Należy rozważyć określenie prefunded konta Ethereum w bloku genesis, aby nie czekać na wyszukiwania.|Poprawne dane JSON |Nie dotyczy
Wspólny klucz połączenia|Klucz udostępniony dla połączenia między sieciami Wirtualnymi bramy.| 12 znaków|Nie dotyczy
Adres URL konsorcjum danych|Adres URL wskazujący konsorcjum odpowiednie dane konfiguracji udostępniane przez innego członka wdrożenia. <br /><br />Te informacje są udostępniane przez członka już połączony, który ma wdrożenia. Jeśli wdrożono pozostałej części sieci, adres URL jest dane wyjściowe wdrażanie szablonu, o nazwie konsorcjum danych.||Nie dotyczy
Brama sieci wirtualnej do nawiązania połączenia|Ścieżka zasobu bramy sieci wirtualnej, którym chcesz się połączyć.<br />Te informacje są udostępniane przez członka już połączony, który ma wdrożenia. Jeśli wdrożono pozostałej części sieci, adres URL jest wynik szablonu wdrażania, o nazwie CONSORTIUM_MEMBER_GATEWAY_ID. Uwaga: Należy użyć tego samego członka konsorcjum danych adresu URL i bramy sieci wirtualnej zasobów.||Nie dotyczy
Punkt końcowy elementu równorzędnego informacji rejestratora|Punkt końcowy informacji równorzędnej udostępniane przez innego użytkownika wdrożenia|Nieprawidłowy punkt końcowy pierwszego elementu członkowskiego w konsorcjum|Nie dotyczy
Klucz elementu równorzędnego informacji rejestratora|Klucz podstawowy informacji równorzędnej udostępniane przez innego użytkownika wdrożenia|Nieprawidłowy klucz podstawowy pierwszego elementu członkowskiego w konsorcjum|Nie dotyczy

### <a name="summary"></a>Podsumowanie

Kliknij podsumowania bloku, aby przejrzeć określone dane wejściowe i uruchomić podstawowe sprawdzanie poprawności przed wdrożeniem.

![Podsumowanie](./media/ethereum-deployment-guide/summary.png)

Przejrzyj postanowienia prawne i ochrona prywatności i kliknij przycisk **zakupu** do wdrożenia. Jeśli wdrożenie ma więcej niż jeden region lub jest w sieci konsorcjum, ten szablon wstępnie wdraża niezbędne bramy sieci VPN w celu obsługi łączności sieciowej z innymi członkami. Wdrożenie bramy może potrwać do 45 minut 50.

## <a name="post-deployment-sanity-checks"></a>Post wdrożenia wykonuje testów

### <a name="administrator-page"></a>Strona administratora

Gdy wdrożenie zostało ukończone pomyślnie, a wszystkie zasoby zostały udostępnione, można przejść do strony administratora, aby wyświetlać sieci blockchain i związane z poprawnością Sprawdź stan wdrożenia. Adres URL strony administratora jest nazwą DNS, usługi równoważenia obciążenia; jest obecny w sekcji dane wyjściowe wdrażanie szablonu jako ADMIN_SITE.

Aby znaleźć, wybierz grupę zasobów, która została wdrożona. Następnie wybierz opcję **omówienie**i kliknij link bezpośrednio pod **wdrożeń** którym wyświetlana jest liczba, która zakończyła się pomyślnie.

![Przegląd grup zasobów](./media/ethereum-deployment-guide/resource-overview.png)

Nowy ekran pokazuje historię wdrażania. Wybierz pierwszy zasób wdrażania (na przykład microsoft-azure-blockchain.azure-blockchain-servi...), a następnie wyszukaj **dane wyjściowe** sekcji w dolnej części strony. Zostanie wyświetlony adres URL strony administratora parametr wyjściowy wdrażania szablonu jako ADMIN_SITE na liście.

![Wdrożenia zasobów](./media/ethereum-deployment-guide/resource-deployments.png)

![Dane wyjściowe wdrażanie](./media/ethereum-deployment-guide/deployment-output.png)

Aby uzyskać dostęp do strony administratora, skopiuj **witryna administratora** danych wyjściowych i otworzyć go w innej karty.

Na stronie administracyjnej można uzyskać ogólny przegląd topologię, która została wdrożona, przeglądając sekcji Ethereum stanu węzła. Zawiera wszystkie nazwy hostów węzła, ich liczba elementów równorzędnych i najnowszych bloku widoczna. Liczba elementów równorzędnych dla każdego węzła jest między co najmniej jednego mniejszej niż *łączna liczba węzłów* i liczba skonfigurowany maksymalny elementu równorzędnego. Należy pamiętać, że liczby elementów równorzędnych nie ogranicza liczbę węzłów, które można wdrożyć w sieci.
Od czasu do czasu zobaczysz liczba elementów równorzędnych zmieniają się i być mniejszy niż (łączna liczba węzłów - 1). Różnica w liczbie nie zawsze jest znak, że węzły są złej kondycji, ponieważ rozwidlenia w księdze może spowodować drobne zmiany w liczbie elementów równorzędnych. Na koniec można sprawdzić bloku najnowsze odebrane przez każdy węzeł w sieci w celu ustalenia rozwidlenia lub odchyłki systemu.

![Stan węzła](./media/ethereum-deployment-guide/node-status.png)

Stan węzła są odświeżane co 10 sekund. Załaduj ponownie stronę za pomocą przeglądarki lub **Załaduj ponownie** przycisk, aby zaktualizować widoku.

### <a name="oms-portal"></a>Portal pakietu OMS

Możesz znaleźć portalu OMS, klikając łącze w danych wyjściowych wdrożenia (OMSPORTALURL) lub wybierając OMS zasobów w grupie zasobów wdrożone.

![ADRES URL PAKIETU OMS](./media/ethereum-deployment-guide/oms-url.png)

![Zasób OMS](./media/ethereum-deployment-guide/oms-resource.png)

Statystyki sieci wysokiego poziomu najpierw będzie wyświetlana omówienie portalu.

![Omówienie OMS](./media/ethereum-deployment-guide/oms-overview.png)

Kliknięcie przeglądu zostanie bezpośrednio do portalu, aby wyświetlić statystyki każdego węzła.

![Statystyka każdego węzła](./media/ethereum-deployment-guide/per-node-stats.png)

### <a name="accessing-nodes"></a>Uzyskiwanie dostępu do węzłów

Możesz zdalnie nawiązać maszyn wirtualnych dla węzłów transakcji za pośrednictwem protokołu SSH z klucz administratora podana nazwa użytkownika i hasło/SSH. Ponieważ węzeł transakcji maszyn wirtualnych nie ma własnych publicznych adresów IP, należy przejść przez moduł równoważenia obciążenia i określ numer portu. Wykonanie polecenia SSH dostępu do pierwszego węzła transakcji ma na liście parametr wyjściowy wdrażania szablonu jako **SSH_TO_FIRST_TX_NODE** (dla Przykładowe wdrożenie: ssh -p 4000 gethadmin@leader4vb.eastus.cloudapp.azure.com). Aby przejść do transakcji dodatkowe węzły, zwiększenie numeru portu przez jedną (na przykład pierwszy węzeł transakcji jest na porcie 4000).

Ponieważ maszyny wirtualne, na których uruchamianie węzłów wyszukiwania nie są dostępne z zewnątrz, musisz przejść za pomocą jednego z węzłów transakcji. Po utworzeniu sesji SSH do węzła transakcji, należy zainstalować klucz prywatny w węźle transakcji, lub użyj hasła, aby uruchomić sesję SSH w każdym z węzłów wyszukiwania.

**Uwaga**

Nazwy hostów można uzyskać od administratora lokacji lub w portalu Azure. W portalu Azure, nazwy hostów węzłów obecne w zasobie (VMSS) zestawu skali maszyny wirtualnej jest wyświetlana w obszarze **wystąpień**, który różni się od rzeczywistej nazwy hostów. Na przykład nazwy hosta w portalu Azure może wyglądać jak **mn-asdfmv-reg1_0** , ale rzeczywista nazwa hosta oznaczałoby **mn-asdfmv-reg**.

Na przykład:

Nazwa hosta na portalu Azure| Rzeczywista nazwa hosta
---|---
Mn-ethwvu-reg1_0| Mn-ethwvu-reg1000000
Mn-ethwvu-reg1_1 |Mn-ethwvu-reg1000001
Mn-ethwvu-reg1_2 |Mn-ethwvu-reg1000002

## <a name="adding-a-new-consortium-member"></a>Dodawanie nowego członka konsorcjum

### <a name="sharing-data"></a>Udostępnianie danych

Jako pierwszego elementu członkowskiego (lub element członkowski połączonych) konsorcjum musisz podać o innych elementach członkowskich kilka rodzajów informacji, aby dołączyć i nawiązania połączenia. W szczególności:

1. **Udostępnione dane konfiguracji konsorcjum**: istnieje zestaw danych, które jest używane do organizowania Ethereum połączenie między dwoma elementami. Niezbędne informacje, w tym bloku genesis konsorcjum identyfikator sieci i węzłów rozruchowych są zapisywane do pliku w węzłach transakcji kierujący lub innego członka wdrożone. Lokalizacja tego pliku znajduje się w parametrze danych wyjściowych szablonu wdrożenia o nazwie **danych konsorcjum**.
2. **Elementu równorzędnego punktu końcowego informacji**: elementu równorzędnego punktu końcowego rejestratora informacje można uzyskać informacji o wszystkich węzłów już połączony z siecią Ethereum znaki wiodące lub innego członka wdrożenia. Magazyny DB zestaw informacji dotyczących każdego węzła połączone w sieci, informacje, takie jak nazwa węzła hosta, prywatnego adresu IP itp. Jest to parametr wyjściowy szablonu wdrożenia o nazwie **PEER_INFO_ENDPOINT**.
3. **Elementu równorzędnego informacje o kluczu podstawowym**: klucza podstawowego elementu równorzędnego informacji rejestratora jest używany do uzyskiwania dostępu do wiodące lub innego członka elementu równorzędnego informacje klucza podstawowego. Jest to parametr wyjściowy szablonu wdrożenia o nazwie **PEER_INFO_PRIMARY_KEY**.


4. **Brama sieci Wirtualnej**: każdy element członkowski nawiąże połączenie z siecią cały blockchain przy użyciu istniejącego elementu członkowskiego. Aby połączyć sieć Wirtualną, należy ścieżka zasobu do bramy sieci Wirtualnej, elementu członkowskiego, z którym chcesz się połączyć. Jest to parametr wyjściowy szablonu wdrożenia o nazwie **CONSORTIUM_MEMBER_GATEWAY_ID**.
5. **Wspólny klucz**: A ustalonej wstępnie tajny między dwa elementy członkowskie konsorcjum sieci, które są ustanawiania połączenia. Jest to ciąg alfanumeryczne (od 1 do 128 znaków), który został uzgodniony poza kontekstem wdrożenia. (Na przykład **MySharedKeyAbc123**)

### <a name="acceptance-of-new-member"></a>Akceptację nowego elementu członkowskiego

Ten krok należy wykonać po łącząca element członkowski został pomyślnie wdrożony sieci. Przed elementem członkowskim można dołączyć do sieci, a następnie zobacz ruch transakcji, istniejącego elementu członkowskiego musi wykonać konfiguracji końcowej na ich bramy sieci VPN, aby akceptował połączenia. Oznacza to, że węzły Ethereum łącząca elementu członkowskiego nie zostaną uruchomione, dopóki nie zostanie nawiązane połączenie. Ta konfiguracja może odbywać się za pośrednictwem programu PowerShell lub interfejsu wiersza polecenia xPlat. Skrypt programu PowerShell moduł i xPlat interfejsu wiersza polecenia są także przechowywane w węźle transakcji równolegle z konsorcjum danych. Lokalizacja skryptu jest parametry wyjściowe wdrożenia o nazwie **para-bramy — PS-MODULE** i **para-BRAMA-AZURE-CLISCRIPT**odpowiednio.

**Instalator programu PowerShell/interfejsu wiersza polecenia**

Dodatkowe informacje na temat sposobu wprowadzenie do poleceń cmdlet programu PowerShell Azure i xPlat platformy Azure interfejsu wiersza polecenia można znaleźć w dokumentacji platformy Azure.

Należy otwórz najnowszą wersję poleceń cmdlet systemu Azure zainstalowane lokalnie i sesji. Upewnij się zalogować się do sesji przy użyciu poświadczeń subskrypcji platformy Azure.

**Środowiska PowerShell: Nawiązania połączenia**

Pobieranie modułu programu PowerShell i przechowywane lokalnie. Lokalizacja modułu programu PowerShell jest określona jako **para-bramy — PS-MODULE** parametru wyjściowego wdrażania szablonu.

Jeśli nie zostało jeszcze włączone, użyj **Set-ExecutionPolicy** polecenie cmdlet do sesji lokalnej zezwolić na uruchamianie niepodpisanych modułu.

**Set-ExecutionPolicy Unrestricted CurrentUser**

Następnie zaloguj się do subskrypcji platformy Azure, w którym można wdrożyć za pomocą wdrażania wiodące

**Login-AzureRmAccount**

Następnie zaimportuj moduł:

**Import-Module <filepath to downloaded file>**

Na koniec uruchom funkcję przy użyciu odpowiednich danych wejściowych:

- **MyGatewayResourceId** : ścieżka zasobu bramy. Jest to parametr wyjściowy szablonu wdrożenia o nazwie **CONSORTIUM_MEMBER_GATEWAY_ID**.
- **OtherGatewayResourceId** : ścieżka zasobu bramy łącząca elementu członkowskiego. Odbywa się za pomocą łącząca elementu członkowskiego i parametr szablonu wdrożenia dane wyjściowe z nosi również **CONSORTIUM_MEMBER_GATEWAY_ID**.
- **ConnectionName** : Nazwa w celu identyfikowania tego połączenia bramy.
- **Wspólny klucz** : wcześniej ustalonych klucz tajny między członkami sieci konsorcjum, które są ustanawiania połączenia.

**CreateConnection** -MyGatewayResourceId <resource path of your Gateway> Mojepołączenie - ConnectionName < ścieżka zasobu bramy łącząca elementu członkowskiego > - OtherGatewayResourceId - SharedKey "MySharedKeyAbc123"

**Interfejs wiersza polecenia xPlat: nawiązania połączenia**

Pobieranie skryptu wiersza polecenia platformy Azure i umieść ją lokalnie. Lokalizacja skryptu wiersza polecenia platformy Azure jest określona w parametrze wdrażania szablonu o nazwie **pary bramy-AZURE-CLI-skryptów**.

Uruchom skrypt przy użyciu odpowiednich danych wejściowych:

- **MyGatewayResourceId** : ścieżka zasobu bramy. Jest to parametr wyjściowy szablonu wdrożenia o nazwie **CONSORTIUM_MEMBER_GATEWAY_ID**.
- **OtherGatewayResourceId** : ścieżka zasobu bramy łącząca elementu członkowskiego. To są przekazywane przez członka łącząca i parametr szablonu wdrożenia ich wdrożenia także o nazwie **CONSORTIUM_MEMBER_GATEWAY_ID**.
- **ConnectionName** : Nazwa w celu identyfikowania tego połączenia bramy.
- **Wspólny klucz** : wcześniej ustalonych klucz tajny między członkami sieci konsorcjum, które są ustanawiania połączenia.
- **Lokalizacja** : regionu Azure wdrożonym zasobu bramy.

``` powershell
az network vpn-connection create --name $ConnectionName --resource-group
$MyResourceGroup --vnet-gateway1 $MyGatewayResourceId --shared-key $SharedKey --vnet-
gateway2 $OtherGatewayResourceId --enable-bgp
```

Architektura będą następujące po pomyślnie skonfigurowano połączenie między **wiodące** i **elementu członkowskiego** wdrożeń.

![Architektura wiodące i element członkowski](./media/ethereum-deployment-guide/leader-member.png)

## <a name="fund-new-member-account"></a>Nowe konto Członkowskie Funduszu

### <a name="generated-genesis-block"></a>Wygenerowany genesis bloku

Jako pierwszego elementu członkowskiego, Twoje konto Ethereum jest finansowany z jednego bln ether Jeśli wdrożenie generuje blok genesis (niestandardowe Genesis bloku = nie). O innych elementach członkowskich mają konta, które nie jest wstępnie finansowany a należy zaczekać z accumulate Ether zgodnie z ich wyszukiwania węzły rozpoczną Moje bloki. Aby uniknąć oczekiwania dla Ether nowe elementy członkowskie, musisz jawnie Funduszu łącząca członków Ethereum konta.

Aby to zrobić, dołączenia elementy członkowskie muszą umożliwiają konta Ethereum, na którym jest wyświetlany na ich administratora witryny sieci Web. Następnie można witryny sieci Web admin transfer Ether z Twojego konta do swojego konta, wprowadzając podane konto.

### <a name="custom-genesis-block"></a>Blok genesis niestandardowych

Jeśli blok genesis niestandardowych jest dostarczany z określonym kontem Ethereum, można użyć MetaMask lub innego narzędzia do przenoszenia ether niż określono konto do wstępnie wygenerowane konta Ethereum widoczne w administracyjnej witrynie sieci Web. Aby uzyskać instrukcje dotyczące sposobu używania MetaMask, zobacz [tworzenia konta Ethereum](#create-ethereum-account).

Jeśli nie masz dostępu do wszystkich kont wstępnie przydzielić bloku genesis niestandardowe są dostarczane bez konta, konieczne będzie poczekaj, aż zacząć Moje do generowania Ether na koncie (monety podstawowej) węzły wyszukiwania. Jak szybko fundusze są generowane, zależy od poziomu trudności w bloku genesis niestandardowych.

## <a name="create-ethereum-account"></a>Tworzenie konta Ethereum

Aby utworzyć dodatkowe konta, można użyć różnych rozwiązań. Jednym z rozwiązań jest MetaMask i rozszerzenia programu Chrome, które udostępnia magazynu tożsamości i połączenia z siecią Ethereum publiczny, testów lub niestandardowy. MetaMask formulates transakcji można zarejestrować konta w sieci.

Tej transakcji, podobnie jak inne transakcje zostanie umieszczona na jednym z węzłów transakcji, a ostatecznie wykopany do bloku, jak pokazano poniżej.

![Węzeł transakcji](./media/ethereum-deployment-guide/transaction-node.png)

Aby zainstalować rozszerzenie w przeglądarce Chrome, przejdź do Dostosuj i kontrolować Google Chrome (w przycisku przeciążenia) > więcej narzędzi > Rozszerzenia > Uzyskaj więcej rozszerzeń i wyszukaj MetaMask.

![Rozszerzenie MetaMask](./media/ethereum-deployment-guide/metamask-extension.png)

Po zakończeniu instalacji, otwórz MetaMask i Utwórz nowy magazyn. Domyślnie Magazyn zostaną podłączone do testowej sieci Morden. Należy zmienić, aby połączyć się z siecią wdrożonej konsorcjum prywatnej kierowane do usługi równoważenia obciążenia przed węzłów transakcji. Z danych wyjściowych szablonu, pobrać narażonych Ethereum RPC punktu końcowego w porcie 8545, jako `ETHEREUM-RPC-ENDPOINT`i wprowadź go w niestandardowych RPC, jak pokazano poniżej.

![Ustawienia MetaMask](./media/ethereum-deployment-guide/metamask-settings.png)

Przez utworzenie magazynu, możesz utworzyć portfela, zawierającą konto. Aby utworzyć dodatkowych kont, wybierz **kont przełącznika** , a następnie **+** przycisku.

![MetaMask Tworzenie konta](./media/ethereum-deployment-guide/metamask-create-account.png)

### <a name="initiate-initial-ether-allocation"></a>Zainicjuj początkowej Ether alokacji

Za pomocą strony administratora można sformułować transakcji do przesłania Ether ze wstępnie przydzielić konta na inne konto Ethereum. To przeniesienie Ether jest wysyłany do węzła transakcji i wydobyty do bloku, jak przedstawiono poniżej transakcji.

![Węzeł transakcji](./media/ethereum-deployment-guide/transaction-node-mined.png)

Za pomocą ikony Schowka w portfela MetaMask skopiuj adres konta Ethereum, do którego chcesz przenieść ether i wróć do strony administratora. Wklej skopiowane konto w pole wejściowe, aby przenieść 1000 ether z wstępnie przydzielić konta Ethereum do nowo utworzonego konta. Kliknij przycisk **przesyłania** i poczekaj, aż transakcji można wykopany w bloku.

![Stan węzła](./media/ethereum-deployment-guide/node-status2.png)

Gdy transakcja zostaje zatwierdzona do bloku wydobyty, saldo konta w MetaMask dla Twojego konta zostaną one zastosowane transferu 1000 Ether.

![MetaMask transfer](./media/ethereum-deployment-guide/metamask-transfer.png)

### <a name="transfer-of-ether-between-accounts"></a>Transfer Ether między kontami

W tym momencie możesz przystąpić do wykonania transakcji w sieci prywatnej konsorcjum. Najprostsza transakcji jest transferu Ether z jednego konta do innego. Sformułować tego typu transakcji, umożliwia MetaMask jeszcze raz przesyłania pieniędzy z pierwszego konta używanego powyżej do drugiego konta.

Z **1 portfela** w MetaMask, kliknij przycisk Wyślij. Skopiuj adres drugi portfela, utworzonych do **adres odbiorcy** pola i ilość Ether przeniesienie **kwota** pola wejściowego. Kliknij przycisk **wysyłania** i zaakceptuj transakcji.

![MetaMask wysyłania transakcji](./media/ethereum-deployment-guide/metamask-send.png)

Ponownie gdy transakcja jest wydobywany i zatwierdzona w bloku, środki na koncie zostaną odzwierciedlone w związku z tym. Uwaga: **1 portfela**firmy saldo jest odejmowany więcej niż 15 Ether, ponieważ było opłata wyszukiwania do przetwarzania transakcji.

![Portfela 1](./media/ethereum-deployment-guide/wallet1.png)

![Portfela 2](./media/ethereum-deployment-guide/wallet2.png)

## <a name="next-steps"></a>Kolejne kroki

Teraz można przystąpić do aplikacji i rozwoju inteligentne kontraktu względem sieci prywatnej konsorcjum blockchain.
