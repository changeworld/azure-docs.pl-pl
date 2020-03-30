---
title: Współużytkuj konta i poświadczenia — usługa Azure Active Directory | Dokumenty firmy Microsoft
description: W tym artykule opisano, jak usługa Azure Active Directory umożliwia organizacjom bezpieczne udostępnianie kont dla aplikacji lokalnych i usług w chmurze dla konsumentów.
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 08/01/2019
ms.author: curtand
ms.reviewer: jeffsta
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 987c3ee7c90eb0bb793b96eb2771efbb258f16a4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77565506"
---
# <a name="sharing-accounts-with-azure-ad"></a>Udostępnianie kont za pomocą usługi Azure AD

## <a name="overview"></a>Omówienie

Czasami organizacje muszą używać jednej nazwy użytkownika i hasła dla wielu osób, co zwykle zdarza się w dwóch przypadkach:

* Podczas uzyskiwania dostępu do aplikacji, które wymagają unikatowego logowania i hasła dla każdego użytkownika, czy to w aplikacjach lokalnych, czy w usługach chmury konsumenckiej (na przykład firmowych kontach w mediach społecznościowych).
* Podczas tworzenia środowisk wielu użytkowników. Możesz mieć jedno konto lokalne, które ma podwyższone uprawnienia i jest używane do wykonywania podstawowych czynności związanych z konfiguracją, administracją i odzyskiwaniem. Na przykład lokalne konto "administrator globalny" dla usługi Office 365 lub konto główne w Salesforce.

Tradycyjnie te konta są współużytkowane przez rozpowszechnianie poświadczeń (nazwa użytkownika i hasło) do odpowiednich osób lub przechowywanie ich w lokalizacji udostępnionej, gdzie wielu zaufanych agentów może uzyskać do nich dostęp.

Tradycyjny model udostępniania ma kilka wad:

* Włączenie dostępu do nowych aplikacji wymaga dystrybucji poświadczeń do wszystkich osób, które potrzebują dostępu.
* Każda aplikacja udostępniona może wymagać własnego unikatowego zestawu poświadczeń udostępnionych, wymagając od użytkowników zapamiętywania wielu zestawów poświadczeń. Gdy użytkownicy muszą pamiętać wiele poświadczeń, zwiększa się ryzyko, że uciekają się do ryzykownych praktyk. (na przykład zapisywanie haseł).
* Nie można stwierdzić, kto ma dostęp do aplikacji.
* Nie można stwierdzić, kto *uzyskał dostęp do* aplikacji.
* Jeśli chcesz usunąć dostęp do aplikacji, musisz zaktualizować poświadczenia i rozesłać je do wszystkich osób, które potrzebują dostępu do tej aplikacji.

## <a name="azure-active-directory-account-sharing"></a>Udostępnianie konta usługi Azure Active Directory

Usługa Azure AD zapewnia nowe podejście do korzystania z kont udostępnionych, które eliminuje te wady.

Administrator usługi Azure AD konfiguruje aplikacje, do których użytkownik może uzyskać dostęp za pomocą panelu dostępu i wybierając typ logowania jednokrotnego najlepiej dostosowanego do tej aplikacji. Jeden z tych typów, *oparte na hasłach jednokrotne zalogowanie się,* umożliwia usłudze Azure AD działać jako rodzaj "brokera" podczas procesu logowania dla tej aplikacji.

Użytkownicy logują się raz za pomocą swojego konta organizacji. To konto jest tym samym, którego regularnie używają do uzyskiwania dostępu do swojego pulpitu lub poczty e-mail. Mogą odnajdywać i uzyskiwać dostęp tylko do tych aplikacji, do których są przypisane. W przypadku kont udostępnionych ta lista aplikacji może zawierać dowolną liczbę poświadczeń udostępnionych. Użytkownik końcowy nie musi zapamiętywać ani zapisywać różnych kont, z których może korzystać.

Konta współdzielone nie tylko zwiększają nadzór i zwiększają użyteczność, ale także zwiększają bezpieczeństwo. Użytkownicy z uprawnieniami do używania poświadczeń nie widzą udostępnionego hasła, ale raczej otrzymują uprawnienia do używania hasła w ramach zorganizowanego przepływu uwierzytelniania. Ponadto niektóre aplikacje logowania przy użyciu hasła SSO umożliwiają korzystanie z usługi Azure AD do okresowego przerzucenia (aktualizacji) haseł. System używa dużych, złożonych haseł, co zwiększa bezpieczeństwo konta. Administrator może łatwo udzielić lub odwołać dostęp do aplikacji, wie, kto ma dostęp do konta i kto uzyskał do niego dostęp w przeszłości.

Usługa Azure AD obsługuje konta współdzielone dla dowolnego pakietu EMS (EMS) lub planu licencji usługi Azure AD Premium we wszystkich typach aplikacji jednokrotnego logowania haseł. Możesz udostępniać konta dla dowolnej z tysięcy wstępnie zintegrowanych aplikacji w galerii aplikacji i możesz dodawać własną aplikację uwierzytelniającą hasło za pomocą [niestandardowych aplikacji logowania przy logowaniu.](../manage-apps/configure-single-sign-on-non-gallery-applications.md)

Funkcje usługi Azure AD, które umożliwiają udostępnianie kont, obejmują:

* [Logowanie jednokrotne hasła](../manage-apps/what-is-single-sign-on.md#password-based-sso)
* Agent logowania jednokrotnego hasła
* [Przypisanie grupy](groups-self-service-management.md)
* Aplikacje z hasłami niestandardowymi
* [Pulpit nawigacyjny/raporty użycia aplikacji](../active-directory-passwords-get-insights.md)
* Portale dostępu użytkowników końcowych
* [Serwer proxy aplikacji](../manage-apps/application-proxy.md)
* [Rynek usługi Active Directory](https://azure.microsoft.com/marketplace/active-directory/all/)

## <a name="sharing-an-account"></a>Udostępnianie konta

Aby udostępnić konto za pomocą usługi Azure AD, należy:

* Dodawanie [galerii aplikacji](https://azure.microsoft.com/marketplace/active-directory/) lub [aplikacji niestandardowej](https://cloudblogs.microsoft.com/enterprisemobility/2015/06/17/bring-your-own-app-with-azure-ad-self-service-saml-configuration-now-in-preview/)
* Konfigurowanie aplikacji do logowania jednokrotnego (Logowanie jednokrotne)
* Użyj [przypisania opartego na grupach](groups-saasapps.md) i wybierz opcję wprowadzania poświadczeń udostępnionych

Możesz również uczynić swoje konto udostępnione bezpieczniejsze za pomocą uwierzytelniania wieloskładnikowego (MFA) (dowiedz się więcej o [zabezpieczaniu aplikacji za pomocą usługi Azure AD)](../authentication/concept-mfa-whichversion.md)i możesz delegować możliwość zarządzania, kto ma dostęp do aplikacji przy użyciu zarządzania [grupą samoobsługową usługi Azure AD.](groups-self-service-management.md)

## <a name="next-steps"></a>Następne kroki

* [Zarządzanie aplikacjami w usłudze Azure Active Directory](../manage-apps/what-is-application-management.md)
* [Ochrona aplikacji za pomocą dostępu warunkowego](../active-directory-conditional-access-azure-portal.md)
* [Samoobsługowe zarządzanie grupą/SSAA](groups-self-service-management.md)
