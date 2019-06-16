---
title: Dodawanie gości B2B bez zaproszenia łącze lub wiadomość e-mail — usługi Azure Active Directory | Dokumentacja firmy Microsoft
description: Można pozwolić, aby użytkownik-Gość innych użytkowników-gości można dodawać do usługi Azure AD, bez realizowanie zaproszenia we współpracy B2B usługi Azure Active Directory.
services: active-directory
documentationcenter: ''
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 06/12/2019
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: elisol
ms.collection: M365-identity-device-management
ms.openlocfilehash: 81aad3ef9a4a53532d19fdb81bc48fc50931d49c
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "67056062"
---
# <a name="add-b2b-collaboration-guest-users-without-an-invitation-link-or-email"></a>Dodawanie użytkowników-gości współpracy B2B bez zaproszenia łącze lub wiadomości e-mail

Aby zaprosić użytkowników-gości, można teraz wysłać bezpośredni link do udostępnionej aplikacji. Przy użyciu tej metody użytkownicy-goście nie muszą już używać wiadomości e-mail z zaproszeniem, z wyjątkiem w niektórych przypadkach specjalne. Użytkownik-Gość klika link do aplikacji, przeglądy akceptuje postanowienia dotyczące prywatności i następnie bezproblemowo uzyskuje dostęp do aplikacji. Aby uzyskać więcej informacji, zobacz [realizacja zaproszenia współpracy B2B](redemption-experience.md).   

Zanim ta nowa metoda był dostępny, można zaprosić użytkowników-gości bez konieczności wiadomości e-mail z zaproszeniem, dodając zapraszającej (ze swojej organizacji lub z organizacji partnerskiej) do **osoba zapraszająca gości** rolę katalogu, a następnie posiadanie zapraszającej dodać użytkowników-gości do katalogu, grupy lub aplikacji za pośrednictwem interfejsu użytkownika lub za pomocą programu PowerShell. (Jeśli przy użyciu programu PowerShell, można pominąć wiadomości e-mail z zaproszeniem całkowicie). Na przykład:

1. Użytkownik w organizacji hosta (na przykład WoodGrove) zaprasza jednego użytkownika z organizacji partnerskiej (na przykład Sam@litware.com) jako Gość.
2. Administratorem w organizacji hosta [konfiguruje zasady](delegate-invitations.md) umożliwiające Sam zidentyfikować i Dodaj innych użytkowników z organizacji partnerskiej (Litware). (Sam musi zostać dodany do **osoba zapraszająca gości** roli.)
3. Teraz Sam można dodawać innych użytkowników z Litware do katalogu, grupy lub aplikacje WoodGrove bez zaproszenia można zrealizować. Jeśli Sam powoduje wyliczenie odpowiednie uprawnienia Litware, odbywa się automatycznie.
 
Ta metoda oryginalnego nadal działa. Istnieje jednak niewielkie różnice w zachowaniu. Jeśli używasz programu PowerShell, można zauważyć, że zaproszeni gościa konto ma teraz **PendingAcceptance** stanu, a nie od razu przedstawiający **zaakceptowano**. Mimo że jest w stanie oczekiwania, użytkownik-Gość można nadal Zaloguj się i uzyskać dostęp do aplikacji, nie klikając łącze w wiadomości e-mail z zaproszeniem. Stan oczekiwania oznacza, że użytkownik nie jeszcze zrealizowano [zgody środowisko](redemption-experience.md#consent-experience-for-the-guest), gdzie akceptują warunki ochrony prywatności organizacji zapraszającej. Użytkownik-Gość widzi ten ekran wyrażania zgody, gdy logują się oni po raz pierwszy. 

Jeśli zaproszenie użytkownika do katalogu użytkownik-Gość musi uzyskać dostęp do portalu Azure specyficznym dla dzierżawy zasobów bezpośrednio adres URL (takie jak https://portal.azure.com/ *resourcetenant*. onmicrosoft.com) aby wyświetlić i zaakceptować warunki ochrony prywatności.

## <a name="next-steps"></a>Kolejne kroki

- [Czym jest współpraca B2B w usłudze Azure AD?](what-is-b2b.md)
- [Realizacja zaproszenia współpracy B2B](redemption-experience.md)
- [Delegate invitations for Azure Active Directory B2B collaboration (Delegowanie zaproszeń na potrzeby współpracy B2B w usłudze Azure Active Directory)](delegate-invitations.md)
- [Jak pracownicy przetwarzający informacje mogą dodać użytkowników we współpracy B2B?](add-users-information-worker.md)

