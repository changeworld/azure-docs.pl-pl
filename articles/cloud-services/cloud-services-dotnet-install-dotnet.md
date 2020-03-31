---
title: Instalowanie ról platformy .NET w usługach w chmurze platformy Azure | Dokumenty firmy Microsoft
description: W tym artykule opisano sposób ręcznego instalowania programu .NET Framework w sieci web usługi w chmurze i ról procesu roboczego
services: cloud-services
documentationcenter: .net
author: tgore03
manager: carmonm
ms.service: cloud-services
ms.devlang: dotnet
ms.topic: article
ms.date: 06/22/2018
ms.author: tagore
ms.openlocfilehash: c830dc0ee38ad808579a62274e3db87d0696e099
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79214725"
---
# <a name="install-net-on-azure-cloud-services-roles"></a>Instalowanie ról platformy .NET w usługach w chmurze platformy Azure
W tym artykule opisano sposób instalowania wersji programu .NET Framework, które nie są dostępne w przypadku systemu operacyjnego gościa platformy Azure. Za pomocą platformy .NET w systemu operacyjnego gościa można skonfigurować role sieci web usługi w chmurze i procesowy.

Na przykład można zainstalować program .NET Framework 4.6.2 w rodzinie systemu operacyjnego gościa 4, który nie jest wyposażony w żadną wersję programu .NET Framework 4.6. (Rodzina systemu operacyjnego gościa 5 jest wyposażona w program .NET Framework 4.6). Aby uzyskać najnowsze informacje na temat wydań systemu operacyjnego azure guest, zobacz [wiadomości o wydaniu systemu operacyjnego usługi Azure Guest.](cloud-services-guestos-update-matrix.md) 

>[!IMPORTANT]
>Zestaw Azure SDK 2.9 zawiera ograniczenie dotyczące wdrażania programu .NET Framework 4.6 w rodzinie 4 gościa systemu operacyjnego 4 lub wcześniejszego. Poprawka dotycząca ograniczenia jest dostępna w witrynie [Microsoft Docs.](https://github.com/MicrosoftDocs/azure-cloud-services-files/tree/master/Azure%20Targets%20SDK%202.9)

Aby zainstalować .NET na role sieci web i procesu roboczego, należy dołączyć instalatora sieci web platformy .NET jako część projektu usługi w chmurze. Uruchom instalatora jako część zadań uruchamiania roli. 

## <a name="add-the-net-installer-to-your-project"></a>Dodawanie instalatora platformy .NET do projektu
Aby pobrać instalatora sieci Web programu .NET Framework, wybierz wersję, którą chcesz zainstalować:

* [Instalator sieci Web programu .NET Framework 4.8](https://dotnet.microsoft.com/download/thank-you/net48)
* [Instalator sieci Web programu .NET Framework 4.7.2](https://go.microsoft.com/fwlink/?LinkId=863262)
* [Instalator sieci Web programu .NET Framework 4.6.2](https://www.microsoft.com/download/details.aspx?id=53345)

Aby dodać instalatora dla roli *sieci Web:*
  1. W **Eksploratorze rozwiązań**w obszarze **Role** w projekcie usługi w chmurze kliknij prawym przyciskiem myszy rolę *sieci Web* i wybierz polecenie **Dodaj** > nowy**folder**. Tworzenie folderu o nazwie **bin**.
  2. Kliknij prawym przyciskiem myszy folder pojemnika i wybierz polecenie **Dodaj** > **istniejący element**. Wybierz instalator .NET i dodaj go do folderu pojemnika.
  
Aby dodać instalatora dla roli *procesu roboczego:*
* Kliknij prawym przyciskiem myszy rolę *pracownika* i wybierz polecenie **Dodaj** > **istniejący element**. Wybierz instalator .NET i dodaj go do roli. 

Gdy pliki są dodawane w ten sposób do folderu zawartości roli, są automatycznie dodawane do pakietu usługi w chmurze. Pliki są następnie wdrażane w spójnej lokalizacji na maszynie wirtualnej. Powtórz ten proces dla każdej roli sieci web i procesu roboczego w usłudze w chmurze, tak aby wszystkie role miały kopię instalatora.

> [!NOTE]
> Należy zainstalować program .NET Framework 4.6.2 w roli usługi w chmurze, nawet jeśli aplikacja jest przeznaczona dla platformy .NET Framework 4.6. System operacyjny gościa zawiera aktualizację bazy wiedzy [3098779](https://support.microsoft.com/kb/3098779) i [aktualizację 3097997.](https://support.microsoft.com/kb/3097997) Problemy mogą wystąpić po uruchomieniu aplikacji platformy .NET, jeśli program .NET Framework 4.6 jest zainstalowany na aktualizacjach bazy wiedzy Knowledge Base. Aby uniknąć tych problemów, należy zainstalować program .NET Framework 4.6.2 zamiast wersji 4.6. Aby uzyskać więcej informacji, zobacz [artykuł 3118750 bazy wiedzy](https://support.microsoft.com/kb/3118750) i [4340191](https://support.microsoft.com/kb/4340191).
> 
> 

![Zawartość roli z plikami instalacyjnymi][1]

## <a name="define-startup-tasks-for-your-roles"></a>Definiowanie zadań uruchamiania dla ról
Zadania uruchamiania można używać do wykonywania operacji przed rozpoczęciem roli. Zainstalowanie programu .NET Framework w ramach zadania uruchamiania gwarantuje, że struktura jest zainstalowana przed uruchomieniem kodu aplikacji. Aby uzyskać więcej informacji na temat zadań uruchamiania, zobacz [Uruchamianie zadań uruchamiania na platformie Azure](cloud-services-startup-tasks.md). 

1. Dodaj następującą zawartość do pliku ServiceDefinition.csdef w węźle **WebRole** lub **WorkerRole** dla wszystkich ról:
   
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
   
    Poprzednia konfiguracja uruchamia `install.cmd` polecenie konsoli z uprawnieniami administratora w celu zainstalowania programu .NET Framework. Konfiguracja tworzy również element **LocalStorage** o nazwie **NETFXInstall**. Skrypt startowy ustawia folder tymczasowy do używania tego zasobu magazynu lokalnego. 
    
    > [!IMPORTANT]
    > Aby zapewnić prawidłową instalację struktury, należy ustawić rozmiar tego zasobu na co najmniej 1024 MB.
    
    Aby uzyskać więcej informacji o zadaniach uruchamiania, zobacz [Typowe zadania uruchamiania usług Azure Cloud Services](cloud-services-startup-tasks-common.md).

2. Utwórz plik o nazwie **install.cmd** i dodaj do pliku następujący skrypt instalacji.

   Skrypt sprawdza, czy określona wersja programu .NET Framework jest już zainstalowana na komputerze, przeprowadzając kwerendę w rejestrze. Jeśli wersja programu .NET Framework nie jest zainstalowana, zostanie otwarty instalator sieci web programu .NET Framework. Aby pomóc w rozwiązaniu problemów, skrypt rejestruje wszystkie działania do pliku startuptasklog-(bieżąca data i godzina).txt, który jest przechowywany w **InstallLogs** magazynu lokalnego.
   
   > [!IMPORTANT]
   > Użyj podstawowego edytora tekstu, takiego jak Notatnik systemu Windows, aby utworzyć plik install.cmd. Jeśli używasz programu Visual Studio do utworzenia pliku tekstowego i zmiany rozszerzenia na cmd, plik może nadal zawierać znacznik kolejności bajtów UTF-8. Ten znacznik może spowodować błąd podczas uruchamiania pierwszego wiersza skryptu. Aby uniknąć tego błędu, należy wykonać pierwszy wiersz skryptu REM instrukcji, które mogą być pomijane przez przetwarzanie kolejności bajtów. 
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

3. Dodaj plik install.cmd do każdej roli przy użyciu **funkcji Dodaj** > **istniejący element** w **Eksploratorze rozwiązań,** zgodnie z opisem opisanym wcześniej w tym temacie. 

    Po zakończeniu tego kroku wszystkie role powinny mieć plik instalatora .NET i plik install.cmd.

   ![Zawartość roli ze wszystkimi plikami][2]

## <a name="configure-diagnostics-to-transfer-startup-logs-to-blob-storage"></a>Konfigurowanie diagnostyki do przenoszenia dzienników uruchamiania do magazynu obiektów Blob
Aby uprościć rozwiązywanie problemów z instalacją, można skonfigurować usługę Azure Diagnostics, aby przenieść wszystkie pliki dziennika wygenerowane przez skrypt startowy lub instalatora platformy .NET do magazynu obiektów Blob platformy Azure. Za pomocą tej metody, można wyświetlić dzienniki, pobierając pliki dziennika z magazynu obiektów Blob, a nie konieczności zdalnego pulpitu do roli.


Aby skonfigurować diagnostykę, otwórz plik diagnostics.wadcfgx i dodaj następującą zawartość w węźle **Katalogi:** 

```xml 
<DataSources>
 <DirectoryConfiguration containerName="netfx-install">
  <LocalResource name="NETFXInstall" relativePath="log"/>
 </DirectoryConfiguration>
</DataSources>
```

Ten xml konfiguruje diagnostykę do przesyłania plików w katalogu dziennika w zasobie **NETFXInstall** do konta magazynu diagnostyki w kontenerze obiektów blob **netfx-install.**

## <a name="deploy-your-cloud-service"></a>Wdrażanie usługi w chmurze
Podczas wdrażania usługi w chmurze zadania uruchamiania zainstalować .NET Framework, jeśli nie jest jeszcze zainstalowany. Role usługi w chmurze są w stanie *zajęty,* podczas gdy struktura jest instalowana. Jeśli instalacja struktury wymaga ponownego uruchomienia, role usługi może również ponownie uruchomić. 

## <a name="additional-resources"></a>Zasoby dodatkowe
* [Instalowanie programu .NET Framework][Installing the .NET Framework]
* [Określanie, które wersje programu .NET Framework są zainstalowane][How to: Determine Which .NET Framework Versions Are Installed]
* [Rozwiązywanie problemów z instalacjami programu .NET Framework][Troubleshooting .NET Framework Installations]

[How to: Determine Which .NET Framework Versions Are Installed]: /dotnet/framework/migration-guide/how-to-determine-which-versions-are-installed
[Installing the .NET Framework]: /dotnet/framework/install/guide-for-developers
[Troubleshooting .NET Framework Installations]: /dotnet/framework/install/troubleshoot-blocked-installations-and-uninstallations

<!--Image references-->
[1]: ./media/cloud-services-dotnet-install-dotnet/rolecontentwithinstallerfiles.png
[2]: ./media/cloud-services-dotnet-install-dotnet/rolecontentwithallfiles.png



