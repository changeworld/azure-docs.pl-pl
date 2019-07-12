---
title: Zarządzanie moduły w usłudze Azure Automation
description: W tym artykule opisano sposób zarządzania moduły w usłudze Azure Automation
services: automation
ms.service: automation
author: bobbytreed
ms.author: robreed
ms.date: 06/05/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: cd085164fc9804e0c1c822df1c72d3ef94093a07
ms.sourcegitcommit: 2e4b99023ecaf2ea3d6d3604da068d04682a8c2d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/09/2019
ms.locfileid: "67672783"
---
# <a name="manage-modules-in-azure-automation"></a>Zarządzanie moduły w usłudze Azure Automation

Usługa Azure Automation zapewnia możliwość importowania modułów programu PowerShell do konta usługi Automation, który będzie używany przez elementy runbook programu PowerShell, na podstawie. Te moduły mogą być moduły niestandardowe, które zostały utworzone z galerii programu PowerShell lub AzureRM i Az moduły dla platformy Azure. Podczas tworzenia konta usługi Automation niektóre moduły są importowane domyślnie.

## <a name="import-modules"></a>Importowanie modułów

Istnieje wiele sposobów można zaimportować modułu do konta usługi Automation. W poniższych sekcjach opisano różne sposoby, aby zaimportować moduł.

> [!NOTE]
> Maksymalna ścieżkę pliku w module, który ma być używany w usłudze Azure Automation jest 140 znaków. Dowolną ścieżkę z ponad 140 znaków nie będą mogli być importowane do sesji programu PowerShell przy użyciu `Import-Module`.

### <a name="powershell"></a>PowerShell

Możesz użyć [elementu New-AzureRmAutomationModule](/powershell/module/azurerm.automation/new-azurermautomationmodule) Aby zaimportować moduł do konta usługi Automation. Polecenie cmdlet pobiera adres url do pakietu zip modułu.

```azurepowershell-interactive
New-AzureRmAutomationModule -Name <ModuleName> -ContentLinkUri <ModuleUri> -ResourceGroupName <ResourceGroupName> -AutomationAccountName <AutomationAccountName>
```

### <a name="azure-portal"></a>Azure Portal

W witrynie Azure portal przejdź do konta usługi Automation i wybierz **modułów** w obszarze **zasoby udostępnione**. Kliknij przycisk **+ Dodaj moduł**. Wybierz **zip** pliku, który zawiera Twoje modułu i kliknij przycisk **Ok** do rozpoczęcia procesu importowania.

### <a name="powershell-gallery"></a>Galeria programu PowerShell

Moduły z galerii programu PowerShell może być zaimportowane z [galerii programu PowerShell](https://www.powershellgallery.com) bezpośrednio lub przy użyciu konta usługi Automation.

Aby zaimportować moduł za pomocą galerii programu PowerShell, przejdź do https://www.powershellgallery.com i wyszukaj moduł, który chcesz zaimportować. Kliknij przycisk **wdrażanie w usłudze Azure Automation** na **usługi Azure Automation** karcie **opcje instalacji**. Ta akcja powoduje otwarcie witrynę Azure portal. Na **importu** stronie, wybierz konto usługi Automation i kliknij przycisk **OK**.

![Moduł import galerii programu PowerShell](../media/modules/powershell-gallery.png)

Można również importować moduły z galerii programu PowerShell bezpośrednio z Twojego konta usługi Automation. Na koncie usługi Automation wybierz **modułów** w obszarze **zasoby udostępnione**. Na stronie moduły kliknij **Przeglądaj galerię**. Spowoduje to otwarcie **Przeglądaj galerię** strony. Ta strona służy do wyszukiwania w galerii programu PowerShell dla modułu. Wybierz moduł, który chcesz zaimportować, a następnie kliknij przycisk **zaimportować**. Na **zaimportować** kliknij **OK** aby rozpocząć proces importowania.

![Importuj galerii programu PowerShell w witrynie Azure portal](../media/modules/gallery-azure-portal.png)

## <a name="delete-modules"></a>Usuń moduły

Jeśli masz problemy z modułem lub musisz przywrócić poprzednią wersję modułu, można usunąć go z konta usługi Automation. Nie można usunąć oryginalną wersję [domyślne moduły](#default-modules) , są importowane podczas tworzenia konta usługi Automation. Jeśli moduł, który chcesz usunąć jest nowsza wersja jednego z [domyślne moduły](#default-modules) zainstalowaniu go będzie wycofywania do wersji, który został zainstalowany przy użyciu konta usługi Automation. W przeciwnym razie każdy moduł usunięte z Twojego konta usługi Automation zostanie usunięty.

### <a name="azure-portal"></a>Azure Portal

W witrynie Azure portal przejdź do konta usługi Automation i wybierz **modułów** w obszarze **zasoby udostępnione**. Wybierz moduł, który chcesz usunąć. Na **modułu** strona, clcick **Usuń**. Jeśli ten moduł jest jednym z [domyślne moduły](#default-modules) go zostanie wycofana do wersji, który był obecny, po utworzeniu konta usługi Automation.

### <a name="powershell"></a>PowerShell

Aby usunąć moduł przy użyciu programu PowerShell, uruchom następujące polecenie:

```azurepowershell-interactive
Remove-AzureRmAutomationModule -Name <moduleName> -AutomationAccountName <automationAccountName> -ResourceGroupName <resourceGroupName>
```

## <a name="internal-cmdlets"></a>Poleceń cmdlet wewnętrznego

Poniżej przedstawiono listę poleceń cmdlet wewnętrznego `Orchestrator.AssetManagement.Cmdlets` modułu, który jest importowany do każdego konta usługi Automation. Te polecenia cmdlet są dostępne w elementach runbook i konfiguracjami DSC i umożliwiają interakcję z zasobami w ramach konta usługi Automation. Ponadto wewnętrzne polecenia cmdlet umożliwiają pobierania wpisów tajnych z szyfrowane **zmiennej** wartości **poświadczenia**i zaszyfrowanego **połączenia** pola. Polecenia cmdlet programu Azure PowerShell nie są możliwe do pobrania tych kluczy tajnych. Te polecenia cmdlet nie wymagają niejawnie łączenie z platformą Azure, podczas korzystania z nich. Jest to korzystne dla scenariuszy, w którym masz połączenie, takich jak konto Uruchom jako, należy użyć do uwierzytelniania na platformie Azure.

|Name (Nazwa)|Opis|
|---|---|
|Get-AutomationCertificate|`Get-AutomationCertificate [-Name] <string> [<CommonParameters>]`|
|Get-AutomationConnection|`Get-AutomationConnection [-Name] <string> [-DoNotDecrypt] [<CommonParameters>]` |
|Get-AutomationPSCredential|`Get-AutomationPSCredential [-Name] <string> [<CommonParameters>]` |
|Get-AutomationVariable|`Get-AutomationVariable [-Name] <string> [-DoNotDecrypt] [<CommonParameters>]`|
|Set-AutomationVariable|`Set-AutomationVariable [-Name] <string> -Value <Object> [<CommonParameters>]` |
|Start-AutomationRunbook|`Start-AutomationRunbook [-Name] <string> [-Parameters <IDictionary>] [-RunOn <string>] [-JobId <guid>] [<CommonParameters>]`|
|Wait-AutomationJob|`Wait-AutomationJob -Id <guid[]> [-TimeoutInMinutes <int>] [-DelayInSeconds <int>] [-OutputJobsTransitionedToRunning] [<CommonParameters>]`|

## <a name="add-a-connection-type-to-your-module"></a>Dodaj typ połączenia do modułu

Można podać niestandardowy [typu połączenia](../automation-connections.md) do użycia na koncie usługi Automation, dodając opcjonalny plik do modułu. Ten plik jest plik metadanych określający typ połączenia usługi Azure Automation do użycia z poleceniami cmdlet modułu na koncie usługi Automation. Można to osiągnąć, należy najpierw wie, jak tworzenie modułu programu PowerShell. Aby uzyskać więcej informacji na temat tworzenia modułu, zobacz [jak napisać do modułu skryptu programu PowerShell](/powershell/developer/module/how-to-write-a-powershell-script-module).

![Użyj niestandardowego połączenia w witrynie Azure portal](../media/modules/connection-create-new.png)

Aby dodać typ połączenia usługi Azure Automation, modułu musi zawierać plik o nazwie `<ModuleName>-Automation.json` , który określa właściwości typu połączenia. Jest to plik json, który jest umieszczony w folderze modułu skompresowanego pliku zip. Ten plik zawiera pola połączenia, który jest wymagany do nawiązania system lub usługę, z której reprezentowaną przez moduł. Ta konfiguracja kończy się tworzenie typu połączenia w usłudze Azure Automation. Przy użyciu tego pliku można ustawić nazwy pól, typy i tego, czy pola powinny być szyfrowane lub opcjonalne dla typu połączenia modułu. Poniższy przykład przedstawia szablon w formacie pliku json, który określa właściwości nazwy użytkownika i hasła:

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

## <a name="module-best-practices"></a>Najlepsze rozwiązania modułu

Moduły programu PowerShell można zaimportować do usługi Azure Automation w celu udostępnienia ich poleceń cmdlet do użycia w elementach runbook i zasobów DSC dostępne do użycia w konfiguracjach DSC. Za kulisami usługi Azure Automation przechowuje te moduły, a zadanie elementu runbook i czasu wykonywania zadania kompilacji DSC ładuje je do piaskownic usługi Azure Automation, w którym wykonywanie elementów runbook i kompilowanie konfiguracji DSC. Wszystkie zasoby DSC w modułach są również automatycznie umieszczane na serwerze ściągania usługi Automation DSC. One mogą być ściągane przez komputery, gdy mają one zastosowanie konfiguracji DSC.

Zaleca się, że rozważ następujące opcje podczas tworzenia modułu programu PowerShell do użycia w usłudze Azure Automation:

* Dodaj streszczenie, opis i identyfikator URI pomocy do każdego polecenia cmdlet w module. W programie PowerShell możesz zdefiniować pewne informacje pomocy dotyczące poleceń cmdlet, aby umożliwić użytkownikowi uzyskanie pomocy na temat korzystania z nich dzięki poleceniu cmdlet **Get-Help**. Poniższy przykład pokazuje, jak zdefiniować streszczenie i identyfikator URI dla w pliku psm1 modułu pomocy:

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

  Podanie tych informacji pokazuje pomocy przy użyciu **Get-Help** polecenia cmdlet w konsoli programu PowerShell. Ten opis jest także wyświetlany w witrynie Azure portal.

  ![Pomoc modułu integracji](../media/modules/module-activity-description.png)

* Jeśli moduł łączy do usługi zewnętrznej, musi on zawierać [typu połączenia](#add-a-connection-type-to-your-module). w każdym poleceniu cmdlet w module użytkownik powinien móc zastosować obiekt połączenia (wystąpienie tego typu połączenia) jako parametr. Dzięki temu użytkownicy mogą mapować parametrów elementu zawartości połączenia do odpowiednich parametrów polecenia cmdlet podczas każdego wywoływania polecenia cmdlet. Przykład Contoso zasób połączenia usługi o nazwie ContosoConnection oparte na powyższym przykładzie elementu runbook, używa dostęp do zasobów firmy Contoso i zwrócić dane z usługi zewnętrznej.

  W poniższym przykładzie, pola są mapowane do właściwości nazwy użytkownika i hasła `PSCredential` obiektu, a następnie przekazywane do polecenia cmdlet.

  ```powershell
  $contosoConnection = Get-AutomationConnection -Name 'ContosoConnection'

  $cred = New-Object –TypeName System.Management.Automation.PSCredential –ArgumentList $contosoConnection.UserName, $contosoConnection.Password
  Connect-Contoso -Credential $cred
  }
  ```

  Łatwiejsze i skuteczniejsze podejście do to zachowanie jest bezpośrednie przekazanie obiektu połączenia do polecenia cmdlet:

  ```powershell
  $contosoConnection = Get-AutomationConnection -Name 'ContosoConnection'

  Connect-Contoso -Connection $contosoConnection
  }
  ```

  Aby umożliwić zachowanie podobnie jak w poprzednim przykładzie poleceń cmdlet, należy zezwolić im na akceptowanie obiektu połączenia bezpośrednio jako parametru, a nie tylko pól połączeń dla parametrów. Zazwyczaj chcesz ustawić parametr dla poszczególnych usług, dzięki czemu użytkownik nie używa usługi Azure Automation może wywoływać polecenia cmdlet bez tworzenia obiektu hashtable do działania jako obiekt połączenia. Zestaw parametrów `UserAccount`, używany do przekazywania połączenia właściwości pola. `ConnectionObject` Umożliwia przekazanie za pośrednictwem połączenia.

* Zdefiniuj typ danych wyjściowych dla wszystkich poleceń cmdlet w module. Zdefiniowanie typu danych wyjściowych polecenia cmdlet ułatwia określanie właściwości danych wyjściowych polecenia do użycia podczas tworzenia za pomocą funkcji IntelliSense czasu projektowania. Jest to szczególnie przydatne podczas automatyzacji tworzenia graficznego elementu runbook, gdzie znajomość czasu projektowania jest kluczem do prostego środowiska pracy użytkownika modułu.

  Można to osiągnąć przez dodanie `[OutputType([<MyOutputType>])]` gdzie MyOutputType jest prawidłowym typem. Aby dowiedzieć się więcej na temat OutputType, zobacz [o OutputTypeAttribute funkcji](/powershell/module/microsoft.powershell.core/about/about_functions_outputtypeattribute). Poniższy kod jest przykładem Dodawanie `OutputType` do polecenia cmdlet:

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

  To zachowanie jest podobne do funkcji "wpisywania z wyprzedzeniem" danych wyjściowych polecenia cmdlet środowiska PowerShell ISE bez konieczności jego uruchamiania.

  ![Funkcja IntelliSense programu POSH](../media/modules/automation-posh-ise-intellisense.png)

* Utwórz wszystkie polecenia cmdlet w module jako bezstanowe. W tej samej domenie aplikacji i tego samego procesu i piaskownicy jednocześnie uruchomić wiele zadań elementów runbook. W przypadku każdy stan udostępnione na tych poziomach zadania mogą wpływać na siebie nawzajem. To zachowanie może prowadzić do sporadycznych i trudne do diagnozowania problemów.  Oto przykład czynności, których nie należy wykonywać.

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

* Moduł powinien być w pełni zawarty w pakiecie z obsługą opcji xcopy. Moduły platformy Azure Automation są dystrybuowane do piaskownic usługi Automation, gdy trzeba wykonać elementy runbook. Moduły muszą działać niezależnie od hosta, na których są one uruchamiane w. Powinno być możliwe do pliku Zip przenoszenie pakietu modułu i powinien działać w zwykły sposób po zaimportowaniu do środowiska PowerShell innego hosta. Aby było to możliwe moduł nie powinna zależeć od żadnych plików poza folderem modułu. Ten folder jest folderem, pakowanym podczas importowania modułu do usługi Azure Automation. Moduł powinien również zależy od żadnych ustawień rejestru unikatowych na hoście, takie jak te ustawienia, gdy produkt jest zainstalowany. Wszystkie pliki w module powinny mieć mniej niż 140 znaków ścieżkę. Wszystkie ścieżki w ponad 140 znaków spowoduje, że importowanie elementu runbook. Jeśli na tym najlepszym rozwiązaniem, nie będzie przestrzegana, moduł nie będzie można używać w usłudze Azure Automation.  

* Jeśli odwołanie do [modułów programu Azure Powershell Az](/powershell/azure/new-azureps-module-az?view=azps-1.1.0) w module, upewnij się, nie są również odwołania do `AzureRM`. `Az` Module nie można używać w połączeniu z `AzureRM` modułów. `Az` jest obsługiwane w elementach runbook, ale nie są importowane domyślnie. Aby dowiedzieć się więcej na temat `Az` modułów i zagadnienia, weź pod uwagę, zobacz [Az Obsługa modułu w usłudze Azure Automation](../az-modules.md).

## <a name="default-modules"></a>Domyślne moduły

Poniższa tabela zawiera listę modułów, które są importowane domyślnie po utworzeniu konta usługi Automation. Moduły wymienione poniżej może mieć nowsze wersje je zaimportować, ale oryginalnej wersji nie można usunąć z konta usługi Automation, nawet wtedy, gdy usuniesz ich nowszą wersję.

|Nazwa modułu|Version|
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
| Orchestrator.AssetManagement.Cmdlets | 1 |
| PSDscResources | 2.9.0.0 |
| SecurityPolicyDsc | 2.1.0.0 |
| StateConfigCompositeResources | 1 |
| xDSCDomainjoin | 1.1 |
| xPowerShellExecutionPolicy | 1.1.0.0 |
| xRemoteDesktopAdmin | 1.1.0.0 |

## <a name="next-steps"></a>Następne kroki

* Aby dowiedzieć się więcej na temat tworzenia modułów programu PowerShell, zobacz artykuł [Writing a Windows PowerShell Module](https://msdn.microsoft.com/library/dd878310%28v=vs.85%29.aspx) (Pisanie modułu programu Windows PowerShell).