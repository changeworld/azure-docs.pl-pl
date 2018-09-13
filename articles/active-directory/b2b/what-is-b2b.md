---
title: Czym jest współpraca B2B usługi Azure Active Directory? | Microsoft Docs
description: Współpraca z usługą Azure Active Directory B2B obsługuje relacje między firmami, umożliwiając partnerom biznesowym na selektywny dostęp do aplikacji firmowych.
services: active-directory
ms.service: active-directory
ms.component: B2B
ms.topic: article
ms.date: 04/26/2018
ms.author: mimart
author: msmimart
manager: mtillman
ms.reviewer: sasubram
ms.openlocfilehash: 51a969ae215583a0be8d75ff1de11173e0696a22
ms.sourcegitcommit: e8f443ac09eaa6ef1d56a60cd6ac7d351d9271b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/12/2018
ms.locfileid: "35649261"
---
# <a name="what-is-azure-ad-b2b-collaboration"></a>Czym jest współpraca B2B w usłudze Azure AD?

Usługa Azure Active Directory (Azure AD) business-to-business (B2B) udostępnia funkcje pracy zespołowej Włącz każdej organizacji za pomocą usługi Azure AD do pracy z użytkownikami z dowolnej innej organizacji, małych lub dużych bezpieczne. Te organizacje mogą być z lub bez usługi Azure AD i nie musi nawet firma ma dział IT.

Organizacje za pomocą usługi Azure AD umożliwia dostęp do dokumentów, zasobów i aplikacjom partnerów, zachowując pełną kontrolę nad ich danymi firmowymi. Deweloperzy mogą używać usługi Azure AD business-to-business interfejsów API do pisania aplikacji, które łączą dwie organizacje ze sobą w większej liczbie bezpiecznie. Ponadto jest łatwa dla użytkowników końcowych przejść.

Poniższy klip wideo zawiera omówienie przydatne.
>[!VIDEO https://www.youtube.com/embed/AhwrweCBdsc]

## <a name="key-benefits-of-azure-ad-b2b-collaboration"></a>Do najważniejszych korzyści zapewnianych współpracy B2B usługi Azure AD

### <a name="work-with-any-user-from-any-partner"></a>Praca z żadnym użytkownikiem, od dowolnego partnera

- Czy partnerzy korzystać z własnych poświadczeń
- Nie jest wymagany dla partnerów używać usługi Azure AD
- Żadne katalogi zewnętrzne ani skomplikowane, wymagane

### <a name="simple-and-secure-collaboration"></a>Proste i bezpieczne współpracy

- Zapewnianie dostępu do firmowych aplikacji ani danych, podczas stosowania zaawansowanych, Azure obsługiwane przez usługi AD zasady autoryzacji w
- Łatwe
- Zapewniania bezpieczeństwa danych przedsiębiorstwa dla aplikacji i danych

### <a name="no-management-overhead"></a>Narzut na zarządzanie

- Brak zewnętrznych zarządzania konta lub hasła
- Nie synchronizacji lub ręcznego zarządzania cyklem życia konta
- Nie zewnętrznych koszty administracyjne

## <a name="easily-add-b2b-collaboration-users"></a>Łatwe dodawanie użytkowników we współpracy B2B

Jako administrator, można łatwo dodać użytkowników (gości) we współpracy B2B do Twojej organizacji w [witryny Azure portal](https://portal.azure.com).

![dodać użytkowników-gości](media/what-is-b2b/adding-b2b-users-admin.png)

### <a name="enable-your-collaborators-to-bring-their-own-identity"></a>Włącz współpracownicy w taki sposób wyświetlić jego własnej tożsamości

B2B współpracownicy mogą zalogować się przy użyciu tożsamości wybranych przez nich. Jeśli użytkownik nie ma konta Microsoft lub konta usługi Azure AD — utworzony dla nich bezproblemowo w czasie realizacji oferty.

### <a name="delegate-to-application-and-group-owners"></a>Delegowanie do aplikacji i właścicieli grupy

Jako właściciela grupy lub aplikacji możesz dodać użytkowników B2B bezpośrednio do dowolnej aplikacji, które Cię interesują, czy jest aplikacją firmy Microsoft. Administratorzy mogą delegować uprawnienia do dodawania użytkowników B2B do użytkowników niebędących administratorami. Użytkownicy niebędący administratorami mogą używać [panelu dostępu do aplikacji w usłudze Azure AD](https://myapps.microsoft.com) do dodawania użytkowników we współpracy B2B do aplikacji lub grupy.

![Panel dostępu](media/what-is-b2b/access-panel.png)

![Dodawanie elementu członkowskiego](media/what-is-b2b/add-member.png)

### <a name="authorization-policies-protect-your-corporate-content"></a>Zasady autoryzacji ochrony Twojej zawartości firmowej

Można wymusić zasady dostępu warunkowego, takie jak uwierzytelnianie wieloskładnikowe:
- Na poziomie dzierżawy
- Na poziomie aplikacji
- Dla określonych użytkowników w celu ochrony danych i aplikacji firmowych

### <a name="use-apis-and-sample-code-to-easily-build-applications-to-onboard"></a>Korzystanie z interfejsów API i przykładowy kod, aby łatwo tworzyć aplikacje do dołączenia

Przenieś swoje partnerom zewnętrznym na pokładzie w sposób dostosowany do potrzeb swojej organizacji.

Użyj [zaproszenie współpracy B2B interfejsów API](https://developer.microsoft.com/graph/docs/api-reference/v1.0/resources/invitation) w celu dostosowywania dołączanie środowisk, takich jak tworzenie portale Samoobsługowe dla rejestracji. Firma Microsoft zapewnia przykładowy kod dla portalu samoobsługowego [w serwisie Github](https://github.com/Azure/active-directory-dotnet-graphapi-b2bportal-web).

![portal rejestracji](media/what-is-b2b/sign-up-portal.png)

Przy użyciu funkcji współpracy B2B usługi Azure AD można korzystać z pełnej mocy usługi Azure AD, aby chronić Twoje relacjami z partnerami w taki sposób, że użytkownicy końcowi mogą znaleźć proste i intuicyjne.

## <a name="next-steps"></a>Kolejne kroki

- [Jak Administratorzy usługi Azure Active Directory dodać użytkowników we współpracy B2B?](add-users-administrator.md)
- [Jak pracownicy przetwarzający informacje mogą dodać użytkowników we współpracy B2B?](add-users-information-worker.md)
- [Realizacja zaproszenia współpracy B2B](redemption-experience.md)
- [Licencjonowanie współpracy platformy Azure w usłudze AD B2B](licensing-guidance.md)
- A jak zawsze, połącz się z zespołem produktu, wszelkie opinie, dyskusji i sugestie za pośrednictwem naszego [społeczności technicznej firmy Microsoft](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-B2B/bd-p/AzureAD_B2b).