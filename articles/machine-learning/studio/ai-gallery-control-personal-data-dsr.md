---
title: Zarządzanie danymi galerii sztucznej inteligencji platformy Azure
titleSuffix: ML Studio (classic) - Azure
description: Dane użytkownika w produkcie można eksportować i usuwać z galerii sztucznej inteligencji platformy Azure za pomocą interfejsu lub interfejsu API wykazu galerii sztucznej inteligencji. W tym artykule pokazano, jak to zrobić.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: likebupt
ms.author: keli19
ms.custom: seodec18
ms.date: 05/25/2018
ms.reviewer: jmartens, mldocs
ms.openlocfilehash: 03341b9e663398f2c42266dead0d2dd01e97c3f3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79204549"
---
# <a name="view-and-delete-in-product-user-data-from-azure-ai-gallery"></a>Wyświetlanie i usuwanie danych użytkowników w produkcie z galerii sztucznej inteligencji platformy Azure

[!INCLUDE [Notebook deprecation notice](../../../includes/aml-studio-notebook-notice.md)]

Dane użytkownika w produkcie można wyświetlać i usuwać z galerii sztucznej inteligencji platformy Azure za pomocą interfejsu lub interfejsu API wykazu galerii sztucznej inteligencji. W tym artykule dowiesz się, jak to zrobić.

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-dsr-and-stp-note.md)]

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

## <a name="view-your-data-in-ai-gallery-with-the-ui"></a>Wyświetlanie danych w galerii AI za pomocą interfejsu użytkownika

Można wyświetlić elementy opublikowane za pośrednictwem interfejsu użytkownika witryny sieci Web Galerii sztucznej platformy Azure. Użytkownicy mogą wyświetlać zarówno rozwiązania publiczne, jak i niepubliczne, projekty, eksperymenty i inne opublikowane elementy:

1.    Zaloguj się do [galerii sztucznej inteligencji platformy Azure](https://gallery.azure.ai/).
2.    Kliknij zdjęcie profilowe w prawym górnym rogu, a następnie nazwę konta, aby załadować stronę profilu.
3.    Na stronie profilu są wyświetlane wszystkie elementy opublikowane w galerii, w tym wpisy niepubliczne.

## <a name="use-the-ai-gallery-catalog-api-to-view-your-data"></a>Wyświetlanie danych za pomocą interfejsu API wykazu galerii AI

Można programowo wyświetlać dane zebrane za pośrednictwem interfejsu API https://catalog.cortanaanalytics.com/entitieswykazu galerii AI, który jest dostępny pod adresem . Aby wyświetlić dane, potrzebny jest identyfikator autora. Aby wyświetlić niepubliczne jednostki za pośrednictwem interfejsu API wykazu, potrzebujesz tokenu dostępu.

Odpowiedzi katalogu są zwracane w formacie JSON.

### <a name="get-an-author-id"></a>Uzyskaj identyfikator autora
Identyfikator autora jest oparty na adresie e-mail używanym podczas publikowania w galerii sztucznej inteligencji platformy Azure. To się nie zmienia:

1.    Zaloguj się do [galerii sztucznej inteligencji platformy Azure](https://gallery.azure.ai/).
2.    Kliknij zdjęcie profilowe w prawym górnym rogu, a następnie nazwę konta, aby załadować stronę profilu.
3.    Adres URL na pasku adresu wyświetla identyfikator alfanumeryczny poniżej `authorId=`. Na przykład dla adresu URL:`https://gallery.azure.ai/Home/Author?authorId=99F1F5C6260295F1078187FA179FBE08B618CB62129976F09C6AF0923B02A5BA`
        
    Identyfikator autora:`99F1F5C6260295F1078187FA179FBE08B618CB62129976F09C6AF0923B02A5BA`

### <a name="get-your-access-token"></a>Pobierz token dostępu

Do wyświetlania niepublicznych jednostek za pośrednictwem interfejsu API wykazu potrzebny jest token dostępu. Bez tokenu dostępu użytkownicy mogą nadal wyświetlać jednostki publiczne i inne informacje o użytkowniku.

Aby uzyskać token dostępu, należy `DataLabAccessToken` sprawdzić nagłówek żądania HTTP, które przeglądarka tworzy w interfejsie API wykazu podczas logowania:

1.    Zaloguj się do [galerii sztucznej inteligencji platformy Azure](https://gallery.azure.ai/).
2.    Kliknij zdjęcie profilowe w prawym górnym rogu, a następnie nazwę konta, aby załadować stronę profilu.
3.    Otwórz okienko Narzędzia programistyczne przeglądarki, naciskając klawisz F12, wybierz kartę Sieć i odśwież stronę. 
4. Filtruj żądania w *katalogu* ciągów, wpisując je w polu tekstowym Filtr.
5.    W żądaniach `https://catalog.cortanaanalytics.com/entities`do adresu URL znajdź żądanie GET i wybierz *Request Headers* kartę *Nagłówki.*
6.    Pod nagłówkiem `DataLabAccessToken` znajduje się token alfanumeryczny. Aby zapewnić bezpieczeństwo danych, nie udostępniaj tego tokenu.

### <a name="view-user-information"></a>Wyświetlanie informacji o użytkowniku
Korzystając z identyfikatora autora, który został wydany w poprzednich krokach, wyświetl informacje w profilu użytkownika, zastępując `[AuthorId]` następujący adres URL:

    https://catalog.cortanaanalytics.com/users/[AuthorID]

Na przykład to żądanie adresu URL:
    
    https://catalog.cortanaanalytics.com/users/99F1F5C6260295F1078187FA179FBE08B618CB62129976F09C6AF0923B02A5BA

Zwraca odpowiedź, taką jak:

    {"entities_count":9,"contribution_score":86.351575190956922,"scored_at":"2018-05-07T14:30:25.9305671+00:00","contributed_at":"2018-05-07T14:26:55.0381756+00:00","created_at":"2017-12-15T00:49:15.6733094+00:00","updated_at":"2017-12-15T00:49:15.6733094+00:00","name":"First Last","slugs":["First-Last"],"tenant_id":"14b2744cf8d6418c87ffddc3f3127242","community_id":"9502630827244d60a1214f250e3bbca7","id":"99F1F5C6260295F1078187FA179FBE08B618CB62129976F09C6AF0923B02A5BA","_links":{"self":"https://catalog.azureml.net/tenants/14b2744cf8d6418c87ffddc3f3127242/communities/9502630827244d60a1214f250e3bbca7/users/99F1F5C6260295F1078187FA179FBE08B618CB62129976F09C6AF0923B02A5BA"},"etag":"\"2100d185-0000-0000-0000-5af063010000\""}


### <a name="view-public-entities"></a>Wyświetlanie jednostek publicznych

Interfejs API wykazu przechowuje informacje o opublikowanych jednostkach w Galerii sztucznej inteligencji platformy Azure, które można również wyświetlać bezpośrednio w [witrynie galerii sztucznej inteligencji.](https://gallery.azure.ai/) 

Aby wyświetlić opublikowane jednostki, odwiedź `[AuthorId]` następujący adres URL, zastępując identyfikator autora uzyskany w [obszarze Uzyskaj identyfikator autora](#get-an-author-id) powyżej.

    https://catalog.cortanaanalytics.com/entities?$filter=author/id eq '[AuthorId]'

Przykład:

    https://catalog.cortanaanalytics.com/entities?$filter=author/id eq '99F1F5C6260295F1078187FA179FBE08B618CB62129976F09C6AF0923B02A5BA'

### <a name="view-unlisted-and-public-entities"></a>Wyświetlanie jednostek niepublicznych i publicznych

Ta kwerenda wyświetla tylko jednostki publiczne. Aby wyświetlić wszystkie encje, w tym te niepubliczne, podaj token dostępu uzyskany z poprzedniej sekcji.

1.    Za pomocą narzędzia, takiego jak [Listonosz,](https://www.getpostman.com)utwórz żądanie HTTP GET do adresu URL katalogu, zgodnie z opisem w [polu Pobierz token dostępu](#get-your-access-token).
2.    Utwórz nagłówek żądania `DataLabAccessToken`HTTP o nazwie , z wartością ustawioną na token dostępu.
3.    Prześlij żądanie HTTP.

> [!TIP]
> Jeśli niepubliczne jednostki nie są wyświetlane w odpowiedziach z interfejsu API wykazu, użytkownik może mieć nieprawidłowy lub wygasły token dostępu. Wyloguj się z galerii sztucznej inteligencji platformy Azure, a następnie powtórz kroki w [obszarze Pobierz token dostępu,](#get-your-access-token) aby odnowić token. 
