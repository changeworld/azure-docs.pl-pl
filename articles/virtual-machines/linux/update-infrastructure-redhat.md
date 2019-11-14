---
title: Infrastruktura aktualizacji systemu Red Hat
description: Dowiedz się więcej o Red Hat Update Infrastructure wystąpienia w systemie Red Hat Enterprise Linux na żądanie w systemie Microsoft Azure
services: virtual-machines-linux
documentationcenter: ''
author: asinn826
manager: BorisB2015
editor: ''
ms.assetid: f495f1b4-ae24-46b9-8d26-c617ce3daf3a
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 6/6/2019
ms.author: borisb
ms.openlocfilehash: b19ccad5254418092446aaf781d49fa7edf0e4f4
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/13/2019
ms.locfileid: "74034320"
---
# <a name="red-hat-update-infrastructure-for-on-demand-red-hat-enterprise-linux-vms-in-azure"></a>Red Hat Update Infrastructure maszyn wirtualnych systemu Linux Enterprise na żądanie w systemie Red Hat na platformie Azure
 [Red Hat Update Infrastructure](https://access.redhat.com/products/red-hat-update-infrastructure) (RHUI) umożliwia dostawcom usług w chmurze, takich jak Azure duplikatów zawartości hostowanej w systemie Red Hat repozytorium, Utwórz niestandardowe repozytoria specyficzne dla platformy Azure zawartości i udostępnić go do maszyn wirtualnych przez użytkownika końcowego.

Red Hat Enterprise Linux (RHEL) płatność za rzeczywiste użycie (PAYG) pochodzą wstępnie skonfigurowane do dostępu do usługi RHUI platformy Azure. Dodatkowa konfiguracja nie jest potrzebna. Aby uzyskać najnowsze aktualizacje, uruchom `sudo yum update` po Wystąpienie RHEL jest gotowy. Ta usługa jest częścią opłat za oprogramowanie PAYG systemu RHEL.

Dodatkowe informacje na temat obrazów RHEL na platformie Azure, w tym zasady publikowania i przechowywania, są dostępne [tutaj](./rhel-images.md).

Informacje o zasadach obsługi systemu Red Hat dla wszystkich wersji programu RHEL można znaleźć na stronie [cykl życia Red Hat Enterprise Linux](https://access.redhat.com/support/policy/updates/errata) .

## <a name="important-information-about-azure-rhui"></a>Ważne informacje na temat usługi RHUI platformy Azure

* Azure RHUI to infrastruktura aktualizacji, która obsługuje wszystkie maszyny wirtualne RHEL PAYG utworzone na platformie Azure. Nie wyklucza to rejestrowania maszyn wirtualnych RHEL PAYG przy użyciu Menedżera subskrypcji ani satelity ani innego źródła aktualizacji, ale dzięki temu z maszyną wirtualną PAYG zostanie pośrednim podwójnym rozliczeniami. Aby uzyskać szczegółowe informacje, zobacz następujący punkt.
* Dostęp do usługi RHUI hostowanymi na platformie Azure jest uwzględniona w cenie obrazu systemu RHEL PAYG. Jeśli wyrejestrujesz maszyny Wirtualnej systemu RHEL PAYG z usługi RHUI hostowanymi na platformie Azure, nie Konwertuj maszynę wirtualną do typu bring-your-own-license (BYOL) maszyny Wirtualnej. Jeśli zarejestrujesz tej samej maszyny Wirtualnej z innego źródła aktualizacji, mogą zostać naliczone _pośrednich_ dwukrotnie opłaty. Opłaty są naliczane opłaty za oprogramowania Azure RHEL po raz pierwszy. Opłaty są naliczane w przypadku subskrypcji Red Hat, które zostały zakupione wcześniej po raz drugi. Jeśli ciągle potrzebujesz użyć infrastruktury aktualizacji innej niż hostowana na platformie Azure RHUI, rozważ zarejestrowanie się w celu korzystania z [obrazów RHEL BYOS](https://aka.ms/rhel-byos).

* RHEL SAP PAYG obrazy na platformie Azure (RHEL for SAP, RHEL for SAP HANA i RHEL for SAP Business Applications) są połączone z dedykowanymi kanałami RHUI, które pozostają w określonej wersji pomocniczej RHEL, zgodnie z wymaganiami certyfikacji SAP.

* Dostęp do RHUI hostowanymi na platformie Azure jest ograniczona do maszyn wirtualnych w ramach [zakresy IP centrów danych platformy Azure](https://www.microsoft.com/download/details.aspx?id=41653). Jeśli jesteś pośredniczenie cały ruch maszyny Wirtualnej za pomocą infrastruktury sieci w środowisku lokalnym, może być konieczne skonfigurować trasy zdefiniowane przez użytkownika, dostęp do usługi RHUI Azure maszyn wirtualnych PAYG systemu RHEL. W takim przypadku trasy zdefiniowane przez użytkownika będą musiały zostać dodane dla _wszystkich_ RHUI adresów IP.

## <a name="image-update-behavior"></a>Zachowanie aktualizacji obrazów

Od kwietnia 2019 platforma Azure oferuje obrazy RHEL, które są połączone z repozytoriami z obsługą aktualizacji rozszerzonych (EUS) domyślnie i obrazy RHEL, które są domyślnie połączone z regularnymi repozytoriami (nie EUS). Więcej szczegółów na temat RHEL EUS są dostępne w dokumentacji dotyczącej [cyklu życia](https://access.redhat.com/support/policy/updates/errata) systemu Red Hat i [dokumentacji EUS](https://access.redhat.com/articles/rhel-eus). Domyślne zachowanie `sudo yum update` będzie się różnić w zależności od tego, który obraz RHEL został zainicjowany, ponieważ różne obrazy są połączone z różnymi repozytoriami.

Aby uzyskać pełną listę obrazów, uruchom `az vm image list --publisher redhat --all` przy użyciu interfejsu wiersza polecenia platformy Azure.

### <a name="images-connected-to-non-eus-repositories"></a>Obrazy połączone z repozytoriami nieEUS

Jeśli zainicjujesz maszynę wirtualną z obrazu RHEL, który jest połączony z repozytoriami nieEUS, podczas uruchamiania `sudo yum update`zostanie uaktualniona do najnowszej wersji pomocniczej RHEL. Na przykład jeśli zainicjujesz maszynę wirtualną na podstawie obrazu RHEL 7,4 PAYG i uruchomisz `sudo yum update`, będziesz kończyć maszynę wirtualną RHEL 7,7 (Najnowsza wersja pomocnicza w rodzinie RHEL7).

Obrazy połączone z repozytoriami nieEUS nie będą zawierać pomocniczego numeru wersji w jednostce SKU. Jednostka SKU jest trzecią częścią nazwy URN (pełna nazwa obrazu). Na przykład wszystkie następujące obrazy są dołączone do repozytoriów innych niż EUS:

```text
RedHat:RHEL:7-LVM:7.4.2018010506
RedHat:RHEL:7-LVM:7.5.2018081518
RedHat:RHEL:7-LVM:7.6.2019062414
RedHat:RHEL:7-RAW:7.4.2018010506
RedHat:RHEL:7-RAW:7.5.2018081518
RedHat:RHEL:7-RAW:7.6.2019062120
```

Należy pamiętać, że jednostki SKU to 7-LVM lub 7-RAW. Wersja pomocnicza jest wskazana w wersji (czwarty element w nazwie URN) tych obrazów.

### <a name="images-connected-to-eus-repositories"></a>Obrazy połączone z repozytoriami EUS

Jeśli zainicjujesz maszynę wirtualną z obrazu RHEL, który jest połączony z repozytoriami EUS, podczas uruchamiania `sudo yum update`nie zostanie uaktualniona do najnowszej wersji pomocniczej RHEL. Wynika to z faktu, że obrazy połączone z repozytoriami EUS są również zablokowane w wersji z określoną wersją pomocniczą.

Obrazy połączone z repozytoriami EUS będą zawierać pomocniczy numer wersji w jednostce SKU. Na przykład wszystkie następujące obrazy są dołączone do repozytoriów EUS:

```text
RedHat:RHEL:7.4:7.4.2019062107
RedHat:RHEL:7.5:7.5.2019062018
RedHat:RHEL:7.6:7.6.2019062116
```

## <a name="rhel-eus-and-version-locking-rhel-vms"></a>RHEL EUS i blokowanie wersji RHEL maszyny wirtualne

Niektórzy klienci mogą chcieć zablokować swoje maszyny wirtualne RHEL do pewnej wersji pomocniczej RHEL po zainicjowaniu obsługi administracyjnej maszyny wirtualnej. Aby zablokować maszynę wirtualną RHEL do określonej wersji pomocniczej, należy zaktualizować repozytoria w taki sposób, aby wskazywały repozytoria pomocy technicznej o rozszerzonej aktualizacji. Możesz również cofnąć operację blokowania wersji EUS.

>[!NOTE]
> EUS nie są obsługiwane w dodatkach RHEL. Oznacza to, że w przypadku instalowania pakietu, który jest zazwyczaj dostępny w kanale RHEL Extras, nie będzie można wykonać tego działania w EUS. Cykl życia produktu Red Hat jest szczegółowo opisany w [tym miejscu](https://access.redhat.com/support/policy/updates/extras/).

W czasie tego pisania EUS zakończyło się wsparcie dla RHEL < = 7,3. Aby uzyskać więcej informacji, zobacz sekcję "Red Hat Enterprise Linux więcej obsługi dodatków" w [dokumentacji Red Hat](https://access.redhat.com/support/policy/updates/errata/) .
* RHEL 7,4 EUS support zakończyła się 31 sierpnia 2019
* RHEL 7,5 EUS — 30 kwietnia 2020
* RHEL 7,6 EUS support to 31 października 2020
* RHEL 7,7 EUS support to 30 sierpnia 2021

### <a name="switch-a-rhel-vm-to-eus-version-lock-to-a-specific-minor-version"></a>Przełącz maszynę wirtualną RHEL na EUS (Zablokuj wersję na określoną wersję pomocniczą)
Skorzystaj z poniższych instrukcji, aby zablokować maszynę wirtualną RHEL do określonej pomocniczej wersji (Uruchom jako główny):

>[!NOTE]
> Dotyczy to tylko wersji RHEL, dla których EUS jest dostępna. W czasie tego pisania obejmuje to RHEL 7.2-7.7. Więcej szczegółów można znaleźć na stronie [cykl życia Red Hat Enterprise Linux](https://access.redhat.com/support/policy/updates/errata) .

1. Wyłącz repozytoria inne niż EUS:
    ```bash
    yum --disablerepo='*' remove 'rhui-azure-rhel7'
    ```

1. Dodaj repozytoria EUS:
    ```bash
    yum --config='https://rhelimage.blob.core.windows.net/repositories/rhui-microsoft-azure-rhel7-eus.config' install 'rhui-azure-rhel7-eus'
    ```

1. Zablokuj zmienną releasever (Uruchom jako root):
    ```bash
    echo $(. /etc/os-release && echo $VERSION_ID) > /etc/yum/vars/releasever
    ```

    >[!NOTE]
    > Powyższa instrukcja spowoduje zablokowanie wersji pomocniczej RHEL w bieżącej wersji pomocniczej. Wprowadź konkretną wersję pomocniczą, jeśli zamierzasz uaktualnić i zablokować nowszą wersję pomocniczą, która nie jest najnowsza. Na przykład `echo 7.5 > /etc/yum/vars/releasever` zablokuje wersję RHEL do RHEL 7,5

1. Aktualizowanie maszyny wirtualnej RHEL
    ```bash
    sudo yum update
    ```

### <a name="switch-a-rhel-vm-back-to-non-eus-remove-a-version-lock"></a>Przełącz maszynę wirtualną RHEL z powrotem do innej niż EUS (Usuń blokadę wersji)
Uruchom następujący element jako główny:
1. Usuń plik releasever:
    ```bash
    rm /etc/yum/vars/releasever
     ```

1. Wyłącz repozytoria EUS:
    ```bash
    yum --disablerepo='*' remove 'rhui-azure-rhel7-eus'
   ```

1. Konfigurowanie maszyny wirtualnej RHEL
    ```bash
    yum --config='https://rhelimage.blob.core.windows.net/repositories/rhui-microsoft-azure-rhel7.config' install 'rhui-azure-rhel7'
    ```
    
1. Aktualizowanie maszyny wirtualnej RHEL
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

## <a name="azure-rhui-infrastructure"></a>Infrastruktura usługi Azure RHUI


### <a name="update-expired-rhui-client-certificate-on-a-vm"></a>Aktualizacja wygasłego certyfikatu klienta usługi RHUI na maszynie Wirtualnej

Jeśli używasz starszego obrazu maszyny wirtualnej RHEL, na przykład RHEL 7,4 (nazwa URN obrazu: `RedHat:RHEL:7.4:7.4.2018010506`), wystąpią problemy z łącznością z usługą RHUI z powodu wygasłego certyfikatu klienta SSL. Wyświetlany błąd może wyglądać podobnie do _"element równorzędny protokołu SSL odrzucił certyfikat jako wygasły"_ lub _"błąd: nie można pobrać metadanych repozytorium (repomd. xml) dla repozytorium:... Sprawdź swoją ścieżkę i spróbuj ponownie "_ . Aby rozwiązać ten problem, zaktualizuj pakiet klienta RHUI na maszynie wirtualnej przy użyciu następującego polecenia:

```bash
sudo yum update -y --disablerepo='*' --enablerepo='*microsoft*'
```

Alternatywnie uruchomienie `sudo yum update` może także aktualizować pakiet certyfikatów klienta (w zależności od wersji programu RHEL), mimo błędów "wygasły certyfikat SSL", które będą widoczne dla innych repozytoriów. Jeśli ta aktualizacja zakończyła się pomyślnie, należy przywrócić normalne połączenie z innymi repozytoriami RHUI, dzięki czemu będzie można uruchamiać `sudo yum update` pomyślnie.

Jeśli podczas uruchamiania `yum update`wystąpi błąd 404, spróbuj wykonać następujące czynności, aby odświeżyć pamięć podręczną Yum:
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

### <a name="infrastructure-update"></a>Aktualizacja infrastruktury

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

## <a name="next-steps"></a>Następne kroki
* Tworzenie maszyny Wirtualnej z Red Hat Enterprise Linux na podstawie obrazu PAYG Marketplace usługi Azure i używać RHUI hostowanymi na platformie Azure, przejdź do [portalu Azure Marketplace](https://azure.microsoft.com/marketplace/partners/redhat/).
* Aby dowiedzieć się więcej na temat obrazów Red Hat na platformie Azure, przejdź do [strony dokumentacji](./rhel-images.md).
* Informacje o zasadach obsługi systemu Red Hat dla wszystkich wersji programu RHEL można znaleźć na stronie [cykl życia Red Hat Enterprise Linux](https://access.redhat.com/support/policy/updates/errata) .
