---
title: Przygotowywanie dysku VHD systemu Windows, aby przekazać do usługi Azure | Dokumentacja firmy Microsoft
description: Jak przygotować Windows VHD lub VHDX przed przekazaniem do platformy Azure
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
ms.date: 05/11/2018
ms.author: genli
ms.openlocfilehash: 2d7ee7050f430efea64d9988adf4f5a603128de2
ms.sourcegitcommit: 0c490934b5596204d175be89af6b45aafc7ff730
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/27/2018
ms.locfileid: "37053453"
---
# <a name="prepare-a-windows-vhd-or-vhdx-to-upload-to-azure"></a>Przygotowywanie wirtualnego dysku twardego Windows lub VHDX do przekazania do platformy Azure
Przed przekazaniem Windows maszyn wirtualnych (VM) z lokalnej do systemu Microsoft Azure, należy przygotować wirtualny dysk twardy (VHD lub VHDX). Azure obsługuje tylko generacji 1 maszyn wirtualnych, które mają w formacie pliku wirtualnego dysku twardego o rozmiarze dysk stały. Maksymalny rozmiar dozwolony dla wirtualnego dysku twardego jest 1,023 GB. Możesz przekonwertować generacji 1 maszyny Wirtualnej z dysk VHDX pliku system z dynamicznie powiększających się dysków do stałym rozmiarze i dysków VHD. Ale nie można zmienić generacji maszyny Wirtualnej. Aby uzyskać więcej informacji, zobacz [generacji 1 lub 2 należy utworzyć maszyny Wirtualnej w funkcji Hyper-V](https://technet.microsoft.com/windows-server-docs/compute/hyper-v/plan/should-i-create-a-generation-1-or-2-virtual-machine-in-hyper-v).

Aby uzyskać więcej informacji dotyczących zasad pomocy technicznej dla maszyny Wirtualnej platformy Azure, zobacz [pomocy technicznej oprogramowanie serwera firmy Microsoft dla maszyn wirtualnych Azure Microsoft](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines).

> [!Note]
> Instrukcje w tym artykule dotyczą 64-bitowej wersji systemu Windows Server 2008 R2 i nowszym system operacyjny Windows server. Informacje o 32-bitowej wersji systemu operacyjnego na platformie Azure, zobacz [obsługa 32-bitowych systemów operacyjnych w maszynach wirtualnych platformy Azure](https://support.microsoft.com/help/4021388/support-for-32-bit-operating-systems-in-azure-virtual-machines).

## <a name="convert-the-virtual-disk-to-vhd-and-fixed-size-disk"></a>Konwertuj dysku wirtualnego na wirtualny dysk twardy i dysk o stałym rozmiarze 
Aby przekonwertować dysk wirtualny z wymaganym formatem Azure należy użyć jednej z metod w tej sekcji. Utwórz kopię zapasową maszyny Wirtualnej przed uruchomieniem procesu konwersji dysku wirtualnego i upewnij się, że dysk VHD systemu Windows działa poprawnie na serwerze lokalnym. Usuń wszelkie błędy w samej maszyny Wirtualnej, przed podjęciem próby konwersji lub przekaż go do platformy Azure.

Po przekonwertowaniu dysku, utwórz maszynę Wirtualną, która używa dysku przekonwertowany. Uruchom i zaloguj się do maszyny Wirtualnej, aby zakończyć przygotowywanie maszyny Wirtualnej do przekazywania.

### <a name="convert-disk-using-hyper-v-manager"></a>Konwertuj dysk przy użyciu Menedżera funkcji Hyper-V
1. Otwórz Menedżera funkcji Hyper-V, a następnie wybierz komputera lokalnego, po lewej stronie. W menu powyżej listy komputerów, kliknij przycisk **akcji** > **Edytuj dysk**.
2. Na **lokalizowanie wirtualnego dysku twardego** ekranu, Znajdź i zaznacz dysku wirtualnego.
3. Na **wybierz akcję** ekranu, a następnie wybierz **przekonwertować** i **dalej**.
4. Jeśli chcesz konwertować z VHDX, wybierz **wirtualnego dysku twardego** , a następnie kliknij przycisk **dalej**
5. Jeśli chcesz konwertować z dynamicznie powiększających się dysków, wybierz **stały rozmiar** , a następnie kliknij przycisk **dalej**
6. Znajdź i wybierz ścieżkę, aby zapisać nowy plik wirtualnego dysku twardego.
7. Kliknij przycisk **Zakończ**.

>[!NOTE]
>Polecenia w tym artykule musi działać w sesji programu PowerShell z podwyższonym poziomem uprawnień.

### <a name="convert-disk-by-using-powershell"></a>Konwertuj dysk przy użyciu programu PowerShell
Można przekonwertować dysku wirtualnego za pomocą [Convert-VHD](http://technet.microsoft.com/library/hh848454.aspx) polecenia w programie Windows PowerShell. Wybierz **Uruchom jako administrator** podczas uruchamiania programu PowerShell. 

Następujące przykładowe polecenie konwertuje z VHDX do wirtualnego dysku twardego i dynamicznie powiększających się dysków stały rozmiar:

```Powershell
Convert-VHD –Path c:\test\MY-VM.vhdx –DestinationPath c:\test\MY-NEW-VM.vhd -VHDType Fixed
```
W tym poleceniu zastąp wartość "-ścieżki" ścieżkę do wirtualnego dysku twardego, który chcesz przekonwertować, a wartość "-Ścieżka_docelowa" z nową ścieżkę i nazwę dysku przekonwertowany.

### <a name="convert-from-vmware-vmdk-disk-format"></a>Konwersja z formatu dysku VMware VMDK
Jeśli masz obrazu maszyny Wirtualnej systemu Windows w [format pliku VMDK](https://en.wikipedia.org/wiki/VMDK), przekonwertować go na dysk VHD za pomocą [konwerter maszyny Wirtualnej Microsoft](https://www.microsoft.com/download/details.aspx?id=42497). Aby uzyskać więcej informacji, zobacz artykuł blog [jak Konwertuj dysk maszyny VMware do wirtualnego dysku twardego funkcji Hyper-V](http://blogs.msdn.com/b/timomta/archive/2015/06/11/how-to-convert-a-vmware-vmdk-to-hyper-v-vhd.aspx).

## <a name="set-windows-configurations-for-azure"></a>Ustawianie konfiguracji systemu Windows Azure

Na maszynie Wirtualnej, który chcesz przekazać do platformy Azure, uruchom wszystkie polecenia w poniższych krokach z [okno wiersza polecenia z podwyższonym poziomem uprawnień](https://technet.microsoft.com/library/cc947813.aspx):

1. Usuń wszystkie statyczne trasę w tabeli routingu:
   
   * Aby wyświetlić tabelę tras, uruchom `route print` w wierszu polecenia.
   * Sprawdź **trasy trwałości** sekcje. Jeśli trwałą trasę, użyj [Usuń trasy](https://technet.microsoft.com/library/cc739598.apx) go usunąć.
2. Usuwanie serwera proxy WinHTTP:
   
    ```PowerShell
    netsh winhttp reset proxy
    ```
3. Ustaw zasady sieci SAN dysku [Onlineall](https://technet.microsoft.com/library/gg252636.aspx). 
   
    ```PowerShell
    diskpart 
    ```
    Otwórz okno wiersza polecenia wpisz następujące polecenia:

     ```DISKPART
    san policy=onlineall
    exit   
    ```

4. Ustawianie czasu uniwersalnego czasu koordynowanego (UTC) dla systemu Windows i typ uruchamiania usługi Czas systemu Windows (w32time) do **automatycznie**:
   
    ```PowerShell
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\TimeZoneInformation' -name "RealTimeIsUniversal" 1 -Type DWord

    Set-Service -Name w32time -StartupType Auto
    ```
5. Ustawiono profil zasilania **wysokiej wydajności**:

    ```PowerShell
    powercfg /setactive SCHEME_MIN
    ```

## <a name="check-the-windows-services"></a>Sprawdź usługi systemu Windows
Upewnij się, że każda z następujących usług systemu Windows jest ustawiona na **wartości domyślnych systemu Windows**. Są to minimalne liczby usług, które można skonfigurować w celu upewnij się, że maszyna wirtualna ma łączność. Aby zresetować ustawienia uruchamiania, uruchom następujące polecenia:
   
```PowerShell
Set-Service -Name bfe -StartupType Auto
Set-Service -Name dhcp -StartupType Auto
Set-Service -Name dnscache -StartupType Auto
Set-Service -Name IKEEXT -StartupType Auto
Set-Service -Name iphlpsvc -StartupType Auto
Set-Service -Name netlogon -StartupType Manual
Set-Service -Name netman -StartupType Manual
Set-Service -Name nsi -StartupType Auto
Set-Service -Name termService -StartupType Manual
Set-Service -Name MpsSvc -StartupType Auto
Set-Service -Name RemoteRegistry -StartupType Auto
```

## <a name="update-remote-desktop-registry-settings"></a>Aktualizowanie ustawień rejestru pulpitu zdalnego
Upewnij się, że następujące ustawienia są poprawnie skonfigurowany dla połączeń usług pulpitu zdalnego:

>[!Note] 
>Może pojawić się komunikat o błędzie podczas uruchamiania **ItemProperty zestaw-ścieżki "HKLM:\SOFTWARE\Policies\Microsoft\Windows NT\Terminal usługi - nazwa &lt;nazwa obiektu&gt; &lt;wartość&gt;** w tych krokach. Komunikat o błędzie można bezpiecznie zignorować. Oznacza to, tylko że domeny nie jest wypychanie takiej konfiguracji za pomocą obiektu zasad grupy.
>
>

1. Włączono protokołu RDP (Remote Desktop):
   
    ```PowerShell
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server' -name "fDenyTSConnections" -Value 0 -Type DWord

    Set-ItemProperty -Path 'HKLM:\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services' -name "fDenyTSConnections" -Value 0 -Type DWord
    ```
   
2. Port protokołu RDP jest poprawnie skonfigurowany (domyślny port 3389):
   
    ```PowerShell
   Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp' -name "PortNumber" 3389 -Type DWord
    ```
    Podczas wdrażania maszyny Wirtualnej, domyślne reguły są tworzone z portu 3389. Jeśli chcesz zmienić numer portu, należy to zrobić, po wdrożeniu maszyny Wirtualnej na platformie Azure.

3. Odbiornik nasłuchuje w każdym interfejsu sieciowego:
   
    ```PowerShell
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp' -name "LanAdapter" 0 -Type DWord
   ```
4. Konfigurowanie trybu uwierzytelniania na poziomie sieci dla połączeń protokołu RDP:
   
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
7. Ogranicz liczbę równoczesnych połączeń:
    
    ```PowerShell
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp' -name "MaxInstanceCount" 4294967295 -Type DWord
    ```
8. Jeśli ma żadnych certyfikatów z podpisem własnym powiązane odbiornika protokołu RDP, należy je usunąć:
    
    ```PowerShell
    Remove-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp' -name "SSLCertificateSHA1Hash"
    ```
    To, aby upewnić się, że możesz połączyć na początku podczas wdrażania maszyny Wirtualnej. Można również przejrzeć to na późniejszym etapie po wdrożeniu maszyny Wirtualnej na platformie Azure, jeśli to konieczne.

9. Jeśli maszyna wirtualna ma być częścią domeny, sprawdź wszystkie poniższe ustawienia, aby upewnić się, że wcześniejsze ustawienia nie są przywracane. Zasady, które muszą zostać sprawdzone są następujące:
    
    - Protokół RDP jest włączony:

         Komputer Konfiguracja komputera\Zasady\Ustawienia systemu Settings\Administrative Templates\ Windows\Usługi pulpitu usług sesji usług pulpitu zdalnego\Połączenia:
         
         **Zezwalaj użytkownikom na łączenie się zdalnie przy użyciu pulpitu zdalnego**

    - Zasady grupy uwierzytelniania na poziomie sieci:

        Pulpitu sesji usług pulpitu Templates\Components\Remote Settings\Administrative zdalnego\Zabezpieczenia: 
        
        **Wymagaj uwierzytelniania użytkownika dla połączeń zdalnych za pomocą uwierzytelniania na poziomie sieci**
    
    - Zachowaj ustawienia może działa:

        Komputer Konfiguracja komputera\Zasady\Ustawienia systemu Settings\Administrative administracyjne\Składniki systemu Windows\Usługi pulpitu usług sesji usług pulpitu zdalnego\Połączenia: 
        
        **Skonfiguruj interwał podtrzymania połączenia.**

    - Połącz ponownie ustawienia:

        Komputer Konfiguracja komputera\Zasady\Ustawienia systemu Settings\Administrative administracyjne\Składniki systemu Windows\Usługi pulpitu usług sesji usług pulpitu zdalnego\Połączenia: 
        
        **Automatyczne ponowne łączenie**

    - Ogranicz liczbę połączeń ustawienia:

        Komputer Konfiguracja komputera\Zasady\Ustawienia systemu Settings\Administrative administracyjne\Składniki systemu Windows\Usługi pulpitu usług sesji usług pulpitu zdalnego\Połączenia: 
        
        **Ogranicz liczbę połączeń**

## <a name="configure-windows-firewall-rules"></a>Konfigurowanie reguł zapory systemu Windows
1. Włącz Zaporę systemu Windows na trzy profile (domena, Standard i publicznego):

   ```PowerShell
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\services\SharedAccess\Parameters\FirewallPolicy\DomainProfile' -name "EnableFirewall" -Value 1 -Type DWord
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\services\SharedAccess\Parameters\FirewallPolicy\PublicProfile' -name "EnableFirewall" -Value 1 -Type DWord
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\services\SharedAccess\Parameters\FirewallPolicy\Standardprofile' -name "EnableFirewall" -Value 1 -Type DWord
   ```

2. Uruchom następujące polecenie w programie PowerShell, aby zezwolić WinRM za pośrednictwem trzy profile zapory (domeny, prywatne i publiczne) i włączyć usługi zdalne środowiska PowerShell:
   
   ```PowerShell
    Enable-PSRemoting -force
    netsh advfirewall firewall set rule dir=in name="Windows Remote Management (HTTP-In)" new enable=yes
    netsh advfirewall firewall set rule dir=in name="Windows Remote Management (HTTP-In)" new enable=yes
   ```
3. Włącz następujące reguły zapory zezwalające na ruch RDP 

   ```PowerShell
    netsh advfirewall firewall set rule group="Remote Desktop" new enable=yes
   ```   
4. Włącz regułę udostępnianie plików i drukarek, dzięki czemu maszyna wirtualna może odpowiadać na polecenia ping w sieci wirtualnej:

   ```PowerShell
    netsh advfirewall firewall set rule dir=in name="File and Printer Sharing (Echo Request - ICMPv4-In)" new enable=yes
   ``` 
5. Jeśli maszyna wirtualna ma być częścią domeny, sprawdź następujące ustawienia, aby upewnić się, że wcześniejsze ustawienia nie są przywracane. Zasady usługi AD, które muszą zostać sprawdzone są następujące:

    - Włączanie profilów zapory systemu Windows

        Konfiguracja komputera\Zasady\Ustawienia systemu Settings\Administrative administracyjne\Sieć\Połączenia Connection\Windows Firewall\Domain Profile\Windows zapory: **ochrony wszystkich połączeń sieciowych**

       Konfiguracja komputera\Zasady\Ustawienia systemu Settings\Administrative administracyjne\Sieć\Połączenia Connection\Windows Firewall\Standard Profile\Windows zapory: **ochrony wszystkich połączeń sieciowych**

    - Włącz protokół RDP 

        Konfiguracja komputera\Zasady\Ustawienia systemu Settings\Administrative administracyjne\Sieć\Połączenia Connection\Windows Firewall\Domain Profile\Windows zapory: **Zezwalaj na wyjątki pulpitu zdalnego dla ruchu przychodzącego**

        Konfiguracja komputera\Zasady\Ustawienia systemu Settings\Administrative administracyjne\Sieć\Połączenia Connection\Windows Firewall\Standard Profile\Windows zapory: **Zezwalaj na wyjątki pulpitu zdalnego dla ruchu przychodzącego**

    - Włącz protokół ICMP-V4

        Konfiguracja komputera\Zasady\Ustawienia systemu Settings\Administrative administracyjne\Sieć\Połączenia Connection\Windows Firewall\Domain Profile\Windows zapory: **Zezwalaj na wyjątki protokołu ICMP**

        Konfiguracja komputera\Zasady\Ustawienia systemu Settings\Administrative administracyjne\Sieć\Połączenia Connection\Windows Firewall\Standard Profile\Windows zapory: **Zezwalaj na wyjątki protokołu ICMP**

## <a name="verify-vm-is-healthy-secure-and-accessible-with-rdp"></a>Sprawdź, czy maszyna wirtualna jest w dobrej kondycji, bezpieczne i dostępne przy użyciu protokołu RDP 
1. Aby upewnić się, że dysk jest w dobrej kondycji i spójne, uruchom operację sprawdzania dysku przy następnym ponownym uruchomieniu maszyny Wirtualnej:

    ```PowerShell
    Chkdsk /f
    ```
    Upewnij się, że raport zawiera dysk czyste i dobrej kondycji.

2. Określ ustawienia danych konfiguracji rozruchu (BCD). 

    > [!Note]
    > Upewnij się, uruchom następujące polecenia z podwyższonym poziomem uprawnień okna CMD i **nie** na środowiska PowerShell:
   
   ```CMD
   bcdedit /set {bootmgr} integrityservices enable
   
   bcdedit /set {default} device partition=C:
   
   bcdedit /set {default} integrityservices enable
   
   bcdedit /set {default} recoveryenabled Off
   
   bcdedit /set {default} osdevice partition=C:
   
   bcdedit /set {default} bootstatuspolicy IgnoreAllFailures
   ```
3. Sprawdź, czy repozytorium Instrumentations zarządzania systemu Windows jest zgodne. Aby wykonać, uruchom następujące polecenie:

    ```PowerShell
    winmgmt /verifyrepository
    ```
    Jeśli repozytorium jest uszkodzony, zobacz [WMI: uszkodzenie repozytorium lub nie](https://blogs.technet.microsoft.com/askperf/2014/08/08/wmi-repository-corruption-or-not).

4. Upewnij się, że inna aplikacja nie korzysta z portu 3389. Port ten jest używany przez usługę protokołu RDP na platformie Azure. Można uruchomić **netstat - anob** aby zobaczyć, które porty są w używane na maszynie Wirtualnej:

    ```PowerShell
    netstat -anob
    ```

5. Jeśli wirtualny dysk twardy z systemem Windows, który chcesz przekazać jest kontrolerem domeny, następnie wykonaj następujące czynności:

    A. Postępuj zgodnie z [te dodatkowe kroki](https://support.microsoft.com/kb/2904015) Aby przygotować się na dysku.

    B. Upewnij się, że znasz hasła trybu DSRM, w przypadku, gdy trzeba uruchomić maszyny Wirtualnej w trybie DSRM, w pewnym momencie. Możesz odwoływać się do tego łącza można ustawić [hasło trybu DSRM](https://technet.microsoft.com/library/cc754363(v=ws.11).aspx).

6. Upewnij się, są znane wbudowanego konta administratora i hasła użytkownika. Możesz zresetować bieżące hasło administratora lokalnego i upewnij się, że można użyć tego konta do logowania do systemu Windows za pośrednictwem połączenia RDP. To uprawnienie dostępu jest kontrolowane przez obiekt zasad grupy "Zezwalaj na logowanie za pomocą usług pulpitu zdalnego". Można wyświetlić tego obiektu w lokalnych edytorze zasad grupy w obszarze:

    Komputer opcji Konfiguracja komputera\Ustawienia systemu Windows\Ustawienia zabezpieczeń\Zasady Lokalne\przypisywanie praw użytkownika

7. Sprawdź, czy następujące usługi AD zasady upewnij się, czy nie blokuje dostępu RDP za pośrednictwem protokołu RDP ani z sieci:

    - Komputer opcji Konfiguracja komputera\Ustawienia systemu Windows\Ustawienia zabezpieczeń\Zasady lokalne\Przypisywanie praw Assignment\Deny dostęp do tego komputera z sieci

    - Komputer logowania opcji Konfiguracja komputera\Ustawienia systemu Windows\Ustawienia zabezpieczeń\Zasady lokalne\Przypisywanie praw Assignment\Deny za pomocą usług pulpitu zdalnego


8. Ponowne uruchomienie maszyny Wirtualnej, aby upewnić się, że system Windows jest nadal działa prawidłowo, może być osiągnięta poprzez przy użyciu połączenia protokołu RDP. W tym momencie można utworzyć Maszynę wirtualną w sieci lokalnej funkcji Hyper-V, upewnij się, że maszyna wirtualna jest uruchamiana całkowicie, a następnie przetestować, czy jest dostępny RDP.

9. Usuń wszystkie filtry dodatkowy interfejs sterownika transportu, takiego jak oprogramowanie, która analizuje TCP pakiety lub dodatkowych zapór. Można również przejrzeć to na późniejszym etapie po wdrożeniu maszyny Wirtualnej na platformie Azure, jeśli to konieczne.

10. Odinstalować inne oprogramowanie innych firm i sterownik, który jest powiązany z składniki fizyczne ani innych technologii wirtualizacji.

### <a name="install-windows-updates"></a>Instalowanie aktualizacji systemu Windows
Konfiguracja idealna to **mieć poziom poprawki maszyny r**. Jeśli nie jest to możliwe, upewnij się, że są zainstalowane następujące aktualizacje:

|                       |                   |           |                                       Minimalna wersja x64       |                                      |                                      |                            |
|-------------------------|-------------------|------------------------------------|---------------------------------------------|--------------------------------------|--------------------------------------|----------------------------|
| Składnik               | Binarny            | Windows 7 i Windows Server 2008 R2 | Windows 8 i Windows Server 2012             | Windows 8.1 i Windows Server 2012 R2 | Windows 10 i Windows Server 2016 RS1 | RS2 systemu Windows 10             |
| Magazyn                 | Disk.sys          | 6.1.7601.23403 - KB3125574         | 6.2.9200.17638 / 6.2.9200.21757 - KB3137061 | 6.3.9600.18203 - KB3137061           | -                                    | -                          |
|                         | Storport.sys      | 6.1.7601.23403 - KB3125574         | 6.2.9200.17188 / 6.2.9200.21306 - KB3018489 | 6.3.9600.18573 - KB4022726           | 10.0.14393.1358 - KB4022715          | 10.0.15063.332             |
|                         | ntfs.sys          | 6.1.7601.23403 - KB3125574         | 6.2.9200.17623 / 6.2.9200.21743 - KB3121255 | 6.3.9600.18654 - KB4022726           | 10.0.14393.1198 - KB4022715          | 10.0.15063.447             |
|                         | Iologmsg.dll      | 6.1.7601.23403 - KB3125574         | 6.2.9200.16384 - KB2995387                  | -                                    | -                                    | -                          |
|                         | Classpnp.sys      | 6.1.7601.23403 - KB3125574         | 6.2.9200.17061 / 6.2.9200.21180 - KB2995387 | 6.3.9600.18334 - KB3172614           | 10.0.14393.953 - KB4022715           | -                          |
|                         | Volsnap.sys       | 6.1.7601.23403 - KB3125574         | 6.2.9200.17047 / 6.2.9200.21165 - KB2975331 | 6.3.9600.18265 - KB3145384           | -                                    | 10.0.15063.0               |
|                         | PartMgr.sys       | 6.1.7601.23403 - KB3125574         | 6.2.9200.16681 - KB2877114                  | 6.3.9600.17401 - KB3000850           | 10.0.14393.953 - KB4022715           | 10.0.15063.0               |
|                         | volmgr.sys        |                                    |                                             |                                      |                                      | 10.0.15063.0               |
|                         | Volmgrx.sys       | 6.1.7601.23403 - KB3125574         | -                                           | -                                    | -                                    | 10.0.15063.0               |
|                         | Msiscsi.sys       | 6.1.7601.23403 - KB3125574         | 6.2.9200.21006 - KB2955163                  | 6.3.9600.18624 - KB4022726           | 10.0.14393.1066 - KB4022715          | 10.0.15063.447             |
|                         | MSDSM.sys         | 6.1.7601.23403 - KB3125574         | 6.2.9200.21474 - KB3046101                  | 6.3.9600.18592 - KB4022726           | -                                    | -                          |
|                         | MPIO.sys          | 6.1.7601.23403 - KB3125574         | 6.2.9200.21190 - KB3046101                  | 6.3.9600.18616 - KB4022726           | 10.0.14393.1198 - KB4022715          | -                          |
|                         | Fveapi.dll        | 6.1.7601.23311 - KB3125574         | 6.2.9200.20930 - KB2930244                  | 6.3.9600.18294 - KB3172614           | 10.0.14393.576 - KB4022715           | -                          |
|                         | Fveapibase.dll    | 6.1.7601.23403 - KB3125574         | 6.2.9200.20930 - KB2930244                  | 6.3.9600.17415 - KB3172614           | 10.0.14393.206 - KB4022715           | -                          |
| Sieć                 | netvsc.sys        | -                                  | -                                           | -                                    | 10.0.14393.1198 - KB4022715          | 10.0.15063.250 - KB4020001 |
|                         | mrxsmb10.sys      | 6.1.7601.23816 - KB4022722         | 6.2.9200.22108 - KB4022724                  | 6.3.9600.18603 - KB4022726           | 10.0.14393.479 - KB4022715           | 10.0.15063.483             |
|                         | mrxsmb20.sys      | 6.1.7601.23816 - KB4022722         | 6.2.9200.21548 - KB4022724                  | 6.3.9600.18586 - KB4022726           | 10.0.14393.953 - KB4022715           | 10.0.15063.483             |
|                         | mrxsmb.sys        | 6.1.7601.23816 - KB4022722         | 6.2.9200.22074 - KB4022724                  | 6.3.9600.18586 - KB4022726           | 10.0.14393.953 - KB4022715           | 10.0.15063.0               |
|                         | Tcpip.sys         | 6.1.7601.23761 - KB4022722         | 6.2.9200.22070 - KB4022724                  | 6.3.9600.18478 - KB4022726           | 10.0.14393.1358 - KB4022715          | 10.0.15063.447             |
|                         | Sterownik HTTP.sys          | 6.1.7601.23403 - KB3125574         | 6.2.9200.17285 - KB3042553                  | 6.3.9600.18574 - KB4022726           | 10.0.14393.251 - KB4022715           | 10.0.15063.483             |
|                         | vmswitch.sys      | 6.1.7601.23727 - KB4022719         | 6.2.9200.22117 - KB4022724                  | 6.3.9600.18654 - KB4022726           | 10.0.14393.1358 - KB4022715          | 10.0.15063.138             |
| Podstawowe                    | ntoskrnl.exe      | 6.1.7601.23807 - KB4022719         | 6.2.9200.22170 - KB4022718                  | 6.3.9600.18696 - KB4022726           | 10.0.14393.1358 - KB4022715          | 10.0.15063.483             |
| Usługi pulpitu zdalnego | rdpcorets.dll     | 6.2.9200.21506 - KB4022719         | 6.2.9200.22104 - KB4022724                  | 6.3.9600.18619 - KB4022726           | 10.0.14393.1198 - KB4022715          | 10.0.15063.0               |
|                         | termsrv.dll       | 6.1.7601.23403 - KB3125574         | 6.2.9200.17048 - KB2973501                  | 6.3.9600.17415 - KB3000850           | 10.0.14393.0 - KB4022715             | 10.0.15063.0               |
|                         | termdd.sys        | 6.1.7601.23403 - KB3125574         | -                                           | -                                    | -                                    | -                          |
|                         | win32k.sys        | 6.1.7601.23807 - KB4022719         | 6.2.9200.22168 - KB4022718                  | 6.3.9600.18698 - KB4022726           | 10.0.14393.594 - KB4022715           | -                          |
|                         | rdpdd.dll         | 6.1.7601.23403 - KB3125574         | -                                           | -                                    | -                                    | -                          |
|                         | rdpwd.sys         | 6.1.7601.23403 - KB3125574         | -                                           | -                                    | -                                    | -                          |
| Bezpieczeństwo                | Z powodu WannaCrypt | KB4012212                          | KB4012213                                   | KB4012213                            | KB4012606                            | KB4012606                  |
|                         |                   |                                    | KB4012216                                   |                                      | KB4013198                            | KB4013198                  |
|                         |                   | KB4012215                          | KB4012214                                   | KB4012216                            | KB4013429                            | KB4013429                  |
|                         |                   |                                    | KB4012217                                   |                                      | KB4013429                            | KB4013429                  |
       
### Kiedy należy użyć narzędzia sysprep <a id="step23"></a>    

Sysprep to proces, który można uruchomić do instalacji systemu windows, spowoduje zresetowanie instalacji systemu, który zapewni "fabrycznej wystąpić" przez usunięcie wszystkich danych osobowych i zresetowanie kilka składników. Zwykle w tym jeśli chcesz utworzyć szablon, w którym można wdrożyć kilka innych maszyn wirtualnych, które mają konkretną konfigurację. Ta metoda jest wywoływana **uogólniony obraz**.

Jeśli zamiast tego chcesz tylko utworzyć jedną maszynę Wirtualną z jednego dysku, nie trzeba użyć narzędzia sysprep. W takiej sytuacji można utworzyć maszyny Wirtualnej z określane jako **specjalne obrazu**.

Aby uzyskać więcej informacji o sposobie tworzenia maszyny Wirtualnej z dyskiem specjalne Zobacz:

- [Utwórz maszynę Wirtualną z dyskiem specjalne](create-vm-specialized.md)
- [Tworzenie maszyny Wirtualnej z dysku VHD specjalne](https://review.docs.microsoft.com/en-us/azure/virtual-machines/windows/create-vm-specialized-portal?branch=master)

Aby utworzyć obraz uogólniony, należy uruchomić programu sysprep. Aby uzyskać więcej informacji na temat narzędzia Sysprep, zobacz [sposobu użycia programu Sysprep: wprowadzenie](http://technet.microsoft.com/library/bb457073.aspx). 

Nie każda rola lub aplikacji, która jest zainstalowana na komputerze z systemem Windows obsługuje ten generalizacji. Dlatego przed uruchomieniem tej procedury można znaleźć w następującym artykule, aby upewnić się, że roli tego komputera jest obsługiwana przez program sysprep. Aby uzyskać więcej informacji [Obsługa programu Sysprep dla ról serwera](https://msdn.microsoft.com/windows/hardware/commercialize/manufacture/desktop/sysprep-support-for-server-roles).

### <a name="steps-to-generalize-a-vhd"></a>Kroki w celu uogólnienia wirtualnego dysku twardego

>[!NOTE]
> Po uruchomieniu sysprep.exe określone w poniższych krokach Wyłącz maszynę Wirtualną, a nie włączyć ją ponownie do czasu utworzenia obrazu z niego na platformie Azure.

1. Zaloguj się do maszyny Wirtualnej systemu Windows.
2. Uruchom **wiersza polecenia** jako administrator. 
3. Zmień katalog na: **%windir%\system32\sysprep**, a następnie uruchom **sysprep.exe**.
3. W oknie dialogowym **Narzędzie przygotowywania systemu** wybierz pozycję **Włącz systemowy tryb OOBE** i upewnij się, że pole wyboru **Uogólnij** jest zaznaczone.

    ![Narzędzie przygotowywania systemu](media/prepare-for-upload-vhd-image/syspre.png)
4. W **opcje zamykania**, wybierz pozycję **zamknięcia**.
5. Kliknij przycisk **OK**.
6. Po zakończeniu działania programu Sysprep, zamknij maszynę Wirtualną. Nie używaj **ponowne uruchomienie** można zamknąć maszyny Wirtualnej.
7. Wirtualny dysk twardy jest teraz gotowe do przekazania. Aby uzyskać więcej informacji o sposobie tworzenia maszyny Wirtualnej z dyskiem uogólniony, zobacz [przekazywanie uogólniony wirtualny dysk twardy i umożliwia tworzenie nowych maszyn wirtualnych na platformie Azure](sa-upload-generalized.md).


## <a name="complete-recommended-configurations"></a>Dokończ konfigurację zalecane
Poniższe ustawienia nie wpływają na przekazywanie wirtualnego dysku twardego. Zaleca się jednak skonfigurować je.

* Zainstaluj [agenta maszyny wirtualne platformy Azure](http://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409). Następnie można włączyć rozszerzenia maszyny Wirtualnej. Rozszerzeń maszyny Wirtualnej wykonuje większość krytyczne funkcje, które mogą mają być używane dla maszyn wirtualnych np. zresetowania hasła, konfigurowanie protokołu RDP i tak dalej. Aby uzyskać więcej informacji, zobacz:

    - [Agent maszyny Wirtualnej i rozszerzenia — część 1](https://azure.microsoft.com/blog/vm-agent-and-extensions-part-1/)
    - [Agent maszyny Wirtualnej i rozszerzenia — część 2](https://azure.microsoft.com/blog/vm-agent-and-extensions-part-2/)
* Dziennik zrzutu mogą być pomocne przy rozwiązywaniu problemów awarii systemu Windows. Włącz zbieranie zrzutów dziennika:
  
    ```PowerShell
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\CrashControl' -name "CrashDumpEnable" -Value "2" -Type DWord
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\CrashControl' -name "DumpFile" -Value "%SystemRoot%\MEMORY.DMP"
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\CrashControl' -name "AutoReboot" -Value 0 -Type DWord
    New-Item -Path 'HKLM:\SOFTWARE\Microsoft\Windows\Windows Error Reporting\LocalDumps'
    New-ItemProperty -Path 'HKLM:\SOFTWARE\Microsoft\Windows\Windows Error Reporting\LocalDumps' -name "DumpFolder" -Value "c:\CrashDumps"
    New-ItemProperty -Path 'HKLM:\SOFTWARE\Microsoft\Windows\Windows Error Reporting\LocalDumps' -name "DumpCount" -Value 10 -Type DWord
    New-ItemProperty -Path 'HKLM:\SOFTWARE\Microsoft\Windows\Windows Error Reporting\LocalDumps' -name "DumpType" -Value 2 -Type DWord
    Set-Service -Name WerSvc -StartupType Manual
    ```
    Jeśli wystąpią błędy podczas każdego z procedur opisanych w tym artykule, oznacza to, czy klucze rejestru już istnieje. W takiej sytuacji należy użyć następujących poleceń:

    ```PowerShell
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\CrashControl' -name "CrashDumpEnable" -Value "2" -Type DWord
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\CrashControl' -name "DumpFile" -Value "%SystemRoot%\MEMORY.DMP"
    Set-ItemProperty -Path 'HKLM:\SOFTWARE\Microsoft\Windows\Windows Error Reporting\LocalDumps' -name "DumpFolder" -Value "c:\CrashDumps"
    Set-ItemProperty -Path 'HKLM:\SOFTWARE\Microsoft\Windows\Windows Error Reporting\LocalDumps' -name "DumpCount" -Value 10 -Type DWord
    Set-ItemProperty -Path 'HKLM:\SOFTWARE\Microsoft\Windows\Windows Error Reporting\LocalDumps' -name "DumpType" -Value 2 -Type DWord
    Set-Service -Name WerSvc -StartupType Manual
    ```
*  Po utworzeniu maszyny Wirtualnej na platformie Azure, zaleca się umieszczenie pliku stronicowania na "danych czasowych" dysku, aby zwiększyć wydajność. Można skonfigurować to w następujący sposób:

    ```PowerShell
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Session Manager\Memory Management' -name "PagingFiles" -Value "D:\pagefile"
    ```
W przypadku każdego dysku danych, który jest dołączony do maszyny Wirtualnej, literę dysku woluminu dysku danych czasowych jest zazwyczaj "D." Oznaczenie to może być różne w zależności od liczby dostępnych dysków i ustawienia, które można wprowadzić.

## <a name="next-steps"></a>Kolejne kroki
* [Przekaż obraz maszyny Wirtualnej systemu Windows Azure dla wdrożenia usługi Resource Manager](upload-generalized-managed.md)
* [Rozwiązywanie problemów aktywacji maszyny wirtualnej systemu Windows Azure](troubleshoot-activation-problems.md)

