---
title: Szybki Start — Tworzenie maszyny wirtualnej VMware w chmurze prywatnej
description: Opisuje sposób tworzenia i maszyny wirtualnej VMware w chmurze prywatnej CloudSimple
author: sharaths-cs
ms.author: b-shsury
ms.date: 06/03/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 7d21035fc3e9e80344264b9fde21820162f376d3
ms.sourcegitcommit: c8a102b9f76f355556b03b62f3c79dc5e3bae305
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/06/2019
ms.locfileid: "68816712"
---
# <a name="create-vmware-virtual-machines-on-your-private-cloud"></a>Tworzenie maszyn wirtualnych VMware w chmurze prywatnej

Aby utworzyć maszyny wirtualne w chmurze prywatnej, Zacznij od uzyskania dostępu do portalu CloudSimple z poziomu Azure Portal.

## <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

Zaloguj się do witryny Azure Portal pod adresem [https://portal.azure.com](https://portal.azure.com).

## <a name="access-the-cloudsimple-portal"></a>Uzyskiwanie dostępu do portalu CloudSimple

1. Wybierz pozycję **Wszystkie usługi**.
2. Wyszukaj **usługi CloudSimple Services**.
3. Wybierz usługę CloudSimple, w której chcesz utworzyć chmurę prywatną.
4. Na stronie **Przegląd** kliknij pozycję **Przejdź do portalu CloudSimple** , aby otworzyć nową kartę przeglądarki dla portalu CloudSimple.  Jeśli zostanie wyświetlony monit, zaloguj się przy użyciu poświadczeń logowania na platformie Azure.  

    ![Uruchom Portal CloudSimple](media/launch-cloudsimple-portal.png)

## <a name="launch-vcenter-web-ui"></a>Uruchamianie interfejsu użytkownika vCenter sieci Web

Teraz możesz uruchomić program vCenter, aby skonfigurować maszyny wirtualne i zasady.

Aby uzyskać dostęp do programu vCenter, Zacznij od portalu CloudSimple. Na stronie głównej w obszarze **typowe zadania**kliknij pozycję **Uruchom program vSphere Client**.  Wybierz chmurę prywatną, a następnie kliknij pozycję **Uruchom klienta vSphere** w chmurze prywatnej.

   ![Uruchom klienta vSphere](media/launch-vcenter-from-cloudsimple-portal.png)

## <a name="upload-an-iso-or-vsphere-template"></a>Przekaż szablon ISO lub vSphere

> [!WARNING]
> W przypadku przekazywania obrazu ISO należy użyć klienta HTML5 vSphere.  Korzystanie z programu Flash Client może spowodować wystąpienie błędu.

1. Uzyskaj szablon ISO lub vSphere, który chcesz przekazać do programu vCenter, aby utworzyć maszynę wirtualną i udostępnić ją w systemie lokalnym.

2. W programie vCenter kliknij ikonę **dysk** i wybierz pozycję **vsanDatastore**. Kliknij pozycję **pliki** , a następnie kliknij pozycję **Nowy folder**.

    ![serwer vCenter ISO](media/vcenter-create-folder.png)

3. Utwórz folder do przechowywania plików ISO.

4. Przejdź do utworzonego nowego folderu, a następnie kliknij pozycję **Przekaż pliki**. Postępuj zgodnie z instrukcjami wyświetlanymi na ekranie, aby przekazać plik ISO.

## <a name="create-a-virtual-machine-in-vcenter"></a>Tworzenie maszyny wirtualnej w programie vCenter

1. W programie vCenter kliknij ikonę **hosty i klastry** .

2. Kliknij prawym przyciskiem myszy pozycję **obciążenie** i wybierz pozycję **Nowa maszyna wirtualna**.
    
    ![Tworzenie maszyny wirtualnej](media/create-vcenter-virtual-machine-01.png)

3. Wybierz pozycję **Utwórz nową maszynę wirtualną** , a następnie kliknij przycisk **dalej**.

    ![Kreator nowej maszyny wirtualnej](media/create-vcenter-virtual-machine-02.png)

4. Nazwij maszynę, wybierz folder **obciążenia maszyny wirtualnej** , a następnie kliknij przycisk **dalej**.

    ![Wybierz nazwę i folder](media/create-vcenter-virtual-machine-03.png)

5. Wybierz zasób obliczeniowy **obciążenia** , a następnie kliknij przycisk **dalej**.

    ![Wybierz zasób obliczeniowy](media/create-vcenter-virtual-machine-04.png)

6. Wybierz pozycję **vsanDatastore** , a następnie kliknij przycisk **dalej**.

    ![Wybieranie magazynu](media/create-vcenter-virtual-machine-05.png)

7. Zachowaj domyślny wybór zgodności z ESXi 6,5 i kliknij przycisk **dalej**.

    ![Wybierz zgodność](media/create-vcenter-virtual-machine-06.png)

8. Wybierz system operacyjny gościa dla maszyny wirtualnej, a następnie kliknij przycisk **dalej**.

    ![Dostosowywanie systemu operacyjnego gościa](media/create-vcenter-virtual-machine-07.png)

9. Wybierz opcje dysk twardy i sieć. W przypadku nowej stacji dysków CD/DVD wybierz pozycję **plik ISO magazynu**danych.  Jeśli chcesz zezwolić na ruch z publicznego adresu IP do tej maszyny wirtualnej, wybierz sieć jako maszynę wirtualną **-1**.

    ![Wybierz dostosowanie sprzętu](media/create-vcenter-virtual-machine-08.png)

10. Zostanie otwarte okno wyboru. Wybierz plik, który został wcześniej przekazany do folderu obrazów ISO i templates, a następnie kliknij przycisk **OK**.

    ![Wybierz ISO](media/create-vcenter-virtual-machine-10.png)

11. Przejrzyj ustawienia i kliknij przycisk **OK** , aby utworzyć maszynę wirtualną.

    ![Przejrzyj opcje](media/create-vcenter-virtual-machine-11.png)

Maszyna wirtualna jest teraz dodawana do zasobów obliczeniowych obciążenia i jest gotowa do użycia. 

![Nowa maszyna wirtualna w programie vCenter](media/create-vcenter-virtual-machine-12.png)

Konfiguracja podstawowa została ukończona. Możesz zacząć korzystać z chmury prywatnej podobnej do sposobu korzystania z lokalnej infrastruktury maszyn wirtualnych.

Kolejne sekcje zawierają opcjonalne informacje dotyczące konfigurowania serwerów DNS i DHCP na potrzeby obciążeń chmury prywatnej i modyfikowania domyślnej konfiguracji sieci.

## <a name="add-users-and-identity-sources-to-vcenter-optional"></a>Dodawanie użytkowników i źródeł tożsamości do programu vCenter (opcjonalnie)

CloudSimple przypisuje domyślne konto użytkownika vCenter z nazwą **cloudowner@cloudsimple.local** użytkownika. Do rozpoczęcia pracy nie jest wymagana żadna dodatkowa konfiguracja konta.  CloudSimple zwykle przypisuje administratorom uprawnienia potrzebne do wykonywania normalnych operacji.  Skonfiguruj lokalną usługę Active Directory lub usługę Azure AD jako [dodatkowe źródło tożsamości](https://docs.azure.cloudsimple.com/set-vcenter-identity/) w chmurze prywatnej.

## <a name="create-a-dns-and-dhcp-server-optional"></a>Tworzenie serwera DNS i DHCP (opcjonalnie)

Aplikacje i obciążenia działające w środowisku chmury prywatnej wymagają rozpoznawania nazw i usług DHCP do wyszukiwania i przypisywania adresów IP. Aby zapewnić te usługi, wymagana jest właściwa infrastruktura DHCP i DNS. Można skonfigurować maszynę wirtualną w programie vCenter, aby udostępnić te usługi w środowisku chmury prywatnej.

### <a name="prerequisites"></a>Wymagania wstępne

* Grupa portów rozproszonych z konfiguracją sieci VLAN

* Routing skonfigurowany do lokalnego lub internetowego serwera DNS

* Szablon maszyny wirtualnej lub plik ISO do utworzenia maszyny wirtualnej

Poniższe linki zawierają wskazówki dotyczące konfigurowania serwerów DHCP i DNS w systemach Linux i Windows.

### <a name="linux-based-dns-server-setup"></a>Konfiguracja serwera DNS z systemem Linux

System Linux oferuje różne pakiety do konfigurowania serwerów DNS.  Poniżej znajduje się link do instrukcji dotyczących konfigurowania serwera DNS powiązania typu open source.

[Przykładowa konfiguracja](https://www.digitalocean.com/community/tutorials/how-to-configure-bind-as-a-private-network-dns-server-on-centos-7)

### <a name="windows-based-setup"></a>Konfiguracja oparta na systemie Windows

W tych artykułach firmy Microsoft opisano sposób konfigurowania serwera z systemem Windows jako serwera DNS i serwera DHCP.
<br>
[Serwer z systemem Windows jako serwer DNS](https://docs.microsoft.com/windows-server/networking/dns/dns-top)

[Serwer z systemem Windows jako serwer DHCP](https://docs.microsoft.com/windows-server/networking/technologies/dhcp/dhcp-top)

## <a name="customize-networking-configuration-optional"></a>Dostosuj konfigurację sieci (opcjonalnie)

Strony sieci w portalu CloudSimple umożliwiają określenie konfiguracji tabel zapory i publicznych adresów IP dla maszyn wirtualnych.

### <a name="allocate-public-ips"></a>Przydziel publiczne adresy IP

1. Przejdź do **sieci > publiczny adres IP** w portalu CloudSimple.
2. Kliknij pozycję **Przydziel publiczny adres IP**.
3. Wprowadź nazwę identyfikującą wpis adresu IP.
4. Wybierz lokalizację chmury prywatnej.
5. Użyj suwaka, aby zmienić limit czasu bezczynności w razie potrzeby.
6. Wprowadź lokalny adres IP, dla którego chcesz przypisać publiczny adres IP.
7. W razie potrzeby wprowadź skojarzoną nazwę DNS.
8. Kliknij przycisk **Gotowe**.

    ![Publiczny adres IP](media/quick-create-pc-public-ip.png)

Zostanie rozpoczęte zadanie alokowania publicznego adresu IP. Stan zadania można sprawdzić na stronie **zadania > zadań** . Po zakończeniu alokacji nowy wpis zostanie wyświetlony na stronie publiczne adresy IP.

Maszyna wirtualna, do której należy zamapować ten adres IP, musi być skonfigurowana przy użyciu adresu lokalnego określonego powyżej. Procedura konfigurowania adresu IP jest specyficzna dla systemu operacyjnego maszyny wirtualnej. Aby uzyskać poprawną procedurę, zapoznaj się z dokumentacją systemu operacyjnego maszyny wirtualnej.

#### <a name="example"></a>Przykład

Na przykład poniżej przedstawiono szczegóły dotyczące Ubuntu 16,04.

Dodaj statyczną metodę do konfiguracji rodziny adresów inet w pliku ```/etc/network/interfaces```. Zmień wartości Address, mask i Gateway. W tym przykładzie używamy interfejsu eth0, wewnętrznego adresu IP 192.168.24.10, adresu bramy 192.168.24.1 i karty sieciowej 255.255.255.0. 

```interfaces``` Edytuj plik.

```
sudo vi /etc/network/interfaces
```

Zaktualizuj następującą sekcję w ```interfaces``` pliku.

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

Ponownie włącz interfejs.

```
sudo ifup eth0
```

Domyślnie cały ruch przychodzący z Internetu jest odrzucany. Jeśli chcesz otworzyć inny port, Utwórz [tabelę zapory](https://docs.azure.cloudsimple.com/firewall/).

Po skonfigurowaniu wewnętrznego adresu IP jako statycznego adresu IP Sprawdź, czy można nawiązać połączenie z Internetem z poziomu maszyny wirtualnej.

```
ping 8.8.8.8
```

Sprawdź, czy możesz nawiązać połączenie z maszyną wirtualną z Internetu przy użyciu publicznego adresu IP.

Upewnij się, że wszystkie reguły zapory (iptable) na maszynie wirtualnej nie blokują portu 80 przychodzącego.

```
netstat -an | grep 80
```

Uruchom serwer http, który nasłuchuje na porcie 80.
       
```
python2.7 -m SimpleHTTPServer 80
```

lub

```
python3 -m http.server 80
```

Uruchom przeglądarkę na pulpicie i wskaż port 80 dla publicznego adresu IP, aby przeglądać pliki na maszynie wirtualnej. 

### <a name="default-cloudsimple-firewall-rules-for-public-ip"></a>Domyślne reguły zapory CloudSimple dla publicznego adresu IP

* Ruch sieci VPN: Cały ruch między (od/do) sieci VPN i wszystkich sieci obciążeń i sieci zarządzania jest dozwolony.
* Wewnętrzny ruch w chmurze prywatnej: Wszystkie ruch wschodni wschód między (od/do) sieci obciążeń i sieć zarządzania (pokazany powyżej) są dozwolone.
* Ruch internetowy:
    * Cały ruch przychodzący z Internetu jest odrzucany do sieci obciążeń i sieci zarządzania.
    * Cały ruch wychodzący do Internetu z sieci obciążeń lub sieci zarządzania jest dozwolony.

Można również zmodyfikować sposób zabezpieczania ruchu przy użyciu funkcji reguły zapory. Aby uzyskać więcej informacji, zobacz [Konfigurowanie tabel i reguł zapory](https://docs.azure.cloudsimple.com/firewall/).

## <a name="install-solutions-optional"></a>Instalowanie rozwiązań (opcjonalnie)
Możesz zainstalować rozwiązania w chmurze prywatnej CloudSimple, aby w pełni wykorzystać możliwości środowiska vCenter w chmurze prywatnej. Można skonfigurować kopie zapasowe, odzyskiwanie po awarii, replikację i inne funkcje do ochrony maszyn wirtualnych. Przykłady obejmują replikację programu VMware Site Recovery Manager (VMware SRM) i Veeam Backup & Replication.

Aby zainstalować rozwiązanie, należy zażądać dodatkowych uprawnień przez ograniczony okres. Zobacz temat [Eskalacja uprawnień](https://docs.azure.cloudsimple.com/escalate-private-cloud-privileges).

## <a name="next-steps"></a>Następne kroki

* [Korzystanie z maszyn wirtualnych VMware na platformie Azure](quickstart-create-vmware-virtual-machine.md)
* [Nawiązywanie połączenia z siecią lokalną za pomocą usługi Azure ExpressRoute](https://docs.azure.cloudsimple.com/on-premises-connection)
* [Konfigurowanie bram sieci VPN w sieci CloudSimple](https://docs.azure.cloudsimple.com/vpn-gateway)
