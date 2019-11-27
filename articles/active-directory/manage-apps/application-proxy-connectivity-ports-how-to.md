---
title: Jak otworzyć porty zapory wymagane dla aplikacji serwera proxy aplikacji
description: Dowiedz się, jakie porty otworzyć dla platformy Azure serwera Proxy aplikacji usługi AD do prawidłowego działania
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/21/2018
ms.author: mimart
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2b676508c73ff0233526f19b865bb9e4f7b80f12
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/21/2019
ms.locfileid: "74275550"
---
# <a name="how-to-open-the-firewall-ports-required-for-an-application-proxy-application"></a>Jak otworzyć porty zapory wymagane dla aplikacji serwera Proxy aplikacji

Aby zapoznać się z pełną listą wymaganych portów i funkcji poszczególnych portów, zobacz sekcję wymagania wstępne w [dokumentacji serwera proxy aplikacji](application-proxy-add-on-premises-application.md). należy pamiętać, że serwer Proxy aplikacji używa tylko portów wychodzących.

Możesz również sprawdzić, czy masz otwarte wszystkie wymagane porty, otwierając [Narzędzie Test Ports łącznika](https://aadap-portcheck.connectorporttest.msappproxy.net/) z sieci lokalnej. Więcej zielone znaczniki wyboru oznacza większą odporność. 

## <a name="app-proxy-regions"></a>Regiony serwera Proxy aplikacji

Pracujemy nad sposobem informacją o tym, które z tych regionów musi być zielona dla Ciebie. Teraz upewnij się, że są one wszystkie. Środkowe stany USA, również jest wymagany niezależnie od tego, w którym regionie znajdują się w.

Aby upewnić się, że narzędzie zapewnia odpowiednich wyników, należy koniecznie:

-   Otwórz narzędzie w przeglądarce z serwera, na którym zainstalowano łącznik.

-   Upewnij się, czy wszystkie serwery proxy lub zapory mające zastosowanie do łącznika są również stosowane do tej strony. Można to zrobić w programie Internet Explorer, przechodząc do **ustawień** -&gt; **opcje internetowe** -&gt; **połączenia** -&gt; **Ustawienia sieci LAN**. Na tej stronie zobaczysz pola "Użyj serwera Proxy serwera dla Twojej sieci LAN". Zaznacz to pole, a następnie umieścić adres serwera proxy w polu "Address".

## <a name="next-steps"></a>Następne kroki
[Omówienie łączników serwer proxy aplikacji usługi Azure AD platformy Azure](application-proxy-connectors.md)
