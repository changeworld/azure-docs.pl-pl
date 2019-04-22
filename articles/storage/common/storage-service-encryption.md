---
title: Szyfrowanie usługi Azure Storage dla danych magazynowanych | Dokumentacja firmy Microsoft
description: Szyfrowanie usługi Azure Managed Disks, usługi Azure Blob storage, Azure Files, Azure Queue storage i Azure Table storage po stronie usługi, w przypadku przechowywania danych za pomocą funkcji szyfrowania usługi Storage platformy Azure i je odszyfrować, podczas pobierania danych.
services: storage
author: lakasa
ms.service: storage
ms.topic: article
ms.date: 08/01/2018
ms.author: lakasa
ms.subservice: common
ms.openlocfilehash: 059091315c378ab6e2bb857e580c02df968b5092
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/31/2019
ms.locfileid: "55457177"
---
# <a name="azure-storage-service-encryption-for-data-at-rest"></a>Szyfrowanie usługi Azure Storage dla danych magazynowanych
Szyfrowanie usługi Azure Storage dla danych w spoczynku pomaga chronić dane zgodnie z wymaganiami co do bezpieczeństwa organizacji i zobowiązaniami w zakresie zgodności. Dzięki tej funkcji platformy Azure storage automatycznie szyfruje dane przed utrwaleniem go do usługi Azure Managed Disks, magazynu obiektów Blob platformy Azure, kolejką lub tabelą lub usługi Azure Files i odszyfrowuje je przed pobierania. Obsługa szyfrowania, szyfrowanie nieużywanych danych, odszyfrowywania i zarządzania kluczami w programie Storage Service Encryption jest niewidoczne dla użytkowników. Wszystkie dane zapisane na platformę Azure storage są szyfrowane za pomocą 256-bitowego [szyfrowania AES](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard), jeden blok najsilniejszych szyfrów.

Szyfrowanie usługi Storage jest włączona dla wszystkich kont magazynu z nowymi i istniejącymi i nie może być wyłączony. Ponieważ Twoje dane są zabezpieczane domyślnie, nie trzeba modyfikować kodu lub aplikacji, aby móc korzystać z szyfrowania usługi Storage.

Ta funkcja automatycznie szyfruje dane:

- Usługi magazynu platformy Azure:
    - Dyski zarządzane platformy Azure
    - Azure Blob Storage
    - Azure Files
    - Usługa Azure Queue storage
    - Usługa Azure Table storage.  
- Obu warstwach wydajności (standardowa i Premium).
- Oba modele wdrażania (usługi Azure Resource Manager i model klasyczny).

Szyfrowanie usługi Storage nie wpływa na wydajność usług Azure storage.

Można użyć kluczy szyfrowania zarządzanych przez firmę Microsoft za pomocą szyfrowania usługi Storage lub użyć własnych kluczy szyfrowania. Aby uzyskać więcej informacji na temat korzystania z własnych kluczy, zobacz [szyfrowanie usługi Storage przy użyciu kluczy zarządzanych przez klienta w usłudze Azure Key Vault](storage-service-encryption-customer-managed-keys.md).

## <a name="view-encryption-settings-in-the-azure-portal"></a>Wyświetl ustawienia szyfrowania w witrynie Azure portal
Aby wyświetlić ustawienia szyfrowania usługi magazynu, zaloguj się do [witryny Azure portal](https://portal.azure.com) i wybierz konto magazynu. W **ustawienia** okienku wybierz **szyfrowania** ustawienie.

![Portal zrzut ekranu przedstawiający ustawienie szyfrowania](./media/storage-service-encryption/image1.png)

## <a name="faq-for-storage-service-encryption"></a>Często zadawane pytania dotyczące szyfrowania usługi Storage
**Jak zaszyfrować dane na koncie magazynu usługi Resource Manager?**  
Szyfrowanie usługi Storage jest włączona dla wszystkich kont magazynu — klasyczne i usługi Resource Manager, wszelkie istniejące pliki na konto magazynu utworzone przed włączeniem szyfrowania zostaną wstecznie zaszyfrowane przez proces szyfrowania w tle.

**Szyfrowanie usługi Storage domyślnie działa podczas tworzenia konta magazynu?**  
Tak, szyfrowanie usługi Storage jest włączona dla wszystkich kont magazynu i wszystkich usług Azure storage.

**Mam konto magazynu usługi Resource Manager. Szyfrowanie usługi Storage można włączyć na nim?**  
Szyfrowanie usługi Storage jest domyślnie włączona na wszystkich istniejących kont magazynu usługi Resource Manager. Jest to obsługiwane dla usługi Azure Blob storage, Azure Files Azure Queue storage, Table storage. 

**Na koncie magazynu można wyłączyć szyfrowania?**  
Szyfrowanie jest włączone domyślnie i nie można wyłączyć szyfrowania dla konta magazynu. 

**O ile bardziej kosztuje się usługa Azure Storage, jeśli włączono szyfrowanie usługi Storage?**  
Nie ma żadnych dodatkowych kosztów.

**Można użyć własnych kluczy szyfrowania?**  
Tak, dla usługi Azure Blob storage i Azure Files można użyć własnych kluczy szyfrowania. Klucze zarządzane przez klienta nie są obecnie obsługiwane przez usługę Azure Managed Disks. Aby uzyskać więcej informacji, zobacz [szyfrowanie usługi Storage przy użyciu kluczy zarządzanych przez klienta w usłudze Azure Key Vault](storage-service-encryption-customer-managed-keys.md).

**Czy można odwołać dostęp do kluczy szyfrowania?**  
Tak, jeśli użytkownik [korzystania z własnych kluczy szyfrowania](storage-service-encryption-customer-managed-keys.md) w usłudze Azure Key Vault.

**Czym różni się szyfrowanie usługi Storage z usługi Azure Disk Encryption?**  
Usługa Azure Disk Encryption zapewnia integrację rozwiązań opartych na systemu operacyjnego, takich jak funkcja BitLocker i DM-Crypt i usłudze Azure KeyVault. Szyfrowanie usługi Storage umożliwia szyfrowanie natywnie w warstwie platformy usługi Azure storage, poniżej maszyny wirtualnej.

**Mam klasycznego konta magazynu. Szyfrowanie usługi Storage można włączyć na nim?**  
Szyfrowanie usługi Storage jest włączona dla wszystkich kont magazynu (klasyczne i usługi Resource Manager).

**Jak zaszyfrować dane na moim koncie magazynu klasycznego**  
Z szyfrowaniem, domyślnie włączone wszystkie dane przechowywane w usługach Azure storage są automatycznie szyfrowane. 

**Przy użyciu szyfrowania usługi Storage, włączana przy użyciu programu Azure PowerShell i wiersza polecenia platformy Azure można utworzyć konta magazynu?**  
Szyfrowanie usługi Storage jest domyślnie włączona w czasie tworzenia wszystkich kont magazynu (klasyczne lub usługi Resource Manager). Aby sprawdzić właściwości konta, przy użyciu programu Azure PowerShell i wiersza polecenia platformy Azure.

**Skonfigurowano konta magazynu do replikacji geograficznej nadmiarowości. Przy użyciu szyfrowania usługi Storage będą nadmiarowych kopii również szyfrowane?**  
Tak, szyfrowane są wszystkie kopie na koncie magazynu. Nadmiarowość wszystkie opcje są obsługiwane — magazyn lokalnie nadmiarowy, Magazyn strefowo nadmiarowy, Magazyn geograficznie nadmiarowy i Magazyn geograficznie nadmiarowy z dostępem do odczytu.

**Szyfrowanie usługi Storage jest dozwolone tylko w określonych regionach?**  
Szyfrowanie usługi Storage jest dostępna we wszystkich regionach.

**Magazyn usługi szyfrowania FIPS 140-2 jest zgodne?**  
Tak, szyfrowanie usługi Storage jest FIPS 140-2 zgodny. Aby uzyskać więcej informacji na temat modułów kryptograficznych służących do bazowego szyfrowanie usługi Storage, zobacz [Cryptography API: Następna generacja](https://docs.microsoft.com/windows/desktop/seccng/cng-portal).

**Jak mam się kontaktować ktoś czy jakichkolwiek problemów lub chcesz wyrazić swoją opinię?**  
Skontaktuj się z pomocą [ ssediscussions@microsoft.com ](mailto:ssediscussions@microsoft.com) dla jakiekolwiek problemy lub opinię szyfrowanie usługi Storage.

## <a name="next-steps"></a>Kolejne kroki
Usługa Azure Storage zapewnia kompleksowy zestaw funkcji zabezpieczeń tego razem zapewniają deweloperom tworzenie bezpiecznych aplikacji. Aby uzyskać więcej informacji, zobacz [Przewodnik po zabezpieczeniach magazynu](../storage-security-guide.md).
