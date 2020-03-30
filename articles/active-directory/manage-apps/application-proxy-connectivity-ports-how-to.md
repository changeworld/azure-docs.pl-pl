---
title: Jak otworzyć porty zapory wymagane dla aplikacji Proxy aplikacji
description: Dowiedz się, jakie porty mają być otwierane dla serwera proxy aplikacji usługi Azure AD, aby działały poprawnie
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74275550"
---
# <a name="how-to-open-the-firewall-ports-required-for-an-application-proxy-application"></a>Jak otworzyć porty zapory wymagane dla aplikacji Proxy aplikacji

Aby wyświetlić pełną listę wymaganych portów i funkcji każdego portu, zobacz sekcję wymagań wstępnych [dokumentacji serwera proxy aplikacji](application-proxy-add-on-premises-application.md). Należy pamiętać, że serwer proxy aplikacji używa tylko portów wychodzących.

Można również sprawdzić, czy wszystkie wymagane porty są otwarte, otwierając [narzędzie testowe portów łączników](https://aadap-portcheck.connectorporttest.msappproxy.net/) z sieci lokalnej. Więcej zielonych znaczników wyboru oznacza większą odporność. 

## <a name="app-proxy-regions"></a>Regiony proxy aplikacji

Pracujemy nad sposobem, aby poinformować, który z tych regionów musi być dla Ciebie zielony. Na razie upewnij się, że wszystkie są. Środkowe stany USA są również wymagane niezależnie od regionu, w którym się znajdujesz.

Aby upewnić się, że narzędzie daje właściwe wyniki, należy:

-   Otwórz narzędzie w przeglądarce z serwera, na którym zainstalowano łącznik.

-   Upewnij się, że wszystkie serwery proxy lub zapory mające zastosowanie do łącznika są również stosowane do tej strony. Można to zrobić w programie Internet Explorer, przechodząc do **ustawienia**  - &gt; **opcji**  - &gt; internetowych **Ustawienia**  - &gt; **sieci LAN .** Na tej stronie zobaczysz pole "Użyj serwera proxy dla swojej sieci LAN". Zaznacz to pole i umieść adres serwera proxy w polu "Adres".

## <a name="next-steps"></a>Następne kroki
[Opis łączników serwera proxy aplikacji usługi Azure AD](application-proxy-connectors.md)
