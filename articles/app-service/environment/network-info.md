---
title: Zagadnienia dotyczące pracy w sieci
description: Dowiedz się więcej o ruchu sieciowym ASE i ustawianiu sieciowych grup zabezpieczeń i tras zdefiniowanych przez użytkownika za pomocą środowiska ASE.
author: ccompy
ms.assetid: 955a4d84-94ca-418d-aa79-b57a5eb8cb85
ms.topic: article
ms.date: 01/24/2020
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: fb931c309b5f85902d8abc9cc6da45576bff4041
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79259827"
---
# <a name="networking-considerations-for-an-app-service-environment"></a>Networking considerations for an App Service Environment (Zagadnienia dotyczące sieci w środowisku App Service Environment) #

## <a name="overview"></a>Omówienie ##

 Środowisko usługi Azure [App Service][Intro] to wdrożenie usługi Azure App Service w podsieci w sieci wirtualnej platformy Azure. Istnieją dwa typy wdrożeń dla środowiska usługi app service (ASE):

- **Zewnętrzny ASE:** Udostępnia aplikacje hostowane przez ASE na adres IP dostępny w Internecie. Aby uzyskać więcej informacji, zobacz [Tworzenie zewnętrznego ASE][MakeExternalASE].
- **ILB ASE:** Udostępnia aplikacje hostowane przez ASE na adres IP wewnątrz sieci wirtualnej. Wewnętrzny punkt końcowy jest wewnętrznym modułem równoważenia obciążenia (ILB), dlatego jest nazywany ASE równoważenia obciążenia ilb. Aby uzyskać więcej informacji, zobacz [Tworzenie i używanie ase ILB][MakeILBASE].

Wszystkie ases, zewnętrzne i ILB, mają publiczny adres VIP, który jest używany do ruchu zarządzania przychodzącego i jako adres od podczas nawiązywać połączenia z ASE do Internetu. Wywołania z ASE, które przechodzą do Internetu opuścić sieci wirtualnej za pośrednictwem adresu VIP przypisany do ASE. Publiczny adres IP tego adresu VIP jest źródłowym ip dla wszystkich połączeń z ASE, które przechodzą do Internetu. Jeśli aplikacje w ase nawiązują połączenia z zasobami w sieci wirtualnej lub w sieci VPN, źródłowy adres IP jest jednym z adresów IP w podsieci używanej przez firmę ASE. Ponieważ ase znajduje się w sieci wirtualnej, można również uzyskać dostęp do zasobów w sieci wirtualnej bez dodatkowej konfiguracji. Jeśli sieć wirtualna jest połączona z siecią lokalną, aplikacje w środowisku ASE również mają tam dostęp do zasobów bez dodatkowej konfiguracji.

![Zewnętrzna ASE][1] 

Jeśli masz zewnętrznego ASE, publiczny adres VIP jest również punktem końcowym, który aplikacje ASE rozwiązać dla:

* Protokół HTTP/S 
* FTP/S
* Wdrażanie w sieci Web
* Debugowanie zdalne

![ILB ASE][2]

Jeśli masz ASE równoważenia obciążenia, adres adresu równoważenia obciążenia sieciowego jest punktem końcowym dla HTTP/S, FTP/S, wdrożenia sieci web i zdalnego debugowania.

## <a name="ase-subnet-size"></a>Rozmiar podsieci ASE ##

Nie można zmienić rozmiaru podsieci używanej do hosta ase po wdrożeniu ase.  ASE używa adresu dla każdej roli infrastruktury, jak również dla każdego wystąpienia planu izolowanej usługi aplikacji.  Ponadto istnieje pięć adresów używanych przez usługę Azure Networking dla każdej tworzonej podsieci.  Ase bez planów usługi app service w ogóle użyje 12 adresów przed utworzeniem aplikacji.  Jeśli jest to ASE równoważenia obciążenia, użyje 13 adresów przed utworzeniem aplikacji w tym ASE. Podczas skalowania w poziomie ase role infrastruktury są dodawane co wielokrotność 15 i 20 wystąpień planu usługi App Service.

   > [!NOTE]
   > Nic innego nie może być w podsieci, ale ASE. Pamiętaj, aby wybrać przestrzeń adresową, która pozwala na przyszły wzrost. Nie można później zmienić tego ustawienia. Zalecamy rozmiar `/24` z 256 adresami.

Podczas skalowania w górę lub w dół, nowe role o odpowiednim rozmiarze są dodawane, a następnie obciążenia są migrowane z bieżącego rozmiaru do rozmiaru docelowego. Oryginalne maszyny wirtualne usunięte dopiero po zmigrowaniu obciążeń. Jeśli masz ASE z 100 wystąpień ASP, nie będzie okres, w którym trzeba podwoić liczbę maszyn wirtualnych.  Z tego powodu zalecamy użycie "/24", aby uwzględnić wszelkie zmiany, które mogą być wymagane.  

## <a name="ase-dependencies"></a>Zależności ASE ##

### <a name="ase-inbound-dependencies"></a>Zależności przychodzące ASE ###

Tylko dla ASE do pracy, ASE wymaga następujących portów, które mają być otwarte:

| Użycie | Z | Do |
|-----|------|----|
| Zarządzanie | Adresy zarządzania usługą App Service | Podsieć ASE: 454, 455 |
|  Komunikacja wewnętrzna ASE | Podsieć ASE: Wszystkie porty | Podsieć ASE: Wszystkie porty
|  Zezwalaj na przychodzących modułów równoważenia obciążenia platformy Azure | Moduł równoważenia obciążenia platformy Azure | Podsieć ASE: 16001

Istnieją 2 inne porty, które mogą być wyświetlane jako otwarte podczas skanowania portu, 7654 i 1221. Odpowiadają z adresem IP i nic więcej. W razie potrzeby można je zablokować. 

Ruch zarządzania przychodzący zapewnia polecenia i kontroli ASE oprócz monitorowania systemu. Adresy źródłowe dla tego ruchu są wymienione w dokumencie [adresów ase.][ASEManagement] Konfiguracja zabezpieczeń sieci musi umożliwiać dostęp z adresów zarządzania ASE na portach 454 i 455. Jeśli zablokujesz dostęp z tych adresów, twój ase stanie się w złej kondycji, a następnie zostanie zawieszony. Ruch TCP, który jest dostępny w portach 454 i 455, musi wrócić z tego samego adresu VIP lub będziesz miał problem z routingiem asymetrycznym. 

W podsieci ASE istnieje wiele portów używanych do wewnętrznej komunikacji składników i mogą one ulec zmianie. Wymaga to, aby wszystkie porty w podsieci ASE były dostępne z podsieci ASE. 

W przypadku komunikacji między modułem równoważenia obciążenia platformy Azure a podsiecią ASE minimalne porty, które muszą być otwarte, to 454, 455 i 16001. Port 16001 służy do utrzymywanie ruchu między modułem równoważenia obciążenia a ase. Jeśli używasz ASE ILB, możesz zablokować ruch tylko do portów 454, 455, 16001.  Jeśli używasz zewnętrznego ase, a następnie należy wziąć pod uwagę normalne porty dostępu do aplikacji.  

Inne porty, które musisz się z martwić, to porty aplikacji:

| Użycie | Porty |
|----------|-------------|
|  HTTP/HTTPS  | 80, 443 |
|  FTP/FTPS    | 21, 990, 10001-10020 |
|  Zdalne debugowanie programu Visual Studio  |  4020, 4022, 4024 |
|  Usługa wdrażania w sieci Web | 8172 |

Jeśli zablokujesz porty aplikacji, twój ase może nadal działać, ale aplikacja może nie.  Jeśli używasz adresów IP przypisanych do aplikacji z zewnętrznym programem ASE, musisz zezwolić na ruch z adresów IP przypisanych do aplikacji do podsieci ASE na portach wyświetlanych na stronie > adresy IP portalu ASE.

### <a name="ase-outbound-dependencies"></a>Zależności wychodzące ASE ###

W przypadku dostępu wychodzącego system ASE zależy od wielu systemów zewnętrznych. Wiele z tych zależności systemowych jest zdefiniowanych za pomocą nazw DNS i nie jest mapowanych na stały zestaw adresów IP. W związku z tym ASE wymaga dostępu wychodzącego z podsieci ASE do wszystkich zewnętrznych usług IP w różnych portach. 

ASE komunikuje się z dostępem do Internetu adresami na następujących portach:

| Użycie | Porty |
|-----|------|
| DNS | 53 |
| NTP | 123 |
| Aktualizacje platformy CRL, windows, zależności linuksa, usługi platformy Azure | 80/443 |
| Azure SQL | 1433 | 
| Monitorowanie | 12000 |

Zależności ruchu wychodzącego są wymienione w dokumencie, który opisuje [Blokowanie ruchu wychodzącego środowiska usługi app service](./firewall-integration.md). Jeśli ASE traci dostęp do swoich zależności, przestaje działać. Gdy dzieje się to wystarczająco długo, ASE jest zawieszony. 

### <a name="customer-dns"></a>Dns klienta ###

Jeśli sieci wirtualnej jest skonfigurowany z serwerem DNS zdefiniowanym przez klienta, obciążenia dzierżawy go używać. Ase używa usługi Azure DNS do celów zarządzania. Jeśli sieci wirtualnej jest skonfigurowany z serwerem DNS wybranym przez klienta, serwer DNS musi być osiągalny z podsieci zawierającej ase.

Aby przetestować rozpoznawanie dns z aplikacji sieci web, można użyć polecenia *nameresolver*konsoli . Przejdź do okna debugowania w witrynie scm dla aplikacji lub przejdź do aplikacji w portalu i wybierz konsolę. Z monitu powłoki można wydać *nameresolver* polecenia wraz z nazwą DNS, którą chcesz wyszukać. Wynik, który odzyskasz, jest taki sam, jak to, co aplikacja otrzyma podczas wykonywania tego samego wyszukiwania. Jeśli używasz nslookup, wykonasz wyszukiwanie przy użyciu usługi Azure DNS.

Jeśli zmienisz ustawienie DNS sieci wirtualnej, w której znajduje się serwer ASE, należy ponownie uruchomić system ASE. Aby uniknąć ponownego uruchomienia środowiska ASE, zaleca się skonfigurowanie ustawień DNS sieci wirtualnej przed utworzeniem środowiska ASE.  

<a name="portaldep"></a>

## <a name="portal-dependencies"></a>Zależności portalu ##

Oprócz zależności funkcjonalnych ŚRODOWISKA ASE istnieje kilka dodatkowych elementów związanych z doświadczeniem portalu. Niektóre funkcje w witrynie Azure portal zależą od bezpośredniego dostępu do _witryny SCM._ Dla każdej aplikacji w usłudze Azure App Service istnieją dwa adresy URL. Pierwszym adresem URL jest dostęp do aplikacji. Drugi adres URL to dostęp do witryny SCM, która jest również nazywana _konsolą Kudu._ Funkcje korzystające z witryny SCM obejmują:

-   Zadania WebJob
-   Funkcje
-   Przesyłanie strumieniowe w dzienniku
-   Kudu
-   Rozszerzenia
-   Eksplorator procesów
-   Konsola

Korzystając z ase ILB, witryny SCM nie jest dostępny spoza sieci wirtualnej. Niektóre funkcje nie będą działać z portalu aplikacji, ponieważ wymagają one dostępu do witryny SCM aplikacji. Możesz połączyć się z witryną SCM bezpośrednio zamiast korzystać z portalu. 

Jeśli ase ilb jest nazwą domeny *contoso.appserviceenvironment.net* a nazwa aplikacji jest *testapp,* aplikacja jest osiągnięty w *testapp.contoso.appserviceenvironment.net*. Strona SCM, która idzie z nim jest osiągnięty w *testapp.scm.contoso.appserviceenvironment.net*.

## <a name="ase-ip-addresses"></a>Adresy IP ASE ##

ASE ma kilka adresów IP, o które należy pamiętać. Oto one:

- **Publiczny przychodzący adres IP:** używany do ruchu aplikacji w zewnętrznym ase i ruchu zarządzania zarówno w zewnętrznym ASE i ILB ASE.
- **Wychodzący publiczny adres IP:** używany jako adres IP "od" dla połączeń wychodzących z ase, które opuszczają sieć wirtualną, które nie są kierowane w dół sieci VPN.
- **Adres IP równoważenia**obciążenia obciążenia: Adres IP równoważenia obciążenia obciążenia istnieje tylko w ASE równoważenia obciążenia.
- **Adresy SSL oparte na protokona z aplikacją:** możliwe tylko w przypadku zewnętrznego ase i skonfigurowania protokołu SSL opartego na protokoi.

Wszystkie te adresy IP są widoczne w witrynie Azure portal z interfejsu użytkownika ASE. Jeśli masz ASE ILB, adres IP dla równoważenia obciążenia sieciowego jest wyświetlany.

   > [!NOTE]
   > Te adresy IP nie ulegnie zmianie, dopóki twój ASE pozostanie uruchomiony.  Jeśli twój ASE zostanie zawieszony i przywrócony, zmienią się adresy używane przez twój ASE. Normalną przyczyną zawieszenia ase jest zablokowanie dostępu do zarządzania przychodzącego lub zablokowanie dostępu do zależności ASE. 

![Adresy IP][3]

### <a name="app-assigned-ip-addresses"></a>Adresy IP przypisane do aplikacji ###

Za pomocą zewnętrznego ase można przypisywać adresy IP do poszczególnych aplikacji. Nie można tego zrobić z ASE ILB. Aby uzyskać więcej informacji na temat konfigurowania aplikacji tak, aby miała własny adres IP, zobacz [Zabezpieczanie niestandardowej nazwy DNS z powiązaniem SSL w usłudze Azure App Service](../configure-ssl-bindings.md).

Gdy aplikacja ma swój własny adres SSL oparty na protokoczy IP, ASE rezerwuje dwa porty do mapowania na ten adres IP. Jeden port jest przeznaczony dla ruchu HTTP, a drugi dla protokołu HTTPS. Te porty są wymienione w interfejsie ase w sekcji adresy IP. Ruch musi być w stanie dotrzeć do tych portów z adresu VIP lub aplikacje są niedostępne. To wymaganie należy pamiętać podczas konfigurowania sieciowych grup zabezpieczeń (NSG).

## <a name="network-security-groups"></a>Grupy zabezpieczeń sieci ##

[Sieciowe grupy zabezpieczeń][NSGs] umożliwiają kontrolowanie dostępu do sieci sieci wirtualnej. Korzystając z portalu, istnieje niejawna reguła odmowy o najniższym priorytecie, aby odmówić wszystkiego. To, co budujemy, to twoje reguły zezwalania.

W ase nie masz dostępu do maszyn wirtualnych używanych do hosta ASE się. Są one w subskrypcji zarządzanej przez firmę Microsoft. Jeśli chcesz ograniczyć dostęp do aplikacji w ase, ustaw sieciowych grup zabezpieczeń w podsieci ASE. W ten sposób należy zwrócić szczególną uwagę na zależności ASE. Jeśli zablokujesz wszelkie zależności, ASE przestanie działać.

Sieci zabezpieczeń można skonfigurować za pośrednictwem witryny Azure portal lub za pośrednictwem programu PowerShell. Informacje w tym miejscu pokazuje witryny Azure portal. Tworzenie sieciowych grup zabezpieczeń i zarządzanie nimi w portalu jest zasobem najwyższego poziomu w obszarze **Sieć**.

Wymagane wpisy w nsg, dla ASE do działania, są do umożliwienia ruchu:

**Przychodzący**
* z tagu usługi IP AppServiceManagement na portach 454 455
* z modułu równoważenia obciążenia w porcie 16001
* z podsieci ASE do podsieci ASE na wszystkich portach

**Wychodzący**
* do wszystkich usług IP na porcie 123
* do wszystkich ip na portach 80, 443
* do tagu usługi IP AzureSQL na portach 1433
* do wszystkich usług IP na porcie 12000
* do podsieci ASE na wszystkich portach

Nie trzeba dodawać portu DNS, ponieważ reguły sieciowej sieciowej nie mają wpływu na ruch do systemu DNS. Te porty nie obejmują portów, które aplikacje wymagają pomyślnego użycia. Normalne porty dostępu do aplikacji to:

| Użycie | Porty |
|----------|-------------|
|  HTTP/HTTPS  | 80, 443 |
|  FTP/FTPS    | 21, 990, 10001-10020 |
|  Zdalne debugowanie programu Visual Studio  |  4020, 4022, 4024 |
|  Usługa wdrażania w sieci Web | 8172 |

Gdy wymagania przychodzące i wychodzące są brane pod uwagę, nsgs powinny wyglądać podobnie do grup zabezpieczeń innych firm pokazano w tym przykładzie. 

![Reguły zabezpieczeń dla ruchu przychodzącego][4]

Reguła domyślna umożliwia ip w sieci wirtualnej do rozmowy z podsieci ASE. Inna reguła domyślna umożliwia modułowi równoważenia obciążenia, znane również jako publiczny adres VIP, komunikowanie się z programem ASE. Aby wyświetlić reguły domyślne, wybierz pozycję **Reguły domyślne** obok ikony **Dodaj.** Jeśli reguła odmowy wszystkiego innego zostanie umieszczona przed regułami domyślnymi, uniemożliwisz ruch między adresem VIP a programem ASE. Aby zapobiec ruch przychodzący z wewnątrz sieci wirtualnej, dodaj własną regułę, aby zezwolić na przychodzące. Użyj źródła równego AzureLoadBalancer z **Any** miejscem docelowym **\*** dowolnego i zakresu portów . Ponieważ reguła sieciowej sieciowej sieciowej jest stosowana do podsieci ASE, nie trzeba być określonym w miejscu docelowym.

Jeśli do aplikacji przypisano adres IP, upewnij się, że porty są otwarte. Aby wyświetlić porty, wybierz pozycję**Adresy IP** **środowiska** > usługi app service .  

Wszystkie elementy pokazane w następujących regułach ruchu wychodzącego są potrzebne, z wyjątkiem ostatniego elementu. Umożliwiają one dostęp do sieci do zależności ASE, które zostały odnotowane wcześniej w tym artykule. Jeśli zablokujesz którykolwiek z nich, twój ASE przestanie działać. Ostatni element na liście umożliwia ase do komunikowania się z innymi zasobami w sieci wirtualnej.

![Reguły zabezpieczeń dla ruchu wychodzącego][5]

Po zdefiniowaniu sieciowych grup zabezpieczeń przypisz je do podsieci, w których znajduje się program ASE. Jeśli nie pamiętasz sieci wirtualnej lub podsieci ASE, możesz ją zobaczyć na stronie portalu ASE. Aby przypisać sieciowej sieciowej do podsieci, przejdź do interfejsu użytkownika podsieci i wybierz numer sieciowy.

## <a name="routes"></a>Trasy ##

Wymuszone tunelowanie jest po ustawieniu tras w sieci wirtualnej, więc ruch wychodzący nie przechodzi bezpośrednio do Internetu, ale w innym miejscu, takich jak brama usługi ExpressRoute lub urządzenia wirtualnego.  Jeśli chcesz skonfigurować środowisko ASE w taki sposób, przeczytaj dokument dotyczący [Konfigurowania środowiska usługi App Service za pomocą wymuszonego tunelowania][forcedtunnel].  W tym dokumencie zostaną udostępnione opcje pracy z programem ExpressRoute i tunelowanie wymuszone.

Podczas tworzenia ase w portalu tworzymy również zestaw tabel tras w podsieci, który jest tworzony za pomocą ASE.  Te trasy po prostu powiedzieć, aby wysłać ruch wychodzący bezpośrednio do Internetu.  
Aby ręcznie utworzyć te same trasy, wykonaj następujące czynności:

1. Przejdź do witryny Azure Portal. Wybierz **tabele** > **tras sieciowych**.

2. Utwórz nową tabelę tras w tym samym regionie co sieć wirtualna.

3. Z poziomu interfejsu użytkownika tabeli tras wybierz pozycję **Trasy** > **dodaj**.

4. Ustaw **typ następnego przeskoku** na **Internet** i **prefiks adresu** na **0.0.0.0/0**. Wybierz **pozycję Zapisz**.

    Następnie zobaczysz coś takiego jak:

    ![Trasy funkcjonalne][6]

5. Po utworzeniu nowej tabeli marszruty przejdź do podsieci zawierającej program ASE. Wybierz tabelę tras z listy w portalu. Po zapisaniu zmiany należy wyświetlić sieciowe sieci zabezpieczeń i trasy odnotowane w podsieci.

    ![NSG i trasy][7]

## <a name="service-endpoints"></a>Punkty końcowe usługi ##

Punkty końcowe usługi pozwalają ograniczyć dostęp do usług wielodostępnych do zestawu sieci wirtualnych i podsieci platformy Azure. Więcej informacji na temat punktów końcowych usługi zawiera dokumentacja [punktów końcowych usługi dla sieci wirtualnej][serviceendpoints]. 

Po włączeniu punktów końcowych usługi w zasobie niektóre trasy są utworzone z wyższym priorytetem niż wszystkie inne trasy. Jeśli używasz punktów końcowych usługi w dowolnej usłudze platformy Azure z wymuszonym tunelem ASE, ruch do tych usług nie zostanie wymuszony tunelowane. 

Kiedy punkty końcowe usługi są włączone w podsieci z wystąpieniem usługi Azure SQL, wszystkie wystąpienia usługi Azure SQL połączone z tej podsieci muszą mieć włączone punkty końcowe usługi. Jeśli chcesz uzyskać dostęp do wielu wystąpień usługi Azure SQL z tej samej podsieci, musisz włączyć punkty końcowe usługi na każdym wystąpieniu usługi Azure SQL. Żadna inna usługa platformy Azure nie zachowuje się jak sql platformy Azure w odniesieniu do punktów końcowych usługi. Włączenie punktów końcowych usługi w usłudze Azure Storage powoduje zablokowanie dostępu do tego zasobu z podsieci, ale można nadal uzyskiwać dostęp do innych kont usługi Azure Storage, nawet jeśli nie mają włączonych punktów końcowych usługi.  

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
[ARMOverview]: ../../azure-resource-manager/management/overview.md
[ConfigureSSL]: ../configure-ss-cert.md
[Kudu]: https://azure.microsoft.com/resources/videos/super-secret-kudu-debug-console-for-azure-web-sites/
[ASEWAF]: app-service-app-service-environment-web-application-firewall.md
[AppGW]: ../../application-gateway/application-gateway-web-application-firewall-overview.md
[ASEManagement]: ./management-addresses.md
[serviceendpoints]: ../../virtual-network/virtual-network-service-endpoints-overview.md
[forcedtunnel]: ./forced-tunnel-support.md
[serviceendpoints]: ../../virtual-network/virtual-network-service-endpoints-overview.md
