---
title: Rozwiązanie Azure VMware by CloudSimple — konfigurowanie bramy sieci VPN
description: W tym artykule opisano sposób konfigurowania bramy sieci VPN typu lokacja i bramy sieci VPN typu lokacja lokacja oraz tworzenia połączeń między siecią lokalną a chmurą CloudSimple Private Cloud
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/14/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: a8b7e238333196381524d189904871fe5933c906
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79279496"
---
# <a name="set-up-vpn-gateways-on-cloudsimple-network"></a>Konfigurowanie bram sieci VPN w sieci CloudSimple

Bramy sieci VPN umożliwiają zdalne łączenie się z siecią CloudSimple z sieci lokalnej i z komputera klienckiego. Połączenie sieci VPN między siecią lokalną a siecią CloudSimple zapewnia dostęp do centrum wirtualnego i obciążeń w chmurze prywatnej. CloudSimple obsługuje zarówno sieci VPN typu lokacja, jak i bramy sieci VPN typu punkt-lokacja.

## <a name="vpn-gateway-types"></a>Typy bram sieci VPN

* Połączenie **sieci VPN między lokacjami** umożliwia skonfigurowanie obciążeń chmury prywatnej w celu uzyskania dostępu do usług lokalnych. Lokalna usługa Active Directory służy również jako źródło tożsamości do uwierzytelniania w centrum vCenter usługi Private Cloud.  Obecnie obsługiwany jest tylko typ **sieci VPN oparty na zasadach.**
* Połączenie **sieci VPN typu "point-to-Site"** to najprostszy sposób na połączenie z chmurą prywatną z komputera. Użyj łączności sieci VPN typu punkt-lokacja, aby zdalnie połączyć się z chmurą prywatną. Aby uzyskać informacje dotyczące instalowania klienta dla połączenia sieci VPN typu punkt-lokacja, zobacz [Konfigurowanie połączenia sieci VPN z chmurą prywatną](set-up-vpn.md).

W regionie można utworzyć jedną bramę sieci VPN typu punkt-lokacja i jedną bramę sieci VPN typu lokacja lokacja.

## <a name="automatic-addition-of-vlansubnets"></a>Automatyczne dodawanie sieci VLAN/podsieci

Bramy sieci VPN CloudSimple zapewniają zasady dodawania sieci VLAN/podsieci do bram sieci VPN.  Zasady umożliwiają określenie różnych reguł dla zarządzania siecią VLAN/podsieci i zdefiniowanych przez użytkownika sieci VLAN/podsieci.  Reguły zarządzania siecią VLAN/podsieci mają zastosowanie do wszystkich nowych utworzonych chmur prywatnych.  Reguły dla zdefiniowanych przez użytkownika sieci VLAN/podsieci umożliwiają automatyczne dodawanie nowych sieci VLAN/podsieci do istniejących lub nowych chmur prywatnych Dla bramy sieci VPN typu lokacja-lokacja definiuje się zasady dla każdego połączenia.

Zasady dotyczące dodawania sieci VLAN/podsieci do bram sieci VPN mają zastosowanie zarówno do sieci VPN typu lokacja lokacja, jak i do bram sieci VPN typu lokacja.

## <a name="automatic-addition-of-users"></a>Automatyczne dodawanie użytkowników

Brama sieci VPN typu punkt-lokacja umożliwia zdefiniowanie zasad automatycznego dodawania dla nowych użytkowników. Domyślnie wszyscy właściciele i współautorzy subskrypcji mają dostęp do portalu CloudSimple.  Użytkownicy są tworzone tylko wtedy, gdy CloudSimple portal jest uruchamiany po raz pierwszy.  Wybranie **opcji Automatyczne dodawanie** reguł umożliwia każdemu nowemu użytkownikowi dostęp do sieci CloudSimple przy użyciu połączenia sieci VPN typu punkt-lokacja.

## <a name="set-up-a-site-to-site-vpn-gateway"></a>Konfigurowanie bramy sieci VPN między lokacjami

1. [Przejdź do portalu CloudSimple](access-cloudsimple-portal.md) i wybierz pozycję **Sieć**.
2. Wybierz **bramę sieci VPN**.
3. Kliknij **pozycję Nowa brama sieci VPN**.

    ![Tworzenie bramy sieci VPN](media/create-vpn-gateway.png)

4. W przypadku **konfiguracji bramy**należy określić następujące ustawienia i kliknąć przycisk **Dalej**.

    * Wybierz **sieć VPN lokacja** jako typ bramy.
    * Wprowadź nazwę, aby zidentyfikować bramę.
    * Wybierz lokalizację platformy Azure, w której wdrożona jest usługa CloudSimple.
    * Opcjonalnie włącz wysoką dostępność.

    ![Tworzenie bramy sieci VPN między lokacjami](media/create-vpn-gateway-s2s.png)

    > [!WARNING]
    > Włączenie wysokiej dostępności wymaga lokalnego urządzenia sieci VPN do obsługi łączenia się z dwoma adresami IP. Tej opcji nie można wyłączyć po wdrożeniu bramy sieci VPN.

5. Utwórz pierwsze połączenie z sieci lokalnej i kliknij przycisk **Dalej**.

    * Wprowadź nazwę, aby zidentyfikować połączenie.
    * W przypadku równorzędnego adresu IP wprowadź publiczny adres IP lokalnej bramy sieci VPN.
    * Wprowadź identyfikator równorzędny lokalnej bramy sieci VPN.  Identyfikator elementu równorzędnego jest zwykle publiczny adres IP lokalnej bramy sieci VPN.  Jeśli w bramie skonfigurowano określony identyfikator, wprowadź identyfikator.
    * Skopiuj klucz udostępniony do użycia w celu połączenia z lokalnej bramy sieci VPN.  Aby zmienić domyślny klucz udostępniony i określić nowy, kliknij ikonę edycji.
    * W przypadku **prefiksów lokalnych**wprowadź lokalne prefiksy CIDR, które będą uzyskiwać dostęp do sieci CloudSimple.  Podczas tworzenia połączenia można dodać wiele prefiksów CIDR.

    ![Tworzenie połączenia bramy sieci VPN między lokacjami](media/create-vpn-gateway-s2s-connection.png)

6. Włącz sieć VLAN/podsieci w sieci Private Cloud, do których będzie dostępny z sieci lokalnej, i kliknij przycisk **Dalej**.

    * Aby dodać zarządzanie siecią VLAN/podsieci, włącz **opcję Dodaj sieci VLAN/Podsieci chmur prywatnych do zarządzania**.  Podsieć zarządzania jest wymagana dla podsieci vMotion i vSAN.
    * Aby dodać podsieci vMotion, włącz **opcję Dodaj sieć vMotion chmur prywatnych**.
    * Aby dodać podsieci vSAN, włącz opcję **Dodaj podsieć chmur prywatnych**.
    * Wybierz lub odznaczyć określone sieci VLAN.

    ![Tworzenie połączenia](media/create-vpn-gateway-s2s-connection-vlans.png)

7. Przejrzyj ustawienia i kliknij przycisk **Prześlij**.

    ![Przeglądanie i tworzenie bramy sieci VPN między lokacjami](media/create-vpn-gateway-s2s-review.png)

## <a name="create-point-to-site-vpn-gateway"></a>Tworzenie bramy sieci VPN typu punkt-lokacja

1. [Przejdź do portalu CloudSimple](access-cloudsimple-portal.md) i wybierz pozycję **Sieć**.
2. Wybierz **bramę sieci VPN**.
3. Kliknij **pozycję Nowa brama sieci VPN**.

    ![Tworzenie bramy sieci VPN](media/create-vpn-gateway.png)

4. W przypadku **konfiguracji bramy**należy określić następujące ustawienia i kliknąć przycisk **Dalej**.

    * Wybierz sieć VPN typu sieć WEB typu typu bramy typu typu **wskaż** lokację.
    * Wprowadź nazwę, aby zidentyfikować bramę.
    * Wybierz lokalizację platformy Azure, w której wdrożona jest usługa CloudSimple.
    * Określ podsieć klienta bramy typu punkt-lokacja.  Podczas nawiązywania połączenia adresy DHCP będą podawane z podsieci klienta.

5. W przypadku **opcji Połączenie/Użytkownik**określ następujące ustawienia i kliknij przycisk **Dalej**.

    * Aby automatycznie zezwolić wszystkim obecnym i przyszłym użytkownikom na dostęp do chmury prywatnej za pośrednictwem bramy typu punkt-lokacja, wybierz pozycję **Automatycznie dodaj wszystkich użytkowników**. Po wybraniu tej opcji wszyscy użytkownicy na liście użytkowników są automatycznie wybierani. Opcję automatyczną można zastąpić, odznaczając poszczególnych użytkowników na liście.
    * Aby wybrać poszczególnych użytkowników, kliknij pola wyboru na liście użytkowników.

6. Sekcja Sieci/podsieci VLAN umożliwia określenie sieci/podsieci zarządzania i sieci VLAN dla bramy i połączeń.

    * Opcje **automatycznego dodawania** ustawiają zasady globalne bramy. Ustawienia dotyczą bieżącej bramy. Ustawienia można zastąpić w obszarze **Wybierz.**
    * Wybierz **pozycję Dodaj sieci VLAN/podsieci chmur prywatnych .** 
    * Aby dodać wszystkie sieci VLAN/podsieci zdefiniowane przez użytkownika, kliknij pozycję **Dodaj zdefiniowane przez użytkownika sieci VLAN/Podsieci**.
    * Ustawienia **wyboru** zastępują ustawienia globalne w obszarze **Automatycznie dodawaj**.

7. Kliknij **przycisk Dalej,** aby przejrzeć ustawienia. Kliknij ikony edycji, aby wprowadzić zmiany.
8. Kliknij **przycisk Utwórz,** aby utworzyć bramę sieci VPN.

### <a name="client-subnet-and-protocols-for-point-to-site-vpn-gateway"></a>Podsieć klienta i protokoły bramy sieci VPN typu punkt-lokacja

Brama sieci VPN typu punkt-lokacja umożliwia połączenia TCP i UDP.  Wybierz protokół używany podczas łączenia się z komputerem, wybierając konfigurację TCP lub UDP.

Skonfigurowana podsieć klienta jest używana zarówno dla klientów TCP, jak i UDP.  Prefiks CIDR jest podzielony na dwie podsieci, jedną dla protokołu TCP i jedną dla klientów UDP. Wybierz maskę prefiksu na podstawie liczby użytkowników sieci VPN, którzy będą łączyć się jednocześnie.  

W poniższej tabeli wymieniono liczbę równoczesnych połączeń klientów dla maski prefiksu.

| Maska prefiksu | /24 | /25 | /26 | /27 | /28 |
|-------------|-----|-----|-----|-----|-----|
| Liczba równoczesnych połączeń TCP | 124 | 60 | 28 | 12 | 4 |
| Liczba równoczesnych połączeń UDP | 124 | 60 | 28 | 12 | 4 |

Aby połączyć się przy użyciu sieci VPN typu punkt-lokacja, zobacz [Łączenie się z chmurąZaszej przy użyciu sieci VPN typu punkt-lokacja](set-up-vpn.md#connect-to-cloudsimple-using-point-to-site-vpn).
