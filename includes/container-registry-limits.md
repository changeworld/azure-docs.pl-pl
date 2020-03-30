---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: container-registry
author: dlepow
ms.service: container-registry
ms.topic: include
ms.date: 03/11/2020
ms.author: danlep
ms.custom: include file
ms.openlocfilehash: 0090f02382e024e5539383328b55d58798002d63
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79117142"
---
| Zasób | Podstawowa (Basic) | Standardowa | Premium |
|---|---|---|---|
| Przechowywanie<sup>1</sup> | 10 Gib | 100 Gib| 500 Gib |
| Maksymalny rozmiar warstwy obrazu | 200 Gib | 200 Gib | 200 Gib |
| ReadOps na minutę<sup>2, 3</sup> | 1000 | 3000 | 10 000 |
| WriteOps na minutę<sup>2, 4</sup> | 100 | 500 | 2000 |
| Pobieranie przepustowości MBps<sup>2</sup> | 30 | 60 | 100 |
| Przesyłanie przepustowości<sup>MB/s 2</sup> | 10 | 20 | 50 |
| Elementy webhook | 2 | 10 | 500 |
| Replikacja geograficzna | Nie dotyczy | Nie dotyczy | [Obsługiwane][geo-replication] |
| Zaufanie do zawartości | Nie dotyczy | Nie dotyczy | [Obsługiwane][content-trust] |
| Dostęp do sieci wirtualnej | Nie dotyczy | Nie dotyczy | [Podgląd][vnet] |
| Integracja łączy prywatnych | Nie dotyczy | Nie dotyczy | [Podgląd][plink] |
| Klucze zarządzane przez klienta | Nie dotyczy | Nie dotyczy | [Podgląd][cmk] |
| Uprawnienia o zakresie repozytorium | Nie dotyczy | Nie dotyczy | [Podgląd][token]|
| &bull;Tokeny | Nie dotyczy | Nie dotyczy | 20 000 |
| &bull;Mapy zakresu | Nie dotyczy | Nie dotyczy | 20 000 |
| &bull;Repozytoria na mapę zakresu | Nie dotyczy | Nie dotyczy | 500 |


<sup>1.</sup> Określone limity magazynowania to ilość *dołączonego* magazynu dla każdej warstwy. Za przechowywanie obrazów powyżej tych limitów pobierana jest dodatkowa dzienna stawka za dysk gib. Aby uzyskać informacje o stawkach, zobacz [Cennik rejestru kontenerów platformy Azure][pricing].

<sup>2</sup>*ReadOps*, *WriteOps*i *Przepustowość* są minimalne szacunki. Usługa Azure Container Registry dąży do zwiększenia wydajności w miarę użycia.

<sup>3</sup> [Ściąganie docker](https://docs.docker.com/registry/spec/api/#pulling-an-image) przekłada się na wiele operacji odczytu na podstawie liczby warstw na obrazie oraz pobierania manifestu.

<sup>4</sup> [Wypychanie docker](https://docs.docker.com/registry/spec/api/#pushing-an-image) przekłada się na wiele operacji zapisu, na podstawie liczby warstw, które muszą być wypychane. A `docker push` zawiera *readops* do pobierania manifestu dla istniejącego obrazu.

<!-- LINKS - External -->
[pricing]: https://azure.microsoft.com/pricing/details/container-registry/

<!-- LINKS - Internal -->
[geo-replication]: ../articles/container-registry/container-registry-geo-replication.md
[content-trust]: ../articles/container-registry/container-registry-content-trust.md
[vnet]: ../articles/container-registry/container-registry-vnet.md
[plink]: ../articles/container-registry/container-registry-private-link.md
[cmk]: ../articles/container-registry/container-registry-customer-managed-keys.md
[token]: ../articles/container-registry/container-registry-repository-scoped-permissions.md