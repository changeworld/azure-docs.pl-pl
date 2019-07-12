---
title: Tworzenie nazwy FQDN dla maszyny Wirtualnej z systemem Windows w witrynie Azure portal | Dokumentacja firmy Microsoft
description: Dowiedz się, jak utworzyć w pełni kwalifikowaną nazwę domeny lub nazwę FQDN dla Menedżera zasobów na podstawie maszyny wirtualnej w witrynie Azure portal.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: gwallace
editor: tysonn
tags: azure-resource-manager
ms.assetid: a2ae5887-76df-485e-ae19-0efd96df8600
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 08/15/2018
ms.author: cynthn
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 48ad63cb39218f099d9128dae379c7df105764b9
ms.sourcegitcommit: dad277fbcfe0ed532b555298c9d6bc01fcaa94e2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/10/2019
ms.locfileid: "67723027"
---
# <a name="create-a-fully-qualified-domain-name-in-the-azure-portal-for-a-windows-vm"></a>Tworzenie w pełni kwalifikowanej nazwy domeny w witrynie Azure portal dla maszyny Wirtualnej z systemem Windows

Po utworzeniu maszyny wirtualnej (VM) w [witryny Azure portal](https://portal.azure.com), zasób publicznego adresu IP dla maszyny wirtualnej jest tworzony automatycznie. Ten adres IP umożliwia zdalny dostęp do maszyny Wirtualnej. Mimo że nie tworzy portalu [w pełni kwalifikowaną nazwę domeny](https://en.wikipedia.org/wiki/Fully_qualified_domain_name), lub w pełni kwalifikowaną nazwę domeny, możesz utworzyć je po utworzeniu maszyny Wirtualnej. W tym artykule przedstawiono kroki, aby utworzyć nazwę DNS lub nazwę FQDN.

## <a name="create-a-fqdn"></a>Tworzenie nazwy FQDN
W tym artykule założono, że już utworzono Maszynę wirtualną. Jeśli to konieczne, możesz [Utwórz Maszynę wirtualną w portalu](quick-create-portal.md) lub [przy użyciu programu Azure PowerShell](quick-create-powershell.md). Gdy maszyna wirtualna jest uruchomiona, wykonaj następujące kroki:

[!INCLUDE [virtual-machines-common-portal-create-fqdn](../../../includes/virtual-machines-common-portal-create-fqdn.md)]

Zapewnia teraz możliwość połączenia zdalne z maszyną wirtualną przy użyciu tej nazwy DNS takich jak dla protokołu RDP (Remote Desktop).

## <a name="next-steps"></a>Następne kroki
Teraz, że maszyna wirtualna ma nazwę publicznego adresu IP i systemu DNS, można wdrożyć typowych struktur aplikacji lub usług, takich jak usługi IIS, SQL lub programu SharePoint.

Można również poczytać więcej o [przy użyciu usługi Resource Manager](../../azure-resource-manager/resource-group-overview.md) zawiera porady na temat tworzenia wdrożeń platformy Azure.

