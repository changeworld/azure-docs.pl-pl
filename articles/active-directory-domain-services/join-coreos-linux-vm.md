---
title: Dołączanie maszyny Wirtualnej CoreOS do usług domenowych usługi Azure AD | Dokumenty firmy Microsoft
description: Dowiedz się, jak skonfigurować maszynę wirtualną CoreOS i dołączyć do niej do domeny zarządzanej usług domenowych usługi AZURE AD.
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: 5db65f30-bf69-4ea3-9ea5-add1db83fdb8
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 01/23/2020
ms.author: iainfou
ms.openlocfilehash: 63dfe39b986125abc9cacf6c1a6556876bbd3a99
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/03/2020
ms.locfileid: "80655194"
---
# <a name="join-a-coreos-virtual-machine-to-an-azure-ad-domain-services-managed-domain"></a>Dołączanie maszyny wirtualnej CoreOS do domeny zarządzanej usług domenowych usługi azure AD

Aby umożliwić użytkownikom logowanie się do maszyn wirtualnych (VM) na platformie Azure przy użyciu jednego zestawu poświadczeń, można dołączyć maszyny wirtualne do domeny zarządzanej usług domenowych Active Directory (AD DS). Po dołączeniu maszyny Wirtualnej do domeny zarządzanej usług Azure AD DS konta użytkowników i poświadczenia z domeny mogą służyć do logowania się i zarządzania serwerami. Członkostwa w grupach z domeny zarządzanej usług Azure AD DS są również stosowane w celu umożliwienia kontrolowania dostępu do plików lub usług na maszynie Wirtualnej.

W tym artykule pokazano, jak dołączyć do maszyny Wirtualnej CoreOS do domeny zarządzanej usług Azure AD DS.

## <a name="prerequisites"></a>Wymagania wstępne

Aby ukończyć ten samouczek, potrzebne są następujące zasoby i uprawnienia:

* Aktywna subskrypcja platformy Azure.
    * Jeśli nie masz subskrypcji platformy Azure, [utwórz konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Dzierżawa usługi Azure Active Directory skojarzona z subskrypcją, zsynchronizowana z katalogiem lokalnym lub katalogiem tylko w chmurze.
    * W razie potrzeby [utwórz dzierżawę usługi Azure Active Directory][create-azure-ad-tenant] lub [skojarz subskrypcję platformy Azure z kontem.][associate-azure-ad-tenant]
* Domena zarządzana usługami domenowymi Usługi Active Directory platformy Azure włączona i skonfigurowana w dzierżawie usługi Azure AD.
    * W razie potrzeby pierwszy samouczek [tworzy i konfiguruje wystąpienie usług domenowych Usługi domenowe Active Directory][create-azure-ad-ds-instance]platformy Azure .
* Konto użytkownika, które jest częścią domeny zarządzanej usług Azure AD DS.

## <a name="create-and-connect-to-a-coreos-linux-vm"></a>Tworzenie maszyny wirtualnej CoreOS z systemem Linux i łączenie się z nią

Jeśli masz istniejącą maszynę wirtualną CoreOS z systemem Linux na platformie Azure, połącz się z nią za pomocą SSH, a następnie przejdź do następnego kroku, aby [rozpocząć konfigurowanie maszyny Wirtualnej](#configure-the-hosts-file).

Jeśli chcesz utworzyć maszynę wirtualną CoreOS z systemem Linux lub chcesz utworzyć testową maszynę wirtualną do użycia w tym artykule, możesz użyć jednej z następujących metod:

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
* *coreos* to nazwa hosta maszyny Wirtualnej CoreOS, do której dołączasz do domeny zarządzanej.

Zaktualizuj te nazwy za pomocą własnych wartości:

```console
127.0.0.1 coreos coreos.aaddscontoso.com
```

Po zakończeniu zapisz i *hosts* zamknij plik `:wq` hosts za pomocą polecenia edytora.

## <a name="configure-the-sssd-service"></a>Konfigurowanie usługi SSSD

Zaktualizuj konfigurację */etc/sssd/sssd.conf* SSSD.

```console
sudo vi /etc/sssd/sssd.conf
```

Określ własną nazwę domeny zarządzanej usług Azure AD DS dla następujących parametrów:

* *domen w* ALL UPPER CASE
* *[domena/AADDS],* gdzie AADDS jest w wielkim wielkim
* *ldap_uri*
* *ldap_search_base*
* *krb5_server*
* *krb5_realm* we wszystkich wielkich literach

```console
[sssd]
config_file_version = 2
services = nss, pam
domains = AADDSCONTOSO.COM

[domain/AADDSCONTOSO.COM]
id_provider = ad
auth_provider = ad
chpass_provider = ad

ldap_uri = ldap://aaddscontoso.com
ldap_search_base = dc=aaddscontoso,dc=com
ldap_schema = rfc2307bis
ldap_sasl_mech = GSSAPI
ldap_user_object_class = user
ldap_group_object_class = group
ldap_user_home_directory = unixHomeDirectory
ldap_user_principal = userPrincipalName
ldap_account_expire_policy = ad
ldap_force_upper_case_realm = true
fallback_homedir = /home/%d/%u

krb5_server = aaddscontoso.com
krb5_realm = AADDSCONTOSO.COM
```

## <a name="join-the-vm-to-the-managed-domain"></a>Dołączanie maszyny Wirtualnej do domeny zarządzanej

Po zaktualizowaniu pliku konfiguracyjnego SSSD dołącz teraz do maszyny wirtualnej do domeny zarządzanej.

1. Najpierw użyj `adcli info` polecenia, aby sprawdzić, czy można wyświetlić informacje o domenie zarządzanej usług Azure AD DS. Poniższy przykład pobiera informacje o *AADDSCONTOSO.COM*domeny . Określ własną nazwę domeny zarządzanej usług Azure AD DS w all uppercase:

    ```console
    sudo adcli info AADDSCONTOSO.COM
    ```

   Jeśli `adcli info` polecenie nie może znaleźć domeny zarządzanej usług Azure AD DS, zapoznaj się z następującymi krokami rozwiązywania problemów:

    * Upewnij się, że domena jest osiągalna z maszyny Wirtualnej. Spróbuj `ping aaddscontoso.com` sprawdzić, czy odpowiedź pozytywna jest zwracana.
    * Sprawdź, czy maszyna wirtualna jest wdrażana w tej samej lub równorzędnej sieci wirtualnej, w której jest dostępna domena zarządzana usługą Azure AD DS.
    * Upewnij się, że ustawienia serwera DNS dla sieci wirtualnej zostały zaktualizowane w celu wskazania kontrolerów domeny domeny zarządzanej usług Azure AD DS.

1. Teraz dołącz do maszyny Wirtualnej do domeny `adcli join` zarządzanej usług Azure AD DS za pomocą polecenia. Określ użytkownika, który jest częścią domeny zarządzanej usług Azure AD DS. W razie potrzeby [dodaj konto użytkownika do grupy w usłudze Azure AD](../active-directory/fundamentals/active-directory-groups-members-azure-portal.md).

    Ponownie nazwa domeny domeny zarządzanej usług Azure AD DS musi zostać wprowadzona w all uppercase. W poniższym przykładzie `contosoadmin@aaddscontoso.com` konto o nazwie jest używane do inicjowania protokołu Kerberos. Wprowadź własne konto użytkownika, które jest częścią domeny zarządzanej usług Azure AD DS.

    ```console
    sudo adcli join -D AADDSCONTOSO.COM -U contosoadmin@AADDSCONTOSO.COM -K /etc/krb5.keytab -H coreos.aaddscontoso.com -N coreos
    ```

    Polecenie `adcli join` nie zwraca żadnych informacji, gdy maszyna wirtualna pomyślnie dołączyła do domeny zarządzanej usług Azure AD DS.

1. Aby zastosować konfigurację sprzężenia domeny, uruchom usługę SSSD:
  
    ```console
    sudo systemctl start sssd.service
    ```

## <a name="sign-in-to-the-vm-using-a-domain-account"></a>Logowanie się do maszyny Wirtualnej przy użyciu konta domeny

Aby sprawdzić, czy maszyna wirtualna została pomyślnie przyłączona do domeny zarządzanej usług Azure AD DS, uruchom nowe połączenie SSH przy użyciu konta użytkownika domeny. Upewnij się, że utworzono katalog macierzysty i że członkostwo w grupie z domeny jest stosowane.

1. Utwórz nowe połączenie SSH z konsoli. Użyj konta domeny należącego do domeny `ssh -l` zarządzanej za `contosoadmin@aaddscontoso.com` pomocą polecenia, na przykład, a następnie wprowadź adres maszyny Wirtualnej, na przykład *coreos.aaddscontoso.com*. Jeśli używasz usługi Azure Cloud Shell, użyj publicznego adresu IP maszyny Wirtualnej, a nie wewnętrznej nazwy DNS.

    ```console
    ssh -l contosoadmin@AADDSCONTOSO.com coreos.aaddscontoso.com
    ```

1. Teraz sprawdź, czy członkostwo w grupach jest poprawnie rozwiązywane:

    ```console
    id
    ```

    Powinny być widoczne członkostwa w grupach z domeny zarządzanej usług Azure AD DS.

## <a name="next-steps"></a>Następne kroki

Jeśli masz problemy z połączeniem maszyny Wirtualnej z domeną zarządzaną usługą Azure AD DS lub zalogowaniem się za pomocą konta domeny, zobacz Rozwiązywanie problemów z [dołączaniem do domeny](join-windows-vm.md#troubleshoot-domain-join-issues).

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
