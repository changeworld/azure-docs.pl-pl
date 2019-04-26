---
title: Rozwiązywanie problemów z typowych błędów uwierzytelniania | Dokumentacja firmy Microsoft
description: Zapewnia pomoc dotyczącą typowych błędów uwierzytelniania, gdy za pomocą interfejsów API z portalu Cloud Partner.
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: v-miclar
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: pbutlerm
ms.openlocfilehash: 12ed395855b9d870f8f6e3564dc5b3b899b9c6c1
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60326482"
---
# <a name="troubleshooting-common-authentication-errors"></a>Rozwiązywanie problemów z typowych błędów uwierzytelniania

Ten artykuł zawiera pomoc dotyczącą typowych błędów uwierzytelniania, korzystając z interfejsów API portalu dla partnerów chmury.

## <a name="unauthorized-error"></a>Błąd dotyczący nieautoryzowanego dostępu

Jeśli stale `401 unauthorized` błędy, sprawdź, czy masz token dostępu nie jest ważna.  Jeśli jeszcze tego nie zrobiono, tworzenie podstawowej aplikacji usługi Azure Active Directory (Azure AD) i jednostki usługi zgodnie z opisem w [w obsłudze portalu do utworzenia usługi Azure Active Directory aplikacji i usługi jednostki, które mogą uzyskiwać dostęp do zasobów](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal). Następnie użyj prostego żądania POST protokołu HTTP lub aplikacji, aby zweryfikować dostęp do usługi.  Będzie zawierać identyfikator dzierżawy, identyfikator aplikacji, identyfikator obiektu i klucz tajny, można uzyskać tokenu dostępu, jak pokazano na poniższej ilustracji:

![Rozwiązywanie problemów z błąd 401 — dostęp](./media/cloud-partner-portal-api-troubleshooting-authentication-errors/troubleshooting-401-error.jpg)


## <a name="forbidden-error"></a>Błąd "niedozwolone"

Jeśli otrzymasz `403 forbidden` błąd, upewnij się, że do swojego konta wydawcy w portalu Cloud Partner dodano poprawną jednostką usługi.
Postępuj zgodnie z instrukcjami w [wymagania wstępne](./cloud-partner-portal-api-prerequisites.md) stronę, aby dodać jednostki usługi do portalu.

Jeśli została dodana poprawną jednostką usługi, sprawdź wszystkie pozostałe informacje. Zamknij Zwróć uwagę na to identyfikator obiektu wprowadzony w portalu. Istnieją dwa identyfikatory obiektów na stronie rejestracji aplikacji w usłudze Azure Active Directory i trzeba użyć lokalnego identyfikatora obiektu. Możesz znaleźć poprawnej wartości, przechodząc do **rejestracje aplikacji** strony dla aplikacji i klikając nazwę aplikacji, w obszarze **aplikacja zarządzana w katalogu lokalnym**. Spowoduje to przejście do lokalnych właściwości dla aplikacji, w którym można znaleźć poprawny identyfikator obiektu w **właściwości** strony, jak pokazano na poniższej ilustracji. Ponadto upewnij się, użyj Identyfikatora wydawcy poprawne, po dodaniu nazwy głównej usługi oraz wykonywanie wywołania interfejsu API.

![Rozwiązywanie problemów z błąd 403](./media/cloud-partner-portal-api-troubleshooting-authentication-errors/troubleshooting-403-error.jpg)
