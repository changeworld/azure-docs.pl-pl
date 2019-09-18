---
title: Przyłączanie maszyny wirtualnej Ubuntu do Azure AD Domain Services | Microsoft Docs "
description: Dowiedz się, jak skonfigurować maszynę wirtualną Ubuntu Linux i przyłączyć ją do domeny zarządzanej Azure AD Domain Services.
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: 804438c4-51a1-497d-8ccc-5be775980203
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 09/15/2019
ms.author: iainfou
ms.openlocfilehash: e92327323f632f6b922e3eb948df75bb3666e2a9
ms.sourcegitcommit: 8ef0a2ddaece5e7b2ac678a73b605b2073b76e88
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/17/2019
ms.locfileid: "71075377"
---
# <a name="join-an-ubuntu-linux-virtual-machine-to-an-azure-ad-domain-services-managed-domain"></a>Przyłączanie maszyny wirtualnej Ubuntu Linux do domeny zarządzanej Azure AD Domain Services

Aby umożliwić użytkownikom logowanie się do maszyn wirtualnych na platformie Azure przy użyciu jednego zestawu poświadczeń, można przyłączyć maszyny wirtualne do domeny zarządzanej Azure Active Directory Domain Services (AD DS). Po dołączeniu maszyny wirtualnej do domeny zarządzanej AD DS platformy Azure można użyć kont użytkowników i poświadczeń z domeny, aby zalogować się i zarządzać serwerami. Członkostwa w grupach z domeny zarządzanej AD DS platformy Azure są również stosowane w celu umożliwienia kontroli dostępu do plików lub usług na maszynie wirtualnej.

W tym artykule opisano sposób przyłączania maszyny wirtualnej Ubuntu Linux do domeny zarządzanej AD DS platformy Azure.

## <a name="prerequisites"></a>Wymagania wstępne

Do ukończenia tego samouczka potrzebne są następujące zasoby i uprawnienia:

* Aktywna subskrypcja platformy Azure.
    * Jeśli nie masz subskrypcji platformy Azure, [Utwórz konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Dzierżawa usługi Azure Active Directory skojarzona z subskrypcją, zsynchronizowana z katalogiem lokalnym lub katalogiem w chmurze.
    * W razie konieczności [Utwórz dzierżawę Azure Active Directory][create-azure-ad-tenant] lub [skojarz subskrypcję platformy Azure z Twoim kontem][associate-azure-ad-tenant].
* Azure Active Directory Domain Services zarządzana domena włączona i skonfigurowana w dzierżawie usługi Azure AD.
    * W razie konieczności pierwszy samouczek [tworzy i konfiguruje wystąpienie Azure Active Directory Domain Services][create-azure-ad-ds-instance].
* Konto użytkownika, które jest członkiem grupy *administratorów DC usługi Azure AD* w dzierżawie usługi Azure AD.

## <a name="create-and-connect-to-an-ubuntu-linux-vm"></a>Tworzenie maszyny wirtualnej Ubuntu Linux i nawiązywanie z nią połączenia

Jeśli masz istniejącą Ubuntu Linux maszynę wirtualną na platformie Azure, Połącz się z nią przy użyciu protokołu SSH, a następnie przejdź do następnego kroku, aby [rozpocząć konfigurowanie maszyny wirtualnej](#configure-the-hosts-file).

Jeśli musisz utworzyć maszynę wirtualną Ubuntu Linux lub chcesz utworzyć testową maszynę wirtualną do użycia z tym artykułem, możesz użyć jednej z następujących metod:

* [Azure Portal](../virtual-machines/linux/quick-create-portal.md)
* [Interfejs wiersza polecenia platformy Azure](../virtual-machines/linux/quick-create-cli.md)
* [Azure PowerShell](../virtual-machines/linux/quick-create-powershell.md)

Podczas tworzenia maszyny wirtualnej należy zwrócić uwagę na ustawienia sieci wirtualnej, aby upewnić się, że maszyna wirtualna może komunikować się z domeną zarządzaną platformy Azure AD DS:

* Wdróż maszynę wirtualną w tej samej lub równorzędnej sieci wirtualnej, w której włączono Azure AD Domain Services.
* Wdróż maszynę wirtualną w innej podsieci niż wystąpienie Azure AD Domain Services.

Po wdrożeniu maszyny wirtualnej postępuj zgodnie z instrukcjami, aby nawiązać połączenie z maszyną wirtualną przy użyciu protokołu SSH.

## <a name="configure-the-hosts-file"></a>Konfigurowanie pliku hosts

Aby upewnić się, że nazwa hosta maszyny wirtualnej jest prawidłowo skonfigurowana dla domeny zarządzanej, edytuj plik */etc/hosts* i Ustaw nazwę hosta:

```console
sudo vi /etc/hosts
```

W pliku *hosts* zaktualizuj adres *localhost* . W poniższym przykładzie:

* *contoso.com* to nazwa domeny DNS domeny zarządzanej AD DS platformy Azure.
* *Ubuntu* jest nazwą hosta maszyny wirtualnej Ubuntu, która jest dołączana do domeny zarządzanej.

Zaktualizuj te nazwy przy użyciu własnych wartości:

```console
127.0.0.1 ubuntu.contoso.com ubuntu
```

Po zakończeniu Zapisz i wyjdź z pliku *hosts* za pomocą `:wq` polecenia edytora.

## <a name="install-required-packages"></a>Instalowanie wymaganych pakietów

Maszyna wirtualna wymaga dodatkowych pakietów do przyłączenia maszyny wirtualnej do domeny zarządzanej AD DS platformy Azure. Aby zainstalować i skonfigurować te pakiety, zaktualizuj i zainstaluj narzędzia do przyłączania do domeny za pomocą`apt-get`

Podczas instalacji protokołu Kerberos pakiet *krb5-User* będzie monitował o nazwę obszaru we wszystkich wielkich. Na przykład jeśli nazwa domeny zarządzanej platformy Azure AD DS to *contoso.com*, wprowadź *contoso.com* jako obszar. Instalacja zapisuje `[realm]` sekcje i `[domain_realm]` w pliku konfiguracji */etc/krb5.conf* . Upewnij się, że dla obszaru określono wszystkie wielkie litery:

```console
sudo apt-get update
sudo apt-get install krb5-user samba sssd sssd-tools libnss-sss libpam-sss ntp ntpdate realmd adcli
```

## <a name="configure-network-time-protocol-ntp"></a>Konfigurowanie protokołu NTP (Network Time Protocol)

Aby komunikacja w domenie działała poprawnie, Data i godzina maszyny wirtualnej Ubuntu muszą być synchronizowane z domeną zarządzaną platformy Azure AD DS. Dodaj nazwę hosta NTP w domenie zarządzanej platformy Azure AD DS do pliku */etc/ntp.conf* .

1. Otwórz plik *NTP. conf* z edytorem:

    ```console
    sudo vi /etc/ntp.conf
    ```

1. W pliku *NTP. conf* Utwórz wiersz, aby dodać nazwę DNS domeny zarządzanej platformy Azure AD DS. W poniższym przykładzie zostanie dodany wpis dla *contoso.com* . Użyj własnej nazwy DNS:

    ```console
    server contoso.com
    ```

    Po zakończeniu Zapisz i wyjdź z pliku *NTP. conf* przy użyciu `:wq` polecenia edytora.

1. Aby upewnić się, że maszyna wirtualna jest zsynchronizowana z domeną zarządzaną platformy Azure AD DS, wymagane są następujące kroki:

    * Zatrzymaj serwer NTP
    * Aktualizowanie daty i godziny z domeny zarządzanej
    * Uruchom usługę NTP

    Uruchom następujące polecenia, aby wykonać te kroki. Użyj własnej nazwy DNS z `ntpdate` poleceniem:

    ```console
    sudo systemctl stop ntp
    sudo ntpdate contoso.com
    sudo systemctl start ntp
    ```

## <a name="join-vm-to-the-managed-domain"></a>Dołącz maszynę wirtualną do domeny zarządzanej

Teraz, gdy wymagane pakiety są zainstalowane na maszynie wirtualnej, a NTP jest skonfigurowany, Dołącz maszynę wirtualną do domeny zarządzanej AD DS platformy Azure.

1. `realm discover` Użyj polecenia, aby odnaleźć domenę zarządzaną platformy Azure AD DS. Poniższy przykład umożliwia odnajdywanie obszaru *contoso.com*. Określ własną nazwę domeny zarządzanej przez usługę Azure AD DS w dowolnej wielkiej litery:

    ```console
    sudo realm discover CONTOSO.COM
    ```

   `realm discover` Jeśli polecenie nie może znaleźć domeny zarządzanej AD DS platformy Azure, zapoznaj się z następującymi krokami rozwiązywania problemów:

    * Upewnij się, że domena jest osiągalna z maszyny wirtualnej. Spróbuj `ping contoso.com` sprawdzić, czy jest zwracana pozytywna odpowiedź.
    * Sprawdź, czy maszyna wirtualna jest wdrożona w tej samej lub równorzędnej sieci wirtualnej, w której jest dostępna domena zarządzana platformy Azure AD DS.
    * Upewnij się, że ustawienia serwera DNS dla sieci wirtualnej zostały zaktualizowane w taki sposób, aby wskazywały kontrolery domeny w domenie zarządzanej platformy Azure AD DS.

1. Teraz zainicjuj protokół Kerberos `kinit` za pomocą polecenia. Określ użytkownika, który należy do grupy *administratorów kontrolera domeny usługi AAD* . W razie potrzeby [Dodaj konto użytkownika do grupy w usłudze Azure AD](../active-directory/fundamentals/active-directory-groups-members-azure-portal.md).

    Ponownie nazwa domeny zarządzanej platformy Azure AD DS musi być wpisana WIELKImi LITERAmi. W poniższym przykładzie konto o nazwie `contosoadmin@contoso.com` jest używane do inicjowania protokołu Kerberos. Wprowadź własne konto użytkownika, które jest członkiem grupy *administratorów domeny usługi AAD* :

    ```console
    kinit contosoadmin@CONTOSO.COM
    ```

1. Na koniec Dołącz maszynę do domeny zarządzanej AD DS platformy Azure przy `realm join` użyciu polecenia. Użyj tego samego konta użytkownika, które jest członkiem grupy *administratorów domeny usługi AAD* , która została określona w poprzednim `kinit` poleceniu, na `contosoadmin@CONTOSO.COM`przykład:

    ```console
    sudo realm join --verbose CONTOSO.COM -U 'contosoadmin@CONTOSO.COM' --install=/
    ```

Dołączenie maszyny wirtualnej do domeny zarządzanej platformy Azure AD DS może chwilę potrwać. Następujące przykładowe dane wyjściowe pokazują, że maszyna wirtualna została pomyślnie dołączona do domeny zarządzanej AD DS platformy Azure:

```output
Successfully enrolled machine in realm
```

Jeśli maszyna wirtualna nie może pomyślnie ukończyć procesu przyłączania do domeny, upewnij się, że sieciowa Grupa zabezpieczeń maszyny wirtualnej zezwala na ruch wychodzący protokołu Kerberos na porcie TCP + UDP 464 do podsieci sieci wirtualnej dla domeny zarządzanej platformy Azure AD DS.

## <a name="update-the-sssd-configuration"></a>Aktualizowanie konfiguracji SSSD

Jeden z pakietów zainstalowanych w poprzednim kroku był przeznaczony dla demona usług zabezpieczeń systemu (SSSD). Gdy użytkownik próbuje zalogować się do maszyny wirtualnej przy użyciu poświadczeń domeny, SSSD przekazuje żądanie do dostawcy uwierzytelniania. W tym scenariuszu SSSD używa usługi Azure AD DS do uwierzytelniania żądania.

1. Otwórz plik *SSSD. conf* z edytorem:

    ```console
    sudo vi /etc/sssd/sssd.conf
    ```

1. Dodaj komentarz do wiersza dla *use_fully_qualified_names* w następujący sposób:

    ```console
    # use_fully_qualified_names = True
    ```

    Po zakończeniu Zapisz i zamknij plik *SSSD. conf* przy użyciu `:wq` polecenia edytora.

1. Aby zastosować zmianę, uruchom ponownie usługę SSSD:

    ```console
    sudo service sssd restart
    ```

## <a name="configure-user-account-and-group-settings"></a>Skonfiguruj ustawienia konta użytkownika i grupy

Jeśli maszyna wirtualna jest przyłączona do domeny zarządzanej AD DS platformy Azure i skonfigurowana do uwierzytelniania, istnieje kilka opcji konfiguracji użytkownika do ukończenia. Te zmiany konfiguracji obejmują możliwość uwierzytelniania opartego na hasłach oraz automatyczne tworzenie katalogów macierzystych na lokalnej maszynie wirtualnej, gdy użytkownicy domeny logują się po raz pierwszy.

### <a name="allow-password-authentication-for-ssh"></a>Zezwalaj na uwierzytelnianie za pośrednictwem protokołu SSH

Domyślnie użytkownicy mogą logować się tylko do maszyny wirtualnej przy użyciu uwierzytelniania opartego na kluczu publicznym SSH. Uwierzytelnianie oparte na hasłach kończy się niepowodzeniem. Po dołączeniu maszyny wirtualnej do domeny zarządzanej AD DS platformy Azure te konta domeny muszą używać uwierzytelniania opartego na hasłach. Zaktualizuj konfigurację protokołu SSH, aby zezwolić na uwierzytelnianie oparte na hasłach w następujący sposób.

1. Otwórz plik *sshd_conf* z edytorem:

    ```console
    sudo vi /etc/ssh/sshd_config
    ```

1. Zaktualizuj wiersz dla elementu *PasswordAuthentication* do *tak*:

    ```console
    PasswordAuthentication yes
    ```

    Po zakończeniu Zapisz i zamknij plik *sshd_conf* za pomocą `:wq` polecenia edytora.

1. Aby zastosować zmiany i umożliwić użytkownikom logowanie się przy użyciu hasła, należy ponownie uruchomić usługę SSH:

    ```console
    sudo systemctl restart ssh
    ```

### <a name="configure-automatic-home-directory-creation"></a>Konfigurowanie automatycznego tworzenia katalogów macierzystych

Aby włączyć automatyczne tworzenie katalogu macierzystego, gdy użytkownik loguje się po raz pierwszy, wykonaj następujące czynności:

1. Otwórz plik */etc/pam.d/Common-Session* w edytorze:

    ```console
    sudo vi /etc/pam.d/common-session
    ```

1. Dodaj następujący wiersz w tym pliku pod wierszem `session optional pam_sss.so`:

    ```console
    session required pam_mkhomedir.so skel=/etc/skel/ umask=0077
    ```

    Po zakończeniu Zapisz i zamknij plik *Common-Session* przy użyciu `:wq` polecenia edytora.

### <a name="grant-the-aad-dc-administrators-group-sudo-privileges"></a>Przyznaj grupie "Administratorzy usługi AAD DC" uprawnienia sudo

Aby udzielić członkom uprawnień administracyjnych grupy *administratorów usługi AAD* na maszynie wirtualnej Ubuntu, należy dodać wpis do */etc/sudoers*. Po dodaniu członkowie grupy *administratorów domeny usługi AAD* mogą używać `sudo` polecenia na maszynie wirtualnej Ubuntu.

1. Otwórz plik *sudo* do edycji:

    ```console
    sudo visudo
    ```

1. Dodaj następujący wpis na końcu pliku */etc/sudoers* :

    ```console
    # Add 'AAD DC Administrators' group members as admins.
    %AAD\ DC\ Administrators ALL=(ALL) NOPASSWD:ALL
    ```

    Po zakończeniu Zapisz i wyjdź z edytora przy użyciu `Ctrl-X` polecenia.

## <a name="sign-in-to-the-vm-using-a-domain-account"></a>Logowanie się do maszyny wirtualnej przy użyciu konta domeny

Aby sprawdzić, czy maszyna wirtualna została pomyślnie dołączona do domeny zarządzanej AD DS platformy Azure, uruchom nowe połączenie SSH przy użyciu konta użytkownika domeny. Upewnij się, że katalog macierzysty został utworzony, a członkostwo w grupie jest stosowane.

1. Utwórz nowe połączenie SSH z poziomu konsoli. Użyj konta domeny, które należy do domeny zarządzanej przy użyciu `ssh -l` polecenia, na przykład `contosoadmin@contoso.com` , a następnie wprowadź adres maszyny wirtualnej, na przykład *Ubuntu.contoso.com*. Jeśli używasz Azure Cloud Shell, użyj publicznego adresu IP maszyny wirtualnej, a nie wewnętrznej nazwy DNS.

    ```console
    ssh -l contosoadmin@CONTOSO.com ubuntu.contoso.com
    ```

1. Po pomyślnym nawiązaniu połączenia z maszyną wirtualną Sprawdź, czy katalog macierzysty został zainicjowany prawidłowo:

    ```console
    pwd
    ```

    Należy mieć katalog */Home* z własnym katalogiem, który odpowiada kontu użytkownika.

1. Teraz sprawdź, czy członkostwa w grupach są poprawnie rozwiązane:

    ```console
    id
    ```

    Członkostwa w grupach powinny być widoczne z domeny zarządzanej usługi Azure AD DS.

1. Jeśli zalogowano się do maszyny wirtualnej jako członek grupy *administratorów domeny usługi AAD* , sprawdź, czy można prawidłowo użyć `sudo` polecenia:

    ```console
    sudo apt-get update
    ```

## <a name="next-steps"></a>Następne kroki

Jeśli masz problemy z połączeniem maszyny wirtualnej z domeną zarządzaną platformy Azure AD DS lub zalogowanie się przy użyciu konta domeny, zobacz [Rozwiązywanie problemów z przyłączaniem do domeny](join-windows-vm.md#troubleshoot-domain-join-issues).

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
