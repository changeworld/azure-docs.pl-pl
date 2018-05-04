---
title: 'Magazynu Azure stosu: Różnice i zagadnienia dotyczące'
description: Opis różnic między Azure stosu magazynu i usługi Azure Storage oraz zagadnienia dotyczące wdrażania usługi Azure stosu.
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
ms.reviwer: xiaofmao
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 02/21/2017
ms.author: jeffgilb
ms.openlocfilehash: 165a899dbad0893b3a2bddcfc68c9b5d737e9d3d
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2018
---
# <a name="azure-stack-storage-differences-and-considerations"></a>Magazynu Azure stosu: Różnice i zagadnienia dotyczące

*Dotyczy: Azure stosu zintegrowanych systemów i Azure stosu Development Kit*

Magazyn Azure stosu to zbiór magazynu usługi w chmurze Microsoft Azure stosu. Usługa Azure Storage stosu udostępnia obiektów blob, tabeli, kolejki i funkcje zarządzania kontami z semantyki spójnej platformy Azure.

Ten artykuł zawiera podsumowanie znane różnice Azure stosu magazynu z usługi Magazyn Azure. Także podsumowanie innych kwestii, które należy wziąć pod uwagę podczas wdrażania usługi Azure stosu. Aby uzyskać informacje ogólne różnice między stosu Azure i usługi Azure, zobacz [kluczowe zagadnienia dotyczące](azure-stack-considerations.md) tematu.

## <a name="cheat-sheet-storage-differences"></a>Ściągawka: różnice magazynu

| Cecha | Azure (globalna) | Azure Stack |
| --- | --- | --- |
|File Storage|Oparte na chmurze udziałów plików SMB obsługiwane|Nie jest jeszcze obsługiwane
|Szyfrowanie usługi Azure Storage dla danych magazynowanych|256-bitowe szyfrowanie AES|Funkcja BitLocker 128-bitowe szyfrowanie AES
|Typ konta magazynu|Ogólnego przeznaczenia i Azure kont magazynu obiektów Blob|Ogólnego przeznaczenia tylko.
|Opcje replikacji|Magazyn lokalnie nadmiarowy, magazynu geograficznie nadmiarowego magazynu geograficznie nadmiarowego dostęp do odczytu i Magazyn strefowo nadmiarowy|Magazyn lokalnie nadmiarowy.
|Premium Storage|W pełni obsługiwane|Można udostępnić, ale brak limitu wydajności lub gwarancji.
|Dyski zarządzane|Premium i standardowa obsługiwane|Nie jest jeszcze obsługiwane.
|Nazwa obiektu blob|1024 znaków (2048 bajtów)|880 znaków (1,760 w bajtach)
|Maksymalny rozmiar obiektu blob bloku|4,75 TB (100 MB X 50 000 bloków)|4,75 TB (100 MB x 50 000 bloków) 1802 aktualizacji lub nowszej wersji. 50 000 x 4 MB (około 195 GB) w poprzednich wersjach.
|Kopiowanie migawki obiektu blob strony|Kopia zapasowa Azure niezarządzane maszyny Wirtualnej dysków dołączonych do uruchomionej maszyny Wirtualnej obsługiwane|Nie jest jeszcze obsługiwane.
|Kopiuj przyrostowe migawki obiektu blob strony|Premium i standardowa Azure stronicowe obiekty BLOB obsługiwane|Nie jest jeszcze obsługiwane.
|Warstwy magazynowania dla magazynu obiektów blob|Wdrażanie cool oraz archiwum warstw magazynowania.|Nie jest jeszcze obsługiwane.
Usuwania nietrwałego dla magazynu obiektów blob|Wersja zapoznawcza|Nie jest jeszcze obsługiwane.
|Maksymalny rozmiar obiektu blob strony|8 TB|1 TB
|Rozmiar strony obiektu blob strony|512 bajtów|4 KB
|Rozmiar klucza w tabeli klucz partycji i wiersza|1024 znaków (2048 bajtów)|400 znaków (800 w bajtach)
|Migawki obiektu blob|Maksymalna liczba migawek jednego obiektu blob nie jest ograniczona.|Maksymalna liczba migawek jednego obiektu blob jest 1000.|

### <a name="metrics"></a>Metryki
Istnieją pewne różnice z metryki magazynu:
* Metryki magazynu danych transakcji nie odróżnia przepustowość sieci wewnętrznych lub zewnętrznych.
* Danych transakcji w metryki magazynu nie ma na maszynie wirtualnej dostęp do dysków zainstalowanych.

## <a name="api-version"></a>Wersja interfejsu API
Obsługiwane są następujące wersje z usługą Azure Storage stosu:

Interfejsy API z usług magazynu Azure:

Zaktualizuj 1802 lub nowsza:
 - [2017-04-17](https://docs.microsoft.com/rest/api/storageservices/version-2017-04-17)
 - [2016-05-31](https://docs.microsoft.com/rest/api/storageservices/version-2016-05-31)
 - [2015-12-11](https://docs.microsoft.com/rest/api/storageservices/version-2015-12-11)
 - [2015-07-08 ](https://docs.microsoft.com/rest/api/storageservices/version-2015-07-08)
 - [2015-04-05](https://docs.microsoft.com/rest/api/storageservices/version-2015-04-05)

Poprzednie wersje:
 - [2015-04-05](https://docs.microsoft.com/rest/api/storageservices/version-2015-04-05)


Magazyn Azure usługi Zarządzanie interfejsami API:

 - [2015-05-01-preview](https://docs.microsoft.com/rest/api/storagerp/?redirectedfrom=MSDN)
 - [2015-06-15](https://docs.microsoft.com/rest/api/storagerp/?redirectedfrom=MSDN)
 - [2016-01-01](https://docs.microsoft.com/rest/api/storagerp/?redirectedfrom=MSDN)

## <a name="sdk-versions"></a>Wersje zestawu SDK

Obsługiwane są następujące biblioteki klienta z usługą Azure Storage stosu:

| Biblioteka kliencka | Azure stosu obsługiwana wersja | Link                                                                                                                                                                                                                                                                                                                                     | Specyfikacja punktu końcowego       |
|----------------|-------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|------------------------------|
| .NET           | Z 6.2.0 do 8.7.0.          | Pakiet Nuget:<br>https://www.nuget.org/packages/WindowsAzure.Storage/<br> <br>Wersja usługi GitHub:<br>https://github.com/Azure/azure-storage-net/releases                                                                                                                                                                                    | pliku App.config              |
| Java           | Z 4.1.0 do 6.1.0           | Pakiet maven:<br>http://mvnrepository.com/artifact/com.microsoft.azure/azure-storage<br> <br>Wersja usługi GitHub:<br>https://github.com/Azure/azure-storage-java/releases                                                                                                                                                                    | Ustawienia połączenia      |
| Node.js        | Z 1.1.0 do 2.7.0           | Łącze NPM:<br>https://www.npmjs.com/package/azure-storage<br>(Na przykład: Uruchom "instalacji narzędzia npm azure-storage@2.7.0")<br> <br>Wersja usługi Github:<br>https://github.com/Azure/azure-storage-node/releases                                                                                                                                         | Deklaracja wystąpienia usługi |
| C++            | Z 2.4.0 do 3.1.0           | Pakiet Nuget:<br>https://www.nuget.org/packages/wastorage.v140/<br> <br>Wersja usługi GitHub:<br>https://github.com/Azure/azure-storage-cpp/releases                                                                                                                                                                                          | Ustawienia połączenia      |
| PHP            | Z 0.15.0 do 1.0.0          | Wersja usługi GitHub:<br>https://github.com/Azure/azure-storage-php/releases<br> <br>Zainstaluj za pośrednictwem Composer (Zobacz szczegóły poniżej)                                                                                                                                                                                                                  | Ustawienia połączenia      |
| Python         | Z 0.30.0 do 1.0.0          | Wersja usługi GitHub:<br>https://github.com/Azure/azure-storage-python/releases                                                                                                                                                                                                                                                                | Deklaracja wystąpienia usługi |
| Ruby           | Z 0.12.1 do 1.0.1          | Pakiet RubyGems:<br>Wspólne:<br>https://rubygems.org/gems/azure-storage-common/<br>Obiekt blob: https://rubygems.org/gems/azure-storage-blob/<br>Kolejki: https://rubygems.org/gems/azure-storage-queue/<br>Tabela: https://rubygems.org/gems/azure-storage-table/<br> <br>Wersja usługi GitHub:<br>https://github.com/Azure/azure-storage-ruby/releases | Ustawienia połączenia      |

## <a name="next-steps"></a>Kolejne kroki

* [Wprowadzenie do narzędzi programistycznych magazyn Azure stosu](azure-stack-storage-dev.md)
* [Wprowadzenie do magazynu Azure stosu](azure-stack-storage-overview.md)

