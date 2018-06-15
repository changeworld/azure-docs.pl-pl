---
title: Typowe zadania uruchamiania usługi w chmurze | Dokumentacja firmy Microsoft
description: Niektóre przykłady typowych zadań uruchomienia, które można wykonywać w roli sieci web usługi w chmurze lub roli proces roboczy.
services: cloud-services
documentationcenter: ''
author: Thraka
manager: timlt
editor: ''
ms.assetid: a7095dad-1ee7-4141-bc6a-ef19a6e570f1
ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/18/2017
ms.author: adegeo
ms.openlocfilehash: cee23da5b089b02bfc0ef10afd60f0f2272585b1
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/11/2017
ms.locfileid: "23843700"
---
# <a name="common-cloud-service-startup-tasks"></a>Typowe zadania uruchamiania usługi w chmurze
Ten artykuł zawiera kilka przykładów typowych zadań uruchomienia, które można wykonywać w usłudze w chmurze. Zadania uruchamiania służy do wykonywania operacji przed rozpoczęciem roli. Operacje, które można wykonać to instalowania składnika, rejestrowanie składników modelu COM, ustawienie kluczy rejestru lub uruchamia proces wymagającą dużo czasu. 

Zobacz [w tym artykule](cloud-services-startup-tasks.md) zrozumieć sposób działania uruchamiania zadań, a w szczególności, jak tworzyć wpisów, które definiują zadanie uruchamiania.

> [!NOTE]
> Uruchamianie zadania nie są stosowane do maszyn wirtualnych, tylko do sieci Web usługi chmury i roli proces roboczy.
> 

## <a name="define-environment-variables-before-a-role-starts"></a>Zdefiniuj zmienne środowiskowe, przed rozpoczęciem roli
Zmienne środowiskowe zdefiniowane dla określonego zadania, należy użyć [środowiska] element wewnątrz [zadań] elementu.

```xml
<ServiceDefinition name="MyService" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition">
    <WorkerRole name="WorkerRole1">
        ...
        <Startup>
            <Task commandLine="Startup.cmd" executionContext="limited" taskType="simple">
                <Environment>
                    <Variable name="MyEnvironmentVariable" value="MyVariableValue" />
                </Environment>
            </Task>
        </Startup>
    </WorkerRole>
</ServiceDefinition>
```

Zmienne można również użyć [prawidłową wartość wyrażenia XPath Azure](cloud-services-role-config-xpath.md) do odwołania elementu dotyczących wdrożenia. Zamiast `value` atrybutu, zdefiniuj [RoleInstanceValue] element podrzędny.

```xml
<Variable name="PathToStartupStorage">
    <RoleInstanceValue xpath="/RoleEnvironment/CurrentInstance/LocalResources/LocalResource[@name='StartupLocalStorage']/@path" />
</Variable>
```


## <a name="configure-iis-startup-with-appcmdexe"></a>Konfigurowanie uruchomienia usług IIS przy użyciu AppCmd.exe
[AppCmd.exe](https://technet.microsoft.com/library/jj635852.aspx) narzędzia wiersza polecenia może służyć do zarządzania ustawieniami usług IIS podczas uruchamiania na platformie Azure. *AppCmd.exe* umożliwia wygodne, wiersza polecenia dostęp do ustawień konfiguracji do użycia w zadania uruchamiania na platformie Azure. Przy użyciu *AppCmd.exe*, ustawienia witryny sieci Web mogą być dodane, zmodyfikowane lub usunięte dla witryn i aplikacji.

Istnieje jednak kilka istotnych Zwróć uwagę na użycie *AppCmd.exe* jako zadanie uruchamiania:

* Uruchamianie zadania mogą być uruchamiane w więcej niż raz między ponownego uruchomienia. Na przykład, gdy rola odtwarzana.
* Jeśli *AppCmd.exe* akcja jest wykonywana więcej niż raz, może generować wystąpił błąd. Na przykład próba dodania sekcji, aby *Web.config* dwukrotnie można wygenerować wystąpił błąd.
* Uruchamianie zadań zakończy się niepowodzeniem, jeśli zwracają kod wyjścia równy zero lub **errorlevel**. Na przykład, jeśli *AppCmd.exe* generuje błąd.

Dobrym rozwiązaniem do sprawdzenia jest **errorlevel** po wywołaniu *AppCmd.exe*, który jest łatwo zrobić, jeśli zawijanie wywołanie *AppCmd.exe* z *cmd* pliku. W przypadku wykrycia znanego **errorlevel** odpowiedzi, możesz go zignorować lub przekaż go ponownie.

Errorlevel zwrócony przez *AppCmd.exe* są wymienione w pliku winerror.h pliku, a także będą widoczne na [MSDN](https://msdn.microsoft.com/library/windows/desktop/ms681382.aspx).

### <a name="example-of-managing-the-error-level"></a>Przykład zarządzanie poziom błędu
W tym przykładzie dodaje sekcji kompresji i wpis kompresji dla formatu JSON do *Web.config* pliku z powodu błędu obsługi i rejestrowania.

Istotne sekcje [ServiceDefinition.csdef] plików są wyświetlane w tym miejscu, które obejmują ustawienia [executionContext](https://msdn.microsoft.com/library/azure/gg557552.aspx#Task) atrybutu `elevated` umożliwiają *AppCmd.exe* wystarczających uprawnień do zmiany ustawień w *Web.config* pliku:

```xml
<ServiceDefinition name="MyService" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition">
    <WorkerRole name="WorkerRole1">
        ...
        <Startup>
            <Task commandLine="Startup.cmd" executionContext="elevated" taskType="simple" />
        </Startup>
    </WorkerRole>
</ServiceDefinition>
```

*Startup.cmd* partii używa pliku *AppCmd.exe* można dodać sekcję kompresji i wpis kompresji dla formatu JSON do *Web.config* pliku. Oczekiwana **errorlevel** 183 jest ustawiony na wartość zero korzystanie z VERIFY. Wywołanie pliku EXE programu wiersza polecenia. Nieoczekiwany errorlevels StartupErrorLog.txt są rejestrowane.

```cmd
REM   *** Add a compression section to the Web.config file. ***
%windir%\system32\inetsrv\appcmd set config /section:urlCompression /doDynamicCompression:True /commit:apphost >> "%TEMP%\StartupLog.txt" 2>&1

REM   ERRORLEVEL 183 occurs when trying to add a section that already exists. This error is expected if this
REM   batch file were executed twice. This can occur and must be accounted for in a Azure startup
REM   task. To handle this situation, set the ERRORLEVEL to zero by using the Verify command. The Verify
REM   command will safely set the ERRORLEVEL to zero.
IF %ERRORLEVEL% EQU 183 DO VERIFY > NUL

REM   If the ERRORLEVEL is not zero at this point, some other error occurred.
IF %ERRORLEVEL% NEQ 0 (
    ECHO Error adding a compression section to the Web.config file. >> "%TEMP%\StartupLog.txt" 2>&1
    GOTO ErrorExit
)

REM   *** Add compression for json. ***
%windir%\system32\inetsrv\appcmd set config  -section:system.webServer/httpCompression /+"dynamicTypes.[mimeType='application/json; charset=utf-8',enabled='True']" /commit:apphost >> "%TEMP%\StartupLog.txt" 2>&1
IF %ERRORLEVEL% EQU 183 VERIFY > NUL
IF %ERRORLEVEL% NEQ 0 (
    ECHO Error adding the JSON compression type to the Web.config file. >> "%TEMP%\StartupLog.txt" 2>&1
    GOTO ErrorExit
)

REM   *** Exit batch file. ***
EXIT /b 0

REM   *** Log error and exit ***
:ErrorExit
REM   Report the date, time, and ERRORLEVEL of the error.
DATE /T >> "%TEMP%\StartupLog.txt" 2>&1
TIME /T >> "%TEMP%\StartupLog.txt" 2>&1
ECHO An error occurred during startup. ERRORLEVEL = %ERRORLEVEL% >> "%TEMP%\StartupLog.txt" 2>&1
EXIT %ERRORLEVEL%
```

## <a name="add-firewall-rules"></a>Dodawanie reguł zapory
Na platformie Azure czy skutecznie dwóch zapory. Pierwszy zapory steruje połączeń między maszyną wirtualną a publicznie. Zapora jest kontrolowany przez [punkty końcowe] element [ServiceDefinition.csdef] pliku.

Drugi zapory steruje połączeń między maszyną wirtualną i procesów w tej maszynie wirtualnej. Zapora może być kontrolowane przez `netsh advfirewall firewall` narzędzia wiersza polecenia.

Platforma Azure tworzy reguły zapory dla procesów uruchomiona w ramach poszczególnych ról. Na przykład po uruchomieniu usługi lub program Azure automatycznie tworzy reguły zapory niezbędne, aby zezwolić tej usługi do komunikacji z Internetem. Jednak w przypadku utworzenia usługę, która jest uruchamiana przez proces poza roli użytkownika (np. usługi COM + lub zaplanowanych zadań systemu Windows), należy ręcznie utworzyć regułę zapory, aby zezwolić na dostęp do tej usługi. Te reguły zapory można tworzyć za pomocą zadania uruchamiania.

Zadanie uruchamiania, która tworzy regułę zapory muszą mieć [executionContext][zadań] z **z podwyższonym poziomem uprawnień**. Dodaj następujące zadanie uruchamiania do [ServiceDefinition.csdef] pliku.

```xml
<ServiceDefinition name="MyService" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition">
    <WorkerRole name="WorkerRole1">
        ...
        <Startup>
            <Task commandLine="AddFirewallRules.cmd" executionContext="elevated" taskType="simple" />
        </Startup>
    </WorkerRole>
</ServiceDefinition>
```

Aby dodać regułę zapory, należy użyć odpowiedniej `netsh advfirewall firewall` poleceń w pliku wsadowym uruchamiania. W tym przykładzie zadanie uruchamiania wymaga zabezpieczeń i szyfrowania dla portu TCP 80.

```cmd
REM   Add a firewall rule in a startup task.

REM   Add an inbound rule requiring security and encryption for TCP port 80 traffic.
netsh advfirewall firewall add rule name="Require Encryption for Inbound TCP/80" protocol=TCP dir=in localport=80 security=authdynenc action=allow >> "%TEMP%\StartupLog.txt" 2>&1

REM   If an error occurred, return the errorlevel.
EXIT /B %errorlevel%
```

## <a name="block-a-specific-ip-address"></a>Blok określonego adresu IP
Możesz ograniczyć dostęp roli sieci web platformy Azure na zestaw określonych adresów IP, modyfikując programu IIS **web.config** pliku. Należy również użyć pliku polecenia, który umożliwia odblokowanie **ipSecurity** sekcji **ApplicationHost.config** pliku.

Aby odblokować **ipSecurity** sekcji **ApplicationHost.config** plików, tworzenie pliku poleceń, który jest uruchamiany podczas uruchamiania roli. Utwórz folder na głównym poziomie roli sieci web o nazwie **uruchamiania** i, w tym folderze utwórz plik wsadowy o nazwie **startup.cmd**. Dodaj ten plik do projektu programu Visual Studio i ustaw właściwości **zawsze Kopiuj** aby upewnić się, że jest dołączony do pakietu.

Dodaj następujące zadanie uruchamiania do [ServiceDefinition.csdef] pliku.

```xml
<ServiceDefinition name="MyService" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition">
    <WebRole name="WebRole1">
        ...
        <Startup>
            <Task commandLine="startup.cmd" executionContext="elevated" />
        </Startup>
    </WebRole>
</ServiceDefinition>
```

To polecenie, aby dodać **startup.cmd** pliku:

```cmd
@echo off
@echo Installing "IPv4 Address and Domain Restrictions" feature 
powershell -ExecutionPolicy Unrestricted -command "Install-WindowsFeature Web-IP-Security"
@echo Unlocking configuration for "IPv4 Address and Domain Restrictions" feature 
%windir%\system32\inetsrv\AppCmd.exe unlock config -section:system.webServer/security/ipSecurity
```

To zadanie sprawia, że **startup.cmd** plik wsadowy do uruchomienia za każdym razem, gdy roli sieci web został zainicjowany, upewniając się, że wymagane **ipSecurity** sekcja jest odblokowane.

Na koniec zmodyfikuj [sekcja system.webServer](http://www.iis.net/configreference/system.webserver/security/ipsecurity#005) roli sieci web **web.config** plik, aby dodać listę adresów IP, które mają prawo dostępu, jak pokazano w poniższym przykładzie:

Ten przykładowy konfiguracji **umożliwia** wszystkie adresy IP na dostęp do serwera, z wyjątkiem dwóch zdefiniowany

```xml
<system.webServer>
    <security>
    <!--Unlisted IP addresses are granted access-->
    <ipSecurity>
        <!--The following IP addresses are denied access-->
        <add allowed="false" ipAddress="192.168.100.1" subnetMask="255.255.0.0" />
        <add allowed="false" ipAddress="192.168.100.2" subnetMask="255.255.0.0" />
    </ipSecurity>
    </security>
</system.webServer>
```

Ten przykładowy konfiguracji **nie zezwala na** wszystkie adresy IP do serwera, z wyjątkiem dwóch zdefiniowane.

```xml
<system.webServer>
    <security>
    <!--Unlisted IP addresses are denied access-->
    <ipSecurity allowUnlisted="false">
        <!--The following IP addresses are granted access-->
        <add allowed="true" ipAddress="192.168.100.1" subnetMask="255.255.0.0" />
        <add allowed="true" ipAddress="192.168.100.2" subnetMask="255.255.0.0" />
    </ipSecurity>
    </security>
</system.webServer>
```

## <a name="create-a-powershell-startup-task"></a>Utwórz zadanie uruchamiania programu PowerShell
Nie można wywołać skryptów programu Windows PowerShell bezpośrednio z [ServiceDefinition.csdef] pliku, ale może być wywoływany z w pliku wsadowym uruchamiania.

PowerShell (domyślnie) uruchamiać niepodpisane skrypty. Jeśli nie zarejestrujesz skryptu, należy skonfigurować PowerShell, aby uruchamiać niepodpisane skrypty. Aby uruchamiać niepodpisane skrypty **ExecutionPolicy** musi mieć ustawioną **bez ograniczeń**. **ExecutionPolicy** ustawienie Użyj jest oparty na wersji środowiska Windows PowerShell.

```cmd
REM   Run an unsigned PowerShell script and log the output
PowerShell -ExecutionPolicy Unrestricted .\startup.ps1 >> "%TEMP%\StartupLog.txt" 2>&1

REM   If an error occurred, return the errorlevel.
EXIT /B %errorlevel%
```

Jeśli używasz systemu operacyjnego gościa, który uruchamia program PowerShell 2.0 jest lub 1.0, możesz wymusić w wersji 2 do uruchomienia, a jeśli jest niedostępny, użyj wersji 1.

```cmd
REM   Attempt to set the execution policy by using PowerShell version 2.0 syntax.
PowerShell -Version 2.0 -ExecutionPolicy Unrestricted .\startup.ps1 >> "%TEMP%\StartupLog.txt" 2>&1

REM   If PowerShell version 2.0 isn't available. Set the execution policy by using the PowerShell
IF %ERRORLEVEL% EQU -393216 (
   PowerShell -Command "Set-ExecutionPolicy Unrestricted" >> "%TEMP%\StartupLog.txt" 2>&1
   PowerShell .\startup.ps1 >> "%TEMP%\StartupLog.txt" 2>&1
)

REM   If an error occurred, return the errorlevel.
EXIT /B %errorlevel%
```

## <a name="create-files-in-local-storage-from-a-startup-task"></a>Tworzenie plików w magazynie lokalnym z zadanie uruchamiania
Zasób lokalnego magazynu służy do przechowywania plików utworzonych przez zadanie uruchamiania, który jest dostępny później przez aplikację.

Aby utworzyć zasób magazynu lokalnego, Dodaj [LocalResources] sekcji do [ServiceDefinition.csdef] pliku, a następnie dodaj [LocalStorage] element podrzędny. Nadaj unikatową nazwę i odpowiedni rozmiar zasobu magazynu lokalnego uruchomienia zadania.

Do użycia zasobów magazynu lokalnego w uruchomienia zadania, należy utworzyć zmienną środowiskową, aby odwołać lokalizacji zasobów magazynu lokalnego. Następnie zadanie uruchamiania i aplikacji mogą odczytywać i zapisywać pliki do zasobu magazynu lokalnego.

Istotne sekcje **ServiceDefinition.csdef** plików są wyświetlane tutaj:

```xml
<ServiceDefinition name="MyService" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition">
  <WorkerRole name="WorkerRole1">
    ...

    <LocalResources>
      <LocalStorage name="StartupLocalStorage" sizeInMB="5"/>
    </LocalResources>

    <Startup>
      <Task commandLine="Startup.cmd" executionContext="limited" taskType="simple">
        <Environment>
          <Variable name="PathToStartupStorage">
            <RoleInstanceValue xpath="/RoleEnvironment/CurrentInstance/LocalResources/LocalResource[@name='StartupLocalStorage']/@path" />
          </Variable>
        </Environment>
      </Task>
    </Startup>
  </WorkerRole>
</ServiceDefinition>
```

Na przykład to **Startup.cmd** partii używa pliku **PathToStartupStorage** zmiennej środowiskowej, aby utworzyć plik **MyTest.txt** w lokalizacji magazynu lokalnego.

```cmd
REM   Create a simple text file.

ECHO This text will go into the MyTest.txt file which will be in the    >  "%PathToStartupStorage%\MyTest.txt"
ECHO path pointed to by the PathToStartupStorage environment variable.  >> "%PathToStartupStorage%\MyTest.txt"
ECHO The contents of the PathToStartupStorage environment variable is   >> "%PathToStartupStorage%\MyTest.txt"
ECHO "%PathToStartupStorage%".                                          >> "%PathToStartupStorage%\MyTest.txt"

REM   Exit the batch file with ERRORLEVEL 0.

EXIT /b 0
```

Magazyn lokalny folder mogą korzystać z zestawu SDK platformy Azure, za pomocą [GetLocalResource](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleenvironment.getlocalresource.aspx) metody.

```csharp
string localStoragePath = Microsoft.WindowsAzure.ServiceRuntime.RoleEnvironment.GetLocalResource("StartupLocalStorage").RootPath;

string fileContent = System.IO.File.ReadAllText(System.IO.Path.Combine(localStoragePath, "MyTestFile.txt"));
```

## <a name="run-in-the-emulator-or-cloud"></a>Uruchamianie w emulatorze lub w chmurze
Program może wykonywać inne czynności działającego w chmurze, gdy jest on w emulatora obliczeń w porównaniu do uruchomienia zadania. Na przykład można użyć świeżą kopię danych SQL tylko wtedy, gdy działa w emulatorze. Lub może być w celu optymalizacji wydajności dla chmury, który nie należy wykonać podczas uruchamiania w emulatorze.

Ta możliwość wykonywania różnych działań na emulator obliczeń i chmury można osiągnąć, tworząc zmienną środowiskową w [ServiceDefinition.csdef] pliku. Następnie można przetestować tej zmiennej środowiskowej dla wartości w zadania uruchamiania.

Aby utworzyć zmienną środowiskową, dodać [zmiennej]/[RoleInstanceValue] element i utworzyć wartość wyrażenia XPath `/RoleEnvironment/Deployment/@emulated`. Wartość **ComputeEmulatorRunning %** jest zmienna środowiskowa `true` podczas uruchamiania emulatora obliczeń i `false` podczas uruchamiania w chmurze.

```xml
<ServiceDefinition name="MyService" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition">
  <WorkerRole name="WorkerRole1">

    ...

    <Startup>
      <Task commandLine="Startup.cmd" executionContext="limited" taskType="simple">
        <Environment>
          <Variable name="ComputeEmulatorRunning">
            <RoleInstanceValue xpath="/RoleEnvironment/Deployment/@emulated" />
          </Variable>
        </Environment>
      </Task>
    </Startup>

  </WorkerRole>
</ServiceDefinition>
```

Teraz można sprawdzić zadania **ComputeEmulatorRunning %** zmiennej środowiskowej do wykonywania różnych akcji w oparciu Określa, czy jest uruchomiona rola w chmurze lub emulator. Oto .cmd skrypt powłoki, który sprawdza, czy tej zmiennej środowiskowej.

```cmd
REM   Check if this task is running on the compute emulator.

IF "%ComputeEmulatorRunning%" == "true" (
    REM   This task is running on the compute emulator. Perform tasks that must be run only in the compute emulator.
) ELSE (
    REM   This task is running on the cloud. Perform tasks that must be run only in the cloud.
)
```


## <a name="detect-that-your-task-has-already-run"></a>Wykryj, czy zadanie zostało już uruchomione
Rola może odtworzyć bez ponownego uruchomienia komputera powoduje ponowne uruchomienie zadania uruchamiania. Nie ma żadnych flagę wskazującą, czy zadanie zostało już uruchomione na hostingu maszyny Wirtualnej. Może być niektórych zadań, których nie ma znaczenia, że uruchomione wiele razy. Jednak może wystąpić sytuacja, w których należy uniemożliwić zadania uruchomione w więcej niż raz.

Najprostszym sposobem, aby wykryć, czy zadanie zostało już uruchomione jest utworzenie pliku w **% TEMP %** folderu, gdy zadanie zakończy się pomyślnie i wyszukać go na początku zadania. Poniżej przedstawiono przykładowy skrypt powłoki cmd nie.

```cmd
REM   If Task1_Success.txt exists, then Application 1 is already installed.
IF EXIST "%RoleRoot%\Task1_Success.txt" (
  ECHO Application 1 is already installed. Exiting. >> "%TEMP%\StartupLog.txt" 2>&1
  GOTO Finish
)

REM   Run your real exe task
ECHO Running XYZ >> "%TEMP%\StartupLog.txt" 2>&1
"%PathToApp1Install%\setup.exe" >> "%TEMP%\StartupLog.txt" 2>&1

IF %ERRORLEVEL% EQU 0 (
  REM   The application installed without error. Create a file to indicate that the task
  REM   does not need to be run again.

  ECHO This line will create a file to indicate that Application 1 installed correctly. > "%RoleRoot%\Task1_Success.txt"

) ELSE (
  REM   An error occurred. Log the error and exit with the error code.

  DATE /T >> "%TEMP%\StartupLog.txt" 2>&1
  TIME /T >> "%TEMP%\StartupLog.txt" 2>&1
  ECHO  An error occurred running task 1. Errorlevel = %ERRORLEVEL%. >> "%TEMP%\StartupLog.txt" 2>&1

  EXIT %ERRORLEVEL%
)

:Finish

REM   Exit normally.
EXIT /B 0
```

## <a name="task-best-practices"></a>Najlepsze rozwiązania w zakresie zadania
Poniżej przedstawiono najważniejsze wskazówki, które należy wykonać podczas konfigurowania zadania dla roli użytkownika sieci web lub procesu roboczego.

### <a name="always-log-startup-activities"></a>Zawsze dziennika uruchomienia działania
Program Visual Studio nie zapewnia debugera do wykonania kroków opisanych pliki wsadowe, dlatego warto uzyskać jak najwięcej danych w operacji pliki wsadowe, jak to możliwe. Rejestrowanie dane wyjściowe pliki wsadowe, zarówno **stdout** i **stderr**, umożliwiają ważne informacje podczas próby debugowanie i Usuń pliki wsadowe. Do logowania zarówno **stdout** i **stderr** do StartupLog.txt pliku w katalogu wskazanym przez **% TEMP %** zmiennej środowiskowej, Dodaj tekst `>>  "%TEMP%\\StartupLog.txt" 2>&1` na końcu określonych wierszy, które mają być rejestrowane. Na przykład, aby wykonać setup.exe w **PathToApp1Install %** katalogu:

    "%PathToApp1Install%\setup.exe" >> "%TEMP%\StartupLog.txt" 2>&1

Aby uprościć kod xml, należy utworzyć otoka *cmd* pliku, który wywołuje wszystkie uruchamiania zadań wraz z rejestrowania i zapewnia każdego zadania podrzędnego współużytkują tego samego zmiennych środowiskowych.

Może być irytujące do użycia `>> "%TEMP%\StartupLog.txt" 2>&1` na końcu każdego uruchomienia zadania. Rejestrowanie zadania można wymusić przez utworzenie otoka obsługuje rejestrowanie dla Ciebie. Tej otoki wywołuje plik wsadowy prawdziwe, który chcesz uruchomić. Wszystkie dane wyjściowe z pliku wsadowego docelowej, zostanie przekierowany do *Startuplog.txt* pliku.

Poniższy przykład pokazuje, jak przekierować wszystkie dane wyjściowe z pliku wsadowego uruchamiania. W tym przykładzie plik ServerDefinition.csdef tworzy zadanie uruchamiania, który wywołuje *logwrap.cmd*. *logwrap.cmd* wywołania *Startup2.cmd*, wszystkie dane wyjściowe do przekierowywania **% TEMP %\\StartupLog.txt**.

ServiceDefinition.cmd:

```xml
<Startup>
    <Task commandLine="logwrap.cmd startup2.cmd" executionContext="limited" taskType="simple" />
</Startup>
```

**logwrap.cmd:**

```cmd
@ECHO OFF

REM   logwrap.cmd calls passed in batch file, redirecting all output to the StartupLog.txt log file.

ECHO [%date% %time%] == START logwrap.cmd ============================================== >> "%TEMP%\StartupLog.txt" 2>&1
ECHO [%date% %time%] Running %1 >> "%TEMP%\StartupLog.txt" 2>&1

REM   Call the child command batch file, redirecting all output to the StartupLog.txt log file.
START /B /WAIT %1 >> "%TEMP%\StartupLog.txt" 2>&1

REM   Log the completion of child command.
ECHO [%date% %time%] Done >> "%TEMP%\StartupLog.txt" 2>&1

IF %ERRORLEVEL% EQU 0 (

   REM   No errors occurred. Exit logwrap.cmd normally.
   ECHO [%date% %time%] == END logwrap.cmd ================================================ >> "%TEMP%\StartupLog.txt" 2>&1
   ECHO.  >> "%TEMP%\StartupLog.txt" 2>&1
   EXIT /B 0

) ELSE (

   REM   Log the error.
   ECHO [%date% %time%] An error occurred. The ERRORLEVEL = %ERRORLEVEL%.  >> "%TEMP%\StartupLog.txt" 2>&1
   ECHO [%date% %time%] == END logwrap.cmd ================================================ >> "%TEMP%\StartupLog.txt" 2>&1
   ECHO.  >> "%TEMP%\StartupLog.txt" 2>&1
   EXIT /B %ERRORLEVEL%

)
```

**Startup2.cmd:**

```cmd
@ECHO OFF

REM   This is the batch file where the startup steps should be performed. Because of the
REM   way Startup2.cmd was called, all commands and their outputs will be stored in the
REM   StartupLog.txt file in the directory pointed to by the TEMP environment variable.

REM   If an error occurs, the following command will pass the ERRORLEVEL back to the
REM   calling batch file.

ECHO [%date% %time%] Some log information about this task
ECHO [%date% %time%] Some more log information about this task

EXIT %ERRORLEVEL%
```

Przykładowe dane wyjściowe w **StartupLog.txt** pliku:

```txt
[Mon 10/17/2016 20:24:46.75] == START logwrap.cmd ============================================== 
[Mon 10/17/2016 20:24:46.75] Running command1.cmd 
[Mon 10/17/2016 20:24:46.77] Some log information about this task
[Mon 10/17/2016 20:24:46.77] Some more log information about this task
[Mon 10/17/2016 20:24:46.77] Done 
[Mon 10/17/2016 20:24:46.77] == END logwrap.cmd ================================================ 
```

> [!TIP]
> **StartupLog.txt** plik znajduje się w *C:\Resources\temp\\\RoleTemp {identyfikator roli}* folderu.
> 
> 

### <a name="set-executioncontext-appropriately-for-startup-tasks"></a>Ustaw executionContext odpowiednio do uruchamiania zadań
Ustaw uprawnienia odpowiednio do uruchomienia zadania. Czasami uruchamiania zadań należy uruchomić z podwyższonym poziomem uprawnień, nawet jeśli roli jest uruchamiany z normalnym uprawnieniami.

[ExecutionContext][zadań] atrybut ustawia poziom uprawnień uruchamiania zadania. Przy użyciu `executionContext="limited"` oznacza, że zadanie uruchamiania ma taki sam poziom uprawnień jako rolę. Przy użyciu `executionContext="elevated"` oznacza, że zadanie uruchamiania ma uprawnienia administratora, dzięki czemu zadanie uruchamiania do wykonywania zadań administratora bez udzielania uprawnień administratora do roli użytkownika.

Przykładem zadanie uruchamiania, który wymaga pełnych uprawnień jest zadanie uruchamiania, która używa **AppCmd.exe** do skonfigurowania usług IIS. **AppCmd.exe** wymaga `executionContext="elevated"`.

### <a name="use-the-appropriate-tasktype"></a>Użyć odpowiednich taskType
[TaskType][zadań] atrybut określa sposób uruchamiania zadań jest wykonywany. Istnieją trzy wartości: **proste**, **tła**, i **pierwszego planu**. Zadania tła i pierwszego planu są uruchamiane asynchronicznie, a następnie prostych zadań są wykonywane synchronicznie pojedynczo.

Z **proste** uruchomienia zadania, można ustawić kolejność uruchamiania zadań, według kolejności, w którym zadania są wymienione w pliku ServiceDefinition.csdef. Jeśli **proste** zadań kończy się z kodem zakończenia inną niż zero, a następnie zatrzymuje procedury uruchamiania i roli nie uruchomi się.

Różnica między **tła** uruchamiania zadań i **pierwszego planu** uruchomienia zadania oznacza, że **pierwszego planu** zadania zachować roli uruchomiony, dopóki nie **pierwszego planu** zakończeniem zadania. Oznacza to również, że jeśli **pierwszego planu** zawiesza się zadania lub awarii (Crash), rola nie jest odtwarzana do **pierwszego planu** wymusza zadań zamknięte. Z tego powodu **tła** zadania są zalecane dla asynchronicznego uruchamiania zadań, chyba że potrzebne danej funkcji **pierwszego planu** zadań.

### <a name="end-batch-files-with-exit-b-0"></a>Pliki wsadowe zakończenia z /B zakończenia 0
Rola uruchamia się tylko wtedy, jeśli **errorlevel** z każdej z prostego uruchamiania zadań wynosi zero. Nie wszystkie programy ustawić **errorlevel** (kod zakończenia) poprawnie, więc plik wsadowy powinien kończyć się `EXIT /B 0` Jeżeli wszystko działa prawidłowo.

Brak `EXIT /B 0` na końcu partii uruchamiania pliku jest częstą przyczyną ról, które nie są uruchamiane.

> [!NOTE]
> I zaobserwowany tej partii zagnieżdżone pliki czasami rozłączaj po użyciu `/B` parametru. Może zajść potrzeba upewnij się, że ten problem zawieszenie nie odbywa się jeśli inny plik wsadowy wymaga bieżącego pliku wsadowego, takich jak użycie [otoki dziennika](#always-log-startup-activities). Można pominąć `/B` parametru w tym przypadku.
> 
> 

### <a name="expect-startup-tasks-to-run-more-than-once"></a>Oczekiwane uruchomienia zadania mają działać więcej niż raz
Nie wszystkie odtwarza roli obejmować ponowne uruchomienie komputera, ale wszystkie odtwarza roli obejmują uruchomione wszystkie zadania uruchamiania. Oznacza to, że uruchamiania zadań muszą zostać można uruchamiać wiele razy między uruchamiany ponownie bez problemów. Jest to omówione w [powyższej sekcji](#detect-that-your-task-has-already-run).

### <a name="use-local-storage-to-store-files-that-must-be-accessed-in-the-role"></a>Użyj lokalnego magazynu do przechowywania plików, które muszą być dostępne w roli
Jeśli chcesz skopiować lub tworzenie pliku podczas uruchamiania zadania, który jest dostępny dla Twojej roli tego pliku muszą znajdować się w magazynie lokalnym. Zobacz [powyższej sekcji](#create-files-in-local-storage-from-a-startup-task).

## <a name="next-steps"></a>Następne kroki
Przejrzyj chmury [z modelu i pakietu](cloud-services-model-and-package.md)

Dowiedz się więcej na temat [zadania](cloud-services-startup-tasks.md) pracy.

[Tworzenie i wdrażanie](cloud-services-how-to-create-deploy-portal.md) pakietu usługi w chmurze.

[ServiceDefinition.csdef]: cloud-services-model-and-package.md#csdef
[zadań]: https://msdn.microsoft.com/library/azure/gg557552.aspx#Task
[Startup]: https://msdn.microsoft.com/library/azure/gg557552.aspx#Startup
[Runtime]: https://msdn.microsoft.com/library/azure/gg557552.aspx#Runtime
[środowiska]: https://msdn.microsoft.com/library/azure/gg557552.aspx#Environment
[zmiennej]: https://msdn.microsoft.com/library/azure/gg557552.aspx#Variable
[RoleInstanceValue]: https://msdn.microsoft.com/library/azure/gg557552.aspx#RoleInstanceValue
[RoleEnvironment]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleenvironment.aspx
[Punkty końcowe]: https://msdn.microsoft.com/library/azure/gg557552.aspx#Endpoints
[LocalStorage]: https://msdn.microsoft.com/library/azure/gg557552.aspx#LocalStorage
[LocalResources]: https://msdn.microsoft.com/library/azure/gg557552.aspx#LocalResources
[RoleInstanceValue]: https://msdn.microsoft.com/library/azure/gg557552.aspx#RoleInstanceValue
