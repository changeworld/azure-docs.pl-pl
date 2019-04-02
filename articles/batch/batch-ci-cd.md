---
title: Potoki usługi Azure umożliwia tworzenie i wdrażanie rozwiązań HPC — w usłudze Azure Batch | Dokumentacja firmy Microsoft
description: Dowiedz się, jak wdrożyć potok kompilacji/wydania dla aplikacji HPC działającej w usłudze Azure Batch.
author: christianreddington
ms.author: chredd
ms.date: 03/28/2019
ms.topic: conceptual
services: batch
ms.openlocfilehash: 1e3b2d42ae390b9eb755e568b82fc1a90e0eae6a
ms.sourcegitcommit: 3341598aebf02bf45a2393c06b136f8627c2a7b8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/01/2019
ms.locfileid: "58807049"
---
# <a name="use-azure-pipelines-to-build-and-deploy-hpc-solutions"></a>Potoki usługi Azure umożliwia tworzenie i wdrażanie rozwiązań HPC

Usługom DevOps platformy Azure zapewniają szeroką gamę narzędzi używanych przez zespoły deweloperów, podczas tworzenia niestandardowych aplikacji. Narzędzi dostarczanych przez DevOps platformy Azure można przekształcić automatycznego kompilowania i testowania rozwiązań obliczeniowych o wysokiej wydajności. W tym artykule przedstawiono sposób konfigurowania ciągłej integracji (CI) i ciągłe wdrażanie (CD) przy użyciu potoki Azure pod kątem wysokiej wydajności obliczeń rozwiązanie wdrożone w usłudze Azure Batch.

Potoki usługi Azure udostępnia szereg nowoczesnych procesów ciągłej integracji/ciągłego wdrażania, tworzenia, wdrażania, testowania i monitorowania oprogramowania. Te procesy przyspieszenie dostarczania oprogramowania, dzięki czemu możesz skupić się na kodzie, a nie obsługuje infrastrukturę i operacje.

## <a name="create-an-azure-pipeline"></a>Instrukcje tworzenia potoku usługi Azure

W tym przykładzie firma Microsoft będzie tworzenie kompilacji potoku wydania i aby wdrożyć infrastrukturę usługi Azure Batch i wersji pakietu aplikacji. Przy założeniu, że kod jest opracowany lokalnie, jest to przepływ ogólnego wdrożenia:

![Diagram przedstawiający przepływu wdrożenia w nasz potok](media/batch-ci-cd/DeploymentFlow.png)

### <a name="setup"></a>Konfigurowanie

Wykonaj kroki opisane w tym artykule, musisz organizacji DevOps platformy Azure oraz projektu zespołowego.

* [Utwórz organizację DevOps platformy Azure](https://docs.microsoft.com/azure/devops/organizations/accounts/create-organization?view=azure-devops)
* [Tworzenie projektu w DevOps platformy Azure](https://docs.microsoft.com/azure/devops/organizations/projects/create-project?view=azure-devops)

### <a name="source-control-for-your-environment"></a>Kontrola źródła dla środowiska

Kontrola źródła pozwala zespołom na śledzenie zmiany wprowadzone do kodu i sprawdź poprzednie wersje kodu.

Zazwyczaj kontroli źródła jest traktować ręcznie — dostępnych z kodem oprogramowania. Co myślisz o podstawowej infrastruktury? Ten sposób dotarliśmy do infrastruktury jako kodu, której używamy szablonów usługi Azure Resource Manager lub inne alternatywy dla typu open source do deklaratywne zdefiniowanie naszych podstawowej infrastruktury.

W tym przykładzie intensywnie korzysta z wielu szablonów usługi Resource Manager (dokumentów JSON) i istniejące pliki binarne. Możesz skopiować te przykłady w repozytorium i odesłać je do metodyki DevOps platformy Azure.

Struktura codebase używane w tym przykładzie jest podobny do następującego;

* **Szablonów arm** folderu zawierającego wiele szablonów usługi Azure Resource Manager. Szablony są szczegółowo opisane w tym artykule.
* A **aplikacja kliencka** folderu, który jest kopią elementu [usługi Azure Batch .NET pliku przetwarzania za pomocą narzędzia ffmpeg](https://github.com/Azure-Samples/batch-dotnet-ffmpeg-tutorial) próbki. Nie jest to potrzebne w tym artykule.
* **Aplikację hpc** folder, który jest Windows 64-bitowych wersji [narzędzia ffmpeg 3.4](https://ffmpeg.zeranoe.com/builds/win64/static/ffmpeg-3.4-win64-static.zip).
* A **potoki** folderu. To znajduje się plik YAML konspekt nasz proces kompilacji. Zostało to omówione w artykule.

W tej sekcji założono, że czytelnik zna wersji kontroli i projektowania szablonów usługi Resource Manager. Jeśli nie znasz tych pojęć, zobacz następujące strony, aby uzyskać więcej informacji.

* [Co to jest kontrola źródła?](https://docs.microsoft.com/azure/devops/user-guide/source-control?view=azure-devops)
* [Understand the structure and syntax of Azure Resource Manager Templates (Omówienie struktury i składni szablonów usługi Azure Resource Manager)](../azure-resource-manager/resource-group-authoring-templates.md)

#### <a name="azure-resource-manager-templates"></a>Szablony usługi Azure Resource Manager

W tym przykładzie korzysta z wielu szablonów usługi Resource Manager, aby wdrożyć Nasze rozwiązanie. Aby to zrobić, używamy wiele szablonów możliwości (podobne do jednostki lub moduły), które implementują konkretne funkcje. Możemy również użyć szablonu rozwiązania end-to-end, która jest odpowiedzialna za te możliwości ze sobą. Istnieje kilka zalet tego podejścia:

* Podstawowy szablony możliwości mogą być indywidualnie jednostki przetestowane.
* Podstawowych szablonów funkcji mogą być definiowane jako standard wewnątrz organizacji, a następnie można użyć ponownie w przypadku wielu rozwiązań.

W tym przykładzie ma szablon rozwiązania end-to-end (deployment.json), który jest wdrażany trzy szablony. Szablony podstawowej są szablony możliwości odpowiedzialnego za wdrożenie określonych aspektów rozwiązania.

![Przykład struktury szablonu połączone za pomocą szablonów usługi Azure Resource Manager](media/batch-ci-cd/ARMTemplateHierarchy.png)

Pierwszy szablon, który omówimy jest dla konta usługi Azure Storage. Nasze rozwiązanie wymaga konta magazynu, aby wdrożyć aplikację na nasze konto usługi Batch. Warto pamiętać o [Podręcznik informacyjny szablonu usługi Resource Manager dla typów zasobów Microsoft.Storage](https://docs.microsoft.com/azure/templates/microsoft.storage/allversions) podczas tworzenia szablonów usługi Resource Manager dla kont magazynu.

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

Następnie przyjrzymy się szablon Azure konto usługi Batch. Konto usługi Azure Batch działa jako platformy do uruchamiania wielu aplikacji w pulach (grupowania maszyn). Warto pamiętać o [Podręcznik informacyjny szablonu usługi Resource Manager dla typów zasobów Microsoft.Batch](https://docs.microsoft.com/azure/templates/microsoft.batch/allversions) podczas tworzenia szablonów usługi Resource Manager dla konta usługi Batch.

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

Kolejny szablon przedstawiono przykład tworzenia puli usługi Azure Batch (maszyn wewnętrznej bazy danych do przetwarzania naszych aplikacji). Warto pamiętać o [Podręcznik informacyjny szablonu usługi Resource Manager dla typów zasobów Microsoft.Batch](https://docs.microsoft.com/azure/templates/microsoft.batch/allversions) podczas tworzenia szablonów usługi Resource Manager dla pul konta usługi Batch.

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

Na koniec mamy szablonu, która działa podobnie do programu orchestrator. Ten szablon jest odpowiedzialny za wdrażanie szablonów funkcji.

Możesz także dowiedzieć się więcej o [tworzenia połączonymi szablonami usługi Azure Resource Manager](../azure-resource-manager/resource-manager-tutorial-create-linked-templates.md) w oddzielnym artykule.

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

#### <a name="the-hpc-solution"></a>Rozwiązania HPC

Infrastruktury i oprogramowania można zdefiniowane jako kod i wspólnie przechowywane w tym samym repozytorium.

W przypadku tego rozwiązania ffmpeg jest używana jako pakiet aplikacji. Można pobrać pakietu ffmpeg [tutaj](https://ffmpeg.zeranoe.com/builds/win64/static/ffmpeg-3.4-win64-static.zip).

![Przykład struktury repozytorium Git](media/batch-ci-cd/git-repository.jpg)

Istnieją cztery główne sekcje z tym certyfikatem:

* **Szablonów arm** folder przechowujący naszej infrastruktury jako kodu
* **Aplikację hpc** folder, który zawiera pliki binarne ffmpeg
* **Potoki** folder, który zawiera definicję dla nasz potok kompilacji.
* **Opcjonalnie**: **Aplikacja kliencka** folder, w której będą przechowywane kod dla aplikacji .NET. Nie użyjemy tych w przykładzie, ale w własny projekt, możesz chcieć wykonać uruchomień aplikacji usługi Batch HPC za pomocą aplikacji klienckiej.

> [!NOTE]
> Jest to tylko jeden przykład struktury w bazie kodu. To podejście jest używany w celu wykazania, że aplikacji, infrastruktury i kodem potoku są przechowywane w tym samym repozytorium.

Teraz, gdy kod źródłowy jest skonfigurowana, możemy rozpocząć pierwsza kompilacja.

## <a name="continuous-integration"></a>Integracja ciągła

[Potoki usługi Azure](https://docs.microsoft.com/azure/devops/pipelines/get-started/?view=azure-devops), w ramach usługi DevOps platformy Azure, pomaga zaimplementowania potoku kompilacji, testowania i wdrażania aplikacji.

Podczas tego etapu potoku są zazwyczaj testy do weryfikacji kodu i kompilacji odpowiednie elementy oprogramowania. Liczbę i typy testów i wszelkie dodatkowe zadania, które uruchamiasz będzie zależeć od szersze kompilacji i wydania strategii.

## <a name="preparing-the-hpc-application"></a>Przygotowywanie aplikacji HPC

W tym przykładzie skupimy się na **aplikację hpc** folderu. **Aplikację hpc** folder to oprogramowanie ffmpeg, które będzie uruchamiać z poziomu konta usługi Azure Batch.

1. Przejdź do sekcji kompilacje potoków usługi Azure w Twojej organizacji DevOps platformy Azure. Tworzenie **nowy potok**.

    ![Utwórz nowy potok kompilacji](media/batch-ci-cd/new-build-pipeline.jpg)

1. Masz dwie opcje, aby utworzyć potok kompilacji:

    a. [Za pomocą projektanta wizualnego](https://docs.microsoft.com/azure/devops/pipelines/get-started-designer?view=azure-devops&tabs=new-nav). Aby użyć tej opcji, kliknij "Przy użyciu projektanta wizualnego" **nowy potok** strony.

    b. [Za pomocą kodu YAML kompilacje](https://docs.microsoft.com/azure/devops/pipelines/get-started-yaml?view=azure-devops). Można utworzyć nowy potok YAML, klikając repozytoriów platformy Azure lub opcji GitHub na nowej stronie potoku. Alternatywnie można przechowywać w poniższym przykładzie w kontroli źródła i odwoływać się do istniejącego pliku YAML, klikając wizualnego projektanta, a następnie za pomocą szablonu YAML.

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

1. Gdy kompilacja jest skonfigurowany zgodnie z potrzebami, wybierz **Zapisz k & Olejką**. W przypadku ciągłej integracji, włączone (w **wyzwalaczy** sekcji), kompilacja wyzwoli automatycznie po wysłaniu nowego zatwierdzenia w repozytorium, spełniające warunki ustawiony w kompilacji.

    ![Przykład istniejący potok kompilacji](media/batch-ci-cd/existing-build-pipeline.jpg)

1. Wyświetl aktualizacje na bieżąco o postępie kompilacji w DevOps platformy Azure, przechodząc do **kompilacji** sekcji potoki usługi Azure. Wybierz odpowiednią kompilację z definicji kompilacji.

    ![Wyświetlanie danych wyjściowych na żywo z kompilacji](media/batch-ci-cd/Build-1.jpg)

> [!NOTE]
> Jeśli używasz aplikacji klienckiej wykonywanie aplikacji usługi Batch HPC, musisz utworzyć definicję kompilacji oddzielnych dla tej aplikacji. Można znaleźć szereg przewodniki z instrukcjami w [potoki Azure](https://docs.microsoft.com/azure/devops/pipelines/get-started/index?view=azure-devops) dokumentacji.

## <a name="continuous-deployment"></a>Ciągłe wdrażanie

Potoki usługi Azure, która jest również używane do wdrażania aplikacji i bazowej infrastruktury. [Potoki wersji](https://docs.microsoft.com/azure/devops/pipelines/release/what-is-release-management?view=azure-devops) jest składnikiem, który umożliwia ciągłe wdrażanie i automatyzuje proces tworzenia wydań.

### <a name="deploying-your-application-and-underlying-infrastructure"></a>Wdrażanie aplikacji i podstawową infrastrukturę

Istnieje szereg kroków związanych z wdrażaniem infrastruktury. Jak użyliśmy [połączone szablony](../azure-resource-manager/resource-group-linked-templates.md), te szablony musi być dostępny z publicznym punktem końcowym (HTTP lub HTTPS). Może to być repozytorium w serwisie GitHub, lub konto usługi Azure Blob Storage lub innej lokalizacji magazynu. Artefakty przekazany szablon można pozostaną bezpieczne, jak mogą być przechowywane w trybie prywatnym, ale uzyskać dostęp przy użyciu pewnego rodzaju token (SAS) sygnatury dostępu współdzielonego. Poniższy przykład pokazuje, jak wdrożyć infrastrukturę za pomocą szablonów z obiektu blob usługi Azure Storage.

1. Tworzenie **nową definicję wydania**i wybierz pustą definicję. Następnie należy zmienić nazwę nowo utworzonego środowiska na coś, co jest istotne dla nasz potok.

    ![Wersja początkowa potoku](media/batch-ci-cd/Release-0.jpg)

1. Utwórz zależność na potoku kompilacji, aby pobrać dane wyjściowe dla naszej aplikacji HPC.

    > [!NOTE]
    > Ponownie, należy pamiętać, **Alias źródła**, jak to konieczne, gdy zadania są tworzone wewnątrz definicji wydania.

    ![Utwórz łącze artefaktu do HPCApplicationPackage w potoku odpowiednią kompilację](media/batch-ci-cd/Release-1.jpg)

1. Utworzenie łącza do innego artefaktu, tym razem repozytorium platformy Azure. Jest to wymagane do dostępu do szablonów usługi Resource Manager, przechowywane w repozytorium. Zgodnie z szablonów usługi Resource Manager nie jest wymagane kompilacji, nie trzeba przekazać je za pomocą potoku kompilacji.

    > [!NOTE]
    > Ponownie, należy pamiętać, **Alias źródła**, jak to konieczne, gdy zadania są tworzone wewnątrz definicji wydania.

    ![Utwórz łącze artefaktu do repozytoriów platformy Azure](media/batch-ci-cd/Release-2.jpg)

1. Przejdź do **zmienne** sekcji. Zaleca się tworzenie liczbę zmiennych w potoku, nie są wprowadzania tych samych informacji w wielu zadań. Są to zmienne używane w tym przykładzie i ich wpływ na wdrożenie.

    * **applicationStorageAccountName**: Nazwa konta magazynu do przechowywania plików binarnych aplikacji HPC
    * **batchAccountApplicationName**: Nazwa aplikacji w ramach konta usługi Batch Azure
    * **batchAccountName**: Nazwa konta usługi Azure Batch
    * **batchAccountPoolName**: Nazwa puli maszyn wirtualnych, wykonując przetwarzanie
    * **batchApplicationId**: Unikatowy identyfikator aplikacji usługi Azure Batch
    * **batchApplicationVersion**: Semantyczne wersję aplikacji usługi batch (pliki binarne ffmpeg)
    * **Lokalizacja**: Lokalizacja dla zasobów platformy Azure do wdrożenia
    * **resourceGroupName**: Nazwa grupy zasobów, które ma zostać utworzony i wdrożonym zasobów
    * **storageAccountName**: Nazwa konta magazynu do przechowywania połączonymi szablonami usługi Resource Manager

    ![Przykład zmienne ustawione dla wersji Azure potoków](media/batch-ci-cd/Release-4.jpg)

1. Przejdź do zadania w środowisku deweloperskim. W poniższych migawki, możesz zobaczyć sześciu zadań podrzędnych. Te zadania zostaną: pobieranie plików zip narzędzia ffmpeg, wdrożyć konto magazynu, aby hostować zagnieżdżonych szablonów usługi Resource Manager, skopiuj te szablony usługi Resource Manager do konta magazynu, wdrażanie konta usługi batch i wymagane zależności, tworzenie aplikacji w Konto usługi Azure Batch i przekazywanie pakietu aplikacji do konta usługi Azure Batch.

    ![Przykładowe zadania przy użyciu aplikacji HPC w usłudze Azure Batch](media/batch-ci-cd/Release-3.jpg)

1. Dodaj **pobieranie artefaktu potoku (wersja zapoznawcza)** zadań, a następnie ustaw następujące właściwości:
    * **Nazwa wyświetlana:** Pobierz ApplicationPackage do agenta
    * **Nazwa artefaktu do pobrania:** aplikację hpc
    * **Ścieżka pobierania do**: $(System.DefaultWorkingDirectory)

1. Utwórz konto magazynu do przechowywania artefaktów. Istniejące konto magazynu z rozwiązania może być używane, ale dla próbki niezależna i izolacji zawartości, firma Microsoft wprowadza konta dedykowanych dla magazynu dla naszych artefaktów (w szczególności szablonów usługi Resource Manager).

    Dodaj **wdrożenie grupy zasobów Azure** zadań, a następnie ustaw następujące właściwości:
    * **Nazwa wyświetlana:** Wdrażanie konta magazynu dla szablonów usługi Resource Manager
    * **Subskrypcja platformy Azure:** Wybierz odpowiednią subskrypcję platformy Azure
    * **Akcja**: Utwórz lub zaktualizuj grupę zasobów
    * **Resource Group**: $(resourceGroupName)
    * **Lokalizacja**: $(location)
    * **Szablon**: $(System.ArtifactsDirectory)/**{YourAzureRepoArtifactSourceAlias}**/arm-templates/storageAccount.json
    * **Przesłanianie parametrów szablonu**: $(storageAccountName) - nazwa konta

1. Przekaż artefakty z kontroli źródła do konta magazynu. Brak zadań potoku usługi Azure, które należy wykonać. W ramach tego zadania adres URL kontenera konta magazynu i tokenu sygnatury dostępu Współdzielonego może zostać zwrócone do zmiennej w potokach platformy Azure. Oznacza to, że może zostać użyte w tej fazie agenta.

    Dodaj **kopiowania plików na platformę Azure** zadań, a następnie ustaw następujące właściwości:
    * **Źródło:** $(System.ArtifactsDirectory)/**{YourAzureRepoArtifactSourceAlias}**/arm-templates /
    * **Typ połączenia usługi Azure**: Azure Resource Manager
    * **Subskrypcja platformy Azure:** Wybierz odpowiednią subskrypcję platformy Azure
    * **Typ docelowy**: Obiekt bob Azure
    * **Konto magazynu RM**: $(storageAccountName)
    * **Nazwa kontenera**: szablony
    * **Identyfikator URI kontenera magazynu**: templateContainerUri
    * **Tokenu sygnatury dostępu Współdzielonego kontenera magazynu**: templateContainerSasToken

1. Wdrażanie szablonu programu orchestrator. Odwołaj szablonu programu orchestrator z wcześniej, można zauważyć, że wystąpiły parametry URL kontenera konta magazynu, oprócz tokenu sygnatury dostępu Współdzielonego. Należy zauważyć, albo są przechowywane w zmiennych części definicji wydania zmienne wymagane w szablonie usługi Resource Manager lub zostały ustawione z innego zadania potoki usługi Azure (na przykład część zadania kopiowania obiektów Blob platformy Azure).

    Dodaj **wdrożenie grupy zasobów Azure** zadań, a następnie ustaw następujące właściwości:
    * **Nazwa wyświetlana:** Wdrażanie usługi Azure Batch
    * **Subskrypcja platformy Azure:** Wybierz odpowiednią subskrypcję platformy Azure
    * **Akcja**: Utwórz lub zaktualizuj grupę zasobów
    * **Resource Group**: $(resourceGroupName)
    * **Lokalizacja**: $(location)
    * **Template**: $(System.ArtifactsDirectory)/**{YourAzureRepoArtifactSourceAlias}**/arm-templates/deployment.json
    * **Przesłanianie parametrów szablonu**: ```-templateContainerUri $(templateContainerUri) -templateContainerSasToken $(templateContainerSasToken) -batchAccountName $(batchAccountName) -batchAccountPoolName $(batchAccountPoolName) -applicationStorageAccountName $(applicationStorageAccountName)```

Powszechną praktyką jest używanie zadań usługi Azure Key Vault. Jeśli nazwa główna usługi (połączenie z subskrypcją platformy Azure) zawiera odpowiedni dostęp zasad zabezpieczeń ustawionych, można pobrać Wpisy tajne z usługi Azure Key Vault i być używane jako zmienne w potoku. Nazwa wpisu tajnego zostaną ustawione przy użyciu skojarzona wartość. Na przykład klucz tajny sshPassword może odwoływać się za pomocą $(sshPassword) w definicji wydania.

1. Następne kroki wywołać wiersza polecenia platformy Azure. Pierwszy jest używany do tworzenia aplikacji w usłudze Azure Batch. i przekaż skojarzonych pakietów.

    Dodaj **wiersza polecenia platformy Azure** zadań, a następnie ustaw następujące właściwości:
    * **Nazwa wyświetlana:** Tworzenie aplikacji w usłudze Azure konta usługi Batch
    * **Subskrypcja platformy Azure:** Wybierz odpowiednią subskrypcję platformy Azure
    * **Lokalizację skryptu**: Wykonanie wbudowanego skryptu
    * **Wykonanie wbudowanego skryptu**: ```az batch application create --application-id $(batchApplicationId) --name $(batchAccountName) --resource-group $(resourceGroupName)```

1. Drugi krok jest używany do przekazywania skojarzonych pakietów do aplikacji. W naszym przypadku pliki narzędzia ffmpeg.

    Dodaj **wiersza polecenia platformy Azure** zadań, a następnie ustaw następujące właściwości:
    * **Nazwa wyświetlana:** Przekaż pakiet do konta usługi Azure Batch
    * **Subskrypcja platformy Azure:** Wybierz odpowiednią subskrypcję platformy Azure
    * **Lokalizację skryptu**: Wykonanie wbudowanego skryptu
    * **Wykonanie wbudowanego skryptu**: ```az batch application package create --application-id $(batchApplicationId)  --name $(batchAccountName)  --resource-group $(resourceGroupName) --version $(batchApplicationVersion) --package-file=$(System.DefaultWorkingDirectory)/$(Release.Artifacts.{YourBuildArtifactSourceAlias}.BuildId).zip```

    > [!NOTE]
    > Numer wersji pakietu aplikacji ustawiono zmienną. Ta opcja jest przydatna, jeśli zastąpienie poprzedniej wersji pakietu Ci odpowiada, a użytkownik chce ręcznie sterować numer wersji pakietu wypchnięte do usługi Azure Batch.

1. Tworzenie nowej wersji, wybierając **wydania > Tworzenie nowej wersji**. Po wyzwoleniu wybierz łącze do swojej nowej wersji, aby wyświetlić stan.

1. Na żywo dane wyjściowe z agenta można wyświetlić, wybierając **dzienniki** przycisku poniżej środowiska.

    ![Wyświetlanie stanu wydania](media/batch-ci-cd/Release-5.jpg)

### <a name="testing-the-environment"></a>Testowanie w środowisku

Po skonfigurowaniu środowiska upewnij się, że można pomyślnie ukończyć następujące testy.

Nawiązać nowe konto usługi Batch Azure przy użyciu wiersza polecenia platformy Azure, w wierszu polecenia programu PowerShell.

* Zaloguj się do konta platformy Azure przy użyciu `az login` i postępuj zgodnie z instrukcjami w celu uwierzytelnienia.
* Teraz można uwierzytelniać konta usługi Batch: `az batch account login -g <resourceGroup> -n <batchAccount>`

#### <a name="list-the-available-applications"></a>Lista dostępnych aplikacji

```azurecli
az batch application list -g <resourcegroup> -n <batchaccountname>
```

#### <a name="check-the-pool-is-valid"></a>Sprawdź, czy pula jest nieprawidłowy

```azurecli
az batch pool list
```

Zanotuj wartość ustawienia `currentDedicatedNodes` z danych wyjściowych tego polecenia. Ta wartość jest dostosowywana do następnego testu.

#### <a name="resize-the-pool"></a>Zmiana rozmiaru puli

Zmień rozmiar puli, dzięki czemu są dostępne dla zadań i zadań testowania węzłów obliczeniowych, skontaktuj się z polecenie listy puli, aby wyświetlić bieżący stan do momentu zmiany rozmiaru zostało ukończone i ma dostępnych węzłów

```azurecli
az batch pool resize --pool-id <poolname> --target-dedicated-nodes 4
```

## <a name="next-steps"></a>Kolejne kroki

Oprócz tego artykułu istnieją dwa samouczków, które wykorzystują narzędzie ffmpeg, przy użyciu platformy .NET i Python. Zobacz te samouczki, aby uzyskać więcej informacji na temat sposobu interakcji z kontem usługi Batch za pomocą prostej aplikacji.

* [Uruchamianie równoległego obciążenia w usłudze Azure Batch przy użyciu interfejsu API języka Python](tutorial-parallel-python.md)
* [Uruchamianie równoległego obciążenia w usłudze Azure Batch przy użyciu interfejsu API platformy .NET](tutorial-parallel-dotnet.md)
