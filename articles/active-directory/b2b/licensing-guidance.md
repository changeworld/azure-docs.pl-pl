---
title: Współpraca B2B licencjonowania wskazówki — usługi Azure Active Directory | Dokumentacja firmy Microsoft
description: Usługa Azure Active Directory B2B, które nie wymagają współpracy płatne licencje usługi Azure AD, ale użytkownik może również uzyskać płatnych funkcji dla użytkowników-gości B2B
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 10/04/2018
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2df98966b69250d31f2a741411aea6e5554f9ed6
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "67112862"
---
# <a name="azure-active-directory-b2b-collaboration-licensing-guidance"></a>Wskazówki dotyczące licencjonowania funkcji współpracy między firmami przy użyciu usługi Azure Active Directory

Przy użyciu funkcji współpracy business-to-business (B2B) usługi Azure Active Directory (Azure AD), możesz zaprosić użytkowników zewnętrznych (lub "użytkowników-gości") do użycia z płatnych usług Azure AD. Dla każdego płatnej licencji usługi Azure AD, który przypiszesz do użytkownika, możesz zaprosić do pięciu użytkowników-gości w ramach dodatku użytkownika zewnętrznego.

Licencjonowanie na użytkownika B2B gościa jest automatycznie obliczana i zgłoszone na podstawie współczynnika 1:5. Obecnie nie jest możliwe do przypisania B2B licencji użytkownika gościa bezpośrednio do użytkowników-gości.

Ponadto gościa, które użytkownicy mogą korzystać z bezpłatnych funkcji usługi Azure AD z żadnymi dodatkowymi wymaganiami licencjonowania. Użytkownicy-goście mają dostęp do bezpłatnych funkcji usługi Azure AD, nawet jeśli nie masz żadnych płatne licencje usługi Azure AD. 

## <a name="examples-calculating-guest-user-licenses"></a>Przykłady: Obliczanie licencji użytkownika gościa
Po określeniu, ilu użytkowników-gości, uzyskać dostęp z płatnych usług Azure AD, upewnij się, masz wystarczająco usługi Azure AD płatne licencje użytkowników-gości współczynnik wymagane 1:5. Oto kilka przykładów:

- Aby zaprosić 100 użytkowników-gości do aplikacji usługi Azure AD lub usługi, a chcesz przypisać zarządzania dostępem i Inicjowanie obsługi wszystkich użytkowników-gości. Chcesz także wymagać uwierzytelniania Wieloskładnikowego i dostępu warunkowego dla 50 tych użytkowników-gości. Aby uwzględnić to połączenie, musisz 10 licencji usługi Azure AD podstawowa i 10 licencji usługi Azure AD Premium P1. Jeśli planowane jest korzystanie z funkcji ochrony tożsamości użytkowników z gościa, potrzebujesz licencji usługi Azure AD Premium P2 w tej samej proporcji 1:5 użytkowników-gości.
- Chcesz zaprosić 60 użytkowników-gości, którzy wymagają usługi MFA, więc musisz mieć co najmniej 12 licencji usługi Azure AD Premium P1. Masz 10 pracowników za pomocą licencji usługi Azure AD Premium P1, które umożliwiałyby maksymalnie 50 użytkowników-gości w ramach licencjonowania współczynnik 1:5. Musisz kupić dwie dodatkowe licencje Premium P1 10 użytkowników-gości dodatkowe.

## <a name="next-steps"></a>Kolejne kroki

Na współpracy B2B usługi Azure AD, zobacz następujące zasoby:

* [Cennik usługi Azure Active Directory](https://azure.microsoft.com/pricing/details/active-directory/)
* [Czym jest współpraca B2B w usłudze Azure AD?](what-is-b2b.md)
* [Współpraca B2B w usłudze Active Directory Azure — często zadawane pytania](faq.md)
