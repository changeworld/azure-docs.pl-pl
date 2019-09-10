---
title: 'Azure AD Connect agenta aprowizacji: Historia wersji systemu | Microsoft Docs'
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
ms.date: 04/05/2019
ms.subservice: app-mgmt
ms.author: chmutali
ms.collection: M365-identity-device-management
ms.openlocfilehash: de6ef5dedb0779536cfbc1e6a3440e748ff78524
ms.sourcegitcommit: 65131f6188a02efe1704d92f0fd473b21c760d08
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/10/2019
ms.locfileid: "70862111"
---
# <a name="azure-ad-connect-provisioning-agent-version-release-history"></a>Azure AD Connect agenta aprowizacji: Historia wersji
W tym artykule wymieniono wersje i funkcje agenta aprowizacji Azure AD Connect, który został wystawiony. Zespół usługi Azure AD regularnie aktualizuje agenta aprowizacji za pomocą nowych funkcji i funkcjonalności. Agenci aprowizacji są automatycznie aktualizowani po wydaniu nowej wersji. 

Zalecamy włączenie autoaktualizacji dla agentów, aby upewnić się, że masz najnowsze funkcje i poprawki błędów. Firma Microsoft zapewnia bezpośrednią pomoc techniczną dotyczącą najnowszej wersji agenta i jednej wersji.

## <a name="11670"></a>1.1.67.0

### <a name="release-status"></a>Stan wydania

9 września 2019: Wydane na potrzeby aktualizacji AutoUpdate

### <a name="new-features-and-improvements"></a>Nowe funkcje i ulepszenia

* Możliwość skonfigurowania dodatkowego śledzenia i rejestrowania problemów z agentami aprowizacji
* Możliwość pobrania tylko tych atrybutów usługi AD, które są skonfigurowane w mapowaniu, aby zwiększyć wydajność synchronizacji

### <a name="fixed-issues"></a>Rozwiązane problemy

* Rozwiązano problem polegający na tym, że agent przeszedł w stan nieodpowiadający, jeśli wystąpiły problemy z błędami połączenia z usługą AD
* Rozwiązano błąd, który spowodował problemy, gdy odczytano dane binarne z Active Directory
* Rozwiązano problem polegający na tym, że Agent nie mógł odnowić relacji zaufania z usługą tożsamości hybrydowej w chmurze

## <a name="11300"></a>1.1.30.0

### <a name="release-status"></a>Stan wydania

23 stycznia 2019: Wydane do pobrania

### <a name="new-features-and-improvements"></a>Nowe funkcje i ulepszenia

* Architektura łącznika & aprowizacji agentów w celu zapewnienia lepszej wydajności, stabilności i niezawodności 
* Uproszczona konfiguracja agenta aprowizacji przy użyciu Kreatora instalacji opartej na interfejsie użytkownika 
* Dodano obsługę automatycznych aktualizacji agenta

