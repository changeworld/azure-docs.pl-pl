---
title: Parametry wejściowe elementu Runbook
description: Parametry wejściowe elementu Runbook zwiększyć elastyczność elementów runbook, umożliwiając przekazywanie danych do elementu runbook, gdy jest ona uruchamiana. W tym artykule opisano różne scenariusze, w której parametry wejściowe są używane w elementach runbook.
services: automation
ms.service: automation
ms.subservice: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 02/14/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 5f190d60a059108b9763f35e2ee8cf99ae77b694
ms.sourcegitcommit: c63fe69fd624752d04661f56d52ad9d8693e9d56
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2019
ms.locfileid: "58578156"
---
# <a name="runbook-input-parameters"></a>Parametry wejściowe elementu Runbook

Parametry wejściowe elementu Runbook zwiększyć elastyczność elementów runbook, umożliwiając przekazuje dane do niej, gdy jest ona uruchamiana. Parametry umożliwiają akcji elementu runbook, ma zostać użyty dla określonych scenariuszach i środowiskach. W tym artykule opisano różne scenariusze użycia parametrów wejściowych w elementach runbook.

## <a name="configure-input-parameters"></a>Konfigurowanie parametrów wejściowych

Parametry wejściowe można skonfigurować w programie PowerShell, przepływ pracy programu PowerShell, Python i graficzne elementy runbook. Element runbook może mieć wiele parametrów z różnymi typami danych nie parametrów lub na wszystkich. Parametry wejściowe może być wymagany lub opcjonalny i może mieć wartości domyślnej dla parametrów opcjonalnych. Możesz przypisać wartości parametrów wejściowych elementu runbook podczas jego uruchamiania za pomocą jednego z dostępnych metod. Te metody obejmują uruchamianie elementu runbook z witryny Azure portal, usługę sieci web lub programu PowerShell. Można także uruchomić jeden z nich jako podrzędnego elementu runbook o nazwie wbudowanego w innym elemencie runbook.

## <a name="configure-input-parameters-in-powershell-runbooks"></a>Konfigurowanie parametrów wejściowych w elementach runbook programu PowerShell

Runbook programu PowerShell i przepływie pracy programu PowerShell w usłudze Azure Automation obsługują parametrów wejściowych, które są definiowane za pomocą następujących atrybutów:  

| **Właściwość** | **Opis** |
|:--- |:--- |
| `Type` |Wymagany. Typ danych, oczekiwano wartości parametru. Przydaje się dowolnego typu platformy .NET. |
| `Name` |Wymagany. Nazwa parametru. To musi być unikatowa w obrębie elementu runbook i mogą zawierać tylko litery, cyfry lub znaki podkreślenia. Musi ona zaczynać się literą. |
| `Mandatory` |Opcjonalny. Określa, czy należy podać wartość dla parametru. Jeśli ustawisz to  **\$true**, a następnie po uruchomieniu elementu runbook należy podać wartość. Jeśli ustawisz to  **\$false**, wartość jest opcjonalna. |
| `Default value` |Opcjonalny. Określa wartość, która jest używana dla parametru, jeśli wartość nie zostanie przekazany, po uruchomieniu elementu runbook. Wartość domyślną można ustawić dla każdego parametru i automatycznie wprowadzi parametr opcjonalny, niezależnie od ustawienia obowiązkowe. |

Program Windows PowerShell obsługuje więcej atrybutów parametrów wejściowych, niż te wymienione w tym miejscu, takich jak sprawdzanie poprawności, aliasy, a parametr ustawia. Usługa Azure Automation obsługuje obecnie tylko poprzedniego parametrów wejściowych.

Definicji parametru w elementach runbook przepływu pracy programu PowerShell ma następującą postać ogólne, w których wiele parametrów są oddzielone przecinkami.

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
> Podczas definiowania parametrów, jeśli nie określisz **obowiązkowe** atrybutu, a następnie domyślnie parametru są traktowane jako opcjonalne. Ponadto jeśli ustawisz wartość domyślną dla parametru w elementach runbook przepływu pracy programu PowerShell, jest ona traktowana programu PowerShell, jako parametr opcjonalny, niezależnie od wartości **obowiązkowe** wartość atrybutu.

Na przykład Skonfigurujmy parametry wejściowe elementu runbook przepływu pracy programu PowerShell, który wyświetla szczegółowe informacje o maszynach wirtualnych, pojedynczej maszyny Wirtualnej lub wszystkich maszyn wirtualnych w grupie zasobów. Ten element runbook zawiera dwa parametry, jak pokazano na poniższym zrzucie ekranu: Nazwa maszyny wirtualnej i nazwę grupy zasobów.

![Przepływ pracy programu PowerShell usługi Automation](media/automation-runbook-input-parameters/automation-01-powershellworkflow.png)

W tej definicji parametru parametry  **\$VMName** i  **\$resourceGroupName** są proste parametry typu String. Jednak elementy runbook programu PowerShell i przepływie pracy programu PowerShell obsługują wszystkie typy proste i złożone typy, takie jak **obiektu** lub **PSCredential** dla parametrów wejściowych.

Jeśli element runbook ma parametr wejściowy typu obiektu, należy użyć tablicy skrótów środowiska PowerShell przez (nazwa, wartość) pary, aby przekazać wartość. Na przykład, jeśli masz następujący parametr w elemencie runbook:

```powershell
[Parameter (Mandatory = $true)]
[object] $FullName
```

Następnie można przekazać następującą wartość do parametru:

```powershell
@{"FirstName"="Joe";"MiddleName"="Bob";"LastName"="Smith"}
```

> [!NOTE]
> Podczas przekazywania żadnej wartości opcjonalny `[String]` typ parametru, który ma _wartość domyślna_ z `\$null`, nie będą wartości parametru _pusty ciąg_, **nie** `\$null`.

## <a name="configure-input-parameters-in-graphical-runbooks"></a>Konfigurowanie parametrów wejściowych w graficznych elementach runbook

Aby [skonfigurować graficznego elementu runbook](automation-first-runbook-graphical.md) z parametrami wejściowymi, Utwórzmy graficznego elementu runbook, który wyświetla szczegóły dotyczące maszyn wirtualnych albo jednej maszyny Wirtualnej lub wszystkich maszyn wirtualnych w grupie zasobów. Konfigurowanie elementu runbook składa się z dwóch głównych działań, zgodnie z poniższym opisem.

[**Uwierzytelnianie elementów Runbook przy użyciu konta Uruchom jako platformy Azure** ](automation-sec-configure-azure-runas-account.md) do uwierzytelniania za pomocą platformy Azure.

[**Get-AzureRmVm** ](/powershell/module/azurerm.compute/get-azurermvm) można pobrać właściwości maszyny wirtualnej.

Możesz użyć [ **Write-Output** ](/powershell/module/microsoft.powershell.utility/write-output) działanie wyjściowych nazwy maszyn wirtualnych. Działanie **Get-AzureRmVm** przyjmuje dwa parametry **nazwę maszyny wirtualnej** i **nazwy grupy zasobów**. Ponieważ te parametry może wymagać różne wartości w każdym uruchomieniu elementu runbook, możesz dodać do elementu runbook parametry wejściowe. Poniżej przedstawiono kroki, aby dodać parametry wejściowe:

1. Wybierz graficznego elementu runbook z **elementów Runbook** bloku, a następnie kliknij przycisk [ **Edytuj** ](automation-graphical-authoring-intro.md) go.
2. W edytorze elementów runbook, kliknij **dane wejściowe i wyjściowe** otworzyć **dane wejściowe i wyjściowe** bloku.

   ![Graficzny element runbook usługi Automation](media/automation-runbook-input-parameters/automation-02-graphical-runbok-editor.png)

3. **Dane wejściowe i wyjściowe** blok zawiera listę parametrów wejściowych, które są zdefiniowane dla elementu runbook. W tym bloku możesz dodać nowego parametru wejściowego lub edytować konfiguracji istniejącego parametru wejściowego. Aby dodać nowego parametru dla elementu runbook, kliknij **Dodaj dane wejściowe** otworzyć **parametr wejściowy elementu Runbook** bloku. Można skonfigurować następujące parametry:

   | **Właściwość** | **Opis** |
   |:--- |:--- |
   | `Name` |Wymagany. Nazwa parametru. To musi być unikatowa w obrębie elementu runbook i mogą zawierać tylko litery, cyfry lub znaki podkreślenia. Musi ona zaczynać się literą. |
   | `Description` |Opcjonalny. Opis dotyczące przeznaczenia parametru wejściowego. |
   | `Type` |Opcjonalny. Typ danych, który jest oczekiwany dla wartości parametru. Obsługiwane typy parametrów **ciąg**, **Int32**, **Int64**, **dziesiętna**, **logiczna**,  **Data i godzina**, i **obiektu**. Jeśli typem danych nie jest zaznaczone, domyślnie **ciąg**. |
   | `Mandatory` |Opcjonalny. Określa, czy należy podać wartość dla parametru. Jeśli wybierzesz **tak**, a następnie podać wartość po uruchomieniu elementu runbook. Jeśli wybierzesz **nie**, wartość nie jest wymagane, gdy element runbook jest uruchomiony i może zostać ustawiona wartość domyślna. |
   | `Default Value` |Opcjonalny. Określa wartość, która jest używana dla parametru, jeśli wartość nie zostanie przekazany, po uruchomieniu elementu runbook. Można ustawić wartości domyślnej parametru, która nie jest obowiązkowe. Aby ustawić wartość domyślną, wybierz **niestandardowe**. Ta wartość jest używana, chyba że inna wartość zostanie podana podczas uruchamiania elementu runbook. Wybierz **Brak** Jeśli nie chcesz podać dowolną wartość domyślną. |

    ![Dodaj nowe dane wejściowe](media/automation-runbook-input-parameters/automation-runbook-input-parameter-new.png)
4. Utwórz dwa parametry, z następującymi właściwościami, które są używane przez **Get-AzureRmVm** działania:

   * **Parametr Parameter1:**
     * Nazwa - VMName
     * Typ — ciąg
     * Obowiązkowe - Brak
   * **Parametr2:**
     * Nazwa - resourceGroupName
     * Typ — ciąg
     * Obowiązkowe - Brak
     * Wartość domyślna — niestandardowa
     * Niestandardowa wartość domyślna - \<nazwę grupy zasobów, która zawiera maszyny wirtualne\>

5. Po dodaniu parametry, kliknij przycisk **OK**. Teraz można wyświetlić je w **dane wejściowe i dane wyjściowe strony**. Kliknij przycisk **OK** ponownie, a następnie kliknij przycisk **Zapisz** i **Publikuj** elementu runbook.

## <a name="configure-input-parameters-in-python-runbooks"></a>Konfigurowanie parametrów wejściowych w elementach runbook języka Python

W przeciwieństwie do programu PowerShell, przepływ pracy programu PowerShell i graficzne elementy runbook elementy runbook języka Python nie przyjmują parametrów nazwanych.
Wszystkie parametry wejściowe są analizowane jako tablicę wartości argumentu.
Dostęp do tablicy przez zaimportowanie `sys` modułu do Twojego skryptu języka Python, a następnie `sys.argv` tablicy.
Ważne jest, aby pamiętać, że pierwszy element tablicy, `sys.argv[0]`, nazywa się skrypt, aby pierwszy rzeczywisty parametr wejściowy jest `sys.argv[1]`.

Na przykład jak używać parametrów wejściowych w elemencie runbook języka Python, zobacz [Mój pierwszy element runbook Python w usłudze Azure Automation](automation-first-runbook-textual-python2.md).

## <a name="assign-values-to-input-parameters-in-runbooks"></a>Przypisz wartości do wprowadzania parametrów w elementach runbook

Można przekazać wartości do wprowadzania parametrów w elementach runbook w następujących scenariuszach:

### <a name="start-a-runbook-and-assign-parameters"></a>Uruchamianie elementu runbook i przypisać parametry

Element runbook można uruchomić wiele sposobów: za pośrednictwem witryny Azure portal, za pomocą elementu webhook, za pomocą poleceń cmdlet programu PowerShell, za pomocą interfejsu API REST lub zestawu SDK. Poniżej omówiono sposoby uruchamiania elementu runbook i przypisywanie parametrów.

#### <a name="start-a-published-runbook-by-using-the-azure-portal-and-assign-parameters"></a>Uruchom opublikowanego elementu runbook za pomocą witryny Azure portal i parametry

Gdy użytkownik [uruchomić element runbook](start-runbooks.md#start-a-runbook-with-the-azure-portal), **uruchamianie elementu Runbook** zostanie otwarty blok i można wprowadzić wartości dla parametrów, które zostały utworzone.

![Rozpoczynanie korzystania z portalu](media/automation-runbook-input-parameters/automation-04-startrunbookusingportal.png)

W etykiecie poniżej pola wejściowego można zobaczyć atrybuty, które zostały ustawione dla parametru. Atrybuty zawierają wymagany lub opcjonalny, typ i wartość domyślną. W dymku pomocy obok nazwy parametru widać wszystkie kluczowe informacje potrzebne do podejmowania decyzji o wartości wejściowych parametrów. Informacje te obejmują, czy parametr jest wymagany lub opcjonalny. Obejmuje również typ i wartość domyślna (jeśli istnieje) i inne przydatne uwagi.

> [!NOTE]
> Obsługa parametrów typu ciąg **pusty** wartości ciągu.  Wprowadzanie **[ciąg EmptyString]** w parametrze wejściowym pole przekazuje do parametru pusty ciąg. Ponadto nie obsługuje parametrów typu ciąg **Null** wartości przekazywanych. Jeśli nie przekażesz żadnej wartości na parametr typu ciąg, następnie PowerShell zinterpretuje ją jako wartość null.

#### <a name="start-a-published-runbook-by-using-powershell-cmdlets-and-assign-parameters"></a>Uruchom opublikowanego elementu runbook za pomocą poleceń cmdlet programu PowerShell i parametry

* **Poleceń cmdlet usługi Azure Resource Manager:** Można uruchomić elementu runbook usługi Automation, który został utworzony w grupie zasobów przy użyciu [Start-AzureRmAutomationRunbook](/powershell/module/azurerm.automation/start-azurermautomationrunbook).
  
  **Przykład:**

  ```powershell
  $params = @{"VMName"="WSVMClassic";"resourceGroupeName"="WSVMClassicSG"}
  
  Start-AzureRmAutomationRunbook -AutomationAccountName "TestAutomation" -Name "Get-AzureVMGraphical" –ResourceGroupName $resourceGroupName -Parameters $params
  ```

* **Polecenia cmdlet modelu klasycznym wdrożeniu platformy Azure:** Można uruchomić elementu runbook usługi automation, który został utworzony w domyślnej grupie zasobów przy użyciu [Start AzureAutomationRunbook](/powershell/module/servicemanagement/azure/start-azureautomationrunbook).
  
  **Przykład:**

  ```powershell
  $params = @{"VMName"="WSVMClassic"; "ServiceName"="WSVMClassicSG"}
  
  Start-AzureAutomationRunbook -AutomationAccountName "TestAutomation" -Name "Get-AzureVMGraphical" -Parameters $params
  ```

> [!NOTE]
> Po uruchomieniu elementu runbook za pomocą poleceń cmdlet programu PowerShell, domyślnego parametru **MicrosoftApplicationManagementStartedBy** jest tworzona przy użyciu wartości **PowerShell**. Można wyświetlić tego parametru w **szczegóły zadania** strony.  

#### <a name="start-a-runbook-by-using-an-sdk-and-assign-parameters"></a>Uruchamianie elementu runbook za pomocą zestawu SDK i przypisać parametry

* **Usługa Azure Resource Manager metody:** Aby uruchomić element runbook, należy za pomocą zestawu SDK języka programowania. Poniżej przedstawiono fragment kodu języka C# do uruchamiania elementu runbook na Twoim koncie usługi Automation. Możesz wyświetlić cały kod w naszym [repozytorium GitHub](https://github.com/Azure/azure-sdk-for-net/blob/master/src/ResourceManagement/Automation/Automation.Tests/TestSupport/AutomationTestBase.cs).  

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

* **Metoda modelu klasycznym wdrożeniu platformy Azure:** Aby uruchomić element runbook, należy za pomocą zestawu SDK języka programowania. Poniżej przedstawiono fragment kodu języka C# do uruchamiania elementu runbook na Twoim koncie usługi Automation. Możesz wyświetlić cały kod w naszym [repozytorium GitHub](https://github.com/Azure/azure-sdk-for-net/blob/master/src/ServiceManagement/Automation/Automation.Tests/TestSupport/AutomationTestBase.cs).

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

  Aby uruchomić tę metodę, Utwórz słownik do przechowywania parametrów elementu runbook **VMName** i **resourceGroupName**i ich wartości. Następnie uruchom element runbook. Poniżej znajduje się fragment kodu języka C# dla wywołania metody, która jest zdefiniowana powyżej.

  ```csharp
  IDictionary<string, string> RunbookParameters = new Dictionary<string, string>();
  
  // Add parameters to the dictionary.
  RunbookParameters.Add("VMName", "WSVMClassic");
  RunbookParameters.Add("resourceGroupName", "WSSC1");
  
  //Call the StartRunbook method with parameters
  StartRunbook("Get-AzureVMGraphical", RunbookParameters);
  ```

#### <a name="start-a-runbook-by-using-the-rest-api-and-assign-parameters"></a>Uruchamianie elementu runbook za pomocą interfejsu API REST i przypisać parametry

Można tworzyć zadania elementu runbook i wprowadzenie interfejsu API REST usługi Azure Automation za pomocą **umieścić** metody za pomocą następującego identyfikatora URI żądania: `https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Automation/automationAccounts/{automationAccountName}/jobs/{jobName}?api-version=2017-05-15-preview`


W identyfikatorze URI żądania Zastąp następujące parametry:

* **subscriptionId:** Identyfikator subskrypcji platformy Azure.  
* **resourceGroupName:** Nazwa grupy zasobów dla konta usługi Automation.
* **automationAccountName:** Nazwa konta usługi automation, która znajduje się w obrębie określona usługa w chmurze.  
* **Nazwa zadania:** Identyfikator GUID dla zadania. Identyfikatory GUID w programie PowerShell można utworzyć za pomocą **[GUID]::NewGuid(). ToString()** polecenia.

Aby można było przekazać parametry dla zadania elementu runbook, należy użyć treści żądania. Trwa dwie poniższe właściwości podane w formacie JSON:

* **Nazwa elementu Runbook:** Wymagany. Nazwa elementu runbook, zadania rozpocząć.  
* **Parametry elementu Runbook:** Opcjonalny. Słownik listę parametrów (nazwa, wartość) sformatuj, gdzie nazwa powinna być typu String i wartość może być dowolną prawidłową wartość JSON.

Jeśli chcesz zacząć **Get AzureVMTextual** elementu runbook, który został wcześniej utworzony za pomocą **VMName** i **resourceGroupName** jako parametry, użyj następującego formatu JSON dla Treść żądania.

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

Jeśli utworzono zadanie, zwracany jest kod stanu HTTP 201. Aby uzyskać więcej informacji na nagłówki odpowiedzi i treść odpowiedzi, zobacz artykuł o [utworzenia zadania elementu runbook za pomocą interfejsu API REST.](/rest/api/automation/job/create)

### <a name="test-a-runbook-and-assign-parameters"></a>Testowanie elementu runbook i przypisać parametry

Po użytkownik [przetestować wersję roboczą elementu runbook](automation-testing-runbook.md) przy użyciu opcji testu **Test** zostanie otwarta strona i można skonfigurować wartości parametrów, które zostały utworzone.

![Parametry testów i przypisz](media/automation-runbook-input-parameters/automation-06-testandassignparameters.png)

### <a name="link-a-schedule-to-a-runbook-and-assign-parameters"></a>Powiązanie harmonogramu z elementem runbook i przypisać parametry

Możesz [powiązania harmonogramu](automation-schedules.md) do elementu runbook, aby element runbook, który rozpoczyna się w określonym czasie. Możesz przypisać parametry wejściowe, utworzyć harmonogramu, gdy element runbook używa tych wartości, gdy jest ona uruchamiana zgodnie z harmonogramem. Nie można zapisać harmonogram, dopóki nie znajdują się wszystkie wartości parametrów obowiązkowych.

![Planowanie i przypisywanie parametrów](media/automation-runbook-input-parameters/automation-07-scheduleandassignparameters.png)

### <a name="create-a-webhook-for-a-runbook-and-assign-parameters"></a>Utwórz element webhook dla elementu runbook i przypisz parametrów

Możesz utworzyć [elementu webhook](automation-webhooks.md) dla elementu runbook i konfigurowanie parametrów wejściowych elementu runbook. Nie można zapisać elementu webhook, dopóki nie znajdują się wszystkie wartości parametrów obowiązkowych.

![Utwórz element webhook i przypisać parametry](media/automation-runbook-input-parameters/automation-08-createwebhookandassignparameters.png)

Podczas wykonywania elementu runbook za pomocą elementu webhook, wstępnie zdefiniowanych parametrów wejściowych **[Webhookdata](automation-webhooks.md#details-of-a-webhook)** jest wysyłany wraz z parametrami wejściowymi, które zostały zdefiniowane. Można kliknąć, aby rozwinąć **WebhookData** parametru, aby uzyskać więcej informacji.

![Parametr WebhookData](media/automation-runbook-input-parameters/automation-09-webhook-data-parameters.png)

## <a name="pass-a-json-object-to-a-runbook"></a>Przekazywanie obiektu JSON do elementu runbook

Może być przydatne do przechowywania danych, które mają być przekazane do elementu runbook w pliku JSON.
Na przykład może utworzyć pliku JSON, który zawiera wszystkie parametry, które mają być przekazane do elementu runbook. Aby to zrobić, musisz przekonwertować na ciąg za pomocą pliku JSON, a następnie przekonwertowania ciągu na obiekt programu PowerShell, zanim zostanie on przekazany do elementu runbook.

W tym przykładzie ma skrypt programu PowerShell, który wywołuje [Start-AzureRmAutomationRunbook](/powershell/module/azurerm.automation/start-azurermautomationrunbook) można uruchomić elementu runbook programu PowerShell, przekazanie zawartości za pomocą pliku JSON do elementu runbook.
Element runbook programu PowerShell uruchamia Maszynę wirtualną platformy Azure, wprowadzenie parametrów dla maszyny Wirtualnej z formatu JSON, która została przekazana.

### <a name="create-the-json-file"></a>Utwórz plik JSON

Wpisz następujący test w pliku tekstowym, a następnie zapisz go jako `test.json` pozwalającego na komputerze lokalnym.

```json
{
   "VmName" : "TestVM",
   "ResourceGroup" : "AzureAutomationTest"
}
```

### <a name="create-the-runbook"></a>Tworzenie elementu runbook

Utwórz nowy element runbook programu PowerShell o nazwie "Test-Json" w usłudze Azure Automation.
Aby dowiedzieć się, jak utworzyć nowy element runbook programu PowerShell, zobacz [Mój pierwszy element runbook programu PowerShell](automation-first-runbook-textual-powershell.md).

Aby zaakceptować danych JSON, element runbook musi podjąć obiekt jako parametr wejściowy.

Element runbook może następnie używać właściwości zdefiniowane w formacie JSON.

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

Zapisz i Opublikuj ten element runbook na Twoim koncie usługi Automation.

### <a name="call-the-runbook-from-powershell"></a>Wywołaj element runbook za pomocą programu PowerShell

Teraz możesz wywołać element runbook z komputera lokalnego przy użyciu programu Azure PowerShell.
Uruchom następujące polecenia środowiska PowerShell:

1. Logowanie do platformy Azure:

   ```powershell
   Connect-AzureRmAccount
   ```

   Monit o podanie poświadczeń platformy Azure.

   > [!IMPORTANT]
   > **Add-AzureRmAccount** teraz jest aliasem dla **Connect-AzureRMAccount**. Podczas wyszukiwania biblioteki elementów, jeśli nie widzisz **Connect-AzureRMAccount**, możesz użyć **Add-AzureRmAccount**, lub na koncie usługi Automation można zaktualizować moduły.

1. Pobierz zawartość pliku JSON, a następnie przekonwertować na ciąg:

   ```powershell
   $json =  (Get-content -path 'JsonPath\test.json' -Raw) | Out-string
   ```

   `JsonPath` jest to ścieżka, w której zapisano plik JSON.

1. Konwertuj zawartość ciągu `$json` na obiekt programu PowerShell:

   ```powershell
   $JsonParams = @{"json"=$json}
   ```

1. Utworzyć obiektu hashtable parametrów dla `Start-AzureRmAutomationRunbook`:

   ```powershell
   $RBParams = @{
        AutomationAccountName = 'AATest'
        ResourceGroupName = 'RGTest'
        Name = 'Test-Json'
        Parameters = $JsonParams
   }
   ```

   Należy zauważyć, że w przypadku ustawienia wartości `Parameters` na obiekt programu PowerShell, który zawiera wartości z pliku JSON.
1. Uruchamianie elementu runbook

   ```powershell
   $job = Start-AzureRmAutomationRunbook @RBParams
   ```

## <a name="next-steps"></a>Kolejne kroki

* Aby uzyskać szczegółowe informacje dotyczące różnych sposobów uruchamiania elementu runbook, zobacz [uruchamianie elementu runbook](automation-starting-a-runbook.md).
* Aby edytować element runbook tekstową, zapoznaj się [edytowanie tekstowych elementów runbook](automation-edit-textual-runbook.md).
* Edytuj graficzny element runbook, znaleźć [tworzenia elementów graficznych w usłudze Azure Automation](automation-graphical-authoring-intro.md).
