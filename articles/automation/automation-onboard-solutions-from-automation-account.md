---
title: Dowiedz się, jak dołączyć rozwiązań zarządzania aktualizacjami, śledzenia zmian i spisu w usłudze Automatyzacja Azure
description: Dowiedz się, jak można dołączyć wirtualnej Azure maszyny z rozwiązań zarządzania aktualizacjami, śledzenia zmian i magazynu, które są częścią usługi Automatyzacja Azure
services: automation
ms.service: automation
author: georgewallace
ms.author: gwallace
ms.date: 03/16/2018
ms.topic: conceptual
manager: carmonm
ms.custom: mvc
ms.openlocfilehash: 2f5d664b660d43e61dba46d13aff1ced796de884
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/16/2018
---
# <a name="onboard-update-management-change-tracking-and-inventory-solutions"></a>Dołączyć rozwiązań do zarządzania aktualizacjami, śledzenia zmian i magazynu

Automatyzacja Azure zapewnia rozwiązania do zarządzania aktualizacjami zabezpieczeń systemu operacyjnego, śledzenie zmian i magazynowych zainstalowanych w komputerach. Istnieje wiele sposobów, aby dołączyć maszyny, możesz dołączyć rozwiązania [z maszyny wirtualnej](automation-onboard-solutions-from-vm.md), z Twojego konta automatyzacji lub przez [runbook](automation-onboard-solutions.md). W tym artykule omówiono dołączania tych rozwiązań z Twojego konta automatyzacji.

## <a name="log-in-to-azure"></a>Zaloguj się do platformy Azure.

Logowanie do platformy Azure w https://portal.azure.com

## <a name="enable-solutions"></a>Włączanie rozwiązań

Przejdź do swojego konta automatyzacji i wybierz opcję **spisu** lub **śledzenie zmian** w obszarze **zarządzania konfiguracją**.

Wybierz obszar roboczy analizy dzienników i konto usługi Automatyzacja i kliknij **włączyć** umożliwiające rozwiązanie. Włączanie rozwiązania może trwać do 15 minut.

![Zintegrowanego rozwiązania magazynu](media/automation-onboard-solutions-from-automation-account/onboardsolutions.png)

Rozwiązanie Change Tracking and Inventory umożliwia [śledzenie zmian](automation-vm-change-tracking.md) i tworzenie [spisu](automation-vm-inventory.md) na maszynach wirtualnych. W tym kroku włączysz rozwiązanie na maszynie wirtualnej.

Po zakończeniu powiadomienia o dołączeniu rozwiązania do śledzenia zmian i spisu kliknij pozycję **Update Management** w obszarze **ZARZĄDZANIE KONFIGURACJĄ**.

Rozwiązanie Update Management umożliwia zarządzanie aktualizacjami i poprawkami dla maszyn wirtualnych z systemem Windows platformy Azure. Można ocenić stan dostępnych aktualizacji, zaplanować instalację wymaganych aktualizacji i przejrzeć wyniki wdrażania, aby sprawdzić, czy aktualizacje zostały zastosowane pomyślnie do maszyny wirtualnej. Ta akcja włączone rozwiązania dla maszyny Wirtualnej.

Wybierz **zarządzanie aktualizacjami** w obszarze **zarządzania AKTUALIZACJAMI**. Wybrany obszar roboczy usługi Log Analytics jest tym samym obszarem roboczym, który był używany w poprzednim kroku. Kliknij przycisk **Włącz**, aby dołączyć rozwiązanie Update management. Włączanie rozwiązania może trwać do 15 minut.

![Rozwiązanie dołączenia aktualizacji](media/automation-onboard-solutions-from-automation-account/onboardsolutions2.png)

## <a name="scope-configuration"></a>Konfiguracja zakresu

Każde rozwiązanie używa konfiguracji zakresu w obszarze roboczym pod kątem komputerów, które rozwiązanie. Konfiguracja zakresu jest grupą co najmniej jeden zapisanych wyszukiwań służy do ograniczania zakresu rozwiązanie do określonych komputerów. Do konfiguracji zakresu, na Twoim koncie automatyzacji w obszarze **powiązane zasoby**, wybierz pozycję **obszaru roboczego**. Następnie w obszarze roboczym **źródeł danych obszaru roboczego**, wybierz pozycję **konfiguracji zakresu**.

Jeśli wybranego obszaru roboczego nie ma rozwiązania zarządzania aktualizacjami lub śledzenia zmian, ale są tworzone następujące konfiguracje w zakresie:

* **Śledzenia zmian MicrosoftDefaultScopeConfig**

* **Aktualizacje MicrosoftDefaultScopeConfig**

Jeśli wybranego obszaru roboczego jest już w rozwiązaniu. Rozwiązanie nie jest ponownie wdrożone i konfigurację zakresu nie zostaną dodane do niego.

## <a name="saved-searches"></a>Zapisane wyszukiwania

Po dodaniu komputera do zarządzania aktualizacjami lub śledzenia zmian i spisu rozwiązań, ich dodawania do jednej z dwóch zapisane wyszukiwania w obszarze roboczym. Zapisane wyszukiwania są zapytania, które zawierają komputery, które są przeznaczone dla tych rozwiązań.

Przejdź do swojego konta automatyzacji i wybierz **zapisane wyszukiwania** w obszarze **ogólne**. Dwa zapisanych wyszukiwań używane przez te rozwiązania są widoczne w poniższej tabeli:

|Name (Nazwa)     |Kategoria  |Alias  |
|---------|---------|---------|
|MicrosoftDefaultComputerGroup     |  Śledzenia zmian       | ChangeTracking__MicrosoftDefaultComputerGroup        |
|MicrosoftDefaultComputerGroup     | Aktualizacje        | Updates__MicrosoftDefaultComputerGroup         |

Wybierz albo zapisane wyszukiwanie, aby wyświetlić zapytania używany do wypełniania grupy. Na poniższej ilustracji przedstawiono zapytania, a jego wyniki:

![Zapisane wyszukiwania](media/automation-onboard-solutions-from-automation-account/savedsearch.png)

## <a name="onboard-an-azure-machine"></a>Dołączyć maszynę platformy Azure

Twoje automatyzacji konta wybierz **spisu** lub **śledzenie zmian** w obszarze **zarządzania konfiguracją**, lub **zarządzanie aktualizacjami** w obszarze **zarządzania AKTUALIZACJAMI**.

Kliknij przycisk **+ Dodaj maszyny Wirtualnej Azure**, wybierz maszynę Wirtualną z listy. Na **zarządzania aktualizacjami** kliknij przycisk **włączyć**. Spowoduje to dodanie bieżącej maszyny Wirtualnej do grupy komputerów zapisanego wyszukiwania dla rozwiązania.

## <a name="onboard-a-non-azure-machine"></a>Dołączanie maszyny spoza platformy Azure

Twoje automatyzacji konta wybierz **spisu** lub **śledzenie zmian** w obszarze **zarządzania konfiguracją**, lub **zarządzanie aktualizacjami** w obszarze **zarządzania AKTUALIZACJAMI**.

Kliknij przycisk **maszynę z systemem innym niż Azure Dodaj**. Spowoduje to otwarcie się nowe okno przeglądarki z instrukcjami dotyczącymi sposobu instalowania i konfigurowania programu Microsoft Monitoring Agent na komputerze, komputer można rozpocząć raportowania do rozwiązania. W przypadku przechodzenia do komputera, który obecnie zarządzany przez program System Center Operations Manager nie jest wymagany nowy agent, informacje o obszarze roboczym jest wprowadzany do istniejącego agenta.

## <a name="onboard-machines-in-the-workspace"></a>Dołączenia maszyny w obszarze roboczym

Twoje automatyzacji konta wybierz **spisu** lub **śledzenie zmian** w obszarze **zarządzania konfiguracją**, lub **zarządzanie aktualizacjami** w obszarze **zarządzania AKTUALIZACJAMI**.

Wybierz **Zarządzanie maszynami**. Spowoduje to otwarcie **Zarządzanie maszyny** strony. Ta strona umożliwia rozwiązanie na wybierz zestaw komputerów, wszystkie dostępne maszyny, Włącz lub Włącz rozwiązania na wszystkich komputerach bieżąca i włączenie go dla wszystkich przyszłych maszyn.

![Zapisane wyszukiwania](media/automation-onboard-solutions-from-automation-account/managemachines.png)

### <a name="selected-machines"></a>Wybrane maszyny

Aby włączyć rozwiązania dla co najmniej jednej maszyny, zaznacz **włączyć na wybranych komputerach** i kliknij przycisk **dodać** obok każdego komputera ma zostać dodany do rozwiązania. To zadanie dodaje nazwy wybranych komputerów do grupy komputerów zapisane kwerendy wyszukiwania dla rozwiązania.

### <a name="all-available-machines"></a>Wszystkie dostępne maszyny

Aby włączyć rozwiązania dla wszystkich maszyn dostępna, zaznacz **Włącz na wszystkich komputerach dostępne**. Powoduje wyłączenie formantu można pojedynczo dodać maszyny. To zadanie dodaje wszystkie nazwy komputerów, raportowanie do obszaru roboczego do grupy komputerów zapisane kwerendy wyszukiwania.

### <a name="all-available-and-future-machines"></a>Wszystkie dostępne i przyszłych maszyny

Aby włączyć rozwiązania dla wszystkich dostępnych maszyn i wszystkie maszyny w przyszłości, zaznacz **Włącz na wszystkich komputerach dostępne i przyszłych**. Ta opcja usuwa zapisanych wyszukiwań i konfiguracji zakresu, w obszarze roboczym. Spowoduje to otwarcie rozwiązania do wszystkich Azure i maszyny z systemem innym niż Azure, które raporty do obszaru roboczego.

## <a name="next-steps"></a>Kolejne kroki

Nadal samouczki dotyczące rozwiązań informacje na temat korzystania z nich.

* [Samouczek — Zarządzanie aktualizacji dla maszyny Wirtualnej](automation-tutorial-update-management.md)

* [Samouczek — identyfikacji oprogramowania na maszynie Wirtualnej](automation-tutorial-installed-software.md)

* [Samouczek — Rozwiązywanie problemów z zmiany na maszynie Wirtualnej](automation-tutorial-troubleshoot-changes.md)