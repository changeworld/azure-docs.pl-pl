---
title: Co to jest współpraca z usługi Azure Active Directory B2B? | Microsoft Docs
description: Współpraca z usługą Azure Active Directory B2B obsługuje relacji między firmami, umożliwiając partnerom biznesowym selektywne uzyskiwanie dostępu do aplikacji firmowych.
services: active-directory
ms.service: active-directory
ms.component: B2B
ms.topic: article
ms.date: 04/26/2018
ms.author: twooley
author: twooley
manager: mtillman
ms.reviewer: sasubram
ms.openlocfilehash: 2bc405e6356113e0423f833868c86890c0c3d5d2
ms.sourcegitcommit: 96089449d17548263691d40e4f1e8f9557561197
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/17/2018
---
# <a name="what-is-azure-ad-b2b-collaboration"></a>Czym jest współpraca B2B w usłudze Azure AD?

Azure Active Directory (Azure AD) business-to-business (B2B) możliwości współpracy Włącz każdej przy użyciu usługi Azure AD do pracy bezpiecznie z użytkowników z dowolnej innej organizacji, małych i dużych organizacji. Te organizacje mogą być lub bez usługi Azure AD, a nawet nie muszą mieć dział INFORMATYCZNY.

Organizacje przy użyciu usługi Azure AD umożliwia dostęp do dokumentów, zasobów i aplikacji do ich partnerów, zachowując pełną kontrolę nad ich danymi firmowymi. Deweloperzy mogą używać usługi Azure AD business-to-business interfejsy API do programowania aplikacji, które sobą dwie organizacje bardziej bezpieczne. Ponadto jest łatwy do nawigowania.

Poniższe wideo zawiera omówienie przydatne.
>[!VIDEO https://www.youtube.com/embed/AhwrweCBdsc]

## <a name="key-benefits-of-azure-ad-b2b-collaboration"></a>Najważniejsze zalety współpracy B2B usługi Azure AD

### <a name="work-with-any-user-from-any-partner"></a>Praca z żadnym użytkownikiem od dowolnego partnera

- Partnerzy użyć własnych poświadczeń
- Nie jest wymagany dla partnerów przy użyciu usługi Azure AD
- Nie zewnętrznych katalogów lub skomplikowane wymagane

### <a name="simple-and-secure-collaboration"></a>Proste i bezpiecznej współpracy

- Dostęp do dowolnej aplikacji firmowych lub danych, podczas stosowania zaawansowane, Azure zasad zasilania AD autoryzacji
- Użytkownicy mogą łatwo
- Zabezpieczenia korporacyjnej dla aplikacji i danych

### <a name="no-management-overhead"></a>Nie nakład pracy

- Nie zewnętrznych zarządzania konto lub hasło
- Nie synchronizacji lub ręcznego zarządzania cyklem życia konta
- Nie zewnętrznych koszty administracyjne

## <a name="easily-add-b2b-collaboration-users"></a>Łatwe dodawanie użytkowników współpracy B2B

Jako administrator, można łatwo dodać użytkowników (Gość) współpracy B2B do Twojej organizacji w [portalu Azure](https://portal.azure.com).

![Dodaj gości](media/what-is-b2b/adding-b2b-users-admin.png)

### <a name="enable-your-collaborators-to-bring-their-own-identity"></a>Włącz Twojej współpracowników do ich własnych tożsamości

B2B współpracownicy mogą zalogować się przy użyciu tożsamości siebie. Jeśli użytkownik nie ma konta Microsoft lub konta usługi Azure AD — utworzenia dla nich bezproblemowo w czasie na realizację oferty.

### <a name="delegate-to-application-and-group-owners"></a>Deleguj do aplikacji i właścicieli grupy

Jako aplikacja lub właściciel grupy można dodać użytkowników B2B bezpośrednio do dowolnej aplikacji, która Cię interesuje, czy nie jest on aplikacji firmy Microsoft. Administratorzy mają możliwość delegowania uprawnień do dodawania użytkowników B2B użytkowników niebędących administratorami. Użytkownicy inni niż administratorzy mogą używać [Panel dostępu do aplikacji w usłudze Azure AD](https://myapps.microsoft.com) do dodawania użytkowników współpracy B2B do aplikacji lub grupy.

![panel dostępu](media/what-is-b2b/access-panel.png)

![Dodawanie elementu członkowskiego](media/what-is-b2b/add-member.png)

### <a name="authorization-policies-protect-your-corporate-content"></a>Zasady autoryzacji ochrony zawartości w sieci firmowej

Zasady dostępu warunkowego, takich jak uwierzytelnianie wieloskładnikowe, można wymusić:
- Na poziomie dzierżawy
- Na poziomie aplikacji
- Dla określonych użytkowników dotyczące ochrony danych i aplikacji firmowych

### <a name="use-apis-and-sample-code-to-easily-build-applications-to-onboard"></a>Przy użyciu interfejsów API i przykładowy kod umożliwia łatwe tworzenie aplikacji do dołączenia

Przełącz z partnerami zewnętrznymi na statku w sposób dostosowany do potrzeb organizacji.

Użyj [zaproszenia współpracy B2B interfejsów API](https://developer.microsoft.com/graph/docs/api-reference/v1.0/resources/invitation) do dostosowania z dołączania napotyka, w tym tworzenie portale Samoobsługowe dla rejestracji. Firma Microsoft udostępnia kodu do portal samoobsługowy [w serwisie Github](https://github.com/Azure/active-directory-dotnet-graphapi-b2bportal-web).

![portal rejestracji](media/what-is-b2b/sign-up-portal.png)

Współpracy B2B usługi Azure AD możesz uzyskać pełną moc usługi Azure AD, aby chronić Twoje relacje z partnerami w taki sposób, aby znaleźć użytkownikom końcowym proste i intuicyjne.

## <a name="next-steps"></a>Kolejne kroki

- [Jak Administratorzy usługi Azure Active Directory dodać użytkowników współpracy B2B?](add-users-administrator.md)
- [Jak pracownicy przetwarzający informacje dodać użytkowników współpracy B2B?](add-users-information-worker.md)
- [Realizacji zaproszenia współpracy B2B](redemption-experience.md)
- [Azure licencjonowania współpracy B2B usługi AD](licensing-guidance.md)
- I jak zawsze Uzyskuj dostęp do zespołu produktu, opinii, dyskusji i sugestie za pośrednictwem naszego [społeczność techniczna Microsoft](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-B2B/bd-p/AzureAD_B2b).