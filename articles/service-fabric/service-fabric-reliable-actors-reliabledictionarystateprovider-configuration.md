---
title: Zmień ustawienia ReliableDictionaryActorStateProvider w Azure mikrousług | Dokumentacja firmy Microsoft
description: Informacje na temat konfigurowania stanowe uczestnikami typu ReliableDictionaryActorStateProvider sieć szkieletowa usług Azure.
services: Service-Fabric
documentationcenter: .net
author: sumukhs
manager: timlt
editor: ''
ms.assetid: 79b48ffa-2474-4f1c-a857-3471f9590ded
ms.service: Service-Fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 10/2/2017
ms.author: sumukhs
ms.openlocfilehash: 00ae5db5fc7a327ae19e64c3d8adf653afd12677
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/16/2018
ms.locfileid: "34213116"
---
# <a name="configuring-reliable-actors--reliabledictionaryactorstateprovider"></a>Konfigurowanie Reliable Actors — ReliableDictionaryActorStateProvider
Domyślna konfiguracja ReliableDictionaryActorStateProvider można modyfikować, zmieniając pliku settings.xml generowane w głównym pakietu programu Visual Studio w folderze konfiguracji dla określonego aktora.

Środowisko uruchomieniowe usługi sieć szkieletowa usług Azure szuka nazwy sekcji wstępnie zdefiniowanych w pliku settings.xml i wykorzystuje wartości konfiguracji podczas tworzenia podstawowych składników środowiska wykonawczego.

> [!NOTE]
> Czy **nie** usunięcia lub zmodyfikowania nazwy sekcji następujące konfiguracje w pliku settings.xml, który jest generowany w rozwiązaniu Visual Studio.
> 
> 

Istnieją ustawienia globalne, które mają wpływ na konfigurację ReliableDictionaryActorStateProvider.

## <a name="global-configuration"></a>Konfigurację globalną
W manifeście klastra dla klastra, w sekcji KtlLogger określono jego konfigurację globalną. Go umożliwia skonfigurowanie lokalizacji udostępnionej dziennika i rozmiaru i limity pamięci globalnej używane przez rejestratora. Należy pamiętać, że zmiany w manifeście klastra mają wpływ na wszystkich usług, które używają ReliableDictionaryActorStateProvider i niezawodnych usług stanowych.

Plik manifestu klastra to pojedynczy plik XML, który przechowuje ustawienia i konfiguracje, które są stosowane do wszystkich węzłów i usług w klastrze. Plik jest zwykle nazywany ClusterManifest.xml. Widać klastra dla klastra przy użyciu polecenia programu powershell Get-ServiceFabricClusterManifest manifestu.

### <a name="configuration-names"></a>Nazwy konfiguracji
| Name (Nazwa) | Jednostka | Wartość domyślna | Uwagi |
| --- | --- | --- | --- |
| WriteBufferMemoryPoolMinimumInKB |Kilobajty |8388608 |Minimalna liczba KB przydzielić w trybie jądra dla puli pamięci buforu zapisu rejestratora. Tej puli pamięci jest używana do buforowania informacji o stanie przed zapisu na dysku. |
| WriteBufferMemoryPoolMaximumInKB |Kilobajty |Bez ograniczeń |Maksymalny rozmiar, do którego rejestratora zapisu puli bufora pamięci można powiększać. |
| SharedLogId |Identyfikator GUID |"" |Określa unikatowy identyfikator GUID służących do identyfikowania domyślne udostępniony plik dziennika używanych przez wszystkie usługi niezawodnego we wszystkich węzłach w klastrze, które nie określają SharedLogId ich określonej konfiguracji usługi. Jeśli określono SharedLogId, to SharedLogPath należy również określić. |
| SharedLogPath |W pełni kwalifikowana nazwa |"" |Określa pełną ścieżkę, w którym plik dziennika udostępniony używany przez wszystkie niezawodnej usługi na wszystkich węzłach w klastrze, które nie określają SharedLogPath ich określonej konfiguracji usługi. Jednak jeśli SharedLogPath jest określona, to SharedLogId należy również określić. |
| SharedLogSizeInMB |Megabajty |8192 |Określa liczbę MB miejsca na dysku przydzielić statycznie dla dziennika udostępniony. Wartość musi być 2048 lub większy. |

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
Rejestrator ma globalnej puli pamięci przydzielonej z pamięci niestronicowana jądra, która jest dostępna dla wszystkich usług niezawodnej w węźle do buforowania danych o stanie przed są zapisywane w dzienniku dedykowanych skojarzone z repliki niezawodnej usługi. Rozmiar puli jest kontrolowana przez ustawienia WriteBufferMemoryPoolMinimumInKB i WriteBufferMemoryPoolMaximumInKB. WriteBufferMemoryPoolMinimumInKB Określa początkowy rozmiar tej puli pamięci i rozmiar najniższy, do której może zmniejszyć rozmiar puli pamięci. WriteBufferMemoryPoolMaximumInKB jest najwyższym rozmiar, do której może zwiększyć się pula pamięci. Każda replika niezawodnej usługi, która jest otwarta może zwiększyć rozmiar puli pamięci o kwotę systemu ustalić maksymalnie WriteBufferMemoryPoolMaximumInKB. W przypadku żądanie więcej pamięci w puli pamięci niż jest dostępne, żądania dotyczące pamięci zostanie opóźnione aż do pamięci. W związku z tym jeśli puli pamięci buforu zapisu jest zbyt mały dla konkretnej konfiguracji następnie to spowodować obniżenie wydajności.

Ustawienia SharedLogId i SharedLogPath zawsze są używane razem do definiowania identyfikator GUID i lokalizacja dziennika udostępniony domyślnego dla wszystkich węzłów w klastrze. Dziennika udostępniony domyślny jest używany dla wszystkich usług niezawodne, które nie określają ustawienia w pliku settings.xml dla określonej usługi. Aby uzyskać najlepszą wydajność udostępnione pliki dziennika należy umieścić na dyskach, które są używane wyłącznie do pliku dziennika udostępniony do zmniejszenia rywalizacji.

SharedLogSizeInMB określa ilość miejsca na dysku do przydzielenia dla dziennika udostępniony domyślne we wszystkich węzłach.  SharedLogId i SharedLogPath nie trzeba określać aby SharedLogSizeInMB należy określić.

## <a name="replicator-security-configuration"></a>Konfiguracja zabezpieczeń replikatora
Replikator konfiguracji zabezpieczeń służą do zabezpieczania kanał komunikacyjny używany podczas replikacji. Oznacza to, czy usługi nie widzi siebie nawzajem ruch związany z replikacją, zapewnienie, że dane, które jest zyskuje dużą dostępność również jest bezpieczne.
Domyślnie puste zabezpieczeń sekcji konfiguracji uniemożliwia zabezpieczeń replikacji.

### <a name="section-name"></a>Nazwa sekcji
&lt;ActorName&gt;ServiceReplicatorSecurityConfig

## <a name="replicator-configuration"></a>Konfiguracja replikatora
Konfiguracje replikatora służą do skonfigurowania replikatora odpowiedzialną za tworzenie stanu dostawcy stanu aktora wysoce niezawodne replikacji i przechowywanie stanu lokalnie.
Domyślna konfiguracja jest generowany przez szablon programu Visual Studio i powinny wystarczyć. Ta sekcja zawiera informacje o dodatkowych konfiguracjach, które są dostępne dostroić replikatora.

### <a name="section-name"></a>Nazwa sekcji
&lt;ActorName&gt;ServiceReplicatorConfig

### <a name="configuration-names"></a>Nazwy konfiguracji
| Name (Nazwa) | Jednostka | Wartość domyślna | Uwagi |
| --- | --- | --- | --- |
| BatchAcknowledgementInterval |Sekundy |0.015 |Okres, dla którego replikatora na dodatkowej czeka po otrzymaniu operacji przed wysłaniem z powrotem do podstawowej potwierdzenia. Inne potwierdzeń na wysłanie operacji przetwarzane w ramach tego interwału są wysyłane jako jedna odpowiedź. |
| ReplicatorEndpoint |ND |Brak wartości domyślnej — wymagany parametr |Ustaw adres IP i port, który replikatora podstawowe i pomocnicze będzie używany do komunikowania się z innymi replikatorów w replice. To powinien odwoływać się do zasobu punkt końcowy protokołu TCP w manifeście usługi. Zapoznaj się [zasoby manifestu usługi](service-fabric-service-manifest-resources.md) Aby dowiedzieć się więcej o Definiowanie zasobów punktu końcowego manifestu usługi. |
| MaxReplicationMessageSize |Bajty |50 MB |Maksymalny rozmiar danych replikacji, które mogą być przenoszone w pojedynczym komunikacie. |
| MaxPrimaryReplicationQueueSize |Liczba operacji |8192 |Maksymalna liczba operacji w kolejce podstawowego. Operacja są zwalniane, gdy Replikator podstawowy otrzyma potwierdzenia od wszystkich dodatkowej replikatorów. Ta wartość musi być większa niż 64 i potęgą liczby 2. |
| MaxSecondaryReplicationQueueSize |Liczba operacji |16384 |Maksymalna liczba operacji w kolejce dodatkowej. Operacja są zwalniane po dokonaniu jej stan wysokiej dostępności za pośrednictwem trwałości. Ta wartość musi być większa niż 64 i potęgą liczby 2. |
| CheckpointThresholdInMB |MB |200 |Ilość miejsca pliku dziennika, po upływie którego stan jest w użyciu. |
| MaxRecordSizeInKB |KB |1024 |Największy rozmiar rekordu, który replikatora może zapisywać w dzienniku. Ta wartość musi być wielokrotnością liczby 4 i większa niż 16. |
| OptimizeLogForLowerDiskUsage |Wartość logiczna |true |W przypadku wartości true dziennika jest skonfigurowana tak, aby w pliku dziennika dedykowanego repliki jest tworzona przy użyciu pliku rozrzedzonego systemu plików NTFS. Zmniejsza to wykorzystanie miejsca rzeczywistego dysku dla pliku. Gdy ma wartość false, plik jest tworzony o stałej alokacji, które zapewniają najlepszą wydajność zapisu. |
| SharedLogId |Identyfikator GUID |"" |Określa unikatowy identyfikator guid służących do identyfikowania pliku dziennika udostępniony używany z tej repliki. Zazwyczaj usług nie należy używać tego ustawienia. Jednak jeśli SharedLogId jest określona, to SharedLogPath należy również określić. |
| SharedLogPath |W pełni kwalifikowana nazwa |"" |Określa pełną ścieżkę, w którym zostanie utworzony plik dziennika udostępniony dla tej repliki. Zazwyczaj usług nie należy używać tego ustawienia. Jednak jeśli SharedLogPath jest określona, to SharedLogId należy również określić. |

## <a name="sample-configuration-file"></a>Przykładowy plik konfiguracyjny
```xml
<?xml version="1.0" encoding="utf-8"?>
<Settings xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/2011/01/fabric">
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
Parametr BatchAcknowledgementInterval Określa opóźnienie replikacji. Wartość "0" powoduje najniższym opóźnieniu możliwe, kosztem przepływności (jak więcej komunikatów potwierdzenia musi być wysyłane i przetwarzane, zawierający mniejszą liczbę potwierdzeń).
Im większa wartość BatchAcknowledgementInterval, tym wyższy ogólną przepustowość replikacji, kosztem większego opóźnienia operacji. Bezpośrednio przekłada się to opóźnienie zatwierdzeń transakcji.

Parametr CheckpointThresholdInMB określa ilość miejsca na dysku używanego przez replikatora do przechowywania informacji o stanie w pliku dziennika dedykowanego repliki. Zwiększanie tego do wartości większej niż domyślny może spowodować szybsze ponownej konfiguracji po dodaniu nowej repliki do zestawu. Jest to spowodowane transferu stanu częściowego, który odbywa się z powodu dostępności historii więcej operacji w dzienniku. To potencjalnie spowodować wydłużenie czasu odzyskiwania repliki, po awarii.

Jeśli OptimizeForLowerDiskUsage jest ustawiona na wartość true, miejsce w pliku dziennika będą nadmiernie elastycznie tak, aby aktywnej repliki można przechowywać więcej informacji o stanie w swoich plików dziennika, gdy nieaktywnych replik użyje mniej miejsca na dysku. Pozwala na hosta replik więcej w węźle. Jeśli OptimizeForLowerDiskUsage jest ustawiona na wartość false, informacje o stanie są zapisywane w plikach dziennika szybciej.

Ustawienie MaxRecordSizeInKB określa maksymalny rozmiar rekordu, które mogą być zapisywane w pliku dziennika przez replikatora. W większości przypadków domyślny rozmiar rekordu 1024 KB jest optymalna. Jednak jeśli usługa powoduje większe elementy danych jako część informacji o stanie, ta wartość może być konieczne zwiększenia. Brak małego korzyści w podejmowaniu MaxRecordSizeInKB mniejszych niż 1024, jak mniejsze rekordów tylko przestrzeń na mniejsze rekordu. Oczekuje się, że ta wartość musi można zmienić tylko w rzadkich przypadkach.

Ustawienia SharedLogId i SharedLogPath zawsze są używane razem aby używać oddzielnego dziennika udostępniony z domyślnego dziennika udostępniony dla węzła usługi. Dla zapewnienia optymalnej wydajności dowolną liczbę usług, jak to możliwe, należy określić tego samego udostępnionego dziennika. Udostępnione pliki dziennika powinna zostać umieszczona na dyskach, które są używane wyłącznie do pliku dziennika udostępniony do zmniejszenia rywalizacji przepływu head. Oczekuje się, że te wartości muszą można zmienić tylko w rzadkich przypadkach.

