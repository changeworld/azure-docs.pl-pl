---
title: Tworzenie modułu integracji usługi Azure Automation
description: Samouczek, który przeprowadzi Cię przez proces tworzenia, testowania i przykładowego użycia modułów integracji w usłudze Azure Automation.
services: automation
ms.service: automation
ms.subservice: shared-capabilities
author: georgewallace
ms.author: gwallace
ms.date: 03/16/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 609a841ed410832739041bbbbf7d33d3a01a4bfc
ms.sourcegitcommit: 9999fe6e2400cf734f79e2edd6f96a8adf118d92
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/22/2019
ms.locfileid: "54436488"
---
# <a name="azure-automation-integration-modules"></a>Moduły integracji usługi Azure Automation
Program PowerShell to podstawowa technologia używana w usłudze Azure Automation. Ponieważ usługa Azure Automation jest zbudowana w oparciu o program PowerShell, moduły tego programu stanowią podstawę możliwości rozszerzania usługi Azure Automation. W tym artykule przeprowadzimy Cię przez szczegółowe informacje na temat użytkowania s usługi Azure Automation modułów programu PowerShell, nazywane modułami integracji i najlepsze rozwiązania dotyczące tworzenia własnych modułów programu PowerShell upewnij się, że działają jako moduły integracji w systemie Azure Usługi Automation. 

## <a name="what-is-a-powershell-module"></a>Co to jest moduł programu PowerShell?
Moduł to grupa poleceń cmdlet programu PowerShell, takich jak **Get-Date** lub **Copy-Item**, które mogą być używane w konsoli programu PowerShell, skryptów, przepływów pracy, elementów Runbook i zasobów konfiguracji DSC programu PowerShell, takich jak plik lub funkcja systemu Windows, których można używać z poziomu konfiguracji DSC programu PowerShell. Wszystkie funkcje programu PowerShell są dostępne za pośrednictwem poleceń cmdlet i zasobów DSC. Wszystkie polecenia cmdlet i zasoby DSC są wspierane przez moduł programu PowerShell (wiele takich modułów jest dołączanych do programu PowerShell). Na przykład polecenie cmdlet **Get-Date** jest częścią modułu Microsoft.PowerShell.Utility programu PowerShell, polecenie cmdlet **Copy-Item** jest częścią modułu Microsoft.PowerShell.Management, a zasób DSC pakietu jest częścią modułu PSDesiredStateConfiguration. Oba te moduły są dostarczane z programem PowerShell. Wiele modułów programu PowerShell nie jest dołączanych do tego programu. Są one rozpowszechniane razem z produktami firmy Microsoft lub firm zewnętrznych, takimi jak System Center 2012 Configuration Manager, albo przez dużą społeczność programu PowerShell w miejscach takich jak Galeria programu PowerShell. Moduły są przydatne, ponieważ w prostszy sposób wykonują złożone zadania za pomocą zhermetyzowanych funkcji.  Aby uzyskać więcej informacji, zobacz artykuł o [modułach programu PowerShell w witrynie MSDN](https://msdn.microsoft.com/library/dd878324%28v=vs.85%29.aspx). 

## <a name="what-is-an-azure-automation-integration-module"></a>Co to jest moduł integracji usługi Azure Automation?
Moduł integracji nie różni się od modułu programu PowerShell. Jest to po prostu moduł programu PowerShell opcjonalnie zawierający jeden dodatkowy plik — plik metadanych określający typ połączenia usługi Azure Automation do użycia z poleceniami cmdlet modułu w elementach Runbook. Bez względu na to, czy plik opcjonalny jest używany, te moduły programu PowerShell mogą być importowane do usługi Azure Automation w celu udostępnienia ich poleceń cmdlet do użycia w elementach Runbook, a zasobów DSC — do użycia w konfiguracjach DSC. Usługa Azure Automation przechowuje te moduły w tle, a w momencie wykonywania zadania elementu Runbook i zadania kompilacji DSC ładuje je do piaskownic usługi Azure Automation, w których są wykonywane elementy Runbook i kompilowane są konfiguracje DSC. Wszystkie zasoby DSC w modułach są również automatycznie umieszczane na serwerze ściągania konfiguracji DSC usługi Automation, dzięki czemu mogą być ściągane przez komputery próbujące zastosować konfiguracje DSC.  

Niektóre moduły programu Azure PowerShell są dostarczane z usługą Azure Automation jako gotowe do użytku. Dzięki temu można od razu rozpocząć automatyzację zarządzania platformą Azure. Można również importować moduły programu PowerShell niezależnie od systemu, usługi lub narzędzia do zintegrowania. 

> [!NOTE]
> Niektóre moduły są dostarczane jako moduły globalne w usłudze Automation. Moduły globalne są dostępne w przypadku tworzenia konta usługi automation i możemy je aktualizować czasami, który ich automatyczne Wypychanie do konta usługi automation. Jeśli t nie będą automatycznie aktualizowane, zawsze możesz zaimportować sam moduł samodzielnie i który ma pierwszeństwo przed wersją globalną modułu, która jest dostarczana w usłudze. 

Format, w którym można zaimportować pakiet modułu integracji, to skompresowany plik o takiej samej nazwie jak moduł, z rozszerzeniem ZIP. Zawiera on moduł programu Windows PowerShell oraz wszelkie pliki pomocnicze, w tym plik manifestu (psd1), jeśli istnieje dla modułu.

Jeśli moduł powinien zawierać typ połączenia usługi Azure Automation, musi również zawierać plik o nazwie `<ModuleName>-Automation.json`, który określa właściwości typu połączenia. Jest to plik json umieszczony w folderze modułu skompresowanego pliku zip i zawiera pola połączenia, który jest wymagany do nawiązania system lub usługę, z której reprezentowaną przez moduł. To kończy się tworzenie typu połączenia w usłudze Azure Automation. Przy użyciu tego pliku można ustawić nazwy i typy pól oraz określić, czy powinny one być szyfrowane lub opcjonalne dla danego typu połączenia modułu. Szablon w formacie pliku JSON wygląda następująco:

```json
{ 
   "ConnectionFields": [
   {
      "IsEncrypted":  false,
      "IsOptional":  false,
      "Name":  "ComputerName",
      "TypeName":  "System.String"
   },
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
   }],
   "ConnectionTypeName":  "DataProtectionManager",
   "IntegrationModuleName":  "DataProtectionManager"
}
```

Jeśli wdrożono Service Management Automation i utworzono pakiety modułów integracji dla elementów runbook automatyzacji, szablon powinien wyglądać znanym. 

## <a name="authoring-best-practices"></a>Najlepsze rozwiązania w zakresie tworzenia
Mimo że moduły integracji są zasadniczo modułami programu PowerShell, występują s nadal jest kilka rzeczy, firma Microsoft zaleca wziąć pod uwagę podczas tworzenia modułu programu PowerShell, aby zmaksymalizować jego użyteczność w usłudze Azure Automation. Niektóre z nich są usługi Azure Automation, a niektóre z nich są użyteczne po prostu ułatwiają współpracę modułów z przepływem pracy programu PowerShell, niezależnie od tego, czy możesz ponownie przy użyciu automatyzacji. 

1. Dodaj streszczenie, opis i identyfikator URI pomocy do każdego polecenia cmdlet w module. W programie PowerShell możesz zdefiniować pewne informacje pomocy dotyczące poleceń cmdlet, aby umożliwić użytkownikowi uzyskanie pomocy na temat korzystania z nich dzięki poleceniu cmdlet **Get-Help**. Na przykład tutaj s jak można zdefiniować streszczenie i URI pomocy dla modułu programu PowerShell zapisanego w pliku psm1.<br>  
   
    ```powershell
    <#
        .SYNOPSIS
         Gets all outgoing phone numbers for this Twilio account 
    #>
    function Get-TwilioPhoneNumbers {
    [CmdletBinding(DefaultParameterSetName='SpecifyConnectionFields', `
    HelpUri='https://www.twilio.com/docs/api/rest/outgoing-caller-ids')]
    param(
       [Parameter(ParameterSetName='SpecifyConnectionFields', Mandatory=$true)]
       [ValidateNotNullOrEmpty()]
       [string]
       $AccountSid,
   
       [Parameter(ParameterSetName='SpecifyConnectionFields', Mandatory=$true)]
       [ValidateNotNullOrEmpty()]
       [string]
       $AuthToken,
   
       [Parameter(ParameterSetName='UseConnectionObject', Mandatory=$true)]
       [ValidateNotNullOrEmpty()]
       [Hashtable]
       $Connection
    )
   
    $cred = CreateTwilioCredential -Connection $Connection -AccountSid $AccountSid -AuthToken $AuthToken
   
    $uri = "$TWILIO_BASE_URL/Accounts/" + $cred.UserName + "/IncomingPhoneNumbers"
   
    $response = Invoke-RestMethod -Method Get -Uri $uri -Credential $cred
   
    $response.TwilioResponse.IncomingPhoneNumbers.IncomingPhoneNumber
    }
    ```
   <br> Podanie tych informacji nie tylko umożliwi wyświetlenie pomocy przy użyciu polecenia cmdlet **Get-Help** w konsoli programu PowerShell, ale także udostępni tę funkcję pomocy w usłudze Azure Automation.  Może to mieć na przykład miejsce podczas wstawiania działań w procesie tworzenia elementu Runbook. Klikając pozycję Widok szczegółowy pomocy spowoduje otwarcie identyfikator URI pomocy na innej karcie przeglądarki sieci web możesz ponownie przy użyciu dostęp do usługi Azure Automation.<br>![Pomoc modułu integracji](media/automation-integration-modules/automation-integration-module-activitydesc.png)
1. Jeśli moduł działa w odniesieniu do systemu zdalnego,

    a. powinien zawierać plik metadanych modułu integracji, który definiuje informacje wymagane do nawiązania połączenia z systemem zdalnym, co oznacza typ połączenia;  
    b. w każdym poleceniu cmdlet w module użytkownik powinien móc zastosować obiekt połączenia (wystąpienie tego typu połączenia) jako parametr.  

    Poleceń cmdlet w module będzie można łatwiej używać w usłudze Azure Automation, jeśli zezwolisz na przekazywanie obiektu z polami typu połączenia jako parametru do polecenia cmdlet. Ten sposób użytkownicy don ma mapować parametrów elementu zawartości połączenia do odpowiednich parametrów polecenia cmdlet podczas każdego wywoływania polecenia cmdlet. W powyższym przykładzie elementu Runbook element zawartości połączenia usługi Twilio o nazwie CorpTwilio jest używany do uzyskiwania dostępu do usługi Twilio i zwraca wszystkie numery telefonów w ramach konta.  Zwróć uwagę na sposób mapowania pól połączenia do parametrów polecenia cmdlet.<br>
   
    ```powershell
    workflow Get-CorpTwilioPhones
    {
      $CorpTwilio = Get-AutomationConnection -Name 'CorpTwilio'
   
      Get-TwilioPhoneNumbers 
        -AccountSid $CorpTwilio.AccountSid  
        -AuthToken $CorptTwilio.AuthToken
    }
    ```
  
    Łatwiejsze i skuteczniejsze podejście do tej czynności to bezpośrednie przekazanie obiektu połączenia do polecenia cmdlet:
   
    ```powershell
    workflow Get-CorpTwilioPhones
    {
      $CorpTwilio = Get-AutomationConnection -Name 'CorpTwilio'
   
      Get-TwilioPhoneNumbers -Connection $CorpTwilio
    }
    ```
   
    Aby umożliwić takie zachowanie poleceń cmdlet, należy zezwolić im na akceptowanie obiektu połączenia bezpośrednio jako parametru, a nie tylko pól połączeń dla parametrów. Zazwyczaj chcesz ustawić parametr dla poszczególnych usług, dzięki czemu użytkownik nie używa usługi Azure Automation może wywoływać polecenia cmdlet bez tworzenia obiektu hashtable do działania jako obiekt połączenia. Poniższy zestaw parametrów **SpecifyConnectionFields** jest używany do przekazywania kolejnych właściwości pól połączeń. Parametr **UseConnectionObject** umożliwia bezpośrednie przekazanie połączenia. Jak widać, polecenie cmdlet Send-TwilioSMS w [module programu PowerShell dla usługi Twilio](https://gallery.technet.microsoft.com/scriptcenter/Twilio-PowerShell-Module-8a8bfef8) umożliwia przekazywanie w dowolny sposób: 
   
    ```powershell
    function Send-TwilioSMS {
      [CmdletBinding(DefaultParameterSetName='SpecifyConnectionFields', `
      HelpUri='https://www.twilio.com/docs/api/rest/sending-sms')]
      param(
         [Parameter(ParameterSetName='SpecifyConnectionFields', Mandatory=$true)]
         [ValidateNotNullOrEmpty()]
         [string]
         $AccountSid,
   
         [Parameter(ParameterSetName='SpecifyConnectionFields', Mandatory=$true)]
         [ValidateNotNullOrEmpty()]
         [string]
         $AuthToken,
   
         [Parameter(ParameterSetName='UseConnectionObject', Mandatory=$true)]
         [ValidateNotNullOrEmpty()]
         [Hashtable]
         $Connection
   
       )
    }
    ```
   <br>
1. Zdefiniuj typ danych wyjściowych dla wszystkich poleceń cmdlet w module. Zdefiniowanie typu danych wyjściowych polecenia cmdlet ułatwia określanie właściwości danych wyjściowych polecenia do użycia podczas tworzenia za pomocą funkcji IntelliSense czasu projektowania. Jest szczególnie przydatne podczas tworzenia graficznego elementu Runbook usługi Azure Automation, gdzie znajomość czasu projektowania jest kluczem do stworzenia prostego środowiska pracy użytkownika modułu.<br><br> ![Typ danych wyjściowych graficznego elementu Runbook](media/automation-integration-modules/runbook-graphical-module-output-type.png)<br> Jest to funkcja podobna do funkcji „wpisywania z wyprzedzeniem” danych wyjściowych polecenia cmdlet w środowisku PowerShell ISE bez konieczności jego uruchamiania.<br><br> ![Funkcja IntelliSense programu POSH](media/automation-integration-modules/automation-posh-ise-intellisense.png)<br>
1. Polecenia cmdlet w module nie powinny przyjmować złożonych typów obiektów jako parametrów. Przepływ pracy programu PowerShell różni się od programu PowerShell tym, że przechowuje typy złożone w formie zdeserializowanej. Typy pierwotne pozostają, ale typy złożone są konwertowane na wersje zdeserializowane, które zasadniczo są zbiorami właściwości. Jeśli na przykład w elemencie Runbook (lub przepływie pracy programu PowerShell) użyto polecenia cmdlet **Get-Process**, zwróci ono obiekt typu [Deserialized.System.Diagnostic.Process], a nie oczekiwany typ [System.Diagnostic.Process]. Ten typ ma te same właściwości co typ niezdeserializowany, ale nie zawiera żadnej z metod. A Jeśli spróbujesz przekazać tę wartość jako parametr do polecenia cmdlet, gdzie polecenie cmdlet oczekuje wartości [System.Diagnostic.Process] dla tego parametru, pojawi się następujący błąd: *Nie można przetworzyć przekształcenia argumentu dla parametru "process". Błąd: "Nie można przekonwertować wartości"System.Diagnostics.Process (CcmExec)"typu"Deserialized.System.Diagnostics.Process"na typ"System.Diagnostics.Process".*   Przyczyną tego jest niezgodność między oczekiwanym typem [System.Diagnostic.Process] i danym typem [Deserialized.System.Diagnostic.Process]. Aby rozwiązać ten problem, upewnij się, że polecenia cmdlet modułu nie przyjmują złożonych typów jako parametrów. Oto nieprawidłowy sposób wykonania tego zadania.
   
    ```powershell
    function Get-ProcessDescription {
      param (
            [System.Diagnostic.Process] $process
      )
      $process.Description
    }
    ``` 
    <br>
    A oto odpowiednia metoda — przyjęcie elementu podstawowego, który może być wewnętrznie używany przez polecenie cmdlet do pobierania i używania obiektu złożonego. Ponieważ polecenia cmdlet są wykonywane w kontekście programu PowerShell, a nie przepływu pracy programu PowerShell, wewnątrz polecenia cmdlet element $process staje się poprawnym typem [System.Diagnostic.Process].  
   
    ```powershell
    function Get-ProcessDescription {
      param (
            [String] $processName
      )
      $process = Get-Process -Name $processName
   
      $process.Description
    }
    ```
   <br>
   Trwałe połączenia w elementach runbook to tabele skrótów typu złożonego, a jeszcze tych obiektach HashTable wydaje się, że można je przekazywać do poleceń cmdlet ich parametru Connection idealnie, bez wyjątku rzutowania. Technicznie rzecz biorąc, niektóre typy programu PowerShell można prawidłowo rzutować z postaci serializowanej do postaci zdeserializowanej i dlatego można je przekazywać do poleceń cmdlet jako parametry akceptujące typy niezdeserializowane. Przykładem jest tablica skrótów. Jego s dla autora modułu s możliwe zdefiniowanych typów, które mają zostać zastosowane w taki sposób, że są one również prawidłowo zdeserializowane, ale istnieją pewne kompromisów należy wziąć pod uwagę. Typ musi zawierać konstruktor domyślny i element PSTypeConverter, a wszystkie jego właściwości muszą być publiczne. Jednak dla typów już zdefiniowanych, które nie jest właścicielem przez autora modułu, nie ma możliwości Aby to naprawić, dlatego zaleca się unikanie typów złożonych jako parametrów. Porada tworzenia elementu Runbook: Jeśli z dowolnej przyczyny polecenia cmdlet muszą przyjmować parametr typu złożonego, lub używasz ktoś inny parametr typu modułu s, która wymaga złożony, obejściem w elementach runbook przepływu pracy programu PowerShell i przepływów pracy programu PowerShell w lokalnym programie PowerShell jest opakowanie polecenia cmdlet które generuje typ złożony i polecenia cmdlet, które wykorzystuje typ złożony, w ramach tego samego działania InlineScript. Ponieważ skrypt InlineScript wykonuje zawartość jako program PowerShell, a nie przepływ pracy programu PowerShell, wygenerowanie przez polecenie cmdlet typu złożonego spowoduje utworzenie prawidłowego typu, a nie zdeserializowanego typu złożonego.
1. Utwórz wszystkie polecenia cmdlet w module jako bezstanowe. Przepływ pracy programu PowerShell powoduje uruchomienie każdego polecenia cmdlet wywoływanego w przepływie pracy w ramach innej sesji. Oznacza to, że polecenia cmdlet, które zależą od stanu sesji utworzone/zmodyfikowane przez inne polecenia cmdlet w tym samym module, nie będą działać w elementach Runbook przepływu pracy programu PowerShell.  Oto przykład czynności, których nie należy wykonywać.
   
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
   <br>
1. Moduł powinien być w pełni zawarty w pakiecie z obsługą opcji Xcopy. Ponieważ moduły usługi Azure Automation są dystrybuowane do piaskownic usługi Automation, jeśli trzeba wykonać elementy Runbook, muszą one działać niezależnie od hosta, na którym są uruchamiane. Oznacza to, że powinno być możliwe do pliku Zip paczkę modułu, przenieś go do innego hosta, z tą samą lub nowszą wersją programu PowerShell i ma działać normalnie po zaimportowaniu do środowiska PowerShell tego hosta. Aby było to możliwe, moduł nie powinien być zależny od żadnych plików poza folderem modułu (folderem pakowanym podczas importowania do usługi Azure Automation) ani od żadnych ustawień rejestru unikatowych na hoście, na przykład określonych podczas instalowania produktu. Jeśli to rozwiązanie nie zostanie zastosowane, użycie modułu w usłudze Azure Automation będzie niemożliwe.  

## <a name="next-steps"></a>Kolejne kroki

* Aby rozpocząć pracę z elementami Runbook przepływu pracy programu PowerShell, zobacz artykuł [My first PowerShell workflow runbook](automation-first-runbook-textual.md) (Mój pierwszy element Runbook przepływu pracy programu PowerShell).
* Aby dowiedzieć się więcej na temat tworzenia modułów programu PowerShell, zobacz artykuł [Writing a Windows PowerShell Module](https://msdn.microsoft.com/library/dd878310%28v=vs.85%29.aspx) (Pisanie modułu programu Windows PowerShell).


