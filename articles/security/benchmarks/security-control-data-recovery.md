---
title: Kontrola zabezpieczeń platformy Azure — odzyskiwanie danych
description: Odzyskiwanie danych kontroli zabezpieczeń
author: msmbaldwin
manager: rkarlin
ms.service: security
ms.topic: conceptual
ms.date: 12/30/2019
ms.author: mbaldwin
ms.custom: security-recommendations
ms.openlocfilehash: c585ebd903d4070f6247456e06efffbc6ec45270
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75934504"
---
# <a name="security-control-data-recovery"></a>Kontrola bezpieczeństwa: Odzyskiwanie danych

Upewnij się, że wszystkie dane systemowe, konfiguracje i wpisy tajne są regularnie archiwizowane automatycznie.

## <a name="91-ensure-regular-automated-back-ups"></a>9.1: Zapewnienie regularnych automatycznych kopii zapasowych

| Identyfikator platformy Azure | Identyfikatory SIC | Odpowiedzialność |
|--|--|--|
| 9.1 | 10.1 | Klient |

Włącz usługę Azure Backup i skonfiguruj źródło kopii zapasowej (maszyny wirtualne platformy Azure, SQL Server lub udziały plików), a także żądaną częstotliwość i okres przechowywania.

Jak włączyć usługę Azure Backup:

https://docs.microsoft.com/azure/backup/

## <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2: Wykonywanie kompletnych kopii zapasowych systemu i tworzenie kopii zapasowych dowolnych kluczy zarządzanych przez klienta

| Identyfikator platformy Azure | Identyfikatory SIC | Odpowiedzialność |
|--|--|--|
| 9.2 | 10.2 | Klient |

Włącz usługę Azure Backup i docelowych maszyn wirtualnych, a także żądaną częstotliwość i okresy przechowywania. Tworzenie kopii zapasowych kluczy zarządzanych przez klienta w usłudze Azure Key Vault.

Jak włączyć usługę Azure Backup:

https://docs.microsoft.com/azure/backup/

Jak wykonać kopię zapasową kluczy magazynu kluczy na platformie Azure:

https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey?view=azurermps-6.13.0

## <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3: Sprawdzanie poprawności wszystkich kopii zapasowych, w tym kluczy zarządzanych przez klienta

| Identyfikator platformy Azure | Identyfikatory SIC | Odpowiedzialność |
|--|--|--|
| 9.3 | 10.3 | Klient |

Zapewnij możliwość okresowego przywracania zawartości w ramach usługi Azure Backup. Jeśli to konieczne, test przywrócić do izolowanej sieci VLAN. Przetestuj przywrócenie kopii zapasowych kluczy zarządzanych przez klienta.

Jak odzyskać pliki z kopii zapasowej maszyny wirtualnej platformy Azure:

https://docs.microsoft.com/azure/backup/backup-azure-restore-files-from-vm

Jak przywrócić klucze magazynu kluczy na platformie Azure:

https://docs.microsoft.com/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey?view=azurermps-6.13.0

## <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4: Zapewnienie ochrony kopii zapasowych i kluczy zarządzanych przez klienta

| Identyfikator platformy Azure | Identyfikatory SIC | Odpowiedzialność |
|--|--|--|
| 9.4 | 10.4 | Klient |

W przypadku kopii zapasowych w środowisku lokalnym szyfrowanie danych w spoczynku odbywa się przy użyciu hasła podanego podczas tworzenia kopii zapasowej na platformie Azure. W przypadku maszyn wirtualnych platformy Azure dane są szyfrowane w spoczynku przy użyciu szyfrowania usługi Storage (SSE). W magazynie kluczy można włączyć funkcję Usuwania programów nieużytowych, aby chronić klucze przed przypadkowym lub złośliwym usunięciem.

Jak włączyć usuwanie programowe w przechowalni kluczy:

https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete?tabs=azure-portal

## <a name="next-steps"></a>Następne kroki

Zobacz następną kontrolę zabezpieczeń: [Reagowanie na incydenty](security-control-incident-response.md)
