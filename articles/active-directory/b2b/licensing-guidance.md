---
title: Wskazówki dotyczące licencjonowania współpracy B2B — Azure Active Directory | Microsoft Docs
description: Azure Active Directory współpracy B2B nie wymagają płatnych licencji usługi Azure AD, ale możesz również otrzymywać płatne funkcje dla użytkowników-Gości B2B
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 08/15/2019
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.collection: M365-identity-device-management
ms.openlocfilehash: 081061eae07fa3765d032ad155e59ebf5aa3cbc9
ms.sourcegitcommit: 0e59368513a495af0a93a5b8855fd65ef1c44aac
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/15/2019
ms.locfileid: "69512560"
---
# <a name="azure-active-directory-b2b-collaboration-licensing-guidance"></a>Wskazówki dotyczące licencjonowania funkcji współpracy między firmami przy użyciu usługi Azure Active Directory

Dzięki funkcji współpracy między firmami (B2B, Business-to-Business) w Azure Active Directory (Azure AD) można zapraszać użytkowników zewnętrznych (lub "Gości") do korzystania z płatnych usług Azure AD. Niektóre funkcje są bezpłatne, ale w przypadku wszystkich płatnych funkcji usługi Azure AD można zaprosić do pięciu użytkowników-Gości dla każdej licencji na usługę Azure AD, która jest własnością pracownika lub użytkownika innego niż Gość w dzierżawie.

> [!NOTE]
> Zapoznaj się z [cennikiem Azure Active Directory](https://azure.microsoft.com/pricing/details/active-directory/) , aby uzyskać szczegółowe informacje na temat cen usługi Azure AD i funkcji współpracy B2B.

Licencjonowanie użytkowników gościa B2B jest automatycznie obliczane i raportowane na podstawie współczynnika 1:5. Obecnie nie jest możliwe przypisanie licencji użytkownika gościa B2B bezpośrednio do użytkowników-Gości.

Ponadto użytkownicy-Goście mogą korzystać z bezpłatnych funkcji usługi Azure AD bez dodatkowych wymagań dotyczących licencjonowania. Użytkownicy-Goście mają dostęp do bezpłatnych funkcji usługi Azure AD, nawet jeśli nie masz żadnych płatnych licencji usługi Azure AD. 

## <a name="examples-calculating-guest-user-licenses"></a>Przykłady: Obliczanie licencji użytkownika-gościa
Po ustaleniu, ilu użytkowników-Gości musi uzyskać dostęp do płatnych usług Azure AD, upewnij się, że masz wystarczającą liczbę płatnych licencji usługi Azure AD, aby uwzględnić użytkowników-Gości w wymaganym współczynniku 1:5. Oto kilka przykładów:

- Chcesz zaprosić użytkowników-Gości 100 do aplikacji lub usług usługi Azure AD, a także chcesz przypisać zarządzanie dostępem i Inicjowanie obsługi administracyjnej wszystkim użytkownikom-Gościom. Użytkownik chce również wymagać uwierzytelniania wieloskładnikowego i dostępu warunkowego dla 50 tych użytkowników-Gości. Aby obsłużyć tę kombinację, należy dysponować 10 Azure AD — wersja Podstawowa licencji i 10 Azure AD — wersja Premium licencji P1. Jeśli planujesz używanie funkcji ochrony tożsamości z użytkownikami gościa, musisz Azure AD — wersja Premium licencje P2 w tym samym współczynniku 1:5, aby obsłużyć użytkowników-Gości.
- Chcesz zaprosić 60 użytkowników-Gości, którzy wszystkie wymagają uwierzytelniania wieloskładnikowego, więc musisz mieć co najmniej 12 Azure AD — wersja Premiumych licencji P1. Masz 10 pracowników z licencjami na Azure AD — wersja Premium P1, co pozwoli maksymalnie 50 użytkownikom-Gościom w ramach wskaźnika licencjonowania 1:5. Musisz zakupić dwie dodatkowe licencje na P1, aby obkryć 10 dodatkowym użytkownikom-Gościom.

## <a name="next-steps"></a>Następne kroki

Zobacz następujące zasoby dotyczące współpracy B2B usługi Azure AD:

* [Cennik Azure Active Directory](https://azure.microsoft.com/pricing/details/active-directory/)
* [Czym jest współpraca B2B w usłudze Azure AD?](what-is-b2b.md)
* [Współpraca B2B w usłudze Active Directory Azure — często zadawane pytania](faq.md)
