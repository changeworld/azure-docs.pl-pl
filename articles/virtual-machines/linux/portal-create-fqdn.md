---
title: Utwórz nazwę FQDN dla maszyny Wirtualnej systemu Linux, w portalu Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak utworzyć w pełni kwalifikowaną nazwę domeny lub nazwę FQDN dla Menedżera zasobów na podstawie maszyny wirtualnej w portalu Azure.
services: virtual-machines-linux
documentationcenter: ''
author: iainfoulds
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager
ms.assetid: 2cd6c249-a737-4a0a-b5ba-e1c09e551b30
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 12/13/2017
ms.author: iainfou
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 81182f5a03821dc42aed2390d9a0145460b1ddd7
ms.sourcegitcommit: 0e4491b7fdd9ca4408d5f2d41be42a09164db775
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/14/2017
ms.locfileid: "26707291"
---
# <a name="create-a-fully-qualified-domain-name-in-the-azure-portal-for-a-linux-vm"></a>Utwórz w pełni kwalifikowaną nazwą domeny w portalu Azure dla maszyny Wirtualnej systemu Linux

Po utworzeniu maszyny wirtualnej (VM) w [portalu Azure](https://portal.azure.com), zasób publicznego adresu IP dla maszyny wirtualnej jest tworzony automatycznie. Ten adres IP umożliwia zdalny dostęp do maszyny Wirtualnej. Mimo że nie tworzy portalu [pełną nazwę domeny](https://en.wikipedia.org/wiki/Fully_qualified_domain_name), lub w pełni kwalifikowaną nazwę domeny, możesz dodać kategorię, po utworzeniu maszyny Wirtualnej. W tym artykule przedstawiono kroki, aby utworzyć nazwę DNS lub nazwy FQDN.

## <a name="create-a-fqdn"></a>Utwórz nazwę FQDN
W tym artykule przyjęto założenie, że utworzono już maszyny Wirtualnej. W razie potrzeby można [tworzenie maszyny Wirtualnej w portalu](quick-create-portal.md) lub [z wiersza polecenia platformy Azure](quick-create-cli.md). Po skonfigurowaniu i uruchomieniu maszyny Wirtualnej, wykonaj następujące kroki:

[!INCLUDE [virtual-machines-common-portal-create-fqdn](../../../includes/virtual-machines-common-portal-create-fqdn.md)]

Można teraz nawiązać połączenie zdalne maszyny Wirtualnej przy użyciu tej nazwy DNS, takich jak z `ssh azureuser@mydns.westus.cloudapp.azure.com`.

## <a name="next-steps"></a>Następne kroki
Teraz, gdy maszyna wirtualna ma nazwę publicznego adresu IP i DNS, można wdrożyć wspólnej struktury aplikacji lub usług, takich jak nginx, bazy danych MongoDB, Docker, itp.

Można również uzyskać więcej informacji [za pomocą Menedżera zasobów](../../azure-resource-manager/resource-group-overview.md) porady na temat budowania Azure wdrożeń.

