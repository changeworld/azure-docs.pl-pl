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
ms.openlocfilehash: 298b92205e73b3623db02fb1dd803edac8379700
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34664591"
---
### <a name="sql-servers"></a>Serwery SQL Server

|  |  |
|---------|---------|
| [Audit no Azure Active Directory administrator](../articles/azure-policy/scripts/audit-no-aad-admin.md) (Sprawdzanie, czy nie skonfigurowano konta administratora usługi Azure Active Directory) | Sprawdzanie, czy do programu SQL Server nie przypisano administratora usługi Azure Active Directory. |
| [Audit Server level threat detection setting](../articles/azure-policy/scripts/audit-sql-ser-threat-det-setting.md) (Przeprowadzanie inspekcji ustawień wykrywania zagrożeń na poziomie serwera) | Przeprowadza inspekcję zasad alertu zabezpieczeń bazy danych SQL, dla których nie ustawiono określonego stanu. Należy określić wartość, która wskazuje, czy wykrywanie zagrożeń ma być włączone czy wyłączone.  |
| [Audit SQL Server audit settings](../articles/azure-policy/scripts/sql-server-audit.md) (Przeprowadzanie inspekcji ustawień inspekcji programu SQL Server) | Przeprowadza inspekcję programu SQL Server w zależności od tego, czy włączono ustawienia inspekcji. |
| [Audit SQL Server Level Audit Setting](../articles/azure-policy/scripts/audit-sql-ser-leve-audit-setting.md) (Przeprowadzanie inspekcji ustawień inspekcji na poziomie programu SQL Server) | Sprawdza, czy ustawienia inspekcji programu SQL Server nie są zgodne z określonym ustawieniem. Należy określić wartość, która wskazuje, czy ustawienia inspekcji mają być włączone czy wyłączone. |
| [Require SQL Server Version 12.0](../articles/azure-policy/scripts/req-sql-12.md) (Wymaganie programu SQL Server w wersji 12.0) | Wymaga używania wersji 12.0 programu SQL Server.  |