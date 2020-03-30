---
title: Tworzenie & wdrażania rozwiązań HPC — usługa Azure Batch za pomocą potoków platformy Azure
description: Dowiedz się, jak wdrożyć potok kompilacji/wydania dla aplikacji HPC uruchomionej w usłudze Azure Batch.
author: chrisreddington
ms.author: chredd
ms.date: 03/28/2019
ms.topic: conceptual
ms.custom: fasttrack-new
services: batch
ms.service: batch
ms.openlocfilehash: 50cb711dfd16c2a8718d13ba9255ace1e7e3e26d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79533134"
---
# <a name="use-azure-pipelines-to-build-and-deploy-hpc-solutions"></a>Tworzenie i wdrażanie rozwiązań HPC za pomocą usługi Azure Pipelines

Usługi Azure DevOps zapewniają szereg narzędzi używanych przez zespoły deweloperskie podczas tworzenia aplikacji niestandardowej. Narzędzia dostarczane przez platformę Azure DevOps mogą przekładać się na automatyczne tworzenie i testowanie wysokowydajnych rozwiązań obliczeniowych. W tym artykule pokazano, jak skonfigurować ciągłą integrację (CI) i ciągłe wdrażanie (CD) przy użyciu usługi Azure Pipelines dla rozwiązania obliczeniowego o wysokiej wydajności wdrożonego w usłudze Azure Batch.

Usługa Azure Pipelines oferuje szereg nowoczesnych procesów ciągłej integracji/ciągłego wdrażania do tworzenia, wdrażania, testowania i monitorowania oprogramowania. Procesy te przyspieszają dostarczanie oprogramowania, co pozwala skupić się na kodzie, a nie na infrastrukturze i operacjach.

## <a name="create-an-azure-pipeline"></a>Tworzenie potoku platformy Azure

W tym przykładzie utworzymy potok kompilacji i wydania, aby wdrożyć infrastrukturę usługi Azure Batch i zwolnić pakiet aplikacji. Zakładając, że kod jest rozwijany lokalnie, jest to ogólny przepływ wdrażania:

![Diagram przedstawiający przepływ wdrożenia w naszym potoku](media/batch-ci-cd/DeploymentFlow.png)

### <a name="setup"></a>Konfiguracja

Aby wykonać kroki opisane w tym artykule, potrzebujesz organizacji Azure DevOps i projektu zespołowego.

* [Tworzenie organizacji devops platformy Azure](https://docs.microsoft.com/azure/devops/organizations/accounts/create-organization?view=azure-devops)
* [Tworzenie projektu w usłudze Azure DevOps](https://docs.microsoft.com/azure/devops/organizations/projects/create-project?view=azure-devops)

### <a name="source-control-for-your-environment"></a>Kontrola źródła dla twojego środowiska

Kontrola źródła umożliwia zespołom śledzenie zmian wprowadzonych w bazie kodu i sprawdzanie poprzednich wersji kodu.

Zazwyczaj kontrola źródła jest myśl o ręka w rękę z kodem oprogramowania. Jak o podstawowej infrastruktury? Spowoduje to nam infrastrukturę jako kod, gdzie użyjemy szablonów usługi Azure Resource Manager lub innych alternatyw typu open source do deklaratywnego zdefiniowania naszej podstawowej infrastruktury.

Ten przykład w dużym stopniu opiera się na wielu szablonach Menedżera zasobów (dokumenty JSON) i istniejących plikach binarnych. Można skopiować te przykłady do repozytorium i wypchnąć je do usługi Azure DevOps.

Struktura bazy kodu używana w tym przykładzie przypomina następujące;

* Folder **szablonów ramion,** zawierający wiele szablonów usługi Azure Resource Manager. Szablony są wyjaśnione w tym artykule.
* Folder **aplikacji klienckiej,** który jest kopią usługi Azure Batch .NET File Processing z próbką [ffmpeg.](https://github.com/Azure-Samples/batch-dotnet-ffmpeg-tutorial) Nie jest to potrzebne w tym artykule.
* Folder **aplikacji hpc,** który jest windows 64-bitową wersją [ffmpeg 3.4](https://ffmpeg.zeranoe.com/builds/win64/static/ffmpeg-3.4-win64-static.zip).
* Folder **potoków.** Zawiera plik YAML przedstawiający nasz proces kompilacji. Jest to omówione w artykule.

W tej sekcji przyjęto założenie, że użytkownik jest zaznajomiony z kontrolą wersji i projektowaniem szablonów Menedżera zasobów. Jeśli nie znasz tych pojęć, zobacz następujące strony, aby uzyskać więcej informacji.

* [Co to jest kontrola źródła?](https://docs.microsoft.com/azure/devops/user-guide/source-control?view=azure-devops)
* [Understand the structure and syntax of Azure Resource Manager Templates (Omówienie struktury i składni szablonów usługi Azure Resource Manager)](../azure-resource-manager/templates/template-syntax.md)

#### <a name="azure-resource-manager-templates"></a>Szablony usługi Azure Resource Manager

W tym przykładzie wykorzystuje wiele szablonów Menedżera zasobów, aby wdrożyć nasze rozwiązanie. Aby to zrobić, używamy wielu szablonów możliwości (podobnych do jednostek lub modułów), które implementują określony element funkcjonalności. Używamy również kompleksowego szablonu rozwiązania, który jest odpowiedzialny za połączenie tych podstawowych możliwości. Istnieje kilka korzyści z tego podejścia:

* Podstawowe szablony możliwości mogą być indywidualnie testowane jednostkowo.
* Podstawowe szablony możliwości można zdefiniować jako standard wewnątrz organizacji i być ponownie w wielu rozwiązaniach.

W tym przykładzie istnieje szablon rozwiązania end-to-end (deployment.json), który wdraża trzy szablony. Szablony podstawowe są szablony możliwości, odpowiedzialny za wdrażanie określonego aspektu rozwiązania.

![Przykład połączonej struktury szablonów przy użyciu szablonów usługi Azure Resource Manager](media/batch-ci-cd/ARMTemplateHierarchy.png)

Pierwszy szablon, który będziemy patrzeć na to dla konta usługi Azure Storage. Nasze rozwiązanie wymaga konta magazynu, aby wdrożyć aplikację na naszym koncie wsadowym. Warto zapoznać się z [przewodnikiem od szablonu Menedżera zasobów dla typów zasobów microsoft.storage](https://docs.microsoft.com/azure/templates/microsoft.storage/allversions) podczas tworzenia szablonów Menedżera zasobów dla kont magazynu.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "accountName": {
            "type": "string",
            "metadata": {
                 "description": "Name of the Azure Storage Account"
             }
         }
    },
    "variables": {},
    "resources": [
        {
            "type": "Microsoft.Storage/storageAccounts",
            "name": "[parameters('accountName')]",
            "sku": {
                "name": "Standard_LRS"
            },
            "apiVersion": "2018-02-01",
            "location": "[resourceGroup().location]",
            "properties": {}
        }
    ],
    "outputs": {
        "blobEndpoint": {
          "type": "string",
          "value": "[reference(resourceId('Microsoft.Storage/storageAccounts', parameters('accountName'))).primaryEndpoints.blob]"
        },
        "resourceId": {
          "type": "string",
          "value": "[resourceId('Microsoft.Storage/storageAccounts', parameters('accountName'))]"
        }
    }
}
```

Następnie przyjrzymy się szablonowi konta wsadowego platformy Azure. Konto wsadowe platformy Azure działa jako platforma do uruchamiania wielu aplikacji w pulach (grupowania maszyn). Warto zapoznać się z [przewodnikiem od szablonu Menedżera zasobów dla typów zasobów microsoft.batch](https://docs.microsoft.com/azure/templates/microsoft.batch/allversions) podczas tworzenia szablonów Menedżera zasobów dla kont wsadowych.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "batchAccountName": {
           "type": "string",
           "metadata": {
                "description": "Name of the Azure Batch Account"
            }
        },
        "storageAccountId": {
           "type": "string",
           "metadata": {
                "description": "ID of the Azure Storage Account"
            }
        }
    },
    "variables": {},
    "resources": [
        {
            "name": "[parameters('batchAccountName')]",
            "type": "Microsoft.Batch/batchAccounts",
            "apiVersion": "2017-09-01",
            "location": "[resourceGroup().location]",
            "properties": {
              "poolAllocationMode": "BatchService",
              "autoStorage": {
                  "storageAccountId": "[parameters('storageAccountId')]"
              }
            }
          }
    ],
    "outputs": {}
}
```

Następny szablon przedstawia przykład tworzenia puli wsadowej platformy Azure (komputerów zaplecza do przetwarzania naszych aplikacji). Warto zapoznać się z [przewodnikiem od szablonu Menedżera zasobów dla typów zasobów microsoft.batch](https://docs.microsoft.com/azure/templates/microsoft.batch/allversions) podczas tworzenia szablonów Menedżera zasobów dla pul kont wsadowych.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "batchAccountName": {
           "type": "string",
           "metadata": {
                "description": "Name of the Azure Batch Account"
           }
        },
        "batchAccountPoolName": {
            "type": "string",
            "metadata": {
                 "description": "Name of the Azure Batch Account Pool"
             }
         }
    },
    "variables": {},
    "resources": [
        {
            "name": "[concat(parameters('batchAccountName'),'/', parameters('batchAccountPoolName'))]",
            "type": "Microsoft.Batch/batchAccounts/pools",
            "apiVersion": "2017-09-01",
            "properties": {
                "deploymentConfiguration": {
                    "virtualMachineConfiguration": {
                        "imageReference": {
                            "publisher": "Canonical",
                            "offer": "UbuntuServer",
                            "sku": "18.04-LTS",
                            "version": "latest"
                        },
                        "nodeAgentSkuId": "batch.node.ubuntu 18.04"
                    }
                },
                "vmSize": "Standard_D1_v2"
            }
          }
    ],
    "outputs": {}
}
```

Na koniec mamy szablon, który działa podobnie do koordynatora. Ten szablon jest odpowiedzialny za wdrażanie szablonów możliwości.

Możesz również dowiedzieć się więcej o [tworzeniu połączonych szablonów usługi Azure Resource Manager](../azure-resource-manager/templates/template-tutorial-create-linked-templates.md) w osobnym artykule.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "templateContainerUri": {
           "type": "string",
           "metadata": {
                "description": "URI of the Blob Storage Container containing the Azure Resouce Manager templates"
            }
        },
        "templateContainerSasToken": {
           "type": "string",
           "metadata": {
                "description": "The SAS token of the container containing the Azure Resouce Manager templates"
            }
        },
        "applicationStorageAccountName": {
            "type": "string",
            "metadata": {
                 "description": "Name of the Azure Storage Account"
            }
         },
        "batchAccountName": {
            "type": "string",
            "metadata": {
                 "description": "Name of the Azure Batch Account"
            }
         },
         "batchAccountPoolName": {
             "type": "string",
             "metadata": {
                  "description": "Name of the Azure Batch Account Pool"
              }
          }
    },
    "variables": {},
    "resources": [
        {
            "apiVersion": "2017-05-10",
            "name": "storageAccountDeployment",
            "type": "Microsoft.Resources/deployments",
            "properties": {
                "mode": "Incremental",
                "templateLink": {
                  "uri": "[concat(parameters('templateContainerUri'), '/storageAccount.json', parameters('templateContainerSasToken'))]",
                  "contentVersion": "1.0.0.0"
                },
                "parameters": {
                    "accountName": {"value": "[parameters('applicationStorageAccountName')]"}
                }
            }
        },  
        {
            "apiVersion": "2017-05-10",
            "name": "batchAccountDeployment",
            "type": "Microsoft.Resources/deployments",
            "dependsOn": [
                "storageAccountDeployment"
            ],
            "properties": {
                "mode": "Incremental",
                "templateLink": {
                  "uri": "[concat(parameters('templateContainerUri'), '/batchAccount.json', parameters('templateContainerSasToken'))]",
                  "contentVersion": "1.0.0.0"
                },
                "parameters": {
                    "batchAccountName": {"value": "[parameters('batchAccountName')]"},
                    "storageAccountId": {"value": "[reference('storageAccountDeployment').outputs.resourceId.value]"}
                }
            }
        },
        {
            "apiVersion": "2017-05-10",
            "name": "poolDeployment",
            "type": "Microsoft.Resources/deployments",
            "dependsOn": [
                "batchAccountDeployment"
            ],
            "properties": {
                "mode": "Incremental",
                "templateLink": {
                  "uri": "[concat(parameters('templateContainerUri'), '/batchAccountPool.json', parameters('templateContainerSasToken'))]",
                  "contentVersion": "1.0.0.0"
                },
                "parameters": {
                    "batchAccountName": {"value": "[parameters('batchAccountName')]"},
                    "batchAccountPoolName": {"value": "[parameters('batchAccountPoolName')]"}
                }
            }
        }
    ],
    "outputs": {}
}
```

#### <a name="the-hpc-solution"></a>Rozwiązanie HPC

Infrastruktura i oprogramowanie można zdefiniować jako kod i współlokować w tym samym repozytorium.

W tym rozwiązaniu ffmpeg jest używany jako pakiet aplikacji. Pakiet ffmpeg można pobrać [tutaj](https://ffmpeg.zeranoe.com/builds/win64/static/ffmpeg-3.4-win64-static.zip).

![Przykładowa struktura repozytorium Git](media/batch-ci-cd/git-repository.jpg)

Istnieją cztery główne sekcje tego repozytorium:

* Folder **szablonów ramion,** w których przechowuje się nasza infrastruktura jako kod
* Folder **aplikacji hpc,** który zawiera pliki binarne dla ffmpeg
* Folder **potoków,** który zawiera definicję naszego potoku kompilacji.
* **Opcjonalnie:** Folder **aplikacji klienckiej,** który przechowuje kod dla aplikacji .NET. Nie używamy tego w przykładzie, ale we własnym projekcie można wykonać przebiegi aplikacji wsadowej HPC za pośrednictwem aplikacji klienckiej.

> [!NOTE]
> Jest to tylko jeden przykład struktury do bazy kodu. Takie podejście jest używane do celów wykazania, że aplikacja, infrastruktura i kod potoku są przechowywane w tym samym repozytorium.

Teraz, gdy kod źródłowy jest skonfigurowany, możemy rozpocząć pierwszą kompilację.

## <a name="continuous-integration"></a>Ciągła integracja

[Usługa Azure Pipelines](https://docs.microsoft.com/azure/devops/pipelines/get-started/?view=azure-devops), w ramach usług Azure DevOps Services, ułatwia implementowanie potoku kompilacji, testowania i wdrażania dla aplikacji.

Na tym etapie potoku testy są zazwyczaj uruchamiane w celu sprawdzania poprawności kodu i tworzenia odpowiednich fragmentów oprogramowania. Liczba i typy testów oraz wszelkie dodatkowe zadania, które można uruchomić będzie zależeć od szerszej strategii kompilacji i wydania.

## <a name="preparing-the-hpc-application"></a>Przygotowanie aplikacji HPC

W tym przykładzie skupimy się na folderze **aplikacji hpc.** Folder **aplikacji hpc** to oprogramowanie ffmpeg, które będzie uruchamiane z poziomu konta usługi Azure Batch.

1. Przejdź do sekcji Kompilacje potoków platformy Azure w organizacji Azure DevOps. Utwórz **nowy potok**.

    ![Tworzenie nowego potoku kompilacji](media/batch-ci-cd/new-build-pipeline.jpg)

1. Dostępne są dwie opcje tworzenia potoku kompilacji:

    a. [Korzystanie z programu Visual Designer](https://docs.microsoft.com/azure/devops/pipelines/get-started-designer?view=azure-devops&tabs=new-nav). Aby użyć tej funkcji, kliknij przycisk "Użyj projektanta wizualnego" na stronie **Nowy potok.**

    b. [Korzystanie z kompilacji YAML](https://docs.microsoft.com/azure/devops/pipelines/get-started-yaml?view=azure-devops). Możesz utworzyć nowy potok YAML, klikając opcję Repozytorium platformy Azure lub GitHub na stronie Nowy potok. Alternatywnie można zapisać poniższy przykład w formancie źródłowym i odwołać się do istniejącego pliku YAML, klikając visual designer, a następnie za pomocą szablonu YAML.

    ```yml
    # To publish an application into Azure Batch, we need to
    # first zip the file, and then publish an artifact, so that
    # we can take the necessary steps in our release pipeline.
    steps:
    # First, we Zip up the files required in the Batch Account
    # For this instance, those are the ffmpeg files
    - task: ArchiveFiles@2
      displayName: 'Archive applications'
      inputs:
        rootFolderOrFile: hpc-application
        includeRootFolder: false
        archiveFile: '$(Build.ArtifactStagingDirectory)/package/$(Build.BuildId).zip'
    # Publish that zip file, so that we can use it as part
    # of our Release Pipeline later
    - task: PublishPipelineArtifact@0
      inputs:
        artifactName: 'hpc-application'
        targetPath: '$(Build.ArtifactStagingDirectory)/package'
    ```

1. Po skonfigurowaniu kompilacji w razie potrzeby wybierz pozycję **Zapisz & kolejki**. Jeśli masz włączoną ciągłą integrację (w sekcji **Wyzwalacze),** kompilacja zostanie automatycznie wyzwolona po wprowadzeniu nowego zatwierdzenia do repozytorium, spełniając warunki określone w kompilacji.

    ![Przykład istniejącego potoku kompilacji](media/batch-ci-cd/existing-build-pipeline.jpg)

1. Wyświetlanie aktualizacji na żywo na temat postępu kompilacji w usłudze Azure DevOps przez przejście do sekcji **Kompilacja** potoków platformy Azure. Wybierz odpowiednią kompilację z definicji kompilacji.

    ![Wyświetlanie wyników na żywo z kompilacji](media/batch-ci-cd/Build-1.jpg)

> [!NOTE]
> Jeśli używasz aplikacji klienckiej do wykonania aplikacji wsadowej HPC, należy utworzyć oddzielną definicję kompilacji dla tej aplikacji. W dokumentacji [usługi Azure Pipelines](https://docs.microsoft.com/azure/devops/pipelines/get-started/index?view=azure-devops) można znaleźć wiele poradniki.

## <a name="continuous-deployment"></a>Ciągłe wdrażanie

Usługa Azure Pipelines również używane do wdrażania aplikacji i podstawowej infrastruktury. [Potoki wydania](https://docs.microsoft.com/azure/devops/pipelines/release) to składnik, który umożliwia ciągłe wdrażanie i automatyzuje proces wydawania.

### <a name="deploying-your-application-and-underlying-infrastructure"></a>Wdrażanie aplikacji i podstawowej infrastruktury

Istnieje kilka kroków związanych z wdrażaniem infrastruktury. Ponieważ użyliśmy [połączonych szablonów,](../azure-resource-manager/templates/linked-templates.md)szablony te będą musiały być dostępne z publicznego punktu końcowego (HTTP lub HTTPS). Może to być repozytorium w usłudze GitHub lub konto usługi Azure Blob Storage lub inna lokalizacja magazynu. Artefakty przekazanego szablonu mogą pozostać bezpieczne, ponieważ mogą być przechowywane w trybie prywatnym, ale dostępne przy użyciu jakiejś formy tokenu podpisu dostępu współdzielonego (SAS). W poniższym przykładzie pokazano, jak wdrożyć infrastrukturę z szablonami z obiektu blob usługi Azure Storage.

1. Utwórz **nową definicję wydania**i wybierz pustą definicję. Następnie musimy zmienić nazwę nowo utworzonego środowiska na coś istotnego dla naszego potoku.

    ![Rurociąg wstępnego wydania](media/batch-ci-cd/Release-0.jpg)

1. Utwórz zależność od potoku kompilacji, aby uzyskać dane wyjściowe dla naszej aplikacji HPC.

    > [!NOTE]
    > Po raz kolejny należy zwrócić uwagę **na alias źródłowy**, ponieważ będzie to potrzebne, gdy zadania są tworzone wewnątrz definicji wydania.

    ![Tworzenie łącza artefaktu do pakietu HPCApplicationPackage w odpowiednim potoku kompilacji](media/batch-ci-cd/Release-1.jpg)

1. Utwórz łącze do innego artefaktu, tym razem usługi Azure Repo. Jest to wymagane do uzyskania dostępu do szablonów Menedżera zasobów przechowywanych w repozytorium. Ponieważ szablony Menedżera zasobów nie wymagają kompilacji, nie trzeba wypychać ich przez potok kompilacji.

    > [!NOTE]
    > Po raz kolejny należy zwrócić uwagę **na alias źródłowy**, ponieważ będzie to potrzebne, gdy zadania są tworzone wewnątrz definicji wydania.

    ![Tworzenie łącza artefaktów do repozytorium platformy Azure](media/batch-ci-cd/Release-2.jpg)

1. Przejdź do sekcji **zmiennych.** Zaleca się utworzenie wielu zmiennych w potoku, więc nie są wprowadzania tych samych informacji do wielu zadań. Są to zmienne używane w tym przykładzie i jak wpływają one na wdrożenie.

    * **applicationStorageAccountName**: Nazwa konta pamięci masowej do przechowywania plików binarnych aplikacji HPC
    * **batchAccountApplicationName**: Nazwa aplikacji na koncie wsadowym platformy Azure
    * **batchAccountName**: Nazwa konta wsadowego platformy Azure
    * **batchAccountPoolName**: Nazwa puli maszyn wirtualnych wykonujących przetwarzanie
    * **batchApplicationId:** Unikatowy identyfikator aplikacji usługi Azure Batch
    * **batchApplicationVersion**: Semantyczna wersja aplikacji wsadowej (czyli pliki binarne ffmpeg)
    * **lokalizacja:** Lokalizacja dla zasobów platformy Azure do wdrożenia
    * **resourceGroupName:** Nazwa grupy zasobów, która ma zostać utworzona, i miejsce, w którym zostaną wdrożone zasoby
    * **storageAccountName**: Nazwa konta magazynu do przechowywania połączonych szablonów Menedżera zasobów

    ![Przykład zmiennych ustawionych dla wydania usługi Azure Pipelines](media/batch-ci-cd/Release-4.jpg)

1. Przejdź do zadań w środowisku deweloperskim. W poniższej migawce można zobaczyć sześć zadań. Zadania te: pobieranie spakowanych plików ffmpeg, wdrażanie konta magazynu w celu obsługi zagnieżdżonych szablonów Menedżera zasobów, kopiowanie tych szablonów Menedżera zasobów na konto magazynu, wdrażanie konta wsadowego i wymaganych zależności, tworzenie aplikacji w konto wsadowe platformy Azure i przekaż pakiet aplikacji do konta usługi Azure Batch.

    ![Przykład zadań używanych do zwalniania aplikacji HPC na platformie Azure Batch](media/batch-ci-cd/Release-3.jpg)

1. Dodaj zadanie **Pobierz artefakt potoku (Wersja zapoznawcza)** i ustaw następujące właściwości:
    * **Nazwa wyświetlana:** Pobierz pakiet aplikacji do agenta
    * **Nazwa artefaktu do pobrania:** hpc-application
    * **Ścieżka do pobrania do**: $(System.DefaultWorkingDirectory)

1. Utwórz konto magazynu, aby przechowywać artefakty. Istniejące konto magazynu z rozwiązania może służyć, ale dla samodzielnego przykładu i izolacji zawartości tworzymy dedykowane konto magazynu dla naszych artefaktów (w szczególności szablonów Menedżera zasobów).

    Dodaj zadanie **wdrażania grupy zasobów platformy Azure** i ustaw następujące właściwości:
    * **Nazwa wyświetlana:** Wdrażanie konta magazynu dla szablonów Menedżera zasobów
    * **Subskrypcja platformy Azure:** Wybierz odpowiednią subskrypcję platformy Azure
    * **Akcja**: Tworzenie lub aktualizowanie grupy zasobów
    * **Grupa zasobów:**$(resourceGroupName)
    * **Lokalizacja**: $(lokalizacja)
    * **Szablon:**$(System.ArtifactsDirectory)/**{YourAzureRepoArtifactSourceAlias}**/arm-templates/storageAccount.json
    * **Zastąp parametry szablonu**: -accountName $(storageAccountName)

1. Przekaż artefakty z kontrolki źródłowej na konto magazynu. Istnieje zadanie potoku platformy Azure, aby to wykonać. W ramach tego zadania adres URL kontenera konta magazynu i token sygnatury dostępu Współdzielonego mogą być wyprowadzane do zmiennej w potokach platformy Azure. Oznacza to, że może być ponownie ponownie w całej tej fazie agenta.

    Dodaj zadanie **kopiowania plików platformy Azure** i ustaw następujące właściwości:
    * **Źródło:** $(System.ArtifactsDirectory)/**{YourAzureRepoArtifactSourceAlias}**/arm-templates/
    * **Typ połączenia platformy Azure:** Usługa Azure Resource Manager
    * **Subskrypcja platformy Azure:** Wybierz odpowiednią subskrypcję platformy Azure
    * **Typ miejsca docelowego:** obiekt blob platformy Azure
    * **Konto magazynu RM:**$(storageAccountName)
    * **Nazwa kontenera**: szablony
    * **Identyfikator URI kontenera magazynu**: szablonContainerUri
    * **Token SAS kontenera magazynu:** templateContainerSasToken

1. Wdrażanie szablonu programu orchestrator. Przypomnij szablon aranżatora z wcześniej, można zauważyć, że istnieją parametry dla adresu URL kontenera konta magazynu, oprócz tokenu sygnatury dostępu Współdzielonego. Należy zauważyć, że zmienne wymagane w szablonie Menedżera zasobów są przechowywane w sekcji zmiennych definicji wersji lub zostały ustawione z innego zadania potoków platformy Azure (na przykład część zadania kopiowania obiektów blob platformy Azure).

    Dodaj zadanie **wdrażania grupy zasobów platformy Azure** i ustaw następujące właściwości:
    * **Nazwa wyświetlana:** Wdrażanie usługi Azure Batch
    * **Subskrypcja platformy Azure:** Wybierz odpowiednią subskrypcję platformy Azure
    * **Akcja**: Tworzenie lub aktualizowanie grupy zasobów
    * **Grupa zasobów:**$(resourceGroupName)
    * **Lokalizacja**: $(lokalizacja)
    * **Szablon:**$(System.ArtifactsDirectory)/**{YourAzureRepoArtifactSourceAlias}**/arm-templates/deployment.json
    * **Zastąp parametry szablonu:**```-templateContainerUri $(templateContainerUri) -templateContainerSasToken $(templateContainerSasToken) -batchAccountName $(batchAccountName) -batchAccountPoolName $(batchAccountPoolName) -applicationStorageAccountName $(applicationStorageAccountName)```

Powszechną praktyką jest użycie zadań usługi Azure Key Vault. Jeśli podmiot usługi (połączenie z subskrypcją platformy Azure) ma ustawioną odpowiednią zasadę dostępu, może pobrać wpisy tajne z usługi Azure Key Vault i być używane jako zmienne w potoku. Nazwa klucza tajnego zostanie ustawiona z skojarzoną wartością. Na przykład tajemnica sshPassword może być odwoływana z $(sshPassword) w definicji wydania.

1. Następne kroki wywołać interfejsu wiersza polecenia platformy Azure. Pierwszy jest używany do tworzenia aplikacji w usłudze Azure Batch. i przesyłać powiązane pakiety.

    Dodaj zadanie **interfejsu wiersza polecenia platformy Azure** i ustaw następujące właściwości:
    * **Nazwa wyświetlana:** Tworzenie aplikacji na koncie wsadowym platformy Azure
    * **Subskrypcja platformy Azure:** Wybierz odpowiednią subskrypcję platformy Azure
    * **Lokalizacja skryptu**: Skrypt wbudowany
    * **Skrypt wbudowany:**```az batch application create --application-id $(batchApplicationId) --name $(batchAccountName) --resource-group $(resourceGroupName)```

1. Drugi krok służy do przekazywania skojarzonych pakietów do aplikacji. W naszym przypadku pliki ffmpeg.

    Dodaj zadanie **interfejsu wiersza polecenia platformy Azure** i ustaw następujące właściwości:
    * **Nazwa wyświetlana:** Przekazywanie pakietu na konto wsadowe platformy Azure
    * **Subskrypcja platformy Azure:** Wybierz odpowiednią subskrypcję platformy Azure
    * **Lokalizacja skryptu**: Skrypt wbudowany
    * **Skrypt wbudowany:**```az batch application package create --application-id $(batchApplicationId)  --name $(batchAccountName)  --resource-group $(resourceGroupName) --version $(batchApplicationVersion) --package-file=$(System.DefaultWorkingDirectory)/$(Release.Artifacts.{YourBuildArtifactSourceAlias}.BuildId).zip```

    > [!NOTE]
    > Numer wersji pakietu aplikacji jest ustawiony na zmienną. Jest to wygodne, jeśli zastąpienie poprzednich wersji pakietu działa dla Ciebie i jeśli chcesz ręcznie kontrolować numer wersji pakietu wypchnięte do usługi Azure Batch.

1. Utwórz nową wersję, wybierając **pozycję Zwolnij > Utwórz nową wersję**. Po wyzwoleniu wybierz łącze do nowej wersji, aby wyświetlić stan.

1. Dane wyjściowe na żywo z agenta można wyświetlić, wybierając przycisk **Dzienniki** pod środowiskiem.

    ![Wyświetlanie stanu wydania](media/batch-ci-cd/Release-5.jpg)

### <a name="testing-the-environment"></a>Testowanie środowiska

Po skonfigurowaniu środowiska upewnij się, że następujące testy można pomyślnie ukończyć.

Połącz się z nowym kontem usługi Azure Batch, używając interfejsu wiersza polecenia platformy Azure z wiersza polecenia programu PowerShell.

* Zaloguj się do konta `az login` platformy Azure i postępuj zgodnie z instrukcjami, aby uwierzytelnić.
* Teraz uwierzytelnij konto usługi Batch:`az batch account login -g <resourceGroup> -n <batchAccount>`

#### <a name="list-the-available-applications"></a>Lista dostępnych aplikacji

```azurecli
az batch application list -g <resourcegroup> -n <batchaccountname>
```

#### <a name="check-the-pool-is-valid"></a>Sprawdź, czy pula jest prawidłowa

```azurecli
az batch pool list
```

Należy zwrócić `currentDedicatedNodes` uwagę na wartość z danych wyjściowych tego polecenia. Ta wartość jest korygowana w następnym teście.

#### <a name="resize-the-pool"></a>Zmienić rozmiar puli

Zmiana rozmiaru puli, tak aby dostępne były węzły obliczeniowe do testowania zadań i zadań, sprawdź polecenie listy puli, aby zobaczyć bieżący stan, dopóki zmiana rozmiaru nie zostanie zakończona i dostępne są węzły

```azurecli
az batch pool resize --pool-id <poolname> --target-dedicated-nodes 4
```

## <a name="next-steps"></a>Następne kroki

Oprócz tego artykułu istnieją dwa samouczki, które wykorzystują ffmpeg, przy użyciu .NET i Python. Zobacz te samouczki, aby uzyskać więcej informacji na temat interakcji z kontem usługi Batch za pośrednictwem prostej aplikacji.

* [uruchamianie równoległego obciążenia w usłudze Azure Batch przy użyciu interfejsu API Python](tutorial-parallel-python.md)
* [Uruchamianie równoległego obciążenia w usłudze Azure Batch przy użyciu interfejsu API środowiska .NET](tutorial-parallel-dotnet.md)
