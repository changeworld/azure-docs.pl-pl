---
title: Omówienie szablonu licencji Widevine | Dokumentacja firmy Microsoft
description: Ten temat zawiera omówienie szablonu licencji Widevine, który jest używany do konfigurowania licencji Widevine.
author: juliako
manager: cfowler
editor: ''
services: media-services
documentationcenter: ''
ms.assetid: 0e6f1f05-7ed6-4ed6-82a0-0cc2182b075a
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/29/2017
ms.author: juliako
ms.openlocfilehash: eefe82eb022584029b7afb0f2c3524d400c700bd
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/07/2018
ms.locfileid: "33790482"
---
# <a name="widevine-license-template-overview"></a>Omówienie szablonu licencji Widevine
Azure Media Services służy do konfigurowania i żądają licencji Google Widevine. Próbą odtwarzanie zawartości chronionej przez Widevine, żądanie jest wysyłane do usługi dostarczania licencji uzyskanie licencji. Jeśli usługa licencji zatwierdza żądanie, usługa wystawia licencję. Są wysyłane do klienta, a służy do odszyfrowania i odtwarzać określonej zawartości.

Żądania licencji Widevine jest w formacie JSON wiadomości.  

>[!NOTE]
> Można utworzyć pusty bez wartości, po prostu "{}." Szablon licencji jest tworzona przy użyciu ustawień domyślnych. Ustawienie domyślne działa w większości przypadków. Scenariusze dostarczania licencji firmy Microsoft należy zawsze używać wartości domyślne. Jeśli musisz ustawić wartości "dostawca" i "content_id" dostawcy muszą pasować do poświadczeń Widevine.

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
| ładunek |Ciąg kodowany w formacie Base64 |Żądanie licencji wysłane przez klienta. |
| content_id |Ciąg kodowany w formacie Base64 |Identyfikator używany do uzyskania Identyfikatora klucza i zawartości kluczy dla każdego content_key_specs.track_type. |
| Dostawcy |ciąg |Umożliwia wyszukiwanie zasad i zawartości kluczy. Jeśli dostawy klucza firmy Microsoft jest używane w celu dostarczania licencji Widevine, ten parametr zostanie zignorowany. |
| nazwa_zasady |ciąg |Nazwa zasady wcześniej zarejestrowane. Opcjonalny. |
| allowed_track_types |wyliczenia |SD_ONLY lub SD_HD. Formanty, które zawartości klucze są uwzględniane w licencji. |
| content_key_specs |Tablica JSON struktury, zobacz sekcję "Zawartości najważniejszych."  |Formant precyzyjny system w klawisze zawartości do zwrócenia. Aby uzyskać więcej informacji zobacz sekcję "Zawartości najważniejszych." Można określić tylko jeden allowed_track_types i content_key_specs wartości. |
| use_policy_overrides_exclusively |Wartość logiczną PRAWDA lub FAŁSZ |Użyj zasad atrybuty określone w policy_overrides i pominąć wszystkie zapisane wcześniej zasady. |
| policy_overrides |JSON struktury, zobacz sekcję "Zastąpienia zasad". |Ustawienia zasad dla tej licencji.  W przypadku tego zasobu ma wstępnie zdefiniowane zasady, są używane te określone wartości. |
| session_init |JSON struktury, zobacz sekcję "Inicjowanie sesji." |Opcjonalne dane są przekazywane do licencji. |
| parse_only |Wartość logiczną PRAWDA lub FAŁSZ |Żądanie licencji jest analizowana, ale żadna licencja jest wystawiony. Jednak wartości z żądania licencji są zwracane w odpowiedzi. |

## <a name="content-key-specs"></a>Specyfikacja klucza zawartości
Jeśli istnieje istniejących zasad, jest niepotrzebna, aby określić jedną z wartości w zawartości specyfikacja klucza. Istniejących zasady skojarzone z tą zawartością służy do określania ochrony danych wyjściowych, takich jak wysokiej przepustowości ochrony zawartości cyfrowej (HDCP) i System zarządzania ogólne (CGMS) kopiowania. Jeśli istniejących zasad nie jest zarejestrowana na serwerze licencji Widevine, dostawcy zawartości można wprowadzić wartości do żądania licencji.   

Należy określić wartość każdego content_key_specs dla wszystkich ścieżek, niezależnie od opcji use_policy_overrides_exclusively. 

| Name (Nazwa) | Wartość | Opis |
| --- | --- | --- |
| content_key_specs. track_type |ciąg |Nazwa typu ścieżki. Jeśli content_key_specs jest określony w żądaniu licencji, upewnij się śledzić wszystkie typy jawnie określić. Błąd w tym celu powoduje niepowodzenie do odtwarzania po 10 sekundach. |
| content_key_specs  <br/> security_level |uint32 |Definiuje wymagania dotyczące niezawodności klienta dla odtwarzania. <br/> -Opartych na oprogramowaniu kryptografii biały pole jest wymagane. <br/> -Kryptografia oprogramowania i zaciemnionego dekodera są wymagane. <br/> -Klucza materiałów i kryptografii operacje odbywa się w środowisku wykonywania zaufanych sprzętowej. <br/> Kryptografii i dekodowania zawartości muszą być wykonywane w środowisku sprzętowej zaufanych wykonywania.  <br/> Kryptografii dekodowania i obsługę wszystkich nośnika (skompresowanym i nieskompresowanym) musi być obsługiwane w środowisku wykonywania zaufanych sprzętowej. |
| content_key_specs <br/> required_output_protection.hdc |ciąg, z których jeden HDCP_V2 HDCP_NONE, HDCP_V1, |Wskazuje, czy HDCP jest wymagana. |
| content_key_specs <br/>key |Base64<br/>ciąg kodowany jako |Klucz zawartości do użycia dla tej ścieżki. Jeśli zostanie określona, track_type lub key_id jest wymagany. Dostawcy zawartości służy tę opcję, aby wprowadzić klucz zawartości dla tej ścieżki, zamiast czekać na serwerze licencji Widevine Generowanie lub wyszukiwania klucza. |
| content_key_specs.key_id |Ciąg kodowany w formacie Base64 binary, 16 bajtów |Unikatowy identyfikator dla klucza. |

## <a name="policy-overrides"></a>Zastąpienia zasad
| Name (Nazwa) | Wartość | Opis |
| --- | --- | --- |
| policy_overrides. can_play |Wartość logiczną PRAWDA lub FAŁSZ |Wskazuje, że odtwarzanie zawartości jest dozwolone. Wartość domyślna to false. |
| policy_overrides. can_persist |Wartość logiczną PRAWDA lub FAŁSZ |Wskazuje, że licencja może utrwalone nieulotnej pamięci masowej w trybie offline. Wartość domyślna to false. |
| policy_overrides. can_renew |Wartość logiczną PRAWDA lub FAŁSZ |Wskazuje, że odnowienia licencji jest dozwolone. Jeśli PRAWDA, czas trwania licencji można przedłużyć pulsu. Wartość domyślna to false. |
| policy_overrides. license_duration_seconds |int64 |Określa przedział czasu dla określonych licencji. Wartość 0 wskazuje, że nie ma żadnego limitu czasu trwania. Domyślna to 0. |
| policy_overrides. rental_duration_seconds |int64 |Określa przedział czasu, podczas odtwarzania jest dozwolone. Wartość 0 wskazuje, że nie ma żadnego limitu czasu trwania. Domyślna to 0. |
| policy_overrides. playback_duration_seconds |int64 |Wyświetlanie okna czasu po rozpoczęciu w czasie trwania licencji. Wartość 0 wskazuje, że nie ma żadnego limitu czasu trwania. Domyślna to 0. |
| policy_overrides. renewal_server_url |ciąg |Wszystkie żądania pulsu (odnowienia) licencji są kierowane do określonego adresu URL. To pole jest używane tylko wtedy, gdy can_renew ma wartość true. |
| policy_overrides. renewal_delay_seconds |int64 |Liczbę sekund po license_start_time przed próbą najpierw odnawiania. To pole jest używane tylko wtedy, gdy can_renew ma wartość true. Domyślna to 0. |
| policy_overrides. renewal_retry_interval_seconds |int64 |Określa opóźnienie w sekundach między żądań odnowienia kolejnych licencji, w razie awarii. To pole jest używane tylko wtedy, gdy can_renew ma wartość true. |
| policy_overrides. renewal_recovery_duration_seconds |int64 |Okno czasu, w których odtwarzania można kontynuować, gdy podejmowana jest próba odnowienia, ale nie powiodło się z powodu problemów z zaplecza z serwerem licencji. Wartość 0 wskazuje, że nie ma żadnego limitu czasu trwania. To pole jest używane tylko wtedy, gdy can_renew ma wartość true. |
| policy_overrides. renew_with_usage |Wartość logiczną PRAWDA lub FAŁSZ |Wskazuje, że licencja jest wysyłane do odnawiania podczas uruchamiania użycia. To pole jest używane tylko wtedy, gdy can_renew ma wartość true. |

## <a name="session-initialization"></a>Inicjowanie sesji
| Name (Nazwa) | Wartość | Opis |
| --- | --- | --- |
| provider_session_token |Ciąg kodowany w formacie Base64 |Token sesji jest przekazany z powrotem do licencji i istnieje w kolejnych odnowienia. Token sesji nie jest zachowany poza sesji. |
| provider_client_token |Ciąg kodowany w formacie Base64 |Token klienta do wysłania w odpowiedzi licencji. Jeśli żądanie licencji zawiera token klienta, ta wartość jest ignorowana. Token klienta utrzymuje poza sesji licencji. |
| override_provider_client_token |Wartość logiczną PRAWDA lub FAŁSZ |Jeśli wartość FAŁSZ i żądania licencji zawiera token klienta, użyj tokenu z żądania, nawet wtedy, gdy określono token klienta w tej struktury. Jeśli PRAWDA, należy zawsze używać tokenu określone w tej struktury. |

## <a name="configure-your-widevine-licenses-by-using-net-types"></a>Konfigurowanie licencji Widevine przy użyciu typów .NET
Usługa Media Services udostępnia interfejsów API architektury .NET, którego można użyć do skonfigurowania licencji Widevine. 

### <a name="classes-as-defined-in-the-media-services-net-sdk"></a>Klasy zgodnie z definicją w SDK .NET usługi Media Services
Następujące klasy są definicje z następujących typów:

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
Poniższy przykład pokazuje, jak skonfigurować proste licencji Widevine przy użyciu interfejsów API architektury .NET:

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
[Użyj PlayReady i Widevine dynamicznego szyfrowania common encryption](media-services-protect-with-playready-widevine.md)

