---
title: Jak otworzyć porty zapory wymagane dla aplikacji serwera Proxy aplikacji | Dokumentacja firmy Microsoft
description: Dowiedz się, jakie porty otworzyć dla platformy Azure serwera Proxy aplikacji usługi AD do prawidłowego działania
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
ms.topic: conceptual
ms.date: 05/21/2018
ms.author: barbkess
ms.reviewer: asteen
ms.openlocfilehash: 6c26fb7cf88a44e7392d6be934d9dfae43b31c08
ms.sourcegitcommit: f86e5d5b6cb5157f7bde6f4308a332bfff73ca0f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/31/2018
ms.locfileid: "39362787"
---
# <a name="how-to-open-the-firewall-ports-required-for-an-application-proxy-application"></a>Jak otworzyć porty zapory wymagane dla aplikacji serwera Proxy aplikacji

Aby wyświetlić pełną listę wymagane porty i funkcję każdego portu, zobacz sekcję wymagania wstępne [dokumentacji serwera Proxy aplikacji](manage-apps/application-proxy-enable.md). należy pamiętać, że serwer Proxy aplikacji używa tylko portów wychodzących.

Możesz również sprawdzić, czy masz wszystkie wymagane porty otwarte, otwierając [narzędzie Test porty łącznika](https://aadap-portcheck.connectorporttest.msappproxy.net/) z sieci lokalnej. Więcej zielone znaczniki wyboru oznacza większą odporność. 

## <a name="app-proxy-regions"></a>Regiony serwera Proxy aplikacji

Pracujemy nad sposobem informacją o tym, które z tych regionów musi być zielona dla Ciebie. Teraz upewnij się, że są one wszystkie. Środkowe stany USA, również jest wymagany niezależnie od tego, w którym regionie znajdują się w.

Aby upewnić się, że narzędzie zapewnia odpowiednich wyników, należy koniecznie:

-   Otwórz narzędzie w przeglądarce z serwera, na którym zainstalowano łącznik.

-   Upewnij się, czy wszystkie serwery proxy lub zapory mające zastosowanie do łącznika są również stosowane do tej strony. Można to zrobić w programie Internet Explorer, przechodząc do **ustawienia**  - &gt; **Opcje internetowe**  - &gt; **połączeń**  - &gt; **Ustawienia sieci Lan**. Na tej stronie zobaczysz pola "Użyj serwera Proxy serwera dla Twojej sieci LAN". Zaznacz to pole, a następnie umieścić adres serwera proxy w polu "Address".

## <a name="next-steps"></a>Kolejne kroki
[Omówienie łączników serwera Proxy aplikacji usługi Azure AD](manage-apps/application-proxy-connectors.md)
