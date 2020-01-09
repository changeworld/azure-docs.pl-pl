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
ms.openlocfilehash: 1cd3e39fd504b5095a83192a4a6314c71d3ca980
ms.sourcegitcommit: 5925df3bcc362c8463b76af3f57c254148ac63e3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/31/2019
ms.locfileid: "75564298"
---
# <a name="security-control-data-recovery"></a>Kontrola zabezpieczeń: odzyskiwanie danych

Upewnij się, że regularnie tworzone są kopie zapasowe wszystkich danych, konfiguracji i wpisów tajnych systemu.

## <a name="91-ensure-regular-automated-back-ups"></a>9,1: Zapewnij regularne zautomatyzowane przywracanie awaryjne

| Identyfikator platformy Azure | Identyfikatory WNP | Odpowiedzialność |
|--|--|--|
| 9,1 | 10.1 | Klient |

Włącz Azure Backup i skonfiguruj Źródło kopii zapasowej (maszyny wirtualne platformy Azure, SQL Server lub udziały plików), a także żądaną częstotliwość i okres przechowywania.

Jak włączyć Azure Backup: https://docs.microsoft.com/azure/backup/

## <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9,2: wykonaj kompletne kopie zapasowe systemu i Utwórz kopię zapasową wszystkich kluczy zarządzanych przez klienta

| Identyfikator platformy Azure | Identyfikatory WNP | Odpowiedzialność |
|--|--|--|
| 9.2 | 10.2 | Klient |

Włącz Azure Backup i docelowe maszyny wirtualne, a także żądaną częstotliwość i okres przechowywania. Utwórz kopię zapasową kluczy zarządzanych przez klienta w Azure Key Vault.

Jak włączyć Azure Backup: https://docs.microsoft.com/azure/backup/

Jak utworzyć kopię zapasową kluczy magazynu kluczy na platformie Azure: https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey?view=azurermps-6.13.0

## <a name="93-validate-all-backups-including-customer-managed-keys"></a>9,3: Weryfikuj wszystkie kopie zapasowe, w tym klucze zarządzane przez klienta

| Identyfikator platformy Azure | Identyfikatory WNP | Odpowiedzialność |
|--|--|--|
| 9,3 | 10,3 | Klient |

Pamiętaj, aby okresowo wykonywać przywracanie danych w Azure Backup. W razie potrzeby przetestuj przywracanie do izolowanej sieci VLAN. Testowanie przywracania kopii zapasowych kluczy zarządzanych przez klienta.

Jak odzyskać pliki z kopii zapasowej maszyny wirtualnej platformy Azure:

https://docs.microsoft.com/azure/backup/backup-azure-restore-files-from-vm

Jak przywrócić klucze magazynu kluczy na platformie Azure:

https://docs.microsoft.com/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey?view=azurermps-6.13.0

## <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9,4: zapewnianie ochrony kopii zapasowych i kluczy zarządzanych przez klienta

| Identyfikator platformy Azure | Identyfikatory WNP | Odpowiedzialność |
|--|--|--|
| 9.4 | 10,4 | Klient |

W przypadku kopii zapasowych w środowisku lokalnym szyfrowanie danych w spoczynku odbywa się przy użyciu hasła podanego podczas tworzenia kopii zapasowej na platformie Azure. W przypadku maszyn wirtualnych platformy Azure dane są szyfrowane w spoczynku przy użyciu szyfrowania usługi Storage (SSE). Możesz włączyć funkcję usuwania nietrwałego w Key Vault, aby chronić klucze przed przypadkowym lub złośliwym usunięciem.

Jak włączyć usuwanie nietrwałe w Key Vault:

https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete?tabs=azure-portal

## <a name="next-steps"></a>Następne kroki

Zobacz następną kontrolę zabezpieczeń: [odpowiedź na zdarzenie](security-control-incident-response.md)
