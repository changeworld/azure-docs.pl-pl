---
title: Integralność infrastruktury platformy Azure
description: W tym artykule opisano integralności infrastruktury platformy Azure.
services: security
documentationcenter: na
author: TerryLanfear
manager: barbkess
editor: TomSh
ms.assetid: 61e95a87-39c5-48f5-aee6-6f90ddcd336e
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/06/2018
ms.author: terrylan
ms.openlocfilehash: 24d54fa7a8985a6af58cddfc969b8023485c73c2
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60587125"
---
# <a name="azure-infrastructure-integrity"></a>Integralność infrastruktury platformy Azure

## <a name="software-installation"></a>Instalacja oprogramowania
Wszystkie składniki stosu oprogramowania, które są zainstalowane w środowisku platformy Azure są niestandardowe utworzone po procesie cykl projektowania zabezpieczeń (SDL). Wszystkie składniki oprogramowania, w tym obrazów systemu operacyjnego (OS) i usługi SQL Database są wdrażane jako część zarządzania zmianami i zarządzania procesu tworzenia wersji. System operacyjny, który działa we wszystkich węzłach jest dostosowaną wersją systemu Windows Server 2008 lub Windows Server 2012. Dokładna wersja jest wybierany przez kontroler sieci szkieletowej (FC) zgodnie z rolą, przeznaczonych dla systemu operacyjnego odtworzyć. Ponadto system operacyjny hosta nie zezwala na instalację wszystkich składników nieautoryzowanego oprogramowania.

Niektóre składniki platformy Azure są wdrażane jako klientów platformy Azure z gościa maszyny Wirtualnej, systemem operacyjnym gościa.

## <a name="virus-scans-on-builds"></a>Skanowanie antywirusowe w kompilacji
Kompilacje (w tym systemu operacyjnego) składnik oprogramowania platformy Azure mają przeprowadzenie skanowanie w poszukiwaniu wirusów przy użyciu narzędzia ochrony antywirusowej programu Endpoint Protection. Każdy skanowanie w poszukiwaniu wirusów tworzy dziennik w ramach katalogu skojarzonej kompilacji ze szczegółami dotyczącymi co przeskanowano i wyniki skanowania. Skanowanie w poszukiwaniu wirusów jest częścią kompilacji kodu źródłowego dla każdego składnika w systemie Azure. Kod nie jest przenoszony do środowiska produkcyjnego bez konieczności wirus czystego, jak i pomyślnie skanowania. Jeśli wszystkie problemy zostały wymienione, kompilacja jest zablokowane i następnie przechodzi do zespołów zabezpieczeń w usłudze Microsoft Security do identyfikowania, gdzie kod "nieautoryzowanych" wprowadzić kompilacji.

## <a name="closed-and-locked-environment"></a>Środowisko zamknięte i zablokowane
Domyślnie węzłów infrastruktury platformy Azure i maszyn wirtualnych gościa nie masz konta użytkowników utworzone na nich. Oprócz domyślnych kont administratora Windows są również wyłączone. Administratorów pomocy technicznej platformy Azure na żywo mogą za pomocą odpowiednie uwierzytelnianie, zaloguj się do tych maszyn i administrowania siecią platformy Azure środowiska produkcyjnego do naprawy.

## <a name="azure-sql-database-authentication"></a>Uwierzytelnianie usługi Azure SQL Database
Podobnie jak w przypadku stosowania programu SQL Server musi być ściśle kontrolowane Zarządzanie kontami użytkowników. Usługa Azure SQL Database obsługuje tylko uwierzytelnianie programu SQL Server. Jako uzupełnienie model zabezpieczeń danych klienta, użytkownik konta za pomocą silnych haseł i skonfigurowane przy użyciu określonych praw, które powinny być używane także.

## <a name="acls-and-firewalls-between-the-microsoft-corporate-network-and-an-azure-cluster"></a>Listy kontroli dostępu i zapory między sieci firmy Microsoft i klastrze platformy Azure
Listy kontroli dostępu (ACL) i zapory między sieci firmy Microsoft i platformy usługi ochrony wystąpień bazy danych SQL, przed dostępem nieautoryzowanych niejawnego programu testów. Co więcej tylko użytkownicy z zakresów adresów IP z sieci firmowej firmy Microsoft można uzyskać dostęp do punktu końcowego zarządzania dla platformy Windows Fabric.

## <a name="acls-and-firewalls-between-nodes-in-a-sql-database-cluster"></a>Listy kontroli dostępu i zapory między węzłami w klastrze bazy danych SQL
Jako dodatkową ochronę, ochrona w głębi-strategii, w ramach zostały zaimplementowane listy kontroli dostępu i zapory między węzłami w klastrze bazy danych SQL. Całej komunikacji wewnątrz klastra platformy Windows Fabric, a także wszystkie kodu jest zaufany.

## <a name="custom-monitoring-agents"></a>Niestandardowe agentów monitorowania
SQL Database wykorzystuje niestandardowe agentów monitorowania (MAs), nazywany również watchdogs, do monitorowania kondycji klastra SQL Database.

## <a name="web-protocols"></a>Protokoły internetowe

### <a name="role-instance-monitoring-and-restart"></a>Monitorowanie wystąpienia roli i uruchom ponownie
Azure zapewnia, że wszystkie wdrożone, uruchomione role (sieci web dostępnym z Internetu lub ról procesów roboczych przetwarzania zaplecza) jest zależna od kondycji stałego monitorowania, aby zapewnić, że sprawnej i skutecznej dostarczają usługi, dla których już zostały udostępnione. Jeśli rola staje się zła, krytyczne błędów w aplikacji, która jest hostowana lub podstawowy problem z konfiguracją w obrębie samego wystąpienia roli, FC wykryje problem w wystąpieniu roli i inicjuje naprawcze stanu.

### <a name="compute-connectivity"></a>Łączność wystąpienia obliczeniowego
Platforma Azure zapewnia, że do wdrożonej aplikacji lub usługi jest dostępny za pośrednictwem standardowych protokołów opartych na sieci web. Wirtualne wystąpienia ról sieci web dostępnym z Internetu mieć zewnętrzne połączenie z Internetem, a są dostępne bezpośrednio przez użytkowników w sieci web. Do ochrony poufności i integralności operacje wykonujące ról procesów roboczych w imieniu wirtualne wystąpienia roli publicznie dostępny w sieci web, wirtualne wystąpienia ról procesu roboczego zaplecza przetwarzania mieć zewnętrzne połączenie z Internetem, ale nie może być dostępne bezpośrednio dla użytkowników zewnętrznych sieci web.

## <a name="next-steps"></a>Kolejne kroki
Aby dowiedzieć się więcej na temat firmy Microsoft jest zapewnienie infrastruktury platformy Azure, zobacz:

- [Urządzenia platformy Azure, lokalnie i zabezpieczenia fizyczne](azure-physical-security.md)
- [Dostępność infrastruktury platformy Azure](azure-infrastructure-availability.md)
- [Usługi Azure information składników systemu i granice](azure-infrastructure-components.md)
- [Architektura sieci platformy Azure](azure-infrastructure-network.md)
- [Sieć platformy Azure środowiska produkcyjnego](azure-production-network.md)
- [Funkcje zabezpieczeń w usłudze Azure SQL Database](azure-infrastructure-sql.md)
- [Operacje platformy Azure środowiska produkcyjnego i zarządzanie](azure-infrastructure-operations.md)
- [Monitorowanie infrastruktury platformy Azure](azure-infrastructure-monitoring.md)
- [Ochrona danych klientów platformy Azure](azure-protection-of-customer-data.md)
