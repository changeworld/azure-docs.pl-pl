---
title: Korzystanie z App Service środowiska — Azure
description: Tworzenie, publikowanie i skalowanie aplikacji w środowisku Azure App Service
services: app-service
documentationcenter: na
author: ccompy
manager: stefsch
ms.assetid: a22450c4-9b8b-41d4-9568-c4646f4cf66b
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 05/28/2019
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: 841271c474ba8e24bc352bcae1fa037cf382a8ec
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73470581"
---
# <a name="use-an-app-service-environment"></a>Używanie środowiska App Service #

Azure App Service Environment to wdrożenie Azure App Service w podsieci w sieci wirtualnej platformy Azure klienta. Składa się z:

- **Frontony**: frontony http/https w środowisku App Serviceowym (ASE).
- **Procesy robocze**: pracownicy są zasobami, które obsługują aplikacje.
- **Baza danych**: baza danych zawiera informacje, które definiują środowisko.
- **Magazyn**: Magazyn jest używany do hostowania aplikacji opublikowanych przez klienta.

> [!NOTE]
> Istnieją dwie wersje App Service Environment: środowiska asev1 i ASEv2. W środowiska asev1 należy zarządzać zasobami, zanim będzie można z nich korzystać. Aby dowiedzieć się, jak konfigurować środowiska asev1 i zarządzać nim, zobacz [Konfigurowanie środowiska App Service w wersji 1][ConfigureASEv1]. Pozostała część tego artykułu koncentruje się na ASEv2.
>
>

Aby uzyskać dostęp do aplikacji, można wdrożyć środowisko ASE (środowiska asev1 i ASEv2) przy użyciu zewnętrznego lub wewnętrznego adresu VIP. Wdrożenie z zewnętrznym adresem VIP jest zwykle nazywane zewnętrznym środowiskiem ASE. Wersja wewnętrzna jest nazywana ILB ASE, ponieważ używa wewnętrznego modułu równoważenia obciążenia (ILB). Aby dowiedzieć się więcej na temat środowiska ILB ASE, zobacz [Tworzenie i używanie środowiska ILB ASE][MakeILBASE].

## <a name="create-an-app-in-an-ase"></a>Tworzenie aplikacji w środowisku ASE ##

Aby utworzyć aplikację w środowisku ASE, należy użyć tego samego procesu jak w przypadku normalnego tworzenia, ale przy użyciu kilku małych różnic. Podczas tworzenia nowego planu App Service:

- Zamiast wybierać lokalizację geograficzną, w której ma zostać wdrożona aplikacja, należy wybrać środowisko ASE jako lokalizację.
- Wszystkie plany App Service utworzone w środowisku ASE muszą znajdować się w odizolowanej warstwie cenowej.

Jeśli nie masz środowiska ASE, możesz go utworzyć, postępując zgodnie z instrukcjami w temacie [Create a App Service Environment][MakeExternalASE].

Aby utworzyć aplikację w środowisku ASE:

1. Wybierz pozycję **Utwórz zasób** > **Sieć Web + aplikacje mobilne** > **aplikacji sieci Web**.

2. Wprowadź nazwę aplikacji. Jeśli w środowisku ASE został już wybrany plan App Service, nazwa domeny aplikacji odzwierciedla nazwę domeny środowiska ASE.

    ![Wybór nazwy aplikacji][1]

1. Wybierz subskrypcję.

1. Wprowadź nazwę nowej grupy zasobów lub wybierz pozycję **Użyj istniejącej** , a następnie wybierz jedną z listy rozwijanej.

1. Wybierz system operacyjny. 

1. Wybierz istniejący plan App Service w środowisku ASE lub Utwórz nowy, wykonując następujące czynności:

    a. Wybierz pozycję **Utwórz nowy**.

    b. Wprowadź nazwę planu App Service.

    d. Wybierz pozycję ASE na liście rozwijanej **Lokalizacja** . 
    
    d. Wybierz **izolowaną** warstwę cenową. Wybierz przycisk **Wybierz**.

    e. Kliknij przycisk **OK**.
    
    ![Warstwy cenowe izolowane][2]

    > [!NOTE]
    > Aplikacje Linux i aplikacje systemu Windows nie mogą znajdować się w tym samym planie App Service, ale mogą być w tej samej App Service Environment. 
    >

2. Wybierz pozycję **Utwórz**.

## <a name="how-scale-works"></a>Jak działa skalowanie ##

Każda aplikacja App Service uruchamiana w planie App Service. Model kontenera to środowiska, w których są przechowywane plany App Service, a plany App Service przechowują aplikacje. Skalowanie aplikacji umożliwia skalowanie planu App Service, a tym samym skalowanie wszystkich aplikacji w ramach tego samego planu.

W ASEv2, podczas skalowania planu App Service, wymagana infrastruktura jest automatycznie dodawana. Istnieje opóźnienie czasu na skalowanie operacji podczas dodawania infrastruktury. W środowiska asev1 należy dodać wymaganą infrastrukturę, aby można było utworzyć lub skalować plan App Service. 

W wielodostępnym App Service skalowanie jest zwykle natychmiastowe, ponieważ pula zasobów jest łatwo dostępna do obsługi. W środowisku ASE nie ma takiego buforu, a zasoby są przyliczane w zależności od potrzeb.

W środowisku ASE można skalować do 100 wystąpień. Te wystąpienia 100 mogą być wszystkie w jednym planie App Service lub dystrybuowane w wielu planach App Service.

## <a name="ip-addresses"></a>Adresy IP ##

App Service ma możliwość przydzielenia dedykowanego adresu IP do aplikacji. Ta funkcja jest dostępna po skonfigurowaniu protokołu SSL opartego na protokole IP, zgodnie z opisem w [powiązaniu istniejącego niestandardowego certyfikatu protokołu SSL w celu Azure App Service][ConfigureSSL]. Jednak w środowisku ASE występuje istotny wyjątek. Nie można dodać dodatkowych adresów IP, które mają być używane dla protokołu SSL opartego na protokole IP w środowisku ILB ASE.

W środowiska asev1 należy przydzielić adresy IP jako zasoby, aby można było ich używać. W programie ASEv2 są one używane w aplikacji tak samo jak w App Service wielodostępnym. Zawsze istnieje jeden adres zapasowy w ASEv2 do 30 adresów IP. Za każdym razem, gdy używasz jednej z nich, zostanie dodany inny, aby adres był zawsze dostępny do użycia. Opóźnienie jest wymagane do przydzielenia innego adresu IP, co uniemożliwia Dodawanie adresów IP w krótkim czasie.

## <a name="front-end-scaling"></a>Skalowanie frontonu ##

W ASEv2, podczas skalowania planów App Service pracownicy są automatycznie dodawani do ich obsługi. Każde środowisko ASE jest tworzone z dwoma frontonami. Ponadto frontony są automatycznie skalowane w poziomie jednego frontonu dla każdego 15 wystąpień w planach App Service. Na przykład jeśli masz 15 wystąpień, masz trzy frontony. Jeśli skalujesz do 30 wystąpień, będziesz mieć cztery frontony i tak dalej.

Ta liczba frontonów powinna być większa niż wystarczająca dla większości scenariuszy. Można jednak skalować w poziomie przy szybszym tempie. Można zmienić współczynnik na tak samo jak jeden fronton dla każdego pięciu wystąpień. Zmiana współczynnika jest naliczana. Aby uzyskać więcej informacji, zobacz [Cennik usługi Azure App Service][Pricing].

Zasoby frontonu są punktami końcowymi protokołu HTTP/HTTPS dla środowiska ASE. Z domyślną konfiguracją frontonu użycie pamięci na fronton jest spójne około 60 procent. Obciążenia klientów nie są uruchamiane na frontonie. Najważniejszym czynnikiem dla frontonu w odniesieniu do skali jest procesor CPU, który jest oparty głównie na ruch HTTPS.

## <a name="app-access"></a>Dostęp do aplikacji ##

W zewnętrznym środowisku ASE domena używana podczas tworzenia aplikacji różni się od App Service wielodostępnych. Zawiera nazwę środowiska ASE. Aby uzyskać więcej informacji na temat tworzenia zewnętrznego środowiska ASE, zobacz [Create a App Service Environment][MakeExternalASE]. Nazwa domeny w zewnętrznym środowisku ASE wygląda jak *.&lt;asename&gt;. p.azurewebsites.NET*. Na przykład jeśli środowisko ASE ma nazwę _External-ASE_ i hostuje aplikację o nazwie _contoso_ w tym środowisku ASE, dotrzemy do niej przy użyciu następujących adresów URL:

- contoso.external-ase.p.azurewebsites.net
- contoso.scm.external-ase.p.azurewebsites.net

Adres URL contoso.scm.external-ase.p.azurewebsites.net służy do uzyskiwania dostępu do konsoli usługi kudu lub publikowania aplikacji za pomocą narzędzia Web Deploy. Aby uzyskać informacje na temat konsoli kudu, zobacz [kudu Console for Azure App Service][Kudu]. Konsola kudu udostępnia interfejs użytkownika sieci Web do debugowania, przekazywania plików, edytowania plików i wielu innych.

W środowisku ILB ASE należy określić domenę w czasie wdrażania. Aby uzyskać więcej informacji na temat sposobu tworzenia środowiska ILB ASE, zobacz [Tworzenie i używanie środowiska ILB ASE][MakeILBASE]. W przypadku określenia nazwy domeny _ILB-ASE.info_, aplikacje w tym środowisku ASE używają tej domeny podczas tworzenia aplikacji. W przypadku aplikacji o nazwie _contoso_adresy URL są następujące:

- contoso.ilb-ase.info
- contoso.scm.ilb-ase.info

## <a name="publishing"></a>Publikowanie ##

Podobnie jak w przypadku App Service wielodostępnego, w środowisku ASE można publikować przy użyciu:

- Wdrażanie w sieci Web.
- Zwrócił.
- Ciągła integracja.
- Przeciągnij i upuść w konsoli kudu.
- Środowisko IDE, takie jak Visual Studio, zaćmienie lub IntelliJ pomysł.

W przypadku zewnętrznego środowiska ASE te opcje publikowania działają tak samo. Aby uzyskać więcej informacji, zobacz [wdrażanie w Azure App Service][AppDeploy]. 

Główna różnica polega na publikowaniu w odniesieniu do ILB ASE. W ILB ASE punkty końcowe publikowania są dostępne tylko w ILB. ILB znajduje się w prywatnym adresie IP w podsieci środowiska ASE w sieci wirtualnej. Jeśli nie masz dostępu do sieci do ILB, nie możesz publikować żadnych aplikacji w tym środowisku ASE. Zgodnie z opisem w temacie [Tworzenie i używanie środowiska ILB ASE][MakeILBASE]należy skonfigurować serwer DNS dla aplikacji w systemie. Obejmuje punkt końcowy SCM. Jeśli nie są one prawidłowo zdefiniowane, nie można publikować. Twoje środowisk IDE muszą także mieć dostęp sieciowy do ILB w celu bezpośredniego opublikowania go.

Poza platformą internetowe systemy CI, takie jak GitHub i Azure DevOps, nie współpracują z ILB ASE, ponieważ punkt końcowy publikowania nie jest dostępny dla Internetu. W przypadku usługi Azure DevOps można obejść ten krok poprzez zainstalowanie samoobsługowego agenta wydania w sieci wewnętrznej, gdzie może dotrzeć do ILB. Alternatywnie można również użyć systemu CI, który używa modelu ściągania, takiego jak Dropbox.

Punkty końcowe publikowania dla aplikacji w środowisku ASE z wewnętrznym modułem równoważenia obciążenia używają domeny, za pomocą której utworzono to środowisko. Zobaczysz ją w profilu publikowania aplikacji i w bloku portalu aplikacji (w temacie **omówienie** > **Essentials** , a także we **właściwościach**). 

## <a name="pricing"></a>Cennik ##

Jednostka SKU z cennikiem o nazwie **izolowana** została utworzona do użycia tylko z ASEv2. Wszystkie plany App Service hostowane w ASEv2 znajdują się w odizolowanej jednostce SKU cenowej. Stawki za plan App Service izolowane mogą się różnić w zależności od regionu. 

Oprócz ceny planów App Service istnieje stała stawka dla samego środowiska ASE. Płaska stawka nie zmienia rozmiaru środowiska ASE i płaci za infrastrukturę ASE z domyślną szybkością skalowania 1 dodatkowego frontonu dla każdego 15 App Service wystąpienia planu.  

Jeśli domyślna częstotliwość skalowania z 1 frontonu dla każdego 15 App Service wystąpienia planu nie jest wystarczająco szybko dostępna, można dostosować współczynnik, w którym są dodawane frontony lub rozmiar frontonu.  Po dostosowaniu współczynnika lub rozmiaru płacisz za rdzenie frontonu, które nie zostaną dodane domyślnie.  

Na przykład, jeśli dostosowujesz współczynnik skalowania do 10, fronton zostanie dodany dla każdego 10 wystąpień w planach App Service. Stała opłata obejmuje szybkość skalowania jednego frontonu dla każdego 15 wystąpień. Ze stosunkiem skali 10 opłata jest naliczana za trzeci fronton, który jest dodawany do 10 wystąpień planu App Service. Nie musisz płacisz za niego w przypadku osiągnięcia 15 wystąpień, ponieważ została ona dodana automatycznie.

W przypadku dostosowania rozmiaru frontonu do 2 rdzeni, ale nie dostosowania współczynnika, opłaty są napłacone za dodatkowe rdzenie.  Środowisko ASE jest tworzone z 2 frontonami, więc nawet poniżej progu automatycznego skalowania zapłaciła za 2 dodatkowe rdzenie, jeśli zwiększono rozmiar na 2 rdzenie frontonu.

Aby uzyskać więcej informacji, zobacz [Cennik usługi Azure App Service][Pricing].

## <a name="delete-an-ase"></a>Usuwanie środowiska ASE ##

Aby usunąć środowisko ASE: 

1. Użyj **Usuń** w górnej części bloku **App Service Environment** . 

1. Wprowadź nazwę środowiska ASE, aby potwierdzić, że chcesz go usunąć. Po usunięciu środowiska ASE zostanie również usunięta cała jego zawartość. 

    ![Usuwanie środowiska ASE][3]

<!--Image references-->
[1]: ./media/using_an_app_service_environment/usingase-appcreate.png
[2]: ./media/using_an_app_service_environment/usingase-pricingtiers.png
[3]: ./media/using_an_app_service_environment/usingase-delete.png


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
[Functions]: ../../azure-functions/index.yml
[Pricing]: https://azure.microsoft.com/pricing/details/app-service/
[ARMOverview]: ../../azure-resource-manager/resource-group-overview.md
[ConfigureSSL]: ../configure-ssl-certificate.md
[Kudu]: https://azure.microsoft.com/resources/videos/super-secret-kudu-debug-console-for-azure-web-sites/
[AppDeploy]: ../deploy-local-git.md
[ASEWAF]: app-service-app-service-environment-web-application-firewall.md
[AppGW]: ../../application-gateway/application-gateway-web-application-firewall-overview.md
