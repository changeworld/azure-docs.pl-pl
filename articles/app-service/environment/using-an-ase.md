---
title: Używanie App Service Environment i zarządzanie nim
description: Tworzenie, publikowanie i skalowanie aplikacji w środowisku Azure App Servicem. Znajdź typowe zadania w jednym dokumencie.
author: ccompy
ms.assetid: a22450c4-9b8b-41d4-9568-c4646f4cf66b
ms.topic: article
ms.date: 01/01/2020
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: 7be1676c8949cd30d5e1fe93a73afd75a5a9b67f
ms.sourcegitcommit: f27b045f7425d1d639cf0ff4bcf4752bf4d962d2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/23/2020
ms.locfileid: "77565682"
---
# <a name="use-an-app-service-environment"></a>Używanie środowiska App Service #

App Service Environment (ASE) to wdrożenie Azure App Service w podsieci w sieci wirtualnej platformy Azure klienta. Środowisko ASE składa się z:

- **Frontony**: frontony http/https są przerywane w środowisku App servicem.
- **Procesy robocze**: pracownicy są zasobami, które obsługują aplikacje.
- **Baza danych**: baza danych zawiera informacje, które definiują środowisko.
- **Magazyn**: Magazyn jest używany do hostowania aplikacji opublikowanych przez klienta.

W celu uzyskania dostępu do aplikacji można wdrożyć środowisko ASE z zewnętrznym lub wewnętrznym adresem VIP. Wdrożenie z zewnętrznym adresem VIP jest zwykle nazywane zewnętrznym środowiskiem ASE. Wersja wewnętrzna jest nazywana ILB ASE, ponieważ używa wewnętrznego modułu równoważenia obciążenia (ILB). Aby dowiedzieć się więcej na temat środowiska ILB ASE, zobacz [Tworzenie i używanie środowiska ILB ASE][MakeILBASE].

## <a name="create-an-app-in-an-ase"></a>Tworzenie aplikacji w środowisku ASE ##

Aby utworzyć aplikację w środowisku ASE, należy użyć tego samego procesu jak w przypadku normalnego tworzenia, ale przy użyciu kilku małych różnic. Podczas tworzenia nowego planu App Service (ASP):

- Zamiast wybierać lokalizację geograficzną, w której ma zostać wdrożona aplikacja, należy wybrać środowisko ASE jako lokalizację.
- Wszystkie plany App Service utworzone w środowisku ASE mogą znajdować się tylko w warstwie cenowej izolowanej.

Jeśli nie masz środowiska ASE, możesz go utworzyć, postępując zgodnie z instrukcjami w temacie [Create a App Service Environment][MakeExternalASE].

Aby utworzyć aplikację w środowisku ASE:

1. Wybierz pozycję **Utwórz zasób** > **Sieć Web + aplikacje mobilne** > **aplikacji sieci Web**.

2. Wprowadź nazwę aplikacji. Jeśli w środowisku ASE został już wybrany plan App Service, nazwa domeny aplikacji odzwierciedla nazwę domeny środowiska ASE.

    ![Wybór nazwy aplikacji][1]

1. Wybierz subskrypcję.

1. Wprowadź nazwę nowej grupy zasobów lub wybierz pozycję **Użyj istniejącej** , a następnie wybierz jedną z listy rozwijanej.

1. Wybierz system operacyjny. 

1. Wybierz istniejący plan App Service w środowisku ASE lub Utwórz nowy, wykonując następujące czynności:

    a. Z menu Azure Portal po lewej stronie wybierz pozycję **Utwórz zasób > aplikacji sieci Web**.

    b. Wybierz subskrypcję.
    
    c. Wybierz lub Utwórz grupę zasobów.
    
    d. Podaj nazwę aplikacji sieci Web.
    
    e. Wybierz pozycję Code lub DockerContainer.
    
    f. Wybierz pozycję stos środowiska uruchomieniowego.
    
    g. Wybierz pozycję Linux lub Windows. 
    
    h. Wybierz środowisko ASE na liście rozwijanej **region** . 
    
    i. Wybierz lub Utwórz nowy plan App Service. W przypadku tworzenia nowego planu App Service wybierz odpowiedni **wyizolowany** rozmiar jednostki SKU.
    
    ![Warstwy cenowe izolowane][2]

    > [!NOTE]
    > Aplikacje Linux i aplikacje systemu Windows nie mogą znajdować się w tym samym planie App Service, ale mogą być w tej samej App Service Environment. 
    >

2. Wybierz pozycję **Recenzja + Utwórz** , a następnie wybierz pozycję **Utwórz** , jeśli informacje są poprawne.

## <a name="how-scale-works"></a>Jak działa skalowanie ##

Każda aplikacja App Service uruchamiana w planie App Service. Środowiska App Service przechowują plany App Service, a App Service plany przechowują aplikacje. Skalowanie aplikacji umożliwia skalowanie planu App Service, a tym samym skalowanie wszystkich aplikacji w ramach tego samego planu.

Podczas skalowania planu App Service jest automatycznie dodawana wymagana infrastruktura. Istnieje opóźnienie czasu na skalowanie operacji podczas dodawania infrastruktury. Jeśli kilka operacji skalowania zostanie wykonanych z sekwencji, pierwsze żądanie skalowania infrastruktury jest przetwarzane na, a pozostałe kolejki. Po zakończeniu pierwszej operacji skalowania pozostałe żądania infrastruktury działają razem. Po dodaniu infrastruktury App Service plany są przypisywane zgodnie z potrzebami. Tworzenie nowego planu App Service jest operacją skalowania, ponieważ wymaga dodatkowego sprzętu. 

W App Service wielodostępnym skalowanie jest natychmiastowe, ponieważ pula zasobów jest łatwo dostępna do obsługi. W środowisku ASE nie ma takiego buforu, a zasoby są przyliczane w zależności od potrzeb.

W środowisku ASE można skalować plan App Service do 100 wystąpień. Środowisko ASE może mieć nawet 201 wszystkich wystąpień dla wszystkich planów App Serviceych, które znajdują się w środowisku ASE. 

## <a name="ip-addresses"></a>Adresy IP ##

App Service ma możliwość przydzielenia dedykowanego adresu IP do aplikacji. Możliwość przydzielenia dedykowanego adresu IP do aplikacji jest dostępna po skonfigurowaniu protokołu SSL opartego na protokole IP, zgodnie z opisem w [powiązaniu istniejącego niestandardowego certyfikatu protokołu SSL w celu Azure App Service][ConfigureSSL]. W środowisku ILB ASE nie można dodać dodatkowych adresów IP, które mają być używane dla protokołu SSL opartego na protokole IP.

Za pomocą zewnętrznego środowiska ASE można skonfigurować protokół SSL oparty na protokole IP dla aplikacji w taki sam sposób, jak w App Service wielodostępnej. W środowisku ASE istnieje zawsze jeden adres zapasowy do 30 adresów IP. Za każdym razem, gdy używasz jednej z nich, zostanie dodany inny, aby adres był zawsze dostępny do użycia. Opóźnienie jest wymagane do przydzielenia innego adresu IP, co uniemożliwia Dodawanie adresów IP w krótkim czasie.

## <a name="front-end-scaling"></a>Skalowanie frontonu ##

Po skalowaniu planów App Service procesy robocze są automatycznie dodawane do ich obsługi. Każde środowisko ASE jest tworzone z dwoma frontonami. Frontony są automatycznie skalowane z szybkością jednego frontonu dla każdego łącznego 15 wystąpień planu App Service. Jeśli masz trzy App Service plany z pięcioma wystąpieniami, możesz mieć łącznie 15 wystąpień i trzy frontony. W przypadku skalowania do całkowitej liczby 30 wystąpień, dostępne są cztery frontony i tak dalej. 

Liczba frontonów, które są przydzielone domyślnie, jest dobra do umiarkowanego obciążenia. Można zmienić współczynnik tak, aby miał mniej niż jeden fronton dla każdego pięciu wystąpień. Możesz również zmienić rozmiar frontonu. Domyślnie są to pojedyncze rdzeń. Zamiast tego można zmienić rozmiar frontonów w portalu na dwa lub cztery podstawowe rozmiary. Istnieje opłata za zmianę współczynnika lub rozmiaru frontonu. Aby uzyskać więcej informacji, zobacz [Cennik usługi Azure App Service][Pricing]. Jeśli chcesz zwiększyć pojemność obciążenia środowiska ASE, uzyskasz większą poprawę przez pierwsze skalowanie do dwóch podstawowych frontonów przed dopasowaniem współczynnika skalowania. Zmiana rozmiaru rdzenia frontonu spowoduje uaktualnienie środowiska ASE i należy wykonać poza zwykłymi godzinami pracy.

Zasoby frontonu są punktami końcowymi protokołu HTTP/HTTPS dla środowiska ASE. Z domyślną konfiguracją frontonu użycie pamięci na fronton jest spójne około 60 procent. Podstawowym powodem skalowania frontonu jest procesor CPU, który jest głównie oparty na ruchu HTTPS.

## <a name="app-access"></a>Dostęp do aplikacji ##

W zewnętrznym środowisku ASE sufiks domeny używany do tworzenia aplikacji to *.&lt;asename&gt;. p.azurewebsites.NET*. Jeśli środowisko ASE ma nazwę _External-ASE_ i hostuje aplikację o nazwie _contoso_ w tym środowisku ASE, dotrzemy do niej przy użyciu następujących adresów URL:

- contoso.external-ase.p.azurewebsites.net
- contoso.scm.external-ase.p.azurewebsites.net

Aby uzyskać więcej informacji na temat tworzenia zewnętrznej platformy ASE, zobacz [Tworzenie środowiska App Serviceowego][MakeExternalASE]

W środowisku ILB ASE sufiks domeny używany do tworzenia aplikacji to *.&lt;asename&gt;. appserviceenvironment.NET*. Jeśli środowisko ASE ma nazwę _ILB-ASE_ i hostuje aplikację o nazwie _contoso_ w tym środowisku ASE, docieramy do niej przy użyciu następujących adresów URL:

- contoso.ilb-ase.appserviceenvironment.net
- contoso.scm.ilb-ase.appserviceenvironment.net

Aby uzyskać więcej informacji na temat sposobu tworzenia środowiska ILB ASE, zobacz [Tworzenie i używanie środowiska ILB ASE][MakeILBASE]. 

Adres URL usługi SCM służy do uzyskiwania dostępu do konsoli usługi kudu lub publikowania aplikacji za pomocą narzędzia Web Deploy. Aby uzyskać informacje na temat konsoli kudu, zobacz [kudu Console for Azure App Service][Kudu]. Konsola kudu udostępnia interfejs użytkownika sieci Web do debugowania, przekazywania plików, edytowania plików i wielu innych.

## <a name="publishing"></a>Publikowanie ##

Podobnie jak w przypadku App Service wielodostępnego, w środowisku ASE można publikować przy użyciu:

- Wdrażanie w sieci Web.
- FTP.
- Ciągła integracja.
- Przeciągnij i upuść w konsoli kudu.
- Środowisko IDE, takie jak Visual Studio, zaćmienie lub IntelliJ pomysł.

W przypadku zewnętrznego środowiska ASE te opcje publikowania działają tak samo. Aby uzyskać więcej informacji, zobacz [wdrażanie w Azure App Service][AppDeploy]. 

Główna różnica polega na publikowaniu w odniesieniu do ILB ASE. W ILB ASE punkty końcowe publikowania są dostępne tylko w ILB. ILB znajduje się w prywatnym adresie IP w podsieci środowiska ASE w sieci wirtualnej. Jeśli nie masz dostępu do sieci do ILB, nie możesz publikować żadnych aplikacji w tym środowisku ASE. Zgodnie z opisem w temacie [Tworzenie i używanie środowiska ILB ASE][MakeILBASE]należy skonfigurować serwer DNS dla aplikacji w systemie. Obejmuje punkt końcowy SCM. Jeśli nie są one prawidłowo zdefiniowane, nie można publikować. Twoje środowisk IDE muszą także mieć dostęp sieciowy do ILB w celu bezpośredniego opublikowania go.

Poza platformą internetowe systemy CI, takie jak GitHub i Azure DevOps, nie współpracują z ILB ASE, ponieważ punkt końcowy publikowania nie jest dostępny dla Internetu. Możesz włączyć publikowanie w środowisku ILB ASE z poziomu usługi Azure DevOps, instalując własny Agent wydania w sieci wirtualnej, który zawiera ILB ASE. Alternatywnie można również użyć systemu CI, który używa modelu ściągania, takiego jak Dropbox.

Punkty końcowe publikowania dla aplikacji w środowisku ASE z wewnętrznym modułem równoważenia obciążenia używają domeny, za pomocą której utworzono to środowisko. Zobaczysz ją w profilu publikowania aplikacji i w bloku portalu aplikacji (w temacie **omówienie** > **Essentials** , a także we **właściwościach**). 

## <a name="storage"></a>Storage

Środowisko ASE ma 1 TB miejsca do magazynowania dla wszystkich aplikacji w środowisku ASE. Dla izolowanej jednostki SKU App Service domyślnie obowiązuje limit 250 GB. Jeśli masz pięć lub więcej planów App Service, musisz mieć ostrożność, aby nie przekroczyć limitu 1 TB środowiska ASE. Jeśli potrzebujesz więcej niż limit 250 GB w jednym planie App Service, skontaktuj się z pomocą techniczną, aby dostosować limit planu App Service do maksymalnie 1 TB. Po dostosowaniu limitu planu nadal obowiązuje limit 1 TB we wszystkich planach App Service w środowisku ASE. 

## <a name="logging"></a>Rejestrowanie ##

Środowisko ASE można zintegrować z Azure Monitor, aby wysyłać dzienniki dotyczące środowiska ASE do magazynu, centrum zdarzeń lub Log Analytics. Zarejestrowane dzisiaj elementy to:

| Istniał | Komunikat |
|---------|----------|
| Środowisko ASE jest w złej kondycji | Określony środowisko ASE jest w złej kondycji ze względu na nieprawidłową konfigurację sieci wirtualnej. Środowisko ASE zostanie zawieszone w przypadku kontynuowania stanu złej kondycji. Upewnij się, że zostały wykonane następujące wytyczne: https://docs.microsoft.com/azure/app-service/environment/network-info |
| Za mało miejsca w podsieci ASE | Określone środowisko ASE znajduje się w podsieci, która jest w prawie nieprawidłowym miejscu. Istnieją {0} pozostałymi adresami. Po wyczerpaniu tych adresów środowisko ASE nie będzie w stanie skalować  |
| Środowisko ASE zbliża się do łącznego limitu wystąpień | Określone środowisko ASE zbliża się do całkowitego limitu wystąpień środowiska ASE. Obecnie zawiera {0} wystąpienia planu App Service dla maksymalnie 201 wystąpień. |
| Środowisko ASE nie może nawiązać połączenia z zależnością | Określony środowisko ASE nie może nawiązać połączenia {0}.  Upewnij się, że zostały wykonane następujące wytyczne: https://docs.microsoft.com/azure/app-service/environment/network-info |
| Środowisko ASE jest zawieszone | Określona środowisko ASE jest zawieszone. Zawieszenie środowiska ASE może być spowodowane brakiem konta lub konfiguracją nieprawidłowej sieci wirtualnej. Rozpoznaj główną przyczynę i Wznów działanie środowiska ASE, aby kontynuować obsługę ruchu |
| Rozpoczęto Uaktualnianie środowiska ASE | Rozpoczęto Uaktualnianie platformy do określonego środowiska ASE. Oczekiwane opóźnienia operacji skalowania |
| Ukończono Uaktualnianie środowiska ASE | Uaktualnienie platformy do określonego środowiska ASE zostało zakończone |
| Rozpoczęto operacje skalowania | Plan App Service ({0}) rozpoczął skalowanie. Żądany stan: {1} I{2} procesów roboczych 
| Ukończono operacje skalowania | Zakończono skalowanie planu App Service ({0}). Bieżący stan: {1} I{2} procesów roboczych |
| Operacje skalowania zakończyły się niepowodzeniem | Skalowanie planu App Service ({0}) nie powiodło się. Bieżący stan: {1} I{2} procesów roboczych |

Aby włączyć rejestrowanie w środowisku ASE: 

1. Przejdź do ustawień diagnostycznych w portalu.  
1. Wybierz pozycję Dodaj ustawienie diagnostyczne.
1. Podaj nazwę dla integracji dziennika
1. Sprawdź i skonfiguruj wymagane miejsca docelowe dziennika. 
1. Sprawdź AppServiceEnvironmentPlatformLogs

![Ustawienia dziennika diagnostyki środowiska ASE][4]

W przypadku integracji z usługą Log Analytics można zobaczyć dzienniki, wybierając pozycję Dzienniki w portalu środowiska ASE i tworząc zapytanie względem AppServiceEnvironmentPlatformLogs. 

## <a name="upgrade-preference"></a>Preferencja uaktualniania ##

Jeśli masz wiele środowisk ASE, możesz chcieć uaktualnić kilka środowisk ASE przed innymi. W ramach obiektu Menedżer zasobów ASE HostingEnvironment można ustawić wartość dla UpgradePreference. Ustawienie upgradePreference można skonfigurować za pomocą szablonu, ARMClient lub https://resources.azure.com.  Dostępne są trzy wartości:

* Brak — nie jest to ustawienie domyślne i oznacza, że platforma Azure będzie uaktualniać środowisko ASE bez określonej partii
* Wczesne wczesne oznacza, że środowisko ASE zostanie uaktualnione w pierwszej połowie App Service uaktualnieniami
* Późne-późne oznacza, że środowisko ASE zostanie uaktualnione w drugiej połowie App Service uaktualnieniami

Jeśli używasz https://resources.azure.com, możesz ustawić wartość upgradePreferences na:

1. Przechodzenie do resources.azure.com i logowanie przy użyciu konta platformy Azure
1. Przejdź przez subskrypcje\/\[nazwa subskrypcji\]\/resourceGroups\/\[nazwa grupy zasobów\]\/dostawców\/Microsoft. Web\/hostingEnvironments\/\[ASE\]
1. Wybieranie pozycji odczyt/zapis u góry
1. Wybierz pozycję Edytuj
1. Zmień wartość parametru upgradePreference na dowolne z trzech opcji.
1. Wybierz poprawkę

![zasoby ekranu platformy Azure com][5]

Funkcja upgradePreferences naprawdę najlepiej sprawdza się, gdy masz wiele środowisk aseów, ponieważ "wczesny" uaktualniony środowisk ASE zostanie uaktualniony przed "późnym" środowisk ASE. Jeśli masz wiele środowisk ASE, musisz mieć ustawiony środowisk ASE Dev/Test na "wczesny", a produkcyjny środowisk ASE zostanie ustawiony jako "późny".

## <a name="pricing"></a>Ceny ##

Jednostka SKU cenowej o nazwie **izolowanej** jest używana tylko w środowisku ASE. Wszystkie plany App Service, które są hostowane w środowisku ASE, znajdują się w odizolowanej jednostce SKU cenowej. Stawki za plan App Service izolowane mogą się różnić w zależności od regionu. 

Oprócz ceny planów App Service istnieje stała stawka dla samego środowiska ASE. Płaska stawka nie zmienia rozmiaru środowiska ASE i płaci za infrastrukturę ASE przy domyślnej szybkości skalowania jednego dodatkowego frontonu dla każdego 15 App Service wystąpienia planu.  

Jeśli domyślna częstotliwość skalowania jednego frontonu dla każdego 15 wystąpienia planu App Service nie jest wystarczająco szybko dostępna, można dostosować współczynnik, w którym są dodawane frontony lub rozmiar frontonu.  Po dostosowaniu współczynnika lub rozmiaru płacisz za rdzenie frontonu, które nie zostaną dodane domyślnie.  

Na przykład, jeśli dostosowujesz współczynnik skalowania do 10, fronton zostanie dodany dla każdego 10 wystąpień w planach App Service. Stała opłata obejmuje szybkość skalowania jednego frontonu dla każdego 15 wystąpień. Ze stosunkiem skali 10 opłata jest naliczana za trzeci fronton, który jest dodawany do 10 wystąpień planu App Service. Nie musisz płacisz za niego w przypadku osiągnięcia 15 wystąpień, ponieważ została ona dodana automatycznie.

W przypadku dostosowania rozmiaru frontonu do dwóch rdzeni, ale nie dostosowania współczynnika, płacisz za dodatkowe rdzenie.  Środowisko ASE jest tworzone z dwoma frontonami, więc nawet poniżej progu skalowania automatycznego, które byłyby płatne za dwa dodatkowe rdzenie, jeśli zwiększono rozmiar do dwóch głównych frontonów.

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
[4]: ./media/using_an_app_service_environment/usingase-logsetup.png
[4]: ./media/using_an_app_service_environment/usingase-logs.png
[5]: ./media/using_an_app_service_environment/usingase-upgradepref.png


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
[ARMOverview]: ../../azure-resource-manager/management/overview.md
[ConfigureSSL]: ../configure-ssl-certificate.md
[Kudu]: https://azure.microsoft.com/resources/videos/super-secret-kudu-debug-console-for-azure-web-sites/
[AppDeploy]: ../deploy-local-git.md
[ASEWAF]: app-service-app-service-environment-web-application-firewall.md
[AppGW]: ../../application-gateway/application-gateway-web-application-firewall-overview.md
