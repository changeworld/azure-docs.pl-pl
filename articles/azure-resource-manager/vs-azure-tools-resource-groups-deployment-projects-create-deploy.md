---
title: Utwórz & Wdróż projekty grupy zasobów platformy Azure dla programu Visual Studio
description: Użyj programu Visual Studio do utworzenia projektu grupy zasobów platformy Azure i wdrożenia zasobów na platformie Azure.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: quickstart
ms.date: 06/20/2019
ms.author: tomfitz
ms.openlocfilehash: c7f71dbadf24244756cf65f68ceea24547190737
ms.sourcegitcommit: aef6040b1321881a7eb21348b4fd5cd6a5a1e8d8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2019
ms.locfileid: "72170241"
---
# <a name="creating-and-deploying-azure-resource-groups-through-visual-studio"></a>Tworzenie i wdrażanie grup zasobów platformy Azure za pomocą programu Visual Studio

Za pomocą programu Visual Studio można utworzyć projekt, który wdraża infrastrukturę i kod na platformie Azure. Można na przykład wdrożyć hosta sieci Web, witrynę internetową i kod witryny internetowej. Program Visual Studio oferuje wiele różnych początkowych szablonów do wdrażania typowych scenariuszy. W tym artykule opisano wdrażanie aplikacji sieci Web.

W tym artykule pokazano, jak używać [programu Visual Studio 2019 lub nowszego z zainstalowanymi obciążeniami deweloperskimi i ASP.NET platformy Azure](/visualstudio/install/install-visual-studio?view=vs-2019). W przypadku korzystania z programu Visual Studio 2017 środowisko jest w dużym stopniu takie samo.

## <a name="create-azure-resource-group-project"></a>Tworzenie projektu grupy zasobów platformy Azure

W tej sekcji utworzysz projekt grupy zasobów platformy Azure z szablonem **aplikacji sieci Web** .

1. W programie Visual Studio wybierz kolejno opcje **plik**, **Nowy**i **projekt**. Wybierz szablon projektu **grupy zasobów platformy Azure** i kliknij przycisk **dalej**.

    ![Utwórz projekt](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/create-project.png)

1. Nadaj projektowi nazwę. Inne ustawienia domyślne są prawdopodobnie odpowiednie, ale zapoznaj się z nimi, aby działały w danym środowisku. Po zakończeniu wybierz pozycję **Utwórz**.

    ![Utwórz projekt](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/name-project.png)

1. Wybierz szablon, który chcesz wdrożyć, aby Azure Resource Manager. Należy zauważyć, że istnieje wiele różnych opcji w zależności od typu projektu, który ma zostać wdrożony. W tym artykule wybierz szablon **aplikacji sieci Web** i **przycisk OK**.

    ![Wybierz szablon](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/select-project.png)

    Wybierany szablon to tylko punkt początkowy; Możesz dodawać i usuwać zasoby, aby zrealizować swój scenariusz.

1. Program Visual Studio tworzy projekt wdrożenia grupy zasobów dla aplikacji sieci Web. Aby wyświetlić pliki dla projektu, przyjrzyj się węzłowi w projekcie wdrożenia.

    ![Pokaż węzły](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/show-items.png)

    Po wybraniu szablonu aplikacji sieci Web zobaczysz następujące pliki:

   | Nazwa pliku | Opis |
   | --- | --- |
   | Deploy-AzureResourceGroup. ps1 |Skrypt programu PowerShell, który uruchamia polecenia programu PowerShell do wdrożenia w Azure Resource Manager. Program Visual Studio używa tego skryptu programu PowerShell do wdrożenia szablonu. |
   | Plik Web. JSON |Szablon Menedżer zasobów, który definiuje infrastrukturę, którą chcesz wdrożyć na platformie Azure, oraz parametry, które można podać podczas wdrażania. Definiuje również zależności między zasobami, aby Menedżer zasobów wdrażać zasoby w odpowiedniej kolejności. |
   | Web. Parameters. JSON |Plik parametrów, który zawiera wartości wymagane przez szablon. Wartości parametrów są przekazywane w celu dostosowania poszczególnych wdrożeń. |

    Wszystkie projekty wdrożenia grupy zasobów mają te podstawowe pliki. Inne projekty mogą zawierać dodatkowe pliki do obsługi innych funkcji.

## <a name="customize-resource-manager-template"></a>Dostosowywanie szablonu Menedżer zasobów

Projekt wdrożenia można dostosować, modyfikując szablon Menedżer zasobów, który opisuje zasoby, które mają zostać wdrożone. Aby dowiedzieć się więcej na temat elementów szablonu Menedżer zasobów, zobacz [tworzenie Azure Resource Manager szablonów](resource-group-authoring-templates.md).

1. Aby móc korzystać z szablonu, Otwórz plik **Web. JSON**.

1. Edytor programu Visual Studio udostępnia narzędzia ułatwiające edytowanie szablonu Menedżer zasobów. Okno **konspektu JSON** ułatwia Wyświetlanie elementów zdefiniowanych w szablonie.

   ![Pokaż konspekt JSON](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/show-json-outline.png)

1. Wybierz element w konspekcie, aby przejść do tej części szablonu.

   ![Nawigacja w formacie JSON](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/navigate-json.png)

1. Możesz dodać zasób, wybierając przycisk **Dodaj zasób** u góry okna konspektu JSON lub klikając prawym przyciskiem myszy pozycję **zasoby** i wybierając polecenie **Dodaj nowy zasób**.

   ![Dodaj zasób](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/add-resource.png)

1. Wybierz pozycję **konto magazynu** i nadaj jej nazwę. Podaj nazwę, która nie ma więcej niż 11 znaków i zawiera tylko cyfry i małe litery.

   ![Dodawanie magazynu](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/add-storage.png)

1. Należy zauważyć, że dodano nie tylko zasób, ale również parametr dla konta magazynu typu oraz zmienna dla nazwy konta magazynu.

   ![Pokaż konspekt](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/show-new-items.png)

1. Parametr dla typu konta magazynu jest wstępnie zdefiniowany z dozwolonymi typami i typem domyślnym. Możesz pozostawić te wartości lub edytować je w danym scenariuszu. Jeśli nie chcesz, aby wszyscy użytkownicy mogli wdrożyć konto magazynu **Premium_LRS** za pośrednictwem tego szablonu, usuń je z dozwolonych typów.

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

1. Program Visual Studio udostępnia również funkcję IntelliSense, która ułatwia zrozumienie właściwości dostępnych podczas edytowania szablonu. Aby na przykład edytować właściwości planu App Service, przejdź do zasobu **HostingPlan** i Dodaj wartość **Właściwości**. Zauważ, że technologia IntelliSense wyświetla dostępne wartości i zawiera opis tej wartości.

   ![Pokaż funkcję IntelliSense](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/show-intellisense.png)

   Można ustawić **numberOfWorkers** na 1 i zapisać plik.

   ```json
   "properties": {
     "name": "[parameters('hostingPlanName')]",
     "numberOfWorkers": 1
   }
   ```

1. Otwórz plik **Web. Parameters. JSON** . Plik parametrów służy do przekazywania wartości podczas wdrażania, który dostosowuje wdrażany zasób. Nadaj nazwę planu hostingu i Zapisz plik.

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

Domyślnie skrypt programu PowerShell (Deploy-AzureResourceGroup. ps1) w projekcie używa modułu AzureRM. Jeśli nadal masz zainstalowany moduł AzureRM i chcesz kontynuować korzystanie z niego, możesz użyć tego domyślnego skryptu. Za pomocą tego skryptu można wdrożyć rozwiązanie przy użyciu interfejsu programu Visual Studio.

Jeśli jednak przeprowadzono migrację do nowego elementu [AZ module](/powershell/azure/new-azureps-module-az), musisz dodać nowy skrypt do projektu. Aby dodać skrypt, który używa AZ module, skopiuj skrypt [Deploy-AzTemplate. ps1](https://github.com/Azure/azure-quickstart-templates/blob/master/Deploy-AzTemplate.ps1) i dodaj go do projektu. Aby użyć tego skryptu do wdrożenia, należy uruchomić go z konsoli programu PowerShell, zamiast korzystać z interfejsu wdrażania programu Visual Studio.

W tym artykule przedstawiono oba podejścia. Ten artykuł odnosi się do domyślnego skryptu jako skryptu modułu AzureRM oraz nowego skryptu jako skryptu AZ module.

### <a name="az-module-script"></a>AZ module Script

Dla skryptu AZ module Otwórz konsolę programu PowerShell i uruchom polecenie:

```powershell
.\Deploy-AzTemplate.ps1 -ArtifactStagingDirectory . -Location centralus -TemplateFile WebSite.json -TemplateParametersFile WebSite.parameters.json
```

### <a name="azurerm-module-script"></a>Skrypt modułu AzureRM

W przypadku skryptu modułu AzureRM Użyj programu Visual Studio:

1. W menu skrótów węzła projektu wdrażania wybierz pozycję **wdróż** > **Nowy**.

    ![Nowy element menu wdrożenia](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/deploy.png)

1. Zostanie wyświetlone okno dialogowe **wdrażanie w grupie zasobów** . W polu listy rozwijanej **Grupa zasobów** wybierz istniejącą grupę zasobów lub Utwórz nową. Wybierz pozycję **Wdróż**.

    ![Okno dialogowe wdrażanie w grupie zasobów](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/show-deployment.png)

1. W oknach **danych wyjściowych** zostanie wyświetlony stan wdrożenia. Po zakończeniu wdrożenia ostatni komunikat wskazuje na pomyślne wdrożenie z podobną do:

   ```output
   18:00:58 - Successfully deployed template 'website.json' to resource group 'ExampleAppDeploy'.
   ```

## <a name="view-deployed-resources"></a>Wyświetl wdrożone zasoby

Sprawdźmy wyniki.

1. W przeglądarce Otwórz [Azure Portal](https://portal.azure.com/) i zaloguj się na swoim koncie. Aby wyświetlić grupę zasobów, wybierz pozycję **grupy zasobów** i grupę zasobów, do której została wdrożona.

1. Zobaczysz wszystkie wdrożone zasoby. Należy zauważyć, że nazwa konta magazynu nie jest dokładnie określona podczas dodawania tego zasobu. Konto magazynu musi być unikatowe. Szablon automatycznie dodaje ciąg znaków do podanej nazwy, aby utworzyć unikatową nazwę.

    ![Pokaż zasoby](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/show-deployed-resources.png)

## <a name="add-code-to-project"></a>Dodawanie kodu do projektu

W tym momencie wdrożono infrastrukturę dla aplikacji, ale nie istnieje rzeczywisty kod wdrożony w projekcie.

1. Dodaj projekt do rozwiązania programu Visual Studio. Kliknij prawym przyciskiem myszy rozwiązanie, a następnie wybierz pozycję **dodaj** > **Nowy projekt**.

    ![Dodaj projekt](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/add-project.png)

1. Dodaj **ASP.NET Core aplikację sieci Web**.

    ![Dodaj aplikację sieci Web](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/add-app.png)

1. Nadaj nazwę aplikacji sieci Web, a następnie wybierz pozycję **Utwórz**.

    ![Nazwa aplikacji sieci Web](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/name-web-app.png)

1. Wybierz pozycję **aplikacja sieci Web** i **Utwórz**.

    ![Wybierz aplikację sieci Web](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/select-project-type.png)

1. Po utworzeniu aplikacji sieci Web przez program Visual Studio zobaczysz oba projekty w rozwiązaniu.

    ![Pokaż projekty](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/show-projects.png)

1. Teraz musisz upewnić się, że projekt grupy zasobów jest świadomy nowego projektu. Wróć do projektu grupy zasobów (ExampleAppDeploy). Kliknij prawym przyciskiem myszy pozycję **odwołania** i wybierz polecenie **Dodaj odwołanie**.

    ![Dodaj odwołanie](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/add-new-reference.png)

1. Wybierz utworzony projekt aplikacji sieci Web.

   ![Dodaj odwołanie](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/add-reference.png)

   Dodanie odwołania powoduje połączenie projektu aplikacji sieci Web z projektem grupy zasobów i automatyczne ustawienie niektórych właściwości. Te właściwości są wyświetlane w oknie **Właściwości** dla odwołania. **Ścieżka do pliku dołączanego** ma ścieżkę, w której tworzony jest pakiet. Zanotuj folder (ExampleApp) i plik (Package. zip). Musisz znać te wartości, ponieważ są one podane jako parametry podczas wdrażania aplikacji.

   ![Zobacz odwołanie](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/see-reference.png)

1. Wróć do szablonu (Web. JSON) i Dodaj zasób do szablonu.

    ![Dodaj zasób](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/add-resource-2.png)

1. Tym razem wybierz **Web Deploy dla Web Apps**.

    ![Dodawanie narzędzia Web Deploy](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/add-web-deploy.png)

   Zapisz szablon.

1. Szablon zawiera nowe parametry. Dodano je w poprzednim kroku. Nie musisz podawać wartości dla **_artifactsLocation** lub **_artifactsLocationSasToken** , ponieważ te wartości są generowane automatycznie. Należy jednak ustawić folder i nazwę pliku na ścieżkę zawierającą pakiet wdrożeniowy. Nazwy tych parametrów kończą się z **PackageFolder** i **PackageFileName**. Pierwsza część nazwy jest nazwą dodanego zasobu Web Deploy. W tym artykule są one nazwane **ExampleAppPackageFolder** i **ExampleAppPackageFileName**.

   Otwórz plik **Web. Parameters. JSON** i ustaw te parametry na wartości, które zostały podane we właściwościach odwołania. Ustaw **ExampleAppPackageFolder** na nazwę folderu. Ustaw **ExampleAppPackageFileName** na nazwę pliku zip.

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

Ponieważ kod został dodany do projektu, wdrożenie jest nieco inne. Podczas wdrażania można przemieszczać artefakty dla projektu do miejsca, do którego Menedżer zasobów może uzyskać dostęp. Artefakty są przemieszczane do konta magazynu.

### <a name="az-module-script"></a>AZ module Script

Jeśli używasz skryptu AZ module, musisz wprowadzić jedną małą zmianę w szablonie. Ten skrypt dodaje ukośnik do lokalizacji artefaktów, ale szablon nie oczekuje tego ukośnika. Otwórz plik Web. JSON i Znajdź właściwości rozszerzenia MSDeploy. Ma właściwość o nazwie **packageUri**. Usuń ukośnik między lokalizacją artefaktów a folderem pakietu.

Powinien wyglądać następująco:

```json
"packageUri": "[concat(parameters('_artifactsLocation'), parameters('ExampleAppPackageFolder'), '/', parameters('ExampleAppPackageFileName'), parameters('_artifactsLocationSasToken'))]",
```

Zwróć uwagę, że w poprzednim przykładzie nie ma `'/',` między **parametrami ("_artifactsLocation")** i **parametrami ("ExampleAppPackageFolder")** .

Ponownie skompiluj projekt. Kompilowanie projektu pozwala upewnić się, że pliki, które należy wdrożyć, są dodawane do folderu przemieszczania.

Teraz otwórz konsolę programu PowerShell i uruchom następujące polecenie:

```powershell
.\Deploy-AzTemplate.ps1 -ArtifactStagingDirectory .\bin\Debug\staging\ExampleAppDeploy -Location centralus -TemplateFile WebSite.json -TemplateParametersFile WebSite.parameters.json -UploadArtifacts -StorageAccountName <storage-account-name>
```

### <a name="azurerm-module-script"></a>Skrypt modułu AzureRM

W przypadku skryptu modułu AzureRM Użyj programu Visual Studio:

1. Aby przeprowadzić ponowne wdrożenie, wybierz pozycję **Wdróż**i wdrożoną wcześniej grupę zasobów.

    ![Wdróż ponownie projekt](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/redeploy.png)

1. Wybierz konto magazynu wdrożone za pomocą tej grupy zasobów dla **konta magazynu artefaktów**.

   ![Wdróż ponownie Web Deploy](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/redeploy-web-app.png)

## <a name="view-web-app"></a>Wyświetl aplikację sieci Web

1. Po zakończeniu wdrażania wybierz swoją aplikację sieci Web w portalu. Wybierz adres URL, aby przejść do witryny.

   ![Przeglądaj witrynę](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/browse-site.png)

1. Zauważ, że domyślna aplikacja ASP.NET została pomyślnie wdrożona.

   ![Pokaż wdrożoną aplikację](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/show-deployed-app.png)

## <a name="add-operations-dashboard"></a>Dodaj pulpit nawigacyjny operacji

Nie tylko zasoby, które są dostępne za pomocą interfejsu programu Visual Studio. Wdrożenie można dostosować, dodając zasób niestandardowy do szablonu. Aby pokazać Dodawanie zasobu, należy dodać operacyjny pulpit nawigacyjny do zarządzania wdrożonym zasobem.

1. Otwórz plik Web. JSON i Dodaj następujący kod JSON po zasobie konta magazynu, ale przed zamknięciem `]` w sekcji Resources.

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

1. Wdróż ponownie projekt.

1. Po zakończeniu wdrażania Wyświetl pulpit nawigacyjny w portalu. Wybierz pozycję **pulpit nawigacyjny** i wybierz wdrożony.

   ![Niestandardowy pulpit nawigacyjny](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/view-custom-dashboards.png)

1. Zobaczysz dostosowany pulpit nawigacyjny.

   ![Niestandardowy pulpit nawigacyjny](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/Ops-DemoSiteGroup-dashboard.png)

Dostęp do pulpitu nawigacyjnego można zarządzać przy użyciu grup RBAC. Możesz również dostosować wygląd pulpitu nawigacyjnego po jego wdrożeniu. Jednak w przypadku ponownego wdrożenia grupy zasobów pulpit nawigacyjny zostanie zresetowany do stanu domyślnego w szablonie. Aby uzyskać więcej informacji na temat tworzenia pulpitów nawigacyjnych, zobacz programowe [Tworzenie pulpitów nawigacyjnych platformy Azure](../azure-portal/azure-portal-dashboards-create-programmatically.md).

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Gdy zasoby platformy Azure nie są już potrzebne, Oczyść wdrożone zasoby, usuwając grupę zasobów.

1. Z Azure Portal z menu po lewej stronie wybierz pozycję **grupy zasobów** .

1. Wybierz nazwę grupy zasobów.

1. W górnym menu wybierz pozycję **Usuń grupę zasobów** .

## <a name="next-steps"></a>Następne kroki

W tym przewodniku szybki start przedstawiono sposób tworzenia i wdrażania szablonów przy użyciu programu Visual Studio. Aby dowiedzieć się więcej na temat tworzenia szablonów, zobacz naszą nową serię samouczków dla początkujących:

> [!div class="nextstepaction"]
> [Samouczki początkującego](./template-tutorial-create-first-template.md)
