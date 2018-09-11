---
title: 'Generowanie i eksportowanie certyfikatów for Point-to-Site: Linux: interfejs wiersza polecenia: Azure | Dokumentacja firmy Microsoft'
description: Tworzenie certyfikatu głównego z podpisem własnym, wyeksportować klucz publiczny i wygenerować certyfikaty klienta przy użyciu systemu Linux (strongSwan) interfejsu wiersza polecenia.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: article
ms.date: 09/06/2018
ms.author: cherylmc
ms.openlocfilehash: 8647b3b3eda980dbd5d5ec368b6b4b13949ecaf1
ms.sourcegitcommit: f3bd5c17a3a189f144008faf1acb9fabc5bc9ab7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/10/2018
ms.locfileid: "44305744"
---
# <a name="generate-and-export-certificates-for-point-to-site-using-linux-strongswan-cli"></a>Generowanie i eksportowanie certyfikatów dla punkt-lokacja przy użyciu systemu Linux strongSwan interfejsu wiersza polecenia

Połączenia punkt-lokacja używają certyfikatów do uwierzytelniania. W tym artykule pokazano, jak utworzyć certyfikat główny z podpisem własnym i generowania certyfikatów klienta przy użyciu interfejsu wiersza polecenia systemu Linux i strongSwan. Jeśli potrzebujesz innego certyfikatu instrukcje, zobacz [Powershell](vpn-gateway-certificates-point-to-site.md) lub [MakeCert](vpn-gateway-certificates-point-to-site-makecert.md) artykułów.

> [!NOTE]
> Kroki opisane w tym artykule wymaga strongSwan.
>

Konfiguracja komputera używane na potrzeby procedury w tym artykule było następujące:

| | |
|---|---|
|**Komputer**| Ubuntu Server 16.04<br>ID_LIKE = debian<br>PRETTY_NAME = "Ubuntu 16.04.4 LTS"<br>VERSION_ID = "16.04" |
|**Zależności**| Protokół ikev2 strongswan polecenia apt-get install strongswan wtyczka eap-tls<br>polecenie apt-get install libstrongswan — standardowy — wtyczek |

## <a name="install-strongswan"></a>Zainstaluj strongSwan

1. `apt-get install strongswan-ikev2 strongswan-plugin-eap-tls`
2. `apt-get install libstrongswan-standard-plugins`

Aby uzyskać informacje o sposobie instalowania strongSwan przy użyciu graficznego interfejsu użytkownika, zobacz kroki w [konfiguracji klienta](point-to-site-vpn-client-configuration-azure-cert.md#install) artykułu.

## <a name="generate-keys-and-certificate"></a>Generowanie kluczy i certyfikatów

1. Generowanie certyfikatu urzędu certyfikacji.

  ```
  ipsec pki --gen --outform pem > caKey.pem
  ipsec pki --self --in caKey.pem --dn "CN=VPN CA" --ca --outform pem > caCert.pem
  ```
2. Drukowanie certyfikatu urzędu certyfikacji w formacie base64. Jest to format, który jest obsługiwany przez platformę Azure. Można będzie później przekazać na platformę Azure, jako część konfiguracji P2S.

  ```
  openssl x509 -in caCert.pem -outform der | base64 -w0 ; echo
  ```
3. Generowanie certyfikatu użytkownika.

  ```
  export PASSWORD="password"
  export USERNAME="client"

  ipsec pki --gen --outform pem > "${USERNAME}Key.pem"
  ipsec pki --pub --in "${USERNAME}Key.pem" | ipsec pki --issue --cacert caCert.pem --cakey caKey.pem --dn "CN=${USERNAME}" --san "${USERNAME}" --flag clientAuth --outform pem > "${USERNAME}Cert.pem"
  ```
4. Generowanie pakietu p12, zawierający certyfikat użytkownika. Ten pakiet będzie używany w następnych krokach podczas pracy z [plików konfiguracji klienta](point-to-site-vpn-client-configuration-azure-cert.md#linuxinstallcli).

  ```
  openssl pkcs12 -in "${USERNAME}Cert.pem" -inkey "${USERNAME}Key.pem" -certfile caCert.pem -export -out "${USERNAME}.p12" -password "pass:${PASSWORD}"
  ```

## <a name="next-steps"></a>Kolejne kroki

Kontynuuj konfiguracji punkt-lokacja, aby [tworzenie i instalowanie plików konfiguracji klienta sieci VPN](point-to-site-vpn-client-configuration-azure-cert.md#linuxinstallcli).