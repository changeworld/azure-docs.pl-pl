---
title: Wzorzec aplikacji sieci Web z wieloma dzierżawami | Dokumentacja firmy Microsoft
description: Znajdź omówienia architektury i wzorce projektowe, które opisują sposób implementacji aplikacji sieci web wielodostępnych na platformie Azure.
services: ''
documentationcenter: .net
author: wadepickett
manager: wpickett
editor: ''
ms.assetid: 4f0281d2-1555-42b0-a99d-1222fade0b0f
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 06/05/2015
ms.author: wpickett
ms.openlocfilehash: 92a0caedca34756228dbf57ec9099fd2ece3d84e
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "66225974"
---
# <a name="multitenant-applications-in-azure"></a>Aplikacji wielodostępnych na platformie Azure
Wielodostępnej aplikacji jest zasobem współdzielonym, umożliwiająca oddzielne użytkowników lub "dzierżawy" wyświetlić aplikację tak, jakby była swoje własne. Typowy scenariusz, który jest przydatna w aplikacji wielodostępnej jest jeden w którym wszyscy użytkownicy aplikacji mogą chcieć dostosować środowisko użytkownika, ale mają te same wymagania podstawowe biznesowych. Przykłady dużych wielodostępne aplikacje usługi Office 365, Outlook.com i visualstudio.com.

Z perspektywy dostawcy aplikacji zalety wielodostępności odnoszą się przede wszystkim do efektywność operacyjną i kosztów. Jednej wersji aplikacji może zaspokajać potrzeby wielu dzierżaw/klientów, umożliwiając konsolidacją system zadań administracyjnych, takich jak monitorowanie, dostrajaniu wydajności, obsługi oprogramowania i kopie zapasowe danych.

Poniżej wymieniono najważniejsze cele i wymagania z perspektywy dostawcy.

* **Inicjowanie obsługi administracyjnej**: Musi umożliwiać aprowizowanie nowych dzierżaw w aplikacji.  Dla aplikacji wielodostępnych z wieloma dzierżawami jest zazwyczaj konieczne zautomatyzować ten proces, należy włączyć samoobsługowe Inicjowanie obsługi administracyjnej.
* **Łatwość konserwacji**: Musi być w stanie Uaktualnij aplikację i wykonywać inne zadania konserwacji, gdy jest używany przez wielu dzierżawców.
* **Monitorowanie**: Należy monitorować aplikacji przez cały czas, aby zidentyfikować wszelkie problemy i aby rozwiązać ten problem. Dotyczy to monitorowania, jak Każda dzierżawa korzysta z aplikacji.

Właściwie zaimplementowana wielodostępnej aplikacji zapewnia następujące korzyści dla użytkowników.

* **Izolacja**: Działania indywidualnych dzierżawcy nie wpływają na korzystanie z aplikacji przez innych dzierżawców. Dzierżawcy nie mają dostępu dane innych osób. Wydaje się do dzierżawy, tak, jakby mają wyłącznego użytku aplikacji.
* **Dostępność**: Poszczególne dzierżawcy chcą, aby aplikacja ma być stale dostępny, być może gwarancje zdefiniowane w umowie SLA. Ponownie działania innych dzierżaw powinien wpływa na dostępność aplikacji.
* **Skalowalność**: Aplikacja jest skalowana w celu spełnienia określonych wymagań poszczególnych dzierżaw. Obecność i działania innych dzierżaw powinien wpływa na wydajność aplikacji.
* **Koszty**: Koszty są niższe niż uruchamianie aplikacji dedykowaną, pojedynczej dzierżawy, ponieważ wielodostępu umożliwia udostępnianie zasobów.
* **Dostosowywalności**. Możliwość dostosowywania aplikacji dla dzierżawy usługi na różne sposoby, takie jak dodawanie lub usuwanie funkcji, zmienianie kolorów i logo lub nawet dodanie własnych kodu lub skryptu.

Krótko mówiąc dostępnych jest wiele kwestii, które należy wziąć pod uwagę, do świadczenia usług o wysokim stopniu skalowalności, istnieją również liczbę cele i wymagania, które są wspólne dla wielu aplikacji wielodostępnych. Niektóre mogą nie być odpowiednie w określonych scenariuszach i ważność poszczególnych cele i wymagania różnią się w każdym scenariuszu. Jako dostawca aplikacji wielodostępnej Ponadto będziesz mieć cele i wymagania, takie jak spotkania dzierżawcy cele i wymagania, rentowność, rozliczenia, wiele poziomów usług, inicjowania obsługi, monitorowania łatwość konserwacji i automatyzacji.

Aby uzyskać więcej informacji na temat zagadnień projektowych dodatkowe wielodostępnej aplikacji, zobacz [hostingu aplikacji z wieloma dzierżawami, na platformie Azure][Hosting a Multi-Tenant Application on Azure]. Aby uzyskać informacje na temat typowych wzorców architektury danych w aplikacjach baz danych typu oprogramowanie jako usługa (SaaS), zobacz artykuł [Design Patterns for Multi-tenant SaaS Applications with Azure SQL Database](sql-database/sql-database-design-patterns-multi-tenancy-saas-applications.md) (Wzorce projektowe dla wielodostępnych aplikacji SaaS korzystających z usługi Azure SQL Database). 

System Azure oferuje wiele funkcji, które umożliwiają rozwiązywać kluczowe problemy występujące podczas projektowania systemie wielodostępnym.

**Izolacja**

* Witryny sieci Web segmentu dzierżaw przez nagłówki hosta z lub bez komunikacji SSL
* Witryny sieci Web segmentu dzierżaw przez parametry zapytania
* Usługi sieci Web w przypadku ról procesów roboczych
  * Role procesów roboczych, zwykle przetwarzających dane do wewnętrznej bazy danych aplikacji.
  * Role sieci Web, które zazwyczaj działa jako fronton dla aplikacji.

**Storage**

Zarządzanie danymi, takich jak usługi Azure SQL Database lub magazynu Azure, takich jak usługa tabeli, która zapewnia usługi do przechowywania dużych ilości danych bez struktury i usługi Blob service, która zapewnia usługi do przechowywania dużych ilości tekstu bez struktury lub dane binarne, takie jak wideo, audio i obrazy.

* Zabezpieczanie wielodostępnej bazie danych SQL logowania programu SQL Server dla dzierżawcy.
* Przy użyciu tabel platformy Azure dla zasobów aplikacji, określając zasady dostępu na poziomie kontenera, może mieć możliwość dostosowania uprawnień bez konieczności wystawiania nowego adresy URL dla zasobów chronione przy użyciu sygnatury dostępu współdzielonego.
* Kolejki platformy Azure dla kolejek platformy Azure zasoby aplikacji są często używane do przetwarzania dysku w imieniu dzierżaw, ale może również służyć do dostarczania pracy wymaganej do inicjowania obsługi administracyjnej lub zarządzania.
* Kolejki usługi Service Bus dla zasobów aplikacji, który wypycha działają udostępnionej usługi, możesz użyć pojedynczej kolejki gdzie nadawcy Każda dzierżawa ma tylko uprawnienia (wyprowadzana z oświadczeń wystawionych z usługi ACS) do wypychania do tej kolejki, a tylko odbiorcy z usługi uprawnienia do pobierania z kolejki danych przechodzących z wieloma dzierżawcami.

**Połączenie i usług zabezpieczeń**

* Usługa Azure Service Bus to infrastruktura obsługi komunikatów, która pośredniczy między aplikacjami, umożliwiając im wymianę komunikatów w swobodną ułatwia skalowanie i zwiększa odporność.

**Usługi sieciowe**

Platforma Azure udostępnia kilka usług sieciowych, które obsługują uwierzytelnianie i zwiększyć możliwości aplikacji hostowanej. Te usługi są następujące:

* Azure umożliwia sieci wirtualnej możesz aprowizować i zarządzać nimi wirtualnych sieci prywatnych (VPN) na platformie Azure oraz bezpieczne łączenie ich z lokalną infrastrukturą informatyczną.
* Menedżer ruchu w sieci wirtualnej umożliwia równoważenie obciążenia ruchem przychodzącym wielu hostowanych usług platformy Azure, czy są one uruchamiane w jednym centrum danych lub w różnych centrach danych na całym świecie.
* Azure Active Directory (Azure AD) to nowoczesna, oparta na REST usługa, która zapewnia funkcji kontroli tożsamości zarządzania i dostęp do aplikacji w chmurze. Używanie programu Azure AD dla zasobów aplikacji udostępnia łatwy sposób uwierzytelniania i autoryzacji użytkowników uzyskujących dostęp do aplikacji sieci web i usług, zezwalając funkcji uwierzytelniania i autoryzacji, aby uwzględnić poza swój kod.
* Usługa Azure Service Bus zapewnia bezpiecznej wymiany komunikatów i możliwości przepływu danych na potrzeby rozproszonego oraz aplikacje hybrydowe, takie jak komunikacja między platformą Azure aplikacji i aplikacji lokalnych i usług hostowanych, bez konieczności złożonych zapory i zabezpieczeń infrastruktura. Dostęp do usług, które są dostępne jako punktów końcowych przy użyciu usługi Service Bus Relay dla zasobów aplikacji może należeć do dzierżawy (na przykład hostowane poza usługą systemu, takich jak lokalne) lub mogą one być aprowizowana specjalnie dla dzierżawy (ponieważ usług dane poufne, specyficznym dla dzierżawy porusza się między nimi).

**Aprowizowanie zasobów**

Platforma Azure udostępnia szereg sposobów aprowizacja nowych dzierżaw w aplikacji. Dla aplikacji wielodostępnych z wieloma dzierżawami jest zazwyczaj konieczne zautomatyzować ten proces, należy włączyć samoobsługowe Inicjowanie obsługi administracyjnej.

* Role proces roboczy umożliwiają aprowizacji i anulować obsługę każdego dzierżawcy zasoby (na przykład gdy nową dzierżawę tworzy konto lub anuluje), zbieranie metryk dla pomiaru użycia, skalowania i zarządzanie nim zgodnie z harmonogramem niektórych lub w odpowiedzi na przekroczenia progów wydajności wskaźniki. Tej samej roli może również służyć do wypychania aktualizacji i uaktualnień do rozwiązania.
* Obiekty BLOB platformy Azure, które mogą być używane do udostępniania obliczeń lub wstępnie zainicjowana zasobów magazynu dla nowych dzierżaw, przy jednoczesnym zapewnieniu zasady dostępu na poziomie kontenera chronić zasoby obliczeniowe usługi pakietów, obrazów wirtualnych dysków Twardych i innych zasobów.
* Opcje aprowizacji zasobów bazy danych SQL dla dzierżawy:
  
  * DDL w skryptach lub osadzony jako zasoby w ramach zestawów.
  * SQL Server 2008 R2 pakiety DAC wdrożone programowo.
  * Kopiowanie z bazy danych master odwołania.
  * Do aprowizowania nowych baz danych z pliku przy użyciu bazy danych importu i eksportu.

<!--links-->

[Hosting a Multi-Tenant Application on Azure]: https://msdn.microsoft.com/library/hh534480.aspx
[Designing Multitenant Applications on Azure]: https://msdn.microsoft.com/library/windowsazure/hh689716
