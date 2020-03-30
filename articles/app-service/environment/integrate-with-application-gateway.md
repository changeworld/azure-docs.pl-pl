---
title: Integracja z usługą Application Gateway
description: Dowiedz się, jak zintegrować aplikację ze środowiskiem usługi ILB App Service z bramą aplikacji w tym kompleksowym przejściu.
author: ccompy
ms.assetid: a6a74f17-bb57-40dd-8113-a20b50ba3050
ms.topic: article
ms.date: 03/03/2018
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: dfb6d72b3f8f61e1350101173ecec6134a614edf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74687151"
---
# <a name="integrate-your-ilb-app-service-environment-with-the-azure-application-gateway"></a>Integrate your ILB App Service Environment with the Azure Application Gateway (Integrowanie środowiska App Service Environment wewnętrznego modułu równoważenia obciążenia z usługą Azure Application Gateway) #

[Środowisko usługi aplikacji](./intro.md) jest wdrożenie usługi Azure App Service w podsieci sieci wirtualnej platformy Azure klienta. Można go wdrożyć z publicznym lub prywatnym punktem końcowym dostępu do aplikacji. Wdrożenie środowiska usługi app service z prywatnym punktem końcowym (czyli wewnętrznym modułem równoważenia obciążenia) jest nazywane środowiskiem usługi aplikacji równoważenia obciążenia.  

Zapory aplikacji sieci Web pomagają zabezpieczyć aplikacje internetowe, sprawdzając przychodzący ruch internetowy w celu blokowania iniekcji SQL, skryptów międzyustnych, przekazywania złośliwego oprogramowania & aplikacji DDoS i innych ataków. Sprawdza również odpowiedzi z serwerów sieci web zaplecza dla zapobiegania utracie danych (DLP). Możesz uzyskać urządzenie WAF z portalu Azure marketplace lub użyć [bramy aplikacji azure.][appgw]

Brama aplikacji platformy Azure to urządzenie wirtualne, które zapewnia równoważenie obciążenia warstwy 7, odciążanie SSL i ochronę zapory aplikacji sieci web (WAF). Można nasłuchiwać na publiczny adres IP i kierowania ruchu do punktu końcowego aplikacji. W poniższych informacjach opisano sposób integracji bramy aplikacji skonfigurowanej przez waf z aplikacją w środowisku usługi aplikacji równoważenia obciążenia.  

Integracja bramy aplikacji ze środowiskiem usługi aplikacji równoważenia obciążenia sieciowego jest na poziomie aplikacji. Podczas konfigurowania bramy aplikacji ze środowiskiem usługi aplikacji równoważenia obciążenia, robisz to dla określonych aplikacji w środowisku usługi aplikacji równoważenia obciążenia. Ta technika umożliwia hosting bezpiecznych aplikacji wielodostępnych w jednym środowisku usługi aplikacji równoważenia obciążenia.  

![Brama aplikacji wskazująca aplikację w środowisku usługi aplikacji równoważenia obciążenia sieciowego][1]

Korzystając z tego przewodnika, wykonasz następujące czynności:

* Utwórz bramę aplikacji platformy Azure.
* Skonfiguruj bramę aplikacji, aby wskazywała aplikację w środowisku usługi aplikacji równoważenia obciążenia.
* Skonfiguruj aplikację tak, aby honorować niestandardową nazwę domeny.
* Edytuj publiczną nazwę hosta DNS, która wskazuje bramę aplikacji.

## <a name="prerequisites"></a>Wymagania wstępne

Aby zintegrować bramę aplikacji ze środowiskiem usługi app service ilb, należy:

* Środowisko usługi aplikacji równoważenia obciążenia sieciowego.
* Aplikacja uruchomiona w środowisku usługi aplikacji równoważenia obciążenia.
* Nazwa domeny z routingiem internetowym, która ma być używana z aplikacją w środowisku usługi aplikacji równoważenia obciążenia sieciowego.
* Adres równoważenia obciążenia sieciowego używany przez środowisko usługi aplikacji równoważenia obciążenia sieciowego. Te informacje znajdują się w portalu Środowiska usługi app service w obszarze **Ustawienia** > **adresów IP:**

    ![Przykładowa lista adresów IP używanych przez środowisko usługi aplikacji równoważenia obciążenia sieciowego][9]
    
* Publiczna nazwa DNS, która jest później używana do wskazywanie bramy aplikacji. 

Aby uzyskać szczegółowe informacje na temat tworzenia środowiska usługi aplikacji równoważenia obciążenia, zobacz [Tworzenie i używanie środowiska usługi aplikacji równoważenia obciążenia.][ilbase]

W tym artykule założono, że chcesz bramy aplikacji w tej samej sieci wirtualnej platformy Azure, w której jest wdrażane środowisko usługi app service. Przed rozpoczęciem tworzenia bramy aplikacji należy wybrać lub utworzyć podsieć, która będzie używana do hosta bramy. 

Należy użyć podsieci, która nie jest o nazwie GatewaySubnet. Jeśli brama aplikacji zostanie umieszczona w sieci GatewaySubnet, nie będzie można później utworzyć bramy sieci wirtualnej. 

Nie można również umieścić bramy w podsieci używanej przez środowisko usługi app service równoważenia obciążenia. Środowisko usługi aplikacji jest jedyną rzeczą, która może znajdować się w tej podsieci.

## <a name="configuration-steps"></a>Kroki konfiguracji ##

1. W witrynie Azure portal przejdź do **nowej** > bramy aplikacji**sieciowej** > **Application Gateway**.

2. W obszarze **Podstawy:**

   a. W **ciemięgosce**wprowadź nazwę bramy aplikacji.

   b. W przypadku **warstwy**wybierz **WAF**.

   d. W przypadku **subskrypcji**wybierz tę samą subskrypcję, której używa sieć wirtualna środowiska usługi App Service.

   d. W przypadku **grupy zasobów**utwórz lub wybierz grupę zasobów.

   e. W obszarze **Lokalizacja**wybierz lokalizację sieci wirtualnej Środowisko usługi aplikacji.

   ![Podstawy tworzenia bramy aplikacji][2]

3. W obszarze **Ustawienia:**

   a. W przypadku **sieci wirtualnej**wybierz sieć wirtualną środowiska usługi aplikacji.

   b. W przypadku **podsieci**wybierz podsieć, w której należy wdrożyć bramę aplikacji. Nie należy używać GatewaySubnet, ponieważ uniemożliwi to tworzenie bram sieci VPN.

   d. W przypadku **typu adresu IP**wybierz pozycję **Publiczne**.

   d. W przypadku **publicznego adresu IP**wybierz publiczny adres IP. Jeśli go nie masz, utwórz go teraz.

   e. W przypadku **protokołu**wybierz **http** lub **HTTPS**. Jeśli konfigurujesz dla protokołu HTTPS, musisz podać certyfikat PFX.

   f. W przypadku **zapory aplikacji sieci Web**można włączyć zaporę, a także ustawić ją dla **wykrywania** lub **zapobiegania** w razie potrzeby.

   ![Nowe ustawienia tworzenia bramy aplikacji][3]
    
4. W sekcji **Podsumowanie** przejrzyj ustawienia i wybierz **przycisk OK**. Zakończenie instalacji bramy aplikacji może potrwać nieco ponad 30 minut.  

5. Po zakończeniu konfiguracji bramy aplikacji przejdź do portalu bramy aplikacji. Wybierz **pulę wewnętrznej bazy danych**. Dodaj adres równoważenia obciążenia sieciowego dla środowiska usługi aplikacji równoważenia obciążenia.

   ![Konfigurowanie puli zaplecza][4]

6. Po zakończeniu procesu konfigurowania puli zaplecza wybierz opcję **Sondy kondycji**. Utwórz sondę kondycji dla nazwy domeny, której chcesz użyć dla aplikacji. 

   ![Konfigurowanie sond kondycji][5]
    
7. Po zakończeniu procesu konfigurowania sond kondycji wybierz **pozycję Ustawienia HTTP**. Edytuj istniejące ustawienia, wybierz **pozycję Użyj sondy niestandardowej**i wybierz skonfigurowany numer sondy.

   ![Konfigurowanie ustawień HTTP][6]
    
8. Przejdź do sekcji **Omówienie** bramy aplikacji i skopiuj publiczny adres IP używany przez bramę aplikacji. Ustaw ten adres IP jako rekord A dla nazwy domeny aplikacji lub użyj nazwy DNS dla tego adresu w rekordzie CNAME. Łatwiej jest wybrać publiczny adres IP i skopiować go z interfejsu użytkownika publicznego adresu IP, zamiast skopiować go z łącza w sekcji **Przegląd** bramy aplikacji. 

   ![Portal bramy aplikacji][7]

9. Ustaw niestandardową nazwę domeny aplikacji w środowisku usługi ILB App Service. Przejdź do aplikacji w portalu i w obszarze **Ustawienia**wybierz pozycję **Domeny niestandardowe**.

   ![Ustawianie niestandardowej nazwy domeny w aplikacji][8]

Informacje dotyczące ustawiania niestandardowych nazw domen aplikacji sieci Web znajdują się w artykule [Ustawianie niestandardowych nazw domen aplikacji sieci Web][custom-domain]. Ale dla aplikacji w środowisku usługi aplikacji równoważenia obciążenia, nie ma żadnych sprawdzania poprawności nazwy domeny. Ponieważ jesteś właścicielem systemu DNS, który zarządza punktami końcowymi aplikacji, możesz umieścić tam wszystko, co chcesz. Niestandardowa nazwa domeny dodana w tym przypadku nie musi znajdować się w systemie DNS, ale nadal musi być skonfigurowana w aplikacji. 

Po zakończeniu instalacji i pozwolono na krótki czas propagacji zmian DNS, można uzyskać dostęp do aplikacji przy użyciu niestandardowej nazwy domeny, która została utworzona. 


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
