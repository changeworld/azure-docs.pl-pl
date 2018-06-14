---
title: Tworzenie i używanie wewnętrzny moduł równoważenia obciążenia z środowiska usługi aplikacji | Dokumentacja firmy Microsoft
description: Tworzenie i korzystanie z ILB ASE
services: app-service
documentationcenter: ''
author: ccompy
manager: stefsch
editor: ''
ms.assetid: ad9a1e00-d5e5-413e-be47-e21e5b285dbf
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: ccompy
ms.openlocfilehash: f7c94b790c6aa7c75c62fd05671f016b7185b2a2
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/21/2018
ms.locfileid: "29388819"
---
# <a name="using-an-internal-load-balancer-with-an-app-service-environment"></a>Przy użyciu wewnętrznego modułu równoważenia obciążenia z środowiska usługi aplikacji

> [!NOTE] 
> Ten artykuł dotyczy v1 środowiska usługi aplikacji. Istnieje nowsza wersja środowiska usługi aplikacji jest łatwiejsza w użyciu, który jest uruchamiany na bardziej zaawansowanych infrastruktury. Aby dowiedzieć się więcej o nowy początek wersji z [wprowadzenie do środowiska usługi aplikacji](intro.md).
>

Funkcji środowiska usługi aplikacji (ASE) to opcja Premium usługi Azure App Service, która zapewnia możliwość rozszerzonej konfiguracji, która nie jest dostępna w sygnatury wielodostępnej. Funkcja ASE wdraża zasadniczo usłudze Azure App Service w Twojej Network(VNet) wirtualnych Azure. Aby uzyskać lepsze zrozumienie możliwości oferowane przez odczytać środowiska usługi App Service [co to jest środowisko usługi aplikacji] [ WhatisASE] dokumentacji. Jeśli nie znasz zalet działających w sieci wirtualnej odczytu [często zadawane pytania sieci wirtualnych Azure][virtualnetwork]. 

## <a name="overview"></a>Przegląd
ASE można wdrożyć z dostępnym punkcie końcowym internet lub z adresu IP w sieci wirtualnej. Aby ustawić adres IP na adres sieci wirtualnej, należy wdrożyć Twojej ASE z wewnętrznego Balancer(ILB) obciążenia. Po skonfigurowaniu sieci ASE ILB, zawierają:

* własnych domeny lub poddomeny. Aby ułatwić, tym dokumencie przyjęto założenie domeny podrzędnej, ale można skonfigurować w obu przypadkach. 
* certyfikat używany do obsługi protokołu HTTPS
* Zarządzanie systemem DNS z domeny podrzędnej. 

Umożliwia to realizowanie m.in. następujących zadań:

* Host intranet aplikacji, takich jak aplikacji — biznesowych bezpiecznie w chmurze, która dostęp za pośrednictwem witryny do witryny lub sieci VPN usługi ExpressRoute
* aplikacje hosta w chmurze, które nie są wymienione w publicznych serwerów DNS
* Utwórz internet izolowane wewnętrznej bazy danych aplikacji, z którymi frontonu aplikacji można bezpiecznie zintegrować

#### <a name="disabled-functionality"></a>Funkcje wyłączone
Istnieje kilka kwestii, które nie można wykonać przy użyciu ASE ILB. Czynności te obejmują:

* przy użyciu IPSSL
* Przypisywanie adresów IP do określonych aplikacji
* kupowanie i używa certyfikatu z aplikacją za pośrednictwem portalu. Można oczywiście nadal uzyskiwanie certyfikatów bezpośrednio z urzędu certyfikacji i używać go z aplikacjami, ale nie za pośrednictwem portalu Azure.

## <a name="creating-an-ilb-ase"></a>Tworzenie ILB ASE
Tworzenie ASE ILB nie jest znacznie różni się od tworzenia ASE normalnie. Omówienie więcej danych na temat tworzenia ASE, zobacz [tworzenie środowiska usługi aplikacji][HowtoCreateASE]. Proces tworzenia ASE ILB jest taka sama, między tworzenia sieci wirtualnej podczas tworzenia ASE lub wybierz istniejące sieci wirtualnej. Aby utworzyć środowisko ASE z wewnętrznym modułem równoważenia obciążenia: 

1. W portalu Azure wybierz **Utwórz zasób -> Sieć Web i mobilność -> środowisko usługi aplikacji**.
2. Wybierz subskrypcję.
3. Wybierz lub utwórz grupę zasobów.
4. Wybierz lub utwórz sieć wirtualną.
5. Utwórz podsieć, w przypadku wybrania sieci wirtualnej.
6. Wybierz **wirtualnego lokalizacji-> Konfiguracja sieci wirtualnej** i Ustaw typ adresu VIP do wewnętrznego.
7. Podaj nazwę domeny podrzędnej (Ta nazwa jest poddomeny używane w przypadku aplikacji utworzone w tym ASE).
8. Wybierz **OK** , a następnie **utworzyć**.

![][1]

W okienku sieci wirtualnej ma konfigurację sieci wirtualnej, wybierz opcję umożliwiającą między zewnętrznego adresu VIP lub wewnętrznego adresu VIP. Wartość domyślna to zewnętrznego. Jeśli masz ustawioną zewnętrzne sieci ASE używa internet dostępne wirtualne adresy IP. W przypadku wybrania wewnętrzne Twojej ASE skonfigurowano ILB na adres IP w sieci wirtualnej. 

Po wybraniu wewnętrzne, możliwość dodawania więcej adresów IP do sieci ASE zostaną usunięte i zamiast tego należy podać poddomeny ASE. W elemencie ASE z zewnętrznego wirtualnego adresu IP nazwę ASE służy w poddomenie aplikacje utworzone w tym ASE. Jeśli Twoje ASE nosi nazwę ***contosotest*** i nosi nazwę aplikacji w tym ASE ***test***, poddomeny jest w formacie ***contosotest.p.azurewebsites.net*** i adres URL dla tej aplikacji jest ***mytest.contosotest.p.azurewebsites.net***. Jeśli ustawisz typ adresu VIP do wewnętrznego nazwę ASE nie jest używany podczas poddomeny dla ASE. Należy jawnie określić poddomeny. Jeśli Twoje domeny podrzędnej jest ***contoso.corp.net*** i wprowadzone aplikacji, w tym o nazwie ASE ***timereporting***, adres URL dla tej aplikacji jest ***timereporting.contoso.corp.net***.

## <a name="apps-in-an-ilb-ase"></a>Aplikacje w elemencie ASE ILB
Tworzenie aplikacji w elemencie ASE ILB jest taka sama, jak podczas tworzenia aplikacji w elemencie ASE normalnie. 

1. W portalu Azure wybierz **Utwórz zasób -> Sieć Web i mobilność -> sieci Web** lub **Mobile** lub **aplikacji interfejsu API**.
2. Wprowadź nazwę aplikacji.
3. Wybierz subskrypcję.
4. Wybierz lub utwórz grupę zasobów.
5. Wybierz lub Utwórz Plan(ASP) usługi aplikacji. W przypadku tworzenia nowego ASP, wybierz użytkownika ASE jako lokalizację, a następnie wybierz puli procesów roboczych ma strona ASP ma być utworzony w. Podczas tworzenia ASP, należy wybrać Twojej ASE jako lokalizację i puli procesów roboczych. Po określeniu nazwy aplikacji, zobaczysz, że poddomeny pod nazwą aplikacji zostało zastąpione przez poddomeny dla Twojego ASE. 
6. Wybierz pozycję **Utwórz**. Należy wybrać **Przypnij do pulpitu nawigacyjnego** pole wyboru, jeśli chcesz, aby aplikacja wyświetlani na pulpicie nawigacyjnym. 

![][2]

W obszarze nazwy aplikacji Nazwa poddomeny pobiera zaktualizowana w celu odzwierciedlenia poddomeny ASE użytkownika. 

## <a name="post-ilb-ase-creation-validation"></a>Sprawdzanie poprawności tworzenia ILB ASE POST
Środowisko ASE z wewnętrznym modułem równoważenia obciążenia różni się nieco od wersji bez tego modułu. Jak już zauważyć trzeba zarządzać własną DNS należy również podać swój własny certyfikat dla połączeń HTTPS. 

Po utworzeniu sieci ASE zauważysz poddomeny pokazuje poddomeny, zostanie określony, a nowy element **ustawienie** menu o nazwie **certyfikatu ILB**. ASE jest tworzony z certyfikatu z podpisem własnym, co pozwala na łatwiejsze testowanie HTTPS. Portalu informuje, że musisz dostarczyć własny certyfikat dla protokołu HTTPS, ale to zachęca znajduje się certyfikat, który łączy się z Twojej domeny podrzędnej. 

![][3]

Jeśli są po prostu testuje rzeczy, a nie wiadomo, jak utworzyć certyfikat, można użyć aplikacji konsoli MMC programu IIS do utworzenia certyfikatu z podpisem własnym. Po jego utworzeniu, należy go wyeksportować w postaci pliku PFX, a następnie przekaż w Interfejsie użytkownika ILB certyfikatu. Po uzyskaniu dostępu do witryny zabezpieczonej z certyfikatu z podpisem własnym, przeglądarka wyświetla ostrzeżenie, że uzyskują dostęp do witryny nie jest zabezpieczone z powodu braku możliwości zweryfikowania poprawności certyfikatu. Jeśli chcesz uniknąć tego ostrzeżenia, należy poprawnie podpisane certyfikat, który jest zgodny z poddomeny i ma łańcuch zaufania, który jest rozpoznawany przez przeglądarkę.

![][6]

Jeśli chcesz spróbować przepływ z własnych certyfikatów i testowanie protokołów HTTP i HTTPS dostęp do Twojego ASE:

1. Przejdź do ASE interfejsu użytkownika, po utworzeniu ASE **ASE -> Ustawienia -> Certyfikaty ILB**.
2. Określ certyfikat ILB, wybierając plik pfx certyfikatu i podaj hasło. Ten krok zajmuje chwilę do przetwarzania i pojawi się komunikat, że operacja skalowania jest w toku.
3. Pobierz adres ILB dla Twojego ASE (**ASE -> Właściwości -> wirtualnego adresu IP**).
4. Tworzenie aplikacji sieci web w ASE po jego utworzeniu. 
5. Tworzenie maszyny Wirtualnej, jeśli nie masz w tej sieci VNET (nie w tej samej podsieci co podział ASE lub innymi).
6. Ustaw DNS z domeny podrzędnej. Można użyć symbolu wieloznacznego z Twojej domeny podrzędnej w systemie DNS lub jeśli chcesz to proste testy, należy edytować plik hosts na maszynie Wirtualnej można ustawić nazwy aplikacji sieci web do adresu VIP. Jeśli Twoje ASE ma nazwę poddomeny. ilbase.com i wprowadzone mytestapp aplikacji sieci web, dzięki czemu będą podejmowane na mytestapp.ilbase.com, ustawić go w pliku hostów. (W systemie Windows, w pliku hosts jest w C:\Windows\System32\drivers\etc\)
7. Korzystanie z przeglądarki na tej maszynie Wirtualnej, przejdź do http://mytestapp.ilbase.com (lub niezależnie od nazwę aplikacji sieci web jest z Twojej domeny podrzędnej).
8. Za pomocą przeglądarki na tej maszynie wirtualnej przejdź do strony https://mojaaplikacjatestowa.aseilb.com. Jeśli przy użyciu certyfikatu z podpisem własnym, musisz zaakceptować braku zabezpieczeń. 

Adres IP dla sieci ILB wymienionym we właściwościach wirtualnego adresu IP.

![][4]

## <a name="using-an-ilb-ase"></a>Przy użyciu środowiska ASE wewnętrznego modułu równoważenia obciążenia
#### <a name="network-security-groups"></a>Grupy zabezpieczeń sieci
ASE ILB umożliwia izolację sieci dla aplikacji. Aplikacje nie są dostępne lub nawet znane przez internet. Takie podejście jest doskonała do hostowania witryn intranetowych, takich jak aplikacje z biznesowych. Gdy musisz ograniczyć jeszcze bardziej dostęp do kontroli dostępu na poziomie sieci można nadal używać Groups(NSGs) zabezpieczeń sieci. 

Jeśli chcesz bardziej ograniczyć dostęp za pomocą grup NSG, należy się upewnić się, że nie przerwanie komunikacji ASE wymaga, aby działać. Chociaż dostęp HTTP/HTTPS jest tylko za pośrednictwem ILB używane przez ASE, ASE nadal zależy od zasobów poza siecią wirtualną. Aby zobaczyć, jakie dostępu do sieci jest nadal wymagane, zobacz [kontrolowanie ruch przychodzący do środowiska usługi aplikacji] [ ControlInbound] i [szczegóły konfiguracji środowiska usługi App Service z sieci ExpressRoute][ExpressRoute]. 

Aby skonfigurować grup NSG, musisz znać adres IP, który jest używany przez usługę Azure do zarządzania z ASE. Ten adres IP jest również wychodzący adres IP z sieci ASE, jeśli żądania internetowe. Wychodzący adres IP dla sieci ASE pozostaje statyczna przez cały okres istnienia ASE użytkownika. Jeśli Usuń i Utwórz ponownie Twoje ASE, otrzymasz nowy adres IP. Aby znaleźć adres IP, przejdź do **Ustawienia -> właściwości** i Znajdź **wychodzący adres IP**. 

![][5]

#### <a name="general-ilb-ase-management"></a>Zarządzanie ogólne ILB ASE
Zarządzanie ASE ILB przede wszystkim jest taka sama jak zarządzanie ASE normalnie. Należy skalować Twojej pule procesów roboczych, aby obsługiwać więcej wystąpień ASP i skalowanie w górę serwery frontonu, aby obsłużyć zwiększonej ilości ruchu HTTP/HTTPS. Ogólne informacje na temat zarządzania konfiguracją ASE, zobacz [Konfigurowanie środowiska usługi aplikacji][ASEConfig]. 

Elementy na dodatkowe zarządzanie to zarządzanie certyfikatami i zarządzania DNS. Należy uzyskać i przekazać certyfikat używany do obsługi protokołu HTTPS, po utworzeniu ILB ASE i zastąp go przed jego wygaśnięciem. Ponieważ Azure jest właścicielem domeny podstawowej, zapewniają certyfikaty dla ASEs z zewnętrznego adresu VIP. Ponieważ może to być dowolne poddomeny używane przez ASE ILB, musisz podać swój własny certyfikat dla protokołu HTTPS. 

#### <a name="dns-configuration"></a>Konfiguracja DNS
Podczas korzystania z zewnętrznego adresu VIP, DNS jest zarządzana przez platformę Azure. Każda aplikacja utworzona w środowisku ASE jest automatycznie dodawana do usługi Azure DNS, która jest publiczną usługą DNS. W środowisku ASE z wewnętrznym modułem równoważenia obciążenia musisz zarządzać własną usługą DNS. Dla danej domeny podrzędnej, takich jak contoso.corp.net należy utworzyć rekordy, które wskazują na Twój adres ILB A systemu DNS:

    * 
    publikowanie *.SCM ftp 


## <a name="getting-started"></a>Wprowadzenie
Wprowadzenie do środowiska usługi App Service, zobacz [wprowadzenie do środowiska usługi App Service][WhatisASE]

[!INCLUDE [app-service-web-try-app-service](../../../includes/app-service-web-try-app-service.md)]

<!--Image references-->
[1]: ./media/app-service-environment-with-internal-load-balancer/ilbase-createilbase.png
[2]: ./media/app-service-environment-with-internal-load-balancer/ilbase-createapp.png
[3]: ./media/app-service-environment-with-internal-load-balancer/ilbase-newase.png
[4]: ./media/app-service-environment-with-internal-load-balancer/ilbase-vip.png
[5]: ./media/app-service-environment-with-internal-load-balancer/ilbase-externalvip.png
[6]: ./media/app-service-environment-with-internal-load-balancer/ilbase-ilbcertificate.png

<!--Links-->
[WhatisASE]: app-service-app-service-environment-intro.md
[HowtoCreateASE]: app-service-web-how-to-create-an-app-service-environment.md
[ControlInbound]: app-service-app-service-environment-control-inbound-traffic.md
[virtualnetwork]: https://azure.microsoft.com/documentation/articles/virtual-networks-faq/
[AppServicePricing]: http://azure.microsoft.com/pricing/details/app-service/
[ASEAutoscale]: app-service-environment-auto-scale.md
[ExpressRoute]: app-service-app-service-environment-network-configuration-expressroute.md
[vnetnsgs]: http://azure.microsoft.com/documentation/articles/virtual-networks-nsg/
[ASEConfig]: app-service-web-configure-an-app-service-environment.md
