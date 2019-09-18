---
title: Wyłącz weryfikację poczty e-mail podczas rejestracji w usłudze Azure Active Directory B2C | Microsoft Docs
description: Temat pokazujący, jak wyłączyć weryfikację wiadomości e-mail podczas tworzenia konta użytkownika w programie Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 11/30/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: d827843bdbaff1ad95dce3318f39867abe8b2d0f
ms.sourcegitcommit: f209d0dd13f533aadab8e15ac66389de802c581b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/17/2019
ms.locfileid: "71065618"
---
# <a name="disable-email-verification-during-consumer-sign-up-in-azure-active-directory-b2c"></a>Wyłącz weryfikację poczty e-mail podczas rejestracji w usłudze Azure Active Directory B2C
Gdy ta opcja jest włączona, Azure Active Directory B2C (Azure AD B2C) umożliwia konsumentowi zarejestrowanie się w aplikacjach przez podanie adresu e-mail i utworzenie konta lokalnego. Azure AD B2C zapewnia prawidłowe adresy e-mail, wymagając od użytkowników zweryfikowania ich podczas procesu rejestracji. Uniemożliwia to również złośliwy zautomatyzowany proces generowania fałszywych kont dla aplikacji.

Niektórzy deweloperzy aplikacji wolą pominąć weryfikację wiadomości e-mail podczas procesu rejestracji, a zamiast tego powinni zweryfikować adres e-mail w późniejszym czasie. Aby to umożliwić, Azure AD B2C można skonfigurować w celu wyłączenia weryfikacji wiadomości e-mail. W ten sposób powstaje proces tworzenia nowego konta i umożliwia deweloperom elastyczność rozróżniania konsumentów, którzy sprawdzili swój adres e-mail od tych użytkowników, którzy nie zostali.

Domyślnie przepływy użytkowników rejestracji mają włączoną weryfikację poczty e-mail. Wykonaj następujące kroki, aby je wyłączyć:

1. Kliknij pozycję **przepływy użytkownika**.
2. Kliknij swój przepływ użytkownika (na przykład "B2C_1_SiUp"), aby go otworzyć.
3. Kliknij pozycję **układy strony**.
4. Kliknij pozycję **konto lokalne Rejestracja strony**.
5. Kliknij pozycję **adres e-mail** w kolumnie **Nazwa** w sekcji **atrybuty użytkownika** .
6. W obszarze **wymaga weryfikacji**wybierz pozycję **nie**.
7. Kliknij pozycję **Zapisz** w górnej części bloku. Wszystko gotowe!

> [!NOTE]
> Wyłączenie weryfikacji poczty e-mail w procesie tworzenia konta może prowadzić do spamu. Jeśli wyłączysz wartość domyślną, zalecamy dodanie własnego systemu weryfikacyjnego.
>
>
