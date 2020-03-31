---
title: Infrastruktura aktualizacji Red Hat | Dokumenty firmy Microsoft
description: Dowiedz się więcej o infrastrukturze red hat update dla wystąpień systemu Red Hat Enterprise linux na żądanie na platformie Microsoft Azure
services: virtual-machines-linux
documentationcenter: ''
author: asinn826
manager: BorisB2015
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 02/10/2020
ms.author: alsin
ms.openlocfilehash: aa9fd230f59b5e46576e78beb0436c85449d3c5d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80256916"
---
# <a name="red-hat-update-infrastructure-for-on-demand-red-hat-enterprise-linux-vms-in-azure"></a>Infrastruktura aktualizacji Red Hat dla maszyn wirtualnych red hat enterprise z systemem Linux na platformie Azure
 [Red Hat Update Infrastructure](https://access.redhat.com/products/red-hat-update-infrastructure) (RHUI) umożliwia dostawcom chmury, takim jak Azure, tworzenie kopii lustrzanych zawartości repozytorium hostowanego przez czerwony kapelusz, tworzenie niestandardowych repozytoriów z zawartością specyficzną dla platformy Azure i udostępnianie jej maszynom wirtualnym użytkowników końcowych.

Obrazy płatności zgodnie z rzeczywistymarmie (PAYG) red hat enterprise linux (RHEL) są wstępnie skonfigurowane w celu uzyskania dostępu do platformy Azure RHUI. Nie jest wymagana żadna dodatkowa konfiguracja. Aby uzyskać najnowsze aktualizacje, uruchom `sudo yum update` po wystąpieniu RHEL jest gotowy. Usługa ta jest wliczona w cenę opłat za oprogramowanie RHEL PAYG.

Dodatkowe informacje na temat obrazów RHEL na platformie Azure, w tym zasady publikowania i przechowywania, są dostępne [tutaj](./redhat-images.md).

Informacje na temat zasad obsługi Red Hat dla wszystkich wersji RHEL można znaleźć na stronie [Red Hat Enterprise Linux Life Cycle.](https://access.redhat.com/support/policy/updates/errata)

> [!IMPORTANT]
> RHUI jest przeznaczony tylko dla pay-as-you-go (PAYG) obrazy. W przypadku niestandardowych i złotych obrazów, znanych również jako bring-your-own-subscription (BYOS), system musi być dołączony do RHSM lub Satellite, aby otrzymywać aktualizacje. Zobacz [artykuł Red Hat,](https://access.redhat.com/solutions/253273) aby uzyskać więcej informacji.


## <a name="important-information-about-azure-rhui"></a>Ważne informacje o platformie Azure RHUI

* Azure RHUI to infrastruktura aktualizacji, która obsługuje wszystkie maszyny wirtualne RHEL PAYG utworzone na platformie Azure. Nie wyklucza to zarejestrowania maszyn wirtualnych PAYG RHEL w Menedżerze subskrypcji lub usłudze Satellite lub innym źródle aktualizacji, ale wykonanie tego za pomocą maszyny wirtualnej PAYG spowoduje pośrednie podwójne rozliczenie. Zobacz poniższy punkt, aby uzyskać szczegółowe informacje.
* Dostęp do hostowanego na platformie Azure interfejsu RHUI jest uwzględniony w cenie obrazu RHEL PAYG. Jeśli wyrejestrujesz maszynę wirtualną PAYG RHEL z platformy RHUI hostowanego na platformie Azure, która nie konwertuje maszyny wirtualnej na maszynę wirtualną typu bring-your-own-license (BYOL). Jeśli zarejestrujesz tę samą maszynę wirtualną z innym źródłem aktualizacji, możesz ponieść _pośrednie_ podwójne opłaty. Opłata za oprogramowanie Azure RHEL jest naliczana po raz pierwszy. Opłata jest naliczana po raz drugi za subskrypcje Red Hat, które zostały zakupione wcześniej. Jeśli konsekwentnie trzeba użyć infrastruktury aktualizacji innych niż hostowane na platformie Azure RHUI, należy rozważyć zarejestrowanie się do korzystania z [obrazów RHEL BYOS](./byos.md).

* Obrazy RHEL SAP PAYG na platformie Azure (RHEL dla SAP, RHEL dla SAP HANA i RHEL dla aplikacji biznesowych SAP) są połączone z dedykowanymi kanałami RHUI, które pozostają w określonej wersji pomocniczej RHEL zgodnie z wymaganiami certyfikacji SAP.

* Dostęp do platformy RHUI hostowanej na platformie Azure jest ograniczony do maszyn wirtualnych w [zakresach ip centrum danych platformy Azure.](https://www.microsoft.com/download/details.aspx?id=41653) Jeśli proxy wszystkich ruchu maszyny Wirtualnej za pośrednictwem lokalnej infrastruktury sieciowej, może być konieczne skonfigurowanie tras zdefiniowanych przez użytkownika dla maszyn wirtualnych RHEL PAYG, aby uzyskać dostęp do platformy Azure RHUI. W takim przypadku trasy zdefiniowane przez użytkownika będą musiały zostać dodane dla _wszystkich_ adresów IP RHUI.


## <a name="image-update-behavior"></a>Zachowanie aktualizacji obrazu

Od kwietnia 2019 r. platforma Azure oferuje domyślnie obrazy RHEL połączone z repozytoriami rozszerzonej obsługi aktualizacji (EUS) oraz obrazy RHEL, które są domyślnie połączone z regularnymi repozytoriami (spoza EUS). Więcej informacji na temat RHEL EUS można znaleźć w [dokumentacji cyklu życia wersji](https://access.redhat.com/support/policy/updates/errata) Red Hat i dokumentacji [EUS.](https://access.redhat.com/articles/rhel-eus) Domyślne zachowanie `sudo yum update` będzie się różnić w zależności od tego, który obraz RHEL, który został aprowizowany, ponieważ różne obrazy są połączone z różnymi repozytoriami.

Aby uzyskać pełną listę `az vm image list --publisher redhat --all` obrazów, uruchom przy użyciu interfejsu wiersza polecenia platformy Azure.

### <a name="images-connected-to-non-eus-repositories"></a>Obrazy połączone z repozytoriami spoza EUS

Jeśli aprowizujesz maszynę wirtualną z obrazu RHEL, który jest połączony z repozytoriami spoza EUS, `sudo yum update`po uruchomieniu zostanie uaktualniona do najnowszej wersji pomocniczej RHEL. Na przykład jeśli aprowizujesz maszynę wirtualną z obrazu RHEL `sudo yum update`7.4 PAYG i uruchomisz , skończysz z maszyną wirtualną RHEL 7.7 (najnowszą wersją pomocniczą z rodziny RHEL7).

Obrazy połączone z repozytoriami spoza EUS nie będą zawierać pomocniczego numeru wersji w jednostce SKU. Jednostka SKU jest trzecim elementem w URN (pełna nazwa obrazu). Na przykład wszystkie poniższe obrazy są dołączone do repozytoriów innych niż EUS:

```text
RedHat:RHEL:7-LVM:7.4.2018010506
RedHat:RHEL:7-LVM:7.5.2018081518
RedHat:RHEL:7-LVM:7.6.2019062414
RedHat:RHEL:7-RAW:7.4.2018010506
RedHat:RHEL:7-RAW:7.5.2018081518
RedHat:RHEL:7-RAW:7.6.2019062120
```

Należy zauważyć, że jednostki SKU są 7-LVM lub 7-RAW. Wersja pomocnicza jest wskazana w wersji (czwarty element w URN) tych obrazów.

### <a name="images-connected-to-eus-repositories"></a>Obrazy połączone z repozytoriami EUS

Jeśli aprowizujesz maszynę wirtualną z obrazu RHEL, który jest połączony z repozytoriami EUS, `sudo yum update`nie zostaniesz uaktualniony do najnowszej wersji pomocniczej RHEL po uruchomieniu . Dzieje się tak dlatego, że obrazy podłączone do repozytoriów EUS są również zablokowane w wersji do ich konkretnej wersji pomocniczej.

Obrazy podłączone do repozytoriów EUS będą zawierać numer wersji pomocniczej w jednostce SKU. Na przykład wszystkie poniższe obrazy są dołączone do repozytoriów EUS:

```text
RedHat:RHEL:7.4:7.4.2019062107
RedHat:RHEL:7.5:7.5.2019062018
RedHat:RHEL:7.6:7.6.2019062116
```

## <a name="rhel-eus-and-version-locking-rhel-vms"></a>RHEL EUS i maszyny wirtualne RHEL blokujące wersje

Repozytoria rozszerzonej obsługi aktualizacji (EUS) są dostępne dla klientów, którzy mogą chcieć zablokować swoje maszyny wirtualne RHEL do pewnego niewielkiego wydania RHEL po zainicjowaniu obsługi administracyjnej maszyny wirtualnej. Można zablokować wersję maszyny Wirtualnej RHEL do określonej wersji pomocniczej, aktualizując repozytoria, aby wskazać repozytoria obsługi aktualizacji rozszerzonej. Można również cofnąć operację blokowania wersji EUS.

>[!NOTE]
> EUS nie jest obsługiwany w dodatkach RHEL. Oznacza to, że jeśli instalujesz pakiet, który jest zwykle dostępny na kanale RHEL Extras, nie będziesz mógł tego zrobić podczas eus. Cykl życia produktu Red Hat Extras jest szczegółowo opisany [tutaj.](https://access.redhat.com/support/policy/updates/extras/)

W momencie pisania tego tekstu wsparcie EUS zakończyło się dla RHEL <= 7,4. Zobacz sekcję "Red Hat Enterprise Linux Longer Support Add-Ons" w [dokumentacji Red Hat,](https://access.redhat.com/support/policy/updates/errata/) aby uzyskać więcej informacji.
* RHEL 7.4 Wsparcie EUS kończy się 31 sierpnia 2019 r.
* RHEL 7.5 Wsparcie EUS kończy się 30 kwietnia 2020 r.
* Wsparcie RHEL 7.6 EUS kończy się 31 października 2020 r.
* Wsparcie RHEL 7.7 EUS kończy się 30 sierpnia 2021 r.

### <a name="switch-a-rhel-vm-to-eus-version-lock-to-a-specific-minor-version"></a>Przełączanie maszyny wirtualnej RHEL na EUS (blokada wersji na określoną wersję pomocniczą)
Użyj następujących instrukcji, aby zablokować maszynę wirtualną RHEL do określonego wydania pomocniczego (uruchamianego jako root):

>[!NOTE]
> Dotyczy to tylko wersji RHEL, dla których eus jest dostępny. W momencie pisania tego tekstu obejmuje to RHEL 7.2-7.7. Więcej szczegółów można znaleźć na stronie [Red Hat Enterprise Linux Life Cycle.](https://access.redhat.com/support/policy/updates/errata)

1. Wyłącz repozytoria spoza UE:
    ```bash
    yum --disablerepo='*' remove 'rhui-azure-rhel7'
    ```

1. Dodaj repozytoria EUS:
    ```bash
    yum --config='https://rhelimage.blob.core.windows.net/repositories/rhui-microsoft-azure-rhel7-eus.config' install 'rhui-azure-rhel7-eus'
    ```

1. Zablokuj zmienną `releasever` (uruchom jako root):
    ```bash
    echo $(. /etc/os-release && echo $VERSION_ID) > /etc/yum/vars/releasever
    ```

    >[!NOTE]
    > Powyższa instrukcja zablokuje wydanie pomocnicze RHEL do bieżącego wydania pomocniczego. Wprowadź określoną wersję pomocniczą, jeśli chcesz uaktualnić i zablokować do późniejszej wersji pomocniczej, która nie jest najnowsza. Na przykład, `echo 7.5 > /etc/yum/vars/releasever` zablokuje wersję RHEL do RHEL 7.5

1. Aktualizowanie maszyny wirtualnej RHEL
    ```bash
    sudo yum update
    ```

### <a name="switch-a-rhel-vm-back-to-non-eus-remove-a-version-lock"></a>Przełączanie maszyny wirtualnej RHEL z powrotem na system spoza EUS (usuń blokadę wersji)
Uruchom następujące jako root:
1. Usuń `releasever` plik:
    ```bash
    rm /etc/yum/vars/releasever
     ```

1. Wyłącz repozytoria EUS:
    ```bash
    yum --disablerepo='*' remove 'rhui-azure-rhel7-eus'
   ```

1. Konfigurowanie maszyny Wirtualnej RHEL
    ```bash
    yum --config='https://rhelimage.blob.core.windows.net/repositories/rhui-microsoft-azure-rhel7.config' install 'rhui-azure-rhel7'
    ```

1. Aktualizowanie maszyny wirtualnej RHEL
    ```bash
    sudo yum update
    ```

## <a name="the-ips-for-the-rhui-content-delivery-servers"></a>Serwery IP dla serwerów dostarczania zawartości RHUI

Rhui jest dostępny we wszystkich regionach, w których dostępne są obrazy RHEL na żądanie. Obecnie obejmuje wszystkie regiony publiczne wymienione na stronie [pulpitu nawigacyjnego stanu platformy Azure,](https://azure.microsoft.com/status/) usługi Azure us government i regionów Microsoft Azure Germany.

Jeśli używasz konfiguracji sieciowej do dalszego ograniczania dostępu z maszyn wirtualnych RHEL PAYG, upewnij się, że następujące wiadomości IP mogą `yum update` działać w zależności od środowiska, w które się znajdujesz:


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

## <a name="azure-rhui-infrastructure"></a>Infrastruktura platformy Azure RHUI


### <a name="update-expired-rhui-client-certificate-on-a-vm"></a>Aktualizacja wygasłego certyfikatu klienta RHUI na maszynie wirtualnej

Jeśli używasz starszego obrazu maszyny Wirtualnej RHEL, na przykład RHEL `RedHat:RHEL:7.4:7.4.2018010506`7.4 (obraz URN: ), wystąpią problemy z łącznością z RHUI z powodu wygasłego certyfikatu klienta TLS/SSL. Wyświetlony błąd może wyglądać następująco: _"Element równorzędny SSL odrzucił twój certyfikat jako wygasły"_ lub _"Błąd: Nie można pobrać metadanych repomd.xml) dla repozytorium: ... Sprawdź jego ścieżkę i spróbuj ponownie"_. Aby rozwiązać ten problem, zaktualizuj pakiet klienta RHUI na maszynie wirtualnej za pomocą następującego polecenia:

```bash
sudo yum update -y --disablerepo='*' --enablerepo='*microsoft*'
```

Alternatywnie uruchomienie `sudo yum update` może również zaktualizować pakiet certyfikatów klienta (w zależności od wersji RHEL), pomimo błędów "wygasłego certyfikatu SSL", które zostaną wyświetlone w przypadku innych repozytoriów. Jeśli ta aktualizacja zakończy się pomyślnie, normalna łączność z innymi repozytoriami RHUI powinna zostać przywrócona, dzięki czemu będzie można uruchomić `sudo yum update` pomyślnie.

Jeśli napotkasz błąd 404 `yum update`podczas uruchamiania , spróbuj wykonać następujące czynności, aby odświeżyć pamięć podręczną yum:
```bash
sudo yum clean all;
sudo yum makecache
```

### <a name="troubleshoot-connection-problems-to-azure-rhui"></a>Rozwiązywanie problemów z połączeniem z platformą Azure RHUI
Jeśli wystąpią problemy z połączeniem z platformą Azure RHUI z maszyny wirtualnej Payg usługi Azure RHEL, wykonaj następujące kroki:

1. Sprawdź konfigurację maszyny Wirtualnej dla punktu końcowego rhui platformy Azure:

    1. Sprawdź, `/etc/yum.repos.d/rh-cloud.repo` czy plik zawiera `rhui-[1-3].microsoft.com` odwołanie `baseurl` do `[rhui-microsoft-azure-rhel*]` sekcji pliku. Jeśli tak, używasz nowego programu Azure RHUI.

    1. Jeśli wskazuje lokalizację z następującym `mirrorlist.*cds[1-4].cloudapp.net`wzorcem, wymagana jest aktualizacja konfiguracji. Używasz starej migawki maszyny Wirtualnej i musisz ją zaktualizować, aby wskazywała nową platformę Azure RHUI.

1. Dostęp do platformy RHUI hostowanej na platformie Azure jest ograniczony do maszyn wirtualnych w [zakresach ip centrum danych platformy Azure.](https://www.microsoft.com/download/details.aspx?id=41653)

1. Jeśli używasz nowej konfiguracji, sprawdź, czy maszyna wirtualna łączy się z zakresu adresów IP platformy Azure i nadal nie można połączyć się z platformą Azure RHUI, złożyć sprawę pomocy technicznej z firmą Microsoft lub Red Hat.

### <a name="infrastructure-update"></a>Aktualizacja infrastruktury

We wrześniu 2016 r. wdrożyliśmy zaktualizowaną platformę Azure RHUI. W kwietniu 2017 r. firma Microsoft zamknął stary program Azure RHUI. Jeśli używasz obrazów RHEL PAYG (lub ich migawek) od września 2016 r. lub później, automatycznie łączysz się z nową platformą Azure RHUI. Jeśli jednak masz starsze migawki na maszynach wirtualnych, musisz ręcznie zaktualizować ich konfigurację, aby uzyskać dostęp do interfejsu RHUI platformy Azure, zgodnie z opisem w poniższej sekcji.

Nowe serwery RHUI platformy Azure są wdrażane za pomocą [usługi Azure Traffic Manager.](https://azure.microsoft.com/services/traffic-manager/) W usłudze Traffic Manager pojedynczy punkt końcowy (rhui-1.microsoft.com) może być używany przez dowolną maszynę wirtualną, niezależnie od regionu.

### <a name="manual-update-procedure-to-use-the-azure-rhui-servers"></a>Procedura ręcznej aktualizacji w celu użycia serwerów RHUI platformy Azure
Ta procedura jest przewidziana wyłącznie w celach informacyjnych. Obrazy RHEL PAYG mają już poprawną konfigurację do łączenia się z platformą Azure RHUI. Aby ręcznie zaktualizować konfigurację w celu użycia serwerów RHUI platformy Azure, wykonaj następujące kroki:

- Dla RHEL 6:
  ```bash
  yum --config='https://rhelimage.blob.core.windows.net/repositories/rhui-microsoft-azure-rhel6.config' install 'rhui-azure-rhel6'
  ```

- Dla RHEL 7:
  ```bash
  yum --config='https://rhelimage.blob.core.windows.net/repositories/rhui-microsoft-azure-rhel7.config' install 'rhui-azure-rhel7'
  ```

- Dla RHEL 8:
    1. Tworzenie pliku konfiguracyjnego:
        ```bash
        vi rhel8.config
        ```
    1. Dodaj następującą zawartość do pliku konfiguracyjnego:
        ```bash
        [rhui-microsoft-azure-rhel8]
        name=Microsoft Azure RPMs for Red Hat Enterprise Linux 8
        baseurl=https://rhui-1.microsoft.com/pulp/repos/microsoft-azure-rhel8 https://rhui-2.microsoft.com/pulp/repos/microsoft-azure-rhel8 https://rhui-3.microsoft.com/pulp/repos/microsoft-azure-rhel8
        enabled=1
        gpgcheck=1
        gpgkey=https://rhelimage.blob.core.windows.net/repositories/RPM-GPG-KEY-microsoft-azure-release sslverify=1
        ```
    1. Zapisz plik i uruchom następujące polecenie:
        ```bash
        dnf --config rhel8.config install 'rhui-azure-rhel8'
        ```
    1. Aktualizowanie maszyny Wirtualnej
        ```bash
        sudo dnf update
        ```


## <a name="next-steps"></a>Następne kroki
* Aby utworzyć maszynę wirtualną red hat enterprise z systemem Linux na podstawie obrazu PAYG w portalu Azure Marketplace i użyć hostowanego na platformie Azure interfejsu RHUI, przejdź do [portalu Azure Marketplace](https://azure.microsoft.com/marketplace/partners/redhat/).
* Aby dowiedzieć się więcej o obrazach Czerwonego Kapelusza na platformie Azure, przejdź do [strony dokumentacji](./redhat-images.md).
* Informacje na temat zasad obsługi Red Hat dla wszystkich wersji RHEL można znaleźć na stronie [Red Hat Enterprise Linux Life Cycle.](https://access.redhat.com/support/policy/updates/errata)
