---
title: Porównanie warstw usługi Azure API Management oparta na funkcjach | Dokumentacja firmy Microsoft
description: W tym artykule porównano warstw usługi API Management, w oparciu o funkcje, które oferują one.
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/26/2018
ms.author: apimpm
ms.openlocfilehash: 34c4ef2885a82b6c392b814eeb624e616e341d48
ms.sourcegitcommit: 009334a842d08b1c83ee183b5830092e067f4374
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/29/2019
ms.locfileid: "66304350"
---
# <a name="feature-based-comparison-of-the-azure-api-management-tiers"></a>Porównanie warstw usługi Azure API Management oparta na funkcjach

Każda usługa API Management [warstwy cenowej](https://aka.ms/apimpricing) oferuje różne zestaw funkcji oraz jednostkę [pojemności](api-management-capacity.md). W poniższej tabeli podsumowano najważniejsze funkcje dostępne w każdej z warstw. Niektóre funkcje działają inaczej, ale mają różne możliwości, w zależności od warstwy. W takich przypadkach różnice są wywoływane w artykułach dokumentacji opisujące te poszczególnych funkcji.

| Cecha                                                                                      | Zużycie | Developer      | Podstawowa          | Standardowa (Standard)       | Premium        |
| -------------------------------------------------------------------------------------------- | ----------------------------- | -------------- | -------------- | -------------- | -------------- |
| Integracja z usługą Azure AD<sup>1</sup>                                                             | Nie                            | Yes            | Nie             | Yes            | Yes            |
| Obsługa usługi Virtual Network (VNet)                                                               | Nie                            | Yes            | Nie             | Nie             | Tak            |
| Wdrażanie w wielu regionach                                                                      | Nie                            | Nie             | Nie             | Nie             | Tak            |
| Wiele niestandardowych nazw domen                                                                 | Nie                            | Nie             | Nie             | Nie             | Tak            |
| Portal dla deweloperów<sup>2</sup>                                                                 | Nie                            | Yes            | Yes            | Yes            | Tak            |
| Wbudowaną pamięć podręczną                                                                               | Nie                            | Yes            | Yes            | Yes            | Yes            |
| Wbudowane narzędzia do analityki                                                                           | Nie                            | Yes            | Yes            | Yes            | Yes            |
| [Ustawienia protokołu SSL](api-management-howto-manage-protocols-ciphers.md)                             | Tak                            | Yes            | Yes            | Yes            | Yes            |
| [Zewnętrzna pamięć podręczna](https://aka.ms/apimbyoc)                                                    | Tak                           | Yes            | Yes            | Yes            | Yes            |
| [Uwierzytelnianie certyfikatu klienta](api-management-howto-mutual-certificates-for-clients.md) | Yes                | Yes            | Yes            | Yes            | Yes            |
| [Tworzenie kopii zapasowej i przywracanie](api-management-howto-disaster-recovery-backup-restore.md)               | Nie                            | Yes            | Yes            | Yes            | Tak            |
| [Zarządzanie za pośrednictwem usługi Git](api-management-configuration-repository-git.md)                        | Nie                            | Yes            | Yes            | Yes            | Yes            |
| Bezpośredni interfejs API zarządzania                                                                        | Nie                            | Yes            | Yes            | Yes            | Yes            |
| Usługa Azure Monitor dzienniki i metryki                                                               | Nie                | Yes            | Yes            | Yes            | Yes            |

<sup>1</sup> umożliwia korzystanie z usługi Azure AD (i usługi Azure AD B2C) jako tożsamość dostawcy dla użytkownika logowania portalu dla deweloperów.<br/>
<sup>2</sup> pokrewne funkcje w tym np. użytkownikom, grupom, problemy, aplikacje i szablony wiadomości e-mail i powiadomień.<br/>
