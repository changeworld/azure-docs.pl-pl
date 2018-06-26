---
title: Integracja z usługą Azure Active Directory | Dokumentacja firmy Microsoft
description: Przewodnik dotyczący zalet i zasoby do integracji z usługą Azure Active Directory.
services: active-directory
documentationcenter: dev-center-name
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: d13bba54-96bd-4b81-bee9-c8025ffa1648
ms.service: active-directory
ms.component: develop
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/27/2017
ms.author: celested
ms.reviewer: bryanla
ms.custom: aaddev
ms.openlocfilehash: b6de3094e903c3584aa4a57e7792e4b07711f74a
ms.sourcegitcommit: 828d8ef0ec47767d251355c2002ade13d1c162af
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/25/2018
ms.locfileid: "36937142"
---
# <a name="integrating-with-azure-active-directory"></a>Integracja z usługą Azure Active Directory
[!INCLUDE [active-directory-devguide](../../../includes/active-directory-devguide.md)]

Usługa Azure Active Directory udostępnia organizacjom korporacyjnej Zarządzanie tożsamościami dla aplikacji w chmurze. Integracja z usługą Azure AD zwiększa udoskonalone środowisko logowania użytkowników i aplikacja jest zgodna z zasadami IT.

## <a name="how-to-integrate"></a>Sposób integracji
Istnieje kilka sposobów aplikacji do integracji z usługą Azure AD. Skorzystaj z jako wiele lub kilka z tych scenariuszy, ponieważ jest odpowiedni dla twojej aplikacji.

### <a name="support-azure-ad-as-a-way-to-sign-in-to-your-application"></a>Obsługa usługi Azure AD jako sposobem logowania do aplikacji
**Zmniejsz logowania tarcia i zmniejszyć koszty pomocy technicznej.** Za pomocą usługi Azure AD do logowania do aplikacji, użytkownicy nie mają więcej nazw i haseł do zapamiętania. Deweloper będziesz mieć jedno hasło mniej do przechowywania i ochrony. Brak konieczności obsługi resetuje zapomniane hasło może być znaczne oszczędności, samodzielnie. Usługi Azure AD uprawnień logowania dla niektórych na świecie najbardziej popularnych aplikacji w chmurze, w tym usługi Office 365 i Microsoft Azure. Setki milionów użytkowników z milionów organizacji, prawdopodobnie użytkownika jest już zalogowany do usługi Azure AD. Dowiedz się więcej o [dodanie obsługi usługi Azure AD w celu logowania się](active-directory-authentication-scenarios.md).

**Uproszczenie logowania się aplikacji.**  Podczas tworzenia konta dla aplikacji usługi Azure AD można wysłać ważne informacje o użytkowniku, aby było wstępnie wypełnić swojego konta formularza lub całkowicie wyeliminować. Użytkownicy mogą rejestrować się w aplikacji przy użyciu konta usługi Azure AD za pomocą znanych zgody środowisko podobne do tych znalezionych w mediów społecznościowych i aplikacji dla urządzeń przenośnych. Każdy użytkownik, można zarejestrować się i zaloguj się do aplikacji, która jest zintegrowany z usługą Azure AD bez konieczności zaangażowania IT. Dowiedz się więcej o [zarejestrowanie się aplikacji dla usługi Azure AD konto logowania](../../app-service/app-service-mobile-how-to-configure-active-directory-authentication.md).

### <a name="browse-for-users-manage-user-provisioning-and-control-access-to-your-application"></a>Przeglądaj w poszukiwaniu użytkowników, zarządzanie, inicjowanie obsługi użytkowników i kontroli dostępu do aplikacji
**Wyszukaj użytkowników w katalogu.**  Użyj interfejsu API programu Graph, aby pomóc użytkownikom wyszukiwanie, a następnie przejdź do innych osób w organizacji, gdy zapraszanie innych osób lub udzielanie dostępu, nie wymagając od nich wpisz adres e-mail adresów. Użytkownicy mogą przeglądać przy użyciu interfejsu Styl książki znanych adresów, w tym wyświetlanie szczegółów hierarchii organizacji. Dowiedz się więcej o [interfejsu API programu Graph](active-directory-graph-api.md).

**Ponowne wykorzystywanie grup usługi Active Directory i list dystrybucyjnych, który klienta jest już zarządzany.**  Usługi Azure AD zawiera grupy klient jest już przy użyciu dystrybucji poczty e-mail i zarządzanie dostępem. Ponownie przy użyciu interfejsu API programu Graph, używać tych grup zamiast konieczności tworzenia i zarządzania nimi osobne zestawy grup w aplikacji klienta. Informacje o grupie mogą być również wysyłane do aplikacji w logowania tokenów. Dowiedz się więcej o [interfejsu API programu Graph](active-directory-graph-api.md).

**Użyj usługi Azure AD w celu kontrolowania, kto ma dostęp do aplikacji.**  Administratorzy i właściciele aplikacji w usłudze Azure AD można przypisać dostęp do aplikacji do konkretnych użytkowników i grup. Przy użyciu interfejsu API programu Graph, można czytać tej listy i użyj go do kontroli aprowizację i anulowanie obsługi zasobów i udostępnić w aplikacji.

**Kontrola dostępu oparta na użycie usługi Azure AD dla ról.**  Administratorzy i właściciele aplikacji mogą przypisywać użytkowników i grup do ról, które definiują podczas rejestrowania aplikacji w usłudze Azure AD. Informacje o rolach są wysyłane do aplikacji w logowania tokeny i również mogą być odczytywane przy użyciu interfejsu API programu Graph. Dowiedz się więcej o [przy użyciu usługi Azure AD, do autoryzacji](https://cloudblogs.microsoft.com/enterprisemobility/2014/12/18/azure-active-directory-now-with-group-claims-and-application-roles/).

### <a name="get-access-to-users-profile-calendar-email-contacts-files-and-more"></a>Uzyskiwanie dostępu do profilu użytkownika, kalendarza, poczty E-mail, kontakty, plików i więcej
**Usługi Azure AD jest serwerem autoryzacji dla usługi Office 365 i innych usług biznesowych firmy Microsoft.**  Jeśli są używane usługi Azure AD w celu logowania się do aplikacji lub łączenie bieżącego konta użytkownika do konta użytkownika usługi Azure AD przy użyciu protokołu OAuth 2.0 pomocy technicznej może wysłać żądanie odczytu i zapisu do profilu użytkownika, kalendarza, poczty e-mail, kontakty, plików i innych informacji. Można bezproblemowo zapisać zdarzeń do kalendarza użytkownika i do odczytu lub zapisu plików do usługi OneDrive. Dowiedz się więcej o [podczas uzyskiwania dostępu do interfejsów API Office 365](https://msdn.microsoft.com/office/office365/howto/platform-development-overview).

### <a name="promote-your-application-in-the-azure-and-office-365-marketplaces"></a>Wspieranie aplikacji platformy Azure i rynków w portalu usługi Office 365
**Podwyższ poziom aplikacji do milionów organizacje, które już używają usługi Azure AD.**  Użytkownicy, którzy wyszukiwania i Przeglądaj tych rynków już jest używany jeden lub więcej usług w chmurze, dzięki czemu kwalifikowaną klienci usługi w chmurze. Dowiedz się więcej o aplikacji w [portalu Azure Marketplace](https://azure.microsoft.com/marketplace/partner-program/).

**Przy rejestrowaniu użytkowników aplikacji pojawi się w ich panel dostępu usługi Azure AD i uruchamiania aplikacji usługi Office 365.**  Użytkownicy będą mogli szybko i łatwo wrócić do aplikacji później, poprawy stopnia zaangażowania użytkowników. Dowiedz się więcej o [panel dostępu usługi Azure AD](../active-directory-saas-access-panel-introduction.md).

### <a name="secure-device-to-service-and-service-to-service-communication"></a>Zabezpieczenia komunikacji usługi urządzeń i usług
**Za pomocą usługi Azure AD dla zarządzania tożsamościami, usług i urządzeń zmniejsza należy napisać kod i pozwala działowi IT zarządzanie dostępem.**  Usługi i urządzenia można uzyskać tokenów z usługi Azure AD przy użyciu protokołu OAuth i tych tokenów umożliwiają dostęp do interfejsów API sieci web. Używanie programu Azure AD można uniknąć pisania kodu uwierzytelniania złożonego. Ponieważ tożsamości, urządzeń i usług są przechowywane w usłudze Azure AD, IT mogą zarządzać kluczami i odwołania w jednym miejscu zamiast w tym oddzielnie w aplikacji.

## <a name="benefits-of-integration"></a>Korzyści wynikające z integracji
Integracja z usługą Azure AD jest dostarczany z korzyści, które nie wymagają napisać dodatkowy kod.

### <a name="integration-with-enterprise-identity-management"></a>Integracja z usługą zarządzania tożsamościami w przedsiębiorstwie
**Pomoc aplikacji są zgodne z zasadami IT.**  Organizacje zintegrować ich systemy zarządzania tożsamościami przedsiębiorstwa z usługą Azure AD, gdy osoba opuszcza organizację, automatycznie utracą dostęp do aplikacji bez IT konieczności wykonać dodatkowe czynności. IT można zarządzać, kto może uzyskać dostępu do aplikacji i ustalenia, jakie zasady dostępu są wymagane — przykład uwierzytelnianie wieloskładnikowe - zmniejszenie trzeba napisać kod do wykonania złożonych zasadami firmy. Usługa Azure AD zapewnia administratorów z dziennika inspekcji szczegółowe kto zalogowany do aplikacji tak IT mogą śledzić użycie.

**Usługi Azure AD rozszerza usługi Active Directory do chmury, tak aby aplikację można zintegrować z usługą Active Directory.**  Wiele organizacji na całym świecie korzystania z usługi Active Directory jako ich główna logowania i system zarządzania tożsamościami i wymaga ich aplikacji do pracy z usługą Active Directory. Integracja z usługą Azure AD integruje się z usługą Active Directory aplikacji.

### <a name="advanced-security-features"></a>Funkcje zaawansowane zabezpieczenia
**Uwierzytelnianie wieloskładnikowe.**  Usługa Azure AD zapewnia natywnego uwierzytelnianie wieloskładnikowe. Administratorzy IT mogą wymagać uwierzytelniania wieloskładnikowego na dostęp do aplikacji, dzięki czemu nie trzeba samodzielnie kodu ta obsługa. Dowiedz się więcej o [uwierzytelnianie wieloskładnikowe](https://azure.microsoft.com/documentation/services/multi-factor-authentication/).

**Nietypowe logowania wykrywania.**  Usługi Azure AD procesów logowania ponad miliard dziennie, używając algorytmów uczenia maszynowego w celu wykrycia podejrzanych działań, a także powiadamiania administratorów IT o potencjalnych problemach. Dzięki obsłudze logowania w usłudze Azure AD, aplikacja pobiera asysty tej ochrony. Dowiedz się więcej o [wyświetlanie raportów dostępu do usługi Azure Active Directory](../active-directory-view-access-usage-reports.md).

**Dostęp warunkowy.**  Oprócz uwierzytelnianie wieloskładnikowe, Administratorzy mogą wymagać określonych warunków zostać spełnione, aby użytkownicy mogą zalogować się do aplikacji. Warunki, które można ustawić obejmują zakres adresów IP na urządzeniach klienckich, członkostwa w określonej grupie i stan urządzenia używane dla dostępu. Dowiedz się więcej o [dostępu warunkowego w usłudze Azure Active Directory](../active-directory-conditional-access-azure-portal.md).

### <a name="easy-development"></a>Łatwość programowania
**Standardowych protokołach branżowych.**  Firma Microsoft dokłada starań, aby obsługa standardy branżowe. Usługi Azure AD obsługuje protokoły uwierzytelniania SAML 2.0, OpenID Connect 1.0 OAuth 2.0 i WS-Federation 1.2. Interfejsu API programu Graph jest OData 4.0 zgodne. Jeśli aplikacja już obsługuje protokoły SAML 2.0 lub OpenID Connect 1.0 federacyjnym w celu logowania się, włączenie obsługi usługi Azure AD może być prosta. Dowiedz się więcej o [usługi Azure AD obsługiwane protokoły uwierzytelniania](active-directory-authentication-protocols.md).

**Biblioteki typu open source.**  Firma Microsoft udostępnia biblioteki w pełni obsługiwane typu open source dla popularnych języków i platform do rozwoju szybkości. Kod źródłowy jest licencją Apache 2.0 i wolnych do rozwidlania i brać udział w projektach. Dowiedz się więcej o [biblioteki uwierzytelniania usługi Azure AD](active-directory-authentication-libraries.md).

### <a name="worldwide-presence-and-high-availability"></a>Obecność na całym świecie i wysokiej dostępności
**Usługi Azure AD jest wdrażana w centrach danych na całym świecie zarządzane i które są monitorowane przez całą dobę.**  Usługi Azure AD to system zarządzania tożsamość platformy Microsoft Azure i usługi Office 365 i jest wdrażany w 28 centrów danych na całym świecie. Katalog danych jest gwarantowana powinny być replikowane na co najmniej trzech centrów danych. Usługi równoważenia obciążenia globalne zapewnienia użytkownikom dostępu najbliższego kopię usługi Azure AD, zawierający dane i automatycznie przekierowuje żądania do innych centrów danych, jeśli zostanie wykryty problem.

## <a name="next-steps"></a>Następne kroki
[Wprowadzenie do pisania kodu](active-directory-developers-guide.md#get-started).

[Loguj użytkowników przy użyciu usługi Azure AD](active-directory-authentication-scenarios.md)

