---
title: Przewidywane wdrażanie aplikacji za pomocą usługi ARM
description: Dowiedz się, jak wdrożyć wiele aplikacji usługi Azure App Service jako pojedynczą jednostkę i w przewidywalny sposób przy użyciu szablonów usługi Azure Resource Management i skryptów programu PowerShell.
ms.assetid: bb51e565-e462-4c60-929a-2ff90121f41d
ms.topic: article
ms.date: 01/06/2016
ms.custom: seodec18
ms.openlocfilehash: 62d0bf776b2d0c97d95b992ed6a1fd2a356e467a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75967379"
---
# <a name="provision-and-deploy-microservices-predictably-in-azure"></a>Udostępnianie i wdrażanie mikrousług w przewidywalny sposób na platformie Azure
W tym samouczku pokazano, jak aprowizować i wdrażać aplikację składającą się z [mikrousług](https://en.wikipedia.org/wiki/Microservices) w [usłudze Azure App Service](https://azure.microsoft.com/services/app-service/) jako pojedynczej jednostki i w przewidywalny sposób przy użyciu szablonów grup zasobów JSON i skryptów programu PowerShell. 

Podczas inicjowania obsługi administracyjnej i wdrażania aplikacji na dużą skalę, które składają się z mikrousług wysoce oddzielonych, powtarzalność i przewidywalność mają kluczowe znaczenie dla powodzenia. [Usługa Azure App Service](https://azure.microsoft.com/services/app-service/) umożliwia tworzenie mikrousług, które obejmują aplikacje sieci web, tylne końce dla urządzeń przenośnych i aplikacje interfejsu API. [Usługa Azure Resource Manager](../azure-resource-manager/management/overview.md) umożliwia zarządzanie wszystkimi mikrousługami jako jednostką, wraz z zależnościami zasobów, takimi jak ustawienia kontroli bazy danych i źródła. Teraz można również wdrożyć taką aplikację przy użyciu szablonów JSON i prostych skryptów programu PowerShell. 

## <a name="what-you-will-do"></a>Co zrobisz
W samouczku wdrożysz aplikację, która zawiera:

* Dwie aplikacje usługi App Service (tj. dwie mikrousługi)
* Baza danych SQL wewnętrznej bazy danych wewnętrznej bazy danych
* Ustawienia aplikacji, parametry połączenia i kontrola źródła
* Wgląd w aplikacje, alerty, ustawienia skalowania automatycznego

## <a name="tools-you-will-use"></a>Narzędzia, których będziesz używać
W tym samouczku użyjesz następujących narzędzi. Ponieważ nie jest to kompleksowa dyskusja na temat narzędzi, mam zamiar trzymać się scenariusza end-to-end i po prostu daje krótkie wprowadzenie do każdego, i gdzie można znaleźć więcej informacji na ten temat. 

### <a name="azure-resource-manager-templates-json"></a>Szablony usługi Azure Resource Manager (JSON)
Za każdym razem, gdy tworzysz aplikację w usłudze Azure App Service, na przykład usługa Azure Resource Manager używa szablonu JSON do utworzenia całej grupy zasobów ze składnikami. Złożony szablon z [portalu Azure Marketplace](/azure/marketplace) może zawierać bazę danych, konta magazynu, plan usługi app service, samą aplikację, reguły alertów, ustawienia aplikacji, ustawienia skalowania automatycznego i inne, a wszystkie te szablony są dostępne za pośrednictwem programu PowerShell. Aby uzyskać więcej informacji na temat szablonów usługi Azure Resource Manager, zobacz [Tworzenie szablonów usługi Azure Resource Manager](../azure-resource-manager/templates/template-syntax.md)

### <a name="azure-sdk-26-for-visual-studio"></a>Zestaw SDK 2.6 platformy Azure dla programu Visual Studio
Najnowszy SDK zawiera ulepszenia obsługi szablonów Menedżera zasobów w edytorze JSON. Można go użyć do szybkiego utworzenia szablonu grupy zasobów od podstaw lub otwarcia istniejącego szablonu JSON (takiego jak pobrany szablon galerii) w celu modyfikacji, zapełnienia pliku parametrów, a nawet wdrożenia grupy zasobów bezpośrednio z zasobu platformy Azure Rozwiązanie grupowe.

Aby uzyskać więcej informacji, zobacz [Zestaw SDK 2.6 platformy Azure dla programu Visual Studio](https://azure.microsoft.com/blog/2015/04/29/announcing-the-azure-sdk-2-6-for-net/).

### <a name="azure-powershell-080-or-later"></a>Program Azure PowerShell 0.8.0 lub nowszy
Począwszy od wersji 0.8.0, instalacja programu Azure PowerShell zawiera moduł usługi Azure Resource Manager oprócz modułu platformy Azure. Ten nowy moduł umożliwia skrypt wdrażania grup zasobów.

Aby uzyskać więcej informacji, zobacz [Korzystanie z usługi Azure PowerShell z usługą Azure Resource Manager](../powershell-azure-resource-manager.md)

### <a name="azure-resource-explorer"></a>Eksplorator zasobów Azure
To [narzędzie w wersji zapoznawczej](https://resources.azure.com) umożliwia eksplorowanie definicji JSON wszystkich grup zasobów w subskrypcji i poszczególnych zasobów. W narzędziu można edytować definicje JSON zasobu, usuwać całą hierarchię zasobów i tworzyć nowe zasoby.  Informacje łatwo dostępne w tym narzędziu są bardzo pomocne w tworzenie szablonów, ponieważ pokazują, jakie właściwości należy ustawić dla określonego typu zasobu, poprawne wartości itp. Można nawet utworzyć grupę zasobów w [witrynie Azure Portal,](https://portal.azure.com/)a następnie sprawdzić jej definicje JSON w narzędziu explorer, aby ułatwić tworzenie szablonów grupy zasobów.

### <a name="deploy-to-azure-button"></a>Przycisk Wdróż na platformie Azure
Jeśli używasz GitHub do kontroli źródła, można umieścić [przycisk Wdrażanie na platformie Azure](https://azure.microsoft.com/blog/2014/11/13/deploy-to-azure-button-for-azure-websites-2/) do readme. MD, który umożliwia interfejs użytkownika wdrażania pod klucz na platformie Azure. Chociaż można to zrobić dla dowolnej prostej aplikacji, można rozszerzyć to, aby włączyć wdrażanie całej grupy zasobów, umieszczając plik azuredeploy.json w katalogu głównym repozytorium. Ten plik JSON, który zawiera szablon grupy zasobów, będzie używany przez przycisk Wdrażanie na platformie Azure, aby utworzyć grupę zasobów. Na przykład zobacz przykład [ToDoApp,](https://github.com/azure-appservice-samples/ToDoApp) który będzie używany w tym samouczku.

## <a name="get-the-sample-resource-group-template"></a>Pobierz przykładowy szablon grupy zasobów
Więc teraz przejdźmy do tego.

1. Przejdź do przykładu usługi aplikacji [ToDoApp.](https://github.com/azure-appservice-samples/ToDoApp)
2. W readme.md kliknij pozycję **Wdrażanie na platformie Azure**.
3. Zostaniesz zabrany do lokacji [wdrażania do platformy Azure](https://deploy.azure.com) i poproszony o wprowadzenie parametrów wdrożenia. Należy zauważyć, że większość pól są wypełniane nazwą repozytorium i niektóre losowe ciągi dla Ciebie. Możesz zmienić wszystkie pola, jeśli chcesz, ale jedyne, co musisz wprowadzić, to logowanie administracyjne programu SQL Server i hasło, a następnie kliknij przycisk **Dalej**.
   
   ![](./media/app-service-deploy-complex-application-predictably/gettemplate-1-deploybuttonui.png)
4. Następnie kliknij przycisk **Wdrażanie,** aby rozpocząć proces wdrażania. Po zakończeniu procesu kliknij łącze http://todoapp *XXXX*.azurewebsites.net, aby przeglądać wdrożoną aplikację. 
   
   ![](./media/app-service-deploy-complex-application-predictably/gettemplate-2-deployprogress.png)
   
   Interfejs użytkownika będzie trochę powolny, gdy po raz pierwszy przeglądać go, ponieważ aplikacje dopiero się uruchamiają, ale przekonaj się, że jest to w pełni funkcjonalna aplikacja.
5. Na stronie Wdrażanie kliknij **łącze Zarządzaj,** aby wyświetlić nową aplikację w witrynie Azure Portal.
6. Z listy **rozwijanej Podstawowe informacje** kliknij łącze grupy zasobów. Należy również zauważyć, że aplikacja jest już połączona z repozytorium GitHub w obszarze **Projekt zewnętrzny**. 
   
   ![](./media/app-service-deploy-complex-application-predictably/gettemplate-3-portalresourcegroup.png)
7. W bloku grupy zasobów należy zauważyć, że istnieją już dwie aplikacje i jedna baza danych SQL w grupie zasobów.
   
   ![](./media/app-service-deploy-complex-application-predictably/gettemplate-4-portalresourcegroupclicked.png)

Wszystko, co właśnie widziałem w ciągu kilku minut jest w pełni wdrożona aplikacja dwóch mikrousług, ze wszystkimi składnikami, zależnościami, ustawieniami, bazą danych i publikowaniem ciągłym, skonfigurowanym przez zautomatyzowaną aranżację w usłudze Azure Resource Manager. Wszystko to zostało zrobione przez dwie rzeczy:

* Przycisk Wdrażanie na platformie Azure
* azuredeploy.json w katalogu głównym repozytorium

Tę samą aplikację można wdrożyć dziesiątki, setki lub tysiące razy i mieć dokładnie taką samą konfigurację za każdym razem. Powtarzalność i przewidywalność tego podejścia umożliwia łatwe i bezpieczne wdrażanie aplikacji na dużą skalę.

## <a name="examine-or-edit-azuredeployjson"></a>Sprawdź (lub edytuj) AZUREDEPLOY. Json
Teraz przyjrzyjmy się, jak skonfigurowano repozytorium GitHub. Będziesz używać edytora JSON w usłudze Azure .NET SDK, więc jeśli nie zainstalowano jeszcze [platformy Azure .NET SDK 2.6,](https://azure.microsoft.com/downloads/)zrób to teraz.

1. Sklonuj repozytorium [ToDoApp](https://github.com/azure-appservice-samples/ToDoApp) za pomocą ulubionego narzędzia git. Na poniższym zrzucie ekranu robię to w Eksploratorze zespołu w programie Visual Studio 2013.
   
   ![](./media/app-service-deploy-complex-application-predictably/examinejson-1-vsclone.png)
2. Z katalogu głównego repozytorium otwórz plik azuredeploy.json w programie Visual Studio. Jeśli nie widzisz okienka Konspektu JSON, musisz zainstalować zestaw SDK platformy Azure .NET.
   
   ![](./media/app-service-deploy-complex-application-predictably/examinejson-2-vsjsoneditor.png)

Nie zamierzam opisywać wszystkich szczegółów formatu JSON, ale sekcja [Więcej zasobów](#resources) zawiera łącza do nauki języka szablonu grupy zasobów. Tutaj pokażę ci interesujące funkcje, które pomogą Ci rozpocząć tworzenie własnego szablonu niestandardowego do wdrażania aplikacji.

### <a name="parameters"></a>Parametry
Zapoznaj się z sekcją parametry, aby zobaczyć, że większość z tych parametrów jest tym, co przycisk **Wdrażanie na platformie Azure** monituje o wprowadzenie danych. Lokacja za **przycisku Wdrażanie na platformie Azure** wypełnia wejściowego interfejsu użytkownika przy użyciu parametrów zdefiniowanych w azuredeploy.json. Parametry te są używane w definicjach zasobów, takich jak nazwy zasobów, wartości właściwości itp.

### <a name="resources"></a>Resources
W węźle zasobów widać, że zdefiniowano 4 zasoby najwyższego poziomu, w tym wystąpienie programu SQL Server, plan usługi app service i dwie aplikacje. 

#### <a name="app-service-plan"></a>Plan usługi App Service
Zacznijmy od prostego zasobu na poziomie głównym w JSON. W konspekcie JSON kliknij plan usługi app service o nazwie **[hostingPlanName],** aby wyróżnić odpowiedni kod JSON. 

![](./media/app-service-deploy-complex-application-predictably/examinejson-3-appserviceplan.png)

Należy zauważyć, że `type` element określa ciąg dla planu usługi app service (został nazwany farmy serwerów dawno, dawno temu), a inne elementy i właściwości są wypełniane przy użyciu parametrów zdefiniowanych w pliku JSON, a ten zasób nie ma żadnych zagnieżdżonych zasobów.

> [!NOTE]
> Należy również zauważyć, `apiVersion` że wartość informuje platformę Azure, która wersja interfejsu API REST do użycia definicji `{}`zasobów JSON z i może mieć wpływ na sposób sformatowania zasobu wewnątrz . 
> 
> 

#### <a name="sql-server"></a>SQL Server
Następnie kliknij zasób programu SQL Server o nazwie **SQLServer** w konspekcie JSON.

![](./media/app-service-deploy-complex-application-predictably/examinejson-4-sqlserver.png)

Zwróć uwagę na następujące informacje na temat wyróżnionego kodu JSON:

* Użycie parametrów zapewnia, że utworzone zasoby są nazwane i skonfigurowane w sposób, który sprawia, że są spójne ze sobą.
* Zasób serwera SQLServer ma dwa zagnieżdżone zasoby, z których każdy ma inną wartość dla `type`.
* Zagnieżdżone zasoby wewnątrz `“resources”: […]`, gdzie zdefiniowano `dependsOn` bazę danych i reguły zapory, mają element określający identyfikator zasobu zasobu SQLServer na poziomie głównym. Informuje to usługę Azure Resource Manager, "przed utworzeniem tego zasobu, że inny zasób musi już istnieć; a jeśli ten inny zasób jest zdefiniowany w szablonie, a następnie utwórz go najpierw".
  
  > [!NOTE]
  > Aby uzyskać szczegółowe informacje `resourceId()` na temat korzystania z tej funkcji, zobacz [Funkcje szablonów usługi Azure Resource Manager](../azure-resource-manager/templates/template-functions-resource.md#resourceid).
  > 
  > 
* `dependsOn` Efektem elementu jest to, że usługa Azure Resource Manager może wiedzieć, które zasoby mogą być tworzone równolegle i które zasoby muszą być tworzone sekwencyjnie. 

#### <a name="app-service-app"></a>Aplikacja usługi App Service
Teraz przejdźmy do rzeczywistych aplikacji, które są bardziej skomplikowane. Kliknij aplikację [variables('apiSiteName')] w konspekcie JSON, aby wyróżnić jego kod JSON. Zauważysz, że sprawy stają się coraz bardziej interesujące. W tym celu opowiem o funkcjach jeden po drugim:

##### <a name="root-resource"></a>Zasób główny
Aplikacja zależy od dwóch różnych zasobów. Oznacza to, że usługa Azure Resource Manager utworzy aplikację tylko po utworzeniu planu usługi app service i wystąpienia programu SQL Server.

![](./media/app-service-deploy-complex-application-predictably/examinejson-5-webapproot.png)

##### <a name="app-settings"></a>Ustawienia aplikacji
Ustawienia aplikacji są również definiowane jako zagnieżdżony zasób.

![](./media/app-service-deploy-complex-application-predictably/examinejson-6-webappsettings.png)

W `properties` elemencie dla `config/appsettings`, masz `"<name>" : "<value>"`dwa ustawienia aplikacji w formacie .

* `PROJECT`to [ustawienie KUDU,](https://github.com/projectkudu/kudu/wiki/Customizing-deployments) które informuje o wdrożeniu platformy Azure, który projekt ma być używany w rozwiązaniu programu Visual Studio dla wielu projektów. Pokażę ci później, jak formant źródła jest skonfigurowany, ale ponieważ kod ToDoApp znajduje się w rozwiązaniu programu Visual Studio wieloprojektowym, potrzebujemy tego ustawienia.
* `clientUrl`jest po prostu ustawienie aplikacji, który używa kodu aplikacji.

##### <a name="connection-strings"></a>Parametry połączeń
Parametry połączenia są również zdefiniowane jako zagnieżdżony zasób.

![](./media/app-service-deploy-complex-application-predictably/examinejson-7-webappconnstr.png)

W `properties` elemencie dla `config/connectionstrings`programu każdy ciąg połączenia jest również definiowany `"<name>" : {"value": "…", "type": "…"}`jako para nazwa:wartość, z określonym formatem . Dla `type` elementu, możliwe `MySql`wartości `SQLServer` `SQLAzure`są `Custom`, , i .

> [!TIP]
> Aby uzyskać ostateczną listę typów ciągów połączeń, uruchom następujące \[polecenie w programie Azure PowerShell: Enum]:GetNames("Microsoft.WindowsAzure.Commands.Utilities.Websites.Services.WebEntities.DatabaseType")
> 
> 

##### <a name="source-control"></a>Kontrola źródła
Ustawienia kontroli źródła są również definiowane jako zagnieżdżony zasób. Usługa Azure Resource Manager używa tego zasobu do `IsManualIntegration` konfigurowania ciągłego publikowania (zobacz zastrzeżenie później), a także do automatycznego uruchamiania wdrażania kodu aplikacji podczas przetwarzania pliku JSON.

![](./media/app-service-deploy-complex-application-predictably/examinejson-8-webappsourcecontrol.png)

`RepoUrl`i `branch` powinny być dość intuicyjne i powinny wskazywać na repozytorium Git i nazwę oddziału do opublikowania. Ponownie są one definiowane przez parametry wejściowe. 

Uwaga w `dependsOn` elemencie, który oprócz `sourcecontrols/web` samego zasobu aplikacji, zależy również od `config/appsettings` i `config/connectionstrings`. Dzieje się `sourcecontrols/web` tak, ponieważ po skonfigurowaniu proces wdrażania platformy Azure automatycznie podejmie próbę wdrożenia, kompilacji i uruchomienia kodu aplikacji. W związku z tym wstawianie tej zależności pomaga upewnić się, że aplikacja ma dostęp do wymaganych ustawień aplikacji i parametry połączenia przed uruchomieniem kodu aplikacji. 

> [!NOTE]
> Należy również `IsManualIntegration` pamiętać, `true`że jest ustawiona na . Ta właściwość jest niezbędna w tym samouczku, ponieważ w rzeczywistości nie jesteś właścicielem repozytorium GitHub, a zatem nie można faktycznie udzielić uprawnień do platformy Azure w celu skonfigurowania ciągłego publikowania z [Aplikacji ToDoApp](https://github.com/azure-appservice-samples/ToDoApp) (tj. wypychania automatycznych aktualizacji repozytorium na platformie Azure). Wartości `false` domyślnej dla określonego repozytorium można użyć tylko wtedy, gdy wcześniej skonfigurowano poświadczenia github właściciela w [witrynie Azure portal.](https://portal.azure.com/) Innymi słowy, jeśli wcześniej skonfigurowano kontrolę źródła w usłudze GitHub lub BitBucket dla dowolnej aplikacji w [witrynie Azure Portal](https://portal.azure.com/) przy użyciu poświadczeń użytkownika, platforma Azure zapamięta poświadczenia i będzie ich używać za każdym razem, gdy wdrażasz dowolną aplikację z gitHub lub BitBucket w przyszłości. Jeśli jednak nie zostało to jeszcze zrobione, wdrożenie szablonu JSON zakończy się niepowodzeniem, gdy usługa Azure Resource Manager spróbuje skonfigurować ustawienia kontroli źródła aplikacji, ponieważ nie może zalogować się do gitHub lub BitBucket przy użyciu poświadczeń właściciela repozytorium.
> 
> 

## <a name="compare-the-json-template-with-deployed-resource-group"></a>Porównanie szablonu JSON z wdrożoną grupą zasobów
W tym miejscu można przejść przez wszystkie ostrza aplikacji w [witrynie Azure Portal](https://portal.azure.com/), ale istnieje inne narzędzie, które jest równie przydatne, jeśli nie więcej. Przejdź do narzędzia [podglądu Eksploratora zasobów platformy Azure,](https://resources.azure.com) które zapewnia reprezentację JSON wszystkich grup zasobów w subskrypcjach, ponieważ faktycznie istnieją one w wewnętrznej bazy danych platformy Azure. Można również zobaczyć, jak hierarchia JSON grupy zasobów na platformie Azure odpowiada hierarchii w pliku szablonu, który jest używany do jej tworzenia.

Na przykład, gdy idę do narzędzia [Eksplorator zasobów platformy Azure](https://resources.azure.com) i rozwiń węzły w eksploratorze, widzę grupę zasobów na poziomie głównym, które są zbierane w ramach ich odpowiednich typów zasobów.

![](./media/app-service-deploy-complex-application-predictably/ARM-1-treeview.png)

Jeśli przechodzisz do szczegółów aplikacji, powinieneś być w stanie zobaczyć szczegóły konfiguracji aplikacji podobne do poniższego zrzutu ekranu:

![](./media/app-service-deploy-complex-application-predictably/ARM-2-jsonview.png)

Ponownie zagnieżdżone zasoby powinny mieć hierarchię bardzo podobną do hierarchii w pliku szablonu JSON i powinny być widoczne ustawienia aplikacji, parametry połączenia itp., prawidłowo odzwierciedlone w okienku JSON. Brak ustawień w tym miejscu może wskazywać na problem z plikiem JSON i może pomóc w rozwiązaniu problemu pliku szablonu JSON.

## <a name="deploy-the-resource-group-template-yourself"></a>Samodzielne wdrażanie szablonu grupy zasobów
Przycisk **Wdrażanie na platformie Azure** jest świetny, ale umożliwia wdrożenie szablonu grupy zasobów w witrynie azuredeploy.json tylko wtedy, gdy został już wypchnięty azuredeploy.json do usługi GitHub. Zestaw Azure .NET SDK udostępnia również narzędzia do wdrażania dowolnego pliku szablonu JSON bezpośrednio z komputera lokalnego. Aby to zrobić, wykonaj poniższe czynności:

1. W programie Visual Studio kliknij pozycję **Plik** > **nowego** > **projektu**.
2. Kliknij **pozycję Visual C#** > **Cloud** > **Azure Resource Group**, a następnie kliknij przycisk **OK**.
   
   ![](./media/app-service-deploy-complex-application-predictably/deploy-1-vsproject.png)
3. W **obszarze Wybierz szablon platformy Azure**wybierz pusty **szablon** i kliknij przycisk **OK**.
4. Przeciągnij plik azuredeploy.json do folderu **Szablon** nowego projektu.
   
   ![](./media/app-service-deploy-complex-application-predictably/deploy-2-copyjson.png)
5. W Eksploratorze rozwiązań otwórz skopiowaną platformę azuredeploy.json.
6. Tylko ze względu na demonstrację, dodajmy kilka standardowych zasobów Application Insight do naszego pliku JSON, klikając **dodaj zasób**. Jeśli interesuje Cię tylko wdrażanie pliku JSON, przejdź do kroków wdrażania.
   
   ![](./media/app-service-deploy-complex-application-predictably/deploy-3-newresource.png)
7. Wybierz **pozycję Statystyka aplikacji dla aplikacji sieci Web,** a następnie upewnij się, że jest zaznaczony istniejący plan usługi App Service i aplikacja, a następnie kliknij przycisk **Dodaj**.
   
   ![](./media/app-service-deploy-complex-application-predictably/deploy-4-newappinsight.png)
   
   Teraz będzie można zobaczyć kilka nowych zasobów, które, w zależności od zasobu i co robi, mają zależności od planu usługi app service lub aplikacji. Te zasoby nie są włączone przez ich istniejącą definicję i zamierzasz to zmienić.
   
   ![](./media/app-service-deploy-complex-application-predictably/deploy-5-appinsightresources.png)
8. W konspekcie JSON kliknij **appInsights Autoscale,** aby wyróżnić jego kod JSON. Jest to ustawienie skalowania dla planu usługi app service.
9. W wyróżnionym kodzie JSON `location` `enabled` zlokalizuj właściwości i ustaw je w sposób pokazany poniżej.
   
   ![](./media/app-service-deploy-complex-application-predictably/deploy-6-autoscalesettings.png)
10. W konspekcie JSON kliknij pozycję **CPUHigh appInsights,** aby wyróżnić jego kod JSON. Jest to alert.
11. Znajdź `location` i `isEnabled` właściwości i ustawić je, jak pokazano poniżej. Zrób to samo dla pozostałych trzech alertów (fioletowe żarówki).
    
    ![](./media/app-service-deploy-complex-application-predictably/deploy-7-alerts.png)
12. Teraz możesz przystąpić do wdrożenia. Kliknij prawym przyciskiem myszy projekt i wybierz polecenie **Wdrażanie** > **nowego wdrożenia**.
    
    ![](./media/app-service-deploy-complex-application-predictably/deploy-8-newdeployment.png)
13. Zaloguj się do konta platformy Azure, jeśli jeszcze tego nie zrobiono.
14. Wybierz istniejącą grupę zasobów w ramach subskrypcji lub utwórz nową, wybierz **azuredeploy.json**, a następnie kliknij przycisk **Edytuj parametry**.
    
    ![](./media/app-service-deploy-complex-application-predictably/deploy-9-deployconfig.png)
    
    Teraz będzie można edytować wszystkie parametry zdefiniowane w pliku szablonu w ładnej tabeli. Parametry definiujące wartości domyślne będą już miały swoje wartości domyślne, a parametry definiujące listę dozwolonych wartości będą wyświetlane jako listy rozwijane.
    
    ![](./media/app-service-deploy-complex-application-predictably/deploy-10-parametereditor.png)
15. Wypełnij wszystkie puste parametry i użyj [adresu repozytorium GitHub dla Aplikacji ToDoApp](https://github.com/azure-appservice-samples/ToDoApp.git) w **repoUrl**. Następnie kliknij przycisk **Zapisz**.
    
    ![](./media/app-service-deploy-complex-application-predictably/deploy-11-parametereditorfilled.png)
    
    > [!NOTE]
    > Skalowanie automatyczne jest funkcją oferowaną w warstwie **Standardowa** lub nowsza, a alerty na poziomie planu są funkcjami oferowanymi w warstwie **Podstawowa** lub nowsza, musisz ustawić parametr **sku** na **Standardowy** lub **Premium,** aby wyświetlić wszystkie nowe zasoby usługi App Insights.
    > 
    > 
16. Kliknij przycisk **Deploy (Wdróż)**. Jeśli wybrano **opcję Zapisz hasła,** hasło zostanie zapisane w pliku parametrów **w postaci zwykłego tekstu**. W przeciwnym razie zostaniesz poproszony o wprowadzenie hasła bazy danych podczas procesu wdrażania.

Gotowe. Teraz wystarczy przejść do [witryny Azure Portal](https://portal.azure.com/) i narzędzia [Azure Resource Explorer,](https://resources.azure.com) aby wyświetlić nowe alerty i ustawienia skalowania automatycznego dodane do wdrożonej aplikacji JSON.

W swoich krokach w tej sekcji wykonano głównie następujące czynności:

1. Przygotowanie pliku szablonu
2. Utworzono plik parametrów, aby przejść z plikiem szablonu
3. Wdrożono plik szablonu z plikiem parametrów

Ostatni krok jest łatwo wykonywane przez polecenie cmdlet programu PowerShell. Aby zobaczyć, co program Visual Studio zrobił podczas wdrażania aplikacji, otwórz Scripts\Deploy-AzureResourceGroup.ps1. Istnieje wiele kodu, ale jestem po prostu podkreślić wszystkie odpowiedni kod, czego potrzebujesz, aby wdrożyć plik szablonu z plikiem parametrów.

![](./media/app-service-deploy-complex-application-predictably/deploy-12-powershellsnippet.png)

Ostatnie polecenie cmdlet, jest tym, `New-AzureResourceGroup`który faktycznie wykonuje akcję. Wszystko to powinno wykazać, że za pomocą narzędzi jest stosunkowo proste do wdrożenia aplikacji w chmurze przewidywalnie. Za każdym razem, gdy uruchamiasz polecenie cmdlet na tym samym szablonie z tym samym plikiem parametrów, uzyskasz ten sam wynik.

## <a name="summary"></a>Podsumowanie
W DevOps powtarzalności i przewidywalności są klucze do pomyślnego wdrożenia aplikacji na dużą skalę składa się z mikrousług. W tym samouczku wdrożono aplikację dwóch mikrousług na platformie Azure jako pojedynczą grupę zasobów przy użyciu szablonu usługi Azure Resource Manager. Mam nadzieję, że dał ci wiedzę, której potrzebujesz, aby rozpocząć konwertowanie aplikacji na platformie Azure na szablon i można ją aprowizować i wdrażać zgodnie z przewidywaniami. 

<a name="resources"></a>

## <a name="more-resources"></a>Więcej zasobów
* [Język szablonu usługi Azure Resource Manager](../azure-resource-manager/templates/template-syntax.md)
* [Tworzenie szablonów usługi Azure Resource Manager](../azure-resource-manager/templates/template-syntax.md)
* [Funkcje szablonów usługi Azure Resource Manager](../azure-resource-manager/templates/template-functions.md)
* [Wdrażanie aplikacji za pomocą szablonu usługi Azure Resource Manager](../azure-resource-manager/templates/deploy-powershell.md)
* [Używanie programu Azure PowerShell z usługą Azure Resource Manager](../azure-resource-manager/powershell-azure-resource-manager.md)
* [Rozwiązywanie problemów z wdrożeniami grup zasobów na platformie Azure](../azure-resource-manager/templates/common-deployment-errors.md)

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej o składni JSON i właściwościach typów zasobów wdrożonych w tym artykule, zobacz:

* [Microsoft.Sql/serwery](/azure/templates/microsoft.sql/servers)
* [Microsoft.Sql/serwery/bazy danych](/azure/templates/microsoft.sql/servers/databases)
* [Microsoft.Sql/servers/firewallRules](/azure/templates/microsoft.sql/servers/firewallrules)
* [Microsoft.Web/farmy serwerów](/azure/templates/microsoft.web/serverfarms)
* [Microsoft.Web/witryny](/azure/templates/microsoft.web/sites)
* [Microsoft.Web/sites/slots](/azure/templates/microsoft.web/sites/slots)
* [Microsoft.Insights/skalowanie automatyczne](/azure/templates/microsoft.insights/autoscalesettings)