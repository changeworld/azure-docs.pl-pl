---
title: Zdalne łączenie się z urządzeniem StorSimple | Dokumentacja firmy Microsoft
description: Opisano sposób konfigurowania urządzenia pod kątem zarządzania zdalnego oraz połączyć się z programu Windows PowerShell dla usługi StorSimple za pośrednictwem protokołu HTTP lub HTTPS.
services: storsimple
documentationcenter: ''
author: alkohli
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 01/02/2018
ms.author: alkohli
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 05bec60f4c56c98e9b910b50e858656a2e5554b2
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60631879"
---
# <a name="connect-remotely-to-your-storsimple-8000-series-device"></a>Połącz się zdalnie z urządzenia StorSimple 8000 series

## <a name="overview"></a>Omówienie

Możesz zdalnie nawiązać urządzenia za pomocą programu Windows PowerShell. Po nawiązaniu połączenia w ten sposób, nie ma menu. (Możesz zobaczyć menu tylko wtedy, gdy używasz konsoli szeregowej urządzenia do łączenia). Za pomocą komunikacji zdalnej programu Windows PowerShell połączenie z określonym obszarem działania. Można również określić język wyświetlania.

Aby uzyskać więcej informacji na temat przy użyciu komunikacji zdalnej programu Windows PowerShell do zarządzania urządzeniem, przejdź do [Użyj programu Windows PowerShell dla usługi StorSimple do administrowania urządzeniem StorSimple](storsimple-8000-windows-powershell-administration.md).

W tym samouczku wyjaśniono, jak skonfigurować urządzenie do zdalnego zarządzania, a następnie jak połączyć się z programu Windows PowerShell dla usługi StorSimple. Zdalne połączenia za pośrednictwem programu Windows PowerShell, można użyć protokołu HTTP lub HTTPS. Jednakże wybierając sposób nawiązywania połączeń w programie Windows PowerShell dla usługi StorSimple, należy wziąć pod uwagę następujące informacje:

* Bezpośredniego połączenia z konsolą szeregową urządzenia są bezpieczne, ale nie jest połączenie z konsolą szeregową za pośrednictwem przełączników sieciowych. Należy zachować ostrożność zagrożenie bezpieczeństwa, podczas nawiązywania połączenia z konsolą szeregową urządzenia za pośrednictwem przełączników sieciowych.
* Łącząc się za pośrednictwem sesji HTTP może zaoferować lepsze zabezpieczenia niż łączenie za pośrednictwem konsoli szeregowej za pośrednictwem sieci. Chociaż nie jest najbezpieczniejszą metodą, jest dopuszczalne w sieciach zaufanych.
* Łącząc się za pośrednictwem sesji protokołu HTTPS przy użyciu certyfikatu z podpisem własnym jest najbezpieczniejsza i zalecana opcja.

Można nawiązać zdalnego interfejsu programu Windows PowerShell. Jednak zdalnego dostępu do urządzenia StorSimple za pośrednictwem interfejsu programu Windows PowerShell nie jest włączona domyślnie. Należy najpierw włączyć zarządzanie zdalne na urządzeniu, a następnie na komputerze klienckim umożliwiający dostęp do urządzenia.

Kroki opisane w tym artykule zostały wykonane na komputerze hosta z systemem Windows Server 2012 R2.

## <a name="connect-through-http"></a>Łączenie się za pośrednictwem protokołu HTTP

Łączenie z usługą programu Windows PowerShell dla usługi StorSimple za pośrednictwem sesji HTTP oferuje lepsze zabezpieczenia niż łączenie za pośrednictwem konsoli szeregowej urządzenia StorSimple. Chociaż nie jest najbezpieczniejszą metodą, jest dopuszczalne w sieciach zaufanych.

Aby skonfigurować zdalne zarządzanie, można użyć witryny Azure portal lub konsoli szeregowej. Wybierz jedną z następujących procedur:

* Użyj witryny Azure portal, aby włączyć zarządzanie zdalne za pośrednictwem protokołu HTTP
* [Użyj konsoli szeregowej, aby włączyć zarządzanie zdalne za pośrednictwem protokołu HTTP](#use-the-serial-console-to-enable-remote-management-over-http)

Po włączeniu zdalnego zarządzania, należy użyć poniższej procedury przygotować klienta dla połączenia zdalnego.

* [Przygotuj klienta dla połączenia zdalnego](#prepare-the-client-for-remote-connection)

### <a name="use-the-azure-portal-to-enable-remote-management-over-http"></a>Użyj witryny Azure portal, aby włączyć zarządzanie zdalne za pośrednictwem protokołu HTTP

Wykonaj następujące czynności w witrynie Azure portal, aby włączyć zarządzanie zdalne za pośrednictwem protokołu HTTP.

#### <a name="to-enable-remote-management-through-the-azure-portal"></a>Aby włączyć zarządzanie zdalne za pomocą witryny Azure portal

1. Przejdź do usługi Menedżer urządzeń StorSimple. Wybierz **urządzeń** a następnie wybierz i kliknij urządzenie, którą chcesz skonfigurować do zdalnego zarządzania. Przejdź do **ustawienia urządzenia > zabezpieczeń**.
2. W **ustawienia zabezpieczeń** bloku kliknij **zdalne zarządzanie**.
3. W **zdalne zarządzanie** ustawić bloku **włączyć zdalne zarządzanie** do **tak**.
4. Teraz możesz wybrać opcję połączenia przy użyciu protokołu HTTP. (Wartość domyślna to nawiązać połączenie za pośrednictwem protokołu HTTPS). Upewnij się, że wybrano HTTP.
   
   > [!NOTE]
   > Łączenie za pośrednictwem protokołu HTTP jest dopuszczalne tylko w sieciach zaufanych.
   
5. Kliknij przycisk **Zapisz** i po wyświetleniu monitu o potwierdzenie, wybierz **tak**.

### <a name="use-the-serial-console-to-enable-remote-management-over-http"></a>Użyj konsoli szeregowej, aby włączyć zarządzanie zdalne za pośrednictwem protokołu HTTP
Wykonaj następujące czynności na konsoli szeregowej urządzenia, aby włączyć zdalne zarządzanie.

#### <a name="to-enable-remote-management-through-the-device-serial-console"></a>Aby włączyć zarządzanie zdalne za pośrednictwem konsoli szeregowej urządzenia
1. W menu konsoli szeregowej wybierz opcję 1. Aby uzyskać więcej informacji o używaniu konsoli szeregowej urządzenia, przejdź do [nawiązywanie połączenia z programu Windows PowerShell dla usługi StorSimple za pośrednictwem konsoli szeregowej urządzenia](storsimple-8000-windows-powershell-administration.md#connect-to-windows-powershell-for-storsimple-via-the-device-serial-console).
2. W wierszu polecenia wpisz polecenie: `Enable-HcsRemoteManagement –AllowHttp`
3. Użytkownik jest powiadamiany o luk w zabezpieczeniach do nawiązania połączenia z urządzeniem za pomocą protokołu HTTP. Po wyświetleniu monitu Potwierdź, wpisując **Y**.
4. Sprawdź, czy HTTP jest włączony, wpisując: `Get-HcsSystem`
5. Upewnij się, że **RemoteManagementMode** pola pokazuje **HttpsAndHttpEnabled**. Poniższa ilustracja przedstawia te ustawienia w programie PuTTY.
   
     ![Serial HTTPS i HTTP włączony](./media/storsimple-remote-connect/HCS_SerialHttpsAndHttpEnabled.png)

### <a name="prepare-the-client-for-remote-connection"></a>Przygotuj klienta dla połączenia zdalnego
Wykonaj następujące czynności na komputerze klienckim, aby włączyć zdalne zarządzanie.

#### <a name="to-prepare-the-client-for-remote-connection"></a>Aby przygotować klienta dla połączenia zdalnego
1. Uruchom sesję programu Windows PowerShell jako administrator. Jeśli używasz klienta systemu Windows 10, domyślnie, usługa Windows Remote Management ustawiono na ręcznie. Konieczne może uruchomić usługi, wpisując:

    `Start-Service WinRM`
    
2. Wpisz następujące polecenie, aby dodać adres IP urządzenia StorSimple do listy zaufanych hostów klienta:
   
     `Set-Item wsman:\localhost\Client\TrustedHosts <device_ip> -Concatenate -Force`
   
     Zastąp <*device_ip*> adres IP urządzenia; na przykład: 
   
     `Set-Item wsman:\localhost\Client\TrustedHosts 10.126.173.90 -Concatenate -Force`
3. Wpisz następujące polecenie, aby zapisać poświadczenia urządzenia w zmiennej: 
   
    ```
    $cred = Get-Credential
    ```
    
4. W wyświetlonym oknie dialogowym:
   
   1. Wpisz nazwę użytkownika w następującym formacie: *device_ip\SSAdmin*.
   2. Wpisz hasło administratora urządzenia, która została ustawiona, gdy urządzenie zostało skonfigurowane przy użyciu Kreatora instalacji. Domyślne hasło jest *Password1*.
5. Uruchom sesję programu Windows PowerShell na urządzeniu, wpisując polecenie:
   
     `Enter-PSSession -Credential $cred -ConfigurationName SSAdminConsole -ComputerName <device_ip>`
   
   > [!NOTE]
   > Aby utworzyć sesję programu Windows PowerShell do użytku z urządzeniem wirtualnym StorSimple, należy dołączyć `–Port` parametru i określić port publiczny, który został skonfigurowany w komunikację zdalną dla urządzenia wirtualnego StorSimple.
   
   
W tym momencie powinien mieć aktywnych sesji zdalnej programu Windows PowerShell na urządzeniu.
   
![Komunikacja zdalna programu PowerShell przy użyciu protokołu HTTP](./media/storsimple-remote-connect/HCS_PSRemotingUsingHTTP.png)

## <a name="connect-through-https"></a>Łączenie się za pośrednictwem protokołu HTTPS

Łączenie z usługą programu Windows PowerShell dla usługi StorSimple za pośrednictwem sesji protokołu HTTPS to najbardziej bezpieczna i zalecana metoda zdalnego nawiązywania połączenia z urządzeniem Microsoft Azure StorSimple. Poniższe procedury dotyczą sposobu konfigurowania serial konsoli i komputerów klienckich, dzięki czemu można użyć protokołu HTTPS, połączyć się z programu Windows PowerShell dla usługi StorSimple.

Aby skonfigurować zdalne zarządzanie, można użyć witryny Azure portal lub konsoli szeregowej. Wybierz jedną z następujących procedur:

* Użyj witryny Azure portal, aby włączyć zdalne zarządzanie przy użyciu protokołu HTTPS
* [Użyj konsoli szeregowej, aby włączyć zdalne zarządzanie przy użyciu protokołu HTTPS](#use-the-serial-console-to-enable-remote-management-over-https)

Po włączeniu zdalnego zarządzania, należy użyć poniższych procedur, przygotować hosta do zdalnego zarządzania i połączyć się z urządzeniem z hosta zdalnego.

* [Przygotowanie hosta do zarządzania zdalnego](#prepare-the-host-for-remote-management)
* [Połącz z hostem zdalnym](#connect-to-the-device-from-the-remote-host)

### <a name="use-the-azure-portal-to-enable-remote-management-over-https"></a>Użyj witryny Azure portal, aby włączyć zdalne zarządzanie przy użyciu protokołu HTTPS

Wykonaj następujące czynności w witrynie Azure portal, aby włączyć zdalne zarządzanie przy użyciu protokołu HTTPS.

#### <a name="to-enable-remote-management-over-https-from-the-azure-portal"></a>Aby włączyć zdalne zarządzanie przy użyciu protokołu HTTPS w witrynie Azure portal

1. Przejdź do usługi Menedżer urządzeń StorSimple. Wybierz **urządzeń** a następnie wybierz i kliknij urządzenie, którą chcesz skonfigurować do zdalnego zarządzania. Przejdź do **ustawienia urządzenia > zabezpieczeń**.
2. W **ustawienia zabezpieczeń** bloku kliknij **zdalne zarządzanie**.
3. Ustaw opcję **Włącz zdalne zarządzanie** na **Tak**.
4. Teraz możesz połączyć się przy użyciu protokołu HTTPS. (Wartość domyślna to nawiązać połączenie za pośrednictwem protokołu HTTPS). Upewnij się, że protokół HTTPS jest zaznaczone.
5. Kliknij pozycję..., a następnie kliknij przycisk **Pobierz certyfikat zdalnego zarządzania**. Określ lokalizację do zapisania tego pliku. Należy zainstalować certyfikat na komputerze klienta lub hosta, który będzie używać do nawiązania połączenia z urządzeniem.
6. Kliknij przycisk **Zapisz** a następnie kliknij przycisk **tak** po wyświetleniu monitu o potwierdzenie.

### <a name="use-the-serial-console-to-enable-remote-management-over-https"></a>Użyj konsoli szeregowej, aby włączyć zdalne zarządzanie przy użyciu protokołu HTTPS

Wykonaj następujące czynności na konsoli szeregowej urządzenia, aby włączyć zdalne zarządzanie.

#### <a name="to-enable-remote-management-through-the-device-serial-console"></a>Aby włączyć zarządzanie zdalne za pośrednictwem konsoli szeregowej urządzenia
1. W menu konsoli szeregowej wybierz opcję 1. Aby uzyskać więcej informacji o używaniu konsoli szeregowej urządzenia, przejdź do [nawiązywanie połączenia z programu Windows PowerShell dla usługi StorSimple za pośrednictwem konsoli szeregowej urządzenia](storsimple-8000-windows-powershell-administration.md#connect-to-windows-powershell-for-storsimple-via-the-device-serial-console).
2. W wierszu polecenia wpisz polecenie:
   
     `Enable-HcsRemoteManagement`
   
    To należy włączyć protokół HTTPS na urządzeniu.
3. Sprawdź, czy HTTPS został włączony, wpisując: 
   
     `Get-HcsSystem`
   
    Upewnij się, że **RemoteManagementMode** pola pokazuje **HttpsEnabled**. Poniższa ilustracja przedstawia te ustawienia w programie PuTTY.
   
     ![Serial obsługujące protokół HTTPS.](./media/storsimple-remote-connect/HCS_SerialHttpsEnabled.png)
4. Z danych wyjściowych `Get-HcsSystem`, skopiuj numer seryjny urządzenia i zapisz go w celu późniejszego użycia.
   
   > [!NOTE]
   > Numer seryjny mapuje nazwy CN certyfikatu.
   
5. Uzyskaj certyfikat zdalnego zarządzania, wpisując: 
   
     `Get-HcsRemoteManagementCert`
   
    Pojawi się podobne do następujących certyfikatu.
   
    ![Pobierz certyfikat zdalnego zarządzania](./media/storsimple-remote-connect/HCS_GetRemoteManagementCertificate.png)
6. Skopiuj informacje w certyfikacie z **---BEGIN CERTIFICATE---** do **---END CERTIFICATE---** do edytora tekstów, takiego jak Notatnik, a następnie zapisz go jako plik cer. (Zostanie skopiowany ten plik do zdalnego hosta podczas przygotowywania hosta.)
   
   > [!NOTE]
   > Aby wygenerować nowy certyfikat, należy użyć `Set-HcsRemoteManagementCert` polecenia cmdlet.
   
### <a name="prepare-the-host-for-remote-management"></a>Przygotowywanie hosta do zarządzania zdalnego

Aby przygotować komputer-host do połączenia zdalnego, który używa sesję protokołu HTTPS, należy wykonać następujące procedury:

* [Zaimportuj plik cer do magazynu głównego klienta lub hosta zdalnego](#to-import-the-certificate-on-the-remote-host).
* [Dodawanie numerów seryjnych urządzeń w pliku hosts na zdalnym hoście](#to-add-device-serial-numbers-to-the-remote-host).

Poniżej opisano każdą z tych procedur.

#### <a name="to-import-the-certificate-on-the-remote-host"></a>Aby zaimportować certyfikat na zdalnym hoście
1. Kliknij prawym przyciskiem myszy plik cer, a następnie wybierz pozycję **Zainstaluj certyfikat**. Spowoduje to uruchomienie Kreatora importu certyfikatów.
   
    ![Kreator importu certyfikatów 1](./media/storsimple-remote-connect/HCS_CertificateImportWizard1.png)
2. W polu **Lokalizacja magazynu** wybierz pozycję **Maszyna lokalna**, a następnie kliknij przycisk **Dalej**.
3. Wybierz pozycję **Umieść wszystkie certyfikaty w następującym magazynie**, a następnie kliknij przycisk **Przeglądaj**. Przejdź do głównego magazynu hosta zdalnego, a następnie kliknij przycisk **Dalej**.
   
    ![Kreator importu certyfikatów 2](./media/storsimple-remote-connect/HCS_CertificateImportWizard2.png)
4. Kliknij przycisk **Zakończ**. Pojawi się komunikat informujący, że importowanie zakończyło się pomyślnie.
   
    ![Kreator importu certyfikatów 3](./media/storsimple-remote-connect/HCS_CertificateImportWizard3.png)

#### <a name="to-add-device-serial-numbers-to-the-remote-host"></a>Aby dodać numery seryjne urządzeń do hosta zdalnego
1. Uruchom program Notatnik jako administrator, a następnie otwórz plik hostów zlokalizowany w \Windows\System32\Drivers\etc.
2. Dodaj następujące trzy wpisy do pliku hosts: **Interfejs DATA 0 adres IP**, **adres IP stały kontrolera 0**, i **stały adres IP kontrolera 1 adres**.
3. Wprowadź numer seryjny urządzenia, który został wcześniej zapisany. Mapuj adres IP jak pokazano na poniższej ilustracji. Dla kontrolera 0 i kontrolera 1, dołącz **Controller0** i **Controller1** na końcu numeru seryjnego (nazwa Pospolita).
   
    ![Dodawanie nazwy CN pliku hosts](./media/storsimple-remote-connect/HCS_AddingCNNameToHostsFile.png)
4. Zapisz w pliku hosts.

### <a name="connect-to-the-device-from-the-remote-host"></a>Połącz z hostem zdalnym

Użyj programu Windows PowerShell i protokołu SSL, aby wprowadzić sesję SSAdmin na urządzeniu z hostem zdalnym lub klienta. Sesja SSAdmin mapuje opcja 1 w [konsoli szeregowej](storsimple-8000-windows-powershell-administration.md#connect-to-windows-powershell-for-storsimple-via-the-device-serial-console) menu urządzenia.

Na komputerze, z którego chcesz nawiązać połączenie zdalne programu Windows PowerShell, należy wykonać poniższą procedurę.

#### <a name="to-enter-an-ssadmin-session-on-the-device-by-using-windows-powershell-and-ssl"></a>Aby wprowadzić sesję SSAdmin na urządzeniu przy użyciu programu Windows PowerShell i protokołu SSL
1. Uruchom sesję programu Windows PowerShell jako administrator. Jeśli używasz klienta systemu Windows 10, domyślnie, usługa Windows Remote Management ustawiono na ręcznie. Konieczne może uruchomić usługi, wpisując:

    `Start-Service WinRM`

2. Dodaj adres IP urządzenia do klienta zaufanych hostów, wpisując:
   
     `Set-Item wsman:\localhost\Client\TrustedHosts <device_ip> -Concatenate -Force`
   
    Gdzie <*device_ip*> jest adres IP urządzenia, na przykład: 
   
     `Set-Item wsman:\localhost\Client\TrustedHosts 10.126.173.90 -Concatenate -Force`
3. Aby utworzyć nowe poświadczenie, wpisz:
   
     `$cred = New-Object pscredential @("<IP of target device>\SSAdmin", (ConvertTo-SecureString -Force -AsPlainText "<Device Administrator Password>"))`
   
    Gdzie <*IP urządzenie docelowe*> jest adres IP DATA 0 dla Twojego urządzenia; na przykład **10.126.173.90** pokazany na wcześniejszej ilustracji, używając pliku hosts. Ponadto należy podać hasło administratora dla danego urządzenia.
4. Utwórz sesję, wpisując:
   
     `$session = New-PSSession -UseSSL -ComputerName <Serial number of target device> -Credential $cred -ConfigurationName "SSAdminConsole"`
   
    Podaj parametr - ComputerName w poleceniu cmdlet <*numer seryjny urządzenia docelowego*>. Ten numer seryjny został zamapowany na adres IP interfejsu dane 0 w pliku hosts na zdalnym hoście; na przykład **SHX0991003G44MT** jak pokazano na poniższej ilustracji.
5. Wpisz:
   
     `Enter-PSSession $session`
6. Trzeba będzie poczekać kilka minut, a następnie możesz zostaną podłączone do urządzenia za pośrednictwem protokołu HTTPS za pośrednictwem protokołu SSL. Wyświetlony komunikat, który wskazuje, że nawiązano połączenie z urządzeniem.
   
    ![Komunikacja zdalna programu PowerShell przy użyciu protokołu HTTPS i SSL](./media/storsimple-remote-connect/HCS_PSRemotingUsingHTTPSAndSSL.png)

## <a name="next-steps"></a>Kolejne kroki

* Dowiedz się więcej o [przy użyciu programu Windows PowerShell do administrowania urządzeniem StorSimple](storsimple-8000-windows-powershell-administration.md).
* Dowiedz się więcej o [przy użyciu usługi Menedżer urządzeń StorSimple do administrowania urządzeniem StorSimple](storsimple-8000-manager-service-administration.md).

