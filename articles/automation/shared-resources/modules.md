---
title: Zarządzanie modułami w usłudze Azure Automation
description: W tym artykule opisano sposób zarządzania modułami w usłudze Azure Automation.
services: automation
ms.service: automation
author: mgoedtel
ms.author: magoedte
ms.date: 01/31/2020
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 605d1bc72406a9aeecc9273f9bd2d7fd2b30ab11
ms.sourcegitcommit: bc738d2986f9d9601921baf9dded778853489b16
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/02/2020
ms.locfileid: "80618687"
---
# <a name="manage-modules-in-azure-automation"></a>Zarządzanie modułami w usłudze Azure Automation

Moduły programu PowerShell można zaimportować do usługi Azure Automation, aby ich polecenia cmdlet były dostępne w podręcznikach runbook i ich zasobach DSC dostępnych w konfiguracjach DSC. W tle usługa Azure Automation przechowuje te moduły. W przypadku zadania runbook i czasu wykonywania zadania kompilacji DSC automatyzacja ładuje je do skrzynek izolowania usługi Azure Automation, w których są wykonywane elementy runbook i kompilowanie konfiguracji DSC. Wszystkie zasoby DSC w modułach są również automatycznie umieszczane na serwerze ściągania DSC automatyzacji. Maszyny mogą pobierać je, gdy stosują konfiguracje DSC.

Moduły używane w usłudze Azure Automation mogą być niestandardowymi modułami, które zostały utworzone, modułami z galerii programu PowerShell lub modułami AzureRM i Az dla platformy Azure. Podczas tworzenia konta automatyzacji niektóre moduły są importowane domyślnie.

## <a name="default-modules"></a>Moduły domyślne

W poniższej tabeli wymieniono moduły zaimportowane domyślnie podczas tworzenia konta automatyzacji. Automatyzacja może importować nowsze wersje tych modułów. Jednak nie można usunąć oryginalnej wersji z konta automatyzacji, nawet jeśli usuniesz nowszą wersję.

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

## <a name="internal-cmdlets"></a>Wewnętrzne polecenia cmdlet

W poniższej tabeli wymieniono `Orchestrator.AssetManagement.Cmdlets` polecenia cmdlet w module wewnętrznym importowanym do każdego konta automatyzacji. Te polecenia cmdlet są dostępne w konfiguracjach czął i DSC i umożliwiają interakcję z zasobami na koncie automatyzacji. Ponadto wewnętrzne polecenia cmdlet umożliwiają pobieranie wpisów tajnych z zaszyfrowanych zmiennych, poświadczeń i połączeń szyfrowanych. Polecenia cmdlet programu Azure PowerShell nie są w stanie pobrać tych wpisów tajnych. Te polecenia cmdlet nie wymagają niejawnego łączenia się z platformą Azure podczas korzystania z nich, jak podczas korzystania z konta Uruchom jako do uwierzytelniania na platformie Azure.

>[!NOTE]
>Te wewnętrzne polecenia cmdlet są dostępne w usłudze Windows Hybrid Runbook Worker, ale nie w usłudze Linux Hybrid Runbook Worker. Użyj odpowiednich poleceń cmdlet [azurerm.automation](https://docs.microsoft.com/powershell/module/AzureRM.Automation/?view=azurermps-6.13.0) lub [az modułu](../az-modules.md) dla śliwków runbook uruchomionych bezpośrednio na komputerze lub zasobów w danym środowisku. 

|Nazwa|Opis|
|---|---|
|Get-AutomationCertificate|`Get-AutomationCertificate [-Name] <string> [<CommonParameters>]`|
|Get-AutomationConnection|`Get-AutomationConnection [-Name] <string> [-DoNotDecrypt] [<CommonParameters>]` |
|Get-AutomationPSCredential|`Get-AutomationPSCredential [-Name] <string> [<CommonParameters>]` |
|Get-AutomationVariable|`Get-AutomationVariable [-Name] <string> [-DoNotDecrypt] [<CommonParameters>]`|
|Set-AutomationVariable|`Set-AutomationVariable [-Name] <string> -Value <Object> [<CommonParameters>]` |
|Start-AutomationRunbook|`Start-AutomationRunbook [-Name] <string> [-Parameters <IDictionary>] [-RunOn <string>] [-JobId <guid>] [<CommonParameters>]`|
|Wait-AutomationOb|`Wait-AutomationJob -Id <guid[]> [-TimeoutInMinutes <int>] [-DelayInSeconds <int>] [-OutputJobsTransitionedToRunning] [<CommonParameters>]`|

## <a name="importing-modules"></a>Importowanie modułów

Istnieje wiele sposobów, które można zaimportować moduł do konta automatyzacji. W poniższych sekcjach przedstawiono różne sposoby importowania modułu.

> [!NOTE]
> Maksymalny rozmiar ścieżki dla pliku w module używanym w usłudze Azure Automation wynosi 140 znaków. Automatyzacja nie może zaimportować pliku o rozmiarze ścieżki powyżej `Import-Module`140 znaków do sesji programu PowerShell z programem .

### <a name="import-modules-in-azure-portal"></a>Importowanie modułów w witrynie Azure portal

Aby zaimportować moduł w witrynie Azure portal:

1. Przejdź do swojego konta automatyzacji.
2. Wybierz **moduły** w obszarze **Zasoby udostępnione**.
3. Kliknij **pozycję Dodaj moduł**. 
4. Wybierz plik **zip** zawierający moduł.
5. Kliknij **przycisk OK,** aby rozpocząć importowanie procesu.

### <a name="import-modules-using-powershell"></a>Importowanie modułów przy użyciu programu PowerShell

Za pomocą polecenia cmdlet [New-AzureRmAutomationModule](/powershell/module/azurerm.automation/new-azurermautomationmodule) można zaimportować moduł do konta automatyzacji. Polecenie cmdlet przyjmuje adres URL pakietu .zip modułu.

```azurepowershell-interactive
New-AzureRmAutomationModule -Name <ModuleName> -ContentLinkUri <ModuleUri> -ResourceGroupName <ResourceGroupName> -AutomationAccountName <AutomationAccountName>
```

Można również użyć tego samego polecenia cmdlet, aby bezpośrednio zaimportować moduł z galerii programu PowerShell. Upewnij się, `ModuleName` `ModuleVersion` że można pobrać i z [galerii programu PowerShell](https://www.powershellgallery.com).

```azurepowershell-interactive
$moduleName = <ModuleName>
$moduleVersion = <ModuleVersion>
New-AzureRmAutomationModule -AutomationAccountName <AutomationAccountName> -ResourceGroupName <ResourceGroupName> -Name $moduleName -ContentLinkUri "https://www.powershellgallery.com/api/v2/package/$moduleName/$moduleVersion"
```
### <a name="import-modules-from-powershell-gallery"></a>Importowanie modułów z galerii programu PowerShell

Moduły [galerii programu PowerShell](https://www.powershellgallery.com) można importować bezpośrednio z galerii lub z konta automatyzacji.

Aby zaimportować moduł bezpośrednio z galerii programu PowerShell:

1. Przejdź https://www.powershellgallery.com do i wyszukaj moduł do zaimportowania.
2. Kliknij **pozycję Wdrażanie w usłudze Azure Automation** na karcie **Automatyzacja platformy Azure** w obszarze Opcje **instalacji**. Ta akcja otwiera witrynę Azure portal. 
3. Na stronie Importowanie wybierz konto automatyzacji i kliknij przycisk **OK**.

![Moduł importu galerii programu PowerShell](../media/modules/powershell-gallery.png)

Aby zaimportować moduł Galerii programu PowerShell bezpośrednio z konta automatyzacji:

1. Wybierz **moduły** w obszarze **Zasoby udostępnione**. 
2. Na stronie Moduły kliknij pozycję **Przeglądaj galerię**, a następnie wyszukaj w galerii moduł. 
3. Wybierz moduł do zaimportowania i kliknij przycisk **Importuj**. 
4. Na stronie Importuj kliknij przycisk **OK,** aby rozpocząć proces importowania.

![Importowanie galerii programu PowerShell z witryny Azure portal](../media/modules/gallery-azure-portal.png)

## <a name="deleting-modules"></a>Usuwanie modułów

Jeśli masz problemy z modułem lub musisz przywrócić poprzednią wersję modułu, możesz usunąć go z konta automatyzacji. Nie można usunąć oryginalnych wersji [modułów domyślnych,](#default-modules) które są importowane podczas tworzenia konta automatyzacji. Jeśli moduł do usunięcia jest nowszą wersją jednego z [modułów domyślnych,](#default-modules)jest wycofywany do wersji zainstalowanej na koncie automatyzacji. W przeciwnym razie każdy moduł usunięty z konta automatyzacji zostanie usunięty.

### <a name="delete-modules-in-azure-portal"></a>Usuwanie modułów w witrynie Azure portal

Aby usunąć moduł w witrynie Azure portal:

1. Przejdź do konta automatyzacji i wybierz **moduły** w obszarze **Zasoby udostępnione**. 
2. Wybierz moduł, który chcesz usunąć. 
3. Na stronie **Moduł** wybierz pozycję **Usuń**. Jeśli ten moduł jest jednym z [modułów domyślnych,](#default-modules)jest wycofywany do wersji, która istniała podczas tworzenia konta automatyzacji.

### <a name="delete-modules-using-powershell"></a>Usuwanie modułów przy użyciu programu PowerShell

Aby usunąć moduł za pośrednictwem programu PowerShell, uruchom następujące polecenie:

```azurepowershell-interactive
Remove-AzureRmAutomationModule -Name <moduleName> -AutomationAccountName <automationAccountName> -ResourceGroupName <resourceGroupName>
```
## <a name="adding-a-connection-type-to-your-module"></a>Dodawanie typu połączenia do modułu

Można podać [niestandardowy typ połączenia](../automation-connections.md) do użycia na koncie automatyzacji, dodając opcjonalny plik metadanych do modułu. Ten plik określa typ połączenia usługi Azure Automation, który ma być używany z poleceniami cmdlet modułu na koncie automatyzacji. Aby to osiągnąć, należy najpierw wiedzieć, jak autor modułu programu PowerShell. Zobacz [Jak napisać moduł skryptu programu PowerShell](/powershell/scripting/developer/module/how-to-write-a-powershell-script-module).

![Używanie połączenia niestandardowego w witrynie Azure portal](../media/modules/connection-create-new.png)

Plik określający właściwości typu połączenia nosi nazwę ** &lt;ModuleName&gt;-Automation.json** i znajduje się w folderze modułu skompresowanego pliku **zip.** Ten plik zawiera pola połączenia, które są wymagane do połączenia z systemem lub usługą, którą reprezentuje moduł. Konfiguracja umożliwia utworzenie typu połączenia w usłudze Azure Automation. Za pomocą tego pliku można ustawić nazwy pól, typy i czy pola są zaszyfrowane lub opcjonalne dla typu połączenia modułu. Poniższy przykład jest szablonem w formacie pliku **json,** który definiuje nazwę użytkownika i właściwości hasła:

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

## <a name="best-practices-for-authoring-modules"></a>Najważniejsze wskazówki dotyczące tworzenia modułów

Zaleca się, aby postępować zgodnie z zagadnieniami w tej sekcji podczas tworzenia modułu programu PowerShell do użycia w usłudze Azure Automation.

### <a name="version-folder"></a>Folder Wersja

NIE dołączaj folderu wersji w pakiecie **.zip** dla modułu.  Ten problem jest mniej problemem dla śmięty, ale powoduje problem z usługą konfiguracji stanu. Usługa Azure Automation automatycznie tworzy folder wersji, gdy moduł jest dystrybuowany do węzłów zarządzanych przez DSC. Jeśli istnieje folder wersji, kończy się z dwóch wystąpień. Oto przykładowa struktura folderów dla modułu DSC:

```powershell
myModule
  - DSCResources
    - myResourceFolder
      myResourceModule.psm1
      myResourceSchema.mof
  myModuleManifest.psd1
```

### <a name="help-information"></a>Informacje o pomocy

Dołącz streszczenie, opis i identyfikator URI pomocy dla każdego polecenia cmdlet w module. W programie PowerShell można zdefiniować informacje pomocy `Get-Help` dla poleceń cmdlet przy użyciu polecenia cmdlet. W poniższym przykładzie pokazano, jak zdefiniować streszczenie i pomóc identyfikatora URI w pliku modułu **.psm1:**

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

  Podanie tych informacji pokazuje `Get-Help` tekst pomocy za pośrednictwem polecenia cmdlet w konsoli programu PowerShell. Ten tekst jest również wyświetlany w witrynie Azure portal.

  ![Pomoc modułu integracji](../media/modules/module-activity-description.png)

### <a name="connection-type"></a>Typ połączenia

Jeśli moduł łączy się z usługą zewnętrzną, należy zdefiniować [typ połączenia](#adding-a-connection-type-to-your-module). Każde polecenie cmdlet w module powinno akceptować obiekt połączenia (wystąpienie tego typu połączenia) jako parametr. Użytkownicy mapują parametry zasobu połączenia na odpowiednie parametry polecenia cmdlet za każdym razem, gdy wywołują polecenie cmdlet. Na podstawie powyższego przykładu wiązki uruchomieniu używa `ContosoConnection` przykładowego zasobu połączenia Contoso wywoływanego do uzyskiwania dostępu do zasobów contoso i zwracania danych z usługi zewnętrznej.

  W poniższym przykładzie pola są `UserName` mapowane na `Password` `PSCredential` i właściwości obiektu, a następnie przekazywane do polecenia cmdlet.

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

  Podobne zachowanie dla poleceń cmdlet można włączyć, umożliwiając im bezpośrednie akceptowanie obiektu połączenia jako parametru, a nie tylko pola połączenia dla parametrów. Zazwyczaj chcesz zestaw parametrów dla każdego, tak aby użytkownik nie korzystający z usługi Azure Automation można wywołać polecenia cmdlet bez konstruowania mieszania do działania jako obiekt połączenia. Zestaw parametrów `UserAccount`służy do przekazywania właściwości pola połączenia. `ConnectionObject`umożliwia przejście połączenia bezpośrednio przez.

### <a name="output-type"></a>Typ wyjścia

Zdefiniuj typ wyjściowy dla wszystkich poleceń cmdlet w module. Zdefiniowanie typu danych wyjściowych polecenia cmdlet ułatwia określanie właściwości danych wyjściowych polecenia do użycia podczas tworzenia za pomocą funkcji IntelliSense czasu projektowania. Praktyka ta jest szczególnie przydatna podczas tworzenia graficznego podręcznika projektowania automatyzacji, dla którego wiedza w czasie projektowania jest kluczem do łatwego doświadczenia użytkownika z modułem.

`[OutputType([<MyOutputType>])]` Dodaj, `MyOutputType` gdzie jest prawidłowy typ. Aby dowiedzieć `OutputType`się więcej o , zobacz [Informacje o funkcjach OutputTypeAttribute](/powershell/module/microsoft.powershell.core/about/about_functions_outputtypeattribute). Poniższy kod jest przykładem dodawania `OutputType` do polecenia cmdlet:

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

### <a name="cmdlet-state"></a>Stan polecenia cmdlet

Wszystkie polecenia cmdlet w module są bezstanowe. Wiele zadań uruchomieniu można jednocześnie `AppDomain` uruchamiać w tym samym i tym samym procesie i piaskownicy. Jeśli istnieje dowolny stan współużytkowane na tych poziomach, zadania mogą mieć wpływ na siebie nawzajem. To zachowanie może prowadzić do sporadyczne i trudne do zdiagnozowania problemy. Oto przykład czego nie robić:

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

### <a name="module-dependency"></a>Zależność modułu

Upewnij się, że moduł jest w pełni zawarty w pakiecie xcopy-able. Moduły usługi Azure Automation są dystrybuowane do skrzynek izolowanych automatyzacji podczas wykonywania uruchomieniu. systemu. Moduły muszą działać niezależnie od hosta, który je uruchamia. 

Powinieneś być w stanie spakować i przenieść pakiet modułu i mieć go działać normalnie po zaimportowaniu do środowiska programu PowerShell innego hosta. Aby tak się stało, upewnij się, że moduł nie zależy od plików spoza folderu modułu, który jest spakowany, gdy moduł jest importowany do usługi Azure Automation. 

Moduł nie powinien zależeć od żadnych unikatowych ustawień rejestru na hoście. Przykładem są ustawienia wprowadzone po zainstalowaniu produktu. 

Upewnij się, że wszystkie pliki w module mają ścieżki z mniej niż 140 znaków. Wszystkie ścieżki powyżej 140 znaków powodują problemy z importowaniem śmięty. Jeśli nie zastosujesz się do tego najlepszego rozwiązania, moduł nie jest użyteczny w usłudze Azure Automation.  

### <a name="references-to-azurerm-and-az"></a>Odwołania do azurerm i az

Jeśli odwołujesz się do [modułu Az usługi Azure PowerShell](/powershell/azure/new-azureps-module-az?view=azps-1.1.0) w module, upewnij się, że nie odwołujesz się również do usługi AzureRM. Nie można użyć modułu Az w połączeniu z modułem AzureRM. Az jest obsługiwany w elementach runbook, ale nie jest importowany domyślnie. Aby dowiedzieć się więcej o module Az i zagadnieniach, które należy wziąć pod uwagę, zobacz [Obsługa modułów Az w usłudze Azure Automation](../az-modules.md).

## <a name="next-steps"></a>Następne kroki

* Aby dowiedzieć się więcej na temat tworzenia modułów programu PowerShell, zobacz [Pisanie modułu programu Windows PowerShell](/powershell/scripting/developer/windows-powershell).
