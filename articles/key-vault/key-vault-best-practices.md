---
title: Najlepsze rozwiązania dotyczące używania usługi Key Vault — usługi Azure Key Vault | Dokumentacja firmy Microsoft
description: W tym dokumencie opisano niektóre z najlepszych rozwiązań za pomocą usługi Key Vault
services: key-vault
documentationcenter: ''
author: yvprashanth
manager: barbkess
tags: azure-key-vault
ms.assetid: e5b4e083-4a39-4410-8e3a-2832ad6db405
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 03/07/2019
ms.author: prashanthyv
ms.openlocfilehash: 85bd1858bc3f2d505e1e4d0a88e8c77a46ae4447
ms.sourcegitcommit: b8f9200112cae265155b8877f7e1621c4bcc53fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/14/2019
ms.locfileid: "57882037"
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

## <a name="backup-your-vault"></a>Magazyn usługi Backup

Upewnij się, zapoznasz się z regularne tworzenie kopii zapasowych Twojej [magazynu](https://blogs.technet.microsoft.com/kv/2018/07/20/announcing-backup-and-restore-of-keys-secrets-and-certificates/) na update/delete/tworzenia obiektów w magazynie.

## <a name="turn-on-logging"></a>Włącz rejestrowanie

[Włącz rejestrowanie](key-vault-logging.md) dla magazynu. Też skonfigurować alerty.

## <a name="turn-on-recovery-options"></a>Włącz opcje odzyskiwania

1. Włącz [usuwania nietrwałego](key-vault-ovw-soft-delete.md).
2. Jeśli chcesz zabezpieczyć się przed Wymuś usunięcie wpisu tajnego / magazynu nawet po zakończeniu usuwania nietrwałego jest włączona, należy włączyć funkcję Ochrona przed czyszczeniem.
