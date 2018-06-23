---
title: Jak otworzyć porty zapory wymagane dla aplikacji serwera Proxy aplikacji | Dokumentacja firmy Microsoft
description: Dowiedzieć się, jakie porty do otwarcia dla aplikacji serwera Proxy Azure AD działała prawidłowo
services: active-directory
documentationcenter: ''
author: barbkess
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/21/2018
ms.author: barbkess
ms.reviewer: asteen
ms.openlocfilehash: bc1ed4a0b49588bd9828bd64508caa50e92c304a
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/23/2018
ms.locfileid: "36335676"
---
# <a name="how-to-open-the-firewall-ports-required-for-an-application-proxy-application"></a>Jak otworzyć porty zapory wymagane dla aplikacji serwera Proxy aplikacji

Aby wyświetlić pełną listę wymagane porty i funkcji każdy port, zobacz sekcję wymagania wstępne [dokumentacji serwera Proxy aplikacji](manage-apps/application-proxy-enable.md). należy pamiętać, że serwer Proxy aplikacji tylko używa portów wychodzących.

Możesz również sprawdzić, czy znajdują się wszystkie wymagane porty Otwórz otwierając [narzędzia Test porty łącznik](https://aadap-portcheck.connectorporttest.msappproxy.net/) z sieci lokalnej. Więcej zaznaczeń zielony oznacza większą elastyczność. 

## <a name="app-proxy-regions"></a>Regiony serwera Proxy aplikacji

Pracujemy nad sposobem informacją o tym, które z tych regionów, musi być zielona dla Ciebie. Teraz upewnij się, że są one wszystkich. Środkowe stany USA jest również wymagany niezależnie od tego, w którym znajdują się w regionie.

Aby upewnij się, że narzędzie daje prawo wyników, należy koniecznie:

-   Otwórz narzędzie w przeglądarce z serwera, na którym zainstalowano łącznik.

-   Upewnij się, że wszystkie serwery proxy lub zapory mające zastosowanie do Twojego łącznika również są stosowane do tej strony. Można to zrobić w programie Internet Explorer, przechodząc do **ustawienia**  - &gt; **Opcje internetowe**  - &gt; **połączeń**  - &gt; **Ustawienia sieci Lan**. Na tej stronie zobacz temat pola "Użyj serwera Proxy serwera dla your LAN". Zaznacz to pole i umieścić adres serwera proxy w polu "Address".

## <a name="next-steps"></a>Kolejne kroki
[Zrozumienie łączniki serwera Proxy aplikacji usługi Azure AD](manage-apps/application-proxy-connectors.md)
