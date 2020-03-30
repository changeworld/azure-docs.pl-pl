---
title: Szybki start — tworzenie maszyny wirtualnej platformy Azure vmware w chmurze prywatnej — rozwiązanie Azure VMware firmy CloudSimple
description: W tym artykule opisano sposób tworzenia maszyny wirtualnej platformy Azure vmware w chmurze prywatnej CloudSimple
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/16/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 4ac818cfd267b781366c0e32c9f93cc885dff99c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77566152"
---
# <a name="create-vmware-virtual-machines-on-your-private-cloud"></a>Tworzenie maszyn wirtualnych VMware w chmurze prywatnej

Aby utworzyć maszyny wirtualne w chmurze prywatnej, należy rozpocząć od uzyskania dostępu do portalu CloudSimple z witryny Azure portal.

## <a name="sign-in-to-the-azure-portal"></a>Logowanie się do witryny Azure Portal

Zaloguj się do witryny Azure portal w [https://portal.azure.com](https://portal.azure.com).

## <a name="access-the-cloudsimple-portal"></a>Uzyskiwanie dostępu do portalu CloudSimple

1. Wybierz pozycję **Wszystkie usługi**.
2. Wyszukaj **usługi CloudSimple**.
3. Wybierz usługę CloudSimple, w której chcesz utworzyć chmurę prywatną.
4. Na stronie **Przegląd** kliknij pozycję **Przejdź do portalu CloudSimple,** aby otworzyć nową kartę przeglądarki dla portalu CloudSimple.  Jeśli zostanie wyświetlony monit, zaloguj się przy użyciu poświadczeń logowania platformy Azure.  

    ![Uruchom portal CloudSimple](media/launch-cloudsimple-portal.png)

## <a name="launch-vcenter-web-ui"></a>Uruchamianie vCenter web-ui

Teraz można uruchomić vCenter, aby skonfigurować maszyny wirtualne i zasady.

Aby uzyskać dostęp do vCenter, uruchom z portalu CloudSimple. Na stronie głównej w obszarze **Typowe zadania**kliknij pozycję Uruchom **klienta vSphere**.  Wybierz chmurę prywatną, a następnie kliknij przycisk **Uruchom klienta vSphere** w chmurze prywatnej.

   ![Uruchom klienta vSphere](media/launch-vcenter-from-cloudsimple-portal.png)

## <a name="upload-an-iso-or-vsphere-template"></a>Przekazywanie szablonu ISO lub vSphere

  > [!WARNING]
  > W przypadku przekazywania ISO użyj klienta vSphere HTML5.  Użycie klienta Flash może spowodować błąd.

1. Uzyskaj szablon ISO lub vSphere, który chcesz przekazać do vCenter, aby utworzyć maszynę wirtualną i udostępnić ją w systemie lokalnym.
2. W programie vCenter kliknij ikonę **Dysk** i wybierz **pozycję vsanDatastore**. Kliknij pozycję **Pliki,** a następnie kliknij pozycję **Nowy folder**.
    ![vCentrer ISO](media/vciso00.png)

3. Utwórz folder zatytułowany "Iso i szablony".

4. Przejdź do folderu ISO w iso i szablony i kliknij przycisk **Przekaż pliki**. Postępuj zgodnie z instrukcjami wyświetlanymi na ekranie, aby przesłać iso.

## <a name="create-a-virtual-machine-in-vcenter"></a>Tworzenie maszyny wirtualnej w centrum vCenter

1. W vCenter kliknij ikonę **Hosty i klastry.**

2. Kliknij prawym przyciskiem myszy **pozycję Obciążenie** i wybierz pozycję Nowa **maszyna wirtualna**.
    ![Nowa maszyna wirtualna](media/vcvm01.png)

3. Wybierz **pozycję Utwórz nową maszynę wirtualną** i kliknij przycisk **Dalej**.
    ![Nowa maszyna wirtualna](media/vcvm02.png)

4. Nazwij komputer, wybierz lokalizację **maszyny wirtualnej obciążenia** i kliknij przycisk **Dalej**.
    ![Nowa maszyna wirtualna](media/vcvm03.png)

5. Wybierz zasób obliczeniowy **obciążenia** i kliknij przycisk **Dalej**.
    ![Nowa maszyna wirtualna](media/vcvm04.png)

6. Wybierz **vsanDatastore** i kliknij przycisk **Dalej**.
    ![Nowa maszyna wirtualna](media/vcvm05.png)

7. Zachowaj domyślny wybór zgodności ESXi 6.5 i kliknij przycisk **Dalej**.
    ![Nowa maszyna wirtualna](media/vcvm06.png)

8. Wybierz system operacyjny gościa iso dla maszyny Wirtualnej, którą tworzysz i kliknij przycisk **Dalej**.
    ![Nowa maszyna wirtualna](media/vcvm07.png)

9. Wybierz opcje dysku twardego i sieci. W przypadku nowego napędu CD/DVD wybierz **plik ISO magazynu danych**.  Jeśli chcesz zezwolić na ruch z publicznego adresu IP na tę maszynę wirtualną, wybierz sieć jako **vm-1**.
    ![Nowa maszyna wirtualna](media/vcvm08.png)

10. Zostanie otwarte okno wyboru. Wybierz plik, który wcześniej został przesłany do folderu ISO i szablony, a następnie kliknij przycisk **OK**.
    ![Nowa maszyna wirtualna](media/vcvm10.png)

11. Przejrzyj ustawienia i kliknij przycisk **OK,** aby utworzyć maszynę wirtualną.
    ![Nowa maszyna wirtualna](media/vcvm11.png)

Maszyna wirtualna jest teraz dodawana do zasobów obliczeniowych obciążenia i jest gotowa do użycia. 
![Nowa maszyna wirtualna](media/vcvm12.png)

Podstawowa konfiguracja została zakończona. Możesz rozpocząć korzystanie z chmury prywatnej podobne do sposobu korzystania z infrastruktury lokalnej maszyny Wirtualnej.

Poniższe sekcje zawierają opcjonalne informacje dotyczące konfigurowania serwerów DNS i DHCP dla obciążeń chmury prywatnej oraz modyfikowania domyślnej konfiguracji sieci.

## <a name="add-users-and-identity-sources-to-vcenter-optional"></a>Dodawanie użytkowników i źródeł tożsamości do centrum vCenter (opcjonalnie)

CloudSimple przypisuje domyślne konto użytkownika vCenter z nazwą użytkownika `cloudowner@cloudsimple.local`. Aby rozpocząć pracę, nie jest wymagana żadna dodatkowa konfiguracja konta.  CloudSimple zwykle przypisuje administratorom uprawnienia, których potrzebują do wykonywania normalnych operacji.  Skonfiguruj lokalną usługę Active Directory lub usługę Azure AD jako [dodatkowe źródło tożsamości](set-vcenter-identity.md) w chmurze prywatnej.

## <a name="create-a-dns-and-dhcp-server-optional"></a>Tworzenie serwera DNS i DHCP (opcjonalnie)

Aplikacje i obciążenia uruchomione w środowisku chmury prywatnej wymagają rozpoznawania nazw i usług DHCP do odnośnia i przypisywania adresów IP. Do świadczenia tych usług wymagana jest odpowiednia infrastruktura DHCP i DNS. Maszynę wirtualną można skonfigurować w centrum vCenter, aby zapewnić te usługi w środowisku chmury prywatnej.

Wymagania wstępne

* Rozproszona grupa portów ze skonfigurowaną siecią VLAN

* Kierowanie konfiguracji do lokalnych lub internetowych serwerów DNS

* Szablon maszyny wirtualnej lub iso do tworzenia maszyny wirtualnej

Poniższe łącza zawierają wskazówki dotyczące konfigurowania serwerów DHCP i DNS w systemach Linux i Windows.

#### <a name="linux-based-dns-server-setup"></a>Konfiguracja serwera DNS oparta na systemie Linux

Linux oferuje różne pakiety do konfigurowania serwerów DNS.  Oto łącze do instrukcji konfigurowania serwera DNS BIND typu open source.

[Przykładowa konfiguracja](https://www.digitalocean.com/community/tutorials/how-to-configure-bind-as-a-private-network-dns-server-on-centos-7)

#### <a name="windows-based-setup"></a>Konfiguracja oparta na systemie Windows

W tych tematach firmy Microsoft opisano sposób konfigurowania serwera Windows jako serwera DNS i serwera DHCP.

[Windows Server jako serwer DNS](https://docs.microsoft.com/windows-server/networking/dns/dns-top)

[Windows Server jako serwer DHCP](https://docs.microsoft.com/windows-server/networking/technologies/dhcp/dhcp-top)

## <a name="customize-networking-configuration-optional"></a>Dostosowywanie konfiguracji sieci (opcjonalnie)

Strony sieciowe w portalu CloudSimple umożliwiają określenie konfiguracji tabel zapory i publicznych adresów IP maszyn wirtualnych.

### <a name="allocate-public-ips"></a>Przydzielanie publicznych usług IP

1. Przejdź do **sieciowego > publicznego adresu IP** w portalu CloudSimple.
2. Kliknij **pozycję Przydziel publiczny adres IP**.
3. Wprowadź nazwę, aby zidentyfikować wpis adresu IP.
4. Zachowaj domyślną lokalizację.
5. Użyj suwaka, aby w razie potrzeby zmienić limit czasu bezczynnego.
6. Wprowadź lokalny adres IP, do którego chcesz przypisać publiczny adres IP.
7. W razie potrzeby wprowadź skojarzoną nazwę DNS.
8. Kliknij przycisk **Gotowe**.

    ![Publiczny adres IP](media/quick-create-pc-public-ip.png)

Rozpoczyna się zadanie przydzielania publicznego adresu IP. Stan zadania można sprawdzić na stronie **Działanie > Zadania.** Po zakończeniu alokacji nowy wpis jest wyświetlany na stronie Publiczne wiadomości IP.

Maszyna wirtualna, do której ten adres IP musi być mapowany, musi być skonfigurowana przy pomocy adresu lokalnego określonego powyżej. Procedura konfigurowania adresu IP jest specyficzna dla systemu operacyjnego maszyny Wirtualnej. Aby uzyskać poprawną procedurę, zapoznaj się z dokumentacją systemu operacyjnego maszyny Wirtualnej.

#### <a name="example"></a>Przykład

Oto szczegóły dotyczące Ubuntu 16.04.

Dodaj metodę statyczną do konfiguracji rodziny adresów inet w pliku /etc/network/interfaces. Zmień wartości adresu, maski sieciowej i bramy. W tym przykładzie używamy interfejsu eth0, wewnętrznego adresu IP 192.168.24.10, adresu bramy 192.168.24.1 i maski sieciowej 255.255.255.0. W twoim środowisku dostępne informacje o podsieci są dostępne w wiadomości e-mail powitalnej.

```
sudo vi /etc/network/interfaces
```

```
auto eth0
Iface eth0 inet static
iface eth0 inet static
address 192.168.24.10
netmask 255.255.255.0
gateway 192.168.24.1
dns-nameservers 8.8.8.8
dns-domain acme.com
dns-search acme.com
```
Ręcznie wyłącz interfejs.

```
sudo ifdown eth0
```
Ponownie włącz interfejs ręcznie.

```
sudo ifup eth0
```

Domyślnie cały ruch przychodzący z Internetu jest **odrzucany**. Jeśli chcesz otworzyć inny port, utwórz [tabelę zapory](firewall.md).

Po skonfigurowaniu wewnętrznego adresu IP jako statycznego adresu IP sprawdź, czy można dotrzeć do Internetu z poziomu maszyny Wirtualnej.

```
ping 8.8.8.8
```
Sprawdź również, czy można dotrzeć do maszyny Wirtualnej z Internetu przy użyciu publicznego adresu IP.

Upewnij się, że wszystkie reguły iptable na maszynie Wirtualnej nie blokują portu 80 przychodzącego.
        
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
Uruchom przeglądarkę na pulpicie i wskaż ją do portu 80 dla publicznego adresu IP, aby przeglądać pliki na maszynie Wirtualnej.

### <a name="default-cloudsimple-firewall-rules-for-public-ip"></a>Domyślne reguły zapory CloudSimple dla publicznego adresu IP

* Ruch sieci VPN: Cały ruch między siecią VPN (z/do) a wszystkimi sieciami obciążenia i siecią zarządzania jest dozwolony.
* Ruch wewnętrzny chmury prywatnej: dozwolony jest cały ruch wschód-zachód między sieciami obciążenia (z/do) a siecią zarządzania (pokazaną powyżej).
* Ruch internetowy:
  * Cały ruch przychodzący z Internetu jest odrzucany do sieci obciążenia i sieci zarządzania.
  * Cały ruch wychodzący do Internetu z sieci obciążeń lub sieci zarządzania jest dozwolony.

Można również zmodyfikować sposób zabezpieczenia ruchu za pomocą funkcji Reguły zapory. Aby uzyskać więcej informacji, zobacz [Konfigurowanie tabel i reguł zapory](firewall.md).

## <a name="install-solutions-optional"></a>Rozwiązania instalacyjne (opcjonalnie)

Możesz zainstalować rozwiązania w chmurze CloudSimple Private Cloud, aby w pełni korzystać ze środowiska private cloud vCenter. Można skonfigurować tworzenie kopii zapasowych, odzyskiwanie po awarii, replikację i inne funkcje w celu ochrony maszyn wirtualnych. Przykłady obejmują VMware Site Recovery Manager (VMware SRM) i Veeam Backup & Replication.

Aby zainstalować rozwiązanie, należy zażądać dodatkowych uprawnień przez ograniczony okres. Zobacz [Uprawnienia eskalacji](escalate-private-cloud-privileges.md).

## <a name="next-steps"></a>Następne kroki

* [Używanie maszyn wirtualnych VMware na platformie Azure](quickstart-create-vmware-virtual-machine.md)
* [Łączenie się z siecią lokalną przy użyciu usługi Azure ExpressRoute](on-premises-connection.md)
* [Konfigurowanie bram sieci VPN w sieci CloudSimple](vpn-gateway.md)
