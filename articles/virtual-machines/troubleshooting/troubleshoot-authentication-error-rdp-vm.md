---
title: Rozwiązywanie problemów z błędami uwierzytelniania podczas łączenia się z maszyną wirtualną platformy Azure za pomocą protokołu RDP | Dokumenty firmy Microsoft
description: ''
services: virtual-machines-windows
documentationcenter: ''
author: Deland-Han
manager: dcscontentpm
editor: ''
tags: ''
ms.service: virtual-machines
ms.topic: troubleshooting
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: azurecli
ms.date: 11/01/2018
ms.author: delhan
ms.openlocfilehash: b7a561907e3f1968eb9adead3606822d7a1321c8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79266977"
---
# <a name="troubleshoot-authentication-errors-when-you-use-rdp-to-connect-to-azure-vm"></a>Rozwiązywanie problemów z uwierzytelnianiem podczas używania protokołu RDP w celu połączenia z maszyną wirtualną platformy Azure

Ten artykuł może pomóc w rozwiązywaniu problemów z błędami uwierzytelniania, które występują podczas łączenia się z maszyną wirtualną platformy Azure przy użyciu połączenia protokołu RDP (Remote Desktop Protocol).

## <a name="symptoms"></a>Objawy

Przechwytywanie zrzut ekranu maszyny Wirtualnej platformy Azure, który pokazuje ekran powitalny i wskazuje, że system operacyjny jest uruchomiony. Jednak podczas próby nawiązania połączenia z maszyną wirtualną przy użyciu usługi Podłączanie pulpitu zdalnego jest otrzymuje jeden z następujących komunikatów o błędach.

### <a name="error-message-1"></a>Komunikat o błędzie 1

**Wystąpił błąd uwierzytelniania. Nie można skontaktować się z Lokalnym Urzędem Bezpieczeństwa.**

### <a name="error-message-2"></a>Komunikat o błędzie 2

**Komputer zdalny, z którego próbujesz się połączyć, wymaga uwierzytelniania na poziomie sieci (NLA), ale nie można skontaktować się z kontrolerem domeny systemu Windows w celu wykonania nla. Jeśli jesteś administratorem na komputerze zdalnym, możesz wyłączyć nla za pomocą opcji na karcie Zdalne w oknie dialogowym Właściwości systemu.**

### <a name="error-message-3-generic-connection-error"></a>Komunikat o błędzie 3 (ogólny błąd połączenia)

**Ten komputer nie może połączyć się z komputerem zdalnym. Spróbuj połączyć się ponownie, jeśli problem będzie się powtarzał, skontaktuj się z właścicielem komputera zdalnego lub administratorem sieci.**

## <a name="cause"></a>Przyczyna

Istnieje wiele powodów, dla których NLA może zablokować dostęp RDP do maszyny Wirtualnej.

### <a name="cause-1"></a>Przyczyna 1

Maszyna wirtualna nie może komunikować się z kontrolerem domeny(DC). Ten problem może uniemożliwić sesji RDP dostęp do maszyny Wirtualnej przy użyciu poświadczeń domeny. Jednak nadal można zalogować się przy użyciu poświadczeń administratora lokalnego. Ten problem może wystąpić w następujących sytuacjach:

1. Kanał zabezpieczeń usługi Active Directory między tą maszyną wirtualną a kontrolerem domeny jest uszkodzony.

2. Maszyna wirtualna ma starą kopię hasła do konta, a kontroler domeny ma nowszą kopię.

3. Kontroler domeny, z którego łączy się ta maszyna wirtualna, jest w złej kondycji.

### <a name="cause-2"></a>Przyczyna 2

Poziom szyfrowania maszyny Wirtualnej jest wyższy niż ten, który jest używany przez komputer kliencki.

### <a name="cause-3"></a>Przyczyna 3

Protokoły TLS 1.0, 1.1 lub 1.2 (serwer) są wyłączone na maszynie wirtualnej.

### <a name="cause-4"></a>Przyczyna 4

Maszyna wirtualna została skonfigurowana w celu wyłączenia logowania przy użyciu poświadczeń domeny, a urząd zabezpieczeń lokalnych (LSA) jest skonfigurowany niepoprawnie.

### <a name="cause-5"></a>Przyczyna 5

Maszyna wirtualna została skonfigurowana do akceptowania tylko połączeń algorytmów zgodnych ze standardem FIPS (Federal Information Processing Standard). Zwykle odbywa się to przy użyciu zasad usługi Active Directory. Jest to rzadka konfiguracja, ale FIPS można wymusić tylko dla połączeń pulpitu zdalnego.

## <a name="before-you-troubleshoot"></a>Przed podjęciem problemów

### <a name="create-a-backup-snapshot"></a>Tworzenie migawki kopii zapasowej

Aby utworzyć migawkę kopii zapasowej, wykonaj kroki opisane w [obszarze Migawka dysku](../windows/snapshot-copy-managed-disk.md).

### <a name="connect-to-the-vm-remotely"></a>Zdalne łączenie się z maszyną wirtualną

Aby zdalnie połączyć się z maszyną wirtualną, użyj jednej z metod w [obszarze Jak używać narzędzi zdalnych do rozwiązywania problemów z maszyną wirtualną platformy Azure](remote-tools-troubleshoot-azure-vm-issues.md).

### <a name="group-policy-client-service"></a>Obsługa klienta zasad grupy

Jeśli jest to maszyna wirtualna przyłączona do domeny, najpierw zatrzymaj usługę Klienta zasad grupy, aby zapobiec zastępowaniu zmian przez jakiekolwiek zasady usługi Active Directory. Aby to zrobić, uruchom następujące polecenie:

```cmd
REM Disable the member server to retrieve the latest GPO from the domain upon start
REG add "HKLM\SYSTEM\CurrentControlSet\Services\gpsvc" /v Start /t REG_DWORD /d 4 /f
```

Po zażekwii problemu przywróć możliwość skontaktowania się z domeną w celu pobrania najnowszego obiektu zasad grupy z domeny. Aby to zrobić, uruchom następujące polecenia:

```cmd
sc config gpsvc start= auto
sc start gpsvc

gpupdate /force
```

Jeśli zmiana zostanie przywrócona, oznacza to, że przyczyną problemu jest zasada usługi Active Directory. 

### <a name="workaround"></a>Obejście

Aby obejść ten problem, uruchom następujące polecenia w oknie polecenia, aby wyłączyć NLA:

```cmd
REM Disable the Network Level Authentication
reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v SecurityLayer /t REG_DWORD /d 0
reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v UserAuthentication /t REG_DWORD /d 0
reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v fAllowSecProtocolNegotiation /t REG_DWORD /d 0
```

Następnie uruchom ponownie maszynę wirtualną.

Aby ponownie włączyć nla, uruchom następujące polecenie, a następnie uruchom ponownie maszynę wirtualną:

```cmd
REG add "HKLM\SYSTEM\CurrentControlSet\Control\Lsa" /v disabledomaincreds /t REG_DWORD /d 0 /f

REG add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v SecurityLayer /t REG_DWORD /d 1 /f
REG add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v UserAuthentication /t REG_DWORD /d 1 /f
REG add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v fAllowSecProtocolNegotiation /t REG_DWORD /d 1 /f
```

## <a name="troubleshooting"></a>Rozwiązywanie problemów

### <a name="for-domain-joined-vms"></a>Dla maszyn wirtualnych przyłączonych do domeny

Aby rozwiązać ten problem, najpierw sprawdź, czy maszyna wirtualna może łączyć się z kontrolerem domeny i czy kontroler domeny ma stan "w dobrej kondycji" i może obsługiwać żądania z maszyny Wirtualnej.

>[!Note] 
>Aby przetestować kondycję kontrolera domeny, można użyć innej maszyny Wirtualnej na tej samej sieci wirtualnej i podsieci, które współużytkują ten sam serwer logowania.

Połącz się z maszyną wirtualną, która ma problem przy użyciu konsoli szeregowej, zdalnego współrzędnościowego połączenia cmd lub zdalnego programu PowerShell, zgodnie z instrukcjami w sekcji "Połącz się z maszyną wirtualną zdalnie".

Aby ustalić, z którym kontrolerem domeny łączy się maszyna wirtualna, uruchom następujące polecenie w konsoli: 

```cmd
set | find /i "LOGONSERVER"
```

Następnie sprawdź kondycję bezpiecznego kanału między maszyną wirtualną a kontrolerem domeny. Aby to zrobić, uruchom następujące polecenie w wystąpieniu programu PowerShell z podwyższonym poziomem uprawnień. To polecenie zwraca flagę logiczną, która wskazuje, czy bezpieczny kanał jest żywy:

```powershell
Test-ComputerSecureChannel -verbose
```

Jeśli kanał jest uszkodzony, uruchom następujące polecenie, aby go naprawić:

```powershell
Test-ComputerSecureChannel -repair
```

Upewnij się, że hasło konta komputera w usłudze Active Directory jest aktualizowane na maszynie Wirtualnej i kontrolerze domeny:

```powershell
Reset-ComputerMachinePassword -Server "<COMPUTERNAME>" -Credential <DOMAIN CREDENTIAL WITH DOMAIN ADMIN LEVEL>
```

Jeśli komunikacja między kontrolerem domeny a maszyną wirtualną jest dobra, ale kontroler domeny nie jest wystarczająco zdrowy, aby otworzyć sesję RDP, można spróbować ponownie uruchomić kontroler domeny.

Jeśli poprzednie polecenia nie rozwiązały problemu z komunikacją z domeną, można ponownie dołączyć tę maszynę wirtualną do domeny. W tym celu wykonaj następujące kroki:

1. Utwórz skrypt o nazwie Unjoin.ps1 przy użyciu następującej zawartości, a następnie wdrożyć skrypt jako niestandardowe rozszerzenie skryptu w witrynie Azure portal:

    ```cmd
    cmd /c "netdom remove <<MachineName>> /domain:<<DomainName>> /userD:<<DomainAdminhere>> /passwordD:<<PasswordHere>> /reboot:10 /Force"
    ```
    
    Ten skrypt usuwa maszynę wirtualną z domeny na przymusowo i uruchamia ją ponownie 10 sekund później. Następnie należy wyczyścić komputer obiektu po stronie domeny.

2.  Po zakończeniu oczyszczania ponownie dołącz tę maszynę wirtualną do domeny. Aby to zrobić, należy utworzyć skrypt o nazwie JoinDomain.ps1 przy użyciu następującej zawartości, a następnie wdrożyć skrypt jako niestandardowe rozszerzenie skryptu w witrynie Azure portal: 

    ```cmd
    cmd /c "netdom join <<MachineName>> /domain:<<DomainName>> /userD:<<DomainAdminhere>> /passwordD:<<PasswordHere>> /reboot:10"
    ```

    >[!Note] 
    >To łączy maszynę wirtualną w domenie przy użyciu określonych poświadczeń.

Jeśli kanał usługi Active Directory jest w dobrej kondycji, hasło komputera jest aktualizowane, a kontroler domeny działa zgodnie z oczekiwaniami, spróbuj wykonać następujące kroki.

Jeśli problem będzie się powtarzał, sprawdź, czy poświadczenia domeny są wyłączone. Aby to zrobić, otwórz okno wiersza polecenia z podwyższonym poziomem uprawnień, a następnie uruchom następujące polecenie, aby ustalić, czy maszyna wirtualna jest skonfigurowana do wyłączania kont domeny w celu zalogowania się na maszynie wirtualnej:

```cmd
REG query "HKLM\SYSTEM\CurrentControlSet\Control\Lsa" /v disabledomaincreds
```

Jeśli klucz jest ustawiony na **1**, oznacza to, że serwer został skonfigurowany, aby nie zezwalać na poświadczenia domeny. Zmień ten klawisz na **0**.

### <a name="for-standalone-vms"></a>Dla autonomicznych maszyn wirtualnych

#### <a name="check-minencryptionlevel"></a>Sprawdź Poziom MinEncryption

W wystąpieniu CMD uruchom następujące polecenie, aby wykonać kwerendę wartości rejestru **MinEncryptionLevel:**

```cmd
reg query "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v MinEncryptionLevel
```

Na podstawie wartości rejestru wykonaj następujące kroki:

* 4 (FIPS): Przejdź do [sprawdź połączenia algorytmów zgodnych z FIP](#fips-compliant).

* 3 (szyfrowanie 128-bitowe): ustaw ważność na **2,** uruchamiając następujące polecenie:

    ```cmd
    reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v MinEncryptionLevel /t REG_DWORD /d 2 /f
    ```

* 2 (Najwyższe możliwe szyfrowanie, zgodnie z wymaganiami klienta): Możesz spróbować ustawić szyfrowanie na minimalną wartość **1,** uruchamiając następujące polecenie:

    ```cmd
    reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v MinEncryptionLevel /t REG_DWORD /d 1 /f
    ```
    
Uruchom ponownie maszynę wirtualną, aby zmiany w rejestrze zostały wprowadzone.

#### <a name="tls-version"></a>Wersja TLS

W zależności od systemu protokół RDP używa protokołu TLS 1.0, 1.1 lub 1.2 (serwer). Aby zbadać sposób konfigurowania tych protokołów na maszynie Wirtualnej, otwórz wystąpienie cmd, a następnie uruchom następujące polecenia:

```cmd
reg query "HKLM\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.0\Server" /v Enabled
reg query "HKLM\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.1\Server" /v Enabled
reg query "HKLM\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Server" /v Enabled
```

Jeśli zwracane wartości nie są wszystkie **1,** oznacza to, że protokół jest wyłączony. Aby włączyć te protokoły, uruchom następujące polecenia:

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
> Pobierz wersję X.x SSH/TLS z dzienników systemu operacyjnego gościa na błędach SCHANNEL.

#### <a name="check-fips-compliant-algorithms-connections"></a><a name="fips-compliant"></a>Sprawdzanie połączeń algorytmów zgodnych ze standardami FIPs

Pulpit zdalny można wymusić, aby używać tylko połączeń algorytmów zgodnych ze standardem FIPs. Można to ustawić przy użyciu klucza rejestru. Aby to zrobić, otwórz okno wiersza polecenia z podwyższonym poziomem uprawnień, a następnie wykonaj kwerendę z następującymi kluczami:

```cmd
reg query "HKLM\SYSTEM\CurrentControlSet\Control\Lsa\FIPSAlgorithmPolicy" /v Enabled
```

Jeśli polecenie zwraca **wartość 1,** zmień wartość rejestru na **0**.

```cmd
reg query "HKLM\SYSTEM\CurrentControlSet\Control\Lsa\FIPSAlgorithmPolicy" /v Enabled /t REG_DWORD /d 0
```

Sprawdź, który jest bieżący MinEncryptionLevel na maszynie Wirtualnej:

```cmd
reg query "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v MinEncryptionLevel
```

Jeśli polecenie zwraca **wartość 4,** zmień wartość rejestru na **2**

```cmd
reg query "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v MinEncryptionLevel /t REG_DWORD /d 2
```

Uruchom ponownie maszynę wirtualną, aby zmiany w rejestrze zostały wprowadzone.

## <a name="next-steps"></a>Następne kroki

[SetEncryptionNastępnie metody Win32_TSGeneralSetting klasy](https://docs.microsoft.com/windows/desktop/TermServ/win32-tsgeneralsetting-setencryptionlevel)

[Konfigurowanie poziomów uwierzytelniania i szyfrowania serwera](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc770833(v=ws.11))

[klasa Win32_TSGeneralSetting](https://docs.microsoft.com/windows/desktop/TermServ/win32-tsgeneralsetting)
