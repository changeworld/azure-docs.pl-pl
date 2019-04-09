---
title: Zmień ustawienia klastra usługi Azure Service Fabric | Dokumentacja firmy Microsoft
description: W tym artykule opisano ustawienia sieci szkieletowej i zasad uaktualniania sieci szkieletowej, które można dostosować.
services: service-fabric
documentationcenter: .net
author: aljo-microsoft
manager: chackdan
editor: ''
ms.assetid: 7ced36bf-bd3f-474f-a03a-6ebdbc9677e2
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: reference
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 12/11/2018
ms.author: aljo
ms.openlocfilehash: 7252af42ac515f9177b8988e2995e6ce77f4e12f
ms.sourcegitcommit: b4ad15a9ffcfd07351836ffedf9692a3b5d0ac86
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/05/2019
ms.locfileid: "59058870"
---
# <a name="customize-service-fabric-cluster-settings"></a>Dostosowywanie ustawień klastra usługi Service Fabric
W tym artykule opisano różne ustawienia sieci szkieletowej klastra usługi Service Fabric, którą można dostosować. W przypadku klastrów hostowanych na platformie Azure, można dostosować ustawienia za pośrednictwem [witryny Azure portal](https://portal.azure.com) lub przy użyciu szablonu usługi Azure Resource Manager. Aby uzyskać więcej informacji, zobacz [Uaktualnij konfigurację klastra usługi Azure](service-fabric-cluster-config-upgrade-azure.md). W przypadku klastrów autonomicznych dostosować ustawienia, aktualizując *ClusterConfig.json* plików i przeprowadzania konfiguracji uaktualnienia w klastrze. Aby uzyskać więcej informacji, zobacz [uaktualnić konfiguracji klastra autonomicznego](service-fabric-cluster-config-upgrade-windows-server.md).

Istnieją trzy różne zasady uaktualniania:

- **Dynamiczne** — zmiany w konfiguracji dynamicznej nie powodują ponowne uruchomienia procesu usługi Service Fabric procesy lub procesy hosta usługi. 
- **Statyczne** — zmiany w konfiguracji statycznej spowoduje, że węzeł usługi Service Fabric, aby ponowne uruchomienie w celu korzystania z zmiany. Usługi w węzłach zostanie ponownie uruchomiona.
- **NotAllowed** — nie można modyfikować tych ustawień. Zmiana tych ustawień wymaga zniszczone klastra i utworzyć nowy klaster. 

Poniżej przedstawiono listę sieci szkieletowej ustawienia, które można dostosować, uporządkowane według sekcji.

## <a name="applicationgatewayhttp"></a>ApplicationGateway/Http

| **Parametr** | **Dozwolone wartości** | **Zasady uaktualniania** | **Wskazówki dotyczące lub krótki opis** |
| --- | --- | --- | --- |
|ApplicationCertificateValidationPolicy|ciąg, domyślnie jest "None"|Statyczny| Nie sprawdza certyfikat serwera; Powodzenie żądanie. Można znaleźć konfiguracji ServiceCertificateThumbprints rozdzielaną przecinkami listę odciski palca certyfikatów zdalnego, ufające zwrotnego serwera proxy. Można znaleźć konfiguracji ServiceCommonNameAndIssuer dla podmiotu nazwy i Wystawca odcisk palca certyfikatów zdalnego, ufające zwrotnego serwera proxy. Aby dowiedzieć się więcej, zobacz [odwrotnego serwera proxy bezpiecznego połączenia](service-fabric-reverseproxy-configure-secure-communication.md#secure-connection-establishment-between-the-reverse-proxy-and-services). |
|BodyChunkSize |Uint, wartością domyślną jest 16384 |Dynamiczny| Zapewnia rozmiar fragmentu w bajtach, używany do odczytu treści. |
|CrlCheckingFlag|uint, domyślny jest 0x40000000 |Dynamiczny| Flagi dla weryfikacji łańcucha certyfikatu aplikacji/usługi; np. sprawdzania listy CRL 0x10000000 CERT_CHAIN_REVOCATION_CHECK_END_CERT 0x20000000 CERT_CHAIN_REVOCATION_CHECK_CHAIN 0x40000000 CERT_CHAIN_REVOCATION_CHECK_CHAIN_EXCLUDE_ROOT 0x80000000 CERT_CHAIN_REVOCATION_CHECK_CACHE_ONLY ustawienie na wartość 0 Wyłącza listy CRL sprawdzanie, czy pełną listę obsługiwanych wartości jest udokumentowany przez Flagidw CertGetCertificateChain: https://msdn.microsoft.com/library/windows/desktop/aa376078(v=vs.85).aspx  |
|DefaultHttpRequestTimeout |Czas w sekundach. Wartość domyślna to 120 |Dynamiczny|Określ przedział czasu w sekundach.  Udostępnia domyślny limit czasu żądania dla żądań http przetwarzanych w bramie aplikacji protokołu http. |
|ForwardClientCertificate|wartość logiczna, domyślna to FALSE|Dynamiczny|Gdy ustawiona na wartość false, odwrotnego serwera proxy nie zażąda certyfikatu klienta. Ustawiona na wartość true, zwrotny serwer proxy będzie żądać certyfikatu klienta w trakcie uzgadniania protokołu SSL i przekazywać je zakodowane w formacie base64 ciąg formatu PEM z usługą w nagłówku o nazwie Certificate.The-X-klienta usługi może zakończyć się niepowodzeniem żądania z kodem stanu odpowiednie Po sprawdzeniu danych certyfikatu. Jeśli to PRAWDA i klient nie przedstawić certyfikat, zwrotny serwer proxy przesyłania dalej pusty nagłówek i zezwala na obsłużyć przypadek. Zwrotny serwer proxy będzie działać jako przezroczysty warstwy. Aby dowiedzieć się więcej, zobacz [skonfigurować uwierzytelnianie certyfikatu klienta](service-fabric-reverseproxy-configure-secure-communication.md#setting-up-client-certificate-authentication-through-the-reverse-proxy). |
|GatewayAuthCredentialType |ciąg, domyślnie jest "None" |Statyczny| Wskazuje typ poświadczeń zabezpieczeń w http app gateway punktu końcowego prawidłowe wartości to "Brak / X 509. |
|GatewayX509CertificateFindType |ciąg, domyślną jest "FindByThumbprint" |Dynamiczny| Wskazuje, jak wyszukiwać certyfikatu w magazynie określonym przez wartość GatewayX509CertificateStoreName obsługiwane: FindByThumbprint; FindBySubjectName. |
|GatewayX509CertificateFindValue | ciąg, domyślna to "" |Dynamiczny| Wartość filtru wyszukiwania używana do lokalizowania certyfikatu bramy aplikacji protokołu http. Ten certyfikat jest skonfigurowany w punkcie końcowym protokołu https i może również służyć do zweryfikowania tożsamości aplikacji w razie potrzeby przez usługi. FindValue jest najpierw wyszukiwane; a jeśli nie istnieje; Wyszukiwane FindValueSecondary. |
|GatewayX509CertificateFindValueSecondary | ciąg, domyślna to "" |Dynamiczny|Wartość filtru wyszukiwania używana do lokalizowania certyfikatu bramy aplikacji protokołu http. Ten certyfikat jest skonfigurowany w punkcie końcowym protokołu https i może również służyć do zweryfikowania tożsamości aplikacji w razie potrzeby przez usługi. FindValue jest najpierw wyszukiwane; a jeśli nie istnieje; Wyszukiwane FindValueSecondary.|
|GatewayX509CertificateStoreName |ciąg, domyślnie jest "Mój" |Dynamiczny| Nazwa magazynu certyfikatu X.509, który zawiera certyfikat dla bramy aplikacji protokołu http. |
|HttpRequestConnectTimeout|Przedział czasu, wartością domyślną jest Common::TimeSpan::FromSeconds(5)|Dynamiczny|Określ przedział czasu w sekundach.  Zapewnia limit czasu połączenia dla żądania http wysyłane z bramy aplikacji protokołu http.  |
|IgnoreCrlOfflineError|wartość logiczna, domyślny ma wartość TRUE|Dynamiczny|Czy chcesz zignorować listy CRL błąd w trybie offline dla aplikacji/usługi certyfikatu weryfikacji. |
|IsEnabled |Wartość logiczna, wartość domyślna to false |Statyczny| Włącza/wyłącza HttpApplicationGateway. HttpApplicationGateway jest domyślnie wyłączona, a ta konfiguracja musi być ustawiona, aby go włączyć. |
|NumberOfParallelOperations | Uint, wartość domyślna to 5000 |Statyczny|Liczba odczytów w stosunku do wysłania do kolejki serwera http. Kontroluje to liczbę żądań, które mogą zostać wykonane przez HttpGateway. |
|RemoveServiceResponseHeaders|ciąg, domyślną jest "Data; Serwer"|Statyczny|Średnikami / rozdzielana przecinkami lista nagłówków odpowiedzi, które zostaną usunięte z odpowiedzi usługi; przed przekazaniem go do klienta. Jeśli jest ono ustawione na pusty ciąg; Przekaż wszystkie nagłówki, które są zwracane przez usługę jako-to. i.e Nie zastępuj daty i serwera |
|ResolveServiceBackoffInterval |Czas w sekundach, domyślna to 5 |Dynamiczny|Określ przedział czasu w sekundach.  Udostępnia rozwiązania interwał wycofywania domyślne przed ponowieniem próby wykonania nieudanej operacji usługi. |
|SecureOnlyMode|wartość logiczna, domyślna to FALSE|Dynamiczny| SecureOnlyMode: true: Zwrotny serwer Proxy tylko przesyła do usług, które publikują bezpieczne punkty końcowe. false: Zwrotny serwer Proxy może przekazywać żądania do bezpiecznego/niezabezpieczone punktów końcowych. Aby dowiedzieć się więcej, zobacz [odwrotnego logikę wyboru punktu końcowego serwera proxy](service-fabric-reverseproxy-configure-secure-communication.md#endpoint-selection-logic-when-services-expose-secure-as-well-as-unsecured-endpoints).  |
|ServiceCertificateThumbprints|ciąg, domyślna to ""|Dynamiczny|Rozdzielana przecinkami lista odciski palca certyfikatów zdalnego, ufające zwrotnego serwera proxy. Aby dowiedzieć się więcej, zobacz [odwrotnego serwera proxy bezpiecznego połączenia](service-fabric-reverseproxy-configure-secure-communication.md#secure-connection-establishment-between-the-reverse-proxy-and-services). |

## <a name="applicationgatewayhttpservicecommonnameandissuer"></a>Brama ApplicationGateway/Http/ServiceCommonNameAndIssuer

| **Parametr** | **Dozwolone wartości** | **Zasady uaktualniania** | **Wskazówki dotyczące lub krótki opis** |
| --- | --- | --- | --- |
|PropertyGroup|X509NameMap, domyślna wartość to Brak|Dynamiczny| Temat nazwy i Wystawca odcisk palca certyfikatów zdalnego, ufające zwrotny serwer proxy. Aby dowiedzieć się więcej, zobacz [odwrotnego serwera proxy bezpiecznego połączenia](service-fabric-reverseproxy-configure-secure-communication.md#secure-connection-establishment-between-the-reverse-proxy-and-services). |

## <a name="backuprestoreservice"></a>BackupRestoreService

| **Parametr** | **Dozwolone wartości** | **Zasady uaktualniania** | **Wskazówki dotyczące lub krótki opis** |
| --- | --- | --- | --- |
|MinReplicaSetSize|int, domyślna to 0|Statyczny|MinReplicaSetSize dla BackupRestoreService |
|PlacementConstraints|ciąg, domyślna to ""|Statyczny|  PlacementConstraints BackupRestore usługi |
|SecretEncryptionCertThumbprint|ciąg, domyślna to ""|Dynamiczny|Odcisk palca certyfikatu szyfrowania klucza tajnego X509 |
|SecretEncryptionCertX509StoreName|ciąg, domyślnie jest "Mój"|   Dynamiczny|    Oznacza to, certyfikat do użycia podczas szyfrowania i odszyfrowywania poświadczenia magazynu certyfikatów nazwa X.509, który jest używany do szyfrowania, odszyfrowywania poświadczeń magazynu używanego przez usługę Backup Restore |
|TargetReplicaSetSize|int, domyślna to 0|Statyczny| TargetReplicaSetSize dla BackupRestoreService |

## <a name="clustermanager"></a>ClusterManager

| **Parametr** | **Dozwolone wartości** | **Zasady uaktualniania** | **Wskazówki dotyczące lub krótki opis** |
| --- | --- | --- | --- |
|EnableDefaultServicesUpgrade | Wartość logiczna, wartość domyślna to false |Dynamiczny|Włącz uaktualnianie usługi domyślne podczas uaktualniania aplikacji. Opisów usługi domyślnej zostaną zastąpione po uaktualnieniu. |
|FabricUpgradeHealthCheckInterval |Czas w sekundach, domyślna to 60 |Dynamiczny|Częstotliwość kondycja sprawdzania podczas uaktualniania sieci szkieletowej przy użyciu monitorowanego |
|FabricUpgradeStatusPollInterval |Czas w sekundach, domyślna to 60 |Dynamiczny|Częstotliwość sondowania stanu uaktualnienia sieci szkieletowej. Ta wartość określa częstotliwość aktualizacji dla każde wywołanie GetFabricUpgradeProgress |
|ImageBuilderTimeoutBuffer |Czas w sekundach, domyślna to 3 |Dynamiczny|Określ przedział czasu w sekundach. Ilość czasu na błędy określonym limicie czasu kreatora obrazów do zwrócenia do klienta. Jeśli bufor jest zbyt mały; Następnie klient upłynie limit czasu, zanim serwer i pobiera błąd ogólny limit czasu. |
|InfrastructureTaskHealthCheckRetryTimeout | Czas w sekundach, domyślna to 60 |Dynamiczny|Określ przedział czasu w sekundach. Ilość czasu, które możesz wydać ponawianie próby nie kondycji sprawdza podczas przetwarzania końcowego zadania infrastruktury. Monitorowanie sprawdzenie przekazanych kondycji spowoduje zresetowanie ten czasomierz. |
|InfrastructureTaskHealthCheckStableDuration | Czas w sekundach, domyślnie wynosi 0|Dynamiczny| Określ przedział czasu w sekundach. Ilość czasu, aby obserwować kolejne przekazany kontrole kondycji przed przetwarzania końcowego zadania infrastruktury zakończy się pomyślnie. Monitorowanie kondycji nie powiodło się sprawdzenie spowoduje zresetowanie ten czasomierz. |
|InfrastructureTaskHealthCheckWaitDuration |Czas w sekundach, domyślnie wynosi 0|Dynamiczny| Określ przedział czasu w sekundach. Ilość czasu oczekiwania przed rozpoczęciem kontrole kondycji po przetwarzania końcowego zadania infrastruktury. |
|InfrastructureTaskProcessingInterval | Czas w sekundach, domyślna wynosi 10 |Dynamiczny|Określ przedział czasu w sekundach. Interwał przetwarzania używane przez zadanie infrastruktury przetwarzania automatu stanów. |
|MaxCommunicationTimeout |Czas w sekundach, domyślna to 600 |Dynamiczny|Określ przedział czasu w sekundach. Maksymalny limit czasu na potrzeby wewnętrznej komunikacji między ClusterManager i innych usług (tj. Usługa nazewnictwa; Menedżer trybu failover i jest itp.). Limit czasu powinien być mniejszy niż MaxOperationTimeout globalnych, w (jak może to być wiele komunikacji między składnikami systemu dla każdej operacji klienta). |
|MaxDataMigrationTimeout |Czas w sekundach, domyślna to 600 |Dynamiczny|Określ przedział czasu w sekundach. Maksymalny limit czasu dla operacji odzyskiwania migracji danych po przeprowadzeniu uaktualnienia sieci szkieletowej. |
|MaxOperationRetryDelay |Czas w sekundach, domyślna to 5|Dynamiczny| Określ przedział czasu w sekundach. Maksymalne opóźnienie wewnętrznego ponownych prób, gdy wystąpią błędy. |
|MaxOperationTimeout |Czas w sekundach, wartość domyślna to MaxValue |Dynamiczny| Określ przedział czasu w sekundach. Maksymalna globalnego limitu czasu dla wewnętrznego przetwarzania ClusterManager. |
|MaxTimeoutRetryBuffer | Czas w sekundach, domyślna to 600 |Dynamiczny|Określ przedział czasu w sekundach. Operacja maksymalny limit czasu podczas wewnętrznie ponawiania z powodu przekroczenia limitu czasu to <Original Time out>  +  <MaxTimeoutRetryBuffer>. Dodatkowe limitu czasu jest dodawana w przyrostach MinOperationTimeout. |
|MinOperationTimeout | Czas w sekundach, domyślna to 60 |Dynamiczny|Określ przedział czasu w sekundach. Minimalna globalnego limitu czasu dla wewnętrznego przetwarzania ClusterManager. |
|MinReplicaSetSize |Int, domyślna to 3 |Niedozwolone|MinReplicaSetSize dla ClusterManager. |
|PlacementConstraints | ciąg, domyślna to "" |Niedozwolone|PlacementConstraints dla ClusterManager. |
|QuorumLossWaitDuration |Czas w sekundach, wartość domyślna to MaxValue |Niedozwolone| Określ przedział czasu w sekundach. QuorumLossWaitDuration dla ClusterManager. |
|ReplicaRestartWaitDuration |Czas w sekundach, wartość domyślna to (60.0 * 30)|Niedozwolone|Określ przedział czasu w sekundach. ReplicaRestartWaitDuration dla ClusterManager. |
|ReplicaSetCheckTimeoutRollbackOverride |Czas w sekundach, wartość domyślna to 1200 |Dynamiczny| Określ przedział czasu w sekundach. Jeśli wartości ReplicaSetCheckTimeout jest równa maksymalnej wartości DWORD; następnie zostanie zastąpiona wartością tej konfiguracji na potrzeby wycofywania. Wartość wykorzystana do przodu nigdy nie zostanie zastąpiona. |
|SkipRollbackUpdateDefaultService | Wartość logiczna, wartość domyślna to false |Dynamiczny|WP pominie przywracanie usług domyślnych zaktualizowane podczas wycofywania uaktualniania aplikacji. |
|StandByReplicaKeepDuration | Czas w sekundach, domyślną wartością jest (3600.0 * 2)|Niedozwolone|Określ przedział czasu w sekundach. StandByReplicaKeepDuration dla ClusterManager. |
|TargetReplicaSetSize |Int, domyślna to 7 |Niedozwolone|TargetReplicaSetSize dla ClusterManager. |
|UpgradeHealthCheckInterval |Czas w sekundach, domyślna to 60 |Dynamiczny|Częstotliwość stan kondycji sprawdza podczas uaktualniania monitorowanej aplikacji |
|UpgradeStatusPollInterval |Czas w sekundach, domyślna to 60 |Dynamiczny|Częstotliwość sondowania stanu uaktualnienia aplikacji. Ta wartość określa częstotliwość aktualizacji dla każde wywołanie GetApplicationUpgradeProgress |

## <a name="common"></a>Wspólne

| **Parametr** | **Dozwolone wartości** | **Zasady uaktualniania** | **Wskazówki dotyczące lub krótki opis** |
| --- | --- | --- | --- |
|PerfMonitorInterval |Czas w sekundach, wartość domyślna to 1 |Dynamiczny|Określ przedział czasu w sekundach. Interwał monitorowania wydajności. Ustawienie wartości 0 ani ujemna wyłącza monitorowanie. |

## <a name="defragmentationemptynodedistributionpolicy"></a>DefragmentationEmptyNodeDistributionPolicy
| **Parametr** | **Dozwolone wartości** |**Zasady uaktualniania**| **Wskazówki dotyczące lub krótki opis** |
| --- | --- | --- | --- |
|PropertyGroup|KeyIntegerValueMap, domyślna wartość to Brak|Dynamiczny|Określa, że defragmentacja zasad następuje podczas opróżniania węzłów. Dla danej metryki 0 wskazuje, że SF należy dążyć do defragmentowania węzłów równomiernie między domenami aktualizacji i błędów; wartość 1 oznacza tylko węzły muszą defragmentacji |

## <a name="defragmentationmetrics"></a>DefragmentationMetrics
| **Parametr** | **Dozwolone wartości** |**Zasady uaktualniania**| **Wskazówki dotyczące lub krótki opis** |
| --- | --- | --- | --- |
|PropertyGroup|KeyBoolValueMap, domyślna wartość to Brak|Dynamiczny|Określa zestaw metryk, które mają być używane do defragmentacji, a nie do równoważenia obciążenia. |

## <a name="defragmentationmetricspercentornumberofemptynodestriggeringthreshold"></a>DefragmentationMetricsPercentOrNumberOfEmptyNodesTriggeringThreshold
| **Parametr** | **Dozwolone wartości** |**Zasady uaktualniania**| **Wskazówki dotyczące lub krótki opis** |
| --- | --- | --- | --- |
|PropertyGroup|KeyDoubleValueMap, domyślna wartość to Brak|Dynamiczny|Określa liczbę węzły w warstwie bezpłatna, które są potrzebne do należy wziąć pod uwagę klastra defragmentacji, określając albo procent w zakresie [0.0-1.0) lub liczba pustych węzłów jako liczba > = 1,0 |

## <a name="diagnostics"></a>Diagnostyka

| **Parametr** | **Dozwolone wartości** | **Zasady uaktualniania** | **Wskazówki dotyczące lub krótki opis** |
| --- | --- | --- | --- |
|AppDiagnosticStoreAccessRequiresImpersonation |Wartość logiczna, wartość domyślna to true | Dynamiczny |Określa, czy personifikacja jest wymagana podczas uzyskiwania dostępu do diagnostyki są przechowywane w imieniu aplikacji. |
|AppEtwTraceDeletionAgeInDays |Int, domyślna to 3 | Dynamiczny |Liczba dni, po upływie których możemy usunąć stare pliki ETL zawierający śladów funkcji ETW w aplikacji. |
|ApplicationLogsFormatVersion |int, domyślna to 0 | Dynamiczny |Wersja aplikacji rejestruje formatu. Obsługiwane wartości to 0 i 1. Wersja 1 zawiera więcej pól rekordu zdarzenia funkcji ETW niż wersja 0. |
|ClusterId |String | Dynamiczny |Unikatowy identyfikator klastra. To jest generowany podczas tworzenia klastra. |
|ConsumerInstances |String | Dynamiczny |Lista wystąpień odbiorców DCA. |
|DiskFullSafetySpaceInMB |Int, domyślny wynosi 1024 | Dynamiczny |Pozostałe miejsce na dysku w MB w celu ochrony z zasobów używanych przez DCA. |
|EnableCircularTraceSession |Wartość logiczna, wartość domyślna to false | Statyczny |Flaga wskazuje, czy można używać sesji śledzenia cykliczne. |
|EnableTelemetry |Wartość logiczna, wartość domyślna to true | Dynamiczny |To spowoduje utworzenie Włączanie lub wyłączanie telemetrii. |
|MaxDiskQuotaInMB |Int, wartość domyślna to 65536 | Dynamiczny |Przydział dysku w plikach dziennika MB dla Windows Fabric. |
|ProducerInstances |String | Dynamiczny |Lista wystąpień producentów DCA. |

## <a name="dnsservice"></a>DnsService
| **Parametr** | **Dozwolone wartości** |**Zasady uaktualniania**| **Wskazówki dotyczące lub krótki opis** |
| --- | --- | --- | --- |
|EnablePartitionedQuery|wartość logiczna, domyślna to FALSE|Statyczny|Flaga ustawiana, aby włączyć obsługę zapytań DNS dotyczących usług podzielonym na partycje. Ta funkcja jest domyślnie wyłączona. Aby uzyskać więcej informacji, zobacz [DNS usługa Service Fabric.](service-fabric-dnsservice.md)|
|InstanceCount|int, domyślna to -1|Statyczny|Wartość domyślna to -1, co oznacza, że usługa DNS działa w każdym węźle. OneBox musi to być równa 1, ponieważ usługa DNS używa dobrze znany port 53, dzięki czemu jej nie może mieć wiele wystąpień na tym samym komputerze.|
|IsEnabled|wartość logiczna, domyślna to FALSE|Statyczny|Włącza/wyłącza usługa DNS. Usługa DNS jest domyślnie wyłączona, a ta konfiguracja musi być ustawiona, aby go włączyć. |
|PartitionPrefix|ciąg, domyślna to "--"|Statyczny|Określa wartość ciągu prefiks partycji w zapytań DNS dotyczących usług podzielonym na partycje. Wartość: <ul><li>Powinny być zgodne ze standardem RFC, ponieważ będzie on część zapytania DNS.</li><li>Nie może zawierać pojedynczego znaku kropki ".", zgodnie z dot zakłócenie działania sufiks DNS.</li><li>Nie może być większa niż 5 znaków.</li><li>Nie może być ciągiem pustym.</li><li>Jeśli ustawienie PartitionPrefix zostanie zastąpiona, a następnie PartitionSuffix musi zostać zastąpiona i na odwrót.</li></ul>Aby uzyskać więcej informacji, zobacz [DNS usługa Service Fabric.](service-fabric-dnsservice.md).|
|PartitionSuffix|ciąg, domyślna to ""|Statyczny|Określa wartość partycji sufiks ciągu zapytania DNS dla usług podzielonym na partycje. Wartość: <ul><li>Powinny być zgodne ze standardem RFC, ponieważ będzie on część zapytania DNS.</li><li>Nie może zawierać pojedynczego znaku kropki ".", zgodnie z dot zakłócenie działania sufiks DNS.</li><li>Nie może być większa niż 5 znaków.</li><li>Jeśli ustawienie PartitionPrefix zostanie zastąpiona, a następnie PartitionSuffix musi zostać zastąpiona i na odwrót.</li></ul>Aby uzyskać więcej informacji, zobacz [DNS usługa Service Fabric.](service-fabric-dnsservice.md). |

## <a name="eventstore"></a>Bazy danych EventStore

| **Parametr** | **Dozwolone wartości** | **Zasady uaktualniania** | **Wskazówki dotyczące lub krótki opis** |
| --- | --- | --- | --- |
|MinReplicaSetSize|int, domyślna to 0|Statyczny|MinReplicaSetSize usługi bazy danych EventStore |
|PlacementConstraints|ciąg, domyślna to ""|Statyczny|  PlacementConstraints usługi bazy danych EventStore |
|TargetReplicaSetSize|int, domyślna to 0|Statyczny| TargetReplicaSetSize usługi bazy danych EventStore |

## <a name="fabricclient"></a>FabricClient

| **Parametr** | **Dozwolone wartości** | **Zasady uaktualniania** | **Wskazówki dotyczące lub krótki opis** |
| --- | --- | --- | --- |
|ConnectionInitializationTimeout |Czas w sekundach, domyślna to 2 |Dynamiczny|Określ przedział czasu w sekundach. Interwał limitu czasu połączenia dla każdego klienta czas próbuje nawiązać połączenie z bramą.|
|HealthOperationTimeout |Czas w sekundach, domyślna to 120 |Dynamiczny|Określ przedział czasu w sekundach. Limit czasu dla raportu wiadomości wysyłane do Menedżera kondycji. |
|HealthReportRetrySendInterval |Czas w sekundach, wartość domyślna to 30, minimalna liczba to 1 |Dynamiczny|Określ przedział czasu w sekundach. Interwał, jaką składnika raportowania umożliwia ponowne wysłanie kondycji zebranych raportów do Menedżera kondycji. |
|HealthReportSendInterval |Czas w sekundach, wartość domyślna to 30 |Dynamiczny|Określ przedział czasu w sekundach. Interwał, w którym zgłasza, że składnik wysyła raporty dotyczące zebranych kondycji do Menedżera kondycji. |
|KeepAliveIntervalInSeconds |Int, domyślna to 20 |Statyczny|Interwał, z jaką transportu FabricClient wysyła komunikaty utrzymywania aktywności do bramy. 0; keepAlive jest wyłączona. Musi być wartością dodatnią. |
|MaxFileSenderThreads |Uint — wartość domyślna wynosi 10 |Statyczny|Maksymalna liczba plików, które są przekazywane w sposób równoległy. |
|NodeAddresses |ciąg, domyślna to "" |Statyczny|Kolekcja adresów (parametry połączenia) w różnych węzłach, które mogą służyć do komunikacji z usługą nazewnictwa. Początkowo klient nawiąże połączenie, wybierając jeden z adresów losowo. Jeśli podano więcej niż jeden ciąg połączenia i połączenie zakończy się niepowodzeniem z powodu komunikacji lub przekroczenie limitu czasu; Przełącza klienta do korzystania z adresu następnego sekwencyjnie. Zobacz adres usługi nazewnictwa, spróbuj ponownie, aby uzyskać szczegółowe informacje na semantyce ponownych prób. |
|PartitionLocationCacheLimit |Int, wartość domyślna to 100000 |Statyczny|Liczba partycji w pamięci podręcznej i usługi rozpoznawania (ustawione na 0, brak limitu). |
|RetryBackoffInterval |Czas w sekundach, domyślna to 3 |Dynamiczny|Określ przedział czasu w sekundach. Interwał wycofywania przed ponowieniem próby wykonania operacji. |
|ServiceChangePollInterval |Czas w sekundach, domyślna to 120 |Dynamiczny|Określ przedział czasu w sekundach. Odstęp czasu między kolejnymi sondowaniami usługi zmienia się z klienta bramy dla zarejestrowanej usługi zmiany powiadomienia z wywołań zwrotnych. |

## <a name="fabrichost"></a>FabricHost

| **Parametr** | **Dozwolone wartości** | **Zasady uaktualniania** | **Wskazówki dotyczące lub krótki opis** |
| --- | --- | --- | --- |
|ActivationMaxFailureCount |Int, domyślna wynosi 10 |Dynamiczny|Jest to maksymalna liczba, dla której system ponowi próbę aktywacji nie powiodło się przed rezygnacją. |
|ActivationMaxRetryInterval |Czas w sekundach, domyślna to 300 |Dynamiczny|Określ przedział czasu w sekundach. Maksymalny interwał ponawiania dla aktywacji. W przypadku każdego niepowodzenia ciągłe interwał ponawiania jest obliczany jako (ActivationMaxRetryInterval; Min Liczba niepowodzeń ciągłe * ActivationRetryBackoffInterval). |
|ActivationRetryBackoffInterval |Czas w sekundach, domyślna to 5 |Dynamiczny|Określ przedział czasu w sekundach. Interwał wycofywania, co w przypadku niepowodzenia aktywacji; na co w przypadku niepowodzenia aktywacji ciągłe system ponowi próbę aktywacji dla maksymalnie MaxActivationFailureCount. Interwał ponawiania prób przy każdej próbie jest produktem w przypadku niepowodzenia aktywacji ciągłe i interwał wycofywania aktywacji. |
|EnableRestartManagement |Wartość logiczna, wartość domyślna to false |Dynamiczny|To jest umożliwienie ponownego uruchomienia serwera. |
|EnableServiceFabricAutomaticUpdates |Wartość logiczna, wartość domyślna to false |Dynamiczny|To jest włączenie automatycznej aktualizacji sieci szkieletowej za pośrednictwem aktualizacji Windows. |
|EnableServiceFabricBaseUpgrade |Wartość logiczna, wartość domyślna to false |Dynamiczny|To jest włączenie aktualizacji dla serwera. |
|StartTimeout |Czas w sekundach, domyślna to 300 |Dynamiczny|Określ przedział czasu w sekundach. Limit czasu uruchamiania fabricactivationmanager. |
|StopTimeout |Czas w sekundach, domyślna to 300 |Dynamiczny|Określ przedział czasu w sekundach. Limit czasu w celu aktywacji usługi hostowanej. Dezaktywacja i uaktualniania. |

## <a name="fabricnode"></a>FabricNode

| **Parametr** | **Dozwolone wartości** | **Zasady uaktualniania** | **Wskazówki dotyczące lub krótki opis** |
| --- | --- | --- | --- |
|ClientAuthX509FindType |ciąg, domyślną jest "FindByThumbprint" |Dynamiczny|Wskazuje, jak wyszukiwać certyfikatu w magazynie określonym przez wartość ClientAuthX509StoreName obsługiwane: FindByThumbprint; FindBySubjectName. |
|ClientAuthX509FindValue |ciąg, domyślna to "" | Dynamiczny|Używana do lokalizowania certyfikatów dla roli Administrator domyślny FabricClient wartość filtru wyszukiwania. |
|ClientAuthX509FindValueSecondary |ciąg, domyślna to "" |Dynamiczny|Używana do lokalizowania certyfikatów dla roli Administrator domyślny FabricClient wartość filtru wyszukiwania. |
|ClientAuthX509StoreName |ciąg, domyślnie jest "Mój" |Dynamiczny|Nazwa magazynu certyfikatu X.509, który zawiera certyfikat dla domyślnego administratora roli FabricClient. |
|ClusterX509FindType |ciąg, domyślną jest "FindByThumbprint" |Dynamiczny|Wskazuje, jak wyszukiwać certyfikat klastra w magazynie określonym przez ClusterX509StoreName obsługiwane wartości: "FindByThumbprint"; "FindBySubjectName" z "FindBySubjectName"; gdy istnieje wiele dopasowań; jeden z wygaśnięciem pakietu najdalszego jest używany. |
|ClusterX509FindValue |ciąg, domyślna to "" |Dynamiczny|Wartość filtru wyszukiwania używana do lokalizowania certyfikatu klastra. |
|ClusterX509FindValueSecondary |ciąg, domyślna to "" |Dynamiczny|Wartość filtru wyszukiwania używana do lokalizowania certyfikatu klastra. |
|ClusterX509StoreName |ciąg, domyślnie jest "Mój" |Dynamiczny|Nazwa magazynu certyfikatów X.509, który zawiera certyfikat klastra do zabezpieczania komunikacji wewnątrz klastra. |
|EndApplicationPortRange |int, domyślna to 0 |Statyczny|Koniec (nie z wartościami granicznymi) porty aplikacji zarządzanych, udostępniając podsystemu. Wymagane, jeśli EndpointFilteringEnabled ma wartość true hostingu. |
|ServerAuthX509FindType |ciąg, domyślną jest "FindByThumbprint" |Dynamiczny|Wskazuje sposób wyszukiwania dla certyfikatu serwera w magazynie określonym przez wartość ServerAuthX509StoreName obsługiwane: FindByThumbprint; FindBySubjectName. |
|ServerAuthX509FindValue |ciąg, domyślna to "" |Dynamiczny|Wartość filtru wyszukiwania używana do lokalizowania certyfikatu serwera. |
|ServerAuthX509FindValueSecondary |ciąg, domyślna to "" |Dynamiczny|Wartość filtru wyszukiwania używana do lokalizowania certyfikatu serwera. |
|ServerAuthX509StoreName |ciąg, domyślnie jest "Mój" |Dynamiczny|Nazwa magazynu certyfikatu X.509, który zawiera certyfikat serwera dla usługi dań głównych. |
|StartApplicationPortRange |int, domyślna to 0 |Statyczny|Początek porty aplikacji zarządzanych, udostępniając podsystemu. Wymagane, jeśli EndpointFilteringEnabled ma wartość true hostingu. |
|StateTraceInterval |Czas w sekundach, domyślna to 300 |Statyczny|Określ przedział czasu w sekundach. Interwał w celu śledzenia stanu węzła, w każdym węźle oraz jego nowszymi wersjami węzły FM/FMM. |
|UserRoleClientX509FindType |ciąg, domyślną jest "FindByThumbprint" |Dynamiczny|Wskazuje, jak wyszukiwać certyfikatu w magazynie określonym przez wartość UserRoleClientX509StoreName obsługiwane: FindByThumbprint; FindBySubjectName. |
|UserRoleClientX509FindValue |ciąg, domyślna to "" |Dynamiczny|Używana do lokalizowania certyfikatów dla roli użytkownika domyślnego FabricClient wartość filtru wyszukiwania. |
|UserRoleClientX509FindValueSecondary |ciąg, domyślna to "" |Dynamiczny|Używana do lokalizowania certyfikatów dla roli użytkownika domyślnego FabricClient wartość filtru wyszukiwania. |
|UserRoleClientX509StoreName |ciąg, domyślnie jest "Mój" |Dynamiczny|Nazwa magazynu certyfikatu X.509, który zawiera certyfikat dla roli użytkownika domyślnego FabricClient. |

## <a name="failovermanager"></a>FailoverManager

| **Parametr** | **Dozwolone wartości** | **Zasady uaktualniania** | **Wskazówki dotyczące lub krótki opis** |
| --- | --- | --- | --- |
|BuildReplicaTimeLimit|Przedział czasu, wartością domyślną jest Common::TimeSpan::FromSeconds(3600)|Dynamiczny|Określ przedział czasu w sekundach. Limit czasu na kompilowanie stanowych replik; Po upływie którego zostanie zainicjowana raport o kondycji ostrzeżenia |
|ClusterPauseThreshold|int, domyślna to 1|Dynamiczny|Jeśli liczba węzłów w systemie zejść poniżej tej wartości, a następnie umieszczanie; obciążenia równoważenia; i trybu failover zostanie zatrzymana. |
|CreateInstanceTimeLimit|Przedział czasu, wartością domyślną jest Common::TimeSpan::FromSeconds(300)|Dynamiczny|Określ przedział czasu w sekundach. Limit czasu tworzenia wystąpienia bezstanowe; Po upływie którego zostanie zainicjowana raport o kondycji ostrzeżenia |
|ExpectedClusterSize|int, domyślna to 1|Dynamiczny|Po początkowym uruchomieniu klastra FM będzie czekać w tym wiele węzłów do zgłaszania samodzielnie się przed rozpoczęciem, wprowadzenie do innych usług; w tym usług systemowych, takich jak nazewnictwa. Zwiększenie tej wartości zwiększa czas uruchomienia; klastra ale zapobiega wczesne węzły z przeciążenia, a także dodatkowe przenosi, które będą znajdować się konieczne, ponieważ więcej węzłów przejdzie w tryb online. Ta wartość zazwyczaj powinna być równa końcowej mały rozmiar klastra. |
|ExpectedNodeDeactivationDuration|Przedział czasu, wartością domyślną jest Common::TimeSpan::FromSeconds(60.0 * 30)|Dynamiczny|Określ przedział czasu w sekundach. Jest to oczekiwany czas trwania dla węzła ukończyć dezaktywacji w. |
|ExpectedNodeFabricUpgradeDuration|Przedział czasu, wartością domyślną jest Common::TimeSpan::FromSeconds(60.0 * 30)|Dynamiczny|Określ przedział czasu w sekundach. To jest oczekiwany czas dla węzła, który ma zostać uaktualniony podczas uaktualniania systemu Windows w sieci szkieletowej. |
|ExpectedReplicaUpgradeDuration|Przedział czasu, wartością domyślną jest Common::TimeSpan::FromSeconds(60.0 * 30)|Dynamiczny|Określ przedział czasu w sekundach. Jest to oczekiwany czas trwania dla wszystkich replik, które ma zostać uaktualniony w węźle podczas uaktualniania aplikacji. |
|IsSingletonReplicaMoveAllowedDuringUpgrade|wartość logiczna, domyślny ma wartość TRUE|Dynamiczny|Jeśli ustawiono wartość true; repliki przy użyciu rozmiaru docelowego zestawu replik 1 będzie możliwe, aby przenieść podczas uaktualniania. |
|MinReplicaSetSize|Int, domyślna to 3|Niedozwolone|Jest to minimalny rozmiar zestawu replik FM. Jeśli liczba aktywnych replik FM spada poniżej tej wartości; FM spowoduje odrzucenie zmian w klastrze, dopóki co najmniej minimalna liczba replik jest przywracany. |
|PlacementConstraints|ciąg, domyślna to ""|Niedozwolone|Wszelkie ograniczeniami dotyczącymi umieszczania dla replik Menedżer trybu failover |
|PlacementTimeLimit|Przedział czasu, wartością domyślną jest Common::TimeSpan::FromSeconds(600)|Dynamiczny|Określ przedział czasu w sekundach. Limit czasu na osiągnięcie liczby replik docelowych; Po upływie którego zostanie zainicjowana raport o kondycji ostrzeżenia |
|QuorumLossWaitDuration |Czas w sekundach, wartość domyślna to MaxValue |Dynamiczny|Określ przedział czasu w sekundach. Jest to maksymalny czas trwania, dla którego zezwalamy na partycję, aby być w stanie utraciła kworum. Jeśli partycja jest nadal w utraciła kworum po tym czasie; partycja wznowiła utraciła kworum, biorąc pod uwagę dół repliki jako utracone. Należy pamiętać, że to może potencjalnie pociągnąć za sobą utratę danych. |
|ReconfigurationTimeLimit|Przedział czasu, wartością domyślną jest Common::TimeSpan::FromSeconds(300)|Dynamiczny|Określ przedział czasu w sekundach. Limit czasu ponownej konfiguracji; Po upływie którego zostanie zainicjowana raport o kondycji ostrzeżenia |
|ReplicaRestartWaitDuration|Przedział czasu, wartością domyślną jest Common::TimeSpan::FromSeconds(60.0 * 30)|Niedozwolone|Określ przedział czasu w sekundach. Jest to ReplicaRestartWaitDuration dla FMService |
|StandByReplicaKeepDuration|Przedział czasu, wartością domyślną jest Common::TimeSpan::FromSeconds(3600.0 * 24 * 7)|Niedozwolone|Określ przedział czasu w sekundach. Jest to StandByReplicaKeepDuration dla FMService |
|TargetReplicaSetSize|Int, domyślna to 7|Niedozwolone|Jest to docelowej liczby replik FM, zapewniające Windows Fabric. Większa liczba zostanie podana skutkuje zwiększa niezawodność danych FM. za pomocą kosztem wydajności małych. |
|UserMaxStandByReplicaCount |int, domyślna to 1 |Dynamiczny|Domyślna maksymalna liczba replik w stanie wstrzymania, które system przechowuje usługi użytkownika. |
|UserReplicaRestartWaitDuration |Czas w sekundach, wartość domyślna to 60.0 * 30 |Dynamiczny|Określ przedział czasu w sekundach. Gdy utrwalonych replika ulegnie awarii; Windows Fabric czeka, aż ten czas trwania dla repliki w celu przywracane przed utworzeniem nowej repliki zastąpienia, (które wymagałyby kopię stanu). |
|UserStandByReplicaKeepDuration |Czas w sekundach, domyślna to 3600.0 * 24 * 7 |Dynamiczny|Określ przedział czasu w sekundach. Gdy utrwalonych repliki wrócić z stan naciśnięcia; go może już zostały zastąpione. Ten czasomierz Określa, jak długo FM zapewnią wstrzymania repliki przed usunięciem. |

## <a name="faultanalysisservice"></a>FaultAnalysisService

| **Parametr** | **Dozwolone wartości** | **Zasady uaktualniania** | **Wskazówki dotyczące lub krótki opis** |
| --- | --- | --- | --- |
|CompletedActionKeepDurationInSeconds | Int, wartością domyślną jest 604800 |Statyczny| Jest to około jak długo przechowywać akcje, które znajdują się w stan końcowy. Zależy to od również StoredActionCleanupIntervalInSeconds; od czasu pracy, aby oczyścić odbywa się tylko na tego interwału. 604800 wynosi 7 dni. |
|DataLossCheckPollIntervalInSeconds|int, domyślna to 5|Statyczny|Jest to czas między kontrole, które system wykonuje podczas oczekiwania na utratę danych, do wykonania. Liczba przypadków, gdy numer utraty danych, zostanie sprawdzony na iterację wewnętrznego jest DataLossCheckWaitDurationInSeconds/tej. |
|DataLossCheckWaitDurationInSeconds|int, domyślna to 25|Statyczny|Całkowita ilość czasu; w ciągu kilku sekund; czy system będzie czekać na utratę danych, które ma być wykonywana. To ustawienie jest używana wewnętrznie, po wywołaniu interfejsu api StartPartitionDataLossAsync(). |
|MinReplicaSetSize |int, domyślna to 0 |Statyczny|MinReplicaSetSize dla FaultAnalysisService. |
|PlacementConstraints | ciąg, domyślna to ""|Statyczny| PlacementConstraints dla FaultAnalysisService. |
|QuorumLossWaitDuration | Czas w sekundach, wartość domyślna to MaxValue |Statyczny|Określ przedział czasu w sekundach. QuorumLossWaitDuration dla FaultAnalysisService. |
|ReplicaDropWaitDurationInSeconds|int, domyślna to 600|Statyczny|Ten parametr jest używany, gdy wywoływana jest utrata danych interfejsu api. Określa, jak długo system będzie czekać repliki są umieszczane po Usuń replikę wewnętrznie zostanie wywołana na nim. |
|ReplicaRestartWaitDuration |Czas w sekundach, wartość domyślna to 60 minut|Statyczny|Określ przedział czasu w sekundach. ReplicaRestartWaitDuration dla FaultAnalysisService. |
|StandByReplicaKeepDuration| Czas w sekundach, wartość domyślna to (60*24*7) minut |Statyczny|Określ przedział czasu w sekundach. StandByReplicaKeepDuration dla FaultAnalysisService. |
|StoredActionCleanupIntervalInSeconds | Int, wartość domyślna to 3600 |Statyczny|Jest to, jak często wyczyszczone magazynu. Tylko akcje w stan końcowy; i ukończone co najmniej CompletedActionKeepDurationInSeconds temu zostanie usunięty. |
|StoredChaosEventCleanupIntervalInSeconds | Int, wartość domyślna to 3600 |Statyczny|Jest to, jak często będą inspekcji magazynie oczyszczania; Jeśli liczba zdarzeń jest więcej niż 30000; oczyszczanie będzie zaczną działać. |
|TargetReplicaSetSize |int, domyślna to 0 |Statyczny|NOT_PLATFORM_UNIX_START The TargetReplicaSetSize for FaultAnalysisService. |

## <a name="federation"></a>Federacja

| **Parametr** | **Dozwolone wartości** | **Zasady uaktualniania** | **Wskazówki dotyczące lub krótki opis** |
| --- | --- | --- | --- |
|LeaseDuration |Czas w sekundach, wartość domyślna to 30 |Dynamiczny|Czas trwania dzierżawy ważny od węzła i jego sąsiadami. |
|LeaseDurationAcrossFaultDomain |Czas w sekundach, wartość domyślna to 30 |Dynamiczny|Czas trwania dzierżawy ważny od węzła i jego sąsiadami w domenach błędów. |

## <a name="filestoreservice"></a>FileStoreService

| **Parametr** | **Dozwolone wartości** | **Zasady uaktualniania** | **Wskazówki dotyczące lub krótki opis** |
| --- | --- | --- | --- |
|AcceptChunkUpload|wartość logiczna, domyślny ma wartość TRUE|Dynamiczny|Konfiguracja, aby określić, czy usługa Magazyn plików akceptuje fragmentów na podstawie pliku przekazywania nie podczas kopiowania pakietu aplikacji. |
|AnonymousAccessEnabled | Wartość logiczna, wartość domyślna to true |Statyczny|Włącz/Wyłącz dostęp anonimowy do udziałów FileStoreService. |
|CommonName1Ntlmx509CommonName|ciąg, domyślna to ""|Statyczny| Nazwa pospolita X509 certyfikat używany do generowania HMAC na CommonName1NtlmPasswordSecret w przypadku korzystania z uwierzytelniania NTLM |
|CommonName1Ntlmx509StoreLocation|ciąg, domyślną jest "LocalMachine"|Statyczny|Lokalizacja magazynu X509 certyfikat używany do generowania HMAC na CommonName1NtlmPasswordSecret w przypadku korzystania z uwierzytelniania NTLM |
|CommonName1Ntlmx509StoreName|ciąg, domyślna to "MY"| Statyczny|Nazwa magazynu X509 certyfikat używany do generowania HMAC na CommonName1NtlmPasswordSecret w przypadku korzystania z uwierzytelniania NTLM |
|CommonName2Ntlmx509CommonName|ciąg, domyślna to ""|Statyczny|Nazwa pospolita X509 certyfikat używany do generowania HMAC na CommonName2NtlmPasswordSecret w przypadku korzystania z uwierzytelniania NTLM |
|CommonName2Ntlmx509StoreLocation|ciąg, domyślną jest "LocalMachine"| Statyczny|Lokalizacja magazynu X509 certyfikat używany do generowania HMAC na CommonName2NtlmPasswordSecret w przypadku korzystania z uwierzytelniania NTLM |
|CommonName2Ntlmx509StoreName|ciąg, domyślna to "MY"|Statyczny| Nazwa magazynu X509 certyfikat używany do generowania HMAC na CommonName2NtlmPasswordSecret w przypadku korzystania z uwierzytelniania NTLM |
|CommonNameNtlmPasswordSecret|SecureString, wartością domyślną jest Common::SecureString("")| Statyczny|Klucz tajny hasła, który używane jako inicjator do wygenerowanego tego samego hasła podczas korzystania z uwierzytelniania NTLM |
|GenerateV1CommonNameAccount| wartość logiczna, domyślny ma wartość TRUE|Statyczny|Określa, czy wygenerować konta przy użyciu algorytmu generowania V1 nazwy użytkownika. Począwszy od usługi Service Fabric wersji 6.1; konta z generacji 2 zawsze jest tworzony. Konto w wersji 1 jest niezbędne do uaktualnienia z i do wersji, które nie obsługują generowanie w wersji 2 (przed 6.1).|
|MaxCopyOperationThreads | Uint — wartość domyślna to 0 |Dynamiczny| Maksymalna liczba równoległych plików oznacza dodatkowej można skopiować z podstawowego. "0" == liczby rdzeni. |
|MaxFileOperationThreads | Uint — wartość domyślna to 100 |Statyczny| Maksymalna liczba równoległych wątków może wykonywać FileOperations (kopiowania/przenoszenia) w podstawowym. "0" == liczby rdzeni. |
|MaxRequestProcessingThreads | Uint, wartość domyślna to 200 |Statyczny|Maksymalna liczba wątków równoległych dopuszczona do przetwarzania żądań w podstawowym. "0" == liczby rdzeni. |
|MaxSecondaryFileCopyFailureThreshold | Uint — wartość domyślna to 25|Dynamiczny|Maksymalna liczba ponowień kopiowania plików na serwerze pomocniczym przed rezygnacją. |
|MaxStoreOperations | Uint, wartość domyślna to 4096 |Statyczny|Maksymalna liczba operacji transakcji magazynu równoległego dozwolone na maszynie podstawowej. "0" == liczby rdzeni. |
|NamingOperationTimeout |Czas w sekundach, domyślna to 60 |Dynamiczny|Określ przedział czasu w sekundach. Limit czasu wykonywania operacji nazewnictwa. |
|PrimaryAccountNTLMPasswordSecret | SecureString, domyślne jest puste |Statyczny| Klucz tajny hasła, który jest używany jako inicjator do wygenerowanego tego samego hasła podczas korzystania z uwierzytelniania NTLM. |
|PrimaryAccountNTLMX509StoreLocation | ciąg, domyślną jest "LocalMachine"|Statyczny| Lokalizacja magazynu X509 certyfikat używany do generowania HMAC na PrimaryAccountNTLMPasswordSecret w przypadku korzystania z uwierzytelniania NTLM. |
|PrimaryAccountNTLMX509StoreName | ciąg, domyślna to "MY"|Statyczny| Nazwa magazynu X509 certyfikat używany do generowania HMAC na PrimaryAccountNTLMPasswordSecret w przypadku korzystania z uwierzytelniania NTLM. |
|PrimaryAccountNTLMX509Thumbprint | ciąg, domyślna to ""|Statyczny|Odcisk palca X509 certyfikat używany do generowania HMAC na PrimaryAccountNTLMPasswordSecret w przypadku korzystania z uwierzytelniania NTLM. |
|PrimaryAccountType | ciąg, domyślna to "" |Statyczny|Podstawowy AccountType jednostki do listy ACL FileStoreService udziałów. |
|PrimaryAccountUserName | ciąg, domyślna to "" |Statyczny|Konto głównej nazwy użytkownika głównej do listy ACL FileStoreService udziałów. |
|PrimaryAccountUserPassword | SecureString, domyślne jest puste |Statyczny|Hasło główne konto jednostki do listy ACL FileStoreService udziałów. |
|QueryOperationTimeout | Czas w sekundach, domyślna to 60 |Dynamiczny|Określ przedział czasu w sekundach. Limit czasu wykonywania operacji zapytania. |
|SecondaryAccountNTLMPasswordSecret | SecureString, domyślne jest puste |Statyczny| Klucz tajny hasła, który jest używany jako inicjator do wygenerowanego tego samego hasła podczas korzystania z uwierzytelniania NTLM. |
|SecondaryAccountNTLMX509StoreLocation | ciąg, domyślną jest "LocalMachine" |Statyczny|Lokalizacja magazynu X509 certyfikat używany do generowania HMAC na SecondaryAccountNTLMPasswordSecret w przypadku korzystania z uwierzytelniania NTLM. |
|SecondaryAccountNTLMX509StoreName | ciąg, domyślna to "MY" |Statyczny|Nazwa magazynu X509 certyfikat używany do generowania HMAC na SecondaryAccountNTLMPasswordSecret w przypadku korzystania z uwierzytelniania NTLM. |
|SecondaryAccountNTLMX509Thumbprint | ciąg, domyślna to ""| Statyczny|Odcisk palca X509 certyfikat używany do generowania HMAC na SecondaryAccountNTLMPasswordSecret w przypadku korzystania z uwierzytelniania NTLM. |
|SecondaryAccountType | ciąg, domyślna to ""|Statyczny| Pomocniczy dla konta podmiotu zabezpieczeń do listy ACL FileStoreService udziałów. |
|SecondaryAccountUserName | ciąg, domyślna to ""| Statyczny|Drugie konto nazwy użytkownika głównej do listy ACL FileStoreService udziałów. |
|SecondaryAccountUserPassword | SecureString, domyślne jest puste |Statyczny|Hasło konta dodatkowej jednostki do listy ACL FileStoreService udziałów. |
|SecondaryFileCopyRetryDelayMilliseconds|uint — wartość domyślna to 500|Dynamiczny|Kopiowanie plików opóźnienie (w milisekundach).|
|UseChunkContentInTransportMessage|wartość logiczna, domyślny ma wartość TRUE|Dynamiczny|Flaga wskazująca, korzystających z nowej wersji wprowadzone w v6.4 protokół przekazywania. Ta wersja protokołu używa transport usługi Service fabric, aby przekazać pliki do magazynu obrazów, który zapewnia lepszą wydajność niż protokół SMB używany w poprzednich wersjach. |

## <a name="healthmanager"></a>HealthManager

| **Parametr** | **Dozwolone wartości** | **Zasady uaktualniania** | **Wskazówki dotyczące lub krótki opis** |
| --- | --- | --- | --- |
|EnableApplicationTypeHealthEvaluation |Wartość logiczna, wartość domyślna to false |Statyczny|Zasady oceny kondycji klastra: Włącz na ocenę kondycji typu aplikacji. |
|MaxSuggestedNumberOfEntityHealthReports|Int, domyślna to 500 |Dynamiczny|Maksymalna liczba kondycji raporty, że jednostka może mieć przed zgłoszeniem wątpliwości dotyczących raportowania logiki kondycji strażnika. Każda jednostka kondycji powinien mieć stosunkowo małej liczby raportów o kondycji. Jeśli liczba raportów przekroczy wartość ta liczba. mogą wystąpić problemy z implementacją strażnika. Obiekt o zbyt wiele raportów zostanie oflagowana za pośrednictwem raport o kondycji ostrzeżenia podczas szacowania jednostki. |

## <a name="healthmanagerclusterhealthpolicy"></a>HealthManager/ClusterHealthPolicy

| **Parametr** | **Dozwolone wartości** | **Zasady uaktualniania** | **Wskazówki dotyczące lub krótki opis** |
| --- | --- | --- | --- |
|ConsiderWarningAsError |Wartość logiczna, wartość domyślna to false |Statyczny|Zasady oceny kondycji klastra: ostrzeżenia są traktowane jako błędy. |
|MaxPercentUnhealthyApplications | int, domyślna to 0 |Statyczny|Zasady oceny kondycji klastra: maksymalny procent aplikacji w złej kondycji dozwolone dla klastra, będące w dobrej kondycji. |
|MaxPercentUnhealthyNodes | int, domyślna to 0 |Statyczny|Zasady oceny kondycji klastra: maksymalny procent węzłów w złej kondycji dozwolone dla klastra, będące w dobrej kondycji. |

## <a name="healthmanagerclusterupgradehealthpolicy"></a>HealthManager/ClusterUpgradeHealthPolicy

| **Parametr** | **Dozwolone wartości** | **Zasady uaktualniania** | **Wskazówki dotyczące lub krótki opis** |
| --- | --- | --- | --- |
|MaxPercentDeltaUnhealthyNodes|Int, domyślna wynosi 10|Statyczny|Zasady oceny uaktualnienia, kondycja klastra: maksymalny procent różnicy węzłów w złej kondycji dozwolone dla klastra, będące w dobrej kondycji |
|MaxPercentUpgradeDomainDeltaUnhealthyNodes|int, domyślna to 15|Statyczny|Zasady oceny uaktualnienia, kondycja klastra: maksymalna wartość procentowa delta węzłów w złej kondycji w domenie uaktualnienia jest dozwolone dla klastra, będące w dobrej kondycji |

## <a name="hosting"></a>Hosting

| **Parametr** | **Dozwolone wartości** | **Zasady uaktualniania** | **Wskazówki dotyczące lub krótki opis** |
| --- | --- | --- | --- |
|ActivationMaxFailureCount |Liczba całkowita, domyślna wynosi 10 |Dynamiczny|Liczba ponownych prób systemu nie powiodło się aktywacji przed rezygnacją |
|ActivationMaxRetryInterval |Czas w sekundach, domyślna to 300 |Dynamiczny|W przypadku każdego niepowodzenia ciągłe aktywacji systemu ponawia próbę aktywacji dla maksymalnie ActivationMaxFailureCount. ActivationMaxRetryInterval Określa interwał czasu oczekiwania przed ponownych prób po co w przypadku niepowodzenia aktywacji |
|ActivationRetryBackoffInterval |Czas w sekundach, domyślna to 5 |Dynamiczny|Interwał wycofywania w każdym przypadku niepowodzenia aktywacji; W przypadku każdego niepowodzenia ciągłe aktywacji systemu ponawia próbę aktywacji dla maksymalnie MaxActivationFailureCount. Interwał ponawiania prób przy każdej próbie jest produktem w przypadku niepowodzenia aktywacji ciągłe i interwał wycofywania aktywacji. |
|ActivationTimeout| Przedział czasu, wartością domyślną jest Common::TimeSpan::FromSeconds(180)|Dynamiczny| Określ przedział czasu w sekundach. Limit czasu w celu aktywacji aplikacji. Dezaktywacja i uaktualniania. |
|ApplicationHostCloseTimeout| Przedział czasu, wartością domyślną jest Common::TimeSpan::FromSeconds(120)|Dynamiczny| Określ przedział czasu w sekundach. Po zakończenia sieci szkieletowej wykryciu własnym aktywacji procesów; FabricRuntime zamyka wszystkie repliki w procesie hosta (hosta aplikacji) użytkownika. Jest to limit czasu dla operacji zamknięcia. |
|ApplicationUpgradeTimeout| Przedział czasu, wartością domyślną jest Common::TimeSpan::FromSeconds(360)|Dynamiczny| Określ przedział czasu w sekundach. Limit czasu dla uaktualnienie aplikacji. Jeśli limit czasu jest mniejsza niż deployer "ActivationTimeout" zakończy się niepowodzeniem. |
|ContainerServiceArguments|ciąg, domyślna to "-H 2375 -H npipe: / /"|Statyczny|Sieć szkieletowa usług (CPP) zarządza demona platformy docker (z wyjątkiem komputerów klienckich systemu windows, takich jak Windows 10). Ta konfiguracja umożliwia użytkownikowi określić niestandardowe argumenty, które powinny być przekazywane do demona platformy docker, podczas jego uruchamiania. Jeśli określono niestandardowe argumenty, Usługa Service Fabric nie przekaże żadnego innego argumentu do aparatu platformy Docker z wyjątkiem "--pidfile" argument. Dlatego użytkownicy nie należy określać "--pidfile" argument jako część ich argumentów klienta. Ponadto niestandardowe argumenty należy upewnić się, że platforma docker demona nasłuchuje na domyślnym potoku nazw w systemie Windows (lub w gnieździe domeny systemu Unix w systemie Linux) dla usługi Service Fabric móc komunikować się z nim.|
|ContainerServiceLogFileMaxSizeInKb|int, domyślny jest 32768|Statyczny|Maksymalny rozmiar pliku dziennika wygenerowany przez kontenery platformy docker.  Tylko Windows.|
|ContainerImageDownloadTimeout|int, liczbę sekund, wartość domyślna to 1200 (20 minut)|Dynamiczny|Liczba sekund przed upływem limitu czasu pobierania obrazów.|
|ContainerImagesToSkip|ciąg nazwy obrazów oddzielony znakiem linii pionowej, wartością domyślną jest ""|Statyczny|Nazwa obrazów kontenerów, które nie powinny być usuwane.  Używane z parametrem PruneContainerImages.|
|ContainerServiceLogFileNamePrefix|ciąg, domyślną jest "sfcontainerlogs"|Statyczny|Prefiks nazwy pliku dla plików dziennika generowanych przez kontenery platformy docker.  Tylko Windows.|
|ContainerServiceLogFileRetentionCount|Int, domyślna wynosi 10|Statyczny|Liczba plików dziennika generowanych przez kontenery platformy docker, zanim pliki dziennika zostaną zastąpione.  Tylko Windows.|
|CreateFabricRuntimeTimeout|Przedział czasu, wartością domyślną jest Common::TimeSpan::FromSeconds(120)|Dynamiczny| Określ przedział czasu w sekundach. Wartość limitu czasu w celu synchronizacji FabricCreateRuntime wywołania |
|DefaultContainerRepositoryAccountName|ciąg, domyślna to ""|Statyczny|Domyślne poświadczenia używane zamiast poświadczeń określonych w ApplicationManifest.xml |
|DefaultContainerRepositoryPassword|ciąg, domyślna to ""|Statyczny|Domyślne hasło poświadczenia używane zamiast poświadczeń określonych w ApplicationManifest.xml|
|DefaultContainerRepositoryPasswordType|ciąg, domyślna to ""|Statyczny|Jeśli nie jest pusty ciąg, wartość może być "Encrypted" lub "SecretsStoreRef".|
|DeploymentMaxFailureCount|Int, domyślna to 20| Dynamiczny|Wdrażanie aplikacji zostanie ponowiona dla czasów DeploymentMaxFailureCount przed zaniechaniem wdrożenie tej aplikacji w węźle.| 
|DeploymentMaxRetryInterval| Przedział czasu, wartością domyślną jest Common::TimeSpan::FromSeconds(3600)|Dynamiczny| Określ przedział czasu w sekundach. Maksymalny interwał ponawiania dla wdrożenia. W przypadku każdego niepowodzenia ciągłe interwał ponawiania jest obliczany jako (DeploymentMaxRetryInterval; Min Liczba niepowodzeń ciągłe * DeploymentRetryBackoffInterval) |
|DeploymentRetryBackoffInterval| Przedział czasu, wartością domyślną jest Common::TimeSpan::FromSeconds(10)|Dynamiczny|Określ przedział czasu w sekundach. Interwał wycofywania niepowodzenia wdrożenia. W przypadku niepowodzenia ciągłego wdrażania, co system ponowi próbę wdrożenia dla maksymalnie MaxDeploymentFailureCount. Interwał ponawiania jest wynikiem błędu ciągłego wdrażania i interwał wycofywania wdrożenia. |
|DisableContainers|wartość logiczna, domyślna to FALSE|Statyczny|Config wyłączenie containers — zamiast DisableContainerServiceStartOnContainerActivatorOpen, który jest używany przestarzały konfiguracji |
|DisableDockerRequestRetry|wartość logiczna, domyślna to FALSE |Dynamiczny| Domyślnie SF komunikuje się za pomocą DD (docker dameon) z limitem czasu równym "DockerRequestTimeout" dla każdego żądania http wysyłane do niej. Jeśli DD nie odpowie w tym okresie; Jeśli najwyższego poziomu operacji środki, nieopłacone pozostały czas wysyła żądanie SF ponownie.  Za pomocą kontenera funkcji Hyper-v; DD upłynąć znacznie więcej czasu na wyświetlenie kontenera lub go dezaktywować. W takich przypadkach DD żądaniu limit czasu z punktu widzenia SF i SF ponawia operację. Czasami zajmuje to nieco dodaje więcej wykorzystanie w polu DD. Ta konfiguracja umożliwia wyłączenie ta ponowna próba i poczekaj, aż DD odpowiedzi. |
|EnableActivateNoWindow| wartość logiczna, domyślna to FALSE|Dynamiczny| Aktywowanego procesu jest tworzony w tle bez żadnych konsoli. |
|EnableContainerServiceDebugMode|wartość logiczna, domyślny ma wartość TRUE|Statyczny|Włączanie/wyłączanie rejestrowania dla kontenerów docker.  Tylko Windows.|
|EnableDockerHealthCheckIntegration|wartość logiczna, domyślny ma wartość TRUE|Statyczny|Umożliwia integrację zdarzeń funkcji HEALTHCHECK platformy docker za pomocą usługi Service Fabric raport o kondycji systemu |
|EnableProcessDebugging|wartość logiczna, domyślna to FALSE|Dynamiczny| Umożliwia uruchomienie na hoście aplikacji w debugerze |
|EndpointProviderEnabled| wartość logiczna, domyślna to FALSE|Statyczny| Umożliwia zarządzanie zasobami punktu końcowego przez sieć szkieletową. Wymaga określenia początek i koniec zakresu portów aplikacji w FabricNode. |
|FabricContainerAppsEnabled| wartość logiczna, domyślna to FALSE|Statyczny| |
|FirewallPolicyEnabled|wartość logiczna, domyślna to FALSE|Statyczny| Umożliwia otwarcie portów zapory dla punktu końcowego zasobów za pomocą jawnego porty określone w pliku ServiceManifest |
|GetCodePackageActivationContextTimeout|Przedział czasu, wartością domyślną jest Common::TimeSpan::FromSeconds(120)|Dynamiczny|Określ przedział czasu w sekundach. Wartość limitu czasu dla wywołań CodePackageActivationContext. Nie ma to zastosowania do usług ad-hoc. |
|GovernOnlyMainMemoryForProcesses|wartość logiczna, domyślna to FALSE|Statyczny|Domyślne zachowanie nadzór nad zasobami polega na umieszczeniu limitu określonego we MemoryInMB na ilości całkowitej ilości pamięci (RAM + wymiany) przetwarzające używa. Jeśli zostanie przekroczony limit; Ten proces zostanie wyświetlony zdarzeń OutOfMemory wyjątku. Jeśli ten parametr ma wartość true; limit zostaną zastosowane tylko do ilości pamięci RAM, używanego przez proces. W przypadku przekroczenia tego limitu; a jeśli to ustawienie ma wartość true; system operacyjny zostanie następnie zamienić głównego pamięci na dysk. |
|IPProviderEnabled|wartość logiczna, domyślna to FALSE|Statyczny|Umożliwia zarządzanie adresami IP. |
|IsDefaultContainerRepositoryPasswordEncrypted|wartość logiczna, domyślna to FALSE|Statyczny|Czy DefaultContainerRepositoryPassword jest zaszyfrowany.|
|LinuxExternalExecutablePath|ciąg, domyślna to "/ usr/bin /" |Statyczny|Katalog podstawowy wykonywalnego poleceń zewnętrznych w węźle.|
|NTLMAuthenticationEnabled|wartość logiczna, domyślna to FALSE|Statyczny| Umożliwia obsługę przez pakiety kodu, które są z innymi użytkownikami, tak, aby procesów między maszynami mogą bezpiecznie komunikować się przy użyciu metod NTLM. |
|NTLMAuthenticationPasswordSecret|SecureString, wartością domyślną jest Common::SecureString("")|Statyczny|Jest zaszyfrowane ma, który jest używany do generowania haseł dla użytkowników NTLM. Musi być nelze nastavit, pokud NTLMAuthenticationEnabled ma wartość true. Zatwierdzone przez wdrażania. |
|NTLMSecurityUsersByX509CommonNamesRefreshInterval|Przedział czasu, wartością domyślną jest Common::TimeSpan::FromMinutes(3)|Dynamiczny|Określ przedział czasu w sekundach. Ustawienia specyficzne dla środowiska okresowe interwał, w których hostingu skanowania pod kątem nowych certyfikatów służący do uwierzytelniania NTLM FileStoreService konfiguracji. |
|NTLMSecurityUsersByX509CommonNamesRefreshTimeout|Przedział czasu, wartością domyślną jest Common::TimeSpan::FromMinutes(4)|Dynamiczny| Określ przedział czasu w sekundach. Limit czasu konfigurowania użytkowników protokołu NTLM przy użyciu nazwy pospolite certyfikatów. Użytkownicy NTLM są wymagane przez FileStoreService udziałów. |
|PruneContainerImages|wartość logiczna, domyślna to FALSE|Dynamiczny| Usuń aplikację nieużywanych obrazów kontenerów z węzłów. Gdy ApplicationType jest wyrejestrowywany z klastra usługi Service Fabric, obrazy kontenera, które były używane przez tę aplikację zostaną usunięte w węzłach, w którym został on pobrany przez usługę Service Fabric. Oczyszczanie uruchamiany co godzinę, dzięki czemu może potrwać maksymalnie jedną godzinę (oraz czas, aby oczyścić obrazu) dla obrazów do usunięcia z klastra.<br>Usługi Service Fabric nigdy nie będzie pobrać lub usunięcia obrazów, które nie są powiązane z aplikacją.  Niepowiązanych obrazy, które zostały pobrane ręcznie lub w inny sposób muszą zostać jawnie usunięte.<br>Obrazy, które nie powinny być usuwane, można określić w parametrze ContainerImagesToSkip.| 
|RegisterCodePackageHostTimeout|Przedział czasu, wartością domyślną jest Common::TimeSpan::FromSeconds(120)|Dynamiczny| Określ przedział czasu w sekundach. Wartość limitu czasu dla FabricRegisterCodePackageHost wywołanie synchroniczne. Dotyczy tylko multi kod pakietu aplikacji hosty takich jak FWP |
|RequestTimeout|Przedział czasu, wartością domyślną jest Common::TimeSpan::FromSeconds(30)|Dynamiczny| Określ przedział czasu w sekundach. Ta pozycja reprezentuje limit czasu komunikacji między hostem aplikacji i proces sieci szkieletowej dla różnych hostingu powiązanych operacji, takich jak rejestracji fabrycznej; użytkownika Rejestracja w czasie wykonywania. |
|RunAsPolicyEnabled| wartość logiczna, domyślna to FALSE|Statyczny| Umożliwia uruchamianie kodu pakietów jako użytkownik lokalny inny niż użytkownik w ramach której sieci szkieletowej proces jest uruchomiony. Aby włączyć te zasady sieci szkieletowej musi działać jako SYSTEM lub użytkownik, który ma SeAssignPrimaryTokenPrivilege. |
|ServiceFactoryRegistrationTimeout| Przedział czasu, wartością domyślną jest Common::TimeSpan::FromSeconds(120)|Dynamiczny|Określ przedział czasu w sekundach. Wartość limitu czasu w celu synchronizacji wywołanie ServiceFactory rejestru (Stateless/stanowa) |
|ServiceTypeDisableFailureThreshold |Liczba całkowita, domyślna to 1 |Dynamiczny|To jest próg liczby awarii, po upływie którego FailoverManager (FM) jest powiadamiany o konieczności Wyłącz typ usługi, w tym węźle i spróbować innego węzła do umieszczenia. |
|ServiceTypeDisableGraceInterval|Przedział czasu, wartością domyślną jest Common::TimeSpan::FromSeconds(30)|Dynamiczny|Określ przedział czasu w sekundach. Przedział czasu, po którym można wyłączyć typ usługi |
|ServiceTypeRegistrationTimeout |Czas w sekundach, domyślna to 300 |Dynamiczny|Maksymalny dozwolony czas realizacji ServiceType do zarejestrowane w usłudze Service fabric |
|UseContainerServiceArguments|wartość logiczna, domyślny ma wartość TRUE|Statyczny|Ta konfiguracja informuje hostowania Pomiń przekazywanie argumentów (określone w pliku konfiguracyjnym ContainerServiceArguments) do demona platformy docker.|

## <a name="httpgateway"></a>HttpGateway

| **Parametr** | **Dozwolone wartości** | **Zasady uaktualniania** | **Wskazówki dotyczące lub krótki opis** |
| --- | --- | --- | --- |
|ActiveListeners |Uint, wartością domyślną jest 50 |Statyczny| Liczba odczytów w stosunku do wysłania do kolejki serwera http. Kontroluje to liczbę żądań, które mogą zostać wykonane przez HttpGateway. |
|HttpGatewayHealthReportSendInterval |Czas w sekundach, wartość domyślna to 30 |Statyczny|Określ przedział czasu w sekundach. Interwał wysyła bramy Http skumulowana kondycji Raporty menedżera kondycji. |
|HttpStrictTransportSecurityHeader|ciąg, domyślna to ""|Dynamiczny| Określ wartość nagłówka rygorystyczne zabezpieczenia transportu HTTP do uwzględnienia w każdej odpowiedzi wysyłane przez HttpGateway. Po ustawieniu ciąg pusty; tego pliku nagłówkowego nie są uwzględniane w odpowiedzi bramy.|
|IsEnabled|Wartość logiczna, wartość domyślna to false |Statyczny| Włącza/wyłącza HttpGateway. HttpGateway jest domyślnie wyłączona. |
|MaxEntityBodySize |Uint, wartość domyślna to 4194304 |Dynamiczny|Daje maksymalny rozmiar treści, których można oczekiwać od żądania http. Wartość domyślna to 4MB. Httpgateway zakończy się niepowodzeniem na żądanie, jeśli ma on treści o rozmiarze > tej wartości. Rozmiar minimalny fragmentu odczytu wynosi 4096 bajtów. Tak, to musi być > = 4096. |

## <a name="imagestoreservice"></a>ImageStoreService

| **Parametr** | **Dozwolone wartości** | **Zasady uaktualniania** | **Wskazówki dotyczące lub krótki opis** |
| --- | --- | --- | --- |
|Enabled (Włączony) |Wartość logiczna, wartość domyślna to false |Statyczny|Flaga włączone dla ImageStoreService. Domyślnie: false |
|MinReplicaSetSize | Int, domyślna to 3 |Statyczny|MinReplicaSetSize dla ImageStoreService. |
|PlacementConstraints | ciąg, domyślna to "" |Statyczny| PlacementConstraints dla ImageStoreService. |
|QuorumLossWaitDuration | Czas w sekundach, wartość domyślna to MaxValue |Statyczny| Określ przedział czasu w sekundach. QuorumLossWaitDuration dla ImageStoreService. |
|ReplicaRestartWaitDuration | Czas w sekundach, wartość domyślna to 60.0 * 30 |Statyczny|Określ przedział czasu w sekundach. ReplicaRestartWaitDuration dla ImageStoreService. |
|StandByReplicaKeepDuration | Czas w sekundach, domyślną wartością jest 3600.0 * 2 |Statyczny| Określ przedział czasu w sekundach. StandByReplicaKeepDuration dla ImageStoreService. |
|TargetReplicaSetSize | Int, domyślna to 7 |Statyczny|TargetReplicaSetSize dla ImageStoreService. |

## <a name="ktllogger"></a>KtlLogger

| **Parametr** | **Dozwolone wartości** | **Zasady uaktualniania** | **Wskazówki dotyczące lub krótki opis** |
| --- | --- | --- | --- |
|AutomaticMemoryConfiguration |int, domyślna to 1 |Dynamiczny|Flaga wskazująca, jeśli ustawienia pamięci powinien zostać automatycznie i dynamicznie skonfigurowany. Jeśli wartość zero, a następnie ustawienia konfiguracji pamięci są używane bezpośrednio, a nie należy zmieniać na podstawie systemu warunków. Jeśli jeden, a następnie ustawienia pamięci zostaną skonfigurowane automatycznie i może ulec zmianie na podstawie systemu warunków. |
|MaximumDestagingWriteOutstandingInKB | int, domyślna to 0 |Dynamiczny|Liczba KB do Zezwalaj na logowanie udostępnionego awansować wcześniej dedykowanych dziennika. Użycie wartości 0, aby wskazać, bez ograniczeń.
|SharedLogId |ciąg, domyślna to "" |Statyczny|Unikatowy identyfikator guid dla udostępnionego dziennika kontenera. Użyj "" Jeśli przy użyciu domyślnej ścieżki, w obszarze katalogu głównego danych sieci szkieletowej. |
|SharedLogPath |ciąg, domyślna to "" |Statyczny|Ścieżka i nazwa pliku do lokalizacji, aby umieścić kontenera udostępnionego dziennika. Użyj "" dotyczące korzystania z domyślnej ścieżki, w obszarze katalogu głównego danych sieci szkieletowej. |
|SharedLogSizeInMB |Int, wartością domyślną jest 8192 |Statyczny|Liczba MB do przydzielenia w kontenerze udostępnionego dziennika. |
|SharedLogThrottleLimitInPercentUsed|int, domyślna to 0 | Statyczny | Procent użycia udostępnionego dziennika, który będzie wywoływać ograniczania przepustowości. Wartość powinna być z zakresu od 0 do 100. Wartość 0 oznacza użyta domyślna wartość procentową. Wartości 100 oznacza bez ograniczania przepływności w ogóle. Wartość z zakresu od 1 do 99 określa wartość procentową użycia dziennika powyższe ograniczenie, które nastąpią; na przykład jeśli udostępniony dziennik jest 10GB, a wartość jest 90, a następnie ograniczania nastąpi po 9GB, jest w użyciu. Zaleca się przy użyciu wartości domyślnej.|
|WriteBufferMemoryPoolMaximumInKB | int, domyślna to 0 |Dynamiczny|Liczba KB, aby umożliwić puli pamięci bufor zapisu do rozwijania. Użycie wartości 0, aby wskazać, bez ograniczeń. |
|WriteBufferMemoryPoolMinimumInKB |Int, wartością domyślną jest 8388608 |Dynamiczny|Numer bazy wiedzy można wstępnie przydzielić dla puli pamięci buforu zapisu. Użycie wartości 0, aby wskazać, ma ograniczenia domyślne powinny być zgodne z SharedLogSizeInMB poniżej. |

## <a name="management"></a>Zarządzanie

| **Parametr** | **Dozwolone wartości** | **Zasady uaktualniania** | **Wskazówki dotyczące lub krótki opis** |
| --- | --- | --- | --- |
|AzureStorageMaxConnections | Int, wartość domyślna to 5000 |Dynamiczny|Maksymalna liczba jednoczesnych połączeń z usługi azure storage. |
|AzureStorageMaxWorkerThreads | int, domyślna to 25 |Dynamiczny|Maksymalna liczba wątków roboczych równolegle. |
|AzureStorageOperationTimeout | Czas w sekundach, wartość domyślna to 6000 |Dynamiczny|Określ przedział czasu w sekundach. Limit czasu na zakończenie operacji xstore. |
|CleanupApplicationPackageOnProvisionSuccess|wartość logiczna, domyślna to FALSE |Dynamiczny|Ta konfiguracja Włącza lub wyłącza automatycznego czyszczenia pakietu aplikacji na pomyślne aprowizacji. |
|DisableChecksumValidation | Wartość logiczna, wartość domyślna to false |Statyczny| Ta konfiguracja pozwala włączyć lub wyłączyć weryfikacja sumy kontrolnej podczas udostępniania aplikacji. |
|DisableServerSideCopy | Wartość logiczna, wartość domyślna to false |Statyczny|Ta konfiguracja Włącza lub wyłącza po stronie serwera kopię pakietu aplikacji na ImageStore podczas udostępniania aplikacji. |
|ImageCachingEnabled | Wartość logiczna, wartość domyślna to true |Statyczny|Ta konfiguracja pozwala włączyć lub wyłączyć buforowanie. |
|ImageStoreConnectionString |SecureString |Statyczny|Parametry połączenia do katalogu głównego dla ImageStore. |
|ImageStoreMinimumTransferBPS | Int, domyślny wynosi 1024 |Dynamiczny|Szybkość transferu minimalne między klastrem i ImageStore. Ta wartość jest używana do określenia wartości limitu czasu podczas uzyskiwania dostępu do zewnętrznych ImageStore. Tę wartość można zmienić tylko wtedy, gdy opóźnienie między klastrem i ImageStore jest wysoka, aby dać więcej czasu dla klastra można pobrać z zewnętrznego ImageStore. |

## <a name="metricactivitythresholds"></a>MetricActivityThresholds
| **Parametr** | **Dozwolone wartości** |**Zasady uaktualniania**| **Wskazówki dotyczące lub krótki opis** |
| --- | --- | --- | --- |
|PropertyGroup|KeyIntegerValueMap, domyślna wartość to Brak|Dynamiczny|Określa zbiór MetricActivityThresholds metryki w klastrze. Równoważenie będzie działać, jeśli maxNodeLoad jest większa niż MetricActivityThresholds. Aby uzyskać defragmentacji metryk definiuje stopień równe obciążenie, lub poniżej której usługi Service Fabric uzna węzeł pusty |

## <a name="metricbalancingthresholds"></a>MetricBalancingThresholds
| **Parametr** | **Dozwolone wartości** |**Zasady uaktualniania**| **Wskazówki dotyczące lub krótki opis** |
| --- | --- | --- | --- |
|PropertyGroup|KeyDoubleValueMap, domyślna wartość to Brak|Dynamiczny|Określa zbiór MetricBalancingThresholds metryki w klastrze. Równoważenie będzie działać, jeśli maxNodeLoad/minNodeLoad jest większa niż MetricBalancingThresholds. Defragmentacja będzie działać, jeśli wartość jest mniejsza niż MetricBalancingThresholds maxNodeLoad/minNodeLoad w co najmniej jednym FD UD. |

## <a name="namingservice"></a>NamingService

| **Parametr** | **Dozwolone wartości** | **Zasady uaktualniania** | **Wskazówki dotyczące lub krótki opis** |
| --- | --- | --- | --- |
|GatewayServiceDescriptionCacheLimit |int, domyślna to 0 |Statyczny|Maksymalna liczba wpisów w pamięci podręcznej opis usługi LRU na bramie nazewnictwa (ustawione na 0, brak limitu). |
|MaxClientConnections |Int, domyślna to 1000 |Dynamiczny|Maksymalna dozwolona liczba połączeń z klientami na bramę. |
|MaxFileOperationTimeout |Czas w sekundach, wartość domyślna to 30 |Dynamiczny|Określ przedział czasu w sekundach. Maksymalny limit czasu, dozwolone dla operacji usługi Magazyn plików. Określanie większego limitu czasu żądania zostanie odrzucone. |
|MaxIndexedEmptyPartitions |Int, domyślna to 1000 |Dynamiczny|Maksymalna liczba puste partycje, które będą nadal indeksowany w pamięci podręcznej powiadomień, synchronizowania klientów ponownie nawiązującego połączenie. Wszelkie puste partycje powyżej tej liczby zostaną usunięte z indeksu wyszukiwania wersji rosnąco. Ponowne łączenie klientów można nadal przeprowadzić synchronizację i otrzymywać aktualizacje brakujących pustą partycję; ale protokołu synchronizacji staje się bardziej kosztowne. |
|MaxMessageSize |Int, domyślna to 4\*1024\*1024 |Statyczny|Maksymalny rozmiar komunikatu do komunikacji z klientem węzła przy użyciu nazw. Łagodzenia skutków ataków DOS; Wartość domyślna to 4MB. |
|MaxNamingServiceHealthReports | Int, domyślna wynosi 10 |Dynamiczny|Maksymalnej liczby powolne operacji, które przechowują nazewnictwa usługi Raporty o złej kondycji, w tym samym czasie. Jeśli jest to 0; wszystkie operacje powolne są wysyłane. |
|MaxOperationTimeout |Czas w sekundach, domyślna to 600 |Dynamiczny|Określ przedział czasu w sekundach. Maksymalny limit czasu, dozwolone w przypadku operacji klienta. Określanie większego limitu czasu żądania zostanie odrzucone. |
|MaxOutstandingNotificationsPerClient |Int, domyślna to 1000 |Dynamiczny|Maksymalna liczba oczekujących powiadomienia przed rejestracji klienta jest zamknięte przez bramę. |
|MinReplicaSetSize | Int, domyślna to 3 |Niedozwolone| Minimalna liczba replik nazewnictwa usług wymaganych do zapisania w celu ukończenia aktualizacji. W przypadku mniejszej liczby replik niż to aktywnych w systemie System niezawodność nie zezwala na aktualizacje do Naming Store Service dopóki repliki zostaną przywrócone. Ta wartość nigdy nie powinna być większa niż TargetReplicaSetSize. |
|PartitionCount |Int, domyślna to 3 |Niedozwolone|Liczba partycji usługi nazewnictwa przechowywać ma zostać utworzony. Każda partycja jest właścicielem pojedynczego klucza partycji, umożliwiająca jej indeks; klucze partycji tak [0. Liczba partycji) istnieje. Zwiększenie liczby usługi nazewnictwa partycji wzrost skali, które Usługa nazewnictwa może wykonywać w dzięki skróceniu średniej ilości danych przechowywanych w dowolnej repliki, tworzenie kopii ustawić; kosztem większego wykorzystania zasobów (ponieważ liczba partycji * musi być utrzymywana ReplicaSetSize usługi repliki).|
|PlacementConstraints | ciąg, domyślna to "" |Niedozwolone| Ograniczenie umieszczania dla usługi nazewnictwa. |
|QuorumLossWaitDuration | Czas w sekundach, wartość domyślna to MaxValue |Niedozwolone| Określ przedział czasu w sekundach. Gdy Usługa nazewnictwa pozwala utraciła kworum; Ten czasomierz zostanie uruchomiony. Po jego wygaśnięciu FM uzna dół repliki jako utraconych; i próbuje odzyskać kworum. Nie, że może to spowodować utratę danych. |
|RepairInterval | Czas w sekundach, domyślna to 5 |Statyczny| Określ przedział czasu w sekundach. Interwał, w której rozpocznie się nazewnictwa naprawy niespójność między urzędu właściciela i właściciela. |
|ReplicaRestartWaitDuration | Czas w sekundach, wartość domyślna to (60.0 * 30)|Niedozwolone| Określ przedział czasu w sekundach. Jeśli replika usługi Naming Service ulegnie awarii; Ten czasomierz zostanie uruchomiony. Po jego wygaśnięciu FM rozpocznie się zastąpić replik, które są wyłączone (nie jeszcze uważa ich zgubienia). |
|ServiceDescriptionCacheLimit | int, domyślna to 0 |Statyczny| Maksymalna liczba wpisów w pamięci podręcznej opis usługi LRU na Naming Store Service (ustawione na 0, brak limitu). |
|ServiceNotificationTimeout |Czas w sekundach, wartość domyślna to 30 |Dynamiczny|Określ przedział czasu w sekundach. Limit czasu używany w celu dostarczenia powiadomień dotyczących usług do klienta. |
|StandByReplicaKeepDuration | Czas w sekundach, domyślną wartością jest 3600.0 * 2 |Niedozwolone| Określ przedział czasu w sekundach. Jeśli replika usługi Naming Service wrócić z stan naciśnięcia; go może już zostały zastąpione. Ten czasomierz Określa, jak długo FM zapewnią wstrzymania repliki przed usunięciem. |
|TargetReplicaSetSize |Int, domyślna to 7 |Niedozwolone|Ustawia liczbę replik dla każdej partycji nazewnictwa usług w sklepie. Zwiększenie liczby zestawów replik zwiększa poziom niezawodności, aby uzyskać informacje zawarte w Naming Store Service; zmniejszenie zmiana informacji będzie tracone w wyniku awarii węzła; kosztem zwiększonego obciążenia w sieci szkieletowej Windows i czas potrzebny do wykonywania aktualizacji danych nazewnictwa.|

## <a name="nodebufferpercentage"></a>NodeBufferPercentage
| **Parametr** | **Dozwolone wartości** |**Zasady uaktualniania**| **Wskazówki dotyczące lub krótki opis** |
| --- | --- | --- | --- |
|PropertyGroup|KeyDoubleValueMap, domyślna wartość to Brak|Dynamiczny|Procent zarezerwowanej węzła na nazwę metryki; używane jako bufor, aby zachować niektóre wolnego miejsca w węźle w przypadku pracy awaryjnej. |

## <a name="nodecapacities"></a>NodeCapacities

| **Parametr** | **Dozwolone wartości** | **Zasady uaktualniania** | **Wskazówki dotyczące lub krótki opis** |
| --- | --- | --- | --- |
|PropertyGroup |NodeCapacityCollectionMap |Statyczny|Kolekcja wydajność węzła dla różnych metryk. |

## <a name="nodedomainids"></a>NodeDomainIds

| **Parametr** | **Dozwolone wartości** | **Zasady uaktualniania** | **Wskazówki dotyczące lub krótki opis** |
| --- | --- | --- | --- |
|PropertyGroup |NodeFaultDomainIdCollection |Statyczny|W tym artykule opisano domen błędów, do której należy dany węzeł. Domena błędów jest zdefiniowana za pomocą identyfikatora URI, który określa lokalizację węzła w centrum danych.  Identyfikatory URI domeny błędów są w formacie fd: / fd/następuje segmentu ścieżki identyfikatora URI.|
|UpgradeDomainId |ciąg, domyślna to "" |Statyczny|W tym artykule opisano domeny uaktualnień, której należy dany węzeł. |

## <a name="nodeproperties"></a>NodeProperties

| **Parametr** | **Dozwolone wartości** | **Zasady uaktualniania** | **Wskazówki dotyczące lub krótki opis** |
| --- | --- | --- | --- |
|PropertyGroup |NodePropertyCollectionMap |Statyczny|Kolekcja par klucz wartość ciągu dla właściwości węzła. |

## <a name="paas"></a>Paas

| **Parametr** | **Dozwolone wartości** | **Zasady uaktualniania** | **Wskazówki dotyczące lub krótki opis** |
| --- | --- | --- | --- |
|ClusterId |ciąg, domyślna to "" |Niedozwolone|X509 certyfikatu Magazyn używany przez sieć szkieletową konfiguracji ochrony. |

## <a name="performancecounterlocalstore"></a>PerformanceCounterLocalStore

| **Parametr** | **Dozwolone wartości** | **Zasady uaktualniania** | **Wskazówki dotyczące lub krótki opis** |
| --- | --- | --- | --- |
|Liczniki |String | Dynamiczny |Rozdzielana przecinkami lista liczników wydajności zbierających dane. |
|IsEnabled |Wartość logiczna, wartość domyślna to true | Dynamiczny |Flaga wskazuje, czy jest włączone zbieranie danych licznika wydajności w lokalnym węźle. |
|MaxCounterBinaryFileSizeInMB |int, domyślna to 1 | Dynamiczny |Maksymalny rozmiar (w MB) dla każdego pliku binarnego licznika wydajności. |
|NewCounterBinaryFileCreationIntervalInMinutes |Int, domyślna wynosi 10 | Dynamiczny |Maksymalny interwał (w sekundach), po którym tworzony jest nowy plik binarny licznika wydajności. |
|SamplingIntervalInSeconds |Int, domyślna to 60 | Dynamiczny |Interwał próbkowania dla liczników wydajności są zbierane. |

## <a name="placementandloadbalancing"></a>PlacementAndLoadBalancing

| **Parametr** | **Dozwolone wartości** | **Zasady uaktualniania** | **Wskazówki dotyczące lub krótki opis** |
| --- | --- | --- | --- |
|AffinityConstraintPriority | int, domyślna to 0 | Dynamiczny|Określa priorytet koligacji ograniczenia: 0: Twarde; 1: Soft; negative: Ignoruj. |
|ApplicationCapacityConstraintPriority | int, domyślna to 0 | Dynamiczny|Określa priorytet pojemności ograniczenia: 0: Twarde; 1: Soft; negative: Ignoruj. |
|AutoDetectAvailableResources|wartość logiczna, domyślny ma wartość TRUE|Statyczny|Ta konfiguracja wywoła automatyczne wykrywanie dostępnych zasobów w węźle (procesora CPU i pamięci) kiedy ta konfiguracja jest ustawiona na wartość true, — firma Microsoft odczytać rzeczywistej pojemności i popraw je, jeśli użytkownik określony nieprawidłowy węzeł pojemności lub nie zostały zdefiniowane je w każdym przypadku ta konfiguracja jest ustawiona na false — firma Microsoft będzie  Ostrzeżenie, ten użytkownik określony nieprawidłowy węzeł możliwości; śledzenia Jednak firma Microsoft nie może poprawiać co oznacza, że użytkownik chce, aby mieć pojemności, określony jako > niż węzeł naprawdę czy pojemności są niezdefiniowane; przyjmowane jest założenie nieograniczoną pojemność |
|BalancingDelayAfterNewNode | Czas w sekundach, domyślna to 120 |Dynamiczny|Określ przedział czasu w sekundach. Nie należy uruchamiać równoważenia działań w tym okresie po dodaniu nowego węzła. |
|BalancingDelayAfterNodeDown | Czas w sekundach, domyślna to 120 |Dynamiczny|Określ przedział czasu w sekundach. Nie należy uruchamiać równoważenia działań w tym okresie po węźle szczegółów zdarzenia. |
|CapacityConstraintPriority | int, domyślna to 0 | Dynamiczny|Określa priorytet pojemności ograniczenia: 0: Twarde; 1: Soft; negative: Ignoruj. |
|ConsecutiveDroppedMovementsHealthReportLimit | Int, domyślna to 20 | Dynamiczny|Określa, ile razy pod rząd, które ruchów wystawionych ResourceBalancer są usuwane, zanim powiązane diagnostyki i ostrzeżenia dotyczące kondycji są emitowane. Ujemne: Nie ostrzeżeń emitowane w ramach tego warunku. |
|ConstraintFixPartialDelayAfterNewNode | Czas w sekundach, domyślna to 120 |Dynamiczny| Określ przedział czasu w sekundach. DDo nie naprawić FaultDomain i UpgradeDomain naruszenia ograniczeń w tym okresie po dodaniu nowego węzła. |
|ConstraintFixPartialDelayAfterNodeDown | Czas w sekundach, domyślna to 120 |Dynamiczny| Określ przedział czasu w sekundach. Czy nie naprawić FaultDomain i UpgradeDomain naruszenia ograniczeń w tym okresie po węźle szczegółów zdarzenia. |
|ConstraintViolationHealthReportLimit | Int, wartością domyślną jest 50 |Dynamiczny| Określa liczbę przypadków, gdy ograniczenia naruszenie repliki musi być trwałe Niepoprawione, zanim diagnostyki są wykonywane i raportów kondycji, są emitowane. |
|DetailedConstraintViolationHealthReportLimit | Int, wartość domyślna to 200 |Dynamiczny| Określa liczbę przypadków, gdy ograniczenia naruszenie repliki musi być trwałe Niepoprawione, zanim diagnostyki są wykonywane i szczegóły kondycji, które są emitowane raportów. |
|DetailedDiagnosticsInfoListLimit | int, domyślna to 15 |Dynamiczny| Definiuje liczbę pozycji diagnostycznych (wraz ze szczegółowymi informacjami) na ograniczenia do uwzględnienia przed obcięciem w diagnostyce.|
|DetailedNodeListLimit | int, domyślna to 15 |Dynamiczny| Określa liczbę węzłów na ograniczenia do uwzględnienia przed obcięciem w raportach nieumieszczone repliki. |
|DetailedPartitionListLimit | int, domyślna to 15 |Dynamiczny| Definiuje liczbę partycji wpisami diagnostyczne dla ograniczenia do uwzględnienia przed obcięciem w diagnostyce. |
|DetailedVerboseHealthReportLimit | Int, wartość domyślna to 200 | Dynamiczny|Określa liczbę przypadków, gdy nieumieszczone repliki musi być trwałe nieumieszczone, zanim są emitowane raportów o kondycji szczegółowe. |
|FaultDomainConstraintPriority | int, domyślna to 0 |Dynamiczny| Określa priorytet ograniczenia domeny błędów: 0: Twarde; 1: Soft; negative: Ignoruj. |
|GlobalMovementThrottleCountingInterval | Czas w sekundach, domyślna to 600 |Statyczny| Określ przedział czasu w sekundach. Wskazuje ostatnich interwał, który chcesz śledzić na przemieszczania repliki domeny (używane wraz z GlobalMovementThrottleThreshold). Może być równa 0 ignorowanie, globalne ograniczenie całkowicie. |
|GlobalMovementThrottleThreshold | Uint — wartość domyślna to 1000 |Dynamiczny| Maksymalna liczba przeniesień typu dozwolone w fazie równoważenia w ostatnim interwale wskazywanym przez GlobalMovementThrottleCountingInterval. |
|GlobalMovementThrottleThresholdForBalancing | Uint — wartość domyślna to 0 | Dynamiczny|Maksymalna liczba przeniesień typu dozwolone w fazie równoważenia w ostatnim interwale wskazywanym przez GlobalMovementThrottleCountingInterval. 0 oznacza brak limitu. |
|GlobalMovementThrottleThresholdForPlacement | Uint — wartość domyślna to 0 |Dynamiczny| Maksymalna liczba przeniesień typu dozwolone w fazie umieszczania w ostatnim interwale wskazywanym przez GlobalMovementThrottleCountingInterval.0 oznacza brak limitu.|
|GlobalMovementThrottleThresholdPercentage|Double domyślnie wynosi 0|Dynamiczny|Maksymalna liczba całkowita przeniesień dozwolone w fazach równoważenia i położenia (wyrażone jako procent łącznej liczby replik w klastrze) w ostatnim interwale wskazywanym przez GlobalMovementThrottleCountingInterval. 0 oznacza brak limitu. Jeśli ta oraz podano GlobalMovementThrottleThreshold; następnie używany jest bardziej konserwatywnego limit.|
|GlobalMovementThrottleThresholdPercentageForBalancing|Double domyślnie wynosi 0|Dynamiczny|Maksymalna liczba przeniesień typu dozwolone w fazie równoważenia, (wyrażone jako procent łącznej liczby replik w PLB) w ostatnim interwale wskazywanym przez GlobalMovementThrottleCountingInterval. 0 oznacza brak limitu. Jeśli ta oraz podano GlobalMovementThrottleThresholdForBalancing; następnie używany jest bardziej konserwatywnego limit.|
|InBuildThrottlingAssociatedMetric | ciąg, domyślna to "" |Statyczny| Skojarzone nazwę metryki dla tego ograniczania. |
|InBuildThrottlingEnabled | Wartość logiczna, wartość domyślna to false |Dynamiczny| Ustal, czy jest włączone ograniczenie w kompilacji. |
|InBuildThrottlingGlobalMaxValue | int, domyślna to 0 |Dynamiczny|Maksymalna liczba replik wbudowany dozwolone globalnie. |
|InterruptBalancingForAllFailoverUnitUpdates | Wartość logiczna, wartość domyślna to false | Dynamiczny|Określa dowolnego typu aktualizacji jednostki trybu failover powinny przerwań szybkie czy wolne równoważenia Uruchom. Przy użyciu określonych równoważenia "false", uruchom zostanie przerwana w przypadku FailoverUnit: jest tworzone lub usunąć; Brak repliki; zmienić lokalizację repliki podstawowej lub zmiany liczby replik. Równoważenie uruchamiania nie zostanie przerwana w innych przypadkach — jeśli FailoverUnit: ma dodatkowe repliki; zmienione jakakolwiek Flaga repliki; zmieniona tylko wersję partycji lub dowolnym innym przypadku. |
|MinConstraintCheckInterval | Czas w sekundach, wartość domyślna to 1 |Dynamiczny| Określ przedział czasu w sekundach. Określa minimalną ilość czasu, jaki musi minąć, zanim dwóch następujących po sobie ograniczenia Sprawdź Zaokrągla liczbę. |
|MinLoadBalancingInterval | Czas w sekundach, domyślna to 5 |Dynamiczny| Określ przedział czasu w sekundach. Określa minimalną ilość czasu, jaki musi minąć, zanim dwóch następujących po sobie równoważenia Zaokrągla liczbę. |
|MinPlacementInterval | Czas w sekundach, wartość domyślna to 1 |Dynamiczny| Określ przedział czasu w sekundach. Określa minimalną ilość czasu, jaki musi minąć, zanim dwóch następujących po sobie umieszczania Zaokrągla liczbę. |
|MoveExistingReplicaForPlacement | Wartość logiczna, wartość domyślna to true |Dynamiczny|Ustawienie, który decyduje o tym if przenieść istniejącą replikę podczas umieszczania. |
|MovementPerPartitionThrottleCountingInterval | Czas w sekundach, domyślna to 600 |Statyczny| Określ przedział czasu w sekundach. Wskazuje ostatnich interwał, który chcesz śledzić przemieszczania repliki dla każdej partycji (używane wraz z MovementPerPartitionThrottleThreshold). |
|MovementPerPartitionThrottleThreshold | Uint, wartością domyślną jest 50 |Dynamiczny| Ma związanych z równoważenia ruchu wystąpi dla partycji, jeśli liczba równoważenia powiązane przemieszczania replik partycji ma osiągnięto lub przekroczono MovementPerFailoverUnitThrottleThreshold w ostatnim interwale wskazywanym przez MovementPerPartitionThrottleCountingInterval. |
|MoveParentToFixAffinityViolation | Wartość logiczna, wartość domyślna to false |Dynamiczny| Ustawienie, który określa, czy nadrzędnego repliki można przenieść naprawić ograniczenia koligacji.|
|PartiallyPlaceServices | Wartość logiczna, wartość domyślna to true |Dynamiczny| Określa, jeśli wszystkie repliki usługi w klastrze zostaną umieszczone "wszystko lub nic" podany ograniczone odpowiednich węzłów dla nich.|
|PlaceChildWithoutParent | Wartość logiczna, wartość domyślna to true | Dynamiczny|Ustawienia, które określa, czy podrzędnych usługi repliki można umieścić w przypadku replikacji nie jest włączony. |
|PlacementConstraintPriority | int, domyślna to 0 | Dynamiczny|Określa priorytet ograniczeń umieszczania: 0: Twarde; 1: Soft; negative: Ignoruj. |
|PlacementConstraintValidationCacheSize | Int, wartość domyślna to 10000 |Dynamiczny| Ogranicza rozmiar tabeli używany do szybkiego sprawdzania poprawności i buforowanie wyrażenia ograniczeń umieszczania. |
|PlacementSearchTimeout | Czas w sekundach, wynosi 0,5 |Dynamiczny| Określ przedział czasu w sekundach. W przypadku umieszczenia usług. Wyszukaj co najwyżej to długo przed zwróceniem wyników. |
|PLBRefreshGap | Czas w sekundach, wartość domyślna to 1 |Dynamiczny| Określ przedział czasu w sekundach. Określa minimalną ilość czasu, jaki musi minąć, zanim PLB odświeża stan ponownie. |
|PreferredLocationConstraintPriority | Int, domyślna to 2| Dynamiczny|Określa priorytet ograniczenie preferowanych lokalizacji: 0: Twarde; 1: Soft; 2: Optymalizacja; ujemne: Zignoruj |
|PreferUpgradedUDs|wartość logiczna, domyślny ma wartość TRUE|Dynamiczny|Włącza włączać i wyłączać logiki, która preferuje przechodzenia do już uaktualniona domenami aktualizacji.|
|PreventTransientOvercommit | Wartość logiczna, wartość domyślna to false | Dynamiczny|Określa, powinien modułu PLB natychmiast możesz liczyć na zasoby, które zostaną zwolnione przez przenosi zainicjowane. Domyślnie; PLB można zainicjować Przenieś i overcommit przeniesienie w na tym samym węźle, który można utworzyć przejściowy. Ustawienie tego parametru na wartość true uniemożliwi tych rodzajów z overcommits defragmentacji na żądanie (zwane również placementWithMove) zostanie wyłączony. |
|ScaleoutCountConstraintPriority | int, domyślna to 0 |Dynamiczny| Określa priorytet ograniczenie liczby skalowania w poziomie: 0: Twarde; 1: Soft; negative: Ignoruj. |
|SwapPrimaryThrottlingAssociatedMetric | ciąg, domyślna to ""|Statyczny| Skojarzone nazwę metryki dla tego ograniczania. |
|SwapPrimaryThrottlingEnabled | Wartość logiczna, wartość domyślna to false|Dynamiczny| Ustal, czy jest włączone ograniczenie podstawowe wymiany. |
|SwapPrimaryThrottlingGlobalMaxValue | int, domyślna to 0 |Dynamiczny| Maksymalna liczba replik podstawowego wymiany dozwolone globalnie. |
|TraceCRMReasons |Wartość logiczna, wartość domyślna to true |Dynamiczny|Określa, czy śledzenie przyczyn CRM wystawiony przemieszczania do kanału zdarzenia operacyjne. |
|UpgradeDomainConstraintPriority | int, domyślna to 1| Dynamiczny|Określa priorytet ograniczenia domeny uaktualnień: 0: Twarde; 1: Soft; negative: Ignoruj. |
|UseMoveCostReports | Wartość logiczna, wartość domyślna to false | Dynamiczny|Powoduje, że modułu równoważenia obciążenia, aby zignorować element kosztów funkcji oceniania; Wynikowy potencjalnie dużą liczbę przenosi umieszczania lepiej o zrównoważonym obciążeniu. |
|UseSeparateSecondaryLoad | Wartość logiczna, wartość domyślna to true | Dynamiczny|Ustawienia, które określa, czy użyć różne obciążenia dodatkowej. |
|ValidatePlacementConstraint | Wartość logiczna, wartość domyślna to true |Dynamiczny| Określa, czy wyrażenie PlacementConstraint usługi sprawdzania poprawności po zaktualizowaniu ServiceDescription usługi. |
|VerboseHealthReportLimit | Int, domyślna to 20 | Dynamiczny|Określa liczbę przypadków, gdy replika musi przejść nieumieszczone, zanim ostrzeżenie kondycji jest zgłaszany go (jeśli jest włączone raportowanie stanu pełne). |

## <a name="reconfigurationagent"></a>ReconfigurationAgent

| **Parametr** | **Dozwolone wartości** | **Zasady uaktualniania** | **Wskazówki dotyczące lub krótki opis** |
| --- | --- | --- | --- |
|ApplicationUpgradeMaxReplicaCloseDuration | Czas w sekundach, domyślna to 900 |Dynamiczny|Określ przedział czasu w sekundach. Czas, dla której system będzie czekać przed zakończeniem hosty usługi, które mają repliki zostaną zablokowane w Zamknij podczas uaktualniania aplikacji.|
|FabricUpgradeMaxReplicaCloseDuration | Czas w sekundach, domyślna to 900 |Dynamiczny| Określ przedział czasu w sekundach. Czas, dla której system będzie czekać przed zakończeniem hosty usługi, które mają repliki zostaną zablokowane w Zamknij podczas uaktualniania sieci szkieletowej. |
|GracefulReplicaShutdownMaxDuration|Przedział czasu, wartością domyślną jest Common::TimeSpan::FromSeconds(120)|Dynamiczny|Określ przedział czasu w sekundach. Czas, dla której system będzie czekać przed zakończeniem hosty usługi, które mają repliki zostaną zablokowane w Zamknij. Jeśli ta wartość jest równa 0, replik nie należy zamknąć.|
|NodeDeactivationMaxReplicaCloseDuration | Czas w sekundach, domyślna to 900 |Dynamiczny|Określ przedział czasu w sekundach. Czas, dla której system będzie czekać przed zakończeniem hosty usługi, które mają repliki zostaną zablokowane w Zamknij podczas dezaktywacji węzła. |
|PeriodicApiSlowTraceInterval | Czas w sekundach, wartość domyślna to 5 minut |Dynamiczny| Określ przedział czasu w sekundach. PeriodicApiSlowTraceInterval Określa interwał, przez który wolne wywołania interfejsu API będzie ponownego wyświetlania przez monitor interfejsu API. |
|ReplicaChangeRoleFailureRestartThreshold|Int, domyślna wynosi 10|Dynamiczny| Liczba całkowita. Określ liczbę błędów interfejsu API podczas podwyższania poziomu podstawowego, po upływie którego zostaną zastosowane automatycznie ograniczenia akcji (ponowne uruchomienie repliki). |
|ReplicaChangeRoleFailureWarningReportThreshold|int, domyślna to 2147483647|Dynamiczny| Liczba całkowita. Określ liczbę błędów interfejsu API podczas podwyższania poziomu podstawowego, po którym ostrzeżenie raport o kondycji zostanie wygenerowany.|
|ServiceApiHealthDuration | Czas w sekundach, wartość domyślna to 30 minut |Dynamiczny| Określ przedział czasu w sekundach. ServiceApiHealthDuration definiuje, jak długo poczekamy dla interfejsu API usługi do uruchomienia przed możemy to zgłosić złej kondycji. |
|ServiceReconfigurationApiHealthDuration | Czas w sekundach, wartość domyślna to 30 |Dynamiczny| Określ przedział czasu w sekundach. ServiceReconfigurationApiHealthDuration definiuje, jak długo poczekamy dla interfejsu API usługi do uruchomienia przed raportu możemy złej kondycji. Dotyczy to wywołania interfejsu API, które mają wpływ na dostępność.|

## <a name="replication"></a>Replikacja
| **Parametr** | **Dozwolone wartości** | **Zasady uaktualniania**| **Wskazówki dotyczące lub krótki opis** |
| --- | --- | --- | --- |
|BatchAcknowledgementInterval|Przedział czasu, wartością domyślną jest Common::TimeSpan::FromMilliseconds(15)|Statyczny|Określ przedział czasu w sekundach. Określa ilość czasu oczekiwania przez replikatora po otrzymaniu operację przed powrotem wysyłanie potwierdzenia. Inne operacje odebranych w trakcie tego okresu będą mieć ich potwierdzeń w pojedynczym komunikacie -> zmniejszenie ruchu sieciowego, ale potencjalnie zmniejszenie przepływności replikatora.|
|MaxCopyQueueSize|uint, domyślny wynosi 1024|Statyczny|Jest to maksymalna wartość Określa początkowy rozmiar kolejki, która utrzymuje operacji replikacji. Należy pamiętać, że musi być potęgą liczby 2. Jeśli podczas wykonywania kolejki rośnie na tę operację rozmiar zostanie ograniczona między replikatorów podstawowego i pomocniczego.|
|MaxPrimaryReplicationQueueMemorySize|Uint — wartość domyślna to 0|Statyczny|Jest to maksymalna wartość kolejki podstawowej replikacji w bajtach.|
|MaxPrimaryReplicationQueueSize|uint, domyślny wynosi 1024|Statyczny|To jest maksymalna liczba operacji, które może znajdować się w kolejce podstawowej replikacji. Należy pamiętać, że musi być potęgą liczby 2.|
|MaxReplicationMessageSize|uint, domyślny jest 52428800|Statyczny|Maksymalny rozmiar komunikatu operacji replikacji. Domyślną jest 50MB.|
|MaxSecondaryReplicationQueueMemorySize|Uint — wartość domyślna to 0|Statyczny|Jest to maksymalna wartość kolejki replikacji pomocniczej w bajtach.|
|MaxSecondaryReplicationQueueSize|uint, domyślna wartość to 2048|Statyczny|To jest maksymalna liczba operacji, które może znajdować się w kolejce replikacji pomocniczej. Należy pamiętać, że musi być potęgą liczby 2.|
|QueueHealthMonitoringInterval|Przedział czasu, wartością domyślną jest Common::TimeSpan::FromSeconds(30)|Statyczny|Określ przedział czasu w sekundach. Ta wartość określa okres czasu, używane przez replikatora do monitorowania zdarzeń kondycji błąd/ostrzeżenie kolejki operacji replikacji. Wartość "0" powoduje wyłączenie monitorowania kondycji |
|QueueHealthWarningAtUsagePercent|uint — wartość domyślna to 80|Statyczny|Ta wartość określa użycia kolejki replikacji (w procentach) po upływie którego możemy zgłaszanie ostrzeżenie kolejki wysokiego użycia. Możemy to zrobić po okresie prolongaty, o QueueHealthMonitoringInterval. W przypadku użycia kolejki spada poniżej tej wartości procentowej w okresie prolongaty|
|ReplicatorAddress|ciąg, domyślną jest "localhost:0"|Statyczny|Punkt końcowy w postaci ciągu - IP:Port, który jest używany przez replikatora sieci szkieletowej Windows do nawiązywania połączeń z innymi replikami w celu wysyłania i odbierania operacji.|
|ReplicatorListenAddress|ciąg, domyślną jest "localhost:0"|Statyczny|Punkt końcowy w postaci ciągu - IP:Port, który jest używany przez replikatora Windows Service Fabric do operacji odbioru z innymi replikami.|
|ReplicatorPublishAddress|ciąg, domyślną jest "localhost:0"|Statyczny|Punkt końcowy w postaci ciągu - IP:Port, który jest używany przez replikatora Windows Fabric wysyłać operacje z innymi replikami.|
|RetryInterval|Przedział czasu, wartością domyślną jest Common::TimeSpan::FromSeconds(5)|Statyczny|Określ przedział czasu w sekundach. Podczas operacji zostanie utracone lub odrzucony ten czasomierz Określa, jak często replikatora ponowi próbę wykonania operacji wysyłania.|

## <a name="resourcemonitorservice"></a>ResourceMonitorService
| **Parametr** | **Dozwolone wartości** | **Zasady uaktualniania**| **Wskazówki dotyczące lub krótki opis** |
| --- | --- | --- | --- |
|IsEnabled|wartość logiczna, domyślna to FALSE |Statyczny|Kontroluje, czy usługa jest włączona w klastrze, czy nie. |

## <a name="runas"></a>Uruchom jako

| **Parametr** | **Dozwolone wartości** | **Zasady uaktualniania** | **Wskazówki dotyczące lub krótki opis** |
| --- | --- | --- | --- |
|RunAsAccountName |ciąg, domyślna to "" |Dynamiczny|Określa nazwę konta Uruchom jako. Jest to potrzebne tylko dla konta "DomainUser" lub "ManagedServiceAccount" typu. Prawidłowe wartości to "domena\użytkownik" lub "user@domain". |
|RunAsAccountType|ciąg, domyślna to "" |Dynamiczny|Wskazuje typ konta Uruchom jako. Jest to niezbędne dla dowolnego polecenia RunAs sekcji prawidłowe wartości to "DomainUser/NetworkService/ManagedServiceAccount/LocalSystem".|
|RunAsPassword|ciąg, domyślna to "" |Dynamiczny|Określa hasło do konta Uruchom jako. Jest to potrzebne tylko dla typu konta "DomainUser". |

## <a name="runasdca"></a>RunAs_DCA

| **Parametr** | **Dozwolone wartości** | **Zasady uaktualniania** | **Wskazówki dotyczące lub krótki opis** |
| --- | --- | --- | --- |
|RunAsAccountName |ciąg, domyślna to "" |Dynamiczny|Określa nazwę konta Uruchom jako. Jest to potrzebne tylko dla konta "DomainUser" lub "ManagedServiceAccount" typu. Prawidłowe wartości to "domena\użytkownik" lub "user@domain". |
|RunAsAccountType|ciąg, domyślna to "" |Dynamiczny|Wskazuje typ konta Uruchom jako. Jest to niezbędne dla dowolnego polecenia RunAs sekcji prawidłowe wartości to "LocalUser/DomainUser/NetworkService/ManagedServiceAccount/system lokalny". |
|RunAsPassword|ciąg, domyślna to "" |Dynamiczny|Określa hasło do konta Uruchom jako. Jest to potrzebne tylko dla typu konta "DomainUser". |

## <a name="runasfabric"></a>RunAs_Fabric

| **Parametr** | **Dozwolone wartości** | **Zasady uaktualniania** | **Wskazówki dotyczące lub krótki opis** |
| --- | --- | --- | --- |
|RunAsAccountName |ciąg, domyślna to "" |Dynamiczny|Określa nazwę konta Uruchom jako. Jest to potrzebne tylko dla konta "DomainUser" lub "ManagedServiceAccount" typu. Prawidłowe wartości to "domena\użytkownik" lub "user@domain". |
|RunAsAccountType|ciąg, domyślna to "" |Dynamiczny|Wskazuje typ konta Uruchom jako. Jest to niezbędne dla dowolnego polecenia RunAs sekcji prawidłowe wartości to "LocalUser/DomainUser/NetworkService/ManagedServiceAccount/system lokalny". |
|RunAsPassword|ciąg, domyślna to "" |Dynamiczny|Określa hasło do konta Uruchom jako. Jest to potrzebne tylko dla typu konta "DomainUser". |

## <a name="runashttpgateway"></a>RunAs_HttpGateway

| **Parametr** | **Dozwolone wartości** | **Zasady uaktualniania** | **Wskazówki dotyczące lub krótki opis** |
| --- | --- | --- | --- |
|RunAsAccountName |ciąg, domyślna to "" |Dynamiczny|Określa nazwę konta Uruchom jako. Jest to potrzebne tylko dla konta "DomainUser" lub "ManagedServiceAccount" typu. Prawidłowe wartości to "domena\użytkownik" lub "user@domain". |
|RunAsAccountType|ciąg, domyślna to "" |Dynamiczny|Wskazuje typ konta Uruchom jako. Jest to niezbędne dla dowolnego polecenia RunAs sekcji prawidłowe wartości to "LocalUser/DomainUser/NetworkService/ManagedServiceAccount/system lokalny". |
|RunAsPassword|ciąg, domyślna to "" |Dynamiczny|Określa hasło do konta Uruchom jako. Jest to potrzebne tylko dla typu konta "DomainUser". |

## <a name="security"></a>Bezpieczeństwo
| **Parametr** | **Dozwolone wartości** |**Zasady uaktualniania**| **Wskazówki dotyczące lub krótki opis** |
| --- | --- | --- | --- |
|AADCertEndpointFormat|ciąg, domyślna to ""|Statyczny|Usługi AAD certyfikat punktu końcowego formatu, domyślnej wersji Azure Commercial określonego środowiska innych niż domyślne, takie jak Azure dla instytucji rządowych "https:\//login.microsoftonline.us/{0}/federationmetadata/2007-06/federationmetadata.xml" |
|AADClientApplication|ciąg, domyślna to ""|Statyczny|Nazwa klienta natywnego w aplikacji lub identyfikator reprezentujący klientów sieci szkieletowej |
|AADClusterApplication|ciąg, domyślna to ""|Statyczny|Nazwa aplikacji interfejsu API sieci Web lub identyfikator reprezentujący klastra |
|AADLoginEndpoint|ciąg, domyślna to ""|Statyczny|Usługi AAD punkt końcowy logowania, domyślnej wersji Azure Commercial określona dla innych niż domyślne środowisko, takich jak Azure dla instytucji rządowych "https:\//login.microsoftonline.us" |
|AADTenantId|ciąg, domyślna to ""|Statyczny|Identyfikator dzierżawy (GUID) |
|AdminClientCertThumbprints|ciąg, domyślna to ""|Dynamiczny|Odciski palców certyfikatów używanych przez klientów w roli administratora. Jest to lista rozdzielonych przecinkami nazw. |
|AADTokenEndpointFormat|ciąg, domyślna to ""|Statyczny|Usługi AAD punktu końcowego tokenu, domyślnej wersji Azure Commercial określona dla innych niż domyślne środowisko, takich jak Azure dla instytucji rządowych "https:\//login.microsoftonline.us/{0}" |
|AdminClientClaims|ciąg, domyślna to ""|Dynamiczny|Wszystkie oświadczenia można oczekiwać od klientów administratora. tym samym formacie co ClientClaims; Ta lista pobiera wewnętrznie dodane do ClientClaims; więc nie trzeba również dodać ten sam wpisy do ClientClaims. |
|AdminClientIdentities|ciąg, domyślna to ""|Dynamiczny|Windows tożsamości klientów w sieci szkieletowej w roli administratora; używane do autoryzowania operacje uprzywilejowane sieci szkieletowej. Jest to rozdzielana przecinkami lista; Każdy wpis jest nazwa konta domeny lub nazwę grupy. Dla wygody; konto, na którym uruchomiono fabric.exe automatycznie przypisano rolę administratora; Dlatego jest grupy ServiceFabricAdministrators. |
|AppRunAsAccountGroupX509Folder|ciąg, domyślny jest /home/sfuser/sfusercerts |Statyczny|Folder, gdzie znajdują się AppRunAsAccountGroup X509 certyfikaty i klucze prywatne |
|CertificateExpirySafetyMargin|Przedział czasu, wartością domyślną jest Common::TimeSpan::FromMinutes(43200)|Statyczny|Określ przedział czasu w sekundach. Margines bezpieczeństwa do wygaśnięcia certyfikatu; certyfikat kondycji raportu zmiany stanu z OK na ostrzeżenie, jeśli czas wygaśnięcia jest bliżej niż to. Domyślny to 30 dni. |
|CertificateHealthReportingInterval|Przedział czasu, wartością domyślną jest Common::TimeSpan::FromSeconds(3600 * 8)|Statyczny|Określ przedział czasu w sekundach. Określ interwał raportowania stanu certyfikatu; Domyślnie 8 godzin; ustawienie na wartość 0 wyłącza raportowanie stanu certyfikatu |
|ClientCertThumbprints|ciąg, domyślna to ""|Dynamiczny|Odciski palca certyfikatów używanych przez klientów do komunikacji klastra; klaster używa to autoryzowanie połączenia przychodzącego. Jest to lista rozdzielonych przecinkami nazw. |
|ClientClaimAuthEnabled|wartość logiczna, domyślna to FALSE|Statyczny|Wskazuje, czy uwierzytelnianie za pomocą oświadczeń jest włączona na komputerach klienckich; Ustawienie true niejawnie ustawia ClientRoleEnabled. |
|ClientClaims|ciąg, domyślna to ""|Dynamiczny|Wszystkie roszczenia możliwych oczekiwanych z klientów do łączenia się z bramy. Jest to lista "Lub": ClaimsEntry \| \| ClaimsEntry \| \| ClaimsEntry... każdego ClaimsEntry znajduje się lista "I": Element ClaimType = ClaimValue & & oświadczenia = ClaimValue & & oświadczenia = ClaimValue... |
|ClientIdentities|ciąg, domyślna to ""|Dynamiczny|Windows tożsamości FabricClient; Brama nazewnictwa używa tych informacji do autoryzowania połączeń przychodzących. Jest to rozdzielana przecinkami lista; Każdy wpis jest nazwa konta domeny lub nazwę grupy. Dla wygody; konto, na którym uruchomiono fabric.exe jest automatycznie dozwolony; dlatego są grupy ServiceFabricAllowedUsers i ServiceFabricAdministrators. |
|ClientRoleEnabled|wartość logiczna, domyślna to FALSE|Statyczny|Wskazuje, czy rola klienta jest włączone; Po ustawieniu na wartość true; Klienci są przypisane role, w oparciu o ich tożsamości. W wersji 2; Włączanie oznacza to, że klient nie znajduje się w AdminClientCommonNames/AdminClientIdentities tylko można wykonać operacji tylko do odczytu. |
|ClusterCertThumbprints|ciąg, domyślna to ""|Dynamiczny|Odciski palca certyfikatów mogła dołączyć do klastra; Lista rozdzielonych przecinkami nazw. |
|ClusterCredentialType|ciąg, domyślnie jest "None"|Niedozwolone|Wskazuje typ poświadczenia zabezpieczeń używane w celu zabezpieczania klastra. Prawidłowe wartości to "Brak/X509/Windows" |
|ClusterIdentities|ciąg, domyślna to ""|Dynamiczny|Windows tożsamości węzłów klastra; używane na potrzeby autoryzacji członkostwa w klastrze. Jest to rozdzielana przecinkami lista; Każdy wpis jest nazwa konta domeny lub grupy |
|ClusterSpn|ciąg, domyślna to ""|Niedozwolone|Główna nazwa usługi klastra; gdy Service fabric może działać jako użytkownik jednej domeny (konto użytkownika domeny/gMSA). To nazwa SPN odbiorników dzierżawy i słuchaczy w fabric.exe: odbiorniki Federacji; odbiorniki replikacji wewnętrznej; środowisko uruchomieniowe usługi odbiornik i nazewnictwa odbiornika bramy. To powinno być puste podczas Service fabric może działać jako konta komputera; w takim przypadku połączenie po stronie obliczeń odbiornika SPN z adresu transportu odbiornika. |
|CrlCheckingFlag|uint, domyślny jest 0x40000000|Dynamiczny|Domyślne flagi weryfikacji łańcucha certyfikatu; może być zastąpiona przez flagę specyficzne dla składników; np. Federacji/X509CertChainFlags 0x10000000 CERT_CHAIN_REVOCATION_CHECK_END_CERT 0x20000000 CERT_CHAIN_REVOCATION_CHECK_CHAIN 0x40000000 CERT_CHAIN_REVOCATION_CHECK_CHAIN_EXCLUDE_ROOT 0x80000000 CERT_CHAIN_REVOCATION_CHECK_CACHE_ JEDYNE ustawienie na 0 spowoduje wyłączenie listy CRL sprawdzanie, czy pełną listę obsługiwanych wartości jest udokumentowany przez Flagidw CertGetCertificateChain: https://msdn.microsoft.com/library/windows/desktop/aa376078(v=vs.85).aspx |
|CrlDisablePeriod|Przedział czasu, wartością domyślną jest Common::TimeSpan::FromMinutes(15)|Dynamiczny|Określ przedział czasu w sekundach. Jak długo sprawdzanie listy CRL jest wyłączony dla danego certyfikatu po napotkaniu błędu w trybie offline; w przypadku listy CRL w trybie offline błąd można zignorować. |
|CrlOfflineHealthReportTtl|Przedział czasu, wartością domyślną jest Common::TimeSpan::FromMinutes(1440)|Dynamiczny|Określ przedział czasu w sekundach. |
|DisableFirewallRuleForDomainProfile| wartość logiczna, domyślny ma wartość TRUE |Statyczny| Wskazuje, czy reguły zapory nie powinno zostać włączone profilu domeny |
|DisableFirewallRuleForPrivateProfile| wartość logiczna, domyślny ma wartość TRUE |Statyczny| Wskazuje, czy reguły zapory nie powinno zostać włączone profil prywatny | 
|DisableFirewallRuleForPublicProfile| wartość logiczna, domyślny ma wartość TRUE | Statyczny|Wskazuje, czy reguły zapory nie powinno zostać włączone dla profilu publicznego |
|FabricHostSpn| ciąg, domyślna to "" |Statyczny| Główna nazwa usługi z elementu FabricHost; gdy Service fabric może działać jako użytkownik domeny pojedynczej (gMSA/domeny konta użytkownika) i elementu FabricHost działa w ramach konta komputera. Jest odbiornika SPN IPC FabricHost; Domyślnie powinien pozostać pusty program FabricHost działa w ramach konta komputera. |
|IgnoreCrlOfflineError|wartość logiczna, domyślna to FALSE|Dynamiczny|Czy chcesz zignorować błąd w trybie offline listy CRL w przypadku, gdy po stronie serwera sprawdza przychodzące certyfikaty klientów |
|IgnoreSvrCrlOfflineError|wartość logiczna, domyślny ma wartość TRUE|Dynamiczny|Czy chcesz zignorować błąd w trybie offline listy CRL w przypadku, gdy po stronie klienta sprawdza przychodzące certyfikaty serwera; Domyślna wartość true. Ataki za pomocą certyfikatów odwołanych serwera wymagają naruszania DNS; trudniejsze niż odwołane certyfikaty klienta. |
|ServerAuthCredentialType|ciąg, domyślnie jest "None"|Statyczny|Wskazuje typ poświadczeń zabezpieczeń do użycia w celu zabezpieczenia komunikacji między FabricClient i klastra. Prawidłowe wartości to "Brak/X509/Windows" |
|ServerCertThumbprints|ciąg, domyślna to ""|Dynamiczny|Odciski palca certyfikatów serwera używany przez klaster do komunikowania się klientów. Klienci używają tego do uwierzytelniania w klastrze. Jest to lista rozdzielonych przecinkami nazw. |
|SettingsX509StoreName| ciąg, domyślna to "MY"| Dynamiczny|X509 certyfikatu Magazyn używany przez sieć szkieletową konfiguracji ochrony |
|UseClusterCertForIpcServerTlsSecurity|wartość logiczna, domyślna to FALSE|Statyczny|Czy ma być używany certyfikat klastra do zabezpieczania IPC serwera TLS transportu jednostki |
|X509Folder|ciąg, domyślny jest /var/lib/waagent|Statyczny|Folder gdzie X509 znajdują certyfikaty i klucze prywatne |

## <a name="securityadminclientx509names"></a>Security/AdminClientX509Names

| **Parametr** | **Dozwolone wartości** | **Zasady uaktualniania** | **Wskazówki dotyczące lub krótki opis** |
| --- | --- | --- | --- |
|PropertyGroup|X509NameMap, domyślna wartość to Brak|Dynamiczny|To jest lista par "Name" i "Wartość". Każdy "Name" jest nazwa pospolita podmiotu lub DnsName X509 certyfikaty autoryzacji dla administratora operacji klienta. Dla danego "Name" "Value" jest listę oddzielaną przecinkami odciski palców certyfikatu dla wystawcy, przypinanie, jeśli nie puste, bezpośredniego wystawcę certyfikaty klienta administrator musi być na liście. |

## <a name="securityclientaccess"></a>Zabezpieczenia/ClientAccess

| **Parametr** | **Dozwolone wartości** | **Zasady uaktualniania** | **Wskazówki dotyczące lub krótki opis** |
| --- | --- | --- | --- |
|ActivateNode |ciąg, domyślna to "Admin" |Dynamiczny| Konfiguracja zabezpieczeń w celu aktywacji, gdy węzeł. |
|CancelTestCommand |ciąg, domyślna to "Admin" |Dynamiczny| Anuluje określonych TestCommand —, jeśli jest w locie. |
|CodePackageControl |ciąg, domyślna to "Admin" |Dynamiczny| Konfiguracja zabezpieczeń ponownego uruchamiania pakiety kodu. |
|CreateApplication |ciąg, domyślna to "Admin" | Dynamiczny|Konfiguracja zabezpieczeń do tworzenia aplikacji. |
|CreateComposeDeployment|ciąg, domyślna to "Admin"| Dynamiczny|Tworzy wdrożenie redagowania opisanego przez pliki narzędzia compose |
|CreateGatewayResource|ciąg, domyślna to "Admin"| Dynamiczny|Utwórz zasób bramy |
|CreateName |ciąg, domyślna to "Admin" |Dynamiczny|Konfiguracja zabezpieczeń tworzenia identyfikatora URI nazewnictwa. |
|CreateNetwork|ciąg, domyślna to "Admin" |Dynamiczny|Tworzy sieć kontenerów |
|CreateService |ciąg, domyślna to "Admin" |Dynamiczny| Konfiguracja zabezpieczeń do tworzenia usług. |
|CreateServiceFromTemplate |ciąg, domyślna to "Admin" |Dynamiczny|Konfiguracja zabezpieczeń do tworzenia usług z szablonu. |
|CreateVolume|ciąg, domyślna to "Admin"|Dynamiczny|Tworzy wolumin |
|DeactivateNode |ciąg, domyślna to "Admin" |Dynamiczny| Dezaktywacja węzła konfiguracji zabezpieczeń. |
|DeactivateNodesBatch |ciąg, domyślna to "Admin" |Dynamiczny| Konfiguracja zabezpieczeń dezaktywowanie wiele węzłów. |
|Usuwanie |ciąg, domyślna to "Admin" |Dynamiczny| Konfiguracje zabezpieczeń dla obrazu przechowywać operacji usuwania klienta. |
|DeleteApplication |ciąg, domyślna to "Admin" |Dynamiczny| Konfiguracja zabezpieczeń do usunięcia aplikacji. |
|DeleteComposeDeployment|ciąg, domyślna to "Admin"| Dynamiczny|Usuwa wdrożenie redagowania |
|DeleteGatewayResource|ciąg, domyślna to "Admin"| Dynamiczny|Usuwa zasób bramy |
|DeleteName |ciąg, domyślna to "Admin" |Dynamiczny|Konfiguracja zabezpieczeń do usunięcia z identyfikatora URI nazewnictwa. |
|DeleteNetwork|ciąg, domyślna to "Admin" |Dynamiczny|Usuwa sieć kontenerów |
|DeleteService |ciąg, domyślna to "Admin" |Dynamiczny|Konfiguracja zabezpieczeń do usunięcia usługi. |
|DeleteVolume|ciąg, domyślna to "Admin"|Dynamiczny|Usuwa woluminu.| 
|EnumerateProperties |ciąg, domyślna to "Admin\|\|użytkownika" | Dynamiczny|Konfiguracja zabezpieczeń nazewnictwa wyliczanie właściwości. |
|EnumerateSubnames |ciąg, domyślna to "Admin\|\|użytkownika" |Dynamiczny| Konfiguracja zabezpieczeń dla identyfikatora URI nazewnictwa wyliczenia. |
|FileContent |ciąg, domyślna to "Admin" |Dynamiczny| Konfiguracja zabezpieczeń dla obrazu przechowywać klienta transferu plików (zewnętrzna do klastra). |
|FileDownload |ciąg, domyślna to "Admin" |Dynamiczny| Konfiguracja zabezpieczeń rozpoczęcia pobierania pliku obrazu klienta magazynu, które zostały (zewnętrzna do klastra). |
|FinishInfrastructureTask |ciąg, domyślna to "Admin" |Dynamiczny| Konfiguracja zabezpieczeń kończenia zadań związanych z infrastrukturą. |
|GetChaosReport | ciąg, domyślna to "Admin\|\|użytkownika" |Dynamiczny| Pobiera stan Chaos w danym okresie. |
|GetClusterConfiguration | ciąg, domyślna to "Admin\|\|użytkownika" | Dynamiczny|Wywołuje GetClusterConfiguration na partycji. |
|GetClusterConfigurationUpgradeStatus | ciąg, domyślna to "Admin\|\|użytkownika" |Dynamiczny| Wywołuje GetClusterConfigurationUpgradeStatus na partycji. |
|GetFabricUpgradeStatus |ciąg, domyślna to "Admin\|\|użytkownika" |Dynamiczny| Konfiguracja zabezpieczeń sondowania stanu uaktualnienia klastra. |
|GetNodeDeactivationStatus |ciąg, domyślna to "Admin" |Dynamiczny| Trwa sprawdzanie stanu dezaktywacji konfiguracji zabezpieczeń. |
|GetNodeTransitionProgress | ciąg, domyślna to "Admin\|\|użytkownika" |Dynamiczny| Konfiguracja zabezpieczeń w celu uzyskania postęp w poleceniu Przejście węzła. |
|GetPartitionDataLossProgress | ciąg, domyślna to "Admin\|\|użytkownika" | Dynamiczny|Pobiera postęp invoke wywołanie interfejsu api utraty danych. |
|GetPartitionQuorumLossProgress | ciąg, domyślna to "Admin\|\|użytkownika" |Dynamiczny| Pobiera postęp dla wywołania interfejsu api utratę kworum invoke. |
|GetPartitionRestartProgress | ciąg, domyślna to "Admin\|\|użytkownika" |Dynamiczny| Pobiera postęp dla wywołania interfejsu api partycji ponownego uruchomienia. |
|GetSecrets|ciąg, domyślna to "Admin"|Dynamiczny|Pobierz wartości klucza tajnego |
|GetServiceDescription |ciąg, domyślna to "Admin\|\|użytkownika" |Dynamiczny| Konfiguracja zabezpieczeń dla powiadomień dotyczących usług długich sondowania i odczytywanie opisy usług. |
|GetStagingLocation |ciąg, domyślna to "Admin" |Dynamiczny| Konfiguracja zabezpieczeń dla obrazu przechowywać klienta tymczasowej lokalizacji pobierania. |
|GetStoreLocation |ciąg, domyślna to "Admin" |Dynamiczny| Konfiguracja zabezpieczeń dla obrazu przechowywać Pobieranie lokalizacji magazynu klienta. |
|GetUpgradeOrchestrationServiceState|ciąg, domyślna to "Admin"| Dynamiczny|Wywołuje GetUpgradeOrchestrationServiceState na partycji |
|GetUpgradesPendingApproval |ciąg, domyślna to "Admin" |Dynamiczny| Wywołuje GetUpgradesPendingApproval na partycji. |
|GetUpgradeStatus |ciąg, domyślna to "Admin\|\|użytkownika" |Dynamiczny| Konfiguracja zabezpieczeń sondowania stanu uaktualniania aplikacji. |
|InternalList |ciąg, domyślna to "Admin" | Dynamiczny|Konfiguracja zabezpieczeń dla obrazu przechowywać operacji listy plików klienta (wewnętrzny). |
|InvokeContainerApi|ciąg, domyślna to "Admin"|Dynamiczny|Wywołania interfejsu API kontenera |
|InvokeInfrastructureCommand |ciąg, domyślna to "Admin" |Dynamiczny| Konfiguracja zabezpieczeń infrastruktury zadań zarządzania poleceń. |
|InvokeInfrastructureQuery |ciąg, domyślna to "Admin\|\|użytkownika" | Dynamiczny|Konfiguracja zabezpieczeń podczas wykonywania zapytań zadań związanych z infrastrukturą. |
|List |ciąg, domyślna to "Admin\|\|użytkownika" | Dynamiczny|Konfiguracja zabezpieczeń dla obrazu przechowywać operacja wygenerowania listy plików klienta. |
|MoveNextFabricUpgradeDomain |ciąg, domyślna to "Admin" |Dynamiczny| Konfiguracja zabezpieczeń w przypadku wznawiania Uaktualnianie klastra za pomocą jawnego domeny uaktualnienia. |
|MoveNextUpgradeDomain |ciąg, domyślna to "Admin" |Dynamiczny| Konfiguracja zabezpieczeń w przypadku wznawiania uaktualnień aplikacji za pomocą jawnego domeny uaktualnienia. |
|MoveReplicaControl |ciąg, domyślna to "Admin" | Dynamiczny|Przenieś repliki. |
|NameExists |ciąg, domyślna to "Admin\|\|użytkownika" | Dynamiczny|Sprawdza, czy konfiguracja zabezpieczeń istnienie identyfikatora URI nazewnictwa. |
|NodeControl |ciąg, domyślna to "Admin" |Dynamiczny| Konfiguracja zabezpieczeń do uruchamiania; zatrzymywanie; i ponowne uruchamianie węzłów. |
|NodeStateRemoved |ciąg, domyślna to "Admin" |Dynamiczny| Konfiguracja zabezpieczeń dla raportowania stanu węzła usunięte. |
|Ping |ciąg, domyślna to "Admin\|\|użytkownika" |Dynamiczny| Konfiguracja zabezpieczeń klienta polecenia ping. |
|PredeployPackageToNode |ciąg, domyślna to "Admin" |Dynamiczny| Przed wdrożeniem interfejsu api. |
|PrefixResolveService |ciąg, domyślna to "Admin\|\|użytkownika" |Dynamiczny| Konfiguracja zabezpieczeń do rozpoznania prefiksu usługa oparta na zgodne. |
|PropertyReadBatch |ciąg, domyślna to "Admin\|\|użytkownika" |Dynamiczny| Operacje odczytu konfiguracji zabezpieczeń dla nazw właściwości. |
|PropertyWriteBatch |ciąg, domyślna to "Admin" |Dynamiczny|Operacje zapisu w konfiguracji zabezpieczeń dla nazw właściwości. |
|ProvisionApplicationType |ciąg, domyślna to "Admin" |Dynamiczny| Konfiguracja zabezpieczeń aprowizacji typu aplikacji. |
|ProvisionFabric |ciąg, domyślna to "Admin" |Dynamiczny| Konfiguracja zabezpieczeń tożsamości usługi Zarządzanej i/lub klastra aprowizacji manifestu. |
|Zapytanie |ciąg, domyślna to "Admin\|\|użytkownika" |Dynamiczny| Konfiguracja zabezpieczeń dla zapytań. |
|RecoverPartition |ciąg, domyślna to "Admin" | Dynamiczny|Konfiguracja zabezpieczeń służących do przywrócenia partycji. |
|RecoverPartitions |ciąg, domyślna to "Admin" | Dynamiczny|Konfiguracja zabezpieczeń służących do przywrócenia partycji. |
|RecoverServicePartitions |ciąg, domyślna to "Admin" |Dynamiczny| Konfiguracja zabezpieczeń służących do przywrócenia partycji usługi. |
|RecoverSystemPartitions |ciąg, domyślna to "Admin" |Dynamiczny| Konfiguracja zabezpieczeń służących do przywrócenia partycji usług systemowych. |
|RemoveNodeDeactivations |ciąg, domyślna to "Admin" |Dynamiczny| Konfiguracja zabezpieczeń Przywracanie dezaktywacji na wielu węzłach. |
|ReportFabricUpgradeHealth |ciąg, domyślna to "Admin" |Dynamiczny| Konfiguracja zabezpieczeń w przypadku wznawiania Uaktualnianie klastra za pomocą Bieżący postęp uaktualnienia. |
|ReportFault |ciąg, domyślna to "Admin" |Dynamiczny| Raportowanie błędów konfiguracji zabezpieczeń. |
|ReportHealth |ciąg, domyślna to "Admin" |Dynamiczny| Konfiguracja zabezpieczeń dla raportowania kondycji. |
|ReportUpgradeHealth |ciąg, domyślna to "Admin" |Dynamiczny| Konfiguracja zabezpieczeń w przypadku wznawiania uaktualnień aplikacji za pomocą Bieżący postęp uaktualnienia. |
|ResetPartitionLoad |ciąg, domyślna to "Admin\|\|użytkownika" |Dynamiczny| Konfiguracja zabezpieczeń resetowania obciążenia na potrzeby failoverUnit. |
|ResolveNameOwner |ciąg, domyślna to "Admin\|\|użytkownika" | Dynamiczny|Konfiguracja zabezpieczeń rozpoznawania identyfikatora URI nazewnictwa właściciela. |
|ResolvePartition |ciąg, domyślna to "Admin\|\|użytkownika" | Dynamiczny|Konfiguracja zabezpieczeń rozpoznawania usług systemowych. |
|ResolveService |ciąg, domyślna to "Admin\|\|użytkownika" |Dynamiczny| Konfiguracja zabezpieczeń dla usługi zgodne rozwiązania. |
|ResolveSystemService|ciąg, domyślna to "Admin\|\|użytkownika"|Dynamiczny| Konfiguracja zabezpieczeń rozpoznawania usług systemowych |
|RollbackApplicationUpgrade |ciąg, domyślna to "Admin" |Dynamiczny| Wycofywanie uaktualnienia aplikacji konfiguracji zabezpieczeń. |
|RollbackFabricUpgrade |ciąg, domyślna to "Admin" |Dynamiczny| Konfiguracja zabezpieczeń wycofywanie uaktualnienia klastra. |
|ServiceNotifications |ciąg, domyślna to "Admin\|\|użytkownika" |Dynamiczny| Konfiguracja zabezpieczeń dla powiadomień dotyczących usług opartych na zdarzeniach. |
|SetUpgradeOrchestrationServiceState|ciąg, domyślna to "Admin"| Dynamiczny|Wywołuje SetUpgradeOrchestrationServiceState na partycji |
|StartApprovedUpgrades |ciąg, domyślna to "Admin" |Dynamiczny| Wywołuje StartApprovedUpgrades na partycji. |
|StartChaos |ciąg, domyślna to "Admin" |Dynamiczny| Uruchamia Chaos — Jeśli nie jest już uruchomiona. |
|StartClusterConfigurationUpgrade |ciąg, domyślna to "Admin" |Dynamiczny| Wywołuje StartClusterConfigurationUpgrade na partycji. |
|StartInfrastructureTask |ciąg, domyślna to "Admin" | Dynamiczny|Konfiguracja zabezpieczeń do uruchamiania zadań związanych z infrastrukturą. |
|StartNodeTransition |ciąg, domyślna to "Admin" |Dynamiczny| Konfiguracja zabezpieczeń do uruchamiania przechodzenia węzłów. |
|StartPartitionDataLoss |ciąg, domyślna to "Admin" |Dynamiczny| Powoduje utratę danych na partycji. |
|StartPartitionQuorumLoss |ciąg, domyślna to "Admin" |Dynamiczny| Wywołuje utraciła kworum na partycji. |
|StartPartitionRestart |ciąg, domyślna to "Admin" |Dynamiczny| Jednoczesne ponowne uruchomienie niektórych lub wszystkich replik partycji. |
|StopChaos |ciąg, domyślna to "Admin" |Dynamiczny| Zatrzymuje Chaos —, jeśli została ona uruchomiona. |
|ToggleVerboseServicePlacementHealthReporting | ciąg, domyślna to "Admin\|\|użytkownika" |Dynamiczny| Konfiguracja zabezpieczeń przełączanie pełne ServicePlacement HealthReporting. |
|UnprovisionApplicationType |ciąg, domyślna to "Admin" |Dynamiczny| Konfiguracja zabezpieczeń cofnięcie aprowizacji typu aplikacji. |
|UnprovisionFabric |ciąg, domyślna to "Admin" |Dynamiczny| Konfiguracja zabezpieczeń tożsamości usługi Zarządzanej i/lub klastra manifestu cofnięcie aprowizacji. |
|UnreliableTransportControl |ciąg, domyślna to "Admin" |Dynamiczny| Zawodne Transport do dodawania i usuwania zachowania. |
|UpdateService |ciąg, domyślna to "Admin" |Dynamiczny|Konfiguracja zabezpieczeń dla aktualizacji usługi. |
|UpgradeApplication |ciąg, domyślna to "Admin" |Dynamiczny| Konfiguracja zabezpieczeń dla uruchamiania lub przerywania uaktualnieniami aplikacji. |
|UpgradeComposeDeployment|ciąg, domyślna to "Admin"| Dynamiczny|Uaktualnienie wdrożenia compose |
|UpgradeFabric |ciąg, domyślna to "Admin" |Dynamiczny| Konfiguracja zabezpieczeń do uruchamiania uaktualnienia klastra. |
|Upload |ciąg, domyślna to "Admin" | Dynamiczny|Konfiguracja zabezpieczeń dla obrazu przechowywać operacji przekazywania klienta. |

## <a name="securityclientcertificateissuerstores"></a>Security/ClientCertificateIssuerStores

| **Parametr** | **Dozwolone wartości** | **Zasady uaktualniania** | **Wskazówki dotyczące lub krótki opis** |
| --- | --- | --- | --- |
|PropertyGroup|IssuerStoreKeyValueMap, domyślna wartość to Brak |Dynamiczny|X509 magazyny certyfikatów wystawcy certyfikatów klienta; Nazwa = clientIssuerCN; Wartość = rozdzielana przecinkami lista magazynów |

## <a name="securityclientx509names"></a>Zabezpieczenia/ClientX509Names

| **Parametr** | **Dozwolone wartości** | **Zasady uaktualniania** | **Wskazówki dotyczące lub krótki opis** |
| --- | --- | --- | --- |
|PropertyGroup|X509NameMap, domyślna wartość to Brak|Dynamiczny|To jest lista par "Name" i "Wartość". Każdy "Name" jest nazwa pospolita podmiotu lub DnsName X509 certyfikaty autoryzacji dla operacji klienta. Dla danego "Name" "Value" jest listę oddzielaną przecinkami odciski palców certyfikatu dla wystawcy, przypinanie, jeśli nie puste, bezpośredniego wystawcę certyfikaty klienta musi być na liście.|

## <a name="securityclustercertificateissuerstores"></a>Security/ClusterCertificateIssuerStores

| **Parametr** | **Dozwolone wartości** | **Zasady uaktualniania** | **Wskazówki dotyczące lub krótki opis** |
| --- | --- | --- | --- |
|PropertyGroup|IssuerStoreKeyValueMap, domyślna wartość to Brak |Dynamiczny|X509 magazyny certyfikatów wystawcy certyfikatów klastra; Nazwa = clusterIssuerCN; Wartość = rozdzielana przecinkami lista magazynów |

## <a name="securityclusterx509names"></a>Security/ClusterX509Names

| **Parametr** | **Dozwolone wartości** | **Zasady uaktualniania** | **Wskazówki dotyczące lub krótki opis** |
| --- | --- | --- | --- |
|PropertyGroup|X509NameMap, domyślna wartość to Brak|Dynamiczny|To jest lista par "Name" i "Wartość". Każdy "Name" jest nazwa pospolita podmiotu lub DnsName X509 certyfikaty autoryzacji dla operacji klastra. Dla danego "Name" "Value" jest listę oddzielaną przecinkami odciski palców certyfikatu dla wystawcy, przypinanie, jeśli nie puste, bezpośredniego wystawcę certyfikatów klastra musi być na liście.|

## <a name="securityservercertificateissuerstores"></a>Security/ServerCertificateIssuerStores

| **Parametr** | **Dozwolone wartości** | **Zasady uaktualniania** | **Wskazówki dotyczące lub krótki opis** |
| --- | --- | --- | --- |
|PropertyGroup|IssuerStoreKeyValueMap, domyślna wartość to Brak |Dynamiczny|X509 magazyny certyfikatów wystawcy dla certyfikatów serwera; Nazwa = serverIssuerCN; Wartość = rozdzielana przecinkami lista magazynów |

## <a name="securityserverx509names"></a>Security/ServerX509Names

| **Parametr** | **Dozwolone wartości** | **Zasady uaktualniania** | **Wskazówki dotyczące lub krótki opis** |
| --- | --- | --- | --- |
|PropertyGroup|X509NameMap, domyślna wartość to Brak|Dynamiczny|To jest lista par "Name" i "Wartość". Każdy "Name" jest nazwa pospolita podmiotu lub DnsName X509 certyfikaty autoryzacji dla operacji serwera. Dla danego "Name" "Value" jest listę oddzielaną przecinkami odciski palców certyfikatu dla wystawcy, przypinanie, jeśli nie puste, bezpośredniego wystawcę certyfikaty serwera musi być na liście.|

## <a name="setup"></a>Konfigurowanie

| **Parametr** | **Dozwolone wartości** | **Zasady uaktualniania** | **Wskazówki dotyczące lub krótki opis** |
| --- | --- | --- | --- |
|ContainerNetworkName|ciąg, domyślna to ""| Statyczny |Nazwa sieciowa do użycia podczas konfigurowania sieci kontenera.|
|ContainerNetworkSetup|wartość logiczna, domyślna to FALSE| Statyczny |Określa, czy sieć kontenera.|
|FabricDataRoot |String | Niedozwolone |Katalog główny danych usługi Service Fabric. Domyślne dla systemu Azure jest d:\svcfab |
|FabricLogRoot |String | Niedozwolone |Katalog główny aplikacji usługi Service fabric dziennika. Jest to, gdzie umieścić SF dzienniki i dane śledzenia. |
|NodesToBeRemoved|ciąg, domyślna to ""| Dynamiczny |Węzły, które powinny zostać usunięte w ramach konfiguracji uaktualnienia. (Tylko w przypadku autonomicznych wdrożeniach)|
|ServiceRunAsAccountName |String | Niedozwolone |Nazwa konta, pod którym chcesz uruchamiać usługę hosta sieci szkieletowej. |
|SkipContainerNetworkResetOnReboot|wartość logiczna, domyślna to FALSE|NotAllowed|Określa, czy pominąć Resetowanie kontenera sieci przy ponownym uruchamianiu.|
|SkipFirewallConfiguration |Wartość logiczna, wartość domyślna to false | Niedozwolone |Określa ustawienia zapory muszą być ustawiony przez system, czy nie. Dotyczy to tylko wtedy, gdy używasz zapory systemu windows. Jeśli używasz innej zapory, następnie należy otworzyć porty dla systemu i aplikacji do używania |

## <a name="tokenvalidationservice"></a>TokenValidationService

| **Parametr** | **Dozwolone wartości** | **Zasady uaktualniania** | **Wskazówki dotyczące lub krótki opis** |
| --- | --- | --- | --- |
|Dostawcy |ciąg, domyślną jest "DSTS" |Statyczny|Rozdzielana przecinkami lista dostawców weryfikacji tokenu, aby włączyć (prawidłowych dostawców usług są: DSTS; AAD). Obecnie w dowolnym momencie można włączyć tylko jednego dostawcę. |

## <a name="traceetw"></a>Trace/Etw.

| **Parametr** | **Dozwolone wartości** | **Zasady uaktualniania** | **Wskazówki dotyczące lub krótki opis** |
| --- | --- | --- | --- |
|Poziom |Int, domyślna to 4 | Dynamiczny |Poziom funkcji etw śledzenia może przyjmować wartości 1, 2, 3, 4. Obsługiwany poziom śledzenia należy pozostawić u 4 |

## <a name="transactionalreplicator"></a>Replikator transakcyjny

| **Parametr** | **Dozwolone wartości** | **Zasady uaktualniania** | **Wskazówki dotyczące lub krótki opis** |
| --- | --- | --- | --- |
|BatchAcknowledgementInterval | Czas w sekundach, wartość domyślna to 0,015 | Statyczny | Określ przedział czasu w sekundach. Określa ilość czasu oczekiwania przez replikatora po otrzymaniu operację przed powrotem wysyłanie potwierdzenia. Inne operacje odebranych w trakcie tego okresu będą mieć ich potwierdzeń w pojedynczym komunikacie -> zmniejszenie ruchu sieciowego, ale potencjalnie zmniejszenie przepływności replikatora. |
|MaxCopyQueueSize |Uint, wartością domyślną jest 16384 | Statyczny |Jest to maksymalna wartość Określa początkowy rozmiar kolejki, która utrzymuje operacji replikacji. Należy pamiętać, że musi być potęgą liczby 2. Jeśli podczas wykonywania kolejki rośnie na tę operację rozmiar zostanie ograniczona między replikatorów podstawowego i pomocniczego. |
|MaxPrimaryReplicationQueueMemorySize |Uint — wartość domyślna to 0 | Statyczny |Jest to maksymalna wartość kolejki podstawowej replikacji w bajtach. |
|MaxPrimaryReplicationQueueSize |Uint, wartością domyślną jest 8192 | Statyczny |To jest maksymalna liczba operacji, które może znajdować się w kolejce podstawowej replikacji. Należy pamiętać, że musi być potęgą liczby 2. |
|MaxReplicationMessageSize |uint, domyślny jest 52428800 | Statyczny | Maksymalny rozmiar komunikatu operacji replikacji. Domyślną jest 50MB. |
|MaxSecondaryReplicationQueueMemorySize |Uint — wartość domyślna to 0 | Statyczny |Jest to maksymalna wartość kolejki replikacji pomocniczej w bajtach. |
|MaxSecondaryReplicationQueueSize |Uint, wartością domyślną jest 16384 | Statyczny |To jest maksymalna liczba operacji, które może znajdować się w kolejce replikacji pomocniczej. Należy pamiętać, że musi być potęgą liczby 2. |
|ReplicatorAddress |ciąg, domyślną jest "localhost:0" | Statyczny | Punkt końcowy w postaci ciągu - IP:Port, który jest używany przez replikatora sieci szkieletowej Windows do nawiązywania połączeń z innymi replikami w celu wysyłania i odbierania operacji. |

## <a name="transport"></a>Transport
| **Parametr** | **Dozwolone wartości** |**Zasady uaktualniania** |**Wskazówki dotyczące lub krótki opis** |
| --- | --- | --- | --- |
|ConnectionOpenTimeout|Przedział czasu, wartością domyślną jest Common::TimeSpan::FromSeconds(60)|Statyczny|Określ przedział czasu w sekundach. Limit czasu konfigurowania połączenia przychodzące i zgłaszać po stronie (w tym zabezpieczeń negocjacji w trybie bezpiecznym) |
|FrameHeaderErrorCheckingEnabled|wartość logiczna, domyślny ma wartość TRUE|Statyczny|Domyślne ustawienie dla błąd podczas sprawdzania w nagłówku ramki w trybie — Zabezpieczanie; ustawienie składnika zastępuje to. |
|MessageErrorCheckingEnabled|wartość logiczna, domyślna to FALSE|Statyczny|Domyślne ustawienie dla sprawdzanie błędów dla nagłówka wiadomości oraz treść w trybie — Zabezpieczanie; ustawienie składnika zastępuje to. |
|ResolveOption|ciąg, domyślna to "nieokreślone"|Statyczny|Określa, jak są rozwiązywane nazwy FQDN.  Prawidłowe wartości to "nie określono tego parametru/ipv4/ipv6". |
|SendTimeout|Przedział czasu, wartością domyślną jest Common::TimeSpan::FromSeconds(300)|Dynamiczny|Określ przedział czasu w sekundach. Limitu czasu wykrywania zablokowane połączenia wysyłania. Raporty o awarii TCP nie są wiarygodne niektóre środowiska. To może być konieczne można dostosować zgodnie z dostępną przepustowość sieci i rozmiar danych wychodzących (\*MaxMessageSize\/\*SendQueueSizeLimit). |

## <a name="upgradeorchestrationservice"></a>UpgradeOrchestrationService

| **Parametr** | **Dozwolone wartości** | **Zasady uaktualniania** | **Wskazówki dotyczące lub krótki opis** |
| --- | --- | --- | --- |
|AutoupgradeEnabled | Wartość logiczna, wartość domyślna to true |Statyczny| Automatyczne sondowania i na podstawie pliku stan docelowy akcji uaktualniania. |
|AutoupgradeInstallEnabled|wartość logiczna, domyślna to FALSE|Statyczny|Automatyczne sondowania, inicjowanie obsługi administracyjnej i zainstaluj kod uaktualnienia akcji na podstawie pliku stan docelowy.|
|GoalStateExpirationReminderInDays|int, domyślna to 30|Statyczny|Ustawia liczbę pozostałych dni, po upływie których mają być wyświetlane przypomnienie o stanie celem.|
|MinReplicaSetSize |int, domyślna to 0 |Statyczny |MinReplicaSetSize dla UpgradeOrchestrationService.|
|PlacementConstraints | ciąg, domyślna to "" |Statyczny| PlacementConstraints dla UpgradeOrchestrationService. |
|QuorumLossWaitDuration | Czas w sekundach, wartość domyślna to MaxValue |Statyczny| Określ przedział czasu w sekundach. QuorumLossWaitDuration dla UpgradeOrchestrationService. |
|ReplicaRestartWaitDuration | Czas w sekundach, wartość domyślna to 60 minut|Statyczny| Określ przedział czasu w sekundach. ReplicaRestartWaitDuration dla UpgradeOrchestrationService. |
|StandByReplicaKeepDuration | Czas w sekundach, domyślna to 60*24*7 minut |Statyczny| Określ przedział czasu w sekundach. StandByReplicaKeepDuration dla UpgradeOrchestrationService. |
|TargetReplicaSetSize |int, domyślna to 0 |Statyczny |TargetReplicaSetSize dla UpgradeOrchestrationService. |
|UpgradeApprovalRequired | Wartość logiczna, wartość domyślna to false | Statyczny|Ustawienie, aby uaktualnianie kodu, które wymagają zgody administratora, przed kontynuowaniem. |

## <a name="upgradeservice"></a>UpgradeService

| **Parametr** | **Dozwolone wartości** | **Zasady uaktualniania** | **Wskazówki dotyczące lub krótki opis** |
| --- | --- | --- | --- |
|BaseUrl | ciąg, domyślna to "" |Statyczny|BaseUrl dla UpgradeService. |
|ClusterId | ciąg, domyślna to "" |Statyczny|ClusterId dla UpgradeService. |
|CoordinatorType | ciąg, domyślną jest "WUTest"|Niedozwolone|CoordinatorType dla UpgradeService. |
|MinReplicaSetSize | Int, domyślna to 2 |Niedozwolone| MinReplicaSetSize dla UpgradeService. |
|OnlyBaseUpgrade | Wartość logiczna, wartość domyślna to false |Dynamiczny|OnlyBaseUpgrade dla UpgradeService. |
|PlacementConstraints |ciąg, domyślna to "" |Niedozwolone|PlacementConstraints dla uaktualnienie usługi. |
|PollIntervalInSeconds|Przedział czasu, wartością domyślną jest Common::TimeSpan::FromSeconds(60) |Dynamiczny|Określ przedział czasu w sekundach. Interwał sondowania UpgradeService operacji zarządzania ARM. |
|TargetReplicaSetSize | Int, domyślna to 3 |Niedozwolone| TargetReplicaSetSize dla UpgradeService. |
|TestCabFolder | ciąg, domyślna to "" |Statyczny| TestCabFolder dla UpgradeService. |
|X509FindType | ciąg, domyślna to ""|Dynamiczny| X509FindType dla UpgradeService. |
|X509FindValue | ciąg, domyślna to "" |Dynamiczny| X509FindValue dla UpgradeService. |
|X509SecondaryFindValue | ciąg, domyślna to "" |Dynamiczny| X509SecondaryFindValue dla UpgradeService. |
|X509StoreLocation | ciąg, domyślna to "" |Dynamiczny| X509StoreLocation for UpgradeService. |
|X509StoreName | ciąg, domyślnie jest "Mój"|Dynamiczny|X509StoreName dla UpgradeService. |

## <a name="next-steps"></a>Kolejne kroki
Aby uzyskać więcej informacji, zobacz [Uaktualnij konfigurację klastra usługi Azure](service-fabric-cluster-config-upgrade-azure.md) i [uaktualnić konfiguracji klastra autonomicznego](service-fabric-cluster-config-upgrade-windows-server.md).
