---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 03/17/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: c61378510fbfc8bdc13f35ba1063a0d9316d88e3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80066218"
---
## <a name="1-download-the-file"></a>1. Pobierz plik

Uruchom następujące polecenia. Skopiuj wynikowy adres URL do przeglądarki, aby pobrać plik zip profilu.

```azurepowershell-interactive
$profile = New-AzVpnClientConfiguration -ResourceGroupName AADAuth -Name AADauthGW -AuthenticationMethod "EapTls"
   
$PROFILE.VpnProfileSASUrl
```

## <a name="2-extract-the-zip-file"></a>2. Wyodrębnij plik zip

Wyodrębnij plik ZIP. Plik zawiera następujące foldery:

* Usługa AzureVPN
* Ogólny
* OpenVPN (Jeśli masz włączone ustawienia uwierzytelniania OpenVPN i Azure AD na bramie. W przypadku bramy sieci VPN zobacz [Tworzenie dzierżawy](../articles/vpn-gateway/openvpn-azure-ad-tenant.md). W przypadku wirtualnej sieci WAN zobacz [Tworzenie dzierżawy — VWAN](../articles/virtual-wan/openvpn-azure-ad-tenant.md).

## <a name="3-retrieve-information"></a>3. Pobieranie informacji

W folderze **AzureVPN** przejdź do pliku ***azurevpnconfig.xml*** i otwórz go za pomocą Notatnika. Zanotuj tekst między następującymi znacznikami.

```
<audience>          </audience>
<issuer>            </issuer>
<tennant>           </tennant>
<fqdn>              </fqdn>
<serversecret>      </serversecret>
```

## <a name="profile-details"></a>Szczegóły profilu

Po dodaniu połączenia użyj informacji zebranych w poprzednim kroku dla strony szczegółów profilu. Pola odpowiadają następującym informacjom:

   * **Publiczność:** Identyfikuje zasób odbiorcy, dla którego token jest przeznaczony
   * **Emitent:** Identyfikuje usługę tokenu zabezpieczeń (STS), która emitowała token, a także dzierżawę usługi Azure AD
   * **Najemca:** Zawiera niezmienny, unikatowy identyfikator dzierżawy katalogu, który wystawił token
   * **FQDN:** W pełni kwalifikowana nazwa domeny (FQDN) w bramie sieci VPN platformy Azure
   * **ServerSecret:** Klucz wstępny bramy sieci VPN

## <a name="folder-contents"></a>Zawartość folderu

* Folder **ogólny** zawiera publiczny certyfikat serwera i plik VpnSettings.xml. Plik VpnSettings.xml zawiera informacje potrzebne do skonfigurowania klienta ogólnego.

* Pobrany plik zip może również zawierać foldery **WindowsAmd64** i **WindowsX86.** Te foldery zawierają instalator dla klientów SSTP i IKEv2 dla systemu Windows. Aby je zainstalować, potrzebujesz praw administratora na kliencie.