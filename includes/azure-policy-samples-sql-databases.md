---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: azure-policy
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 05/17/2018
ms.author: dacoulte
ms.custom: include file
ms.openlocfilehash: 50a6388a95bce14bc9af7e0c9a5387e148f6fa73
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34664603"
---
### <a name="sql-databases"></a>Bazy danych SQL

|  |  |
|---------|---------|
| [Allowed SQL DB SKUs](../articles/azure-policy/scripts/allowed-sql-db-skus.md) (Dozwolone jednostki SKU usługi SQL DB) | Wymaga, aby bazy danych SQL używały zatwierdzonych jednostek SKU. Należy określić tablicę identyfikatorów dozwolonych jednostek SKU lub tablicę nazw dozwolonych jednostek SKU. |
| [Audit DB level threat detection setting](../articles/azure-policy/scripts/audit-db-threat-det-setting.md) (Przeprowadzanie inspekcji ustawień wykrywania zagrożeń na poziomie bazy danych) | Przeprowadza inspekcję zasad alertu zabezpieczeń bazy danych SQL, dla których nie ustawiono określonego stanu. Należy określić wartość, która wskazuje, czy wykrywanie zagrożeń ma być włączone czy wyłączone.  |
| [Audit SQL Database encryption](../articles/azure-policy/scripts/sql-database-encryption-audit.md) (Inspekcja szyfrowania w usłudze SQL Database) | Sprawdza, czy usługa SQL Database nie ma włączonego przezroczystego szyfrowania danych. |
| [Audit SQL DB Level Audit Setting](../articles/azure-policy/scripts/audit-sql-db-audit-setting.md) (Przeprowadzanie inspekcji ustawień inspekcji na poziomie usługi SQL Database) | Sprawdza, czy ustawienia inspekcji usługi SQL Database nie są zgodne z określonym ustawieniem. Należy określić wartość, która wskazuje, czy ustawienia inspekcji mają być włączone czy wyłączone.  |
| [Audit transparent data encryption status](../articles/azure-policy/scripts/audit-trans-data-enc-status.md) (Inspekcja stanu przezroczystego szyfrowania danych) | Sprawdza, czy usługa SQL Database nie ma włączonego przezroczystego szyfrowania danych.  |