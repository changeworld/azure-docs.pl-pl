---
title: Zmień ustawienia ReliableDictionaryActorStateProvider aktorów usługi Azure Service Fabric | Dokumentacja firmy Microsoft
description: Informacje na temat konfigurowania usługi Azure Service Fabric aktorów stanowych typu ReliableDictionaryActorStateProvider.
services: Service-Fabric
documentationcenter: .net
author: sumukhs
manager: chackdan
editor: ''
ms.assetid: 79b48ffa-2474-4f1c-a857-3471f9590ded
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 10/2/2017
ms.author: sumukhs
ms.openlocfilehash: 4e39357a765ec85aa64055b1aa422d8d7a01c116
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60727135"
---
# <a name="configuring-reliable-actors--reliabledictionaryactorstateprovider"></a>Konfigurowanie elementów Reliable Actors — ReliableDictionaryActorStateProvider
Domyślnej konfiguracji elementu ReliableDictionaryActorStateProvider można zmodyfikować, zmieniając plik settings.xml generowane w katalogu głównym pakietu Visual Studio w folderze konfiguracji dla określonego aktora.

Środowisko uruchomieniowe usługi Azure Service Fabric szuka nazwy sekcji wstępnie zdefiniowane w pliku settings.xml i zużywa wartości konfiguracji podczas tworzenia podstawowych składników środowiska wykonawczego.

> [!NOTE]
> Czy **nie** usunąć ani zmodyfikować nazwy sekcji z następujących konfiguracji w pliku settings.xml, który jest generowany w rozwiązaniu Visual Studio.
> 
> 

Istnieją także ustawienia globalne, które wpływają na konfiguracji elementu ReliableDictionaryActorStateProvider.

## <a name="global-configuration"></a>Konfigurację globalną
W manifeście klastra dla klastra, w sekcji KtlLogger określono jego konfigurację globalną. Umożliwia konfigurację lokalizacji udostępnionej dziennika i rozmiaru oraz limity pamięci globalnej, używane przez rejestrator. Należy pamiętać, że zmiany w manifeście klastra mają wpływ na wszystkie usługi, które używają ReliableDictionaryActorStateProvider i niezawodne usługi stanowe.

Manifest klastra jest pojedynczy plik XML, który przechowuje ustawienia i konfiguracje, które są stosowane do wszystkich węzłów i usług w klastrze. Plik jest zwykle nazywany ClusterManifest.xml. Możesz zobaczyć klastra manifestu dla klastra przy użyciu polecenia programu powershell Get-ServiceFabricClusterManifest.

### <a name="configuration-names"></a>Nazwy konfiguracji
| Name (Nazwa) | Jednostka | Wartość domyślna | Uwagi |
| --- | --- | --- | --- |
| WriteBufferMemoryPoolMinimumInKB |Kilobytes |8388608 |Minimalna liczba KB do przydzielenia w trybie jądra dla puli pamięci buforu zapisu rejestratora. Tej puli pamięci jest używana do buforowania informacji o stanie przed zapisywania na dysku. |
| WriteBufferMemoryPoolMaximumInKB |Kilobytes |Bez ograniczeń |Maksymalny rozmiar, w której rejestratora zapisuje bufora pamięci puli można powiększać. |
| SharedLogId |Identyfikator GUID |"" |Określa unikatowy identyfikator GUID służących do identyfikowania domyślny udostępniony plik dziennika używana przez wszystkich usług reliable services we wszystkich węzłach w klastrze, których nie należy określać SharedLogId w ich konfiguracji specyficznej dla usługi. Jeśli określono SharedLogId, następnie SharedLogPath musi być także określona. |
| SharedLogPath |W pełni kwalifikowanej nazwy ścieżki |"" |Określa pełną ścieżkę, gdzie plik dziennika udostępnione używane przez wszystkich usług reliable services we wszystkich węzłach w klastrze, których nie należy określać SharedLogPath w ich konfiguracji specyficznej dla usługi. Jednak jeśli SharedLogPath jest określona, opcja SharedLogId musi być także określona. |
| SharedLogSizeInMB |(MB) |8192 |Określa liczbę MB miejsca na dysku statycznie przydzielić dla udostępnionego dziennika. Wartość musi być 2048 lub większą. |

### <a name="sample-cluster-manifest-section"></a>Sekcja manifestu klastra próbki
```xml
   <Section Name="KtlLogger">
     <Parameter Name="WriteBufferMemoryPoolMinimumInKB" Value="8192" />
     <Parameter Name="WriteBufferMemoryPoolMaximumInKB" Value="8192" />
     <Parameter Name="SharedLogId" Value="{7668BB54-FE9C-48ed-81AC-FF89E60ED2EF}"/>
     <Parameter Name="SharedLogPath" Value="f:\SharedLog.Log"/>
     <Parameter Name="SharedLogSizeInMB" Value="16383"/>
   </Section>
```

### <a name="remarks"></a>Uwagi
Rejestrator ma globalnej puli pamięci zaalokowanej z pamięci niestronicowana jądra, która jest dostępna dla wszystkich usług reliable services w węźle do buforowania danych o stanie, zanim są zapisywane w dzienniku dedykowanych skojarzone z repliki usługi reliable service. Rozmiar puli jest kontrolowana przez ustawienia WriteBufferMemoryPoolMinimumInKB i WriteBufferMemoryPoolMaximumInKB. WriteBufferMemoryPoolMinimumInKB Określa początkowy rozmiar tej puli pamięci i najmniejszy rozmiar, do której może spowodować zmniejszenie puli pamięci. WriteBufferMemoryPoolMaximumInKB jest najwyższym rozmiar puli pamięci może. Każda replika usługi reliable service, która jest otwierana może zwiększyć rozmiar puli pamięci o kwotę system określić maksymalnie WriteBufferMemoryPoolMaximumInKB. Jeśli istnieje więcej zapotrzebowanie na pamięć z puli pamięci niż dostępna, żądania dotyczące pamięci będą opóźnione aż do pamięci. W związku z tym jeśli puli pamięci buforu zapisu jest zbyt mały dla określonej konfiguracji następnie może to spowodować obniżenie wydajności.

Ustawienia SharedLogId i SharedLogPath zawsze są używane razem do definiowania identyfikator GUID i lokalizację dziennika udostępnionego domyślnego dla wszystkich węzłów w klastrze. Domyślny dziennik udostępnione jest używany dla wszystkich usług reliable services, które określają ustawienia settings.xml dla określonej usługi. Najlepszą wydajność udostępnione pliki dziennika powinny być umieszczane na dyskach, które są używane wyłącznie do pliku dziennika udostępnione do zmniejszenia stopnia rywalizacji.

SharedLogSizeInMB określa ilość miejsca na dysku do przydzielenia dla dziennika udostępnionego domyślne we wszystkich węzłach.  SharedLogId i SharedLogPath nie muszą być podane w kolejności dla SharedLogSizeInMB należy określić.

## <a name="replicator-security-configuration"></a>Konfiguracja zabezpieczeń replikatora
Konfiguracje zabezpieczeń replikatora są używane do zabezpieczenia kanału komunikacyjnego, który jest używany podczas replikacji. Oznacza to, że usługi nie widzą siebie nawzajem ruch związany z replikacją, zapewnienie, że dane, wysoce dostępny jest również bezpieczne.
Domyślnie puste zabezpieczeń sekcji konfiguracji uniemożliwia zabezpieczeń replikacji.

> [!IMPORTANT]
> W węzłach systemu Linux certyfikaty muszą być w formacie PEM. Aby dowiedzieć się więcej na temat znajdowania i konfigurowanie certyfikatów dla systemu Linux, zobacz [Konfigurowanie certyfikatów w systemie Linux](./service-fabric-configure-certificates-linux.md). 
> 

### <a name="section-name"></a>Nazwa sekcji
&lt;ActorName&gt;ServiceReplicatorSecurityConfig

## <a name="replicator-configuration"></a>Replikator konfiguracji
Replikator konfiguracji są używane do konfigurowania replikatora, który jest odpowiedzialny za wprowadzanie stan dostawcy stanu aktora o wysokiej niezawodności, replikacji i przechowywanie stanu lokalnie.
Domyślna konfiguracja jest generowany przez szablon programu Visual Studio i powinny wystarczyć. W tej sekcji zawiera informacje o dodatkowych konfiguracjach, które można dostrajanie replikatora.

### <a name="section-name"></a>Nazwa sekcji
&lt;ActorName&gt;ServiceReplicatorConfig

### <a name="configuration-names"></a>Nazwy konfiguracji
| Name (Nazwa) | Jednostka | Wartość domyślna | Uwagi |
| --- | --- | --- | --- |
| BatchAcknowledgementInterval |Sekundy |0.015 |Okres czasu, dla którego replikatora na pomocniczy czeka po otrzymaniu operacji przed wysłaniem z powrotem do podstawowej potwierdzenia. Inne potwierdzeń, w celu przetworzonych w ramach tego interwału operacji są wysyłane jako jedną odpowiedź. |
| ReplicatorEndpoint |ND |Brak wartości domyślnej — wymagany parametr |Ustaw adres IP i port, który Replikator podstawowy/pomocniczy będzie używany do komunikowania się z innymi replikatorów w replice. To powinien odwoływać się do zasobu punktu końcowego TCP w manifeście usługi. Zapoznaj się [zasoby manifestu usługi](service-fabric-service-manifest-resources.md) Aby dowiedzieć się więcej o definiowaniu punktu końcowego zasobów w manifeście usługi. |
| MaxReplicationMessageSize |Bajty |50 MB |Maksymalny rozmiar danych replikacji, które mogą być przenoszone w pojedynczym komunikacie. |
| MaxPrimaryReplicationQueueSize |Liczba operacji |8192 |Maksymalna liczba operacji w kolejce podstawowego. Operacja są zwalniane, gdy Replikator podstawowy otrzyma potwierdzenia od wszystkich replikatorów dodatkowej. Ta wartość musi być większa niż 64 i potęgą liczby 2. |
| MaxSecondaryReplicationQueueSize |Liczba operacji |16384 |Maksymalna liczba operacji w kolejce dodatkowej. Operacja nie zostanie zwolniona po wprowadzeniu swojego stanu o wysokiej dostępności za pośrednictwem trwałości. Ta wartość musi być większa niż 64 i potęgą liczby 2. |
| CheckpointThresholdInMB |MB |200 |Ilość miejsca w dzienniku plików po upływie którego stan jest utworzono punkt kontrolny. |
| MaxRecordSizeInKB |KB |1024 |Największy rozmiar rekordu, który replikatora może zapisywać w dzienniku. Ta wartość musi być wielokrotnością liczby 4 i większa niż 16. |
| OptimizeLogForLowerDiskUsage |Boolean |true |W przypadku wartości true dziennika jest skonfigurowana tak, aby repliki dedykowanych plik dziennika jest tworzona przy użyciu pliku rozrzedzonego systemu plików NTFS. Zmniejsza to użycie miejsca na dysku rzeczywistego pliku. W przypadku wartości FAŁSZ, plik jest tworzony o stałej alokacji, które oferują najlepszą wydajność zapisu. |
| SharedLogId |Identyfikator GUID |"" |Określa unikatowy identyfikator guid służących do identyfikowania pliku dziennika udostępnione używane przy użyciu tej repliki. Zazwyczaj usług nie należy używać tego ustawienia. Jednak jeśli SharedLogId jest określona, opcja SharedLogPath musi być także określona. |
| SharedLogPath |W pełni kwalifikowanej nazwy ścieżki |"" |Określa pełną ścieżkę, w której zostanie utworzony plik dziennika udostępnionego dla tej repliki. Zazwyczaj usług nie należy używać tego ustawienia. Jednak jeśli SharedLogPath jest określona, opcja SharedLogId musi być także określona. |

## <a name="sample-configuration-file"></a>Przykładowy plik konfiguracji
```xml
<?xml version="1.0" encoding="utf-8"?>
<Settings xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/2011/01/fabric">
   <Section Name="MyActorServiceReplicatorConfig">
      <Parameter Name="ReplicatorEndpoint" Value="MyActorServiceReplicatorEndpoint" />
      <Parameter Name="BatchAcknowledgementInterval" Value="0.05"/>
      <Parameter Name="CheckpointThresholdInMB" Value="180" />
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

Parametr CheckpointThresholdInMB określa ilość miejsca na dysku używanego przez replikatora do przechowywania informacji o stanie w dedykowanym pliku dziennika repliki. Zwiększa to do wartości większej niż domyślne może spowodować szybsze ponownej konfiguracji po dodaniu nowej repliki do zestawu. Jest to spowodowane transferu częściowe stanu, który ma miejsce ze względu na dostępność więcej historii działań w dzienniku. Potencjalnie może to zwiększyć czas odzyskiwania, repliki bazy danych po awarii.

Jeśli OptimizeForLowerDiskUsage jest ustawiona na wartość true, miejsce w pliku dziennika będą nadmiernie aprowizowane, dzięki czemu active repliki można przechowywać więcej informacji o stanie w ich pliki dziennika, gdy nieaktywne repliki będzie używać mniej miejsca na dysku. Pozwala na hostowanie większa liczba replik w węźle. Jeśli OptimizeForLowerDiskUsage jest ustawiona na wartość false, informacje o stanie są zapisywane w plikach dziennika szybciej.

Ustawienie MaxRecordSizeInKB określa maksymalny rozmiar rekordu, które mogą być zapisywane przez replikatora do pliku dziennika. W większości przypadków domyślny rozmiar rekordu 1024 KB jest optymalna. Jednak jeśli usługa powoduje większe elementów danych jako część informacji o stanie, ta wartość może być konieczne zwiększenie. Ma niewielkie korzyści w podejmowaniu MaxRecordSizeInKB mniejszych niż 1024, zgodnie z mniejszych rekordy, należy użyć tylko przestrzeń na mniejsze rekordu. Oczekujemy, że ta wartość będzie muszą zostać zmienione tylko w rzadkich przypadkach.

Ustawienia SharedLogId i SharedLogPath są zawsze używane razem używać oddzielnych dziennikach udostępnionego z domyślny dziennik udostępnionego dla węzła usługi. Aby uzyskać najlepszą wydajność jak wiele usług, jak to możliwe, należy określić tego samego dziennika dla udostępnionego. Udostępnione pliki dziennika powinny być umieszczone na dyskach, które są używane wyłącznie do pliku dziennika udostępnione do zmniejszenia stopnia rywalizacji głównego przepływu. Oczekujemy, że te wartości muszą można zmienić tylko w rzadkich przypadkach.

