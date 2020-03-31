---
title: Zdalne łączenie się z urządzeniem StorSimple
description: W tym artykule wyjaśniono, jak skonfigurować urządzenie do zdalnego zarządzania i jak połączyć się z programem Windows PowerShell dla storsimple za pośrednictwem protokołu HTTP lub HTTPS.
author: alkohli
ms.service: storsimple
ms.topic: conceptual
ms.date: 01/02/2018
ms.author: alkohli
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 58d61df932da06e32bb4c8f21a3a296b185f02d9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80299001"
---
# <a name="connect-remotely-to-your-storsimple-8000-series-device"></a>Zdalne podłączanie do urządzenia z serii StorSimple 8000

## <a name="overview"></a>Omówienie

Za pośrednictwem programu Windows PowerShell można zdalnie łączyć się z urządzeniem. Po podłączeniu w ten sposób nie widać menu. (Menu jest wyświetlane tylko wtedy, gdy do nawiązania połączenia używasz konsoli szeregowej na urządzeniu). W programie Windows PowerShell komunikacji zdalnej można połączyć się z określonym obszarem run. Można również określić język wyświetlania.

Aby uzyskać więcej informacji na temat zarządzania urządzeniem za pomocą funkcji zdalnego obsługi urządzenia w programie Windows PowerShell, przejdź do [witryny Korzystanie z programu Windows PowerShell for StorSimple w celu administrowania urządzeniem StorSimple](storsimple-8000-windows-powershell-administration.md).

W tym samouczku wyjaśniono, jak skonfigurować urządzenie do zdalnego zarządzania, a następnie jak połączyć się z programem Windows PowerShell dla storsimple. Za pomocą protokołu HTTP lub HTTPS można zdalnie łączyć się za pośrednictwem programu Windows PowerShell. Jednak podczas podejmowania decyzji, jak połączyć się z programem Windows PowerShell dla StorSimple, należy wziąć pod uwagę następujące informacje:

* Podłączanie bezpośrednio do konsoli szeregowej urządzenia jest bezpieczne, ale łączenie się z konsolą szeregową za pośrednictwem przełączników sieciowych nie jest. Należy zachować ostrożność podczas łączenia się z konsolą szeregową urządzenia za pośrednictwem przełączników sieciowych.
* Łączenie się za pośrednictwem sesji HTTP może zapewnić większe bezpieczeństwo niż łączenie się za pośrednictwem konsoli szeregowej za pośrednictwem sieci. Chociaż nie jest to najbezpieczniejsza metoda, jest dopuszczalna w zaufanych sieciach.
* Łączenie się za pośrednictwem sesji HTTPS z certyfikatem z podpisem własnym jest najbezpieczniejszą i zalecaną opcją.

Można połączyć się zdalnie z interfejsem programu Windows PowerShell. Jednak zdalny dostęp do urządzenia StorSimple za pośrednictwem interfejsu programu Windows PowerShell nie jest domyślnie włączony. Należy najpierw włączyć zdalne zarządzanie na urządzeniu, a następnie na kliencie, który jest używany do uzyskiwania dostępu do urządzenia.

Kroki opisane w tym artykule zostały wykonane w systemie hosta z systemem Windows Server 2012 R2.

## <a name="connect-through-http"></a>Łączenie się za pośrednictwem protokołu HTTP

Łączenie się z programem Windows PowerShell for StorSimple za pośrednictwem sesji HTTP zapewnia większe bezpieczeństwo niż łączenie się za pośrednictwem konsoli szeregowej urządzenia StorSimple. Chociaż nie jest to najbezpieczniejsza metoda, jest dopuszczalna w zaufanych sieciach.

Do skonfigurowania zdalnego zarządzania można użyć witryny Azure portal lub konsoli szeregowej. Wybierz jedną z następujących procedur:

* Korzystanie z witryny Azure Portal w celu umożliwienia zdalnego zarządzania za pośrednictwem protokołu HTTP
* [Włącz zdalne zarządzanie za pośrednictwem protokołu HTTP za pomocą konsoli szeregowej](#use-the-serial-console-to-enable-remote-management-over-http)

Po włączeniu zdalnego zarządzania należy użyć poniższej procedury, aby przygotować klienta do połączenia zdalnego.

* [Przygotowanie klienta do połączenia zdalnego](#prepare-the-client-for-remote-connection)

### <a name="use-the-azure-portal-to-enable-remote-management-over-http"></a>Korzystanie z witryny Azure Portal w celu umożliwienia zdalnego zarządzania za pośrednictwem protokołu HTTP

Wykonaj następujące kroki w witrynie Azure portal, aby włączyć zdalne zarządzanie za pośrednictwem protokołu HTTP.

#### <a name="to-enable-remote-management-through-the-azure-portal"></a>Aby włączyć zdalne zarządzanie za pośrednictwem witryny Azure portal

1. Przejdź do usługi Menedżer urządzeń StorSimple. Wybierz **pozycję Urządzenia,** a następnie wybierz pozycję i kliknij urządzenie, które chcesz skonfigurować do zdalnego zarządzania. Przejdź do **ustawień urządzenia > security**.
2. W bloku **Ustawienia zabezpieczeń** kliknij pozycję **Zarządzanie zdalne**.
3. W bloku **Zarządzanie zdalne** ustaw **włącz zarządzanie zdalne** na **Tak**.
4. Teraz możesz wybrać opcję połączenia przy użyciu protokołu HTTP. (Ustawieniem domyślnym jest łączenie się za pośrednictwem protokołu HTTPS). Upewnij się, że jest zaznaczony protokół HTTP.
   
   > [!NOTE]
   > Łączenie za pośrednictwem protokołu HTTP jest dopuszczalne tylko w sieciach zaufanych.
   
5. Kliknij **przycisk Zapisz,** a po wyświetleniu monitu o potwierdzenie wybierz pozycję **Tak**.

### <a name="use-the-serial-console-to-enable-remote-management-over-http"></a>Włącz zdalne zarządzanie za pośrednictwem protokołu HTTP za pomocą konsoli szeregowej
Wykonaj następujące czynności na konsoli szeregowej urządzenia, aby włączyć zdalne zarządzanie.

#### <a name="to-enable-remote-management-through-the-device-serial-console"></a>Aby włączyć zdalne zarządzanie za pomocą konsoli szeregowej urządzenia
1. W menu konsoli szeregowej wybierz opcję 1. Aby uzyskać więcej informacji na temat korzystania z konsoli szeregowej na urządzeniu, przejdź [do połącz z programem Windows PowerShell dla storsimple za pośrednictwem konsoli szeregowej urządzenia](storsimple-8000-windows-powershell-administration.md#connect-to-windows-powershell-for-storsimple-via-the-device-serial-console).
2. W wierszu polecenia wpisz:`Enable-HcsRemoteManagement –AllowHttp`
3. Użytkownik jest powiadamiany o lukach w zabezpieczeniach związanych z używaniem protokołu HTTP do łączenia się z urządzeniem. Po wyświetleniu monitu potwierdź wpisując **Y**.
4. Sprawdź, czy protokół HTTP jest włączony przez wpisanie:`Get-HcsSystem`
5. Sprawdź, czy w polu **RemoteManagementMode** jest wyświetlany **plik HttpsAndHttpEnabled**. Na poniższej ilustracji przedstawiono te ustawienia w putty.
   
     ![Szeregowy protokół HTTPS i protokół HTTP włączony](./media/storsimple-remote-connect/HCS_SerialHttpsAndHttpEnabled.png)

### <a name="prepare-the-client-for-remote-connection"></a>Przygotowanie klienta do połączenia zdalnego
Wykonaj następujące kroki na kliencie, aby włączyć zdalne zarządzanie.

#### <a name="to-prepare-the-client-for-remote-connection"></a>Aby przygotować klienta do zdalnego połączenia
1. Uruchom sesję programu Windows PowerShell jako administrator. Jeśli używasz klienta systemu Windows 10, domyślnie usługa zdalnego zarządzania systemem Windows jest ustawiona na ręczną. Może być konieczne uruchomienie usługi przez wpisanie:

    `Start-Service WinRM`
    
2. Wpisz następujące polecenie, aby dodać adres IP urządzenia StorSimple do listy zaufanych hostów klienta:
   
     `Set-Item wsman:\localhost\Client\TrustedHosts <device_ip> -Concatenate -Force`
   
     Zastąp> *<device_ip* adresem IP urządzenia; na przykład: 
   
     `Set-Item wsman:\localhost\Client\TrustedHosts 10.126.173.90 -Concatenate -Force`
3. Wpisz następujące polecenie, aby zapisać poświadczenia urządzenia w zmiennej: 
   
    ```
    $cred = Get-Credential
    ```
    
4. W wyświetlonym oknie dialogowym:
   
   1. Wpisz nazwę użytkownika w tym formacie: *device_ip\SSAdmin*.
   2. Wpisz hasło administratora urządzenia ustawione podczas konfigurowania urządzenia za pomocą kreatora konfiguracji. Domyślnym hasłem jest *Password1*.
5. Uruchom sesję programu Windows PowerShell na urządzeniu, wpisując to polecenie:
   
     `Enter-PSSession -Credential $cred -ConfigurationName SSAdminConsole -ComputerName <device_ip>`
   
   > [!NOTE]
   > Aby utworzyć sesję programu Windows PowerShell do użytku z urządzeniem wirtualnym StorSimple, dołącz `–Port` parametr i określ port publiczny skonfigurowany w programie Remoting for StorSimple Virtual Appliance.
   
   
W tym momencie powinna być aktywna zdalna sesja programu Windows PowerShell do urządzenia.
   
![Tworzenie komunikacji zdalnej programu PowerShell przy użyciu protokołu HTTP](./media/storsimple-remote-connect/HCS_PSRemotingUsingHTTP.png)

## <a name="connect-through-https"></a>Łączenie się za pośrednictwem protokołu HTTPS

Łączenie się z programem Windows PowerShell for StorSimple za pośrednictwem sesji HTTPS jest najbezpieczniejszą i najbezpieczniejszą metodą zdalnego łączenia się z urządzeniem Microsoft Azure StorSimple. W poniższych procedurach wyjaśniono, jak skonfigurować konsolę szeregową i komputery klienckie, aby można było połączyć się z programem Windows PowerShell dla programu StorSimple za pomocą protokołu HTTPS.

Do skonfigurowania zdalnego zarządzania można użyć witryny Azure portal lub konsoli szeregowej. Wybierz jedną z następujących procedur:

* Korzystanie z witryny Azure Portal w celu umożliwienia zdalnego zarządzania za pośrednictwem protokołu HTTPS
* [Włącz zdalne zarządzanie za pośrednictwem protokołu HTTPS za pomocą konsoli szeregowej](#use-the-serial-console-to-enable-remote-management-over-https)

Po włączeniu zdalnego zarządzania użyj następujących procedur, aby przygotować hosta do zdalnego zarządzania i połączyć się z urządzeniem z hosta zdalnego.

* [Przygotowywanie hosta do zarządzania zdalnego](#prepare-the-host-for-remote-management)
* [Łączenie się z urządzeniem z hosta zdalnego](#connect-to-the-device-from-the-remote-host)

### <a name="use-the-azure-portal-to-enable-remote-management-over-https"></a>Korzystanie z witryny Azure Portal w celu umożliwienia zdalnego zarządzania za pośrednictwem protokołu HTTPS

Wykonaj następujące kroki w witrynie Azure portal, aby włączyć zdalne zarządzanie za pośrednictwem protokołu HTTPS.

#### <a name="to-enable-remote-management-over-https-from-the-azure-portal"></a>Aby włączyć zdalne zarządzanie za pośrednictwem protokołu HTTPS z witryny Azure portal

1. Przejdź do usługi Menedżer urządzeń StorSimple. Wybierz **pozycję Urządzenia,** a następnie wybierz pozycję i kliknij urządzenie, które chcesz skonfigurować do zdalnego zarządzania. Przejdź do **ustawień urządzenia > security**.
2. W bloku **Ustawienia zabezpieczeń** kliknij pozycję **Zarządzanie zdalne**.
3. Ustaw opcję **Włącz zdalne zarządzanie** na **Tak**.
4. Teraz możesz połączyć się przy użyciu protokołu HTTPS. (Ustawieniem domyślnym jest łączenie się za pośrednictwem protokołu HTTPS). Upewnij się, że jest zaznaczona opcja HTTPS.
5. Kliknij... , a następnie kliknij pozycję **Pobierz certyfikat zdalnego zarządzania**. Określ lokalizację, aby zapisać ten plik. Należy zainstalować ten certyfikat na komputerze klienckim lub hostowym, który będzie używany do łączenia się z urządzeniem.
6. Kliknij **pozycję Zapisz,** a następnie kliknij przycisk **Tak** po wyświetleniu monitu o potwierdzenie.

### <a name="use-the-serial-console-to-enable-remote-management-over-https"></a>Włącz zdalne zarządzanie za pośrednictwem protokołu HTTPS za pomocą konsoli szeregowej

Wykonaj następujące czynności na konsoli szeregowej urządzenia, aby włączyć zdalne zarządzanie.

#### <a name="to-enable-remote-management-through-the-device-serial-console"></a>Aby włączyć zdalne zarządzanie za pomocą konsoli szeregowej urządzenia
1. W menu konsoli szeregowej wybierz opcję 1. Aby uzyskać więcej informacji na temat korzystania z konsoli szeregowej na urządzeniu, przejdź [do połącz z programem Windows PowerShell dla storsimple za pośrednictwem konsoli szeregowej urządzenia](storsimple-8000-windows-powershell-administration.md#connect-to-windows-powershell-for-storsimple-via-the-device-serial-console).
2. W wierszu polecenia wpisz:
   
     `Enable-HcsRemoteManagement`
   
    Powinno to włączyć protokół HTTPS na urządzeniu.
3. Sprawdź, czy protokół HTTPS został włączony, wpisując: 
   
     `Get-HcsSystem`
   
    Upewnij się, że w polu **RemoteManagementMode** jest wyświetlany **plik HttpsEnabled**. Na poniższej ilustracji przedstawiono te ustawienia w putty.
   
     ![Szeregowy protokół HTTPS włączony](./media/storsimple-remote-connect/HCS_SerialHttpsEnabled.png)
4. Z wyjścia `Get-HcsSystem`, skopiować numer seryjny urządzenia i zapisać go do późniejszego użycia.
   
   > [!NOTE]
   > Numer seryjny jest mapowana na nazwę CN w certyfikacie.
   
5. Uzyskaj certyfikat zdalnego zarządzania, wpisując: 
   
     `Get-HcsRemoteManagementCert`
   
    Pojawi się certyfikat podobny do następującego.
   
    ![Uzyskaj certyfikat zdalnego zarządzania](./media/storsimple-remote-connect/HCS_GetRemoteManagementCertificate.png)
6. Skopiuj informacje w certyfikacie z **-----BEGIN CERTIFICATE-----** do **-----END CERTIFICATE-----** do edytora tekstu, takiego jak Notatnik, i zapisz je jako plik cer. (Ten plik zostanie skopiowany do hosta zdalnego podczas przygotowywania hosta).
   
   > [!NOTE]
   > Aby wygenerować nowy `Set-HcsRemoteManagementCert` certyfikat, użyj polecenia cmdlet.
   
### <a name="prepare-the-host-for-remote-management"></a>Przygotowywanie hosta do zarządzania zdalnego

Aby przygotować komputer-host do połączenia zdalnego korzystającego z sesji HTTPS, wykonaj następujące procedury:

* [Zaimportuj plik cer do magazynu głównego klienta lub hosta zdalnego](#to-import-the-certificate-on-the-remote-host).
* [Dodaj numery seryjne urządzenia do pliku hosts na hoście zdalnym](#to-add-device-serial-numbers-to-the-remote-host).

Poniżej opisano każdą z tych procedur.

#### <a name="to-import-the-certificate-on-the-remote-host"></a>Aby zaimportować certyfikat na hoście zdalnym
1. Kliknij prawym przyciskiem myszy plik cer, a następnie wybierz pozycję **Zainstaluj certyfikat**. Spowoduje to uruchomienie Kreatora importu certyfikatów.
   
    ![Kreator importu certyfikatów 1](./media/storsimple-remote-connect/HCS_CertificateImportWizard1.png)
2. W polu **Lokalizacja magazynu** wybierz pozycję **Maszyna lokalna**, a następnie kliknij przycisk **Dalej**.
3. Wybierz pozycję **Umieść wszystkie certyfikaty w następującym magazynie**, a następnie kliknij przycisk **Przeglądaj**. Przejdź do głównego magazynu hosta zdalnego, a następnie kliknij przycisk **Dalej**.
   
    ![Kreator importu certyfikatów 2](./media/storsimple-remote-connect/HCS_CertificateImportWizard2.png)
4. Kliknij przycisk **Zakończ**. Pojawi się komunikat informujący, że importowanie zakończyło się pomyślnie.
   
    ![Kreator importu certyfikatów 3](./media/storsimple-remote-connect/HCS_CertificateImportWizard3.png)

#### <a name="to-add-device-serial-numbers-to-the-remote-host"></a>Aby dodać numery seryjne urządzenia do hosta zdalnego
1. Uruchom Notatnik jako administrator, a następnie otwórz plik hosts znajdujący się w folderze \Windows\System32\Drivers\etc.
2. Dodaj następujące trzy wpisy do pliku hosts: **DATA 0 adres IP**, Kontroler **0 Stały adres IP**i Kontroler **1 Stały adres IP**.
3. Wprowadź zapisany wcześniej numer seryjny urządzenia. Zamapuj to na adres IP, jak pokazano na poniższej ilustracji. Dla kontrolera 0 i kontrolera 1, dołącz **Controller0** i **Controller1** na końcu numeru seryjnego (nazwa CN).
   
    ![Dodawanie nazwy CN do pliku hosta](./media/storsimple-remote-connect/HCS_AddingCNNameToHostsFile.png)
4. Zapisz plik hosts.

### <a name="connect-to-the-device-from-the-remote-host"></a>Łączenie się z urządzeniem z hosta zdalnego

Użyj programu Windows PowerShell i TLS, aby wprowadzić sesję SSAdmin na urządzeniu z hosta zdalnego lub klienta. Sesja SSAdmin jest mapowana na opcję 1 w menu [konsoli szeregowej](storsimple-8000-windows-powershell-administration.md#connect-to-windows-powershell-for-storsimple-via-the-device-serial-console) urządzenia.

Wykonaj następującą procedurę na komputerze, z którego chcesz nawiązać zdalne połączenie programu Windows PowerShell.

#### <a name="to-enter-an-ssadmin-session-on-the-device-by-using-windows-powershell-and-tls"></a>Aby wprowadzić sesję SSAdmin na urządzeniu przy użyciu programów Windows PowerShell i TLS
1. Uruchom sesję programu Windows PowerShell jako administrator. Jeśli używasz klienta systemu Windows 10, domyślnie usługa zdalnego zarządzania systemem Windows jest ustawiona na ręczną. Może być konieczne uruchomienie usługi przez wpisanie:

    `Start-Service WinRM`

2. Dodaj adres IP urządzenia do zaufanych hostów klienta, wpisując:
   
     `Set-Item wsman:\localhost\Client\TrustedHosts <device_ip> -Concatenate -Force`
   
    Gdzie <*device_ip*> jest adres IP urządzenia; na przykład: 
   
     `Set-Item wsman:\localhost\Client\TrustedHosts 10.126.173.90 -Concatenate -Force`
3. Aby utworzyć nowe poświadczenia, wpisz:
   
     `$cred = New-Object pscredential @("<IP of target device>\SSAdmin", (ConvertTo-SecureString -Force -AsPlainText "<Device Administrator Password>"))`
   
    W przypadku gdy <*IP urządzenia docelowego*> jest adresem IP danych 0 dla urządzenia; na przykład **10.126.173.90,** jak pokazano na poprzednim obrazie pliku hosts. Podaj również hasło administratora urządzenia.
4. Utwórz sesję, wpisując:
   
     `$session = New-PSSession -UseSSL -ComputerName <Serial number of target device> -Credential $cred -ConfigurationName "SSAdminConsole"`
   
    Dla parametru -ComputerName w policzce cmdlet podaj <*numer seryjny urządzenia docelowego*>. Ten numer seryjny został zamapowany na adres IP danych 0 w pliku hosts na hoście zdalnym; na przykład **SHX0991003G44MT,** jak pokazano na poniższej ilustracji.
5. Wpisz:
   
     `Enter-PSSession $session`
6. Trzeba będzie odczekać kilka minut, a następnie zostaniesz połączony z urządzeniem za pośrednictwem protokołu HTTPS przez TLS. Zostanie wyświetlony komunikat informujący, że urządzenie jest połączone.
   
    ![Tworzenie komunikacji zdalnej programu PowerShell przy użyciu protokołu HTTPS i TLS](./media/storsimple-remote-connect/HCS_PSRemotingUsingHTTPSAndSSL.png)

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej o [administrowaniu urządzeniem StorSimple za pomocą programu Windows PowerShell](storsimple-8000-windows-powershell-administration.md).
* Dowiedz się więcej o [administrowaniu urządzeniem StorSimple za pomocą usługi StorSimple Device Manager.](storsimple-8000-manager-service-administration.md)

