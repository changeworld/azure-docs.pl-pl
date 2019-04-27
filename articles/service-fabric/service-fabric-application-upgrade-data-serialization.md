---
title: 'Uaktualnianie aplikacji: serializacja danych | Dokumentacja firmy Microsoft'
description: Najlepsze rozwiązania dotyczące serializacji danych i jak wpływa na uaktualnienia stopniowe aplikacji.
services: service-fabric
documentationcenter: .net
author: vturecek
manager: chackdan
editor: ''
ms.assetid: a5f36366-a2ab-4ae3-bb08-bc2f9533bc5a
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/02/2017
ms.author: vturecek
ms.openlocfilehash: 55cbd869e7434469ebddd7af493c91bfedafc594
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60614444"
---
# <a name="how-data-serialization-affects-an-application-upgrade"></a>Jak serializacji danych wpływa na uaktualnienie aplikacji
W [stopniowego uaktualnienia aplikacji](service-fabric-application-upgrade.md), uaktualnienie jest stosowany do podzbioru węzłów, jedną domenę uaktualnienia w danym momencie. W trakcie tego procesu niektórych domen uaktualnienia znajdują się na nowszą wersję aplikacji, a niektóre domeny uaktualnienia znajdują się na starszą wersję aplikacji. W miarę wprowadzania aktualizacji nowa wersja aplikacji musi mieć możliwość odczytu stara wersja usługi danych, a stara wersja aplikacji musi mieć możliwość odczytu nową wersję usługi danych. Jeśli format danych jest niezgodny ze starszymi i nowszymi, uaktualnienie może zakończyć się niepowodzeniem lub co gorsza, może być utraty lub uszkodzenia danych. W tym artykule omówiono, co stanowi przez Ciebie formacie danych i oferuje najlepsze rozwiązania umożliwiające zapewnienie, że Twoje dane są do przodu i do tyłu zgodne.

## <a name="what-makes-up-your-data-format"></a>Z czego składa się z formatu danych?
W usłudze Azure Service Fabric jest trwały i replikowane dane pochodzą z Twojego C# klasy. W przypadku aplikacji, które używają [elementów Reliable Collections](service-fabric-reliable-services-reliable-collections.md), że dane są obiektów słowników niezawodne i kolejek. W przypadku aplikacji, które używają [elementów Reliable Actors](service-fabric-reliable-actors-introduction.md), to znaczy stanu zapasowy aktora. Te C# klasy musi być możliwy do serializacji w celu utrwalenia i replikacji. W związku z tym format danych został zdefiniowany przez pola i właściwości, które są serializowane, a także jak są one serializacji. Na przykład w `IReliableDictionary<int, MyClass>` dane znajdują się Zserializowany obiekt `int` i Zserializowany obiekt `MyClass`.

### <a name="code-changes-that-result-in-a-data-format-change"></a>Zmian kodu, których wynikiem zmianę formatu danych
Ponieważ format danych jest określany przez C# klas, zmiany do klas może spowodować zmianę formatu danych. Należy uważać, aby upewnić się, że uaktualnienie stopniowe może obsłużyć zmianę formatu danych. Przykłady, które mogą powodować zmiany formatu danych:

* Dodawanie lub usuwanie pola lub właściwości
* Zmiana nazwy pola lub właściwości
* Zmienianie typów pól lub właściwości
* Zmiana nazwy klasy lub przestrzeni nazw

### <a name="data-contract-as-the-default-serializer"></a>Kontrakt danych jako domyślnego elementu serializującego
Serializator jest zazwyczaj odpowiedzialny za odczytywanie danych i deserializować go z bieżącą wersją, nawet jeśli dane znajdują się w starszej lub *nowszej* wersji. Domyślnym elementem serializującym jest [serializatora kontraktu danych](https://msdn.microsoft.com/library/ms733127.aspx), który ma reguły dobrze zdefiniowanych kontroli wersji. Niezawodne kolekcje zezwalają na serializatora do zastąpienia, ale elementów Reliable Actors obecnie nie. Serializator danych odgrywa ważną rolę w naprawieniu uaktualnienia stopniowe. Serializator kontraktów danych jest element serializujący, zalecane w przypadku aplikacji usługi Service Fabric.

## <a name="how-the-data-format-affects-a-rolling-upgrade"></a>Wpływ uaktualnienia stopniowego format danych
Podczas uaktualnień stopniowych, istnieją dwa główne scenariusze, gdzie może wystąpić starszą serializator lub *nowszej* wersja usługi danych:

1. Po węzeł zostanie uaktualniony i rozpoczyna wykonywanie kopii zapasowych, nowy element serializujący załaduje dane, który został zachowany na dysku przez starszą wersję.
2. Podczas uaktualnienia stopniowego klastra będzie zawierać zarówno stare i nowe wersje kodu. Ponieważ repliki może znajdować się w różnych domenach uaktualnienia i repliki wysyłać dane do siebie nawzajem, nowej lub starej wersji Twojej serializator może napotkał nowej lub starej wersji danych.

> [!NOTE]
> "Nowa wersja" i "starą wersję" w tym miejscu można znaleźć wersji swój kod, który jest uruchomiony. "Nowe serializatora" odnosi się do kodu serializator, który jest wykonywany w nowej wersji aplikacji. "Nowe dane" odnosi się do serializacji C# klasy z nowej wersji aplikacji.
> 
> 

Dwie wersje formatu kodu i danych muszą być zarówno do przodu i do tyłu zgodne. Jeśli nie są zgodne, uaktualnienia stopniowego może zakończyć się niepowodzeniem, lub dane mogą zostać utracone. Uaktualnienie stopniowe może zakończyć się niepowodzeniem, ponieważ kod lub serializator może zgłaszać wyjątków lub awarii po napotkaniu innej wersji. Dane mogą zostać utracone, jeśli na przykład dodano nową właściwość, ale stary element serializujący odrzuci ją podczas deserializacji.

Kontrakt danych jest zalecanym rozwiązaniem dla zapewnienia, że Twoje dane są zgodne. Zawiera reguły dobrze zdefiniowanych kontroli wersji, dodawanie, usuwanie i zmienianie pól. Ponadto wprowadzono obsługę rozwiązywania problemów związanych z polami nieznany, przechwytywanie procesem serializacji i deserializacji i rozwiązywania problemów związanych z dziedziczenia klasy. Aby uzyskać więcej informacji, zobacz [kontraktu danych za pomocą](https://msdn.microsoft.com/library/ms733127.aspx).

## <a name="next-steps"></a>Kolejne kroki
[Uaktualnienie z aplikacji przy użyciu programu Visual Studio](service-fabric-application-upgrade-tutorial.md) przeprowadzi uaktualnienie aplikacji przy użyciu programu Visual Studio.

[Uaktualnienie z aplikacji przy użyciu programu Powershell](service-fabric-application-upgrade-tutorial-powershell.md) przeprowadzi uaktualnienie aplikacji przy użyciu programu PowerShell.

Kontrolować, jak uaktualnić aplikację przy użyciu [parametry uaktualniania](service-fabric-application-upgrade-parameters.md).

Dowiedz się, jak korzystać z zaawansowanych funkcji podczas uaktualniania aplikacji, odwołując się do [Tematy zaawansowane](service-fabric-application-upgrade-advanced.md).

Rozwiązywanie typowych problemów podczas uaktualniania aplikacji korzystając z procedury opisanej w [Rozwiązywanie problemów z uaktualnieniami aplikacji](service-fabric-application-upgrade-troubleshooting.md).

