---
title: Zmienianie ustawień reliabledictionaryactorstateprovider
description: Dowiedz się więcej o konfigurowaniu stanowych aktorów usługi Azure Service tego typu ReliableDictionaryActorStateProvider.
author: sumukhs
ms.topic: conceptual
ms.date: 10/2/2017
ms.author: sumukhs
ms.openlocfilehash: fbd6f7cd3ade753c659464522408aa715cce48f9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75609744"
---
# <a name="configuring-reliable-actors--reliabledictionaryactorstateprovider"></a>Konfigurowanie niezawodnych aktorów — niezawodny program ReliableDictionaryActorStateProvider
Domyślną konfigurację programu ReliableDictionaryActorStateProvider można zmodyfikować, zmieniając plik settings.xml wygenerowany w katalogu głównym pakietu programu Visual Studio w folderze Config dla określonego aktora.

Środowisko uruchomieniowe sieci szkieletowej usług Azure wyszukuje wstępnie zdefiniowane nazwy sekcji w pliku settings.xml i zużywa wartości konfiguracji podczas tworzenia podstawowych składników środowiska uruchomieniowego.

> [!NOTE]
> **Nie** należy usuwać ani modyfikować nazw sekcji następujących konfiguracji w pliku settings.xml, który jest generowany w rozwiązaniu programu Visual Studio.
> 
> 

Istnieją również ustawienia globalne, które wpływają na konfigurację ReliableDictionaryActorStateProvider.

## <a name="global-configuration"></a>Konfiguracja globalna
Konfiguracja globalna jest określona w manifeście klastra dla klastra w sekcji KtlLogger. Umożliwia konfigurację lokalizacji i rozmiaru udostępnionego dziennika oraz limitów pamięci globalnej używanych przez rejestratora. Należy zauważyć, że zmiany w manifeście klastra dotyczą wszystkich usług, które używają ReliableDictionaryActorStateProvider i niezawodne usługi stanowe.

Manifest klastra jest pojedynczym plikiem XML, który zawiera ustawienia i konfiguracje, które mają zastosowanie do wszystkich węzłów i usług w klastrze. Plik jest zazwyczaj nazywany ClusterManifest.xml. Manifest klastra dla klastra można zobaczyć za pomocą polecenia Get-ServiceFabricClusterManifest powershell.

### <a name="configuration-names"></a>Nazwy konfiguracji
| Nazwa | Jednostka | Wartość domyślna | Uwagi |
| --- | --- | --- | --- |
| WriteBufferMemoryPoolMinimumInKB |Kilobajtów |8388608 |Minimalna liczba KB do przydzielenia w trybie jądra dla puli pamięci buforu zapisu rejestratora. Ta pula pamięci jest używana do buforowania informacji o stanie przed zapisaniem na dysku. |
| WriteBufferMemoryPoolMaximumInKB |Kilobajtów |Bez ograniczeń |Maksymalny rozmiar, do którego może wzrosnąć pula pamięci buforu zapisu rejestratora. |
| SharedLogId (ida użytkownika sharedlogid) |Identyfikator GUID |"" |Określa unikatowy identyfikator GUID używany do identyfikowania domyślnego udostępnionego pliku dziennika używanego przez wszystkie niezawodne usługi we wszystkich węzłach w klastrze, które nie określają identyfikatora SharedLogId w konfiguracji specyficznej dla usługi. Jeśli sharedlogid jest określony, a następnie SharedLogPath musi być również określony. |
| Ścieżka sharedlogpath |W pełni kwalifikowana nazwa ścieżki |"" |Określa w pełni kwalifikowaną ścieżkę, w której udostępniony plik dziennika używany przez wszystkie niezawodne usługi we wszystkich węzłach w klastrze, które nie określają SharedLogPath w konfiguracji specyficznej dla usługi. Jeśli jednak określono sharedlogpath, a następnie SharedLogId musi być również określony. |
| SharedLogSizeInMB |Megabajtów |8192 |Określa liczbę MB miejsca na dysku, które statycznie przydzielane jest dla udostępnionego dziennika. Wartość musi być 2048 lub większa. |

### <a name="sample-cluster-manifest-section"></a>Przykładowa sekcja manifestu klastra
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
Rejestrator ma globalną pulę pamięci przydzieloną z niestronicowanej pamięci jądra, która jest dostępna dla wszystkich niezawodnych usług w węźle do buforowania danych o stanie przed zapisaniem w dedykowanym dzienniku skojarzonym z niezawodną repliką usługi. Rozmiar puli jest kontrolowany przez ustawienia WriteBufferMemoryPoolMinimumInKB i WriteBufferMemoryPoolMaximumInKB. WriteBufferMemoryPoolMinimumInKB określa zarówno początkowy rozmiar tej puli pamięci, jak i najniższy rozmiar, do którego może się zmniejszyć pula pamięci. WriteBufferMemoryPoolMaximumInKB jest najwyższy rozmiar, do którego może rosnąć puli pamięci. Każda replika niezawodnej usługi, która jest otwarta może zwiększyć rozmiar puli pamięci przez system określony kwotę do WriteBufferMemoryPoolMaximumInKB. Jeśli istnieje większe zapotrzebowanie na pamięć z puli pamięci niż jest dostępna, żądania pamięci będą opóźnione, dopóki pamięć jest dostępna. W związku z tym, jeśli puli pamięci buforu zapisu jest zbyt mały dla określonej konfiguracji, a następnie wydajność może ucierpieć.

Ustawienia SharedLogId i SharedLogPath są zawsze używane razem do definiowania identyfikatora GUID i lokalizacji dla domyślnego udostępnionego dziennika dla wszystkich węzłów w klastrze. Domyślny dziennik udostępniony jest używany dla wszystkich niezawodnych usług, które nie określają ustawień w pliku settings.xml dla określonej usługi. Aby uzyskać najlepszą wydajność, udostępnione pliki dziennika powinny być umieszczane na dyskach, które są używane wyłącznie dla udostępnionego pliku dziennika w celu zmniejszenia rywalizacji.

SharedLogSizeInMB określa ilość miejsca na dysku do wstępnego przydzielenia domyślnego udostępnionego dziennika we wszystkich węzłach.  SharedLogId i SharedLogPath nie muszą być określone w celu sharedLogSizeInMB być określone.

## <a name="replicator-security-configuration"></a>Konfiguracja zabezpieczeń replikatora
Konfiguracje zabezpieczeń replikatora służą do zabezpieczania kanału komunikacyjnego używanego podczas replikacji. Oznacza to, że usługi nie widzą wzajemnego ruchu replikacji, zapewniając, że dane, które są udostępniane jako wysoce dostępne, są również bezpieczne.
Domyślnie pusta sekcja konfiguracji zabezpieczeń uniemożliwia bezpieczeństwo replikacji.

> [!IMPORTANT]
> W węzłach systemu Linux certyfikaty muszą być sformatowane w formacie PEM. Aby dowiedzieć się więcej o lokalizowaniu i konfigurowaniu certyfikatów dla systemu Linux, zobacz [Konfigurowanie certyfikatów w systemie Linux](./service-fabric-configure-certificates-linux.md). 
> 

### <a name="section-name"></a>Nazwa sekcji
&lt;ActorName&gt;ServiceReplicatorSecurityConfig

## <a name="replicator-configuration"></a>Konfiguracja replikatora
Konfiguracje replikatora są używane do konfigurowania replikatora, który jest odpowiedzialny za uczynienie stanu dostawcy stanu aktora wysoce niezawodne przez replikowanie i utrwalanie stanu lokalnie.
Domyślna konfiguracja jest generowana przez szablon programu Visual Studio i powinna wystarczyć. W tej sekcji o mówi o dodatkowych konfiguracjach, które są dostępne do strojenia replikatora.

### <a name="section-name"></a>Nazwa sekcji
&lt;ActorName&gt;ServiceReplicatorConfig

### <a name="configuration-names"></a>Nazwy konfiguracji
| Nazwa | Jednostka | Wartość domyślna | Uwagi |
| --- | --- | --- | --- |
| BatchAcknowledgementInterval (W przypadku danych: BatchAcknowledgementInterval) |Sekundy |0.015 |Okres, dla którego replikator w pomocniczej czeka po otrzymaniu operacji przed wysłaniem potwierdzenia do podstawowego. Wszelkie inne potwierdzenia, które mają być wysyłane do operacji przetworzonych w tym przedziale czasu są wysyłane jako jedna odpowiedź. |
| ReplicatorEndpoint (Punkt replikatora) |Nie dotyczy |Brak domyślnego parametru wymaganego |Adres IP i port, które replikator podstawowy/pomocniczy będzie używany do komunikowania się z innymi replikatorami w zestawie replik. Powinno to odwoływać się do punktu końcowego zasobu TCP w manifeście usługi. Zapoznaj się [z zasobów manifestu usługi,](service-fabric-service-manifest-resources.md) aby dowiedzieć się więcej o definiowaniu zasobów punktu końcowego w manifeście usługi. |
| Rozmiar MaxReplicationMessageSize |Bajty |50 MB |Maksymalny rozmiar danych replikacji, które mogą być przesyłane w jednej wiadomości. |
| Maksymalna liczba aplikacji MaxPrimaryReplicationQueueSize |Liczba operacji |8192 |Maksymalna liczba operacji w kolejce podstawowej. Operacja jest zwalniana po tym, jak replikator podstawowy otrzyma potwierdzenie od wszystkich replikatorów pomocniczych. Wartość ta musi być większa niż 64 i moc 2. |
| MaxSecondaryReplicationQueueSize |Liczba operacji |16384 |Maksymalna liczba operacji w kolejce pomocniczej. Operacja jest zwalniana po udostępnieniu jej stanu wysoce dostępne za pośrednictwem trwałości. Wartość ta musi być większa niż 64 i moc 2. |
| Punkt kontrolnyThresholdInMB |MB |200 |Ilość miejsca na plik dziennika, po którym stan jest punkt kontrolny. |
| MaxRecordSizeInKB |KB |1024 |Największy rozmiar rekordu, który replikator może zapisać w dzienniku. Ta wartość musi być wielokrotnością 4 i większą niż 16. |
| OptimizeLogForLowerDiskUsage |Wartość logiczna |true |Jeśli true, dziennik jest skonfigurowany tak, że dedykowany plik dziennika repliki jest tworzony przy użyciu pliku rozrzedzonego NTFS. Zmniejsza to rzeczywiste użycie miejsca na dysku dla pliku. Gdy false, plik jest tworzony ze stałymi alokacji, które zapewniają najlepszą wydajność zapisu. |
| SharedLogId (ida użytkownika sharedlogid) |Identyfikator GUID |"" |Określa unikatowy identyfikator guid służący do identyfikowania udostępnionego pliku dziennika używanego w tej replice. Zazwyczaj usługi nie powinny używać tego ustawienia. Jeśli jednak określono identyfikator SharedLogId, należy również określić sharedlogpath. |
| Ścieżka sharedlogpath |W pełni kwalifikowana nazwa ścieżki |"" |Określa w pełni kwalifikowaną ścieżkę, w której zostanie utworzony udostępniony plik dziennika dla tej repliki. Zazwyczaj usługi nie powinny używać tego ustawienia. Jeśli jednak określono sharedlogpath, a następnie SharedLogId musi być również określony. |

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
Parametr BatchAcknowledgementInterval steruje opóźnieniem replikacji. Wartość "0" powoduje najniższe możliwe opóźnienie, kosztem przepływności (ponieważ więcej komunikatów potwierdzenia musi być wysyłanych i przetwarzanych, z których każdy zawiera mniej potwierdzeń).
Im większa wartość batchacknowledgementInterval, tym wyższa całkowita przepływność replikacji, kosztem wyższego opóźnienia operacji. To bezpośrednio przekłada się na opóźnienie zatwierdzania transakcji.

Parametr CheckpointThresholdInMB steruje ilością miejsca na dysku, której replikator może używać do przechowywania informacji o stanie w dedykowanym pliku dziennika repliki. Zwiększenie tej wartości do wartości wyższej niż wartość domyślna może spowodować szybsze czasy ponownej konfiguracji, gdy nowa replika zostanie dodana do zestawu. Jest to spowodowane częściowym transferem stanu, który odbywa się ze względu na dostępność większej liczby operacji w dzienniku. Może to potencjalnie zwiększyć czas odzyskiwania repliki po awarii.

Jeśli ustawisz OptimizeForLowerDiskUsage do true, obszar pliku dziennika będzie nadmiernie aprowizowane, tak aby aktywne repliki mogą przechowywać więcej informacji o stanie w swoich plikach dziennika, podczas gdy nieaktywne repliki zużywają mniej miejsca na dysku. Dzięki temu można hostować więcej replik w węźle. Jeśli ustawisz OptimizeForLowerDiskUsage na false, informacje o stanie są zapisywane w plikach dziennika szybciej.

Ustawienie MaxRecordSizeInKB definiuje maksymalny rozmiar rekordu, który może być zapisany przez replikatora w pliku dziennika. W większości przypadków domyślny rozmiar rekordu 1024 KB jest optymalny. Jednak jeśli usługa powoduje, że większe elementy danych są częścią informacji o stanie, wartość ta może wymagać zwiększenia. Jest niewiele korzyści w podejmowaniu MaxRecordSizeInKB mniejsze niż 1024, jak mniejsze rekordy używać tylko miejsca potrzebnego do mniejszego rekordu. Oczekujemy, że wartość ta będzie musiała zostać zmieniona tylko w rzadkich przypadkach.

Ustawienia SharedLogId i SharedLogPath są zawsze używane razem, aby usługa używała oddzielnego udostępnionego dziennika z domyślnego udostępnionego dziennika dla węzła. Aby uzyskać najlepszą wydajność, jak najwięcej usług, jak to możliwe należy określić ten sam dziennik udostępniony. Udostępnione pliki dziennika powinny być umieszczane na dyskach, które są używane wyłącznie dla udostępnionego pliku dziennika, aby zmniejszyć rywalizację o ruch głowy. Oczekujemy, że te wartości będą musiały zostać zmienione tylko w rzadkich przypadkach.

