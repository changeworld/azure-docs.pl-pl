---
title: Zmień ustawienia klastra usługi sieć szkieletowa usług Azure | Dokumentacja firmy Microsoft
description: W tym artykule opisano ustawienia sieci szkieletowej i zasad uaktualniania sieci szkieletowej, które można dostosować.
services: service-fabric
documentationcenter: .net
author: aljo-microsoft
manager: timlt
editor: ''
ms.assetid: 7ced36bf-bd3f-474f-a03a-6ebdbc9677e2
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: reference
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/27/2018
ms.author: aljo
ms.openlocfilehash: 84f8827a58d7f3c5dcc32943d2ba891b02c1e1ab
ms.sourcegitcommit: d1eefa436e434a541e02d938d9cb9fcef4e62604
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/28/2018
ms.locfileid: "37083196"
---
# <a name="customize-service-fabric-cluster-settings-and-fabric-upgrade-policy"></a>Dostosowywanie ustawień klastra sieci szkieletowej usług i zasady uaktualniania sieci szkieletowej
Ten dokument zawiera informacje dotyczące dostosować różne ustawienia sieci szkieletowej i sieci szkieletowej uaktualniania zasad dla klastra usługi sieć szkieletowa usług. Możesz dostosować je za pomocą [portalu Azure](https://portal.azure.com) lub przy użyciu szablonu usługi Azure Resource Manager.

> [!NOTE]
> Nie wszystkie ustawienia są dostępne w portalu. W przypadku, gdy ustawienie wymienionych poniżej nie jest dostępny za pośrednictwem portalu dostosować go za pomocą szablonu usługi Azure Resource Manager.
> 

## <a name="description-of-the-different-upgrade-policies"></a>Opis różnych zasad uaktualniania

- **Dynamiczne** — zmiany w konfiguracji dynamicznej nie powodują ponowne uruchomienia procesu procesów usługi sieć szkieletowa usług lub procesów hosta usługi. 
- **Statyczne** — zmian do konfiguracji statycznej spowoduje ponowne uruchomienie w celu korzystania z zmiany węzeł sieci szkieletowej usług. Usługi w węzłach zostanie uruchomiona ponownie.
- **NotAllowed** — nie można modyfikować tych ustawień. Zmiana tych ustawień wymaga niszczone klastra i utworzyć nowy klaster. 

## <a name="customize-cluster-settings-using-resource-manager-templates"></a>Dostosowywanie ustawień klastra przy użyciu szablonów usługi Resource Manager
Poniższe kroki zilustrować dodać nowe ustawienie *MaxDiskQuotaInMB* do *diagnostyki* sekcji.

1. Przejdź do strony https://resources.azure.com
2. Przejdź do subskrypcji, rozwijając **subskrypcje** -> **\<Twojej subskrypcji >** -> **resourceGroups**  ->   **\<Your grupa zasobów >** -> **dostawców** -> **Microsoft.ServiceFabric**  ->  **klastrów** -> **\<Your nazwa klastra >**
3. W prawym górnym rogu, wybierz **odczytu/zapisu.**
4. Wybierz **Edytuj** i zaktualizuj `fabricSettings` elementu JSON i Dodaj nowy element:

```
      {
        "name": "Diagnostics",
        "parameters": [
          {
            "name": "MaxDiskQuotaInMB",
            "value": "65536"
          }
        ]
      }
```

Poniżej znajduje się lista sieci szkieletowej ustawień, które można dostosować, uporządkowane według sekcji.

## <a name="applicationgatewayhttp"></a>Bramy aplikacji/Http
| **Parametr** | **Dozwolone wartości** | **Zasady uaktualniania** | **Wskazówki lub krótki opis** |
| --- | --- | --- | --- |
|ApplicationCertificateValidationPolicy|ciąg, domyślny jest L "None"|Statyczny| ApplicationCertificateValidationPolicy: Brak: nie zweryfikować certyfikatu serwera; powiodło się żądanie. ServiceCertificateThumbprints: Odwoływać się do konfiguracji ServiceCertificateThumbprints rozdzielana przecinkami lista odciski palców certyfikatów zdalnego ufające zwrotnego serwera proxy. ServiceCommonNameAndIssuer: Odwoływać się do konfiguracji ServiceCommonNameAndIssuer dla podmiotu nazwy i wystawców odcisk palca certyfikatów zdalnego ufające zwrotnego serwera proxy. |
|BodyChunkSize |Uint — wartość domyślna to 16384 |Dynamiczny| Zapewnia rozmiar fragmentu w bajtach używany do odczytu treści. |
|CrlCheckingFlag|uint, domyślny jest 0x40000000 |Dynamiczny| Flagi weryfikacji łańcucha certyfikatu aplikacji/usługi; np. sprawdzania listy CRL 0x10000000 CERT_CHAIN_REVOCATION_CHECK_END_CERT 0x20000000 CERT_CHAIN_REVOCATION_CHECK_CHAIN 0x40000000 CERT_CHAIN_REVOCATION_CHECK_CHAIN_EXCLUDE_ROOT 0x80000000 ustawienie CERT_CHAIN_REVOCATION_CHECK_CACHE_ONLY 0 Wyłącza listy CRL sprawdzanie pełną listę obsługiwanych wartości jest opisane przez wartość elementu dwFlags z CertGetCertificateChain: http://msdn.microsoft.com/library/windows/desktop/aa376078(v=vs.85).aspx  |
|DefaultHttpRequestTimeout |Czas w sekundach. domyślna to 120 |Dynamiczny|Określ zakres czasu w sekundach.  Udostępnia domyślny limit czasu żądania żądań http przetwarzanych przez bramę aplikacji http. |
|ForwardClientCertificate|wartość logiczna, domyślna to FALSE|Dynamiczny| |
|GatewayAuthCredentialType |ciąg, domyślną jest "None" |Statyczny| Wskazuje typ poświadczeń zabezpieczeń w http aplikacji bramy punktu końcowego prawidłowe wartości to "Brak / X 509. |
|GatewayX509CertificateFindType |ciąg, domyślna ma "postać FindByThumbprint" |Dynamiczny| Wskazuje sposób wyszukiwania certyfikatu w magazynie określonym przez wartość obsługiwane GatewayX509CertificateStoreName: postać FindByThumbprint; FindBySubjectName. |
|GatewayX509CertificateFindValue | ciąg, domyślna to "" |Dynamiczny| Wartość filtru wyszukiwania używana do lokalizowania certyfikatu bramy aplikacji http. Ten certyfikat jest skonfigurowany w punkcie końcowym https i mogą służyć do weryfikacji tożsamości aplikacji w razie potrzeby przez usługi. FindValue jest najpierw wyszukiwane; a jeśli nie istnieją; FindValueSecondary będą wyszukiwane. |
|GatewayX509CertificateFindValueSecondary | ciąg, domyślna to "" |Dynamiczny|Wartość filtru wyszukiwania używana do lokalizowania certyfikatu bramy aplikacji http. Ten certyfikat jest skonfigurowany w punkcie końcowym https i mogą służyć do weryfikacji tożsamości aplikacji w razie potrzeby przez usługi. FindValue jest najpierw wyszukiwane; a jeśli nie istnieją; FindValueSecondary będą wyszukiwane.|
|GatewayX509CertificateStoreName |jest domyślny ciąg "Moje" |Dynamiczny| Nazwa magazynu certyfikatu X.509, który zawiera certyfikat dla protokołu http bramy aplikacji. |
|HttpRequestConnectTimeout|Zakres czasu, domyślnie jest Common::TimeSpan::FromSeconds(5)|Dynamiczny|Określ zakres czasu w sekundach.  Zapewnia limit czasu połączenia dla żądania http są wysyłane z bramy aplikacji http.  |
|IgnoreCrlOfflineError|wartość logiczna, domyślna ma wartość TRUE|Dynamiczny|Określa, czy zignorować błąd w trybie offline listy CRL na potrzeby weryfikacji certyfikatu aplikacji/usługi. |
|IsEnabled |Wartość logiczna, wartość domyślna to false |Statyczny| Włącza/wyłącza HttpApplicationGateway. HttpApplicationGateway jest domyślnie wyłączona i tej konfiguracji musi być ustawiona, aby je włączyć. |
|NumberOfParallelOperations | Uint — wartość domyślna to 5000 |Statyczny|Liczba odczytów do wysłania do kolejki serwera http. Kontroluje to liczbę żądań, które mogą być spełnione przez HttpGateway. |
|RemoveServiceResponseHeaders|ciąg, domyślny jest L "Data; Serwer"|Statyczny|Średnikiem / rozdzielana przecinkami lista nagłówków odpowiedzi, które zostaną usunięte z odpowiedzi usługi; przed przekazaniem ich do klienta. Jeśli ustawiono to ciąg pusty; Przekaż wszystkie nagłówki, które są zwracane przez usługę jako — jest. tj Nie zastępuj datę i serwera |
|ResolveServiceBackoffInterval |Czas w sekundach, domyślna to 5 |Dynamiczny|Określ zakres czasu w sekundach.  Pozwala rozwiązać domyślny wycofania interwał przed ponowną próbą wykonania nieudanej operacji usługi. |
|SecureOnlyMode|wartość logiczna, domyślna to FALSE|Dynamiczny| SecureOnlyMode: true: zwrotny serwer Proxy tylko przekazać dalej do usług, które publikują bezpieczne punkty końcowe. false: zwrotny serwer Proxy może przekazywać żądania do bezpiecznego/niezabezpieczonego punktów końcowych.  |
|ServiceCertificateThumbprints|ciąg, domyślny jest L""|Dynamiczny| |

## <a name="applicationgatewayhttpservicecommonnameandissuer"></a>ServiceCommonNameAndIssuer/Http/bramy aplikacji
| **Parametr** | **Dozwolone wartości** | **Zasady uaktualniania** | **Wskazówki lub krótki opis** |
| --- | --- | --- | --- |
|PropertyGroup|X509NameMap, domyślna wartość to Brak|Dynamiczny|  |

## <a name="backuprestoreservice"></a>BackupRestoreService
| **Parametr** | **Dozwolone wartości** | **Zasady uaktualniania** | **Wskazówki lub krótki opis** |
| --- | --- | --- | --- |
|MinReplicaSetSize|int, domyślne to 0|Statyczny|MinReplicaSetSize dla BackupRestoreService |
|PlacementConstraints|wstring, domyślny jest L""|Statyczny| PlacementConstraints BackupRestore usługi |
|SecretEncryptionCertThumbprint|wstring, domyślny jest L""|Dynamiczny|Odcisk palca certyfikatu szyfrowania tajny X509 |
|SecretEncryptionCertX509StoreName|wstring, domyślny jest L "Moje"|  Dynamiczny|    Oznacza to certyfikat używany do szyfrowania i odszyfrowywania poświadczenia magazynu certyfikatów nazwa X.509, który jest używany do szyfrowania, odszyfrowywania poświadczeń magazynu używane przez usługę Przywracanie kopii zapasowej |
|Wartość TargetReplicaSetSize|int, domyślne to 0|Statyczny| TargetReplicaSetSize dla BackupRestoreService |

## <a name="clustermanager"></a>ClusterManager
| **Parametr** | **Dozwolone wartości** | **Zasady uaktualniania** | **Wskazówki lub krótki opis** |
| --- | --- | --- | --- |
|EnableDefaultServicesUpgrade | Wartość logiczna, wartość domyślna to false |Dynamiczny|Włącz uaktualnianie usług domyślnych podczas uaktualniania aplikacji. Opisów domyślnej usługi zostaną zastąpione po uaktualnieniu. |
|FabricUpgradeHealthCheckInterval |Czas w sekundach, domyślna to 60 |Dynamiczny|Częstotliwość stan kondycji Sprawdź podczas uaktualniania monitorowanych sieci szkieletowej |
|FabricUpgradeStatusPollInterval |Czas w sekundach, domyślna to 60 |Dynamiczny|Stan uaktualnienia częstotliwość sondowania sieci szkieletowej. Ta wartość określa częstotliwość aktualizacji dla każde wywołanie GetFabricUpgradeProgress |
|ImageBuilderTimeoutBuffer |Czas w sekundach, domyślna to 3 |Dynamiczny|Określ zakres czasu w sekundach. Ilość czasu, aby umożliwić błędy przekroczenia limitu czasu określonego składnika Image Builder do zwrócenia do klienta. Jeśli bufor jest zbyt mały; Następnie klient upłynie limit czasu, zanim serwer i pobiera błąd ogólny limit czasu. |
|InfrastructureTaskHealthCheckRetryTimeout | Czas w sekundach, domyślna to 60 |Dynamiczny|Określ zakres czasu w sekundach. Ilość czasu poświęcanego ponawianie próby nie przeszły pomyślnie sprawdzania kondycji podczas przetwarzania końcowego zadanie infrastruktury. Sprawdzenie kondycji przekazany obserwowania spowoduje zresetowanie tego czasomierza. |
|InfrastructureTaskHealthCheckStableDuration | Czas w sekundach, domyślna to 0|Dynamiczny| Określ zakres czasu w sekundach. Ilość czasu, aby przyjrzeć się kontroli kolejnych kondycji przekazany przed rozpoczęciem przetwarzania po infrastruktury zadań zakończy się pomyślnie. Przywróci ten czasomierz obserwowania sprawdzenie kondycji nie powiodło się. |
|InfrastructureTaskHealthCheckWaitDuration |Czas w sekundach, domyślna to 0|Dynamiczny| Określ zakres czasu w sekundach. Ilość czasu oczekiwania przed uruchomieniem kontroli kondycji po przetwarzania końcowego zadanie infrastruktury. |
|InfrastructureTaskProcessingInterval | Czas w sekundach, domyślna to 10 |Dynamiczny|Określ zakres czasu w sekundach. Interwał przetwarzania używane przez zadanie infrastruktury przetwarzania komputera stanu. |
|MaxCommunicationTimeout |Czas w sekundach, domyślna to 600 |Dynamiczny|Określ zakres czasu w sekundach. Maksymalny limit czasu dla wewnętrznej komunikacji między ClusterManager i innym systemom usług (tj. Usługa nazewnictwa; Menedżer trybu failover, a itp.). Tego limitu czasu powinna być krótsza od parametru MaxOperationTimeout globalnych, (ponieważ może istnieć wiele komunikacji między składnikami systemu dla każdej operacji klienta). |
|MaxDataMigrationTimeout |Czas w sekundach, domyślna to 600 |Dynamiczny|Określ zakres czasu w sekundach. Maksymalny limit czasu dla operacji odzyskiwania migracji danych po przeprowadzeniu uaktualnienia sieci szkieletowej. |
|MaxOperationRetryDelay |Czas w sekundach, domyślna to 5|Dynamiczny| Określ zakres czasu w sekundach. Maksymalne opóźnienie ponownych wewnętrznego, gdy wystąpią błędy. |
|MaxOperationTimeout |Czas w sekundach, domyślnie jest MaxValue |Dynamiczny| Określ zakres czasu w sekundach. Maksymalna globalnego limitu czasu dla operacji na ClusterManager wewnętrznie przetwarzania. |
|MaxTimeoutRetryBuffer | Czas w sekundach, domyślna to 600 |Dynamiczny|Określ zakres czasu w sekundach. Operacja maksymalny limit czasu podczas ponownego wewnętrznie z powodu przekroczenia limitu czasu to <Original Time out>  +  <MaxTimeoutRetryBuffer>. Dodatkowe limitu czasu jest dodawany w przyrostach MinOperationTimeout. |
|MinOperationTimeout | Czas w sekundach, domyślna to 60 |Dynamiczny|Określ zakres czasu w sekundach. Minimalna globalnego limitu czasu dla operacji na ClusterManager wewnętrznie przetwarzania. |
|MinReplicaSetSize |Int, domyślna to 3 |Niedozwolone|MinReplicaSetSize dla ClusterManager. |
|PlacementConstraints | ciąg, domyślna to "" |Niedozwolone|PlacementConstraints dla ClusterManager. |
|QuorumLossWaitDuration |Czas w sekundach, domyślnie jest MaxValue |Niedozwolone| Określ zakres czasu w sekundach. QuorumLossWaitDuration dla ClusterManager. |
|ReplicaRestartWaitDuration |Czas w sekundach, wartość domyślna to (60.0 * 30)|Niedozwolone|Określ zakres czasu w sekundach. ReplicaRestartWaitDuration dla ClusterManager. |
|ReplicaSetCheckTimeoutRollbackOverride |Czas w sekundach, domyślnie jest 1200 |Dynamiczny| Określ zakres czasu w sekundach. Jeśli wartości ReplicaSetCheckTimeout jest równa wartości maksymalnej DWORD; następnie zostanie on przesłonięty przez wartość tej konfiguracji na potrzeby wycofywania. Wartość używaną do przewijaniem do nigdy nie zostanie zastąpiona. |
|SkipRollbackUpdateDefaultService | Wartość logiczna, wartość domyślna to false |Dynamiczny|WP pominie cofanie usług domyślnych zaktualizowany podczas wycofywania uaktualniania aplikacji. |
|StandByReplicaKeepDuration | Czas w sekundach, domyślnie jest (3600.0 * 2)|Niedozwolone|Określ zakres czasu w sekundach. StandByReplicaKeepDuration dla ClusterManager. |
|Wartość TargetReplicaSetSize |Int, domyślna to 7 |Niedozwolone|TargetReplicaSetSize dla ClusterManager. |
|UpgradeHealthCheckInterval |Czas w sekundach, domyślna to 60 |Dynamiczny|Częstotliwość stan kondycji sprawdza podczas uaktualniania monitorowanej aplikacji |
|UpgradeStatusPollInterval |Czas w sekundach, domyślna to 60 |Dynamiczny|Częstotliwość sondowania stanu uaktualniania aplikacji. Ta wartość określa częstotliwość aktualizacji dla każde wywołanie GetApplicationUpgradeProgress |

## <a name="common"></a>Wspólne
| **Parametr** | **Dozwolone wartości** | **Zasady uaktualniania** | **Wskazówki lub krótki opis** |
| --- | --- | --- | --- |
|PerfMonitorInterval |Czas w sekundach, wartość domyślna to 1 |Dynamiczny|Określ zakres czasu w sekundach. Interwał monitorowania wydajności. Ustawienie wartości 0 ani ujemna powoduje wyłączenie monitorowania. |

## <a name="defragmentationemptynodedistributionpolicy"></a>DefragmentationEmptyNodeDistributionPolicy
| **Parametr** | **Dozwolone wartości** |**Zasady uaktualniania**| **Wskazówki lub krótki opis** |
| --- | --- | --- | --- |
|PropertyGroup|KeyIntegerValueMap, domyślna wartość to Brak|Dynamiczny|Określa, że defragmentacja zasad następuje podczas opróżniania węzłów. Dla danej metryki 0 wskazuje, że rz należy dążyć do defragmentacji węzłów równomiernie między UDs i FDs; wartość 1 oznacza tylko węzły muszą defragmentacji |

## <a name="defragmentationmetrics"></a>DefragmentationMetrics
| **Parametr** | **Dozwolone wartości** |**Zasady uaktualniania**| **Wskazówki lub krótki opis** |
| --- | --- | --- | --- |
|PropertyGroup|KeyBoolValueMap, domyślna wartość to Brak|Dynamiczny|Określa zestaw metryk, które mają być używane do defragmentacji, a nie równoważenia obciążenia. |

## <a name="defragmentationmetricspercentornumberofemptynodestriggeringthreshold"></a>DefragmentationMetricsPercentOrNumberOfEmptyNodesTriggeringThreshold
| **Parametr** | **Dozwolone wartości** |**Zasady uaktualniania**| **Wskazówki lub krótki opis** |
| --- | --- | --- | --- |
|PropertyGroup|KeyDoubleValueMap, domyślna wartość to Brak|Dynamiczny|Określa liczbę wolne węzły, które są potrzebne do uwzględnienia klastra defragmentacji, określając albo procent w zakresie [0.0-1.0) lub liczbę węzłów pusty jako liczbę > = 1.0 |

## <a name="diagnostics"></a>Diagnostyka
| **Parametr** | **Dozwolone wartości** | **Zasady uaktualniania** | **Wskazówki lub krótki opis** |
| --- | --- | --- | --- |
|AppDiagnosticStoreAccessRequiresImpersonation |Wartość logiczna, domyślna to true | Dynamiczny |Określa, czy personifikacja jest wymagana podczas uzyskiwania dostępu do diagnostyki są przechowywane w imieniu aplikacji. |
|AppEtwTraceDeletionAgeInDays |Int, domyślna to 3 | Dynamiczny |Liczba dni, po których firma Microsoft usuwanie starych plików ETL zawierający śladów ETW aplikacji. |
|ApplicationLogsFormatVersion |Int, domyślna to 0 | Dynamiczny |Wersja aplikacji rejestruje formatu. Obsługiwane wartości to 0 i 1. Wersja 1 zawiera więcej pól w rekordzie zdarzeń ETW niż wersja 0. |
|ClusterId |Ciąg | Dynamiczny |Unikatowy identyfikator klastra. To jest generowany po utworzeniu klastra. |
|ConsumerInstances |Ciąg | Dynamiczny |Lista wystąpień konsumenta DCA. |
|DiskFullSafetySpaceInMB |Int, domyślny to 1024 | Dynamiczny |Pozostałe miejsce na dysku w MB na ochronę przed użyciem przez DCA. |
|EnableCircularTraceSession |Wartość logiczna, wartość domyślna to false | Statyczny |Flaga wskazuje, czy ma być używane sesje cykliczne śledzenia. |
|EnableTelemetry |Wartość logiczna, domyślna to true | Dynamiczny |Będzie Włączanie lub wyłączanie telemetrii. |
|MaxDiskQuotaInMB |Int, domyślna to 65536 | Dynamiczny |Przydział dysku w plikach dziennika MB dla systemu Windows Fabric. |
|ProducerInstances |Ciąg | Dynamiczny |Lista wystąpień producent DCA. |

## <a name="dnsservice"></a>DnsService
| **Parametr** | **Dozwolone wartości** |**Zasady uaktualniania**| **Wskazówki lub krótki opis** |
| --- | --- | --- | --- |
|IsEnabled|wartość logiczna, domyślna to FALSE|Statyczny| |
|Wartość InstanceCount|int, domyślna wynosi -1|Statyczny|  |

## <a name="fabricclient"></a>Klienta fabricclient z rolą
| **Parametr** | **Dozwolone wartości** | **Zasady uaktualniania** | **Wskazówki lub krótki opis** |
| --- | --- | --- | --- |
|ConnectionInitializationTimeout |Czas w sekundach, domyślna to 2 |Dynamiczny|Określ zakres czasu w sekundach. Interwał limitu czasu połączenia dla każdego klienta czasu próbuje nawiązać połączenie z bramą.|
|HealthOperationTimeout |Czas w sekundach, domyślna to 120 |Dynamiczny|Określ zakres czasu w sekundach. Limit czasu dla raportu komunikat wysłany do Menedżera kondycji. |
|HealthReportRetrySendInterval |Czas w sekundach, wartość domyślna to 30 |Dynamiczny|Określ zakres czasu w sekundach. Interwał, w którym raportowania składnika ponownie wysyła raporty kondycji skumulowany do Menedżera kondycji. |
|HealthReportSendInterval |Czas w sekundach, wartość domyślna to 30 |Dynamiczny|Określ zakres czasu w sekundach. Interwał, w którym raportowania składnika wysyła raporty kondycji skumulowany do Menedżera kondycji. |
|KeepAliveIntervalInSeconds |Int, domyślna to 20 |Statyczny|Interwał, z jaką transportu klienta fabricclient z rolą wysyła komunikaty utrzymywania aktywności do bramy. 0; keepAlive jest wyłączona. Musi być wartością dodatnią. |
|MaxFileSenderThreads |Uint — wartość domyślna to 10 |Statyczny|Maksymalna liczba plików, które są transferowane równolegle. |
|NodeAddresses |ciąg, domyślna to "" |Statyczny|Kolekcja adresów (parametry połączenia) w różnych węzłach, które mogą służyć do komunikacji z Naming Service. Początkowo klient nawiązuje połączenie, wybierając jedną z adresów losowo. Jeśli podano więcej niż jeden ciąg połączenia i połączenia kończy się niepowodzeniem z powodu komunikacji lub błąd upływu limitu czasu; przełączniki klienta do korzystania z adresu następnego sekwencyjnie. Zobacz adres usługi nazewnictwa, spróbuj ponownie, aby uzyskać szczegółowe informacje na semantykę ponownych prób. |
|PartitionLocationCacheLimit |Int, domyślnie jest 100000 |Statyczny|Liczba partycji w pamięci podręcznej usługi rozpoznawania (wartość 0 oznacza brak limitu). |
|RetryBackoffInterval |Czas w sekundach, domyślna to 3 |Dynamiczny|Określ zakres czasu w sekundach. Interwał wycofania przed wykonaniem operacji. |
|ServiceChangePollInterval |Czas w sekundach, domyślna to 120 |Dynamiczny|Określ zakres czasu w sekundach. Interwał między kolejnymi sondowaniami usługi zmienia się z klienta bramy dla wywołania zwrotne powiadomienia zmian zarejestrowanej usługi. |

## <a name="fabrichost"></a>Elementu FabricHost
| **Parametr** | **Dozwolone wartości** | **Zasady uaktualniania** | **Wskazówki lub krótki opis** |
| --- | --- | --- | --- |
|ActivationMaxFailureCount |Int, domyślna to 10 |Dynamiczny|Jest to maksymalna liczba, dla której system będzie ponawiał próby aktywacji nie powiodło się zrezygnuje. |
|ActivationMaxRetryInterval |Czas w sekundach, domyślna to 300 |Dynamiczny|Określ zakres czasu w sekundach. Maksymalna liczba interwału ponawiania prób aktywacji. Co ciągłego niepowodzeń interwał ponawiania jest obliczany jako wartość minimalną (ActivationMaxRetryInterval; Ciągłe liczby awarii * ActivationRetryBackoffInterval). |
|ActivationRetryBackoffInterval |Czas w sekundach, domyślna to 5 |Dynamiczny|Określ zakres czasu w sekundach. Interwał wycofywania co w przypadku niepowodzenia aktywacji; na co w przypadku niepowodzenia aktywacji ciągłego system będzie ponawiał próby aktywacji dla maksymalnie MaxActivationFailureCount. Interwał ponawiania przy każdej próbie jest produktem w przypadku niepowodzenia aktywacji ciągłe i interwał wycofania aktywacji. |
|EnableRestartManagement |Wartość logiczna, wartość domyślna to false |Dynamiczny|To jest umożliwienie ponownego uruchomienia serwera. |
|EnableServiceFabricAutomaticUpdates |Wartość logiczna, wartość domyślna to false |Dynamiczny|To, aby włączyć automatyczną aktualizację sieci szkieletowej za pośrednictwem usługi Windows Update. |
|EnableServiceFabricBaseUpgrade |Wartość logiczna, wartość domyślna to false |Dynamiczny|To jest umożliwienie aktualizacji dla serwera. |
|StartTimeout |Czas w sekundach, domyślna to 300 |Dynamiczny|Określ zakres czasu w sekundach. Limit czasu uruchamiania fabricactivationmanager. |
|StopTimeout |Czas w sekundach, domyślna to 300 |Dynamiczny|Określ zakres czasu w sekundach. Limit czasu w celu aktywacji usługi hostowanej. Dezaktywacja i uaktualniania. |

## <a name="fabricnode"></a>FabricNode
| **Parametr** | **Dozwolone wartości** | **Zasady uaktualniania** | **Wskazówki lub krótki opis** |
| --- | --- | --- | --- |
|ClientAuthX509FindType |ciąg, domyślna ma "postać FindByThumbprint" |Dynamiczny|Wskazuje sposób wyszukiwania certyfikatu w magazynie określonym przez wartość obsługiwane ClientAuthX509StoreName: postać FindByThumbprint; FindBySubjectName. |
|ClientAuthX509FindValue |ciąg, domyślna to "" | Dynamiczny|Wartość filtru wyszukiwania używana do lokalizowania certyfikatu do roli administratora domyślnego klienta fabricclient z rolą. |
|ClientAuthX509FindValueSecondary |ciąg, domyślna to "" |Dynamiczny|Wartość filtru wyszukiwania używana do lokalizowania certyfikatu do roli administratora domyślnego klienta fabricclient z rolą. |
|ClientAuthX509StoreName |jest domyślny ciąg "Moje" |Dynamiczny|Nazwa magazynu certyfikatu X.509, który zawiera certyfikat do roli administratora domyślnego klienta fabricclient z rolą. |
|ClusterX509FindType |ciąg, domyślna ma "postać FindByThumbprint" |Dynamiczny|Wskazuje sposób wyszukiwania certyfikatu klaster w magazynie określonym przez ClusterX509StoreName obsługiwane wartości: "Postać FindByThumbprint"; "FindBySubjectName" z "FindBySubjectName"; gdy istnieje wiele dopasowań; jest używany z najdalej wygaśnięcia. |
|ClusterX509FindValue |ciąg, domyślna to "" |Dynamiczny|Wartość filtru wyszukiwania używana do lokalizowania certyfikatu klastra. |
|ClusterX509FindValueSecondary |ciąg, domyślna to "" |Dynamiczny|Wartość filtru wyszukiwania używana do lokalizowania certyfikatu klastra. |
|ClusterX509StoreName |jest domyślny ciąg "Moje" |Dynamiczny|Nazwa magazynu certyfikatów X.509, który zawiera certyfikat klastra do zabezpieczania komunikacji wewnątrz klastra. |
|EndApplicationPortRange |Int, domyślna to 0 |Statyczny|Koniec (nie z wartościami granicznymi) porty aplikacji zarządzanych przez hosting podsystemu. Wymagane, jeśli EndpointFilteringEnabled ma wartość true w hostingu. |
|ServerAuthX509FindType |ciąg, domyślna ma "postać FindByThumbprint" |Dynamiczny|Wskazuje sposób wyszukiwania certyfikatu serwera w magazynie określonym przez wartość obsługiwane ServerAuthX509StoreName: postać FindByThumbprint; FindBySubjectName. |
|ServerAuthX509FindValue |ciąg, domyślna to "" |Dynamiczny|Wartość filtru wyszukiwania używana do lokalizowania certyfikatu serwera. |
|ServerAuthX509FindValueSecondary |ciąg, domyślna to "" |Dynamiczny|Wartość filtru wyszukiwania używana do lokalizowania certyfikatu serwera. |
|ServerAuthX509StoreName |jest domyślny ciąg "Moje" |Dynamiczny|Nazwa magazynu certyfikatu X.509, który zawiera certyfikat serwera dla usługi entrée. |
|StartApplicationPortRange |Int, domyślna to 0 |Statyczny|Początek portów aplikacji zarządzanych przez hosting podsystemu. Wymagane, jeśli EndpointFilteringEnabled ma wartość true w hostingu. |
|StateTraceInterval |Czas w sekundach, domyślna to 300 |Statyczny|Określ zakres czasu w sekundach. Interwał śledzenia stanu węzła w każdym węźle oraz węzły FM/FMM. |
|UserRoleClientX509FindType |ciąg, domyślna ma "postać FindByThumbprint" |Dynamiczny|Wskazuje sposób wyszukiwania certyfikatu w magazynie określonym przez wartość obsługiwane UserRoleClientX509StoreName: postać FindByThumbprint; FindBySubjectName. |
|UserRoleClientX509FindValue |ciąg, domyślna to "" |Dynamiczny|Wartość filtru wyszukiwania używana do lokalizowania certyfikatu dla roli użytkownika domyślnego klienta fabricclient z rolą. |
|UserRoleClientX509FindValueSecondary |ciąg, domyślna to "" |Dynamiczny|Wartość filtru wyszukiwania używana do lokalizowania certyfikatu dla roli użytkownika domyślnego klienta fabricclient z rolą. |
|UserRoleClientX509StoreName |jest domyślny ciąg "Moje" |Dynamiczny|Nazwa magazynu certyfikatu X.509, który zawiera certyfikat do domyślnej roli użytkownika klienta fabricclient z rolą. |

## <a name="failovermanager"></a>FailoverManager
| **Parametr** | **Dozwolone wartości** | **Zasady uaktualniania** | **Wskazówki lub krótki opis** |
| --- | --- | --- | --- |
|BuildReplicaTimeLimit|Zakres czasu, domyślnie jest Common::TimeSpan::FromSeconds(3600)|Dynamiczny|Określ zakres czasu w sekundach. Limit czasu na tworzenie replik stanowe; Po upływie którego ostrzeżenie raportu kondycji zostanie zainicjowana |
|ClusterPauseThreshold|int, domyślna to 1|Dynamiczny|Jeśli liczba węzłów w systemie zejść poniżej tej wartości, a następnie umieszczania; Równoważenie; obciążenia i trybu failover zostanie zatrzymana. |
|CreateInstanceTimeLimit|Zakres czasu, domyślnie jest Common::TimeSpan::FromSeconds(300)|Dynamiczny|Określ zakres czasu w sekundach. Limit czasu na tworzenie wystąpienia bezstanowych; Po upływie którego ostrzeżenie raportu kondycji zostanie zainicjowana |
|ExpectedClusterSize|int, domyślna to 1|Dynamiczny|Po początkowym uruchomieniu klastra FM będzie czekać w tym wiele węzłów do zgłaszania samodzielnie się przed rozpoczęciem wprowadzania do innych usług. w tym usług systemowych, takich jak nazewnictwa. Zwiększenie tej wartości zwiększa czas uruchomienia; klastra ale uniemożliwia wczesne węzłów z przeciążenia, a także dodatkowe przenosi, które będą potrzebne, ponieważ więcej węzłów do trybu online. Ta wartość zazwyczaj powinien mieć ustawioną niektórych ułamku rozmiaru klastra. |
|ExpectedNodeDeactivationDuration|Zakres czasu, domyślnie jest Common::TimeSpan::FromSeconds(60.0 * 30)|Dynamiczny|Określ zakres czasu w sekundach. Jest to oczekiwany czas trwania dla węzła w celu ukończenia dezaktywacji w. |
|ExpectedNodeFabricUpgradeDuration|Zakres czasu, domyślnie jest Common::TimeSpan::FromSeconds(60.0 * 30)|Dynamiczny|Określ zakres czasu w sekundach. Oczekiwany czas trwania dla węzła do uaktualnienia jest podczas uaktualniania systemu Windows w sieci szkieletowej. |
|ExpectedReplicaUpgradeDuration|Zakres czasu, domyślnie jest Common::TimeSpan::FromSeconds(60.0 * 30)|Dynamiczny|Określ zakres czasu w sekundach. Jest to oczekiwany czas trwania dla wszystkich replik do uaktualnienia w węźle podczas uaktualniania aplikacji. |
|IsSingletonReplicaMoveAllowedDuringUpgrade|wartość logiczna, domyślna ma wartość TRUE|Dynamiczny|Jeśli ma wartość true; repliki z rozmiar docelowego zestawu replik 1 będzie możliwe, aby przenieść podczas uaktualniania. |
|MinReplicaSetSize|Int, domyślna to 3|Niedozwolone|Jest to minimalny rozmiar zestawu replik FM. Jeśli liczba aktywnych replik FM spada poniżej tej wartości; FM spowoduje odrzucenie zmian w klastrze, dopóki co najmniej minimalną liczbę replik jest odzyskiwana |
|PlacementConstraints|ciąg, domyślny jest L""|Niedozwolone|Żadnych ograniczeń umieszczania dla replik menedżera trybu failover |
|PlacementTimeLimit|Zakres czasu, domyślnie jest Common::TimeSpan::FromSeconds(600)|Dynamiczny|Określ zakres czasu w sekundach. Limit czasu osiągnięcia docelowa liczba replik; Po upływie którego ostrzeżenie raportu kondycji zostanie zainicjowana |
|QuorumLossWaitDuration |Czas w sekundach, domyślnie jest MaxValue |Dynamiczny|Określ zakres czasu w sekundach. Jest to maksymalny czas trwania, dla którego zezwolimy partycji stanu utraty kworum. Jeśli partycja jest nadal w wyniku utraty kworum po tym okresie; partycja jest odzyskała sprawność działania po utraty kworum przy uwzględnieniu dół repliki jako utracone. Należy pamiętać, że to potencjalnie ponieść utraty danych. |
|ReconfigurationTimeLimit|Zakres czasu, domyślnie jest Common::TimeSpan::FromSeconds(300)|Dynamiczny|Określ zakres czasu w sekundach. Limit czasu zmiany konfiguracji; Po upływie którego ostrzeżenie raportu kondycji zostanie zainicjowana |
|ReplicaRestartWaitDuration|Zakres czasu, domyślnie jest Common::TimeSpan::FromSeconds(60.0 * 30)|Niedozwolone|Określ zakres czasu w sekundach. Jest to ReplicaRestartWaitDuration dla FMService |
|StandByReplicaKeepDuration|Zakres czasu, domyślnie jest Common::TimeSpan::FromSeconds(3600.0 * 24 * 7)|Niedozwolone|Określ zakres czasu w sekundach. Jest to StandByReplicaKeepDuration dla FMService |
|Wartość TargetReplicaSetSize|Int, domyślna to 7|Niedozwolone|Jest to liczba docelowych FM replik, które mają być przechowywane w sieci szkieletowej systemu Windows. Większa liczba powoduje wyższy niezawodność danych FM; z zależnościami wydajność. |
|UserMaxStandByReplicaCount |Int, wartość domyślna to 1 |Dynamiczny|Domyślna maksymalna liczba replik stan wstrzymania, które system przechowuje usługi użytkownika. |
|UserReplicaRestartWaitDuration |Czas w sekundach, wartość domyślna to 60.0 * 30 |Dynamiczny|Określ zakres czasu w sekundach. Gdy replika utrwalonego spadnie; Windows Fabric czeka na ten czas trwania na repliki przywracane przed utworzeniem nowej repliki zastąpienia (które wymagają kopii stanu). |
|UserStandByReplicaKeepDuration |Czas w sekundach, domyślna to 3600.0 * 24 * 7 |Dynamiczny|Określ zakres czasu w sekundach. Gdy utrwalonego repliki wrócić z opuszczona; go może już zostały zastąpione. Ten czasomierz Określa, jak długo FM zachowa rezerwy repliki przed usunięciem. |

## <a name="faultanalysisservice"></a>FaultAnalysisService
| **Parametr** | **Dozwolone wartości** | **Zasady uaktualniania** | **Wskazówki lub krótki opis** |
| --- | --- | --- | --- |
|CompletedActionKeepDurationInSeconds | Int, domyślnie jest 604800 |Statyczny| Jest to około czas przechowywania akcje, które są w stanie terminala. Zależy to od również StoredActionCleanupIntervalInSeconds; ponieważ pracy do czyszczenia jest wykonywane tylko w tym interwał. 604800 wynosi 7 dni. |
|DataLossCheckPollIntervalInSeconds|int, domyślna to 5|Statyczny|Jest to czas między testy, które system wykonuje podczas oczekiwania na utratę danych, mieć miejsce. Ile razy numer utraty danych, zostanie sprawdzony na iterację wewnętrznego jest DataLossCheckWaitDurationInSeconds/to. |
|DataLossCheckWaitDurationInSeconds|int, domyślna to 25|Statyczny|Łączny czas trwania; w ciągu sekund; czy system będzie oczekiwał na utratę danych, mieć miejsce. To jest używana wewnętrznie po wywołaniu interfejsu api StartPartitionDataLossAsync(). |
|MinReplicaSetSize |Int, domyślna to 0 |Statyczny|MinReplicaSetSize dla FaultAnalysisService. |
|PlacementConstraints | ciąg, domyślna to ""|Statyczny| PlacementConstraints dla FaultAnalysisService. |
|QuorumLossWaitDuration | Czas w sekundach, domyślnie jest MaxValue |Statyczny|Określ zakres czasu w sekundach. QuorumLossWaitDuration dla FaultAnalysisService. |
|ReplicaDropWaitDurationInSeconds|int, domyślny to 600|Statyczny|Ten parametr jest używany, gdy jest wywoływana utraty danych interfejsu api. Określa, jak długo system będzie oczekiwał na replikę w celu pobrania porzucona po Usuń replikę wewnętrznie jest wywoływana na nim. |
|ReplicaRestartWaitDuration |Czas w sekundach, domyślna to 60 minut|Statyczny|Określ zakres czasu w sekundach. ReplicaRestartWaitDuration dla FaultAnalysisService. |
|StandByReplicaKeepDuration| Czas w sekundach, jest domyślną (60*24*7) minut |Statyczny|Określ zakres czasu w sekundach. StandByReplicaKeepDuration dla FaultAnalysisService. |
|StoredActionCleanupIntervalInSeconds | Int, domyślna to 3600 |Statyczny|Jest to, jak często oczyścić magazynu. Tylko akcje w stanie terminali; i że ukończono co najmniej CompletedActionKeepDurationInSeconds temu będzie można usunąć. |
|StoredChaosEventCleanupIntervalInSeconds | Int, domyślna to 3600 |Statyczny|Jest to, jak często magazynie będzie przeprowadzać inspekcję oczyszczania; Jeśli liczba zdarzeń jest więcej niż 30000; oczyszczanie będzie zaczną działać. |
|Wartość TargetReplicaSetSize |Int, domyślna to 0 |Statyczny|NOT_PLATFORM_UNIX_START TargetReplicaSetSize dla FaultAnalysisService. |

## <a name="federation"></a>Federacja
| **Parametr** | **Dozwolone wartości** | **Zasady uaktualniania** | **Wskazówki lub krótki opis** |
| --- | --- | --- | --- |
|LeaseDuration |Czas w sekundach, wartość domyślna to 30 |Dynamiczny|Czas trwania dzierżawy ważny między węzłem a jego sąsiadów. |
|LeaseDurationAcrossFaultDomain |Czas w sekundach, wartość domyślna to 30 |Dynamiczny|Czas trwania dzierżawy ważny między węzłem a jego sąsiadów domen błędów. |

## <a name="filestoreservice"></a>FileStoreService
| **Parametr** | **Dozwolone wartości** | **Zasady uaktualniania** | **Wskazówki lub krótki opis** |
| --- | --- | --- | --- |
|AnonymousAccessEnabled | Wartość logiczna, domyślna to true |Statyczny|Włączanie/wyłączanie dostępu anonimowego do udziałów FileStoreService. |
|CommonName1Ntlmx509CommonName|ciąg, domyślny jest L""|Statyczny| Nazwa pospolita X509 certyfikat używany do generowania HMAC na CommonName1NtlmPasswordSecret, gdy jest używane uwierzytelnianie NTLM |
|CommonName1Ntlmx509StoreLocation|ciąg, domyślny jest L "LocalMachine"|Statyczny|Lokalizacja magazynu X509 certyfikat używany do generowania HMAC na CommonName1NtlmPasswordSecret, gdy jest używane uwierzytelnianie NTLM |
|CommonName1Ntlmx509StoreName|ciąg, domyślny jest L "MY"| Statyczny|Nazwa magazynu X509 certyfikat używany do generowania HMAC na CommonName1NtlmPasswordSecret, gdy jest używane uwierzytelnianie NTLM |
|CommonName2Ntlmx509CommonName|ciąg, domyślny jest L""|Statyczny|Nazwa pospolita X509 certyfikat używany do generowania HMAC na CommonName2NtlmPasswordSecret, gdy jest używane uwierzytelnianie NTLM |
|CommonName2Ntlmx509StoreLocation|ciąg, domyślny jest L "LocalMachine"| Statyczny|Lokalizacja magazynu X509 certyfikat używany do generowania HMAC na CommonName2NtlmPasswordSecret, gdy jest używane uwierzytelnianie NTLM |
|CommonName2Ntlmx509StoreName|ciąg, domyślny jest L "MY"|Statyczny| Nazwa magazynu X509 certyfikat używany do generowania HMAC na CommonName2NtlmPasswordSecret, gdy jest używane uwierzytelnianie NTLM |
|CommonNameNtlmPasswordSecret|SecureString, domyślnie jest Common::SecureString(L"")| Statyczny|Hasła, użyty jako inicjatora do wygenerowanego tego samego hasła, gdy jest używane uwierzytelnianie NTLM |
|GenerateV1CommonNameAccount| wartość logiczna, domyślna ma wartość TRUE|Statyczny|Określa, czy do generowania konta z algorytmu generowania V1 nazwy użytkownika. Począwszy od platformy Service Fabric w wersji 6.1; zawsze zostanie utworzone konto w wersji 2 generacji. Konto V1 jest niezbędne do uaktualnienia z/do wersji, które nie obsługują generowanie V2 (przed 6.1).|
|MaxCopyOperationThreads | Uint — wartość domyślna to 0 |Dynamiczny| Maksymalna liczba równoległych plików to dodatkowej można skopiować z podstawowego. "0" == liczby rdzeni. |
|MaxFileOperationThreads | Uint — wartość domyślna to 100 |Statyczny| Maksymalna liczba równoległych wątków może wykonać FileOperations (skopiowania/przeniesienia) w podstawowym. "0" == liczby rdzeni. |
|MaxRequestProcessingThreads | Uint — wartość domyślna to 200 |Statyczny|Maksymalna liczba równoległych wątków do przetwarzania żądań w podstawowym. "0" == liczby rdzeni. |
|MaxSecondaryFileCopyFailureThreshold | Uint — wartość domyślna to 25|Dynamiczny|Maksymalna liczba ponownych prób kopiowania plików na serwerze pomocniczym zrezygnuje. |
|MaxStoreOperations | Uint — wartość domyślna to 4096 |Statyczny|Maksymalna liczba operacji transakcji magazynu równoległego dozwolone na głównym. "0" == liczby rdzeni. |
|NamingOperationTimeout |Czas w sekundach, domyślna to 60 |Dynamiczny|Określ zakres czasu w sekundach. Limit czasu dla operacji nazewnictwa. |
|PrimaryAccountNTLMPasswordSecret | SecureString, domyślnym jest pusty |Statyczny| Klucz tajny hasła, który będzie używany jako inicjatora do wygenerowanego tego samego hasła podczas korzystania z uwierzytelniania NTLM. |
|PrimaryAccountNTLMX509StoreLocation | ciąg, domyślną jest "LocalMachine"|Statyczny| Lokalizacja magazynu X509 certyfikat używany do generowania HMAC na PrimaryAccountNTLMPasswordSecret w przypadku korzystania z uwierzytelniania NTLM. |
|PrimaryAccountNTLMX509StoreName | ciąg, domyślną jest "MY"|Statyczny| Nazwa magazynu X509 certyfikat używany do generowania HMAC na PrimaryAccountNTLMPasswordSecret w przypadku korzystania z uwierzytelniania NTLM. |
|PrimaryAccountNTLMX509Thumbprint | ciąg, domyślna to ""|Statyczny|Odcisk palca X509 certyfikat używany do generowania HMAC na PrimaryAccountNTLMPasswordSecret w przypadku korzystania z uwierzytelniania NTLM. |
|PrimaryAccountType | ciąg, domyślna to "" |Statyczny|Udostępnia podstawową dla konta głównego listę ACL FileStoreService. |
|PrimaryAccountUserName | ciąg, domyślna to "" |Statyczny|Udostępnia FileStoreService kontem podstawowym Username podmiotu do listy kontroli dostępu. |
|PrimaryAccountUserPassword | SecureString, domyślnym jest pusty |Statyczny|Hasło do konta podstawowego podmiotu do listy ACL FileStoreService udziałów. |
|QueryOperationTimeout | Czas w sekundach, domyślna to 60 |Dynamiczny|Określ zakres czasu w sekundach. Limit czasu dla operacji zapytania. |
|SecondaryAccountNTLMPasswordSecret | SecureString, domyślnym jest pusty |Statyczny| Klucz tajny hasła, który będzie używany jako inicjatora do wygenerowanego tego samego hasła podczas korzystania z uwierzytelniania NTLM. |
|SecondaryAccountNTLMX509StoreLocation | ciąg, domyślną jest "LocalMachine" |Statyczny|Lokalizacja magazynu X509 certyfikat używany do generowania HMAC na SecondaryAccountNTLMPasswordSecret w przypadku korzystania z uwierzytelniania NTLM. |
|SecondaryAccountNTLMX509StoreName | ciąg, domyślną jest "MY" |Statyczny|Nazwa magazynu X509 certyfikat używany do generowania HMAC na SecondaryAccountNTLMPasswordSecret w przypadku korzystania z uwierzytelniania NTLM. |
|SecondaryAccountNTLMX509Thumbprint | ciąg, domyślna to ""| Statyczny|Odcisk palca X509 certyfikat używany do generowania HMAC na SecondaryAccountNTLMPasswordSecret w przypadku korzystania z uwierzytelniania NTLM. |
|SecondaryAccountType | ciąg, domyślna to ""|Statyczny| Pomocniczy dla konta głównego listę ACL FileStoreService udziałów. |
|SecondaryAccountUserName | ciąg, domyślna to ""| Statyczny|Drugie konto nazwy użytkownika podmiotu do listy ACL FileStoreService udziałów. |
|SecondaryAccountUserPassword | SecureString, domyślnym jest pusty |Statyczny|Hasło do konta dodatkowej podmiotu do listy ACL FileStoreService udziałów. |

## <a name="healthmanager"></a>HealthManager
| **Parametr** | **Dozwolone wartości** | **Zasady uaktualniania** | **Wskazówki lub krótki opis** |
| --- | --- | --- | --- |
| EnableApplicationTypeHealthEvaluation |Wartość logiczna, wartość domyślna to false |Statyczny|Klaster zasad oceny kondycji: Włączanie oceny kondycji typu aplikacji. |

## <a name="healthmanagerclusterhealthpolicy"></a>HealthManager/ClusterHealthPolicy
| **Parametr** | **Dozwolone wartości** | **Zasady uaktualniania** | **Wskazówki lub krótki opis** |
| --- | --- | --- | --- |
|Elementów ConsiderWarningAsError |Wartość logiczna, wartość domyślna to false |Statyczny|Klaster zasad oceny kondycji: ostrzeżenia są traktowane jako błędy. |
|MaxPercentUnhealthyApplications | Int, domyślna to 0 |Statyczny|Klaster zasad oceny kondycji: maksymalny procent złej kondycji aplikacji mogą aby klaster mógł być w dobrej kondycji. |
|MaxPercentUnhealthyNodes | Int, domyślna to 0 |Statyczny|Klaster zasad oceny kondycji: dozwolone maksymalny procent węzłów w złej kondycji, aby klaster mógł być w dobrej kondycji. |

## <a name="healthmanagerclusterupgradehealthpolicy"></a>HealthManager/ClusterUpgradeHealthPolicy
| **Parametr** | **Dozwolone wartości** | **Zasady uaktualniania** | **Wskazówki lub krótki opis** |
| --- | --- | --- | --- |
|Elementów MaxPercentDeltaUnhealthyNodes|Int, domyślna to 10|Statyczny|Zasady oceny kondycji uaktualnienia klastra: dozwolony maksymalny procent złej kondycji węzłów różnicowych aby klaster mógł być w dobrej kondycji |
|MaxPercentUpgradeDomainDeltaUnhealthyNodes|int, domyślną jest 15|Statyczny|Zasady oceny kondycji uaktualnienia klastra: dozwolone maksymalny procent różnicowych węzłów w złej kondycji w domenie uaktualnienia, aby klaster mógł być w dobrej kondycji |

## <a name="hosting"></a>Hosting
| **Parametr** | **Dozwolone wartości** | **Zasady uaktualniania** | **Wskazówki lub krótki opis** |
| --- | --- | --- | --- |
|ActivationMaxFailureCount |Liczby całkowitej, domyślna to 10 |Dynamiczny|Liczba ponownych prób system nie powiodła się Aktywacja zrezygnuje |
|ActivationMaxRetryInterval |Czas w sekundach, domyślna to 300 |Dynamiczny|W przypadku każdego błędu ciągłe system ponawia próbę aktywacji dla maksymalnie ActivationMaxFailureCount. ActivationMaxRetryInterval określa czas oczekiwania, przed ponowną próbą wykonania po co w przypadku niepowodzenia aktywacji |
|ActivationRetryBackoffInterval |Czas w sekundach, domyślna to 5 |Dynamiczny|Interwał wycofywania na co w przypadku niepowodzenia aktywacji; W przypadku każdego błędu ciągłe system ponawia próbę aktywacji dla maksymalnie MaxActivationFailureCount. Interwał ponawiania przy każdej próbie jest produktem w przypadku niepowodzenia aktywacji ciągłe i interwał wycofania aktywacji. |
|ActivationTimeout| Zakres czasu, domyślnie jest Common::TimeSpan::FromSeconds(180)|Dynamiczny| Określ zakres czasu w sekundach. Limit czasu dla aktywacji aplikacji; Dezaktywacja i uaktualniania. |
|ApplicationHostCloseTimeout| Zakres czasu, domyślnie jest Common::TimeSpan::FromSeconds(120)|Dynamiczny| Określ zakres czasu w sekundach. Po wykryciu zakończenia sieci szkieletowej w własnym aktywowane procesy; Środowisko uruchomieniowe sieci szkieletowej zamyka wszystkie repliki procesu (hosta aplikacji) hosta użytkownika. Jest to limit czasu dla operacji zamknięcia. |
|ApplicationUpgradeTimeout| Zakres czasu, domyślnie jest Common::TimeSpan::FromSeconds(360)|Dynamiczny| Określ zakres czasu w sekundach. Limit czasu uaktualniania aplikacji. Jeśli limit czasu jest mniejsza niż wdrażającego "ActivationTimeout" zakończy się niepowodzeniem. |
|ContainerServiceArguments|wstring, domyślny jest L "-H 2375 -H npipe: / /"|Statyczny|Usługa sieci szkieletowej (CPP) zarządza demon docker (z wyjątkiem komputerów klienckich z systemem windows, takich jak Windows 10). Ta konfiguracja umożliwia użytkownikowi określenie niestandardowego argumenty, które powinny zostać przekazane do demon docker podczas jego uruchamiania. Jeśli określono niestandardowe argumenty, sieci szkieletowej usług nie są przekazywane innych argumentu z aparatem platformy Docker z wyjątkiem "--pidfile" argument. Dlatego użytkownicy nie można określić "--pidfile" argument jako część ich argumentów klienta. Niestandardowe argumenty powinny upewnij się również, tego docker wykrywa demon domyślnej nazwy potoku w systemie Windows (lub Unix gniazda domeny w systemie Linux) dla sieci szkieletowej usług można było nawiązać z nim.|
|CreateFabricRuntimeTimeout|Zakres czasu, domyślnie jest Common::TimeSpan::FromSeconds(120)|Dynamiczny| Określ zakres czasu w sekundach. Wartość limitu czasu dla synchronizacji FabricCreateRuntime wywołania |
|DeploymentMaxFailureCount|Int, domyślna to 20| Dynamiczny|Wdrażanie aplikacji zostanie ponowiona w sytuacjach DeploymentMaxFailureCount przed niepowodzeniem wdrożenia tej aplikacji w węźle.| 
|DeploymentMaxRetryInterval| Zakres czasu, domyślnie jest Common::TimeSpan::FromSeconds(3600)|Dynamiczny| Określ zakres czasu w sekundach. Maksymalny interwał ponawiania dla wdrożenia. Co ciągłego niepowodzeń interwał ponawiania jest obliczany jako wartość minimalną (DeploymentMaxRetryInterval; Ciągłe liczby awarii * DeploymentRetryBackoffInterval) |
|DeploymentRetryBackoffInterval| Zakres czasu, domyślnie jest Common::TimeSpan::FromSeconds(10)|Dynamiczny|Określ zakres czasu w sekundach. Wycofania interwał niepowodzenie wdrożenia. W przypadku niepowodzenia co ciągłe wdrażanie systemu ponowi próbę wdrożenia do MaxDeploymentFailureCount. Interwał ponawiania prób to produktu awarii ciągłego wdrażania i interwał wycofywania wdrożenia. |
|EnableActivateNoWindow| wartość logiczna, domyślna to FALSE|Dynamiczny| Aktywowanego procesu jest tworzony w tle bez żadnych konsoli. |
|EnableDockerHealthCheckIntegration|wartość logiczna, domyślna ma wartość TRUE|Statyczny|Umożliwia integrację docker test kondycji zdarzeń o raport o kondycji systemu sieci szkieletowej usług |
|EnableProcessDebugging|wartość logiczna, domyślna to FALSE|Dynamiczny| Umożliwia uruchamianie na hoście aplikacji w debugerze |
|EndpointProviderEnabled| wartość logiczna, domyślna to FALSE|Statyczny| Umożliwia zarządzanie zasobami punktu końcowego w sieci szkieletowej. Wymaga określenia początek i koniec zakresu portów aplikacji w FabricNode. |
|FabricContainerAppsEnabled| wartość logiczna, domyślna to FALSE|Statyczny| |
|FirewallPolicyEnabled|wartość logiczna, domyślna to FALSE|Statyczny| Umożliwia otwarcie portów zapory dla punktu końcowego zasobów z jawnym porty określone w ServiceManifest |
|GetCodePackageActivationContextTimeout|Zakres czasu, domyślnie jest Common::TimeSpan::FromSeconds(120)|Dynamiczny|Określ zakres czasu w sekundach. Wartość limitu czasu dla wywołań CodePackageActivationContext. To nie ma zastosowania do usług ad hoc. |
|IPProviderEnabled|wartość logiczna, domyślna to FALSE|Statyczny|Umożliwia zarządzanie adresami IP. |
|LinuxExternalExecutablePath|wstring, domyślny jest L "/ usr/bin /" |Statyczny|Katalog podstawowy zewnętrznego pliku wykonywalnego poleceń w węźle.|
|NTLMAuthenticationEnabled|wartość logiczna, domyślna to FALSE|Statyczny| Umożliwia obsługę przez pakiety kodu, które są uruchomione innych użytkowników, dzięki czemu procesów na komputerach można bezpiecznego komunikowania się przy użyciu protokołu NTLM. |
|NTLMAuthenticationPasswordSecret|SecureString, domyślnie jest Common::SecureString(L"")|Statyczny|Jest zaszyfrowany ma służący do generowania haseł dla użytkowników NTLM. Musi być ustawiona, jeśli NTLMAuthenticationEnabled ma wartość true. Zweryfikowane przez narzędzia wdrażania. |
|NTLMSecurityUsersByX509CommonNamesRefreshInterval|Zakres czasu, domyślnie jest Common::TimeSpan::FromMinutes(3)|Dynamiczny|Określ zakres czasu w sekundach. Ustawienia charakterystyczne dla środowiska okresowe interwał, w którym hostingu skanowania pod kątem nowych certyfikatów, które ma być użyty do konfiguracji FileStoreService NTLM. |
|NTLMSecurityUsersByX509CommonNamesRefreshTimeout|Zakres czasu, domyślnie jest Common::TimeSpan::FromMinutes(4)|Dynamiczny| Określ zakres czasu w sekundach. Limit czasu dotyczące konfigurowania uwierzytelniania NTLM użytkowników przy użyciu wspólnej nazwy certyfikatów. Użytkownicy NTLM są wymagane dla FileStoreService udziałów. |
|RegisterCodePackageHostTimeout|Zakres czasu, domyślnie jest Common::TimeSpan::FromSeconds(120)|Dynamiczny| Określ zakres czasu w sekundach. Wartość limitu czasu dla wywołania synchronizacji FabricRegisterCodePackageHost. Dotyczy tylko multi kod pakietu aplikacji hosty jak FWP |
|RequestTimeout|Zakres czasu, domyślnie jest Common::TimeSpan::FromSeconds(30)|Dynamiczny| Określ zakres czasu w sekundach. Ta pozycja reprezentuje limit czasu komunikacji między hostem aplikacji i sieci szkieletowej proces dla różnych hostingu powiązanych operacji, takich jak rejestracji fabrycznej; użytkownika Rejestracja środowiska wykonawczego. |
|RunAsPolicyEnabled| wartość logiczna, domyślna to FALSE|Statyczny| Umożliwia uruchamianie pakiety kodu jako lokalnego konta użytkownika niż użytkownika, w którym sieci szkieletowej proces jest uruchomiony. Aby włączyć te zasady sieci szkieletowej musi działać jako SYSTEM lub użytkownik, który ma SeAssignPrimaryTokenPrivilege. |
|ServiceFactoryRegistrationTimeout| Zakres czasu, domyślnie jest Common::TimeSpan::FromSeconds(120)|Dynamiczny|Określ zakres czasu w sekundach. Wartość limitu czasu dla synchronizacji rejestru (Stateless/Stateful) ServiceFactory wywołania |
|ServiceTypeDisableFailureThreshold |Liczby całkowitej, domyślna to 1 |Dynamiczny|To jest próg liczby awarii, po upływie którego FailoverManager (FM) jest powiadamiany o konieczności Wyłącz typ usługi, w tym węźle i spróbuj inny węzeł do umieszczania. |
|ServiceTypeDisableGraceInterval|Zakres czasu, domyślnie jest Common::TimeSpan::FromSeconds(30)|Dynamiczny|Określ zakres czasu w sekundach. Interwał czasu, po którym można wyłączyć typ usługi |
|ServiceTypeRegistrationTimeout |Czas w sekundach, domyślna to 300 |Dynamiczny|Maksymalny dozwolony czas realizacji ServiceType rejestrowana z sieci szkieletowej |
|UseContainerServiceArguments|wartość logiczna, domyślna ma wartość TRUE|Statyczny|Tej konfiguracji informuje hosting pomijania przekazywanie argumentów (określony w konfiguracji ContainerServiceArguments) do demon docker.|

## <a name="httpgateway"></a>HttpGateway
| **Parametr** | **Dozwolone wartości** | **Zasady uaktualniania** | **Wskazówki lub krótki opis** |
| --- | --- | --- | --- |
|ActiveListeners |Uint, domyślną wartością jest 50 |Statyczny| Liczba odczytów do wysłania do kolejki serwera http. Kontroluje to liczbę żądań, które mogą być spełnione przez HttpGateway. |
|HttpGatewayHealthReportSendInterval |Czas w sekundach, wartość domyślna to 30 |Statyczny|Określ zakres czasu w sekundach. Interwał wysyła Http bramy kondycji wszystkich raportów do Menedżera kondycji. |
|IsEnabled|Wartość logiczna, wartość domyślna to false |Statyczny| Włącza/wyłącza HttpGateway. HttpGateway jest domyślnie wyłączona. |
|MaxEntityBodySize |Uint — wartość domyślna to 4194304 |Dynamiczny|Zapewnia maksymalny rozmiar treści, których można oczekiwać od żądania http. Wartość domyślna to 4MB. Httpgateway zakończy się niepowodzeniem na żądanie, jeśli ma ona treści rozmiar > tej wartości. Rozmiar minimalny fragmentu odczytu jest 4096 bajtów. Tak, to musi być > = 4096. |

## <a name="imagestoreclient"></a>ImageStoreClient
| **Parametr** | **Dozwolone wartości** | **Zasady uaktualniania** | **Wskazówki lub krótki opis** |
| --- | --- | --- | --- |
|ClientCopyTimeout | Czas w sekundach, domyślną jest 1800 |Dynamiczny| Określ zakres czasu w sekundach. Wartość limitu czasu dla żądania kopiowania najwyższego poziomu do usługi magazynu obrazów. |
|ClientDefaultTimeout | Czas w sekundach, domyślna to 180 |Dynamiczny| Określ zakres czasu w sekundach. Wartość limitu czasu dla wszystkich żądań bez przekazywania/z systemem innym niż — pobierania (np. istnieje, Usuń) do usługi magazynu obrazów. |
|ClientDownloadTimeout | Czas w sekundach, domyślną jest 1800 |Dynamiczny| Określ zakres czasu w sekundach. Wartość limitu czasu dla żądania pobierania najwyższego poziomu do usługi magazynu obrazów. |
|ClientListTimeout | Czas w sekundach, domyślna to 600 |Dynamiczny|Określ zakres czasu w sekundach. Wartość limitu czasu dla żądania listy najwyższego poziomu do usługi magazynu obrazów. |
|ClientUploadTimeout |Czas w sekundach, domyślną jest 1800 |Dynamiczny|Określ zakres czasu w sekundach. Wartość limitu czasu dla żądania przekazywania najwyższego poziomu do usługi magazynu obrazów. |

## <a name="imagestoreservice"></a>ImageStoreService
| **Parametr** | **Dozwolone wartości** | **Zasady uaktualniania** | **Wskazówki lub krótki opis** |
| --- | --- | --- | --- |
|Enabled (Włączony) |Wartość logiczna, wartość domyślna to false |Statyczny|Flaga włączone dla ImageStoreService. Domyślnie: false |
|MinReplicaSetSize | Int, domyślna to 3 |Statyczny|MinReplicaSetSize dla ImageStoreService. |
|PlacementConstraints | ciąg, domyślna to "" |Statyczny| PlacementConstraints dla ImageStoreService. |
|QuorumLossWaitDuration | Czas w sekundach, domyślnie jest MaxValue |Statyczny| Określ zakres czasu w sekundach. QuorumLossWaitDuration dla ImageStoreService. |
|ReplicaRestartWaitDuration | Czas w sekundach, wartość domyślna to 60.0 * 30 |Statyczny|Określ zakres czasu w sekundach. ReplicaRestartWaitDuration dla ImageStoreService. |
|StandByReplicaKeepDuration | Czas w sekundach, domyślna to 3600.0 * 2 |Statyczny| Określ zakres czasu w sekundach. StandByReplicaKeepDuration dla ImageStoreService. |
|Wartość TargetReplicaSetSize | Int, domyślna to 7 |Statyczny|TargetReplicaSetSize dla ImageStoreService. |

## <a name="ktllogger"></a>KtlLogger
| **Parametr** | **Dozwolone wartości** | **Zasady uaktualniania** | **Wskazówki lub krótki opis** |
| --- | --- | --- | --- |
|AutomaticMemoryConfiguration |Int, wartość domyślna to 1 |Dynamiczny|Flaga wskazująca, czy ustawienia pamięci należy dynamicznego i automatycznie skonfigurować. Jeśli zero, a następnie ustawienia konfiguracji pamięci są używane bezpośrednio i nie należy zmieniać na podstawie systemu warunków. Jeśli jeden to ustawienia pamięci są konfigurowane automatycznie i mogą ulec zmianie na podstawie systemu warunków. |
|MaximumDestagingWriteOutstandingInKB | Int, domyślna to 0 |Dynamiczny|Liczba KB umożliwia udostępnionego dziennika przesuwających wcześniejsze dedykowanych dziennika. Użyj wartości 0, aby wskazać, brak limitu.
|SharedLogId |ciąg, domyślna to "" |Statyczny|Unikatowy identyfikator guid dla kontenera dziennika udostępniony. Użyj "" Jeśli przy użyciu ścieżki domyślnej w katalogu głównym danych sieci szkieletowej. |
|SharedLogPath |ciąg, domyślna to "" |Statyczny|Ścieżka i nazwa pliku do lokalizacji, aby umieścić kontenera dziennika udostępniony. Użyj "" dla przy użyciu ścieżki domyślnej w katalogu głównym danych sieci szkieletowej. |
|SharedLogSizeInMB |Int, domyślnie jest 8192 |Statyczny|Liczba MB do przydzielenia w kontenerze dziennika udostępniony. |
|WriteBufferMemoryPoolMaximumInKB | Int, domyślna to 0 |Dynamiczny|Liczba KB, aby umożliwić wzrostu do puli pamięci buforu zapisu. Użyj wartości 0, aby wskazać, brak limitu. |
|WriteBufferMemoryPoolMinimumInKB |Int, domyślnie jest 8388608 |Dynamiczny|Liczba KB można wstępnie przydzielić dla puli pamięci buforu zapisu. Użycie wartości 0, aby wskazać, brak limitu domyślnego powinny być zgodne z SharedLogSizeInMB poniżej. |

## <a name="management"></a>Zarządzanie
| **Parametr** | **Dozwolone wartości** | **Zasady uaktualniania** | **Wskazówki lub krótki opis** |
| --- | --- | --- | --- |
|AzureStorageMaxConnections | Int, domyślna to 5000 |Dynamiczny|Maksymalna liczba jednoczesnych połączeń z magazynem platformy azure. |
|AzureStorageMaxWorkerThreads | int, domyślna to 25 |Dynamiczny|Maksymalna liczba wątków roboczych równolegle. |
|AzureStorageOperationTimeout | Czas w sekundach, domyślnie jest 6000 |Dynamiczny|Określ zakres czasu w sekundach. Limit czasu na ukończenie operacji xstore. |
|CleanupApplicationPackageOnProvisionSuccess|wartość logiczna, domyślna to FALSE |Dynamiczny|Ta konfiguracja Włącza lub wyłącza funkcję automatycznego czyszczenia pakietu aplikacji złożenia powiodło się. |
|DisableChecksumValidation | Wartość logiczna, wartość domyślna to false |Statyczny| Ta konfiguracja pozwala włączyć lub wyłączyć Weryfikacja sum kontrolnych podczas inicjowania obsługi aplikacji. |
|DisableServerSideCopy | Wartość logiczna, wartość domyślna to false |Statyczny|Ta konfiguracja Włącza lub wyłącza po stronie serwera kopię pakietu aplikacji w składniku ImageStore podczas inicjowania obsługi aplikacji. |
|ImageCachingEnabled | Wartość logiczna, domyślna to true |Statyczny|Ta konfiguracja pozwala włączyć lub wyłączyć buforowanie. |
|ImageStoreConnectionString |SecureString |Statyczny|Parametry połączenia z katalogiem głównym do magazynu ImageStore. |
|ImageStoreMinimumTransferBPS | Int, domyślny to 1024 |Dynamiczny|Szybkość przesyłu minimalna między klastrem i magazynu ImageStore. Ta wartość jest używana do określenia wartości limitu czasu podczas uzyskiwania dostępu do zewnętrznych składniku ImageStore. Zmień tę wartość tylko wtedy, gdy opóźnienie między klastrem i magazynu ImageStore jest wysoka, aby poczekać klaster, aby pobrać z magazynu zewnętrznego ImageStore. |

## <a name="metricactivitythresholds"></a>MetricActivityThresholds
| **Parametr** | **Dozwolone wartości** |**Zasady uaktualniania**| **Wskazówki lub krótki opis** |
| --- | --- | --- | --- |
|PropertyGroup|KeyIntegerValueMap, domyślna wartość to Brak|Dynamiczny|Określa zbiór MetricActivityThresholds metryki w klastrze. Równoważenie będzie działać, jeśli maxNodeLoad jest większa niż MetricActivityThresholds. Do defragmentowania metryki definiuje ilość równości obciążenia lub poniżej którego usługi sieć szkieletowa będzie uwzględniać węzeł pusty |

## <a name="metricbalancingthresholds"></a>MetricBalancingThresholds
| **Parametr** | **Dozwolone wartości** |**Zasady uaktualniania**| **Wskazówki lub krótki opis** |
| --- | --- | --- | --- |
|PropertyGroup|KeyDoubleValueMap, domyślna wartość to Brak|Dynamiczny|Określa zbiór MetricBalancingThresholds metryki w klastrze. Równoważenie będzie działać, jeśli maxNodeLoad/minNodeLoad jest większa niż MetricBalancingThresholds. Defragmentacja będzie działać, jeśli jest mniejszy niż MetricBalancingThresholds maxNodeLoad/minNodeLoad w co najmniej jednym FD lub UD. |

## <a name="namingservice"></a>NamingService
| **Parametr** | **Dozwolone wartości** | **Zasady uaktualniania** | **Wskazówki lub krótki opis** |
| --- | --- | --- | --- |
|GatewayServiceDescriptionCacheLimit |Int, domyślna to 0 |Statyczny|Maksymalna liczba wpisów przechowywane w pamięci podręcznej opis usługi LRU na brama nazewnictwa (wartość 0 oznacza brak limitu). |
|MaxClientConnections |Int, domyślna to 1000 |Dynamiczny|Maksymalna dozwolona liczba połączeń z klientami na bramę. |
|MaxFileOperationTimeout |Czas w sekundach, wartość domyślna to 30 |Dynamiczny|Określ zakres czasu w sekundach. Maksymalny limit czasu, dozwolone dla operacji usługi Magazyn plików. Określanie większego limitu czasu żądania zostaną odrzucone. |
|MaxIndexedEmptyPartitions |Int, domyślna to 1000 |Dynamiczny|Indeksowane maksymalną liczbę puste partycje, które pozostanie w pamięci podręcznej powiadomienia dotyczące synchronizacji klientów ponownie nawiązującego połączenie. Wszelkie puste partycje powyżej tego numeru zostaną usunięte z indeksu rosnąco wersji wyszukiwania. Ponowne łączenie klientów można nadal zsynchronizować i otrzymywać aktualizacje brakujących partycji pusty; Jednak protokół synchronizacji staje się bardziej kosztowne. |
|MaxMessageSize |Int, domyślna to 4\*1024\*1024 |Statyczny|Maksymalny rozmiar wiadomości do komunikacji z klientem węzła przy użyciu nazw. Łagodzenia skutków ataku DOS; Wartość domyślna to 4MB. |
|MaxNamingServiceHealthReports | Int, domyślna to 10 |Dynamiczny|Maksymalna liczba operacji powolne, przechowujących nazewnictwa usługi Raporty złej kondycji w tym samym czasie. Jeśli 0; wszystkie operacje powolne są wysyłane. |
|MaxOperationTimeout |Czas w sekundach, domyślna to 600 |Dynamiczny|Określ zakres czasu w sekundach. Maksymalny limit czasu, dozwolone dla operacji klienta. Określanie większego limitu czasu żądania zostaną odrzucone. |
|MaxOutstandingNotificationsPerClient |Int, domyślna to 1000 |Dynamiczny|Maksymalna liczba oczekujących powiadomień przed rejestracją klienta jest zamknięte przez bramę. |
|MinReplicaSetSize | Int, domyślna to 3 |Niedozwolone| Minimalna liczba replik Naming Service wymagane do zapisania w celu ukończenia aktualizacji. W przypadku replik mniej niż to aktywnych w systemie niezawodności systemu nie zezwala na aktualizacje w magazynie usługi nazewnictwa dopóki repliki zostaną przywrócone. Ta wartość nigdy nie powinna być większa niż wartość TargetReplicaSetSize. |
|Liczba partycji |Int, domyślna to 3 |Niedozwolone|Liczba partycji Naming Service magazynu do utworzenia. Każda partycja jest właścicielem klucza jednej partycji, odpowiadający jej indeks; klucze partycji tak [0; Liczba partycji) istnieje. Zwiększenie liczby Naming Service partycje zwiększa skali, do której Naming Service mogą wykonywać na dzięki skróceniu średnia ilość danych przechowywanych przez repliką zapasowy ustawić; Koszt zwiększenie użycia zasobów (od momentu PartitionCount * ReplicaSetSize repliki usługi muszą zostać zachowane).|
|PlacementConstraints | ciąg, domyślna to "" |Niedozwolone| Ograniczenia umieszczania dla Naming Service. |
|QuorumLossWaitDuration | Czas w sekundach, domyślnie jest MaxValue |Niedozwolone| Określ zakres czasu w sekundach. Gdy pobiera Naming Service w wyniku utraty kworum; Ten czasomierz uruchamia. Po jego wygaśnięciu FM uwzględni dół repliki jako utraconych; i podjęcie próby odzyskania kworum. Nie, że może to spowodować utratę danych. |
|RepairInterval | Czas w sekundach, domyślna to 5 |Statyczny| Określ zakres czasu w sekundach. Interwał, w którym zostanie uruchomiony nazewnictwa naprawy niespójność między urzędu właściciela i nazwy właściciela. |
|ReplicaRestartWaitDuration | Czas w sekundach, wartość domyślna to (60.0 * 30)|Niedozwolone| Określ zakres czasu w sekundach. Gdy replika Naming Service spadnie; Ten czasomierz uruchamia. Po jego wygaśnięciu FM rozpocznie się zastąpić replik, które są w dół (nie jeszcze uważa ich zgubienia). |
|ServiceDescriptionCacheLimit | Int, domyślna to 0 |Statyczny| Maksymalna liczba wpisów przechowywane w pamięci podręcznej opis usługi LRU w magazynie Naming Service (wartość 0 oznacza brak limitu). |
|ServiceNotificationTimeout |Czas w sekundach, wartość domyślna to 30 |Dynamiczny|Określ zakres czasu w sekundach. Limit czasu używany w celu dostarczenia powiadomienia usługi do klienta. |
|StandByReplicaKeepDuration | Czas w sekundach, domyślna to 3600.0 * 2 |Niedozwolone| Określ zakres czasu w sekundach. Gdy replika Naming Service wrócić z opuszczona; go może już zostały zastąpione. Ten czasomierz Określa, jak długo FM zachowa rezerwy repliki przed usunięciem. |
|Wartość TargetReplicaSetSize |Int, domyślna to 7 |Niedozwolone|Liczba zestawów replik dla każdej partycji magazynu Naming Service. Zwiększenie liczby zestawów replik powoduje podwyższenie poziomu niezawodności informacji w magazynie usługi nazewnictwa; zmniejszenie zmiany informacje zostaną utracone w wyniku awarii węzła; przy koszcie zwiększenie obciążenia sieci szkieletowej systemu Windows i czas potrzebny do przeprowadzania aktualizacji do nazewnictwa danych.|

## <a name="nodebufferpercentage"></a>NodeBufferPercentage
| **Parametr** | **Dozwolone wartości** |**Zasady uaktualniania**| **Wskazówki lub krótki opis** |
| --- | --- | --- | --- |
|PropertyGroup|KeyDoubleValueMap, domyślna wartość to Brak|Dynamiczny|Procent przepustowości węzła na nazwę metryki; używane jako buforu, aby zachować niektóre wolnego miejsca w węźle w przypadku trybu failover. |

## <a name="nodecapacities"></a>NodeCapacities
| **Parametr** | **Dozwolone wartości** | **Zasady uaktualniania** | **Wskazówki lub krótki opis** |
| --- | --- | --- | --- |
|PropertyGroup |NodeCapacityCollectionMap |Statyczny|Kolekcja węzła pojemności dla różnych metryk. |

## <a name="nodedomainids"></a>NodeDomainIds
| **Parametr** | **Dozwolone wartości** | **Zasady uaktualniania** | **Wskazówki lub krótki opis** |
| --- | --- | --- | --- |
|PropertyGroup |NodeFaultDomainIdCollection |Statyczny|W tym artykule opisano domen błędów, do której należy dany węzeł. Domena błędów jest zdefiniowany przez identyfikator URI, który określa lokalizację węzła w centrum danych.  Identyfikatory URI domeny błędów są w formacie fd: / fd/następuje segment ścieżki identyfikatora URI.|
|UpgradeDomainId |ciąg, domyślna to "" |Statyczny|Opisuje węzeł należy do domeny uaktualnienia. |

## <a name="nodeproperties"></a>NodeProperties
| **Parametr** | **Dozwolone wartości** | **Zasady uaktualniania** | **Wskazówki lub krótki opis** |
| --- | --- | --- | --- |
|PropertyGroup |NodePropertyCollectionMap |Statyczny|Kolekcja par klucz wartość ciągu dla właściwości węzła. |

## <a name="paas"></a>Paas
| **Parametr** | **Dozwolone wartości** | **Zasady uaktualniania** | **Wskazówki lub krótki opis** |
| --- | --- | --- | --- |
|ClusterId |ciąg, domyślna to "" |Niedozwolone|X509 certyfikatów Magazyn używany przez sieci szkieletowej dla ochrony konfiguracji. |

## <a name="performancecounterlocalstore"></a>PerformanceCounterLocalStore
| **Parametr** | **Dozwolone wartości** | **Zasady uaktualniania** | **Wskazówki lub krótki opis** |
| --- | --- | --- | --- |
|Liczniki |Ciąg | Dynamiczny |Rozdzielana przecinkami lista liczników wydajności do zbierania. |
|IsEnabled |Wartość logiczna, domyślna to true | Dynamiczny |Flaga wskazuje, czy włączono zbieranie danych licznika wydajności w węźle lokalnym. |
|MaxCounterBinaryFileSizeInMB |Int, wartość domyślna to 1 | Dynamiczny |Maksymalny rozmiar (w MB) dla każdego pliku binarnego licznika wydajności. |
|NewCounterBinaryFileCreationIntervalInMinutes |Int, domyślna to 10 | Dynamiczny |Maksymalny interwał (w sekundach), po którym zostanie utworzony nowy plik binarny licznika wydajności. |
|SamplingIntervalInSeconds |Int, domyślna to 60 | Dynamiczny |Interwał próbkowania dla liczniki wydajności są zbierane. |

## <a name="placementandloadbalancing"></a>PlacementAndLoadBalancing
| **Parametr** | **Dozwolone wartości** | **Zasady uaktualniania** | **Wskazówki lub krótki opis** |
| --- | --- | --- | --- |
|AffinityConstraintPriority | Int, domyślna to 0 | Dynamiczny|Określa priorytet ograniczenia koligacji: 0: twarde; 1: nietrwałego; ujemna: Ignoruj. |
|ApplicationCapacityConstraintPriority | Int, domyślna to 0 | Dynamiczny|Określa priorytet ograniczenie pojemności: 0: twarde; 1: nietrwałego; ujemna: Ignoruj. |
|AutoDetectAvailableResources|wartość logiczna, domyślna ma wartość TRUE|Statyczny|Tej konfiguracji wyzwoli automatyczne wykrywanie dostępnych zasobów w węźle (Procesora i pamięci) Jeśli ta konfiguracja jest równa true — firma Microsoft będzie odczytać rzeczywistej pojemności i popraw je, jeśli użytkownika określono nieprawidłowy węzeł pojemności lub nie zostały zdefiniowane je na wszystkich Jeśli tej konfiguracji jest ustawiona na false - zostaną wykonane następujące czynności  Śledzenie ostrzeżenia użytkownika określono nieprawidłowy węzeł możliwości; ale możemy nie poprawi oznacza ten użytkownik chce mieć możliwości określony jako > niż rzeczywiście ma węzeł lub jeśli pojemności to Niezdefiniowany; zakłada nieograniczone pojemności |
|BalancingDelayAfterNewNode | Czas w sekundach, domyślna to 120 |Dynamiczny|Określ zakres czasu w sekundach. Nie uruchamiaj równoważenia działań w tym czasie po dodaniu nowego węzła. |
|BalancingDelayAfterNodeDown | Czas w sekundach, domyślna to 120 |Dynamiczny|Określ zakres czasu w sekundach. Nie uruchamiaj równoważenia działań w tym czasie po węzeł w dół zdarzeń. |
|CapacityConstraintPriority | Int, domyślna to 0 | Dynamiczny|Określa priorytet ograniczenie pojemności: 0: twarde; 1: nietrwałego; ujemna: Ignoruj. |
|ConsecutiveDroppedMovementsHealthReportLimit | Int, domyślna to 20 | Dynamiczny|Definiuje, ile razy pod rząd, które wystawiony ResourceBalancer przemieszczania są usuwane przed diagnostyki są wykonywane i ostrzeżenia są emitowane. Ujemna: Nie ostrzeżenia emitowane w tej sytuacji. |
|ConstraintFixPartialDelayAfterNewNode | Czas w sekundach, domyślna to 120 |Dynamiczny| Określ zakres czasu w sekundach. DDo nie napraw FaultDomain i UpgradeDomain naruszenia ograniczeń w tym czasie po dodaniu nowego węzła. |
|ConstraintFixPartialDelayAfterNodeDown | Czas w sekundach, domyślna to 120 |Dynamiczny| Określ zakres czasu w sekundach. W tym czasie po węźle zdarzenie naciśnięcia, czy nie napraw FaultDomain i UpgradeDomain naruszenia ograniczenia. |
|ConstraintViolationHealthReportLimit | Int, domyślną wartością jest 50 |Dynamiczny| Definiuje, ile razy ograniczenia naruszania replika musi być trwale Niepoprawione przed diagnostyki są wykonywane i raporty dotyczące kondycji są emitowane. |
|DetailedConstraintViolationHealthReportLimit | Int, domyślna to 200 |Dynamiczny| Definiuje, ile razy ograniczenia naruszania replika musi być trwale Niepoprawione przed diagnostyczne są wykonywane i kondycji, które są emitowane raporty szczegółowe. |
|DetailedDiagnosticsInfoListLimit | int, domyślną jest 15 |Dynamiczny| Definiuje liczbę pozycji diagnostycznych (wraz ze szczegółowymi informacjami) na ograniczenia do uwzględnienia przed obcięcie w diagnostyce.|
|DetailedNodeListLimit | int, domyślną jest 15 |Dynamiczny| Określa liczbę węzłów ograniczenia do uwzględnienia przed obcięcie w raportach Unplaced repliki. |
|DetailedPartitionListLimit | int, domyślną jest 15 |Dynamiczny| Definiuje liczbę partycji na diagnostycznych wpis dla ograniczenia do uwzględnienia przed obcięcie w diagnostyce. |
|DetailedVerboseHealthReportLimit | Int, domyślna to 200 | Dynamiczny|Definiuje, ile razy unplaced repliki musi być trwale unplaced przed są emitowane kondycji szczegółowe raporty. |
|FaultDomainConstraintPriority | Int, domyślna to 0 |Dynamiczny| Określa priorytet ograniczenia domeny błędów: 0: twarde; 1: nietrwałego; ujemna: Ignoruj. |
|GlobalMovementThrottleCountingInterval | Czas w sekundach, domyślna to 600 |Statyczny| Określ zakres czasu w sekundach. Wskazuje ostatnich interwał, do których chcesz śledzić na domeny przemieszczania repliki (używane wraz z GlobalMovementThrottleThreshold). Może być równa 0 Aby zignorować globalnego ograniczania przepustowości całkowicie. |
|GlobalMovementThrottleThreshold | Uint — wartość domyślna to 1000 |Dynamiczny| Maksymalna liczba przeniesień dozwolone w fazie równoważenia w ciągu ostatnich interwale wskazywanym przez GlobalMovementThrottleCountingInterval. |
|GlobalMovementThrottleThresholdForBalancing | Uint — wartość domyślna to 0 | Dynamiczny|Maksymalna liczba przeniesień dozwolone w fazie równoważenia w ciągu ostatnich interwale wskazywanym przez GlobalMovementThrottleCountingInterval. 0 oznacza brak limitu. |
|GlobalMovementThrottleThresholdForPlacement | Uint — wartość domyślna to 0 |Dynamiczny| Maksymalna liczba przeniesień dozwolone w fazie umieszczania w ciągu ostatnich interwale wskazywanym przez GlobalMovementThrottleCountingInterval.0 oznacza brak limitu.|
|GlobalMovementThrottleThresholdPercentage|Double domyślna to 0|Dynamiczny|Maksymalna liczba całkowita liczba przeniesień dozwolone w fazach równoważenie i umieszczania (wyrażone jako procent całkowitej liczby replik w klastrze) w ciągu ostatnich interwale wskazywanym przez GlobalMovementThrottleCountingInterval. 0 oznacza brak limitu. Jeśli ta i podano GlobalMovementThrottleThreshold; następnie jest używany więcej zachowawcze limit.|
|GlobalMovementThrottleThresholdPercentageForBalancing|Double domyślna to 0|Dynamiczny|Maksymalna liczba przeniesień dozwolone w fazie równoważenia (wyrażone jako procent całkowitej liczby replik w PLB) w ciągu ostatnich interwale wskazywanym przez GlobalMovementThrottleCountingInterval. 0 oznacza brak limitu. Jeśli ta i podano GlobalMovementThrottleThresholdForBalancing; następnie jest używany więcej zachowawcze limit.|
|InBuildThrottlingAssociatedMetric | ciąg, domyślna to "" |Statyczny| Skojarzone nazwę metryki dla tego ograniczenia przepustowości. |
|InBuildThrottlingEnabled | Wartość logiczna, wartość domyślna to false |Dynamiczny| Ustal, czy jest włączone ograniczenie w kompilacji. |
|InBuildThrottlingGlobalMaxValue | Int, domyślna to 0 |Dynamiczny|Maksymalna liczba replik wbudowany dozwolone globalnie. |
|InterruptBalancingForAllFailoverUnitUpdates | Wartość logiczna, wartość domyślna to false | Dynamiczny|Określa, jeśli dowolny typ aktualizacji jednostki trybu failover powinny szybkiego przerwania lub powolna równoważenia, uruchom. Z określonych równoważenia "false", uruchom zostanie przerwana w przypadku FailoverUnit: jest utworzone/usunąć; Brak replik; zmienić lokalizację repliki podstawowej lub zmiany liczby replik. Równoważenie Uruchom nie zostanie przerwana w innych przypadkach — jeśli FailoverUnit: ma dodatkowe repliki; zmienić wszystkie flagi repliki; zmienić tylko wersję partycji lub innym przypadku. |
|MinConstraintCheckInterval | Czas w sekundach, wartość domyślna to 1 |Dynamiczny| Określ zakres czasu w sekundach. Określa minimalną ilość czasu, jaki musi minąć, zanim dwa kolejne ograniczenia sprawdzania zaokrąglenie. |
|MinLoadBalancingInterval | Czas w sekundach, domyślna to 5 |Dynamiczny| Określ zakres czasu w sekundach. Określa minimalną ilość czasu, który musi upłynąć przed dwóch kolejnych zaokrąglenie równoważenia. |
|MinPlacementInterval | Czas w sekundach, wartość domyślna to 1 |Dynamiczny| Określ zakres czasu w sekundach. Określa minimalną ilość czasu, który musi upłynąć przed dwóch kolejnych umieszczania zaokrąglenie. |
|MoveExistingReplicaForPlacement | Wartość logiczna, domyślna to true |Dynamiczny|Ustawienie, które pozwala określić, jeśli można przenieść istniejącą replikę podczas umieszczania. |
|MovementPerPartitionThrottleCountingInterval | Czas w sekundach, domyślna to 600 |Statyczny| Określ zakres czasu w sekundach. Wskazuje ostatnich interwał, do których chcesz śledzić przeniesień repliki dla każdej partycji (używane wraz z MovementPerPartitionThrottleThreshold). |
|MovementPerPartitionThrottleThreshold | Uint, domyślną wartością jest 50 |Dynamiczny| Przemieszczana związanych z równoważenia zostanie przeprowadzona dla partycji, jeśli liczba równoważenia pokrewne przemieszczania replik tej partycji ma osiągnięto lub przekroczono MovementPerFailoverUnitThrottleThreshold w ciągu ostatnich interwale wskazywanym przez MovementPerPartitionThrottleCountingInterval. |
|MoveParentToFixAffinityViolation | Wartość logiczna, wartość domyślna to false |Dynamiczny| Ustawienie określa, jeśli nadrzędny repliki mogą zostać przeniesione do napraw ograniczenia koligacji.|
|PartiallyPlaceServices | Wartość logiczna, domyślna to true |Dynamiczny| Określa, czy wszystkie repliki usługi w klastrze zostanie umieszczona "wszystkie lub żadne" podany ograniczone węzłów odpowiednie dla nich.|
|PlaceChildWithoutParent | Wartość logiczna, domyślna to true | Dynamiczny|Ustawienie, które określa, czy podrzędnych usługi repliki można umieścić w przypadku replikacji nie jest uruchomiony. |
|PlacementConstraintPriority | Int, domyślna to 0 | Dynamiczny|Określa priorytet ograniczenia umieszczania: 0: twarde; 1: nietrwałego; ujemna: Ignoruj. |
|PlacementConstraintValidationCacheSize | Int, domyślna to 10 000 |Dynamiczny| Ogranicza rozmiar tabeli używany do szybkiego sprawdzania poprawności i buforowanie wyrażeń ograniczeń umieszczania. |
|PlacementSearchTimeout | Czas w sekundach, domyślna to 0,5 |Dynamiczny| Określ zakres czasu w sekundach. Podczas umieszczania usług. Wyszukiwanie co najwyżej takiej długości przed zwróceniem wyniku. |
|PLBRefreshGap | Czas w sekundach, wartość domyślna to 1 |Dynamiczny| Określ zakres czasu w sekundach. Określa minimalną ilość czasu, jaki musi minąć, zanim PLB odświeża stan ponownie. |
|PreferredLocationConstraintPriority | Int, domyślna to 2| Dynamiczny|Określa priorytet ograniczenie lokalizacji preferowanych: 0: twarde; 1: nietrwałego; 2: Optymalizacja; ujemna: Ignoruj |
|PreventTransientOvercommit | Wartość logiczna, wartość domyślna to false | Dynamiczny|Określa powinien PLB natychmiast liczby zasobów, które będą zwalniane przez inicjowane przenosi. Domyślnie; PLB można zainicjować Przenieś i przeniesienie w tym samym węźle, który można utworzyć przejściowej nadmiarowego zatwierdzania. Ustawienie tego parametru na wartość true uniemożliwi tego rodzaju z overcommits defragmentacji na żądanie (alias placementWithMove) zostanie wyłączone. |
|ScaleoutCountConstraintPriority | Int, domyślna to 0 |Dynamiczny| Określa priorytet ograniczenie liczby skalowania w poziomie: 0: twarde; 1: nietrwałego; ujemna: Ignoruj. |
|SwapPrimaryThrottlingAssociatedMetric | ciąg, domyślna to ""|Statyczny| Skojarzone nazwę metryki dla tego ograniczenia przepustowości. |
|SwapPrimaryThrottlingEnabled | Wartość logiczna, wartość domyślna to false|Dynamiczny| Ustal, czy jest włączone ograniczenie podstawowe wymiany. |
|SwapPrimaryThrottlingGlobalMaxValue | Int, domyślna to 0 |Dynamiczny| Maksymalna liczba repliki podstawowej wymiany dozwolone globalnie. |
|TraceCRMReasons |Wartość logiczna, domyślna to true |Dynamiczny|Określa, czy śledzenie przyczyn CRM wystawiony przemieszczania do kanału zdarzenia operacyjne. |
|UpgradeDomainConstraintPriority | Int, wartość domyślna to 1| Dynamiczny|Określa priorytet ograniczenia domeny uaktualnień: 0: twarde; 1: nietrwałego; ujemna: Ignoruj. |
|UseMoveCostReports | Wartość logiczna, wartość domyślna to false | Dynamiczny|Powoduje, że LB ignoruje element koszt oceniania funkcji; Wynikowa potencjalnie dużej liczby przenosi do lepszego zrównoważonym umieszczania. |
|UseSeparateSecondaryLoad | Wartość logiczna, domyślna to true | Dynamiczny|Ustawienie, które określa, czy używać różnych dodatkowej obciążenia. |
|ValidatePlacementConstraint | Wartość logiczna, domyślna to true |Dynamiczny| Określa, czy wyrażenie PlacementConstraint usługi sprawdzania poprawności po zaktualizowaniu usługi ServiceDescription. |
|VerboseHealthReportLimit | Int, domyślna to 20 | Dynamiczny|Definiuje, ile razy replika musi przejść unplaced przed ostrzeżenie kondycji został zgłoszony dla niego (jeśli jest włączone raportowanie kondycji pełne). |

## <a name="reconfigurationagent"></a>ReconfigurationAgent
| **Parametr** | **Dozwolone wartości** | **Zasady uaktualniania** | **Wskazówki lub krótki opis** |
| --- | --- | --- | --- |
|ApplicationUpgradeMaxReplicaCloseDuration | Czas w sekundach, domyślna to 900 |Dynamiczny|Określ zakres czasu w sekundach. Czas, dla którego system będzie oczekiwał przed zakończeniem hostów usługi, które mają repliki są nadal w zamknięte podczas uaktualniania aplikacji.|
|FabricUpgradeMaxReplicaCloseDuration | Czas w sekundach, domyślna to 900 |Dynamiczny| Określ zakres czasu w sekundach. Czas, dla którego system będzie oczekiwał przed zakończeniem hostów usługi, które mają repliki są nadal w zamknięte podczas uaktualnienia programu fabric. |
|GracefulReplicaShutdownMaxDuration|Zakres czasu, domyślnie jest Common::TimeSpan::FromSeconds(120)|Dynamiczny|Określ zakres czasu w sekundach. Czas, dla którego system będzie oczekiwał przed zakończeniem hostów usługi, które mają repliki są nadal w Zamknij. Jeśli ta wartość jest równa 0, nie należy poinstruować replik zamknięcia.|
|NodeDeactivationMaxReplicaCloseDuration | Czas w sekundach, domyślna to 900 |Dynamiczny|Określ zakres czasu w sekundach. Czas, dla którego system będzie oczekiwał przed zakończeniem hostów usługi, które mają repliki są nadal w zamknięte podczas dezaktywacji węzła. |
|PeriodicApiSlowTraceInterval | Czas w sekundach, domyślna to 5 minut |Dynamiczny| Określ zakres czasu w sekundach. PeriodicApiSlowTraceInterval Określa interwał, w którym powolne wywołania interfejsu API będzie ponownego wyświetlania przez monitor interfejsu API. |
|ReplicaChangeRoleFailureRestartThreshold|Int, domyślna to 10|Dynamiczny| Liczba całkowita. Określ liczbę błędów interfejsu API podczas podwyższania poziomu podstawowego, po którym zostaną zastosowane automatycznie ograniczenie akcji (ponowne uruchomienie repliki). |
|ReplicaChangeRoleFailureWarningReportThreshold|int, domyślna to 2147483647|Dynamiczny| Liczba całkowita. Określ liczbę błędów interfejsu API podczas podwyższania poziomu podstawowego, po którym ostrzeżenie raport o kondycji zostanie wygenerowany.|
|ServiceApiHealthDuration | Czas w sekundach, domyślna to 30 minut |Dynamiczny| Określ zakres czasu w sekundach. ServiceApiHealthDuration definiuje, jak długo poczekamy interfejsu API usługi, do uruchomienia przed rozpoczęciem możemy raportu złej kondycji. |
|ServiceReconfigurationApiHealthDuration | Czas w sekundach, wartość domyślna to 30 |Dynamiczny| Określ zakres czasu w sekundach. ServiceReconfigurationApiHealthDuration definiuje, jak długo poczekamy interfejsu API usługi, do uruchomienia przed rozpoczęciem możemy raportu w złej kondycji. Dotyczy to wywołania interfejsu API, które mają wpływ na dostępność.|

## <a name="replication"></a>Replikacja
| **Parametr** | **Dozwolone wartości** | **Zasady uaktualniania**| **Wskazówki lub krótki opis** |
| --- | --- | --- | --- |
|BatchAcknowledgementInterval|Zakres czasu, domyślnie jest Common::TimeSpan::FromMilliseconds(15)|Statyczny|Określ zakres czasu w sekundach. Określa czas oczekiwania po otrzymaniu operacji przed odsyła potwierdzenia replikatora. Inne operacje otrzymanych w tym czasie będzie miał ich potwierdzeń wysyłane z powrotem w pojedynczym komunikacie -> ograniczenia ruchu sieciowego, ale potencjalnie zmniejszenie przepływności replikatora.|
|MaxCopyQueueSize|uint, domyślny to 1024|Statyczny|Jest to maksymalna wartość Określa początkowy rozmiar kolejki, co umożliwia utrzymywanie operacji replikacji. Należy pamiętać, że musi być potęgą liczby 2. Jeśli w czasie wykonywania kolejki rozwoju na tę operację rozmiar zostanie zdławionych między replikatorów podstawowego i pomocniczego.|
|MaxPrimaryReplicationQueueMemorySize|Uint — wartość domyślna to 0|Statyczny|Jest to maksymalna wartość kolejki podstawowej replikacji w bajtach.|
|MaxPrimaryReplicationQueueSize|uint, domyślny to 1024|Statyczny|Jest to maksymalna liczba operacji, które może znajdować się w kolejce podstawowej replikacji. Należy pamiętać, że musi być potęgą liczby 2.|
|MaxReplicationMessageSize|uint, domyślny jest 52428800|Statyczny|Maksymalny rozmiar komunikatu operacji replikacji. Domyślną jest 50MB.|
|MaxSecondaryReplicationQueueMemorySize|Uint — wartość domyślna to 0|Statyczny|Jest to maksymalna wartość kolejki dodatkowej replikacji w bajtach.|
|MaxSecondaryReplicationQueueSize|uint, domyślna wartość to 2048|Statyczny|Jest to maksymalna liczba operacji, które może znajdować się w kolejce replikacji dodatkowej. Należy pamiętać, że musi być potęgą liczby 2.|
|QueueHealthMonitoringInterval|Zakres czasu, domyślnie jest Common::TimeSpan::FromSeconds(30)|Statyczny|Określ zakres czasu w sekundach. Ta wartość określa okres czasu, używany przez Replikator do monitorowania kondycji ostrzeżenie/błąd zdarzeń kolejki operacji replikacji. Wartość "0" powoduje wyłączenie monitorowania kondycji |
|QueueHealthWarningAtUsagePercent|uint, domyślny to 80|Statyczny|Ta wartość Określa użycie kolejki replikacji (w procentach) po upływie którego możemy zgłaszanie ostrzeżenie kolejki wysokiego użycia. Firma Microsoft zrobić po okresie prolongaty, o QueueHealthMonitoringInterval. Jeśli użycie kolejki spada poniżej tej wartości procentowej w okresie prolongaty|
|ReplicatorAddress|ciąg, domyślny jest L "localhost:0"|Statyczny|Punkt końcowy w postaci ciągu - IP:Port, który jest używany przez Replikator sieci szkieletowej systemu Windows do nawiązywania połączeń z innych replik w celu wysyłania i odbierania operacji.|
|ReplicatorListenAddress|ciąg, domyślny jest L "localhost:0"|Statyczny|Punkt końcowy w postaci ciągu - IP:Port, który jest używany przez Replikator sieci szkieletowej systemu Windows do operacji odbioru z innych replik.|
|ReplicatorPublishAddress|ciąg, domyślny jest L "localhost:0"|Statyczny|Punkt końcowy w postaci ciągu - IP:Port, który jest używany przez Replikator sieci szkieletowej systemu Windows do wysłania operacji do innych replik.|
|retryInterval|Zakres czasu, domyślnie jest Common::TimeSpan::FromSeconds(5)|Statyczny|Określ zakres czasu w sekundach. Podczas operacji zostało utracone lub odrzucone ten czasomierz Określa, jak często replikatora ponowi operację wysyłania.|

## <a name="resourcemonitorservice"></a>ResourceMonitorService
| **Parametr** | **Dozwolone wartości** | **Zasady uaktualniania**| **Wskazówki lub krótki opis** |
| --- | --- | --- | --- |
|IsEnabled|wartość logiczna, domyślna to FALSE |Statyczny|Kontroluje, czy usługa jest włączona w klastrze, czy nie. |

## <a name="runas"></a>Uruchom jako
| **Parametr** | **Dozwolone wartości** | **Zasady uaktualniania** | **Wskazówki lub krótki opis** |
| --- | --- | --- | --- |
|RunAsAccountName |ciąg, domyślna to "" |Dynamiczny|Wskazuje nazwę konta Uruchom jako. Jest to potrzebne tylko dla konta "DomainUser" lub "ManagedServiceAccount" typu. Prawidłowe wartości to "domena\użytkownik" lub "user@domain". |
|RunAsAccountType|ciąg, domyślna to "" |Dynamiczny|Wskazuje typ konta Uruchom jako. Jest to potrzebne dla dowolnego RunAs sekcji prawidłowe wartości to "DomainUser/NetworkService/ManagedServiceAccount/LocalSystem".|
|RunAsPassword|ciąg, domyślna to "" |Dynamiczny|Wskazuje hasło do konta Uruchom jako. Jest to potrzebne tylko dla typu konta "DomainUser". |

## <a name="runasdca"></a>RunAs_DCA
| **Parametr** | **Dozwolone wartości** | **Zasady uaktualniania** | **Wskazówki lub krótki opis** |
| --- | --- | --- | --- |
|RunAsAccountName |ciąg, domyślna to "" |Dynamiczny|Wskazuje nazwę konta Uruchom jako. Jest to potrzebne tylko dla konta "DomainUser" lub "ManagedServiceAccount" typu. Prawidłowe wartości to "domena\użytkownik" lub "user@domain". |
|RunAsAccountType|ciąg, domyślna to "" |Dynamiczny|Wskazuje typ konta Uruchom jako. Jest to potrzebne dla dowolnego RunAs sekcji prawidłowe wartości to "LocalUser/DomainUser/NetworkService/ManagedServiceAccount/LocalSystem". |
|RunAsPassword|ciąg, domyślna to "" |Dynamiczny|Wskazuje hasło do konta Uruchom jako. Jest to potrzebne tylko dla typu konta "DomainUser". |

## <a name="runasfabric"></a>RunAs_Fabric
| **Parametr** | **Dozwolone wartości** | **Zasady uaktualniania** | **Wskazówki lub krótki opis** |
| --- | --- | --- | --- |
|RunAsAccountName |ciąg, domyślna to "" |Dynamiczny|Wskazuje nazwę konta Uruchom jako. Jest to potrzebne tylko dla konta "DomainUser" lub "ManagedServiceAccount" typu. Prawidłowe wartości to "domena\użytkownik" lub "user@domain". |
|RunAsAccountType|ciąg, domyślna to "" |Dynamiczny|Wskazuje typ konta Uruchom jako. Jest to potrzebne dla dowolnego RunAs sekcji prawidłowe wartości to "LocalUser/DomainUser/NetworkService/ManagedServiceAccount/LocalSystem". |
|RunAsPassword|ciąg, domyślna to "" |Dynamiczny|Wskazuje hasło do konta Uruchom jako. Jest to potrzebne tylko dla typu konta "DomainUser". |

## <a name="runashttpgateway"></a>RunAs_HttpGateway
| **Parametr** | **Dozwolone wartości** | **Zasady uaktualniania** | **Wskazówki lub krótki opis** |
| --- | --- | --- | --- |
|RunAsAccountName |ciąg, domyślna to "" |Dynamiczny|Wskazuje nazwę konta Uruchom jako. Jest to potrzebne tylko dla konta "DomainUser" lub "ManagedServiceAccount" typu. Prawidłowe wartości to "domena\użytkownik" lub "user@domain". |
|RunAsAccountType|ciąg, domyślna to "" |Dynamiczny|Wskazuje typ konta Uruchom jako. Jest to potrzebne dla dowolnego RunAs sekcji prawidłowe wartości to "LocalUser/DomainUser/NetworkService/ManagedServiceAccount/LocalSystem". |
|RunAsPassword|ciąg, domyślna to "" |Dynamiczny|Wskazuje hasło do konta Uruchom jako. Jest to potrzebne tylko dla typu konta "DomainUser". |

## <a name="security"></a>Bezpieczeństwo
| **Parametr** | **Dozwolone wartości** |**Zasady uaktualniania**| **Wskazówki lub krótki opis** |
| --- | --- | --- | --- |
|AADClientApplication|ciąg, domyślny jest L""|Statyczny|Nazwa aplikacji klienta natywnego lub identyfikator reprezentujący klientów sieci szkieletowej |
|AADClusterApplication|ciąg, domyślny jest L""|Statyczny|Nazwa aplikacji interfejsu API sieci Web lub identyfikator reprezentujący klastra |
|AADTenantId|ciąg, domyślny jest L""|Statyczny|Identyfikator dzierżawcy (GUID) |
|AdminClientCertThumbprints|ciąg, domyślny jest L""|Dynamiczny|Odciski palców certyfikatów używanych przez klientów w roli administratora. Jest listy rozdzielanej przecinkami nazw. |
|AdminClientClaims|ciąg, domyślny jest L""|Dynamiczny|Wszystkie oświadczenia można oczekiwać od klientów administratora. tego samego formatu co ClientClaims; Ta lista pobiera wewnętrznie dodane do ClientClaims; dlatego nie trzeba również dodać te same pozycje do ClientClaims. |
|AdminClientIdentities|ciąg, domyślny jest L""|Dynamiczny|Tożsamości systemu Windows dla klientów sieci szkieletowej w roli administratora; użyty do autoryzacji operacje uprzywilejowane sieci szkieletowej. Jest to rozdzielana przecinkami lista; Każdy wpis jest nazwa konta domeny lub nazwę grupy. Dla wygody; konto, na którym uruchomiono fabric.exe automatycznie przypisano rolę administratora; to jest grupa ServiceFabricAdministrators. |
|CertificateExpirySafetyMargin|Zakres czasu, domyślnie jest Common::TimeSpan::FromMinutes(43200)|Statyczny|Określ zakres czasu w sekundach. Margines bezpieczeństwa do wygaśnięcia certyfikatu; certyfikat kondycji raportu zmiany stanu z OK ostrzeżenie w przypadku wygaśnięcia bliżej niż to. Domyślny to 30 dni. |
|CertificateHealthReportingInterval|Zakres czasu, domyślnie jest Common::TimeSpan::FromSeconds(3600 * 8)|Statyczny|Określ zakres czasu w sekundach. Określ interwał raportowania kondycji certyfikatu; domyślne to 8 godzin; ustawienie na wartość 0 powoduje wyłączenie raportowania kondycji certyfikatu |
|ClientCertThumbprints|ciąg, domyślny jest L""|Dynamiczny|Odciski palców certyfikatów używanych przez klientów do komunikowania się z klastrem; klaster używa to autoryzować połączenia przychodzącego. Jest listy rozdzielanej przecinkami nazw. |
|ClientClaimAuthEnabled|wartość logiczna, domyślna to FALSE|Statyczny|Wskazuje, czy uwierzytelnianie na podstawie oświadczeń jest włączone na klientach; Ustawienie to niejawnie true ustawia ClientRoleEnabled. |
|ClientClaims|ciąg, domyślny jest L""|Dynamiczny|Wszystkie oświadczenia można oczekiwać od klientów do łączenia z bramą. Jest to lista "Lub": ClaimsEntry \| \| ClaimsEntry \| \| ClaimsEntry... każdego ClaimsEntry znajduje się lista "I": typ oświadczenia = ClaimValue & & Typ oświadczenia = ClaimValue & & Typ oświadczenia = ClaimValue... |
|ClientIdentities|ciąg, domyślny jest L""|Dynamiczny|Windows tożsamości klienta fabricclient z rolą; Brama nazewnictwa używa go do autoryzowania połączeń przychodzących. Jest to rozdzielana przecinkami lista; Każdy wpis jest nazwa konta domeny lub nazwę grupy. Dla wygody; konto, na którym uruchomiono fabric.exe jest automatycznie dozwolony; są to grupy ServiceFabricAllowedUsers i ServiceFabricAdministrators. |
|ClientRoleEnabled|wartość logiczna, domyślna to FALSE|Statyczny|Wskazuje, czy rola klienta jest włączone; Po ustawieniu na wartość true; Klienci są przypisywani role oparte na tożsamości. Dla wersji 2; Włączanie to oznacza, że klient nie znajduje się w AdminClientCommonNames/AdminClientIdentities można wykonywać tylko operacje tylko do odczytu. |
|ClusterCertThumbprints|ciąg, domyślny jest L""|Dynamiczny|Odciski palców certyfikatów, można dołączyć do klastra; listę rozdzielonych przecinkami nazw. |
|ClusterCredentialType|ciąg, domyślny jest L "None"|Niedozwolone|Wskazuje typ poświadczenia zabezpieczeń używane w celu zabezpieczania klastra. Prawidłowe wartości to "Brak/X509/systemu Windows" |
|ClusterIdentities|ciąg, domyślny jest L""|Dynamiczny|Tożsamości systemu Windows z węzłów klastra; używane na potrzeby autoryzacji członkostwa w klastrze. Jest to rozdzielana przecinkami lista; Każdy wpis jest nazwa konta domeny lub nazwę grupy |
|ClusterSpn|ciąg, domyślny jest L""|Niedozwolone|Główna nazwa usługi klastra; gdy sieci szkieletowej uruchamia jako użytkownik domeny jednego (gMSA/domeny konta użytkownika). Jest główną nazwę usługi odbiorników dzierżawy i odbiorników w fabric.exe: odbiorniki federacyjnego; obiekty nasłuchujące wewnętrznego replikacja; środowisko uruchomieniowe usługi odbiornika i odbiornika bramy nazewnictwa. To powinno być puste podczas uruchamiania sieci szkieletowej jako konta komputera; w takim przypadku połączenie po stronie obliczeniowe odbiornika SPN odbiornik transportu adres nadawcy. |
|CrlCheckingFlag|uint, domyślny jest 0x40000000|Dynamiczny|Domyślne flagi weryfikacji łańcucha certyfikatu; może być zastąpiona przez flagi specyficzne dla składników; np. federacyjnego/X509CertChainFlags 0x10000000 CERT_CHAIN_REVOCATION_CHECK_END_CERT 0x20000000 CERT_CHAIN_REVOCATION_CHECK_CHAIN 0x40000000 CERT_CHAIN_REVOCATION_CHECK_CHAIN_EXCLUDE_ROOT 0x80000000 CERT_CHAIN_REVOCATION_CHECK_CACHE_ TYLKO ustawienie na 0 spowoduje wyłączenie listy CRL sprawdzanie pełną listę obsługiwanych wartości jest opisane przez wartość elementu dwFlags z CertGetCertificateChain: http://msdn.microsoft.com/library/windows/desktop/aa376078(v=vs.85).aspx |
|CrlDisablePeriod|Zakres czasu, domyślnie jest Common::TimeSpan::FromMinutes(15)|Dynamiczny|Określ zakres czasu w sekundach. Jak długo sprawdzanie listy CRL jest wyłączone dla danego certyfikatu po napotkaniu błędu w trybie offline; Jeśli listy CRL w trybie offline błąd można zignorować. |
|CrlOfflineHealthReportTtl|Zakres czasu, domyślnie jest Common::TimeSpan::FromMinutes(1440)|Dynamiczny|Określ zakres czasu w sekundach. |
|DisableFirewallRuleForDomainProfile| wartość logiczna, domyślna ma wartość TRUE |Statyczny| Wskazuje, czy reguły zapory nie powinno być włączone dla profil domeny |
|DisableFirewallRuleForPrivateProfile| wartość logiczna, domyślna ma wartość TRUE |Statyczny| Wskazuje, czy reguły zapory nie powinno być włączone dla profil prywatny | 
|DisableFirewallRuleForPublicProfile| wartość logiczna, domyślna ma wartość TRUE | Statyczny|Wskazuje, czy reguły zapory nie powinno być włączone dla profilu publicznego |
|FabricHostSpn| ciąg, domyślny jest L"" |Statyczny| Główna nazwa usługi z elementu FabricHost; Kiedy sieci szkieletowej działają jako użytkownik domeny jednego (gMSA/domeny konta użytkownika), a elementu FabricHost zostanie uruchomiona w ramach konta komputera. Jest SPN IPC odbiornika dla elementu FabricHost; Domyślnie powinna pozostać pusta od elementu FabricHost zostanie uruchomiona w ramach konta komputera. |
|IgnoreCrlOfflineError|wartość logiczna, domyślna to FALSE|Dynamiczny|Czy należy zignorować błąd w trybie offline listy CRL, gdy przychodzący certyfikaty klienta sprawdzi po stronie serwera |
|IgnoreSvrCrlOfflineError|wartość logiczna, domyślna ma wartość TRUE|Dynamiczny|Czy należy zignorować błąd w trybie offline listy CRL, gdy po stronie klienta sprawdza, czy certyfikaty serwera przychodzące; Domyślna wartość true. Ataki z serwera odwołane certyfikaty wymagają naruszenie DNS; trudniejsze niż z certyfikatów odwołanych przez klienta. |
|ServerAuthCredentialType|ciąg, domyślny jest L "None"|Statyczny|Wskazuje typ poświadczeń zabezpieczeń do użycia w celu zabezpieczenia komunikacji między klienta fabricclient z rolą i klastra. Prawidłowe wartości to "Brak/X509/systemu Windows" |
|ServerCertThumbprints|ciąg, domyślny jest L""|Dynamiczny|Odciski palców certyfikatów serwera używane przez klaster do komunikowania się klientów. Klienci używają tego do uwierzytelniania w klastrze. Jest listy rozdzielanej przecinkami nazw. |
|SettingsX509StoreName| ciąg, domyślny jest L "MY"| Dynamiczny|X509 certyfikatu Magazyn używany przez sieci szkieletowej dla ochrony konfiguracji |
|UseClusterCertForIpcServerTlsSecurity|wartość logiczna, domyślna to FALSE|Statyczny|Czy do korzystania z certyfikatu klastra do zabezpieczania TLS serwera IPC transportu jednostki |
|X509Folder|ciąg, domyślny jest /var/lib/waagent|Statyczny|Folderu gdzie X509 znajdują się certyfikaty i klucze prywatne |

## <a name="securityadminclientx509names"></a>Zabezpieczenia/AdminClientX509Names
| **Parametr** | **Dozwolone wartości** | **Zasady uaktualniania** | **Wskazówki lub krótki opis** |
| --- | --- | --- | --- |
|PropertyGroup|X509NameMap, domyślna wartość to Brak|Dynamiczny| |

## <a name="securityclientaccess"></a>Zabezpieczenia/ClientAccess
| **Parametr** | **Dozwolone wartości** | **Zasady uaktualniania** | **Wskazówki lub krótki opis** |
| --- | --- | --- | --- |
|ActivateNode |ciąg, domyślną jest "Admin" |Dynamiczny| Konfiguracja zabezpieczeń dla aktywacji, a węzeł. |
|CancelTestCommand |ciąg, domyślną jest "Admin" |Dynamiczny| Anuluje określonych TestCommand — Jeśli w locie. |
|CodePackageControl |ciąg, domyślną jest "Admin" |Dynamiczny| Konfiguracja zabezpieczeń ponownego uruchamiania pakietów kodu. |
|CreateApplication |ciąg, domyślną jest "Admin" | Dynamiczny|Konfiguracja zabezpieczeń do tworzenia aplikacji. |
|CreateComposeDeployment|ciąg, domyślny jest L "Admin"| Dynamiczny|Tworzy wdrożenia Redaguj opisanego przez tworzenia plików |
|CreateName |ciąg, domyślną jest "Admin" |Dynamiczny|Konfiguracja zabezpieczeń do utworzenia identyfikatora URI nazewnictwa. |
|CreateService |ciąg, domyślną jest "Admin" |Dynamiczny| Konfiguracja zabezpieczeń do tworzenia usług. |
|CreateServiceFromTemplate |ciąg, domyślną jest "Admin" |Dynamiczny|Konfiguracja zabezpieczeń do tworzenia usług z szablonu. |
|DeactivateNode |ciąg, domyślną jest "Admin" |Dynamiczny| Dezaktywacja węzła konfiguracji zabezpieczeń. |
|DeactivateNodesBatch |ciąg, domyślną jest "Admin" |Dynamiczny| Konfiguracja zabezpieczeń dezaktywowanie wiele węzłów. |
|Usuwanie |ciąg, domyślną jest "Admin" |Dynamiczny| Konfiguracje zabezpieczeń dla obrazu magazynu operację usuwania klienta. |
|DeleteApplication |ciąg, domyślną jest "Admin" |Dynamiczny| Konfiguracja zabezpieczeń do usunięcia aplikacji. |
|DeleteComposeDeployment|ciąg, domyślny jest L "Admin"| Dynamiczny|Usuwa tworzenia wdrożenia |
|DeleteName |ciąg, domyślną jest "Admin" |Dynamiczny|Konfiguracja zabezpieczeń do usunięcia identyfikatora URI nazewnictwa. |
|Funkcji DeleteService modułu |ciąg, domyślną jest "Admin" |Dynamiczny|Konfiguracja zabezpieczeń do usunięcia usługi. |
|EnumerateProperties |ciąg, domyślna to "Admin\|\|użytkownika" | Dynamiczny|Konfiguracja zabezpieczeń dla nazw właściwości wyliczenia. |
|EnumerateSubnames |ciąg, domyślna to "Admin\|\|użytkownika" |Dynamiczny| Konfiguracja zabezpieczeń dla identyfikatora URI nazewnictwa wyliczenia. |
|FileContent |ciąg, domyślną jest "Admin" |Dynamiczny| Konfiguracja zabezpieczeń dla obrazu magazynu klienta transferu plików (zewnętrznych do klastra). |
|FileDownload |ciąg, domyślną jest "Admin" |Dynamiczny| Konfiguracja zabezpieczeń rozpoczęcia pobierania pliku obrazu klienta magazynu, które zostały (zewnętrznych do klastra). |
|FinishInfrastructureTask |ciąg, domyślną jest "Admin" |Dynamiczny| Konfiguracja zabezpieczeń kończenia zadań infrastruktury. |
|GetChaosReport | ciąg, domyślna to "Admin\|\|użytkownika" |Dynamiczny| Pobiera stan Chaos w danym okresie. |
|GetClusterConfiguration | ciąg, domyślna to "Admin\|\|użytkownika" | Dynamiczny|Wywołuje GetClusterConfiguration na partycji. |
|GetClusterConfigurationUpgradeStatus | ciąg, domyślna to "Admin\|\|użytkownika" |Dynamiczny| Wywołuje GetClusterConfigurationUpgradeStatus na partycji. |
|GetFabricUpgradeStatus |ciąg, domyślna to "Admin\|\|użytkownika" |Dynamiczny| Konfiguracja zabezpieczeń sondowania stan uaktualnienia klastra. |
|GetNodeDeactivationStatus |ciąg, domyślną jest "Admin" |Dynamiczny| Sprawdzanie stanu dezaktywacji konfiguracji zabezpieczeń. |
|GetNodeTransitionProgress | ciąg, domyślna to "Admin\|\|użytkownika" |Dynamiczny| Pobieranie postęp w poleceniu Przejście węzła konfiguracji zabezpieczeń. |
|GetPartitionDataLossProgress | ciąg, domyślna to "Admin\|\|użytkownika" | Dynamiczny|Pobiera postępu dla wywołania interfejsu api invoke utraty danych. |
|GetPartitionQuorumLossProgress | ciąg, domyślna to "Admin\|\|użytkownika" |Dynamiczny| Pobiera postępu dla wywołania interfejsu api utraty kworum invoke. |
|GetPartitionRestartProgress | ciąg, domyślna to "Admin\|\|użytkownika" |Dynamiczny| Pobiera postępu wywołania interfejsu api ponownego uruchamiania partycji. |
|GetServiceDescription |ciąg, domyślna to "Admin\|\|użytkownika" |Dynamiczny| Konfiguracja zabezpieczeń dla powiadomień dotyczących usługi long sondowania i odczytywania opisy usług. |
|GetStagingLocation |ciąg, domyślną jest "Admin" |Dynamiczny| Konfiguracja zabezpieczeń dla obrazu magazynu klienta tymczasowej lokalizacji pobierania. |
|GetStoreLocation |ciąg, domyślną jest "Admin" |Dynamiczny| Konfiguracja zabezpieczeń dla obrazu przechowywać Pobieranie lokalizacji magazynu klienta. |
|GetUpgradeOrchestrationServiceState|ciąg, domyślny jest L "Admin"| Dynamiczny|Wywołuje GetUpgradeOrchestrationServiceState na partycji |
|GetUpgradesPendingApproval |ciąg, domyślną jest "Admin" |Dynamiczny| Wywołuje GetUpgradesPendingApproval na partycji. |
|GetUpgradeStatus |ciąg, domyślna to "Admin\|\|użytkownika" |Dynamiczny| Konfiguracja zabezpieczeń sondowania stanu uaktualniania aplikacji. |
|InternalList |ciąg, domyślną jest "Admin" | Dynamiczny|Konfiguracja zabezpieczeń dla obrazu przechowywać operacja listy plików klienta (wewnętrzny). |
|InvokeContainerApi|wstring, domyślny jest L "Admin"|Dynamiczny|Wywołanie interfejsu API kontenera |
|InvokeInfrastructureCommand |ciąg, domyślną jest "Admin" |Dynamiczny| Konfiguracja zabezpieczeń infrastruktury zadań zarządzania poleceń. |
|InvokeInfrastructureQuery |ciąg, domyślna to "Admin\|\|użytkownika" | Dynamiczny|Badania infrastruktury zadań konfiguracji zabezpieczeń. |
|List |ciąg, domyślna to "Admin\|\|użytkownika" | Dynamiczny|Konfiguracja zabezpieczeń dla obrazu przechowywać operacja listy pliku klienta. |
|MoveNextFabricUpgradeDomain |ciąg, domyślną jest "Admin" |Dynamiczny| Wznawianie uaktualniania klastra z jawnie domeny uaktualnienia konfiguracji zabezpieczeń. |
|MoveNextUpgradeDomain |ciąg, domyślną jest "Admin" |Dynamiczny| Wznawianie uaktualniania aplikacji z jawnym domeny uaktualnienia konfiguracji zabezpieczeń. |
|MoveReplicaControl |ciąg, domyślną jest "Admin" | Dynamiczny|Przenieś repliki. |
|NameExists |ciąg, domyślna to "Admin\|\|użytkownika" | Dynamiczny|Sprawdza, czy konfiguracja zabezpieczeń dla identyfikatora URI nazewnictwa istnienia. |
|NodeControl |ciąg, domyślną jest "Admin" |Dynamiczny| Konfiguracja zabezpieczeń dla uruchamiania; zatrzymywanie; i ponowne uruchamianie węzłów. |
|NodeStateRemoved |ciąg, domyślną jest "Admin" |Dynamiczny| Konfiguracja zabezpieczeń do raportowania stanu węzła usunięte. |
|Ping |ciąg, domyślna to "Admin\|\|użytkownika" |Dynamiczny| Konfiguracja zabezpieczeń klienta polecenia ping. |
|PredeployPackageToNode |ciąg, domyślną jest "Admin" |Dynamiczny| Przedwdrożeniowe interfejsu api. |
|PrefixResolveService |ciąg, domyślna to "Admin\|\|użytkownika" |Dynamiczny| Konfiguracja zabezpieczeń do rozpoznawania prefiksu usługi opartej na zgodne. |
|PropertyReadBatch |ciąg, domyślna to "Admin\|\|użytkownika" |Dynamiczny| Operacje odczytu dla konfiguracji zabezpieczeń dla nazw właściwości. |
|PropertyWriteBatch |ciąg, domyślną jest "Admin" |Dynamiczny|Operacje zapisu konfiguracji zabezpieczeń dla nazw właściwości. |
|ProvisionApplicationType |ciąg, domyślną jest "Admin" |Dynamiczny| Konfiguracja zabezpieczeń aprowizacji typu aplikacji. |
|ProvisionFabric |ciąg, domyślną jest "Admin" |Dynamiczny| Konfiguracja zabezpieczeń MSI i/lub klastra manifestu inicjowania obsługi administracyjnej. |
|Zapytanie |ciąg, domyślna to "Admin\|\|użytkownika" |Dynamiczny| Konfiguracja zabezpieczeń dla zapytań. |
|RecoverPartition |ciąg, domyślną jest "Admin" | Dynamiczny|Konfiguracja zabezpieczeń do przywrócenia partycji. |
|RecoverPartitions |ciąg, domyślną jest "Admin" | Dynamiczny|Konfiguracja zabezpieczeń do przywrócenia partycji. |
|RecoverServicePartitions |ciąg, domyślną jest "Admin" |Dynamiczny| Konfiguracja zabezpieczeń do przywrócenia partycji usługi. |
|RecoverSystemPartitions |ciąg, domyślną jest "Admin" |Dynamiczny| Konfiguracja zabezpieczeń do przywrócenia partycji usług systemowych. |
|RemoveNodeDeactivations |ciąg, domyślną jest "Admin" |Dynamiczny| Konfiguracja zabezpieczeń podczas przywracania dezaktywacji na wielu węzłach. |
|ReportFabricUpgradeHealth |ciąg, domyślną jest "Admin" |Dynamiczny| Wznawianie uaktualniania klastra z Bieżący postęp uaktualniania konfiguracji zabezpieczeń. |
|ReportFault |ciąg, domyślną jest "Admin" |Dynamiczny| Raportowanie błędów konfiguracji zabezpieczeń. |
|ReportHealth |ciąg, domyślną jest "Admin" |Dynamiczny| Konfiguracja zabezpieczeń dla raportowania kondycji. |
|ReportUpgradeHealth |ciąg, domyślną jest "Admin" |Dynamiczny| Wznawianie uaktualniania aplikacji z Bieżący postęp uaktualniania konfiguracji zabezpieczeń. |
|Funkcji resetpartitionload modułu |ciąg, domyślna to "Admin\|\|użytkownika" |Dynamiczny| Konfiguracja zabezpieczeń resetowania obciążenia failoverUnit. |
|ResolveNameOwner |ciąg, domyślna to "Admin\|\|użytkownika" | Dynamiczny|Konfiguracja zabezpieczeń rozpoznawania identyfikatora URI nazewnictwa właściciela. |
|ResolvePartition |ciąg, domyślna to "Admin\|\|użytkownika" | Dynamiczny|Konfiguracja zabezpieczeń w celu rozwiązania usług systemowych. |
|ResolveService |ciąg, domyślna to "Admin\|\|użytkownika" |Dynamiczny| Konfiguracja zabezpieczeń do rozpoznania usług zgodne. |
|ResolveSystemService|ciąg, domyślny jest L "Admin\|\|użytkownika"|Dynamiczny| Konfiguracja zabezpieczeń w celu rozwiązania usług systemowych |
|RollbackApplicationUpgrade |ciąg, domyślną jest "Admin" |Dynamiczny| Wycofywanie uaktualnienia aplikacji konfiguracji zabezpieczeń. |
|RollbackFabricUpgrade |ciąg, domyślną jest "Admin" |Dynamiczny| Wycofywanie uaktualnienia klastra konfiguracji zabezpieczeń. |
|ServiceNotifications |ciąg, domyślna to "Admin\|\|użytkownika" |Dynamiczny| Konfiguracja zabezpieczeń oparty na zdarzeniach usługi powiadomień. |
|SetUpgradeOrchestrationServiceState|ciąg, domyślny jest L "Admin"| Dynamiczny|Wywołuje SetUpgradeOrchestrationServiceState na partycji |
|StartApprovedUpgrades |ciąg, domyślną jest "Admin" |Dynamiczny| Wywołuje StartApprovedUpgrades na partycji. |
|StartChaos |ciąg, domyślną jest "Admin" |Dynamiczny| Uruchamia Chaos — Jeśli nie jest już uruchomiona. |
|StartClusterConfigurationUpgrade |ciąg, domyślną jest "Admin" |Dynamiczny| Wywołuje StartClusterConfigurationUpgrade na partycji. |
|StartInfrastructureTask |ciąg, domyślną jest "Admin" | Dynamiczny|Konfiguracja zabezpieczeń dla uruchamiania zadań infrastruktury. |
|StartNodeTransition |ciąg, domyślną jest "Admin" |Dynamiczny| Konfiguracja zabezpieczeń dla uruchamiania Przejście węzła. |
|StartPartitionDataLoss |ciąg, domyślną jest "Admin" |Dynamiczny| Powoduje utratę danych na partycji. |
|StartPartitionQuorumLoss |ciąg, domyślną jest "Admin" |Dynamiczny| Powoduje utratę kworum na partycji. |
|StartPartitionRestart |ciąg, domyślną jest "Admin" |Dynamiczny| Jednocześnie ponownego uruchomienia niektórych lub wszystkich replik partycji. |
|StopChaos |ciąg, domyślną jest "Admin" |Dynamiczny| Zatrzymuje Chaos — Jeśli została ona uruchomiona. |
|ToggleVerboseServicePlacementHealthReporting | ciąg, domyślna to "Admin\|\|użytkownika" |Dynamiczny| Konfiguracja zabezpieczeń przełączanie pełne ServicePlacement HealthReporting. |
|UnprovisionApplicationType |ciąg, domyślną jest "Admin" |Dynamiczny| Konfiguracja zabezpieczeń do wstrzymania obsługi administracyjnej typu aplikacji. |
|UnprovisionFabric |ciąg, domyślną jest "Admin" |Dynamiczny| Konfiguracja zabezpieczeń MSI i/lub klastra manifestu wstrzymania obsługi administracyjnej. |
|UnreliableTransportControl |ciąg, domyślną jest "Admin" |Dynamiczny| Zawodne transportu do dodawania i usuwania zachowania. |
|UpdateService |ciąg, domyślną jest "Admin" |Dynamiczny|Konfiguracja zabezpieczeń dla usług aktualizacji. |
|UpgradeApplication |ciąg, domyślną jest "Admin" |Dynamiczny| Konfiguracja zabezpieczeń dla uruchamiania lub przerywania uaktualnieniami aplikacji. |
|UpgradeComposeDeployment|ciąg, domyślny jest L "Admin"| Dynamiczny|Uaktualnia tworzenia wdrożenia |
|UpgradeFabric |ciąg, domyślną jest "Admin" |Dynamiczny| Konfiguracja zabezpieczeń dla uruchamiania uaktualnienia klastra. |
|Upload |ciąg, domyślną jest "Admin" | Dynamiczny|Konfiguracja zabezpieczeń dla obrazu przechowywać operacja przekazywania klienta. |

## <a name="securityclientcertificateissuerstores"></a>Zabezpieczenia/ClientCertificateIssuerStores
| **Parametr** | **Dozwolone wartości** | **Zasady uaktualniania** | **Wskazówki lub krótki opis** |
| --- | --- | --- | --- |
|PropertyGroup|IssuerStoreKeyValueMap, domyślna wartość to Brak |Dynamiczny|X509 przechowuje wystawcy certyfikatu dla certyfikatów klienta; Nazwa = clientIssuerCN; Wartość = rozdzielana przecinkami lista magazynów |

## <a name="securityclientx509names"></a>Zabezpieczenia/ClientX509Names
| **Parametr** | **Dozwolone wartości** | **Zasady uaktualniania** | **Wskazówki lub krótki opis** |
| --- | --- | --- | --- |
|PropertyGroup|X509NameMap, domyślna wartość to Brak|Dynamiczny| |

## <a name="securityclustercertificateissuerstores"></a>Zabezpieczenia/ClusterCertificateIssuerStores
| **Parametr** | **Dozwolone wartości** | **Zasady uaktualniania** | **Wskazówki lub krótki opis** |
| --- | --- | --- | --- |
|PropertyGroup|IssuerStoreKeyValueMap, domyślna wartość to Brak |Dynamiczny|X509 magazyny certyfikatów wystawców certyfikatów klastra; Nazwa = clusterIssuerCN; Wartość = rozdzielana przecinkami lista magazynów |

## <a name="securityclusterx509names"></a>Zabezpieczenia/ClusterX509Names
| **Parametr** | **Dozwolone wartości** | **Zasady uaktualniania** | **Wskazówki lub krótki opis** |
| --- | --- | --- | --- |
|PropertyGroup|X509NameMap, domyślna wartość to Brak|Dynamiczny| |

## <a name="securityservercertificateissuerstores"></a>Zabezpieczenia/ServerCertificateIssuerStores
| **Parametr** | **Dozwolone wartości** | **Zasady uaktualniania** | **Wskazówki lub krótki opis** |
| --- | --- | --- | --- |
|PropertyGroup|IssuerStoreKeyValueMap, domyślna wartość to Brak |Dynamiczny|X509 magazyny certyfikatów wystawców certyfikatów serwera; Nazwa = serverIssuerCN; Wartość = rozdzielana przecinkami lista magazynów |

## <a name="securityserverx509names"></a>Zabezpieczenia/ServerX509Names
| **Parametr** | **Dozwolone wartości** | **Zasady uaktualniania** | **Wskazówki lub krótki opis** |
| --- | --- | --- | --- |
|PropertyGroup|X509NameMap, domyślna wartość to Brak|Dynamiczny| |

## <a name="setup"></a>Konfigurowanie
| **Parametr** | **Dozwolone wartości** | **Zasady uaktualniania** | **Wskazówki lub krótki opis** |
| --- | --- | --- | --- |
|ContainerNetworkName|ciąg, domyślny jest L""| Statyczny |Nazwa sieciowa używana podczas konfigurowania sieci kontenera.|
|ContainerNetworkSetup|wartość logiczna, domyślna to FALSE| Statyczny |Określa, czy sieć kontenera.|
|FabricDataRoot |Ciąg | Niedozwolone |Katalog główny danych sieci szkieletowej usług. Domyślny dla platformy Azure jest d:\svcfab |
|FabricLogRoot |Ciąg | Niedozwolone |Katalog główny usługi sieć szkieletowa dziennika. Jest to rozmieszczenia rz dzienników i danych śledzenia. |
|NodesToBeRemoved|ciąg, domyślna to ""| Dynamiczny |Węzły, które należy usunąć w ramach uaktualnienia konfiguracji. (Tylko w przypadku autonomicznych wdrożeniach)|
|ServiceRunAsAccountName |Ciąg | Niedozwolone |Nazwa konta przy użyciu których należy uruchomić usługi hosta sieci szkieletowej. |
|SkipFirewallConfiguration |Wartość logiczna, wartość domyślna to false | Niedozwolone |Określa, czy ustawienia zapory musi być ustawiona przez system, lub nie. Dotyczy to tylko, jeśli używasz zapory systemu windows. Jeśli korzystasz z zapory innej firmy, następnie należy otworzyć porty dla systemu i aplikacji do użycia |

## <a name="tokenvalidationservice"></a>TokenValidationService
| **Parametr** | **Dozwolone wartości** | **Zasady uaktualniania** | **Wskazówki lub krótki opis** |
| --- | --- | --- | --- |
|Dostawcy |ciąg, domyślną jest "DSTS" |Statyczny|Rozdzielana przecinkami lista dostawców weryfikacji tokenu, aby umożliwić (prawidłowych dostawców usług są: DSTS; AAD). Obecnie tylko jednego dostawcę można włączyć w dowolnej chwili. |

## <a name="traceetw"></a>Trace/Etw
| **Parametr** | **Dozwolone wartości** | **Zasady uaktualniania** | **Wskazówki lub krótki opis** |
| --- | --- | --- | --- |
|Poziom |Int, domyślna to 4 | Dynamiczny |Poziom śledzenia zdarzeń systemu Windows może przyjmować wartości 1, 2, 3, 4. Obsługiwany poziom śledzenia musi przechowywać 4 |

## <a name="transactionalreplicator"></a>TransactionalReplicator
| **Parametr** | **Dozwolone wartości** | **Zasady uaktualniania** | **Wskazówki lub krótki opis** |
| --- | --- | --- | --- |
|BatchAcknowledgementInterval | Czas w sekundach, domyślnie jest 0,015 | Statyczny | Określ zakres czasu w sekundach. Określa czas oczekiwania po otrzymaniu operacji przed odsyła potwierdzenia replikatora. Inne operacje otrzymanych w tym czasie będzie miał ich potwierdzeń wysyłane z powrotem w pojedynczym komunikacie -> ograniczenia ruchu sieciowego, ale potencjalnie zmniejszenie przepływności replikatora. |
|MaxCopyQueueSize |Uint — wartość domyślna to 16384 | Statyczny |Jest to maksymalna wartość Określa początkowy rozmiar kolejki, co umożliwia utrzymywanie operacji replikacji. Należy pamiętać, że musi być potęgą liczby 2. Jeśli w czasie wykonywania kolejki rozwoju na tę operację rozmiar zostanie zdławionych między replikatorów podstawowego i pomocniczego. |
|MaxPrimaryReplicationQueueMemorySize |Uint — wartość domyślna to 0 | Statyczny |Jest to maksymalna wartość kolejki podstawowej replikacji w bajtach. |
|MaxPrimaryReplicationQueueSize |Uint — wartość domyślna to 8192 | Statyczny |Jest to maksymalna liczba operacji, które może znajdować się w kolejce podstawowej replikacji. Należy pamiętać, że musi być potęgą liczby 2. |
|MaxReplicationMessageSize |uint, domyślny jest 52428800 | Statyczny | Maksymalny rozmiar komunikatu operacji replikacji. Domyślną jest 50MB. |
|MaxSecondaryReplicationQueueMemorySize |Uint — wartość domyślna to 0 | Statyczny |Jest to maksymalna wartość kolejki dodatkowej replikacji w bajtach. |
|MaxSecondaryReplicationQueueSize |Uint — wartość domyślna to 16384 | Statyczny |Jest to maksymalna liczba operacji, które może znajdować się w kolejce replikacji dodatkowej. Należy pamiętać, że musi być potęgą liczby 2. |
|ReplicatorAddress |ciąg, domyślną jest "localhost:0" | Statyczny | Punkt końcowy w postaci ciągu - IP:Port, który jest używany przez Replikator sieci szkieletowej systemu Windows do nawiązywania połączeń z innych replik w celu wysyłania i odbierania operacji. |

## <a name="transport"></a>Transport
| **Parametr** | **Dozwolone wartości** |**Zasady uaktualniania** |**Wskazówki lub krótki opis** |
| --- | --- | --- | --- |
|ConnectionOpenTimeout|Zakres czasu, domyślnie jest Common::TimeSpan::FromSeconds(60)|Statyczny|Określ zakres czasu w sekundach. Upłynął limit czasu połączenia instalacji po stronie przychodzących i odbierać (w tym negocjacji zabezpieczeń w trybie zabezpieczonym) |
|ResolveOption|ciąg, jest domyślną L "nieokreślony"|Statyczny|Określa, jak nazwa FQDN został rozwiązany.  Prawidłowe wartości to "nieokreślonych/ipv4 i ipv6". |

## <a name="upgradeorchestrationservice"></a>UpgradeOrchestrationService
| **Parametr** | **Dozwolone wartości** | **Zasady uaktualniania** | **Wskazówki lub krótki opis** |
| --- | --- | --- | --- |
|AutoupgradeEnabled | Wartość logiczna, domyślna to true |Statyczny| Automatyczne sondowania i uaktualniania akcji na podstawie pliku stanu docelowego. |
|MinReplicaSetSize |Int, domyślna to 0 |Statyczny |MinReplicaSetSize dla UpgradeOrchestrationService.
|PlacementConstraints | ciąg, domyślna to "" |Statyczny| PlacementConstraints dla UpgradeOrchestrationService. |
|QuorumLossWaitDuration | Czas w sekundach, domyślnie jest MaxValue |Statyczny| Określ zakres czasu w sekundach. QuorumLossWaitDuration dla UpgradeOrchestrationService. |
|ReplicaRestartWaitDuration | Czas w sekundach, domyślna to 60 minut|Statyczny| Określ zakres czasu w sekundach. ReplicaRestartWaitDuration dla UpgradeOrchestrationService. |
|StandByReplicaKeepDuration | Czas w sekundach, domyślna to 60*24*7 minut |Statyczny| Określ zakres czasu w sekundach. StandByReplicaKeepDuration dla UpgradeOrchestrationService. |
|Wartość TargetReplicaSetSize |Int, domyślna to 0 |Statyczny |TargetReplicaSetSize dla UpgradeOrchestrationService. |
|UpgradeApprovalRequired | Wartość logiczna, wartość domyślna to false | Statyczny|Ustawienie, aby wymagają zatwierdzenia przez administratora przed kontynuowaniem uaktualniania kodu. |

## <a name="upgradeservice"></a>UpgradeService
| **Parametr** | **Dozwolone wartości** | **Zasady uaktualniania** | **Wskazówki lub krótki opis** |
| --- | --- | --- | --- |
|BaseUrl | ciąg, domyślna to "" |Statyczny|BaseUrl dla UpgradeService. |
|ClusterId | ciąg, domyślna to "" |Statyczny|ClusterId dla UpgradeService. |
|CoordinatorType | ciąg, domyślną jest "WUTest"|Niedozwolone|CoordinatorType dla UpgradeService. |
|MinReplicaSetSize | Int, domyślna to 2 |Niedozwolone| MinReplicaSetSize dla UpgradeService. |
|OnlyBaseUpgrade | Wartość logiczna, wartość domyślna to false |Dynamiczny|OnlyBaseUpgrade dla UpgradeService. |
|PlacementConstraints |ciąg, domyślna to "" |Niedozwolone|PlacementConstraints uaktualnienia usługi. |
|Wartość TargetReplicaSetSize | Int, domyślna to 3 |Niedozwolone| TargetReplicaSetSize dla UpgradeService. |
|TestCabFolder | ciąg, domyślna to "" |Statyczny| TestCabFolder dla UpgradeService. |
|X509FindType | ciąg, domyślna to ""|Dynamiczny| X509FindType dla UpgradeService. |
|X509FindValue | ciąg, domyślna to "" |Dynamiczny| X509FindValue dla UpgradeService. |
|X509SecondaryFindValue | ciąg, domyślna to "" |Dynamiczny| X509SecondaryFindValue dla UpgradeService. |
|X509StoreLocation | ciąg, domyślna to "" |Dynamiczny| X509StoreLocation dla UpgradeService. |
|X509StoreName | jest domyślny ciąg "Moje"|Dynamiczny|X509StoreName dla UpgradeService. |

## <a name="next-steps"></a>Kolejne kroki
Przeczytaj następujące artykuły, aby uzyskać więcej informacji na temat zarządzania klastrem:

[Dodawanie, przerzucane, Usuń certyfikaty z klastrem Azure ](service-fabric-cluster-security-update-certs-azure.md) 

