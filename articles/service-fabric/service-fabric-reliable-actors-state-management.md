---
title: Elementy Reliable Actors stanu zarządzania | Dokumentacja firmy Microsoft
description: Opisano, jak stanu elementów Reliable Actors jest zarządzany, trwały i replikowane w celu zapewnienia wysokiej dostępności.
services: service-fabric
documentationcenter: .net
author: vturecek
manager: chackdan
editor: ''
ms.assetid: 37cf466a-5293-44c0-a4e0-037e5d292214
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/02/2017
ms.author: vturecek
ms.openlocfilehash: 65dd47ab21ca4b1c50e0f17b73e7bc4eae8a96e8
ms.sourcegitcommit: c6dc9abb30c75629ef88b833655c2d1e78609b89
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2019
ms.locfileid: "58665784"
---
# <a name="reliable-actors-state-management"></a>Niezawodne zarządzanie stanem aktorów
Elementy Reliable Actors są jednowątkowe obiekty, hermetyzują logiki i stanu. Ponieważ aktorów są uruchamiane na usług Reliable Services, ich niezawodne zachowują stan, przy użyciu tego samego trwałości i mechanizmów replikacji. W ten sposób Aktorzy nie strać ich stanu po awarii po ponownej aktywacji po wyrzucania elementów bezużytecznych lub po przeniesieniu ich między węzłami w klastrze z powodu równoważenia zasobów lub uaktualnień.

## <a name="state-persistence-and-replication"></a>Trwałość stanu i replikacji
Wszystkie elementy Reliable Actors są traktowane jako *stanowa* ponieważ każde wystąpienie aktora mapuje do unikatowego identyfikatora. Oznacza to, że wielokrotnego wywołania do tego samego Identyfikatora aktora są kierowane do tego samego wystąpienia aktora. W systemie bezstanowych z drugiej strony, wywołań klienta nie ma gwarancji można kierować do tego samego serwera za każdym razem, gdy. Z tego powodu usług aktora są zawsze usług stanowych.

Mimo że Aktorzy są traktowane jako stanowe, które nie oznacza to, że ich musi niezawodnego przechowywania stanu. Aktorzy można wybrać poziom trwałości stanu i replikacji na podstawie swoich danych wymagania dotyczące magazynu:

* **Stan trwały**: Stan jest zachowywany na dysku, a następnie są replikowane do co najmniej trzema replikami. Utrwalonego stanu jest najbardziej niezawodne opcji magazynu stanu, gdy stan można utrwalić za pośrednictwem awarii całego klastra.
* **Stan volatile**: Stan jest replikowana do co najmniej trzema replikami i przechowywane tylko w pamięci. Stan volatile zapewnia odporność względem awarii węzła i niepowodzeń aktora i podczas uaktualnień i równoważenia zasobów. Jednak nie jest trwały stan dysku. Dlatego jeśli wszystkie repliki zostaną utracone na raz, stan zostanie utracony także.
* **Bez stanu utrwalonego**: Stanu nie jest replikowany lub zapisywane na dysku, używaj tylko dla podmiotów, które nie wymagają niezawodne zarządzania stanem.

Każdy poziom trwałości jest po prostu inny *dostawca stanu* i *replikacji* konfiguracji usługi. Określa, czy stan są zapisywane do dysku zależy od dostawcy stanu — składnik w usługi reliable service, który zapisuje stan. Replikacja zależy od tego, jak wiele replik, usługa jest wdrażany z. Podobnie jak w przypadku usług Reliable Services, zarówno dostawca stanu, jak i liczby replik łatwo można ustawić ręcznie. Struktura aktora zapewnia atrybutu, gdy na aktora, automatycznie wybiera domyślny dostawca stanu i automatycznie generuje ustawienia liczba replik osiągnięcie jednego z tych trzech ustawień stanu trwałego. Atrybut StatePersistence nie jest dziedziczone przez klasy pochodnej, każdy typ aktora należy podać jego poziom StatePersistence.

### <a name="persisted-state"></a>Utrwalonego stanu
```csharp
[StatePersistence(StatePersistence.Persisted)]
class MyActor : Actor, IMyActor
{
}
```
```Java
@StatePersistenceAttribute(statePersistence = StatePersistence.Persisted)
class MyActorImpl  extends FabricActor implements MyActor
{
}
```  
To ustawienie powoduje użycie dostawcy stanu, która przechowuje dane na dysku i automatycznie ustawia liczbę replik usługi do 3.

### <a name="volatile-state"></a>Stan volatile
```csharp
[StatePersistence(StatePersistence.Volatile)]
class MyActor : Actor, IMyActor
{
}
```
```Java
@StatePersistenceAttribute(statePersistence = StatePersistence.Volatile)
class MyActorImpl extends FabricActor implements MyActor
{
}
```
To ustawienie używa dostawcy w pamięci — tylko do stanu i ustawia liczbę replik na 3.

### <a name="no-persisted-state"></a>Nie utrwalonego stanu
```csharp
[StatePersistence(StatePersistence.None)]
class MyActor : Actor, IMyActor
{
}
```
```Java
@StatePersistenceAttribute(statePersistence = StatePersistence.None)
class MyActorImpl extends FabricActor implements MyActor
{
}
```
To ustawienie korzysta z dostawcy usługi w pamięci — tylko do stanu i ustawia liczbę replik na 1.

### <a name="defaults-and-generated-settings"></a>Wartości domyślne i ustawienia wygenerowany
Jeśli używasz `StatePersistence` atrybutu, dostawca stanu jest automatycznie wybrana w czasie wykonywania po uruchomieniu usługi aktora. Liczba replik, jednak jest ustawiony w czasie kompilacji przez narzędzia Visual Studio do kompilacji aktora. Narzędzia do kompilacji automatycznie wygenerować *domyślnej usługi* usługi aktora w ApplicationManifest.xml. Parametry są tworzone dla **rozmiar zestawu replik min** i **rozmiar zestawu replik docelowej**.

Te parametry można zmienić ręcznie. Ale każdorazowo `StatePersistence` zmienić atrybutu, parametry są ustawione na wartości domyślne repliki Ustaw rozmiar dla wybranego `StatePersistence` atrybutu, zastępując wszystkie poprzednie wartości. Oznacza to, czy wartości, które można ustawić w ServiceManifest.xml *tylko* zastąpione w czasie kompilacji, po zmianie `StatePersistence` wartość atrybutu.

```xml
<ApplicationManifest xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="Application12Type" ApplicationTypeVersion="1.0.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
   <Parameters>
      <Parameter Name="MyActorService_PartitionCount" DefaultValue="10" />
      <Parameter Name="MyActorService_MinReplicaSetSize" DefaultValue="3" />
      <Parameter Name="MyActorService_TargetReplicaSetSize" DefaultValue="3" />
   </Parameters>
   <ServiceManifestImport>
      <ServiceManifestRef ServiceManifestName="MyActorPkg" ServiceManifestVersion="1.0.0" />
   </ServiceManifestImport>
   <DefaultServices>
      <Service Name="MyActorService" GeneratedIdRef="77d965dc-85fb-488c-bd06-c6c1fe29d593|Persisted">
         <StatefulService ServiceTypeName="MyActorServiceType" TargetReplicaSetSize="[MyActorService_TargetReplicaSetSize]" MinReplicaSetSize="[MyActorService_MinReplicaSetSize]">
            <UniformInt64Partition PartitionCount="[MyActorService_PartitionCount]" LowKey="-9223372036854775808" HighKey="9223372036854775807" />
         </StatefulService>
      </Service>
   </DefaultServices>
</ApplicationManifest>
```

## <a name="state-manager"></a>Menedżer stanu
Każde wystąpienie aktora ma swój własny Menedżer stanu: struktura danych podobne do słownika, która w niezawodny sposób przechowuje par klucz/wartość. Menedżer stanu jest otokę dostawca stanu. Służy do przechowywania danych, niezależnie od tego, który jest używany ustawień stanu trwałego. Nie zapewnia żadnej gwarancji, że uruchomionej usługi aktora można zmienić ustawienie volatile stanu (w pamięci — tylko) na ustawienie stanu utrwalonego za pośrednictwem uaktualnienie stopniowe przy jednoczesnym zachowaniu danych. Jednak jest możliwe zmienić liczbę replik dla uruchomionej usługi.

Menedżer stanu klucze muszą być ciągami. Wartości są ogólne i mogą być dowolnego typu, łącznie z typami niestandardowymi. Wartości przechowywane w Menedżer stanu musi być możliwy do serializacji kontrakt danych, ponieważ one mogą być przekazywane za pośrednictwem sieci do innych węzłów podczas replikacji i mogą być zapisywane na dysku, w zależności od ustawienia trwałość stanu aktora.

Menedżer stanu przedstawia typowe metody słownika Zarządzanie stanem, podobne do tych w niezawodnym słowniku.

Aby uzyskać przykłady zarządzania stanu aktora, przeczytaj [dostęp, zapisywania i usuwania stanu elementów Reliable Actors](service-fabric-reliable-actors-access-save-remove-state.md).

## <a name="best-practices"></a>Najlepsze praktyki
Poniżej przedstawiono niektóre sugerowane rozwiązania i wskazówki dotyczące rozwiązywania problemów związanych z zarządzaniem stanu usługi aktora.

### <a name="make-the-actor-state-as-granular-as-possible"></a>Wprowadź szczegółowe, jak to możliwe do stanu aktora
Jest to niezbędne, wydajność i użycie zasobów dla aplikacji. Zawsze, gdy występuje wszelkich zapisu/aktualizacji "o nazwie stan" aktora, wartości całkowitej odpowiadającego temu Państwu"o nazwie" jest serializowane i wysyłane przez sieć do replik pomocniczych.  Pomocnicze bazy danych zapisu na dysku lokalnym i odpowiedzi do repliki podstawowej. Gdy podstawowy otrzyma potwierdzeń z kworum replik pomocniczych, zapisuje stan jego dysk lokalny. Na przykład załóżmy, że wartość jest klasa, która ma 20 elementów członkowskich i o rozmiarze 1 MB. Nawet wtedy, gdy został zmodyfikowany tylko jeden z członków klasy, która ma rozmiar 1 KB, zakończenia płaci się koszt serializacji i zapisu na dysku i sieci za pełną 1 MB. Podobnie jeśli wartość wynosi kolekcję (takie jak listy, tablicy lub słownika), naliczana jest opłata za pełną kolekcję nawet wtedy, gdy jeden z członków jej modyfikować. Interfejs StateManager Klasa aktora przypomina słownika. Zawsze powinny modelować struktury danych reprezentujący stanu aktora, na podstawie tego słownika.
 
### <a name="correctly-manage-the-actors-life-cycle"></a>Poprawnie Zarządzanie cyklem życia aktora
Należy mieć jednoznaczne zasady dotyczące zarządzania rozmiar stanu w każdej partycji usługi aktora. Usługa aktora powinni mieć stałą liczbą aktorów i wykorzystać ją w miarę możliwości. Jeśli nieustannie tworzy nowe podmioty, należy je usunąć, po zakończeniu pracy. Struktura aktora przechowuje niektóre metadane dotyczące każdego aktora, który istnieje. Usuwanie stanu aktora nie powoduje usunięcia metadanych dotyczących tego aktora. Należy usunąć aktora (zobacz [usuwanie aktorów i ich stanu](service-fabric-reliable-actors-lifecycle.md#manually-deleting-actors-and-their-state)) do usunięcia wszystkich informacji o jej przechowywanych w systemie. Jako dodatkowe sprawdzenie powinien zapytań usługi aktora (zobacz [wyliczanie aktorów](service-fabric-reliable-actors-enumerate.md)) raz na jakiś czas, aby upewnić się, liczbę uczestników znajdują się w oczekiwanym zakresem.
 
Jeśli kiedykolwiek coraz częściej rozmiar oczekiwany rozmiar pliku bazy danych usługi aktora, upewnij się, że postępujesz zgodnie z poprzednim wytycznych. Jeśli postępujesz zgodnie z tymi wytycznymi i nadal są bazy danych problemów z rozmiarem plików, wykonaj następujące czynności [Otwórz bilet pomocy technicznej](service-fabric-support.md) z zespołem produktu, aby uzyskać pomoc.

## <a name="next-steps"></a>Kolejne kroki

Stan, który jest przechowywany w elementach Reliable Actors, trzeba go serializować przed zapisane na dysku i replikowane w celu zapewnienia wysokiej dostępności. Dowiedz się więcej o [serializacja typów aktora](service-fabric-reliable-actors-notes-on-actor-type-serialization.md).

Następnie Dowiedz się więcej o [aktora Diagnostyka i monitorowanie wydajności](service-fabric-reliable-actors-diagnostics.md).
