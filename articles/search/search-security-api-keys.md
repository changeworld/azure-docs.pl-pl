---
title: Tworzenie, zarządzanie i zabezpieczanie kluczy interfejsu API administratora i kwerendy
titleSuffix: Azure Cognitive Search
description: Klucz interfejsu API kontroluje dostęp do punktu końcowego usługi. Klucze administracyjne zapewniają dostęp do zapisu. Klucze kwerendy mogą być tworzone dla dostępu tylko do odczytu.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 68a17b8b3587077222a9ed2057927c8f16253c1e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "72794371"
---
# <a name="create-and-manage-api-keys-for-an-azure-cognitive-search-service"></a>Tworzenie kluczy interfejsu API dla usługi Azure Cognitive Search i zarządzanie nimi

Wszystkie żądania do usługi wyszukiwania potrzebują tylko do odczytu klucz interfejsu api, który został wygenerowany specjalnie dla usługi. Klucz interfejsu api jest jedynym mechanizmem uwierzytelniania dostępu do punktu końcowego usługi wyszukiwania i musi być uwzględniony na każde żądanie. W [rozwiązaniach REST](search-get-started-postman.md)klucz interfejsu api jest zazwyczaj określony w nagłówku żądania. W [rozwiązaniach platformy .NET](search-howto-dotnet-sdk.md#core-scenarios)klucz jest często określany jako ustawienie konfiguracji, a następnie przekazywany jako [Poświadczenia](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.searchserviceclient.credentials) (klucz administratora) lub [SearchCredentials](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.searchserviceclient.searchcredentials) (klucz zapytania) w [funkcji SearchServiceClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.searchserviceclient).

Klucze są tworzone za pomocą usługi wyszukiwania podczas inicjowania obsługi administracyjnej usługi. Wartości kluczy można wyświetlać i uzyskiwać w [portalu Azure](https://portal.azure.com).

![Strona portalu, ustawienia, sekcja Klawisze](media/search-manage/azure-search-view-keys.png)

## <a name="what-is-an-api-key"></a>Co to jest api-key

Api-key to ciąg składający się z losowo generowanych cyfr i liter. Za [pomocą uprawnień opartych na rolach](search-security-rbac.md)można usuwać lub odczytywać klucze, ale nie można zastąpić klucza hasłem zdefiniowanym przez użytkownika ani używać usługi Active Directory jako podstawowej metodologii uwierzytelniania dostępu do operacji wyszukiwania. 

Dwa typy kluczy są używane do uzyskiwania dostępu do usługi wyszukiwania: admin (odczyt i zapis) i kwerenda (tylko do odczytu).

|Klucz|Opis|Limity|  
|---------|-----------------|------------|  
|Administrator|Udziela pełnych praw do wszystkich operacji, w tym możliwość zarządzania usługą, tworzenia i usuwania indeksów, indeksatorów i źródeł danych.<br /><br /> Dwa klucze administratora, określane jako klucze *podstawowe* i *pomocnicze* w portalu, są generowane podczas tworzenia usługi i mogą być indywidualnie generowane na żądanie. Posiadanie dwóch kluczy umożliwia przewracanie jednego klucza podczas korzystania z drugiego klucza w celu dalszego dostępu do usługi.<br /><br /> Klucze administracyjne są określone tylko w nagłówkach żądań HTTP. Nie można umieścić klucza interfejsu api administratora w adresie URL.|Maksymalnie 2 na usługę|  
|Zapytanie|Udziela dostępu tylko do odczytu do indeksów i dokumentów i są zazwyczaj dystrybuowane do aplikacji klienckich, które wystawiają żądania wyszukiwania.<br /><br /> Klucze zapytań są tworzone na żądanie. Można je utworzyć ręcznie w portalu lub programowo za pośrednictwem [interfejsu API REST zarządzania](https://docs.microsoft.com/rest/api/searchmanagement/).<br /><br /> Klucze kwerendy można określić w nagłówku żądania HTTP dla operacji wyszukiwania, sugestii lub wyszukiwania. Alternatywnie można przekazać klucz zapytania jako parametr w adresie URL. W zależności od sposobu formułowania żądania przez aplikację kliencką może być łatwiej przekazać klucz jako parametr zapytania:<br /><br /> `GET /indexes/hotels/docs?search=*&$orderby=lastRenovationDate desc&api-version=2019-05-06&api-key=[query key]`|50 za usługę|  

 Wizualnie nie ma rozróżnienia między kluczem administratora lub kluczem zapytania. Oba klucze są ciągami składającymi się z 32 losowo generowanych znaków alfanumeryczne. Jeśli utracisz śledzenie, jaki typ klucza jest określony w aplikacji, można [sprawdzić wartości klucza w portalu](https://portal.azure.com) lub użyć interfejsu API [REST,](https://docs.microsoft.com/rest/api/searchmanagement/) aby zwrócić wartość i typ klucza.  

> [!NOTE]  
>  Jest uważany za złą praktykę zabezpieczeń `api-key` do przekazywania poufnych danych, takich jak identyfikator URI żądania. Z tego powodu usługa Azure Cognitive Search akceptuje `api-key` tylko klucz zapytania jako ciąg zapytania i należy tego unikać, chyba że zawartość indeksu powinna być publicznie dostępna. Zgodnie z ogólną zasadą `api-key` zalecamy przekazanie nagłówka żądania jako nagłówka.  

## <a name="find-existing-keys"></a>Znajdowanie istniejących kluczy

Klucze dostępu można uzyskać w portalu lub za pośrednictwem [interfejsu API REST zarządzania](https://docs.microsoft.com/rest/api/searchmanagement/). Aby uzyskać więcej informacji, zobacz [Zarządzanie administratorem i klucze interfejsu API kwerendy](search-security-api-keys.md).

1. Zaloguj się do [Portalu Azure](https://portal.azure.com).
2. Wyświetl [listę usług wyszukiwania](https://portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) subskrypcji.
3. Wybierz usługę i na stronie Przegląd kliknij pozycję**Klawisze** **ustawień,** >aby wyświetlić klucze administratora i kwerendy.

   ![Strona portalu, ustawienia, sekcja Klawisze](media/search-security-overview/settings-keys.png)

## <a name="create-query-keys"></a>Tworzenie kluczy kwerend

Klucze kwerendy są używane do dostępu tylko do odczytu do dokumentów w ramach indeksu dla operacji przeznaczonych dla kolekcji dokumentów. Kwerendy wyszukiwania, filtrowania i sugestii to wszystkie operacje, które przyjmują klucz zapytania. Każda operacja tylko do odczytu, która zwraca definicje danych systemowych lub obiektów, takie jak definicja indeksu lub stan indeksatora, wymaga klucza administratora.

Ograniczanie dostępu i operacji w aplikacjach klienckich jest niezbędne do ochrony zasobów wyszukiwania w usłudze. Zawsze używaj klucza zapytania, a nie klucza administratora dla dowolnej kwerendy pochodzącej z aplikacji klienckiej.

1. Zaloguj się do [Portalu Azure](https://portal.azure.com).
2. Wyświetl [listę usług wyszukiwania](https://portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) subskrypcji.
3. Wybierz usługę i na stronie Przegląd kliknij pozycję**Klawisze** **ustawień** >.
4. Kliknij pozycję **Zarządzaj kluczami kwerend**.
5. Użyj klucza zapytania już wygenerowanego dla usługi lub utwórz maksymalnie 50 nowych kluczy kwerend. Domyślny klucz zapytania nie jest nazwany, ale dodatkowe klucze kwerendy mogą być nazwane dla zarządzania.

   ![Tworzenie klucza kwerendy lub używanie go](media/search-security-overview/create-query-key.png) 

> [!Note]
> Przykład kodu przedstawiający użycie klucza zapytania można znaleźć w [kwerendzie indeksu usługi Azure Cognitive Search w języku C#](search-query-dotnet.md).

<a name="regenerate-admin-keys"></a>

## <a name="regenerate-admin-keys"></a>Ponowne generowanie kluczy administratora

Dwa klucze administratora są tworzone dla każdej usługi, dzięki czemu można obracać klucz podstawowy, przy użyciu klucza pomocniczego dla ciągłości biznesowej.

1. Na stronie**Klawisze** **ustawień** >skopiuj klucz pomocniczy.
2. W przypadku wszystkich aplikacji należy zaktualizować ustawienia klucza interfejsu api, aby użyć klucza pomocniczego.
3. Ponowne generowanie klucza podstawowego.
4. Zaktualizuj wszystkie aplikacje, aby użyć nowego klucza podstawowego.

Jeśli przypadkowo ponownie wygenerujesz oba klucze w tym samym czasie, wszystkie żądania klientów przy użyciu tych kluczy zakończy się niepowodzeniem z HTTP 403 Zabronione. Jednak zawartość nie jest usuwana i nie są zablokowane na stałe. 

Nadal można uzyskać dostęp do usługi za pośrednictwem portalu lub warstwy zarządzania[(INTERFEJS API REST,](https://docs.microsoft.com/rest/api/searchmanagement/) [PowerShell](https://docs.microsoft.com/azure/search/search-manage-powershell)lub Azure Resource Manager). Funkcje zarządzania są operacyjne za pośrednictwem identyfikatora subskrypcji nie usługi api-key, a zatem nadal dostępne, nawet jeśli nie są klucze interfejsu api. 

Po utworzeniu nowych kluczy za pośrednictwem portalu lub warstwy zarządzania, dostęp jest przywracany do zawartości (indeksy, indeksy, źródła danych, mapy synonimów) po uzyskaniu nowych kluczy i zapewnić te klucze na żądania.

## <a name="secure-api-keys"></a>Bezpieczne klucze interfejsu api
Bezpieczeństwo kluczy jest zapewnione przez ograniczenie dostępu za pośrednictwem interfejsu portalu lub Menedżera zasobów (programu PowerShell lub interfejsu wiersza polecenia). Jak wspomniano, administratorzy subskrypcji można wyświetlić i ponownie wygenerować wszystkie klucze interfejsu api. Jako środek ostrożności przejrzyj przypisania ról, aby zrozumieć, kto ma dostęp do kluczy administratora.

+ Na pulpicie nawigacyjnym usługi kliknij pozycję **Kontrola dostępu (IAM),** a następnie kartę **Przypisania ról,** aby wyświetlić przypisania ról dla usługi.

Członkowie następujących ról mogą wyświetlać i regenerować klucze: Właściciel, Współautor, [Współautor usługi wyszukiwania](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#search-service-contributor)

> [!Note]
> W przypadku dostępu opartego na tożsamości nad wynikami wyszukiwania można utworzyć filtry zabezpieczeń, aby przyciąć wyniki według tożsamości, usuwając dokumenty, do których żądający nie powinien mieć dostępu. Aby uzyskać więcej informacji, zobacz [Filtry zabezpieczeń](search-security-trimming-for-azure-search.md) i [Bezpieczne za pomocą usługi Active Directory](search-security-trimming-for-azure-search-with-aad.md).

## <a name="see-also"></a>Zobacz też

+ [Kontrola dostępu oparta na rolach w usłudze Azure Cognitive Search](search-security-rbac.md)
+ [Zarządzanie za pomocą programu PowerShell](search-manage-powershell.md) 
+ [Artykuł o wydajności i optymalizacji](search-performance-optimization.md)