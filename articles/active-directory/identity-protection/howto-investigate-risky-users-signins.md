---
title: Jak zbadać ryzykowne Użytkownicy i zalogować się w usłudze Azure Active Directory Identity Protection (odświeżone) | Microsoft Docs
description: Dowiedz się, jak zbadać ryzykowne Użytkownicy i logowania w programie Azure Active Directory Identity Protection (odświeżone).
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: conceptual
ms.date: 01/25/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: c89658e962654f005eaee5ceff220d5fb343e86e
ms.sourcegitcommit: c71306fb197b433f7b7d23662d013eaae269dc9c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/22/2019
ms.locfileid: "68370324"
---
# <a name="how-to-investigate-risky-users-and-sign-ins"></a>Instrukcje: Badanie ryzykownych użytkowników i logowań 

Dzięki raportom ryzykownych logowań i ryzykownych użytkowników możesz zbadać i uzyskać wgląd w ryzyko związane ze środowiskiem. Dzięki możliwości filtrowania i sortowania ryzykownych logowań i użytkowników można lepiej zrozumieć potencjalne wtargnięcie w organizacji. 

## <a name="risky-users-report"></a>Raport ryzykownych użytkowników

Dzięki informacjom udostępnianym przez raport ryzykownych użytkowników można znaleźć odpowiedzi na pytania, takie jak:

- Którzy użytkownicy są dużym ryzykiem?
- Którzy użytkownicy mają zagrożony stan skorygowany?

Pierwszym punktem wejścia do tego raportu jest sekcja **Badanie** na stronie Zabezpieczenia.

![Raport ryzykownych użytkowników](./media/howto-investigate-risky-users-signins/01.png)

Raport dotyczący ryzykownych użytkowników ma domyślny widok, który pokazuje:

- Name (Nazwa)
- Stan ryzyka
- Poziom ryzyka
- Szczegóły ryzyka
- Ostatnia aktualizacja ryzyka
- Type
- Stan

![Raport ryzykownych użytkowników](./media/howto-investigate-risky-users-signins/03.png)

Możesz dostosować widok listy, klikając pozycję **Kolumny** na pasku narzędzi.

![Raport ryzykownych użytkowników](./media/howto-investigate-risky-users-signins/04.png)

Okno dialogowe kolumny umożliwia wyświetlanie dodatkowych pól lub usuwanie pól, które są już wyświetlane.

Klikając pozycję w widoku listy, możesz wyświetlić jej wszystkie dostępne szczegóły w widoku poziomym.

![Raport ryzykownych użytkowników](./media/howto-investigate-risky-users-signins/05.png)

Widok Szczegóły zawiera następujące informacje:

- Informacje podstawowe
- Ostatnie ryzykowne logowania
- Ryzykowne zdarzenia niepowiązane z logowaniem
- Historia ryzyka

Ponadto można:

![Raport ryzykownych użytkowników](./media/howto-investigate-risky-users-signins/08.png)

- Wyświetl skrót dotyczący wszystkich logowań, aby wyświetlić raport logowania dla tego użytkownika.
- Wyświetl wszystkie ryzykowne logowania, aby wyświetlić wszystkie logowania dla tego użytkownika, które zostały oflagowane jako ryzykowne.
- Zresetuj hasło użytkownika, jeśli uważasz, że zabezpieczenia tożsamości użytkownika zostały naruszone.
- Odrzuć ryzyko związane z użytkownikiem, jeśli uważasz, że aktywne zdarzenia ryzyka użytkownika są fałszywe. Aby uzyskać więcej informacji, zobacz artykuł [zawiera informacje zwrotne o zdarzeniach dotyczących ryzyka w Azure AD Identity Protection](howto-provide-risk-event-feedback.md).

### <a name="filter-risky-users"></a>Filtruj ryzykownych użytkowników

Aby zawęzić zgłoszone dane do poziomu, który działa dla Ciebie, można filtrować ryzykowne dane użytkowników przy użyciu następujących pól domyślnych:

- Name (Nazwa)
- Nazwa użytkownika
- Stan ryzyka
- Poziom ryzyka
- Type
- Stan

![Raport ryzykownych użytkowników](./media/howto-investigate-risky-users-signins/06.png)

Filtr **nazwy** umożliwia określenie nazwy lub głównej nazwy użytkownika (UPN), o której chcesz się zainteresować.

Filtr **stanu ryzyka** umożliwia wybranie:

- Narażeni
- Skorygowane
- Odrzucone

Filtr na **poziomie ryzyka** umożliwia wybranie:

- Wysoka
- Średni
- Małe

Filtr **typu** umożliwia wybranie:

- Gość
- Element członkowski

Filtr **stanu** umożliwia wybranie:

- Usunięte
- Aktywne

### <a name="download-risky-users-data"></a>Pobierz ryzykowne dane użytkowników

Możesz pobrać ryzykowne dane użytkowników, jeśli chcesz korzystać z nich poza Azure Portal. Kliknięcie przycisku Pobierz powoduje utworzenie pliku CSV z najnowszymi rekordami 2 500. 

![Raport ryzykownych użytkowników](./media/howto-investigate-risky-users-signins/07.png)

Możesz dostosować widok listy, klikając kolumny na pasku narzędzi.
 
Dzięki temu możesz wyświetlić dodatkowe pola lub usunąć pola, które są już wyświetlane.
 
Aby dowiedzieć się więcej na temat ryzykownego użytkownika, kliknij szufladę Details (szczegóły), aby ją rozwinąć.

## <a name="risky-sign-ins-report"></a>Raport dotyczący ryzykownych logowań

Korzystając z informacji podanych w raporcie dotyczącym ryzykownych logowań, można znaleźć odpowiedzi na pytania, takie jak:

- Ile pomyślnych logowań było w trakcie ostatniego tygodnia zdarzenia dotyczące ryzyka związanego z anonimowym adresem IP?
- Których użytkowników zostało potwierdzona w ostatnim miesiącu?
- Którzy użytkownicy mają ryzykowne logowania do portalu pakietu Office 365?

Pierwszym punktem wejścia do tego raportu jest sekcja **Badanie** na stronie Zabezpieczenia.

![Raport dotyczący ryzykownych logowań](./media/howto-investigate-risky-users-signins/02.png)

Raport dotyczący ryzykownych logowań ma domyślny widok, który pokazuje:

- Date
- Użytkownik
- Aplikacja
- Stan logowania
- Stan ryzyka
- Poziom ryzyka (zagregowany)
- Poziom ryzyka (w czasie rzeczywistym)
- Dostęp warunkowy
- Wymagana MFA  

![Raport dotyczący ryzykownych logowań](./media/howto-investigate-risky-users-signins/09.png)

Możesz dostosować widok listy, klikając pozycję **Kolumny** na pasku narzędzi.

![Raport ryzykownych użytkowników](./media/howto-investigate-risky-users-signins/11.png)

Okno dialogowe kolumny umożliwia wyświetlanie dodatkowych pól lub usuwanie pól, które są już wyświetlane.

Klikając pozycję w widoku listy, możesz wyświetlić jej wszystkie dostępne szczegóły w widoku poziomym.

![Raport ryzykownych użytkowników](./media/howto-investigate-risky-users-signins/12.png)

Widok Szczegóły zawiera następujące informacje:

- Informacje podstawowe
- Informacje o urządzeniu
- Informacje o ryzyku
- Informacje o uwierzytelnianiu wieloskładnikowym
- Dostęp warunkowy

Ponadto można:

![Raport ryzykownych użytkowników](./media/howto-investigate-risky-users-signins/13.png)

- Potwierdź naruszenie zabezpieczeń 
- Potwierdź bezpieczeństwo

Aby uzyskać więcej informacji, zobacz artykuł [zawiera informacje zwrotne o zdarzeniach dotyczących ryzyka w Azure AD Identity Protection](howto-provide-risk-event-feedback.md).

### <a name="filter-risky-sign-ins"></a>Filtruj ryzykowne logowania

Aby zawęzić zgłoszone dane do poziomu, który działa dla Ciebie, można filtrować ryzykowne dane użytkowników przy użyciu następujących pól domyślnych:

- Użytkownik
- Aplikacja
- Stan logowania
- Stan ryzyka
- Poziom ryzyka (zagregowany)
- Poziom ryzyka (w czasie rzeczywistym)
- Dostęp warunkowy
- Date
- Typ poziomu ryzyka

![Raport dotyczący ryzykownych logowań](./media/howto-investigate-risky-users-signins/14.png)

Filtr **nazwy** umożliwia określenie nazwy lub głównej nazwy użytkownika (UPN), o której chcesz się zainteresować.

Filtr **aplikacji** umożliwia określenie aplikacji w chmurze, do której użytkownik próbował uzyskać dostęp.

Filtr **Stan logowania** umożliwia wybranie jednej z następujących wartości:

- Wszyscy
- Powodzenie
- Niepowodzenie

Filtr **stanu ryzyka** umożliwia wybranie:

- Narażeni
- Potwierdzono naruszenie zabezpieczeń
- Potwierdzono bezpieczeństwo
- Odrzucone
- Skorygowane

Filtr **poziomu ryzyka (Aggregate)** umożliwia wybranie:

- Wysoka
- Średni
- Małe

Filtr **poziomu ryzyka (w czasie rzeczywistym)** umożliwia wybranie:

- Wysoka
- Średni
- Małe

Filtr **dostępu warunkowego** umożliwia wybranie:

- Wszyscy
- Nie zastosowano
- Powodzenie
- Niepowodzenie

Filtr **Data** umożliwia zdefiniowanie przedziału czasu dla zwracanych danych.
Możliwe wartości to:

- Ostatni 1 miesiąc
- Ostatnie 7 dni
- Ostatnie 24 godziny
- Niestandardowy zakres czasu

### <a name="download-risky-sign-ins-data"></a>Pobierz ryzykowne dane logowania

Możesz pobrać ryzykowne dane logowania, jeśli chcesz korzystać z nich poza Azure Portal. Kliknięcie przycisku Pobierz powoduje utworzenie pliku CSV z najnowszymi rekordami 2 500. 

![Raport ryzykownych użytkowników](./media/howto-investigate-risky-users-signins/15.png)

## <a name="next-steps"></a>Następne kroki

Aby zapoznać się z omówieniem Azure AD Identity Protection, zobacz [omówienie Azure AD Identity Protection](overview-v2.md).
