---
title: Usługa Azure Media Services przy użyciu metody Widevine licencji omówienie szablonu | Dokumentacja firmy Microsoft
description: Ten temat zawiera omówienie szablonu licencji Widevine, który jest używany do konfigurowania licencji Widevine.
author: juliako
manager: femila
editor: ''
services: media-services
documentationcenter: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/10/2019
ms.author: juliako
ms.openlocfilehash: c6fc363a7ab9de215647e371a9d3c846f8688bd5
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "61466327"
---
# <a name="widevine-license-template-overview"></a>Omówienie szablonu licencji Widevine 

Usługa Azure Media Services umożliwia szyfrowanie zawartości przy użyciu **Google Widevine**. Media Services udostępnia również usługę dostarczania licencji Widevine. Interfejsy API usług Azure Media Services umożliwiają skonfigurowanie licencji Widevine. Gdy gracz próbuje odtwarzanie zawartości chronionej przez technologię Widevine, żądania są wysyłane do usługi dostarczania licencji można uzyskać licencji. Jeśli usługa licencji zatwierdza żądanie, problemy z usługą licencji. Ona są wysyłane do klienta i jest używany do odszyfrowania i odtwarzania określonej zawartości.

Żądania licencji Widevine są sformatowane jako komunikat JSON.  

>[!NOTE]
> Można utworzyć pustego komunikatu bez wartości, po prostu "{}." Szablon licencji jest tworzona przy użyciu ustawień domyślnych. Domyślnie działa w przypadku większości przypadków. Scenariusze dostarczania licencji firmy Microsoft należy zawsze używać wartości domyślne. Jeśli musisz ustawić wartości "provider" i "content_id" dostawca muszą pasować do poświadczeń Widevine.

    {  
       "payload":"<license challenge>",
       "content_id": "<content id>"
       "provider": "<provider>"
       "allowed_track_types":"<types>",
       "content_key_specs":[  
          {  
             "track_type":"<track type 1>"
          },
          {  
             "track_type":"<track type 2>"
          },
          …
       ],
       "policy_overrides":{  
          "can_play":<can play>,
          "can persist":<can persist>,
          "can_renew":<can renew>,
          "rental_duration_seconds":<rental duration>,
          "playback_duration_seconds":<playback duration>,
          "license_duration_seconds":<license duration>,
          "renewal_recovery_duration_seconds":<renewal recovery duration>,
          "renewal_server_url":"<renewal server url>",
          "renewal_delay_seconds":<renewal delay>,
          "renewal_retry_interval_seconds":<renewal retry interval>,
          "renew_with_usage”:<renew with usage>
       }
    }

## <a name="json-message"></a>Komunikat JSON

| Name (Nazwa) | Wartość | Opis |
| --- | --- | --- |
| ładunek |Ciąg kodowany w formacie Base64 |Żądanie dotyczące licencji wysłane przez klienta. |
| content_id |Ciąg kodowany w formacie Base64 |Identyfikator używany do uzyskania kluczowych identyfikator i zawartości klucza dla każdego content_key_specs.track_type. |
| dostawca |string |Używane w celu wyszukania zawartości kluczy i zasad. Jeśli dostarczania kluczy firmy Microsoft jest używana do dostarczania licencji Widevine, ten parametr jest ignorowany. |
| nazwa_zasady |string |Nazwa zasady wcześniej zarejestrowany. Opcjonalny. |
| allowed_track_types |Wyliczenia |SD_ONLY lub SD_HD. Formanty, które zawartości klucze są uwzględnione w licencji. |
| content_key_specs |Tablica JSON struktur, zobacz sekcję "Specyfikacje klucza zawartości."  |Bardziej szczegółowej kontroli na które kluczy zawartości do zwrócenia. Aby uzyskać więcej informacji zobacz sekcję "Specyfikacje klucza zawartości." Można określić tylko jedną z wartości allowed_track_types i content_key_specs. |
| use_policy_overrides_exclusively |Wartość logiczna PRAWDA lub FAŁSZ |Używanie atrybutów zasady określone przez policy_overrides i pominąć wszystkie zasady wcześniej przechowywanych. |
| policy_overrides |JSON struktury, zobacz sekcję "Zasady przesłonięcia". |Ustawienia zasad dla tej licencji.  W przypadku, gdy ten zasób ma wstępnie zdefiniowanych zasad, te określone wartości są używane. |
| session_init |JSON struktury, zobacz sekcję "Inicjowanie sesji." |Opcjonalne dane są przekazywane do licencji. |
| parse_only |Wartość logiczna PRAWDA lub FAŁSZ |Żądanie licencji jest analizowany, ale wystawiono żadna licencja. Jednak wartości z żądania licencji są zwracane w odpowiedzi. |

## <a name="content-key-specs"></a>Specyfikacje klucza zawartości
Jeśli istnieje wstępnie istniejących zasad, nie ma potrzeby określić jedną z wartości w użycie klucza zawartości. Istniejące zasady skojarzone z tą zawartością służy do określenia ochrona danych wyjściowych, takich jak wysokiej przepustowości ochrony zawartości cyfrowej (HDCP) i systemu zarządzania ogólne (CGMS) kopiowania. Jeśli wcześniej istniejących zasad nie jest zarejestrowany serwer licencji Widevine, dostawcy zawartości może wprowadzać wartości w żądaniu licencji.   

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
| policy_overrides&#46;can_play |Wartość logiczna PRAWDA lub FAŁSZ |Wskazuje, że odtwarzanie zawartości jest dozwolone. Wartość domyślna to false. |
| policy_overrides&#46;can_persist |Wartość logiczna PRAWDA lub FAŁSZ |Wskazuje, czy licencja może być utrwalony na nieulotnej pamięci masowej w trybie offline. Wartość domyślna to false. |
| policy_overrides&#46;can_renew |Wartość logiczna PRAWDA lub FAŁSZ |Wskazuje, czy może odnowienia tej licencji. W przypadku opcji true, czas trwania licencji można rozszerzyć przez pulsu. Wartość domyślna to false. |
| policy_overrides&#46;license_duration_seconds |int64 |Określa przedział czasu dla tej konkretnej licencji. Wartość 0 wskazuje, że nie ma żadnego limitu czasu trwania. Domyślna to 0. |
| policy_overrides&#46;rental_duration_seconds |int64 |Określa przedział czasu, podczas odtwarzania jest dozwolone. Wartość 0 wskazuje, że nie ma żadnego limitu czasu trwania. Domyślna to 0. |
| policy_overrides&#46;playback_duration_seconds |int64 |Wyświetlanie okna czas po rozpoczęciu w trakcie trwania licencji. Wartość 0 wskazuje, że nie ma żadnego limitu czasu trwania. Domyślna to 0. |
| policy_overrides&#46;renewal_server_url |string |Wszystkie żądania pulsu (odnowienie) dla tej licencji jest kierowany do określonego adresu URL. To pole jest używane tylko wtedy, gdy can_renew ma wartość true. |
| policy_overrides&#46;renewal_delay_seconds |int64 |Ile sekund po license_start_time, zanim odnawianie najpierw zostanie podjęta. To pole jest używane tylko wtedy, gdy can_renew ma wartość true. Domyślna to 0. |
| policy_overrides&#46;renewal_retry_interval_seconds |int64 |Określa opóźnienie w ciągu kilku sekund między żądaniami odnowy kolejnych licencji w przypadku awarii. To pole jest używane tylko wtedy, gdy can_renew ma wartość true. |
| policy_overrides&#46;renewal_recovery_duration_seconds |int64 |Okno czasu, w których odtwarzania można kontynuować, gdy podejmowana jest próba odnowienia jeszcze się niepowodzeniem z powodu problemów z zaplecza z serwerem licencji. Wartość 0 wskazuje, że nie ma żadnego limitu czasu trwania. To pole jest używane tylko wtedy, gdy can_renew ma wartość true. |
| policy_overrides&#46;renew_with_usage |Wartość logiczna PRAWDA lub FAŁSZ |Wskazuje, że po uruchomieniu użycia licencji jest wysyłany do odnowienia. To pole jest używane tylko wtedy, gdy can_renew ma wartość true. |

## <a name="session-initialization"></a>Inicjowanie sesji
| Name (Nazwa) | Wartość | Opis |
| --- | --- | --- |
| provider_session_token |Ciąg kodowany w formacie Base64 |Ten token sesji jest przekazywany w licencji, a istnieje w kolejnych odnowienia. Tokenu sesji nie jest zachowana po przekroczeniu sesji. |
| provider_client_token |Ciąg kodowany w formacie Base64 |Klient token do wysłania w odpowiedzi licencji. Jeśli żądanie licencji zawiera token klienta, ta wartość jest ignorowana. Token klienta będzie występować dłużej niż sesje licencji. |
| override_provider_client_token |Wartość logiczna PRAWDA lub FAŁSZ |Jeśli wartość FAŁSZ i żądania licencji zawiera token klienta, należy użyć tokenu z żądania, nawet, jeśli token klienta został określony w tej strukturze. W przypadku opcji true zawsze używać tokenu określone w tej strukturze. |

## <a name="configure-your-widevine-license-with-net"></a>Konfigurowanie licencji Widevine przy użyciu platformy .NET 

Usługa Media Services udostępnia klasę, która umożliwia skonfigurowanie licencji Widevine. Aby skonstruować licencji, należy przekazać ciąg JSON do [WidevineTemplate](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.media.models.contentkeypolicywidevineconfiguration.widevinetemplate?view=azure-dotnet#Microsoft_Azure_Management_Media_Models_ContentKeyPolicyWidevineConfiguration_WidevineTemplate).

Aby skonfigurować szablon, możesz wykonywać następujące czynności:

### <a name="directly-construct-a-json-string"></a>Bezpośrednio konstruowania ciągu JSON

Ta metoda może być podatne na błędy. Zalecane jest użycie inne metody opisanej w [zdefiniuj wymagane klasy i serializować do notacji JSON](#classes).

```csharp
ContentKeyPolicyWidevineConfiguration objContentKeyPolicyWidevineConfiguration = new ContentKeyPolicyWidevineConfiguration
{
    WidevineTemplate = @"{""allowed_track_types"":""SD_HD"",""content_key_specs"":[{""track_type"":""SD"",""security_level"":1,""required_output_protection"":{""hdcp"":""HDCP_V2""}}],""policy_overrides"":{""can_play"":true,""can_persist"":true,""can_renew"":false}}"
};
```

### <a id="classes"></a> Zdefiniuj wymagane klasy i serializacji do formatu JSON

#### <a name="define-classes"></a>Definiowanie klas

Poniższy przykład pokazuje przykład definicje klas, które mapowania do schematu Widevine JSON. Przed rozpoczęciem serializacji je do ciągu JSON, można utworzyć wystąpienie klasy.  

```csharp
public class PolicyOverrides
{
    public bool CanPlay { get; set; }
    public bool CanPersist { get; set; }
    public bool CanRenew { get; set; }
    public int RentalDurationSeconds { get; set; }    //Indicates the time window while playback is permitted. A value of 0 indicates that there is no limit to the duration. Default is 0.
    public int PlaybackDurationSeconds { get; set; }  //The viewing window of time after playback starts within the license duration. A value of 0 indicates that there is no limit to the duration. Default is 0.
    public int LicenseDurationSeconds { get; set; }   //Indicates the time window for this specific license. A value of 0 indicates that there is no limit to the duration. Default is 0.
}

public class ContentKeySpec
{
    public string TrackType { get; set; }
    public int SecurityLevel { get; set; }
    public OutputProtection RequiredOutputProtection { get; set; }
}

public class OutputProtection
{
    public string HDCP { get; set; }
}

public class WidevineTemplate
{
    public string AllowedTrackTypes { get; set; }
    public ContentKeySpec[] ContentKeySpecs { get; set; }
    public PolicyOverrides PolicyOverrides { get; set; }
}
```

#### <a name="configure-the-license"></a>Konfigurowanie licencji

Użyj klas zdefiniowanych w poprzedniej sekcji, aby utworzyć JSON, który jest używany do konfigurowania [WidevineTemplate](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.media.models.contentkeypolicywidevineconfiguration.widevinetemplate?view=azure-dotnet#Microsoft_Azure_Management_Media_Models_ContentKeyPolicyWidevineConfiguration_WidevineTemplate):

```csharp
private static ContentKeyPolicyWidevineConfiguration ConfigureWidevineLicenseTempate()
{
    WidevineTemplate template = new WidevineTemplate()
    {
        AllowedTrackTypes = "SD_HD",
        ContentKeySpecs = new ContentKeySpec[]
        {
            new ContentKeySpec()
            {
                TrackType = "SD",
                SecurityLevel = 1,
                RequiredOutputProtection = new OutputProtection()
                {
                    HDCP = "HDCP_V2"
                }
            }
        },
        PolicyOverrides = new PolicyOverrides()
        {
            CanPlay = true,
            CanPersist = true,
            CanRenew = false,
            RentalDurationSeconds = 2592000,
            PlaybackDurationSeconds = 10800,
            LicenseDurationSeconds = 604800,
        }
    };

    ContentKeyPolicyWidevineConfiguration objContentKeyPolicyWidevineConfiguration = new ContentKeyPolicyWidevineConfiguration
    {
        WidevineTemplate = Newtonsoft.Json.JsonConvert.SerializeObject(template)
    };
    return objContentKeyPolicyWidevineConfiguration;
}
```

## <a name="next-steps"></a>Kolejne kroki

Zapoznaj się z jak [chronić za pomocą technologii DRM](protect-with-drm.md)
