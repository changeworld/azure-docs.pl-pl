---
title: 'Azure Active Directory Domain Services: Dołącz maszyny Wirtualnej systemu Ubuntu do domeny zarządzanej | Dokumentacja firmy Microsoft'
description: Dołączanie maszyny wirtualnej systemu Ubuntu Linux do usług domenowych Azure AD
services: active-directory-ds
documentationcenter: ''
author: mahesh-unnikrishnan
manager: mtillman
editor: curtand
ms.assetid: 804438c4-51a1-497d-8ccc-5be775980203
ms.service: active-directory
ms.component: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/22/2018
ms.author: maheshu
ms.openlocfilehash: d9f4dc0883ced599dd13d0c5d52ff865e03b73ed
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/23/2018
ms.locfileid: "36332915"
---
# <a name="join-an-ubuntu-virtual-machine-in-azure-to-a-managed-domain"></a>Dołączanie maszyny wirtualnej systemu Ubuntu na platformie Azure do domeny zarządzanej
W tym artykule przedstawiono sposób przyłączyć maszyny wirtualnej systemu Ubuntu Linux do domeny zarządzanej usług domenowych Azure AD.

[!INCLUDE [active-directory-ds-prerequisites.md](../../includes/active-directory-ds-prerequisites.md)]

## <a name="before-you-begin"></a>Przed rozpoczęciem
Aby wykonać zadania opisane w tym artykule, należy:  
1. Prawidłowy **subskrypcji platformy Azure**.
2. **Katalog usługi Azure AD** -albo synchronizowane z katalogu lokalnego lub w katalogu tylko w chmurze.
3. **Usługi domenowe Azure AD** musi być włączona dla katalogu usługi Azure AD. Jeśli nie zostało to jeszcze zrobione, należy wykonać wszystkie zadania opisane w temacie [Przewodnik wprowadzający](active-directory-ds-getting-started.md).
4. Upewnij się, że skonfigurowano adresy IP domeny zarządzanej jako serwery DNS dla sieci wirtualnej. Aby uzyskać więcej informacji, zobacz [jak zaktualizować ustawienia DNS dla sieci wirtualnej platformy Azure](active-directory-ds-getting-started-dns.md)
5. Wykonaj kroki wymagane do [synchronizacji haseł do domeny zarządzanej usług domenowych Azure AD](active-directory-ds-getting-started-password-sync.md).


## <a name="provision-an-ubuntu-linux-virtual-machine"></a>Aprowizowanie maszyny wirtualnej systemu Ubuntu Linux
Zapewnij maszyny wirtualnej systemu Ubuntu Linux na platformie Azure przy użyciu dowolnej z następujących metod:
* [Azure Portal](../virtual-machines/linux/quick-create-portal.md)
* [Interfejs wiersza polecenia platformy Azure](../virtual-machines/linux/quick-create-cli.md)
* [Azure PowerShell](../virtual-machines/linux/quick-create-powershell.md)

> [!IMPORTANT]
> * Wdróż maszynę wirtualną do **tej samej sieci wirtualnej, w której włączono usługi domenowe Azure AD**.
> * Wybierz **innej podsieci** niż ten, w której włączono usługi domenowe Azure AD.
>


## <a name="connect-remotely-to-the-ubuntu-linux-virtual-machine"></a>Zdalne nawiązywanie połączenia maszyny wirtualnej systemu Ubuntu Linux
Maszyny wirtualnej systemu Ubuntu zainicjowano na platformie Azure. Następnym zadaniem jest nawiązywanie połączeń zdalnych z maszyną wirtualną przy użyciu konta administratora lokalnego utworzone podczas inicjowania obsługi administracyjnej maszyny Wirtualnej.

Postępuj zgodnie z instrukcjami w artykule [jak zalogować się do maszyny wirtualnej z systemem Linux](../virtual-machines/linux/mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).


## <a name="configure-the-hosts-file-on-the-linux-virtual-machine"></a>Konfigurowanie pliku hostów na maszynie wirtualnej systemu Linux
W terminalu SSH Edytuj plik/etc/hosts i zaktualizować adres IP komputera i nazwy hosta.

```
sudo vi /etc/hosts
```

W pliku hosts wprowadź następujące wartości:

```
127.0.0.1 contoso-ubuntu.contoso100.com contoso-ubuntu
```
W tym miejscu "contoso100.com" jest nazwa domeny DNS domeny zarządzanej. "contoso-ubuntu" jest nazwą hosta maszyny wirtualnej systemu Ubuntu, które są przyłączania do domeny zarządzanej.


## <a name="install-required-packages-on-the-linux-virtual-machine"></a>Zainstaluj wymagane pakiety na maszynie wirtualnej systemu Linux
Następnie zainstaluj pakiety wymagane do przyłączania do domeny na maszynie wirtualnej. Wykonaj następujące czynności:

1.  W terminalu SSH wpisz następujące polecenie, aby pobrać listy pakietów z repozytoriów. To polecenie aktualizuje listy pakietów, aby uzyskać informacje o najnowszych wersji pakietów oraz ich zależności.

    ```
    sudo apt-get update
    ```

2. Wpisz następujące polecenie, aby zainstalować wymagane pakiety.
    ```
      sudo apt-get install krb5-user samba sssd sssd-tools libnss-sss libpam-sss ntp ntpdate realmd adcli
    ```

3. Podczas instalacji protokołu Kerberos Zobacz temat różowym ekranu. Instalacja pakietu "krb5 użytkownika" monituje o podanie nazwy obszaru (wszystkie wielkie litery w). Instalacja zapisuje [obszar] i [domain_realm] części /etc/krb5.conf.

    > [!TIP]
    > Jeśli nazwa domeny zarządzanej jest contoso100.com, wprowadź CONTOSO100.COM jako obszar. Należy pamiętać, że należy określić nazwę obszaru pisane wielkimi literami.
    >
    >


## <a name="configure-the-ntp-network-time-protocol-settings-on-the-linux-virtual-machine"></a>Skonfiguruj ustawienia protokołu NTP (Network czasu Protocol) na maszynie wirtualnej systemu Linux
Data i godzina maszyny Wirtualnej systemu Ubuntu musi zsynchronizować z domeny zarządzanej. Dodaj nazwę domeny zarządzanej NTP hosta w pliku /etc/ntp.conf.

```
sudo vi /etc/ntp.conf
```

W pliku ntp.conf wprowadź następujące wartości, a następnie zapisz plik:

```
server contoso100.com
```
W tym miejscu "contoso100.com" jest nazwa domeny DNS domeny zarządzanej.

Synchronizuj teraz datę i godzinę, z serwera NTP maszyny Wirtualnej systemu Ubuntu, a następnie uruchom usługę NTP:

```
sudo systemctl stop ntp
sudo ntpdate contoso100.com
sudo systemctl start ntp
```


## <a name="join-the-linux-virtual-machine-to-the-managed-domain"></a>Dołącz maszynę wirtualną systemu Linux do domeny zarządzanej
Teraz, wymagane pakiety są zainstalowane na maszynie wirtualnej systemu Linux, następne zadanie to można dołączyć maszyny wirtualnej do domeny zarządzanej.

1. Wykryj domeny zarządzanej usług domenowych w usłudze AAD. W terminalu SSH wpisz następujące polecenie:

    ```
    sudo realm discover CONTOSO100.COM
    ```

   > [!NOTE]
   > **Rozwiązywanie problemów:** Jeśli *odnajdowanie obszaru* nie może znaleźć domeny zarządzanej:
     * Upewnij się, że domena jest osiągalna z poziomu maszyny wirtualnej (ping spróbuj).
     * Sprawdź maszyny wirtualnej w rzeczywistości został wdrożony do tej samej sieci wirtualnej, w których są dostępne domeny zarządzanej.
     * Sprawdź, czy ustawienia serwera DNS dla sieci wirtualnej, aby wskazywał kontrolerów domeny zarządzanej zostały zaktualizowane.
   >

2. Inicjowanie protokołu Kerberos. W terminalu SSH wpisz następujące polecenie:

    > [!TIP]
    > * Upewnij się, że określ użytkownika, który należy do grupy "Administratorzy kontrolera domeny usługi AAD".
    > * Określ nazwę domeny wielkimi literami, else kinit kończy się niepowodzeniem.
    >

    ```
    kinit bob@CONTOSO100.COM
    ```

3. Dołącz maszynę do domeny. W terminalu SSH wpisz następujące polecenie:

    > [!TIP]
    > Użyj tego samego konta użytkownika określonego w poprzednim kroku (kinit).
    >

    ```
    sudo realm join --verbose CONTOSO100.COM -U 'bob@CONTOSO100.COM' --install=/
    ```

Należy pobrać wiadomości ("pomyślnie zarejestrowane maszyny w obszarze") po maszyna jest pomyślnie dołączona do domeny zarządzanej.


## <a name="update-the-sssd-configuration-and-restart-the-service"></a>Zaktualizuj konfigurację SSSD i uruchom ponownie usługę
1. W terminalu SSH wpisz następujące polecenie. Otwórz plik sssd.conf i wprowadź następujące zmiany
    ```
    sudo vi /etc/sssd/sssd.conf
    ```

2. Komentarz wiersz **use_fully_qualified_names = True** i Zapisz plik.
    ```
    # use_fully_qualified_names = True
    ```

3. Uruchom ponownie usługę SSSD.
    ```
    sudo service sssd restart
    ```


## <a name="configure-automatic-home-directory-creation"></a>Konfigurowanie katalogu macierzystego automatycznego tworzenia
Aby włączyć automatyczne tworzenie katalogu macierzystego po zalogowaniu użytkownicy, wpisz następujące polecenia w terminalu PuTTY:
```
sudo vi /etc/pam.d/common-session
```

Dodaj następujący wiersz w tym pliku poniżej wiersza "pam_sss.so opcjonalne sesji" i zapisz go:
```
session required pam_mkhomedir.so skel=/etc/skel/ umask=0077
```


## <a name="verify-domain-join"></a>Sprawdź, przyłączanie do domeny
Sprawdź, czy maszyna została pomyślnie dołączona do domeny zarządzanej. Nawiązać połączenie przy użyciu innego połączenia SSH maszyny Wirtualnej systemu Ubuntu przyłączonych do domeny. Przy użyciu konta użytkownika domeny, a następnie sprawdź, czy konto użytkownika jest poprawnie rozpoznać.

1. Twoje końcowych SSH wpisz następujące polecenie, aby połączyć się z domeną przyłączony maszyny wirtualnej systemu Ubuntu przy użyciu protokołu SSH. Użyj konta domeny, który należy do domeny zarządzanej (na przykład "bob@CONTOSO100.COM" w tym przypadku.)
    ```
    ssh -l bob@CONTOSO100.COM contoso-ubuntu.contoso100.com
    ```

2. W terminalu SSH wpisz następujące polecenie, aby zobaczyć, czy katalog macierzysty został poprawnie zainicjowany.
    ```
    pwd
    ```

3. W terminalu SSH wpisz następujące polecenie, aby zobaczyć, czy członkostwa w grupach są rozpoznawane poprawnie.
    ```
    id
    ```


## <a name="grant-the-aad-dc-administrators-group-sudo-privileges"></a>Udziel uprawnień sudo grupy "Administratorzy kontrolera domeny usługi AAD"
Elementy członkowskie z uprawnieniami administracyjnymi grupy "Administratorzy kontrolera domeny usługi AAD" na Maszynie wirtualnej systemu Ubuntu można przyznać. Plik sudo zostanie umieszczony w /etc/sudoers. Elementy członkowskie dodane w sudoers grup usługi Active Directory można wykonać operacji sudo.

1. W Twojej końcowych SSH upewnij się, że zalogowano się za pomocą uprawnień administratora. Można użyć konta administratora lokalnego, które można określić podczas tworzenia maszyny Wirtualnej. Uruchom następujące polecenie:
    ```
    sudo vi /etc/sudoers
    ```

2. Dodaj następujący wpis do pliku /etc/sudoers i zapisz go:
    ```
    # Add 'AAD DC Administrators' group members as admins.
    %AAD\ DC\ Administrators ALL=(ALL) NOPASSWD:ALL
    ```

3. Można teraz zalogować się jako członek grupy "Administratorzy kontrolera domeny usługi AAD" i powinien mieć uprawnienia administratora na maszynie Wirtualnej.


## <a name="troubleshooting-domain-join"></a>Rozwiązywanie problemów z przyłączania do domeny
Zapoznaj się [przyłączenie do domeny Rozwiązywanie problemów](active-directory-ds-admin-guide-join-windows-vm-portal.md#troubleshoot-joining-a-domain) artykułu.


## <a name="related-content"></a>Powiązana zawartość
* [Usługi domenowe AD Azure - Przewodnik wprowadzający](active-directory-ds-getting-started.md)
* [Dołącz maszynę wirtualną systemu Windows Server do domeny zarządzanej usług domenowych Azure AD](active-directory-ds-admin-guide-join-windows-vm.md)
* [Jak zalogować się do maszyny wirtualnej z systemem Linux](../virtual-machines/linux/mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
