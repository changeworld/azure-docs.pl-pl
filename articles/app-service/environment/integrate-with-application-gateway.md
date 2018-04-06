---
title: Integrację środowiska usługi aplikacji ILB z bramy aplikacji Azure
description: Przewodnik dotyczący sposobu integracji aplikacji w środowisku usługi aplikacji ILB z bramy aplikacji
services: app-service
documentationcenter: na
author: ccompy
manager: stefsch
ms.assetid: a6a74f17-bb57-40dd-8113-a20b50ba3050
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/03/2018
ms.author: ccompy
ms.openlocfilehash: 31aea1d19ed6da856bb5fc634a919819513cb6b2
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/05/2018
---
# <a name="integrate-your-ilb-app-service-environment-with-the-azure-application-gateway"></a>Integrację środowiska usługi aplikacji ILB z bramy aplikacji Azure #

[Środowiska usługi aplikacji](./intro.md) wdrożenie usługi Azure App Service w podsieci sieci wirtualnej platformy Azure klienta. Może on zostać wdrożony z punktu końcowego publicznej lub prywatnej w celu uzyskania dostępu do aplikacji. Wdrożenie środowiska usługi aplikacji z punktem końcowym prywatne (to znaczy wewnętrznego modułu równoważenia obciążenia) jest nazywany ILB środowiska usługi aplikacji.  

Aplikacja sieci Web zapór pomagają w zabezpieczaniu aplikacji sieci web, sprawdzając ruch przychodzący sieci web do blokowania przekazywania złośliwego oprogramowania iniekcji skryptów krzyżowych, SQL i aplikacji przed atakami DDoS i inne ataki. Sprawdza również odpowiedzi z serwerów sieci web zaplecza dla zapobiegania utracie danych (DLP). Urządzenia zapory aplikacji sieci Web można uzyskać z poziomu portalu Azure marketplace lub użyć [Azure Application Gateway][appgw].

Brama aplikacji w usłudze Azure to urządzenie wirtualne, które oferuje usługa równoważenia obciążenia warstwy 7, odciążanie protokołu SSL i ochrony (WAF) zapory aplikacji sieci web. Można go nasłuchiwać na publiczny adres i trasy ruch pakietów IP punktu końcowego aplikacji. Poniższe informacje zawierają opis sposobu integracji bramy aplikacji skonfigurowanej zapory aplikacji sieci Web z aplikacji w środowisku usługi aplikacji ILB.  

Integracja bramy aplikacji z ILB środowiska usługi aplikacji znajduje się na poziomie aplikacji. Podczas konfigurowania bramy aplikacji ze środowiska usługi aplikacji ILB, jest to zrobić dla określonych aplikacji w środowisku usługi aplikacji ILB. Ta metoda umożliwia hosting bezpiecznych wielodostępnych aplikacji w jednym środowisku usługi aplikacji ILB.  

![Wskazuje do aplikacji w środowisku usługi aplikacji ILB bramy aplikacji][1]

Korzystając z tego przewodnika, wykonasz następujące czynności:

* Utwórz bramę aplikacji Azure.
* Konfigurowanie bramy aplikacji, aby wskazywała aplikację w środowisku usługi aplikacji ILB.
* Skonfiguruj aplikację, aby uwzględnić nazwy domeny niestandardowej.
* Edytuj publicznej nazwy hosta DNS wskazujący bramy aplikacji.

## <a name="prerequisites"></a>Wymagania wstępne

Aby zintegrować bramy aplikacji z środowiska usługi aplikacji ILB, potrzebne są:

* Środowisko usługi aplikacji ILB.
* Aplikacja uruchomiona w środowisku usługi aplikacji ILB.
* Nazwy domeny routingu internetowej, która ma być używany z aplikacji w środowisku usługi aplikacji ILB.
* Adres ILB, który używa środowiska usługi aplikacji ILB. Te informacje są w portalu środowiska usługi aplikacji w obszarze **ustawienia** > **adresów IP**:

    ![Przykład listę adresów IP używanych przez ILB środowiska usługi aplikacji][9]
    
* Publiczna nazwa DNS używanej później do punktu z bramą aplikacji. 

Aby uzyskać więcej informacji na temat tworzenia środowiska usługi aplikacji ILB, zobacz [tworzenie i używanie środowiska usługi aplikacji ILB][ilbase].

W tym artykule założono, że brama aplikacji w tej samej sieci wirtualnej platformy Azure wdrożonym środowiska usługi aplikacji. Przed przystąpieniem do tworzenia bramy aplikacji, wybierz lub Utwórz podsieć, która będzie używany do obsługi bramy. 

Należy używać podsieci, która jest nie jeden GatewaySubnet nazwanego. Jeśli brama aplikacji w GatewaySubnet, będziesz opcję późniejszego utworzenia bramy sieci wirtualnej. 

Ponadto nie można umieścić bramy w podsieci, która używa środowiska usługi aplikacji ILB. Środowiska usługi aplikacji jest jedynym elementem, który może być w tej podsieci.

## <a name="configuration-steps"></a>Kroki konfiguracji ##

1. W portalu Azure, przejdź do **nowy** > **sieci** > **brama aplikacji w**.

2. W **podstawy** obszar:

   a. Aby uzyskać **nazwa**, wprowadź nazwę bramy aplikacji.

   b. Aby uzyskać **warstwy**, wybierz pozycję **WAF**.

   c. Aby uzyskać **subskrypcji**, wybrać tej samej subskrypcji, która używa sieci wirtualnej środowiska usługi aplikacji.

   d. Aby uzyskać **grupy zasobów**, Utwórz lub wybierz grupę zasobów.

   e. Aby uzyskać **lokalizacji**, wybierz lokalizację, w sieci wirtualnej środowiska usługi aplikacji.

   ![Nowej podstawy tworzenia bramy aplikacji][2]

3. W **ustawienia** obszar:

   a. Aby uzyskać **sieci wirtualnej**, wybierz sieć wirtualną środowiska usługi aplikacji.

   b. Aby uzyskać **podsieci**, wybierz podsieć, w którym musi zostać wdrożony bramy aplikacji. Nie używaj GatewaySubnet, ponieważ uniemożliwi tworzenie bramy sieci VPN.

   c. Aby uzyskać **typ adresu IP**, wybierz pozycję **publicznego**.

   d. Aby uzyskać **publicznego adresu IP**, wybierz publiczny adres IP. Jeśli nie istnieje, utwórz je.

   e. Aby uzyskać **protokołu**, wybierz pozycję **HTTP** lub **HTTPS**. W przypadku konfigurowania protokołu HTTPS, należy podać certyfikat PFX.

   f. Aby uzyskać **zapory aplikacji sieci Web**, można włączyć zapory, a także ustawić dla dowolnego **wykrywania** lub **zapobiegania** zgodnie z własnymi potrzebami.

   ![Nowe ustawienia tworzenia bramy aplikacji][3]
    
4. W **Podsumowanie** sekcji, przejrzyj ustawienia i wybierz **OK**. Bramy aplikacji może zająć nieco więcej niż 30 minut, aby zakończyć instalację.  

5. Po zakończeniu instalacji bramy aplikacji przejdź do portalu Application Gateway. Wybierz **puli zaplecza**. Dodaj adres ILB dla środowiska usługi aplikacji ILB.

   ![Konfigurowanie puli wewnętrznej bazy danych][4]

6. Po zakończeniu procesu konfigurowania pulę zaplecza, wybierz **sondy kondycji**. Utwórz badanie kondycji dla nazwy domeny, który ma być używany dla aplikacji. 

   ![Konfigurowanie sond kondycji][5]
    
7. Po zakończeniu procesu konfigurowania sieci sondy kondycji wybierz **ustawienia HTTP**. Edytuj istniejące ustawienia, wybierz **sondowania niestandardowych użyj**i wybierz sondowania, który został skonfigurowany.

   ![Skonfiguruj ustawienia protokołu HTTP][6]
    
8. Przejdź na bramie aplikacji **omówienie** sekcji, a następnie skopiuj publiczny adres IP, który używa bramy aplikacji. Ustaw ten adres IP jako rekord A dla nazwy domeny aplikacji lub użyj nazwy DNS dla tego adresu w rekord CNAME. Możliwe jest łatwiejsze wybierz publiczny adres IP i skopiuj go z publicznego adresu IP interfejsu użytkownika, a nie skopiowania link w bramie aplikacji **omówienie** sekcji. 

   ![Portal bramy aplikacji][7]

9. Ustaw nazwę domeny niestandardowej dla aplikacji w środowisku usługi aplikacji ILB. Przejdź do aplikacji w portalu, a w obszarze **ustawienia**, wybierz pozycję **domen niestandardowych**.

   ![Ustaw niestandardową nazwę domeny w aplikacji][8]

Brak informacji na temat ustawiania niestandardowych nazw domen dla aplikacji sieci web w artykule [ustawienie niestandardowych nazw domen dla aplikacji sieci web][custom-domain]. Ale dla aplikacji w środowisku usługi aplikacji ILB, nie ma żadnych Weryfikacja nazwy domeny. Ponieważ jesteś właścicielem DNS, który zarządza punkty końcowe aplikacji, możesz umieścić dowolne w nim. W takim przypadku dodawanej nazwy domeny niestandardowej musi być w systemie DNS, ale można skonfigurować za pomocą aplikacji nadal jest konieczne. 

Po zakończeniu instalacji i pozwala uzyskać krótkim czasie Propaguj zmiany DNS, można uzyskać dostępu do aplikacji, przy użyciu nazwy domeny niestandardowej, która zostanie utworzona. 


<!--IMAGES-->
[1]: ./media/integrate-with-application-gateway/appgw-highlevel.png
[2]: ./media/integrate-with-application-gateway/appgw-createbasics.png
[3]: ./media/integrate-with-application-gateway/appgw-createsettings.png
[4]: ./media/integrate-with-application-gateway/appgw-backendpool.png
[5]: ./media/integrate-with-application-gateway/appgw-healthprobe.png
[6]: ./media/integrate-with-application-gateway/appgw-httpsettings.png
[7]: ./media/integrate-with-application-gateway/appgw-publicip.png
[8]: ./media/integrate-with-application-gateway/appgw-customdomainname.png
[9]: ./media/integrate-with-application-gateway/appgw-iplist.png

<!--LINKS-->
[appgw]: http://docs.microsoft.com/azure/application-gateway/application-gateway-introduction
[custom-domain]: ../app-service-web-tutorial-custom-domain.md
[ilbase]: ./create-ilb-ase.md
