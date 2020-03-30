---
title: Zgłaszane typowe wyjątki FabricClient
description: W tym artykule opisano typowe wyjątki i błędy, które mogą być generowane przez interfejsy API FabricClient podczas wykonywania operacji zarządzania aplikacjami i klastrami.
author: oanapl
ms.topic: conceptual
ms.date: 06/20/2018
ms.author: oanapl
ms.openlocfilehash: 9ad3097a490d4728e05ea90652c17c24b79cac2c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75457933"
---
# <a name="common-exceptions-and-errors-when-working-with-the-fabricclient-apis"></a>Typowe wyjątki i błędy podczas pracy z interfejsami API FabricClient
Interfejsy API [fabricClient](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient) umożliwiają administratorom klastrów i aplikacji wykonywanie zadań administracyjnych w aplikacji, usłudze lub klastrze sieci szkieletowej usług. Na przykład wdrażanie, uaktualnianie i usuwanie aplikacji, sprawdzanie kondycji klastra lub testowanie usługi. Deweloperzy aplikacji i administratorzy klastrów mogą używać interfejsów API FabricClient do tworzenia narzędzi do zarządzania klastrem sieci szkieletowej usług i aplikacjami.

Istnieje wiele różnych typów operacji, które mogą być wykonywane przy użyciu FabricClient.  Każda metoda może zgłaszać wyjątki dla błędów z powodu niepoprawnych danych wejściowych, błędów środowiska uruchomieniowego lub przejściowych problemów z infrastrukturą.  Zobacz dokumentację odwołania interfejsu API, aby dowiedzieć się, które wyjątki są generowane przez określoną metodę. Istnieją pewne wyjątki, jednak, które mogą być generowane przez wiele różnych interfejsów API [FabricClient.](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient) W poniższej tabeli wymieniono wyjątki, które są wspólne dla interfejsów API FabricClient.

| Wyjątek | Rzucony, gdy |
| --- |:--- |
| [System.Fabric.FabricObjectClosedException](https://docs.microsoft.com/dotnet/api/system.fabric.fabricobjectclosedexception) |[FabricClient](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient) Obiekt jest w stanie zamkniętym. Zutylizuj używany obiekt [FabricClient](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient) i skondycjuj tworzenie wystąpienia nowego obiektu [FabricClient.](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient) |
| [System.timeoutexception](https://docs.microsoft.com/dotnet/core/api/system.timeoutexception) |Limit czasu operacji. [OperationTimedOut](https://docs.microsoft.com/dotnet/api/system.fabric.fabricerrorcode) jest zwracany, gdy operacja trwa więcej niż MaxOperationTimeout, aby zakończyć. |
| [System.UnauthorizedAccessException](https://docs.microsoft.com/dotnet/core/api/system.unauthorizedaccessexception) |Sprawdzanie dostępu dla operacji nie powiodło się. E_ACCESSDENIED jest zwracany. |
| [System.Fabric.FabricEkception](https://docs.microsoft.com/dotnet/api/system.fabric.fabricexception) |Wystąpił błąd środowiska uruchomieniowego podczas wykonywania operacji. Każda z FabricClient metody potencjalnie throw [FabricException](https://docs.microsoft.com/dotnet/api/system.fabric.fabricexception), [ErrorCode](https://docs.microsoft.com/dotnet/api/system.fabric.fabricexception.ErrorCode) właściwość wskazuje dokładną przyczynę wyjątku. Kody błędów są zdefiniowane w wyliczeniu [FabricErrorCode.](https://docs.microsoft.com/dotnet/api/system.fabric.fabricerrorcode) |
| [System.Fabric.FabricPrzeientException](https://docs.microsoft.com/dotnet/api/system.fabric.fabrictransientexception) |Operacja nie powiodła się z powodu stanu błędu przejściowego pewnego rodzaju. Na przykład operacja może zakończyć się niepowodzeniem, ponieważ kworum replik jest tymczasowo nie osiągalne. Wyjątki przejściowe odpowiadają nieudanym operacjom, które można ponowić. |

Niektóre typowe [błędy FabricErrorCode,](https://docs.microsoft.com/dotnet/api/system.fabric.fabricerrorcode) które mogą być zwracane w [FabricException:](https://docs.microsoft.com/dotnet/api/system.fabric.fabricexception)

| Błąd | Warunek |
| --- |:--- |
| KomunikacjaError |Błąd komunikacji spowodował niepowodzenie operacji, ponów próbę wykonania operacji. |
| InvalidCredentialType |Typ poświadczeń jest nieprawidłowy. |
| Typ odnajdowania inwalidówX509 |X509FindType jest nieprawidłowy. |
| InvalidX509Lokalizacja magazynu |Lokalizacja sklepu X509 jest nieprawidłowa. |
| InvalidX509Sklepname |Nazwa sklepu X509 jest nieprawidłowa. |
| InvalidX509Thumbprint |Ciąg odcisku palca certyfikatu X509 jest nieprawidłowy. |
| Nieprawidłowy poziom ochrony |Poziom ochrony jest nieprawidłowy. |
| InvalidX509Sklep |Nie można otworzyć magazynu certyfikatów X509. |
| InvalidSubjectName (Nieprawidłowa nazwa |Nazwa podmiotu jest nieprawidłowa. |
| InvalidAllowedCommonNameList |Format wspólnego ciągu listy nazw jest nieprawidłowy. Powinna to być lista oddzielona przecinkami. |

