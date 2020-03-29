---
title: Wskazówki dotyczące licencjonowania współpracy B2B — usługa Azure Active Directory | Dokumenty firmy Microsoft
description: Współpraca usługi Azure Active Directory B2B nie wymaga płatnych licencji usługi Azure AD, ale można również uzyskać płatne funkcje dla użytkowników-gości B2B
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 08/29/2019
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.collection: M365-identity-device-management
ms.openlocfilehash: 28d02160163da4081ad8adbe233b27fee970a0df
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74868851"
---
# <a name="azure-active-directory-b2b-collaboration-licensing-guidance"></a>Wskazówki dotyczące licencjonowania współpracy usługi Azure Active Directory B2B

Dzięki współpracy między firmami usługi Azure Active Directory (Azure AD) (B2B) można zaprosić użytkowników zewnętrznych (lub "użytkowników-gości") do korzystania z płatnych usług azure ad. Niektóre funkcje są bezpłatne, ale w przypadku wszystkich płatnych funkcji usługi Azure AD można zaprosić maksymalnie pięciu użytkowników-gości do każdej posiadanej licencji usługi Azure AD dla pracownika lub użytkownika niebędącego gościem w dzierżawie.

> [!NOTE]
> Szczegółowe informacje na temat cen usługi Azure AD i funkcji współpracy B2B można znaleźć w [cennikach usługi Azure Active Directory.](https://azure.microsoft.com/pricing/details/active-directory/)

Licencjonowanie użytkownika gościa B2B jest automatycznie obliczane i raportowane na podstawie współczynnika 1:5. 

Ponadto użytkownicy-goście mogą korzystać z bezpłatnych funkcji usługi Azure AD bez dodatkowych wymagań dotyczących licencjonowania. Użytkownicy-goście mają dostęp do bezpłatnych funkcji usługi Azure AD, nawet jeśli nie masz żadnych płatnych licencji usługi Azure AD. 

## <a name="examples-calculating-guest-user-licenses"></a>Przykłady: Obliczanie licencji użytkowników-gości
Po określeniu, ilu użytkowników-gościów musi uzyskać dostęp do płatnych usług Azure AD, upewnij się, że masz wystarczającą liczbę płatnych licencji usługi Azure AD, aby objąć użytkowników-gości w wymaganym stosunku 1:5. Oto kilka przykładów:

- Chcesz zaprosić 100 użytkowników-gości do aplikacji lub usług usługi Azure AD i zapewnić zarządzanie dostępem i inicjowanie obsługi administracyjnej. Dla 50 tych użytkowników-gości, należy również wymagać usługi MFA i dostępu warunkowego, więc dla tych funkcji trzeba będzie 10 licencji Usługi Azure AD Premium P1. Jeśli planujesz używać funkcji ochrony tożsamości z użytkownikami-gośćmi, musisz licencje Usługi Azure AD Premium P2 w tym samym stosunku 1:5, aby objąć użytkowników-gości.
- Chcesz zaprosić 60 użytkowników-gości, którzy wszyscy wymagają usługi MFA, więc musisz mieć co najmniej 12 licencji usługi Azure AD Premium P1. Masz 10 pracowników z licencjami Usługi Azure AD Premium P1, co pozwoliłoby do 50 użytkowników-gości w ramach współczynnika licencjonowania 1:5. Musisz zakupić dwie dodatkowe licencje Premium P1, aby objąć 10 dodatkowych użytkowników-gości.

## <a name="next-steps"></a>Następne kroki

Zobacz następujące zasoby dotyczące współpracy usługi Azure AD B2B:

* [Cennik usługi Azure Active Directory](https://azure.microsoft.com/pricing/details/active-directory/)
* [Czym jest współpraca B2B w usłudze Azure AD?](what-is-b2b.md)
* [Współpraca B2B w usłudze Active Directory Azure — często zadawane pytania](faq.md)
