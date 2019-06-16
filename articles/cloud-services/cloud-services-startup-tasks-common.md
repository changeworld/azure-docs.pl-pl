---
title: Typowe zadania uruchamiania usług Cloud Services | Dokumentacja firmy Microsoft
description: Zawiera kilka przykładów typowych zadań uruchamiania, który chcesz wykonać w roli sieci web usług w chmurze lub roli procesu roboczego.
services: cloud-services
documentationcenter: ''
author: jpconnock
manager: timlt
editor: ''
ms.assetid: a7095dad-1ee7-4141-bc6a-ef19a6e570f1
ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/18/2017
ms.author: jeconnoc
ms.openlocfilehash: 1d78ab917589af0eae72eb70e3cdc2cc751072eb
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "67076443"
---
# <a name="common-cloud-service-startup-tasks"></a>Popularne zadania uruchamiania usługi w chmurze
W tym artykule przedstawiono kilka przykładów typowych zadań uruchamiania, które można wykonywać w usłudze w chmurze. Zadania uruchamiania służy do wykonywania operacji przed rozpoczęciem roli. Operacje, które można wykonać obejmują instalowanie składnika, rejestrowanie składników modelu COM, ustawienie kluczy rejestru lub uruchamiania długotrwałych procesu. 

Zobacz [w tym artykule](cloud-services-startup-tasks.md) , aby zrozumieć sposób działania uruchamiania zadań i wyraźnie, jak utworzyć wpisy, które definiują zadania uruchamiania.

> [!NOTE]
> Zadania uruchamiania nie są stosowane do maszyn wirtualnych, tylko do usługi sieci Web i chmury ról procesów roboczych.
> 

## <a name="define-environment-variables-before-a-role-starts"></a>Definiowanie zmiennych środowiskowych, przed rozpoczęciem roli
Zmienne środowiskowe zdefiniowane dla określonego zadania, należy użyć [Środowisko] element wewnątrz [Zadanie] elementu.

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

Zmienne można również użyć [prawidłową wartość wyrażenia XPath Azure](cloud-services-role-config-xpath.md) k odkazu coś o wdrożeniu. Zamiast używania `value` atrybutu, zdefiniuj [RoleInstanceValue] elementu podrzędnego.

```xml
<Variable name="PathToStartupStorage">
    <RoleInstanceValue xpath="/RoleEnvironment/CurrentInstance/LocalResources/LocalResource[@name='StartupLocalStorage']/@path" />
</Variable>
```


## <a name="configure-iis-startup-with-appcmdexe"></a>Konfigurowanie uruchamiania usług IIS przy użyciu AppCmd.exe
[AppCmd.exe](https://technet.microsoft.com/library/jj635852.aspx) narzędzie wiersza polecenia może służyć do zarządzania ustawieniami usługi IIS podczas uruchamiania na platformie Azure. *AppCmd.exe* zapewnia wygodne, wiersza polecenia dostęp do ustawień konfiguracji do użycia w zadaniach uruchamiania na platformie Azure. Za pomocą *AppCmd.exe*, ustawienia witryny sieci Web mogą być dodane, zmodyfikowane lub usunięte, aby aplikacje i witryny.

Istnieje jednak kilka kwestii, które Zwróć uwagę na użycie *AppCmd.exe* jako zadania uruchamiania:

* Zadania uruchamiania może działać więcej niż jeden raz między ponownymi uruchomieniami. Na przykład, gdy rola jest odtwarzana.
* Jeśli *AppCmd.exe* więcej niż jeden raz wykonać akcji, może on wygenerować błąd. Na przykład spróbujesz dodać sekcję, aby *Web.config* dwa razy, można wygenerować błąd.
* Uruchamianie zadań zakończy się niepowodzeniem, jeśli ich zwróci kod zakończenia różny od zera lub **errorlevel**. Na przykład, gdy *AppCmd.exe* generuje błąd.

Jest dobrą praktyką, aby sprawdzić **errorlevel** po wywołaniu *AppCmd.exe*, który jest łatwo zrobić, jeśli opakować wywołanie *AppCmd.exe* z *.cmd* pliku. Jeśli wykryje znanego **errorlevel** odpowiedzi, możesz go zignorować lub przekazać go ponownie.

Zmienna środowiskowa errorlevel zwrócony przez *AppCmd.exe* są wymienione w pliku winerror.h, a także będą widoczne na [MSDN](/windows/desktop/Debug/system-error-codes--0-499-).

### <a name="example-of-managing-the-error-level"></a>Przykład zarządzania poziom błędu
Ten przykład dodaje sekcji kompresji i wpis kompresji dla formatu JSON do *Web.config* pliku z obsługi i rejestrowania błędów.

Istotne sekcje [ServiceDefinition.csdef] plików są wyświetlane w tym miejscu, które obejmują ustawienia [kontekście wykonywania](/previous-versions/azure/reference/gg557552(v=azure.100)#task) atrybutu `elevated` zapewnienie *AppCmd.exe* wystarczających uprawnień do zmiany ustawień w *Web.config* pliku:

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

*Startup.cmd* usługi batch używa pliku *AppCmd.exe* do dodania dla formatu JSON do sekcji kompresji i wpis kompresji *Web.config* pliku. Oczekiwana **errorlevel** 183 jest ustawiony na wartość zero korzystanie z VERIFY. Plik EXE programu wiersza polecenia. Nieoczekiwany errorlevels są rejestrowane StartupErrorLog.txt.

```cmd
REM   *** Add a compression section to the Web.config file. ***
%windir%\system32\inetsrv\appcmd set config /section:urlCompression /doDynamicCompression:True /commit:apphost >> "%TEMP%\StartupLog.txt" 2>&1

REM   ERRORLEVEL 183 occurs when trying to add a section that already exists. This error is expected if this
REM   batch file were executed twice. This can occur and must be accounted for in an Azure startup
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

## <a name="add-firewall-rules"></a>Dodawanie reguły zapory
Na platformie Azure istnieją skutecznie dwiema zaporami. Pierwszy Zapora kontroluje połączenia między maszyną wirtualną a światem zewnętrznym. Ta zapora jest kontrolowana przez [Punkty końcowe] element [ServiceDefinition.csdef] pliku.

Drugi Zapora kontroluje połączenia między maszyną wirtualną a procesów w obrębie tej maszyny wirtualnej. Ta zapora może być kontrolowane przez `netsh advfirewall firewall` narzędzie wiersza polecenia.

Platforma Azure tworzy reguły zapory dla procesów uruchomionych w ramach usługi ról. Na przykład po uruchomieniu usługi lub programu Azure automatycznie tworzy reguły zapory niezbędne, aby umożliwić tej usługi do komunikacji z Internetem. Jednak jeśli tworzysz usługi, który jest uruchamiany w procesie poza roli użytkownika (np. usług COM + lub Windows zaplanowane zadanie), należy ręcznie utworzyć regułę zapory, aby zezwolić na dostęp do tej usługi. Tych reguł zapory można utworzyć za pomocą zadania uruchamiania.

Zadania uruchamiania, który tworzy regułę zapory muszą mieć [kontekście wykonywania][zadanie] z **z podwyższonym poziomem uprawnień**. Dodaj następujące zadanie uruchamiania w celu [ServiceDefinition.csdef] pliku.

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

Aby dodać reguły zapory, należy użyć odpowiedniej `netsh advfirewall firewall` poleceń w pliku wsadowym uruchamiania. W tym przykładzie zadanie uruchamiania wymaga zabezpieczeń i szyfrowania na porcie 80 protokołu TCP.

```cmd
REM   Add a firewall rule in a startup task.

REM   Add an inbound rule requiring security and encryption for TCP port 80 traffic.
netsh advfirewall firewall add rule name="Require Encryption for Inbound TCP/80" protocol=TCP dir=in localport=80 security=authdynenc action=allow >> "%TEMP%\StartupLog.txt" 2>&1

REM   If an error occurred, return the errorlevel.
EXIT /B %errorlevel%
```

## <a name="block-a-specific-ip-address"></a>Blokuj określonego adresu IP
Zestaw określonych adresów IP można ograniczyć dostęp roli sieci web platformy Azure, modyfikując usługi IIS **web.config** pliku. Należy również użyć pliku polecenia, który umożliwia odblokowanie **ipSecurity** części **ApplicationHost.config** pliku.

Aby odblokować **ipSecurity** części **ApplicationHost.config** plików, tworzenie pliku poleceń, który jest uruchamiany podczas uruchamiania roli. Utwórz folder na głównym poziomie roli sieci web o nazwie **uruchamiania** i, w tym folderze utwórz plik wsadowy o nazwie **startup.cmd**. Dodaj ten plik do projektu programu Visual Studio, a następnie ustaw właściwości na **zawsze Kopiuj** aby upewnić się, że jest uwzględniony w pakiecie.

Dodaj następujące zadanie uruchamiania w celu [ServiceDefinition.csdef] pliku.

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

To zadanie sprawia, że **startup.cmd** plik wsadowy uruchamiana za każdym razem, gdy rola sieci web jest inicjowany, upewniając się, że wymagane **ipSecurity** sekcja jest odblokowane.

Na koniec zmodyfikuj [sekcja system.webServer](https://www.iis.net/configreference/system.webserver/security/ipsecurity#005) roli sieci web **web.config** plik, aby dodać listę adresów IP, które mają dostęp, jak pokazano w poniższym przykładzie:

Ta konfiguracja przykładowe **umożliwia** wszystkie adresy IP na potrzeby uzyskiwania dostępu do serwera, z wyjątkiem dwóch zdefiniowane

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

Ta konfiguracja przykładowe **nie zezwala na** wszystkie adresy IP do serwera, z wyjątkiem dwóch zdefiniowane.

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
Skrypty programu Windows PowerShell nie mogą być wywoływane bezpośrednio z [ServiceDefinition.csdef] pliku, ale mogą być wywoływane z w ramach uruchamiania pliku wsadowego.

PowerShell (domyślnie) uruchamiać niepodpisane skrypty. Jeśli nie możesz utworzyć skrypt, należy skonfigurować programu PowerShell, aby uruchamiać niepodpisane skrypty. Aby uruchamiać niepodpisane skrypty **ExecutionPolicy** musi być równa **Unrestricted**. **ExecutionPolicy** ustawienie użycia opiera się na wersję środowiska Windows PowerShell.

```cmd
REM   Run an unsigned PowerShell script and log the output
PowerShell -ExecutionPolicy Unrestricted .\startup.ps1 >> "%TEMP%\StartupLog.txt" 2>&1

REM   If an error occurred, return the errorlevel.
EXIT /B %errorlevel%
```

Jeśli używasz systemu operacyjnego gościa, który uruchamia program PowerShell 2.0 jest lub 1.0, można wymusić w wersji 2 do uruchomienia, a jeśli jest niedostępny, należy użyć wersji 1.

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

## <a name="create-files-in-local-storage-from-a-startup-task"></a>Tworzenie plików w magazynie lokalnym z zadania uruchamiania
Zasób magazynu lokalnego służy do przechowywania plików utworzonych przez użytkownika zadania uruchamiania, który jest dostępny później przez aplikację.

Aby utworzyć zasób magazynu lokalnego, Dodaj [LocalResources] sekcji [ServiceDefinition.csdef] pliku, a następnie dodaj [LocalStorage] elementu podrzędnego. Nadaj unikatową nazwę i odpowiedni rozmiar zasobu lokalnego magazynu zadania uruchamiania.

Aby użyć zasobu magazynu lokalnego zadania uruchamiania, należy utworzyć zmienną środowiskową, aby odwoływać się do lokalizacji zasobów magazynu lokalnego. Następnie zadanie uruchamiania i aplikacji mogą odczytywać i zapisywać pliki do zasobu magazynu lokalnego.

Istotne sekcje **ServiceDefinition.csdef** pliku przedstawiono poniżej:

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

Na przykład to **Startup.cmd** usługi batch używa pliku **PathToStartupStorage** zmiennej środowiskowej, aby utworzyć plik **MyTest.txt** w lokalizacji magazynu lokalnego.

```cmd
REM   Create a simple text file.

ECHO This text will go into the MyTest.txt file which will be in the    >  "%PathToStartupStorage%\MyTest.txt"
ECHO path pointed to by the PathToStartupStorage environment variable.  >> "%PathToStartupStorage%\MyTest.txt"
ECHO The contents of the PathToStartupStorage environment variable is   >> "%PathToStartupStorage%\MyTest.txt"
ECHO "%PathToStartupStorage%".                                          >> "%PathToStartupStorage%\MyTest.txt"

REM   Exit the batch file with ERRORLEVEL 0.

EXIT /b 0
```

Dostępny Magazyn lokalny folder z zestawu SDK platformy Azure przy użyciu [GetLocalResource](/previous-versions/azure/reference/ee772845(v=azure.100)) metody.

```csharp
string localStoragePath = Microsoft.WindowsAzure.ServiceRuntime.RoleEnvironment.GetLocalResource("StartupLocalStorage").RootPath;

string fileContent = System.IO.File.ReadAllText(System.IO.Path.Combine(localStoragePath, "MyTestFile.txt"));
```

## <a name="run-in-the-emulator-or-cloud"></a>Uruchamianie w emulatorze lub w chmurze
Może mieć wykonanie innych czynności działającego w chmurze, gdy jest on w emulatorze obliczeń w porównaniu do zadania uruchamiania. Na przykład można użyć świeżą kopię danych SQL, tylko wtedy, gdy jest uruchomiona w emulatorze. Lub możesz chcieć wykonać kilka optymalizacji wydajności w chmurze, które trzeba wykonać, gdy jest uruchomiona w emulatorze.

Ta możliwość wykonywania różnych działań w emulatorze obliczeń i w chmurze można osiągnąć, tworząc zmienną środowiskową w [ServiceDefinition.csdef] pliku. Następnie testujesz tej zmiennej środowiskowej dla wartości z zadania uruchamiania.

Aby utworzyć zmienną środowiskową, Dodaj [Zmienna]/[RoleInstanceValue] elementu i utworzyć wartość XPath `/RoleEnvironment/Deployment/@emulated`. Wartość **ComputeEmulatorRunning %** jest zmienna środowiskowa `true` podczas uruchamiania w emulatorze obliczeń i `false` podczas uruchamiania w chmurze.

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

Zadania mogą teraz sprawdzić **ComputeEmulatorRunning %** zmiennej środowiskowej, aby wykonywać różne akcje oparte na tego, czy jest uruchomiona rola w chmurze lub w emulatorze. Oto .cmd skrypt powłoki, który sprawdza, czy dla tej zmiennej środowiskowej.

```cmd
REM   Check if this task is running on the compute emulator.

IF "%ComputeEmulatorRunning%" == "true" (
    REM   This task is running on the compute emulator. Perform tasks that must be run only in the compute emulator.
) ELSE (
    REM   This task is running on the cloud. Perform tasks that must be run only in the cloud.
)
```


## <a name="detect-that-your-task-has-already-run"></a>Wykrywa, że zadanie zostało już uruchomione
Rola może odzyskać bez ponownego uruchomienia systemu, powodując swoje zadania uruchamiania ponownie uruchomić. Nie ma żadnych flagę wskazującą, czy zadanie zostało już uruchomione na hostingu maszyn wirtualnych. Może być niektóre zadania, których nie ma znaczenia, czy działają wiele razy. Jednak można uruchamiać w sytuacji, w których trzeba uniemożliwienie zadania więcej niż jeden raz.

Najprostszym sposobem, aby wykryć, czy zadanie zostało już uruchomione, jest utworzenie pliku w **% TEMP %** folderu, gdy zadanie zakończy się pomyślnie i zwróć uwagę na jego przy uruchamianiu zadania. Poniżej przedstawiono przykładowy skrypt powłoki cmd robi to za Ciebie.

```cmd
REM   If Task1_Success.txt exists, then Application 1 is already installed.
IF EXIST "%PathToApp1Install%\Task1_Success.txt" (
  ECHO Application 1 is already installed. Exiting. >> "%TEMP%\StartupLog.txt" 2>&1
  GOTO Finish
)

REM   Run your real exe task
ECHO Running XYZ >> "%TEMP%\StartupLog.txt" 2>&1
"%PathToApp1Install%\setup.exe" >> "%TEMP%\StartupLog.txt" 2>&1

IF %ERRORLEVEL% EQU 0 (
  REM   The application installed without error. Create a file to indicate that the task
  REM   does not need to be run again.

  ECHO This line will create a file to indicate that Application 1 installed correctly. > "%PathToApp1Install%\Task1_Success.txt"

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

## <a name="task-best-practices"></a>Najlepsze rozwiązania zadania
Poniżej przedstawiono kilka najlepszych zasad, które należy wykonać podczas konfigurowania zadania dla roli sieci web lub proces roboczy.

### <a name="always-log-startup-activities"></a>Zawsze działań podczas uruchamiania dziennika
Program Visual Studio nie zapewnia debugera do kroku za pomocą plików usługi batch, więc warto uzyskać jak najwięcej danych operacji plików wsadowych, jak to możliwe. Rejestrowanie danych wyjściowych w plikach wsadowych zarówno **stdout** i **stderr**, udzieli Ci ważne informacje podczas próby debugowania i Usuń pliki wsadowe. Do logowania zarówno **stdout** i **stderr** do StartupLog.txt pliku w katalogu wskazanym przez **% TEMP %** zmiennej środowiskowej, Dodaj tekst `>>  "%TEMP%\\StartupLog.txt" 2>&1` do końca poszczególne wiersze, które mają być rejestrowane. Na przykład, aby wykonać setup.exe w **PathToApp1Install %** katalogu:

    "%PathToApp1Install%\setup.exe" >> "%TEMP%\StartupLog.txt" 2>&1

Aby uprościć kod xml, należy utworzyć otokę *cmd* pliku, który wywołuje wszystkie uruchamiania zadań wraz z rejestrowania i zapewnia każde zadanie podrzędne udostępnia ten sam zmiennych środowiskowych.

Może być irytujące do użycia `>> "%TEMP%\StartupLog.txt" 2>&1` na końcu każdego zadania uruchamiania. Rejestrowanie zadania można wymusić przez tworzenie otok, który obsługuje rejestrowania dla Ciebie. Tę otokę wywołuje plik wsadowy rzeczywistego, który chcesz uruchomić. Wszelkie dane wyjściowe z pliku wsadowego docelowej, zostanie przekierowany do *Startuplog.txt* pliku.

Poniższy przykład pokazuje, jak przekierować wszystkie dane wyjściowe z uruchamiania pliku wsadowego. W tym przykładzie plik ServerDefinition.csdef tworzy zadanie uruchamiania, który wywołuje *logwrap.cmd*. *logwrap.cmd* wywołania *Startup2.cmd*, przekierowanie wszystkie dane wyjściowe do **% TEMP %\\StartupLog.txt**.

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

### <a name="set-executioncontext-appropriately-for-startup-tasks"></a>Ustaw w kontekście wykonywania odpowiednio do zadania uruchamiania
Ustaw uprawnienia odpowiednio dla zadania uruchamiania. Czasami zadania uruchamiania należy uruchomić z podwyższonym poziomem uprawnień, mimo że roli jest uruchamiany z normalnym uprawnieniami.

[Kontekście wykonywania][zadanie] atrybut ustawia poziom uprawnień zadania uruchamiania. Za pomocą `executionContext="limited"` oznacza, że zadanie uruchamiania ma taki sam poziom uprawnień jako rolę. Za pomocą `executionContext="elevated"` oznacza, że zadanie uruchamiania ma uprawnienia administratora, co pozwala zadania uruchamiania do wykonywania zadań administratora bez udzielania uprawnień administratora do Twojej roli.

Przykładem zadania uruchamiania, który wymaga podniesionych uprawnień jest zadanie uruchamiania, który używa **AppCmd.exe** do skonfigurowania usług IIS. **AppCmd.exe** wymaga `executionContext="elevated"`.

### <a name="use-the-appropriate-tasktype"></a>Użyj odpowiedniego taskType
[TaskType][zadanie] atrybut określa sposób zadanie uruchamiania jest wykonywany. Istnieją trzy wartości: **proste**, **tła**, i **pierwszego planu**. Pierwszego planu i tła zadania są uruchamiane asynchronicznie, a następnie proste zadania są wykonywane synchronicznie pojedynczo.

Za pomocą **proste** zadań uruchamiania można ustawić kolejność uruchamiania zadań, które według kolejności, w którym zadania są wymienione w pliku ServiceDefinition.csdef. Jeśli **proste** zadań kończy się kod zakończenia różny od zera, a następnie zatrzymuje procedury uruchamiania i roli nie rozpoczyna się.

Różnica między **tła** zadań uruchamiania i **pierwszego planu** zadania uruchamiania jest to, że **pierwszego planu** zadania zachować roli działał do czasu  **pierwszy plan** zakończeniem zadania. Oznacza to również, że jeśli **pierwszego planu** zadanie zawiesza się lub awarie, rola nie zostanie odtwarzanie aż do **pierwszego planu** zadania jest wymuszone zamknięte. Z tego powodu **tła** zadania są zalecane dla asynchronicznego uruchamiania zadań, chyba że potrzebujesz tej funkcji **pierwszego planu** zadania.

### <a name="end-batch-files-with-exit-b-0"></a>Pliki wsadowe zakończenia z /B zakończenia 0
Rola uruchamia się tylko wtedy, gdy **errorlevel** każdego z nich Twój startup prostych zadań ma wartość zero. Nie wszystkie programy ustaw **errorlevel** (kod zakończenia) poprawnie, więc plik wsadowy powinien kończyć się `EXIT /B 0` Jeśli wszystko zostało wykonane prawidłowo.

Brak `EXIT /B 0` na końcu partii uruchamiania plików jest częstą przyczyną ról, które nie rozpoczynają się.

> [!NOTE]
> Czy mogę zaobserwowany tej partii zagnieżdżone pliki Rozłącz czasami przy użyciu `/B` parametru. Warto upewnić się, że ten problem zawieszenia nie odbywa się jeśli inny plik wsadowy wywołuje bieżący plik wsadowy, takich jak użycie [otoki dziennika](#always-log-startup-activities). Możesz pominąć `/B` parametru, w tym przypadku.
> 
> 

### <a name="expect-startup-tasks-to-run-more-than-once"></a>Oczekiwane zadania uruchamiania, aby uruchomić więcej niż raz
Nie wszystkie odtwarzanie roli dotyczyć ponowne uruchomienie komputera, ale uruchomione wszystkie zadania uruchamiania wszystkich odtwarzanie roli. Oznacza to, że zadania uruchamiania musi być w stanie uruchomić wiele razy między ponownymi uruchomieniami bez problemów. Ta czynność została omówiona [poprzedniej sekcji](#detect-that-your-task-has-already-run).

### <a name="use-local-storage-to-store-files-that-must-be-accessed-in-the-role"></a>Używany Magazyn lokalny do przechowywania plików, które muszą być dostępne w tej roli
Jeśli chcesz skopiować lub utworzyć plik podczas swoje zadania uruchamiania, który następnie jest dostępny dla Twojej roli ten plik należy umieścić w magazynie lokalnym. Zobacz [poprzedniej sekcji](#create-files-in-local-storage-from-a-startup-task).

## <a name="next-steps"></a>Kolejne kroki
Przejrzyj chmury [model i pakiet usługi](cloud-services-model-and-package.md)

Dowiedz się więcej o tym, jak [zadania](cloud-services-startup-tasks.md) pracy.

[Tworzenie i wdrażanie](cloud-services-how-to-create-deploy-portal.md) pakiet usługi w chmurze.

[ServiceDefinition.csdef]: cloud-services-model-and-package.md#csdef
[Zadanie]: https://msdn.microsoft.com/library/azure/gg557552.aspx#Task
[Startup]: https://msdn.microsoft.com/library/azure/gg557552.aspx#Startup
[Runtime]: https://msdn.microsoft.com/library/azure/gg557552.aspx#Runtime
[Środowisko]: https://msdn.microsoft.com/library/azure/gg557552.aspx#Environment
[Zmienna]: https://msdn.microsoft.com/library/azure/gg557552.aspx#Variable
[RoleInstanceValue]: https://msdn.microsoft.com/library/azure/gg557552.aspx#RoleInstanceValue
[RoleEnvironment]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleenvironment.aspx
[Punkty końcowe]: https://msdn.microsoft.com/library/azure/gg557552.aspx#Endpoints
[LocalStorage]: https://msdn.microsoft.com/library/azure/gg557552.aspx#LocalStorage
[LocalResources]: https://msdn.microsoft.com/library/azure/gg557552.aspx#LocalResources
[RoleInstanceValue]: https://msdn.microsoft.com/library/azure/gg557552.aspx#RoleInstanceValue
