---
title: Jak zintegrować się z usługą Azure Active Directory
description: Poznaj zalety integracji aplikacji z usługą Azure Active Directory i uzyskaj zasoby dotyczące funkcji, takich jak uproszczone logowanie, zarządzanie tożsamościami, uwierzytelnianie wieloskładnikowe i kontrola dostępu.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 04/08/2019
ms.author: ryanwi
ms.reviewer: jmprieur
ms.custom: aaddev, seoapril2019
ms.openlocfilehash: 4eaca7350ac006a2fcf2eed8d3eb0cb8ef274841
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/08/2020
ms.locfileid: "80885705"
---
# <a name="integrating-with-azure-active-directory"></a>Integracja z usługą Azure Active Directory

[!INCLUDE [active-directory-devguide](../../../includes/active-directory-devguide.md)]

W tym artykule dowiesz się o korzyściach płynących z integracji aplikacji z usługą Azure Active Directory (Azure AD) i pobierz zasoby do integracji. Usługa Azure AD zapewnia organizacjom zarządzanie tożsamościami klasy korporacyjnej dla aplikacji w chmurze. Integracja z usługą Azure AD zapewnia użytkownikom usprawnione środowisko logowania i pomaga aplikacji dostosować się do zasad IT.

## <a name="how-to-integrate"></a>Sposób przeprowadzania integracji

Istnieje kilka sposobów integracji aplikacji z usługą Azure AD. Skorzystaj z jak największej liczby lub kilku z tych scenariuszy, które są odpowiednie dla aplikacji.

### <a name="support-azure-ad-as-a-way-to-sign-in-to-your-application"></a>Obsługa usługi Azure AD jako sposobu logowania się do aplikacji

**Zmniejsz tarcie logowania i obniż koszty pomocy technicznej.** Korzystając z usługi Azure AD do logowania się do aplikacji, użytkownicy nie będą mieli jeszcze jednej nazwy i hasła do zapamiętania. Jako programista będziesz mieć jedno hasło mniej do przechowywania i ochrony. Nie konieczności obsługi zapomniane resetowanie hasła może być znaczne oszczędności sam. Usługa Azure AD umożliwia logowanie się do niektórych najpopularniejszych aplikacji w chmurze na świecie, takich jak Office 365 i Microsoft Azure. Setki milionów użytkowników z milionów organizacji są prawdopodobnie zalogowane do usługi Azure AD. Dowiedz się więcej o [dodawaniu pomocy technicznej dla logowania usługi Azure AD](authentication-scenarios.md).

**Uprość zarejestrować się w aplikacji.**  Podczas rejestracji w aplikacji usługa Azure AD może wysyłać podstawowe informacje o użytkowniku, aby można było wstępnie wypełnić formularz rejestracji lub całkowicie go wyeliminować. Użytkownicy mogą zarejestrować się w aplikacji przy użyciu konta usługi Azure AD za pośrednictwem znanego środowiska zgody podobnego do tych, które można znaleźć w mediach społecznościowych i aplikacjach mobilnych. Każdy użytkownik może zarejestrować się i zalogować się do aplikacji, która jest zintegrowana z usługą Azure AD bez konieczności zaangażowania it. Dowiedz się więcej o [rejestrowaniu aplikacji do logowania do konta usługi Azure AD](../../app-service/configure-authentication-provider-aad.md).

### <a name="browse-for-users-manage-user-provisioning-and-control-access-to-your-application"></a>Przeglądaj użytkowników, zarządzaj inicjowania obsługi administracyjnej użytkowników i kontroluj dostęp do aplikacji

**Wyszukaj w poszukiwaniu użytkowników w katalogu.**  Użyj interfejsu API programu Microsoft Graph, aby ułatwić użytkownikom wyszukiwanie i przeglądanie innych osób w organizacji podczas zapraszania innych osób lub udzielania dostępu, zamiast wymagać od nich wpisywania adresów e-mail. Użytkownicy mogą przeglądać za pomocą znanego interfejsu stylu książki adresowej, w tym przeglądać szczegóły hierarchii organizacyjnej. Dowiedz się więcej o [interfejsie API programu Microsoft Graph](https://docs.microsoft.com/graph/overview).

**Ponowne używanie grup i list dystrybucyjnych usługi Active Directory, z których klient już zarządza.**  Usługa Azure AD zawiera grupy, których klient już używa do dystrybucji poczty e-mail i zarządzania dostępem. Korzystając z interfejsu API programu Microsoft Graph, należy ponownie używać tych grup zamiast wymagać od klienta tworzenia i zarządzania oddzielnym zestawem grup w aplikacji. Informacje o grupie można również wysłać do aplikacji w tokenach logowania. Dowiedz się więcej o [interfejsie API programu Microsoft Graph](https://docs.microsoft.com/graph/overview).

**Użyj usługi Azure AD, aby kontrolować, kto ma dostęp do aplikacji.**  Administratorzy i właściciele aplikacji w usłudze Azure AD mogą przypisywać dostęp do aplikacji określonym użytkownikom i grupom. Za pomocą interfejsu API programu Microsoft Graph można przeczytać tę listę i używać jej do kontrolowania inicjowania obsługi administracyjnej i usuwania obsługi administracyjnej zasobów i dostępu w aplikacji.

**Użyj usługi Azure AD dla kontroli dostępu opartej na rolach.**  Administratorzy i właściciele aplikacji mogą przypisywać użytkowników i grupy do ról zdefiniowanych podczas rejestrowania aplikacji w usłudze Azure AD. Informacje o roli są wysyłane do aplikacji w tokenach logowania i można je również odczytać za pomocą interfejsu API programu Microsoft Graph. Dowiedz się więcej o [używaniu usługi Azure AD do autoryzacji.](https://cloudblogs.microsoft.com/enterprisemobility/2014/12/18/azure-active-directory-now-with-group-claims-and-application-roles/)

### <a name="get-access-to-users-profile-calendar-email-contacts-files-and-more"></a>Uzyskaj dostęp do profilu użytkownika, kalendarza, poczty e-mail, kontaktów, plików i innych

**Usługa Azure AD to serwer autoryzacji usługi Office 365 i innych usług biznesowych firmy Microsoft.**  Jeśli obsługujesz usługę Azure AD, aby zalogować się do aplikacji lub pomocy technicznej łącząc bieżące konta użytkowników z kontami użytkowników usługi Azure AD przy użyciu usługi OAuth 2.0, możesz zażądać dostępu do odczytu i zapisu do profilu użytkownika, kalendarza, poczty e-mail, kontaktów, plików i innych informacji. Możesz bezproblemowo zapisywać zdarzenia w kalendarzu użytkownika oraz odczytywać lub zapisywać pliki w usłudze OneDrive. Dowiedz się więcej o [uzyskiwaniu dostępu do interfejsów API usługi Office 365](https://msdn.microsoft.com/office/office365/howto/platform-development-overview).

### <a name="promote-your-application-in-the-azure-and-office-365-marketplaces"></a>Promuj swoją aplikację na platformach Azure i w witrynach Marketplaces usługi Office 365

**Promuj swoją aplikację do milionów organizacji, które już korzystają z usługi Azure AD.**  Użytkownicy, którzy wyszukują i przeglądają te rynki, korzystają już z jednej lub więcej usług w chmurze, co czyni ich kwalifikowanymi klientami usług w chmurze. Dowiedz się więcej o promowaniu aplikacji [w portalu Azure Marketplace.](https://azure.microsoft.com/marketplace/partner-program/)

**Gdy użytkownicy zarejestrują się w aplikacji, pojawi się w panelu dostępu usługi Azure AD i programie uruchamiania aplikacji usługi Office 365.**  Użytkownicy będą mogli szybko i łatwo powrócić do aplikacji później, zwiększając zaangażowanie użytkowników. Dowiedz się więcej o [panelu dostępu usługi Azure AD](../user-help/active-directory-saas-access-panel-introduction.md).

### <a name="secure-device-to-service-and-service-to-service-communication"></a>Bezpieczna komunikacja między urządzeniami i usługami

**Korzystanie z usługi Azure AD do zarządzania tożsamościami usług i urządzeń zmniejsza kod potrzebny do zapisu i umożliwia it do zarządzania dostępem.**  Usługi i urządzenia mogą uzyskać tokeny z usługi Azure AD przy użyciu OAuth i używać tych tokenów do uzyskiwania dostępu do internetowych interfejsów API. Za pomocą usługi Azure AD można uniknąć pisania złożonego kodu uwierzytelniania. Ponieważ tożsamości usług i urządzeń są przechowywane w usłudze Azure AD, IT może zarządzać kluczami i odwołania w jednym miejscu, zamiast robić to oddzielnie w aplikacji.

## <a name="benefits-of-integration"></a>Korzyści z integracji

Integracja z usługą Azure AD jest wyposażony w korzyści, które nie wymagają pisania dodatkowego kodu.

### <a name="integration-with-enterprise-identity-management"></a>Integracja z zarządzaniem tożsamościami przedsiębiorstwa

**Pomóż aplikacji przestrzegać zasad IT.**  Organizacje integrują swoje systemy zarządzania tożsamościami przedsiębiorstwa z usługą Azure AD, więc gdy dana osoba opuści organizację, automatycznie utraci dostęp do aplikacji bez konieczności podejmowania dodatkowych kroków przez dział IT. Dział IT może zarządzać, kto może uzyskać dostęp do aplikacji i określić, jakie zasady dostępu są wymagane — na przykład uwierzytelnianie wieloskładnikowe — zmniejszając potrzebę pisania kodu w celu zapewnienia zgodności ze złożonymi zasadami firmowymi. Usługa Azure AD zapewnia administratorom szczegółowy dziennik inspekcji, który zalogował się do aplikacji, dzięki czemu firma IT może śledzić użycie.

**Usługa Azure AD rozszerza usługę Active Directory na chmurę, dzięki czemu aplikacja może integrować się z usługą AD.**  Wiele organizacji na całym świecie używa usługi Active Directory jako głównego systemu logowania i zarządzania tożsamościami i wymaga od swoich aplikacji pracy z usługą AD. Integracja z usługą Azure AD integruje aplikację z usługą Active Directory.

### <a name="advanced-security-features"></a>Zaawansowane funkcje zabezpieczeń

**Uwierzytelnianie wieloskładnikowe.**  Usługa Azure AD zapewnia natywne uwierzytelnianie wieloskładnikowe. Administratorzy IT mogą wymagać uwierzytelniania wieloskładnikowego, aby uzyskać dostęp do aplikacji, dzięki czemu nie trzeba samodzielnie kodować tej pomocy technicznej. Dowiedz się więcej o [uwierzytelnianiu wieloskładnikowym](https://azure.microsoft.com/documentation/services/multi-factor-authentication/).

**Wykrywanie nietypowych znaków.**  Usługa Azure AD przetwarza ponad miliard logów dziennie, korzystając z algorytmów uczenia maszynowego do wykrywania podejrzanych działań i powiadamiania administratorów IT o możliwych problemach. Dzięki obsłudze logowania usługi Azure AD aplikacja pobiera korzyści z tej ochrony. Dowiedz się więcej o [wyświetlaniu raportu dostępu usługi Azure Active Directory](../active-directory-view-access-usage-reports.md).

**Dostęp warunkowy.**  Oprócz uwierzytelniania wieloskładnikowego administratorzy mogą wymagać spełnienia określonych warunków, zanim użytkownicy będą mogli zalogować się do aplikacji. Warunki, które można ustawić obejmują zakres adresów IP urządzeń klienckich, członkostwo w określonych grupach i stan urządzenia używanego do uzyskiwania dostępu. Dowiedz się więcej o [dostępie warunkowym usługi Azure Active Directory](../active-directory-conditional-access-azure-portal.md).

### <a name="easy-development"></a>Łatwy rozwój

**Protokoły branżowe.**  Firma Microsoft dokłada wszelkich starań, aby wspierać standardy branżowe. Platforma tożsamości firmy Microsoft obsługuje standardowe protokoły OAuth 2.0 i OpenID Connect 1.0. Dowiedz się więcej o [protokołach uwierzytelniania platformy tożsamości firmy Microsoft](active-directory-v2-protocols.md).

**Biblioteki open source.**  Firma Microsoft zapewnia w pełni obsługiwane biblioteki open source dla popularnych języków i platform, aby przyspieszyć program rozwoju. Kod źródłowy jest licencjonowany w apache 2.0 i masz swobodę rozwidlić i przyczynić się z powrotem do projektów. Dowiedz się więcej o [bibliotece uwierzytelniania firmy Microsoft (MSAL).](reference-v2-libraries.md)

### <a name="worldwide-presence-and-high-availability"></a>Obecność na całym świecie i wysoka dostępność

**Usługa Azure AD jest wdrażana w centrach danych na całym świecie i jest zarządzana i monitorowana przez całą dobę.**  Usługa Azure AD to system zarządzania tożsamościami dla platformy Microsoft Azure i usługi Office 365 i jest wdrażany w 28 centrach danych na całym świecie. Dane katalogu są gwarantowane do replikacji do co najmniej trzech centrów danych. Globalne moduły równoważenia obciążenia zapewniają użytkownikom dostęp do najbliższej kopii usługi Azure AD zawierającej ich dane i automatycznie ponownie rozsyła żądania do innych centrów danych w przypadku wykrycia problemu.

## <a name="next-steps"></a>Następne kroki

[Zacznij pisać kod](v2-overview.md#getting-started).

[Logowanie użytkowników przy użyciu platformy tożsamości firmy Microsoft](authentication-scenarios.md)

