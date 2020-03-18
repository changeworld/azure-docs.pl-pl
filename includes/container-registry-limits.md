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
ms.sourcegitcommit: f97d3d1faf56fb80e5f901cd82c02189f95b3486
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/11/2020
ms.locfileid: "79117142"
---
| Zasób | Podstawowa | Standardowa (Standard) | Premium |
|---|---|---|---|
| Magazyn<sup>1</sup> | 10 GiB | 100 GiB| 500 GiB |
| Maksymalny rozmiar warstwy obrazu | 200 GiB | 200 GiB | 200 GiB |
| Operacje odczytu na minutę<sup>2, 3</sup> | 1000 | 3000 | 10 000 |
| Operacje zapisu na minutę<sup>2, 4</sup> | 100 | 500 | 2 000 |
| Przepustowość pobierania w MB/s<sup>2</sup> | 30 | 60 | 100 |
| Przepustowość przekazywania w MB/s<sup>2</sup> | 10 | 20 | 50 |
| Elementy webhook | 2 | 10 | 500 |
| Replikacja geograficzna | Nie dotyczy | Nie dotyczy | [Obsługiwane][geo-replication] |
| Zaufanie do zawartości | Nie dotyczy | Nie dotyczy | [Obsługiwane][content-trust] |
| Dostęp do sieci wirtualnej | Nie dotyczy | Nie dotyczy | [Wersja zapoznawcza][vnet] |
| Integracja z łączem prywatnym | Nie dotyczy | Nie dotyczy | [Wersja zapoznawcza][plink] |
| Klucze zarządzane przez klienta | Nie dotyczy | Nie dotyczy | [Wersja zapoznawcza][cmk] |
| Uprawnienia w zakresie repozytorium | Nie dotyczy | Nie dotyczy | [Wersja zapoznawcza][token]|
| &bull; Tokeny | Nie dotyczy | Nie dotyczy | 20 000 |
| &bull; Mapowania zakresu | Nie dotyczy | Nie dotyczy | 20 000 |
| &bull; Repozytoria na mapowanie zakresu | Nie dotyczy | Nie dotyczy | 500 |


<sup>1</sup> Określone limity magazynu to ilość *uwzględnionego* magazynu dla każdej warstwy. Naliczana jest dodatkowa dzienna stawka za GiB magazynu obrazów powyżej tych limitów. Aby uzyskać informacje o stawkach, zobacz [cennik usługi Azure Container Registry][pricing].

<sup>2</sup>*Operacje odczytu*, *operacje zapisu* i *przepustowość* są minimalnymi szacunkami. Usługa Azure Container Registry dąży do poprawy wydajności zgodnie z wymaganiami użycia.

<sup>3</sup>Polecenie [docker pull](https://docs.docker.com/registry/spec/api/#pulling-an-image) przekłada się na wiele operacji odczytu na podstawie liczby warstw obrazu i pobranie manifestu.

<sup>4</sup>Polecenie [docker push](https://docs.docker.com/registry/spec/api/#pushing-an-image) przekłada się na wiele operacji zapisu na podstawie liczby warstw, które muszą zostać wypchnięte. Polecenie `docker push` obejmuje *operacje odczytu* do pobrania manifestu dla istniejącego obrazu.

<!-- LINKS - External -->
[pricing]: https://azure.microsoft.com/pricing/details/container-registry/

<!-- LINKS - Internal -->
[geo-replication]: ../articles/container-registry/container-registry-geo-replication.md
[content-trust]: ../articles/container-registry/container-registry-content-trust.md
[vnet]: ../articles/container-registry/container-registry-vnet.md
[plink]: ../articles/container-registry/container-registry-private-link.md
[cmk]: ../articles/container-registry/container-registry-customer-managed-keys.md
[token]: ../articles/container-registry/container-registry-repository-scoped-permissions.md