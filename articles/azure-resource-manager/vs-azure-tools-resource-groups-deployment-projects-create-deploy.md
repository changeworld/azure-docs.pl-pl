---
title: Projekty grup zasobów platformy Azure programu Visual Studio | Microsoft Docs
description: Tworzenie projektu grupy zasobów platformy Azure przy użyciu programu Visual Studio i wdrażanie zasobów na platformie Azure.
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.service: azure-resource-manager
ms.devlang: multiple
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/21/2019
ms.author: tomfitz
ms.openlocfilehash: 442551424fea353aa7eddef6e7eba6e934f95691
ms.sourcegitcommit: 9f4eb5a3758f8a1a6a58c33c2806fa2986f702cb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/03/2019
ms.locfileid: "58905855"
---
# <a name="creating-and-deploying-azure-resource-groups-through-visual-studio"></a>Tworzenie i wdrażanie grup zasobów platformy Azure za pomocą programu Visual Studio

Program Visual Studio umożliwia utworzenie projektu służącego do wdrażania infrastruktury i kodu na platformie Azure. Można na przykład zdefiniować host internetowy, witrynę internetową i bazę danych dla aplikacji, a następnie wdrożyć tę infrastrukturę wraz z kodem. Program Visual Studio zapewnia wiele różnych szablonów początkowych do wdrażania typowych scenariuszy. Ten artykuł dotyczy wdrażania aplikacji internetowej i bazy danych SQL Database.  

W tym artykule pokazano, jak używać programu [Visual Studio 2017 z zainstalowanymi pakietami roboczymi programowania dla platformy Azure i ASP.NET](/dotnet/azure/dotnet-tools). Jeśli używasz programu Visual Studio 2015 Update 2 i zestawu Microsoft Azure SDK dla środowiska .NET 2.9 lub programu Visual Studio 2013 z zestawem Azure SDK 2.9, czynności są w większości takie same.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="create-azure-resource-group-project"></a>Tworzenie projektu grupy zasobów platformy Azure

W tej sekcji omówiono tworzenie projektu grupy zasobów platformy Azure przy użyciu szablonu **Aplikacja internetowa i baza danych SQL**.

1. W programie Visual Studio wybierz kolejno pozycje **Plik**, **Nowy projekt**, następnie wybierz pozycję **C#** lub **Visual Basic** (wybór języka nie ma wpływu na późniejszym etapie, ponieważ te projekty zawierają tylko zawartość JSON i zawartość programu PowerShell). Wybierz pozycję **Chmura**, a następnie wybierz projekt **Grupa zasobów platformy Azure**.
   
    ![Projekt wdrażania w chmurze](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/create-project.png)
2. Wybierz szablon, który ma zostać wdrożony w usłudze Azure Resource Manager. Należy zauważyć, że dostępnych jest wiele różnych opcji, zależnie od typu projektu, który chcesz wdrożyć. Na potrzeby tego artykułu wybierz szablon **Aplikacja internetowa i baza danych SQL**.
   
    ![Wybieranie szablonu](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/select-project.png)
   
    Wybrany szablon jest tylko punktem wyjściowym. Możesz dodać lub usunąć zasoby, aby wykonać dany scenariusz.
   
   > [!NOTE]
   > Program Visual Studio pobiera listę dostępnych szablonów w trybie online. Może ona ulec zmianie.
   > 
   > 
   
    Program Visual Studio tworzy projekt wdrożenia grupy zasobów dla aplikacji internetowej i bazy danych SQL.
3. Aby wyświetlić utworzone elementy, sprawdź węzeł w projekcie wdrożenia.
   
    ![Wyświetlanie węzłów](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/show-items.png)
   
    Ponieważ w tym przykładzie wybrano szablon Aplikacja internetowa i baza danych SQL, zostaną wyświetlone następujące pliki: 
   
   | Nazwa pliku | Opis |
   | --- | --- |
   | Deploy-AzureResourceGroup.ps1 |Skrypt programu PowerShell, który uruchamia polecenia w celu wdrożenia do usługi Azure Resource Manager.<br />**Uwaga:** program Visual Studio używa tego skryptu programu PowerShell do wdrażania szablonu. Należy zachować ostrożność, ponieważ wszystkie zmiany wprowadzone w tym skrypcie mają wpływ na wdrożenie w programie Visual Studio. |
   | WebSiteSQLDatabase.json |Szablon usługi Resource Manager określający infrastrukturę, którą chcesz wdrożyć na platformie Azure, oraz parametry, które można podać podczas wdrażania. Umożliwia on również definiowanie zależności między zasobami, tak aby usługa Resource Manager wdrożyła je w odpowiedniej kolejności. |
   | WebSiteSQLDatabase.parameters.json |Plik parametrów zawierający wartości wymagane przez szablon. Poszczególne wdrożenia są dostosowywane za pomocą przekazywanych wartości parametrów. |
   
    Wszystkie projekty wdrażania grup zasobów zawierają te podstawowe pliki. Inne projekty mogą zawierać dodatkowe pliki do obsługi innych funkcji.

## <a name="customize-the-resource-manager-template"></a>Dostosowywanie szablonu usługi Resource Manager
Można dostosować projekt wdrożenia, modyfikując szablony JSON określające zasoby, które chcesz wdrożyć. JSON (JavaScript Object Notation) jest łatwym w użyciu formatem serializowanych danych. Pliki JSON używają schematu, do którego odwołujesz się w górnej części każdego pliku. Aby zrozumieć schemat, możesz go pobrać i przeanalizować. Schemat określa prawidłowe elementy, typy i formaty pól, a także dopuszczalne wartości właściwości. Aby uzyskać informacje na temat elementów szablonu usługi Resource Manager, zobacz [Authoring Azure Resource Manager templates (Tworzenie szablonów usługi Azure Resource Manager)](resource-group-authoring-templates.md).

Aby rozpocząć pracę nad szablonem, otwórz plik **WebSiteSQLDatabase.json**.

Edytor programu Visual Studio udostępnia narzędzia pomocne podczas edytowania szablonu usługi Resource Manager. Okno **Konspekt pliku JSON** ułatwia wyświetlanie elementów zdefiniowanych w szablonie.

![wyświetlanie konspektu pliku JSON](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/show-json-outline.png)

Wybranie dowolnego elementu w konspekcie powoduje przejście do odpowiedniej części szablonu i wyróżnienie powiązanego kodu JSON.

![przechodzenie do elementów JSON](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/navigate-json.png)

Nowy zasób można dodać, wybierając przycisk **Dodaj zasób** u góry okna Konspekt pliku JSON lub klikając prawym przyciskiem myszy węzeł **zasoby** i wybierając polecenie **Dodaj nowy zasób**.

![dodawanie zasobu](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/add-resource.png)

W ramach tego samouczka wybierz pozycję **Konto usługi Storage** i nadaj mu nazwę. Podaj nazwę nie dłuższą niż 11 znaków, zawierającą tylko cyfry i małe litery.

![dodawanie magazynu](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/add-storage.png)

Należy zauważyć, że dodany został nie tylko zasób, ale również parametr typu konta magazynu oraz zmienna nazwy konta magazynu.

![wyświetlanie konspektu](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/show-new-items.png)

Parametr **storageType** jest wstępnie zdefiniowany wraz z dozwolonymi typami i typem domyślnym. Możesz pozostawić te wartości bez zmian lub edytować je dla danego scenariusza. Jeśli nie chcesz, aby inne osoby wdrażały konta magazynu **Premium_LRS** przy użyciu tego szablonu, usuń go z dozwolonych typów. 

```json
"storageType": {
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

Program Visual Studio oferuje również funkcję IntelliSense ułatwiającą zrozumienie dostępnych właściwości podczas edytowania szablonu. Aby na przykład edytować właściwości planu usługi App Service, przejdź do zasobu **HostingPlan** i dodaj wartość dla obiektu **properties**. Należy zauważyć, że funkcja IntelliSense wyświetla dostępne wartości oraz ich opisy.

![wyświetlanie funkcji intellisense](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/show-intellisense.png)

Dla wartości **numberOfWorkers** można ustawić wartość 1.

```json
"properties": {
  "name": "[parameters('hostingPlanName')]",
  "numberOfWorkers": 1
}
```

## <a name="deploy-the-resource-group-project-to-azure"></a>Wdrażanie projektu grupy zasobów na platformie Azure
Teraz można przystąpić do wdrażania projektu. Projekt grupy zasobów platformy Azure jest wdrażany do grupy zasobów platformy Azure. To logiczna grupa zasobów mających wspólny cykl życia.

1. W menu skrótów węzła projektu wdrażania wybierz pozycję **Wdróż** > **Nowe**.
   
    ![Element menu Wdróż, Nowe wdrażanie](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/deploy.png)
   
    Zostanie wyświetlone okno dialogowe **Wdrażanie w grupie zasobów**.
   
    ![Okno dialogowe Wdrażanie w grupie zasobów](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/show-deployment.png)
2. W polu listy rozwijanej **Grupa zasobów** wybierz istniejącą grupę zasobów lub utwórz nową. Aby utworzyć grupę, otwórz pole listy rozwijanej **Grupa zasobów** i wybierz pozycję **Utwórz nowy**.
   
    ![Okno dialogowe Wdrażanie w grupie zasobów](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/create-new-group.png)
   
    Zostanie wyświetlone okno dialogowe **Tworzenie grupy zasobów**. Podaj nazwę i lokalizację grupy i wybierz przycisk **Utwórz**.
   
    ![Okno dialogowe Tworzenie grupy zasobów](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/create-resource-group.png)
3. Edytuj parametry wdrożenia, wybierając przycisk **Edytuj parametry**.
   
    ![Przycisk Edytuj parametry](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/edit-parameters.png)
4. Podaj wartości dla pustych parametrów i wybierz przycisk **Zapisz**. Puste parametry to **hostingPlanName**, **administratorLogin**, **administratorLoginPassword** i **databaseName**.
   
    Parametr **hostingPlanName** określa nazwę [planu usługi App Service](../app-service/overview-hosting-plans.md) do utworzenia. 
   
    Parametr **administratorLogin** określa nazwę użytkownika dla administratora programu SQL Server. Nie należy używać typowych nazw administratorów, takich jak **sa** lub **admin**. 
   
    Parametr **administratorLoginPassword** określa hasło administratora programu SQL Server. Opcja **Zapisz hasła jako zwykły tekst w pliku parametrów** nie jest bezpieczna, dlatego nie należy jej wybierać. Ponieważ hasło nie jest zapisywane w postaci zwykłego tekstu, należy podać to hasło ponownie podczas wdrażania. 
   
    Parametr **databaseName** określa nazwę bazy danych do utworzenia. 
   
    ![Okno dialogowe Edytowanie parametrów](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/provide-parameters.png)
5. Wybierz przycisk **Wdróż**, aby wdrożyć projekt na platformie Azure. Konsola programu PowerShell zostanie otwarta poza wystąpieniem programu Visual Studio. Po wyświetleniu monitu wprowadź hasło administratora programu SQL Server w konsoli programu PowerShell. **Konsola programu PowerShell może być ukryta za innymi elementami lub zminimalizowana na pasku zadań.** Odszukaj tę konsolę i wybierz ją, aby podać hasło.
   
   > [!NOTE]
   > W programie Visual Studio może pojawić się monit o zainstalowanie poleceń cmdlet programu Azure PowerShell. Jeśli zostanie wyświetlony monit, zainstaluj je. Moduły Azure PowerShell są niezbędne do pomyślnego wdrożenia grup zasobów. Skrypt programu PowerShell w projekcie nie działa z nowym [modułem Azure PowerShell Az](/powershell/azure/new-azureps-module-az). 
   >
   > Aby uzyskać więcej informacji, zobacz [Instalowanie i konfigurowanie modułów programu Azure PowerShell](/powershell/azure/install-Az-ps).
   > 
   > 
6. Wdrożenie może potrwać kilka minut. W oknach **Dane wyjściowe** jest wyświetlany stan wdrożenia. Po zakończeniu wdrażania ostatni komunikat wskazuje pomyślne wdrożenie i jest podobny do następującego:
   
        ... 
        18:00:58 - Successfully deployed template 'websitesqldatabase.json' to resource group 'DemoSiteGroup'.
7. W przeglądarce otwórz witrynę [Azure Portal](https://portal.azure.com/) i zaloguj się do swojego konta. Aby wyświetlić grupę zasobów wybierz pozycję **Grupy zasobów**, a następnie wybierz grupę, do której wykonano wdrożenie.
   
    ![wybieranie grupy](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/select-group.png)
8. Zostaną wyświetlone wszystkie wdrożone zasoby. Należy zauważyć, że nazwa konta magazynu nie jest identyczna z określoną podczas dodawania tego zasobu. Konto magazynu musi być unikatowe. Szablon automatycznie dodaje ciąg znaków do podanej nazwy w celu zapewnienia unikatowej nazwy. 
   
    ![wyświetlanie zasobów](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/show-deployed-resources.png)
9. Jeśli wprowadzono zmiany i chcesz ponownie wdrożyć projekt, wybierz istniejącą grupę zasobów przy użyciu menu skrótów projektu grupy zasobów platformy Azure. W menu skrótów wybierz pozycję **Wdróż**, a następnie wybierz wdrożoną grupę zasobów.
   
    ![wdrożona grupa zasobów platformy Azure](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/redeploy.png)

## <a name="deploy-code-with-your-infrastructure"></a>Wdrażanie kodu przy użyciu infrastruktury
Wdrożono już infrastrukturę aplikacji, ale z projektem nie został wdrożony rzeczywisty kod. W tym artykule przedstawiono sposób wdrażania aplikacji internetowej i tabel bazy danych SQL Database podczas wdrażania. Jeśli wdrażasz maszynę wirtualną zamiast aplikacji internetowej, w ramach wdrożenia konieczne będzie uruchomienie kodu na maszynie. Proces wdrażania kodu dla aplikacji internetowej lub konfigurowania maszyny wirtualnej jest niemal taki sam.

1. Dodaj projekt do rozwiązania programu Visual Studio. Kliknij prawym przyciskiem myszy rozwiązanie, a następnie wybierz pozycje **Dodaj** > **Nowy projekt**.
   
    ![dodawanie projektu](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/add-project.png)
2. Dodaj **aplikację internetową ASP.NET**. 
   
    ![dodawanie aplikacji internetowej](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/add-app.png)
3. Wybierz pozycję **MVC**.
   
    ![wybieranie pozycji MVC](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/select-mvc.png)
4. Po utworzeniu aplikacji internetowej przez program Visual Studio oba projekty są wyświetlane w rozwiązaniu.
   
    ![wyświetlanie projektów](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/show-projects.png)
5. Musisz teraz upewnić się, że projekt grupy zasobów wie o nowym projekcie. Wróć do projektu grupy zasobów (AzureResourceGroup1). Kliknij prawym przyciskiem myszy pozycję **Odwołania** i wybierz polecenie **Dodaj odwołanie**.
   
    ![dodawanie odwołania](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/add-new-reference.png)
6. Wybierz projekt aplikacji internetowej, który został utworzony.
   
    ![dodawanie odwołania](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/add-reference.png)
   
    Dodanie odwołania powoduje połączenie projektu aplikacji internetowej z projektem grupy zasobów i automatyczne ustawienie trzech głównych właściwości. Te właściwości będą wyświetlane w oknie **Właściwości** odwołania.
   
      ![wyświetlanie odwołania](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/see-reference.png)
   
    Właściwości są następujące:
   
   * Właściwość **Additional Properties** zawiera tymczasową lokalizację pakietu wdrożeniowego, który zostanie przekazany do usługi Azure Storage. Zanotuj folder (ExampleApp) i plik (package.zip). Musisz znać te wartości, ponieważ są one podawane jako parametry podczas wdrażania aplikacji. 
   * Właściwość **Include File Path** zawiera ścieżkę, w której zostanie utworzony pakiet. Właściwość **Include Targets** zawiera polecenie, które zostanie wykonane podczas wdrażania. 
   * Wartość domyślna właściwości **Build;Package** umożliwia skompilowanie i utworzenie pakietu wdrożeniowego (pakiet.zip) podczas wdrażania.  
     
     Profil publikowania nie jest wymagany, ponieważ wdrożenie pobiera niezbędne informacje z właściwości w celu utworzenia pakietu.
7. Wróć do pliku WebSiteSQLDatabase.json i dodaj zasób do szablonu.
   
    ![dodawanie zasobu](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/add-resource-2.png)
8. Tym razem wybierz pozycję **Web Deploy dla usługi Web Apps**. 
   
    ![dodawanie narzędzia web deploy](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/add-web-deploy.png)
9. Ponownie wdróż projekt grupy zasobów w grupie zasobów. Tym razem występuje kilka nowych parametrów. Nie musisz podawać wartości parametrów **_artifactsLocation** ani **_artifactsLocationSasToken**, ponieważ program Visual Studio generuje je automatycznie. Musisz jednak ustawić folder i nazwę pliku na ścieżkę zawierającą pakiet wdrożeniowy (oznaczoną jako **ExampleAppPackageFolder** i **ExampleAppPackageFileName** na poniższej ilustracji). Podaj wartości zanotowane wcześniej z właściwości odwołania (**ExampleApp** i **package.zip**).
   
    ![dodawanie narzędzia web deploy](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/set-new-parameters.png)
   
    Jako **Konto magazynu artefaktów** wybierz konto wdrożone z tą grupą zasobów.
10. Po zakończeniu wdrażania wybierz aplikację internetową w witrynie portalu. Wybierz adres URL, aby przejść do lokacji.
    
     ![przeglądanie lokacji](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/browse-site.png)
11. Zobaczysz, że domyślna aplikacja ASP.NET została pomyślnie wdrożona.
    
     ![wyświetlanie wdrożonej aplikacji](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/show-deployed-app.png)

## <a name="add-an-operations-dashboard-to-your-deployment"></a>Dodawanie pulpitu nawigacyjnego operacji do wdrożenia
Możesz korzystać nie tylko z zasobów dostępnych z poziomu interfejsu programu Visual Studio. Można dostosować wdrożenie, dodając do szablonu zasoby niestandardowe. Aby zobaczyć, jak działa dodawanie zasobu, dodasz operacyjny pulpit nawigacyjny w celu zarządzania wdrożonymi zasobami.

1. Otwórz plik WebsiteSqlDeploy.json i dodaj następujący kod JSON po zasobie konta magazynu, ale przed zamykającym znakiem `]` sekcji zasobów.

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

2. Ponownie wdróż grupę zasobów. Spójrz na pulpit nawigacyjny w witrynie Azure Portal. Zobaczysz, że udostępniony pulpit nawigacyjny został dodany do listy wybranych elementów.

   ![Niestandardowy pulpit nawigacyjny](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/view-custom-dashboards.png)

3. Wybierz ten pulpit nawigacyjny.

   ![Niestandardowy pulpit nawigacyjny](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/Ops-DemoSiteGroup-dashboard.png)

Możesz zarządzać dostępem do pulpitu nawigacyjnego, korzystając z grup kontroli dostępu opartych na rolach. Po wdrożeniu pulpitu nawigacyjnego możesz też dostosowywać jego wygląd. Jeśli jednak ponownie wdrożysz grupę zasobów, pulpit nawigacyjny zostanie zresetowany do domyślnego stanu określonego w szablonie. Aby uzyskać więcej informacji na temat tworzenia pulpitów nawigacyjnych, zobacz [Programmatically create Azure Dashboards (Programowe tworzenie pulpitów nawigacyjnych platformy Azure)](../azure-portal/azure-portal-dashboards-create-programmatically.md).

## <a name="next-steps"></a>Kolejne kroki

W tym przewodniku Szybki start dowiesz się, jak tworzyć i wdrażać szablony przy użyciu programu Visual Studio. W kolejnym samouczku dowiesz się, jak znaleźć informacje w dokumentacji szablonów, aby utworzyć zaszyfrowane konto usługi Azure Storage.

> [!div class="nextstepaction"]
> [Tworzenie zaszyfrowanego konta magazynu](./resource-manager-tutorial-create-encrypted-storage-accounts.md)
