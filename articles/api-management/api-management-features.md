---
title: Porównanie warstw API Management platformy Azure w oparciu o funkcję | Microsoft Docs
description: W tym artykule porównano API Management warstw na podstawie oferowanych przez nie funkcji.
services: api-management
documentationcenter: ''
author: vladvino
manager: gwallace
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 11/01/2019
ms.author: apimpm
ms.openlocfilehash: 72e186bf44b8dffb99c345566b084ccd1c0d6013
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73472391"
---
# <a name="feature-based-comparison-of-the-azure-api-management-tiers"></a>Porównanie warstw API Management platformy Azure oparte na funkcjach

Każda [warstwa cenowa](https://aka.ms/apimpricing) API Management oferuje odrębny zestaw funkcji i poszczególnych [pojemności](api-management-capacity.md)jednostek. Poniższa tabela zawiera podsumowanie najważniejszych funkcji dostępnych w poszczególnych warstwach. Niektóre funkcje mogą współpracować inaczej lub mieć różne możliwości w zależności od warstwy. W takich przypadkach różnice są określane w artykułach dokumentacji opisującej te poszczególne funkcje.

> [!IMPORTANT]
> Zwróć uwagę na to, że warstwa dewelopera dotyczy nieprodukcyjnych przypadków użycia i ocen. Nie oferuje umowy SLA.

| Funkcja                                                                                      | Zużycie | Developer | Podstawowa | Standardowa | Premium |
| -------------------------------------------------------------------------------------------- | ----------- | --------- | ----- | -------- | ------- |
| Integracja z usługą Azure AD<sup>1</sup>                                                             | Nie          | Tak       | Nie    | Tak      | Tak     |
| Obsługa Virtual Network (VNet)                                                               | Nie          | Tak       | Nie    | Nie       | Tak     |
| Wdrażanie w wielu regionach                                                                      | Nie          | Nie        | Nie    | Nie       | Tak     |
| Wiele niestandardowych nazw domen                                                                 | Nie          | Nie        | Nie    | Nie       | Tak     |
| Portal dla deweloperów<sup>2</sup>                                                                 | Nie          | Tak       | Tak   | Tak      | Tak     |
| Wbudowana pamięć podręczna                                                                               | Nie          | Tak       | Tak   | Tak      | Tak     |
| Wbudowane narzędzia do analityki                                                                           | Nie          | Tak       | Tak   | Tak      | Tak     |
| [Samodzielna Brama](self-hosted-gateway-overview.md)<sup>3</sup>                           | Nie          | Tak       | Nie    | Nie       | Tak     |
| [Ustawienia protokołu SSL](api-management-howto-manage-protocols-ciphers.md)                             | Tak         | Tak       | Tak   | Tak      | Tak     |
| [Zewnętrzna pamięć podręczna](https://aka.ms/apimbyoc)                                                    | Tak         | Tak       | Tak   | Tak      | Tak     |
| [Uwierzytelnianie certyfikatu klienta](api-management-howto-mutual-certificates-for-clients.md) | Tak         | Tak       | Tak   | Tak      | Tak     |
| [Tworzenie kopii zapasowej i przywracanie](api-management-howto-disaster-recovery-backup-restore.md)               | Nie          | Tak       | Tak   | Tak      | Tak     |
| [Zarządzanie za pośrednictwem usługi git](api-management-configuration-repository-git.md)                        | Nie          | Tak       | Tak   | Tak      | Tak     |
| Interfejs API zarządzania bezpośredniego                                                                        | Nie          | Tak       | Tak   | Tak      | Tak     |
| Azure Monitor dzienników i metryk                                                               | Nie          | Tak       | Tak   | Tak      | Tak     |
| Statyczny adres IP                                                                                    | Nie          | Tak       | Tak   | Tak      | Tak     |

<sup>1</sup> umożliwia korzystanie z usługi Azure AD (i Azure AD B2C) jako dostawcy tożsamości na potrzeby logowania użytkownika w portalu dla deweloperów.<br/>
<sup>2</sup> w tym powiązane funkcje, takie jak użytkownicy, grupy, problemy, aplikacje i szablony wiadomości e-mail i powiadomienia.<br/>
<sup>3</sup> ograniczone do jednego wdrożenia bramy samoobsługowego z pojedynczym węzłem bramy.<br/>
