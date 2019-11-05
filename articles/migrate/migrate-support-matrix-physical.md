---
title: Macierz obsługi Azure Migrate na potrzeby oceny i migracji serwera fizycznego
description: Podsumowuje ustawienia i ograniczenia dotyczące oceny i migracji serwera fizycznego przy użyciu usługi Azure Migrate.
author: rayne-wiselman
manager: carmonm
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 10/23/2019
ms.author: raynew
ms.openlocfilehash: 56e49ab60733c9c0a813ca6e46712bcd8052a2ef
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73518153"
---
# <a name="support-matrix-for-physical-server-assessment-and-migration"></a>Macierz obsługi na potrzeby oceny i migracji serwera fizycznego

Za pomocą [usługi Azure Migrate](migrate-overview.md) można oceniać i migrować maszyny do chmury Microsoft Azure. Ten artykuł podsumowuje ustawienia i ograniczenia dotyczące obsługi oceniania i migrowania lokalnych serwerów fizycznych.


## <a name="physical-server-scenarios"></a>Scenariusze dotyczące serwera fizycznego

W tabeli zestawiono obsługiwane scenariusze dla serwerów fizycznych.

**Wdrożenie** | **Szczegóły***
--- | ---
**Ocenianie lokalnych serwerów fizycznych** | [Skonfiguruj](tutorial-prepare-physical.md) swoją pierwszą ocenę.
**Migrowanie serwerów fizycznych na platformę Azure** | [Wypróbuj](tutorial-migrate-physical-virtual-machines.md) migrację do platformy Azure.


## <a name="azure-migrate-projects"></a>Projekty Azure Migrate

**Pomoc techniczna** | **Szczegóły**
--- | ---
**Uprawnienia platformy Azure** | Aby utworzyć projekt Azure Migrate, musisz mieć uprawnienia współautora lub właściciela w ramach subskrypcji.
**Serwery fizyczne** | Oceń do 250 serwerów fizycznych w jednym projekcie. W ramach subskrypcji platformy Azure można mieć wiele projektów. Projekt może obejmować serwery fizyczne, maszyny wirtualne VMware i maszyny wirtualne funkcji Hyper-V, a także limity oceny.
**Lokalizacja geograficzna** | Możesz tworzyć Azure Migrate projekty w wielu lokalizacje geograficzneach. Chociaż można tworzyć projekty w określonych lokalizacje geograficzne, można ocenić lub migrować maszyny pod kątem innych lokalizacji docelowych. Lokalizacja geograficzna projektu służy tylko do przechowywania odnalezionych metadanych.

  **Lokalizacja geograficzna** | **Lokalizacja magazynu metadanych**
  --- | ---
  Azure Government | Administracja USA — Wirginia
  Azja i Pacyfik | Azja Wschodnia lub Azja Południowo-Wschodnia
  Australia | Australia Wschodnia lub Australia Południowo-Wschodnia
  Brazylia | Brazylia Południowa
  Kanada | Kanada środkowa lub Kanada Wschodnia
  Europa | Europa Północna lub Europa Zachodnia
  Francja | Francja Środkowa
  Indie | Indie Środkowe lub Indie Południowe
  Japonia |  Japonia Wschodnia lub Japonia Zachodnia
  Korea Południowa | Korea środkowa lub Korea Południowa
  Wielka Brytania | Południowe Zjednoczone Królestwo lub Zachodnie Zjednoczone Królestwo
  Stany Zjednoczone | Środkowe stany USA lub zachodnie stany USA 2


 > [!NOTE]
 > Obsługa Azure Government jest obecnie dostępna tylko dla [starszej wersji](https://docs.microsoft.com/azure/migrate/migrate-services-overview#azure-migrate-versions) programu Azure Migrate.


## <a name="assessment-physical-server-requirements"></a>Ocena — wymagania dotyczące serwera fizycznego

| **Pomoc techniczna**                | **Szczegóły**               
| :-------------------       | :------------------- |
| **Wdrożenie serwera fizycznego**       | Serwer fizyczny może być autonomiczny lub wdrożony w klastrze. |
| **Uprawnienia**           | **System Windows:** Skonfiguruj konto użytkownika lokalnego na wszystkich serwerach z systemem Windows, które mają zostać uwzględnione w odnajdywaniu. Należy dodać konto użytkownika do tych grup — Użytkownicy Pulpit zdalny, użytkownicy i Dzienniki wydajności. <br/> System **Linux:** Na serwerach z systemem Linux, które mają zostać odnajdywane, jest potrzebne konto główne. |
| **System operacyjny** | Wszystkie systemy operacyjne [Windows](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines) i [Linux](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros) , które są obsługiwane przez platformę Azure. |


## <a name="assessment-appliance-requirements"></a>Ocena — wymagania dotyczące urządzenia

W celu oceny Azure Migrate uruchamia lekkie urządzenie w celu odnajdywania serwerów fizycznych oraz wysyłania metadanych serwera i danych wydajności do Azure Migrate. Urządzenie można uruchomić na serwerze fizycznym lub maszynie wirtualnej i skonfigurować je przy użyciu skryptu programu PowerShell pobranego z Azure Portal. Poniższa tabela zawiera podsumowanie wymagań dotyczących urządzeń.

| **Pomoc techniczna**                | **Szczegóły**               
| :-------------------       | :------------------- |
| **Wdrażanie urządzenia**   |  Urządzenie jest wdrażane na serwerze fizycznym lub na maszynie wirtualnej.<br/>  Na maszynie hosta musi działać system Windows Server 2012 R2 lub nowszy.<br/> Host wymaga wystarczającej ilości miejsca, aby przydzielić 16 GB pamięci RAM, 8 procesorów wirtualnych vCPU, około 80 GB miejsca do magazynowania oraz przełącznik zewnętrzny dla maszyny wirtualnej urządzenia.<br/> Urządzenie musi mieć statyczny lub dynamiczny adres IP oraz dostęp do Internetu.
| **Projekt Azure Migrate**  |  Urządzenie może być skojarzone z pojedynczym projektem.<br/> Dowolna liczba urządzeń może być skojarzona z pojedynczym projektem.<br/> Można ocenić do 35 000 maszyn w projekcie.
| **Odnajdowa**              | Pojedyncze urządzenie może wykryć do 200 serwerów.
| **Grupa oceny**       | Można dodać do 35 000 maszyn w jednej grupie.
| **Ocena**             | W ramach jednej oceny można ocenić do 35 000 maszyn.


## <a name="assessment-appliance-url-access"></a>Ocena — dostęp do adresu URL urządzenia

Do oceny maszyn wirtualnych urządzenie Azure Migrate musi mieć łączność z Internetem.

- Podczas wdrażania urządzenia Azure Migrate sprawdza połączenie adresów URL, które zostały podsumowane w poniższej tabeli.
- Jeśli używasz serwera proxy opartego na adresie URL, Zezwól na dostęp do adresów URL w tabeli, upewniając się, że serwer proxy rozpoznaje wszystkie rekordy CNAME otrzymane podczas wyszukiwania adresów URL.
- W przypadku przechwycenia serwera proxy może zaistnieć konieczność zaimportowania certyfikatu serwera z serwera proxy do urządzenia.


**Adres URL** | **Szczegóły**  
--- | ---
*.portal.azure.com | Nawigacja do Azure Portal
*.windows.net <br/> *.msftauth.net <br/> *.msauth.net <br/> *. microsoft.com <br/> *. live.com  | Zaloguj się do Twojej subskrypcji platformy Azure.
*.microsoftonline.com <br/> *. microsoftonline-p.com | Tworzenie aplikacji Azure Active Directory na potrzeby komunikacji urządzeń z usługą.
management.azure.com | Tworzenie aplikacji Azure Active Directory na potrzeby komunikacji urządzeń z usługą.
dc.services.visualstudio.com | Rejestrowanie i monitorowanie
*.vault.azure.net | Zarządzaj wpisami tajnymi w Azure Key Vault podczas komunikacji między urządzeniem i usługą.
aka.ms/* | Zezwalaj na dostęp do linków aliasów.
https://download.microsoft.com/download/* | Zezwala na pobieranie z witryny pobierania firmy Microsoft.



## <a name="assessment-port-requirements"></a>Ocena — wymagania dotyczące portów

Poniższa tabela zawiera podsumowanie wymagań dotyczących portów dla oceny.

**Pliku** | **Połączenie**
--- | ---
**Wprowadzony** | Połączenia przychodzące na porcie TCP 3389, aby zezwolić na połączenia pulpitu zdalnego z urządzeniem.<br/> Połączenia przychodzące na porcie 44368 do zdalnego dostępu do aplikacji do zarządzania urządzeniami przy użyciu adresu URL: ``` https://<appliance-ip-or-name>:44368 ```<br/> Połączenia wychodzące na portach 443, 5671 i 5672 do wysyłania metadanych odnajdywania i wydajności do Azure Migrate.
**Serwery fizyczne** | **System Windows:** Połączenia przychodzące na portach 443, 5989 do ściągania metadanych konfiguracji i wydajności z serwerów z systemem Windows. <br/> System **Linux:**  Połączenia przychodzące na porcie 22 (UDP) do ściągania metadanych konfiguracji i wydajności z serwerów z systemem Linux. |


## <a name="next-steps"></a>Następne kroki

[Przygotuj się do oceny serwera fizycznego](tutorial-prepare-physical.md) na potrzeby oceny i migracji serwera fizycznego.
