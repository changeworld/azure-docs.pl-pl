---
title: 'Azure Active Directory Domain Services: Dołącz maszyny Wirtualnej CentOS do domeny zarządzanej | Dokumentacja firmy Microsoft'
description: Dołącz maszynę wirtualną systemu CentOS Linux do usług domenowych Azure AD
services: active-directory-ds
documentationcenter: ''
author: mahesh-unnikrishnan
manager: mtillman
editor: curtand
ms.assetid: 16100caa-f209-4cb0-86d3-9e218aeb51c6
ms.service: active-directory
ms.component: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/22/2018
ms.author: maheshu
ms.openlocfilehash: d76371935fddbfe94c6dc45e27971487e7fa4277
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/23/2018
ms.locfileid: "36333585"
---
# <a name="join-a-centos-linux-virtual-machine-to-a-managed-domain"></a>Dołącz do maszyny wirtualnej CentOS Linux do domeny zarządzanej
W tym artykule przedstawiono sposób przyłączyć maszyny wirtualnej CentOS Linux na platformie Azure do domeny zarządzanej usług domenowych Azure AD.

[!INCLUDE [active-directory-ds-prerequisites.md](../../includes/active-directory-ds-prerequisites.md)]

## <a name="before-you-begin"></a>Przed rozpoczęciem
Aby wykonać zadania opisane w tym artykule, należy:
1. Prawidłowy **subskrypcji platformy Azure**.
2. **Katalog usługi Azure AD** -albo synchronizowane z katalogu lokalnego lub w katalogu tylko w chmurze.
3. **Usługi domenowe Azure AD** musi być włączona dla katalogu usługi Azure AD. Jeśli nie zostało to jeszcze zrobione, należy wykonać wszystkie zadania opisane w temacie [Przewodnik wprowadzający](active-directory-ds-getting-started.md).
4. Upewnij się, że skonfigurowano adresy IP domeny zarządzanej jako serwery DNS dla sieci wirtualnej. Aby uzyskać więcej informacji, zobacz [jak zaktualizować ustawienia DNS dla sieci wirtualnej platformy Azure](active-directory-ds-getting-started-dns.md)
5. Wykonaj kroki wymagane do [synchronizacji haseł do domeny zarządzanej usług domenowych Azure AD](active-directory-ds-getting-started-password-sync.md).


## <a name="provision-a-centos-linux-virtual-machine"></a>Aprowizowanie maszyny wirtualnej CentOS Linux
Zapewnij maszyny wirtualnej CentOS na platformie Azure przy użyciu dowolnej z następujących metod:
* [Azure Portal](../virtual-machines/linux/quick-create-portal.md)
* [Interfejs wiersza polecenia platformy Azure](../virtual-machines/linux/quick-create-cli.md)
* [Azure PowerShell](../virtual-machines/linux/quick-create-powershell.md)

> [!IMPORTANT]
> * Wdróż maszynę wirtualną do **tej samej sieci wirtualnej, w której włączono usługi domenowe Azure AD**.
> * Wybierz **innej podsieci** niż ten, w której włączono usługi domenowe Azure AD.
>


## <a name="connect-remotely-to-the-newly-provisioned-linux-virtual-machine"></a>Zdalne nawiązywanie połączenia nowo aprowizowanej maszyny wirtualnej systemu Linux
Maszyny wirtualnej CentOS zainicjowano na platformie Azure. Następnym zadaniem jest nawiązywanie połączeń zdalnych z maszyną wirtualną przy użyciu konta administratora lokalnego utworzone podczas inicjowania obsługi administracyjnej maszyny Wirtualnej.

Postępuj zgodnie z instrukcjami w artykule [jak zalogować się do maszyny wirtualnej z systemem Linux](../virtual-machines/linux/mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).


## <a name="configure-the-hosts-file-on-the-linux-virtual-machine"></a>Konfigurowanie pliku hostów na maszynie wirtualnej systemu Linux
W terminalu SSH Edytuj plik/etc/hosts i zaktualizować adres IP komputera i nazwy hosta.

```
sudo vi /etc/hosts
```

W pliku hosts wprowadź następujące wartości:

```
127.0.0.1 contoso-centos.contoso100.com contoso-centos
```
W tym miejscu "contoso100.com" jest nazwa domeny DNS domeny zarządzanej. "contoso-centos" jest nazwą hosta maszyny wirtualnej CentOS, które są przyłączania do domeny zarządzanej.


## <a name="install-required-packages-on-the-linux-virtual-machine"></a>Zainstaluj wymagane pakiety na maszynie wirtualnej systemu Linux
Następnie zainstaluj pakiety wymagane do przyłączania do domeny na maszynie wirtualnej. W terminalu SSH wpisz następujące polecenie, aby zainstalować wymagane pakiety:

    ```
    sudo yum install realmd sssd krb5-workstation krb5-libs oddjob oddjob-mkhomedir samba-common-tools
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
    > * Określ użytkownika, który należy do grupy "Administratorzy kontrolera domeny usługi AAD".
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
    sudo realm join --verbose CONTOSO100.COM -U 'bob@CONTOSO100.COM'
    ```

Należy pobrać wiadomości ("pomyślnie zarejestrowane maszyny w obszarze") po maszyna jest pomyślnie dołączona do domeny zarządzanej.


## <a name="verify-domain-join"></a>Sprawdź, przyłączanie do domeny
Sprawdź, czy maszyna została pomyślnie dołączona do domeny zarządzanej. Nawiązać CentOS przyłączony do domeny maszyny Wirtualnej przy użyciu innego połączenia SSH. Przy użyciu konta użytkownika domeny, a następnie sprawdź, czy konto użytkownika jest poprawnie rozpoznać.

1. Twoje końcowych SSH wpisz następujące polecenie, aby połączyć się z domeną przyłączony maszyny wirtualnej CentOS przy użyciu protokołu SSH. Użyj konta domeny, który należy do domeny zarządzanej (na przykład "bob@CONTOSO100.COM" w tym przypadku.)
    ```
    ssh -l bob@CONTOSO100.COM contoso-centos.contoso100.com
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
* [Instalowanie protokołu Kerberos](https://access.redhat.com/documentation/en-US/Red_Hat_Enterprise_Linux/6/html/Managing_Smart_Cards/installing-kerberos.html)
* [Red Hat Enterprise Linux 7 — przewodnik integracji systemu Windows](https://access.redhat.com/documentation/en-US/Red_Hat_Enterprise_Linux/7/html/Windows_Integration_Guide/index.html)
