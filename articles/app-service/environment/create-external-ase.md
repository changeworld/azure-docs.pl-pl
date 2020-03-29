---
title: Tworzenie zewnętrznego ase
description: Dowiedz się, jak utworzyć środowisko usługi App Service z aplikacją w nim lub utworzyć autonomiczny (pusty) ASE.
author: ccompy
ms.assetid: 94dd0222-b960-469c-85da-7fcb98654241
ms.topic: article
ms.date: 06/13/2017
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: 6c4838e3226b91cbb5d6f86b83266a986418c120
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75430515"
---
# <a name="create-an-external-app-service-environment"></a>Tworzenie środowiska usługi aplikacji zewnętrznych

Środowisko Azure App Service Environment to wdrożenie usługi Azure App Service w podsieci sieci wirtualnej platformy Azure (VNet).

> [!NOTE]
> Każde środowisko usługi aplikacji ma wirtualny adres IP (VIP), który może służyć do kontaktowania się ze środowiskiem usługi app service.

Istnieją dwa sposoby wdrażania środowiska App Service Environment (ASE):

- Z wirtualnym adresem IP będącym zewnętrznym adresem IP — jest to często nazywane zewnętrznym środowiskiem ASE.
- Z adresu VIP na wewnętrzny adres IP, często nazywany ASE ILB, ponieważ wewnętrzny punkt końcowy jest wewnętrzny moduł równoważenia obciążenia (ILB).

W tym artykule pokazano, jak utworzyć zewnętrzny ase. Aby zapoznać się z omówieniem środowiska ASE, zobacz [Wprowadzenie do środowiska usługi app service][Intro]. Aby uzyskać informacje na temat tworzenia ase ILB, zobacz [Tworzenie i używanie ASE ILB][MakeILBASE].

## <a name="before-you-create-your-ase"></a>Przed utworzeniem ase

Po utworzeniu ase nie można zmienić następujących elementów:

- Lokalizacja
- Subskrypcja
- Grupa zasobów
- Używana sieci wirtualnej
- Używana podsieć
- Rozmiar podsieci

> [!NOTE]
> Po wybraniu sieci wirtualnej i określ podsieci upewnij się, że jest wystarczająco duża, aby dostosować się do przyszłych potrzeb wzrostu i skalowania. Zalecamy rozmiar `/24` z 256 adresami.
>

## <a name="three-ways-to-create-an-ase"></a>Trzy sposoby tworzenia ASE

Istnieją trzy sposoby tworzenia ASE:

- **Podczas tworzenia planu usługi aplikacji**. Ta metoda tworzy ASE i plan usługi app service w jednym kroku.
- **Jako samodzielna akcja**. Ta metoda tworzy autonomiczny ASE, który jest ASE z niczego w nim. Ta metoda jest bardziej zaawansowany proces, aby utworzyć ASE. Służy do tworzenia ase z równoważenia obciążenia sieciowego.
- **Z szablonu usługi Azure Resource Manager**. Ta metoda jest dla zaawansowanych użytkowników. Aby uzyskać więcej informacji, zobacz [Tworzenie ase z szablonu][MakeASEfromTemplate].

Zewnętrzny ase ma publiczny adres VIP, co oznacza, że cały ruch HTTP/HTTPS do aplikacji w ASE trafi adres IP dostępny w Internecie. Ase z równoważeniem obciążenia sieciowego ma adres IP z podsieci używanej przez ASE. Aplikacje hostowane w ASE ILB nie są udostępniane bezpośrednio do Internetu.

## <a name="create-an-ase-and-an-app-service-plan-together"></a>Tworzenie planu ASE i usługi aplikacji razem

Plan usługi aplikacji jest kontenerem aplikacji. Podczas tworzenia aplikacji w usłudze App Service należy wybrać lub utworzyć plan usługi app service. Środowiska usługi app service przechowują plany usługi App Service, a plany usługi App Service przechowują aplikacje.

Aby utworzyć ase podczas tworzenia planu usługi app service:

1. W [portalu Azure](https://portal.azure.com/)wybierz pozycję **Utwórz zasób** > **w sieci Web + mobilną** > **aplikację sieci Web**.

    ![Tworzenie aplikacji sieci Web][1]

2. Wybierz subskrypcję. Aplikacja i ASE są tworzone w tych samych subskrypcji.

3. Wybierz lub utwórz grupę zasobów. Za pomocą grup zasobów można zarządzać powiązanymi zasobami platformy Azure jako jednostką. Grupy zasobów są również przydatne podczas ustanawiania reguł kontroli dostępu opartej na rolach dla aplikacji. Aby uzyskać więcej informacji, zobacz [Omówienie usługi Azure Resource Manager][ARMOverview].

4. Wybierz swój system operacyjny (Windows, Linux lub Docker). 

5. Wybierz plan usługi app service, a następnie wybierz pozycję **Utwórz nowy**. Aplikacje internetowe systemu Linux i aplikacje internetowe systemu Windows nie mogą znajdować się w tym samym planie usługi app service, ale mogą znajdować się w tym samym środowisku usługi app service. 

    ![Nowy plan usługi aplikacji][2]

6. Z listy rozwijanej **Lokalizacja** wybierz region, w którym chcesz utworzyć program ASE. Jeśli wybierzesz istniejący ASE, nowy ASE nie zostanie utworzony. Plan usługi aplikacji jest tworzony w ase, który został wybrany. 

7. Wybierz **warstwę cenową**i wybierz jedną z **jednostek SKU** cen izolowanych. Jeśli wybierzesz **izolowane** karty SKU i lokalizacji, która nie jest ASE, nowy ASE jest tworzony w tej lokalizacji. Aby rozpocząć proces tworzenia ase, wybierz **wybierz**. **Izolowana** jednostka SKU jest dostępna tylko w połączeniu z programem ASE. Nie można również używać innych jednostek SKU cenowych w ase innych niż **Izolowane**. 

    ![Wybór warstwy cenowej][3]

8. Wprowadź nazwę swojego ASE. Ta nazwa jest używana w adresowalnej nazwie aplikacji. Jeśli nazwą ASE jest _appsvcenvdemo,_ nazwa domeny to *.appsvcenvdemo.p.azurewebsites.net*. Jeśli utworzysz aplikację o nazwie *mytestapp*, można ją adresować w mytestapp.appsvcenvdemo.p.azurewebsites.net. Nie można użyć odstępu w nazwie. Jeśli używasz wielkich liter, nazwa domeny jest całkowitą wersją małych liter tej nazwy.

    ![Nowa nazwa planu usługi aplikacji][4]

9. Określ szczegóły sieci wirtualnej platformy Azure. Wybierz pozycję **Utwórz nowy** lub **Wybierz istniejący**. Opcja wybrania istniejącej sieci wirtualnej jest dostępna tylko wtedy, gdy w wybranym regionie znajduje się sieci wirtualnej. Jeśli wybierzesz **pozycję Utwórz nowy**, wprowadź nazwę sieci wirtualnej. Zostanie utworzona nowa sieci wirtualnej Menedżera zasobów o tej nazwie. Używa przestrzeni adresowej `192.168.250.0/23` w wybranym regionie. W przypadku **wybrania opcji Wybierz istniejącą**należy:

    a. Wybierz blok adresu sieci wirtualnej, jeśli masz więcej niż jeden.

    b. Wprowadź nową nazwę podsieci.

    d. Wybierz rozmiar podsieci. *Pamiętaj, aby wybrać rozmiar wystarczająco duży, aby dostosować się do przyszłego wzrostu twojego ASE.* Zalecamy `/24`, który ma 128 adresów i może obsługiwać maksymalną wielkość ASE. Nie zalecamy `/28`, na przykład, ponieważ tylko 16 adresy są dostępne. Infrastruktura używa co najmniej siedmiu adresów, a usługa Azure Networking używa kolejnych 5. W `/28` podsieci pozostało maksymalne skalowanie 4 wystąpień planu usługi app service dla zewnętrznego ase i tylko 3 wystąpienia planu usługi app service dla ase równoważenia obciążenia.

    d. Wybierz zakres adresów IP podsieci.

10. Wybierz **pozycję Utwórz,** aby utworzyć ase. Ten proces tworzy również plan usługi app service i aplikacji. Ase, app service plan i aplikacja są w ramach tej samej subskrypcji, a także w tej samej grupie zasobów. Jeśli twój ase potrzebuje oddzielnej grupy zasobów lub jeśli potrzebujesz ASE ILB, wykonaj kroki, aby utworzyć ASE samodzielnie.

## <a name="create-an-ase-and-a-linux-web-app-using-a-custom-docker-image-together"></a>Tworzenie ase i aplikacji sieci Web systemu Linux przy użyciu niestandardowego obrazu platformy Docker razem

1. W [witrynie Azure portal](https://portal.azure.com/) **tworzenie zasobów** > **sieci Web + mobilna** > **aplikacja sieci Web dla kontenerów.** 

    ![Tworzenie aplikacji sieci Web][7]

1. Wybierz subskrypcję. Aplikacja i ASE są tworzone w tych samych subskrypcji.

1. Wybierz lub utwórz grupę zasobów. Za pomocą grup zasobów można zarządzać powiązanymi zasobami platformy Azure jako jednostką. Grupy zasobów są również przydatne podczas ustanawiania reguł kontroli dostępu opartej na rolach dla aplikacji. Aby uzyskać więcej informacji, zobacz [Omówienie usługi Azure Resource Manager][ARMOverview].

1. Wybierz plan usługi app service, a następnie wybierz pozycję **Utwórz nowy**. Aplikacje internetowe systemu Linux i aplikacje internetowe systemu Windows nie mogą znajdować się w tym samym planie usługi app service, ale mogą znajdować się w tym samym środowisku usługi app service. 

    ![Nowy plan usługi aplikacji][8]

1. Z listy rozwijanej **Lokalizacja** wybierz region, w którym chcesz utworzyć program ASE. Jeśli wybierzesz istniejący ASE, nowy ASE nie zostanie utworzony. Plan usługi aplikacji jest tworzony w ase, który został wybrany. 

1. Wybierz **warstwę cenową**i wybierz jedną z **jednostek SKU** cen izolowanych. Jeśli wybierzesz **izolowane** karty SKU i lokalizacji, która nie jest ASE, nowy ASE jest tworzony w tej lokalizacji. Aby rozpocząć proces tworzenia ase, wybierz **wybierz**. **Izolowana** jednostka SKU jest dostępna tylko w połączeniu z programem ASE. Nie można również używać innych jednostek SKU cenowych w ase innych niż **Izolowane**. 

    ![Wybór warstwy cenowej][3]

1. Wprowadź nazwę swojego ASE. Ta nazwa jest używana w adresowalnej nazwie aplikacji. Jeśli nazwą ASE jest _appsvcenvdemo,_ nazwa domeny to *.appsvcenvdemo.p.azurewebsites.net*. Jeśli utworzysz aplikację o nazwie *mytestapp*, można ją adresować w mytestapp.appsvcenvdemo.p.azurewebsites.net. Nie można użyć odstępu w nazwie. Jeśli używasz wielkich liter, nazwa domeny jest całkowitą wersją małych liter tej nazwy.

    ![Nowa nazwa planu usługi aplikacji][4]

1. Określ szczegóły sieci wirtualnej platformy Azure. Wybierz pozycję **Utwórz nowy** lub **Wybierz istniejący**. Opcja wybrania istniejącej sieci wirtualnej jest dostępna tylko wtedy, gdy w wybranym regionie znajduje się sieci wirtualnej. Jeśli wybierzesz **pozycję Utwórz nowy**, wprowadź nazwę sieci wirtualnej. Zostanie utworzona nowa sieci wirtualnej Menedżera zasobów o tej nazwie. Używa przestrzeni adresowej `192.168.250.0/23` w wybranym regionie. W przypadku **wybrania opcji Wybierz istniejącą**należy:

    a. Wybierz blok adresu sieci wirtualnej, jeśli masz więcej niż jeden.

    b. Wprowadź nową nazwę podsieci.

    d. Wybierz rozmiar podsieci. *Pamiętaj, aby wybrać rozmiar wystarczająco duży, aby dostosować się do przyszłego wzrostu twojego ASE.* Zalecamy `/24`, który ma 128 adresów i może obsługiwać maksymalną wielkość ASE. Nie zalecamy `/28`, na przykład, ponieważ tylko 16 adresy są dostępne. Infrastruktura używa co najmniej siedmiu adresów, a usługa Azure Networking używa kolejnych 5. W `/28` podsieci pozostało maksymalne skalowanie 4 wystąpień planu usługi app service dla zewnętrznego ase i tylko 3 wystąpienia planu usługi app service dla ase równoważenia obciążenia.

    d. Wybierz zakres adresów IP podsieci.

1.  Wybierz opcję "Konfiguruj kontener".
    * Wprowadź niestandardową nazwę obrazu (możesz użyć usługi Azure Container Registry, Docker Hub i własnego rejestru prywatnego). Jeśli nie chcesz używać własnego kontenera niestandardowego, możesz po prostu przynieść kod i użyć wbudowanego obrazu z usługą App Service w systemie Linux, korzystając z powyższych instrukcji. 

    ![Konfigurowanie kontenera][9]

1. Wybierz **pozycję Utwórz,** aby utworzyć ase. Ten proces tworzy również plan usługi app service i aplikacji. Ase, app service plan i aplikacja są w ramach tej samej subskrypcji, a także w tej samej grupie zasobów. Jeśli twój ase potrzebuje oddzielnej grupy zasobów lub jeśli potrzebujesz ASE ILB, wykonaj kroki, aby utworzyć ASE samodzielnie.


## <a name="create-an-ase-by-itself"></a>Tworzenie ASE samodzielnie

Jeśli utworzysz standalone ASE, nie ma nic w nim. Pusty ASE nadal ponosi miesięczną opłatę za infrastrukturę. Wykonaj następujące kroki, aby utworzyć ase z równoważenia obciążenia sieciowego lub utworzyć ASE we własnej grupie zasobów. Po utworzeniu ase, można tworzyć aplikacje w nim przy użyciu normalnego procesu. Wybierz nową ase jako lokalizację.

1. Przeszukaj środowisko **usługi App Service**w portalu Azure Marketplace lub wybierz pozycję **Utwórz zasób** > **Środowisko usługi aplikacji**sieci Web**Mobile** > . 

1. Wprowadź nazwę swojego ASE. Ta nazwa jest używana dla aplikacji utworzonych w ase. Jeśli nazwa to *mynewdemoase*, nazwa poddomeny to *.mynewdemoase.p.azurewebsites.net*. Jeśli utworzysz aplikację o nazwie *mytestapp*, można ją adresować w mytestapp.mynewdemoase.p.azurewebsites.net. Nie można użyć odstępu w nazwie. Jeśli używasz wielkich liter, nazwa domeny jest całkowitą wersją małych liter nazwy. Jeśli używasz równoważenia obciążenia sieciowego, nazwa ase nie jest używana w poddomenie, ale zamiast tego jest jawnie podana podczas tworzenia ASE.

    ![Nazewnictwo ASE][5]

1. Wybierz subskrypcję. Ta subskrypcja jest również tym, którego używają wszystkie aplikacje w ase. Nie można umieścić ase w sieci wirtualnej, która jest w innej subskrypcji.

1. Wybierz lub określ nową grupę zasobów. Grupa zasobów używana dla ase musi być taka sama, która jest używana dla sieci wirtualnej. Jeśli wybierzesz istniejącą sieci wirtualnej, wybór grupy zasobów dla ase jest aktualizowany w celu odzwierciedlenia sieci wirtualnej. *W przypadku korzystania z szablonu Menedżera zasobów można utworzyć ase z grupą zasobów inną niż grupa zasobów sieci wirtualnej.* Aby utworzyć środowisko ASE na podstawie szablonu, zobacz [Tworzenie środowiska usługi app service na podstawie szablonu][MakeASEfromTemplate].

    ![Wybór grupy zasobów][6]

1. Wybierz swoją witrynę wirtualną i lokalizację. Można utworzyć nową sieć wirtualną lub wybrać istniejącą sieć wirtualną: 

    * W przypadku wybrania nowej sieci wirtualnej możesz określić nazwę i lokalizację. 
    
    * Nowa sieci wirtualnej ma zakres adresów 192.168.250.0/23 i podsieci o nazwie domyślne. Podsieć jest zdefiniowana jako 192.168.250.0/24. Można wybrać tylko sieci wirtualnej Menedżera zasobów. Wybór **typu VIP** określa, czy ase można uzyskać bezpośrednio z Internetu (zewnętrzne) lub jeśli używa ILB. Aby dowiedzieć się więcej o tych opcjach, zobacz [Tworzenie i używanie wewnętrznego modułu równoważenia obciążenia w środowisku usługi App Service][MakeILBASE]. 

      * W przypadku **wybrania opcji Zewnętrzna** dla **typu VIP**można wybrać liczbę zewnętrznych adresów IP tworzonych przez system do celów protokołu SSL opartych na protokãoła IP. 
    
      * Jeśli wybierzesz **opcję Wewnętrzna** dla **typu VIP,** musisz określić domenę używana przez program ASE. Można wdrożyć ase w sieci wirtualnej, która używa publicznych lub prywatnych zakresów adresów. Aby użyć sieci wirtualnej z zakresem adresów publicznych, należy utworzyć sieć wirtualną z wyprzedzeniem. 
    
    * Jeśli wybierzesz istniejącą sieci wirtualną, podczas tworzenia ase zostanie utworzona nowa podsieć. *Nie można użyć wstępnie utworzonej podsieci w portalu. Jeśli używasz szablonu Menedżera zasobów, można utworzyć ase z istniejącą podsiecią.* Aby utworzyć środowisko ASE na podstawie szablonu, zobacz [Tworzenie środowiska usługi app service na podstawie szablonu][MakeASEfromTemplate].

## <a name="app-service-environment-v1"></a>Środowisko usługi App Service — wersja 1

Nadal można tworzyć wystąpienia pierwszej wersji środowiska usługi App Service (ASEv1). Aby rozpocząć ten proces, wyszukaj środowisko **usługi App Service w wersji 1**w portalu Marketplace . Program ASE jest tworzęsiony w taki sam sposób, jak autonomiczny program ASE. Po zakończeniu ASEv1 ma dwa frontowe i dwóch pracowników. Z ASEv1, należy zarządzać front ends i pracowników. Nie są one automatycznie dodawane podczas tworzenia planów usługi App Service. Front ends działają jako punkty końcowe HTTP/HTTPS i wysyłają ruch do pracowników. Pracownicy są rolami, które hostują aplikacje. Po utworzeniu ase można dostosować liczbę frontów i pracowników. 

Aby dowiedzieć się więcej o ASEv1, zobacz [Wprowadzenie do środowiska usługi app service w wersji 1][ASEv1Intro]. Aby uzyskać więcej informacji na temat skalowania, zarządzania i monitorowania asev1, zobacz [Jak skonfigurować środowisko usługi app service][ConfigureASEv1].

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
[ARMOverview]: ../../azure-resource-manager/management/overview.md
