---
title: Integrowanie aplikacji z usługą Azure Virtual Network — Azure App Service
description: Pokazuje, jak połączyć aplikację w Azure App Service z nową lub istniejącą siecią wirtualną platformy Azure
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
ms.date: 07/25/2019
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: 8321a9dd779406b2d1de44bd4c9313e4d855548d
ms.sourcegitcommit: 0f54f1b067f588d50f787fbfac50854a3a64fff7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/12/2019
ms.locfileid: "68740892"
---
# <a name="integrate-your-app-with-an-azure-virtual-network"></a>Integrowanie aplikacji z usługą Azure Virtual Network
W tym dokumencie opisano funkcję integracji Azure App Service sieci wirtualnej i sposób jej konfigurowania z aplikacjami w [Azure App Service](https://go.microsoft.com/fwlink/?LinkId=529714). [Sieci wirtualne platformy Azure][VNETOverview] (Sieci wirtualnych) umożliwia umieszczenie wielu zasobów platformy Azure w sieci bez obsługi Internetu.  

Azure App Service ma dwie odmiany. 

1. Systemy wielodostępne, które obsługują pełny zakres planów cenowych, z wyjątkiem izolowanych
2. App Service Environment (ASE), który wdraża w sieci wirtualnej i obsługuje aplikacje izolowanego planu cenowego

Ten dokument obejmuje dwie funkcje integracji sieci wirtualnej, które są używane w App Service z wieloma dzierżawcami. Jeśli Twoja aplikacja znajduje się w [App Service Environment][ASEintro], jest już w sieci wirtualnej i nie wymaga użycia funkcji integracji sieci wirtualnej w celu uzyskania dostępu do zasobów w tej samej sieci wirtualnej. Aby uzyskać szczegółowe informacje na temat wszystkich App Service funkcji sieciowych, Przeczytaj [App Service funkcje sieciowe](networking-features.md)

Istnieją dwa formy funkcji integracji sieci wirtualnej

1. Jedna wersja umożliwia integrację z sieci wirtualnych w tym samym regionie. Ta forma funkcji wymaga podsieci w sieci wirtualnej w tym samym regionie. Ta funkcja jest nadal w wersji zapoznawczej, ale jest obsługiwana w przypadku obciążeń produkcyjnych aplikacji systemu Windows z pewnymi zastrzeżeniami wymienionymi poniżej.
2. Inna wersja umożliwia integrację z usługą sieci wirtualnych w innych regionach lub z klasyczną sieci wirtualnych. Ta wersja funkcji wymaga wdrożenia bramy Virtual Network w sieci wirtualnej. Jest to funkcja oparta na sieci VPN typu punkt-lokacja i jest obsługiwana tylko w aplikacjach systemu Windows.

Aplikacja może jednocześnie korzystać z jednej formy funkcji integracji sieci wirtualnej. Następnie pytanie to funkcja, której należy użyć. Możesz użyć dowolnej dla wielu rzeczy. Jasne odróżniające są następujące:

| Problem  | Rozwiązanie | 
|----------|----------|
| Chcesz uzyskać dostęp do adresu RFC 1918 (10.0.0.0/8, 172.16.0.0/12, 192.168.0.0/16) w tym samym regionie | Integracja z regionalną siecią wirtualną |
| Chcesz uzyskać dostęp do zasobów w klasycznej sieci wirtualnej lub sieci wirtualnej w innym regionie | Integracja sieci wirtualnej wymagana przez bramę |
| Chcesz uzyskać dostęp do punktów końcowych RFC 1918 w całym ExpressRoute | Integracja z regionalną siecią wirtualną |
| Chcesz uzyskać dostęp do zasobów między punktami końcowymi usługi | Integracja z regionalną siecią wirtualną |

Żadna funkcja nie pozwoli na dostęp do adresów innych niż RFC 1918 w ramach ExpressRoute. Aby to zrobić, musisz teraz użyć środowiska ASE.

Użycie integracji regionalnej sieci wirtualnej nie łączy sieć wirtualną z lokalnymi lub konfiguruje punkty końcowe usługi. Jest to osobna konfiguracja sieci. Integracja regionalnej sieci wirtualnej po prostu umożliwia aplikacji wykonywanie wywołań w ramach tych typów połączeń.

Niezależnie od używanej wersji integracja z siecią wirtualną umożliwia aplikacji sieci Web dostęp do zasobów w sieci wirtualnej, ale nie przyznaje prywatnego dostępu do aplikacji sieci Web z sieci wirtualnej. Dostęp do lokacji prywatnej dotyczy udostępniania aplikacji tylko z sieci prywatnej, takiej jak z poziomu sieci wirtualnej platformy Azure. Integracja z siecią wirtualną dotyczy tylko wykonywania połączeń wychodzących z aplikacji do sieci wirtualnej. 

Funkcja integracji sieci wirtualnej:

* wymaga planu cenowego Standard, Premium lub PremiumV2 
* obsługuje protokoły TCP i UDP
* współpracuje z aplikacjami App Service i aplikacjami funkcji

Istnieje kilka rzeczy, które nie są obsługiwane przez integrację sieci wirtualnej, w tym:

* Instalowanie dysku
* Integracja z usługą AD 
* NetBios

## <a name="regional-vnet-integration"></a>Integracja z regionalną siecią wirtualną 

Gdy Integracja sieci wirtualnej jest używana z sieci wirtualnych w tym samym regionie, w którym znajduje się aplikacja, wymaga użycia delegowanej podsieci z co najmniej 32 adresem. Podsieci nie można używać dla żadnych innych elementów. Wywołania wychodzące wykonane z aplikacji zostaną nawiązane z adresów w podsieci delegowanej. W przypadku korzystania z tej wersji integracji sieci wirtualnej wywołania są nawiązywane z adresów w sieci wirtualnej. Użycie adresów w sieci wirtualnej umożliwia aplikacji:

* Wykonywanie wywołań usługi zabezpieczonych punktów końcowych usługi
* Dostęp do zasobów między połączeniami ExpressRoute
* Dostęp do zasobów w sieci wirtualnej, z którą nawiązano połączenie
* Dostęp do zasobów w połączeniach komunikacji równorzędnej, w tym połączeń ExpressRoute

Ta funkcja jest w wersji zapoznawczej, ale jest obsługiwana w przypadku obciążeń produkcyjnych aplikacji systemu Windows z następującymi ograniczeniami:

* Można uzyskać dostęp tylko do adresów znajdujących się w zakresie RFC 1918. Są to adresy w blokach adresów 10.0.0.0/8, 172.16.0.0/12 i 192.168.0.0/16.
* Nie można uzyskać dostępu do zasobów w ramach globalnych połączeń komunikacji równorzędnej
* Nie można ustawić tras dla ruchu pochodzącego z aplikacji do sieci wirtualnej
* Funkcja jest dostępna tylko w nowszych App Service jednostkach skalowania, które obsługują plany App Service PremiumV2.
* Podsieć integracji może być używana tylko przez jeden plan App Service
* Funkcja nie może być używana przez aplikacje planu izolowanego, które znajdują się w App Service Environment
* Ta funkcja wymaga nieużywanej podsieci, która jest/27 z 32 adresów lub większa w sieci wirtualnej Menedżer zasobów
* Aplikacja i Sieć wirtualna muszą znajdować się w tym samym regionie
* Nie można usunąć sieci wirtualnej przy użyciu zintegrowanej aplikacji. Najpierw należy usunąć integrację 
* Możesz mieć tylko jedną regionalną integrację sieci wirtualnej na App Service plan. Wiele aplikacji w tym samym planie App Service może korzystać z tej samej sieci wirtualnej. 

Jeden adres jest używany dla każdego wystąpienia planu App Service. Jeśli aplikacja została przeskalowana do 5 wystąpień, oznacza to, że używane są 5 adresów. Ponieważ nie można zmienić rozmiaru podsieci po przypisaniu, należy użyć podsieci, która jest wystarczająco duża, aby można było dowolnie skalować dostęp do aplikacji. Zalecanym rozmiarem jest/27 z 32 adres, który będzie uwzględniać plan App Service w warstwie Premium, który jest skalowany do 20 wystąpień.

Jeśli chcesz, aby aplikacje w innym App Service planować dostęp do sieci wirtualnej, która jest już połączona z aplikacjami w innym planie App Service, musisz wybrać inną podsieć niż ta, która jest używana przez istniejącą integrację z siecią wirtualną.  

Ta funkcja jest dostępna w wersji zapoznawczej dla systemu Linux. Aby użyć funkcji integracji sieci wirtualnej z siecią wirtualną Menedżer zasobów w tym samym regionie:

1. Przejdź do interfejsu użytkownika sieci w portalu. Jeśli aplikacja będzie mogła korzystać z nowej funkcji, zobaczysz opcję dodania sieci wirtualnej (wersja zapoznawcza).  

   ![Wybierz integrację sieci wirtualnej][6]

1. Wybierz pozycję **Dodaj sieć wirtualną (wersja zapoznawcza)** .  

1. Wybierz sieć wirtualną Menedżer zasobów, z którą chcesz zintegrować program, a następnie utwórz nową podsieć lub Wybierz pustą istniejącą podsieć. Integracja trwa krócej niż minutę. W trakcie integracji aplikacja zostanie ponownie uruchomiona.  Po zakończeniu integracji zobaczysz szczegóły dotyczące zintegrowanej sieci wirtualnej oraz transparent u góry informujący o tym, że ta funkcja jest dostępna w wersji zapoznawczej.

   ![Wybieranie sieci wirtualnej i podsieci][7]

Gdy aplikacja zostanie zintegrowana z siecią wirtualną, użyje tego samego serwera DNS, z którym jest skonfigurowana Sieć wirtualna. 

Aby odłączyć aplikację od sieci wirtualnej, wybierz pozycję **Rozłącz**. Spowoduje to ponowne uruchomienie aplikacji sieci Web. 


#### <a name="web-app-for-containers"></a>Web App for Containers

Jeśli używasz App Service w systemie Linux z wbudowanymi obrazami, funkcja integracji regionalnej sieci wirtualnej działa bez dodatkowych zmian. W przypadku używania Web App for Containers należy zmodyfikować obraz platformy Docker, aby można było korzystać z integracji z siecią wirtualną. W obrazie platformy Docker Użyj zmiennej środowiskowej portu jako portu nasłuchu głównego serwera sieci Web, zamiast używać numeru portu stałe. Zmienna środowiskowa portu jest automatycznie ustawiana przez App Service platformę w czasie uruchamiania kontenera.

### <a name="service-endpoints"></a>Punkty końcowe usługi

Nowa funkcja integracji sieci wirtualnej umożliwia korzystanie z punktów końcowych usługi.  Aby korzystać z punktów końcowych usługi w aplikacji, należy użyć nowej integracji sieci wirtualnej w celu nawiązania połączenia z wybraną siecią wirtualną, a następnie skonfigurować punkty końcowe usługi w podsieci użytej do integracji. 


### <a name="how-vnet-integration-works"></a>Jak działa integracja z siecią wirtualną

Aplikacje w App Service są hostowane na rolach procesów roboczych. Plany cenowe w warstwach Podstawowa i wyższa są dedykowanymi planami hostingu, w których nie ma żadnych innych obciążeń klientów działających w ramach tych samych procesów roboczych. Integracja z siecią wirtualną działa przez zainstalowanie interfejsów wirtualnych z adresami w podsieci delegowanej. Ponieważ adres od jest w sieci wirtualnej, ma dostęp do większości rzeczy w sieci wirtualnej lub przez sieć wirtualną, podobnie jak w przypadku maszyny wirtualnej w sieci wirtualnej. Implementacja sieci jest inna niż w przypadku uruchamiania maszyny wirtualnej w sieci wirtualnej, co oznacza, że niektóre funkcje sieciowe nie są jeszcze dostępne podczas korzystania z tej funkcji.

![Integracja sieci wirtualnej](media/web-sites-integrate-with-vnet/vnet-integration.png)

Po włączeniu integracji sieci wirtualnej aplikacja nadal będzie wyłączać połączenia wychodzące do Internetu za pomocą tych samych kanałów co normalny. Adresy wychodzące, które są wyświetlane w portalu właściwości aplikacji, nadal są adresami używanymi przez aplikację. Jakie zmiany w aplikacji to, wywołania do usług zabezpieczonych przez punkt końcowy usługi lub adresy RFC 1918 są umieszczane w sieci wirtualnej. 

Funkcja obsługuje tylko jeden interfejs wirtualny na proces roboczy.  Jeden wirtualny interfejs na proces roboczy oznacza jedną regionalną integrację sieci wirtualnej na App Service plan. Wszystkie aplikacje w tym samym planie App Service mogą korzystać z tej samej integracji sieci wirtualnej, ale jeśli potrzebna jest aplikacja do łączenia się z dodatkową siecią wirtualną, konieczne będzie utworzenie kolejnego planu App Service. Używany interfejs wirtualny nie jest zasobem, do którego klienci mają bezpośredni dostęp.

Ze względu na to, jak działa ta technologia, ruch używany z integracją sieci wirtualnej nie jest wyświetlany w dziennikach przepływów Network Watcher lub sieciowej grupy zabezpieczeń.  

## <a name="gateway-required-vnet-integration"></a>Integracja sieci wirtualnej wymagana przez bramę 

Funkcja integracji sieci wirtualnej wymagana przez bramę:

* Może służyć do nawiązywania połączeń z sieci wirtualnych w dowolnym regionie, które są Menedżer zasobów lub klasyczne sieci wirtualnych
* Umożliwia aplikacji łączenie tylko z 1 siecią wirtualną jednocześnie
* Umożliwia zintegrowanie maksymalnie pięciu sieci wirtualnych z planem App Service 
* Umożliwia korzystanie z tej samej sieci wirtualnej przez wiele aplikacji w planie App Service bez wpływu na łączną liczbę, która może być używana przez plan App Service.  Jeśli masz 6 aplikacji korzystających z tej samej sieci wirtualnej w ramach tego samego planu App Service, który jest liczony jako 1 Sieć wirtualna. 
* Wymaga bramy Virtual Network skonfigurowanej z użyciem sieci VPN typu punkt-lokacja
* Obsługuje umowę SLA na 99,9% ze względu na umowę SLA dotyczącą bramy

Ta funkcja nie obsługuje:
* Korzystanie z aplikacji systemu Linux
* Uzyskiwanie dostępu do zasobów w usłudze ExpressRoute 
* Uzyskiwanie dostępu do zasobów w obrębie punktów końcowych usługi 

### <a name="getting-started"></a>Wprowadzenie

Poniżej przedstawiono niektóre kwestie, które należy wziąć pod uwagę przed połączeniem aplikacji sieci Web z siecią wirtualną:

* Docelowa sieć wirtualna musi mieć włączoną sieć VPN typu punkt-lokacja z bramą opartą na trasach, zanim będzie mogła zostać podłączona do aplikacji. 
* Sieć wirtualna musi znajdować się w tej samej subskrypcji co plan App Service (ASP).
* Aplikacje, które integrują się z siecią wirtualną, używają systemu DNS określonego dla tej sieci wirtualnej.

### <a name="set-up-a-gateway-in-your-vnet"></a>Konfigurowanie bramy w sieci wirtualnej ###

Jeśli masz już skonfigurowaną bramę z adresami punkt-lokacja, możesz pominąć, aby skonfigurować integrację sieci wirtualnej z aplikacją.  
Aby utworzyć bramę:

1. [Utwórz podsieć bramy][creategatewaysubnet] w sieci wirtualnej.  

1. [Utwórz bramę sieci VPN][creategateway]. Wybierz typ sieci VPN opartej na trasach.

1. [Ustaw pozycję adresy witryn][setp2saddresses]. Jeśli Brama nie znajduje się w podstawowej jednostce SKU, protokół IKEV2 musi być wyłączony w konfiguracji punkt-lokacja, a protokół SSTP musi być wybrany. Przestrzeń adresowa musi należeć do bloków adresów RFC 1918, 10.0.0.0/8, 172.16.0.0/12, 192.168.0.0/16

Jeśli właśnie tworzysz bramę do użycia z integracją App Service sieci wirtualnej, nie musisz przekazywać certyfikatu. Tworzenie bramy może potrwać 30 minut. Nie będzie można zintegrować aplikacji z siecią wirtualną do momentu aprowizacji bramy. 

### <a name="configure-vnet-integration-with-your-app"></a>Konfigurowanie integracji sieci wirtualnej z aplikacją 

Aby włączyć integrację sieci wirtualnej w aplikacji: 

1. Przejdź do aplikacji w Azure Portal i Otwórz aplet Ustawienia aplikacji, a następnie wybierz pozycję Sieć > Integracja z siecią wirtualną. Środowisko ASP musi znajdować się w standardowej jednostce SKU lub lepiej korzystać z funkcji integracji sieci wirtualnej. 
 ![Interfejs użytkownika integracji sieci wirtualnej][1]

1. Wybierz pozycję **Dodaj sieć wirtualną**. 
 ![Dodaj integrację sieci wirtualnej][2]

1. Wybierz sieć wirtualną. 
  ![Wybierz sieć wirtualną][8]
  
Aplikacja zostanie ponownie uruchomiona po ostatnim kroku.  

### <a name="how-the-gateway-required-vnet-integration-feature-works"></a>Jak działa funkcja integracji sieci wirtualnej wymagana przez bramę

Funkcja integracja z siecią wirtualną wymagana przez bramę jest oparta na technologii sieci VPN typu punkt-lokacja. Technologia punkt-lokacja ogranicza dostęp sieciowy tylko do maszyny wirtualnej obsługującej aplikację. Aplikacje są ograniczone tylko do wysyłania ruchu do Internetu za pomocą Połączenia hybrydowe lub przez integrację sieci wirtualnej. 

![Jak działa integracja z siecią wirtualną][3]

## <a name="managing-vnet-integration"></a>Zarządzanie integracją sieci wirtualnej
Możliwość nawiązywania połączenia i rozłączenia z siecią wirtualną odbywa się na poziomie aplikacji. Operacje, które mogą mieć wpływ na integrację sieci wirtualnej w wielu aplikacjach, są na poziomie planu App Service. W portalu integracja z aplikacją > Networking > sieci wirtualnej możesz uzyskać szczegółowe informacje dotyczące sieci wirtualnej. Podobne informacje można wyświetlić na poziomie ASP w portalu sieci > ASP, > Portal Integration, w tym, które aplikacje w tym planie App Service używają danej integracji.

 ![Szczegóły sieci wirtualnej][4]

Informacje dostępne dla Ciebie w interfejsie użytkownika integracji sieci wirtualnej są takie same między portalami aplikacji i ASP.

* Nazwa sieci wirtualnej — linki do interfejsu użytkownika
* Location — odzwierciedla lokalizację sieci wirtualnej. Integracja z siecią wirtualną w innej lokalizacji może powodować problemy z opóźnieniami aplikacji. 
* Stan certyfikatu — odzwierciedla, czy certyfikaty są zsynchronizowane między planem App Service i siecią wirtualną.
* Stan bramy — należy używać integracji z siecią wirtualną wymagana przez bramę, aby zobaczyć stan bramy.
* Przestrzeń adresów sieci wirtualnej — pokazuje przestrzeń adresów IP dla sieci wirtualnej. 
* Przestrzeń adresowa punkt-lokacja — pokazuje punkt do przestrzeni adresów IP lokacji dla sieci wirtualnej. Podczas wykonywania wywołań do sieci wirtualnej podczas korzystania z funkcji wymaganej przez bramę, adres z aplikacji będzie mieć jeden z tych adresów. 
* Przestrzeń adresowa lokacja-lokacja — za pomocą sieci VPN typu lokacja-lokacja można połączyć sieć wirtualną z zasobami lokalnymi lub z innymi sieciami wirtualnymi. Zakresy adresów IP zdefiniowane za pomocą tego połączenia sieci VPN są wyświetlane w tym miejscu.
* Serwery DNS — pokazuje serwery DNS skonfigurowane z siecią wirtualną.
* Adresy IP kierowane do sieci wirtualnej — pokazuje bloki adresów kierowane do dysku sieciowego 

Jedyną operacją, którą można wykonać w widoku aplikacji integracji sieci wirtualnej, jest rozłączenie aplikacji z sieci wirtualnej, z którą jest ona aktualnie połączona. Aby odłączyć aplikację od sieci wirtualnej, wybierz pozycję **Rozłącz**. Aplikacja zostanie uruchomiona ponownie po rozłączeniu z siecią wirtualną. Rozłączanie nie zmienia sieci wirtualnej. Podsieć lub Brama nie jest usuwana. Jeśli chcesz usunąć sieć wirtualną, musisz najpierw odłączyć aplikację od sieci wirtualnej i usunąć z niej zasoby, takie jak bramy. 

Aby uzyskać dostęp do interfejsu użytkownika integracji sieci wirtualnej ASP, Otwórz interfejs użytkownika ASP i wybierz pozycję **Sieć**.  W obszarze integracja z siecią wirtualną wybierz **pozycję kliknij tutaj, aby skonfigurować** program, aby otworzyć interfejs użytkownika stanu funkcji sieci.

![Informacje o integracji sieci wirtualnej ASP][5]

Interfejs użytkownika integracji sieci wirtualnej ASP pokaże wszystkie sieci wirtualnych, które są używane przez aplikacje w środowisku ASP. Aby wyświetlić szczegółowe informacje o każdej sieci wirtualnej, kliknij sieć wirtualną, której chcesz zainteresować. Istnieją dwie akcje, które można wykonać w tym miejscu.

* **Synchronizuj sieć**. Operacja synchronizacji sieci dotyczy tylko funkcji integracji wirtualnej zależnej od bramy. Wykonanie operacji synchronizacji sieci gwarantuje, że certyfikaty i informacje o sieci są zsynchronizowane. Jeśli dodasz lub zmienisz system DNS sieci wirtualnej, musisz wykonać operację **synchronizacji sieci** . Ta operacja spowoduje ponowne uruchomienie wszystkich aplikacji korzystających z tej sieci wirtualnej.
* **Dodawanie tras** Dodanie tras spowoduje przekierowanie ruchu wychodzącego do sieci wirtualnej.

**Routing** Trasy, które są zdefiniowane w sieci wirtualnej, są używane do kierowania ruchu do sieci wirtualnej z aplikacji. Jeśli musisz wysłać dodatkowy ruch wychodzący do sieci wirtualnej, możesz dodać te bloki adresów tutaj. Ta funkcja działa tylko z wirtualną integracją sieci wirtualnej.

**Certyfikaty** Gdy brama wymaga integracji sieci wirtualnej, wymagana jest wymiana certyfikatów w celu zapewnienia bezpieczeństwa połączenia. Wraz z certyfikatami są Konfiguracja DNS, trasy i inne podobne elementy opisujące sieć.
Jeśli zostaną zmienione informacje o certyfikatach lub sieci, należy kliknąć pozycję "Synchronizuj sieć". Po kliknięciu przycisku "Synchronizuj sieć" pojawi się krótka awaria połączenia między aplikacją a siecią wirtualną. Gdy aplikacja nie zostanie uruchomiona ponownie, utrata łączności może spowodować, że lokacja nie działa prawidłowo. 

## <a name="accessing-on-premises-resources"></a>Uzyskiwanie dostępu do zasobów lokalnych
Aplikacje mogą uzyskiwać dostęp do zasobów lokalnych przez integrację z usługą sieci wirtualnych, która ma połączenia lokacja-lokacja. W przypadku korzystania z integracji sieci wirtualnej wymagane przez bramę należy zaktualizować trasy lokalnej bramy sieci VPN do bloków adresów punkt-lokacja. Po pierwszym skonfigurowaniu sieci VPN typu lokacja-lokacja, skrypty używane do skonfigurowania należy prawidłowo skonfigurować trasy. W przypadku dodania adresów punkt-lokacja po utworzeniu sieci VPN typu lokacja-lokacja należy ręcznie zaktualizować trasy. Szczegółowe informacje o tym, jak to zrobić, różnią się w zależności od bramy i nie zostały opisane tutaj. Nie można skonfigurować protokołu BGP z połączeniem sieci VPN typu lokacja-lokacja.

Nie jest wymagana dodatkowa konfiguracja funkcji integracji regionalnej sieci wirtualnej z siecią wirtualną ani w środowisku lokalnym. Wystarczy połączyć sieć wirtualną z lokalnymi przy użyciu usługi ExpressRoute lub sieci VPN typu lokacja-lokacja. 

> [!NOTE]
> Funkcja integracji sieci wirtualnej wymagana przez bramę nie integruje aplikacji z siecią wirtualną, która ma bramę ExpressRoute. Nawet jeśli Brama ExpressRoute jest skonfigurowana w [trybie][VPNERCoex] współistnienia, integracja sieci wirtualnej nie działa. Jeśli potrzebujesz dostępu do zasobów za pomocą połączenia ExpressRoute, możesz użyć funkcji integracji regionalnej sieci wirtualnej lub [App Service Environment][ASE], która działa w sieci wirtualnej. 
> 
> 

## <a name="peering"></a>Komunikacja równorzędna
Jeśli używasz komunikacji równorzędnej z integracją regionalnej sieci wirtualnej, nie musisz wykonywać żadnych dodatkowych czynności konfiguracyjnych. 

Jeśli jest używana brama wymagana Integracja sieci wirtualnej z usługą komunikacji równorzędnej, należy skonfigurować kilka dodatkowych elementów. Aby skonfigurować komunikację równorzędną do pracy z aplikacją:

1. Dodaj połączenie komunikacji równorzędnej w sieci wirtualnej, z którą łączy się aplikacja. Podczas dodawania połączenia komunikacji równorzędnej Włącz **dostęp do sieci wirtualnej** i sprawdź, czy **Zezwalaj na ruch przesłany dalej** i **Zezwalaj na tranzyt bramy**.
1. Dodaj połączenie komunikacji równorzędnej w sieci wirtualnej, która jest połączona z siecią wirtualną, z którą nawiązano połączenie. Podczas dodawania połączenia komunikacji równorzędnej w docelowej sieci wirtualnej Włącz **dostęp do usługi Virtual Network** i sprawdź, czy Zezwalaj na **ruch przesłany dalej** i **Zezwalaj na bramy zdalne**.
1. Przejdź do planu App Service > Networking > interfejsie użytkownika integracji sieci wirtualnej w portalu.  Wybierz sieć wirtualną, z którą aplikacja nawiązuje połączenie. W sekcji Routing Dodaj zakres adresów sieci wirtualnej, która jest połączona z siecią wirtualną.  


## <a name="pricing-details"></a>Szczegóły cennika
Funkcja integracji regionalnej sieci wirtualnej nie ma dodatkowej opłaty za użycie poza warstwą cenową ASP.

Istnieją trzy powiązane opłaty za korzystanie z funkcji integracji sieci wirtualnej wymagane przez bramę:

* Opłaty za warstwę cenową ASP — Twoje aplikacje muszą być zgodne z planem App Service Standard, Premium lub PremiumV2. Więcej informacji o tych kosztach można znaleźć tutaj: [Cennik App Service][ASPricing]. 
* Koszty transferu danych — naliczane są opłaty za wychodzące dane, nawet jeśli sieć wirtualna znajduje się w tym samym centrum danych. Te opłaty są opisane w [transfer danych szczegóły cennika][DataPricing]. 
* VPN Gateway koszty — istnieje koszt dla bramy sieci wirtualnej, która jest wymagana w przypadku połączeń VPN typu punkt-lokacja. Szczegóły znajdują się na stronie [cennika VPN Gateway][VNETPricing] .


## <a name="troubleshooting"></a>Rozwiązywanie problemów
Chociaż ta funkcja jest łatwa do skonfigurowania, nie oznacza to, że Twoje środowisko nie będzie miało problemu. Jeśli wystąpią problemy z uzyskaniem dostępu do żądanego punktu końcowego, istnieją pewne narzędzia, których można użyć do testowania łączności z poziomu konsoli aplikacji. Istnieją dwie konsole, których można użyć. Jedna z nich jest konsolą kudu, a druga jest konsolą w Azure Portal. Aby nawiązać połączenie z konsolą kudu z poziomu aplikacji, przejdź do pozycji narzędzia-> kudu. Jest to takie samo, jak w przypadku programu [sitename]. SCM. azurewebsites. NET. Po jego otwarciu przejdź do karty konsola debugowania. Aby przejść do Azure Portal hostowanej konsoli, w aplikacji przejdź do pozycji narzędzia — > Konsola. 

#### <a name="tools"></a>Narzędzia
Narzędzia **ping**, **nslookup** i **tracert** nie przełączają się za pomocą konsoli ze względu na ograniczenia zabezpieczeń. Aby wypełnić wartość void, dodano dwa osobne narzędzia. W celu przetestowania funkcjonalności DNS dodaliśmy narzędzie o nazwie nameresolver. exe. Składnia jest następująca:

    nameresolver.exe hostname [optional: DNS Server]

Możesz użyć **nameresolver** , aby sprawdzić nazwy hostów, od których zależy aplikacja. W ten sposób możesz sprawdzić, czy masz jakieś nieprawidłowo skonfigurowane usługi DNS lub że nie masz dostępu do serwera DNS. Możesz zobaczyć serwer DNS, który będzie używany przez aplikację w konsoli programu, przeglądając zmienne środowiskowe WEBSITE_DNS_SERVER i WEBSITE_DNS_ALT_SERVER.

Następne narzędzie umożliwia przetestowanie łączności TCP z połączeniem hosta i portu. To narzędzie ma nazwę **tcpping** , a Składnia:

    tcpping.exe hostname [optional: port]

Narzędzie **tcpping** informuje o tym, czy można nawiązać połączenie z określonym hostem i portem. Można wyświetlić tylko sukces, jeśli: istnieje aplikacja nasłuchująca na hoście i w kombinacji portów i istnieje dostęp do sieci z aplikacji do określonego hosta i portu.

#### <a name="debugging-access-to-vnet-hosted-resources"></a>Debugowanie dostępu do hostowanych zasobów sieci wirtualnej
Istnieje wiele rzeczy, które mogą uniemożliwić aplikacji osiągnięcie określonego hosta i portu. Większość czasu jest jedną z trzech rzeczy:

* **Zapora jest w ten sposób.** Jeśli w ten sposób masz zaporę, zostanie osiągnięty limit czasu protokołu TCP. Limit czasu protokołu TCP wynosi 21 sekund w tym przypadku. Użyj narzędzia **tcpping** , aby przetestować łączność. Limity czasu protokołu TCP mogą być spowodowane wieloma niektórymi zaporami, ale w tym miejscu. 
* **Usługa DNS jest niedostępna.** Limit czasu DNS wynosi trzy sekundy na serwer DNS. Jeśli masz dwa serwery DNS, limit czasu wynosi 6 sekund. Użyj nameresolver, aby sprawdzić, czy usługa DNS działa. Należy pamiętać, że nie można użyć polecenia nslookup, które nie korzysta z serwera DNS skonfigurowanego dla sieci wirtualnej. W przypadku niedostępności może być dostępna zapora lub sieciowej grupy zabezpieczeń blokujące dostęp do systemu DNS lub może nie działać.

Jeśli te elementy nie odpowiadają na problemy, należy najpierw sprawdzić, jak: 

**Integracja z regionalną siecią wirtualną**
* Czy miejscem docelowym jest adres RFC 1918
* Czy istnieje sieciowej grupy zabezpieczeń blokujące ruch wychodzący z podsieci integracji
* w przypadku przechodzenia między ExpressRoute lub sieci VPN czy brama lokalna jest skonfigurowana do kierowania ruchu z kopii zapasowej na platformę Azure? Jeśli masz dostęp do punktów końcowych w sieci wirtualnej, ale nie w środowisku lokalnym, możesz to sprawdzić.

**Integracja sieci wirtualnej wymagana przez bramę**
* czy zakres adresów punkt-lokacja w zakresach RFC 1918 (10.0.0.0-10.255.255.255/172.16.0.0-172.31.255.255/192.168.0.0-192.168.255.255)?
* Czy brama ma być wyświetlana w portalu? Jeśli Brama nie działa, przenieś ją z powrotem.
* Czy certyfikaty są wyświetlane jako zsynchronizowane lub czy podejrzewasz, że konfiguracja sieci została zmieniona?  Jeśli certyfikaty nie są zsynchronizowane lub podejrzewasz, że wprowadzono zmiany w konfiguracji sieci wirtualnej, które nie zostały zsynchronizowane z nazywani, naciśnij pozycję "Synchronizuj sieć".
* w przypadku przechodzenia między ExpressRoute lub sieci VPN czy brama lokalna jest skonfigurowana do kierowania ruchu z kopii zapasowej na platformę Azure? Jeśli masz dostęp do punktów końcowych w sieci wirtualnej, ale nie w środowisku lokalnym, możesz to sprawdzić.

Debugowanie problemów z siecią jest wyzwaniem, ponieważ nie można zobaczyć, co blokuje dostęp do określonego hosta: kombinacja portów. Niektóre z tych przyczyn obejmują:

* na hoście istnieje Zapora uniemożliwiająca dostęp do portu aplikacji z zakresu adresów IP punktu do lokacji. Przekroczenia podsieci często wymagają dostępu publicznego.
* host docelowy nie działa
* Aplikacja nie działa
* masz nieprawidłowy adres IP lub nazwa hosta
* aplikacja nasłuchuje na innym porcie niż oczekiwano. Identyfikator procesu można dopasować do portu nasłuchiwania przy użyciu polecenia "netstat-Aon" na hoście punktu końcowego. 
* sieciowe grupy zabezpieczeń są skonfigurowane w taki sposób, że uniemożliwiają dostęp do hosta i portu aplikacji z zakresu adresów IP punktu do lokacji

Należy pamiętać, że nie wiesz, jaki jest adres używany przez aplikację. Może to być dowolny adres w podsieci integracji lub zakres adresów punktu do lokacji, dlatego należy zezwolić na dostęp z całego zakresu adresów. 

Dodatkowe kroki debugowania obejmują:

* Nawiąż połączenie z maszyną wirtualną w sieci wirtualnej i spróbuj skontaktować się z hostem zasobów w tym miejscu: port. Aby przetestować dostęp do protokołu TCP, użyj polecenia programu PowerShell **test-NetConnection**. Składnia jest następująca:

      test-netconnection hostname [optional: -Port]

* Wywołaj aplikację na maszynie wirtualnej i przetestuj dostęp do tego hosta i portu z konsoli programu z poziomu aplikacji przy użyciu usługi **tcpping**

#### <a name="on-premises-resources"></a>Zasoby lokalne ####

Jeśli aplikacja nie może nawiązać połączenia z lokalnym zasobem, sprawdź, czy możesz uzyskać dostęp do zasobu z sieci wirtualnej. Użyj polecenia **test-NetConnection** PowerShell, aby sprawdzić dostęp do protokołu TCP. Jeśli maszyna wirtualna nie może nawiązać połączenia z zasobem lokalnym, połączenie sieci VPN lub ExpressRoute może być niepoprawnie skonfigurowane.

Jeśli hostowana maszyna wirtualna może nawiązać połączenie z systemem lokalnym, ale aplikacja nie może, przyczyna jest prawdopodobnie jedną z następujących przyczyn:

* trasy nie są skonfigurowane z podsiecią lub wskazują zakresy adresów lokacji w bramie lokalnej
* sieciowe grupy zabezpieczeń blokują dostęp do zakresu adresów IP punkt-lokacja
* Zapora lokalna blokuje ruch z zakresu adresów IP punkt-lokacja
* próbujesz nawiązać połączenie z adresem innym niż RFC 1918 przy użyciu funkcji integracji regionalnej sieci wirtualnej


## <a name="powershell-automation"></a>Automatyzacja programu PowerShell

Możesz zintegrować App Service z Virtual Network platformy Azure przy użyciu programu PowerShell. Aby uzyskać gotowy do uruchomienia skrypt, zobacz [łączenie aplikacji w Azure App Service z usługą Azure Virtual Network](https://gallery.technet.microsoft.com/scriptcenter/Connect-an-app-in-Azure-ab7527e3).


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
