---
title: Tworzenie i wdrażanie projekty grup zasobów w programie Visual Studio na platformie Azure
description: Tworzenie projektu grupy zasobów platformy Azure przy użyciu programu Visual Studio i wdrażanie zasobów na platformie Azure.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: quickstart
ms.date: 06/20/2019
ms.author: tomfitz
ms.openlocfilehash: 8677d906375853bdde5c192c86dacc7479f2e31e
ms.sourcegitcommit: 5cb0b6645bd5dff9c1a4324793df3fdd776225e4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/21/2019
ms.locfileid: "67311196"
---
# <a name="creating-and-deploying-azure-resource-groups-through-visual-studio"></a>Tworzenie i wdrażanie grup zasobów platformy Azure za pomocą programu Visual Studio

Program Visual Studio umożliwia utworzenie projektu służącego do wdrażania infrastruktury i kodu na platformie Azure. Na przykład można wdrożyć hosta sieci web, witryny sieci web i kodu dla witryny sieci web. Program Visual Studio zapewnia wiele różnych szablonów początkowych do wdrażania typowych scenariuszy. W tym artykule wdrożysz aplikację sieci web.  

W tym artykule pokazano, jak [programu Visual Studio 2019 lub nowszym z programowanie na platformie Azure i zainstalowanych obciążeń programu ASP.NET](/visualstudio/install/install-visual-studio?view=vs-2019). Jeśli używasz programu Visual Studio 2017, czynności są w większości takie same.

## <a name="create-azure-resource-group-project"></a>Tworzenie projektu grupy zasobów platformy Azure

W tej sekcji utworzysz projekt grupy zasobów platformy Azure z **aplikacji sieci Web** szablonu.

1. W programie Visual Studio, wybierz **pliku**, **New**, i **projektu**. Wybierz **grupy zasobów platformy Azure** szablonu projektu i **dalej**.

    ![Tworzenie projektu](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/create-project.png)

1. Nadaj projektowi nazwę. Inne domyślne ustawienia prawdopodobnie jest poprawna, ale umożliwia podejmowanie Przejrzyj działają w danym środowisku. Po zakończeniu wybierz pozycję **Utwórz**.

    ![Tworzenie projektu](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/name-project.png)

1. Wybierz szablon, który ma zostać wdrożony w usłudze Azure Resource Manager. Należy zauważyć, że dostępnych jest wiele różnych opcji, zależnie od typu projektu, który chcesz wdrożyć. W tym artykule, należy wybrać **aplikacji sieci Web** szablonu i **OK**.

    ![Wybieranie szablonu](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/select-project.png)

    Wybrany szablon jest tylko punktem wyjściowym. Możesz dodać lub usunąć zasoby, aby wykonać dany scenariusz.

1. Program Visual Studio tworzy projekt wdrożenia grupy zasobów, aplikacji sieci web. Aby wyświetlić pliki w projekcie, sprawdź węzeł w projekcie wdrożenia.

    ![Pokaż węzły](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/show-items.png)

    Ponieważ wybrano szablon aplikacja sieci Web, zostaną wyświetlone następujące pliki:

   | Nazwa pliku | Opis |
   | --- | --- |
   | Deploy-AzureResourceGroup.ps1 |Skrypt programu PowerShell, który uruchamia polecenia w celu wdrożenia do usługi Azure Resource Manager. Visual Studio używa tego skryptu programu PowerShell do wdrażania szablonu. |
   | WebSite.json |Szablon usługi Resource Manager określający infrastrukturę, którą chcesz wdrożyć na platformie Azure, oraz parametry, które można podać podczas wdrażania. Umożliwia on również definiowanie zależności między zasobami, tak aby usługa Resource Manager wdrożyła je w odpowiedniej kolejności. |
   | WebSite.parameters.json |Plik parametrów zawierający wartości wymagane przez szablon. Poszczególne wdrożenia są dostosowywane za pomocą przekazywanych wartości parametrów. |

    Wszystkie projekty wdrażania grup zasobów zawierają te podstawowe pliki. Inne projekty mogą zawierać dodatkowe pliki do obsługi innych funkcji.

## <a name="customize-resource-manager-template"></a>Dostosowywanie szablonu usługi Resource Manager

Możesz dostosować projekt wdrożenia, modyfikując szablon usługi Resource Manager, który opisuje zasoby, które mają zostać wdrożone. Aby uzyskać informacje na temat elementów szablonu usługi Resource Manager, zobacz [Authoring Azure Resource Manager templates (Tworzenie szablonów usługi Azure Resource Manager)](resource-group-authoring-templates.md).

1. Aby pracować nad szablonem, otwórz **WebSite.json**.

1. Edytor programu Visual Studio udostępnia narzędzia pomocne podczas edytowania szablonu usługi Resource Manager. Okno **Konspekt pliku JSON** ułatwia wyświetlanie elementów zdefiniowanych w szablonie.

   ![Wyświetlanie konspektu pliku JSON](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/show-json-outline.png)

1. Wybierz element w konspekcie, aby przejść do części szablonu.

   ![Przechodzenie do elementów JSON](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/navigate-json.png)

1. Nowy zasób można dodać, wybierając przycisk **Dodaj zasób** u góry okna Konspekt pliku JSON lub klikając prawym przyciskiem myszy węzeł **zasoby** i wybierając polecenie **Dodaj nowy zasób**.

   ![Dodaj zasób](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/add-resource.png)

1. Wybierz **konta magazynu** i nadaj mu nazwę. Podaj nazwę nie dłuższą niż 11 znaków, zawierającą tylko cyfry i małe litery.

   ![Dodawanie magazynu](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/add-storage.png)

1. Należy zauważyć, że dodany został nie tylko zasób, ale również parametr typu konta magazynu oraz zmienna nazwy konta magazynu.

   ![Wyświetlanie konspektu](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/show-new-items.png)

1. Parametr typu konta magazynu jest wstępnie zdefiniowany wraz z dozwolonymi typami i typem domyślnym. Możesz pozostawić te wartości bez zmian lub edytować je dla danego scenariusza. Jeśli nie chcesz, aby inne osoby wdrażały konta magazynu **Premium_LRS** przy użyciu tego szablonu, usuń go z dozwolonych typów.

   ```json
   "demoaccountType": {
     "type": "string",
     "defaultValue": "Standard_LRS",
     "allowedValues": [
       "Standard_LRS",
       "Standard_ZRS",
       "Standard_GRS",
       "Standard_RAGRS"
     ]
   }
   ```

1. Program Visual Studio oferuje również funkcję IntelliSense ułatwiającą zrozumienie dostępnych właściwości podczas edytowania szablonu. Aby na przykład edytować właściwości planu usługi App Service, przejdź do zasobu **HostingPlan** i dodaj wartość dla obiektu **properties**. Należy zauważyć, że funkcja IntelliSense wyświetla dostępne wartości oraz ich opisy.

   ![wyświetlanie funkcji intellisense](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/show-intellisense.png)

   Możesz ustawić **numberOfWorkers** 1 i Zapisz plik.

   ```json
   "properties": {
     "name": "[parameters('hostingPlanName')]",
     "numberOfWorkers": 1
   }
   ```

1. Otwórz **WebSite.parameters.json** pliku. Użyjesz pliku parametrów do przekazywania wartości podczas wdrażania, umożliwiające dostosowanie zasobów wdrażane. Nadaj nazwę planu hostingu, a następnie zapisz plik.

   ```json
   {
     "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
     "contentVersion": "1.0.0.0",
     "parameters": {
       "hostingPlanName": {
         "value": "demoHostPlan"
       }
     }
   }
   ```

## <a name="deploy-project-to-azure"></a>Wdrażanie projektu na platformie Azure

Teraz możesz przystąpić do wdrażania projektu w grupie zasobów.

Domyślnie skrypt programu PowerShell (wdrażanie AzureResourceGroup.ps1) w projekcie używa modułu AzureRM. Jeśli nadal masz zainstalowany moduł AzureRM i chcesz nadal jej używać, możesz użyć tego skryptu domyślne. Za pomocą tego skryptu można użyć interfejsu programu Visual Studio do wdrożenia rozwiązania.

Jednakże jeśli zostały zmigrowane do nowego [modułu Az](/powershell/azure/new-azureps-module-az), należy dodać nowy skrypt do projektu. Aby dodać skrypt, który używa modułu Az, skopiuj [AzTemplate.ps1 Wdróż](https://github.com/Azure/azure-quickstart-templates/blob/master/Deploy-AzTemplate.ps1) skryptu i dodaj go do projektu. Aby użyć tego skryptu do wdrożenia, należy uruchomić go z konsoli programu PowerShell, a nie przy użyciu interfejsu wdrożenia programu Visual Studio.

Oba podejścia są wyświetlane w tym artykule. Ten artykuł dotyczy domyślny skrypt jako skrypt moduł AzureRM i nowy skrypt jako skrypt Az modułu.

### <a name="az-module-script"></a>Skrypt module az

Az skryptu modułu Otwórz konsolę programu PowerShell i uruchom:

```powershell
.\Deploy-AzTemplate.ps1 -ArtifactStagingDirectory . -Location centralus -TemplateFile WebSite.json -TemplateParametersFile WebSite.parameters.json
```

### <a name="azurerm-module-script"></a>Skrypt moduł AzureRM

Aby skrypt moduł AzureRM należy użyć programu Visual Studio:

1. W menu skrótów węzła projektu wdrażania wybierz pozycję **Wdróż** > **Nowe**.

    ![Nowy element menu wdrożenia](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/deploy.png)

1. Zostanie wyświetlone okno dialogowe **Wdrażanie w grupie zasobów**. W polu listy rozwijanej **Grupa zasobów** wybierz istniejącą grupę zasobów lub utwórz nową. Wybierz pozycję **Wdróż**.

    ![Wdrażanie do grupy zasobów, okno dialogowe](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/show-deployment.png)

1. W oknach **Dane wyjściowe** jest wyświetlany stan wdrożenia. Po zakończeniu wdrażania ostatni komunikat wskazuje pomyślne wdrożenie i jest podobny do następującego:

   ```output
   18:00:58 - Successfully deployed template 'website.json' to resource group 'ExampleAppDeploy'.
   ```

## <a name="view-deployed-resources"></a>Wyświetl wdrożone zasoby

Sprawdźmy wyniki.

1. W przeglądarce otwórz witrynę [Azure Portal](https://portal.azure.com/) i zaloguj się do swojego konta. Aby wyświetlić grupę zasobów wybierz pozycję **Grupy zasobów**, a następnie wybierz grupę, do której wykonano wdrożenie.

1. Zostaną wyświetlone wszystkie wdrożone zasoby. Należy zauważyć, że nazwa konta magazynu nie jest identyczna z określoną podczas dodawania tego zasobu. Konto magazynu musi być unikatowe. Szablon automatycznie dodaje ciąg znaków do nazwy podanej utworzenia unikatowej nazwy.

    ![Wyświetlanie zasobów](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/show-deployed-resources.png)

## <a name="add-code-to-project"></a>Dodaj kod do projektu

Wdrożono już infrastrukturę aplikacji, ale z projektem nie został wdrożony rzeczywisty kod.

1. Dodaj projekt do rozwiązania programu Visual Studio. Kliknij prawym przyciskiem myszy rozwiązanie, a następnie wybierz pozycje **Dodaj** > **Nowy projekt**.

    ![Dodaj projekt](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/add-project.png)

1. Dodaj **aplikacji sieci Web platformy ASP.NET Core**.

    ![Dodawanie aplikacji sieci web](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/add-app.png)

1. Nadaj nazwę aplikacji sieci web, a następnie wybierz pozycję **Utwórz**.

    ![Nazwa aplikacji sieci web](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/name-web-app.png)

1. Wybierz **aplikacji sieci Web** i **tworzenie**.

    ![Wybierz aplikację sieci Web](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/select-project-type.png)

1. Po utworzeniu aplikacji internetowej przez program Visual Studio oba projekty są wyświetlane w rozwiązaniu.

    ![wyświetlanie projektów](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/show-projects.png)

1. Musisz teraz upewnić się, że projekt grupy zasobów wie o nowym projekcie. Wróć do projektu grupy zasobów (ExampleAppDeploy). Kliknij prawym przyciskiem myszy pozycję **Odwołania** i wybierz polecenie **Dodaj odwołanie**.

    ![Dodawanie odwołania](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/add-new-reference.png)

1. Wybierz projekt aplikacji internetowej, który został utworzony.

   ![Dodawanie odwołania](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/add-reference.png)

   Przez dodanie odwołania powoduje link projektu aplikacji sieci web do projektu grupy zasobów i automatycznie ustawia niektóre właściwości. Te właściwości będą wyświetlane w oknie **Właściwości** odwołania. Właściwość **Include File Path** zawiera ścieżkę, w której zostanie utworzony pakiet. Zanotuj folder (ExampleApp) i plik (package.zip). Musisz znać te wartości, ponieważ są one podawane jako parametry podczas wdrażania aplikacji.

   ![Zobacz odwołania](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/see-reference.png)

1. Wróć do szablonu (WebSite.json) i Dodaj zasób do szablonu.

    ![Dodaj zasób](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/add-resource-2.png)

1. Tym razem wybierz pozycję **Web Deploy dla usługi Web Apps**. 

    ![Dodawanie sieci web wdrażanie](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/add-web-deploy.png)

   Zapisz szablon.

1. Istnieje kilka nowych parametrów w szablonie. Zostały one dodane w poprzednim kroku. Nie musisz podawać wartości **_artifactsLocation** lub **_artifactsLocationSasToken** ponieważ te wartości są generowane automatycznie. Można jednak ustawić folder i nazwę pliku na ścieżkę zawierającą pakiet wdrożeniowy. Nazwy tych parametrów kończyć się znakiem **PackageFolder** i **PackageFileName**. Pierwszą część nazwy jest nazwą zasobu narzędzia Web Deploy, którą dodałeś. W tym artykule jest o nazwie **ExampleAppPackageFolder** i **ExampleAppPackageFileName**. 

   Otwórz **Website.parameters.json** i ustaw wartości zanotowane we właściwościach odwołanie do tych parametrów. Ustaw **ExampleAppPackageFolder** do nazwy folderu. Ustaw **ExampleAppPackageFileName** do nazwy pliku zip.

   ```json
   {
     "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
     "contentVersion": "1.0.0.0",
     "parameters": {
       "hostingPlanName": {
         "value": "demoHostPlan"
       },
       "ExampleAppPackageFolder": {
         "value": "ExampleApp"
       },
       "ExampleAppPackageFileName": {
         "value": "package.zip"
       }
     }
   }
   ```

## <a name="deploy-code-with-infrastructure"></a>Wdrażanie kodu za pomocą infrastruktury

Ponieważ kod jest dodawany do projektu, wdrożenie jest nieco inny tym razem. Podczas wdrażania Przygotuj się artefaktów projektu do miejsca, w których usługi Resource Manager może uzyskać dostęp. Artefakty są umieszczane na koncie magazynu.

### <a name="az-module-script"></a>Skrypt module az

Brak jednego niewielkie zmiany, które należy wprowadzić do szablonu, jeśli używasz skryptu modułu Az. Ten skrypt dodaje ukośnika lokalizacji artefakty, ale szablonu nie oczekuje tego ukośnika. Otwórz WebSite.json i znaleźć właściwości rozszerzenia MSDeploy. Ma właściwość o nazwie **packageUri**. Usuń ukośnik między lokalizacją artefaktów i folder pakietu.

Powinien on wyglądać podobnie:

```json
"packageUri": "[concat(parameters('_artifactsLocation'), parameters('ExampleAppPackageFolder'), '/', parameters('ExampleAppPackageFileName'), parameters('_artifactsLocationSasToken'))]",
```

Powiadomienie w poprzednim przykładzie, jest nie `'/',` między **parameters('_artifactsLocation')** i **parameters('ExampleAppPackageFolder')** .

Skompiluj ponownie projekt. Tworzenie projektu zapewnia, że pliki, które należy wdrożyć są dodawane do folderu przemieszczania.

Teraz Otwórz konsolę programu PowerShell i uruchom:

```powershell
.\Deploy-AzTemplate.ps1 -ArtifactStagingDirectory .\bin\Debug\staging\ExampleAppDeploy -Location centralus -TemplateFile WebSite.json -TemplateParametersFile WebSite.parameters.json -UploadArtifacts -StorageAccountName <storage-account-name>
```

### <a name="azurerm-module-script"></a>Skrypt moduł AzureRM

Aby skrypt moduł AzureRM należy użyć programu Visual Studio:

1. Aby przeprowadzić ponowne wdrożenie, wybierz opcję **Wdróż**i grupy zasobów została wcześniej wdrożona.

    ![Ponowne wdrażanie projektu](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/redeploy.png)

1. Wybierz konto magazynu, wdrożone z tą grupą zasobów dla **konto magazynu artefaktów**.

   ![Ponowne wdrażanie sieci web wdrażanie](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/redeploy-web-app.png)

## <a name="view-web-app"></a>Wyświetl aplikację sieci web

1. Po zakończeniu wdrażania wybierz aplikację internetową w witrynie portalu. Wybierz adres URL, aby przejść do lokacji.

   ![Przeglądaj witrynę](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/browse-site.png)

1. Zobaczysz, że domyślna aplikacja ASP.NET została pomyślnie wdrożona.

   ![Wyświetlanie wdrożonej aplikacji](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/show-deployed-app.png)

## <a name="add-operations-dashboard"></a>Dodaj pulpit nawigacyjny operacji

Możesz korzystać nie tylko z zasobów dostępnych z poziomu interfejsu programu Visual Studio. Można dostosować wdrożenie, dodając do szablonu zasoby niestandardowe. Aby zobaczyć, jak działa dodawanie zasobu, dodasz operacyjny pulpit nawigacyjny w celu zarządzania wdrożonymi zasobami.

1. Otwórz plik WebSite.json i Dodaj następujący kod JSON, po zasób konta magazynu, ale przed zamykającym `]` sekcji zasobów.

   ```json
    ,{
      "properties": {
        "lenses": {
          "0": {
            "order": 0,
            "parts": {
              "0": {
                "position": {
                  "x": 0,
                  "y": 0,
                  "colSpan": 4,
                  "rowSpan": 6
                },
                "metadata": {
                  "inputs": [
                    {
                      "name": "resourceGroup",
                      "isOptional": true
                    },
                    {
                      "name": "id",
                      "value": "[resourceGroup().id]",
                      "isOptional": true
                    }
                  ],
                  "type": "Extension/HubsExtension/PartType/ResourceGroupMapPinnedPart"
                }
              },
              "1": {
                "position": {
                  "x": 4,
                  "y": 0,
                  "rowSpan": 3,
                  "colSpan": 4
                },
                "metadata": {
                  "inputs": [],
                  "type": "Extension[azure]/HubsExtension/PartType/MarkdownPart",
                  "settings": {
                    "content": {
                      "settings": {
                        "content": "__Customizations__\n\nUse this dashboard to create and share the operational views of services critical to the application performing. To customize simply pin components to the dashboard and then publish when you're done. Others will see your changes when you publish and share the dashboard.\n\nYou can customize this text too. It supports plain text, __Markdown__, and even limited HTML like images <img width='10' src='https://portal.azure.com/favicon.ico'/> and <a href='https://azure.microsoft.com' target='_blank'>links</a> that open in a new tab.\n",
                        "title": "Operations",
                        "subtitle": "[resourceGroup().name]"
                      }
                    }
                  }
                }
              }
            }
          }
        },
        "metadata": {
          "model": {
            "timeRange": {
              "value": {
                "relative": {
                  "duration": 24,
                  "timeUnit": 1
                }
              },
              "type": "MsPortalFx.Composition.Configuration.ValueTypes.TimeRange"
            }
          }
        }
      },
      "apiVersion": "2015-08-01-preview",
      "name": "[concat('ARM-',resourceGroup().name)]",
      "type": "Microsoft.Portal/dashboards",
      "location": "[resourceGroup().location]",
      "tags": {
        "hidden-title": "[concat('OPS-',resourceGroup().name)]"
      }
    }
   ```

1. Ponownie Wdróż projekt.

1. Po zakończeniu wdrażania widok pulpitu nawigacyjnego w portalu. Wybierz **pulpit nawigacyjny** i wybierz ten, który został wdrożony.

   ![Niestandardowy pulpit nawigacyjny](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/view-custom-dashboards.png)

1. Zostanie wyświetlony dostosowanego pulpitu nawigacyjnego.

   ![Niestandardowy pulpit nawigacyjny](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/Ops-DemoSiteGroup-dashboard.png)

Możesz zarządzać dostępem do pulpitu nawigacyjnego, korzystając z grup kontroli dostępu opartych na rolach. Po wdrożeniu pulpitu nawigacyjnego możesz też dostosowywać jego wygląd. Jeśli jednak ponownie wdrożysz grupę zasobów, pulpit nawigacyjny zostanie zresetowany do domyślnego stanu określonego w szablonie. Aby uzyskać więcej informacji na temat tworzenia pulpitów nawigacyjnych, zobacz [Programmatically create Azure Dashboards (Programowe tworzenie pulpitów nawigacyjnych platformy Azure)](../azure-portal/azure-portal-dashboards-create-programmatically.md).

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Gdy zasoby platformy Azure nie będą już potrzebne, wyczyść wdrożone zasoby, usuwając grupę zasobów.

1. W witrynie Azure portal, wybierz **grup zasobów** menu po lewej stronie.

1. Wybierz nazwę grupy zasobów.

1. Wybierz pozycję **Usuń grupę zasobów** z górnego menu.

## <a name="next-steps"></a>Kolejne kroki

W tym przewodniku Szybki start dowiesz się, jak tworzyć i wdrażać szablony przy użyciu programu Visual Studio. W kolejnym samouczku dowiesz się, jak znaleźć informacje w dokumentacji szablonów, aby utworzyć zaszyfrowane konto usługi Azure Storage.

> [!div class="nextstepaction"]
> [Tworzenie zaszyfrowanego konta usługi Storage](./resource-manager-tutorial-create-encrypted-storage-accounts.md)
