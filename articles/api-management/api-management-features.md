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
ms.openlocfilehash: a57f8e44d19432f82abe4fa5e7bafce900db3394
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/28/2019
ms.locfileid: "67448008"
---
# <a name="feature-based-comparison-of-the-azure-api-management-tiers"></a>Porównanie warstw usługi Azure API Management oparta na funkcjach

Każda usługa API Management [warstwy cenowej](https://aka.ms/apimpricing) oferuje różne zestaw funkcji oraz jednostkę [pojemności](api-management-capacity.md). W poniższej tabeli podsumowano najważniejsze funkcje dostępne w każdej z warstw. Niektóre funkcje działają inaczej, ale mają różne możliwości, w zależności od warstwy. W takich przypadkach różnice są wywoływane w artykułach dokumentacji opisujące te poszczególnych funkcji.

| Cecha                                                                                      | Zużycie | Developer      | Podstawowa          | Standardowa (Standard)       | Premium        |
| -------------------------------------------------------------------------------------------- | ----------------------------- | -------------- | -------------- | -------------- | -------------- |
| Integracja z usługą Azure AD<sup>1</sup>                                                             | Nie                            | Yes            | Nie             | Yes            | Tak            |
| Obsługa usługi Virtual Network (VNet)                                                               | Nie                            | Yes            | Nie             | Nie             | Yes            |
| Wdrażanie w wielu regionach                                                                      | Nie                            | Nie             | Nie             | Nie             | Tak            |
| Wiele niestandardowych nazw domen                                                                 | Nie                            | Nie             | Nie             | Nie             | Yes            |
| Portal dla deweloperów<sup>2</sup>                                                                 | Nie                            | Yes            | Yes            | Yes            | Yes            |
| Wbudowaną pamięć podręczną                                                                               | Nie                            | Yes            | Yes            | Yes            | Tak            |
| Wbudowane narzędzia do analityki                                                                           | Nie                            | Yes            | Yes            | Yes            | Tak            |
| [Ustawienia protokołu SSL](api-management-howto-manage-protocols-ciphers.md)                             | Yes                            | Yes            | Yes            | Yes            | Yes            |
| [Zewnętrzna pamięć podręczna](https://aka.ms/apimbyoc)                                                    | Yes                           | Yes            | Yes            | Yes            | Tak            |
| [Uwierzytelnianie certyfikatu klienta](api-management-howto-mutual-certificates-for-clients.md) | Tak                | Yes            | Yes            | Yes            | Tak            |
| [Tworzenie kopii zapasowej i przywracanie](api-management-howto-disaster-recovery-backup-restore.md)               | Nie                            | Yes            | Yes            | Yes            | Yes            |
| [Zarządzanie za pośrednictwem usługi Git](api-management-configuration-repository-git.md)                        | Nie                            | Yes            | Yes            | Yes            | Tak            |
| Bezpośredni interfejs API zarządzania                                                                        | Nie                            | Yes            | Yes            | Yes            | Yes            |
| Usługa Azure Monitor dzienniki i metryki                                                               | Nie                | Yes            | Yes            | Yes            | Tak            |
| Statyczny adres IP                                                               | Nie                | Yes            | Yes            | Yes            | Tak            |

<sup>1</sup> umożliwia korzystanie z usługi Azure AD (i usługi Azure AD B2C) jako tożsamość dostawcy dla użytkownika logowania portalu dla deweloperów.<br/>
<sup>2</sup> pokrewne funkcje w tym np. użytkownikom, grupom, problemy, aplikacje i szablony wiadomości e-mail i powiadomień.<br/>
