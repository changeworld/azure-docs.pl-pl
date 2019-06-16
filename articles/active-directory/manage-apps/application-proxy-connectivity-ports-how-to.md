---
title: Jak otworzyć porty zapory wymagane dla aplikacji serwera Proxy aplikacji | Dokumentacja firmy Microsoft
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
ms.openlocfilehash: 3e69f2e5049ca290a17c058c9d18dc7c6ec91f49
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "65783564"
---
# <a name="how-to-open-the-firewall-ports-required-for-an-application-proxy-application"></a>Jak otworzyć porty zapory wymagane dla aplikacji serwera Proxy aplikacji

Aby wyświetlić pełną listę wymagane porty i funkcję każdego portu, zobacz sekcję wymagania wstępne [dokumentacji serwera Proxy aplikacji](application-proxy-add-on-premises-application.md). należy pamiętać, że serwer Proxy aplikacji używa tylko portów wychodzących.

Możesz również sprawdzić, czy masz wszystkie wymagane porty otwarte, otwierając [narzędzie Test porty łącznika](https://aadap-portcheck.connectorporttest.msappproxy.net/) z siecią lokalną. Więcej zielone znaczniki wyboru oznacza większą odporność. 

## <a name="app-proxy-regions"></a>Regiony serwera Proxy aplikacji

Pracujemy nad sposobem informacją o tym, które z tych regionów musi być zielona dla Ciebie. Teraz upewnij się, że są one wszystkie. Środkowe stany USA, również jest wymagany niezależnie od tego, w którym regionie znajdują się w.

Aby upewnić się, że narzędzie zapewnia odpowiednich wyników, należy koniecznie:

-   Otwórz narzędzie w przeglądarce z serwera, na którym zainstalowano łącznik.

-   Upewnij się, czy wszystkie serwery proxy lub zapory mające zastosowanie do łącznika są również stosowane do tej strony. Można to zrobić w programie Internet Explorer, przechodząc do **ustawienia**  - &gt; **Opcje internetowe**  - &gt; **połączeń**  - &gt; **Ustawienia sieci LAN**. Na tej stronie zobaczysz pola "Użyj serwera Proxy serwera dla Twojej sieci LAN". Zaznacz to pole, a następnie umieścić adres serwera proxy w polu "Address".

## <a name="next-steps"></a>Kolejne kroki
[Omówienie łączników serwera Proxy aplikacji usługi Azure AD](application-proxy-connectors.md)
