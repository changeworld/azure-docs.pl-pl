---
title: Najlepsze rozwiązania dotyczące używania usługi Key Vault — usługi Azure Key Vault | Dokumentacja firmy Microsoft
description: W tym dokumencie opisano niektóre z najlepszych rozwiązań za pomocą usługi Key Vault
services: key-vault
author: msmbaldwin
manager: barbkess
tags: azure-key-vault
ms.service: key-vault
ms.topic: conceptual
ms.date: 03/07/2019
ms.author: mbaldwin
ms.openlocfilehash: eb7150d0b1c3a4a312b0c05ba7612960aaf640f6
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "65227936"
---
# <a name="best-practices-to-use-key-vault"></a>Najlepsze rozwiązania za pomocą usługi Key Vault

## <a name="control-access-to-your-vault"></a>Kontrola dostępu do magazynu

Usługa Azure Key Vault to usługa w chmurze, która zabezpiecza klucze szyfrowania i wpisy tajne, takie jak certyfikaty, parametry połączenia i hasła. Ponieważ te dane są poufne i krytyczne dla działania, należy zabezpieczyć dostęp do magazynów kluczy, zezwalając tylko autoryzowane aplikacje i użytkownicy. To [artykułu](key-vault-secure-your-key-vault.md) zawiera omówienie modelu dostępu usługi Key Vault. Wyjaśnia uwierzytelnianie i autoryzację, a w tym artykule opisano sposób zabezpieczania dostępu do magazynów kluczy.

Sugestie podczas kontroli dostępu do magazynu są następujące:
1. Zablokowanie dostępu do subskrypcji, grupy zasobów i magazyny klucz (RBAC)
2. Tworzenie zasad dostępu dla każdego magazynu
3. Użyj najniższych uprawnień dostępu do jednostki, aby udzielić dostępu
4. Włącz zaporę i [punkty końcowe usługi sieci Wirtualnej](key-vault-overview-vnet-service-endpoints.md)

## <a name="use-separate-key-vault"></a>Używanie oddzielnych usługi Key Vault

Nasze zaleca się użycie magazynu dla aplikacji dla środowiska (rozwój, przedprodukcyjnych i produkcyjnych). To pomoże Ci udostępniaj wpisów tajnych w środowiskach i zmniejsza zagrożenie w razie naruszenia zabezpieczeń.

## <a name="backup"></a>Backup

Upewnij się, zapoznasz się z regularne tworzenie kopii zapasowych Twojej [magazynu](https://blogs.technet.microsoft.com/kv/2018/07/20/announcing-backup-and-restore-of-keys-secrets-and-certificates/) na update/delete/tworzenia obiektów w magazynie.

## <a name="turn-on-logging"></a>Włącz rejestrowanie

[Włącz rejestrowanie](key-vault-logging.md) dla magazynu. Też skonfigurować alerty.

## <a name="turn-on-recovery-options"></a>Włącz opcje odzyskiwania

1. Włącz [usuwania nietrwałego](key-vault-ovw-soft-delete.md).
2. Jeśli chcesz zabezpieczyć się przed Wymuś usunięcie wpisu tajnego / magazynu nawet po zakończeniu usuwania nietrwałego jest włączona, należy włączyć funkcję Ochrona przed czyszczeniem.
