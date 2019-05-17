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
ms.openlocfilehash: d881c8de7ecc32be0ca0cc2c5a82e0d2d51a7054
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/16/2019
ms.locfileid: "65780318"
---
# <a name="feature-based-comparison-of-the-azure-api-management-tiers"></a>Porównanie warstw usługi Azure API Management oparta na funkcjach

Każda usługa API Management [warstwy cenowej](https://aka.ms/apimpricing) oferuje różne zestaw funkcji oraz jednostkę [pojemności](api-management-capacity.md). W poniższej tabeli podsumowano najważniejsze funkcje dostępne w każdej z warstw. Niektóre funkcje działają inaczej, ale mają różne możliwości, w zależności od warstwy. W takich przypadkach różnice są wywoływane w artykułach dokumentacji opisujące te poszczególnych funkcji.

| Cecha                                                                                      | Użycie<sup>(wersja zapoznawcza)</sup> | Deweloper      | Podstawowy          | Standardowa (Standard)       | Premium        |
| -------------------------------------------------------------------------------------------- | ----------------------------- | -------------- | -------------- | -------------- | -------------- |
| Integracja z usługą Azure AD<sup>1</sup>                                                             | Nie                            | Yes            | Nie             | Yes            | Tak            |
| Obsługa usługi Virtual Network (VNet)                                                               | Nie                            | Yes            | Nie             | Nie             | Tak            |
| Wdrażanie w wielu regionach                                                                      | Nie                            | Nie             | Nie             | Nie             | Tak            |
| Wiele niestandardowych nazw domen                                                                 | Nie                            | Nie             | Nie             | Nie             | Tak            |
| Portal dla deweloperów<sup>2</sup>                                                                 | Nie                            | Yes            | Yes            | Yes            | Tak            |
| Wbudowaną pamięć podręczną                                                                               | Nie                            | Yes            | Yes            | Yes            | Tak            |
| Wbudowane narzędzia do analityki                                                                           | Nie                            | Yes            | Yes            | Yes            | Tak            |
| [Ustawienia protokołu SSL](api-management-howto-manage-protocols-ciphers.md)                             | Nie                            | Yes            | Yes            | Yes            | Tak            |
| [Zewnętrzna pamięć podręczna](https://aka.ms/apimbyoc)                                                    | Tak                           | Yes            | Yes            | Yes            | Tak            |
| [Uwierzytelnianie certyfikatu klienta](api-management-howto-mutual-certificates-for-clients.md) | Nie<sup>3</sup>                | Tak            | Yes            | Yes            | Tak            |
| [Tworzenie kopii zapasowej i przywracanie](api-management-howto-disaster-recovery-backup-restore.md)               | Nie                            | Yes            | Yes            | Yes            | Tak            |
| [Zarządzanie za pośrednictwem usługi Git](api-management-configuration-repository-git.md)                        | Nie                            | Yes            | Yes            | Yes            | Tak            |
| Bezpośredni interfejs API zarządzania                                                                        | Nie                            | Yes            | Yes            | Yes            | Tak            |
| Usługa Azure Monitor dzienniki i metryki                                                               | Nie<sup>4</sup>                | Tak            | Yes            | Yes            | Tak            |

<sup>1</sup> umożliwia korzystanie z usługi Azure AD (i usługi Azure AD B2C) jako tożsamość dostawcy dla użytkownika logowania portalu dla deweloperów.<br/>
<sup>2</sup> pokrewne funkcje w tym np. użytkownikom, grupom, problemy, aplikacje i szablony wiadomości e-mail i powiadomień.<br/>
<sup>3</sup> uwierzytelnianie certyfikatu klienta, które zostaną dodane do warstwa zużycie przed jego ogólnie dostępne.<br/>
<sup>4</sup> pełne usługi Azure Monitor Obsługa zostanie dodana do warstwy zużycia.
