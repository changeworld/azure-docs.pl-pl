---
title: Usługa Azure Media Services w wersji 3 z omówieniem szablonu licencji Widevine
description: W tym temacie przedstawiono omówienie szablonu licencji Widevine używanego do konfigurowania licencji Widevine.
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
ms.openlocfilehash: 94ce5e45a9a43e81020096ddc0a67429b286d9b1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76705636"
---
# <a name="media-services-v3-with-widevine-license-template-overview"></a>Omówienie szablonu licencji usługi Media Services w wersji 3 z szablonem licencji Widevine

Usługa Azure Media Services umożliwia szyfrowanie zawartości za pomocą **programu Google Widevine.** Usługa Media Services zapewnia również usługę dostarczania licencji Widevine. Za pomocą interfejsów API usługi Azure Media Services można skonfigurować licencje Widevine. Gdy gracz próbuje odtworzyć zawartość chronioną przez Widevine, żądanie jest wysyłane do usługi dostarczania licencji w celu uzyskania licencji. Jeśli usługa licencji zatwierdzi żądanie, usługa wystawia licencję. Jest wysyłany do klienta i służy do odszyfrowywania i odtwarzania określonej zawartości.

Żądanie licencji Widevine jest sformatowany jako komunikat JSON.  

>[!NOTE]
> Można utworzyć pustą wiadomość bez wartości, po prostu "{}." Następnie tworzony jest szablon licencji z wartościami domyślnymi. Wartość domyślna działa w większości przypadków. Scenariusze dostarczania licencji oparte na platformie Microsoft powinny zawsze używać wartości domyślnych. Jeśli musisz ustawić wartości "dostawca" i "content_id", dostawca musi być zgodny z poświadczeniami Widevine.

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
          "renew_with_usage":<renew with usage>
       }
    }

## <a name="json-message"></a>Komunikat JSON

| Nazwa | Wartość | Opis |
| --- | --- | --- |
| payload |Ciąg zakodowany w kodowanym base64 |Żądanie licencji wysłane przez klienta. |
| content_id |Ciąg zakodowany w kodowanym base64 |Identyfikator używany do wyprowadzania identyfikatora klucza i klucza zawartości dla każdego content_key_specs.track_type. |
| Dostawca |ciąg |Służy do wyszukiwania kluczy zawartości i zasad. Jeśli dostarczanie kluczy firmy Microsoft jest używane do dostarczania licencji Widevine, ten parametr jest ignorowany. |
| policy_name |ciąg |Nazwa wcześniej zarejestrowanej zasady. Element opcjonalny. |
| allowed_track_types |enum |SD_ONLY lub SD_HD. Określa, które klucze zawartości są zawarte w licencji. |
| content_key_specs |Tablica struktur JSON, zobacz sekcję "Specyfikacje klucza zawartości".  |Drobnoziarnista kontrolka, na której klucze zawartości do zwrócenia. Aby uzyskać więcej informacji, zobacz sekcję "Specyfikacje klucza zawartości". Można określić tylko jedną z wartości allowed_track_types i content_key_specs. |
| use_policy_overrides_exclusively |Logiczne, prawdziwe lub fałszywe |Użyj atrybutów zasad określonych przez policy_overrides i pomiń wszystkie wcześniej przechowywane zasady. |
| policy_overrides |JSON struktury, zobacz sekcję "Zasady zastąpienia." |Ustawienia zasad dla tej licencji.  W przypadku, gdy ten zasób ma wstępnie zdefiniowane zasady, te określone wartości są używane. |
| session_init |JSON struktury, zobacz sekcję "Inicjowanie sesji." |Dane opcjonalne są przekazywane do licencji. |
| parse_only |Logiczne, prawdziwe lub fałszywe |Żądanie licencji jest analizowane, ale nie wydano licencji. Jednak wartości z żądania licencji są zwracane w odpowiedzi. |

## <a name="content-key-specs"></a>Specyfikacje klucza zawartości
Jeśli istnieje wcześniej istniejące zasady, nie ma potrzeby określania żadnej z wartości w specyfikacji klucza zawartości. Istniejące wcześniej zasady skojarzone z tą zawartością są używane do określania ochrony danych wyjściowych, takiej jak ochrona zawartości cyfrowej o wysokiej przepustowości (HDCP) i system zarządzania ogólnego kopiowania (CGMS). Jeśli istniejące wcześniej zasady nie są zarejestrowane na serwerze licencji Widevine, dostawca zawartości może wstrzyknąć wartości do żądania licencji.   

Każda wartość content_key_specs musi być określona dla wszystkich ścieżek, niezależnie od opcji use_policy_overrides_exclusively. 

| Nazwa | Wartość | Opis |
| --- | --- | --- |
| content_key_specs. track_type |ciąg |Nazwa typu ścieżki. Jeśli content_key_specs jest określony w żądaniu licencji, upewnij się, aby określić wszystkie typy ścieżek jawnie. Niezastosowanie się do tego powoduje, że odtwarzanie w ciągu ostatnich 10 sekund nie jest odtwarzane. |
| content_key_specs  <br/> security_level |uint32 |Definiuje wymagania dotyczące niezawodności klienta dla odtwarzania. <br/> - Kryptografia oparta na oprogramowaniu white-box jest wymagana. <br/> - Wymagana jest kryptografia oprogramowania i zaciemniony dekoder. <br/> - Kluczowe operacje materiałowe i kryptograficzne muszą być wykonywane w środowisku zaufanym wykonanie wspieranym sprzętowo. <br/> - Kryptografii i dekodowania zawartości muszą być wykonywane w środowisku zaufanym wykonaniu wspieranym przez sprzęt.  <br/> - Kryptografii, dekodowania i wszystkie obsługi nośnika (skompresowane i nieskompresowane) muszą być obsługiwane w środowisku zaufanych wykonywania wspierane przez sprzęt. |
| content_key_specs <br/> required_output_protection.hdc |jeden z HDCP_NONE, HDCP_V1, HDCP_V2 |Wskazuje, czy hdcp jest wymagany. |
| content_key_specs <br/>key |Baza 64-<br/>zakodowany ciąg |Klucz zawartości do użycia w tej ścieżce. Jeśli jest określony, wymagane jest track_type lub key_id. Dostawca zawartości może użyć tej opcji, aby wstrzyknąć klucz zawartości dla tej ścieżki zamiast pozwolić serwerowi licencji Widevine generować lub wyszukać klucz. |
| content_key_specs.key_id |Plik binarny ciągów zakodowanych w formacie Base64, 16 bajtów |Unikatowy identyfikator klucza. |

## <a name="policy-overrides"></a>Zastąpienia zasad
| Nazwa | Wartość | Opis |
| --- | --- | --- |
| policy_overrides&#46;can_play |Logiczne, prawdziwe lub fałszywe |Wskazuje, że odtwarzanie zawartości jest dozwolone. Wartość domyślna to false. |
| policy_overrides&#46;can_persist |Logiczne, prawdziwe lub fałszywe |Wskazuje, że licencja może być utrwalona do magazynu nieulotnego do użytku w trybie offline. Wartość domyślna to false. |
| policy_overrides can_renew&#46; |Logiczne, prawdziwe lub fałszywe |Wskazuje, że odnowienie tej licencji jest dozwolone. Jeśli true, czas trwania licencji można przedłużyć przez bicie serca. Wartość domyślna to false. |
| policy_overrides&#46;license_duration_seconds |int64 |Wskazuje przedział czasu dla tej konkretnej licencji. Wartość 0 wskazuje, że nie ma limitu czasu trwania. Wartość domyślna to 0. |
| policy_overrides&#46;rental_duration_seconds |int64 |Wskazuje okno czasu, w które odtwarzanie jest dozwolone. Wartość 0 wskazuje, że nie ma limitu czasu trwania. Wartość domyślna to 0. |
| policy_overrides&#46;playback_duration_seconds |int64 |Okno wyświetlania czasu po odtwoniu rozpoczyna się w czasie trwania licencji. Wartość 0 wskazuje, że nie ma limitu czasu trwania. Wartość domyślna to 0. |
| policy_overrides&#46;renewal_server_url |ciąg |Wszystkie żądania pulsu (odnowienia) dla tej licencji są kierowane do określonego adresu URL. To pole jest używane tylko wtedy, gdy can_renew jest true. |
| policy_overrides renewal_delay_seconds&#46; |int64 |Ile sekund po license_start_time przed pierwszą próbą odnowienia. To pole jest używane tylko wtedy, gdy can_renew jest true. Wartość domyślna to 0. |
| policy_overrides&#46;renewal_retry_interval_seconds |int64 |Określa opóźnienie w sekundach między kolejnymi żądaniami odnowienia licencji w przypadku niepowodzenia. To pole jest używane tylko wtedy, gdy can_renew jest true. |
| policy_overrides&#46;renewal_recovery_duration_seconds |int64 |Okno czasu, w którym odtwarzanie może być kontynuowane podczas próby odnowienia, ale nie powiodło się z powodu problemów z zapleczem serwera licencji. Wartość 0 wskazuje, że nie ma limitu czasu trwania. To pole jest używane tylko wtedy, gdy can_renew jest true. |
| policy_overrides&#46;renew_with_usage |Logiczne, prawdziwe lub fałszywe |Wskazuje, że licencja jest wysyłana do odnowienia po rozpoczęciu użytkowania. To pole jest używane tylko wtedy, gdy can_renew jest true. |

## <a name="session-initialization"></a>Inicjowanie sesji
| Nazwa | Wartość | Opis |
| --- | --- | --- |
| provider_session_token |Ciąg zakodowany w kodowanym base64 |Ten token sesji jest przekazywany z powrotem w licencji i istnieje w kolejnych odnowieniach. Token sesji nie utrzymuje się poza sesjami. |
| provider_client_token |Ciąg zakodowany w kodowanym base64 |Token klienta do wysłania z powrotem w odpowiedzi licencji. Jeśli żądanie licencji zawiera token klienta, ta wartość jest ignorowana. Token klienta będzie się powtarzał poza sesjami licencji. |
| override_provider_client_token |Logiczne, prawdziwe lub fałszywe |Jeśli false i żądanie licencji zawiera token klienta, użyj tokenu z żądania, nawet jeśli token klienta został określony w tej strukturze. Jeśli true, zawsze należy użyć tokenu określonego w tej strukturze. |

## <a name="configure-your-widevine-license-with-net"></a>Skonfiguruj licencję Widevine za pomocą platformy .NET 

Media Services zapewnia klasę, która umożliwia skonfigurowanie licencji Widevine. Aby skonstruować licencję, przekaż JSON do [WidevineTemplate](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.media.models.contentkeypolicywidevineconfiguration.widevinetemplate?view=azure-dotnet#Microsoft_Azure_Management_Media_Models_ContentKeyPolicyWidevineConfiguration_WidevineTemplate).

Aby skonfigurować szablon, można:

### <a name="directly-construct-a-json-string"></a>Bezpośrednie konstruowanie ciągu JSON

Ta metoda może być podatna na błędy. Zaleca się użycie innej metody opisanej w [define potrzebne klasy i serializacji do JSON](#classes).

```csharp
ContentKeyPolicyWidevineConfiguration objContentKeyPolicyWidevineConfiguration = new ContentKeyPolicyWidevineConfiguration
{
    WidevineTemplate = @"{""allowed_track_types"":""SD_HD"",""content_key_specs"":[{""track_type"":""SD"",""security_level"":1,""required_output_protection"":{""hdcp"":""HDCP_V2""}}],""policy_overrides"":{""can_play"":true,""can_persist"":true,""can_renew"":false}}"
};
```

### <a name="define-needed-classes-and-serialize-to-json"></a><a id="classes"></a>Definiowanie potrzebnych klas i serializowanie do JSON

#### <a name="define-classes"></a>Definiowanie klas

Poniższy przykład przedstawia przykład definicji klas, które mapują do schematu JSON Widevine. Można utworzyć wystąpienia klas przed serializacją ich do ciągu JSON.  

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

Użyj klas zdefiniowanych w poprzedniej sekcji, aby utworzyć JSON, który jest używany do konfigurowania [WidevineTemplate:](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.media.models.contentkeypolicywidevineconfiguration.widevinetemplate?view=azure-dotnet#Microsoft_Azure_Management_Media_Models_ContentKeyPolicyWidevineConfiguration_WidevineTemplate)

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

## <a name="additional-notes"></a>Uwagi dodatkowe

* Widevine jest usługą świadczoną przez Google Inc. i podlega warunkom korzystania z usługi oraz Polityce prywatności Firmy Google, Inc.

## <a name="next-steps"></a>Następne kroki

Sprawdź, jak [chronić za pomocą DRM](protect-with-drm.md)
