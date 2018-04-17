---
title: Zarządzanie usługą Azure Key Vault przy użyciu automatyzacji Azure | Dokumentacja firmy Microsoft
description: Więcej informacji na temat sposobu usługi Automatyzacja Azure może służyć do zarządzania usługi Azure Key Vault.
services: Key-Vault, automation
documentationcenter: ''
author: mgoedtel
manager: jwhit
editor: ''
ms.assetid: 4e780762-19b6-4ca6-b894-ebb44c538f35
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/29/2016
ms.author: magoedte
ms.openlocfilehash: b5f8a1b826717d51729b0bb621bf26e35d4bdd36
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2018
---
# <a name="managing-azure-key-vault-using-azure-automation"></a>Zarządzanie usługą Azure Key Vault za pomocą usługi Automatyzacja Azure
W tym przewodniku przedstawiono usługi Automatyzacja Azure oraz jak on używany w celu uproszczenia zarządzania kluczy i kluczy tajnych w magazynie kluczy Azure.

## <a name="what-is-azure-automation"></a>Co to jest Azure Automation?
[Automatyzacja Azure](../automation/automation-intro.md) jest usługą platformy Azure, dla uproszczenia zarządzania chmurą za pośrednictwem automatyzacji procesu i konfiguracji żądanego stanu. Przy użyciu automatyzacji Azure, ręczne, powtarzane, długotrwałe i podatne na błędy zadań można zautomatyzować, aby zwiększyć niezawodność, wydajność i wartość czasu dla Twojej organizacji.

Automatyzacja Azure umożliwia aparatowi wykonywania przepływów pracy wysoce niezawodne, wysokiej dostępności, która może obsłużyć do własnych potrzeb. W automatyzacji Azure procesów może być rozpoczęte ręcznie, przez systemy 3rd firmy lub w zaplanowanych odstępach czasu tak, aby zadania stanie dokładnie w razie potrzeby.

Obniżenie kosztów operacyjnych i zwolnić IT i pracownicy DevOps skupić się na pracy dodającego wartość biznesową przez przeniesienie zadań zarządzania chmury do automatycznego uruchamiania przez usługi Automatyzacja Azure.

## <a name="how-can-azure-automation-help-manage-azure-key-vault"></a>Jak usługi Automatyzacja Azure ułatwia zarządzanie usługą Azure Key Vault?
Za pomocą można zarządzać w automatyzacji Azure Key Vault [poleceń cmdlet usługi Key Vault AzureRM](https://www.powershellgallery.com/packages/AzureRM.KeyVault/1.1.4) i [poleceń cmdlet klasycznej usługi Azure Key Vault](https://msdn.microsoft.com/library/azure/dn868052.aspx). Moduł Azure do zarządzania klasycznym usługi Key Vault jest dostępna automatycznie w automatyzacji Azure i można zaimportować [AzureRM KeyVault modułu](https://www.powershellgallery.com/packages/AzureRM.KeyVault/1.1.4) w automatyzacji Azure tak, aby można było wykonać wiele zadań zarządzania w ramach usługi Key Vault. Można również skojarzyć te polecenia cmdlet usługi Automatyzacja Azure, za pomocą poleceń cmdlet dla innych usług Azure, aby zautomatyzować złożone zadania 3 systemów firm i usług Azure.

Za pomocą poleceń cmdlet usługi Azure Key Vault może wykonać te zadania, między innymi: 

* Tworzenie i Konfigurowanie magazynu kluczy
* Tworzenie lub Importowanie klucza
* Utwórz lub zaktualizuj klucz tajny
* Atrybuty aktualizacji klucza
* Pobierz klucz lub klucz tajny
* Usuwanie klucza lub klucza tajnego

Oto kilka przykładów przy użyciu programu PowerShell do zarządzania usługi Key Vault:  

* [Usługi Azure Key Vault - krok po kroku](https://blogs.technet.microsoft.com/kv/2015/06/02/azure-key-vault-step-by-step)
* [Instalowanie i konfigurowanie usługi Azure Key Vault](https://www.simple-talk.com/cloud/platform-as-a-service/setting-up-and-configuring-an-azure-key-vault)

## <a name="next-steps"></a>Kolejne kroki
Teraz, kiedy znasz już podstawy usługi Automatyzacja Azure i jak może służyć do zarządzania usługą Azure Key Vault, skorzystaj z poniższych linków, aby dowiedzieć się więcej na temat automatyzacji Azure.

* Zobacz automatyzacji Azure [Wprowadzenie — samouczek](../automation/automation-first-runbook-graphical.md).
* Zobacz [skryptów programu PowerShell magazynu kluczy Azure](https://gallery.technet.microsoft.com/scriptcenter/site/search?query=azure%20key%20vault&f%5B0%5D.Value=azure%20key%20vault&f%5B0%5D.Type=SearchText&ac=5).

