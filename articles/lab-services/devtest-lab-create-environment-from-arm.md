---
title: Tworzenie środowisk wielu maszyn wirtualnych i zasobów PaaS za pomocą szablonów
description: Dowiedz się, jak tworzyć środowiska wielu maszyn wirtualnych i zasoby PaaS w laboratoriach devtest platformy Azure na podstawie szablonu usługi Azure Resource Manager
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.assetid: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/16/2020
ms.author: spelluru
ms.openlocfilehash: 1385b20847cf90c212a13591389dfb6cda08432a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76169648"
---
# <a name="create-multi-vm-environments-and-paas-resources-with-azure-resource-manager-templates"></a>Tworzenie środowisk z wieloma maszynami wirtualnymi i zasobów PaaS za pomocą szablonów usługi Azure Resource Manager

Środowiska Azure DevTest Labs umożliwiają użytkownikom łatwe wdrażanie złożonych infrastruktur w spójny sposób w obrębie laboratorium. Szablony [usługi Azure Resource Manager](../azure-resource-manager/templates/template-syntax.md) umożliwiają tworzenie środowisk z zestawami zasobów w laboratoriach DevTest Labs. Te środowiska mogą zawierać wszystkie zasoby platformy Azure, które mogą tworzyć szablony Usługi Resource Manager.

Można łatwo [dodać jedną maszynę wirtualną (VM) naraz](devtest-lab-add-vm.md) do laboratorium przy użyciu [portalu Azure.](https://portal.azure.com) Jednak scenariusze, takie jak wielowarstwowe aplikacje sieci web lub farma programu SharePoint, wymagają mechanizmu tworzenia wielu maszyn wirtualnych w jednym kroku. Za pomocą szablonów usługi Azure Resource Manager, można zdefiniować infrastrukturę i konfigurację rozwiązania platformy Azure i wielokrotnie wdrażać wiele maszyn wirtualnych w spójnym stanie.

Szablony usługi Azure Resource Manager zapewniają również następujące korzyści:

- Szablony usługi Azure Resource Manager są ładowane bezpośrednio z repozytorium kontroli źródła usługi GitHub lub usługi Azure Repozytorium.
- Użytkownicy mogą utworzyć środowisko, wybierając skonfigurowany szablon usługi Azure Resource Manager z witryny Azure portal, podobnie jak w przypadku innych typów [baz maszyn wirtualnych.](devtest-lab-comparing-vm-base-image-types.md)
- Można aprowizować zasoby usługi Azure PaaS, a także maszyny wirtualne IaaS w środowisku z szablonu usługi Azure Resource Manager.
- Można śledzić koszt środowisk w laboratorium, oprócz poszczególnych maszyn wirtualnych utworzonych przez inne typy baz. Zasoby PaaS są tworzone i będą wyświetlane w śledzeniu kosztów. Automatyczne zamykanie maszyny Wirtualnej nie ma jednak zastosowania do zasobów PaaS.

Aby dowiedzieć się więcej o korzyściach płynących z wdrażania, aktualizowania lub usuwania wielu zasobów laboratoryjnych w ramach jednej operacji za pomocą [szablonów Menedżera zasobów.](../azure-resource-manager/management/overview.md#the-benefits-of-using-resource-manager)

> [!NOTE]
> Podczas tworzenia maszyn wirtualnych w laboratorium szablonu Menedżera zasobów jest korzystać z szablonu Menedżera zasobów, istnieją pewne różnice między tworzeniem wielu maszyn wirtualnych lub pojedynczej maszyny wirtualnej. Aby uzyskać więcej informacji, zobacz [Używanie szablonu usługi Azure Resource Manager maszyny wirtualnej](devtest-lab-use-resource-manager-template.md).
>

## <a name="use-devtest-labs-public-environments"></a>Korzystanie z środowisk publicznych DevTest Labs
Usługa Azure DevTest Labs ma [publiczne repozytorium szablonów usługi Azure Resource Manager,](https://github.com/Azure/azure-devtestlab/tree/master/Environments) których można używać do tworzenia środowisk bez konieczności samodzielnego łączenia się z zewnętrznym źródłem usługi GitHub. To publiczne repozytorium jest podobne do publicznego repozytorium artefaktów, które jest dostępne w witrynie Azure portal dla każdego laboratorium, które tworzysz. Repozytorium środowiska umożliwia szybkie rozpoczęcie pracy z wstępnie utworzone szablony środowiska, które mają kilka parametrów wejściowych. Te szablony zapewniają płynne rozpoczęcie pracy dla zasobów PaaS w laboratoriach.

W repozytorium publicznym zespół DevTest Labs i inne osoby utworzyły i udostępniły często używane szablony, takie jak usługi Azure Web Apps, klaster sieci szkieletowej usług i środowisko farmy programu SharePoint. Możesz użyć tych szablonów bezpośrednio lub dostosować je do własnych potrzeb. Aby uzyskać więcej informacji, zobacz [Konfigurowanie i używanie środowisk publicznych w laboratoriach DevTest](devtest-lab-configure-use-public-environments.md). Po utworzeniu własnych szablonów możesz przechowywać je w tym repozytorium, aby udostępnić je innym osobom, lub skonfigurować własne repozytorium Git.

<a name="configure-your-own-template-repositories"></a>
## <a name="create-your-own-template-repositories"></a>Tworzenie własnych repozytoriów szablonów

Jako jeden z najlepszych rozwiązań z infrastruktury jako kodu i konfiguracji jako kodu, należy zarządzać szablonów środowiska w kontroli źródła. Laboratorium devtest platformy Azure stosuje tę praktykę i ładuje wszystkie szablony usługi Azure Resource Manager bezpośrednio z repozytoriów repozytoriów usługi GitHub lub repozytorium platformy Azure. W rezultacie można użyć szablonów Menedżera zasobów w całym cyklu wersji, od środowiska testowego do środowiska produkcyjnego.

Istnieje kilka reguł, których należy przestrzegać, aby zorganizować szablony usługi Azure Resource Manager w repozytorium:

- Musisz nadać nazwę plikowi szablonu *wzorcowego azuredeploy.json*.

- Jeśli chcesz użyć wartości parametrów zdefiniowanych w pliku parametrów, plik parametru musi mieć nazwę *azuredeploy.parameters.json*.

  Można użyć `_artifactsLocation` parametrów `_artifactsLocationSasToken` i skonstruować parametersLink URI wartość, dzięki czemu DevTest Labs automatycznie zarządzać szablonami zagnieżdżonych. Aby uzyskać więcej informacji, zobacz [Wdrażanie zagnieżdżonych szablonów usługi Azure Resource Manager w środowiskach testowych](deploy-nested-template-environments.md).

- Metadane można zdefiniować w celu określenia nazwy i opisu wyświetlanego szablonu w pliku o nazwie *metadata.json*w następujący sposób:

  ```json
  {
    "itemDisplayName": "<your template name>",
    "description": "<description of the template>"
  }
  ```

![Kluczowe pliki szablonów usługi Azure Resource Manager](./media/devtest-lab-create-environment-from-arm/master-template.png)

## <a name="add-template-repositories-to-the-lab"></a>Dodawanie repozytoriów szablonów do laboratorium

Po utworzeniu i skonfigurowaniu repozytorium można dodać je do laboratorium przy użyciu witryny Azure portal:

1. Zaloguj się do [Portalu Azure](https://portal.azure.com).
1. Wybierz **pozycję Wszystkie usługi**, a następnie wybierz z listy pozycję **DevTest Labs.**
1. Z listy laboratoriów wybierz laboratorium, które chcesz.
1. W okienku **Przegląd** laboratorium wybierz pozycję **Konfiguracja i zasady**.

   ![Konfiguracja i zasady](./media/devtest-lab-create-environment-from-arm/configuration-and-policies-menu.png)

1. Z listy **Ustawienia konfiguracji i zasad** wybierz pozycję **Repozytoria**. **Publiczne repozytorium repozytorium artefaktów** jest generowane automatycznie dla wszystkich laboratoriów i łączy się z [publicznym repozytorium GitHub w laboratoriach DevTest Labs.](https://github.com/Azure/azure-devtestlab)

1. Aby dodać repozytorium szablonów usługi Azure Resource Manager, wybierz pozycję **Dodaj**.

   ![Publiczne repozytorium](./media/devtest-lab-create-environment-from-arm/public-repo.png)

1. W okienku **Repozytoria** wprowadź następujące informacje:

   - **Nazwa**: Wprowadź nazwę repozytorium do użycia w laboratorium.
   - **Adres URL klonowania Git:** Wprowadź adres URL klonowania Git HTTPS z usługi GitHub lub Usługi Azure Repos.
   - **Gałąź** (opcjonalnie): Wprowadź nazwę oddziału, aby uzyskać dostęp do definicji szablonów usługi Azure Resource Manager.
   - **Osobisty token dostępu:** Wprowadź osobisty token dostępu, który jest używany do bezpiecznego dostępu do repozytorium.
     - Aby uzyskać token z repozytorium platformy Azure, w obszarze Twój profil wybierz **pozycję Ustawienia** > użytkownika**Tokeny** > **dostępu security personal**.
     - Aby uzyskać token z gitHub, w swoim profilu wybierz **ustawienia ustawień** > **Developer Settings** > **programisty tokeny dostępu osobistego**.
   - **Ścieżki folderów:** Wprowadź ścieżkę folderu względem identyfikatora URI klonu Git dla definicji artefaktów lub definicji szablonów usługi Azure Resource Manager.

1. Wybierz **pozycję Zapisz**.

   ![Dodawanie nowego repozytorium](./media/devtest-lab-create-environment-from-arm/repo-values.png)

Po dodaniu szablonu usługi Azure Resource Manager do laboratorium użytkownicy laboratorium mogą tworzyć środowiska przy użyciu szablonu.

## <a name="configure-access-rights-for-lab-users"></a>Konfigurowanie praw dostępu dla użytkowników laboratorium

Użytkownicy laboratorium mają domyślnie rolę **czytnika,** więc nie mogą zmienić zasobów w grupie zasobów środowiska. Na przykład nie mogą zatrzymać lub uruchomić swoje zasoby.

Aby nadać użytkownikom laboratorium rolę **współautora,** aby mogli edytować zasoby w swoich środowiskach, wykonaj następujące kroki:

1. W [witrynie Azure portal](https://portal.azure.com)w okienku **Przegląd** laboratorium wybierz pozycję **Konfiguracja i zasady**, a następnie wybierz pozycję **Ustawienia laboratorium**.

1. W okienku **Ustawienia laboratorium** wybierz pozycję **Współautor**, a następnie wybierz pozycję **Zapisz,** aby przyznać uprawnienia do zapisu użytkownikom laboratorium.

   ![Konfigurowanie praw dostępu użytkowników w laboratorium](./media/devtest-lab-create-environment-from-arm/config-access-rights.png)

Następna sekcja przechodzi przez tworzenie środowisk z szablonu usługi Azure Resource Manager.

## <a name="create-environments-from-templates-in-the-azure-portal"></a>Tworzenie środowisk na podstawie szablonów w witrynie Azure portal

Po dodaniu szablonu usługi Azure Resource Manager do laboratorium użytkownicy laboratorium mogą tworzyć środowiska w witrynie Azure portal, wykonując następujące kroki:

1. Zaloguj się do [Portalu Azure](https://portal.azure.com).

1. Wybierz **pozycję Wszystkie usługi**, a następnie wybierz z listy pozycję **DevTest Labs.**

1. Z listy laboratoriów wybierz laboratorium, które chcesz.

1. Na stronie laboratorium wybierz pozycję **Dodaj**.

1. W **okienku Wybierz bazę** wyświetlane są obrazy podstawowe, których można użyć, a szablony usługi Azure Resource Manager zostaną wyświetlone jako pierwsze. Wybierz odpowiedni szablon usługi Azure Resource Manager.

   ![Wybieranie bazy](./media/devtest-lab-create-environment-from-arm/choose-a-base.png)

1. W okienku **Dodawanie** wprowadź wartość **nazwy środowiska,** która ma być wyświetlana użytkownikom środowiska.

   Szablon usługi Azure Resource Manager definiuje pozostałe pola wejściowe. Jeśli szablon *azuredeploy.parameter.json* plik definiuje wartości domyślne, pola wejściowe pokaż te wartości.

   W przypadku parametrów typu *bezpieczny ciąg*można użyć wpisów tajnych z usługi Azure Key Vault. Aby dowiedzieć się więcej o przechowywaniu wpisów tajnych w magazynie kluczy i używaniu ich podczas tworzenia zasobów laboratoryjnych, zobacz [Przechowywanie wpisów tajnych w usłudze Azure Key Vault.](devtest-lab-store-secrets-in-key-vault.md)  

   ![Dodawanie okienka](./media/devtest-lab-create-environment-from-arm/add.png)

   > [!NOTE]
   > Następujące wartości parametrów nie są wyświetlane w polach wejściowych, nawet jeśli szablon je określa. Zamiast tego formularz pokazuje puste pola wejściowe, w których użytkownicy laboratorium muszą wprowadzać wartości podczas tworzenia środowiska.
   >
   > - GEN-UNIQUE
   > - GEN-UNIQUE-[N]
   > - GEN-SSH-PUB-KLUCZ
   > - GEN-HASŁO

1. Wybierz **pozycję Dodaj,** aby utworzyć środowisko.

   Środowisko rozpoczyna inicjowanie obsługi administracyjnej natychmiast, ze stanem wyświetlanym na liście **Moje maszyny wirtualne.** Laboratorium automatycznie tworzy nową grupę zasobów do aprowizowania wszystkich zasobów zdefiniowanych w szablonie usługi Azure Resource Manager.

1. Po utworzeniu środowiska wybierz środowisko na liście **Moje maszyny wirtualne,** aby otworzyć okienko grupy zasobów i przeglądać wszystkie zasoby aprowizacji środowiska.

   ![Zasoby środowiskowe](./media/devtest-lab-create-environment-from-arm/all-environment-resources.png)

   Można również rozwinąć środowisko, aby wyświetlić tylko listę maszyn wirtualnych środowiska aprowizowanego.

   ![Lista moich maszyn wirtualnych](./media/devtest-lab-create-environment-from-arm/my-vm-list.png)

1. Wybierz dowolne ze środowisk, aby wyświetlić dostępne akcje, takie jak stosowanie artefaktów, dołączanie dysków z danymi, zmiana czasu automatycznego zamykania i inne.

   ![Działania środowiskowe](./media/devtest-lab-create-environment-from-arm/environment-actions.png)

<a name="automate-deployment-of-environments"></a>
## <a name="automate-environment-creation-with-powershell"></a>Automatyzacja tworzenia środowiska za pomocą programu PowerShell

Jest to możliwe, aby użyć witryny Azure portal, aby dodać jedno środowisko do laboratorium, ale gdy scenariusz rozwoju lub testowania musi utworzyć wiele środowisk, automatyczne wdrażanie jest lepsze środowisko.

Przed kontynuowaniem upewnij się, że masz szablon usługi Azure Resource Manager, który definiuje zasoby do utworzenia. [Dodaj i skonfiguruj szablon w repozytorium Git](#configure-your-own-template-repositories)i [dodaj repozytorium do laboratorium.](#add-template-repositories-to-the-lab)

Poniższy przykładowy skrypt tworzy środowisko w laboratorium. Komentarze pomagają lepiej zrozumieć skrypt.

1. Zapisz poniższy przykładowy skrypt programu PowerShell na dysku twardym jako *deployenv.ps1*.

   [!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

   ```powershell
   #Requires -Module Az.Resources

   [CmdletBinding()]

   param (
   # ID of the Azure Subscription for the lab
   [string] [Parameter(Mandatory=$true)] $SubscriptionId,

   # Name of the existing lab in which to create the environment
   [string] [Parameter(Mandatory=$true)] $LabName,

   # Name of the connected repository in the lab
   [string] [Parameter(Mandatory=$true)] $RepositoryName,

   # Name of the template (folder name in the Git repository)
   [string] [Parameter(Mandatory=$true)] $TemplateName,

   # Name of the environment to be created in the lab
   [string] [Parameter(Mandatory=$true)] $EnvironmentName,

   # The parameters to be passed to the template. Each parameter is prefixed with "-param_".
   # For example, if the template has a parameter named "TestVMName" with a value of "MyVMName",
   # the string in $Params will have the form: -param_TestVMName MyVMName.
   # This convention allows the script to dynamically handle different templates.
   [Parameter(ValueFromRemainingArguments=$true)]
       $Params
   )

   # Sign in to Azure.
   # Comment out the following statement to completely automate the environment creation.
   Connect-AzAccount

   # Select the subscription that has the lab.  
   Set-AzContext -SubscriptionId $SubscriptionId | Out-Null

   # Get information about the user, specifically the user ID, which is used later in the script.  
   $UserId = $((Get-AzADUser -UserPrincipalName (Get-AzContext).Account).Id.Guid)

   # Get information about the lab, such as lab location.
   $lab = Get-AzResource -ResourceType "Microsoft.DevTestLab/labs" -Name $LabName -ResourceGroupName $ResourceGroupName
   if ($lab -eq $null) { throw "Unable to find lab $LabName in subscription $SubscriptionId." }

   # Get information about the repository in the lab.
   $repository = Get-AzResource -ResourceGroupName $lab.ResourceGroupName `
       -ResourceType 'Microsoft.DevTestLab/labs/artifactsources' `
       -ResourceName $LabName `
       -ApiVersion 2016-05-15 `
       | Where-Object { $RepositoryName -in ($_.Name, $_.Properties.displayName) } `
       | Select-Object -First 1
   if ($repository -eq $null) { throw "Unable to find repository $RepositoryName in lab $LabName." }

   # Get information about the Resource Manager template base for the environment.
   $template = Get-AzResource -ResourceGroupName $lab.ResourceGroupName `
       -ResourceType "Microsoft.DevTestLab/labs/artifactSources/armTemplates" `
       -ResourceName "$LabName/$($repository.Name)" `
       -ApiVersion 2016-05-15 `
       | Where-Object { $TemplateName -in ($_.Name, $_.Properties.displayName) } `
       | Select-Object -First 1
   if ($template -eq $null) { throw "Unable to find template $TemplateName in lab $LabName." }

   # Build the template parameters with parameter name and values.  
   $parameters = Get-Member -InputObject $template.Properties.contents.parameters -MemberType NoteProperty | Select-Object -ExpandProperty Name
   $templateParameters = @()

   # Extract the custom parameters from $Params and format as name/value pairs.
   $Params | ForEach-Object {
       if ($_ -match '^-param_(.*)' -and $Matches[1] -in $parameters) {
           $name = $Matches[1]                
       } elseif ( $name ) {
           $templateParameters += @{ "name" = "$name"; "value" = "$_" }
           $name = $null #reset name variable
       }
   }

   # Once name/value pairs are isolated, create an object to hold the necessary template properties.
   $templateProperties = @{ "deploymentProperties" = @{ "armTemplateId" = "$($template.ResourceId)"; "parameters" = $templateParameters }; }

   # Now, create or deploy the environment in the lab by using the New-AzResource command.
   New-AzResource -Location $Lab.Location `
       -ResourceGroupName $lab.ResourceGroupName `
       -Properties $templateProperties `
       -ResourceType 'Microsoft.DevTestLab/labs/users/environments' `
       -ResourceName "$LabName/$UserId/$EnvironmentName" `
       -ApiVersion '2016-05-15' -Force

   Write-Output "Environment $EnvironmentName completed."
   ```

1. Uruchom skrypt w następujący sposób, używając określonych wartości dla SubscriptionId, LabName, ResourceGroupName, RepositoryName, TemplateName (folder w repozytorium Git) i EnvironmentName.

   ```powershell
   ./deployenv.ps1 -SubscriptionId "000000000-0000-0000-0000-0000000000000" -LabName "mydevtestlab" -ResourceGroupName "mydevtestlabRG000000" -RepositoryName "myRepository" -TemplateName "My Environment template name" -EnvironmentName "myGroupEnv"
   ```

Można również użyć interfejsu wiersza polecenia platformy Azure do wdrażania zasobów za pomocą szablonów Menedżera zasobów. Aby uzyskać więcej informacji, zobacz [Wdrażanie zasobów za pomocą szablonów Usługi Resource Manager i interfejsu wiersza polecenia platformy Azure](../azure-resource-manager/templates/deploy-cli.md).

> [!NOTE]
> Tylko użytkownik z uprawnieniami właściciela laboratorium może tworzyć maszyny wirtualne z szablonu Menedżera zasobów przy użyciu programu Azure PowerShell. Jeśli chcesz zautomatyzować tworzenie maszyn wirtualnych przy użyciu szablonu Menedżera zasobów i masz tylko uprawnienia użytkownika, możesz użyć polecenia CLI [az lab vm create](/cli/azure/lab/vm#az-lab-vm-create).

## <a name="resource-manager-template-limitations-in-devtest-labs"></a>Ograniczenia szablonu Menedżera zasobów w laboratoriach DevTest

Należy wziąć pod uwagę te ograniczenia podczas korzystania z szablonów Menedżera zasobów w DevTest Labs:

- Szablony Menedżera zasobów nie mogą odwoływać się do większości istniejących zasobów. Mogą tworzyć tylko nowe zasoby. Jeśli masz szablony Menedżera zasobów używane poza DevTest Labs, które odwołują się do istniejących zasobów, nie można ich używać w DevTest Labs. Jedynym wyjątkiem jest możliwość odwoływania się do istniejącej sieci wirtualnej.

- Nie można tworzyć formuł ani obrazów niestandardowych z maszyn wirtualnych laboratoryjnych utworzonych na podstawie szablonu Menedżera zasobów.

- Większość zasad nie jest oceniana podczas wdrażania szablonów Menedżera zasobów.

  Na przykład może mieć zasady laboratorium, które użytkownik może utworzyć tylko pięć maszyn wirtualnych. Jednak użytkownik może wdrożyć szablon Menedżera zasobów, który tworzy dziesiątki maszyn wirtualnych. Zasady, które nie są oceniane obejmują:

  - Liczba maszyn wirtualnych na użytkownika

  - Liczba maszyn wirtualnych premium na użytkownika laboratorium

  - Liczba dysków premium na użytkownika laboratorium

## <a name="next-steps"></a>Następne kroki
- Po utworzeniu maszyny Wirtualnej można połączyć się z maszyną wirtualną, wybierając **pozycję Połącz** w okienku zarządzania maszyny Wirtualnej.
- Wyświetlanie zasobów w środowisku i zarządzanie nimi przez wybranie środowiska na liście **Moje maszyny wirtualne** w laboratorium.
- Zapoznaj się [z szablonami usługi Azure Resource Manager z galerii szablonów Szybki start platformy Azure](https://github.com/Azure/azure-quickstart-templates).
