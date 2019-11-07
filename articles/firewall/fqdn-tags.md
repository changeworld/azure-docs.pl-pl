---
title: Omówienie tagów FQDN dla zapory platformy Azure
description: Dowiedz się więcej o tagach FQDN w zaporze platformy Azure
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 4/23/2019
ms.author: victorh
ms.openlocfilehash: d78f67b96776fa3fb1d13fbf7b385e41ca3aa59a
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/06/2019
ms.locfileid: "73665560"
---
# <a name="fqdn-tags-overview"></a>Omówienie tagów FQDN

Tag FQDN reprezentuje grupę w pełni kwalifikowanych nazw domen (FQDN) skojarzonych z dobrze znanymi usługami firmy Microsoft. Możesz użyć znacznika FQDN w regułach aplikacji, aby zezwolić na wymagany ruch sieciowy wychodzący przez zaporę.

Na przykład aby ręcznie zezwolić na Windows Update ruchu sieciowego przez zaporę, należy utworzyć wiele reguł aplikacji na podstawie dokumentacji firmy Microsoft. Za pomocą tagów FQDN można utworzyć regułę aplikacji, dołączyć tag **aktualizacje systemu Windows** i teraz ruch sieciowy do punktów końcowych firmy Microsoft Windows Update może przepływać przez zaporę.

Nie można tworzyć własnych tagów FQDN ani nie można określać, które nazwy FQDN są zawarte w tagu. Firma Microsoft zarządza nazwami FQDN, które obejmują tag FQDN, i aktualizuje tag w miarę zmiany nazw FQDN. 

<!--- screenshot of application rule with a FQDN tag.-->

W poniższej tabeli przedstawiono bieżące Tagi FQDN, których można użyć. Firma Microsoft zachowuje te Tagi i można oczekiwać, że dodatkowe Tagi będą dodawane okresowo.

## <a name="current-fqdn-tags"></a>Bieżące Tagi FQDN

|Tag FQDN  |Opis  |
|---------|---------|
|Windows Update     |Zezwalaj na dostęp wychodzący do Microsoft Update zgodnie z opisem w artykule [jak skonfigurować zaporę pod kątem aktualizacji oprogramowania](https://technet.microsoft.com/library/bb693717.aspx).|
|Diagnostyka systemu Windows|Zezwalaj na dostęp wychodzący do wszystkich [punktów końcowych diagnostyki systemu Windows](https://docs.microsoft.com/windows/privacy/configure-windows-diagnostic-data-in-your-organization#endpoints).|
|Usługa Microsoft Active Protection Service (MAPS)|Zezwalaj na dostęp wychodzący do [map](https://cloudblogs.microsoft.com/enterprisemobility/2016/05/31/important-changes-to-microsoft-active-protection-service-maps-endpoint/).|
|App Service Environment (ASE)|Zezwala na dostęp wychodzący do ruchu platformy ASE. Ten tag nie obejmuje magazynów specyficznych dla klienta i punktów końcowych SQL utworzonych przez środowisko ASE. Powinny one być włączane za pośrednictwem [punktów końcowych usługi](../virtual-network/tutorial-restrict-network-access-to-resources.md) lub dodawane ręcznie.<br><br>Aby uzyskać więcej informacji na temat integrowania zapory platformy Azure z środowiskiem ASE, zobacz [blokowanie App Service Environment](../app-service/environment/firewall-integration.md#configuring-azure-firewall-with-your-ase).|
|Azure Backup|Zezwala na dostęp wychodzący do usług Azure Backup.|
|Azure HDInsight|Zezwala na dostęp wychodzący dla ruchu platformy usługi HDInsight. Ten tag nie obejmuje magazynów specyficznych dla klienta ani ruchu SQL z usługi HDInsight. Włącz je za pomocą [punktów końcowych usługi](../virtual-network/tutorial-restrict-network-access-to-resources.md) lub Dodaj je ręcznie.|

> [!NOTE]
> W przypadku wybrania znacznika FQDN w regule aplikacji pole Protokół: Port musi być ustawione na **https**.

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się, jak wdrożyć zaporę platformy Azure, zobacz [Samouczek: wdrażanie i Konfigurowanie zapory platformy Azure przy użyciu Azure Portal](tutorial-firewall-deploy-portal.md).
