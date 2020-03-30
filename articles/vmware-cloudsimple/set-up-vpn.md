---
title: Rozwiązanie Azure VMware by CloudSimple — konfigurowanie sieci VPN między usługą lokalną a chmurą prywatną
description: W tym artykule opisano sposób konfigurowania połączenia sieci VPN między lokacją a siecią VPN typu lokacja-lokacja między siecią lokalną a chmurą CloudSimple Private Cloud
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/14/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: d000d8390375466232c7daac2a4a056ef424be79
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77087130"
---
# <a name="configure-a-vpn-connection-to-your-cloudsimple-private-cloud"></a>Konfigurowanie połączenia sieci VPN z chmurą prywatną CloudSimple

Bramy sieci VPN umożliwiają zdalne łączenie się z siecią CloudSimple z sieci lokalnej i z komputera klienckiego.  W tym artykule można znaleźć informacje na temat konfigurowania bram sieci VPN z portalu CloudSimple.  Połączenie sieci VPN między siecią lokalną a siecią CloudSimple zapewnia dostęp do centrum wirtualnego i obciążeń w chmurze prywatnej. CloudSimple obsługuje zarówno sieci VPN typu punkt-lokacja, jak i bramy sieci VPN typu lokacja lokacja.

## <a name="vpn-gateway-types"></a>Typy bram sieci VPN

* Połączenie **sieci VPN typu "point-to-Site"** to najprostszy sposób na połączenie z chmurą prywatną z komputera. Użyj łączności sieci VPN typu punkt-lokacja, aby zdalnie łączyć się z chmurą prywatną.
* Połączenie **sieci VPN między lokacjami** umożliwia skonfigurowanie obciążeń chmury prywatnej w celu uzyskania dostępu do usług lokalnych. Lokalna usługa Active Directory służy również jako źródło tożsamości do uwierzytelniania w centrum vCenter usługi Private Cloud.  Obecnie obsługiwany jest typ **sieci VPN oparty na zasadach.**

W regionie można utworzyć jedną bramę sieci VPN lokacja lokacja i jedną bramę sieci VPN typu punkt-lokacja.

## <a name="point-to-site-vpn"></a>Sieć VPN typu punkt-lokacja

Aby utworzyć bramę sieci VPN typu punkt-lokacja, zobacz [Tworzenie bramy sieci VPN typu punkt-lokacja](vpn-gateway.md#create-point-to-site-vpn-gateway).

### <a name="connect-to-cloudsimple-using-point-to-site-vpn"></a>Łączenie się z cloudsimple przy użyciu sieci VPN typu punkt-lokacja

Klient sieci VPN jest potrzebny do łączenia się z cloudsimple z komputera.  Pobierz [klienta OpenVPN](https://openvpn.net/community-downloads/) dla Windows lub [Lepkości](https://www.sparklabs.com/viscosity/download/) dla systemów macOS i OS X.

1. Uruchom portal CloudSimple i wybierz pozycję **Sieć**.
2. Wybierz **bramę sieci VPN**.
3. Z listy bram sieci VPN kliknij bramę sieci VPN typu punkt-lokacja.
4. Wybierz pozycję **Użytkownicy**.
5. Kliknij pobierz **konfigurację sieci VPN**

    ![Pobieranie konfiguracji sieci VPN](media/download-p2s-vpn-configuration.png)

6. Importowanie konfiguracji na kliencie sieci VPN

    * Instrukcje [dotyczące importowania konfiguracji na kliencie systemu Windows](https://openvpn.net/vpn-server-resources/connecting-to-access-server-with-windows/#openvpn-open-source-openvpn-gui-program)
    * Instrukcje [importowania konfiguracji w systemie macOS lub OS X](https://www.sparklabs.com/support/kb/article/getting-started-with-viscosity-mac/#creating-your-first-connection)

7. Połącz się z bramą sieci VPN CloudSimple.

Poniższy przykład przedstawia importowanie połączenia przy użyciu **klienta lepkości**.

#### <a name="import-connection-on-viscosity-client"></a>Importowanie połączenia na kliencie lepkości

1. Wyodrębnij zawartość konfiguracji sieci VPN z pobranego pliku .zip.

2. Otwórz lepkość na komputerze.

3. Kliknij **+** ikonę i wybierz pozycję **Importuj połączenie** > **z pliku**.

    ![Importowanie konfiguracji sieci VPN z pliku](media/import-p2s-vpn-config.png)

4. Wybierz plik konfiguracyjny OpenVPN (.ovpn) dla protokołu, którego chcesz użyć, i kliknij przycisk **Otwórz**.

    ![Sieć VPN](media/import-p2s-vpn-config-choose-ovpn.png)

Połączenie pojawi się teraz w menu Lepkość.

#### <a name="connect-to-the-vpn"></a>Połącz się z siecią VPN

Aby połączyć się z siecią VPN za pomocą klienta Viscosity OpenVPN, wybierz połączenie z menu. Ikona menu zostanie zaktualizowana, aby wskazać, że połączenie zostało nawiązane.

![Sieć VPN](media/vis03.png)

### <a name="connecting-to-multiple-private-clouds"></a>Łączenie się z wieloma chmurami prywatnymi

Połączenie sieci VPN typu punkt-lokacja rozwiązuje nazwy DNS pierwszej utworzonej chmury prywatnej. Aby uzyskać dostęp do innych chmur prywatnych, należy zaktualizować serwer DNS na kliencie sieci VPN.

1. Uruchom [portal CloudSimple](access-cloudsimple-portal.md).

2. Przejdź do chmury**prywatnej** **zasobów** > i wybierz chmurę prywatną, z którą chcesz się połączyć.

3. Na stronie **Podsumowanie** chmury prywatnej skopiuj adres IP serwera DNS private cloud w obszarze **Informacje podstawowe**.

    ![Serwery DNS w chmurze prywatnej](media/private-cloud-dns-server.png)

4. Kliknij prawym przyciskiem myszy ikonę Lepkość w zasobniku systemowym komputera i wybierz polecenie **Preferencje**.

    ![Sieć VPN](media/vis00.png)

5. Wybierz połączenie CloudSimple VPN.

    ![Połączenie sieci VPN](media/viscosity-client.png)

6. Kliknij **przycisk Edytuj,** aby zmienić właściwości połączenia.

    ![Edytowanie połączenia sieci VPN](media/viscosity-edit-connection.png)

7. Kliknij kartę **Sieć** i wprowadź adresy IP serwera DNS w chmurze prywatnej ```cloudsimple.io```oddzielone przecinkiem lub spacją, a domena jako domena jako .  Wybierz **pozycję Ignoruj ustawienia DNS wysyłane przez serwer sieci VPN**.

    ![Sieć VPN](media/viscosity-edit-connection-networking.png)

> [!IMPORTANT]
> Aby połączyć się z pierwszą chmurą prywatną, usuń te ustawienia i połącz się z serwerem sieci VPN.

## <a name="site-to-site-vpn"></a>Sieć VPN typu lokacja-lokacja

Aby utworzyć bramę sieci VPN typu lokacja lokacja, zobacz [Tworzenie bramy sieci VPN między lokacjami](vpn-gateway.md#set-up-a-site-to-site-vpn-gateway).  Połączenie sieci VPN lokacja-lokacja z sieci lokalnej z usługą Private Cloud zapewnia te korzyści.  

* Dostępność usługi Private Cloud vCenter z dowolnej stacji roboczej w sieci lokalnej
* Korzystanie z lokalnej usługi Active Directory jako źródła tożsamości vCenter
* Wygodne przesyłanie szablonów maszyn wirtualnych, obiektów ISO i innych plików z zasobów lokalnych do usługi Private Cloud vCenter
* Dostępność obciążeń działających w chmurze prywatnej z sieci lokalnej

Aby skonfigurować lokalną bramę sieci VPN w trybie wysokiej dostępności, zobacz [Konfigurowanie połączenia sieci VPN o wysokiej dostępności](high-availability-vpn-connection.md).

> [!IMPORTANT]
>    1. Ustaw TCP MSS Clamping na 1200 na urządzeniu VPN. Lub jeśli urządzenia sieci VPN nie obsługują zaciskania MSS, możesz alternatywnie ustawić jednostkę MTU w interfejsie tunelu na 1240 bajtów.
> 2. Po skonfigurowaniu sieci VPN między lokacjami należy przekazywać żądania DNS dla *.cloudsimple.io do serwerów DNS w chmurze prywatnej.  Postępuj zgodnie z instrukcjami w [lokalnej instalacji DNS](on-premises-dns-setup.md).
