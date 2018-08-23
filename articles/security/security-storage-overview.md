---
title: Funkcje zabezpieczeń, które mogą być używane z usługi Azure Storage | Dokumentacja firmy Microsoft
description: Ten artykuł zawiera omówienie podstawowych funkcji zabezpieczeń platformy Azure, które mogą być używane z usługi Azure Storage.
services: security
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: TomSh
ms.assetid: 521180dc-2cc9-43f1-ae87-2701de7ca6b8
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/17/2018
ms.author: terrylan
ms.openlocfilehash: 4ed4e73348db8cfffb6e79afaa9d196e242d7488
ms.sourcegitcommit: 30c7f9994cf6fcdfb580616ea8d6d251364c0cd1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/18/2018
ms.locfileid: "42057077"
---
# <a name="azure-storage-security-overview"></a>Omówienie zabezpieczeń usługi Azure Storage

Usługa Azure Storage to rozwiązanie magazynu w chmurze dla nowoczesnych aplikacji, które polegają na trwałości, dostępności i skalowalności, aby spełniać potrzeby klientów. Usługa Azure Storage udostępnia rozbudowany zestaw funkcji zabezpieczeń. Możesz:

* Zabezpieczanie konta magazynu przy użyciu kontroli dostępu opartej na rolach (RBAC) i Azure Active Directory.
* Zabezpiecz dane przesyłane między aplikacją i platformy Azure za pomocą szyfrowania po stronie klienta, HTTPS lub SMB 3.0.
* Ustaw automatycznie szyfrowania są zapisywane do usługi Azure Storage przy użyciu szyfrowania usługi magazynu danych.
* Zestaw dysków systemu operacyjnego i danych używanych przez maszyny wirtualne (VM) do zaszyfrowania, za pomocą usługi Azure Disk Encryption.
* Przyznawanie delegowanego dostępu do obiektów danych w usłudze Azure Storage przy użyciu sygnatury dostępu współdzielonego (SASs).
* Korzystanie z analizy, aby śledzić metodę uwierzytelniania, która korzysta z aktualnie podczas uzyskiwania dostępu do magazynu.

Aby uzyskać bardziej szczegółowy widok zabezpieczeń w usłudze Azure Storage, zobacz [Przewodnik po zabezpieczeniach usługi Azure Storage](../storage/common/storage-security-guide.md). Ten przewodnik zawiera szczegółowe informacje na funkcje zabezpieczeń usługi Azure Storage. Funkcje te obejmują klucze konta magazynu, szyfrowanie danych podczas przesyłania i nieużywanych danych i usługa storage analytics.

Ten artykuł zawiera omówienie funkcji zabezpieczeń platformy Azure, korzystających z usługi Azure Storage. Linki do artykułów podać szczegóły każdej funkcji, dzięki czemu możesz dowiedzieć się więcej.

## <a name="role-based-access-control"></a>Kontrola dostępu oparta na rolach

Możesz pomóc zabezpieczyć swoje konto magazynu przy użyciu kontroli dostępu opartej na rolach. Ograniczanie dostępu na podstawie [trzeba znać](https://en.wikipedia.org/wiki/Need_to_know) i [najniższych uprawnień](https://en.wikipedia.org/wiki/Principle_of_least_privilege) jest zasad zabezpieczeń dla organizacji, które chcesz wymuszać zasady zabezpieczeń dostępu do danych. Te prawa dostępu są przyznawane, przypisując odpowiednie role RBAC do grup i aplikacji w określonym zakresie. Możesz użyć [wbudowane role kontroli RBAC](../role-based-access-control/built-in-roles.md), np. Współautor konta magazynu, aby przypisać uprawnienia do użytkowników.

Więcej informacji:

* [Kontrola dostępu oparta na rolach usługi Azure Active Directory](../role-based-access-control/role-assignments-portal.md)

## <a name="delegated-access-to-storage-objects"></a>Delegowanego dostępu do obiektów magazynu

Sygnatury dostępu współdzielonego zapewnia delegowany dostęp do zasobów na koncie magazynu. Sygnatura dostępu Współdzielonego oznacza, że możliwe jest przyznanie klientowi ograniczonych uprawnień do obiektów na koncie magazynu określonym przedziale czasu i z określonym zestawem uprawnień. Te ograniczone uprawnienia można przyznać bez konieczności udostępniania kluczy dostępu do Twojego konta. 

Sygnatura dostępu Współdzielonego to identyfikator URI, który obejmuje jego parametry zapytań, wszystkie informacje niezbędne do uwierzytelnionego dostępu do zasobu magazynu. Aby uzyskać dostęp do zasobów magazynu przy użyciu sygnatury dostępu Współdzielonego, klient musi tylko zapewniają sygnatury dostępu Współdzielonego do odpowiedniego konstruktora lub metody.

Więcej informacji:

* [Opis modelu sygnatur dostępu Współdzielonego](../storage/common/storage-dotnet-shared-access-signature-part-1.md)
* [Tworzenie i używanie sygnatury dostępu Współdzielonego z usługą Blob storage](../storage/blobs/storage-dotnet-shared-access-signature-part-2.md)

## <a name="encryption-in-transit"></a>Szyfrowanie podczas transferu

Szyfrowanie podczas transferu jest mechanizm ochrony danych podczas ich przesyłania w sieciach. Za pomocą usługi Azure Storage można zabezpieczyć dane przy użyciu:

* [Szyfrowanie na poziomie transportu](../storage/common/storage-security-guide.md#encryption-in-transit), taki jak HTTPS, gdy transfer danych do i z usługi Azure Storage.
* [Połączenie szyfrowania](../storage/common/storage-security-guide.md#using-encryption-during-transit-with-azure-file-shares), takie jak szyfrowanie protokołu SMB 3.0 na platformie Azure udziałów plików.
* [Szyfrowanie po stronie klienta](../storage/common/storage-security-guide.md#using-client-side-encryption-to-secure-data-that-you-send-to-storage), aby szyfrować dane, zanim zostanie przekazany do magazynu, jak i do odszyfrowania danych, gdy zostanie przeniesiona poza magazynu.

Dowiedz się więcej o funkcji Szyfrowanie po stronie klienta:

* [Szyfrowanie po stronie klienta dla usługi Microsoft Azure Storage](https://blogs.msdn.microsoft.com/windowsazurestorage/2015/04/28/client-side-encryption-for-microsoft-azure-storage-preview/)
* [Usługa cloud security kontroluje serii: szyfrowanie danych podczas przesyłania](http://blogs.microsoft.com/cybertrust/2015/08/10/cloud-security-controls-series-encrypting-data-in-transit/)

## <a name="encryption-at-rest"></a>Szyfrowanie w spoczynku

W przypadku wielu organizacji [szyfrowanie danych magazynowanych](https://blogs.microsoft.com/cybertrust/2015/09/10/cloud-security-controls-series-encrypting-data-at-rest/) stanowi krok obowiązkowy kierunku niezależność danych ochrony prywatności, zgodności i danych. Trzy funkcje platformy Azure zapewniają szyfrowanie danych magazynowanych:

* [Szyfrowanie usługi Storage](../storage/common/storage-security-guide.md#encryption-at-rest) pozwala na żądanie, czy usługi storage automatyczne szyfrowanie danych podczas zapisywania jej do usługi Azure Storage.
* [Szyfrowanie po stronie klienta](../storage/common/storage-security-guide.md#client-side-encryption) udostępnia również funkcję szyfrowanie danych magazynowanych.
* [Usługa Azure Disk Encryption](../storage/common/storage-security-guide.md#using-azure-disk-encryption-to-encrypt-disks-used-by-your-virtual-machines) umożliwia szyfrowanie dysków systemu operacyjnego i dysków z danymi używanymi w maszynie wirtualnej IaaS.

Dowiedz się więcej o funkcji szyfrowanie usługi Storage:

* [Szyfrowanie usługi Azure Storage](https://azure.microsoft.com/services/storage/) jest dostępna dla [usługi Azure Blob storage](https://azure.microsoft.com/services/storage/blobs/). Aby uzyskać szczegółowe informacje dotyczące innych typów magazynu platformy Azure, zobacz [usługi Azure Files](https://azure.microsoft.com/services/storage/files/), [dysku (Premium Storage)](https://azure.microsoft.com/services/storage/premium-storage/), [Table storage](https://azure.microsoft.com/services/storage/tables/), i [Queue storage](https://azure.microsoft.com/services/storage/queues/).
* [Szyfrowanie usługi Azure Storage dla danych magazynowanych](../storage/common/storage-service-encryption.md)

## <a name="azure-disk-encryption"></a>Usługa Azure Disk Encryption

Usługa Azure Disk Encryption dla maszyn wirtualnych ułatwia zaspakajanie organizacyjnych bezpieczeństwa i zgodności z wymaganiami. Szyfrowanie dysków maszyn wirtualnych (w tym dysków rozruchowych i danych) przy użyciu kluczy i zasad objętych kontrolą w [usługi Azure Key Vault](https://azure.microsoft.com/services/key-vault/).

Szyfrowanie dysków dla maszyn wirtualnych działa w przypadku systemów operacyjnych Linux i Windows. Używa również usługi Key Vault, aby pomóc w zabezpieczaniu, zarządzanie i przeprowadzanie inspekcji użycia kluczy szyfrowania dysku. Wszystkie dane w dysków maszyn wirtualnych są szyfrowane w stanie spoczynku przy użyciu standardowych w branży technologii na kontach usługi Azure storage. Rozwiązanie Disk Encryption for Windows opiera się na [szyfrowania dysków Microsoft BitLocker](https://technet.microsoft.com/library/cc732774.aspx), a rozwiązanie dla systemu Linux opiera się na [dm-crypt](https://en.wikipedia.org/wiki/Dm-crypt).

Dowiedz się więcej

* [Usługa Azure Disk Encryption dla maszyn wirtualnych IaaS z systemem Linux i Windows](https://gallery.technet.microsoft.com/Azure-Disk-Encryption-for-a0018eb0)

## <a name="azure-storage-firewalls-and-virtual-networks"></a>Sieci platformy Azure z zapór usługi Storage i Virtual

Usługa Azure storage umożliwia włączenie reguły zapory dla konta magazynu. Raz włączone one zablokuje żądań przychodzących dla danych, w tym żądania od innych usług platformy Azure. Możesz skonfigurować wyjątki, aby zezwolić na ruch. Reguły zapory może być włączona na istniejące konta magazynu lub podczas tworzenia.

Do zabezpieczania konta magazynu do określonego zestawu dozwolonych sieci, należy użyć tej funkcji.

Aby uzyskać więcej informacji na temat usługi Azure storage zapory i sieci wirtualne zapoznaj się z artykułem [Konfigurowanie usługi Azure Storage zapory i sieci wirtualne](../storage/common/storage-network-security.md)

## <a name="azure-key-vault"></a>W usłudze Azure Key Vault

Usługa Azure Disk Encryption korzysta [usługi Azure Key Vault](https://azure.microsoft.com/services/key-vault/) ułatwiają kontrolowanie i zarządzaniu wpisami tajnymi w ramach subskrypcji usługi key vault i kluczami szyfrowania dysku. Gwarantuje również, że wszystkie dane na dyskach maszyn wirtualnych są szyfrowane, gdy w usłudze Azure Storage. Należy używać usługi Key Vault do inspekcji kluczy i zasad użycia.

Dowiedz się więcej

* [Co to jest usługa Azure Key Vault?](../key-vault/key-vault-whatis.md)
* [Rozpoczynanie pracy z usługą Azure Key Vault](../key-vault/key-vault-get-started.md)
