---
title: Dane Rest szyfrowanie w usłudze Azure Stack
description: Dowiedz się, jak usługa Azure Stack chroni dane za pomocą szyfrowanie w spoczynku
services: azure-stack
author: PatAltimore
manager: femila
ms.service: azure-stack
ms.topic: article
ms.date: 03/11/2019
ms.author: patricka
ms.reviewer: fiseraci
ms.lastreviewed: 03/11/2019
keywords: ''
ms.openlocfilehash: 018b8f6cf4fc5d3cd380535fca71a038b7fd4208
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/12/2019
ms.locfileid: "57769391"
---
# <a name="data-at-rest-encryption-in-azure-stack"></a>Dane rest szyfrowanie w usłudze Azure Stack

Usługi Azure Stack zapewnia ochronę infrastruktury danych na poziomie podsystemu magazynowania, dzięki funkcji szyfrowania nieużywanych i użytkowników. Usługi Azure Stack podsystemu magazynowania są szyfrowane przy użyciu funkcji BitLocker przy użyciu szyfrowania AES 128-bitowego. Klucze funkcji BitLocker są zachowywane w wewnętrznej magazynu wpisów tajnych.

Dane szyfrowanie rest są typowym wymogiem dla wielu standardów zgodności główne (na przykład PCI-DSS, FedRAMP, HIPAA). Usługa Azure Stack umożliwia spełniają te wymagania bez dodatkowej pracy i konfiguracji wymaganej. Aby uzyskać więcej informacji na temat jak Azure Stack ułatwia spełniają standardy zgodności, zobacz [Portal zaufania usługi Microsoft](https://aka.ms/AzureStackCompliance).

> [!NOTE]
> Dane szyfrowanie rest chroni dane przed uzyskiwany przez osobę, która fizycznie udałoby jeden lub więcej dysków twardych. Dane szyfrowanie rest nie chroni przed przechwycenie ogólnie rzecz biorąc, za pośrednictwem sieci (dane podczas przesyłania), dane są obecnie używane (w pamięci) lub więcej danych dane są exfiltrated, gdy system jest włączony i uruchomiony.

## <a name="retrieving-bitlocker-recovery-keys"></a>Trwa pobieranie kluczy odzyskiwania funkcji BitLocker

Odbywa się wewnętrznie Azure klucze funkcji BitLocker stosu dla danych magazynowanych. Nie należy zapewnić im o regularnych operacjach lub podczas uruchamiania systemu. Obsługiwane scenariusze mogą jednak wymagać klucze odzyskiwania funkcji BitLocker, aby wyświetlić systemu w trybie online.  

> [!WARNING]
> Pobierz klucze odzyskiwania funkcji BitLocker i przechowywać je w bezpiecznym miejscu poza usługi Azure Stack. Nie ma kluczy odzyskiwania w niektórych scenariuszach dotyczących pomocy technicznej może spowodować utratę danych i wymaga Przywracanie systemu z kopii zapasowej obrazu.

Trwa pobieranie kluczy odzyskiwania funkcji BitLocker wymaga dostępu do [uprzywilejowanych punktu końcowego](azure-stack-privileged-endpoint.md) (program ten). Z poziomu sesji program ten należy uruchomić polecenie cmdlet Get-AzsRecoveryKeys.

```powershell
##This cmdlet retrieves the recovery keys for all the volumes that are encrypted with BitLocker.
Get-AzsRecoveryKeys
```

Parametry opcjonalne dla *Get AzsRecoveryKeys* polecenia cmdlet:

| Parametr | Opis | Type | Wymagane |
|---------|---------|---------|---------|
|*nieprzetworzone* | Zwraca nieprzetworzone dane mapowania między klucza odzyskiwania, nazwę komputera i identyfikatory hasła wszystkich woluminów zaszyfrowanych  | — przełącznik | nie (zaprojektowane dla scenariuszy pomocy technicznej)|


## <a name="troubleshoot-issues"></a>Rozwiązywanie problemów

W skrajnych przypadkach odblokowywanie funkcją BitLocker przez żądanie może zakończyć się niepowodzeniem skutkuje określonego woluminu, aby nie rozruchu. W zależności od dostępności niektóre składniki architektury ten błąd może spowodować przestoje i ryzyko utraty danych, jeśli nie masz klucze odzyskiwania funkcji BitLocker.

> [!WARNING]
> Pobierz klucze odzyskiwania funkcji BitLocker i przechowywać je w bezpiecznym miejscu poza usługi Azure Stack. Nie ma kluczy odzyskiwania w niektórych scenariuszach dotyczących pomocy technicznej może spowodować utratę danych i wymaga Przywracanie systemu z kopii zapasowej obrazu.

Jeśli podejrzewasz, że system ma problemy z funkcją BitLocker, takich jak Azure Stack, nie można uruchomić, skontaktuj się z pomocą techniczną. Pomoc techniczna wymaga klucze odzyskiwania funkcji BitLocker. Problemy można rozwiązać z operacją FRU dla tego określonego/wolumin hosta VM/związane z większości funkcji BitLocker. W pozostałych przypadkach można wykonać procedurę ręcznego odblokowywanie przy użyciu kluczy odzyskiwania funkcji BitLocker. Jeśli klucze odzyskiwania funkcji BitLocker nie są dostępne, jedyną opcją jest można przywrócić z kopii zapasowej obrazu. W zależności od tego, kiedy ostatnia kopia zapasowa została wykonana może wystąpić utrata danych.

## <a name="next-steps"></a>Kolejne kroki

- [Dowiedz się więcej na temat zabezpieczeń usługi Azure Stack](azure-stack-security-foundations.md)
- Aby uzyskać więcej informacji na temat jak funkcja BitLocker zapewnia ochronę woluminów CSV, zobacz [ochrony klastra udostępnionego woluminów i sieci magazynowania za pomocą funkcji BitLocker](https://docs.microsoft.com/windows/security/information-protection/bitlocker/protecting-cluster-shared-volumes-and-storage-area-networks-with-bitlocker).