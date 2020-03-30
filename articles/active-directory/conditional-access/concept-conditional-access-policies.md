---
title: Tworzenie zasad dostępu warunkowego — usługa Azure Active Directory
description: Jakie są wszystkie opcje dostępne do tworzenia zasad dostępu warunkowego i co one oznaczają?
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 03/25/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: 907ad8705742e4b2e38b13c3c675ebd333bd27d2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80295321"
---
# <a name="building-a-conditional-access-policy"></a>Tworzenie zasad dostępu warunkowego

Jak wyjaśniono w artykule [Co to jest dostęp warunkowy,](overview.md)zasada dostępu warunkowego jest instrukcją if-then, kontroli **przydziałów** i **dostępu.** Zasady dostępu warunkowego łączy sygnały, do podejmowania decyzji i egzekwowania zasad organizacji.

W jaki sposób organizacja tworzy te zasady? Co jest wymagane?

![Dostęp warunkowy (sygnały + decyzje + egzekwowanie = zasady)](./media/concept-conditional-access-policies/conditional-access-signal-decision-enforcement.png)

## <a name="assignments"></a>Przypisania

Część przydziałów kontroluje kto, co i gdzie zasady dostępu warunkowego.

### <a name="users-and-groups"></a>Użytkownicy i grupy

[Użytkownicy i grupy](concept-conditional-access-users-groups.md) przypisują, kogo zasady będą uwzględniać lub wykluczać. To przypisanie może obejmować wszystkich użytkowników, określone grupy użytkowników, role katalogu lub zewnętrznych użytkowników-gości. 

### <a name="cloud-apps-or-actions"></a>Aplikacje w chmurze lub akcje

[Aplikacje lub akcje w chmurze](concept-conditional-access-cloud-apps.md) mogą zawierać lub wykluczać aplikacje w chmurze lub akcje użytkownika, które będą podlegać zasadom.

### <a name="conditions"></a>Warunki

Zasady mogą zawierać wiele [warunków](concept-conditional-access-conditions.md).

#### <a name="sign-in-risk"></a>Ryzyko logowania

W przypadku organizacji z [usługą Azure AD Identity Protection](../identity-protection/overview.md)wygenerowane tam wykrywanie ryzyka może mieć wpływ na zasady dostępu warunkowego.

#### <a name="device-platforms"></a>Platformy urządzeń

Organizacje z wieloma platformami systemów operacyjnych urządzeń mogą chcieć wymusić określone zasady na różnych platformach. 

Informacje używane do obliczania platformy urządzenia pochodzą z niezweryfikowanych źródeł, takich jak ciągi agenta użytkownika, które można zmienić.

#### <a name="locations"></a>Lokalizacje

Dane o lokalizacji są dostarczane przez dane geolokalizacji IP. Administratorzy mogą definiować lokalizacje i oznaczać je jako zaufane, jak te dla lokalizacji sieciowych organizacji.

#### <a name="client-apps"></a>Aplikacje klienckie

Domyślnie zasady dostępu warunkowego mają zastosowanie do aplikacji przeglądarki, aplikacji mobilnych i klientów klasycznych obsługujących nowoczesne uwierzytelnianie. 

Ten warunek przypisania umożliwia zasadom dostępu warunkowego kierowanie określonych aplikacji klienckich, które nie używają nowoczesnego uwierzytelniania. Aplikacje te obejmują klientów Programu Exchange ActiveSync, starsze aplikacje pakietu Office, które nie używają nowoczesnego uwierzytelniania, oraz protokoły pocztowe, takie jak IMAP, MAPI, POP i SMTP.

#### <a name="device-state"></a>Stan urządzenia

Ten formant służy do wykluczania urządzeń, które są przyłączone do usługi Azure AD lub oznaczone jako zgodne w usłudze Intune. To wykluczenie można wykonać, aby zablokować urządzenia niezarządzane. 

## <a name="access-controls"></a>Kontrole dostępu

Część kontroli dostępu w zasadach dostępu warunkowego określa sposób wymuszania zasad.

### <a name="grant"></a>Uprawnienie

[Grant](concept-conditional-access-grant.md) zapewnia administratorom środki wymuszania zasad, w których mogą blokować lub udzielać dostępu.

#### <a name="block-access"></a>Blokowanie dostępu

Blok dostępu właśnie to, będzie blokować dostęp w ramach określonych przydziałów. Sterowanie blokiem jest potężne i powinno być dzierżone z odpowiednią wiedzą.

#### <a name="grant-access"></a>Udzielanie dostępu

Kontrola dotacji może wywołać wymuszanie jednego lub więcej formantów. 

- Wymagaj uwierzytelniania wieloskładnikowego (uwierzytelniania wieloskładnikowego azure)
- Wymagaj, aby urządzenie było oznaczone jako zgodne (usługa Intune)
- Wymagaj urządzenia przyłączającego do usługi Azure AD
- Wymaganie zatwierdzonej aplikacji klienckiej
- Wymaganie zasad ochrony aplikacji

Administratorzy mogą wymagać jednego z poprzednich formantów lub wszystkich wybranych formantów, korzystając z następujących opcji. Domyślnie dla wielu formantów jest wymagać wszystkich.

- Wymagaj wszystkich wybranych elementów sterujących (sterowania i sterowania)
- Wymagaj jednego z wybranych formantów (formantu lub formantu)

### <a name="session"></a>Sesja

[Formanty sesji](concept-conditional-access-session.md) mogą ograniczać 

- Korzystanie z ograniczeń wymuszonych przez aplikację
   - Obecnie działa tylko z usługą Exchange Online i SharePoint Online.
      - Przekazuje informacje o urządzeniu, aby umożliwić kontrolę nad doświadczeniem, przyznając pełny lub ograniczony dostęp.
- Korzystanie z kontroli aplikacji dostępu warunkowego
   - Używa sygnałów z usługi Microsoft Cloud App Security do wykonywania takich czynności, jak: 
      - Blokuj pobieranie, wycinanie, kopiowanie i drukowanie poufnych dokumentów.
      - Monitoruj ryzykowne zachowanie sesji.
      - Wymagaj etykietowania poufnych plików.
- Częstotliwość logowania
   - Możliwość zmiany domyślnej częstotliwości logowania dla nowoczesnego uwierzytelniania.
- Trwała sesja przeglądarki
   - Umożliwia użytkownikom pozostanie zalogowany po zamknięciu i ponownym otwarciu okna przeglądarki.

## <a name="simple-policies"></a>Proste zasady

Zasady dostępu warunkowego muszą zawierać co najmniej następujące elementy, które mają być wymuszane:

- **Nazwa** zasady.
- **Przypisania**
   - **Użytkownicy i/lub grupy,** do które mają być stosowane zasady.
   - **Aplikacje w chmurze lub akcje,** do które należy zastosować zasady.
- **Kontrole dostępu**
   - **Kontrolki udzielania** lub **blokowania**

![Puste zasady dostępu warunkowego](./media/concept-conditional-access-policies/conditional-access-blank-policy.png)

Artykuł [Wspólne zasady dostępu warunkowego](concept-conditional-access-policy-common.md) zawiera niektóre zasady, które uważamy za przydatne dla większości organizacji.

## <a name="next-steps"></a>Następne kroki

[Symulowanie zachowania logowania za pomocą narzędzia Co jeśli dostęp warunkowy](troubleshoot-conditional-access-what-if.md)

[Planowanie wdrożenia usługi Azure Multi-Factor Authentication opartej na chmurze](../authentication/howto-mfa-getstarted.md)

[Zarządzanie zgodnością urządzeń z usłudze Intune](/intune/device-compliance-get-started)

[Zabezpieczenia aplikacji w chmurze firmy Microsoft i dostęp warunkowy](/cloud-app-security/proxy-intro-aad)
