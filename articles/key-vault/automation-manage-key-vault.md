---
title: Zarządzanie usługi Azure Key Vault przy użyciu usługi Azure Automation — usłudze Azure Key Vault | Dokumentacja firmy Microsoft
description: Naucz się, jak używać usługi Azure Automation do zarządzania usługi Azure Key Vault.
services: Key-Vault, automation
author: mgoedtel
manager: jwhit
editor: ''
ms.service: key-vault
ms.topic: conceptual
ms.date: 01/07/2019
ms.author: magoedte
ms.openlocfilehash: 87fdb565f47e7d88342c82bd5940c1ddb9c137e6
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "64712129"
---
# <a name="managing-azure-key-vault-using-azure-automation"></a>Zarządzanie usługi Azure Key Vault przy użyciu usługi Azure Automation

Ten przewodnik Wprowadzenie do usługi Azure Automation i jak może służyć do uproszczenia zarządzania kluczy i wpisów tajnych w usłudze Azure Key Vault.

## <a name="what-is-azure-automation"></a>Co to jest Azure Automation?

[Usługa Azure Automation](../automation/automation-intro.md) jest usługą platformy Azure dla uproszczenie zarządzania chmurą poprzez automatyzację procesów i konfiguracja żądanego stanu. Za pomocą usługi Azure Automation ręcznego powtarzany, zadań długotrwałych i podatnych można zautomatyzować zwiększyć niezawodność, wydajność i czasy wykonywania operacji dla Twojej organizacji.

Usługa Azure Automation zapewnia aparatu wykonywania przepływów pracy o wysokiej niezawodności, wysokiej dostępności, która skaluje się do swoich potrzeb. W usłudze Azure Automation procesów może być rozpoczęła się ręcznie, przez systemy innych firm 3 lub w zaplanowanych odstępach czasu tak, aby zadania się tak zdarzyć, dokładnie tak, gdy jest to potrzebne.

Obniżenie kosztów operacyjnych i Zwolnij część IT oraz pracownicy działu DevOps, aby skupić się na pracy, która przynosi wymierne efekty, przenosząc zadań zarządzania systemem chmury uruchamiana automatycznie przez usługę Azure Automation.

## <a name="how-can-azure-automation-help-manage-azure-key-vault"></a>Jak usługa Azure Automation ułatwiają zarządzanie usługi Azure Key Vault?

Usługa Key Vault można zarządzać w usłudze Azure Automation za pomocą [poleceń cmdlet usługi AzureRM Key Vault](https://www.powershellgallery.com/packages/AzureRM.KeyVault/1.1.4) i [poleceń cmdlet klasycznej usługi Azure Key Vault](https://docs.microsoft.com/powershell/module/servicemanagement/azure). Moduł platformy Azure do zarządzania klasycznej usługi Key Vault jest dostępnych automatycznie w usłudze Azure Automation, a następnie można zaimportować [moduł AzureRM KeyVault](https://www.powershellgallery.com/packages/AzureRM.KeyVault/1.1.4) do usługi Azure Automation, dzięki czemu można wykonać wiele zadań zarządzania systemem usługi Key Vault w ramach usługi. Aby dowiedzieć się więcej o sposobie importowania modułu do usługi Azure Automation, zobacz [zarządzania modułami w usłudze Azure Automation](../automation/shared-resources/modules.md) może również łączyć się tych poleceń cmdlet w usłudze Azure Automation za pomocą poleceń cmdlet dla innych usług platformy Azure w celu zautomatyzowania złożonych zadań w Usługi platformy Azure i systemami firm 3.

Za pomocą poleceń cmdlet usługi Azure Key Vault można wykonywać te zadania, między innymi: 

* Tworzenie i konfigurowanie usługi key vault
* Tworzenie lub Importowanie klucza
* Utwórz lub zaktualizuj wpis tajny
* Aktualizowanie atrybutów klucza
* Pobieranie klucza lub klucza tajnego
* Usuwanie klucza lub klucza tajnego

Poniżej przedstawiono kilka przykładów przy użyciu programu PowerShell do zarządzania usługi Key Vault:  

* [Usługa Azure Key Vault — krok po kroku](https://blogs.technet.microsoft.com/kv/2015/06/02/azure-key-vault-step-by-step)
* [Instalowanie i konfigurowanie usługi Azure Key Vault](https://www.simple-talk.com/cloud/platform-as-a-service/setting-up-and-configuring-an-azure-key-vault)

## <a name="next-steps"></a>Kolejne kroki

Teraz, kiedy znasz już podstawy usługi Azure Automation i jak może służyć do zarządzania usługi Azure Key Vault, skorzystaj z poniższych linków, aby dowiedzieć się więcej o usłudze Azure Automation.

* Zobacz usługi Azure Automation [Wprowadzenie — samouczek](../automation/automation-first-runbook-graphical.md).
* Zobacz [skrypty programu PowerShell dla usługi Azure Key Vault](https://gallery.technet.microsoft.com/scriptcenter/site/search?query=azure%20key%20vault&f%5B0%5D.Value=azure%20key%20vault&f%5B0%5D.Type=SearchText&ac=5).

