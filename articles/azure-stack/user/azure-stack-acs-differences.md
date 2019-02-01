---
title: Usługa Azure stack magazynu różnice i zagadnienia | Dokumentacja firmy Microsoft
description: Poznaj różnice między magazynu usługi Azure stack i Azure storage oraz zagadnienia dotyczące wdrażania usługi Azure Stack.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 01/30/2019
ms.author: mabrigg
ms.reviwer: xiaofmao
ms.lastreviewed: 01/30/2019
ms.openlocfilehash: 11736b978242416bcfb95d3025975028e4148e98
ms.sourcegitcommit: 5978d82c619762ac05b19668379a37a40ba5755b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/31/2019
ms.locfileid: "55486542"
---
# <a name="azure-stack-storage-differences-and-considerations"></a>Usługa Azure storage stosu: Różnice i zagadnienia

*Dotyczy: Zintegrowane usługi Azure Stack, systemy i usługi Azure Stack Development Kit*

Magazyn usługi Azure Stack to zestaw usług magazynu w chmurze w usłudze Microsoft Azure Stack. Magazyn usługi Azure Stack zapewnia obiektów blob, tabela, kolejka i funkcji zarządzania konta z semantyką spójnych z platformą Azure.

Ten artykuł zawiera podsumowanie znane różnice usługi Azure Stack Storage z usługi Azure Storage. Zawiera również listę rzeczy, które należy wziąć pod uwagę podczas wdrażania usługi Azure Stack. Aby poznać Ogólne różnice między globalnej platformy Azure i usługi Azure Stack, zobacz temat [kluczowe zagadnienia](azure-stack-considerations.md) artykułu.

## <a name="cheat-sheet-storage-differences"></a>Ściągawka: Różnice magazynu

| Cecha | Azure (globalna) | Azure Stack |
| --- | --- | --- |
|File Storage|Oparte na chmurze udziałów plików SMB obsługiwane|Nie jest jeszcze obsługiwany
|Szyfrowanie usługi Azure storage dla danych magazynowanych|256-bitowego szyfrowania AES. Obsługuje szyfrowanie przy użyciu kluczy zarządzanych przez klienta w usłudze Key Vault.|Funkcja BitLocker 128-bitowego szyfrowania AES. Szyfrowanie przy użyciu kluczy zarządzanych przez klienta nie jest obsługiwane.
|Typ konta magazynu|Kont magazynu ogólnego zastosowania V1, V2 i obiektów Blob|Ogólnego przeznaczenia w wersji 1 tylko.
|Opcje replikacji|Magazyn lokalnie nadmiarowy, Magazyn geograficznie nadmiarowy, Magazyn geograficznie nadmiarowy geograficznie nadmiarowy i Magazyn strefowo nadmiarowy|Magazyn lokalnie nadmiarowy.
|Premium Storage|W pełni obsługiwane|Mogą być udostępniane, ale bez ograniczeń wydajności lub gwarancji.
|Dyski zarządzane|Premium i standardowa obsługiwane|Obsługiwane w przypadku używania wersji 1808 lub nowszej.
|Nazwa obiektu blob|1024 znaków (2048 bajtów)|880 znaków (1,760 w bajtach)
|Maksymalny rozmiar obiektu blob bloku|4.75 TB (100 MB X 50 000 bloków)|4.75 TB (100 MB x 50 000 bloków) dla aktualizacji 1802 lub nowszej wersji. 50 000 x 4 MB (około 195 GB) dla wcześniejszych wersji.
|Kopiowanie migawki obiektu blob strony|Kopii zapasowej Azure niezarządzanych dysków maszyn wirtualnych dołączony do uruchomionej maszyny Wirtualnej obsługiwane|Nie jest jeszcze obsługiwany.
|Kopiowanie przyrostowe migawki obiektu blob strony|W warstwie Premium i standardowa usługi Azure BLOB typu Page obsługiwane|Nie jest jeszcze obsługiwany.
|Warstwy magazynowania dla magazynu obiektów blob|Gorąca chłodna i archiwalna magazynu.|Nie jest jeszcze obsługiwany.
|Usuwania nietrwałego dla magazynu obiektów blob|Ogólnie dostępna|Nie jest jeszcze obsługiwany.
|Rozmiar maksymalny stronicowego obiektu blob|8 TB|1 TB
|Rozmiar strony stronicowego obiektu blob|512 bajtów|4 KB
|Rozmiar klucza w tabeli klucza partycji i wiersza|1024 znaków (2048 bajtów)|400 znaków (800 w bajtach)
|Migawki obiektu blob|Maksymalna liczba migawek jeden obiekt blob nie jest ograniczona.|Maksymalna liczba migawek jeden obiekt blob wynosi 1000.
|Uwierzytelnianie usługi Azure AD dla magazynu|W wersji zapoznawczej|Nie jest jeszcze obsługiwany.
|Niezmienne obiektów blob|Ogólnie dostępna|Nie jest jeszcze obsługiwany.
|Zapory i reguł sieci wirtualnej dla magazynu|Ogólnie dostępna|Nie jest jeszcze obsługiwany.|

Istnieją różnice za pomocą metryk usługi storage:

* Dane transakcji w metryk magazynowania nie odróżniają przepustowość sieci wewnętrznych lub zewnętrznych.
* Dane transakcji w metryk usługi storage nie ma na maszynie wirtualnej dostęp do dysków zainstalowanych.

## <a name="api-version"></a>Wersja interfejsu API

Następujące wersje są obsługiwane z usługą Azure Storage stosu:

Interfejsy API usługi Azure Storage:

1811 aktualizacji lub nowszych wersji:

- [2017-11-09](https://docs.microsoft.com/rest/api/storageservices/version-2017-11-09)
- [2017-07-29](https://docs.microsoft.com/rest/api/storageservices/version-2017-07-29)
- [2017-04-17](https://docs.microsoft.com/rest/api/storageservices/version-2017-04-17)
- [2016-05-31](https://docs.microsoft.com/rest/api/storageservices/version-2016-05-31)
- [2015-12-11](https://docs.microsoft.com/rest/api/storageservices/version-2015-12-11)
- [2015-07-08](https://docs.microsoft.com/rest/api/storageservices/version-2015-07-08)
- [2015-04-05](https://docs.microsoft.com/rest/api/storageservices/version-2015-04-05)

Poprzednie wersje:

- [2017-04-17](https://docs.microsoft.com/rest/api/storageservices/version-2017-04-17)
- [2016-05-31](https://docs.microsoft.com/rest/api/storageservices/version-2016-05-31)
- [2015-12-11](https://docs.microsoft.com/rest/api/storageservices/version-2015-12-11)
- [2015-07-08](https://docs.microsoft.com/rest/api/storageservices/version-2015-07-08)
- [2015-04-05](https://docs.microsoft.com/rest/api/storageservices/version-2015-04-05)

Interfejsy API zarządzania usługi Azure Storage:

1811 aktualizacji lub nowszych wersji:

- [2017-10-01](https://docs.microsoft.com/rest/api/storagerp/?redirectedfrom=MSDN)
- [2017-06-01](https://docs.microsoft.com/rest/api/storagerp/?redirectedfrom=MSDN)
- [2016-12-01](https://docs.microsoft.com/rest/api/storagerp/?redirectedfrom=MSDN)
- [2016-05-01](https://docs.microsoft.com/rest/api/storagerp/?redirectedfrom=MSDN)
- [2016-01-01](https://docs.microsoft.com/rest/api/storagerp/?redirectedfrom=MSDN)
- [2015-06-15](https://docs.microsoft.com/rest/api/storagerp/?redirectedfrom=MSDN)
- [2015-05-01-preview](https://docs.microsoft.com/rest/api/storagerp/?redirectedfrom=MSDN)

Poprzednie wersje:

- [2016-01-01](https://docs.microsoft.com/rest/api/storagerp/?redirectedfrom=MSDN)
- [2015-06-15](https://docs.microsoft.com/rest/api/storagerp/?redirectedfrom=MSDN)
- [2015-05-01-preview](https://docs.microsoft.com/rest/api/storagerp/?redirectedfrom=MSDN)

Aby uzyskać więcej informacji na temat biblioteki klienta magazynu obsługiwanych w usłudze Azure Stack zobacz: [Wprowadzenie do narzędzia programistyczne dotyczące magazynu usługi Azure Stack](azure-stack-storage-dev.md).

## <a name="next-steps"></a>Kolejne kroki

* [Wprowadzenie do narzędzi programistycznych usługi Azure Storage stosu](azure-stack-storage-dev.md)
* [Użyj narzędzia transferu danych dla magazynu usługi Azure Stack](azure-stack-storage-transfer.md)
* [Wprowadzenie do magazynu usługi Azure Stack](azure-stack-storage-overview.md)
