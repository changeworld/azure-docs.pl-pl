---
title: Tworzenie nazwy FQDN dla maszyny Wirtualnej z systemem Linux w witrynie Azure portal | Dokumentacja firmy Microsoft
description: Dowiedz się, jak utworzyć w pełni kwalifikowaną nazwę domeny lub nazwę FQDN dla Menedżera zasobów na podstawie maszyny wirtualnej w witrynie Azure portal.
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager
ms.assetid: 2cd6c249-a737-4a0a-b5ba-e1c09e551b30
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 08/15/2018
ms.author: cynthn
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 54d1f77823b982ee8f49122c46b0a01cb27390f2
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "62129421"
---
# <a name="create-a-fully-qualified-domain-name-in-the-azure-portal-for-a-linux-vm"></a>Tworzenie w pełni kwalifikowanej nazwy domeny w witrynie Azure portal dla maszyny Wirtualnej z systemem Linux

Po utworzeniu maszyny wirtualnej (VM) w [witryny Azure portal](https://portal.azure.com), zasób publicznego adresu IP dla maszyny wirtualnej jest tworzony automatycznie. Ten adres IP umożliwia zdalny dostęp do maszyny Wirtualnej. Mimo że nie tworzy portalu [w pełni kwalifikowaną nazwę domeny](https://en.wikipedia.org/wiki/Fully_qualified_domain_name), lub w pełni kwalifikowaną nazwę domeny, możesz dodać kategorię, po utworzeniu maszyny Wirtualnej. W tym artykule przedstawiono kroki, aby utworzyć nazwę DNS lub nazwę FQDN.

## <a name="create-a-fqdn"></a>Tworzenie nazwy FQDN
W tym artykule założono, że już utworzono Maszynę wirtualną. Jeśli to konieczne, możesz [Utwórz Maszynę wirtualną w portalu](quick-create-portal.md) lub [przy użyciu wiersza polecenia platformy Azure](quick-create-cli.md). Gdy maszyna wirtualna jest uruchomiona, wykonaj następujące kroki:

[!INCLUDE [virtual-machines-common-portal-create-fqdn](../../../includes/virtual-machines-common-portal-create-fqdn.md)]

Zapewnia teraz możliwość połączenia zdalne z maszyną wirtualną przy użyciu tej nazwy DNS, takich jak za pomocą `ssh azureuser@mydns.westus.cloudapp.azure.com`.

## <a name="next-steps"></a>Kolejne kroki
Teraz, że maszyna wirtualna ma nazwę publicznego adresu IP i systemu DNS, można wdrożyć w typowych struktur aplikacji lub usług, takich jak serwer nginx, MongoDB, platformy Docker, itp.

Można również poczytać więcej o [przy użyciu usługi Resource Manager](../../azure-resource-manager/resource-group-overview.md) zawiera porady na temat tworzenia wdrożeń platformy Azure.

