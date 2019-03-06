---
title: Tworzenie modułu integracji usługi Azure Automation
description: Samouczek, który przeprowadzi Cię przez proces tworzenia, testowania i przykładowego użycia modułów integracji w usłudze Azure Automation.
services: automation
ms.service: automation
ms.subservice: shared-capabilities
author: georgewallace
ms.author: gwallace
ms.date: 01/24/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 2800385581ea3f08fc1013e980f8c133a2f7241e
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/06/2019
ms.locfileid: "57443736"
---
# <a name="azure-automation-integration-modules"></a>Moduły integracji usługi Azure Automation

Program PowerShell to podstawowa technologia używana w usłudze Azure Automation. Ponieważ usługa Azure Automation jest zbudowana w oparciu o program PowerShell, moduły tego programu stanowią podstawę możliwości rozszerzania usługi Azure Automation. W tym artykule dowiesz się, szczegółowe informacje na temat użycia usługi Azure Automation modułów programu PowerShell, nazywane modułami integracji. Poznasz również zalecanych rozwiązań dotyczących tworzenia własnych modułów programu PowerShell, aby upewnić się, że działają jako moduły integracji w usłudze Azure Automation. 

## <a name="what-is-a-powershell-module"></a>Co to jest moduł programu PowerShell?

Moduł programu PowerShell jest grupą poleceń cmdlet programu PowerShell, takie jak **Get-Date** lub **Copy-Item**, które mogą być używane z:

* Konsoli programu PowerShell
* fragmentów
* Przepływy pracy
* Elementy Runbook
* Zasoby DSC

Wszystkie funkcje programu PowerShell jest dostępna za pośrednictwem poleceń cmdlet i zasobów DSC. Każdego polecenia cmdlet i zasobów DSC jest wspierana przez moduł programu PowerShell wiele jest dołączanych do programu PowerShell. Na przykład **Get-Date** polecenia cmdlet jest częścią `Microsoft.PowerShell.Utility` moduł programu PowerShell, a **Copy-Item** polecenia cmdlet jest częścią `Microsoft.PowerShell.Management` modułu programu PowerShell i zasób DSC pakietu jest częścią Modułu Psdesiredstateconfiguration. Oba te moduły są dostarczane z programem PowerShell. Wiele modułów programu PowerShell nie są dostarczane jako część programu PowerShell. Te moduły są dystrybuowane za pomocą produktów pierwsze lub innych firm lub w miejscach takich galerii programu PowerShell. Moduły są przydatne, ponieważ w prostszy sposób wykonują złożone zadania za pomocą zhermetyzowanych funkcji.  Aby uzyskać więcej informacji, zobacz artykuł o [modułach programu PowerShell w witrynie MSDN](https://msdn.microsoft.com/library/dd878324%28v=vs.85%29.aspx). 

## <a name="what-is-an-azure-automation-integration-module"></a>Co to jest moduł integracji usługi Azure Automation?

Moduł integracji nie różni się od modułu programu PowerShell. Jest to po prostu moduł programu PowerShell opcjonalnie zawierający jeden dodatkowy plik — plik metadanych określający typ połączenia usługi Azure Automation do użycia z poleceniami cmdlet modułu w elementach Runbook. Moduły programu PowerShell można zaimportować do usługi Azure Automation w celu udostępnienia ich poleceń cmdlet do użycia w elementach runbook i zasobów DSC dostępne do użycia w konfiguracjach DSC. Za kulisami usługi Azure Automation przechowuje te moduły, a zadanie elementu runbook i czasu wykonywania zadania kompilacji DSC ładuje je do piaskownic usługi Azure Automation, w którym wykonywanie elementów runbook i kompilowanie konfiguracji DSC. Wszystkie zasoby DSC w modułach są również automatycznie umieszczane na serwerze ściągania usługi Automation DSC. One mogą być ściągane przez komputery, gdy mają one zastosowanie konfiguracji DSC.  

Firma Microsoft niektóre są dostarczane liczbę modułów programu Azure PowerShell w usłudze Azure Automation. Ale można również importować moduły programu PowerShell niezależnie od systemu, usługi lub narzędzia, którą chcesz zintegrować z.

> [!NOTE]
> Niektóre moduły są dostarczane jako **moduły globalne** w usłudze Automation. Moduły globalne są dostępne w przypadku tworzenia konta usługi automation i możemy je aktualizować czasami, który ich automatyczne Wypychanie do konta usługi automation. Jeśli nie mają być automatycznie aktualizowane, zawsze możesz zaimportować sam moduł samodzielnie i który ma pierwszeństwo przed wersją globalną modułu, która jest dostarczana w usłudze.

Format, w którym można zaimportować pakiet modułu integracji, to skompresowany plik o takiej samej nazwie jak moduł, z rozszerzeniem ZIP. Zawiera on moduł programu Windows PowerShell oraz wszelkie pliki pomocnicze, w tym plik manifestu (psd1), jeśli istnieje dla modułu.

Jeśli moduł powinien zawierać typ połączenia usługi Azure Automation, musi również zawierać plik o nazwie `<ModuleName>-Automation.json`, który określa właściwości typu połączenia. Jest to plik json, który jest umieszczony w folderze modułu skompresowanego pliku zip. Ten plik zawiera pola **połączenia** co jest wymagane, aby nawiązać połączenie z systemem lub usługą reprezentowaną przez moduł. Ta konfiguracja kończy się tworzenie typu połączenia w usłudze Azure Automation. Przy użyciu tego pliku można ustawić nazwy i typy pól oraz określić, czy powinny one być szyfrowane lub opcjonalne dla danego typu połączenia modułu. Poniższy przykład przedstawia szablon w formacie json:

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

Jeśli wdrożono Service Management Automation i utworzono pakiety modułów integracji dla elementów runbook usługi automation, tego szablonu powinna wyglądać znajomo dla Ciebie.

## <a name="authoring-best-practices"></a>Najlepsze rozwiązania w zakresie tworzenia

Mimo że moduły integracji są moduły programu PowerShell, się nadal istnieje kilka kwestii, które warto wziąć pod uwagę podczas tworzenia modułu programu PowerShell do użycia w usłudze Azure Automation. Niektóre zalecenia są przydatne do upewnij współpracę modułów z przepływem pracy programu PowerShell.

1. Dodaj streszczenie, opis i identyfikator URI pomocy do każdego polecenia cmdlet w module. W programie PowerShell możesz zdefiniować pewne informacje pomocy dotyczące poleceń cmdlet, aby umożliwić użytkownikowi uzyskanie pomocy na temat korzystania z nich dzięki poleceniu cmdlet **Get-Help**. Na przykład Oto jak można zdefiniować streszczenie i identyfikator URI pomocy dla modułu programu PowerShell zapisanego w pliku psm1. 
   
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

   Podanie tych informacji pokazuje pomocy przy użyciu **Get-Help** polecenia cmdlet w konsoli programu PowerShell. Te informacje jest również udostępniany w usłudze Azure Automation.  Może to mieć na przykład miejsce podczas wstawiania działań w procesie tworzenia elementu Runbook. Klikając pozycję "Wyświetl szczegółową pomoc" Otwórz identyfikator URI pomocy na innej karcie przeglądarki sieci web, używane do dostępu do usługi Azure Automation.

   ![Pomoc modułu integracji](media/automation-integration-modules/automation-integration-module-activitydesc.png)

2. Jeśli moduł działa w odniesieniu do systemu zdalnego:

   1. powinien zawierać plik metadanych modułu integracji, który definiuje informacje wymagane do nawiązania połączenia z systemem zdalnym, co oznacza typ połączenia;
   2. w każdym poleceniu cmdlet w module użytkownik powinien móc zastosować obiekt połączenia (wystąpienie tego typu połączenia) jako parametr.  

    Poleceń cmdlet w module będzie można łatwiej używać w usłudze Azure Automation, jeśli zezwolisz na przekazywanie obiektu z polami typu połączenia jako parametru do polecenia cmdlet. Dzięki temu użytkownicy mogą mapować parametrów elementu zawartości połączenia do odpowiednich parametrów polecenia cmdlet podczas każdego wywoływania polecenia cmdlet.

    W powyższym przykładzie elementu Runbook element zawartości połączenia usługi Twilio o nazwie CorpTwilio jest używany do uzyskiwania dostępu do usługi Twilio i zwraca wszystkie numery telefonów w ramach konta.  Zwróć uwagę, sposób mapowania pól połączenia do parametrów polecenia cmdlet?

    ```powershell
    workflow Get-CorpTwilioPhones
    {
      $CorpTwilio = Get-AutomationConnection -Name 'CorpTwilio'

      Get-TwilioPhoneNumbers 
        -AccountSid $CorpTwilio.AccountSid  
        -AuthToken $CorptTwilio.AuthToken
    }
    ```
  
    Łatwiejsze i skuteczniejsze podejście do to zachowanie jest bezpośrednie przekazanie obiektu połączenia do polecenia cmdlet:

    ```powershell
    workflow Get-CorpTwilioPhones
    {
      $CorpTwilio = Get-AutomationConnection -Name 'CorpTwilio'

      Get-TwilioPhoneNumbers -Connection $CorpTwilio
    }
    ```

    Aby umożliwić zachowanie podobnie jak w poprzednim przykładzie poleceń cmdlet, należy zezwolić im na akceptowanie obiektu połączenia bezpośrednio jako parametru, a nie tylko pól połączeń dla parametrów. Zazwyczaj chcesz ustawić parametr dla poszczególnych usług, dzięki czemu użytkownik nie używa usługi Azure Automation może wywoływać polecenia cmdlet bez tworzenia obiektu hashtable do działania jako obiekt połączenia. Zestaw parametrów **SpecifyConnectionFields**, używany do przekazywania połączenia właściwości pola jedno po drugim. Parametr **UseConnectionObject** umożliwia bezpośrednie przekazanie połączenia. Jak widać, polecenie cmdlet Send-TwilioSMS w [module programu PowerShell dla usługi Twilio](https://gallery.technet.microsoft.com/scriptcenter/Twilio-PowerShell-Module-8a8bfef8) umożliwia przekazywanie w dowolny sposób:

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

3. Zdefiniuj typ danych wyjściowych dla wszystkich poleceń cmdlet w module. Zdefiniowanie typu danych wyjściowych polecenia cmdlet ułatwia określanie właściwości danych wyjściowych polecenia do użycia podczas tworzenia za pomocą funkcji IntelliSense czasu projektowania. Jest to szczególnie przydatne podczas automatyzacji tworzenia graficznego elementu runbook, gdzie znajomość czasu projektowania jest kluczem do prostego środowiska pracy użytkownika modułu.

   ![Typ danych wyjściowych graficznego elementu Runbook](media/automation-integration-modules/runbook-graphical-module-output-type.png)

   To zachowanie jest podobne do funkcji "wpisywania z wyprzedzeniem" danych wyjściowych polecenia cmdlet środowiska PowerShell ISE bez konieczności jego uruchamiania.

   ![Funkcja IntelliSense programu POSH](media/automation-integration-modules/automation-posh-ise-intellisense.png)

4. Polecenia cmdlet w module nie powinny przyjmować złożonych typów obiektów dla parametrów. Przepływ pracy programu PowerShell różni się od programu PowerShell tym, że przechowuje typy złożone w formie zdeserializowanej. Typy pierwotne pozostają, ale typy złożone Konwertuj na wersje zdeserializowane, które zasadniczo są zbiorami właściwości. Jeśli na przykład w elemencie Runbook (lub przepływie pracy programu PowerShell) użyto polecenia cmdlet **Get-Process**, zwróci ono obiekt typu [Deserialized.System.Diagnostic.Process], a nie oczekiwany typ [System.Diagnostic.Process]. Ten typ ma te same właściwości co typ niezdeserializowany, ale nie zawiera żadnej z metod. A Jeśli spróbujesz przekazać tę wartość jako parametr do polecenia cmdlet, gdzie polecenie cmdlet oczekuje wartości [System.Diagnostic.Process] dla tego parametru, pojawi się następujący błąd: *Nie można przetworzyć przekształcenia argumentu dla parametru "process". Błąd: "Nie można przekonwertować wartości"System.Diagnostics.Process (CcmExec)"typu"Deserialized.System.Diagnostics.Process"na typ"System.Diagnostics.Process".*   Jest tak, ponieważ występuje niezgodność między oczekiwanym typem [System.Diagnostic.Process] i danym typem [Deserialized.System.Diagnostic.Process]. Aby rozwiązać ten problem jest upewnij się, że polecenia cmdlet modułu nie przyjmują złożonych typów jako parametrów. Oto nieprawidłowy sposób wykonania tego zadania.

    ```powershell
    function Get-ProcessDescription {
      param (
            [System.Diagnostic.Process] $process
      )
      $process.Description
    }
    ```

    Prawidłowy sposób jest podjęcie podstawowego, która jest używana wewnętrznie przez polecenie cmdlet do pobierania obiektu złożonego i używania. Ponieważ polecenia cmdlet są wykonywane w kontekście programu PowerShell, a nie przepływu pracy programu PowerShell, wewnątrz polecenia cmdlet element $process staje się poprawnym typem [System.Diagnostic.Process].  

    ```powershell
    function Get-ProcessDescription {
      param (
            [String] $processName
      )
      $process = Get-Process -Name $processName
   
      $process.Description
    }
    ```

   Trwałe połączenia w elementach runbook to tabele skrótów typu złożonego, a jeszcze tych obiektach HashTable wydaje się być mogą być przekazywane do polecenia cmdlet dotyczące ich "parametru Connection idealnie, za pomocą żaden wyjątek rzutowania. Technicznie rzecz biorąc, niektóre typy programu PowerShell można prawidłowo rzutować z postaci serializowanej do postaci zdeserializowanej i dlatego można je przekazywać do poleceń cmdlet jako parametry akceptujące typy niezdeserializowane. Przykładem jest tablica skrótów. Jest możliwe dla typów zdefiniowanych przez autora modułu do implementacji w taki sposób, że są one również prawidłowo zdeserializowane, ale istnieją pewne kompromisów należy wziąć pod uwagę. Typ musi zawierać konstruktor domyślny i element PSTypeConverter, a wszystkie jego właściwości muszą być publiczne. Jednak dla typów już zdefiniowanych, które nie jest właścicielem przez autora modułu, nie ma możliwości do "Napraw", dlatego zaleca się unikanie typów złożonych jako parametrów. Porada tworzenia elementu Runbook: Jeśli poleceń cmdlet muszą przyjmować parametr typu złożonego, obejściem w przepływach pracy programu PowerShell, jest zawijany polecenia cmdlet, które generuje typ złożony i polecenia cmdlet, które wykorzystuje typ złożony, w ramach tego samego działania InlineScript. Ponieważ skrypt InlineScript wykonuje zawartość jako program PowerShell, a nie przepływ pracy programu PowerShell, wygenerowanie przez polecenie cmdlet typu złożonego spowoduje utworzenie prawidłowego typu, a nie zdeserializowanego typu złożonego.

5. Utwórz wszystkie polecenia cmdlet w module jako bezstanowe. Przepływ pracy programu PowerShell powoduje uruchomienie każdego polecenia cmdlet wywoływanego w przepływie pracy w ramach innej sesji. Oznacza to, że polecenia cmdlet, które są zależne od stanu sesji utworzone / zmodyfikowane przez inne polecenia cmdlet w tym samym module nie będzie działać w elementach runbook przepływu pracy programu PowerShell.  Oto przykład czynności, których nie należy wykonywać.
   
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

6. Moduł powinien być w pełni zawarty w pakiecie z obsługą opcji Xcopy. Moduły platformy Azure Automation są dystrybuowane do piaskownic usługi Automation, gdy trzeba wykonać elementy runbook. Moduły muszą działać niezależnie od hosta, na których są one uruchamiane w. Powinno być możliwe do pliku Zip przenoszenie pakietu modułu i powinien działać w zwykły sposób po zaimportowaniu do środowiska PowerShell innego hosta. Aby było to możliwe moduł nie powinna zależeć od żadnych plików poza folderem modułu. Ten folder jest folderem, pakowanym podczas importowania modułu do usługi Azure Automation. Moduł powinien również zależy od żadnych ustawień rejestru unikatowych na hoście, takie jak te ustawienia, gdy produkt jest zainstalowany. Jeśli na tym najlepszym rozwiązaniem, nie będzie przestrzegana, moduł nie będzie można używać w usłudze Azure Automation.  

7. Jeśli odwołanie do [modułów programu Azure Powershell Az](/powershell/azure/new-azureps-module-az?view=azps-1.1.0) w module, upewnij się, nie są również odwołania do `AzureRM`. `Az` Module nie można używać w połączeniu z `AzureRM` modułów. `Az` jest obsługiwane w elementach runbook, ale nie są importowane domyślnie. Aby dowiedzieć się więcej na temat `Az` modułów i zagadnienia, weź pod uwagę, zobacz [Az Obsługa modułu w usłudze Azure Automation](az-modules.md).

## <a name="next-steps"></a>Kolejne kroki

* Aby rozpocząć pracę z elementami Runbook przepływu pracy programu PowerShell, zobacz artykuł [My first PowerShell workflow runbook](automation-first-runbook-textual.md) (Mój pierwszy element Runbook przepływu pracy programu PowerShell).
* Aby dowiedzieć się więcej na temat tworzenia modułów programu PowerShell, zobacz artykuł [Writing a Windows PowerShell Module](https://msdn.microsoft.com/library/dd878310%28v=vs.85%29.aspx) (Pisanie modułu programu Windows PowerShell).
