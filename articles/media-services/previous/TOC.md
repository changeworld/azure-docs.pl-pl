# [Dokumentacja usługi Media Services](index.md)

# [Omówienie](media-services-overview.md)
## [Scenarios and availability (Scenariusze i dostępność)](scenarios-and-availability.md)
## [Pojęcia](media-services-concepts.md)

# Rozpoczęcie pracy
## [Tworzenie konta i zarządzanie nim](media-services-portal-create-account.md)
## [Konfigurowanie środowiska deweloperskiego](media-services-set-up-computer.md)
### [.NET](media-services-dotnet-how-to-use.md)
### [REST](media-services-rest-how-to-use.md)  
## [Używanie uwierzytelniania usługi AAD do uzyskiwania dostępu do interfejsu API](media-services-use-aad-auth-to-access-ams-api.md)
### [Zarządzanie uwierzytelnianiem usługi AAD za pomocą portalu](media-services-portal-get-started-with-aad.md)
### [Dostęp do interfejsu API za pomocą platformy .NET](media-services-dotnet-get-started-with-aad.md)
### [Dostęp do interfejsu API za pomocą architektury REST](media-services-rest-connect-with-aad.md)
### [Tworzenie i konfigurowanie aplikacji usługi AAD za pomocą interfejsu wiersza polecenia platformy Azure](media-services-cli-create-and-configure-aad-app.md)
### [Tworzenie i konfigurowanie aplikacji usługi AAD za pomocą programu PowerShell](media-services-powershell-create-and-configure-aad-app.md)

## Dostarczanie wideo na żądanie
### [Azure Portal](media-services-portal-vod-get-started.md)
### [Zestaw SDK platformy .NET](media-services-dotnet-get-started.md)
### [Java](media-services-java-how-to-use.md)
### [REST](media-services-rest-get-started.md)
## Transmisja strumieniowa na żywo
### [Azure Portal](media-services-portal-live-passthrough-get-started.md)
### [.NET](media-services-dotnet-live-encode-with-onpremises-encoders.md)

# Instrukcje
## Zarządzanie
### Jednostki
#### [.NET](media-services-dotnet-manage-entities.md)
#### [REST](media-services-rest-manage-entities.md)
### [Punkty końcowe przesyłania strumieniowego](media-services-streaming-endpoints-overview.md)
#### [Azure Portal](media-services-portal-manage-streaming-endpoints.md)
#### [.NET](media-services-dotnet-manage-streaming-endpoints.md)
### Magazyn
#### [Aktualizowanie usługi Media Services po stopniowym uaktualnieniu kluczy dostępu do magazynu](media-services-roll-storage-access-keys.md)
#### [Zarządzanie elementami zawartości na wielu kontach magazynu](meda-services-managing-multiple-storage-accounts.md)
### [Limity przydziału i ograniczenia](media-services-quotas-and-limitations.md)
## [Konfigurowanie programu Postman](media-rest-apis-with-postman.md)
### [Kolekcja przesyłania strumieniowego na żądanie](postman-collection.md)
### [Kolekcja przesyłania strumieniowego na żywo](postman-live-streaming-collection.md)
### [Środowisko](postman-environment.md)
## Przekazywanie zawartości
### Przekazywanie plików do konta
#### [Azure Portal](media-services-portal-upload-files.md)
#### [.NET](media-services-dotnet-upload-files.md)
#### [REST](media-services-rest-upload-files.md)
### [Przekazywanie dużych plików za pomocą rozwiązania Aspera](media-services-upload-files-with-aspera.md)
### [Przekazywanie plików za pomocą usługi StorSimple](media-services-upload-files-from-storsimple.md)
### [Kopiowanie istniejących obiektów blob](media-services-copying-existing-blob.md)

## [Kodowanie zawartości](media-services-encode-asset.md)
### [Porównanie koderów](media-services-compare-encoders.md)
### [Zarządzanie szybkością i współbieżnością kodowania](media-services-manage-encoding-speed.md)
### Media Encoder Standard (MES)
#### [Formaty i kodeki narzędzia Media Encoder Standard](media-services-media-encoder-standard-formats.md)
#### [Automatyczne generowanie drabiny szybkości transmisji bitów za pomocą usługi MES](media-services-autogen-bitrate-ladder-with-mes.md)
#### Kodowanie za pomocą usługi Media Encoder Standard
##### [Azure Portal](media-services-portal-encode.md)
##### [.NET](media-services-dotnet-encode-with-media-encoder-standard.md)
##### [REST](media-services-rest-encode-asset.md)
#### [Zaawansowane kodowanie za pomocą usługi MES](media-services-advanced-encoding-with-mes.md)
##### [Dostosowywanie ustawień wstępnych usługi Media Encoder Standard](media-services-custom-mes-presets-with-dotnet.md)
##### [Generowanie miniatur przy użyciu usługi Media Encoder Standard za pomocą platformy .NET](media-services-dotnet-generate-thumbnail-with-mes.md)
##### [Przycinanie wideo za pomocą usługi Media Encoder Standard](media-services-crop-video.md)
#### Schematy usługi MES
##### [Schemat usługi Media Encoder Standard](media-services-mes-schema.md)
##### [Metadane wejściowe](media-services-input-metadata-schema.md)
##### [Metadane wyjściowe](media-services-output-metadata-schema.md)
#### [Ustawienia wstępne usługi MES](media-services-mes-presets-overview.md) 
##### [Wielokrotna szybkość transmisji bitów H264 1080p Audio 5.1](media-services-mes-preset-H264-Multiple-Bitrate-1080p-Audio-5.1.md)
##### [Wielokrotna szybkość transmisji bitów H264 1080p](media-services-mes-preset-H264-Multiple-Bitrate-1080p.md)
##### [Wielokrotna szybkość transmisji bitów H264 16x9 SD Audio 5.1](media-services-mes-preset-H264-Multiple-Bitrate-16x9-SD-Audio-5.1.md)
##### [Wielokrotna szybkość transmisji bitów H264 16x9 SD](media-services-mes-preset-H264-Multiple-Bitrate-16x9-SD.md)
##### [Wielokrotna szybkość transmisji bitów H264 16x9 dla systemu iOS](media-services-mes-preset-H264-Multiple-Bitrate-16x9-for-iOS.md)
##### [Wielokrotna szybkość transmisji bitów H264 4K Audio 5.1](media-services-mes-preset-H264-Multiple-Bitrate-4K-Audio-5.1.md)
##### [Wielokrotna szybkość transmisji bitów H264 4K](media-services-mes-preset-H264-Multiple-Bitrate-4K.md)
##### [Wielokrotna szybkość transmisji bitów H264 4x3 SD Audio 5.1](media-services-mes-preset-H264-Multiple-Bitrate-4x3-SD-Audio-5.1.md)
##### [Wielokrotna szybkość transmisji bitów H264 4x3 SD](media-services-mes-preset-H264-Multiple-Bitrate-4x3-SD.md)
##### [Wielokrotna szybkość transmisji bitów H264 4x3 dla systemu iOS](media-services-mes-preset-H264-Multiple-Bitrate-4x3-for-iOS.md)
##### [Wielokrotna szybkość transmisji bitów H264 720p Audio 5.1](media-services-mes-preset-H264-Multiple-Bitrate-720p-Audio-5.1.md)
##### [Wielokrotna szybkość transmisji bitów H264 720p](media-services-mes-preset-H264-Multiple-Bitrate-720p.md)
##### [Pojedyncza szybkość transmisji bitów H264 1080p Audio 5.1](media-services-mes-preset-H264-Single-Bitrate-1080p-Audio-5.1.md)
##### [Pojedyncza szybkość transmisji bitów H264 1080p](media-services-mes-preset-H264-Single-Bitrate-1080p.md)
##### [Pojedyncza szybkość transmisji bitów H264 16x9 SD Audio 5.1](media-services-mes-preset-H264-Single-Bitrate-16x9-SD-Audio-5.1.md)
##### [Pojedyncza szybkość transmisji bitów H264 16x9 SD](media-services-mes-preset-H264-Single-Bitrate-16x9-SD.md)
##### [Pojedyncza szybkość transmisji bitów H264 4K Audio 5.1](media-services-mes-preset-H264-Single-Bitrate-4K-Audio-5.1.md)
##### [Pojedyncza szybkość transmisji bitów H264 4K](media-services-mes-preset-H264-Single-Bitrate-4K.md)
##### [Pojedyncza szybkość transmisji bitów H264 4x3 SD Audio 5.1](media-services-mes-preset-H264-Single-Bitrate-4x3-SD-Audio-5.1.md)
##### [Pojedyncza szybkość transmisji bitów H264 4x3 SD](media-services-mes-preset-H264-Single-Bitrate-4x3-SD.md)
##### [Pojedyncza szybkość transmisji bitów H264 720p Audio 5.1](media-services-mes-preset-H264-Single-Bitrate-720p-Audio-5.1.md)
##### [Pojedyncza szybkość transmisji bitów H264 720p](media-services-mes-preset-H264-Single-Bitrate-720p.md)
##### [Pojedyncza szybkość transmisji bitów H264 720p dla systemu Android](media-services-mes-preset-H264-Single-Bitrate-720p-for-Android.md)
##### [Pojedyncza szybkość transmisji bitów H264 SD wysokiej jakości dla systemu Android](media-services-mes-preset-H264-Single-Bitrate-High-Quality-SD-for-Android.md)
##### [Pojedyncza szybkość transmisji bitów H264 SD niskiej jakości dla systemu Android](media-services-mes-preset-H264-Single-Bitrate-Low-Quality-SD-for-Android.md)
### Przepływ pracy usługi Media Encoder w warstwie Premium
#### [Formaty i kodeki usługi Media Encoder Premium Workflow](media-services-premium-workflow-encoder-formats.md)
#### Kodowanie za pomocą przepływu pracy usługi Media Encoder Premium
##### [Przepływ pracy usługi Media Encoder w warstwie Premium](media-services-encode-with-premium-workflow.md)
##### [Samouczki dotyczące przepływu pracy usługi Media Encoder w warstwie Premium](media-services-media-encoder-premium-workflow-tutorials.md)
##### [Tworzenie zaawansowanych przepływów pracy kodowania za pomocą projektanta przepływu pracy](media-services-workflow-designer.md)
##### [Przepływ pracy w warstwie Premium z wieloma danymi wejściowymi](media-services-media-encoder-premium-workflow-multiplefilesinput.md)
### [Tworzenie zadania generującego fragmenty fMP4](media-services-generate-fmp4-chunks.md)
### Procesory multimediów
#### [.NET](media-services-get-media-processor.md)
#### [REST](media-services-rest-get-media-processor.md)
### [Kody błędów](media-services-encoding-error-codes.md)
### Przestarzałe
#### [Statyczne tworzenie pakietów i szyfrowanie](media-services-static-packaging.md)

## [Przesyłanie strumieniowe na żywo](media-services-manage-channels-overview.md)
### [Kodery lokalne](media-services-live-streaming-with-onprem-encoders.md)
#### [Zalecane kodery lokalne](media-services-recommended-encoders.md)
#### [Azure Portal](media-services-portal-live-passthrough-get-started.md)
#### [.NET](media-services-dotnet-live-encode-with-onpremises-encoders.md)
### [Transmisja strumieniowa na żywo z użyciem kodera w chmurze](media-services-manage-live-encoder-enabled-channels.md)
#### [Azure Portal](media-services-portal-creating-live-encoder-enabled-channel.md)
#### [.NET](media-services-dotnet-creating-live-encoder-enabled-channel.md)
### [Konfigurowanie koderów lokalnych do użycia z koderem w chmurze](media-services-live-encoders-overview.md)
#### [Koder Elemental Live](media-services-configure-elemental-live-encoder.md)
#### [Koder FMLE](media-services-configure-fmle-live-encoder.md)
#### [Koder Haivision KB](media-services-configure-kb-live-encoder.md)
#### [Koder NewTek TriCaster](media-services-configure-tricaster-live-encoder.md)
#### [Koder Wirecast](media-services-configure-wirecast-live-encoder.md)
### [Obsługa operacji długotrwałych](media-services-dotnet-long-operations.md)
### [Specyfikacja odbierania podzielonej zawartości na żywo w formacie MP4](media-services-fmp4-live-ingest-overview.md)

## [Przycinanie zawartości](media-services-azure-media-clipper-overview.md)
### [Rozpoczęcie pracy](media-services-azure-media-clipper-getting-started.md)
### [Ładowanie wideo](media-services-azure-media-clipper-load-assets.md)
### [Konfigurowanie skrótów klawiaturowych](media-services-azure-media-clipper-keyboard-shortcuts.md)
### [Konfigurowanie lokalizacji](media-services-azure-media-clipper-localization.md)
### [Przesyłanie zadań przycinania](media-services-azure-media-clipper-submit-job.md)
### [Azure Portal](media-services-azure-media-clipper-portal.md)

## [Ochrona zawartości](media-services-content-protection-overview.md)
### [Szyfrowanie magazynu](media-services-rest-storage-encryption.md)
### [Szyfrowanie AES-128](media-services-protect-with-aes128.md)
### [Technologia PlayReady/Widevine do przesyłania strumieniowego](media-services-protect-with-playready-widevine.md)
### [Technologia FairPlay do przesyłania strumieniowego](media-services-protect-hls-with-fairplay.md)
### [Technologia PlayReady w trybie offline dla systemu Windows 10](https://blogs.msdn.microsoft.com/playready4/2016/10/26/does-azure-media-services-support-offline-mode/)
### [Technologia FairPlay w trybie offline dla systemu iOS](media-services-protect-hls-with-offline-fairplay.md)
### [Technologia Widevine w trybie offline dla systemu Android](offline-widevine-for-android.md)
### [Konfigurowanie w witrynie Azure Portal](media-services-portal-protect-content.md)
### [Dostarczanie licencji DRM](media-services-deliver-keys-and-licenses.md)
### Tworzenie kluczy zawartości
#### [.NET](media-services-dotnet-create-contentkey.md)
#### [REST](media-services-rest-create-contentkey.md)
### Omówienie szablonów licencji
#### [Szablon licencji PlayReady](media-services-playready-license-template-overview.md)
#### [Szablon licencji Widevine](media-services-widevine-license-template-overview.md)
### Konfigurowanie zasad dostarczania elementów zawartości
#### [.NET](media-services-dotnet-configure-asset-delivery-policy.md)
#### [REST](media-services-rest-configure-asset-delivery-policy.md)
### Konfigurowanie zasad autoryzacji klucza zawartości
#### [Azure Portal](media-services-portal-configure-content-key-auth-policy.md)
#### [.NET](media-services-dotnet-configure-content-key-auth-policy.md)
#### [REST](media-services-rest-configure-content-key-auth-policy.md)
### [Przekazywanie tokenów uwierzytelniania do usług AMS](media-services-pass-authentication-tokens.md)
### Projekty referencyjne
#### [Projekt hybrydowego systemu DRM](hybrid-design-drm-sybsystem.md)
#### [Referencyjny projekt z wieloma systemami DRM](media-services-cenc-with-multidrm-access-control.md)

## [Analiza](media-services-analytics-overview.md)
### [Analizowanie multimediów za pomocą witryny Azure Portal](media-services-portal-analyze.md)
### [Przetwarzanie za pomocą indeksatora w wersji 2](media-services-process-content-with-indexer2.md)
### [Przetwarzanie za pomocą indeksatora](media-services-index-content.md)
#### [Task preset (Ustawienie wstępne zadania)](indexer-task-preset.md)
### [Przetwarzanie za pomocą usługi Hyperlapse](media-services-hyperlapse-content.md)
### [Przetwarzanie za pomocą usługi Face Detector](media-services-face-and-emotion-detection.md)
### [Przetwarzanie za pomocą usługi Motion Detector](media-services-motion-detection.md)
### [Przetwarzanie za pomocą usługi Face Redactor](media-services-face-redaction.md)
#### [Przewodnik po usłudze Face Redactor](media-services-redactor-walkthrough.md)
### [Przetwarzanie za pomocą miniatur wideo](media-services-video-summarization.md)
### [Przetwarzanie za pomocą oprogramowania OCR](media-services-video-optical-character-recognition.md)
### [Przetwarzanie za pomocą pakietu Content Moderator](media-services-content-moderation.md)

## [Konfigurowanie telemetrii](media-services-telemetry-overview.md)
###[.NET](media-services-dotnet-telemetry.md)
###[REST](media-services-rest-telemetry.md)

## Skalowanie
### [Przetwarzanie multimediów](media-services-scale-media-processing-overview.md)
#### [Azure Portal](media-services-portal-scale-media-processing.md)
#### [.NET](media-services-dotnet-encoding-units.md)
### Punkty końcowe przesyłania strumieniowego
#### [Azure Portal](media-services-portal-scale-streaming-endpoints.md)

## [Dostarczanie zawartości](media-services-deliver-content-overview.md)
### [Dynamiczne tworzenie pakietów](media-services-dynamic-packaging-overview.md)
### [Omówienie filtrów i manifestów dynamicznych](media-services-dynamic-manifest-overview.md)
#### [Tworzenie filtrów za pomocą programu .NET](media-services-dotnet-dynamic-manifest.md)
#### [Tworzenie filtrów za pomocą wzorca REST](media-services-rest-dynamic-manifest.md)
### [Zasady buforowania usługi CDN w rozszerzeniu usług Media Services](../../cdn/cdn-caching-policy.md?toc=%2fazure%2fmedia-services%2ftoc.json)
### Publikowanie zawartości
#### [Azure Portal](media-services-portal-publish.md)
#### [.NET](media-services-deliver-streaming-content.md)
#### [REST](media-services-rest-deliver-streaming-content.md)
### [Dostarczanie przez pobieranie](media-services-deliver-asset-download.md)
### [Scenariusz transmisji strumieniowej w trybie failover](media-services-implement-failover.md)

## Używanie
### [Odtwarzanie multimediów za pomocą istniejących odtwarzaczy](media-services-playback-content-with-existing-players.md)
### [Odtwarzanie multimediów za pomocą odtwarzacza Media Player](media-services-develop-video-players.md)
### Inne opcje odtwarzania
#### [Przesyłanie aplikacji do Sklepu Windows przy użyciu funkcji Smooth Streaming](media-services-build-smooth-streaming-apps.md)
#### [Aplikacja HTML5 z implementacją DASH.js](media-services-embed-mpeg-dash-in-html5.md)
#### [Odtwarzacze w technologii Open Source Media Framework firmy Adobe](media-services-use-osmf-smooth-streaming-client-plugin.md)
### [Wstawianie reklam po stronie klienta](media-services-inserting-ads-on-client-side.md)
### [Licencjonowanie zestawu Microsoft Smooth Streaming Client Porting Kit](media-services-sspk.md)

## Integracja
### [Używanie usługi Azure Functions z usługą Media Services](media-services-dotnet-how-to-use-azure-functions.md)
### [Przykłady używania usługi Azure Functions z usługą Media Services](https://github.com/Azure-Samples/media-services-dotnet-functions-integration)

## Monitorowanie
### Sprawdzanie postępu zadania
#### [REST](media-services-rest-check-job-progress.md)
#### [Azure Portal](media-services-portal-check-job-progress.md)
#### [.NET](media-services-check-job-progress.md)
### [Monitorowanie powiadomień dotyczących zadań przy użyciu magazynu kolejek](media-services-dotnet-check-job-progress-with-queues.md)
### [Monitorowanie powiadomień dotyczących zadań przy użyciu elementów webhook](media-services-dotnet-check-job-progress-with-webhooks.md)

## Rozwiązywanie problemów
### [Często zadawane pytania](media-services-frequently-asked-questions.md)
### [Przewodnik rozwiązywania problemów z transmisją strumieniową na żywo](media-services-troubleshooting-live-streaming.md)
### [Kody błędów](media-services-error-codes.md)
### [Logika ponowień](media-services-retry-logic-in-dotnet-sdk.md)

# Dokumentacja
## [Przykłady kodu](https://azure.microsoft.com/resources/samples/?service=media-services)
## [Azure PowerShell (Resource Manager)](/powershell/module/azurerm.media)
## [Azure PowerShell (zarządzanie usługami)](/powershell/module/azure/?view=azuresmps-3.7.0)
## [.NET](/dotnet/api/microsoft.windowsazure.mediaservices.client)
## [REST](/rest/api/media/mediaservice)
## Specyfikacje
### [Pozyskiwanie na żywo — specyfikacja pozyskiwania na żywo podzielonej zawartości w formacie MP4](media-services-fmp4-live-ingest-overview.md)
### [Pozyskiwanie na żywo — sygnalizowanie metadanych czasowych w transmisji strumieniowej na żywo](media-services-specifications-live-timed-metadata.md)
### [Smooth Streaming — HEVC](media-services-specifications-ms-sstr-amendment-hevc.md)

# Zasoby
## [Społeczność usługi Azure Media Services](media-services-community.md)
## [Harmonogram działania dla platformy Azure](https://azure.microsoft.com/roadmap/?category=web-mobile)
## [Cennik](https://azure.microsoft.com/pricing/details/media-services/)
## [Kalkulator cen](https://azure.microsoft.com/pricing/calculator/)
## [Informacje o wersji](media-services-release-notes.md)
## [Wideo](https://azure.microsoft.com/resources/videos/index/?services=media-services)
