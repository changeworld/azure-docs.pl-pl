---
title: Porównanie warstw API Management platformy Azure w oparciu o funkcję | Microsoft Docs
description: W tym artykule porównano API Management warstw na podstawie oferowanych przez nie funkcji.
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
ms.openlocfilehash: c06e297d3d81623b7224082cb66f8faa6879205d
ms.sourcegitcommit: 4b5dcdcd80860764e291f18de081a41753946ec9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/03/2019
ms.locfileid: "68774949"
---
# <a name="feature-based-comparison-of-the-azure-api-management-tiers"></a>Porównanie warstw API Management platformy Azure oparte na funkcjach

>[!IMPORTANT]
> Zwróć uwagę na to, że warstwa dewelopera dotyczy nieprodukcyjnych przypadków użycia i ocen. Nie oferuje umowy SLA. 

Każda [warstwa cenowa](https://aka.ms/apimpricing) API Management oferuje odrębny zestaw funkcji i poszczególnych [pojemności](api-management-capacity.md)jednostek. Poniższa tabela zawiera podsumowanie najważniejszych funkcji dostępnych w poszczególnych warstwach. Niektóre funkcje mogą współpracować inaczej lub mieć różne możliwości w zależności od warstwy. W takich przypadkach różnice są określane w artykułach dokumentacji opisującej te poszczególne funkcje.

| Cecha                                                                                      | Zużycie | Deweloper      | Podstawowa          | Standardowa (Standard)       | Premium        |
| -------------------------------------------------------------------------------------------- | ----------------------------- | -------------- | -------------- | -------------- | -------------- |
| Integracja z usługą Azure AD<sup>1</sup>                                                             | Nie                            | Yes            | Nie             | Yes            | Yes            |
| Obsługa Virtual Network (VNet)                                                               | Nie                            | Yes            | Nie             | Nie             | Tak            |
| Wdrażanie w wielu regionach                                                                      | Nie                            | Nie             | Nie             | Nie             | Tak            |
| Wiele niestandardowych nazw domen                                                                 | Nie                            | Nie             | Nie             | Nie             | Tak            |
| Portal dla deweloperów<sup>2</sup>                                                                 | Nie                            | Yes            | Yes            | Yes            | Tak            |
| Wbudowana pamięć podręczna                                                                               | Nie                            | Yes            | Yes            | Yes            | Tak            |
| Wbudowane narzędzia do analityki                                                                           | Nie                            | Yes            | Yes            | Yes            | Tak            |
| [Ustawienia protokołu SSL](api-management-howto-manage-protocols-ciphers.md)                             | Tak                            | Yes            | Yes            | Yes            | Tak            |
| [Zewnętrzna pamięć podręczna](https://aka.ms/apimbyoc)                                                    | Tak                           | Yes            | Yes            | Yes            | Tak            |
| [Uwierzytelnianie certyfikatu klienta](api-management-howto-mutual-certificates-for-clients.md) | Yes                | Yes            | Yes            | Yes            | Yes            |
| [Tworzenie kopii zapasowej i przywracanie](api-management-howto-disaster-recovery-backup-restore.md)               | Nie                            | Yes            | Yes            | Yes            | Tak            |
| [Zarządzanie za pośrednictwem usługi git](api-management-configuration-repository-git.md)                        | Nie                            | Yes            | Yes            | Yes            | Yes            |
| Interfejs API zarządzania bezpośredniego                                                                        | Nie                            | Yes            | Yes            | Yes            | Tak            |
| Azure Monitor dzienników i metryk                                                               | Nie                | Yes            | Yes            | Yes            | Yes            |
| Statyczny adres IP                                                               | Nie                | Yes            | Yes            | Yes            | Tak            |

<sup>1</sup> umożliwia korzystanie z usługi Azure AD (i Azure AD B2C) jako dostawcy tożsamości na potrzeby logowania użytkownika w portalu dla deweloperów.<br/>
<sup>2</sup> w tym powiązane funkcje, takie jak użytkownicy, grupy, problemy, aplikacje i szablony wiadomości e-mail i powiadomienia.<br/>
