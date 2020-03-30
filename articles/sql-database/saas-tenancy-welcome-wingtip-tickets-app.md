---
title: Witamy w aplikacji Wingtips
description: Dowiedz się więcej o modelach dzierżawy bazy danych i przykładowej aplikacji SaaS wingtips dla usługi Azure SQL Database w środowisku w chmurze.
keywords: samouczek usługi sql database
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: billgib
ms.date: 01/25/2019
ms.openlocfilehash: 4e0b3afe51ac7c7a6b9213fcee79af57cbbd8197
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73818329"
---
# <a name="the-wingtip-tickets-saas-application"></a>Aplikacja Wingtip Tickets SaaS

Ta sama aplikacja *Wingtip Tickets* SaaS jest implementowana w każdej z trzech próbek. Aplikacja jest prostą listą wydarzeń i biletami aplikacji SaaS kierowania małych miejscach - teatry, kluby, itp. Każde miejsce jest najemcą aplikacji i ma własne dane: szczegóły miejsca, listy wydarzeń, klientów, zamówienia biletów itp.  Aplikacja, wraz ze skryptami zarządzania i samouczki, prezentuje scenariusz SaaS end-to-end. Obejmuje to inicjowanie obsługi administracyjnej dzierżawców, monitorowanie i zarządzanie wydajnością, zarządzanie schematami oraz raportowanie i analizy między dzierżawami.

## <a name="three-saas-application-and-tenancy-patterns"></a>Trzy wzorce aplikacji i najmu SaaS

Dostępne są trzy wersje aplikacji; każdy eksploruje inny wzorzec dzierżawy bazy danych w usłudze Azure SQL Database.  Pierwszy używa autonomicznej aplikacji na dzierżawę z własną bazą danych. Drugi używa aplikacji z wieloma dzierżawami z bazą danych na dzierżawę. Trzeci przykład używa aplikacji z wieloma dzierżawami z podzielonymi na fragmenty wielodostępne bazy danych.

![Trzy wzorce najmu][image-three-tenancy-patterns]

 Każdy przykład zawiera kod aplikacji, a także skrypty zarządzania i samouczki, które eksplorują zakres wzorców projektowania i zarządzania.  Każda próbka wdraża w mniej niż pięć minut.  Wszystkie trzy można wdrożyć obok siebie, dzięki czemu można porównać różnice w projektowaniu i zarządzaniu.

## <a name="standalone-application-per-tenant-pattern"></a>Autonomiczna aplikacja na wzorzec dzierżawy

Autonomiczna aplikacja na wzorzec dzierżawy używa aplikacji pojedynczej dzierżawy z bazą danych dla każdej dzierżawy. Każda aplikacja dzierżawy, w tym jej bazy danych, jest wdrażany w oddzielnej grupie zasobów platformy Azure. Grupa zasobów można wdrożyć w subskrypcji dostawcy usług lub subskrypcji dzierżawy i zarządzane przez dostawcę w imieniu dzierżawy. Autonomiczna aplikacja na wzorzec dzierżawy zapewnia największą izolację dzierżawy, ale jest zazwyczaj najdroższa, ponieważ nie ma możliwości udostępniania zasobów między wieloma dzierżawcami.  Ten wzorzec jest dobrze nadaje się do aplikacji, które mogą być bardziej złożone i które są wdrażane do mniejszej liczby dzierżaw.  W przypadku wdrożeń autonomicznych aplikację można dostosować dla każdej dzierżawy łatwiej niż w innych wzorcach.  

Sprawdź [samouczki][docs-tutorials-for-wingtip-sa] i kod na GitHub [.../Microsoft/WingtipTicketsSaaS-StandaloneApp][github-code-for-wingtip-sa].

## <a name="database-per-tenant-pattern"></a>Baza danych na wzorzec dzierżawy

Wzorzec bazy danych na dzierżawę jest skuteczny dla dostawców usług, którzy zajmują się izolacją dzierżawy i chcą uruchomić scentralizowaną usługę, która umożliwia oszczędne korzystanie z zasobów udostępnionych. Baza danych jest tworzona dla każdego miejsca lub dzierżawy, a wszystkie bazy danych są zarządzane centralnie. Bazy danych mogą być hostowane w pulach elastycznych, aby zapewnić ekonomiczne i łatwe zarządzanie wydajnością, co wykorzystuje nieprzewidywalne wzorce obciążenia dzierżawców. Baza danych wykazu przechowuje mapowanie między dzierżawcami i ich bazami danych. To mapowanie jest zarządzane przy użyciu funkcji zarządzania mapami niezależnego fragmentu [biblioteki klienta elastycznej bazy danych,](sql-database-elastic-database-client-library.md)która zapewnia efektywne zarządzanie połączeniami z aplikacją.

Sprawdź [samouczki][docs-tutorials-for-wingtip-dpt] i kod na GitHub [.../Microsoft/WingtipTicketsSaaS-DbPerTenant][github-code-for-wingtip-dpt].

## <a name="sharded-multi-tenant-database-pattern"></a>Wzorzec bazy danych z wieloma dzierżawami podzielonymi na fragmenty

Bazy danych z wieloma dzierżawcami są skuteczne dla dostawców usług poszukujących niższych kosztów na dzierżawę i w porządku z ograniczoną izolacją dzierżawy. Ten wzorzec umożliwia pakowanie dużej liczby dzierżawców do pojedynczej bazy danych, co powoduje obniżenie kosztu dzierżawcy. Prawie nieskończona skala jest możliwe przez dzielenie na fragmenty dzierżawy w wielu bazach danych. Baza danych wykazu mapuje dzierżawców do baz danych.  

Ten wzorzec umożliwia również model *hybrydowy,* w którym można zoptymalizować pod kątem kosztów z wieloma dzierżawcami w bazie danych lub zoptymalizować pod kątem izolacji z jednej dzierżawy w ich własnej bazie danych. Wybór można dokonać na zasadzie dzierżawy przez dzierżawcę, gdy dzierżawa jest aprowizowana lub później, bez wpływu na aplikację.  Ten model może być skutecznie stosowany, gdy grupy dzierżawców muszą być traktowane inaczej. Na przykład tanich dzierżawców można przypisać do udostępnionych baz danych, podczas gdy dzierżawy premium mogą być przypisane do własnych baz danych. 

Sprawdź [samouczki][docs-tutorials-for-wingtip-mt] i kod na GitHub [.../Microsoft/WingtipTicketsSaaS-MultiTenantDb][github-code-for-wingtip-mt].

## <a name="next-steps"></a>Następne kroki

#### <a name="conceptual-descriptions"></a>Opisy koncepcyjne

- Bardziej szczegółowe wyjaśnienie wzorców najmu aplikacji jest dostępne w [wzorcach dzierżawy bazy danych SaaS z wieloma dzierżawami][saas-tenancy-app-design-patterns-md]

#### <a name="tutorials-and-code"></a>Samouczki i kod

- Autonomiczna aplikacja na dzierżawę:
    - [Samouczki dotyczące samodzielnej aplikacji][docs-tutorials-for-wingtip-sa].
    - [Kod dla samodzielnej aplikacji, na GitHub][github-code-for-wingtip-sa].

- Baza danych na dzierżawcę:
    - [Samouczki dla bazy danych dla dzierżawcy][docs-tutorials-for-wingtip-dpt].
    - [Kod bazy danych dla dzierżawy, w usłudze GitHub][github-code-for-wingtip-dpt].

- Podzielony na fragmenty wielodostępny:
    - [Samouczki dla podzielonej wielodostępne.][docs-tutorials-for-wingtip-mt]
    - [Kod dla podzielonej wielodostępnych, w usłudze GitHub][github-code-for-wingtip-mt].



<!-- Image references. -->

[image-three-tenancy-patterns]: media/saas-tenancy-welcome-wingtip-tickets-app/three-tenancy-patterns.png "Trzy wzorce najmu."

<!-- Docs.ms.com references. -->

[saas-tenancy-app-design-patterns-md]: saas-tenancy-app-design-patterns.md

<!-- WWWeb http references. -->

[docs-tutorials-for-wingtip-sa]: https://aka.ms/wingtipticketssaas-sa
[github-code-for-wingtip-sa]: https://github.com/Microsoft/WingtipTicketsSaaS-StandaloneApp

[docs-tutorials-for-wingtip-dpt]: https://aka.ms/wingtipticketssaas-dpt
[github-code-for-wingtip-dpt]: https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant

[docs-tutorials-for-wingtip-mt]: https://aka.ms/wingtipticketssaas-mt
[github-code-for-wingtip-mt]: https://github.com/Microsoft/WingtipTicketsSaaS-MultiTenantDb

