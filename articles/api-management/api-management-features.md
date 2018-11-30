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
ms.openlocfilehash: a00328608c582dcd28dbc78b5b56829f9d1ab500
ms.sourcegitcommit: 56d20d444e814800407a955d318a58917e87fe94
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/29/2018
ms.locfileid: "52585554"
---
# <a name="feature-based-comparison-of-the-azure-api-management-tiers"></a>Porównanie warstw usługi Azure API Management oparta na funkcjach

Każda usługa API Management [warstwy cenowej](https://aka.ms/apimpricing) oferuje różne zestaw funkcji oraz jednostkę [pojemności](api-management-capacity.md). W poniższej tabeli podsumowano najważniejsze funkcje dostępne w każdej z warstw. Niektóre funkcje działają inaczej, ale mają różne możliwości, w zależności od warstwy. W takich przypadkach różnice są wywoływane w artykułach dokumentacji opisujące te poszczególnych funkcji.

| Cecha                                                                                      | Użycie<sup>(wersja zapoznawcza)</sup> | Developer      | Podstawowa          | Standardowa (Standard)       | Premium        |
| -------------------------------------------------------------------------------------------- | ----------------------------- | -------------- | -------------- | -------------- | -------------- |
| Integracja z usługą Azure AD<sup>1</sup>                                                             | Nie                            | Yes            | Nie             | Yes            | Yes            |
| Obsługa usługi Virtual Network (VNet)                                                               | Nie                            | Yes            | Nie             | Nie             | Yes            |
| Wdrażanie w wielu regionach                                                                      | Nie                            | Nie             | Nie             | Nie             | Yes            |
| Wielu niestandardowych nazw domen                                                                 | Nie                            | Nie             | Nie             | Nie             | Yes            |
| Portal dla deweloperów<sup>2</sup>                                                                 | Nie                            | Yes            | Yes            | Yes            | Yes            |
| Wbudowaną pamięć podręczną                                                                               | Nie                            | Yes            | Yes            | Yes            | Yes            |
| Wbudowanym narzędziom analitycznym                                                                           | Nie                            | Yes            | Yes            | Yes            | Yes            |
| [Ustawienia protokołu SSL](api-management-howto-manage-protocols-ciphers.md)                             | Nie                            | Yes            | Yes            | Yes            | Yes            |
| [Zewnętrzna pamięć podręczna](https://aka.ms/apimbyoc)                                                    | Yes                           | Nie<sup>3</sup> | Nie<sup>3</sup> | Nie<sup>3</sup> | Nie<sup>3</sup> |
| [Uwierzytelnianie certyfikatu klienta](api-management-howto-mutual-certificates-for-clients.md) | Nie<sup>4</sup>                | Yes            | Yes            | Yes            | Yes            |
| [Tworzenie kopii zapasowej i przywracanie](api-management-howto-disaster-recovery-backup-restore.md)               | Nie                            | Yes            | Yes            | Yes            | Yes            |
| [Zarządzanie za pośrednictwem usługi Git](api-management-configuration-repository-git.md)                        | Nie                            | Yes            | Yes            | Yes            | Yes            |
| Bezpośredni interfejs API zarządzania                                                                        | Nie                            | Yes            | Yes            | Yes            | Yes            |
| Usługa Azure Monitor dzienniki i metryki                                                               | Nie<sup>5</sup>                | Yes            | Yes            | Yes            | Yes            |

<sup>1</sup> umożliwia korzystanie z usługi Azure AD (i usługi Azure AD B2C) jako dostawcy tożsamości dla logowanie użytkowników w portalu dla deweloperów.<br/>
<sup>2</sup> pokrewne funkcje w tym np. użytkownikom, grupom, problemy, szablony applicationsn i poczty e-mail i powiadomień.<br/>
<sup>3</sup> wkrótce zostanie udostępniona Obsługa zewnętrzna pamięć podręczna dla tej warstwy.<br/>
<sup>4</sup> uwierzytelnianie certyfikatu klienta, które zostaną dodane do warstwa zużycie przed jego ogólnie dostępne.<br/>
<sup>5</sup> pełne usługi Azure Monitor Obsługa zostanie dodana do warstwy zużycia.
