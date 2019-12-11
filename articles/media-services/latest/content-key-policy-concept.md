---
title: Zawartość zasady kluczy w usłudze Media Services — Azure | Dokumentacja firmy Microsoft
description: Ten artykuł zawiera wyjaśnienie, co to są zasady klucz zawartości i jak są one używane przez usługi Azure Media Services.
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
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/10/2019
ms.locfileid: "74969908"
---
# <a name="content-key-policies"></a>Zasady kluczy zawartości

Usługa Media Services umożliwia dostarczanie zawartości na żywo i na żądanie dynamicznie zaszyfrowany za pomocą Advanced Encryption Standard (AES-128), lub jeden z systemów zarządzania (prawami cyfrowymi DRM) trzech głównych prawami cyfrowymi: PlayReady firmy Microsoft, Google Widevine i FairPlay firmy Apple. Media Services udostępnia również usługę dostarczania kluczy AES i technologii DRM (PlayReady, Widevine i FairPlay) licencji do autoryzowanych klientów. 

Aby określić opcje szyfrowania w strumieniu, należy utworzyć [zasady przesyłania strumieniowego](streaming-policy-concept.md) i skojarzyć je z [lokalizatorem przesyłania strumieniowego](streaming-locators-concept.md). Tworzenie [zasad klucza zawartości](https://docs.microsoft.com/rest/api/media/contentkeypolicies) w celu skonfigurowania sposobu, w jaki klucz zawartości (zapewniający bezpieczny dostęp do [zasobów](assets-concept.md)) jest dostarczany do klientów końcowych. Należy ustawić wymagania (ograniczenia) dotyczące zasad kluczy zawartości, które muszą zostać spełnione, aby klucze z określoną konfiguracją były dostarczane do klientów. Zasady kluczy zawartości nie są wymagane do wyczyszczenia przesyłania strumieniowego ani pobierania. 

Zazwyczaj należy skojarzyć Zasady kluczy zawartości z [lokalizatorem przesyłania strumieniowego](streaming-locators-concept.md). Alternatywnie możesz określić zasady dotyczące klucza zawartości w ramach [zasad przesyłania strumieniowego](streaming-policy-concept.md) (podczas tworzenia niestandardowych zasad przesyłania strumieniowego dla scenariuszy zaawansowanych). 

## <a name="best-practices-and-considerations"></a>Najlepsze rozwiązania i zagadnienia

> [!IMPORTANT]
> Zapoznaj się z poniższymi zaleceniami.

* Należy zapoznać się z ograniczonym zestawem zasad dla konta usługi multimediów i użyć ich ponownie dla lokalizatorów przesyłania strumieniowego, gdy są potrzebne te same opcje. Aby uzyskać więcej informacji, zobacz [przydziały i ograniczenia](limits-quotas-constraints.md).
* Zasady kluczy zawartości są aktualizowalne. W przypadku pamięci podręcznej dostarczania kluczy do aktualizacji i pobrania zaktualizowanych zasad może upłynąć do 15 minut. 

   Aktualizacja zasad powoduje zastąpienie istniejącej pamięci podręcznej usługi CDN, co może spowodować problemy z odtwarzaniem klientów korzystających z zawartości w pamięci podręcznej.  
* Zalecamy, aby nie tworzyć nowych zasad kluczy zawartości dla każdego zasobu. Główną zaletą udostępniania tych samych zasad kluczy zawartości między zasobami, które wymagają tych samych opcji zasad, są:
   
   * Zarządzanie niewielką liczbą zasad jest łatwiejsze.
   * Jeśli musisz wprowadzić aktualizacje zasad klucza zawartości, zmiany zaczną obowiązywać od wszystkich nowych żądań licencji niemal od razu.
* Jeśli musisz utworzyć nowe zasady, musisz utworzyć nowy lokalizator przesyłania strumieniowego dla elementu zawartości.
* Zalecane jest, aby Media Services automatycznie generować klucz zawartości. 

   Zazwyczaj należy użyć długiego klucza i sprawdzić istnienie zasad klucza zawartości za pomocą [Get](https://docs.microsoft.com/rest/api/media/contentkeypolicies/get). Aby uzyskać klucz, należy wywołać oddzielną metodę akcji w celu uzyskania kluczy tajnych lub poświadczeń, zobacz Poniższy przykład.

## <a name="example"></a>Przykład

Aby przejść do klucza, użyj `GetPolicyPropertiesWithSecretsAsync`, jak pokazano w przykładzie [Pobieranie klucza podpisywania z istniejących zasad](get-content-key-policy-dotnet-howto.md#get-contentkeypolicy-with-secrets) .

## <a name="filtering-ordering-paging"></a>Filtrowania, sortowania, stronicowania

Zobacz [filtrowanie, porządkowanie, stronicowanie jednostek Media Services](entities-overview.md).

## <a name="additional-notes"></a>Uwagi dodatkowe

* Właściwości zasad kluczy zawartości typu `Datetime` są zawsze w formacie UTC.
* Widevine to usługa świadczona przez firmę Google Inc. z zastrzeżeniem warunków użytkowania i zasad zachowania poufności informacji w firmie Google, Inc.

## <a name="next-steps"></a>Następne kroki

* [Używanie dynamicznego szyfrowania AES-128 i usługi dostarczania kluczy](protect-with-aes128.md)
* [Używanie usługi dostarczania licencji i szyfrowania dynamicznego w technologii DRM](protect-with-drm.md)
* [EncodeHTTPAndPublishAESEncrypted](https://github.com/Azure-Samples/media-services-v3-dotnet-core-tutorials/tree/master/NETCore/EncodeHTTPAndPublishAESEncrypted)
