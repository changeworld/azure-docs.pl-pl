---
title: Zarządzanie modułami w Azure Automation
description: W tym artykule opisano sposób zarządzania modułami w Azure Automation
services: automation
ms.service: automation
author: mgoedtel
ms.author: magoedte
ms.date: 01/31/2020
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: e300bc0f29808215673407d21b65fe329e50ad45
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/13/2020
ms.locfileid: "79278339"
---
# <a name="manage-modules-in-azure-automation"></a>Zarządzanie modułami w Azure Automation

Azure Automation zapewnia możliwość importowania modułów programu PowerShell do konta usługi Automation, które będą używane przez elementy Runbook oparte na programie PowerShell. Te moduły mogą być modułami niestandardowymi, które zostały utworzone, z Galeria programu PowerShell lub z AzureRM i AZ modułów dla platformy Azure. Podczas tworzenia konta usługi Automation niektóre moduły są domyślnie importowane.

## <a name="import-modules"></a>Importowanie modułów

Istnieje wiele sposobów importowania modułu do konta usługi Automation. W poniższych sekcjach przedstawiono różne sposoby importowania modułu.

> [!NOTE]
> Maksymalna ścieżka pliku w module, który ma być używany w Azure Automation to 140 znaków. Dowolna ścieżka o 140 znaków nie będzie mogła zostać zaimportowana do sesji programu PowerShell przy użyciu `Import-Module`.

### <a name="powershell"></a>Program PowerShell

Aby zaimportować moduł do konta usługi Automation, można użyć [nowego AzureRmAutomationModule](/powershell/module/azurerm.automation/new-azurermautomationmodule) . Polecenie cmdlet pobiera adres URL do pakietu zip modułu.

```azurepowershell-interactive
New-AzureRmAutomationModule -Name <ModuleName> -ContentLinkUri <ModuleUri> -ResourceGroupName <ResourceGroupName> -AutomationAccountName <AutomationAccountName>
```

Można również użyć tego samego polecenia cmdlet do zaimportowania modułu bezpośrednio z Galeria programu PowerShell. Upewnij się, że w [Galeria programu PowerShell](https://www.powershellgallery.com)są pożądane **moduły ModuleName** i **ModuleVersion** .

```azurepowershell-interactive
$moduleName = <ModuleName>
$moduleVersion = <ModuleVersion>
New-AzAutomationModule -AutomationAccountName <AutomationAccountName> -ResourceGroupName <ResourceGroupName> -Name $moduleName -ContentLinkUri "https://www.powershellgallery.com/api/v2/package/$moduleName/$moduleVersion"
```

### <a name="azure-portal"></a>Portalu Azure

W Azure Portal przejdź do konta usługi Automation i wybierz pozycję **moduły** w obszarze **zasoby udostępnione**. Kliknij pozycję **+ Dodaj moduł**. Wybierz plik **zip** , który zawiera moduł, a następnie kliknij przycisk **OK** , aby rozpocząć importowanie procesu.

### <a name="powershell-gallery"></a>Galeria programu PowerShell

Moduły z galerii programu PowerShell mogą być importowane z [Galeria programu PowerShell](https://www.powershellgallery.com) bezpośrednio lub z konta usługi Automation.

Aby zaimportować moduł z Galeria programu PowerShell, przejdź do https://www.powershellgallery.com i Wyszukaj moduł, który chcesz zaimportować. Kliknij przycisk **Wdróż, aby Azure Automation** na karcie **Azure Automation** w obszarze **Opcje instalacji**. Ta akcja spowoduje otwarcie Azure Portal. Na stronie **Importowanie** wybierz konto usługi Automation i kliknij przycisk **OK**.

![Galeria programu PowerShell Moduł importowania](../media/modules/powershell-gallery.png)

Możesz również zaimportować moduły z Galeria programu PowerShell bezpośrednio z konta usługi Automation. Na koncie usługi Automation wybierz pozycję **moduły** w obszarze **zasoby udostępnione**. Na stronie moduły kliknij pozycję **Przeglądaj Galerię**, a następnie wyszukaj Galeria programu PowerShell dla modułu. Wybierz moduł, który chcesz zaimportować, a następnie kliknij przycisk **Importuj**. Na stronie **Import** kliknij przycisk **OK** , aby rozpocząć proces importowania.

![Galeria programu PowerShell zaimportować z Azure Portal](../media/modules/gallery-azure-portal.png)

## <a name="delete-modules"></a>Usuń moduły

Jeśli masz problemy z modułem lub musisz przywrócić poprzednią wersję modułu, możesz usunąć go z konta usługi Automation. Nie można usunąć oryginalnej wersji [domyślnych modułów](#default-modules) , które są importowane podczas tworzenia konta usługi Automation. Jeśli moduł, który chcesz usunąć, jest nowszą wersją jednego z zainstalowanych [modułów domyślnych](#default-modules) , zostanie przywrócony do wersji, która została zainstalowana z kontem usługi Automation. W przeciwnym razie wszystkie moduły usunięte z konta usługi Automation zostaną usunięte.

### <a name="azure-portal"></a>Portalu Azure

W Azure Portal przejdź do konta usługi Automation i wybierz pozycję **moduły** w obszarze **zasoby udostępnione**. Wybierz moduł, który chcesz usunąć. Na stronie **moduł** wybierz pozycję **Usuń**. Jeśli ten moduł jest jednym z [domyślnych modułów](#default-modules), zostanie przywrócony do wersji, która była obecna podczas tworzenia konta usługi Automation.

### <a name="powershell"></a>Program PowerShell

Aby usunąć moduł za pomocą programu PowerShell, uruchom następujące polecenie:

```azurepowershell-interactive
Remove-AzureRmAutomationModule -Name <moduleName> -AutomationAccountName <automationAccountName> -ResourceGroupName <resourceGroupName>
```

## <a name="internal-cmdlets"></a>Wewnętrzne polecenia cmdlet

Poniżej znajduje się lista poleceń cmdlet w module wewnętrznym `Orchestrator.AssetManagement.Cmdlets` zaimportowana do każdego konta usługi Automation. Te polecenia cmdlet są dostępne w Twoich elementach Runbook i konfiguracjach DSC i umożliwiają korzystanie z zasobów w ramach konta usługi Automation. Ponadto wewnętrzne polecenia cmdlet umożliwiają pobieranie wpisów tajnych z szyfrowanych wartości **zmiennych** , **poświadczeń**i pól **połączeń** szyfrowanych. Polecenia cmdlet Azure PowerShell nie mogą pobrać tych kluczy tajnych. Te polecenia cmdlet nie wymagają niejawnego połączenia z platformą Azure podczas ich używania, na przykład do uwierzytelniania na platformie Azure przy użyciu konta Uruchom jako.

>[!NOTE]
>Te wewnętrzne polecenia cmdlet są dostępne w hybrydowym procesie roboczym elementu Runbook systemu Windows, ale nie są dostępne w hybrydowym procesie roboczym usługi Linux. Użyj odpowiednich [AzureRM. Automation](https://docs.microsoft.com/powershell/module/AzureRM.Automation/?view=azurermps-6.13.0) lub [AZ modułów](../az-modules.md) dla elementów Runbook działających bezpośrednio na komputerze lub w odniesieniu do zasobów w środowisku. 
>

|Name (Nazwa)|Opis|
|---|---|
|Get-AutomationCertificate|`Get-AutomationCertificate [-Name] <string> [<CommonParameters>]`|
|Get-AutomationConnection|`Get-AutomationConnection [-Name] <string> [-DoNotDecrypt] [<CommonParameters>]` |
|Get-AutomationPSCredential|`Get-AutomationPSCredential [-Name] <string> [<CommonParameters>]` |
|Get-AutomationVariable|`Get-AutomationVariable [-Name] <string> [-DoNotDecrypt] [<CommonParameters>]`|
|Set-AutomationVariable|`Set-AutomationVariable [-Name] <string> -Value <Object> [<CommonParameters>]` |
|Start-AutomationRunbook|`Start-AutomationRunbook [-Name] <string> [-Parameters <IDictionary>] [-RunOn <string>] [-JobId <guid>] [<CommonParameters>]`|
|Oczekiwanie — AutomationJob|`Wait-AutomationJob -Id <guid[]> [-TimeoutInMinutes <int>] [-DelayInSeconds <int>] [-OutputJobsTransitionedToRunning] [<CommonParameters>]`|

## <a name="add-a-connection-type-to-your-module"></a>Dodawanie typu połączenia do modułu

Możesz podać niestandardowy [Typ połączenia](../automation-connections.md) do użycia na koncie usługi Automation, dodając opcjonalny plik do modułu. Ten plik to plik metadanych określający typ połączenia Azure Automation, który ma być używany z poleceniami cmdlet modułu na koncie usługi Automation. Aby to osiągnąć, należy najpierw znać sposób tworzenia modułu programu PowerShell. Aby uzyskać więcej informacji na temat tworzenia modułu, zobacz [jak napisać moduł skryptu programu PowerShell](/powershell/scripting/developer/module/how-to-write-a-powershell-script-module).

![Użyj połączenia niestandardowego w Azure Portal](../media/modules/connection-create-new.png)

Aby dodać Azure Automation typ połączenia, moduł musi zawierać plik o nazwie `<ModuleName>-Automation.json`, który określa właściwości typu połączenia. Plik JSON zostanie umieszczony w folderze modułu skompresowanego pliku zip. Ten plik zawiera pola połączenia, które są wymagane do nawiązania połączenia z systemem lub usługą reprezentowaną przez moduł. Konfiguracja zostanie zakończona, tworząc typ połączenia w Azure Automation. Przy użyciu tego pliku można ustawić nazwy pól, typy i określić, czy pola mają być szyfrowane czy opcjonalne, dla typu połączenia modułu. Poniższy przykład to szablon w formacie pliku JSON, który definiuje nazwę użytkownika i hasło:

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

## <a name="module-best-practices"></a>Najlepsze rozwiązania dotyczące modułów

Moduły programu PowerShell można zaimportować do Azure Automation w celu udostępnienia poleceń cmdlet do użycia w elementach Runbook i ich zasobów DSC dostępnych do użycia w konfiguracjach DSC. W tle Azure Automation przechowuje te moduły oraz czas wykonywania zadań elementu Runbook i zadania kompilacji DSC, ładuje je do Azure Automation piaskownic, w których są wykonywane elementy Runbook i kompilacje konfiguracji DSC. Wszystkie zasoby DSC w modułach są również automatycznie umieszczane na serwerze ściągania Automation DSC. Mogą być ściągane przez maszyny, gdy są stosowane konfiguracje DSC.

Podczas tworzenia modułu programu PowerShell do użycia w Azure Automation Zalecamy rozważenie następujących sytuacji:

* NIE dołączaj folderu wersji w ramach pakietu. zip.  Ten problem jest mniej istotny dla elementów Runbook, ale spowoduje problem z usługą konfiguracji stanu.  Azure Automation automatycznie utworzy folder wersji, gdy moduł zostanie dystrybuowany do węzłów zarządzanych przez DSC, a jeśli folder wersji istnieje, zostanie uruchomiony z dwoma wystąpieniami.  Przykładowa struktura folderów dla modułu DSC:

```powershell
myModule
  - DSCResources
    - myResourceFolder
      myResourceModule.psm1
      myResourceSchema.mof
  myModuleManifest.psd1
```

* Dodaj streszczenie, opis i identyfikator URI pomocy do każdego polecenia cmdlet w module. W programie PowerShell możesz zdefiniować pewne informacje pomocy dotyczące poleceń cmdlet, aby umożliwić użytkownikowi uzyskanie pomocy na temat korzystania z nich dzięki poleceniu cmdlet **Get-Help**. Poniższy przykład pokazuje, jak zdefiniować streszczenie i identyfikator URI pomocy dla w pliku modułu. PSM1:

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

  Podanie tych informacji powoduje wyświetlenie pomocy przy użyciu polecenia cmdlet **Get-Help** w konsoli programu PowerShell. Ten opis jest również wyświetlany w Azure Portal.

  ![Pomoc modułu integracji](../media/modules/module-activity-description.png)

* Jeśli moduł nawiązuje połączenie z usługą zewnętrzną, powinien zawierać [Typ połączenia](#add-a-connection-type-to-your-module). w każdym poleceniu cmdlet w module użytkownik powinien móc zastosować obiekt połączenia (wystąpienie tego typu połączenia) jako parametr. Użytkownicy mapują parametry zasobu połączenia do odpowiednich parametrów polecenia cmdlet za każdym razem, gdy wywołuje polecenie cmdlet. Na podstawie powyższego przykładu elementu Runbook używa przykładowego zasobu połączenia contoso o nazwie ContosoConnection, aby uzyskać dostęp do zasobów firmy Contoso i zwrócić dane z usługi zewnętrznej.

  W poniższym przykładzie pola są mapowane na właściwości UserName i Password obiektu `PSCredential`, a następnie przesyłane do polecenia cmdlet.

  ```powershell
  $contosoConnection = Get-AutomationConnection -Name 'ContosoConnection'

  $cred = New-Object –TypeName System.Management.Automation.PSCredential –ArgumentList $contosoConnection.UserName, $contosoConnection.Password
  Connect-Contoso -Credential $cred
  }
  ```

  Łatwiejszym i lepszym sposobem podejścia do tego zachowania jest bezpośrednie przekazanie obiektu połączenia do polecenia cmdlet:

  ```powershell
  $contosoConnection = Get-AutomationConnection -Name 'ContosoConnection'

  Connect-Contoso -Connection $contosoConnection
  }
  ```

  Możesz włączyć zachowanie takie jak w poprzednim przykładzie dla poleceń cmdlet, zezwalając im na akceptowanie obiektu połączenia bezpośrednio jako parametru, a nie tylko pól połączeń dla parametrów. Zazwyczaj potrzebny jest zestaw parametrów dla każdego, aby użytkownik nie korzystał Azure Automation może wywoływać polecenia cmdlet bez konstruowania obiektu Hashtable do działania jako obiekt połączenia. Zestaw parametrów `UserAccount`jest używany do przekazywania właściwości pola połączenia. `ConnectionObject` umożliwia bezpośrednie przekazywanie połączenia.

* Zdefiniuj typ danych wyjściowych dla wszystkich poleceń cmdlet w module. Zdefiniowanie typu danych wyjściowych polecenia cmdlet ułatwia określanie właściwości danych wyjściowych polecenia do użycia podczas tworzenia za pomocą funkcji IntelliSense czasu projektowania. Jest to szczególnie przydatne podczas tworzenia graficznego elementu Runbook usługi Automation, gdzie znajomość czasu projektowania jest kluczem do łatwego środowiska użytkownika z Twoim modułem.

Dodaj `[OutputType([<MyOutputType>])]`, gdzie element weboutputtype jest prawidłowym typem. Aby dowiedzieć się więcej o wyjściutype, zobacz [Informacje o funkcjach OutputTypeAttribute](/powershell/module/microsoft.powershell.core/about/about_functions_outputtypeattribute). Poniższy kod stanowi przykład dodawania `OutputType` do polecenia cmdlet:

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

  To zachowanie jest podobne do funkcji "Type z wyprzedzeniem" w danych wyjściowych polecenia cmdlet w programie PowerShell ISE bez konieczności uruchamiania go.

  ![Funkcja IntelliSense programu POSH](../media/modules/automation-posh-ise-intellisense.png)

* Utwórz wszystkie polecenia cmdlet w module jako bezstanowe. Wiele zadań elementów Runbook można jednocześnie uruchomić w tej samej domenie aplikacji i w tym samym procesie i w piaskownicy. Jeśli na tych poziomach są udostępnione jakiekolwiek Stany, zadania mogą mieć wpływ na siebie. Takie zachowanie może prowadzić do sporadycznej i trudnej diagnostyki problemów.  Oto przykład czynności, których nie należy wykonywać.

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

* Moduł powinien być w pełni zawarty w pakiecie xcopy. Moduły Azure Automation są dystrybuowane do piaskownic automatyzacji, gdy elementy Runbook wymagają wykonania. Moduły muszą działać niezależnie od hosta, na którym są uruchomione. W przypadku zaimportowania go do środowiska programu PowerShell innego hosta należy mieć możliwość przechowywania i przenoszenia pakietu modułu. W takiej sytuacji moduł nie powinien zależeć od żadnych plików poza folderem modułu. Ten folder jest folderem, który jest zapakowany podczas importowania modułu do Azure Automation. Moduł nie powinien także zależeć od żadnych unikatowych ustawień rejestru na hoście, na przykład ustawień ustawionych podczas instalacji produktu. Wszystkie pliki w module powinny mieć mniej niż 140 znaków. Wszystkie ścieżki ponad 140 znaków spowodują problemy z importowaniem elementu Runbook. W przypadku nieprzestrzegania tego najlepszego rozwiązania moduł nie będzie mógł być użyteczny w Azure Automation.  

* Jeśli do modułu zostanie odwołująca się [usługa Azure PowerShell AZ](/powershell/azure/new-azureps-module-az?view=azps-1.1.0) modules, upewnij się, że nie odwołuje się również do `AzureRM`. Moduł `Az` nie może być używany w połączeniu z modułami `AzureRM`. `Az` jest obsługiwane w elementach Runbook, ale nie są domyślnie importowane. Aby dowiedzieć się więcej na temat `Az` modułów i zagadnień, które należy wziąć pod uwagę, zobacz [AZ module Support in Azure Automation](../az-modules.md).

## <a name="default-modules"></a>Moduły domyślne

Poniższa tabela zawiera listę modułów, które są importowane domyślnie podczas tworzenia konta usługi Automation. Moduły wymienione poniżej mogą mieć zaimportowane nowsze wersje, ale oryginalna wersja nie może zostać usunięta z konta usługi Automation nawet po usunięciu nowszej wersji.

|Nazwa modułu|Wersja|
|---|---|
| AuditPolicyDsc | 1.1.0.0 |
| Azure | 1.0.3 |
| Azure.Storage | 1.0.3 |
| AzureRM.Automation | 1.0.3 |
| AzureRM.Compute | 1.2.1 |
| AzureRM.Profile | 1.0.3 |
| AzureRM.Resources | 1.0.3 |
| AzureRM.Sql | 1.0.3 |
| AzureRM.Storage | 1.0.3 |
| ComputerManagementDsc | 5.0.0.0 |
| GPRegistryPolicyParser | 0.2 |
| Microsoft.PowerShell.Core | 0 |
| Microsoft.PowerShell.Diagnostics |  |
| Microsoft.PowerShell.Management |  |
| Microsoft.PowerShell.Security |  |
| Microsoft.PowerShell.Utility |  |
| Microsoft.WSMan.Management |  |
| Orchestrator. AssetManagement. polecenia cmdlet | 1 |
| PSDscResources | 2.9.0.0 |
| SecurityPolicyDsc | 2.1.0.0 |
| StateConfigCompositeResources | 1 |
| xDSCDomainjoin | 1.1 |
| xPowerShellExecutionPolicy | 1.1.0.0 |
| xRemoteDesktopAdmin | 1.1.0.0 |

## <a name="next-steps"></a>Następne kroki

* Aby dowiedzieć się więcej na temat tworzenia modułów programu PowerShell, zobacz artykuł [Writing a Windows PowerShell Module](/powershell/scripting/developer/windows-powershell) (Pisanie modułu programu Windows PowerShell).
