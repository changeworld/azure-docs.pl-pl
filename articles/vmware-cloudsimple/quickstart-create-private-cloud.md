---
title: Azure VMware Solutions (Automatyczna synchronizacja) — szybki start — tworzenie automatycznej chmury prywatnej
description: Dowiedz się, jak utworzyć i skonfigurować chmurę prywatną do automatycznej synchronizacji przy użyciu rozwiązań VMware platformy Azure (Automatyczna synchronizacja)
titleSuffix: Azure VMware Solutions (AVS)
author: sharaths-cs
ms.author: dikamath
ms.date: 08/16/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: cafcf04dac0542f1506980d8b9484b82b558e100
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/05/2020
ms.locfileid: "77018571"
---
# <a name="quickstart---configure-an-avs-private-cloud-environment"></a>Szybki Start — Konfigurowanie środowiska chmury prywatnej do automatycznej synchronizacji

W tym artykule dowiesz się, jak utworzyć chmurę prywatną do automatycznej synchronizacji i skonfigurować środowisko chmury prywatnej do automatycznej synchronizacji.

## <a name="before-you-begin"></a>Przed rozpoczęciem

Przejrzyj [wymagania wstępne dotyczące sieci](cloudsimple-network-checklist.md).

## <a name="sign-in-to-azure"></a>Zaloguj się w usłudze Azure

Zaloguj się do witryny Azure Portal pod adresem [https://portal.azure.com](https://portal.azure.com).

## <a name="create-an-avs-private-cloud"></a>Utwórz chmurę prywatną do automatycznej synchronizacji

Chmura prywatna w wersji zaautomatycznej to izolowany stos oprogramowania VMware obsługujący hosty ESXi, vCenter, sieci vSAN i NSX.

W portalu do automatycznej synchronizacji są zarządzane chmury prywatne. Mają własny serwer vCenter we własnej domenie zarządzania. Stos działa na dedykowanych węzłach i izolowanych węzłach sprzętowych bez systemu operacyjnego.

1. Wybierz pozycję **Wszystkie usługi**.
2. Wyszukaj **usługi automatycznej synchronizacji**.
3. Wybierz usługę automatycznej synchronizacji, w której chcesz utworzyć swoją chmurę prywatną do automatycznej synchronizacji.
4. W obszarze **Przegląd**kliknij pozycję **Utwórz swoją chmurę prywatną** , aby otworzyć nową kartę przeglądarki dla portalu automatycznej synchronizacji. Jeśli zostanie wyświetlony monit, zaloguj się przy użyciu poświadczeń logowania na platformie Azure. 

    ![Utwórz chmurę prywatną na platformie Azure](media/create-private-cloud-from-azure.png)

5. W portalu automatycznej synchronizacji Podaj nazwę chmury prywatnej do automatycznej synchronizacji.
6. Wybierz **lokalizację** chmury prywatnej automatycznej synchronizacji.
7. Wybierz **Typ węzła**, spójny z zainicjowaną obsługą na platformie Azure.
8. Określ **liczbę węzłów**. Do utworzenia chmury prywatnej automatycznej synchronizacji wymagane są co najmniej trzy węzły.

    ![Tworzenie automatycznej synchronizacji w chmurze prywatnej — informacje podstawowe](media/create-private-cloud-basic-info.png)

9. Kliknij przycisk **Dalej: Opcje zaawansowane**.
10. Wprowadź zakres CIDR dla podsieci vSphere/sieci vSAN. Upewnij się, że zakres CIDR nie nakłada się na żadną z lokalizacji lokalnych lub innych podsieci platformy Azure (sieci wirtualnych) lub z podsiecią bramy.

    **Opcje zakresu CIDR:** /24,/23,/22 lub/21. Zakres CIDR/24 obsługuje maksymalnie 26 węzłów, a/23 zakres CIDR obsługuje do 58 węzłów, a zakres CIDR/22 i/21 obsługuje węzły 64 (maksymalną liczbę węzłów w chmurze prywatnej automatycznej synchronizacji). Aby dowiedzieć się więcej o sieciach VLAN i podsieciach, zobacz [sieci VLAN i podsieci — Omówienie](cloudsimple-vlans-subnets.md).

      > [!IMPORTANT]
      > Adresy IP w zakresie CIDR vSphere/sieci vSAN są zarezerwowane do użytku przez infrastrukturę chmury prywatnej do automatycznej synchronizacji. Nie używaj adresu IP z tego zakresu na żadnej maszynie wirtualnej.

11. Kliknij przycisk **Dalej: Przejrzyj i Utwórz**.
12. Przejrzyj ustawienia. Jeśli musisz zmienić dowolne ustawienia, kliknij przycisk **Wstecz**.
13. Kliknij pozycję **Utwórz**.

Trwa uruchamianie procesu inicjowania obsługi administracyjnej chmury prywatnej. Zainicjowanie obsługi automatycznej synchronizacji z chmurą prywatną może trwać do dwóch godzin.

## <a name="launch-avs-portal"></a>Uruchom Portal automatycznej synchronizacji

Możesz uzyskać dostęp do portalu automatycznej synchronizacji z poziomu Azure Portal. Portal automatycznej rejestracji zostanie uruchomiony przy użyciu poświadczeń logowania platformy Azure za pomocą logowania jednokrotnego (SSO). Dostęp do portalu automatycznej synchronizacji wymaga autoryzacji aplikacji **autoryzacji usługi automatycznej synchronizacji** . Aby uzyskać więcej informacji na temat przyznawania uprawnień, zobacz [wyrażanie zgody na aplikację autoryzacji usługi](access-cloudsimple-portal.md#consent-to-avs-service-authorization-application).

1. Wybierz pozycję **Wszystkie usługi**.
2. Wyszukaj **usługi automatycznej synchronizacji**.
3. Wybierz usługę automatycznej synchronizacji, w której chcesz utworzyć swoją chmurę prywatną do automatycznej synchronizacji.
4. W obszarze przegląd kliknij pozycję **Przejdź do portalu automatycznej synchronizacji** , aby otworzyć nową kartę przeglądarki dla portalu automatycznej synchronizacji. Jeśli zostanie wyświetlony monit, zaloguj się przy użyciu poświadczeń logowania na platformie Azure. 

    ![Uruchom Portal automatycznej synchronizacji](media/launch-cloudsimple-portal.png)

## <a name="create-point-to-site-vpn"></a>Tworzenie sieci VPN typu punkt-lokacja

Połączenie sieci VPN typu punkt-lokacja to najprostszy sposób nawiązywania połączenia z chmurą prywatną automatycznej synchronizacji z komputera użytkownika. Użyj połączenia sieci VPN typu punkt-lokacja, jeśli łączysz się zdalnie z chmurą prywatną. Aby uzyskać szybki dostęp do chmury prywatnej automatycznej synchronizacji, wykonaj poniższe czynności. Dostęp do regionu automatycznej synchronizacji w sieci lokalnej można wykonać przy użyciu [sieci VPN typu lokacja-lokacja](vpn-gateway.md) lub [usługi Azure ExpressRoute](on-premises-connection.md).

### <a name="create-gateway"></a>Utwórz bramę

1. Uruchom Portal automatycznej synchronizacji i wybierz pozycję **Sieć**.
2. Wybierz **VPN Gateway**.
3. Kliknij pozycję **nowe VPN Gateway**.

    ![Tworzenie bramy sieci VPN](media/create-vpn-gateway.png)

4. W obszarze **Konfiguracja bramy**określ poniższe ustawienia, a następnie kliknij przycisk **dalej**.

    * Wybierz **Sieć VPN typu punkt-lokacja** jako typ bramy.
    * Wprowadź nazwę identyfikującą bramę.
    * Wybierz lokalizację platformy Azure, w której wdrożono usługę automatycznej synchronizacji.
    * Określ podsieć klienta dla bramy punkt-lokacja. Po nawiązaniu połączenia będą nadawane adresy DHCP z tej podsieci.

5. W polu **połączenie/użytkownik**określ poniższe ustawienia, a następnie kliknij przycisk **dalej**.

    * Aby automatycznie zezwolić wszystkim bieżącym i przyszłym użytkownikom na dostęp do chmury prywatnej automatycznej synchronizacji za pomocą tej bramy punkt-lokacja, zaznacz opcję **automatycznie Dodaj wszystkich użytkowników**. Po wybraniu tej opcji Wszyscy użytkownicy z listy użytkowników są wybierani automatycznie. Możesz przesłonić opcję automatyczną, usuwając zaznaczenie poszczególnych użytkowników na liście.
    * Aby zaznaczyć tylko poszczególnych użytkowników, kliknij pola wyboru na liście użytkownik.

6. Sekcja sieci VLAN/podsieci pozwala określić zarządzanie i sieci VLAN użytkownika/podsieci dla bramy i połączeń.

    * Opcje **automatycznie Dodaj** ustawienia zasady globalne dla tej bramy. Ustawienia dotyczą bieżącej bramy. Ustawienia można przesłonić w obszarze **wyboru** .
    * Wybierz pozycję **Dodaj sieci VLAN zarządzania/podsieci chmur prywatnych**.
    * Aby dodać wszystkie zdefiniowane przez użytkownika podsieci/sieci VLAN, kliknij opcję **Dodaj zdefiniowane przez użytkownika sieci VLAN/podsieci**.
    * **Wybrane** ustawienia zastępują ustawienia globalne w obszarze **Automatyczne dodawanie**.

7. Kliknij przycisk **dalej** , aby przejrzeć ustawienia. Kliknij ikonę Edytuj, aby wprowadzić zmiany.
8. Kliknij przycisk **Utwórz** , aby utworzyć bramę sieci VPN.

### <a name="connect-to-avs-using-point-to-site-vpn"></a>Nawiązywanie połączenia z programem synchronizacji przy użyciu sieci VPN typu punkt-lokacja

Klient sieci VPN jest wymagany do nawiązywania połączenia z komputerem. Pobierz [klienta OpenVPN](https://openvpn.net/community-downloads/) dla systemu Windows lub [lepkości](https://www.sparklabs.com/viscosity/download/) dla macOS i OS X.

1. Uruchom Portal automatycznej synchronizacji i wybierz pozycję **Sieć**.
2. Wybierz **VPN Gateway**.
3. Na liście bram sieci VPN kliknij pozycję Brama sieci VPN typu punkt-lokacja.
4. Wybierz pozycję **Użytkownicy**.
5. Kliknij pozycję **Pobierz moją konfigurację sieci VPN**.

    ![Pobieranie konfiguracji sieci VPN](media/download-p2s-vpn-configuration.png)

6. Zaimportuj konfigurację na kliencie sieci VPN.

    * Instrukcje dotyczące [importowania konfiguracji w kliencie systemu Windows](https://openvpn.net/vpn-server-resources/connecting-to-access-server-with-windows/#openvpn-open-source-openvpn-gui-program)
    * Instrukcje dotyczące [importowania konfiguracji w systemie macOS lub OS X](https://www.sparklabs.com/support/kb/article/getting-started-with-viscosity-mac/#creating-your-first-connection)

7. Nawiąż połączenie z programem automatyczna synchronizacja.

## <a name="create-a-vlan-for-your-workload-vms"></a>Tworzenie sieci VLAN dla maszyn wirtualnych obciążenia

Po utworzeniu chmury prywatnej do automatycznej synchronizacji Utwórz sieć VLAN, w której będziesz wdrażać maszyny wirtualne obciążeń i aplikacji.

1. W portalu automatycznej synchronizacji wybierz pozycję **Sieć**.
2. Kliknij pozycję **VLAN/podsieci**.
3. Kliknij pozycję **Utwórz sieć VLAN/podsieć**.

    ![Utwórz sieć VLAN/podsieć](media/create-new-vlan-subnet.png)

4. Wybierz **chmurę prywatną automatycznej synchronizacji** dla nowej podsieci/sieci VLAN.
5. Wybierz z listy identyfikator sieci VLAN. 
6. Wprowadź nazwę podsieci, aby zidentyfikować podsieć.
7. Określ zakres i maskę maski podsieci. Ten zakres nie może pokrywać się z żadną istniejącą podsiecią.
8. Kliknij przycisk **Prześlij**.

    ![Utwórz szczegóły sieci VLAN/podsieci](media/create-new-vlan-subnet-details.png)

Zostanie utworzona sieć VLAN/podsieć. Możesz teraz używać tego identyfikatora sieci VLAN do tworzenia rozproszonej grupy portów w programie vCenter w chmurze prywatnej chmury.

## <a name="connect-your-environment-to-an-azure-virtual-network"></a>Łączenie środowiska z siecią wirtualną platformy Azure

Funkcja automatycznej synchronizacji oferuje obwód usługi ExpressRoute dla chmury prywatnej. Możesz połączyć sieć wirtualną na platformie Azure z obwodem usługi ExpressRoute. Aby uzyskać szczegółowe informacje na temat konfigurowania połączenia, wykonaj czynności opisane w temacie [Azure Virtual Network Connection przy użyciu ExpressRoute](https://docs.azure.cloudsimple.com/cloudsimple-azure-network-connection/).

## <a name="sign-in-to-vcenter"></a>Zaloguj się do programu vCenter

Teraz możesz zalogować się do programu vCenter, aby skonfigurować maszyny wirtualne i zasady.

1. Aby uzyskać dostęp do programu vCenter, Zacznij od portalu automatycznej synchronizacji. Na stronie głównej w obszarze **typowe zadania**kliknij pozycję **Uruchom program vSphere Client**. Wybierz chmurę prywatną automatycznej synchronizacji, a następnie kliknij pozycję **Uruchom klienta vSphere** w chmurze prywatnej automatycznej synchronizacji.

    ![Uruchom klienta vSphere](media/launch-vcenter-from-cloudsimple-portal.png)

2. Wybierz preferowanego klienta vSphere, aby uzyskać dostęp do programu vCenter i zalogować się przy użyciu nazwy użytkownika i hasła. Wartości domyślne to:
    * Nazwa użytkownika: **CloudOwner@AVS.local**
    * Hasło: **AVS123!**  

Ekrany programu vCenter w następnych procedurach pochodzą z klienta vSphere (HTML5).

## <a name="change-your-vcenter-password"></a>Zmień hasło programu vCenter

Automatyczna synchronizacja zaleca zmianę hasła przy pierwszym logowaniu się do programu vCenter. Ustawione hasło musi spełniać następujące wymagania:

* Maksymalny okres istnienia: należy zmienić hasło co 365 dni
* Ogranicz ponowne użycie: użytkownicy nie mogą ponownie używać żadnego z pięciu poprzednich haseł
* Długość: 8-20 znaków
* Znak specjalny: co najmniej jeden znak specjalny
* Znaki alfabetyczne: co najmniej jeden znak pisany wielkimi literami, A-Z i co najmniej jedną małą literę, a – z
* Liczby: co najmniej jeden znak numeryczny, 0-9
* Maksymalna liczba identycznych znaków sąsiednich: trzy

    Przykład: CC lub CCC jest akceptowalny jako część hasła, ale CCCC nie jest.

Jeśli ustawisz hasło, które nie spełnia wymagań:

* Jeśli używasz klienta programu vSphere Flash, zgłaszany jest błąd
* Jeśli używasz klienta HTML5, nie zgłasza on błędu. Klient nie akceptuje zmiany i nadal działa stare hasło.

## <a name="access-nsx-manager"></a>Dostęp do programu NSX Manager

Program NSX Manager jest wdrażany z domyślnym hasłem. 

* Nazwa użytkownika: **administrator**
* Hasło: **AVS123!**

W portalu automatycznej synchronizacji można znaleźć w pełni kwalifikowaną nazwę domeny (FQDN) i adres IP programu NSX Manager.

1. Uruchom Portal automatycznej synchronizacji i wybierz pozycję **zasoby**.
2. Kliknij chmurę prywatną do automatycznej synchronizacji, której chcesz użyć.
3. Wybieranie **sieci zarządzania vSphere**
4. Użyj nazwy FQDN lub adresu IP programu **NSX Manager** i nawiąż połączenie przy użyciu przeglądarki sieci Web.

    ![Znajdź nazwę FQDN programu NSX Manager](media/private-cloud-nsx-manager-fqdn.png)

## <a name="create-a-port-group"></a>Tworzenie grupy portów

Aby utworzyć rozproszoną grupę portów w vSphere:

1. Postępuj zgodnie z instrukcjami w temacie "Dodawanie rozproszonej grupy portów" w [przewodniku po sieci vSphere](https://docs.vmware.com/en/VMware-vSphere/6.5/vsphere-esxi-vcenter-server-65-networking-guide.pdf).
2. Podczas konfigurowania rozproszonej grupy portów podaj identyfikator sieci VLAN utworzony w temacie [Tworzenie sieci VLAN dla maszyn wirtualnych obciążenia](#create-a-vlan-for-your-workload-vms).

## <a name="next-steps"></a>Następne kroki

* [Korzystanie z maszyn wirtualnych VMware na platformie Azure](quickstart-create-vmware-virtual-machine.md)
* [Nawiązywanie połączenia z siecią lokalną za pomocą usługi Azure ExpressRoute](on-premises-connection.md)
* [Konfigurowanie sieci VPN typu lokacja-lokacja z poziomu lokalnego](vpn-gateway.md)
