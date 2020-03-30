---
title: Oparte na funkcjach porównanie warstw usługi Azure API Management | Dokumenty firmy Microsoft
description: W tym artykule porównano warstwy zarządzania interfejsami API na podstawie oferowanych przez nie funkcji.
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
ms.openlocfilehash: 2e84138419986ef1033ab076b3745187812e91b7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80335884"
---
# <a name="feature-based-comparison-of-the-azure-api-management-tiers"></a>Oparte na funkcjach porównanie warstw usługi Azure API Management

Każda [warstwa cenowa](https://aka.ms/apimpricing) Zarządzania interfejsami API oferuje odrębny zestaw funkcji i [pojemność jednostkową.](api-management-capacity.md) W poniższej tabeli podsumowano kluczowe funkcje dostępne w każdej z warstw. Niektóre funkcje mogą działać inaczej lub mają różne możliwości w zależności od warstwy. W takich przypadkach różnice są wywoływane w artykułach dokumentacji opisujących te poszczególne funkcje.

> [!IMPORTANT]
> Należy pamiętać, że warstwa Deweloper jest dla przypadków użycia nieprodukcyjnego i ocen. Nie oferuje umowy SLA.

| Funkcja                                                                                      | Zużycie | Developer | Podstawowa (Basic) | Standardowa | Premium |
| -------------------------------------------------------------------------------------------- | ----------- | --------- | ----- | -------- | ------- |
| Integracja z usługą Azure AD<sup>1</sup>                                                             | Nie          | Tak       | Nie    | Tak      | Tak     |
| Obsługa sieci wirtualnej (VNet)                                                               | Nie          | Tak       | Nie    | Nie       | Tak     |
| Wdrażanie w wielu regionach                                                                      | Nie          | Nie        | Nie    | Nie       | Tak     |
| Wiele niestandardowych nazw domen                                                                 | Nie          | Nie        | Nie    | Nie       | Tak     |
| Portal dla deweloperów<sup>2</sup>                                                                 | Nie          | Tak       | Tak   | Tak      | Tak     |
| Wbudowana pamięć podręczna                                                                               | Nie          | Tak       | Tak   | Tak      | Tak     |
| Wbudowana analityka                                                                           | Nie          | Tak       | Tak   | Tak      | Tak     |
| [Brama hostowana samodzielnie](self-hosted-gateway-overview.md)<sup>3</sup>                           | Nie          | Tak       | Nie    | Nie       | Tak     |
| [Ustawienia protokołu TLS](api-management-howto-manage-protocols-ciphers.md)                             | Tak         | Tak       | Tak   | Tak      | Tak     |
| [Zewnętrzna pamięć podręczna](https://aka.ms/apimbyoc)                                                    | Tak         | Tak       | Tak   | Tak      | Tak     |
| [Uwierzytelnianie certyfikatu klienta](api-management-howto-mutual-certificates-for-clients.md) | Tak         | Tak       | Tak   | Tak      | Tak     |
| [Tworzenie kopii zapasowych i przywracanie](api-management-howto-disaster-recovery-backup-restore.md)               | Nie          | Tak       | Tak   | Tak      | Tak     |
| [Zarządzanie nad Git](api-management-configuration-repository-git.md)                        | Nie          | Tak       | Tak   | Tak      | Tak     |
| Interfejs API zarządzania bezpośredniego                                                                        | Nie          | Tak       | Tak   | Tak      | Tak     |
| Dzienniki i metryki usługi Azure Monitor                                                               | Nie          | Tak       | Tak   | Tak      | Tak     |
| Statyczny adres IP                                                                                    | Nie          | Tak       | Tak   | Tak      | Tak     |

<sup>1</sup> Umożliwia korzystanie z usługi Azure AD (i usługi Azure AD B2C) jako dostawcy tożsamości dla logowania użytkowników w portalu dewelopera.<br/>
<sup>2</sup> W tym powiązane funkcje, takie jak użytkownicy, grupy, problemy, aplikacje i szablony wiadomości e-mail oraz powiadomienia.<br/>
<sup>3</sup> Ograniczone do pojedynczego wdrożenia bramy hostowanej samodzielnie z jednym węzłem bramy.<br/>
