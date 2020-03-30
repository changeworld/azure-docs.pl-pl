---
title: Omówienie tagów FQDN dla Zapory platformy Azure
description: Tag FQDN reprezentuje grupę w pełni kwalifikowanych nazw domen (FQDN) skojarzonych ze znanymi usługami firmy Microsoft.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 11/19/2019
ms.author: victorh
ms.openlocfilehash: 6396f8292a4c54f7fce237439f37c3e8156d59e8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74169051"
---
# <a name="fqdn-tags-overview"></a>Omówienie tagów FQDN

Tag FQDN reprezentuje grupę w pełni kwalifikowanych nazw domen (FQDN) skojarzonych ze znanymi usługami firmy Microsoft. Za pomocą tagu FQDN w regułach aplikacji można zezwolić na wymagany wychodzący ruch sieciowy przez zaporę.

Na przykład, aby ręcznie zezwolić na ruch sieciowy usługi Windows Update za pośrednictwem zapory, należy utworzyć wiele reguł aplikacji zgodnie z dokumentacją firmy Microsoft. Korzystając ze znaczników FQDN, można utworzyć regułę aplikacji, dołączyć znacznik **Aktualizacje systemu Windows,** a teraz ruch sieciowy do punktów końcowych usługi Microsoft Windows Update może przepływać przez zaporę.

Nie można tworzyć własnych tagów FQDN ani określać, które nazwy FQDN są zawarte w tagu. Firma Microsoft zarządza sieciami FQDN objętymi tagiem FQDN i aktualizuje znacznik w miarę zmiany nazwy FQDN. 

<!--- screenshot of application rule with a FQDN tag.-->

W poniższej tabeli przedstawiono bieżące znaczniki FQDN, których można użyć. Firma Microsoft przechowuje te tagi i można oczekiwać, że dodatkowe tagi będą okresowo dodawane.

## <a name="current-fqdn-tags"></a>Bieżące tagi FQDN

|Tag FQDN  |Opis  |
|---------|---------|
|Windows Update     |Zezwól na dostęp wychodzący do witryny Microsoft Update zgodnie z opisem w [temacie Jak skonfigurować zaporę dla aktualizacji oprogramowania](https://technet.microsoft.com/library/bb693717.aspx).|
|Diagnostyka systemu Windows|Zezwól na dostęp wychodzący do wszystkich [punktów końcowych diagnostyki systemu Windows](https://docs.microsoft.com/windows/privacy/configure-windows-diagnostic-data-in-your-organization#endpoints).|
|Usługa Microsoft Active Protection Service (MAPS)|Zezwól na dostęp wychodzący do [aplikacji MAPS](https://cloudblogs.microsoft.com/enterprisemobility/2016/05/31/important-changes-to-microsoft-active-protection-service-maps-endpoint/).|
|Środowisko usługi aplikacji (ASE)|Umożliwia dostęp wychodzący do ruchu na platformie ASE. Ten tag nie obejmuje specyficznych dla klienta punktów końcowych magazynu i SQL utworzonych przez firmę ASE. Powinny one być włączone za pośrednictwem [punktów końcowych usługi](../virtual-network/tutorial-restrict-network-access-to-resources.md) lub dodane ręcznie.<br><br>Aby uzyskać więcej informacji na temat integrowania Zapory platformy Azure z [środowiskiem](../app-service/environment/firewall-integration.md#configuring-azure-firewall-with-your-ase)ASE, zobacz Blokowanie środowiska usługi aplikacji .|
|Azure Backup|Umożliwia dostęp wychodzący do usług Azure Backup.|
|Azure HDInsight|Umożliwia dostęp wychodzący dla ruchu na platformie HDInsight. Ten tag nie obejmuje specyficzne dla klienta magazynu lub ruchu SQL z usługi HDInsight. Włącz je przy użyciu [punktów końcowych usługi](../virtual-network/tutorial-restrict-network-access-to-resources.md) lub dodaj je ręcznie.|

> [!NOTE]
> Podczas wybierania znacznika FQDN w regule aplikacji pole protocol:port musi być ustawione na **https**.

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się, jak wdrożyć Zaporę platformy Azure, zobacz [Samouczek: Wdrażanie i konfigurowanie Zapory platformy Azure przy użyciu witryny Azure Portal](tutorial-firewall-deploy-portal.md).
