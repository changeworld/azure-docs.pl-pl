---
title: Parametry wejściowe elementu Runbook
description: Parametry wejściowe elementu Runbook zwiększają elastyczność elementów Runbook, umożliwiając przekazywanie danych do elementu Runbook po jego uruchomieniu. W tym artykule opisano różne scenariusze, w których parametry wejściowe są używane w elementach Runbook.
services: automation
ms.subservice: process-automation
ms.date: 02/14/2019
ms.topic: conceptual
ms.openlocfilehash: b16219c34ea30b4229195c8f019dfa8e1f147d8b
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75417584"
---
# <a name="runbook-input-parameters"></a>Parametry wejściowe elementu Runbook

Parametry wejściowe elementu Runbook zwiększają elastyczność elementów Runbook, umożliwiając przekazywanie do nich danych po ich uruchomieniu. Parametry umożliwiają działanie elementu Runbook w określonych scenariuszach i środowiskach. W tym artykule omówiono różne scenariusze, w których parametry wejściowe są używane w elementach Runbook.

## <a name="configure-input-parameters"></a>Konfigurowanie parametrów wejściowych

Parametry wejściowe można skonfigurować w programie PowerShell, w przepływie pracy programu PowerShell, w języku Python i graficznych elementach Runbook. Element Runbook może mieć wiele parametrów z różnymi typami danych lub nie ma żadnych parametrów. Parametry wejściowe mogą być obowiązkowe lub opcjonalne i można mieć wartość domyślną dla parametrów opcjonalnych. Można przypisać wartości do parametrów wejściowych elementu Runbook po jego uruchomieniu za pomocą jednej z dostępnych metod. Te metody obejmują Uruchamianie elementu Runbook z Azure Portal, usługi sieci Web lub programu PowerShell. Możesz również uruchomić jeden jako podrzędny element Runbook, który jest wywoływany w innym elemencie Runbook.

## <a name="configure-input-parameters-in-powershell-runbooks"></a>Konfigurowanie parametrów wejściowych w elementach Runbook programu PowerShell

Elementy Runbook programu PowerShell i programu PowerShell w programie Azure Automation obsługują parametry wejściowe, które są zdefiniowane za pomocą następujących atrybutów:  

| **Właściwość** | **Opis** |
|:--- |:--- |
| `Type` |Wymagany. Oczekiwano typu danych dla wartości parametru. Dowolny typ .NET jest prawidłowy. |
| `Name` |Wymagany. Nazwa parametru. Ta wartość musi być unikatowa w elemencie Runbook i może zawierać tylko litery, cyfry lub znaki podkreślenia. Musi rozpoczynać się od litery. |
| `Mandatory` |Element opcjonalny. Określa, czy należy podać wartość parametru. Jeśli ustawisz tę opcję na **\$true**, należy podać wartość, gdy element Runbook zostanie uruchomiony. Jeśli ustawisz tę opcję na **\$false**, wartość jest opcjonalna. |
| `Default value` |Element opcjonalny. Określa wartość, która jest używana dla parametru, jeśli wartość nie jest przenoszona podczas uruchamiania elementu Runbook. Wartość domyślna można ustawić dla dowolnego parametru i automatycznie ustawić parametr jako opcjonalny niezależnie od obowiązkowego ustawienia. |

Program Windows PowerShell obsługuje więcej atrybutów parametrów wejściowych niż wymienione tutaj, takich jak Walidacja, aliasy i zestawy parametrów. Jednak Azure Automation obecnie obsługuje tylko poprzednie parametry wejściowe.

Definicja parametru w elementach Runbook przepływu pracy programu PowerShell ma następujący ogólny formularz, w którym wiele parametrów jest oddzielonych przecinkami.

```powershell
Param
(
  [Parameter (Mandatory= $true/$false)]
  [Type] $Name1 = <Default value>,

  [Parameter (Mandatory= $true/$false)]
  [Type] $Name2 = <Default value>
)
```

> [!NOTE]
> W przypadku definiowania parametrów, jeśli nie określisz **obowiązkowego** atrybutu, domyślnie parametr jest traktowany jako opcjonalny. Ponadto, jeśli ustawisz wartość domyślną dla parametru w elementach Runbook przepływu pracy programu PowerShell, jest on traktowany jako opcjonalny parametr programu PowerShell, niezależnie od **obowiązkowej** wartości atrybutu.

Na przykład skonfigurujemy parametry wejściowe dla elementu Runbook przepływu pracy programu PowerShell, który wyprowadza szczegółowe informacje o maszynach wirtualnych, jednej maszynie wirtualnej lub wszystkich maszynach wirtualnych w grupie zasobów. Ten element Runbook ma dwa parametry, jak pokazano na poniższym zrzucie ekranu: Nazwa maszyny wirtualnej i nazwa grupy zasobów.

![Przepływ pracy programu PowerShell automatyzacji](media/automation-runbook-input-parameters/automation-01-powershellworkflow.png)

W tej definicji parametru parametry **\$VMName** i **\$resourceGroupName** są prostymi parametrami typu String. Jednak elementy Runbook programu PowerShell i programu PowerShell obsługują wszystkie typy proste i typy złożone, takie jak **Object** lub **PSCredential** dla parametrów wejściowych.

Jeśli element Runbook ma parametr wejściowy typu Object, użyj obiektu Hashtable programu PowerShell z parami (nazwa, wartość), aby przekazać wartość. Jeśli na przykład w elemencie Runbook znajduje się następujący parametr:

```powershell
[Parameter (Mandatory = $true)]
[object] $FullName
```

Następnie można przekazać następującą wartość do parametru:

```powershell
@{"FirstName"="Joe";"MiddleName"="Bob";"LastName"="Smith"}
```

> [!NOTE]
> Gdy wartość nie zostanie przekazana do opcjonalnego parametru typu `[String]`, który ma _wartość domyślną_ `\$null`, wartość parametru będzie _ciągiem pustym_, a **nie** `\$null`.

## <a name="configure-input-parameters-in-graphical-runbooks"></a>Konfigurowanie parametrów wejściowych w graficznych elementach Runbook

Aby [skonfigurować graficzny element Runbook](automation-first-runbook-graphical.md) z parametrami wejściowymi, Utwórzmy graficzny element Runbook, który będzie wyprowadzał szczegółowe informacje o maszynach wirtualnych, POJEDYNCZą maszynę wirtualną lub wszystkie maszyny wirtualne w grupie zasobów. Konfigurowanie elementu Runbook składa się z dwóch głównych działań, zgodnie z poniższym opisem.

[**Uwierzytelnianie elementów Runbook przy użyciu konta Uruchom jako platformy Azure**](automation-sec-configure-azure-runas-account.md) w celu uwierzytelniania za pomocą platformy Azure.

Get [ **-AzureRmVm**](/powershell/module/azurerm.compute/get-azurermvm) , aby pobrać właściwości maszyny wirtualnej.

Działania [**Write-Output**](/powershell/module/microsoft.powershell.utility/write-output) można użyć do wyprowadzania nazw maszyn wirtualnych. W ramach działania **Get-AzureRmVm** są akceptowane dwa parametry, **Nazwa maszyny wirtualnej** i **Nazwa grupy zasobów**. Ponieważ te parametry mogą wymagać różnych wartości przy każdym uruchomieniu elementu Runbook, można dodać parametry wejściowe do elementu Runbook. Poniżej przedstawiono procedurę dodawania parametrów wejściowych:

1. Wybierz graficzny element Runbook z bloku **elementy Runbook** , a następnie kliknij przycisk [**Edytuj**](automation-graphical-authoring-intro.md) .
2. W Edytorze elementów Runbook kliknij pozycję **dane wejściowe i wyjściowe** , aby otworzyć blok **dane wejściowe i wyjściowe** .

   ![Graficzny element Runbook automatyzacji](media/automation-runbook-input-parameters/automation-02-graphical-runbok-editor.png)

3. W bloku **wejście i wyjście** zostanie wyświetlona lista parametrów wejściowych, które są zdefiniowane dla elementu Runbook. W tym bloku można dodać nowy parametr wejściowy lub edytować konfigurację istniejącego parametru wejściowego. Aby dodać nowy parametr dla elementu Runbook, kliknij przycisk **Dodaj dane wejściowe** , aby otworzyć blok **parametru wejściowego elementu Runbook** . W tym miejscu można skonfigurować następujące parametry:

   | **Właściwość** | **Opis** |
   |:--- |:--- |
   | `Name` |Wymagany. Nazwa parametru. Ta wartość musi być unikatowa w elemencie Runbook i może zawierać tylko litery, cyfry lub znaki podkreślenia. Musi rozpoczynać się od litery. |
   | `Description` |Element opcjonalny. Opis przeznaczenie parametru wejściowego. |
   | `Type` |Element opcjonalny. Typ danych, który jest oczekiwany dla wartości parametru. Obsługiwane typy parametrów to **String**, **Int32**, **Int64**, **Decimal**, **Boolean**, **DateTime**i **Object**. Jeśli typ danych nie jest zaznaczony, domyślnie jest to **ciąg**. |
   | `Mandatory` |Element opcjonalny. Określa, czy należy podać wartość parametru. Jeśli wybierzesz opcję **tak**, należy podać wartość przy uruchamianiu elementu Runbook. Jeśli wybierzesz opcję **nie**, wartość nie jest wymagana, gdy element Runbook zostanie uruchomiony, a wartość domyślna może zostać ustawiona. |
   | `Default Value` |Element opcjonalny. Określa wartość, która jest używana dla parametru, jeśli wartość nie jest przenoszona podczas uruchamiania elementu Runbook. Wartość domyślna można ustawić dla parametru, który nie jest obowiązkowy. Aby ustawić wartość domyślną, wybierz opcję **niestandardowy**. Ta wartość jest używana, chyba że zostanie podana inna wartość, gdy element Runbook zostanie uruchomiony. Wybierz opcję **Brak** , jeśli nie chcesz podawać żadnej wartości domyślnej. |

    ![Dodaj nowe dane wejściowe](media/automation-runbook-input-parameters/automation-runbook-input-parameter-new.png)
4. Utwórz dwa parametry o następujących właściwościach, które są używane przez działanie **Get-AzureRmVm** :

   * **Parametr1**
     * Nazwa — VMName
     * Ciąg typu
     * Obowiązkowe — nie
   * **Parameter2**
     * Nazwa — resourceGroupName
     * Ciąg typu
     * Obowiązkowe — nie
     * Wartość domyślna — niestandardowa
     * Niestandardowa wartość domyślna — nazwa \<grupy zasobów zawierającej maszyny wirtualne\>

5. Po dodaniu parametrów kliknij przycisk **OK**. Teraz można je wyświetlić na **stronie dane wejściowe i wyjściowe**. Kliknij ponownie przycisk **OK** , a następnie kliknij przycisk **Zapisz** i **Opublikuj** element Runbook.

## <a name="configure-input-parameters-in-python-runbooks"></a>Konfigurowanie parametrów wejściowych w elementach Runbook języka Python

W przeciwieństwie do programu PowerShell, przepływu pracy programu PowerShell i graficznych elementów Runbook, elementy Runbook języka Python nie pobierają parametrów nazwanych.
Wszystkie parametry wejściowe są analizowane jako tablica wartości argumentów.
Dostęp do tablicy można uzyskać, importując moduł `sys` do skryptu języka Python, a następnie używając tablicy `sys.argv`.
Należy zauważyć, że pierwszy element tablicy, `sys.argv[0]`, jest nazwą skryptu, więc pierwszy rzeczywisty parametr wejściowy jest `sys.argv[1]`.

Aby zapoznać się z przykładem użycia parametrów wejściowych w elemencie Runbook w języku Python, zobacz [mój pierwszy element Runbook w języku Python w Azure Automation](automation-first-runbook-textual-python2.md).

## <a name="assign-values-to-input-parameters-in-runbooks"></a>Przypisywanie wartości do parametrów wejściowych w elementach Runbook

Można przekazać wartości do parametrów wejściowych w elementach Runbook w następujących scenariuszach:

### <a name="start-a-runbook-and-assign-parameters"></a>Uruchamianie elementu Runbook i przypisywanie parametrów

Element Runbook można uruchomić na wiele sposobów: za pomocą Azure Portal, z elementem webhook, za pomocą poleceń cmdlet programu PowerShell, z interfejsem API REST lub z zestawem SDK. Poniżej omówiono różne metody uruchamiania elementu Runbook i przypisywania parametrów.

#### <a name="start-a-published-runbook-by-using-the-azure-portal-and-assign-parameters"></a>Uruchom opublikowany element Runbook przy użyciu Azure Portal i przypisz parametry

Po [uruchomieniu elementu Runbook](start-runbooks.md#start-a-runbook-with-the-azure-portal)zostanie otwarty blok **Uruchamianie elementu Runbook** , w którym można wprowadzić wartości dla utworzonych parametrów.

![Zacznij korzystać z portalu](media/automation-runbook-input-parameters/automation-04-startrunbookusingportal.png)

W etykiecie poniżej pola wejściowego można zobaczyć atrybuty, które zostały ustawione dla parametru. Atrybuty obejmują wartości obowiązkowe lub opcjonalne, typ i wartość domyślną. W dymku pomocy obok nazwy parametru można zobaczyć wszystkie najważniejsze informacje potrzebne do podejmowania decyzji dotyczących wartości wejściowych parametrów. Te informacje obejmują, czy parametr jest obowiązkowy, czy opcjonalny. Zawiera również typ i wartość domyślną (jeśli istnieje) oraz inne przydatne uwagi.

> [!NOTE]
> Parametry typu String obsługują **puste** wartości ciągu.  Wprowadzenie elementu **[EmptyString]** w polu parametru wejściowego powoduje przekazanie pustego ciągu do parametru. Ponadto parametry typu String nie obsługują wartości **null** , które są przesyłane. Jeśli żadna wartość nie zostanie przekazana do parametru ciągu, program PowerShell interpretuje ją jako wartość null.

#### <a name="start-a-published-runbook-by-using-powershell-cmdlets-and-assign-parameters"></a>Uruchamianie opublikowanego elementu Runbook za pomocą poleceń cmdlet programu PowerShell i przypisywanie parametrów

* **Polecenia cmdlet Azure Resource Manager:** Można uruchomić element Runbook usługi Automation, który został utworzony w grupie zasobów za pomocą polecenia [Start-AzureRmAutomationRunbook](/powershell/module/azurerm.automation/start-azurermautomationrunbook).
  
  **Przykład:**

  ```powershell
  $params = @{"VMName"="WSVMClassic";"resourceGroupeName"="WSVMClassicSG"}
  
  Start-AzureRmAutomationRunbook -AutomationAccountName "TestAutomation" -Name "Get-AzureVMGraphical" –ResourceGroupName $resourceGroupName -Parameters $params
  ```

* **Polecenia cmdlet klasycznego modelu wdrażania platformy Azure:** Można uruchomić element Runbook usługi Automation, który został utworzony w domyślnej grupie zasobów za pomocą polecenia [Start-AzureAutomationRunbook](/powershell/module/servicemanagement/azure/start-azureautomationrunbook).
  
  **Przykład:**

  ```powershell
  $params = @{"VMName"="WSVMClassic"; "ServiceName"="WSVMClassicSG"}
  
  Start-AzureAutomationRunbook -AutomationAccountName "TestAutomation" -Name "Get-AzureVMGraphical" -Parameters $params
  ```

> [!NOTE]
> Po uruchomieniu elementu Runbook za pomocą poleceń cmdlet programu PowerShell, domyślny parametr **MicrosoftApplicationManagementStartedBy** jest tworzony przy użyciu wartości **PowerShell**. Ten parametr można wyświetlić na stronie **szczegóły zadania** .  

#### <a name="start-a-runbook-by-using-an-sdk-and-assign-parameters"></a>Uruchamianie elementu Runbook za pomocą zestawu SDK i przypisywanie parametrów

* **Metoda Azure Resource Manager:** Element Runbook można uruchomić za pomocą zestawu SDK języka programowania. Poniżej znajduje się C# fragment kodu służący do uruchamiania elementu Runbook na koncie usługi Automation. Możesz wyświetlić cały kod w naszym [repozytorium GitHub](https://github.com/Azure/azure-sdk-for-net/blob/master/src/ResourceManagement/Automation/Automation.Tests/TestSupport/AutomationTestBase.cs).  

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

* **Metoda klasycznego modelu wdrażania platformy Azure:** Element Runbook można uruchomić za pomocą zestawu SDK języka programowania. Poniżej znajduje się C# fragment kodu służący do uruchamiania elementu Runbook na koncie usługi Automation. Możesz wyświetlić cały kod w naszym [repozytorium GitHub](https://github.com/Azure/azure-sdk-for-net/blob/master/src/ServiceManagement/Automation/Automation.Tests/TestSupport/AutomationTestBase.cs).

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

  Aby rozpocząć tę metodę, Utwórz słownik do przechowywania parametrów elementu Runbook, **VMName** i **resourceGroupName**oraz ich wartości. Następnie Uruchom element Runbook. Poniżej znajduje się C# fragment kodu dotyczący wywoływania metody, która została zdefiniowana powyżej.

  ```csharp
  IDictionary<string, string> RunbookParameters = new Dictionary<string, string>();
  
  // Add parameters to the dictionary.
  RunbookParameters.Add("VMName", "WSVMClassic");
  RunbookParameters.Add("resourceGroupName", "WSSC1");
  
  //Call the StartRunbook method with parameters
  StartRunbook("Get-AzureVMGraphical", RunbookParameters);
  ```

#### <a name="start-a-runbook-by-using-the-rest-api-and-assign-parameters"></a>Uruchamianie elementu Runbook za pomocą interfejsu API REST i przypisywanie parametrów

Zadanie elementu Runbook można utworzyć i uruchomić przy użyciu interfejsu API REST Azure Automation za pomocą metody **Put** z następującym identyfikatorem URI żądania: `https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Automation/automationAccounts/{automationAccountName}/jobs/{jobName}?api-version=2017-05-15-preview`


W identyfikatorze URI żądania Zastąp następujące parametry:

* **Identyfikator subskrypcji:** Identyfikator subskrypcji platformy Azure.  
* **resourceGroupName:** Nazwa grupy zasobów dla konta usługi Automation.
* **automationAccountName:** Nazwa konta usługi Automation hostowanego w ramach określonej usługi w chmurze.  
* **jobName:** Identyfikator GUID zadania. Identyfikatory GUID w programie PowerShell można utworzyć przy użyciu **[Guid]:: NewGuid (). Polecenie ToString ()** .

Aby przekazać parametry do zadania elementu Runbook, użyj treści żądania. W formacie JSON dostępne są następujące dwie właściwości:

* **Nazwa elementu Runbook:** Wymagane. Nazwa elementu Runbook do uruchomienia zadania.  
* **Parametry elementu Runbook:** Obowiązkowe. Słownik listy parametrów w formacie (nazwa, wartość), gdzie nazwa powinna być typu String, a wartość może być dowolną prawidłową wartością JSON.

Jeśli chcesz uruchomić element Runbook **Get-AzureVMTextual** , który został utworzony wcześniej przy użyciu **VMName** i **resourceGroupName** jako parametry, użyj następującego formatu JSON dla treści żądania.

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

Kod stanu HTTP 201 jest zwracany, jeśli zadanie zostało pomyślnie utworzone. Aby uzyskać więcej informacji na temat nagłówków odpowiedzi i treści odpowiedzi, zobacz artykuł dotyczący [tworzenia zadania elementu Runbook za pomocą interfejsu API REST.](/rest/api/automation/job/create)

### <a name="test-a-runbook-and-assign-parameters"></a>Testowanie elementu Runbook i przypisywanie parametrów

W przypadku [testowania wersji roboczej elementu Runbook](automation-testing-runbook.md) przy użyciu opcji test zostanie otwarta strona **test** i można skonfigurować wartości dla utworzonych przez siebie parametrów.

![Testowanie i przypisywanie parametrów](media/automation-runbook-input-parameters/automation-06-testandassignparameters.png)

### <a name="link-a-schedule-to-a-runbook-and-assign-parameters"></a>Łączenie harmonogramu z elementem Runbook i przypisywanie parametrów

Możesz [połączyć harmonogram z elementem](automation-schedules.md) Runbook, aby element Runbook uruchamiał się o określonej godzinie. Parametry wejściowe są przypisywane podczas tworzenia harmonogramu, a element Runbook używa tych wartości, gdy jest uruchamiany zgodnie z harmonogramem. Nie można zapisać harmonogramu, dopóki nie zostaną podane wszystkie obowiązkowe wartości parametrów.

![Planowanie i przypisywanie parametrów](media/automation-runbook-input-parameters/automation-07-scheduleandassignparameters.png)

### <a name="create-a-webhook-for-a-runbook-and-assign-parameters"></a>Tworzenie elementu webhook dla elementu Runbook i przypisywanie parametrów

Można utworzyć element [webhook](automation-webhooks.md) dla elementu Runbook i skonfigurować parametry wejściowe elementu Runbook. Nie można zapisać elementu webhook, dopóki nie zostaną podane wszystkie obowiązkowe wartości parametrów.

![Utwórz element webhook i przypisz parametry](media/automation-runbook-input-parameters/automation-08-createwebhookandassignparameters.png)

Po wykonaniu elementu Runbook za pomocą elementu webhook jest wysyłany wstępnie zdefiniowany parametr wejściowy **[Webhookdata](automation-webhooks.md#details-of-a-webhook)** wraz ze zdefiniowanymi parametrami wejściowymi. Możesz kliknąć, aby rozwinąć parametr **WebhookData** , aby uzyskać więcej szczegółów.

![WebhookData — parametr](media/automation-runbook-input-parameters/automation-09-webhook-data-parameters.png)

## <a name="pass-a-json-object-to-a-runbook"></a>Przekazywanie obiektu JSON do elementu Runbook

Może być przydatne do przechowywania danych, które mają zostać przekazane do elementu Runbook w pliku JSON.
Można na przykład utworzyć plik JSON zawierający wszystkie parametry, które mają zostać przekazane do elementu Runbook. W tym celu należy przekonwertować kod JSON na ciąg, a następnie przekonwertować ciąg na obiekt programu PowerShell przed przekazaniem go do elementu Runbook.

W tym przykładzie masz skrypt programu PowerShell, który wywołuje polecenie [Start-AzureRmAutomationRunbook](/powershell/module/azurerm.automation/start-azurermautomationrunbook) , aby uruchomić element Runbook programu PowerShell, przekazując zawartość pliku JSON do elementu Runbook.
Element Runbook programu PowerShell uruchamia maszynę wirtualną platformy Azure, pobierając parametry dla maszyny wirtualnej z pliku JSON, który został przekazano.

### <a name="create-the-json-file"></a>Utwórz plik JSON

Wpisz następujący test w pliku tekstowym i Zapisz go jako `test.json` gdzieś na komputerze lokalnym.

```json
{
   "VmName" : "TestVM",
   "ResourceGroup" : "AzureAutomationTest"
}
```

### <a name="create-the-runbook"></a>Tworzenie elementu Runbook

Utwórz nowy element Runbook programu PowerShell o nazwie "test-JSON" w Azure Automation.
Aby dowiedzieć się, jak utworzyć nowy element Runbook programu PowerShell, zobacz [mój pierwszy element Runbook programu PowerShell](automation-first-runbook-textual-powershell.md).

Aby zaakceptować dane JSON, element Runbook musi przyjmować obiekt jako parametr wejściowy.

Element Runbook może następnie użyć właściwości zdefiniowanych w formacie JSON.

```powershell
Param(
     [parameter(Mandatory=$true)]
     [object]$json
)

# Connect to Azure account
$Conn = Get-AutomationConnection -Name AzureRunAsConnection
Connect-AzureRmAccount -ServicePrincipal -Tenant $Conn.TenantID `
    -ApplicationID $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint

# Convert object to actual JSON
$json = $json | ConvertFrom-Json

# Use the values from the JSON object as the parameters for your command
Start-AzureRmVM -Name $json.VMName -ResourceGroupName $json.ResourceGroup
```

Zapisz i Opublikuj ten element Runbook na koncie usługi Automation.

### <a name="call-the-runbook-from-powershell"></a>Wywoływanie elementu Runbook z programu PowerShell

Teraz można wywołać element Runbook z komputera lokalnego przy użyciu Azure PowerShell.
Uruchom następujące polecenia programu PowerShell:

1. Zaloguj się do platformy Azure:

   ```powershell
   Connect-AzureRmAccount
   ```

   Zostanie wyświetlony monit o wprowadzenie poświadczeń platformy Azure.

   > [!IMPORTANT]
   > **Add-AzureRmAccount** jest teraz aliasem dla polecenia **Connect-AzureRmAccount**. Podczas wyszukiwania elementów biblioteki, jeśli nie widzisz pozycji **Connect-AzureRMAccount**, możesz użyć polecenie **Add-AzureRMAccount**lub zaktualizować moduły na koncie usługi Automation.

1. Pobierz zawartość pliku JSON i przekonwertuj go na ciąg:

   ```powershell
   $json =  (Get-content -path 'JsonPath\test.json' -Raw) | Out-string
   ```

   `JsonPath` to ścieżka, w której zapisano plik JSON.

1. Przekonwertuj zawartość ciągu `$json` na obiekt programu PowerShell:

   ```powershell
   $JsonParams = @{"json"=$json}
   ```

1. Utwórz tablicę skrótów dla parametrów dla `Start-AzureRmAutomationRunbook`:

   ```powershell
   $RBParams = @{
        AutomationAccountName = 'AATest'
        ResourceGroupName = 'RGTest'
        Name = 'Test-Json'
        Parameters = $JsonParams
   }
   ```

   Należy zauważyć, że wartość `Parameters` jest ustawiana na obiekt programu PowerShell, który zawiera wartości z pliku JSON.
1. Uruchamianie elementu runbook

   ```powershell
   $job = Start-AzureRmAutomationRunbook @RBParams
   ```

## <a name="next-steps"></a>Następne kroki

* Aby uzyskać szczegółowe informacje o różnych sposobach uruchamiania elementu Runbook, zobacz [Uruchamianie elementu Runbook](automation-starting-a-runbook.md).
* Aby edytować tekstowy element Runbook, zobacz [Edytowanie tekstowych elementów Runbook](automation-edit-textual-runbook.md).
* Aby edytować graficzny element Runbook, zapoznaj się z tematem [Tworzenie graficzne w Azure Automation](automation-graphical-authoring-intro.md).
