---
title: Interfejs API wykresu usługi Azure Active Directory | Dokumenty firmy Microsoft
description: Omówienie i przewodnik szybki start dla interfejsu API programu Azure AD Graph, który umożliwia programowy dostęp do usługi Azure AD za pośrednictwem punktów końcowych interfejsu API REST.
services: active-directory
author: rwike77
manager: CelesteDG
ms.assetid: 5471ad74-20b3-44df-a2b5-43cde2c0a045
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 11/26/2019
ms.author: ryanwi
ms.reviewer: dkershaw, sureshja
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: ef042b9eb625a0d0de5d5dcb883b823c3a499aa9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76698921"
---
# <a name="azure-active-directory-graph-api"></a>Interfejs API programu Graph usługi Azure Active Directory

> [!IMPORTANT]
> Zdecydowanie zaleca się, aby uzyskać dostęp do zasobów usługi Azure Active Directory (Azure AD) za pomocą [programu Microsoft Graph](https://developer.microsoft.com/graph) zamiast interfejsu API programu Azure AD Graph. Obecnie koncentrujemy nasze działania deweloperskie na programie Microsoft Graph i nie planujemy żadnych dodatkowych rozszerzeń dla interfejsu API funkcji Azure AD Graph. Istnieje bardzo ograniczona liczba scenariuszy, dla których interfejs API programu Azure AD Graph może być nadal odpowiedni; Aby uzyskać więcej informacji, zobacz wpis w blogu [programu Microsoft Graph lub microsoft AD Graph](https://dev.office.com/blogs/microsoft-graph-or-azure-ad-graph) i [migruj aplikacje programu Azure AD Graph do programu Microsoft Graph](https://docs.microsoft.com/graph/migrate-azure-ad-graph-overview).

Ten artykuł dotyczy interfejsu API programu Azure AD Graph. Aby uzyskać podobne informacje dotyczące interfejsu API programu Microsoft Graph, zobacz [Korzystanie z interfejsu API programu Microsoft Graph](https://docs.microsoft.com/graph/use-the-api).

Interfejs API usługi Azure Active Directory Graph zapewnia dostęp programistyczny do usługi Azure AD za pomocą punktów końcowych interfejsu API REST. Aplikacje mogą używać interfejsu API programu Azure AD Graph do wykonywania operacji tworzenia, odczytu, aktualizacji i usuwania (CRUD) na danych i obiektach katalogu. Na przykład interfejs API programu Azure AD Graph obsługuje następujące typowe operacje dla obiektu użytkownika:

* Tworzenie nowego użytkownika w katalogu
* Uzyskaj szczegółowe właściwości użytkownika, takie jak ich grupy
* Aktualizowanie właściwości użytkownika, takich jak jego lokalizacja i numer telefonu, lub zmienianie hasła
* Sprawdzanie członkostwa w grupie użytkownika pod kątem dostępu opartego na rolach
* Wyłączanie konta użytkownika lub całkowite usunięcie go

Ponadto można wykonywać podobne operacje na innych obiektach, takich jak grupy i aplikacje. Aby wywołać interfejs API programu Azure AD Graph w katalogu, aplikacja musi być zarejestrowana w usłudze Azure AD. Aplikacja musi również mieć dostęp do interfejsu API programu Azure AD Graph. Ten dostęp jest zwykle osiągany za pośrednictwem przepływu zgody użytkownika lub administratora.

Aby rozpocząć korzystanie z interfejsu API wykresu usługi Azure Active Directory, zobacz [przewodnik szybki start interfejsu API programu Azure AD Graph](active-directory-graph-api-quickstart.md)lub zapoznaj się z [dokumentacją referencyjną interaktywnego interfejsu API programu Azure AD Graph.](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/api-catalog)

## <a name="features"></a>Funkcje

Interfejs API programu Azure AD Graph zawiera następujące funkcje:

* **Punkty końcowe interfejsu API REST:** Interfejs API programu Azure AD Graph to usługa RESTful składająca się z punktów końcowych, do których są dostępne przy użyciu standardowych żądań HTTP. Interfejs API programu Azure AD Graph obsługuje typy zawartości XML lub Javascript Object Notation (JSON) dla żądań i odpowiedzi. Aby uzyskać więcej informacji, zobacz [odwołanie interfejsu API rest programu Azure AD Graph](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/api-catalog).
* **Uwierzytelnianie za pomocą usługi Azure AD:** Każde żądanie do interfejsu API programu Azure AD Graph musi być uwierzytelnione przez dołączenie tokenu JSON Web Token (JWT) w nagłówku autoryzacji żądania. Ten token jest pobierany przez złożenie żądania do punktu końcowego tokenu usługi Azure AD i podanie prawidłowych poświadczeń. Przepływ poświadczeń klienta OAuth 2.0 lub przepływ udzielania kodu autoryzacji można uzyskać token do wywołania wykresu. Aby uzyskać więcej informacji, [OAuth 2.0 w usłudze Azure AD](https://msdn.microsoft.com/library/azure/dn645545.aspx).
* **Autoryzacja oparta na rolach (RBAC)**: Grupy zabezpieczeń są używane do wykonywania funkcji RBAC w interfejsie API programu Azure AD Graph. Na przykład jeśli chcesz ustalić, czy użytkownik ma dostęp do określonego zasobu, aplikacja może wywołać [check group membership (przechodnie)](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/functions-and-actions#checkMemberGroups) operacji, która zwraca true lub false.
* **Kwerenda różnicowa:** Kwerenda różnicowa umożliwia śledzenie zmian w katalogu między dwoma okresami czasu bez konieczności częstego wykonywania zapytań do interfejsu API programu Azure AD Graph. Ten typ żądania zwróci tylko zmiany wprowadzone między poprzednim żądaniem kwerendy różnicowej a bieżącym żądaniem. Aby uzyskać więcej informacji, zobacz [kwerenda różnicowa interfejsu API programu Azure AD Graph](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-differential-query).
* **Rozszerzenia katalogów:** Można dodawać właściwości niestandardowe do obiektów katalogu bez konieczności przechowywania danych zewnętrznych. Na przykład jeśli aplikacja wymaga właściwości Skype ID dla każdego użytkownika, można zarejestrować nową właściwość w katalogu i będzie ona dostępna do użycia na każdym obiekcie użytkownika. Aby uzyskać więcej informacji, zobacz [rozszerzenia schematu katalogów interfejsu API programu Azure AD Graph](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-directory-schema-extensions).
* **Zabezpieczone przez zakresy uprawnień:** Interfejs API programu Azure AD Graph udostępnia zakresy uprawnień, które umożliwiają bezpieczny dostęp do danych usługi Azure AD przy użyciu OAuth 2.0. Obsługuje różne typy aplikacji klienckich, w tym:
  
  * interfejsy użytkownika, które mają delegowany dostęp do danych za pośrednictwem autoryzacji od zalogowany użytkownik (delegowane)
  * aplikacje usługi/demonów, które działają w tle bez obecności zalogowanego użytkownika i korzystają z kontroli dostępu opartej na rolach zdefiniowanej przez aplikację
    
    Uprawnienia delegowane i uprawnienia aplikacji reprezentują uprawnienia udostępniane przez interfejs API programu Azure AD Graph i mogą być wymagane przez aplikacje klienckie za pośrednictwem funkcji uprawnień rejestracji aplikacji w [witrynie Azure portal.](https://portal.azure.com) [Zakresy uprawnień interfejsu API programu Azure AD Graph](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-permission-scopes) zawierają informacje o tym, co jest dostępne do użycia przez aplikację kliencką.

## <a name="scenarios"></a>Scenariusze

Interfejs API programu Azure AD Graph umożliwia wiele scenariuszy aplikacji. Następujące scenariusze są najczęściej:

* **Aplikacja wiersza pracy (pojedyncza dzierżawa):** W tym scenariuszu deweloper dla przedsiębiorstwa pracuje dla organizacji, która ma subskrypcję usługi Office 365. Deweloper buduje aplikację sieci web, która współdziała z usługą Azure AD do wykonywania zadań, takich jak przypisywanie licencji do użytkownika. To zadanie wymaga dostępu do interfejsu API programu Azure AD Graph, więc deweloper rejestruje aplikację pojedynczej dzierżawy w usłudze Azure AD i konfiguruje uprawnienia do odczytu i zapisu dla interfejsu API programu Azure AD Graph. Następnie aplikacja jest skonfigurowana do używania własnych poświadczeń lub tych aktualnie logowania użytkownika, aby uzyskać token do wywołania interfejsu API programu Azure AD Graph.
* **Oprogramowanie jako aplikacja usługi (multi-dzierżawca)**: W tym scenariuszu niezależny dostawca oprogramowania (ISV) opracowuje hostowane wielodostępne aplikacje sieci web, która zapewnia funkcje zarządzania użytkownikami dla innych organizacji korzystających z usługi Azure AD. Te funkcje wymagają dostępu do obiektów katalogu, więc aplikacja musi wywołać interfejs API programu Azure AD Graph. Deweloper rejestruje aplikację w usłudze Azure AD, konfiguruje ją tak, aby wymagał uprawnień do odczytu i zapisu dla interfejsu API programu Azure AD Graph, a następnie włącza dostęp zewnętrzny, aby inne organizacje mogły wyrazić zgodę na korzystanie z aplikacji w ich katalogu. Gdy użytkownik w innej organizacji uwierzytelnia się w aplikacji po raz pierwszy, są one wyświetlane okno dialogowe zgody z uprawnieniami, których żąda aplikacja. Udzielanie zgody następnie przyzna aplikacji te żądane uprawnienia do interfejsu API programu Azure AD Graph w katalogu użytkownika. Aby uzyskać więcej informacji na temat ram zgody, zobacz [Omówienie ram zgody](consent-framework.md).

## <a name="next-steps"></a>Następne kroki

Aby rozpocząć korzystanie z interfejsu API wykresu usługi Azure Active Directory, zobacz następujące tematy:

* [Przewodnik szybki start interfejsu API programu Azure AD Graph](active-directory-graph-api-quickstart.md)
* [Dokumentacja REST programu Azure AD Graph](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/api-catalog)
