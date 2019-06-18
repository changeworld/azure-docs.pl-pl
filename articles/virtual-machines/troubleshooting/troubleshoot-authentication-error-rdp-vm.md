---
title: Rozwiązywanie problemów z uwierzytelnianiem za pomocą protokołu RDP połączyć z maszyną Wirtualną platformy Azure | Dokumentacja firmy Microsoft
description: ''
services: virtual-machines-windows
documentationcenter: ''
author: Deland-Han
manager: cshepard
editor: ''
tags: ''
ms.service: virtual-machines
ms.topic: troubleshooting
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: azurecli
ms.date: 11/01/2018
ms.author: delhan
ms.openlocfilehash: 47d3b827099d3a4a7520ac66765d2928795b6e49
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60594921"
---
# <a name="troubleshoot-authentication-errors-when-you-use-rdp-to-connect-to-azure-vm"></a>Rozwiązywanie problemów z uwierzytelnianiem za pomocą protokołu RDP połączyć z maszyną Wirtualną platformy Azure

Ten artykuł ułatwia rozwiązywanie problemów dotyczących błędów uwierzytelniania, które występują, gdy połączenia protokołu RDP (Remote Desktop) umożliwia łączenie z maszyną wirtualną (VM) platformy Azure.

## <a name="symptoms"></a>Objawy

Możesz przechwycić zrzut ekranu przedstawiający wyświetlony ekran powitalny, która wskazuje, że jest uruchomiony system operacyjny maszyny Wirtualnej Azure. Jednak podczas nawiązywania połączenia z maszyną wirtualną przy użyciu usługi Podłączanie pulpitu zdalnego, otrzymasz następujący komunikat o błędzie.

### <a name="error-message-1"></a>Komunikat o błędzie 1

**Wystąpił błąd uwierzytelniania. Nie można skontaktować się z urzędem zabezpieczeń lokalnych.**

### <a name="error-message-2"></a>Komunikat o błędzie 2

**Komputera zdalnego, w którym próbujesz nawiązać połączenie z wymaga sieci poziom Uwierzytelnianiem, ale nie można skontaktować się z kontrolerem domeny w Windows do wykonywania uwierzytelniania na poziomie sieci. Jeśli jesteś administratorem na komputerze zdalnym, można wyłączyć NLA, korzystając z opcji na karcie zdalny w oknie dialogowym Właściwości systemu.**

### <a name="error-message-3-generic-connection-error"></a>Komunikat o błędzie 3 (błąd połączenia ogólnego)

**Ten komputer nie może połączyć się z komputerem zdalnym. Spróbuj połączyć się ponownie, jeśli problem będzie się powtarzał, skontaktuj się z właścicielem komputera zdalnego lub z administratorem sieci.**

## <a name="cause"></a>Przyczyna

Istnieje wiele powodów dlaczego NLA mogą blokować dostęp RDP z maszyną wirtualną.

### <a name="cause-1"></a>Przyczyny 1

Maszyna wirtualna nie może komunikować się z kontrolerem domeny (DC). Ten problem może uniemożliwić dostęp do maszyny Wirtualnej przy użyciu poświadczeń domeny sesję RDP. Jednak nadal będzie mógł zalogować się przy użyciu poświadczeń administratora lokalnego. Ten problem może wystąpić w następujących sytuacjach:

1. Kanału zabezpieczeń Active Directory, między tej maszyny Wirtualnej i kontroler domeny został przerwany.

2. Maszyna wirtualna ma stara kopia hasło do konta i kontroler domeny ma nowszą kopię.

3. Kontroler domeny, który łączy się ta maszyna wirtualna jest w złej kondycji.

### <a name="cause-2"></a>Przyczyny 2

Poziom szyfrowania maszyny wirtualnej jest wyższa niż ten, który jest używany przez komputer kliencki.

### <a name="cause-3"></a>Przyczyny 3

Protokoły TLS 1.0, 1.1 lub 1.2 (server) są wyłączone na maszynie Wirtualnej.

### <a name="cause-4"></a>Przyczyna 4

Maszyna wirtualna została skonfigurowana do wyłączyć logowania przy użyciu poświadczeń domeny, a urząd zabezpieczeń lokalnych (LSA) jest nieprawidłowo skonfigurowana.

### <a name="cause-5"></a>Przyczyny 5

Maszyna wirtualna została skonfigurowana pod kątem akceptowania tylko informacje o przetwarzaniu Standard FIPS (Federal)-algorytmu zgodnego połączenia. Zwykle odbywa się przy użyciu zasad usługi Active Directory. Jest to konfiguracja rzadkich, ale ze standardem FIPS można wymusić tylko połączeń usług pulpitu zdalnego.

## <a name="before-you-troubleshoot"></a>Przed Rozwiązywanie problemów

### <a name="create-a-backup-snapshot"></a>Tworzenie migawki kopii zapasowej

Aby utworzyć migawkę kopii zapasowej, wykonaj kroki opisane w [Tworzenie migawki dysku](../windows/snapshot-copy-managed-disk.md).

### <a name="connect-to-the-vm-remotely"></a>Zdalne ustanawianie połączenia z maszyną Wirtualną

Aby zdalnie połączyć się z maszyną wirtualną, należy użyć jednej z metod w [sposobu rozwiązywania problemów maszyny Wirtualnej platformy Azure przy użyciu narzędzi zdalnych](remote-tools-troubleshoot-azure-vm-issues.md).

### <a name="group-policy-client-service"></a>Usługa klienta zasad grupy

Jeśli jest to maszyna wirtualna przyłączonych do domeny, należy najpierw zatrzymać usługę klienta zasad grupy, aby zapobiec zastąpieniu zmiany wszystkie zasady usługi Active Directory. Aby to zrobić, uruchom następujące polecenie:

```cmd
REM Disable the member server to retrieve the latest GPO from the domain upon start
REG add "HKLM\SYSTEM\CurrentControlSet\Services\gpsvc" /v Start /t REG_DWORD /d 4 /f
```

Po usunięciu problemu Przywróć możliwości tej maszyny wirtualnej, skontaktuj się z domeny w celu pobrania najnowszych zasad grupy z domeny. Aby to zrobić, uruchom następujące polecenia:

```cmd
sc config gpsvc start= auto
sc start gpsvc

gpupdate /force
```

Jeśli zmiana zostanie wycofana, oznacza to, czy przyczyną problemu jest zasady usługi Active Directory. 

### <a name="workaround"></a>Obejście

Aby obejść ten problem, uruchom następujące polecenia w oknie wiersza polecenia, aby wyłączyć uwierzytelniania na poziomie sieci:

```cmd
REM Disable the Network Level Authentication
reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v SecurityLayer /t REG_DWORD /d 0
reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v UserAuthentication /t REG_DWORD /d 0
reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v fAllowSecProtocolNegotiation /t REG_DWORD /d 0
```

Następnie należy ponownie uruchomić maszynę Wirtualną.

Aby ponownie włączyć NLA, uruchom następujące polecenie, a następnie uruchom ponownie maszynę Wirtualną:

```cmd
REG add "HKLM\SYSTEM\CurrentControlSet\Control\Lsa" /v disabledomaincreds /t REG_DWORD /d 0 /f

REG add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v SecurityLayer /t REG_DWORD /d 1 /f
REG add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v UserAuthentication /t REG_DWORD /d 1 /f
REG add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v fAllowSecProtocolNegotiation /t REG_DWORD /d 1 /f
```

## <a name="troubleshooting"></a>Rozwiązywanie problemów

### <a name="for-domain-joined-vms"></a>Dla maszyn wirtualnych z przyłączonym do domeny

Aby rozwiązać ten problem, najpierw sprawdzić, czy maszyna wirtualna może połączyć się kontrolera domeny i tego, czy kontroler domeny ma stan "w dobrej kondycji" i może obsługiwać żądań z maszyny Wirtualnej.

>[!Note] 
>Aby przetestować kondycji kontrolera domeny, można użyć innej maszyny Wirtualnej w tej samej sieci Wirtualnej i podsieci, które współdzielą ten sam serwer logowania.

Nawiązywanie połączenia z maszyny Wirtualnej, którego dotyczy problem, za pomocą konsoli szeregowej, CMD zdalnego lub zdalnej programu PowerShell, zgodnie z instrukcjami w sekcji "Nawiązać połączenia zdalne z maszyną wirtualną".

Aby określić kontroler domeny, które nawiązuje połączenie maszyna wirtualna, uruchom następujące polecenie w konsoli: 

```cmd
set | find /i "LOGONSERVER"
```

Następnie należy sprawdzić kondycję bezpiecznego kanału między maszyną Wirtualną i kontroler domeny. Aby to zrobić, uruchom następujące polecenie w wystąpieniu programu PowerShell z podwyższonym poziomem uprawnień. To polecenie zwraca flagę logiczną, wskazującą, czy bezpieczny kanał jest aktywna:

```powershell
Test-ComputerSecureChannel -verbose
```

Jeśli kanał jest uszkodzona, uruchom następujące polecenie, aby go naprawić:

```powershell
Test-ComputerSecureChannel -repair
```

Upewnij się, że hasło konta komputera w usłudze Active Directory został zaktualizowany na maszynie Wirtualnej i kontroler domeny:

```powershell
Reset-ComputerMachinePassword -Server "<COMPUTERNAME>" -Credential <DOMAIN CREDENTIAL WITH DOMAIN ADMIN LEVEL>
```

Jeśli komunikacja między kontrolerem domeny i maszyny Wirtualnej jest dobry, ale kontroler domeny nie jest dobra, aby otworzyć sesję RDP, możesz spróbować uruchomić ponownie kontroler domeny.

Jeśli w poprzednich poleceniach nie czy rozwiązanie problemu z komunikacją z domeną, możesz ponownie dołączyć do tej maszyny Wirtualnej do domeny. W tym celu wykonaj następujące kroki:

1. Utwórz skrypt, który nosi nazwę Unjoin.ps1 przy użyciu następującej zawartości, a następnie wdrożyć skrypt jako niestandardowego rozszerzenia skryptu w witrynie Azure portal:

    ```cmd
    cmd /c "netdom remove <<MachineName>> /domain:<<DomainName>> /userD:<<DomainAdminhere>> /passwordD:<<PasswordHere>> /reboot:10 /Force"
    ```
    
    Ten skrypt wymuszone przejście maszyny Wirtualnej spoza domeny i ponownie go uruchamia na 10 sekund później. Następnie należy wyczyścić obiektu komputera, na stronie domeny.

2.  Po zakończeniu czyszczenia ponownie dołączyć tę maszynę Wirtualną do domeny. W tym celu należy utworzyć skrypt o nazwie JoinDomain.ps1 przy użyciu następującej zawartości, a następnie wdrożyć skrypt jako niestandardowego rozszerzenia skryptu w witrynie Azure portal: 

    ```cmd
    cmd /c "netdom join <<MachineName>> /domain:<<DomainName>> /userD:<<DomainAdminhere>> /passwordD:<<PasswordHere>> /reboot:10"
    ```

    >[!Note] 
    >To dołącza do maszyny Wirtualnej w domenie przy użyciu podanych poświadczeń.

Jeśli kanał usługi Active Directory jest w dobrej kondycji, hasło komputera jest aktualizowana, a kontroler domeny działa zgodnie z oczekiwaniami, spróbuj wykonać poniższe czynności.

Jeśli problem będzie nadal występować, sprawdź, czy poświadczenia domeny jest wyłączona. W tym celu należy otworzyć podwyższone okno Wiersz polecenia, a następnie uruchom następujące polecenie, aby określić, czy maszyna wirtualna jest skonfigurowany do wyłączania kont domeny do logowania się do maszyny Wirtualnej:

```cmd
REG query "HKLM\SYSTEM\CurrentControlSet\Control\Lsa" /v disabledomaincreds
```

Jeśli jest ustawiona na **1**, oznacza to, że serwer został ustawiony w usłudze nie zezwalaj na poświadczenia domeny. Należy zmienić wartość tego klucza do **0**.

### <a name="for-standalone-vms"></a>Dla autonomicznych maszyn wirtualnych

#### <a name="check-minencryptionlevel"></a>Sprawdź MinEncryptionLevel

W wystąpieniu CMD, uruchom następujące polecenie, aby wykonać zapytanie **MinEncryptionLevel** wartości rejestru:

```cmd
reg query "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v MinEncryptionLevel
```

Oparte na wartości rejestru, wykonaj następujące kroki:

* 4 (FIPS): Przejdź do [połączenia zgodnych algorytmów FIPs Sprawdź](#fips-compliant).

* 3 (128-bitowe szyfrowanie): Ustaw ważność **2** , uruchamiając następujące polecenie:

    ```cmd
    reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v MinEncryptionLevel /t REG_DWORD /d 2 /f
    ```

* 2 (najwyższy szyfrowania to możliwe, zgodnie z ustawieniem klienta): Możesz ustawić minimalną wartość szyfrowania **1** , uruchamiając następujące polecenie:

    ```cmd
    reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v MinEncryptionLevel /t REG_DWORD /d 1 /f
    ```
    
Tak, aby zmiany w rejestrze wprowadzone, uruchom ponownie maszynę Wirtualną.

#### <a name="tls-version"></a>Wersji protokołu TLS

W zależności od systemu protokołu RDP korzysta z protokołu TLS 1.0, 1.1 lub 1.2 (serwer). Aby zbadać, jak te protokoły są konfigurowane na maszynie Wirtualnej, otwórz wystąpienie CMD, a następnie uruchom następujące polecenia:

```cmd
reg query "HKLM\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.0\Server" /v Enabled
reg query "HKLM\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.1\Server" /v Enabled
reg query "HKLM\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Server" /v Enabled
```

Jeśli zwracane wartości nie są wszystkie **1**, oznacza to, że protokół jest wyłączona. Aby włączyć te protokoły, uruchom następujące polecenia:

```cmd
reg add "HKLM\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.0\Server" /v Enabled /t REG_DWORD /d 1 /f
reg add "HKLM\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.1\Server" /v Enabled /t REG_DWORD /d 1 /f
reg add "HKLM\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Server" /v Enabled /t REG_DWORD /d 1 /f
```

W przypadku innych wersji protokołu można uruchomić następujące polecenia:

<pre lang="bat">
reg query "HKLM\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS <i>x.x</i>\Server" /v Enabled
reg query "HKLM\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS <i>x.x</i>\Server" /v Enabled
</pre>

> [!Note]
> Pobierz x.x wersji protokołu SSH/TLS z dzienników systemu operacyjnego gościa na temat błędów kanału SCHANNEL.

#### <a name="fips-compliant"></a> Sprawdź połączenia algorytmów zgodnych ze standardem FIPs

Aby używać tylko zgodne ze standardem FIPs algorytmu połączeń można wymusić usług pulpitu zdalnego. To można ustawić przy użyciu klucza rejestru. Aby to zrobić, Otwórz okno wiersza polecenia z podwyższonym, a następnie utworzyć zapytanie względem następujące klucze:

```cmd
reg query "HKLM\SYSTEM\CurrentControlSet\Control\Lsa\FIPSAlgorithmPolicy" /v Enabled
```

Jeśli polecenie zwraca **1**, zmień wartość rejestru w celu **0**.

```cmd
reg query "HKLM\SYSTEM\CurrentControlSet\Control\Lsa\FIPSAlgorithmPolicy" /v Enabled /t REG_DWORD /d 0
```

Sprawdzanie, który jest bieżącym MinEncryptionLevel na maszynie Wirtualnej:

```cmd
reg query "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v MinEncryptionLevel
```

Jeśli polecenie zwraca **4**, zmień wartość rejestru w celu **2**

```cmd
reg query "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v MinEncryptionLevel /t REG_DWORD /d 2
```

Tak, aby zmiany w rejestrze wprowadzone, uruchom ponownie maszynę Wirtualną.

## <a name="next-steps"></a>Kolejne kroki

[Metoda SetEncryptionLevel klasy Win32_TSGeneralSetting](https://docs.microsoft.com/windows/desktop/TermServ/win32-tsgeneralsetting-setencryptionlevel)

[Konfigurowanie uwierzytelniania serwera i poziomy szyfrowania](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc770833(v=ws.11))

[Klasa Win32_TSGeneralSetting](https://docs.microsoft.com/windows/desktop/TermServ/win32-tsgeneralsetting)
