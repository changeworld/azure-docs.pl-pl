---
title: Formanty niestandardowe w usłudze Azure AD Dostęp warunkowy
description: Dowiedz się, jak działają formanty niestandardowe w usłudze Azure Active Directory Dostęp warunkowy.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: article
ms.date: 03/18/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: inbarc
ms.collection: M365-identity-device-management
ms.openlocfilehash: f8c149279a755eb186a3fdc7891e9b511d18c7f2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80050551"
---
# <a name="custom-controls-preview"></a>Formanty niestandardowe (wersja zapoznawcza)

Formanty niestandardowe to funkcja podglądu usługi Azure Active Directory. Podczas korzystania z formantów niestandardowych użytkownicy są przekierowywani do zgodnej usługi w celu spełnienia wymagań uwierzytelniania poza usługą Azure Active Directory. Aby spełnić ten formant, przeglądarka użytkownika jest przekierowywane do usługi zewnętrznej, wykonuje wszelkie wymagane uwierzytelnianie, a następnie jest przekierowywane z powrotem do usługi Azure Active Directory. Usługa Azure Active Directory weryfikuje odpowiedź, a jeśli użytkownik został pomyślnie uwierzytelniony lub zweryfikowany, użytkownik kontynuuje przepływ dostępu warunkowego.

> [!NOTE]
> Aby uzyskać więcej informacji na temat zmian, które planujemy w funkcji kontroli niestandardowej, zobacz luty 2020 [Co nowego aktualizować](../fundamentals/whats-new.md#upcoming-changes-to-custom-controls).

## <a name="creating-custom-controls"></a>Tworzenie niestandardowych formantów

Formanty niestandardowe współpracuje z ograniczonym zestawem zatwierdzonych dostawców uwierzytelniania. Aby utworzyć formant niestandardowy, należy najpierw skontaktować się z dostawcą, którego chcesz wykorzystać. Każdy dostawca firmy innej niż Microsoft ma swój własny proces i wymagania, aby zarejestrować się, subskrybować lub w inny sposób stać się częścią usługi i wskazać, że chcesz zintegrować z dostępem warunkowym. W tym momencie dostawca dostarczy Ci blok danych w formacie JSON. Te dane umożliwiają dostawcy i dostępu warunkowego do współpracy dla dzierżawy, tworzy nowy formant i określa, jak dostęp warunkowy może stwierdzić, czy użytkownicy pomyślnie wykonali weryfikację z dostawcą.

Skopiuj dane JSON, a następnie wklej go do powiązanego pola tekstowego. Nie należy wprowadzać żadnych zmian w JSON, chyba że jawnie zrozumieć zmiany, które wprowadzasz. Wprowadzenie jakichkolwiek zmian może spowodować przerwanie połączenia między dostawcą a firmą Microsoft i potencjalnie zablokować użytkownika i użytkowników z kont.

Opcja utworzenia formantu niestandardowego znajduje się w sekcji **Zarządzanie** na stronie **Dostęp warunkowy.**

![Kontrola](./media/controls/82.png)

Kliknięcie **przycisku Nowy formant niestandardowy**powoduje otwarcie bloku z polem tekstowym dla danych JSON formantu.  

![Kontrola](./media/controls/81.png)

## <a name="deleting-custom-controls"></a>Usuwanie formantów niestandardowych

Aby usunąć formant niestandardowy, należy najpierw upewnić się, że nie jest on używany w żadnych zasadach dostępu warunkowego. Po zakończeniu:

1. Przejdź do listy Kontrole niestandardowe
1. Kliknij...  
1. Wybierz pozycję **Usuń**.

## <a name="editing-custom-controls"></a>Edytowanie formantów niestandardowych

Aby edytować formant niestandardowy, należy usunąć bieżący formant i utworzyć nowy formant ze zaktualizowanymi informacjami.

## <a name="known-limitations"></a>Znane ograniczenia

Formantów niestandardowych nie można używać z automatyzacją usługi Identity Protection wymagającą uwierzytelniania wieloskładnikowego platformy Azure, samoobsługowego resetowania haseł usługi Azure AD, spełniania wymagań dotyczących oświadczeń dotyczących uwierzytelniania wieloskładnikowego ani podnoszenia ról w uprzywilejowanych Menedżer tożsamości (PIM).

## <a name="next-steps"></a>Następne kroki

- [Wspólne zasady dostępu warunkowego](concept-conditional-access-policy-common.md)

- [Tryb samego raportu](concept-conditional-access-report-only.md)

- [Symulowanie zachowania logowania za pomocą narzędzia Co jeśli dostęp warunkowy](troubleshoot-conditional-access-what-if.md)
