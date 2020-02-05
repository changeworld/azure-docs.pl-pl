---
title: Azure VMware Solutions (Automatyczna synchronizacja) — Konfigurowanie sieci VPN między chmurą prywatną i automatyczna synchronizacja
description: Opisuje sposób konfigurowania połączenia sieci VPN typu lokacja-lokacja lub punkt-lokacja między siecią lokalną a chmurą prywatną do automatycznej synchronizacji
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/14/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: fbd2b227c9292593a7652044ef4c013bf0cfaf8e
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/05/2020
ms.locfileid: "77017007"
---
# <a name="configure-a-vpn-connection-to-your-avs-private-cloud"></a>Skonfiguruj połączenie sieci VPN z chmurą prywatną do automatycznej synchronizacji

Bramy sieci VPN umożliwiają łączenie się z siecią lokalną i zdalnie z komputera klienckiego. W tym artykule znajdują się informacje dotyczące konfigurowania bram sieci VPN w portalu automatycznej synchronizacji. Połączenie sieci VPN między siecią lokalną a siecią o automatycznej synchronizacji zapewnia dostęp do programu vCenter i obciążeń w chmurze prywatnej automatycznej synchronizacji. Automatyczna synchronizacja obsługuje zarówno bramę sieci VPN typu punkt-lokacja, jak i bramy sieci VPN typu lokacja-lokacja.

## <a name="vpn-gateway-types"></a>Typy bram sieci VPN

* Połączenie **sieci VPN typu punkt-lokacja** to najprostszy sposób nawiązywania połączenia z chmurą prywatną automatycznej synchronizacji z komputera. Za pomocą połączenia sieci VPN typu punkt-lokacja można zdalnie łączyć się z chmurą prywatną.
* Połączenie **sieci VPN typu lokacja-lokacja** umożliwia skonfigurowanie obciążeń chmur prywatnych w celu uzyskania dostępu do usług lokalnych. Możesz również użyć Active Directory lokalnego jako źródła tożsamości do uwierzytelniania w ramach programu vCenter w chmurze prywatnej chmury. Obecnie jest obsługiwany **oparty na zasadach typ sieci VPN** .

W regionie można utworzyć jedną bramę sieci VPN typu lokacja-lokacja i jedną bramę sieci VPN typu punkt-lokacja.

## <a name="point-to-site-vpn"></a>Sieci VPN typu punkt lokacja

Aby utworzyć bramę sieci VPN typu punkt-lokacja, zobacz [Tworzenie bramy sieci VPN typu punkt-lokacja](vpn-gateway.md#create-point-to-site-vpn-gateway).

### <a name="connect-to-avs-using-point-to-site-vpn"></a>Nawiązywanie połączenia z programem synchronizacji przy użyciu sieci VPN typu punkt-lokacja

Klient sieci VPN jest wymagany do nawiązywania połączenia z komputerem. Pobierz [klienta OpenVPN](https://openvpn.net/community-downloads/) dla systemu Windows lub [lepkości](https://www.sparklabs.com/viscosity/download/) dla macOS i OS X.

1. Uruchom Portal automatycznej synchronizacji i wybierz pozycję **Sieć**.
2. Wybierz **VPN Gateway**.
3. Na liście bram sieci VPN kliknij pozycję Brama sieci VPN typu punkt-lokacja.
4. Wybierz pozycję **Użytkownicy**.
5. Kliknij pozycję **Pobierz moją konfigurację sieci VPN**

    ![Pobieranie konfiguracji sieci VPN](media/download-p2s-vpn-configuration.png)

6. Importowanie konfiguracji na kliencie sieci VPN

    * Instrukcje dotyczące [importowania konfiguracji w kliencie systemu Windows](https://openvpn.net/vpn-server-resources/connecting-to-access-server-with-windows/#openvpn-open-source-openvpn-gui-program)
    * Instrukcje dotyczące [importowania konfiguracji w systemie macOS lub OS X](https://www.sparklabs.com/support/kb/article/getting-started-with-viscosity-mac/#creating-your-first-connection)

7. Nawiąż połączenie z usługą automatycznej synchronizacji z bramą sieci VPN.

W poniższym przykładzie pokazano Importowanie połączenia za pomocą **klienta lepkości**.

#### <a name="import-connection-on-viscosity-client"></a>Importuj połączenie na kliencie z lepką

1. Wyodrębnij zawartość konfiguracji sieci VPN z pobranego pliku zip.

2. Otwórz na komputerze.

3. Kliknij ikonę **+** i wybierz pozycję **Importuj połączenie** > **z pliku**.

    ![Importuj konfigurację sieci VPN z pliku](media/import-p2s-vpn-config.png)

4. Wybierz plik konfiguracji OpenVPN (. ovpn) dla protokołu, którego chcesz użyć, a następnie kliknij przycisk **Otwórz**.

    ![VPN](media/import-p2s-vpn-config-choose-ovpn.png)

Połączenie zostanie wyświetlone w menu lepkość.

#### <a name="connect-to-the-vpn"></a>Połącz się z siecią VPN

Aby nawiązać połączenie z siecią VPN za pomocą OpenVPNego, wybierz połączenie z menu. Ikona menu jest aktualizowana w celu wskazania, że połączenie zostało nawiązane.

![VPN](media/vis03.png)

### <a name="connecting-to-multiple-avs-private-clouds"></a>Łączenie z wieloma chmurami prywatnymi automatycznej synchronizacji

Połączenie sieci VPN typu punkt-lokacja rozwiązuje nazwy DNS pierwszej utworzonej chmury prywatnej automatycznej wersji. Jeśli chcesz uzyskać dostęp do innych chmur prywatnych automatycznej synchronizacji, musisz zaktualizować serwer DNS na kliencie sieci VPN.

1. Uruchom [Portal automatycznej synchronizacji](access-cloudsimple-portal.md).

2. Przejdź do **zasobów** > **automatycznej synchronizacji chmur prywatnych** i wybierz chmurę prywatną do automatycznej synchronizacji, z którą chcesz nawiązać połączenie.

3. Na stronie **Podsumowanie** w chmurze prywatnej automatyczna synchronizacja Skopiuj adres IP serwera DNS w chmurze automatycznej synchronizacji w obszarze **podstawowe informacje**.

    ![Automatyczna synchronizacja serwerów DNS w chmurze prywatnej](media/private-cloud-dns-server.png)

4. Kliknij prawym przyciskiem myszy ikonę lepkość na pasku systemowym komputera i wybierz pozycję **Preferencje**.

    ![VPN](media/vis00.png)

5. Wybierz połączenie sieci VPN do automatycznej synchronizacji.

    ![Połączenie sieci VPN](media/viscosity-client.png)

6. Kliknij przycisk **Edytuj** , aby zmienić właściwości połączenia.

    ![Edytowanie połączenia sieci VPN](media/viscosity-edit-connection.png)

7. Kliknij kartę **Sieć** , a następnie wprowadź adres IP serwera DNS w chmurze automatycznej synchronizacji rozdzielony przecinkami lub spacją i domeną jako ```AVS.io```. Wybierz opcję **Ignoruj ustawienia DNS wysyłane przez serwer sieci VPN**.

    ![Sieci VPN](media/viscosity-edit-connection-networking.png)

> [!IMPORTANT]
> Aby nawiązać połączenie z pierwszą chmurą prywatną w wersji zaautomatycznej, usuń te ustawienia i Połącz się z serwerem sieci VPN.

## <a name="site-to-site-vpn"></a>Sieci VPN typu lokacja lokacja

Aby utworzyć bramę sieci VPN typu lokacja-lokacja, zobacz [Tworzenie bramy sieci VPN typu lokacja-lokacja](vpn-gateway.md#set-up-a-site-to-site-vpn-gateway). Połączenia sieci VPN typu lokacja-lokacja z sieci lokalnej do chmury prywatnej automatycznej synchronizacji zapewniają te korzyści. 

* Dostępność narzędzia do automatycznej synchronizacji w chmurze prywatnej dla programu vCenter z dowolnej stacji roboczej w sieci lokalnej
* Używanie Active Directory lokalnego jako źródła tożsamości programu vCenter
* Wygodny transfer szablonów maszyn wirtualnych, obrazów ISO i innych plików z zasobów lokalnych do swojej automatycznej synchronizacji w chmurze prywatnej programu vCenter
* Dostępność obciążeń działających w chmurze prywatnej automatycznej synchronizacji z sieci lokalnej

Aby skonfigurować lokalną bramę sieci VPN w trybie wysokiej dostępności, zobacz [Konfigurowanie połączenia sieci VPN o wysokiej dostępności](high-availability-vpn-connection.md).

> [!IMPORTANT]
>    1. Na urządzeniu sieci VPN ustaw wartość opcji ograniczania TCP = 1200. Lub jeśli urządzenia sieci VPN nie obsługują funkcji ograniczania, można zamiast tego ustawić jednostkę MTU w interfejsie tunelu na 1240 bajtów.
> 2. Po skonfigurowaniu sieci VPN typu lokacja-lokacja Prześlij dalej żądania DNS dla *. AVS.io na serwery DNS chmury prywatnej do automatycznej synchronizacji. Postępuj zgodnie z instrukcjami w [lokalnej konfiguracji DNS](on-premises-dns-setup.md).
