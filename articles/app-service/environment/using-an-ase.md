---
title: Za pomocą środowiska usługi App Service — platformy Azure
description: Jak tworzenie, publikowanie i skalowanie aplikacji w środowisku usługi Azure App Service
services: app-service
documentationcenter: na
author: ccompy
manager: stefsch
ms.assetid: a22450c4-9b8b-41d4-9568-c4646f4cf66b
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/28/2019
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: e9c34d3cfd5ce9bb3a8f9a9072f2843331065100
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "66496520"
---
# <a name="use-an-app-service-environment"></a>Użyj środowiska usługi App Service #

Usługa Azure App Service Environment to wdrożenie usługi Azure App Service w podsieci sieci wirtualnej platformy Azure klienta. Składa się z:

- **Frontonu kończy się**: Frontony są, gdzie kończy HTTP/HTTPS w środowisku usługi App Service (ASE).
- **Procesy robocze**: Procesy robocze są zasoby, które hostowania aplikacji.
- **Baza danych**: Baza danych przechowuje informacje, który definiuje środowiska.
- **Magazyn**: Magazyn jest używany do hostowania aplikacji opublikowanych przez klienta.

> [!NOTE]
> Istnieją dwie wersje środowiska App Service Environment: ASEv1 i ASEv2. W przypadku środowiska ASEv1 można zarządzać zasobami, zanim będzie można ich użyć. Aby dowiedzieć się, jak skonfigurować i zarządzać nimi środowiska ASEv1, zobacz [Konfigurowanie usługi App Service environment w wersji 1][ConfigureASEv1]. W pozostałej części tego artykułu koncentruje się na ASEv2.
>
>

Możesz wdrożyć środowisko ASE wewnętrznego (środowiska ASEv1 i ASEv2) za pomocą zewnętrznego lub wewnętrznego adresu VIP do uzyskania dostępu do aplikacji. Wdrożenie za pomocą zewnętrznego wirtualnego adresu IP jest często nazywane zewnętrznym środowiskiem ASE. Wersja wewnętrzna nazywa się środowisko ASE wewnętrznego modułu równoważenia obciążenia, ponieważ używa wewnętrznego modułu równoważenia obciążenia (ILB). Aby dowiedzieć się więcej na temat środowiska ASE wewnętrznego modułu równoważenia obciążenia, zobacz [tworzenia i używania środowiska ASE z wewnętrznym modułem równoważenia obciążenia][MakeILBASE].

## <a name="create-an-app-in-an-ase"></a>Tworzenie aplikacji w środowisku ASE ##

Aby utworzyć aplikację w środowisku ASE, należy użyć tego samego procesu co zwykle tworzenia, ale z niewielkimi różnicami małych. Podczas tworzenia nowego planu usługi App Service:

- Zamiast wybierać z lokalizacją geograficzną, w której chcesz wdrożyć aplikację, możesz wybrać środowisko ASE jako lokalizację.
- Wszystkie plany usługi App Service, utworzone w środowisku ASE musi być w izolowany warstwy cenowej.

Jeśli masz środowisko ASE można utworzyć jeden, postępując zgodnie z instrukcjami w [tworzenie środowiska usługi App Service][MakeExternalASE].

Aby utworzyć aplikację w środowisku ASE:

1. Wybierz **Utwórz zasób** > **sieci Web i mobilność** > **aplikacji sieci Web**.

2. Wprowadź nazwę aplikacji. Jeśli wybrano już plan usługi App Service w środowisku ASE, nazwę domeny dla aplikacji odzwierciedla nazwa domeny środowiska ASE.

    ![Wybór nazwy aplikacji][1]

1. Wybierz subskrypcję.

1. Wprowadź nazwę nowej grupy zasobów lub wybierz **Użyj istniejącej** i wybierz ją z listy rozwijanej.

1. Wybierz system operacyjny. 

1. Wybierz istniejący plan usługi App Service w środowisku ASE lub Utwórz nową, wykonując następujące czynności:

    a. Wybierz **tworzenia nowych**.

    b. Wprowadź nazwę dla planu usługi App Service.

    c. Wybierz środowisko ASE w **lokalizacji** listy rozwijanej. 
    
    d. Wybierz **izolowany** warstwy cenowej. Wybierz **wybierz**.

    e. Kliknij przycisk **OK**.
    
    ![Izolowane warstwy cenowe][2]

    > [!NOTE]
    > Aplikacje systemu Linux i Windows, aplikacje nie mogą być w tym samym planie usługi App Service, ale mogą znajdować się w tym samym środowisku usługi App Service. 
    >

2. Wybierz pozycję **Utwórz**.

## <a name="how-scale-works"></a>Jak skalować działa ##

Każda aplikacja usługi App Service działa w ramach planu usługi App Service. Model kontenera jest środowisk przytrzymaj planów usługi App Service i planów usługi App Service przechowywania aplikacji. Skalowanie aplikacji skalowania planu usługi App Service, a więc skalowanie wszystkich aplikacji w ten sam plan.

W ASEv2 skalowanie planu usługi App Service, infrastruktura wymagana jest automatycznie dodawany. Gdy zostanie dodany infrastruktury jest czas opóźnienia do skalowania operacji. W przypadku środowiska ASEv1 infrastruktury potrzebne należy dodać przed przystąpieniem do tworzenia lub skalowania w poziomie plan usługi App Service. 

W przypadku wielodostępnej usługi App Service skalowanie jest zwykle natychmiastowego ponieważ puli zasobów jest łatwo dostępne do jego obsługi. W przypadku środowiska ASE jest nie takiego buforu, a zasoby są przydzielane na potrzeby.

W przypadku środowiska ASE można skalować do 100 wystąpień. Te 100 wystąpień może być w jeden pojedynczy plan usługi App Service lub rozproszone między wiele planów usługi App Service.

## <a name="ip-addresses"></a>Adresy IP ##

Usługa App Service ma możliwość przydzielony dedykowany adres IP do aplikacji. Ta funkcja jest dostępna po skonfigurowaniu SSL opartego na protokole IP, zgodnie z opisem w [powiązania istniejącego niestandardowego certyfikatu SSL w usłudze Azure App Service][ConfigureSSL]. Jednak w przypadku środowiska ASE ma istotne wyjątku. Nie można dodać dodatkowych adresów IP do użycia protokołu SSL opartego na protokole IP w środowisku ASE z wewnętrznym modułem równoważenia obciążenia.

W przypadku środowiska ASEv1 należy do przydzielania adresów IP jako zasoby, zanim będzie można ich użyć. W ASEv2 można ich używać ze swojej aplikacji tak samo, jak w przypadku wielodostępnej usługi App Service. Istnieje jeden adres zamiennych w ASEv2 maksymalnie 30 adresy IP. Każdorazowo używasz jednego innego są dodawane, że adres jest zawsze łatwo dostępne do użycia. Czasu, opóźnienie jest wymagane, aby przydzielić inny adres IP, który uniemożliwia Dodawanie adresu IP adresów w szybki odstępie czasu.

## <a name="front-end-scaling"></a>Skalowanie frontonu ##

W ASEv2 skalowanie w poziomie planów usługi App Service, procesy robocze są automatycznie dodawane do ich obsługi. Każdy środowisko ASE jest tworzone za pomocą dwa Frontony. Ponadto frontonów automatycznego skalowania w poziomie z szybkością co fronton dla każdego wystąpienia 15 w planach usługi App Service. Na przykład w przypadku wystąpienia 15 użytkownik ma trzy frontonów. Jeśli możesz skalować do 30 wystąpień, użytkownik ma cztery frontonu kończy się i tak dalej.

Ta liczba frontonów może być więcej niż wystarczające dla większości scenariuszy. Jednak można skalować w poziomie szybsze. Możesz zmienić współczynnik powinna się skończyć jako jeden z przodu niski co pięć wystąpień. Istnieje opłata za zmianę współczynnik. Aby uzyskać więcej informacji, zobacz [cennik usługi Azure App Service][Pricing].

Zasoby frontonu są punktu końcowego HTTP/HTTPS dla środowiska ASE. W przypadku domyślnej konfiguracji frontonu użycie pamięci na frontonie jest stale około 60 procent. Obciążeń klientów nie działają na frontonie. Kluczowym czynnikiem dla frontonu w odniesieniu do skalowania jest Procesora, który jest wymuszany przede wszystkim przez ruchu HTTPS.

## <a name="app-access"></a>Dostęp do aplikacji ##

W przypadku zewnętrznego środowiska ASE domeny, która jest używana podczas tworzenia aplikacji różni się od wielodostępnej usługi App Service. Zawiera nazwę środowiska ASE. Aby uzyskać więcej informacji na temat sposobu Tworzenie zewnętrznego środowiska ASE, zobacz [tworzenie środowiska usługi App Service][MakeExternalASE]. Nazwę domeny w zewnętrznym środowiskiem ASE wygląda *.&lt; asename&gt;. p.azurewebsites.net*. Na przykład, jeśli środowisko ASE ma nazwę _ase zewnętrzne_ i Hostuj aplikację o nazwie _contoso_ w tym, że środowisko ASE, możesz uzyskiwać dostęp go niego w następujących adresów URL:

- contoso.external-ase.p.azurewebsites.net
- contoso.scm.external-ase.p.azurewebsites.net

Adres URL contoso.scm.external-ase.p.azurewebsites.net służy do dostępu do konsoli Kudu lub publikowania aplikacji przy użyciu sieci web deploy. Aby uzyskać informacji na temat konsoli Kudu, zobacz [Konsola Kudu dla usługi Azure App Service][Kudu]. Konsola Kudu umożliwia interfejsu użytkownika sieci web do debugowania, przekazywanie plików, edytowanie plików i wiele więcej.

W przypadku środowiska ASE z wewnętrznym modułem równoważenia obciążenia należy określić domeny, w czasie wdrażania. Aby uzyskać więcej informacji na temat tworzenia środowiska ASE z wewnętrznym modułem równoważenia obciążenia, zobacz [tworzenia i używania środowiska ASE z wewnętrznym modułem równoważenia obciążenia][MakeILBASE]. Jeśli określisz nazwę domeny _ase.info wewnętrznego modułu równoważenia obciążenia_, aplikacje, w tym środowisku ASE korzystania z tej domeny podczas tworzenia aplikacji. Dla aplikacji o nazwie _contoso_, adresy URL są:

- contoso.ilb-ase.info
- contoso.scm.ilb-ase.info

## <a name="publishing"></a>Publikowanie ##

Podobnie jak w przypadku wielodostępnej usługi App Service w środowisku ASE można opublikować za pomocą:

- Wdrażanie w Internecie.
- FTP.
- Ciągła integracja.
- Przeciągnij i upuść w konsoli Kudu.
- Środowisko IDE, takie jak Visual Studio, Eclipse lub IntelliJ IDEA

Za pomocą zewnętrznego środowiska ASE te opcje publikowania wszystkich działa tak samo. Aby uzyskać więcej informacji, zobacz [wdrożenia w usłudze Azure App Service][AppDeploy]. 

Główna różnica z publikowaniem jest względem środowisko ASE z wewnętrznym modułem równoważenia obciążenia. Środowisko ASE z wewnętrznym modułem równoważenia obciążenia publikowanie punktów końcowych są dostępne tylko za pośrednictwem wewnętrznego modułu równoważenia obciążenia. Wewnętrznego modułu równoważenia obciążenia znajduje się na prywatny adres IP w podsieci środowiska ASE w sieci wirtualnej. Jeśli nie masz dostępu do sieci do wewnętrznego modułu równoważenia obciążenia nie można opublikować wszystkie aplikacje, w tym środowisku ASE. Jak wspomniano w [tworzenia i używania środowiska ASE z wewnętrznym modułem równoważenia obciążenia][MakeILBASE], należy skonfigurować usługę DNS dla aplikacji w systemie. Obejmuje to punkt końcowy SCM. Jeśli nie są prawidłowo zdefiniowane, nie można opublikować. Twojego środowiska IDE muszą mieć dostęp do wewnętrznego modułu równoważenia obciążenia sieci w celu publikowania bezpośrednio do niego.

Gotowych oparty na Internecie systemy ciągłej integracji, takich jak GitHub i DevOps platformy Azure, nie działają z ASE z wewnętrznym modułem równoważenia obciążenia, ponieważ punkt końcowy publikowania nie jest dostępny przez Internet. Dla metodyki DevOps platformy Azure można obejść to przez zainstalowanie agenta na wersji samodzielnie hostowanej sieci wewnętrznej, w którym może osiągnąć wewnętrznego modułu równoważenia obciążenia. Alternatywnie można również użyć systemu ciągłej integracji, która używa modelu ściągania, takiego jak Dropbox.

Punkty końcowe publikowania dla aplikacji w środowisku ASE z wewnętrznym modułem równoważenia obciążenia używają domeny, za pomocą której utworzono to środowisko. Można to sprawdzić w profilu publikowania aplikacji i w bloku portalu aplikacji (w **Przegląd** > **Essentials** oraz w **właściwości**). 

## <a name="pricing"></a>Cennik ##

Cennik, jednostki SKU o nazwie **izolowany** została utworzona tylko do użytku z ASEv2. Wszystkie plany usługi App Service, które są hostowane w ASEv2 znajdują się w izolowany jednostce SKU wyceny. Izolowanej usługi App Service plan stawki mogą się różnić na region. 

Oprócz ceny dla planów usługi App Service jest stosowana jest stała stawka ASE sam. Stosowana jest stała stawka nie zmienia rozmiar środowiska ASE i pokrywa koszty infrastruktury ASE na domyślny skalowanie szybkości 1 dodatkowe fronton dla każdego 15 wystąpień planu usługi App Service.  

Jeśli domyślny współczynnik skali 1 frontonu na potrzeby co 15 wystąpień planu usługi App Service nie jest wystarczająco szybko, możesz dostosować współczynnik, na które Frontony są dodawane lub rozmiar frontonów.  Po zmodyfikowaniu współczynnik lub rozmiar płacisz rdzenie frontonu, które nie zostaną dodane domyślnie.  

Na przykład jeśli możesz dostosować współczynnik skalowania do 10, fronton jest dodawany do każdego 10 wystąpień w planach usługi App Service. Obowiązuje stała opłata obejmuje wysokości Skala jednego fronton dla każdego wystąpienia 15. Za pomocą współczynnik skalowania 10 płacisz opłaty trzeci frontonu, który jest dodawany do 10 wystąpień planu usługi App Service. Nie trzeba płacić za jej, gdy osiągniesz 15 wystąpień, ponieważ został dodany automatycznie.

Jeśli dostosować rozmiar frontonów do 2 rdzeni, ale nie ustawiaj stosunek następnie płacisz za dodatkowe rdzenie.  Środowisko ASE jest tworzone z 2 Frontony, więc nawet progowej automatycznego skalowania będzie można płacić za 2 dodatkowe rdzenie, jeśli zwiększyć rozmiar do 2 Frontony core.

Aby uzyskać więcej informacji, zobacz [cennik usługi Azure App Service][Pricing].

## <a name="delete-an-ase"></a>Usuń środowisko ASE ##

Aby usunąć środowisko ASE: 

1. Użyj **Usuń** w górnej części **środowiska App Service Environment** bloku. 

1. Wprowadź nazwę środowiska ASE, aby upewnić się, że chcesz go usunąć. Jeśli usuniesz środowisko ASE, Usuń całą zawartość w nim także. 

    ![Usunięcie środowiska ASE][3]

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
[ConfigureSSL]: ../web-sites-purchase-ssl-web-site.md
[Kudu]: https://azure.microsoft.com/resources/videos/super-secret-kudu-debug-console-for-azure-web-sites/
[AppDeploy]: ../deploy-local-git.md
[ASEWAF]: app-service-app-service-environment-web-application-firewall.md
[AppGW]: ../../application-gateway/application-gateway-web-application-firewall-overview.md
