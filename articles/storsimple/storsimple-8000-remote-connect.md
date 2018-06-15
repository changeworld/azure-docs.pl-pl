---
title: Zdalne nawiązywanie połączenia urządzenia StorSimple | Dokumentacja firmy Microsoft
description: Wyjaśniono, jak skonfigurować urządzenie do zdalnego zarządzania oraz sposób nawiązywania połączenia z programu Windows PowerShell dla urządzenia StorSimple za pośrednictwem protokołu HTTP lub HTTPS.
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
ms.openlocfilehash: 9414d9c93fe463910ffa6fce72aada6a0d720464
ms.sourcegitcommit: 79683e67911c3ab14bcae668f7551e57f3095425
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/25/2018
ms.locfileid: "28103959"
---
# <a name="connect-remotely-to-your-storsimple-8000-series-device"></a>Zdalne nawiązywanie połączenia z urządzenia z serii StorSimple 8000

## <a name="overview"></a>Przegląd

Możesz zdalnie nawiązać urządzenia za pomocą środowiska Windows PowerShell. Po ustanowieniu połączenia w ten sposób nie ma menu. (Możesz zobaczyć menu tylko wtedy, gdy używasz konsoli szeregowej urządzenia do łączenia). Przy użyciu komunikacji zdalnej programu Windows PowerShell należy nawiązać określonego obszaru działania. Można również określić język wyświetlania.

Aby uzyskać więcej informacji o użyciu komunikacji zdalnej programu Windows PowerShell do zarządzania urządzeniem, przejdź do [Użyj środowiska Windows PowerShell dla urządzenia StorSimple do administrowania urządzeniem StorSimple](storsimple-8000-windows-powershell-administration.md).

W tym samouczku opisano, jak skonfigurować urządzenie do zdalnego zarządzania, a następnie sposób nawiązywania połączenia z programu Windows PowerShell dla urządzenia StorSimple. Zdalne połączenia za pomocą środowiska Windows PowerShell można użyć protokołu HTTP lub HTTPS. Jednakże wybierając sposób nawiązywania połączenia z programu Windows PowerShell dla urządzenia StorSimple, należy wziąć pod uwagę następujące informacje:

* Połączenie bezpośrednio z konsolą szeregową urządzenia jest bezpieczne, ale połączenie z konsolą szeregową za pośrednictwem przełączników sieciowych nie jest. Połączenie z konsolą szeregową urządzenia za pośrednictwem przełączników sieciowych, należy uważać na zagrożenie bezpieczeństwa.
* Łączących się za pośrednictwem sesji HTTP może oferować lepsze bezpieczeństwo niż łączących się za pośrednictwem konsoli szeregowej za pośrednictwem sieci. Chociaż nie jest najbezpieczniejszą metodą, jest dopuszczalne w zaufanych sieciach.
* Połączenie za pomocą sesji protokołu HTTPS z certyfikatu z podpisem własnym to najwyższy poziom bezpieczeństwa i zalecana opcja.

Możesz połączyć zdalnie do interfejsu programu Windows PowerShell. Jednak dostępu zdalnego do urządzenia StorSimple za pośrednictwem interfejsu programu Windows PowerShell nie jest włączona domyślnie. Należy najpierw włączyć zdalne zarządzanie na urządzeniu, a następnie na kliencie umożliwiające dostęp do urządzenia.

Kroki opisane w tym artykule wykonano na komputerze hosta z systemem Windows Server 2012 R2.

## <a name="connect-through-http"></a>Łączenie się za pośrednictwem protokołu HTTP

Łączenie z programu Windows PowerShell dla urządzenia StorSimple za pośrednictwem sesji HTTP oferuje lepsze zabezpieczenia niż łączących się za pośrednictwem konsoli szeregowej urządzenia StorSimple. Chociaż nie jest najbezpieczniejszą metodą, jest dopuszczalne w zaufanych sieciach.

Aby skonfigurować zdalne zarządzanie, można użyć portalu Azure lub konsoli szeregowej. Wybierz jedną z następujących procedur:

* [Użyj portalu Azure, aby włączyć zdalne zarządzanie za pośrednictwem protokołu HTTP](#use-the-azure-classic-portal-to-enable-remote-management-over-http)
* [Użyj konsoli szeregowej, aby włączyć zdalne zarządzanie za pośrednictwem protokołu HTTP](#use-the-serial-console-to-enable-remote-management-over-http)

Po włączeniu zdalnego zarządzania umożliwia przygotowanie klienta dla połączenia zdalnego poniższej procedury.

* [Przygotuj klienta dla połączenia zdalnego](#prepare-the-client-for-remote-connection)

### <a name="use-the-azure-portal-to-enable-remote-management-over-http"></a>Użyj portalu Azure, aby włączyć zdalne zarządzanie za pośrednictwem protokołu HTTP

Wykonaj poniższe kroki w portalu Azure, aby włączyć zdalne zarządzanie za pośrednictwem protokołu HTTP.

#### <a name="to-enable-remote-management-through-the-azure-portal"></a>Aby włączyć zdalne zarządzanie za pomocą portalu Azure

1. Przejdź do usługi Menedżer urządzeń StorSimple. Wybierz **urządzeń** , a następnie wybierz i kliknij przycisk urządzenia, które chcesz skonfigurować do zdalnego zarządzania. Przejdź do **ustawienia urządzenia > zabezpieczeń**.
2. W **ustawienia zabezpieczeń** bloku, kliknij przycisk **zdalnego zarządzania**.
3. W **zdalnego zarządzania** ustawić bloku **włączyć zdalne zarządzanie** do **tak**.
4. Teraz możesz wybrać opcję połączenia przy użyciu protokołu HTTP. (Wartość domyślna to łączenie za pośrednictwem protokołu HTTPS). Upewnij się, że wybrano HTTP.
   
   > [!NOTE]
   > Łączenie za pośrednictwem protokołu HTTP jest dopuszczalne tylko w sieciach zaufanych.
   
5. Kliknij przycisk **zapisać** i po wyświetleniu monitu o potwierdzenie, wybierz **tak**.

### <a name="use-the-serial-console-to-enable-remote-management-over-http"></a>Użyj konsoli szeregowej, aby włączyć zdalne zarządzanie za pośrednictwem protokołu HTTP
Wykonaj następujące czynności na konsoli szeregowej urządzenia, aby włączyć zdalne zarządzanie.

#### <a name="to-enable-remote-management-through-the-device-serial-console"></a>Aby włączyć zdalne zarządzanie za pośrednictwem konsoli szeregowej urządzenia
1. W menu konsoli szeregowej wybierz opcję 1. Aby uzyskać więcej informacji na temat używania konsoli szeregowej na urządzeniu, przejdź do [Połącz z programu Windows PowerShell dla StorSimple za pośrednictwem konsoli szeregowej urządzenia](storsimple-8000-windows-powershell-administration.md#connect-to-windows-powershell-for-storsimple-via-the-device-serial-console).
2. W wierszu polecenia wpisz:`Enable-HcsRemoteManagement –AllowHttp`
3. Użytkownik jest powiadamiany o luk w zabezpieczeniach systemu przy użyciu protokołu HTTP do nawiązania połączenia z urządzeniem. Po wyświetleniu monitu Potwierdź, wpisując **Y**.
4. Sprawdź, czy HTTP jest włączona, wpisując:`Get-HcsSystem`
5. Sprawdź, czy **RemoteManagementMode** pola pokazuje **HttpsAndHttpEnabled**. Na poniższej ilustracji przedstawiono te ustawienia w PuTTY.
   
     ![Serial HTTPS i HTTP włączone](./media/storsimple-remote-connect/HCS_SerialHttpsAndHttpEnabled.png)

### <a name="prepare-the-client-for-remote-connection"></a>Przygotuj klienta dla połączenia zdalnego
Wykonaj następujące czynności na komputerze klienckim, aby włączyć zdalne zarządzanie.

#### <a name="to-prepare-the-client-for-remote-connection"></a>Aby przygotować klienta dla połączenia zdalnego
1. Uruchom sesję programu Windows PowerShell jako administrator. Jeśli za pomocą klienta systemu Windows 10, domyślnie, usługa Windows Remote Management ma ustawiony ręczny. Może być konieczne uruchomienie usługi przez wpisanie:

    `Start-Service WinRM`
    
2. Wpisz następujące polecenie, aby dodać adres IP urządzenia StorSimple do listy zaufanych hostów klienta:
   
     `Set-Item wsman:\localhost\Client\TrustedHosts <device_ip> -Concatenate -Force`
   
     Zastąp <*device_ip*> przy użyciu adresu IP urządzenia; na przykład: 
   
     `Set-Item wsman:\localhost\Client\TrustedHosts 10.126.173.90 -Concatenate -Force`
3. Wpisz następujące polecenie, aby zapisać poświadczenia urządzeń w zmiennej: 
   
    ```
    $cred = Get-Credential
    ```
    
4. W wyświetlonym oknie dialogowym:
   
   1. Wpisz nazwę użytkownika w następującym formacie: *device_ip\SSAdmin*.
   2. Wpisz hasło administratora urządzenia, która została ustawiona, gdy urządzenie zostało skonfigurowane przy użyciu Kreatora instalacji. Domyślne hasło jest *Password1*.
5. Uruchom sesję programu Windows PowerShell na urządzeniu, wpisując polecenie:
   
     `Enter-PSSession -Credential $cred -ConfigurationName SSAdminConsole -ComputerName <device_ip>`
   
   > [!NOTE]
   > Aby utworzyć sesję programu Windows PowerShell do użytku z urządzenia wirtualnego StorSimple, dołącz `–Port` parametru i określ port publiczny, skonfigurowanego w komunikację zdalną dla urządzenia wirtualnego StorSimple.
   
   
W tym momencie powinna mieć aktywnych sesji zdalnej programu Windows PowerShell do urządzenia.
   
![Usługi zdalne środowiska PowerShell przy użyciu protokołu HTTP](./media/storsimple-remote-connect/HCS_PSRemotingUsingHTTP.png)

## <a name="connect-through-https"></a>Łączenie się za pośrednictwem protokołu HTTPS

Łączenie z programu Windows PowerShell dla urządzenia StorSimple za pomocą sesji protokołu HTTPS jest najbardziej bezpieczna i zalecana metoda zdalne połączenie z urządzenia Microsoft Azure StorSimple. Poniższe procedury dotyczą sposobu konfigurowania serial konsoli i komputerów klienckich, aby mogli używać protokołu HTTPS do łączenia z programu Windows PowerShell dla StorSimple.

Aby skonfigurować zdalne zarządzanie, można użyć portalu Azure lub konsoli szeregowej. Wybierz jedną z następujących procedur:

* [Użyj portalu Azure, aby włączyć zdalne zarządzanie przy użyciu protokołu HTTPS](#use-the-azure-classic-portal-to-enable-remote-management-over-https)
* [Użyj konsoli szeregowej, aby włączyć zdalne zarządzanie przy użyciu protokołu HTTPS](#use-the-serial-console-to-enable-remote-management-over-https)

Po włączeniu zdalnego zarządzania umożliwia poniższych procedur Przygotuj hosta do zdalnego zarządzania i nawiąż połączenie z urządzeniem z hosta zdalnego.

* [Przygotowanie hosta do zarządzania zdalnego](#prepare-the-host-for-remote-management)
* [Nawiąż połączenie z urządzeniem z hostem zdalnym](#connect-to-the-device-from-the-remote-host)

### <a name="use-the-azure-portal-to-enable-remote-management-over-https"></a>Użyj portalu Azure, aby włączyć zdalne zarządzanie przy użyciu protokołu HTTPS

Wykonaj poniższe kroki w portalu Azure, aby włączyć zdalne zarządzanie przy użyciu protokołu HTTPS.

#### <a name="to-enable-remote-management-over-https-from-the-azure-portal"></a>Aby włączyć zdalne zarządzanie przy użyciu protokołu HTTPS z portalu Azure

1. Przejdź do usługi Menedżer urządzeń StorSimple. Wybierz **urządzeń** , a następnie wybierz i kliknij przycisk urządzenia, które chcesz skonfigurować do zdalnego zarządzania. Przejdź do **ustawienia urządzenia > zabezpieczeń**.
2. W **ustawienia zabezpieczeń** bloku, kliknij przycisk **zdalnego zarządzania**.
3. Ustaw opcję **Włącz zdalne zarządzanie** na **Tak**.
4. Można teraz nawiązać połączenia przy użyciu protokołu HTTPS. (Wartość domyślna to łączenie za pośrednictwem protokołu HTTPS). Upewnij się, że wybrany jest protokół HTTPS.
5. Kliknij przycisk..., a następnie kliknij przycisk **Pobierz certyfikat zdalnego zarządzania**. Określ lokalizację do zapisania tego pliku. Należy zainstalować ten certyfikat na komputerze klienta lub hosta używaną do nawiązania połączenia z urządzeniem.
6. Kliknij przycisk **zapisać** , a następnie kliknij przycisk **tak** po wyświetleniu monitu o potwierdzenie.

### <a name="use-the-serial-console-to-enable-remote-management-over-https"></a>Użyj konsoli szeregowej, aby włączyć zdalne zarządzanie przy użyciu protokołu HTTPS

Wykonaj następujące czynności na konsoli szeregowej urządzenia, aby włączyć zdalne zarządzanie.

#### <a name="to-enable-remote-management-through-the-device-serial-console"></a>Aby włączyć zdalne zarządzanie za pośrednictwem konsoli szeregowej urządzenia
1. W menu konsoli szeregowej wybierz opcję 1. Aby uzyskać więcej informacji na temat używania konsoli szeregowej na urządzeniu, przejdź do [Połącz z programu Windows PowerShell dla StorSimple za pośrednictwem konsoli szeregowej urządzenia](storsimple-8000-windows-powershell-administration.md#connect-to-windows-powershell-for-storsimple-via-the-device-serial-console).
2. W wierszu polecenia wpisz:
   
     `Enable-HcsRemoteManagement`
   
    To należy włączyć protokół HTTPS na urządzeniu.
3. Sprawdź, czy został włączony protokół HTTPS, wpisując: 
   
     `Get-HcsSystem`
   
    Upewnij się, że **RemoteManagementMode** pola pokazuje **HttpsEnabled**. Na poniższej ilustracji przedstawiono te ustawienia w PuTTY.
   
     ![Serial obsługujące protokół HTTPS.](./media/storsimple-remote-connect/HCS_SerialHttpsEnabled.png)
4. Z danych wyjściowych `Get-HcsSystem`, skopiuj numer seryjny urządzenia i zapisz go na przyszłość.
   
   > [!NOTE]
   > Numer seryjny mapuje Nazwa CN certyfikatu.
   
5. Uzyskaj certyfikat zdalnego zarządzania, wpisując: 
   
     `Get-HcsRemoteManagementCert`
   
    Zostanie wyświetlony certyfikat podobny do następującego.
   
    ![Pobierz certyfikat zdalnego zarządzania](./media/storsimple-remote-connect/HCS_GetRemoteManagementCertificate.png)
6. Skopiuj dane w certyfikacie z **---BEGIN CERTIFICATE---** do **---END CERTIFICATE---** do edytora tekstu, takiego jak Notatnik, a następnie zapisz go jako plik cer. (Zostanie skopiowany ten plik do zdalnego hosta podczas przygotowywania hosta.)
   
   > [!NOTE]
   > Aby wygenerować nowy certyfikat, użyj `Set-HcsRemoteManagementCert` polecenia cmdlet.
   
### <a name="prepare-the-host-for-remote-management"></a>Przygotowanie hosta do zarządzania zdalnego

Aby przygotować komputer-host dla połączenia zdalnego, które używa sesję protokołu HTTPS, należy wykonać następujące procedury:

* [Importowanie pliku .cer w magazynie głównym klienta lub hosta zdalnego](#to-import-the-certificate-on-the-remote-host).
* [Dodaj numery seryjne w pliku hosts na zdalnym hoście](#to-add-device-serial-numbers-to-the-remote-host).

Poniżej opisano każdy z powyższych procedurach.

#### <a name="to-import-the-certificate-on-the-remote-host"></a>Aby zaimportować certyfikat na zdalnym hoście
1. Kliknij prawym przyciskiem myszy plik cer, a następnie wybierz **instalacji certyfikatu**. Spowoduje to uruchomienie Kreatora importu certyfikatów.
   
    ![Kreator importu certyfikatów 1](./media/storsimple-remote-connect/HCS_CertificateImportWizard1.png)
2. Dla **lokalizacji magazynu**, wybierz pozycję **komputera lokalnego**, a następnie kliknij przycisk **dalej**.
3. Wybierz **Umieść wszystkie certyfikaty w następującym magazynie**, a następnie kliknij przycisk **Przeglądaj**. Przejdź do zdalnego hosta w magazynie głównym, a następnie kliknij przycisk **dalej**.
   
    ![Kreator importu certyfikatów 2](./media/storsimple-remote-connect/HCS_CertificateImportWizard2.png)
4. Kliknij przycisk **Zakończ**. Zostanie wyświetlony komunikat informujący o tym, że importowanie zakończyło się pomyślnie.
   
    ![Kreator importu certyfikatów 3](./media/storsimple-remote-connect/HCS_CertificateImportWizard3.png)

#### <a name="to-add-device-serial-numbers-to-the-remote-host"></a>Aby dodać numerów seryjnych urządzeń z hostem zdalnym
1. Uruchom program Notatnik jako administrator, a następnie otwórz plik hostów zlokalizowany w \Windows\System32\Drivers\etc.
2. Dodaj następujące trzy wpisy do pliku hosts: **adres IP interfejsu dane 0**, **adresu IP stałym kontrolera 0**, i **adres IP stałym kontrolera 1**.
3. Wprowadź numer seryjny urządzenia, który został wcześniej zapisany. Mapowanie to adres IP, jak pokazano na poniższej ilustracji. Dla kontrolera 0 i kontrolera 1, dołącz **Controller0** i **kontroler1** na końcu numeru seryjnego (nazwa Pospolita).
   
    ![Dodawanie pliku hosts nazwa Pospolita](./media/storsimple-remote-connect/HCS_AddingCNNameToHostsFile.png)
4. Zapisz plik hosts.

### <a name="connect-to-the-device-from-the-remote-host"></a>Nawiąż połączenie z urządzeniem z hostem zdalnym

Użyj programu Windows PowerShell i protokołu SSL, aby wprowadzić sesji SSAdmin na urządzeniu z hosta zdalnego lub klienta. Mapuje opcja 1 w sesji SSAdmin [konsoli szeregowej](storsimple-8000-windows-powershell-administration.md#connect-to-windows-powershell-for-storsimple-via-the-device-serial-console) menu urządzenia.

Na komputerze, z którego mają być połączenia zdalnego programu Windows PowerShell, należy wykonać poniższą procedurę.

#### <a name="to-enter-an-ssadmin-session-on-the-device-by-using-windows-powershell-and-ssl"></a>Aby wprowadzić sesji SSAdmin na urządzeniu za pomocą środowiska Windows PowerShell i SSL
1. Uruchom sesję programu Windows PowerShell jako administrator. Jeśli za pomocą klienta systemu Windows 10, domyślnie, usługa Windows Remote Management ma ustawiony ręczny. Może być konieczne uruchomienie usługi przez wpisanie:

    `Start-Service WinRM`

2. Dodaj adres IP urządzenia do klienta zaufanych hostów, wpisując:
   
     `Set-Item wsman:\localhost\Client\TrustedHosts <device_ip> -Concatenate -Force`
   
    Gdzie <*device_ip*> to adres IP urządzenia, na przykład: 
   
     `Set-Item wsman:\localhost\Client\TrustedHosts 10.126.173.90 -Concatenate -Force`
3. Aby utworzyć nowe poświadczenie, wpisz:
   
     `$cred = New-Object pscredential @("<IP of target device>\SSAdmin", (ConvertTo-SecureString -Force -AsPlainText "<Device Administrator Password>"))`
   
    Gdzie <*IP urządzenie docelowe*> jest adresem IP dane 0 dla urządzenia; na przykład **10.126.173.90** opisane w poprzednim obraz w pliku hosts. Ponadto należy podać hasło administratora urządzenia.
4. Utwórz sesję, wpisując:
   
     `$session = New-PSSession -UseSSL -ComputerName <Serial number of target device> -Credential $cred -ConfigurationName "SSAdminConsole"`
   
    Parametru - ComputerName w polecenia cmdlet, podaj <*numer seryjny urządzenia docelowego*>. Ten numer seryjny został zamapowany na adres IP interfejsu dane 0 w pliku hosts na zdalnym hoście; na przykład **SHX0991003G44MT** jak pokazano na poniższej ilustracji.
5. Wpisz:
   
     `Enter-PSSession $session`
6. Należy odczekać kilka minut, a następnie nastąpi połączenie do urządzenia za pośrednictwem protokołu HTTPS przy użyciu protokołu SSL. Zostanie wyświetlony komunikat, który wskazuje, że nawiązano połączenie z urządzeniem.
   
    ![Usługi zdalne środowiska PowerShell przy użyciu protokołu HTTPS i SSL](./media/storsimple-remote-connect/HCS_PSRemotingUsingHTTPSAndSSL.png)

## <a name="next-steps"></a>Kolejne kroki

* Dowiedz się więcej o [przy użyciu programu Windows PowerShell do administrowania urządzeniem StorSimple](storsimple-8000-windows-powershell-administration.md).
* Dowiedz się więcej o [przy użyciu usługi Menedżer StorSimple urządzenia do administrowania urządzeniem StorSimple](storsimple-8000-manager-service-administration.md).

