---
title: Tworzenie i używanie wewnętrznego modułu równoważenia obciążenia w środowisku App Service — Azure | Dokumentacja firmy Microsoft
description: Tworzenie i używanie środowiska ASE z wewnętrznym modułem równoważenia obciążenia
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
ms.custom: seodec18
ms.openlocfilehash: 88f100bc780d8df0202cfcce9b390085a71fc905
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "62130606"
---
# <a name="using-an-internal-load-balancer-with-an-app-service-environment"></a>Przy użyciu wewnętrznego modułu równoważenia obciążenia w środowisku usługi App Service

> [!NOTE] 
> Ten artykuł dotyczy środowiska App Service Environment w wersji 1. Istnieje nowsza wersja usługi App Service Environment jest łatwiejszy w obsłudze, która działa na bardziej zaawansowanych infrastruktury. Aby dowiedzieć się więcej na temat nowej wersji rozpoczynać [wprowadzenie do usługi App Service Environment](intro.md).
>

Funkcja App Service Environment (ASE) to opcja usługi Premium usługi Azure App Service, która dostarcza możliwości rozszerzonej konfiguracji, która nie jest dostępna w sygnatury wielodostępu. Funkcja środowiska ASE wdraża przede wszystkim usłudze Azure App Service w Twoja sieć wirtualna platformy Azure. Aby uzyskać lepsze zrozumienie możliwości oferowane przez odczyt środowisk App Service Environment [co to jest środowisko usługi App Service] [ WhatisASE] dokumentacji. Jeśli nie znasz korzyści systemu operacyjnego podczas odczytywania sieci wirtualnej [często zadawane pytania sieci wirtualnych Azure][virtualnetwork]. 

## <a name="overview"></a>Omówienie
Środowisko ASE można wdrożyć z dostępnym punkcie końcowym Internetu lub adresu IP w sieci wirtualnej. Aby ustawić adres IP adres sieci wirtualnej należy wdrożyć środowisko ASE przy użyciu wewnętrznego Balancer(ILB) obciążenia. Po skonfigurowaniu środowiska ASE z wewnętrznym modułem równoważenia obciążenia zawierają:

* własne domeny lub poddomeny. Ułatwia, w tym dokumencie przyjęto założenie, domeny podrzędnej, ale można skonfigurować w obu przypadkach. 
* certyfikat używany do obsługi protokołu HTTPS
* Zarządzanie usługą DNS dla swojej domeny podrzędnej. 

Umożliwia to realizowanie m.in. następujących zadań:

* hostowanie aplikacji intranetowych, takich jak aplikacje line-of-business, bezpieczne w chmurze, które są dostępne za pośrednictwem witryny do witryny lub usługi ExpressRoute sieci VPN
* hostowanie aplikacji w chmurze, które nie są wymienione na publicznych serwerach DNS
* Twórz aplikacje zaplecza internetowego samodzielnie za pomocą których można bezpiecznie integrować aplikacje frontonu

#### <a name="disabled-functionality"></a>Funkcje wyłączone
Istnieje kilka kwestii, które nie możesz zrobić w przypadku korzystania z ASE z wewnętrznym modułem równoważenia obciążenia. Czynności te obejmują:

* za pomocą IPSSL
* Przypisywanie adresów IP do określonych aplikacji
* kupowanie i używa certyfikatu z aplikacją za pośrednictwem portalu. Można oczywiście nadal uzyskać bezpośrednio z urzędu certyfikacji certyfikatów i go używać razem z aplikacjami, ale nie przy użyciu witryny Azure portal.

## <a name="creating-an-ilb-ase"></a>Tworzenie środowiska ASE z wewnętrznym modułem równoważenia obciążenia
Tworzenie środowiska ASE z wewnętrznym modułem równoważenia obciążenia nie jest znacznie różni się od tworzenia środowiska ASE normalnie. Aby uzyskać bardziej dyskusji na temat tworzenia środowiska ASE, zobacz [jak utworzyć środowisko App Service Environment][HowtoCreateASE]. Proces, aby utworzyć środowisko ASE z wewnętrznym modułem równoważenia obciążenia jest taka sama, między tworzenia sieci wirtualnej podczas tworzenia środowiska ASE lub wybranie wcześniej istniejącej sieci wirtualnej. Aby utworzyć środowisko ASE z wewnętrznym modułem równoważenia obciążenia: 

1. W witrynie Azure portal wybierz **Utwórz zasób -> Sieć Web i mobilność -> środowisko App Service Environment**.
2. Wybierz subskrypcję.
3. Wybierz lub utwórz grupę zasobów.
4. Wybierz lub utwórz sieć wirtualną.
5. Utwórz podsieć, w przypadku wybrania sieci wirtualnej.
6. Wybierz **sieć wirtualna/lokalizacja-> Konfiguracja sieci wirtualnej** i Ustaw typ adresu VIP wewnętrznego.
7. Podaj nazwę domeny podrzędnej (Ta nazwa jest poddomeny używane w przypadku aplikacji tworzonych w tym środowisku ASE).
8. Wybierz **OK** i następnie **tworzenie**.

![][1]

W okienku sieć wirtualna istnieje konfiguracja sieci wirtualnej, opcja, która umożliwia wybranie zewnętrznego lub wewnętrznego adresu VIP. Wartość domyślna to zewnętrzny. Jeśli masz ustawioną na zewnętrzne środowisko ASE używa internet dostępne wirtualne adresy IP. Jeśli wybierzesz wewnętrzne, środowisko ASE jest skonfigurowane przy użyciu wewnętrznego adresu IP w sieci wirtualnej. 

Po wybraniu wewnętrzne, możliwość dodawania większej liczby adresów IP do środowiska ASE jest usuwany, a zamiast tego należy podać domenę podrzędną środowiska ASE. W środowisku ASE z zewnętrznym wirtualnym adresem IP nazwa środowiska ASE jest używany podczas poddomeny dla aplikacji utworzonych w tym środowisku ASE. Jeśli środowisko ASE ma nazwę ***contosotest*** i nosi nazwę aplikacji w tym środowisku ASE ***testowa***, poddomeny ma format ***contosotest.p.azurewebsites.net*** i adres URL dla tej aplikacji ***mytest.contosotest.p.azurewebsites.net***. Jeśli typ adresu VIP jest ustawiona na wewnętrzne, nazwa środowiska ASE nie jest używany podczas poddomeny dla środowiska ASE. Należy jawnie określić poddomeny. Jeśli Twoje domeny podrzędnej jest ***contoso.corp.net*** i wprowadziliśmy aplikację, w tym środowisku ASE o nazwie ***raportowanieczasu***, adres URL dla tej aplikacji jest ***raportowanieczasu.contoso.corp.NET***.

## <a name="apps-in-an-ilb-ase"></a>Aplikacje w środowisku ASE z wewnętrznym modułem równoważenia obciążenia
Tworzenie aplikacji w środowisku ASE z wewnętrznym modułem równoważenia obciążenia jest taka sama, jak podczas tworzenia aplikacji w środowisku ASE normalnie. 

1. W witrynie Azure portal wybierz **Utwórz zasób -> Sieć Web i mobilność -> sieci Web** lub **Mobile** lub **aplikacji interfejsu API**.
2. Wprowadź nazwę aplikacji.
3. Wybierz subskrypcję.
4. Wybierz lub utwórz grupę zasobów.
5. Wybierz lub Utwórz Plan(ASP) usługi aplikacji. W przypadku tworzenia nowego ASP, wybierz środowisko ASE jako lokalizację, a następnie wybierz pulę procesów roboczych, które chcesz, aby strona ASP ma być utworzony w. Kiedy tworzysz ASP, możesz wybrać środowisko ASE jako lokalizację i pulę procesów roboczych. Po określeniu nazwy aplikacji, zobaczysz, że poddomeny w obszarze nazwy aplikacji zastępuje poddomeny dla środowiska ASE. 
6. Wybierz pozycję **Utwórz**. Pamiętaj o wybraniu **Przypnij do pulpitu nawigacyjnego** pole wyboru, jeśli chcesz, aby aplikacji do wyświetlenia na pulpicie nawigacyjnym. 

![][2]

W obszarze nazwy aplikacji nazwy domeny podrzędnej pobiera zaktualizowana w celu odzwierciedlenia poddomeny środowiska ASE. 

## <a name="post-ilb-ase-creation-validation"></a>Po weryfikacji tworzenia środowiska ASE z wewnętrznym modułem równoważenia obciążenia
Środowisko ASE z wewnętrznym modułem równoważenia obciążenia różni się nieco od wersji bez tego modułu. Jak już zauważyć trzeba zarządzać własną usługą DNS można też zapewnienie własnego certyfikatu do połączeń HTTPS. 

Po utworzeniu środowiska ASE, można zauważyć, że poddomeny zawiera domenę podrzędną wskazana i znajduje się nowy element w **ustawienie** menu o nazwie **certyfikat wewnętrznego modułu równoważenia obciążenia**. Środowisko ASE jest tworzony przy użyciu certyfikatu z podpisem własnym, co ułatwia przetestowanie usługi HTTPS. Portalu informuje, że konieczne jest zapewnienie własnego certyfikatu dla protokołu HTTPS, ale ma to na celu zachęcamy tego certyfikatu, która łączy się z Twojej domeny podrzędnej. 

![][3]

Jeśli są po prostu wypróbowanie rzeczy, a nie wiadomo, jak utworzyć certyfikat, można użyć aplikacji konsoli MMC programu IIS do utworzenia certyfikatu z podpisem własnym. Po jego utworzeniu należy go wyeksportować jako plik PFX, a następnie przekaż go w Interfejsie użytkownika certyfikat wewnętrznego modułu równoważenia obciążenia. Gdy uzyskujesz dostęp do witryny zabezpieczonej za pomocą certyfikatu z podpisem własnym, przeglądarka wyświetla ostrzeżenie, witryny, do której chcesz uzyskać dostęp nie jest bezpieczny ze względu na brak możliwości weryfikacji certyfikatu. Jeśli chcesz uniknąć tego ostrzeżenia, należy poprawnie podpisane certyfikat, który pasuje do Twojej domeny podrzędnej i ma łańcuch zaufania, który jest rozpoznawany przez przeglądarkę.

![][6]

Jeśli chcesz spróbować uruchomić przepływ przy użyciu własne certyfikaty i przetestować protokołów HTTP i HTTPS dostęp do środowiska ASE:

1. Przejdź do interfejsu użytkownika środowiska ASE, po utworzeniu środowiska ASE **ASE -> Ustawienia -> Certyfikaty wewnętrznego modułu równoważenia obciążenia**.
2. Ustaw certyfikat wewnętrznego modułu równoważenia obciążenia, wybierając plik pfx certyfikatu, a następnie podaj hasło. Może to zająć pewien czas przetwarzania i pojawi się komunikat, że operacja skalowania jest w toku.
3. Uzyskaj adres wewnętrznego modułu równoważenia obciążenia środowiska ASE (**ASE -> Właściwości -> wirtualnego adresu IP**).
4. Tworzenie aplikacji internetowej w środowisku ASE, po utworzeniu. 
5. Tworzenie maszyny Wirtualnej, jeśli nie masz w tej sieci Wirtualnej (nie w tej samej podsieci przerwania środowiska ASE lub rzeczy).
6. Ustaw DNS dla swojej domeny podrzędnej. Można użyć symbolu wieloznacznego Twojej domeny podrzędnej w systemie DNS lub jeśli chcesz przeprowadzić proste testy, Edytuj plik hosts na maszynie Wirtualnej, aby ustawić nazwę aplikacji internetowej Wirtualnego adresu IP. Jeśli środowisko ASE ma nazwę poddomeny. aseilb.com i wprowadzono MojaAplikacja aplikacji sieci web, dzięki czemu będą podejmowane na MojaAplikacja.aseilb.com, zestawu w pliku hosts. (W Windows, w pliku hosts wynosi C:\Windows\System32\drivers\etc\)
7. Korzystanie z przeglądarki na tej maszynie Wirtualnej i przejdź do https://mytestapp.ilbase.com (lub niezależnie od nazwy aplikacji sieci web za pomocą usługi poddomeny).
8. Skorzystaj z przeglądarki na tej maszynie wirtualnej i przejdź na stronę https://mytestapp.ilbase.com. Jeśli przy użyciu certyfikatu z podpisem własnym, należy zaakceptować Brak zabezpieczeń. 

Adres IP dla swojej wewnętrznego modułu równoważenia obciążenia znajduje się we właściwościach jako wirtualny adres IP.

![][4]

## <a name="using-an-ilb-ase"></a>Przy użyciu środowiska ASE wewnętrznego modułu równoważenia obciążenia
#### <a name="network-security-groups"></a>Grupy zabezpieczeń sieci
Środowisko ASE z wewnętrznym modułem równoważenia obciążenia umożliwia izolację sieci dla aplikacji. Aplikacje nie są dostępne lub nawet znane przez internet. To podejście jest doskonała do hostowania witryn intranetowych, takich jak aplikacje line-of-business. Gdy musisz ograniczyć dostęp do jeszcze bardziej, możesz nadal używać Groups(NSGs) zabezpieczeń sieci do kontroli dostępu na poziomie sieci. 

Jeśli chcesz bardziej ograniczyć dostęp przy użyciu sieciowych grup zabezpieczeń, upewnij się, że nie zrywasz komunikacji, że środowisko ASE wymaga, aby mogła działać. Mimo że dostępu HTTP/HTTPS jest wyłącznie za pośrednictwem używane przez środowisko ASE wewnętrznego modułu równoważenia obciążenia, środowisko ASE nadal zależy od zasobów spoza sieci wirtualnej. Aby zobaczyć, jakie dostępu do sieci jest nadal wymagane, zobacz [kontrolowanie ruch przychodzący w środowisku usługi App Service] [ ControlInbound] i [szczegóły konfiguracji sieci dla środowisk usługi App Service przy użyciu Usługa ExpressRoute][ExpressRoute]. 

Aby skonfigurować sieciowych grup zabezpieczeń, trzeba znać adres IP, który jest używany przez platformę Azure do zarządzania środowiska ASE. Ten adres IP jest również adres IP ruchu wychodzącego ze środowiska ASE, jeśli kieruje żądania internetowe. Adres IP ruchu wychodzącego dla środowiska ASE pozostanie statyczny dla cyklu życia środowiska ASE. Jeśli usuniesz, a następnie ponownie utworzyć środowisko ASE, zostanie wyświetlony nowy adres IP. Aby znaleźć adres IP, przejdź do **Ustawienia -> właściwości** i Znajdź **wychodzący adres IP**. 

![][5]

#### <a name="general-ilb-ase-management"></a>Zarządzanie ogólne środowisko ASE z wewnętrznym modułem równoważenia obciążenia
Zarządzanie środowisko ASE z wewnętrznym modułem równoważenia obciążenia są w większości takie same, jak zwykle Zarządzanie środowisko ASE. Musisz przeprowadzać skalowanie w górę puli procesów roboczych do obsługi większej liczby wystąpień ASP, a następnie skalować w górę swoje serwerów frontonu w celu obsługi zwiększonej ilości ruchu HTTP/HTTPS. Aby uzyskać ogólne informacje na temat zarządzania konfiguracją środowisko ASE, zobacz [Konfigurowanie środowiska usługi App Service][ASEConfig]. 

Elementy dodatkowe zarządzanie to zarządzanie certyfikatami i zarządzania usługą DNS. Należy uzyskać i przekazać certyfikat używany do obsługi protokołu HTTPS, po utworzeniu środowiska ASE z wewnętrznym modułem równoważenia obciążenia i zastąp go przed jego wygaśnięciem. Ponieważ platformy Azure jest właścicielem domenę podstawową, jej udostępniania certyfikatów dla środowiska ASE z zewnętrznym wirtualnym adresem IP. Ponieważ może to być dowolna poddomena używane przez środowisko ASE z wewnętrznym modułem równoważenia obciążenia, musisz podać swój własny certyfikat dla protokołu HTTPS. 

#### <a name="dns-configuration"></a>Konfiguracja DNS
Korzystając z zewnętrznym wirtualnym adresem IP, usługi DNS jest zarządzane przez platformę Azure. Każda aplikacja utworzona w środowisku ASE jest automatycznie dodawana do usługi Azure DNS, która jest publiczną usługą DNS. W środowisku ASE z wewnętrznym modułem równoważenia obciążenia musisz zarządzać własną usługą DNS. Dla danej domeny podrzędnej, takich jak contoso.corp.net należy utworzyć A systemu DNS rekordy, które wskazują na adres wewnętrznego modułu równoważenia obciążenia dla:

    * 
    publikowanie *.SCM ftp 


## <a name="getting-started"></a>Wprowadzenie
Aby rozpocząć pracę przy użyciu środowisk usługi App Service, zobacz [wprowadzenie do środowisk App Service Environment][WhatisASE]

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
