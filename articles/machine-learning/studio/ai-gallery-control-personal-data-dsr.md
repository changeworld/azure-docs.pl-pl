---
title: Wyświetlanie i usuwanie danych z galerii Azure AI | Dokumentacja firmy Microsoft
description: Można wyeksportować i usuwać dane użytkowników w produkcie z galerii AI Azure za pomocą interfejsu lub interfejs API AI galerii wykazu. W tym artykule opisano sposób.
services: machine-learning
author: heatherbshapiro
ms.author: hshapiro
manager: cgronlun
ms.reviewer: jmartens, mldocs
ms.service: machine-learning
ms.component: studio
ms.topic: conceptual
ms.date: 05/25/2018
ms.openlocfilehash: bc6ffa912d7914c8662dbde623e04947540ac149
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34655612"
---
# <a name="view-and-delete-in-product-user-data-from-azure-ai-gallery"></a>Wyświetl i Usuń dane użytkownika w produktu z galerii Azure AI

Wyświetlać i usuwać dane użytkownika w produktu z galerii AI Azure za pomocą interfejsu lub interfejs API wykazu galerii AI. W tym artykule wyjaśniono sposób.

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-dsr-and-stp-note.md)]

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

## <a name="view-your-data-in-ai-gallery-with-the-ui"></a>Wyświetlanie danych w galerii AI przy użyciu interfejsu użytkownika

Można wyświetlić elementy, które są publikowane za pośrednictwem witryny galerii Azure AI interfejsu użytkownika. Użytkownicy mogą wyświetlać publiczny i nieznajdujące się na liście rozwiązania, projekty eksperymenty i innych opublikowanych elementów:

1.  Zaloguj się do [galerii Azure AI](https://gallery.azure.ai/).
2.  Kliknij obraz profilu w prawym górnym rogu, a następnie nazwę konta do załadowania strony swojego profilu.
3.  Strony profilu Wyświetla wszystkie elementy opublikowany w galerii, w tym wpisy nieznajdujące się na liście.

## <a name="use-the-ai-gallery-catalog-api-to-view-your-data"></a>Aby wyświetlić dane za pomocą interfejsu API katalogu galerii AI

Można programowo wyświetlać danych zbieranych za pośrednictwem API katalogu AI galerii, która jest dostępna w https://catalog.cortanaanalytics.com/entities. Aby wyświetlić dane, należy uzyskać identyfikator autora. Aby wyświetlić nieznajdujące się na liście jednostek za pośrednictwem interfejsu API katalogu, należy tokenu dostępu.

Wykaz odpowiedzi są zwracane w formacie JSON.

### <a name="get-an-author-id"></a>Pobierz identyfikator autora
Identyfikator autora jest oparta na adres e-mail używany w przypadku publikowania do galerii Azure AI. Nie zmienia ono:

1.  Zaloguj się do [galerii Azure AI](https://gallery.azure.ai/).
2.  Kliknij obraz profilu w prawym górnym rogu, a następnie nazwę konta do załadowania strony swojego profilu.
3.  Adres URL na pasku adresu Wyświetla alfanumeryczne następujący identyfikator `authorId=`. Na przykład dla adresu URL: `https://gallery.cortanaintelligence.com/Home/Author?authorId=99F1F5C6260295F1078187FA179FBE08B618CB62129976F09C6AF0923B02A5BA`
        
    Identyfikator Autor: `99F1F5C6260295F1078187FA179FBE08B618CB62129976F09C6AF0923B02A5BA`

### <a name="get-your-access-token"></a>Uzyskaj token dostępu

Należy tokenu dostępu, aby wyświetlić nieznajdujące się na liście jednostek za pośrednictwem interfejsu API katalogu. Użytkownicy bez tokenu dostępu, nadal można wyświetlać, publiczny jednostek i inne informacje o użytkowniku.

Aby uzyskać token dostępu, należy sprawdzić `DataLabAccessToken` nagłówka żądania HTTP, przeglądarka sprawia, że do interfejsu API katalogu, logując się:

1.  Zaloguj się do [galerii Azure AI](https://gallery.azure.ai/).
2.  Kliknij obraz profilu w prawym górnym rogu, a następnie nazwę konta do załadowania strony swojego profilu.
3.  Otwórz okienko narzędzi deweloperskich przeglądarki, naciskając klawisz F12, wybierz kartę sieci i Odśwież stronę. 
4. Filtrowanie żądań na ciąg *katalogu* , wpisując polecenie w polu tekstowym filtru.
5.  W żądaniach wysyłanych do adresu URL `https://catalog.cortanaanalytics.com/entities`, Znajdź żądania GET i zaznacz pozycję *nagłówki* kartę. Przewiń w dół do *nagłówkami żądań* sekcji.
6.  Pod nagłówkiem `DataLabAccessToken` jest tokenem alfanumeryczne. Aby zabezpieczyć dane, nie należy współużytkować ten token.

### <a name="view-user-information"></a>Wyświetlanie informacji o użytkowniku
Za pomocą Identyfikatora autora uzyskano w poprzednich krokach, należy przejrzeć informacje w profilu użytkownika przez zastąpienie `[AuthorId]` następujący adres URL:

    https://catalog.cortanaanalytics.com/users/[AuthorID]

Na przykład to żądanie adresu URL:
    
    https://catalog.cortanaanalytics.com/users/99F1F5C6260295F1078187FA179FBE08B618CB62129976F09C6AF0923B02A5BA

Zwraca odpowiedź, takich jak:

    {"entities_count":9,"contribution_score":86.351575190956922,"scored_at":"2018-05-07T14:30:25.9305671+00:00","contributed_at":"2018-05-07T14:26:55.0381756+00:00","created_at":"2017-12-15T00:49:15.6733094+00:00","updated_at":"2017-12-15T00:49:15.6733094+00:00","name":"First Last","slugs":["First-Last"],"tenant_id":"14b2744cf8d6418c87ffddc3f3127242","community_id":"9502630827244d60a1214f250e3bbca7","id":"99F1F5C6260295F1078187FA179FBE08B618CB62129976F09C6AF0923B02A5BA","_links":{"self":"https://catalog.azureml.net/tenants/14b2744cf8d6418c87ffddc3f3127242/communities/9502630827244d60a1214f250e3bbca7/users/99F1F5C6260295F1078187FA179FBE08B618CB62129976F09C6AF0923B02A5BA"},"etag":"\"2100d185-0000-0000-0000-5af063010000\""}


### <a name="view-public-entities"></a>Wyświetl jednostki publiczne

Interfejs API wykazu przechowuje informacje dotyczące opublikowanych jednostki do galerii AI Azure, które można również wyświetlić bezpośrednio na [AI galerii witryny sieci Web](https://gallery.azure.ai/). 

Aby wyświetlić opublikowanej jednostek, skorzystaj z następującego adresu URL, zastępując `[AuthorId]` o identyfikatorze autora uzyskanych w [uzyskiwanie Identyfikatora autora](#get-an-author-ID) powyżej.

    https://catalog.cortanaanalytics.com/entities?$filter=author/id eq '[AuthorId]'

Na przykład:

    https://catalog.cortanaanalytics.com/entities?$filter=author/id eq '99F1F5C6260295F1078187FA179FBE08B618CB62129976F09C6AF0923B02A5BA'

### <a name="view-unlisted-and-public-entities"></a>Wyświetlanie obiektów nieznajdujących się na liście i publiczny

To zapytanie wyświetla tylko publiczne jednostki. Aby wyświetlić wszystkie Twoje encje, w tym nieznajdujące się na liście widocznych dostęp tokenu uzyskanego z poprzedniej sekcji.

1.  Przy użyciu narzędzia, takiego jak [Postman](https://www.getpostman.com), Utwórz żądanie HTTP GET do adresu URL katalogu w sposób opisany w [uzyskać tokenu dostępu](#get-your-access-token).
2.  Utwórz nagłówek żądania HTTP o nazwie `DataLabAccessToken`, wartość tokenu dostępu.
3.  Przesyłanie żądania HTTP.

> [!TIP]
> Jeśli nieznajdujące się na liście jednostek nie są wyświetlane w odpowiedzi z interfejsu API katalogu, użytkownik może być nieprawidłowy lub wygasły token dostępu. Wylogować się z galerii AI Azure, a następnie powtórz kroki opisane w [uzyskać tokenu dostępu](#get-your-access-token) do odnawiania tokenu. 
