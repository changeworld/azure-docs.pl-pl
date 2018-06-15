---
title: Aprowizacji i wdrażania mikrousług przewidywalnego na platformie Azure
description: Dowiedz się, jak wdrożyć aplikację składa się z mikrousług w usłudze Azure App Service jako pojedyncza jednostka i w sposób przewidywalny przy użyciu szablonów grupy zasobów JSON i skrypty programu PowerShell.
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
ms.openlocfilehash: 3719e037f1564411a8f94d1ca962ba1ef6b5d435
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/11/2017
ms.locfileid: "23837099"
---
# <a name="provision-and-deploy-microservices-predictably-in-azure"></a>Aprowizacji i wdrażania mikrousług przewidywalnego na platformie Azure
W tym samouczku przedstawiono sposób obsługi administracyjnej i wdrażania aplikacji składający się z [mikrousług](https://en.wikipedia.org/wiki/Microservices) w [usłudze Azure App Service](/services/app-service/) jako pojedyncza jednostka i w sposób przewidywalny przy użyciu szablonów grupy zasobów JSON i skrypty programu PowerShell. 

Podczas inicjowania obsługi administracyjnej i wdrażania aplikacji wysokiej skali, które składają się z bardzo całkowicie niezależna mikrousług, powtarzalność i przewidywalności są podstawowe znaczenie dla sukcesu. [Usługa aplikacji Azure](/services/app-service/) umożliwia utworzenie mikrousług, który zawiera aplikacje sieci web, aplikacje mobilne, aplikacje interfejsu API i aplikacje logiki. [Usługa Azure Resource Manager](../azure-resource-manager/resource-group-overview.md) pozwala na zarządzanie wszystkich mikrousług jako jednostki, wraz z zależności zasobów, takich jak bazy danych i ustawień kontroli źródła. Teraz można także wdrożyć takiej aplikacji przy użyciu szablony JSON i proste skrypty środowiska PowerShell. 

[!INCLUDE [app-service-web-to-api-and-mobile](../../includes/app-service-web-to-api-and-mobile.md)]

## <a name="what-you-will-do"></a>Będzie wykonywać
W samouczku wdrożysz aplikację, która obejmuje:

* Dwie sieci web aplikacji (tj. dwa mikrousług)
* Wewnętrznej bazy danych SQL Database
* Ustawienia aplikacji, parametry połączenia i kontroli źródła
* Usługa Application insights, alerty, ustawienia skalowania automatycznego

## <a name="tools-you-will-use"></a>Narzędzia, które będą używane
W tym samouczku użyjesz następujących narzędzi. Ponieważ nie jest kompleksowe omówienie narzędzia, użyjemy osadzania scenariusz end-to-end i po prostu umożliwiają krótkie wprowadzenie do poszczególnych usług, i gdzie można znaleźć więcej informacji na nim. 

### <a name="azure-resource-manager-templates-json"></a>Szablony usługi Azure Resource Manager (JSON)
Za każdym razem, gdy utworzysz aplikację sieci web w usłudze Azure App Service, na przykład usługi Azure Resource Manager używa szablonu JSON utworzyć całej grupy zasobów z zasobami składnika. Szablon złożonych z [portalu Azure Marketplace](/marketplace) jak [Scalable WordPress](/marketplace/partners/wordpress/scalablewordpress/) aplikacji mogą obejmować bazy danych MySQL, konta magazynu, plan usługi aplikacji, aplikacji sieci web, reguły alertów, ustawienia aplikacji, ustawienia skalowania automatycznego i inne, a te szablony są dostępne za pośrednictwem programu PowerShell. Aby uzyskać informacje o sposobie pobrania i zastosowania tych szablonów, zobacz [przy użyciu programu Azure PowerShell z usługą Azure Resource Manager](../powershell-azure-resource-manager.md).

Aby uzyskać więcej informacji dotyczących szablonów usługi Azure Resource Manager, zobacz [Authoring Azure Resource Manager szablonów](../azure-resource-manager/resource-group-authoring-templates.md)

### <a name="azure-sdk-26-for-visual-studio"></a>Zestaw Azure SDK 2.6 dla programu Visual Studio
Najnowsze zestaw SDK zawiera ulepszenia obsługi szablonu usługi Resource Manager w edytorze JSON. Można umożliwia szybkie tworzenie grupy zasobów szablonu od początku lub Otwórz istniejący szablon JSON (na przykład szablon pobrany galerii) do modyfikacji, wypełnić pliku parametrów, a nawet wdrożyć grupę zasobów bezpośrednio z rozwiązania do grupy zasobów platformy Azure.

Aby uzyskać więcej informacji, zobacz [2.6 zestawu SDK platformy Azure dla programu Visual Studio](https://azure.microsoft.com/blog/2015/04/29/announcing-the-azure-sdk-2-6-for-net/).

### <a name="azure-powershell-080-or-later"></a>Program Azure PowerShell 0.8.0 lub nowszy
Począwszy od wersji 0.8.0, instalacja programu Azure PowerShell obejmuje moduł usługi Azure Resource Manager oprócz modułu platformy Azure. Ten nowy moduł umożliwia skryptów wdrażania grup zasobów.

Aby uzyskać więcej informacji, zobacz [przy użyciu programu Azure PowerShell z usługą Azure Resource Manager](../powershell-azure-resource-manager.md)

### <a name="azure-resource-explorer"></a>Eksplorator zasobów Azure
To [narzędzia Podgląd](https://resources.azure.com) umożliwia Eksplorowanie JSON definicje wszystkich grup zasobów w Twojej subskrypcji i pojedynczych zasobów. W narzędziu można edytować JSON definicji zasobu, Usuń całej hierarchii zasobów i Utwórz nowe zasoby.  Łatwo dostępne w tym narzędziu informacje są bardzo przydatne do tworzenia szablonu, ponieważ zawiera ona właściwości, które należy ustawić dla określonego typu zasobu poprawnych wartości, itp. Można również utworzyć grupy zasobów w [Azure Portal](https://portal.azure.com/), następnie sprawdź ich definicje JSON za pomocą narzędzia Eksplorator ułatwiające templatize grupy zasobów.

### <a name="deploy-to-azure-button"></a>Wdrażanie na przycisku Azure
Jeśli używasz usługi GitHub dla kontroli źródła, możesz umieścić [wdrażanie na przycisku Azure](https://azure.microsoft.com/blog/2014/11/13/deploy-to-azure-button-for-azure-websites-2/) do Twojej Plik README. MD, co umożliwia wdrożenie gotowe interfejsu użytkownika na platformie Azure. Czynności można wykonać dla dowolnej aplikacji sieci web proste, można rozszerzyć tutaj, aby włączyć wdrażanie całej grupy zasobów, ustawiając plik azuredeploy.json w folderze głównym repozytorium. Ten plik JSON, który zawiera szablon grupy zasobów, będzie służyć za wdrażanie na przycisku Azure można utworzyć grupy zasobów. Na przykład zobacz [ToDoApp](https://github.com/azure-appservice-samples/ToDoApp) próbki, który będzie używany w tym samouczku.

## <a name="get-the-sample-resource-group-template"></a>Pobierz przykładowy szablon grupy zasobów
Dlatego teraz, zaloguj się w prawo, aby go.

1. Przejdź do [ToDoApp](https://github.com/azure-appservice-samples/ToDoApp) przykładowej aplikacji usługi.
2. W readme.md, kliknij przycisk **wdrażanie na platformie Azure**.
3. W przypadku podjąć w celu [wdrożyć do azure](https://deploy.azure.com) lokacji i poproszony o podanie parametrów wdrożenia. Zwróć uwagę, że większość pól są wypełniane przy użyciu nazwy repozytorium i niektórych losowe ciągów dla Ciebie. Można zmienić wszystkie pola, jeśli chcesz, ale tylko elementy, które należy wprowadzić są logowania administratora serwera SQL i hasło, a następnie kliknij przycisk **dalej**.
   
   ![](./media/app-service-deploy-complex-application-predictably/gettemplate-1-deploybuttonui.png)
4. Następnie kliknij przycisk **Wdróż** do rozpoczęcia procesu wdrażania. Po uruchomieniu do ukończenia procesu kliknij http://todoapp*XXXX*. azurewebsites.net link, aby przejrzeć wdrożonej aplikacji. 
   
   ![](./media/app-service-deploy-complex-application-predictably/gettemplate-2-deployprogress.png)
   
   Interfejsu użytkownika może zająć nieco podczas najpierw przechodzenia do niego, ponieważ właśnie uruchamianych aplikacji, ale przekonać się, jest pełnej funkcjonalności aplikacji.
5. Ponownie Wdróż na stronie kliknij **Zarządzaj** łącze, aby wyświetlić nową aplikację w portalu Azure.
6. W **Essentials** listy rozwijanej, kliknij łącze grupę zasobów. Należy też zauważyć, że aplikacja sieci web jest już połączony z repozytorium GitHub pod **projektu zewnętrznego**. 
   
   ![](./media/app-service-deploy-complex-application-predictably/gettemplate-3-portalresourcegroup.png)
7. W bloku grupy zasobów należy pamiętać, że istnieją już dwie aplikacje sieci web i jednej bazy danych SQL w grupie zasobów.
   
   ![](./media/app-service-deploy-complex-application-predictably/gettemplate-4-portalresourcegroupclicked.png)

Wszystko, który został wyświetlony w ciągu kilku minut krótkich aplikacją pełni wdrożone mikrousługi dwa z wszystkich składników, zależności, ustawienia, bazy danych i ciągłe publikowanie skonfigurowany przez automatyczne aranżacji w usłudze Azure Resource Manager. Wszystko to zostało to zrobione przez dwie czynności:

* Wdrażanie na przycisku Azure
* azuredeploy.JSON w folderze głównym repozytorium

Można wdrożyć tej samej aplikacji dziesiątki, setek lub tysięcy razy i mają taką samą konfigurację dokładne zawsze. Powtarzalność i przewidywalność takie podejście umożliwia wdrażanie aplikacji w dużej skali mogliby łatwo i zaufania.

## <a name="examine-or-edit-azuredeployjson"></a>Sprawdź (lub edytować) AZUREDEPLOY. JSON
Teraz Przyjrzyjmy się konfiguracji repozytorium GitHub. Będziesz używać edytora JSON w zestawie SDK .NET usługi Azure, tak więc jeśli nie została jeszcze zainstalowana [Azure .NET SDK w wersji 2.6](https://azure.microsoft.com/downloads/), zrób to teraz.

1. Klonowanie [ToDoApp](https://github.com/azure-appservice-samples/ToDoApp) repozytorium narzędzie git ulubionych. Na poniższym zrzucie ekranu robimy to w programie Team Explorer programu Visual Studio 2013.
   
   ![](./media/app-service-deploy-complex-application-predictably/examinejson-1-vsclone.png)
2. W katalogu głównym repozytorium Otwórz azuredeploy.json w programie Visual Studio. Jeśli nie jest widoczny w okienku Konspekt pliku JSON, musisz zainstalować zestawu SDK .NET usługi Azure.
   
   ![](./media/app-service-deploy-complex-application-predictably/examinejson-2-vsjsoneditor.png)

Użyjemy nie opisano każdy szczegółów formatu JSON, ale [więcej zasobów](#resources) sekcja zawiera łącza do uczenia język szablonu grupy zasobów. W tym miejscu wystarczy użyjemy do pokazania wprowadzenie interesujące funkcje, które mogą pomóc Ci w podejmowaniu szablonu niestandardowego dla wdrożenia aplikacji.

### <a name="parameters"></a>Parametry
Spójrz na sekcji Parametry, aby zobaczyć, że większość te parametry są co **wdrażanie na platformie Azure** przycisk monituje. Witryna za **wdrażanie na platformie Azure** przycisk wypełnia interfejsu użytkownika przy użyciu parametrów zdefiniowanych w azuredeploy.json danych wejściowych. Te parametry są używane w definicjach zasobów, takich jak nazwy zasobów, wartości właściwości, itd.

### <a name="resources"></a>Zasoby
W węźle zasobów widać, że są zdefiniowane 4 zasobów najwyższego poziomu, w tym wystąpienia programu SQL Server, plan usługi aplikacji i dwie aplikacje sieci web. 

#### <a name="app-service-plan"></a>Plan usługi App Service
Zacznijmy od prostego zasobów poziomu głównego, w formacie JSON. W konspekt pliku JSON, kliknij plan usługi aplikacji o nazwie **[hostingPlanName]** zaznacz odpowiedni kod JSON. 

![](./media/app-service-deploy-complex-application-predictably/examinejson-3-appserviceplan.png)

Należy pamiętać, że `type` element określa parametry dla planu usługi App Service (została wywołana farmy serwerów długie, długi czas temu) i inne elementy i właściwości są wypełniane przy użyciu parametrów zdefiniowanych w pliku JSON, a ten zasób nie zawiera żadnych zagnieżdżonych zasobów.

> [!NOTE]
> Należy zauważyć, że wartość `apiVersion` informuje Azure, która wersja interfejsu API REST do użycia JSON definicji zasobu z, a może mieć wpływ na sposób zasób powinien być sformatowany wewnątrz `{}`. 
> 
> 

#### <a name="sql-server"></a>Oprogramowanie SQL Server
Następnie kliknij przycisk na zasób programu SQL Server o nazwie **SQLServer** w konspekt pliku JSON.

![](./media/app-service-deploy-complex-application-predictably/examinejson-4-sqlserver.png)

Należy zwrócić uwagę na poniższe kwestie wyróżniony kod JSON:

* Użycie parametrów temu utworzone zasoby są o nazwie i skonfigurowane w sposób umożliwiający ich zgodne ze sobą.
* Zasób SQLServer ma dwa zagnieżdżonych zasobów, każdy ma inną wartość dla `type`.
* Zagnieżdżonych zasobów wewnątrz `“resources”: […]`, gdzie są zdefiniowane bazy danych i jego reguły zapory, mieć `dependsOn` element, który określa identyfikator zasobu zasobu SQLServer głównego poziomu. Ta wartość informuje usługi Azure Resource Manager "przed utworzeniem tego zasobu, który innego zasobu musi już istnieć; i innych zasobu jest zdefiniowany w szablonie, następnie co najpierw utworzyć".
  
  > [!NOTE]
  > Aby uzyskać szczegółowe informacje dotyczące sposobu używania `resourceId()` funkcji, zobacz [funkcje szablonów usługi Azure Resource Manager](../azure-resource-manager/resource-group-template-functions-resource.md#resourceid).
  > 
  > 
* Efekt `dependsOn` element jest, że usługi Azure Resource Manager może znać zasobów, do których można tworzyć równolegle i które zasoby muszą być tworzone po kolei. 

#### <a name="web-app"></a>Aplikacja internetowa
Teraz Przyjrzyjmy się rzeczywista aplikacji sieci web, które są bardziej skomplikowane. Kliknij aplikację sieci web [variables('apiSiteName')] w konspekt pliku JSON, aby wyróżnić jego kod JSON. Można zauważyć, że elementy stają się coraz bardziej interesujące. W tym celu I będzie porozmawiać na temat funkcji:

##### <a name="root-resource"></a>Zasób katalogu głównego
Aplikacja sieci web jest zależny od dwóch różnych zasobów. Oznacza to, usługi Azure Resource Manager utworzy aplikację sieci web tylko wtedy, gdy są tworzone zarówno plan usługi aplikacji, jak i wystąpienie programu SQL Server.

![](./media/app-service-deploy-complex-application-predictably/examinejson-5-webapproot.png)

##### <a name="app-settings"></a>Ustawienia aplikacji
Ustawienia aplikacji są także definiowane jako zagnieżdżonych zasobów.

![](./media/app-service-deploy-complex-application-predictably/examinejson-6-webappsettings.png)

W `properties` elementu `config/appsettings`, mają dwa ustawienia aplikacji w formacie `“<name>” : “<value>”`.

* `PROJECT`jest [ustawienie KUDU](https://github.com/projectkudu/kudu/wiki/Customizing-deployments) , który zawiadamia wdrożenia usługi Azure projektu do użycia w rozwiązaniu Visual Studio wielu projektów. I wyświetli później, jak jest skonfigurowany do kontroli źródła, ale ponieważ jest kod ToDoApp w rozwiązaniu Visual Studio wielu projektów, potrzebujemy to ustawienie.
* `clientUrl`jest używany po prostu aplikacji ustawienie kodu aplikacji.

##### <a name="connection-strings"></a>Parametry połączeń
Parametry połączenia są także definiowane jako zagnieżdżonych zasobów.

![](./media/app-service-deploy-complex-application-predictably/examinejson-7-webappconnstr.png)

W `properties` elementu `config/connectionstrings`, każdy ciąg połączenia jest również zdefiniowany jako pary nazwa: wartość w określonym formacie `“<name>” : {“value”: “…”, “type”: “…”}`. Aby uzyskać `type` elementu, możliwe wartości to `MySql`, `SQLServer`, `SQLAzure`, i `Custom`.

> [!TIP]
> Ostateczne listę typów ciąg połączenia, uruchom następujące polecenie w programie Azure PowerShell: \[Enum]::GetNames("Microsoft.WindowsAzure.Commands.Utilities.Websites.Services.WebEntities.DatabaseType")
> 
> 

##### <a name="source-control"></a>Kontrola źródła
Ustawienia kontroli źródła są także definiowane jako zagnieżdżonych zasobów. Usługa Azure Resource Manager używa tego zasobu można skonfigurować publikowanie ciągłego (zobacz zastrzeżenie: na `IsManualIntegration` później), a także, aby rozpocząć poza wdrażania kodu aplikacji automatycznie podczas przetwarzania pliku JSON.

![](./media/app-service-deploy-complex-application-predictably/examinejson-8-webappsourcecontrol.png)

`RepoUrl`i `branch` powinny być bardzo intuicyjne i powinny wskazywać repozytorium Git i nazwę gałęzi do opublikowania z. Ponownie są one zdefiniowane przez parametry wejściowe. 

Należy zwrócić uwagę w `dependsOn` element, który oprócz zasobów aplikacji sieci web, `sourcecontrols/web` zależy również od `config/appsettings` i `config/connectionstrings`. Jest to spowodowane po `sourcecontrols/web` jest skonfigurowany, proces wdrożenia usługi Azure automatycznie podejmie próbę wdrożenia, skompilować i uruchomić kod aplikacji. W związku z tym Wstawianie tę zależność pomaga upewnić się, że aplikacja ma dostęp do ustawień wymaganych aplikacji i parametry połączenia przed uruchomieniem kodu aplikacji. 

> [!NOTE]
> Należy zauważyć, że `IsManualIntegration` ma ustawioną wartość `true`. Ta właściwość jest niezbędne w tym samouczku, ponieważ nie ma faktycznego repozytorium GitHub i w związku z tym nie może faktycznie Przyznaj uprawnienie do platformy Azure, aby skonfigurować publikowanie ciągłego z [ToDoApp](https://github.com/azure-appservice-samples/ToDoApp) (tj. wypychania automatyczne repozytorium aktualizacje na platformie Azure). Można użyć wartości domyślnej `false` dla określonego repozytorium tylko wtedy, gdy skonfigurowano właściciela GitHub poświadczeń w [portalu Azure](https://portal.azure.com/) przed. Innymi słowy Jeśli zdefiniowano kontroli źródła do usługi GitHub lub BitBucket dla wszystkich aplikacji w [Azure Portal](https://portal.azure.com/) wcześniej przy użyciu użytkownika poświadczeń, Azure będą Pamiętaj poświadczenia i ich używać w przyszłości wdrożyć dowolną aplikację z usługi GitHub lub BitBucket. Jednak jeśli użytkownik nie zostało to jeszcze zrobione, wdrożenia szablonu JSON zakończy się niepowodzeniem, gdy usługi Azure Resource Manager spróbuje skonfigurować ustawienia kontroli źródła dla aplikacji sieci web, ponieważ nie można zalogować do usługi GitHub lub BitBucket, z poświadczeniami właściciela repozytorium.
> 
> 

## <a name="compare-the-json-template-with-deployed-resource-group"></a>Porównanie szablonu JSON z grupą wdrożonych zasobów
W tym miejscu, można przejść przez bloki wszystkich aplikacji sieci web w [Azure Portal](https://portal.azure.com/), ale innego narzędzia, która po prostu jako przydatne, jeśli nie jest więcej. Przejdź do [Eksploratora zasobów Azure](https://resources.azure.com) Podgląd narzędzia, które zapewnia JSON reprezentację wszystkich grup zasobów w subskrypcji, ponieważ faktycznie istnieją one w wewnętrznej bazy danych Azure. Można również sprawdzić, jak odpowiada hierarchii JSON grupy zasobów na platformie Azure w hierarchii w pliku szablonu, który jest używany do jej utworzenia.

Na przykład po przejściu do [Eksploratora zasobów Azure](https://resources.azure.com) narzędzia i rozwiń węzły w Eksploratorze, widać, grupy zasobów i zasoby poziomu głównego, które są zbierane w ich typów odpowiednich zasobów.

![](./media/app-service-deploy-complex-application-predictably/ARM-1-treeview.png)

W przypadku przechodzenia do aplikacji sieci web, można wyświetlić szczegóły konfiguracji aplikacji sieci web podobne do poniższych zrzut ekranu:

![](./media/app-service-deploy-complex-application-predictably/ARM-2-jsonview.png)

Ponownie zagnieżdżonych zasobów należy skonfigurować hierarchię bardzo podobne do pliku szablonu JSON i powinna zostać wyświetlona ustawienia aplikacji, parametry połączenia, itp., prawidłowo odzwierciedlane w okienku JSON. Brak tutaj ustawień może oznaczać problem z plikiem JSON i ułatwiają rozwiązywanie problemów z pliku szablonu JSON.

## <a name="deploy-the-resource-group-template-yourself"></a>Wdrażanie szablonu grupy zasobów, samodzielnie
**Wdrażanie na platformie Azure** przycisk stanowi doskonałe rozwiązanie, ale umożliwia wdrażanie szablonu grupy zasobów w azuredeploy.json tylko wtedy, gdy azuredeploy.json ma już przypisany do usługi GitHub. Zestaw .NET SDK usługi Azure udostępnia narzędzia do wdrożenia dowolnego pliku szablonu JSON bezpośrednio z komputera lokalnego. Aby to zrobić, wykonaj następujące czynności:

1. W programie Visual Studio, kliknij przycisk **pliku** > **nowy** > **projektu**.
2. Kliknij przycisk **Visual C#** > **chmury** > **grupy zasobów platformy Azure**, następnie kliknij przycisk **OK**.
   
   ![](./media/app-service-deploy-complex-application-predictably/deploy-1-vsproject.png)
3. W **wybierz szablon Azure**, wybierz pozycję **pusty szablon** i kliknij przycisk **OK**.
4. Przeciągnij azuredeploy.json w **szablonu** folderu nowego projektu.
   
   ![](./media/app-service-deploy-complex-application-predictably/deploy-2-copyjson.png)
5. W Eksploratorze rozwiązań Otwórz azuredeploy.json skopiowane.
6. Tylko do celów demonstracyjnych, możemy dodać niektóre standardowe zasoby szczegółowe informacje o aplikacji do naszej pliku JSON, klikając **dodawania zasobów**. Jeśli interesuje Cię tylko wdrażanie pliku JSON, przejdź do procedury wdrażania.
   
   ![](./media/app-service-deploy-complex-application-predictably/deploy-3-newresource.png)
7. Wybierz **usługi Application Insights dla aplikacji sieci Web**, następnie upewnij się, istniejącą aplikację sieci web i plan usługi aplikacji jest zaznaczone, a następnie kliknij przycisk **Dodaj**.
   
   ![](./media/app-service-deploy-complex-application-predictably/deploy-4-newappinsight.png)
   
   Teraz będzie można wyświetlić kilka nowych zasobów, który, zależnie od zasobu oraz jakie operacje, są zależne od planu usługi aplikacji lub aplikacji sieci web. Te zasoby nie są włączone przez ich istniejącej definicji, a użytkownik chce zmienić.
   
   ![](./media/app-service-deploy-complex-application-predictably/deploy-5-appinsightresources.png)
8. W konspekt pliku JSON, kliknij przycisk **appInsights skalowania automatycznego** aby wyróżnić jego kod JSON. Jest to ustawienie skalowania dla planu usługi aplikacji.
9. Wyróżniony kod JSON, odszukaj `location` i `enabled` właściwości i ustaw je, jak pokazano poniżej.
   
   ![](./media/app-service-deploy-complex-application-predictably/deploy-6-autoscalesettings.png)
10. W konspekt pliku JSON, kliknij przycisk **CPUHigh appInsights** aby wyróżnić jego kod JSON. To jest alert.
11. Zlokalizuj `location` i `isEnabled` właściwości i ustaw je, jak pokazano poniżej. Wykonaj te same innych alertów trzy (purpurowy bulw).
    
    ![](./media/app-service-deploy-complex-application-predictably/deploy-7-alerts.png)
12. Możesz teraz gotowe do wdrożenia. Kliknij prawym przyciskiem myszy projekt i wybierz **Wdróż** > **nowe wdrożenie**.
    
    ![](./media/app-service-deploy-complex-application-predictably/deploy-8-newdeployment.png)
13. Zaloguj się do konta platformy Azure, jeśli jeszcze tego nie zrobiono.
14. Wybierz istniejącą grupę zasobów w ramach subskrypcji lub Utwórz nowe wybierz jedną, **azuredeploy.json**, a następnie kliknij przycisk **Edytuj parametry**.
    
    ![](./media/app-service-deploy-complex-application-predictably/deploy-9-deployconfig.png)
    
    Można teraz edytować wszystkich parametrów zdefiniowanych w pliku szablonu nieuprzywilejowany tabeli. Parametry, które definiują ustawienia domyślne mają już wartości domyślne, a parametry określające listę dozwolonych wartości będą wyświetlane jako listę rozwijaną.
    
    ![](./media/app-service-deploy-complex-application-predictably/deploy-10-parametereditor.png)
15. Wypełnij puste parametry, a następnie użyj [adres repozytorium GitHub ToDoApp](https://github.com/azure-appservice-samples/ToDoApp.git) w **repoUrl**. Następnie kliknij przycisk **zapisać**.
    
    ![](./media/app-service-deploy-complex-application-predictably/deploy-11-parametereditorfilled.png)
    
    > [!NOTE]
    > Funkcję skalowania automatycznego jest funkcją oferowanych w **standardowe** warstwy lub nowszej i poziomu planu alerty są oferowane w funkcji **podstawowe** warstwę lub nowszym, musisz ustawić **jednostki sku** parametr **standardowe** lub **Premium** aby zobaczyć wszystkie nowe usługi App Insights zasobami światła w górę.
    > 
    > 
16. Kliknij przycisk **wdrażanie**. W przypadku wybrania **zapisywanie haseł**, hasło zostanie zapisane w pliku parametrów **w postaci zwykłego tekstu**. W przeciwnym razie zostanie wyświetlony monit wprowadzanie hasła bazy danych podczas procesu wdrażania.

Gotowe. Teraz wystarczy przejść do [Azure Portal](https://portal.azure.com/) i [Eksploratora zasobów Azure](https://resources.azure.com) narzędzia, aby zobaczyć nowe alerty i ustawienia skalowania automatycznego dodany do Twojego JSON wdrożonych aplikacji.

Wszystkie czynności w tej sekcji głównie wykonać następujące czynności:

1. Przygotowane pliku szablonu
2. Utworzony plik parametru z pliku szablonu
3. Wdrożony plik szablonu z pliku parametrów

Ostatnim krokiem łatwo odbywa się przy użyciu polecenia cmdlet programu PowerShell. Aby zobaczyć, co program Visual Studio została po jej wdrożeniu aplikacji, otwórz Scripts\Deploy AzureResourceGroup.ps1. Wiele kodu istnieje, ale użyjemy po prostu zaznacz odpowiednie kodu, należy wdrożyć plik szablonu z pliku parametrów.

![](./media/app-service-deploy-complex-application-predictably/deploy-12-powershellsnippet.png)

Polecenia cmdlet ostatniego `New-AzureResourceGroup`, który faktycznie wykonuje akcję. Powinno to pokazują użytkownikowi, że za pomocą narzędzi, jest stosunkowo prosta do wdrożenia aplikacji w chmurze przewidywalnego. Przy każdym uruchomieniu polecenia cmdlet na samym szablonie z tego samego pliku parametrów użytkownik chce uzyskać ten sam rezultat.

## <a name="summary"></a>Podsumowanie
W DevOps powtarzalność i przewidywalności są klucze do dowolnego pomyślne wdrożenie aplikacji wysokiej skali składa się z mikrousług. W tym samouczku wdrożono aplikację mikrousługi dwa na platformie Azure jako pojedyncza grupa zasobów przy użyciu szablonu usługi Azure Resource Manager. Mamy nadzieję, że jej przyznał Ci potrzebne, aby można było uruchomić konwertowania aplikacji na platformie Azure do szablonu i można udostępnić i wdróż je przewidywalnego wiedzy. 

<a name="resources"></a>

## <a name="more-resources"></a>Więcej zasobów
* [Język szablonu usługi Azure Resource Manager](../azure-resource-manager/resource-group-authoring-templates.md)
* [Tworzenie szablonów usługi Azure Resource Manager](../azure-resource-manager/resource-group-authoring-templates.md)
* [Azure Functions szablonu usługi Resource Manager](../azure-resource-manager/resource-group-template-functions.md)
* [Wdrażanie aplikacji przy użyciu szablonu Azure Resource Manager](../azure-resource-manager/resource-group-template-deploy.md)
* [Używanie programu Azure PowerShell z usługą Azure Resource Manager](../azure-resource-manager/powershell-azure-resource-manager.md)
* [Rozwiązywanie problemów z wdrożeniami grup zasobów na platformie Azure](../azure-resource-manager/resource-manager-common-deployment-errors.md)

