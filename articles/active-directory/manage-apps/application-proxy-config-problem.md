---
title: Problem z utworzeniem aplikacji proxy aplikacji | Dokumenty firmy Microsoft
description: Jak rozwiązywać problemy z tworzeniem aplikacji serwera proxy aplikacji w portalu administratora usługi Azure AD
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/21/2018
ms.author: mimart
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: 104b98cba0948ec5d0896877e54eab1e7cd4049f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "65825817"
---
# <a name="problem-creating-an-application-proxy-application"></a>Problem z utworzeniem aplikacji proxy aplikacji 

Poniżej znajdują się niektóre z typowych problemów, z którymi ludzie napotykają podczas tworzenia nowej aplikacji proxy aplikacji.

## <a name="recommended-documents"></a>Zalecane dokumenty 

Aby dowiedzieć się więcej na temat tworzenia aplikacji serwera proxy aplikacji za pośrednictwem portalu administracyjnego, zobacz [Publikowanie aplikacji przy użyciu serwera proxy aplikacji usługi Azure AD](application-proxy-add-on-premises-application.md).

Jeśli postępujesz zgodnie z instrukcjami w tym dokumencie i otrzymujesz błąd podczas tworzenia aplikacji, zobacz szczegóły błędu, aby uzyskać informacje i sugestie dotyczące sposobu naprawy aplikacji. Większość komunikatów o błędach zawiera sugerowaną poprawkę. 

## <a name="specific-things-to-check"></a>Konkretne rzeczy do sprawdzenia

Aby uniknąć typowych błędów, sprawdź:

-   Jesteś administratorem z uprawnieniami do tworzenia aplikacji proxy aplikacji

-   Wewnętrzny adres URL jest unikatowy

-   Zewnętrzny adres URL jest unikatowy

-   Adresy URL zaczynają się od http lub https, a kończą na "/"

-   Adres URL powinien być nazwą domeny, a nie adresem IP

Komunikat o błędzie powinien być wyświetlany w prawym górnym rogu podczas tworzenia aplikacji. Można również wybrać ikonę powiadomień, aby wyświetlić komunikaty o błędach.

   ![Monit o powiadomienie](./media/application-proxy-config-problem/error-message.png)

## <a name="next-steps"></a>Następne kroki
[Włączanie serwera proxy aplikacji w witrynie Azure Portal](application-proxy-add-on-premises-application.md)
