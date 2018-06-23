---
title: 'Azure Active Directory Domain Services: Dołącz Maszynę wirtualną systemu Linux CoreOS do domeny zarządzanej | Dokumentacja firmy Microsoft'
description: Dołącz maszynę wirtualną systemu CoreOS Linux do usług domenowych Azure AD
services: active-directory-ds
documentationcenter: ''
author: mahesh-unnikrishnan
manager: mtillman
editor: curtand
ms.assetid: 5db65f30-bf69-4ea3-9ea5-add1db83fdb8
ms.service: active-directory
ms.component: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/22/2018
ms.author: maheshu
ms.openlocfilehash: bb94d0c817cf1a15c90ac5e928406e5f5e59a068
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/23/2018
ms.locfileid: "36332808"
---
# <a name="join-a-coreos-linux-virtual-machine-to-a-managed-domain"></a>Dołącz maszynę wirtualną systemu CoreOS Linux do domeny zarządzanej
W tym artykule przedstawiono sposób przyłączyć maszyny wirtualnej systemu CoreOS Linux na platformie Azure do domeny zarządzanej usług domenowych Azure AD.

[!INCLUDE [active-directory-ds-prerequisites.md](../../includes/active-directory-ds-prerequisites.md)]

## <a name="before-you-begin"></a>Przed rozpoczęciem
Aby wykonać zadania opisane w tym artykule, należy:
1. Prawidłowy **subskrypcji platformy Azure**.
2. **Katalog usługi Azure AD** -albo synchronizowane z katalogu lokalnego lub w katalogu tylko w chmurze.
3. **Usługi domenowe Azure AD** musi być włączona dla katalogu usługi Azure AD. Jeśli nie zostało to jeszcze zrobione, należy wykonać wszystkie zadania opisane w temacie [Przewodnik wprowadzający](active-directory-ds-getting-started.md).
4. Upewnij się, że skonfigurowano adresy IP domeny zarządzanej jako serwery DNS dla sieci wirtualnej. Aby uzyskać więcej informacji, zobacz [jak zaktualizować ustawienia DNS dla sieci wirtualnej platformy Azure](active-directory-ds-getting-started-dns.md)
5. Wykonaj kroki wymagane do [synchronizacji haseł do domeny zarządzanej usług domenowych Azure AD](active-directory-ds-getting-started-password-sync.md).


## <a name="provision-a-coreos-linux-virtual-machine"></a>Aprowizowanie maszyny wirtualnej systemu CoreOS Linux
Zapewnij CoreOS maszyny wirtualnej na platformie Azure przy użyciu dowolnej z następujących metod:
* [Azure Portal](../virtual-machines/linux/quick-create-portal.md)
* [Interfejs wiersza polecenia platformy Azure](../virtual-machines/linux/quick-create-cli.md)
* [Azure PowerShell](../virtual-machines/linux/quick-create-powershell.md)

W tym artykule wykorzystano **CoreOS systemu Linux (stały)** obraz maszyny wirtualnej na platformie Azure.

> [!IMPORTANT]
> * Wdróż maszynę wirtualną do **tej samej sieci wirtualnej, w której włączono usługi domenowe Azure AD**.
> * Wybierz **innej podsieci** niż ten, w której włączono usługi domenowe Azure AD.
>


## <a name="connect-remotely-to-the-newly-provisioned-linux-virtual-machine"></a>Zdalne nawiązywanie połączenia nowo aprowizowanej maszyny wirtualnej systemu Linux
Maszyna wirtualna CoreOS zainicjowano na platformie Azure. Następnym zadaniem jest nawiązywanie połączeń zdalnych z maszyną wirtualną przy użyciu konta administratora lokalnego utworzone podczas inicjowania obsługi administracyjnej maszyny Wirtualnej.

Postępuj zgodnie z instrukcjami w artykule [jak zalogować się do maszyny wirtualnej z systemem Linux](../virtual-machines/linux/mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).


## <a name="configure-the-hosts-file-on-the-linux-virtual-machine"></a>Konfigurowanie pliku hostów na maszynie wirtualnej systemu Linux
W terminalu SSH Edytuj plik/etc/hosts i zaktualizować adres IP komputera i nazwy hosta.

```
sudo vi /etc/hosts
```

W pliku hosts wprowadź następujące wartości:

```
127.0.0.1 contoso-coreos.contoso100.com contoso-coreos
```
W tym miejscu "contoso100.com" jest nazwa domeny DNS domeny zarządzanej. "contoso-coreos" jest nazwą hosta maszyny wirtualnej CoreOS, które są przyłączania do domeny zarządzanej.


## <a name="configure-the-sssd-service-on-the-linux-virtual-machine"></a>Skonfiguruj usługę SSSD na maszynie wirtualnej systemu Linux
Następnie zaktualizuj w pliku konfiguracji SSSD ("/ etc/sssd/sssd.conf") do dopasowania w poniższym przykładzie:

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
Zastąp "CONTOSO100. COM' z nazwą domeny DNS domeny zarządzanej. Upewnij się, że w przypadku kapitałowych w pliku conf należy określić nazwę domeny.


## <a name="join-the-linux-virtual-machine-to-the-managed-domain"></a>Dołącz maszynę wirtualną systemu Linux do domeny zarządzanej
Teraz, wymagane pakiety są zainstalowane na maszynie wirtualnej systemu Linux, następne zadanie to można dołączyć maszyny wirtualnej do domeny zarządzanej.

```
sudo adcli join -D CONTOSO100.COM -U bob@CONTOSO100.COM -K /etc/krb5.keytab -H contoso-coreos.contoso100.com -N coreos
```


> [!NOTE]
> **Rozwiązywanie problemów:** Jeśli *adcli* nie może znaleźć domeny zarządzanej:
  * Upewnij się, że domena jest osiągalna z poziomu maszyny wirtualnej (ping spróbuj).
  * Sprawdź maszyny wirtualnej w rzeczywistości został wdrożony do tej samej sieci wirtualnej, w których są dostępne domeny zarządzanej.
  * Sprawdź, czy ustawienia serwera DNS dla sieci wirtualnej, aby wskazywał kontrolerów domeny zarządzanej zostały zaktualizowane.
>

Uruchom usługę SSSD. W terminalu SSH wpisz następujące polecenie:
  ```
  sudo systemctl start sssd.service
  ```


## <a name="verify-domain-join"></a>Sprawdź, przyłączanie do domeny
Sprawdź, czy maszyna została pomyślnie dołączona do domeny zarządzanej. Połączyć się z CoreOS maszyny Wirtualnej przy użyciu innego połączenia SSH przyłączonych do domeny. Przy użyciu konta użytkownika domeny, a następnie sprawdź, czy konto użytkownika jest poprawnie rozpoznać.

1. Twoje końcowych SSH wpisz następujące polecenie, aby połączyć się z domeną przyłączony CoreOS maszyny wirtualnej przy użyciu protokołu SSH. Użyj konta domeny, który należy do domeny zarządzanej (na przykład "bob@CONTOSO100.COM" w tym przypadku.)
    ```
    ssh -l bob@CONTOSO100.COM contoso-coreos.contoso100.com
    ```

2. W terminalu SSH wpisz następujące polecenie, aby zobaczyć, czy katalog macierzysty został poprawnie zainicjowany.
    ```
    pwd
    ```

3. W terminalu SSH wpisz następujące polecenie, aby zobaczyć, czy członkostwa w grupach są rozpoznawane poprawnie.
    ```
    id
    ```


## <a name="troubleshooting-domain-join"></a>Rozwiązywanie problemów z przyłączania do domeny
Zapoznaj się [przyłączenie do domeny Rozwiązywanie problemów](active-directory-ds-admin-guide-join-windows-vm-portal.md#troubleshoot-joining-a-domain) artykułu.

## <a name="related-content"></a>Powiązana zawartość
* [Usługi domenowe AD Azure - Przewodnik wprowadzający](active-directory-ds-getting-started.md)
* [Dołącz maszynę wirtualną systemu Windows Server do domeny zarządzanej usług domenowych Azure AD](active-directory-ds-admin-guide-join-windows-vm.md)
* [Jak zalogować się do maszyny wirtualnej z systemem Linux](../virtual-machines/linux/mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
