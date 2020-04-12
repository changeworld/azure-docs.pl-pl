---
title: Tworzenie ase równoważenia obciążenia sieciowego z ARM
description: Dowiedz się, jak utworzyć środowisko usługi App Service za pomocą wewnętrznego modułu równoważenia obciążenia (ILB ASE) przy użyciu szablonów usługi Azure Resource Manager. W pełni izoluj aplikacje od Internetu.
author: ccompy
ms.assetid: 0f4c1fa4-e344-46e7-8d24-a25e247ae138
ms.topic: quickstart
ms.date: 08/05/2019
ms.author: ccompy
ms.custom: mvc, seodec18
ms.openlocfilehash: b7fa447e8564fcbf77702f1d3d474cceb48705c5
ms.sourcegitcommit: fb23286d4769442631079c7ed5da1ed14afdd5fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/10/2020
ms.locfileid: "81114638"
---
# <a name="create-and-use-an-internal-load-balancer-app-service-environment"></a>Tworzenie i używanie środowiska usługi aplikacji modułu wewnętrznego równoważenia obciążenia 

Środowisko usługi azure app service to wdrożenie usługi Azure App Service w podsieci w sieci wirtualnej platformy Azure. Istnieją dwa sposoby wdrażania środowiska App Service Environment (ASE): 

- Z wirtualnym adresem IP będącym zewnętrznym adresem IP — jest to często nazywane zewnętrznym środowiskiem ASE.
- Z wirtualnym adresem IP będącym wewnętrznym adresem IP — jest to często nazywane środowiskiem ASE z wewnętrznym modułem równoważenia obciążenia, ponieważ wewnętrzny punkt końcowy jest wewnętrznym modułem równoważenia obciążenia (ILB, Internal Load Balancer). 

W tym artykule przedstawiono sposób tworzenia środowiska ASE z wewnętrznym modułem równoważenia obciążenia. Aby uzyskać omówienie środowiska ASE, zobacz [Wprowadzenie do środowisk usługi app service][Intro]. Aby dowiedzieć się, jak utworzyć zewnętrzne środowisko ASE, zobacz [Create an External ASE][MakeExternalASE] (Tworzenie zewnętrznego środowiska ASE).

## <a name="overview"></a>Omówienie 

Środowisko ASE można wdrożyć za pomocą punktu końcowego dostępnego z Internetu lub adresu IP w sieci wirtualnej. Aby można było ustawić jako adres IP adres sieci wirtualnej, należy wdrożyć środowisko ASE z wewnętrznym modułem równoważenia obciążenia. Podczas wdrażania ase z równoważenia obciążenia sieciowego, należy podać nazwę ase. Nazwa twojego ASE jest używana w sufiksie domeny dla aplikacji w ase.  Sufiks domeny dla ase &lt;ILB&gt;jest nazwa ASE .appserviceenvironment.net. Aplikacje, które są wykonane w ase ILB nie są umieszczane w publicznej usługi DNS. 

Wcześniejsze wersje ase równoważenia obciążenia sieciowego wymagały podania sufiksu domeny i domyślnego certyfikatu dla połączeń HTTPS. Sufiks domeny nie jest już zbierany podczas tworzenia ase ilb i domyślny certyfikat również nie jest już zbierany. Podczas tworzenia ase równoważenia obciążenia sieciowego teraz, domyślny certyfikat jest dostarczany przez firmę Microsoft i jest zaufany przez przeglądarkę. Nadal możesz ustawić niestandardowe nazwy domen w aplikacjach w programie ASE i ustawić certyfikaty dla tych niestandardowych nazw domen. 

Dzięki ase ilb można wykonywać takie czynności, jak:

-   Hostuj aplikacje intranetowe bezpiecznie w chmurze, do których uzyskujesz dostęp za pośrednictwem witryny do lokacji lub usługi ExpressRoute.
-   Ochrona aplikacji za pomocą urządzenia WAF
-   Hostowanie w chmurze aplikacji, które nie są wymienione na publicznych serwerach DNS.
-   Tworzenie odizolowanych od Internetu aplikacji zaplecza, z którymi można bezpiecznie integrować aplikacje frontonu.

### <a name="disabled-functionality"></a>Funkcje wyłączone ###

Pewnych zadań nie można realizować w przypadku używania środowiska ASE z wewnętrznym modułem równoważenia obciążenia:

-   Używanie protokołu SSL opartego na protokole IP.
-   Przypisywanie adresów IP do określonych aplikacji.
-   Kupowanie i używanie certyfikatu za pomocą aplikacji za pośrednictwem witryny Azure Portal. Certyfikaty można uzyskiwać bezpośrednio od urzędu certyfikacji i używać ich z aplikacjami. Nie można ich uzyskiwać za pośrednictwem witryny Azure Portal.

## <a name="create-an-ilb-ase"></a>Tworzenie środowiska ASE z wewnętrznym modułem równoważenia obciążenia ##

Aby utworzyć środowisko ASE z wewnętrznym modułem równoważenia obciążenia:

1. W portalu azure wybierz pozycję **Utwórz** > **środowisko usługi aplikacji**sieci**Web** > .

2. Wybierz subskrypcję.

3. Wybierz lub utwórz grupę zasobów.

4. Wprowadź nazwę środowiska usługi app service.

5. Wybierz wirtualny typ IP wewnętrzny.

    ![Tworzenie środowiska ASE](media/creating_and_using_an_internal_load_balancer_with_app_service_environment/createilbase.png)

> [!NOTE]
> Nazwa środowiska usługi aplikacji nie może być większa niż 37 znaków.

6. Wybierz sieć

7. Wybierz lub utwórz sieć wirtualną. Jeśli utworzysz nową sieć wirtualną w tym miejscu, zostanie ona zdefiniowana z zakresem adresów 192.168.250.0/23. Aby utworzyć sieć wirtualną z innym zakresem adresów lub w innej grupie zasobów niż środowisko ASE, należy użyć portalu tworzenia sieci wirtualnej platformy Azure. 

8. Zaznacz lub utwórz pustą podsieć. Jeśli chcesz wybrać podsieć, musi ona być pusta i nie jest delegowana. Nie można zmienić rozmiaru podsieci po utworzeniu ase. Zalecamy rozmiar `/24`, który zapewnia 256 adresów i może obsłużyć środowiska ASE o maksymalnym rozmiarze i dowolnych potrzebach dotyczących skalowania. 

    ![Sieć ASE][1]

7. Wybierz **pozycję Recenzja i utwórz,** a następnie wybierz pozycję **Utwórz**.


## <a name="create-an-app-in-an-ilb-ase"></a>Tworzenie aplikacji w środowisku ASE z wewnętrznym modułem równoważenia obciążenia ##

Aplikację w środowisku ASE z wewnętrznym modułem równoważenia obciążenia tworzy się tak samo jak w normalnym środowisku ASE.

1. W portalu azure wybierz pozycję **Utwórz zasób** > **aplikacji Web** > **App**.

1. Wprowadź nazwę aplikacji.

1. Wybierz subskrypcję.

1. Wybierz lub utwórz grupę zasobów.

1. Wybierz publikuj, stos czasu wykonywania i system operacyjny.

1. Wybierz lokalizację, w której lokalizacją jest istniejący ase równoważenia obciążenia sieciowego.  Można również utworzyć nowy ASE podczas tworzenia aplikacji, wybierając plan izolowanej usługi aplikacji. Jeśli chcesz utworzyć nowy ASE, wybierz region, w którym ma zostać utworzony ase.

1. Wybierz lub utwórz plan usługi App Service. 

1. **Wybierz pozycję Recenzja i utwórz,** a następnie wybierz pozycję **Utwórz,** gdy będziesz gotowy.

### <a name="web-jobs-functions-and-the-ilb-ase"></a>Zadania Web Job, usługa Functions i środowisko ASE z wewnętrznym modułem równoważenia obciążenia 

W środowisku ASE z wewnętrznym modułem równoważenia obciążenia jest obsługiwana zarówno usługa Functions, jak i zadania Web Job, ale aby portal z nimi współdziałał, musisz mieć dostęp do witryny SCM.  Oznacza to, że przeglądarka musi działać na hoście, który albo znajduje się w sieci wirtualnej, albo jest z nią połączony. Jeśli ase ILB ma nazwę domeny, która nie kończy się *appserviceenvironment.net*, trzeba będzie uzyskać przeglądarkę, aby zaufać certyfikat HTTPS używany przez witrynę scm.

## <a name="dns-configuration"></a>Konfiguracja usługi DNS 

Gdy używasz zewnętrznego wirtualnego adresu IP, usługą DNS zarządza platforma Azure. Każda aplikacja utworzona w środowisku ASE jest automatycznie dodawana do usługi Azure DNS, która jest publiczną usługą DNS. W środowisku ASE z wewnętrznym modułem równoważenia obciążenia musisz zarządzać własną usługą DNS. Sufiks domeny używany z ase ILB zależy od nazwy ASE. Sufiks domeny to * &lt;&gt;nazwa ASE .appserviceenvironment.net*. Adres IP modułu równoważenia obciążenia sieciowego znajduje się w portalu pod **adresami IP**. 

Aby skonfigurować system DNS:

- utworzyć strefę dla * &lt;&gt;nazwy ASE .appserviceenvironment.net*
- utworzyć rekord A w tej strefie, który wskazuje * adres IP równoważenia obciążenia obciążenia
- utworzyć rekord A w tej strefie, który wskazuje @ na adres IP równoważenia obciążenia obciążenia
- tworzenie strefy * &lt;w nazwie&gt;ASE .appserviceenvironment.net* o nazwie scm
- utworzyć rekord A w strefie scm, który wskazuje * adres IP ILB

## <a name="publish-with-an-ilb-ase"></a>Publikowanie za pomocą środowiska ASE z wewnętrznym modułem równoważenia obciążenia

Dla każdej tworzonej aplikacji istnieją dwa punkty końcowe. W ASE ILB masz * &lt;nazwę&gt;&lt; aplikacji . ILB ASE&gt; Domena* i * &lt;&gt;nazwa&lt; aplikacji .scm. Domena ASE&gt;równoważenia obciążenia sieciowego równoważenia*obciążenia . 

Nazwa witryny SCM umożliwia przejście do konsoli Kudu, nazywanej **portalem zaawansowanym**, w witrynie Azure Portal. Konsola Kudu umożliwia między innymi wyświetlanie zmiennych środowiskowych, eksplorowanie dysku i używanie konsoli. Aby uzyskać więcej informacji, zobacz [Kudu console for Azure App Service][Kudu] (Konsola Kudu dla usługi Azure App Service). 

Internetowe systemy ciągłej integracji, takie jak usługi GitHub i Azure DevOps, będą nadal działać ze środowiskiem ASE z wewnętrznym modułem równoważenia obciążenia, jeśli agent kompilacji jest dostępny w Internecie i w tej samej sieci co to środowisko. Dlatego w przypadku usługi Azure DevOps, jeśli agent kompilacji został utworzony w tej samej sieci wirtualnej co środowisko ASE z wewnętrznym modułem równoważenia obciążenia (być może w innej podsieci), to będzie mógł ściągnąć kod z usługi Git Azure DevOps i wdrożyć go w tym środowisku. Jeśli nie chcesz tworzyć własnego agenta kompilacji, musisz użyć systemu ciągłej integracji wykorzystującego model ściągania, takiego jak Dropbox.

Punkty końcowe publikowania dla aplikacji w środowisku ASE z wewnętrznym modułem równoważenia obciążenia używają domeny, za pomocą której utworzono to środowisko. Ta domena pojawia się w profilu publikowania aplikacji i w bloku portalu aplikacji (**Przegląd** > **Podstawowe,** a także **Właściwości**). Jeśli masz ASE ILB z * &lt;sufiksem&gt;domeny ASE nazwa .appserviceenvironment.net*i aplikacji o nazwie *mytest*, użyj *&lt; mytest. Nazwa&gt;ASE .appserviceenvironment.net* dla ftp i *mytest.scm.contoso.net* do wdrożenia w sieci Web.

## <a name="configure-an-ilb-ase-with-a-waf-device"></a>Konfigurowanie ase równoważenia obciążenia sieciowego za pomocą urządzenia WAF ##

Urządzenie zapory aplikacji sieci web (WAF) z interfejsem ASE równoważenia obciążenia sieciowego można połączyć tylko z aplikacjami, które chcesz udostępnić w Internecie, a resztę udostępnić tylko w sieci wirtualnej. Dzięki temu można tworzyć bezpieczne aplikacje wielowarstwowe między innymi.

Aby dowiedzieć się więcej o konfigurowaniu środowiska ASE równoważenia obciążenia sieciowego za pomocą urządzenia WAF, zobacz [Konfigurowanie zapory aplikacji sieci web w środowisku usługi App Service][ASEWAF]. W tym artykule wyjaśniono, jak używać urządzenia wirtualnego Barracuda ze środowiskiem ASE. Innym rozwiązaniem jest używanie usługi Azure Application Gateway. Usługa Application Gateway zabezpiecza wszelkie umieszczone za nią aplikacje za pomocą reguł podstawowych OWASP. Aby uzyskać więcej informacji na temat usługi Application Gateway, zobacz [Introduction to the Azure web application firewall][AppGW] (Wprowadzenie do zapory aplikacji internetowych platformy Azure).

## <a name="ilb-ases-made-before-may-2019"></a>ILB ASE wykonane przed majem 2019

Ases ILB, które zostały wprowadzone przed majem 2019 wymagane do ustawiania sufiksu domeny podczas tworzenia ASE. Wymagali również przesłania domyślnego certyfikatu opartego na tym sufiksie domeny. Ponadto w przypadku starszego programu ILB ASE nie można samodzielnie logować się do konsoli Kudu z aplikacjami w tym ASE ILB. Podczas konfigurowania systemu DNS dla starszego środowiska ASE równoważenia obciążenia sieciowego należy ustawić rekord wieloznaczny A w strefie zgodnej z sufiksem domeny. 

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
[ARMOverview]: ../../azure-resource-manager/management/overview.md
[ConfigureSSL]: ../configure-ssl-certificate.md
[Kudu]: https://azure.microsoft.com/resources/videos/super-secret-kudu-debug-console-for-azure-web-sites/
[ASEWAF]: app-service-app-service-environment-web-application-firewall.md
[AppGW]: ../../application-gateway/application-gateway-web-application-firewall-overview.md
[customdomain]: ../app-service-web-tutorial-custom-domain.md
[linuxapp]: ../containers/app-service-linux-intro.md
