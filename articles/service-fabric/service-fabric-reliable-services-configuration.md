---
title: Skonfiguruj niezawodnej mikrousług Azure | Dokumentacja firmy Microsoft
description: Informacje na temat konfigurowania stanowe niezawodne usługi w sieci szkieletowej usług Azure.
services: Service-Fabric
documentationcenter: .net
author: sumukhs
manager: timlt
editor: vturecek
ms.assetid: 9f72373d-31dd-41e3-8504-6e0320a11f0e
ms.service: Service-Fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 10/02/2017
ms.author: sumukhs
ms.openlocfilehash: 3e058242840a3fa8b86c9ae9d5a0940cc02f04d2
ms.sourcegitcommit: 0fa8b4622322b3d3003e760f364992f7f7e5d6a9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/27/2018
ms.locfileid: "37020393"
---
# <a name="configure-stateful-reliable-services"></a>Skonfiguruj stanowe niezawodne usługi
Istnieją dwa zestawy ustawień konfiguracji niezawodne usługi. Jeden zestaw jest globalne dla wszystkich usług niezawodnej w klastrze, gdy drugi zestaw jest przeznaczony dla konkretnego niezawodnej usługi.

## <a name="global-configuration"></a>Konfigurację globalną
W manifeście klastra dla klastra, w sekcji KtlLogger określana jest Konfiguracja niezawodnej usługi globalne. Go umożliwia skonfigurowanie lokalizacji udostępnionej dziennika i rozmiaru i limity pamięci globalnej używane przez rejestratora. Plik manifestu klastra to pojedynczy plik XML, który przechowuje ustawienia i konfiguracje, które są stosowane do wszystkich węzłów i usług w klastrze. Plik jest zwykle nazywany ClusterManifest.xml. Widać klastra dla klastra przy użyciu polecenia programu powershell Get-ServiceFabricClusterManifest manifestu.

### <a name="configuration-names"></a>Nazwy konfiguracji
| Name (Nazwa) | Jednostka | Wartość domyślna | Uwagi |
| --- | --- | --- | --- |
| WriteBufferMemoryPoolMinimumInKB |Kilobajty |8388608 |Minimalna liczba KB przydzielić w trybie jądra dla puli pamięci buforu zapisu rejestratora. Tej puli pamięci jest używana do buforowania informacji o stanie przed zapisu na dysku. |
| WriteBufferMemoryPoolMaximumInKB |Kilobajty |Bez ograniczeń |Maksymalny rozmiar, do którego rejestratora zapisu puli bufora pamięci można powiększać. |
| SharedLogId |Identyfikator GUID |"" |Określa unikatowy identyfikator GUID służących do identyfikowania domyślne udostępniony plik dziennika używanych przez wszystkie usługi niezawodnego we wszystkich węzłach w klastrze, które nie określają SharedLogId ich określonej konfiguracji usługi. Jeśli określono SharedLogId, to SharedLogPath należy również określić. |
| SharedLogPath |W pełni kwalifikowana nazwa |"" |Określa pełną ścieżkę, w którym plik dziennika udostępniony używany przez wszystkie niezawodnej usługi na wszystkich węzłach w klastrze, które nie określają SharedLogPath ich określonej konfiguracji usługi. Jednak jeśli SharedLogPath jest określona, to SharedLogId należy również określić. |
| SharedLogSizeInMB |Megabajty |8192 |Określa liczbę MB miejsca na dysku przydzielić statycznie dla dziennika udostępniony. Wartość musi być 2048 lub większy. |

Azure ARM lub lokalnymi JSON szablonu w poniższym przykładzie pokazano, jak zmienić dziennika transakcji udostępnionego, który jest tworzony kopii wszystkie kolekcje niezawodnych usług stanowych.

    "fabricSettings": [{
        "name": "KtlLogger",
        "parameters": [{
            "name": "SharedLogSizeInMB",
            "value": "4096"
        }]
    }]

### <a name="sample-local-developer-cluster-manifest-section"></a>Sekcja manifestu klastra lokalnego developer próbki
Aby zmienić to ustawienie w środowisku lokalnym programowanie, należy edytować plik clustermanifest.xml lokalnego.

```xml
   <Section Name="KtlLogger">
     <Parameter Name="SharedLogSizeInMB" Value="4096"/>
     <Parameter Name="WriteBufferMemoryPoolMinimumInKB" Value="8192" />
     <Parameter Name="WriteBufferMemoryPoolMaximumInKB" Value="8192" />
     <Parameter Name="SharedLogId" Value="{7668BB54-FE9C-48ed-81AC-FF89E60ED2EF}"/>
     <Parameter Name="SharedLogPath" Value="f:\SharedLog.Log"/>
   </Section>
```

### <a name="remarks"></a>Uwagi
Rejestrator ma globalnej puli pamięci przydzielonej z pamięci niestronicowana jądra, która jest dostępna dla wszystkich usług niezawodnej w węźle do buforowania danych o stanie przed są zapisywane w dzienniku dedykowanych skojarzone z repliki niezawodnej usługi. Rozmiar puli jest kontrolowana przez ustawienia WriteBufferMemoryPoolMinimumInKB i WriteBufferMemoryPoolMaximumInKB. WriteBufferMemoryPoolMinimumInKB Określa początkowy rozmiar tej puli pamięci i rozmiar najniższy, do której może zmniejszyć rozmiar puli pamięci. WriteBufferMemoryPoolMaximumInKB jest najwyższym rozmiar, do której może zwiększyć się pula pamięci. Każda replika niezawodnej usługi, która jest otwarta może zwiększyć rozmiar puli pamięci o kwotę systemu ustalić maksymalnie WriteBufferMemoryPoolMaximumInKB. W przypadku żądanie więcej pamięci w puli pamięci niż jest dostępne, żądania dotyczące pamięci zostanie opóźnione aż do pamięci. W związku z tym jeśli puli pamięci buforu zapisu jest zbyt mały dla konkretnej konfiguracji następnie to spowodować obniżenie wydajności.

Ustawienia SharedLogId i SharedLogPath zawsze są używane razem do definiowania identyfikator GUID i lokalizacja dziennika udostępniony domyślnego dla wszystkich węzłów w klastrze. Dziennika udostępniony domyślny jest używany dla wszystkich usług niezawodne, które nie określają ustawienia w pliku settings.xml dla określonej usługi. Aby uzyskać najlepszą wydajność udostępnione pliki dziennika należy umieścić na dyskach, które są używane wyłącznie do pliku dziennika udostępniony do zmniejszenia rywalizacji.

SharedLogSizeInMB określa ilość miejsca na dysku do przydzielenia dla dziennika udostępniony domyślne we wszystkich węzłach.  SharedLogId i SharedLogPath nie trzeba określać aby SharedLogSizeInMB należy określić.

## <a name="service-specific-configuration"></a>Określonej konfiguracji usługi
Można modyfikować konfiguracje domyślne stanowe niezawodnych usług przy użyciu pakietu konfiguracji (Config) lub implementacji usługi (kod).

* **Config** -konfiguracji za pomocą pakietu konfiguracji odbywa się przez zmianę pliku Settings.xml, który jest generowany w folderze głównym pakietu Microsoft Visual Studio w folderze konfiguracji dla każdej usługi w aplikacji.
* **Kod** -konfiguracji za pomocą kodu jest realizowane przez utworzenie ReliableStateManager, za pomocą obiektu ReliableStateManagerConfiguration z zestawem odpowiednie opcje.

Domyślnie środowisko uruchomieniowe usługi sieć szkieletowa usług Azure szuka nazwy sekcji wstępnie zdefiniowanych w pliku Settings.xml i wykorzystuje wartości konfiguracji podczas tworzenia podstawowych składników środowiska wykonawczego.

> [!NOTE]
> Czy **nie** usunąć nazwy sekcji następujące konfiguracje w pliku Settings.xml, który jest generowany w rozwiązaniu Visual Studio, chyba że ma zostać skonfigurowany za pomocą kodu usługi.
> Zmiana nazwy konfiguracji pakietu lub sekcji nazw wymagają zmiany kodu podczas konfigurowania ReliableStateManager.
> 
> 

### <a name="replicator-security-configuration"></a>Konfiguracja zabezpieczeń replikatora
Replikator konfiguracji zabezpieczeń służą do zabezpieczania kanał komunikacyjny używany podczas replikacji. Oznacza to, usługi nie będą widoczne siebie nawzajem ruch związany z replikacją, zapewnia, że dane, które jest zyskuje dużą dostępność również jest bezpieczne. Domyślnie puste zabezpieczeń sekcji konfiguracji uniemożliwia zabezpieczeń replikacji.

> [!IMPORTANT]
> W węzłach Linux certyfikaty muszą być sformatowane PEM. Aby uzyskać więcej informacji o lokalizacji i konfigurowanie certyfikatów dla systemu Linux, zobacz [Konfigurowanie certyfikatów w systemie Linux](./service-fabric-configure-certificates-linux.md). 
> 
> 

### <a name="default-section-name"></a>Domyślna nazwa sekcji
ReplicatorSecurityConfig

> [!NOTE]
> Aby zmienić nazwę tej sekcji, należy zastąpić parametr replicatorSecuritySectionName do konstruktora ReliableStateManagerConfiguration podczas tworzenia ReliableStateManager dla tej usługi.
> 
> 

### <a name="replicator-configuration"></a>Konfiguracja replikatora
Konfiguracje replikatora skonfiguruj replikatora odpowiedzialną dokonywania stan stanowe usługi niezawodnego wysoce niezawodne replikacji i przechowywanie stanu lokalnie.
Domyślna konfiguracja jest generowany przez szablon programu Visual Studio i powinny wystarczyć. Ta sekcja zawiera informacje o dodatkowych konfiguracjach, które są dostępne dostroić replikatora.

### <a name="default-section-name"></a>Domyślna nazwa sekcji
ReplicatorConfig

> [!NOTE]
> Aby zmienić nazwę tej sekcji, należy zastąpić parametr replicatorSettingsSectionName do konstruktora ReliableStateManagerConfiguration podczas tworzenia ReliableStateManager dla tej usługi.
> 
> 

### <a name="configuration-names"></a>Nazwy konfiguracji
| Name (Nazwa) | Jednostka | Wartość domyślna | Uwagi |
| --- | --- | --- | --- |
| BatchAcknowledgementInterval |Sekundy |0.015 |Okres, dla którego replikatora na dodatkowej czeka po otrzymaniu operacji przed wysłaniem z powrotem do podstawowej potwierdzenia. Inne potwierdzeń na wysłanie operacji przetwarzane w ramach tego interwału są wysyłane jako jedna odpowiedź. |
| ReplicatorEndpoint |ND |Brak wartości domyślnej — wymagany parametr |Ustaw adres IP i port, który replikatora podstawowe i pomocnicze będzie używany do komunikowania się z innymi replikatorów w replice. To powinien odwoływać się do zasobu punkt końcowy protokołu TCP w manifeście usługi. Zapoznaj się [zasoby manifestu usługi](service-fabric-service-manifest-resources.md) Aby dowiedzieć się więcej o Definiowanie zasobów punktu końcowego w manifeście usługi. |
| MaxPrimaryReplicationQueueSize |Liczba operacji |8192 |Maksymalna liczba operacji w kolejce podstawowego. Operacja są zwalniane, gdy Replikator podstawowy otrzyma potwierdzenia od wszystkich dodatkowej replikatorów. Ta wartość musi być większa niż 64 i potęgą liczby 2. |
| MaxSecondaryReplicationQueueSize |Liczba operacji |16384 |Maksymalna liczba operacji w kolejce dodatkowej. Operacja są zwalniane po dokonaniu jej stan wysokiej dostępności za pośrednictwem trwałości. Ta wartość musi być większa niż 64 i potęgą liczby 2. |
| CheckpointThresholdInMB |MB |50 |Ilość miejsca pliku dziennika, po upływie którego stan jest w użyciu. |
| MaxRecordSizeInKB |KB |1024 |Największy rozmiar rekordu, który replikatora może zapisywać w dzienniku. Ta wartość musi być wielokrotnością liczby 4 i większa niż 16. |
| MinLogSizeInMB |MB |0 (systemowy ustalić) |Minimalny rozmiar dziennika transakcyjnych. Dziennik nie będzie można obciąć rozmiar poniżej tego ustawienia. wartość 0 wskazuje, że replikatora Określa minimalny rozmiar dziennika. Zwiększenie tej wartości zwiększa możliwości wykonywania częściowej kopii i przyrostowe kopie zapasowe od szanse rekordów dziennika odpowiednich obcięcie jest obniżony. |
| TruncationThresholdFactor |Współczynnik |2 |Określa, w jaki rozmiar dziennika zostanie wywołane obcięcie. Obcięcie wartości progowej, jest określana przez MinLogSizeInMB pomnożona przez TruncationThresholdFactor. TruncationThresholdFactor musi być większa niż 1. MinLogSizeInMB * TruncationThresholdFactor musi być mniejsza niż MaxStreamSizeInMB. |
| ThrottlingThresholdFactor |Współczynnik |4 |Określa, w jaki rozmiar dziennika repliki rozpocznie się ograniczane. Próg ograniczenia przepustowości (w MB) jest określana przez Max ((MinLogSizeInMB * ThrottlingThresholdFactor),(CheckpointThresholdInMB * ThrottlingThresholdFactor)). Próg ograniczenia przepustowości (w MB) musi być większy niż próg obcięcie (w MB). Próg obcięcie (w MB) musi być mniejsza niż MaxStreamSizeInMB. |
| MaxAccumulatedBackupLogSizeInMB |MB |800 |Maksymalna liczba zebranych elementów rozmiar (w MB) kopii zapasowych dzienników łańcucha danej kopii zapasowej dziennika. Przyrostowej kopii zapasowej żądań zakończy się niepowodzeniem, jeśli wygenerowanie kopii zapasowej dziennika, spowodowałoby skumulowany dzienniki kopii zapasowych od odpowiednich pełnej kopii zapasowej będzie większy niż rozmiar przyrostowej kopii zapasowej. W takim przypadku użytkownik musi wykonać pełną kopię zapasową. |
| SharedLogId |Identyfikator GUID |"" |Określa unikatowy identyfikator GUID służących do identyfikowania pliku dziennika udostępniony używany z tej repliki. Zazwyczaj usług nie należy używać tego ustawienia. Jednak jeśli SharedLogId jest określona, to SharedLogPath należy również określić. |
| SharedLogPath |W pełni kwalifikowana nazwa |"" |Określa pełną ścieżkę, w którym zostanie utworzony plik dziennika udostępniony dla tej repliki. Zazwyczaj usług nie należy używać tego ustawienia. Jednak jeśli SharedLogPath jest określona, to SharedLogId należy również określić. |
| SlowApiMonitoringDuration |Sekundy |300 |Określa interwał monitorowania zarządzanego interfejsu API. Przykład: podany przez użytkownika funkcja tworzenia kopii zapasowej wywołania zwrotnego. Po upływie interwału Raport kondycji ostrzeżenie zostanie wysłany do Menedżera kondycji. |

### <a name="sample-configuration-via-code"></a>Przykładowa konfiguracja przy użyciu kodu
```csharp
class Program
{
    /// <summary>
    /// This is the entry point of the service host process.
    /// </summary>
    static void Main()
    {
        ServiceRuntime.RegisterServiceAsync("HelloWorldStatefulType",
            context => new HelloWorldStateful(context, 
                new ReliableStateManager(context, 
        new ReliableStateManagerConfiguration(
                        new ReliableStateManagerReplicatorSettings()
            {
                RetryInterval = TimeSpan.FromSeconds(3)
                        }
            )))).GetAwaiter().GetResult();
    }
}    
```
```csharp
class MyStatefulService : StatefulService
{
    public MyStatefulService(StatefulServiceContext context, IReliableStateManagerReplica stateManager)
        : base(context, stateManager)
    { }
    ...
}
```


### <a name="sample-configuration-file"></a>Przykładowy plik konfiguracyjny
```xml
<?xml version="1.0" encoding="utf-8"?>
<Settings xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/2011/01/fabric">
   <Section Name="ReplicatorConfig">
      <Parameter Name="ReplicatorEndpoint" Value="ReplicatorEndpoint" />
      <Parameter Name="BatchAcknowledgementInterval" Value="0.05"/>
      <Parameter Name="CheckpointThresholdInMB" Value="512" />
   </Section>
   <Section Name="ReplicatorSecurityConfig">
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


### <a name="remarks"></a>Uwagi
BatchAcknowledgementInterval Określa opóźnienie replikacji. Wartość "0" powoduje najniższym opóźnieniu możliwe, kosztem przepływności (jak więcej komunikatów potwierdzenia musi być wysyłane i przetwarzane, zawierający mniejszą liczbę potwierdzeń).
Im większa wartość BatchAcknowledgementInterval, tym wyższy ogólną przepustowość replikacji, kosztem większego opóźnienia operacji. Bezpośrednio przekłada się to opóźnienie zatwierdzeń transakcji.

Wartość CheckpointThresholdInMB określa ilość miejsca na dysku używanego przez replikatora do przechowywania informacji o stanie w pliku dziennika dedykowanego repliki. Zwiększanie tego do wartości większej niż domyślny może spowodować szybsze ponownej konfiguracji po dodaniu nowej repliki do zestawu. Jest to spowodowane transferu stanu częściowego, który odbywa się z powodu dostępności historii więcej operacji w dzienniku. To potencjalnie spowodować wydłużenie czasu odzyskiwania repliki, po awarii.

Ustawienie MaxRecordSizeInKB określa maksymalny rozmiar rekordu, które mogą być zapisywane w pliku dziennika przez replikatora. W większości przypadków domyślny rozmiar rekordu 1024 KB jest optymalna. Jednak jeśli usługa powoduje większe elementy danych jako część informacji o stanie, ta wartość może być konieczne zwiększenia. Brak małego korzyści w podejmowaniu MaxRecordSizeInKB mniejszych niż 1024, jak mniejsze rekordów tylko przestrzeń na mniejsze rekordu. Oczekuje się, że ta wartość będzie muszą zostać zmienione tylko sporadycznie.

Ustawienia SharedLogId i SharedLogPath zawsze są używane razem aby używać oddzielnego dziennika udostępniony z domyślnego dziennika udostępniony dla węzła usługi. Dla zapewnienia optymalnej wydajności dowolną liczbę usług, jak to możliwe, należy określić tego samego udostępnionego dziennika. Udostępnione pliki dziennika powinna zostać umieszczona na dyskach, które są używane wyłącznie do pliku dziennika udostępniony do zmniejszenia rywalizacji przepływu head. Oczekuje się, że ta wartość będzie muszą zostać zmienione tylko sporadycznie.

## <a name="next-steps"></a>Kolejne kroki
* [Debugowanie aplikacji sieci szkieletowej usług w programie Visual Studio](service-fabric-debugging-your-application.md)
* [Dokumentacja dla deweloperów dla niezawodne usługi](https://msdn.microsoft.com/library/azure/dn706529.aspx)

