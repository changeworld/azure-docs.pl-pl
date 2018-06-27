---
title: Zmień ustawienia KVSActorStateProvider w Azure mikrousług | Dokumentacja firmy Microsoft
description: Informacje na temat konfigurowania stanowe uczestnikami typu KVSActorStateProvider sieć szkieletowa usług Azure.
services: Service-Fabric
documentationcenter: .net
author: sumukhs
manager: timlt
editor: ''
ms.assetid: dbed72f4-dda5-4287-bd56-da492710cd96
ms.service: Service-Fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 10/2/2017
ms.author: sumukhs
ms.openlocfilehash: c01bcfecea8d79784b764e715f077c76e7d4be45
ms.sourcegitcommit: 0fa8b4622322b3d3003e760f364992f7f7e5d6a9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/27/2018
ms.locfileid: "37017651"
---
# <a name="configuring-reliable-actors--kvsactorstateprovider"></a>Konfigurowanie Reliable Actors — KVSActorStateProvider
Domyślna konfiguracja KVSActorStateProvider można modyfikować, zmieniając pliku settings.xml, generowany w folderze głównym pakietu Microsoft Visual Studio w folderze konfiguracji dla określonego aktora.

Środowisko uruchomieniowe usługi sieć szkieletowa usług Azure szuka nazwy sekcji wstępnie zdefiniowanych w pliku settings.xml i wykorzystuje wartości konfiguracji podczas tworzenia podstawowych składników środowiska wykonawczego.

> [!NOTE]
> Czy **nie** usunięcia lub zmodyfikowania nazwy sekcji następujące konfiguracje w pliku settings.xml, który jest generowany w rozwiązaniu Visual Studio.
> 
> 

## <a name="replicator-security-configuration"></a>Konfiguracja zabezpieczeń replikatora
Replikator konfiguracji zabezpieczeń służą do zabezpieczania kanał komunikacyjny używany podczas replikacji. Oznacza to, czy usługi nie widzi siebie nawzajem ruch związany z replikacją, zapewnia, że dane, które jest zyskuje dużą dostępność również jest bezpieczne.
Domyślnie puste zabezpieczeń sekcji konfiguracji uniemożliwia zabezpieczeń replikacji.

> [!IMPORTANT]
> W węzłach Linux certyfikaty muszą być sformatowane PEM. Aby uzyskać więcej informacji o lokalizacji i konfigurowanie certyfikatów dla systemu Linux, zobacz [Konfigurowanie certyfikatów w systemie Linux](./service-fabric-configure-certificates-linux.md). 
> 

### <a name="section-name"></a>Nazwa sekcji
&lt;ActorName&gt;ServiceReplicatorSecurityConfig

## <a name="replicator-configuration"></a>Konfiguracja replikatora
Konfiguracje replikatora skonfiguruj replikatora odpowiedzialną za tworzenie wysoce niezawodne stanu dostawcy stanu aktora.
Domyślna konfiguracja jest generowany przez szablon programu Visual Studio i powinny wystarczyć. Ta sekcja zawiera informacje o dodatkowych konfiguracjach, które są dostępne dostroić replikatora.

### <a name="section-name"></a>Nazwa sekcji
&lt;ActorName&gt;ServiceReplicatorConfig

### <a name="configuration-names"></a>Nazwy konfiguracji
| Name (Nazwa) | Jednostka | Wartość domyślna | Uwagi |
| --- | --- | --- | --- |
| BatchAcknowledgementInterval |Sekundy |0.015 |Okres, dla którego replikatora na dodatkowej czeka po otrzymaniu operacji przed wysłaniem z powrotem do podstawowej potwierdzenia. Inne potwierdzeń na wysłanie operacji przetwarzane w ramach tego interwału są wysyłane jako jedna odpowiedź. |
| ReplicatorEndpoint |ND |Brak wartości domyślnej — wymagany parametr |Ustaw adres IP i port, który replikatora podstawowe i pomocnicze będzie używany do komunikowania się z innymi replikatorów w replice. To powinien odwoływać się do zasobu punkt końcowy protokołu TCP w manifeście usługi. Zapoznaj się [zasoby manifestu usługi](service-fabric-service-manifest-resources.md) Aby dowiedzieć się więcej o Definiowanie zasobów punktu końcowego w manifeście usługi. |
| retryInterval |Sekundy |5 |Okres, po którym replikatora ponownie przesyła komunikat, jeśli nie otrzyma potwierdzenia dla danej operacji. |
| MaxReplicationMessageSize |Bajty |50 MB |Maksymalny rozmiar danych replikacji, które mogą być przenoszone w pojedynczym komunikacie. |
| MaxPrimaryReplicationQueueSize |Liczba operacji |1024 |Maksymalna liczba operacji w kolejce podstawowego. Operacja są zwalniane, gdy Replikator podstawowy otrzyma potwierdzenia od wszystkich dodatkowej replikatorów. Ta wartość musi być większa niż 64 i potęgą liczby 2. |
| MaxSecondaryReplicationQueueSize |Liczba operacji |2048 |Maksymalna liczba operacji w kolejce dodatkowej. Operacja są zwalniane po dokonaniu jej stan wysokiej dostępności za pośrednictwem trwałości. Ta wartość musi być większa niż 64 i potęgą liczby 2. |

## <a name="store-configuration"></a>Konfiguracja magazynu
Konfiguracje magazynu są używane do konfigurowania magazynu lokalnego służący do utrwalenia stanu, który jest replikowany.
Domyślna konfiguracja jest generowany przez szablon programu Visual Studio i powinny wystarczyć. Ta sekcja zawiera informacje o dodatkowych konfiguracjach, które są dostępne dostroić magazynu lokalnego.

### <a name="section-name"></a>Nazwa sekcji
&lt;ActorName&gt;ServiceLocalStoreConfig

### <a name="configuration-names"></a>Nazwy konfiguracji
| Name (Nazwa) | Jednostka | Wartość domyślna | Uwagi |
| --- | --- | --- | --- |
| MaxAsyncCommitDelayInMilliseconds |Milisekundy |200 |Ustawia maksymalną przetwarzanie wsadowe interwał zatwierdzeń trwałego magazynu lokalnego. |
| MaxVerPages |Liczba stron |16384 |Maksymalną liczbę stron w wersji lokalnej przechowywania bazy danych. Określa maksymalną liczbę oczekujących transakcji. |

## <a name="sample-configuration-file"></a>Przykładowy plik konfiguracyjny
```xml
<?xml version="1.0" encoding="utf-8"?>
<Settings xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/2011/01/fabric">
   <Section Name="MyActorServiceReplicatorConfig">
      <Parameter Name="ReplicatorEndpoint" Value="MyActorServiceReplicatorEndpoint" />
      <Parameter Name="BatchAcknowledgementInterval" Value="0.05"/>
   </Section>
   <Section Name="MyActorServiceLocalStoreConfig">
      <Parameter Name="MaxVerPages" Value="8192" />
   </Section>
   <Section Name="MyActorServiceReplicatorSecurityConfig">
      <Parameter Name="CredentialType" Value="X509" />
      <Parameter Name="FindType" Value="FindByThumbprint" />
      <Parameter Name="FindValue" Value="9d c9 06 b1 69 dc 4f af fd 16 97 ac 78 1e 80 67 90 74 9d 2f" />
      <Parameter Name="StoreLocation" Value="LocalMachine" />
      <Parameter Name="StoreName" Value="My" />
      <Parameter Name="ProtectionLevel" Value="EncryptAndSign" />
      <Parameter Name="AllowedCommonNames" Value="My-Test-SAN1-Alice,My-Test-SAN1-Bob" />
   </Section>
</Settings>
```
## <a name="remarks"></a>Uwagi
Parametr BatchAcknowledgementInterval Określa opóźnienie replikacji. Wartość "0" powoduje najniższym opóźnieniu możliwe, kosztem przepływności (jak więcej komunikatów potwierdzenia musi być wysyłane i przetwarzane, zawierający mniejszą liczbę potwierdzeń).
Im większa wartość BatchAcknowledgementInterval, tym wyższy ogólną przepustowość replikacji, kosztem większego opóźnienia operacji. Bezpośrednio przekłada się to opóźnienie zatwierdzeń transakcji.

