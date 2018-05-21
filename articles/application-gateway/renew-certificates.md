---
title: Odnów certyfikat bramy aplikacji Azure
description: Jak odnowić certyfikat skojarzony z odbiornik bramy aplikacji.
services: application-gateway
author: vhorne
manager: jpconnock
ms.service: application-gateway
ms.topic: article
ms.date: 05/17/2018
ms.author: victorh
ms.openlocfilehash: b125f707e8de17764701e981736a53492e5e756c
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/20/2018
---
# <a name="renew-application-gateway-certificates"></a>Odnów certyfikaty bramy aplikacji

W pewnym momencie należy odnowić certyfikaty użytkownika, po skonfigurowaniu bramy aplikacji do szyfrowania SSL.

Mogą odnowić certyfikat skojarzony z odbiornikiem przy użyciu portalu Azure lub programu Azure PowerShell:

## <a name="azure-portal"></a>Azure Portal

Aby odnowić certyfikat odbiornika z portalu, przejdź do odbiorników bramy Twojej aplikacji. Kliknij przycisk odbiornik, który ma certyfikat, który ma zostać odnowiony, a następnie kliknij przycisk **odnawiania lub Edytuj wybrany certyfikat**.

![Odnawianie certyfikatu](media/renew-certificate/ssl-cert.png)

Przekaż nowy certyfikat PFX, nadaj mu nazwę, wpisz hasło, a następnie kliknij **zapisać**.

## <a name="azure-powershell"></a>Azure PowerShell

Aby odnowić certyfikat przy użyciu programu Azure PowerShell, użyj następującego polecenia cmdlet:

```PowerShell
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

## <a name="next-steps"></a>Kolejne kroki

Aby dowiedzieć się, jak skonfigurować odciążanie protokołu SSL z bramy aplikacji Azure, zobacz [skonfigurować odciążanie protokołu SSL](application-gateway-ssl-portal.md)
