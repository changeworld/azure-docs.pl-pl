---
title: Plik dyrektywy include
description: dołączanie pliku z wymaganiami wstępnymi
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
ms.openlocfilehash: f7b1b294e9500ef9e0aadd24cfe3cd4e61fddda1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "68426931"
---
> [!IMPORTANT]
> **Przed wykonaniem zadań w tym artykule włącz synchronizację skrótów haseł z usługami domenowymi usługi Azure AD.**
>
> Postępuj zgodnie z poniższymi instrukcjami, w zależności od typu użytkowników w katalogu usługi Azure AD. Wykonaj oba zestawy instrukcji, jeśli masz kombinację tylko w chmurze i zsynchronizowane konta użytkowników w katalogu usługi Azure AD. Możesz nie być w stanie wykonać następujących operacji w przypadku, gdy próbujesz użyć konta gościa B2B (np. gmail lub MSA od innego dostawcy tożsamości, na co zezwalamy), ponieważ nie mamy hasła dla tych użytkowników zsynchronizowanych z domeną zarządzaną, ponieważ są to kont gości w katalogu. Pełne informacje o tych kontach, w tym ich hasła będą poza usługą Azure AD i ponieważ te informacje nie są w usłudze Azure AD, w związku z tym nawet nie są synchronizowane z domeną zarządzaną. 
> - [Instrukcje dotyczące kont użytkowników tylko w chmurze](../articles/active-directory-domain-services/active-directory-ds-getting-started-password-sync.md)
> - [Instrukcje dotyczące kont użytkowników zsynchronizowanych z katalogu lokalnego](../articles/active-directory-domain-services/active-directory-ds-getting-started-password-sync-synced-tenant.md)
