---
title: Zarządzanie danymi Azure AI Gallery
titleSuffix: ML Studio (classic) - Azure
description: Możesz wyeksportować i usuwania danych użytkownika w ramach produktu przy użyciu galerii sztucznej Inteligencji Azure za pomocą interfejsu lub interfejs API wykazu galerii sztucznej Inteligencji. W tym artykule przedstawiono, jak.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: xiaoharper
ms.author: zhanxia
ms.custom: seodec18
ms.date: 05/25/2018
ms.reviewer: jmartens, mldocs
ms.openlocfilehash: 8406da7a429fb5b9429845628a00cdb6cc715540
ms.sourcegitcommit: 812bc3c318f513cefc5b767de8754a6da888befc
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/12/2020
ms.locfileid: "77148696"
---
# <a name="view-and-delete-in-product-user-data-from-azure-ai-gallery"></a>Wyświetlanie i usunąć dane użytkownika w produkcie z galerii sztucznej Inteligencji platformy Azure

Można wyświetlić i usunąć dane użytkowników w ramach produktu z galerii sztucznej Inteligencji platformy Azure przy użyciu interfejsu lub interfejs API wykazu galerii sztucznej Inteligencji. W tym artykule wyjaśniono sposób.

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-dsr-and-stp-note.md)]

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

## <a name="view-your-data-in-ai-gallery-with-the-ui"></a>Wyświetlanie danych w galerii sztucznej Inteligencji za pomocą interfejsu użytkownika

Można wyświetlić elementy, które są publikowane za pośrednictwem interfejsu użytkownika witryny galerii sztucznej Inteligencji platformy Azure. Użytkownicy mogą wyświetlać, zarówno publiczne, jak i spoza listy rozwiązań, projektów, eksperymenty i inne elementy opublikowane:

1.  Zaloguj się do [Azure AI Gallery](https://gallery.azure.ai/).
2.  Kliknij obraz profilu w prawym górnym rogu, a następnie nazwę konta, aby załadować stronę profilu.
3.  Strona profilu Wyświetla wszystkie elementy opublikowanych w galerii łącznie z wpisy nieznajdujące się na liście.

## <a name="use-the-ai-gallery-catalog-api-to-view-your-data"></a>Wyświetl dane usługi przy użyciu interfejsu API wykazu galerii sztucznej Inteligencji

Można programowo przeglądać dane zbierane za pomocą interfejsu API wykazu galerii AI, który jest dostępny w https://catalog.cortanaanalytics.com/entities. Aby wyświetlić dane, należy uzyskać identyfikator autora. Aby wyświetlić nieznajdujące się na liście jednostek za pomocą interfejsu API wykazu, konieczne jest token dostępu.

Wykaz odpowiedzi są zwracane w formacie JSON.

### <a name="get-an-author-id"></a>Uzyskiwanie Identyfikatora autora
Identyfikator Autor opiera się na adres e-mail używany w przypadku publikowania do galerii sztucznej Inteligencji platformy Azure. Nie zmienia się on:

1.  Zaloguj się do [Azure AI Gallery](https://gallery.azure.ai/).
2.  Kliknij obraz profilu w prawym górnym rogu, a następnie nazwę konta, aby załadować stronę profilu.
3.  Adres URL na pasku adresu zawiera alfanumeryczny identyfikator następujący `authorId=`. Na przykład dla adresu URL: `https://gallery.azure.ai/Home/Author?authorId=99F1F5C6260295F1078187FA179FBE08B618CB62129976F09C6AF0923B02A5BA`
        
    Identyfikator autora: `99F1F5C6260295F1078187FA179FBE08B618CB62129976F09C6AF0923B02A5BA`

### <a name="get-your-access-token"></a>Uzyskiwanie tokenu dostępu

Należy tokenu dostępu, aby wyświetlić nieznajdujące się na liście jednostek za pomocą interfejsu API wykazu. Bez tokenu dostępu użytkownicy nadal mogą wyświetlać podmiotów publicznych i inne informacje o użytkowniku.

Aby uzyskać token dostępu, musisz sprawdzić nagłówek `DataLabAccessToken` żądania HTTP, gdy przeglądarka przetworzy interfejs API wykazu podczas logowania:

1.  Zaloguj się do [Azure AI Gallery](https://gallery.azure.ai/).
2.  Kliknij obraz profilu w prawym górnym rogu, a następnie nazwę konta, aby załadować stronę profilu.
3.  Otwórz okienko narzędzi dla deweloperów przeglądarki, naciskając klawisz F12, wybierz kartę sieci i Odśwież stronę. 
4. Przefiltruj żądania w *wykazie* ciągów, wpisując je w polu tekstowym filtr.
5.  W obszarze żądania kierowane do adresu URL `https://catalog.cortanaanalytics.com/entities`Znajdź żądanie GET i wybierz kartę *nagłówki* . Przewiń w dół do sekcji *żądania nagłówków* .
6.  Pod nagłówkiem `DataLabAccessToken` jest token alfanumeryczny. Aby zachować bezpieczeństwo danych, nie udostępniaj tego tokenu.

### <a name="view-user-information"></a>Wyświetlanie informacji o użytkowniku
Używając identyfikatora autora podanego w poprzednich krokach, Wyświetl informacje w profilu użytkownika, zastępując `[AuthorId]` następujący adres URL:

    https://catalog.cortanaanalytics.com/users/[AuthorID]

Na przykład to żądanie adresu URL:
    
    https://catalog.cortanaanalytics.com/users/99F1F5C6260295F1078187FA179FBE08B618CB62129976F09C6AF0923B02A5BA

Zwraca odpowiedź, takich jak:

    {"entities_count":9,"contribution_score":86.351575190956922,"scored_at":"2018-05-07T14:30:25.9305671+00:00","contributed_at":"2018-05-07T14:26:55.0381756+00:00","created_at":"2017-12-15T00:49:15.6733094+00:00","updated_at":"2017-12-15T00:49:15.6733094+00:00","name":"First Last","slugs":["First-Last"],"tenant_id":"14b2744cf8d6418c87ffddc3f3127242","community_id":"9502630827244d60a1214f250e3bbca7","id":"99F1F5C6260295F1078187FA179FBE08B618CB62129976F09C6AF0923B02A5BA","_links":{"self":"https://catalog.azureml.net/tenants/14b2744cf8d6418c87ffddc3f3127242/communities/9502630827244d60a1214f250e3bbca7/users/99F1F5C6260295F1078187FA179FBE08B618CB62129976F09C6AF0923B02A5BA"},"etag":"\"2100d185-0000-0000-0000-5af063010000\""}


### <a name="view-public-entities"></a>Wyświetl jednostki publiczne

Interfejs API wykazu przechowuje informacje o opublikowanych jednostkach do Azure AI Gallery, które można także wyświetlić bezpośrednio w [witrynie sieci Web galerii AI](https://gallery.azure.ai/). 

Aby wyświetlić opublikowane jednostki, odwiedź następujący adres URL, zastępując `[AuthorId]` IDENTYFIKATORem autora uzyskanym w polu [Uzyskaj identyfikator autora](#get-an-author-id) powyżej.

    https://catalog.cortanaanalytics.com/entities?$filter=author/id eq '[AuthorId]'

Na przykład:

    https://catalog.cortanaanalytics.com/entities?$filter=author/id eq '99F1F5C6260295F1078187FA179FBE08B618CB62129976F09C6AF0923B02A5BA'

### <a name="view-unlisted-and-public-entities"></a>Wyświetlanie jednostek nieznajdujące się na liście i publicznych

To zapytanie wyświetla tylko publiczne jednostki. Aby wyświetlić wszystkie swoje jednostki, w tym nieznajdujące się na liście, zapewniają dostęp token uzyskany w poprzedniej sekcji.

1.  Za pomocą narzędzia takiego jak [Poster](https://www.getpostman.com)Utwórz żądanie HTTP GET w adresie URL wykazu, zgodnie z opisem w artykule [pobieranie tokenu dostępu](#get-your-access-token).
2.  Utwórz nagłówek żądania HTTP o nazwie `DataLabAccessToken`, z wartością ustawioną na token dostępu.
3.  Prześlij żądanie HTTP.

> [!TIP]
> Jeśli nieznajdujące się na liście jednostek nie są wyświetlane w odpowiedzi z interfejsu API wykazu, użytkownik może być nieprawidłowy lub wygasły token dostępu. Wyloguj się z Azure AI Gallery, a następnie powtórz kroki opisane w sekcji [Uzyskiwanie tokenu dostępu](#get-your-access-token) w celu odnowienia tokenu. 
