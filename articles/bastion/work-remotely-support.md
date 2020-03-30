---
title: 'Praca zdalna przy użyciu bastionu Bastion: Bastion platformy Azure'
description: Na tej stronie opisano, jak można korzystać z usługi Azure Bastion, aby umożliwić pracę zdalną ze względu na pandemię COVID-19.
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: conceptual
ms.date: 03/25/2020
ms.author: mialdrid
ms.openlocfilehash: c0bba2560cccc208e2384421218ecebdfef65236
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80333064"
---
# <a name="working-remotely-using-azure-bastion"></a>Praca zdalna przy użyciu usługi Azure Bastion

Usługa Azure Bastion odgrywa kluczową rolę we wspieraniu scenariuszy pracy zdalnej, umożliwiając użytkownikom z łącznością z Internetem dostęp do maszyn wirtualnych platformy Azure. W szczególności umożliwia administratorom IT zarządzanie aplikacjami uruchomionymi na platformie Azure w dowolnym czasie i z dowolnego miejsca na całym świecie.

>[!NOTE]
>W tym artykule opisano, jak można korzystać z usługi Azure Bastion, platformy Azure, sieci firmy Microsoft i ekosystemu partnerów platformy Azure do pracy zdalnej i łagodzenia problemów sieciowych, które występują z powodu kryzysu COVID-19.
>

## <a name="securely-access-virtual-machines"></a>Bezpieczny dostęp do maszyn wirtualnych

W szczególności usługa Azure Bastion zapewnia bezpieczną i bezproblemową łączność RDP/SSH z maszynami wirtualnymi w sieci wirtualnej platformy Azure, bezpośrednio w witrynie Azure portal, bez użycia publicznego adresu IP. Aby uzyskać więcej informacji na temat architektury bastionu platformy Azure i kluczowych funkcji, zapoznaj się z [what is Azure Bastion](bastion-overview.md).

Usługa Azure Bastion jest wdrażana na sieć wirtualną, co oznacza, że firmy mogą konfigurować jedną usługę Azure Bastion i zarządzać nią, aby szybko obsługiwać zdalny dostęp użytkowników do maszyn wirtualnych w sieci wirtualnej platformy Azure. Aby uzyskać wskazówki dotyczące tworzenia bastionu platformy Azure i zarządzania nim, zobacz [Tworzenie hosta bastionu](bastion-create-host-portal.md).

## <a name="next-steps"></a>Następne kroki

* Skonfiguruj usługę Azure Bastion przy użyciu [witryny Azure portal](bastion-create-host-portal.md), [programu PowerShell](bastion-create-host-powershell.md)lub interfejsu wiersza polecenia platformy Azure.

* Przeczytaj często zadawane pytania dotyczące [bastionu, aby](bastion-faq.md) uzyskać dodatkowe informacje.
