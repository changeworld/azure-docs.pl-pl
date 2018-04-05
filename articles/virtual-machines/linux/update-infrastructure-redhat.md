---
title: Red Hat aktualizacji infrastruktury | Dokumentacja firmy Microsoft
description: Dowiedz się więcej o Red Hat aktualizacji infrastruktury dla wystąpień Red Hat Enterprise Linux na żądanie w systemie Microsoft Azure
services: virtual-machines-linux
documentationcenter: ''
author: BorisB2015
manager: timlt
editor: ''
ms.assetid: f495f1b4-ae24-46b9-8d26-c617ce3daf3a
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 09/10/2017
ms.author: borisb
ms.openlocfilehash: fe354c6c9415c1525527dd6b95d1b3a481b5a2de
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/03/2018
---
# <a name="red-hat-update-infrastructure-for-on-demand-red-hat-enterprise-linux-vms-in-azure"></a>Red Hat infrastruktury aktualizacji na żądanie Red Hat Enterprise Linux w maszynach wirtualnych na platformie Azure
 [Red Hat aktualizacji infrastruktury](https://access.redhat.com/products/red-hat-update-infrastructure) (RHUI) umożliwia dostawcom usług chmury, takich jak Azure zawartości hostowanej Red Hat repozytorium, Utwórz niestandardowe repozytoria specyficzne dla platformy Azure zawartości i był dostępny dla maszyn wirtualnych przez użytkownika końcowego.

Red Hat Enterprise Linux (RHEL) płatność za rzeczywiste użycie (między) pochodzą wstępnie skonfigurowany dostęp Azure RHUI. Dodatkowa konfiguracja nie jest potrzebna. Aby uzyskać najnowsze aktualizacje, należy uruchomić `sudo yum update` po wystąpieniu RHEL jest gotowy. Ta usługa jest częścią oprogramowania między RHEL opłat.

## <a name="important-information-about-azure-rhui"></a>Ważne informacje o Azure RHUI
* Azure RHUI aktualnie obsługuje tylko najnowszej wersji pomocniczej w każdej rodziny RHEL (RHEL6 lub RHEL7). Aby uaktualnić wystąpienie RHEL maszyny Wirtualnej podłączone do RHUI do najnowszej wersji pomocniczej, uruchom `sudo yum update`.

    Na przykład udostępnić Maszynę wirtualną z obrazu między 7.2 RHEL i uruchom `sudo yum update`, na końcu RHEL 7.4 maszyny Wirtualnej (Najnowsza wersja pomocnicza rodziny RHEL7).

    Aby uniknąć tego zachowania, należy utworzyć własny obraz zgodnie z opisem w [tworzenie i przekazywanie maszyny wirtualnej z systemem Red Hat Azure](redhat-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) artykułu. Należy nawiązać infrastruktury inną aktualizację ([bezpośrednio na Red Hat treści serwerów dostarczania](https://access.redhat.com/solutions/253273) lub [serwera Red Hat satelity](https://access.redhat.com/products/red-hat-satellite)).

* Dostęp do RHUI hostowanymi na platformie Azure jest uwzględniony w cenie obrazu między RHEL. Jeśli wyrejestrować między RHEL maszyny Wirtualnej z RHUI hostowanymi na platformie Azure maszyny wirtualnej nie konwersji na typ bring your właścicielem licencji (BYOL) maszyny wirtualnej. Jeśli zarejestrujesz tej samej maszyny Wirtualnej z innego źródła aktualizacji, użytkownik może pociągnąć za sobą _pośrednie_ dwukrotnie opłat. Są naliczane opłaty oprogramowania Azure RHEL po raz pierwszy. Są naliczane opłaty za drugim razem Red Hat subskrypcji, które zostały zakupione wcześniej. Jeśli potrzebujesz spójne używanie infrastruktury aktualizacji niż RHUI hostowanymi na platformie Azure, należy wziąć pod uwagę tworzenia i wdrażania obrazów (BYOL typu). Ten proces jest opisany w [tworzenie i przekazywanie maszyny wirtualnej z systemem Red Hat Azure](redhat-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

* Dwie klasy RHEL między obrazów na platformie Azure (RHEL dla SAP HANA) i RHEL dla aplikacji biznesowych SAP są podłączone do dedykowanych kanałów RHUI, które pozostają na określonych wersja pomocnicza RHEL, co jest wymagane dla programu SAP certyfikacji. 

* Dostęp do RHUI hostowanymi na platformie Azure jest ograniczona do maszyn wirtualnych w ramach [zakresy IP centrum danych Azure](https://www.microsoft.com/download/details.aspx?id=41653). Jeśli jesteś pośredniczenie cały ruch maszyny Wirtualnej przy użyciu lokalnej infrastruktury sieci, może być konieczne konfigurowanie tras zdefiniowanych przez użytkownika dla maszyn wirtualnych między RHEL dostępu Azure RHUI do.

### <a name="the-ips-for-the-rhui-content-delivery-servers"></a>Adresy IP dla serwerów dostarczania zawartości RHUI

RHUI jest dostępna we wszystkich regionach, gdzie RHEL obrazów na żądanie są dostępne. Obecnie obejmuje wszystkie regiony publicznego wymienione na [pulpitu nawigacyjnego platformy Azure stan](https://azure.microsoft.com/status/) strony, Azure instytucji rządowych Stanów Zjednoczonych i regiony platformy Azure w Niemczech firmy Microsoft. 

Jeśli używasz konfiguracji sieci bardziej ograniczyć dostęp z maszyn wirtualnych między RHEL, upewnij się, że następujące adresy IP, są dozwolone dla `yum update` do pracy w zależności od środowiska korzystania: 

```
# Azure Global
13.91.47.76
40.85.190.91
52.187.75.218
52.174.163.213
52.237.203.198

# Azure US Government
13.72.186.193

# Azure Germany
51.5.243.77
51.4.228.145
```

## <a name="rhui-azure-infrastructure-update"></a>RHUI Azure aktualizacji infrastruktury

We wrześniu 2016 roku wdrożyliśmy zaktualizowane RHUI Azure. Kwietnia 2017 możemy zamknąć starych RHUI Azure. Jeśli używany był obrazy między RHEL (lub migawki ich) od września 2016 lub nowszy, automatycznie nawiązywane nowe RHUI Azure. Jeśli jednak masz starszą migawek na maszyny wirtualne, należy ręcznie zaktualizować ich konfigurację, aby uzyskać dostęp Azure RHUI, zgodnie z opisem w poniższej sekcji.

Nowe serwery Azure RHUI zostały wdrożone za pomocą [usługi Azure Traffic Manager](https://azure.microsoft.com/services/traffic-manager/). W Menedżerze ruchu jeden punkt końcowy (rhui 1.microsoft.com) mogą posłużyć żadnej maszyny Wirtualnej, niezależnie od tego regionu. 

### <a name="troubleshoot-connection-problems-to-azure-rhui"></a>Rozwiązywanie problemów z połączeniem do Azure RHUI
Jeśli występują problemy z połączeniem z maszyny Wirtualnej Azure RHEL między Azure RHUI, wykonaj następujące kroki:

1. Sprawdź konfigurację maszyny Wirtualnej dla punktu końcowego Azure RHUI:

    a. Sprawdź, czy `/etc/yum.repos.d/rh-cloud.repo` plik zawiera odwołanie do `rhui-[1-3].microsoft.com` w `baseurl` z `[rhui-microsoft-azure-rhel*]` sekcji pliku. Jeśli tak, organizacja używa nowego RHUI Azure.

    b. Wskazuje na lokalizacji przy użyciu następującego wzorca `mirrorlist.*cds[1-4].cloudapp.net`, wymagana jest aktualizacja konfiguracji. Używasz starego migawki maszyny Wirtualnej, i należy zaktualizować go, aby wskazywały nowy RHUI Azure.

2. Dostęp do RHUI hostowanymi na platformie Azure jest ograniczona do maszyn wirtualnych w ramach [zakresy IP centrum danych Azure] (https://www.microsoft.com/download/details.aspx?id=41653).
 
3. Jeśli używasz nowa konfiguracja została zweryfikowana nawiązuje połączenie z zakresu adresów IP Azure maszyny Wirtualnej i nadal nie można połączyć się RHUI Azure, plików sprawy pomocy technicznej firmy Microsoft lub Red Hat.

### <a name="manual-update-procedure-to-use-the-azure-rhui-servers"></a>Procedura ręcznej aktualizacji do korzystania z serwerów Azure RHUI
Ta procedura jest podana jedynie do celów referencyjnych. Obrazy między RHEL już prawidłowej konfiguracji do nawiązania połączenia Azure RHUI. Aby ręcznie zaktualizować konfigurację, aby użyć serwerów Azure RHUI, wykonaj następujące kroki:

1. Pobierz podpis klucza publicznego, za pośrednictwem curl.

   ```bash
   curl -o RPM-GPG-KEY-microsoft-azure-release https://download.microsoft.com/download/9/D/9/9d945f05-541d-494f-9977-289b3ce8e774/microsoft-sign-public.asc 
   ```

2. Sprawdź ważność pobranego klucza.

   ```bash
   gpg --list-packets --verbose < RPM-GPG-KEY-microsoft-azure-release
   ```

3. Sprawdź dane wyjściowe, a następnie sprawdź `keyid` i `user ID packet`.

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

4. Zainstaluj klucz publiczny.

   ```bash
   sudo install -o root -g root -m 644 RPM-GPG-KEY-microsoft-azure-release /etc/pki/rpm-gpg
   sudo rpm --import /etc/pki/rpm-gpg/RPM-GPG-KEY-microsoft-azure-release
   ```

5. Pobierz, sprawdź i zainstaluj klienta Menedżera pakietów RPM (RPM).
    
    >[!NOTE]
    >Zmiana wersji pakietu. Jeśli ręcznie nawiązać Azure RHUI można znaleźć najnowszą wersję pakietu klienta dla każdej rodziny RHEL aprowizując najnowsze obraz w galerii.
  
   a. Pobierz. 
   
    - Dla RHEL 6:
        ```bash
        curl -o azureclient.rpm https://rhui-1.microsoft.com/pulp/repos/microsoft-azure-rhel6/rhui-azure-rhel6-2.1-32.noarch.rpm 
        ```
    
    - Dla RHEL 7:
        ```bash
        curl -o azureclient.rpm https://rhui-1.microsoft.com/pulp/repos/microsoft-azure-rhel7/rhui-azure-rhel7-2.1-19.noarch.rpm  
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

6. Po zakończeniu upewnij się, że masz dostęp Azure RHUI z maszyny Wirtualnej.

## <a name="next-steps"></a>Następne kroki
Aby utworzyć maszynę Wirtualną Red Hat Enterprise Linux z obrazu Azure Marketplace między i użyj RHUI hostowanymi na platformie Azure, przejdź do [portalu Azure Marketplace](https://azure.microsoft.com/marketplace/partners/redhat/). 
