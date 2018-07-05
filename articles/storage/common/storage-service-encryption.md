---
title: Szyfrowanie usługi Azure Storage dla danych magazynowanych | Dokumentacja firmy Microsoft
description: Szyfrowanie usługi Azure Blob storage po stronie usługi, w przypadku przechowywania danych za pomocą funkcji szyfrowania usługi Storage platformy Azure i je odszyfrować, podczas pobierania danych.
services: storage
author: lakasa
manager: jeconnoc
ms.service: storage
ms.topic: article
ms.date: 06/12/2018
ms.author: lakasa
ms.openlocfilehash: d469dfb5092f1269a6600ee8ee2f81778fd83b96
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/03/2018
ms.locfileid: "37450090"
---
# <a name="azure-storage-service-encryption-for-data-at-rest"></a>Szyfrowanie usługi Azure Storage dla danych magazynowanych

Szyfrowanie usługi Azure Storage dla danych w spoczynku pomaga chronić dane zgodnie z wymaganiami co do bezpieczeństwa organizacji i zobowiązaniami w zakresie zgodności. Dzięki tej funkcji usługi Azure Storage automatycznie szyfruje dane przed utrwaleniem go do usługi Azure Storage i odszyfrowuje je przed pobierania. Obsługa szyfrowania, szyfrowanie nieużywanych danych, odszyfrowywania i zarządzania kluczami w programie Storage Service Encryption jest niewidoczne dla użytkowników. Wszystkie dane zapisywane w magazynie platformy Azure są szyfrowane za pomocą 256-bitowego [szyfrowania AES](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard), jeden blok najsilniejszych szyfrów.

Szyfrowanie usługi Storage jest włączona dla wszystkich kont magazynu z nowymi i istniejącymi i nie może być wyłączony. Ponieważ Twoje dane są zabezpieczane domyślnie, nie trzeba modyfikować kodu lub aplikacji, aby móc korzystać z szyfrowania usługi Storage.

Ta funkcja automatycznie szyfruje dane:

- Obu warstwach wydajności (standardowa i Premium).
- Oba modele wdrażania (usługi Azure Resource Manager i model klasyczny).
- Wszystkie usługi Azure Storage services (usługi Blob storage, Queue storage, Table storage i Azure Files). 

Szyfrowanie usługi Storage nie wpływa na wydajność usługi Azure Storage.

Za pomocą kluczy zarządzanych przez firmę Microsoft szyfrowania szyfrowanie usługi Storage lub użyć własnych kluczy szyfrowania. Aby uzyskać więcej informacji na temat korzystania z własnych kluczy, zobacz [szyfrowanie usługi Storage przy użyciu kluczy zarządzanych przez klienta w usłudze Azure Key Vault](storage-service-encryption-customer-managed-keys.md).

## <a name="view-encryption-settings-in-the-azure-portal"></a>Wyświetl ustawienia szyfrowania w witrynie Azure portal

Aby wyświetlić ustawienia szyfrowania usługi magazynu, zaloguj się do [witryny Azure portal](https://portal.azure.com) i wybierz konto magazynu. W **ustawienia** okienku wybierz **szyfrowania** ustawienie.

![Portal zrzut ekranu przedstawiający ustawienie szyfrowania](./media/storage-service-encryption/image1.png)

## <a name="faq-for-storage-service-encryption"></a>Często zadawane pytania dotyczące szyfrowania usługi Storage

**Pytanie: Mam klasycznego konta magazynu. Szyfrowanie usługi Storage można włączyć na nim?**

Odp.: Storage Service Encryption jest włączona dla wszystkich kont magazynu (klasyczne i usługi Resource Manager).

**P: jak szyfrowania danych na moim koncie magazynu klasycznego**

Odp.: z szyfrowaniem, domyślnie włączone usługi Azure Storage automatycznie szyfruje nowych danych. 

**Pytanie: mam konta magazynu usługi Resource Manager. Szyfrowanie usługi Storage można włączyć na nim?**

Odp.: storage Service Encryption jest domyślnie włączona na wszystkich istniejących kont magazynu usługi Resource Manager. Jest to obsługiwane dla usługi Blob storage, Table storage, Queue storage i Azure Files. 

**P: jak szyfrować dane w ramach konta magazynu usługi Resource Manager?**

Szyfrowanie usługi Storage A: jest włączona dla wszystkich kont magazynu — klasyczne i usługi Resource Manager, wszelkie istniejące pliki na konto magazynu utworzone przed włączeniem szyfrowania zostaną wstecznie zaszyfrowane przez proces szyfrowania w tle.

**P: czy można utworzyć konta magazynu przy użyciu szyfrowania usługi Storage, włączana przy użyciu programu Azure PowerShell i wiersza polecenia platformy Azure?**

Odp.: Storage Service Encryption jest domyślnie włączona w czasie tworzenia wszystkich kont magazynu (klasyczne lub usługi Resource Manager). Aby sprawdzić właściwości konta, przy użyciu programu Azure PowerShell i wiersza polecenia platformy Azure.

**P: jak dużo bardziej kosztuje się usługa Azure Storage, jeśli włączono szyfrowanie usługi Storage?**

Odp.: nie ma żadnych dodatkowych kosztów.

**P: czy można użyć własnych kluczy szyfrowania?**

Odp. tak, można użyć własnych kluczy szyfrowania. Aby uzyskać więcej informacji, zobacz [szyfrowanie usługi Storage przy użyciu kluczy zarządzanych przez klienta w usłudze Azure Key Vault](storage-service-encryption-customer-managed-keys.md).

**P: czy można odwołać dostęp do kluczy szyfrowania?**

Odp.: tak, jeśli użytkownik [korzystania z własnych kluczy szyfrowania](storage-service-encryption-customer-managed-keys.md) w usłudze Azure Key Vault.

**P: czy domyślnie włączone szyfrowanie usługi Storage, tworząc konto magazynu?**

Odp. tak, szyfrowanie usługi Storage jest włączona dla wszystkich kont magazynu i wszystkich usług Azure Storage.

**P: jak różni się od usługi Azure Disk Encryption?**

Odp.: Usługa azure Disk Encryption umożliwia szyfrowanie dysków systemu operacyjnego i danych na maszynach wirtualnych IaaS. Aby uzyskać więcej informacji, zobacz [Przewodnik po zabezpieczeniach magazynu](../storage-security-guide.md).

**P: co mogę zrobić, jeśli włączyć usługi Azure Disk Encryption na dyski z danymi?**

Odp.: to będzie działać bezproblemowo. Obie metody będą szyfrowania danych.

**Pytanie: Moja konto magazynu jest skonfigurowane do replikacji geograficznej nadmiarowości. Przy użyciu szyfrowania usługi Storage będą nadmiarowych kopii również szyfrowane?**

Odp. tak, szyfrowane są wszystkie kopie na koncie magazynu. Nadmiarowość wszystkie opcje są obsługiwane — magazyn lokalnie nadmiarowy, Magazyn strefowo nadmiarowy, Magazyn geograficznie nadmiarowy i Magazyn geograficznie nadmiarowy z dostępem do odczytu.

**P: czy można wyłączyć szyfrowania na moim koncie usługi storage?**

Odp.: szyfrowanie jest włączone domyślnie i nie można wyłączyć szyfrowania dla konta magazynu. 

**P: czy szyfrowanie usługi Storage można używać tylko w określonych regionach?**

Odp.: storage Service Encryption jest dostępna we wszystkich regionach dla wszystkich usług.

**P: czy szyfrowanie usługi Storage FIPS 140-2 zgodne?**

Odp. tak, szyfrowanie usługi Storage to FIPS 140-2 zgodny.

**P: jak mam się kontaktować ktoś czy jakichkolwiek problemów lub chcesz wyrazić swoją opinię?**

Odp.: Skontaktuj się z [ ssediscussions@microsoft.com ](mailto:ssediscussions@microsoft.com) dla jakiekolwiek problemy lub opinię szyfrowanie usługi Storage.

## <a name="next-steps"></a>Kolejne kroki
Usługa Azure Storage zapewnia kompleksowy zestaw funkcji zabezpieczeń tego razem zapewniają deweloperom tworzenie bezpiecznych aplikacji. Aby uzyskać więcej informacji, zobacz [Przewodnik po zabezpieczeniach magazynu](../storage-security-guide.md).
