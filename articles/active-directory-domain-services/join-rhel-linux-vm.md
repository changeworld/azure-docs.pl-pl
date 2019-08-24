---
title: 'Azure Active Directory Domain Services: Przyłączanie maszyny wirtualnej RHEL do domeny zarządzanej | Microsoft Docs'
description: Przyłączanie maszyny wirtualnej Red Hat Enterprise Linux do Azure AD Domain Services
services: active-directory-ds
documentationcenter: ''
author: iainfoulds
manager: daveba
editor: curtand
ms.assetid: d76ae997-2279-46dd-bfc5-c0ee29718096
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/20/2019
ms.author: iainfou
ms.openlocfilehash: b59bd7c7196ceb87da087967498eca6dda7c212b
ms.sourcegitcommit: 007ee4ac1c64810632754d9db2277663a138f9c4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/23/2019
ms.locfileid: "69990606"
---
# <a name="join-a-red-hat-enterprise-linux-7-virtual-machine-to-a-managed-domain"></a>Przyłączanie maszyny wirtualnej z systemem Red Hat Enterprise Linux 7 do domeny zarządzanej
W tym artykule opisano sposób przyłączania maszyny wirtualnej Red Hat Enterprise Linux (RHEL) 7 do domeny zarządzanej Azure AD Domain Services.

[!INCLUDE [active-directory-ds-prerequisites.md](../../includes/active-directory-ds-prerequisites.md)]

## <a name="before-you-begin"></a>Przed rozpoczęciem
Aby wykonać zadania wymienione w tym artykule, potrzebne są:  
1. Prawidłowa **subskrypcja platformy Azure**.
2. **Katalog usługi Azure AD** — zsynchronizowany z katalogiem lokalnym lub katalogiem w chmurze.
3. Należy włączyć **Azure AD Domain Services** dla katalogu usługi Azure AD. Jeśli nie zostało to zrobione, postępuj zgodnie ze wszystkimi zadaniami opisanymi w [przewodniku wprowadzenie](tutorial-create-instance.md).
4. Upewnij się, że adresy IP domeny zarządzanej zostały skonfigurowane jako serwery DNS dla sieci wirtualnej. Aby uzyskać więcej informacji, zobacz [jak zaktualizować ustawienia DNS dla sieci wirtualnej platformy Azure](tutorial-create-instance.md#update-dns-settings-for-the-azure-virtual-network)
5. Wykonaj kroki wymagane do [synchronizacji haseł do domeny zarządzanej Azure AD Domain Services](tutorial-create-instance.md#enable-user-accounts-for-azure-ad-ds).


## <a name="provision-a-red-hat-enterprise-linux-virtual-machine"></a>Inicjowanie obsługi administracyjnej maszyny wirtualnej Red Hat Enterprise Linux
Zapewnij maszynę wirtualną z systemem RHEL 7 na platformie Azure przy użyciu dowolnej z następujących metod:
* [Azure Portal](../virtual-machines/linux/quick-create-portal.md)
* [Interfejs wiersza polecenia platformy Azure](../virtual-machines/linux/quick-create-cli.md)
* [Azure PowerShell](../virtual-machines/linux/quick-create-powershell.md)

> [!IMPORTANT]
> * Wdróż maszynę wirtualną w tej **samej sieci wirtualnej, w której włączono Azure AD Domain Services**.
> * Wybierz **inną podsieć** niż ta, w której włączono Azure AD Domain Services.
>


## <a name="connect-remotely-to-the-newly-provisioned-linux-virtual-machine"></a>Zdalne nawiązywanie połączenia z nowo zainicjowaną maszyną wirtualną z systemem Linux
Maszyna wirtualna z RHEL 7,2 została zainicjowana na platformie Azure. Następnym zadaniem jest zdalne nawiązywanie połączenia z maszyną wirtualną przy użyciu konta administratora lokalnego utworzonego podczas aprowizacji maszyny wirtualnej.

Postępuj zgodnie z instrukcjami w artykule [Jak zalogować się do maszyny wirtualnej z systemem Linux](../virtual-machines/linux/mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).


## <a name="configure-the-hosts-file-on-the-linux-virtual-machine"></a>Konfigurowanie pliku Hosts na maszynie wirtualnej z systemem Linux
W terminalu SSH Edytuj plik/etc/hosts i zaktualizuj adres IP i nazwę hosta maszyny.

```console
sudo vi /etc/hosts
```

W pliku hosts Wprowadź następującą wartość:

```console
127.0.0.1 contoso-rhel.contoso.com contoso-rhel
```

W tym miejscu "contoso.com" jest nazwą domeny DNS domeny zarządzanej. "contoso-RHEL" jest nazwą hosta maszyny wirtualnej RHEL, do której jest przyłączany do domeny zarządzanej.


## <a name="install-required-packages-on-the-linux-virtual-machine"></a>Instalowanie wymaganych pakietów na maszynie wirtualnej z systemem Linux
Następnie zainstaluj pakiety wymagane do przyłączenia do domeny na maszynie wirtualnej. W terminalu SSH wpisz następujące polecenie, aby zainstalować wymagane pakiety:

```console
sudo yum install realmd sssd krb5-workstation krb5-libs samba-common-tools
```


## <a name="join-the-linux-virtual-machine-to-the-managed-domain"></a>Przyłączanie maszyny wirtualnej z systemem Linux do domeny zarządzanej
Teraz, gdy wymagane pakiety są zainstalowane na maszynie wirtualnej z systemem Linux, następne zadanie polega na przyłączeniu maszyny wirtualnej do domeny zarządzanej.

1. Odkryj domenę zarządzaną usług domenowych w usłudze AAD. W terminalu SSH wpisz następujące polecenie:

    ```console
    sudo realm discover CONTOSO.COM
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

    ```console
    kinit bob@CONTOSO.COM
    ```

3. Dołącz komputer do domeny. W terminalu SSH wpisz następujące polecenie:

    > [!TIP]
    > Użyj tego samego konta użytkownika, które zostało określone w poprzednim kroku ("Narzędzie kinit").
    >
    > Jeśli maszyna wirtualna nie może przyłączyć się do domeny, upewnij się, że sieciowa Grupa zabezpieczeń maszyny wirtualnej zezwala na ruch wychodzący Kerberos na porcie TCP + UDP 464 do podsieci sieci wirtualnej dla domeny zarządzanej platformy Azure AD DS.

    ```console
    sudo realm join --verbose CONTOSO.COM -U 'bob@CONTOSO.COM'
    ```

Po pomyślnym przyłączeniu komputera do domeny zarządzanej powinien zostać wyświetlony komunikat ("pomyślnie zarejestrowano maszynę w obszarze").


## <a name="verify-domain-join"></a>Weryfikuj przyłączanie do domeny
Sprawdź, czy maszyna została pomyślnie przyłączona do domeny zarządzanej. Nawiąż połączenie z dołączoną do domeny maszyną wirtualną RHEL przy użyciu innego połączenia SSH. Użyj konta użytkownika domeny, a następnie sprawdź, czy konto użytkownika jest prawidłowo rozwiązane.

1. W terminalu SSH wpisz następujące polecenie, aby nawiązać połączenie z przyłączoną do domeny maszyną wirtualną RHEL przy użyciu protokołu SSH. Użyj konta domeny, które należy do domeny zarządzanej (na przykład "bob@CONTOSO.COM" w tym przypadku).
    
    ```console
    ssh -l bob@CONTOSO.COM contoso-rhel.contoso.com
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
* [Instalowanie protokołu Kerberos](https://access.redhat.com/documentation/en-US/Red_Hat_Enterprise_Linux/6/html/Managing_Smart_Cards/installing-kerberos.html)
* [Red Hat Enterprise Linux 7 — przewodnik po integracji systemu Windows](https://access.redhat.com/documentation/en-US/Red_Hat_Enterprise_Linux/7/html/Windows_Integration_Guide/index.html)
