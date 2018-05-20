---
title: 'Uaktualnianie aplikacji: serializacja danych | Dokumentacja firmy Microsoft'
description: Najlepsze rozwiązania dla serializacji danych i jak wpływa na uaktualnienia równoległe aplikacji.
services: service-fabric
documentationcenter: .net
author: vturecek
manager: timlt
editor: ''
ms.assetid: a5f36366-a2ab-4ae3-bb08-bc2f9533bc5a
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/02/2017
ms.author: vturecek
ms.openlocfilehash: 2f6fad0ecca09ff9210b5961301fea3446a88f11
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/16/2018
---
# <a name="how-data-serialization-affects-an-application-upgrade"></a>Jak serializacja danych wpływa na uaktualnienia aplikacji
W [aplikacji uaktualnienia stopniowego](service-fabric-application-upgrade.md), uaktualnienie ma zostać zastosowane do podzbioru węzłów, domeny uaktualnienia pojedynczo. W trakcie tego procesu są niektórych domen uaktualnienia w nowszej wersji aplikacji i niektóre domen uaktualnienia znajdują się na starszej wersji aplikacji. Podczas wdrożenia nowa wersja aplikacji musi mieć możliwość odczytu starą wersję danych, a starą wersję aplikacji musi mieć możliwość odczytu nowej wersji danych. Jeśli format danych jest niezgodny z przodu i do tyłu, uaktualnienie może się nie powieść lub gorsza, może utraty lub uszkodzenia danych. W tym artykule opisano, co stanowi formatu danych i oferuje najlepsze rozwiązania dotyczące zapewnienie, że dane są do przodu i do tyłu zgodne.

## <a name="what-makes-up-your-data-format"></a>Co tworzą formatu danych?
W sieci szkieletowej usług Azure dane, które jest utrwalenia i replikacji pochodzi z klasy C#. Dla aplikacji używających [niezawodnej kolekcje](service-fabric-reliable-services-reliable-collections.md), że dane są obiektów niezawodnej słowniki i kolejek. Dla aplikacji używających [Reliable Actors](service-fabric-reliable-actors-introduction.md), czyli stanu zapasowy aktora. Te klasy C# musi podlegać serializacji do utrwalenia i replikacji. W związku z tym format danych został zdefiniowany przez pola i właściwości, które są serializowane, a także jak ich są serializowane. Na przykład w `IReliableDictionary<int, MyClass>` dane są serializowane `int` i serializacji `MyClass`.

### <a name="code-changes-that-result-in-a-data-format-change"></a>Wynik zmianę formatu danych zmiany kodu
Ponieważ format danych jest określany przez klas C#, zmiany klasy może spowodować zmianę formatu danych. Należy uważać, aby upewnić się, że uaktualnienie stopniowe może obsłużyć zmianę formatu danych. Przykłady, które mogą powodować zmiany formatowania danych:

* Dodawanie lub usuwanie pól lub właściwości
* Zmiana nazwy pola lub właściwości
* Zmiany typów pól lub właściwości
* Zmiana nazwy klasy lub przestrzeni nazw

### <a name="data-contract-as-the-default-serializer"></a>Kontrakt danych jako domyślnego elementu serializującego
Serializator jest zazwyczaj odpowiedzialny za odczytywanie danych i deserializacji go do wersji bieżącej, nawet jeśli dane znajdują się w starszej lub *nowszej* wersji. Domyślnym elementem serializującym jest [serializator kontraktu danych](https://msdn.microsoft.com/library/ms733127.aspx), który zawiera reguły dobrze zdefiniowany kontroli wersji. Kolekcje niezawodnej Zezwalaj serializatora do zastąpienia, ale Reliable Actors aktualnie nie. Serializator danych odgrywa ważną rolę w naprawieniu uaktualnienia równoległe. Serializator kontraktu danych jest serializator zalecane dla aplikacji sieci szkieletowej usług.

## <a name="how-the-data-format-affects-a-rolling-upgrade"></a>Jak format danych wpływa na uaktualnienia stopniowego
Podczas uaktualnienia stopniowego, istnieją dwa główne scenariusze, w którym serializator napotkać starszej lub *nowszej* wersji danych:

1. Po węzeł zostanie uaktualniony i uruchamia tworzenie kopii zapasowej, nowe serializator załaduje dane, które zostało utrwalone na dysku przez starszą wersję.
2. Podczas uaktualnienia stopniowego klastra będzie zawierać mieszanie starej i nowej wersji kodu. Ponieważ replik może znajdować się w różnych domenach uaktualnienia i replik wysyłania danych do siebie, stary i/lub nowych wersji danych mogą wystąpić przez stary i/lub nowych wersji programu szeregującego.

> [!NOTE]
> "Nowa wersja" i "starą wersję" w tym miejscu można znaleźć wersji swój kod, który jest uruchomiony. "Nowe serializatora" odnosi się do kodu serializator, który jest wykonywany w nowej wersji aplikacji. "Nowe dane" odwołuje się do serializacji klasy C# z nowej wersji aplikacji.
> 
> 

Dwie wersje formatu kodu i danych musi być zarówno do przodu i do tyłu zgodne. Jeśli nie są zgodne, uaktualnienia stopniowego może zakończyć się niepowodzeniem lub dane mogą zostać utracone. Uaktualnienie stopniowe może zakończyć się niepowodzeniem, ponieważ kod lub serializator może zgłaszać wyjątków lub fault po napotkaniu innych wersji. Jeśli na przykład dodano nową właściwość, ale odrzuca je podczas deserializacji serializator stare dane mogą zostać utracone.

Kontrakt danych jest zalecanym rozwiązaniem dla zapewnienia, że dane są zgodne. Ma ona versioning dobrze zdefiniowane zasady Dodawanie, usuwanie i zmiana pola. Ma również obsługę zajmujących się nieznany pól, przechwytywanie do procesu serializacji i deserializacji i zajmujących się dziedziczenia klas. Aby uzyskać więcej informacji, zobacz [kontraktu danych przy użyciu](https://msdn.microsoft.com/library/ms733127.aspx).

## <a name="next-steps"></a>Kolejne kroki
[Uaktualnianie aplikacji za pomocą Visual Studio](service-fabric-application-upgrade-tutorial.md) przeprowadzi Cię przez proces uaktualnienia aplikacji przy użyciu programu Visual Studio.

[Uaktualnienie z aplikacji przy użyciu programu Powershell](service-fabric-application-upgrade-tutorial-powershell.md) przeprowadzi Cię przez proces uaktualnienia aplikacji przy użyciu programu PowerShell.

Kontrolowanie sposobu uaktualnienia aplikacji przy użyciu [uaktualnienia parametrów](service-fabric-application-upgrade-parameters.md).

Dowiedz się, jak korzystać z zaawansowanych funkcji podczas uaktualniania aplikacji, odwołując się do [Tematy zaawansowane](service-fabric-application-upgrade-advanced.md).

Rozwiązywania typowych problemów w uaktualnień aplikacji, korzystając z procedury opisanej w [Rozwiązywanie problemów z uaktualnieniami aplikacji ](service-fabric-application-upgrade-troubleshooting.md).

