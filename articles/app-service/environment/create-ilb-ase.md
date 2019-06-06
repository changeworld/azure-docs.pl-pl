---
title: Tworzenie i używanie wewnętrznego modułu równoważenia obciążenia ze środowiskiem Azure App Service Environment
description: Szczegółowe informacje na temat tworzenia i używania środowiska Azure App Service Environment odizolowanego od Internetu
services: app-service
documentationcenter: na
author: ccompy
manager: stefsch
ms.assetid: 0f4c1fa4-e344-46e7-8d24-a25e247ae138
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 05/28/2019
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: 5b05755502ad5836a21080a122d2e1721825f10c
ms.sourcegitcommit: 4cdd4b65ddbd3261967cdcd6bc4adf46b4b49b01
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/06/2019
ms.locfileid: "66734686"
---
# <a name="create-and-use-an-internal-load-balancer-app-service-environment"></a>Tworzenie i używanie wewnętrznego obciążenia równoważenia środowiska usługi App Service 

Azure App Service Environment to wdrożenie usługi Azure App Service w podsieci sieci wirtualnej platformy Azure (VNet). Istnieją dwa sposoby wdrażania środowiska App Service Environment (ASE): 

- Z wirtualnym adresem IP będącym zewnętrznym adresem IP — jest to często nazywane zewnętrznym środowiskiem ASE.
- Z wirtualnym adresem IP będącym wewnętrznym adresem IP — jest to często nazywane środowiskiem ASE z wewnętrznym modułem równoważenia obciążenia, ponieważ wewnętrzny punkt końcowy jest wewnętrznym modułem równoważenia obciążenia (ILB, Internal Load Balancer). 

W tym artykule przedstawiono sposób tworzenia środowiska ASE z wewnętrznym modułem równoważenia obciążenia. Omówienie środowiska ASE podano w temacie [Wprowadzenie do środowisk App Service Environment][Intro]. Aby dowiedzieć się, jak utworzyć zewnętrzne środowisko ASE, zobacz [Create an External ASE][MakeExternalASE] (Tworzenie zewnętrznego środowiska ASE).

## <a name="overview"></a>Omówienie 

Środowisko ASE można wdrożyć za pomocą punktu końcowego dostępnego z Internetu lub adresu IP w sieci wirtualnej. Aby można było ustawić jako adres IP adres sieci wirtualnej, należy wdrożyć środowisko ASE z wewnętrznym modułem równoważenia obciążenia. Podczas wdrażania środowiska ASE z wewnętrznym modułem równoważenia obciążenia należy podać nazwę środowiska ASE. Nazwa środowiska ASE jest używana w sufiks domeny dla aplikacji w środowisku ASE.  Sufiks domeny środowiska ASE wewnętrznego modułu równoważenia obciążenia jest &lt;Nazwa środowiska ASE&gt;. appservicewebsites.net. Aplikacje, które zostały wprowadzone w środowisku ASE z wewnętrznym modułem równoważenia obciążenia nie są umieszczane w publicznym systemie DNS. 

Wcześniejszych wersjach środowiska ASE wewnętrznego modułu równoważenia obciążenia wymaga zapewniają sufiks domeny i domyślny certyfikat dla połączeń HTTPS. Sufiks domeny nie są zbierane podczas tworzenia środowiska ASE z wewnętrznym modułem równoważenia obciążenia i domyślny certyfikat nie jest już także zbierane. Po utworzeniu środowiska ASE z wewnętrznym modułem równoważenia obciążenia teraz domyślnego certyfikatu udostępniana przez firmę Microsoft i jest zaufany przez przeglądarkę. Jesteś nadal można ustawić niestandardowe nazwy domen aplikacji w środowisku ASE i ustaw opcję certyfikaty na tych niestandardowych nazw domen. 

Środowisko ASE z wewnętrznym modułem równoważenia obciążenia można wykonać czynności takich jak:

-   Hostowanie aplikacji intranetowych bezpieczne w chmurze, którego dostęp można uzyskać za pośrednictwem lokacja lokacja lub ExpressRoute.
-   Ochrona aplikacji przy użyciu urządzenia zapory aplikacji sieci Web
-   Hostowanie w chmurze aplikacji, które nie są wymienione na publicznych serwerach DNS.
-   Tworzenie odizolowanych od Internetu aplikacji zaplecza, z którymi można bezpiecznie integrować aplikacje frontonu.

### <a name="disabled-functionality"></a>Funkcje wyłączone ###

Pewnych zadań nie można realizować w przypadku używania środowiska ASE z wewnętrznym modułem równoważenia obciążenia:

-   Używanie protokołu SSL opartego na protokole IP.
-   Przypisywanie adresów IP do określonych aplikacji.
-   Kupowanie i używanie certyfikatu za pomocą aplikacji za pośrednictwem witryny Azure Portal. Certyfikaty można uzyskiwać bezpośrednio od urzędu certyfikacji i używać ich z aplikacjami. Nie można ich uzyskiwać za pośrednictwem witryny Azure Portal.

## <a name="create-an-ilb-ase"></a>Tworzenie środowiska ASE z wewnętrznym modułem równoważenia obciążenia ##

Aby utworzyć środowisko ASE z wewnętrznym modułem równoważenia obciążenia:

1. W witrynie Azure Portal wybierz pozycję **Utwórz zasób** > **Internet** > **App Service Environment**.

2. Wybierz subskrypcję.

3. Wybierz lub utwórz grupę zasobów.

4. Wprowadź nazwę środowiska App Service Environment.

5. Wybierz typ wirtualnego adresu IP wewnętrzne.

    ![Tworzenie środowiska ASE](media/creating_and_using_an_internal_load_balancer_with_app_service_environment/createilbase.png)

6. Wybierz sieć

7. Wybierz lub Utwórz sieć wirtualną. Jeśli tworzysz nową sieć wirtualną w tym miejscu, zostanie zdefiniowany zakres adresów równy 192.168.250.0/23. Tworzenie sieci wirtualnej z zakresem innego adresu lub w innej grupie zasobów niż środowisko ASE, użyj portalu tworzenie sieci wirtualnej platformy Azure. 

8. Wybierz lub Utwórz pustą podsieć. Jeśli chcesz wybrać podsieć, musi być puste i bez delegowania. Nie można zmienić rozmiar podsieci, po utworzeniu środowiska ASE. Zalecamy rozmiar `/24`, który zapewnia 256 adresów i może obsłużyć środowiska ASE o maksymalnym rozmiarze i dowolnych potrzebach dotyczących skalowania. 

    ![Sieci środowiska ASE][1]

7. Wybierz **przeglądu i Utwórz** polecenie **Utwórz**.

## <a name="create-an-app-in-an-ilb-ase"></a>Tworzenie aplikacji w środowisku ASE z wewnętrznym modułem równoważenia obciążenia ##

Aplikację w środowisku ASE z wewnętrznym modułem równoważenia obciążenia tworzy się tak samo jak w normalnym środowisku ASE.

1. W witrynie Azure portal wybierz **Utwórz zasób** > **Web** > **aplikacji sieci Web**.

1. Wprowadź nazwę aplikacji.

1. Wybierz subskrypcję.

1. Wybierz lub utwórz grupę zasobów.

1. Wybierz publikowania, stosu środowiska uruchomieniowego i systemu operacyjnego.

1. Wybierz lokalizację, lokalizacja, w którym znajduje się istniejącego środowiska ASE z wewnętrznym modułem równoważenia obciążenia.  Można również utworzyć nowego środowiska ASE podczas tworzenia aplikacji, wybierając plan izolowanej usługi App Service. Jeśli chcesz utworzyć nowego środowiska ASE, wybierz wymaganym ASE można utworzyć w regionie.

1. Wybierz lub utwórz plan usługi App Service. 

1. Wybierz **przeglądu i Utwórz** polecenie **Utwórz** po osiągnięciu gotowości.

### <a name="web-jobs-functions-and-the-ilb-ase"></a>Zadania Web Job, usługa Functions i środowisko ASE z wewnętrznym modułem równoważenia obciążenia 

W środowisku ASE z wewnętrznym modułem równoważenia obciążenia jest obsługiwana zarówno usługa Functions, jak i zadania Web Job, ale aby portal z nimi współdziałał, musisz mieć dostęp do witryny SCM.  Oznacza to, że przeglądarka musi działać na hoście, który albo znajduje się w sieci wirtualnej, albo jest z nią połączony. Jeśli środowisko ASE wewnętrznego modułu równoważenia obciążenia ma nazwę domeny, która nie kończy się *appserviceenvironment.net*, konieczne będzie uzyskanie przeglądarkę, aby zaufać certyfikatowi protokołu HTTPS, używane przez witryny funkcji scm.

## <a name="dns-configuration"></a>Konfiguracja usługi DNS 

Gdy używasz zewnętrznego wirtualnego adresu IP, usługą DNS zarządza platforma Azure. Każda aplikacja utworzona w środowisku ASE jest automatycznie dodawana do usługi Azure DNS, która jest publiczną usługą DNS. W środowisku ASE z wewnętrznym modułem równoważenia obciążenia musisz zarządzać własną usługą DNS. Sufiks domeny używane z ASE z wewnętrznym modułem równoważenia obciążenia jest zależna od Nazwa środowiska ASE. Sufiks domeny jest  *&lt;Nazwa środowiska ASE&gt;. appserviceenvironment.net*. Adres IP dla swojej wewnętrznego modułu równoważenia obciążenia znajduje się w witrynie portal w obszarze **adresów IP**. 

Aby skonfigurować serwer DNS:

- Tworzenie strefy dla  *&lt;Nazwa środowiska ASE&gt;. appserviceenvironment.net*
- Utwórz rekord w tej strefie, który wskazuje * adres IP wewnętrznego modułu równoważenia obciążenia 
- Utwórz strefę w  *&lt;Nazwa środowiska ASE&gt;. scm.appserviceenvironment.net* o nazwie Menedżer sterowania usługami
- Tworzenie rekordu w strefie scm, wskazujący na adres IP wewnętrznego modułu równoważenia obciążenia

## <a name="publish-with-an-ilb-ase"></a>Publikowanie za pomocą środowiska ASE z wewnętrznym modułem równoważenia obciążenia

Dla każdej tworzonej aplikacji istnieją dwa punkty końcowe. W przypadku środowiska ASE z wewnętrznym modułem równoważenia obciążenia, masz *&lt;nazwy aplikacji&gt;.&lt; Domena środowiska ASE z wewnętrznym modułem równoważenia obciążenia&gt;* i  *&lt;nazwy aplikacji&gt;.scm.&lt; Domena środowiska ASE z wewnętrznym modułem równoważenia obciążenia&gt;* . 

Nazwa witryny SCM umożliwia przejście do konsoli Kudu, nazywanej **portalem zaawansowanym**, w witrynie Azure Portal. Konsola Kudu umożliwia między innymi wyświetlanie zmiennych środowiskowych, eksplorowanie dysku i używanie konsoli. Aby uzyskać więcej informacji, zobacz [Kudu console for Azure App Service][Kudu] (Konsola Kudu dla usługi Azure App Service). 

Internetowe systemy ciągłej integracji, takie jak usługi GitHub i Azure DevOps, będą nadal działać ze środowiskiem ASE z wewnętrznym modułem równoważenia obciążenia, jeśli agent kompilacji jest dostępny w Internecie i w tej samej sieci co to środowisko. Dlatego w przypadku usługi Azure DevOps, jeśli agent kompilacji został utworzony w tej samej sieci wirtualnej co środowisko ASE z wewnętrznym modułem równoważenia obciążenia (być może w innej podsieci), to będzie mógł ściągnąć kod z usługi Git Azure DevOps i wdrożyć go w tym środowisku. Jeśli nie chcesz tworzyć własnego agenta kompilacji, musisz użyć systemu ciągłej integracji wykorzystującego model ściągania, takiego jak Dropbox.

Punkty końcowe publikowania dla aplikacji w środowisku ASE z wewnętrznym modułem równoważenia obciążenia używają domeny, za pomocą której utworzono to środowisko. Ta domena jest wyświetlana w profilu publikowania aplikacji i w bloku portalu aplikacji (**Przegląd** > **Podstawy** oraz **Właściwości**). Jeśli masz środowisko ASE wewnętrznego modułu równoważenia obciążenia z sufiksem domeny  *&lt;Nazwa środowiska ASE&gt;. appserviceenvironment.net*i aplikacji o nazwie *testowa*, użyj *testowa.&lt; Nazwa środowiska ASE&gt;. appserviceenvironment.net* dla połączenia FTP i *testowa.SCM.contoso.NET* dla wdrażania w Internecie.

## <a name="configure-an-ilb-ase-with-a-waf-device"></a>Skonfiguruj środowisko ASE z wewnętrznym modułem równoważenia obciążenia z urządzeniem zapory aplikacji sieci Web ##

Możesz połączyć urządzenie umożliwiającymi zainstalowanie zapory aplikacji sieci web za pomocą środowiska ASE wewnętrznego modułu równoważenia obciążenia, aby uwidocznić tylko aplikacje, które mają się z Internetem i Zachowaj pozostałe dostępny tylko z w sieci wirtualnej. Umożliwia to tworzenie bezpiecznych aplikacji wielowarstwowej, między innymi.

Aby dowiedzieć się więcej na temat sposobu konfigurowania środowiska ASE wewnętrznego modułu równoważenia obciążenia z urządzeniem zapory aplikacji sieci Web, zobacz [Konfigurowanie zapory aplikacji sieci web przy użyciu środowiska App Service environment][ASEWAF]. W tym artykule wyjaśniono, jak używać urządzenia wirtualnego Barracuda ze środowiskiem ASE. Innym rozwiązaniem jest używanie usługi Azure Application Gateway. Usługa Application Gateway zabezpiecza wszelkie umieszczone za nią aplikacje za pomocą reguł podstawowych OWASP. Aby uzyskać więcej informacji na temat usługi Application Gateway, zobacz [Introduction to the Azure web application firewall][AppGW] (Wprowadzenie do zapory aplikacji internetowych platformy Azure).

## <a name="ilb-ases-made-before-may-2019"></a>Środowiska ASE wewnętrznego modułu równoważenia obciążenia przed maja 2019 r

Środowiska ASE wewnętrznego modułu równoważenia obciążenia, które zostały dokonane przed maja 2019 wymaga ustawienia sufiks domeny podczas tworzenia środowiska ASE. Są również wymagane przekazania domyślnego certyfikatu, która była oparta na ten sufiks domeny. Ponadto za pomocą starszej ASE z wewnętrznym modułem równoważenia obciążenia nie można wykonać logowanie jednokrotne do konsoli Kudu, za pomocą aplikacji w tym środowisku ASE z wewnętrznym modułem równoważenia obciążenia. Podczas konfigurowania DNS dla starszych ASE z wewnętrznym modułem równoważenia obciążenia, musisz ustawić rekord A symbolu wieloznacznego w strefie, która pasuje do sieci sufiksów domeny. 

## <a name="get-started"></a>Rozpoczęcie pracy ##

* Aby rozpocząć pracę ze środowiskami ASE, zobacz [Wprowadzenie do środowisk App Service Environment][Intro]. 

<!--Image references-->
[1]: ./media/creating_and_using_an_internal_load_balancer_with_app_service_environment/createilbase-network.png
[2]: ./media/creating_and_using_an_internal_load_balancer_with_app_service_environment/createilbase-webapp.png
[5]: ./media/creating_and_using_an_internal_load_balancer_with_app_service_environment/createilbase-ipaddresses.png

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
[webapps]: ../overview.md
[mobileapps]: ../../app-service-mobile/app-service-mobile-value-prop.md
[Functions]: ../../azure-functions/index.yml
[Pricing]: https://azure.microsoft.com/pricing/details/app-service/
[ARMOverview]: ../../azure-resource-manager/resource-group-overview.md
[ConfigureSSL]: ../web-sites-purchase-ssl-web-site.md
[Kudu]: https://azure.microsoft.com/resources/videos/super-secret-kudu-debug-console-for-azure-web-sites/
[ASEWAF]: app-service-app-service-environment-web-application-firewall.md
[AppGW]: ../../application-gateway/application-gateway-web-application-firewall-overview.md
[customdomain]: ../app-service-web-tutorial-custom-domain.md
[linuxapp]: ../containers/app-service-linux-intro.md
