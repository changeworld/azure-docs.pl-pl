---
title: Włącz zaporę usługi Azure w wersji zapoznawczej
description: Włącz zaporę usługi Azure w wersji zapoznawczej za pomocą programu Azure PowerShell
author: vhorne
ms.service: firewall
services: firewall
ms.topic: article
ms.date: 7/11/2018
ms.author: victorh
ms.openlocfilehash: 263b16a419b5ff20a9b6d62860385f92c2a18f9c
ms.sourcegitcommit: df50934d52b0b227d7d796e2522f1fd7c6393478
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/12/2018
ms.locfileid: "38991315"
---
# <a name="enable-the-azure-firewall-public-preview"></a>Włącz zaporę usługi Azure w wersji zapoznawczej

[!INCLUDE [firewall-preview-notice](../../includes/firewall-preview-notice.md)]

## <a name="enable-using-azure-powershell"></a>Włącz przy użyciu programu Azure PowerShell

Aby włączyć zaporę usługi Azure w wersji zapoznawczej, użyj następujących poleceń programu Azure PowerShell:

```PowerShell
Register-AzureRmProviderFeature -FeatureName AllowRegionalGatewayManagerForSecureGateway -ProviderNamespace Microsoft.Network

Register-AzureRmProviderFeature -FeatureName AllowAzureFirewall -ProviderNamespace Microsoft.Network
```

Trwa rejestracja funkcji ukończyć do 30 minut. Twój status rejestracji można sprawdzić, uruchamiając następujące polecenia programu Azure PowerShell:

```PowerShell

Get-AzureRmProviderFeature -FeatureName AllowRegionalGatewayManagerForSecureGateway -ProviderNamespace Microsoft.Network

Get-AzureRmProviderFeature -FeatureName AllowAzureFirewall -ProviderNamespace Microsoft.Network
```
Po zakończeniu rejestracji, uruchom następujące polecenie:

```PowerShell
Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Network
```

## <a name="next-steps"></a>Kolejne kroki

- [Samouczek: Wdrażanie i konfigurowanie zapory platformy Azure przy użyciu witryny Azure portal](tutorial-firewall-deploy-portal.md)

