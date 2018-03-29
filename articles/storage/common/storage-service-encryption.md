---
title: Szyfrowanie usługi Magazyn Azure dla danych magazynowanych | Dokumentacja firmy Microsoft
description: Szyfrowanie magazynu obiektów Blob platformy Azure na stronie usługi, gdy dane są przechowywane przy użyciu funkcji szyfrowanie usługi Magazyn Azure, a go odszyfrować podczas pobierania danych.
services: storage
author: lakasa
manager: jeconnoc
ms.service: storage
ms.topic: article
ms.date: 03/14/2018
ms.author: lakasa
ms.openlocfilehash: 1963d011462255d45de327ec17b7fad564d87654
ms.sourcegitcommit: c3d53d8901622f93efcd13a31863161019325216
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2018
---
# <a name="azure-storage-service-encryption-for-data-at-rest"></a>Szyfrowanie usługi Azure Storage dla danych magazynowanych

Azure szyfrowanie usługi Magazyn danych magazynowanych pomaga chronić dane w celu spełnienia organizacji bezpieczeństwa i zgodności zobowiązań. W przypadku tej funkcji usługi Magazyn Azure automatycznie szyfruje dane przed trwałym go do magazynu Azure i odszyfrowuje dane przed pobierania. Obsługa szyfrowania, szyfrowanie w rest, odszyfrowywania i zarządzania kluczami w programie szyfrowanie usługi Magazyn jest niewidoczny dla użytkowników. Wszystkie dane zapisane w magazynie Azure są szyfrowane za pomocą 256-bitowego [szyfrowania AES](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard), jednego bloku najwyższy szyfrów dostępne.

Nie można wyłączyć szyfrowanie usługi Magazyn, jest włączona dla wszystkich kont magazynu nowych i istniejących. Ponieważ domyślnie jest zabezpieczone dane, nie należy modyfikować z kodu lub aplikacji, aby móc korzystać z magazynu usługi szyfrowania.

Automatyczne szyfrowanie danych w przez funkcję:

- Obu warstwach wydajności (Standard i Premium).
- Oba modele wdrażania (usługi Azure Resource Manager i model klasyczny).
- Wszystkie usługi Azure Storage services (magazynu obiektów Blob, magazyn kolejek, Magazyn tabel i plików Azure). 

Szyfrowanie usługi magazynu nie ma wpływu na wydajność magazynu Azure.

Klucze szyfrowania zarządzany przez firmę Microsoft można używać z szyfrowanie usługi Magazyn lub może używać kluczy szyfrowania. Aby uzyskać więcej informacji o korzystaniu z własnych kluczy, zobacz [szyfrowanie usługi Magazyn przy użyciu kluczy zarządzany przez klienta w usłudze Azure Key Vault](storage-service-encryption-customer-managed-keys.md).

## <a name="view-encryption-settings-in-the-azure-portal"></a>Wyświetl ustawienia szyfrowania w portalu Azure

Aby wyświetlić ustawienia szyfrowanie usługi Magazyn, zaloguj się do [portalu Azure](https://portal.azure.com) i wybierz konto magazynu. W **ustawienia** okienku wybierz **szyfrowania** ustawienie.

![Portal zrzut ekranu pokazujący ustawienia szyfrowania](./media/storage-service-encryption/image1.png)

## <a name="faq-for-storage-service-encryption"></a>Często zadawane pytania dotyczące szyfrowanie usługi magazynu

**Pytanie: czy masz konto magazynu klasycznego. Szyfrowanie usługi magazynu można włączyć na nim?**

Szyfrowanie usługi Magazyn A: jest domyślnie włączona dla wszystkich kont magazynu (klasyczne i Resource Manager).

**Pytanie: jak szyfrować dane w moim koncie magazynu classic**

Odp.: z szyfrowaniem, domyślnie włączona magazyn Azure szyfruje automatycznie nowych danych. 

**Pytanie: czy masz konto magazynu Menedżera zasobów. Szyfrowanie usługi magazynu można włączyć na nim?**

A: szyfrowanie usługi Magazyn jest włączona domyślnie na wszystkich istniejących kont magazynu Menedżera zasobów. Ta jest obsługiwana w przypadku magazynu obiektów Blob, Magazyn tabel, magazyn kolejek i plików platformy Azure. 

**Pytanie: jak szyfrowania danych na koncie magazynu Resource Manager?**

Szyfrowanie usługi Magazyn A: jest domyślnie włączona dla wszystkich kont magazynu — klasycznego i Resource Manager wszystkie istniejące pliki w ramach konta magazynu utworzone przed włączeniem szyfrowania będzie wstecznie uzyskać szyfrowane przez proces szyfrowania w tle.

**Pytanie: czy można utworzyć konta magazynu przy użyciu szyfrowania usługi magazynu przy użyciu programu Azure PowerShell i interfejsu wiersza polecenia Azure?**

Szyfrowanie usługi Magazyn A: jest domyślnie włączona w czasie tworzenia dowolne konto magazynu (klasyczne lub Resource Manager). Właściwości konta można sprawdzić, używając programu Azure PowerShell i interfejsu wiersza polecenia Azure.

**Pytanie: jak bardziej usługi Azure Storage koszt Jeśli włączono szyfrowanie usługi Magazyn?**

Odpowiedź: nie istnieje bez dodatkowych kosztów.

**Pytanie: czy można użyć własnych kluczy szyfrowania?**

Odpowiedź: tak, można użyć własnych kluczy szyfrowania. Aby uzyskać więcej informacji, zobacz [szyfrowanie usługi Magazyn przy użyciu kluczy zarządzany przez klienta w usłudze Azure Key Vault](storage-service-encryption-customer-managed-keys.md).

**Pytanie: czy czy można odwołać dostęp do kluczy szyfrowania?**

A. tak, jeśli użytkownik [używać kluczy szyfrowania](storage-service-encryption-customer-managed-keys.md) w usłudze Azure Key Vault.

**Pytanie: jest domyślnie włączone szyfrowanie usługi magazynu podczas tworzenia konta magazynu?**

Odpowiedź: tak, szyfrowanie usługi Magazyn (przy użyciu kluczy zarządzany przez firmę Microsoft) jest domyślnie włączona dla wszystkich kont magazynu — usługi Azure Resource Manager i model klasyczny. Włączono dla wszystkich usług również--magazynu obiektów Blob, Magazyn tabel, magazyn kolejek i plików platformy Azure.

**Pytanie: jak różni się od szyfrowania dysków Azure?**

A: szyfrowanie dysków azure jest używany do szyfrowania dysków systemu operacyjnego i danych na maszynach wirtualnych IaaS. Aby uzyskać więcej informacji, zobacz [przewodnik zabezpieczeń magazynu](../storage-security-guide.md).

**Pytanie: co zrobić, jeśli włączyć szyfrowania dysków Azure na mój dysk danych?**

A: to będzie działać bezproblemowo. Obie metody zostanie szyfrowania danych.

**Pytanie: moim koncie magazynu skonfigurowano powinny być replikowane geograficznie nadmiarowości. Przy użyciu szyfrowania usługi magazynu nadmiarowych kopii także będą zaszyfrowane?**

Odpowiedź: tak, wszystkie kopie konta magazynu są szyfrowane. Nadmiarowość wszystkie opcje są obsługiwane — magazyn lokalnie nadmiarowy, Magazyn strefowo nadmiarowy magazynu geograficznie nadmiarowego i magazynu geograficznie nadmiarowego dostęp do odczytu.

**Pytanie: czy można wyłączyć szyfrowania na moim koncie magazynu?**

Odpowiedź: szyfrowanie jest włączone domyślnie i nie można wyłączyć szyfrowania dla konta magazynu. 

**Pytanie: jest szyfrowanie usługi magazynu jest dozwolony tylko w określonych regionach?**

A: szyfrowanie usługi Magazyn jest dostępna we wszystkich regionach dla wszystkich usług. 

**Pytanie: jak można skontaktować się z osobą czy problemów lub chcesz wyrazić swoją opinię?**

A: kontaktu [ ssediscussions@microsoft.com ](mailto:ssediscussions@microsoft.com) dla jakiekolwiek problemy lub opinii związanych z magazynu usługi szyfrowania.

## <a name="next-steps"></a>Kolejne kroki
Magazyn Azure oferuje ustawienie Zaawansowane funkcje zabezpieczeń tym razem pomocy deweloperzy aplikacji bezpiecznego kompilacji. Aby uzyskać więcej informacji, zobacz [przewodnik zabezpieczeń magazynu](../storage-security-guide.md).
