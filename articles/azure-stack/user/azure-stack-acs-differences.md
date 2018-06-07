---
title: Różnice magazynu Azure stosu i zagadnienia dotyczące | Dokumentacja firmy Microsoft
description: Opis różnic między Azure stosu magazynu i magazynu systemu Azure wraz ze stosu Azure zagadnienia dotyczące wdrażania.
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 05/21/2018
ms.author: jeffgilb
ms.reviwer: xiaofmao
ms.openlocfilehash: 2a6cb3f1a1f8009af411ba4d97a23194f6f089ae
ms.sourcegitcommit: 680964b75f7fff2f0517b7a0d43e01a9ee3da445
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34604464"
---
# <a name="azure-stack-storage-differences-and-considerations"></a>Magazyn Azure stosu: różnice i zagadnienia

*Dotyczy: Azure stosu zintegrowanych systemów i Azure stosu Development Kit*

Azure stosu magazynu to zbiór magazynu usługi w chmurze Microsoft Azure stosu. Magazyn Azure stosu zapewnia obiektów blob, tabeli, kolejki i funkcje zarządzania kontami z semantyki spójnej platformy Azure.

Ten artykuł zawiera podsumowanie znane różnice Azure stosu magazynu z usług Azure Storage. Zawiera także listę rzeczy, które należy wziąć pod uwagę podczas wdrażania usługi Azure stosu. Aby dowiedzieć się więcej o wysokim poziomie różnice między globalne Azure i stosu Azure, zobacz [kluczowe zagadnienia dotyczące](azure-stack-considerations.md) tematu.

## <a name="cheat-sheet-storage-differences"></a>Ściągawka: różnice magazynu

| Cecha | Azure (globalna) | Azure Stack |
| --- | --- | --- |
|File Storage|Oparte na chmurze udziałów plików SMB obsługiwane|Nie jest jeszcze obsługiwane
|Szyfrowanie usługi Magazyn Azure dla przechowywanych danych|256-bitowe szyfrowanie AES|Funkcja BitLocker 128-bitowe szyfrowanie AES
|Typ konta magazynu|Konta magazynu ogólnego przeznaczenia i Azure obiektów blob|Ogólnego przeznaczenia tylko.
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

Dostępne są również w przypadku metryki magazynu:

* Metryki magazynu danych transakcji nie odróżnia przepustowość sieci wewnętrznych lub zewnętrznych.
* Danych transakcji w metryki magazynu nie ma na maszynie wirtualnej dostęp do dysków zainstalowanych.

## <a name="api-version"></a>Wersja interfejsu API

Obsługiwane są następujące wersje z usługą Azure Storage stosu:

Interfejsy API z usług magazynu Azure:

Zaktualizuj 1802 lub nowsza:

 - [2017-04-17](https://docs.microsoft.com/rest/api/storageservices/version-2017-04-17)
 - [2016-05-31](https://docs.microsoft.com/rest/api/storageservices/version-2016-05-31)
 - [2015-12-11](https://docs.microsoft.com/rest/api/storageservices/version-2015-12-11)
 - [2015-07-08](https://docs.microsoft.com/rest/api/storageservices/version-2015-07-08)
 - [2015-04-05](https://docs.microsoft.com/rest/api/storageservices/version-2015-04-05)

Poprzednie wersje:

 - [2015-04-05](https://docs.microsoft.com/rest/api/storageservices/version-2015-04-05)

Magazyn Azure usługi Zarządzanie interfejsami API:

 - [2015-05-01-preview](https://docs.microsoft.com/rest/api/storagerp/?redirectedfrom=MSDN)
 - [2015-06-15](https://docs.microsoft.com/rest/api/storagerp/?redirectedfrom=MSDN)
 - [2016-01-01](https://docs.microsoft.com/rest/api/storagerp/?redirectedfrom=MSDN)

## <a name="sdk-versions"></a>Wersje zestawu SDK

Usługa Azure storage stosu obsługuje następujące biblioteki klienta:

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
