---
title: Zarządzanie modułami w usłudze Azure Automation
description: W tym artykule opisano sposób zarządzania modułami w usłudze Azure Automation
services: automation
ms.service: automation
author: mgoedtel
ms.author: magoedte
ms.date: 01/31/2020
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: e300bc0f29808215673407d21b65fe329e50ad45
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79278339"
---
# <a name="manage-modules-in-azure-automation"></a>Zarządzanie modułami w usłudze Azure Automation

Usługa Azure Automation umożliwia importowanie modułów programu PowerShell do konta automatyzacji, które mają być używane przez elementy runbook oparte na programie PowerShell. Te moduły mogą być modułami niestandardowymi utworzonymi z galerii programu PowerShell lub modułów AzureRM i Az dla platformy Azure. Podczas tworzenia konta automatyzacji niektóre moduły są importowane domyślnie.

## <a name="import-modules"></a>Importowanie modułów

Istnieje wiele sposobów, które można zaimportować moduł do konta automatyzacji. W poniższych sekcjach przedstawiono różne sposoby importowania modułu.

> [!NOTE]
> Maksymalna ścieżka pliku w module, który ma być używany w usłudze Azure Automation, to 140 znaków. Żadna ścieżka powyżej 140 znaków nie będzie mogła zostać zaimportowana do sesji programu PowerShell z plikiem `Import-Module`.

### <a name="powershell"></a>PowerShell

Za pomocą [new-AzureRmAutomationModule](/powershell/module/azurerm.automation/new-azurermautomationmodule) można zaimportować moduł do konta automatyzacji. Polecenie cmdlet pobiera adres URL do pakietu zip modułu.

```azurepowershell-interactive
New-AzureRmAutomationModule -Name <ModuleName> -ContentLinkUri <ModuleUri> -ResourceGroupName <ResourceGroupName> -AutomationAccountName <AutomationAccountName>
```

Można również użyć tego samego polecenia cmdlet, aby bezpośrednio zaimportować moduł z galerii programu PowerShell. Upewnij się, że **pobierzesz ModuleName** i **ModuleVersion** z [programu PowerShell Gallery](https://www.powershellgallery.com).

```azurepowershell-interactive
$moduleName = <ModuleName>
$moduleVersion = <ModuleVersion>
New-AzAutomationModule -AutomationAccountName <AutomationAccountName> -ResourceGroupName <ResourceGroupName> -Name $moduleName -ContentLinkUri "https://www.powershellgallery.com/api/v2/package/$moduleName/$moduleVersion"
```

### <a name="azure-portal"></a>Portal Azure

W witrynie Azure portal przejdź do konta automatyzacji i wybierz **pozycję Moduły** w obszarze **Zasoby udostępnione**. Kliknij **+ Dodaj moduł**. Wybierz plik **zip** zawierający moduł i kliknij przycisk **Ok,** aby rozpocząć proces importowania.

### <a name="powershell-gallery"></a>Galeria programu PowerShell

Moduły z galerii programu PowerShell można zaimportować z [galerii programu PowerShell](https://www.powershellgallery.com) bezpośrednio lub z konta automatyzacji.

Aby zaimportować moduł z Galerii https://www.powershellgallery.com programu PowerShell, przejdź do i wyszukaj moduł, który chcesz zaimportować. Kliknij **pozycję Wdrażanie w usłudze Azure Automation** na karcie **Automatyzacja platformy Azure** w obszarze Opcje **instalacji**. Ta akcja otwiera witrynę Azure portal. Na stronie **Importowanie** wybierz konto automatyzacji i kliknij przycisk **OK**.

![Moduł importu galerii programu PowerShell](../media/modules/powershell-gallery.png)

Można również zaimportować moduły z galerii programu PowerShell bezpośrednio z konta automatyzacji. Na koncie automatyzacji wybierz pozycję **Moduły** w obszarze **Zasoby udostępnione**. Na stronie modułów kliknij pozycję **Przeglądaj galerię**, a następnie wyszukaj moduł w Galerii programu PowerShell. Wybierz moduł, który chcesz zaimportować, a następnie kliknij przycisk **Importuj**. Na stronie **Importuj** kliknij przycisk **OK,** aby rozpocząć proces importowania.

![Importowanie galerii programu PowerShell z witryny Azure portal](../media/modules/gallery-azure-portal.png)

## <a name="delete-modules"></a>Usuwanie modułów

Jeśli masz problemy z modułem lub musisz przywrócić poprzednią wersję modułu, możesz go usunąć z konta automatyzacji. Nie można usunąć oryginalnej wersji [modułów domyślnych,](#default-modules) które są importowane podczas tworzenia konta automatyzacji. Jeśli moduł, który chcesz usunąć, jest nowszą wersją jednego z [zainstalowanych modułów domyślnych,](#default-modules) zostanie on przywróceniu wersji zainstalowanej przy za pomocą konta automatyzacji. W przeciwnym razie każdy moduł usunięty z konta automatyzacji zostanie usunięty.

### <a name="azure-portal"></a>Portal Azure

W witrynie Azure portal przejdź do konta automatyzacji i wybierz **pozycję Moduły** w obszarze **Zasoby udostępnione**. Wybierz moduł, który chcesz usunąć. Na stronie **Moduł** wybierz pozycję **Usuń**. Jeśli ten moduł jest jednym z [modułów domyślnych,](#default-modules)zostanie on wycofany do wersji, która była obecna podczas tworzenia konta automatyzacji.

### <a name="powershell"></a>PowerShell

Aby usunąć moduł za pośrednictwem programu PowerShell, uruchom następujące polecenie:

```azurepowershell-interactive
Remove-AzureRmAutomationModule -Name <moduleName> -AutomationAccountName <automationAccountName> -ResourceGroupName <resourceGroupName>
```

## <a name="internal-cmdlets"></a>Wewnętrzne polecenia cmdlet

Poniżej znajduje się lista poleceń cmdlet w module wewnętrznym, `Orchestrator.AssetManagement.Cmdlets` który jest importowany do każdego konta automatyzacji. Te polecenia cmdlet są dostępne w konfiguracjach cząstek i DSC i umożliwiają interakcję z zasobami na koncie automatyzacji. Ponadto wewnętrzne polecenia cmdlet umożliwiają pobieranie wpisów tajnych z zaszyfrowanych wartości **zmiennych,** **poświadczeń**i zaszyfrowanych pól **Połączenia.** Polecenia cmdlet programu Azure PowerShell nie są w stanie pobrać tych wpisów tajnych. Te polecenia cmdlet nie wymagają niejawnego łączenia się z platformą Azure podczas ich używania, na przykład przy użyciu konta Uruchom jako do uwierzytelniania na platformie Azure.

>[!NOTE]
>Te wewnętrzne polecenia cmdlet są dostępne w usłudze Windows Hybrid Runbook Worker, nie są dostępne w usłudze Linux Hybrid Runbook Worker. Użyj odpowiednich [modułów AzureRM.Automation](https://docs.microsoft.com/powershell/module/AzureRM.Automation/?view=azurermps-6.13.0) lub [Az](../az-modules.md) dla życiówek działających bezpośrednio na komputerze lub względem zasobów w danym środowisku. 
>

|Nazwa|Opis|
|---|---|
|Get-AutomationCertificate|`Get-AutomationCertificate [-Name] <string> [<CommonParameters>]`|
|Get-AutomationConnection|`Get-AutomationConnection [-Name] <string> [-DoNotDecrypt] [<CommonParameters>]` |
|Get-AutomationPSCredential|`Get-AutomationPSCredential [-Name] <string> [<CommonParameters>]` |
|Get-AutomationVariable|`Get-AutomationVariable [-Name] <string> [-DoNotDecrypt] [<CommonParameters>]`|
|Set-AutomationVariable|`Set-AutomationVariable [-Name] <string> -Value <Object> [<CommonParameters>]` |
|Start-AutomationRunbook|`Start-AutomationRunbook [-Name] <string> [-Parameters <IDictionary>] [-RunOn <string>] [-JobId <guid>] [<CommonParameters>]`|
|Wait-AutomationOb|`Wait-AutomationJob -Id <guid[]> [-TimeoutInMinutes <int>] [-DelayInSeconds <int>] [-OutputJobsTransitionedToRunning] [<CommonParameters>]`|

## <a name="add-a-connection-type-to-your-module"></a>Dodawanie typu połączenia do modułu

Możesz podać niestandardowy [typ połączenia,](../automation-connections.md) którego można używać na koncie automatyzacji, dodając opcjonalny plik do modułu. Ten plik jest plikiem metadanych określającym typ połączenia usługi Azure Automation, który ma być używany z poleceniami cmdlet modułu na koncie automatyzacji. Aby to osiągnąć, należy najpierw wiedzieć, jak autor modułu programu PowerShell. Aby uzyskać więcej informacji na temat tworzenia modułów, zobacz [Jak napisać moduł skryptu programu PowerShell](/powershell/scripting/developer/module/how-to-write-a-powershell-script-module).

![Używanie połączenia niestandardowego w witrynie Azure portal](../media/modules/connection-create-new.png)

Aby dodać typ połączenia usługi Azure Automation, moduł `<ModuleName>-Automation.json` musi zawierać plik o nazwie określającej właściwości typu połączenia. Plik json jest umieszczany w folderze modułu skompresowanego pliku zip. Ten plik zawiera pola połączenia, które jest wymagane do połączenia z systemem lub usługą, którą reprezentuje moduł. Konfiguracja kończy się tworzenie typu połączenia w usłudze Azure Automation. Za pomocą tego pliku można ustawić nazwy pól, typy i czy pola powinny być szyfrowane lub opcjonalne dla typu połączenia modułu. Poniższy przykład jest szablonem w formacie pliku json, który definiuje właściwość nazwy użytkownika i hasła:

```json
{
   "ConnectionFields": [
   {
      "IsEncrypted":  false,
      "IsOptional":  true,
      "Name":  "Username",
      "TypeName":  "System.String"
   },
   {
      "IsEncrypted":  true,
      "IsOptional":  false,
      "Name":  "Password",
      "TypeName":  "System.String"
   }
   ],
   "ConnectionTypeName":  "MyModuleConnection",
   "IntegrationModuleName":  "MyModule"
}
```

## <a name="module-best-practices"></a>Najważniejsze wskazówki dotyczące modułów

Moduły programu PowerShell można zaimportować do usługi Azure Automation, aby ich polecenia cmdlet były dostępne do użytku w ramach śmiób ekwiomowych i ich zasobów DSC do użytku w konfiguracjach DSC. W tle usługa Azure Automation przechowuje te moduły, a w zadaniu runbook i czas wykonywania zadania kompilacji DSC ładuje je do skrzynek izolowania usługi Azure Automation, w których są wykonywane elementy runbook i kompiluje konfiguracje DSC. Wszystkie zasoby DSC w modułach są również automatycznie umieszczane na serwerze ściągania DSC automatyzacji. Mogą być ciągnięte przez maszyny, gdy stosują konfiguracje DSC.

Zaleca się, aby wziąć pod uwagę następujące podczas tworzenia modułu programu PowerShell do użycia w usłudze Azure Automation:

* NIE dołączaj folderu wersji do pakietu .zip.  Ten problem jest mniej problemem dla śmięty, ale spowoduje problem z usługą konfiguracji stanu.  Usługa Azure Automation automatycznie utworzy folder wersji, gdy moduł jest dystrybuowany do węzłów zarządzanych przez DSC, a jeśli istnieje folder wersji, skończysz z dwoma wystąpieniami.  Przykładowa struktura folderów dla modułu DSC:

```powershell
myModule
  - DSCResources
    - myResourceFolder
      myResourceModule.psm1
      myResourceSchema.mof
  myModuleManifest.psd1
```

* Dodaj streszczenie, opis i identyfikator URI pomocy do każdego polecenia cmdlet w module. W programie PowerShell możesz zdefiniować pewne informacje pomocy dotyczące poleceń cmdlet, aby umożliwić użytkownikowi uzyskanie pomocy na temat korzystania z nich dzięki poleceniu cmdlet **Get-Help**. W poniższym przykładzie pokazano, jak zdefiniować streszczenie i pomóc identyfikatora URI w pliku modułu psm1:

  ```powershell
  <#
       .SYNOPSIS
        Gets a Contoso User account
  #>
  function Get-ContosoUser {
  [CmdletBinding](DefaultParameterSetName='UseConnectionObject', `
  HelpUri='https://www.contoso.com/docs/information')]
  [OutputType([String])]
  param(
     [Parameter(ParameterSetName='UserAccount', Mandatory=true)]
     [ValidateNotNullOrEmpty()]
     [string]
     $UserName,

     [Parameter(ParameterSetName='UserAccount', Mandatory=true)]
     [ValidateNotNullOrEmpty()]
     [string]
     $Password,

     [Parameter(ParameterSetName='ConnectionObject', Mandatory=true)]
     [ValidateNotNullOrEmpty()]
     [Hashtable]
     $Connection
  )

  switch ($PSCmdlet.ParameterSetName) {
     "UserAccount" {
        $cred = New-Object –TypeName System.Management.Automation.PSCredential –ArgumentList $UserName, $Password
        Connect-Contoso -Credential $cred
     }
     "ConnectionObject" {
        Connect-Contoso -Connection $Connection
    }
  }
  }
  ```

  Podanie tych informacji pokazuje tę pomoc przy użyciu polecenia cmdlet **Get-Help** w konsoli programu PowerShell. Ten opis jest również wyświetlany w witrynie Azure portal.

  ![Pomoc modułu integracji](../media/modules/module-activity-description.png)

* Jeśli moduł łączy się z usługą zewnętrzną, powinien zawierać [typ połączenia](#add-a-connection-type-to-your-module). w każdym poleceniu cmdlet w module użytkownik powinien móc zastosować obiekt połączenia (wystąpienie tego typu połączenia) jako parametr. Użytkownicy mapują parametry zasobu połączenia na odpowiednie parametry polecenia cmdlet za każdym razem, gdy wywołują polecenie cmdlet. Na podstawie powyższego przykładu wiązki uruchomieniu używa przykładowego zasobu połączenia Contoso o nazwie ContosoConnection, aby uzyskać dostęp do zasobów contoso i zwrócić dane z usługi zewnętrznej.

  W poniższym przykładzie pola są mapowane do UserName `PSCredential` i Password właściwości obiektu, a następnie przekazywane do polecenia cmdlet.

  ```powershell
  $contosoConnection = Get-AutomationConnection -Name 'ContosoConnection'

  $cred = New-Object –TypeName System.Management.Automation.PSCredential –ArgumentList $contosoConnection.UserName, $contosoConnection.Password
  Connect-Contoso -Credential $cred
  }
  ```

  Łatwiejszym i lepszym sposobem podejścia do tego zachowania jest bezpośrednie przekazywanie obiektu połączenia do polecenia cmdlet:

  ```powershell
  $contosoConnection = Get-AutomationConnection -Name 'ContosoConnection'

  Connect-Contoso -Connection $contosoConnection
  }
  ```

  Zachowanie, takie jak poprzedni przykład dla poleceń cmdlet, można włączyć, umożliwiając im akceptowanie obiektu połączenia bezpośrednio jako parametru, a nie tylko pola połączenia dla parametrów. Zazwyczaj chcesz zestaw parametrów dla każdego, tak aby użytkownik nie korzystający z usługi Azure Automation można wywołać polecenia cmdlet bez konstruowania mieszania do działania jako obiekt połączenia. Zestaw parametrów `UserAccount`służy do przekazywania właściwości pola połączenia. `ConnectionObject`umożliwia przejście połączenia bezpośrednio przez.

* Zdefiniuj typ wyjściowy dla wszystkich poleceń cmdlet w module. Zdefiniowanie typu danych wyjściowych polecenia cmdlet ułatwia określanie właściwości danych wyjściowych polecenia do użycia podczas tworzenia za pomocą funkcji IntelliSense czasu projektowania. Jest to szczególnie przydatne podczas tworzenia graficznego podręcznika projektowania automatyzacji, gdzie wiedza o czasie projektowania jest kluczem do łatwego doświadczenia użytkownika z modułem.

Dodaj, `[OutputType([<MyOutputType>])]` gdzie MyOutputType jest prawidłowym typem. Aby dowiedzieć się więcej o OutputType, zobacz [Informacje o funkcjach OutputTypeAttribute](/powershell/module/microsoft.powershell.core/about/about_functions_outputtypeattribute). Poniższy kod jest przykładem dodawania `OutputType` do polecenia cmdlet:

  ```powershell
  function Get-ContosoUser {
  [OutputType([String])]
  param(
     [string]
     $Parameter1
  )
  # <script location here>
  }
  ```

  ![Typ danych wyjściowych graficznego elementu Runbook](../media/modules/runbook-graphical-module-output-type.png)

  To zachowanie jest podobne do funkcji "typ z wyprzedzeniem" danych wyjściowych polecenia cmdlet w programie PowerShell ISE bez konieczności uruchamiania go.

  ![Funkcja IntelliSense programu POSH](../media/modules/automation-posh-ise-intellisense.png)

* Utwórz wszystkie polecenia cmdlet w module jako bezstanowe. Wiele zadań likwidowania można jednocześnie uruchamiać w tej samej domenie aplikacji i tym samym procesie i piaskownicy. Jeśli istnieje dowolny stan współużytkowane na tych poziomach, zadania mogą mieć wpływ na siebie nawzajem. To zachowanie może prowadzić do przerywany i trudne do zdiagnozowania problemów.  Oto przykład czynności, których nie należy wykonywać.

  ```powershell
  $globalNum = 0
  function Set-GlobalNum {
     param(
         [int] $num
     )

     $globalNum = $num
  }
  function Get-GlobalNumTimesTwo {
     $output = $globalNum * 2

     $output
  }
  ```

* Moduł powinien być w pełni zawarty w pakiecie xcopy-stanie. Moduły usługi Azure Automation są dystrybuowane do skrzynek izolowanych automatyzacji, gdy elementy runbook muszą być wykonywane. Moduły muszą działać niezależnie od hosta, na którego są uruchomione. Powinieneś być w stanie zip i przenieść pakiet modułu i mieć go działać normalnie po zaimportowaniu do innego hosta środowiska programu PowerShell. Aby tak się stało, moduł nie powinien zależeć od żadnych plików poza folderem modułu. Ten folder jest folderem, który zostanie spakowany, gdy moduł jest importowany do usługi Azure Automation. Moduł nie powinien również zależeć od unikatowych ustawień rejestru na hoście, takich jak ustawienia ustawione po zainstalowaniu produktu. Wszystkie pliki w module powinny mieć ścieżkę mniejszą niż 140 znaków. Wszystkie ścieżki powyżej 140 znaków spowoduje problemy z importowaniem egonatu. Jeśli to najlepsze rozwiązanie nie jest przestrzegane, moduł nie będzie można stosować w usłudze Azure Automation.  

* Jeśli odwołujesz się do [modułów usługi Azure Powershell Az](/powershell/azure/new-azureps-module-az?view=azps-1.1.0) `AzureRM`w module, upewnij się, że nie odwołujesz się również do. Modułu `Az` nie można używać w `AzureRM` połączeniu z modułami. `Az`jest obsługiwany w elementach runbook, ale nie są domyślnie importowane. Aby dowiedzieć `Az` się więcej o modułach i zagadnieniach, które należy wziąć pod uwagę, zobacz [obsługa modułów Az w usłudze Azure Automation](../az-modules.md).

## <a name="default-modules"></a>Moduły domyślne

W poniższej tabeli wymieniono moduły, które są importowane domyślnie podczas tworzenia konta automatyzacji. Moduły wymienione poniżej mogą mieć nowsze wersje z nich importowane, ale oryginalna wersja nie może zostać usunięta z konta automatyzacji, nawet jeśli usuniesz ich nowszą wersję.

|Nazwa modułu|Wersja|
|---|---|
| AudytPolicyDsc | 1.1.0.0 |
| Azure | 1.0.3 |
| Azure.Storage | 1.0.3 |
| AzureRM.Automation | 1.0.3 |
| AzureRM.Compute | 1.2.1 |
| AzureRM.Profile | 1.0.3 |
| AzureRM.Resources | 1.0.3 |
| AzureRM.Sql | 1.0.3 |
| AzureRM.Storage | 1.0.3 |
| ComputerManagementDsc | 5.0.0.0 |
| GPRegistryPolcyParser | 0.2 |
| Microsoft.PowerShell.Core | 0 |
| Diagnostyka programu Microsoft.PowerShell.Diagnostics |  |
| Zarządzanie programem Microsoft.PowerShell.Management |  |
| Microsoft.PowerShell.Security |  |
| Microsoft.PowerShell.Utility |  |
| Zarządzanie programem Microsoft.WSMan.Management |  |
| Orchestrator.AssetManagement.Cmdlets | 1 |
| Źródła PSDscResources | 2.9.0.0 |
| SecurityPolicyDsc (Polityka bezpieczeństwa) | 2.1.0.0 |
| StateConfigCompositeŹródła | 1 |
| xDSCDołączenie do | 1.1 |
| xPowerShellExecutionPolicy | 1.1.0.0 |
| xRemoteDesktopAdmin | 1.1.0.0 |

## <a name="next-steps"></a>Następne kroki

* Aby dowiedzieć się więcej na temat tworzenia modułów programu PowerShell, zobacz artykuł [Writing a Windows PowerShell Module](/powershell/scripting/developer/windows-powershell) (Pisanie modułu programu Windows PowerShell).
