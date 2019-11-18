---
title: 'Azure VPN Gateway: informacje o profilach klienta sieci VPN P2S'
description: Pomaga to w pracy z plikiem profilu klienta
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: article
ms.date: 11/04/2019
ms.author: cherylmc
ms.openlocfilehash: 5386cace7191be60534f0d2fbf4a85b592d1ecdd
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/17/2019
ms.locfileid: "74151622"
---
# <a name="about-p2s-vpn-client-profiles"></a>Informacje o profilach klienta sieci VPN P2S

Pobrany plik profilu zawiera informacje niezbędne do skonfigurowania połączenia sieci VPN. Ten artykuł pomoże Ci uzyskać i zrozumieć informacje niezbędne dla profilu klienta sieci VPN.

## <a name="1-download-the-file"></a>1. Pobierz plik

Uruchom następujące polecenia. Skopiuj adres URL wyniku do przeglądarki, aby pobrać plik zip profilu.

```azurepowershell-interactive
$profile = New-AzVpnClientConfiguration -ResourceGroupName AADAuth -Name AADauthGW -AuthenticationMethod "EapTls"
   
$PROFILE.VpnProfileSASUrl
```

## <a name="2-extract-the-zip-file"></a>2. Wyodrębnij plik zip

Wyodrębnij plik ZIP. Plik zawiera następujące foldery:

* AzureVPN
* Ogólny
* OpenVPN (Jeśli włączono ustawienia uwierzytelniania OpenVPN i Azure AD na bramie. Zobacz [Tworzenie dzierżawy](openvpn-azure-ad-tenant.md).)

## <a name="3-retrieve-information"></a>3. Pobieranie informacji

W folderze **AzureVPN** przejdź do pliku ***azurevpnconfig. XML*** i otwórz go w Notatniku. Zanotuj tekst między następującymi tagami.

```
<audience>          </audience>
<issuer>            </issuer>
<tennant>           </tennant>
<fqdn>              </fqdn>
<serversecret>      </serversecret>
```

## <a name="profile-details"></a>Szczegóły profilu

Po dodaniu połączenia użyj informacji zebranych w poprzednim kroku dla strony Szczegóły profilu. Pola odnoszą się do następujących informacji:

   * **Odbiorcy:** Identyfikuje zasób odbiorcy, dla którego jest przeznaczony token
   * **Wystawca:** Identyfikuje usługę tokenu zabezpieczającego (STS), która emituje token, a także dzierżawę usługi Azure AD
   * **Dzierżawca:** Zawiera niezmienny, unikatowy identyfikator dzierżawy katalogu, który wystawił token
   * **Nazwa FQDN:** W pełni kwalifikowana nazwa domeny (FQDN) w bramie sieci VPN platformy Azure
   * **ServerSecret:** Klucz wstępny bramy sieci VPN

## <a name="folder-contents"></a>Zawartość folderu

* **Folder OpenVPN** zawiera profil *OVPN* , który należy zmodyfikować w taki sposób, aby zawierał klucz i certyfikat. Aby uzyskać więcej informacji, zobacz [Konfigurowanie klientów OpenVPN dla platformy Azure VPN Gateway](vpn-gateway-howto-openvpn-clients.md#windows).

* **Folder ogólny** zawiera publiczny certyfikat serwera i plik VpnSettings. XML. Plik VpnSettings. xml zawiera informacje, które są konieczne do skonfigurowania klienta ogólnego.

* Pobrany plik zip może również zawierać foldery **WindowsAmd64** i **WindowsX86** . Foldery te zawierają Instalatora protokołu SSTP i IKEv2 dla klientów z systemem Windows. Aby je zainstalować, musisz mieć uprawnienia administratora na kliencie.

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat usługi Point-to-site, zobacz [Informacje o punkcie-to-site](point-to-site-about.md).
