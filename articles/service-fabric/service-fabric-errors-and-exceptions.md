---
title: Typowe wyjątki klienta fabricclient z rolą zgłaszane | Dokumentacja firmy Microsoft
description: W tym artykule opisano typowe wyjątków i błędów, które może zostać wygenerowany przez interfejsy API klienta fabricclient z rolą podczas wykonywania aplikacji i operacji zarządzania klastrem.
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: ''
ms.assetid: bb821313-b221-479f-b08e-36cf07e60a07
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/20/2018
ms.author: ryanwi
ms.openlocfilehash: e854ed42b6af8bc090950e8399e3229e202a2ed0
ms.sourcegitcommit: 1438b7549c2d9bc2ace6a0a3e460ad4206bad423
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/20/2018
ms.locfileid: "36293416"
---
# <a name="common-exceptions-and-errors-when-working-with-the-fabricclient-apis"></a>Typowe wyjątków i błędów podczas pracy z interfejsami API klienta fabricclient z rolą
[Klienta fabricclient z rolą](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient#System_Fabric_FabricClient) interfejsów API umożliwiają administratorom klastra i aplikacji do wykonywania zadań administracyjnych w aplikacji, usługi lub klastra sieci szkieletowej usług. Na przykład wdrożenia aplikacji, uaktualniania i usuwania, sprawdzanie kondycji klastra lub testowania usługi. Deweloperzy aplikacji i Administratorzy klastra mogą używać interfejsów API klienta fabricclient z rolą do opracowywania narzędzia do zarządzania, klastra sieci szkieletowej usług i aplikacji.

Istnieje wiele różnych typów działań, które mogą być wykonywane przy użyciu klienta fabricclient z rolą.  Każda metoda może zgłaszają wyjątki, błędy ze względu na nieprawidłowe dane wejściowe, błędy podczas wykonywania lub infrastruktury przejściowych problemów.  Zobacz dokumentacji interfejsu API, aby znaleźć wyjątki, które są generowane przez określonej metody. Istnieją pewne wyjątki, jednak może zostać wygenerowany przez wiele różnych [klienta fabricclient z rolą](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient#System_Fabric_FabricClient) interfejsów API. W poniższej tabeli wymieniono wyjątki, które są wspólne dla interfejsów API klienta fabricclient z rolą.

| Wyjątek | Generowane, gdy |
| --- |:--- |
| [System.Fabric.FabricObjectClosedException](https://docs.microsoft.com/dotnet/api/system.fabric.fabricobjectclosedexception#System_Fabric_FabricObjectClosedException) |[Klienta fabricclient z rolą](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient#System_Fabric_FabricClient) obiekt jest w stanie zamkniętym. Usuwa [klienta fabricclient z rolą](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient#System_Fabric_FabricClient) obiekt jest używany i utworzenia wystąpienia nowy [klienta fabricclient z rolą](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient#System_Fabric_FabricClient) obiektu. |
| [System.TimeoutException](https://docs.microsoft.com/dotnet/core/api/system.timeoutexception#System_TimeoutException) |Upłynął limit czasu operacji. [OperationTimedOut](https://docs.microsoft.com/dotnet/api/system.fabric.fabricerrorcode#System_Fabric_FabricErrorCode) jest zwracany, gdy operacja przyjmuje więcej niż parametru MaxOperationTimeout, aby zakończyć. |
| [System.UnauthorizedAccessException](https://docs.microsoft.com/dotnet/core/api/system.unauthorizedaccessexception#System_UnauthorizedAccessException) |Nie można sprawdzić dostępu dla tej operacji. Zwracany jest błąd E_ACCESSDENIED. |
| [System.Fabric.FabricException](https://docs.microsoft.com/dotnet/api/system.fabric.fabricexception#System_Fabric_FabricException) |Podczas wykonywania operacji wystąpił błąd w czasie wykonywania. Wszystkie metody klienta fabricclient z rolą potencjalnie może zgłosić [FabricException](https://docs.microsoft.com/dotnet/api/system.fabric.fabricexception#System_Fabric_FabricException), [ErrorCode](https://docs.microsoft.com/dotnet/api/system.fabric.fabricexception#System_Fabric_FabricException_ErrorCode) właściwość wskazuje dokładną przyczynę wyjątku. Kody błędów są zdefiniowane w [FabricErrorCode](https://docs.microsoft.com/dotnet/api/system.fabric.fabricerrorcode#System_Fabric_FabricErrorCode) wyliczenia. |
| [System.Fabric.FabricTransientException](https://docs.microsoft.com/dotnet/api/system.fabric.fabrictransientexception#System_Fabric_FabricTransientException) |Operacja nie powiodła się ze względu na stan błędu przejściowego określonego rodzaju. Na przykład operacji może nie powieść, ponieważ kworum replik jest tymczasowo niedostępna. Wyjątki przejściowej odpowiadają nieudane operacje, które można ponowić. |

Niektóre typowe [FabricErrorCode](https://docs.microsoft.com/dotnet/api/system.fabric.fabricerrorcode#System_Fabric_FabricErrorCode) błędów, które mogą być zwracane w [FabricException](https://docs.microsoft.com/dotnet/api/system.fabric.fabricexception#System_Fabric_FabricException):

| Błąd | Warunek |
| --- |:--- |
| CommunicationError |Operacja się nie uda, spróbuj ponownie wykonać operację z powodu błędu komunikacji. |
| InvalidCredentialType |Typ poświadczeń jest nieprawidłowy. |
| InvalidX509FindType |X509FindType jest nieprawidłowy. |
| InvalidX509StoreLocation |X509 lokalizacja magazynu jest nieprawidłowa. |
| InvalidX509StoreName |X509 nazwa magazynu jest nieprawidłowy. |
| InvalidX509Thumbprint |X509 ciąg odcisk palca certyfikatu jest nieprawidłowy. |
| InvalidProtectionLevel |Poziom ochrony jest nieprawidłowa. |
| InvalidX509Store |X509 nie można otworzyć magazynu certyfikatów. |
| InvalidSubjectName |Nazwa podmiotu jest nieprawidłowa. |
| InvalidAllowedCommonNameList |Format wspólnej nazwy ciąg z listą jest nieprawidłowy. Powinno być rozdzielaną przecinkami listą. |

