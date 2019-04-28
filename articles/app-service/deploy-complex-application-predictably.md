---
title: Inicjowanie obsługi oraz wdrażanie mikrousług przewidywalny — usłudze Azure App Service
description: Dowiedz się, jak wdrożyć aplikację złożone z mikrousług w usłudze Azure App Service jako pojedyncza jednostka i w przewidywalny sposób przy użyciu szablony grup zasobów w formacie JSON i skryptów programu PowerShell.
services: app-service
documentationcenter: ''
author: cephalin
manager: erikre
editor: jimbe
ms.assetid: bb51e565-e462-4c60-929a-2ff90121f41d
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/06/2016
ms.author: cephalin
ms.custom: seodec18
ms.openlocfilehash: e6d18222e15f62f12592362827b6dbc4a3d7dfbc
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60766923"
---
# <a name="provision-and-deploy-microservices-predictably-in-azure"></a>Inicjowanie obsługi oraz wdrażanie mikrousług przewidywalny na platformie Azure
W tym samouczku pokazano, jak aprowizować i wdrażanie aplikacji składających się z [mikrousług](https://en.wikipedia.org/wiki/Microservices) w [usługi Azure App Service](https://azure.microsoft.com/services/app-service/) jako pojedyncza jednostka i w przewidywalny sposób za pomocą szablonów grup zasobów JSON i Wykonywanie skryptów programu PowerShell. 

Podczas inicjowania obsługi administracyjnej i wdrażania aplikacji o dużej skali, które składają się z wysoce odłączone mikrousług, powtarzalność i przewidywalność są niezwykle istotne, tak aby wskazywał Powodzenie. [Usługa Azure App Service](https://azure.microsoft.com/services/app-service/) pozwala na tworzenie mikrousług, która obejmuje aplikacje sieci web, mobilnych zaplecza i aplikacji API apps. [Usługa Azure Resource Manager](../azure-resource-manager/resource-group-overview.md) pozwala na zarządzanie wszystkie mikrousługi jako jednostka wraz z zależności zasobów, takich jak bazy danych i ustawienia kontroli źródła. Teraz można także wdrożyć takiej aplikacji za pomocą szablonów JSON i prostych skryptów środowiska PowerShell. 

## <a name="what-you-will-do"></a>Będziesz robić
W tym samouczku wdrożysz aplikację, która obejmuje:

* Dwie aplikacje usługi App Service (tj. dwa mikrousług)
* Wewnętrznej bazy danych SQL Database
* Ustawienia aplikacji, parametry połączenia i kontrola źródła
* Usługa Application insights, alerty, ustawienia skalowania automatycznego

## <a name="tools-you-will-use"></a>Narzędzia, które będą używane
W tym samouczku użyjesz następujących narzędzi. Ponieważ nie jest kompleksowe omówienie narzędzia, zamierzam używany scenariusz end-to-end, a także tylko krótkie wprowadzenie do każdego z nich, oraz gdzie znajdziesz więcej informacji na jego temat. 

### <a name="azure-resource-manager-templates-json"></a>Szablony usługi Azure Resource Manager (JSON)
Za każdym razem, gdy tworzysz aplikację w usłudze Azure App Service, na przykład usługi Azure Resource Manager używa szablon JSON do tworzenia całą grupę zasobów z zasobami składnika. Złożonych szablonów z [portalu Azure Marketplace](/azure/marketplace) może zawierać bazy danych, kont magazynu, plan usługi App Service, aplikacji, reguły alertów, ustawienia aplikacji, ustawień automatycznego skalowania i więcej, a te szablony są dostępne dla użytkownika za pomocą programu PowerShell. Aby uzyskać więcej informacji na temat szablonów usługi Azure Resource Manager, zobacz [Tworzenie szablonów usługi Resource Manager platformy Azure](../azure-resource-manager/resource-group-authoring-templates.md)

### <a name="azure-sdk-26-for-visual-studio"></a>Azure SDK 2.6 for Visual Studio
Najnowsze zestaw SDK zawiera ulepszenia dotyczące obsługi szablonów usługi Resource Manager w edytorze kodu JSON. Umożliwia szybkie tworzenie szablonu grupy zasobów od podstaw lub Otwórz istniejący szablon JSON (np. szablon galerii z pobranych) do modyfikacji, wypełnij plik parametrów i możliwe jest nawet wdrażanie grupy zasobów bezpośrednio z zasobu platformy Azure Rozwiązanie grupy.

Aby uzyskać więcej informacji, zobacz [Azure SDK 2.6 dla programu Visual Studio](https://azure.microsoft.com/blog/2015/04/29/announcing-the-azure-sdk-2-6-for-net/).

### <a name="azure-powershell-080-or-later"></a>Program Azure PowerShell 0.8.0 lub nowszej
Począwszy od wersji 0.8.0 instalacji programu Azure PowerShell zawiera moduł usługi Azure Resource Manager, oprócz modułu platformy Azure. Ten nowy moduł umożliwia skryptu wdrożenia grup zasobów.

Aby uzyskać więcej informacji, zobacz [przy użyciu programu Azure PowerShell z usługą Azure Resource Manager](../powershell-azure-resource-manager.md)

### <a name="azure-resource-explorer"></a>Eksplorator zasobów Azure
To [(wersja zapoznawcza) narzędzia](https://resources.azure.com) umożliwia poznawanie definicji JSON, wszystkich grup zasobów w subskrypcji i poszczególne zasoby. W narzędziu edytowania definicji JSON zasobu, Usuń całą hierarchię zasobów i tworzyć nowe zasoby.  Informacje, które są łatwo dostępne, w tym narzędziu są bardzo przydatne w przypadku tworzenia szablonu, ponieważ zawiera właściwości, które należy ustawić dla określonego typu zasobu poprawne wartości, itp. Możesz nawet utworzyć grupę zasobów w [witryny Azure Portal](https://portal.azure.com/), następnie sprawdź jej definicji JSON za pomocą narzędzia Eksplorator ułatwiające templatize grupy zasobów.

### <a name="deploy-to-azure-button"></a>Wdrażanie do przycisku platformy Azure
Jeśli używasz usługi GitHub do kontroli źródła, możesz umieścić [Wdróż do platformy Azure przycisku](https://azure.microsoft.com/blog/2014/11/13/deploy-to-azure-button-for-azure-websites-2/) do Twojego pliku README. MD, co umożliwia wdrożenie setką kompleksowych interfejsu użytkownika na platformie Azure. Chociaż można to zrobić dla każdej prostych aplikacji, można rozszerzyć ten element, aby umożliwić wdrażanie całą grupę zasobów, umieszczając plik azuredeploy.json w katalogu głównym repozytorium. Ten plik JSON, który zawiera szablon grupy zasobów, będzie służyć za wdrażanie do przycisku platformy Azure do utworzenia grupy zasobów. Aby uzyskać przykład, zobacz [ToDoApp](https://github.com/azure-appservice-samples/ToDoApp) próbki, który będzie używany w ramach tego samouczka.

## <a name="get-the-sample-resource-group-template"></a>Pobierz przykładowy szablon grupy zasobów
Teraz uzyskajmy prawo do niego.

1. Przejdź do [ToDoApp](https://github.com/azure-appservice-samples/ToDoApp) przykładowej usługi App Service.
2. Readme.md, kliknij **Wdróż na platformie Azure**.
3. Nastąpi przekierowanie do [wdrażanie na platformę azure](https://deploy.azure.com) lokacji i poproszony o podanie parametrów wdrożenia. Należy zauważyć, że większość pól są wypełniane przy użyciu nazwy repozytorium i niektóre ciągi losowe dla Ciebie. Można zmienić wszystkie pola, jeśli chcesz, ale tylko elementy, które należy wprowadzić to logowanie administracyjne programu SQL Server i hasło, a następnie kliknij **dalej**.
   
   ![](./media/app-service-deploy-complex-application-predictably/gettemplate-1-deploybuttonui.png)
4. Następnie kliknij przycisk **Wdróż** do rozpoczęcia procesu wdrażania. Gdy proces zostaje ukończone, kliknij przycisk http://todoapp *XXXX*. azurewebsites.net link umożliwiający przejście wdrożonej aplikacji. 
   
   ![](./media/app-service-deploy-complex-application-predictably/gettemplate-2-deployprogress.png)
   
   Interfejs użytkownika będzie wolno nieco po użytkownik najpierw przejść do niego, ponieważ aplikacje są właśnie zaczynają, ale przekonać się, jest w pełni funkcjonalnych aplikacji.
5. Ponownie Wdróż na stronie kliknij **Zarządzaj** link, aby wyświetlić nową aplikację w witrynie Azure Portal.
6. W **Essentials** listy rozwijanej, kliknij link grupy zasobów. Należy zauważyć, że aplikacja jest już połączona z repozytorium GitHub w ramach **projektu zewnętrznego**. 
   
   ![](./media/app-service-deploy-complex-application-predictably/gettemplate-3-portalresourcegroup.png)
7. W bloku grupy zasobów należy pamiętać, że istnieją już dwóch aplikacji i jednej bazy danych SQL w grupie zasobów.
   
   ![](./media/app-service-deploy-complex-application-predictably/gettemplate-4-portalresourcegroupclicked.png)

Wszystko, co właśnie uruchomiliśmy w ciągu kilku minut krótki w przypadku aplikacji pełni wdrożone dwa mikrousług przy użyciu wszystkich składników, zależności, ustawienia, bazy danych i publikowanie ciągłe skonfigurowany przez zautomatyzowanej aranżacji w usłudze Azure Resource Manager. Wszystko, co zostało to zrobione za dwie rzeczy:

* Wdróż do przycisku platformy Azure
* azuredeploy.JSON w katalogu głównym repozytorium

Możesz wdrożyć tę samą aplikację dziesiątki, setki lub tysiące razy i mieć dokładnie tę samą konfigurację zawsze. Powtarzalność i przewidywalność takie podejście umożliwia wdrażanie aplikacji o dużej skali z łatwością i pewnością.

## <a name="examine-or-edit-azuredeployjson"></a>Sprawdź lub edytować AZUREDEPLOY. JSON
Teraz Przyjrzyjmy się konfiguracji repozytorium GitHub. Będziesz używać edytora JSON zestawu Azure .NET SDK, więc jeśli nie została jeszcze zainstalowana [Azure .NET SDK 2.6](https://azure.microsoft.com/downloads/), zrób to teraz.

1. Klonuj [ToDoApp](https://github.com/azure-appservice-samples/ToDoApp) repozytorium za pomocą narzędzia swoje ulubione narzędzia git. Na poniższym zrzucie ekranu robimy to w programie Team Explorer w programie Visual Studio 2013.
   
   ![](./media/app-service-deploy-complex-application-predictably/examinejson-1-vsclone.png)
2. Korzystając z katalogu głównego repozytorium Otwórz azuredeploy.json w programie Visual Studio. Jeśli nie widzisz okienka konspekt pliku JSON, musisz zainstalować zestaw Azure .NET SDK.
   
   ![](./media/app-service-deploy-complex-application-predictably/examinejson-2-vsjsoneditor.png)

Nie zamierzam opisano szczegóły każdego zdarzenia w formacie JSON, ale [więcej zasobów](#resources) sekcji znajdują się linki do nauki języka szablonu grupy zasobów. W tym miejscu po prostu zamierzam dowiesz się, że interesujące funkcje, które mogą pomóc Ci rozpocząć tworzenie szablonu niestandardowego dla wdrożenia aplikacji.

### <a name="parameters"></a>Parametry
Przyjrzyj się w sekcji parametrów, aby zobaczyć, że większość z tych parametrów są co **Wdróż na platformie Azure** przycisk monituje o dane wejściowe. Witryna za **Wdróż na platformie Azure** przycisk wypełnia danych wejściowych interfejsu użytkownika przy użyciu parametrów zdefiniowanych w azuredeploy.json. Te parametry są używane w definicjach zasobów, takich jak nazwy zasobów, wartości właściwości itp.

### <a name="resources"></a>Zasoby
W węźle zasoby można zobaczyć, że 4 zasoby najwyższego poziomu są zdefiniowane, w tym wystąpieniu programu SQL Server, plan usługi App Service i dwie aplikacje. 

#### <a name="app-service-plan"></a>Plan usługi App Service
Zacznijmy od prostego zasobu katalogu głównego w formacie JSON. W konspekt pliku JSON, kliknij plan usługi App Service o nazwie **[hostingPlanName]** aby wyróżnić odpowiedni kod JSON. 

![](./media/app-service-deploy-complex-application-predictably/examinejson-3-appserviceplan.png)

Należy pamiętać, że `type` element określa parametry dla planu usługi App Service (została wywołana farmy serwerów długie, długi czas temu) i innych elementów i właściwości są wypełniane przy użyciu parametrów zdefiniowanych w pliku JSON, a ten zasób nie ma żadnego zagnieżdżone zasoby.

> [!NOTE]
> Należy zauważyć, że wartość `apiVersion` nakazuje platformie Azure, którą wersję interfejsu API REST do użycia JSON definicji zasobu z, a może mieć wpływ na sposób zasobu powinny być sformatowane w `{}`. 
> 
> 

#### <a name="sql-server"></a>Oprogramowanie SQL Server
Następnie kliknij zasób programu SQL Server o nazwie **SQLServer** w konspekt pliku JSON.

![](./media/app-service-deploy-complex-application-predictably/examinejson-4-sqlserver.png)

Należy pamiętać o następujących dotyczących wyróżniony kod JSON:

* Użycie parametrów zapewnia, że utworzone zasoby są o nazwie i skonfigurowane w taki sposób, że ich zgodne ze sobą.
* Zasób SQLServer ma dwa zasoby zagnieżdżonych, każdy z nich ma różne wartości `type`.
* Zagnieżdżone zasobów w obrębie `“resources”: […]`, gdzie są zdefiniowane bazy danych i reguł zapory, mają `dependsOn` element, który określa identyfikator zasobu zasobów SQLServer poziomu głównego. "Przed przystąpieniem do tworzenia tego zasobu, który inny zasób musi już istnieć; informuje usługi Azure Resource Manager a jeśli ten inny zasób jest zdefiniowany w szablonie, następnie utwórz ten. najpierw".
  
  > [!NOTE]
  > Aby uzyskać szczegółowe informacje dotyczące sposobu używania `resourceId()` funkcji, zobacz [funkcje szablonu usługi Azure Resource Manager](../azure-resource-manager/resource-group-template-functions-resource.md#resourceid).
  > 
  > 
* Efekt `dependsOn` element jest, że usługi Azure Resource Manager można wiedzą, które zasoby mogą zostać utworzone równolegle i które zasoby, które muszą być tworzone po kolei. 

#### <a name="app-service-app"></a>Aplikacja usługi App Service
Teraz przejdźmy do rzeczywistej aplikacji, które są bardziej skomplikowane. Kliknij aplikację [variables('apiSiteName')] w konspekt pliku JSON, aby wyróżnić jego kod JSON. Można zauważyć, że rzeczy stają się coraz bardziej interesujące. W tym celu omówię funkcji:

##### <a name="root-resource"></a>Zasób katalogu głównego
Aplikacja jest zależna od dwóch różnych zasobów. Oznacza to, że usługi Azure Resource Manager zostanie utworzona aplikacja, tylko wtedy, gdy zarówno plan usługi App Service, jak i wystąpienia programu SQL Server są tworzone.

![](./media/app-service-deploy-complex-application-predictably/examinejson-5-webapproot.png)

##### <a name="app-settings"></a>Ustawienia aplikacji
Ustawienia aplikacji również są definiowane jako zagnieżdżonych zasobów.

![](./media/app-service-deploy-complex-application-predictably/examinejson-6-webappsettings.png)

W `properties` elementu `config/appsettings`, mają dwa ustawienia aplikacji w formacie `"<name>" : "<value>"`.

* `PROJECT` jest [ustawienie KUDU](https://github.com/projectkudu/kudu/wiki/Customizing-deployments) który informuje wdrażania na platformie Azure do projektu w rozwiązaniu Visual Studio obejmujących wiele projektów. Pokażę, pokażemy, jak skonfigurowano kontroli źródła, ale ponieważ kod ToDoApp znajduje się w rozwiązaniu Visual Studio obejmujących wiele projektów, musimy to ustawienie.
* `clientUrl` jest po prostu aplikację ustawienia kodu aplikacji.

##### <a name="connection-strings"></a>Parametry połączeń
Parametry połączenia są również określone jako zasób zagnieżdżonych.

![](./media/app-service-deploy-complex-application-predictably/examinejson-7-webappconnstr.png)

W `properties` elementu `config/connectionstrings`, każdy ciąg połączenia jest również zdefiniowana jako parę nazwa: wartość w określonym formacie `"<name>" : {"value": "…", "type": "…"}`. Aby uzyskać `type` elementu, możliwe wartości to `MySql`, `SQLServer`, `SQLAzure`, i `Custom`.

> [!TIP]
> Ostateczne listę typów ciągów połączeń uruchom następujące polecenie w programie Azure PowerShell: \[Enum]::GetNames("Microsoft.WindowsAzure.Commands.Utilities.Websites.Services.WebEntities.DatabaseType")
> 
> 

##### <a name="source-control"></a>Kontrola źródła
Ustawienia kontroli źródła są również określone jako zasób zagnieżdżonych. Usługa Azure Resource Manager używa tego zasobu, aby skonfigurować publikowanie ciągłe (zobacz zastrzeżenie: na `IsManualIntegration` później) a także uruchamiał wdrażania kodu aplikacji automatycznie podczas przetwarzania pliku JSON.

![](./media/app-service-deploy-complex-application-predictably/examinejson-8-webappsourcecontrol.png)

`RepoUrl` i `branch` powinny być bardzo intuicyjna i powinien wskazywać repozytorium Git i nazwę gałęzi, aby opublikować z. Ponownie są one definiowane przez parametry wejściowe. 

Zwróć uwagę, w `dependsOn` elementu, oprócz zasobów aplikacji, `sourcecontrols/web` zależy również od `config/appsettings` i `config/connectionstrings`. Jest to spowodowane po `sourcecontrols/web` jest skonfigurowany, proces wdrażania platformy Azure automatycznie podejmie próbę wdrożenia, skompilować i uruchomić kod aplikacji. W związku z tym Wstawianie tę zależność pomaga upewnić się, że aplikacja ma dostęp do ustawień wymaganych aplikacji i parametry połączenia, przed uruchomieniem kodu aplikacji. 

> [!NOTE]
> Należy zauważyć, że `IsManualIntegration` ustawiono `true`. Ta właściwość jest to konieczne, w tym samouczku, ponieważ nie są faktycznie właścicielami repozytorium GitHub, a zatem nie może faktycznie Przyznaj uprawnienie do platformy Azure, aby skonfigurować publikowanie ciągłe z [ToDoApp](https://github.com/azure-appservice-samples/ToDoApp) (czyli wypychania automatycznego repozytorium aktualizacje platformy Azure). Możesz użyć wartości domyślnej `false` dla określonego repozytorium tylko wtedy, gdy skonfigurowano poświadczenia usługi GitHub właściciela w [witryny Azure portal](https://portal.azure.com/) przed. Innymi słowy Jeśli skonfigurowano kontroli źródła do usługi GitHub lub BitBucket dla każdej aplikacji w [witryny Azure Portal](https://portal.azure.com/) wcześniej przy użyciu użytkownika poświadczeń, Azure będą Pamiętaj poświadczenia i ich używać każdą aplikację z GitHub lub BitBucket w przyszłości. Jednak jeśli nie zostało to jeszcze zrobione, wdrożenie szablonu JSON zakończy się niepowodzeniem podczas usługi Azure Resource Manager można skonfigurować ustawienia kontroli źródła dla aplikacji, ponieważ nie można zalogować w usłudze GitHub lub BitBucket, przy użyciu poświadczeń właściciela repozytorium.
> 
> 

## <a name="compare-the-json-template-with-deployed-resource-group"></a>Porównaj szablon JSON z grupą zasobów wdrożonych
W tym miejscu można przejść przez wszystkie aplikacji bloków w [witryny Azure Portal](https://portal.azure.com/), ale ma inne narzędzie, które są równie przydatne, jeśli nie jest więcej. Przejdź do [Eksploratora zasobów Azure](https://resources.azure.com) (wersja zapoznawcza) narzędzia, które umożliwia reprezentacja JSON wszystkich grup zasobów w Twoich subskrypcjach, jak faktycznie istnieją one w wewnętrznej bazie danych Azure. Widać również, jak odpowiada hierarchii JSON grupy zasobów na platformie Azure z hierarchią w pliku szablonu, który służy do jego utworzenia.

Na przykład, po przejściu do [Eksploratora zasobów Azure](https://resources.azure.com) narzędzia i rozwinąć węzły w Eksploratorze, widać, grupa zasobów i zasobów katalogu głównego, które są zbierane w ramach ich typy odpowiednich zasobów.

![](./media/app-service-deploy-complex-application-predictably/ARM-1-treeview.png)

Jeśli możesz przejść do aplikacji, powinno być możliwe wyświetlić szczegóły konfiguracji aplikacji, podobnie jak poniższy zrzut ekranu:

![](./media/app-service-deploy-complex-application-predictably/ARM-2-jsonview.png)

Ponownie zagnieżdżonych zasobów powinny mieć hierarchię bardzo podobne do tych w pliku szablonu JSON i powinien zostać wyświetlony ustawienia aplikacji, parametry połączenia, itp., prawidłowo odzwierciedlane w okienku JSON. Brak ustawienia może oznaczać problem z pliku JSON i może pomóc w rozwiązaniu plik szablonu JSON.

## <a name="deploy-the-resource-group-template-yourself"></a>Wdrażanie szablonu grupy zasobów, samodzielnie
**Wdróż na platformie Azure** dobrze jest przycisk, ale pozwala na wdrożenie szablonu grupy zasobów w azuredeploy.json, tylko wtedy, gdy azuredeploy.json zostały już przekazane do usługi GitHub. Zestaw Azure .NET SDK udostępnia narzędzia do wdrażania wszystkich plików szablonu JSON bezpośrednio z komputera lokalnego. Aby to zrobić, wykonaj następujące czynności:

1. W programie Visual Studio kliknij kolejno pozycje **Plik** > **Nowy** > **Projekt**.
2. Kliknij przycisk **Visual C#** > **chmury** > **grupy zasobów platformy Azure**, następnie kliknij przycisk **OK**.
   
   ![](./media/app-service-deploy-complex-application-predictably/deploy-1-vsproject.png)
3. W **wybierz szablon Azure**, wybierz opcję **pustego szablonu** i kliknij przycisk **OK**.
4. Przeciągnij azuredeploy.json do **szablonu** folder nowego projektu.
   
   ![](./media/app-service-deploy-complex-application-predictably/deploy-2-copyjson.png)
5. W Eksploratorze rozwiązań otwórz skopiowany azuredeploy.json.
6. Tylko w celach demonstracyjnych, możemy dodać niektóre standardowe zasoby programu Application Insights do naszego pliku JSON, klikając **Dodaj zasób**. Jeśli interesuje Cię tylko wdrażanie pliku JSON, przejdź do procedury wdrażania.
   
   ![](./media/app-service-deploy-complex-application-predictably/deploy-3-newresource.png)
7. Wybierz **usługi Application Insights dla aplikacji sieci Web**, upewnij się, istniejącego planu usługi App Service i aplikacji jest zaznaczone, a następnie kliknij przycisk **Dodaj**.
   
   ![](./media/app-service-deploy-complex-application-predictably/deploy-4-newappinsight.png)
   
   Teraz będzie można wyświetlić kilka nowych zasobów, w zależności od zasobów i jakie operacje, są zależne od planu usługi App Service lub aplikacji. Te zasoby nie są włączone przez ich istniejącą definicję i zamierzasz zmienić.
   
   ![](./media/app-service-deploy-complex-application-predictably/deploy-5-appinsightresources.png)
8. Konspekt pliku JSON, kliknij **appInsights skalowania automatycznego** aby wyróżnić jego kod JSON. To ustawienie skalowania dla planu usługi App Service.
9. W wyróżnionych kod JSON, zlokalizuj `location` i `enabled` właściwości i ustaw je tak, jak pokazano poniżej.
   
   ![](./media/app-service-deploy-complex-application-predictably/deploy-6-autoscalesettings.png)
10. Konspekt pliku JSON, kliknij **CPUHigh appInsights** aby wyróżnić jego kod JSON. Jest to alert.
11. Znajdź `location` i `isEnabled` właściwości i ustaw je tak, jak pokazano poniżej. Zrób to samo dla innych alertów trzy (purpurowe żarówek).
    
    ![](./media/app-service-deploy-complex-application-predictably/deploy-7-alerts.png)
12. Teraz możesz przystąpić do wdrażania. Kliknij prawym przyciskiem myszy projekt i wybierz **Wdróż** > **nowe wdrożenie**.
    
    ![](./media/app-service-deploy-complex-application-predictably/deploy-8-newdeployment.png)
13. Jeśli jeszcze tego nie zrobiono, należy zalogować się do konta platformy Azure.
14. Wybierz istniejącą grupę zasobów w ramach subskrypcji lub utworzyć nowy wybierz jedną, **azuredeploy.json**, a następnie kliknij przycisk **Edytuj parametry**.
    
    ![](./media/app-service-deploy-complex-application-predictably/deploy-9-deployconfig.png)
    
    Teraz będzie można edytować wszystkie parametry, które są zdefiniowane w pliku szablonu w tabeli dobre rozwiązanie. Parametry, które definiują ustawienia domyślne mają już wartości domyślne, a parametry, które definiują listę dozwolonych wartości, które będą wyświetlane jako list rozwijanych.
    
    ![](./media/app-service-deploy-complex-application-predictably/deploy-10-parametereditor.png)
15. Wypełnij puste parametry, a następnie użyj [adres repozytorium GitHub dla ToDoApp](https://github.com/azure-appservice-samples/ToDoApp.git) w **repoUrl**. Następnie kliknij przycisk **Zapisz**.
    
    ![](./media/app-service-deploy-complex-application-predictably/deploy-11-parametereditorfilled.png)
    
    > [!NOTE]
    > Skalowanie automatyczne to funkcja oferowana w **standardowa** warstwy lub nowszej i poziom planu alerty są funkcji oferowanych w ramach **podstawowe** warstwy lub nowszej, musisz ustawić **jednostki sku** parametru Aby **standardowa** lub **Premium** zobaczenie wszystkich nowych App Insights zasobów światła w górę.
    > 
    > 
16. Kliknij przycisk **Deploy (Wdróż)**. W przypadku wybrania **zapisywać hasła**, hasło zostanie zapisane w pliku parametrów **w postaci zwykłego tekstu**. W przeciwnym razie użytkownik zostanie zapytany wprowadzanie hasła bazy danych w procesie wdrażania.

Gotowe. Teraz po prostu musisz przejść do [witryny Azure Portal](https://portal.azure.com/) i [Eksploratora zasobów Azure](https://resources.azure.com) narzędzie, aby zobaczyć nowe alerty i ustawienia automatycznego skalowania, dodane do JSON wdrożono aplikację.

Kroki w taki sposób, w tej sekcji głównie wykonano następujące czynności:

1. Przygotowanie pliku szablonu
2. Utworzony plik parametrów, aby przejść z pliku szablonu
3. Wdrożyć plik szablonu przy użyciu pliku parametrów

Ostatnim krokiem łatwo odbywa się przez polecenia cmdlet programu PowerShell. Aby zobaczyć, co program Visual Studio czy po jej wdrożeniu aplikacji, otwórz Scripts\Deploy AzureResourceGroup.ps1. Jest dużo kodu istnieje, ale po prostu użyję zaznacz odpowiednie kodu, należy wdrożyć plik szablonu przy użyciu pliku parametrów.

![](./media/app-service-deploy-complex-application-predictably/deploy-12-powershellsnippet.png)

Polecenia cmdlet ostatniego `New-AzureResourceGroup`, który faktycznie wykonuje akcję. To wszystko powinno pokazują, do Ciebie, za pomocą narzędzi jest stosunkowo prosta do wdrożenia aplikacji w chmurze przewidywalne. Za każdym razem, gdy uruchomieniu polecenia cmdlet na tym samym szablonie przy użyciu tego samego pliku parametrów użytkownik chce uzyskać ten sam wynik.

## <a name="summary"></a>Podsumowanie
W metodyce DevOps powtarzalność i przewidywalność są klucze do dowolnego pomyślne wdrożenie aplikacji o dużej skali składające się z mikrousług. W tym samouczku wdrożono aplikację dwóch mikrousług na platformie Azure jako pojedynczą grupę zasobów przy użyciu szablonu usługi Azure Resource Manager. Mamy nadzieję, że jej przyznał Ci wiedzę, których potrzebujesz, aby można było uruchomić konwertowania aplikacji na platformie Azure do szablonu można aprowizować i wdrożyć ją w przewidywalny. 

<a name="resources"></a>

## <a name="more-resources"></a>Więcej zasobów
* [Język szablonu usługi Azure Resource Manager](../azure-resource-manager/resource-group-authoring-templates.md)
* [Tworzenie szablonów usługi Azure Resource Manager](../azure-resource-manager/resource-group-authoring-templates.md)
* [Funkcje szablonu usługi Azure Resource Manager](../azure-resource-manager/resource-group-template-functions.md)
* [Wdrażanie aplikacji przy użyciu szablonu usługi Azure Resource Manager](../azure-resource-manager/resource-group-template-deploy.md)
* [Używanie programu Azure PowerShell z usługą Azure Resource Manager](../azure-resource-manager/powershell-azure-resource-manager.md)
* [Rozwiązywanie problemów z wdrożeniami grup zasobów na platformie Azure](../azure-resource-manager/resource-manager-common-deployment-errors.md)

## <a name="next-steps"></a>Kolejne kroki

Aby dowiedzieć się więcej na temat składni JSON i właściwości dla typów zasobów wdrożonych w tym artykule, zobacz:

* [Microsoft.Sql/servers](/azure/templates/microsoft.sql/servers)
* [Microsoft.Sql/servers/databases](/azure/templates/microsoft.sql/servers/databases)
* [Microsoft.Sql/servers/firewallRules](/azure/templates/microsoft.sql/servers/firewallrules)
* [Microsoft.Web/serverfarms](/azure/templates/microsoft.web/serverfarms)
* [Microsoft.Web/sites](/azure/templates/microsoft.web/sites)
* [Microsoft.Web/sites/slots](/azure/templates/microsoft.web/sites/slots)
* [Microsoft.Insights/autoscalesettings](/azure/templates/microsoft.insights/autoscalesettings)