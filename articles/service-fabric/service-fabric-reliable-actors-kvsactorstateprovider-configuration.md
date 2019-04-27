---
title: Zmień ustawienia dostawcy KVSActorStateProvider aktorów usługi Azure Service Fabric | Dokumentacja firmy Microsoft
description: Informacje na temat konfigurowania usługi Azure Service Fabric aktorów stanowych typu dostawcy KVSActorStateProvider.
services: Service-Fabric
documentationcenter: .net
author: sumukhs
manager: chackdan
editor: ''
ms.assetid: dbed72f4-dda5-4287-bd56-da492710cd96
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 10/2/2017
ms.author: sumukhs
ms.openlocfilehash: 8b10ef18fd389179a4f5422783606c45fa2e0d32
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60728053"
---
# <a name="configuring-reliable-actors--kvsactorstateprovider"></a>Konfigurowanie elementów Reliable Actors — dostawcy KVSActorStateProvider
Domyślna konfiguracja dostawcy KVSActorStateProvider można zmodyfikować, zmieniając plik settings.xml, który jest generowany w katalogu głównym pakietu Microsoft Visual Studio w folderze konfiguracji dla określonego aktora.

Środowisko uruchomieniowe usługi Azure Service Fabric szuka nazwy sekcji wstępnie zdefiniowane w pliku settings.xml i zużywa wartości konfiguracji podczas tworzenia podstawowych składników środowiska wykonawczego.

> [!NOTE]
> Czy **nie** usunąć ani zmodyfikować nazwy sekcji z następujących konfiguracji w pliku settings.xml, który jest generowany w rozwiązaniu Visual Studio.
> 
> 

## <a name="replicator-security-configuration"></a>Konfiguracja zabezpieczeń replikatora
Konfiguracje zabezpieczeń replikatora są używane do zabezpieczenia kanału komunikacyjnego, który jest używany podczas replikacji. Oznacza to, że usługi nie widzą siebie nawzajem ruch związany z replikacją, zapewnia, że danych, zapewniając wysoką dostępność, również jest bezpieczne.
Domyślnie puste zabezpieczeń sekcji konfiguracji uniemożliwia zabezpieczeń replikacji.

> [!IMPORTANT]
> W węzłach systemu Linux certyfikaty muszą być w formacie PEM. Aby dowiedzieć się więcej na temat znajdowania i konfigurowanie certyfikatów dla systemu Linux, zobacz [Konfigurowanie certyfikatów w systemie Linux](./service-fabric-configure-certificates-linux.md). 
> 

### <a name="section-name"></a>Nazwa sekcji
&lt;ActorName&gt;ServiceReplicatorSecurityConfig

## <a name="replicator-configuration"></a>Replikator konfiguracji
Konfiguracje replikatora skonfiguruj replikatora, który jest odpowiedzialny za wprowadzanie stan dostawcy stanu aktora o wysokiej niezawodności.
Domyślna konfiguracja jest generowany przez szablon programu Visual Studio i powinny wystarczyć. W tej sekcji zawiera informacje o dodatkowych konfiguracjach, które można dostrajanie replikatora.

### <a name="section-name"></a>Nazwa sekcji
&lt;ActorName&gt;ServiceReplicatorConfig

### <a name="configuration-names"></a>Nazwy konfiguracji
| Name (Nazwa) | Jednostka | Wartość domyślna | Uwagi |
| --- | --- | --- | --- |
| BatchAcknowledgementInterval |Sekundy |0.015 |Okres czasu, dla którego replikatora na pomocniczy czeka po otrzymaniu operacji przed wysłaniem z powrotem do podstawowej potwierdzenia. Inne potwierdzeń, w celu przetworzonych w ramach tego interwału operacji są wysyłane jako jedną odpowiedź. |
| ReplicatorEndpoint |ND |Brak wartości domyślnej — wymagany parametr |Ustaw adres IP i port, który Replikator podstawowy/pomocniczy będzie używany do komunikowania się z innymi replikatorów w replice. To powinien odwoływać się do zasobu punktu końcowego TCP w manifeście usługi. Zapoznaj się [zasoby manifestu usługi](service-fabric-service-manifest-resources.md) Aby dowiedzieć się więcej o definiowaniu punktu końcowego zasobów w manifeście usługi. |
| RetryInterval |Sekundy |5 |Okres, po upływie którego replikatora ponownie przesyła wiadomość, jeśli nie odbiera potwierdzenie operacji. |
| MaxReplicationMessageSize |Bajty |50 MB |Maksymalny rozmiar danych replikacji, które mogą być przenoszone w pojedynczym komunikacie. |
| MaxPrimaryReplicationQueueSize |Liczba operacji |1024 |Maksymalna liczba operacji w kolejce podstawowego. Operacja są zwalniane, gdy Replikator podstawowy otrzyma potwierdzenia od wszystkich replikatorów dodatkowej. Ta wartość musi być większa niż 64 i potęgą liczby 2. |
| MaxSecondaryReplicationQueueSize |Liczba operacji |2048 |Maksymalna liczba operacji w kolejce dodatkowej. Operacja nie zostanie zwolniona po wprowadzeniu swojego stanu o wysokiej dostępności za pośrednictwem trwałości. Ta wartość musi być większa niż 64 i potęgą liczby 2. |

## <a name="store-configuration"></a>Konfiguracja Store
Konfiguracje Store są używane do konfigurowania magazynu lokalnego, która umożliwia utrwalanie stanu, który jest replikowany.
Domyślna konfiguracja jest generowany przez szablon programu Visual Studio i powinny wystarczyć. W tej sekcji zawiera informacje o dodatkowych konfiguracjach, które można dostrajanie magazynu lokalnego.

### <a name="section-name"></a>Nazwa sekcji
&lt;ActorName&gt;ServiceLocalStoreConfig

### <a name="configuration-names"></a>Nazwy konfiguracji
| Name (Nazwa) | Jednostka | Wartość domyślna | Uwagi |
| --- | --- | --- | --- |
| MaxAsyncCommitDelayInMilliseconds |Milisekundy |200 |Ustawia maksymalny interwał zatwierdzenia trwałego magazynu lokalnego dzielenia na partie. |
| MaxVerPages |Liczba stron |16384 |Maksymalna liczba stron w wersji lokalnej przechowywania bazy danych. Określa maksymalną liczbę oczekujących transakcji. |

## <a name="sample-configuration-file"></a>Przykładowy plik konfiguracji
```xml
<?xml version="1.0" encoding="utf-8"?>
<Settings xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/2011/01/fabric">
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
Parametr BatchAcknowledgementInterval Określa opóźnienie replikacji. Wartość "0" powoduje najniższe możliwe opóźnienie kosztem przepływności (jak więcej wiadomości potwierdzenia musi być wysyłane i przetwarzane, zawierający mniejszą liczbę potwierdzeń).
Im większa wartość BatchAcknowledgementInterval, tym większe ogólną przepustowość replikacji, kosztem większego opóźnienia operacji. Bezpośrednio przekłada się to opóźnienie zatwierdzenia transakcji.

