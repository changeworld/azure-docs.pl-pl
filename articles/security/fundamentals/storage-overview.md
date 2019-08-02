---
title: Funkcje zabezpieczeń, które mogą być używane z usługą Azure Storage | Microsoft Docs
description: Ten artykuł zawiera omówienie podstawowych funkcji zabezpieczeń platformy Azure, które mogą być używane z usługą Azure Storage.
services: security
documentationcenter: na
author: TerryLanfear
manager: barbkess
editor: TomSh
ms.assetid: 521180dc-2cc9-43f1-ae87-2701de7ca6b8
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/28/2019
ms.author: terrylan
ms.openlocfilehash: 249380b5bd9d95e969a9c7a812102b694b9d1e3b
ms.sourcegitcommit: 85b3973b104111f536dc5eccf8026749084d8789
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/01/2019
ms.locfileid: "68726543"
---
# <a name="azure-storage-security-overview"></a>Omówienie zabezpieczeń usługi Azure Storage

Ten artykuł zawiera omówienie funkcji zabezpieczeń platformy Azure, których można używać z usługą Azure Storage. Usługa Azure Storage to rozwiązanie magazynu w chmurze dla nowoczesnych aplikacji, które polegają na trwałości, dostępności i skalowalności, aby spełniać potrzeby klientów. Usługa Azure Storage zapewnia kompleksowy zestaw funkcji zabezpieczeń. Możesz:

* Zabezpiecz konto magazynu za pomocą Access Control opartych na rolach (RBAC) i Azure Active Directory.
* Zabezpieczanie danych przesyłanych między aplikacją i platformą Azure przy użyciu szyfrowania po stronie klienta, protokołu HTTPS lub protokołu SMB 3,0.
* Ustaw dane, które mają być automatycznie szyfrowane podczas zapisywania do usługi Azure Storage przy użyciu szyfrowanie usługi Storage.
* Ustaw dyski systemu operacyjnego i danych używane przez maszyny wirtualne do zaszyfrowania przy użyciu Azure Disk Encryption.
* Udzielanie delegowanego dostępu do obiektów danych w usłudze Azure Storage przy użyciu sygnatur dostępu współdzielonego (SASs).
* Użyj analizy, aby śledzić metodę uwierzytelniania używaną przez kogoś podczas uzyskiwania dostępu do magazynu.

Aby zapoznać się z bardziej szczegółowym opisem zabezpieczeń w usłudze Azure Storage, zobacz [Przewodnik po zabezpieczeniach usługi Azure Storage](../../storage/common/storage-security-guide.md). Ten przewodnik zawiera szczegółowe szczegółowe funkcji zabezpieczeń usługi Azure Storage. Te funkcje obejmują klucze konta magazynu, szyfrowanie danych podczas przesyłania i przechowywania oraz analizę magazynu.

## <a name="role-based-access-control"></a>Kontrola dostępu oparta na rolach

Za pomocą Access Control opartych na rolach można zabezpieczyć konto magazynu. Ograniczanie dostępu na podstawie [konieczności znajomości](https://en.wikipedia.org/wiki/Need_to_know) i najniższych zasad zabezpieczeń jest niezbędne dla organizacji, które chcą wymuszać zasady zabezpieczeń dostępu do danych. [](https://en.wikipedia.org/wiki/Principle_of_least_privilege) Te prawa dostępu są przyznawane przez przypisanie odpowiedniej roli RBAC do grup i aplikacji w określonym zakresie. Do przypisywania uprawnień użytkownikom można używać [wbudowanych ról RBAC](/azure/role-based-access-control/built-in-roles), takich jak współautor konta magazynu.

Dowiedz się więcej:

* [Azure Active Directory Access Control oparte na rolach](/azure/role-based-access-control/role-assignments-portal)

## <a name="delegated-access-to-storage-objects"></a>Delegowany dostęp do obiektów magazynu

Sygnatury dostępu współdzielonego zapewnia delegowany dostęp do zasobów na koncie magazynu. Sygnatura dostępu współdzielonego oznacza, że można udzielić klientowi ograniczonych uprawnień do obiektów na koncie magazynu przez określony czas i z określonym zestawem uprawnień. Można przyznać te ograniczone uprawnienia bez konieczności udostępniania kluczy dostępu do konta.

Sygnatura dostępu współdzielonego to identyfikator URI, który obejmuje parametry zapytania, wszystkie informacje niezbędne do uzyskania uwierzytelniony dostęp do zasobu magazynu. Aby uzyskać dostęp do zasobów magazynu za pomocą sygnatury dostępu współdzielonego, klient musi tylko dostarczyć sygnaturę dostępu współdzielonego do odpowiedniego konstruktora lub metody.

Dowiedz się więcej:

* [Omówienie modelu SAS](/azure/storage/common/storage-dotnet-shared-access-signature-part-1)
* [Tworzenie i używanie sygnatury dostępu współdzielonego z usługą BLOB Storage](/azure/storage/common/storage-dotnet-shared-access-signature-part-1)

## <a name="encryption-in-transit"></a>Szyfrowanie podczas przesyłania

Szyfrowanie podczas przesyłania jest mechanizmem ochrony danych przesyłanych między sieciami. Za pomocą usługi Azure Storage można zabezpieczyć dane przy użyciu:

* [Szyfrowanie na poziomie transportu](/azure/storage/common/storage-security-guide#encryption-in-transit), takie jak https, podczas transferu danych do usługi Azure Storage lub z niej.
* [Szyfrowanie sieciowe](/azure/storage/common/storage-security-guide#using-encryption-during-transit-with-azure-file-shares), takie jak szyfrowanie SMB 3,0, dla udziałów plików platformy Azure.
* [Szyfrowanie po stronie klienta](/azure/storage/common/storage-security-guide#using-client-side-encryption-to-secure-data-that-you-send-to-storage)umożliwia szyfrowanie danych przed ich przesłaniem do magazynu oraz odszyfrowanie danych po ich przeniesieniu poza magazyn.

Dowiedz się więcej o szyfrowaniu po stronie klienta:

* [Szyfrowanie po stronie klienta dla Microsoft Azure Storage](https://blogs.msdn.microsoft.com/windowsazurestorage/2015/04/28/client-side-encryption-for-microsoft-azure-storage-preview/)
* [Seria kontroli zabezpieczeń w chmurze: Szyfrowanie danych podczas przesyłania](https://cloudblogs.microsoft.com/microsoftsecure/2015/08/10/cloud-security-controls-series-encrypting-data-in-transit/)

## <a name="encryption-at-rest"></a>Szyfrowanie w spoczynku

W przypadku wielu organizacji [szyfrowanie danych w spoczynku](https://cloudblogs.microsoft.com/microsoftsecure/2015/09/10/cloud-security-controls-series-encrypting-data-at-rest/) stanowi obowiązkowy krok w kierunku prywatności, zgodności i suwerenności danych. Trzy funkcje platformy Azure zapewniają szyfrowanie danych, które są przechowywane w spoczynku:

* [Szyfrowanie usługi Storage](/azure/storage/common/storage-security-guide#encryption-at-rest) jest zawsze włączone i automatycznie szyfruje dane usługi magazynu podczas zapisywania ich w usłudze Azure Storage.
* [Szyfrowanie po stronie klienta](/azure/storage/common/storage-security-guide#client-side-encryption) udostępnia również funkcję szyfrowania w stanie spoczynku.
* [Azure Disk Encryption](/azure/storage/common/storage-security-guide#using-azure-disk-encryption-to-encrypt-disks-used-by-your-virtual-machines) umożliwia szyfrowanie dysków systemu operacyjnego i dysków danych używanych przez maszynę wirtualną IaaS.

Dowiedz się więcej o szyfrowanie usługi Storage:

* [Platforma azure szyfrowanie usługi Storage](https://azure.microsoft.com/services/storage/) jest dostępna dla [usługi Azure Blob Storage](https://azure.microsoft.com/services/storage/blobs/). Aby uzyskać szczegółowe informacje na temat innych typów usługi Azure Storage, zobacz [Azure Files](https://azure.microsoft.com/services/storage/files/), [Table Storage](https://azure.microsoft.com/services/storage/tables/)i [queue storage](https://azure.microsoft.com/services/storage/queues/).
* [szyfrowanie usługi Storage platformy Azure dla danych magazynowanych](/azure/storage/common/storage-service-encryption)

## <a name="azure-disk-encryption"></a>Azure Disk Encryption

Azure Disk Encryption dla maszyn wirtualnych ułatwia rozwiązywanie związanych z bezpieczeństwem i zgodnością organizacji. Służy do szyfrowania dysków maszyny wirtualnej (w tym dysków rozruchowych i danych) przy użyciu kluczy i zasad, które można kontrolować w [Azure Key Vault](https://azure.microsoft.com/services/key-vault/).

Szyfrowanie dysków dla maszyn wirtualnych działa w systemach operacyjnych Linux i Windows. Używa ona również Key Vault, aby pomóc w zabezpieczaniu i monitorowaniu kluczy szyfrowania dysków oraz zarządzaniu nimi. Wszystkie dane na dyskach maszyn wirtualnych są szyfrowane w stanie spoczynku przy użyciu standardowej technologii szyfrowania stosowanej w branży na kontach usługi Azure Storage. Rozwiązanie do szyfrowania dysków dla systemu Windows jest oparte na [szyfrowanie dysków funkcją BitLocker firmy Microsoft](https://technet.microsoft.com/library/cc732774.aspx), a rozwiązanie Linux opiera się na usłudze [dm-crypt](https://en.wikipedia.org/wiki/Dm-crypt).

Dowiedz się więcej

* [Azure Disk Encryption dla systemów Windows i Linux IaaS Virtual Machines](https://gallery.technet.microsoft.com/Azure-Disk-Encryption-for-a0018eb0)

## <a name="firewalls-and-virtual-networks"></a>Zapory i sieci wirtualne

Usługa Azure Storage umożliwia włączenie reguł zapory dla kont magazynu. Po włączeniu będą blokowane przychodzące żądania danych, w tym żądania z innych usług platformy Azure. Można skonfigurować wyjątki, aby zezwalać na ruch. Reguły zapory mogą być włączone na istniejących kontach magazynu lub w czasie jego tworzenia.

Ta funkcja powinna być używana do zabezpieczenia kont magazynu w określonym zestawie dozwolonych sieci.

Aby uzyskać więcej informacji na temat zapór usługi Azure Storage i sieci wirtualnych, zapoznaj się z artykułem [Konfigurowanie zapór i sieci wirtualnych usługi Azure Storage](/azure/storage/common/storage-network-security)

## <a name="azure-data-box"></a>Azure Data Box

Urządzenia Data Box, Data Box Disk i Data Box Heavy pomagają w przesyłaniu dużych ilości danych na platformę Azure, kiedy nie można skorzystać z sieci. Te urządzenia transferu danych w trybie offline są dostarczane między organizacją a centrum danych platformy Azure. Korzystają one z szyfrowania AES, aby pomóc w ochronie Twoich danych podczas przesyłania, i przechodzą dokładny proces oczyszczania po przekazaniu, aby usunąć Twoje dane z urządzenia.

Usługi Data Box Edge i Data Box Gateway to produkty transferu danych online, które działają jak bramy magazynu sieciowego, aby zarządzać danymi między Twoją witryną a platformą Azure. Data Box Edge, lokalne urządzenie sieciowe, przesyła dane na platformę Azure i z niej oraz przetwarza dane przy użyciu funkcji obliczeniowej na brzegu sieci z obsługą sztucznej inteligencji. Data Box Gateway to urządzenie wirtualne z funkcjami bramy magazynu.

Dowiedz się więcej:

* [Azure Data Box](https://azure.microsoft.com/services/storage/databox/)
* [Azure Data Box Edge](/azure/databox-online/data-box-edge-overview)
* [Azure Data Box Gateway](/azure/databox-online/data-box-gateway-overview)

## <a name="advanced-threat-protection"></a>Zaawansowana ochrona przed zagrożeniami

Usługa Azure Storage zapewnia zaawansowaną ochronę przed zagrożeniami dla dodatkowej warstwy analizy zabezpieczeń, która wykrywa nietypowe i potencjalnie szkodliwe próby uzyskania dostępu do konta magazynu lub korzystania z niego. Zaawansowana ochrona przed zagrożeniami służy do monitorowania dzienników diagnostycznych usługi Azure Storage dla podejrzanych żądań odczytu, zapisu lub usuwania do magazynu obiektów BLOB.

Alerty zaawansowanej ochrony przed zagrożeniami można przeglądać z poziomu [Azure Security Center](https://azure.microsoft.com/services/security-center/). Azure Security Center zawiera szczegółowe informacje o wykrytych podejrzanych działaniach i zaleca działania umożliwiające zbadanie i skorygowanie potencjalnego zagrożenia.

Dowiedz się więcej:

* [Omówienie zaawansowanej ochrony przed zagrożeniami w usłudze Azure Storage](/azure/storage/common/storage-advanced-threat-protection)

## <a name="azure-key-vault"></a>W usłudze Azure Key Vault

Azure Disk Encryption używa [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) , aby pomóc Ci kontrolować klucze szyfrowania dysków i wpisy tajne w ramach subskrypcji magazynu kluczy oraz nimi zarządzać. Gwarantuje również, że wszystkie dane na dyskach maszyn wirtualnych są szyfrowane w usłudze Azure Storage. Należy używać Key Vault do inspekcji kluczy i użycia zasad.

Dowiedz się więcej

* [Co to jest Azure Key Vault?](/azure/key-vault/key-vault-overview)
