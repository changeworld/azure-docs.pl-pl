---
title: Dołączyć rozwiązań zarządzania aktualizacjami, śledzenia zmian i magazynu z maszyny Wirtualnej platformy Azure
description: Dowiedz się, jak można dołączyć maszyny wirtualnej platformy Azure z rozwiązaniami do zarządzania aktualizacjami, śledzenia zmian i spisu które są częścią usługi Automatyzacja Azure.
services: automation
author: georgewallace
ms.author: gwallace
ms.date: 06/06/2018
ms.topic: conceptual
ms.service: automation
ms.custom: mvc
manager: carmonm
ms.openlocfilehash: f270b2ccea51e83bc6475051b8667bf73d7fd717
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/19/2018
ms.locfileid: "36221516"
---
# <a name="onboard-update-management-change-tracking-and-inventory-solutions-from-an-azure-virtual-machine"></a>Dołączyć rozwiązań zarządzania aktualizacjami, śledzenia zmian i magazynu z maszyny wirtualnej platformy Azure

Automatyzacja Azure udostępnia rozwiązania ułatwiające zarządzanie aktualizacjami zabezpieczeń systemu operacyjnego, śledzenie zmian i spisu zainstalowanych na komputerach. Istnieje wiele sposobów, aby dołączyć maszyn. Możesz dołączyć rozwiązania z maszyny wirtualnej, [z Twojego konta automatyzacji](automation-onboard-solutions-from-automation-account.md), [z wieloma maszynami przeglądania](automation-onboard-solutions-from-browse.md), lub za pomocą [runbook](automation-onboard-solutions.md). W tym artykule omówiono dołączania tych rozwiązań z maszyny wirtualnej platformy Azure.

## <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

Zaloguj się do witryny Azure Portal pod adresem https://portal.azure.com.

## <a name="enable-the-solutions"></a>Włącz rozwiązania

Przejdź do istniejącej maszyny wirtualnej. W obszarze **operacji**, wybierz pozycję **zarządzanie aktualizacjami**, **spisu**, lub **śledzenia zmian**.

Aby włączyć rozwiązania dla tylko maszynę Wirtualną, upewnij się, że **Włącz dla tej maszyny Wirtualnej** jest zaznaczone. Aby dołączyć wiele maszyn do rozwiązania, wybierz **włączanie dla maszyn wirtualnych w tej subskrypcji**, a następnie wybierz **kliknij, aby wybrać komputerach, aby umożliwić**. Aby dowiedzieć się, jak Aby dołączyć wiele maszyn jednocześnie, zobacz [rozwiązania zintegrowanego zarządzania aktualizacjami, śledzenia zmian i spisu](automation-onboard-solutions-from-automation-account.md).

Wybierz obszar roboczy analizy dzienników Azure oraz konta automatyzacji, a następnie wybierz **włączyć** umożliwiające rozwiązanie. Włączanie rozwiązania może trwać do 15 minut.

![Dodaj rozwiązanie do zarządzania aktualizacjami](media/automation-onboard-solutions-from-vm/onboard-solution.png)

Przejdź do innych rozwiązań, a następnie wybierz **włączyć**. Listy rozwijanej konta analizy dzienników i automatyzacji są wyłączone, ponieważ te rozwiązania użyć tego samego obszaru roboczego i konto automatyzacji jako rozwiązanie wcześniej włączony.

> [!NOTE]
> **Śledzenie zmian** i **spisu** za pomocą tego samego rozwiązania. Jeśli jedno z tych rozwiązań jest włączona, druga również jest włączona.

## <a name="scope-configuration"></a>Konfiguracja zakresu

Każde rozwiązanie używa konfiguracji zakresu w obszarze roboczym pod kątem komputerów, które rozwiązanie. Ta konfiguracja zakresu to grupa co najmniej jeden zapisanych wyszukiwań, które są używane do ograniczenia zakresu rozwiązanie do określonych komputerów. W obszarze dostępu konfiguracji zakresu, na Twoim koncie automatyzacji do **powiązane zasoby**, wybierz pozycję **obszaru roboczego**. W obszarze roboczym w obszarze **źródeł danych obszaru roboczego**, wybierz pozycję **konfiguracji zakresu**.

Jeśli wybranego obszaru roboczego nie ma już rozwiązania zarządzania aktualizacjami lub śledzenia zmian, tworzone są następujące konfiguracje w zakresie:

* **Śledzenia zmian MicrosoftDefaultScopeConfig**

* **Aktualizacje MicrosoftDefaultScopeConfig**

Jeśli wybranego obszaru roboczego ma już rozwiązania, rozwiązanie nie jest wdrożone i nie jest dodawany konfigurację zakresu.

Wybierz wielokropek (**...** ) na wszystkich konfiguracji, a następnie wybierz **Edytuj**. W **konfigurację zakresu edycji** okienku wybierz **wybierz grupy komputerów**. **Grup komputerów** w okienku zostaną wyświetlone zapisane wyszukiwania, które są używane do tworzenia zakresu konfiguracji.

## <a name="saved-searches"></a>Zapisane wyszukiwania

Po dodaniu komputera do zarządzania aktualizacjami, śledzenia zmian lub rozwiązań magazynu, komputer jest dodawany do jednej z dwóch zapisane wyszukiwania w obszarze roboczym. Zapisane wyszukiwania są zapytania, które zawierają komputery, które są przeznaczone dla tych rozwiązań.

Przejdź do swojego obszaru roboczego. W obszarze **ogólne**, wybierz pozycję **zapisane wyszukiwania**. Dwa zapisanych wyszukiwań, które są używane przez te rozwiązania są przedstawione w poniższej tabeli:

|Name (Nazwa)     |Kategoria  |Alias  |
|---------|---------|---------|
|MicrosoftDefaultComputerGroup     |  Śledzenia zmian       | ChangeTracking__MicrosoftDefaultComputerGroup        |
|MicrosoftDefaultComputerGroup     | Aktualizacje        | Updates__MicrosoftDefaultComputerGroup         |

Wybierz jedną z zapisane wyszukiwania, aby wyświetlić zapytania, który służy do wypełniania grupy. Na poniższej ilustracji przedstawiono zapytania, a jego wyniki:

![Zapisane wyszukiwania](media/automation-onboard-solutions-from-vm/logsearch.png)

## <a name="next-steps"></a>Kolejne kroki

Nadal samouczki dotyczące rozwiązania, aby dowiedzieć się, jak ich używać:

* [Samouczek — Zarządzanie aktualizacji dla maszyny Wirtualnej](automation-tutorial-update-management.md)
* [Samouczek — identyfikacji oprogramowania na maszynie Wirtualnej](automation-tutorial-installed-software.md)
* [Samouczek — Rozwiązywanie problemów z zmiany na maszynie Wirtualnej](automation-tutorial-troubleshoot-changes.md)
