---
title: Udostępnianie kont i poświadczeń — usługi Azure Active Directory | Dokumentacja firmy Microsoft
description: W tym artykule opisano, jak usługi Azure Active Directory umożliwia organizacjom bezpieczne udostępnianie kont lokalnych aplikacji i usług w chmurze konsumenta.
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 01/31/2019
ms.author: curtand
ms.reviewer: jeffsta
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: ba9deb00b885dad1d69eb38d4977aafd3d80ab91
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60468025"
---
# <a name="sharing-accounts-with-azure-ad"></a>Udostępnianie kont w usłudze Azure AD
## <a name="overview"></a>Omówienie
Czasami organizacje muszą używać jednej nazwy użytkownika i hasło dla wielu osób, które zwykle odbywa się w dwóch przypadkach:

* Podczas uzyskiwania dostępu do aplikacji, które wymagają unikatowych logowania i hasło dla każdego użytkownika, czy aplikacje lokalne lub odbiorcy usługi w chmurze (na przykład kont mediów społecznościowych firmy).
* Podczas tworzenia środowiska z wieloma użytkownikami. Może mieć jednego, lokalnego konta, mającego podniesione uprawnienia, która jest używana do podstawowego działania Instalatora, administracji i odzyskiwania. Na przykład lokalnego "administrator globalny" konta usługi Office 365 lub konta głównego w usłudze Salesforce.

Tradycyjnie te konta są udostępniane przez dystrybucję poświadczeń (nazwy użytkownika i hasło) do prawej osób lub zapisywania ich w udostępnionej lokalizacji, w których wielu agentów zaufane mają do nich dostęp.

Tradycyjnego modelu udostępniania ma kilka wady:

* Włączanie dostępu do nowych aplikacji wymaga rozpowszechnić poświadczenia dla wszystkich użytkowników, których potrzebuje dostępu.
* Każdy udostępnionej aplikacji może wymagać swój własny unikatowy zestaw poświadczeń udostępnionych, wymagając od użytkowników do zapamiętania wiele zestawów poświadczeń. Gdy użytkownicy mają do zapamiętania wiele poświadczeń, ryzyko zwiększa ich zastosuje poważniejsze ryzykowne rozwiązań. (na przykład zapisywać hasła).
* Nie wiadomo, kto ma dostęp do aplikacji.
* Nie można sprawdzić, kto ma *dostępne* aplikacji.
* Jeśli chcesz usunąć dostęp do aplikacji, musisz zaktualizować poświadczenia i przekazywania ich do wszystkich osób, które wymagają dostępu do tej aplikacji.

## <a name="azure-active-directory-account-sharing"></a>Konta i do udostępniania usługi Azure Active Directory
Usługa Azure AD zapewnia nowe podejście do korzystania z udostępnionych kont, które eliminuje te niedogodności.

Administrator usługi Azure AD umożliwia skonfigurowanie, aplikacji, które użytkownik ma dostęp, za pomocą panelu dostępu i wybierając typ najlepiej rejestracji jednokrotnej odpowiednie dla danego zastosowania. Jeden z tych typów *opartego na hasłach logowania jednokrotnego*, umożliwia usłudze Azure AD działają jako rodzaju "brokerze" podczas procesu logowania jednokrotnego dla tej aplikacji.

Użytkownicy logują raz przy użyciu swojego konta organizacji. To konto jest używane regularnie uzyskać dostęp do swojego pulpitu lub wiadomości e-mail. Mogą odnajdywać i dostęp do tych aplikacji, które są przypisane. Za pomocą udostępnionych kont tej listy aplikacji może zawierać dowolną liczbę poświadczeń udostępnionych. Użytkownik końcowy nie musi Zapamiętaj lub Zapisz różnych kont, które mogą używać.

Udostępnione konta nie tylko zwiększyć nadzoru i zwiększyć użyteczność, również zwiększyć bezpieczeństwo. Użytkownicy z uprawnieniami do korzystania z poświadczeń nie widzisz wspólne hasło, ale raczej uzyskać uprawnienia do korzystania z hasła jako część przepływu uwierzytelniania zorganizowane. Ponadto niektóre hasło logowania jednokrotnego aplikacji zapewniają możliwość korzystania z usługi Azure AD, należy okresowo przerzucania (aktualizacja) hasła. System używa dużych i złożonych haseł, które zwiększa bezpieczeństwo konta. Administrator może łatwo udzielić lub odwołać dostęp do aplikacji, wie, kto ma dostęp do konta i kto ma dostęp do niego w przeszłości.

Usługa Azure AD obsługuje udostępnione konta dla dowolnej usługi Enterprise Mobility Suite (EMS), Premium lub podstawowa licencjonowanych użytkowników, w przypadku wszystkich typów hasło pojedynczego logowania aplikacji. Można udostępniać konta dla dowolnej tysiące wstępnie zintegrowanych aplikacji w galerii aplikacji i można dodać własne uwierzytelniania hasła aplikacji przy użyciu [niestandardowe aplikacje logowania jednokrotnego](../manage-apps/configure-single-sign-on-portal.md).

Funkcje platformy Azure AD, które umożliwiają udostępnianie konta obejmują:

* [Hasło logowania jednokrotnego](../manage-apps/what-is-single-sign-on.md#password-based-sso)
* Hasło pojedynczego logowania jednokrotnego agenta
* [Przypisanie do grupy](groups-self-service-management.md)
* Niestandardowe hasło aplikacji
* [Raporty pulpitu nawigacyjnego użycie aplikacji](../active-directory-passwords-get-insights.md)
* Portale dostępu użytkownika końcowego
* [Serwer proxy aplikacji](../manage-apps/application-proxy.md)
* [Active Directory Marketplace](https://azure.microsoft.com/marketplace/active-directory/all/)

## <a name="sharing-an-account"></a>Udostępnianie kont
Aby korzystać z usługi Azure AD, aby udostępnić konto, należy:

* Dodawanie aplikacji [galerii aplikacji](https://azure.microsoft.com/marketplace/active-directory/) lub [aplikacji niestandardowej](https://cloudblogs.microsoft.com/enterprisemobility/2015/06/17/bring-your-own-app-with-azure-ad-self-service-saml-configuration-now-in-preview/)
* Konfigurowanie aplikacji dla hasła pojedynczego logowania jednokrotnego (SSO)
* Użyj [przypisywania na podstawie grupy](groups-saasapps.md) i wybrać opcję wprowadzenia poświadczeń udostępnionych
* Opcjonalnie: w niektórych aplikacjach, takich jak Facebook, Twitter i LinkedIn, możesz włączyć opcję dla [usługi Azure AD automatycznego przerzucania hasła](https://cloudblogs.microsoft.com/enterprisemobility/2015/02/20/azure-ad-automated-password-roll-over-for-facebook-twitter-and-linkedin-now-in-preview/)

Można również ustawić konta udostępnionego bezpieczniejsze z usługi Multi-Factor Authentication (MFA) (Dowiedz się więcej o [zabezpieczanie aplikacji z usługą Azure AD](../authentication/concept-mfa-whichversion.md)) i delegować możliwość zarządzania, kto ma dostęp do aplikacji przy użyciu [ Usługa Azure AD samoobsługi](groups-self-service-management.md) grupy zarządzania.

## <a name="related-articles"></a>Pokrewne artykuły:
* [Zarządzanie aplikacjami w usłudze Azure Active Directory](../manage-apps/what-is-application-management.md)
* [Ochrona aplikacji przy użyciu dostępu warunkowego](../active-directory-conditional-access-azure-portal.md)
* [Zarządzanie grupami samoobsługi/SSAA](groups-self-service-management.md)
