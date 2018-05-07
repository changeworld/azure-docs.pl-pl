---
title: Przy użyciu bazy danych SQL Azure stosu | Dokumentacja firmy Microsoft
description: Dowiedz się, jak można wdrożyć baz danych jako usługa na stosie Azure i Szybkie kroki wdrażania karty dostawcy zasobów programu SQL Server.
services: azure-stack
documentationCenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/01/2018
ms.author: jeffgilb
ms.reviewer: jeffgo
ms.openlocfilehash: c2686a2d5241af46e70263d1827028aa7e9b2138
ms.sourcegitcommit: c47ef7899572bf6441627f76eb4c4ac15e487aec
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/04/2018
---
# <a name="remove-the-sql-resource-provider"></a>Usuń dostawcę zasobów SQL

Aby usunąć dostawcę zasobów SQL, konieczne jest najpierw usunąć wszelkie zależności:

1. Upewnij się, że masz oryginalny pakiet wdrożeniowy, który został pobrany dla tej wersji karty dostawcy zasobów SQL.

2. Należy usunąć wszystkie bazy danych użytkownika od dostawcy zasobów. (Usunięcie bazy danych użytkownika nie powoduje usunięcia danych.) To zadanie powinno być wykonywane przez użytkowników, samodzielnie.

3. Administrator musi usunąć serwerami hostingu z karty dostawcy zasobów SQL.

4. Administrator, należy usunąć wszystkie plany odwołujące się do karty dostawcy zasobów SQL.

5. Administrator musi usunąć wszystkie jednostki SKU i przydziałów, które są skojarzone z kartą dostawcy zasobów programu SQL.

6. Uruchom ponownie skrypt wdrożenia wraz z następującymi elementami:
    - Odinstalować parametru
    - Punkty końcowe usługi Azure Resource Manager
    - DirectoryTenantID
    - Poświadczenia dla konta administratora usługi

