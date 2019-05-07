---
title: Integrowanie aplikacji z siecią wirtualną platformy Azure — usłudze Azure App Service
description: Pokazuje, jak połączyć aplikację w usłudze Azure App Service do nowej lub istniejącej sieci wirtualnej platformy Azure
services: app-service
documentationcenter: ''
author: ccompy
manager: stefsch
ms.assetid: 90bc6ec6-133d-4d87-a867-fcf77da75f5a
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/12/2018
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: b97fe5d638b5967f533e489a67d4235bbc5a3db1
ms.sourcegitcommit: 0568c7aefd67185fd8e1400aed84c5af4f1597f9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/06/2019
ms.locfileid: "65198574"
---
# <a name="integrate-your-app-with-an-azure-virtual-network"></a>Integrowanie aplikacji z siecią wirtualną platformy Azure
Ten dokument zawiera opis funkcji integracji sieci wirtualnej usługi Azure App Service i pokazuje, jak je skonfigurować za pomocą aplikacji w [usługi Azure App Service](https://go.microsoft.com/fwlink/?LinkId=529714). [Sieci wirtualne platformy Azure] [ VNETOverview] (Vnet) pozwala na umieszczenie wielu zasobów platformy Azure w sieci, lecz-internet. Te sieci mogą być następnie połączone do sieci w środowisku lokalnym przy użyciu technologii sieci VPN. 

Azure App Service ma dwie formy. 

1. Systemy wielodostępne, obsługujące pełną gamę cennikami z wyjątkiem izolowany
2. App Service Environment (ASE), co spowoduje jego wdrożenie w sieci wirtualnej. 

W tym dokumencie omówiono funkcji integracji sieci wirtualnej, która jest przeznaczona do użycia w wielodostępnej usłudze App Service.  Jeśli aplikacja znajduje się w [środowiska App Service Environment][ASEintro], znajduje się już w sieci wirtualnej, a nie wymaga użycia funkcji integracji sieci wirtualnej w celu uzyskania dostępu do zasobów w tej samej sieci wirtualnej.

Integracja sieci wirtualnej zapewnia Twojej aplikacji internetowej dostępu do zasobów w sieci wirtualnej, ale nie są przyznawane prywatny dostęp do aplikacji sieci web z sieci wirtualnej. Dostęp do prywatnej witryny dotyczy tylko udostępnianie aplikacji z sieci prywatnej takich jak z w obrębie sieci wirtualnej platformy Azure. Dostęp do prywatnej witryny jest dostępna tylko dla środowiska ASE skonfigurowane za pomocą wewnętrznego obciążenia równoważenia (ILB). Aby uzyskać szczegółowe informacje na temat korzystania z ASE z wewnętrznym modułem równoważenia obciążenia rozpoczynać artykuł tutaj: [Tworzenie i używanie środowisko ASE z wewnętrznym modułem równoważenia obciążenia][ILBASE]. 

Integracja z siecią wirtualną jest często używane do włączenia dostęp do bazy danych z aplikacji i usług działających w sieci web. Integracja z siecią wirtualną nie wymagają do udostępnienia publicznego punktu końcowego dla aplikacji na maszynie Wirtualnej, ale można zamiast tego użyj prywatnych adresów Routing-internet. 

Funkcja integracji sieci wirtualnej:

* wymaga Standard, Premium lub PremiumV2 planu cenowego 
* sprawdza się w modelu klasycznym lub sieci wirtualnej usługi Resource Manager 
* obsługuje protokoły TCP i UDP
* współpracuje z aplikacji mobilnych, interfejsów API sieci Web i aplikacje funkcji
* umożliwia aplikacji połączyć się z tylko 1 sieć wirtualną w czasie
* Umożliwia maksymalnie pięć sieci wirtualnych można zintegrować z usługą w planie usługi App Service 
* zezwala na tej samej sieci wirtualnej, który będzie używany przez wiele aplikacji w planie usługi App Service
* wymaga bramy sieci wirtualnej, podczas konfigurowania punktu do lokacji sieci VPN
* obsługuje wartość SLA 99,9%, ze względu na warunki umowy SLA w bramie

Istnieje kilka kwestii, które nie są obsługiwane Integracja sieci wirtualnej w tym:

* zainstalowanie dysku
* Integracja z usługą AD 
* NetBios
* dostęp do prywatnej witryny
* Uzyskiwanie dostępu do zasobów dla usługi ExpressRoute 
* Uzyskiwanie dostępu do zasobów w obrębie punktów końcowych usługi 

### <a name="getting-started"></a>Wprowadzenie
Oto kilka rzeczy, o których należy pamiętać przed nawiązaniem połączenia z aplikacją sieci web do sieci wirtualnej:

* Docelowa sieć wirtualna musi mieć point-to-site VPN włączone z bramą opartą na trasach, zanim można podłączyć do aplikacji. 
* Sieć wirtualna musi być w tej samej subskrypcji, co Plan(ASP) usługi Twojej aplikacji.
* Aplikacje, które integrują się z siecią wirtualną używają DNS, który jest określony dla tej sieci wirtualnej.

## <a name="enabling-vnet-integration"></a>Włączanie integracji sieci wirtualnej

Istnieje nowa wersja funkcji integracji sieci wirtualnej, która jest dostępna w wersji zapoznawczej. Go nie są zależne od sieci VPN typu punkt lokacja i ExpressRoute lub punktów końcowych usługi obsługuje również dostęp do zasobów. Aby dowiedzieć się więcej na temat nowych funkcji w wersji zapoznawczej, przejdź na końcu tego dokumentu. 

### <a name="set-up-a-gateway-in-your-vnet"></a>Konfigurowanie bramy w sieci wirtualnej ###

Jeśli masz już skonfigurowane przy użyciu adresów punkt lokacja bramy, możesz przejść do konfigurowania integracji sieci wirtualnej wraz z aplikacją.  
Aby utworzyć bramę:

1. [Tworzenie podsieci bramy] [ creategatewaysubnet] w sieci wirtualnej.  

1. [Tworzenie bramy sieci VPN][creategateway]. Wybierz typ sieci VPN opartej na trasach.

1. [Ustaw punkt do lokacji adresów][setp2saddresses]. Jeśli brama nie znajduje się w ramach podstawowej jednostki SKU, następnie IKEV2 musi zostać wyłączone w konfiguracji punkt lokacja i protokół SSTP musi być zaznaczone. Przestrzeń adresowa musi należeć do jednej z następujących bloki adresów:

* 10.0.0.0/8 — oznacza to, że zakres adresów IP od 10.0.0.0 do 10.255.255.255
* 172.16.0.0/12 — oznacza to, że zakres adresów IP z 172.16.0.0 172.31.255.255 
* 192.168.0.0/16 - oznacza to, że zakres adresów IP z 192.168.0.0 192.168.255.255

Jeśli po prostu tworzenia bramy dla za pomocą integracji sieci wirtualnej usługi aplikacji, nie należy przekazać certyfikat. Tworzenie bramy może potrwać 30 minut. Nie można Integrowanie aplikacji z sieci wirtualnej, dopóki brama jest aprowizowana. 

### <a name="configure-vnet-integration-with-your-app"></a>Konfigurowanie integracji z siecią wirtualną za pomocą aplikacji ###

Aby włączyć integrację sieci wirtualnej w aplikacji: 

1. Przejdź do aplikacji w witrynie Azure portal, Otwórz okno Ustawienia aplikacji i wybierz pozycję sieci > Integracja z siecią wirtualną. Skalowanie strona ASP do standardowej jednostki SKU lub lepiej, aby można było skonfigurować Integracja z siecią wirtualną. 
 ![Integracja z siecią wirtualną interfejsu użytkownika][1]

1. Wybierz **dodać sieci wirtualnej**. 
 ![Dodaj Integracja sieci wirtualnej][2]

1. Wybierz sieć wirtualną. 
  ![Wybierz sieć wirtualną][8]
  
Aplikacja zostanie uruchomiona ponownie po tym ostatnim kroku.  

## <a name="how-the-system-works"></a>Jak działa system
Funkcja integracji sieci wirtualnej jest oparty na technologii sieci VPN typu punkt lokacja. Aplikacje w usłudze Azure App Service znajdują się w systemie wielodostępnym, co wyklucza inicjowania obsługi administracyjnej aplikacji bezpośrednio w sieci wirtualnej. Technologia point-to-site dostęp sieciowy do hostowania aplikacji tylko maszyny wirtualne. Aplikacje są ograniczone do tylko wysyłać ruch do Internetu za pomocą połączeń hybrydowych lub Integracja z siecią wirtualną. 

![Jak działa Integracja z siecią wirtualną][3]

## <a name="managing-the-vnet-integrations"></a>Zarządzanie integracji sieci wirtualnej
Możliwość nawiązywanie i zakańczanie z siecią wirtualną jest na poziomie aplikacji. Operacje, które mogą wpłynąć na Integracja z siecią wirtualną przez wiele aplikacji znajdują się na poziomie plan usługi App Service. Z poziomu aplikacji UID możesz uzyskać szczegółowe informacje w Twojej sieci wirtualnej. Te same informacje jest również wyświetlany na poziomie ASP. 

 ![Szczegóły sieci wirtualnej][4]

Na stronie Stan funkcji sieci widać, jeśli aplikacja jest połączona z siecią wirtualną. Jeśli brama sieci wirtualnej działa niezależnie od przyczyny, Twój status pokazuje, jak połączone nie. 

Informacje, które masz teraz dostępne w aplikacji, z poziomu interfejsu użytkownika z integracji sieci wirtualnej jest taka sama jak szczegółowe informacje, który jest pobierany z ASP. Poniżej przedstawiono te elementy:

* Nazwa sieci wirtualnej — łącza do sieci wirtualnej interfejsu użytkownika
* Lokalizacja — odzwierciedla lokalizację sieci wirtualnej. Integracja z siecią wirtualną w innej lokalizacji mogą powodować problemy opóźnienia dla aplikacji. 
* Stan certyfikatu — odzwierciedla, czy certyfikaty są zsynchronizowane między planu usługi App Service i sieci wirtualnej.
* Stan bramy — bram należy dół jakiejś przyczyny następnie aplikacji nie dostęp do zasobów w sieci wirtualnej. 
* Przestrzeń adresowa sieci wirtualnej — pokazuje przestrzeń adresów IP dla sieci wirtualnej. 
* Przestrzeń adresowa punkt lokacja — pokazuje wskaż przestrzeń adresów IP dla lokacji sieci wirtualnej. Podczas wykonywania wywołań do sieci wirtualnej, adres nadawcy Twojej aplikacji będą jednego z tych adresów. 
* Przestrzeń adresowa lokacja lokacja — można użyć sieci VPN typu lokacja lokacja do łączenia z sieci wirtualnej do zasobów w środowisku lokalnym lub innymi sieciami wirtualnymi. Zakresy adresów IP zdefiniowane za pomocą tego połączenia sieci VPN są wyświetlane w tym miejscu.
* Serwery DNS — pokazuje, serwery DNS są skonfigurowane przy użyciu sieci wirtualnej.
* Adresy IP kierowane do sieci wirtualnej — pokazuje kierowane bloki adresów używany do dysku ruchu w sieci wirtualnej 

Jedyną operacją, które można wykonać w widoku aplikacji Integracja z siecią wirtualną jest rozłączenie aplikacji z jest aktualnie połączony z siecią wirtualną. Aby odłączyć aplikację z sieci wirtualnej, wybierz **rozłączenia**. Aplikacja zostanie uruchomiona ponownie po rozłączeniu z sieci wirtualnej. Odłączanie nie powoduje zmiany sieci wirtualnej. Sieć wirtualna i jego konfiguracji, w tym bramy pozostaje bez zmian. Jeśli chcesz, następnie można usunąć sieci wirtualnej, należy najpierw usunąć zasoby w nim bramy. 

Aby przejść do ASP integracji sieci wirtualnej z interfejsu użytkownika, Otwórz interfejs użytkownika ASP, a następnie wybierz **sieć**.  W obszarze Integracja z siecią wirtualną, wybierz **kliknij tutaj, aby skonfigurować** otworzyć Interfejs użytkownika stan funkcji sieci.

![Informacje o integracji sieci wirtualnej ASP][5]

ASP integracji sieci wirtualnej z interfejsu użytkownika pokazuje wszystkie sieci wirtualnych, które są używane przez aplikacje w strona ASP. Może mieć maksymalnie 5 sieci wirtualne połączone za wszystkie pozostałe aplikacje w planie usługi App Service. Każda aplikacja może mieć tylko jeden skonfigurowano integrację. Aby wyświetlić szczegóły dotyczące każdej sieci wirtualnej, kliknij sieć wirtualną Cię interesuje. Istnieją dwie akcje, które można wykonać w tym miejscu.

* **Synchronizuj sieć**. Operacja sieci synchronizacji zapewnia, że certyfikaty i informacje o sieci są zsynchronizowane. W przypadku dodania lub zmiany w systemie DNS w sieci wirtualnej, należy wykonać **Synchronizuj sieć** operacji. Ta operacja spowoduje ponowne uruchomienie wszystkich aplikacji za pomocą tej sieci wirtualnej.
* **Dodawanie tras** dodawanie tras regulują ruch wychodzący w sieci wirtualnej.

**Routing** tras, które są zdefiniowane w Twojej sieci wirtualnej są używane do kierowania ruchem danych w sieci wirtualnej z poziomu aplikacji. Jeśli potrzebujesz wysłać dodatkowe ruch wychodzący w sieci wirtualnej, można dodać tych bloków adresów.   

**Certyfikaty** włączone podczas integracji sieci wirtualnej, jest wymagana wymiany certyfikatów do zabezpieczenia połączenia. Wraz z certyfikatów są konfiguracji serwera DNS, trasy i innych podobnych zadań, które opisują sieci.
Jeśli certyfikaty lub informacje o sieci zostało zmienione, musisz kliknąć przycisk "Sync sieci". Po kliknięciu przycisku "Synchronizuj sieć" można spowodować niedostępność łączność między aplikacją użytkownika a siecią wirtualną. Gdy aplikacja nie jest ponownie uruchamiany, utrata łączności może spowodować, witryny nie będzie działał prawidłowo. 

## <a name="accessing-on-premises-resources"></a>Uzyskiwanie dostępu do zasobów lokalnych
Aplikacje mogą uzyskiwać dostęp w zasoby lokalne dzięki integracji z sieciami wirtualnymi, który ma połączeń lokacja lokacja. Aby uzyskać dostęp do zasobów lokalnych, należy zaktualizować trasy bramy lokalnej sieci VPN za pomocą usługi bloki adresów punkt lokacja. Po pierwszym skonfigurowaniu sieci VPN typu lokacja lokacja skrypty używane do jego konfiguracji należy skonfigurować trasy prawidłowo. Jeśli dodasz adresy point-to-site, po utworzeniu sieci VPN lokacja lokacja, musisz ręcznie zaktualizować trasy. Szczegółowe informacje na temat jak to zrobić, różnią się na bramę i nie zostały opisane w tym miejscu. Ponadto nie może mieć skonfigurowany za pomocą połączenia sieci VPN typu lokacja lokacja protokołu BGP.

> [!NOTE]
> Funkcja integracji sieci wirtualnej nie umożliwia integracji aplikacji z siecią wirtualną, która ma bramę usługi ExpressRoute. Nawet jeśli brama usługi ExpressRoute jest w [tryb współistnienie] [ VPNERCoex] Integracja sieci wirtualnej nie działa. Jeśli potrzebujesz dostępu do zasobów za pośrednictwem połączenia usługi ExpressRoute, a następnie można użyć [środowiska App Service Environment][ASE], która działa w sieci wirtualnej. 
> 
> 

## <a name="peering"></a>Komunikacja równorzędna
Integracja sieci wirtualnej umożliwia dostęp do zasobów w sieciach wirtualnych, która jest połączona z siecią wirtualną nawiązano połączenie. Aby skonfigurować komunikację równorzędną, aby pracować z Twoją aplikacją:

1. Dodaj połączenie komunikacji równorzędnej w sieci wirtualnej aplikacja łączy się z firmą. Podczas dodawania połączenia komunikacji równorzędnej, Włącz **zezwolić na dostęp do sieci wirtualnej** i sprawdź **Zezwalaj na przekazywany ruch** i **Zezwalaj na tranzyt bramy**.
1. Dodaj połączenie komunikacji równorzędnej na sieć wirtualną, która jest jest połączona z siecią wirtualną nawiązano połączenie. Podczas dodawania połączenia komunikacji równorzędnej w lokalizacji docelowej sieci wirtualnej, Włącz **zezwolić na dostęp do sieci wirtualnej** i sprawdź **Zezwalaj na przekazywany ruch** i **Zezwalaj bram zdalnych**.
1. Przejdź do planu usługi App Service > Sieć > UI integracji sieci wirtualnej w portalu.  Wybierz sieć wirtualną, łączy się z aplikacją. W sekcji routingu Dodaj zakres adresów sieci wirtualnej, która jest połączona z siecią wirtualną, Twoja aplikacja jest połączona.  


## <a name="pricing-details"></a>Szczegóły cennika
Istnieją trzy powiązane opłaty do korzystania z funkcji integracji sieci wirtualnej:

* Cennik warstwy wymagania ASP
* Koszty transferu danych
* Koszty bramy sieci VPN.

Aplikacje należy standardowa, Premium lub PremiumV2 App Service Plan. Te koszty tutaj można zobaczyć więcej szczegółów: [Cennik usługi App Service][ASPricing]. 

Brak opłat za wyjście danych, nawet jeśli sieć wirtualna jest w tym samym centrum danych. Te opłaty są opisane w [Szczegóły ceny transferu danych][DataPricing]. 

Nic nie kosztuje do bramy sieci wirtualnej, która jest wymagana dla sieci VPN typu punkt lokacja. Szczegółowe informacje znajdują się na [cennik bramy sieci VPN] [ VNETPricing] strony.

## <a name="troubleshooting"></a>Rozwiązywanie problemów
Gdy ta funkcja jest łatwe do skonfigurowania, który nie oznacza, że środowisko będzie bezpłatne problem. Powinien wystąpić problemy podczas dostępu do żądanego punktu końcowego usługi są niektóre narzędzia używane do testowania łączności z aplikacji konsoli. Istnieją dwie konsole, które są dostępne. Jeden to konsola Kudu, a drugi to konsoli w witrynie Azure portal. Aby uzyskać dostęp do konsoli Kudu ze swojej aplikacji, przejdź do pozycji Narzędzia -> Kudu. To jest taka sama, jak zamierza [sitename]. scm.azurewebsites.net. Gdy które otwiera, przejdź do karty konsoli debugowania. Można pobrać z konsoli platformy Azure hostowanej portalu, a następnie z poziomu aplikacji przejdź do pozycji Narzędzia -> konsoli. 

#### <a name="tools"></a>Narzędzia
Narzędzia **ping**, **nslookup** i **tracert** nie będą działać przy użyciu konsoli z powodu ograniczeń zabezpieczeń. Aby wypełnić void, dodano dwa osobne narzędzia. Aby można było przetestować funkcje DNS, dodaliśmy narzędzia o nazwie nameresolver.exe. Składnia jest następująca:

    nameresolver.exe hostname [optional: DNS Server]

Możesz użyć **nameresolver** do sprawdzania nazwy hostów, która zależy od aplikacji. W ten sposób można sprawdzić, jeśli cokolwiek źle skonfigurowany serwer DNS, lub może nie mają dostępu do serwera DNS.

Narzędzie dalej pozwala do testowania łączności kombinację hosta i portu TCP. To narzędzie jest nazywany **tcpping** i składnia jest następująca:

    tcpping.exe hostname [optional: port]

**Tcpping** narzędzie informuje, jeśli możesz docierać do konkretnego hosta i portu. Można widoczna sukces tylko jeśli: istnieje aplikacja nasłuchuje na hoście i porcie kombinacji i ma dostęp do sieci z poziomu aplikacji na określonym hoście i porcie.

#### <a name="debugging-access-to-vnet-hosted-resources"></a>Debugowanie dostępu do sieci wirtualnej hostowanych zasobów
Istnieje kilka kwestii, które mogą uniemożliwić aplikacji docieranie do konkretnego hosta i portu. W większości przypadków jest to jeden z trzech zdarzeń:

* **Zapora jest w ten sposób.** Jeśli masz zaporę w taki sposób, spowoduje osiągnięcie limitu czasu protokołu TCP. Limit czasu protokołu TCP w tym przypadku jest 21 sekund. Użyj **tcpping** narzędzia do testowania łączności. Limity czasu protokołu TCP można ze względu na wiele elementów poza zaporami, ale od tego. 
* **DNS jest niedostępny.** Limit czasu DNS to trzy sekundy na serwerze DNS. Jeśli masz dwa serwery DNS, limit czasu jest 6 w ciągu kilku sekund. Użyj nameresolver, aby zobaczyć, jeśli serwer DNS działa. Pamiętaj, że nie można użyć nslookup, jak które nie używają DNS sieci wirtualnej jest konfigurowana.
* **Zakres adresów punkt lokacja jest nieprawidłowy.** Zakres adresów IP point-to-site musi znajdować się w dokumencie RFC 1918 prywatnych adresów IP (10.0.0.0-10.255.255.255 / 172.16.0.0-172.31.255.255 / 192.168.0.0-192.168.255.255). Jeśli zakres używa adresów IP poza który, elementy nie będą działać. 

Jeśli te elementy nie można znaleźć odpowiedzi problemu, sprawdź pierwszy podczas wykonywania prostych operacji, takich jak: 

* Czy brama jest wyświetlany jako znajdujące się w portalu?
* Czy certyfikaty są wyświetlane jako znajdujące się w synchronizacji?
* Każda osoba zmieniać konfiguracji sieciowej bez wykonania tej operacji "Synchronizuj sieć" w usterce ASP 

Jeśli brama nie działa, następnie połącz kopii zapasowej. Jeśli certyfikaty są zsynchronizowane, przejdź do widoku ASP Integracja z siecią wirtualną, a trafienia "Synchronizuj sieć". Jeśli podejrzewasz, że wprowadzono zmiany wprowadzone w konfiguracji sieci wirtualnej, który nie został zsynchronizowany z usługi ASP, a następnie naciśnij klawisz "Synchronizuj sieć".  Operacji "Synchronizuj sieć" nastąpi ponowne uruchomienie wszystkich aplikacji ASP, które są zintegrowane z tej sieci wirtualnej. 

Jeśli to wszystko jest poprawnie, należy się z materiałami nieco bardziej:

* Czy istnieją inne aplikacje, korzystając z integracji sieci wirtualnej do uzyskania dostępu do zasobów w tej samej sieci wirtualnej? 
* Można przejść do konsoli aplikacji i umożliwia tcpping dotrzeć do wszystkich innych zasobów w sieci wirtualnej? 

Jeśli wykonanie powyższych są spełnione, następnie Integracja z siecią wirtualną jest w dobrym stanie, a problem nie zostanie w innym miejscu. Jest to, gdzie staje się to być więcej wyzwanie, ponieważ nie ma prostego sposobu aby zobaczyć, dlaczego nie można nawiązać połączenia hosta: port. Przyczyny są między innymi:

* Możesz mieć zapory na hoście uniemożliwia dostęp do portu aplikacji z punktu do lokacji, zakres adresów IP. Często przekroczenie granic podsieci wymaga dostępu publicznego.
* dostęp do hosta docelowego nie działa
* Aplikacja nie działa
* ma nieprawidłowy adres IP lub nazwa hosta
* aplikacja nasłuchuje na innym porcie, niż oczekiwano. Za pomocą "netstat - aon" na hoście punktu końcowego może odnosić się Twój identyfikator procesu przy użyciu portu nasłuchiwania. 
* grupy zabezpieczeń sieci są skonfigurowane w taki sposób, że mogą uniemożliwić dostęp do aplikacji hosta i portu, z punktu do lokacji, zakres adresów IP

Należy pamiętać, że nie wiesz, jakiego adresu IP z zakresu adresów IP Point-to-Site, Twoja aplikacja będzie używać, więc należy zezwolić na dostęp z całego zakresu. 

Debugowanie dodatkowe kroki są następujące:

* Łączenie z maszyną Wirtualną w sieci wirtualnej i próbują uzyskać dostęp z Twojego zasobu: port hosta w tym miejscu. Aby sprawdzić, czy dostęp do protokołu TCP, użyj polecenia PowerShell **test-netconnection**. Składnia jest następująca:

      test-netconnection hostname [optional: -Port]

* wyświetlenie dostęp maszyny Wirtualnej i testowania aplikacji na tym hoście i porcie z konsoli z aplikacji

#### <a name="on-premises-resources"></a>Zasoby lokalne ####

Jeśli aplikacja nie może połączyć się z zasobem w środowisku lokalnym, sprawdź, w przypadku zasobów możesz się połączyć z siecią wirtualną. Użyj **test-netconnection** polecenia programu PowerShell, aby sprawdzić, czy dostęp do protokołu TCP. Jeśli maszyna wirtualna nie może połączyć się z zasobami lokalnymi, upewnij się, że połączenie sieci VPN między lokacjami działa. Jeśli to działa, Sprawdź te same elementy, jak wspomniano wcześniej, a także konfiguracji bramy w środowisku lokalnym i stanu. 

Jeśli sieć wirtualna hostowana maszyna wirtualna może nawiązać połączenie z lokalnym systemem, ale aplikacja nie może, a następnie przyczyną jest prawdopodobnie jednym z następujących powodów:

* trasy nie są skonfigurowane przy użyciu punktu do lokacji zakresów adresów IP w bramie lokalnej
* sieciowe grupy zabezpieczeń blokują dostęp dla zakresu adresów IP Point-to-Site
* zapory w środowisku lokalnym blokuje ruch z zakresowi adresów IP Point-to-Site


## <a name="powershell-automation"></a>Automatyzacja programu PowerShell

Usługa App Service można zintegrować z usługą Azure Virtual Network przy użyciu programu PowerShell. Gotowe do uruchomienia skryptu, zobacz [Połącz aplikację w usłudze Azure App Service do usługi Azure Virtual Network](https://gallery.technet.microsoft.com/scriptcenter/Connect-an-app-in-Azure-ab7527e3).

## <a name="hybrid-connections-and-app-service-environments"></a>Połączenia hybrydowe i środowiska usługi App Service
Istnieją trzy funkcje, które umożliwiają dostęp do zasobów hostowanych w sieci wirtualnej. Oto one:

* Integracja z siecią wirtualną
* Połączenia hybrydowe
* Środowiska usługi App Service

Połączenia hybrydowe wymagają zainstalowania agenta przekazywania o nazwie Manager(HCM) połączenie hybrydowe w sieci. HCM, musi mieć możliwość łączenia z platformy Azure, a także aplikacji. Połączenia hybrydowe nie wymaga dla ruchu przychodzącego internet dostępnym punkcie końcowym sieci zdalnej, w jakim są wymagane dla połączenia sieci VPN. HCM działa tylko w Windows, i może mieć maksymalnie pięć wystąpień do zapewnienia wysokiej dostępności. Mimo że połączeń hybrydowych TCP obsługuje tylko, a każdy punkt końcowy połączenia Hybrydowego musi być zgodna z kombinacji określonego hosta: port. 

Funkcja środowiska App Service umożliwia uruchamianie wystąpienia pojedynczej dzierżawy usługi Azure App Service w sieci wirtualnej. Jeśli Twoje aplikacje znajdują się w środowisku usługi App Service, aplikacje mają dostęp zasobów w sieci wirtualnej bez żadnych dodatkowych czynności. W środowisku usługi App Service swoje aplikacje na bardziej zaawansowanych procesów roboczych a można skalować do 100 wystąpień ASP. Środowiska usługi App Service działa z wszystkich funkcji sieciowych, w tym usługi ExpressRoute i punktów końcowych usługi.  

Choć używać niektórych przypadków nakładają się, żadna z tych funkcji można zastąpić, pozostałe. Wiedząc, jakie funkcji jest powiązany do Twoich potrzeb. Na przykład:

* Jeśli jesteś deweloperem i chcesz uruchomić witryny na platformie Azure, które ma dostęp do bazy danych na stacji roboczej w ramach działu, użyj najłatwiejszym jest połączeń hybrydowych. 
* Jeśli masz dużą organizację, która chce wstrzymać dużą liczbę właściwości sieci web w publicznej chmurze i zarządzaj nimi we własnej sieci, a następnie chcesz przejść za pomocą usługi App Service Environment. 
* Jeśli masz wiele aplikacji, które muszą uzyskać dostęp do zasobów w sieci wirtualnej, integracja z siecią wirtualną jest Zdajemy sobie. 

Po Twojej sieci wirtualnej jest już połączona z sieci lokalnej, wówczas używanie Integracja z siecią wirtualną lub usługę App Service Environment to prosty sposób korzystać z zasobów lokalnych. Jeśli sieć wirtualna nie jest połączony z siecią lokalną, jest znacznie większym obciążeniem do skonfigurowania sieci VPN między lokacjami z sieci wirtualnej w porównaniu z instalowaniem HCM. 

Poza różnice funkcjonalne, dostępne są także różnice cenowe. Funkcja środowiska App Service Environment jest Premium oferty usługi ale oferuje najbardziej sieci możliwości konfiguracji oprócz inne interesujące funkcje. Integracja sieci wirtualnej mogą być używane z warstwy standardowa lub Premium ASP i idealne w przypadku bezpieczne korzystanie z zasobów w sieci wirtualnej z wieloma dzierżawami usługi App Service. Połączenia hybrydowe obecnie zależy od BizTalk, konta, które ma poziomy, które Rozpocznij bezpłatnie, a następnie stopniowo bardziej kosztowne ceny, oparte na ilości, czego potrzebujesz. Jeśli chodzi o jednak działa w wielu sieciach, nie istnieje żadne inne funkcje, takie jak połączeń hybrydowych, które umożliwiają dostęp do zasobów w dobrze ponad 100 odrębnych sieci. 

## <a name="new-vnet-integration"></a>Nowa integracja z siecią wirtualną ##

Istnieje nowa wersja funkcji integracji sieci wirtualnej, które nie są zależne od technologii sieci VPN typu punkt-lokacja. W przeciwieństwie do istniejących funkcji nowa funkcja w wersji zapoznawczej będą działać przy użyciu usługi ExpressRoute i punktów końcowych usługi. 

Nowa funkcja jest dostępna tylko z nowszych jednostek skalowania w usłudze Azure App Service. Jeśli można przeprowadzać skalowanie do PremiumV2, jesteś w nowszych jednostki skalowania usługi App Service. Interfejs użytkownika Integracja sieci wirtualnej w portalu poinformuje, jeśli Twoja aplikacja może używać nowej funkcji integracji sieci wirtualnej. 

Nowa wersja jest dostępna w wersji zapoznawczej i ma następujące cechy.

* Brak bramy jest wymagana do używania nowej funkcji integracji sieci wirtualnej
* Bez żadnych dodatkowych konfiguracji oprócz integracji z usługą ExpressRoute połączona sieć wirtualna może dostęp do zasobów połączeń usługi ExpressRoute.
* Aplikacja i sieć wirtualna musi być w tym samym regionie
* Nowa funkcja wymaga nieużywanej podsieci w sieci wirtualnej usługi Resource Manager.
* Aplikacja musi być we wdrożeniu usługi Azure App Service, która jest w stanie skalowanie w górę do wersji Premium v2.
* Plan usługi App Service musi być planu Standard, Premium lub PremiumV2
* Obciążenia produkcyjne nie są obsługiwane w nowej funkcji, gdy znajduje się w wersji zapoznawczej
* Nowej funkcji integracji sieci wirtualnej nie działa dla aplikacji w środowisku usługi App Service.
* Nie można usunąć sieci wirtualnej za pomocą zintegrowanych aplikacji.  
* Tabele tras i globalnej komunikacji równorzędnej nie są jeszcze dostępne w nowej integracji sieci wirtualnej.  
* Jeden adres jest używany dla poszczególnych wystąpień planu usługi App Service. Ponieważ rozmiar podsieci nie można zmienić po przypisaniu, należy użyć podsieci, które więcej niż może obejmować rozmiaru skali maksymalnej. / 27 przy użyciu 32 adresów jest zalecany rozmiar, jak który może pomieścić, plan usługi App Service, który jest skalowany do 20 wystąpień.
* Będzie można korzystać przy użyciu nowej funkcji integracji sieci wirtualnej zasobów zabezpieczonych punktu końcowego usługi. Aby to zrobić, należy włączyć punkty końcowe usługi w podsieci użyć do integracji sieci wirtualnej.

Aby korzystać z nowych funkcji:

1. Przejdź do interfejsu użytkownika sieci, w portalu. Jeśli aplikacja jest w stanie korzystać z nowych funkcji, zostanie wyświetlone możliwości korzystania z nowych funkcji w wersji zapoznawczej.  

   ![Wybierz ikonę Podgląd nowego Integracja sieci wirtualnej][6]

1. Wybierz **Dodaj sieć wirtualna (wersja zapoznawcza)**.  

1. Wybierz sieć wirtualną usługi Resource Manager, który chcesz zintegrować z, a następnie utwórz nową podsieć lub wybrać już istniejącą pustą podsieć. Integracja zajmuje mniej niż minutę. Podczas integracji aplikacji zostanie ponownie uruchomiona.  Po ukończeniu integracji zobaczysz szczegółowe informacje dotyczące sieci wirtualnej są zintegrowane z usługą i Baner u góry strony, która informuje, że ta funkcja jest dostępna w wersji zapoznawczej.

   ![Wybierz sieć wirtualną i podsieć][7]

Aby włączyć aplikację do używania serwera DNS, który skonfigurowano sieci wirtualnej, należy utworzyć ustawienie aplikacji dla swojej aplikacji, której nazwa to WEBSITE_DNS_SERVER, a jego wartość to adres IP serwera.  Jeśli masz pomocniczy serwer DNS, Utwórz innego ustawienia aplikacji, której nazwa to WEBSITE_DNS_ALT_SERVER, a jego wartość to adres IP serwera. 

Aby odłączyć aplikację z sieci wirtualnej, wybierz **rozłączenia**. To spowoduje ponowne uruchomienie aplikacji sieci web. 

Nowej funkcji integracji sieci wirtualnej umożliwia użycie punktów końcowych usługi.  Punktów końcowych usługi za pomocą aplikacji, należy użyć nowej integracji sieci wirtualnej Aby nawiązać połączenie z wybranej sieci wirtualnej, a następnie skonfigurować punkty końcowe usługi w podsieci używanej do integracji. 

<!--Image references-->
[1]: ./media/web-sites-integrate-with-vnet/vnetint-app.png
[2]: ./media/web-sites-integrate-with-vnet/vnetint-addvnet.png
[3]: ./media/web-sites-integrate-with-vnet/vnetint-howitworks.png
[4]: ./media/web-sites-integrate-with-vnet/vnetint-details.png
[5]: ./media/web-sites-integrate-with-vnet/vnetint-aspdetails.png
[6]: ./media/web-sites-integrate-with-vnet/vnetint-newvnet.png
[7]: ./media/web-sites-integrate-with-vnet/vnetint-newvnetdetails.png
[8]: ./media/web-sites-integrate-with-vnet/vnetint-selectvnet.png

<!--Links-->
[VNETOverview]: https://azure.microsoft.com/documentation/articles/virtual-networks-overview/ 
[AzurePortal]: https://portal.azure.com/
[ASPricing]: https://azure.microsoft.com/pricing/details/app-service/
[VNETPricing]: https://azure.microsoft.com/pricing/details/vpn-gateway/
[DataPricing]: https://azure.microsoft.com/pricing/details/data-transfers/
[V2VNETP2S]: https://azure.microsoft.com/documentation/articles/vpn-gateway-howto-point-to-site-rm-ps/
[ASEintro]: environment/intro.md
[ILBASE]: environment/create-ilb-ase.md
[V2VNETPortal]: ../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md
[VPNERCoex]: ../expressroute/expressroute-howto-coexist-resource-manager.md
[ASE]: environment/intro.md
[creategatewaysubnet]: https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal#gatewaysubnet
[creategateway]: https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal#creategw
[setp2saddresses]: https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal#addresspool
