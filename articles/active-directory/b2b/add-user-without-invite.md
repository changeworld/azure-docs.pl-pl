---
title: Dodawanie użytkowników współpracy B2B do usługi Azure Active Directory bez zaproszenia | Dokumentacja firmy Microsoft
description: Możesz pozwolić, aby dodać inne gości do usługi Azure AD bez realizowanie zaproszenie w usłudze Azure Active Directory B2B współpracy użytkownika gościa.
services: active-directory
documentationcenter: ''
ms.service: active-directory
ms.component: B2B
ms.topic: article
ms.date: 05/21/2018
ms.author: twooley
author: twooley
manager: mtillman
ms.reviewer: sasubram
ms.openlocfilehash: 50c64386f1eab07c299112617283b1d8d7295295
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34591055"
---
# <a name="add-b2b-collaboration-guest-users-without-an-invitation"></a>Dodaj gości współpracy B2B bez zaproszenia

Teraz można zaprosić użytkowników gościa przy wysyłaniu bezpośredniego łącza do aplikacji udostępnionej. Ta metoda gościa użytkownicy nie muszą korzystać wiadomość e-mail z zaproszeniem z wyjątkiem w niektórych przypadkach specjalnych. Użytkownik-Gość kliknie link do aplikacji, recenzje akceptuje zasadami zachowania poufności informacji i następnie bezproblemowo uzyskuje dostęp do aplikacji. Aby uzyskać więcej informacji, zobacz [realizacji zaproszenia współpracy B2B](redemption-experience.md).   

Zanim ta nowa metoda była dostępna, można zaprosić użytkowników gościa, bez konieczności wiadomość e-mail z zaproszeniem przez dodanie zapraszającej (ze swojej organizacji lub od organizacji będącej partnerem) do **zapraszającej gościa** roli katalogu, a następnie o zapraszającej Dodaj gości do katalogu, grupy lub aplikacji za pośrednictwem interfejsu użytkownika lub za pomocą programu PowerShell. (Jeśli przy użyciu programu PowerShell, można pominąć wiadomość e-mail z zaproszeniem całkowicie). Na przykład:

1. Użytkownik w organizacji hosta (na przykład WoodGrove) zaprasza jeden użytkownik z organizacji partnerskiej (na przykład Sam@litware.com) jako Gość.
2. Administrator w organizacji hosta [konfiguruje zasady](delegate-invitations.md) umożliwiające Sam do identyfikowania i dodać inni użytkownicy z organizacji partnerskiej (Litware). (Sam musi zostać dodany do **zapraszającej gościa** roli.)
3. Teraz Sam można dodawać innych użytkowników z Litware do katalogu WoodGrove, grupy lub aplikacji bez konieczności zaproszenia do zostać zrealizowane. Jeśli Sam ma wyliczenie odpowiednie uprawnienia w Litware, odbywa się automatycznie.
 
Ta metoda oryginalnego nadal działa. Istnieje jednak niewielkie różnice w zachowaniu. Jeśli używasz programu PowerShell, można zauważyć konta gościa zaproszonych teraz ma **PendingAcceptance** stanu nie wyświetla natychmiast **zaakceptowane**. Mimo że oczekuje stan użytkownika gościa nadal zalogowanie się i uzyskiwać dostęp do aplikacji bez klikając łącze w wiadomości e-mail zaproszenia. Stan oczekiwania oznacza, że użytkownik nie został jeszcze przekazany przez [zgodę środowisko](redemption-experience.md#privacy-policy-agreement), gdzie akceptacji zasadami zachowania poufności informacji organizacji zaproszenia. Gość widzi na tym ekranie zgody po zalogowaniu się po raz pierwszy. 

Jeśli możesz zaprosić użytkowników do katalogu, Gość muszą uzyskać dostęp do portalu Azure specyficznego dla dzierżawy zasobów bezpośrednio adres URL (takich jak https://portal.azure.com/ *resourcetenant*. onmicrosoft.com) do wyświetlania i Akceptuję postanowienia dotyczące prywatności.

### <a name="next-steps"></a>Kolejne kroki

- [Czym jest współpraca B2B w usłudze Azure AD?](what-is-b2b.md)
- [Realizacji zaproszenia współpracy B2B](redemption-experience.md)
- [Delegowanie zaproszeń do skorzystania z usługi Azure Active Directory B2B współpracy](delegate-invitations.md)
- [Jak pracownicy przetwarzający informacje dodać użytkowników współpracy B2B?](add-users-information-worker.md)

