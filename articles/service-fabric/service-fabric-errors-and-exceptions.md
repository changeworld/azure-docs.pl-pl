---
title: Typowe wyjątki FabricClient zgłaszane | Dokumentacja firmy Microsoft
description: W tym artykule opisano typowe wyjątki i błędy, które mogą być generowane przez interfejsy API FabricClient podczas wykonywania aplikacji i operacji zarządzania klastrem.
services: service-fabric
documentationcenter: .net
author: oanapl
manager: chackdan
editor: ''
ms.assetid: bb821313-b221-479f-b08e-36cf07e60a07
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/20/2018
ms.author: oanapl
ms.openlocfilehash: 5bf17f4ced6bb01d8b62b6fa40ed1aeffe6f712f
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60946548"
---
# <a name="common-exceptions-and-errors-when-working-with-the-fabricclient-apis"></a>Typowe wyjątki i błędy podczas pracy z interfejsami API FabricClient
[FabricClient](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient) interfejsy API umożliwiają administratorom klastrami i aplikacjami do wykonywania zadań administracyjnych w aplikacji, usług lub klastra usługi Service Fabric. Na przykład wdrożenie aplikacji, uaktualniania i usuwania, sprawdzanie kondycji klastra lub testowania usługi. Deweloperzy aplikacji i administratorów klastrów umożliwia interfejsy API FabricClient Tworzenie narzędzi do zarządzania klastrem usługi Service Fabric i aplikacji.

Istnieje wiele różnych rodzajów działań, które mogą być wykonywane przy użyciu FabricClient.  Każda metoda może generować wyjątki, błędy, ze względu na nieprawidłowe dane wejściowe, błędów środowiska uruchomieniowego lub problem z infrastrukturą przejściowego.  Zobacz dokumentację referencyjną interfejsu API, aby znaleźć wyjątki, które są generowane przez określonej metody. Istnieją różne niektóre wyjątki, jednak mogą być generowane przez wiele [FabricClient](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient) interfejsów API. W poniższej tabeli wymieniono wyjątki, które są wspólne dla interfejsów API FabricClient.

| Wyjątek | Generowany, gdy |
| --- |:--- |
| [System.Fabric.FabricObjectClosedException](https://docs.microsoft.com/dotnet/api/system.fabric.fabricobjectclosedexception) |[FabricClient](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient) obiekt jest w stanie zamkniętym. Usuwanie [FabricClient](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient) używania i tworzenia wystąpienia nowego obiektów [FabricClient](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient) obiektu. |
| [System.TimeoutException](https://docs.microsoft.com/dotnet/core/api/system.timeoutexception) |Upłynął limit czasu operacji. [OperationTimedOut](https://docs.microsoft.com/dotnet/api/system.fabric.fabricerrorcode) jest zwracany, jeśli operacja przyjmuje więcej niż MaxOperationTimeout, aby zakończyć. |
| [System.UnauthorizedAccessException](https://docs.microsoft.com/dotnet/core/api/system.unauthorizedaccessexception) |Sprawdzanie dostępu dla tej operacji nie powiodło się. E_ACCESSDENIED jest zwracana. |
| [System.Fabric.FabricException](https://docs.microsoft.com/dotnet/api/system.fabric.fabricexception) |Wystąpił błąd w czasie wykonywania podczas wykonywania operacji. Dowolnej z metod FabricClient potencjalnie może zgłosić [FabricException](https://docs.microsoft.com/dotnet/api/system.fabric.fabricexception), [ErrorCode](https://docs.microsoft.com/dotnet/api/system.fabric.fabricexception.ErrorCode) właściwość wskazuje dokładną przyczynę wyjątku. Kody błędów są zdefiniowane w [FabricErrorCode](https://docs.microsoft.com/dotnet/api/system.fabric.fabricerrorcode) wyliczenia. |
| [System.Fabric.FabricTransientException](https://docs.microsoft.com/dotnet/api/system.fabric.fabrictransientexception) |Operacja nie powiodła się ze względu na stan błędu przejściowego pewnego rodzaju. Na przykład operacja może zakończyć się niepowodzeniem, ponieważ tymczasowo kworum replik nie jest dostępny. Przejściowych wyjątków odpowiadają zakończone niepowodzeniem operacje, które mogą być ponawiane. |

Niektóre typowe [FabricErrorCode](https://docs.microsoft.com/dotnet/api/system.fabric.fabricerrorcode) błędy, które mogą być zwracane w [FabricException](https://docs.microsoft.com/dotnet/api/system.fabric.fabricexception):

| Błąd | Warunek |
| --- |:--- |
| CommunicationError |Operacja się nie uda, spróbuj ponownie wykonać operację z powodu błędu komunikacji. |
| InvalidCredentialType |Typ poświadczeń jest nieprawidłowy. |
| InvalidX509FindType |X509FindType jest nieprawidłowy. |
| InvalidX509StoreLocation |X509 lokalizacja magazynu jest nieprawidłowa. |
| InvalidX509StoreName |X509 nazwa magazynu jest nieprawidłowy. |
| InvalidX509Thumbprint |X509 ciąg odcisku palca certyfikatu jest nieprawidłowy. |
| InvalidProtectionLevel |Poziom ochrony jest nieprawidłowy. |
| InvalidX509Store |X509 nie można otworzyć magazynu certyfikatów. |
| InvalidSubjectName |Nazwa podmiotu jest nieprawidłowa. |
| InvalidAllowedCommonNameList |Format common ciąg z listą nazw jest nieprawidłowy. Powinno być rozdzielaną przecinkami listę. |

