---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: azure-policy
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 09/18/2018
ms.author: dacoulte
ms.custom: include file
ms.openlocfilehash: 76e161be1adca4aa2ec7b682a13b0a42e4b79412
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/24/2018
ms.locfileid: "47003731"
---
### <a name="sql-servers"></a>Serwery SQL Server

|  |  |
|---------|---------|
| [Audit no Azure Active Directory administrator](../articles/governance/policy/samples/audit-no-aad-admin.md) (Sprawdzanie, czy nie skonfigurowano konta administratora usługi Azure Active Directory) | Sprawdzanie, czy do programu SQL Server nie przypisano administratora usługi Azure Active Directory. |
| [Audit Server level threat detection setting](../articles/governance/policy/samples/audit-sql-ser-threat-det-setting.md) (Przeprowadzanie inspekcji ustawień wykrywania zagrożeń na poziomie serwera) | Przeprowadza inspekcję zasad alertu zabezpieczeń bazy danych SQL, dla których nie ustawiono określonego stanu. Należy określić wartość, która wskazuje, czy wykrywanie zagrożeń ma być włączone czy wyłączone.  |
| [Audit SQL Server audit settings](../articles/governance/policy/samples/sql-server-audit.md) (Przeprowadzanie inspekcji ustawień inspekcji programu SQL Server) | Przeprowadza inspekcję programu SQL Server w zależności od tego, czy włączono ustawienia inspekcji. |
| [Audit SQL Server Level Audit Setting](../articles/governance/policy/samples/audit-sql-ser-leve-audit-setting.md) (Przeprowadzanie inspekcji ustawień inspekcji na poziomie programu SQL Server) | Sprawdza, czy ustawienia inspekcji programu SQL Server nie są zgodne z określonym ustawieniem. Należy określić wartość, która wskazuje, czy ustawienia inspekcji mają być włączone czy wyłączone. |
| [Require SQL Server Version 12.0](../articles/governance/policy/samples/req-sql-12.md) (Wymaganie programu SQL Server w wersji 12.0) | Wymaga używania wersji 12.0 programu SQL Server.  |