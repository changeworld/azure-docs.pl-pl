---
title: Niezawodne serializacji obiektu kolekcji w sieci szkieletowej usług Azure | Dokumentacja firmy Microsoft
description: Azure serializacji obiektu kolekcji niezawodnej sieci szkieletowej usług
services: service-fabric
documentationcenter: .net
author: mcoskun
manager: timlt
editor: masnider,rajak
ms.assetid: 9d35374c-2d75-4856-b776-e59284641956
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: required
ms.date: 5/8/2017
ms.author: mcoskun
ms.openlocfilehash: b02d8924749abb0e2fe815b555d55767bf1e5cc1
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/16/2018
---
# <a name="reliable-collection-object-serialization-in-azure-service-fabric"></a>Niezawodne serializacji obiektu kolekcji w sieci szkieletowej usług Azure
Niezawodne kolekcje replikacji i utrwalić swoich elementów, aby upewnić się, że są trwałe na błędy maszyn i awarie zasilania.
Do replikacji i elementy będą się powtarzać, niezawodne kolekcje muszą serializować je.

Kolekcje niezawodnej uzyskanie odpowiedniego programu szeregującego dla danego typu niezawodnej Menedżer stanu.
Menedżer stanu niezawodny zawiera wbudowane serializatorów i umożliwia niestandardowych serializatorów rejestracji dla danego typu.

## <a name="built-in-serializers"></a>Wbudowane serializatorów

Menedżer stanu niezawodny obejmuje wbudowane serializatora dla niektórych typowych tak, aby serializować wydajnie domyślnie. Dla innych typów niezawodnej Menedżer stanu powraca do użycia [DataContractSerializer](https://msdn.microsoft.com/library/system.runtime.serialization.datacontractserializer(v=vs.110).aspx).
Wbudowane serializatorów są bardziej wydajne, ponieważ wiedzieli, nie można zmienić ich typów i nie muszą obejmować informacje o typie, takie jak jego nazwa typu.

Menedżer stanu niezawodny ma wbudowane serializatora dla następujących typów: 
- Identyfikator GUID
- wartość logiczna
- bajt
- sbyte —
- byte[]
- char
- ciąg
- Decimal
- double
- liczba zmiennoprzecinkowa
- int
- uint
- dł.
- ulong
- krótki
- ushort

## <a name="custom-serialization"></a>Niestandardowej serializacji

Niestandardowe serializatorów są często używane w celu zwiększenia wydajności lub do szyfrowania danych przez sieć oraz na dysku. Wśród innych powodów serializatorów niestandardowe są często bardziej efektywne niż ogólny serializator, ponieważ nie potrzebują do serializacji informacje o typie. 

[IReliableStateManager.TryAddStateSerializer<T> ](https://docs.microsoft.com/dotnet/api/microsoft.servicefabric.data.ireliablestatemanager.tryaddstateserializer--1?Microsoft_ServiceFabric_Data_IReliableStateManager_TryAddStateSerializer__1_Microsoft_ServiceFabric_Data_IStateSerializer___0__) służy do rejestrowania niestandardowego programu szeregującego dla danego typu T. Rejestracja powinno się zdarzyć w konstrukcji StatefulServiceBase zapewnienie przed rozpoczęciem odzyskiwania, wszystkie kolekcje niezawodny dostęp do odpowiednich serializatora do odczytania ich danych.

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
> Niestandardowe serializatorów są pierwszeństwo nad serializatorów wbudowanych. Na przykład po zarejestrowaniu niestandardowego programu szeregującego dla int on jest używany do serializacji liczb całkowitych zamiast wbudowanego serializatora dla int.

### <a name="how-to-implement-a-custom-serializer"></a>Jak zaimplementować serializatora niestandardowego

Serializatora niestandardowego należy zaimplementować [IStateSerializer<T> ](https://docs.microsoft.com/dotnet/api/microsoft.servicefabric.data.istateserializer-1) interfejsu.

> [!NOTE]
> IStateSerializer<T> zawiera przeciążenia dla zapisu i odczytu, która przyjmuje w dodatkowych T jako wartości podstawowej. Ten interfejs API jest różnicowej serializacji. Obecnie funkcja różnicowej serializacji nie jest widoczne. W związku z tym te dwa przeciążenia nie są nazywane, dopóki różnicowej serializacji jest widoczne i włączone.

Poniżej przedstawiono przykład typu niestandardowego o nazwie OrderKey, który zawiera cztery właściwości

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

Poniżej przedstawiono przykład stosowania IStateSerializer<OrderKey>.
Należy pamiętać, że odczytywanie i zapisywanie przeciążeń, które przyjmują w baseValue, wywołania ich odpowiednich przeciążenia dla zgodności wyszukiwanie do przodu.

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

## <a name="upgradability"></a>Możliwość
W [aplikacji uaktualnienia stopniowego](service-fabric-application-upgrade.md), uaktualnienie ma zostać zastosowane do podzbioru węzłów, domeny uaktualnienia pojedynczo. W trakcie tego procesu będzie niektórych domen uaktualnienia w nowszej wersji aplikacji, a niektóre domen uaktualnienia będą realizowane w starszej wersji aplikacji. Podczas wdrożenia nowa wersja aplikacji musi mieć możliwość odczytu starą wersję danych, a starą wersję aplikacji musi mieć możliwość odczytu nowej wersji danych. Jeśli format danych jest niezgodny z przodu i do tyłu, uaktualnienie może się nie powieść lub gorsza, może utraty lub uszkodzenia danych.

Jeśli korzystasz z wbudowanych serializator, nie masz martwić się o zgodności.
Jednak jeśli używasz niestandardowego programu szeregującego lub elementu DataContractSerializer, dane muszą być nieograniczonej zgodne przodu i do tyłu.
Innymi słowy każda wersja programu szeregującego musi być możliwe do serializacji i deserializacji dowolnej wersji tego typu.

Użytkownicy kontraktu danych powinien być zgodny reguły dobrze zdefiniowany kontroli wersji, dodawanie, usuwanie i zmiana pola. Kontrakt danych ma również obsługę zajmujących się nieznany pól, przechwytywanie do procesu serializacji i deserializacji i zajmujących się dziedziczenia klas. Aby uzyskać więcej informacji, zobacz [kontraktu danych przy użyciu](https://msdn.microsoft.com/library/ms733127.aspx).

Użytkownicy niestandardowego programu szeregującego powinien zgodne z wytycznymi serializator, którego używają Sprawdź jest Wstecz i przekazuje zgodne.
Typowy sposób obsługi wszystkich wersji jest dodawania informacji o rozmiarze na początku i tylko właściwości opcjonalnych.
W ten sposób każdej wersji może odczytywać znacznie można i przejść w pozostałej części strumienia.

## <a name="next-steps"></a>Kolejne kroki
  * [Serializacja i uaktualniania](service-fabric-application-upgrade-data-serialization.md)
  * [Dokumentacja dla deweloperów niezawodnej kolekcji](https://msdn.microsoft.com/library/azure/microsoft.servicefabric.data.collections.aspx)
  * [Uaktualnianie aplikacji za pomocą Visual Studio](service-fabric-application-upgrade-tutorial.md) przeprowadzi Cię przez proces uaktualnienia aplikacji przy użyciu programu Visual Studio.
  * [Uaktualnienie z aplikacji przy użyciu programu Powershell](service-fabric-application-upgrade-tutorial-powershell.md) przeprowadzi Cię przez proces uaktualnienia aplikacji przy użyciu programu PowerShell.
  * Kontrolowanie sposobu uaktualnienia aplikacji przy użyciu [uaktualnienia parametrów](service-fabric-application-upgrade-parameters.md).
  * Dowiedz się, jak korzystać z zaawansowanych funkcji podczas uaktualniania aplikacji, odwołując się do [Tematy zaawansowane](service-fabric-application-upgrade-advanced.md).
  * Rozwiązywania typowych problemów w uaktualnień aplikacji, korzystając z procedury opisanej w [Rozwiązywanie problemów z uaktualnieniami aplikacji](service-fabric-application-upgrade-troubleshooting.md).
