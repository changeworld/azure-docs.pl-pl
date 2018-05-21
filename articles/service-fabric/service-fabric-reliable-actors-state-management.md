---
title: Zarządzanie stanem Reliable Actors | Dokumentacja firmy Microsoft
description: Opisuje sposób Reliable Actors stanu jest zarządzany, utrwalenia i replikacji wysokiej dostępności.
services: service-fabric
documentationcenter: .net
author: vturecek
manager: timlt
editor: ''
ms.assetid: 37cf466a-5293-44c0-a4e0-037e5d292214
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/02/2017
ms.author: vturecek
ms.openlocfilehash: 3cab4d87eacc7bce17da64cda213086c262179a8
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/16/2018
---
# <a name="reliable-actors-state-management"></a>Zarządzanie stanem podmiotów niezawodnej
Reliable Actors są jednowątkowych obiektów hermetyzacji zarówno logiki i stanu. Ponieważ podmioty są uruchamiane na niezawodne usługi, ich niezawodnie zachowują stan, przy użyciu tego samego stanu trwałego i mechanizmów replikacji. W ten sposób złośliwych użytkowników nie utracili ich stanu po awariach po ponownej aktywacji po wyrzucanie elementów bezużytecznych lub po przeniesieniu ich między węzłami w klastrze równoważenia zasobów lub uaktualnień.

## <a name="state-persistence-and-replication"></a>Trwałość stanu i replikacji
Uwzględniane są wszystkie Reliable Actors *stateful* ponieważ każde wystąpienie aktora jest mapowany na unikatowy identyfikator. Oznacza to, że ten sam identyfikator aktora powtarzane wywołania są kierowane do tego samego wystąpienia aktora. W systemie bezstanowych natomiast wywołań klienta nie ma gwarancji być kierowane do tego samego serwera zawsze. Z tego powodu usługi aktora są zawsze usług stanowych.

Mimo że podmioty są traktowane jako wartość, która oznacza to, że ich musi niezawodne przechowywanie stanu. Złośliwych użytkowników można wybrać poziom trwałości stanu i replikacji na podstawie ich danych wymagania dotyczące magazynu:

* **Stan utrwalony**: stan jest zachowywany na dysku i jest replikowany do co najmniej trzech replik. Stan utrwalony jest najbardziej niezawodna opcji magazynu stanu, gdzie stan może się powtarzać, za pośrednictwem awarii całego klastra.
* **Stan volatile**: stan jest replikowana do co najmniej trzech replik i tylko przechowywany w pamięci. Stan volatile gwarantuje odporność względem awarii węzła i niepowodzenie aktora oraz podczas uaktualniania i równoważenia zasobów. Jednak stan nie jest zachowywany na dysku. Jeśli więc wszystkie repliki zostaną utracone na raz, stan utracone również.
* **Nie stanu utrwalonego**: stan nie jest replikowany lub zapisywane na dysku, używaj tylko dla osób, które nie wymagają do zarządzania stanem niezawodnie.

Każdy poziom trwałości jest po prostu inną *dostawcy stanu* i *replikacji* konfiguracji usługi. Określa, czy stan jest zapisywany na dysku jest zależna od dostawcy stanu — składnik w niezawodnej usługi, która przechowuje stan. Zależy od liczby replikami usługa jest wdrażana z replikacji. Podobnie jak w przypadku niezawodne usługi dostawcy stanu i liczby replik można łatwo skonfigurować ręcznie. Framework aktora udostępnia atrybut, gdy używany na aktora, automatycznie wybiera domyślny dostawca stanu i automatycznie generuje ustawienia liczby replik osiągnięcie jednego z tych trzech ustawień trwałości. Atrybut StatePersistence nie jest dziedziczone przez klasy pochodnej, podać jego poziom StatePersistence każdego typu aktora.

### <a name="persisted-state"></a>Stan utrwalony
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
To ustawienie powoduje użycie dostawcy stanu, która przechowuje dane na dysku i automatycznie ustawia liczbę repliki usługi do 3.

### <a name="volatile-state"></a>Stan nietrwałe
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
To ustawienie korzysta z dostawcy stanu w pamięci wyłącznie- i ustawia liczby replik 3.

### <a name="no-persisted-state"></a>Nie stanu utrwalonego
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
To ustawienie korzysta z dostawcy stanu w pamięci — tylko i ustawia liczbę replik równą 1.

### <a name="defaults-and-generated-settings"></a>Wygenerowany ustawienia i wartości domyślnych
Jeśli używasz `StatePersistence` atrybutu, dostawcy stanu jest automatycznie wybrana w czasie wykonywania podczas uruchamiania usługi aktora. Liczba replik, jednak jest ustawiony w czasie kompilacji za pomocą narzędzi Visual Studio aktora kompilacji. Narzędzia kompilacji automatycznego generowania *usługi domyślnej* usługi aktora w pliku ApplicationManifest.xml. Parametry są tworzone dla **rozmiar zestawu replik min** i **rozmiar zestawu replik docelowej**.

Te parametry można zmienić ręcznie. Ale zawsze `StatePersistence` zmiany atrybutu, parametry zostaną ustawione na wartości domyślne rozmiar zestawu replik dla wybranego `StatePersistence` atrybutu zastąpienie poprzedniej wartości. Innymi słowy, są ustawiane w pliku ServiceManifest.xml wartości *tylko* zastąpione w czasie kompilacji zmiana `StatePersistence` wartość atrybutu.

```xml
<ApplicationManifest xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="Application12Type" ApplicationTypeVersion="1.0.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
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
Każde wystąpienie aktora ma własną Menedżer stanu: struktura typu słownika danych, która w niezawodny sposób przechowuje par klucz/wartość. Menedżer stanów jest otokę dostawcy stanu. Można go użyć do przechowywania danych, niezależnie od tego, które jest używane ustawienie trwałości. Nie ma żadnej gwarancji, że można zmienić uruchomionej usługi aktora z ustawienie volatile (w pamięci —) stanie tylko do ustawienia stanu utrwalonego za pomocą uaktualnienia stopniowego przy zachowaniu danych. Istnieje możliwość zmiany liczby replik dla uruchomionej usługi.

Menedżer stanu klucze muszą być ciągami. Wartości są ogólne i mogą być dowolnego typu, łącznie z niestandardowych typów. Wartości przechowywane w Menedżerze stanu musi być kontraktu danych, które można serializować, ponieważ mogą być przesyłane przez sieć do innych węzłów podczas replikacji i mogą być zapisywane na dysku, w zależności od ustawienia trwałości stanu aktora.

Menedżer stanu przedstawia stan podobne do tych znaleziony w słowniku niezawodnej zarządzania typowe metody słownika.

Przykłady zarządzania stanu aktora odczytu [dostępu, zapisywania i usuwania stanu Reliable Actors](service-fabric-reliable-actors-access-save-remove-state.md).

## <a name="best-practices"></a>Najlepsze praktyki
Poniżej przedstawiono niektóre sugerowane rozwiązania i wskazówki dotyczące rozwiązywania problemów związanych z zarządzaniem Nazwa stanu aktora.

### <a name="make-the-actor-state-as-granular-as-possible"></a>Wprowadź stanu aktora szczegółowe, jak to możliwe
Jest to szczególnie ważne, wydajności i użycia zasobów aplikacji. Zawsze, gdy istnieje zapisu/zaktualizowanie "o nazwie stanu" aktora, całe wartością odpowiadającą temu Państwu"o nazwie" jest serializowany i przesyłane przez sieć w replikach pomocniczych.  Pomocnicze zapisu na dysku lokalnym i odpowiedzi do repliki podstawowej. Gdy podstawowy otrzyma potwierdzeń z kworum replik pomocniczych, zapisuje stan do jego dysku lokalnym. Na przykład załóżmy, że wartość jest klasa, która ma 20 elementów członkowskich i o rozmiarze 1 MB. Nawet wtedy, gdy jeden z elementów członkowskich klasy, które jest tylko zmodyfikowane rozmiarze 1 KB, koniec się zwracając koszt serializacji i zapisy sieci i dysku do pełnej 1 MB. Podobnie jeśli wartość jest kolekcji (na przykład listy, tablicy lub słownika), płacisz koszt pełną kolekcję nawet wtedy, gdy jeden z jego elementów członkowskich zmodyfikować. Interfejs zabezpiecza klasy aktora jest podobne do słownika. Należy zawsze modelu struktury danych reprezentujący stanu aktora u góry tego słownika.
 
### <a name="correctly-manage-the-actors-life-cycle"></a>Poprawnie Zarządzanie cyklem życia aktora
Należy dobrze wyczyść zasad dotyczących zarządzania rozmiar stanu w każdej partycji usługi aktora. Usługi aktora powinny mieć stała liczba uczestników i użyj ich ponownie bardzo, jak to możliwe. Jeśli stale utworzyć nowych podmiotów, należy je usunąć po zakończeniu pracy. Framework aktora przechowuje niektóre metadane dotyczące każdego aktora, czy istnieje. Usunięcie stanu aktora nie powoduje usunięcia metadane dotyczące tego aktora. Należy usunąć aktora (zobacz [Usuwanie złośliwych użytkowników i ich stan](service-fabric-reliable-actors-lifecycle.md#manually-deleting-actors-and-their-state)) do usunięcia wszystkich informacji o ona przechowywana w systemie. Jako dodatkowe wyboru, należy wysłać zapytania do usługi aktora (zobacz [wyliczania podmiotów](service-fabric-reliable-actors-platform.md)) okazjonalnie, aby upewnić się, liczba uczestników są oczekiwanego zakresu.
 
Jeśli kiedykolwiek widać, że rozmiar pliku bazy danych usługi aktora zwiększa się poza oczekiwanym rozmiarem, upewnij się, po poprzednim wytyczne. Jeśli są następujące wskazówki i są nadal bazy danych problemów z rozmiarem plików, należy [Otwórz bilet pomocy technicznej](service-fabric-support.md) zespołowi produktu, aby uzyskać pomoc.

## <a name="next-steps"></a>Kolejne kroki

Stan, który jest przechowywany w Reliable Actors musi być serializowany przed zapisany na dysku i replikowany wysokiej dostępności. Dowiedz się więcej o [serializacji typu aktora](service-fabric-reliable-actors-notes-on-actor-type-serialization.md).

Następnie Dowiedz się więcej o [aktora Diagnostyka i monitorowanie wydajności](service-fabric-reliable-actors-diagnostics.md).
