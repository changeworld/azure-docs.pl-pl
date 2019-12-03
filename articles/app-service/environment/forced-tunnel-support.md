---
title: Konfigurowanie wymuszonego tunelowania
description: Dowiedz się, jak umożliwić App Service Environment pracy w przypadku wymuszonego tunelowania ruchu wychodzącego w sieci wirtualnej.
author: ccompy
ms.assetid: 384cf393-5c63-4ffb-9eb2-bfd990bc7af1
ms.topic: quickstart
ms.date: 05/29/2018
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: e0164ac3903c63632c97c4a089066cf6ad23b31b
ms.sourcegitcommit: 48b7a50fc2d19c7382916cb2f591507b1c784ee5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/02/2019
ms.locfileid: "74687174"
---
# <a name="configure-your-app-service-environment-with-forced-tunneling"></a>Konfigurowanie wymuszonego tunelowania środowiska App Service Environment

Środowisko Azure App Service Environment (ASE) to wdrożenie usługi Azure App Service w sieci Azure Virtual Network klienta. Wielu klientów konfiguruje sieci wirtualne platformy Azure jako rozszerzenia sieci lokalnych za pomocą sieci VPN lub połączeń usługi Azure ExpressRoute. Wymuszone tunelowanie ma miejsce podczas przekierowywania ruchu powiązanego z Internetem do sieci VPN lub urządzenia wirtualnego. Urządzenia wirtualne są często używane w celu sprawdzania i inspekcji wychodzącego ruchu sieciowego. 

Środowisko ASE ma wiele zależności zewnętrznych, które opisano w dokumencie [App Service Environment architektury sieci][network] . Zwykle cały ruch wychodzący zależności środowiska ASE musi przechodzić przez wirtualny adres IP, dla którego zainicjowano obsługę tego środowiska. Jeśli zmienisz routing ruchu do lub ze środowiska ASE bez przestrzegania poniższych informacji, Twoje środowisko ASE przestanie działać.

W sieci wirtualnej platformy Azure routing odbywa się na podstawie najdłuższego dopasowania prefiksu (LPM, Longest Prefix Match). Jeśli istnieje więcej niż jedna trasa z tym samym dopasowaniem LPM, wybór trasy odbywa się według następującej kolejności:

* Trasa zdefiniowana przez użytkownika
* Trasa protokołu BGP (jeśli używane są połączenia ExpressRoute)
* Trasa systemowa

Aby dowiedzieć się więcej na temat routingu w sieci wirtualnej, Odczytaj [trasy zdefiniowane przez użytkownika i przekazywanie adresów IP][routes]. 

Jeśli chcesz skierować ruch wychodzący środowiska ASE gdzieś indziej niż bezpośrednio do Internetu, dostępne są następujące opcje:

* Zapewnienie środowisku ASE bezpośredniego dostępu do Internetu
* Konfigurowanie podsieci środowiska ASE w celu ignorowania tras protokołu BGP
* Skonfigurowanie podsieci środowiska ASE w celu używania punktów końcowych usług Azure SQL i Azure Storage
* Dodanie własnych adresów IP do zapory usługi Azure SQL środowiska ASE

## <a name="enable-your-app-service-environment-to-have-direct-internet-access"></a>Zapewnianie środowisku App Service Environment bezpośredniego dostępu do Internetu

Aby umożliwić środowisku ASE bezpośredni dostęp do Internetu, nawet jeśli sieć wirtualną platformy Azure skonfigurowano przy użyciu usługi ExpressRoute, można użyć następujących metod:

* Skonfiguruj w usłudze ExpressRoute anonsowanie adresów 0.0.0.0/0. Domyślnie powoduje to kierowanie całego ruchu wychodzącego do środowiska lokalnego.
* Utwórz trasę zdefiniowaną przez użytkownika z prefiksem adresu 0.0.0.0/0 oraz typem następnego przeskoku Internet i zastosuj ją do podsieci środowiska ASE.

Po wprowadzeniu tych dwóch zmian ruch kierowany do Internetu pochodzący z podsieci środowiska App Service Environment nie będzie w sposób wymuszony kierowany do połączenia usługi ExpressRoute.

Jeśli sieć już przekierowuje ruch lokalnie, należy utworzyć podsieć w celu hostowania środowiska ASE i skonfigurować trasę zdefiniowaną przez użytkownika przed podjęciem próby wdrożenia środowiska ASE.  

> [!IMPORTANT]
> Trasy zdefiniowane przez użytkownika muszą być wystarczająco szczegółowe, aby miały pierwszeństwo względem wszelkich tras anonsowanych przez konfigurację usługi ExpressRoute. W poprzednim przykładzie użyto szerokiego zakresu adresów 0.0.0.0/0. Może to zostać przypadkowo zastąpione przez anonsy tras z bardziej szczegółowymi zakresami adresów.
>
> Środowiska App Service Environment nie są obsługiwane w przypadku konfiguracji usługi ExpressRoute z anonsowaniem krzyżowym tras ze ścieżki publicznej komunikacji równorzędnej do ścieżki prywatnej komunikacji równorzędnej. Konfiguracje usługi ExpressRoute ze skonfigurowaną publiczną komunikacją równorzędną otrzymują anonsy tras od firmy Microsoft. Te anonsy zawierają duży zestaw zakresów adresów platformy Microsoft Azure. W przypadku anonsowania krzyżowego zakresów adresów w ścieżce prywatnej komunikacji równorzędnej wszystkie pakiety sieciowe wychodzące z podsieci środowiska App Service Environment są kierowane do lokalnej infrastruktury sieciowej klienta. Ten przepływ sieciowy nie jest domyślnie obsługiwany w przypadku środowisk App Service Environment. Jednym z rozwiązań tego problemu jest zatrzymanie anonsowania krzyżowego tras ze ścieżki publicznej komunikacji równorzędnej do ścieżki prywatnej komunikacji równorzędnej. Innym jest umożliwienie środowisku App Service Environment działania w przypadku konfiguracji wymuszonego tunelowania.

![Bezpośredni dostęp do Internetu][1]

## <a name="configure-your-ase-subnet-to-ignore-bgp-routes"></a>Konfigurowanie podsieci środowiska ASE w celu ignorowania tras protokołu BGP ## 

Można skonfigurować podsieć środowiska ASE tak, aby były w niej ignorowane wszystkie trasy protokołu BGP.  W przypadku skonfigurowania ignorowania tras protokołu BGP środowisko ASE może uzyskać dostęp do wszystkich zależności bez problemów.  Jednak aby umożliwić aplikacjom dostęp do zasobów lokalnych, konieczne będzie utworzenie tras zdefiniowanych przez użytkownika.

Aby skonfigurować ignorowanie tras protokołu BGP w podsieci środowiska ASE:

* Utwórz trasę zdefiniowaną przez użytkownika i przypisz ją do podsieci środowiska ASE (jeśli taka trasa jeszcze nie istnieje).
* W witrynie Azure Portal otwórz interfejs użytkownika tabeli tras przypisanej do podsieci środowiska ASE.  Wybierz pozycję Konfiguracja.  Wyłącz propagację tras protokołu BGP.  Kliknij pozycję Zapisz. Ta dokumentacja dotycząca wyłączenia programu znajduje się w dokumencie [Tworzenie tabeli tras][routetable] .

Po skonfigurowaniu ignorowania wszystkich tras protokołu BGP w podsieci środowiska ASE aplikacje nie będą mogły nawiązywać połączenia ze środowiskiem lokalnym. Aby umożliwić aplikacjom uzyskiwanie dostępu do zasobów lokalnych, zmodyfikuj trasę zdefiniowaną przez użytkownika przypisaną do podsieci środowiska ASE, dodając trasy dla lokalnych zakresów adresów. Należy wybrać opcję Brama sieci wirtualnej jako Typ następnego przeskoku. 


## <a name="configure-your-ase-with-service-endpoints"></a>Konfigurowanie środowiska ASE z punktami końcowymi usługi ##

Aby przekierować cały ruch wychodzący ze środowiska ASE, z wyjątkiem tego wychodzącego do usług Azure SQL i Azure Storage, wykonaj następujące czynności:

1. Utwórz tabelę tras i przypisz ją do podsieci środowiska ASE. Znajdź w tym miejscu adresy zgodne z Twoim regionem [App Service Environment adresy zarządzania][management]. Utwórz trasy dla tych adresów z następnym przeskokiem do Internetu. Te trasy są niezbędne, ponieważ ruch przychodzący zarządzania środowiska App Service Environment musi udzielać odpowiedzi z tego samego adresu, na który został wysłany.   

2. Włączanie punktów końcowych w usługach Azure SQL i Azure Storage dla podsieci środowiska ASE.  Po wykonaniu tego kroku można skonfigurować sieć wirtualną przy użyciu wymuszonego tunelowania.

Aby utworzyć środowisko ASE w sieci wirtualnej, która została już skonfigurowana do kierowania ruchu lokalnie, należy utworzyć środowisko ASE przy użyciu szablonu usługi Resource Manager.  Środowiska ASE nie można utworzyć przy użyciu portalu we wcześniej istniejącej podsieci.  W przypadku wdrażania środowiska ASE w sieci wirtualnej, która została już skonfigurowana do lokalnego kierowania ruchu wychodzącego, należy utworzyć środowisko ASE przy użyciu szablonu usługi Resource Manager, który nie zezwala na wybranie wcześniej istniejącej podsieci. Aby uzyskać szczegółowe informacje na temat wdrażania środowiska ASE z szablonem, przeczytaj artykuł [tworzenie App Service Environment przy użyciu szablonu][template].

Punkty końcowe usługi pozwalają ograniczyć dostęp do usług wielodostępnych do zestawu sieci wirtualnych i podsieci platformy Azure. Więcej informacji na temat punktów końcowych usługi można znaleźć w dokumentacji [punktów końcowych usługi Virtual Network][serviceendpoints] . 

Po włączeniu punktów końcowych usługi w zasobie niektóre trasy są utworzone z wyższym priorytetem niż wszystkie inne trasy. Jeśli używasz punktów końcowych usługi w środowisku ASE z wymuszonym tunelowaniem, ruch związany z zarządzaniem usługami Azure SQL i Azure Storage nie będzie tunelowany w sposób wymuszony. Tunelowanie pozostałego ruchu zależności środowiska ASE jest wymuszane i nie może on zostać utracony, ponieważ w przeciwnym razie środowisko ASE nie będzie działać prawidłowo.

Kiedy punkty końcowe usługi są włączone w podsieci z wystąpieniem usługi Azure SQL, wszystkie wystąpienia usługi Azure SQL połączone z tej podsieci muszą mieć włączone punkty końcowe usługi. Jeśli chcesz uzyskać dostęp do wielu wystąpień usługi Azure SQL z tej samej podsieci, musisz włączyć punkty końcowe usługi na każdym wystąpieniu usługi Azure SQL.  Usługa Azure Storage nie zachowuje się tak samo jak usługa Azure SQL.  Włączenie punktów końcowych usługi w usłudze Azure Storage powoduje zablokowanie dostępu do tego zasobu z podsieci, ale można nadal uzyskiwać dostęp do innych kont usługi Azure Storage, nawet jeśli nie mają włączonych punktów końcowych usługi.  

Jeśli konfigurujesz wymuszone tunelowanie przy użyciu urządzenia filtru sieciowego, pamiętaj, że środowisko ASE ma zależności oprócz usług Azure SQL i Azure Storage. Jeśli ruch do tych zależności zostanie zablokowany, środowisko ASE nie będzie działać prawidłowo.

![Wymuszone tunelowanie z punktów końcowych usługi][2]

## <a name="add-your-own-ips-to-the-ase-azure-sql-firewall"></a>Dodanie własnych adresów IP do zapory usługi Azure SQL środowiska ASE ##

Aby tunelować cały ruch wychodzący ze środowiska ASE, z wyjątkiem tego wychodzącego do usługi Azure Storage, wykonaj następujące czynności:

1. Utwórz tabelę tras i przypisz ją do podsieci środowiska ASE. Znajdź w tym miejscu adresy zgodne z Twoim regionem [App Service Environment adresy zarządzania][management]. Utwórz trasy dla tych adresów z następnym przeskokiem do Internetu. Te trasy są niezbędne, ponieważ ruch przychodzący zarządzania środowiska App Service Environment musi udzielać odpowiedzi z tego samego adresu, na który został wysłany. 

2. Włączanie punktów końcowych w usłudze Azure Storage dla podsieci środowiska ASE

3. Pobierz adres, który będzie używany dla całego ruchu wychodzącego ze środowiska App Service Environment do Internetu. Jeśli ruch jest przekierowywany lokalnie, te adresy pochodzą z translatora adresów sieciowych (NAT) lub są adresami IP bramy. Jeśli ruch wychodzący środowiska App Service Environment ma być kierowany przez urządzenie NVA, adres ruchu wychodzącego to publiczny adres IP urządzenia NVA.

4. _Aby ustawić adresy ruchu wychodzącego w istniejącym środowisku App Service Environment:_ przejdź do strony resources.azure.com, a następnie do obszaru Subscription/\<identyfikator subskrypcji>/resourceGroups/\<grupa zasobów ase>/providers/Microsoft.Web/hostingEnvironments/\<nazwa ase>. Znajdziesz tam informacje w formacie JSON z opisem Twojego środowiska App Service Environment. Upewnij się, że u góry jest wyświetlana informacja **read/write** (odczyt/zapis). Wybierz pozycję **Edit** (Edytuj). Przewiń do samego dołu. Zmień ustawienie pozycji **userWhitelistedIpRanges** z wartości **null** na wartość podobną do następującej. Użyj adresów, które chcesz ustawić jako zakres adresów ruchu wychodzącego. 

        "userWhitelistedIpRanges": ["11.22.33.44/32", "55.66.77.0/24"] 

   Wybierz pozycję **PUT** u góry. Ta opcja wyzwala operację skalowania środowiska App Service Environment i dostosowanie zapory.

_Aby utworzyć środowisko ASE przy użyciu adresów ruchu_wychodzącego: Postępuj zgodnie z instrukcjami w temacie [Tworzenie App Service Environment z szablonem][template] i ściąganie odpowiedniego szablonu.  Edytuj sekcję „zasoby” w pliku azuredeploy.json, ale nie w bloku „properties”, i dołącz wiersz dla elementu **userWhitelistedIpRanges** z własnymi wartościami.

    "resources": [
      {
        "apiVersion": "2015-08-01",
        "type": "Microsoft.Web/hostingEnvironments",
        "name": "[parameters('aseName')]",
        "kind": "ASEV2",
        "location": "[parameters('aseLocation')]",
        "properties": {
          "name": "[parameters('aseName')]",
          "location": "[parameters('aseLocation')]",
          "ipSslAddressCount": 0,
          "internalLoadBalancingMode": "[parameters('internalLoadBalancingMode')]",
          "dnsSuffix" : "[parameters('dnsSuffix')]",
          "virtualNetwork": {
            "Id": "[parameters('existingVnetResourceId')]",
            "Subnet": "[parameters('subnetName')]"
          },
        "userWhitelistedIpRanges":  ["11.22.33.44/32", "55.66.77.0/30"]
        }
      }
    ]

Te zmiany powodują wysyłanie ruchu do usługi Azure Storage bezpośrednio ze środowiska ASE i umożliwiają dostęp do usługi Azure SQL z dodatkowych adresów innych niż wirtualne adresy IP środowiska ASE.

   ![Wymuszone tunelowanie z listą dozwolonych adresów SQL][3]

## <a name="preventing-issues"></a>Zapobieganie problemom ##

Jeśli komunikacja między środowiskiem ASE i jego zależnościami zostanie przerwana, środowisko ASE przejdzie w stan złej kondycji.  Jeśli środowisko ASE pozostanie w złej kondycji zbyt długo, zostanie wstrzymane. Aby cofnąć wstrzymanie środowiska ASE, postępuj zgodnie z instrukcjami w portalu środowiska ASE.

Oprócz zwykłego przerwania komunikacji możesz niekorzystnie wpłynąć na środowisko ASE przez spowodowanie zbyt dużego opóźnienia. Zbyt duże opóźnienie może wystąpić, jeśli środowisko ASE znajduje się za daleko od sieci lokalnej.  Przykłady zbyt dużej odległości to próba nawiązania połączenia z siecią lokalną za oceanem lub na drugim końcu kontynentu. Opóźnienie może również wystąpić z powodu przeciążenia intranetu lub ograniczeń przepustowości wychodzącej.


<!--IMAGES-->
[1]: ./media/forced-tunnel-support/asedependencies.png
[2]: ./media/forced-tunnel-support/forcedtunnelserviceendpoint.png
[3]: ./media/forced-tunnel-support/forcedtunnelexceptstorage.png

<!--Links-->
[management]: ./management-addresses.md
[network]: ./network-info.md
[routes]: ../../virtual-network/virtual-networks-udr-overview.md
[template]: ./create-from-template.md
[serviceendpoints]: ../../virtual-network/virtual-network-service-endpoints-overview.md
[routetable]: ../../virtual-network/manage-route-table.md#create-a-route-table
