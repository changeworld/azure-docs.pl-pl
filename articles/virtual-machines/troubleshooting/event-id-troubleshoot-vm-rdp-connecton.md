---
title: Rozwiązywanie problemów z wg Identyfikatora zdarzenia, problemy z połączeniem RDP maszyny Wirtualnej platformy Azure | Dokumentacja firmy Microsoft
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
ms.openlocfilehash: 4c783c70217a84bbe5ccf15accc4a2bec0b7cca8
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "61485519"
---
# <a name="troubleshoot-azure-vm-rdp-connection-issues-by-event-id"></a>Rozwiązywanie problemów z połączeniem RDP maszyny wirtualnej platformy Azure według identyfikatora zdarzenia 

W tym artykule opisano sposób rozwiązywania problemów, które uniemożliwiają połączenie pulpitu zdalnego protocol (RDP) do maszyny wirtualnej (maszyny Wirtualnej platformy Azure) przy użyciu identyfikatorów zdarzeń.

## <a name="symptoms"></a>Objawy

Spróbuj nawiązać połączenie z Maszyną wirtualną platformy Azure przy użyciu sesji pulpitu zdalnego protocol (RDP). Po należy wprowadzić swoje poświadczenia, połączenie kończy się niepowodzeniem i pojawi się następujący komunikat o błędzie:

**Ten komputer nie może połączyć się z komputerem zdalnym. Spróbuj połączyć się ponownie, jeśli problem będzie się powtarzał, skontaktuj się z właścicielem komputera zdalnego lub z administratorem sieci.**

Aby rozwiązać ten problem, przejrzyj dzienniki zdarzeń na maszynie Wirtualnej, a następnie odwołuje się do następujących scenariuszy.

## <a name="before-you-troubleshoot"></a>Przed Rozwiązywanie problemów

### <a name="create-a-backup-snapshot"></a>Tworzenie migawki kopii zapasowej

Aby utworzyć migawkę kopii zapasowej, wykonaj kroki opisane w [Tworzenie migawki dysku](../windows/snapshot-copy-managed-disk.md).

### <a name="connect-to-the-vm-remotely"></a>Zdalne ustanawianie połączenia z maszyną Wirtualną

Aby zdalnie połączyć się z maszyną wirtualną, należy użyć jednej z metod w [sposobu rozwiązywania problemów maszyny Wirtualnej platformy Azure przy użyciu narzędzi zdalnych](remote-tools-troubleshoot-azure-vm-issues.md).

## <a name="scenario-1"></a>Scenariusz 1

### <a name="event-logs"></a>Dzienniki zdarzeń

W wystąpieniu CMD uruchom następujące polecenia, aby sprawdzić, czy zdarzenie 1058 lub 1057 są rejestrowane w dzienniku systemu, w ciągu ostatnich 24 godzin:

```cmd
wevtutil qe system /c:1 /f:text /q:"Event[System[Provider[@Name='Microsoft-Windows-TerminalServices-RemoteConnectionManager'] and EventID=1058 and TimeCreated[timediff(@SystemTime) <= 86400000]]]" | more
wevtutil qe system /c:1 /f:text /q:"Event[System[Provider[@Name='Microsoft-Windows-TerminalServices-RemoteConnectionManager'] and EventID=1057 and TimeCreated[timediff(@SystemTime) <= 86400000]]]" | more
```

**Nazwa dziennika:**      System <br />
**Źródło:**        Microsoft-Windows-TerminalServices-RemoteConnectionManager <br />
**Data:** *czasu* <br />
**Identyfikator zdarzenia:**      1058 <br />
**Kategoria zadania:** Brak <br />
**Poziom:**         Błąd <br />
**Słowa kluczowe:**      Wdrożenie klasyczne <br />
**Użytkownik:**          ND <br />
**Komputer:** *komputera* <br />
**Opis:** Serwer hosta sesji usług pulpitu zdalnego nie powiodło się zastąpić wygasłego certyfikatu używany do uwierzytelniania serwera hosta sesji usług pulpitu zdalnego w połączeniach SSL z podpisem własnym. Odpowiedni kod stanu była odmowa dostępu.

**Nazwa dziennika:**      System <br />
**Źródło:**        Microsoft-Windows-TerminalServices-RemoteConnectionManager <br />
**Data:** *czasu* <br />
**Identyfikator zdarzenia:**      1058 <br />
**Kategoria zadania:** Brak <br />
**Poziom:**         Błąd <br />
**Słowa kluczowe:**      Wdrożenie klasyczne <br />
**Użytkownik:**          ND <br />
**Komputer:** *komputera* <br />
**Opis:** Serwer hosta sesji usług pulpitu zdalnego nie udało się utworzyć nowy certyfikat z podpisem własnym służący do uwierzytelniania serwera hosta sesji usług pulpitu zdalnego w połączeniach SSL, odpowiedni kod stanu to obiekt już istnieje.

**Nazwa dziennika:**      System <br />
**Źródło:**        Microsoft-Windows-TerminalServices-RemoteConnectionManager <br />
**Data:** *czasu* <br />
**Identyfikator zdarzenia:**      1057 <br />
**Kategoria zadania:** Brak <br />
**Poziom:**         Błąd <br />
**Słowa kluczowe:**      Wdrożenie klasyczne <br />
**Użytkownik:**          ND <br />
**Komputer:** *komputera* <br />
**Opis:** Serwer hosta sesji usług pulpitu zdalnego nie powiodło się tworzenie nowego certyfikatu używanego do uwierzytelniania serwera hosta sesji usług pulpitu zdalnego w połączeniach SSL z podpisem własnym. Odpowiedni kod stanu to zestaw kluczy nie istnieje.

Możesz również sprawdzić dla zdarzenia błędów kanału SCHANNEL 36872 i 36870, uruchamiając następujące polecenia:

```cmd
wevtutil qe system /c:1 /f:text /q:"Event[System[Provider[@Name='Schannel'] and EventID=36870 and TimeCreated[timediff(@SystemTime) <= 86400000]]]" | more
wevtutil qe system /c:1 /f:text /q:"Event[System[Provider[@Name='Schannel'] and EventID=36872 and TimeCreated[timediff(@SystemTime) <= 86400000]]]" | more
```

**Nazwa dziennika:**      System <br />
**Źródło:**        Dostawca Schannel <br />
**Data:** — <br />
**Identyfikator zdarzenia:**      36870 <br />
**Kategoria zadania:** Brak <br />
**Poziom:**         Błąd <br />
**Słowa kluczowe:**       <br />
**Użytkownik:**          SYSTEM <br />
**Komputer:** *komputera* <br />
**Opis:** Wystąpił błąd krytyczny podczas próby uzyskania dostępu do klucza prywatnego poświadczeń SSL serwera. Kod błędu zwrócony z modułu kryptograficznego: 0x8009030d.  <br />
Stan błędu wewnętrznego jest 10001.

### <a name="cause"></a>Przyczyna
Ten problem występuje, ponieważ nie można uzyskać dostępu do lokalnych kluczy szyfrowania RSA w folderze MachineKeys na maszynie Wirtualnej. Ten problem może wystąpić jeden z następujących powodów:

1. Konfiguracja problem uprawnień w folderze Machinekeys lub pliki RSA.

2. Uszkodzone lub brakuje klucza RSA.

### <a name="resolution"></a>Rozwiązanie

Aby rozwiązać ten problem, należy skonfigurować odpowiednie uprawnienia dotyczące certyfikatu protokołu RDP, wykonując następujące kroki.

#### <a name="grant-permission-to-the-machinekeys-folder"></a>Udziel uprawnień w folderze MachineKeys

1. Aby utworzyć skrypt, używając następującej zawartości:

   ```powershell
   remove-module psreadline 
   icacls C:\ProgramData\Microsoft\Crypto\RSA\MachineKeys /t /c > c:\temp\BeforeScript_permissions.txt
   takeown /f "C:\ProgramData\Microsoft\Crypto\RSA\MachineKeys" /a /r
   icacls C:\ProgramData\Microsoft\Crypto\RSA\MachineKeys /t /c /grant "NT AUTHORITY\System:(F)"
   icacls C:\ProgramData\Microsoft\Crypto\RSA\MachineKeys /t /c /grant "NT AUTHORITY\NETWORK SERVICE:(R)"
   icacls C:\ProgramData\Microsoft\Crypto\RSA\MachineKeys /t /c /grant "BUILTIN\Administrators:(F)"
   icacls C:\ProgramData\Microsoft\Crypto\RSA\MachineKeys /t /c > c:\temp\AfterScript_permissions.txt
   Restart-Service TermService -Force
   ```

2.  Uruchom ten skrypt, aby zresetować uprawnień folderu elementu MachineKey i zresetować pliki RSA do wartości domyślnych.

3.  Spróbuj ponownie uzyskać dostęp maszyny Wirtualnej.

Po uruchomieniu skryptu można sprawdzić następujące pliki, które występują problemy z uprawnieniami:

* c:\temp\BeforeScript_permissions.txt
* c:\temp\AfterScript_permissions.txt

#### <a name="renew-rdp-self-signed-certificate"></a>Odnów certyfikat z podpisem własnym protokołu RDP

Jeśli problem będzie się powtarzać, uruchom następujący skrypt, aby upewnić się, że zostanie odnowiony certyfikat z podpisem własnym protokołu RDP:

```powershell
Import-Module PKI
Set-Location Cert:\LocalMachine
$RdpCertThumbprint = 'Cert:\LocalMachine\Remote Desktop\'+((Get-ChildItem -Path 'Cert:\LocalMachine\Remote Desktop\').thumbprint)
Remove-Item -Path $RdpCertThumbprint
Stop-Service -Name "SessionEnv"
Start-Service -Name "SessionEnv"
```

Jeśli nie można odnowić certyfikat, wykonaj następujące kroki, aby spróbować usunąć certyfikat:

1. Na inną maszynę Wirtualną w tej samej sieci Wirtualnej, otwórz **Uruchom** wpisz **mmc**, a następnie naciśnij klawisz **OK**. 

2. Na **pliku** menu, wybierz opcję **Dodaj/Usuń przystawkę**.

3. W **dostępne przystawki** listy wybierz **certyfikaty**, a następnie wybierz pozycję **Dodaj**.

4. Wybierz **konto komputera**, a następnie wybierz pozycję **dalej**.

5. Wybierz **inny komputer**, a następnie dodaj adres IP maszyny Wirtualnej, która ma problemy.
   >[!Note]
   >Spróbuj skorzystać z siecią wewnętrzną uniknięcia wirtualnego adresu IP.

6. Wybierz **Zakończ**, a następnie wybierz pozycję **OK**.

   ![Wybierz komputer](./media/event-id-troubleshoot-vm-rdp-connecton/select-computer.png)

7. Rozwiń certyfikatów, przejdź do folderu Desktop\Certificates zdalnego, kliknij prawym przyciskiem myszy certyfikat, a następnie wybierz **Usuń**.

8. Uruchom ponownie usługę konfiguracji pulpitu zdalnego:

   ```cmd
   net stop SessionEnv
   net start SessionEnv
   ```

   >[!Note]
   >W tym momencie po odświeżeniu magazynu z programu mmc, certyfikat zostanie wyświetlony ponownie. 

Spróbuj uzyskać dostęp do maszyny Wirtualnej, ponownie przy użyciu protokołu RDP.

#### <a name="update-secure-sockets-layer-ssl-certificate"></a>Zaktualizuj certyfikat protokołu Secure Sockets Layer (SSL)

Jeśli maszyna wirtualna jest skonfigurowany do użycia certyfikatu SSL, uruchom następujące polecenie, aby uzyskać odcisk palca. Następnie sprawdź, czy jest to ten sam odcisk palca certyfikatu:

```cmd
reg query "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v SSLCertificateSHA1Hash
```

Jeśli nie, należy zmienić odcisk palca:

```cmd
reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v SSLCertificateSHA1Hash /t REG_BINARY /d <CERTIFICATE THUMBPRINT>
```

Można również próbować usunąć klucz, tak aby protokołu RDP, używa certyfikatu z podpisem własnym dla protokołu RDP:

```cmd
reg delete "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v SSLCertificateSHA1Hash
```

## <a name="scenario-2"></a>Scenariusz 2

### <a name="event-log"></a>Dziennik zdarzeń

W wystąpieniu CMD uruchom następujące polecenia, aby sprawdzić, czy zdarzenie błędu SCHANNEL 36871 są rejestrowane w dzienniku systemu w ciągu ostatnich 24 godzin:

```cmd
wevtutil qe system /c:1 /f:text /q:"Event[System[Provider[@Name='Schannel'] and EventID=36871 and TimeCreated[timediff(@SystemTime) <= 86400000]]]" | more
```

**Nazwa dziennika:**      System <br />
**Źródło:**        Dostawca Schannel <br />
**Data:** — <br />
**Identyfikator zdarzenia:**      36871 <br />
**Kategoria zadania:** Brak <br />
**Poziom:**         Błąd <br />
**Słowa kluczowe:**       <br />
**Użytkownik:**          SYSTEM <br />
**Komputer:** *komputera* <br />
**Opis:** Wystąpił błąd krytyczny podczas tworzenia poświadczeń serwera protokołu TLS. Stan błędu wewnętrznego jest 10013.
 
### <a name="cause"></a>Przyczyna

Ten problem jest spowodowany przez zasady zabezpieczeń. Gdy wyłączone są starsze wersje protokołu TLS (na przykład 1.0), dostęp RDP zakończy się niepowodzeniem.

### <a name="resolution"></a>Rozwiązanie

Jako domyślnego protokołu RDP korzysta z protokołu TLS 1.0. Jednak protokół może być zmieniony na protokołu TLS 1.1, która stanowi nowy standard.

Aby rozwiązać ten problem, zobacz [Rozwiązywanie problemów z uwierzytelnianiem za pomocą protokołu RDP połączyć z maszyną Wirtualną platformy Azure](troubleshoot-authentication-error-rdp-vm.md#tls-version).

## <a name="scenario-3"></a>Scenariusz 3

Jeśli zainstalowano **brokera połączeń usług pulpitu zdalnego** roli na maszynie Wirtualnej, sprawdza, czy jest zdarzenie 2056 lub 1296 w ciągu ostatnich 24 godzin. W wystąpieniu CMD uruchom następujące polecenia: 

```cmd
wevtutil qe system /c:1 /f:text /q:"Event[System[Provider[@Name=' Microsoft-Windows-TerminalServices-SessionBroker '] and EventID=2056 and TimeCreated[timediff(@SystemTime) <= 86400000]]]" | more
wevtutil qe system /c:1 /f:text /q:"Event[System[Provider[@Name=' Microsoft-Windows-TerminalServices-SessionBroker-Client '] and EventID=1296 and TimeCreated[timediff(@SystemTime) <= 86400000]]]" | more
```

**Nazwa dziennika:**      Microsoft-Windows-TerminalServices-SessionBroker/Operational <br />
**Źródło:**        Microsoft-Windows-TerminalServices-SessionBroker <br />
**Data:** *czasu* <br />
**Identyfikator zdarzenia:**      2056 <br />
**Kategoria zadania:** (109) <br />
**Poziom:**         Błąd <br />
**Słowa kluczowe:**       <br />
**Użytkownik:**          USŁUGA SIECIOWA <br />
**Komputer:** *nazwy fqdn komputera* <br />
**Opis:** Nie można odnaleźć opis 2056 identyfikator zdarzenia ze źródła Microsoft-Windows-TerminalServices-SessionBroker. Składnik, który wywołuje to zdarzenie nie jest zainstalowany na komputerze lokalnym albo instalacja jest uszkodzona. Można zainstalować lub naprawić składnik na komputerze lokalnym. <br />
Jeśli zdarzenie pochodzi z innego komputera, wyświetlane informacje musiały zostać zapisane w ze zdarzeniem. <br />
Zdarzenie dołączono następujące informacje: <br />
NULL <br />
NULL <br />
Logowanie do bazy danych nie powiodło się.

**Nazwa dziennika:**      Microsoft-Windows-TerminalServices-SessionBroker-Client/Operational <br />
**Źródło:**        Microsoft-Windows-TerminalServices-SessionBroker-Client <br />
**Data:** *czasu* <br />
**Identyfikator zdarzenia:**      1296 <br />
**Kategoria zadania:** (104) <br />
**Poziom:**         Błąd <br />
**Słowa kluczowe:**       <br />
**Użytkownik:**          USŁUGA SIECIOWA <br />
**Komputer:** *nazwy fqdn komputera* <br />
**Opis:** Nie można odnaleźć opis 1296 identyfikator zdarzenia ze źródła Microsoft-Windows-TerminalServices-SessionBroker-Client. Składnik, który wywołuje to zdarzenie nie jest zainstalowany na komputerze lokalnym albo instalacja jest uszkodzona. Można zainstalować lub naprawić składnik na komputerze lokalnym.
Jeśli zdarzenie pochodzi z innego komputera, wyświetlane informacje musiały zostać zapisane w ze zdarzeniem.
Zdarzenie dołączono następujące informacje:  <br />
*text* <br />
*text* <br />
Broker połączeń usług pulpitu zdalnego nie jest gotowy do komunikacji RPC.

### <a name="cause"></a>Przyczyna

Ten problem występuje, ponieważ zmieniona nazwa hosta serwera usługi Broker połączeń usług pulpitu zdalnego, który nie jest obsługiwana zmiana. 

Nazwa hosta ma wpisów i zależności na Windows wewnętrznej bazy danych, która jest wymagana przez farmy usługi pulpitu zdalnego, aby móc pracować. Zmienianie nazwy hosta, po wbudowanych farmy powoduje, że wiele błędów i może spowodować, że serwer brokera przestanie działać.

### <a name="resolution"></a>Rozwiązanie 

Aby rozwiązać ten problem, wymaga ponownego zainstalowania roli brokera połączeń usług pulpitu zdalnego i Windows wewnętrznej bazy danych.

## <a name="next-steps"></a>Następne kroki

[Zdarzenia kanału Schannel](https://technet.microsoft.com/library/dn786445(v=ws.11).aspx)

[Omówienie techniczne dostawcy obsługi zabezpieczeń Schannel](https://technet.microsoft.com/library/dn786429(v=ws.11).aspx)

[Protokół RDP zakończy się niepowodzeniem z identyfikator zdarzenia 1058 & zdarzenia 36870 za pomocą certyfikatu hosta sesji usług pulpitu zdalnego i komunikacji SSL](https://blogs.technet.microsoft.com/askperf/2014/10/22/rdp-fails-with-event-id-1058-event-36870-with-remote-desktop-session-host-certificate-ssl-communication/)

[Dostawca Schannel 36872 lub Schannel 36870 na kontrolerze domeny](https://blogs.technet.microsoft.com/instan/2009/01/05/schannel-36872-or-schannel-36870-on-a-domain-controller/)

[Identyfikator zdarzenia 1058 — Usług pulpitu zdalnego uwierzytelniania i szyfrowania](https://technet.microsoft.com/library/ee890862(v=ws.10).aspx)

