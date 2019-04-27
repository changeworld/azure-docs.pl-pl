---
title: Konfigurowanie usług Reliable Services usługi Azure Service Fabric | Dokumentacja firmy Microsoft
description: Dowiedz się więcej o konfigurowaniu stanowych usług Reliable Services w usłudze Azure Service Fabric.
services: Service-Fabric
documentationcenter: .net
author: sumukhs
manager: chackdan
editor: vturecek
ms.assetid: 9f72373d-31dd-41e3-8504-6e0320a11f0e
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 10/02/2017
ms.author: sumukhs
ms.openlocfilehash: 8ddb5d0566c57dd1d507d543ac53c0975a83dd43
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60723566"
---
# <a name="configure-stateful-reliable-services"></a>Konfigurowanie stanowych usług reliable services
Istnieją dwa rodzaje ustawień konfiguracji dla usług reliable services. Jeden zestaw jest globalne dla wszystkich usług reliable services w klastrze, a drugi zestaw zależy od określonej usługi reliable service.

## <a name="global-configuration"></a>Konfigurację globalną
Konfiguracja globalnej usługi reliable service została określona w manifeście klastra dla klastra, w sekcji KtlLogger. Umożliwia konfigurację lokalizacji udostępnionej dziennika i rozmiaru oraz limity pamięci globalnej, używane przez rejestrator. Manifest klastra jest pojedynczy plik XML, który przechowuje ustawienia i konfiguracje, które są stosowane do wszystkich węzłów i usług w klastrze. Plik jest zwykle nazywany ClusterManifest.xml. Możesz zobaczyć klastra manifestu dla klastra przy użyciu polecenia programu powershell Get-ServiceFabricClusterManifest.

### <a name="configuration-names"></a>Nazwy konfiguracji
| Name (Nazwa) | Jednostka | Wartość domyślna | Uwagi |
| --- | --- | --- | --- |
| WriteBufferMemoryPoolMinimumInKB |Kilobajty |8388608 |Minimalna liczba KB do przydzielenia w trybie jądra dla puli pamięci buforu zapisu rejestratora. Tej puli pamięci jest używana do buforowania informacji o stanie przed zapisywania na dysku. |
| WriteBufferMemoryPoolMaximumInKB |Kilobajty |Bez ograniczeń |Maksymalny rozmiar, w której rejestratora zapisuje bufora pamięci puli można powiększać. |
| SharedLogId |Identyfikator GUID |"" |Określa unikatowy identyfikator GUID służących do identyfikowania domyślny udostępniony plik dziennika używana przez wszystkich usług reliable services we wszystkich węzłach w klastrze, których nie należy określać SharedLogId w ich konfiguracji specyficznej dla usługi. Jeśli określono SharedLogId, następnie SharedLogPath musi być także określona. |
| SharedLogPath |W pełni kwalifikowanej nazwy ścieżki |"" |Określa pełną ścieżkę, gdzie plik dziennika udostępnione używane przez wszystkich usług reliable services we wszystkich węzłach w klastrze, których nie należy określać SharedLogPath w ich konfiguracji specyficznej dla usługi. Jednak jeśli SharedLogPath jest określona, opcja SharedLogId musi być także określona. |
| SharedLogSizeInMB |Megabajty |8192 |Określa liczbę MB miejsca na dysku statycznie przydzielić dla udostępnionego dziennika. Wartość musi być 2048 lub większą. |

W Azure ARM lub szablon JSON w środowisku lokalnym w poniższym przykładzie pokazano, jak można zmienić dziennik transakcji udostępnione, utworzonej w taki sposób, aby utworzyć kopię żadnych elementów reliable collections usług stanowych.

    "fabricSettings": [{
        "name": "KtlLogger",
        "parameters": [{
            "name": "SharedLogSizeInMB",
            "value": "4096"
        }]
    }]

### <a name="sample-local-developer-cluster-manifest-section"></a>Przykładowe sekcji manifestu klastra lokalnego dla deweloperów
Jeśli chcesz zmienić na lokalne Środowisko deweloperskie, należy edytować plik lokalny clustermanifest.xml.

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
Rejestrator ma globalnej puli pamięci zaalokowanej z pamięci niestronicowana jądra, która jest dostępna dla wszystkich usług reliable services w węźle do buforowania danych o stanie, zanim są zapisywane w dzienniku dedykowanych skojarzone z repliki usługi reliable service. Rozmiar puli jest kontrolowana przez ustawienia WriteBufferMemoryPoolMinimumInKB i WriteBufferMemoryPoolMaximumInKB. WriteBufferMemoryPoolMinimumInKB Określa początkowy rozmiar tej puli pamięci i najmniejszy rozmiar, do której może spowodować zmniejszenie puli pamięci. WriteBufferMemoryPoolMaximumInKB jest najwyższym rozmiar puli pamięci może. Każda replika usługi reliable service, która jest otwierana może zwiększyć rozmiar puli pamięci o kwotę system określić maksymalnie WriteBufferMemoryPoolMaximumInKB. Jeśli istnieje więcej zapotrzebowanie na pamięć z puli pamięci niż dostępna, żądania dotyczące pamięci będą opóźnione aż do pamięci. W związku z tym jeśli puli pamięci buforu zapisu jest zbyt mały dla określonej konfiguracji następnie może to spowodować obniżenie wydajności.

Ustawienia SharedLogId i SharedLogPath zawsze są używane razem do definiowania identyfikator GUID i lokalizację dziennika udostępnionego domyślnego dla wszystkich węzłów w klastrze. Domyślny dziennik udostępnione jest używany dla wszystkich usług reliable services, które określają ustawienia settings.xml dla określonej usługi. Najlepszą wydajność udostępnione pliki dziennika powinny być umieszczane na dyskach, które są używane wyłącznie do pliku dziennika udostępnione do zmniejszenia stopnia rywalizacji.

SharedLogSizeInMB określa ilość miejsca na dysku do przydzielenia dla dziennika udostępnionego domyślne we wszystkich węzłach.  SharedLogId i SharedLogPath nie muszą być podane w kolejności dla SharedLogSizeInMB należy określić.

## <a name="service-specific-configuration"></a>Określonej konfiguracji usługi
Można modyfikować konfiguracje domyślne stanowych usług Reliable Services, przy użyciu pakietu konfiguracji (Konfiguracja) lub implementacji usługi (kod).

* **Config** — Konfiguracja przy użyciu pakietu konfiguracji odbywa się przez zmienianie pliku Settings.xml, który jest generowany w katalogu głównym pakietu Microsoft Visual Studio w folderze konfiguracji dla każdej usługi w aplikacji.
* **Kod** — Konfiguracja za pośrednictwem kodu odbywa się przez utworzenie ReliableStateManager, przy użyciu obiektu ReliableStateManagerConfiguration z zestawem odpowiednie opcje.

Domyślnie środowisko uruchomieniowe usługi Azure Service Fabric szuka nazwy sekcji wstępnie zdefiniowane w pliku Settings.xml i zużywa wartości konfiguracji podczas tworzenia podstawowych składników środowiska wykonawczego.

> [!NOTE]
> Czy **nie** usuwanie nazwy sekcji z następujących konfiguracji w pliku Settings.xml, który jest generowany w rozwiązaniu Visual Studio, jeśli nie chcesz konfigurować usługi za pomocą kodu.
> Zmiana nazwy nazwy pakietu lub sekcji konfiguracji będzie wymagać zmiany w kodzie, podczas konfigurowania ReliableStateManager.
> 
> 

### <a name="replicator-security-configuration"></a>Konfiguracja zabezpieczeń replikatora
Konfiguracje zabezpieczeń replikatora są używane do zabezpieczenia kanału komunikacyjnego, który jest używany podczas replikacji. Oznacza to, usługi nie będą mogli zobaczyć siebie nawzajem ruch związany z replikacją, zapewnia, że danych, zapewniając wysoką dostępność, również jest bezpieczne. Domyślnie puste zabezpieczeń sekcji konfiguracji uniemożliwia zabezpieczeń replikacji.

> [!IMPORTANT]
> W węzłach systemu Linux certyfikaty muszą być w formacie PEM. Aby dowiedzieć się więcej na temat znajdowania i konfigurowanie certyfikatów dla systemu Linux, zobacz [Konfigurowanie certyfikatów w systemie Linux](./service-fabric-configure-certificates-linux.md). 
> 
> 

### <a name="default-section-name"></a>Domyślna nazwa sekcji
ReplicatorSecurityConfig

> [!NOTE]
> Aby zmienić nazwę tej sekcji, zastąp wartość parametru replicatorSecuritySectionName Konstruktor ReliableStateManagerConfiguration, tworząc ReliableStateManager dla tej usługi.
> 
> 

### <a name="replicator-configuration"></a>Replikator konfiguracji
Konfiguracje replikatora skonfiguruj replikatora, który jest odpowiedzialny za wprowadzanie stanu stanowej usługi Reliable Service o wysokiej niezawodności, replikacji i przechowywanie stanu lokalnie.
Domyślna konfiguracja jest generowany przez szablon programu Visual Studio i powinny wystarczyć. W tej sekcji zawiera informacje o dodatkowych konfiguracjach, które można dostrajanie replikatora.

### <a name="default-section-name"></a>Domyślna nazwa sekcji
ReplicatorConfig

> [!NOTE]
> Aby zmienić nazwę tej sekcji, zastąp wartość parametru replicatorSettingsSectionName Konstruktor ReliableStateManagerConfiguration, tworząc ReliableStateManager dla tej usługi.
> 
> 

### <a name="configuration-names"></a>Nazwy konfiguracji
| Name (Nazwa) | Jednostka | Wartość domyślna | Uwagi |
| --- | --- | --- | --- |
| BatchAcknowledgementInterval |Sekundy |0.015 |Okres czasu, dla którego replikatora na pomocniczy czeka po otrzymaniu operacji przed wysłaniem z powrotem do podstawowej potwierdzenia. Inne potwierdzeń, w celu przetworzonych w ramach tego interwału operacji są wysyłane jako jedną odpowiedź. |
| ReplicatorEndpoint |ND |Brak wartości domyślnej — wymagany parametr |Ustaw adres IP i port, który Replikator podstawowy/pomocniczy będzie używany do komunikowania się z innymi replikatorów w replice. To powinien odwoływać się do zasobu punktu końcowego TCP w manifeście usługi. Zapoznaj się [zasoby manifestu usługi](service-fabric-service-manifest-resources.md) Aby dowiedzieć się więcej o definiowaniu punktu końcowego zasobów w manifeście usługi. |
| MaxPrimaryReplicationQueueSize |Liczba operacji |8192 |Maksymalna liczba operacji w kolejce podstawowego. Operacja są zwalniane, gdy Replikator podstawowy otrzyma potwierdzenia od wszystkich replikatorów dodatkowej. Ta wartość musi być większa niż 64 i potęgą liczby 2. |
| MaxSecondaryReplicationQueueSize |Liczba operacji |16384 |Maksymalna liczba operacji w kolejce dodatkowej. Operacja nie zostanie zwolniona po wprowadzeniu swojego stanu o wysokiej dostępności za pośrednictwem trwałości. Ta wartość musi być większa niż 64 i potęgą liczby 2. |
| CheckpointThresholdInMB |MB |50 |Ilość miejsca w dzienniku plików po upływie którego stan jest utworzono punkt kontrolny. |
| MaxRecordSizeInKB |KB |1024 |Największy rozmiar rekordu, który replikatora może zapisywać w dzienniku. Ta wartość musi być wielokrotnością liczby 4 i większa niż 16. |
| MinLogSizeInMB |MB |0 (systemowy określona) |Minimalny rozmiar dziennika transakcji. Dziennik nie będzie można obciąć rozmiar poniżej tego ustawienia. wartość 0 wskazuje, że replikatora określi minimalny rozmiar dziennika. Zwiększenie tej wartości zwiększa możliwości wykonywania częściowych i przyrostowych kopii zapasowych od szanse rekordy odpowiedniego dziennika, które spowodowało obcięcie jest obniżony. |
| TruncationThresholdFactor |współczynnik |2 |Określa, w jaki rozmiar dziennika zostanie wyzwolone obcięcie. Obcięcie wartości progowej, jest określana przez MinLogSizeInMB pomnożona przez TruncationThresholdFactor. TruncationThresholdFactor musi być większa niż 1. MinLogSizeInMB * TruncationThresholdFactor musi być mniejsza niż MaxStreamSizeInMB. |
| ThrottlingThresholdFactor |współczynnik |4 |Określa, w jaki rozmiar dziennika repliki rozpocznie się ograniczane. Próg ograniczania (w MB) jest określana przez Max ((MinLogSizeInMB * ThrottlingThresholdFactor),(CheckpointThresholdInMB * ThrottlingThresholdFactor)). Próg ograniczania (w MB) musi być większy niż próg obcięcie (w MB). Próg obcięcie (w MB) musi być mniejsza niż MaxStreamSizeInMB. |
| MaxAccumulatedBackupLogSizeInMB |MB |800 |Maksymalna liczba zgromadzonych rozmiar (w MB) dzienniki kopii zapasowych w łańcuchu danego dziennika kopii zapasowej. Przyrostowe żądania tworzenia kopii zapasowej zakończy się niepowodzeniem, jeśli przyrostowej kopii zapasowej może wygenerować kopii zapasowej dziennika, który spowodowałoby skumulowana dzienniki kopii zapasowych od odpowiednich pełnej kopii zapasowej będzie większy niż ten rozmiar. W takim przypadku użytkownik musi wykonać pełną kopię zapasową. |
| SharedLogId |Identyfikator GUID |"" |Określa unikatowy identyfikator GUID służących do identyfikowania pliku dziennika udostępnione używane przy użyciu tej repliki. Zazwyczaj usług nie należy używać tego ustawienia. Jednak jeśli SharedLogId jest określona, opcja SharedLogPath musi być także określona. |
| SharedLogPath |W pełni kwalifikowanej nazwy ścieżki |"" |Określa pełną ścieżkę, w której zostanie utworzony plik dziennika udostępnionego dla tej repliki. Zazwyczaj usług nie należy używać tego ustawienia. Jednak jeśli SharedLogPath jest określona, opcja SharedLogId musi być także określona. |
| SlowApiMonitoringDuration |Sekundy |300 |Ustawia interwał monitorowania dla zarządzanych wywołań interfejsu API. Przykład: podany przez użytkownika funkcji wywołania zwrotnego kopii zapasowej. Po upływie interwału raport o kondycji ostrzeżenia będą wysyłane do Menedżera kondycji. |
| LogTruncationIntervalSeconds |Sekundy |0 |Można skonfigurować interwał, w dzienniku, które będą inicjowane obcięcie dla każdej repliki. Służy do upewnij się, że dziennik jest również obcięty, na podstawie czasu, a nie tylko rozmiar dziennika. To ustawienie wymusza także przeczyszczania wpisów usuniętych w niezawodnym słowniku. Dlatego można użyć zapewnienie usunięte elementy zostaną usunięte w odpowiednim czasie. |

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


### <a name="sample-configuration-file"></a>Przykładowy plik konfiguracji
```xml
<?xml version="1.0" encoding="utf-8"?>
<Settings xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/2011/01/fabric">
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
BatchAcknowledgementInterval kontroluje opóźnienie replikacji. Wartość "0" powoduje najniższe możliwe opóźnienie kosztem przepływności (jak więcej wiadomości potwierdzenia musi być wysyłane i przetwarzane, zawierający mniejszą liczbę potwierdzeń).
Im większa wartość BatchAcknowledgementInterval, tym większe ogólną przepustowość replikacji, kosztem większego opóźnienia operacji. Bezpośrednio przekłada się to opóźnienie zatwierdzenia transakcji.

Wartość CheckpointThresholdInMB określa ilość miejsca na dysku używanego przez replikatora do przechowywania informacji o stanie w dedykowanym pliku dziennika repliki. Zwiększa to do wartości większej niż domyślne może spowodować szybsze ponownej konfiguracji po dodaniu nowej repliki do zestawu. Jest to spowodowane transferu częściowe stanu, który ma miejsce ze względu na dostępność więcej historii działań w dzienniku. Potencjalnie może to zwiększyć czas odzyskiwania, repliki bazy danych po awarii.

Ustawienie MaxRecordSizeInKB określa maksymalny rozmiar rekordu, które mogą być zapisywane przez replikatora do pliku dziennika. W większości przypadków domyślny rozmiar rekordu 1024 KB jest optymalna. Jednak jeśli usługa powoduje większe elementów danych jako część informacji o stanie, ta wartość może być konieczne zwiększenie. Ma niewielkie korzyści w podejmowaniu MaxRecordSizeInKB mniejszych niż 1024, zgodnie z mniejszych rekordy, należy użyć tylko przestrzeń na mniejsze rekordu. Oczekujemy, że ta wartość będzie muszą zostać zmienione tylko sporadycznie.

Ustawienia SharedLogId i SharedLogPath są zawsze używane razem używać oddzielnych dziennikach udostępnionego z domyślny dziennik udostępnionego dla węzła usługi. Aby uzyskać najlepszą wydajność jak wiele usług, jak to możliwe, należy określić tego samego dziennika dla udostępnionego. Udostępnione pliki dziennika powinny być umieszczone na dyskach, które są używane wyłącznie do pliku dziennika udostępnione do zmniejszenia stopnia rywalizacji głównego przepływu. Oczekujemy, że ta wartość będzie muszą zostać zmienione tylko sporadycznie.

## <a name="next-steps"></a>Kolejne kroki
* [Debugowanie aplikacji usługi Service Fabric w programie Visual Studio](service-fabric-debugging-your-application.md)
* [Dokumentacja dla deweloperów dla usług Reliable Services](https://msdn.microsoft.com/library/azure/dn706529.aspx)

