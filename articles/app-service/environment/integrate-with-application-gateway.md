---
title: Integracja środowiska App Service wewnętrznego modułu równoważenia obciążenia z usługą Application Gateway — Azure
description: Przewodnik dotyczący sposobu integrowania aplikacji w środowiska App Service wewnętrznego modułu równoważenia obciążenia przy użyciu bramy aplikacji
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
ms.custom: seodec18
ms.openlocfilehash: ea46b5e57e4e508a3311de8633ae61d346b574eb
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60764928"
---
# <a name="integrate-your-ilb-app-service-environment-with-the-azure-application-gateway"></a>Integrację środowiska App Service wewnętrznego modułu równoważenia obciążenia za pomocą usługi Azure Application Gateway #

[Środowiska App Service Environment](./intro.md) to wdrożenie usługi Azure App Service w podsieci sieci wirtualnej platformy Azure klienta. Można je było wdrożyć przy użyciu punktu końcowego publicznych lub prywatnych w celu uzyskania dostępu do aplikacji. Wdrożenie usługi App Service Environment przy użyciu prywatnych punktów końcowych (czyli wewnętrznego modułu równoważenia obciążenia) nazywa się środowisko App Service wewnętrznego modułu równoważenia obciążenia.  

Aplikacja sieci Web zapór pomagają w zabezpieczaniu aplikacji internetowych, sprawdzając przychodzący ruch internetowy do blokowania przekazywania złośliwego oprogramowania wstrzyknięć kodu, Cross-Site Scripting, SQL i ataki DDOS na aplikacje i inne ataki. Sprawdza również odpowiedzi z serwerów sieci web zaplecza dla ochrony przed utratą danych (DLP). Urządzenia zapory aplikacji sieci Web można pobrać z witryny Azure marketplace lub użyć [usługi Azure Application Gateway][appgw].

Azure Application Gateway to urządzenie wirtualne, które zapewnia równoważenie obciążenia warstwy 7, odciążanie protokołu SSL i ochrona umożliwiającymi zainstalowanie zapory aplikacji sieci web. Może nasłuchiwać na publicznych adresów IP adres i kierować ruchem do punktu końcowego aplikacji. Poniższe informacje zawierają opis sposobu integrowania bramy aplikacji skonfigurowanej zapory aplikacji sieci Web z aplikacji w środowisko App Service wewnętrznego modułu równoważenia obciążenia.  

Integracja usługi application gateway z wewnętrznym modułem równoważenia obciążenia usługi App Service Environment jest na poziomie aplikacji. Po skonfigurowaniu bramy aplikacji za pomocą środowiska App Service wewnętrznego modułu równoważenia obciążenia wykonujesz je dla określonych aplikacji w środowiska App Service wewnętrznego modułu równoważenia obciążenia. Ta technika umożliwia hostowanie bezpieczne aplikacje wielodostępne w jednym środowisku usługi App wewnętrznego modułu równoważenia obciążenia.  

![Usługa Application gateway wskazujący aplikację na środowisko App Service wewnętrznego modułu równoważenia obciążenia][1]

Korzystając z tego przewodnika, wykonasz następujące czynności:

* Tworzenie bramy aplikacji platformy Azure.
* Konfigurowanie bramy aplikacji, aby wskazywał aplikację w środowiska App Service wewnętrznego modułu równoważenia obciążenia.
* Skonfiguruj aplikację, aby uwzględnić niestandardowej nazwy domeny.
* Edytuj publicznej nazwy DNS hosta, który wskazuje na Twojej bramy application gateway.

## <a name="prerequisites"></a>Wymagania wstępne

Aby zintegrować Twojej bramy Application Gateway, za pomocą środowiska App Service wewnętrznego modułu równoważenia obciążenia, potrzebne są:

* Środowiska usługi App Service wewnętrznego modułu równoważenia obciążenia.
* Aplikacja uruchomiona w środowisku App Service Environment wewnętrznego modułu równoważenia obciążenia.
* Nazwa domeny Routing internet, która ma być używany razem z aplikacją w środowisku App Service Environment wewnętrznego modułu równoważenia obciążenia.
* Adres wewnętrznego modułu równoważenia obciążenia, który używa środowiska App Service wewnętrznego modułu równoważenia obciążenia. Te informacje są w portalu usługi App Service Environment w obszarze **ustawienia** > **adresów IP**:

    ![Przykład listy adresów IP używanych przez środowisko usługi App Service wewnętrznego modułu równoważenia obciążenia][9]
    
* Publiczna nazwa DNS, który jest używany później, aby wskazywał Twojej bramy Application Gateway. 

Aby uzyskać więcej informacji na temat tworzenia środowiska App Service Environment wewnętrznego modułu równoważenia obciążenia, zobacz [tworzenie i używanie wewnętrznego modułu równoważenia obciążenia App Service Environment][ilbase].

W tym artykule założono, że bramy aplikacji w tej samej sieci wirtualnej platformy Azure wdrożonym środowiska App Service Environment. Przed przystąpieniem do tworzenia bramy aplikacji, wybierz lub Utwórz podsieć używaną do obsługi bramy. 

Należy użyć podsieci, która jest nie jeden o nazwie GatewaySubnet. Application Gateway jest umieszczenie w podsieci GatewaySubnet, będzie można później utworzyć bramę sieci wirtualnej. 

Ponadto nie można umieścić bramę w podsieci, która używa środowiska App Service wewnętrznego modułu równoważenia obciążenia. Środowisko usługi App Service jest jedynym elementem, który może znajdować się w tej podsieci.

## <a name="configuration-steps"></a>Kroki konfiguracji ##

1. W witrynie Azure portal przejdź do **New** > **sieci** > **Application Gateway**.

2. W **podstawy** obszaru:

   a. Aby uzyskać **nazwa**, wprowadź nazwę usługi Application Gateway.

   b. Aby uzyskać **warstwy**, wybierz opcję **zapory aplikacji sieci Web**.

   c. Aby uzyskać **subskrypcji**, wybrać tej samej subskrypcji, która korzysta z sieci wirtualnej środowiska App Service Environment.

   d. Aby uzyskać **grupy zasobów**, Utwórz lub wybierz grupę zasobów.

   e. Aby uzyskać **lokalizacji**, wybierz lokalizację sieci wirtualnej środowiska App Service Environment.

   ![Nowej podstawy tworzenia bramy aplikacji][2]

3. W **ustawienia** obszaru:

   a. Aby uzyskać **sieć wirtualna**, wybierz sieć wirtualną w środowisku usługi App Service.

   b. Aby uzyskać **podsieci**, wybierz podsieć, w którym należy wdrożyć bramy aplikacji. Nie używaj GatewaySubnet, ponieważ uniemożliwi tworzenie bramy sieci VPN.

   c. Aby uzyskać **typ adresu IP**, wybierz opcję **publicznych**.

   d. Aby uzyskać **publiczny adres IP**, wybierz publiczny adres IP. Jeśli nie masz, utwórz ją teraz.

   e. Aby uzyskać **protokołu**, wybierz opcję **HTTP** lub **HTTPS**. Jeśli konfigurujesz dla protokołu HTTPS, należy podać certyfikat PFX.

   f. Aby uzyskać **zapory aplikacji sieci Web**, można włączyć zapory i również ustawić dla dowolnego **wykrywania** lub **zapobiegania** zgodnie z potrzebami.

   ![Nowe ustawienia tworzenia bramy aplikacji][3]
    
4. W **Podsumowanie** , przejrzyj ustawienia i wybierz pozycję **OK**. Twoja brama Application Gateway może potrwać trochę więcej niż 30 minut, aby zakończyć instalację.  

5. Po ukończeniu instalacji bramy Application Gateway, przejdź do portalu usługi Application Gateway. Wybierz **puli zaplecza**. Dodaj adres wewnętrznego modułu równoważenia obciążenia dla środowiska App Service wewnętrznego modułu równoważenia obciążenia.

   ![Konfigurowanie puli zaplecza][4]

6. Po zakończeniu procesu konfiguracji puli zaplecza, wybierz **sondy kondycji**. Utwórz sondę kondycji dla nazwy domeny, którego chcesz użyć dla aplikacji. 

   ![Konfigurowanie sond kondycji][5]
    
7. Po zakończeniu procesu konfiguracji usługi sond kondycji wybierz **ustawienia HTTP**. Edytuj istniejące ustawienia, wybierz **Użyj niestandardowe sondy**, a następnie wybierz sondowania, który został skonfigurowany.

   ![Skonfiguruj ustawienia protokołu HTTP][6]
    
8. Przejdź do usługi Application Gateway **Przegląd** sekcji, a następnie skopiuj publiczny adres IP, który używa bramy Application Gateway. Ustaw ten adres IP jako rekord A dla nazwy domeny aplikacji lub użyj nazwy DNS dla tego adresu do rekordu CNAME. Ułatwia to wybierz publiczny adres IP i skopiuj go z poziomu interfejsu użytkownika publicznego adresu IP zamiast skopiuj go przy użyciu linku w usłudze Application Gateway **Przegląd** sekcji. 

   ![Portal usługi Application Gateway][7]

9. Ustaw niestandardową nazwę domeny dla swojej aplikacji w środowiska App Service wewnętrznego modułu równoważenia obciążenia. Przejdź do aplikacji w portalu i w obszarze **ustawienia**, wybierz opcję **domen niestandardowych**.

   ![Ustaw nazwę domeny niestandardowej na aplikację][8]

Brak informacji na temat ustawiania niestandardowych nazw domen dla aplikacji sieci web w artykule [Ustawianie niestandardowych nazw domen dla aplikacji sieci web][custom-domain]. Jednak dla aplikacji w środowisko App Service wewnętrznego modułu równoważenia obciążenia, nie ma żadnych weryfikacji od nazwy domeny. Ponieważ jesteś właścicielem DNS, który zarządza punktów końcowych aplikacji, możesz umieścić dowolne tam. Niestandardowa nazwa domeny, w tym przypadku dodasz nie musi znajdować się w systemie DNS, ale nadal musi on mieć skonfigurowaną aplikację. 

Po zakończeniu instalacji i dopuszczono krótkim czasie zmiany Propagacja DNS, mają dostęp do aplikacji przy użyciu nazwy domeny niestandardowej, który został utworzony. 


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
[appgw]: https://docs.microsoft.com/azure/application-gateway/application-gateway-introduction
[custom-domain]: ../app-service-web-tutorial-custom-domain.md
[ilbase]: ./create-ilb-ase.md
