---
title: Parametry wejściowe elementu Runbook
description: Parametry wejściowe zestawu runbook zwiększają elastyczność śliwi, umożliwiając przekazywanie danych do egonatu po uruchomieniu. W tym artykule opisano różne scenariusze, w których parametry wejściowe są używane w żyłaczkach.
services: automation
ms.subservice: process-automation
ms.date: 02/14/2019
ms.topic: conceptual
ms.openlocfilehash: 3741adbab6bcbc68f266c331e3056013afc0105e
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/03/2020
ms.locfileid: "80656025"
---
# <a name="runbook-input-parameters"></a>Parametry wejściowe elementu Runbook

Parametry wejściowe zestawu runbook zwiększają elastyczność uruchomieniu, umożliwiając przekazywanie do niego danych po uruchomieniu. Te parametry umożliwiają akcje uruchomieniu mają być kierowane do określonych scenariuszy i środowisk. W tym artykule opisano konfigurację i użycie parametrów wejściowych w ystawach runbook.

>[!NOTE]
>Ten artykuł został zaktualizowany o korzystanie z nowego modułu Azure PowerShell Az. Nadal możesz używać modułu AzureRM, który będzie nadal otrzymywać poprawki błędów do co najmniej grudnia 2020 r. Aby dowiedzieć się więcej na temat nowego modułu Az i zgodności z modułem AzureRM, zobacz [Wprowadzenie do nowego modułu Az programu Azure PowerShell](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Aby uzyskać instrukcje instalacji modułu Az w hybrydowym usłudze Runbook Worker, zobacz [Instalowanie modułu programu Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). Dla konta automatyzacji można zaktualizować moduły do najnowszej wersji przy użyciu [jak zaktualizować moduły programu Azure PowerShell w usłudze Azure Automation.](automation-update-azure-modules.md)

## <a name="configuring-input-parameters"></a>Konfigurowanie parametrów wejściowych

Można skonfigurować parametry wejściowe dla programów PowerShell, Przepływ pracy programu PowerShell, grafiki i python. Grupa runbook może mieć wiele parametrów z różnymi typami danych lub bez parametrów. Parametry wejściowe mogą być obowiązkowe lub opcjonalne i można użyć wartości domyślnych dla parametrów opcjonalnych.

Przystawieniu wartości do parametrów wejściowych dla wiązka uruchomieniu po jego uruchomieniu. Podręcznik runbook można uruchomić z witryny Azure portal, usługi sieci web lub programu PowerShell. Można również uruchomić jeden jako podrzędny runbook, który jest nazywany wbudowany w innym uruchomieniu. ścielicz.

### <a name="configure-input-parameters-in-powershell-runbooks"></a>Konfigurowanie parametrów wejściowych w łacinie programu PowerShell

Elementy runbooki przepływu pracy programu PowerShell i programu PowerShell w usłudze Azure Automation obsługują parametry wejściowe zdefiniowane za pomocą następujących właściwości. 

| **Właściwość** | **Opis** |
|:--- |:--- |
| Typ |Wymagany. Typ danych oczekiwany dla wartości parametru. Każdy typ .NET jest prawidłowy. |
| Nazwa |Wymagany. Nazwa parametru. Ta nazwa musi być unikatowa w elemicie runbooka, musi zaczynać się od litery i może zawierać tylko litery, cyfry lub znaki podkreślenia. |
| Obowiązkowy |Element opcjonalny. Wartość logiczna określająca, czy parametr wymaga wartości. Jeśli ustawisz tę wartość true, wartość musi być podana podczas uruchamiania śmięcie. Jeśli ustawisz to na False, wartość jest opcjonalna. Jeśli nie określisz wartości `Mandatory` właściwości, program PowerShell domyślnie uzna parametr wejściowy za opcjonalny. |
| Wartość domyślna |Element opcjonalny. Wartość, która jest używana dla parametru, jeśli nie wartość wejściowa jest przekazywana podczas uruchamiania runbook. Element runbook może ustawić wartość domyślną dla dowolnego parametru. |

Program Windows PowerShell obsługuje więcej atrybutów parametrów wejściowych niż wymienione powyżej, takich jak sprawdzanie poprawności, aliasy i zestawy parametrów. Jednak usługa Azure Automation obsługuje obecnie tylko wymienione właściwości parametru wejściowego.

Na przykład przyjrzyjmy się definicji parametrów w uruchomieniu łań szkoleniu programu PowerShell. Ta definicja ma następujący formularz ogólny, gdzie wiele parametrów są oddzielone przecinkami.

```powershell
Param
(
  [Parameter (Mandatory= $true/$false)]
  [Type] $Name1 = <Default value>,

  [Parameter (Mandatory= $true/$false)]
  [Type] $Name2 = <Default value>
)
```

Teraz skonfigurujmy parametry wejściowe dla wiązka przepływu pracy programu PowerShell, która wyprowadza szczegółowe informacje o maszynach wirtualnych, pojedynczej maszynie wirtualnej lub wszystkich maszynach wirtualnych w grupie zasobów. Ten projekt runbook ma dwa parametry, jak pokazano na poniższym zrzucie ekranu: nazwa maszyny wirtualnej (`VMName`) i nazwa grupy zasobów (`resourceGroupName`).

![Przepływ pracy programu PowerShell automatyzacji](media/automation-runbook-input-parameters/automation-01-powershellworkflow.png)

W tej definicji parametrów wejściowych są proste parametry ciągu typu.

Należy zauważyć, że programy PowerShell i PowerShell Workflow runbooks obsługują wszystkie proste typy i typy złożone, takie jak `Object` lub `PSCredential` dla parametrów wejściowych. Jeśli element runbook ma parametr wejściowy obiektu, należy użyć skrótu programu PowerShell z parami nazwa-wartość, aby przekazać w wartości. Na przykład masz następujący parametr w elementów runbook.

```powershell
[Parameter (Mandatory = $true)]
[object] $FullName
```

W takim przypadku można przekazać następującą wartość do parametru.

```powershell
@{"FirstName"="Joe";"MiddleName"="Bob";"LastName"="Smith"}
```

> [!NOTE]
> Jeśli wartość nie zostanie przekażena do opcjonalnego parametru String z wartością domyślną null, wartość parametru jest pustym ciągiem zamiast Null.

### <a name="configure-input-parameters-in-graphical-runbooks"></a>Konfigurowanie parametrów wejściowych w elementach runbook graficznych

Aby zilustrować konfigurację parametrów wejściowych dla graficznego zestawu runbook, utwórzmy element runbook, który wyprowadza szczegóły dotyczące maszyn wirtualnych, pojedynczej maszyny wirtualnej lub wszystkich maszyn wirtualnych w ramach grupy zasobów. Aby uzyskać szczegółowe informacje, zobacz [Mój pierwszy graficzny element runbook](automation-first-runbook-graphical.md).

Graficzny element runbook używa tych głównych działań żyła:

* Konfiguracja konta Azure Run As do uwierzytelniania za pomocą platformy Azure. 
* Definicja polecenia cmdlet [Get-AzVM,](https://docs.microsoft.com/powershell/module/az.compute/get-azvm?view=azps-3.5.0) aby uzyskać właściwości maszyny Wirtualnej.
* Użyj [działania zapisu i wyjścia](/powershell/module/microsoft.powershell.utility/write-output) do wyprowadzania nazw maszyn wirtualnych. 

Działanie `Get-AzVM` definiuje dwa dane wejściowe: nazwę maszyny Wirtualnej i nazwę grupy zasobów. Ponieważ te nazwy mogą być różne przy każdym uruchomieniu zestawu runbook, należy dodać parametry wejściowe do egobratu, aby zaakceptować te dane wejściowe. Zapoznaj się [z tworzeniem graficznym w usłudze Azure Automation](automation-graphical-authoring-intro.md).

Wykonaj następujące kroki, aby skonfigurować parametry wejściowe.

1. Wybierz element runbook graficzny ze strony Runbooks, a następnie kliknij pozycję **Edytuj**.
2. W edytorze graficznym kliknij przycisk **Wejście i wyjście,** a następnie **dodaj dane wejściowe,** aby otworzyć okienko Parametr wejściowy księgi umień.

   ![Graficzny projekt graficzny automatyzacji](media/automation-runbook-input-parameters/automation-02-graphical-runbok-editor.png)

3. Formant Wejście i Wyjście wyświetla listę parametrów wejściowych zdefiniowanych dla ego księgi runbook. W tym miejscu można dodać nowy parametr wejściowy lub edytować konfigurację istniejącego parametru wejściowego. Aby dodać nowy parametr dla elementu runbook, kliknij przycisk **Dodaj dane wejściowe,** aby otworzyć blok **parametrów wejściowych elementu Runbook,** gdzie można skonfigurować parametry przy użyciu właściwości zdefiniowanych w [obszarze Tworzenie graficzne w usłudze Azure Automation](automation-graphical-authoring-intro.md).

    ![Dodawanie nowych danych wejściowych](media/automation-runbook-input-parameters/automation-runbook-input-parameter-new.png)
4. Utwórz dwa parametry z następującymi właściwościami, które mają być używane przez `Get-AzVM` działanie, a następnie kliknij przycisk **OK**.

   * Parametr 1:
        * **Nazwa** -- **VMName**
        * **Typ** -- Ciąg
        * **Obowiązkowy** -- **nr**

   * Parametr 2:
        * **Nazwa** -- **zasobuGroupName**
        * **Typ** -- Ciąg
        * **Obowiązkowy** -- **nr**
        * **Wartość domyślna** -- **Niestandardowa**
        * Niestandardowa wartość domyślna — nazwa grupy zasobów zawierającej maszyny wirtualne

5. Służy do wyświetlania parametrów w formancie Wejście i Wyjście. 
6. Kliknij ponownie **przycisk OK,** a następnie kliknij pozycję **Zapisz**.
7. Kliknij **pozycję Publikuj,** aby opublikować swój podręcznik.

### <a name="configure-input-parameters-in-python-runbooks"></a>Konfigurowanie parametrów wejściowych w łańce runbook python

W przeciwieństwie do programu PowerShell, przepływu pracy programu PowerShell i uruchomieniu grafików, podręczniki python nie przyjmują nazwanych parametrów. Edytor wiązki uruchomieniu analizuje wszystkie parametry wejściowe jako tablicę wartości argumentów. Dostęp do tablicy można `sys` uzyskać, importując moduł do `sys.argv` skryptu języka Python, a następnie używając tablicy. Należy pamiętać, że pierwszy element tablicy `sys.argv[0]`, jest nazwą skryptu. Dlatego pierwszym rzeczywistym parametrem `sys.argv[1]`wejściowym jest .

Na przykład, jak używać parametrów wejściowych w uruchomieniu pythonie, zobacz [Mój pierwszy element runbook Python w usłudze Azure Automation](automation-first-runbook-textual-python2.md).

## <a name="assigning-values-to-input-parameters-in-runbooks"></a>Przypisywanie wartości do parametrów wejściowych w ystawach runbook

W tej sekcji opisano kilka sposobów przekazywania wartości do parametrów wejściowych w yjsce. Wartości parametrów można przypisać podczas:

* [Uruchamianie elementu Runbook](#start-a-runbook-and-assign-parameters)
* [Testowanie elementu Runbook](#test-a-runbook-and-assign-parameters)
* [Łączenie harmonogramu dla runbooka](#link-a-schedule-to-a-runbook-and-assign-parameters)
* [Tworzenie elementu webhook dla elementu runbook](#create-a-webhook-for-a-runbook-and-assign-parameters)

### <a name="start-a-runbook-and-assign-parameters"></a>Uruchamianie wiązka eksty i przypisywanie parametrów

Element runbook można uruchomić na wiele sposobów: za pośrednictwem witryny Azure portal, za pomocą elementu webhook, z poleceniami cmdlet programu PowerShell, z interfejsem API REST lub przy czym zestaw SDK. 

#### <a name="start-a-published-runbook-using-the-azure-portal-and-assign-parameters"></a>Uruchamianie opublikowanego zestawu runbook przy użyciu portalu Azure i przypisywanie parametrów

Po [uruchomieniu zestawu runbook](start-runbooks.md#start-a-runbook-with-the-azure-portal) w witrynie Azure portal otwiera się blok **Uruchom księgę uruchomień** i można wprowadzić wartości dla parametrów, które zostały utworzone.

![Rozpoczynanie korzystania z portalu](media/automation-runbook-input-parameters/automation-04-startrunbookusingportal.png)

W etykiecie pod polem wejściowym można zobaczyć właściwości, które zostały ustawione w celu zdefiniowania atrybutów parametrów, na przykład obowiązkowe lub opcjonalne, typ, wartość domyślna. Dymek pomocy obok nazwy parametru definiuje również kluczowe informacje potrzebne do podejmowania decyzji dotyczących wartości wejściowych parametrów. 

> [!NOTE]
> Parametry ciągu obsługują puste wartości typu String. Wprowadzenie `[EmptyString]` w polu parametru wejściowego powoduje przekroczenie pustego ciągu do parametru. Ponadto parametry ciągu nie obsługują null. Jeśli nie przekażesz żadnej wartości do parametru ciągu, program PowerShell interpretuje ją jako Null.

#### <a name="start-a-published-runbook-using-powershell-cmdlets-and-assign-parameters"></a>Uruchamianie opublikowanego zestawu runbook przy użyciu poleceń cmdlet programu PowerShell i przypisywanie parametrów

* **Polecenia cmdlet usługi Azure Resource Manager:** Element runbook automatyzacji, który został utworzony w grupie zasobów przy użyciu [start-AzAutomationRunbook](https://docs.microsoft.com/powershell/module/Az.Automation/Start-AzAutomationRunbook?view=azps-3.5.0
).

   ```powershell
     $params = @{"VMName"="WSVMClassic";"resourceGroupeName"="WSVMClassicSG"}
  
     Start-AzAutomationRunbook -AutomationAccountName "TestAutomation" -Name "Get-AzureVMGraphical" –ResourceGroupName $resourceGroupName -Parameters $params
   ```

* **Polecenia cmdlet modelu wdrażania klasycznego platformy Azure:** Można uruchomić element runbook automatyzacji, który został utworzony w domyślnej grupie zasobów przy użyciu [Start-AzureAutomationRunbook](/powershell/module/servicemanagement/azure/start-azureautomationrunbook).
  
   ```powershell
     $params = @{"VMName"="WSVMClassic"; "ServiceName"="WSVMClassicSG"}
  
     Start-AzureAutomationRunbook -AutomationAccountName "TestAutomation" -Name "Get-AzureVMGraphical" -Parameters $params
   ```

> [!NOTE]
> Po uruchomieniu wiązki ś.: po uruchomieniu polecenia `MicrosoftApplicationManagementStartedBy`cmdlet programu `PowerShell`PowerShell tworzony jest parametr domyślny z wartością . Ten parametr można wyświetlić w okienku Szczegóły zadania.  

#### <a name="start-a-runbook-using-an-sdk-and-assign-parameters"></a>Uruchamianie wiązka wiązkowego przy użyciu zestawu SDK i przypisywanie parametrów

* **Metoda usługi Azure Resource Manager:** Można uruchomić program runbook przy użyciu sdk języka programowania. Poniżej znajduje się fragment kodu języka C# do uruchamiania systemu runbook na koncie automatyzacji. Możesz zobaczyć cały kod w naszym [repozytorium GitHub](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/automation/Microsoft.Azure.Management.Automation/tests/TestSupport/AutomationTestBase.cs).

   ```csharp
   public Job StartRunbook(string runbookName, IDictionary<string, string> parameters = null)
      {
        var response = AutomationClient.Jobs.Create(resourceGroupName, automationAccount, new JobCreateParameters
         {
            Properties = new JobCreateProperties
             {
                Runbook = new RunbookAssociationProperty
                 {
                   Name = runbookName
                 },
                   Parameters = parameters
             }
         });
      return response.Job;
      }
   ```

* **Klasyczna metoda modelu wdrażania platformy Azure:** Podręcznik runbook można uruchomić przy użyciu sdk języka programowania. Poniżej znajduje się fragment kodu języka C# do uruchamiania systemu runbook na koncie automatyzacji. Możesz zobaczyć cały kod w naszym [repozytorium GitHub](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/automation/Microsoft.Azure.Management.Automation/tests/TestSupport/AutomationTestBase.cs).

   ```csharp
  public Job StartRunbook(string runbookName, IDictionary<string, string> parameters = null)
    {
      var response = AutomationClient.Jobs.Create(automationAccount, new JobCreateParameters
    {
      Properties = new JobCreateProperties
         {
           Runbook = new RunbookAssociationProperty
         {
           Name = runbookName
              },
                Parameters = parameters
              }
       });
      return response.Job;
    }
   ```

   Aby uruchomić tę metodę, należy utworzyć słownik `VMName` do `resourceGroupName` przechowywania parametrów runbook i ich wartości. Następnie uruchom projekt runbooka. Poniżej znajduje się fragment kodu języka C# do wywoływania metody, która jest zdefiniowana powyżej.

   ```csharp
   IDictionary<string, string> RunbookParameters = new Dictionary<string, string>();
  
   // Add parameters to the dictionary.
  RunbookParameters.Add("VMName", "WSVMClassic");
   RunbookParameters.Add("resourceGroupName", "WSSC1");
  
   //Call the StartRunbook method with parameters
   StartRunbook("Get-AzureVMGraphical", RunbookParameters);
   ```

#### <a name="start-a-runbook-using-the-rest-api-and-assign-parameters"></a>Uruchamianie wiązka wiązkowego przy użyciu interfejsu API REST i przypisywanie parametrów

Zadanie uruchamiania i uruchamianie go za pomocą interfejsu `PUT` API REST usługi Azure Automation można utworzyć i uruchomić przy użyciu metody z następującym identyfikatorem URI żądania:`https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Automation/automationAccounts/{automationAccountName}/jobs/{jobName}?api-version=2017-05-15-preview`

W identyfikatorze URI żądania zastąp następujące parametry:

* `subscriptionId`: Identyfikator subskrypcji platformy Azure.  
* `resourceGroupName`: Nazwa grupy zasobów dla konta Automatyzacja.
* `automationAccountName`: Nazwa konta automatyzacji, które jest hostowane w ramach określonej usługi w chmurze.  
* `jobName`: Identyfikator GUID dla zadania. Identyfikatory GUID w programie PowerShell można tworzyć za pomocą programu `[GUID]::NewGuid().ToString()*`.

Aby przekazać parametry do zadania runbook, należy użyć treści żądania. Zawiera następujące informacje, dostarczone w formacie JSON:

* Nazwa ekwidu: Wymagane. Nazwa systemu runbook dla zadania do uruchomienia.  
* Parametry eksmisji: Opcjonalnie. Słownik listy parametrów w formacie (nazwa, wartość), gdzie nazwa jest typu String i wartość może być dowolną prawidłową wartością JSON.

Jeśli chcesz uruchomić element runbook **Get-AzureVMTextual** utworzony wcześniej z `VMName` i `resourceGroupName` jako parametry, użyj następującego formatu JSON dla treści żądania.

```json
    {
      "properties":{
        "runbook":{
        "name":"Get-AzureVMTextual"},
      "parameters":{
         "VMName":"WindowsVM",
         "resourceGroupName":"ContosoSales"}
        }
    }
```

Kod stanu HTTP 201 jest zwracany, jeśli zadanie zostanie pomyślnie utworzone. Aby uzyskać więcej informacji na temat nagłówków odpowiedzi i treści odpowiedzi, zobacz [tworzenie zadania uruchomieniu przy użyciu interfejsu API REST](/rest/api/automation/job/create).

### <a name="test-a-runbook-and-assign-parameters"></a>Testowanie wiązka umie i przypisywanie parametrów

Podczas [testowania wersji roboczej roboczej roboczej roboczej](automation-testing-runbook.md) przy użyciu opcji testu zostanie otwarta strona testowa. Ta strona służy do konfigurowania wartości utworzonych parametrów.

![Testowanie i przypisywanie parametrów](media/automation-runbook-input-parameters/automation-06-testandassignparameters.png)

### <a name="link-a-schedule-to-a-runbook-and-assign-parameters"></a>Łączenie harmonogramu z schematem śmiwalna i przypisywanie parametrów

Harmonogram można [połączyć z zestawem](automation-schedules.md) runbook, tak aby projekt runbook został uruchomiony o określonej godzinie. Parametry wejściowe są przypisywane podczas tworzenia harmonogramu, a projekt runbook używa tych wartości, gdy jest on uruchamiany przez harmonogram. Nie można zapisać harmonogramu, dopóki nie zostaną podane wszystkie obowiązkowe wartości parametrów.

![Planowanie i przypisywanie parametrów](media/automation-runbook-input-parameters/automation-07-scheduleandassignparameters.png)

### <a name="create-a-webhook-for-a-runbook-and-assign-parameters"></a>Tworzenie elementu webhook dla elementu runbook i przypisywanie parametrów

Można utworzyć [element webhook](automation-webhooks.md) dla elementu runbook i skonfigurować parametry wejściowe elementu runbook. Nie można zapisać elementu webhook, dopóki nie zostaną podane wszystkie obowiązkowe wartości parametrów.

![Tworzenie elementu webhook i przypisywanie parametrów](media/automation-runbook-input-parameters/automation-08-createwebhookandassignparameters.png)

Podczas wykonywania elementu runbook przy użyciu elementu webhook, `[WebhookData](automation-webhooks.md)` wstępnie zdefiniowany parametr wejściowy jest wysyłany wraz z parametrami wejściowymi, które można zdefiniować. 

![Parametr WebhookData](media/automation-runbook-input-parameters/automation-09-webhook-data-parameters.png)

## <a name="passing-a-json-object-to-a-runbook"></a>Przekazywanie obiektu JSON do śmigła

Może być przydatne do przechowywania danych, które mają być przekazać do żyrówki w pliku JSON. Na przykład można utworzyć plik JSON, który zawiera wszystkie parametry, które mają być przedysy, które mają być przedysy, które mają zostać przedysze do wiązki kreślenia. Aby to zrobić, należy przekonwertować kod JSON na ciąg, a następnie przekonwertować ciąg na obiekt programu PowerShell przed przekazaniem go do śmiga.

W tej sekcji użyto przykładu, w którym skrypt programu PowerShell wywołuje [start-AzAutomationRunbook,](https://docs.microsoft.com/powershell/module/az.automation/start-azautomationrunbook?view=azps-3.5.0) aby uruchomić element runbook programu PowerShell, przekazując zawartość pliku JSON do żyjącego. Element runbook programu PowerShell uruchamia maszynę wirtualną platformy Azure, pobierając parametry maszyny Wirtualnej z obiektu JSON.

### <a name="create-the-json-file"></a>Tworzenie pliku JSON

Wpisz następujący kod w pliku tekstowym i zapisz go jako **test.json** gdzieś na komputerze lokalnym.

```json
{
   "VmName" : "TestVM",
   "ResourceGroup" : "AzureAutomationTest"
}
```

### <a name="create-the-runbook"></a>Tworzenie śmiętu

Utwórz nowy projekt umownie programu PowerShell o nazwie **Test-Json** w usłudze Azure Automation. Zobacz [Mój pierwszy program runbook programu PowerShell](automation-first-runbook-textual-powershell.md).

Aby zaakceptować dane JSON, element runbook musi wziąć obiekt jako parametr wejściowy. Elementu runbook można następnie użyć właściwości zdefiniowanych w pliku JSON.

```powershell
Param(
     [parameter(Mandatory=$true)]
     [object]$json
)

# Connect to Azure account
$Conn = Get-AutomationConnection -Name AzureRunAsConnection
Connect-AzAccount -ServicePrincipal -Tenant $Conn.TenantID `
    -ApplicationID $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint

# Convert object to actual JSON
$json = $json | ConvertFrom-Json

# Use the values from the JSON object as the parameters for your command
Start-AzVM -Name $json.VMName -ResourceGroupName $json.ResourceGroup
```

Zapisz i opublikuj ten projekt runbook na koncie automatyzacji.

### <a name="call-the-runbook-from-powershell"></a>Wywoływanie uruchomieniu z programu PowerShell

Teraz można wywołać runbook z komputera lokalnego przy użyciu programu Azure PowerShell. 

1. Zaloguj się na platformie Azure, jak pokazano. Następnie zostanie wyświetlony monit o wprowadzenie poświadczeń platformy Azure.

   ```powershell
   Connect-AzAccount
   ```

    >[!NOTE]
    >Dla uruchomieniu programu PowerShell `Add-AzAccount` i `Add-AzureRMAccount` są `Connect-AzAccount`aliasy dla . Należy zauważyć, że te aliasy nie są dostępne dla cząstek graficznych. Graficzny element runbook może `Connect-AzAccount` używać tylko siebie.

1. Pobierz zawartość zapisanego pliku JSON i przekonwertuj go na ciąg. `JsonPath`wskazuje ścieżkę, w której zapisano plik JSON.

   ```powershell
   $json =  (Get-content -path 'JsonPath\test.json' -Raw) | Out-string
   ```

1. Konwertuj `$json` zawartość ciągu na obiekt programu PowerShell.

   ```powershell
   $JsonParams = @{"json"=$json}
   ```

1. Utwórz skrót dla parametrów `Start-AzAutomationRunbook`dla . 

   ```powershell
   $RBParams = @{
        AutomationAccountName = 'AATest'
        ResourceGroupName = 'RGTest'
        Name = 'Test-Json'
        Parameters = $JsonParams
   }
   ```

   Należy zauważyć, że ustawiasz `Parameters` wartość obiektu programu PowerShell, który zawiera wartości z pliku JSON.
1. Uruchom program runbook.

   ```powershell
   $job = Start-AzAutomationRunbook @RBParams
   ```

## <a name="next-steps"></a>Następne kroki

* Aby uzyskać szczegółowe informacje na temat różnych sposobów uruchamiania śmiętu, zobacz [Uruchamianie ekstysu](automation-starting-a-runbook.md).
* Aby edytować tekstowy projekt runbook, zobacz [Edytowanie tekstów eksmisjalnych](automation-edit-textual-runbook.md).
* Aby edytować element runbook graficzny, zobacz [Tworzenie graficzne w usłudze Azure Automation](automation-graphical-authoring-intro.md).
