---
title: Infrastruktura aktualizacji systemu Red Hat | Dokumentacja firmy Microsoft
description: Dowiedz się więcej o Red Hat Update Infrastructure wystąpienia w systemie Red Hat Enterprise Linux na żądanie w systemie Microsoft Azure
services: virtual-machines-linux
documentationcenter: ''
author: BorisB2015
manager: jeconnoc
editor: ''
ms.assetid: f495f1b4-ae24-46b9-8d26-c617ce3daf3a
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 5/7/2019
ms.author: borisb
ms.openlocfilehash: 7909ee1dc3980a5a4ff2418d4d6790361a66a65e
ms.sourcegitcommit: 6f043a4da4454d5cb673377bb6c4ddd0ed30672d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/08/2019
ms.locfileid: "65410737"
---
# <a name="red-hat-update-infrastructure-for-on-demand-red-hat-enterprise-linux-vms-in-azure"></a>Red Hat Update Infrastructure maszyn wirtualnych systemu Linux Enterprise na żądanie w systemie Red Hat na platformie Azure
 [Red Hat Update Infrastructure](https://access.redhat.com/products/red-hat-update-infrastructure) (RHUI) umożliwia dostawcom usług w chmurze, takich jak Azure duplikatów zawartości hostowanej w systemie Red Hat repozytorium, Utwórz niestandardowe repozytoria specyficzne dla platformy Azure zawartości i udostępnić go do maszyn wirtualnych przez użytkownika końcowego.

Red Hat Enterprise Linux (RHEL) płatność za rzeczywiste użycie (PAYG) pochodzą wstępnie skonfigurowane do dostępu do usługi RHUI platformy Azure. Dodatkowa konfiguracja nie jest potrzebna. Aby uzyskać najnowsze aktualizacje, uruchom `sudo yum update` po Wystąpienie RHEL jest gotowy. Ta usługa jest częścią opłat za oprogramowanie PAYG systemu RHEL.

Dostępne są dodatkowe informacje dotyczące obrazów systemu RHEL na platformie Azure, publikowanie i zasad przechowywania, w tym [tutaj](./rhel-images.md).

Można znaleźć informacji na temat zasad wsparcia firmy Red Hat dla wszystkich wersji systemu RHEL na [Red Hat Enterprise Linux cyklu](https://access.redhat.com/support/policy/updates/errata) strony.

## <a name="important-information-about-azure-rhui"></a>Ważne informacje na temat usługi RHUI platformy Azure
* RHUI platformy Azure obsługuje obecnie tylko najnowszą wersję pomocniczą w każdej rodziny RHEL (RHEL6 lub RHEL7). Aby uaktualnić wystąpienia maszyny Wirtualnej z systemem RHEL podłączony do usługi RHUI do najnowszej wersji pomocniczej, uruchom `sudo yum update`.

    Na przykład, jeśli możesz aprowizować maszynę Wirtualną z obrazu systemu RHEL 7.4 PAYG i uruchomić `sudo yum update`, na końcu RHEL 7.6 maszyny Wirtualnej (Najnowsza wersja pomocnicza rodziny RHEL7).

    Aby uniknąć tego zachowania, potrzebne do tworzenia własnego obrazu, zgodnie z opisem w [tworzenie i przekazywanie maszyny wirtualnej z systemem Red Hat na platformie Azure](redhat-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) artykułu. Musisz połączyć go z infrastruktury inną aktualizację ([bezpośrednio do firmy Red Hat zawartości serwerów dostarczania](https://access.redhat.com/solutions/253273) lub [serwera Red Hat satelitarnej](https://access.redhat.com/products/red-hat-satellite)).

* Dostęp do usługi RHUI hostowanymi na platformie Azure jest uwzględniona w cenie obrazu systemu RHEL PAYG. Jeśli wyrejestrujesz maszyny Wirtualnej systemu RHEL PAYG z usługi RHUI hostowanymi na platformie Azure, nie Konwertuj maszynę wirtualną do typu bring-your-own-license (BYOL) maszyny Wirtualnej. Jeśli zarejestrujesz tej samej maszyny Wirtualnej z innego źródła aktualizacji, mogą zostać naliczone _pośrednich_ dwukrotnie opłaty. Opłaty są naliczane opłaty za oprogramowania Azure RHEL po raz pierwszy. Opłaty są naliczane w przypadku subskrypcji Red Hat, które zostały zakupione wcześniej po raz drugi. Jeśli potrzebujesz spójne używanie infrastruktury aktualizacji niż RHUI hostowanymi na platformie Azure, należy wziąć pod uwagę tworzenie i wdrażanie własnych obrazów (typ BYOL). Ten proces jest opisany w [tworzenie i przekazywanie maszyny wirtualnej z systemem Red Hat na platformie Azure](redhat-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

* Obrazy systemu RHEL, PAYG SAP na platformie Azure (RHEL for SAP, RHEL for SAP HANA i RHEL for SAP Business Applications) są połączone z dedykowanych kanałów RHUI, które pozostają na określonych wersji pomocniczej RHEL, zgodnie z potrzebami do certyfikacji SAP.

* Dostęp do RHUI hostowanymi na platformie Azure jest ograniczona do maszyn wirtualnych w ramach [zakresy IP centrów danych platformy Azure](https://www.microsoft.com/download/details.aspx?id=41653). Jeśli jesteś pośredniczenie cały ruch maszyny Wirtualnej za pomocą infrastruktury sieci w środowisku lokalnym, może być konieczne skonfigurować trasy zdefiniowane przez użytkownika, dostęp do usługi RHUI Azure maszyn wirtualnych PAYG systemu RHEL.

## <a name="rhel-eus-and-version-locking-rhel-vms"></a>RHEL EUS i blokowanie wersji maszyn wirtualnych systemu RHEL
Niektórzy klienci mogą chcieć zablokować RHEL maszyn wirtualnych do niektórych wersji pomocniczej systemu RHEL. Możesz wersji blokady do określonej wersji pomocniczej maszyny Wirtualnej systemu RHEL, aktualizując repozytoriów, aby wskazywał repozytoriów wsparcia aktualizacji. Użyj poniższych instrukcji, aby zablokować maszyny Wirtualnej z systemem RHEL w konkretnej wersji pomocniczej:

>[!NOTE]
> Dotyczy to tylko dla wersji systemu RHEL, dla których EUS jest dostępna. W momencie pisania tego dokumentu w tym systemu RHEL 7.2 7.6. Szczegółowe informacje są dostępne pod adresem [Red Hat Enterprise Linux cyklu](https://access.redhat.com/support/policy/updates/errata) strony.

1. Wyłącz EUS innych repozytoriów:
    ```bash
    sudo yum --disablerepo='*' remove 'rhui-azure-rhel7'
    ```

1. Dodaj EUS repozytoriów:
    ```bash
    yum --config='https://rhelimage.blob.core.windows.net/repositories/rhui-microsoft-azure-rhel7-eus.config' install 'rhui-azure-rhel7-eus'
    ```

1. Blokada zmiennej releasever:
    ```bash
    echo $(. /etc/os-release && echo $VERSION_ID) > /etc/yum/vars/releasever
    ```

    >[!NOTE]
    > Spowoduje to zablokowanie wersję pomocniczą systemu RHEL do bieżącej wersji pomocniczej powyższych instrukcji. Wprowadź określonych wersji pomocniczej, jeśli chcesz przeprowadzić uaktualnienie i Zablokuj do nowszej wersji pomocniczej, która nie jest najpóźniejsza. Na przykład `echo 7.5 > /etc/yum/vars/releasever` spowoduje zablokowanie używanej wersji systemu RHEL na RHEL w wersji 7.5

1. Aktualizowanie maszyny Wirtualnej systemu RHEL
    ```bash
    sudo yum update
    ```

## <a name="the-ips-for-the-rhui-content-delivery-servers"></a>Adresy IP usługi RHUI serwerów dostarczania zawartości

Usługi RHUI jest dostępna we wszystkich regionach, w której obrazów na żądanie systemu RHEL są dostępne. Obecnie zawiera wszystkich publicznych regionach na [pulpit nawigacyjny stanu platformy Azure](https://azure.microsoft.com/status/) strony, dla administracji USA i regiony Microsoft Azure (Niemcy).

Jeśli używasz konfiguracji sieci bardziej ograniczyć dostęp z maszyn wirtualnych z systemem RHEL PAYG, upewnij się, że następujące adresy IP są dozwolone w przypadku `yum update` do pracy w zależności od środowiska, jesteś w:


```
# Azure Global
13.91.47.76
40.85.190.91
52.187.75.218
52.174.163.213
52.237.203.198

# Azure US Government
13.72.186.193
13.72.14.155
52.244.249.194

# Azure Germany
51.5.243.77
51.4.228.145
```

## <a name="azure-rhui-infrastructure"></a>Infrastruktura platformy Azure usługi RHUI


### <a name="update-expired-rhui-client-certificate-on-a-vm"></a>Aktualizacja wygasłego certyfikatu klienta usługi RHUI na maszynie Wirtualnej

Jeśli używasz starszej obrazu maszyny Wirtualnej z systemem RHEL, na przykład RHEL 7.4 (obraz URN: `RedHat:RHEL:7.4:7.4.2018010506`), będą występować problemy z połączeniem usługi RHUI z powodu wygasł certyfikat klienta SSL. Błąd widzisz może wyglądać jak _"elementu równorzędnego protokołu SSL odrzucony certyfikatu, jako wygasłe"_ lub _"Błąd: Nie można pobrać metadanych repozytorium (repomd.xml) dla repozytorium:... Sprawdź jego ścieżkę i spróbuj ponownie"_. Aby rozwiązać ten problem, zaktualizuj pakiet klienta usługi RHUI na maszynie Wirtualnej, używając następującego polecenia:

```bash
sudo yum update -y --disablerepo='*' --enablerepo='*microsoft*'
```

Alternatywnie systemem `sudo yum update` także zaktualizować pakiet certyfikatu klienta (w zależności od używanej wersji systemu RHEL), pomimo błędów "wygasły certyfikat SSL" zostanie wyświetlony w przypadku innych repozytoriów. Jeśli ta aktualizacja się powiedzie, normalne łączność do innych repozytoriów usługi RHUI powinna zostać przywrócona, aby można było uruchomić `sudo yum update` pomyślnie.

Jeśli napotkasz błąd 404 podczas uruchamiania `yum update`, spróbuj wykonać następujące czynności, aby odświeżyć pamięć podręczną yum:
```bash
sudo yum clean all;
sudo yum makecache
```

### <a name="troubleshoot-connection-problems-to-azure-rhui"></a>Rozwiązywanie problemów z połączeniem do usługi RHUI platformy Azure
Jeśli wystąpią problemy z połączeniem z maszyny Wirtualnej platformy Azure RHEL PAYG RHUI platformy Azure, wykonaj następujące kroki:

1. Sprawdź konfigurację maszyny Wirtualnej dla punktu końcowego usługi RHUI platformy Azure:

    a. Sprawdź, czy `/etc/yum.repos.d/rh-cloud.repo` plik zawiera odwołanie do `rhui-[1-3].microsoft.com` w `baseurl` z `[rhui-microsoft-azure-rhel*]` części pliku. Jeśli tak jest, używasz nowej usługi RHUI platformy Azure.

    b. Jeśli wskazuje on lokalizację z następującym wzorcem `mirrorlist.*cds[1-4].cloudapp.net`, wymagana jest aktualizacja konfiguracji. Używasz starego migawki maszyny Wirtualnej, a musisz zaktualizować to ustawienie, aby nowe usługi RHUI platformy Azure.

1. Dostęp do RHUI hostowanymi na platformie Azure jest ograniczona do maszyn wirtualnych w ramach [zakresy IP centrów danych platformy Azure](https://www.microsoft.com/download/details.aspx?id=41653).

1. Jeśli używasz nowej konfiguracji sprawdzeniu, czy maszyna wirtualna nawiązuje połączenie z zakresu adresów IP platformy Azure i nadal nie można nawiązać połączenia usługi RHUI platformy Azure, plik zgłoszenia do pomocy technicznej z firmą Microsoft lub Red Hat.

### <a name="infrastructure-update"></a>Infrastruktura aktualizacji

We wrześniu 2016 roku wdrożyliśmy zaktualizowane RHUI platformy Azure. Kwietnia 2017 r. Firma Microsoft zamknąć starych RHUI platformy Azure. Jeśli masz doświadczenie z obrazów systemu RHEL PAYG (lub ich migawki) od września 2016 lub nowszym, automatycznie łączenia z nowej usługi RHUI platformy Azure. Jeśli jednak masz starszą migawek na maszynach wirtualnych, musisz ręcznie zaktualizować swoją konfigurację dostępu usługi RHUI platformy Azure, zgodnie z opisem w poniższej sekcji.

Nowe serwery usługi RHUI platformy Azure są wdrażane przy użyciu [usługi Azure Traffic Manager](https://azure.microsoft.com/services/traffic-manager/). W usłudze Traffic Manager, jeden punkt końcowy (rhui 1.microsoft.com) może służyć przez dowolnej maszyny Wirtualnej, niezależnie od określonego regionu.

### <a name="manual-update-procedure-to-use-the-azure-rhui-servers"></a>Procedura ręcznej aktualizacji do korzystania z serwerów usługi RHUI platformy Azure
Ta procedura znajduje się tylko do celów referencyjnych. Obrazy systemu RHEL PAYG już prawidłowej konfiguracji, aby nawiązać połączenie usługi RHUI Azure. Aby ręcznie zaktualizować konfigurację, aby korzystać z serwerów usługi RHUI platformy Azure, wykonaj następujące czynności:

1. Pobierz podpis klucza publicznego, za pomocą programu curl.

   ```bash
   curl -o RPM-GPG-KEY-microsoft-azure-release https://download.microsoft.com/download/9/D/9/9d945f05-541d-494f-9977-289b3ce8e774/microsoft-sign-public.asc
   ```

1. Sprawdź poprawność pobrany klucz.

   ```bash
   gpg --list-packets --verbose < RPM-GPG-KEY-microsoft-azure-release
   ```

1. Sprawdź dane wyjściowe, a następnie sprawdź `keyid` i `user ID packet`.

   ```bash
   Version: GnuPG v1.4.7 (GNU/Linux)
   :public key packet:
           version 4, algo 1, created 1446074508, expires 0
           pkey[0]: [2048 bits]
           pkey[1]: [17 bits]
           keyid: EB3E94ADBE1229CF
   :user ID packet: "Microsoft (Release signing) <gpgsecurity@microsoft.com>"
   :signature packet: algo 1, keyid EB3E94ADBE1229CF
           version 4, created 1446074508, md5len 0, sigclass 0x13
           digest algo 2, begin of digest 1a 9b
           hashed subpkt 2 len 4 (sig created 2015-10-28)
           hashed subpkt 27 len 1 (key flags: 03)
           hashed subpkt 11 len 5 (pref-sym-algos: 9 8 7 3 2)
           hashed subpkt 21 len 3 (pref-hash-algos: 2 8 3)
           hashed subpkt 22 len 2 (pref-zip-algos: 2 1)
           hashed subpkt 30 len 1 (features: 01)
           hashed subpkt 23 len 1 (key server preferences: 80)
           subpkt 16 len 8 (issuer key ID EB3E94ADBE1229CF)
           data: [2047 bits]
   ```

1. Zainstaluj klucz publiczny.

   ```bash
   sudo install -o root -g root -m 644 RPM-GPG-KEY-microsoft-azure-release /etc/pki/rpm-gpg
   sudo rpm --import /etc/pki/rpm-gpg/RPM-GPG-KEY-microsoft-azure-release
   ```

1. Pobierz, sprawdź i zainstaluj klienta Menedżera pakietów RPM (RPM).

    >[!NOTE]
    >Zmiana wersji pakietu. Należy ręcznie połączyć się z usługi RHUI platformy Azure, można znaleźć najnowszą wersję pakietu klienta w ramach każdej rodziny RHEL, aprowizując najnowszego obrazu z galerii.

   a. Pobierz.

    - Systemu RHEL 6:
        ```bash
        curl -o azureclient.rpm https://rhui-1.microsoft.com/pulp/repos/microsoft-azure-rhel6/Packages/r/rhui-azure-rhel6-2.2-74.noarch.rpm
        ```

    - Systemu RHEL 7:
        ```bash
        curl -o azureclient.rpm https://rhui-1.microsoft.com/pulp/repos/microsoft-azure-rhel7/Packages/r/rhui-azure-rhel7-2.2-74.noarch.rpm
        ```

   b. Sprawdź.

   ```bash
   rpm -Kv azureclient.rpm
   ```

   c. Sprawdź dane wyjściowe, aby upewnić się, że podpis pakietu jest OK.

   ```bash
   azureclient.rpm:
       Header V3 RSA/SHA256 Signature, key ID be1229cf: OK
       Header SHA1 digest: OK (927a3b548146c95a3f6c1a5d5ae52258a8859ab3)
       V3 RSA/SHA256 Signature, key ID be1229cf: OK
       MD5 digest: OK (c04ff605f82f4be8c96020bf5c23b86c)
   ```

   d. Zainstaluj obr. / min.

    ```bash
    sudo rpm -U azureclient.rpm
    ```

1. Po zakończeniu wprowadzania zmian, sprawdź, czy możesz uzyskać dostęp usługi RHUI platformy Azure z maszyny Wirtualnej.

## <a name="next-steps"></a>Kolejne kroki
* Tworzenie maszyny Wirtualnej z Red Hat Enterprise Linux na podstawie obrazu PAYG Marketplace usługi Azure i używać RHUI hostowanymi na platformie Azure, przejdź do [portalu Azure Marketplace](https://azure.microsoft.com/marketplace/partners/redhat/).
* Aby dowiedzieć się więcej na temat obrazów Red Hat na platformie Azure, przejdź do [stronę z dokumentacją dotyczącą](./rhel-images.md).
* Można znaleźć informacji na temat zasad wsparcia firmy Red Hat dla wszystkich wersji systemu RHEL na [Red Hat Enterprise Linux cyklu](https://access.redhat.com/support/policy/updates/errata) strony.
