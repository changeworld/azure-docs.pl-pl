---
title: Rozszerzenie podsieci na platformie Azure | Dokumenty firmy Microsoft
description: Dowiedz się więcej o rozszerzeniu podsieci na platformie Azure.
services: virtual-network
documentationcenter: na
author: anupam-p
manager: narayan
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/31/2019
ms.author: anupand
ms.openlocfilehash: f718471c3f79e9a33b0e03b088f8c8d2ae0231d3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73587513"
---
# <a name="subnet-extension"></a>Rozszerzenie podsieci
Migracja obciążenia do chmury publicznej wymaga starannego planowania i koordynacji. Jedną z kluczowych kwestii może być możliwość zachowania adresów IP. Co może być ważne, zwłaszcza jeśli aplikacje mają zależność adresu IP lub masz wymagania dotyczące zgodności, aby używać określonych adresów IP. Usługa Azure Virtual Network rozwiązuje ten problem, umożliwiając tworzenie sieci wirtualnej i podsieci przy użyciu wybranego zakresu adresów IP.

Migracje mogą być nieco trudne, gdy powyższe wymaganie jest połączone z dodatkowym wymaganiem, aby zachować niektóre aplikacje lokalnie. W takiej sytuacji należy podzielić aplikacje między platformą Azure i lokalnie, bez ponownego numerowania adresów IP po obu stronach. Ponadto należy zezwolić aplikacjom na komunikowanie się tak, jakby były w tej samej sieci.

Jednym z rozwiązań powyższego problemu jest rozszerzenie podsieci. Rozszerzenie sieci umożliwia aplikacjom rozmawianie za pośrednictwem tej samej domeny emisji, gdy istnieją w różnych lokalizacjach fizycznych, eliminując konieczność ponownego wychwytywania topologii sieci. 

Chociaż rozszerzanie sieci nie jest ogólnie dobrą praktyką, poniższe przypadki użycia mogą to konieczne.

- **Migracja fazowa:** Najczęstszym scenariuszem jest faza migracji. Chcesz przenieść kilka aplikacji najpierw i z czasem migrować pozostałe aplikacje na platformę Azure.
- **Opóźnienie:** Wymagania o małym opóźnieniu może być kolejnym powodem, aby zachować niektóre aplikacje w środowisku lokalnym, aby upewnić się, że są one jak najbliżej centrum danych.
- **Zgodność:** Innym przypadkiem użycia jest to, że może być wymagane zgodność, aby zachować niektóre aplikacje w środowisku lokalnym.
 
> [!NOTE] 
> Nie należy rozszerzać podsieci, chyba że jest to konieczne. W przypadkach, gdy należy rozszerzyć podsieci, należy spróbować uczynić go krokiem pośrednim. Z czasem należy spróbować ponownie numer aplikacji w sieci lokalnej i migracji ich do platformy Azure.

W następnej sekcji omówimy, jak można rozszerzyć podsieci na platformę Azure.


## <a name="extend-your-subnet-to-azure"></a>Rozszerzanie podsieci na platformę Azure
 Można rozszerzyć podsieci lokalne na platformę Azure przy użyciu rozwiązania opartego na sieci nakładek warstwy 3. Większość rozwiązań korzysta z technologii nakładek, takich jak VXLAN, aby rozszerzyć sieć warstwy 2 za pomocą sieci nakładek warstwy 3. Poniższy diagram przedstawia uogólnione rozwiązanie. W tym rozwiązaniu ta sama podsieć istnieje po obu stronach, czyli azure i lokalnie. 

![Przykład rozszerzenia podsieci](./media/subnet-extension/subnet-extension.png)

Adresy IP z podsieci są przypisywane do maszyn wirtualnych na platformie Azure i lokalnie. Zarówno platformy Azure, jak i lokalnie mają wstawiony system WUS w swoich sieciach. Gdy maszyna wirtualna na platformie Azure próbuje rozmawiać z maszyną wirtualną w sieci lokalnej, usługa Azure WUS przechwytuje pakiet, hermetyzuje go i wysyła za pośrednictwem trasy SIECI VPN/Express do sieci lokalnej. Lokalna sieć WUS odbiera pakiet, decapsuluje go i przekazuje do zamierzonego odbiorcy w swojej sieci. Ruch zwraca używa podobnej ścieżki i logiki.

W powyższym przykładzie usługi Azure WUS i lokalnego urządzenia WUS komunikują się i dowiadują się o adresach IP za sobą. Bardziej złożone sieci mogą również mieć usługę mapowania, która utrzymuje mapowanie między sieciami WDO i adresami IP za nimi. Gdy urządzenie WUS odbiera pakiet, wysyła zapytanie do usługi mapowania, aby znaleźć adres urządzenia wuszyn, który ma za sobą docelowy adres IP.

W następnej sekcji znajdziesz szczegółowe informacje na temat rozwiązań rozszerzenia podsieci, które przetestowaliśmy na platformie Azure.

## <a name="next-steps"></a>Następne kroki 
[Rozszerz podsieć na platformę Azure przy użyciu rozwiązań dla dostawców.](https://github.com/microsoft/Azure-LISP)