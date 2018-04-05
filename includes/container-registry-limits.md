---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: container-registry
author: mmacy
ms.service: container-registry
ms.topic: include
ms.date: 03/23/2018
ms.author: marsma
ms.custom: include file
ms.openlocfilehash: 575483192954f4e05db50e701e223829e041cffc
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/05/2018
---
| Zasób | Podstawowa | Standardowa (Standard) | Premium |
|---|---|---|---|---|
| Magazyn | 10 GiB | 100 GiB| 500 GiB |
| ReadOps na minutę<sup>1, 2</sup> | 1000 | 3000 | 10 000 |
| WriteOps na minutę<sup>1, 3</sup> | 100 | 500 | 2000 |
| Pobierz MB/s przepustowości<sup>1</sup> | 30 | 60 | 100 |
| Przekaż MB/s przepustowości<sup>1</sup> | 10 | 20 | 50 |
| Elementy webhook | 2 | 10 | 100 |
| Replikacja geograficzna | ND | ND | [Obsługiwane *(wersja zapoznawcza)*](https://docs.microsoft.com/azure/container-registry/container-registry-geo-replication) |

<sup>1</sup> *ReadOps*, *WriteOps*, i *przepustowości* są szacowane wartości minimalnej. ACR dokłada starań zwiększyć wydajność, ponieważ wymaga użycia.

<sup>2</sup> [ściągania docker](https://docs.docker.com/registry/spec/api/#pulling-an-image) przekłada się wiele operacji odczytu na podstawie liczby warstw w obrazu, a także pobierania manifestu.

<sup>3</sup> [wypychania docker](https://docs.docker.com/registry/spec/api/#pushing-an-image) tłumaczy wiele operacji zapisu, na podstawie liczby warstw, które muszą zostać przeniesiony. A `docker push` obejmuje *ReadOps* można pobrać manifestu dla istniejącego obrazu.