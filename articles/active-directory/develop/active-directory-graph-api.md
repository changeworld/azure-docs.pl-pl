---
title: Interfejs API Graph usługi Azure Active Directory | Dokumentacja firmy Microsoft
description: Omówienie i szybkiego startu Podręcznik interfejsu API Azure AD Graph, dzięki czemu programowy dostęp do usługi Azure AD za pomocą punktów końcowych interfejsu API REST.
services: active-directory
documentationcenter: ''
author: mtillman
manager: mtillman
editor: mbaldwin
ms.assetid: 5471ad74-20b3-44df-a2b5-43cde2c0a045
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/02/2018
ms.author: mtillman
ms.custom: aaddev
ms.openlocfilehash: 00ca193b281f0a8d24935dd8673cae4e05b4d167
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/08/2018
---
# <a name="azure-active-directory-graph-api"></a>Interfejs API programu Graph usługi Azure Active Directory
> [!IMPORTANT]
> Zdecydowanie zalecamy uzyskiwanie dostępu do zasobów usługi Azure Active Directory za pomocą programu [Microsoft Graph](https://graph.microsoft.io/) zamiast interfejsu API funkcji Azure AD Graph. Obecnie koncentrujemy nasze działania deweloperskie na programie Microsoft Graph i nie planujemy żadnych dodatkowych rozszerzeń dla interfejsu API funkcji Azure AD Graph. Istnieje bardzo ograniczona liczba scenariuszy, w których można używać interfejsu API funkcji Azure AD Graph. Więcej informacji można znaleźć we wpisie w blogu [Microsoft Graph or the Azure AD Graph](https://dev.office.com/blogs/microsoft-graph-or-azure-ad-graph) w Centrum deweloperów pakietu Office.
> 
> 

Azure Active Directory interfejsu API programu Graph zapewnia dostęp programistyczny do usługi Azure AD za pomocą punktów końcowych interfejsu API REST. Aplikacje mogą używać interfejsu API usługi Azure AD Graph do wykonania tworzenia, odczytu, aktualizacji i usuwania operacji (CRUD) w katalogu danych i obiektów. Na przykład Azure AD Graph API obsługuje następujące typowe operacje dla obiekt użytkownika:

* Tworzenie nowego użytkownika w katalogu
* Pobierz właściwości szczegółowe użytkownika, takie jak ich grup
* Zaktualizuj właściwości użytkownika, takie jak ich lokalizacji i numer telefonu lub zmień ich hasła
* Sprawdź członkostwo w grupie użytkownika dla dostępu opartej na rolach
* Wyłącz konto użytkownika lub usuń go całkowicie

Ponadto mogą wykonywać operacje podobne na inne obiekty, takie jak grupy i aplikacje. Aby wywołać interfejsu API usługi Azure AD Graph dla katalogu, aplikacja musi być zarejestrowany z usługą Azure AD. Aplikacja musi również otrzymać dostęp do interfejsu API Azure AD Graph. Dostęp zwykle odbywa się za pośrednictwem przepływu zgody użytkownika lub administratora.

Aby rozpocząć korzystanie z usługi Azure Active Directory interfejsu API programu Graph, zobacz [Przewodnik Szybki Start interfejsu API Graph usługi Azure AD](active-directory-graph-api-quickstart.md), lub wyświetlić [interakcyjne dokumentacji interfejsu API usługi Azure AD Graph](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/api-catalog).

## <a name="features"></a>Funkcje
Azure AD Graph API oferuje następujące funkcje:

* **Punkty końcowe interfejsu API REST**: interfejs API programu Graph usługi AD platformy Azure jest usługą RESTful, składającej się z punktów końcowych, które są dostępne przy użyciu standardowych żądań HTTP. Azure AD Graph API obsługuje XML lub Javascript Object Notation (JSON) typy zawartości dla żądań i odpowiedzi. Aby uzyskać więcej informacji, zobacz [dokumentacja interfejsu API REST usługi Azure AD Graph](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/api-catalog).
* **Uwierzytelnianie za pomocą usługi Azure AD**: każde żądanie do interfejsu API Azure AD Graph musi zostać uwierzytelniony przez dołączenie JSON Web Token (JWT) w nagłówku autoryzacji żądania. Token ten jest uzyskaną przez przesyłania do punktu końcowego tokenu usługi Azure AD i podanie prawidłowych poświadczeń. Można użyć przepływ poświadczeń klienta OAuth 2.0 lub kod autoryzacji Udziel przepływu można uzyskać tokenu umożliwiającego wywołanie wykresu. Aby uzyskać więcej informacji [OAuth 2.0 w usłudze Azure AD](https://msdn.microsoft.com/library/azure/dn645545.aspx).
* **Autoryzacji opartej na rolach (RBAC)**: grupy zabezpieczeń są używane do wykonywania RBAC w interfejsie API Azure AD Graph. Na przykład, jeśli chcesz określić, czy użytkownik ma dostęp do określonych zasobów, aplikacja może wywołać [sprawdzić członkostwa w grupie (przechodnie)](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/functions-and-actions#checkMemberGroups) operacja, która zwraca wartość PRAWDA lub FAŁSZ.
* **Zapytanie różnicowej**: różnicowej zapytania umożliwia śledzenie zmian w katalogu między dwoma okresów bez konieczności częste zapytań do interfejsu API Azure AD Graph. Ten typ żądania zwróci tylko zmiany wprowadzone od poprzedniego żądania różnicowej zapytania i bieżącego żądania. Aby uzyskać więcej informacji, zobacz [Azure AD Graph API różnicowej zapytania](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-differential-query).
* **Rozszerzenia katalogów**: jednostki organizacyjnej można dodać właściwości niestandardowe do obiektów katalogu bez konieczności magazynu danych zewnętrznych. Na przykład jeśli aplikacja wymaga właściwości Identyfikatora użytkownika Skype dla każdego użytkownika, możesz zarejestrować nową właściwość w katalogu i będzie on dostępny do użycia dla każdego obiektu użytkownika. Aby uzyskać więcej informacji, zobacz [Azure AD Graph interfejsu API katalogu schematu rozszerzenia](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-directory-schema-extensions).
* **Zabezpieczone przez zakresy uprawnień**: Azure AD Graph API przedstawia zakresy uprawnień, które umożliwiają bezpieczny dostęp do danych usługi Azure AD przy użyciu protokołu OAuth 2.0. Obsługuje ona różnych typów aplikacji klienta, w tym:
  
  * interfejsy użytkownika, które podano delegowany dostęp do danych za pomocą autoryzacji przez zalogowanego użytkownika (delegowane)
  * aplikacje usługi/demon, które działają w tle, użytkownik nie jest zalogowany jest obecny i za pomocą kontroli dostępu opartej na rolach zdefiniowanym przez aplikację
    
    Oba delegowane i uprawnienia aplikacji reprezentują uprawnienia udostępniany przez interfejs API Azure AD Graph i może być wysłane przez aplikacje klienckie za pośrednictwem funkcji uprawnień rejestracji aplikacji w [portalu Azure](https://portal.azure.com). [Zakresy uprawnień interfejsu API Graph usługi Azure AD](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-permission-scopes) zawiera informacje na temat co to jest dostępne do użycia przez aplikację klienta.

## <a name="scenarios"></a>Scenariusze
Azure AD Graph API umożliwia wiele scenariuszy, w aplikacji. Najczęściej używane są następujące scenariusze:

* **Aplikacji biznesowej (pojedynczej dzierżawy)**: W tym scenariuszu developer enterprise działa w przypadku organizacji, która ma subskrypcji usługi Office 365. Deweloper tworzenia aplikacji sieci web, która współdziała z usługą Azure AD umożliwia wykonywanie zadań takich przypisywania licencji do użytkownika. To zadanie wymaga dostępu do interfejsu API Azure AD Graph, dzięki czemu Deweloper rejestruje aplikacji pojedynczej dzierżawy w usłudze Azure AD i konfiguruje uprawnienia odczytu i zapisu dla interfejsu API Azure AD Graph. Następnie aplikacja jest skonfigurowana na potrzeby uzyskania tokenu umożliwiającego wywołanie interfejsu API usługi Azure AD Graph własne poświadczenia lub te, które obecnie logowania użytkownika.
* **Aplikacja usługi (wielodostępnej) oprogramowania jako**: W tym scenariuszu niezależnego dostawcy oprogramowania (ISV) jest tworzenie aplikacji hostowanej wielodostępnej sieci web, która zawiera funkcje zarządzania użytkownika do innych organizacji korzystających z usługi Azure AD. Funkcje te wymagają dostępu do obiektów katalogu, a więc aplikacja musi wywołać interfejs API Azure AD Graph. Deweloper rejestruje aplikacji w usłudze Azure AD, skonfiguruje je do wymagają odczytu i zapisu uprawnienia dla interfejsu API usługi Azure AD Graph i następnie włącza dostępu zewnętrznego, dzięki czemu inne organizacje mogą wyrazić zgodę korzystanie z aplikacji w ich katalogu. Podczas uwierzytelniania użytkownika w innej organizacji do aplikacji po raz pierwszy, są wyświetlane okno dialogowe zgody użytkownika z uprawnieniami, który żąda aplikacji.  Udzielając zgody określi aplikacji te wymagane uprawnienia do interfejsu API Azure AD Graph w katalogu użytkownika. Aby uzyskać więcej informacji w ramach zgody, zobacz [omówienie Framework zgody](active-directory-integrating-applications.md).

## <a name="see-also"></a>Zobacz też
[Przewodnik Szybki Start Azure interfejs API Graph usługi AD](active-directory-graph-api-quickstart.md)

[Dokumentację platformy Azure AD Graph REST](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/api-catalog)

[Przewodnik dewelopera usługi Azure Active Directory](active-directory-developers-guide.md)

