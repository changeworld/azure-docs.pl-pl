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
ms.date: 07/06/2018
ms.author: terrylan
ms.openlocfilehash: 867bc66a68bec662153d8336e649cf46df02f101
ms.sourcegitcommit: d551ddf8d6c0fd3a884c9852bc4443c1a1485899
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/07/2018
ms.locfileid: "37901320"
---
# <a name="azure-infrastructure-integrity"></a>Integralność infrastruktury platformy Azure

## <a name="software-installation"></a>Instalacja oprogramowania
Wszystkie składniki stosu oprogramowania, które są zainstalowane w środowisku platformy Azure są niestandardowym zgodnie z procesem cykl projektowania zabezpieczeń (SDL) firmy Microsoft. Wszystkie składniki oprogramowania (w tym obrazów systemu operacyjnego i SQL Database) są wdrażane jako część procesu zmiany i Release Management. System operacyjny, który działa we wszystkich węzłach jest dostosowaną wersją systemu Windows Server 2008 lub Windows Server 2012. Dokładna wersja jest wybierany przez FC zgodnie z rolą, przeznaczonych dla systemu operacyjnego odtworzyć. Ponadto systemu operacyjnego hosta nie zezwala na instalację wszystkich składników nieautoryzowanego oprogramowania.

Niektóre składniki Microsoft Azure (np. fronton REDDOG, Portal dla deweloperów itp.) są wdrażane jako klientów platformy Azure na maszynie Wirtualnej gościa, uruchomiony w systemie operacyjnym gościa.

## <a name="virus-scans-on-builds"></a>Skanowanie antywirusowe w kompilacji
Kompilacje (w tym systemu operacyjnego) składnik oprogramowania platformy Azure musisz przejść przez skanowanie w poszukiwaniu wirusów za pomocą narzędzia oprogramowania antywirusowego firmy Microsoft Endpoint Protection (MEP). Każdy skanowania antywirusowego spowoduje utworzenie dziennika w katalogu skojarzonej kompilacji ze szczegółami dotyczącymi co przeskanowano i wyniki skanowania. Skanowanie w poszukiwaniu wirusów jest częścią kompilacji kodu źródłowego dla każdego składnika w systemie Azure. Kod nie zostaną przeniesione do środowiska produkcyjnego bez konieczności wirus czystego, jak i pomyślnie skanowania. W przypadku problemów z wymienionych kompilacji jest zablokowane i będzie następnie przejdź do zespołów zabezpieczeń w usłudze Microsoft Security do identyfikowania, gdzie kod "nieautoryzowanych" wprowadzić kompilacji.

## <a name="closedlocked-environment"></a>Zamknięte zablokowane środowiska
Domyślnie węzłów infrastruktury platformy Azure i maszyn wirtualnych gościa nie mają kont użytkowników utworzone na nich. Oprócz domyślnych kont administratora Windows są również wyłączone. Administratorzy z Microsoft Azure na żywo pomocy technicznej (WALS) mogą przy użyciu odpowiedniego uwierzytelnienia — Zaloguj się do tych maszyn i administrować produkcyjnego Azure do naprawy.

## <a name="microsoft-azure-sql-database-authentication"></a>Uwierzytelnianie bazy danych SQL Microsoft Azure
Podobnie jak w przypadku stosowania programu SQL Server musi być ściśle kontrolowane Zarządzanie kontami użytkowników. Microsoft Azure SQL Database obsługuje tylko uwierzytelnianie programu SQL Server. Użytkownik konta za pomocą silnych haseł i skonfigurowany z określonymi praw powinna służyć także do uzupełnia model zabezpieczeń danych klienta.

## <a name="firewallacls-between-msft-corpnet-and-microsoft-azure-cluster"></a>Zapora/list kontroli dostępu między siecią firmową MSFT i klastrem platformy Microsoft Azure
Listy ACL/zapora między platformę usługi i sieci firmy MS ochrony Microsoft Azure SQL Database przed dostępem nieautoryzowanych niejawnego programu testów. Sprawy tylko użytkownicy z adresów IP pochodzących z Microsoft CorpNet można uzyskać dostęp do punktu końcowego WinFabric platformy zarządzania.

## <a name="firewallacls-between-nodes-in-an-azure-sql-db-cluster"></a>Zapora/list ACL między węzłami w klastrze usługi Azure SQL DB
Jako dodatkową ochronę, ochrona w głębi-strategii, w ramach zostały zaimplementowane list ACL/zapora między węzłami w klastrze bazy danych SQL Microsoft Azure. Całej komunikacji wewnątrz klastra platformy WinFabric, a także wszystkie kodu jest zaufany.

## <a name="custom-mas-watchdogs"></a>Niestandardowe MAs (Watchdogs)
Microsoft Azure SQL Database wykorzystuje MAs niestandardowej o nazwie watchdogs do monitorowania kondycji klastra systemu Microsoft Azure SQL DB.

## <a name="web-protocols"></a>Protokoły internetowe

### <a name="role-instance-monitoring-and-restart"></a>Monitorowanie wystąpienia roli i uruchom ponownie
Azure gwarantuje, że wszystkie uruchomione role (sieci web dostępnym z Internetu lub ról procesów roboczych przetwarzania zaplecza) wdrożonych podlegają kondycji stałego monitorowania, aby zapewnić, że są one efektywnie i wydajnie dostarczanie usług, w których już zostały udostępnione. W przypadku roli staje się nieprawidłowy, poprzez krytyczny błąd w aplikacji jest problem z konfiguracją hostowanej lub podstawowej w obrębie samego wystąpienia roli, Microsoft Azure FC wykryje problem w wystąpieniu roli i zainicjować naprawcze stanu .

### <a name="compute-connectivity"></a>Łączność wystąpienia obliczeniowego
Platforma Azure zapewnia, że aplikacja/Usługa wdrożona jest dostępny za pośrednictwem standardowych protokołów opartych na sieci web. Wirtualne wystąpienia roli sieci web dostępnym z Internetu będzie miał łączność zewnętrzną Internet i będą dostępne bezpośrednio przez użytkowników w sieci web. Wirtualne wystąpienia roli procesu roboczego zaplecza przetwarzania mieć zewnętrzne połączenie z Internetem, ale nie są dostępne bezpośrednio przez użytkownika zewnętrznego w sieci web, w celu ochrony poufności i integralności operacje wykonujące ról procesów roboczych w imieniu osób wirtualne wystąpienia roli publicznie dostępny w sieci web.

## <a name="next-steps"></a>Kolejne kroki
Aby dowiedzieć się więcej na temat firmy Microsoft jest zapewnienie infrastruktury platformy Azure, zobacz:

- [Urządzenia platformy Azure, lokalnie i zabezpieczenia fizyczne](azure-physical-security.md)
- [Dostępność infrastruktury platformy Azure](azure-infrastructure-availability.md)
- [Usługi Azure information składników systemu i granice](azure-infrastructure-components.md)
- [Architektura sieci platformy Azure](azure-infrastructure-network.md)
- [Sieć platformy Azure środowiska produkcyjnego](azure-production-network.md)
- [Funkcje zabezpieczeń Microsoft Azure SQL Database](azure-infrastructure-sql.md)
- [Operacje platformy Azure środowiska produkcyjnego i zarządzanie](azure-infrastructure-operations.md)
- [Monitorowanie infrastruktury platformy Azure](azure-infrastructure-monitoring.md)
- [Ochrona danych klientów na platformie Azure](azure-protection-of-customer-data.md)
