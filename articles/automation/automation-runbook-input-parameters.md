---
title: Parametry wejściowe elementu Runbook
description: Parametry wejściowe elementu Runbook zwiększyć elastyczność elementów runbook, umożliwiając przekazywania danych do elementu runbook, gdy jest ona uruchamiana. W tym artykule opisano różne scenariusze, w których są używane parametry wejściowe w elementach runbook.
services: automation
ms.service: automation
ms.component: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 03/16/2018
ms.topic: article
manager: carmonm
ms.openlocfilehash: 3e1e00f81fe7761494ae73b2c8ac7419cb5ffa32
ms.sourcegitcommit: d28bba5fd49049ec7492e88f2519d7f42184e3a8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/11/2018
---
# <a name="runbook-input-parameters"></a>Parametry wejściowe elementu Runbook

Parametry wejściowe elementu Runbook zwiększyć elastyczność elementów runbook, umożliwiając przekazywania danych do niej po uruchomieniu. Parametry Zezwalaj na działania elementu runbook, które można zastosować dla określonych scenariuszach i środowiskach. W tym artykule należy przeprowadzenie różnych scenariuszy gdzie parametry wejściowe są używane w elementach runbook.

## <a name="configure-input-parameters"></a>Skonfiguruj parametry wejściowe

Parametry wejściowe można skonfigurować w programu PowerShell, przepływ pracy programu PowerShell, Python i graficznych elementów runbook. Element runbook może mieć wiele parametrów o różnych typach danych lub Brak parametrów w ogóle. Parametry wejściowe mogą być wymagane lub opcjonalne i można przypisać wartości domyślnej dla parametrów opcjonalnych. Po ponownym uruchomieniu za pomocą jednego z dostępnych metod, można przypisać wartości do parametrów wejściowych dla elementu runbook. Te metody obejmują uruchamianie elementu runbook z portalu lub usługi sieci web. Można również uruchomić jako podrzędnego elementu runbook, wywoływaną wbudowanego innego elementu runbook.

## <a name="configure-input-parameters-in-powershell-and-powershell-workflow-runbooks"></a>Skonfiguruj parametry wejściowe w elementach runbook programu PowerShell i przepływ pracy programu PowerShell

Środowiska PowerShell i [elementach runbook przepływu pracy programu PowerShell](automation-first-runbook-textual.md) automatyzacji Azure obsługuje parametry wejściowe zdefiniowane przez następujące atrybuty:  

| **Właściwość** | **Opis** |
|:--- |:--- |
| Typ |Wymagany. Typ danych dla wartości parametru. Dowolny typ .NET jest prawidłowy. |
| Name (Nazwa) |Wymagany. Nazwa parametru. To musi być unikatowe w obrębie elementu runbook i mogą zawierać tylko litery, cyfry lub znaki podkreślenia. Musi ona rozpoczynać się od litery. |
| Obowiązkowy |Opcjonalny. Określa, czy należy podać wartość parametru. Jeśli ustawisz to **$true**, a następnie po uruchomieniu elementu runbook, należy podać wartość. Jeśli ustawisz to **$false**, a następnie wartość jest opcjonalna. |
| Wartość domyślna |Opcjonalny. Określa wartość, która jest używana dla parametru, jeśli wartość nie zostanie przekazany, po uruchomieniu elementu runbook. Wartość domyślna można ustawić dla każdego parametru i automatycznie wprowadzi parametr opcjonalny niezależnie od ustawienia obowiązkowe. |

Program Windows PowerShell obsługuje więcej atrybutów parametrów wejściowych niż wymienione w tym miejscu, takich jak sprawdzanie poprawności, aliasy i zestawy parametrów. Automatyzacja Azure obsługuje obecnie tylko poprzednie parametry wejściowe.

Definicję parametrów w elementach runbook przepływu pracy programu PowerShell ma następujący format Ogólne, gdzie wiele parametrów są oddzielone przecinkami.

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
> Podczas definiowania parametrów, jeśli nie określisz **obowiązkowe** atrybutu, a następnie domyślnie parametr są traktowane jako opcjonalne. Ponadto jeśli ustawisz wartość domyślną dla parametru w elementach runbook przepływu pracy programu PowerShell, będzie traktowane przez programu PowerShell jako opcjonalny parametr niezależnie od tego **obowiązkowe** wartość atrybutu.
> 
> 

Na przykład Skonfigurujmy parametry wejściowe elementu runbook przepływu pracy programu PowerShell, która wyświetla szczegółowe informacje o maszynach wirtualnych, jednej maszyny Wirtualnej lub wszystkich maszyn wirtualnych w grupie zasobów. Ten element runbook zawiera dwa parametry, jak pokazano na poniższym zrzucie ekranu: Nazwa maszyny wirtualnej i nazwę grupy zasobów.

![Automatyzacja przepływu pracy programu PowerShell](media/automation-runbook-input-parameters/automation-01-powershellworkflow.png)

W definicji tego parametru, parametry **$VMName** i **$resourceGroupName** proste parametrów typu ciąg. Jednak elementów runbook programu PowerShell i przepływ pracy programu PowerShell obsługuje wszystkie typy proste i typów złożonych, takie jak **obiektu** lub **PSCredential** dla parametrów wejściowych.

Jeśli element runbook ma parametru wejściowego typu obiektu, użyj tablicy skrótów programu PowerShell z (nazwa, wartość) pary do przekazania wartości. Na przykład, jeśli masz następującego parametru w elemencie runbook:

```powershell
[Parameter (Mandatory = $true)]
[object] $FullName
```

Następnie można przekazać następującą wartość parametru:

```powershell
@{"FirstName"="Joe";"MiddleName"="Bob";"LastName"="Smith"}
```

## <a name="configure-input-parameters-in-graphical-runbooks"></a>Skonfiguruj parametry wejściowe w graficznych elementów runbook

Aby [skonfigurować graficznym elementem runbook](automation-first-runbook-graphical.md) z parametrami wejściowymi, Utwórzmy graficzny element runbook, która wyświetla szczegółowe informacje o maszynach wirtualnych, albo jednej maszyny Wirtualnej lub wszystkich maszyn wirtualnych w grupie zasobów. Konfigurowanie elementu runbook składa się z dwóch głównych działań, zgodnie z poniższym opisem.

[**Uwierzytelniania elementów Runbook za pomocą konta Uruchom jako platformy Azure** ](automation-sec-configure-azure-runas-account.md) do uwierzytelniania w usłudze Azure.

[**Get-AzureRmVm** ](https://msdn.microsoft.com/library/mt603718.aspx) można pobrać właściwości maszyny wirtualnej.

Można użyć [ **Write-Output** ](https://technet.microsoft.com/library/hh849921.aspx) działania do wyjściowego nazwy maszyn wirtualnych. Działanie **Get-AzureRmVm** akceptuje dwa parametry **nazwę maszyny wirtualnej** i **Nazwa grupy zasobów**. Ponieważ te parametry można wymaga różne wartości w każdym uruchomieniu elementu runbook, można dodać parametry wejściowe do elementu runbook. Poniżej przedstawiono kroki, aby dodać parametry wejściowe:

1. Wybierz graficzny element runbook z **Runbook** bloku, a następnie kliknij przycisk [ **Edytuj** ](automation-graphical-authoring-intro.md) go.
2. W edytorze elementów runbook, kliknij polecenie **dane wejściowe i wyjściowe** otworzyć **dane wejściowe i wyjściowe** bloku.
   
    ![Graficzny element runbook automatyzacji](media/automation-runbook-input-parameters/automation-02-graphical-runbok-editor.png)
3. **Dane wejściowe i wyjściowe** bloku zostanie wyświetlona lista parametrów wejściowych, które są zdefiniowane dla elementu runbook. W tym bloku należy dodać nowy parametr wejściowy lub Edytuj konfigurację istniejącego parametru wejściowego. Aby dodać nowego parametru dla elementu runbook, kliknij przycisk **dodać dane wejściowe** otworzyć **parametr wejściowy elementu Runbook** bloku. Istnieje można skonfigurować następujące parametry:
   
   | **Właściwość** | **Opis** |
   |:--- |:--- |
   | Name (Nazwa) |Wymagany. Nazwa parametru. To musi być unikatowe w obrębie elementu runbook i mogą zawierać tylko litery, cyfry lub znaki podkreślenia. Musi ona rozpoczynać się od litery. |
   | Opis |Opcjonalny. Opis przeznaczenia parametru wejściowego. |
   | Typ |Opcjonalny. Typ danych, który jest oczekiwany dla wartości parametru. Typy parametrów obsługiwanych są **ciąg**, **Int32**, **Int64**, **dziesiętną**, **logiczna**, **DateTime**, i **obiektu**. Jeśli typem danych nie jest zaznaczone, domyślnie **ciąg**. |
   | Obowiązkowy |Opcjonalny. Określa, czy należy podać wartość parametru. Jeśli wybierzesz **tak**, a następnie po uruchomieniu elementu runbook, należy podać wartość. Jeśli wybierzesz **nie**, a następnie wartość nie jest wymagana podczas uruchamiania elementu runbook i może zostać ustawiona wartość domyślna. |
   | Wartość domyślna |Opcjonalny. Określa wartość, która jest używana dla parametru, jeśli wartość nie zostanie przekazany, po uruchomieniu elementu runbook. Można ustawić wartości domyślnej dla parametru, który nie jest obowiązkowe. Aby ustawić wartość domyślną, wybierz **niestandardowy**. Ta wartość jest używana, chyba że inna wartość zostanie podana podczas uruchamiania elementu runbook. Wybierz **Brak** , jeśli nie chcesz podać wartości domyślnej. |
   
    ![Dodaj nowe dane wejściowe](media/automation-runbook-input-parameters/automation-runbook-input-parameter-new.png)
4. Utwórz dwa parametry o następujących właściwościach, które jest używane przez **Get AzureRmVm** działania:
   
   * **Parametr1:**
     
     * Nazwa - VMName
     * Typ — ciąg
     * Obowiązkowy - nr
   * **Parameter2:**
     
     * Nazwa - resourceGroupName
     * Typ — ciąg
     * Obowiązkowy - nr
     * Wartość domyślna — niestandardowy
     * Wartość domyślna niestandardowe — \<nazwę grupy zasobów, która zawiera maszyny wirtualnej >
5. Jeśli dodasz parametry, kliknij przycisk **OK**. Możesz teraz przeglądać w **danych wejściowych i wyjściowych bloku**. Kliknij przycisk **OK** ponownie, a następnie kliknij przycisk **zapisać** i **publikowania** elementu runbook.

## <a name="configure-input-parameters-in-python-runbooks"></a>Skonfiguruj parametry wejściowe w elementach runbook języka Python

W przeciwieństwie do programu PowerShell, przepływ pracy programu PowerShell i graficznych elementów runbook elementów runbook Python nie mają parametrów nazwanych.
Wszystkie parametry wejściowe są analizowane jako tablicę wartości argumentów.
Dostęp do tablicy przez zaimportowanie `sys` modułu do Twojego skryptu języka Python, a następnie użyć `sys.argv` tablicy.
Należy zauważyć, że pierwszy element tablicy, `sys.argv[0]`, to nazwa skryptu, dlatego pierwszy rzeczywisty parametr wejściowy jest `sys.argv[1]`.

Na przykład dotyczące używania parametrów wejściowych w elemencie runbook Python zobacz [Mój pierwszy Python runbook automatyzacji Azure](automation-first-runbook-textual-python2.md).

## <a name="assign-values-to-input-parameters-in-runbooks"></a>Przypisywanie wartości do parametrów w elementach runbook wejściowych

Można jednak przekazać wartości wprowadzanie parametrów w elementach runbook w następujących scenariuszach:

### <a name="start-a-runbook-and-assign-parameters"></a>Uruchom element runbook i przypisz parametrów

Element runbook może zostać uruchomiona na wiele sposobów: za pośrednictwem portalu Azure, z elementu webhook, polecenia cmdlet programu PowerShell, przy użyciu interfejsu API REST lub przy użyciu zestawu SDK. Poniżej omówiono różne metody uruchamiania elementu runbook i przypisywanie parametrów.

#### <a name="start-a-published-runbook-by-using-the-azure-portal-and-assign-parameters"></a>Uruchom opublikowanego elementu runbook za pomocą portalu Azure i parametry

Gdy użytkownik [uruchamiania elementu runbook](automation-starting-a-runbook.md#starting-a-runbook-with-the-azure-portal), **Uruchom element Runbook** zostanie otwarty blok i wprowadź wartości parametrów, które zostały utworzone.

![Rozpoczynanie korzystania z portalu](media/automation-runbook-input-parameters/automation-04-startrunbookusingportal.png)

Etykieta poniżej pola wejściowego zawiera atrybuty, które zostały ustawione dla parametru. Atrybuty obejmują obowiązkowe i opcjonalne, typ i wartość domyślną. W dymku pomocy obok nazwy parametru wyświetlane są wszystkie kluczowe dane potrzebne do podejmowania decyzji o wartości wejściowe parametru. Informacje te obejmują, czy parametr jest obowiązkowy lub opcjonalne. Zawiera również typ i wartość domyślną (jeśli istnieje), a inne przydatne informacje.

> [!NOTE]
> Obsługa parametrów typu String **pusty** wartości ciągu.  Wprowadzanie **[ciąg EmptyString]** w parametrze wejściowym pole przekazuje do parametru ciągiem pustym. Ponadto nie obsługują parametrów typu ciąg **Null** wartości były przekazywane. Jeśli wartości nie są przekazywane do parametru ciągu, następnie PowerShell zinterpretuje ją jako wartość null.
> 
> 

#### <a name="start-a-published-runbook-by-using-powershell-cmdlets-and-assign-parameters"></a>Uruchom opublikowanego elementu runbook za pomocą poleceń cmdlet programu PowerShell i parametry

* **Polecenia cmdlet Menedżera zasobów systemu Azure:** można uruchomić element runbook usługi Automatyzacja, który został utworzony w grupie zasobów za pomocą [Start AzureRmAutomationRunbook](https://msdn.microsoft.com/library/mt603661.aspx).
  
  **Przykład:**
  
  ```powershell
  $params = @{“VMName”=”WSVMClassic”;”resourceGroupeName”=”WSVMClassicSG”}
  
  Start-AzureRmAutomationRunbook -AutomationAccountName “TestAutomation” -Name “Get-AzureVMGraphical” –ResourceGroupName $resourceGroupName -Parameters $params
  ```
* **Polecenia cmdlet programu Azure wdrażania klasycznego modelu:** można uruchomić element runbook usługi Automatyzacja, który został utworzony w domyślnej grupie zasobów za pomocą [Start AzureAutomationRunbook](https://msdn.microsoft.com/library/dn690259.aspx).
  
  **Przykład:**
  
  ```powershell
  $params = @{“VMName”=”WSVMClassic”; ”ServiceName”=”WSVMClassicSG”}
  
  Start-AzureAutomationRunbook -AutomationAccountName “TestAutomation” -Name “Get-AzureVMGraphical” -Parameters $params
  ```

> [!NOTE]
> Po uruchomieniu elementu runbook za pomocą poleceń cmdlet programu PowerShell, domyślnego parametru, **MicrosoftApplicationManagementStartedBy** jest tworzony z wartością **PowerShell**. Można wyświetlić tego parametru w **szczegóły zadania** bloku.  
> 
> 

#### <a name="start-a-runbook-by-using-an-sdk-and-assign-parameters"></a>Uruchom element runbook przy użyciu zestawu SDK i przypisz parametrów

* **Metoda Menedżera zasobów Azure:** można uruchomić elementu runbook przy użyciu zestawu SDK języka programowania. Poniżej przedstawiono fragment kodu C# dla uruchamianie elementu runbook na Twoim koncie automatyzacji. Można wyświetlić wszystkie kodu w naszym [repozytorium GitHub](https://github.com/Azure/azure-sdk-for-net/blob/master/src/ResourceManagement/Automation/Automation.Tests/TestSupport/AutomationTestBase.cs).  
  
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
* **Metoda modelu wdrożenia usługi Azure classic:** można uruchomić elementu runbook przy użyciu zestawu SDK języka programowania. Poniżej przedstawiono fragment kodu C# dla uruchamianie elementu runbook na Twoim koncie automatyzacji. Można wyświetlić wszystkie kodu w naszym [repozytorium GitHub](https://github.com/Azure/azure-sdk-for-net/blob/master/src/ServiceManagement/Automation/Automation.Tests/TestSupport/AutomationTestBase.cs).
  
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
  
  Aby uruchomić tę metodę, utworzyć słownika do przechowywania parametrów elementu runbook **VMName** i **resourceGroupName**i ich wartości. Następnie można uruchomić elementu runbook. Poniżej znajduje się fragment kodu C# dla wywołania metody, który jest zdefiniowany powyżej.
  
  ```csharp
  IDictionary<string, string> RunbookParameters = new Dictionary<string, string>();
  
  // Add parameters to the dictionary.
  RunbookParameters.Add("VMName", "WSVMClassic");
  RunbookParameters.Add("resourceGroupName", "WSSC1");
  
  //Call the StartRunbook method with parameters
  StartRunbook(“Get-AzureVMGraphical”, RunbookParameters);
  ```

#### <a name="start-a-runbook-by-using-the-rest-api-and-assign-parameters"></a>Uruchom element runbook za pomocą interfejsu API REST i przypisz parametrów
Zadanie elementu runbook można tworzyć i wprowadzenie do interfejsu API REST usługi Automatyzacja Azure za pomocą **PUT** metody za pomocą następującego identyfikatora URI żądania:

    https://management.core.windows.net/<subscription-id>/cloudServices/<cloud-service-name>/resources/automation/~/automationAccounts/<automation-account-name>/jobs/<job-id>?api-version=2014-12-08`

W identyfikatorze URI żądania Zamień następujące parametry:

* **Identyfikator subskrypcji:** Twojego identyfikatora subskrypcji platformy Azure.  
* **Nazwa usługi chmury:** nazwę chmury usługi, do którego mają być wysyłane żądania.  
* **Automatyzacja account-name:** nazwa konta automatyzacji hostowanej w usłudze określonej chmury.  
* **Identyfikator zadania:** identyfikatora GUID dla zadania. Identyfikatory GUID w programie PowerShell można utworzyć przy użyciu **[GUID]::NewGuid(). ToString()** polecenia.

W celu przekazania parametrów do zadania elementu runbook, użyj treści żądania. Trwa następujące dwie właściwości podany w formacie JSON:

* **Nazwa elementu Runbook:** wymagane. Nazwa elementu runbook można uruchomić zadania.  
* **Parametry elementu Runbook:** opcjonalne. Słownik z listą parametrów w (nazwa, wartość) formatu, gdzie nazwa powinna być typu String, a wartość może być dowolną prawidłową wartość JSON.

Jeśli chcesz uruchomić **Get-AzureVMTextual** elementu runbook, który został utworzony wcześniej **VMName** i **resourceGroupName** jako parametry, użyj następującego formatu JSON w treści żądania.

   ```json
    {
      "properties":{
        "runbook":{
        "name":"Get-AzureVMTextual"},
      "parameters":{
         "VMName":"WSVMClassic",
         "resourceGroupName":”WSCS1”}
        }
    }
   ```

Kod stanu HTTP 201 jest zwracany, jeśli pomyślnie utworzono zadanie. Aby uzyskać więcej informacji o nagłówki odpowiedzi i treść odpowiedzi, zobacz artykuł o sposobie [utworzyć zadanie elementu runbook za pomocą interfejsu API REST.](https://msdn.microsoft.com/library/azure/mt163849.aspx)

### <a name="test-a-runbook-and-assign-parameters"></a>Testowanie elementu runbook i przypisz parametrów
Gdy możesz [przetestować wersję roboczą elementu runbook](automation-testing-runbook.md) przy użyciu opcji testu **Test** zostanie otwarta strona i można skonfigurować wartości parametrów, które zostały utworzone.

![Testowanie i parametry](media/automation-runbook-input-parameters/automation-06-testandassignparameters.png)

### <a name="link-a-schedule-to-a-runbook-and-assign-parameters"></a>Połącz harmonogram z elementem runbook i przypisz parametrów
Możesz [Połącz harmonogram](automation-schedules.md) do elementu runbook, aby element runbook zostanie uruchomiony w określonym czasie. Można przypisać parametry wejściowe podczas tworzenia harmonogramu, a element runbook używa tych wartości, gdy jest ona uruchamiana zgodnie z harmonogramem. Nie można zapisać harmonogram, zanim nie zostaną dostarczone wszystkie wartości parametrów obowiązkowych.

![Planowanie i przydzielanie parametrów](media/automation-runbook-input-parameters/automation-07-scheduleandassignparameters.png)

### <a name="create-a-webhook-for-a-runbook-and-assign-parameters"></a>Tworzenie elementu webhook dla elementu runbook i parametry
Można utworzyć [webhook](automation-webhooks.md) dla elementu runbook i skonfiguruj parametry wejściowe elementu runbook. Nie można zapisać elementu webhook, zanim nie zostaną dostarczone wszystkie wartości parametrów obowiązkowych.

![Utwórz element webhook i przypisz parametrów](media/automation-runbook-input-parameters/automation-08-createwebhookandassignparameters.png)

Podczas wykonywania elementu runbook przy użyciu elementu webhook wstępnie zdefiniowanych parametrów wejściowych **[Webhookdata](automation-webhooks.md#details-of-a-webhook)** jest wysyłany razem z zdefiniowanych parametrów wejściowych. Można kliknąć, aby rozwinąć **WebhookData** parametr, aby uzyskać więcej informacji.

![Parametr WebhookData](media/automation-runbook-input-parameters/automation-09-webhook-data-parameters.png)

## <a name="next-steps"></a>Następne kroki
* Aby uzyskać więcej informacji na runbook przychodzących i wychodzących, zobacz [usługi Automatyzacja Azure: element runbook danych wejściowych, wyjściowych i zagnieżdżonych elementów rrunbook](https://azure.microsoft.com/blog/azure-automation-runbook-input-output-and-nested-runbooks/).
* Aby uzyskać szczegółowe informacje o różnych sposobach uruchamiania elementu runbook, zobacz [uruchamianie elementu runbook](automation-starting-a-runbook.md).
* Aby edytować element runbook tekstową, zajrzyj do [edytowania elementów runbook tekstową](automation-edit-textual-runbook.md).
* Aby edytować graficznego elementu runbook, należy zapoznać się [tworzenia graficznego automatyzacji Azure](automation-graphical-authoring-intro.md).

