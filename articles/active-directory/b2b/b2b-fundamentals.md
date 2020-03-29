---
title: Najważniejsze wskazówki i zalecenia dotyczące usługi Azure Active Directory B2B
description: Poznaj najlepsze rozwiązania i zalecenia dotyczące dostępu użytkownika-gościa między firmami (B2B) w usłudze Azure Active Directory.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 12/18/2019
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: elisol
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 54f5721ef606b6ea916f5a00031c58f5e2adeb0e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80050849"
---
# <a name="azure-active-directory-b2b-best-practices"></a>Najważniejsze wskazówki dotyczące usługi Azure Active Directory B2B
Ten artykuł zawiera zalecenia i najlepsze rozwiązania dotyczące współpracy między firmami (B2B) w usłudze Azure Active Directory (Azure AD).

   > [!IMPORTANT]
   > **Począwszy od 31 marca 2021**r. firma Microsoft nie będzie już obsługiwać realizacji zaproszeń, tworząc niezarządzane konta usługi Azure AD i dzierżawy scenariuszy współpracy B2B. W ramach przygotowań zachęcamy klientów do wyrażenia zgody na [jednorazowe uwierzytelnianie kodem dostępu pocztą e-mail.](one-time-passcode.md) Cieszymy się z waszych opinii na temat tej publicznej funkcji podglądu i cieszymy się, że możemy stworzyć jeszcze więcej sposobów współpracy.

## <a name="b2b-recommendations"></a>Zalecenia B2B
| Zalecenie | Komentarze |
| --- | --- |
| Aby uzyskać optymalne środowisko logowania, można powiększyć się z dostawcami tożsamości | Jeśli to możliwe, należy łączyć bezpośrednio z dostawcami tożsamości, aby umożliwić zaproszonym użytkownikom logowanie się do udostępnionych aplikacji i zasobów bez konieczności tworzenia kont Microsoft (MSA) lub kont usługi Azure AD. Możesz użyć [funkcji federacji Google,](google-federation.md) aby umożliwić użytkownikom-gościom B2B logowanie się na ich kontach Google. Można też użyć [funkcji Federacja bezpośrednia (wersja zapoznawcza)](direct-federation.md) do skonfigurowania bezpośredniej federacji z dowolną organizacją, której dostawca tożsamości (IdP) obsługuje protokół SAML 2.0 lub WS-Fed. |
| Korzystanie z funkcji jednorazowego kodu (podglądu) poczty e-mail dla gości B2B, którzy nie mogą uwierzytelnić się w inny sposób | Funkcja [jednorazowego kodu (podglądu) poczty e-mail](one-time-passcode.md) uwierzytelnia użytkowników-gości B2B, gdy nie można ich uwierzytelnić za pomocą innych środków, takich jak usługa Azure AD, konto Microsoft (MSA) lub federacja Google. Gdy użytkownik-gość realizuje zaproszenie lub uzyskuje dostęp do zasobu udostępnionego, może zażądać kodu tymczasowego, który jest wysyłany na ich adres e-mail. Następnie wprowadź ten kod, aby kontynuować logowanie. |
| Dodawanie znakowania firmowego do strony logowania | Możesz dostosować stronę logowania, aby była bardziej intuicyjna dla użytkowników-gości B2B. Zobacz, jak [dodać markę firmową do logowania i strony panelu dostępu.](../fundamentals/customize-branding.md) |
| Dodaj swoje zasady zachowania poufności informacji do środowiska realizacji użytkownika gościa B2B | Możesz dodać adres URL zasad zachowania poufności informacji w organizacji do procesu realizacji zaproszenia po raz pierwszy, aby zaproszony użytkownik musiał wyrazić zgodę na warunki zachowania poufności informacji. Zobacz [instrukcje: dodawanie informacji o ochronie prywatności organizacji w usłudze Azure Active Directory](https://aka.ms/adprivacystatement). |
| Użyj funkcji zaproszenia zbiorczego (podglądu), aby zaprosić wielu użytkowników-gości B2B w tym samym czasie | Zaproś wielu użytkowników-gości do swojej organizacji w tym samym czasie przy użyciu funkcji zbiorczego podglądu zaproszenia w witrynie Azure portal. Ta funkcja umożliwia przekazywanie pliku CSV w celu tworzenia użytkowników-gości B2B i wysyłania zaproszeń zbiorczo. Zobacz [Samouczek dla zbiorczego zapraszania użytkowników B2B](tutorial-bulk-invite.md). |
| Wymuszanie zasad dostępu warunkowego dla uwierzytelniania wieloskładnikowego (MFA) | Zalecamy wymuszanie zasad usługi MFA w aplikacjach, które chcesz udostępnić partnerom B2B. W ten sposób usługa MFA będzie konsekwentnie wymuszana w aplikacjach w dzierżawie, niezależnie od tego, czy organizacja partnerska korzysta z usługi MFA. Zobacz [Dostęp warunkowy dla użytkowników współpracy B2B](conditional-access.md). |
| Jeśli wymuszasz zasady dostępu warunkowego oparte na urządzeniach, użyj list wykluczeń, aby zezwolić na dostęp użytkownikom B2B | Jeśli zasady dostępu warunkowego oparte na urządzeniach są włączone w organizacji, urządzenia użytkowników-gości B2B zostaną zablokowane, ponieważ nie są zarządzane przez organizację. Można utworzyć listy wykluczeń zawierające określonych użytkowników partnerskich, aby wykluczyć ich z zasad dostępu warunkowego opartego na urządzeniach. Zobacz [Dostęp warunkowy dla użytkowników współpracy B2B](conditional-access.md). |
| Używanie adresu URL specyficznego dla dzierżawy podczas podawania bezpośrednich linków do użytkowników-gości B2B | Alternatywą dla wiadomości e-mail z zaproszeniem można nadać gościowi bezpośredni link do aplikacji lub portalu. To bezpośrednie łącze musi być specyficzne dla dzierżawy, co oznacza, że musi zawierać identyfikator dzierżawy lub zweryfikowaną domenę, aby gość mógł być uwierzytelniony w dzierżawie, w której znajduje się udostępniona aplikacja. Zobacz [Środowisko realizacji dla użytkownika-gościa](redemption-experience.md). |
| Podczas tworzenia aplikacji użyj usertype, aby określić środowisko użytkownika gościa  | Jeśli tworzysz aplikację i chcesz zapewnić różne środowiska dla użytkowników dzierżawy i użytkowników-gości, użyj UserType właściwości. UserType oświadczenie nie jest obecnie uwzględniony w tokenie. Aplikacje powinny używać interfejsu API programu Microsoft Graph do wykonywania zapytań o katalog użytkownika w celu uzyskania typu użytkownika. |
| Zmienianie właściwości UserType *tylko* wtedy, gdy zmienia się relacja użytkownika z organizacją | Chociaż istnieje możliwość użycia programu PowerShell do konwersji UserType właściwości dla użytkownika z Elementu członkowskiego do gościa (i odwrotnie), należy zmienić tę właściwość tylko wtedy, gdy relacja użytkownika do organizacji ulegnie zmianie. Zobacz [Właściwości użytkownika gościa B2B](user-properties.md).|

## <a name="next-steps"></a>Następne kroki

[Zarządzanie udostępnianiem B2B](delegate-invitations.md)
