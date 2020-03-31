---
title: Dodawanie gości B2B bez łącza zaproszenia lub wiadomości e-mail — Azure AD
description: Użytkownik-gość może dodawać innych użytkowników-gości do usługi Azure AD bez realizowania zaproszenia we współpracy usługi Azure Active Directory B2B.
documentationcenter: ''
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 06/12/2019
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: elisol
ms.collection: M365-identity-device-management
ms.openlocfilehash: 42c9caaf581fab37e2e55cbe408db6d6d846622a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80050906"
---
# <a name="add-b2b-collaboration-guest-users-without-an-invitation-link-or-email"></a>Dodawanie użytkowników-gości współpracujących b2b bez łącza zaproszenia lub wiadomości e-mail

Możesz teraz zaprosić użytkowników-gości, wysyłając bezpośredni link do udostępnionej aplikacji. Dzięki tej metodzie użytkownicy-goście nie muszą już używać wiadomości e-mail z zaproszeniem, z wyjątkiem niektórych szczególnych przypadków. Użytkownik-gość klika łącze do aplikacji, przegląda i akceptuje warunki prywatności, a następnie bezproblemowo uzyskuje dostęp do aplikacji. Aby uzyskać więcej informacji, zobacz [Wykup zaproszenia do współpracy B2B](redemption-experience.md).   

Zanim ta nowa metoda była dostępna, można było zaprosić użytkowników-gości bez konieczności wysłania zaproszenia pocztą e-mail przez dodanie osoby zaproszonej (z organizacji lub organizacji partnerskiej) do roli katalogu **osoby zaproszonej gościa,** a następnie dodanie przez osobę zaproszoną użytkowników-gości do katalogu, grup lub aplikacji za pośrednictwem interfejsu użytkownika lub programu PowerShell. (Jeśli używasz programu PowerShell, można całkowicie pominąć e-mail z zaproszeniem). Przykład:

1. Użytkownik w organizacji hosta (na przykład WoodGrove) zaprasza jednego użytkownika z Sam@litware.comorganizacji partnerskiej (na przykład) jako gościa.
2. Administrator w organizacji hosta [konfiguruje zasady,](delegate-invitations.md) które umożliwiają Same'owi identyfikowanie i dodawanie innych użytkowników z organizacji partnerskiej (Litware). (Sam musi zostać dodany do roli **osoby zapraszania gościa).**
3. Teraz Sam może dodawać innych użytkowników z Litware do katalogu WoodGrove, grup lub aplikacji bez konieczności zapraszania do realizacji. Jeśli Sam ma odpowiednie uprawnienia wyliczenia w Litware, dzieje się to automatycznie.
 
Ta oryginalna metoda nadal działa. Istnieje jednak niewielka różnica w zachowaniu. Jeśli używasz programu PowerShell, zauważysz, że zaproszone konto gościa ma teraz stan **Oczekująceakceptowanie** zamiast natychmiast **wyświetlać komunikat Zaakceptowane.** Chociaż stan jest oczekujący, użytkownik-gość nadal może zalogować się i uzyskać dostęp do aplikacji bez klikania łącza zaproszenia e-mail. Stan oczekiwania oznacza, że użytkownik nie przeszedł jeszcze [doświadczenia zgody,](redemption-experience.md#consent-experience-for-the-guest)gdzie akceptuje warunki prywatności zapraszania organizacji. Użytkownik-gość widzi ten ekran zgody, gdy loguje się po raz pierwszy. 

Jeśli zaprosisz użytkownika do katalogu, użytkownik-gość musi uzyskać bezpośredni dostęp do adresu https://portal.azure.com/URL witryny azure portal specyficzne dla dzierżawy zasobów (na przykład *resourcetenant*.onmicrosoft.com), aby wyświetlić i zaakceptować warunki prywatności.

## <a name="next-steps"></a>Następne kroki

- [Czym jest współpraca B2B w usłudze Azure AD?](what-is-b2b.md)
- [Wykup zaproszenia do współpracy B2B](redemption-experience.md)
- [Delegate invitations for Azure Active Directory B2B collaboration (Delegowanie zaproszeń na potrzeby współpracy B2B w usłudze Azure Active Directory)](delegate-invitations.md)
- [W jaki sposób pracownicy informacji dodają użytkowników współpracy B2B?](add-users-information-worker.md)

