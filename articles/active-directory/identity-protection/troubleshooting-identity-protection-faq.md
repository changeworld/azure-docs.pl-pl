---
title: Często zadawane pytania dotyczące ochrony tożsamości w usłudze Azure Active Directory
description: Często zadawane pytania Dotyczące usługi Azure AD Identity Protection
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: troubleshooting
ms.date: 12/13/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 140ad45d9c4f6b6f49a4ea4aefb9298e58a2cf10
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75443574"
---
# <a name="frequently-asked-questions-identity-protection-in-azure-active-directory"></a>Często zadawane pytania Ochrona tożsamości w usłudze Azure Active Directory

## <a name="dismiss-user-risk-known-issues"></a>Odrzuć znane problemy związane z ryzykiem użytkownika

**Odrzuć ryzyko użytkownika** w klasycznej ochronie tożsamości ustawia aktora w historii ryzyka użytkownika w usłudze Identity Protection na **platformie Azure AD.**

**Odrzuć ryzyko użytkownika** w ochronie tożsamości ustawia aktora w historii ryzyka użytkownika w ochronie tożsamości na ** \<\>nazwę administratora z hiperłączem wskazującym ostrze użytkownika**.

Istnieje bieżący znany problem powodujący opóźnienie w przepływie zwolnienia ryzyka użytkownika. Jeśli masz "Zasady dotyczące ryzyka użytkownika", ta zasada przestanie obowiązywać do zwolnionych użytkowników w ciągu kilku minut od kliknięcia przycisku "Odrzuć ryzyko użytkownika". Istnieją jednak znane opóźnienia z UX odświeżając "Stan ryzyka" zwolnionych użytkowników. Aby obejść ten problem, odśwież stronę na poziomie przeglądarki, aby wyświetlić najnowszy użytkownik "Stan ryzyka".

## <a name="risky-users-report-known-issues"></a>Ryzykowni użytkownicy zgłaszają znane problemy

W kwerendach w polu **nazwy użytkownika** rozróżniana jest wielkość liter, podczas gdy w kwerendach w polu **Nazwa** wielkość liter jest niezależna od wielkości liter.

Przełączanie **Pokaż daty jako** ukrywa **ryzyko ostatnia aktualizacja** kolumny. Aby odczytać kolumnę, kliknij **kolumny** w górnej części bloku Użytkownicy ryzykowni.

**Odrzuć wszystkie zdarzenia** w klasycznej ochronie tożsamości ustawia stan wykrywania ryzyka na **Zamknięte (rozwiązane)**.

## <a name="risky-sign-ins-report-known-issues"></a>Ryzykowne logowania zgłaszają znane problemy

**Rozwiązanie** w sprawie wykrywania ryzyka ustawia stan **Użytkownicy przekazywany mfa napędzany przez zasady oparte na ryzyku**.

## <a name="frequently-asked-questions"></a>Często zadawane pytania

### <a name="why-is-a-user-is-at-risk"></a>Dlaczego użytkownik jest zagrożony?

Jeśli jesteś klientem usługi Azure AD Identity Protection, przejdź do [widoku użytkowników ryzykownych](howto-identity-protection-investigate-risk.md#risky-users) i kliknij na użytkownika zagrożonego. W szufladzie u dołu zakładka "Historia ryzyka" wyświetli wszystkie zdarzenia, które doprowadziły do zmiany ryzyka użytkownika. Aby wyświetlić wszystkie ryzykowne logowania użytkownika, kliknij "Ryzykowne logowania użytkownika". Aby wyświetlić wszystkie wykrywanie ryzyka dla tego użytkownika, kliknij "Wykrywanie ryzyka użytkownika".

### <a name="how-can-i-get-a-report-of-detections-of-a-specific-type"></a>Jak mogę uzyskać raport wykrywania określonego typu?

Przejdź do widoku i filtrowania wykrywania ryzyka według "Typu wykrywania". Następnie można pobrać ten raport w pliku . CSV lub . JSON za pomocą przycisku **Pobierz** u góry. Aby uzyskać więcej informacji, zobacz artykuł [Jak: Badanie ryzyka](howto-identity-protection-investigate-risk.md#risk-detections).

### <a name="why-cant-i-set-my-own-risk-levels-for-each-risk-detection"></a>Dlaczego nie mogę ustawić własnych poziomów ryzyka dla każdego wykrywania ryzyka?

Poziomy ryzyka w ochronie tożsamości są oparte na precyzji wykrywania i są obsługiwane przez nasze nadzorowane uczenie maszynowe. Aby dostosować środowisko, jakie użytkownicy są prezentowane, administrator może uwzględnić/wykluczyć niektórych użytkowników /grup z zasad ryzyka ryzyka użytkownika i logowania.

### <a name="why-does-the-location-of-a-sign-in-not-match-where-the-user-truly-signed-in-from"></a>Dlaczego lokalizacja logowania nie jest zgodna z miejscem, z którego użytkownik naprawdę się zalogował?

Mapowanie geolokalizacji IP jest wyzwaniem dla całej branży. Jeśli uważasz, że lokalizacja wymieniona w raporcie logowania nie jest zgodna z rzeczywistą lokalizacją, skontaktuj się z pomocą techniczną firmy Microsoft. 

### <a name="how-can-i-close-specific-risk-detections-like-i-did-in-the-old-ui"></a>Jak mogę zamknąć konkretne wykrywanie ryzyka, tak jak w starym interfejsie użytkownika?

Możesz przekazać opinię na temat wykrywania ryzyka, potwierdzając połączone logowanie jako zagrożone lub bezpieczne. Informacje zwrotne podane na logowanie spływa do wszystkich wykrywania dokonanego na tym logowanie. Jeśli chcesz zamknąć wykrywania, które nie są połączone z logowania, można podać tę opinię na poziomie użytkownika. Aby uzyskać więcej informacji, zobacz artykuł [Jak: Przekazywanie opinii o ryzyku w usłudze Azure AD Identity Protection](howto-identity-protection-risk-feedback.md).

### <a name="how-far-can-i-go-back-in-time-to-understand-whats-going-on-with-my-user"></a>Jak daleko mogę cofnąć się w czasie, aby zrozumieć, co dzieje się z moim użytkownikiem?

- Widok [ryzykownych użytkowników](howto-identity-protection-investigate-risk.md#risky-users) pokazuje, że użytkownik stoi na podstawie wszystkich wcześniejszych logów. 
- Widok [ryzykownych logów](howto-identity-protection-investigate-risk.md#risky-sign-ins) pokazuje oznaki ryzyka w ciągu ostatnich 30 dni. 
- Widok [wykrywania ryzyka](howto-identity-protection-investigate-risk.md#risk-detections) pokazuje wykrywanie ryzyka wykonane w ciągu ostatnich 90 dni.

### <a name="how-can-i-learn-more-about-a-specific-detection"></a>Jak mogę dowiedzieć się więcej o konkretnym wykrywaniu?

Wszystkie wykrywanie ryzyka są udokumentowane w artykule [Co to jest ryzyko](concept-identity-protection-risks.md#risk-types-and-detection). Możesz najechać kursorem na symbol (i) obok wykrywania w witrynie Azure portal, aby dowiedzieć się więcej o wykrywaniu.

### <a name="how-do-the-feedback-mechanisms-in-identity-protection-work"></a>Jak działają mechanizmy sprzężenia zwrotnego w ochronie tożsamości?

**Potwierdź naruszenie** (podczas logowania) — informuje usługę Azure AD Identity Protection, że logowanie nie zostało wykonane przez właściciela tożsamości i wskazuje naruszenie zabezpieczeń.

- Po otrzymaniu tej opinii przenosimy stan logowania i ryzyka użytkownika do **potwierdzonego naruszenia** i poziomu ryzyka do **wysokiego.**

- Ponadto dostarczamy informacje do naszych systemów uczenia maszynowego w celu przyszłych ulepszeń w ocenie ryzyka.

    > [!NOTE]
    > Jeśli użytkownik został już naprawiony, nie **klikaj przycisku Potwierdź,** ponieważ przenosi stan logowania i ryzyka użytkownika do **potwierdzonego naruszenia zabezpieczeń** i poziomu ryzyka do **wysokiego**.

**Potwierdź bezpieczne** (przy logowaniu) — informuje usługę Azure AD Identity Protection, że logowanie zostało wykonane przez właściciela tożsamości i nie wskazuje naruszenia zabezpieczeń.

- Po otrzymaniu tej opinii przenosimy stan ryzyka logowania (nie użytkownika) do **potwierdzonego bezpieczeństwa,** a poziom ryzyka do **-**.

- Ponadto dostarczamy informacje do naszych systemów uczenia maszynowego w celu przyszłych ulepszeń w ocenie ryzyka.

    > [!NOTE]
    > Jeśli uważasz, że użytkownik nie jest zagrożona, należy użyć **odrzucić ryzyko użytkownika** na poziomie użytkownika zamiast przy użyciu **Potwierdzone bezpieczne** na poziomie logowania. A **Odrzuć ryzyko użytkownika** na poziomie użytkownika zamyka ryzyko użytkownika i wszystkie wcześniejsze ryzykowne logowania i wykrywanie ryzyka.

### <a name="why-am-i-seeing-a-user-with-a-low-or-above-risk-score-even-if-no-risky-sign-ins-or-risk-detections-are-shown-in-identity-protection"></a>Dlaczego widzę użytkownika z niskim (lub wyższym) wynikiem ryzyka, nawet jeśli w ucho.

Biorąc pod uwagę, że ryzyko użytkownika ma charakter kumulatywny i nie wygasa, użytkownik może mieć ryzyko użytkownika niskie lub wyższe, nawet jeśli nie ma ostatnio żadnych ryzykownych logów lub wykrywania ryzyka pokazano w ochronie tożsamości. Taka sytuacja może się zdarzyć, jeśli tylko złośliwe działanie użytkownika miało miejsce poza ramy czasowe, dla których przechowujemy szczegóły ryzykownych logowania i wykrywania ryzyka. Nie wygasamy ryzyko użytkownika, ponieważ złe podmioty były znane pozostać w środowisku klientów ponad 140 dni za zagrożone tożsamości przed rozkręceniem ich ataku. Klienci mogą przejrzeć oś czasu ryzyka użytkownika, aby zrozumieć, dlaczego użytkownik jest zagrożony, przechodząc do:`Azure Portal > Azure Active Directory > Risky users’ report > Click on an at-risk user > Details’ drawer > Risk history tab`

### <a name="why-does-a-sign-in-have-a-sign-in-risk-aggregate-score-of-high-when-the-detections-associated-with-it-are-of-low-or-medium-risk"></a>Dlaczego logowanie ma wynik "ryzyka logowania (agregacji)" wysoki, gdy wykrywania skojarzone z nim są niskiego lub średniego ryzyka?

Wynik wysokiego ryzyka zagregowanego może być oparty na innych funkcjach logowania lub na fakcie, że dla tego logowania zostało wystrzelonych więcej niż jedno wykrycie. I odwrotnie, logowanie może mieć ryzyko logowania (agregacja) medium, nawet jeśli wykrywania skojarzone z logowania są wysokiego ryzyka. 
