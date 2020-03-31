---
title: Zarządzanie państwem wiarygodnych aktorów
description: W tym artykule opisano, jak stan niezawodne podmioty jest zarządzany, utrwalony i replikowane dla wysokiej dostępności.
author: vturecek
ms.topic: conceptual
ms.date: 11/02/2017
ms.author: vturecek
ms.openlocfilehash: 9962d4333e458243670d1005ad2ccfbc0bb7c92a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75348912"
---
# <a name="reliable-actors-state-management"></a>Zarządzanie państwem wiarygodnych aktorów
Reliable Actors są jednowątkowe obiekty, które można hermetyzować zarówno logiki i stanu. Ponieważ podmioty działają na niezawodne usługi, mogą niezawodnie utrzymywać stan przy użyciu tych samych mechanizmów trwałości i replikacji. W ten sposób aktorzy nie tracą ich stanu po awarii, po reaktywacji po wyrzucaniu elementów bezużytecznych lub gdy są one przenoszone między węzłami w klastrze z powodu równoważenia zasobów lub uaktualnień.

## <a name="state-persistence-and-replication"></a>Trwałość i replikacja stanu
Wszystkie niezawodne aktorzy są uważane za *stanowe,* ponieważ każde wystąpienie aktora mapuje unikatowy identyfikator. Oznacza to, że powtarzające się wywołania tego samego identyfikatora aktora są kierowane do tego samego wystąpienia aktora. W systemie bezstanowym, z drugiej strony wywołania klienta nie są gwarantowane być kierowane do tego samego serwera za każdym razem. Z tego powodu usługi aktora są zawsze usługi stanowe.

Mimo że podmioty są uważane za stanowe, nie oznacza to, że muszą przechowywać stan niezawodnie. Aktorzy mogą wybrać poziom trwałości stanu i replikacji na podstawie ich wymagań dotyczących przechowywania danych:

* **Utrwalony stan:** Stan jest utrwalony na dysku i jest replikowany do co najmniej trzech replik. Utrwalony stan jest najbardziej trwałe opcji magazynu stanu, gdzie stan może się utrzymać przez awarię klastra pełne.
* **Stan lotny:** Stan jest replikowany do trzech lub więcej replik i przechowywany tylko w pamięci. Stan nietrwały zapewnia odporność na błąd węzła i awarii aktora, a podczas uaktualnień i równoważenia zasobów. Jednak stan nie jest utrwalony na dysku. Więc jeśli wszystkie repliki zostaną utracone na raz, stan jest również tracony.
* **Nie utrwalone stan:** Stan nie jest replikowany lub zapisywane na dysku, tylko używać dla podmiotów, które nie muszą utrzymywać stan niezawodnie.

Każdy poziom trwałości jest po prostu inny *dostawca stanu* i konfiguracji *replikacji* usługi. To, czy stan jest zapisywany na dysku, zależy od dostawcy stanu — składnika w niezawodnej usłudze, która przechowuje stan. Replikacja zależy od liczby replik, z jakim jest wdrażana usługa. Podobnie jak w przypadku usług niezawodnych, zarówno dostawca stanu, jak i liczba replik można łatwo ustawić ręcznie. Struktura aktora udostępnia atrybut, który, gdy jest używany w aktora, automatycznie wybiera domyślnego dostawcy stanu i automatycznie generuje ustawienia dla liczby replik, aby osiągnąć jeden z tych trzech ustawień trwałości. Atrybut StatePersistence nie jest dziedziczona przez klasę pochodną, każdy typ aktora musi podać swój poziom StatePersistence.

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
To ustawienie używa dostawcy stanu, który przechowuje dane na dysku i automatycznie ustawia liczbę replik usługi na 3.

### <a name="volatile-state"></a>Stan lotny
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
To ustawienie używa dostawcy stanu tylko w pamięci i ustawia liczbę replik na 3.

### <a name="no-persisted-state"></a>Brak stanu utrwalonych
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
To ustawienie używa dostawcy stanu tylko w pamięci i ustawia liczbę replik na 1.

### <a name="defaults-and-generated-settings"></a>Ustawienia domyślne i wygenerowane
Podczas korzystania z `StatePersistence` atrybutu, dostawca stanu jest automatycznie wybierany dla Ciebie w czasie wykonywania po uruchomieniu usługi aktora. Liczba replik jest jednak ustawiana w czasie kompilacji przez narzędzia kompilacji aktora programu Visual Studio. Narzędzia kompilacji automatycznie generują *domyślną usługę* dla usługi aktora w pliku ApplicationManifest.xml. Parametry są tworzone dla **minimalnego rozmiaru zestawu replik** i **docelowego zestawu replik**.

Parametry te można zmienić ręcznie. Jednak za `StatePersistence` każdym razem, gdy atrybut jest zmieniany, parametry są `StatePersistence` ustawiane na domyślne wartości rozmiaru zestawu replik dla wybranego atrybutu, zastępując wszystkie poprzednie wartości. Innymi słowy wartości ustawione w ServiceManifest.xml są zastępowane *tylko* w czasie `StatePersistence` kompilacji po zmianie wartości atrybutu.

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

## <a name="state-manager"></a>Kierownik państwa
Każde wystąpienie aktora ma swój własny menedżer stanu: strukturę danych podobną do słownika, która niezawodnie przechowuje pary klucz/wartość. Menedżer stanu jest otoki wokół dostawcy stanu. Można go używać do przechowywania danych, niezależnie od tego, które ustawienie trwałości jest używane. Nie zapewnia żadnych gwarancji, że uruchomionej usługi aktora można zmienić z ustawienia stanu volatile (tylko w pamięci) do ustawienia stanu utrwalonego za pośrednictwem uaktualnienia stopniowego przy jednoczesnym zachowaniu danych. Jednak istnieje możliwość zmiany liczby replik dla uruchomionej usługi.

Klucze menedżera stanu muszą być ciągami. Wartości są ogólne i mogą być dowolnego typu, w tym typów niestandardowych. Wartości przechowywane w Menedżerze stanu muszą być serializowalne dla kontraktu danych, ponieważ mogą być przesyłane przez sieć do innych węzłów podczas replikacji i mogą być zapisywane na dysku, w zależności od ustawienia trwałości stanu aktora.

Menedżer stanu udostępnia typowe metody słownika do zarządzania stanem, podobne do tych znalezionych w reliable dictionary.

Przykłady zarządzania stanem aktora, przeczytaj [access, zapisz i usuń stan Niezawodni aktorzy](service-fabric-reliable-actors-access-save-remove-state.md).

## <a name="best-practices"></a>Najlepsze rozwiązania
Oto kilka sugerowanych praktyk i wskazówek dotyczących rozwiązywania problemów dotyczących zarządzania stanem aktora.

### <a name="make-the-actor-state-as-granular-as-possible"></a>Spraw, aby stan aktora był jak najbardziej szczegółowy
Ma to kluczowe znaczenie dla wydajności i użycia zasobów aplikacji. Za każdym razem, gdy istnieje dowolny zapis/aktualizacja do "nazwanego stanu" aktora, cała wartość odpowiadająca tej "nazwanej stanie" jest serializowana i wysyłana przez sieć do replik pomocniczych.  Pomocnicze zapisują go na dysku lokalnym i odpowiadają z powrotem do repliki podstawowej. Gdy podstawowy odbiera potwierdzenia z kworum replik pomocniczych, zapisuje stan na swoim dysku lokalnym. Załóżmy na przykład, że wartość jest klasą, która ma 20 elementów członkowskich i rozmiar 1 MB. Nawet jeśli zmodyfikowano tylko jeden z członków klasy, który ma rozmiar 1 KB, w końcu płacisz koszt serializacji i zapisów sieciowych i dyskowych dla pełnego 1 MB. Podobnie jeśli wartość jest kolekcji (takich jak lista, tablicy lub słownika), należy zapłacić koszt pełnej kolekcji, nawet jeśli zmodyfikować jeden z jej członków. Interfejs StateManager klasy aktora jest jak słownik. Zawsze należy modelować strukturę danych reprezentującą stan aktora na górze tego słownika.
 
### <a name="correctly-manage-the-actors-life-cycle"></a>Prawidłowe zarządzanie cyklem życia aktora
Należy mieć jasne zasady dotyczące zarządzania rozmiarem stanu w każdej partycji usługi aktora. Usługa aktora powinna mieć ustaloną liczbę aktorów i używać ich ponownie w miarę możliwości. Jeśli stale tworzysz nowe podmioty, musisz je usunąć po zakończeniu ich pracy. Struktura aktora przechowuje niektóre metadane dotyczące każdego aktora, który istnieje. Usunięcie całego stanu aktora nie powoduje usunięcia metadanych dotyczących tego aktora. Należy usunąć aktora (zobacz [usuwanie aktorów i ich stan),](service-fabric-reliable-actors-lifecycle.md#manually-deleting-actors-and-their-state)aby usunąć wszystkie informacje o nim przechowywane w systemie. Jako dodatkowe sprawdzenie, należy zbadać usługi aktora (zobacz [wyliczanie aktorów)](service-fabric-reliable-actors-enumerate.md)raz na jakiś czas, aby upewnić się, że liczba aktorów są w oczekiwanym zakresie.
 
Jeśli kiedykolwiek zobaczysz, że rozmiar pliku bazy danych usługi aktora wzrasta poza oczekiwany rozmiar, upewnij się, że są zgodne z poprzednimi wytycznymi. Jeśli przestrzegasz tych wytycznych i nadal są problemy z rozmiarem pliku bazy danych, należy [otworzyć bilet pomocy technicznej](service-fabric-support.md) z zespołem produktu, aby uzyskać pomoc.

## <a name="next-steps"></a>Następne kroki

Stan, który jest przechowywany w reliable actors musi być serializowany przed jego zapisane na dysku i replikowane dla wysokiej dostępności. Dowiedz się więcej [o serializacji typu aktora](service-fabric-reliable-actors-notes-on-actor-type-serialization.md).

Następnie dowiedz się więcej o [diagnostyce aktora i monitorowaniu wydajności](service-fabric-reliable-actors-diagnostics.md).
