---
title: Jak zwiększyć dokładność wykrywania w usłudze Azure Active Directory Identity Protection (odświeżane) | Dokumentacja firmy Microsoft
description: Jak zwiększyć dokładność wykrywania w usłudze Azure Active Directory Identity Protection (odświeżane).
services: active-directory
keywords: Usługa Azure active directory identity protection odnajdywania aplikacji w chmurze, zarządzanie aplikacji, zabezpieczenia, ryzyka, poziom ryzyka, luk w zabezpieczeniach, zasady zabezpieczeń
documentationcenter: ''
author: MicrosoftGuyJFlo
manager: mtillman
ms.assetid: e7434eeb-4e98-4b6b-a895-b5598a6cccf1
ms.service: active-directory
ms.subservice: identity-protection
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/31/2019
ms.author: joflore
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7724d69a9294b420ca061d5ad26ad64826372203
ms.sourcegitcommit: 6da4959d3a1ffcd8a781b709578668471ec6bf1b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2019
ms.locfileid: "58517678"
---
# <a name="how-to-improve-the-detection-accuracy"></a>Instrukcje: Zwiększania dokładności wykrywania 

Identity Protection udostępnia mechanizmy, aby przekazać opinię z usługą Azure AD na wykrywanie ryzyka w danym środowisku. Aby przekazać opinię, można potwierdzić stanu wykrytych ryzykownych użytkowników lub zdarzeń logowania. Microsoft użytkownicy tej opinii do podejmowanie akcji na bieżącym wykryć zagrożenia i zwiększyć dokładność wykrywania w przyszłości. 


## <a name="what-is-detection"></a>Co to jest wykrywanie?

Wykrywanie polega na identyfikacji podejrzanych działań w połączeniu z kontami użytkowników. Podejrzane działania usługi Azure AD może wykryć, są nazywane [zdarzenie o podwyższonym ryzyku](../reports-monitoring/concept-risk-events.md). Proces wykrywania opiera się na adaptacyjne algorytmy uczenia maszynowego i algorytmów heurystycznych w celu wykrycia zdarzenia o podwyższonym ryzyku dla użytkowników.

Wyniki wykrywania są używane do ustalenia, czy użytkownicy i logowania są zagrożone. 


## <a name="how-can-i-improve-the-detection-accuracy"></a>Jak mogę poprawić dokładność wykrywania

Ponieważ wykrywania jest zautomatyzowany proces jest możliwe, że usługa Azure AD zgłasza wyniki fałszywie dodatnie. Aby zwiększyć dokładność wykrywania, należy opinii do usługi Azure AD dotyczące wyników wykrywania.

Istnieją trzy sposoby, aby zwiększyć dokładność wykrywania: Upewnij się, których bezpieczeństwo zostało naruszone logowania, comfirm bezpiecznego logowania i odrzucić ryzyka związanego z użytkownikiem. Można to zrobić, z następujących raportów:

- **Raport dotyczący ryzykownych logowań —** w raport dotyczący ryzykownych logowań, można potwierdzić, że operacje logowania są bezpieczne lub ze złamanymi zabezpieczeniami

- **Raport ryzykownych użytkowników —** w raporcie ryzykownych użytkowników należy odrzucić ryzyka związanego z użytkownikiem 

Twoja opinia jest przetwarzany przez usługę Azure AD, aby poprawić dokładność wyników wykrywania. Zazwyczaj można przekazać opinię, jako część ryzyka związanego z użytkownikiem lub badania ryzyka logowania. Aby uzyskać więcej informacji, zobacz [sposobu badania ryzykownych użytkowników oraz operacje logowania](howto-investigate-risky-users-signins.md).


## <a name="confirm-compromised"></a>Potwierdź naruszenie zabezpieczeń

Potwierdzanie zdarzeń logowania, ponieważ naruszenia zabezpieczeń sygnały do usługi Azure AD, logowania nie zostało autoryzowane przez właściciela tożsamości. Po wybraniu przycisku "Potwierdź złamane" usługi Azure AD będzie

- Zwiększenie ryzyka użytkownika, którego dotyczy wysokiego związanego z użytkownikiem.

- Przyczynić się do zoptymalizowania machine learning, który wykrywa zdarzenia o podwyższonym ryzyku
 
- Wykonaj dodatkowe środki, aby jeszcze lepiej chronić Twojej organizacji



Aby potwierdzić, którego bezpieczeństwo zostało naruszone logowania:

- **Raport dotyczący ryzykownych logowań** — ta opcja umożliwia upewnij się, których bezpieczeństwo zostało naruszone logowania dla co najmniej jeden zdarzenia logowania.

    ![Odrzuć ryzyko związane z użytkownikiem](./media/howto-improve-detection-accuracy/07.png)

- **Raport dotyczący ryzykownych logowań w widoku szczegółów** — ta opcja umożliwia potwierdzenie zagrożone konto dla wybranego zdarzenia logowania w raport dotyczący ryzykownych logowań. 

    ![Odrzuć ryzyko związane z użytkownikiem](./media/howto-improve-detection-accuracy/04.png)


 
## <a name="confirm-safe"></a>Potwierdź bezpieczeństwo


Potwierdzanie zdarzeń logowania jako bezpieczne sygnałów do usługi Azure AD, logowania **został** autoryzowane przez właściciela odpowiedniej tożsamości. Po wybraniu przycisku "Potwierdź bezpieczne" usługi Azure AD wykonują następujące czynności:

- Przywróć udział ryzyka użytkownika wybrane sesje logowania

- Zamknij zdarzeń związanych z ryzykiem

- Przyczynić się do zoptymalizowania machine learning, który wykrywa zdarzenia o podwyższonym ryzyku

- Wykonaj dodatkowe środki, aby jeszcze lepiej chronić Twojej organizacji
 

Aby sprawdzić, bezpiecznego logowania w:

- **Raport dotyczący ryzykownych logowań** — ta opcja umożliwia potwierdzenie bezpiecznego logowania dla co najmniej jeden zdarzenia logowania.

    ![Odrzuć ryzyko związane z użytkownikiem](./media/howto-improve-detection-accuracy/08.png)

- **Raport dotyczący ryzykownych logowań w widoku szczegółów** — ta opcja umożliwia potwierdzenie bezpiecznego logowania dla wybranych zdarzeń logowania w raport dotyczący ryzykownych logowań. 

    ![Odrzuć ryzyko związane z użytkownikiem](./media/howto-improve-detection-accuracy/05.png)




## <a name="dismiss-user-risk"></a>Odrzuć ryzyko związane z użytkownikiem

Jeśli już podjęte działania korygujące ryzyka użytkownika lub uważają, że błędnie zostały oznaczone jako ryzykowne, można zignorować ryzyka związanego z użytkownikiem. Odrzucanie ryzyka związanego z użytkownikiem przywraca użytkownika — ryzykowne stan. Wszystkie wcześniejsze ryzykowne logowania i ryzyka zdarzenia dla wybranego użytkownika zostanie zamknięty.


Można zignorować ryzyka użytkownika zgłoszonego:

- **Raport ryzykownych użytkowników** — ta opcja umożliwia odrzucanie ryzyka związanego z użytkownikiem dla jednego lub więcej wybranych użytkowników.

    ![Odrzuć ryzyko związane z użytkownikiem](./media/howto-improve-detection-accuracy/02.png)

- **Widok szczegółów** — ta opcja umożliwia odrzucić ryzyka użytkownika dla wybranego użytkownika w raporcie ryzyka użytkownika. 

    ![Odrzuć ryzyko związane z użytkownikiem](./media/howto-improve-detection-accuracy/01.png)


**Co należy wiedzieć:**

- Nie można cofnąć tej akcji.

- Może upłynąć kilka minut, zanim ta akcja zakończona, dlatego nie należy ponownie przesłać żądanie.

- Tę akcję można wykonać tylko, jeśli usługi AD zarządza poświadczeń użytkownika. 



## <a name="best-practices"></a>Najlepsze praktyki

Odrzucanie ryzyka związanego z użytkownikiem jest jednym ze sposobów, aby odblokować je, jeśli zostały zablokowane przez użytkownika zasady o podwyższonym ryzyku i nie może samodzielnie korygowanie z powodu niemający resetowania hasła i/lub włączone uwierzytelnianie wieloskładnikowe. W takiej sytuacji najlepiej jest zapewnienie użytkownika, a następnie rejestruje resetowania haseł i uwierzytelniania Wieloskładnikowego, tak, aby mogli wykonać samodzielną korektę wszystkie zdarzenia o podwyższonym ryzyku w przyszłości.


## <a name="next-steps"></a>Kolejne kroki

Aby uzyskać omówienie programu Azure AD Identity Protection, zobacz [Omówienie usługi Azure AD Identity Protection](overview-v2.md).


