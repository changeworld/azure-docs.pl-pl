---
title: Uzyskiwanie ciągu połączenia z witryny Azure portal
description: Uzyskiwanie ciągu połączenia z witryny Azure portal
keywords: połączenie sql,parametry połączenia
services: sql-database
author: dalechen
manager: craigg
ms.service: sql-database
ms.custom: develop apps
ms.topic: include
ms.date: 07/13/2018
ms.author: ninarn
ms.openlocfilehash: 6ead2e0ea326b5c3f2e76e7aa9cc4ab3c50d4154
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "67183311"
---
### <a name="obtain-the-connection-string-from-the-azure-portal"></a>Uzyskiwanie ciągu połączenia z witryny Azure portal
Użyj [witryny Azure Portal,](https://portal.azure.com/) aby uzyskać parametry połączenia, które jest niezbędne dla programu klienckiego do interakcji z usługą Azure SQL Database.

1. Wybierz **wszystkie usługi** > **baz danych SQL**.

2. Wprowadź nazwę bazy danych w polu tekstowym filtru w lewym górnym rogu bloku **baz danych SQL.**

3. Wybierz wiersz bazy danych.

4. Po pojawi się blok dla bazy danych, dla wygody wizualnej wybierz **przyciski Minimalizuj,** aby zwinąć ostrza używane do przeglądania i filtrowania bazy danych.

5. W bloku bazy danych wybierz pozycję **Pokaż parametry połączenia bazy danych**.

6. Skopiuj odpowiedni parametry połączenia. czyli jeśli zamierzasz korzystać z biblioteki połączeń ADO.NET, skopiuj odpowiedni ciąg z karty **ADO.NET.**

    ![Kopiowanie ciągu połączenia ADO dla bazy danych][20-CopyAdoConnectionString]

7. W razie potrzeby edytuj ciąg połączenia. czyli włóż hasło do ciągu połączenia lub usuń&lt;"@&gt;nazwa_serwera" z nazwy użytkownika, jeśli nazwa użytkownika lub nazwa serwera są zbyt długie.

8. W tym czy innym formacie wklej informacje o ciągu połączenia do kodu programu klienta.

Aby uzyskać więcej informacji, zobacz [Parametry połączenia i pliki konfiguracyjne](https://msdn.microsoft.com/library/ms254494.aspx).

<!-- Image references. -->



[20-CopyAdoConnectionString]: ./media/sql-database-include-connection-string-20-portalshots/connqry-connstr-b.png


<!--
These three includes/ files are a sequenced set, but you can pick and choose:

includes/sql-database-include-connection-string-20-portalshots.md
includes/sql-database-include-connection-string-30-compare.md
includes/sql-database-include-connection-string-40-config.md
-->
