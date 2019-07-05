---
title: Rozwiązanie programu VMware na platformie Azure przez CloudSimple Przewodnik Szybki Start — tworzenie chmury prywatnej
description: Dowiedz się, jak utworzyć i skonfigurować chmury prywatnej za pomocą usługi Azure VMware Solution by CloudSimple
author: sharaths-cs
ms.author: dikamath
ms.date: 04/10/2019
ms.topic: article
ms.service: vmware
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 85a8840ccf6f6fe6390b5eeaccd715d87169f157
ms.sourcegitcommit: f811238c0d732deb1f0892fe7a20a26c993bc4fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/29/2019
ms.locfileid: "67476030"
---
# <a name="quickstart---configure-a-private-cloud-environment"></a>Przewodnik Szybki Start — Konfigurowanie środowiska chmury prywatnej

W tym artykule Dowiedz się, jak utworzyć chmurę prywatną CloudSimple i skonfiguruj swoje środowisko chmury prywatnej.

## <a name="sign-in-to-azure"></a>Logowanie do platformy Azure
Zaloguj się do witryny Azure Portal pod adresem [https://portal.azure.com](https://portal.azure.com).

## <a name="create-a-private-cloud"></a>Tworzenie chmury prywatnej

1. Wybierz pozycję **Wszystkie usługi**.
2. Wyszukaj **usług CloudSimple**.
3. Wybierz usługę CloudSimple, na którym chcesz utworzyć chmury prywatnej.
4. Z przeglądu, kliknij przycisk **tworzenie chmury prywatnej** aby otworzyć nową kartę przeglądarki dla portalu CloudSimple.  Jeśli zostanie wyświetlony monit, zaloguj się przy użyciu subskrypcji platformy Azure Zaloguj się w poświadczeniach.  

    ![Tworzenie chmury prywatnej z platformy Azure](media/create-private-cloud-from-azure.png)

5. W portalu CloudSimple Podaj nazwę chmury prywatnej
6. Wybierz **lokalizacji** chmury prywatnej
7. Wybierz **typ węzła** aprowizować na platformie Azure.  Możesz wybrać [opcji CS28 lub CS36](cloudsimple-node.md#vmware-solution-by-cloudsimple-nodes-sku). Tę druga opcję zawiera maksymalną moc obliczeniową i pamięć.
8. Określ **liczby węzłów**.  Co najmniej trzy węzły są wymagane do utworzenia chmury prywatnej

    ![Tworzenie chmury prywatnej — informacje podstawowe](media/create-private-cloud-basic-info.png)

9. Kliknij pozycję **Next: Zaawansowane opcje**.
10. Wprowadź zakres CIDR podsieci vSphere/sieci vSAN. Upewnij się, że zakres CIDR nie pokrywa się z żadną z lokalnych lub innych podsieci platformy Azure.

    ![Tworzenie chmury prywatnej — opcje zaawansowane](media/create-private-cloud-advanced-options.png)

11. Wybierz opcję **Dalej: Przejrzyj i Utwórz**.
12. Przejrzyj ustawienia. Jeśli potrzebujesz zmienić dowolne ustawienia, kliknij przycisk **Wstecz**.
13. Kliknij pozycję **Utwórz**.

Chmura prywatna w procesie inicjowania obsługi zostanie uruchomiony.  Może potrwać maksymalnie dwie godziny dla chmury prywatnej do zainicjowania obsługi administracyjnej.

## <a name="launch-cloudsimple-portal"></a>Uruchom CloudSimple portal

Można uzyskać dostęp do portalu CloudSimple z witryny Azure portal.  CloudSimple portal zostanie uruchomiona przy użyciu subskrypcji platformy Azure Zaloguj poświadczeń za pomocą logowania jednokrotnego (SSO).  Uzyskiwanie dostępu do portalu CloudSimple wymaga autoryzacji **CloudSimple usługi autoryzacji** aplikacji.  Aby uzyskać więcej informacji na temat udzielania uprawnień, zobacz [zgoda na aplikację CloudSimple usługi autoryzacji](https://docs.azure.cloudsimple.com/access-cloudsimple-portal/#consent-to-cloudsimple-service-authorization-application)

1. Wybierz pozycję **Wszystkie usługi**.
2. Wyszukaj **usług CloudSimple**.
3. Wybierz usługę CloudSimple, na którym chcesz utworzyć chmury prywatnej.
4. Z przeglądu, kliknij przycisk **przejdź do portalu CloudSimple** aby otworzyć nową kartę przeglądarki dla portalu CloudSimple.  Jeśli zostanie wyświetlony monit, zaloguj się przy użyciu subskrypcji platformy Azure Zaloguj się w poświadczeniach.  

    ![Uruchom CloudSimple Portal](media/launch-cloudsimple-portal.png)

## <a name="create-point-to-site-vpn"></a>Tworzenie sieci VPN typu punkt lokacja

Połączenie sieci VPN typu punkt lokacja to najprostszy sposób połączyć się z chmury prywatnej z komputera. Użyj połączenia sieci VPN typu punkt lokacja, jeśli łączysz się Chmura prywatna zdalnie.  Aby uzyskać szybki dostęp do chmury prywatnej wykonaj poniższe kroki.  Dostęp do regionu CloudSimple z sieci lokalnej można zrobić za pomocą [sieci VPN typu lokacja-lokacja](https://docs.azure.cloudsimple.com/vpn-gateway/) lub [usługi Azure ExpressRoute](https://docs.azure.cloudsimple.com/on-premises-connection/).

### <a name="create-gateway"></a>Tworzenie bramy

1. Uruchom CloudSimple portal i wybierz pozycję **sieci**.
2. Wybierz **bramy sieci VPN**.
3. Kliknij przycisk **nową bramę sieci VPN**.

    ![Tworzenie bramy sieci VPN](media/create-vpn-gateway.png)

4. Aby uzyskać **konfiguracji bramy**, określ poniższe ustawienia i kliknij przycisk **dalej**.

    * Wybierz **Point-to-Site VPN** jako typ bramy.
    * Wprowadź nazwę identyfikującą bramy.
    * Wybierz lokalizację platformy Azure, gdzie usługa CloudSimple zostanie wdrożona.
    * Określ podsieci klienta dla bramy punkt lokacja.  Z tą podsiecią otrzymają adresów DHCP po nawiązaniu połączenia.

5. Dla **połączenia/użytkownika**, określ poniższe ustawienia i kliknij przycisk **dalej**.

    * Aby automatycznie zezwolić na wszystkie obecnym i przyszłym użytkownikom dostęp do chmury prywatnej za pośrednictwem tej bramy point-to-site, wybierz **automatycznie dodać wszystkich użytkowników**. Po wybraniu tej opcji, wszyscy użytkownicy na liście użytkowników zostaną zaznaczone automatycznie. Opcja automatycznego można zastąpić, anulując poszczególnych użytkowników na liście.
    * Aby wybrać tylko pojedynczy użytkownicy, kliknij pole wyboru na liście użytkowników.

6. W sekcji podsieci/sieci VLAN umożliwia określenie, zarządzania i bramy oraz połączeń użytkownika podsieci/sieci VLAN.

    * **Automatyczne dodawanie** opcje ustawiania globalnych zasad tej bramy. Ustawienia są stosowane do bieżącego bramy. Ustawienia mogą zostać zastąpione w **wybierz** obszaru.
    * Wybierz **Dodaj zarządzania podsieci/sieci VLAN chmur prywatnych**. 
    * Aby dodać wszystkie zdefiniowane przez użytkownika podsieci/sieci VLAN, kliknij **Dodaj użytkownika podsieci/sieci VLAN**. 
    * **Wybierz** ustawienia zastąpią ustawienia globalne w ramach **automatyczne dodawanie**. 

7. Kliknij przycisk **dalej** Aby przejrzeć ustawienia. Kliknij ikony edycji, aby wprowadzać żadnych zmian.
8. Kliknij przycisk **Utwórz** do tworzenia bramy sieci VPN.

### <a name="connect-to-cloudsimple-using-point-to-site-vpn"></a>Nawiązać połączenie z CloudSimple przy użyciu sieci VPN typu punkt lokacja

Klient sieci VPN jest wymagana do łączenia się z CloudSimple z komputera.  Pobierz [klienta OpenVPN](https://openvpn.net/community-downloads/) dla Windows lub [lepkości](https://www.sparklabs.com/viscosity/download/) dla systemu macOS i OS X.

1. Uruchom CloudSimple portal i wybierz pozycję **sieci**.
2. Wybierz **bramy sieci VPN**.
3. Z listy bram sieci VPN kliknij w bramie sieci VPN typu punkt lokacja.
4. Wybierz **użytkowników**.
5. Kliknij pozycję **Pobierz mój konfigurację sieci VPN**

    ![Pobieranie konfiguracji sieci VPN](media/download-p2s-vpn-configuration.png)

6. Importowanie konfiguracji klienta sieci VPN

    * Instrukcje dotyczące [importowania konfiguracji w kliencie Windows](https://openvpn.net/vpn-server-resources/connecting-to-access-server-with-windows/#openvpn-open-source-openvpn-gui-program)
    * Instrukcje dotyczące [importowania konfiguracji w systemie macOS lub OS X](https://www.sparklabs.com/support/kb/article/getting-started-with-viscosity-mac/#creating-your-first-connection)

7. Nawiązać połączenie z CloudSimple

## <a name="create-a-vlan-for-your-workload-vms"></a>Tworzenie sieci VLAN dla obciążenia maszyn wirtualnych

Po utworzeniu chmury prywatnej, należy utworzyć sieci VLAN, w przypadku wdrażania obciążeń/aplikacji maszyn wirtualnych.

1. W portalu CloudSimple wybierz **sieci**.
2. Kliknij przycisk **podsieci/sieci VLAN**.
3. Kliknij przycisk **Tworzenie podsieci/sieci VLAN**

    ![Tworzenie podsieci/sieci VLAN](media/create-new-vlan-subnet.png)

4. Wybierz **Chmura prywatna** dla nowej sieci VLAN/podsieci.
5. Wybierz identyfikator sieci VLAN z listy.  
6. Wprowadź nazwę podsieci do identyfikacji podsieci.
7. Określ zakres CIDR podsieci i maski.  Ten zakres nie nakładać się na wszystkie istniejące podsieci.
8. Kliknij przycisk **Submit** (Prześlij).

    ![Tworzenie szczegółów podsieci/sieci VLAN](media/create-new-vlan-subnet-details.png)

Podsieci/sieci VLAN zostanie utworzony.  Ten identyfikator sieci VLAN można teraz używać do tworzenia grupy portów rozproszone na vCenter, Chmura prywatna. 

## <a name="connect-your-environment-to-an-azure-virtual-network"></a>Środowiska nawiązać połączenie z siecią wirtualną platformy Azure

CloudSimple zapewnia obwodu usługi ExpressRoute w chmurze prywatnej. Możesz połączyć sieć wirtualną na platformie Azure z obwodem usługi ExpressRoute. Aby uzyskać szczegółowe informacje na temat konfigurowania połączenia, postępuj zgodnie z instrukcjami w [Azure wirtualnego połączenia sieciowego przy użyciu usługi ExpressRoute](https://docs.azure.cloudsimple.com/cloudsimple-azure-network-connection/)

## <a name="sign-in-to-vcenter"></a>Zaloguj się do vCenter

Użytkownik może teraz zalogować do vCenter do skonfigurowania zasad i maszyn wirtualnych.

1. Aby uzyskać dostęp vCenter, uruchomić z poziomu portalu CloudSimple. Na stronie głównej w obszarze **typowe zadania związane z**, kliknij przycisk **Uruchom klienta vSphere**.  Wybierz chmurę prywatną, a następnie kliknij przycisk **Uruchom klienta vSphere** w chmurze prywatnej.

    ![Uruchom klienta vSphere](media/launch-vcenter-from-cloudsimple-portal.png)

2. Wybierz preferowaną vSphere klienta dostępu do vCenter i zaloguj się przy użyciu nazwy użytkownika i hasła.  Wartości domyślne to:
    * Nazwa użytkownika: **CloudOwner@cloudsimple.local**
    * Hasło: **CloudSimple123!**  

Ekrany vCenter w następnych procedurach są w kliencie vSphere (HTML5).

## <a name="change-your-vcenter-password"></a>Zmień hasło vCenter

CloudSimple zaleca się zmienić hasło podczas pierwszego logowania się do vCenter.  
Hasło, które muszą spełniać następujące wymagania:

* Maksymalny okres istnienia: Należy zmienić hasło co 365 dni
* Ogranicz ponowne użycie: Użytkownicy nie ponowne użycie żadnego z 5 poprzednich haseł
* Długość: 8 - 20 znaków
* Znaki specjalne: Co najmniej jeden znak specjalny
* Znaki alfabetyczne: Co najmniej jedną wielką literę, A-Z i co najmniej jedną małą literę, a-z
* Liczby: Co najmniej jedną cyfrę, 0-9
* Maksymalna liczba znaków sąsiadujących identyczne: Trzy

    Przykład: DW lub CCC jest dopuszczalna jako część hasło, ale nie jest CCCC.

Jeśli ustawisz hasło nie spełnia wymagań:

* Jeśli używasz programu Flash klienta vSphere, zgłasza błąd
* Jeśli używasz klienta HTML5, go nie komunikat o błędzie. Klient nie obsługuje zmiany i starego hasła będzie nadal działać.

## <a name="change-nsx-administrator-password"></a>Zmienianie hasła administratora NSX

Menedżer NSX jest wdrażana przy użyciu hasła domyślne.  Zalecamy zmianę hasła, po utworzeniu chmury prywatnej.

   * Nazwa użytkownika: **administratora**
   * Hasło: **CloudSimple123!**

W pełni kwalifikowaną nazwę domeny (FQDN) i adres IP Menedżera NSX można znaleźć w portalu CloudSimple.

1. Uruchom CloudSimple portal i wybierz pozycję **zasobów**.
2. Polecenie chmury prywatnej, w której chcesz użyć.
3. Wybierz **vSphere sieć zarządzania**
4. Użyj nazwy FQDN lub adres IP **Menedżera NSX** i nawiązywanie połączenia przy użyciu przeglądarki sieci web. 

    ![Znajdź Menedżera NSX w pełni kwalifikowaną nazwę domeny](media/private-cloud-nsx-manager-fqdn.png)

Aby zmienić hasła, postępuj zgodnie z instrukcjami [zarządzanie hasłem użytkownika](https://docs.vmware.com/en/VMware-NSX-T-Data-Center/2.4/administration/GUID-DB31B304-66A5-4516-9E55-2712D12B4F27.html).

> [!WARNING]
> Domyślnie hasło administratora NSX wygasa po upływie 90 dni.

## <a name="create-a-port-group"></a>Tworzenie grupy portów

Aby utworzyć grupy portów rozproszonych w vSphere:

1. Postępuj zgodnie z instrukcjami "Dodaj grupę rozproszonych portu" w [przewodnik dotyczący sieci programu vSphere](https://docs.vmware.com/en/VMware-vSphere/6.5/vsphere-esxi-vcenter-server-65-networking-guide.pdf).
2. Podczas konfigurowania grupy portów rozproszonych, należy podać identyfikator sieci VLAN, utworzone w [tworzenie sieci VLAN dla maszyn wirtualnych z systemem obciążenie](#create-a-vlan-for-your-workload-vms).

## <a name="next-steps"></a>Kolejne kroki

* [Używanie maszyn wirtualnych VMware na platformie Azure](https://docs.azure.cloudsimple.com/quickstart-create-vmware-virtual-machine)
* [Używanie maszyn wirtualnych VMware na platformie Azure](quickstart-create-vmware-virtual-machine.md)
* [Nawiązać połączenie z siecią lokalną przy użyciu usługi Azure ExpressRoute](https://docs.azure.cloudsimple.com/on-premises-connection/)
* [Konfigurowanie sieci VPN typu lokacja lokacja, ze środowiska lokalnego](https://docs.azure.cloudsimple.com/vpn-gateway/)
