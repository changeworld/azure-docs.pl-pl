---
title: Zagadnienia dotyczące sieci, za pomocą środowiska usługi App Service — platformy Azure
description: Wyjaśnia, ruch sieciowy środowiska ASE i jak ustawić sieciowe grupy zabezpieczeń i tras zdefiniowanych przez użytkownika za pomocą środowiska ASE
services: app-service
documentationcenter: na
author: ccompy
manager: stefsch
ms.assetid: 955a4d84-94ca-418d-aa79-b57a5eb8cb85
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/31/2019
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: 3f80f3c6be747cf84aa9d8b2c386c0568a7511ad
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "67069378"
---
# <a name="networking-considerations-for-an-app-service-environment"></a>Informacje dotyczące sieci środowiska App Service Environment #

## <a name="overview"></a>Przegląd ##

 Azure [środowiska App Service Environment] [ Intro] to wdrożenie usługi Azure App Service w podsieci sieci wirtualnej platformy Azure (VNet). Istnieją dwa typy wdrożenia dla środowiska usługi App Service (ASE):

- **Zewnętrzne środowisko ASE**: Przedstawia aplikacje hostowane środowisko ASE na adres IP dostępne za pośrednictwem Internetu. Aby uzyskać więcej informacji, zobacz [Tworzenie zewnętrznego środowiska ASE][MakeExternalASE].
- **ŚRODOWISKO ASE Z WEWNĘTRZNYM MODUŁEM RÓWNOWAŻENIA OBCIĄŻENIA**: Przedstawia aplikacje hostowane środowisko ASE na adres IP wewnątrz sieci wirtualnej. Wewnętrzny punkt końcowy jest wewnętrzny moduł równoważenia obciążenia (ILB), dlatego jest określana mianem środowisko ASE z wewnętrznym modułem równoważenia obciążenia. Aby uzyskać więcej informacji, zobacz [tworzenia i używania środowiska ASE z wewnętrznym modułem równoważenia obciążenia][MakeILBASE].

Środowiska ASE, zewnętrzne i wewnętrznego modułu równoważenia obciążenia, należy mieć publicznych adresów VIP, która jest używana dla ruchu przychodzącego zarządzania oraz z adresu podczas nawiązywania połączeń ze środowiska ASE z Internetem. Wywołania z ASE, które bardziej szczegółowo w Internecie należy pozostawić sieci wirtualnej za pomocą adresów VIP przypisanych do środowiska ASE. Publiczny adres IP to adres VIP jest źródłowy adres IP dla wszystkich wywołań ze środowiska ASE, które bardziej szczegółowo w Internecie. Jeśli aplikacje w środowisku ASE wywołań do zasobów w sieci wirtualnej lub sieci VPN, źródłowy adres IP jest jeden z adresów IP w podsieci używana przez środowisko ASE. Ponieważ środowisko ASE znajduje się w sieci wirtualnej, można także przejść zasobów w ramach sieci wirtualnej bez przeprowadzania dodatkowej konfiguracji. Jeśli sieć wirtualna jest połączona z siecią lokalną, aplikacje w środowisku ASE również mieć dostęp do zasobów istnieje bez dodatkowej konfiguracji.

![Zewnętrznego środowiska ASE][1] 

W przypadku zewnętrznego środowiska ASE publicznych adresów VIP jest również punkt końcowy, który aplikacji środowiska ASE, rozwiązania dla:

* HTTP/S 
* FTP/S
* Wdrażanie w Internecie
* Debugowanie zdalne

![ŚRODOWISKO ASE Z WEWNĘTRZNYM MODUŁEM RÓWNOWAŻENIA OBCIĄŻENIA][2]

Jeśli masz środowisko ASE z wewnętrznym modułem równoważenia obciążenia, adres adres wewnętrznego modułu równoważenia obciążenia jest punkt końcowy dla protokołu HTTP/S, FTP/S, wdrażanie w Internecie i zdalne debugowanie.

## <a name="ase-subnet-size"></a>Rozmiar podsieci środowiska ASE ##

Nie można zmienić rozmiaru podsieci używanej do hostowania środowiska ASE po jego wdrożeniu.  Środowisko ASE używa adresu dla każdej infrastruktury roli oraz jak w przypadku każdego wystąpienia planu izolowanej usługi App Service.  Ponadto są pięciu adresów używanych przez sieci platformy Azure dla każdej podsieci, który jest tworzony.  Środowisko ASE z żadnych planów usługi App Service w ogóle będzie używać 12 adresów przed przystąpieniem do tworzenia aplikacji.  Jeśli środowisko ASE z wewnętrznym modułem równoważenia obciążenia, następnie zostanie użyty 13 adresów przed przystąpieniem do tworzenia aplikacji w tym środowisku ASE. Podczas skalowania środowiska ASE rolami infrastruktury zostaną dodane co wielokrotnością 15 i 20 wystąpień planu usługi App Service.

   > [!NOTE]
   > W tej podsieci, ale środowisko ASE można nic innego. Pamiętaj wybrać przestrzeń adresów, który pozwala na przyszły rozwój. Nie można zmienić tego ustawienia później. Firma Microsoft zaleca rozmiar `/24` za pomocą 256 adresów.

Skalowanie w górę lub w dół, są dodawane nowe role odpowiedni rozmiar, a następnie obciążenia są migrowane z bieżącego rozmiaru do rozmiaru docelowego. Oryginalne maszyny wirtualne usunięte tylko wtedy, gdy zostały poddane migracji obciążeń. Jeśli masz środowisko ASE przy użyciu 100 wystąpień ASP, istniała okres, w których należy podwoić liczbę maszyn wirtualnych.  To z tego powodu, które firma Microsoft zaleca korzystanie z "/ 24" Aby uwzględnić zmiany, które mogą być wymagane.  

## <a name="ase-dependencies"></a>Zależności środowiska ASE ##

### <a name="ase-inbound-dependencies"></a>Środowisko ASE zależności dla ruchu przychodzącego ###

Tylko dla środowiska ASE działanie środowiska ASE wymaga być otwarte poniższe porty:

| Użycie | Od | Do |
|-----|------|----|
| Zarządzanie | Adresy zarządzania usługi aplikacji | Podsieci środowiska ASE: 454, 455 |
|  Środowisko ASE wewnętrznej komunikacji | Podsieci środowiska ASE: Wszystkie porty | Podsieci środowiska ASE: Wszystkie porty
|  Zezwalaj na moduł równoważenia obciążenia platformy Azure dla ruchu przychodzącego | Moduł równoważenia obciążenia platformy Azure | Podsieci środowiska ASE: 16001

Istnieją 2 porty, które można wyświetlić jako otwarty na skanowanie portów, 7654 i 1221. Ich odpowiedzi z adresu IP i od niczego więcej. Może zostać zablokowany w razie potrzeby. 

Ruch przychodzący zarządzania zapewnia poleceń i kontroli środowiska ASE, oprócz systemu monitorowania. Adresy źródła dla tego ruchu są wymienione w [adresy zarządzania środowiska ASE] [ ASEManagement] dokumentu. Konfiguracja zabezpieczeń sieci musi zezwolić na dostęp z adresy zarządzania środowiska ASE na portach 454 i 455. Jeśli zablokujesz dostęp z tych adresów, Twoje środowisko ASE przestanie złej kondycji i następnie wstrzymane. Ruch TCP, których można użyć w na portach 454 i 455 musi przejść z tego samego adresu VIP lub będzie mieć problemu z routingiem asymetrycznym. 

W obrębie podsieci środowiska ASE są wielu portów używany do komunikacji z wewnętrznych składników i mogą zmieniać. Wymaga to wszystkie porty w podsieci środowiska ASE, które mają być dostępne z podsieci środowiska ASE. 

Do komunikacji między równoważenia obciążenia platformy Azure oraz podsieci środowiska ASE niezbędne porty, które muszą być otwarte są 454 i 455 16001. 16001 port jest używany keep alive ruchu między modułu równoważenia obciążenia i środowisko ASE. Jeśli używasz środowisko ASE z wewnętrznym modułem równoważenia obciążenia, a następnie zablokować ruch w dół, aby po prostu 454, 455, 16001 portów.  Jeśli używasz zewnętrznego środowiska ASE, należy wziąć pod uwagę porty dostępu normalna aplikacja.  

Inne porty, które trzeba należmy są porty aplikacji:

| Użycie | Porty |
|----------|-------------|
|  HTTP/HTTPS  | 80, 443 |
|  FTP/FTPS    | 21, 990, 10001-10020 |
|  Visual Studio zdalne debugowanie  |  4020, 4022, 4024 |
|  Wdrażanie usługi sieci Web | 8172 |

Jeśli zablokujesz porty aplikacji, środowiska ASE mogą nadal działać, ale aplikacja nie może być.  Jeśli używane są adresy IP przypisane aplikacji za pomocą zewnętrznego środowiska ASE, musisz zezwolić na ruch z adresów IP przypisanych do aplikacji w celu podsieci środowiska ASE na portach, wyświetlana w portalu środowiska ASE > stronę adresy IP.

### <a name="ase-outbound-dependencies"></a>Wychodzące zależności środowiska ASE ###

Na potrzeby dostępu wychodzącego środowisko ASE zależy od wielu systemów zewnętrznych. Wiele z tych zależności systemowe są definiowane przy użyciu nazw DNS i nie mapowania stały zestaw adresów IP. W związku z tym środowisko ASE wymaga dostęp ruchu wychodzącego z podsieci środowiska ASE do wszystkich zewnętrznych adresów IP na różnych portów. 

Środowisko ASE komunikuje się z dostępny adresami internetowymi na następujące porty:

| Użycie | Porty |
|-----|------|
| DNS | 53 |
| NTP | 123 |
| 8CRL, aktualizacji Windows, Linux, zależności, usług platformy Azure | 80/443 |
| Azure SQL | 1433 | 
| Monitorowanie | 12000 |

Wychodzące zależności są wymienione w dokumencie, który opisuje [Zablokowanie ruchu wychodzącego środowiska App Service Environment](./firewall-integration.md). Jeśli środowisko ASE utraci dostęp do jego zależności, przestanie działać. Jeśli tak się stanie wystarczająco długi, środowisko ASE jest zawieszone. 

### <a name="customer-dns"></a>Klient DNS ###

Jeśli sieć wirtualna jest skonfigurowany na serwerze DNS przez klienta, obciążenia dzierżaw go użyć. Środowisko ASE używa usługi Azure DNS do celów zarządzania. Jeśli sieć wirtualna jest skonfigurowana na serwerze DNS klient wybrany, serwer DNS musi być dostępny z poziomu podsieci zawierającej środowisko ASE.

Aby sprawdzić rozpoznawanie nazw DNS z aplikacją sieci web, można użyć polecenia konsoli *nameresolver*. Przejdź do okna debugowania witryny funkcji scm aplikacji lub przejdź do aplikacji w portalu i wybierz pozycję Konsola. W wierszu polecenia powłoki można wydać polecenie *nameresolver* wraz z nazwą DNS, które chcesz wyszukać. Wynik, wracając jest taka sama jak aplikacja otrzymamy podczas wprowadzania tych samych wyszukiwania. Jeśli używasz nslookup, wykonasz wyszukiwania, zamiast tego używanie usługi Azure DNS.

Jeśli zmienisz ustawienia DNS sieci wirtualnej, należącym do środowiska ASE, konieczne będzie ponowne uruchomienie środowiska ASE. Aby uniknąć ponownego uruchomienia środowiska ASE, zdecydowanie zaleca się skonfigurowania ustawień DNS dla sieci wirtualnej, przed przystąpieniem do tworzenia środowiska ASE.  

<a name="portaldep"></a>

## <a name="portal-dependencies"></a>Zależności portalu ##

Oprócz zależności funkcjonalne środowisko ASE istnieje kilka dodatkowych elementów związane z środowisko pracy w portalu. Niektóre funkcje w witrynie Azure portal są zależne od bezpośredni dostęp do _witryny SCM_. Istnieją dwa adresy URL, dla każdej aplikacji w usłudze Azure App Service. Pierwszy adres URL jest dostęp do aplikacji. Drugi adres URL jest dostęp do witryny SCM, nazywana także _konsoli Kudu_. Funkcje, które używają witryny SCM:

-   Zadania Web Job
-   Funkcje
-   Przesyłanie strumieniowe dzienników
-   Kudu
-   Rozszerzenia
-   Eksplorator procesów
-   Konsola

Gdy używasz środowisko ASE z wewnętrznym modułem równoważenia obciążenia, witryny SCM nie jest dostępne spoza sieci wirtualnej. Niektóre funkcje nie będą działać z portalu aplikacji, ponieważ wymagają one dostęp do witryny SCM aplikacji. Możesz połączyć się z lokacją SCM bezpośrednio zamiast przy użyciu portalu. 

Jeśli nazwa domeny środowiska ASE wewnętrznego modułu równoważenia obciążenia *contoso.appserviceenvironnment.net* i Twoja nazwa aplikacji jest *testapp*, aplikacja zostanie osiągnięty w *testapp.contoso.appserviceenvironment.net*. Witryny SCM, która łączy się z jej zostanie osiągnięty w *testapp.scm.contoso.appserviceenvironment.net*.

## <a name="ase-ip-addresses"></a>Adresy IP środowiska ASE ##

Środowisko ASE ma kilka adresów IP, pod uwagę. Oto one:

- **Publiczny adres IP dla ruchu przychodzącego**: Używany do ruchu aplikacji w zewnętrznym środowiskiem ASE i ruch związany z zarządzaniem w zewnętrznym środowiskiem ASE i środowisko ASE z wewnętrznym modułem równoważenia obciążenia.
- **Wychodzące publiczny adres IP**: Używane jako "od" adres IP dla połączeń wychodzących ze środowiska ASE, które należy pozostawić sieci wirtualnej, które nie są kierowane szczegółów sieci VPN.
- **Adres IP wewnętrznego modułu równoważenia obciążenia**: Adres IP wewnętrznego modułu równoważenia obciążenia istnieje tylko w przypadku środowiska ASE z wewnętrznym modułem równoważenia obciążenia.
- **Aplikacja przypisana SSL opartego na protokole IP adresów**: Możliwe tylko za pomocą zewnętrznego środowiska ASE i po skonfigurowaniu SSL opartego na protokole IP.

Te adresy IP są widoczne w witrynie Azure portal z poziomu interfejsu użytkownika środowiska ASE. Jeśli masz środowisko ASE z wewnętrznym modułem równoważenia obciążenia, znajduje się adres IP dla wewnętrznego modułu równoważenia obciążenia.

   > [!NOTE]
   > Te adresy IP nie ulegnie zmianie, tak długo, jak środowisko ASE pozostaje uruchomiona.  Jeśli środowisko ASE staje się wstrzymane, a następnie przywrócić, adresy, używane przez środowisko ASE zostanie zmieniona. Normalne przyczynę wstrzymane środowisko ASE wewnętrznego jest możesz zablokować dostęp do funkcji zarządzania dla ruchu przychodzącego lub zablokować dostęp do zależności środowiska ASE. 

![Adresy IP][3]

### <a name="app-assigned-ip-addresses"></a>Adresy IP przypisane do aplikacji ###

Za pomocą zewnętrznego środowiska ASE adresów IP można przypisać do poszczególnych aplikacji. Nie możesz tego zrobić za pomocą środowisko ASE z wewnętrznym modułem równoważenia obciążenia. Aby uzyskać więcej informacji na temat konfigurowania aplikacji, aby mieć swój własny adres IP, zobacz [powiązania istniejącego niestandardowego certyfikatu SSL w usłudze Azure App Service](../app-service-web-tutorial-custom-ssl.md).

Gdy aplikacja ma własny adres SSL opartego na protokole IP, środowisko ASE zastrzega sobie dwa porty do mapowania na ten adres IP. Jeden port jest dla ruchu HTTP i innych portów to dla protokołu HTTPS. Te porty są wyświetlane w interfejsie użytkownika środowiska ASE w sekcji adresów IP. Ruch musi być w stanie nawiązać połączenie z adresu VIP tych portów lub aplikacje są niedostępne. Ten wymóg jest pamiętać podczas konfigurowania sieciowych grup zabezpieczeń (NSG).

## <a name="network-security-groups"></a>Grupy zabezpieczeń sieci ##

[Sieciowe grupy zabezpieczeń] [ NSGs] zapewniają możliwość kontrolowania dostępu do sieci w sieci wirtualnej. Korzystając z portalu, ma regułę Odmów niejawne pod najniższy priorytet, Odmów wszystkiego. Możesz tworzyć są z reguły zezwalania.

W przypadku środowiska ASE nie masz dostępu do maszyn wirtualnych, używane do hostowania środowiska ASE, sam. Są one w ramach subskrypcji zarządzanych przez firmę Microsoft. Jeśli chcesz ograniczyć dostęp do aplikacji w środowisku ASE, należy ustawić sieciowych grup zabezpieczeń w podsieci środowiska ASE. W ten sposób zapłaty uważnego zależności środowiska ASE. Jeśli zablokujesz jakieś zależności, środowisko ASE przestanie działać.

Sieciowe grupy zabezpieczeń można skonfigurować za pomocą witryny Azure portal lub za pośrednictwem programu PowerShell. W tym miejscu informacje przedstawia witryny Azure portal. Tworzenie i zarządzanie sieciowymi grupami zabezpieczeń w portalu jako zasobem najwyższego poziomu w ramach **sieć**.

Są odpowiednie wpisy w sieciowej grupie zabezpieczeń, za środowisko ASE, funkcji, aby zezwolić na ruch:

**Dla ruchu przychodzącego**
* z adresu IP tag AppServiceManagement na portach 454,455 usługi
* z modułu równoważenia obciążenia na porcie 16001
* z podsieci środowiska ASE do podsieci środowiska ASE na wszystkich portach

**Wychodzące**
* dla wszystkich adresów IP na port 123
* dla wszystkich adresów IP na portach 80, 443
* do adresu IP tag AzureSQL porty 1433 usługi
* dla wszystkich adresów IP na porcie 12000
* do podsieci środowiska ASE na wszystkich portach

DNS port nie musi być dodawane jako ruchu sieciowego w systemie DNS nie ma wpływu reguły sieciowej grupy zabezpieczeń. Te porty są uwzględniane portów, które aplikacje wymagają do pomyślnego użycia. Dostępne są następujące porty dostępu zwykła aplikacja:

| Użycie | Porty |
|----------|-------------|
|  HTTP/HTTPS  | 80, 443 |
|  FTP/FTPS    | 21, 990, 10001-10020 |
|  Visual Studio zdalne debugowanie  |  4020, 4022, 4024 |
|  Wdrażanie usługi sieci Web | 8172 |

Gdy wymagania dotyczące ruchu przychodzącego i wychodzącego są brane pod uwagę, sieciowe grupy zabezpieczeń powinien wyglądać podobnie do sieciowych grup zabezpieczeń, w poniższym przykładzie. 

![Reguły zabezpieczeń dla ruchu przychodzącego][4]

Domyślna reguła umożliwia adresów IP w sieci wirtualnej na komunikowanie się z podsieci środowiska ASE. Inną regułę domyślną umożliwia równoważenia obciążenia, znany także jako publicznych adresów VIP do komunikowania się z ASE. Aby wyświetlić domyślne reguły, wybierz **domyślne reguły** obok **Dodaj** ikony. Jeśli umieścisz wszystkie inne reguły przed domyślnych reguł odmowy, uniemożliwia ruchu między adres VIP i środowisko ASE. Aby zapobiec ruchu przychodzącego z wewnątrz sieci wirtualnej, należy dodać własne reguły w celu umożliwienia ruchu przychodzącego. Korzystanie ze źródła, które są równe AzureLoadBalancer z miejsca docelowego **wszelkie** i zakres portów **\*** . Ponieważ reguła sieciowej grupy zabezpieczeń jest stosowana do podsieci środowiska ASE, trzeba należeć do określonego miejsca docelowego.

Jeśli adres IP przypisany do aplikacji, upewnij się, że porty pozostawić otwarty. Aby zobaczyć porty, wybierz **środowiska App Service Environment** > **adresów IP**.  

Wszystkie elementy, które są wyświetlane w następujących reguł ruchu wychodzącego są potrzebne, z wyjątkiem ostatniego elementu. Umożliwiają one dostęp sieciowy do zależności środowiska ASE, które zostały podane wcześniej w tym artykule. Jeśli zablokujesz któryś z nich Twoje środowisko ASE przestanie działać. Ostatni element na liście umożliwia środowiska ASE do komunikowania się z innymi zasobami w sieci wirtualnej.

![Reguły zabezpieczeń dla ruchu wychodzącego][5]

Po zdefiniowaniu sieciowych grup zabezpieczeń, należy je przypisać do podsieci, która znajduje się w środowisku ASE. Jeśli nie pamiętasz środowiska ASE w sieci wirtualnej lub podsieci, można go było wyświetlić na stronie portalu środowiska ASE. Aby przypisać sieciowej grupy zabezpieczeń do podsieci, przejdź do podsieci interfejsu użytkownika, a następnie wybierz sieciową grupę zabezpieczeń.

## <a name="routes"></a>Trasy ##

Wymuszone tunelowanie ma miejsce podczas skonfigurować trasy w sieci wirtualnej, dzięki czemu ruch wychodzący nie przejdź bezpośrednio do Internetu, ale gdzie indziej, takie jak brama usługi ExpressRoute lub urządzenie wirtualne.  Jeśli musisz skonfigurować środowiska ASE w taki sposób, na następnie przeczytaj dokument [Konfigurowanie środowiska App Service Environment przy wymuszonego tunelowania][forcedtunnel].  W tym dokumencie poinformuje, dostępne opcje Aby pracować z usługi ExpressRoute i wymuszonym tunelowaniem.

Po utworzeniu środowiska ASE w witrynie portal musimy również utworzyć zbiór tabel tras w podsieci, która jest tworzona przy użyciu środowiska ASE.  Te trasy po prostu, że na wysyłanie ruchu wychodzącego bezpośrednio do Internetu.  
Aby ręcznie utworzyć ten sam tras, wykonaj następujące kroki:

1. Przejdź do witryny Azure Portal. Wybierz **sieć** > **tabele tras**.

2. Utwórz nową tabelę tras w tym samym regionie, co sieć wirtualna.

3. W ramach tabeli tras interfejsu użytkownika, zaznacz **trasy** > **Dodaj**.

4. Ustaw **typu następnego przeskoku** do **Internet** i **prefiksu adresu** do **0.0.0.0/0**. Wybierz pozycję **Zapisz**.

    Zostanie wyświetlony podobny do poniższego:

    ![Trasy funkcjonalności][6]

5. Po utworzeniu nowej tabeli tras, przejdź do podsieci, która zawiera środowiska ASE. Wybieranie tabeli tras z listy w portalu. Po zapisaniu zmian, następnie powinny sieciowe grupy zabezpieczeń i tras oznaczane przy użyciu podsieci.

    ![Sieciowe grupy zabezpieczeń i trasy][7]

## <a name="service-endpoints"></a>Punkty końcowe usługi ##

Punkty końcowe usługi pozwalają ograniczyć dostęp do usług wielodostępnych do zestawu sieci wirtualnych i podsieci platformy Azure. Więcej informacji na temat punktów końcowych usługi zawiera dokumentacja [punktów końcowych usługi dla sieci wirtualnej][serviceendpoints]. 

Po włączeniu punktów końcowych usługi w zasobie niektóre trasy są utworzone z wyższym priorytetem niż wszystkie inne trasy. Jeśli używasz punktów końcowych usługi dowolnej usługi platformy Azure, przy wymuszonego tunelowania środowiska ASE, ruch do tych usług nie można wymusić tunelowania. 

Kiedy punkty końcowe usługi są włączone w podsieci z wystąpieniem usługi Azure SQL, wszystkie wystąpienia usługi Azure SQL połączone z tej podsieci muszą mieć włączone punkty końcowe usługi. Jeśli chcesz uzyskać dostęp do wielu wystąpień usługi Azure SQL z tej samej podsieci, musisz włączyć punkty końcowe usługi na każdym wystąpieniu usługi Azure SQL. Inne usługi platformy Azure zachowuje się jak Azure SQL w odniesieniu do punktów końcowych usługi. Włączenie punktów końcowych usługi w usłudze Azure Storage powoduje zablokowanie dostępu do tego zasobu z podsieci, ale można nadal uzyskiwać dostęp do innych kont usługi Azure Storage, nawet jeśli nie mają włączonych punktów końcowych usługi.  

![Punkty końcowe usługi][8]

<!--Image references-->
[1]: ./media/network_considerations_with_an_app_service_environment/networkase-overflow.png
[2]: ./media/network_considerations_with_an_app_service_environment/networkase-overflow2.png
[3]: ./media/network_considerations_with_an_app_service_environment/networkase-ipaddresses.png
[4]: ./media/network_considerations_with_an_app_service_environment/networkase-inboundnsg.png
[5]: ./media/network_considerations_with_an_app_service_environment/networkase-outboundnsg.png
[6]: ./media/network_considerations_with_an_app_service_environment/networkase-udr.png
[7]: ./media/network_considerations_with_an_app_service_environment/networkase-subnet.png
[8]: ./media/network_considerations_with_an_app_service_environment/serviceendpoint.png

<!--Links-->
[Intro]: ./intro.md
[MakeExternalASE]: ./create-external-ase.md
[MakeASEfromTemplate]: ./create-from-template.md
[MakeILBASE]: ./create-ilb-ase.md
[ASENetwork]: ./network-info.md
[UsingASE]: ./using-an-ase.md
[UDRs]: ../../virtual-network/virtual-networks-udr-overview.md
[NSGs]: ../../virtual-network/security-overview.md
[ConfigureASEv1]: app-service-web-configure-an-app-service-environment.md
[ASEv1Intro]: app-service-app-service-environment-intro.md
[mobileapps]: ../../app-service-mobile/app-service-mobile-value-prop.md
[Functions]: ../../azure-functions/index.yml
[Pricing]: https://azure.microsoft.com/pricing/details/app-service/
[ARMOverview]: ../../azure-resource-manager/resource-group-overview.md
[ConfigureSSL]: ../web-sites-purchase-ssl-web-site.md
[Kudu]: https://azure.microsoft.com/resources/videos/super-secret-kudu-debug-console-for-azure-web-sites/
[ASEWAF]: app-service-app-service-environment-web-application-firewall.md
[AppGW]: ../../application-gateway/application-gateway-web-application-firewall-overview.md
[ASEManagement]: ./management-addresses.md
[serviceendpoints]: ../../virtual-network/virtual-network-service-endpoints-overview.md
[forcedtunnel]: ./forced-tunnel-support.md
[serviceendpoints]: ../../virtual-network/virtual-network-service-endpoints-overview.md
