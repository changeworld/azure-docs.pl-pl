---
title: Tworzenie ase ilb v1
description: Tworzenie i używanie ASE z równoważeniem obciążenia. Ten doc jest dostępna tylko dla klientów, którzy używają starszej wersji ASE w wersji 1.
author: ccompy
ms.assetid: ad9a1e00-d5e5-413e-be47-e21e5b285dbf
ms.topic: article
ms.date: 07/11/2017
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: 0c03905017629e28e41cce2adaa65eac347b8185
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80294731"
---
# <a name="using-an-internal-load-balancer-with-an-app-service-environment"></a>Korzystanie z wewnętrznego modułu równoważenia obciążenia ze środowiskiem usługi aplikacji

> [!NOTE] 
> Ten artykuł dotyczy środowiska usługi app service w wersji 1. Istnieje nowsza wersja środowiska usługi app service, która jest łatwiejsza w użyciu i działa na bardziej zaawansowanej infrastruktury. Aby dowiedzieć się więcej o nowej wersji, zacznij od [wprowadzenia do środowiska usługi app service](intro.md).
>

Funkcja środowiska usługi aplikacji (ASE) to opcja usługi Premium usługi Azure App Service, która zapewnia ulepszoną konfigurację, która nie jest dostępna w sygnaturach z wieloma dzierżawami. Funkcja ASE zasadniczo wdraża usługę Azure App Service w sieci wirtualnej(VNet) platformy Azure. Aby uzyskać lepsze zrozumienie możliwości oferowanych przez środowiska usługi App Service, przeczytaj [dokumentację Środowiska usługi app service.][WhatisASE] Jeśli nie znasz korzyści płynących z pracy w sieci wirtualnej, przeczytaj często zadawane [pytania dotyczące sieci wirtualnej platformy Azure.][virtualnetwork] 

## <a name="overview"></a>Omówienie
Ase można wdrożyć z punktu końcowego dostępnego dla Internetu lub z adresem IP w sieci wirtualnej. Aby ustawić adres IP na adres sieci wirtualnej, należy wdrożyć program ASE z wewnętrznym modułem równoważenia obciążenia(ILB). Gdy ase jest skonfigurowany z równoważeniem obciążenia sieciowego, należy podać:

* własnej domeny lub subdomeny. Aby ułatwić, ten dokument zakłada poddomenę, ale można ją skonfigurować w obu przypadkach. 
* certyfikat używany dla protokołu HTTPS
* Zarządzanie dns dla poddomeny. 

Umożliwia to realizowanie m.in. następujących zadań:

* hostowanie aplikacji intranetowych, takich jak aplikacje biznesowe, bezpiecznie w chmurze, do której uzyskujesz dostęp za pośrednictwem witryny do witryny lub usługi ExpressRoute VPN
* hostowanie aplikacji w chmurze, które nie są wymienione na publicznych serwerach DNS
* tworzenie izolowanych aplikacji zaplecza internetowego, z którymi aplikacje front-end mogą bezpiecznie integrować

#### <a name="disabled-functionality"></a>Funkcje wyłączone
Istnieje kilka rzeczy, których nie można wykonać podczas korzystania z ASE ILB. Należą do nich:

* korzystanie z IPSSL
* przypisywanie adresów IP do określonych aplikacji
* zakupu i korzystania z certyfikatu z aplikacją za pośrednictwem portalu. Oczywiście nadal można uzyskać certyfikaty bezpośrednio z urzędem certyfikacji i używać go z aplikacjami, ale nie za pośrednictwem witryny Azure portal.

## <a name="creating-an-ilb-ase"></a>Tworzenie ase równoważenia obciążenia sieciowego
Tworzenie ASE ILB nie różni się zbytnio od tworzenia ASE normalnie. Aby uzyskać głębszą dyskusję na temat tworzenia środowiska ASE, zobacz [Jak utworzyć środowisko usługi app service][HowtoCreateASE]. Proces tworzenia ase równoważenia obciążenia jest taka sama między tworzeniem sieci wirtualnej podczas tworzenia ASE lub wybranie istniejącej sieci wirtualnej. Aby utworzyć środowisko ASE z wewnętrznym modułem równoważenia obciążenia: 

1. W witrynie Azure portal wybierz pozycję **Utwórz środowisko usługi App Service > sieci Web + mobile > .**
2. Wybierz subskrypcję.
3. Wybierz lub utwórz grupę zasobów.
4. Wybierz lub utwórz sieć wirtualną.
5. Utwórz podsieć, jeśli wybierzesz sieć wirtualną.
6. Wybierz **opcję Wirtualna sieć/lokalizacja -> konfigurację sieci wirtualnej** i ustaw typ adresu VIP na Wewnętrzny.
7. Podaj nazwę poddomeny (ta nazwa jest poddomeną używaną dla aplikacji utworzonych w tym ase).
8. Wybierz **przycisk OK,** a następnie **pozycję Utwórz**.

![][1]

W okienku Sieci wirtualnej istnieje opcja Konfiguracja sieci wirtualnej, która umożliwia wybór między zewnętrznym adresem VIP lub wewnętrznym programem VIP. Wartość domyślna to Zewnętrzny. Jeśli masz go ustawionego na Zewnętrzny, twój ASE korzysta z internetowego programu VIP. W przypadku wybrania opcji Wewnętrzny środowisko ASE jest skonfigurowane z wewnętrznym modułem równoważenia obciążenia i adresem IP w sieci wirtualnej. 

Po wybraniu opcji Internal możliwość dodania większej liczby adresów IP do systemu ASE jest usuwana, a zamiast tego należy podać poddomenę ASE. W programie ASE z zewnętrznym elementem VIP nazwa programu ASE jest używana w poddomenie dla aplikacji utworzonych w tym programie ASE. Jeśli twój ASE nosi nazwę ***contosotest,*** a aplikacja w tym ASE nosi nazwę ***mytest,*** poddomena ma format ***contosotest.p.azurewebsites.net,*** a adres URL tej aplikacji jest ***mytest.contosotest.p.azurewebsites.net***. Jeśli ustawisz typ VIP na Wewnętrzny, twoja nazwa ASE nie jest używana w poddomenie dla ase. Subdomenę można określić jawnie. Jeśli twoja poddomena jest ***contoso.corp.net*** i została udostępniona aplikacja w tym ASE o nazwie ***timereporting,*** adres URL tej aplikacji jest ***timereporting.contoso.corp.net***.

## <a name="apps-in-an-ilb-ase"></a>Aplikacje w ase ILB
Tworzenie aplikacji w ase ILB jest taka sama jak tworzenie aplikacji w ASE normalnie. 

1. W witrynie Azure portal wybierz pozycję **Utwórz zasób -> Web + Mobile -> Web** lub **Mobile** lub **API App**.
2. Wprowadź nazwę aplikacji.
3. Wybierz subskrypcję.
4. Wybierz lub utwórz grupę zasobów.
5. Wybierz lub utwórz plan usługi aplikacji (ASP). Jeśli tworzysz nowy asp, wybierz ase jako lokalizację i wybierz pulę pracowników, w której ma zostać utworzony asp. Podczas tworzenia asp, należy wybrać ase jako lokalizację i puli pracowników. Po określeniu nazwy aplikacji zostanie wyświetlona nazwa poddomeny pod nazwą aplikacji, która zostanie zastąpiona poddomeną dla systemu ASE. 
6. Wybierz **pozycję Utwórz**. Pamiętaj, aby zaznaczyć pole wyboru **Przypnij do pulpitu nawigacyjnego,** jeśli chcesz, aby aplikacja wyświetlała się na pulpicie nawigacyjnym. 

![][2]

Pod nazwą aplikacji nazwa poddomeny zostanie zaktualizowana w celu odzwierciedlenia poddomeny systemu ASE. 

## <a name="post-ilb-ase-creation-validation"></a>Sprawdzanie poprawności tworzenia ASE przy tworzeniu ilb
Środowisko ASE z wewnętrznym modułem równoważenia obciążenia różni się nieco od wersji bez tego modułu. Jak już wspomniano, musisz zarządzać własnym systemem DNS, a także musisz podać własny certyfikat dla połączeń HTTPS. 

Po utworzeniu ase, można zauważyć, że poddomena pokazuje poddomenę, którą określono i jest nowy element w menu **Ustawienie** o nazwie **Certyfikat równoważenia obciążenia.** ASE jest tworzony z certyfikatem z podpisem własnym, co ułatwia testowanie protokołu HTTPS. Portal informuje, że musisz podać własny certyfikat dla protokołu HTTPS, ale ma to na celu zachęcenie do posiadania certyfikatu, który jest związany z poddomeną. 

![][3]

Jeśli po prostu próbujesz i nie wiesz, jak utworzyć certyfikat, możesz użyć aplikacji konsoli programu IIS MMC do utworzenia certyfikatu z podpisem własnym. Po utworzeniu można go wyeksportować jako plik .pfx, a następnie przekazać w interfejsie użytkownika certyfikatu równoważenia obciążenia obciążenia. Po uzyskaniu dostępu do witryny zabezpieczonej certyfikatem z podpisem własnym przeglądarka ostrzega, że witryna, do której uzyskujesz dostęp, nie jest bezpieczna ze względu na niemożność zweryfikowanie certyfikatu. Jeśli chcesz uniknąć tego ostrzeżenia, musisz poprawnie podpisany certyfikat, który pasuje do poddomeny i ma łańcuch zaufania, który jest rozpoznawany przez przeglądarkę.

![][6]

Jeśli chcesz wypróbować przepływ z własnymi certyfikatami i przetestować dostęp http i HTTPS do swojego ASE:

1. Przejdź do interfejsu UŻYTKOWNIKA ASE po utworzeniu **ase -> Ustawienia -> certyfikaty ILB**.
2. Ustaw certyfikat równoważenia obciążenia sieciowego, wybierając plik pfx certyfikatu i podaj hasło. Ten krok zajmuje trochę czasu, aby przetworzyć i pojawia się komunikat, że operacja skalowania jest w toku.
3. Pobierz adres równoważenia obciążenia sieciowego dla środowiska ASE (**ASE -> Properties -> Virtual IP Address**).
4. Utwórz aplikację internetową w ase po utworzeniu. 
5. Utwórz maszynę wirtualną, jeśli nie masz go w tej sieci wirtualnej (nie w tej samej podsieci co ASE lub rzeczy przerwy).
6. Ustaw dns dla poddomeny. Możesz użyć symbolu wieloznacznego z poddomeną w systemie DNS lub jeśli chcesz wykonać kilka prostych testów, edytuj plik hosts na maszynie Wirtualnej, aby ustawić nazwę aplikacji internetowej na adres IP VIP. Jeśli twój ASE miał nazwę poddomeny .ilbase.com i zrobiłeś aplikację internetową mytestapp tak, że zostanie on rozwiązany w mytestapp.ilbase.com, ustaw to w pliku hosts. (W systemie Windows plik hosts znajduje się pod adresem C:\Windows\System32\drivers\etc\)
7. Użyj przeglądarki na tej maszynie `https://mytestapp.ilbase.com` wirtualnej i przejdź do (lub niezależnie od nazwy aplikacji sieci web jest z poddomeną).
8. Skorzystaj z przeglądarki na tej maszynie wirtualnej i przejdź na stronę `https://mytestapp.ilbase.com`. W przypadku korzystania z certyfikatu z podpisem własnym należy zaakceptować brak zabezpieczeń. 

Adres IP równoważenia obciążenia sieciowego jest wyświetlany we właściwościach jako wirtualny adres IP.

![][4]

## <a name="using-an-ilb-ase"></a>Korzystanie z ase ILB
#### <a name="network-security-groups"></a>Grupy zabezpieczeń sieci
ALB ASE umożliwia izolację sieci dla aplikacji. Aplikacje nie są dostępne lub nawet znane przez Internet. Takie podejście jest doskonałe do hostowania witryn intranetowych, takich jak aplikacje biznesowe. Jeśli trzeba jeszcze bardziej ograniczyć dostęp, nadal można użyć sieciowych grup zabezpieczeń (NSG) do kontrolowania dostępu na poziomie sieci. 

Jeśli chcesz użyć nsgs do dalszego ograniczania dostępu, należy upewnić się, że nie złamać komunikacji, że ASE potrzebuje, aby działać. Mimo że dostęp HTTP/HTTPS jest tylko za pośrednictwem równoważenia obciążenia sieciowego używanego przez ASE, ase nadal zależy od zasobów poza siecią wirtualną. Aby zobaczyć, jaki dostęp do sieci jest nadal wymagany, zobacz [Kontrolowanie ruchu przychodzącego do środowiska usługi aplikacji][ControlInbound] i szczegóły konfiguracji sieci dla środowisk usługi app service za pomocą usługi [ExpressRoute][ExpressRoute]. 

Aby skonfigurować sieciowe sieciowe, musisz znać adres IP, który jest używany przez platformę Azure do zarządzania systemem ASE. Ten adres IP jest również wychodzącym adresem IP z ase, jeśli sprawia, że żądania internetowe. Wychodzący adres IP dla twojego ASE pozostaje statyczny przez cały okres użytkowania ase. Jeśli usuniesz i odtworzysz swój ASE, otrzymasz nowy adres IP. Aby znaleźć adres IP, przejdź do **ustawienia -> właściwości** i znajdź **wychodzący adres IP**. 

![][5]

#### <a name="general-ilb-ase-management"></a>Ogólne zarządzanie ASE ILB
Zarządzanie ASE ILB jest w dużej mierze taka sama jak zarządzanie ASE normalnie. Należy skalować w górę pule procesów roboczych, aby obsługiwać więcej wystąpień ASP i skalować serwery frontona, aby obsłużyć zwiększone ilości ruchu HTTP/HTTPS. Aby uzyskać ogólne informacje dotyczące zarządzania konfiguracją środowiska ASE, zobacz [Konfigurowanie środowiska usługi aplikacji][ASEConfig]. 

Dodatkowymi elementami zarządzania są zarządzanie certyfikatami i dns. Po utworzeniu ase i zastąpieniu go przed jego wygaśnięciem należy uzyskać i przekazać certyfikat używany do stosowania protokołu HTTPS. Ponieważ platforma Azure jest właścicielem domeny podstawowej, może dostarczać certyfikaty dla asów z zewnętrznym obiektem VIP. Ponieważ poddomeną używaną przez ASE równoważenia obciążenia sieciowego może być wszystko, należy podać własny certyfikat dla protokołu HTTPS. 

#### <a name="dns-configuration"></a>Konfiguracja DNS
Podczas korzystania z zewnętrznego adresu VIP dns jest zarządzany przez platformę Azure. Każda aplikacja utworzona w środowisku ASE jest automatycznie dodawana do usługi Azure DNS, która jest publiczną usługą DNS. W środowisku ASE z wewnętrznym modułem równoważenia obciążenia musisz zarządzać własną usługą DNS. W przypadku danej poddomeny, takiej jak contoso.corp.net, należy utworzyć rekordy DNS A wskazujące adres ILB dla:

    * 
    *.scm ftp publikować 


## <a name="getting-started"></a>Wprowadzenie
Aby rozpocząć korzystanie ze środowisk usługi App Service, zobacz [Wprowadzenie do środowisk usługi app service][WhatisASE]

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
[AppServicePricing]: https://azure.microsoft.com/pricing/details/app-service/
[ASEAutoscale]: app-service-environment-auto-scale.md
[ExpressRoute]: app-service-app-service-environment-network-configuration-expressroute.md
[vnetnsgs]: https://azure.microsoft.com/documentation/articles/virtual-networks-nsg/
[ASEConfig]: app-service-web-configure-an-app-service-environment.md
