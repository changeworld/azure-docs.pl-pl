---
title: 'Azure Active Directory Domain Services: Dołączanie do maszyny wirtualnej z systemem CoreOS Linux | Microsoft Docs'
description: Przyłączanie maszyny wirtualnej z systemem CoreOS Linux do Azure AD Domain Services
services: active-directory-ds
documentationcenter: ''
author: iainfoulds
manager: daveba
editor: curtand
ms.assetid: 5db65f30-bf69-4ea3-9ea5-add1db83fdb8
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/20/2019
ms.author: iainfou
ms.openlocfilehash: dc76d9a0d492d8ef0e37c0c34173216ff4c75164
ms.sourcegitcommit: 007ee4ac1c64810632754d9db2277663a138f9c4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/23/2019
ms.locfileid: "69990569"
---
# <a name="join-a-coreos-linux-virtual-machine-to-a-managed-domain"></a>Przyłączanie maszyny wirtualnej z systemem CoreOS Linux do domeny zarządzanej
W tym artykule opisano sposób przyłączania maszyny wirtualnej z systemem CoreOS Linux na platformie Azure do domeny zarządzanej Azure AD Domain Services.

[!INCLUDE [active-directory-ds-prerequisites.md](../../includes/active-directory-ds-prerequisites.md)]

## <a name="before-you-begin"></a>Przed rozpoczęciem
Aby wykonać zadania wymienione w tym artykule, potrzebne są:
1. Prawidłowa **subskrypcja platformy Azure**.
2. **Katalog usługi Azure AD** — zsynchronizowany z katalogiem lokalnym lub katalogiem w chmurze.
3. Należy włączyć **Azure AD Domain Services** dla katalogu usługi Azure AD. Jeśli nie zostało to zrobione, postępuj zgodnie ze wszystkimi zadaniami opisanymi w [przewodniku wprowadzenie](tutorial-create-instance.md).
4. Upewnij się, że adresy IP domeny zarządzanej zostały skonfigurowane jako serwery DNS dla sieci wirtualnej. Aby uzyskać więcej informacji, zobacz [jak zaktualizować ustawienia DNS dla sieci wirtualnej platformy Azure](tutorial-create-instance.md#update-dns-settings-for-the-azure-virtual-network)
5. Wykonaj kroki wymagane do [synchronizacji haseł do domeny zarządzanej Azure AD Domain Services](tutorial-create-instance.md#enable-user-accounts-for-azure-ad-ds).


## <a name="provision-a-coreos-linux-virtual-machine"></a>Inicjowanie obsługi administracyjnej maszyny wirtualnej z systemem CoreOS Linux
Inicjowanie obsługi administracyjnej maszyny wirtualnej CoreOS na platformie Azure przy użyciu dowolnej z następujących metod:
* [Azure Portal](../virtual-machines/linux/quick-create-portal.md)
* [Interfejs wiersza polecenia platformy Azure](../virtual-machines/linux/quick-create-cli.md)
* [Azure PowerShell](../virtual-machines/linux/quick-create-powershell.md)

W tym artykule jest stosowany obraz maszyny wirtualnej z **systemem CoreOS Linux (stabilny)** na platformie Azure.

> [!IMPORTANT]
> * Wdróż maszynę wirtualną w tej **samej sieci wirtualnej, w której włączono Azure AD Domain Services**.
> * Wybierz **inną podsieć** niż ta, w której włączono Azure AD Domain Services.
>


## <a name="connect-remotely-to-the-newly-provisioned-linux-virtual-machine"></a>Zdalne nawiązywanie połączenia z nowo zainicjowaną maszyną wirtualną z systemem Linux
Maszyna wirtualna CoreOS została zainicjowana na platformie Azure. Następnym zadaniem jest zdalne nawiązywanie połączenia z maszyną wirtualną przy użyciu konta administratora lokalnego utworzonego podczas aprowizacji maszyny wirtualnej.

Postępuj zgodnie z instrukcjami w artykule [Jak zalogować się do maszyny wirtualnej z systemem Linux](../virtual-machines/linux/mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).


## <a name="configure-the-hosts-file-on-the-linux-virtual-machine"></a>Konfigurowanie pliku Hosts na maszynie wirtualnej z systemem Linux
W terminalu SSH Edytuj plik/etc/hosts i zaktualizuj adres IP i nazwę hosta maszyny.

```console
sudo vi /etc/hosts
```

W pliku hosts Wprowadź następującą wartość:

```console
127.0.0.1 contoso-coreos.contoso.com contoso-coreos
```

W tym miejscu "contoso.com" jest nazwą domeny DNS domeny zarządzanej. "contoso-CoreOS" jest nazwą hosta maszyny wirtualnej CoreOS, do której jest przyłączany do domeny zarządzanej.


## <a name="configure-the-sssd-service-on-the-linux-virtual-machine"></a>Konfigurowanie usługi SSSD na maszynie wirtualnej z systemem Linux
Następnie zaktualizuj plik konfiguracji SSSD w programie ("/etc/SSSD/SSSD.conf"), aby pasował do poniższego przykładu:

```console
[sssd]
config_file_version = 2
services = nss, pam
domains = CONTOSO.COM

[domain/CONTOSO.COM]
id_provider = ad
auth_provider = ad
chpass_provider = ad

ldap_uri = ldap://contoso.com
ldap_search_base = dc=contoso,dc=com
ldap_schema = rfc2307bis
ldap_sasl_mech = GSSAPI
ldap_user_object_class = user
ldap_group_object_class = group
ldap_user_home_directory = unixHomeDirectory
ldap_user_principal = userPrincipalName
ldap_account_expire_policy = ad
ldap_force_upper_case_realm = true
fallback_homedir = /home/%d/%u

krb5_server = contoso.com
krb5_realm = CONTOSO.COM
```

Zamień element "CONTOSO. COM ' z nazwą domeny DNS domeny zarządzanej. Upewnij się, że w pliku conf określono nazwę domeny w przypadku wielkości liter.


## <a name="join-the-linux-virtual-machine-to-the-managed-domain"></a>Przyłączanie maszyny wirtualnej z systemem Linux do domeny zarządzanej
Teraz, gdy wymagane pakiety są zainstalowane na maszynie wirtualnej z systemem Linux, następne zadanie polega na przyłączeniu maszyny wirtualnej do domeny zarządzanej.

```console
sudo adcli join -D CONTOSO.COM -U bob@CONTOSO.COM -K /etc/krb5.keytab -H contoso-coreos.contoso.com -N coreos
```


> [!NOTE]
> **Rozwiązywanie problemów:** Jeśli *adcli* nie może znaleźć Twojej domeny zarządzanej:
>   * Upewnij się, że domena jest dostępna z poziomu maszyny wirtualnej (Wypróbuj polecenie ping).
>   * Sprawdź, czy maszyna wirtualna rzeczywiście została wdrożona w tej samej sieci wirtualnej, w której jest dostępna domena zarządzana.
>   * Sprawdź, czy zostały zaktualizowane ustawienia serwera DNS dla sieci wirtualnej, aby wskazywały kontrolery domeny zarządzanej domeny.

Uruchom usługę SSSD. W terminalu SSH wpisz następujące polecenie:
  
```console
sudo systemctl start sssd.service
```


## <a name="verify-domain-join"></a>Weryfikuj przyłączanie do domeny
Sprawdź, czy maszyna została pomyślnie przyłączona do domeny zarządzanej. Nawiąż połączenie z dołączoną do domeny maszyną wirtualną CoreOS przy użyciu innego połączenia SSH. Użyj konta użytkownika domeny, a następnie sprawdź, czy konto użytkownika jest prawidłowo rozwiązane.

1. W terminalu SSH wpisz następujące polecenie, aby nawiązać połączenie z przyłączoną do domeny maszyną wirtualną CoreOS przy użyciu protokołu SSH. Użyj konta domeny, które należy do domeny zarządzanej (na przykład "bob@CONTOSO.COM" w tym przypadku).
    
    ```console
    ssh -l bob@CONTOSO.COM contoso-coreos.contoso.com
    ```

2. W terminalu SSH wpisz następujące polecenie, aby sprawdzić, czy katalog macierzysty został zainicjowany prawidłowo.
    
    ```console
    pwd
    ```

3. W terminalu SSH wpisz następujące polecenie, aby sprawdzić, czy członkostwa w grupach są poprawnie rozwiązane.
   
    ```console
    id
    ```


## <a name="troubleshooting-domain-join"></a>Rozwiązywanie problemów z przyłączaniem domeny
Zobacz artykuł [Rozwiązywanie problemów](join-windows-vm.md#troubleshoot-domain-join-issues) z przyłączaniem do domeny.

## <a name="related-content"></a>Powiązana zawartość
* [Przewodnik po Wprowadzenie Azure AD Domain Services](tutorial-create-instance.md)
* [Przyłączanie maszyny wirtualnej z systemem Windows Server do domeny zarządzanej Azure AD Domain Services](active-directory-ds-admin-guide-join-windows-vm.md)
* [Jak zalogować się do maszyny wirtualnej z systemem Linux](../virtual-machines/linux/mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
