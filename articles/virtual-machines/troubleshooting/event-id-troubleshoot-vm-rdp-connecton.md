---
title: Rozwiązywanie problemów z połączeniem RDP platformy Azure według identyfikatora zdarzenia | Dokumenty firmy Microsoft
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
ms.openlocfilehash: 166648402eec7f8033c090a3f7862a902bae4be6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "71154205"
---
# <a name="troubleshoot-azure-vm-rdp-connection-issues-by-event-id"></a>Rozwiązywanie problemów z połączeniem RDP maszyny wirtualnej platformy Azure według identyfikatora zdarzenia 

W tym artykule wyjaśniono, jak używać identyfikatorów zdarzeń do rozwiązywania problemów uniemożliwiające połączenie protokołu RDP (Remote Desktop) z maszyną wirtualną platformy Azure.This article explains how to use event IDs to troubleshoot issues that prevent a Remote Desktop protocol (RDP) connection to an Azure Virtual Machine (VM).

## <a name="symptoms"></a>Objawy

Spróbuj użyć sesji protokołu RDP (Remote Desktop), aby połączyć się z maszyną wirtualną platformy Azure. Po wprowadzeniu poświadczeń połączenie zakończy się niepowodzeniem i zostanie wyświetlony następujący komunikat o błędzie:

**Ten komputer nie może połączyć się z komputerem zdalnym. Spróbuj połączyć się ponownie, jeśli problem będzie się powtarzał, skontaktuj się z właścicielem komputera zdalnego lub administratorem sieci.**

Aby rozwiązać ten problem, przejrzyj dzienniki zdarzeń na maszynie Wirtualnej, a następnie zapoznaj się z następującymi scenariuszami.

## <a name="before-you-troubleshoot"></a>Przed podjęciem problemów

### <a name="create-a-backup-snapshot"></a>Tworzenie migawki kopii zapasowej

Aby utworzyć migawkę kopii zapasowej, wykonaj kroki opisane w [obszarze Migawka dysku](../windows/snapshot-copy-managed-disk.md).

### <a name="connect-to-the-vm-remotely"></a>Zdalne łączenie się z maszyną wirtualną

Aby zdalnie połączyć się z maszyną wirtualną, użyj jednej z metod w [obszarze Jak używać narzędzi zdalnych do rozwiązywania problemów z maszyną wirtualną platformy Azure.](remote-tools-troubleshoot-azure-vm-issues.md)

## <a name="scenario-1"></a>Scenariusz 1

### <a name="event-logs"></a>Dzienniki zdarzeń

W wystąpieniu CMD uruchom następujące polecenia, aby sprawdzić, czy zdarzenie 1058 lub zdarzenie 1057 jest rejestrowane w dzienniku systemu w ciągu ostatnich 24 godzin:

```cmd
wevtutil qe system /c:1 /f:text /q:"Event[System[Provider[@Name='Microsoft-Windows-TerminalServices-RemoteConnectionManager'] and EventID=1058 and TimeCreated[timediff(@SystemTime) <= 86400000]]]" | more
wevtutil qe system /c:1 /f:text /q:"Event[System[Provider[@Name='Microsoft-Windows-TerminalServices-RemoteConnectionManager'] and EventID=1057 and TimeCreated[timediff(@SystemTime) <= 86400000]]]" | more
```

**Nazwa dziennika:**      System <br />
**Źródło:**        Menedżer Microsoft-Windows-TerminalServices-RemoteConnectionManager <br />
**Data:**          *godzina* <br />
**Identyfikator zdarzenia:** 1058 <br />
**Kategoria zadania:** Brak <br />
**Poziom:**         Błąd <br />
**Słowa kluczowe:**      Klasyczny <br />
**Użytkownik:**          N/a <br />
**Komputer:**      *komputer* <br />
**Opis:** Serwer hosta sesji usług pulpitu zdalnego nie może zastąpić wygasłego certyfikatu z podpisem własnym, używanego do uwierzytelniania serwera hosta sesji usług pulpitu zdalnego w połączeniach SSL. Odpowiedni kod stanu został odmowa dostępu.

**Nazwa dziennika:**      System <br />
**Źródło:**        Menedżer Microsoft-Windows-TerminalServices-RemoteConnectionManager <br />
**Data:**          *godzina* <br />
**Identyfikator zdarzenia:** 1058 <br />
**Kategoria zadania:** Brak <br />
**Poziom:**         Błąd <br />
**Słowa kluczowe:**      Klasyczny <br />
**Użytkownik:**          N/a <br />
**Komputer:**      *komputer* <br />
**Opis:** Serwer hosta sesji usług pulpitu zdalnego nie może utworzyć nowego certyfikatu z podpisem własnym, który ma być używany do uwierzytelniania serwera hosta sesji usług pulpitu zdalnego w połączeniach SSL, odpowiedni kod stanu już istnieje.

**Nazwa dziennika:**      System <br />
**Źródło:**        Menedżer Microsoft-Windows-TerminalServices-RemoteConnectionManager <br />
**Data:**          *godzina* <br />
**Identyfikator zdarzenia:** 1057 <br />
**Kategoria zadania:** Brak <br />
**Poziom:**         Błąd <br />
**Słowa kluczowe:**      Klasyczny <br />
**Użytkownik:**          N/a <br />
**Komputer:**      *komputer* <br />
**Opis:** Serwer hosta sesji usług pulpitu zdalnego nie może utworzyć nowego certyfikatu z podpisem własnym, który ma być używany do uwierzytelniania serwera hosta sesji usług pulpitu zdalnego w połączeniach SSL. Odpowiedni kod stanu był Keyset nie istnieje

Można również sprawdzić, czy zdarzenia błędów SCHANNEL 36872 i 36870 można również sprawdzić, uruchamiając następujące polecenia:

```cmd
wevtutil qe system /c:1 /f:text /q:"Event[System[Provider[@Name='Schannel'] and EventID=36870 and TimeCreated[timediff(@SystemTime) <= 86400000]]]" | more
wevtutil qe system /c:1 /f:text /q:"Event[System[Provider[@Name='Schannel'] and EventID=36872 and TimeCreated[timediff(@SystemTime) <= 86400000]]]" | more
```

**Nazwa dziennika:**      System <br />
**Źródło:**        Schannel <br />
**Data:** — <br />
**Identyfikator zdarzenia:** 36870 <br />
**Kategoria zadania:** Brak <br />
**Poziom:**         Błąd <br />
**Słowa kluczowe:**       <br />
**Użytkownik:**          System <br />
**Komputer:**      *komputer* <br />
**Opis:** Wystąpił błąd krytyczny podczas próby uzyskania dostępu do klucza prywatnego poświadczeń serwera SSL. Kod błędu zwrócony z modułu kryptograficznego to 0x8009030D.  <br />
Stan błędu wewnętrznego wynosi 10001.

### <a name="cause"></a>Przyczyna
Ten problem występuje, ponieważ nie można uzyskać dostępu do lokalnych kluczy szyfrowania RSA w folderze MachineKeys na maszynie Wirtualnej. Ten problem może wystąpić z jednego z następujących powodów:

1. Nieprawidłowa konfiguracja uprawnień w folderze Machinekeys lub plików RSA.

2. Uszkodzony lub brakujący klucz RSA.

### <a name="resolution"></a>Rozwiązanie

Aby rozwiązać ten problem, należy skonfigurować poprawne uprawnienia do certyfikatu RDP przy użyciu tych kroków.

#### <a name="grant-permission-to-the-machinekeys-folder"></a>Udzielanie uprawnień do folderu MachineKeys

1. Utwórz skrypt przy użyciu następującej zawartości:

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

2.  Uruchom ten skrypt, aby zresetować uprawnienia folderu MachineKey i zresetować pliki RSA do wartości domyślnych.

3.  Spróbuj ponownie uzyskać dostęp do maszyny Wirtualnej.

Po uruchomieniu skryptu można sprawdzić następujące pliki, w których występują problemy z uprawnieniami:

* c:\temp\BeforeScript_permissions.txt
* c:\temp\AfterScript_permissions.txt

#### <a name="renew-rdp-self-signed-certificate"></a>Odnawianie certyfikatu z podpisem rdp

Jeśli problem będzie się powtarzał, uruchom następujący skrypt, aby upewnić się, że certyfikat z podpisem własnym protokołu RDP jest odnawiany:

```powershell
Import-Module PKI
Set-Location Cert:\LocalMachine
$RdpCertThumbprint = 'Cert:\LocalMachine\Remote Desktop\'+((Get-ChildItem -Path 'Cert:\LocalMachine\Remote Desktop\').thumbprint)
Remove-Item -Path $RdpCertThumbprint
Stop-Service -Name "SessionEnv"
Start-Service -Name "SessionEnv"
```

Jeśli nie możesz odnowić certyfikatu, wykonaj następujące czynności, aby spróbować usunąć certyfikat:

1. Na innej maszynie wirtualnej w tej samej sieci wirtualnej otwórz pole **Uruchom,** wpisz **mmc**, a następnie naciśnij **przycisk OK**. 

2. W menu **Plik** wybierz polecenie **Dodaj/Usuń przystawkę**.

3. Na liście **Dostępne przystawki wybierz** pozycję **Certyfikaty**, a następnie wybierz pozycję **Dodaj**.

4. Wybierz **pozycję Konto komputera**, a następnie wybierz pozycję **Dalej**.

5. Wybierz **pozycję Inny komputer**, a następnie dodaj adres IP maszyny Wirtualnej, która ma problemy.
   >[!Note]
   >Spróbuj użyć sieci wewnętrznej, aby uniknąć używania wirtualnego adresu IP.

6. Wybierz **pozycję Zakończ**, a następnie wybierz przycisk **OK**.

   ![Wybierz komputer](./media/event-id-troubleshoot-vm-rdp-connecton/select-computer.png)

7. Rozwiń certyfikaty, przejdź do folderu Pulpit zdalny\Certyfikaty, kliknij prawym przyciskiem myszy certyfikat, a następnie wybierz polecenie **Usuń**.

8. Uruchom ponownie usługę konfiguracji pulpitu zdalnego:

   ```cmd
   net stop SessionEnv
   net start SessionEnv
   ```

   >[!Note]
   >W tym momencie, jeśli odświeżysz magazyn z programu mmc, certyfikat pojawi się ponownie. 

Spróbuj uzyskać dostęp do maszyny Wirtualnej przy użyciu protokołu RDP ponownie.

#### <a name="update-secure-sockets-layer-ssl-certificate"></a>Aktualizowanie certyfikatu warstwy SSL (Secure Sockets Layer)

Jeśli skonfigurowano maszynę wirtualną do używania certyfikatu SSL, uruchom następujące polecenie, aby uzyskać odcisk palca. Następnie sprawdź, czy jest taki sam jak odcisk palca certyfikatu:

```cmd
reg query "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v SSLCertificateSHA1Hash
```

Jeśli tak nie jest, zmień odcisk palca:

```cmd
reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v SSLCertificateSHA1Hash /t REG_BINARY /d <CERTIFICATE THUMBPRINT>
```

Można również spróbować usunąć klucz, tak aby prow umowy RDP używał certyfikatu z podpisem własnym dla protokołu RDP:

```cmd
reg delete "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v SSLCertificateSHA1Hash
```

## <a name="scenario-2"></a>Scenariusz 2

### <a name="event-log"></a>Dziennik zdarzeń

W wystąpieniu CMD uruchom następujące polecenia, aby sprawdzić, czy zdarzenie błędu SCHANNEL 36871 jest rejestrowane w dzienniku systemu w ciągu ostatnich 24 godzin:

```cmd
wevtutil qe system /c:1 /f:text /q:"Event[System[Provider[@Name='Schannel'] and EventID=36871 and TimeCreated[timediff(@SystemTime) <= 86400000]]]" | more
```

**Nazwa dziennika:**      System <br />
**Źródło:**        Schannel <br />
**Data:** — <br />
**Identyfikator zdarzenia:** 36871 <br />
**Kategoria zadania:** Brak <br />
**Poziom:**         Błąd <br />
**Słowa kluczowe:**       <br />
**Użytkownik:**          System <br />
**Komputer:**      *komputer* <br />
**Opis:** Wystąpił błąd krytyczny podczas tworzenia poświadczeń serwera TLS. Stan błędu wewnętrznego to 10013.
 
### <a name="cause"></a>Przyczyna

Ten problem jest spowodowany przez zasady zabezpieczeń. Gdy starsze wersje protokołu TLS (takie jak 1.0) są wyłączone, dostęp RDP kończy się niepowodzeniem.

### <a name="resolution"></a>Rozwiązanie

Protokół RDP używa protokołu TLS 1.0 jako protokołu domyślnego. Jednak protokół może zostać zmieniony na TLS 1.1, który jest nowym standardem.

Aby rozwiązać ten problem, zobacz [Rozwiązywanie problemów z błędami uwierzytelniania podczas łączenia się z maszyną wirtualną platformy Azure za pomocą protokołu RDP.](troubleshoot-authentication-error-rdp-vm.md#tls-version)

## <a name="scenario-3"></a>Scenariusz 3

Jeśli zainstalowano rolę **Broker połączeń usług pulpitu zdalnego** na maszynie wirtualnej, sprawdź, czy w ciągu ostatnich 24 godzin wystąpiło zdarzenie 2056 lub zdarzenie 1296. W wystąpieniu CMD uruchom następujące polecenia: 

```cmd
wevtutil qe system /c:1 /f:text /q:"Event[System[Provider[@Name=' Microsoft-Windows-TerminalServices-SessionBroker '] and EventID=2056 and TimeCreated[timediff(@SystemTime) <= 86400000]]]" | more
wevtutil qe system /c:1 /f:text /q:"Event[System[Provider[@Name=' Microsoft-Windows-TerminalServices-SessionBroker-Client '] and EventID=1296 and TimeCreated[timediff(@SystemTime) <= 86400000]]]" | more
```

**Nazwa dziennika:**      Microsoft-Windows-TerminalServices-SessionBroker/Operacyjne <br />
**Źródło:**        Microsoft-Windows-TerminalServices-SessionBroker <br />
**Data:**          *godzina* <br />
**Identyfikator zdarzenia:** 2056 <br />
**Kategoria zadania:** (109) <br />
**Poziom:**         Błąd <br />
**Słowa kluczowe:**       <br />
**Użytkownik:**          USŁUGA SIECIOWA <br />
**Komputer:**      *komputer fqdn* <br />
**Opis:** Nie można odnaleźć opisu zdarzenia o identyfikatorze 2056 ze źródła Microsoft-Windows-TerminalServices-SessionBroker. Składnik, który wywołuje to zdarzenie nie jest zainstalowany na komputerze lokalnym lub instalacja jest uszkodzona. Składnik można zainstalować lub naprawić na komputerze lokalnym. <br />
Jeśli zdarzenie pochodzi z innego komputera, informacje o wyświetlaniu musiały zostać zapisane wraz ze zdarzeniem. <br />
Do zdarzenia uwzględniono następujące informacje: <br />
NULL <br />
NULL <br />
Logowanie do bazy danych nie powiodło się.

**Nazwa dziennika:**      Microsoft-Windows-TerminalServices-SessionBroker-Client/Operacyjne <br />
**Źródło:**        Klient-brokera-klienta microsoft-windows-terminale-sessionbroker <br />
**Data:**          *godzina* <br />
**Identyfikator zdarzenia:** 1296 <br />
**Kategoria zadania:** (104) <br />
**Poziom:**         Błąd <br />
**Słowa kluczowe:**       <br />
**Użytkownik:**          USŁUGA SIECIOWA <br />
**Komputer:**      *komputer fqdn* <br />
**Opis:** Nie można odnaleźć opisu zdarzenia o identyfikatorze 1296 ze źródła Microsoft-Windows-TerminalServices-SessionBroker-Client. Składnik, który wywołuje to zdarzenie nie jest zainstalowany na komputerze lokalnym lub instalacja jest uszkodzona. Składnik można zainstalować lub naprawić na komputerze lokalnym.
Jeśli zdarzenie pochodzi z innego komputera, informacje o wyświetlaniu musiały zostać zapisane wraz ze zdarzeniem.
Do zdarzenia uwzględniono następujące informacje:  <br />
*Tekst* <br />
*Tekst* <br />
Broker połączeń usług pulpitu zdalnego nie jest gotowy do komunikacji RPC.

### <a name="cause"></a>Przyczyna

Ten problem występuje, ponieważ nazwa hosta serwera brokera połączeń usług pulpitu zdalnego została zmieniona, co nie jest obsługiwaną zmianą. 

Nazwa hosta zawiera wpisy i zależności w wewnętrznej bazie danych systemu Windows, która jest wymagana przez farmę usług pulpitu zdalnego, aby móc pracować. Zmiana nazwy hosta po zbudowaniu farmy powoduje wiele błędów i może spowodować, że serwer brokera przestanie działać.

### <a name="resolution"></a>Rozwiązanie 

Aby rozwiązać ten problem, należy ponownie zainstalować rolę Brokera połączeń usług pulpitu zdalnego i wewnętrzną bazę danych systemu Windows.

## <a name="next-steps"></a>Następne kroki

[Zdarzenia Schannel](https://technet.microsoft.com/library/dn786445(v=ws.11).aspx)

[Omówienie techniczne dostawcy obsługi zabezpieczeń Schannel](https://technet.microsoft.com/library/dn786429(v=ws.11).aspx)

[Błąd protokołu RDP z identyfikatorem zdarzenia 1058 & zdarzenie 36870 z certyfikatem hosta sesji usług pulpitu zdalnego & komunikacji SSL](https://blogs.technet.microsoft.com/askperf/2014/10/22/rdp-fails-with-event-id-1058-event-36870-with-remote-desktop-session-host-certificate-ssl-communication/)

[Schannel 36872 lub Schannel 36870 na kontrolerze domeny](https://blogs.technet.microsoft.com/instan/2009/01/05/schannel-36872-or-schannel-36870-on-a-domain-controller/)

[Identyfikator zdarzenia 1058 — Uwierzytelnianie i szyfrowanie usług pulpitu zdalnego](https://technet.microsoft.com/library/ee890862(v=ws.10).aspx)

