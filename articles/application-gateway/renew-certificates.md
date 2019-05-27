---
title: Odnów certyfikat usługi Azure Application Gateway
description: Dowiedz się, jak odnowić certyfikat, który został skojarzony odbiornik bramy aplikacji.
services: application-gateway
author: vhorne
manager: jpconnock
ms.service: application-gateway
ms.topic: article
origin.date: 08/15/2018
ms.date: 03/12/2019
ms.author: v-junlch
ms.openlocfilehash: 90200f7be6c71346441922365fc4439111dd8701
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "66133693"
---
# <a name="renew-application-gateway-certificates"></a>Odnów certyfikaty bramy Application Gateway

W pewnym momencie należy odnowić certyfikaty użytkownika, jeśli skonfigurowano bramy application gateway dla szyfrowania SSL.

Można odnowić certyfikat, który został skojarzony odbiornik, albo w witrynie Azure portal, programu Azure PowerShell lub wiersza polecenia platformy Azure:

## <a name="azure-portal"></a>Azure Portal

Aby odnowić certyfikat odbiornika z poziomu portalu, przejdź do swojej odbiorników bramy aplikacji. Kliknij przycisk odbiornik, który ma certyfikat, który wymaga odnowienia, a następnie kliknij przycisk **Odnów lub Edytuj wybrany certyfikat**.

![Odnów certyfikat](./media/renew-certificate/ssl-cert.png)

Przekaż nowy certyfikat PFX, nadaj jej nazwę, wpisz hasło, a następnie kliknij **Zapisz**.

## <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Aby odnowić certyfikat przy użyciu programu Azure PowerShell, użyj następującego skryptu:

```azurepowershell
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

```azurecli
az network application-gateway ssl-cert update `
  -n "<CertName>" `
  --gateway-name "<AppGatewayName>" `
  -g "ResourceGroupName>" `
  --cert-file <PathToCerFile> `
  --cert-password "<password>"
```

## <a name="next-steps"></a>Kolejne kroki

Aby dowiedzieć się, jak skonfigurować odciążanie protokołu SSL za pomocą usługi Azure Application Gateway, zobacz [skonfigurować odciążanie protokołu SSL](application-gateway-ssl-portal.md)

<!-- Update_Description: code update -->