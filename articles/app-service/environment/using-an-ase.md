---
title: Używanie środowiska usługi app service i zarządzanie nim
description: Dowiedz się, jak tworzyć, publikować i skalować aplikacje w środowisku usługi aplikacji. Znajdź wszystkie typowe zadania w tym artykule.
author: ccompy
ms.assetid: a22450c4-9b8b-41d4-9568-c4646f4cf66b
ms.topic: article
ms.date: 3/26/2020
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: 4565580feeddc2df8f6ed3011302016bb39977b4
ms.sourcegitcommit: 3c318f6c2a46e0d062a725d88cc8eb2d3fa2f96a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/02/2020
ms.locfileid: "80586134"
---
# <a name="use-an-app-service-environment"></a>Używanie środowiska usługi App Service

Środowisko usługi aplikacji (ASE) to wdrożenie usługi Azure App Service w podsieci w wystąpieniu sieci wirtualnej platformy Azure klienta. ASE składa się z:

- **Front ends**: Gdzie protokół HTTP lub HTTPS kończy się w środowisku usługi aplikacji
- **Pracownicy:** zasoby hostują aplikacje
- **Baza danych**: Przechowuje informacje definiujące środowisko
- **Magazyn:** służy do obsługi aplikacji opublikowanych przez klienta

Program ASE można wdrożyć z zewnętrznym lub wewnętrznym wirtualnym adresem IP (VIP) w celu uzyskania dostępu do aplikacji. Wdrożenie z zewnętrznym elementem VIP jest powszechnie nazywane *zewnętrznym ASE.* Wdrożenie z wewnętrznym elementem VIP jest nazywane *ase równoważenia obciążenia,* ponieważ używa wewnętrznego modułu równoważenia obciążenia (ILB). Aby dowiedzieć się więcej o ase równoważenia obciążenia, zobacz [Tworzenie i używanie ASE ILB][MakeILBASE].

## <a name="create-an-app-in-an-ase"></a>Tworzenie aplikacji w ase

Aby utworzyć aplikację w ase, należy użyć tego samego procesu, jak podczas normalnie utworzyć aplikację, ale z kilkoma niewielkimi różnicami. Podczas tworzenia nowego planu usługi app service:

- Zamiast wybierać lokalizację geograficzną, w której ma być wdrażana aplikacja, jako lokalizację wybierzesz ase.
- Wszystkie plany usługi app service utworzone w ASE może być tylko w warstwie cenowej izolowane.

Jeśli nie masz środowiska ASE, możesz go utworzyć, postępując zgodnie z instrukcjami w aplikacji [Tworzenie środowiska usługi app service.][MakeExternalASE]

Aby utworzyć aplikację w ase:

1. Wybierz **pozycję Utwórz zasób** > **Web + Mobile** > **Web App**.

1. wprowadź nazwę aplikacji. Jeśli już wybrano plan usługi app service w ase, nazwa domeny aplikacji odzwierciedla nazwę domeny ASE:

    ![Wybór nazwy aplikacji][1]

1. Wybierz subskrypcję.

1. Wprowadź nazwę nowej grupy zasobów lub wybierz pozycję **Użyj istniejącego** i wybierz jedną z listy rozwijanej.

1. Wybierz system operacyjny.

1. Wybierz istniejący plan usługi App Service w ase lub utwórz nowy, wykonując następujące kroki:

    a. Z menu po lewej stronie portalu platformy Azure wybierz polecenie **Utwórz zasób > aplikacji Sieci Web**.

    b. Wybierz subskrypcję.

    d. Zaznacz lub utwórz grupę zasobów.

    d. Wprowadź nazwę aplikacji internetowej.

    e. Wybierz **kod** lub **dockerContainer**.

    f. Wybierz stos środowiska uruchomieniowego.

    g. Wybierz **Linux** lub **Windows**. 

    h. Wybierz swój ASE na liście rozwijanej **Region.** 

    i. Wybierz lub utwórz nowy plan usługi aplikacji. Jeśli tworzysz nowy plan usługi aplikacji, wybierz odpowiedni rozmiar **Izolowanej jednostki** SKU.

    ![Pojedyncze warstwy cenowe][2]

    > [!NOTE]
    > Aplikacje systemu Linux i windows nie mogą znajdować się w tym samym planie usługi App Service, ale mogą znajdować się w tym samym środowisku usługi app service.
    >

1. Wybierz **pozycję Recenzja + utwórz**, upewnij się, że informacje są poprawne, a następnie wybierz pozycję **Utwórz**.

## <a name="how-scale-works"></a>Jak działa skala

Każda aplikacja usługi app service jest uruchamiana w planie usługi app service. Środowiska usługi app service przechowują plany usługi App Service, a plany usługi App Service przechowują aplikacje. Podczas skalowania aplikacji skalujesz również plan usługi app service i wszystkie aplikacje w tym samym planie.

Podczas skalowania planu usługi app service wymagana infrastruktura jest dodawana automatycznie. Istnieje opóźnienie czasowe skalowania operacji podczas dodawania infrastruktury. Jeśli wykonasz kilka operacji skalowania w sekwencji, pierwsze żądanie skalowania infrastruktury jest uruchamiane, a pozostałe są umieszczane w kolejce. Po zakończeniu operacji pierwszej skali, inne żądania infrastruktury wszystkie działają razem. A po dodaniu infrastruktury plany usługi app service są przypisywane odpowiednio. Tworzenie nowego planu usługi app service jest sama operacja skalowania, ponieważ żąda dodatkowego sprzętu.

W usługi aplikacji wielodostępnej skalowanie jest natychmiastowe, ponieważ pula zasobów jest łatwo dostępna do jej obsługi. W ase nie ma takiego buforu i zasoby są przydzielane na podstawie potrzeb.

W ase można skalować usługi app service zaplanować maksymalnie 100 wystąpień. ASE może mieć maksymalnie 201 wystąpień ogółem we wszystkich planach usługi app service w tym ASE.

## <a name="ip-addresses"></a>Adresy IP

Usługa App Service może przydzielić dedykowany adres IP do aplikacji. Ta funkcja jest dostępna po skonfigurowaniu protokołu SSL opartego na protokoście IP, zgodnie z opisem w temacie [Powiązanie istniejącego niestandardowego certyfikatu TLS/SSL z usługą Azure App Service.][ConfigureSSL] W ase równoważenia obciążenia sieciowego nie można dodać więcej adresów IP, które mają być używane dla protokołu SSL opartego na protokosze.

Za pomocą zewnętrznego programu ASE można skonfigurować protokół SSL oparty na protokona ip dla aplikacji w taki sam sposób, jak w usłudze aplikacji wielodostępnej. W ASE zawsze jest jeden wolny adres, do 30 adresów IP. Za każdym razem, gdy używasz jednego, inny jest dodawany tak, że adres jest zawsze łatwo dostępny. Opóźnienie czasowe jest wymagane do przydzielenia innego adresu IP. To opóźnienie zapobiega dodawaniu adresów IP w krótkim odstępie czasu.

## <a name="front-end-scaling"></a>Skalowanie front-end

Po skalowaniu w poziomie planów usługi app service, pracownicy są automatycznie dodawane do ich obsługi. Każdy ASE jest tworzony z dwoma przednimi końcami. Front ends automatycznie skalować w poziomie z szybkością jednego frontu dla każdego zestawu 15 wystąpień planu usługi app service. Na przykład jeśli masz trzy plany usługi App Service z pięcioma wystąpieniami, masz w sumie 15 wystąpień i trzy frontowe. Jeśli skalujesz do 30 wystąpień, masz cztery frontowe. Ten wzorzec jest kontynuowany podczas skalowania w poziomie.

Liczba przednich końcówek, które są domyślnie przydzielane, jest dobra dla umiarkowanego obciążenia. Można obniżyć stosunek do zaledwie jednego frontu na każde pięć wystąpień. Można również zmienić rozmiar przednich końcówek. Domyślnie są one pojedyncze rdzenia. W witrynie Azure portal można zmienić ich rozmiar na dwa lub cztery rdzenie zamiast tego.

Istnieje opłata za zmianę proporcji lub rozmiarów front-end. Aby uzyskać więcej informacji, zobacz [Ceny usługi Azure App Service][Pricing]. Jeśli chcesz zwiększyć nośność systemu ASE, uzyskasz większą poprawę, najpierw skalując do dwurdzeniowych końcówek przed dostosowaniem współczynnika skali. Zmiana rozmiaru rdzenia przednich końcówek spowoduje aktualizację ase i powinna być wykonywana poza zwykłymi godzinami pracy.

Zasoby front-end są punktem końcowym HTTP/HTTPS dla ASE. W domyślnej konfiguracji front-end użycie pamięci na frontonie wynosi stale około 60 procent. Głównym powodem skalowania frontów jest użycie procesora CPU, które jest głównie spowodowane ruchem HTTPS.

## <a name="app-access"></a>Dostęp do aplikacji

W zewnętrznym ase sufiks domeny używany do tworzenia aplikacji jest *.&lt; asename&gt;.p.azurewebsites.net*. Jeśli twój ASE nosi nazwę _zewnętrznego ase_ i hostujesz aplikację o nazwie _contoso_ w tym użymie, docierasz do niej pod następującymi adresami URL:

- contoso.external-ase.p.azurewebsites.net
- contoso.scm.external-ase.p.azurewebsites.net

Aby uzyskać informacje dotyczące tworzenia zewnętrznego środowiska ASE, zobacz [Tworzenie środowiska usługi app service][MakeExternalASE].

W ase równoważenia obciążenia sieciowego sufiks domeny używany do tworzenia aplikacji jest *.&lt; asename&gt;.appserviceenvironment.net*. Jeśli twój ase nosi nazwę _ilb-ase_ i hostujesz aplikację o nazwie _contoso_ w tym użymie, docierasz do niej pod następującymi adresami URL:

- contoso.ilb-ase.appserviceenvironment.net
- contoso.scm.ilb-ase.appserviceenvironment.net

Aby uzyskać informacje dotyczące tworzenia ase ILB, zobacz [Tworzenie i używanie ASE ILB][MakeILBASE].

Adres URL SCM jest używany do uzyskiwania dostępu do konsoli Kudu lub do publikowania aplikacji przy użyciu narzędzia Web Deploy. Aby uzyskać informacje na temat konsoli Kudu, zobacz [konsolę Kudu dla usługi Azure App Service][Kudu]. Konsola Kudu udostępnia internetowy interfejs użytkownika do debugowania, przekazywania plików, edytowania plików i wielu innych.

### <a name="dns-configuration"></a>Konfiguracja usługi DNS 

Podczas korzystania z zewnętrznego ase, aplikacje wykonane w ase są rejestrowane w usłudze Azure DNS. Za pomocą ase równoważenia obciążenia sieciowego należy zarządzać własnym systemem DNS. 

Aby skonfigurować system DNS przy takze ase równoważenia obciążenia sieciowego:

    create a zone for <ASE name>.appserviceenvironment.net
    create an A record in that zone that points * to the ILB IP address
    create an A record in that zone that points @ to the ILB IP address
    create a zone in <ASE name>.appserviceenvironment.net named scm
    create an A record in the scm zone that points * to the ILB IP address

Ustawienia DNS domyślnego sufiksu domeny ASE nie ograniczają aplikacji do dostępu tylko do tych nazw. Niestandardową nazwę domeny można ustawić bez sprawdzania poprawności aplikacji w ase równoważenia obciążenia sieciowego. Jeśli następnie chcesz utworzyć strefę o nazwie *contoso.net*, możesz to zrobić i wskazać jej adres IP równoważenia obciążenia. Niestandardowa nazwa domeny działa dla żądań aplikacji, ale nie dla witryny scm. Strona scm jest dostępna tylko w * &lt;appname&gt;.scm.&lt; asename&gt;.appserviceenvironment.net*. 

Strefa o nazwie *.&lt; asename&gt;.appserviceenvironment.net* jest unikatowa na całym świecie. Przed majem 2019 r. klienci mogli określić sufiks domeny ase ILB. Jeśli chcesz użyć *.contoso.com* dla sufiksu domeny, można było to zrobić i to będzie zawierać witryny scm. Były wyzwania związane z tym modelem, w tym; zarządzanie domyślnym certyfikatem SSL, brak logowania jednokrotnego w lokacji scm i wymóg używania certyfikatu wieloznacznego. Domyślny proces uaktualniania certyfikatu ASE przy przywęzowaniu równoważenia obciążenia sieciowego był również zakłócający działanie i powodował ponowne uruchomienie aplikacji. Aby rozwiązać te problemy, zachowanie ASE równoważenia obciążenia sieciowego zostało zmienione w celu użycia sufiksu domeny na podstawie nazwy programu ASE i sufiksu należącego do firmy Microsoft. Zmiana zachowania ASE ILB dotyczy tylko ases ILB wprowadzone po maju 2019. Istniejące ases równoważenia obciążenia sieciowego i nadal muszą zarządzać domyślnym certyfikatem ASE i ich konfiguracją DNS.

## <a name="publishing"></a>Publikowanie

W ase, podobnie jak w przypadku usługi aplikacji wielodostępnej, można opublikować za pomocą następujących metod:

- Wdrażanie w sieci Web
- FTP
- Ciągła integracja (CI)
- Przeciągnij i upuść w konsoli Kudu
- IDE, takie jak Visual Studio, Eclipse lub IntelliJ IDEA

W przypadku zewnętrznego ase te opcje publikowania działają w ten sam sposób. Aby uzyskać więcej informacji, zobacz [Wdrażanie w usłudze Azure App Service][AppDeploy].

W ase ILB, punkty końcowe publikowania są dostępne tylko za pośrednictwem równoważenia obciążenia sieciowego. Równoważenie obciążenia sieciowego znajduje się na prywatnym adresie IP w podsieci ASE w sieci wirtualnej. Jeśli nie masz dostępu do sieci do równoważenia obciążenia sieciowego, nie możesz publikować żadnych aplikacji na tym ASE. Jak wspomniano w [Tworzenie i używać ASE ILB][MakeILBASE], należy skonfigurować DNS dla aplikacji w systemie. Wymóg ten obejmuje punkt końcowy SCM. Jeśli punkty końcowe nie są poprawnie zdefiniowane, nie można opublikować. Interfejsy adresowe muszą mieć również dostęp do sieci do równoważenia obciążenia sieciowego, aby publikować je bezpośrednio.

Bez dodatkowych zmian internetowe systemy ciągłej integracji, takie jak GitHub i Azure DevOps, nie działają z ase ilb, ponieważ punkt końcowy publikowania nie jest dostępny w Internecie. Można włączyć publikowanie do środowiska ASE równoważenia obciążenia współużytka z usługi Azure DevOps, instalując agenta wydania hostowanego samodzielnie w sieci wirtualnej, która zawiera środowisko ASE równoważenia obciążenia sieciowego. Możesz też użyć systemu ciągłej integracji, który korzysta z modelu ściągania, takiego jak Dropbox.

Punkty końcowe publikowania dla aplikacji w środowisku ASE z wewnętrznym modułem równoważenia obciążenia używają domeny, za pomocą której utworzono to środowisko. Można go zobaczyć w profilu publikowania aplikacji oraz w okienku portalu aplikacji (w **podstawowych elementów przeglądu,** > **Essentials** a także we **właściwościach).**

## <a name="storage"></a>Magazyn

ASE ma 1 TB pamięci masowej dla wszystkich aplikacji w ASE. Plan usługi aplikacji w jednostce SKU cen izolowanych ma domyślnie limit 250 GB. Jeśli masz co najmniej pięć planów usługi app service, należy uważać, aby nie przekroczyć limitu 1 TB ase. Jeśli potrzebujesz więcej niż limit 250 GB w jednym planie usługi App Service, skontaktuj się z pomocą techniczną, aby dostosować limit planu usługi app service do maksymalnie 1 TB. Po skorygowaniu limitu planu nadal istnieje limit 1 TB we wszystkich planach usługi app service w ase.

## <a name="logging"></a>Rejestrowanie

Program ASE można zintegrować z usługą Azure Monitor, aby wysyłać dzienniki dotyczące usługi ASE do usługi Azure Storage, usługi Azure Event Hubs lub usługi Log Analytics. Te elementy są rejestrowane dzisiaj:

| Sytuacji | Komunikat |
|---------|----------|
| ASE jest niezdrowe | Określony ase jest w złej kondycji z powodu nieprawidłowej konfiguracji sieci wirtualnej. Ase zostanie zawieszony, jeśli stan złej kondycji będzie kontynuowany. Upewnij się, że wytyczne https://docs.microsoft.com/azure/app-service/environment/network-infozdefiniowane tutaj są przestrzegane: . |
| Podsieć ASE jest prawie poza przestrzenią | Określony ASE znajduje się w podsieci, która jest prawie poza przestrzenią. Istnieją {0} pozostałe adresy. Po wyczerpaniu tych adresów, ASE nie będzie w stanie skalować.  |
| ASE zbliża się do całkowitego limitu wystąpień | Określony ASE zbliża się do całkowitego limitu wystąpienia ASE. Obecnie zawiera {0} wystąpienia planu usługi app service maksymalnie 201 wystąpień. |
| ASE nie może osiągnąć zależności | Określony ASE nie jest {0}w stanie osiągnąć .  Upewnij się, że wytyczne https://docs.microsoft.com/azure/app-service/environment/network-infozdefiniowane tutaj są przestrzegane: . |
| ASE jest zawieszony | Określony ASE jest zawieszony. Zawieszenie ASE może być spowodowane niedoborem konta lub nieprawidłową konfiguracją sieci wirtualnej. Rozwiąż przyczynę i wznowić ase, aby kontynuować obsługę ruchu. |
| Rozpoczęto uaktualnienie ASE | Rozpoczęto uaktualnianie platformy do określonego ASE. Spodziewaj się opóźnień w operacjach skalowania. |
| Aktualizacja ASE została zakończona | Uaktualnienie platformy do określonego ASE zostało zakończone. |
| Rozpoczęto operacje skalowania | Plan usługi aplikacji{0}( ) rozpoczął skalowanie. Pożądany {1} stan: Ja{2} pracownicy.
| Operacje skalowania zostały zakończone | Plan usługi aplikacji{0}( ) zakończył skalowanie. Aktualny {1} stan:{2} I pracownicy. |
| Operacje skalowania nie powiodły się | Nie można skalować planu usługi aplikacji ({0}) nie powiodło się. Aktualny {1} stan:{2} I pracownicy. |

Aby włączyć logowanie na ase:

1. W portalu przejdź do **ustawień diagnostyki**.
1. Wybierz **pozycję Dodaj ustawienie diagnostyczne**.
1. Podaj nazwę integracji dziennika.
1. Wybierz i skonfiguruj żądane miejsca docelowe dziennika.
1. Wybierz **AppServiceEnvironmentPlatformLogs**.

![Ustawienia dziennika diagnostycznego ASE][4]

Jeśli integrujesz się z usługą Log Analytics, można zobaczyć dzienniki, wybierając **dzienniki** z portalu ASE i tworząc kwerendę względem **AppServiceEnvironmentPlatformLogs**. Dzienniki są emitowane tylko wtedy, gdy program ASE ma zdarzenie, które go wyzwoli. Jeśli twój ASE nie ma takiego zdarzenia, nie będzie żadnych dzienników. Aby szybko wyświetlić przykład dzienników w obszarze roboczym usługi Log Analytics, wykonaj operację skalowania z jednym z planów usługi App Service w ase. Następnie można uruchomić kwerendę względem **AppServiceEnvironmentPlatformLogs,** aby wyświetlić te dzienniki. 

**Tworzenie alertu**

Aby utworzyć alert przed dziennikami, postępuj zgodnie z instrukcjami w [obszarze Tworzenie, wyświetlanie i zarządzanie alertami dziennika za pomocą usługi Azure Monitor.][logalerts] W skrócie:

* Otwórz stronę Alerty w portalu ASE
* Wybierz **nową regułę alertu**
* Wybierz zasób jako obszar roboczy usługi Log Analytics
* Ustaw swój warunek za pomocą niestandardowego wyszukiwania dziennika, aby użyć zapytania, takiego jak "AppServiceEnvironmentPlatformLogs | gdzie ResultDescription zawiera "rozpoczął skalowanie" lub cokolwiek chcesz. Ustaw próg odpowiednio. 
* Dodaj lub utwórz grupę akcji zgodnie z potrzebami. Grupa akcji to miejsce, w którym definiujesz odpowiedź na alert, na przykład wysyłając wiadomość e-mail lub wiadomość SMS
* Nazwij swój alert i zapisz go.

## <a name="upgrade-preference"></a>Preferencja uaktualniania

Jeśli masz wiele ases, możesz chcieć, aby niektóre asy były uaktualniane przed innymi. W ramach obiektu ASE **HostingEnvironment Resource Manager** można ustawić wartość **upgradePreference**. Ustawienie **upgradePreference** można skonfigurować za pomocą szablonu, https://resources.azure.comarmclient lub . Trzy możliwe wartości to:

- **Brak:** Platforma Azure uaktualni twój ase w żadnej konkretnej partii. Ta wartość jest domyślna.
- **Wcześnie**: Twój ase zostanie uaktualniony w pierwszej połowie uaktualnień usługi App Service.
- **Późno:** Twój ase zostanie uaktualniony w drugiej połowie uaktualnień usługi App Service.

Jeśli używasz https://resources.azure.com, wykonaj następujące kroki, aby ustawić **upgradePreferences** wartość:

1. Przejdź do resources.azure.com i zaloguj się za pomocą konta platformy Azure.
1. Przejdź przez zasoby do\/\[subskrypcji\]\/nazwa\/\[subskrypcji resourceGroups resource group\]\/name providers\/Microsoft.Web\/hostingEnvironments\/\[ASE name\].
1. Wybierz **opcję Odczyt/Zapis** u góry.
1. Wybierz pozycję **Edit** (Edytuj).
1. Ustaw **upgradePreference** do jednej z trzech wartości, które chcesz.
1. Wybierz **patch**.

![zasoby azure com wyświetlacz][5]

Funkcja **upgradePreferences** ma największy sens, gdy masz wiele asów, ponieważ twoje "Wczesne" ASE zostaną uaktualnione przed "Późnym" ASE. Jeśli masz wiele ases, należy ustawić rozwoju i testowania ASES być "Early", a ases produkcji być "Late".

## <a name="pricing"></a>Cennik

Cena SKU o nazwie *Isolated* jest do użytku tylko z ASE. Wszystkie plany usługi app service, które są hostowane w ase są w Izolowane jednostki SKU cen. Izolowane stawki dla planów usługi App Service mogą się różnić w zależności od regionu.

Oprócz ceny planów usługi app service istnieje stawka ryczałtowa dla samego ASE. Stawka ryczałtowa nie zmienia się wraz z rozmiarem ASE. Płaci za infrastrukturę ASE z domyślną szybkością skalowania jednego dodatkowego frontonia dla każdego wystąpienia planu usługi App Service 15.

Jeśli domyślna szybkość skalowania jednego frontu dla każdego wystąpienia planu usługi App Service nie jest wystarczająco szybka, można dostosować współczynnik, z którym są dodawane przednie końce, lub rozmiar frontów. Po dostosowaniu współczynnika lub rozmiaru płacisz za rdzenie front-end, które nie zostaną dodane domyślnie.

Na przykład jeśli dostosujesz stosunek skali do 10, fronto-koniec zostanie dodany dla każdego 10 wystąpień w planach usługi App Service. Opłata ryczałtowa obejmuje stawkę skali jednego frontu na każde 15 wystąpień. Przy współczynniku skali 10 płacisz opłatę za trzeci front-end, który jest dodawany dla 10 wystąpień planu usługi app service. Nie musisz za to płacić po osiągnięciu 15 wystąpień, ponieważ został dodany automatycznie.

Jeśli dostosujesz rozmiar przednich końcówek do dwóch rdzeni, ale nie dostosujesz proporcji, zapłacisz za dodatkowe rdzenie. ASE jest tworzony z dwoma frontami, więc nawet poniżej automatycznego progu skalowania zapłacisz za dwa dodatkowe rdzenie, jeśli zwiększysz rozmiar do dwurdzeniowych przódów.

Aby uzyskać więcej informacji, zobacz [Ceny usługi Azure App Service][Pricing].

## <a name="delete-an-ase"></a>Usuwanie ase

Aby usunąć ASE:

1. Wybierz **pozycję Usuń** u góry okienka Środowisko usługi app **service.**

1. Wprowadź nazwę swojego ASE, aby potwierdzić, że chcesz ją usunąć. Usunięcie ase powoduje również usunięcie całej zawartości w nim.

    ![Usunięcie ASE][3]

1. Kliknij przycisk **OK**.

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
[logalerts]: ../../azure-monitor/platform/alerts-log.md
