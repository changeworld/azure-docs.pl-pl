---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: container-registry
author: dlepow
ms.service: container-registry
ms.topic: include
ms.date: 05/14/2019
ms.author: danlep
ms.custom: include file
ms.openlocfilehash: ee8ff3529524a63ca2e54a64327570197f363538
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/18/2019
ms.locfileid: "67183595"
---
| Resource | Podstawowa | Standardowa (Standard) | Premium |
|---|---|---|---|
| Storage<sup>1</sup> | 10 GiB | 100 GiB| 500 GiB |
| Maksymalny rozmiar warstwy | 200 GiB | 200 GiB | 200 GiB |
| ReadOps minutę<sup>2, 3</sup> | 1000 | 3000 | 10 000 |
| WriteOps minutę<sup>2, 4</sup> | 100 | 500 | 2000 |
| Pobierz przepustowości w MB/s<sup>2</sup> | 30 | 60 | 100 |
| Przekaż przepustowości w MB/s<sup>2</sup> | 10 | 20 | 50 |
| Elementy webhook | 2 | 10 | 100 |
| Replikacja geograficzna | ND | ND | [Obsługiwane][geo-replication] |
| Zaufanie do zawartości | ND | ND | [Obsługiwane][content-trust] |

<sup>1</sup>limity określonego magazynu są ilość *uwzględnione* magazynu dla każdej warstwy. Opłaty są naliczane dodatkowe stawka dzienna za GiB do przechowywania obrazu powyżej tych limitów. Szybkość informacji, zobacz [cennika usługi Azure Container Registry][pricing].

<sup>2</sup>*ReadOps*, *WriteOps*, i *przepustowości* szacunkowe minimalnej. Usługa Azure Container Registry dokłada wszelkich starań zwiększyć wydajność, ponieważ wymaga użycia.

<sup>3</sup>A [polecenie docker pull](https://docs.docker.com/registry/spec/api/#pulling-an-image) przekłada się na wiele operacji odczytu, w oparciu o liczbę warstw obrazu, a także pobierania manifestu.

<sup>4</sup>A [docker push](https://docs.docker.com/registry/spec/api/#pushing-an-image) przekłada się na wiele operacji zapisu, na podstawie liczby warstw, które muszą zostać przeniesiony. A `docker push` obejmuje *ReadOps* można pobrać manifestu istniejącego obrazu.

<!-- LINKS - External -->
[pricing]: https://azure.microsoft.com/pricing/details/container-registry/

<!-- LINKS - Internal -->
[geo-replication]: ../articles/container-registry/container-registry-geo-replication.md
[content-trust]: ../articles/container-registry/container-registry-content-trust.md
