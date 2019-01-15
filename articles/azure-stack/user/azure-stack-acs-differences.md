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
ms.date: 12/03/2018
ms.author: mabrigg
ms.reviwer: xiaofmao
ms.lastreviewed: 12/03/2018
ms.openlocfilehash: 947886a96ab31150cf81ebea0a3cdd69e0273b01
ms.sourcegitcommit: 70471c4febc7835e643207420e515b6436235d29
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/15/2019
ms.locfileid: "54305758"
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

Aktualizacja wersji 1802 1809 aktualizacji:

- [2017-04-17](https://docs.microsoft.com/rest/api/storageservices/version-2017-04-17)
- [2016-05-31](https://docs.microsoft.com/rest/api/storageservices/version-2016-05-31)
- [2015-12-11](https://docs.microsoft.com/rest/api/storageservices/version-2015-12-11)
- [2015-07-08](https://docs.microsoft.com/rest/api/storageservices/version-2015-07-08)
- [2015-04-05](https://docs.microsoft.com/rest/api/storageservices/version-2015-04-05)

Poprzednie wersje:

- [2015-04-05](https://docs.microsoft.com/rest/api/storageservices/version-2015-04-05)

Interfejsy API zarządzania usługi Azure Storage:

- [2015-05-01-preview](https://docs.microsoft.com/rest/api/storagerp/?redirectedfrom=MSDN)
- [2015-06-15](https://docs.microsoft.com/rest/api/storagerp/?redirectedfrom=MSDN)
- [2016-01-01](https://docs.microsoft.com/rest/api/storagerp/?redirectedfrom=MSDN)

Poprzednie wersje:

 - [2016-01-01](https://docs.microsoft.com/rest/api/storagerp/?redirectedfrom=MSDN)
 - [2015-06-15](https://docs.microsoft.com/rest/api/storagerp/?redirectedfrom=MSDN)
 - [2015-05-01-preview](https://docs.microsoft.com/rest/api/storagerp/?redirectedfrom=MSDN)
 
## <a name="sdk-versions"></a>Wersje zestawu SDK

Magazyn usługi Azure Stack obsługuje podanych niżej bibliotek klienta:

| Biblioteka kliencka | Usługa Azure Stack obsługiwana wersja | Link                                                                                                                                                                                                                                                                                                                                     | Specyfikacja punktu końcowego       |
|----------------|-------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|------------------------------|
| .NET           | Z 6.2.0 do 8.7.0.          | Pakiet NuGet:<br>https://www.nuget.org/packages/WindowsAzure.Storage/<br> <br>Wersja usługi GitHub:<br>https://github.com/Azure/azure-storage-net/releases                                                                                                                                                                                    | pliku App.config              |
| Java           | Z 4.1.0 do 6.1.0           | Pakiet maven:<br>http://mvnrepository.com/artifact/com.microsoft.azure/azure-storage<br> <br>Wersja usługi GitHub:<br>https://github.com/Azure/azure-storage-java/releases                                                                                                                                                                    | Ustawienia parametrów połączenia      |
| Node.js        | Z 1.1.0 do 2.7.0           | NPM link:<br>https://www.npmjs.com/package/azure-storage<br>(Na przykład: Uruchom "npm zainstalować azure-storage@2.7.0")<br> <br>Wersja usługi GitHub:<br>https://github.com/Azure/azure-storage-node/releases                                                                                                                                         | Deklaracja wystąpienia usługi |
| C++            | Z 2.4.0 do 3.1.0           | Pakiet NuGet:<br>https://www.nuget.org/packages/wastorage.v140/<br> <br>Wersja usługi GitHub:<br>https://github.com/Azure/azure-storage-cpp/releases                                                                                                                                                                                          | Ustawienia parametrów połączenia      |
| PHP            | Z 0.15.0 1.0.0          | Wersja usługi GitHub:<br>https://github.com/Azure/azure-storage-php/releases<br> <br>Instalowanie za pomocą Composer (Zobacz szczegóły poniżej)                                                                                                                                                                                                                  | Ustawienia parametrów połączenia      |
| Python         | Z 0.30.0 1.0.0          | Wersja usługi GitHub:<br>https://github.com/Azure/azure-storage-python/releases                                                                                                                                                                                                                                                                | Deklaracja wystąpienia usługi |
| Ruby           | Z 0.12.1 na 1.0.1          | Pakiet RubyGems:<br>Wspólne:<br>https://rubygems.org/gems/azure-storage-common/<br>Obiekt blob: https://rubygems.org/gems/azure-storage-blob/<br>Kolejka: https://rubygems.org/gems/azure-storage-queue/<br>Tabela: https://rubygems.org/gems/azure-storage-table/<br> <br>Wersja usługi GitHub:<br>https://github.com/Azure/azure-storage-ruby/releases | Ustawienia parametrów połączenia      |

## <a name="next-steps"></a>Kolejne kroki

* [Wprowadzenie do narzędzi programistycznych usługi Azure Storage stosu](azure-stack-storage-dev.md)
* [Wprowadzenie do magazynu usługi Azure Stack](azure-stack-storage-overview.md)
