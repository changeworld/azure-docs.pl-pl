---
title: Typowe zadania uruchamiania usług w chmurze | Dokumenty firmy Microsoft
description: Zawiera kilka przykładów typowych zadań uruchamiania, które można wykonać w roli sieci web usług w chmurze lub roli procesu roboczego.
services: cloud-services
documentationcenter: ''
author: tgore03
ms.service: cloud-services
ms.topic: article
ms.date: 07/18/2017
ms.author: tagore
ms.openlocfilehash: 4fe1ee3ccf2849943959889838ba0f22fb64bb9a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79273061"
---
# <a name="common-cloud-service-startup-tasks"></a>Zadania uruchamiania usługi Common Cloud Service
W tym artykule przedstawiono kilka przykładów typowych zadań uruchamiania, które można wykonać w usłudze w chmurze. Zadania uruchamiania można używać do wykonywania operacji przed rozpoczęciem roli. Operacje, które można wykonać, obejmują instalowanie składnika, rejestrowanie składników COM, ustawianie kluczy rejestru lub uruchamianie długotrwałego procesu. 

Zobacz [ten artykuł,](cloud-services-startup-tasks.md) aby zrozumieć, jak działają zadania uruchamiania, a w szczególności jak utworzyć wpisy definiujące zadanie uruchamiania.

> [!NOTE]
> Zadania uruchamiania nie mają zastosowania do maszyn wirtualnych, tylko do ról Usługi w chmurze i procesu roboczego.
> 

## <a name="define-environment-variables-before-a-role-starts"></a>Definiowanie zmiennych środowiskowych przed rozpoczęciem roli
Jeśli potrzebujesz zmiennych środowiskowych zdefiniowanych dla określonego zadania, użyj [environment] element wewnątrz [Task] elementu.

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

Zmienne można również użyć [prawidłowej wartości XPath platformy Azure,](cloud-services-role-config-xpath.md) aby odwołać się do czegoś o wdrożeniu. Zamiast używać atrybutu, `value` zdefiniuj element [podrzędny RoleInstanceValue.]

```xml
<Variable name="PathToStartupStorage">
    <RoleInstanceValue xpath="/RoleEnvironment/CurrentInstance/LocalResources/LocalResource[@name='StartupLocalStorage']/@path" />
</Variable>
```


## <a name="configure-iis-startup-with-appcmdexe"></a>Konfigurowanie uruchamiania usługi IIS za pomocą programu AppCmd.exe
Narzędzie wiersza polecenia [AppCmd.exe](https://technet.microsoft.com/library/jj635852.aspx) może służyć do zarządzania ustawieniami usług IIS podczas uruchamiania na platformie Azure. *Program AppCmd.exe* zapewnia wygodny dostęp do ustawień konfiguracji w wierszu polecenia do użycia w zadaniach uruchamiania na platformie Azure. Korzystanie z *appcmd.exe*, Ustawienia witryny mogą być dodawane, modyfikowane lub usuwane dla aplikacji i witryn.

Istnieje jednak kilka rzeczy, na które należy zwrócić uwagę podczas korzystania z *appcmd.exe* jako zadania startowego:

* Zadania uruchamiania można uruchamiać więcej niż jeden raz między ponownym uruchomieniem. Na przykład, gdy rola jest odtwarzana.
* Jeśli akcja *AppCmd.exe* jest wykonywana więcej niż jeden raz, może wygenerować błąd. Na przykład próba dodania sekcji do *witryny Web.config* dwukrotnie może spowodować wygenerowanie błędu.
* Zadania uruchamiania nie powiodą się, jeśli zwracają kod zakończenia niezerowy lub **poziom błędu**. Na przykład, gdy *appcmd.exe* generuje błąd.

Dobrą praktyką jest sprawdzenie **poziomu błędu** po wywołaniu *appcmd.exe*, co jest łatwe do zrobienia, jeśli zawinąć *wywołanie AppCmd.exe* z *.cmd* pliku. Jeśli wykryjesz znaną odpowiedź **poziomu błędu,** możesz ją zignorować lub przekazać z powrotem.

Poziom błędu zwrócony przez *AppCmd.exe* są wymienione w pliku winerror.h, a także można zobaczyć na [MSDN](/windows/desktop/Debug/system-error-codes--0-499-).

### <a name="example-of-managing-the-error-level"></a>Przykład zarządzania poziomem błędu
W tym przykładzie dodano sekcję kompresji i wpis kompresji dla JSON do pliku *Web.config* z obsługą błędów i rejestrowaniem.

Odpowiednie sekcje pliku [ServiceDefinition.csdef] są pokazane tutaj, które obejmują ustawienie `elevated` [atrybutu executionContext,](/previous-versions/azure/reference/gg557552(v=azure.100)#task) aby nadać *appcmd.exe* wystarczające uprawnienia do zmiany ustawień w pliku *Web.config:*

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

Plik wsadowy *Startup.cmd* używa *pliku AppCmd.exe* do dodania sekcji kompresji i wpisu kompresji dla usługi JSON do pliku *Web.config.* Oczekiwany **poziom błędu** 183 jest ustawiony na zero przy użyciu VERIFY. EXE program wiersza polecenia. Nieoczekiwane błędy są rejestrowane w StartupErrorLog.txt.

```cmd
REM   *** Add a compression section to the Web.config file. ***
%windir%\system32\inetsrv\appcmd set config /section:urlCompression /doDynamicCompression:True /commit:apphost >> "%TEMP%\StartupLog.txt" 2>&1

REM   ERRORLEVEL 183 occurs when trying to add a section that already exists. This error is expected if this
REM   batch file were executed twice. This can occur and must be accounted for in an Azure startup
REM   task. To handle this situation, set the ERRORLEVEL to zero by using the Verify command. The Verify
REM   command will safely set the ERRORLEVEL to zero.
IF %ERRORLEVEL% EQU 183 VERIFY > NUL

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
Na platformie Azure są skutecznie dwie zapory. Pierwsza zapora steruje połączeniami między maszyną wirtualną a światem zewnętrznym. Ta zapora jest kontrolowana przez element [Punkty końcowe] w pliku [ServiceDefinition.csdef.]

Druga zapora steruje połączeniami między maszyną wirtualną a procesami w obrębie tej maszyny wirtualnej. Ta zapora może `netsh advfirewall firewall` być kontrolowana za pomocą narzędzia wiersza polecenia.

Platforma Azure tworzy reguły zapory dla procesów uruchomionych w ramach ról. Na przykład po uruchomieniu usługi lub programu platforma Azure automatycznie tworzy reguły zapory niezbędne, aby umożliwić tej usłudze komunikowanie się z Internetem. Jeśli jednak utworzysz usługę uruchamianą przez proces poza rolą (np. usługę COM+ lub zaplanowane zadanie systemu Windows), musisz ręcznie utworzyć regułę zapory, aby zezwolić na dostęp do tej usługi. Te reguły zapory można utworzyć za pomocą zadania uruchamiania.

Zadanie uruchamiania, które tworzy regułę zapory, musi mieć [wykonanieContext][Task] z **podwyższonym poziomem uprawnień**. Dodaj następujące zadanie uruchamiania do pliku [ServiceDefinition.csdef.]

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

Aby dodać regułę zapory, `netsh advfirewall firewall` należy użyć odpowiednich poleceń w pliku wsadowym uruchamiania. W tym przykładzie zadanie uruchamiania wymaga zabezpieczeń i szyfrowania dla portu TCP 80.

```cmd
REM   Add a firewall rule in a startup task.

REM   Add an inbound rule requiring security and encryption for TCP port 80 traffic.
netsh advfirewall firewall add rule name="Require Encryption for Inbound TCP/80" protocol=TCP dir=in localport=80 security=authdynenc action=allow >> "%TEMP%\StartupLog.txt" 2>&1

REM   If an error occurred, return the errorlevel.
EXIT /B %errorlevel%
```

## <a name="block-a-specific-ip-address"></a>Blokowanie określonego adresu IP
Dostęp do roli sieci Web platformy Azure można ograniczyć do zestawu określonych adresów IP, modyfikując plik **web.config** usług IIS. Należy również użyć pliku polecenia, który odblokowuje sekcję **ipSecurity** pliku **ApplicationHost.config.**

Aby odblokować sekcję **ipSecurity** pliku **ApplicationHost.config,** utwórz plik polecenia, który działa przy rozpoczynaniu roli. Utwórz folder na poziomie głównym roli sieci Web o nazwie **uruchamianie** i w tym folderze utwórz plik wsadowy o nazwie **startup.cmd**. Dodaj ten plik do projektu programu Visual Studio i ustaw właściwości **na Zawsze kopiuj,** aby upewnić się, że jest on zawarty w pakiecie.

Dodaj następujące zadanie uruchamiania do pliku [ServiceDefinition.csdef.]

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

Dodaj to polecenie do pliku **startup.cmd:**

```cmd
@echo off
@echo Installing "IPv4 Address and Domain Restrictions" feature 
powershell -ExecutionPolicy Unrestricted -command "Install-WindowsFeature Web-IP-Security"
@echo Unlocking configuration for "IPv4 Address and Domain Restrictions" feature 
%windir%\system32\inetsrv\AppCmd.exe unlock config -section:system.webServer/security/ipSecurity
```

To zadanie powoduje uruchomienie pliku wsadowego **startup.cmd** za każdym razem, gdy zostanie zainicjowana rola sieci web, zapewniając odblokowanie wymaganej sekcji **ipSecurity.**

Na koniec zmodyfikuj [sekcję system.webServer](https://www.iis.net/configreference/system.webserver/security/ipsecurity#005) pliku **web.config** roli sieci Web, aby dodać listę adresów IP, którym udzielono dostępu, jak pokazano w poniższym przykładzie:

Ten przykładowy config **umożliwia** dostęp do serwera wszystkim adresom IP z wyjątkiem dwóch zdefiniowanych

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

Ta przykładowa **konfigura uniemożliwia** dostęp do serwera wszystkim adresom IP z wyjątkiem dwóch zdefiniowanych.

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

## <a name="create-a-powershell-startup-task"></a>Tworzenie zadania uruchamiania programu PowerShell
Skryptów programu Windows PowerShell nie można wywoływać bezpośrednio z pliku [ServiceDefinition.csdef,] ale można je wywoływać z poziomu pliku wsadowego uruchamiania.

Program PowerShell (domyślnie) nie uruchamia niepodpisanych skryptów. Jeśli skrypt nie zostanie podpisany, należy skonfigurować program PowerShell do uruchamiania niepodpisanych skryptów. Aby uruchomić skrypty niepodpisane, **executionPolicy** musi być ustawiona na **Nieograniczone**. Używane ustawienie **ExecutionPolicy** jest oparte na wersji programu Windows PowerShell.

```cmd
REM   Run an unsigned PowerShell script and log the output
PowerShell -ExecutionPolicy Unrestricted .\startup.ps1 >> "%TEMP%\StartupLog.txt" 2>&1

REM   If an error occurred, return the errorlevel.
EXIT /B %errorlevel%
```

Jeśli używasz systemu operacyjnego gościa, który jest uruchomiony programEm PowerShell 2.0 lub 1.0, możesz wymusić uruchomienie wersji 2, a jeśli jest niedostępny, użyj wersji 1.

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
Zasób magazynu lokalnego służy do przechowywania plików utworzonych przez zadanie uruchamiania, który jest uzyskiwał dostęp później przez aplikację.

Aby utworzyć zasób magazynu lokalnego, dodaj sekcję [LocalResources] do pliku [ServiceDefinition.csdef,] a następnie dodaj element [podrzędny LocalStorage.] Nadaj zasobowi magazynu lokalnego unikatową nazwę i odpowiedni rozmiar dla zadania uruchamiania.

Aby użyć zasobu magazynu lokalnego w zadaniu uruchamiania, należy utworzyć zmienną środowiskową, aby odwoływać się do lokalizacji zasobów magazynu lokalnego. Następnie zadanie uruchamiania i aplikacja są w stanie odczytywać i zapisywać pliki do zasobu magazynu lokalnego.

Odpowiednie sekcje pliku **ServiceDefinition.csdef** są pokazane tutaj:

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

Na przykład ten plik wsadowy **Startup.cmd** używa zmiennej środowiskowej **PathToStartupStorage** do utworzenia pliku **MyTest.txt** w lokalizacji magazynu lokalnego.

```cmd
REM   Create a simple text file.

ECHO This text will go into the MyTest.txt file which will be in the    >  "%PathToStartupStorage%\MyTest.txt"
ECHO path pointed to by the PathToStartupStorage environment variable.  >> "%PathToStartupStorage%\MyTest.txt"
ECHO The contents of the PathToStartupStorage environment variable is   >> "%PathToStartupStorage%\MyTest.txt"
ECHO "%PathToStartupStorage%".                                          >> "%PathToStartupStorage%\MyTest.txt"

REM   Exit the batch file with ERRORLEVEL 0.

EXIT /b 0
```

Dostęp do folderu magazynu lokalnego można uzyskać z narzędzia Azure SDK przy użyciu metody [GetLocalResource.](/previous-versions/azure/reference/ee772845(v=azure.100))

```csharp
string localStoragePath = Microsoft.WindowsAzure.ServiceRuntime.RoleEnvironment.GetLocalResource("StartupLocalStorage").RootPath;

string fileContent = System.IO.File.ReadAllText(System.IO.Path.Combine(localStoragePath, "MyTestFile.txt"));
```

## <a name="run-in-the-emulator-or-cloud"></a>Uruchamianie w emulatorze lub chmurze
Zadanie uruchamiania może wykonywać różne kroki, gdy działa w chmurze w porównaniu do tego, kiedy znajduje się w emulatorze obliczeniowym. Na przykład można użyć nowej kopii danych SQL tylko podczas uruchamiania w emulatorze. Lub można zrobić kilka optymalizacji wydajności dla chmury, które nie trzeba zrobić podczas uruchamiania w emulatorze.

Ta możliwość wykonywania różnych akcji na emulatorze obliczeniowym i chmurze można wykonać, tworząc zmienną środowiskową w pliku [ServiceDefinition.csdef.] Następnie należy przetestować tę zmienną środowiskową dla wartości w zadaniu uruchamiania.

Aby utworzyć zmienną środowiskową, dodaj element [Variable]/[RoleInstanceValue] i utwórz wartość XPath . `/RoleEnvironment/Deployment/@emulated` Wartość zmiennej środowiskowej **%ComputeEmulatorRunning%** jest `true` uruchamiana na emulatorze obliczeniowym i `false` podczas uruchamiania w chmurze.

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

Zadanie można teraz sprawdzić **%ComputeEmulatorRunning%** zmiennej środowiskowej do wykonywania różnych akcji w zależności od tego, czy rola jest uruchomiona w chmurze lub emulatora. Oto skrypt powłoki cmd, który sprawdza, czy dla tej zmiennej środowiskowej.

```cmd
REM   Check if this task is running on the compute emulator.

IF "%ComputeEmulatorRunning%" == "true" (
    REM   This task is running on the compute emulator. Perform tasks that must be run only in the compute emulator.
) ELSE (
    REM   This task is running on the cloud. Perform tasks that must be run only in the cloud.
)
```


## <a name="detect-that-your-task-has-already-run"></a>Wykrywanie, że zadanie zostało już uruchomione
Rola może odtworzyć bez ponownego uruchomienia, co powoduje ponowne uruchomienie zadań uruchamiania. Nie ma flagi, aby wskazać, że zadanie zostało już uruchomione na maszynie wirtualnej hostingu. Możesz mieć pewne zadania, gdzie nie ma znaczenia, że są one uruchamiane wiele razy. Jednak może napotkać sytuację, w której należy uniemożliwić zadanie z systemem więcej niż jeden raz.

Najprostszym sposobem wykrycia, że zadanie zostało już uruchomione, jest utworzenie pliku w folderze **%TEMP%,** gdy zadanie zakończy się pomyślnie i wyszukiwanie go na początku zadania. Oto przykładowy skrypt powłoki cmd, który robi to za Ciebie.

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

## <a name="task-best-practices"></a>Najważniejsze wskazówki dotyczące zadań
Oto kilka najlepszych rozwiązań, które należy stosować podczas konfigurowania zadania dla roli sieci web lub procesu roboczego.

### <a name="always-log-startup-activities"></a>Zawsze rejestruj działania startowe
Visual Studio nie zapewnia debugera do krok po kroku plików wsadowych, więc dobrze jest uzyskać jak najwięcej danych na temat działania plików wsadowych, jak to możliwe. Rejestrowanie danych wyjściowych plików wsadowych, zarówno **stdout** i **stderr**, może dać ważne informacje podczas próby debugowania i naprawy plików wsadowych. Aby zarejestrować **zarówno stdout,** jak i **stderr** do pliku StartupLog.txt w katalogu wskazanym przez zmienną środowiskową **%TEMP%,** dodaj tekst `>>  "%TEMP%\\StartupLog.txt" 2>&1` na końcu określonych wierszy, które chcesz zarejestrować. Na przykład, aby wykonać plik setup.exe w katalogu **%PathToApp1Install%:**

    "%PathToApp1Install%\setup.exe" >> "%TEMP%\StartupLog.txt" 2>&1

Aby uprościć xml, można utworzyć plik *cmd* otoki, który wywołuje wszystkie zadania uruchamiania wraz z rejestrowaniem i zapewnia, że każde zadanie podrzędne współużywa te same zmienne środowiskowe.

Może się okazać irytujące `>> "%TEMP%\StartupLog.txt" 2>&1` jednak używać na końcu każdego zadania uruchamiania. Rejestrowanie zadań można wymusić, tworząc otokę, która obsługuje rejestrowanie. Ta otoka wywołuje prawdziwy plik wsadowy, który chcesz uruchomić. Wszelkie dane wyjściowe z docelowego pliku wsadowego zostaną przekierowane do pliku *Startuplog.txt.*

W poniższym przykładzie pokazano, jak przekierować wszystkie dane wyjściowe z pliku wsadowego uruchamiania. W tym przykładzie plik ServerDefinition.csdef tworzy zadanie uruchamiania, które wywołuje *plik logwrap.cmd*. *logwrap.cmd* wywołuje *startup2.cmd,* przekierując wszystkie dane wyjściowe do **%TEMP%\\StartupLog.txt**.

UsługaDefinicja.cmd:

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

Przykładowe dane wyjściowe w pliku **StartupLog.txt:**

```txt
[Mon 10/17/2016 20:24:46.75] == START logwrap.cmd ============================================== 
[Mon 10/17/2016 20:24:46.75] Running command1.cmd 
[Mon 10/17/2016 20:24:46.77] Some log information about this task
[Mon 10/17/2016 20:24:46.77] Some more log information about this task
[Mon 10/17/2016 20:24:46.77] Done 
[Mon 10/17/2016 20:24:46.77] == END logwrap.cmd ================================================ 
```

> [!TIP]
> Plik **StartupLog.txt** znajduje się w folderze *C:\Resources\temp\\{role identifier}\RoleTemp.*
> 
> 

### <a name="set-executioncontext-appropriately-for-startup-tasks"></a>Ustaw wykonanieContext odpowiednio dla zadań uruchamiania
Ustaw uprawnienia odpowiednio dla zadania uruchamiania. Czasami zadania uruchamiania muszą być uruchamiane z podwyższonymi uprawnieniami, nawet jeśli rola jest uruchamiana z normalnymi uprawnieniami.

Atrybut [executionContext][Task] ustawia poziom uprawnień zadania uruchamiania. Korzystanie `executionContext="limited"` oznacza, że zadanie uruchamiania ma ten sam poziom uprawnień co rola. Użycie `executionContext="elevated"` oznacza, że zadanie uruchamiania ma uprawnienia administratora, co umożliwia zadanie uruchamiania wykonywanie zadań administratora bez nadawania uprawnień administratora do roli.

Przykładem zadania uruchamiania, które wymaga podwyższonych uprawnień, jest zadanie uruchamiania, które konfiguruje usługi IIS za pomocą **programu AppCmd.exe.** **AppCmd.exe** `executionContext="elevated"`wymaga .

### <a name="use-the-appropriate-tasktype"></a>Użyj odpowiedniego taskType
Atrybut [TaskType][Task] określa sposób wykonywania zadania uruchamiania. Istnieją trzy wartości: **prosty,** **tło**i **pierwszy plan.** Zadania tła i pierwszego planu są uruchamiane asynchronicznie, a następnie proste zadania są wykonywane synchronicznie po jednym naraz.

Za pomocą **prostych** zadań uruchamiania można ustawić kolejność uruchamiania zadań według kolejności, w jakiej zadania są wymienione w pliku ServiceDefinition.csdef. Jeśli **proste** zadanie kończy się kodem zakończenia niezerowego, procedura uruchamiania zostanie zatrzymana, a rola nie zostanie rozpocznie się.

Różnica między zadaniami **uruchamiania w** **tle** a zadaniami uruchamiania pierwszego planu polega na tym, że zadania **pierwszego planu** utrzymują rolę w działa, dopóki zadanie **pierwszego planu** nie zostanie zakończona. Oznacza to również, że jeśli zadanie **pierwszego planu** zawiesza się lub ulega awarii, rola nie będzie odtwarzać, dopóki zadanie pierwszego planu nie zostanie **wymuszone** zamknięciem. Z tego powodu zadania **w tle** są zalecane dla asynchronicznych zadań **uruchamiania,** chyba że jest to potrzebne tej funkcji zadania pierwszego planu.

### <a name="end-batch-files-with-exit-b-0"></a>Zakończ pliki wsadowe z wyjściem /B 0
Rola zostanie uruchomiony tylko wtedy, gdy **poziom błędu** z każdego prostego zadania uruchamiania wynosi zero. Nie wszystkie programy poprawnie ustawiły **poziom błędu** (kod zakończenia), więc `EXIT /B 0` plik wsadowy powinien zakończyć się, jeśli wszystko działało poprawnie.

Brak `EXIT /B 0` na końcu pliku wsadowego uruchamiania jest częstą przyczyną ról, które nie uruchamiają się.

> [!NOTE]
> Zauważyłem, że zagnieżdżone pliki `/B` wsadowe czasami zawieszają się podczas korzystania z parametru. Można się upewnić, że ten problem z zawieszeniem nie występuje, jeśli inny plik wsadowy wywołuje bieżący plik wsadowy, na przykład jeśli używasz [otoki dziennika](#always-log-startup-activities). W tym przypadku `/B` można pominąć parametr.
> 
> 

### <a name="expect-startup-tasks-to-run-more-than-once"></a>Oczekiwanie na uruchomienie zadań uruchamiania więcej niż jeden raz
Nie wszystkie odtwarzanie roli obejmują ponowne uruchomienie, ale wszystkie odtwarzanie roli obejmują uruchamianie wszystkich zadań uruchamiania. Oznacza to, że zadania uruchamiania muszą być w stanie uruchamiać wiele razy między ponownym uruchomieniem bez żadnych problemów. Zostało to omówione w [powyższej sekcji](#detect-that-your-task-has-already-run).

### <a name="use-local-storage-to-store-files-that-must-be-accessed-in-the-role"></a>Używanie magazynu lokalnego do przechowywania plików, do których należy uzyskać dostęp w roli
Jeśli chcesz skopiować lub utworzyć plik podczas zadania uruchamiania, który jest następnie dostępny dla twojej roli, plik ten musi zostać umieszczony w magazynie lokalnym. Zobacz [poprzednią sekcję](#create-files-in-local-storage-from-a-startup-task).

## <a name="next-steps"></a>Następne kroki
Przejrzyj model usługi w chmurze [i pakiet](cloud-services-model-and-package.md)

Dowiedz się więcej o [działaniu zadań.](cloud-services-startup-tasks.md)

[Tworzenie i wdrażanie](cloud-services-how-to-create-deploy-portal.md) pakietu usług w chmurze.

[UsługaDefinicja.csdef]: cloud-services-model-and-package.md#csdef
[Zadanie]: https://msdn.microsoft.com/library/azure/gg557552.aspx#Task
[Startup]: https://msdn.microsoft.com/library/azure/gg557552.aspx#Startup
[Runtime]: https://msdn.microsoft.com/library/azure/gg557552.aspx#Runtime
[Środowisko]: https://msdn.microsoft.com/library/azure/gg557552.aspx#Environment
[Zmienna]: https://msdn.microsoft.com/library/azure/gg557552.aspx#Variable
[Wartość roli]: https://msdn.microsoft.com/library/azure/gg557552.aspx#RoleInstanceValue
[RoleEnvironment]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleenvironment.aspx
[Punkty końcowe]: https://msdn.microsoft.com/library/azure/gg557552.aspx#Endpoints
[Localstorage]: https://msdn.microsoft.com/library/azure/gg557552.aspx#LocalStorage
[Zasoby lokalne]: https://msdn.microsoft.com/library/azure/gg557552.aspx#LocalResources
[Wartość roli]: https://msdn.microsoft.com/library/azure/gg557552.aspx#RoleInstanceValue



