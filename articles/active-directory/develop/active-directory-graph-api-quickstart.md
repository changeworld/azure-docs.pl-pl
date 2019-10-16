---
title: Jak korzystać z interfejs API programu Graph usługi Azure AD
description: Interfejs API programu Graph Azure Active Directory (Azure AD) zapewnia programistyczny dostęp do usługi Azure AD za pomocą punktów końcowych interfejsu API REST protokołu OData. Aplikacje mogą używać usługi Azure AD interfejs API programu Graph do wykonywania operacji tworzenia, odczytu, aktualizacji i usuwania (CRUD) na danych i obiektach katalogu.
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
ms.date: 08/28/2019
ms.author: ryanwi
ms.reviewer: sureshja
ms.custom: aaddev, identityplatformtop40
ms.collection: M365-identity-device-management
ms.openlocfilehash: 662d8ecf3d20716a717a5f04f30e04114c9dce04
ms.sourcegitcommit: 0576bcb894031eb9e7ddb919e241e2e3c42f291d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/15/2019
ms.locfileid: "72374167"
---
# <a name="how-to-use-the-azure-ad-graph-api"></a>Instrukcje: korzystanie z interfejs API programu Graph usługi Azure AD

> [!IMPORTANT]
> Zdecydowanie zalecamy uzyskiwanie dostępu do zasobów usługi Azure Active Directory za pomocą programu [Microsoft Graph](https://developer.microsoft.com/graph) zamiast interfejsu API funkcji Azure AD Graph. Obecnie koncentrujemy nasze działania deweloperskie na programie Microsoft Graph i nie planujemy żadnych dodatkowych rozszerzeń dla interfejsu API funkcji Azure AD Graph. Istnieje bardzo ograniczona liczba scenariuszy, w których usługa Azure AD interfejs API programu Graph nadal może być odpowiednia; Aby uzyskać więcej informacji, zapoznaj się z wpisami w blogu [Microsoft Graph lub Azure AD Graph](https://dev.office.com/blogs/microsoft-graph-or-azure-ad-graph) i [Migrowanie aplikacji Azure ad Graph do Microsoft Graph](https://docs.microsoft.com/graph/migrate-azure-ad-graph-overview).

Interfejs API programu Graph Azure Active Directory (Azure AD) zapewnia programistyczny dostęp do usługi Azure AD za pomocą punktów końcowych interfejsu API REST protokołu OData. Aplikacje mogą używać usługi Azure AD interfejs API programu Graph do wykonywania operacji tworzenia, odczytu, aktualizacji i usuwania (CRUD) na danych i obiektach katalogu. Można na przykład użyć usługi Azure AD interfejs API programu Graph, aby utworzyć nowego użytkownika, wyświetlić lub zaktualizować właściwości użytkownika, zmienić hasło użytkownika, sprawdzić członkostwo w grupie na potrzeby dostępu opartego na rolach, wyłączyć lub usunąć użytkownika. Aby uzyskać więcej informacji na temat funkcji usługi Azure AD interfejs API programu Graph i scenariuszy aplikacji, zobacz Wymagania wstępne dotyczące [usługi Azure ad interfejs API programu Graph](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/api-catalog) i [usługi Azure AD interfejs API programu Graph](https://msdn.microsoft.com/library/hh974476.aspx). Usługa Azure AD interfejs API programu Graph działa tylko z kontami służbowymi i firmowymi.

Ten artykuł ma zastosowanie do usługi Azure AD interfejs API programu Graph. Aby poznać podobne informacje dotyczące interfejsu API Microsoft Graph, zobacz [Korzystanie z interfejsu api Microsoft Graph](https://developer.microsoft.com/graph/docs/concepts/use_the_api).

## <a name="how-to-construct-a-graph-api-url"></a>Jak utworzyć adres URL interfejs API programu Graph

W interfejs API programu Graph, aby uzyskać dostęp do danych i obiektów katalogu (innymi słowy, zasobów lub jednostek), dla których chcesz wykonywać operacje CRUD, możesz użyć adresów URL opartych na protokole Open Data (OData). Adresy URL używane w interfejs API programu Graph składają się z czterech głównych części: katalogu głównego usług, identyfikatora dzierżawy, ścieżki zasobów i opcji ciągu zapytania: `https://graph.windows.net/{tenant-identifier}/{resource-path}?[query-parameters]`. Skorzystaj z przykładu następującego adresu URL: `https://graph.windows.net/contoso.com/groups?api-version=1.6`.

* **Katalog główny usługi**: w usłudze Azure AD interfejs API programu Graph katalog główny usługi jest zawsze https://graph.windows.net.
* **Identyfikator dzierżawy**: Ta sekcja może być zweryfikowaną (zarejestrowaną) nazwą domeny w powyższym przykładzie contoso.com. Może to być również identyfikator obiektu dzierżawy lub alias "weborganization" lub "Me". Aby uzyskać więcej informacji, zobacz temat [adresowanie jednostek i operacji w usłudze Azure AD interfejs API programu Graph](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-operations-overview).
* **Ścieżka zasobu**: Ta sekcja adresu URL identyfikuje zasób, z którym ma być przeprowadzana praca (użytkowników, grup, określony użytkownik lub określona grupa itp.). W powyższym przykładzie jest to "grupy" najwyższego poziomu, aby określić ten zestaw zasobów. Można również rozwiązać określony obiekt, na przykład "Users/{objectId}" lub "Users/userPrincipalName".
* **Parametry zapytania**: znak zapytania (?) oddziela sekcję ścieżki zasobu z sekcji parametry zapytania. Parametr zapytania "API-Version" jest wymagany dla wszystkich żądań w usłudze Azure AD interfejs API programu Graph. Usługa Azure AD interfejs API programu Graph obsługuje również następujące opcje zapytania OData: **$Filter**, **$OrderBy**, **$expand**, **$Top**i **$Format**. Następujące opcje zapytania nie są obecnie obsługiwane: **$Count**, **$inlinecount**i **$Skip**. Aby uzyskać więcej informacji, zobacz temat [obsługiwane zapytania, filtry i opcje stronicowania w usłudze Azure AD interfejs API programu Graph](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-supported-queries-filters-and-paging-options).

## <a name="graph-api-versions"></a>Wersje interfejs API programu Graph

Należy określić wersję żądania interfejs API programu Graph w parametrze zapytania "API-Version". W przypadku wersji 1,5 i nowszych należy użyć wartości numerycznej wersji; API-Version = 1.6. W przypadku wcześniejszych wersji należy użyć ciągu daty zgodnego z formatem RRRR-MM-DD; na przykład API-Version = 2013-11-08. W przypadku funkcji wersji zapoznawczej Użyj ciągu "beta"; na przykład API-version = Beta. Aby uzyskać więcej informacji na temat różnic między wersjami interfejs API programu Graph, zobacz [przechowywanie wersji interfejs API programu Graph usługi Azure AD](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-versioning).

## <a name="graph-api-metadata"></a>Metadane interfejs API programu Graph

Aby zwrócić plik metadanych interfejs API programu Graph usługi Azure AD, Dodaj segment "$metadata" po identyfikatorze dzierżawy w adresie URL, na przykład następujący adres URL zwraca metadane dla firmy demonstracyjnej: `https://graph.windows.net/GraphDir1.OnMicrosoft.com/$metadata?api-version=1.6`. Możesz wprowadzić ten adres URL na pasku adresu przeglądarki sieci Web, aby wyświetlić metadane. Zwrócony dokument metadanych CSDL zawiera opis jednostek i typów złożonych, ich właściwości oraz funkcji i akcji udostępnianych przez żądaną wersję interfejs API programu Graph. Pominięcie parametru API-Version zwraca metadane dla najnowszej wersji.

## <a name="common-queries"></a>Typowe zapytania

[Często zadawane zapytania w usłudze azure interfejs API programu Graph AD](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-supported-queries-filters-and-paging-options#CommonQueries) zawierają listę typowych zapytań, które mogą być używane z wykresem usługi Azure AD, w tym zapytania, których można użyć w celu uzyskania dostępu do zasobów najwyższego poziomu w katalogu i zapytania do wykonywania operacji w katalogu.

Na przykład `https://graph.windows.net/contoso.com/tenantDetails?api-version=1.6` zwraca informacje o firmie dla katalogu contoso.com.

Lub `https://graph.windows.net/contoso.com/users?api-version=1.6` wyświetla wszystkie obiekty użytkownika w katalogu contoso.com.

## <a name="using-the-azure-ad-graph-explorer"></a>Korzystanie z Eksploratora grafów usługi Azure AD
Za pomocą Eksploratora usługi Azure AD Graph dla interfejs API programu Graph usługi Azure AD można wysyłać zapytania o dane katalogu podczas kompilowania aplikacji.

Poniższy zrzut ekranu przedstawia dane wyjściowe, które pojawią się, jeśli chcesz przejść do Eksploratora Azure AD Graph, zaloguj się i wprowadź `https://graph.windows.net/GraphDir1.OnMicrosoft.com/users?api-version=1.6`, aby wyświetlić wszystkich użytkowników w katalogu zalogowanego użytkownika:

![Przykładowe dane wyjściowe w Eksploratorze interfejs API programu Graph usługi Azure AD](./media/active-directory-graph-api-quickstart/graph_explorer.png)

**Załaduj Eksplorator programu Azure AD Graph**: aby załadować narzędzie, przejdź do [https://graphexplorer.azurewebsites.net/](https://graphexplorer.azurewebsites.net/). Kliknij przycisk **Zaloguj** i zaloguj się przy użyciu poświadczeń konta usługi Azure AD, aby uruchomić Eksploratora grafów usługi Azure AD dla dzierżawy. Jeśli uruchamiasz Eksploratora programu Graph w usłudze Azure AD przy użyciu własnej dzierżawy, ty lub administrator musi wyrazić zgodę podczas logowania. Jeśli masz subskrypcję pakietu Office 365, automatycznie masz dzierżawę usługi Azure AD. Poświadczenia używane do logowania się w usłudze Office 365 to, w rzeczywistości konta usługi Azure AD, i można użyć tych poświadczeń z Eksploratorem Azure AD Graph.

**Uruchom zapytanie**: Aby uruchomić zapytanie, wpisz zapytanie w polu tekstowym żądanie, a następnie kliknij przycisk **Pobierz** lub kliknij klawisz **Enter** . Wyniki są wyświetlane w polu odpowiedź. Na przykład `https://graph.windows.net/myorganization/groups?api-version=1.6` wyświetla wszystkie obiekty grupy w katalogu zalogowanego użytkownika.

Należy zwrócić uwagę na następujące funkcje i ograniczenia dotyczące Eksploratora grafów usługi Azure AD:

* Możliwość autouzupełniania w zestawach zasobów. Aby wyświetlić tę funkcję, kliknij pole tekstowe żądanie (gdzie pojawia się adres URL firmy). Zestaw zasobów można wybrać z listy rozwijanej.
* Historia żądań.
* Obsługuje aliasy adresów "Me" i "weborganization". Na przykład można użyć `https://graph.windows.net/me?api-version=1.6` do zwrócenia obiektu użytkownika zalogowanego użytkownika lub `https://graph.windows.net/myorganization/users?api-version=1.6`, aby zwrócić wszystkich użytkowników w katalogu zalogowanego użytkownika.
* Program obsługuje pełne operacje CRUD na własnym katalogu przy użyciu `POST`, `GET`, `PATCH` i `DELETE`.
* Sekcja nagłówki odpowiedzi. Ta sekcja może pomóc w rozwiązywaniu problemów występujących podczas wykonywania zapytań.
* Podgląd JSON dla odpowiedzi z możliwościami rozwijania i zwijania.
* Brak obsługi wyświetlania lub przekazywania fotografii miniatury.

## <a name="using-fiddler-to-write-to-the-directory"></a>Zapisywanie w katalogu przy użyciu programu Fiddler

Na potrzeby tego przewodnika Szybki Start można użyć debugera sieci Web programu Fiddler do wykonania operacji "Write" w katalogu usługi Azure AD. Możesz na przykład uzyskać i przekazać zdjęcie profilu użytkownika (co nie jest możliwe w przypadku Eksploratora Azure AD Graph). Aby uzyskać więcej informacji i zainstalować programu Fiddler, zobacz [https://www.telerik.com/fiddler](https://www.telerik.com/fiddler).

W poniższym przykładzie użyto debugera sieci Web programu Fiddler w celu utworzenia nowej grupy zabezpieczeń "Moja Tester" w katalogu usługi Azure AD.

**Uzyskaj token dostępu**: Aby uzyskać dostęp do programu Azure AD Graph, klienci muszą najpierw przeprowadzić uwierzytelnienie w usłudze Azure AD. Aby uzyskać więcej informacji, zobacz [scenariusze uwierzytelniania dla usługi Azure AD](v1-authentication-scenarios.md).

**Tworzenie i uruchamianie zapytania**: wykonaj następujące czynności:

1. Otwórz debuger sieci Web programu Fiddler i przejdź do karty **układacz** .
2. Ponieważ chcesz utworzyć nową grupę zabezpieczeń, wybierz pozycję **post** jako metodę http z menu rozwijanego. Aby uzyskać więcej informacji o operacjach i uprawnieniach do obiektu grupy, zobacz [Group](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#group-entity) in the [Azure AD Graf API REST Reference](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/api-catalog).
3. W polu obok pozycji **Opublikuj**wpisz następujący adres URL żądania: `https://graph.windows.net/{mytenantdomain}/groups?api-version=1.6`.
   
   > [!NOTE]
   > Należy zastąpić {mytenantdomain} nazwą domeny własnego katalogu usługi Azure AD.

4. W polu bezpośrednio poniżej wpisów ściągania wpisz następujący nagłówek HTTP:
   
    ```
   Host: graph.windows.net
   Authorization: Bearer <your access token>
   Content-Type: application/json
   ```
   
   > [!NOTE]
   > Zastąp token dostępu @no__t 0your @ no__t-1 tokenem dostępu do katalogu usługi Azure AD.

5. W polu **treść żądania** wpisz następujący kod JSON:
   
    ```
        {
            "displayName":"MyTestGroup",
            "mailNickname":"MyTestGroup",
            "mailEnabled":"false",
            "securityEnabled": true
        }
   ```
   
    Aby uzyskać więcej informacji na temat tworzenia grup, zobacz [Create Group](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/groups-operations#CreateGroup).

Aby uzyskać więcej informacji o jednostkach i typach usługi Azure AD, które są udostępniane przez Graf i informacje o operacjach, które można wykonać na nich przy użyciu programu Graph, zobacz [Dokumentacja interfejsu API REST usługi Azure AD Graph](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/api-catalog).

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej o [usłudze Azure AD interfejs API programu Graph](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/api-catalog)
* Dowiedz się więcej o [zakresach uprawnień interfejs API programu Graph usługi Azure AD](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-permission-scopes)
