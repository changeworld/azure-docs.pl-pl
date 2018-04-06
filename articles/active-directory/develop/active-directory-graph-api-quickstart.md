---
title: Szybki Start dla usługi Azure AD Graph API | Dokumentacja firmy Microsoft
description: Azure Active Directory interfejsu API programu Graph zapewnia dostęp programistyczny do usługi Azure AD za pomocą interfejsu API REST OData punktów końcowych. Aplikacje mogą używać interfejsu API usługi Azure AD Graph do wykonania tworzenia, odczytu, aktualizacji i usuwania operacji (CRUD) w katalogu danych i obiektów.
services: active-directory
documentationcenter: n/a
author: mtillman
manager: mtillman
editor: ''
tags: ''
ms.assetid: 9dc268a9-32e8-402c-a43f-02b183c295c5
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/02/2018
ms.author: mtillman
ms.custom: aaddev
ms.openlocfilehash: d195d808e07b872c11379f13b6e89794da39f70e
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/05/2018
---
# <a name="quickstart-for-the-azure-ad-graph-api"></a>Szybki Start dla usługi Azure AD Graph API
Interfejsu API programu Graph usługi Azure Active Directory (AD) zapewnia dostęp programistyczny do usługi Azure AD za pomocą interfejsu API REST OData punktów końcowych. Aplikacje mogą używać interfejsu API usługi Azure AD Graph do wykonania tworzenia, odczytu, aktualizacji i usuwania operacji (CRUD) w katalogu danych i obiektów. Na przykład można użyć interfejsu API Azure AD Graph, aby utworzyć nowego użytkownika, Wyświetl lub zaktualizuj właściwości użytkownika, zmiany hasła użytkownika, sprawdź członkostwo grupy dostępu oparte na rolach, wyłączyć lub usunąć użytkownika. Aby uzyskać więcej informacji o funkcji Azure AD Graph API i scenariuszy aplikacji, zobacz [interfejsu API usługi Azure AD Graph](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/api-catalog) i [wymagania wstępne programu Azure AD Graph API](https://msdn.microsoft.com/library/hh974476.aspx). 

> [!IMPORTANT]
> Zdecydowanie zalecamy uzyskiwanie dostępu do zasobów usługi Azure Active Directory za pomocą programu [Microsoft Graph](https://developer.microsoft.com/graph) zamiast interfejsu API funkcji Azure AD Graph. Obecnie koncentrujemy nasze działania deweloperskie na programie Microsoft Graph i nie planujemy żadnych dodatkowych rozszerzeń dla interfejsu API funkcji Azure AD Graph. Istnieje bardzo ograniczona liczba scenariuszy, w których można używać interfejsu API funkcji Azure AD Graph. Więcej informacji można znaleźć we wpisie w blogu [Microsoft Graph or the Azure AD Graph](https://dev.office.com/blogs/microsoft-graph-or-azure-ad-graph) w Centrum deweloperów pakietu Office.
> 
> 

## <a name="how-to-construct-a-graph-api-url"></a>Jak utworzyć adres URL interfejsu API Graph
W interfejsu API programu Graph dostęp do katalogu danych i obiektów (innymi słowy, zasobów lub jednostek), z którymi ma do wykonywania operacji CRUD, można użyć adresy URL na podstawie protokołu Open Data (OData). Adresy URL używane w interfejsu API programu Graph składają się z czterech głównych części: Usługa głównego, identyfikator dzierżawy, ścieżka zasobu i opcji ciągu zapytania: `https://graph.windows.net/{tenant-identifier}/{resource-path}?[query-parameters]`. Przykładowy następujący adres URL: `https://graph.windows.net/contoso.com/groups?api-version=1.6`.

* **Katalogu głównego usługi**: W interfejsie API Graph usługi Azure AD, katalog główny usługi jest zawsze https://graph.windows.net.
* **Identyfikator dzierżawy**: w tej sekcji można nazwę zweryfikowanej domeny (zarejestrowanym), w tym przykładzie contoso.com. Może również być identyfikator obiektu dzierżawcy lub "myorganization" lub "me" alias. Aby uzyskać więcej informacji, zobacz [adresowania jednostki i operacji w usłudze Azure AD Graph API](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-operations-overview).
* **Ścieżka zasobu**: Ta część adresu URL identyfikuje zasobu przetwarzanie (użytkowników, grup, określonego użytkownika lub określonej grupy, itp.) W powyższym przykładzie jest najwyższego poziomu "grupy" adres konfigurować zasób. Można też rozwiązać, określonej jednostki, na przykład "użytkowników / {objectId}" lub "użytkowników/userPrincipalName".
* **Parametry zapytania**: znak zapytania (?) oddziela sekcji Ścieżka zasobu z sekcji Parametry zapytania. Parametr zapytania "api-version" jest wymagany dla wszystkich żądań w interfejsie API Azure AD Graph. Azure AD Graph API obsługuje również poniższe opcje zapytania OData: **$filter**, **$orderby**, **rozwiń $**, **$top**i **$format**. Następujące opcje zapytania nie są obecnie obsługiwane: **$count**, **$inlinecount**, i **$skip**. Aby uzyskać więcej informacji, zobacz [obsługiwane zapytania, filtrów i stronicowania opcji w interfejsie API Azure AD Graph](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-supported-queries-filters-and-paging-options).

## <a name="graph-api-versions"></a>Wersje interfejsu API programu Graph
Należy określić wersję dla żądania interfejsu API programu Graph w parametru zapytania "api-version". W wersji 1.5 lub nowszym Użyj wartości liczbowe wersji; Interfejs API-version = 1.6. W przypadku wcześniejszych wersji Użyj ciągu daty zgodnego z formatem RRRR-MM-DD; na przykład interfejsu api-version = 2013-11-08. Dla funkcji w wersji zapoznawczej Użyj ciągu "beta"; na przykład interfejsu api-version = beta. Aby uzyskać więcej informacji na temat różnic między wersjami interfejsu API programu Graph, zobacz [wersji interfejsu API Azure AD Graph](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-versioning).

## <a name="graph-api-metadata"></a>Metadanych interfejsu API programu Graph
Aby zwracać plik metadanych interfejsu API Azure AD Graph, dodanie segmentu "$metadata" po identyfikatora dzierżawy w przykładzie adres URL dla, następujący adres URL zwraca metadane dla firmy Pokaz: `https://graph.windows.net/GraphDir1.OnMicrosoft.com/$metadata?api-version=1.6`. Możesz wprowadzić ten adres URL na pasku adresu przeglądarki sieci web, aby wyświetlić metadanych. Dokument metadanych CSDL zwrócił opisuje jednostek i typów złożonych, ich właściwości oraz funkcje i udostępniane przez wersję interfejsu API programu Graph żądanej akcji. Pominięcie parametru api-version zwraca metadane dla najnowszej wersji.

## <a name="common-queries"></a>Typowe zapytania
[Azure AD Graph API typowych kwerend](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-supported-queries-filters-and-paging-options#CommonQueries) wymieniono typowe zapytania, które mogą być używane z usługi Azure AD Graph, łącznie z zapytania, które mogą służyć do dostępu do zasobów najwyższego poziomu w katalogu i zapytań do wykonywania operacji w katalogu.

Na przykład `https://graph.windows.net/contoso.com/tenantDetails?api-version=1.6` zwraca firmy informacji o katalogu contoso.com.

Lub `https://graph.windows.net/contoso.com/users?api-version=1.6` Wyświetla listę wszystkich obiektów użytkownika w domenie contoso.com katalogu.

## <a name="using-the-azure-ad-graph-explorer"></a>Za pomocą Eksploratora usługi Azure AD Graph
Explorer Azure AD Graph dla interfejsu API programu Azure AD Graph służy do wykonywania zapytań o dane katalogu podczas tworzenia aplikacji.

Poniższy zrzut ekranu jest zostanie wyświetlony, jeśli masz zamiar przejdź do Eksploratora usługi Azure AD Graph, zaloguj się, a następnie wprowadź dane wyjściowe `https://graph.windows.net/GraphDir1.OnMicrosoft.com/users?api-version=1.6` do wyświetlenia wszystkich użytkowników w katalogu zalogowanego użytkownika:

![Azure AD graph api explorer](./media/active-directory-graph-api-quickstart/graph_explorer.png)

**Ładowanie Eksploratora Azure AD Graph**: Aby załadować narzędzie, przejdź do [ https://graphexplorer.azurewebsites.net/ ](https://graphexplorer.azurewebsites.net/). Kliknij przycisk **logowania** i zaloguj się przy użyciu poświadczeń konta usługi Azure AD w celu uruchomienia Eksploratora Azure AD Graph dzierżawy. Jeśli wykonywane Explorer Azure AD Graph własną dzierżawę, użytkownik lub administrator musi wyrazić zgodę podczas logowania. Jeśli masz subskrypcję usługi Office 365, automatycznie jest dzierżawa usługi Azure AD. Poświadczenia, których używasz do logowania do usługi Office 365 są w rzeczywistości konta usługi Azure AD i te poświadczenia można użyć z programem Azure AD Graph Explorer.

**Uruchom zapytanie**: Aby uruchomić zapytanie, wpisz zapytanie w polu tekstowym żądania, a następnie kliknij przycisk **UZYSKAĆ** lub kliknij przycisk **wprowadź** klucza. Wyniki są wyświetlane w polu odpowiedzi. Na przykład `https://graph.windows.net/myorganization/groups?api-version=1.6` Wyświetla listę wszystkich obiektów grupy w katalogu zalogowanego użytkownika.

Należy zwrócić uwagę, następujące funkcje i ograniczenia dotyczące programu Azure AD Graph Explorer:

* Ustawia funkcji AutoComplete w zasobie. Aby wyświetlić tę funkcję, kliknij w polu tekstowym żądania (gdzie adres URL firmy jest wyświetlana). Możesz wybrać zestaw z listy rozwijanej zasobów.
* Historia żądań.
* Obsługuje "me" i "myorganization" adresowania aliasów. Na przykład można użyć `https://graph.windows.net/me?api-version=1.6` do zwrócenia obiektu użytkownika zalogowanego użytkownika lub `https://graph.windows.net/myorganization/users?api-version=1.6` do zwrócenia wszystkich użytkowników w katalogu zalogowanego użytkownika.
* Obsługuje pełne operacje CRUD przed przy użyciu własnego katalogu `POST`, `GET`, `PATCH` i `DELETE`.
* Sekcja nagłówków odpowiedzi. W tej sekcji mogą służyć do rozwiązywania problemów występujących podczas uruchamiania zapytań.
* Podgląd JSON odpowiedzi z możliwości rozwijanie i zwijanie.
* Brak obsługi do wyświetlania lub przekazywania miniatury zdjęcia.

## <a name="using-fiddler-to-write-to-the-directory"></a>Przy użyciu programu Fiddler do zapisu do katalogu
Na potrzeby tego przewodnika Szybki Start można użyć debugera sieci Web narzędzia Fiddler do rozwiązań wykonywanie operacji względem katalogu usługi Azure AD "write". Na przykład możesz pobrać i przekaż zdjęcie profilu użytkownika, (co nie jest możliwe z programem Azure AD Graph Explorer). Aby uzyskać więcej informacji i zainstalować narzędzia Fiddler, zobacz [ http://www.telerik.com/fiddler ](http://www.telerik.com/fiddler).

W poniższym przykładzie używasz narzędzia Fiddler sieci Web debugera do tworzenia nowej grupy zabezpieczeń "MyTestGroup" w katalogu usługi Azure AD.

**Uzyskaj token dostępu**: Aby uzyskać dostęp do usługi Azure AD Graph, klienci są wymagane do pomyślnego uwierzytelnienia najpierw do usługi Azure AD. Aby uzyskać więcej informacji, zobacz [scenariusze uwierzytelniania dla usługi Azure AD](active-directory-authentication-scenarios.md).

**Napisz, a następnie uruchomić kwerendę**: wykonaj następujące czynności:

1. Otwórz Fiddler debuger sieci Web i przejdź do **Composer** kartę.
2. Ponieważ chcesz utworzyć nową grupę zabezpieczeń, wybierz opcję **Post** metodą HTTP z menu rozwijanego. Aby uzyskać więcej informacji na temat operacji i uprawnień w obiekcie grupy, zobacz [grupy](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#group-entity) w [dokumentacja interfejsu API REST usługi Azure AD Graph](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/api-catalog).
3. W polu obok **Post**, wpisz następujący adres URL żądania: `https://graph.windows.net/{mytenantdomain}/groups?api-version=1.6`.
   
   > [!NOTE]
   > {Mytenantdomain} musi zastąpić nazwę domeny katalogu usługi Azure AD.
   > 
   > 
4. W polu bezpośrednio pod rozwijanego Post wpisz następujący nagłówek HTTP:
   
    ```
   Host: graph.windows.net
   Authorization: Bearer <your access token>
   Content-Type: application/json
   ```
   
   > [!NOTE]
   > SUBSTITUTE Twojej &lt;tokenu dostępu&gt; przy użyciu tokenu dostępu dla katalogu usługi Azure AD.
   > 
   > 
5. W **treść żądania** wpisz następujący kod JSON:
   
    ```
        {
            "displayName":"MyTestGroup",
            "mailNickname":"MyTestGroup",
            "mailEnabled":"false",
            "securityEnabled": true
        }
   ```
   
    Aby uzyskać więcej informacji o tworzeniu grup, zobacz [Utwórz grupę](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/groups-operations#CreateGroup).

Więcej informacji na temat usługi Azure AD jednostek i typy, które są udostępniane przez wykres i informacje o operacji, które można wykonywać na nich za pomocą wykresu, zobacz [dokumentacja interfejsu API REST usługi Azure AD Graph](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/api-catalog).

## <a name="next-steps"></a>Kolejne kroki
* Dowiedz się więcej o [interfejsu API usługi Azure AD Graph](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/api-catalog)
* Dowiedz się więcej o [zakresy uprawnień interfejsu API Graph usługi Azure AD](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-permission-scopes)

