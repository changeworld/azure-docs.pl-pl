---
title: 'Azure Active Directory Domain Services: Dołącz Maszynę wirtualną CoreOS systemu Linux do domeny zarządzanej | Dokumentacja firmy Microsoft'
description: Dołączanie maszyny wirtualnej systemu CoreOS Linux do usługi Azure AD Domain Services
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
ms.openlocfilehash: 93d8279c07c936d7e5ce2c7e756baadfbe4a1b0a
ms.sourcegitcommit: f811238c0d732deb1f0892fe7a20a26c993bc4fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/29/2019
ms.locfileid: "67473305"
---
# <a name="join-a-coreos-linux-virtual-machine-to-a-managed-domain"></a>Dołącz maszynę wirtualną CoreOS Linux do domeny zarządzanej
W tym artykule pokazano, jak dołączanie maszyny wirtualnej systemu Linux systemu CoreOS na platformie Azure do domeny zarządzanej usług domenowych Azure AD.

[!INCLUDE [active-directory-ds-prerequisites.md](../../includes/active-directory-ds-prerequisites.md)]

## <a name="before-you-begin"></a>Przed rozpoczęciem
Aby wykonać zadania opisane w tym artykule, potrzebne są:
1. Nieprawidłowy **subskrypcji platformy Azure**.
2. **Katalog usługi Azure AD** — albo synchronizowane z katalogu lokalnego lub w katalogu tylko w chmurze.
3. **Usługi domenowe Azure AD** musi być włączona dla katalogu usługi Azure AD. Jeśli nie zostało to zrobione, wykonaj wszystkie zadania, które są opisane w temacie [Wprowadzenie — przewodnik](create-instance.md).
4. Upewnij się, że zostały skonfigurowane adresy IP w domenie zarządzanej jako serwery DNS dla sieci wirtualnej. Aby uzyskać więcej informacji, zobacz [jak zaktualizować ustawienia DNS dla sieci wirtualnej platformy Azure](active-directory-ds-getting-started-dns.md)
5. Wykonaj kroki wymagane do [synchronizacji haseł do domeny zarządzanej usług domenowych Azure AD](active-directory-ds-getting-started-password-sync.md).


## <a name="provision-a-coreos-linux-virtual-machine"></a>Aprowizowanie maszyny wirtualnej systemu Linux systemu CoreOS
Aprowizowanie maszyny wirtualnej systemu CoreOS na platformie Azure przy użyciu dowolnej z następujących metod:
* [Azure Portal](../virtual-machines/linux/quick-create-portal.md)
* [Interfejs wiersza polecenia platformy Azure](../virtual-machines/linux/quick-create-cli.md)
* [Azure PowerShell](../virtual-machines/linux/quick-create-powershell.md)

W tym artykule wykorzystano **CoreOS Linux (wersja stabilna)** obrazu maszyny wirtualnej na platformie Azure.

> [!IMPORTANT]
> * Wdróż maszynę wirtualną do **tej samej sieci wirtualnej, w której włączono usługi domenowe Azure AD**.
> * Wybierz **innej podsieci** niż ta, w której włączono usługi domenowe Azure AD.
>


## <a name="connect-remotely-to-the-newly-provisioned-linux-virtual-machine"></a>Połącz się zdalnie nowo aprowizowanej maszyny wirtualnej systemu Linux
Został aprowizowany maszyny wirtualnej systemu CoreOS na platformie Azure. Kolejnym krokiem jest zdalne ustanawianie połączenia z maszyną wirtualną przy użyciu konta administratora lokalnego, utworzony podczas aprowizowania maszyny Wirtualnej.

Postępuj zgodnie z instrukcjami w artykule [jak zalogować się do maszyny wirtualnej z systemem Linux](../virtual-machines/linux/mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).


## <a name="configure-the-hosts-file-on-the-linux-virtual-machine"></a>Konfigurowanie pliku hosts na maszynie wirtualnej systemu Linux
W terminalu SSH Edytuj plik/etc/hosts i zaktualizować adres IP komputera i nazwa hosta.

```
sudo vi /etc/hosts
```

W pliku hosts wprowadź następującą wartość:

```
127.0.0.1 contoso-coreos.contoso100.com contoso-coreos
```
W tym miejscu "contoso100.com" jest nazwa domeny DNS z domeny zarządzanej. "contoso-coreos" jest nazwą hosta maszyny wirtualnej systemu CoreOS, z którą następuje przyłączanie do domeny zarządzanej.


## <a name="configure-the-sssd-service-on-the-linux-virtual-machine"></a>Skonfiguruj usługę SSSD na maszynie wirtualnej systemu Linux
Następnie zaktualizuj plik konfiguracji SSSD w ("/ etc/sssd/sssd.conf"), aby dopasować następujący przykład:

 ```
 [sssd]
 config_file_version = 2
 services = nss, pam
 domains = CONTOSO100.COM

 [domain/CONTOSO100.COM]
 id_provider = ad
 auth_provider = ad
 chpass_provider = ad

 ldap_uri = ldap://contoso100.com
 ldap_search_base = dc=contoso100,dc=com
 ldap_schema = rfc2307bis
 ldap_sasl_mech = GSSAPI
 ldap_user_object_class = user
 ldap_group_object_class = group
 ldap_user_home_directory = unixHomeDirectory
 ldap_user_principal = userPrincipalName
 ldap_account_expire_policy = ad
 ldap_force_upper_case_realm = true
 fallback_homedir = /home/%d/%u

 krb5_server = contoso100.com
 krb5_realm = CONTOSO100.COM
 ```
Zastąp "CONTOSO100. COM "z nazwą domeny DNS z domeny zarządzanej. Upewnij się, że należy określić nazwę domeny w przypadku kapitałowych w pliku potwierdzenie.


## <a name="join-the-linux-virtual-machine-to-the-managed-domain"></a>Dołączanie maszyny wirtualnej systemu Linux do domeny zarządzanej
Teraz, wymagane pakiety są zainstalowane na maszynie wirtualnej systemu Linux, kolejnym krokiem jest można dołączyć maszyny wirtualnej do domeny zarządzanej.

```
sudo adcli join -D CONTOSO100.COM -U bob@CONTOSO100.COM -K /etc/krb5.keytab -H contoso-coreos.contoso100.com -N coreos
```


> [!NOTE]
> **Rozwiązywanie problemów:** Jeśli *adcli* nie może znaleźć Twojej domeny zarządzanej:
>   * Upewnij się, że domena jest dostępny z maszyny wirtualnej (spróbuj ping).
>   * Sprawdź, czy maszyna wirtualna w rzeczywistości został wdrożony na tej samej sieci wirtualnej, w którym domena zarządzana jest dostępna.
>   * Sprawdź, czy zostały zaktualizowane ustawienia serwera DNS dla sieci wirtualnej, aby wskazywała na kontrolerach domeny z domeny zarządzanej.

Uruchom usługę SSSD. W terminalu SSH wpisz następujące polecenie:
  ```
  sudo systemctl start sssd.service
  ```


## <a name="verify-domain-join"></a>Sprawdź, przyłączanie do domeny
Sprawdź, czy komputer został pomyślnie dołączony do domeny zarządzanej. Połączyć się z częścią domeny przy użyciu innego połączenia SSH maszyny Wirtualnej systemu CoreOS. Użyj konta użytkownika domeny, a następnie sprawdź, czy konto użytkownika jest poprawnie rozwiązany.

1. Twoje terminala protokołu SSH wpisz następujące polecenie, aby połączyć się z domeną przyłączony maszynę wirtualną CoreOS przy użyciu protokołu SSH. Użyj konta domeny, który należy do domeny zarządzanej (na przykład "bob@CONTOSO100.COM" w tym przypadku.)
    ```
    ssh -l bob@CONTOSO100.COM contoso-coreos.contoso100.com
    ```

2. W terminalu SSH wpisz następujące polecenie, aby zobaczyć, jeśli katalog macierzysty został poprawnie zainicjowany.
    ```
    pwd
    ```

3. W terminalu SSH wpisz następujące polecenie, aby zobaczyć, jeśli w grupach są rozpoznawane poprawnie.
    ```
    id
    ```


## <a name="troubleshooting-domain-join"></a>Rozwiązywanie problemów z przyłączania do domeny
Zapoznaj się [przyłączania do domeny Rozwiązywanie problemów](join-windows-vm.md#troubleshoot-joining-a-domain) artykułu.

## <a name="related-content"></a>Powiązana zawartość
* [Azure AD Domain Services — wprowadzenie — przewodnik](create-instance.md)
* [Dołączanie maszyny wirtualnej systemu Windows Server do domeny zarządzanej usług domenowych Azure AD](active-directory-ds-admin-guide-join-windows-vm.md)
* [Jak zalogować się do maszyny wirtualnej z systemem Linux](../virtual-machines/linux/mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
