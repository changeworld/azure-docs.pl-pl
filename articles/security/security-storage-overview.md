---
title: Funkcje zabezpieczeń, które mogą być używane z usługą Azure Storage | Dokumentacja firmy Microsoft
description: " Ten artykuł zawiera omówienie podstawowe funkcje zabezpieczeń platformy Azure, które mogą być używane z usługą Azure Storage. "
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
ms.date: 11/21/2017
ms.author: terrylan
ms.openlocfilehash: 8160ede71930bf4c15969044deb3fced855f03e6
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2018
---
# <a name="azure-storage-security-overview"></a>Omówienie zabezpieczeń usługi Azure storage
Usługa Azure Storage to rozwiązanie magazynu w chmurze dla nowoczesnych aplikacji, które polegają na trwałości, dostępności i skalowalności, aby spełniać potrzeby klientów. Magazyn Azure oferuje rozbudowany zestaw funkcji zabezpieczeń:

* Konto magazynu mogą być chronione przy użyciu kontroli dostępu opartej na rolach i Azure Active Directory.
* Dane mogą być chronione przesyłanych między aplikacją a Azure za pomocą szyfrowania po stronie klienta, HTTPS lub SMB 3.0.
* Dane można ustawić automatycznie były szyfrowane, gdy zapisany w magazynie Azure przy użyciu szyfrowania usługi magazynu.
* Systemu operacyjnego i dysków z danymi używanych przez maszyny wirtualne można ustawić będą szyfrowane przy użyciu szyfrowania dysków Azure.
* Delegowany dostęp do obiektów danych w usłudze Azure Storage można otrzymać za pomocą sygnatury dostępu współdzielonego.
* Metodę uwierzytelniania używaną przez osobę przy uzyskiwaniu dostępu do magazynu mogą być śledzone za pomocą analityka magazynu.

Aby uzyskać bardziej szczegółowy widok zabezpieczeń w usłudze Azure Storage, zobacz [przewodnik zabezpieczeń usługi Azure Storage](../storage/common/storage-security-guide.md). Ten przewodnik zawiera nowości w funkcji zabezpieczeń usługi Azure Storage, takie jak klucze konta magazynu, szyfrowanie danych przesyłanych i rest i analiza magazynu.

Ten artykuł zawiera omówienie funkcji zabezpieczeń platformy Azure, których można użyć z usługą Azure Storage. Łącza są dostarczane do artykułów, które zapewniają szczegółowe informacje dotyczące każdej funkcji, dlatego możesz dowiedzieć się więcej.

Poniżej przedstawiono podstawowe funkcje powinny być zawarte w tym artykule:

* Kontrola dostępu oparta na rolach
* Delegowany dostęp do magazynu obiektów
* Szyfrowanie podczas przesyłania
* Szyfrowanie Szyfrowanie usługi rest i magazynowania
* Usługa Azure Disk Encryption
* W usłudze Azure Key Vault

## <a name="role-based-access-control-rbac"></a>Kontrola dostępu oparta na rolach (RBAC)
Można zabezpieczyć konto magazynu z kontroli dostępu opartej na rolach (RBAC). Ograniczanie dostępu na podstawie [, trzeba znać](https://en.wikipedia.org/wiki/Need_to_know) i [najniższych uprawnień](https://en.wikipedia.org/wiki/Principle_of_least_privilege) jest zasad zabezpieczeń dla organizacji, które mają zostać wymuszone zasady zabezpieczeń dla dostępu do danych. Te prawa dostępu są udzielane przez przypisywanie odpowiednie role RBAC do grup i aplikacji w określonego zakresu. Można użyć [wbudowane role RBAC](../role-based-access-control/built-in-roles.md), takich jak współautora konta magazynu, aby przypisać uprawnienia do użytkowników.

Więcej informacji:

* [Kontrola dostępu oparta na rolach w usłudze Azure Active Directory](../role-based-access-control/role-assignments-portal.md)

## <a name="delegated-access-to-storage-objects"></a>Delegowany dostęp do magazynu obiektów
Sygnatury dostępu współdzielonego (SAS) umożliwiają dostęp delegowany do zasobów na koncie magazynu. Sygnatury dostępu Współdzielonego oznacza, że można udzielać się, że klient ograniczone uprawnienia do obiektów na koncie magazynu w określonym przedziale czasu i z określonym zestawem uprawnień. Ograniczone uprawnienia można przyznać bez konieczności udostępniania kluczy dostępu konta. Sygnatury dostępu Współdzielonego to identyfikator URI, który obejmuje w jego parametrów zapytania, wszystkie informacje niezbędne do uwierzytelniony dostęp do zasobów magazynu. Aby uzyskać dostęp do zasobów magazynu przy użyciu sygnatury dostępu Współdzielonego, klient tylko należy podać SAS odpowiedniego konstruktora lub metody.

Więcej informacji:

* [Opis modelu sygnatur dostępu Współdzielonego](../storage/common/storage-dotnet-shared-access-signature-part-1.md)
* [Tworzenie i używanie sygnatury dostępu Współdzielonego z magazynem obiektów Blob](../storage/blobs/storage-dotnet-shared-access-signature-part-2.md)

## <a name="encryption-in-transit"></a>Szyfrowanie podczas przesyłania
Szyfrowanie podczas przesyłania jest mechanizm ochrony danych podczas ich przesyłania w sieciach. Z usługą Azure Storage, można zabezpieczyć dane przy użyciu:

* [Szyfrowanie na poziomie transportu](../storage/common/storage-security-guide.md#encryption-in-transit), taki jak HTTPS podczas transferu danych do i z usługi Azure Storage.
* [Połączenie szyfrowania](../storage/common/storage-security-guide.md#using-encryption-during-transit-with-azure-file-shares), takimi jak szyfrowanie protokołu SMB 3.0 udziałów plików Azure.
* [Szyfrowanie po stronie klienta](../storage/common/storage-security-guide.md#using-client-side-encryption-to-secure-data-that-you-send-to-storage), aby szyfrować dane, zanim zostanie przekazany do magazynu, jak i do odszyfrowania danych po przeniesieniu poza magazynu.

Dowiedz się więcej na temat szyfrowania po stronie klienta:

* [Szyfrowanie po stronie klienta dla usługi Microsoft Azure Storage](https://blogs.msdn.microsoft.com/windowsazurestorage/2015/04/28/client-side-encryption-for-microsoft-azure-storage-preview/)
* [Zabezpieczenia chmury sterują serii: szyfrowanie przesyłanych danych](http://blogs.microsoft.com/cybertrust/2015/08/10/cloud-security-controls-series-encrypting-data-in-transit/)

## <a name="encryption-at-rest"></a>Szyfrowanie w spoczynku
W przypadku wielu organizacji [szyfrowanie danych magazynowanych](https://blogs.microsoft.com/cybertrust/2015/09/10/cloud-security-controls-series-encrypting-data-at-rest/) jest obowiązkowy krok na drodze danych suwerenności prywatności, zgodności i danych. Istnieją trzy funkcje platformy Azure, które zapewniają szyfrowania danych, która jest "w stanie spoczynku":

* [Szyfrowanie usługi Magazyn](../storage/common/storage-security-guide.md#encryption-at-rest) umożliwia zażądanie, czy Usługa magazynu automatyczne szyfrowanie danych podczas zapisywania go do magazynu Azure.
* [Szyfrowanie po stronie klienta](../storage/common/storage-security-guide.md#client-side-encryption) oferuje także funkcję szyfrowania w stanie spoczynku.
* [Szyfrowanie dysków Azure](../storage/common/storage-security-guide.md#using-azure-disk-encryption-to-encrypt-disks-used-by-your-virtual-machines) umożliwia szyfrowanie dysków systemu operacyjnego i dysków danych używanych przez maszyny wirtualne IaaS.

Dowiedz się więcej na temat szyfrowanie usługi magazynu:

* [Szyfrowanie usługi Magazyn Azure](https://azure.microsoft.com/services/storage/) jest dostępna dla [magazyn obiektów Blob Azure](https://azure.microsoft.com/services/storage/blobs/). Aby uzyskać szczegółowe informacje dotyczące innych typów magazynu Azure, zobacz [pliku](https://azure.microsoft.com/services/storage/files/), [dysku (magazyn w warstwie Premium)](https://azure.microsoft.com/services/storage/premium-storage/), [tabeli](https://azure.microsoft.com/services/storage/tables/), i [kolejki](https://azure.microsoft.com/services/storage/queues/).
* [Szyfrowanie usługi Magazyn Azure dla przechowywanych danych](../storage/common/storage-service-encryption.md)

## <a name="azure-disk-encryption"></a>Usługa Azure Disk Encryption
Azure szyfrowania dysków na maszynach wirtualnych (VM) ułatwia adres zabezpieczeń organizacji i wymagania zgodności przez szyfrowanie dysków maszyny Wirtualnej (w tym rozruchu i dysków z danymi) przy użyciu kluczy i zasad kontroli w [usługi Azure Key Vault](https://azure.microsoft.com/services/key-vault/).

Szyfrowanie dysków dla maszyn wirtualnych działa w przypadku systemów operacyjnych Linux i Windows. Używa również Key Vault, aby pomóc w zabezpieczaniu, zarządzanie i inspekcja użycia kluczy szyfrowania dysku. Wszystkie dane w dysków maszyny Wirtualnej jest szyfrowane, gdy przy użyciu technologii szyfrowania standardowych kont usługi Azure Storage. Szyfrowanie dysków rozwiązania dla systemu Windows jest oparta na [szyfrowania dysków funkcją BitLocker Microsoft](https://technet.microsoft.com/library/cc732774.aspx), i rozwiązanie Linux jest oparta na [dm-crypt](https://en.wikipedia.org/wiki/Dm-crypt).

Więcej informacji:

* [Szyfrowanie dysków Azure dla systemu Windows i maszyn wirtualnych systemu Linux IaaS](https://gallery.technet.microsoft.com/Azure-Disk-Encryption-for-a0018eb0)

## <a name="azure-key-vault"></a>W usłudze Azure Key Vault
Używa szyfrowania dysków Azure [usługi Azure Key Vault](https://azure.microsoft.com/services/key-vault/) ułatwiają kontrolowanie i zarządzanie nimi klucze szyfrowania dysku i kluczy tajnych w magazynie kluczy subskrypcji, podczas gdy zapewnienie, że wszystkie dane na dyskach maszyny wirtualnej są szyfrowane, gdy w platformy Azure Magazyn. Magazyn kluczy należy używać inspekcji kluczy i użycia zasad.

Więcej informacji:

* [Co to jest usługa Azure Key Vault?](../key-vault/key-vault-whatis.md)
* [Wprowadzenie do usługi Azure Key Vault](../key-vault/key-vault-get-started.md)
