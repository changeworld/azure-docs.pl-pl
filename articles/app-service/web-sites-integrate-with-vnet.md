---
title: Integracja aplikacji z sieci wirtualnej platformy Azure
description: Pokazuje, jak połączyć aplikację w usłudze Azure App Service do nowej lub istniejącej sieci wirtualnej platformy Azure
services: app-service
documentationcenter: ''
author: ccompy
manager: erikre
editor: cephalin
ms.assetid: 90bc6ec6-133d-4d87-a867-fcf77da75f5a
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/23/2017
ms.author: ccompy
ms.openlocfilehash: 83f5c64926eb9b718463c415a5478af374245f31
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2018
---
# <a name="integrate-your-app-with-an-azure-virtual-network"></a>Integracja aplikacji z sieci wirtualnej platformy Azure
Ten dokument zawiera opis funkcji integracji sieci wirtualnej Azure App Service i pokazuje, jak je skonfigurować przy użyciu aplikacji w [usłudze Azure App Service](http://go.microsoft.com/fwlink/?LinkId=529714). Jeśli znasz sieci wirtualnych Azure (sieci wirtualne), jest możliwość, która pozwala umieszczać wiele zasobów platformy Azure w kontroli dostępu do sieci routeable z systemem innym niż internet. Te sieci mogą być następnie połączone z sieciami lokalnymi przy użyciu różnych technologii sieci VPN. Aby dowiedzieć się więcej o sieciach wirtualnych platformy Azure, należy uruchomić z informacjami w tym miejscu: [omówienie sieci wirtualnych Azure][VNETOverview]. 

Usługi aplikacji Azure ma dwa formularze. 

1. Systemów wieloma dzierżawcami, które obsługują pełnego zakresu planów cenowych
2. Funkcja premium środowiska usługi aplikacji (ASE), która wdraża do sieci wirtualnej. 

Ten dokument jest przesyłany przez integrację sieci wirtualnej i nie środowiska usługi aplikacji. Jeśli chcesz dowiedzieć się więcej o funkcji ASE rozpoczynać się od informacji w tym miejscu: [wprowadzenie środowiska usługi aplikacji][ASEintro].

Integracja z sieciami wirtualnymi pozwala Ci dostęp do aplikacji sieci web do zasobów w sieci wirtualnej, ale nie powoduje przyznania dostępu prywatnego do aplikacji sieci web z siecią wirtualną. Dostęp do witryny prywatnej odwołuje się do tylko udostępnianie aplikacji z sieci prywatnej takich jak z wewnątrz sieci wirtualnej platformy Azure. Dostęp do witryny prywatny jest dostępny tylko dla ASE skonfigurowane z wewnętrznego obciążenia równoważenia (ILB). Aby uzyskać więcej informacji na temat używania ASE ILB, rozpoczynać się od tego artykułu, w tym miejscu: [tworzenie i używanie ASE ILB][ILBASE]. 

Typowy scenariusz, w której można zastosować integrację sieci wirtualnej jest umożliwienie dostępu z aplikacji sieci web do bazy danych lub usługi sieci web uruchomiony na maszynie wirtualnej w sieci wirtualnej platformy Azure. Dzięki integracji sieci wirtualnej nie trzeba ujawniać publicznego punktu końcowego dla aplikacji w sieci maszyny Wirtualnej, ale może użyć prywatnych adresów routingu internetowego z systemem innym niż. 

Funkcja integracji sieci wirtualnej:

* wymaga Standard, Premium lub izolowany cenową planu 
* działa z klasycznym lub Menedżera zasobów w sieci wirtualnej 
* obsługuje TCP i UDP
* współpracuje z sieci Web, urządzeń przenośnych, aplikacje interfejsu API i aplikacje — funkcja
* umożliwia aplikacji łączenie się tylko 1 sieci wirtualnej w czasie
* Włącza do pięciu sieci wirtualnych do zintegrowane z planu usługi App Service 
* zezwala na tej samej sieci wirtualnej, który będzie używany przez wiele aplikacji w planie usługi aplikacji
* obsługuje 99,9% Umowy dotyczącej poziomu usług z powodu umowy SLA dla bramy sieci wirtualnej

Istnieje kilka kwestii, które integracji sieci wirtualnej nie obsługuje łącznie:

* zainstalowanie dysku
* Funkcja integracji usługi AD 
* NetBios
* dostęp do witryny prywatnej

### <a name="getting-started"></a>Wprowadzenie
Poniżej przedstawiono niektóre czynności, które należy wziąć pod uwagę przed podłączeniem do sieci wirtualnej aplikacji sieci web:

* Integracja z sieciami wirtualnymi działa tylko w przypadku aplikacji w **standardowe**, **Premium**, lub **izolowany** cenową planu. Czy włączyć funkcję, a następnie skalować Twojego planu usługi aplikacji do nieobsługiwanego planu cenowego aplikacji utracą połączenia do sieci wirtualnych, używają. 
* Sieci wirtualnej docelowy już istnieje, musi on mieć włączone z bramą routingu dynamicznego przed podłączeniem do aplikacji VPN punkt lokacja. Jeśli brama jest skonfigurowana z routingiem statycznym, nie można włączyć punkt lokacja wirtualnej sieci prywatnej (VPN).
* Sieć wirtualna musi być w tej samej subskrypcji co Twoje Plan(ASP) usługi aplikacji.
* Jeśli ta brama już istnieje z włączoną punkt do lokacji, a nie jest podstawowy SKU, IKEV2 musi zostać wyłączone w konfiguracji lokacji punktu.
* Aplikacje, które integrują się z użyciem sieci wirtualnej DNS, który jest określony dla tej sieci wirtualnej.
* Domyślnie aplikacje całkującej tylko kierować ruchem w sieci wirtualnej na podstawie tras, które są zdefiniowane w sieci wirtualnej. 

## <a name="enabling-vnet-integration"></a>Włączanie integracji sieci wirtualnej

Użytkownik może także łączenie aplikacji do nowej lub istniejącej sieci wirtualnej. Jeśli tworzysz nową sieć jako część integracją, oprócz tworzenie sieci wirtualnej, jest wstępnie skonfigurowanej bramy routingu dynamicznego i punkt witryny sieci VPN jest włączona. 

> [!NOTE]
> Konfigurowanie nowego integracji sieci wirtualnej może potrwać kilka minut. 
> 
> 

Aby włączyć integrację sieci wirtualnej, Otwórz aplikację ustawienia, a następnie wybierz sieć. Interfejs użytkownika, który zostaje otwarty oferuje trzy opcje sieci. Ten przewodnik jest tylko do integracji sieci wirtualnej jednak połączeń hybrydowych i środowiska usługi App Service zostały omówione w dalszej części tego dokumentu. 

Jeśli aplikacja jest nie na poziomie właściwej cenach planu, interfejs użytkownika umożliwia skalowanie planu do wyższych planu cenowego wybranych przez użytkownika.

![][1]

### <a name="enabling-vnet-integration-with-a-pre-existing-vnet"></a>Włączanie integracji sieci wirtualnej z istniejącej sieci wirtualnej
Interfejsu użytkownika integracji sieci wirtualnej można wybrać z listy Twojej sieci wirtualnych. Klasyczne sieci wirtualne wskazuje, że są takie od słowa "Klasyczny" w nawiasach obok nazwy sieci wirtualnej. Lista jest sortowana w taki sposób, że sieci wirtualnych Menedżera zasobów są wyświetlane jako pierwsze. W obrazie pokazano poniżej widać, że można wybrać tylko jedną sieć wirtualną. Istnieje wiele przyczyn, że sieć wirtualną można wyszarzone w tym:

* sieć wirtualna jest w innej subskrypcji, czy Twoje konto ma dostęp do
* sieć wirtualna nie ma punktu do lokacji włączona
* sieć wirtualna nie ma dynamicznych bramy routingu

![][2]

Aby włączyć integrację kliknij po prostu chcesz zintegrować z sieci wirtualnej. Po wybraniu sieć wirtualna aplikacja jest automatycznie uruchamiany ponownie aby zmiany zaczęły obowiązywać. 

##### <a name="enable-point-to-site-in-a-classic-vnet"></a>Włącz punkt do lokacji w klasycznej sieci wirtualnej
Jeśli nie ma bramy sieci wirtualnej, ani ma punkt do lokacji, następnie należy skonfigurować pierwszy. Aby to zrobić klasycznej sieci wirtualnej, przejdź do [portalu Azure] [ AzurePortal] i wyświetlić wykaz Networks(classic) wirtualnego. W tym miejscu kliknij sieć, którą chcesz zintegrować z, a następnie kliknij duży pole w obszarze Essentials o nazwie połączeń sieci VPN. W tym miejscu można utworzyć punktu witryny sieci VPN i nawet utworzyć bramę. Po przejściu przez punkt do lokacji z obsługą tworzenia bramy jest około 30 minut, zanim będzie gotowy. 

![][8]

##### <a name="enabling-point-to-site-in-a-resource-manager-vnet"></a>Włączanie punkt do lokacji w sieci wirtualnej usługi Resource Manager
Aby skonfigurować sieć wirtualną Resource Manager z bramy oraz punkt do lokacji, można albo PowerShell zgodnie z opisem w tym miejscu [skonfigurowano połączenia punkt-lokacja z sieci wirtualnej przy użyciu programu PowerShell] [ V2VNETP2S] lub użyj portalu Azure, zgodnie z opisem w tym miejscu [skonfigurowano połączenia punkt-lokacja z sieci wirtualnej przy użyciu portalu Azure][V2VNETPortal]. Interfejs użytkownika do wykonania tej funkcji nie jest jeszcze dostępna. Należy pamiętać, że nie trzeba tworzyć certyfikaty dla wskaż konfigurację witryny. To jest automatycznie konfigurowany po podłączeniu Twojej aplikacji sieci Web do sieci wirtualnej. 

### <a name="creating-a-pre-configured-vnet"></a>Tworzenie wstępnie skonfigurowane sieci wirtualnej
Jeśli chcesz tworzyć nowej sieci wirtualnej, który jest skonfigurowany dla bramy i punkt-lokacja usługi App Service sieci interfejsu użytkownika ma możliwość to zrobić, ale tylko dla sieci wirtualnej Menedżera zasobów. Jeśli chcesz utworzyć sieć wirtualną klasycznego bramy i punkt-lokacja, należy to zrobić ręcznie za pomocą interfejsu użytkownika sieci. 

Aby utworzyć sieć wirtualną Resource Manager za pośrednictwem interfejsu użytkownika integracji sieci wirtualnej, po prostu wybierz **Utwórz nową sieć wirtualną** i podaj:

* Nazwa sieci wirtualnej
* Blok adresów sieci wirtualnej
* Nazwa podsieci
* Blok adresów podsieci
* Blok adresów bramy
* Blok adresów połączeń punkt-lokacja

Jeśli chcesz, aby ta sieć wirtualna łączenie się z innymi sieciami, następnie należy unikać pobrania przestrzeń adresów IP, który pokrywa się z tych sieci. 

> [!NOTE]
> Tworzenie sieci wirtualnej Menedżera zasobów przy użyciu bramy trwa około 30 minut i obecnie nie jest zintegrowana z sieci wirtualnej z aplikacją. Po utworzeniu sieci wirtualnej z bramą należy wrócić do swojej aplikacji integracji sieci wirtualnej w interfejsie użytkownika, a następnie wybierz nowej sieci.
> 
> 

![][3]

Sieci wirtualne Azure zwykle są tworzone w sieci prywatnej adresów. Domyślnie integracji sieci wirtualnej funkcji rozsyła cały ruch kierowany do tych zakresów adresów IP w sieci wirtualnej. Zakresów prywatnych adresów IP są:

* 10.0.0.0/8 — jest to ten sam jako 10.0.0.0 - 10.255.255.255
* -to jest taka sama jak 172.16.0.0 - 172.16.0.0/12 172.31.255.255 
* 192.168.0.0/16 - to jest taka sama jak 192.168.0.0 - 192.168.255.255

Musi być określone w notacji CIDR przestrzeni adresowej sieci wirtualnej. Jeśli nie znasz w notacji CIDR, to metoda określania bloki adresów przy użyciu adresu IP i liczba całkowita, która reprezentuje maska sieci. Jako podręczny wykaz należy wziąć pod uwagę że 10.1.0.0/24 byłyby 256 adresów i 10.1.0.0/25 byłoby 128 adresów. Adres IPv4 z /32 będą tylko 1 adres. 

Jeśli ustawisz tutaj informacje o serwerze DNS, który jest ustawić sieci wirtualnej. Po utworzeniu sieci wirtualnej można edytować tych informacji z sieci wirtualnej środowiska użytkownika. Jeśli zmienisz DNS w sieci wirtualnej, należy wykonać operacji sieci synchronizacji.

Podczas tworzenia klasycznych sieci wirtualnej za pomocą interfejsu użytkownika integracji sieci wirtualnej, tworzy sieć wirtualną w tej samej grupie zasobów co aplikacja. 

## <a name="how-the-system-works"></a>Jak działa system
W obszarze obejmuje ta funkcja kompilacje ponad technologii sieci VPN typu punkt-lokacja także łączenie aplikacji do sieci wirtualnej. Aplikacje w usłudze Azure App Service mają architektury wielodostępnej systemu, co wyklucza Inicjowanie obsługi aplikacji bezpośrednio w sieci wirtualnej, co jest wykonywane z maszynami wirtualnymi. Tworzenie na technologii punkt lokacja możemy ograniczające dostęp sieciowy do hostowania aplikacji tylko maszyn wirtualnych. Dostęp do sieci dalsze jest ograniczone na tych hostach aplikacji, umożliwiając aplikacji tylko dostęp do sieci, które można skonfigurować im to dostęp do. 

![][4]

Jeśli nie skonfigurowano serwera DNS z sieci wirtualnej, aplikacji należy używać adresów IP do zasobów w sieci wirtualnej. Podczas korzystania z adresów IP, należy pamiętać, że główną zaletą tej funkcji jest umożliwia używanie prywatnych adresów w sieci prywatnej. Jeśli ustawisz aplikacji do obsługi publicznego adresu IP, adresy dla jednej z maszyn wirtualnych, a następnie nie korzystają z funkcji Integracja z sieciami wirtualnymi i komunikują się za pośrednictwem Internetu.

## <a name="managing-the-vnet-integrations"></a>Zarządzanie integracji sieci wirtualnej
Możliwość nawiązywanie i zakańczanie do sieci wirtualnej jest na poziomie aplikacji. Operacje, które mogą wpłynąć na wielu aplikacjom integrację sieci wirtualnej są na poziomie ASP. W interfejsie użytkownika, który jest wyświetlany na poziomie aplikacji można uzyskać szczegółowe informacje w Twojej sieci wirtualnej. Większość tych samych informacji jest także pokazany na poziomie ASP. 

![][5]

Na stronie Stan funkcji sieci widać, jeśli aplikacja jest podłączony do sieci wirtualnej. Jeśli bramy sieci wirtualnej działa niezależnie od przyczyny, następnie to będzie wyświetlane jako połączony nie. 

Informacje, które masz teraz dostępne w aplikacji, z poziomu interfejsu użytkownika integracji sieci wirtualnej jest taka sama jak szczegółowe informacje, które można uzyskać z ASP. Poniżej przedstawiono te elementy:

* Nazwa sieci wirtualnej — to łącze powoduje otwarcie sieci wirtualnej platformy Azure interfejsu użytkownika
* Lokalizacja — odzwierciedla lokalizacji sieci wirtualnej. Istnieje możliwość integracji z sieciami wirtualnymi w innej lokalizacji.
* Stan certyfikatu — istnieją certyfikaty używane do zabezpieczenia połączenia sieci VPN między sieciami wirtualnymi i aplikacji. Ta właściwość odzwierciedla test, aby upewnić się, że są one zsynchronizowane.
* Stan bramy — z bram należy dół jakiegoś powodu następnie aplikacji nie dostępu do zasobów w sieci wirtualnej. 
* Sieci wirtualnej przestrzeni adresowej — jest to przestrzeń adresów IP dla sieci wirtualnej. 
* Wskaż przestrzeni adresowej Site - to jest punkt do lokacji przestrzeń adresową sieci wirtualnej. Aplikacja zawiera komunikatu jako pochodzących z jednego z adresów IP w tej przestrzeni adresowej. 
* Lokacji do lokacji przestrzeni adresowej — można sieci VPN do lokacji sieci wirtualnej połączyć się z zasobami lokalnymi lub innymi sieci wirtualnej. Powinien mieć, który skonfigurowany, a następnie zakresów IP zdefiniowane z tutaj pokazano połączenia sieci VPN.
* Serwery DNS — Jeśli masz serwerom DNS skonfigurowanym z sieci wirtualnej, a następnie są one wyświetlane tutaj.
* Adresy IP kierowane do sieci wirtualnej - tam przedstawiono listę adresów IP sieci wirtualnej ma routingu zdefiniowane dla i tych adresów Pokaż tutaj. 

Jedyną operacją, które można wykonać w widoku aplikacji integracji sieci wirtualnej jest rozłączyć aplikacji jest aktualnie połączony z siecią wirtualną. Aby zrobić to po prostu kliknij przycisk Odłącz u góry. Ta akcja nie powoduje zmiany sieci wirtualnej. Sieć wirtualna i jej konfiguracji w tym bramy pozostaje niezmieniona. Jeśli następnie chcesz usunąć sieci wirtualnej, należy najpierw usunąć zawartych w niej w tym bramy zasobów. 

W widoku Plan usługi App Service ma liczbę dodatkowych operacji. Jest on również dostępny inaczej niż w aplikacji. Do osiągnięcia interfejsu użytkownika sieci ASP po prostu otworzyć ASP interfejsu użytkownika i przewiń w dół. Brak elementu interfejsu użytkownika o nazwie stan funkcji sieci. Udostępnia pewne szczegóły pomocnicza wokół integracją sieci wirtualnej. Kliknięcie tego interfejsu użytkownika otwiera interfejs stanu funkcji sieci. Jeśli następnie polecenie "Kliknij tutaj, aby zarządzać" otwiera interfejsu użytkownika, który zawiera listę integracji sieci wirtualnej, w tym ASP.

![][6]

Lokalizacja ASP jest warto pamiętać podczas wyszukiwania w lokalizacjach sieci wirtualnych, należy przeprowadzić integrację. Gdy sieć wirtualna jest w innej lokalizacji jest znacznie bardziej prawdopodobne wyświetlić opóźnień. 

Sieci wirtualne, zintegrowany z przypomina na liczbę sieci wirtualnych się, że aplikacje są zintegrowane z usługą, w tym ASP i ile można mieć. 

Aby wyświetlić szczegóły dodanych w każdej sieci wirtualnej, po prostu kliknij w sieci wirtualnej. Oprócz szczegółów, które zostały wymienione wcześniej można także wyświetlić listę aplikacji w tym ASP, które korzystają z tej sieci wirtualnej. 

W odniesieniu do działań istnieją dwie podstawowe akcje. Pierwsza to możliwość dodawania tras, na których dysków ruchu wychodzącego aplikacji w sieci wirtualnej. Drugiej akcji jest możliwość synchronizacji certyfikaty i informacje o sieci.

![][7]

**Routing** jak wspomniano wcześniej, są trasy zdefiniowane w sieci wirtualnej jest używany do kierowania ruchu w sieci wirtualnej z aplikacji. Brak niektórych zastosowaniach chociaż gdzie klienci chcą wysyłanie dodatkowych ruchu wychodzącego z aplikacji do sieci wirtualnej i ich ta możliwość jest dostępne. Co się dzieje na ruch będzie maksymalnie jak klient skonfigurował ich sieci wirtualnej. 

**Certyfikaty** kontroli przez usługę App Service można sprawdzić poprawności wykonywana, że certyfikaty używane dla połączenia sieci VPN są nadal dobrej odzwierciedla stan certyfikatu. Po włączeniu integracji sieci wirtualnej, jeśli to jest pierwszy integracji do tej sieci wirtualnej z dowolnej aplikacji, w tym ASP występuje wymagane wymiany certyfikatów do zabezpieczenia połączenia. Wraz z certyfikatów uzyskujemy konfiguracji serwera DNS, tras i innymi podobne, które opisują sieci.
Zmiana tych certyfikatów lub informacje o sieci następnie zajść potrzeba kliknięcia przycisku "Synchronizacji sieci". **Uwaga**: po kliknięciu "Synchronizacji sieci", a następnie spowodować chwilowa niedostępność w łączności między aplikacji i sieci wirtualnej. Gdy aplikacja nie zostanie ponownie uruchomiony, utraty połączenia może spowodować nieprawidłowe działanie witryny poprawnie. 

## <a name="accessing-on-premises-resources"></a>Uzyskiwanie dostępu do zasobów lokalnych
Jedną z zalet funkcji integracji sieci wirtualnej jest to, że jeśli sieci wirtualnej jest podłączona do sieci lokalnej za pomocą sieci VPN między lokacjami, a następnie aplikacje mogą uzyskiwać dostęp do zasobów lokalnych z aplikacji. Aby to zrobić, chociaż może być konieczne aktualizacji bramy sieci VPN, lokalnej z tras dla punktu do lokacji IP w zakresie. Po pierwszym skonfigurowaniu sieci VPN między lokacjami następnie skrypty używane do konfigurowania go należy skonfigurować tras w tym punkt do lokacji sieci VPN. Jeśli dodasz punkt do lokacji sieci VPN po utworzeniu Twojej sieci VPN między lokacjami, należy ręcznie zaktualizować trasy. Szczegółowe informacje o tym, jak to zrobić zmienia się wraz z bramy i nie zostały opisane w tym miejscu. 

> [!NOTE]
> Funkcja integracji sieci wirtualnej nie obsługują aplikacji sieć wirtualną, która ma bramę usługi ExpressRoute. Nawet jeśli skonfigurowano bramę usługi ExpressRoute w [tryb współistnienie] [ VPNERCoex] integracji sieci wirtualnej nie działa. Jeśli musisz uzyskiwać dostęp do zasobów za pośrednictwem połączenia ExpressRoute, a następnie można użyć [środowiska usługi aplikacji][ASE], które uruchamia się w sieci wirtualnej.
> 
> 

## <a name="pricing-details"></a>Szczegóły cennika
Istnieje kilka cennik wszystkie szczegóły, które należy zwrócić uwagę podczas korzystania z funkcji integracji sieci wirtualnej. Istnieją 3 opłat powiązanych do używania tej funkcji:

* Wymagania dotyczące warstwy cenowej ASP
* Koszty transferu danych
* Koszty bramy sieci VPN.

Do pracy z aplikacjami można było korzystać z tej funkcji muszą znajdować się w Standard lub Plan usługi aplikacji — warstwa Premium. Można wyświetlić więcej szczegółów tych kosztów tutaj: [App Service — ceny][ASPricing]. 

Ze względu na sposób punktu witryny sieci VPN są obsługiwane, zawsze masz opłat dla danych wychodzących za pośrednictwem połączenia integracji sieci wirtualnej, nawet jeśli sieć wirtualna jest w tym samym centrum danych. Aby zobaczyć, jakie są tych opłat, zapoznaj się z informacjami w tym miejscu: [szczegóły cennika transferu danych][DataPricing]. 

Ostatni element jest koszt bramy sieci wirtualnej. Jeśli nie potrzebujesz bramy do czegoś innego, takich jak witryny sieci VPN są płatność za bramy do obsługi funkcji integracji sieci wirtualnej. Brak szczegółów kosztów tych tutaj: [cennik bramy sieci VPN][VNETPricing]. 

## <a name="troubleshooting"></a>Rozwiązywanie problemów
Gdy funkcja jest łatwa do skonfigurowania, nie oznacza to, że będzie problem wolnego. Powinien wystąpić problemy z dostępem do żądanego punktu końcowego są niektóre narzędzia, które służy do testowania łączności z aplikacji konsoli. Istnieją dwa środowiska konsoli, których można używać. Jest jednym z poziomu konsoli Kudu, a drugi to konsola, której możesz uzyskiwać dostęp w portalu Azure. Aby uzyskać dostęp do konsoli Kudu z aplikacji przejdź do pozycji Narzędzia -> Kudu. Jest to taka sama jak zamierza [sitename]. scm.azurewebsites.net. Po który otwiera po prostu przejdź do karty konsoli debugowania. Aby uzyskać konsoli platformy Azure hostowanej portalu, a następnie z aplikacji przejdź do pozycji Narzędzia -> konsoli. 

#### <a name="tools"></a>Narzędzia
Polecenie ping narzędzi, nslookup i tracert nie będzie działać za pośrednictwem konsoli z powodu ograniczeń zabezpieczeń. Aby wypełnić void tam zostały dwóch oddzielnych narzędzi dodane. Aby przetestować funkcje DNS dodaliśmy narzędzia o nazwie nameresolver.exe. Składnia jest następująca:

    nameresolver.exe hostname [optional: DNS Server]

Nameresolver służy do sprawdzania nazwy hostów, która zależy od aplikacji. W ten sposób można przetestować niczego źle skonfigurowany systemie DNS lub możliwe, że nie ma dostępu do serwera DNS.

Narzędzie dalej służy do testowania połączeń TCP kombinację hosta i portu. To narzędzie jest nazywany tcpping.exe i składnia jest następująca:

    tcpping.exe hostname [optional: port]

Narzędzie tcpping informuje, jeżeli można osiągnąć konkretnego hosta i portu. Tylko może sygnalizować Powodzenie jeśli: Brak aplikacji nasłuchującej na kombinacji hosta i portu i Brak dostępu do sieci z aplikacji do określonego hosta i portu.

#### <a name="debugging-access-to-vnet-hosted-resources"></a>Debugowanie dostępu do sieci wirtualnej hostowanych zasobów
Istnieje kilka rzeczy, które mogą uniemożliwić aplikacji osiągnięcia konkretnego hosta i portu. W większości przypadków jest jednym z trzech zdarzeń:

* **W ten sposób znajduje się zapora** Jeśli masz zaporę w taki sposób, nastąpi trafienie limitu czasu TCP. W takim przypadku to 21 sekund. Aby przetestować połączenie, należy użyć narzędzia tcpping. Limity czasu protokołu TCP można ze względu na wiele rzeczy poza zaporą, ale uruchomić. 
* **Usługa DNS nie jest dostępny** limit czasu DNS jest trzech sekund na serwerze DNS. Jeśli masz dwa serwery DNS, limit czasu to 6 sekund. Użyj nameresolver, aby zobaczyć, czy działa DNS. Należy pamiętać, że nie można używać polecenia nslookup, ponieważ nie używa DNS skonfigurowano sieci wirtualnej.
* **Nieprawidłowy zakres adresów IP P2S** wskaż zakres adresów IP lokacji musi być w zakresach RFC 1918 prywatnego adresu IP (10.0.0.0-10.255.255.255 / 172.16.0.0-172.31.255.255 / 192.168.0.0-192.168.255.255). Jeśli poza tym, że zakres korzysta z adresów IP, czynności nie będzie działać. 

Jeśli te elementy nie odpowiada problemu, Szukaj pierwszego podczas wykonywania prostych operacji, takich jak: 

* Bramy są wyświetlane jako znajdujące się w portalu?
* Certyfikaty są wyświetlane jako znajdujące się w synchronizacji?
* Każdy zmieniły się konfiguracja sieci nie robiąc "Synchronizacji sieci" w odpowiednim ASP? 

Jeśli brama nie działa, następnie włączenie go kopii zapasowych. Jeśli certyfikaty nie są zsynchronizowane, przejdź do widoku ASP integracji z sieciami wirtualnymi i kliknij przycisk "Synchronizacji sieci". Jeśli podejrzewasz, że wprowadzono zmiany wprowadzone w konfiguracji sieci wirtualnej, a nie był synchronizacji będzie z programu ASP, następnie przejdź do widoku ASP integracji z sieciami wirtualnymi i kliknij "Synchronizacji sieci" Just przypomnieniem, powoduje to chwilowa niedostępność z połączeniem sieci wirtualnej i aplikacji. 

Jeśli wszystko jest poprawnie, wymagana będzie szczegółowej nieco analizy:

* Czy istnieją inne aplikacje przy użyciu integracji sieci wirtualnej do zasobów w tej samej sieci wirtualnej? 
* Można przejść do aplikacji konsoli i używać tcpping do żadnych innych zasobów w sieci wirtualnej? 

Jeśli albo powyższe warunki są spełnione, następnie Integracja sieci wirtualnej jest poprawnie, a problem nie zostanie w innym miejscu. Jest to, gdzie pobiera będzie więcej wyzwanie, ponieważ nie istnieje prosty sposób aby zobaczyć, dlaczego nie można osiągnąć hosta: port. Przyczyny są między innymi:

* masz zaporę na hoście uniemożliwienia dostępu do portu aplikacji z punktu do lokacji zakres adresów IP. Często przekroczenie granic podsieci wymaga dostępu publicznego.
* dostęp do hosta docelowego nie działa
* Aplikacja nie działa
* miał nieprawidłowy adres IP lub nazwa hosta
* aplikacja nasłuchuje na innym porcie niż oczekiwano. Można to sprawdzić, przechodząc na tym hoście i za pomocą "netstat - aon" w wierszu polecenia. Przedstawia identyfikator nasłuchuje na porcie co procesu. 
* grupy zabezpieczeń sieci są skonfigurowane w taki sposób, że zapobiegają dostępu do aplikacji hosta i portu punkt do lokacji zakres adresów IP

Należy pamiętać, że nie wiesz, jakie IP w punktu zakres IP witryny, które Twoja aplikacja będzie używać, więc należy zezwolić na dostęp z całego zakresu. 

Debugowania dodatkowe kroki obejmują:

* Zaloguj się na inną maszynę Wirtualną w sieci wirtualnej i próbują uzyskać dostęp z zasobów hosta: port. stamtąd. Istnieją pewne narzędzia polecenia ping protokołu TCP w tym celu można użyć, lub nawet służy telnet, jeśli muszą być. W tym miejscu celem jest tylko w celu ustalenia, czy połączenie jest z tej maszyny Wirtualnej. 
* Wywołaj okno aplikacji na dostęp do innej maszyny Wirtualnej i testowania do tego hosta i portu z poziomu konsoli z aplikacji

#### <a name="on-premises-resources"></a>Zasobów lokalnych ####
Jeśli aplikację można nawiązać połączenia z zasobami lokalnymi, najpierw należy sprawdzić, jest, jeżeli można osiągnąć zasobu w sieci wirtualnej. Jeśli to zrobić, spróbuj nawiązać aplikacji lokalnej z maszyny Wirtualnej w sieci wirtualnej. Można użyć narzędzia telnet lub narzędzie ping protokołu TCP. Jeśli maszyny Wirtualnej nie może uzyskać dostęp do zasobu lokalnego, upewnij się, że połączenie sieci VPN do lokacji działa. Jeżeli działa, Sprawdź te same elementy, jak wspomniano wcześniej, a także konfiguracji bramy lokalnej i stanu. 

Teraz Jeśli sieci wirtualnej jest hostowany maszyny Wirtualnej może nawiązać połączenie z lokalnego systemu, ale aplikacji nie może następnie przyczyną jest prawdopodobnie jednym z następujących czynności:

* nie skonfigurowano trasy z punktu do lokacji zakresów IP w bramy sieci lokalnej
* grupy zabezpieczeń sieci są blokowanie dostępu do punktu do zakresu adresów IP lokacji
* lokalnej zapory blokuje ruch z punktu do zakresu adresów IP lokacji
* masz użytkownika Route(UDR) zdefiniowana w sieci wirtualnej, która uniemożliwia punkt do lokacji, na podstawie ruchu nawiązywania połączenia z siecią lokalną

## <a name="powershell-automation"></a>Automatyzacja programu PowerShell

Usługi aplikacji można zintegrować z sieci wirtualnej platformy Azure przy użyciu programu PowerShell. Gotowy do uruchomienia skryptu, zobacz [łączenie aplikacji w usłudze Azure App Service z sieci wirtualnej platformy Azure](https://gallery.technet.microsoft.com/scriptcenter/Connect-an-app-in-Azure-ab7527e3).

## <a name="hybrid-connections-and-app-service-environments"></a>Połączenia hybrydowe i środowiska usługi aplikacji
Istnieją trzy funkcje, które umożliwiają dostęp do zasobów hostowanych w sieci wirtualnej. Oto one:

* Integracja sieci wirtualnej
* Połączenia hybrydowe
* Środowiska usługi App Service

Połączenia hybrydowe wymaga zainstalowania agenta przekazywania o nazwie Manager(HCM) połączeń hybrydowych w sieci. HCM musi mieć możliwość nawiązania połączenia platformy Azure, a także do aplikacji. To rozwiązanie jest szczególnie dużą z sieci zdalnej, takich jak sieci lokalnej lub nawet innej chmury hostowanej sieci, ponieważ nie wymaga dostępnym punkcie końcowym internet. HCM działa tylko w systemie Windows i może mieć maksymalnie pięć wystąpień uruchomiona w celu zapewnienia wysokiej dostępności. Jednak połączeń hybrydowych TCP obsługuje tylko, a każdy punkt końcowy HC musi być zgodna z określonego hosta: port. kombinacji. 

Funkcja środowiska usługi aplikacji umożliwia uruchamianie wystąpienia usługi Azure App Service w sieci wirtualnej. Umożliwia to aplikacji dostęp do zasobów w sieci wirtualnej bez żadnych dodatkowych czynności. Są niektóre zalety środowiska usługi aplikacji, której można pracowników Dv2 oparte o 14 GB pamięci RAM. Kolejna korzyść związana jest skalowanie systemu zgodnie z potrzebami. W odróżnieniu od środowiska wielodostępne, w którym strona ASP jest ograniczona do 20 wystąpień w elemencie ASE można skalować do 100 wystąpień ASP. Jednym z czynników, które można pobrać z ASE, który nie dzięki integracji sieci wirtualnej jest czy środowiska usługi aplikacji może współpracować z sieci VPN usługi ExpressRoute. 

Gdy jest korzystanie z niektórych przypadków nakładają się na siebie, żaden z tych funkcji można zastąpić pozostałe. Uzyskiwanie informacji o tym, jakie funkcji jest powiązany z Twoim potrzebom. Na przykład:

* Jeśli jesteś deweloperem i po prostu chcesz uruchomić witryny na platformie Azure, a jego dostęp do bazy danych na stacji roboczej w obszarze biurku, użyj najłatwiejszym jest połączeń hybrydowych było możliwe. 
* W przypadku dużej organizacji, która chce wstrzymać dużej liczby właściwości sieci web w publicznej w chmurze i zarządzanie nimi we własnej sieci, a następnie chcesz przejść z środowiska usługi aplikacji. 
* Jeśli masz wiele aplikacji usługi hostowanej aplikacji i po prostu chcesz uzyskać dostęp do zasobów w sieci wirtualnej, następnie integracji sieci wirtualnej jest sposób, aby przejść. 

Poza przypadkami użycia są uproszczenia niektórych aspektach. Jeśli sieci wirtualnej jest już podłączony do sieci lokalnej, następnie za pomocą integracji sieci wirtualnej lub w środowisku usługi aplikacji jest prosty sposób korzystać z zasobów lokalnych. Z drugiej strony Jeśli w Twojej sieci wirtualnej nie jest podłączony do sieci lokalnej następnie jest znacznie większe obciążenie można skonfigurować sieci VPN między lokacjami z sieci wirtualnej w porównaniu z instalowaniem HCM. 

Poza różnice funkcjonalne, dostępne są również cennik różnice. Funkcja środowiska usługi aplikacji jest w wersji Premium oferty usługi ale oferuje najbardziej sieci możliwości konfiguracji oprócz inne funkcje. Integracja sieci wirtualnej może być używany z Standard lub Premium ASP i idealne w przypadku bezpieczne korzystanie z zasobów w sieci wirtualnej z wieloma dzierżawcami usługi aplikacji. Połączeń hybrydowych obecnie zależy od usług BizTalk — wersja konta, które ma cennik poziomy uruchamianych wolnego i następnie pobrać progresywnie droższe na podstawie ilości potrzebne. Chodzi o działa jednak w wielu sieciach, nie ma żadnych innych funkcji połączeń hybrydowych, które umożliwiają dostęp do zasobów w różnych sieciach dobrze ponad 100. 

<!--Image references-->
[1]: ./media/web-sites-integrate-with-vnet/vnetint-upgradeplan.png
[2]: ./media/web-sites-integrate-with-vnet/vnetint-existingvnet.png
[3]: ./media/web-sites-integrate-with-vnet/vnetint-createvnet.png
[4]: ./media/web-sites-integrate-with-vnet/vnetint-howitworks.png
[5]: ./media/web-sites-integrate-with-vnet/vnetint-appmanage.png
[6]: ./media/web-sites-integrate-with-vnet/vnetint-aspmanage.png
[7]: ./media/web-sites-integrate-with-vnet/vnetint-aspmanagedetail.png
[8]: ./media/web-sites-integrate-with-vnet/vnetint-vnetp2s.png

<!--Links-->
[VNETOverview]: http://azure.microsoft.com/documentation/articles/virtual-networks-overview/ 
[AzurePortal]: http://portal.azure.com/
[ASPricing]: http://azure.microsoft.com/pricing/details/app-service/
[VNETPricing]: http://azure.microsoft.com/pricing/details/vpn-gateway/
[DataPricing]: http://azure.microsoft.com/pricing/details/data-transfers/
[V2VNETP2S]: http://azure.microsoft.com/documentation/articles/vpn-gateway-howto-point-to-site-rm-ps/
[ASEintro]: environment/intro.md
[ILBASE]: environment/create-ilb-ase.md
[V2VNETPortal]: ../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md
[VPNERCoex]: ../expressroute/expressroute-howto-coexist-resource-manager.md
[ASE]: environment/intro.md
