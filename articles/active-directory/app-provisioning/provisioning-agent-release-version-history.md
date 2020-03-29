---
title: 'Agent inicjowania obsługi administracyjnej usługi Azure AD Connect: historia wydania wersji | Dokumenty firmy Microsoft'
description: W tym artykule wymieniono wszystkie wersje agenta inicjowania obsługi administracyjnej usługi Azure AD Connect i opisano nowe funkcje i problemy z naprawą
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
ms.date: 02/26/2020
ms.subservice: app-provisioning
ms.author: chmutali
ms.collection: M365-identity-device-management
ms.openlocfilehash: 559bca4f5020cebe06be7f24f7af5ec2e94bec0e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78183248"
---
# <a name="azure-ad-connect-provisioning-agent-version-release-history"></a>Agent inicjowania obsługi administracyjnej usługi Azure AD Connect: historia wydania wersji wersji
W tym artykule wymieniono wersje i funkcje agenta inicjowania obsługi administracyjnej usługi Azure Active Directory Connect, które zostały wydane. Zespół usługi Azure AD regularnie aktualizuje agenta inicjowania obsługi administracyjnej o nowe funkcje i funkcje. Agent inicjowania obsługi administracyjnej jest aktualizowany automatycznie po wydaniu nowej wersji. 

Firma Microsoft zapewnia bezpośrednie wsparcie dla najnowszej wersji agenta i jednej wersji wcześniej.

## <a name="11960"></a>1.1.96.0

### <a name="release-status"></a>Stan wydania

4 grudnia 2019: Premiera do pobrania

### <a name="new-features-and-improvements"></a>Nowe funkcje i ulepszenia

* Obejmuje obsługę [obsługi administracyjnej chmury usługi Azure AD Connect](../cloud-provisioning/what-is-cloud-provisioning.md) w celu synchronizowania danych użytkowników, kontaktów i grup z lokalnej usługi Active Directory do usługi Azure AD


## <a name="11670"></a>1.1.67.0

### <a name="release-status"></a>Stan wydania

9 września 2019: Opublikowano do automatycznej aktualizacji

### <a name="new-features-and-improvements"></a>Nowe funkcje i ulepszenia

* Możliwość konfigurowania dodatkowych śledzenia i rejestrowania dla debugowania problemów agenta inicjowania obsługi administracyjnej
* Możliwość pobierania tylko tych atrybutów usługi Azure AD skonfigurowanych w mapowaniu w celu zwiększenia wydajności synchronizacji

### <a name="fixed-issues"></a>Rozwiązane problemy

* Naprawiono błąd polegający na tym, że agent przechodził w stan braku odpowiedzi, jeśli wystąpiły problemy z błędami połączenia usługi Azure AD
* Naprawiono błąd, który powodował problemy podczas odczytywania danych binarnych z usługi Azure Active Directory
* Naprawiono błąd polegający na tym, że agent nie może odnowić zaufania za pomocą usługi tożsamości hybrydowej w chmurze

## <a name="11300"></a>1.1.30.0

### <a name="release-status"></a>Stan wydania

Styczeń 23, 2019: Wydany do pobrania

### <a name="new-features-and-improvements"></a>Nowe funkcje i ulepszenia

* Przebudowano agenta aprowizacji i architekturę łączników, aby uzyskać lepszą wydajność, stabilność i niezawodność 
* Uprościć konfigurację agenta inicjowania obsługi administracyjnej za pomocą Kreatora instalacji opartego na interfejsie użytkownika 
* Dodano obsługę automatycznych aktualizacji agentów

