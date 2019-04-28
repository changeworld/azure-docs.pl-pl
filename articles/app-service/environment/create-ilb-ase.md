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
ms.date: 06/12/2018
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: 01d982d91d772ccfd468ccdac6391f971be4f43b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60765050"
---
# <a name="create-and-use-an-internal-load-balancer-with-an-app-service-environment"></a>Tworzenie i używanie wewnętrznego modułu równoważenia obciążenia ze środowiskiem App Service Environment #

 Środowisko Azure App Service Environment to wdrożenie usługi Azure App Service w podsieci sieci wirtualnej platformy Azure (VNet). Istnieją dwa sposoby wdrażania środowiska App Service Environment (ASE): 

- Z wirtualnym adresem IP będącym zewnętrznym adresem IP — jest to często nazywane zewnętrznym środowiskiem ASE.
- Z wirtualnym adresem IP będącym wewnętrznym adresem IP — jest to często nazywane środowiskiem ASE z wewnętrznym modułem równoważenia obciążenia, ponieważ wewnętrzny punkt końcowy jest wewnętrznym modułem równoważenia obciążenia (ILB, Internal Load Balancer). 

W tym artykule przedstawiono sposób tworzenia środowiska ASE z wewnętrznym modułem równoważenia obciążenia. Omówienie środowiska ASE podano w temacie [Wprowadzenie do środowisk App Service Environment][Intro]. Aby dowiedzieć się, jak utworzyć zewnętrzne środowisko ASE, zobacz [Create an External ASE][MakeExternalASE] (Tworzenie zewnętrznego środowiska ASE).

## <a name="overview"></a>Omówienie ##

Środowisko ASE można wdrożyć za pomocą punktu końcowego dostępnego z Internetu lub adresu IP w sieci wirtualnej. Aby można było ustawić jako adres IP adres sieci wirtualnej, należy wdrożyć środowisko ASE z wewnętrznym modułem równoważenia obciążenia. W przypadku wdrażania środowiska ASE z wewnętrznym modułem równoważenia obciążenia należy podać następujące informacje:

-   Własna domena używana podczas tworzenia aplikacji.
-   Certyfikat używany do obsługi protokołu HTTPS.
-   Zarządzanie usługą DNS dla domeny.

Umożliwia to realizowanie m.in. następujących zadań:

-   Bezpieczne hostowanie aplikacji intranetowych w chmurze dostępnych za pośrednictwem sieci VPN usługi Azure ExpressRoute lub połączenia typu lokacja-lokacja.
-   Hostowanie w chmurze aplikacji, które nie są wymienione na publicznych serwerach DNS.
-   Tworzenie odizolowanych od Internetu aplikacji zaplecza, z którymi można bezpiecznie integrować aplikacje frontonu.

### <a name="disabled-functionality"></a>Funkcje wyłączone ###

Pewnych zadań nie można realizować w przypadku używania środowiska ASE z wewnętrznym modułem równoważenia obciążenia:

-   Używanie protokołu SSL opartego na protokole IP.
-   Przypisywanie adresów IP do określonych aplikacji.
-   Kupowanie i używanie certyfikatu za pomocą aplikacji za pośrednictwem witryny Azure Portal. Certyfikaty można uzyskiwać bezpośrednio od urzędu certyfikacji i używać ich z aplikacjami. Nie można ich uzyskiwać za pośrednictwem witryny Azure Portal.

## <a name="create-an-ilb-ase"></a>Tworzenie środowiska ASE z wewnętrznym modułem równoważenia obciążenia ##

Aby utworzyć środowisko ASE z wewnętrznym modułem równoważenia obciążenia:

1. W witrynie Azure Portal wybierz pozycję  **Utwórz zasób** > **Internet** > **App Service Environment**.

2. Wybierz subskrypcję.

3. Wybierz lub utwórz grupę zasobów.

4. Wybierz lub utwórz sieć wirtualną.

5. W przypadku wybrania istniejącej sieci wirtualnej należy utworzyć podsieć, w której zostanie umieszczone środowisko ASE. Ustaw wystarczająco duży rozmiar podsieci, aby pomieścić środowisko ASE, nawet gdy w przyszłości się rozrośnie. Zalecamy rozmiar `/24`, który zapewnia 256 adresów i może obsłużyć środowiska ASE o maksymalnym rozmiarze i dowolnych potrzebach dotyczących skalowania. 

6. Wybierz pozycję  **Sieć wirtualna/lokalizacja** > **Konfiguracja sieci wirtualnej**. Skonfiguruj dla ustawienia **Typ adresu VIP** wartość **Wewnętrzny**.

7. Wprowadź nazwę domeny. Jest to domena używana w przypadku aplikacji tworzonych w tym środowisku ASE. Obowiązują pewne ograniczenia. Nie mogą to być następujące domeny:

    * net   

    * azurewebsites.net

    * p.azurewebsites.net

    * &lt;nazwa_ASE&gt;.p.azurewebsites.net

   Możesz [mapować istniejącą nazwę DNS na swoją aplikację][customdomain]. Nazwa domeny niestandardowej używanej z aplikacjami i nazwa domeny używana przez środowisko ASE nie mogą się nakładać. W przypadku środowiska ASE z wewnętrznym modułem równoważenia obciążenia mającego nazwę domeny _contoso.com_ nie można używać następujących nazw domen niestandardowych dla aplikacji:

   * www\.contoso.com

   * abcd.def.contoso.com

   * abcd.contoso.com

   Jeśli znasz nazwy domen niestandardowych używane z aplikacjami, wybierz dla środowiska ASE z wewnętrznym modułem równoważenia obciążenia taką domenę, dla której nie występuje konflikt z tymi nazwami domen niestandardowych. W tym przykładzie można użyć jako domeny środowiska ASE czegoś w rodzaju *contoso-wewn.com*, ponieważ ta nazwa nie jest w konflikcie z nazwami domen niestandardowych, które kończą się na *.contoso.com*.

8. Wybierz przycisk **OK**, a następnie wybierz pozycję **Utwórz**.

    ![Tworzenie środowiska ASE][1]

W bloku **Sieć wirtualna** dostępna jest opcja **Konfiguracja sieci wirtualnej**. Umożliwia ona wybranie zewnętrznego lub wewnętrznego wirtualnego adresu IP. Wartość domyślna to **Zewnętrzny**. W przypadku wybrania opcji **Zewnętrzny** środowisko ASE używa wirtualnego adresu IP dostępnego z Internetu. W przypadku wybrania opcji **Wewnętrzny** środowisko ASE jest skonfigurowane z wewnętrznym modułem równoważenia obciążenia i adresem IP w sieci wirtualnej.

Po wybraniu opcji **Wewnętrzny** możliwość dodawania kolejnych adresów IP do środowiska ASE zostaje usunięta. Zamiast tego należy podać domenę środowiska ASE. W przypadku środowiska ASE z zewnętrznym wirtualnym adresem IP nazwa środowiska ASE jest używana w domenie dla aplikacji tworzonych w tym środowisku ASE.

Jeśli dla ustawienia **Typ adresu VIP** została wybrana opcja **Wewnętrzny**, nazwa środowiska ASE nie jest używana w domenie dla środowiska ASE. Domenę określa się jawnie. Jeśli domena to *contoso.corp.net* i utworzysz w tym środowisku ASE aplikację o nazwie  *raportowanieczasu*, adres URL dla tej aplikacji to raportowanieczasu.contoso.corp.net.


## <a name="create-an-app-in-an-ilb-ase"></a>Tworzenie aplikacji w środowisku ASE z wewnętrznym modułem równoważenia obciążenia ##

Aplikację w środowisku ASE z wewnętrznym modułem równoważenia obciążenia tworzy się tak samo jak w normalnym środowisku ASE.

1. W witrynie Azure Portal wybierz pozycję  **Utwórz zasób** > **Sieć Web + aplikacje mobilne** > **Aplikacja internetowa**.

1. Wprowadź nazwę aplikacji.

1. Wybierz subskrypcję.

1. Wybierz lub utwórz grupę zasobów.

1. Wybierz system operacyjny. 

    * Jeśli chcesz utworzyć aplikację systemu Linux przy użyciu niestandardowego kontenera platformy Docker, możesz użyć własnego kontenera, korzystając z instrukcji podanych [w tym miejscu][linuxapp]. 

1. Wybierz lub utwórz plan usługi App Service. Jeśli chcesz utworzyć nowy plan usługi App Service, wybierz środowisko ASE jako lokalizację. Wybierz pulę procesów roboczych, w której ma zostać utworzony plan usługi App Service. Podczas tworzenia planu usługi App Service wybierz środowisko ASE jako lokalizację i pulę procesów roboczych. Po określeniu nazwy aplikacji domena w obszarze nazwy aplikacji zostanie zamieniona na domenę środowiska ASE.

1. Wybierz pozycję **Utwórz**. Jeśli aplikacja ma być wyświetlana na pulpicie nawigacyjnym, zaznacz pole wyboru  **Przypnij do pulpitu nawigacyjnego** .

    ![Tworzenie planu usługi App Service][2]

    W obszarze **Nazwa aplikacji** nazwa domeny zostanie zaktualizowana w celu odzwierciedlenia domeny środowiska ASE.

## <a name="post-ilb-ase-creation-validation"></a>Weryfikacja po utworzeniu środowiska ASE z wewnętrznym modułem równoważenia obciążenia ##

Środowisko ASE z wewnętrznym modułem równoważenia obciążenia różni się nieco od wersji bez tego modułu. Jak już wspomniano, trzeba zarządzać własną usługą DNS. Konieczne jest też zapewnienie własnego certyfikatu do połączeń HTTPS.

Po utworzeniu środowiska ASE jako nazwa domeny wskazywana jest podana nazwa domeny. W menu  **Ustawienie**  jest wyświetlany nowy element menu o nazwie  **Certyfikat wewnętrznego modułu równoważenia obciążenia**. Środowisko ASE jest tworzone wraz z certyfikatem, który nie określa domeny środowiska ASE z wewnętrznym modułem równoważenia obciążenia. Jeśli spróbujesz użyć środowiska ASE z tym certyfikatem, przeglądarka poinformuje, że jest on nieprawidłowy. Ten certyfikat ułatwia przetestowanie usługi HTTPS, ale należy przekazać własny certyfikat powiązany z domeną środowiska ASE z wewnętrznym modułem równoważenia obciążenia. Ten krok jest niezbędny niezależnie od tego, czy certyfikat jest z podpisem własnym, czy otrzymano go od urzędu certyfikacji.

![Nazwa domeny środowiska ASE z wewnętrznym modułem równoważenia obciążenia][3]

Środowisko ASE z wewnętrznym modułem równoważenia obciążenia wymaga prawidłowego certyfikatu SSL. Użyj wewnętrznych urzędów certyfikacji, kup certyfikat od wystawcy zewnętrznego lub użyj certyfikatu z podpisem własnym. Niezależnie od źródła certyfikatu SSL należy poprawnie skonfigurować następujące atrybuty certyfikatu:

* **Podmiot**: Ten atrybut musi mieć ustawioną wartość *.domena-główna.
* **Alternatywna nazwa podmiotu**: ten atrybut musi zawierać zarówno pozycję **.domena-główna*, jak i **.scm.domena-główna*. W połączeniach SSL z witryną SCM/Kudu skojarzonych z poszczególnymi aplikacjami używane są adresy w postaci *nazwa-aplikacji.scm.domena-główna*.

Przekonwertuj lub zapisz certyfikat SSL jako plik pfx. Plik pfx musi zawierać wszystkie certyfikaty pośrednie i główne. Zabezpiecz go przy użyciu hasła.

Jeśli chcesz utworzyć certyfikat z podpisem własnym, możesz użyć tu poleceń programu PowerShell. Pamiętaj, aby użyć nazwy domeny środowiska ASE z wewnętrznym modułem równoważenia obciążenia, a nie domeny *wewnętrzna.contoso.com*: 

    $certificate = New-SelfSignedCertificate -certstorelocation cert:\localmachine\my -dnsname "*.internal-contoso.com","*.scm.internal-contoso.com"
    
    $certThumbprint = "cert:\localMachine\my\" +$certificate.Thumbprint
    $password = ConvertTo-SecureString -String "CHANGETHISPASSWORD" -Force -AsPlainText
    
    $fileName = "exportedcert.pfx" 
    Export-PfxCertificate -cert $certThumbprint -FilePath $fileName -Password $password

Certyfikat wygenerowany za pomocą tych poleceń programu PowerShell jest oflagowywany przez przeglądarki, ponieważ nie został utworzony przez urząd certyfikacji obecny w łańcuchu zaufania przeglądarki. Aby używać certyfikatu, któremu będzie ufać przeglądarka, uzyskaj go od komercyjnego urzędu certyfikacji obecnego w łańcuchu zaufania przeglądarki. 

![Ustawianie certyfikatu wewnętrznego modułu równoważenia obciążenia][4]

Aby przekazać własne certyfikaty i przetestować dostęp:

1. Po utworzeniu środowiska ASE przejdź do interfejsu użytkownika tego środowiska. Wybierz pozycję **ASE** > **Ustawienia** > **Certyfikat wewnętrznego modułu równoważenia obciążenia**.

1. Aby ustawić certyfikat wewnętrznego modułu równoważenia obciążenia, wybierz plik certyfikatu pfx, a następnie wprowadź hasło. Ten krok zajmuje trochę czasu. Pojawi się komunikat z informacją, że operacja przekazywania jest w toku.

1. Uzyskaj adres wewnętrznego modułu równoważenia obciążenia środowiska ASE. Wybierz pozycję **ASE** > **Właściwości** > **Wirtualny adres IP**.

2. Po utworzeniu środowiska ASE utwórz w nim aplikację.

3. Utwórz maszynę wirtualną, jeśli w tej sieci wirtualnej nie ma żadnej.

    > [!NOTE] 
    > Nie próbuj tworzyć tej maszyny wirtualnej w tej samej podsieci, w której istnieje środowisko ASE — nie powiedzie się to lub wystąpią problemy.
    >

4. Skonfiguruj ustawienia DNS dla domeny środowiska ASE. W domenie w usłudze DNS możesz użyć symbolu wieloznacznego. Aby przeprowadzić proste testy, edytuj plik hosts na maszynie wirtualnej w celu ustawienia dla nazwy aplikacji wirtualnego adresu IP:

    a. Jeśli nazwa domeny środowiska ASE to _.aseilb.com_ i utworzysz aplikację o nazwie _mojaaplikacja_, będzie ona miała adres _mojaaplikacja.aseilb.com_. Następnie skonfiguruj rozpoznawanie adresu _mojaaplikacja.aseilb.com_ jako adresu wewnętrznego modułu równoważenia obciążenia. (W Windows, plik hosts znajduje się w _C:\Windows\System32\drivers\etc\\_.)

    b. Aby przetestować publikowanie wdrażania w Internecie lub dostęp do konsoli zaawansowanej, utwórz rekord dla _mojaaplikacja.scm.aseilb.com_.

5. Skorzystaj z przeglądarki na tej maszynie wirtualnej i przejdź na stronę https://mytestapp.ilbase.com. (Lub przejdź do strony z nazwą dowolnej aplikacji w używanej domenie).

6. Skorzystaj z przeglądarki na tej maszynie wirtualnej i przejdź na stronę https://mytestapp.ilbase.com. Jeśli używasz certyfikatu z podpisem własnym, zaakceptuj brak zabezpieczeń.

    Adres IP wewnętrznego modułu równoważenia obciążenia znajduje się w obszarze **Adresy IP**. Ta lista zawiera również adresy IP używane przez zewnętrzne wirtualne adresy IP oraz do obsługi ruchu przychodzącego zarządzania.

    ![Adres IP wewnętrznego modułu równoważenia obciążenia][5]

## <a name="web-jobs-functions-and-the-ilb-ase"></a>Zadania Web Job, usługa Functions i środowisko ASE z wewnętrznym modułem równoważenia obciążenia ##

W środowisku ASE z wewnętrznym modułem równoważenia obciążenia jest obsługiwana zarówno usługa Functions, jak i zadania Web Job, ale aby portal z nimi współdziałał, musisz mieć dostęp do witryny SCM.  Oznacza to, że przeglądarka musi działać na hoście, który albo znajduje się w sieci wirtualnej, albo jest z nią połączony.  

W trakcie używania usługi Azure Functions w środowisku ASE z wewnętrznym modułem równoważenia obciążenia może pojawić się komunikat o błędzie „Nie możemy teraz pobrać funkcji. Spróbuj ponownie później”. Ten błąd występuje, ponieważ interfejs użytkownika usługi Functions korzysta z witryny SCM za pośrednictwem protokołu HTTPS, a w łańcuchu zaufania przeglądarki nie ma certyfikatu głównego. Podobny problem występuje w przypadku zadań Web Job. Aby uniknąć tego problemu, możesz wykonać jedną z następujących czynności:

- Dodaj certyfikat do magazynu zaufanych certyfikatów. Odblokowuje to obsługę w przeglądarkach Microsoft Edge i Internet Explorer.
- W przeglądarce Chrome przejdź najpierw do witryny SCM, zaakceptuj niezaufany certyfikat, a następnie przejdź do portalu.
- Użyj komercyjnego certyfikatu obecnego w łańcuchu zaufania przeglądarki.  Jest to najlepsze rozwiązanie.  

## <a name="dns-configuration"></a>Konfiguracja usługi DNS ##

Gdy używasz zewnętrznego wirtualnego adresu IP, usługą DNS zarządza platforma Azure. Każda aplikacja utworzona w środowisku ASE jest automatycznie dodawana do usługi Azure DNS, która jest publiczną usługą DNS. W środowisku ASE z wewnętrznym modułem równoważenia obciążenia musisz zarządzać własną usługą DNS. Dla danej domeny, takiej jak _contoso.net_, należy w używanej usłudze DNS utworzyć rekordy A systemu DNS wskazujące adres wewnętrznego modułu równoważenia obciążenia dla:

- *.contoso.net
- *.scm.contoso.net

Jeśli domena środowiska ASE z wewnętrznym modułem równoważenia obciążenia jest używana także do celów innych niż obsługa tego środowiska, może być konieczne zarządzanie usługą DNS na poziomie nazw aplikacji. Może to okazać się wyzwaniem, ponieważ wymaga dodania nazwy każdej nowej aplikacji do usługi DNS podczas jej tworzenia. Z tego powodu zalecamy używanie dedykowanej domeny.

## <a name="publish-with-an-ilb-ase"></a>Publikowanie za pomocą środowiska ASE z wewnętrznym modułem równoważenia obciążenia ##

Dla każdej tworzonej aplikacji istnieją dwa punkty końcowe. W środowisku ASE z wewnętrznym modułem równoważenia obciążenia istnieje punkt *&lt;nazwa aplikacji>.&lt;domena środowiska ASE z wewnętrznym modułem równoważenia obciążenia>* i punkt *&lt;nazwa aplikacji>.scm.&lt;domena środowiska ASE z wewnętrznym modułem równoważenia obciążenia>*. 

Nazwa witryny SCM umożliwia przejście do konsoli Kudu, nazywanej **portalem zaawansowanym**, w witrynie Azure Portal. Konsola Kudu umożliwia między innymi wyświetlanie zmiennych środowiskowych, eksplorowanie dysku i używanie konsoli. Aby uzyskać więcej informacji, zobacz [Kudu console for Azure App Service][Kudu] (Konsola Kudu dla usługi Azure App Service). 

W przypadku wielodostępnej usługi App Service i zewnętrznego środowiska ASE obsługiwane jest logowanie jednokrotne między konsolą Kudu a witryną Azure Portal. Jednak w przypadku środowiska ASE z wewnętrznym modułem równoważenia obciążenia do logowania się do konsoli Kudu należy używać poświadczeń publikowania.

Internetowe systemy ciągłej integracji, takie jak usługi GitHub i Azure DevOps, będą nadal działać ze środowiskiem ASE z wewnętrznym modułem równoważenia obciążenia, jeśli agent kompilacji jest dostępny w Internecie i w tej samej sieci co to środowisko. Dlatego w przypadku usługi Azure DevOps, jeśli agent kompilacji został utworzony w tej samej sieci wirtualnej co środowisko ASE z wewnętrznym modułem równoważenia obciążenia (być może w innej podsieci), to będzie mógł ściągnąć kod z usługi Git Azure DevOps i wdrożyć go w tym środowisku. Jeśli nie chcesz tworzyć własnego agenta kompilacji, musisz użyć systemu ciągłej integracji wykorzystującego model ściągania, takiego jak Dropbox.

Punkty końcowe publikowania dla aplikacji w środowisku ASE z wewnętrznym modułem równoważenia obciążenia używają domeny, za pomocą której utworzono to środowisko. Ta domena jest wyświetlana w profilu publikowania aplikacji i w bloku portalu aplikacji (**Przegląd** > **Podstawy** oraz **Właściwości**). Jeśli masz środowisko ASE z wewnętrznym modułem równoważenia obciążenia z poddomeną *contoso.net* i aplikację o nazwie *testowa*, użyj adresu *testowa.contoso.net* dla protokołu FTP i *testowa.scm.contoso.net* dla wdrożenia w Internecie.

## <a name="couple-an-ilb-ase-with-a-waf-device"></a>Łączenie środowiska ASE z wewnętrznym modułem równoważenia obciążenia z urządzeniem zapory aplikacji internetowych ##

Usługa Azure App Service udostępnia wiele mechanizmów zabezpieczeń chroniących system. Ułatwiają one też ustalenie, czy aplikacja nie została zaatakowana. Najlepszą ochronę aplikacji internetowej zapewnia połączenie platformy hostingowej, takiej jak usługa Azure App Service, z zaporą aplikacji internetowych. Ponieważ środowisko ASE z wewnętrznym modułem równoważenia obciążenia ma punkt końcowy aplikacji izolowany od sieci, takie rozwiązanie jest odpowiednie.

Aby dowiedzieć się więcej na temat sposobu konfigurowania używania środowiska ASE z wewnętrznym modułem równoważenia obciążenia z urządzeniem zapory aplikacji internetowych, zobacz [Configure a web application firewall with your App Service environment][ASEWAF] (Konfigurowanie zapory aplikacji internetowych dla środowiska App Service Environment). W tym artykule wyjaśniono, jak używać urządzenia wirtualnego Barracuda ze środowiskiem ASE. Innym rozwiązaniem jest używanie usługi Azure Application Gateway. Usługa Application Gateway zabezpiecza wszelkie umieszczone za nią aplikacje za pomocą reguł podstawowych OWASP. Aby uzyskać więcej informacji na temat usługi Application Gateway, zobacz [Introduction to the Azure web application firewall][AppGW] (Wprowadzenie do zapory aplikacji internetowych platformy Azure).

## <a name="get-started"></a>Rozpoczęcie pracy ##

* Aby rozpocząć pracę ze środowiskami ASE, zobacz [Wprowadzenie do środowisk App Service Environment][Intro].
 

<!--Image references-->
[1]: ./media/creating_and_using_an_internal_load_balancer_with_app_service_environment/createilbase-network.png
[2]: ./media/creating_and_using_an_internal_load_balancer_with_app_service_environment/createilbase-webapp.png
[3]: ./media/creating_and_using_an_internal_load_balancer_with_app_service_environment/createilbase-certificate.png
[4]: ./media/creating_and_using_an_internal_load_balancer_with_app_service_environment/createilbase-certificate2.png
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
