---
title: Przyłączanie maszyny wirtualnej RHEL do Azure AD Domain Services | Microsoft Docs
description: Dowiedz się, jak skonfigurować maszynę wirtualną Red Hat Enterprise Linux i przyłączyć ją do domeny zarządzanej Azure AD Domain Services.
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: 16100caa-f209-4cb0-86d3-9e218aeb51c6
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 09/15/2019
ms.author: iainfou
ms.openlocfilehash: 9472abd7a16c887a796e36b8190e8530c84dafa9
ms.sourcegitcommit: 8074f482fcd1f61442b3b8101f153adb52cf35c9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/22/2019
ms.locfileid: "72755709"
---
# <a name="join-a-red-hat-enterprise-linux-virtual-machine-to-an-azure-ad-domain-services-managed-domain"></a>Dołączanie maszyny wirtualnej z systemem Red Hat Enterprise Linux do domeny zarządzanej Usług domenowych Azure AD

Aby umożliwić użytkownikom logowanie się do maszyn wirtualnych na platformie Azure przy użyciu jednego zestawu poświadczeń, można przyłączyć maszyny wirtualne do domeny zarządzanej Azure Active Directory Domain Services (AD DS). Po dołączeniu maszyny wirtualnej do domeny zarządzanej AD DS platformy Azure można użyć kont użytkowników i poświadczeń z domeny, aby zalogować się i zarządzać serwerami. Członkostwa w grupach z domeny zarządzanej AD DS platformy Azure są również stosowane w celu umożliwienia kontroli dostępu do plików lub usług na maszynie wirtualnej.

W tym artykule opisano sposób przyłączania maszyny wirtualnej z systemem Red Hat Enterprise Linux (RHEL) do domeny zarządzanej AD DS platformy Azure.

## <a name="prerequisites"></a>Wymagania wstępne

Do ukończenia tego samouczka potrzebne są następujące zasoby i uprawnienia:

* Aktywna subskrypcja platformy Azure.
    * Jeśli nie masz subskrypcji platformy Azure, [Utwórz konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Dzierżawa usługi Azure Active Directory skojarzona z subskrypcją, zsynchronizowana z katalogiem lokalnym lub katalogiem w chmurze.
    * W razie konieczności [Utwórz dzierżawę Azure Active Directory][create-azure-ad-tenant] lub [skojarz subskrypcję platformy Azure z Twoim kontem][associate-azure-ad-tenant].
* Azure Active Directory Domain Services zarządzana domena włączona i skonfigurowana w dzierżawie usługi Azure AD.
    * W razie konieczności pierwszy samouczek [tworzy i konfiguruje wystąpienie Azure Active Directory Domain Services][create-azure-ad-ds-instance].
* Konto użytkownika, które jest członkiem grupy *administratorów DC usługi Azure AD* w dzierżawie usługi Azure AD.

## <a name="create-and-connect-to-a-rhel-linux-vm"></a>Tworzenie maszyny wirtualnej z systemem RHEL Linux i nawiązywanie z nią połączenia

Jeśli masz istniejącą maszynę wirtualną z systemem Linux RHEL na platformie Azure, Połącz się z nią przy użyciu protokołu SSH, a następnie przejdź do następnego kroku, aby [rozpocząć konfigurowanie maszyny wirtualnej](#configure-the-hosts-file).

Jeśli musisz utworzyć maszynę wirtualną z systemem Linux RHEL lub chcesz utworzyć testową maszynę wirtualną do użycia z tym artykułem, możesz użyć jednej z następujących metod:

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
* *RHEL* jest nazwą hosta maszyny wirtualnej RHEL, która jest dołączana do domeny zarządzanej.

Zaktualizuj te nazwy przy użyciu własnych wartości:

```console
127.0.0.1 rhel rhel.contoso.com
```

Po zakończeniu Zapisz i wyjdź z pliku *hosts* za pomocą polecenia `:wq` edytora.

## <a name="install-required-packages"></a>Instalowanie wymaganych pakietów

Maszyna wirtualna wymaga dodatkowych pakietów do przyłączenia maszyny wirtualnej do domeny zarządzanej AD DS platformy Azure. Aby zainstalować i skonfigurować te pakiety, zaktualizuj i zainstaluj narzędzia do przyłączania do domeny przy użyciu `yum`:

 **RHEL 7** 

```console
sudo yum install realmd sssd krb5-workstation krb5-libs oddjob oddjob-mkhomedir samba-common-tools
```

 **RHEL 6** 

```console
sudo yum install adcli sssd authconfig krb5-workstation
```

## <a name="join-vm-to-the-managed-domain"></a>Dołącz maszynę wirtualną do domeny zarządzanej

Teraz, gdy wymagane pakiety są zainstalowane na maszynie wirtualnej, Dołącz maszynę wirtualną do domeny zarządzanej AD DS platformy Azure.
 
  **RHEL 7**
     
1. Użyj `realm discover` polecenia, aby odnaleźć domenę zarządzaną platformy Azure AD DS. Poniższy przykład umożliwia odnajdywanie obszaru *contoso.com*. Określ własną nazwę domeny zarządzanej przez usługę Azure AD DS w dowolnej wielkiej litery:

    ```console
    sudo realm discover CONTOSO.COM
    ```

   Jeśli polecenie `realm discover` nie może znaleźć domeny zarządzanej AD DS platformy Azure, zapoznaj się z następującymi krokami rozwiązywania problemów:
   
    * Upewnij się, że domena jest osiągalna z maszyny wirtualnej. Spróbuj `ping contoso.com`, aby sprawdzić, czy jest zwracana pozytywna odpowiedź.
    * Sprawdź, czy maszyna wirtualna jest wdrożona w tej samej lub równorzędnej sieci wirtualnej, w której jest dostępna domena zarządzana platformy Azure AD DS.
    * Upewnij się, że ustawienia serwera DNS dla sieci wirtualnej zostały zaktualizowane w taki sposób, aby wskazywały kontrolery domeny w domenie zarządzanej platformy Azure AD DS.

1. Teraz zainicjuj protokół Kerberos za pomocą polecenia `kinit`. Określ użytkownika, który należy do grupy *administratorów kontrolera domeny usługi AAD* . W razie potrzeby [Dodaj konto użytkownika do grupy w usłudze Azure AD](../active-directory/fundamentals/active-directory-groups-members-azure-portal.md).

    Ponownie nazwa domeny zarządzanej platformy Azure AD DS musi być wpisana WIELKImi LITERAmi. W poniższym przykładzie konto o nazwie `contosoadmin@contoso.com` jest używane do inicjowania protokołu Kerberos. Wprowadź własne konto użytkownika, które jest członkiem grupy *administratorów domeny usługi AAD* :
    
    ```console
    kinit contosoadmin@CONTOSO.COM
    ``` 

1. Na koniec Dołącz maszynę do domeny zarządzanej AD DS platformy Azure przy użyciu polecenia `realm join`. Użyj tego samego konta użytkownika, które jest członkiem grupy *administratorów domeny usługi AAD* , która została określona w poprzednim `kinit` polecenie, na przykład `contosoadmin@CONTOSO.COM`:

    ```console
    sudo realm join --verbose CONTOSO.COM -U 'contosoadmin@CONTOSO.COM'
    ```

Dołączenie maszyny wirtualnej do domeny zarządzanej platformy Azure AD DS może chwilę potrwać. Następujące przykładowe dane wyjściowe pokazują, że maszyna wirtualna została pomyślnie dołączona do domeny zarządzanej AD DS platformy Azure:

```output
Successfully enrolled machine in realm
```

  **RHEL 6** 

1. Użyj `adcli info` polecenia, aby odnaleźć domenę zarządzaną platformy Azure AD DS. Poniższy przykład umożliwia odnajdywanie obszaru *contoso.com*. Określ własną nazwę domeny zarządzanej przez usługę Azure AD DS w dowolnej wielkiej litery:

    ```console
    sudo adcli info contoso.com
    ```
    
   Jeśli polecenie `adcli info` nie może znaleźć domeny zarządzanej AD DS platformy Azure, zapoznaj się z następującymi krokami rozwiązywania problemów:
   
    * Upewnij się, że domena jest osiągalna z maszyny wirtualnej. Spróbuj `ping contoso.com`, aby sprawdzić, czy jest zwracana pozytywna odpowiedź.
    * Sprawdź, czy maszyna wirtualna jest wdrożona w tej samej lub równorzędnej sieci wirtualnej, w której jest dostępna domena zarządzana platformy Azure AD DS.
    * Upewnij się, że ustawienia serwera DNS dla sieci wirtualnej zostały zaktualizowane w taki sposób, aby wskazywały kontrolery domeny w domenie zarządzanej platformy Azure AD DS.

1. Najpierw Przyłącz się do domeny za pomocą polecenia `adcli join`, to polecenie spowoduje również utworzenie plik KEYTAB do uwierzytelnienia maszyny. Użyj konta użytkownika, które jest członkiem grupy *administratorów domeny usługi AAD* . 

    ```console
    sudo adcli join contoso.com -U contosoadmin
    ```

1. Teraz Skonfiguruj `/ect/krb5.conf` i Utwórz pliki `/etc/sssd/sssd.conf` do używania `contoso.com` Active Directory domeny. 
   Upewnij się, że `CONTOSO.COM` jest zastępowana przez własną nazwę domeny:

    Otwórz plik `/ect/krb5.conf` z edytorem:

    ```console
    sudo vi /etc/krb5.conf
    ```

    Zaktualizuj plik `krb5.conf`, aby pasował do poniższego przykładu:

    ```console
    [logging]
     default = FILE:/var/log/krb5libs.log
     kdc = FILE:/var/log/krb5kdc.log
     admin_server = FILE:/var/log/kadmind.log
    
    [libdefaults]
     default_realm = CONTOSO.COM
     dns_lookup_realm = true
     dns_lookup_kdc = true
     ticket_lifetime = 24h
     renew_lifetime = 7d
     forwardable = true
    
    [realms]
     CONTOSO.COM = {
     kdc = CONTOSO.COM
     admin_server = CONTOSO.COM
     }
    
    [domain_realm]
     .CONTOSO.COM = CONTOSO.COM
     CONTOSO.COM = CONTOSO.COM
    ```
    
   Utwórz plik `/etc/sssd/sssd.conf`:
    
    ```console
    sudo vi /etc/sssd/sssd.conf
    ```

    Zaktualizuj plik `sssd.conf`, aby pasował do poniższego przykładu:

    ```console
    [sssd]
     services = nss, pam, ssh, autofs
     config_file_version = 2
     domains = CONTOSO.COM
    
    [domain/CONTOSO.COM]
    
     id_provider = ad
    ```

1. Upewnij się, że uprawnienia `/etc/sssd/sssd.conf` są 600 i należą do użytkownika root:

    ```console
    sudo chmod 600 /etc/sssd/sssd.conf
    sudo chown root:root /etc/sssd/sssd.conf
    ```

1. Użyj `authconfig`, aby nakazać maszynie wirtualnej integrację z usługą AD systemu Linux:

    ```console
    sudo authconfig --enablesssd --enablesssdauth --update
    ```
    
1. Uruchom i Włącz usługę SSSD:

    ```console
    sudo service sssd start
    sudo chkconfig sssd on
    ```

Jeśli maszyna wirtualna nie może pomyślnie ukończyć procesu przyłączania do domeny, upewnij się, że sieciowa Grupa zabezpieczeń maszyny wirtualnej zezwala na ruch wychodzący protokołu Kerberos na porcie TCP + UDP 464 do podsieci sieci wirtualnej dla domeny zarządzanej platformy Azure AD DS.

Sprawdź teraz, czy możesz wysyłać zapytania o informacje o użytkowniku przy użyciu `getent`

```console
sudo getent passwd contosoadmin
```

## <a name="allow-password-authentication-for-ssh"></a>Zezwalaj na uwierzytelnianie za pośrednictwem protokołu SSH

Domyślnie użytkownicy mogą logować się tylko do maszyny wirtualnej przy użyciu uwierzytelniania opartego na kluczu publicznym SSH. Uwierzytelnianie oparte na hasłach kończy się niepowodzeniem. Po dołączeniu maszyny wirtualnej do domeny zarządzanej AD DS platformy Azure te konta domeny muszą używać uwierzytelniania opartego na hasłach. Zaktualizuj konfigurację protokołu SSH, aby zezwolić na uwierzytelnianie oparte na hasłach w następujący sposób.

1. Otwórz plik *sshd_conf* z edytorem:

    ```console
    sudo vi /etc/ssh/sshd_config
    ```

1. Zaktualizuj wiersz dla elementu *PasswordAuthentication* do *tak*:

    ```console
    PasswordAuthentication yes
    ```

    Po zakończeniu Zapisz i zamknij plik *sshd_conf* za pomocą polecenia `:wq` edytora.

1. Aby zastosować zmiany i umożliwić użytkownikom logowanie się przy użyciu hasła, należy ponownie uruchomić usługę SSH:

   **RHEL 7** 
    
    ```console
    sudo systemctl restart sshd
    ```

   **RHEL 6** 
    
    ```console
    sudo service sshd restart
    ```

## <a name="grant-the-aad-dc-administrators-group-sudo-privileges"></a>Przyznaj grupie "Administratorzy usługi AAD DC" uprawnienia sudo

Aby udzielić członkom uprawnień administracyjnych grupy *administratorów usługi AAD* na maszynie wirtualnej RHEL, należy dodać wpis do */etc/sudoers*. Po dodaniu członkowie grupy *Administratorzy domeny usługi AAD* mogą używać polecenia `sudo` na maszynie wirtualnej RHEL.

1. Otwórz plik *sudo* do edycji:

    ```console
    sudo visudo
    ```

1. Dodaj następujący wpis na końcu pliku */etc/sudoers* . Grupa *Administratorzy domeny usługi AAD* zawiera odstępy w nazwie, dlatego w nazwie grupy należy umieścić znak ucieczki odwrotnej kreski ułamkowej. Dodaj własną nazwę domeny, taką jak *contoso.com*:

    ```console
    # Add 'AAD DC Administrators' group members as admins.
    %AAD\ DC\ Administrators@contoso.com ALL=(ALL) NOPASSWD:ALL
    ```

    Po zakończeniu Zapisz i wyjdź z edytora przy użyciu `:wq` polecenia edytora.

## <a name="sign-in-to-the-vm-using-a-domain-account"></a>Logowanie się do maszyny wirtualnej przy użyciu konta domeny

Aby sprawdzić, czy maszyna wirtualna została pomyślnie dołączona do domeny zarządzanej AD DS platformy Azure, uruchom nowe połączenie SSH przy użyciu konta użytkownika domeny. Upewnij się, że katalog macierzysty został utworzony, a członkostwo w grupie jest stosowane.

1. Utwórz nowe połączenie SSH z poziomu konsoli. Użyj konta domeny należącego do domeny zarządzanej przy użyciu polecenia `ssh -l`, takiego jak `contosoadmin@contoso.com`, a następnie wprowadź adres maszyny wirtualnej, na przykład *RHEL.contoso.com*. Jeśli używasz Azure Cloud Shell, użyj publicznego adresu IP maszyny wirtualnej, a nie wewnętrznej nazwy DNS.

    ```console
    ssh -l contosoadmin@CONTOSO.com rhel.contoso.com
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
    sudo yum update
    ```

## <a name="next-steps"></a>Następne kroki

Jeśli masz problemy z połączeniem maszyny wirtualnej z domeną zarządzaną platformy Azure AD DS lub zalogowanie się przy użyciu konta domeny, zobacz [Rozwiązywanie problemów z przyłączaniem do domeny](join-windows-vm.md#troubleshoot-domain-join-issues).

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
