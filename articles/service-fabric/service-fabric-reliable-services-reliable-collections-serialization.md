---
title: Serializacja obiektów niezawodnej kolekcji
description: Dowiedz się więcej o serializacji obiektów niezawodnych kolekcji usługi Azure Service Fabric, w tym o domyślnej strategii i o tym, jak zdefiniować niestandardową serializację."
ms.topic: conceptual
ms.date: 5/8/2017
ms.openlocfilehash: 666e1bb45a9c75ee143f15a0d871d6ae1408eca9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75639551"
---
# <a name="reliable-collection-object-serialization-in-azure-service-fabric"></a>Serializacja obiektów niezawodnej kolekcji w sieci szkieletowej usług Azure
Reliable Collections replikować i utrwalać swoje elementy, aby upewnić się, że są trwałe w przypadku awarii komputera i przerw w zasilaniu.
Zarówno do replikowania i utrwalania elementów, niezawodne kolekcje trzeba je serializować.

Reliable Collections' uzyskać odpowiedni serializator dla danego typu z Menedżera stanu niezawodne.
Menedżer stanu niezawodne zawiera wbudowane serializatory i umożliwia niestandardowych serializatorów, które mają być zarejestrowane dla danego typu.

## <a name="built-in-serializers"></a>Wbudowane serializatory

Menedżer stanu niezawodne zawiera wbudowany serializator dla niektórych typowych typów, dzięki czemu mogą być serializowane wydajnie efektywnie przez domyślnie. W przypadku innych typów Menedżer stanu niezawodnego powraca do używania [datacontractSerializer](https://msdn.microsoft.com/library/system.runtime.serialization.datacontractserializer(v=vs.110).aspx).
Wbudowane serializatory są bardziej wydajne, ponieważ wiedzą, że ich typy nie można zmienić i nie trzeba dołączać informacje o typie, takich jak jego nazwa typu.

Reliable State Manager ma wbudowany serializator dla następujących typów: 
- Guid (identyfikator GUID)
- bool
- byte
- sbyte
- bajt[]
- char
- ciąg
- decimal
- double
- float
- int
- uint
- long
- ulong
- short
- ushort

## <a name="custom-serialization"></a>Serializacja niestandardowa

Niestandardowe serializatory są często używane do zwiększenia wydajności lub szyfrowania danych za pomocą sieci owej i na dysku. Między innymi z niestandardowych serializatorów są powszechnie bardziej wydajne niż serializator ogólny, ponieważ nie trzeba serializować informacje o typie. 

[IReliableStateManager.TryAddStateSerializer\<T>](https://docs.microsoft.com/dotnet/api/microsoft.servicefabric.data.ireliablestatemanager.tryaddstateserializer) służy do rejestrowania niestandardowego serializatora dla danego typu T. Ta rejestracja powinna mieć miejsce w budowie StatefulServiceBase, aby upewnić się, że przed rozpoczęciem odzyskiwania wszystkie niezawodne kolekcje mają dostęp do odpowiedniego serializatora, aby odczytać ich utrwalone dane.

```csharp
public StatefulBackendService(StatefulServiceContext context)
  : base(context)
  {
    if (!this.StateManager.TryAddStateSerializer(new OrderKeySerializer()))
    {
      throw new InvalidOperationException("Failed to set OrderKey custom serializer");
    }
  }
```

> [!NOTE]
> Niestandardowe serializatory mają pierwszeństwo przed wbudowanymi serializatorami. Na przykład, gdy niestandardowy serializator dla int jest zarejestrowany, jest używany do serializacji liczby całkowite zamiast wbudowanego serializatora dla int.

### <a name="how-to-implement-a-custom-serializer"></a>Jak zaimplementować niestandardowy serializator

Niestandardowy serializator musi zaimplementować interfejs [IStateSerializer\<T>.](https://docs.microsoft.com/dotnet/api/microsoft.servicefabric.data.istateserializer-1)

> [!NOTE]
> IStateSerializer\<T> zawiera przeciążenie dla zapisu i odczytu, który przyjmuje w dodatkowej wartości T o nazwie podstawowej. Ten interfejs API jest przeznaczony do serializacji różnicowej. Obecnie funkcja serializacji różnicowej nie jest narażona. W związku z tym te dwa przeciążenia nie są wywoływane, dopóki serializacji różnicowej jest narażony i włączony.

Poniżej znajduje się przykładowy typ niestandardowy o nazwie OrderKey, który zawiera cztery właściwości

```csharp
public class OrderKey : IComparable<OrderKey>, IEquatable<OrderKey>
{
    public byte Warehouse { get; set; }

    public short District { get; set; }

    public int Customer { get; set; }

    public long Order { get; set; }

    #region Object Overrides for GetHashCode, CompareTo and Equals
    #endregion
}
```

Poniżej przedstawiono przykład implementacji IStateSerializer\<OrderKey>.
Należy zauważyć, że odczytu i zapisu przeciążenia, które mają w baseValue, wywołać ich odpowiednie przeciążenie dla zgodności przesyłania dalej.

```csharp
public class OrderKeySerializer : IStateSerializer<OrderKey>
{
  OrderKey IStateSerializer<OrderKey>.Read(BinaryReader reader)
  {
      var value = new OrderKey();
      value.Warehouse = reader.ReadByte();
      value.District = reader.ReadInt16();
      value.Customer = reader.ReadInt32();
      value.Order = reader.ReadInt64();

      return value;
  }

  void IStateSerializer<OrderKey>.Write(OrderKey value, BinaryWriter writer)
  {
      writer.Write(value.Warehouse);
      writer.Write(value.District);
      writer.Write(value.Customer);
      writer.Write(value.Order);
  }
  
  // Read overload for differential de-serialization
  OrderKey IStateSerializer<OrderKey>.Read(OrderKey baseValue, BinaryReader reader)
  {
      return ((IStateSerializer<OrderKey>)this).Read(reader);
  }

  // Write overload for differential serialization
  void IStateSerializer<OrderKey>.Write(OrderKey baseValue, OrderKey newValue, BinaryWriter writer)
  {
      ((IStateSerializer<OrderKey>)this).Write(newValue, writer);
  }
}
```

## <a name="upgradability"></a>Możliwość modernizacji
W [uaktualnieniu aplikacji stopniowej](service-fabric-application-upgrade.md)uaktualnienie jest stosowane do podzbioru węzłów, jednej domeny uaktualnienia naraz. Podczas tego procesu niektóre domeny uaktualnienia będą w nowszej wersji aplikacji, a niektóre domeny uaktualnienia będą w starszej wersji aplikacji. Podczas wdrażania nowa wersja aplikacji musi być w stanie odczytać starą wersję danych, a stara wersja aplikacji musi być w stanie odczytać nową wersję danych. Jeśli format danych nie jest zgodny z danymi do przodu i do tyłu, uaktualnienie może zakończyć się niepowodzeniem lub co gorsza, dane mogą zostać utracone lub uszkodzone.

Jeśli używasz wbudowanego serializatora, nie musisz się martwić o zgodność.
Jednak jeśli używasz niestandardowego serializatora lub DataContractSerializer, dane muszą być nieskończenie wstecz i zgodne z dalej.
Innymi słowy każda wersja serializatora musi być w stanie serializować i de-serializacji dowolnej wersji typu.

Użytkownicy kontraktów danych powinni przestrzegać dobrze zdefiniowanych reguł przechowywania wersji do dodawania, usuwania i zmieniania pól. Data Contract ma również obsługę obsługi nieznanych pól, podłączanie do procesu serializacji i deserializacji i radzenie sobie z dziedziczeniem klas. Aby uzyskać więcej informacji, zobacz [Korzystanie z kontraktu danych](https://msdn.microsoft.com/library/ms733127.aspx).

Użytkownicy niestandardowego serializatora należy stosować się do wytycznych serializatora, których używają, aby upewnić się, że jest wstecz i do przodu zgodne.
Typowym sposobem obsługi wszystkich wersji jest dodawanie informacji o rozmiarze na początku i dodawanie tylko właściwości opcjonalnych.
W ten sposób każda wersja może odczytać jak najwięcej i przeskoczyć pozostałą część strumienia.

## <a name="next-steps"></a>Następne kroki
  * [Serializacja i uaktualnianie](service-fabric-application-upgrade-data-serialization.md)
  * [Informacje dla dewelopera dotyczące niezawodnych kolekcji](https://msdn.microsoft.com/library/azure/microsoft.servicefabric.data.collections.aspx)
  * [Uaktualnianie aplikacji przy użyciu programu Visual Studio](service-fabric-application-upgrade-tutorial.md) przeprowadzi Cię przez uaktualnienie aplikacji przy użyciu programu Visual Studio.
  * [Uaktualnianie aplikacji przy użyciu programu Powershell](service-fabric-application-upgrade-tutorial-powershell.md) przeprowadzi Cię przez uaktualnienie aplikacji przy użyciu programu PowerShell.
  * Kontroluj sposób uaktualniania aplikacji przy użyciu [parametrów uaktualnienia](service-fabric-application-upgrade-parameters.md).
  * Dowiedz się, jak korzystać z zaawansowanych funkcji podczas uaktualniania aplikacji, odwołując się do [tematów zaawansowanych](service-fabric-application-upgrade-advanced.md).
  * Rozwiąż typowe problemy w uaktualnieniach aplikacji, odwołując się do kroków [rozwiązywania problemów z uaktualnieniami aplikacji](service-fabric-application-upgrade-troubleshooting.md).
