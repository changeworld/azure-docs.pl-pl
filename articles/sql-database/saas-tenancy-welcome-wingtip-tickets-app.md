---
title: Witamy w aplikacji Wingtips — usługi Azure SQL Database | Dokumentacja firmy Microsoft
description: Informacje dotyczące bazy danych dzierżawy modeli i przykładowa aplikacja Wingtips SaaS, usługi Azure SQL Database w środowisku chmury.
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
manager: craigg
ms.date: 01/25/2019
ms.openlocfilehash: 963d7d44ef3ef77604fc5a9faac479a9e4c91ee6
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "61487384"
---
# <a name="the-wingtip-tickets-saas-application"></a>Aplikacja SaaS o nazwie Wingtip Tickets

Taki sam *Wingtip Tickets* aplikacji SaaS jest implementowany we wszystkich trzech próbek. Aplikacja jest zdarzenie proste, wyświetlanie listy i tworzenia biletów aplikacji SaaS, przeznaczone dla małych venues - wykładowych, kluby itp. Każde miejsce to dzierżawy aplikacji, a własnych danych: szczegóły miejsca, listy zdarzeń, klientów, bilet zamówienia itp.  Aplikacja, wraz z samouczków i skrypty zarządzania prezentuje scenariusza rozwiązania SaaS end-to-end. Obejmuje to dzierżaw inicjowania obsługi administracyjnej, monitorowanie i zarządzanie wydajnością, zarządzania schematami i międzydzierżawowa raportowania i analizy.

## <a name="three-saas-application-and-tenancy-patterns"></a>Trzy wzorce dzierżawców i aplikacji SaaS

Trzy wersje aplikacji są dostępne; Każdy odkrywa wzorzec dzierżawy innej bazy danych w usłudze Azure SQL Database.  Pierwszy używa autonomiczną aplikacją na dzierżawę z własną bazę danych. W drugiej zastosowano aplikacji z wieloma dzierżawami z bazą danych na dzierżawę. Trzeci przykład używa aplikacji z wieloma dzierżawami z podzielonych na fragmenty wielodostępnych baz danych.

![Trzy wzorce dzierżawy][image-three-tenancy-patterns]

 Każdy przykład zawiera kod aplikacji i skrypty zarządzania i samouczki, przedstawiających szeroką gamę wzorców projektowania i zarządzania.  Każda próbka wdraża w czasie krótszym tego pięć minut.  Wszystkie trzy może być wdrożone side-by-side, więc można porównać projektowania i zarządzania.

## <a name="standalone-application-per-tenant-pattern"></a>Aplikacja autonomiczna na wzorzec dzierżawy

Aplikacji autonomicznej na wzorzec dzierżawy za pomocą aplikacji pojedynczej dzierżawy bazy danych dla każdego dzierżawcy. Każdej dzierżawy aplikacji, w tym swojej bazy danych, jest wdrażany do grupy oddzielne zasoby platformy Azure. Grupa zasobów może wdrożone w subskrypcji dostawcy usług lub subskrypcji dzierżawcy i zarządzana przez dostawcę w imieniu dzierżawcy. Aplikacji autonomicznej na wzorzec dzierżawy zapewnia największą izolacji dzierżawców, ale zazwyczaj jest najbardziej kosztowne, gdyż nie ma okazji do udostępniania zasobów między wieloma dzierżawami.  Ten wzorzec jest dobrze nadaje się do aplikacji, która może być bardziej skomplikowane i których są wdrażane do mniejszej liczby dzierżawców.  O autonomicznych wdrożeniach aplikacji można dostosować dla każdego dzierżawcy łatwiej, niż w innych wzorców.  

Zapoznaj się z [samouczki] [ docs-tutorials-for-wingtip-sa] i kodu w serwisie GitHub [.../Microsoft/WingtipTicketsSaaS-StandaloneApp][github-code-for-wingtip-sa].

## <a name="database-per-tenant-pattern"></a>Bazy danych dla każdego wzorca dzierżawy

Bazy danych dla każdego dzierżawcy wzorzec obowiązuje dla dostawców, którzy są zainteresowani izolacji dzierżawców i chcą uruchamiać scentralizowane usługa, która umożliwia wydajne korzystanie z zasobów udostępnionych. Baza danych jest tworzona dla każdej właściwości lub dzierżawy, a wszystkie bazy danych są zarządzane centralnie. Bazy danych mogą być hostowane w elastycznych pul, aby zapewnić wydajne i zarządzania wydajnością łatwe, który korzysta z wzorców nieprzewidywalne zmiany obciążenia dzierżawców. Baza danych wykazu zawiera mapowania między dzierżawami i ich baz danych. To mapowanie jest zarządzane przy użyciu funkcji zarządzania mapy fragmentów z [Biblioteka kliencka Elastic Database](sql-database-elastic-database-client-library.md), który umożliwia zarządzanie wydajność połączenia do aplikacji.

Zapoznaj się z [samouczki] [ docs-tutorials-for-wingtip-dpt] i kodu w serwisie GitHub [.../Microsoft/WingtipTicketsSaaS-DbPerTenant][github-code-for-wingtip-dpt].

## <a name="sharded-multi-tenant-database-pattern"></a>Wzorzec podzielonej na fragmenty wielodostępną bazą danych.

Wielodostępne bazy danych są skuteczne dla dostawców usług szukasz niższy koszt za dzierżawy i OK izolacji dzierżawy mniejsze. Ten wzorzec umożliwia użycie pakowania dużej liczby dzierżawców do poszczególnych baz danych, obniżenie kosztów na dla dzierżawcy. Prawie nieskończone możliwości skalowania jest możliwe poprzez fragmentowania dzierżaw w wielu bazach danych. Baza danych wykazu mapuje baz danych dzierżaw.  

Ten wzorzec umożliwia także *hybrydowego* modelu, w którym można zoptymalizować koszty dzięki wielu dzierżaw w bazie danych lub Optymalizacja pod kątem izolacji z pojedynczej dzierżawy w swoją własną bazę danych. Wybór można wprowadzić, na podstawie dzierżawy przez dzierżawy, albo gdy dzierżawa nie zostanie aprowizowane lub nowszej, bez wpływu na aplikację.  Ten model można efektywnie używać, gdy grupy dzierżaw muszą być traktowane inaczej. Na przykład dzierżaw niskie koszty można przypisać do udostępnionej bazy danych, podczas gdy dzierżawy w warstwie premium mogą być przypisane do ich własnych baz danych. 

Zapoznaj się z [samouczki] [ docs-tutorials-for-wingtip-mt] i kodu w serwisie GitHub [.../Microsoft/WingtipTicketsSaaS-MultiTenantDb][github-code-for-wingtip-mt].

## <a name="next-steps"></a>Kolejne kroki

#### <a name="conceptual-descriptions"></a>Opisy pojęć

- Bardziej szczegółowy opis wzorców dzierżawy aplikacji znajduje się w temacie [wielodostępne oprogramowanie SaaS, bazy danych dzierżawy wzorców][saas-tenancy-app-design-patterns-md]

#### <a name="tutorials-and-code"></a>Samouczki i kod

- Autonomiczna aplikacja dla dzierżawy:
    - [Samouczki dla aplikacji autonomicznej][docs-tutorials-for-wingtip-sa].
    - [Kod dla aplikacji autonomicznych, w witrynie GitHub][github-code-for-wingtip-sa].

- Baza danych dla dzierżawy:
    - [Samouczki dotyczące bazy danych dla każdego dzierżawcy][docs-tutorials-for-wingtip-dpt].
    - [Kod dla bazy danych dla każdej dzierżawy w witrynie GitHub][github-code-for-wingtip-dpt].

- Wielodostępne podzielonej na fragmenty:
    - [Samouczki dotyczące podzielonej na fragmenty wielodostępnych][docs-tutorials-for-wingtip-mt].
    - [Kod dla wielu dzierżawców podzielonej na fragmenty w serwisie GitHub][github-code-for-wingtip-mt].



<!-- Image references. -->

[image-three-tenancy-patterns]: media/saas-tenancy-welcome-wingtip-tickets-app/three-tenancy-patterns.png "Trzy wzorce dzierżawy."

<!-- Docs.ms.com references. -->

[saas-tenancy-app-design-patterns-md]: saas-tenancy-app-design-patterns.md

<!-- WWWeb http references. -->

[docs-tutorials-for-wingtip-sa]: https://aka.ms/wingtipticketssaas-sa
[github-code-for-wingtip-sa]: https://github.com/Microsoft/WingtipTicketsSaaS-StandaloneApp

[docs-tutorials-for-wingtip-dpt]: https://aka.ms/wingtipticketssaas-dpt
[github-code-for-wingtip-dpt]: https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant

[docs-tutorials-for-wingtip-mt]: https://aka.ms/wingtipticketssaas-mt
[github-code-for-wingtip-mt]: https://github.com/Microsoft/WingtipTicketsSaaS-MultiTenantDb

