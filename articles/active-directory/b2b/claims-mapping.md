---
title: Mapowanie oświadczeń użytkowników współpracy B2B — usługa Azure Active Directory
description: Dostosuj oświadczenia użytkowników, które są wystawiane w tokenie SAML dla użytkowników B2B usługi Azure Active Directory (Azure AD).
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 04/06/2018
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: elisolMS
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3508865d9f89501cf70e09087c6a609beb6662b2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74273185"
---
# <a name="b2b-collaboration-user-claims-mapping-in-azure-active-directory"></a>Mapowanie oświadczeń użytkowników współpracy B2B w usłudze Azure Active Directory

Usługa Azure Active Directory (Azure AD) obsługuje dostosowywanie oświadczeń, które są wystawiane w tokenie SAML dla użytkowników współpracy B2B. Gdy użytkownik uwierzytelnia się w aplikacji, usługa Azure AD wystawia token SAML do aplikacji, która zawiera informacje (lub oświadczenia) o użytkowniku, który jednoznacznie identyfikuje je. Domyślnie obejmuje to nazwę użytkownika, adres e-mail, imię i nazwisko.

W [witrynie Azure portal](https://portal.azure.com)można wyświetlić lub edytować oświadczenia, które są wysyłane w tokenie SAML do aplikacji. Aby uzyskać dostęp do ustawień, wybierz pozycję **Aplikacje usługi Azure Active Directory** > **Enterprise** > aplikacji skonfigurowaną do logowania jednokrotnego > **logowanie jednokrotne**. Zobacz ustawienia tokenu SAML w sekcji **Atrybuty użytkownika.**

![Pokazuje atrybuty tokenu SAML w interfejsie użytkownika](media/claims-mapping/view-claims-in-saml-token.png)

Istnieją dwa możliwe powody, dla których może być konieczne edytowanie oświadczeń, które są wystawiane w tokenie SAML:

1. Aplikacja wymaga innego zestawu identyfikatorów URI oświadczeń lub wartości oświadczeń.

2. Aplikacja wymaga NameIdentifier oświadczenia jest coś innego niż nazwa główna użytkownika (UPN), który jest przechowywany w usłudze Azure AD.

Aby uzyskać informacje dotyczące dodawania i edytowania oświadczeń, zobacz [Dostosowywanie oświadczeń wystawionych w tokenie SAML dla aplikacji dla przedsiębiorstw w usłudze Azure Active Directory](../develop/active-directory-saml-claims-customization.md).

W przypadku użytkowników współpracujących b2b mapowanie nameid i międzynajedynachów upn jest uniemożliwione ze względów bezpieczeństwa.

## <a name="next-steps"></a>Następne kroki

- Aby uzyskać informacje o właściwościach użytkownika współpracy B2B, zobacz [Właściwości użytkownika współpracy usługi Azure Active Directory B2B](user-properties.md).
- Aby uzyskać informacje na temat tokenów użytkowników dla użytkowników współpracujących z B2B, zobacz [Opis tokenów użytkowników we współpracy z usługą Azure AD B2B.](user-token.md)

