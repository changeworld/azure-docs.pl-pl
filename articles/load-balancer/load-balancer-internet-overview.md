---
title: Omówienie usługi równoważenia obciążenia internetowy | Dokumentacja firmy Microsoft
description: Omówienie internetowy modułu równoważenia obciążenia i ich funkcje. Jak usługi równoważenia obciążenia działa na platformie Azure przy użyciu maszyn wirtualnych i usług w chmurze.
services: load-balancer
documentationcenter: na
author: KumudD
manager: timlt
editor: tysonn
ms.assetid: 529b37aa-a45c-41d1-8877-fee8cc1fa375
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: kumud
ms.openlocfilehash: 33a63538e3b96c5a37ddda6be31a0d972884b9b7
ms.sourcegitcommit: c3d53d8901622f93efcd13a31863161019325216
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2018
---
# <a name="internet-facing-load-balancer-overview"></a>Omówienie usługi równoważenia obciążenia połączonej Internet


Moduł równoważenia obciążenia Azure mapuje publiczny adres IP i port numer ruchu przychodzącego do prywatnego adresu IP adres i numer portu maszyny wirtualnej i odwrotnie dla ruchu odpowiedzi z maszyny wirtualnej. Reguły równoważenia obciążenia umożliwiają dystrybucji określonych rodzajów ruchu sieciowego między wiele maszyn wirtualnych lub usług. Na przykład mogą rozprzestrzeniać się obciążenie ruchu w sieci web żądania wielu serwerów sieci web lub role sieci web.

Dla usługi w chmurze, zawierającej wystąpienia ról sieci web lub roli proces roboczy można określić publiczny punkt końcowy w pliku definicji (csdef) usługi.

*Servicedefinition.csdef* plik zawiera konfigurację punktu końcowego i jeśli istnieje wiele wystąpień roli we wdrożeniu roli sieci web lub procesu roboczego Instalatora dla niego będzie usługa równoważenia obciążenia. Liczba wystąpień w pliku konfiguracji usługi (.csfg) zmienia sposób można dodać do wdrożenia chmury wystąpień.

## <a name="example-of-an-internet-facing-load-balancer"></a>Przykład internetowy modułu równoważenia obciążenia

Na poniższej ilustracji przedstawiono punktu końcowego równoważeniem obciążenia dla ruchu w sieci web współużytkowany trzech maszyn wirtualnych dla publicznych i prywatnych port TCP 80. Te trzy maszyny wirtualne są w zestawie o zrównoważonym obciążeniu.

![przykład modułu równoważenia obciążenia publiczny](./media/load-balancer-internet-overview/IC727496.png)

Rysunek 1 — równoważeniem obciążenia punktu końcowego dla ruchu w sieci web

Gdy klienci internetowi wysyłać żądania strony sieci web do publicznego adresu IP na porcie TCP 80 usługi w chmurze, usługi równoważenia obciążenia Azure dystrybuuje żądania między trzy maszyny wirtualne w zestawie równoważeniem obciążenia. Aby uzyskać więcej informacji dotyczących algorytmów równoważenia obciążenia, zobacz [strony Przegląd usługi równoważenia obciążenia podstawowych](load-balancer-overview.md#fundamental-load-balancer-features).

Domyślnie usługa równoważenia obciążenia Azure dystrybuuje ruch sieciowy równomiernie wielu wystąpień maszyny wirtualnej. Można również skonfigurować koligację sesji, aby uzyskać więcej informacji, zobacz [tryb dystrybucji modułu równoważenia obciążenia](load-balancer-distribution-mode.md).

## <a name="next-steps"></a>Kolejne kroki

Dowiedz się więcej o [wewnętrznego modułu równoważenia obciążenia](load-balancer-internal-overview.md) aby lepiej zrozumieć, w których usługi równoważenia obciążenia jest lepszym rozwiązaniem dla danego wdrożenia w chmurze.

Możesz również [rozpocząć tworzenie internetowy modułu równoważenia obciążenia](load-balancer-get-started-internet-arm-ps.md) i skonfigurować typ [trybu rozkładu](load-balancer-distribution-mode.md) dla zachowania ruchu sieci usługi równoważenia obciążenia określonego.

Jeśli zastosowanie wymaga zachowania działających połączeń z serwerami za modułem równoważenia obciążenia, zapoznaj się z informacjami o [ustawieniach limitu czasu bezczynności protokołu TCP modułu równoważenia obciążenia](load-balancer-tcp-idle-timeout.md). Pozwoli to zrozumieć zachowanie bezczynnego połączenia podczas używania usługi Azure Load Balancer.
