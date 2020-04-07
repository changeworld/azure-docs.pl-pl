---
title: Używanie skryptów wdrażania w szablonach | Dokumenty firmy Microsoft
description: użyj skryptów wdrażania w szablonach usługi Azure Resource Manager.
services: azure-resource-manager
author: mumian
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 04/06/2020
ms.author: jgao
ms.openlocfilehash: aa49b313f0fb10175dc6c0003f1a919f61731269
ms.sourcegitcommit: bd5fee5c56f2cbe74aa8569a1a5bce12a3b3efa6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2020
ms.locfileid: "80743319"
---
# <a name="use-deployment-scripts-in-templates-preview"></a>Używanie skryptów wdrażania w szablonach (Wersja zapoznawcza)

Dowiedz się, jak używać skryptów wdrażania w szablonach zasobów platformy Azure. Dzięki nowemu typowi zasobu o nazwie `Microsoft.Resources/deploymentScripts`użytkownicy mogą wykonywać skrypty wdrażania we wdrożeniach szablonów i przeglądać wyniki wykonywania. Te skrypty mogą służyć do wykonywania niestandardowych kroków, takich jak:

- dodawanie użytkowników do katalogu
- tworzenie rejestracji aplikacji
- wykonywanie operacji na płaszczyznie danych, na przykład kopiowanie obiektów blob lub bazy danych źródłowych
- wyszukuj i weryfikuj klucz licencyjny
- tworzenie certyfikatu z podpisem własnym
- tworzenie obiektu w usłudze Azure AD
- wyszukuj bloki adresów IP z systemu niestandardowego

Korzyści ze skryptu wdrażania:

- Łatwe do kodu, użycia i debugowania. Skrypty wdrażania można tworzyć w ulubionych środowiskach programistów. Skrypty mogą być osadzone w szablonach lub w zewnętrznych plikach skryptów.
- Można określić język skryptu i platformę. Obecnie obsługiwane są skrypty wdrażania platformy Azure PowerShell i interfejsu wiersza polecenia platformy Azure w środowisku systemu Linux.
- Zezwalaj na określanie tożsamości, które są używane do wykonywania skryptów. Obecnie obsługiwana jest tylko [tożsamość zarządzana przypisana przez użytkownika platformy Azure.](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md)
- Zezwalaj na przekazywanie argumentów wiersza polecenia do skryptu.
- Można określić dane wyjściowe skryptu i przekazać je z powrotem do wdrożenia.

Zasób skryptu wdrażania jest dostępny tylko w regionach, w których jest dostępne wystąpienie kontenera platformy Azure.  Zobacz [Dostępność zasobów dla wystąpień kontenerów platformy Azure w regionach platformy Azure.](../../container-instances/container-instances-region-availability.md)

> [!IMPORTANT]
> Dwa zasoby skryptu wdrażania, konto magazynu i wystąpienie kontenera, są tworzone w tej samej grupie zasobów do wykonywania skryptów i rozwiązywania problemów. Te zasoby są zwykle usuwane przez usługę skryptu, gdy wykonanie skryptu wdrożenia zostanie w stanie terminala. Naliczane są naliczane za zasoby, dopóki zasoby nie zostaną usunięte. Aby dowiedzieć się więcej, zobacz [Oczyszczanie zasobów skryptu wdrażania](#clean-up-deployment-script-resources).

## <a name="prerequisites"></a>Wymagania wstępne

- **Tożsamość zarządzana przypisana przez użytkownika z rolą współautora do docelowej grupy zasobów**. Ta tożsamość jest używana do wykonywania skryptów wdrażania. Aby wykonać operacje poza grupą zasobów, należy udzielić dodatkowych uprawnień. Na przykład przypisz tożsamość do poziomu subskrypcji, jeśli chcesz utworzyć nową grupę zasobów.

  > [!NOTE]
  > Aparat skryptów wdrażania tworzy konto magazynu i wystąpienie kontenera w tle.  Tożsamość zarządzana przypisana przez użytkownika z rolą współautora na poziomie subskrypcji jest wymagana, jeśli subskrypcja nie zarejestrowała dostawców zasobów konta magazynu platformy Azure (Microsoft.Storage) i wystąpienia kontenera platformy Azure (Microsoft.ContainerInstance).

  Aby utworzyć tożsamość, zobacz [Tworzenie tożsamości zarządzanej przypisanej przez użytkownika przy użyciu portalu Azure](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md)portal lub przy użyciu interfejsu [wiersza polecenia platformy Azure](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-cli.md)lub przy użyciu programu Azure [PowerShell](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-powershell.md). Identyfikator tożsamości jest potrzebny podczas wdrażania szablonu. Format tożsamości jest:

  ```json
  /subscriptions/<SubscriptionID>/resourcegroups/<ResourceGroupName>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<IdentityID>
  ```

  Użyj następującego skryptu interfejsu wiersza polecenia lub programu PowerShell, aby uzyskać identyfikator, podając nazwę grupy zasobów i nazwę tożsamości.

  # <a name="cli"></a>[Interfejs wiersza polecenia](#tab/CLI)

  ```azurecli-interactive
  echo "Enter the Resource Group name:" &&
  read resourceGroupName &&
  echo "Enter the managed identity name:" &&
  read idName &&
  az identity show -g jgaoidentity1008rg -n jgaouami --query id
  ```

  # <a name="powershell"></a>[PowerShell](#tab/PowerShell)

  ```azurepowershell-interactive
  $idGroup = Read-Host -Prompt "Enter the resource group name for the managed identity"
  $idName = Read-Host -Prompt "Enter the name of the managed identity"

  (Get-AzUserAssignedIdentity -resourcegroupname $idGroup -Name $idName).Id
  ```

  ---

- **Narzędzie Azure PowerShell** lub **Azure CLI**. Aby uzyskać listę obsługiwanych wersji programu Azure PowerShell, zobacz [tutaj;](https://mcr.microsoft.com/v2/azuredeploymentscripts-powershell/tags/list) aby uzyskać listę obsługiwanych wersji interfejsu wiersza polecenia platformy Azure, zobacz [tutaj](https://mcr.microsoft.com/v2/azuredeploymentscripts-powershell/tags/list).

    >[!IMPORTANT]
    > Skrypt wdrażania używa dostępnych obrazów interfejsu wiersza polecenia z rejestru kontenerów firmy Microsoft(MCR) . Trwa około jednego miesiąca, aby certyfikować obraz interfejsu wiersza polecenia dla skryptu wdrażania. Nie używaj wersji interfejsu wiersza polecenia, które zostały wydane w ciągu 30 dni. Aby znaleźć daty wydania obrazów, zobacz [Informacje o wersji interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/release-notes-azure-cli?view=azure-cli-latest). Jeśli używana jest wersja nieobjęta obsługą, komunikat o błędzie zawiera listę obsługiwanych wersji.

    Nie potrzebujesz tych wersji do wdrażania szablonów. Ale te wersje są potrzebne do testowania skryptów wdrażania lokalnie. Zobacz [Instalowanie modułu programu Azure PowerShell](/powershell/azure/install-az-ps). Można użyć wstępnie skonfigurowanego obrazu platformy Docker.  Zobacz [Konfigurowanie środowiska programistycznego](#configure-development-environment).

## <a name="sample-templates"></a>Przykładowe szablony

Przykładem jest następujący json.  Najnowszy schemat szablonu można znaleźć [tutaj](/azure/templates/microsoft.resources/deploymentscripts).

```json
{
  "type": "Microsoft.Resources/deploymentScripts",
  "apiVersion": "2019-10-01-preview",
  "name": "runPowerShellInline",
  "location": "[resourceGroup().location]",
  "kind": "AzurePowerShell", // or "AzureCLI"
  "identity": {
    "type": "userAssigned",
    "userAssignedIdentities": {
      "/subscriptions/01234567-89AB-CDEF-0123-456789ABCDEF/resourceGroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/myID": {}
    }
  },
  "properties": {
    "forceUpdateTag": 1,
    "azPowerShellVersion": "3.0",  // or "azCliVersion": "2.0.80"
    "arguments": "[concat('-name ', parameters('name'))]",
    "environmentVariables": [
      {
        "name": "someSecret",
        "secureValue": "if this is really a secret, don't put it here... in plain text..."
      }
    ],
    "scriptContent": "
      param([string] $name)
      $output = 'Hello {0}' -f $name
      Write-Output $output
      $DeploymentScriptOutputs = @{}
      $DeploymentScriptOutputs['text'] = $output
    ", // or "primaryScriptUri": "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/deployment-script/deploymentscript-helloworld.ps1",
    "supportingScriptUris":[],
    "timeout": "PT30M",
    "cleanupPreference": "OnSuccess",
    "retentionInterval": "P1D"
  }
}
```

> [!NOTE]
> Przykład jest w celu demonstracji.  **scriptContent** i **primaryScriptUris** nie mogą współistnieć w szablonie.

Szczegóły wartości właściwości:

- **Tożsamość:** Usługa skryptu wdrażania używa tożsamości zarządzanej przypisanej przez użytkownika do wykonywania skryptów. Obecnie obsługiwana jest tylko tożsamość zarządzana przypisana przez użytkownika.
- **rodzaj**: Określ typ skryptu. Obecnie obsługiwane są skrypty interfejsu wiersza polecenia azure powershell i interfejsu wiersza polecenia platformy Azure. Wartości to **AzurePowerShell** i **AzureCLI**.
- **forceUpdateTag:** Zmiana tej wartości między wdrożeniami szablonów zmusza skrypt wdrożenia do ponownego wykonania. Użyj funkcji newGuid() lub utcNow(), która musi być ustawiona jako domyślnaValue parametru. Aby dowiedzieć się więcej, zobacz [Uruchamianie skryptu więcej niż raz](#run-script-more-than-once).
- **azPowerShellVersion**/**azCliVersion**: Określ wersję modułu, która ma być używana. Aby uzyskać listę obsługiwanych wersji programu PowerShell i CLI, zobacz [Wymagania wstępne](#prerequisites).
- **argumenty**: Określ wartości parametrów. Wartości są oddzielone spacjami.
- **environmentVariables**: Określ zmienne środowiskowe, które mają być przerzuceni do skryptu. Aby uzyskać więcej informacji, zobacz [Tworzenie skryptów wdrażania](#develop-deployment-scripts).
- **scriptContent**: Określ zawartość skryptu. Aby uruchomić skrypt zewnętrzny, należy użyć zamiast tego. `primaryScriptUri` Przykłady można znaleźć w 1999 r. w [yd.](#use-inline-scripts) [Use external script](#use-external-scripts)
- **primaryScriptUri**: Określ publicznie dostępny adres URL do podstawowego skryptu wdrażania z obsługiwanymi rozszerzeniami plików.
- **supportingScriptUris**: Określ tablicę publicznie dostępnych adresów URL do `ScriptContent` obsługi `PrimaryScriptUri`plików, które są wywoływane w jednym lub .
- **limit czasu:** Określ maksymalny dozwolony czas wykonywania skryptu określony w [formacie ISO 8601](https://en.wikipedia.org/wiki/ISO_8601). Wartością domyślną jest **P1D**.
- **oczyszczaniePreference**. Określ preferencje czyszczenia zasobów wdrażania, gdy wykonanie skryptu zostanie w stanie terminala. Domyślnym ustawieniem jest **Zawsze**, co oznacza usunięcie zasobów pomimo stanu terminala (Powodzenie, Niepowodzenie, Anulowano). Aby dowiedzieć się więcej, zobacz [Czyszczenie zasobów skryptu wdrażania](#clean-up-deployment-script-resources).
- **retentionInterval**: Określ interwał, dla którego usługa zachowuje zasoby skryptu wdrażania po wykonaniu skryptu wdrożenia osiągnie stan terminala. Zasoby skryptu wdrażania zostaną usunięte po upływie tego czasu. Czas trwania jest oparty na [wzorze ISO 8601](https://en.wikipedia.org/wiki/ISO_8601). Wartością domyślną jest **P1D**, co oznacza siedem dni. Ta właściwość jest używana, gdy cleanupPreference jest ustawiona *na OnExpiration*. *Właściwość OnExpiration* nie jest obecnie włączona. Aby dowiedzieć się więcej, zobacz [Czyszczenie zasobów skryptu wdrażania](#clean-up-deployment-script-resources).

### <a name="additional-samples"></a>Dodatkowe próbki

- [tworzenie i przypisywanie certyfikatu do magazynu kluczy](https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/deployment-script/deploymentscript-keyvault.json)

- [tworzenie i przypisywanie tożsamości zarządzanej przypisanej przez użytkownika do grupy zasobów oraz uruchamianie skryptu wdrażania](https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/deployment-script/deploymentscript-keyvault-mi.json).

> [!NOTE]
> Zaleca się utworzenie tożsamości przypisanej przez użytkownika i przyznanie uprawnień z wyprzedzeniem. Jeśli użytkownik utworzy tożsamość i udzieli uprawnień w tym samym szablonie, w którym uruchamiasz skrypty wdrażania, może pojawić się błędy związane z logiem i uprawnieniami. To zajmuje trochę czasu, zanim uprawnienia stają się skuteczne.

## <a name="use-inline-scripts"></a>Używanie skryptów wbudowanych

Poniższy szablon ma jeden `Microsoft.Resources/deploymentScripts` zasób zdefiniowany z typem. Wyróżniona część jest skryptem wbudowanym.

:::code language="json" source="~/resourcemanager-templates/deployment-script/deploymentscript-helloworld.json" range="1-54" highlight="34-40":::

> [!NOTE]
> Ponieważ skrypty wdrażania wbudowanego są ujęte w cudzysłowy podwójne, ciągi wewnątrz skryptów wdrażania muszą być ujęte w pojedyncze cudzysłowy. Znak ucieczki programu PowerShell jest **&#92;**. Można również rozważyć użycie podstawienia ciągów, jak pokazano w poprzednim przykładzie JSON. Zobacz domyślną wartość parametru name.

Skrypt przyjmuje jeden parametr i wyprowadza wartość parametru. **DeploymentScriptOutputs** służy do przechowywania wyjść.  W sekcji wyjścia wiersz **wartości** pokazuje, jak uzyskać dostęp do przechowywanych wartości. `Write-Output`służy do debugowania. Aby dowiedzieć się, jak uzyskać dostęp do pliku wyjściowego, zobacz [Skrypty wdrażania debugowania](#debug-deployment-scripts).  Opisy właściwości można znaleźć w [przykładowych szablonach](#sample-templates).

Aby uruchomić skrypt, wybierz pozycję **Wypróbuj,** aby otworzyć powłokę chmury, a następnie wklej następujący kod do okienka powłoki.

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the name of the resource group to be created"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"
$id = Read-Host -Prompt "Enter the user-assigned managed identity ID"

New-AzResourceGroup -Name $resourceGroupName -Location $location

New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/deployment-script/deploymentscript-helloworld.json" -identity $id

Write-Host "Press [ENTER] to continue ..."
```

Dane wyjściowe wyglądają następująco:

![Skrypt wdrażania szablonu Menedżera zasobów hello world output](./media/deployment-script-template/resource-manager-template-deployment-script-helloworld-output.png)

## <a name="use-external-scripts"></a>Używanie skryptów zewnętrznych

Oprócz skryptów wbudowanych można również używać zewnętrznych plików skryptów. Obsługiwane są tylko podstawowe skrypty programu PowerShell z rozszerzeniem pliku **ps1.** W przypadku skryptów interfejsu wiersza polecenia skrypty podstawowe mogą mieć dowolne rozszerzenia (lub bez rozszerzenia), o ile skrypty są prawidłowymi skryptami bash. Aby użyć zewnętrznych plików `scriptContent` `primaryScriptUri`skryptów, zastąp . Przykład:

```json
"primaryScriptURI": "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/deployment-script/deploymentscript-helloworld.ps1",
```

Aby zobaczyć przykład, wybierz [tutaj](https://github.com/Azure/azure-docs-json-samples/blob/master/deployment-script/deploymentscript-helloworld-primaryscripturi.json).

Pliki skryptów zewnętrznych muszą być dostępne.  Aby zabezpieczyć pliki skryptów przechowywane na kontach magazynu platformy Azure, zobacz [Samouczek: Bezpieczne artefakty we wdrożeniach szablonów usługi Azure Resource Manager](./template-tutorial-secure-artifacts.md).

Użytkownik jest odpowiedzialny za zapewnienie integralności skryptów, do których odwołuje się skrypt wdrażania, **PrimaryScriptUri** lub **SupportingScriptUris**.  Odwoływanie się tylko do zaufanych skryptów.

## <a name="use-supporting-scripts"></a>Używanie skryptów pomocniczych

Skomplikowane logiki można rozdzielić na jeden lub więcej pomocniczych plików skryptów. Właściwość `supportingScriptURI` umożliwia udostępnienie tablicy identyfikatorów URI do obsługiwanych plików skryptów, jeśli to konieczne:

```json
"scriptContent": "
    ...
    ./Create-Cert.ps1
    ...
"

"supportingScriptUris": [
  "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/deployment-script/create-cert.ps1"
],
```

Obsługiwane pliki skryptów mogą być wywoływane zarówno ze skryptów wbudowanych, jak i z podstawowych plików skryptów. Obsługa plików skryptów nie ma żadnych ograniczeń dotyczących rozszerzenia pliku.

Pliki pomocnicze są kopiowane do azscripts/azscriptinput w czasie wykonywania. Użyj ścieżki względnej, aby odwołać się do plików pomocniczych ze skryptów wbudowanych i plików skryptów podstawowych.

## <a name="work-with-outputs-from-powershell-script"></a>Praca z wyjściami ze skryptu programu PowerShell

Poniższy szablon pokazuje, jak przekazywać wartości między dwoma zasobami deploymentScripts:

:::code language="json" source="~/resourcemanager-templates/deployment-script/deploymentscript-basic.json" range="1-84" highlight="39-40,66":::

W pierwszym zasobie należy zdefiniować zmienną o nazwie **$DeploymentScriptOutputs**i używać jej do przechowywania wartości wyjściowych. Aby uzyskać dostęp do wartości wyjściowej z innego zasobu w szablonie, należy użyć:

```json
reference('<ResourceName>').output.text
```

## <a name="work-with-outputs-from-cli-script"></a>Praca z wyjściami ze skryptu interfejsu wiersza polecenia

Różni się od skryptu wdrażania programu PowerShell, obsługa interfejsu WIERSZA POLECENIA/bash nie udostępnia wspólnej zmiennej do przechowywania wyjść skryptu, zamiast tego istnieje zmienna środowiskowa o nazwie **AZ_SCRIPTS_OUTPUT_PATH,** która przechowuje lokalizację, w której znajduje się plik wyjścia skryptu. Jeśli skrypt wdrożenia jest uruchamiany z szablonu Menedżera zasobów, ta zmienna środowiskowa jest ustawiana automatycznie przez powłokę Bash.

Wyjścia skryptu wdrażania muszą być zapisywane w lokalizacji AZ_SCRIPTS_OUTPUT_PATH, a dane wyjściowe muszą być prawidłowym obiektem ciągu JSON. Zawartość pliku musi być zapisana jako para klucz-wartość. Na przykład tablica ciągów jest przechowywana jako { "MyResult": [ "foo", "bar"] }.  Przechowywanie tylko wyników tablicy, na przykład [ "foo", "bar" ], jest nieprawidłowe.

:::code language="json" source="~/resourcemanager-templates/deployment-script/deploymentscript-basic-cli.json" range="1-44" highlight="32":::

[jq](https://stedolan.github.io/jq/) jest używany w poprzedniej próbce. Pochodzi z obrazów kontenera. Zobacz [Konfigurowanie środowiska programistycznego](#configure-development-environment).

## <a name="develop-deployment-scripts"></a>Tworzenie skryptów wdrażania

### <a name="handle-non-terminating-errors"></a>Obsługa błędów nieukańszących

Można kontrolować, jak program PowerShell reaguje na błędy nie kończące się przy użyciu [**zmiennej $ErrorActionPreference**](/powershell/module/microsoft.powershell.core/about/about_preference_variables?view=powershell-7#erroractionpreference
) w skrypcie wdrażania. Aparat skryptów wdrażania nie ustawia/nie zmienia wartości.  Pomimo wartości ustawionej dla $ErrorActionPreference skrypt wdrażania ustawia stan inicjowania obsługi administracyjnej zasobów *na Niepowodzenie,* gdy skrypt napotka błąd.

### <a name="pass-secured-strings-to-deployment-script"></a>Przekazywanie zabezpieczonych ciągów do skryptu wdrażania

Ustawienie zmiennych środowiskowych (EnvironmentVariable) w wystąpieniach kontenera umożliwia zapewnienie dynamicznej konfiguracji aplikacji lub skryptu uruchamianego przez kontener. Skrypt wdrażania obsługuje niezabezpieczonych i zabezpieczonych zmiennych środowiskowych w taki sam sposób jak wystąpienie kontenera platformy Azure. Aby uzyskać więcej informacji, zobacz [Ustawianie zmiennych środowiskowych w wystąpieniach kontenera](../../container-instances/container-instances-environment-variables.md#secure-values).

## <a name="debug-deployment-scripts"></a>Skrypty wdrażania debugowania

Usługa skryptu tworzy [konto magazynu](../../storage/common/storage-account-overview.md) i [wystąpienie kontenera](../../container-instances/container-instances-overview.md) do wykonywania skryptu. Oba zasoby mają **sufiks azscripts** w nazwach zasobów.

![Nazwy zasobów skryptu wdrażania szablonów Menedżera zasobów](./media/deployment-script-template/resource-manager-template-deployment-script-resources.png)

Skrypt użytkownika, wyniki wykonania i plik stdout są przechowywane w udziałach plików konta magazynu. Istnieje folder o nazwie **azscripts**. W folderze znajdują się jeszcze dwa foldery dla plików wejściowych i wyjściowych: **azscriptinput** i **azscriptoutput**.

Folder wyjściowy zawiera **plik executionresult.json** i plik wyjściowy skryptu. Komunikat o błędzie wykonywania skryptu można zobaczyć w **pliku executionresult.json**. Plik wyjściowy jest tworzony tylko wtedy, gdy skrypt jest wykonywany pomyślnie. Folder wejściowy zawiera systemowy plik skryptu programu PowerShell i pliki skryptów wdrażania użytkownika. Można zastąpić plik skryptu wdrażania użytkownika z poprawionym plikiem i ponownie uruchomić skrypt wdrażania z wystąpienia kontenera platformy Azure.

You can get the deployment script resource deployment information at the resource group level and the subscription level by using REST API:

```rest
/subscriptions/<SubscriptionID>/resourcegroups/<ResourceGroupName>/providers/microsoft.resources/deploymentScripts/<DeploymentScriptResourceName>?api-version=2019-10-01-preview
```

```rest
/subscriptions/<SubscriptionID>/providers/microsoft.resources/deploymentScripts?api-version=2019-10-01-preview
```

W poniższym przykładzie użyto [funkcji ARMClient:](https://github.com/projectkudu/ARMClient)

```azurepowershell
armclient login
armclient get /subscriptions/01234567-89AB-CDEF-0123-456789ABCDEF/resourcegroups/myrg/providers/microsoft.resources/deploymentScripts/myDeployementScript?api-version=2019-10-01-preview
```

Dane wyjściowe są podobne do następujących:

:::code language="json" source="~/resourcemanager-templates/deployment-script/deploymentscript-status.json" range="1-37" highlight="15,34":::

Dane wyjściowe pokazują stan wdrożenia i identyfikatory zasobów skryptu wdrażania.

Następujący interfejs API REST zwraca dziennik:

```rest
/subscriptions/<SubscriptionID>/resourcegroups/<ResourceGroupName>/providers/microsoft.resources/deploymentScripts/<DeploymentScriptResourceName>/logs?api-version=2019-10-01-preview
```

Działa tylko przed usunięciem zasobów skryptu wdrażania.

Aby wyświetlić zasób deploymentScripts w portalu, wybierz pozycję **Pokaż ukryte typy:**

![Skrypt wdrażania szablonu Menedżera zasobów, pokaż ukryte typy, portal](./media/deployment-script-template/resource-manager-deployment-script-portal-show-hidden-types.png)

## <a name="clean-up-deployment-script-resources"></a>Oczyszczanie zasobów skryptu wdrażania

Skrypt wdrażania tworzy konto magazynu i wystąpienie kontenera, które są używane do wykonywania skryptów wdrażania i przechowywania informacji debugowania. Te dwa zasoby są tworzone w tej samej grupie zasobów co zasoby aprowizacji i zostaną usunięte przez usługę skryptu po wygaśnięciu skryptu. Można kontrolować cykl życia tych zasobów.  Dopóki nie zostaną usunięte, są naliczane dla obu zasobów. Aby uzyskać informacje o cenie, zobacz [Ceny wystąpień kontenerów](https://azure.microsoft.com/pricing/details/container-instances/) i [cennik usługi Azure Storage](https://azure.microsoft.com/pricing/details/storage/).

Cykl życia tych zasobów jest kontrolowany przez następujące właściwości w szablonie:

- **cleanupPreference**: Wyczyść preferencje, gdy wykonanie skryptu zostanie w stanie terminala.  Obsługiwane wartości to:

  - **Zawsze:** Usuń zasoby po wykonaniu skryptu dostaje w stanie terminala. Ponieważ zasób deploymentScripts może nadal znajdować się po oczyszczeniu zasobów, skrypt systemowy skopiuje wyniki wykonywania skryptu, na przykład stdout, dane wyjściowe, zwracaną wartość itp.
  - **OnSuccess**: Usuń zasoby tylko wtedy, gdy wykonanie skryptu zakończy się pomyślnie. Nadal można uzyskać dostęp do zasobów, aby znaleźć informacje debugowania.
  - **OnExpiration**: Usuń zasoby tylko wtedy, gdy **retencjiInterwale** ustawienie wygasło. Ta właściwość jest obecnie wyłączona.

- **retentionInterval**: Określ przedział czasu, w którym zasób skryptu zostanie zachowany i po którym zostanie wygaszony i usunięty.

> [!NOTE]
> Nie zaleca się używania zasobów skryptu wdrażania do innych celów.

## <a name="run-script-more-than-once"></a>Uruchamianie skryptu więcej niż jeden raz

Wykonanie skryptu wdrażania jest operacją idempotentną. Jeśli żadna z właściwości zasobu deploymentScripts (w tym skrypt wbudowany) nie zostanie zmieniona, skrypt nie zostanie wykonany po ponownej rozmieszczeniu szablonu. Usługa skryptu wdrażania porównuje nazwy zasobów w szablonie z istniejącymi zasobami w tej samej grupie zasobów. Istnieją dwie opcje, jeśli chcesz wykonać ten sam skrypt wdrażania wiele razy:

- Zmień nazwę zasobu deploymentScripts. Na przykład użyj funkcji szablonu [utcNow](./template-functions-string.md#utcnow) jako nazwy zasobu lub jako części nazwy zasobu. Zmiana nazwy zasobu powoduje utworzenie nowego zasobu deploymentScripts. Jest to dobre dla utrzymania historii wykonywania skryptu.

    > [!NOTE]
    > Funkcja utcNow może być używana tylko w wartości domyślnej parametru.

- Określ inną wartość `forceUpdateTag` we właściwości szablonu.  Na przykład użyj utcNow jako wartość.

> [!NOTE]
> Zapisz skrypty wdrażania, które są idempotentne. Gwarantuje to, że jeśli uruchomią się ponownie przypadkowo, nie spowoduje to zmian w systemie. Na przykład jeśli skrypt wdrażania jest używany do tworzenia zasobu platformy Azure, sprawdź, że zasób nie istnieje przed jego utworzeniem, więc skrypt zakończy się pomyślnie lub nie utworzysz zasobu ponownie.

## <a name="configure-development-environment"></a>Konfigurowanie środowiska programowania

Można użyć wstępnie skonfigurowanego obrazu kontenera platformy docker jako środowiska programistycznego skryptu wdrażania. Poniższa procedura pokazuje, jak skonfigurować obraz docker w systemie Windows. W przypadku systemów Linux i Mac można znaleźć informacje w Internecie.

1. Zainstaluj [program Docker Desktop](https://www.docker.com/products/docker-desktop) na komputerze deweloperskim.
1. Otwórz pulpit platformy Docker.
1. Wybierz ikonę Pulpit platformy Docker z paska zadań, a następnie wybierz pozycję **Ustawienia**.
1. Wybierz **pozycję Dyski udostępnione,** wybierz dysk lokalny, który ma być dostępny dla kontenerów, a następnie wybierz pozycję **Zastosuj**

    ![Dysk dokowy skryptu wdrażania szablonu Menedżera zasobów](./media/deployment-script-template/resource-manager-deployment-script-docker-setting-drive.png)

1. Wprowadź poświadczenia systemu Windows w wierszu polecenia.
1. Otwórz okno terminala, wiersz polecenia lub program Windows PowerShell (nie należy używać programu PowerShell ISE).
1. Pociągnij obraz kontenera skryptu wdrożenia do komputera lokalnego:

    ```command
    docker pull mcr.microsoft.com/azuredeploymentscripts-powershell:az2.7
    ```

    W przykładzie użyto wersji programu PowerShell 2.7.0.

    Aby wyciągnąć obraz interfejsu wiersza polecenia z rejestru kontenerów firmy Microsoft (MCR):

    ```command
    docker pull mcr.microsoft.com/azure-cli:2.0.80
    ```

    W tym przykładzie użyto wersji CLI 2.0.80. Skrypt wdrażania używa domyślnych obrazów kontenerów interfejsu [wiersza](https://hub.docker.com/_/microsoft-azure-cli)polecenia znalezionych w tym miejscu .

1. Uruchom obraz docker lokalnie.

    ```command
    docker run -v <host drive letter>:/<host directory name>:/data -it mcr.microsoft.com/azuredeploymentscripts-powershell:az2.7
    ```

    Zastąp ** &lt;>listu sterownika hosta** i ** &lt;nazwę katalogu hosta>** istniejącym folderem na dysku udostępnionym.  Mapuje folder do **folderu /data** w kontenerze. Przykłady do mapowania D:\docker:

    ```command
    docker run -v d:/docker:/data -it mcr.microsoft.com/azuredeploymentscripts-powershell:az2.7
    ```

    **-oznacza to** utrzymanie obrazu kontenera przy życiu.

    Przykład interfejsu wiersza polecenia:

    ```command
    docker run -v d:/docker:/data -it mcr.microsoft.com/azure-cli:2.0.80
    ```

1. Wybierz **pozycję Udostępnij po** wyświetleniu monitu.
1. Poniższy zrzut ekranu pokazuje, jak uruchomić skrypt programu PowerShell, biorąc pod uwagę, że masz plik helloworld.ps1 w folderze d:\docker.

    ![Dokowane skryptu wdrażania szablonu Menedżera zasobów](./media/deployment-script-template/resource-manager-deployment-script-docker-cmd.png)

Po pomyślnym przetestowaniu skryptu można go użyć jako skryptu wdrażania.

## <a name="next-steps"></a>Następne kroki

W tym artykule dowiesz się, jak używać skryptów wdrażania. Aby przejść przez samouczek skryptu wdrażania:

> [!div class="nextstepaction"]
> [Samouczek: używanie skryptów wdrażania w szablonach usługi Azure Resource Manager](./template-tutorial-deployment-script.md)