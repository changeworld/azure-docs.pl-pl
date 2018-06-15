---
title: Użyj środowiska usługi aplikacji Azure
description: Jak tworzenie, publikowanie i skalowanie aplikacji w środowisku usługi aplikacji Azure
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
ms.date: 06/13/2017
ms.author: ccompy
ms.openlocfilehash: 66ef20616df77dc809a79e516a53133a80759dc7
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/20/2018
ms.locfileid: "34355308"
---
# <a name="use-an-app-service-environment"></a>Użyj środowiska usługi aplikacji #

## <a name="overview"></a>Przegląd ##

Środowiska usługi aplikacji Azure to wdrożenie usługi Azure App Service w podsieci sieci wirtualnej platformy Azure klienta. Składa się z:

- **Zakończenia przód**: front końce są, gdzie HTTP/HTTPS kończy się w środowisku usługi aplikacji (ASE).
- **Pracownicy**: pracownicy są zasoby, które hosta aplikacji.
- **Baza danych**: bazy danych zawiera informacje określające środowiska.
- **Magazyn**: Magazyn jest używany na potrzeby hostowania aplikacji publikowanych przez klienta.

> [!NOTE]
> Istnieją dwie wersje środowiska usługi aplikacji: ASEv1 i ASEv2. Przed ich użyciem w ASEv1, możesz zarządzać zasobami. Aby uzyskać informacje o konfigurowaniu i zarządzaniu nimi ASEv1, zobacz [skonfigurować v1 środowiska usługi aplikacji][ConfigureASEv1]. Pozostała część ten artykuł koncentruje się na ASEv2.
>
>

ASE (ASEv1 i ASEv2) można wdrożyć z zewnętrznym lub wewnętrznym adresu VIP do uzyskania dostępu do aplikacji. Wdrożenia z zewnętrznego adresu VIP jest popularnie określany ASE zewnętrznych. Wersja wewnętrznej jest nazywana ILB ASE, ponieważ używa wewnętrznego modułu równoważenia obciążenia (ILB). Aby dowiedzieć się więcej na temat ILB ASE, zobacz [tworzenie i używanie ASE ILB][MakeILBASE].

## <a name="create-a-web-app-in-an-ase"></a>Tworzenie aplikacji sieci web w elemencie ASE ##

Aby utworzyć aplikację sieci web w elemencie ASE, należy użyć te same czynności co zwykle tworzenia, jednak z kilku niewielkie różnice. Podczas tworzenia nowego planu usługi aplikacji:

- Zamiast wybierać lokalizację geograficzną, w której chcesz wdrożyć aplikację, wybierz polecenie ASE jako lokalizacji.
- Wszystkich utworzonych w elemencie ASE planów usługi aplikacji musi być izolowany warstwy cenowej.

Jeśli nie masz ASE, możesz utworzyć jedną zgodnie z instrukcjami w [tworzenie środowiska usługi aplikacji][MakeExternalASE].

Aby utworzyć aplikację sieci web w elemencie ASE:

1. Wybierz **Utwórz zasób** > **sieci Web i mobilność** > **aplikacji sieci Web**.

2. Wprowadź nazwę dla aplikacji sieci web. Jeśli plan usługi aplikacji jest już wybrana w elemencie ASE, nazwę domeny dla aplikacji odzwierciedla ASE nazwę domeny.

    ![Wybór nazwy aplikacji sieci Web][1]

3. Wybierz subskrypcję.

4. Wprowadź nazwę nowej grupy zasobów lub wybierz **Użyj istniejącego** i wybierz z listy rozwijanej.

5. Wybierz system operacyjny. 

    * Hosting w aplikacji systemu Linux w elemencie ASE jest nowa funkcja w wersji zapoznawczej, dlatego zalecamy nie dodać aplikacje dla systemu Linux w elemencie ASE, które jest aktualnie uruchomione obciążeń produkcyjnych. 
    * Dodawanie aplikacji systemu Linux w elemencie ASE oznacza, że ASE będzie także w wersji zapoznawczej. 

5. Wybierz istniejący plan usługi aplikacji w Twojej ASE lub Utwórz nową, wykonując następujące kroki:

    a. Wybierz **tworzenia nowych**.

    b. Wprowadź nazwę planu usługi aplikacji.

    c. Wybierz użytkownika ASE w **lokalizacji** listy rozwijanej. Hosting w aplikacji systemu Linux w elemencie ASE jest włączona tylko w regionach, 6, obecnie: **zachodnie stany USA, wschodnie stany USA, Europa Zachodnia, Europa Północna, Australia Wschodnia, Azja południowo-wschodnia.** 

    d. Wybierz **izolowany** warstwy cenowej. Wybierz **wybierz**.

    e. Kliknij przycisk **OK**.
    
    ![Izolowane warstw cenowych][2]

    > [!NOTE]
    > Linux aplikacji sieci web i aplikacji sieci web systemu Windows nie może należeć do tego samego planu usługi App Service, ale może być w tym samym środowisku usługi aplikacji. 
    >

6. Wybierz pozycję **Utwórz**.

## <a name="how-scale-works"></a>Jak skalować działa ##

Każda aplikacja usługi aplikacji jest uruchamiany w plan usługi aplikacji. Model kontenera jest środowisk przytrzymaj planów usługi App Service i planów usługi App Service przechowywania aplikacji. Skalowanie aplikacji należy skalowanie planu usługi aplikacji i w związku z tym wszystkie aplikacje w tym samym planie.

W ASEv2 skalowanie planu usługi App Service, infrastruktura wymagana jest automatycznie dodawany. Podczas dodawania jest infrastruktura istnieje opóźnienie operacji skalowania. ASEv1 wymagane infrastruktury muszą zostać dodane przed utworzeniem lub skalowanie planu usługi aplikacji. 

W wielodostępnej usługi aplikacji skalowanie jest zwykle bezpośredniego, ponieważ pula zasobów jest łatwo dostępna do jego obsługi. W elemencie ASE istnieje takie buforu i zasoby są przydzielane na potrzeby.

W elemencie ASE można skalować wystąpień do 100. Te wystąpienia, 100 może być wszystko w jednym jednego planu usługi aplikacji lub rozproszone na wielu planów usługi aplikacji.

## <a name="ip-addresses"></a>Adresy IP ##

Usługa App Service oferuje możliwość przydzielony dedykowany adres IP do aplikacji. Ta funkcja jest dostępna po skonfigurowaniu opartych na protokole SSL, zgodnie z opisem w [Powiąż istniejący certyfikat SSL niestandardowych do aplikacji sieci web platformy Azure][ConfigureSSL]. W elemencie ASE istnieje jednak zauważalne wyjątku. Nie można dodać dodatkowe adresy IP stosowaną do opartych na protokole SSL w elemencie ASE ILB.

W ASEv1 należy przydzielić adresy IP jako zasoby przed ich użyciem. W ASEv2 można ich używać z aplikacji tak samo jak w wielodostępnej usługi aplikacji. Istnieje jeden adres zapasowe w ASEv2 maksymalnie 30 adresów IP. Zawsze używany jest jeden, inny są dodawane, że adres zawsze jest gotowe do użycia. Czas opóźnienia należy przydzielić inny adres IP, co uniemożliwia dodanie adresu IP adresów szybkie naciśnięcie.

## <a name="front-end-scaling"></a>Skalowanie frontonu ##

W ASEv2 gdy skalowanie planów usługi aplikacji pracowników są automatycznie dodawane do ich obsługi. Co ASE jest tworzony z przodu punktów końcowych. Ponadto przodu kończy się automatycznie skalowania w poziomie z szybkością co frontonu dla każdego wystąpienia 15 w planów usługi aplikacji. Na przykład jeśli masz wystąpień 15 masz trzy front kończy się. Jeśli można skalować do 30 wystąpień, użytkownik ma cztery zakończenia przód i tak dalej.

Ten numer interfejsy powinny być to wystarczająca liczba dla większości scenariuszy. Jednak można skalować w poziomie szybsze. Możesz zmienić stosunek jako jeden z przodu niski zakończyć co pięć wystąpień. Opłata za zmianę stosunek nie istnieje. Aby uzyskać więcej informacji, zobacz [cennik usługi aplikacji Azure][Pricing].

Frontonu zasoby są ASE punktu końcowego HTTP/HTTPS. Z domyślnej konfiguracji frontonu użycie pamięci na fronton jest stale około 60 procent. Obciążeń klientów nie działają na fronton. Kluczowe znaczenie dla frontonu w odniesieniu do skalowania jest Procesora, który jest wymuszany głównie przez ruchu HTTPS.

## <a name="app-access"></a>Dostęp do aplikacji ##

W elemencie ASE zewnętrznych domeny, który jest używany podczas tworzenia aplikacji różni się od wielodostępnej usługi aplikacji. Zawiera nazwę ASE. Aby uzyskać więcej informacji na temat tworzenia ASE zewnętrznych, zobacz [tworzenie środowiska usługi aplikacji][MakeExternalASE]. Nazwa domeny w elemencie ASE zewnętrznych wygląda następująco: *.&lt; asename&gt;. p.azurewebsites.net*. Na przykład, jeśli nosi nazwę Twojej ASE _ase zewnętrzne_ i hosta aplikacji o nazwie _contoso_ w tym ASE, możesz uzyskać do niej dostęp w następujących adresów URL:

- contoso.external-ase.p.azurewebsites.net
- contoso.scm.external-ase.p.azurewebsites.net

Adres URL contoso.scm.external-ase.p.azurewebsites.net umożliwia dostęp do konsoli Kudu lub do publikowania aplikacji za pomocą sieci web deploy. Informacje w konsoli Kudu, zobacz [konsoli Kudu dla usługi Azure App Service][Kudu]. Konsoli Kudu zapewnia interfejsu użytkownika sieci web dla debugowania przekazywania plików, edytowanie plików i wiele innych.

W elemencie ASE ILB należy określić domeny, w czasie wdrażania. Aby uzyskać więcej informacji na temat tworzenia ASE ILB, zobacz [tworzenie i używanie ASE ILB][MakeILBASE]. Jeśli określisz nazwy domeny _ilb ase.info_, aplikacji, w tym ASE korzystania z tej domeny podczas tworzenia aplikacji. Dla aplikacji o nazwie _contoso_, adresy URL są:

- contoso.ilb-ase.info
- contoso.scm.ilb-ase.info

## <a name="publishing"></a>Publikowanie ##

Podobnie jak w przypadku wielodostępnej App Service w elemencie ASE można opublikować z:

- Wdrażanie sieci Web.
- FTP.
- Ciągłej integracji.
- Przeciągnij i upuść w konsoli Kudu.
- IDE, takie jak Visual Studio, Eclipse lub IntelliJ IDEA

Z zewnętrznego ASE te opcje publikowania wszystkich działają tak samo. Aby uzyskać więcej informacji, zobacz [wdrożenia w usłudze Azure App Service][AppDeploy]. 

Główna różnica z publikowaniem są względem ASE ILB. Z ASE ILB publikowania punktów końcowych, które są dostępne tylko za pośrednictwem ILB. ILB znajduje się na prywatnego adresu IP w podsieci ASE w sieci wirtualnej. Jeśli nie masz dostępu do sieci do ILB, nie można opublikować żadnej aplikacji na tym ASE. Jak wspomniano w [tworzenie i używanie ASE ILB][MakeILBASE], należy skonfigurować usługę DNS dla aplikacji w systemie. Która zawiera punkt końcowy SCM. Jeśli nie są prawidłowo zdefiniowane, nie można opublikować. IDEs Twojego muszą mieć dostęp sieciowy do ILB, aby opublikować bezpośrednio do niego.

Internetowych systemów elementu konfiguracji, takich jak GitHub i Visual Studio Team Services nie działają z ASE ILB, ponieważ punkt końcowy publikowania nie jest dostępny Internet. Zamiast tego należy używać systemu ciągłej integracji używającego modelu ściągania, takiego jak Dropbox.

Punkty końcowe publikowania dla aplikacji w środowisku ASE z wewnętrznym modułem równoważenia obciążenia używają domeny, za pomocą której utworzono to środowisko. Można to sprawdzić w profilu publikowania aplikacji i w bloku portalu aplikacji (w **omówienie** > **Essentials** oraz w **właściwości**). 

## <a name="pricing"></a>Cennik ##

Cennik SKU o nazwie **izolowany** został utworzony do użytku z ASEv2. Wszystkie plany usługi aplikacji, które znajdują się w ASEv2 znajdują się w izolowany cennik jednostki SKU. Izolowane stawki planu usługi aplikacji może się różnić dla regionu. 

Oprócz ceny dla planów usługi aplikacji istnieje szybkość płaski ASE samej siebie. Szybkość płaski nie zmienia się rozmiar Twojej ASE i płaci infrastruktury ASE na domyślny skalowanie szybkość 1 dodatkowe frontonu dla każdego 15 wystąpieniach planu usługi aplikacji.  

Jeśli domyślna stawka skali 1 frontonu dla każdego 15 wystąpieniach planu usługi aplikacji nie jest wystarczająca, można dostosować stosunek w której przodu kończy się zostaną dodane lub rozmiaru zakończenia przód.  Po zmodyfikowaniu stosunek lub rozmiar płacisz za rdzeni frontonu, które nie zostanie dodany domyślnie.  

Na przykład jeśli współczynnik skali, do 10, fronton jest dodawany do co 10 wystąpień w planów usługi aplikacji. Stałe opłaty obejmuje współczynnika skalowania jeden frontonu dla każdego wystąpienia 15. O stosunku skali 10 opłaty opłacać trzeci fronton, który jest dodawany do 10 wystąpieniach planu usługi aplikacji. Nie trzeba płacić za go po osiągnięciu 15 wystąpienia, ponieważ zostało dodane automatycznie.

Jeśli dostosować rozmiar zakończenia przód 2 rdzenie, ale nie ustawiaj stosunek następnie na zapłacenie za dodatkowe rdzenie.  ASE jest tworzony z 2 przodu zakończenia, dlatego nawet progu automatycznego skalowania będzie na zapłacenie za 2 dodatkowe rdzenie, jeśli zwiększyć rozmiar do 2 rdzenie przodu zakończenia.

Aby uzyskać więcej informacji, zobacz [cennik usługi aplikacji Azure][Pricing].

## <a name="delete-an-ase"></a>Usuń ASE ##

Aby usunąć ASE: 

1. Użyj **usunąć** w górnej części **środowiska usługi aplikacji** bloku. 

2. Wprowadź nazwę użytkownika ASE, aby potwierdzić, czy chcesz go usunąć. Po usunięciu ASE usunąć całą zawartość w niej również. 

    ![Usuwanie ASE][3]

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
[Pricing]: http://azure.microsoft.com/pricing/details/app-service/
[ARMOverview]: ../../azure-resource-manager/resource-group-overview.md
[ConfigureSSL]: ../web-sites-purchase-ssl-web-site.md
[Kudu]: http://azure.microsoft.com/resources/videos/super-secret-kudu-debug-console-for-azure-web-sites/
[AppDeploy]: ../app-service-deploy-local-git.md
[ASEWAF]: app-service-app-service-environment-web-application-firewall.md
[AppGW]: ../../application-gateway/application-gateway-web-application-firewall-overview.md
