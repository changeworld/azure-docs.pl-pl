---
title: Odnów certyfikat bramy aplikacji Azure
description: Jak odnowić certyfikat skojarzony z odbiornik bramy aplikacji.
services: application-gateway
author: vhorne
manager: jpconnock
ms.service: application-gateway
ms.topic: article
ms.date: 05/18/2018
ms.author: victorh
ms.openlocfilehash: b44a57fe8ebcc985d3ab66ea04936a1558d00863
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34598270"
---
# <a name="renew-application-gateway-certificates"></a>Odnów certyfikaty bramy aplikacji

W pewnym momencie należy odnowić certyfikaty użytkownika, po skonfigurowaniu bramy aplikacji do szyfrowania SSL.

Mogą odnowić certyfikat skojarzony z odbiornikiem przy użyciu albo portalu Azure, programu Azure PowerShell lub interfejsu wiersza polecenia Azure:

## <a name="azure-portal"></a>Azure Portal

Aby odnowić certyfikat odbiornika z portalu, przejdź do odbiorników bramy Twojej aplikacji. Kliknij przycisk odbiornik, który ma certyfikat, który ma zostać odnowiony, a następnie kliknij przycisk **odnawiania lub Edytuj wybrany certyfikat**.

![Odnawianie certyfikatu](media/renew-certificate/ssl-cert.png)

Przekaż nowy certyfikat PFX, nadaj mu nazwę, wpisz hasło, a następnie kliknij **zapisać**.

## <a name="azure-powershell"></a>Azure PowerShell

Aby odnowić certyfikat przy użyciu programu Azure PowerShell, użyj następującego polecenia cmdlet:

```azurepowershell-interactive
$appgw = Get-AzureRmApplicationGateway `
  -ResourceGroupName <ResourceGroup> `
  -Name <AppGatewayName>

$password = ConvertTo-SecureString `
  -String "<password>" `
  -Force `
  -AsPlainText

set-azureRmApplicationGatewaySSLCertificate -Name <oldcertname> `
-ApplicationGateway $appgw -CertificateFile <newcertPath> -Password $password
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

## <a name="next-steps"></a>Kolejne kroki

Aby dowiedzieć się, jak skonfigurować odciążanie protokołu SSL z bramy aplikacji Azure, zobacz [skonfigurować odciążanie protokołu SSL](application-gateway-ssl-portal.md)
