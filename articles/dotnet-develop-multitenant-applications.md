---
title: Wzorzec aplikacji sieci Web z wieloma dzierżawcami | Dokumentacja firmy Microsoft
description: Znajdowanie omówienie architektury i wzorce projektowe, które opisują sposób wdrożenia aplikacji sieci web z wieloma dzierżawcami na platformie Azure.
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
ms.openlocfilehash: 57ba0e46139bda2d74c9f7db0ffab2f2122b0df2
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/11/2017
ms.locfileid: "23850714"
---
# <a name="multitenant-applications-in-azure"></a>Wielodostępna aplikacji na platformie Azure
Wielodostępna aplikacja jest zasób udostępniony, umożliwiający kilku użytkowników, lub "dzierżawy" wyświetlić aplikację tak, jakby była własnych. Typowy scenariusz, który pozwala na wielodostępnych aplikacji jest jeden, w której wszyscy użytkownicy aplikacji mogą chcieć dostosowanie środowiska użytkownika, ale w przeciwnym razie ma tych samych wymagań biznesowych. Przykładem dużych aplikacji wielodostępnym są usługi Office 365, Outlook.com i visualstudio.com.

Z perspektywy dostawcę aplikacji z zalet wielodostępności przede wszystkim dotyczą efektywność działania i kosztów. Jednej wersji aplikacji może zaspokoić potrzeby wiele dzierżaw/klientów, umożliwiając konsolidacji systemu zadań administracyjnych, takich jak monitorowanie, dostrajanie wydajności obsługi oprogramowania i kopie zapasowe danych.

Poniżej wymieniono najważniejsze cele i wymagania względem dostawcy.

* **Inicjowanie obsługi administracyjnej**: użytkownik musi mieć możliwość obsługi administracyjnej nowych dzierżaw dla aplikacji.  Dla wielodostępnych aplikacji z wieloma dzierżawcami jest zazwyczaj konieczne jest zautomatyzować ten proces, należy włączyć samoobsługowe inicjowania obsługi administracyjnej.
* **Możliwość utrzymania**: użytkownik musi mieć możliwość uaktualnienia aplikacji i wykonywać inne zadania konserwacji, gdy jest ona używana przez wielu dzierżawców.
* **Monitorowanie**: musi mieć możliwość monitorowania aplikacji przez cały czas do identyfikowania problemów i ich rozwiązywania. W tym monitorowania, jak każdy dzierżawca jest korzystanie z aplikacji.

Właściwie zaimplementowana aplikacji wielodostępnym zapewnia następujące korzyści użytkownikom.

* **Izolacja**: działania poszczególnych dzierżaw nie wpływają na używanie aplikacji przez innych dzierżawców. Dzierżawcy nie może uzyskać dostępu w danych. Wygląda na to dzierżawcy tak, jakby mają wyłącznego użytku aplikacji.
* **Dostępność**: poszczególnych dzierżawcy chcą aplikacji stale dostępny, być może z gwarancje określone w umowie SLA. Ponownie działania innych dzierżawców nie wpływają na dostępność aplikacji.
* **Skalowalność**: aplikacja może obsłużyć spełnić wymagania poszczególnych dzierżawców. Obecność i akcje innych dzierżawców nie wpływają na wydajność aplikacji.
* **Koszty**: koszty są niższe niż uruchamianie aplikacji dedykowanym, pojedynczej dzierżawy, ponieważ wielodostępność umożliwia udostępnianie zasobów.
* **Dostosowywalności**. Możliwość dostosowywania aplikacji dla indywidualnych dzierżawcy na różne sposoby, takie jak dodawanie lub usuwanie funkcji, zmienianie kolorów i logo lub nawet dodanie własnych kod lub skrypt.

Krótko mówiąc gdy istnieje wiele kwestii, które należy wziąć pod uwagę, udostępnianie wysoce skalowalna usługi, istnieją również liczbę cele i wymagania, które są wspólne dla wielu aplikacji wielodostępnym. Niektóre mogą nie być odpowiednie w określonych scenariuszach i znaczenie poszczególnych cele i wymagania będą się różnić w każdym ze scenariuszy. Jako dostawca wielodostępnych aplikacji również należy cele i wymagania, takich jak spotkania umowę cele i wymagania, zyskowności, rozliczeń, wiele poziomów usług, inicjowania obsługi administracyjnej, łatwości monitorowania i automatyzacji.

Aby uzyskać więcej informacji na dodatkowe zagadnienia dotyczące projektu wielodostępnych aplikacji, zobacz [Hosting aplikacji wielodostępnym, w systemie Azure][Hosting a Multi-Tenant Application on Azure]. Aby uzyskać informacje na temat typowych wzorców architektury danych w aplikacjach baz danych typu oprogramowanie jako usługa (SaaS), zobacz artykuł [Design Patterns for Multi-tenant SaaS Applications with Azure SQL Database](sql-database/sql-database-design-patterns-multi-tenancy-saas-applications.md) (Wzorce projektowe dla wielodostępnych aplikacji SaaS korzystających z usługi Azure SQL Database). 

Platforma Azure oferuje wiele funkcji umożliwiających klucza problemy podczas projektowania systemu wielodostępnym.

**Izolacji**

* Witryny sieci Web segmentu dzierżawcy przez nagłówkami hosta lub bez komunikacji SSL
* Segment dzierżaw witryny sieci Web na podstawie parametrów zapytania
* Usługi sieci Web, rolę procesu roboczego
  * Role proces roboczy. które zwykle przetwarzania danych do wewnętrznej bazy danych aplikacji.
  * Role sieci Web, które zwykle pełnić rolę serwera sieci Web dla aplikacji.

**Storage**

Zarządzanie danymi, takie jak usługi baza danych SQL Azure lub usługi Azure Storage, takich jak usługi tabeli, która udostępnia usługi do przechowywania dużych ilości danych bez struktury oraz usługa Blob, która zapewnia usługi do przechowywania dużych ilości nieustrukturyzowanych tekstowe lub binarne dane, takie jak wideo, audio i obrazów.

* Zabezpieczanie danych wielodostępnej w bazie danych SQL odpowiednie logowania do programu SQL Server dla dzierżawy.
* Przy użyciu tabel Azure dla aplikacji zasobów za pośrednictwem zasad dostępu na poziomie kontenera można umożliwia dostosowanie uprawnień bez konieczności wydania nowego adresu URL dla zasobów chronione przy użyciu sygnatury dostępu współdzielonego.
* Kolejek platformy Azure dla kolejek Azure zasoby aplikacji są najczęściej używane do przetwarzania dysku imieniu dzierżawcy, ale może również służyć do rozpraszających wymagane do obsługi lub zarządzania.
* Kolejek usługi Service Bus dla zasobów aplikacji, który wypycha działać do udostępnionej usługi, jeżeli nadawca każdej dzierżawy ma tylko uprawnienia (jak określony na podstawie oświadczeń wystawione przez usługi ACS) można użyć pojedynczej kolejki do wypychania do tej kolejki, chociaż tylko odbiorcy z usługi uprawnienie do ściągnięcia z kolejki danych pochodzących z wielu dzierżawców.

**Połączenie i usług zabezpieczeń**

* Usługa Azure Service Bus, infrastruktury obsługi wiadomości, która znajduje się między aplikacjami, dzięki czemu do wymiany wiadomości w sposób luźno powiązanych ulepszone skali i odporność.

**Usługi sieciowe**

Platforma Azure udostępnia kilka usług sieciowych, które obsługuje uwierzytelnianie i zwiększyć możliwości zarządzania hostowanej aplikacji. Te usługi są następujące:

* Azure umożliwia sieci wirtualnej, należy udostępnić i zarządzanie wirtualnych sieci prywatnych (VPN) na platformie Azure jak bezpiecznie połączyć je z lokalnej infrastruktury IT.
* Menedżer ruchu sieci wirtualnej umożliwia równoważenia obciążenia przychodzącego ruchu w wielu usługach Azure hostowanej czy pracujesz w tym samym centrum danych lub w różnych centrach danych na całym świecie.
* Azure Active Directory (Azure AD) jest usługą nowoczesny, opartego na interfejsie REST, która udostępnia możliwości kontroli dostępu i zarządzania tożsamościami dla aplikacji w chmurze. Używanie programu Azure AD dla zasobów aplikacji w usłudze Azure AD zapewnia prosty sposób uwierzytelniania i autoryzacji użytkowników do uzyskiwania dostępu do usług i aplikacji sieci web integrację funkcji uwierzytelniania i autoryzacji, aby uwzględnić poza swój kod.
* Usługa Azure Service Bus zapewnia bezpiecznej wymiany komunikatów i możliwość przepływu danych dla rozproszonej i hybrydowych aplikacji, takie jak komunikacja między Azure hostowanej aplikacji i lokalnych aplikacji i usług, bez konieczności złożonych zapory i zabezpieczeń infrastruktura. Przy użyciu przekaźnik magistrali usług dla zasobów aplikacji do usług, które są widoczne jako punkty końcowe mogą należeć do dzierżawy (na przykład hostowane poza systemu, takie jak lokalnie) lub mogą być udostępniane specjalnie dla dzierżawcy (ponieważ usług dane poufne, specyficznego dla dzierżawy są przesyłane w nich).

**Inicjowanie obsługi zasobów**

Platforma Azure udostępnia kilka różnych sposobów udostępniania nowi dzierżawcy dla aplikacji. Dla wielodostępnych aplikacji z wieloma dzierżawcami jest zazwyczaj konieczne jest zautomatyzować ten proces, należy włączyć samoobsługowe inicjowania obsługi administracyjnej.

* Proces roboczy role umożliwiają dostarczanie i dezaktywowanie udostępniania dla każdego dzierżawcy zasoby (na przykład gdy nowej dzierżawy znaki w górę lub anuluje), zbierać metryki pomiaru użycia i Zarządzaj skalowania po niektórych harmonogramu lub w odpowiedzi na przekroczenia progów wydajności wskaźniki. Tej samej roli może również służyć do wypychania aktualizacji i uaktualnień do rozwiązania.
* Obiekty BLOB platformy Azure można użyć do udostępnienia obliczeń lub wstępnie zainicjowana pakietów, obrazy VHD i inne zasoby usługi zasobów magazynu dla nowego dzierżawcy zapewniając zasady dostępu na poziomie kontenera ochrony mocy obliczeniowej.
* Opcje inicjowania obsługi administracyjnej zasobów bazy danych SQL dla dzierżawy obejmują:
  
  * DDL w skryptach lub osadzony jako zasoby w obrębie zestawów
  * SQL Server 2008 R2 pakiety DAC wdrożyć programowo.
  * Kopiowanie z bazy danych master odwołania
  * Do obsługi administracyjnej nowych baz danych z pliku przy użyciu bazy danych importu i eksportu.

<!--links-->

[Hosting a Multi-Tenant Application on Azure]: http://msdn.microsoft.com/library/hh534480.aspx
[Designing Multitenant Applications on Azure]: http://msdn.microsoft.com/library/windowsazure/hh689716
