---
title: Konfigurowanie wymuszonego tunelowania środowiska Azure App Service Environment
description: Umożliwianie działania środowiska App Service Environment w przypadku wymuszonego tunelowania ruchu wychodzącego
services: app-service
documentationcenter: na
author: ccompy
manager: stefsch
ms.assetid: 384cf393-5c63-4ffb-9eb2-bfd990bc7af1
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 03/20/2018
ms.author: ccompy
ms.custom: mvc
ms.openlocfilehash: 904641a433d55cc5f1d04b17ed067cd560c6b33c
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/05/2018
---
# <a name="configure-your-app-service-environment-with-forced-tunneling"></a>Konfigurowanie wymuszonego tunelowania środowiska App Service Environment

Środowisko Azure App Service Environment (ASE) to wdrożenie usługi Azure App Service w sieci Azure Virtual Network klienta. Wielu klientów konfiguruje sieci wirtualne platformy Azure jako rozszerzenia sieci lokalnych za pomocą sieci VPN lub połączeń usługi Azure ExpressRoute. Wymuszone tunelowanie ma miejsce podczas przekierowywania ruchu powiązanego z Internetem do sieci VPN lub urządzenia wirtualnego. Często jest to wykonywane w ramach wymagań dotyczących zabezpieczeń w celu przeglądania i inspekcji całego ruchu wychodzącego. 

Środowisko ASE jest zależne od szeregu elementów zewnętrznych, które opisano w dokumencie [App Service Environment network architecture][network] (Architektura sieci w środowisku App Service Environment). Zwykle cały ruch wychodzący zależności środowiska ASE musi przechodzić przez wirtualny adres IP, dla którego zainicjowano obsługę tego środowiska. Jeśli zmienisz routing ruchu do lub ze środowiska ASE bez przestrzegania poniższych informacji, Twoje środowisko ASE przestanie działać.

W sieci wirtualnej platformy Azure routing odbywa się na podstawie najdłuższego dopasowania prefiksu (LPM, Longest Prefix Match). Jeśli istnieje więcej niż jedna trasa z tym samym dopasowaniem LPM, wybór trasy odbywa się według następującej kolejności:

* Trasa zdefiniowana przez użytkownika
* Trasa protokołu BGP (jeśli używane są połączenia ExpressRoute)
* Trasa systemowa

Aby dowiedzieć się więcej na temat routingu w sieci wirtualnej, przeczytaj [User-defined routes and IP forwarding][routes] (Trasy zdefiniowane przez użytkownika i przesyłanie pakietów IP dalej). 

Jeśli chcesz skierować ruch wychodzący środowiska ASE gdzieś indziej niż bezpośrednio do Internetu, dostępne są następujące opcje:

* Zapewnienie środowisku ASE bezpośredniego dostępu do Internetu
* Skonfigurowanie podsieci środowiska ASE w celu używania punktów końcowych usług Azure SQL i Azure Storage
* Dodanie własnych adresów IP do zapory usługi Azure SQL środowiska ASE

## <a name="enable-your-app-service-environment-to-have-direct-internet-access"></a>Zapewnianie środowisku App Service Environment bezpośredniego dostępu do Internetu

Aby umożliwić środowisku ASE przechodzenie bezpośrednio do Internetu, nawet jeśli sieć wirtualną platformy Azure skonfigurowano przy użyciu usługi ExpressRoute, można użyć następujących metod:

* Skonfiguruj w usłudze ExpressRoute anonsowanie adresów 0.0.0.0/0. Domyślnie powoduje to kierowanie całego ruchu wychodzącego do środowiska lokalnego.
* Utwórz trasę zdefiniowaną przez użytkownika z prefiksem adresu 0.0.0.0/0 oraz typem następnego przeskoku Internet i zastosuj ją do podsieci środowiska ASE.

Po wprowadzeniu tych dwóch zmian ruch kierowany do Internetu pochodzący z podsieci środowiska App Service Environment nie będzie w sposób wymuszony kierowany do połączenia usługi ExpressRoute.

Jeśli sieć już przekierowuje ruch lokalnie, należy utworzyć podsieć w celu hostowania środowiska ASE i skonfigurować odpowiednią trasę UDR przed podjęciem próby wdrożenia środowiska ASE.  

> [!IMPORTANT]
> Trasy zdefiniowane przez użytkownika muszą być wystarczająco szczegółowe, aby miały pierwszeństwo względem wszelkich tras anonsowanych przez konfigurację usługi ExpressRoute. W poprzednim przykładzie użyto szerokiego zakresu adresów 0.0.0.0/0. Może to zostać przypadkowo zastąpione przez anonsy tras z bardziej szczegółowymi zakresami adresów.
>
> Środowiska App Service Environment nie są obsługiwane w przypadku konfiguracji usługi ExpressRoute z anonsowaniem krzyżowym tras ze ścieżki publicznej komunikacji równorzędnej do ścieżki prywatnej komunikacji równorzędnej. Konfiguracje usługi ExpressRoute ze skonfigurowaną publiczną komunikacją równorzędną otrzymują anonsy tras od firmy Microsoft. Te anonse zawierają duży zestaw zakresów adresów platformy Microsoft Azure. W przypadku anonsowania krzyżowego zakresów adresów w ścieżce prywatnej komunikacji równorzędnej wszystkie pakiety sieciowe wychodzące z podsieci środowiska App Service Environment są kierowane do lokalnej infrastruktury sieciowej klienta. Ten przepływ sieciowy nie jest domyślnie obsługiwany w przypadku środowisk App Service Environment. Jednym z rozwiązań tego problemu jest zatrzymanie anonsowania krzyżowego tras ze ścieżki publicznej komunikacji równorzędnej do ścieżki prywatnej komunikacji równorzędnej. Innym jest umożliwienie środowisku App Service Environment działania w przypadku konfiguracji wymuszonego tunelowania.

![Bezpośredni dostęp do Internetu][1]


## <a name="configure-your-ase-with-service-endpoints"></a>Konfigurowanie środowiska ASE z punktami końcowymi usługi ##

Aby przekierować cały ruch wychodzący ze środowiska ASE, z wyjątkiem tego wychodzącego do usług Azure SQL i Azure Storage, wykonaj następujące czynności:

1. Utwórz tabelę tras i przypisz ją do podsieci środowiska ASE. Aby znaleźć adresy zgodne z Twoim regionem, zobacz [App Service Environment management addresses][management] (Adresy zarządzania środowiska App Service Environment). Utwórz trasy dla tych adresów z następnym przeskokiem do Internetu. Jest to niezbędne, ponieważ ruch przychodzący zarządzania środowiska App Service Environment musi udzielać odpowiedzi z tego samego adresu, na który został wysłany.   

2. Włączanie punktów końcowych w usługach Azure SQL i Azure Storage dla podsieci środowiska ASE.  Po wykonaniu tego kroku można skonfigurować sieć wirtualną przy użyciu wymuszonego tunelowania.

Aby utworzyć środowisko ASE w sieci wirtualnej, która została już skonfigurowana do kierowania ruchu lokalnie, należy utworzyć środowisko ASE przy użyciu szablonu usługi Resource Manager.  Środowiska ASE nie można utworzyć przy użyciu portalu we wcześniej istniejącej podsieci.  W przypadku wdrażania środowiska ASE w sieci wirtualnej, która została już skonfigurowana do lokalnego kierowania ruchu wychodzącego, należy utworzyć środowisko ASE przy użyciu szablonu usługi Resource Manager, który nie zezwala na wybranie wcześniej istniejącej podsieci. Szczegółowe informacje na temat wdrażania środowiska ASE przy użyciu szablonu można znaleźć w temacie [Creating an App Service Environment using a template (Tworzenie środowiska App Service Environment przy użyciu szablonu)][template].

Punkty końcowe usługi pozwalają ograniczyć dostęp do usług wielodostępnych do zestawu sieci wirtualnych i podsieci platformy Azure. Więcej informacji na temat punktów końcowych usługi zawiera dokumentacja [punktów końcowych usługi sieci wirtualnej][serviceendpoints]. 

Po włączeniu punktów końcowych usługi w zasobie niektóre trasy są utworzone z wyższym priorytetem niż wszystkie inne trasy. Jeśli używasz punktów końcowych usługi w środowisku ASE z wymuszonym tunelowaniem, ruch związany z zarządzaniem usługami Azure SQL i Azure Storage nie będzie tunelowany w sposób wymuszony. Tunelowanie pozostałego ruchu zależności środowiska ASE jest wymuszane i nie może on zostać utracony, ponieważ w przeciwnym razie środowisko ASE nie będzie działać prawidłowo.

Kiedy punkty końcowe usługi są włączone w podsieci z wystąpieniem usługi Azure SQL, wszystkie wystąpienia usługi Azure SQL połączone z tej podsieci muszą mieć włączone punkty końcowe usługi. Jeśli chcesz uzyskać dostęp do wielu wystąpień usługi Azure SQL z tej samej podsieci, musisz włączyć punkty końcowe usługi na każdym wystąpieniu usługi Azure SQL.  Usługa Azure Storage nie zachowuje się tak samo jak usługa Azure SQL.  Włączenie punktów końcowych usługi w usłudze Azure Storage powoduje zablokowanie dostępu do tego zasobu z podsieci, ale można nadal uzyskiwać dostęp do innych kont usługi Azure Storage, nawet jeśli nie mają włączonych punktów końcowych usługi.  

Jeśli konfigurujesz wymuszone tunelowanie przy użyciu urządzenia filtru sieciowego, pamiętaj, że środowisko ASE ma zależności oprócz usług Azure SQL i Azure Storage. Musisz zezwolić na ruch do tych zależności; w przeciwnym razie środowisko ASE nie będzie działać prawidłowo.

![Wymuszone tunelowanie z punktów końcowych usługi][2]

## <a name="add-your-own-ips-to-the-ase-azure-sql-firewall"></a>Dodanie własnych adresów IP do zapory usługi Azure SQL środowiska ASE ##

Aby tunelować cały ruch wychodzący ze środowiska ASE, z wyjątkiem tego wychodzącego do usługi Azure Storage, wykonaj następujące czynności:

1. Utwórz tabelę tras i przypisz ją do podsieci środowiska ASE. Aby znaleźć adresy zgodne z Twoim regionem, zobacz [App Service Environment management addresses][management] (Adresy zarządzania środowiska App Service Environment). Utwórz trasy dla tych adresów z następnym przeskokiem do Internetu. Jest to niezbędne, ponieważ ruch przychodzący zarządzania środowiska App Service Environment musi udzielać odpowiedzi z tego samego adresu, na który został wysłany. 

2. Włączanie punktów końcowych w usłudze Azure Storage dla podsieci środowiska ASE

3. Pobierz adres, który będzie używany dla całego ruchu wychodzącego ze środowiska App Service Environment do Internetu. Jeśli ruch jest przekierowywany lokalnie, te adresy pochodzą z translatora adresów sieciowych (NAT) lub są adresami IP bramy. Jeśli ruch wychodzący środowiska App Service Environment ma być kierowany przez urządzenie NVA, adres ruchu wychodzącego to publiczny adres IP urządzenia NVA.

4. _Aby ustawić adresy ruchu wychodzącego w istniejącym środowisku App Service Environment:_ przejdź do strony resource.azure.com, a następnie do obszaru Subscription/<subscription id>/resourceGroups/<ase resource group>/providers/Microsoft.Web/hostingEnvironments/<ase name>. Znajdziesz tam informacje w formacie JSON z opisem Twojego środowiska App Service Environment. Upewnij się, że u góry jest wyświetlana informacja **read/write** (odczyt/zapis). Wybierz pozycję **Edit** (Edytuj). Przewiń do samego dołu. Zmień ustawienie pozycji **userWhitelistedIpRanges** z wartości **null** na wartość podobną do następującej. Użyj adresów, które chcesz ustawić jako zakres adresów ruchu wychodzącego. 

        "userWhitelistedIpRanges": ["11.22.33.44/32", "55.66.77.0/24"] 

   Wybierz pozycję **PUT** u góry. Ta opcja wyzwala operację skalowania środowiska App Service Environment i dostosowanie zapory.

_Aby utworzyć środowisko ASE z adresami ruchu wychodzącego_: postępuj zgodnie z instrukcjami podanymi w temacie [Create an App Service Environment with a template][template] (Tworzenie środowiska App Service Environment przy użyciu szablonu) i uzyskaj odpowiedni szablon.  Edytuj sekcję „zasoby” w pliku azuredeploy.json, ale nie w bloku „properties”, i dołącz wiersz dla elementu **userWhitelistedIpRanges** z własnymi wartościami.

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
