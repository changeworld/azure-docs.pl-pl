---
title: Jak używać interfejsu API programu Graph usługi Azure AD
description: Interfejs API programu Graph usługi Azure Active Directory (Azure AD) zapewnia dostęp programistyczny do usługi Azure AD za pośrednictwem punktów końcowych interfejsu API REST protokołu OData. Aplikacje mogą używać interfejsu API usługi Azure AD Graph do wykonywania tworzenia, odczytu, aktualizacji i usuwania (CRUD) operacje na danych i obiektów katalogu.
services: active-directory
documentationcenter: n/a
author: rwike77
manager: CelesteDG
editor: ''
tags: ''
ms.assetid: 9dc268a9-32e8-402c-a43f-02b183c295c5
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/24/2018
ms.author: ryanwi
ms.reviewer: sureshja
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: a2ae83b655bfe607b1061168649ec74f02ab4064
ms.sourcegitcommit: 9b80d1e560b02f74d2237489fa1c6eb7eca5ee10
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/01/2019
ms.locfileid: "67483176"
---
# <a name="how-to-use-the-azure-ad-graph-api"></a>Instrukcje: Korzystanie z interfejsu API Graph usługi Azure AD

Interfejs API programu Graph usługi Azure Active Directory (Azure AD) zapewnia dostęp programistyczny do usługi Azure AD za pośrednictwem punktów końcowych interfejsu API REST protokołu OData. Aplikacje mogą używać interfejsu API usługi Azure AD Graph do wykonywania tworzenia, odczytu, aktualizacji i usuwania (CRUD) operacje na danych i obiektów katalogu. Na przykład, można użyć interfejsu API usługi Azure AD Graph, aby utworzyć nowego użytkownika, wyświetlić lub zaktualizować właściwości użytkownika, zmiany hasła użytkownika, sprawdź członkostwo w grupie na potrzeby dostępu opartego na rolach wyłączyć lub usunąć użytkownika. Aby uzyskać więcej informacji na temat funkcji interfejsu API programu Graph usługi Azure AD i scenariuszy aplikacji, zobacz [interfejsu API usługi Azure AD Graph](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/api-catalog) i [wymagania wstępne programu Azure AD Graph API](https://msdn.microsoft.com/library/hh974476.aspx).

Ten artykuł ma zastosowanie do interfejsu API programu Graph usługi Azure AD. Aby podobnych informacji związanych z interfejsu API Microsoft Graph, zobacz [Użyj interfejsu API programu Microsoft Graph](https://developer.microsoft.com/graph/docs/concepts/use_the_api).

> [!IMPORTANT]
> Zdecydowanie zalecamy uzyskiwanie dostępu do zasobów usługi Azure Active Directory za pomocą programu [Microsoft Graph](https://developer.microsoft.com/graph) zamiast interfejsu API funkcji Azure AD Graph. Obecnie koncentrujemy nasze działania deweloperskie na programie Microsoft Graph i nie planujemy żadnych dodatkowych rozszerzeń dla interfejsu API funkcji Azure AD Graph. Istnieje bardzo ograniczona liczba scenariuszy, w których można używać interfejsu API funkcji Azure AD Graph. Więcej informacji można znaleźć we wpisie w blogu [Microsoft Graph or the Azure AD Graph](https://dev.office.com/blogs/microsoft-graph-or-azure-ad-graph) w Centrum deweloperów pakietu Office.

## <a name="how-to-construct-a-graph-api-url"></a>Jak skonstruować adres URL interfejsu API programu Graph

W interfejsie API programu Graph dostęp do danych katalogu i obiekty (innymi słowy, zasoby lub jednostek), wobec których chcesz wykonać operacje CRUD, można użyć adresów URL na podstawie protokołu Open Data (OData). Adresy URL używane w interfejsie API programu Graph składają się z czterech głównych części: usługi katalogu głównego, identyfikator dzierżawy, ścieżka zasobu i opcje ciągu zapytania: `https://graph.windows.net/{tenant-identifier}/{resource-path}?[query-parameters]`. Przykładowy następującego adresu URL: `https://graph.windows.net/contoso.com/groups?api-version=1.6`.

* **Usługa głównego**: W interfejsie API programu Graph usługi Azure AD, główny usługi jest zawsze https://graph.windows.net.
* **Identyfikator dzierżawy**: W tej sekcji można nazwę zweryfikowanej domeny (zarejestrowane), w powyższym przykładzie contoso.com. Może też być identyfikator obiektu dzierżawy lub "myorganization" lub "me" alias. Aby uzyskać więcej informacji, zobacz [adresowania jednostek i operacji w interfejsie API programu Graph usługi Azure AD](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-operations-overview).
* **Ścieżka zasobu**: Ta część adresu URL identyfikuje zasób jest możliwa bezpośrednia interakcja (użytkowników, grup, określonego użytkownika lub określonej grupy, itp.) W powyższym przykładzie jest najwyższego poziomu "groups" do adresu, który zestawu zasobów. Można też rozwiązać określonej jednostki, na przykład "użytkowników / {objectId}" lub "użytkowników/userPrincipalName".
* **Parametry zapytania**: Znak zapytania (?) oddziela sekcji Ścieżka zasobu z sekcji parametrów zapytania. Parametr zapytania "api-version" jest wymagana dla wszystkich żądań w interfejsie API programu Graph usługi Azure AD. Usługa Azure AD Graph API obsługuje również następujące opcje zapytania OData: **$filter**, **$orderby**, **$expand**, **$top**i **$format**. Następujące opcje zapytania nie są obecnie obsługiwane: **$count**, **$inlinecount**, i **$skip**. Aby uzyskać więcej informacji, zobacz [obsługiwane zapytania, filtry i opcje stronicowania w interfejsie API programu Graph usługi Azure AD](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-supported-queries-filters-and-paging-options).

## <a name="graph-api-versions"></a>Wersje interfejsu API programu Graph

Określasz wersji dla żądania interfejsu API programu Graph w parametrze zapytania "api-version". W wersji 1.5 lub nowszej Użyj wartości liczbowe wersji; Interfejs API-version = 1.6. W starszych wersjach należy użyć ciągu daty, która jest zgodna z formatem RRRR-MM-DD; na przykład parametru api-version = 2013-11-08. Dla funkcji w wersji zapoznawczej należy użyć ciągu "beta"; na przykład parametru api-version = beta. Aby uzyskać więcej informacji na temat różnic między wersjami interfejsu API programu Graph, zobacz [przechowywanie wersji interfejsu API programu Graph usługi Azure AD](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-versioning).

## <a name="graph-api-metadata"></a>Metadane interfejsu API programu Graph

Zwraca plik metadanych interfejsu API programu Graph usługi Azure AD, należy dodać segment "$metadata" po identyfikator dzierżawy w przykładzie adres URL dla, następujący adres URL zwraca metadane dla firmy Pokaz: `https://graph.windows.net/GraphDir1.OnMicrosoft.com/$metadata?api-version=1.6`. Możesz wprowadzić ten adres URL w pasku adresu przeglądarki sieci web, aby wyświetlić metadane. Dokument metadanych CSDL, które są zwracane w tym artykule opisano jednostek i typów złożonych, ich właściwości i funkcje i udostępnianych przez wersję interfejsu API programu Graph żądanej akcji. Pominięcie parametru api-version zwraca metadane dla najnowszej wersji.

## <a name="common-queries"></a>Typowe zapytania

[Azure AD Graph typowych zapytań do interfejsu API](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-supported-queries-filters-and-paging-options#CommonQueries) zawiera listę typowych zapytań, które mogą być używane z usługi Azure AD Graph, w tym reguł zapytań, które mogą służyć do dostępu do zasobów najwyższego poziomu w katalogu i zapytania w celu wykonywania operacji w katalogu.

Na przykład `https://graph.windows.net/contoso.com/tenantDetails?api-version=1.6` zwraca firmy informacji dla domeny contoso.com w katalogu.

Lub `https://graph.windows.net/contoso.com/users?api-version=1.6` Wyświetla listę wszystkich obiektów użytkownika w katalogu contoso.com.

## <a name="using-the-azure-ad-graph-explorer"></a>Za pomocą Eksploratora usługi Azure AD Graph
Eksplorator usługi Azure AD Graph dla interfejsu API programu Graph usługi Azure AD umożliwia wykonywanie zapytań dotyczących danych katalogu podczas tworzenia aplikacji.

Poniższy zrzut ekranu przedstawia dane wyjściowe zostanie wyświetlony, jeśli masz zamiar przejść do Eksploratora usługi Azure AD Graph, zaloguj się i wprowadź `https://graph.windows.net/GraphDir1.OnMicrosoft.com/users?api-version=1.6` do wyświetlenia wszystkich użytkowników w katalogu zalogowanego użytkownika:

![Przykładowe dane wyjściowe w Eksploratorze interfejsu API programu Graph w usłudze Azure AD](./media/active-directory-graph-api-quickstart/graph_explorer.png)

**Ładowanie Eksploratora usługi Azure AD Graph**: Aby załadować narzędzie, przejdź do [ https://graphexplorer.azurewebsites.net/ ](https://graphexplorer.azurewebsites.net/). Kliknij przycisk **logowania** i zaloguj się przy użyciu poświadczeń konta usługi Azure AD, aby uruchomić Eksploratora usługi Azure AD Graph dla Twojej dzierżawy. Jeśli uruchamiasz własnej dzierżawy usługi Azure AD Graph Explorer użytkownik lub administrator musi wyrazić zgodę podczas logowania. Jeśli masz subskrypcję usługi Office 365, automatycznie masz dzierżawę usługi Azure AD. Poświadczenia, których używasz do logowania do usługi Office 365 są w rzeczywistości kont usługi Azure AD i użyć tych poświadczeń, za pomocą Eksploratora usługi Azure AD Graph.

**Uruchom zapytanie**: Aby uruchomić zapytanie, wpisz swoje zapytanie w polu tekstowym żądania, a następnie kliknij przycisk **UZYSKAĆ** lub kliknij przycisk **wprowadź** klucza. Wyniki są wyświetlane w polu odpowiedzi. Na przykład `https://graph.windows.net/myorganization/groups?api-version=1.6` Wyświetla listę wszystkich obiektów grupy w katalogu zalogowanego użytkownika.

Należy zwrócić uwagę, następujące funkcje i ograniczenia dotyczące Eksploratora usługi Azure AD Graph:

* Funkcja automatycznego uzupełniania w zestawach zasobów. Aby wyświetlić tę funkcję, kliknij pole tekstowe żądania (gdzie wyświetlany adres URL firmy). Można wybrać zasób z listy rozwijanej.
* Historia żądań.
* Obsługuje "me" i "myorganization" adresowanie aliasów. Na przykład, można użyć `https://graph.windows.net/me?api-version=1.6` zwracać obiektu użytkownika zalogowanego użytkownika lub `https://graph.windows.net/myorganization/users?api-version=1.6` zwróci wszystkich użytkowników w katalogu zalogowanego użytkownika.
* Obsługuje pełne operacji CRUD względem własnego katalogu przy użyciu `POST`, `GET`, `PATCH` i `DELETE`.
* Sekcja nagłówków odpowiedzi. W tej sekcji mogą służyć do rozwiązywania problemów występujących podczas uruchamiania zapytań.
* Przez przeglądarkę kodu JSON odpowiedzi dzięki możliwościom rozwijanie i zwijanie.
* Brak obsługi wyświetlania lub przekazywanie miniatury zdjęcia.

## <a name="using-fiddler-to-write-to-the-directory"></a>Za pomocą narzędzia Fiddler do zapisu do katalogu

Na potrzeby tego przewodnika Szybki Start można użyć debugera w sieci Web programu Fiddler rozwiązanie polegające na wykonywanie operacji względem katalogu usługi Azure AD "write". Na przykład możesz pobrać i przekaż zdjęcie w profilu użytkownika (co nie jest możliwe za pomocą Eksploratora usługi Azure AD Graph). Aby uzyskać więcej informacji i zainstaluj program Fiddler, zobacz [ https://www.telerik.com/fiddler ](https://www.telerik.com/fiddler).

W poniższym przykładzie umożliwia debuger sieci Web programu Fiddler Utwórz nową grupę zabezpieczeń "MyTestGroup" w katalogu usługi Azure AD.

**Uzyskiwanie tokenu dostępu**: Aby uzyskać dostęp do usługi Azure AD Graph, klienci są wymagane do pomyślnego uwierzytelnienia najpierw do usługi Azure AD. Aby uzyskać więcej informacji, zobacz [scenariusze uwierzytelniania dla usługi Azure AD](authentication-scenarios.md).

**Tworzenie i uruchamianie kwerendy**: Wykonaj następujące czynności:

1. Otwórz program Fiddler debuger sieci Web i przejdź do **Composer** kartę.
2. Ponieważ chcesz utworzyć nową grupę zabezpieczeń, wybierz opcję **wpis** jako metodę HTTP, z menu rozwijanego. Aby uzyskać więcej informacji na temat operacji i uprawnień w obiekcie grupy, zobacz [grupy](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#group-entity) w ramach [dokumentacja interfejsu API REST usługi Azure AD Graph](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/api-catalog).
3. W polu obok **wpis**, wpisz następujący adres URL żądania: `https://graph.windows.net/{mytenantdomain}/groups?api-version=1.6`.
   
   > [!NOTE]
   > {Mytenantdomain} należy zastąpić nazwą domeny w katalogu usługi Azure AD.

4. W polu bezpośrednio pod rozwijanego wpis należy wpisać następujący nagłówek HTTP:
   
    ```
   Host: graph.windows.net
   Authorization: Bearer <your access token>
   Content-Type: application/json
   ```
   
   > [!NOTE]
   > Zastąp swoje &lt;tokenu dostępu&gt; przy użyciu tokenu dostępu dla katalogu usługi Azure AD.

5. W **treść żądania** wpisz następujący kod JSON:
   
    ```
        {
            "displayName":"MyTestGroup",
            "mailNickname":"MyTestGroup",
            "mailEnabled":"false",
            "securityEnabled": true
        }
   ```
   
    Aby uzyskać więcej informacji na temat tworzenia grup, zobacz [Utwórz grupę](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/groups-operations#CreateGroup).

Więcej informacji na temat usługi Azure AD, jednostek i typy, które są udostępniane przez usługę Graph i informacji na temat operacji, które można wykonywać na nich za pomocą programu Graph, zobacz [dokumentacja interfejsu API REST usługi Azure AD Graph](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/api-catalog).

## <a name="next-steps"></a>Kolejne kroki

* Dowiedz się więcej o [interfejsu API usługi Azure AD Graph](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/api-catalog)
* Dowiedz się więcej o [zakresów uprawnień interfejsu API programu Graph usługi Azure AD](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-permission-scopes)
