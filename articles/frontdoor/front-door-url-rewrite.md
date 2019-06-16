---
title: Azure Front drzwiczki Service — ponowne zapisywanie adresów URL | Dokumentacja firmy Microsoft
description: Ten artykuł pomoże Ci zrozumieć, jak Azure drzwiami frontowymi usługa ma ponownego zapisywania adresów URL dla trasy, jeśli skonfigurowane.
services: front-door
documentationcenter: ''
author: sharad4u
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/10/2018
ms.author: sharadag
ms.openlocfilehash: dc2126276e3e8e0d35ce8ed1f835544386659eff
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60736189"
---
# <a name="url-rewrite-custom-forwarding-path"></a>Ponowne zapisywanie adresów URL (ścieżka przekazywanie niestandardowych)
Usługa drzwiami frontowymi obsługuje ponowne zapisywanie adresów URL, umożliwiając konfigurowanie opcjonalny **ścieżki przekazywania niestandardowego** do użycia podczas konstruowania żądania do zaplecza. Domyślnie jeśli nie ma określonej niestandardowej ścieżki przesyłania dalej, usługa Front Door kopiuje przychodzącą ścieżkę adresu URL do adresu URL użytego w przesłanym dalej żądaniu. Nagłówek Host użyty w przesłanym dalej żądaniu jest skonfigurowany dla wybranego zaplecza. Odczyt [nagłówek hosta zaplecza](front-door-backend-pool.md#hostheader) Aby dowiedzieć się, jak działa i jak można go skonfigurować.

Zaawansowana część ponowne zapisywanie adresów URL przy użyciu ścieżki niestandardowe przekazywania jest, spowoduje to skopiowanie dowolnej części ścieżki przychodzącej, który pasuje do ścieżki symboli wieloznacznych w ścieżce przekazywane (te segmenty ścieżki są **zielony** segmentów w poniższym przykładzie):
</br>
![Ponowne zapisywanie adresów URL w drzwi wejściowe platformy Azure][1]

## <a name="url-rewrite-example"></a>Przykład ponownego zapisywania adresu URL
Następujące hosty frontonu i skonfigurowanych ścieżek, należy wziąć pod uwagę reguły routingu:

| Hosts      | Ścieżki       |
|------------|-------------|
| www\.contoso.com | /\*         |
|            | /foo        |
|            | /foo/\*     |
|            | /foo/paska /\* |

Pierwszą kolumnę w poniższej tabeli przedstawiono przykłady żądań przychodzących i druga kolumna zawiera, jaki byłyby pasującej trasy "większość specific" "Path".  Trzecia i kolejnych kolumn pierwszy wiersz w tabeli przedstawiono przykłady skonfigurowanego **ścieżki przekazywania niestandardowe**, z użyciem usług rest wierszach tych kolumn reprezentujących przykłady co przekazane żądanie byłaby to ścieżka Jeśli był zgodny z na żądanie, w tym wierszu.

Na przykład, jeśli możemy odczytać w drugim wierszu, jego jest informacją, że dla żądania przychodzącego `www.contoso.com/sub`, jeśli ścieżka przekazywanie niestandardowych `/`, a następnie przekazywane ścieżka byłaby `/sub`. Jeśli ścieżka przekazywanie niestandardowych `/fwd/`, a następnie przekazywane ścieżka byłaby `/fwd/sub`. I tak dalej dla pozostałych kolumn. **Wyróżniono** części ścieżki poniżej reprezentują te części, które są częścią dopasowanie z symbolami wieloznacznymi.


| Żądanie przychodzące       | Ścieżka specyficzne dla większości dopasowania | /          | /FWD/          | /foo/          | /foo/paska /          |
|------------------------|--------------------------|------------|----------------|----------------|--------------------|
| www\.contoso.com/            | /\*                      | /          | /FWD/          | /foo/          | /foo/paska /          |
| www\.contoso.com/**sub**     | /\*                      | /**Sub**   | /FWD/**sub**   | /foo/**sub**   | /foo/paska/**sub**   |
| www\.contoso.com/**a/b/c.**   | /\*                      | /**a/b/c.** | /fwd/**a/b/c** | /foo/**a/b/c.** | /foo/paska/**a/b/c.** |
| www\.contoso.com/foo         | /foo                     | /          | /FWD/          | /foo/          | /foo/paska /          |
| www\.contoso.com/foo/        | /foo/\*                  | /          | /FWD/          | /foo/          | /foo/paska /          |
| www\.contoso.com/foo/**paska** | /foo/\*                  | /**Pasek**   | /FWD/**paska**   | /foo/**paska**   | /foo/paska/**paska**   |


## <a name="optional-settings"></a>Ustawienia opcjonalne
Dostępne są dodatkowe ustawienia opcjonalne, które można również określić dla danego ustawienia reguły routingu:

* **Konfiguracja w pamięci podręcznej** — Jeśli wyłączone lub nie określono żądania, które odpowiadają tej reguły routingu nie podejmie próbę użycia zawartości w pamięci podręcznej, a zamiast tego będzie zawsze fetch z wewnętrznej bazy danych. Przeczytaj więcej na temat [buforowania przy wejściu](front-door-caching.md).



## <a name="next-steps"></a>Kolejne kroki

- Dowiedz się, jak [utworzyć usługę Front Door](quickstart-create-front-door.md).
- Dowiedz się, [jak działa usługa Front Door](front-door-routing-architecture.md).

<!--Image references-->
[1]: ./media/front-door-url-rewrite/front-door-url-rewrite-example.jpg