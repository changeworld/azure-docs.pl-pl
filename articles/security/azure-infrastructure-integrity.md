---
title: Integralność infrastruktury platformy Azure
description: W tym artykule opisano integralności infrastruktury platformy Azure.
services: security
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: TomSh
ms.assetid: 61e95a87-39c5-48f5-aee6-6f90ddcd336e
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/28/2018
ms.author: terrylan
ms.openlocfilehash: 18d7fab30c0bbaa5292fe5d3003b7f2309b34d3b
ms.sourcegitcommit: d7725f1f20c534c102021aa4feaea7fc0d257609
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/29/2018
ms.locfileid: "37102522"
---
# <a name="integrity-of-azure-infrastructure"></a>Integralność infrastruktury platformy Azure   

## <a name="software-installation"></a>Instalacja oprogramowania
Wszystkie składniki stosu oprogramowania, które są zainstalowane w środowisku platformy Azure są niestandardowym następujący proces Security Development Lifecycle (SDL) firmy Microsoft. Wszystkie składniki oprogramowania (w tym obrazów systemu operacyjnego i bazy danych SQL) są wdrażane w ramach procesu zmiany i zarządzania zleceniami. System operacyjny działającą na wszystkich węzłach jest dostosowaną wersję systemu Windows Server 2008 lub Windows Server 2012. Aby wybrać dokładnej wersji FC zgodnie z rolą przeznaczonych dla systemu operacyjnego do odtwarzania. Ponadto system operacyjny hosta nie zezwala na instalację wszystkich składników nieautoryzowanego oprogramowania.

Niektóre składniki Microsoft Azure (na przykład frontonu REDDOG, portalu dla deweloperów, itp.) są wdrażane jako klientów platformy Azure na maszynie Wirtualnej gościa z systemu operacyjnego gościa.

## <a name="virus-scans-on-builds"></a>Skanowanie wirusów kompilacji
Kompilacji (w tym systemu operacyjnego) składnik Azure oprogramowania musi przechodzić przez skanowanie w poszukiwaniu wirusów za pomocą narzędzia oprogramowania antywirusowego firmy Microsoft Endpoint Protection (MEP). Każdy skanowanie w poszukiwaniu wirusów utworzy dziennika w katalogu kompilacji określające, jaki był skanowany i wyniki skanowania. Skanowanie w poszukiwaniu wirusów jest częścią kompilacji kodu źródłowego dla każdego składnika w systemie Azure. Kod nie zostaną przeniesione do środowiska produkcyjnego bez konieczności wirus czyste i pomyślnie skanowanie. Występują problemy zauważyć, kompilacji jest zablokowana, a następnie przejdzie zespoły zabezpieczeń w ramach Microsoft Security do identyfikowania, gdy kod "nieautoryzowanych" wprowadzony kompilacji.

## <a name="closedlocked-environment"></a>Zamknięte zablokowany środowiska
Domyślnie węzłów infrastruktury platformy Azure i maszyny wirtualne gości nie mają kont użytkowników na ich utworzyć. Ponadto domyślne konta administratora systemu Windows, również są wyłączone. Administratorzy z Microsoft Azure na żywo pomocy technicznej (WALS) mogą z odpowiedniego uwierzytelnienia — Zaloguj się do tych urządzeń i zarządzać nim sieci produkcyjnej Azure do napraw awaryjnego.

## <a name="microsoft-azure-sql-database-authentication"></a>Uwierzytelnianie bazy danych SQL Microsoft Azure
Podobnie jak w przypadku stosowania programu SQL Server musi być ściśle kontrolowane Zarządzanie kontami użytkowników. Baza danych programu Microsoft Azure SQL obsługuje tylko uwierzytelnianie programu SQL Server. Użytkownik kont z silnych haseł i skonfigurowana z określonymi prawa powinny być również używane do uzupełnienia modelu zabezpieczeń danych klienta.

## <a name="firewallacls-between-msft-corpnet-and-microsoft-azure-cluster"></a>Zapora/list ACL między MSFT CorpNet i klastra platformy Microsoft Azure
Platforma usługi od sieci firmowej MS listy ACL/zaporą ochronę Microsoft Azure SQL Database z wewnętrznymi nieautoryzowanego dostępu. Co więcej tylko użytkownicy z zakresów adresów IP z CorpNet firmy Microsoft można uzyskać dostępu do punktu końcowego WinFabric platformy zarządzania.

## <a name="firewallacls-between-nodes-in-an-azure-sql-db-cluster"></a>Zapora/list ACL między węzłami w klastrze serwerów bazy danych SQL Azure
Jako dodatkową ochronę, w ramach strategii ochrony w głębokość-listy ACL/zapory została zaimplementowana między węzłami w klastrze bazy danych SQL Azure firmy Microsoft. Komunikacji wewnątrz klastra platformy WinFabric, a także wszystkie kodu jest zaufany.

## <a name="custom-mas-watchdogs"></a>Niestandardowe MAs (Watchdogs)
Baza danych SQL Azure Microsoft wykorzystuje MAs niestandardowy o nazwie watchdogs do monitorowania prawidłowości klastra bazy danych SQL Azure firmy Microsoft.

## <a name="web-protocols"></a>Protokoły sieci Web

### <a name="role-instance-monitoring-and-restart"></a>Monitorowanie wystąpienia roli i ponowne uruchomienie
Azure zapewnia, że wszystkie uruchomione role (web skierowane do Internetu lub roli proces roboczy przetwarzania zaplecza) wdrożony podlegają kondycji przez monitorowanie w celu zapewnienia, że są one skutecznie i efektywnie dostarczania usług, w których zostały zostały udostępnione. W przypadku roli staje się nieprawidłowy, poprzez krytyczny błąd w aplikacji hostowanej lub podstawowej konfiguracji problem w obrębie samego wystąpienia roli, Microsoft Azure FC wykryje problem w wystąpieniu roli i zainicjować korygujące stanu .

### <a name="compute-connectivity"></a>Obliczeniowe łączności
Azure zapewnia, że aplikacja usługi wdrożone jest osiągalne za pomocą standardowych protokołów opartych na sieci web. Wirtualne wystąpień roli sieci web skierowane do Internetu będzie mieć zewnętrzne połączenie z Internetem i był dostępny bezpośrednio przez użytkowników sieci web. Wirtualne wystąpień roli procesu roboczego przetwarzania zaplecza zewnętrzne jest połączony z Internetem, ale nie można uzyskać dostępu do bezpośrednio przez użytkownika zewnętrznego w sieci web, aby chronić ważność i integralności operacje roli proces roboczy dla wirtualne wystąpień roli publicznie dostępny w sieci web.

## <a name="next-steps"></a>Kolejne kroki
Aby dowiedzieć się więcej na temat firmy Microsoft jest zapewnienie infrastruktury platformy Azure, zobacz:

- [Urządzenia platformy Azure, lokalne i zabezpieczenia fizyczne](azure-physical-security.md)
- [Dostępność infrastruktury platformy Azure](azure-infrastructure-availability.md)
- [Składniki systemu Azure informacji i granice](azure-infrastructure-components.md)
- [Architektura sieci platformy Azure](azure-infrastructure-network.md)
- [Sieci Azure środowiska produkcyjnego](azure-production-network.md)
- [Funkcje zabezpieczeń bazy danych SQL Azure firmy Microsoft](azure-infrastructure-sql.md)
- [Operacje Azure środowiska produkcyjnego i zarządzania](azure-infrastructure-operations.md)
- [Monitorowanie infrastruktury platformy Azure](azure-infrastructure-monitoring.md)
- [Ochrona danych klienta na platformie Azure](azure-protection-of-customer-data.md)
