---
title: Tworzenie środowiska usługi aplikacji Azure zewnętrznych
description: Wyjaśnia sposób tworzenia środowiska usługi aplikacji podczas tworzenia aplikacji lub autonomiczne
services: app-service
documentationcenter: na
author: ccompy
manager: stefsch
ms.assetid: 94dd0222-b960-469c-85da-7fcb98654241
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/13/2017
ms.author: ccompy
ms.openlocfilehash: 34248d75c190aa4636c39f087d399d946b589d58
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/20/2018
---
# <a name="create-an-external-app-service-environment"></a>Tworzenie środowiska usługi aplikacji zewnętrznych #

Środowisko Azure App Service Environment to wdrożenie usługi Azure App Service w podsieci sieci wirtualnej platformy Azure (VNet). Istnieją dwa sposoby wdrażania środowiska App Service Environment (ASE):

- Z wirtualnym adresem IP będącym zewnętrznym adresem IP — jest to często nazywane zewnętrznym środowiskiem ASE.
- Przy użyciu adresu VIP wewnętrznego adresu IP często nazywane ASE ILB, ponieważ wewnętrzny punkt końcowy jest wewnętrzny moduł równoważenia obciążenia (ILB).

W tym artykule przedstawiono sposób tworzenia ASE zewnętrznych. Aby zapoznać się z omówieniem ASE, zobacz [wprowadzenie do środowiska usługi aplikacji][Intro]. Aby uzyskać informacje na temat tworzenia ASE ILB, zobacz [tworzenie i używanie ASE ILB][MakeILBASE].

## <a name="before-you-create-your-ase"></a>Przed utworzeniem sieci ASE ##

Po utworzeniu sieci ASE, nie można zmienić następujących czynności:

- Lokalizacja
- Subskrypcja
- Grupa zasobów
- Używane w sieci wirtualnej
- Podsieci używane
- Rozmiar podsieci

> [!NOTE]
> Po wybraniu sieci wirtualnej i określ podsieć, upewnij się, że jest wystarczająco duża, aby uwzględnić przyszły wzrost. Firma Microsoft zaleca rozmiar `/25` z adresami 128.
>

## <a name="three-ways-to-create-an-ase"></a>Trzy sposoby tworzenia ASE ##

Istnieją trzy sposoby tworzenia ASE:

- **Podczas tworzenia planu usługi App Service**. Ta metoda tworzy ASE i plan usługi aplikacji w jednym kroku.
- **Jako akcję autonomiczny**. Ta metoda tworzy autonomiczny ASE, czyli ASE pustą. Ta metoda jest procesem bardziej zaawansowanych można utworzyć ASE. Można go użyć do utworzenia ASE z ILB.
- **Za pomocą szablonu usługi Azure Resource Manager**. Ta metoda jest dla użytkowników zaawansowanych. Aby uzyskać więcej informacji, zobacz [utworzyć ASE na podstawie szablonu][MakeASEfromTemplate].

Zewnętrzne ASE ma publiczny adres VIP, co oznacza, że cały ruch HTTP/HTTPS do aplikacji w ASE trafienia internet dostępny adres IP. ASE z ILB ma adres IP w podsieci używanej przez ASE. Aplikacje obsługiwane w elemencie ASE ILB nie są bezpośrednie połączenie z Internetem.

## <a name="create-an-ase-and-an-app-service-plan-together"></a>Utwórz razem ASE i plan usługi aplikacji ##

Plan usługi aplikacji to kontener aplikacji. Po utworzeniu aplikacji w usłudze App Service wybierz lub Utwórz plan usługi aplikacji. Środowiska usługi aplikacji przytrzymaj planów usługi App Service i planów usługi App Service przechowywania aplikacji.

Aby utworzyć ASE podczas tworzenia planu usługi aplikacji:

1. W [portalu Azure](https://portal.azure.com/), wybierz pozycję **Utwórz zasób** > **sieci Web i mobilność** > **aplikacji sieci Web**.

    ![Tworzenie aplikacji sieci Web][1]

2. Wybierz subskrypcję. Aplikacji a ASE są tworzone w tej samej subskrypcji.

3. Wybierz lub utwórz grupę zasobów. Z grupami zasobów można zarządzać jako jednostka powiązanych zasobów systemu Azure. Grupy zasobów są także przydatne podczas ustanawiania zasady kontroli dostępu opartej na rolach dla aplikacji. Aby uzyskać więcej informacji, zobacz [Omówienie usługi Azure Resource Manager][ARMOverview].

4. Wybierz system operacyjny. 

    * Hosting w aplikacji systemu Linux w elemencie ASE jest nowa funkcja w wersji zapoznawczej, dlatego zalecamy nie dodać aplikacje dla systemu Linux w elemencie ASE, które jest aktualnie uruchomione obciążeń produkcyjnych. 
    * Dodawanie aplikacji systemu Linux w elemencie ASE oznacza, że ASE będzie także w wersji zapoznawczej. 

5. Wybierz plan usługi aplikacji, a następnie wybierz **Utwórz nowy**. Linux aplikacji sieci web i aplikacji sieci web systemu Windows nie może należeć do tego samego planu usługi App Service, ale może być w tym samym środowisku usługi aplikacji. 

    ![Nowy plan usługi App Service][2]

6. W **lokalizacji** listy rozwijanej wybierz region, w którym chcesz utworzyć ASE. W przypadku wybrania istniejącego ASE nowe ASE nie jest tworzone. Plan usługi aplikacji jest tworzony w ASE wybrany. 

    > [!NOTE]
    > Linux na ASE jest włączona tylko w regionach, 6, obecnie: **zachodnie stany USA, wschodnie stany USA, Europa Zachodnia, Europa Północna, Australia Wschodnia, Azja południowo-wschodnia.** Ponieważ systemu Linux na ASE jest funkcja w wersji zapoznawczej, nie zaznaczaj ASE miał utworzony przed tej wersji zapoznawczej.
    >

7. Wybierz **warstwa cenowa**i wybierz jedną z **izolowany** cennik jednostki SKU. Jeśli wybierzesz **izolowany** karty jednostki SKU i lokalizacji, która nie jest ASE nowe ASE jest tworzony w tej lokalizacji. Aby rozpocząć proces tworzenia ASE, zaznacz **wybierz**. **Izolowany** jednostka SKU jest dostępna tylko w połączeniu z ASE. Również nie można użyć innych SKU cenową w elemencie ASE innych niż **izolowany**. 

    * Dla systemu Linux na podgląd ASE rabat 50% będzie dotyczyć izolowanego jednostki SKU (na nie będzie żadnych rabat opłata płaski ASE, sam).

    ![Wybór warstwy cenowej][3]

8. Wprowadź nazwę użytkownika ASE. Ta nazwa jest używana w nazwie adresowanego dla aplikacji. Jeśli nazwa ASE _appsvcenvdemo_, nazwa domeny jest *. appsvcenvdemo.p.azurewebsites.net*. Jeśli tworzysz aplikację o nazwie *mytestapp*, jest adresowanego na mytestapp.appsvcenvdemo.p.azurewebsites.net. Nie można użyć biały znak w nazwie. Jeśli używasz wielkich liter, nazwa domeny jest całkowita wersji małe o tej nazwie.

    ![Nowa nazwa planu usługi aplikacji][4]

9. Określ szczegóły sieci wirtualnej platformy Azure. Wybierz opcję **tworzenia nowych** lub **wybierz istniejącą**. Możliwość wybrania istniejącej sieci wirtualnej jest dostępna tylko wtedy, gdy sieć wirtualną w wybranym regionie. W przypadku wybrania **Utwórz nowy**, wprowadź nazwę sieci wirtualnej. Utworzono nową sieć wirtualną Menedżera zasobów o tej nazwie. Używa przestrzeni adresowej `192.168.250.0/23` w wybranym regionie. W przypadku wybrania **wybierz istniejącą**, musisz:

    a. Wybierz blok adresów sieci wirtualnej, jeśli masz więcej niż jeden.

    b. Wprowadź nazwę nowej podsieci.

    c. Wybierz rozmiar podsieci. *Pamiętaj, aby wybrać rozmiar wystarczająco duża, aby uwzględnić przyszły wzrost Twojej ASE.* Firma Microsoft zaleca `/25`, który 128 adresów i może obsłużyć ASE rozmiar maksymalny. Nie zaleca się `/28`, na przykład tylko 16 adresów nie są dostępne. Infrastruktura używa adresów co najmniej siedmiu i Azure Networking używa innego 5. W `/28` podsieci, w przypadku pozostałych składającą się maksymalnie z 4 wystąpieniach planu usługi aplikacji — dla zewnętrznych ASE skalowania i tylko 3 wystąpienia planu usług aplikacji dla ASE ILB.

    d. Wybierz zakres adresów IP podsieci.

10. Wybierz **Utwórz** utworzyć ASE. Ten proces tworzy również plan usługi aplikacji i aplikacji. ASE planu usługi aplikacji i aplikacji są pod tą samą subskrypcją, a także w tej samej grupie zasobów. Jeśli Twoje ASE wymaga oddzielnej grupie zasobów lub należy ASE ILB, wykonaj kroki, aby utworzyć ASE samodzielnie.

## <a name="create-an-ase-and-a-linux-web-app-using-a-custom-docker-image-together"></a>Utwórz ASE i aplikacji sieci web systemu Linux ze sobą przy użyciu niestandardowego obrazu Docker

1. W [portalu Azure](https://portal.azure.com/), **Utwórz zasób** > **sieci Web i mobilność** > **aplikacji sieci Web dla kontenerów.** 

    ![Tworzenie aplikacji sieci Web][7]

2. Wybierz subskrypcję. Aplikacji a ASE są tworzone w tej samej subskrypcji.

3. Wybierz lub utwórz grupę zasobów. Z grupami zasobów można zarządzać jako jednostka powiązanych zasobów systemu Azure. Grupy zasobów są także przydatne podczas ustanawiania zasady kontroli dostępu opartej na rolach dla aplikacji. Aby uzyskać więcej informacji, zobacz [Omówienie usługi Azure Resource Manager][ARMOverview].

4. Wybierz plan usługi aplikacji, a następnie wybierz **Utwórz nowy**. Linux aplikacji sieci web i aplikacji sieci web systemu Windows nie może należeć do tego samego planu usługi App Service, ale może być w tym samym środowisku usługi aplikacji. 

    ![Nowy plan usługi App Service][8]

5. W **lokalizacji** listy rozwijanej wybierz region, w którym chcesz utworzyć ASE. W przypadku wybrania istniejącego ASE nowe ASE nie jest tworzone. Plan usługi aplikacji jest tworzony w ASE wybrany. 

    > [!NOTE]
    > Linux na ASE jest włączona tylko w regionach, 6, obecnie: **zachodnie stany USA, wschodnie stany USA, Europa Zachodnia, Europa Północna, Australia Wschodnia, Azja południowo-wschodnia.** Ponieważ systemu Linux na ASE jest funkcja w wersji zapoznawczej, nie zaznaczaj ASE miał utworzony przed tej wersji zapoznawczej.
    >

6. Wybierz **warstwa cenowa**i wybierz jedną z **izolowany** cennik jednostki SKU. Jeśli wybierzesz **izolowany** karty jednostki SKU i lokalizacji, która nie jest ASE nowe ASE jest tworzony w tej lokalizacji. Aby rozpocząć proces tworzenia ASE, zaznacz **wybierz**. **Izolowany** jednostka SKU jest dostępna tylko w połączeniu z ASE. Również nie można użyć innych SKU cenową w elemencie ASE innych niż **izolowany**. 

    * Dla systemu Linux na podgląd ASE rabat 50% będzie dotyczyć izolowanego jednostki SKU (na nie będzie żadnych rabat opłata płaski ASE, sam).

    ![Wybór warstwy cenowej][3]

7. Wprowadź nazwę użytkownika ASE. Ta nazwa jest używana w nazwie adresowanego dla aplikacji. Jeśli nazwa ASE _appsvcenvdemo_, nazwa domeny jest *. appsvcenvdemo.p.azurewebsites.net*. Jeśli tworzysz aplikację o nazwie *mytestapp*, jest adresowanego na mytestapp.appsvcenvdemo.p.azurewebsites.net. Nie można użyć biały znak w nazwie. Jeśli używasz wielkich liter, nazwa domeny jest całkowita wersji małe o tej nazwie.

    ![Nowa nazwa planu usługi aplikacji][4]

8. Określ szczegóły sieci wirtualnej platformy Azure. Wybierz opcję **tworzenia nowych** lub **wybierz istniejącą**. Możliwość wybrania istniejącej sieci wirtualnej jest dostępna tylko wtedy, gdy sieć wirtualną w wybranym regionie. W przypadku wybrania **Utwórz nowy**, wprowadź nazwę sieci wirtualnej. Utworzono nową sieć wirtualną Menedżera zasobów o tej nazwie. Używa przestrzeni adresowej `192.168.250.0/23` w wybranym regionie. W przypadku wybrania **wybierz istniejącą**, musisz:

    a. Wybierz blok adresów sieci wirtualnej, jeśli masz więcej niż jeden.

    b. Wprowadź nazwę nowej podsieci.

    c. Wybierz rozmiar podsieci. *Pamiętaj, aby wybrać rozmiar wystarczająco duża, aby uwzględnić przyszły wzrost Twojej ASE.* Firma Microsoft zaleca `/25`, który 128 adresów i może obsłużyć ASE rozmiar maksymalny. Nie zaleca się `/28`, na przykład tylko 16 adresów nie są dostępne. Infrastruktura używa adresów co najmniej siedmiu i Azure Networking używa innego 5. W `/28` podsieci, w przypadku pozostałych składającą się maksymalnie z 4 wystąpieniach planu usługi aplikacji — dla zewnętrznych ASE skalowania i tylko 3 wystąpienia planu usług aplikacji dla ASE ILB.

    d. Wybierz zakres adresów IP podsieci.

9.  Wybierz opcję "Konfigurowanie kontenera".
    * Wprowadź nazwę niestandardowego obrazu (możesz użyć rejestru kontenera platformy Azure, Centrum Docker i prywatnych rejestru). Jeśli nie chcesz użyć własnych niestandardowych kontenera, można po prostu przenieś swój kod i użyć wbudowanych obrazu z usługi aplikacji w systemie Linux przy użyciu powyższych instrukcji. 

    ! [Skonfiguruj kontener] [9]

10. Wybierz **Utwórz** utworzyć ASE. Ten proces tworzy również plan usługi aplikacji i aplikacji. ASE planu usługi aplikacji i aplikacji są pod tą samą subskrypcją, a także w tej samej grupie zasobów. Jeśli Twoje ASE wymaga oddzielnej grupie zasobów lub należy ASE ILB, wykonaj kroki, aby utworzyć ASE samodzielnie.


## <a name="create-an-ase-by-itself"></a>Utwórz ASE samodzielnie ##

Tworzenie autonomicznych ASE, nie ma nic w nim. Pusty ASE nadal wiąże miesięcznych opłat infrastruktury. Wykonaj poniższe kroki tworzenia ASE z ILB lub utworzyć ASE w jego własnej grupy zasobów. Po utworzeniu sieci ASE mogą tworzyć aplikacje w nim przy użyciu standardowego procesu. Twoje nowe ASE wybierz jako lokalizację.

1. Wyszukiwanie Azure Marketplace w celu **środowiska usługi aplikacji**, lub wybierz **Utwórz zasób** > **Web Mobile** > **aplikacji Usługi środowiska**. 

2. Wprowadź nazwę użytkownika ASE. Ta nazwa jest używana na potrzeby aplikacji utworzonych w ASE. Jeśli nazwa jest *mynewdemoase*, nazwa domeny podrzędnej jest *. mynewdemoase.p.azurewebsites.net*. Jeśli tworzysz aplikację o nazwie *mytestapp*, jest adresowanego na mytestapp.mynewdemoase.p.azurewebsites.net. Nie można użyć biały znak w nazwie. Jeśli używasz wielkich liter, nazwa domeny jest całkowita małe wersja nazwy. Jeśli używasz ILB, nazwę ASE nie jest używana w Twojej domeny podrzędnej, ale zamiast tego jest jasno określone podczas tworzenia ASE.

    ![Nazewnictwo ASE][5]

3. Wybierz subskrypcję. Ta subskrypcja jest również używanego przez wszystkie aplikacje w ASE. Twoje ASE nie można umieścić w sieci wirtualnej, który znajduje się w innej subskrypcji.

4. Wybierz lub określ nową grupę zasobów. Grupa zasobów używany do Twojej ASE muszą być takie same, używany dla sieci wirtualnej. W przypadku wybrania istniejącej sieci wirtualnej wybranej grupy zasobów dla Twojego ASE jest aktualizowany w celu odzwierciedlenia z sieci wirtualnej. *Możesz utworzyć ASE z grupą zasobów, która różni się od grupy zasobów w sieci wirtualnej, jeśli używasz szablonu usługi Resource Manager.* Aby utworzyć ASE na podstawie szablonu, zobacz [tworzenie środowiska usługi aplikacji na podstawie szablonu][MakeASEfromTemplate].

    ![Wybór grupy zasobów][6]

5. Wybierz sieć wirtualną i lokalizacji. Możesz utworzyć nową sieć wirtualną lub wybierz istniejącej sieci wirtualnej: 

    * W przypadku wybrania nowej sieci wirtualnej możesz określić nazwę i lokalizację. Jeśli zamierzasz hostować aplikacje systemu Linux w tym środowisku ASE, w tej chwili obsługiwanych jest tylko 6 następujących regionów: **Zachodnie stany USA, Wschodnie stany USA, Europa Zachodnia, Europa Północna, Australia Wschodnia, Azja Południowo-Wschodnia.** 
    
    * Nowej sieci wirtualnej ma 192.168.250.0/23 zakres adresów i podsieć o nazwie domyślnej. Podsieć jest zdefiniowana jako 192.168.250.0/24. Można wybrać tylko sieć wirtualną Menedżera zasobów. **Typu VIP** wybór określa Twojej ASE są bezpośrednio dostępne z Internetu (zewnętrzne) lub korzysta z ILB. Aby dowiedzieć się więcej o tych opcjach, zobacz [tworzenie i używanie wewnętrznego modułu równoważenia obciążenia z środowiska usługi aplikacji][MakeILBASE]. 

      * W przypadku wybrania **zewnętrznych** dla **typu VIP**, możesz wybrać ile zewnętrzne adresy IP systemu jest tworzony z celów opartych na protokole SSL. 
    
      * W przypadku wybrania **wewnętrzne** dla **typu VIP**, należy określić domeny, który korzysta z ASE. ASE można wdrożyć w sieci wirtualnej, które są używane zakresy adresów publicznych lub prywatnych. Aby użyć sieci wirtualnej z zakresem adresów publicznych, należy utworzyć sieć wirtualną wcześniejsze. 
    
    * W przypadku wybrania istniejącej sieci wirtualnej nowa podsieć jest tworzony podczas tworzenia ASE. *Nie można użyć podsieci wstępnie utworzone w portalu. Jeśli używasz szablonu usługi Resource Manager, możesz utworzyć ASE się na istniejącą podsieć.* Aby utworzyć ASE na podstawie szablonu, zobacz [tworzenie środowiska usługi aplikacji na podstawie szablonu][MakeASEfromTemplate].

## <a name="app-service-environment-v1"></a>Środowisko usługi App Service — wersja 1 ##

Można tworzyć wystąpień pierwszej wersji środowiska usługi aplikacji (ASEv1). Aby uruchomić ten proces, Wyszukaj witryny Marketplace w celu **v1 środowiska usługi aplikacji**. Możesz utworzyć ASE w taki sam sposób jak utworzyć autonomiczny ASE. Po zakończeniu Twojej ASEv1 ma dwa końce front i dwóch pracowników. Z ASEv1 można zarządzać interfejsy i pracowników. Są one automatycznie dodane, podczas tworzenia planów usługi aplikacji. Front kończy się działać jako punktów końcowych HTTP/HTTPS i wysyłać ruch do pracowników. Pracownicy są role, które hosta aplikacji. Po utworzeniu sieci ASE, można dostosować liczba przedniego kończy się i pracowników. 

Aby dowiedzieć się więcej na temat ASEv1, zobacz [wprowadzenie do środowiska usługi aplikacji v1][ASEv1Intro]. Aby uzyskać więcej informacji na temat skalowania, zarządzanie i monitorowanie ASEv1, zobacz [sposobu konfigurowania środowiska usługi aplikacji][ConfigureASEv1].

<!--Image references-->
[1]: ./media/how_to_create_an_external_app_service_environment/createexternalase-create.png
[2]: ./media/how_to_create_an_external_app_service_environment/createexternalase-aspcreate.png
[3]: ./media/how_to_create_an_external_app_service_environment/createexternalase-pricing.png
[4]: ./media/how_to_create_an_external_app_service_environment/createexternalase-embeddedcreate.png
[5]: ./media/how_to_create_an_external_app_service_environment/createexternalase-standalonecreate.png
[6]: ./media/how_to_create_an_external_app_service_environment/createexternalase-network.png
[7]: ./media/how_to_create_an_external_app_service_environment/createexternalase-createwafc.png
[8]: ./media/how_to_create_an_external_app_service_environment/createexternalase-aspcreatewafc.png
[8]: ./media/how_to_create_an_external_app_service_environment/createexternalase-configurecontainer.png



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
[webapps]: ../app-service-web-overview.md
[mobileapps]: ../../app-service-mobile/app-service-mobile-value-prop.md
[Functions]: ../../azure-functions/index.yml
[Pricing]: http://azure.microsoft.com/pricing/details/app-service/
[ARMOverview]: ../../azure-resource-manager/resource-group-overview.md
