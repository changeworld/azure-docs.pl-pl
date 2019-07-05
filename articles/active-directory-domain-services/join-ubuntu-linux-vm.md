---
title: 'Azure Active Directory Domain Services: Dołącz Maszynę wirtualną Ubuntu do domeny zarządzanej | Dokumentacja firmy Microsoft'
description: Przyłączanie maszyny wirtualnej z systemem Ubuntu Linux do usługi Azure AD Domain Services
services: active-directory-ds
documentationcenter: ''
author: iainfoulds
manager: daveba
editor: curtand
ms.assetid: 804438c4-51a1-497d-8ccc-5be775980203
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/20/2019
ms.author: iainfou
ms.openlocfilehash: b21c5c517b1f4a1cbcbf2028a079793c70996d58
ms.sourcegitcommit: f811238c0d732deb1f0892fe7a20a26c993bc4fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/29/2019
ms.locfileid: "67473116"
---
# <a name="join-an-ubuntu-virtual-machine-in-azure-to-a-managed-domain"></a>Dołączanie maszyny wirtualnej systemu Ubuntu na platformie Azure do domeny zarządzanej
W tym artykule pokazano, jak dołączyć maszynę wirtualną Ubuntu Linux do domeny zarządzanej usług domenowych Azure AD.

[!INCLUDE [active-directory-ds-prerequisites.md](../../includes/active-directory-ds-prerequisites.md)]

## <a name="before-you-begin"></a>Przed rozpoczęciem
Aby wykonać zadania opisane w tym artykule, potrzebne są:  
1. Nieprawidłowy **subskrypcji platformy Azure**.
2. **Katalog usługi Azure AD** — albo synchronizowane z katalogu lokalnego lub w katalogu tylko w chmurze.
3. **Usługi domenowe Azure AD** musi być włączona dla katalogu usługi Azure AD. Jeśli nie zostało to zrobione, wykonaj wszystkie zadania, które są opisane w temacie [Wprowadzenie — przewodnik](create-instance.md).
4. Upewnij się, że zostały skonfigurowane adresy IP w domenie zarządzanej jako serwery DNS dla sieci wirtualnej. Aby uzyskać więcej informacji, zobacz [jak zaktualizować ustawienia DNS dla sieci wirtualnej platformy Azure](active-directory-ds-getting-started-dns.md)
5. Wykonaj kroki wymagane do [synchronizacji haseł do domeny zarządzanej usług domenowych Azure AD](active-directory-ds-getting-started-password-sync.md).


## <a name="provision-an-ubuntu-linux-virtual-machine"></a>Aprowizowanie maszyny wirtualnej z systemem Ubuntu Linux
Aprowizuj maszynę wirtualną Ubuntu Linux na platformie Azure przy użyciu dowolnej z następujących metod:
* [Azure Portal](../virtual-machines/linux/quick-create-portal.md)
* [Interfejs wiersza polecenia platformy Azure](../virtual-machines/linux/quick-create-cli.md)
* [Azure PowerShell](../virtual-machines/linux/quick-create-powershell.md)

> [!IMPORTANT]
> * Wdróż maszynę wirtualną do **tej samej sieci wirtualnej, w której włączono usługi domenowe Azure AD**.
> * Wybierz **innej podsieci** niż ta, w której włączono usługi domenowe Azure AD.
>


## <a name="connect-remotely-to-the-ubuntu-linux-virtual-machine"></a>Zdalne ustanawianie połączenia z maszyną wirtualną Ubuntu Linux
Został aprowizowany maszyny wirtualnej systemu Ubuntu na platformie Azure. Kolejnym krokiem jest zdalne ustanawianie połączenia z maszyną wirtualną przy użyciu konta administratora lokalnego, utworzony podczas aprowizowania maszyny Wirtualnej.

Postępuj zgodnie z instrukcjami w artykule [jak zalogować się do maszyny wirtualnej z systemem Linux](../virtual-machines/linux/mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).


## <a name="configure-the-hosts-file-on-the-linux-virtual-machine"></a>Konfigurowanie pliku hosts na maszynie wirtualnej systemu Linux
W terminalu SSH Edytuj plik/etc/hosts i zaktualizować adres IP komputera i nazwa hosta.

```
sudo vi /etc/hosts
```

W pliku hosts wprowadź następującą wartość:

```
127.0.0.1 contoso-ubuntu.contoso100.com contoso-ubuntu
```
W tym miejscu "contoso100.com" jest nazwa domeny DNS z domeny zarządzanej. "contoso-ubuntu" jest nazwą hosta maszyny wirtualnej systemu Ubuntu, z którą następuje przyłączanie do domeny zarządzanej.


## <a name="install-required-packages-on-the-linux-virtual-machine"></a>Instalowanie wymaganych pakietów na maszynie wirtualnej systemu Linux
Następnie zainstaluj pakiety wymagane do przyłączania do domeny na maszynie wirtualnej. Wykonaj poniższe czynności:

1.  W terminalu SSH wpisz następujące polecenie, aby pobrać listę pakietów z repozytoriów. To polecenie aktualizuje listę pakietów, aby uzyskać informacje na temat najnowszej wersji pakietów i ich zależności.

    ```
    sudo apt-get update
    ```

2. Wpisz następujące polecenie, aby zainstalować wymagane pakiety.
    ```
      sudo apt-get install krb5-user samba sssd sssd-tools libnss-sss libpam-sss ntp ntpdate realmd adcli
    ```

3. Podczas instalacji protokołu Kerberos, zostanie wyświetlony różowy ekran. Instalacja pakietu "krb5 user" monituje o podanie nazwy obszaru (wszystkie wielkie litery w). Instalacja zapisuje [obszaru] i [domain_realm] sekcje /etc/krb5.conf.

    > [!TIP]
    > Jeśli nazwa Twojej domeny zarządzanej jest contoso100.com, wprowadź CONTOSO100.COM jako obszar. Należy pamiętać, że nazwa obszaru musi być określona pisane wielkimi literami.
    >
    >


## <a name="configure-the-ntp-network-time-protocol-settings-on-the-linux-virtual-machine"></a>Konfigurowanie ustawień NTP (Network Management Protocol) na maszynie wirtualnej systemu Linux
Data i godzina maszyny wirtualnej systemu Ubuntu musi zsynchronizować z domeną zarządzaną. Dodaj do pliku /etc/ntp.conf hostname NTP w domeny zarządzanej.

```
sudo vi /etc/ntp.conf
```

W pliku ntp.conf wprowadź poniższą wartość, a następnie zapisz plik:

```
server contoso100.com
```
W tym miejscu "contoso100.com" jest nazwa domeny DNS z domeny zarządzanej.

Teraz synchronizować daty i czasu z serwerem NTP maszyny Wirtualnej systemu Ubuntu, a następnie uruchomić usługę NTP:

```
sudo systemctl stop ntp
sudo ntpdate contoso100.com
sudo systemctl start ntp
```


## <a name="join-the-linux-virtual-machine-to-the-managed-domain"></a>Dołączanie maszyny wirtualnej systemu Linux do domeny zarządzanej
Teraz, wymagane pakiety są zainstalowane na maszynie wirtualnej systemu Linux, kolejnym krokiem jest można dołączyć maszyny wirtualnej do domeny zarządzanej.

1. Odkryj domeny zarządzanej usług domenowych w usłudze AAD. W terminalu SSH wpisz następujące polecenie:

    ```
    sudo realm discover CONTOSO100.COM
    ```

   > [!NOTE]
   > **Rozwiązywanie problemów:** Jeśli *odnajdywania obszaru* nie może znaleźć Twojej domeny zarządzanej:
   >   * Upewnij się, że domena jest dostępny z maszyny wirtualnej (spróbuj ping).
   >   * Sprawdź, czy maszyna wirtualna w rzeczywistości został wdrożony na tej samej sieci wirtualnej, w którym domena zarządzana jest dostępna.
   >   * Sprawdź, czy zostały zaktualizowane ustawienia serwera DNS dla sieci wirtualnej, aby wskazywała na kontrolerach domeny z domeny zarządzanej.

2. Zainicjuj protokołu Kerberos. W terminalu SSH wpisz następujące polecenie:

    > [!TIP]
    > * Upewnij się, że podajesz użytkownik, który należy do grupy "Administratorzy usługi AAD DC".
    > * Określ nazwę domeny wielkimi literami, narzędzie kinit w przyłączonej jeszcze nie powiedzie się.
    >

    ```
    kinit bob@CONTOSO100.COM
    ```

3. Przyłączanie komputera do domeny. W terminalu SSH wpisz następujące polecenie:

    > [!TIP]
    > Użyj tego samego konta użytkownika, które określiłeś w poprzednim kroku (kinit).
    >

    ```
    sudo realm join --verbose CONTOSO100.COM -U 'bob@CONTOSO100.COM' --install=/
    ```

Powinien zostać wyświetlony komunikat ("zarejestrowano pomyślnie maszyny w obszarze") po komputer pomyślnie jest przyłączony do domeny zarządzanej.


## <a name="update-the-sssd-configuration-and-restart-the-service"></a>Zaktualizuj konfigurację SSSD i uruchom ponownie usługę
1. W terminalu SSH wpisz następujące polecenie. Otwórz plik sssd.conf i wprowadź następującą zmianę
    ```
    sudo vi /etc/sssd/sssd.conf
    ```

2. Komentarz wiersza **use_fully_qualified_names = True** i Zapisz plik.
    ```
    # use_fully_qualified_names = True
    ```

3. Uruchom ponownie usługę SSSD.
    ```
    sudo service sssd restart
    ```


## <a name="configure-automatic-home-directory-creation"></a>Konfigurowanie tworzenia automatycznych katalogu macierzystego
Aby włączyć automatyczne tworzenie katalogu macierzystego po zalogowaniu się użytkownicy, wpisz następujące polecenia w terminalu PuTTY:
```
sudo vi /etc/pam.d/common-session
```

Dodaj następujący wiersz w tym pliku poniżej wiersza "pam_sss.so opcjonalne sesja" i zapisz go:
```
session required pam_mkhomedir.so skel=/etc/skel/ umask=0077
```


## <a name="verify-domain-join"></a>Sprawdź, przyłączanie do domeny
Sprawdź, czy komputer został pomyślnie dołączony do domeny zarządzanej. Połączyć się z częścią domeny maszyny Wirtualnej systemu Ubuntu przy użyciu innego połączenia SSH. Użyj konta użytkownika domeny, a następnie sprawdź, czy konto użytkownika jest poprawnie rozwiązany.

1. W swojej terminala protokołu SSH wpisz następujące polecenie, aby połączyć się z domeną, przyłączone do maszyny wirtualnej systemu Ubuntu przy użyciu protokołu SSH. Użyj konta domeny, który należy do domeny zarządzanej (na przykład "bob@CONTOSO100.COM" w tym przypadku.)
    ```
    ssh -l bob@CONTOSO100.COM contoso-ubuntu.contoso100.com
    ```

2. W terminalu SSH wpisz następujące polecenie, aby zobaczyć, jeśli katalog macierzysty został poprawnie zainicjowany.
    ```
    pwd
    ```

3. W terminalu SSH wpisz następujące polecenie, aby zobaczyć, jeśli w grupach są rozpoznawane poprawnie.
    ```
    id
    ```


## <a name="grant-the-aad-dc-administrators-group-sudo-privileges"></a>Udziel uprawnień "sudo" grupy "Administratorzy usługi AAD DC"
Można udzielić członkom uprawnień administracyjnych grupy "Administratorzy usługi AAD DC" na maszynie Wirtualnej systemu Ubuntu. Plik "sudo" znajduje się w /etc/sudoers. Członkowie grupy AD dodany do programu sudo, można wykonać "sudo".

1. W swojej terminala protokołu SSH upewnij się, że zalogowano się za pomocą uprawnień administratora. Można użyć konta administratora lokalnego, które określono podczas tworzenia maszyny Wirtualnej. Wykonaj następujące polecenie:
    ```
    sudo vi /etc/sudoers
    ```

2. Dodaj następujący wpis do pliku /etc/sudoers i zapisz go:
    ```
    # Add 'AAD DC Administrators' group members as admins.
    %AAD\ DC\ Administrators ALL=(ALL) NOPASSWD:ALL
    ```

3. Możesz teraz zalogować się jako członek grupy "Administratorzy usługi AAD DC" i powinien mieć uprawnienia administracyjne na maszynie Wirtualnej.


## <a name="troubleshooting-domain-join"></a>Rozwiązywanie problemów z przyłączania do domeny
Zapoznaj się [przyłączania do domeny Rozwiązywanie problemów](join-windows-vm.md#troubleshoot-joining-a-domain) artykułu.


## <a name="related-content"></a>Powiązana zawartość
* [Azure AD Domain Services — wprowadzenie — przewodnik](create-instance.md)
* [Dołączanie maszyny wirtualnej systemu Windows Server do domeny zarządzanej usług domenowych Azure AD](active-directory-ds-admin-guide-join-windows-vm.md)
* [Jak zalogować się do maszyny wirtualnej z systemem Linux](../virtual-machines/linux/mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
