---
title: Tworzenie & wdrażanie projektów grup zasobów programu Visual Studio
description: Tworzenie projektu grupy zasobów platformy Azure przy użyciu programu Visual Studio i wdrażanie zasobów na platformie Azure.
ms.topic: conceptual
ms.date: 10/16/2019
ms.openlocfilehash: 5127732ac0c33d4b27f70bd616fb23aaec5c871f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76152735"
---
# <a name="creating-and-deploying-azure-resource-groups-through-visual-studio"></a>Tworzenie i wdrażanie grup zasobów platformy Azure za pomocą programu Visual Studio

Program Visual Studio umożliwia utworzenie projektu służącego do wdrażania infrastruktury i kodu na platformie Azure. Na przykład można wdrożyć hosta sieci web, witryny sieci Web i kodu dla witryny sieci Web. Program Visual Studio zapewnia wiele różnych szablonów początkowych do wdrażania typowych scenariuszy. W tym artykule można wdrożyć aplikację sieci web.

W tym artykule pokazano, jak używać [programu Visual Studio 2019 lub nowszego z zainstalowanymi obciążeniami deweloperów platformy Azure i ASP.NET.](/visualstudio/install/install-visual-studio?view=vs-2019) Jeśli używasz programu Visual Studio 2017, środowisko jest w dużej mierze taka sama.

## <a name="create-azure-resource-group-project"></a>Tworzenie projektu grupy zasobów platformy Azure

W tej sekcji utworzysz projekt grupy zasobów platformy Azure z szablonem **aplikacji sieci Web.**

1. W programie Visual Studio wybierz pozycję **Plik**>**nowego**>**projektu**.
1. Wybierz szablon projektu **grupy zasobów platformy Azure** i **dalej**.

    ![Tworzenie projektu](./media/create-visual-studio-deployment-project/create-project.png)

1. Nadaj projektowi nazwę. Inne ustawienia domyślne są prawdopodobnie w porządku, ale przejrzyj je, aby działały dla Twojego środowiska. Po zakończeniu wybierz pozycję **Utwórz**.

    ![Tworzenie projektu](./media/create-visual-studio-deployment-project/name-project.png)

1. Wybierz szablon, który ma zostać wdrożony w usłudze Azure Resource Manager. Należy zauważyć, że dostępnych jest wiele różnych opcji, zależnie od typu projektu, który chcesz wdrożyć. W tym artykule wybierz szablon **aplikacji sieci Web** i przycisk **OK**.

    ![Wybieranie szablonu](./media/create-visual-studio-deployment-project/select-project.png)

    Wybrany szablon jest tylko punktem wyjściowym. Możesz dodać lub usunąć zasoby, aby wykonać dany scenariusz.

1. Program Visual Studio tworzy projekt wdrażania grupy zasobów dla aplikacji sieci web. Aby wyświetlić pliki dla projektu, spójrz na węzeł w projekcie wdrażania.

    ![Pokaż węzły](./media/create-visual-studio-deployment-project/show-items.png)

    Ponieważ wybrano szablon aplikacji sieci Web, są widoczne następujące pliki:

   | Nazwa pliku | Opis |
   | --- | --- |
   | Deploy-AzureResourceGroup.ps1 |Skrypt programu PowerShell, który uruchamia polecenia w celu wdrożenia do usługi Azure Resource Manager. Visual Studio używa tego skryptu programu PowerShell do wdrożenia szablonu. |
   | Witryna Internetowa.json |Szablon usługi Resource Manager określający infrastrukturę, którą chcesz wdrożyć na platformie Azure, oraz parametry, które można podać podczas wdrażania. Umożliwia on również definiowanie zależności między zasobami, tak aby usługa Resource Manager wdrożyła je w odpowiedniej kolejności. |
   | Witryna WebSite.parameters.json |Plik parametrów zawierający wartości wymagane przez szablon. Poszczególne wdrożenia są dostosowywane za pomocą przekazywanych wartości parametrów. |

    Wszystkie projekty wdrażania grup zasobów zawierają te podstawowe pliki. Inne projekty mogą zawierać dodatkowe pliki do obsługi innych funkcji.

## <a name="customize-resource-manager-template"></a>Dostosowywanie szablonu Menedżera zasobów

Projekt wdrażania można dostosować, modyfikując szablon Menedżera zasobów opisujący zasoby, które chcesz wdrożyć. Aby uzyskać informacje na temat elementów szablonu usługi Resource Manager, zobacz [Authoring Azure Resource Manager templates](template-syntax.md) (Tworzenie szablonów usługi Azure Resource Manager).

1. Aby pracować nad szablonem, otwórz witrynę **WebSite.json**.

1. Edytor programu Visual Studio udostępnia narzędzia pomocne podczas edytowania szablonu usługi Resource Manager. Okno **Konspekt pliku JSON** ułatwia wyświetlanie elementów zdefiniowanych w szablonie.

   ![Pokaż konspekt JSON](./media/create-visual-studio-deployment-project/show-json-outline.png)

1. Wybierz element w konspekcie, aby przejść do tej części szablonu.

   ![Nawigacja JSON](./media/create-visual-studio-deployment-project/navigate-json.png)

1. Nowy zasób można dodać, wybierając przycisk **Dodaj zasób** u góry okna Konspekt pliku JSON lub klikając prawym przyciskiem myszy węzeł **zasoby** i wybierając polecenie **Dodaj nowy zasób**.

   ![Dodawanie zasobu](./media/create-visual-studio-deployment-project/add-resource.png)

1. Wybierz **pozycję Konto magazynu** i nadaj mu nazwę. Podaj nazwę nie dłuższą niż 11 znaków, zawierającą tylko cyfry i małe litery.

   ![Dodawanie magazynu](./media/create-visual-studio-deployment-project/add-storage.png)

1. Należy zauważyć, że dodany został nie tylko zasób, ale również parametr typu konta magazynu oraz zmienna nazwy konta magazynu.

   ![Pokaż konspekt](./media/create-visual-studio-deployment-project/show-new-items.png)

1. Parametr dla typu konta magazynu jest wstępnie zdefiniowany z dozwolonymi typami i typem domyślnym. Możesz pozostawić te wartości bez zmian lub edytować je dla danego scenariusza. Jeśli nie chcesz, aby inne osoby wdrażały konta magazynu **Premium_LRS** przy użyciu tego szablonu, usuń go z dozwolonych typów.

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

   ![Pokaż intellisense](./media/create-visual-studio-deployment-project/show-intellisense.png)

   Można ustawić **numberOfWorkers** na 1 i zapisać plik.

   ```json
   "properties": {
     "name": "[parameters('hostingPlanName')]",
     "numberOfWorkers": 1
   }
   ```

1. Otwórz plik **WebSite.parameters.json.** Plik parametrów służy do przekazywania wartości podczas wdrażania, które dostosowują wdrażany zasób. Nadaj planowi hostingu nazwę i zapisz plik.

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

Domyślnie skrypt programu PowerShell (Deploy-AzureResourceGroup.ps1) w projekcie używa modułu AzureRM. Jeśli nadal masz zainstalowany moduł AzureRM i chcesz nadal go używać, możesz użyć tego skryptu domyślnego. Za pomocą tego skryptu można użyć interfejsu programu Visual Studio do wdrożenia rozwiązania.

Jeśli jednak został migrowany do nowego [modułu Az,](/powershell/azure/new-azureps-module-az)musisz dodać nowy skrypt do projektu. Aby dodać skrypt, który używa modułu Az, skopiuj skrypt [Deploy-AzTemplate.ps1](https://github.com/Azure/azure-quickstart-templates/blob/master/Deploy-AzTemplate.ps1) i dodaj go do projektu. Aby użyć tego skryptu do wdrożenia, należy uruchomić go z konsoli programu PowerShell, a nie przy użyciu interfejsu wdrażania programu Visual Studio.

Oba podejścia są pokazane w tym artykule. W tym artykule odnosi się do skryptu domyślnego jako skrypt modułu AzureRM i nowy skrypt jako skrypt modułu Az.

### <a name="az-module-script"></a>Skrypt modułu Az

W przypadku skryptu modułu Az otwórz konsolę programu PowerShell i uruchom:

```powershell
.\Deploy-AzTemplate.ps1 -ArtifactStagingDirectory . -Location centralus -TemplateFile WebSite.json -TemplateParametersFile WebSite.parameters.json
```

### <a name="azurerm-module-script"></a>Skrypt modułu AzureRM

W przypadku skryptu modułu AzureRM użyj programu Visual Studio:

1. W menu skrótów węzła projektu wdrożenia wybierz pozycję **Wdrażanie** > **nowego**.

    ![Nowy element menu wdrażania](./media/create-visual-studio-deployment-project/deploy.png)

1. Zostanie wyświetlone okno dialogowe **Wdrażanie w grupie zasobów**. W polu listy rozwijanej **Grupa zasobów** wybierz istniejącą grupę zasobów lub utwórz nową. Wybierz pozycję **Wdróż**.

    ![Okno dialogowe Wdrażanie w grupie zasobów](./media/create-visual-studio-deployment-project/show-deployment.png)

1. W oknach **Dane wyjściowe** jest wyświetlany stan wdrożenia. Po zakończeniu wdrażania ostatni komunikat wskazuje pomyślne wdrożenie i jest podobny do następującego:

   ```output
   18:00:58 - Successfully deployed template 'website.json' to resource group 'ExampleAppDeploy'.
   ```

## <a name="view-deployed-resources"></a>Wyświetlanie wdrożonych zasobów

Sprawdźmy wyniki.

1. W przeglądarce otwórz [witrynę Azure portal](https://portal.azure.com/) i zaloguj się na swoje konto. Aby wyświetlić grupę zasobów wybierz pozycję **Grupy zasobów**, a następnie wybierz grupę, do której wykonano wdrożenie.

1. Zostaną wyświetlone wszystkie wdrożone zasoby. Należy zauważyć, że nazwa konta magazynu nie jest identyczna z określoną podczas dodawania tego zasobu. Konto magazynu musi być unikatowe. Szablon automatycznie dodaje ciąg znaków do podanej nazwy, aby utworzyć unikatową nazwę.

    ![Pokaż zasoby](./media/create-visual-studio-deployment-project/show-deployed-resources.png)

## <a name="add-code-to-project"></a>Dodawanie kodu do projektu

Wdrożono już infrastrukturę aplikacji, ale z projektem nie został wdrożony rzeczywisty kod.

1. Dodaj projekt do rozwiązania programu Visual Studio. Kliknij prawym przyciskiem myszy rozwiązanie, a następnie wybierz polecenie **Dodaj** > **nowy projekt**.

    ![Dodaj projekt](./media/create-visual-studio-deployment-project/add-project.png)

1. Dodaj **ASP.NET podstawową aplikację sieci Web**.

    ![Dodawanie aplikacji sieci Web](./media/create-visual-studio-deployment-project/add-app.png)

1. Nadaj aplikacji internetowej nazwę i wybierz pozycję **Utwórz**.

    ![Nazwa aplikacji sieci Web](./media/create-visual-studio-deployment-project/name-web-app.png)

1. Wybierz **pozycję Aplikacja sieci Web** i **utwórz**.

    ![Wybieranie aplikacji sieci Web](./media/create-visual-studio-deployment-project/select-project-type.png)

1. Po utworzeniu aplikacji internetowej przez program Visual Studio oba projekty są wyświetlane w rozwiązaniu.

    ![Pokaż projekty](./media/create-visual-studio-deployment-project/show-projects.png)

1. Musisz teraz upewnić się, że projekt grupy zasobów wie o nowym projekcie. Wróć do projektu grupy zasobów (ExampleAppDeploy). Kliknij prawym przyciskiem myszy pozycję **Odwołania** i wybierz polecenie **Dodaj odwołanie**.

    ![Dodawanie odwołania](./media/create-visual-studio-deployment-project/add-new-reference.png)

1. Wybierz projekt aplikacji internetowej, który został utworzony.

   ![Dodawanie odwołania](./media/create-visual-studio-deployment-project/add-reference.png)

   Dodając odwołanie, należy połączyć projekt aplikacji sieci web z projektem grupy zasobów i automatycznie ustawia niektóre właściwości. Te właściwości będą wyświetlane w oknie **Właściwości** odwołania. Właściwość **Include File Path** zawiera ścieżkę, w której zostanie utworzony pakiet. Zanotuj folder (ExampleApp) i plik (package.zip). Musisz znać te wartości, ponieważ są one podawane jako parametry podczas wdrażania aplikacji.

   ![Zobacz odwołanie](./media/create-visual-studio-deployment-project/see-reference.png)

1. Wróć do szablonu (WebSite.json) i dodaj zasób do szablonu.

    ![Dodawanie zasobu](./media/create-visual-studio-deployment-project/add-resource-2.png)

1. Tym razem wybierz pozycję **Web Deploy dla usługi Web Apps**.

    ![Dodawanie wdrażania sieci Web](./media/create-visual-studio-deployment-project/add-web-deploy.png)

   Zapisz szablon.

1. W szablonie pojawiło się kilka nowych parametrów. Zostały one dodane w poprzednim kroku. Nie trzeba podawać wartości dla **_artifactsLocation** lub **_artifactsLocationSasToken,** ponieważ te wartości są generowane automatycznie. Jednak należy ustawić nazwę folderu i pliku do ścieżki, która zawiera pakiet wdrażania. Nazwy tych parametrów kończą się **na packagefolder** i **PackageFileName**. Pierwsza część nazwy to nazwa dodanego zasobu wdrażania sieci Web. W tym artykule są one nazwane **ExampleAppPackageFolder** i **ExampleAppPackageFileName**.

   Otwórz **witrynę Website.parameters.json** i ustaw te parametry na wartości, które zostały wyświetlone we właściwościach odwołania. Ustaw **exampleAppPackageFolder** na nazwę folderu. Ustaw **ExampleAppPackageFileName** na nazwę pliku zip.

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

## <a name="deploy-code-with-infrastructure"></a>Wdrażanie kodu z infrastrukturą

Ponieważ dodano kod do projektu, wdrożenie jest nieco inny tym razem. Podczas wdrażania artefakty dla projektu są wystawiane w miejscu, do którego menedżer zasobów może uzyskać dostęp. Artefakty są przemieszczane na koncie magazynu.

### <a name="az-module-script"></a>Skrypt modułu Az

Jest jedna mała zmiana, którą musisz wprowadzić w szablonie, jeśli używasz skryptu modułu Az. Ten skrypt dodaje ukośnik do lokalizacji artefaktów, ale szablon nie oczekuje, że ukośnik. Otwórz witrynę WebSite.json i znajdź właściwości rozszerzenia MSDeploy. Ma właściwość o nazwie **packageUri**. Usuń ukośnik między lokalizacją artefaktów a folderem pakietu.

Powinno to wyglądać następująco:

```json
"packageUri": "[concat(parameters('_artifactsLocation'), parameters('ExampleAppPackageFolder'), '/', parameters('ExampleAppPackageFileName'), parameters('_artifactsLocationSasToken'))]",
```

Uwaga w poprzednim przykładzie `'/',` nie ma między **parametrami("_artifactsLocation")** i **parametrami("ExampleAppPackageFolder")**.

Odbuduj projekt. Tworzenie projektu upewnia się, że pliki, które należy wdrożyć, są dodawane do folderu przemieszczania.

Teraz otwórz konsolę programu PowerShell i uruchom:

```powershell
.\Deploy-AzTemplate.ps1 -ArtifactStagingDirectory .\bin\Debug\staging\ExampleAppDeploy -Location centralus -TemplateFile WebSite.json -TemplateParametersFile WebSite.parameters.json -UploadArtifacts -StorageAccountName <storage-account-name>
```

### <a name="azurerm-module-script"></a>Skrypt modułu AzureRM

W przypadku skryptu modułu AzureRM użyj programu Visual Studio:

1. Aby ponownie wdrożyć, wybierz pozycję **Wdrażanie**i grupę zasobów wdrożoną wcześniej.

    ![Projekt ponownego rozmieszczania](./media/create-visual-studio-deployment-project/redeploy.png)

1. Wybierz konto magazynu wdrożone w tej grupie zasobów dla **konta magazynu artefaktów**.

   ![Ponowne wdrażanie w sieci Web](./media/create-visual-studio-deployment-project/redeploy-web-app.png)

## <a name="view-web-app"></a>Wyświetlanie aplikacji sieci Web

1. Po zakończeniu wdrażania wybierz aplikację internetową w witrynie portalu. Wybierz adres URL, aby przejść do lokacji.

   ![Przeglądaj witrynę](./media/create-visual-studio-deployment-project/browse-site.png)

1. Zobaczysz, że domyślna aplikacja ASP.NET została pomyślnie wdrożona.

   ![Pokaż wdrożoną aplikację](./media/create-visual-studio-deployment-project/show-deployed-app.png)

## <a name="add-operations-dashboard"></a>Dodaj pulpit nawigacyjny operacji

Możesz korzystać nie tylko z zasobów dostępnych z poziomu interfejsu programu Visual Studio. Można dostosować wdrożenie, dodając do szablonu zasoby niestandardowe. Aby zobaczyć, jak działa dodawanie zasobu, dodasz operacyjny pulpit nawigacyjny w celu zarządzania wdrożonymi zasobami.

1. Otwórz plik WebSite.json i dodaj następujący JSON po zasobie `]` konta magazynu, ale przed zamknięciem sekcji zasobów.

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
      "type": "Microsoft.Portal/dashboards",
      "apiVersion": "2015-08-01-preview",
      "name": "[concat('ARM-',resourceGroup().name)]",
      "location": "[resourceGroup().location]",
      "tags": {
        "hidden-title": "[concat('OPS-',resourceGroup().name)]"
      }
    }
   ```

1. Ponownie wdrożyć swój projekt.

1. Po zakończeniu wdrażania wyświetl pulpit nawigacyjny w portalu. Wybierz **pulpit nawigacyjny** i wybierz wdrożony.

   ![Niestandardowy pulpit nawigacyjny](./media/create-visual-studio-deployment-project/view-custom-dashboards.png)

1. Zobaczysz dostosowany pulpit nawigacyjny.

   ![Niestandardowy pulpit nawigacyjny](./media/create-visual-studio-deployment-project/Ops-DemoSiteGroup-dashboard.png)

Możesz zarządzać dostępem do pulpitu nawigacyjnego, korzystając z grup kontroli dostępu opartych na rolach. Po wdrożeniu pulpitu nawigacyjnego możesz też dostosowywać jego wygląd. Jeśli jednak ponownie wdrożysz grupę zasobów, pulpit nawigacyjny zostanie zresetowany do domyślnego stanu określonego w szablonie. Aby uzyskać więcej informacji na temat tworzenia pulpitów nawigacyjnych, zobacz [Programmatically create Azure Dashboards (Programowe tworzenie pulpitów nawigacyjnych platformy Azure)](../../azure-portal/azure-portal-dashboards-create-programmatically.md).

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Gdy zasoby platformy Azure nie będą już potrzebne, wyczyść wdrożone zasoby, usuwając grupę zasobów.

1. W witrynie Azure portal wybierz **grupy zasobów** z lewego menu.

1. Wybierz nazwę grupy zasobów.

1. Wybierz **pozycję Usuń grupę zasobów** z górnego menu.

## <a name="next-steps"></a>Następne kroki

W tym artykule dowiesz się, jak tworzyć i wdrażać szablony za pomocą programu Visual Studio. Aby dowiedzieć się więcej o tworzeniu szablonów, zobacz naszą nową serię samouczków dla początkujących:

> [!div class="nextstepaction"]
> [Samouczki dla początkujących](./template-tutorial-create-first-template.md)
