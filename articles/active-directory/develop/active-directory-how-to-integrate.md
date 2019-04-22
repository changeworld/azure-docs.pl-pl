---
title: Jak zintegrować z usługą Azure Active Directory | Dokumentacja firmy Microsoft
description: Informacje o zaletach usługi integracji aplikacji z usługą Azure Active Directory i pobrać zasoby dla funkcji, takich jak uproszczone logowania, Zarządzanie tożsamościami, uwierzytelnianie wieloskładnikowe i kontroli dostępu.
services: active-directory
documentationcenter: dev-center-name
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: d13bba54-96bd-4b81-bee9-c8025ffa1648
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/08/2019
ms.author: celested
ms.reviewer: bryanla
ms.custom: aaddev, seoapril2019
ms.collection: M365-identity-device-management
ms.openlocfilehash: 726d70c46dca712deaf4846c24976cdabcc49be9
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/18/2019
ms.locfileid: "59359899"
---
# <a name="integrating-with-azure-active-directory"></a>Integracja z usługą Azure Active Directory

[!INCLUDE [active-directory-devguide](../../../includes/active-directory-devguide.md)]

W tym artykule poznasz korzyści integracji aplikacji z usługą Azure Active Directory (Azure AD) i pobieranie zasobów w celu integracji. Usługa Azure AD zapewnia organizacjom przeznaczonych dla przedsiębiorstw Zarządzanie tożsamościami dla aplikacji w chmurze. Integracja z usługą Azure AD zapewnia usprawnione środowisko logowania użytkowników i pomaga aplikacjom są zgodne z zasadami IT.

## <a name="how-to-integrate"></a>Sposób przeprowadzania integracji

Istnieje kilka sposobów dla swojej aplikacji w celu integracji z usługą Azure AD. Korzystaj z jako wiele lub kilku z tych scenariuszy, ponieważ jest odpowiedni dla aplikacji.

### <a name="support-azure-ad-as-a-way-to-sign-in-to-your-application"></a>Obsługa usługi Azure AD, aby zalogować się do aplikacji

**Zmniejsz logowania Ogranicz liczbę problemów i zmniejszyć koszty pomocy technicznej.** Za pomocą usługi Azure AD do logowania do aplikacji, użytkownicy nie mają jedną nazwę więcej i hasło do zapamiętania. Jako deweloper będziesz mieć jedno hasło mniej przechowywać i chronić. Brak konieczności obsługi Resetowanie zapomnianych haseł może być znaczne oszczędności, samodzielnie. Usługa Azure AD obsługuje logowanie dla niektórych na świecie najpopularniejszych aplikacji w chmurze, w tym usługi Office 365 i Microsoft Azure. Setki milionów użytkowników z milionów urządzeń w organizacji, jest szansa, użytkownik jest już zalogowany do usługi Azure AD. Dowiedz się więcej o [dodano obsługę logowania do usługi Azure AD](authentication-scenarios.md).

**Aby uprościć logowania się dla aplikacji.**  Podczas logowania się dla aplikacji usługi Azure AD można wysłać podstawowe informacje dotyczące użytkownika, tak, aby można było wstępnie wypełnić rejestrowania formularza lub całkowicie wyeliminować. Użytkownicy mogą zarejestrować się w aplikacji przy użyciu swojego konta usługi Azure AD za pośrednictwem doświadczenie znanych zgody, podobne do tych w mediach społecznościowych i aplikacji mobilnych. Każdy użytkownik, można zarejestrować się i zaloguj się do aplikacji, która jest zintegrowana z usługą Azure AD bez konieczności dodatkowych zasobów informatycznych. Dowiedz się więcej o [rejestracji w ramach aplikacji na potrzeby logowania do konta usługi Azure AD](../../app-service/configure-authentication-provider-aad.md).

### <a name="browse-for-users-manage-user-provisioning-and-control-access-to-your-application"></a>Przeglądaj w poszukiwaniu użytkowników, zarządzanie aprowizowaniem użytkowników i kontrolować dostęp do aplikacji

**Przeglądaj w poszukiwaniu użytkowników w katalogu.**  Pomaganie użytkownikom wyszukiwanie, a następnie przejdź do innych osób w organizacji, podczas zapraszania innych za pomocą interfejsu API programu Graph lub udzielania dostępu, nie wymagając od nich wpisz adres e-mail adresów. Użytkownicy mogą przeglądać przy użyciu interfejsu stylu książki znanych adresów, w tym wyświetlanie szczegółów hierarchii organizacji. Dowiedz się więcej o [interfejsu API programu Graph](active-directory-graph-api.md).

**Ponowne używanie grup usługi Active Directory i listy dystrybucyjne, który klient jest już zarządzany.**  Usługa Azure AD zawiera grupy klient jest już za pomocą poczty e-mail dystrybucji i zarządzaniu dostępem. Za pomocą interfejsu API programu Graph, ponownie używać tych grup, zamiast Twojego klienta, tworzenie i zarządzanie nimi oddzielny zestaw grup w Twojej aplikacji. Informacje o grupie mogą być również wysyłane do aplikacji w logowania tokenów. Dowiedz się więcej o [interfejsu API programu Graph](active-directory-graph-api.md).

**Używaj usługi Azure AD w celu kontrolowania, kto ma dostęp do aplikacji.**  Administratorzy i właścicieli aplikacji w usłudze Azure AD może przypisać dostęp do aplikacji do konkretnych użytkowników i grup. Za pomocą interfejsu API programu Graph, może odczytywać z tej listy i użyć jej do kontrolowania aprowizację i cofanie aprowizacji zasobów i udostępnić w aplikacji.

**Kontrola dostępu oparta na użycie usługi Azure AD dla ról.**  Administratorzy i właścicieli aplikacji można przypisać użytkowników i grup do ról, które można zdefiniować podczas rejestrowania aplikacji w usłudze Azure AD. Informacje o rolach są wysyłane do aplikacji w logowania tokenów i także może zostać odczytany przy użyciu interfejsu API programu Graph. Dowiedz się więcej o [przy użyciu usługi Azure AD, do autoryzacji](https://cloudblogs.microsoft.com/enterprisemobility/2014/12/18/azure-active-directory-now-with-group-claims-and-application-roles/).

### <a name="get-access-to-users-profile-calendar-email-contacts-files-and-more"></a>Uzyskaj dostęp do użytkownika profilu, kalendarza, poczty e-mail, kontakty, pliki i więcej

**Usługa Azure AD jest serwer autoryzacji dla usługi Office 365 i innych usług biznesowych firmy Microsoft.**  Jeśli usługi Azure AD jest obsługuje logowanie do aplikacji lub pomocy technicznej, łączenie bieżących kont użytkowników z kontami użytkowników usługi Azure AD za pomocą protokołu OAuth 2.0, możesz zażądać dostępu odczytu i zapisu do profilu użytkownika, kalendarza, poczty e-mail, kontakty, pliki i inne informacje. Ty możesz bezproblemowo zapisu zdarzenia do kalendarza użytkownika i odczytywać i zapisywać pliki usługi onedrive. Dowiedz się więcej o [uzyskiwania dostępu do interfejsów API usługi Office 365](https://msdn.microsoft.com/office/office365/howto/platform-development-overview).

### <a name="promote-your-application-in-the-azure-and-office-365-marketplaces"></a>Promuj swoją aplikację na platformie Azure i Office 365 rynków

**Promuj swoją aplikację do milionów użytkowników w organizacji, którzy są już za pomocą usługi Azure AD.**  Użytkownicy, którzy wyszukiwać i przeglądać te rynków już korzystają z jednego lub więcej usług w chmurze, dzięki czemu kwalifikowana klienci usługi w chmurze. Dowiedz się więcej na temat promowania aplikacji na platformie [w portalu Azure Marketplace](https://azure.microsoft.com/marketplace/partner-program/).

**Gdy użytkownicy tworzą konta dla aplikacji, pojawi się w swoich panelach dostępu usługi Azure AD i uruchamianie aplikacji usługi Office 365.**  Użytkownicy będą mogli łatwo i szybko powrócić do aplikacji później, zwiększanie się stopnia zaangażowania użytkowników. Dowiedz się więcej o [panelu dostępu usługi Azure AD](../user-help/active-directory-saas-access-panel-introduction.md).

### <a name="secure-device-to-service-and-service-to-service-communication"></a>Bezpieczna komunikacja usługi urządzeń i usług

**Używanie programu Azure AD do zarządzania tożsamościami urządzeń i usług zmniejsza kod, który trzeba było pisać i umożliwiają IT do zarządzania dostępem.**  Usługi i urządzenia można uzyskać tokenów z usługi Azure AD przy użyciu protokołu OAuth i użyj tych tokenów dostępu do interfejsów API sieci web. Używanie programu Azure AD można uniknąć, pisanie kodu uwierzytelniania złożonego. Ponieważ tożsamości, urządzeń i usług są przechowywane w usłudze Azure AD IT może zarządzać kluczami i odwołania w jednym miejscu zamiast konieczności Zrób to oddzielnie w aplikacji.

## <a name="benefits-of-integration"></a>Korzyści wynikające z integracji

Integracja z usługą Azure AD jest powiązana z korzyści, które nie wymagają pisania dodatkowego kodu.

### <a name="integration-with-enterprise-identity-management"></a>Integracja z usługą zarządzania tożsamościami w przedsiębiorstwie

**Pomoc aplikacji są zgodne z zasadami IT.**  Organizacje integrowanie ich systemów firmowych identity management z usługą Azure AD, gdy osoba odejdzie z organizacji, automatycznie utracą dostęp do aplikacji bez konieczności wykonywania dodatkowych czynności IT. IT można zarządzać, kto jest dostęp do Twoich aplikacji i określić, jakie zasady dostępu są wymagane — na przykład Multi-Factor authentication — zmniejszenie trzeba napisać kod, aby spełniać złożone zasady firmowe. Usługa Azure AD zapewnia administratorom dziennik inspekcji szczegółowe kto logowanie do aplikacji tak IT mogą śledzić użycie.

**Usługa Azure AD rozszerza usługi Active Directory do chmury, aby zintegrować aplikację z usługą AD.**  Wiele organizacji na całym świecie Użyj usługi Active Directory jako swojej głównej logowania i systemu zarządzania tożsamościami i wymagają ich aplikacje do pracy z usługą AD. Integracja z usługą Azure AD integruje się z usługą Active Directory swojej aplikacji.

### <a name="advanced-security-features"></a>Zaawansowane funkcje bezpieczeństwa

**Uwierzytelnianie wieloskładnikowe.**  Usługa Azure AD zapewnia natywne uwierzytelnianie wieloskładnikowe. Administratorzy IT mogą wymagać stosowania uwierzytelniania wieloskładnikowego uzyskiwanie dostępu do aplikacji, tak że nie masz kodu do obsługi tej samodzielnie. Dowiedz się więcej o [uwierzytelnianie wieloskładnikowe](https://azure.microsoft.com/documentation/services/multi-factor-authentication/).

**Nietypowe logowanie wykrywania.**  Usługa Azure AD przetwarza ponad miliard logowań na dobę, podczas korzystania z algorytmów uczenia maszynowego do wykrywania podejrzanych działań i powiadomienia administratorów IT o ewentualnych problemach. Obsługa logowania w usłudze Azure AD, że dana aplikacja otrzyma korzyści tej ochrony. Dowiedz się więcej o [wyświetlanie raportów dostępu do usługi Azure Active Directory](../active-directory-view-access-usage-reports.md).

**Dostęp warunkowy.**  Oprócz usługi uwierzytelnianie wieloskładnikowe, Administratorzy mogą wymagać od spełnienia określonych warunków, zanim użytkownicy mogą zalogować się do aplikacji. Warunki, które można ustawić obejmują zakres adresów IP urządzeń klienckich, członkostwa w określonych grup i stan urządzenia używane dla dostępu. Dowiedz się więcej o [dostępu warunkowego usługi Azure Active Directory](../active-directory-conditional-access-azure-portal.md).

### <a name="easy-development"></a>Łatwe projektowanie

**Standardowe protokoły.**  Firma Microsoft jest zaangażowana w obsłudze standardów branżowych. Platforma tożsamości firmy Microsoft obsługuje standardowe protokoły OAuth 2.0 i OpenID Connect 1.0. Dowiedz się więcej o [Microsoft protokoły uwierzytelniania platformy](active-directory-v2-protocols.md).

**Biblioteki typu open source.**  Firma Microsoft oferuje biblioteki w pełni obsługiwana "open source" dla popularnych języków i platform, w celu przyspieszenia tworzenia aplikacji. Kod źródłowy jest licencjonowane w ramach Apache w wersji 2.0 i masz swobodę rozwidlenie i brać udział w projektach. Dowiedz się więcej o [Microsoft Authentication Library (MSAL)](reference-v2-libraries.md).

### <a name="worldwide-presence-and-high-availability"></a>Obecność na całym świecie i wysokiej dostępności

**Usługa Azure AD jest wdrażana w centrach danych na całym świecie i jest zarządzane i monitorowane przez całą dobę.**  Usługa Azure AD jest systemu zarządzania tożsamościami dla Microsoft Azure i Office 365 i jest wdrażana w 28 centrach danych na całym świecie. Dane katalogu może zostać zreplikowane na co najmniej trzech centrów danych. Moduły równoważenia obciążenia globalne zapewnić użytkownikom dostęp najbliższe kopię zawierające ich dane z usługi Azure AD i automatycznie przekierowuje żądania do innych centrach danych, jeśli zostanie wykryty problem.

## <a name="next-steps"></a>Kolejne kroki

[Rozpocznij pisanie kodu](v2-overview.md#getting-started).

[Loguj użytkowników przy użyciu platformy tożsamości firmy Microsoft](authentication-scenarios.md)

