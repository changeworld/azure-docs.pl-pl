---
title: Plik dyrektywy include
description: Dołącz plik z warunków wstępnych
services: active-directory-ds
documentationcenter: ''
author: mahesh-unnikrishnan
manager: mtillman
editor: curtand
ms.assetid: 66b5f8e2-e08d-43c8-b460-6204aecda8f7
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.custom: include file
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/22/2018
ms.author: maheshu
ms.openlocfilehash: 1fba8cc9ae40cf5539016bbd73de65f557a64136
ms.sourcegitcommit: 031e4165a1767c00bb5365ce9b2a189c8b69d4c0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/13/2019
ms.locfileid: "59551620"
---
> [!IMPORTANT]
> **Włączanie synchronizacji skrótów haseł w usługach domenowych Azure AD, przed wykonaniem zadania w tym artykule.**
>
> Postępuj zgodnie z instrukcjami poniżej, w zależności od typu użytkowników w katalogu usługi Azure AD. Jeśli masz kombinację kont użytkowników tylko w chmurze i synchronizowane w katalogu usługi Azure AD, należy ukończyć oba zestawy instrukcji. Nie można przeprowadzić następujące operacje, w przypadku, gdy próbujesz użyć konta gościa B2B (przykład usługi gmail lub MSA od innego dostawcy tożsamości, które są dozwolone), ponieważ nie ma hasła dla tych użytkowników zsynchronizowanych do domeny zarządzanej, jak te są to konta gościa w katalogu. Pełne informacje na temat tych kont, włącznie z haseł będzie znajdować się poza usługi Azure AD i jak te informacje nie znajduje się w usłudze Azure AD więc go nie nawet uzyskać synchronizowane do domeny zarządzanej. 
> - [Instrukcje dotyczące kont użytkowników tylko w chmurze](../articles/active-directory-domain-services/active-directory-ds-getting-started-password-sync.md)
> - [Instrukcje dotyczące kont użytkowników synchronizowanych z katalogiem lokalnym](../articles/active-directory-domain-services/active-directory-ds-getting-started-password-sync-synced-tenant.md)
