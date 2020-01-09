---
title: Tworzenie nazwy FQDN dla maszyny wirtualnej z systemem Windows w Azure Portal
description: Informacje na temat tworzenia w pełni kwalifikowanej nazwy domeny lub nazwy FQDN dla Menedżer zasobów maszyny wirtualnej opartej na Azure Portal.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: gwallace
editor: tysonn
tags: azure-resource-manager
ms.assetid: a2ae5887-76df-485e-ae19-0efd96df8600
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 08/15/2018
ms.author: cynthn
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: fa4e4bd7561a7a745e6eb0b70016144b9fb57998
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75371279"
---
# <a name="create-a-fully-qualified-domain-name-in-the-azure-portal-for-a-windows-vm"></a>Tworzenie w pełni kwalifikowanej nazwy domeny w Azure Portal dla maszyny wirtualnej z systemem Windows

Podczas tworzenia maszyny wirtualnej w [Azure Portal](https://portal.azure.com)zostanie automatycznie utworzony publiczny zasób IP dla maszyny wirtualnej. Ten adres IP jest używany do zdalnego uzyskiwania dostępu do maszyny wirtualnej. Mimo że portal nie tworzy w [pełni kwalifikowanej nazwy domeny](https://en.wikipedia.org/wiki/Fully_qualified_domain_name)lub nazwy FQDN, można utworzyć jedną po utworzeniu maszyny wirtualnej. W tym artykule przedstawiono procedurę tworzenia nazwy DNS lub FQDN.

## <a name="create-a-fqdn"></a>Utwórz nazwę FQDN
W tym artykule przyjęto założenie, że maszyna wirtualna została już utworzona. W razie konieczności można [utworzyć maszynę wirtualną w portalu](quick-create-portal.md) lub [za pomocą Azure PowerShell](quick-create-powershell.md). Wykonaj następujące kroki, gdy maszyna wirtualna jest uruchomiona:

[!INCLUDE [virtual-machines-common-portal-create-fqdn](../../../includes/virtual-machines-common-portal-create-fqdn.md)]

Teraz można połączyć się zdalnie z maszyną wirtualną przy użyciu tej nazwy DNS, takiej jak dla Remote Desktop Protocol (RDP).

## <a name="next-steps"></a>Następne kroki
Teraz, gdy maszyna wirtualna ma publiczny adres IP i nazwę DNS, można wdrażać typowe struktury aplikacji lub usługi, takie jak IIS, SQL lub SharePoint.

Więcej informacji na temat korzystania z [Menedżer zasobów](../../azure-resource-manager/management/overview.md) można znaleźć w tematach dotyczących tworzenia wdrożeń platformy Azure.

