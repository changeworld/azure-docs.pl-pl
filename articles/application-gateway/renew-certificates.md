---
title: Odnawianie certyfikatu bramy aplikacji platformy Azure
description: Dowiedz się, jak odnowić certyfikat skojarzony z detektorem bramy aplikacji.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 8/15/2018
ms.author: victorh
ms.openlocfilehash: 4c0c0ab84e60335f58ac075459b95cfabb9135ac
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/14/2020
ms.locfileid: "81311965"
---
# <a name="renew-application-gateway-certificates"></a>Odnawianie certyfikatów bramy aplikacji

W pewnym momencie należy odnowić certyfikaty, jeśli skonfigurowano bramę aplikacji do szyfrowania TLS/SSL.

Certyfikat skojarzony z detektorem można odnowić za pomocą witryny Azure Portal, Azure PowerShell lub interfejsu wiersza polecenia platformy Azure:

## <a name="azure-portal"></a>Azure Portal

Aby odnowić certyfikat odbiornika z portalu, przejdź do detektorów bramy aplikacji. Kliknij odbiornik, który ma certyfikat, który musi zostać odnowiony, a następnie kliknij przycisk **Odnów lub edytuj wybrany certyfikat**.

![Odnawianie certyfikatu](media/renew-certificate/ssl-cert.png)

Prześlij nowy certyfikat PFX, nadaj mu nazwę, wpisz hasło, a następnie kliknij przycisk **Zapisz**.

## <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Aby odnowić certyfikat przy użyciu programu Azure PowerShell, użyj następującego skryptu:

```azurepowershell-interactive
$appgw = Get-AzApplicationGateway `
  -ResourceGroupName <ResourceGroup> `
  -Name <AppGatewayName>

$password = ConvertTo-SecureString `
  -String "<password>" `
  -Force `
  -AsPlainText

set-AzApplicationGatewaySSLCertificate -Name <oldcertname> `
-ApplicationGateway $appgw -CertificateFile <newcertPath> -Password $password

Set-AzApplicationGateway -ApplicationGateway $appgw
```
## <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure

```azurecli-interactive
az network application-gateway ssl-cert update \
  -n "<CertName>" \
  --gateway-name "<AppGatewayName>" \
  -g "ResourceGroupName>" \
  --cert-file <PathToCerFile> \
  --cert-password "<password>"
```

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się, jak skonfigurować odciążanie TLS za pomocą bramy aplikacji platformy Azure, zobacz [Konfigurowanie odciążania protokołu TLS](application-gateway-ssl-portal.md)
