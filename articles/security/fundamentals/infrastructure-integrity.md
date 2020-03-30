---
title: Integralność infrastruktury platformy Azure
description: Ten artykuł dotyczy integralności infrastruktury platformy Azure.
services: security
documentationcenter: na
author: TerryLanfear
manager: barbkess
editor: TomSh
ms.assetid: 61e95a87-39c5-48f5-aee6-6f90ddcd336e
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/06/2018
ms.author: terrylan
ms.openlocfilehash: ef81e74b07a351139aa8feefbdf1b89ea7e4994f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "68727188"
---
# <a name="azure-infrastructure-integrity"></a>Integralność infrastruktury platformy Azure

## <a name="software-installation"></a>Instalacja oprogramowania
Wszystkie składniki w stosie oprogramowania, które są zainstalowane w środowisku platformy Azure są tworzone na zamówienie zgodnie z procesem rozwoju zabezpieczeń firmy Microsoft (SDL). Wszystkie składniki oprogramowania, w tym obrazy systemu operacyjnego (OS) i bazy danych SQL, są wdrażane w ramach procesu zarządzania zmianami i zarządzania wydaniami. System operacyjny uruchamiany we wszystkich węzłach jest dostosowaną wersją systemu Windows Server 2008 lub Windows Server 2012. Dokładna wersja jest wybierana przez kontroler sieci szkieletowej (FC) zgodnie z rolą, jaką zamierza odgrywać system operacyjny. Ponadto system operacyjny hosta nie zezwala na instalację nieautoryzowanych składników oprogramowania.

Niektóre składniki platformy Azure są wdrażane jako klienci platformy Azure na maszynie wirtualnej gościa uruchomionej w systemu operacyjnym gościa.

## <a name="virus-scans-on-builds"></a>Skanowanie antywirusowe na kompilacjach
Kompilacje składników oprogramowania platformy Azure (w tym systemu operacyjnego) muszą zostać poddane skanowaniu antywirusowemu, które korzysta z narzędzia antywirusowego endpoint protection. Każde skanowanie antywirusowe tworzy dziennik w powiązanym katalogu kompilacji, szczegółowo opisujący, co zostało zeskanowane i wyniki skanowania. Skanowanie antywirusowe jest częścią kodu źródłowego kompilacji dla każdego składnika na platformie Azure. Kod nie jest przenoszony do produkcji bez czystego i udanego skanowania antywirusowego. Jeśli zostaną odnotowane jakiekolwiek problemy, kompilacja zostanie zamrożona, a następnie przechodzi do zespołów zabezpieczeń w programie Microsoft Security, aby zidentyfikować, gdzie kod "nieuczciwych" wszedł do kompilacji.

## <a name="closed-and-locked-environment"></a>Zamknięte i zablokowane środowisko
Domyślnie węzły infrastruktury platformy Azure i maszyny wirtualne gościa nie mają utworzonych na nich kont użytkowników. Ponadto domyślne konta administratora systemu Windows są również wyłączone. Administratorzy z pomocy technicznej platformy Azure na żywo mogą, przy odpowiednim uwierzytelnianiu, zalogować się do tych komputerów i administrować siecią produkcyjną platformy Azure w celu naprawy awaryjnej.

## <a name="azure-sql-database-authentication"></a>Uwierzytelnianie w usłudze Azure SQL Database
Podobnie jak w przypadku każdej implementacji programu SQL Server, zarządzanie kontem użytkownika musi być ściśle kontrolowane. Usługa Azure SQL Database obsługuje tylko uwierzytelnianie programu SQL Server. Aby uzupełnić model zabezpieczeń danych klienta, należy również używać kont użytkowników z silnymi hasłami i skonfigurowanych z określonymi prawami.

## <a name="acls-and-firewalls-between-the-microsoft-corporate-network-and-an-azure-cluster"></a>Listy ACL i zapory między siecią firmową firmy Microsoft a klastrem platformy Azure
Listy kontroli dostępu (ACL) i zapory między platformą usług a siecią firmową firmy Microsoft chronią wystąpienia bazy danych SQL przed nieautoryzowanym dostępem do niejawnych informacji poufnych. Ponadto tylko użytkownicy z adresu IP waha się od sieci firmowej firmy Microsoft mogą uzyskać dostęp do punktu końcowego zarządzania platformą Windows Fabric.

## <a name="acls-and-firewalls-between-nodes-in-a-sql-database-cluster"></a>Listy ACL i zapory między węzłami w klastrze bazy danych SQL
Jako dodatkową ochronę, w ramach obrony w strategii głębi, listy ACL i zapora zostały zaimplementowane między węzłami w klastrze bazy danych SQL. Cała komunikacja wewnątrz klastra platformy sieci szkieletowej systemu Windows, a także cały uruchomiony kod jest zaufany.

## <a name="custom-monitoring-agents"></a>Niestandardowe środki monitorowania
Baza danych SQL wykorzystuje niestandardowych agentów monitorowania (MA), nazywanych również watchdogs, do monitorowania kondycji klastra bazy danych SQL.

## <a name="web-protocols"></a>Protokoły sieci Web

### <a name="role-instance-monitoring-and-restart"></a>Monitorowanie i ponowne uruchamianie wystąpienia roli
Platforma Azure zapewnia, że wszystkie wdrożone, uruchomione role (internetowe lub role procesu roboczego przetwarzania zaplecza) podlegają ciągłemu monitorowaniu kondycji, aby zapewnić, że skutecznie i wydajnie dostarczają usługi, dla których zostały zainicjowane. Jeśli rola staje się w złej kondycji, przez błąd krytyczny w aplikacji, która jest hostowana lub podstawowy problem konfiguracji w samym wystąpieniu roli, FC wykrywa problem w wystąpieniu roli i inicjuje stan korekcyjny.

### <a name="compute-connectivity"></a>Łączność obliczeniowa
Platforma Azure zapewnia, że wdrożona aplikacja lub usługa jest osiągalna za pośrednictwem standardowych protokołów opartych na sieci Web. Wirtualne wystąpienia ról internetowych z internetem mają zewnętrzną łączność z Internetem i są dostępne bezpośrednio przez użytkowników internetu. Aby chronić czułość i integralność operacji, które role robocze wykonują w imieniu publicznie dostępnych wystąpień wirtualnych ról sieci Web, wirtualne wystąpienia ról pracownika przetwarzania zaplecza mają zewnętrzną łączność z Internetem, ale nie mogą być bezpośrednio przez zewnętrznych użytkowników internetu.

## <a name="next-steps"></a>Następne kroki
Aby dowiedzieć się więcej o tym, co firma Microsoft robi, aby zabezpieczyć infrastrukturę platformy Azure, zobacz:

- [Obiekty platformy Azure, pomieszczenia i zabezpieczenia fizyczne](physical-security.md)
- [Dostępność infrastruktury platformy Azure](infrastructure-availability.md)
- [Składniki i granice systemu informacyjnego platformy Azure](infrastructure-components.md)
- [Architektura sieci platformy Azure](infrastructure-network.md)
- [Sieć produkcyjna platformy Azure](production-network.md)
- [Funkcje zabezpieczeń usługi Azure SQL Database](infrastructure-sql.md)
- [Operacje produkcyjne i zarządzanie platformą Azure](infrastructure-operations.md)
- [Monitorowanie infrastruktury platformy Azure](infrastructure-monitoring.md)
- [Ochrona danych klientów platformy Azure](protection-customer-data.md)
