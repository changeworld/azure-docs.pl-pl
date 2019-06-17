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
ms.date: 6/6/2019
ms.author: borisb
ms.openlocfilehash: 4315a849f3f117633f5f6a9d93c995ece9f527a3
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "67076997"
---
# <a name="red-hat-update-infrastructure-for-on-demand-red-hat-enterprise-linux-vms-in-azure"></a>Red Hat Update Infrastructure maszyn wirtualnych systemu Linux Enterprise na żądanie w systemie Red Hat na platformie Azure
 [Red Hat Update Infrastructure](https://access.redhat.com/products/red-hat-update-infrastructure) (RHUI) umożliwia dostawcom usług w chmurze, takich jak Azure duplikatów zawartości hostowanej w systemie Red Hat repozytorium, Utwórz niestandardowe repozytoria specyficzne dla platformy Azure zawartości i udostępnić go do maszyn wirtualnych przez użytkownika końcowego.

Red Hat Enterprise Linux (RHEL) płatność za rzeczywiste użycie (PAYG) pochodzą wstępnie skonfigurowane do dostępu do usługi RHUI platformy Azure. Dodatkowa konfiguracja nie jest potrzebna. Aby uzyskać najnowsze aktualizacje, uruchom `sudo yum update` po Wystąpienie RHEL jest gotowy. Ta usługa jest częścią opłat za oprogramowanie PAYG systemu RHEL.

Dostępne są dodatkowe informacje dotyczące obrazów systemu RHEL na platformie Azure, publikowanie i zasad przechowywania, w tym [tutaj](./rhel-images.md).

Można znaleźć informacji na temat zasad wsparcia firmy Red Hat dla wszystkich wersji systemu RHEL na [Red Hat Enterprise Linux cyklu](https://access.redhat.com/support/policy/updates/errata) strony.

## <a name="important-information-about-azure-rhui"></a>Ważne informacje na temat usługi RHUI platformy Azure
* Azure RHUI jest infrastruktura aktualizacji, która obsługuje wszystkie RHEL PAYG maszyny wirtualne utworzone na platformie Azure. To nie uniemożliwia możesz rejestrowanie maszyn wirtualnych z systemem RHEL PAYG w usłudze Menedżer subskrypcji lub satelitarnej lub innego źródła aktualizacji, ale to z maszyną Wirtualną PAYG będzie doprowadzić do pośredniego rozliczeń podwójnej precyzji. Zobacz następujący punkt, aby uzyskać szczegółowe informacje.
* Dostęp do usługi RHUI hostowanymi na platformie Azure jest uwzględniona w cenie obrazu systemu RHEL PAYG. Jeśli wyrejestrujesz maszyny Wirtualnej systemu RHEL PAYG z usługi RHUI hostowanymi na platformie Azure, nie Konwertuj maszynę wirtualną do typu bring-your-own-license (BYOL) maszyny Wirtualnej. Jeśli zarejestrujesz tej samej maszyny Wirtualnej z innego źródła aktualizacji, mogą zostać naliczone _pośrednich_ dwukrotnie opłaty. Opłaty są naliczane opłaty za oprogramowania Azure RHEL po raz pierwszy. Opłaty są naliczane w przypadku subskrypcji Red Hat, które zostały zakupione wcześniej po raz drugi. Jeśli potrzebujesz spójne używanie infrastruktury aktualizacji niż RHUI hostowanymi na platformie Azure, należy wziąć pod uwagę rejestrowanie użycia [obrazów systemu RHEL BYOS](https://aka.ms/rhel-byos).
* Domyślne zachowanie usługi RHUI to uaktualnienia do najnowszej wersji pomocniczej maszyny Wirtualnej systemu RHEL, po uruchomieniu `sudo yum update`.

    Na przykład, jeśli możesz aprowizować maszynę Wirtualną z obrazu systemu RHEL 7.4 PAYG i uruchomić `sudo yum update`, na końcu RHEL 7.6 maszyny Wirtualnej (Najnowsza wersja pomocnicza rodziny RHEL7).

    Aby uniknąć tego zachowania, możesz przełączyć się do [kanały rozszerzony obsługę aktualizacji](#rhel-eus-and-version-locking-rhel-vms) lub utworzyć własny obraz, zgodnie z opisem w [tworzenie i przekazywanie maszyny wirtualnej z systemem Red Hat na platformie Azure](redhat-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) artykułu. W przypadku tworzenia własnego obrazu, należy nawiązać infrastruktury inną aktualizację ([bezpośrednio do firmy Red Hat zawartości serwerów dostarczania](https://access.redhat.com/solutions/253273) lub [serwera Red Hat satelitarnej](https://access.redhat.com/products/red-hat-satellite)).



* Obrazy systemu RHEL, PAYG SAP na platformie Azure (RHEL for SAP, RHEL for SAP HANA i RHEL for SAP Business Applications) są połączone z dedykowanych kanałów RHUI, które pozostają na określonych wersji pomocniczej RHEL, zgodnie z potrzebami do certyfikacji SAP.

* Dostęp do RHUI hostowanymi na platformie Azure jest ograniczona do maszyn wirtualnych w ramach [zakresy IP centrów danych platformy Azure](https://www.microsoft.com/download/details.aspx?id=41653). Jeśli jesteś pośredniczenie cały ruch maszyny Wirtualnej za pomocą infrastruktury sieci w środowisku lokalnym, może być konieczne skonfigurować trasy zdefiniowane przez użytkownika, dostęp do usługi RHUI Azure maszyn wirtualnych PAYG systemu RHEL.

## <a name="rhel-eus-and-version-locking-rhel-vms"></a>RHEL EUS i blokowanie wersji maszyn wirtualnych systemu RHEL
Niektórzy klienci mogą chcieć zablokować RHEL maszyn wirtualnych do niektórych wersji pomocniczej systemu RHEL. Możesz wersji blokady do określonej wersji pomocniczej maszyny Wirtualnej systemu RHEL, aktualizując repozytoriów, aby wskazywał repozytoriów wsparcia aktualizacji. Operacja blokowania wersji EUS również można cofnąć.

>[!NOTE]
> EUS nie jest obsługiwana na RHEL dodatki. Oznacza to, że jeśli instalujesz pakiet, który znajduje się zwykle z kanału dodatki RHEL, nie będzie w tym celu znajduje się na EUS. Red Hat dodatki cykl życia została szczegółowo opisana [tutaj](https://access.redhat.com/support/policy/updates/extras/).

W momencie pisania tego dokumentu, obsługa EUS została zakończona systemu RHEL < = 7.3. Zobacz sekcję "Red Hat Enterprise Linux dłużej pomocy technicznej dodatki" w [dokumentacji firmy Red Hat](https://access.redhat.com/support/policy/updates/errata/) Aby uzyskać więcej informacji.
* RHEL 7.4 EUS pomocy technicznej kończy się 31 sierpnia 2019 r.
* RHEL w wersji 7.5 EUS pomocy technicznej kończy się 30 kwietnia 2020 r.
* RHEL 7.6 EUS pomocy technicznej kończy się 31 października 2020 r.

### <a name="switch-a-rhel-vm-to-eus-version-lock-to-a-specific-minor-version"></a>Przełącz maszyny Wirtualnej z systemem RHEL do EUS (do określonej wersji pomocniczej wersji — blokada)
Użyj poniższych instrukcji, aby zablokować maszyny Wirtualnej z systemem RHEL w konkretnej wersji pomocniczej (Uruchom jako użytkownik główny):

>[!NOTE]
> Dotyczy to tylko dla wersji systemu RHEL, dla których EUS jest dostępna. W momencie pisania tego dokumentu w tym systemu RHEL 7.2 7.6. Szczegółowe informacje są dostępne pod adresem [Red Hat Enterprise Linux cyklu](https://access.redhat.com/support/policy/updates/errata) strony.

1. Wyłącz EUS innych repozytoriów:
    ```bash
    yum --disablerepo='*' remove 'rhui-azure-rhel7'
    ```

1. Dodaj EUS repozytoriów:
    ```bash
    yum --config='https://rhelimage.blob.core.windows.net/repositories/rhui-microsoft-azure-rhel7-eus.config' install 'rhui-azure-rhel7-eus'
    ```

1. Blokowanie zmiennej releasever (Uruchom jako użytkownik główny):
    ```bash
    echo $(. /etc/os-release && echo $VERSION_ID) > /etc/yum/vars/releasever
    ```

    >[!NOTE]
    > Spowoduje to zablokowanie wersję pomocniczą systemu RHEL do bieżącej wersji pomocniczej powyższych instrukcji. Wprowadź określonych wersji pomocniczej, jeśli chcesz przeprowadzić uaktualnienie i Zablokuj do nowszej wersji pomocniczej, która nie jest najpóźniejsza. Na przykład `echo 7.5 > /etc/yum/vars/releasever` spowoduje zablokowanie używanej wersji systemu RHEL na RHEL w wersji 7.5

1. Aktualizowanie maszyny Wirtualnej systemu RHEL
    ```bash
    sudo yum update
    ```

### <a name="switch-a-rhel-vm-back-to-non-eus-remove-a-version-lock"></a>Przełącz maszyny Wirtualnej z systemem RHEL się do innych EUS (usunąć blokadę wersji)
Jako użytkownik główny, uruchom następujące polecenie:
1. Usuń plik releasever:
    ```bash
    rm /etc/yum/vars/releasever
     ```

1. Wyłącz EUS repozytoriów:
    ```bash
    yum --disablerepo='*' remove 'rhui-azure-rhel7-eus'
   ```

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

Jeśli używasz starszej obrazu maszyny Wirtualnej z systemem RHEL, na przykład RHEL 7.4 (obraz URN: `RedHat:RHEL:7.4:7.4.2018010506`), będą występować problemy z połączeniem usługi RHUI z powodu wygasł certyfikat klienta SSL. Błąd widzisz może wyglądać jak _"elementu równorzędnego protokołu SSL odrzucony certyfikatu, jako wygasłe"_ lub _"Błąd: Nie można pobrać metadanych repozytorium (repomd.xml) dla repozytorium:... Sprawdź jego ścieżkę i spróbuj ponownie"_ . Aby rozwiązać ten problem, zaktualizuj pakiet klienta usługi RHUI na maszynie Wirtualnej, używając następującego polecenia:

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

    1. Sprawdź, czy `/etc/yum.repos.d/rh-cloud.repo` plik zawiera odwołanie do `rhui-[1-3].microsoft.com` w `baseurl` z `[rhui-microsoft-azure-rhel*]` części pliku. Jeśli tak jest, używasz nowej usługi RHUI platformy Azure.

    1. Jeśli wskazuje on lokalizację z następującym wzorcem `mirrorlist.*cds[1-4].cloudapp.net`, wymagana jest aktualizacja konfiguracji. Używasz starego migawki maszyny Wirtualnej, a musisz zaktualizować to ustawienie, aby nowe usługi RHUI platformy Azure.

1. Dostęp do RHUI hostowanymi na platformie Azure jest ograniczona do maszyn wirtualnych w ramach [zakresy IP centrów danych platformy Azure](https://www.microsoft.com/download/details.aspx?id=41653).

1. Jeśli używasz nowej konfiguracji sprawdzeniu, czy maszyna wirtualna nawiązuje połączenie z zakresu adresów IP platformy Azure i nadal nie można nawiązać połączenia usługi RHUI platformy Azure, plik zgłoszenia do pomocy technicznej z firmą Microsoft lub Red Hat.

### <a name="infrastructure-update"></a>Infrastruktura aktualizacji

We wrześniu 2016 roku wdrożyliśmy zaktualizowane RHUI platformy Azure. Kwietnia 2017 r. Firma Microsoft zamknąć starych RHUI platformy Azure. Jeśli masz doświadczenie z obrazów systemu RHEL PAYG (lub ich migawki) od września 2016 lub nowszym, automatycznie łączenia z nowej usługi RHUI platformy Azure. Jeśli jednak masz starszą migawek na maszynach wirtualnych, musisz ręcznie zaktualizować swoją konfigurację dostępu usługi RHUI platformy Azure, zgodnie z opisem w poniższej sekcji.

Nowe serwery usługi RHUI platformy Azure są wdrażane przy użyciu [usługi Azure Traffic Manager](https://azure.microsoft.com/services/traffic-manager/). W usłudze Traffic Manager, jeden punkt końcowy (rhui 1.microsoft.com) może służyć przez dowolnej maszyny Wirtualnej, niezależnie od określonego regionu.

### <a name="manual-update-procedure-to-use-the-azure-rhui-servers"></a>Procedura ręcznej aktualizacji do korzystania z serwerów usługi RHUI platformy Azure
Ta procedura znajduje się tylko do celów referencyjnych. Obrazy systemu RHEL PAYG już prawidłowej konfiguracji, aby nawiązać połączenie usługi RHUI Azure. Aby ręcznie zaktualizować konfigurację, aby korzystać z serwerów usługi RHUI platformy Azure, wykonaj następujące czynności:

- Systemu RHEL 6:
  ```bash
  yum --config='https://rhelimage.blob.core.windows.net/repositories/rhui-microsoft-azure-rhel6.config' install 'rhui-azure-rhel6'
  ```

- Systemu RHEL 7:
  ```bash
  yum --config='https://rhelimage.blob.core.windows.net/repositories/rhui-microsoft-azure-rhel7.config' install 'rhui-azure-rhel7'
  ```

## <a name="next-steps"></a>Kolejne kroki
* Tworzenie maszyny Wirtualnej z Red Hat Enterprise Linux na podstawie obrazu PAYG Marketplace usługi Azure i używać RHUI hostowanymi na platformie Azure, przejdź do [portalu Azure Marketplace](https://azure.microsoft.com/marketplace/partners/redhat/).
* Aby dowiedzieć się więcej na temat obrazów Red Hat na platformie Azure, przejdź do [stronę z dokumentacją dotyczącą](./rhel-images.md).
* Można znaleźć informacji na temat zasad wsparcia firmy Red Hat dla wszystkich wersji systemu RHEL na [Red Hat Enterprise Linux cyklu](https://access.redhat.com/support/policy/updates/errata) strony.
