# [Dokumentacja usługi CDN](index.md)

# [Omówienie](cdn-overview.md)
## [Co to jest Azure CDN?](../best-practices-cdn.md?toc=%2fazure%2fcdn%2ftoc.json)

# Rozpoczęcie pracy
## [Włączanie usługi Azure CDN](cdn-create-new-endpoint.md)

# Instrukcje
## Integracja
### [Web Apps](../app-service/app-service-web-tutorial-content-delivery-network.md?toc=%2fazure%2fcdn%2ftoc.json)
### [Cloud Services](cdn-cloud-service-with-cdn.md)
### Magazyn
#### [Integrate a storage account (Integrowanie konta magazynu)](cdn-create-a-storage-account-with-cdn.md)
#### [SAS storage support (Magazyn SAS — pomoc techniczna)](cdn-sas-storage-support.md)
### [Współużytkowanie zasobów między źródłami](cdn-cors.md)
### [Dodawanie domeny niestandardowej do punktu końcowego usługi CDN](cdn-map-content-to-custom-domain.md)
### [Konfigurowanie protokołu HTTPS w domenie niestandardowej](cdn-custom-ssl.md)
## Optymalizowanie zawartości
### [Omówienie optymalizacji](cdn-optimization-overview.md)
####[Optymalizacja dużych plików](cdn-large-file-optimization.md)
####[Optymalizacja przesyłania strumieniowego multimediów](cdn-media-streaming-optimization.md)
####[Przyspieszanie witryn dynamicznych](cdn-dynamic-site-acceleration.md)
 
## Zarządzanie
### [Zarządzanie przy użyciu programu Azure PowerShell](cdn-manage-powershell.md)
### [Ograniczanie dostępu według kraju](cdn-restrict-access-by-country.md)
### [Poprawianie wydajności poprzez kompresowanie plików](cdn-improve-performance.md)
### Sterowanie zachowaniem buforowania
#### [Jak działa buforowanie](cdn-how-caching-works.md)
#### [Sterowanie zachowaniem buforowania przy użyciu reguł buforowania](cdn-caching-rules.md)
#### Buforowanie zawartości według ciągów zapytania
##### [Warstwa standardowa](cdn-query-string.md)
##### [Warstwa Premium](cdn-query-string-premium.md)
#### [Czyszczenie elementów zawartości zapisanych w pamięci podręcznej](cdn-purge-endpoint.md)
#### [Wstępne ładowanie elementów zawartości zapisanych w pamięci podręcznej](cdn-preload-endpoint.md)
### Konfigurowanie czasu wygaśnięcia
#### [Zawartość sieci Web platformy Azure](cdn-manage-expiration-of-cloud-service-content.md)
#### [Azure Blob Storage](cdn-manage-expiration-of-blob-content.md)
### [Uwierzytelnianie przy użyciu tokenów](cdn-token-auth.md)
### [Monitorowanie zasobów](cdn-resource-health.md)
### [Zastępowanie zachowania przy użyciu reguł](cdn-rules-engine.md)
### [Uzyskiwanie alertów w czasie rzeczywistym](cdn-real-time-alerts.md)
### [Obsługa protokołu HTTP/2](cdn-http2.md)

## Analiza
### [Analizowanie wzorców użycia usługi Azure CDN](cdn-log-analysis.md)
#### [Dzienniki diagnostyczne platformy Azure](cdn-azure-diagnostic-logs.md)
#### Narzędzia do analizy dla usługi Azure CDN świadczonej przez firmę Verizon
##### [Raporty podstawowe z usługi Verizon](cdn-analyze-usage-patterns.md)
##### [Raporty niestandardowe z usługi Verizon](cdn-verizon-custom-reports.md)
#### Narzędzia do analizy dla usługi Azure CDN Premium świadczonej przez firmę Verizon
##### [Generowanie zaawansowanych raportów HTTP](cdn-advanced-http-reports.md)
##### [Wyświetlanie statystyk w czasie rzeczywistym](cdn-real-time-stats.md)
##### [Analizowanie wydajności węzła krawędzi](cdn-edge-performance.md)

## Programowanie
### [.NET](cdn-app-dev-net.md)
### [Node.js](cdn-app-dev-node.md)

## Rozwiązywanie problemów
### [Stan 404](cdn-troubleshoot-endpoint.md)
### [Kompresja plików](cdn-troubleshoot-compression.md)

# Dokumentacja
##  [Dokumentacja aparatu reguł](cdn-rules-engine-reference.md)
### [Wyrażenia warunkowe aparatu reguł](cdn-rules-engine-reference-conditional-expressions.md)
### [Warunki dopasowań aparatu reguł](cdn-rules-engine-reference-match-conditions.md)
### [Funkcje aparatu reguł](cdn-rules-engine-reference-features.md)
## [Przykłady kodu](https://azure.microsoft.com/en-us/resources/samples/?service=cdn)
## [Azure PowerShell](/powershell/module/azurerm.cdn)
## [.NET](/dotnet/api/microsoft.azure.management.cdn)
## [Java](/java/api/com.microsoft.azure.management.cdn)
## [REST](/rest/api/cdn/)

# Zasoby
## Lokalizacje POP usługi Azure CDN
### [Lokalizacje POP według regionu](cdn-pop-locations.md)
### [Lokalizacje POP według skrótu](cdn-pop-abbreviations.md)
## [Harmonogram działania dla platformy Azure](https://azure.microsoft.com/roadmap/)
## [Forum MSDN](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurecdn)
## [Cennik](https://azure.microsoft.com/pricing/details/cdn/)
## [Kalkulator cen](https://azure.microsoft.com/pricing/calculator/)
## [Aktualizacje usług](https://azure.microsoft.com/updates/?product=cdn)
## [Stack Overflow](http://stackoverflow.com/questions/tagged/azure-cdn)
## [Wideo](https://azure.microsoft.com/documentation/videos/index/?services=cdn)

