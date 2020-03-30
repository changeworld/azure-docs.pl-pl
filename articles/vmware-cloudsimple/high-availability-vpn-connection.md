---
title: Rozwiązanie Azure VMware by CloudSimple — konfigurowanie wysokiej dostępności z lokalnej do bramy sieci VPN CloudSimple
description: W tym artykule opisano sposób konfigurowania połączenia o wysokiej dostępności ze środowiska lokalnego do bramy sieci VPN CloudSimple włączonej w celu zapewnienia wysokiej dostępności
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/14/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 6e3118814eacc6cc63b5db59bd7f1877c1d347dc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77025269"
---
# <a name="configure-a-high-availability-connection-from-on-premises-to-cloudsimple-vpn-gateway"></a>Konfigurowanie połączenia o wysokiej dostępności z lokalnej do bramy sieci VPN CloudSimple

Administratorzy sieci mogą skonfigurować połączenie sieci VPN iPsec z lokacją do lokacji o wysokiej dostępności z lokalnego środowiska do bramy sieci VPN CloudSimple.

W tym przewodniku przedstawiono kroki konfigurowania zapory lokalnej dla połączenia sieci VPN z lokacją do lokacji IPsec. Szczegółowe kroki są specyficzne dla typu zapory lokalnej. W tym przewodniku przedstawiono kroki dotyczące dwóch typów zapór: Cisco ASA i Palo Alto Networks.

## <a name="before-you-begin"></a>Przed rozpoczęciem

Przed skonfigurowaniem zapory lokalnej wykonaj następujące zadania.

1. Sprawdź, czy twoja organizacja [aprowizować](create-nodes.md) wymagane węzły i utworzyć co najmniej jeden CloudSimple Private Cloud.
2. [Skonfiguruj bramę sieci VPN między siecią](vpn-gateway.md#set-up-a-site-to-site-vpn-gateway) lokalną a chmurą CloudSimple Private Cloud.

Zobacz [omówienie bram sieci VPN](cloudsimple-vpn-gateways.md) dla obsługiwanych propozycji fazy 1 i 2.

## <a name="configure-on-premises-cisco-asa-firewall"></a>Konfigurowanie lokalnej zapory Cisco ASA

Instrukcje zawarte w tej sekcji dotyczą rozwiązania Cisco ASA w wersji 8.4 lub nowszej. W przykładzie konfiguracji oprogramowanie Cisco Adaptive Security Appliance w wersji 9.10 jest wdrażane i konfigurowane w trybie IKEv1.

Aby sieć VPN między lokacjami działała, należy zezwolić na korzystanie z sieci VPN 500/4500 i ESP (protokół IP 50) z podstawowego i pomocniczego publicznego adresu IP (peer IP) cloudsimple na zewnętrznym interfejsie lokalnej bramy sieci VPN Cisco ASA.

### <a name="1-configure-phase-1-ikev1"></a>1. Konfigurowanie fazy 1 (IKEv1)

Aby włączyć fazę 1 (IKEv1) na interfejsie zewnętrznym, wprowadź następujące polecenie interfejsu wiersza polecenia w zaporze Cisco ASA.

```crypto ikev1 enable outside```

### <a name="2-create-an-ikev1-policy"></a>2. Tworzenie zasad IKEv1

Utwórz zasadę IKEv1, która definiuje algorytmy i metody, które mają być używane do mieszania, uwierzytelniania, grupy Diffie-Hellman, okresu istnienia i szyfrowania.

```
crypto ikev1 policy 1
authentication pre-share
encryption aes-256
hash sha
group 2
lifetime 28800
```

### <a name="3-create-a-tunnel-group"></a>3. Tworzenie grupy tuneli

Utwórz grupę tuneli pod atrybutami IPsec. Skonfiguruj równorzędny adres IP i klucz wstępny tunelu, który jest ustawiany podczas [konfigurowania bramy sieci VPN lokacja lokacja.](vpn-gateway.md#set-up-a-site-to-site-vpn-gateway)

```
tunnel-group <primary peer ip> type ipsec-l2l
tunnel-group <primary peer ip> ipsec-attributes
ikev1 pre-shared-key *****

tunnel-group <secondary peer ip> type ipsec-l2l
tunnel-group <secondary peer ip> ipsec-attributes
ikev1 pre-shared-key *****
```

### <a name="4-configure-phase-2-ipsec"></a>4. Konfigurowanie fazy 2 (IPsec)

Aby skonfigurować fazę 2 (IPsec), należy utworzyć listę kontroli dostępu (ACL), która definiuje ruch, który ma być zaszyfrowany i tunelowany. W poniższym przykładzie ruch zainteresowania pochodzi z tunelu, który jest pozyskiwany z lokalnej podsieci lokalnej (10.16.1.0/24) do podsieci zdalnej chmury prywatnej (192.168.0.0/24). Listy ACL mogą zawierać wiele wpisów, jeśli między lokacjami znajduje się wiele podsieci.

W aplikacji Cisco ASA w wersji 8.4 lub nowszej można tworzyć obiekty lub grupy obiektów, które służą jako kontenery dla sieci, podsieci, adresów IP hosta lub wielu obiektów. Utwórz obiekt dla lokalnego i obiektu dla zdalnych podsieci i użyj ich dla krypto listy ACL i instrukcji NAT.

#### <a name="define-an-on-premises-local-subnet-as-an-object"></a>Definiowanie lokalnej podsieci lokalnej jako obiektu

```
object network AZ_inside
subnet 10.16.1.0 255.255.255.0
```

#### <a name="define-the-cloudsimple-remote-subnet-as-an-object"></a>Definiowanie zdalnej podsieci CloudSimple jako obiektu

```
object network CS_inside
subnet 192.168.0.0 255.255.255.0
```

#### <a name="configure-an-access-list-for-the-traffic-of-interest"></a>Konfigurowanie listy dostępu dla ruchu będącego przedmiotem zainteresowania

```
access-list ipsec-acl extended permit ip object AZ_inside object CS_inside
```

### <a name="5-configure-the-transform-set"></a>5. Skonfiguruj zestaw transformacji

Skonfiguruj zestaw transformacji (TS), ```ikev1```który musi obejmować słowo kluczowe . Atrybuty szyfrowania i mieszania określone w systemie szyfrowania muszą być zgodne z parametrami wymienionymi w [polu Domyślna konfiguracja bram sieci VPN CloudSimple](cloudsimple-vpn-gateways.md).

```
crypto ipsec ikev1 transform-set devtest39 esp-aes-256 esp-sha-hmac 
```

### <a name="6-configure-the-crypto-map"></a>6. Konfigurowanie mapy kryptograficznej

Skonfiguruj mapę krypto, która zawiera następujące składniki:

* Równorzędny adres IP
* Zdefiniowana acl zawierająca ruch interesów
* Zestaw transformacji

```
crypto map mymap 1 set peer <primary peer ip> <secondary peer ip>
crypto map mymap 1 match address ipsec-acl
crypto map mymap 1 set ikev1 transform-set devtest39
```

### <a name="7-apply-the-crypto-map"></a>7. Zastosuj mapę kryptografii

Zastosuj mapę krypto na interfejsie zewnętrznym:

```crypto map mymap interface outside```

### <a name="8-confirm-applicable-nat-rules"></a>8. Potwierdzenie obowiązujących zasad NAT

Poniżej znajduje się używana reguła NAT. Upewnij się, że ruch sieci VPN nie jest poddawany żadnej innej regułie nat.

```nat (inside,outside) source static AZ_inside AZ_inside destination static CS_inside CS_inside```

### <a name="sample-ipsec-site-to-site-vpn-established-output-from-cisco-asa"></a>Przykładowa sieć VPN typu lokacja-lokacja ustalona przez cisco ASA

Wyjście fazy 1:

![Wyjście fazy 1 dla zapory Cisco ASA](media/ha-vpn-connection-cisco-phase1.png)

Wyjście fazy 2:

![Wyjście fazy 2 dla zapory Cisco ASA](media/ha-vpn-connection-cisco-phase2.png)

## <a name="configure-on-premises-palo-alto-networks-firewall"></a>Konfigurowanie lokalnej zapory sieci Palo Alto

Instrukcje opisane w tej sekcji dotyczą palo Alto Networks w wersji 7.1 i nowszej. W tym przykładzie konfiguracji oprogramowanie z serii VM Palo Alto w wersji 8.1.0 jest wdrażane i konfigurowane w trybie IKEv1.

Aby sieć VPN typu lokacja-lokacja działała, należy zezwolić na korzystanie z sieci VPN 500/4500 i ESP (protokół IP 50) z podstawowego i pomocniczego publicznego adresu IP (peer IP) cloudsimple na zewnętrznym interfejsie lokalnej bramy Palo Alto Networks.

### <a name="1-create-primary-and-secondary-tunnel-interfaces"></a>1. Tworzenie interfejsów tunelu podstawowego i pomocniczego

Zaloguj się do zapory Palo Alto, wybierz pozycję**Dodawanie tuneli** > **Add****interfejsów sieciowych,** >  **Network** > skonfiguruj następujące pola i kliknij przycisk **OK**.

* Nazwa interfejsu. Pierwsze pole jest automatycznie wypełniane słowem kluczowym "tunel". W sąsiednim polu wprowadź dowolną liczbę od 1 do 9999. Ten interfejs będzie używany jako podstawowy interfejs tunelu do przenoszenia ruchu lokacja-lokacja między lokalnym centrum danych a chmurą prywatną.
* Komentarz. Wprowadź komentarze ułatwiające identyfikację celu tunelu
* Profil przepływu netto. Pozostaw wartość domyślną.
* Config. Przypisz interfejs do: routera wirtualnego: wybierz **domyślny**. 
        Strefa zabezpieczeń: Wybierz strefę dla zaufanego ruchu LAN. W tym przykładzie nazwa strefy dla ruchu LAN to "Zaufanie".
* IPv4. Kliknij **przycisk Dodaj** i dodaj dowolny nienakładający się nieużywany /32 adres IP w danym środowisku, który zostanie przypisany do głównego interfejsu tunelu i będzie używany do monitorowania tuneli (wyjaśniono później).

Ponieważ ta konfiguracja jest dla sieci VPN o wysokiej dostępności, wymagane są dwa interfejsy tunelu: jeden podstawowy i jeden pomocniczy. Powtórz poprzednie kroki, aby utworzyć interfejs tunelu pomocniczego. Wybierz inny identyfikator tunelu i inny nieużyny /32 adres IP.

### <a name="2-set-up-static-routes-for-private-cloud-subnets-to-be-reached-over-the-site-to-site-vpn"></a>2. Konfigurowanie tras statycznych dla podsieci private cloud, do których można uzyskać kontakt za pośrednictwem sieci VPN lokacja-lokacja

Trasy są niezbędne do osiągnięcia podsieci chmur prywatnych cloud cloud w sieciach lokalnych.

Wybierz**domyślne trasy statyczne Routery** > *default* > **sieciowe** >  **Network** > **Dodaj,** skonfiguruj następujące pola i kliknij przycisk **OK**.

* Nazwa. Wprowadź dowolną nazwę, aby ułatwić identyfikację celu trasy.
* Docelowy. Określ podsieci chmury prywatnej CloudSimple, do których mają być dostępne interfejsy tunelu S2S z lokalnego
* Interfejs. Wybierz z listy rozwijanej podstawowy interfejs tunelu utworzony w kroku 1(Sekcja 2). W tym przykładzie jest tunnel.20.
* Następny hop. Wybierz pozycję **Brak**.
* Odległość administratora. Pozostaw wartość domyślną.
* Metrycznych. Wprowadź dowolną wartość od 1 do 65535. Kluczem jest wprowadzenie niższej metryki dla trasy odpowiadającej interfejsowi tunelu podstawowego w porównaniu z trasą odpowiadającą interfejsowi tunelu pomocniczego, dzięki czemu była trasa jest preferowana. Jeśli tunnel.20 ma wartość metryczną 20 w przeciwieństwie do wartości metrycznej 30 dla tunnel.30, tunnel.20 jest preferowane.
* Tabela tras. Pozostaw wartość domyślną.
* bfd profilu. Pozostaw wartość domyślną.
* Monitorowanie ścieżki. Pozostaw niezaznaczone.

Powtórz poprzednie kroki, aby utworzyć inną trasę dla podsieci private cloud, która będzie używana jako trasa pomocnicza/zapasowa za pośrednictwem pomocniczego interfejsu tunelu. Tym razem wybierz inny identyfikator tunelu i wyższą metrykę niż dla trasy podstawowej.

### <a name="3-define-the-cryptographic-profile"></a>3. Zdefiniuj profil kryptograficzny

Zdefiniuj profil kryptograficzny, który określa protokoły i algorytmy identyfikacji, uwierzytelniania i szyfrowania, które mają być używane do konfigurowania tuneli sieci VPN w fazie 1 IKEv1.

Wybierz **pozycję Sieć** > **Rozwijaj profile sieciowe** > **IKE Crypto** > **Add**, skonfiguruj następujące pola i kliknij przycisk **OK**.

* Nazwa. Wprowadź dowolną nazwę profilu kryptograficznego IKE.
* grupy DH. Kliknij **pozycję Dodaj** i wybierz odpowiednią grupę DH.
* Szyfrowanie. Kliknij **przycisk Dodaj** i wybierz odpowiednią metodę szyfrowania.
* Uwierzytelnianie. Kliknij **przycisk Dodaj** i wybierz odpowiednią metodę uwierzytelniania.
* Okres istnienia klucza. Pozostaw wartość domyślną.
* Uwierzytelnianie IKEv2 wiele. Pozostaw wartość domyślną.

### <a name="4-define-ike-gateways"></a>4. Definiowanie bram IKE

Zdefiniuj bramy IKE, aby ustanowić komunikację między równorzędnymi na każdym końcu tunelu sieci VPN.

Wybierz **pozycję Sieć** > **Rozwiń profile sieciowe** > **Bramy IKE** > **Dodaj,** skonfiguruj następujące pola i kliknij przycisk **OK**.

Karta Ogólne:

* Nazwa. Wprowadź nazwę bramy IKE, która ma być równorzędna z podstawowym elementem równorzędnym sieci VPN CloudSimple.
* Wersja. Wybierz **tryb tylko IKEv1**.
* Typ adresu. Wybierz pozycję **IPv4**.
* Interfejs. Wybierz interfejs publiczny skierowany lub zewnętrzny.
* Lokalny adres IP. Pozostaw wartość domyślną.
* Typ adresu IP elementu równorzędnego. Wybierz **IP**.
* Adres równorzędny. Wprowadź podstawowy adres IP elementu sieci VPN CloudSimple.
* Uwierzytelnianie. Wybierz **opcję Klucz wstępny**.
* Wstępnie udostępniony klucz / Potwierdzanie wstępnie udostępnionego klucza. Wprowadź klucz wstępny, aby dopasować klucz bramy sieci VPN CloudSimple.
* Identyfikacja lokalna. Wprowadź publiczny adres IP lokalnej zapory Palo Alto.
* Identyfikacja elementu równorzędnego. Wprowadź podstawowy adres IP elementu sieci VPN CloudSimple.

Karta Opcje zaawansowane:

* Włącz tryb pasywny. Pozostaw niezaznaczone.
* Włącz przechodzenie na nat. Pozostaw niezaznaczone, jeśli lokalna zapora Palo Alto nie znajduje się za żadnym urządzeniem NAT. W przeciwnym razie zaznacz to pole wyboru.

IKEv1:

* w trybie wymiany. Wybierz **główny**.
* Profil kryptograficzny IKE. Wybierz utworzony wcześniej profil IKE Crypto. Pole Włącz fragmentację nie pole wyboru.
* Wykrywanie martwego elementu równorzędnego. Pozostaw pole niezaznaczone.

Powtórz poprzednie kroki, aby utworzyć pomocniczą bramę IKE.

### <a name="5-define-ipsec-crypto-profiles"></a>5. Definiowanie profilów kryptograficznych IPSEC

Wybierz **pozycję Sieć** > **Rozwiń profile sieciowe** > **IPSEC Crypto** > **Add**, skonfiguruj następujące pola i kliknij przycisk **OK**.

* Nazwa. Wprowadź nazwę profilu kryptograficznego IPsec.
* protokół IPsec. Wybierz **ESP**.
* Szyfrowanie. Kliknij **przycisk Dodaj** i wybierz odpowiednią metodę szyfrowania.
* Uwierzytelnianie. Kliknij **przycisk Dodaj** i wybierz odpowiednią metodę uwierzytelniania.
* grupy DH. Wybierz **no-pfs**.
* Okres istnienia. Ustaw jako 30 minut.
* Włącz. Pozostaw pole niezaznaczone.

Powtórz poprzednie kroki, aby utworzyć inny profil kryptograficzny IPsec, który będzie używany jako pomocniczy element równorzędny sieci VPN CloudSimple. Ten sam profil kryptograficzny PROTOKOŁU IPSEC może być również używany zarówno w tunelach podstawowego, jak i pomocniczego protokołu IPsec (zobacz poniższą procedurę).

### <a name="6-define-monitor-profiles-for-tunnel-monitoring"></a>6. Zdefiniuj profile monitorów do monitorowania tunelu

Wybierz **pozycję** > **Monitor** > **uzupełniania sieciowego** > **Dodaj,** skonfiguruj następujące pola i kliknij przycisk **OK**.

* Nazwa. Wprowadź dowolną nazwę profilu monitora, który ma być używany do monitorowania tunelu w celu proaktywnej reakcji na awarię.
* Działania. Wybierz opcję **Przewijanie awaryjne**.
* Interwał. Wprowadź wartość **3**.
* Próg. Wprowadź wartość **7**.

### <a name="7-set-up-primary-and-secondary-ipsec-tunnels"></a>7. Konfigurowanie podstawowych i pomocniczych tuneli IPsec.

Wybierz **opcję Dodawanie** > **tuneli** > protokołu IPsec**sieciowego,** skonfiguruj następujące pola i kliknij przycisk **OK**.

Karta Ogólne:

* Nazwa. Wprowadź dowolną nazwę podstawowego tunelu IPSEC, który ma być równorzędny z podstawowym elementem równorzędnym sieci VPN CloudSimple.
* Interfejs tunelu. Wybierz główny interfejs tunelu.
* Typu. Pozostaw wartość domyślną.
* Typ adresu. Wybierz pozycję **IPv4**.
* Brama IKE. Wybierz podstawową bramę IKE.
* Profil kryptograficzny protokołu IPsec. Wybierz podstawowy profil IPsec. Wybierz **pozycję Pokaż opcje zaawansowane**.
* Włącz ochronę przed powtórkami. Pozostaw wartość domyślną.
* Kopiowanie nagłówka TOS. Pozostaw pole niezaznaczone.
* Monitor tunelu. Zaznacz to pole wyboru.
* Docelowy adres IP. Wprowadź dowolny adres IP należący do podsieci CloudSimple Private Cloud, który jest dozwolony za na połączenie lokacja lokacja. Upewnij się, że interfejsy tunelu (takie jak tunnel.20 - 10.64.5.2/32 i tunnel.30 - 10.64.6.2/32) na Palo Alto mogą dotrzeć do adresu IP CloudSimple Private Cloud za pośrednictwem sieci VPN site-to-site. Zobacz następującą konfigurację identyfikatorów proxy.
* Proﬁl. Wybierz profil monitora.

Karta Identyfikatory proxy: kliknij pozycję **IPv4** > **Dodaj** i skonfiguruj następujące elementy:

* Identyfikator serwera proxy. Wprowadź dowolną nazwę interesującego ruchu. Wewnątrz jednego tunelu IPsec może znajdować się wiele identyfikatorów proxy.
* Lokalnych. Określ lokalne podsieci lokalne, które mogą komunikować się z podsieciami usługi Private Cloud za pośrednictwem sieci VPN lokacja lokacja.
* Zdalnego. Określ podsieci zdalne w chmurze prywatnej, które mogą komunikować się z podsieciami lokalnymi.
* Protokół. Wybierz **dowolny**plik .

Powtórz poprzednie kroki, aby utworzyć inny tunel IPsec do użycia dla pomocniczego elementu równorzędnego sieci VPN CloudSimple.

## <a name="references"></a>Dokumentacja

Konfigurowanie translatora i ciągłego kontroli na cisco ASA:

<a href="https://www.cisco.com/c/en/us/td/docs/security/asa/asa84/configuration/guide/asa_84_cli_config/nat_objects.html" target="_blank">Przewodnik konfiguracyjny serii Cisco ASA 5500</a>

Obsługiwane atrybuty IKEv1 i IKEv2 w cisco ASA:

<a href="https://www.cisco.com/c/en/us/td/docs/security/asa/asa90/configuration/guide/asa_90_cli_config/vpn_ike.html#21661" target="_blank">Przewodnik konfiguracji interfejsu wiersza polecenia z serii Cisco ASA</a>

Konfigurowanie sieci VPN między lokacjami protokołu IPsec w systemie Cisco ASA w wersji 8.4 lub nowszej:

<a href="https://www.cisco.com/c/en/us/support/docs/security/asa-5500-x-series-next-generation-firewalls/119141-configure-asa-00.html#anc8" target="_blank">Konfigurowanie tuneli lokacja-lokacja IKEv1 z asdm lub cli na ASA</a>

Konfigurowanie wirtualnego urządzenia Cisco Adaptive Security Appliance (ASAv) na platformie Azure:

<a href="https://www.cisco.com/c/en/us/td/docs/security/asa/asa96/asav/quick-start-book/asav-96-qsg/asav-azure.html" target="_blank">Przewodnik szybki start dla cisco Adaptive Security Virtual Appliance (ASAv)</a>

Konfigurowanie sieci VPN typu lokacja-lokacja przy pozokreślaniu identyfikatorów proxy w palo alto:

[Konfigurowanie sieci VPN między lokacjami](https://docs.paloaltonetworks.com/pan-os/9-0/pan-os-admin/vpns/set-up-site-to-site-vpn#)

Konfigurowanie monitora tunelu:

[Konfigurowanie monitorowania tuneli](https://docs.paloaltonetworks.com/pan-os/7-1/pan-os-admin/vpns/set-up-tunnel-monitoring.html)

Operacje w tunelu bramy IKE lub IPsec:

<a href="https://docs.paloaltonetworks.com/pan-os/9-0/pan-os-admin/vpns/set-up-site-to-site-vpn/enabledisable-refresh-or-restart-an-ike-gateway-or-ipsec-tunnel#" target="_blank">Włączanie/wyłączanie, odświeżanie lub ponowne uruchamianie bramy IKE lub tunelu IPsec</a>
