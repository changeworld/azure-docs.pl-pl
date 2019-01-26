---
title: Interfejs API programu Graph usługi Azure Active Directory | Dokumentacja firmy Microsoft
description: Omówienie i szybki start przewodnik dla interfejsu API programu Graph usługi Azure AD, która zezwala na dostęp programistyczny do usługi Azure AD za pośrednictwem punktów końcowych interfejsu API REST.
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
ms.assetid: 5471ad74-20b3-44df-a2b5-43cde2c0a045
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/24/2018
ms.author: celested
ms.reviewer: dkershaw, sureshja
ms.custom: aaddev
ms.openlocfilehash: ce1940d0b83e0713ce41d84e8d4bfeff6cb75954
ms.sourcegitcommit: 58dc0d48ab4403eb64201ff231af3ddfa8412331
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/26/2019
ms.locfileid: "55075860"
---
# <a name="azure-active-directory-graph-api"></a>Interfejs API programu Graph usługi Azure Active Directory

> [!IMPORTANT]
> Zdecydowanie zalecamy uzyskiwanie dostępu do zasobów usługi Azure Active Directory za pomocą programu [Microsoft Graph](https://developer.microsoft.com/graph/) zamiast interfejsu API funkcji Azure AD Graph. Obecnie koncentrujemy nasze działania deweloperskie na programie Microsoft Graph i nie planujemy żadnych dodatkowych rozszerzeń dla interfejsu API funkcji Azure AD Graph. Istnieje bardzo ograniczona liczba scenariuszy, w których można używać interfejsu API funkcji Azure AD Graph. Więcej informacji można znaleźć we wpisie w blogu [Microsoft Graph or the Azure AD Graph](https://dev.office.com/blogs/microsoft-graph-or-azure-ad-graph) w Centrum deweloperów pakietu Office.

Ten artykuł ma zastosowanie do interfejsu API programu Graph usługi Azure AD. Aby podobnych informacji związanych z interfejsu API Microsoft Graph, zobacz [Użyj interfejsu API programu Microsoft Graph](https://developer.microsoft.com/en-us/graph/docs/concepts/use_the_api). 

Azure Active Directory interfejsu API programu Graph zapewnia dostęp programistyczny do usługi Azure AD za pośrednictwem punktów końcowych interfejsu API REST. Aplikacje mogą używać interfejsu API usługi Azure AD Graph do wykonywania tworzenia, odczytu, aktualizacji i usuwania (CRUD) operacje na danych i obiektów katalogu. Na przykład interfejsu API usługi Azure AD Graph obsługuje następujące typowe operacje obiektu użytkownika:

* Tworzenie nowego użytkownika w katalogu
* Uzyskaj szczegółowe właściwości użytkownika, takich jak ich grupy.
* Aktualizowanie właściwości użytkownika, takie jak ich lokalizacji i numeru telefonu lub zmiana hasła
* Sprawdź członkostwo w grupie użytkownika dla dostępu opartej na rolach
* Wyłącz konto użytkownika lub całkowicie usunąć

Ponadto można wykonywać operacje podobne do innych obiektów, takich jak grupy i aplikacje. Do wywołania interfejsu API programu Graph usługi Azure AD w katalogu, aplikacja musi być zarejestrowany z usługą Azure AD. Aplikacja musi również otrzymać dostęp do interfejsu API programu Graph usługi Azure AD. Ten dostęp, zwykle odbywa się za pośrednictwem przepływu wyrażania zgody użytkownika lub administratora.

Aby rozpocząć korzystanie z usługi Azure Active Directory interfejsu API programu Graph, zobacz [Przewodnik Szybki start dotyczący interfejsu API usługi Azure AD Graph](active-directory-graph-api-quickstart.md), lub wyświetlać [interaktywne dokumentację referencyjną interfejsu API usługi Azure AD Graph](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/api-catalog).

## <a name="features"></a>Funkcje

Usługa Azure AD Graph API zapewnia następujące funkcje:

* **Punkty końcowe interfejsu API REST**: Usługa Azure AD interfejsu API programu Graph jest usługą RESTful składające się z punktami końcowymi, które są dostępne przy użyciu standardowych żądań HTTP. Usługa Azure AD Graph API obsługuje typy zawartości XML lub Javascript Object Notation (JSON) dla żądań i odpowiedzi. Aby uzyskać więcej informacji, zobacz [dokumentacja interfejsu API REST usługi Azure AD Graph](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/api-catalog).
* **Uwierzytelnianie za pomocą usługi Azure AD**: Każde żądanie do interfejsu API programu Graph usługi Azure AD musi zostać uwierzytelnione przez dołączenie JSON Web Token (JWT) w nagłówku autoryzacji żądania. Token ten jest uzyskiwany zgłaszającą żądanie do punktu końcowego tokenu usługi Azure AD i podając prawidłowe poświadczenia. Można użyć przepływu poświadczeń klienta OAuth 2.0 lub kod autoryzacji udzielić przepływu w celu uzyskania tokenu służącego do wywoływania na wykresie. Aby uzyskać więcej informacji [OAuth 2.0 w usłudze Azure AD](https://msdn.microsoft.com/library/azure/dn645545.aspx).
* **Autoryzacja oparta na rolach (RBAC)**: Grupy zabezpieczeń są używane do wykonywania funkcji RBAC w interfejsie API programu Graph usługi Azure AD. Na przykład, jeśli chcesz określić, czy użytkownik ma dostęp do określonego zasobu, aplikacja może wywołać [Sprawdź członkostwo w grupie (przechodnie)](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/functions-and-actions#checkMemberGroups) operacji, która zwraca wartość PRAWDA lub FAŁSZ.
* **Zapytanie różnicowe**: Zapytanie różnicowe umożliwia śledzenie zmian w katalogu w dwóch okresach bez konieczności wykonywania częstych zapytań do interfejsu API programu Graph usługi Azure AD. Ten typ żądania zwróci tylko zmiany wprowadzone do zakresu od poprzedniego żądania zapytania różnicowego do bieżącego żądania. Aby uzyskać więcej informacji, zobacz [zapytanie różnicowe interfejsu API usługi Azure AD Graph](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-differential-query).
* **Rozszerzenia katalogów**: Można dodać właściwości niestandardowe do obiektów katalogu, bez konieczności z magazynu danych zewnętrznych. Na przykład jeśli aplikacja wymaga właściwości Identyfikator Skype dla każdego użytkownika, możesz zarejestrować nowej właściwości w katalogu i będzie on dostępny do użytku dla każdego obiektu użytkownika. Aby uzyskać więcej informacji, zobacz [rozszerzenia schematu usługi interfejsu API programu Graph usługi Azure AD directory](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-directory-schema-extensions).
* **Zabezpieczone przez zakresy uprawnień**: Usługa Azure AD Graph API przedstawia zakresy uprawnień, które umożliwiają bezpieczny dostęp do danych usługi Azure AD przy użyciu protokołu OAuth 2.0. Obsługuje ona różne typy aplikacji klienckich, w tym:
  
  * interfejsy użytkownika, które są określone delegowanego dostępu do danych za pomocą funkcji autoryzacji przez zalogowanego użytkownika (delegowane)
  * aplikacji demona/usługi, które działają w tle bez zalogowanego użytkownika w obecności i korzystanie z kontroli dostępu opartej na rolach na zdefiniowanych przez aplikację
    
    Zarówno delegowane i uprawnienia aplikacji reprezentuje uprawnienie udostępnianych przez interfejs API programu Graph usługi Azure AD i mogą być żądane przez aplikacje klienckie za pośrednictwem funkcji uprawnień rejestracji aplikacji w [witryny Azure portal](https://portal.azure.com). [Zakresy uprawnień w usłudze Azure AD Graph API](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-permission-scopes) zawiera informacje na temat co to jest dostępne do użycia przez aplikację kliencką.

## <a name="scenarios"></a>Scenariusze

Funkcji Azure AD Graph API umożliwia obsługę wielu scenariuszy aplikacji. Najbardziej typowe są następujące scenariusze:

* **Aplikacji (pojedyncza dzierżawa) biznesowej**: W tym scenariuszu Deweloper w przedsiębiorstwie działa w przypadku organizacji, która ma subskrypcję usługi Office 365. Deweloper tworzy aplikację internetową, która wchodzi w interakcję z usługą Azure AD do wykonywania zadań takich jak przypisywanie licencji do użytkownika. To zadanie wymaga dostępu do interfejsu API programu Graph usługi Azure AD, dzięki czemu Deweloper rejestruje aplikacja jednej dzierżawy w usłudze Azure AD i konfiguruje uprawnienia odczytu i zapisu dla interfejsu API programu Graph usługi Azure AD. Następnie aplikacja jest skonfigurowana do używania jej własnych poświadczeń lub tych, które obecnie logowania użytkownika można uzyskać tokenu do wywołania interfejsu API programu Graph usługi Azure AD.
* **Oprogramowanie jako usługa aplikacji (z wieloma dzierżawami)**: W tym scenariuszu niezależny dostawca oprogramowania (ISV) jest tworzenie aplikacji hostowanej web wieloma dzierżawami, która oferuje funkcje zarządzania użytkowników przez inne organizacje korzystające z usługi Azure AD. Te funkcje wymagają dostępu do obiektów katalogu, dzięki czemu aplikacja musi wywołać interfejs API programu Graph usługi Azure AD. Deweloper rejestruje aplikację w usłudze Azure AD, konfiguruje ją do wymagają Odczyt i zapis dla interfejsu API programu Graph usługi Azure AD i następnie umożliwia dostęp zewnętrzny, dzięki czemu inne organizacje mogą wyrazić zgodę na używanie aplikacji w jego katalogu. Podczas uwierzytelniania użytkownika w innej organizacji na podstawie aplikacji po raz pierwszy, są one wyświetlane okno dialogowe zgody przy użyciu uprawnień, których żąda aplikacja. Udzielając zgody określi aplikacji te żądane uprawnienia do interfejsu API usługi Azure AD Graph w katalogu użytkownika. Aby uzyskać więcej informacji na temat platformy wyrażania zgody, zobacz [omówienie platformy wyrażania zgody](consent-framework.md).

## <a name="next-steps"></a>Kolejne kroki

Aby rozpocząć korzystanie z usługi Azure Active Directory interfejsu API programu Graph, zobacz następujące tematy:

* [Przewodnik Szybki Start usługi Azure AD interfejsu API programu Graph](active-directory-graph-api-quickstart.md)
* [Dokumentacja usługi Azure AD Graph REST](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/api-catalog)
