---
title: Zmienianie ustawień aplikacji KVSActorStateProvider
description: Dowiedz się więcej o konfigurowaniu stanowych podmiotów usługi Azure Service fabric typu KVSActorStateProvider.
author: sumukhs
ms.topic: conceptual
ms.date: 10/2/2017
ms.author: sumukhs
ms.openlocfilehash: cdb115bd57cf3d5af4388f4efa03c2522feef9ca
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75609778"
---
# <a name="configuring-reliable-actors--kvsactorstateprovider"></a>Konfigurowanie niezawodnych aktorów - KVSActorStateProvider
Domyślną konfigurację programu KVSActorStateProvider można zmodyfikować, zmieniając plik settings.xml wygenerowany w katalogu głównym pakietu programu Microsoft Visual Studio w folderze Config dla określonego aktora.

Środowisko uruchomieniowe sieci szkieletowej usług Azure wyszukuje wstępnie zdefiniowane nazwy sekcji w pliku settings.xml i zużywa wartości konfiguracji podczas tworzenia podstawowych składników środowiska uruchomieniowego.

> [!NOTE]
> **Nie** należy usuwać ani modyfikować nazw sekcji następujących konfiguracji w pliku settings.xml, który jest generowany w rozwiązaniu programu Visual Studio.
> 
> 

## <a name="replicator-security-configuration"></a>Konfiguracja zabezpieczeń replikatora
Konfiguracje zabezpieczeń replikatora służą do zabezpieczania kanału komunikacyjnego używanego podczas replikacji. Oznacza to, że usługi nie widzą wzajemnego ruchu replikacji, zapewniając, że dane, które są udostępniane jako wysoce dostępne, są również bezpieczne.
Domyślnie pusta sekcja konfiguracji zabezpieczeń uniemożliwia bezpieczeństwo replikacji.

> [!IMPORTANT]
> W węzłach systemu Linux certyfikaty muszą być sformatowane w formacie PEM. Aby dowiedzieć się więcej o lokalizowaniu i konfigurowaniu certyfikatów dla systemu Linux, zobacz [Konfigurowanie certyfikatów w systemie Linux](./service-fabric-configure-certificates-linux.md). 
> 

### <a name="section-name"></a>Nazwa sekcji
&lt;ActorName&gt;ServiceReplicatorSecurityConfig

## <a name="replicator-configuration"></a>Konfiguracja replikatora
Konfiguracje replikatora skonfigurować replikatora, który jest odpowiedzialny za uczynienie stan dostawcy stanu aktora wysoce niezawodne.
Domyślna konfiguracja jest generowana przez szablon programu Visual Studio i powinna wystarczyć. W tej sekcji o mówi o dodatkowych konfiguracjach, które są dostępne do strojenia replikatora.

### <a name="section-name"></a>Nazwa sekcji
&lt;ActorName&gt;ServiceReplicatorConfig

### <a name="configuration-names"></a>Nazwy konfiguracji
| Nazwa | Jednostka | Wartość domyślna | Uwagi |
| --- | --- | --- | --- |
| BatchAcknowledgementInterval (W przypadku danych: BatchAcknowledgementInterval) |Sekundy |0.015 |Okres, dla którego replikator w pomocniczej czeka po otrzymaniu operacji przed wysłaniem potwierdzenia do podstawowego. Wszelkie inne potwierdzenia, które mają być wysyłane do operacji przetworzonych w tym przedziale czasu są wysyłane jako jedna odpowiedź. |
| ReplicatorEndpoint (Punkt replikatora) |Nie dotyczy |Brak domyślnego parametru wymaganego |Adres IP i port, które replikator podstawowy/pomocniczy będzie używany do komunikowania się z innymi replikatorami w zestawie replik. Powinno to odwoływać się do punktu końcowego zasobu TCP w manifeście usługi. Zapoznaj się [z zasobów manifestu usługi,](service-fabric-service-manifest-resources.md) aby dowiedzieć się więcej o definiowaniu zasobów punktu końcowego w manifeście usługi. |
| Ponowne próbyInterwalne |Sekundy |5 |Okres, po którym replikator ponownie przesyła wiadomość, jeśli nie otrzyma potwierdzenia dla operacji. |
| Rozmiar MaxReplicationMessageSize |Bajty |50 MB |Maksymalny rozmiar danych replikacji, które mogą być przesyłane w jednej wiadomości. |
| Maksymalna liczba aplikacji MaxPrimaryReplicationQueueSize |Liczba operacji |1024 |Maksymalna liczba operacji w kolejce podstawowej. Operacja jest zwalniana po tym, jak replikator podstawowy otrzyma potwierdzenie od wszystkich replikatorów pomocniczych. Wartość ta musi być większa niż 64 i moc 2. |
| MaxSecondaryReplicationQueueSize |Liczba operacji |2048 |Maksymalna liczba operacji w kolejce pomocniczej. Operacja jest zwalniana po udostępnieniu jej stanu wysoce dostępne za pośrednictwem trwałości. Wartość ta musi być większa niż 64 i moc 2. |

## <a name="store-configuration"></a>Konfiguracja sklepu
Konfiguracje magazynu są używane do konfigurowania magazynu lokalnego, który jest używany do utrwalania stanu, który jest replikowany.
Domyślna konfiguracja jest generowana przez szablon programu Visual Studio i powinna wystarczyć. W tej sekcji o mówi o dodatkowych konfiguracjach, które są dostępne do strojenia magazynu lokalnego.

### <a name="section-name"></a>Nazwa sekcji
&lt;ActorName&gt;ServiceLocalStoreConfig

### <a name="configuration-names"></a>Nazwy konfiguracji
| Nazwa | Jednostka | Wartość domyślna | Uwagi |
| --- | --- | --- | --- |
| MaxAsyncCommitDelayInMilliseconds |Milisekund |200 |Ustawia maksymalny interwał wsadowego dla trwałych zatwierdzeń magazynu lokalnego. |
| Strony MaxVer |Liczba stron |16384 |Maksymalna liczba stron wersji w lokalnej bazie danych magazynu. Określa maksymalną liczbę transakcji pozostających do spłaty. |

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
Parametr BatchAcknowledgementInterval steruje opóźnieniem replikacji. Wartość "0" powoduje najniższe możliwe opóźnienie, kosztem przepływności (ponieważ więcej komunikatów potwierdzenia musi być wysyłanych i przetwarzanych, z których każdy zawiera mniej potwierdzeń).
Im większa wartość batchacknowledgementInterval, tym wyższa całkowita przepływność replikacji, kosztem wyższego opóźnienia operacji. To bezpośrednio przekłada się na opóźnienie zatwierdzania transakcji.

