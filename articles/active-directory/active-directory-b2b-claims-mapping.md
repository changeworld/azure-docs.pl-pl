---
title: Mapowanie w usłudze Azure Active Directory oświadczeń użytkowników współpracy B2B | Dokumentacja firmy Microsoft
description: Dostosowywanie oświadczeń użytkowników, które są wydawane w tokenie SAML dla użytkowników B2B usługi Azure Active Directory (Azure AD).
services: active-directory
documentationcenter: ''
author: twooley
manager: mtillman
editor: ''
tags: ''
ms.assetid: ''
ms.service: active-directory
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: identity
ms.date: 04/06/2018
ms.author: twooley
ms.reviewer: sasubram
ms.openlocfilehash: 8f5e471d4e7102300cd5581976b45c9fa8cc57bc
ms.sourcegitcommit: 3a4ebcb58192f5bf7969482393090cb356294399
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2018
---
# <a name="b2b-collaboration-user-claims-mapping-in-azure-active-directory"></a>Mapowanie w usłudze Azure Active Directory oświadczeń użytkowników współpracy B2B

Dostosowywanie oświadczeń wydanych w tokenie SAML dla użytkowników współpracy B2B usługi Azure obsługuje usługi Active Directory (Azure AD). Podczas uwierzytelniania użytkownika do aplikacji, usługi Azure AD wystawia SAML token aplikacja, która zawiera informacje (lub oświadczenia) dotyczące użytkownika, który unikatowo identyfikuje je. Domyślnie w tym nazwę użytkownika, adres e-mail, imię i nazwisko użytkownika.

W [portalu Azure](https://portal.azure.com), można wyświetlić lub edytować oświadczenia, które są wysyłane w tokenie SAML do aplikacji. Aby uzyskać dostęp do ustawień, wybierz **usługi Azure Active Directory** > **aplikacje dla przedsiębiorstw** > aplikacji, która jest skonfigurowana dla logowania jednokrotnego > **logowanie jednokrotne** . Informacje o ustawieniach tokenu SAML w **atrybuty użytkownika** sekcji.

![Pokazuje atrybuty tokenu SAML w interfejsie użytkownika](media/active-directory-b2b-claims-mapping/view-claims-in-saml-token.png)

Istnieją dwie możliwe przyczyny, dlaczego konieczne może być Edycja oświadczeń, które są wydawane w tokenie SAML:

1. Aplikacja wymaga innego zestawu identyfikatorów URI lub wartości oświadczeń.

2. Aplikacja wymaga oświadczeń NameIdentifier inny niż główna nazwa użytkownika (UPN) przechowywanych w usłudze Azure AD.

Aby uzyskać informacje o tym, jak dodawać i edytować oświadczeń, zobacz [Dostosowywanie oświadczeń wydanych w tokenie SAML dla aplikacji przedsiębiorstwa w usłudze Azure Active Directory](develop/active-directory-saml-claims-customization.md).

Do współpracy B2B użytkowników mapowania NameID i UPN dzierżawy między będą mogły ze względów bezpieczeństwa.

## <a name="next-steps"></a>Kolejne kroki

- Informacje o właściwościach użytkownika współpracy B2B, zobacz [właściwości użytkownika współpracy usługi Azure Active Directory B2B](active-directory-b2b-user-properties.md).
- Informacje użytkownika tokeny dla użytkowników współpracy B2B, zobacz [zrozumieć tokenów użytkowników we współpracy B2B usługi Azure AD](active-directory-b2b-user-token.md).

