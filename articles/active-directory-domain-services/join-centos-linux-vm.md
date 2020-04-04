---
title: Dołączanie maszyny Wirtualnej CentOS do usług domenowych usługi Azure AD | Dokumenty firmy Microsoft
description: Dowiedz się, jak skonfigurować maszynę wirtualną systemu CentOS z systemem Linux i dołączyć do domeny zarządzanej usług domenowych usługi Azure AD.
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: 16100caa-f209-4cb0-86d3-9e218aeb51c6
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 01/23/2020
ms.author: iainfou
ms.openlocfilehash: d08552dcae51c897f2419d94e5e61e857247f09a
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/03/2020
ms.locfileid: "80655238"
---
# <a name="join-a-centos-linux-virtual-machine-to-an-azure-ad-domain-services-managed-domain"></a>Dołączanie maszyny wirtualnej systemu CentOS z systemem Linux do domeny zarządzanej usług ad-domenowych usługi Azure

Aby umożliwić użytkownikom logowanie się do maszyn wirtualnych (VM) na platformie Azure przy użyciu jednego zestawu poświadczeń, można dołączyć maszyny wirtualne do domeny zarządzanej usług domenowych Active Directory (AD DS). Po dołączeniu maszyny Wirtualnej do domeny zarządzanej usług Azure AD DS konta użytkowników i poświadczenia z domeny mogą służyć do logowania się i zarządzania serwerami. Członkostwa w grupach z domeny zarządzanej usług Azure AD DS są również stosowane w celu umożliwienia kontrolowania dostępu do plików lub usług na maszynie Wirtualnej.

W tym artykule pokazano, jak dołączyć do maszyny Wirtualnej CentOS z systemem Linux do domeny zarządzanej usług Azure AD DS.

## <a name="prerequisites"></a>Wymagania wstępne

Aby ukończyć ten samouczek, potrzebne są następujące zasoby i uprawnienia:

* Aktywna subskrypcja platformy Azure.
    * Jeśli nie masz subskrypcji platformy Azure, [utwórz konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Dzierżawa usługi Azure Active Directory skojarzona z subskrypcją, zsynchronizowana z katalogiem lokalnym lub katalogiem tylko w chmurze.
    * W razie potrzeby [utwórz dzierżawę usługi Azure Active Directory][create-azure-ad-tenant] lub [skojarz subskrypcję platformy Azure z kontem.][associate-azure-ad-tenant]
* Domena zarządzana usługami domenowymi Usługi Active Directory platformy Azure włączona i skonfigurowana w dzierżawie usługi Azure AD.
    * W razie potrzeby pierwszy samouczek [tworzy i konfiguruje wystąpienie usług domenowych Usługi domenowe Active Directory][create-azure-ad-ds-instance]platformy Azure .
* Konto użytkownika, które jest częścią domeny zarządzanej usługi Azure AD DS.

## <a name="create-and-connect-to-a-centos-linux-vm"></a>Tworzenie maszyny wirtualnej z systemem Linux i łączenie się z nią

Jeśli masz istniejącą maszynę wirtualną CentOS z systemem Linux na platformie Azure, połącz się z nią za pomocą SSH, a następnie przejdź do następnego kroku, aby [rozpocząć konfigurowanie maszyny Wirtualnej](#configure-the-hosts-file).

Jeśli chcesz utworzyć maszynę wirtualną CentOS z systemem Linux lub chcesz utworzyć testową maszynę wirtualną do użycia w tym artykule, możesz użyć jednej z następujących metod:

* [Azure Portal](../virtual-machines/linux/quick-create-portal.md)
* [Interfejs wiersza polecenia platformy Azure](../virtual-machines/linux/quick-create-cli.md)
* [Azure PowerShell](../virtual-machines/linux/quick-create-powershell.md)

Podczas tworzenia maszyny Wirtualnej należy zwrócić uwagę na ustawienia sieci wirtualnej, aby upewnić się, że maszyna wirtualna może komunikować się z domeną zarządza zarządza zarządzana usługą Azure AD DS:

* Wdrażanie maszyny Wirtualnej w tej samej lub równorzędnej sieci wirtualnej, w której włączono usługi domenowe usługi Azure AD Domain Services.
* Wdrażanie maszyny Wirtualnej w innej podsieci niż wystąpienie usług domenowych usługi Azure AD.

Po wdrożeniu maszyny Wirtualnej wykonaj kroki, aby połączyć się z maszyną wirtualną przy użyciu SSH.

## <a name="configure-the-hosts-file"></a>Konfigurowanie pliku hosts

Aby upewnić się, że nazwa hosta maszyny Wirtualnej jest poprawnie skonfigurowana dla domeny zarządzanej, edytuj plik */etc/hosts* i ustaw nazwę hosta:

```console
sudo vi /etc/hosts
```

W pliku *hosts* zaktualizuj adres *hosta lokalnego.* W poniższym przykładzie:

* *aaddscontoso.com* jest nazwą domeny DNS domeny zarządzanej usługą Azure AD DS.
* *centos* to nazwa hosta maszyny Wirtualnej CentOS, do której dołączasz do domeny zarządzanej.

Zaktualizuj te nazwy za pomocą własnych wartości:

```console
127.0.0.1 centos.aaddscontoso.com centos
```

Po zakończeniu zapisz i *hosts* zamknij plik `:wq` hosts za pomocą polecenia edytora.

## <a name="install-required-packages"></a>Instalowanie wymaganych pakietów

Maszyna wirtualna potrzebuje dodatkowych pakietów, aby dołączyć do maszyny Wirtualnej do domeny zarządzanej usług Azure AD DS. Aby zainstalować i skonfigurować te pakiety, zaktualizuj i zainstaluj narzędzia dołączenia do domeny za pomocą `yum`:

```console
sudo yum install realmd sssd krb5-workstation krb5-libs oddjob oddjob-mkhomedir samba-common-tools
```

## <a name="join-vm-to-the-managed-domain"></a>Dołączanie maszyny Wirtualnej do domeny zarządzanej

Teraz, gdy wymagane pakiety są zainstalowane na maszynie Wirtualnej, dołącz do maszyny Wirtualnej do domeny zarządzanej usług Azure AD DS.

1. Użyj `realm discover` polecenia, aby odkryć domenę zarządza zarządzana usługą Azure AD DS. W poniższym przykładzie odnajduje się *AADDSCONTOSO.COM*obszaru . Określ własną nazwę domeny zarządzanej usług Azure AD DS w all uppercase:

    ```console
    sudo realm discover AADDSCONTOSO.COM
    ```

   Jeśli `realm discover` polecenie nie może znaleźć domeny zarządzanej usług Azure AD DS, zapoznaj się z następującymi krokami rozwiązywania problemów:

    * Upewnij się, że domena jest osiągalna z maszyny Wirtualnej. Spróbuj `ping aaddscontoso.com` sprawdzić, czy odpowiedź pozytywna jest zwracana.
    * Sprawdź, czy maszyna wirtualna jest wdrażana w tej samej lub równorzędnej sieci wirtualnej, w której jest dostępna domena zarządzana usługą Azure AD DS.
    * Upewnij się, że ustawienia serwera DNS dla sieci wirtualnej zostały zaktualizowane w celu wskazania kontrolerów domeny domeny zarządzanej usług Azure AD DS.

1. Teraz zainiszczaj protokół Kerberos za pomocą `kinit` polecenia. Określ użytkownika, który jest częścią domeny zarządzanej usług Azure AD DS. W razie potrzeby [dodaj konto użytkownika do grupy w usłudze Azure AD](../active-directory/fundamentals/active-directory-groups-members-azure-portal.md).

    Ponownie nazwa domeny domeny zarządzanej usług Azure AD DS musi zostać wprowadzona w all uppercase. W poniższym przykładzie `contosoadmin@aaddscontoso.com` konto o nazwie jest używane do inicjowania protokołu Kerberos. Wprowadź własne konto użytkownika, które jest częścią domeny zarządzanej usług Azure AD DS:

    ```console
    kinit contosoadmin@AADDSCONTOSO.COM
    ```

1. Na koniec dołącz do komputera do domeny zarządzanej usług Azure AD DS za pomocą `realm join` polecenia. Użyj tego samego konta użytkownika, które jest częścią domeny zarządzanej usługi `kinit` Azure AD `contosoadmin@AADDSCONTOSO.COM`DS określonej w poprzednim poleceniu, takiej jak:

    ```console
    sudo realm join --verbose AADDSCONTOSO.COM -U 'contosoadmin@AADDSCONTOSO.COM'
    ```

To trwa kilka chwil, aby dołączyć do maszyny Wirtualnej do domeny zarządzanej usług Ad DS platformy Azure. Poniższe przykładowe dane wyjściowe pokazują, że maszyna wirtualna pomyślnie dołączyła do domeny zarządzanej usług Azure AD DS:

```output
Successfully enrolled machine in realm
```

Jeśli maszyna wirtualna nie może pomyślnie ukończyć procesu dołączania do domeny, upewnij się, że sieciowa grupa zabezpieczeń maszyny Wirtualnej zezwala na ruch wychodzący protokołu Kerberos na porcie TCP + UDP 464 do podsieci sieci wirtualnej domeny zarządzanej usługAmi AD DS.

## <a name="allow-password-authentication-for-ssh"></a>Zezwalaj na uwierzytelnianie hasłem dla SSH

Domyślnie użytkownicy mogą logować się tylko do maszyny Wirtualnej przy użyciu uwierzytelniania opartego na kluczach publicznych SSH. Uwierzytelnianie oparte na hasłach kończy się niepowodzeniem. Po dołączeniu maszyny Wirtualnej do domeny zarządzanej usług Azure AD DS te konta domeny muszą używać uwierzytelniania opartego na hasłach. Zaktualizuj konfigurację SSH, aby zezwolić na uwierzytelnianie oparte na hasłach w następujący sposób.

1. Otwórz plik *sshd_conf* za pomocą edytora:

    ```console
    sudo vi /etc/ssh/sshd_config
    ```

1. Zaktualizuj wiersz dla *PasswordAuthentication* do *tak:*

    ```console
    PasswordAuthentication yes
    ```

    Po zakończeniu zapisz i zamknij plik *sshd_conf* `:wq` za pomocą polecenia edytora.

1. Aby zastosować zmiany i umożliwić użytkownikom zalogowanie się przy użyciu hasła, uruchom ponownie usługę SSH:

    ```console
    sudo systemctl restart sshd
    ```

## <a name="grant-the-aad-dc-administrators-group-sudo-privileges"></a>Przyznawanie uprawnień sudo grupy "Administratorzy kontrolera domeny usługi AAD"

Aby przyznać członkom grupy *administratorów kontrolera domeny usługi AAD* uprawnienia administracyjne na maszynie Wirtualnej CentOS, należy dodać wpis do */etc/sudoers*. Po dodaniu członkowie grupy *Administratorzy kontrolera domeny usługi AAD* mogą używać `sudo` tego polecenia na maszynie Wirtualnej CentOS.

1. Otwórz plik *sudoers do* edycji:

    ```console
    sudo visudo
    ```

1. Dodaj następujący wpis na końcu *pliku /etc/sudoers.* Grupa *Administratorzy kontrolera domeny usługi AAD* zawiera odstępy w nazwie, więc dołącz znak ucieczki ukośnika odwrotnego w nazwie grupy. Dodaj własną nazwę domeny, taką jak *aaddscontoso.com:*

    ```console
    # Add 'AAD DC Administrators' group members as admins.
    %AAD\ DC\ Administrators@aaddscontoso.com ALL=(ALL) NOPASSWD:ALL
    ```

    Po zakończeniu zapisz i zamknij `:wq` edytor za pomocą polecenia edytora.

## <a name="sign-in-to-the-vm-using-a-domain-account"></a>Logowanie się do maszyny Wirtualnej przy użyciu konta domeny

Aby sprawdzić, czy maszyna wirtualna została pomyślnie przyłączona do domeny zarządzanej usług Azure AD DS, uruchom nowe połączenie SSH przy użyciu konta użytkownika domeny. Upewnij się, że utworzono katalog macierzysty i że członkostwo w grupie z domeny jest stosowane.

1. Utwórz nowe połączenie SSH z konsoli. Użyj konta domeny należącego do domeny `ssh -l` zarządzanej za `contosoadmin@aaddscontoso.com` pomocą polecenia, na przykład, a następnie wprowadź adres maszyny Wirtualnej, na przykład *centos.aaddscontoso.com*. Jeśli używasz usługi Azure Cloud Shell, użyj publicznego adresu IP maszyny Wirtualnej, a nie wewnętrznej nazwy DNS.

    ```console
    ssh -l contosoadmin@AADDSCONTOSO.com centos.aaddscontoso.com
    ```

1. Po pomyślnym nawiązaniu połączenia z maszyną wirtualną sprawdź, czy katalog macierzysty został poprawnie zainicjowany:

    ```console
    pwd
    ```

    Powinieneś znajdować się w *katalogu /home* z własnym katalogiem, który pasuje do konta użytkownika.

1. Teraz sprawdź, czy członkostwo w grupach jest poprawnie rozwiązywane:

    ```console
    id
    ```

    Powinny być widoczne członkostwa w grupach z domeny zarządzanej usług Azure AD DS.

1. Jeśli zalogowano się do maszyny Wirtualnej jako członek grupy *Administratorzy kontrolera domeny usługi AAD,* sprawdź, czy można poprawnie użyć `sudo` tego polecenia:

    ```console
    sudo yum update
    ```

## <a name="next-steps"></a>Następne kroki

Jeśli masz problemy z połączeniem maszyny Wirtualnej z domeną zarządzaną usługą Azure AD DS lub zalogowaniem się za pomocą konta domeny, zobacz Rozwiązywanie problemów z [dołączaniem do domeny](join-windows-vm.md#troubleshoot-domain-join-issues).

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
