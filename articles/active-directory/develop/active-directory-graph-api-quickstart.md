---
title: Jak korzystać z interfejsu API programu Azure AD Graph
description: Interfejs API programu Azure Active Directory (Azure AD) Graph zapewnia programowy dostęp do usługi Azure AD za pośrednictwem punktów końcowych interfejsu API OData REST. Aplikacje mogą używać interfejsu API programu Azure AD Graph do wykonywania operacji tworzenia, odczytu, aktualizacji i usuwania (CRUD) na danych i obiektach katalogu.
services: active-directory
author: rwike77
manager: CelesteDG
ms.assetid: 9dc268a9-32e8-402c-a43f-02b183c295c5
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 08/28/2019
ms.author: ryanwi
ms.reviewer: sureshja
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: 701aadcbfe03c82f6a4c341ef9698f8a1bf1a347
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/01/2020
ms.locfileid: "80476586"
---
# <a name="how-to-use-the-azure-ad-graph-api"></a>Jak: Korzystanie z interfejsu API programu Azure AD Graph

> [!IMPORTANT]
> Zdecydowanie zaleca się, aby uzyskać dostęp do zasobów usługi Azure Active Directory (Azure AD) za pomocą [programu Microsoft Graph](https://developer.microsoft.com/graph) zamiast interfejsu API programu Azure AD Graph. Obecnie koncentrujemy nasze działania deweloperskie na programie Microsoft Graph i nie planujemy żadnych dodatkowych rozszerzeń dla interfejsu API funkcji Azure AD Graph. Istnieje bardzo ograniczona liczba scenariuszy, dla których interfejs API programu Azure AD Graph może być nadal odpowiedni; Aby uzyskać więcej informacji, zobacz wpis w blogu [programu Microsoft Graph lub microsoft AD Graph](https://developer.microsoft.com/office/blogs/microsoft-graph-or-azure-ad-graph/) i [migruj aplikacje programu Azure AD Graph do programu Microsoft Graph](https://docs.microsoft.com/graph/migrate-azure-ad-graph-overview).

Interfejs API programu Azure AD Graph zapewnia programowy dostęp do usługi Azure AD za pośrednictwem punktów końcowych interfejsu API OData REST. Aplikacje mogą używać interfejsu API programu Azure AD Graph do wykonywania operacji tworzenia, odczytu, aktualizacji i usuwania (CRUD) na danych i obiektach katalogu. Na przykład można użyć interfejsu API programu Azure AD Graph, aby utworzyć nowego użytkownika, wyświetlić lub zaktualizować właściwości użytkownika, zmienić hasło użytkownika, sprawdzić członkostwo w grupie pod kątem dostępu opartego na rolach, wyłączyć lub usunąć użytkownika. Aby uzyskać więcej informacji na temat funkcji interfejsu API programu Azure AD Graph i scenariuszy aplikacji, zobacz [Wymagania wstępne interfejsu API programu Azure AD Graph](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/api-catalog) i interfejsu API programu Azure AD [Graph](https://msdn.microsoft.com/library/hh974476.aspx). Interfejs API programu Azure AD Graph działa tylko z kontami służbowymi lub szkolnymi/organizacyjnymi.

Ten artykuł dotyczy interfejsu API programu Azure AD Graph. Aby uzyskać podobne informacje dotyczące interfejsu API programu Microsoft Graph, zobacz [Korzystanie z interfejsu API programu Microsoft Graph](https://developer.microsoft.com/graph/docs/concepts/use_the_api).

## <a name="how-to-construct-a-graph-api-url"></a>Jak utworzyć adres URL interfejsu API wykresu

W interfejsie API wykresu, aby uzyskać dostęp do danych i obiektów katalogu (innymi słowy zasobów lub jednostek), względem których chcesz wykonywać operacje CRUD, można użyć adresów URL opartych na protokole Open Data (OData). Adresy URL używane w interfejsie API wykresu składają się z czterech głównych części: `https://graph.windows.net/{tenant-identifier}/{resource-path}?[query-parameters]`katalogu głównego usługi, identyfikatora dzierżawy, ścieżki zasobów i opcji ciągu zapytania: . Weźmy przykład następującego `https://graph.windows.net/contoso.com/groups?api-version=1.6`adresu URL: .

* **Katalog główny usługi:** W interfejsie API programu https://graph.windows.netAzure AD Graph katalog główny usługi jest zawsze .
* **Identyfikator dzierżawy:** Ta sekcja może być zweryfikowaną (zarejestrowaną) nazwą domeny, w poprzednim przykładzie contoso.com. Może to być również identyfikator obiektu dzierżawcy lub alias "myorganization" lub "ja". Aby uzyskać więcej informacji, zobacz [Adresowanie jednostek i operacji w interfejsie API programu Azure AD Graph](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-operations-overview).
* **Ścieżka zasobu:** Ta sekcja adresu URL identyfikuje zasób, z który ma wchodzić w interakcje (użytkownicy, grupy, określony użytkownik lub określona grupa itp.) W powyższym przykładzie jest najwyższego poziomu "grupy", aby rozwiązać ten zestaw zasobów. Można również adres określonej jednostki, na przykład "users/{objectId}" lub "users/userPrincipalName".
* **Parametry kwerendy**: Znak zapytania (?) oddziela sekcję ścieżki zasobu od sekcji parametry kwerendy. Parametr zapytania "wersja interfejsu api" jest wymagany dla wszystkich żądań w interfejsie API programu Azure AD Graph. Interfejs API programu Azure AD Graph obsługuje również następujące opcje kwerendy OData: **$filter**, **$orderby**, **$expand**, **$top**i **$format**. Następujące opcje kwerendy nie są obecnie obsługiwane: **$count**, **$inlinecount**i **$skip**. Aby uzyskać więcej informacji, zobacz [Obsługiwane kwerendy, filtry i opcje stronicowania w interfejsie API programu Azure AD Graph](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-supported-queries-filters-and-paging-options).

## <a name="graph-api-versions"></a>Wersje interfejsu API wykresu

Należy określić wersję żądania interfejsu API wykresu w parametrze zapytania "wersja interfejsu api". W wersji 1.5 lub nowszej należy użyć wartości wersji numerycznej; api-version=1.6. W przypadku wcześniejszych wersji należy użyć ciągu daty zgodnego z formatem YYYY-MM-DD; na przykład api-version=2013-11-08. Aby uzyskać funkcje podglądu, użyj ciągu "beta"; na przykład api-version=beta. Aby uzyskać więcej informacji na temat różnic między wersjami interfejsu API programu Graph, zobacz [Przechowywanie wersji interfejsu API programu Azure AD Graph](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-versioning).

## <a name="graph-api-metadata"></a>Metadane interfejsu API wykresu

Aby zwrócić plik metadanych interfejsu API programu Azure AD Graph, dodaj segment "$metadata" po identyfikatorze dzierżawy w adresie `https://graph.windows.net/GraphDir1.OnMicrosoft.com/$metadata?api-version=1.6`URL Na przykład następujący adres URL zwraca metadane firmy demonstracyjnej: . Możesz wprowadzić ten adres URL na pasku adresu przeglądarki internetowej, aby wyświetlić metadane. Zwrócony dokument metadanych CSDL opisuje jednostki i typy złożone, ich właściwości oraz funkcje i akcje udostępniane przez żądaną wersję interfejsu API wykresu. Pominięcie parametru wersji interfejsu API zwraca metadane dla najnowszej wersji.

## <a name="common-queries"></a>Typowe zapytania

[Typowe kwerendy interfejsu API usługi Azure AD Graph](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-supported-queries-filters-and-paging-options#CommonQueries) zawierają listę typowych kwerend, które mogą być używane z programem Azure AD Graph, w tym kwerend, które mogą być używane do uzyskiwania dostępu do zasobów najwyższego poziomu w katalogu i kwerend do wykonywania operacji w katalogu.

Na przykład `https://graph.windows.net/contoso.com/tenantDetails?api-version=1.6` zwraca informacje o firmie dla contoso.com katalogu.

Lub `https://graph.windows.net/contoso.com/users?api-version=1.6` wyświetla listę wszystkich obiektów użytkownika w katalogu contoso.com.

## <a name="using-the-azure-ad-graph-explorer"></a>Korzystanie z Eksploratora wykresów usługi Azure AD
Za pomocą Eksploratora wykresu usługi Azure AD dla interfejsu API programu Azure AD Graph można wysyłać zapytania do danych katalogu podczas tworzenia aplikacji.

Poniższy zrzut ekranu jest dane wyjściowe, które można zobaczyć, jeśli były do `https://graph.windows.net/GraphDir1.OnMicrosoft.com/users?api-version=1.6` przejścia do Eksploratora wykresu usługi Azure AD, zaloguj się i wprowadź, aby wyświetlić wszystkich użytkowników w katalogu zalogowany użytkownik:

![Przykładowe dane wyjściowe w Eksploratorze interfejsu API programu Azure AD Graph](./media/active-directory-graph-api-quickstart/graph_explorer.png)

**Załaduj Eksploratora wykresów usługi Azure AD:** Aby załadować narzędzie, przejdź do . [https://graphexplorer.azurewebsites.net/](https://graphexplorer.azurewebsites.net/) Kliknij **pozycję Zaloguj się** i zaloguj się przy użyciu poświadczeń konta usługi Azure AD, aby uruchomić Eksploratora wykresu usługi Azure AD przeciwko dzierżawie. Jeśli uruchomisz Eksploratora wykresu usługi Azure AD przeciwko własnej dzierżawie, użytkownik lub administrator musi wyrazić zgodę podczas logowania. Jeśli masz subskrypcję usługi Office 365, automatycznie masz dzierżawę usługi Azure AD. Poświadczenia używane do logowania się do usługi Office 365 są w rzeczywistości kontami usługi Azure AD i można ich używać za pomocą Eksploratora programu Azure AD Graph.

**Uruchamianie kwerendy:** Aby uruchomić kwerendę, wpisz kwerendę w polu tekstowym żądania i kliknij przycisk **POBIERZ** lub kliknij klawisz **Enter.** Wyniki są wyświetlane w polu odpowiedzi. Na przykład `https://graph.windows.net/myorganization/groups?api-version=1.6` wyświetla listę wszystkich obiektów grupy w katalogu zalogowanych użytkowników.

Należy zwrócić uwagę na następujące funkcje i ograniczenia Eksploratora wykresów usługi Azure AD:

* Możliwość autouzupełniania w zestawach zasobów. Aby wyświetlić tę funkcję, kliknij pole tekstowe żądania (w którym pojawia się adres URL firmy). Można wybrać zestaw zasobów z listy rozwijanej.
* Historia żądań.
* Obsługuje aliasy adresujące "ja" i "mojaorganizacja". Na przykład można `https://graph.windows.net/me?api-version=1.6` użyć do zwrócenia obiektu użytkownika zalogowanego `https://graph.windows.net/myorganization/users?api-version=1.6` użytkownika lub zwrócić wszystkich użytkowników w katalogu zalogowanego użytkownika.
* Obsługuje pełne operacje CRUD względem `POST`własnego `GET` `PATCH` katalogu `DELETE`za pomocą , i .
* Sekcja nagłówków odpowiedzi. W tej sekcji można pomóc w rozwiązywaniu problemów, które występują podczas uruchamiania kwerend.
* Przeglądarka JSON dla odpowiedzi z możliwością rozwijania i zwijania.
* Brak obsługi wyświetlania lub przesyłania zdjęcia miniatury.

## <a name="using-fiddler-to-write-to-the-directory"></a>Używanie fiddler do zapisywania w katalogu

Na potrzeby tego przewodnika Szybki start można użyć debugera sieci Web Fiddler do wykonywania operacji "zapisu" względem katalogu usługi Azure AD. Na przykład można uzyskać i przekazać zdjęcie profilowe użytkownika (co nie jest możliwe w eksploratorze programu Azure AD Graph). Aby uzyskać więcej informacji i [https://www.telerik.com/fiddler](https://www.telerik.com/fiddler)zainstalować Fiddler, zobacz .

W poniższym przykładzie używasz debugera sieci Web Fiddler, aby utworzyć nową grupę zabezpieczeń "MyTestGroup" w katalogu usługi Azure AD.

**Uzyskaj token dostępu:** Aby uzyskać dostęp do programu Azure AD Graph, klienci muszą najpierw pomyślnie uwierzytelnić się w usłudze Azure AD. Aby uzyskać więcej informacji, zobacz [Scenariusze uwierzytelniania dla usługi Azure AD](authentication-scenarios.md).

**Skomponuj i uruchom kwerendę:** Wykonaj następujące kroki:

1. Otwórz debuger sieci Web Fiddler i przełącz się na kartę **Kompozytor.**
2. Ponieważ chcesz utworzyć nową grupę zabezpieczeń, wybierz **pozycję Opublikuj** jako metodę HTTP z menu rozwijanego. Aby uzyskać więcej informacji na temat operacji i uprawnień obiektu grupy, zobacz [Grupowanie](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#group-entity) w [ramach odwołania interfejsu API REST programu Azure AD Graph](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/api-catalog).
3. W polu obok **pozycji Opublikuj**wpisz następujący `https://graph.windows.net/{mytenantdomain}/groups?api-version=1.6`adres URL żądania: .
   
   > [!NOTE]
   > Musisz zastąpić {mytenantdomena} nazwą domeny własnego katalogu usługi Azure AD.

4. W polu bezpośrednio pod instrukcją poniżej poniżej poniżej poniżej nagłówka HTTP wpisz następujący nagłówek HTTP:
   
    ```
   Host: graph.windows.net
   Authorization: Bearer <your access token>
   Content-Type: application/json
   ```
   
   > [!NOTE]
   > &lt;Zastąp&gt; token dostępu tokenem dostępu dla katalogu usługi Azure AD.

5. W treści **Żądanie** wpisz następujący język JSON:
   
    ```
        {
            "displayName":"MyTestGroup",
            "mailNickname":"MyTestGroup",
            "mailEnabled":"false",
            "securityEnabled": true
        }
   ```
   
    Aby uzyskać więcej informacji na temat tworzenia grup, zobacz [Tworzenie grupy](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/groups-operations#CreateGroup).

Aby uzyskać więcej informacji na temat jednostek i typów usługi Azure AD, które są udostępniane przez program Graph oraz informacji o operacjach, które można wykonać na nich za pomocą programu Graph, zobacz [odwołanie do interfejsu API REST programu Azure AD Graph](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/api-catalog).

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej o [interfejsie API programu Azure AD Graph](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/api-catalog)
* Dowiedz się więcej o [zakresach uprawnień interfejsu API programu Azure AD Graph](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-permission-scopes)
