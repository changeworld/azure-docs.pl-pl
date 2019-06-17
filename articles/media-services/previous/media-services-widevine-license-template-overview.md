---
title: Omówienie szablonu licencji Widevine | Dokumentacja firmy Microsoft
description: Ten temat zawiera omówienie szablonu licencji Widevine, który jest używany do konfigurowania licencji Widevine.
author: juliako
manager: femila
editor: ''
services: media-services
documentationcenter: ''
ms.assetid: 0e6f1f05-7ed6-4ed6-82a0-0cc2182b075a
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/20/2019
ms.author: juliako
ms.openlocfilehash: d0bb72361e1bff3615f6785ac4c91a10ea773498
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60825544"
---
# <a name="widevine-license-template-overview"></a>Omówienie szablonu licencji Widevine 
Aby skonfigurować i żądania licencji Google Widevine, można użyć usługi Azure Media Services. Gdy gracz próbuje odtwarzanie zawartości chronionej przez technologię Widevine, żądanie jest wysyłane do usługi dostarczania licencji uzyskanie licencji. Jeśli usługa licencji zatwierdza żądanie, problemy z usługą licencji. Ona są wysyłane do klienta i jest używany do odszyfrowania i odtwarzania określonej zawartości.

Żądania licencji Widevine są sformatowane jako komunikat JSON.  

>[!NOTE]
> Można utworzyć pustego komunikatu bez wartości, po prostu "{}." Szablon licencji jest tworzona przy użyciu ustawień domyślnych. Domyślnie działa w przypadku większości przypadków. Scenariusze dostarczania licencji firmy Microsoft należy zawsze używać wartości domyślne. Jeśli musisz ustawić wartości "provider" i "content_id" dostawca muszą pasować do poświadczeń Widevine.

    {  
       “payload”:“<license challenge>”,
       “content_id”: “<content id>” 
       “provider”: ”<provider>”
       “allowed_track_types”:“<types>”,
       “content_key_specs”:[  
          {  
             “track_type”:“<track type 1>”
          },
          {  
             “track_type”:“<track type 2>”
          },
          …
       ],
       “policy_overrides”:{  
          “can_play”:<can play>,
          “can persist”:<can persist>,
          “can_renew”:<can renew>,
          “rental_duration_seconds”:<rental duration>,
          “playback_duration_seconds”:<playback duration>,
          “license_duration_seconds”:<license duration>,
          “renewal_recovery_duration_seconds”:<renewal recovery duration>,
          “renewal_server_url”:”<renewal server url>”,
          “renewal_delay_seconds”:<renewal delay>,
          “renewal_retry_interval_seconds”:<renewal retry interval>,
          “renew_with_usage”:<renew with usage>
       }
    }

## <a name="json-message"></a>Komunikat JSON
| Name (Nazwa) | Wartość | Opis |
| --- | --- | --- |
| ładunek |Ciąg kodowany w formacie Base64 |Żądanie dotyczące licencji wysłane przez klienta. |
| content_id |Ciąg kodowany w formacie Base64 |Identyfikator używany do uzyskania kluczowych identyfikator i zawartości klucza dla każdego content_key_specs.track_type. |
| Dostawcy |string |Używane w celu wyszukania zawartości kluczy i zasad. Jeśli dostarczania kluczy firmy Microsoft jest używana do dostarczania licencji Widevine, ten parametr jest ignorowany. |
| nazwa_zasady |string |Nazwa zasady wcześniej zarejestrowany. Opcjonalny. |
| allowed_track_types |Wyliczenia |SD_ONLY lub SD_HD. Formanty, które zawartości klucze są uwzględnione w licencji. |
| content_key_specs |Tablica JSON struktur, zobacz sekcję "Specyfikacje klucza zawartości."  |Bardziej szczegółowej kontroli na które kluczy zawartości do zwrócenia. Aby uzyskać więcej informacji zobacz sekcję "Specyfikacje klucza zawartości." Można określić tylko jedną z wartości allowed_track_types i content_key_specs. |
| use_policy_overrides_exclusively |Wartość logiczna PRAWDA lub FAŁSZ |Używanie atrybutów zasady określone przez policy_overrides i pominąć wszystkie zasady wcześniej przechowywanych. |
| policy_overrides |JSON struktury, zobacz sekcję "Zasady przesłonięcia". |Ustawienia zasad dla tej licencji.  W przypadku, gdy ten zasób ma wstępnie zdefiniowanych zasad, te określone wartości są używane. |
| session_init |JSON struktury, zobacz sekcję "Inicjowanie sesji." |Opcjonalne dane są przekazywane do licencji. |
| parse_only |Wartość logiczna PRAWDA lub FAŁSZ |Żądanie licencji jest analizowany, ale wystawiono żadna licencja. Jednak wartości z żądania licencji są zwracane w odpowiedzi. |

## <a name="content-key-specs"></a>Specyfikacje klucza zawartości
Jeśli istnieje istniejących zasad, nie ma potrzeby określić jedną z wartości w użycie klucza zawartości. Istniejących zasad skojarzonych z tą zawartością służy do określenia ochrona danych wyjściowych, takich jak wysokiej przepustowości ochrony zawartości cyfrowej (HDCP) i systemu zarządzania ogólne (CGMS) kopiowania. Jeśli serwer licencji Widevine istniejących zasad nie jest zarejestrowany, dostawcy zawartości może wprowadzać wartości w żądaniu licencji.   

Każda wartość content_key_specs należy określić dla wszystkich ścieżek, niezależnie od opcji use_policy_overrides_exclusively. 

| Name (Nazwa) | Wartość | Opis |
| --- | --- | --- |
| content_key_specs. track_type |string |Nazwa typu ścieżki. Jeśli content_key_specs jest określony w żądaniu licencji, upewnij się, do określania, że wszystkie typy śledzenia jawnie. Niewykonanie tej czynności powoduje niepowodzenie odtwarzania ostatnie 10 sekund. |
| content_key_specs  <br/> security_level |uint32 |Określa wymagania dotyczące niezawodności klienta do odtwarzania. <br/> Oprogramowania oparte na kryptografii biały pole jest wymagane. <br/> — Kryptografia oprogramowanie i zaciemnionego dekodera są wymagane. <br/> Kluczowych operacji materiał i kryptografii muszą być wykonywane w środowisku sprzętowej zaufanych wykonywania. <br/> Kryptografii i dekodowania zawartości muszą być wykonywane w środowisku sprzętowej zaufanych wykonywania.  <br/> Kryptografii dekodowanie i obsługę wszystkich nośnika (skompresowanym i nieskompresowanym formatem) muszą być obsługiwane w środowisku sprzętowej zaufanych wykonywania. |
| content_key_specs <br/> required_output_protection.hdc |string, one of HDCP_NONE, HDCP_V1, HDCP_V2 |Wskazuje, czy HDCP jest wymagana. |
| content_key_specs <br/>key |Base64 —<br/>Ciąg zakodowany |Klucz zawartości do użycia dla tej ścieżki. Jeśli zostanie określony, track_type lub key_id jest wymagany. Dostawcy zawartości można użyć tej opcji do dodania klucza zawartości dla tej ścieżki samodzielny serwer licencji Widevine wygenerować lub wyszukać klucz. |
| content_key_specs.key_id |Plik binarny z ciągu zakodowanego algorytmem Base64, 16 bajtów |Unikatowy identyfikator klucza. |

## <a name="policy-overrides"></a>Zastąpienia zasad
| Name (Nazwa) | Wartość | Opis |
| --- | --- | --- |
| policy_overrides. can_play |Wartość logiczna PRAWDA lub FAŁSZ |Wskazuje, że odtwarzanie zawartości jest dozwolone. Wartość domyślna to false. |
| policy_overrides. can_persist |Wartość logiczna PRAWDA lub FAŁSZ |Wskazuje, czy licencja może być utrwalony na nieulotnej pamięci masowej w trybie offline. Wartość domyślna to false. |
| policy_overrides. can_renew |Wartość logiczna PRAWDA lub FAŁSZ |Wskazuje, czy może odnowienia tej licencji. W przypadku opcji true, czas trwania licencji można rozszerzyć przez pulsu. Wartość domyślna to false. |
| policy_overrides. license_duration_seconds |int64 |Określa przedział czasu dla tej konkretnej licencji. Wartość 0 wskazuje, że nie ma żadnego limitu czasu trwania. Domyślna to 0. |
| policy_overrides. rental_duration_seconds |int64 |Określa przedział czasu, podczas odtwarzania jest dozwolone. Wartość 0 wskazuje, że nie ma żadnego limitu czasu trwania. Domyślna to 0. |
| policy_overrides. playback_duration_seconds |int64 |Wyświetlanie okna czas po rozpoczęciu w trakcie trwania licencji. Wartość 0 wskazuje, że nie ma żadnego limitu czasu trwania. Domyślna to 0. |
| policy_overrides. renewal_server_url |string |Wszystkie żądania pulsu (odnowienie) dla tej licencji są kierowane do określonego adresu URL. To pole jest używane tylko wtedy, gdy can_renew ma wartość true. |
| policy_overrides. renewal_delay_seconds |int64 |Ile sekund po license_start_time, zanim odnawianie najpierw zostanie podjęta. To pole jest używane tylko wtedy, gdy can_renew ma wartość true. Domyślna to 0. |
| policy_overrides. renewal_retry_interval_seconds |int64 |Określa opóźnienie w ciągu kilku sekund między żądaniami odnowy kolejnych licencji w przypadku awarii. To pole jest używane tylko wtedy, gdy can_renew ma wartość true. |
| policy_overrides. renewal_recovery_duration_seconds |int64 |Okno czasu, w których odtwarzania można kontynuować, gdy podejmowana jest próba odnowienia jeszcze się niepowodzeniem z powodu problemów z zaplecza z serwerem licencji. Wartość 0 wskazuje, że nie ma żadnego limitu czasu trwania. To pole jest używane tylko wtedy, gdy can_renew ma wartość true. |
| policy_overrides. renew_with_usage |Wartość logiczna PRAWDA lub FAŁSZ |Wskazuje, że po uruchomieniu użycia licencji jest wysyłany do odnowienia. To pole jest używane tylko wtedy, gdy can_renew ma wartość true. |

## <a name="session-initialization"></a>Inicjowanie sesji
| Name (Nazwa) | Wartość | Opis |
| --- | --- | --- |
| provider_session_token |Ciąg kodowany w formacie Base64 |Ten token sesji jest przekazywany w licencji, a istnieje w kolejnych odnowienia. Tokenu sesji nie jest zachowana po przekroczeniu sesji. |
| provider_client_token |Ciąg kodowany w formacie Base64 |Klient token do wysłania w odpowiedzi licencji. Jeśli żądanie licencji zawiera token klienta, ta wartość jest ignorowana. Token klienta będzie występować dłużej niż sesje licencji. |
| override_provider_client_token |Wartość logiczna PRAWDA lub FAŁSZ |Jeśli wartość FAŁSZ i żądania licencji zawiera token klienta, należy użyć tokenu z żądania, nawet, jeśli token klienta został określony w tej strukturze. W przypadku opcji true zawsze używać tokenu określone w tej strukturze. |

## <a name="configure-your-widevine-licenses-by-using-net-types"></a>Skonfigurować licencje Widevine przy użyciu typów .NET
Usługa Media Services udostępnia interfejsy API platformy .NET, którego można użyć, aby skonfigurować licencje Widevine. 

### <a name="classes-as-defined-in-the-media-services-net-sdk"></a>Klasy, zgodnie z definicją w Media Services .NET SDK
Następujące klasy są definicje typów:

    public class WidevineMessage
    {
        public WidevineMessage();

        [JsonProperty(NullValueHandling = NullValueHandling.Ignore)]
        public AllowedTrackTypes? allowed_track_types { get; set; }
        [JsonProperty(NullValueHandling = NullValueHandling.Ignore)]
        public ContentKeySpecs[] content_key_specs { get; set; }
        [JsonProperty(NullValueHandling = NullValueHandling.Ignore)]
        public object policy_overrides { get; set; }
    }

    [JsonConverter(typeof(StringEnumConverter))]
    public enum AllowedTrackTypes
    {
        SD_ONLY = 0,
        SD_HD = 1
    }
    public class ContentKeySpecs
    {
        public ContentKeySpecs();

        [JsonProperty(NullValueHandling = NullValueHandling.Ignore)]
        public string key_id { get; set; }
        [JsonProperty(NullValueHandling = NullValueHandling.Ignore)]
        public RequiredOutputProtection required_output_protection { get; set; }
        [JsonProperty(NullValueHandling = NullValueHandling.Ignore)]
        public int? security_level { get; set; }
        [JsonProperty(NullValueHandling = NullValueHandling.Ignore)]
        public string track_type { get; set; }
    }

    public class RequiredOutputProtection
    {
        public RequiredOutputProtection();

        public Hdcp hdcp { get; set; }
    }

    [JsonConverter(typeof(StringEnumConverter))]
    public enum Hdcp
    {
        HDCP_NONE = 0,
        HDCP_V1 = 1,
        HDCP_V2 = 2
    }

### <a name="example"></a>Przykład
Poniższy przykład pokazuje, jak skonfigurować prostą licencji Widevine przy użyciu interfejsów API platformy .NET:

    private static string ConfigureWidevineLicenseTemplate()
    {
        var template = new WidevineMessage
        {
            allowed_track_types = AllowedTrackTypes.SD_HD,
            content_key_specs = new[]
            {
                new ContentKeySpecs
                {
                    required_output_protection = new RequiredOutputProtection { hdcp = Hdcp.HDCP_NONE},
                    security_level = 1,
                    track_type = "SD"
                }
            },
            policy_overrides = new
            {
                can_play = true,
                can_persist = true,
                can_renew = false
            }
        };

        string configuration = JsonConvert.SerializeObject(template);
        return configuration;
    }


## <a name="media-services-learning-paths"></a>Ścieżki szkoleniowe dotyczące usługi Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Przekazywanie opinii
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="see-also"></a>Zobacz także
[Używanie dynamicznego szyfrowania Common Encryption w usługach PlayReady i Widevine](media-services-protect-with-playready-widevine.md)

