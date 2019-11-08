---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: container-registry
author: dlepow
ms.service: container-registry
ms.topic: include
ms.date: 11/05/2019
ms.author: danlep
ms.custom: include file
ms.openlocfilehash: bc4ac68cb415a43ac34d36afc2adc30307e6d37c
ms.sourcegitcommit: 827248fa609243839aac3ff01ff40200c8c46966
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/07/2019
ms.locfileid: "73795269"
---
| Zasób | Podstawowa | Standardowa | Premium |
|---|---|---|---|
| Magazyn<sup>1</sup> | 10 GiB | 100 GiB| 500 GiB |
| Maksymalny rozmiar warstwy obrazu | 200 GiB | 200 GiB | 200 GiB |
| ReadOps na minutę<sup>2, 3</sup> | 1000 | 3,000 | 10 000 |
| WriteOps na minutę<sup>2, 4</sup> | 100 | 500 | 2 000 |
| Pobierz przepustowość<sup>2</sup> MB/s | 30 | 60 | 100 |
| Przepustowość przekazywania<sup>2</sup> MB/s | 10 | 20 | 50 |
| Elementy webhook | 2 | 10 | 100 |
| Replikacja geograficzna | Nie dotyczy | Nie dotyczy | [Obsługiwane][geo-replication] |
| Zaufanie do zawartości | Nie dotyczy | Nie dotyczy | [Obsługiwane][content-trust] |
| Dostęp do sieci wirtualnej | Nie dotyczy | Nie dotyczy | [Wersja zapoznawcza][vnet] |
| Uprawnienia w zakresie repozytorium | Nie dotyczy | Nie dotyczy | [Wersja zapoznawcza][token]|
| Tokeny &bull; | Nie dotyczy | Nie dotyczy | 20 000 |
| mapy zakresu &bull; | Nie dotyczy | Nie dotyczy | 20 000 |
| &bull; repozytoria na mapę zakresu | Nie dotyczy | Nie dotyczy | 500 |


<sup>1</sup> Określone limity magazynu to ilość *dołączonego* magazynu dla każdej warstwy. Opłata jest naliczana za dodatkową dzienną stawkę za GiB dla magazynu obrazów powyżej tych limitów. Aby uzyskać informacje o stawkach, zobacz [Cennik usługi Azure Container Registry][pricing].

<sup>2</sup>*ReadOps*, *WriteOps*i *przepustowość* są minimalnymi oszacowaniami. Azure Container Registry dąży do poprawy wydajności, ponieważ wymaga użycia.

<sup>3</sup> [Docker pull](https://docs.docker.com/registry/spec/api/#pulling-an-image) Wykonuje translację na wiele operacji odczytu na podstawie liczby warstw w obrazie oraz pobierania manifestu.

<sup>4</sup> [Wypychanie Docker](https://docs.docker.com/registry/spec/api/#pushing-an-image) jest tłumaczone na wiele operacji zapisu na podstawie liczby warstw, które muszą zostać wypchnięte. `docker push` zawiera *ReadOps* do pobrania manifestu dla istniejącego obrazu.

<!-- LINKS - External -->
[pricing]: https://azure.microsoft.com/pricing/details/container-registry/

<!-- LINKS - Internal -->
[geo-replication]: ../articles/container-registry/container-registry-geo-replication.md
[content-trust]: ../articles/container-registry/container-registry-content-trust.md
[vnet]: ../articles/container-registry/container-registry-vnet.md
[token]: ../articles/container-registry/container-registry-repository-scoped-permissions.md