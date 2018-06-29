---
title: Zainstaluj program .NET w przypadku ról usługi w chmurze Azure | Dokumentacja firmy Microsoft
description: W tym artykule opisano, jak ręcznie zainstaluj program .NET Framework na role usługi w chmurze sieci web i proces roboczy
services: cloud-services
documentationcenter: .net
author: thraka
manager: timlt
editor: ''
ms.assetid: 8d1243dc-879c-4d1f-9ed0-eecd1f6a6653
ms.service: cloud-services
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/22/2018
ms.author: adegeo
ms.openlocfilehash: 8bd146f5265e499c38b016ea8d8cb00685867cd3
ms.sourcegitcommit: 150a40d8ba2beaf9e22b6feff414f8298a8ef868
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/27/2018
ms.locfileid: "37036051"
---
# <a name="install-net-on-azure-cloud-services-roles"></a>Zainstaluj program .NET w przypadku ról usługi w chmurze Azure
W tym artykule opisano sposób instalowania wersji programu .NET Framework, które nie pochodzą z systemu operacyjnego gościa Azure. Aby skonfigurować role usługi w chmurze sieci web i proces roboczy, można użyć .NET na systemu operacyjnego gościa.

Na przykład można zainstalować program .NET 4.6.2 rodziny systemów operacyjnych gościa 4, które nie pochodzą z dowolną wersją .NET 4.6. (Rodziny systemów operacyjnych gościa 5 pochodzą z .NET 4.6). Aby uzyskać najnowsze informacje o wersjach systemu operacyjnego gościa Azure, zobacz [wiadomości wersji systemu operacyjnego gościa Azure](cloud-services-guestos-update-matrix.md). 

>[!IMPORTANT]
>Zestaw Azure SDK 2.9 zawiera ograniczenia dotyczące wdrażania .NET 4.6 rodziny systemów operacyjnych gościa, 4 lub starszym. Poprawka dla ograniczenia jest dostępna w [Microsoft Docs](https://github.com/MicrosoftDocs/azure-cloud-services-files/tree/master/Azure%20Targets%20SDK%202.9) lokacji.

Aby zainstalować program .NET w przypadku ról użytkownika sieci web i proces roboczy, należy uwzględnić Instalator sieci web .NET w ramach projektu usługi w chmurze. Uruchamianie Instalatora jako część zadania uruchamiania roli. 

## <a name="add-the-net-installer-to-your-project"></a>Dodaj Instalator .NET do projektu
Aby pobrać Instalatora sieci web dla programu .NET Framework, należy wybrać wersję, która ma zostać zainstalowany:

* [Instalator sieci web .NET 4.7.2](http://go.microsoft.com/fwlink/?LinkId=863262)
* [Instalator sieci web .NET 4.6.2](https://www.microsoft.com/download/details.aspx?id=53345)

Aby dodać Instalatora dla *web* roli:
  1. W **Eksploratora rozwiązań**w obszarze **ról** w projekcie usługi chmury, kliknij prawym przyciskiem myszy użytkownika *web* rolę i wybierz **Dodaj**  >  **Nowy Folder**. Utwórz folder o nazwie **bin**.
  2. Kliknij prawym przyciskiem myszy bin folder, a następnie wybierz **Dodaj** > **istniejący element**. Wybierz Instalatora .NET i dodaj go do folderu bin.
  
Aby dodać Instalatora dla *procesu roboczego* roli:
* Kliknij prawym przyciskiem myszy użytkownika *procesu roboczego* rolę i wybierz **Dodaj** > **istniejący element**. Wybierz Instalatora .NET i dodaj go do roli. 

Kiedy pliki są dodawane w ten sposób do folderu zawartości roli, są one automatycznie dodawane do pakietu usługi w chmurze. Następnie wdrażane pliki do lokalizacji spójne na maszynie wirtualnej. Powtórz ten proces dla każdej roli sieci web i proces roboczy w usłudze w chmurze, aby wszystkie role mają kopię Instalatora.

> [!NOTE]
> Należy zainstalować program .NET 4.6.2 od swojej roli usługi w chmurze, nawet wtedy, gdy jest przeznaczony dla aplikacji .NET 4.6. Systemu operacyjnego gościa zawiera bazy wiedzy Knowledge Base [aktualizacji 3098779](https://support.microsoft.com/kb/3098779) i [aktualizacji 3097997](https://support.microsoft.com/kb/3097997). Problemy mogą wystąpić przy uruchamianiu aplikacji platformy .NET, jeśli .NET 4.6 zostanie zainstalowana na aktualizacje bazy wiedzy Knowledge Base. Aby uniknąć tych problemów, należy zainstalować program .NET 4.6.2 zamiast wersji 4.6. Aby uzyskać więcej informacji, zobacz [artykułu bazy wiedzy 3118750](https://support.microsoft.com/kb/3118750) i [4340191](https://support.microsoft.com/kb/4340191).
> 
> 

![Zawartość roli z plików Instalatora][1]

## <a name="define-startup-tasks-for-your-roles"></a>Definiowanie zadań uruchomienia dla poszczególnych ról
Zadania uruchamiania służy do wykonywania operacji przed rozpoczęciem roli. Instalowanie programu .NET Framework jako część zadania uruchamiania zapewnia czy framework jest zainstalowany, przed uruchomieniem kodu aplikacji. Aby uzyskać więcej informacji na temat uruchamiania zadań, zobacz [uruchamiać zadania uruchamiania na platformie Azure](cloud-services-startup-tasks.md). 

1. Dodaj następującą zawartość do pliku ServiceDefinition.csdef **sieć Web** lub **proces roboczy** węzła dla wszystkich ról:
   
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
   
    Tej konfiguracji uruchamia polecenie konsoli `install.cmd` z uprawnieniami administratora do zainstalowania programu .NET Framework. Tworzy również konfigurację **LocalStorage** elementu o nazwie **NETFXInstall**. Skrypt uruchamiania ustawia folder tymczasowy, aby użyć tego zasobu magazynu lokalnego. 
    
    > [!IMPORTANT]
    > W celu zapewnienia prawidłowego zainstalowania platformy, należy ustawić rozmiaru tego zasobu na co najmniej 1024 MB.
    
    Aby uzyskać więcej informacji na temat uruchamiania zadań, zobacz [wspólne usługi w chmurze Azure uruchamiania zadań](cloud-services-startup-tasks-common.md).

2. Utwórz plik o nazwie **install.cmd** i Dodaj poniższy skrypt instalacji do pliku.

   Skrypt sprawdza, czy określonej wersji programu .NET Framework jest już zainstalowana na komputerze, badając rejestru. Jeśli nie zainstalowano programu .NET w wersji, Instalator sieci web .NET jest otwarty. Do rozwiązywania problemów, skrypt logowania wszystkich działań pliku startuptasklog-(bieżącą datę i godzinę) .txt są przechowywane w **InstallLogs** Magazyn lokalny.
   
   > [!IMPORTANT]
   > Umożliwia utworzenie pliku install.cmd edytorze tekstu podstawowego, takim jak Notatnik systemu Windows. Jeśli używasz programu Visual Studio Utwórz plik tekstowy i zmień rozszerzenie na .cmd plik nadal może zawierać znacznik kolejności bajtów UTF-8. Znak ten może spowodować błąd, gdy jest uruchamiany pierwszy wiersz skryptu. Aby uniknąć tego błędu, należy w pierwszym wierszu skryptu REM — instrukcja, który był pomijany przez przetwarzanie kolejności bajtów. 
   > 
   >
   
   ```cmd
   REM Set the value of netfx to install appropriate .NET Framework. 
   REM ***** To install .NET 4.5.2 set the variable netfx to "NDP452" *****
   REM ***** To install .NET 4.6 set the variable netfx to "NDP46" *****
   REM ***** To install .NET 4.6.1 set the variable netfx to "NDP461" ***** http://go.microsoft.com/fwlink/?LinkId=671729
   REM ***** To install .NET 4.6.2 set the variable netfx to "NDP462" ***** https://www.microsoft.com/download/details.aspx?id=53345
   REM ***** To install .NET 4.7 set the variable netfx to "NDP47" ***** 
   REM ***** To install .NET 4.7.1 set the variable netfx to "NDP471" ***** http://go.microsoft.com/fwlink/?LinkId=852095
   REM ***** To install .NET 4.7.2 set the variable netfx to "NDP472" ***** http://go.microsoft.com/fwlink/?LinkId=863262
   set netfx="NDP472"
   
   REM ***** Set script start timestamp *****
   set timehour=%time:~0,2%
   set timestamp=%date:~-4,4%%date:~-10,2%%date:~-7,2%-%timehour: =0%%time:~3,2%
   set "log=install.cmd started %timestamp%."
   
   REM ***** Exit script if running in Emulator *****
   if %ComputeEmulatorRunning%=="true" goto exit
   
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
   
   :restart
   echo Restarting to complete .NET (%netfx%) installation >> %startuptasklog%
   EXIT /B %ERRORLEVEL%
   
   :installed
   echo .NET (%netfx%) is installed >> %startuptasklog%
   
   :end
   echo install.cmd completed: %date:~-4,4%%date:~-10,2%%date:~-7,2%-%timehour: =0%%time:~3,2% >> %startuptasklog%
   
   :exit
   EXIT /B 0
   ```

3. Dodaj plik install.cmd do każdej roli przy użyciu **Dodaj** > **istniejący element** w **Eksploratora rozwiązań** zgodnie z opisem we wcześniejszej części tego tematu. 

    Po zakończeniu tego kroku wszystkie role powinny mieć plik Instalatora .NET i install.cmd pliku.

   ![Zawartość roli przy użyciu wszystkich plików][2]

## <a name="configure-diagnostics-to-transfer-startup-logs-to-blob-storage"></a>Skonfigurować diagnostykę do uruchomienia dzienniki transferu do magazynu obiektów Blob
Aby ułatwić rozwiązywanie problemów z instalacji, możesz skonfigurować diagnostyki Azure, aby przenieść wszystkie pliki dziennika wygenerowane przez skryptu uruchomieniowego lub Instalator .NET do magazynu obiektów Blob Azure. Przy użyciu tej metody, można wyświetlić dzienniki, pobierania plików dziennika z magazynu obiektów Blob, zamiast konieczności pulpitu zdalnego do roli.


Aby skonfigurować diagnostykę, otwórz plik diagnostics.wadcfgx i dodaj następującą zawartość w obszarze **katalogów** węzła: 

```xml 
<DataSources>
 <DirectoryConfiguration containerName="netfx-install">
  <LocalResource name="NETFXInstall" relativePath="log"/>
 </DirectoryConfiguration>
</DataSources>
```

Plik XML konfiguruje diagnostyki do transferu plików w katalogu dziennika w **NETFXInstall** zasobów na koncie magazynu diagnostyki w **netfx instalacji** kontenera obiektów blob.

## <a name="deploy-your-cloud-service"></a>Wdrażanie usługi w chmurze
Podczas wdrażania usługi w chmurze, uruchamiania zadań zainstalowania programu .NET Framework, jeśli to nie jest jeszcze zainstalowana. Role usługi w chmurze są w *zajęty* stanu podczas instalowania programu framework. Jeśli instalacja framework wymaga ponownego uruchomienia, role usługi również może być ponownie. 

## <a name="additional-resources"></a>Zasoby dodatkowe
* [Instalowanie programu .NET Framework][Installing the .NET Framework]
* [Określanie, które wersje programu .NET Framework są zainstalowane][How to: Determine Which .NET Framework Versions Are Installed]
* [Rozwiązywanie problemów z instalacja programu .NET Framework][Troubleshooting .NET Framework Installations]

[How to: Determine Which .NET Framework Versions Are Installed]: /dotnet/framework/migration-guide/how-to-determine-which-versions-are-installed
[Installing the .NET Framework]: /dotnet/framework/install/guide-for-developers
[Troubleshooting .NET Framework Installations]: /dotnet/framework/install/troubleshoot-blocked-installations-and-uninstallations

<!--Image references-->
[1]: ./media/cloud-services-dotnet-install-dotnet/rolecontentwithinstallerfiles.png
[2]: ./media/cloud-services-dotnet-install-dotnet/rolecontentwithallfiles.png
