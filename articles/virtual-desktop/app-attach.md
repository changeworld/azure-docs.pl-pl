---
title: Dołączanie aplikacji MSIX dla pulpitu wirtualnego systemu Windows — platforma Azure
description: Jak skonfigurować aplikację MSIX dołącz dla pulpitu wirtualnego systemu Windows.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 12/14/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: 20a82cbd7de4b5678648bac19ab9b59bf557b0ff
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79128320"
---
# <a name="set-up-msix-app-attach"></a>Konfigurowanie dołączania aplikacji MSIX

> [!IMPORTANT]
> Msix app attach jest obecnie w publicznej wersji zapoznawczej.
> Ta wersja zapoznawcza nie jest objęta umową dotyczącą poziomu usług i nie zalecamy korzystania z niej w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone. Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

W tym temacie opisano sposób konfigurowania aplikacji MSIX dołączania w środowisku pulpitu wirtualnego systemu Windows.

## <a name="requirements"></a>Wymagania

Zanim zaczniesz, oto, co należy skonfigurować do dołączania aplikacji MSIX:

- Dostęp do portalu niejawnego programu testów systemu Windows w celu uzyskania wersji systemu Windows 10 z obsługą aplikacji MSIX dołącz interfejsy API.
- Działające wdrożenie pulpitu wirtualnego systemu Windows. Aby uzyskać więcej informacji, zobacz [Tworzenie dzierżawy na pulpicie wirtualnym systemu Windows](tenant-setup-azure-active-directory.md).
- Narzędzie do pakowania MSIX
- Udział sieciowy we wdrożeniu pulpitu wirtualnego systemu Windows, w którym pakiet MSIX będzie przechowywany

## <a name="get-the-os-image"></a>Pobierz obraz systemu operacyjnego

Najpierw musisz uzyskać obraz systemu operacyjnego, którego użyjesz w aplikacji MSIX. Aby uzyskać obraz systemu operacyjnego:

1. Otwórz [portal niejawnych testerów systemu Windows](https://www.microsoft.com/software-download/windowsinsiderpreviewadvanced?wa=wsignin1.0) i zaloguj się.

     >[!NOTE]
     >Aby uzyskać dostęp do portalu niejawnych testów systemu Windows, musisz być członkiem niejawnego programu testów systemu Windows. Aby dowiedzieć się więcej o niejawnym programie testów systemu Windows, zapoznaj się z naszą [dokumentacją niejawnego programu testów systemu Windows.](/windows-insider/at-home/)

2. Przewiń w dół do sekcji **Wybierz wersję** i wybierz pozycję **Windows 10 Insider Preview Enterprise (FAST) — Kompilacja 19035** lub nowsza.

3. Wybierz **pozycję Potwierdź**, a następnie wybierz język, którego chcesz użyć, a następnie wybierz ponownie pozycję **Potwierdź.**
    
     >[!NOTE]
     >W tej chwili angielski jest jedynym językiem, który został przetestowany z funkcją. Można wybrać inne języki, ale mogą one nie być wyświetlane zgodnie z przeznaczeniem.
    
4. Po wygenerowaniu łącza pobierania wybierz **64-bitowy download** i zapisz go na lokalnym dysku twardym.

## <a name="prepare-the-vhd-image-for-azure"></a>Przygotowywanie obrazu VHD dla platformy Azure 

Zanim zaczniesz, musisz utworzyć główny obraz VHD. Jeśli nie utworzono jeszcze głównego obrazu VHD, przejdź do [strony Przygotuj i dostosuj główny obraz VHD](set-up-customize-master-image.md) i postępuj zgodnie z instrukcjami. 

Po utworzeniu głównego obrazu VHD należy wyłączyć automatyczne aktualizacje aplikacji MSIX dołączania aplikacji. Aby wyłączyć aktualizacje automatyczne, musisz uruchomić następujące polecenia w wierszu polecenia z podwyższonym poziomem uprawnień:

```cmd
rem Disable Store auto update:

reg add HKLM\Software\Policies\Microsoft\WindowsStore /v AutoDownload /t REG_DWORD /d 0 /f
Schtasks /Change /Tn "\Microsoft\Windows\WindowsUpdate\Automatic app update" /Disable
Schtasks /Change /Tn "\Microsoft\Windows\WindowsUpdate\Scheduled Start" /Disable

rem Disable Content Delivery auto download apps that they want to promote to users:

reg add HKCU\Software\Microsoft\Windows\CurrentVersion\ContentDeliveryManager /v PreInstalledAppsEnabled /t REG_DWORD /d 0 /f

reg add HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\ContentDeliveryManager\Debug /v ContentDeliveryAllowedOverride /t REG_DWORD /d 0x2 /f

rem Disable Windows Update:

sc config wuauserv start=disabled
```

Następnie przygotuj wirtualną maszynę wirtualną dla platformy Azure i przekaż wynikowy dysk VHD na platformę Azure. Aby dowiedzieć się więcej, zobacz [Przygotowywanie i dostosowywanie głównego obrazu VHD](set-up-customize-master-image.md).

Po przekazaniu dysku VHD na platformę Azure utwórz pulę hostów opartą na tym nowym obrazie, postępując zgodnie z instrukcjami w [puli tworzenia hosta przy użyciu samouczka portalu Azure Marketplace.](create-host-pools-azure-marketplace.md)

## <a name="prepare-the-application-for-msix-app-attach"></a>Przygotowanie wniosku do aplikacji MSIX dołączyć 

Jeśli masz już pakiet MSIX, przejdź do przodu, [aby skonfigurować infrastrukturę pulpitu wirtualnego systemu Windows](#configure-windows-virtual-desktop-infrastructure). Jeśli chcesz przetestować starsze aplikacje, postępuj zgodnie z instrukcjami w [Tworzenie pakietu MSIX z instalatora pulpitu na maszynie Wirtualnej,](/windows/msix/packaging-tool/create-app-package-msi-vm/) aby przekonwertować starszą aplikację na pakiet MSIX.

## <a name="generate-a-vhd-or-vhdx-package-for-msix"></a>Generowanie pakietu VHD lub VHDX dla MSIX

Pakiety są w formacie VHD lub VHDX w celu optymalizacji wydajności. MSIX wymaga pakietów VHD lub VHDX do poprawnego działania.

Aby wygenerować pakiet VHD lub VHDX dla MSIX:

1. [Pobierz narzędzie msixmgr](https://aka.ms/msixmgr) i zapisz folder .zip w folderze w ramach maszyny wirtualnej hosta sesji.

2. Rozpakuj folder .zip narzędzia msixmgr.

3. Umieść źródłowy pakiet MSIX w tym samym folderze, w którym rozpakowane jest narzędzie msixmgr.

4. Uruchom następujące polecenie cmdlet w programie PowerShell, aby utworzyć dysk VHD:

    ```powershell
    New-VHD -SizeBytes <size>MB -Path c:\temp\<name>.vhd -Dynamic -Confirm:$false
    ```

    >[!NOTE]
    >Upewnij się, że rozmiar dysku VHD jest wystarczająco duży, aby pomieścić rozszerzony msix.*

5. Uruchom następujące polecenie cmdlet, aby zamontować nowo utworzony dysk VHD:

    ```powershell
    $vhdObject = Mount-VHD c:\temp\<name>.vhd -Passthru
    ```

6. Uruchom to polecenie cmdlet, aby zainicjować dysk VHD:

    ```powershell
    $disk = Initialize-Disk -Passthru -Number $vhdObject.Number
    ```

7. Uruchom to polecenie cmdlet, aby utworzyć nową partycję:

    ```powershell
    $partition = New-Partition -AssignDriveLetter -UseMaximumSize -DiskNumber $disk.Number
    ```

8. Uruchom to polecenie cmdlet, aby sformatować partycję:

    ```powershell
    Format-Volume -FileSystem NTFS -Confirm:$false -DriveLetter $partition.DriveLetter -Force
    ```

9. Utwórz folder nadrzędny na zamontowanym dysku VHD. Ten krok jest obowiązkowy, ponieważ dołączanie aplikacji MSIX wymaga folderu nadrzędnego. Możesz nazwać folder nadrzędny, co chcesz.

### <a name="expand-msix"></a>Rozwiń MSIX

Następnie musisz "rozwinąć" obraz MSIX, rozpakowując go. Aby rozpakować obraz MSIX:

1. Otwórz wiersz polecenia jako administrator i przejdź do folderu, w którym pobrano i rozpakowałeś narzędzie msixmgr.

2. Uruchom następujące polecenie cmdlet, aby rozpakować MSIX do dysku VHD utworzonego i zamontowanego w poprzedniej sekcji.

    ```powershell
    msixmgr.exe -Unpack -packagePath <package>.msix -destination "f:\<name of folder you created earlier>" -applyacls
    ```

    Po zakończeniu rozpakowywania powinien pojawić się następujący komunikat:

    `Successfully unpacked and applied ACLs for package: <package name>.msix`

    >[!NOTE]
    > Jeśli używasz pakietów ze sklepu Microsoft Store dla firm (lub edukacji) w sieci lub na urządzeniach, które nie są połączone z Internetem, musisz uzyskać licencje na pakiety ze Sklepu i zainstalować je, aby pomyślnie uruchomić aplikację. Zobacz [Korzystanie z pakietów w trybie offline](#use-packages-offline).

3. Przejdź do zainstalowanego dysku VHD i otwórz folder aplikacji i upewnij się, że zawartość pakietu jest obecna.

4. Odinstaluj dysk VHD.

## <a name="configure-windows-virtual-desktop-infrastructure"></a>Konfigurowanie infrastruktury pulpitu wirtualnego systemu Windows

Zgodnie z projektem pojedynczy rozszerzony pakiet MSIX (dysk VHD utworzony w poprzedniej sekcji) może być współużytkowany przez maszyny wirtualne wielu hostów sesji, ponieważ dyski VHD są dołączone w trybie tylko do odczytu.

Przed rozpoczęciem upewnij się, że udział sieciowy spełnia następujące wymagania:

- Udział jest zgodny z SMB.
- Maszyny wirtualne, które są częścią puli hostów sesji mają uprawnienia NTFS do udziału.

### <a name="set-up-an-msix-app-attach-share"></a>Konfigurowanie udziału dołączania aplikacji MSIX 

W środowisku pulpitu wirtualnego systemu Windows utwórz udział sieciowy i przenieś pakiet.

>[!NOTE]
> Najlepszym rozwiązaniem w zakresie tworzenia udziałów sieciowych MSIX jest skonfigurowanie udziału sieciowego z uprawnieniami tylko do odczytu ntfs.

## <a name="install-certificates"></a>Instalowanie certyfikatów

Jeśli aplikacja korzysta z certyfikatu, który nie jest zaufany publicznie lub był podpisany samodzielnie, oto jak go zainstalować:

1. Kliknij prawym przyciskiem myszy pakiet i wybierz polecenie **Właściwości**.
2. W wyświetlonym oknie wybierz kartę **Podpisy cyfrowe.** Na karcie powinna znajdować się tylko jedna pozycja, jak pokazano na poniższej ilustracji. Zaznacz ten element, aby wyróżnić element, a następnie wybierz pozycję **Szczegóły**.
3. Po wyświetleniu okna szczegółów podpisu cyfrowego wybierz kartę **Ogólne,** a następnie wybierz pozycję **Zainstaluj certyfikat**.
4. Po otwarciu instalatora wybierz **komputer lokalny** jako lokalizację magazynowania, a następnie wybierz pozycję **Dalej**.
5. Jeśli instalator zapyta, czy chcesz zezwolić aplikacji na wprowadzanie zmian na urządzeniu, wybierz opcję **Tak**.
6. Wybierz **pozycję Umieść wszystkie certyfikaty w następującym magazynie,** a następnie wybierz pozycję **Przeglądaj**.
7. Po wyświetleniu okna magazynu wybranych certyfikatów wybierz pozycję **Zaufane osoby,** a następnie wybierz przycisk **OK**.
8. Wybierz **pozycję Zakończ**.

## <a name="prepare-powershell-scripts-for-msix-app-attach"></a>Przygotowywanie skryptów programu PowerShell dla dołączania aplikacji MSIX

Msix app attach ma cztery różne fazy, które muszą być wykonywane w następującej kolejności:

1. Etap
2. Zarejestruj
3. Wyrejestrować
4. Destage (Destage)

Każda faza tworzy skrypt programu PowerShell. Przykładowe skrypty dla każdej fazy są dostępne [tutaj](https://github.com/Azure/RDS-Templates/tree/master/msix-app-attach).

### <a name="stage-the-powershell-script"></a>Przygotowanie skryptu programu PowerShell

Przed zaktualizowaniem skryptów programu PowerShell upewnij się, że masz identyfikator GUID woluminu w dysku VHD. Aby uzyskać identyfikator GUID woluminu:

1.  Otwórz udział sieciowy, w którym znajduje się dysk VHD wewnątrz maszyny Wirtualnej, gdzie uruchomisz skrypt.

2.  Kliknij prawym przyciskiem myszy dysk VHD i wybierz pozycję **Mount**. Spowoduje to zamontowanie dysku VHD na literze dysku.

3.  Po zainstalowaniu dysku VHD otworzy się okno **Eksploratora plików.** Przechwytywanie folderu nadrzędnego i aktualizowanie zmiennej **$parentFolder**

    >[!NOTE]
    >Jeśli nie widzisz folderu nadrzędnego, oznacza to, że MSIX nie został poprawnie rozwinięty. Wykonaj ponownie poprzednią sekcję i spróbuj ponownie.

4.  Otwórz folder nadrzędny. Jeśli zostanie poprawnie rozwinięty, zobaczysz folder o takiej samej nazwie jak pakiet. Zaktualizuj zmienną **$packageName,** aby była zgodna z nazwą tego folderu.

    Na przykład `VSCodeUserSetup-x64-1.38.1_1.38.1.0_x64__8wekyb3d8bbwe`.

5.  Otwórz wiersz polecenia i wprowadź **mountvol**. To polecenie wyświetli listę woluminów i ich identyfikatorów GUID. Skopiuj identyfikator GUID woluminu, w którym litera dysku pasuje do dysku zainstalowanego vhd w kroku 2.

    Na przykład w tym przykładzie danych wyjściowych dla polecenia mountvol, jeśli został zamontowany dysk `C:\`VHD na dysku C, należy skopiować powyższą wartość:

    ```cmd
    Possible values for VolumeName along with current mount points are:

    \\?\Volume{a12b3456-0000-0000-0000-10000000000}\
    *** NO MOUNT POINTS ***

    \\?\Volume{c78d9012-0000-0000-0000-20000000000}\
        E:\

    \\?\Volume{d34e5678-0000-0000-0000-30000000000}\
        C:\

    ```


6.  Zaktualizuj zmienną **$volumeGuid** za pomocą właśnie skopiowanego identyfikatora GUID woluminu.

7. Otwórz monit programu Admin PowerShell i zaktualizuj następujący skrypt programu PowerShell ze zmiennymi stosowanymi do środowiska.

    ```powershell
    #MSIX app attach staging sample

    #region variables

    $vhdSrc="<path to vhd>"

    $packageName = "<package name>"

    $parentFolder = "<package parent folder>"

    $parentFolder = "\" + $parentFolder + "\"

    $volumeGuid = "<vol guid>"

    $msixJunction = "C:\temp\AppAttach\"

    #endregion

    #region mountvhd

    try

    {

    Mount-Diskimage -ImagePath $vhdSrc -NoDriveLetter -Access ReadOnly

    Write-Host ("Mounting of " + $vhdSrc + " was completed!") -BackgroundColor Green

    }

    catch

    {

    Write-Host ("Mounting of " + $vhdSrc + " has failed!") -BackgroundColor Red

    }

    #endregion

    #region makelink

    $msixDest = "\\?\Volume{" + $volumeGuid + "}\"

    if (!(Test-Path $msixJunction))

    {

    md $msixJunction

    }

    $msixJunction = $msixJunction + $packageName

    cmd.exe /c mklink /j $msixJunction $msixDest

    #endregion

    #region stage

    [Windows.Management.Deployment.PackageManager,Windows.Management.Deployment,ContentType=WindowsRuntime]
    | Out-Null

    Add-Type -AssemblyName System.Runtime.WindowsRuntime

    $asTask = ([System.WindowsRuntimeSystemExtensions].GetMethods() | Where {
    $_.ToString() -eq 'System.Threading.Tasks.Task`1[TResult]
    AsTask[TResult,TProgress](Windows.Foundation.IAsyncOperationWithProgress`2[TResult,TProgress])'})[0]

    $asTaskAsyncOperation =
    $asTask.MakeGenericMethod([Windows.Management.Deployment.DeploymentResult],
    [Windows.Management.Deployment.DeploymentProgress])

    $packageManager = [Windows.Management.Deployment.PackageManager]::new()

    $path = $msixJunction + $parentFolder + $packageName # needed if we do the pbisigned.vhd

    $path = ([System.Uri]$path).AbsoluteUri

    $asyncOperation = $packageManager.StagePackageAsync($path, $null, "StageInPlace")

    $task = $asTaskAsyncOperation.Invoke($null, @($asyncOperation))

    $task

    #endregion
    ```

### <a name="register-powershell-script"></a>Rejestrowanie skryptu programu PowerShell

Aby uruchomić skrypt rejestru, uruchom następujące polecenia cmdlet programu PowerShell z wartościami zastępczymi zastąpionymi wartościami, które mają zastosowanie do środowiska.

```powershell
#MSIX app attach registration sample

#region variables

$packageName = "<package name>"

$path = "C:\Program Files\WindowsApps\" + $packageName + "\AppxManifest.xml"

#endregion

#region register

Add-AppxPackage -Path $path -DisableDevelopmentMode -Register

#endregion
```

### <a name="deregister-powershell-script"></a>Wyrejestrowanie skryptu programu PowerShell

W przypadku tego skryptu zastąp symbol zastępczy **dla $packageName** nazwą testowego pakietu.

```powershell
#MSIX app attach deregistration sample

#region variables

$packageName = "<package name>"

#endregion

#region deregister

Remove-AppxPackage -PreserveRoamableApplicationData $packageName

#endregion
```

### <a name="destage-powershell-script"></a>Skrypt Destage PowerShell

W przypadku tego skryptu zastąp symbol zastępczy **dla $packageName** nazwą testowego pakietu.

```powershell
#MSIX app attach de staging sample

#region variables

$packageName = "<package name>"

$msixJunction = "C:\temp\AppAttach\"

#endregion

#region deregister

Remove-AppxPackage -AllUsers -Package $packageName

cd $msixJunction

rmdir $packageName -Force -Verbose

#endregion
```

## <a name="set-up-simulation-scripts-for-the-msix-app-attach-agent"></a>Konfigurowanie skryptów symulacyjnych dla agenta dołączania aplikacji MSIX

Po utworzeniu skryptów użytkownicy mogą je ręcznie uruchomić lub skonfigurować tak, aby były uruchamiane automatycznie jako skrypty uruchamiania, logowania, wylogowywania i zamykania. Aby dowiedzieć się więcej o tych typach skryptów, zobacz [Używanie skryptów uruchamiania, zamykania, logowania i wylogowywania w zasadach grupy](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/dn789196(v=ws.11)/).

Każdy z tych skryptów automatycznych uruchamia jedną fazę dołączania skryptów aplikacji:

- Skrypt startowy uruchamia skrypt etapu.
- Skrypt logowania uruchamia skrypt rejestru.
- Skrypt wylogowywania uruchamia skrypt wyrejestrowania.
- Skrypt zamknięcia uruchamia skrypt destage.

## <a name="use-packages-offline"></a>Korzystanie z pakietów w trybie offline

Jeśli korzystasz z pakietów ze [sklepu Microsoft Store dla firm](https://businessstore.microsoft.com/) lub ze sklepu Microsoft Store dla szkół [wyższych](https://educationstore.microsoft.com/) w sieci lub na urządzeniach, które nie są połączone z Internetem, musisz uzyskać licencje na pakiety ze sklepu Microsoft Store i zainstalować je na urządzeniu, aby pomyślnie uruchomić aplikację. Jeśli urządzenie jest w trybie online i może łączyć się ze sklepem Microsoft Store dla firm, wymagane licencje powinny być pobierane automatycznie, ale jeśli jesteś w trybie offline, musisz ręcznie skonfigurować licencje. 

Aby zainstalować pliki licencji, musisz użyć skryptu programu PowerShell, który wywołuje klasę MDM_EnterpriseModernAppManagement_StoreLicenses02_01 w dostawcy mostka WMI.  

Aby skonfigurować licencje do użytku w trybie offline: 

1. Pobierz pakiet aplikacji, licencje i wymagane struktury ze sklepu Microsoft Store dla firm. Potrzebne są zarówno zakodowane, jak i niekodowane pliki licencji. Szczegółowe instrukcje pobierania można znaleźć [tutaj](/microsoft-store/distribute-offline-apps#download-an-offline-licensed-app).
2. Zaktualizuj następujące zmienne w skrypcie dla kroku 3:
      1. `$contentID`jest wartością ContentID z pliku licencji Unencoded (xml). Plik licencji można otworzyć w wybranym edytorze tekstu.
      2. `$licenseBlob`to cały ciąg obiektu blob licencji w zakodowanym pliku licencji (.bin). Zakodowany plik licencji można otworzyć w wybranym edytorze tekstu. 
3. Uruchom następujący skrypt z monitu programu Admin PowerShell. Dobrym miejscem do wykonania instalacji licencji jest na końcu [skryptu przemieszczania,](#stage-the-powershell-script) który również musi być uruchomiony z monitu administratora.

```powershell
$namespaceName = "root\cimv2\mdm\dmmap"
$className = "MDM_EnterpriseModernAppManagement_StoreLicenses02_01"
$methodName = "AddLicenseMethod"
$parentID = "./Vendor/MSFT/EnterpriseModernAppManagement/AppLicenses/StoreLicenses"

#TODO - Update $contentID with the ContentID value from the unencoded license file (.xml)
$contentID = "{'ContentID'_in_unencoded_license_file}"

#TODO - Update $licenseBlob with the entire String in the encoded license file (.bin)
$licenseBlob = "{Entire_String_in_encoded_license_file}"

$session = New-CimSession 

#The final string passed into the AddLicenseMethod should be of the form <License Content="encoded license blob" />
$licenseString = '<License Content='+ '"' + $licenseBlob +'"' + ' />' 

$params = New-Object Microsoft.Management.Infrastructure.CimMethodParametersCollection
$param = [Microsoft.Management.Infrastructure.CimMethodParameter]::Create("param",$licenseString ,"String", "In")
$params.Add($param) 


try
{
   $instance = New-CimInstance -Namespace $namespaceName -ClassName $className -Property @{ParentID=$parentID;InstanceID=$contentID}
   $session.InvokeMethod($namespaceName, $instance, $methodName, $params)

}
catch [Exception]
{
     write-host $_ | out-string
}  
```

## <a name="next-steps"></a>Następne kroki

Ta funkcja nie jest obecnie obsługiwana, ale możesz zadawać pytania społeczności w [programie Windows Virtual Desktop TechCommunity](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop).

Opinie dla pulpitu wirtualnego systemu Windows można również przesyłać w [centrum opinii pulpitu wirtualnego systemu Windows](https://aka.ms/MRSFeedbackHub)lub przesyłać opinie dotyczące aplikacji MSIX i narzędzia do pakowania w centrum opinii [msix dołączającego](https://aka.ms/msixappattachfeedback) i [centrum opinii narzędzia do pakowania MSIX.](https://aka.ms/msixtoolfeedback)
