---
title: Przygotowywanie wirtualnego dysku twardego Windows do przekazania na platformę Azure | Dokumentacja firmy Microsoft
description: Jak przygotować przed przekazaniem do usługi Azure Windows dysku VHD lub VHDX
services: virtual-machines-windows
documentationcenter: ''
author: glimoli
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 7802489d-33ec-4302-82a4-91463d03887a
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: troubleshooting
ms.date: 08/23/2018
ms.author: genli
ms.openlocfilehash: 293671ac3426a92453e0c1642d421038824ac02c
ms.sourcegitcommit: f1e6e61807634bce56a64c00447bf819438db1b8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/24/2018
ms.locfileid: "42886977"
---
# <a name="prepare-a-windows-vhd-or-vhdx-to-upload-to-azure"></a>Przygotowywanie wirtualnego dysku twardego Windows lub VHDX można przekazać na platformę Azure
Przed przekazaniem Windows maszyn wirtualnych (VM) ze środowiska lokalnego w systemie Microsoft Azure, należy przygotować wirtualny dysk twardy (VHD lub VHDX). Platforma Azure obsługuje **tylko maszyny wirtualne generacji 1** są w formacie pliku wirtualnego dysku twardego oraz mieć stały dysk o rozmiarze. Maksymalny dozwolony rozmiar wirtualnego dysku twardego jest 1,023 GB. Możesz również przekonwertować generacji 1 maszyny Wirtualnej z VHDX pliku system do wirtualnego dysku twardego i z dynamicznie powiększających się dysków na stałych rozmiarach. Ale nie można zmienić generacji maszyny Wirtualnej. Aby uzyskać więcej informacji, zobacz [generacji 1 lub 2 należy utworzyć maszyny Wirtualnej w funkcji Hyper-V](https://technet.microsoft.com/windows-server-docs/compute/hyper-v/plan/should-i-create-a-generation-1-or-2-virtual-machine-in-hyper-v).

Aby uzyskać więcej informacji o zasady pomocy technicznej dla maszyn wirtualnych platformy Azure, zobacz [pomocy technicznej oprogramowanie serwera firmy Microsoft dla maszyn wirtualnych platformy Microsoft](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines).

> [!Note]
> Instrukcje w tym artykule mają zastosowanie do 64-bitowej wersji systemu Windows Server 2008 R2 i nowszym systemem operacyjnym serwera Windows. Aby dowiedzieć się, uruchamianie 32-bitowej wersji systemu operacyjnego na platformie Azure, zobacz [obsługa 32-bitowych systemach operacyjnych w usłudze Azure virtual machines](https://support.microsoft.com/help/4021388/support-for-32-bit-operating-systems-in-azure-virtual-machines).

## <a name="convert-the-virtual-disk-to-vhd-and-fixed-size-disk"></a>Konwertuj wirtualny dysk wirtualny dysk twardy i dysk o stałym rozmiarze 
Jeśli zachodzi potrzeba Konwertuj wirtualny dysk z wymaganym formatem dla platformy Azure, należy użyć jednej z metod w tej sekcji. Tworzenie kopii zapasowej maszyny Wirtualnej przed uruchomieniem procesu konwersji dysku wirtualnego i upewnij się, że wirtualny dysk twardy Windows działa poprawnie na serwerze lokalnym. Usuń wszelkie błędy, w ramach samą maszynę Wirtualną, przed podjęciem próby konwersji lub przekaż go na platformę Azure.

Po przekonwertowaniu dysku, utwórz maszynę Wirtualną, która korzysta z dysków przekonwertowana. Uruchom i zaloguj się do maszyny Wirtualnej, aby zakończyć przygotowywanie maszyny Wirtualnej w celu przekazania.

### <a name="convert-disk-using-hyper-v-manager"></a>Konwertuj dysk za pomocą Menedżera funkcji Hyper-V
1. Otwórz Menedżera funkcji Hyper-V, a następnie wybierz komputera lokalnego po lewej stronie. W menu powyżej listy komputerów, kliknij **akcji** > **Edytuj dysk**.
2. Na **lokalizowanie wirtualnego dysku twardego** ekranu, Znajdź i zaznacz dysku wirtualnego.
3. Na **Wybieranie czynności** ekranu, a następnie wybierz pozycję **przekonwertować** i **dalej**.
4. Jeśli potrzebujesz przekonwertować dysk VHDX, wybierz **wirtualnego dysku twardego** a następnie kliknij przycisk **dalej**.
5. Jeśli musisz przekonwertować z dynamicznie powiększających dysku, wybierz opcję **ustalony rozmiar** a następnie kliknij przycisk **dalej**.
6. Zlokalizuj i wybierz ścieżkę, aby zapisać nowy plik wirtualnego dysku twardego.
7. Kliknij przycisk **Zakończ**.

>[!NOTE]
>Polecenia w tym artykule muszą być uruchamiane w sesji programu PowerShell z podwyższonym poziomem uprawnień.

### <a name="convert-disk-by-using-powershell"></a>Konwertuj dysk przy użyciu programu PowerShell
Można przekonwertować dysk wirtualny, używając [Convert-VHD](http://technet.microsoft.com/library/hh848454.aspx) polecenia w programie Windows PowerShell. Wybierz **Uruchom jako administrator** podczas uruchamiania programu PowerShell. 

Następujące przykładowe polecenie konwertuje z dysku VHDX do wirtualnego dysku twardego, dynamicznie powiększających się dysków na stałym rozmiarze:

```Powershell
Convert-VHD –Path c:\test\MY-VM.vhdx –DestinationPath c:\test\MY-NEW-VM.vhd -VHDType Fixed
```
W tym poleceniu, zastępując wartość "— ścieżki" ze ścieżką do wirtualnego dysku twardego, który chcesz przekonwertować, a wartość "-Ścieżka_docelowa" z nową ścieżkę i nazwę dysku przekonwertowana.

### <a name="convert-from-vmware-vmdk-disk-format"></a>Konwersja z formatu dysku VMDK programu VMware
W przypadku obrazu maszyny Wirtualnej Windows [format pliku VMDK](https://en.wikipedia.org/wiki/VMDK), przekonwertuj go na dysku VHD za pomocą [konwerter maszyn wirtualnych Microsoft](https://www.microsoft.com/download/details.aspx?id=42497). Aby uzyskać więcej informacji, zobacz artykuł z bloga [sposób konwertowania VMDK programu VMware do wirtualnego dysku twardego funkcji Hyper-V](http://blogs.msdn.com/b/timomta/archive/2015/06/11/how-to-convert-a-vmware-vmdk-to-hyper-v-vhd.aspx).

## <a name="set-windows-configurations-for-azure"></a>Ustawianie konfiguracji Windows na platformie Azure

Na maszynie Wirtualnej, który chcesz przekazać na platformę Azure, należy uruchomić wszystkie polecenia w poniższych krokach z [okna wiersza polecenia z podwyższonym poziomem uprawnień](https://technet.microsoft.com/library/cc947813.aspx):

1. Usuń wszystkie trasy statycznej trwałego w tabeli routingu:
   
   * Aby wyświetlić tabelę tras, uruchom `route print` w wierszu polecenia.
   * Sprawdź **trasy trwałości** sekcje. Jeśli trasę, użyj **route delete** polecenie, aby go usunąć.
2. Usuwanie serwera proxy WinHTTP:
   
    ```PowerShell
    netsh winhttp reset proxy
    ```
3. Ustaw zasady sieci SAN dysku [Onlineall](https://technet.microsoft.com/library/gg252636.aspx):
   
    ```PowerShell
    diskpart 
    ```
    Otwórz okno wiersza polecenia wpisz następujące polecenia:

     ```DISKPART
    san policy=onlineall
    exit   
    ```

4. Ustawianie czasu uniwersalnego czasu koordynowanego (UTC) dla Windows i typ uruchomienia usługi Czas Windows (w32time) do **automatycznie**:
   
    ```PowerShell
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\TimeZoneInformation' -name "RealTimeIsUniversal" 1 -Type DWord

    Set-Service -Name w32time -StartupType Automatic
    ```
5. Ustaw profil zasilania **o wysokiej wydajności**:

    ```PowerShell
    powercfg /setactive SCHEME_MIN
    ```

## <a name="check-the-windows-services"></a>Sprawdź usługi Windows
Upewnij się, że każda z następujących usług Windows jest ustawiona na **wartości domyślne Windows**. Są to minimalne liczby usług, które należy skonfigurować, aby upewnić się, że maszyna wirtualna ma łączność. Aby zresetować ustawienia uruchamiania, uruchom następujące polecenia:
   
```PowerShell
Set-Service -Name bfe -StartupType Automatic
Set-Service -Name dhcp -StartupType Automatic
Set-Service -Name dnscache -StartupType Automatic
Set-Service -Name IKEEXT -StartupType Automatic
Set-Service -Name iphlpsvc -StartupType Automatic
Set-Service -Name netlogon -StartupType Manual
Set-Service -Name netman -StartupType Manual
Set-Service -Name nsi -StartupType Automatic
Set-Service -Name termService -StartupType Manual
Set-Service -Name MpsSvc -StartupType Automatic
Set-Service -Name RemoteRegistry -StartupType Automatic
```

## <a name="update-remote-desktop-registry-settings"></a>Aktualizowanie ustawień rejestru usług pulpitu zdalnego
Upewnij się, że następujące ustawienia są poprawnie skonfigurowane dla połączeń usług pulpitu zdalnego:

>[!Note] 
>Podczas uruchamiania, może wystąpić komunikat o błędzie **Set-zmieniona właściwość elementu — ścieżka "HKLM:\SOFTWARE\Policies\Microsoft\Windows NT\Terminal usługi — nazwa &lt;nazwa obiektu&gt; &lt;wartość&gt;** w ramach tej procedury. Komunikat o błędzie można bezpiecznie zignorować. Oznacza to, tylko że domeny nie jest wypychanie tę konfigurację za pomocą obiektu zasad grupy.
>
>

1. Włączono protokół RDP (Remote Desktop):
   
    ```PowerShell
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server' -name "fDenyTSConnections" -Value 0 -Type DWord

    Set-ItemProperty -Path 'HKLM:\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services' -name "fDenyTSConnections" -Value 0 -Type DWord
    ```
   
2. Portu RDP jest prawidłowo skonfigurowany (domyślnie port 3389):
   
    ```PowerShell
   Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp' -name "PortNumber" 3389 -Type DWord
    ```
    Podczas wdrażania maszyny Wirtualnej, domyślne reguły są tworzone dla portu 3389. Jeśli chcesz zmienić numer portu to zrobić po wdrożeniu maszyny Wirtualnej na platformie Azure.

3. Odbiornik nasłuchuje w każdy interfejs sieciowy:
   
    ```PowerShell
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp' -name "LanAdapter" 0 -Type DWord
   ```
4. Skonfigurować tryb uwierzytelniania na poziomie sieci na potrzeby połączenia RDP:
   
    ```PowerShell
   Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp' -name "UserAuthentication" 1 -Type DWord

    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp' -name "SecurityLayer" 1 -Type DWord

    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp' -name "fAllowSecProtocolNegotiation" 1 -Type DWord
     ```

5. Ustaw wartość keep-alive:
    
    ```PowerShell
    Set-ItemProperty -Path 'HKLM:\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services' -name "KeepAliveEnable" 1 -Type DWord
    Set-ItemProperty -Path 'HKLM:\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services' -name "KeepAliveInterval" 1 -Type DWord
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp' -name "KeepAliveTimeout" 1 -Type DWord
    ```
6. Połącz ponownie:
    
    ```PowerShell
    Set-ItemProperty -Path 'HKLM:\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services' -name "fDisableAutoReconnect" 0 -Type DWord
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp' -name "fInheritReconnectSame" 1 -Type DWord
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp' -name "fReconnectSame" 0 -Type DWord
    ```
7. Ogranicz liczbę jednoczesnych połączeń:
    
    ```PowerShell
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp' -name "MaxInstanceCount" 4294967295 -Type DWord
    ```
8. Jeśli ma żadnych certyfikatów z podpisem własnym powiązane odbiornik protokołu RDP, należy je usunąć:
    
    ```PowerShell
    Remove-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp' -name "SSLCertificateSHA1Hash"
    ```
    To, aby upewnić się, że możesz połączyć na początku podczas wdrażania maszyny Wirtualnej. Możesz również sprawdzić to na późniejszym etapie po wdrożeniu maszyny Wirtualnej na platformie Azure, jeśli to konieczne.

9. Jeśli maszyna wirtualna ma być częścią domeny, sprawdź wszystkie poniższe ustawienia, aby upewnić się, że wcześniejsze ustawienia nie są przywracane. Zasady, które musi być zaznaczone są następujące:
    
    | Cel                                     | Zasady                                                                                                                                                       | Wartość                                                                                    |
    |------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------|
    | Włączono protokół RDP                           | Komputera Konfiguracja komputera\Zasady\Ustawienia Settings\Administrative Templates\Components\Remote pulpitu usług sesji pulpitu zdalnego\Połączenia         | Umożliwiają użytkownikom zdalne łączenie się przy użyciu pulpitu zdalnego                                  |
    | NLA zasad grupy                         | Settings\Administrative Templates\Components\Remote pulpitu pulpitu sesji usług zdalnego\Zabezpieczenia                                                    | Wymagaj uwierzytelniania użytkownika dla połączeń zdalnych za pomocą uwierzytelniania na poziomie sieci |
    | Zachowaj ustawienia utrzymywania aktywności                      | Komputera Konfiguracja komputera\Zasady\Ustawienia Settings\Administrative administracyjne\Składniki systemu Windows\Usługi pulpitu usług sesji pulpitu zdalnego\Połączenia | Skonfiguruj interwał utrzymywania aktywności połączenia                                                 |
    | Połącz ponownie ustawienia                       | Komputera Konfiguracja komputera\Zasady\Ustawienia Settings\Administrative administracyjne\Składniki systemu Windows\Usługi pulpitu usług sesji pulpitu zdalnego\Połączenia | Automatyczne ponowne łączenie                                                                   |
    | Ogranicz liczbę ustawienia połączenia | Komputera Konfiguracja komputera\Zasady\Ustawienia Settings\Administrative administracyjne\Składniki systemu Windows\Usługi pulpitu usług sesji pulpitu zdalnego\Połączenia | Ogranicz liczbę połączeń                                                              |

## <a name="configure-windows-firewall-rules"></a>Konfigurowanie reguł zapory Windows
1. Włącz zaporę Windows, na trzy profile (domeny, Standard i publiczne):

   ```PowerShell
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\services\SharedAccess\Parameters\FirewallPolicy\DomainProfile' -name "EnableFirewall" -Value 1 -Type DWord
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\services\SharedAccess\Parameters\FirewallPolicy\PublicProfile' -name "EnableFirewall" -Value 1 -Type DWord
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\services\SharedAccess\Parameters\FirewallPolicy\Standardprofile' -name "EnableFirewall" -Value 1 -Type DWord
   ```

2. W programie PowerShell, aby włączyć usługę WinRM za pośrednictwem trzech profili zapory (domena, prywatne i publiczne) i włączyć usługi zdalne programu PowerShell, uruchom następujące polecenie:
   
   ```PowerShell
    Enable-PSRemoting -force
    netsh advfirewall firewall set rule dir=in name="Windows Remote Management (HTTP-In)" new enable=yes
    netsh advfirewall firewall set rule dir=in name="Windows Remote Management (HTTP-In)" new enable=yes
   ```
3. Włącz następujące reguły zapory zezwalające na ruch RDP:

   ```PowerShell
    netsh advfirewall firewall set rule group="Remote Desktop" new enable=yes
   ```   
4. Tak, aby maszyna wirtualna może odpowiadać na polecenia ping w sieci wirtualnej, należy włączyć zasadę udostępnianie plików i drukarek:

   ```PowerShell
    netsh advfirewall firewall set rule dir=in name="File and Printer Sharing (Echo Request - ICMPv4-In)" new enable=yes
   ``` 
5. Jeśli maszyna wirtualna ma być częścią domeny, sprawdź następujące ustawienia, aby upewnić się, że wcześniejsze ustawienia nie są przywracane. Zasady usługi AD, które musi być zaznaczone są następujące:

    | Cel                                 | Zasady                                                                                                                                                  | Wartość                                   |
    |--------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------|-----------------------------------------|
    | Włącz profil zapory Windows | Zapora Profile\Windows Firewall\Domain Connection\Windows administracyjne\Sieć\Połączenia komputera Konfiguracja komputera\Zasady\Ustawienia Settings\Administrative   | Chroń wszystkie połączenia sieciowe         |
    | Włącz protokół RDP                           | Zapora Profile\Windows Firewall\Domain Connection\Windows administracyjne\Sieć\Połączenia komputera Konfiguracja komputera\Zasady\Ustawienia Settings\Administrative   | Zezwalaj na przychodzące wyjątki usług pulpitu zdalnego |
    |                                      | Zapora Profile\Windows Firewall\Standard Connection\Windows administracyjne\Sieć\Połączenia komputera Konfiguracja komputera\Zasady\Ustawienia Settings\Administrative | Zezwalaj na przychodzące wyjątki usług pulpitu zdalnego |
    | Włączanie protokołu ICMP V4                       | Zapora Profile\Windows Firewall\Domain Connection\Windows administracyjne\Sieć\Połączenia komputera Konfiguracja komputera\Zasady\Ustawienia Settings\Administrative   | Zezwalaj na wyjątki protokołu ICMP                   |
    |                                      | Zapora Profile\Windows Firewall\Standard Connection\Windows administracyjne\Sieć\Połączenia komputera Konfiguracja komputera\Zasady\Ustawienia Settings\Administrative | Zezwalaj na wyjątki protokołu ICMP                   |

## <a name="verify-vm-is-healthy-secure-and-accessible-with-rdp"></a>Sprawdź, czy maszyna wirtualna jest w dobrej kondycji, bezpieczne i dostępne za pomocą protokołu RDP 
1. Aby upewnić się, że dysk jest w dobrej kondycji i spójne, uruchom operację sprawdzania dysku przy następnym ponownym uruchomieniu maszyny Wirtualnej:

    ```PowerShell
    Chkdsk /f
    ```
    Upewnij się, przedstawia dysku dobrej kondycji i przejrzysty.

2. Określ ustawienia danych konfiguracji rozruchu (BCD). 

    > [!Note]
    > Upewnij się, że uruchom następujące polecenia z podwyższonym poziomem uprawnień okno programu PowerShell.
   
   ```powershell
    cmd

    bcdedit /set {bootmgr} integrityservices enable
    bcdedit /set {default} device partition=C:
    bcdedit /set {default} integrityservices enable
    bcdedit /set {default} recoveryenabled Off
    bcdedit /set {default} osdevice partition=C:
    bcdedit /set {default} bootstatuspolicy IgnoreAllFailures

    #Enable Serial Console Feature
    bcdedit /set {bootmgr} displaybootmenu yes
    bcdedit /set {bootmgr} timeout 10
    bcdedit /set {bootmgr} bootems yes
    bcdedit /ems {current} ON
    bcdedit /emssettings EMSPORT:1 EMSBAUDRATE:115200

    exit
   ```
3. Dziennik zrzutu może być pomocne w rozwiązywaniu problemów z awarii Windows. Włącz zbieranie danych dziennika zrzutu:

    ```powershell
    cmd

    #Setup the Guest OS to collect a kernel dump on an OS crash event
    REG ADD "HKLM\SYSTEM\CurrentControlSet\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "%SystemRoot%\MEMORY.DMP" /f
    REG ADD "HKLM\SYSTEM\CurrentControlSet\Control\CrashControl" /v CrashDumpEnabled /t REG_DWORD /d 2 /f
    REG ADD "HKLM\SYSTEM\CurrentControlSet\Control\CrashControl" /v NMICrashDump /t REG_DWORD /d 1 /f

    #Setup the Guest OS to collect user mode dumps on a service crash event
    md c:\Crashdumps
    REG ADD "HKLM\SOFTWARE\Microsoft\Windows\Windows Error Reporting" /v DumpFolder /t REG_EXPAND_SZ /d "c:\CrashDumps" /f
    REG ADD "HKLM\SOFTWARE\Microsoft\Windows\Windows Error Reporting" /v CrashCount /t REG_DWORD /d 10 /f
    REG ADD "HKLM\SOFTWARE\Microsoft\Windows\Windows Error Reporting" /v DumpType /t REG_DWORD /d 2 /f
    sc config WerSvc start= demand

    exit
    
    ```
4. Sprawdź, czy repozytorium systemu Windows Management Instrumentations spójne. Aby to wykonać, uruchom następujące polecenie:

    ```PowerShell
    winmgmt /verifyrepository
    ```
    Jeśli repozytorium jest uszkodzony, zobacz [WMI: uszkodzenie repozytorium lub nie](https://blogs.technet.microsoft.com/askperf/2014/08/08/wmi-repository-corruption-or-not).

5. Upewnij się, że wszystkie inne aplikacje nie korzysta z portu 3389. Port ten jest używany przez usługę protokołu RDP na platformie Azure. Możesz uruchomić **netstat - anob** aby zobaczyć, które porty w używanych na maszynie Wirtualnej:

    ```PowerShell
    netstat -anob
    ```

6. Jeśli Windows wirtualnego dysku twardego, który chcesz przekazać jest kontrolerem domeny, następnie wykonaj następujące kroki:

    1. Postępuj zgodnie z [te dodatkowe kroki](https://support.microsoft.com/kb/2904015) do przygotowania dysku.

    1. Upewnij się, że znasz hasło trybu DSRM, w przypadku, gdy musisz uruchomić maszyny Wirtualnej w trybie DSRM w pewnym momencie. Możesz odwoływać się do tego linku, aby ustawić [hasło trybu DSRM](https://technet.microsoft.com/library/cc754363(v=ws.11).aspx).

7. Upewnij się, są znane konta wbudowanego konta administratora i hasła dla użytkownika. Możesz zresetować bieżące hasło administratora lokalnego i upewnij się, że zalogować się do Windows za pośrednictwem połączenia RDP, można użyć tego konta. To uprawnienie dostępu jest kontrolowane przez obiekt zasad grupy "Zezwalaj na logowanie za pomocą usług pulpitu zdalnego". Ten obiekt można wyświetlić w lokalnej edytorze zasad grupy w obszarze:

    Komputera komputera\Ustawienia systemu Windows\Ustawienia zabezpieczeń\Zasady Lokalne\przypisywanie praw użytkownika

8. Sprawdź, czy zasady następujących usług AD, aby upewnić się, czy nie blokuje dostępu RDP za pośrednictwem protokołu RDP ani nie pochodzi z sieci:

    - Komputera komputera\Ustawienia systemu Windows\Ustawienia zabezpieczeń\Zasady lokalne\przypisanie praw użytkownika\odmowa dostęp do tego komputera z sieci

    - Komputera komputera\Ustawienia systemu Windows\Ustawienia zabezpieczeń\Zasady lokalne\przypisanie praw użytkownika\odmowa logowania za pomocą usług pulpitu zdalnego


9. Sprawdź następujące zasady usługi AD, aby upewnić się, że nie jest usuwana dowolną z następujących kont dostępu wymagana do:

    - Komputera komputera\Ustawienia systemu Windows\Ustawienia zabezpieczeń\Zasady lokalne\przypisanie praw Assignment\Access to obliczeń z sieci

    Tych zasad, powinny zostać wyświetlone następujące grupy:

    - Administratorzy
    - Operatorzy kopii zapasowych
    - Wszyscy
    - Użytkownicy

10. Ponowne uruchomienie maszyny Wirtualnej, aby upewnić się, że Windows jest nadal działa prawidłowo, jest osiągalna przy użyciu połączenia RDP. W tym momencie możesz utworzyć Maszynę wirtualną w sieci lokalnej funkcji Hyper-V upewnij się, że maszyna wirtualna jest uruchamiana całkowite, a następnie sprawdź, czy jest dostępny protokołu RDP.

11. Usuń wszelkie dodatkowe filtry interfejs sterownika transportu, takiego jak oprogramowanie, które analizuje TCP pakietów lub dodatkowe zapory. Możesz również sprawdzić to na późniejszym etapie po wdrożeniu maszyny Wirtualnej na platformie Azure, jeśli to konieczne.

12. Odinstaluj każde inne oprogramowanie innych firm i sterownika, który jest powiązany z składniki fizyczne lub jakakolwiek inna technologia wirtualizacji.

### <a name="install-windows-updates"></a>Instalowanie aktualizacji Windows
Jest optymalną konfigurację **ma poziom poprawki maszyny r**. Jeśli nie jest to możliwe, upewnij się, że zainstalowano następujące aktualizacje:

| Składnik               | Binarny         | Windows 7 z dodatkiem SP1, Windows Server 2008 R2 z dodatkiem SP1 | Windows 8, Windows Server 2012               | Windows 8.1, Windows Server 2012 R2 | Wersja systemu Windows 10 1607Windows Server 2016 w wersji 1607 | Windows 10 w wersji 1703    | Windows Server 2016 systemu Windows 10 1709 w wersji 1709 | System Windows 10 1803Windows Server 2016 w wersji 1803 |
|-------------------------|----------------|-------------------------------------------|---------------------------------------------|------------------------------------|---------------------------------------------------------|----------------------------|-------------------------------------------------|-------------------------------------------------|
| Magazyn                 | Disk.sys       | 6.1.7601.23403 - KB3125574                | 6.2.9200.17638 / 6.2.9200.21757 - KB3137061 | 6.3.9600.18203 - KB3137061         | -                                                       | -                          | -                                               | -                                               |
|                         | Storport.sys   | 6.1.7601.23403 - KB3125574                | 6.2.9200.17188 / 6.2.9200.21306 - KB3018489 | 6.3.9600.18573 - KB4022726         | 10.0.14393.1358 - KB4022715                             | 10.0.15063.332             | -                                               | -                                               |
|                         | ntfs.sys       | 6.1.7601.23403 - KB3125574                | 6.2.9200.17623 / 6.2.9200.21743 - KB3121255 | 6.3.9600.18654 - KB4022726         | 10.0.14393.1198 - KB4022715                             | 10.0.15063.447             | -                                               | -                                               |
|                         | Iologmsg.dll   | 6.1.7601.23403 - KB3125574                | 6.2.9200.16384 - KB2995387                  | -                                  | -                                                       | -                          | -                                               | -                                               |
|                         | Classpnp.sys   | 6.1.7601.23403 - KB3125574                | 6.2.9200.17061 / 6.2.9200.21180 - KB2995387 | 6.3.9600.18334 - KB3172614         | 10.0.14393.953 - KB4022715                              | -                          | -                                               | -                                               |
|                         | Volsnap.sys    | 6.1.7601.23403 - KB3125574                | 6.2.9200.17047 / 6.2.9200.21165 - KB2975331 | 6.3.9600.18265 - KB3145384         | -                                                       | 10.0.15063.0               | -                                               | -                                               |
|                         | PartMgr.sys    | 6.1.7601.23403 - KB3125574                | 6.2.9200.16681 - KB2877114                  | 6.3.9600.17401 - KB3000850         | 10.0.14393.953 - KB4022715                              | 10.0.15063.0               | -                                               | -                                               |
|                         | volmgr.sys     |                                           |                                             |                                    |                                                         | 10.0.15063.0               | -                                               | -                                               |
|                         | Volmgrx.sys    | 6.1.7601.23403 - KB3125574                | -                                           | -                                  | -                                                       | 10.0.15063.0               | -                                               | -                                               |
|                         | Msiscsi.sys    | 6.1.7601.23403 - KB3125574                | 6.2.9200.21006 - KB2955163                  | 6.3.9600.18624 - KB4022726         | 10.0.14393.1066 - KB4022715                             | 10.0.15063.447             | -                                               | -                                               |
|                         | MSDSM.sys      | 6.1.7601.23403 - KB3125574                | 6.2.9200.21474 - KB3046101                  | 6.3.9600.18592 - KB4022726         | -                                                       | -                          | -                                               | -                                               |
|                         | MPIO.sys       | 6.1.7601.23403 - KB3125574                | 6.2.9200.21190 - KB3046101                  | 6.3.9600.18616 - KB4022726         | 10.0.14393.1198 - KB4022715                             | -                          | -                                               | -                                               |
|                         | vmstorfl.sys   | 6.3.9600.18907 - KB4072650                | 6.3.9600.18080 - KB3063109                  | 6.3.9600.18907 - KB4072650         | 10.0.14393.2007 - KB4345418                             | 10.0.15063.850 - KB4345419 | 10.0.16299.371 - KB4345420                      | -                                               |
|                         | Fveapi.dll     | 6.1.7601.23311 - KB3125574                | 6.2.9200.20930 - KB2930244                  | 6.3.9600.18294 - KB3172614         | 10.0.14393.576 - KB4022715                              | -                          | -                                               | -                                               |
|                         | Fveapibase.dll | 6.1.7601.23403 - KB3125574                | 6.2.9200.20930 - KB2930244                  | 6.3.9600.17415 - KB3172614         | 10.0.14393.206 - KB4022715                              | -                          | -                                               | -                                               |
| Sieć                 | netvsc.sys     | -                                         | -                                           | -                                  | 10.0.14393.1198 - KB4022715                             | 10.0.15063.250 - KB4020001 | -                                               | -                                               |
|                         | mrxsmb10.sys   | 6.1.7601.23816 - KB4022722                | 6.2.9200.22108 - KB4022724                  | 6.3.9600.18603 - KB4022726         | 10.0.14393.479 - KB4022715                              | 10.0.15063.483             | -                                               | -                                               |
|                         | mrxsmb20.sys   | 6.1.7601.23816 - KB4022722                | 6.2.9200.21548 - KB4022724                  | 6.3.9600.18586 - KB4022726         | 10.0.14393.953 - KB4022715                              | 10.0.15063.483             | -                                               | -                                               |
|                         | mrxsmb.sys     | 6.1.7601.23816 - KB4022722                | 6.2.9200.22074 - KB4022724                  | 6.3.9600.18586 - KB4022726         | 10.0.14393.953 - KB4022715                              | 10.0.15063.0               | -                                               | -                                               |
|                         | Tcpip.sys      | 6.1.7601.23761 - KB4022722                | 6.2.9200.22070 - KB4022724                  | 6.3.9600.18478 - KB4022726         | 10.0.14393.1358 - KB4022715                             | 10.0.15063.447             | -                                               | -                                               |
|                         | Sterownik HTTP.sys       | 6.1.7601.23403 - KB3125574                | 6.2.9200.17285 - KB3042553                  | 6.3.9600.18574 - KB4022726         | 10.0.14393.251 - KB4022715                              | 10.0.15063.483             | -                                               | -                                               |
|                         | vmswitch.sys   | 6.1.7601.23727 - KB4022719                | 6.2.9200.22117 - KB4022724                  | 6.3.9600.18654 - KB4022726         | 10.0.14393.1358 - KB4022715                             | 10.0.15063.138             | -                                               | -                                               |
| Podstawowe                    | ntoskrnl.exe   | 6.1.7601.23807 - KB4022719                | 6.2.9200.22170 - KB4022718                  | 6.3.9600.18696 - KB4022726         | 10.0.14393.1358 - KB4022715                             | 10.0.15063.483             | -                                               | -                                               |
| Usługi pulpitu zdalnego | rdpcorets.dll  | 6.2.9200.21506 - KB4022719                | 6.2.9200.22104 - KB4022724                  | 6.3.9600.18619 - KB4022726         | 10.0.14393.1198 - KB4022715                             | 10.0.15063.0               | -                                               | -                                               |
|                         | termsrv.dll    | 6.1.7601.23403 - KB3125574                | 6.2.9200.17048 - KB2973501                  | 6.3.9600.17415 - KB3000850         | 10.0.14393.0 - KB4022715                                | 10.0.15063.0               | -                                               | -                                               |
|                         | termdd.sys     | 6.1.7601.23403 - KB3125574                | -                                           | -                                  | -                                                       | -                          | -                                               | -                                               |
|                         | win32k.sys     | 6.1.7601.23807 - KB4022719                | 6.2.9200.22168 - KB4022718                  | 6.3.9600.18698 - KB4022726         | 10.0.14393.594 - KB4022715                              | -                          | -                                               | -                                               |
|                         | rdpdd.dll      | 6.1.7601.23403 - KB3125574                | -                                           | -                                  | -                                                       | -                          | -                                               | -                                               |
|                         | rdpwd.sys      | 6.1.7601.23403 - KB3125574                | -                                           | -                                  | -                                                       | -                          | -                                               | -                                               |
| Bezpieczeństwo                | MS17-010       | KB4012212                                 | KB4012213                                   | KB4012213                          | KB4012606                                               | KB4012606                  | -                                               | -                                               |
|                         |                |                                           | KB4012216                                   |                                    | KB4013198                                               | KB4013198                  | -                                               | -                                               |
|                         |                | KB4012215                                 | KB4012214                                   | KB4012216                          | KB4013429                                               | KB4013429                  | -                                               | -                                               |
|                         |                |                                           | KB4012217                                   |                                    | KB4013429                                               | KB4013429                  | -                                               | -                                               |
|                         | CVE-2018-0886  | KB4103718               | KB4103730                | KB4103725       | KB4103723                                               | KB4103731                  | KB4103727                                       | KB4103721                                       |
|                         |                | KB4103712          | KB4103726          | KB4103715|                                                         |                            |                                                 |                                                 |
       
### Kiedy należy użyć narzędzia sysprep <a id="step23"></a>    

Sysprep to proces, który można uruchomić do instalacji systemu windows, spowoduje to zresetowanie instalacji systemu, która zapewni "gotowe środowisko" przez usunięcie wszystkich danych osobowych i zresetowanie kilka składników. Zwykle w tym jeśli chcesz utworzyć szablon, w którym można wdrożyć kilka innych maszyn wirtualnych, które mają konkretną konfigurację. Jest to nazywane **uogólniony obraz**.

Jeśli zamiast tego chcesz tylko utworzyć jedną maszynę Wirtualną z jednego dysku, nie trzeba użyć narzędzia sysprep. W takiej sytuacji można utworzyć maszyny Wirtualnej od co to jest znany jako **obrazu wyspecjalizowanego**.

Aby uzyskać więcej informacji o tym, jak utworzyć maszynę Wirtualną na podstawie wyspecjalizowanego dysku Zobacz:

- [Tworzenie maszyny Wirtualnej na podstawie wyspecjalizowanego dysku](create-vm-specialized.md)
- [Tworzenie maszyny Wirtualnej na podstawie wyspecjalizowanego dysku wirtualnego dysku twardego](https://review.docs.microsoft.com/en-us/azure/virtual-machines/windows/create-vm-specialized-portal?branch=master)

Jeśli chcesz utworzyć uogólnionego obrazu, należy uruchomić programu sysprep. Aby uzyskać więcej informacji na temat narzędzia Sysprep, zobacz [sposobu użycia programu Sysprep: wprowadzenie](http://technet.microsoft.com/library/bb457073.aspx). 

Nie każda rola lub aplikacji, która jest zainstalowana na komputerze z systemem Windows obsługuje to uogólnienie. Dlatego przed uruchomieniem tej procedury zapoznaj się z następującym artykułem, aby upewnić się, że roli tego komputera jest obsługiwana przez program sysprep. Aby uzyskać więcej informacji [Obsługa narzędzia Sysprep dla ról serwera](https://msdn.microsoft.com/windows/hardware/commercialize/manufacture/desktop/sysprep-support-for-server-roles).

### <a name="steps-to-generalize-a-vhd"></a>Kroki, aby uogólnić wirtualnego dysku twardego

>[!NOTE]
> Po uruchomieniu sysprep.exe określonych w poniższych krokach Wyłącz maszynę Wirtualną, a nie należy włączać je ponownie do czasu utworzenia obrazu z niego na platformie Azure.

1. Zaloguj się w usłudze Windows maszyny Wirtualnej.
2. Uruchom **polecenia** jako administrator. 
3. Zmień katalog na: **%windir%\system32\sysprep**, a następnie uruchom **sysprep.exe**.
3. W oknie dialogowym **Narzędzie przygotowywania systemu** wybierz pozycję **Włącz systemowy tryb OOBE** i upewnij się, że pole wyboru **Uogólnij** jest zaznaczone.

    ![Narzędzie przygotowania systemu](media/prepare-for-upload-vhd-image/syspre.png)
4. W **opcje zamykania**, wybierz opcję **zamknięcia**.
5. Kliknij przycisk **OK**.
6. Po zakończeniu działania programu Sysprep, zamknij maszynę Wirtualną. Nie używaj **ponowne uruchomienie** do zamykania maszyny Wirtualnej.
7. Teraz wirtualny dysk twardy jest gotowy do przekazania. Aby uzyskać więcej informacji o tym, jak utworzyć maszynę Wirtualną na podstawie uogólnionego dysku, zobacz [przekazywanie uogólnionego wirtualnego dysku twardego i użyć go do utworzenia nowych maszyn wirtualnych na platformie Azure](sa-upload-generalized.md).


## <a name="complete-recommended-configurations"></a>Dokończ konfigurację zalecane
Następujące ustawienia nie wpływają na przekazywanie wirtualnego dysku twardego. Jednak zdecydowanie zaleca się je skonfigurować.

* Zainstaluj [agenta na maszynach wirtualnych platformy Azure](http://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409). Następnie można włączyć rozszerzenia maszyn wirtualnych. Rozszerzenia maszyn wirtualnych wdrożyć najbardziej krytycznych funkcje, które być może chcesz używać z maszynami wirtualnymi takie jak resetowanie haseł, konfigurowania protokołu RDP i tak dalej. Aby uzyskać więcej informacji, zobacz:

    - [Agent maszyny Wirtualnej i rozszerzenia — część 1](https://azure.microsoft.com/blog/vm-agent-and-extensions-part-1/)
    - [Agent maszyny Wirtualnej i rozszerzenia — część 2](https://azure.microsoft.com/blog/vm-agent-and-extensions-part-2/)

*  Po utworzeniu maszyny Wirtualnej na platformie Azure, zaleca się umieszczenie pliku stronicowania w woluminie "Stacja danych czasowych", aby zwiększyć wydajność. Możesz skonfigurować to w następujący sposób:

    ```PowerShell
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Session Manager\Memory Management' -name "PagingFiles" -Value "D:\pagefile"
    ```
W przypadku każdego dysku danych, który jest dołączony do maszyny Wirtualnej, litera dysku woluminu dysku danych czasowych jest zazwyczaj "D." Oznaczenie to może być różne w zależności od liczby dostępnych dysków i ustawienia, które wprowadzasz.

## <a name="next-steps"></a>Kolejne kroki
* [Przekazywanie obrazu maszyny Wirtualnej Windows Azure dla wdrożeń usługi Resource Manager](upload-generalized-managed.md)
* [Rozwiązywanie problemów aktywacji maszyny wirtualnej Windows Azure](troubleshoot-activation-problems.md)

