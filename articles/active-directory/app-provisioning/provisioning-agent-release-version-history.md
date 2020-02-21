---
title: 'Azure AD Connect agenta aprowizacji: historia wersji | Microsoft Docs'
description: W tym artykule wymieniono wszystkie wersje agenta aprowizacji Azure AD Connect i opisano nowe funkcje oraz naprawione problemy
services: active-directory
documentationcenter: ''
author: cmmdesai
manager: daveba
editor: ''
ms.assetid: ''
ms.service: active-directory
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/04/2020
ms.subservice: app-provisioning
ms.author: chmutali
ms.collection: M365-identity-device-management
ms.openlocfilehash: 164d156be030aa4a177ea77ed61782780338abb0
ms.sourcegitcommit: 3c8fbce6989174b6c3cdbb6fea38974b46197ebe
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/21/2020
ms.locfileid: "77522377"
---
# <a name="azure-ad-connect-provisioning-agent-version-release-history"></a>Azure AD Connect agenta aprowizacji: historia wersji
W tym artykule wymieniono wersje i funkcje agenta aprowizacji Azure Active Directory Connect, który został wystawiony. Zespół usługi Azure AD regularnie aktualizuje agenta aprowizacji za pomocą nowych funkcji i funkcjonalności. Agent aprowizacji jest automatycznie aktualizowany po wydaniu nowej wersji. 

Zalecamy włączenie autoaktualizacji dla agentów, aby upewnić się, że masz najnowsze funkcje i poprawki błędów. Firma Microsoft zapewnia bezpośrednią pomoc techniczną dotyczącą najnowszej wersji agenta i jednej wersji.

## <a name="11960"></a>1.1.96.0

### <a name="release-status"></a>Stan wydania

4 grudnia 2019: wydano do pobrania

### <a name="new-features-and-improvements"></a>Nowe funkcje i ulepszenia

* Obejmuje obsługę [Azure AD Connect aprowizacji w chmurze](../cloud-provisioning/what-is-cloud-provisioning.md) w celu synchronizowania danych użytkowników, kontaktów i grup z Active Directory lokalnych do usługi Azure AD


## <a name="11670"></a>1.1.67.0

### <a name="release-status"></a>Stan wydania

9 września 2019: wydano do aktualizacji AutoUpdate

### <a name="new-features-and-improvements"></a>Nowe funkcje i ulepszenia

* Możliwość skonfigurowania dodatkowego śledzenia i rejestrowania problemów z agentami aprowizacji
* Możliwość pobrania tylko tych atrybutów usługi Azure AD, które są skonfigurowane w mapowaniu, aby zwiększyć wydajność synchronizacji

### <a name="fixed-issues"></a>Rozwiązane problemy

* Rozwiązano problem polegający na tym, że agent przeszedł w stan nieodpowiadający, jeśli wystąpiły problemy z błędami połączenia z usługą Azure AD
* Rozwiązano błąd, który spowodował problemy, gdy odczytano dane binarne z Azure Active Directory
* Rozwiązano problem polegający na tym, że Agent nie mógł odnowić relacji zaufania z usługą tożsamości hybrydowej w chmurze

## <a name="11300"></a>1.1.30.0

### <a name="release-status"></a>Stan wydania

23 stycznia 2019: wydano do pobrania

### <a name="new-features-and-improvements"></a>Nowe funkcje i ulepszenia

* Odnowionych agenta aprowizacji i architektury łącznika w celu zapewnienia lepszej wydajności, stabilności i niezawodności 
* Uproszczona konfiguracja agenta aprowizacji przy użyciu Kreatora instalacji opartej na interfejsie użytkownika 
* Dodano obsługę automatycznych aktualizacji agenta

