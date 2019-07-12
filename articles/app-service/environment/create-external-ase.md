---
title: Tworzenie środowiska usługi aplikacji zewnętrznej — platformy Azure
description: Wyjaśnia, jak utworzyć środowisko usługi App Service podczas tworzenia aplikacji lub autonomiczny
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
ms.custom: seodec18
ms.openlocfilehash: e04dfa4148213e88aa46e464a31cdd9b6125e0bf
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/09/2019
ms.locfileid: "67705758"
---
# <a name="create-an-external-app-service-environment"></a>Tworzenie środowiska usługi aplikacji zewnętrznej

Środowisko Azure App Service Environment to wdrożenie usługi Azure App Service w podsieci sieci wirtualnej platformy Azure (VNet).

> [!NOTE]
> Każdy środowiska App Service Environment ma Virtual IP (VIP), która może służyć do kontaktowania się z usługi App Service Environment.

Istnieją dwa sposoby wdrażania środowiska App Service Environment (ASE):

- Z wirtualnym adresem IP będącym zewnętrznym adresem IP — jest to często nazywane zewnętrznym środowiskiem ASE.
- Z Wirtualnym adresem IP będącym wewnętrznego adresu IP często nazywane środowisko ASE z wewnętrznym modułem równoważenia obciążenia, ponieważ wewnętrzny punkt końcowy wewnętrznego modułu równoważenia obciążenia (ILB).

W tym artykule pokazano, jak tworzenie zewnętrznego środowiska ASE. Aby uzyskać omówienie środowiska ASE, zobacz [wprowadzenie do usługi App Service Environment][Intro]. For information on how to create an ILB ASE, see [Create and use an ILB ASE][MakeILBASE].

## <a name="before-you-create-your-ase"></a>Przed przystąpieniem do tworzenia środowiska ASE

Po utworzeniu środowiska ASE, nie można zmienić następujące czynności:

- Location
- Subscription
- Resource group
- Używane w sieci wirtualnej
- Podsieć używana
- Rozmiar podsieci

> [!NOTE]
> Po wybraniu sieci wirtualnej i określ podsieć, upewnij się, że jest wystarczająco duży, aby uwzględnić przyszły rozwój i skalowanie potrzeb. Firma Microsoft zaleca rozmiar `/24` za pomocą 256 adresów.
>

## <a name="three-ways-to-create-an-ase"></a>Trzy sposoby, aby utworzyć środowisko ASE

Istnieją trzy sposoby, aby utworzyć środowisko ASE:

- **Podczas tworzenia planu usługi App Service**. Ta metoda tworzy plan usługi App Service i środowisko ASE w jednym kroku.
- **Jako autonomiczny akcji**. Ta metoda tworzy autonomiczne środowisko ASE, która jest środowisko ASE pustą. Ta metoda jest bardziej zaawansowany proces, aby utworzyć środowisko ASE. Umożliwia ona tworzenie środowiska ASE z wewnętrznym modułem równoważenia obciążenia.
- **Przy użyciu szablonu usługi Azure Resource Manager**. Ta metoda jest dla użytkowników zaawansowanych. Aby uzyskać więcej informacji, zobacz [utworzyć środowisko ASE na podstawie szablonu][MakeASEfromTemplate].

Zewnętrzne środowisko ASE ma publicznych adresów VIP, co oznacza, że cały ruch HTTP/HTTPS do aplikacji w środowisku ASE trafienia adresu IP dostępne za pośrednictwem Internetu. Środowisko ASE z wewnętrznym modułem równoważenia obciążenia ma adres IP w podsieci używanej przez środowisko ASE. Aplikacje hostowane w środowisku ASE z wewnętrznym modułem równoważenia obciążenia nie są bezpośrednio połączone z Internetem.

## <a name="create-an-ase-and-an-app-service-plan-together"></a>Jednocześnie utworzyć środowisko ASE i plan usługi App Service

Plan usługi App Service jest kontenerem aplikacji. Podczas tworzenia aplikacji w usłudze App Service, wybierz lub Utwórz plan usługi App Service. Środowiska usługi App Service, przytrzymaj planów usługi App Service i planów usługi App Service przechowywania aplikacji.

Aby utworzyć środowisko ASE, podczas tworzenia planu usługi App Service:

1. W [witryny Azure portal](https://portal.azure.com/), wybierz opcję **Utwórz zasób** > **sieci Web i mobilność** > **aplikacji sieci Web**.

    ![Tworzenie aplikacji sieci Web][1]

2. Wybierz subskrypcję. Aplikacja i środowisko ASE są tworzone w tej samej subskrypcji.

3. Wybierz lub utwórz grupę zasobów. Przy użyciu grup zasobów powiązanych zasobów platformy Azure można zarządzać jako jednostka. Grupy zasobów są także przydatne podczas ustanawiania zasad kontroli dostępu opartej na rolach dla aplikacji. Aby uzyskać więcej informacji, zobacz [Omówienie usługi Azure Resource Manager][ARMOverview].

4. Wybierz system operacyjny (Windows, Linux i Docker). 

5. Wybierz plan usługi App Service, a następnie wybierz **Utwórz nowy**. Aplikacje internetowe systemu Linux i Windows, aplikacje sieci web nie może być w tym samym planie usługi App Service, ale mogą znajdować się w tym samym środowisku usługi App Service. 

    ![Nowy plan usługi App Service][2]

6. W **lokalizacji** listy rozwijanej wybierz region, w którym chcesz utworzyć środowisko ASE. Jeśli wybierzesz istniejącego środowiska ASE, nowego środowiska ASE nie jest tworzony. Plan usługi App Service jest tworzony w wybranym środowisku ASE. 

7. Wybierz **warstwa cenowa**i wybierz jedną z **izolowany** wycena jednostek SKU. Jeśli wybierzesz **izolowany** karty jednostki SKU i lokalizacji, która nie jest środowisko ASE, nowe środowisko ASE jest tworzony w tej lokalizacji. Aby uruchomić proces, aby utworzyć środowisko ASE, zaznacz **wybierz**. **Izolowany** jednostka SKU jest dostępna tylko w połączeniu z ASE. Również nie można użyć innych SKU cen w środowisku ASE innych niż **izolowany**. 

    ![Wybór warstwy cenowej][3]

8. Wprowadź nazwę środowiska ASE. Ta nazwa jest używana w nazwie mogą być adresowane dla aplikacji. Jeśli nazwa środowiska ASE jest _appsvcenvdemo_, nazwa domeny jest *. appsvcenvdemo.p.azurewebsites.net*. Jeśli tworzysz aplikację o nazwie *MojaAplikacja*, jest adresowalnej na mytestapp.appsvcenvdemo.p.azurewebsites.net. Nie można użyć biały znak w nazwie. Użycie wielkich liter, nazwa domeny jest całkowita wersję o takiej nazwie.

    ![Nowa nazwa planu usługi App Service][4]

9. Podaj szczegóły sieci wirtualnej platformy Azure. Wybierz opcję **tworzenia nowych** lub **wybierz istniejący**. Możliwość wybrania istniejącej sieci wirtualnej jest dostępna tylko wtedy, gdy masz sieć wirtualną w wybranym regionie. Jeśli wybierzesz **Utwórz nowy**, wprowadź nazwę sieci wirtualnej. Utworzono nową sieć wirtualną usługi Resource Manager przy użyciu tej nazwy. Używa ona przestrzeń adresową `192.168.250.0/23` w wybranym regionie. Jeśli wybierzesz **wybierz istniejącą**, musisz:

    a. Blok adresów sieci wirtualnej, należy wybrać, jeśli masz więcej niż jeden.

    b. Wprowadź nazwę nowej podsieci.

    c. Wybierz rozmiar podsieci. *Pamiętaj, aby wybrać rozmiar wystarczająco dużą do obsługi przyszłego rozwoju środowiska ASE.* Firma Microsoft zaleca `/24`, który zapewnia 128 adresów i może obsłużyć środowiska ASE o maksymalnym rozmiarze. Nie zaleca się `/28`, na przykład, ponieważ nie są dostępne tylko 16 adresów. Infrastruktury przy użyciu co najmniej siedmiu adresów, a inny 5 korzysta z sieci platformy Azure. W `/28` podsieci, w przypadku pozostałych dłuższą skalowanie 4 wystąpień planu usługi App Service dla zewnętrznego środowiska ASE i tylko 3 wystąpień planu usługi App Service, za środowisko ASE wewnętrznego modułu równoważenia obciążenia.

    d. Wybierz zakres adresów IP podsieci.

10. Wybierz **Utwórz** do tworzenia środowiska ASE. Ten proces tworzy również plan usługi App Service i aplikację. Środowisko ASE, plan usługi App Service i aplikację są wszystkie tej samej subskrypcji, a także w tej samej grupie zasobów. Jeśli środowisko ASE wymaga oddzielnej grupie zasobów lub jeśli potrzebujesz środowisko ASE z wewnętrznym modułem równoważenia obciążenia, postępuj zgodnie z instrukcjami, aby utworzyć środowisko ASE przez siebie.

## <a name="create-an-ase-and-a-linux-web-app-using-a-custom-docker-image-together"></a>Tworzenie środowiska ASE i aplikację internetową systemu Linux, ze sobą przy użyciu niestandardowego obrazu platformy Docker

1. W [witryny Azure portal](https://portal.azure.com/), **Utwórz zasób** > **sieci Web i mobilność** > **Web App for Containers.** 

    ![Tworzenie aplikacji sieci Web][7]

1. Wybierz subskrypcję. Aplikacja i środowisko ASE są tworzone w tej samej subskrypcji.

1. Wybierz lub utwórz grupę zasobów. Przy użyciu grup zasobów powiązanych zasobów platformy Azure można zarządzać jako jednostka. Grupy zasobów są także przydatne podczas ustanawiania zasad kontroli dostępu opartej na rolach dla aplikacji. Aby uzyskać więcej informacji, zobacz [Omówienie usługi Azure Resource Manager][ARMOverview].

1. Wybierz plan usługi App Service, a następnie wybierz **Utwórz nowy**. Aplikacje internetowe systemu Linux i Windows, aplikacje sieci web nie może być w tym samym planie usługi App Service, ale mogą znajdować się w tym samym środowisku usługi App Service. 

    ![Nowy plan usługi App Service][8]

1. W **lokalizacji** listy rozwijanej wybierz region, w którym chcesz utworzyć środowisko ASE. Jeśli wybierzesz istniejącego środowiska ASE, nowego środowiska ASE nie jest tworzony. Plan usługi App Service jest tworzony w wybranym środowisku ASE. 

1. Wybierz **warstwa cenowa**i wybierz jedną z **izolowany** wycena jednostek SKU. Jeśli wybierzesz **izolowany** karty jednostki SKU i lokalizacji, która nie jest środowisko ASE, nowe środowisko ASE jest tworzony w tej lokalizacji. Aby uruchomić proces, aby utworzyć środowisko ASE, zaznacz **wybierz**. **Izolowany** jednostka SKU jest dostępna tylko w połączeniu z ASE. Również nie można użyć innych SKU cen w środowisku ASE innych niż **izolowany**. 

    ![Wybór warstwy cenowej][3]

1. Wprowadź nazwę środowiska ASE. Ta nazwa jest używana w nazwie mogą być adresowane dla aplikacji. Jeśli nazwa środowiska ASE jest _appsvcenvdemo_, nazwa domeny jest *. appsvcenvdemo.p.azurewebsites.net*. Jeśli tworzysz aplikację o nazwie *MojaAplikacja*, jest adresowalnej na mytestapp.appsvcenvdemo.p.azurewebsites.net. Nie można użyć biały znak w nazwie. Użycie wielkich liter, nazwa domeny jest całkowita wersję o takiej nazwie.

    ![Nowa nazwa planu usługi App Service][4]

1. Podaj szczegóły sieci wirtualnej platformy Azure. Wybierz opcję **tworzenia nowych** lub **wybierz istniejący**. Możliwość wybrania istniejącej sieci wirtualnej jest dostępna tylko wtedy, gdy masz sieć wirtualną w wybranym regionie. Jeśli wybierzesz **Utwórz nowy**, wprowadź nazwę sieci wirtualnej. Utworzono nową sieć wirtualną usługi Resource Manager przy użyciu tej nazwy. Używa ona przestrzeń adresową `192.168.250.0/23` w wybranym regionie. Jeśli wybierzesz **wybierz istniejącą**, musisz:

    a. Blok adresów sieci wirtualnej, należy wybrać, jeśli masz więcej niż jeden.

    b. Wprowadź nazwę nowej podsieci.

    c. Wybierz rozmiar podsieci. *Pamiętaj, aby wybrać rozmiar wystarczająco dużą do obsługi przyszłego rozwoju środowiska ASE.* Firma Microsoft zaleca `/24`, który zapewnia 128 adresów i może obsłużyć środowiska ASE o maksymalnym rozmiarze. Nie zaleca się `/28`, na przykład, ponieważ nie są dostępne tylko 16 adresów. Infrastruktury przy użyciu co najmniej siedmiu adresów, a inny 5 korzysta z sieci platformy Azure. W `/28` podsieci, w przypadku pozostałych dłuższą skalowanie 4 wystąpień planu usługi App Service dla zewnętrznego środowiska ASE i tylko 3 wystąpień planu usługi App Service, za środowisko ASE wewnętrznego modułu równoważenia obciążenia.

    d. Wybierz zakres adresów IP podsieci.

1.  Wybierz pozycję "Konfigurowanie kontenera".
    * Wprowadź nazwę niestandardowego obrazu (możesz użyć usługi Azure Container Registry i Docker Hub, prywatnego rejestru). Jeśli nie chcesz użyć własnego kontenera niestandardowego, można po prostu przenieść kod i wbudowanego obrazu za pomocą usługi App Service w systemie Linux, korzystając z powyższych instrukcji. 

    ![Konfiguruj kontener][9]

1. Wybierz **Utwórz** do tworzenia środowiska ASE. Ten proces tworzy również plan usługi App Service i aplikację. Środowisko ASE, plan usługi App Service i aplikację są wszystkie tej samej subskrypcji, a także w tej samej grupie zasobów. Jeśli środowisko ASE wymaga oddzielnej grupie zasobów lub jeśli potrzebujesz środowisko ASE z wewnętrznym modułem równoważenia obciążenia, postępuj zgodnie z instrukcjami, aby utworzyć środowisko ASE przez siebie.


## <a name="create-an-ase-by-itself"></a>Tworzenie środowiska ASE samodzielnie

Jeśli tworzysz autonomiczne środowisko ASE, go nie ma nic w nim. Środowisko ASE pusty nadal opłatę miesięczną infrastruktury. Wykonaj następujące kroki, aby utworzyć środowisko ASE z wewnętrznym modułem równoważenia obciążenia lub utworzyć środowisko ASE w jego własnej grupie zasobów. Po utworzeniu środowiska ASE, można utworzyć aplikacji w nim przy użyciu standardowego procesu. Wybierz nowe środowisko ASE jako lokalizację.

1. Wyszukaj w portalu Azure Marketplace **środowiska App Service Environment**, lub wybierz **Utwórz zasób** > **Web Mobile** > **aplikacji Usługa środowiska**. 

1. Wprowadź nazwę środowiska ASE. Ta nazwa jest używana w przypadku aplikacji tworzonych w środowisku ASE. Jeśli nazwa jest *mynewdemoase*, nazwy domeny podrzędnej jest *. mynewdemoase.p.azurewebsites.net*. Jeśli tworzysz aplikację o nazwie *MojaAplikacja*, jest adresowalnej na mytestapp.mynewdemoase.p.azurewebsites.net. Nie można użyć biały znak w nazwie. Użycie wielkich liter, nazwa domeny jest całkowita wersję nazwy. Jeśli korzystasz z wewnętrznym modułem równoważenia obciążenia, nazwa środowiska ASE nie jest używany w Twojej domeny podrzędnej, ale zamiast tego jest jasno określone podczas tworzenia środowiska ASE.

    ![Nazewnictwo środowiska ASE][5]

1. Wybierz subskrypcję. Ta subskrypcja jest także jednym, używanego przez wszystkie aplikacje w środowisku ASE. Środowisko ASE nie można umieścić w sieci wirtualnej, która znajduje się w innej subskrypcji.

1. Wybierz lub określ nową grupę zasobów. Grupę zasobów używaną dla środowiska ASE musi być taka sama, taki, który jest używany dla sieci wirtualnej. Wybranie istniejącej sieci wirtualnej, wybranej grupy zasobów dla środowiska ASE jest aktualizowana w celu odzwierciedlenia zmiany sieci wirtualnej. *Możesz utworzyć środowisko ASE z grupą zasobów, która jest inna niż grupa zasobów sieci wirtualnej, w przypadku używania szablonu usługi Resource Manager.* Aby utworzyć środowisko ASE na podstawie szablonu, zobacz [tworzenie środowiska usługi App Service na podstawie szablonu][MakeASEfromTemplate].

    ![Wybór grupy zasobów][6]

1. Wybierz sieć wirtualną i lokalizacji. Możesz utworzyć nową sieć wirtualną lub wybierz istniejącej sieci wirtualnej: 

    * W przypadku wybrania nowej sieci wirtualnej możesz określić nazwę i lokalizację. 
    
    * Nową sieć wirtualną ma 192.168.250.0/23 zakres adresów i podsieć o nazwie domyślnej. Podsieć jest zdefiniowany jako 192.168.250.0/24. Można wybrać tylko z siecią wirtualną usługi Resource Manager. **Typ adresu VIP** ten wybór decyduje, jeśli środowisko ASE są bezpośrednio dostępne z Internetu (zewnętrzny) lub korzysta z wewnętrznym modułem równoważenia obciążenia. Aby dowiedzieć się więcej o tych opcjach, zobacz [tworzenia i używania wewnętrznego modułu równoważenia obciążenia ze środowiskiem usługi App Service environment][MakeILBASE]. 

      * Jeśli wybierzesz **zewnętrznych** dla **typ adresu VIP**, można wybrać liczbę zewnętrzne adresy IP systemu jest tworzony z celów SSL opartego na protokole IP. 
    
      * Jeśli wybierzesz **wewnętrzne** dla **typ adresu VIP**, należy określić domeny, która używa środowiska ASE. Środowisko ASE można wdrożyć w sieci wirtualnej, która używa zakresów adresów publicznych lub prywatnych. Aby użyć sieci wirtualnej z zakresem adresów publicznych, musisz utworzyć sieci wirtualnej wcześniejsze. 
    
    * Jeśli wybierzesz istniejącej sieci wirtualnej, nowa podsieć jest tworzony po utworzeniu środowiska ASE. *Nie można użyć wstępnie utworzonej podsieci, w portalu. Jeśli używasz szablonu usługi Resource Manager, możesz utworzyć środowisko ASE się na istniejącą podsieć.* Aby utworzyć środowisko ASE na podstawie szablonu, zobacz [tworzenie środowiska usługi App Service na podstawie szablonu][MakeASEfromTemplate].

## <a name="app-service-environment-v1"></a>Środowisko usługi App Service — wersja 1

Nadal można utworzyć wystąpienia elementu pierwszą wersję środowiska App Service Environment (środowiska ASEv1). Aby rozpocząć ten proces, portalu Marketplace wyszukaj hasło **środowiska App Service Environment v1**. Należy utworzyć środowisko ASE w taki sam sposób, jak tworzyć autonomiczne środowisko ASE. Po zakończeniu program Twojego środowiska ASEv1 ma dwa Frontony i dwa procesy robocze. Za pomocą środowiska ASEv1 muszą zarządzać frontonami ani procesami roboczymi. Są dodawane automatycznie, gdy tworzysz swoje plany usługi App Service. Frontony działanie jako punktów końcowych HTTP/HTTPS i wysyłania ruchu do pracowników. Procesy robocze to role, które hostują swoje aplikacje. Po utworzeniu środowiska ASE, można dostosować ilość frontonami ani procesami roboczymi. 

Aby dowiedzieć się więcej na temat środowiska ASEv1, zobacz [wprowadzenie do środowiska App Service Environment w wersji 1][ASEv1Intro]. For more information on scaling, managing, and monitoring ASEv1, see [How to configure an App Service Environment][ConfigureASEv1].

<!--Image references-->
[1]: ./media/how_to_create_an_external_app_service_environment/createexternalase-create.png
[2]: ./media/how_to_create_an_external_app_service_environment/createexternalase-aspcreate.png
[3]: ./media/how_to_create_an_external_app_service_environment/createexternalase-pricing.png
[4]: ./media/how_to_create_an_external_app_service_environment/createexternalase-embeddedcreate.png
[5]: ./media/how_to_create_an_external_app_service_environment/createexternalase-standalonecreate.png
[6]: ./media/how_to_create_an_external_app_service_environment/createexternalase-network.png
[7]: ./media/how_to_create_an_external_app_service_environment/createexternalase-createwafc.png
[8]: ./media/how_to_create_an_external_app_service_environment/createexternalase-aspcreatewafc.png
[9]: ./media/how_to_create_an_external_app_service_environment/createexternalase-configurecontainer.png



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
