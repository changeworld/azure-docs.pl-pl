---
title: Rozwiązanie Azure VMware by CloudSimple — tworzenie kopii zapasowych maszyn wirtualnych obciążenia w chmurze prywatnej przy użyciu rozwiązania Veeam
description: W tym artykule opisano, jak można&wyw.
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/16/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 3262841efb9109b1de24fe501ea0a7bea0dd612d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77025133"
---
# <a name="back-up-workload-vms-on-cloudsimple-private-cloud-using-veeam-br"></a>Tworzenie kopii zapasowych maszyn wirtualnych z obciążeniem w chmurze CloudSimple private cloud przy użyciu rozwiązania Veeam B&R

W tym przewodniku opisano, jak można wy&w.

## <a name="about-the-veeam-back-up-and-recovery-solution"></a>Informacje o rozwiązaniu veeam do odzyskiwania kopii zapasowych i odzyskiwania

Rozwiązanie Veeam obejmuje następujące komponenty.

**Serwer kopii zapasowych**

Serwer kopii zapasowych to serwer windowsowy (VM), który służy jako centrum sterowania veeam i wykonuje następujące funkcje: 

* Współrzędne zadań tworzenia kopii zapasowych, replikacji, weryfikacji odzyskiwania i przywracania
* Steruje planowaniem zadań i alokacją zasobów
* Umożliwia konfigurowanie i zarządzanie składnikami infrastruktury kopii zapasowych oraz określanie ustawień globalnych infrastruktury kopii zapasowych

**Serwery proxy**

Serwery proxy są instalowane między serwerem kopii zapasowych a innymi składnikami infrastruktury kopii zapasowej. Zarządzają następującymi funkcjami:

* Pobieranie danych maszyn wirtualnych z magazynu produkcyjnego
* Kompresja
* Deduplikacja
* Szyfrowanie
* Przesyłanie danych do repozytorium kopii zapasowych

**Repozytorium kopii zapasowych**

Repozytorium kopii zapasowych to miejsce przechowywania, w którym veeam przechowuje pliki kopii zapasowych, kopie maszyn wirtualnych i metadane replikowanych maszyn wirtualnych.  Repozytorium może być serwerem Windows lub Linux z dyskami lokalnymi (lub zamontowanym systemem NFS/SMB) lub urządzeniem deduplikacji magazynu sprzętowego.

### <a name="veeam-deployment-scenarios"></a>Scenariusze wdrażania veeam
Możesz korzystać z platformy Azure, aby zapewnić repozytorium kopii zapasowych i miejsce docelowe magazynu dla długoterminowej kopii zapasowych i archiwizacji. Cały ruch sieciowy kopii zapasowej między maszynami wirtualnymi w chmurze prywatnej i repozytorium kopii zapasowych na platformie Azure odbywa się za pośrednictwem łącza o dużej przepustowości i małych opóźnieniach. Ruch replikacji w różnych regionach odbywa się przez wewnętrzną sieć zaplecza platformy Azure, co obniża koszty przepustowości dla użytkowników.

**Wdrożenie podstawowe**

W środowiskach o pojemności kopii zapasowej mniejszej niż 30 TB cloudSimple zaleca następującą konfigurację:

* Serwer kopii zapasowych veeam i serwer proxy zainstalowane na tej samej maszynie wirtualnej w chmurze prywatnej.
* Podstawowe repozytorium kopii zapasowych oparte na systemie Linux na platformie Azure skonfigurowane jako miejsce docelowe dla zadań tworzenia kopii zapasowych.
* `azcopy`służy do kopiowania danych z podstawowego repozytorium kopii zapasowych do kontenera obiektów blob platformy Azure, który jest replikowany do innego regionu.

![Podstawowe scenariusze wdrażania](media/veeam-basicdeployment.png)

**Zaawansowane wdrażanie**

W przypadku środowisk z więcej niż 30 TB do utworzenia kopii zapasowej CloudSimple zaleca następującą konfigurację:

* Jeden serwer proxy na węzeł w klastrze vSAN, zgodnie z zaleceniami firmy Veeam.
* Podstawowe repozytorium kopii zapasowych oparte na systemie Windows w chmurze prywatnej do buforowania pięciu dni danych w celu szybkiego przywracania.
* Repozytorium kopii zapasowych systemu Linux na platformie Azure jako miejsce docelowe zadań kopiowania kopii zapasowych dla dłuższego przechowywania czasu przechowywania. To repozytorium powinno być skonfigurowane jako repozytorium kopii zapasowych skalowanych w poziomie.
* `azcopy`służy do kopiowania danych z podstawowego repozytorium kopii zapasowych do kontenera obiektów blob platformy Azure, który jest replikowany do innego regionu.

![Podstawowe scenariusze wdrażania](media/veeam-advanceddeployment.png)

Na poprzednim rysunku należy zauważyć, że serwer proxy kopii zapasowej jest maszyną wirtualną z dostępem hot add do dysków maszyn wirtualnych obciążenia w magazynie danych vSAN. Veeam korzysta z trybu transportu zastępczego kopii zapasowej urządzenia wirtualnego dla vSAN.

## <a name="requirements-for-veeam-solution-on-cloudsimple"></a>Wymagania dotyczące rozwiązania Veeam w usłudze CloudSimple

Rozwiązanie Veeam wymaga wykonania następujących czynności:

* Zapewnij własne licencje Veeam.
* Wdrażanie rozwiązania Veeam i zarządzanie nim w celu tworzenia kopii zapasowych obciążeń działających w chmurze prywatnej CloudSimple.

To rozwiązanie zapewnia pełną kontrolę nad narzędziem do tworzenia kopii zapasowych veeam i oferuje możliwość korzystania z natywnego interfejsu Veeam lub wtyczki Veeam vCenter do zarządzania zadaniami tworzenia kopii zapasowych maszyn wirtualnych.

Jeśli jesteś istniejącym użytkownikiem rozwiązania Veeam, możesz pominąć tę sekcję dotyczącą składników rozwiązania Veeam i przejść bezpośrednio do [scenariuszy wdrażania rozwiązania Veeam](#veeam-deployment-scenarios).

## <a name="install-and-configure-veeam-backups-in-your-cloudsimple-private-cloud"></a>Instalowanie i konfigurowanie kopii zapasowych veeam w chmurze CloudSimple Private Cloud

W poniższych sekcjach opisano sposób instalowania i konfigurowania rozwiązania do tworzenia kopii zapasowych veeam dla chmury CloudSimple Private Cloud.

Proces wdrażania składa się z następujących kroków:

1. [Interfejs użytkownika vCenter: konfigurowanie usług infrastruktury w chmurze prywatnej](#vcenter-ui-set-up-infrastructure-services-in-your-private-cloud)
2. [Portal CloudSimple: Konfigurowanie sieci private cloud dla rozwiązania Veeam](#cloudsimple-private-cloud-set-up-private-cloud-networking-for-veeam)
3. [CloudSimple portal: eskalacji uprawnień](#cloudsimple-private-cloud-escalate-privileges-for-cloudowner)
4. [Portal Azure: Łączenie sieci wirtualnej z chmurą prywatną](#azure-portal-connect-your-virtual-network-to-the-private-cloud)
5. [Witryna Azure portal: tworzenie repozytorium kopii zapasowych na platformie Azure](#azure-portal-connect-your-virtual-network-to-the-private-cloud)
6. [Witryna Azure portal: konfigurowanie magazynu obiektów blob platformy Azure pod kątem długoterminowego przechowywania danych](#configure-azure-blob-storage-for-long-term-data-retention)
7. [Interfejs użytkownika vCenter private cloud: zainstaluj veeam B&R](#vcenter-console-of-private-cloud-install-veeam-br)
8. [Veeam Console: Konfigurowanie oprogramowania Veeam Backup & Recovery](#veeam-console-install-veeam-backup-and-recovery-software)
9. [Portal CloudSimple: konfigurowanie uprawnień veeam dostępu i deeskalacji](#cloudsimple-portal-set-up-veeam-access-and-de-escalate-privileges)

### <a name="before-you-begin"></a>Przed rozpoczęciem

Przed rozpoczęciem wdrażania veeam wymagane są następujące czynności:

* Subskrypcja platformy Azure należąca do Ciebie
* Wstępnie utworzona grupa zasobów platformy Azure
* Sieć wirtualna platformy Azure w ramach subskrypcji
* Konto usługi Azure Storage
* [Chmura prywatna](create-private-cloud.md) utworzona przy użyciu portalu CloudSimple.  

Na etapie wdrażania potrzebne są następujące elementy:

* Szablony VMware dla systemu Windows do zainstalowania veeam (takie jak Windows Server 2012 R2 - 64-bitowy obraz)
* Jedna dostępna sieć VLAN zidentyfikowana dla sieci kopii zapasowych
* CIDR podsieci, która ma być przypisana do sieci kopii zapasowej
* Nośniki instalowalne Veeam 9.5 u3 (ISO) przesłane do magazynu danych vSAN w chmurze prywatnej

### <a name="vcenter-ui-set-up-infrastructure-services-in-your-private-cloud"></a>Interfejs użytkownika vCenter: konfigurowanie usług infrastruktury w chmurze prywatnej

Skonfiguruj usługi infrastruktury w chmurze prywatnej, aby ułatwić zarządzanie obciążeniami i narzędziami.

* Można dodać zewnętrznego dostawcę tożsamości zgodnie z opisem w [yłącz źródła tożsamości vCenter, aby używać usługi Active Directory,](set-vcenter-identity.md) jeśli ma zastosowanie którakolwiek z następujących opcji:

  * Chcesz zidentyfikować użytkowników z lokalnej usługi Active Directory (AD) w chmurze prywatnej.
  * Chcesz skonfigurować usługę AD w chmurze prywatnej dla wszystkich użytkowników.
  * Chcesz użyć usługi Azure AD.
* Aby zapewnić usługi wyszukiwania adresów IP, zarządzania adresami IP i rozpoznawania nazw dla obciążeń w chmurze prywatnej, skonfiguruj serwer DHCP i DNS zgodnie z opisem w [aplikacji i obciążeniach DNS i DHCP w chmurze CloudSimple Private Cloud](dns-dhcp-setup.md).

### <a name="cloudsimple-private-cloud-set-up-private-cloud-networking-for-veeam"></a>CloudSimple Private Cloud: Konfigurowanie sieci private cloud dla rozwiązania Veeam

Uzyskaj dostęp do portalu CloudSimple, aby skonfigurować sieć private cloud dla rozwiązania Veeam.

Utwórz sieć VLAN dla sieci kopii zapasowych i przypisz jej cidr podsieci. Aby uzyskać instrukcje, zobacz [Tworzenie sieci/podsieci i zarządzanie nimi](create-vlan-subnet.md).

Tworzenie reguł zapory między podsiecią zarządzania a siecią kopii zapasowych w celu umożliwienia ruchu sieciowego na portach używanych przez firmę Veeam. Zobacz temat Używane [porty](https://helpcenter.veeam.com/docs/backup/vsphere/used_ports.html?ver=95)veeam . Aby uzyskać instrukcje dotyczące tworzenia reguł zapory, zobacz [Konfigurowanie tabel i reguł zapory](firewall.md).

Poniższa tabela zawiera listę portów.

| Ikona | Opis | Ikona | Opis |
| ------------ | ------------- | ------------ | ------------- |
| Serwer kopii zapasowych  | vCenter  | HTTPS / TCP  | 443 |
| Serwer kopii zapasowych <br> *Wymagane do wdrażania składników veeam backup & replikacja* | Serwer proxy kopii zapasowej  | Protokół TCP/UDP  | 135, 137 do 139 i 445 |
    | Serwer kopii zapasowych   | DNS  | UDP  | 53  | 
    | Serwer kopii zapasowych   | Serwer powiadomień o aktualizacji veeam  | TCP  | 80  | 
    | Serwer kopii zapasowych   | Serwer aktualizacji licencji Veeam  | TCP  | 443  | 
    | Serwer proxy kopii zapasowej   | vCenter |   |   | 
    | Serwer proxy kopii zapasowej  | Repozytorium kopii zapasowych systemu Linux   | TCP  | 22  | 
    | Serwer proxy kopii zapasowej  | Repozytorium kopii zapasowych systemu Windows  | TCP  | 49152 - 65535   | 
    | Repozytorium kopii zapasowych  | Serwer proxy kopii zapasowej  | TCP  | 2500 -5000  | 
    | Źródło repozytorium kopii zapasowych<br> *Używane do wykonywania kopii zapasowych zadań*  | Docelowe repozytorium kopii zapasowych  | TCP  | 2500 - 5000  | 

Tworzenie reguł zapory między podsiecią obciążenia a siecią kopii zapasowych zgodnie z opisem w [obszarze Konfigurowanie tabel i reguł zapory](firewall.md).  W przypadku tworzenia kopii zapasowych i przywracania z uwzględnieniem aplikacji należy otworzyć [dodatkowe porty](https://helpcenter.veeam.com/docs/backup/vsphere/used_ports.html?ver=95) na maszynach wirtualnych obciążenia obsługujących określone aplikacje.

Domyślnie CloudSimple udostępnia łącze 1Gbps ExpressRoute. W przypadku większych rozmiarów środowiska może być wymagane łącze o wyższej przepustowości. Skontaktuj się z pomocą techniczną platformy Azure, aby uzyskać więcej informacji na temat łączy o wyższej przepustowości.

Aby kontynuować konfigurację, potrzebujesz identyfikatora URI klucza autoryzacji i obwodu równorzędnego oraz dostępu do subskrypcji platformy Azure.  Te informacje są dostępne na stronie Połączenie sieci wirtualnej w portalu CloudSimple. Aby uzyskać instrukcje, zobacz [Uzyskiwanie informacji dotyczących komunikacji równorzędnej dla sieci wirtualnej platformy Azure do połączenia CloudSimple](virtual-network-connection.md). Jeśli masz jakiekolwiek problemy z uzyskaniem informacji, [skontaktuj się z pomocą techniczną](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

### <a name="cloudsimple-private-cloud-escalate-privileges-for-cloudowner"></a>CloudSimple Private Cloud: eskalacja uprawnień dla właściciela chmury

Domyślny użytkownik "cloudowner" nie ma wystarczających uprawnień w centrum vCenter private cloud, aby zainstalować program VEEAM, więc uprawnienia vCenter użytkownika muszą zostać eskalowane. Aby uzyskać więcej informacji, zobacz [Eskalacja uprawnień](escalate-private-cloud-privileges.md).

### <a name="azure-portal-connect-your-virtual-network-to-the-private-cloud"></a>Portal Azure: Łączenie sieci wirtualnej z chmurą prywatną

Połącz swoją sieć wirtualną z chmurą prywatną, postępując zgodnie z instrukcjami zawartymi w [usłudze Azure Virtual Network Connection przy użyciu usługi ExpressRoute](azure-expressroute-connection.md).

### <a name="azure-portal-create-a-backup-repository-vm"></a>Portal Azure: tworzenie maszyny Wirtualnej repozytorium kopii zapasowych

1. Utwórz standardową maszynę wirtualną D2 v3 z (2 procesory wirtualne i 8 GB pamięci).
2. Wybierz obraz oparty na centos 7.4.
3. Konfigurowanie sieciowej grupy zabezpieczeń (NSG) dla maszyny Wirtualnej. Sprawdź, czy maszyna wirtualna nie ma publicznego adresu IP i nie jest osiągalna z publicznego Internetu.
4. Utwórz konto użytkownika i hasło dla nowej maszyny Wirtualnej. Aby uzyskać instrukcje, zobacz [Tworzenie maszyny wirtualnej systemu Linux w witrynie Azure portal](../virtual-machines/linux/quick-create-portal.md).
5. Utwórz standardowy dysk twardy GiB 1x512 i podłącz go do maszyny wirtualnej repozytorium.  Aby uzyskać instrukcje, zobacz [Jak dołączyć zarządzany dysk danych do maszyny Wirtualnej systemu Windows w witrynie Azure portal](../virtual-machines/windows/attach-managed-disk-portal.md).
6. [Utwórz wolumin XFS na dysku zarządzanym](https://www.digitalocean.com/docs/volumes/how-to/). Zaloguj się do maszyny Wirtualnej przy użyciu wcześniej wymienionych poświadczeń. Wykonaj następujący skrypt, aby utworzyć wolumin logiczny, dodać dysk do niego, utworzyć [partycję](https://www.digitalocean.com/docs/volumes/how-to/partition/) systemu plików XFS i [zainstalować](https://www.digitalocean.com/docs/volumes/how-to/mount/) partycję pod ścieżką /backup1.

    Przykładowy skrypt:

    ```
    sudo pvcreate /dev/sdc
    sudo vgcreate backup1 /dev/sdc
    sudo lvcreate -n backup1 -l 100%FREE backup1
    sudo mkdir -p /backup1
    sudo chown veeamadmin /backup1
    sudo chmod 775 /backup1
    sudo mkfs.xfs -d su=64k -d sw=1 -f /dev/mapper/backup1-backup1
    sudo mount -t xfs /dev/mapper/backup1-backup1 /backup1
    ```

7. Uwidacznianie /backup1 jako punktu instalacji systemu plików NFS na serwerze kopii zapasowej Veeam, który jest uruchomiony w chmurze prywatnej. Aby uzyskać instrukcje, zobacz artykuł Digital Ocean [Jak skonfigurować uchwyt NFS na CentOS 6](https://www.digitalocean.com/community/tutorials/how-to-set-up-an-nfs-mount-on-centos-6). Tej nazwy udziału NFS należy używać podczas konfigurowania repozytorium kopii zapasowych na serwerze kopii zapasowych Veeam.

8. Skonfiguruj reguły filtrowania w sieciowej sieciowej sieciowej dla maszyny Wirtualnej repozytorium kopii zapasowych, aby jawnie zezwolić na cały ruch sieciowy do i z maszyny Wirtualnej.

> [!NOTE]
> Veeam Backup & Replication używa protokołu SSH do komunikowania się z repozytoriami kopii zapasowych systemu Linux i wymaga narzędzia SCP w repozytoriach systemu Linux. Sprawdź, czy demon SSH jest poprawnie skonfigurowany i czy protokół SCP jest dostępny na hoście systemu Linux.

### <a name="configure-azure-blob-storage-for-long-term-data-retention"></a>Konfigurowanie magazynu obiektów blob platformy Azure pod kątem długoterminowego przechowywania danych

1. Utwórz konto magazynu ogólnego przeznaczenia (GPv2) typu standardowego i kontener obiektów blob, zgodnie z opisem w klipie wideo firmy Microsoft [Wprowadzenie do usługi Azure Storage](https://azure.microsoft.com/resources/videos/get-started-with-azure-storage).
2. Utwórz kontener magazynu platformy Azure, zgodnie z opisem w [odwołaniu do kontenera Utwórz.](https://docs.microsoft.com/rest/api/storageservices/create-container)
2. Pobierz `azcopy` narzędzie wiersza polecenia dla Linuksa od firmy Microsoft. W centos 7.5 można użyć następujących poleceń w osłonie bash.

    ```
    wget -O azcopy.tar.gz https://aka.ms/downloadazcopylinux64
    tar -xf azcopy.tar.gz
    sudo ./install.sh
    sudo yum -y install libunwind.x86_64
    sudo yum -y install icu
    ```

3. Polecenie `azcopy` służy do kopiowania plików kopii zapasowych do kontenera obiektów blob i z niego.  Szczegółowe polecenia można znaleźć w sekcji [Transfer danych z azcopy na linuksie.](../storage/common/storage-use-azcopy-linux.md)

### <a name="vcenter-console-of-private-cloud-install-veeam-br"></a>konsola vCenter private cloud: zainstaluj veeam B&R

Uzyskaj dostęp do vCenter z chmury prywatnej, aby utworzyć konto usługi Veeam, zainstalować veeam B&R 9.5 i skonfigurować veeam przy użyciu konta usługi.

1. Utwórz nową rolę o nazwie "Veeam Backup Role" i przypisz jej niezbędne uprawnienia zgodnie z zaleceniami firmy Veeam. Aby uzyskać szczegółowe informacje, zobacz temat Veeam [Wymagane uprawnienia](https://helpcenter.veeam.com/docs/backup/vsphere/required_permissions.html?ver=95).
2. Utwórz nową grupę użytkowników "Veeam User Group" w vCenter i przypisz jej "Rolę kopii zapasowej Veeam".
3. Utwórz nowego użytkownika "Konto usługi Veeam" i dodaj go do "Grupy użytkowników Veeam".

    ![Tworzenie konta usługi Veeam](media/veeam-vcenter01.png)

4. Utwórz grupę portów rozproszonych w centrum vCenter przy użyciu sieci kopii zapasowych sieci VLAN. Aby uzyskać szczegółowe informacje, zobacz wideo VMware [Tworzenie grupy portów rozproszonych w kliencie sieci Web vSphere](https://www.youtube.com/watch?v=wpCd5ZbPOpA).
5. Tworzenie maszyn wirtualnych dla serwerów kopii zapasowych i serwerów proxy Veeam w vCente, zgodnie z [wymaganiami systemowymi veeam.](https://helpcenter.veeam.com/docs/backup/vsphere/system_requirements.html?ver=95) Można użyć systemu Windows 2012 R2 lub Linux. Aby uzyskać więcej informacji, zobacz [Wymagania dotyczące korzystania z repozytoriów kopii zapasowych systemu Linux](https://www.veeam.com/kb2216).
6. Zainstaluj instalowalną funkcję Veeam ISO jako urządzenie CDROM na maszynie wirtualnej serwera kopii zapasowych Veeam.
7. Korzystając z sesji RDP na komputerze z systemem Windows 2012 R2 (cel instalacji veeam), [zainstaluj veeam B&R 9.5u3](https://helpcenter.veeam.com/docs/backup/vsphere/install_vbr.html?ver=95) na maszynie wirtualnej systemu Windows 2012 R2.
8. Znajdź wewnętrzny adres IP maszyny Wirtualnej serwera kopii zapasowych Veeam i skonfiguruj adres IP jako statyczny na serwerze DHCP. Dokładne kroki wymagane do wykonania tej czynności zależą od serwera DHCP. Na przykład <a href="https://www.netgate.com/docs/pfsense/dhcp/dhcp-server.html" target="_blank">statyczne mapowania DHCP</a> w artykule Netgate wyjaśniają, jak skonfigurować serwer DHCP przy użyciu routera pfSense.

### <a name="veeam-console-install-veeam-backup-and-recovery-software"></a>Konsola Veeam: instalowanie oprogramowania do tworzenia kopii zapasowych i odzyskiwania rozwiązania Veeam

Za pomocą konsoli Veeam skonfiguruj oprogramowanie do tworzenia kopii zapasowych i odzyskiwania rozwiązania Veeam. Aby uzyskać szczegółowe informacje, zobacz [Rozwiązanie Veeam Backup & Replication v9 — Instalacja i wdrażanie](https://www.youtube.com/watch?v=b4BqC_WXARk).

1. Dodaj VMware vSphere jako środowisko serwera zarządzanego. Po wyświetleniu monitu podaj poświadczenia konta usługi Veeam utworzonego na początku [programu vCenter Console of Private Cloud: Zainstaluj veeam B&R](#vcenter-console-of-private-cloud-install-veeam-br).

    * Użyj ustawień domyślnych dla kontroli obciążenia i domyślnych ustawień zaawansowanych.
    * Ustaw lokalizację serwera instalacji jako serwer kopii zapasowej.
    * Zmień lokalizację kopii zapasowej konfiguracji serwera Veeam na repozytorium zdalne.

2. Dodaj serwer systemu Linux na platformie Azure jako repozytorium kopii zapasowych.

    * Użyj ustawień domyślnych do kontroli obciążenia i ustawień zaawansowanych. 
    * Ustaw lokalizację serwera instalacji jako serwer kopii zapasowej.
    * Zmień lokalizację kopii zapasowej konfiguracji serwera Veeam na repozytorium zdalne.

3. Włącz szyfrowanie kopii zapasowej konfiguracji przy użyciu **ustawień kopii zapasowej konfiguracji>**.

4. Dodaj maszynę wirtualną z systemem Windows server jako serwer proxy dla środowiska VMware. Korzystanie z "Reguł ruchu drogowego" dla serwera proxy, szyfrowanie danych kopii zapasowej przez sieć.

5. Konfigurowanie zadań tworzenia kopii zapasowych.
    * Aby skonfigurować zadania tworzenia kopii zapasowych, postępuj zgodnie z instrukcjami dotyczącymi [tworzenia zadania tworzenia kopii zapasowej](https://www.youtube.com/watch?v=YHxcUFEss4M).
    * Włącz szyfrowanie plików kopii zapasowych w obszarze **Ustawienia zaawansowane > Magazyn**.

6. Konfigurowanie zadań kopiowania kopii zapasowej.

    * Aby skonfigurować zadania kopiowania kopii zapasowej, postępuj zgodnie z instrukcjami zawartymi w klipie wideo [Tworzenie zadania kopiowania kopii zapasowej](https://www.youtube.com/watch?v=LvEHV0_WDWI&t=2s).
    * Włącz szyfrowanie plików kopii zapasowych w obszarze **Ustawienia zaawansowane > Magazyn**.

### <a name="cloudsimple-portal-set-up-veeam-access-and-de-escalate-privileges"></a>Portal CloudSimple: konfigurowanie uprawnień veeam dostępu i deeskalacji
Utwórz publiczny adres IP dla serwera kopii zapasowych i odzyskiwania veeam. Aby uzyskać instrukcje, zobacz [Przydzielanie publicznych adresów IP](public-ips.md).

Utwórz regułę zapory, aby umożliwić serwerowi kopii zapasowej Veeam utworzenie połączenia wychodzącego z witryną veeam w celu pobierania aktualizacji/poprawek na port TCP 80. Aby uzyskać instrukcje, zobacz [Konfigurowanie tabel i reguł zapory](firewall.md).

Aby deeskalować uprawnienia, zobacz [De-eskalacji uprawnień](escalate-private-cloud-privileges.md#de-escalate-privileges).

## <a name="references"></a>Dokumentacja

### <a name="cloudsimple-references"></a>Odwołania cloudsimple

* [Tworzenie chmury prywatnej](create-private-cloud.md)
* [Tworzenie sieci/podsieci i zarządzanie nimi](create-vlan-subnet.md)
* [Źródła tożsamości vCenter](set-vcenter-identity.md)
* [Ustawienia DNS i DHCP obciążenia](dns-dhcp-setup.md)
* [Podwyższanie poziomu uprawnień](escalate-privileges.md)
* [Konfigurowanie tabel i reguł zapory](firewall.md)
* [Uprawnienia chmury prywatnej](learn-private-cloud-permissions.md)
* [Przydzielanie publicznych adresów IP](public-ips.md)

### <a name="veeam-references"></a>Odwołania do veeam

* [Używane porty](https://helpcenter.veeam.com/docs/backup/vsphere/used_ports.html?ver=95)
* [Wymagane uprawnienia](https://helpcenter.veeam.com/docs/backup/vsphere/required_permissions.html?ver=95)
* [Wymagania systemowe](https://helpcenter.veeam.com/docs/backup/vsphere/system_requirements.html?ver=95)
* [Instalowanie replikacji & kopii zapasowej veeam](https://helpcenter.veeam.com/docs/backup/vsphere/install_vbr.html?ver=95)
* [Wymagane moduły i uprawnienia do obsługi flr i repozytorium multi-os dla systemu Linux](https://www.veeam.com/kb2216)
* [Veeam Backup & Replication v9 - Instalacja i wdrażanie - Wideo](https://www.youtube.com/watch?v=b4BqC_WXARk)
* [Veeam v9 Tworzenie zadania tworzenia kopii zapasowej - Wideo](https://www.youtube.com/watch?v=YHxcUFEss4M)
* [Veeam v9 Tworzenie zadania kopiowania kopii zapasowej - Wideo](https://www.youtube.com/watch?v=LvEHV0_WDWI&t=2s)

### <a name="azure-references"></a>Odwołania do platformy Azure

* [Konfigurowanie bramy sieci wirtualnej dla usługi ExpressRoute przy użyciu portalu Azure](../expressroute/expressroute-howto-add-gateway-portal-resource-manager.md)
* [Podłączanie sieci wirtualnej do obwodu — inna subskrypcja](../expressroute/expressroute-howto-linkvnet-portal-resource-manager.md#connect-a-vnet-to-a-circuit---different-subscription)
* [Tworzenie maszyny wirtualnej systemu Linux w witrynie Azure portal](../virtual-machines/linux/quick-create-portal.md)
* [Jak dołączyć zarządzany dysk danych do maszyny Wirtualnej systemu Windows w witrynie Azure portal](../virtual-machines/windows/attach-managed-disk-portal.md)
* [Wprowadzenie do usługi Azure Storage — wideo](https://azure.microsoft.com/resources/videos/get-started-with-azure-storage)
* [Tworzenie kontenera](https://docs.microsoft.com/rest/api/storageservices/create-container)
* [Transferowanie danych za pomocą narzędzia AzCopy w systemie Linux](../storage/common/storage-use-azcopy-linux.md)

### <a name="vmware-references"></a>Odwołania do VMware

* [Tworzenie rozproszonej grupy portów w kliencie sieci Web vSphere — wideo](https://www.youtube.com/watch?v=wpCd5ZbPOpA)

### <a name="other-references"></a>Inne referencje

* [Tworzenie woluminu XFS na dysku zarządzanym — RedHat](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/storage_administration_guide/ch-xfs)
* [Jak skonfigurować uchwyt NFS na CentOS 7 - HowToForge](https://www.howtoforge.com/nfs-server-and-client-on-centos-7)
* [Konfigurowanie serwera DHCP — Netgate](https://www.netgate.com/docs/pfsense/dhcp/dhcp-server.html)
