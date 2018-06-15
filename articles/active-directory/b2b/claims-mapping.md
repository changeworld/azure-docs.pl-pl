---
title: Mapowanie w usłudze Azure Active Directory oświadczeń użytkowników współpracy B2B | Dokumentacja firmy Microsoft
description: Dostosowywanie oświadczeń użytkowników, które są wydawane w tokenie SAML dla użytkowników B2B usługi Azure Active Directory (Azure AD).
services: active-directory
ms.service: active-directory
ms.component: B2B
ms.topic: article
ms.date: 04/06/2018
ms.author: twooley
author: twooley
manager: mtillman
ms.reviewer: sasubram
ms.openlocfilehash: f2617fb79fc20fcb385c7e2fc3596245ab18caba
ms.sourcegitcommit: 96089449d17548263691d40e4f1e8f9557561197
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/17/2018
ms.locfileid: "34260295"
---
# <a name="b2b-collaboration-user-claims-mapping-in-azure-active-directory"></a>Mapowanie w usłudze Azure Active Directory oświadczeń użytkowników współpracy B2B

Dostosowywanie oświadczeń wydanych w tokenie SAML dla użytkowników współpracy B2B usługi Azure obsługuje usługi Active Directory (Azure AD). Podczas uwierzytelniania użytkownika do aplikacji, usługi Azure AD wystawia SAML token aplikacja, która zawiera informacje (lub oświadczenia) dotyczące użytkownika, który unikatowo identyfikuje je. Domyślnie w tym nazwę użytkownika, adres e-mail, imię i nazwisko użytkownika.

W [portalu Azure](https://portal.azure.com), można wyświetlić lub edytować oświadczenia, które są wysyłane w tokenie SAML do aplikacji. Aby uzyskać dostęp do ustawień, wybierz **usługi Azure Active Directory** > **aplikacje dla przedsiębiorstw** > aplikacji, która jest skonfigurowana dla logowania jednokrotnego > **logowanie jednokrotne** . Informacje o ustawieniach tokenu SAML w **atrybuty użytkownika** sekcji.

![Pokazuje atrybuty tokenu SAML w interfejsie użytkownika](media/claims-mapping/view-claims-in-saml-token.png)

Istnieją dwie możliwe przyczyny, dlaczego konieczne może być Edycja oświadczeń, które są wydawane w tokenie SAML:

1. Aplikacja wymaga innego zestawu identyfikatorów URI lub wartości oświadczeń.

2. Aplikacja wymaga oświadczeń NameIdentifier inny niż główna nazwa użytkownika (UPN) przechowywanych w usłudze Azure AD.

Aby uzyskać informacje o tym, jak dodawać i edytować oświadczeń, zobacz [Dostosowywanie oświadczeń wydanych w tokenie SAML dla aplikacji przedsiębiorstwa w usłudze Azure Active Directory](../develop/active-directory-saml-claims-customization.md).

Do współpracy B2B użytkowników mapowania NameID i UPN dzierżawy między będą mogły ze względów bezpieczeństwa.

## <a name="next-steps"></a>Kolejne kroki

- Informacje o właściwościach użytkownika współpracy B2B, zobacz [właściwości użytkownika współpracy usługi Azure Active Directory B2B](user-properties.md).
- Informacje użytkownika tokeny dla użytkowników współpracy B2B, zobacz [zrozumieć tokenów użytkowników we współpracy B2B usługi Azure AD](user-token.md).

