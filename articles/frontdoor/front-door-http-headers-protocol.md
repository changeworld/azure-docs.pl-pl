---
title: Usługa Azure drzwiami frontowymi — Obsługa protokołu nagłówki HTTP | Dokumentacja firmy Microsoft
description: Ten artykuł pomoże Ci zrozumieć obsługiwanych protokołów nagłówka HTTP przy wejściu
services: frontdoor
documentationcenter: ''
author: sharad4u
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/10/2018
ms.author: sharadag
ms.openlocfilehash: 0dcb769627714be9da55faf2a8e82c8750789498
ms.sourcegitcommit: 4ecc62198f299fc215c49e38bca81f7eb62cdef3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/24/2018
ms.locfileid: "47038854"
---
# <a name="azure-front-door-service---http-headers-protocol-support"></a>Usługa Azure drzwiami frontowymi — Obsługa protokołu nagłówki HTTP
W tym dokumencie przedstawiono protokołu, obsługującego usługi drzwiami frontowymi platformy Azure przy użyciu różnych części ścieżki wywołanie przedstawione na poniższej ilustracji. Poniższe sekcje zawierają więcej szczegółowych informacji nagłówków HTTP, który obsługuje drzwiami frontowymi.

![Azure protokołu nagłówki HTTP usługi drzwiami frontowymi][1]

>[!WARNING]
>Usługa drzwiami frontowymi nie zapewniają gwarancji na wszystkie nagłówki HTTP, które nie są opisane tutaj.

## <a name="1-client-to-front-door"></a>1. Klient drzwi
Drzwiami frontowymi akceptuje większość nagłówków z żądania przychodzącego (bez modyfikacji), jednak istnieją pewne zarezerwowanych nagłówki, które zostaną usunięte z żądania przychodzącego, jeśli są one wysyłane. W tym nagłówki z prefiksami następujące:
 - X-FD *
 - X-MS *

## <a name="2-front-door-to-backend"></a>2. Drzwi do wewnętrznej bazy danych

Drzwi wejściowe będą zawierać nagłówki z żądania przychodzącego, chyba że zostały one usunięte ze względu na ograniczenia, o których wspomniano powyżej. Drzwi zostaną dodane również następujące nagłówki:

| Nagłówek  | Przykład i opis |
| ------------- | ------------- |
| X-MS-Ref |  *X-MS-Ref: 0WrHgWgAAAACFupORp/8MS6vxhG/WUvawV1NURURHRTAzMjEARWRnZQ ==* </br> Jest to ciąg unikatowy odwołania, który identyfikuje żądanie obsługiwanej przez drzwi wejściowe. Koniecznie do rozwiązywania problemów, ponieważ jest używany do wyszukiwania dzienników dostępu.|
| X-MS-RequestChain |  *X-MS-RequestChain: przeskoki = 1* </br> Jest to nagłówek, w którym wejściu do wykrywania pętli żądania i użytkownicy nie powinna przyjmować zależności na nim. |
| X-MS-Via |  *X-MS-Via: Azure* </br> To jest dodawany przez drzwiami frontowymi, aby wskazać, że Azure/drzwiami frontowymi był pośrednich adresata dla żądania między klientem i wewnętrznej bazy danych. |

## <a name="3-front-door-to-client"></a>3. Drzwi do klienta

Poniżej przedstawiono nagłówki, które są wysyłane z wejściu do klientów. Wszelkie nagłówki wysyłane do drzwi z wewnętrznej bazy danych są przekazywane do klienta, jak również.

| Nagłówek  | Przykład |
| ------------- | ------------- |
| X-MS-Ref |  *X-MS-Ref: 0WrHgWgAAAACFupORp/8MS6vxhG/WUvawV1NURURHRTAzMjEARWRnZQ ==* </br> Jest to ciąg unikatowy odwołania, który identyfikuje żądanie obsługiwanej przez drzwi wejściowe. Koniecznie do rozwiązywania problemów, ponieważ jest używany do wyszukiwania dzienników dostępu.|

## <a name="next-steps"></a>Kolejne kroki

- Dowiedz się, jak [tworzenie drzwiami frontowymi](quickstart-create-front-door.md).
- Dowiedz się, [działania drzwiami frontowymi](front-door-routing-architecture.md).

<!--Image references-->
[1]: ./media/front-door-http-headers-protocol/front-door-protocol-summary.png