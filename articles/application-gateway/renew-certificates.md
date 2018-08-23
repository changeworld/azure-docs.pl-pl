---
title: Odnów certyfikat usługi Azure Application Gateway
description: Dowiedz się, jak odnowić certyfikat, który został skojarzony odbiornik bramy aplikacji.
services: application-gateway
author: vhorne
manager: jpconnock
ms.service: application-gateway
ms.topic: article
ms.date: 8/15/2018
ms.author: victorh
ms.openlocfilehash: 48bd548ec977d2dc4dd3b5b2f34df04562a6e918
ms.sourcegitcommit: 744747d828e1ab937b0d6df358127fcf6965f8c8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/16/2018
ms.locfileid: "42054373"
---
# <a name="renew-application-gateway-certificates"></a>Odnów certyfikaty bramy Application Gateway

W pewnym momencie należy odnowić certyfikaty użytkownika, jeśli skonfigurowano bramy application gateway dla szyfrowania SSL.

Można odnowić certyfikat, który został skojarzony odbiornik, albo w witrynie Azure portal, programu Azure PowerShell lub wiersza polecenia platformy Azure:

## <a name="azure-portal"></a>Azure Portal

Aby odnowić certyfikat odbiornika z poziomu portalu, przejdź do swojej odbiorników bramy aplikacji. Kliknij przycisk odbiornik, który ma certyfikat, który wymaga odnowienia, a następnie kliknij przycisk **Odnów lub Edytuj wybrany certyfikat**.

![Odnów certyfikat](media/renew-certificate/ssl-cert.png)

Przekaż nowy certyfikat PFX, nadaj jej nazwę, wpisz hasło, a następnie kliknij **Zapisz**.

## <a name="azure-powershell"></a>Azure PowerShell

Aby odnowić certyfikat przy użyciu programu Azure PowerShell, użyj następującego skryptu:

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

Set-AzureRmApplicationGateway -ApplicationGateway $appgw
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

Aby dowiedzieć się, jak skonfigurować odciążanie protokołu SSL za pomocą usługi Azure Application Gateway, zobacz [skonfigurować odciążanie protokołu SSL](application-gateway-ssl-portal.md)
