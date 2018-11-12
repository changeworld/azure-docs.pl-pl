---
title: Integrowanie aplikacji z siecią wirtualną platformy Azure
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
ms.date: 09/24/2018
ms.author: ccompy
ms.openlocfilehash: 5e7a2150757afeecb6b0b100f3259f1628f42074
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/07/2018
ms.locfileid: "51262084"
---
# <a name="integrate-your-app-with-an-azure-virtual-network"></a>Integrowanie aplikacji z siecią wirtualną platformy Azure
Ten dokument zawiera opis funkcji integracji sieci wirtualnej usługi Azure App Service i pokazuje, jak je skonfigurować za pomocą aplikacji w [usługi Azure App Service](https://go.microsoft.com/fwlink/?LinkId=529714). Jeśli jesteś zaznajomiony z sieciami wirtualnymi platformy Azure (Vnet), to funkcja, która pozwala na umieszczenie wielu zasobów platformy Azure w sieci lecz-internet, która umożliwia kontrolę dostępu do. Te sieci mogą być następnie połączone do sieci w środowisku lokalnym przy użyciu różnych technologii sieci VPN. Aby dowiedzieć się więcej o usłudze Azure Virtual Networks, rozpoczynać się w tym miejscu informacje: [Omówienie usługi Azure Virtual Network][VNETOverview]. 

Azure App Service ma dwie formy. 

1. Systemy wielodostępne, obsługujące pełną gamę planów cenowych
2. Funkcji premium usługi App Service Environment (ASE), która służy do wdrażania w sieci wirtualnej. 

W tym dokumencie omówiono Integracja sieci wirtualnej i nie środowiska App Service Environment. Jeśli chcesz dowiedzieć się więcej na temat funkcji środowiska ASE, start z informacjami w tym miejscu: [wprowadzenie środowiska App Service Environment][ASEintro].

Integracja sieci wirtualnej zapewnia Twojej aplikacji internetowej dostępu do zasobów w sieci wirtualnej, ale nie powoduje przyznania prywatny dostęp do aplikacji sieci web z sieci wirtualnej. Dostęp do prywatnej witryny dotyczy tylko udostępnianie aplikacji z sieci prywatnej takich jak z w obrębie sieci wirtualnej platformy Azure. Dostęp do prywatnej witryny jest dostępna tylko dla środowiska ASE skonfigurowane za pomocą wewnętrznego obciążenia równoważenia (ILB). Aby uzyskać szczegółowe informacje na temat korzystania z ASE z wewnętrznym modułem równoważenia obciążenia, rozpoczynać artykuł tutaj: [tworzenie i używanie środowisko ASE z wewnętrznym modułem równoważenia obciążenia][ILBASE]. 

Typowy scenariusz, w którym będzie korzystać z integracji sieci wirtualnej, jest zapewnienie dostępu z poziomu aplikacji sieci web z bazą danych lub usługi sieci web uruchomiony na maszynie wirtualnej w sieci wirtualnej platformy Azure. Integracja z siecią wirtualną nie wymagają do udostępnienia publicznego punktu końcowego dla aplikacji na maszynie Wirtualnej, ale można zamiast tego użyj prywatnych adresów Routing-internet. 

Funkcja integracji sieci wirtualnej:

* wymaga standardowa, Premium lub izolowany plan taryfowy 
* sprawdza się w modelu klasycznym lub sieci wirtualnej usługi Resource Manager 
* obsługuje protokoły TCP i UDP
* współpracuje z aplikacji mobilnych, interfejsów API sieci Web i aplikacje funkcji
* umożliwia aplikacji połączyć się z tylko 1 sieć wirtualną w czasie
* Umożliwia maksymalnie pięć sieci wirtualnych można zintegrować z usługą w planie usługi App Service 
* zezwala na tej samej sieci wirtualnej, który będzie używany przez wiele aplikacji w planie usługi App Service
* obsługuje wartość SLA 99,9%, ze względu na warunki umowy SLA dla bramy sieci wirtualnej

Istnieje kilka kwestii, które Integracja sieci wirtualnej nie obsługuje, w tym:

* zainstalowanie dysku
* Integracja usługi AD 
* NetBios
* dostęp do prywatnej witryny

### <a name="getting-started"></a>Wprowadzenie
Oto kilka rzeczy, o których należy pamiętać przed nawiązaniem połączenia z aplikacją sieci web do sieci wirtualnej:

* Integracja sieci wirtualnej działa tylko z aplikacji w **standardowa**, **Premium**, lub **izolowany** plan cenowy. Jeśli możesz włączyć tę funkcję, a następnie skalować planu usługi App Service do planu cenowego nieobsługiwanych aplikacji utracą połączenia z sieciami wirtualnymi używają. 
* Docelowa sieć wirtualna już istnieje, musi on mieć point-to-site VPN włączone za pomocą bramy o dynamicznym routingu przed podłączeniem do aplikacji. Jeśli brama jest skonfigurowana z routingiem statycznym, nie można włączyć punkt lokacja wirtualnej sieci prywatnej (VPN).
* Sieć wirtualna musi być w tej samej subskrypcji, co Plan(ASP) usługi Twojej aplikacji.
* Jeśli ta brama już istnieje włączony punkt do lokacji, a nie znajduje się w ramach podstawowej jednostki SKU, protokół IKEV2 musi być wyłączony w konfiguracji punkt lokacja.
* Aplikacje, które integrują się z siecią wirtualną używają DNS, który jest określony dla tej sieci wirtualnej.
* Domyślnie aplikacje zintegrowanie tylko kierować ruchem w sieci wirtualnej na podstawie tras, które są zdefiniowane w Twojej sieci wirtualnej. 

## <a name="enabling-vnet-integration"></a>Włączanie integracji sieci wirtualnej

Użytkownik może się połączyć aplikację do nowej lub istniejącej sieci wirtualnej. Jeśli tworzysz nową sieć w ramach integracji usługi oprócz tworzenie sieci wirtualnej, jest wstępnie skonfigurowane dla Ciebie bramy o dynamicznym routingu i sieci VPN typu punkt-lokacja jest włączona. 

> [!NOTE]
> Konfigurowanie nowej integracji sieci wirtualnej może potrwać kilka minut. 
> 
> 

Aby włączyć integrację sieci wirtualnej, Otwórz ustawienia aplikacji, a następnie wybierz sieć. Interfejs użytkownika, który otwiera oferuje trzy opcje sieci. Ten przewodnik jest tylko do integracji sieci wirtualnej do połączeń hybrydowych i środowisk usługi App Service są omówione w dalszej części tego dokumentu. 

Jeśli aplikacja jest nie prawidłowo planu cenowego, interfejs użytkownika umożliwia skalowanie planu na wyższy cennikiem ulubionego.

![][1]

### <a name="enabling-vnet-integration-with-a-pre-existing-vnet"></a>Włączanie integracji sieci wirtualnej przy użyciu wstępnie istniejącej sieci wirtualnej
Interfejs użytkownika Integracja sieci wirtualnej umożliwia wybranie z listy sieci wirtualnych. Klasyczne sieci wirtualne wskazuje, że są takie z wyrazem "Klasyczny" w nawiasach obok nazwy sieci wirtualnej. Lista jest posortowana w taki sposób, że sieci wirtualne usługi Resource Manager są wymienione jako pierwsze. Na ilustracji poniżej widać, można wybrać tylko jedną sieć wirtualną. Istnieje wiele przyczyn, sieci wirtualnej może być wyszarzone, w tym:

* sieć wirtualna jest w innej subskrypcji, że Twoje konto ma dostęp do
* sieć wirtualna nie ma włączone Point-to-Site
* sieć wirtualna nie ma bramy o dynamicznym routingu

![][2]

Aby włączyć integrację, kliknij sieć wirtualną, którą chcesz zintegrować z. Po wybraniu sieci wirtualnej, aplikacja jest automatycznie uruchamiany ponownie aby zmiany zaczęły obowiązywać. 

##### <a name="enable-point-to-site-in-a-classic-vnet"></a>Włącz punkt lokacja w klasycznej sieci wirtualnej

Jeśli nie ma bramy sieci wirtualnej, ani ma punkt-lokacja, następnie należy skonfigurować pierwszy. Aby skonfigurować bramę do klasycznej sieci wirtualnej, przejdź do portalu i wyświetlić listę Networks(classic) wirtualnego. Wybierz sieć, którą chcesz zintegrować z. Wybierz opcję połączenia sieci VPN. W tym miejscu można utworzyć punktu do lokacji sieci VPN a nawet go utworzyć bramę. Bramy trwa około 30 minut, co pozwoli uzyskać.

![][8]

##### <a name="enabling-point-to-site-in-a-resource-manager-vnet"></a>Włączanie punkt lokacja w sieci wirtualnej usługi Resource Manager
Aby skonfigurować sieć wirtualną usługi Resource Manager przy użyciu bramy i punkt-lokacja, można użyć programu PowerShell zgodnie z opisem w tym miejscu [Konfigurowanie połączenia punkt-lokacja z siecią wirtualną przy użyciu programu PowerShell] [ V2VNETP2S] lub Użyj portalu Azure, zgodnie z opisem w tym miejscu [Konfigurowanie połączenia punkt-lokacja z siecią wirtualną przy użyciu witryny Azure portal][V2VNETPortal]. Interfejs użytkownika do wykonywania tej funkcji nie jest jeszcze dostępna. Nie trzeba tworzyć certyfikaty w konfiguracji punkt-lokacja. Certyfikaty są automatycznie tworzone po nawiązaniu połączenia z siecią wirtualną przy użyciu portalu aplikacji sieci Web. 

### <a name="creating-a-pre-configured-vnet"></a>Utworzenie wstępnie skonfigurowany w sieci wirtualnej
Jeśli chcesz utworzyć nową sieć wirtualną, który jest skonfigurowany przy użyciu bramy i punkt-lokacja sieci interfejsu użytkownika usługi App Service ma możliwość to zrobić, ale tylko dla sieci wirtualnej usługi Resource Manager. Jeśli chcesz utworzyć klasyczną sieć wirtualną przy użyciu bramy i punkt-lokacja, musisz to zrobić ręcznie za pomocą interfejsu użytkownika sieci. 

Aby utworzyć sieć wirtualną usługi Resource Manager przy użyciu interfejsu użytkownika integracji sieci wirtualnej, wybierz **utworzyć nową sieć wirtualną** i zapewniają:

* Nazwa sieci wirtualnej
* Blok adresów sieci wirtualnej
* Nazwa podsieci
* Blok adresów podsieci
* Blok adresu bramy
* Blok adresów połączeń punkt-lokacja

Jeśli chcesz, aby ta sieć wirtualna i łączyć się z innymi sieciami, następnie należy unikać pobrania przestrzeń adresów IP, która nakłada się na tych sieciach. 

> [!NOTE]
> Tworzenie sieci wirtualnej usługi Resource Manager przy użyciu bramy trwa około 30 minut i obecnie nie jest zintegrowana z sieci wirtualnej wraz z aplikacją. Po utworzeniu sieci wirtualnej z bramą należy wrócić do swojej aplikacji interfejsu użytkownika integracji sieci wirtualnej, a następnie wybierz pozycję nowej sieci wirtualnej.
> 
> 

![][3]

Sieci wirtualnych platformy Azure są zwykle tworzone adresów sieci prywatnej. Domyślnie Integracja sieci wirtualnej funkcji rozsyła cały ruch przeznaczony dla tych zakresów adresów IP w sieci wirtualnej. Dostępne są następujące zakresy prywatnych adresów IP:

* 10.0.0.0/8 — jest to taka sama jak 10.0.0.0 - 10.255.255.255
* 172.16.0.0/12 — jest to taka sama jak 172.16.0.0 - 172.31.255.255 
* 192.168.0.0/16 — jest to taka sama jak 192.168.0.0 - 192.168.255.255

Przestrzeń adresowa sieci wirtualnej musi być podany w notacji CIDR. Jeśli jesteś zaznajomiony z notacji CIDR, jest to metoda określania bloków adresów przy użyciu adresu IP i liczba całkowita, która reprezentuje maska sieci. Jako szybkiego odwołania należy wziąć pod uwagę że 10.1.0.0/24 będzie 256 adresów i 10.1.0.0/25 będzie 128 adresami. Adres IPv4 z/32 będzie tylko 1 adres. 

Jeśli ustawisz tutaj informacje o serwerze DNS, który jest wartość dla sieci wirtualnej. Po utworzeniu sieci wirtualnej możesz edytować te informacje z sieci wirtualnej środowiska użytkownika. Jeśli zmienisz DNS sieci wirtualnej, należy wykonać operacji sieci synchronizacji.

Kiedy tworzysz klasyczną siecią wirtualną przy użyciu interfejsu użytkownika integracji sieci wirtualnej, tworzy sieć wirtualną w tej samej grupie zasobów co aplikacja. 

## <a name="how-the-system-works"></a>Jak działa system
Dzieje się w tle ta funkcja opiera się na podstawie technologii sieci VPN typu punkt-lokacja połączyć aplikację z siecią wirtualną. Aplikacje w usłudze Azure App Service mają architektury systemie wielodostępnym, co wyklucza inicjowania obsługi administracyjnej aplikacji bezpośrednio w sieci wirtualnej, co jest wykonywane przy użyciu maszyn wirtualnych. Tworzenie przy użyciu technologii point-to-site ograniczamy dostęp sieciowy do hostowania aplikacji tylko maszyny wirtualne. Dostęp do sieci jest także bardzo ograniczony na hostach aplikacji tak, aby Twoje aplikacje są dostępne tylko skonfigurowanych im to dostęp do sieci. 

![][4]

Jeśli nie skonfigurowano serwer DNS z sieci wirtualnej aplikacji należy używać adresów IP, aby dotrzeć do zasobów w sieci wirtualnej. Podczas korzystania z adresów IP, należy pamiętać, że główną zaletą tej funkcji jest umożliwia używanie prywatnych adresów w sieci prywatnej. Jeśli ustawisz aplikacji do użycia publiczne adresy IP dla jednej z maszyn wirtualnych, a następnie nie korzystasz z funkcji integracji sieci wirtualnej i komunikują się za pośrednictwem Internetu.

## <a name="managing-the-vnet-integrations"></a>Zarządzanie integracji sieci wirtualnej
Możliwość nawiązywanie i zakańczanie z siecią wirtualną jest na poziomie aplikacji. Operacje, które mogą wpłynąć na Integracja z siecią wirtualną przez wiele aplikacji są na poziomie ASP. Z poziomu interfejsu użytkownika, która jest wyświetlana na poziomie aplikacji możesz uzyskać szczegółowe informacje w Twojej sieci wirtualnej. Większość tych samych informacji jest również wyświetlany na poziomie ASP. 

![][5]

Na stronie Stan funkcji sieci widać, jeśli aplikacja jest połączona z siecią wirtualną. Jeśli brama sieci wirtualnej działa niezależnie od przyczyny, następnie to będą wyświetlane jako połączone nie. 

Informacje, które masz teraz dostępne w aplikacji, z poziomu interfejsu użytkownika z integracji sieci wirtualnej jest taka sama jak szczegółowe informacje, który jest pobierany z ASP. Poniżej przedstawiono te elementy:

* Nazwa sieci wirtualnej — ten link zostanie otwarty w sieci wirtualnej platformy Azure interfejsu użytkownika
* Lokalizacja — odzwierciedla lokalizację sieci wirtualnej. Istnieje możliwość integracji z siecią wirtualną w innej lokalizacji.
* Stan certyfikatu — Brak certyfikatów używanych do zabezpieczenia połączenia sieci VPN między aplikacją a siecią wirtualną. Odzwierciedla test, aby upewnić się, że są one zsynchronizowane.
* Stan bramy — bram należy dół jakiejś przyczyny następnie aplikacji nie dostęp do zasobów w sieci wirtualnej. 
* Przestrzeń adresowa sieci wirtualnej — jest to przestrzeń adresów IP dla sieci wirtualnej. 
* Przestrzeń adresowa punkt-lokacja — jest to punkt do lokacji IP przestrzeń adresowa sieci wirtualnej. Aplikacja zawiera komunikatu jako pochodzących z jednego z adresów IP w tej przestrzeni adresowej. 
* Witryny do przestrzeni adresowej lokacji — sieci VPN między lokacjami umożliwia podłączanie sieci wirtualnych do zasobów w środowisku lokalnym lub do innej sieci wirtualnej. Powinna mieć, który skonfigurowany, a następnie zakresów adresów IP zdefiniowane za pomocą w tym miejscu Pokazuje połączenia sieci VPN.
* Serwery DNS — w przypadku serwerów DNS skonfigurowanych przy użyciu sieci wirtualnej, a następnie są one wymienione w tym miejscu.
* Adresy IP kierowane do sieci wirtualnej — tam są listy adresów IP, które ma sieci wirtualnej, routing zdefiniowany dla i te adresy show w tym miejscu. 

Jedyną operacją, które można wykonać w widoku aplikacji Integracja z siecią wirtualną jest rozłączenie aplikacji z jest aktualnie połączony z siecią wirtualną. Aby odłączyć aplikację z sieci wirtualnej, odłącz u góry. Ta akcja nie powoduje zmiany sieci wirtualnej. Sieć wirtualna i jego konfiguracji, w tym bramy pozostaje bez zmian. Jeśli chcesz, następnie można usunąć sieci wirtualnej, należy najpierw usunąć zasoby w nim bramy. 

Wyświetl Plan usługi App Service ma szereg dodatkowych operacji. Jest on również dostępny inaczej niż w aplikacji. Aby uzyskać dostęp do interfejsu użytkownika sieci ASP, należy otworzyć ASP interfejsu użytkownika i przewiń w dół. Wybierz "Stan funkcji sieci" Aby otworzyć Interfejs użytkownika stan funkcji sieci. Wybierz pozycję "Kliknij tutaj, aby zarządzać" do listy integracji sieci wirtualnej, w tym ASP.

![][6]

Lokalizacja ASP jest dobrym pamiętać podczas wyszukiwania w lokalizacjach, które jest integrowany z sieciami wirtualnymi. Gdy sieć wirtualna jest w innej lokalizacji jest znacznie bardziej prawdopodobna zobaczyć problemów z opóźnieniem. 

Sieci wirtualne zintegrowane z to przypomnienie o jak wiele sieci wirtualnych, że Twoje aplikacje są zintegrowane z usługą w tym ASP i ile masz. 

Aby wyświetlić dodano szczegółowe informacje dotyczące każdej sieci wirtualnej, kliknij w sieci wirtualnej Cię interesuje. Oprócz szczegółów, które zostały wcześniej wspomniano można także wyświetlić listę aplikacji w tym ASP, które korzystają z tej sieci wirtualnej. 

W odniesieniu do działań istnieją dwie podstawowe akcje. Pierwszy jest możliwość dodawania tras, na których kierowania ruchu wychodzącego aplikacji w Twojej sieci wirtualnej. Drugiej akcji jest możliwość synchronizacji certyfikatów i informacji o sieci.

![][7]

**Routing** jak wspomniano wcześniej, tras, które są zdefiniowane w Twojej sieci wirtualnej są przeznaczenie kierowanie ruchu w sieci wirtualnej z poziomu aplikacji. Jednak gdy klienci chcą wysyłanie dodatkowych ruchu wychodzącego z aplikacji w sieci wirtualnej i ich ta funkcja jest dostarczany są do niektórych zastosowań. Co się dzieje z ruchu sieciowego po tym to maksymalnie jak klientów konfiguruje sieci wirtualnej. 

**Certyfikaty** stanu certyfikatu odzwierciedla sprawdzenie Trwa wykonywanie usługa App Service, aby sprawdzić, czy certyfikaty, których używasz dla połączenia sieci VPN są w dalszym ciągu idealnie sprawdzają. Po włączeniu Integracja sieci wirtualnej, jeśli jest to pierwszy integracji do tej sieci wirtualnej z dowolnej aplikacji, w tym ASP ma wymagane wymiany certyfikatów do zabezpieczenia połączenia. Wraz z certyfikatami uzyskujemy konfiguracji serwera DNS, trasy i innych podobnych zadań, które opisują sieci.
Zmiana tych certyfikatów lub informacje o sieci, należy kliknąć przycisk "Sync sieci". **Uwaga**: po kliknięciu przycisku "Synchronizuj sieć", a następnie spowodować niedostępność w łączności między aplikacją użytkownika a siecią wirtualną. Gdy aplikacja nie zostanie ponownie uruchomiony, utrata łączności może spowodować, witryny nie będzie działał prawidłowo. 

## <a name="accessing-on-premises-resources"></a>Uzyskiwanie dostępu do zasobów lokalnych
Jedną z zalet funkcji integracji sieci wirtualnej jest to, że jeśli Twojej sieci wirtualnej został połączony z siecią lokalną za pomocą sieci VPN między lokacjami Twoje aplikacje mogą uzyskiwać dostęp do zasobów lokalnych ze swojej aplikacji. Aby to działało, ale konieczne może być aktualizacja bramy sieci VPN w środowisku lokalnym za pomocą tras dla IP Point-to-Site należeć do zakresu. Gdy najpierw skonfigurować sieć VPN między lokacjami następnie skrypty używane do jego konfiguracji należy skonfigurować trasy, łącznie z sieci VPN punkt-lokacja. Jeśli dodasz VPN typu punkt-lokacja, po utworzeniu sieci VPN między lokacjami, musisz ręcznie zaktualizować trasy. Szczegółowe informacje na temat jak to zrobić, różnią się na bramę i nie zostały opisane w tym miejscu. 

> [!NOTE]
> Funkcja integracji sieci wirtualnej nie jest zintegrowana z aplikacji z siecią wirtualną, która ma bramę usługi ExpressRoute. Nawet jeśli brama usługi ExpressRoute jest w [tryb współistnienie] [ VPNERCoex] Integracja sieci wirtualnej nie działa. Jeśli potrzebujesz dostępu do zasobów za pośrednictwem połączenia usługi ExpressRoute, a następnie można użyć [środowiska App Service Environment][ASE], która działa w sieci wirtualnej.
> 
> 

## <a name="pricing-details"></a>Szczegóły cennika
Istnieje kilka ceny wszystkie szczegóły, które należy wiedzieć podczas korzystania z funkcji integracji sieci wirtualnej. Istnieją 3 powiązanych opłat do korzystania z tej funkcji:

* Cennik warstwy wymagania ASP
* Koszty transferu danych
* Koszty bramy sieci VPN.

Dla swoich aplikacji można było korzystać z tej funkcji muszą należeć do standardowa lub Premium App Service Plan. Zostanie wyświetlone więcej szczegółów na tych kosztów, w tym miejscu: [App Service — ceny][ASPricing]. 

Ze względu na sposób obsługi sieci VPN typu punkt-lokacja zawsze masz opłata za dane wychodzące za pośrednictwem połączenia Integracja sieci wirtualnej, nawet jeśli sieć wirtualna znajduje się w tym samym centrum danych. Aby zobaczyć, jakie tych opłat, zapoznaj się tutaj: [Szczegóły ceny transferu danych][DataPricing]. 

Ostatni element to koszt bram sieci wirtualnej. Jeśli nie potrzebujesz bramy do czegoś innego, takich jak sieci VPN między lokacjami są płacenia za bramy służące do obsługi funkcji integracji sieci wirtualnej. W tych kosztów, w tym miejscu znajdują się szczegółowe informacje: [cennik bramy sieci VPN][VNETPricing]. 

## <a name="troubleshooting"></a>Rozwiązywanie problemów
Gdy ta funkcja jest łatwe do skonfigurowania, który nie oznacza, że środowisko będzie bezpłatne problem. Powinien wystąpić problemy podczas dostępu do żądanego punktu końcowego usługi są niektóre narzędzia używane do testowania łączności z aplikacji konsoli. Istnieją dwa środowiska konsoli, których można użyć. Jest jednym z poziomu konsoli Kudu, a drugi to konsola, który może osiągnąć w witrynie Azure portal. Aby uzyskać dostęp do konsoli Kudu ze swojej aplikacji przejdź do pozycji Narzędzia -> Kudu. To jest taka sama, jak zamierza [sitename]. scm.azurewebsites.net. Gdy które otwiera, przejdź do karty konsoli debugowania. Można pobrać z konsoli platformy Azure hostowanej portalu, a następnie z poziomu aplikacji przejdź do pozycji Narzędzia -> konsoli. 

#### <a name="tools"></a>Narzędzia
Narzędzia **ping**, **nslookup** i **tracert** nie będą działać przy użyciu konsoli z powodu ograniczeń zabezpieczeń. Aby wypełnić void miejsca zostały dodano dwa osobne narzędzia. Aby można było przetestować funkcje DNS dodaliśmy narzędzia o nazwie nameresolver.exe. Składnia jest następująca:

    nameresolver.exe hostname [optional: DNS Server]

Możesz użyć **nameresolver** do sprawdzania nazwy hostów, która zależy od aplikacji. W ten sposób można sprawdzić, jeśli cokolwiek źle skonfigurowany serwer DNS, lub może nie mają dostępu do serwera DNS.

Narzędzie dalej pozwala do testowania łączności kombinację hosta i portu TCP. To narzędzie jest nazywany **tcpping** i składnia jest następująca:

    tcpping.exe hostname [optional: port]

**Tcpping** narzędzie informuje, jeśli możesz docierać do konkretnego hosta i portu. Można widoczna sukces tylko jeśli: istnieje aplikacja nasłuchuje na hoście i porcie kombinacji i ma dostęp do sieci z poziomu aplikacji na określonym hoście i porcie.

#### <a name="debugging-access-to-vnet-hosted-resources"></a>Debugowanie dostępu do sieci wirtualnej hostowanych zasobów
Istnieje kilka kwestii, które mogą uniemożliwić aplikacji docieranie do konkretnego hosta i portu. W większości przypadków jest to jeden z trzech zdarzeń:

* **Znajduje się zapora w ten sposób** mając zaporę w taki sposób, spowoduje osiągnięcie limitu czasu protokołu TCP. W tym przypadku to 21 sekund. Użyj **tcpping** narzędzia do testowania łączności. Limity czasu protokołu TCP można ze względu na wiele elementów poza zaporami, ale od tego. 
* **Serwer DNS nie jest dostępny** limit czasu DNS to trzy sekundy na serwerze DNS. Jeśli masz dwa serwery DNS, limit czasu jest 6 w ciągu kilku sekund. Użyj nameresolver, aby zobaczyć, jeśli serwer DNS działa. Pamiętaj, że nie można użyć nslookup, jak nie używa DNS sieci wirtualnej jest konfigurowana.
* **Nieprawidłowy zakres adresów IP P2S** wskaż zakres adresów IP lokacji musi znajdować się w dokumencie RFC 1918 prywatnych adresów IP (10.0.0.0-10.255.255.255 / 172.16.0.0-172.31.255.255 / 192.168.0.0-192.168.255.255). Jeśli zakres używa adresów IP poza który, elementy nie będą działać. 

Jeśli te elementy nie można znaleźć odpowiedzi problemu, sprawdź pierwszy podczas wykonywania prostych operacji, takich jak: 

* Czy brama jest wyświetlany jako znajdujące się w portalu?
* Czy certyfikaty są wyświetlane jako znajdujące się w synchronizacji?
* Każda osoba zmieniać konfiguracji sieciowej bez wykonania tej operacji "Synchronizuj sieć" w usterce ASP 

Jeśli brama nie działa, następnie połącz kopii zapasowej. Jeśli certyfikaty są zsynchronizowane, przejdź do widoku ASP Integracja z siecią wirtualną, a trafienia "Synchronizuj sieć". W przypadku Jeśli podejrzewasz, że wprowadzono zmiany wprowadzone do konfiguracji sieci wirtualnej, a nie był synchronizacji usługi ASP, a następnie przejdź do widoku ASP Integracja z siecią wirtualną i trafień "Synchronizuj sieć" Just będąca przypomnieniem, powoduje to niedostępność przy użyciu połączenia sieć wirtualna, a aplikacje. 

Jeśli to wszystko jest poprawnie, należy się z materiałami nieco bardziej:

* Czy istnieją inne aplikacje, korzystając z integracji sieci wirtualnej do uzyskania dostępu do zasobów w tej samej sieci wirtualnej? 
* Można przejść do konsoli aplikacji i umożliwia tcpping dotrzeć do wszystkich innych zasobów w sieci wirtualnej? 

Jeśli wykonanie powyższych są spełnione, następnie Integracja z siecią wirtualną jest w dobrym stanie, a problem nie zostanie w innym miejscu. Jest to, gdzie staje się to być więcej wyzwanie, ponieważ nie ma prostego sposobu aby zobaczyć, dlaczego nie można nawiązać połączenia hosta: port. Przyczyny są między innymi:

* Możesz mieć zapory na hoście uniemożliwia dostęp do portu aplikacji z punktu do lokacji, zakres adresów IP. Często przekroczenie granic podsieci wymaga dostępu publicznego.
* dostęp do hosta docelowego nie działa
* Aplikacja nie działa
* ma nieprawidłowy adres IP lub nazwa hosta
* aplikacja nasłuchuje na innym porcie, niż oczekiwano. Możesz to sprawdzić, przechodząc na tym hoście i za pomocą "netstat - aon" w wierszu polecenia. Zawiera identyfikator nasłuchuje na porcie co procesu. 
* grupy zabezpieczeń sieci są skonfigurowane w taki sposób, że mogą uniemożliwić dostęp do aplikacji hosta i portu, z punktu do lokacji, zakres adresów IP

Należy pamiętać, że nie wiesz, jakiego adresu IP z zakresu adresów IP Point-to-Site, Twoja aplikacja będzie używać, więc należy zezwolić na dostęp z całego zakresu. 

Debugowanie dodatkowe kroki są następujące:

* Łączenie z maszyną Wirtualną w sieci wirtualnej i próbują uzyskać dostęp z Twojego zasobu: port hosta w tym miejscu. Aby przetestować protokołu TCP dla dostępu, użyj polecenia PowerShell **test-netconnection**. Składnia jest następująca:

      test-netconnection hostname [optional: -Port]

* wyświetlenie dostęp maszyny Wirtualnej i testowania aplikacji na tym hoście i porcie z konsoli z aplikacji

#### <a name="on-premises-resources"></a>Zasoby lokalne ####

Jeśli aplikacja nie może połączyć się z zasobem w środowisku lokalnym, sprawdź, w przypadku zasobów możesz się połączyć z siecią wirtualną. Użyj **test-netconnection** polecenia programu PowerShell, aby to zrobić. Jeśli maszyna wirtualna nie może połączyć się z zasobami lokalnymi, upewnij się, że połączenie sieci VPN między lokacjami działa. Jeśli to działa, Sprawdź te same elementy, jak wspomniano wcześniej, a także konfiguracji bramy w środowisku lokalnym i stanu. 

Hostowane w sieci wirtualnej maszyny Wirtualnej może nawiązać połączenie z lokalnym systemem, ale aplikacja nie może przyczyną jest prawdopodobnie jednym z następujących czynności:

* trasy nie są skonfigurowane przy użyciu punktu do lokacji zakresów adresów IP w bramie lokalnej
* sieciowe grupy zabezpieczeń blokują dostęp dla zakresu adresów IP Point-to-Site
* zapory w środowisku lokalnym blokuje ruch z zakresowi adresów IP Point-to-Site
* masz trasę zdefiniowaną przez użytkownika w Twojej sieci wirtualnej, który uniemożliwia ruchu na podstawie Point-to-Site docieranie do sieci lokalnej

## <a name="powershell-automation"></a>Automatyzacja programu PowerShell

Usługa App Service można zintegrować z usługą Azure Virtual Network przy użyciu programu PowerShell. Gotowe do uruchomienia skryptu, zobacz [Połącz aplikację w usłudze Azure App Service do usługi Azure Virtual Network](https://gallery.technet.microsoft.com/scriptcenter/Connect-an-app-in-Azure-ab7527e3).

## <a name="hybrid-connections-and-app-service-environments"></a>Połączenia hybrydowe i środowiska usługi App Service
Istnieją trzy funkcje, które umożliwiają dostęp do zasobów hostowanych w sieci wirtualnej. Oto one:

* Integracja sieci wirtualnej
* Połączenia hybrydowe
* Środowiska usługi App Service

Połączenia hybrydowe wymagają zainstalowania agenta przekazywania o nazwie Manager(HCM) połączenie hybrydowe w sieci. HCM, musi mieć możliwość łączenia z platformy Azure, a także aplikacji. To rozwiązanie jest szczególnie doskonałe z siecią zdalną, takich jak sieci lokalnej lub sieci jest hostowana w chmurze nawet inny, ponieważ nie wymaga dostępnym punkcie końcowym internet. HCM działa tylko w Windows, i może mieć maksymalnie pięć wystąpień do zapewnienia wysokiej dostępności. Mimo że połączeń hybrydowych TCP obsługuje tylko, a każdy punkt końcowy połączenia Hybrydowego musi być zgodna z kombinacji określonego hosta: port. 

Funkcja środowiska App Service umożliwia uruchamianie wystąpienia usługi Azure App Service w sieci wirtualnej. Dzięki temu Twoje aplikacje uzyskują dostęp do zasobów w sieci wirtualnej bez żadnych dodatkowych czynności. Niektóre inne korzyści zapewnianych przez usługę App Service Environment są, użyć pracowników Dv2, w oparciu o 14 GB pamięci RAM. Kolejną korzyścią jest możliwość skalowania systemu do własnych potrzeb. W przeciwieństwie do środowiska wielodostępnym, w którym strona ASP jest ograniczona do 20 wystąpień w środowisku ASE można skalować do 100 wystąpień ASP. Jest jedną z rzeczy, które oferuje środowisko ASE wewnętrznego nie za pomocą integracji sieci wirtualnej, czy środowisko usługi App Service może współpracować z usługi ExpressRoute sieci VPN. 

Choć używać niektórych przypadków nakładają się, żadna z tych funkcji można zastąpić, pozostałe. Wiedząc, jakie funkcji jest powiązany do Twoich potrzeb. Na przykład:

* Jeśli jesteś deweloperem i chcesz uruchomić witryny na platformie Azure, a jego dostęp do bazy danych na stacji roboczej w ramach działu, użyj najłatwiejszym jest połączeń hybrydowych. 
* Jeśli masz dużą organizację, która chce wstrzymać dużą liczbę właściwości sieci web w publicznej chmurze i zarządzaj nimi we własnej sieci, a następnie chcesz przejść za pomocą usługi App Service Environment. 
* Jeśli masz wiele aplikacji, które muszą uzyskać dostęp do zasobów w sieci wirtualnej, integracja z siecią wirtualną jest Zdajemy sobie. 

Jeśli sieć wirtualna jest już połączony z siecią lokalną, następnie za pomocą Integracja z siecią wirtualną lub usługę App Service Environment to prosty sposób z zasobami lokalnymi. Jeśli sieć wirtualna nie jest połączony z siecią w środowisku lokalnym, jest znacznie większym obciążeniem do skonfigurowania sieci VPN między lokacjami z sieci wirtualnej w porównaniu z instalowaniem HCM. 

Poza różnice funkcjonalne, dostępne są także różnice cenowe. Funkcja środowiska App Service Environment jest Premium oferty usługi ale oferuje najbardziej sieci możliwości konfiguracji oprócz inne interesujące funkcje. Integracja sieci wirtualnej mogą być używane z warstwy standardowa lub Premium ASP i idealne w przypadku bezpieczne korzystanie z zasobów w sieci wirtualnej z wieloma dzierżawami usługi App Service. Połączenia hybrydowe obecnie zależy od BizTalk, konta, które ma poziomy, które Rozpocznij bezpłatnie, a następnie stopniowo bardziej kosztowne ceny, oparte na ilości, czego potrzebujesz. Jeśli chodzi o jednak działa w wielu sieciach, nie istnieje żadne inne funkcje, takie jak połączeń hybrydowych, które umożliwiają dostęp do zasobów w dobrze ponad 100 odrębnych sieci. 

## <a name="new-vnet-integration"></a>Nową opcję integracji z siecią wirtualną ##

Istnieje nowa wersja funkcji integracji sieci wirtualnej, które nie są zależne od technologii sieci VPN typu punkt-lokacja. Ta nowa wersja jest dostępna w wersji zapoznawczej. Nowe możliwości integracji sieci wirtualnej ma następujące cechy.

- Nowa funkcja wymaga nieużywanej podsieci w sieci wirtualnej usługi Resource Manager
- Jeden adres jest używany dla poszczególnych wystąpień planu usługi App Service. Ponieważ rozmiar podsieci nie można zmienić po przypisaniu, należy użyć podsieci, które więcej niż może obejmować rozmiaru skali maksymalnej. / 27 przy użyciu 32 adresów jest zalecany rozmiar, jak który może pomieścić, plan usługi App Service, który jest skalowany do 20 wystąpień.
- Można używać punktu końcowego usługi zasobów zabezpieczonych za pomocą nowej funkcji integracji sieci wirtualnej. Włącz dostęp z podsieci, przypisany do aplikacji, aby skonfigurować punkty końcowe usługi z Twoją aplikacją
- Dostęp do zasobów połączeń usługi ExpressRoute bez przeprowadzania dodatkowej konfiguracji
- Brak bramy jest wymagana do używania nowej funkcji integracji sieci wirtualnej
- Plan usługi App Service musi być planu Standard, Premium lub PremiumV2
- Nowa funkcja jest dostępna tylko z nowszych jednostek skalowania w usłudze Azure App Service. Portal poinformuje, jeśli Twoja aplikacja może używać nowej funkcji integracji sieci wirtualnej. 
- Aplikacja i sieć wirtualna musi być w tym samym regionie

Nowej funkcji integracji sieci wirtualnej jest początkowo dostępna tylko w regionach w Europie północnej oraz wschodnie stany USA.


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
