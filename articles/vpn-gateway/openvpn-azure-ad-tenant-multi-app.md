---
title: 'Brama sieci VPN: dzierżawa usługi Azure AD dla różnych grup użytkowników: uwierzytelnianie usługi Azure AD'
description: Za pomocą sieci VPN P2S można łączyć się z siecią wirtualną przy użyciu uwierzytelniania usługi Azure AD
services: vpn-gateway
author: anzaman
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 02/19/2020
ms.author: alzam
ms.openlocfilehash: 118ea21cbdd2e0527659c7c1beb40d8e42fa1d10
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77485724"
---
# <a name="create-an-azure-active-directory-tenant-for-p2s-openvpn-protocol-connections"></a>Tworzenie dzierżawy usługi Azure Active Directory dla połączeń protokołów OpenVPN P2S

Podczas łączenia się z siecią wirtualną można użyć uwierzytelniania opartego na certyfikatach lub uwierzytelniania RADIUS. Jednak podczas korzystania z protokołu Open VPN można również użyć uwierzytelniania usługi Azure Active Directory. Jeśli chcesz, aby inny zestaw użytkowników mógł łączyć się z różnymi bramami sieci VPN, możesz zarejestrować wiele aplikacji w urzędzie obsługi ad i połączyć je z różnymi bramami sieci VPN. Ten artykuł ułatwia konfigurowanie dzierżawy usługi Azure AD dla uwierzytelniania P2S OpenVPN oraz tworzenie i rejestrowanie wielu aplikacji w usłudze Azure AD w celu umożliwienia różnych dostępu dla różnych użytkowników i grup.

> [!NOTE]
> Uwierzytelnianie usługi Azure AD jest obsługiwane tylko dla połączeń protokołu OpenVPN®.
>

[!INCLUDE [create](../../includes/openvpn-azure-ad-tenant-multi-app.md)]

## <a name="6-enable-authentication-on-the-gateway"></a><a name="enable-authentication"></a>6. Włącz uwierzytelnianie na bramie

W tym kroku włączysz uwierzytelnianie usługi Azure AD w bramie sieci VPN.

1. Włącz uwierzytelnianie usługi Azure AD w bramie sieci VPN, uruchamiając następujące polecenia. Pamiętaj, aby zmodyfikować polecenia, aby odzwierciedlić własne środowisko:

    ```azurepowershell-interactive
    $gw = Get-AzVirtualNetworkGateway -Name <name of VPN gateway> -ResourceGroupName <Resource group>
    Set-AzVirtualNetworkGateway -VirtualNetworkGateway $gw -VpnClientRootCertificates @()
    Set-AzVirtualNetworkGateway -VirtualNetworkGateway $gw -AadTenantUri "https://login.microsoftonline.com/<your Directory ID>" -AadAudienceId "application ID from previous section" -AadIssuerUri "https://sts.windows.net/<your Directory ID>/" -VpnClientAddressPool 192.168.0.0/24
    ```
    > [!NOTE]
    > Nie należy używać identyfikatora aplikacji klienta sieci Vpn platformy Azure w powyższych poleceniach: zapewni wszystkim użytkownikom dostęp do bramy sieci VPN. Użyj identyfikatora zarejestrowanych aplikacji.

2. Utwórz i pobierz profil, uruchamiając następujące polecenia. Zmień wartości -ResourcGroupName i -Name, aby dopasować swoje własne.

    ```azurepowershell-interactive
    $profile = New-AzVpnClientConfiguration -Name <name of VPN gateway> -ResourceGroupName <Resource group> -AuthenticationMethod "EapTls"
    $PROFILE.VpnProfileSASUrl
    ```

3. Po uruchomieniu poleceń, widać wynik podobny do poniższego. Skopiuj wynikowy adres URL do przeglądarki, aby pobrać plik zip profilu.

    ![Azure VPN](./media/openvpn-azure-ad-tenant-multi-app/profile.png)

4. Wyodrębnij pobrany plik zip.

5. Przejdź do rozpakowanego folderu "AzureVPN".

6. Zanotuj lokalizację pliku "azurevpnconfig.xml". Plik azurevpnconfig.xml zawiera ustawienie połączenia sieci VPN i można go zaimportować bezpośrednio do aplikacji klienta sieci VPN platformy Azure. Można również rozpowszechniać ten plik do wszystkich użytkowników, którzy muszą połączyć się za pośrednictwem poczty e-mail lub w inny sposób. Użytkownik będzie potrzebował prawidłowych poświadczeń usługi Azure AD, aby połączyć się pomyślnie.

## <a name="next-steps"></a>Następne kroki

Aby połączyć się z siecią wirtualną, należy utworzyć i skonfigurować profil klienta sieci VPN. Zobacz [Konfigurowanie klienta sieci VPN dla połączeń sieci VPN P2S](openvpn-azure-ad-client.md).
