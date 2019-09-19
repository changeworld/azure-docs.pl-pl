---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: virtual-machines
author: ayshakeen
ms.service: virtual-machines
ms.topic: include
ms.date: 09/18/2019
ms.author: azcspmt;ayshak;cynthn
ms.custom: include file
ms.openlocfilehash: 0f528117b19a3f26b964dee7ae453fc184c8dddb
ms.sourcegitcommit: fad368d47a83dadc85523d86126941c1250b14e2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/19/2019
ms.locfileid: "71123077"
---
Usługa Azure Compute oferuje różne rozmiary maszyn wirtualnych, które są izolowane pod kątem konkretnego rodzaju sprzętu i przeznaczone dla jednego klienta.  Te rozmiary maszyn wirtualnych są dostosowane do obciążeń wymagających wysokiego stopnia izolacji od innych klientów, a zwłaszcza obciążeń związanych z takimi elementami jak zgodność z przepisami i wymogi prawne.  Klienci mogą również dodatkowo podzielić zasoby tych izolowanych maszyn wirtualnych, korzystając z [pomocy technicznej platformy Azure dla zagnieżdżonych maszyn wirtualnych](https://azure.microsoft.com/blog/nested-virtualization-in-azure/).

Wykorzystanie wyizolowanego rozmiaru gwarantuje, że Twoja maszyna wirtualna będzie jedyną uruchomioną w tym konkretnym wystąpieniu serwera.  Obecnie odizolowane oferty maszyn wirtualnych obejmują:
* Standard_E64is_v3
* Standard_E64i_v3
* Standard_M128ms
* Standard_GS5
* Standard_G5
* Standard_DS15_v2
* Standard_D15_v2
* Standard_F72s_v2

Więcej informacji o każdym dostępnym rozmiarze izolowanym można znaleźć [tutaj](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-memory).