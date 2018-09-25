---
title: Omówienie tagi nazwy FQDN dla zapory usługi Azure
description: Dowiedz się więcej o FQDN znaczniki zapory usługi Azure
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 9/24/2018
ms.author: victorh
ms.openlocfilehash: 536c0915cae17aa6f4201c62eae5f5b077805274
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/24/2018
ms.locfileid: "46999481"
---
# <a name="fqdn-tags-overview"></a>Omówienie tagi nazwy FQDN

FQDN tag reprezentuje grupę w pełni kwalifikowanych nazw domen (FQDN) skojarzony z dobrze znanych usług firmy Microsoft. Tag nazwy FQDN w regułach aplikacji służy do zezwalania wymagane wychodzącego ruchu sieciowego przez zaporę.

>[!NOTE]
>Funkcja znaczniki nazwy FQDN jest obecnie dostępna w programie Azure PowerShell i REST tylko.

Na przykład aby ręcznie zezwolić na Windows Update ruchu sieciowego przez zaporę, należy utworzyć wiele reguł aplikacji na dokumentację firmy Microsoft. Za pomocą tagów w pełni kwalifikowaną nazwę domeny, można utworzyć regułę aplikacji, obejmują **aktualizacji Windows** tagów, a teraz ruch sieciowy do punktów końcowych może przepływać za pośrednictwem zapory usługi Microsoft Windows Update.

Nie można utworzyć własne tagi w pełni kwalifikowaną nazwę domeny ani nie możesz określić które nazw FQDN, które są uwzględniane w tagu. Firma Microsoft zarządza nazw FQDN obejmowanymi przez tag nazwy FQDN i aktualizuje tag jako zmiany nazwy FQDN. 

<!--- screenshot of application rule with a FQDN tag.-->

W poniższej tabeli przedstawiono bieżące znaczniki nazwy FQDN, których można użyć. Firma Microsoft udostępnia te znaczniki i można oczekiwać, że dodatkowe tagi do dodania okresowo.

|Nazwa FQDN tagu  |Opis  |
|---------|---------|
|Windows Update     |Zezwalaj na dostęp ruchu wychodzącego do usługi Microsoft Update zgodnie z opisem w [sposobu konfigurowania zapory dla aktualizacji oprogramowania](https://technet.microsoft.com/library/bb693717.aspx).|
|Diagnostyka systemu Windows|Zezwalaj na dostęp ruchu wychodzącego do wszystkich [punktów końcowych diagnostyki Windows](https://docs.microsoft.com/windows/privacy/configure-windows-diagnostic-data-in-your-organization#endpoints).|
|Usługa Microsoft Active Protection Service (MAPS)|Zezwalaj na dostęp ruchu wychodzącego do [mapy](https://cloudblogs.microsoft.com/enterprisemobility/2016/05/31/important-changes-to-microsoft-active-protection-service-maps-endpoint/).|
|Środowisko App Service Environment (ASE)|Umożliwia dostęp ruchu wychodzącego do środowiska ASE platformy ruchu. Ten tag nie obejmuje klienta SQL i Storage punkty końcowe utworzone przez środowisko ASE. Powinno być włączone za pomocą [punktów końcowych usługi](../virtual-network/tutorial-restrict-network-access-to-resources.md) lub dodać ręcznie.|
|Azure Backup|Umożliwia dostęp ruchu wychodzącego do usługi Azure Backup.

## <a name="next-steps"></a>Kolejne kroki

Informacje na temat wdrażania zapory platformy Azure, zobacz [samouczek: Wdrażanie i konfigurowanie zapory platformy Azure przy użyciu witryny Azure portal](tutorial-firewall-deploy-portal.md).