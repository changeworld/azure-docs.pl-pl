---
title: Wprowadzenie do topologii w obserwatora sieciowego Azure | Dokumentacja firmy Microsoft
description: Ta strona zawiera przegląd możliwości topologii obserwatora sieciowego
services: network-watcher
documentationcenter: na
author: jimdial
manager: timlt
editor: ''
ms.assetid: e753a435-38e0-482b-846b-121cb547555c
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: jdial
ms.openlocfilehash: d5cb5ba431eeae1956a9dbf1d48561c66faef9a6
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/11/2017
ms.locfileid: "23863944"
---
# <a name="introduction-to-topology-in-azure-network-watcher"></a>Wprowadzenie do topologii w obserwatora sieciowego Azure

Topologia zwraca wykres zasobów sieciowych w sieci wirtualnej. Wykres przedstawia połączenia między zasobami do reprezentowania łączność sieciową pełnego.

![Omówienie topologii][1]

W portalu topologii zwraca obiekty zasobów na poszczególnych sieci wirtualnej. Relacje są określone przez linie między zasobami zasobów poza region obserwatora sieciowego, nawet jeśli w zasobie grupy nie zostaną wyświetlone. Zasoby zwracane w widoku portalu są podzbiorem składników sieciowych, które są wyświetlone na wykresie. Aby wyświetlić pełną listę zasobów sieciowych, można użyć [PowerShell](network-watcher-topology-powershell.md) lub [REST](network-watcher-topology-rest.md)

> [!NOTE]
> Wystąpienie Monitora sieci jest wymagana w każdym regionie, który chcesz uruchomić topologii.

Ponieważ zasoby są zwracane połączenia między nimi są modelowane w obszarze dwie relacje.

- **Zawieranie** — przykład: sieć wirtualna zawiera podsieci, która zawiera karty Sieciowej
- **Skojarzone** — przykład: A kart interfejsu Sieciowego jest skojarzona z maszyną Wirtualną

### <a name="next-steps"></a>Następne kroki

Dowiedz się, jak za pomocą programu PowerShell można pobrać widoku topologii odwiedzając [topologii obserwatora sieciowego przy użyciu programu PowerShell](network-watcher-topology-powershell.md)

<!--Image references-->

[1]: ./media/network-watcher-topology-overview/topology.png
