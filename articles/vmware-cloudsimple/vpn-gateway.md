---
title: Azure VMware Solutions (Automatyczna synchronizacja) — Konfigurowanie bramy sieci VPN
description: Zawiera opis sposobu konfigurowania bramy sieci VPN typu punkt-lokacja i bramy sieci VPN typu lokacja-lokacja oraz tworzenia połączeń między siecią lokalną i chmurą prywatną.
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/14/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 8731f7a9ff7f2cab7516e43c62ddc1aac2668168
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/05/2020
ms.locfileid: "77016769"
---
# <a name="set-up-vpn-gateways-on-avs-network"></a>Konfigurowanie bram sieci VPN w sieci automatycznej synchronizacji

Bramy sieci VPN umożliwiają łączenie się z siecią lokalną i zdalnie z komputera klienckiego. Połączenie sieci VPN między siecią lokalną a siecią o automatycznej synchronizacji zapewnia dostęp do programu vCenter i obciążeń w chmurze prywatnej automatycznej synchronizacji. Automatyczna synchronizacja obsługuje zarówno sieci VPN typu lokacja-lokacja, jak i bramy sieci VPN typu punkt-lokacja.

## <a name="vpn-gateway-types"></a>Typy bram sieci VPN

* Połączenie **sieci VPN typu lokacja-lokacja** umożliwia skonfigurowanie obciążeń chmur prywatnych w celu uzyskania dostępu do usług lokalnych. Możesz również użyć Active Directory lokalnego jako źródła tożsamości do uwierzytelniania w ramach programu vCenter w chmurze prywatnej chmury. Obecnie obsługiwany jest tylko typ **sieci VPN oparty na zasadach** .
* Połączenie **sieci VPN typu punkt-lokacja** to najprostszy sposób nawiązywania połączenia z chmurą prywatną automatycznej synchronizacji z komputera. Użyj połączenia sieci VPN typu punkt-lokacja, aby zdalnie połączyć się z chmurą prywatną. Aby uzyskać informacje na temat instalowania klienta dla połączenia sieci VPN typu punkt-lokacja, zobacz [Konfigurowanie połączenia sieci VPN z chmurą prywatną](set-up-vpn.md).

W regionie można utworzyć jedną bramę sieci VPN typu punkt-lokacja i jedną bramę sieci VPN typu lokacja-lokacja.

## <a name="automatic-addition-of-vlansubnets"></a>Automatyczne dodawanie sieci VLAN/podsieci

Automatyczna synchronizacja bram sieci VPN zapewnia zasady dodawania sieci VLAN/podsieci do bram sieci VPN. Zasady umożliwiają określenie różnych reguł zarządzania sieci VLAN/podsieci oraz sieci VLAN/podsieci zdefiniowanych przez użytkownika. Reguły dotyczące zarządzania siecią VLAN/podsieci mają zastosowanie do wszystkich nowych chmur prywatnych w wersji zaautomatycznej. Reguły dla sieci VLAN i podsieci zdefiniowanych przez użytkownika umożliwiają automatyczne dodawanie nowych sieci VLAN/podsieci do istniejącej lub nowej chmury prywatnej automatycznej synchronizacji dla bramy sieci VPN typu lokacja-lokacja, zasady dla każdego połączenia należy zdefiniować.

Zasady dotyczące dodawania sieci VLAN/podsieci do bram sieci VPN dotyczą zarówno sieci VPN typu lokacja-lokacja, jak i bramy sieci VPN typu punkt-lokacja.

## <a name="automatic-addition-of-users"></a>Automatyczne dodawanie użytkowników

Brama sieci VPN typu punkt-lokacja umożliwia zdefiniowanie zasad automatycznego dodawania dla nowych użytkowników. Domyślnie wszyscy właściciele i współautorzy subskrypcji mają dostęp do portalu automatycznej synchronizacji. Użytkownicy są tworzeniu tylko wtedy, gdy Portal automatycznej synchronizacji jest uruchamiany po raz pierwszy. Wybranie pozycji **automatycznie Dodaj** reguły umożliwia każdemu nowemu użytkownikowi dostęp do sieci automatycznej synchronizacji przy użyciu połączenia sieci VPN typu punkt-lokacja.

## <a name="set-up-a-site-to-site-vpn-gateway"></a>Konfigurowanie bramy sieci VPN typu lokacja-lokacja

1. [Uzyskaj dostęp do portalu automatycznej synchronizacji](access-cloudsimple-portal.md) i wybierz pozycję **Sieć**.
2. Wybierz **VPN Gateway**.
3. Kliknij pozycję **nowe VPN Gateway**.

    ![Tworzenie bramy sieci VPN](media/create-vpn-gateway.png)

4. W obszarze **Konfiguracja bramy**określ poniższe ustawienia, a następnie kliknij przycisk **dalej**.

    * Wybierz **Sieć VPN typu lokacja-lokacja** jako typ bramy.
    * Wprowadź nazwę identyfikującą bramę.
    * Wybierz lokalizację platformy Azure, w której wdrożono usługę automatycznej synchronizacji.
    * Opcjonalnie Włącz wysoką dostępność.

    ![Tworzenie bramy sieci VPN typu lokacja-lokacja](media/create-vpn-gateway-s2s.png)

    > [!WARNING]
    > Włączenie wysokiej dostępności wymaga, aby lokalne urządzenie sieci VPN obsługiwało łączenie z dwoma adresami IP. Tej opcji nie można wyłączyć po wdrożeniu bramy sieci VPN.

5. Utwórz pierwsze połączenie z sieci lokalnej i kliknij przycisk **dalej**.

    * Wprowadź nazwę identyfikującą połączenie.
    * Dla adresu IP elementu równorzędnego wprowadź publiczny adres IP lokalnej bramy sieci VPN.
    * Wprowadź identyfikator równorzędny lokalnej bramy sieci VPN. Identyfikator elementu równorzędnego jest zazwyczaj publicznym adresem IP lokalnej bramy sieci VPN. Jeśli skonfigurowano określony identyfikator w bramie, wprowadź identyfikator.
    * Skopiuj klucz współużytkowany, który ma być używany na potrzeby połączenia z lokalnej bramy sieci VPN. Aby zmienić domyślny klucz współużytkowany i określić nowy, kliknij ikonę Edytuj.
    * W przypadku **prefiksów lokalnych**wprowadź prefiksy CIDR lokalnego, które będą miały dostęp do sieci automatycznej. Podczas tworzenia połączenia można dodać wiele prefiksów CIDR.

    ![Utwórz połączenie bramy sieci VPN typu lokacja-lokacja](media/create-vpn-gateway-s2s-connection.png)

6. Włącz sieć VLAN/podsieci w sieci prywatnej chmury automatycznej, do której będzie uzyskiwany dostęp z sieci lokalnej, a następnie kliknij przycisk **dalej**.

    * Aby dodać sieci VLAN/podsieci zarządzania, Włącz **Dodawanie sieci VLAN zarządzania/podsieci chmur prywatnych**. Podsieć zarządzania jest wymagana dla podsieci vMotion i sieci vSAN.
    * Aby dodać podsieci vMotion, Włącz **Dodawanie sieci vMotion chmur prywatnych**.
    * Aby dodać podsieci sieci vSAN, Włącz **Dodawanie podsieci sieci vSAN do chmur prywatnych**.
    * Zaznacz lub usuń zaznaczenie określonych sieci VLAN.

    ![Tworzenie połączenia](media/create-vpn-gateway-s2s-connection-vlans.png)

7. Przejrzyj ustawienia i kliknij pozycję **Prześlij**.

    ![Przegląd i tworzenie bramy sieci VPN typu lokacja-lokacja](media/create-vpn-gateway-s2s-review.png)

## <a name="create-point-to-site-vpn-gateway"></a>Tworzenie bramy sieci VPN typu punkt-lokacja

1. [Uzyskaj dostęp do portalu automatycznej synchronizacji](access-cloudsimple-portal.md) i wybierz pozycję **Sieć**.
2. Wybierz **VPN Gateway**.
3. Kliknij pozycję **nowe VPN Gateway**.

    ![Tworzenie bramy sieci VPN](media/create-vpn-gateway.png)

4. W obszarze **Konfiguracja bramy**określ poniższe ustawienia, a następnie kliknij przycisk **dalej**.

    * Wybierz **Sieć VPN typu punkt-lokacja** jako typ bramy.
    * Wprowadź nazwę identyfikującą bramę.
    * Wybierz lokalizację platformy Azure, w której wdrożono usługę automatycznej synchronizacji.
    * Określ podsieć klienta dla bramy punkt-lokacja. Po nawiązaniu połączenia będą nadawane adresy DHCP z podsieci klienta.

5. W polu **połączenie/użytkownik**określ poniższe ustawienia, a następnie kliknij przycisk **dalej**.

    * Aby automatycznie zezwolić wszystkim bieżącym i przyszłym użytkownikom na dostęp do chmury prywatnej automatycznej synchronizacji za pomocą bramy punkt-lokacja, wybierz pozycję **automatycznie Dodaj wszystkich użytkowników**. Po wybraniu opcji Wszyscy użytkownicy z listy użytkowników są wybierani automatycznie. Możesz przesłonić opcję automatyczną, usuwając zaznaczenie poszczególnych użytkowników na liście.
    * Aby wybrać poszczególnych użytkowników, kliknij pola wyboru na liście użytkowników.

6. Sekcja sieci VLAN/podsieci pozwala określić zarządzanie i sieci VLAN użytkownika/podsieci dla bramy i połączeń.

    * Opcje **automatycznie Dodaj** ustawiają globalne zasady dla bramy. Ustawienia dotyczą bieżącej bramy. Ustawienia można przesłonić w obszarze **wyboru** .
    * Wybierz pozycję **Dodaj sieci VLAN zarządzania/podsieci chmur prywatnych**. 
    * Aby dodać wszystkie zdefiniowane przez użytkownika podsieci/sieci VLAN, kliknij opcję **Dodaj zdefiniowane przez użytkownika sieci VLAN/podsieci**.
    * **Wybrane** ustawienia zastępują ustawienia globalne w obszarze **Automatyczne dodawanie**.

7. Kliknij przycisk **dalej** , aby przejrzeć ustawienia. Kliknij ikonę Edytuj, aby wprowadzić zmiany.
8. Kliknij przycisk **Utwórz** , aby utworzyć bramę sieci VPN.

### <a name="client-subnet-and-protocols-for-point-to-site-vpn-gateway"></a>Podsieć i protokoły klienta dla bramy sieci VPN typu punkt-lokacja

Brama sieci VPN typu punkt-lokacja umożliwia połączenia protokołów TCP i UDP. Wybierz protokół, który ma być używany podczas nawiązywania połączenia z komputera, wybierając konfigurację TCP lub UDP.

Skonfigurowana podsieć klienta jest używana zarówno dla klientów TCP, jak i UDP. Prefiks CIDR jest podzielony na dwie podsieci, jeden dla TCP i jeden dla klientów UDP. Wybierz maskę prefiksu na podstawie liczby użytkowników sieci VPN, którzy będą łączyć się współbieżnie. 

W poniższej tabeli przedstawiono liczbę współbieżnych połączeń klienta dla maski prefiksu.

| Maska prefiksu | /24 | /25 | /26 | /27 | /28 |
|-------------|-----|-----|-----|-----|-----|
| Liczba współbieżnych połączeń TCP | 124 | 60 | 28 | 12 | 4 |
| Liczba równoczesnych połączeń UDP | 124 | 60 | 28 | 12 | 4 |

Aby nawiązać połączenie przy użyciu sieci VPN typu punkt-lokacja, zobacz [łączenie z programem synchronizacji przy użyciu sieci VPN typu punkt-lokacja](set-up-vpn.md#connect-to-avs-using-point-to-site-vpn).
