---
title: Utwórz nazwę FQDN dla maszyny Wirtualnej systemu Windows, w portalu Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak utworzyć w pełni kwalifikowaną nazwę domeny lub nazwę FQDN dla Menedżera zasobów na podstawie maszyny wirtualnej w portalu Azure.
services: virtual-machines-windows
documentationcenter: ''
author: iainfoulds
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager
ms.assetid: a2ae5887-76df-485e-ae19-0efd96df8600
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 12/13/2017
ms.author: iainfou
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 8ebc1ef89b24a9aa21f39e5b05051c16351f08cd
ms.sourcegitcommit: 0e4491b7fdd9ca4408d5f2d41be42a09164db775
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/14/2017
ms.locfileid: "26707412"
---
# <a name="create-a-fully-qualified-domain-name-in-the-azure-portal-for-a-windows-vm"></a>Utwórz w pełni kwalifikowaną nazwą domeny w portalu Azure dla maszyny Wirtualnej systemu Windows

Po utworzeniu maszyny wirtualnej (VM) w [portalu Azure](https://portal.azure.com), zasób publicznego adresu IP dla maszyny wirtualnej jest tworzony automatycznie. Ten adres IP umożliwia zdalny dostęp do maszyny Wirtualnej. Mimo że nie tworzy portalu [pełną nazwę domeny](https://en.wikipedia.org/wiki/Fully_qualified_domain_name), lub w pełni kwalifikowaną nazwę domeny, można utworzyć po utworzeniu maszyny Wirtualnej. W tym artykule przedstawiono kroki, aby utworzyć nazwę DNS lub nazwy FQDN.

## <a name="create-a-fqdn"></a>Utwórz nazwę FQDN
W tym artykule przyjęto założenie, że utworzono już maszyny Wirtualnej. W razie potrzeby można [tworzenie maszyny Wirtualnej w portalu](quick-create-portal.md) lub [przy użyciu programu Azure PowerShell](quick-create-powershell.md). Po skonfigurowaniu i uruchomieniu maszyny Wirtualnej, wykonaj następujące kroki:

[!INCLUDE [virtual-machines-common-portal-create-fqdn](../../../includes/virtual-machines-common-portal-create-fqdn.md)]

Teraz można podłączyć zdalnego do maszyny Wirtualnej przy użyciu tej nazwy DNS takich jak dla protokołu RDP (Remote Desktop).

## <a name="next-steps"></a>Następne kroki
Teraz, gdy maszyna wirtualna ma nazwę publicznego adresu IP i DNS, można wdrożyć wspólnej struktury aplikacji lub usług, takich jak IIS, SQL i programu SharePoint.

Można również uzyskać więcej informacji [za pomocą Menedżera zasobów](../../azure-resource-manager/resource-group-overview.md) porady na temat budowania Azure wdrożeń.

