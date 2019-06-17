---
title: Zainstaluj program .NET w ramach ról usług Azure Cloud Services | Dokumentacja firmy Microsoft
description: W tym artykule opisano, jak ręcznie zainstalować program .NET Framework na role usługi w chmurze w sieci web i proces roboczy
services: cloud-services
documentationcenter: .net
author: jpconnock
manager: timlt
editor: ''
ms.assetid: 8d1243dc-879c-4d1f-9ed0-eecd1f6a6653
ms.service: cloud-services
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/22/2018
ms.author: jeconnoc
ms.openlocfilehash: bc861b6730e8bf9db6ba2ab005496914f7b9ed89
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "64699677"
---
# <a name="install-net-on-azure-cloud-services-roles"></a>Zainstaluj program .NET w ramach ról usług Azure Cloud Services
W tym artykule opisano sposób instalowania wersji programu .NET Framework, które nie pochodzą z systemu operacyjnego gościa platformy Azure. Aby skonfigurować role usługi w chmurze w sieci web i proces roboczy, można użyć platformy .NET w systemie operacyjnym gościa.

Na przykład można zainstalować .NET 4.6.2 rodziny systemów operacyjnych gościa 4, która nie pochodzi z dowolnej wersji programu .NET 4.6. (5 z rodziny systemów operacyjnych gościa jest dostarczany z .NET 4.6). Aby uzyskać najnowsze informacje o wersjach systemu operacyjnego gościa platformy Azure, zobacz [informacje o nowych wersjach systemu operacyjnego gościa Azure](cloud-services-guestos-update-matrix.md). 

>[!IMPORTANT]
>Zestaw Azure SDK 2.9 zawiera ograniczenia dotyczące wdrażania .NET 4.6 rodziny systemów operacyjnych gościa, 4 lub wersji wcześniejszych. Poprawka dla ograniczenia jest dostępna w [Microsoft Docs](https://github.com/MicrosoftDocs/azure-cloud-services-files/tree/master/Azure%20Targets%20SDK%202.9) lokacji.

Aby zainstalować program .NET w roli sieci web i proces roboczy, obejmują Instalator sieci web platformy .NET w ramach projektu usługi w chmurze. Uruchom Instalatora jako część zadania uruchamiania roli. 

## <a name="add-the-net-installer-to-your-project"></a>Dodaj Instalatora .NET do projektu
Aby pobrać Instalator internetowy programu .NET Framework, należy wybrać wersję, która ma zostać zainstalowany:

* [Instalator sieci web 4.8 platformy .NET](https://dotnet.microsoft.com/download/thank-you/net48)
* [Instalator sieci web platformy .NET 4.7.2](https://go.microsoft.com/fwlink/?LinkId=863262)
* [Instalator sieci web platformy .NET 4.6.2](https://www.microsoft.com/download/details.aspx?id=53345)

Aby dodać Instalatora dla *web* roli:
  1. W **Eksploratora rozwiązań**w obszarze **role** w projekt usługi w chmurze, kliknij prawym przyciskiem myszy swoje *web* rolę i wybierz pozycję **Dodaj**  >  **Nowy Folder**. Utwórz folder o nazwie **bin**.
  2. Kliknij prawym przyciskiem myszy bin folder, a następnie wybierz pozycję **Dodaj** > **istniejący element**. Wybierz Instalatora .NET i dodaj go do folderu bin.
  
Aby dodać Instalatora dla *procesu roboczego* roli:
* Kliknij prawym przyciskiem myszy użytkownika *procesu roboczego* rolę i wybierz pozycję **Dodaj** > **istniejący element**. Wybierz Instalatora .NET i dodaj go do roli. 

Gdy pliki zostaną dodane w ten sposób do folderu zawartości rola, są automatycznie dodawane do pakietu usługi w chmurze. Pliki są następnie wdrażane na spójne lokalizacji na maszynie wirtualnej. Powtórz ten proces dla każdej roli sieci web i proces roboczy w usłudze w chmurze, więc, że wszystkie role mają kopię Instalatora.

> [!NOTE]
> Należy zainstalować .NET 4.6.2 na rola usługi w chmurze, nawet wtedy, gdy aplikacja jest przeznaczony dla .NET 4.6. System operacyjny gościa zawiera baza wiedzy [aktualizacji 3098779](https://support.microsoft.com/kb/3098779) i [aktualizacji 3097997](https://support.microsoft.com/kb/3097997). Po uruchomieniu aplikacji .NET, jeśli .NET 4.6 jest zainstalowany na podstawie aktualizacje bazy wiedzy Knowledge Base, mogą wystąpić problemy. Aby uniknąć tych problemów, należy zainstalować .NET 4.6.2, a nie w wersji 4.6. Aby uzyskać więcej informacji, zobacz [artykuł bazy wiedzy 3118750](https://support.microsoft.com/kb/3118750) i [4340191](https://support.microsoft.com/kb/4340191).
> 
> 

![Rola zawartości z plikami Instalatora][1]

## <a name="define-startup-tasks-for-your-roles"></a>Zdefiniuj zadania uruchamiania dla swoich ról
Zadania uruchamiania służy do wykonywania operacji przed rozpoczęciem roli. Instalowanie programu .NET Framework jako część zadania uruchamiania gwarantuje, czy framework jest zainstalowany, przed uruchomieniem jakiegokolwiek kodu aplikacji. Aby uzyskać więcej informacji na temat uruchamiania zadań, zobacz [uruchomienia zadania uruchamiania na platformie Azure](cloud-services-startup-tasks.md). 

1. Dodaj następującą zawartość do pliku ServiceDefinition.csdef w obszarze **WebRole** lub **workerrole —** węzła dla wszystkich ról:
   
    ```xml
    <LocalResources>
      <LocalStorage name="NETFXInstall" sizeInMB="1024" cleanOnRoleRecycle="false" />
    </LocalResources>    
    <Startup>
      <Task commandLine="install.cmd" executionContext="elevated" taskType="simple">
        <Environment>
          <Variable name="PathToNETFXInstall">
            <RoleInstanceValue xpath="/RoleEnvironment/CurrentInstance/LocalResources/LocalResource[@name='NETFXInstall']/@path" />
          </Variable>
          <Variable name="ComputeEmulatorRunning">
            <RoleInstanceValue xpath="/RoleEnvironment/Deployment/@emulated" />
          </Variable>
        </Environment>
      </Task>
    </Startup>
    ```
   
    Tej konfiguracji uruchamia polecenie konsoli `install.cmd` z uprawnieniami administratora, aby zainstalować program .NET Framework. Tworzy również konfigurację **LocalStorage** elementu o nazwie **NETFXInstall**. Skrypt uruchamiania ustawia folderu tymczasowego do używania tego zasobu magazynu lokalnego. 
    
    > [!IMPORTANT]
    > W celu zapewnienia prawidłowego zainstalowania Framework, należy ustawić co najmniej 1024 MB rozmiar tego zasobu.
    
    Aby uzyskać więcej informacji na temat uruchamiania zadań, zobacz [zadania uruchamiania typowych usług Azure Cloud Services](cloud-services-startup-tasks-common.md).

2. Utwórz plik o nazwie **install.cmd** i Dodaj poniższy skrypt instalacji do pliku.

   Skrypt sprawdza, czy określonej wersji programu .NET Framework jest już zainstalowana na maszynie wykonując zapytanie w rejestrze. Jeśli nie zainstalowano programu .NET w wersji, Instalator sieci web platformy .NET jest otwarty. Aby ułatwić rozwiązywanie problemów, skrypt loguje wszelką aktywność do pliku startuptasklog-(bieżąca data i godzina) .txt, która jest przechowywana w **InstallLogs** magazynu lokalnego.
   
   > [!IMPORTANT]
   > Użyj edytora tekstów podstawowy przykład w Notatniku Windows, aby utworzyć plik install.cmd. Jeśli używasz programu Visual Studio Utwórz plik tekstowy i zmień rozszerzenie na .cmd plik nadal może zawierać znacznik kolejności bajtów UTF-8. Ten znacznik może spowodować błąd, gdy jest uruchamiany w pierwszy wiersz skryptu. Aby uniknąć tego błędu, należy utworzyć pierwszy wiersz skryptu rem — instrukcja, który można było pominąć, stosując przetwarzanie kolejności bajtów. 
   > 
   >
   
   ```cmd
   REM Set the value of netfx to install appropriate .NET Framework. 
   REM ***** To install .NET 4.5.2 set the variable netfx to "NDP452" *****
   REM ***** To install .NET 4.6 set the variable netfx to "NDP46" *****
   REM ***** To install .NET 4.6.1 set the variable netfx to "NDP461" ***** https://go.microsoft.com/fwlink/?LinkId=671729
   REM ***** To install .NET 4.6.2 set the variable netfx to "NDP462" ***** https://www.microsoft.com/download/details.aspx?id=53345
   REM ***** To install .NET 4.7 set the variable netfx to "NDP47" ***** 
   REM ***** To install .NET 4.7.1 set the variable netfx to "NDP471" ***** https://go.microsoft.com/fwlink/?LinkId=852095
   REM ***** To install .NET 4.7.2 set the variable netfx to "NDP472" ***** https://go.microsoft.com/fwlink/?LinkId=863262
   set netfx="NDP472"
   REM ***** To install .NET 4.8 set the variable netfx to "NDP48" ***** https://dotnet.microsoft.com/download/thank-you/net48
      
   REM ***** Set script start timestamp *****
   set timehour=%time:~0,2%
   set timestamp=%date:~-4,4%%date:~-10,2%%date:~-7,2%-%timehour: =0%%time:~3,2%
   set "log=install.cmd started %timestamp%."
   
   REM ***** Exit script if running in Emulator *****
   if "%ComputeEmulatorRunning%"=="true" goto exit
   
   REM ***** Needed to correctly install .NET 4.6.1, otherwise you may see an out of disk space error *****
   set TMP=%PathToNETFXInstall%
   set TEMP=%PathToNETFXInstall%
   
   REM ***** Setup .NET filenames and registry keys *****
   if %netfx%=="NDP472" goto NDP472
   if %netfx%=="NDP471" goto NDP471
   if %netfx%=="NDP47" goto NDP47
   if %netfx%=="NDP462" goto NDP462
   if %netfx%=="NDP461" goto NDP461
   if %netfx%=="NDP46" goto NDP46
   
   set "netfxinstallfile=NDP452-KB2901954-Web.exe"
   set netfxregkey="0x5cbf5"
   goto logtimestamp
   
   :NDP46
   set "netfxinstallfile=NDP46-KB3045560-Web.exe"
   set netfxregkey="0x6004f"
   goto logtimestamp
   
   :NDP461
   set "netfxinstallfile=NDP461-KB3102438-Web.exe"
   set netfxregkey="0x6040e"
   goto logtimestamp
   
   :NDP462
   set "netfxinstallfile=NDP462-KB3151802-Web.exe"
   set netfxregkey="0x60632"
   goto logtimestamp
   
   :NDP47
   set "netfxinstallfile=NDP47-KB3186500-Web.exe"
   set netfxregkey="0x707FE"
   goto logtimestamp
   
   :NDP471
   set "netfxinstallfile=NDP471-KB4033344-Web.exe"
   set netfxregkey="0x709fc"
   goto logtimestamp
   
   :NDP472
   set "netfxinstallfile=NDP472-KB4054531-Web.exe"
   set netfxregkey="0x70BF6"
   goto logtimestamp
   
   :logtimestamp
   REM ***** Setup LogFile with timestamp *****
   md "%PathToNETFXInstall%\log"
   set startuptasklog="%PathToNETFXInstall%log\startuptasklog-%timestamp%.txt"
   set netfxinstallerlog="%PathToNETFXInstall%log\NetFXInstallerLog-%timestamp%"
   echo %log% >> %startuptasklog%
   echo Logfile generated at: %startuptasklog% >> %startuptasklog%
   echo TMP set to: %TMP% >> %startuptasklog%
   echo TEMP set to: %TEMP% >> %startuptasklog%
   
   REM ***** Check if .NET is installed *****
   echo Checking if .NET (%netfx%) is installed >> %startuptasklog%
   set /A netfxregkeydecimal=%netfxregkey%
   set foundkey=0
   FOR /F "usebackq skip=2 tokens=1,2*" %%A in (`reg query "HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\NET Framework Setup\NDP\v4\Full" /v Release 2^>nul`) do @set /A foundkey=%%C
   echo Minimum required key: %netfxregkeydecimal% -- found key: %foundkey% >> %startuptasklog%
   if %foundkey% GEQ %netfxregkeydecimal% goto installed
   
   REM ***** Installing .NET *****
   echo Installing .NET with commandline: start /wait %~dp0%netfxinstallfile% /q /serialdownload /log %netfxinstallerlog%  /chainingpackage "CloudService Startup Task" >> %startuptasklog%
   start /wait %~dp0%netfxinstallfile% /q /serialdownload /log %netfxinstallerlog% /chainingpackage "CloudService Startup Task" >> %startuptasklog% 2>>&1
   if %ERRORLEVEL%== 0 goto installed
       echo .NET installer exited with code %ERRORLEVEL% >> %startuptasklog%    
       if %ERRORLEVEL%== 3010 goto restart
       if %ERRORLEVEL%== 1641 goto restart
       echo .NET (%netfx%) install failed with Error Code %ERRORLEVEL%. Further logs can be found in %netfxinstallerlog% >> %startuptasklog%
       goto exit
       
   :restart
   echo Restarting to complete .NET (%netfx%) installation >> %startuptasklog%
   shutdown.exe /r /t 5 /c "Installed .NET framework" /f /d p:2:4
   
   :installed
   echo .NET (%netfx%) is installed >> %startuptasklog%
   
   :end
   echo install.cmd completed: %date:~-4,4%%date:~-10,2%%date:~-7,2%-%timehour: =0%%time:~3,2% >> %startuptasklog%
   
   :exit
   EXIT /B 0
   ```

3. Dodaj plik install.cmd do każdej roli przy użyciu **Dodaj** > **istniejący element** w **Eksploratora rozwiązań** zgodnie z opisem we wcześniejszej części tego tematu. 

    Po ukończeniu tego kroku wszystkie role powinny mieć plik Instalatora .NET i plik install.cmd.

   ![Zawartość roli przy użyciu wszystkich plików][2]

## <a name="configure-diagnostics-to-transfer-startup-logs-to-blob-storage"></a>Konfigurowanie diagnostyki dzienników uruchamiania transferu do magazynu obiektów Blob
Aby ułatwić rozwiązywanie problemów z instalacją, można skonfigurować usługi Azure Diagnostics, aby przenieść wszystkie pliki dziennika generowane przez skrypt uruchamiania lub Instalatora .NET w usłudze Azure Blob storage. Za pomocą tego podejścia, możesz wyświetlić dzienniki przez pobranie plików dziennika z magazynu obiektów Blob, niż musieć go pulpitu zdalnego do roli.


Aby skonfigurować diagnostykę, otwórz plik diagnostics.wadcfgx i dodaj następującą zawartość w obszarze **katalogi** węzła: 

```xml 
<DataSources>
 <DirectoryConfiguration containerName="netfx-install">
  <LocalResource name="NETFXInstall" relativePath="log"/>
 </DirectoryConfiguration>
</DataSources>
```

Poniższy kod XML konfiguruje diagnostyki do transferu plików w katalogu dzienników w **NETFXInstall** zasobów do konta magazynu diagnostyki w **netfx instalacji** kontenera obiektów blob.

## <a name="deploy-your-cloud-service"></a>Wdrażanie usługi w chmurze
Podczas wdrażania usługi w chmurze, jeśli jeszcze nie zainstalowano zadania uruchamiania zainstalować program .NET Framework. Role usługi w chmurze znajdują się w *zajęty* stan podczas instalowania programu framework. Jeśli instalacja framework wymaga ponownego uruchomienia, ról usługi również może zostać ponownie uruchomiony. 

## <a name="additional-resources"></a>Dodatkowe zasoby
* [Instalowanie programu .NET Framework][Installing the .NET Framework]
* [Określanie, które wersje programu .NET Framework są zainstalowane.][How to: Determine Which .NET Framework Versions Are Installed]
* [Rozwiązywanie problemów z instalacją .NET Framework][Troubleshooting .NET Framework Installations]

[How to: Determine Which .NET Framework Versions Are Installed]: /dotnet/framework/migration-guide/how-to-determine-which-versions-are-installed
[Installing the .NET Framework]: /dotnet/framework/install/guide-for-developers
[Troubleshooting .NET Framework Installations]: /dotnet/framework/install/troubleshoot-blocked-installations-and-uninstallations

<!--Image references-->
[1]: ./media/cloud-services-dotnet-install-dotnet/rolecontentwithinstallerfiles.png
[2]: ./media/cloud-services-dotnet-install-dotnet/rolecontentwithallfiles.png
