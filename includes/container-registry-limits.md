---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: container-registry
author: mmacy
ms.service: container-registry
ms.topic: include
ms.date: 05/29/2018
ms.author: marsma
ms.custom: include file
ms.openlocfilehash: 92a5d162e7a0b2c752a2f8e9c5941edf43e539e3
ms.sourcegitcommit: df50934d52b0b227d7d796e2522f1fd7c6393478
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/12/2018
ms.locfileid: "38991085"
---
| Zasób | Podstawowa | Standardowa (Standard) | Premium |
|---|---|---|---|---|
| Magazyn | 10 GiB | 100 GiB| 500 GiB |
| Maksymalny rozmiar warstwy obrazu | 20 giB | 20 giB | 50 giB |
| ReadOps minutę<sup>1, 2</sup> | 1000 | 3,000 | 10 000 |
| WriteOps minutę<sup>1, 3</sup> | 100 | 500 | 2,000 |
| Pobierz przepustowości w MB/s<sup>1</sup> | 30 | 60 | 100 |
| Przekaż przepustowości w MB/s<sup>1</sup> | 10 | 20 | 50 |
| Elementy webhook | 2 | 10 | 100 |
| Replikacja geograficzna | ND | ND | [Obsługiwane](https://docs.microsoft.com/azure/container-registry/container-registry-geo-replication) |

<sup>1</sup> *ReadOps*, *WriteOps*, i *przepustowości* szacunkowe minimalnej. Rejestru Azure container Registry dokłada wszelkich starań zwiększyć wydajność, ponieważ wymaga użycia.

<sup>2</sup> [polecenie docker pull](https://docs.docker.com/registry/spec/api/#pulling-an-image) przekłada się na wiele operacji odczytu, w oparciu o liczbę warstw obrazu, a także pobierania manifestu.

<sup>3</sup> [docker push](https://docs.docker.com/registry/spec/api/#pushing-an-image) przekłada się na wiele operacji zapisu, na podstawie liczby warstw, które muszą zostać przeniesiony. A `docker push` obejmuje *ReadOps* można pobrać manifestu istniejącego obrazu.
