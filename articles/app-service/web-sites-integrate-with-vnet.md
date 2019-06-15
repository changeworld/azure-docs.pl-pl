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
ms.date: 06/06/2019
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: a5187ed299f77c11892c6e34c8dfd3f904c7e075
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "67067712"
---
# <a name="integrate-your-app-with-an-azure-virtual-network"></a>Integrowanie aplikacji z siecią wirtualną platformy Azure
Ten dokument zawiera opis funkcji integracji sieci wirtualnej usługi Azure App Service i jak je skonfigurować za pomocą aplikacji w [usługi Azure App Service](https://go.microsoft.com/fwlink/?LinkId=529714). [Sieci wirtualne platformy Azure] [ VNETOverview] (Vnet) pozwala na umieszczenie wielu zasobów platformy Azure w sieci, lecz-internet.  

Azure App Service ma dwie formy. 

1. Systemy wielodostępne, obsługujące pełną gamę cennikami z wyjątkiem izolowany
2. App Service Environment (ASE), służy do wdrażania w sieci wirtualnej, która obsługuje cennik aplikacje plan izolowany

W tym dokumencie przechodzi przez te dwie funkcje integracji sieci wirtualnej, która jest przeznaczona do użytku w wielodostępnej usłudze App Service. Jeśli aplikacja znajduje się w [środowiska App Service Environment][ASEintro], znajduje się już w sieci wirtualnej, a nie wymaga użycia funkcji integracji sieci wirtualnej w celu uzyskania dostępu do zasobów w tej samej sieci wirtualnej. Aby uzyskać szczegółowe informacje o wszystkich funkcji obsługi sieci w usłudze App Service, przeczytaj [funkcji obsługi sieci w usłudze App Service](networking-features.md)

Istnieją dwie formy funkcji integracji sieci wirtualnej

1. Jedna wersja umożliwia integrację z sieciami wirtualnymi w tym samym regionie. Ta forma funkcji wymaga podsieci w sieci wirtualnej, w tym samym regionie. Ta funkcja jest nadal w wersji zapoznawczej, ale jest obsługiwana w przypadku obciążeń produkcyjnych aplikacji Windows za pomocą niektóre zastrzeżenia wskazanych poniżej.
2. Druga wersja umożliwia integrację z sieciami wirtualnymi w innych regionach lub z klasycznymi sieciami wirtualnymi. Ta wersja funkcji wymaga wdrożenia bramy sieci wirtualnej do sieci wirtualnej. Jest to funkcja sieci VPN na podstawie punkt lokacja.

Aplikację można używać tylko jednej formy funkcji integracji sieci wirtualnej w danym momencie. Następnie pytania jest o tym, której funkcji należy używać. Możesz użyć albo wiele zastosowań. Wyczyść różnic jednak są następujące:

| Problem  | Rozwiązanie | 
|----------|----------|
| Chcesz osiągnąć adres RFC 1918 (10.0.0.0/8, 172.16.0.0/12 i 192.168.0.0/16), w tym samym regionie | Integracja regionalnej sieci wirtualnej |
| Aby osiągnąć klasycznej sieci wirtualnej lub sieci wirtualnej w innym regionie | Brama wymagane Integracja sieci wirtualnej |
| Aby trafiające RFC 1918 punktów końcowych za pośrednictwem usługi ExpressRoute | Integracja regionalnej sieci wirtualnej |
| Do uzyskania dostępu do zasobów w obrębie punktów końcowych usługi | Integracja regionalnej sieci wirtualnej |

Żadna funkcja umożliwi trafiające adresów — RFC 1918 za pośrednictwem usługi ExpressRoute. W przypadku wykonywania konieczne korzystanie ze środowiska ASE teraz.

Za pomocą integracji regionalnej sieci wirtualnej nie połączenie Twojej sieci wirtualnej w środowisku lokalnym lub skonfiguruj punkty końcowe usługi. Który jest oddzielony, konfigurację sieci. Integracji regionalnej sieci wirtualnej po prostu umożliwia aplikacji wykonywanie wywołań między te typy połączeń.

Niezależnie od wersji używany Integracja z siecią wirtualną pozwala aplikacji sieci web dostęp do zasobów w sieci wirtualnej, ale nie są przyznawane dla ruchu przychodzącego prywatny dostęp do aplikacji sieci web z sieci wirtualnej. Dostęp do prywatnej witryny dotyczy tylko udostępnianie aplikacji z sieci prywatnej takich jak z w obrębie sieci wirtualnej platformy Azure. Integracja z siecią wirtualną jest tylko dla połączeń wychodzących z aplikacji w Twojej sieci wirtualnej. 

Funkcja integracji sieci wirtualnej:

* wymaga Standard, Premium lub PremiumV2 planu cenowego 
* obsługuje protokoły TCP i UDP
* współdziałanie z usługami aplikacji usługi App Service i aplikacji funkcji

Istnieje kilka kwestii, które nie są obsługiwane Integracja sieci wirtualnej w tym:

* zainstalowanie dysku
* Integracja z usługą AD 
* NetBios

## <a name="regional-vnet-integration"></a>Integracja regionalnej sieci wirtualnej 

Stosowania integracja z siecią wirtualną z sieciami wirtualnymi w tym samym regionie co aplikacja wymaga użycia delegowanego podsieci z co najmniej 32 adresów w nim. Nie można użyć tej podsieci dla żadnych innych czynności. Połączenia wychodzące z Twojej aplikacji będą nawiązywane z adresów w podsieci delegowanego. Korzystając z tej wersji Integracja z siecią wirtualną, wywołań z adresów w sieci wirtualnej. Przy użyciu adresów w sieci wirtualnej umożliwia aplikacji:

* bezpieczne wywołania usług zabezpieczeń punktu końcowego usługi
* dostęp do zasobów w ramach połączeń usługi ExpressRoute
* Uzyskaj dostęp do zasobów w sieci wirtualnej, nawiązano połączenie
* Uzyskaj dostęp do zasobów przy połączeniach równorzędnej w tym połączenia usługi ExpressRoute

Ta funkcja jest dostępna w wersji zapoznawczej, ale jest obsługiwany dla obciążeń produkcyjnych aplikacji Windows, zastosowanie mają poniższe ograniczenia:

* Możesz tylko połączyć adresy, które należą do zakresu w dokumencie RFC 1918. Są adresów 10.0.0.0/8, 172.16.0.0/12 i 192.168.0.0/16 bloki adresów.
* Nie można osiągnąć zasobów globalnych połączeń komunikacji równorzędnej
* Nie można ustawić trasy na ruch z aplikacji w Twojej sieci wirtualnej
* Ta funkcja jest dostępna tylko z nowszych jednostki skalowania usługi App Service, które obsługują planów usługi App Service PremiumV2.
* Ta funkcja nie może używać planu izolowanego aplikacje, które znajdują się w środowisku usługi App Service
* Ta funkcja wymaga nieużywanej podsieci z co najmniej 32 adresów w sieci wirtualnej usługi Resource Manager.
* Aplikacja i sieć wirtualna musi być w tym samym regionie
* Jeden adres jest używany dla poszczególnych wystąpień planu usługi App Service. Ponieważ rozmiar podsieci nie można zmienić po przypisaniu, należy użyć podsieci, które więcej niż może obejmować rozmiaru skali maksymalnej. / 27 przy użyciu 32 adresów jest zalecany rozmiar, jak który może pomieścić, plan usługi App Service, który jest skalowany do 20 wystąpień.
* Nie można usunąć sieci wirtualnej za pomocą zintegrowanych aplikacji. Należy najpierw usunąć integracji 
* Może mieć tylko jeden integracji regionalnej sieci wirtualnej na plan usługi App Service. Wiele aplikacji, w tym samym planie usługi App Service można użyć tej samej sieci wirtualnej. 

Ta funkcja jest dostępna w wersji zapoznawczej, także dla systemu Linux. Aby użyć funkcji integracji sieci wirtualnej z siecią wirtualną Menedżera zasobów, w tym samym regionie:

1. Przejdź do interfejsu użytkownika sieci, w portalu. Jeśli aplikacja jest w stanie korzystać z nowych funkcji, zostanie wyświetlone opcję, aby dodać sieć wirtualną (wersja zapoznawcza).  

   ![Wybierz Integracja sieci wirtualnej][6]

1. Wybierz **Dodaj sieć wirtualna (wersja zapoznawcza)** .  

1. Wybierz sieć wirtualną usługi Resource Manager, który chcesz zintegrować z, a następnie utwórz nową podsieć lub wybrać już istniejącą pustą podsieć. Integracja zajmuje mniej niż minutę. Podczas integracji aplikacji zostanie ponownie uruchomiona.  Po ukończeniu integracji zobaczysz szczegółowe informacje dotyczące sieci wirtualnej są zintegrowane z usługą i Baner u góry strony, która informuje, że ta funkcja jest dostępna w wersji zapoznawczej.

   ![Wybierz sieć wirtualną i podsieć][7]

Gdy aplikacja jest zintegrowana z siecią wirtualną, będzie używać tego samego serwera DNS, który skonfigurowano sieci wirtualnej. 

Aby odłączyć aplikację z sieci wirtualnej, wybierz **rozłączenia**. To spowoduje ponowne uruchomienie aplikacji sieci web. 

Nowej funkcji integracji sieci wirtualnej umożliwia użycie punktów końcowych usługi.  Punktów końcowych usługi za pomocą aplikacji, należy użyć nowej integracji sieci wirtualnej Aby nawiązać połączenie z wybranej sieci wirtualnej, a następnie skonfigurować punkty końcowe usługi w podsieci używanej do integracji. 

### <a name="how-vnet-integration-works"></a>Jak działa Integracja z siecią wirtualną

Aplikacje w usłudze App Service znajdują się w przypadku ról procesów roboczych. Podstawowa i wyższych, plany cenowe są dedykowane plany hostingu w przypadku, gdy nie ma żadnych innych obciążeń klientów uruchomionych na tych samych pracowników. Integracja sieci wirtualnej działa przez zainstalowanie wirtualnego współpracuje z adresów w podsieci delegowanego. Ponieważ z adres znajduje się w sieci wirtualnej, tak samo, jak będzie maszyny Wirtualnej w sieci wirtualnej ma dostęp do większej liczby elementów w lub za pośrednictwem sieci. Implementacja sieciowych różni się od uruchamiania maszyny Wirtualnej w sieci wirtualnej, a to znaczy Dlaczego niektóre funkcje sieciowe nie są jeszcze dostępne podczas używania tej funkcji.

![Integracja z siecią wirtualną](media/web-sites-integrate-with-vnet/vnet-integration.png)

Po włączeniu Integracja z siecią wirtualną aplikacji nadal wprowadzi połączenia wychodzące z Internetem w taki sam sposób, jak zwykle. Wychodzące adresy, które są wymienione w portalu właściwości aplikacji są nadal adresów używanych przez aplikację. Jakie zmiany dla aplikacji są czy wywołania do punktu końcowego usługi chronione usług lub adresy w dokumencie RFC 1918 przechodzi do sieci wirtualnej. 

Ta funkcja obsługuje tylko jeden wirtualny interfejs dla procesu roboczego.  Jeden wirtualny interfejs na proces roboczy oznacza, że jeden integracji regionalnej sieci wirtualnej na plan usługi App Service. Wszystkie aplikacje, w tym samym planie usługi App Service można użyć tego samego Integracja z siecią wirtualną, ale jeśli potrzebujesz aplikację, aby nawiązać połączenie z siecią wirtualną dodatkowe, konieczne będzie utworzyć inny plan usługi App Service. Interfejs wirtualny używane nie jest zasobem, którego klienci mają bezpośredni dostęp do.

Ze względu na charakter sposób działania tej technologii ruchu, który jest używany z Integracja sieci wirtualnej nie jest wyświetlany w dzienniki przepływów usługi Network Watcher lub sieciowej grupy zabezpieczeń.  

## <a name="gateway-required-vnet-integration"></a>Brama wymagane Integracja sieci wirtualnej 

Brama wymaganych funkcji integracji sieci wirtualnej:

* można połączyć się z sieciami wirtualnymi w dowolnym regionie są to usługi Resource Manager lub klasycznymi sieciami wirtualnymi
* umożliwia aplikacji połączyć się z tylko 1 sieć wirtualną w czasie
* Umożliwia maksymalnie pięć sieci wirtualnych można zintegrować z usługą w planie usługi App Service 
* zezwala na tej samej sieci wirtualnej, który będzie używany przez wiele aplikacji w planie usługi App Service bez wywierania wpływu na całkowita liczba, która może być używany przez plan usługi App Service.  Jeśli masz 6 aplikacji przy użyciu tej samej sieci wirtualnej, w tym samym planie usługi App Service, który liczy się jako 1 sieć wirtualną, używane. 
* wymaga bramy sieci wirtualnej, podczas konfigurowania punktu do lokacji sieci VPN
* Nie jest obsługiwane do użytku z aplikacjami dla systemu Linux
* Obsługuje wartość SLA 99,9%, ze względu na warunki umowy SLA w bramie

Nie obsługuje tej funkcji:

* Uzyskiwanie dostępu do zasobów w usłudze ExpressRoute 
* Uzyskiwanie dostępu do zasobów w obrębie punktów końcowych usługi 

### <a name="getting-started"></a>Wprowadzenie

Oto kilka rzeczy, o których należy pamiętać przed nawiązaniem połączenia z aplikacją sieci web do sieci wirtualnej:

* Docelowa sieć wirtualna musi mieć point-to-site VPN włączone z bramą opartą na trasach, zanim można podłączyć do aplikacji. 
* Sieć wirtualna musi być w tej samej subskrypcji, co Plan(ASP) usługi Twojej aplikacji.
* Aplikacje, które integrują się z siecią wirtualną używają DNS, który jest określony dla tej sieci wirtualnej.

### <a name="set-up-a-gateway-in-your-vnet"></a>Konfigurowanie bramy w sieci wirtualnej ###

Jeśli masz już skonfigurowane przy użyciu adresów punkt lokacja bramy, możesz przejść do konfigurowania integracji sieci wirtualnej wraz z aplikacją.  
Aby utworzyć bramę:

1. [Tworzenie podsieci bramy] [ creategatewaysubnet] w sieci wirtualnej.  

1. [Tworzenie bramy sieci VPN][creategateway]. Wybierz typ sieci VPN opartej na trasach.

1. [Ustaw punkt do lokacji adresów][setp2saddresses]. Jeśli brama nie znajduje się w ramach podstawowej jednostki SKU, następnie IKEV2 musi zostać wyłączone w konfiguracji punkt lokacja i protokół SSTP musi być zaznaczone. Przestrzeń adresowa musi znajdować się w dokumencie RFC 1918 bloki adresów, 10.0.0.0/8, 172.16.0.0/12 i 192.168.0.0/16

Jeśli po prostu tworzenia bramy dla za pomocą integracji sieci wirtualnej usługi aplikacji, nie należy przekazać certyfikat. Tworzenie bramy może potrwać 30 minut. Nie można Integrowanie aplikacji z sieci wirtualnej, dopóki brama jest aprowizowana. 

### <a name="configure-vnet-integration-with-your-app"></a>Konfigurowanie integracji z siecią wirtualną za pomocą aplikacji 

Aby włączyć integrację sieci wirtualnej w aplikacji: 

1. Przejdź do aplikacji w witrynie Azure portal, Otwórz okno Ustawienia aplikacji i wybierz pozycję sieci > Integracja z siecią wirtualną. Strona ASP musi znajdować się w standardowej jednostki SKU lub lepiej korzystać z obu funkcji integracji sieci wirtualnej. 
 ![Integracja z siecią wirtualną interfejsu użytkownika][1]

1. Wybierz **dodać sieci wirtualnej**. 
 ![Dodaj Integracja sieci wirtualnej][2]

1. Wybierz sieć wirtualną. 
  ![Wybierz sieć wirtualną][8]
  
Aplikacja zostanie uruchomiona ponownie po tym ostatnim kroku.  

### <a name="how-the-gateway-required-vnet-integration-feature-works"></a>Działa jak brama wymaganych funkcji integracji sieci wirtualnej

Brama wymaganych funkcji integracji sieci wirtualnej jest oparty na technologii sieci VPN typu punkt lokacja. Technologia point-to-site dostęp sieciowy do hostowania aplikacji tylko maszyny wirtualne. Aplikacje są ograniczone do tylko wysyłać ruch do Internetu za pomocą połączeń hybrydowych lub Integracja z siecią wirtualną. 

![Jak działa Integracja z siecią wirtualną][3]

## <a name="managing-vnet-integration"></a>Zarządzanie integracją sieci wirtualnej
Możliwość nawiązywanie i zakańczanie z siecią wirtualną jest na poziomie aplikacji. Operacje, które mogą wpłynąć na Integracja z siecią wirtualną przez wiele aplikacji znajdują się na poziomie plan usługi App Service. Z poziomu aplikacji > Sieć > portal Integracja sieci wirtualnej można uzyskać szczegółowe informacje w Twojej sieci wirtualnej. Zawiera podobne informacje na poziomie ASP w ASP > Sieć > portalu Integracja sieci wirtualnej, w tym, jakie aplikacje w tym planie usługi App Service używają danej integracji.

 ![Szczegóły sieci wirtualnej][4]

Informacje dostępne w Interfejsie integracji sieci wirtualnej jest taka sama, między aplikacją i portale ASP.

* Nazwa sieci wirtualnej — łącza do sieci wirtualnej interfejsu użytkownika
* Lokalizacja — odzwierciedla lokalizację sieci wirtualnej. Integracja z siecią wirtualną w innej lokalizacji mogą powodować problemy opóźnienia dla aplikacji. 
* Stan certyfikatu — odzwierciedla, czy certyfikaty są zsynchronizowane między planu usługi App Service i sieci wirtualnej.
* Stan bramy — powinien być używany bramy wymagane Integracja z siecią wirtualną, możesz sprawdzić stan bramy.
* Przestrzeń adresowa sieci wirtualnej — pokazuje przestrzeń adresów IP dla sieci wirtualnej. 
* Przestrzeń adresowa punkt lokacja — pokazuje wskaż przestrzeń adresów IP dla lokacji sieci wirtualnej. Podczas wykonywania wywołań do sieci wirtualnej podczas korzystania z funkcji wymagane bramy, adres nadawcy Twojej aplikacji będą jednego z tych adresów. 
* Przestrzeń adresowa lokacja lokacja — można użyć sieci VPN typu lokacja lokacja do łączenia z sieci wirtualnej do zasobów w środowisku lokalnym lub innymi sieciami wirtualnymi. Zakresy adresów IP zdefiniowane za pomocą tego połączenia sieci VPN są wyświetlane w tym miejscu.
* Serwery DNS — pokazuje, serwery DNS są skonfigurowane przy użyciu sieci wirtualnej.
* Adresy IP kierowane do sieci wirtualnej — pokazuje kierowane bloki adresów używany do dysku ruchu w sieci wirtualnej 

Jedyną operacją, które można wykonać w widoku aplikacji Integracja z siecią wirtualną jest rozłączenie aplikacji z jest aktualnie połączony z siecią wirtualną. Aby odłączyć aplikację z sieci wirtualnej, wybierz **rozłączenia**. Aplikacja zostanie uruchomiona ponownie po rozłączeniu z sieci wirtualnej. Odłączanie nie powoduje zmiany sieci wirtualnej. Podsieci lub bramy nie zostanie usunięty. Jeśli chcesz, następnie można usunąć sieci wirtualnej, musisz najpierw rozłączenie aplikacji z siecią wirtualną i Usuń zasoby w nim, takie jak bramy. 

Aby przejść do ASP integracji sieci wirtualnej z interfejsu użytkownika, Otwórz interfejs użytkownika ASP, a następnie wybierz **sieć**.  W obszarze Integracja z siecią wirtualną, wybierz **kliknij tutaj, aby skonfigurować** otworzyć Interfejs użytkownika stan funkcji sieci.

![Informacje o integracji sieci wirtualnej ASP][5]

ASP integracji sieci wirtualnej z interfejsu użytkownika pokazuje wszystkie sieci wirtualnych, które są używane przez aplikacje w strona ASP. Aby wyświetlić szczegóły dotyczące każdej sieci wirtualnej, kliknij sieć wirtualną Cię interesuje. Istnieją dwie akcje, które można wykonać w tym miejscu.

* **Synchronizuj sieć**. Operacja sieci synchronizacji jest tylko w przypadku bramy zależnej funkcji integracji sieci wirtualnej. Wykonywanie operacji synchronizacji sieci zapewnia, że certyfikaty i informacje o sieci są zsynchronizowane. W przypadku dodania lub zmiany w systemie DNS w sieci wirtualnej, należy wykonać **Synchronizuj sieć** operacji. Ta operacja spowoduje ponowne uruchomienie wszystkich aplikacji za pomocą tej sieci wirtualnej.
* **Dodawanie tras** dodawanie tras regulują ruch wychodzący w sieci wirtualnej.

**Routing** tras, które są zdefiniowane w Twojej sieci wirtualnej są używane do kierowania ruchem danych w sieci wirtualnej z poziomu aplikacji. Jeśli potrzebujesz wysłać dodatkowe ruch wychodzący w sieci wirtualnej, można dodać tych bloków adresów. Ta funkcja działa tylko w przypadku bramy wymagana jest integracja z siecią wirtualną.

**Certyfikaty** żądanie włączona integracja z siecią wirtualną, brama jest wymagana wymiany certyfikatów do zabezpieczenia połączenia. Wraz z certyfikatów są konfiguracji serwera DNS, trasy i innych podobnych zadań, które opisują sieci.
Jeśli certyfikaty lub informacje o sieci zostało zmienione, musisz kliknąć przycisk "Sync sieci". Po kliknięciu przycisku "Synchronizuj sieć" można spowodować niedostępność łączność między aplikacją użytkownika a siecią wirtualną. Gdy aplikacja nie jest ponownie uruchamiany, utrata łączności może spowodować, witryny nie będzie działał prawidłowo. 

## <a name="accessing-on-premises-resources"></a>Uzyskiwanie dostępu do zasobów lokalnych
Aplikacje mogą uzyskiwać dostęp w zasoby lokalne dzięki integracji z sieciami wirtualnymi, który ma połączeń lokacja lokacja. Jeśli używasz bramy wymagane Integracja sieci wirtualnej, musisz zaktualizować trasy bramy sieci VPN w środowisku lokalnym za pomocą usługi bloki adresów punkt lokacja. Po pierwszym skonfigurowaniu sieci VPN typu lokacja lokacja skrypty używane do jego konfiguracji należy skonfigurować trasy prawidłowo. Jeśli dodasz adresy point-to-site, po utworzeniu sieci VPN lokacja lokacja, musisz ręcznie zaktualizować trasy. Szczegółowe informacje na temat jak to zrobić, różnią się na bramę i nie zostały opisane w tym miejscu. Nie może mieć skonfigurowany za pomocą połączenia sieci VPN typu lokacja lokacja protokołu BGP.

Brak konieczności dodatkowej konfiguracji dla funkcji integracji sieci wirtualnej regionalnych nawiązać połączenie za pośrednictwem sieci wirtualnej i do serwera lokalnego. Po prostu musisz połączyć sieć wirtualną do serwera lokalnego przy użyciu usługi ExpressRoute lub sieci VPN lokacja lokacja. 

> [!NOTE]
> Brama wymaganych funkcji integracji sieci wirtualnej nie umożliwia integracji aplikacji z siecią wirtualną, która ma bramę usługi ExpressRoute. Nawet jeśli brama usługi ExpressRoute jest w [tryb współistnienie] [ VPNERCoex] Integracja sieci wirtualnej nie działa. Jeśli potrzebujesz dostępu do zasobów za pośrednictwem połączenia usługi ExpressRoute, możesz użyć regionalne funkcji integracji sieci wirtualnej lub moduł [środowiska App Service Environment][ASE], która działa w sieci wirtualnej. 
> 
> 

## <a name="peering"></a>Komunikacja równorzędna
Jeśli używasz komunikacji równorzędnej z integracji regionalnej sieci wirtualnej jest konieczne konieczności dodatkowego konfigurowania. 

Jeśli używasz bramy wymagane Integracja z siecią wirtualną za pomocą komunikacji równorzędnej, musisz skonfigurować kilka dodatkowych elementów. Aby skonfigurować komunikację równorzędną, aby pracować z Twoją aplikacją:

1. Dodaj połączenie komunikacji równorzędnej w sieci wirtualnej aplikacja łączy się z firmą. Podczas dodawania połączenia komunikacji równorzędnej, Włącz **zezwolić na dostęp do sieci wirtualnej** i sprawdź **Zezwalaj na przekazywany ruch** i **Zezwalaj na tranzyt bramy**.
1. Dodaj połączenie komunikacji równorzędnej na sieć wirtualną, która jest jest połączona z siecią wirtualną nawiązano połączenie. Podczas dodawania połączenia komunikacji równorzędnej w lokalizacji docelowej sieci wirtualnej, Włącz **zezwolić na dostęp do sieci wirtualnej** i sprawdź **Zezwalaj na przekazywany ruch** i **Zezwalaj bram zdalnych**.
1. Przejdź do planu usługi App Service > Sieć > UI integracji sieci wirtualnej w portalu.  Wybierz sieć wirtualną, łączy się z aplikacją. W sekcji routingu Dodaj zakres adresów sieci wirtualnej, która jest połączona z siecią wirtualną, Twoja aplikacja jest połączona.  


## <a name="pricing-details"></a>Szczegóły cennika
Regionalne funkcji integracji sieci wirtualnej ma bez dodatkowych opłat do użycia poza ASP cen opłaty za warstwy.

Istnieją trzy powiązane opłaty do korzystania z funkcji integracji sieci wirtualnej brama jest wymagana:

* ASP cen warstwy opłaty — aplikacje muszą należeć standardowa, Premium lub PremiumV2 App Service Plan. Te koszty tutaj można zobaczyć więcej szczegółów: [Cennik usługi App Service][ASPricing]. 
* Koszty transferu danych - miejsca jest opłaty za wyjście danych, nawet jeśli sieć wirtualna jest w tym samym centrum danych. Te opłaty są opisane w [Szczegóły ceny transferu danych][DataPricing]. 
* Bramy sieci VPN kosztów — tam jest koszt do bramy sieci wirtualnej, która jest wymagana dla sieci VPN typu punkt lokacja. Szczegółowe informacje znajdują się na [cennik bramy sieci VPN] [ VNETPricing] strony.


## <a name="troubleshooting"></a>Rozwiązywanie problemów
Gdy ta funkcja jest łatwe do skonfigurowania, który nie oznacza, że środowisko będzie bezpłatne problem. Powinien wystąpić problemy podczas dostępu do żądanego punktu końcowego usługi są niektóre narzędzia używane do testowania łączności z aplikacji konsoli. Istnieją dwie konsole, które są dostępne. Jeden to konsola Kudu, a drugi to konsoli w witrynie Azure portal. Aby uzyskać dostęp do konsoli Kudu ze swojej aplikacji, przejdź do pozycji Narzędzia -> Kudu. To jest taka sama, jak zamierza [sitename]. scm.azurewebsites.net. Gdy które otwiera, przejdź do karty konsoli debugowania. Można pobrać z konsoli platformy Azure hostowanej portalu, a następnie z poziomu aplikacji przejdź do pozycji Narzędzia -> konsoli. 

#### <a name="tools"></a>Narzędzia
Narzędzia **ping**, **nslookup** i **tracert** nie będą działać przy użyciu konsoli z powodu ograniczeń zabezpieczeń. Aby wypełnić void, dodano dwa osobne narzędzia. Aby można było przetestować funkcje DNS, dodaliśmy narzędzia o nazwie nameresolver.exe. Składnia jest następująca:

    nameresolver.exe hostname [optional: DNS Server]

Możesz użyć **nameresolver** do sprawdzania nazwy hostów, która zależy od aplikacji. W ten sposób można sprawdzić, jeśli cokolwiek źle skonfigurowany serwer DNS, lub może nie mają dostępu do serwera DNS. Serwer DNS, który Twoja aplikacja będzie używać w konsoli, analizując zmienne środowiskowe WEBSITE_DNS_SERVER i WEBSITE_DNS_ALT_SERVER jest widoczny.

Narzędzie dalej pozwala do testowania łączności kombinację hosta i portu TCP. To narzędzie jest nazywany **tcpping** i składnia jest następująca:

    tcpping.exe hostname [optional: port]

**Tcpping** narzędzie informuje, jeśli możesz docierać do konkretnego hosta i portu. Można widoczna sukces tylko jeśli: istnieje aplikacja nasłuchuje na hoście i porcie kombinacji i ma dostęp do sieci z poziomu aplikacji na określonym hoście i porcie.

#### <a name="debugging-access-to-vnet-hosted-resources"></a>Debugowanie dostępu do sieci wirtualnej hostowanych zasobów
Istnieje kilka kwestii, które mogą uniemożliwić aplikacji docieranie do konkretnego hosta i portu. W większości przypadków jest to jeden z trzech zdarzeń:

* **Zapora jest w ten sposób.** Jeśli masz zaporę w taki sposób, spowoduje osiągnięcie limitu czasu protokołu TCP. Limit czasu protokołu TCP w tym przypadku jest 21 sekund. Użyj **tcpping** narzędzia do testowania łączności. Limity czasu protokołu TCP można ze względu na wiele elementów poza zaporami, ale od tego. 
* **DNS jest niedostępny.** Limit czasu DNS to trzy sekundy na serwerze DNS. Jeśli masz dwa serwery DNS, limit czasu jest 6 w ciągu kilku sekund. Użyj nameresolver, aby zobaczyć, jeśli serwer DNS działa. Pamiętaj, że nie można użyć nslookup, jak które nie używają DNS sieci wirtualnej jest konfigurowana. Jeśli jest niedostępny, masz zaporę lub sieciowej grupy zabezpieczeń blokowanie dostępu do DNS lub może nie działać.

Jeśli te elementy nie odpowiedź problemów, Szukaj pierwszego dla elementów, takich jak: 

**Integracja regionalnej sieci wirtualnej**
* lokalizacji docelowej nie jest adresem RFC 1918
* czy istnieje sieciowa grupa zabezpieczeń blokowania ruchu wychodzącego z podsieci integracji
* Jeśli dla usługi ExpressRoute lub sieci VPN, bramy sieci lokalnej skonfigurowano do kierowania ruchu, tworzenie kopii zapasowej na platformie Azure? Jeśli przekroczysz punktów końcowych w sieci wirtualnej, ale nie w środowisku lokalnym, jest to sprawdzić.

**Brama wymagane Integracja sieci wirtualnej**
* jest zakresem adresów punkt lokacja, w zakresach RFC 1918 (10.0.0.0-10.255.255.255 / 172.16.0.0-172.31.255.255 / 192.168.0.0-192.168.255.255)?
* Czy brama jest wyświetlany jako znajdujące się w portalu? Jeśli brama nie działa, następnie połącz kopii zapasowej.
* Czy certyfikaty są wyświetlane jako znajdujące się w synchronizacji lub istnieje podejrzenie, że zmieniono konfigurację sieci?  Jeśli podejrzewasz, że wprowadzono zmiany wprowadzone w konfiguracji sieci wirtualnej, który nie został zsynchronizowany z usługi ASP certyfikaty nie są zsynchronizowane, a następnie naciśnij klawisz "Synchronizuj sieć".
* Jeśli dla usługi ExpressRoute lub sieci VPN, bramy sieci lokalnej skonfigurowano do kierowania ruchu, tworzenie kopii zapasowej na platformie Azure? Jeśli przekroczysz punktów końcowych w sieci wirtualnej, ale nie w środowisku lokalnym, jest to sprawdzić.

Problemy z siecią debugowanie jest trudne, ponieważ istnieje nie można zobaczyć, co blokuje dostęp do kombinację określonego hosta: port. Przyczyny są między innymi:

* Możesz mieć zapory na hoście uniemożliwia dostęp do portu aplikacji z punktu do lokacji, zakres adresów IP. Często przekroczenie granic podsieci wymaga dostępu publicznego.
* dostęp do hosta docelowego nie działa
* Aplikacja nie działa
* ma nieprawidłowy adres IP lub nazwa hosta
* aplikacja nasłuchuje na innym porcie, niż oczekiwano. Za pomocą "netstat - aon" na hoście punktu końcowego może odnosić się Twój identyfikator procesu przy użyciu portu nasłuchiwania. 
* grupy zabezpieczeń sieci są skonfigurowane w taki sposób, że mogą uniemożliwić dostęp do aplikacji hosta i portu, z punktu do lokacji, zakres adresów IP

Należy pamiętać, że nie wiesz, jakiego adresu, Twoja aplikacja będzie faktycznie używać. Może to być dowolny adres w integracji podsieci lub point-to-site zakres adresów, więc należy zezwolić na dostęp z cały zakres adresów. 

Debugowanie dodatkowe kroki są następujące:

* Łączenie z maszyną Wirtualną w sieci wirtualnej i próbują uzyskać dostęp z Twojego zasobu: port hosta w tym miejscu. Aby sprawdzić, czy dostęp do protokołu TCP, użyj polecenia PowerShell **test-netconnection**. Składnia jest następująca:

      test-netconnection hostname [optional: -Port]

* wyświetlenie aplikacji na maszynę Wirtualną i testowanie dostępu do hosta i portu, z poziomu konsoli z aplikacji przy użyciu **tcpping**

#### <a name="on-premises-resources"></a>Zasoby lokalne ####

Jeśli aplikacja nie może połączyć się z zasobem w środowisku lokalnym, sprawdź, w przypadku zasobów możesz się połączyć z siecią wirtualną. Użyj **test-netconnection** polecenia programu PowerShell, aby sprawdzić, czy dostęp do protokołu TCP. Jeśli maszyna wirtualna nie może połączyć się z zasobami lokalnymi, połączenie sieci VPN lub usługi ExpressRoute może nie być prawidłowo skonfigurowane.

Jeśli sieć wirtualna hostowana maszyna wirtualna może nawiązać połączenie z lokalnym systemem, ale aplikacja nie może, a następnie przyczyną jest prawdopodobnie jednym z następujących powodów:

* trasy nie są skonfigurowane dla podsieci środowiska lub punktu do lokacji zakresów adresów w bramie lokalnej
* sieciowe grupy zabezpieczeń blokują dostęp dla zakresu adresów IP Point-to-Site
* zapory w środowisku lokalnym blokuje ruch z zakresowi adresów IP Point-to-Site
* próbujesz osiągnąć adres — RFC 1918, za pomocą regionalne funkcji integracji sieci wirtualnej


## <a name="powershell-automation"></a>Automatyzacja programu PowerShell

Usługa App Service można zintegrować z usługą Azure Virtual Network przy użyciu programu PowerShell. Gotowe do uruchomienia skryptu, zobacz [Połącz aplikację w usłudze Azure App Service do usługi Azure Virtual Network](https://gallery.technet.microsoft.com/scriptcenter/Connect-an-app-in-Azure-ab7527e3).


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
