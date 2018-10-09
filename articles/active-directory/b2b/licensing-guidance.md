---
title: Wskazówki dotyczące licencjonowania współpracy w usłudze Azure Active Directory B2B | Dokumentacja firmy Microsoft
description: Usługa Azure Active Directory B2B, które nie wymagają współpracy płatne licencje usługi Azure AD, ale użytkownik może również uzyskać płatnych funkcji dla użytkowników-gości B2B
services: active-directory
ms.service: active-directory
ms.component: B2B
ms.topic: conceptual
ms.date: 10/04/2018
ms.author: mimart
author: msmimart
manager: mtillman
ms.reviewer: mal
ms.openlocfilehash: d80794511f334cd6dc5af418e24fc774b7d8728f
ms.sourcegitcommit: 0bb8db9fe3369ee90f4a5973a69c26bff43eae00
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/08/2018
ms.locfileid: "48867514"
---
# <a name="azure-active-directory-b2b-collaboration-licensing-guidance"></a>Wskazówki dotyczące licencjonowania funkcji współpracy między firmami przy użyciu usługi Azure Active Directory

Przy użyciu funkcji współpracy business-to-business (B2B) usługi Azure Active Directory (Azure AD), możesz zaprosić użytkowników zewnętrznych (lub "użytkowników-gości") do użycia z płatnych usług Azure AD. Dla każdego płatnej licencji usługi Azure AD, który przypiszesz do użytkownika, możesz zaprosić do pięciu użytkowników-gości w ramach dodatku użytkownika zewnętrznego.

Użytkownik-Gość to osoba, która nie jest członkiem organizacji lub dowolny z podmiotów stowarzyszonych w Twojej organizacji. Użytkowników-gości są definiowane przez ich związek z Twojej organizacji, nie przez poświadczenia, których używają do logowania. W rzeczywistości użytkownika-gościa może zarejestrować się za pomocą zewnętrznego tożsamości lub za pomocą poświadczeń należących do organizacji.

Poniżej przedstawiono *nie* użytkowników (gości):
- Twoi pracownicy, wykonawcy u klienta lub agentów u klienta
- Pracownicy, wykonawcy u klienta ani lokalnymi przedstawicielami Twojej filii

Licencjonowanie na użytkownika B2B gościa jest automatycznie obliczana i zgłoszone na podstawie współczynnika 1:5. Obecnie nie jest możliwe do przypisania B2B licencji użytkownika gościa bezpośrednio do użytkowników-gości.

Istnieje kilka sytuacji, gdy zgłaszane nie jest użytkownikiem-gościem przy użyciu dodatku użytkownika zewnętrznego 1:5. Jeśli użytkownik-Gość ma już jest licencja usługi Azure AD w organizacji przez użytkownika, użytkownik nie używać jednej z Twoich licencji użytkowników gości B2B. Ponadto gościa, które użytkownicy mogą korzystać z bezpłatnych funkcji usługi Azure AD z żadnymi dodatkowymi wymaganiami licencjonowania. Użytkownicy-goście mają dostęp do bezpłatnych funkcji usługi Azure AD, nawet jeśli nie masz żadnych płatne licencje usługi Azure AD. 

## <a name="examples-calculating-guest-user-licenses"></a>Przykłady: Obliczanie licencji użytkownika gościa
Po określeniu, ilu użytkowników-gości, uzyskać dostęp z płatnych usług Azure AD, upewnij się, masz wystarczająco usługi Azure AD płatne licencje użytkowników-gości współczynnik wymagane 1:5. Oto kilka przykładów:

- Aby zaprosić 100 użytkowników-gości do aplikacji usługi Azure AD lub usługi, a chcesz przypisać zarządzania dostępem i Inicjowanie obsługi wszystkich użytkowników-gości. Chcesz także wymagać uwierzytelniania Wieloskładnikowego i dostępu warunkowego dla 50 tych użytkowników-gości. Aby uwzględnić to połączenie, musisz 10 licencji usługi Azure AD podstawowa i 10 licencji usługi Azure AD Premium P1. Jeśli planowane jest korzystanie z funkcji ochrony tożsamości użytkowników z gościa, potrzebujesz licencji usługi Azure AD Premium P2 w tej samej proporcji 1:5 użytkowników-gości.
- Chcesz zaprosić 60 użytkowników-gości, którzy wymagają usługi MFA, więc musisz mieć co najmniej 12 licencji usługi Azure AD Premium P1. Masz 10 pracowników za pomocą licencji usługi Azure AD Premium P1, które umożliwiałyby maksymalnie 50 użytkowników-gości w ramach licencjonowania współczynnik 1:5. Musisz kupić dwie dodatkowe licencje Premium P1 10 użytkowników-gości dodatkowe.

## <a name="using-the-b2b-collaboration-api-to-invite-users-from-your-affiliates"></a>Za pomocą współpracy B2B interfejsu API można zaprosić użytkowników z Twojej oddziałów

Zgodnie z definicją, użytkownik-Gość B2B jest użytkownikiem zewnętrznym, zaprosić do użycia z płatnych usług i aplikacji usługi Azure AD. Pracownika, wykonawcy u klienta lub agenta u klienta w firmie lub jednym z podmiotów stowarzyszonych nie kwalifikuje się do współpracy B2B, nawet jeśli są używane funkcje B2B. Oto kilka przykładów: 
- Chcesz użyć poświadczeń zewnętrzne (na przykład tożsamości społecznościowych) można zaprosić użytkownika, który jest pracownika z organizacji. W tym scenariuszu nie jest zgodne z wymaganiami dotyczącymi licencjonowania i nie jest dozwolone. Poświadczenia zewnętrznych nie wprowadzaj pracownika użytkownika zewnętrznego.  
- Chcesz zaprosić użytkownika z jednego z podmiotów stowarzyszonych w organizacji za pomocą interfejsów API B2B. Chociaż w tym scenariuszu korzysta z interfejsów API B2B można zaprosić użytkownika, nie jest traktowane jako współpracy B2B. Go nie spełnia wymagań dotyczących licencjonowania ponieważ użytkownika z Twojego partnera nie jest użytkownikiem zewnętrznym. 

W obu tych scenariuszy, lepszym rozwiązaniem jest używanie interfejsu API B2B zaprosić użytkowników jako elementy członkowskie (invitedUserType = — członek) i przypisz je każdej licencji usługi Azure AD. 

## <a name="next-steps"></a>Kolejne kroki

Na współpracy B2B usługi Azure AD, zobacz następujące zasoby:

* [Czym jest współpraca B2B w usłudze Azure AD?](what-is-b2b.md)
* [Często zadawane pytania (FAQ) współpracy w usłudze Azure Active Directory B2B](faq.md)
