---
title: Przewodnik Szybki Start — tworzenie maszyny Wirtualnej VMware na chmurę prywatną
description: Opisuje sposób tworzenia i maszyny Wirtualnej VMware na chmurę prywatną CloudSimple
author: sharaths-cs
ms.author: b-shsury
ms.date: 06/03/19
ms.topic: article
ms.service: vmware
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 217154578ad11755cd658ff3b106cfbe266277ab
ms.sourcegitcommit: 1289f956f897786090166982a8b66f708c9deea1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/17/2019
ms.locfileid: "67154614"
---
# <a name="create-vmware-virtual-machines-on-your-private-cloud"></a>Tworzenie maszyn wirtualnych VMware w chmurze prywatnej

Tworzenie maszyn wirtualnych w chmurze prywatnej, Rozpocznij od uzyskiwania dostępu do portalu CloudSimple w witrynie Azure portal.

## <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

Zaloguj się do witryny Azure Portal pod adresem [https://portal.azure.com](https://portal.azure.com).

## <a name="access-the-cloudsimple-portal"></a>Uzyskiwanie dostępu do portalu CloudSimple

1. Wybierz pozycję **Wszystkie usługi**.
2. Wyszukaj **usług CloudSimple**.
3. Wybierz usługę CloudSimple, na którym chcesz utworzyć chmury prywatnej.
4. Z **Przegląd** kliknij **przejdź do portalu CloudSimple** aby otworzyć nową kartę przeglądarki dla portalu CloudSimple.  Jeśli zostanie wyświetlony monit, zaloguj się przy użyciu subskrypcji platformy Azure Zaloguj się w poświadczeniach.  

    ![Uruchom CloudSimple portal](media/launch-cloudsimple-portal.png)

## <a name="launch-vcenter-web-ui"></a>Uruchom interfejs użytkownika sieci web vCenter

Teraz możesz uruchamiać vCenter do skonfigurowania zasad i maszyn wirtualnych.

Aby uzyskać dostęp vCenter, uruchomić z poziomu portalu CloudSimple. Na stronie głównej w obszarze **typowe zadania związane z**, kliknij przycisk **Uruchom klienta vSphere**.  Wybierz chmurę prywatną, a następnie kliknij przycisk **Uruchom klienta vSphere** w chmurze prywatnej.

   ![Uruchom klienta vSphere](media/launch-vcenter-from-cloudsimple-portal.png)

## <a name="upload-an-iso-or-vsphere-template"></a>Przekaż szablon ISO lub vSphere

> [!WARNING]
> Przekazywanie ISO można użyć w kliencie vSphere HTML5.  Za pomocą Flash klienta może spowodować wystąpienie błędu.

1. Uzyskaj ISO lub vSphere szablon, który chcesz przekazać vcenter, aby utworzyć maszynę wirtualną i jest dostępna w systemie lokalnym.

2. W programie vCenter, kliknij przycisk **dysku** ikonę i wybierz pozycję **vsanDatastore**. Kliknij przycisk **pliki** a następnie kliknij przycisk **nowy Folder**.

    ![vCenter ISO](media/vcenter-create-folder.png)

3. Utwórz folder do przechowywania plików ISO.

4. Przejdź do nowego folderu, które są tworzone, a następnie kliknij przycisk **Przekaż pliki**. Postępuj zgodnie z wyświetlanymi instrukcjami, aby przekazać plik ISO.

## <a name="create-a-virtual-machine-in-vcenter"></a>Utwórz maszynę wirtualną w programie vCenter

1. W programie vCenter, kliknij przycisk **hosty i klastry** ikony.

2. Kliknij prawym przyciskiem myszy **obciążenia** i wybierz **nowej maszyny wirtualnej**.
    
    ![Tworzenie maszyny wirtualnej](media/create-vcenter-virtual-machine-01.png)

3. Wybierz **tworzenia nowej maszyny wirtualnej** i kliknij przycisk **dalej**.

    ![Kreator nowej maszyny wirtualnej](media/create-vcenter-virtual-machine-02.png)

4. Nazwa maszyny, wybierz opcję **obciążenie maszyny Wirtualnej** folder, a następnie kliknij przycisk **dalej**.

    ![Wybierz nazwę i folder](media/create-vcenter-virtual-machine-03.png)

5. Wybierz **obciążenia** zasób obliczeniowy, a następnie kliknij przycisk **dalej**.

    ![Wybierz zasób obliczeniowy](media/create-vcenter-virtual-machine-04.png)

6. Wybierz **vsanDatastore** i kliknij przycisk **dalej**.

    ![Wybieranie magazynu](media/create-vcenter-virtual-machine-05.png)

7. Pozostaw domyślnie zaznaczoną zgodności ESXi 6.5, a następnie kliknij przycisk **dalej**.

    ![Wybierz opcję zgodności](media/create-vcenter-virtual-machine-06.png)

8. Wybierz system operacyjny gościa ISO dla maszyny wirtualnej, a następnie kliknij przycisk **dalej**.

    ![Dostosowywanie systemu operacyjnego gościa](media/create-vcenter-virtual-machine-07.png)

9. Wybierz dysk twardy i Opcje sieci. Nowy dysk CD/DVD, dysk, wybierz **plik Datastore ISO**.  Jeśli chcesz zezwolić na ruch z publiczny adres IP do tej maszyny wirtualnej, wybierz sieć jako **maszyny wirtualnej 1**.

    ![Wybieranie sprzętu dostosowywania](media/create-vcenter-virtual-machine-08.png)

10. Zostanie otwarte okno zaznaczenia. Wybierz plik, który został wcześniej przekazany do folderu Obrazy ISO i szablony, a następnie kliknij przycisk **OK**.

    ![Wybierz obraz ISO](media/create-vcenter-virtual-machine-10.png)

11. Przejrzyj ustawienia, a następnie kliknij przycisk **OK** do utworzenia maszyny wirtualnej.

    ![Zapoznaj się z opcjami](media/create-vcenter-virtual-machine-11.png)

Maszyna wirtualna jest teraz widoczny na obciążenie zasobów obliczeniowych i jest gotowa do użycia. 

![Nową maszynę wirtualną w programie vCenter](media/create-vcenter-virtual-machine-12.png)

Podstawowa konfiguracja została zakończona. Aby zacząć korzystać z chmury prywatnej, podobnie jak skorzystać z infrastruktury lokalnej maszyny wirtualnej.

W kolejnych sekcjach zawierają dodatkowe informacje o konfiguracji DNS i DHCP serwerów dla chmury prywatnej obciążeń i modyfikowanie domyślnej konfiguracji sieci.

## <a name="add-users-and-identity-sources-to-vcenter-optional"></a>Dodawanie użytkowników i źródła tożsamości do vCenter (opcjonalnie)

CloudSimple przypisuje domyślne konto użytkownika vCenter przy użyciu nazwy użytkownika **cloudowner@cloudsimple.local** . Dodatkowe konto konfiguracja nie jest wymagana dla Ciebie rozpocząć pracę.  CloudSimple zwykle przypisuje Administratorzy, uprawnienia, których potrzebują do wykonywania zwykłych operacji.  Konfigurowanie sieci lokalnej usługi active directory lub Azure AD jako [źródła tożsamości dodatkowe](https://docs.azure.cloudsimple.com/set-vcenter-identity/) w chmurze prywatnej.

## <a name="create-a-dns-and-dhcp-server-optional"></a>Utwórz serwer DHCP i DNS (opcjonalnie)

Aplikacje i obciążenia w środowisku chmury prywatnej wymaga rozpoznawania nazw i usługi DHCP do wyszukiwania i przypisywania adresów IP. Odpowiednie infrastruktury DNS i DHCP są wymagane do świadczenia tych usług. Można skonfigurować maszynę wirtualną w programie vCenter, aby zapewnić te usługi w środowisku chmury prywatnej.

### <a name="prerequisites"></a>Wymagania wstępne

* Grupy portów rozproszonych za pomocą sieci VLAN skonfigurowane

* Konfigurowanie lokalnych lub serwery DNS internetowego trasy

* Szablon maszyny wirtualnej lub ISO do utworzenia maszyny wirtualnej

Poniższe łącza zapewniają wskazówki dotyczące konfigurowania serwerów DHCP i DNS w systemie Linux i Windows.

### <a name="linux-based-dns-server-setup"></a>Konfiguracja serwera DNS opartą na systemie Linux

Linux oferuje różne pakiety do konfigurowania serwerów DNS.  Oto link do instrukcje dotyczące konfigurowania serwera DNS POWIĄŻ typu open source.

[Konfiguracja przykładu](https://www.digitalocean.com/community/tutorials/how-to-configure-bind-as-a-private-network-dns-server-on-centos-7)

### <a name="windows-based-setup"></a>Instalatora opartego na Windows

Te artykuły Microsoft opisano sposób konfigurowania serwera Windows jako serwer DNS i serwer DHCP.
<br>
[Windows Server jako serwer DNS](https://docs.microsoft.com/windows-server/networking/dns/dns-top)

[Windows Server jako serwer DHCP](https://docs.microsoft.com/windows-server/networking/technologies/dhcp/dhcp-top)

## <a name="customize-networking-configuration-optional"></a>Dostosowywanie konfiguracji sieci (opcjonalnie)

Strony sieci w portalu CloudSimple umożliwiają określenie konfiguracji zapory, tabele i publiczne adresy IP dla maszyn wirtualnych.

### <a name="allocate-public-ips"></a>Przydzielić publiczne adresy IP

1. Przejdź do **sieci > publiczny adres IP** w portalu CloudSimple.
2. Kliknij przycisk **Przydziel publiczny adres IP**.
3. Wprowadź nazwę identyfikującą wpis adresu IP.
4. Wybierz lokalizację, w chmurze prywatnej.
5. Za pomocą suwaka, aby zmienić limit czasu bezczynności, w razie potrzeby.
6. Wprowadź adres IP lokalnego, dla którego chcesz przypisać publiczny adres IP.
7. Wprowadź skojarzonej nazwy DNS, w razie potrzeby.
8. Kliknij przycisk **Gotowe**.

    ![Publiczny adres IP](media/quick-create-pc-public-ip.png)

Rozpoczyna się zadanie alokacji publicznego adresu IP. Można sprawdzić stan zadania **działania > zadania** strony. Po zakończeniu alokacji nowy wpis jest wyświetlany na stronie publicznych adresów IP.

Maszyny wirtualnej, do którego ten adres IP musi być zamapowana musi skonfigurować adres lokalny, określone powyżej. Procedury, aby skonfigurować adres IP jest specyficzne dla systemu operacyjnego maszyny wirtualnej. W dokumentacji systemu operacyjnego maszyny wirtualnej dla prawidłowe procedury.

#### <a name="example"></a>Przykład

Na przykład poniżej przedstawiono szczegóły systemu Ubuntu 16.04.

Dodaj metody statycznej do konfiguracji inet rodziny adresów, w pliku ```/etc/network/interfaces```. Zmień wartości adresów, maskę sieci i bramy. W tym przykładzie użyto interfejsu eth0, wewnętrzny adres IP 192.168.24.10, adres bramy 192.168.24.1 i masek podsieci 255.255.255.0. 

Edytuj ```interfaces``` pliku.

```
sudo vi /etc/network/interfaces
```

Aktualizuj następujących sekcji w ```interfaces``` pliku.

```
auto eth0
iface eth0 inet static
address 192.168.24.10
netmask 255.255.255.0
gateway 192.168.24.1
dns-nameservers 8.8.8.8
dns-domain acme.com
dns-search acme.com
```

Wyłącz interfejs.

```
sudo ifdown eth0
```

Ponownie włączyć interfejs.

```
sudo ifup eth0
```

Domyślnie cały ruch przychodzący z Internetu jest **odmowa**. Jeśli chcesz otworzyć innego portu, należy utworzyć [tabeli zapory](https://docs.azure.cloudsimple.com/firewall/).

Po skonfigurowaniu wewnętrznego adresu IP jako statycznego adresu IP, sprawdź, czy możesz docierać do Internetu z poziomu maszyny wirtualnej.

```
ping 8.8.8.8
```

Upewnij się, że możesz połączyć maszynę wirtualną z Internetu przy użyciu publicznego adresu IP.

Upewnij się, że odpowiednie reguły zapory (iptable) na maszynie wirtualnej nie są przeszkodą w porcie 80 dla ruchu przychodzącego.

```
netstat -an | grep 80
```

Uruchom serwer http, która nasłuchuje na porcie 80.
       
```
python2.7 -m SimpleHTTPServer 80
```

lub

```
python3 -m http.server 80
```

Uruchom przeglądarkę na pulpicie, aby wskazywał na port 80 dla publicznego adresu IP, przejrzyj pliki na maszynę wirtualną. 

### <a name="default-cloudsimple-firewall-rules-for-public-ip"></a>Domyślne reguły zapory CloudSimple dla publicznego adresu IP

* Ruch w sieci VPN: Cały ruch między (z i do) sieci VPN i obciążenia sieci i sieci zarządzania jest dozwolone.
* Ruch wewnętrzny chmury prywatnej: Cały ruch wschód zachód od (z/do) obciążenie sieci i sieci zarządzania (jak pokazano powyżej) jest dozwolony.
* Ruch internetowy:
    * Cały ruch przychodzący z Internetu nie jest możliwy obciążenie sieci i sieci zarządzania.
    * Cały ruch wychodzący do Internetu z obciążenia sieci lub sieci zarządzania jest dozwolone.

Można również zmodyfikować sposób, w jaki ruch jest zabezpieczony, za pomocą funkcji reguły zapory. Aby uzyskać więcej informacji, zobacz [Konfigurowanie tabel zapory i reguł](https://docs.azure.cloudsimple.com/firewall/).

## <a name="install-solutions-optional"></a>Instalowanie rozwiązania (opcjonalnie)
Można zainstalować rozwiązania w chmurze prywatnej CloudSimple do umożliwiają pełne wykorzystywanie zalet chmury prywatne środowisko vCenter. Możesz skonfigurować kopii zapasowych, odzyskiwania po awarii, replikacji i innych funkcji, aby chronić maszyny wirtualne. Przykłady obejmują Menedżera odzyskiwania lokacji oprogramowania VMware (VMware SRM) i kopia zapasowa Veeam i replikacji.

Aby zainstalować rozwiązania, należy zażądać dodatkowych uprawnień przez ograniczony okres. Zobacz [eskalacji uprawnień wobec](https://docs.azure.cloudsimple.com/escalate-private-cloud-privileges).

## <a name="next-steps"></a>Kolejne kroki

* [Używanie maszyn wirtualnych VMware na platformie Azure](quickstart-create-vmware-virtual-machine.md)
* [Nawiązać połączenie z siecią lokalną przy użyciu usługi Azure ExpressRoute](https://docs.azure.cloudsimple.com/on-premises-connection)
* [Konfigurowanie bramy sieci VPN w sieci CloudSimple](https://docs.azure.cloudsimple.com/vpn-gateway)
