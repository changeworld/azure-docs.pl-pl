---
title: Zasady klucza zawartości w usłudze Media Services — Azure | Dokumenty firmy Microsoft
description: W tym artykule wyjaśniono, jakie są zasady klucza zawartości i jak są one używane przez usługę Azure Media Services.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 07/26/2019
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: 7ddef1e78b4f8f62145e10b4cabc4537e28aba2f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74969908"
---
# <a name="content-key-policies"></a>Zasady kluczy zawartości

Usługa Media Services umożliwia dynamiczne szyfrowanie zawartości na żywo i na żądanie za pomocą standardu Advanced Encryption Standard (AES-128) lub dowolnego z trzech głównych systemów zarządzania prawami cyfrowymi (DRM): Microsoft PlayReady, Google Widevine i Apple FairPlay. Usługa Media Services zapewnia również usługę dostarczania licencji kluczy AES i DRM (PlayReady, Widevine i FairPlay) autoryzowanym klientom. 

Aby określić opcje szyfrowania w strumieniu, należy utworzyć [zasady przesyłania strumieniowego](streaming-policy-concept.md) i skojarzyć je z [lokalizatorem przesyłania strumieniowego](streaming-locators-concept.md). Zasady [klucza zawartości](https://docs.microsoft.com/rest/api/media/contentkeypolicies) są tworzą, aby skonfigurować sposób dostarczania klucza zawartości (który zapewnia bezpieczny dostęp do [zasobów)](assets-concept.md)klientom końcowym. Należy ustawić wymagania (ograniczenia) zasad klucza zawartości, które muszą być spełnione, aby klucze o określonej konfiguracji były dostarczane do klientów. Zasady klucza zawartości nie są potrzebne do wyczyszczenie przesyłania strumieniowego lub pobierania. 

Zazwyczaj zasady klucza zawartości są kojarzą z [lokalizatorem przesyłania strumieniowego](streaming-locators-concept.md). Alternatywnie można określić zasady klucza zawartości wewnątrz [zasad przesyłania strumieniowego](streaming-policy-concept.md) (podczas tworzenia niestandardowych zasad przesyłania strumieniowego dla zaawansowanych scenariuszy). 

## <a name="best-practices-and-considerations"></a>Najważniejsze wskazówki i zagadnienia

> [!IMPORTANT]
> Zapoznaj się z poniższymi zaleceniami.

* Należy zaprojektować ograniczony zestaw zasad dla konta usługi Media Service i użyć ich ponownie dla lokalizatorów przesyłania strumieniowego, gdy są potrzebne te same opcje. Aby uzyskać więcej informacji, zobacz [Przydziały i ograniczenia](limits-quotas-constraints.md).
* Zasady klucza zawartości można aktualizować. Aktualizacja i pobranie zaktualizowanych zasad może potrwać do 15 minut. 

   Aktualizując zasady, są zastępowanie istniejącej pamięci podręcznej sieci CDN, co może spowodować problem z odtwarzaniem dla klientów, którzy używają zawartości w pamięci podręcznej.  
* Zaleca się, aby nie tworzyć nowych zasad klucza zawartości dla każdego zasobu. Główne korzyści z udostępniania tych samych kluczowych zasad treści między zasobami, które wymagają tych samych wariantów strategicznych, to:
   
   * Łatwiej jest zarządzać niewielką liczbą zasad.
   * Jeśli musisz wprowadzić aktualizacje zasad klucza zawartości, zmiany wchodzą w życie we wszystkich nowych żądaniach licencji niemal od razu.
* Jeśli musisz utworzyć nową zasadę, musisz utworzyć nowy lokalizator przesyłania strumieniowego dla zasobu.
* Zaleca się, aby usługa Media Services automatycznie generować klucz zawartości. 

   Zazwyczaj należy użyć klucza długotrwałego i sprawdzić istnienie zasad klucza zawartości z [Get](https://docs.microsoft.com/rest/api/media/contentkeypolicies/get). Aby uzyskać klucz, należy wywołać metodę akcji oddzielne, aby uzyskać wpisy tajne lub poświadczenia, zobacz przykład, który następuje.

## <a name="example"></a>Przykład

Aby przejść do klucza, użyj `GetPolicyPropertiesWithSecretsAsync`, jak pokazano w Pobierz klucz [podpisywania z istniejącego przykładu zasad.](get-content-key-policy-dotnet-howto.md#get-contentkeypolicy-with-secrets)

## <a name="filtering-ordering-paging"></a>Filtrowanie, zamawianie, stronicowanie

Zobacz [Filtrowanie, zamawianie, stronicowanie jednostek usługi Media Services](entities-overview.md).

## <a name="additional-notes"></a>Uwagi dodatkowe

* Właściwości zasad klucza zawartości, które `Datetime` są typu są zawsze w formacie UTC.
* Widevine jest usługą świadczoną przez Google Inc. i podlega warunkom korzystania z usługi oraz Polityce prywatności Firmy Google, Inc.

## <a name="next-steps"></a>Następne kroki

* [Używanie dynamicznego szyfrowania AES-128 i usługi dostarczania kluczy](protect-with-aes128.md)
* [Używanie usługi dostarczania licencji i szyfrowania dynamicznego w technologii DRM](protect-with-drm.md)
* [KodowaniehttpandpublishAESSzyfrowany](https://github.com/Azure-Samples/media-services-v3-dotnet-core-tutorials/tree/master/NETCore/EncodeHTTPAndPublishAESEncrypted)
