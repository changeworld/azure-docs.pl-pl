---
title: Serializacja elementu Reliable Collection obiektu w usłudze Azure Service Fabric | Dokumentacja firmy Microsoft
description: Azure Service Fabric elementy Reliable Collections odpowiedzialność za serializację obiektu
services: service-fabric
documentationcenter: .net
author: aljo-microsoft
manager: chackdan
editor: masnider,rajak
ms.assetid: 9d35374c-2d75-4856-b776-e59284641956
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: required
ms.date: 5/8/2017
ms.author: aljo
ms.openlocfilehash: 2445b37e8152d8f55dad6eff057d273851dc2209
ms.sourcegitcommit: a12b2c2599134e32a910921861d4805e21320159
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/24/2019
ms.locfileid: "67340693"
---
# <a name="reliable-collection-object-serialization-in-azure-service-fabric"></a>Serializacja elementu Reliable Collection obiektu w usłudze Azure Service Fabric
Elementy Reliable Collections Replikuj i utrzymują się ich elementów, aby upewnić się, że są one trwałych maszyny błędów i awarii zasilania.
Do replikowania i utrwalanie elementów, elementów Reliable Collections konieczne serializacji je.

Elementy Reliable Collections serializatora odpowiednie dla danego typu z Reliable State Manager.
Elementy Reliable State Manager zawiera wbudowane serializatory i umożliwia serializatory niestandardowych do zarejestrowania dla danego typu.

## <a name="built-in-serializers"></a>Serializatory wbudowane

Elementy Reliable State Manager zawiera wbudowane serializatora dla niektóre typowe typy, tak, aby serializować efektywnie domyślnie. Dla innych typów Reliable State Manager powraca do użycia [DataContractSerializer](https://msdn.microsoft.com/library/system.runtime.serialization.datacontractserializer(v=vs.110).aspx).
Serializatory wbudowane są bardziej wydajne, ponieważ wiedzieli, nie można zmienić ich typów i nie muszą zawierać informacje o typie, takie jak nazwa jej typu.

Elementy Reliable State Manager ma wbudowane serializatora dla następujących typów: 
- Guid
- bool
- byte
- sbyte —
- byte[]
- char
- string
- decimal
- double
- float
- int
- uint
- long
- ulong
- Krótka
- ushort

## <a name="custom-serialization"></a>Niestandardowej serializacji

Serializatory niestandardowe są często używane, co pozwoli zwiększyć wydajność, lub do szyfrowania danych przez sieć oraz na dysku. Wśród innych powodów serializatory niestandardowe są często bardziej efektywne niż ogólny serializator, ponieważ nie potrzebują do wykonywania serializacji informacje o typie. 

[IReliableStateManager.TryAddStateSerializer\<T >](https://docs.microsoft.com/dotnet/api/microsoft.servicefabric.data.ireliablestatemanager.tryaddstateserializer) służy do rejestrowania niestandardowego serializatora dla danego typu T. Rejestracja powinno mieć miejsce w konstrukcji StatefulServiceBase, aby upewnić się, że przed rozpoczęciem odzyskiwania wszystkich elementów Reliable Collections mają dostęp do odpowiednich serializator na odczytywanie utrwalonych danych.

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
> Serializatory niestandardowe mają pierwszeństwo serializatory wbudowanych. Na przykład po zarejestrowaniu niestandardowego serializatora int, służy do serializacji liczb całkowitych zamiast wbudowanego serializatora dla wewnętrznej

### <a name="how-to-implement-a-custom-serializer"></a>Jak zaimplementować niestandardowy serializator

Należy zaimplementować niestandardowy serializator [IStateSerializer\<T >](https://docs.microsoft.com/dotnet/api/microsoft.servicefabric.data.istateserializer-1) interfejsu.

> [!NOTE]
> IStateSerializer\<T > obejmuje przeciążenia dla zapisu i odczytu, która przyjmuje dodatkowych T o nazwie wartości bazowej. Ten interfejs API jest różnicowej serializacji. Obecnie nie jest uwidaczniana funkcji różnicowej serializacji. Z tego powodu te dwa przeciążenia nie są wywoływane, dopóki serializacji różnicowa jest widoczne i włączone.

Oto przykład niestandardowego typu, o nazwie OrderKey, który zawiera cztery właściwości

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

Poniżej przedstawiono przykład stosowania IStateSerializer\<OrderKey >.
Należy zauważyć, że odczytywanie i zapisywanie przeciążeń, które przyjmują w baseValue, wywołania ich odpowiednich przeciążenia dla zgodności wyszukiwanie do przodu.

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
W [stopniowego uaktualnienia aplikacji](service-fabric-application-upgrade.md), uaktualnienie jest stosowany do podzbioru węzłów, jedną domenę uaktualnienia w danym momencie. W trakcie tego procesu będzie niektórych domen uaktualnienia na nowszą wersję aplikacji, a niektórych domen uaktualnienia będzie znajdować się na starszą wersję aplikacji. W miarę wprowadzania aktualizacji nowa wersja aplikacji musi mieć możliwość odczytu stara wersja usługi danych, a stara wersja aplikacji musi mieć możliwość odczytu nową wersję usługi danych. Jeśli format danych jest niezgodny ze starszymi i nowszymi, uaktualnienie może zakończyć się niepowodzeniem lub co gorsza, może być utraty lub uszkodzenia danych.

Jeśli używasz wbudowanych Serializator nie masz już martwić się o zgodności.
Jednak jeśli używasz niestandardowego serializatora lub elementu DataContractSerializer, dane muszą być nieskończenie przodu i do tyłu zgodne.
Innymi słowy każda wersja serializator musi mieć możliwość serializacji i deserializacji dowolną wersję typu.

Użytkownicy kontraktu danych powinien być zgodny reguły dobrze zdefiniowanych kontroli wersji, dodawanie, usuwanie i zmienianie pól. Kontrakt danych ma również obsługę rozwiązywania problemów związanych z polami nieznany, przechwytywanie procesem serializacji i deserializacji i rozwiązywania problemów związanych z dziedziczenia klasy. Aby uzyskać więcej informacji, zobacz [kontraktu danych za pomocą](https://msdn.microsoft.com/library/ms733127.aspx).

Niestandardowy serializator użytkowników powinien spełniać wytycznych serializator, którego używają zapewnienie jest Wstecz i przekazuje zgodne.
Typowy sposób obsługiwać wszystkie wersje jest dodanie informacje o rozmiarze na początku i tylko dodanie właściwości opcjonalne.
W ten sposób każda wersja może odczytywać podobnie można i przejść przez pozostałe części strumienia.

## <a name="next-steps"></a>Kolejne kroki
  * [Serializacja i uaktualnienia](service-fabric-application-upgrade-data-serialization.md)
  * [Dokumentacja dla deweloperów dla elementów Reliable Collections](https://msdn.microsoft.com/library/azure/microsoft.servicefabric.data.collections.aspx)
  * [Uaktualnienie z aplikacji przy użyciu programu Visual Studio](service-fabric-application-upgrade-tutorial.md) przeprowadzi uaktualnienie aplikacji przy użyciu programu Visual Studio.
  * [Uaktualnienie z aplikacji przy użyciu programu Powershell](service-fabric-application-upgrade-tutorial-powershell.md) przeprowadzi uaktualnienie aplikacji przy użyciu programu PowerShell.
  * Kontrolować, jak uaktualnić aplikację przy użyciu [parametry uaktualniania](service-fabric-application-upgrade-parameters.md).
  * Dowiedz się, jak korzystać z zaawansowanych funkcji podczas uaktualniania aplikacji, odwołując się do [Tematy zaawansowane](service-fabric-application-upgrade-advanced.md).
  * Rozwiązywanie typowych problemów podczas uaktualniania aplikacji korzystając z procedury opisanej w [Rozwiązywanie problemów z uaktualnieniami aplikacji](service-fabric-application-upgrade-troubleshooting.md).
