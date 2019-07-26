---
title: Jak poprawić dokładność wykrywania w Azure Active Directory Identity Protection (odświeżone) | Microsoft Docs
description: Jak poprawić dokładność wykrywania w Azure Active Directory Identity Protection (odświeżone).
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: conceptual
ms.date: 01/31/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 32bb8de7970fc167a6a95e9d9c3c71e4e1dc0150
ms.sourcegitcommit: e9c866e9dad4588f3a361ca6e2888aeef208fc35
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/19/2019
ms.locfileid: "68333939"
---
# <a name="how-to-improve-the-detection-accuracy"></a>Instrukcje: Zwiększ dokładność wykrywania 

Usługa Identity Protection udostępnia mechanizmy przekazywania informacji zwrotnych do usługi Azure AD na potrzeby wykrywania ryzyka w danym środowisku. Aby przekazać opinię, można potwierdzić stan wykrytego ryzykownego zdarzenia użytkownika lub logowania. Użytkownicy firmy Microsoft mogą wykonać akcję dotyczącą bieżącego wykrywania ryzyka i poprawić dokładność wykrycia w przyszłości. 

## <a name="what-is-detection"></a>Co to jest wykrywanie?

Wykrywanie to proces identyfikowania podejrzanych działań w połączeniu z kontami użytkowników. Podejrzane działania mogą być wykrywane przez usługę Azure AD jako [zdarzenie ryzyka](../reports-monitoring/concept-risk-events.md). Proces wykrywania jest oparty na adaptacyjnych algorytmach uczenia maszynowego i heurystycznych w celu wykrywania zdarzeń ryzyka dla użytkowników.

Wyniki wykrywania służą do określenia, czy użytkownicy i logowania są zagrożone. 

## <a name="how-can-i-improve-the-detection-accuracy"></a>Jak można poprawić dokładność wykrywania?

Ponieważ wykrywanie jest procesem zautomatyzowanym, istnieje możliwość, że raporty usługi Azure AD będą fałszywie pozytywne. Dokładność wykrywania można poprawić, przekazując informacje zwrotne do usługi Azure AD dotyczące wyników wykrywania.

Istnieją trzy sposoby ulepszania dokładności wykrywania: Potwierdź złamane logowanie, potwierdź bezpieczne logowanie i Odrzuć ryzyko związane z użytkownikiem. Można to zrobić z następujących raportów:

- **Raport dotyczący ryzykownych logowań —** W raporcie ryzykowne logowania można potwierdzić, że logowanie jest bezpieczne lub naruszone
- **Raport dotyczący ryzykownych użytkowników —** W raporcie ryzykowni użytkownicy można odrzucić ryzyko związane z użytkownikiem 

Twoja opinia jest przetwarzana przez usługę Azure AD w celu poprawienia dokładności wyników wykrywania. Zwykle wyrażasz opinię w ramach ryzyka użytkownika lub związanego z logowaniem. Aby uzyskać więcej informacji, zobacz [jak zbadać ryzykowne Użytkownicy i logowania](howto-investigate-risky-users-signins.md).

## <a name="confirm-compromised"></a>Potwierdź naruszenie bezpieczeństwa

Potwierdzenie zdarzenia logowania jako sygnałów złamanych do usługi Azure AD, że logowanie nie zostało autoryzowane przez właściciela tożsamości. Po wybraniu opcji "Potwierdź złamane" usługa Azure AD będzie

- Zwiększ ryzyko dla użytkownika, którego dotyczy ten użytkownik.
- Pomóż zoptymalizować Uczenie maszynowe, które wykrywa zdarzenia ryzyka
- Wykonaj dodatkowe środki, aby dodatkowo chronić organizację

Aby potwierdzić złamane Logowanie:

- **Raport dotyczący ryzykownych** logowań — ta opcja pozwala potwierdzić złamane logowanie w przypadku co najmniej jednego zdarzenia logowania.

   ![Odrzuć ryzyko związane z użytkownikiem](./media/howto-improve-detection-accuracy/07.png)

- **Widok szczegółów raportu** dotyczącego ryzykownych logowań — ta opcja umożliwia potwierdzenie zaatakowanego konta dla wybranego zdarzenia logowania w raporcie ryzykowne logowania. 

   ![Odrzuć ryzyko związane z użytkownikiem](./media/howto-improve-detection-accuracy/04.png)
 
## <a name="confirm-safe"></a>Potwierdź bezpieczeństwo

Potwierdzenie zdarzenia logowania jako bezpiecznego sygnałów do usługi Azure AD, że logowanie **zostało** autoryzowane przez odpowiedniego właściciela tożsamości. Po wybraniu opcji "Potwierdź bezpieczną" usługa Azure AD będzie:

- Przywróć udział ryzyka użytkownika dla wybranych logowań
- Zamknij bazowe zdarzenia ryzyka
- Pomóż zoptymalizować Uczenie maszynowe, które wykrywa zdarzenia ryzyka
- Wykonaj dodatkowe środki, aby dodatkowo chronić organizację
 
Aby potwierdzić bezpieczne logowanie w programie:

- **Raport dotyczący ryzykownych** logowań — ta opcja umożliwia potwierdzenie bezpiecznego logowania w przypadku co najmniej jednego zdarzenia logowania.

   ![Odrzuć ryzyko związane z użytkownikiem](./media/howto-improve-detection-accuracy/08.png)

- **Widok szczegółów raportu** dotyczącego ryzykownych logowań — ta opcja umożliwia potwierdzenie bezpiecznego logowania na potrzeby wybranego zdarzenia logowania w raporcie ryzykowne operacje logowania. 

   ![Odrzuć ryzyko związane z użytkownikiem](./media/howto-improve-detection-accuracy/05.png)

## <a name="dismiss-user-risk"></a>Odrzuć ryzyko związane z użytkownikiem

Jeśli masz już akcje naprawcze dla użytkownika o podwyższonym ryzyku lub uważasz, że zostały one oznaczone jako ryzykowne, możesz odrzucić ryzyko związane z użytkownikiem. Odrzucanie ryzyka użytkownika powoduje przywrócenie stanu niezwiązanego z ryzykiem. Wszystkie wcześniejsze ryzykowne logowania i zdarzenia ryzyka dla wybranego użytkownika zostaną odrzucone.

Zgłoszone ryzyko użytkownika można odrzucić w programie:

- **Raport dotyczący ryzykownych użytkowników** — ta opcja umożliwia odrzucanie ryzyka użytkownika dla co najmniej jednego z wybranych użytkowników.

   ![Odrzuć ryzyko związane z użytkownikiem](./media/howto-improve-detection-accuracy/02.png)

- **Widok szczegółów** — ta opcja umożliwia odrzucanie ryzyka użytkownika dla wybranego użytkownika w raporcie o ryzyku użytkownika. 

   ![Odrzuć ryzyko związane z użytkownikiem](./media/howto-improve-detection-accuracy/01.png)

**Co należy wiedzieć:**

- Nie można przywrócić tej akcji.
- Wykonanie tej akcji może potrwać kilka minut, dlatego nie należy ponownie przesyłać żądania.
- Tę akcję można wykonać tylko wtedy, gdy AD zarządza poświadczeniami użytkownika. 

## <a name="best-practices"></a>Najlepsze praktyki

Odrzucanie ryzyka użytkownika jest jednym ze sposobów odblokowania ich, jeśli zostały zablokowane przez zasady ryzyka użytkownika i nie może zostać skorygowany z powodu braku możliwości resetowania haseł i/lub włączenia uwierzytelniania MFA. W tej sytuacji najlepszym rozwiązaniem jest upewnienie się, że użytkownik będzie rejestrował się do resetowania haseł i uwierzytelniania wieloskładnikowego, aby umożliwić samodzielne korygowanie wszelkich przyszłych zdarzeń związanych z ryzykiem.

## <a name="next-steps"></a>Kolejne kroki

Aby zapoznać się z omówieniem Azure AD Identity Protection, zobacz [omówienie Azure AD Identity Protection](overview-v2.md).
