---
title: Ciągła integracja i dostarczanie w Azure Data Factory
description: Dowiedz się, jak korzystać z ciągłej integracji i dostarczania, aby przenosić Data Factory potoków z jednego środowiska (Programowanie, testowanie, produkcja) do innego.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
author: djpmsft
ms.author: daperlov
ms.reviewer: maghan
manager: jroth
ms.topic: conceptual
ms.date: 08/14/2019
ms.openlocfilehash: 7c5c1e91e97087bf28b03629659e5194f67c22b3
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/06/2019
ms.locfileid: "73680029"
---
# <a name="continuous-integration-and-delivery-cicd-in-azure-data-factory"></a>Ciągła integracja i dostarczanie (CI/CD) w Azure Data Factory

## <a name="overview"></a>Omówienie

Ciągła integracja to zwyczajowa metoda testowania każdej zmiany wykonywanej w bazie kodu, jak najszybciej, jak to możliwe. Ciągłe dostarczanie odbywa się przy testowaniu, który jest wykonywany podczas ciągłej integracji i wypycha zmiany do systemu przejściowego lub produkcyjnego.

W Azure Data Factory ciągłej integracji & dostarczanie oznacza przemieszczenie Data Factory potoków z jednego środowiska (opracowywanie, testowanie, produkcja) do innego. Aby przeprowadzić ciągłą integrację & dostarczania, możesz użyć Data Factoryj integracji środowiska użytkownika z szablonami Azure Resource Manager. Data Factory UX może wygenerować szablon Menedżer zasobów z listy rozwijanej **szablon ARM** . Po wybraniu opcji **Eksportuj szablon usługi ARM**w portalu zostanie wygenerowany szablon Menedżer zasobów dla fabryki danych i plik konfiguracji, który zawiera wszystkie parametry połączeń i innych parametrów. Następnie należy utworzyć jeden plik konfiguracji dla każdego środowiska (Programowanie, testowanie, produkcja). Główny plik szablonu Menedżer zasobów pozostaje taki sam dla wszystkich środowisk.

W przypadku wprowadzenia i pokazania tej funkcji na dziewięć minut Obejrzyj następujące wideo:

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Continuous-integration-and-deployment-using-Azure-Data-Factory/player]

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="continuous-integration-lifecycle"></a>Cykl życia ciągłej integracji

Poniżej znajduje się przykładowe Omówienie cyklu ciągłej integracji i dostarczania w usłudze Azure Data Factory skonfigurowanej przy użyciu Azure Repos git. Aby uzyskać więcej informacji na temat konfigurowania repozytorium git, zobacz [Kontrola źródła w Azure Data Factory](source-control.md).

1.  Fabryka danych programistycznych jest tworzona i konfigurowana za pomocą usługi Azure Repos git, gdzie wszyscy deweloperzy mają uprawnienia do tworzenia Data Factory zasobów, takich jak potoki i zestawy danych.

1.  Gdy deweloperzy wprowadzają zmiany w gałęzi funkcji, debugują ich potoki z najnowszymi zmianami. Aby uzyskać więcej informacji na temat debugowania przebiegu potoku, zobacz [iteracyjne programowanie i debugowanie za pomocą Azure Data Factory](iterative-development-debugging.md).

1.  Gdy deweloperzy są zadowoleni ze zmian, tworzą one żądanie ściągnięcia z gałęzi funkcji do głównej lub gałęzi współpracy, aby uzyskać zmiany przeglądane przez elementy równorzędne.

1.  Gdy żądanie ściągnięcia zostanie zatwierdzone, a zmiany zostaną scalone w gałęzi głównej, mogą publikować w fabryce programistycznej.

1.  Gdy zespół jest gotowy do wdrożenia zmian w fabryce testowej, a następnie do fabryki produkcyjnej, eksportuje szablon Menedżer zasobów z gałęzi głównej.

1.  Wyeksportowany szablon Menedżer zasobów zostanie wdrożony z innymi plikami parametrów do fabryki testowej i fabryki produkcyjnej.

## <a name="create-a-resource-manager-template-for-each-environment"></a>Utwórz szablon Menedżer zasobów dla każdego środowiska

Z listy rozwijanej **szablon ARM** wybierz pozycję **Eksportuj szablon ARM** , aby wyeksportować szablon Menedżer zasobów dla fabryki danych w środowisku deweloperskim.

![](media/continuous-integration-deployment/continuous-integration-image1.png)

W obszarze fabryki danych testowych i produkcyjnych wybierz pozycję **Importuj szablon ARM**. Ta akcja spowoduje przejście do Azure Portal, w którym można zaimportować wyeksportowany szablon. Wybierz opcję **Kompiluj własny szablon w edytorze,** aby otworzyć edytor szablonów Menedżer zasobów.

![](media/continuous-integration-deployment/continuous-integration-image3.png) 

Kliknij pozycję **Załaduj plik** i wybierz wygenerowany szablon Menedżer zasobów.

![](media/continuous-integration-deployment/continuous-integration-image4.png)

W okienku ustawienia wprowadź wartości konfiguracyjne, takie jak poświadczenia połączonej usługi. Gdy skończysz, kliknij przycisk **Kup** , aby wdrożyć szablon Menedżer zasobów.

![](media/continuous-integration-deployment/continuous-integration-image5.png)

### <a name="connection-strings"></a>Parametry połączeń

Informacje o sposobie konfigurowania parametrów połączenia można znaleźć w artykule dotyczącym poszczególnych łączników. Na przykład aby uzyskać Azure SQL Database, zobacz [Kopiowanie danych do lub z Azure SQL Database przy użyciu Azure Data Factory](connector-azure-sql-database.md). Aby sprawdzić parametry połączenia, można otworzyć widok kodu dla zasobu w Data Factory środowisku użytkownika. W widoku kodu zostanie usunięta wartość hasła lub klucza konta w parametrach połączenia. Aby otworzyć widok kodu, wybierz ikonę wyróżnioną na poniższym zrzucie ekranu.

![Otwórz widok kodu, aby wyświetlić parametry połączenia](media/continuous-integration-deployment/continuous-integration-codeview.png)

## <a name="automate-continuous-integration-with-azure-pipelines-releases"></a>Automatyzowanie ciągłej integracji z wersjami Azure Pipelines

Poniżej znajduje się przewodnik konfigurowania wersji Azure Pipelines, która automatyzuje wdrażanie fabryki danych w wielu środowiskach.

![Diagram ciągłej integracji z Azure Pipelines](media/continuous-integration-deployment/continuous-integration-image12.png)

### <a name="requirements"></a>Wymagania

-   Subskrypcja platformy Azure połączona z Team Foundation Server lub Azure Repos za pomocą [punktu końcowego usługi Azure Resource Manager](https://docs.microsoft.com/azure/devops/pipelines/library/service-endpoints#sep-azure-rm).

-   Data Factory skonfigurowany przy użyciu integracji Azure Repos git.

-    [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) zawierający wpisy tajne dla każdego środowiska.

### <a name="set-up-an-azure-pipelines-release"></a>Konfigurowanie wersji Azure Pipelines

1.  W [środowisku użytkownika usługi Azure DevOps](https://dev.azure.com/)Otwórz projekt skonfigurowany przy użyciu Data Factory.

1.  W lewej części strony kliknij pozycję **potoki** , a następnie wybierz pozycję **wersje**.

    ![](media/continuous-integration-deployment/continuous-integration-image6.png)

1.  Wybierz pozycję **Nowy potok** lub jeśli masz istniejące potoki, **nowe**, a następnie **nowe potoku wydania**.

1.  Wybierz **pusty szablon zadania** .

    ![](media/continuous-integration-deployment/continuous-integration-image13.png)

1.  W polu **Nazwa etapu** wprowadź nazwę środowiska.

1.  Wybierz pozycję **Dodaj artefakt**i wybierz takie samo repozytorium, które zostało skonfigurowane przy użyciu Data Factory. Wybierz `adf_publish` jako gałąź domyślną z najnowszą wersją domyślną.

    ![](media/continuous-integration-deployment/continuous-integration-image7.png)

1.  Dodawanie Azure Resource Manager zadania wdrażania:

    a.  W widoku etap kliknij link **Wyświetl zadania etapów** .

    ![](media/continuous-integration-deployment/continuous-integration-image14.png)

    b.  Utwórz nowe zadanie. Wyszukaj **wdrożenie grupy zasobów platformy Azure**i kliknij przycisk **Dodaj**.

    d.  W zadaniu wdrażania wybierz subskrypcję, grupę zasobów i lokalizację dla Data Factory docelowej, a następnie podaj poświadczenia w razie potrzeby.

    d.  Na liście rozwijanej Akcja wybierz pozycję **Utwórz lub Zaktualizuj grupę zasobów**.

    e.  Wybierz **...** w polu **szablon** . Przeglądaj w poszukiwaniu szablonu Azure Resource Manager Utwórz za pomocą kroku **Importuj szablon ARM** w temacie [Tworzenie szablonu usługi Resource Manager dla każdego środowiska](continuous-integration-deployment.md#create-a-resource-manager-template-for-each-environment). Wyszukaj ten plik w folderze `<FactoryName>` rozgałęzienia `adf_publish`.

    f.  Wybierz **...** w **polu Parametry szablonu.** Aby wybrać plik parametrów. Wybierz odpowiedni plik w zależności od tego, czy utworzono kopię, czy używasz domyślnego pliku *ARMTemplateParametersForFactory. JSON*.

    g.  Wybierz **...** obok pola **Przesłoń parametry szablonu** i wprowadź informacje dotyczące Data Factory docelowej. W przypadku poświadczeń, które pochodzą z magazynu kluczy, wprowadź nazwę klucza tajnego między podwójnymi cudzysłowami. Na przykład, jeśli nazwa wpisu tajnego to `cred1`, wprowadź `"$(cred1)"`dla jego wartości.

    ![](media/continuous-integration-deployment/continuous-integration-image9.png)

    h. Wybierz tryb **przyrostowego** wdrażania.

    > [!WARNING]
    > W przypadku wybrania opcji **Zakończ** tryb wdrażania istniejące zasoby mogą zostać usunięte, w tym wszystkie zasoby w docelowej grupie zasobów, które nie są zdefiniowane w szablonie Menedżer zasobów.

1.  Zapisz potok wydania.

1. Aby wyzwolić wydanie, kliknij pozycję **Utwórz wydanie**

![](media/continuous-integration-deployment/continuous-integration-image10.png)

### <a name="get-secrets-from-azure-key-vault"></a>Pobierz wpisy tajne z Azure Key Vault

Jeśli wpisy tajne zostały przekazane do Azure Resource Manager szablonu, zalecamy użycie Azure Key Vault z wersją Azure Pipelines.

Istnieją dwa sposoby obsługi wpisów tajnych:

1.  Dodaj wpisy tajne do pliku Parameters. Aby uzyskać więcej informacji, zobacz [używanie Azure Key Vault do przekazywania zabezpieczonej wartości parametrów podczas wdrażania](../azure-resource-manager/resource-manager-keyvault-parameter.md).

    -   Utwórz kopię pliku parametrów, który jest przekazywany do gałęzi publikowania i ustaw wartości parametrów, które mają zostać pobrane z magazynu kluczy w następującym formacie:

    ```json
    {
        "parameters": {
            "azureSqlReportingDbPassword": {
                "reference": {
                    "keyVault": {
                        "id": "/subscriptions/<subId>/resourceGroups/<resourcegroupId> /providers/Microsoft.KeyVault/vaults/<vault-name> "
                    },
                    "secretName": " < secret - name > "
                }
            }
        }
    }
    ```

    -   W przypadku korzystania z tej metody wpis tajny jest pobierany automatycznie z magazynu kluczy.

    -   Plik parametrów musi znajdować się również w rozgałęzieniu publikacji.

1.  Dodaj [zadanie Azure Key Vault](https://docs.microsoft.com/azure/devops/pipelines/tasks/deploy/azure-key-vault) przed wdrożeniem Azure Resource Manager opisanym w poprzedniej sekcji:

    -   Wybierz kartę **zadania** , Utwórz nowe zadanie, Wyszukaj **Azure Key Vault** i Dodaj je.

    -   W Key Vault zadania wybierz subskrypcję, w której został utworzony magazyn kluczy, podaj poświadczenia w razie potrzeby, a następnie wybierz magazyn kluczy.

    ![](media/continuous-integration-deployment/continuous-integration-image8.png)

#### <a name="grant-permissions-to-the-azure-pipelines-agent"></a>Przyznawanie uprawnień agentowi Azure Pipelines

Jeśli odpowiednie uprawnienia nie są dostępne, zadanie Azure Key Vault może zakończyć się niepowodzeniem z powodu błędu odmowy dostępu. Pobierz dzienniki wydania i Znajdź plik `.ps1` za pomocą polecenia, aby przyznać uprawnienia agentowi Azure Pipelines. Można uruchomić polecenie bezpośrednio lub skopiować Identyfikator podmiotu zabezpieczeń z pliku i ręcznie dodać zasady dostępu w Azure Portal. Wymagane są **minimalne** uprawnienia.

### <a name="update-active-triggers"></a>Aktualizuj aktywne wyzwalacze

Wdrożenie może zakończyć się niepowodzeniem, jeśli spróbujesz zaktualizować aktywne wyzwalacze. Aby zaktualizować aktywne wyzwalacze, należy je ręcznie zatrzymać i uruchomić po wdrożeniu. Można to zrobić za pomocą zadania programu Azure PowerShell.

1.  Na karcie zadania w wersji Dodaj zadanie **programu Azure PowerShell** .

1.  Wybierz **Azure Resource Manager** jako typ połączenia i wybierz swoją subskrypcję.

1.  Wybierz **skrypt wbudowany** jako typ skryptu, a następnie podaj swój kod. W poniższym przykładzie są zatrzymywane wyzwalacze:

    ```powershell
    $triggersADF = Get-AzDataFactoryV2Trigger -DataFactoryName $DataFactoryName -ResourceGroupName $ResourceGroupName

    $triggersADF | ForEach-Object { Stop-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name $_.name -Force }
    ```

    ![](media/continuous-integration-deployment/continuous-integration-image11.png)

Możesz wykonać podobne kroki (z funkcją `Start-AzDataFactoryV2Trigger`), aby ponownie uruchomić Wyzwalacze po wdrożeniu.

> [!IMPORTANT]
> W scenariuszach ciągłej integracji i wdrażania typ Integration Runtime w różnych środowiskach musi być taki sam. Na przykład jeśli w środowisku deweloperskim jest używane *Samodzielna* Integration Runtime (IR), ten sam IR musi być typu *samodzielnego* w innych środowiskach, takich jak test i produkcja. Podobnie, jeśli udostępniasz środowiska Integration Runtime na wielu etapach, musisz skonfigurować środowisko Integration Runtime jako *połączone samodzielnie hostowane* we wszystkich środowiskach, takich jak programowanie, testowanie i środowisko produkcyjne.

#### <a name="sample-prepostdeployment-script"></a>Przykładowy skrypt poprzedzający/powdrożeniowe

Poniżej znajduje się przykładowy skrypt, aby zatrzymać wyzwalacze przed wdrożeniem, a następnie ponownie uruchomić wyzwalacze. Skrypt zawiera również kod służący do usuwania zasobów, które zostały usunięte. Aby zainstalować najnowszą wersję Azure PowerShell, zobacz [install Azure PowerShell in Windows with PowerShellGet](https://docs.microsoft.com/powershell/azure/install-az-ps).

```powershell
param
(
    [parameter(Mandatory = $false)] [String] $rootFolder,
    [parameter(Mandatory = $false)] [String] $armTemplate,
    [parameter(Mandatory = $false)] [String] $ResourceGroupName,
    [parameter(Mandatory = $false)] [String] $DataFactoryName,
    [parameter(Mandatory = $false)] [Bool] $predeployment=$true,
    [parameter(Mandatory = $false)] [Bool] $deleteDeployment=$false
)

$templateJson = Get-Content $armTemplate | ConvertFrom-Json
$resources = $templateJson.resources

#Triggers 
Write-Host "Getting triggers"
$triggersADF = Get-AzDataFactoryV2Trigger -DataFactoryName $DataFactoryName -ResourceGroupName $ResourceGroupName
$triggersTemplate = $resources | Where-Object { $_.type -eq "Microsoft.DataFactory/factories/triggers" }
$triggerNames = $triggersTemplate | ForEach-Object {$_.name.Substring(37, $_.name.Length-40)}
$activeTriggerNames = $triggersTemplate | Where-Object { $_.properties.runtimeState -eq "Started" -and ($_.properties.pipelines.Count -gt 0 -or $_.properties.pipeline.pipelineReference -ne $null)} | ForEach-Object {$_.name.Substring(37, $_.name.Length-40)}
$deletedtriggers = $triggersADF | Where-Object { $triggerNames -notcontains $_.Name }
$triggerstostop = $triggerNames | where { ($triggersADF | Select-Object name).name -contains $_ }

if ($predeployment -eq $true) {
    #Stop all triggers
    Write-Host "Stopping deployed triggers"
    $triggerstostop | ForEach-Object { 
        Write-host "Disabling trigger " $_
        Stop-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name $_ -Force 
    }
}
else {
    #Deleted resources
    #pipelines
    Write-Host "Getting pipelines"
    $pipelinesADF = Get-AzDataFactoryV2Pipeline -DataFactoryName $DataFactoryName -ResourceGroupName $ResourceGroupName
    $pipelinesTemplate = $resources | Where-Object { $_.type -eq "Microsoft.DataFactory/factories/pipelines" }
    $pipelinesNames = $pipelinesTemplate | ForEach-Object {$_.name.Substring(37, $_.name.Length-40)}
    $deletedpipelines = $pipelinesADF | Where-Object { $pipelinesNames -notcontains $_.Name }
    #datasets
    Write-Host "Getting datasets"
    $datasetsADF = Get-AzDataFactoryV2Dataset -DataFactoryName $DataFactoryName -ResourceGroupName $ResourceGroupName
    $datasetsTemplate = $resources | Where-Object { $_.type -eq "Microsoft.DataFactory/factories/datasets" }
    $datasetsNames = $datasetsTemplate | ForEach-Object {$_.name.Substring(37, $_.name.Length-40) }
    $deleteddataset = $datasetsADF | Where-Object { $datasetsNames -notcontains $_.Name }
    #linkedservices
    Write-Host "Getting linked services"
    $linkedservicesADF = Get-AzDataFactoryV2LinkedService -DataFactoryName $DataFactoryName -ResourceGroupName $ResourceGroupName
    $linkedservicesTemplate = $resources | Where-Object { $_.type -eq "Microsoft.DataFactory/factories/linkedservices" }
    $linkedservicesNames = $linkedservicesTemplate | ForEach-Object {$_.name.Substring(37, $_.name.Length-40)}
    $deletedlinkedservices = $linkedservicesADF | Where-Object { $linkedservicesNames -notcontains $_.Name }
    #Integrationruntimes
    Write-Host "Getting integration runtimes"
    $integrationruntimesADF = Get-AzDataFactoryV2IntegrationRuntime -DataFactoryName $DataFactoryName -ResourceGroupName $ResourceGroupName
    $integrationruntimesTemplate = $resources | Where-Object { $_.type -eq "Microsoft.DataFactory/factories/integrationruntimes" }
    $integrationruntimesNames = $integrationruntimesTemplate | ForEach-Object {$_.name.Substring(37, $_.name.Length-40)}
    $deletedintegrationruntimes = $integrationruntimesADF | Where-Object { $integrationruntimesNames -notcontains $_.Name }

    #Delete resources
    Write-Host "Deleting triggers"
    $deletedtriggers | ForEach-Object { 
        Write-Host "Deleting trigger "  $_.Name
        $trig = Get-AzDataFactoryV2Trigger -name $_.Name -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName
        if ($trig.RuntimeState -eq "Started") {
            Stop-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name $_.Name -Force 
        }
        Remove-AzDataFactoryV2Trigger -Name $_.Name -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Force 
    }
    Write-Host "Deleting pipelines"
    $deletedpipelines | ForEach-Object { 
        Write-Host "Deleting pipeline " $_.Name
        Remove-AzDataFactoryV2Pipeline -Name $_.Name -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Force 
    }
    Write-Host "Deleting datasets"
    $deleteddataset | ForEach-Object { 
        Write-Host "Deleting dataset " $_.Name
        Remove-AzDataFactoryV2Dataset -Name $_.Name -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Force 
    }
    Write-Host "Deleting linked services"
    $deletedlinkedservices | ForEach-Object { 
        Write-Host "Deleting Linked Service " $_.Name
        Remove-AzDataFactoryV2LinkedService -Name $_.Name -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Force 
    }
    Write-Host "Deleting integration runtimes"
    $deletedintegrationruntimes | ForEach-Object { 
        Write-Host "Deleting integration runtime " $_.Name
        Remove-AzDataFactoryV2IntegrationRuntime -Name $_.Name -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Force 
    }

    if ($deleteDeployment -eq $true) {
        Write-Host "Deleting ARM deployment ... under resource group: " $ResourceGroupName
        $deployments = Get-AzResourceGroupDeployment -ResourceGroupName $ResourceGroupName
        $deploymentsToConsider = $deployments | Where { $_.DeploymentName -like "ArmTemplate_master*" -or $_.DeploymentName -like "ArmTemplateForFactory*" } | Sort-Object -Property Timestamp -Descending
        $deploymentName = $deploymentsToConsider[0].DeploymentName

       Write-Host "Deployment to be deleted: " $deploymentName
        $deploymentOperations = Get-AzResourceGroupDeploymentOperation -DeploymentName $deploymentName -ResourceGroupName $ResourceGroupName
        $deploymentsToDelete = $deploymentOperations | Where { $_.properties.targetResource.id -like "*Microsoft.Resources/deployments*" }

        $deploymentsToDelete | ForEach-Object { 
            Write-host "Deleting inner deployment: " $_.properties.targetResource.id
            Remove-AzResourceGroupDeployment -Id $_.properties.targetResource.id
        }
        Write-Host "Deleting deployment: " $deploymentName
        Remove-AzResourceGroupDeployment -ResourceGroupName $ResourceGroupName -Name $deploymentName
    }

    #Start Active triggers - After cleanup efforts
    Write-Host "Starting active triggers"
    $activeTriggerNames | ForEach-Object { 
        Write-host "Enabling trigger " $_
        Start-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name $_ -Force 
    }
}
```

## <a name="use-custom-parameters-with-the-resource-manager-template"></a>Używanie parametrów niestandardowych z szablonem Menedżer zasobów

Jeśli korzystasz z trybu GIT, możesz zastąpić domyślne właściwości w szablonie Menedżer zasobów, aby ustawić właściwości, które są sparametryzowane w szablonie i właściwości, które są zakodowane na stałe. Możesz chcieć zastąpić domyślny szablon parametryzacja w następujących scenariuszach:

* Używasz zautomatyzowanej ciągłej integracji/ciągłego dostarczania i chcesz zmienić niektóre właściwości podczas wdrażania Menedżer zasobów, ale właściwości nie są domyślnie sparametryzowane.
* Fabryka jest tak duża, że domyślny szablon Menedżer zasobów jest nieprawidłowy, ponieważ ma więcej niż maksymalna dozwolona liczba parametrów (256).

W tych warunkach, aby zastąpić domyślny szablon parametryzacja, Utwórz plik o nazwie *ARM-template-parameters-Definition. JSON* w folderze głównym repozytorium. Nazwa pliku musi dokładnie pasować. Data Factory próbuje odczytać ten plik z niezależnej gałęzi, która jest obecnie w portalu Azure Data Factory, a nie tylko z gałęzi współpracy. Można utworzyć lub edytować plik z gałęzi prywatnej, gdzie można testować zmiany przy użyciu **szablonu usługi ARM** w interfejsie użytkownika. Następnie można scalić plik z gałęzią współpracy. Jeśli plik nie zostanie znaleziony, zostanie użyty szablon domyślny.


### <a name="syntax-of-a-custom-parameters-file"></a>Składnia pliku parametrów niestandardowych

Poniżej przedstawiono niektóre wskazówki, które należy stosować podczas tworzenia pliku parametrów niestandardowych. Plik składa się z sekcji dla każdego typu jednostki: wyzwalacza, potok, połączona usługa, zestaw danych, środowisko Integration Runtime itd.
* Wprowadź ścieżkę właściwości pod odpowiednim typem jednostki.
* Po ustawieniu nazwy właściwości na "\*" "wskazujesz, że chcesz Sparametryzuj wszystkie właściwości w tym obszarze (tylko do pierwszego poziomu, a nie cyklicznie). Możesz również podać wszelkie wyjątki.
* Po ustawieniu wartości właściwości jako ciągu, należy wskazać, że właściwość ma być Sparametryzuj. Użyj formatu `<action>:<name>:<stype>`.
   *  `<action>` może być jednym z następujących znaków:
      * `=` oznacza, że bieżąca wartość jest zachowywana jako wartość domyślna parametru.
      * `-` oznacza, że wartość domyślna parametru nie jest zachowywana.
      * `|` to specjalny przypadek dla wpisów tajnych z Azure Key Vault dla parametrów połączenia lub kluczy.
   * `<name>` to nazwa parametru. Jeśli jest pusta, przyjmuje nazwę właściwości. Jeśli wartość zaczyna się od znaku `-`, nazwa zostanie skrócona. Na przykład `AzureStorage1_properties_typeProperties_connectionString` zostałaby skrócona do `AzureStorage1_connectionString`.
   *  `<stype>`jest typem parametru. Jeśli `<stype>` jest puste, domyślnym typem jest `string`. Obsługiwane wartości: `string`, `bool`, `number`, `object`i `securestring`.
* Gdy określisz tablicę w pliku definicji, wskazujesz, że pasująca właściwość w szablonie jest tablicą. Data Factory wykonuje iterację wszystkich obiektów w tablicy przy użyciu definicji określonej w obiekcie Integration Runtime tablicy. Drugi obiekt, ciąg, będzie nazwą właściwości, która jest używana jako nazwa parametru dla każdej iteracji.
* Nie istnieje możliwość zdefiniowania definicji specyficznej dla wystąpienia zasobu. Każda definicja ma zastosowanie do wszystkich zasobów tego typu.
* Domyślnie wszystkie bezpieczne ciągi, takie jak Key Vault Secret i Secure String, takie jak parametry połączenia, klucze i tokeny, są sparametryzowane.
 
### <a name="sample-parameterization-template"></a>Przykładowy szablon parametryzacja

Poniżej znajduje się przykładowy szablon parametryzacja, który może wyglądać następująco:

```json
{
    "Microsoft.DataFactory/factories/pipelines": {
        "properties": {
            "activities": [{
                "typeProperties": {
                    "waitTimeInSeconds": "-::number",
                    "headers": "=::object"
                }
            }]
        }
    },
    "Microsoft.DataFactory/factories/integrationRuntimes": {
        "properties": {
            "typeProperties": {
                "*": "="
            }
        }
    },
    "Microsoft.DataFactory/factories/triggers": {
        "properties": {
            "typeProperties": {
                "recurrence": {
                    "*": "=",
                    "interval": "=:triggerSuffix:number",
                    "frequency": "=:-freq"
                },
                "maxConcurrency": "="
            }
        }
    },
    "Microsoft.DataFactory/factories/linkedServices": {
        "*": {
            "properties": {
                "typeProperties": {
                    "accountName": "=",
                    "username": "=",
                    "connectionString": "|:-connectionString:secureString",
                    "secretAccessKey": "|"
                }
            }
        },
        "AzureDataLakeStore": {
            "properties": {
                "typeProperties": {
                    "dataLakeStoreUri": "="
                }
            }
        }
    },
    "Microsoft.DataFactory/factories/datasets": {
        "properties": {
            "typeProperties": {
                "*": "="
            }
        }
    }
}
```
Poniżej przedstawiono wyjaśnienie sposobu konstruowania powyższego szablonu, podzielonego na typ zasobu.

#### <a name="pipelines"></a>Potoki
    
* Wszystkie właściwości w działaniach Path/typeProperties/waitTimeInSeconds są sparametryzowane. Wszystkie działania w potoku, które mają właściwość poziomu kodu o nazwie `waitTimeInSeconds` (na przykład działanie `Wait`) są sparametryzowane jako liczba z nazwą domyślną. Ale nie będzie on miał wartości domyślnej w szablonie Menedżer zasobów. Będzie to obowiązkowe wejście podczas wdrażania Menedżer zasobów.
* Podobnie właściwość o nazwie `headers` (na przykład w działaniu `Web`) jest sparametryzowane przy użyciu typu `object` (JObject). Ma wartość domyślną, która jest taka sama jak wartość w fabryki źródłowej.

#### <a name="integrationruntimes"></a>IntegrationRuntimes

* Wszystkie właściwości pod ścieżką `typeProperties` są sparametryzowane z odpowiednimi wartościami domyślnymi. Na przykład istnieją dwie właściwości w obszarze właściwości typu **IntegrationRuntimes** : `computeProperties` i `ssisProperties`. Oba typy właściwości są tworzone z odpowiednimi wartościami domyślnymi i typami (Object).

#### <a name="triggers"></a>Wyzwalacze

* W obszarze `typeProperties`dwie właściwości są sparametryzowane. Pierwszy z nich jest `maxConcurrency`, który jest określony jako ma wartość domyślną i jest typu`string`. Ma domyślną nazwę parametru `<entityName>_properties_typeProperties_maxConcurrency`.
* Właściwość `recurrence` jest również sparametryzowana. W obszarze IT wszystkie właściwości na tym poziomie są określane jako ciągi, z wartościami domyślnymi i nazwami parametrów. Wyjątkiem jest właściwość `interval`, która jest sparametryzowane jako typ liczbowy i z nazwą parametru z sufiksem `<entityName>_properties_typeProperties_recurrence_triggerSuffix`. Podobnie Właściwość `freq` jest ciągiem i jest sparametryzowane jako ciąg. Jednak Właściwość `freq` jest sparametryzowane bez wartości domyślnej. Nazwa jest skracana i ma sufiks. Na przykład `<entityName>_freq`.

#### <a name="linkedservices"></a>LinkedServices

* Połączone usługi są unikatowe. Ponieważ połączone usługi i zestawy danych mają szeroką gamę typów, można zapewnić dostosowanie specyficzne dla określonego typu. W tym przykładzie zostaną zastosowane wszystkie połączone usługi typu `AzureDataLakeStore`, określony szablon i dla wszystkich innych (za pośrednictwem \*) zostanie zastosowany inny szablon.
* Właściwość `connectionString` zostanie sparametryzowana jako wartość `securestring`, nie będzie mieć wartości domyślnej i ma skróconą nazwę parametru, która ma sufiks `connectionString`.
* Właściwość `secretAccessKey` występuje `AzureKeyVaultSecret` (na przykład w usłudze połączonej `AmazonS3`). Jest on automatycznie sparametryzowany jako wpis tajny Azure Key Vault i pobierany ze skonfigurowanego magazynu kluczy. Możesz również Sparametryzuj sam magazyn kluczy.

#### <a name="datasets"></a>Zestawy danych

* Chociaż dostosowanie specyficzne dla typu jest dostępne dla zestawów danych, konfiguracja może być świadczona bez jawnego konfigurowania \*na poziomie. W powyższym przykładzie wszystkie właściwości zestawu danych w obszarze `typeProperties` są sparametryzowane.

### <a name="default-parameterization-template"></a>Domyślny szablon parametryzacja

Poniżej znajduje się bieżący domyślny szablon parametryzacja. Jeśli musisz tylko dodać jeden lub kilka parametrów, edytowanie tego elementu bezpośrednio może być przydatne, ponieważ nie utracisz istniejącej struktury parametryzacja.

```json
{
    "Microsoft.DataFactory/factories/pipelines": {
    },
    "Microsoft.DataFactory/factories/integrationRuntimes":{
        "properties": {
            "typeProperties": {
                "ssisProperties": {
                    "catalogInfo": {
                        "catalogServerEndpoint": "=",
                        "catalogAdminUserName": "=",
                        "catalogAdminPassword": {
                            "value": "-::secureString"
                        }
                    },
                    "customSetupScriptProperties": {
                        "sasToken": {
                            "value": "-::secureString"
                        }
                    }
                },
                "linkedInfo": {
                    "key": {
                        "value": "-::secureString"
                    },
                    "resourceId": "="
                }
            }
        }
    },
    "Microsoft.DataFactory/factories/triggers": {
        "properties": {
            "pipelines": [{
                    "parameters": {
                        "*": "="
                    }
                },  
                "pipelineReference.referenceName"
            ],
            "pipeline": {
                "parameters": {
                    "*": "="
                }
            },
            "typeProperties": {
                "scope": "="
            }

        }
    },
    "Microsoft.DataFactory/factories/linkedServices": {
        "*": {
            "properties": {
                "typeProperties": {
                    "accountName": "=",
                    "username": "=",
                    "userName": "=",
                    "accessKeyId": "=",
                    "servicePrincipalId": "=",
                    "userId": "=",
                    "clientId": "=",
                    "clusterUserName": "=",
                    "clusterSshUserName": "=",
                    "hostSubscriptionId": "=",
                    "clusterResourceGroup": "=",
                    "subscriptionId": "=",
                    "resourceGroupName": "=",
                    "tenant": "=",
                    "dataLakeStoreUri": "=",
                    "baseUrl": "=",
                    "database": "=",
                    "serviceEndpoint": "=",
                    "batchUri": "=",
                    "databaseName": "=",
                    "systemNumber": "=",
                    "server": "=",
                    "url":"=",
                    "aadResourceId": "=",
                    "connectionString": "|:-connectionString:secureString"
                }
            }
        },
        "Odbc": {
            "properties": {
                "typeProperties": {
                    "userName": "=",
                    "connectionString": {
                        "secretName": "="
                    }
                }
            }
        }
    },
    "Microsoft.DataFactory/factories/datasets": {
        "*": {
            "properties": {
                "typeProperties": {
                    "folderPath": "=",
                    "fileName": "="
                }
            }
        }}
}
```

Poniżej przedstawiono przykład sposobu dodawania pojedynczej wartości do domyślnego szablonu parametryzacja. Chcemy dodać do pliku parametrów tylko istniejący, interaktywny identyfikator klastra datakostki dla połączonej usługi datakostki. Zwróć uwagę, że Poniższy plik jest taki sam jak plik powyżej, z wyjątkiem `existingClusterId` uwzględnionych w polu właściwości `Microsoft.DataFactory/factories/linkedServices`.

```json
{
    "Microsoft.DataFactory/factories/pipelines": {
    },
    "Microsoft.DataFactory/factories/integrationRuntimes":{
        "properties": {
            "typeProperties": {
                "ssisProperties": {
                    "catalogInfo": {
                        "catalogServerEndpoint": "=",
                        "catalogAdminUserName": "=",
                        "catalogAdminPassword": {
                            "value": "-::secureString"
                        }
                    },
                    "customSetupScriptProperties": {
                        "sasToken": {
                            "value": "-::secureString"
                        }
                    }
                },
                "linkedInfo": {
                    "key": {
                        "value": "-::secureString"
                    },
                    "resourceId": "="
                }
            }
        }
    },
    "Microsoft.DataFactory/factories/triggers": {
        "properties": {
            "pipelines": [{
                    "parameters": {
                        "*": "="
                    }
                },  
                "pipelineReference.referenceName"
            ],
            "pipeline": {
                "parameters": {
                    "*": "="
                }
            },
            "typeProperties": {
                "scope": "="
            }
 
        }
    },
    "Microsoft.DataFactory/factories/linkedServices": {
        "*": {
            "properties": {
                "typeProperties": {
                    "accountName": "=",
                    "username": "=",
                    "userName": "=",
                    "accessKeyId": "=",
                    "servicePrincipalId": "=",
                    "userId": "=",
                    "clientId": "=",
                    "clusterUserName": "=",
                    "clusterSshUserName": "=",
                    "hostSubscriptionId": "=",
                    "clusterResourceGroup": "=",
                    "subscriptionId": "=",
                    "resourceGroupName": "=",
                    "tenant": "=",
                    "dataLakeStoreUri": "=",
                    "baseUrl": "=",
                    "database": "=",
                    "serviceEndpoint": "=",
                    "batchUri": "=",
                    "databaseName": "=",
                    "systemNumber": "=",
                    "server": "=",
                    "url":"=",
                    "aadResourceId": "=",
                    "connectionString": "|:-connectionString:secureString",
                    "existingClusterId": "-"
                }
            }
        },
        "Odbc": {
            "properties": {
                "typeProperties": {
                    "userName": "=",
                    "connectionString": {
                        "secretName": "="
                    }
                }
            }
        }
    },
    "Microsoft.DataFactory/factories/datasets": {
        "*": {
            "properties": {
                "typeProperties": {
                    "folderPath": "=",
                    "fileName": "="
                }
            }
        }}
}
```

## <a name="linked-resource-manager-templates"></a>Połączone szablony Menedżer zasobów

Jeśli skonfigurowano ciągłą integrację i wdrażanie (CI/CD) dla fabryk danych, może wystąpić Azure Resource Manager ograniczeń szablonów w miarę wzrostu fabryki. Przykładem limitu jest maksymalna liczba zasobów w szablonie Menedżer zasobów. Aby obsłużyć duże fabryki oraz generować pełny Menedżer zasobów szablon dla fabryki, Data Factory teraz generuje połączone szablony Menedżer zasobów. W przypadku tej funkcji cały ładunek fabryki jest podzielony na kilka plików, dzięki czemu nie można przekroczyć limitów.

W przypadku skonfigurowania usługi git połączone szablony są generowane i zapisywane wraz z pełnymi szablonami Menedżer zasobów w gałęzi `adf_publish` pod nowym folderem o nazwie `linkedTemplates`.

![Folder połączonych Menedżer zasobów szablonów](media/continuous-integration-deployment/linked-resource-manager-templates.png)

Połączone szablony Menedżer zasobów zwykle mają szablon główny i zestaw szablonów podrzędnych połączonych z serwerem głównym. Szablon nadrzędny ma nazwę `ArmTemplate_master.json`, a szablony podrzędne są nazwane przy użyciu wzorca `ArmTemplate_0.json`, `ArmTemplate_1.json`i tak dalej. Aby użyć połączonych szablonów zamiast szablonu pełnego Menedżer zasobów, zaktualizuj zadanie ciągłej integracji/ciągłego dostarczania, aby wskazywało `ArmTemplate_master.json` zamiast `ArmTemplateForFactory.json` (pełny szablon Menedżer zasobów). Menedżer zasobów wymaga również przekazania połączonych szablonów do konta magazynu, aby umożliwić im dostęp do nich przez platformę Azure podczas wdrażania. Aby uzyskać więcej informacji, zobacz [wdrażanie połączonych szablonów ARM z VSTS](https://blogs.msdn.microsoft.com/najib/2018/04/22/deploying-linked-arm-templates-with-vsts/).

Pamiętaj, aby dodać skrypty Data Factory w potoku ciągłej integracji/ciągłego wdrażania przed i po wykonaniu zadania wdrożenia.

Jeśli nie masz skonfigurowanego narzędzia Git, połączone szablony są dostępne za pośrednictwem gestu **Eksportuj szablon ARM** .

## <a name="hot-fix-production-branch"></a>Rozgałęzienie produkcyjne Hot-Fix

W przypadku wdrożenia fabryki do produkcji i zapoznania się z usterką, która musi zostać naprawiona natychmiast, ale nie można wdrożyć bieżącej gałęzi współpracy, może być konieczne wdrożenie gorącej poprawki. To podejście jest znane jako Szybka naprawa inżynierów lub QFE. 

1.  W usłudze Azure DevOps przejdź do wersji, która została wdrożona w środowisku produkcyjnym, i Znajdź ostatnio wdrożone zatwierdzenie.

2.  Z poziomu komunikatu zatwierdzania Pobierz identyfikator zatwierdzenia gałęzi współpracy.

3.  Utwórz nową gałąź Hot-Fix z tego zatwierdzenia.

4.  Przejdź do Azure Data Factory środowiska użytkownika i Przełącz się do tej gałęzi.

5.  Przy użyciu środowiska Azure Data Factory, napraw usterkę. Przetestuj zmiany.

6.  Po zweryfikowaniu poprawki kliknij pozycję **Eksportuj szablon ARM** w celu pobrania szablonu Menedżer zasobów gorąca.

7.  Ręcznie Zaewidencjonuj tę kompilację do adf_publish rozgałęzienia.

8.  Jeśli potoku wydania został skonfigurowany do automatycznego wyzwalania na podstawie zaewidencjonowania adf_publish, nowe wydanie zostanie automatycznie uruchomione. W przeciwnym razie ręcznie wydawanie kolejki.

9.  Wdróż wersję Hot-Fix do fabryki testowej i produkcyjnej. Ta wersja zawiera poprzedni ładunek produkcyjny oraz poprawkę wykonaną w kroku 5.

10. Dodaj zmiany z rozgałęzienia gorąca do deweloperskiego, aby późniejsze wersje nie były uruchamiane w tej samej usterce.

## <a name="best-practices-for-cicd"></a>Najlepsze rozwiązania dotyczące ciągłej integracji/ciągłego wdrażania

Jeśli korzystasz z integracji narzędzia Git z fabryką danych i masz potok CI/CD, który przenosi zmiany z projektu na test, a następnie do środowiska produkcyjnego, zalecamy następujące najlepsze rozwiązania:

-   **Integracja**z usługą git. Musisz tylko skonfigurować fabrykę danych deweloperskich z integracją z usługą git. Zmiany w testach i produkcji są wdrażane za pośrednictwem ciągłej integracji/ciągłego wdrażania.

-   **Data Factory skrypt**ciągłej integracji/ciągłego wdrażania. Przed wykonaniem kroku wdrażania Menedżer zasobów w obszarze ciągłej integracji/ciągłego wykonywania określonych zadań, takich jak zatrzymywanie/uruchamianie wyzwalaczy i czyszczenie. Zalecamy używanie skryptów programu PowerShell przed i po wdrożeniu. Aby uzyskać więcej informacji, zobacz [Aktualizuj aktywne wyzwalacze](#update-active-triggers). 

-   **Środowisko Integration Runtime i udostępnianie**. Środowiska Integration Runtime nie zmieniają się często i są podobne do wszystkich etapów w ciągłej integracji/ciągłego wdrażania. W związku z tym Data Factory oczekuje, że będzie mieć taką samą nazwę, jak i ten sam typ środowiska Integration Runtime na wszystkich etapach ciągłej integracji/ciągłego wdrażania. Jeśli chcesz udostępnić środowisko Integration Runtime na wszystkich etapach, rozważ użycie usługi Trzyelementowy Factory tylko w celu udostępnienia udostępnionych środowisk Integration Runtime. Tej fabryki udostępnionej można używać we wszystkich środowiskach jako połączonego typu środowiska Integration Runtime.

-   **Key Vault**. W przypadku korzystania z połączonych usług opartych na Azure Key Vaultach można uzyskać więcej korzyści, zachowując oddzielne magazyny kluczy dla różnych środowisk. Dla każdego z nich można także skonfigurować osobne poziomy uprawnień. Na przykład użytkownik może nie chcieć, aby członkowie zespołu mieli uprawnienia do wpisów tajnych produkcji. Jeśli korzystasz z tego podejścia, zalecamy zachowanie tych samych nazw tajnych na wszystkich etapach. Jeśli zachowasz te same nazwy, nie musisz zmieniać szablonów Menedżer zasobów w środowiskach ciągłej integracji/ciągłego wdrażania, ponieważ Jedynym wymaganiem jest nazwa magazynu kluczy, który jest jednym z Menedżer zasobów parametrów szablonu.

## <a name="unsupported-features"></a>Nieobsługiwane funkcje

- Zgodnie z projektem, funkcja ADF _nie zezwala na_ zatwierdzanie wybierania ani selektywne Publikowanie zasobów. Opublikowanie obejmie **wszystkie** zmiany wprowadzone w fabryce danych

    - Jednostki usługi Data Factory są od siebie zależne, na przykład wyzwalacze są zależne od potoków, potoki zależą od zestawów danych i innych potoków itp. Selektywne publikowanie podzestawu zasobów _może_ prowadzić do nieoczekiwanych zachowań i błędów
    - W rzadkich przypadkach, gdy wymagane jest publikowanie selektywne, można rozważyć zastosowanie poprawki. Aby uzyskać więcej informacji, zobacz [rozgałęzienie produkcyjne Hot-Fix](#hot-fix-production-branch)

-   Nie można publikować z gałęzi prywatnych

-   Od teraz nie można hostować projektów w BitBucket
