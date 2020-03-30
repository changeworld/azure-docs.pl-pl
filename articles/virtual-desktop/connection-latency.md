---
title: Opóźnienie połączenia użytkownika pulpitu wirtualnego systemu Windows — platforma Azure
description: Opóźnienie połączenia dla użytkowników pulpitu wirtualnego systemu Windows.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 10/30/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: a4210947d771768943775a3e62c2558fa2883bd5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79128180"
---
# <a name="determine-user-connection-latency-in-windows-virtual-desktop"></a>Określanie opóźnienia połączenia użytkownika na pulpicie wirtualnym systemu Windows

Pulpit wirtualny systemu Windows jest dostępny na całym świecie. Administratorzy mogą tworzyć maszyny wirtualne (VM) w dowolnym regionie platformy Azure. Opóźnienie połączenia będzie się różnić w zależności od lokalizacji użytkowników i maszyn wirtualnych. Usługi pulpitu wirtualnego systemu Windows będą stale wdrażane w nowych lokalizacjach geograficznych w celu zwiększenia opóźnień. 
 
[Narzędzie Do szacowania środowiska pulpitu wirtualnego systemu Windows](https://azure.microsoft.com/services/virtual-desktop/assessment/) może pomóc w określeniu najlepszej lokalizacji w celu optymalizacji opóźnienia maszyn wirtualnych. Zalecamy korzystanie z narzędzia co dwa do trzech miesięcy, aby upewnić się, że optymalna lokalizacja nie uległa zmianie, ponieważ pulpit wirtualny systemu Windows wprowadza się do nowych obszarów. 

## <a name="azure-traffic-manager"></a>Azure Traffic Manager

Pulpit wirtualny systemu Windows używa usługi Azure Traffic Manager, która sprawdza lokalizację serwera DNS użytkownika w celu znalezienia najbliższego wystąpienia usługi pulpitu wirtualnego systemu Windows. Przed wybraniem lokalizacji maszyn wirtualnych zalecamy administratorom przejrzenie lokalizacji serwera DNS użytkownika.

## <a name="next-steps"></a>Następne kroki

- Aby sprawdzić najlepszą lokalizację pod kątem optymalnego opóźnienia, zobacz [narzędzie Do szacowania wydajności pulpitu wirtualnego systemu Windows](https://azure.microsoft.com/services/virtual-desktop/assessment/).
- Aby zapoznać się z planami cenowymi, zobacz [Ceny pulpitu wirtualnego systemu Windows](https://azure.microsoft.com/pricing/details/virtual-desktop/).
- Aby rozpocząć wdrażanie pulpitu wirtualnego systemu Windows, zapoznaj się z [naszym samouczkiem](tenant-setup-azure-active-directory.md).