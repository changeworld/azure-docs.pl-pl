---
title: 'Azure Active Directory Domain Services: Przyłączanie maszyny wirtualnej Ubuntu do domeny zarządzanej | Microsoft Docs'
description: Przyłączanie maszyny wirtualnej Ubuntu Linux do Azure AD Domain Services
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
ms.openlocfilehash: 78afec75269876c309b2c324d8a5973fd5ebf9a8
ms.sourcegitcommit: 4b5dcdcd80860764e291f18de081a41753946ec9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/03/2019
ms.locfileid: "68773034"
---
# <a name="join-an-ubuntu-virtual-machine-in-azure-to-a-managed-domain"></a>Przyłączanie maszyny wirtualnej Ubuntu na platformie Azure do domeny zarządzanej
W tym artykule opisano sposób przyłączania maszyny wirtualnej Ubuntu Linux do domeny zarządzanej Azure AD Domain Services.

[!INCLUDE [active-directory-ds-prerequisites.md](../../includes/active-directory-ds-prerequisites.md)]

## <a name="before-you-begin"></a>Przed rozpoczęciem
Aby wykonać zadania wymienione w tym artykule, potrzebne są:  
1. Prawidłowa **subskrypcja platformy Azure**.
2. **Katalog usługi Azure AD** — zsynchronizowany z katalogiem lokalnym lub katalogiem w chmurze.
3. Należy włączyć **Azure AD Domain Services** dla katalogu usługi Azure AD. Jeśli nie zostało to zrobione, postępuj zgodnie ze wszystkimi zadaniami opisanymi w [przewodniku wprowadzenie](create-instance.md).
4. Upewnij się, że adresy IP domeny zarządzanej zostały skonfigurowane jako serwery DNS dla sieci wirtualnej. Aby uzyskać więcej informacji, zobacz [jak zaktualizować ustawienia DNS dla sieci wirtualnej platformy Azure](active-directory-ds-getting-started-dns.md)
5. Wykonaj kroki wymagane do [synchronizacji haseł do domeny zarządzanej Azure AD Domain Services](active-directory-ds-getting-started-password-sync.md).


## <a name="provision-an-ubuntu-linux-virtual-machine"></a>Inicjowanie obsługi administracyjnej maszyny wirtualnej Ubuntu Linux
Udostępnij Ubuntu Linux maszynę wirtualną na platformie Azure przy użyciu dowolnej z następujących metod:
* [Azure Portal](../virtual-machines/linux/quick-create-portal.md)
* [Interfejs wiersza polecenia platformy Azure](../virtual-machines/linux/quick-create-cli.md)
* [Azure PowerShell](../virtual-machines/linux/quick-create-powershell.md)

> [!IMPORTANT]
> * Wdróż maszynę wirtualną w tej **samej sieci wirtualnej, w której włączono Azure AD Domain Services**.
> * Wybierz **inną podsieć** niż ta, w której włączono Azure AD Domain Services.
>


## <a name="connect-remotely-to-the-ubuntu-linux-virtual-machine"></a>Zdalne nawiązywanie połączenia z maszyną wirtualną Ubuntu Linux
Maszyna wirtualna Ubuntu została zainicjowana na platformie Azure. Następnym zadaniem jest zdalne nawiązywanie połączenia z maszyną wirtualną przy użyciu konta administratora lokalnego utworzonego podczas aprowizacji maszyny wirtualnej.

Postępuj zgodnie z instrukcjami w artykule [Jak zalogować się do maszyny wirtualnej z systemem Linux](../virtual-machines/linux/mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).


## <a name="configure-the-hosts-file-on-the-linux-virtual-machine"></a>Konfigurowanie pliku Hosts na maszynie wirtualnej z systemem Linux
W terminalu SSH Edytuj plik/etc/hosts i zaktualizuj adres IP i nazwę hosta maszyny.

```console
sudo vi /etc/hosts
```

W pliku hosts Wprowadź następującą wartość:

```console
127.0.0.1 contoso-ubuntu.contoso100.com contoso-ubuntu
```

W tym miejscu "contoso100.com" jest nazwą domeny DNS domeny zarządzanej. "contoso-Ubuntu" jest nazwą hosta maszyny wirtualnej Ubuntu, do której jest przyłączany do domeny zarządzanej.


## <a name="install-required-packages-on-the-linux-virtual-machine"></a>Instalowanie wymaganych pakietów na maszynie wirtualnej z systemem Linux
Następnie zainstaluj pakiety wymagane do przyłączenia do domeny na maszynie wirtualnej. Wykonaj poniższe czynności:

1.  W terminalu SSH wpisz następujące polecenie, aby pobrać listy pakietów z repozytoriów. To polecenie aktualizuje listy pakietów, aby uzyskać informacje o najnowszych wersjach pakietów i ich zależności.

    ```console
    sudo apt-get update
    ```

2. Wpisz następujące polecenie, aby zainstalować wymagane pakiety.

    ```console
      sudo apt-get install krb5-user samba sssd sssd-tools libnss-sss libpam-sss ntp ntpdate realmd adcli
    ```

3. Podczas instalacji protokołu Kerberos zobaczysz różowy ekran. Podczas instalacji pakietu "krb5-User" są wyświetlane informacje o nazwie obszaru (WIELKImi LITERAmi). Instalacja zapisuje sekcje [Realm] i [domain_realm] w/etc/krb5.conf.

    > [!TIP]
    > Jeśli nazwa domeny zarządzanej to contoso100.com, wprowadź CONTOSO100.COM jako obszar. Należy pamiętać, że nazwa obszaru musi być określona WIELKImi LITERAmi.


## <a name="configure-the-ntp-network-time-protocol-settings-on-the-linux-virtual-machine"></a>Konfigurowanie ustawień NTP (Network Time Protocol) na maszynie wirtualnej z systemem Linux
Data i godzina maszyny wirtualnej Ubuntu muszą być synchronizowane z domeną zarządzaną. Dodaj nazwę hosta NTP w domenie zarządzanej w pliku/etc/ntp.conf.

```console
sudo vi /etc/ntp.conf
```

W pliku NTP. conf Wprowadź następującą wartość i Zapisz plik:

```console
server contoso100.com
```

W tym miejscu "contoso100.com" jest nazwą domeny DNS domeny zarządzanej.

Teraz zsynchronizuj datę i godzinę maszyny wirtualnej Ubuntu z serwerem NTP, a następnie uruchom usługę NTP:

```console
sudo systemctl stop ntp
sudo ntpdate contoso100.com
sudo systemctl start ntp
```


## <a name="join-the-linux-virtual-machine-to-the-managed-domain"></a>Przyłączanie maszyny wirtualnej z systemem Linux do domeny zarządzanej
Teraz, gdy wymagane pakiety są zainstalowane na maszynie wirtualnej z systemem Linux, następne zadanie polega na przyłączeniu maszyny wirtualnej do domeny zarządzanej.

1. Odkryj domenę zarządzaną usług domenowych w usłudze AAD. W terminalu SSH wpisz następujące polecenie:

    ```console
    sudo realm discover CONTOSO100.COM
    ```

   > [!NOTE]
   > **Rozwiązywanie problemów:** Jeśli *odnajdywanie obszaru* nie może znaleźć domeny zarządzanej:
   >   * Upewnij się, że domena jest dostępna z poziomu maszyny wirtualnej (Wypróbuj polecenie ping).
   >   * Sprawdź, czy maszyna wirtualna rzeczywiście została wdrożona w tej samej sieci wirtualnej, w której jest dostępna domena zarządzana.
   >   * Sprawdź, czy zostały zaktualizowane ustawienia serwera DNS dla sieci wirtualnej, aby wskazywały kontrolery domeny zarządzanej domeny.

2. Zainicjuj protokół Kerberos. W terminalu SSH wpisz następujące polecenie:

    > [!TIP]
    > * Upewnij się, że określono użytkownika, który należy do grupy "Administratorzy usługi AAD DC".
    > * Podaj nazwę domeny z dużymi literami, w przeciwnym razie narzędzie kinit nie powiedzie się.
    >

    ```console
    kinit bob@CONTOSO100.COM
    ```

3. Dołącz komputer do domeny. W terminalu SSH wpisz następujące polecenie:

    > [!TIP]
    > Użyj tego samego konta użytkownika, które zostało określone w poprzednim kroku ("Narzędzie kinit").
    >
    > Jeśli maszyna wirtualna nie może przyłączyć się do domeny, upewnij się, że sieciowa Grupa zabezpieczeń maszyny wirtualnej zezwala na ruch wychodzący Kerberos na porcie TCP + UDP 464 do podsieci sieci wirtualnej dla domeny zarządzanej platformy Azure AD DS.

    ```console
    sudo realm join --verbose CONTOSO100.COM -U 'bob@CONTOSO100.COM' --install=/
    ```

Po pomyślnym przyłączeniu komputera do domeny zarządzanej powinien zostać wyświetlony komunikat ("pomyślnie zarejestrowano maszynę w obszarze").


## <a name="update-the-sssd-configuration-and-restart-the-service"></a>Aktualizowanie konfiguracji SSSD i ponowne uruchamianie usługi
1. W terminalu SSH wpisz następujące polecenie. Otwórz plik SSSD. conf i wprowadź następujące zmiany
    
    ```console
    sudo vi /etc/sssd/sssd.conf
    ```

2. Dodaj komentarz do wiersza **use_fully_qualified_names = true** i Zapisz plik.
    
    ```console
    # use_fully_qualified_names = True
    ```

3. Uruchom ponownie usługę SSSD.
    
    ```console
    sudo service sssd restart
    ```


## <a name="configure-automatic-home-directory-creation"></a>Konfigurowanie automatycznego tworzenia katalogów macierzystych
Aby włączyć automatyczne tworzenie katalogu macierzystego po zalogowaniu się użytkowników, wpisz następujące polecenia w terminalu:

```console
sudo vi /etc/pam.d/common-session
```

Dodaj następujący wiersz w tym pliku poniżej wiersza "Session Optional pam_sss. so" i Zapisz go:

```console
session required pam_mkhomedir.so skel=/etc/skel/ umask=0077
```


## <a name="verify-domain-join"></a>Weryfikuj przyłączanie do domeny
Sprawdź, czy maszyna została pomyślnie przyłączona do domeny zarządzanej. Nawiąż połączenie z dołączoną do domeny maszyną wirtualną Ubuntu przy użyciu innego połączenia SSH. Użyj konta użytkownika domeny, a następnie sprawdź, czy konto użytkownika jest prawidłowo rozwiązane.

1. W terminalu SSH wpisz następujące polecenie, aby nawiązać połączenie z przyłączoną do domeny maszyną wirtualną Ubuntu przy użyciu protokołu SSH. Użyj konta domeny, które należy do domeny zarządzanej (na przykład "bob@CONTOSO100.COM" w tym przypadku).
    
    ```console
    ssh -l bob@CONTOSO100.COM contoso-ubuntu.contoso100.com
    ```

2. W terminalu SSH wpisz następujące polecenie, aby sprawdzić, czy katalog macierzysty został zainicjowany prawidłowo.
    
    ```console
    pwd
    ```

3. W terminalu SSH wpisz następujące polecenie, aby sprawdzić, czy członkostwa w grupach są poprawnie rozwiązane.
    
    ```console
    id
    ```


## <a name="grant-the-aad-dc-administrators-group-sudo-privileges"></a>Przyznaj grupie "Administratorzy usługi AAD DC" uprawnienia sudo
Można udzielić członkom uprawnień administracyjnych grupy administratorów usługi AAD na maszynie wirtualnej Ubuntu. Plik sudo znajduje się w lokalizacji/etc/sudoers. Członkowie grup usługi AD dodani w sudo mogą wykonywać sudo.

1. Upewnij się, że w terminalu SSH zarejestrowano Cię z uprawnieniami administratora. Możesz użyć konta administratora lokalnego, które zostało określone podczas tworzenia maszyny wirtualnej. Wykonaj następujące polecenie:
    
    ```console
    sudo vi /etc/sudoers
    ```

2. Dodaj następujący wpis do pliku/etc/sudoers i Zapisz go:
    
    ```console
    # Add 'AAD DC Administrators' group members as admins.
    %AAD\ DC\ Administrators ALL=(ALL) NOPASSWD:ALL
    ```

3. Możesz teraz zalogować się jako członek grupy "Administratorzy usługi AAD DC" i mieć uprawnienia administratora na maszynie wirtualnej.


## <a name="troubleshooting-domain-join"></a>Rozwiązywanie problemów z przyłączaniem domeny
Zobacz artykuł [Rozwiązywanie problemów](join-windows-vm.md#troubleshoot-joining-a-domain) z przyłączaniem do domeny.


## <a name="related-content"></a>Powiązana zawartość
* [Przewodnik po Wprowadzenie Azure AD Domain Services](create-instance.md)
* [Przyłączanie maszyny wirtualnej z systemem Windows Server do domeny zarządzanej Azure AD Domain Services](active-directory-ds-admin-guide-join-windows-vm.md)
* [Jak zalogować się do maszyny wirtualnej z systemem Linux](../virtual-machines/linux/mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
