---
title: Właściwości użytkownika współpracy usługi Azure Active Directory B2B | Dokumentacja firmy Microsoft
description: Właściwości użytkownika współpraca w usłudze Azure Active Directory B2B są konfigurowane
services: active-directory
ms.service: active-directory
ms.component: B2B
ms.topic: article
ms.date: 05/25/2017
ms.author: twooley
author: twooley
manager: mtillman
ms.reviewer: sasubram
ms.openlocfilehash: 401c001f897a926de1b7d68403b6945164f3333b
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/10/2018
---
# <a name="properties-of-an-azure-active-directory-b2b-collaboration-user"></a>Właściwości użytkownika współpracy usługi Azure Active Directory B2B

Azure Active Directory (Azure AD) biznesowych między firmami (B2B) współpracy użytkownik jest użytkownikiem z UserType = gościa. Zazwyczaj tego użytkownika gościa jest od organizacji będącej partnerem i ma ograniczone uprawnienia w katalogu zaproszenia domyślnie.

W zależności od potrzeb organizacji zaproszenia użytkownik współpracy B2B usługi Azure AD można w jednym z następujących stanów konta:

- Stan 1: Adresem IP w zewnętrznych wystąpienia usługi Azure AD i reprezentowana jako użytkownik-Gość w organizacji zaproszenia. W takim przypadku B2B użytkownik loguje się przy użyciu konta należącego do zaproszonych dzierżawy usługi Azure AD. Jeśli w organizacji partnerskiej nie używa usługi Azure AD, tworzona jest nadal Gość w usłudze Azure AD. Wymagania są one zrealizować ich zaproszenia, a usługi Azure AD, sprawdza swój adres e-mail. To rozmieszczenie jest również nazywany just in time (JIT) dzierżawy lub "wirusa" dzierżawy.

- Stan 2: Adresem IP w ramach konta Microsoft i reprezentowana jako użytkownik-Gość w organizacji hosta. W takim przypadku użytkownika gościa zaloguje się za pomocą konta Microsoft. Tożsamość społecznościowych zaproszonych użytkowników (google.com lub podobny), który nie jest kontem Microsoft, zostanie utworzona jako konto Microsoft podczas realizacji oferty.

- Stan 3: Adresem IP w usłudze Active Directory organizacji hosta lokalnego i zsynchronizowane z organizacji hosta usługi Azure AD. W tej wersji należy ręcznie zmienić UserType takich użytkowników w chmurze za pomocą programu PowerShell.

- Stan 4: Adresem IP w organizacji hosta usługi Azure AD z UserType = poświadczeń zarządzanych przez organizację hosta i gościa.

  ![Wyświetlanie inicjały zapraszającej](media/active-directory-b2b-user-properties/redemption-diagram.png)


Teraz zobaczmy, jak wygląda użytkownika współpracy B2B usługi Azure AD w stanie 1 w usłudze Azure AD.

### <a name="before-invitation-redemption"></a>Przed realizacji zaproszenia

![Przed realizacji oferty](media/active-directory-b2b-user-properties/before-redemption.png)

### <a name="after-invitation-redemption"></a>Po realizacji zaproszenia

![Po realizacji oferty](media/active-directory-b2b-user-properties/after-redemption.png)

## <a name="key-properties-of-the-azure-ad-b2b-collaboration-user"></a>Właściwości klucza użytkownika współpracy B2B usługi Azure AD
### <a name="usertype"></a>UserType
Ta właściwość wskazuje relację użytkownika do dzierżawy hosta. Ta właściwość może mieć dwie wartości:
- Element członkowski: Ta wartość wskazuje pracownika hosta organizacji i użytkowników w organizacji płacowego. Na przykład ten użytkownik zamierza mają dostęp do witryn tylko wewnętrzny. Ten użytkownik mogą nie być uważane za zewnętrzne współpracownika.

- Gość: Ta wartość wskazuje użytkownika, który nie jest uznawane za wewnętrznego w firmie, takie jak współpracownika zewnętrznych, partnerów, klienta lub podobne użytkownika. Taki użytkownik nie powinien otrzymywać memo wewnętrzny Dyrektora Generalnego lub uzyskać korzyści firmy, na przykład.

  > [!NOTE]
  > UserType nie ma związku jak użytkownik się zaloguje, roli katalogu użytkowników i tak dalej. Po prostu ta właściwość wskazuje relacji użytkownika do organizacji hosta i umożliwia organizacji, aby wymusić zasady, które są zależne od tej właściwości.

### <a name="source"></a>Element źródłowy
Ta właściwość wskazuje sposób logowania się użytkownika.

- Zaproszonych użytkowników: Tego użytkownika zaproszono, ale nie ma jeszcze zrealizowane zaproszenia.

- Zewnętrzne usługi Active Directory: Tego użytkownika jest adresem IP w organizacji zewnętrznych i przeprowadza uwierzytelnianie za pomocą konta usługi Azure AD, które należy do innej organizacji. Ten typ logowania odpowiada stanu 1.

- Konto Microsoft: tego użytkownika jest adresem IP w ramach konta Microsoft i przeprowadza uwierzytelnianie za pomocą konta Microsoft. Ten typ logowania odpowiada stan 2.

- Windows Server Active Directory: Ten użytkownik jest zalogowany z usługi Active Directory lokalnego — należącego do tej organizacji. Ten typ logowania odnosi się do stanu 3.

- Azure Active Directory: Ten użytkownik jest uwierzytelniany przy użyciu konta usługi Azure AD, który należy do tej organizacji. Ten typ logowania odpowiada 4 stanu.
  > [!NOTE]
  > Źródło i UserType są niezależne właściwości. Wartość źródła dla UserType nie oznacza określonej wartości.

## <a name="can-azure-ad-b2b-users-be-added-as-members-instead-of-guests"></a>Użytkownicy B2B usługi Azure AD można dodać jako członków zamiast gości?
Zazwyczaj użytkownik B2B usługi Azure AD i użytkownika gościa oznaczają to samo. W związku z tym użytkownikiem współpracy B2B usługi Azure AD nie zostanie dodany jako użytkownik z UserType = domyślnie gościa. Jednak w niektórych przypadkach organizacji partnera jest członkiem większej organizacji, do której należy również organizacji hosta. Jeśli tak, organizacja hosta warto traktować użytkowników w organizacji partnerskiej jako elementy członkowskie zamiast gości. Użyj interfejsów API usługi Azure AD B2B zaproszenia Manager, aby dodać lub zaprosić użytkownikowi z organizacji partnerskiej organizacji hosta jako członek.

## <a name="filter-for-guest-users-in-the-directory"></a>Filtr dla gości w katalogu

![Filtr gości](media/active-directory-b2b-user-properties/filter-guest-users.png)

## <a name="convert-usertype"></a>Konwertuj UserType
Obecnie jest możliwe w dla użytkowników przekonwertować UserType z elementu członkowskiego gościa i na odwrót przy użyciu programu PowerShell. Jednak Właściwość UserType powinna się reprezentować relację użytkownika w organizacji. W związku z tym wartość tej właściwości należy zmieniać tylko wtedy, gdy zmienia się relacji użytkowników w organizacji. Zmiana relacji użytkownika, należy kierować się problemów, takich jak czy główna nazwa użytkownika (UPN), należy zmienić? Należy użytkownik nadal mieć dostęp do tych samych zasobów? Należy przypisać skrzynki pocztowej? W związku z tym nie zaleca się zmiany UserType przy użyciu programu PowerShell jako atomic działania. Ponadto w przypadku, gdy ta właściwość staje się modyfikować za pomocą programu PowerShell, nie zaleca się przyjmowanie zależności od tej wartości.

## <a name="remove-guest-user-limitations"></a>Usuń ograniczenia użytkownika gościa
Może to być przypadki, w którym chcesz zapewnić użytkownikom gościa wyższymi uprawnieniami. Można dodać użytkownika gościa do dowolnej roli i nawet usuwać domyślne ograniczenia użytkownika gościa w katalogu, aby dać użytkownikowi te same uprawnienia co elementów członkowskich.

Istnieje możliwość wyłączyć domyślne ograniczenia użytkownika gościa, dzięki czemu użytkownik-Gość w katalogu firmy znajduje się takie same uprawnienia jak użytkownika elementu członkowskiego.

![Usuń ograniczenia użytkownika gościa](media/active-directory-b2b-user-properties/remove-guest-limitations.png)

## <a name="next-steps"></a>Kolejne kroki

* [Czym jest współpraca B2B w usłudze Azure AD?](active-directory-b2b-what-is-azure-ad-b2b.md)
* [Tokeny użytkownika współpracy B2B](active-directory-b2b-user-token.md)
* [Oświadczenia użytkowników współpracy B2B mapowania](active-directory-b2b-claims-mapping.md)
