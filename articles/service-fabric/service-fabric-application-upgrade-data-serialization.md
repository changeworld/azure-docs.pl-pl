---
title: 'Uaktualnienie aplikacji: serializacja danych'
description: Najważniejsze wskazówki dotyczące serializacji danych i jej wpływu na uaktualnienia aplikacji stopniowych.
author: vturecek
ms.topic: conceptual
ms.date: 11/02/2017
ms.openlocfilehash: 7dc60c28b56982f82c1ac90db55ac752977ea2d6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75457498"
---
# <a name="how-data-serialization-affects-an-application-upgrade"></a>Jak serializacja danych wpływa na uaktualnienie aplikacji
W [uaktualnieniu aplikacji stopniowej](service-fabric-application-upgrade.md)uaktualnienie jest stosowane do podzbioru węzłów, jednej domeny uaktualnienia naraz. Podczas tego procesu niektóre domeny uaktualnienia znajdują się w nowszej wersji aplikacji, a niektóre domeny uaktualnienia znajdują się w starszej wersji aplikacji. Podczas wdrażania nowa wersja aplikacji musi być w stanie odczytać starą wersję danych, a stara wersja aplikacji musi być w stanie odczytać nową wersję danych. Jeśli format danych nie jest zgodny z danymi do przodu i do tyłu, uaktualnienie może zakończyć się niepowodzeniem lub co gorsza, dane mogą zostać utracone lub uszkodzone. W tym artykule omówiono, co stanowi format danych i oferuje najlepsze rozwiązania w celu zapewnienia, że dane są zgodne z danymi do przodu i wstecznie.

## <a name="what-makes-up-your-data-format"></a>Co składa się na format danych?
W usłudze Azure Service Fabric dane, które są utrwalone i replikowane pochodzi z klas języka C#. W przypadku aplikacji korzystających z [niezawodnych kolekcji](service-fabric-reliable-services-reliable-collections.md)te dane są obiektami w niezawodnych słownikach i kolejkach. Dla aplikacji, które używają [reliable actors](service-fabric-reliable-actors-introduction.md), który jest stanem kopii dla aktora. Te klasy Języka C# muszą być serializable być utrwalone i replikowane. W związku z tym format danych jest definiowany przez pola i właściwości, które są serializowane, a także jak są one serializowane. Na przykład w `IReliableDictionary<int, MyClass>` danych jest serializowane `int` i `MyClass`serializowane .

### <a name="code-changes-that-result-in-a-data-format-change"></a>Zmiany kodu, które powodują zmianę formatu danych
Ponieważ format danych jest określany przez klasy Języka C#, zmiany w klasach mogą powodować zmianę formatu danych. Należy zadbać o to, aby uaktualnienie stopniowe obsługiwało zmianę formatu danych. Przykłady, które mogą powodować zmiany formatu danych:

* Dodawanie lub usuwanie pól lub właściwości
* Zmiana nazwy pól lub właściwości
* Zmienianie typów pól lub właściwości
* Zmienianie nazwy klasy lub obszaru nazw

### <a name="data-contract-as-the-default-serializer"></a>Kontrakt danych jako domyślny serializator
Serializator jest ogólnie odpowiedzialny za odczyt danych i deserializacji go do bieżącej wersji, nawet jeśli dane są w starszej lub *nowszej* wersji. Domyślnym serializatorem jest [serializator kontraktu danych,](https://msdn.microsoft.com/library/ms733127.aspx)który ma dobrze zdefiniowane reguły przechowywania wersji. Niezawodne kolekcje umożliwiają serializatora do zastąpienia, ale reliable actors obecnie nie. Serializator danych odgrywa ważną rolę w włączaniu uaktualnień stopniowych. Serializator kontraktu danych jest serializator, który zaleca się dla aplikacji sieci szkieletowej usług.

## <a name="how-the-data-format-affects-a-rolling-upgrade"></a>Jak format danych wpływa na uaktualnienie stopniowe
Podczas uaktualniania stopniowego istnieją dwa główne scenariusze, w których serializator może napotkać starszą lub *nowszą* wersję danych:

1. Po uaktualnieniu węzła i uruchomieniu kopii zapasowej nowy serializator załaduje dane, które zostały utrwalone na dysku przez starą wersję.
2. Podczas uaktualniania stopniowego klaster będzie zawierał kombinację starych i nowych wersji kodu. Ponieważ repliki mogą być umieszczane w różnych domenach uaktualnienia, a repliki wysyłają dane do siebie, nowa i/lub stara wersja danych może zostać napotkana przez nową i/lub starą wersję serializatora.

> [!NOTE]
> "Nowa wersja" i "stara wersja" tutaj odnoszą się do wersji kodu, który jest uruchomiony. "Nowy serializator" odnosi się do kodu serializatora, który jest wykonywany w nowej wersji aplikacji. "Nowe dane" odnosi się do serializowanej klasy C# z nowej wersji aplikacji.
> 
> 

Dwie wersje kodu i formatu danych muszą być zgodne zarówno do przodu, jak i do tyłu. Jeśli nie są one zgodne, uaktualnienie stopniowe może zakończyć się niepowodzeniem lub dane mogą zostać utracone. Uaktualnienie stopniowe może zakończyć się niepowodzeniem, ponieważ kod lub serializator może zgłaszać wyjątki lub błąd, gdy napotka inną wersję. Dane mogą zostać utracone, jeśli, na przykład, nowa właściwość została dodana, ale stary serializator odrzuca go podczas deserializacji.

Data Contract jest zalecanym rozwiązaniem zapewniającym zgodność danych. Ma dobrze zdefiniowane reguły przechowywania wersji do dodawania, usuwania i zmieniania pól. Ma również obsługę do czynienia z nieznanych pól, podłączanie do procesu serializacji i deserializacji i radzenia sobie z dziedziczenia klasy. Aby uzyskać więcej informacji, zobacz [Korzystanie z kontraktu danych](https://msdn.microsoft.com/library/ms733127.aspx).

## <a name="next-steps"></a>Następne kroki
[Uaktualnianie aplikacji przy użyciu programu Visual Studio](service-fabric-application-upgrade-tutorial.md) przeprowadzi Cię przez uaktualnienie aplikacji przy użyciu programu Visual Studio.

[Uaktualnianie aplikacji przy użyciu programu Powershell](service-fabric-application-upgrade-tutorial-powershell.md) przeprowadzi Cię przez uaktualnienie aplikacji przy użyciu programu PowerShell.

Kontroluj sposób uaktualniania aplikacji przy użyciu [parametrów uaktualnienia](service-fabric-application-upgrade-parameters.md).

Dowiedz się, jak korzystać z zaawansowanych funkcji podczas uaktualniania aplikacji, odwołując się do [tematów zaawansowanych](service-fabric-application-upgrade-advanced.md).

Rozwiąż typowe problemy w uaktualnieniach aplikacji, odwołując się do kroków [rozwiązywania problemów z uaktualnieniami aplikacji](service-fabric-application-upgrade-troubleshooting.md).

