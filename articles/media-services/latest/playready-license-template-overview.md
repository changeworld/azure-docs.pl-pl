---
title: Usługa Azure Media Services w wersji 3 z szablonem licencji Programu Microsoft PlayReady
description: W tym temacie przedstawiono omówienie szablonu licencji PlayReady, który jest używany do konfigurowania licencji PlayReady.
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
ms.date: 10/16/2018
ms.author: juliako
ms.openlocfilehash: 6b12dcee2303632c4ec2ccc3602348a4e17fcd05
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76705891"
---
# <a name="media-services-v3-with-playready-license-template"></a>Usługi Media Services w wersji 3 z szablonem licencji PlayReady 

Usługa Azure Media Services umożliwia szyfrowanie zawartości za pomocą programu **Microsoft PlayReady.** Usługa Media Services zapewnia również usługę dostarczania licencji PlayReady. Za pomocą interfejsów API usługi Media Services można skonfigurować licencje PlayReady. Gdy gracz próbuje odtworzyć zawartość chronioną przez PlayReady, żądanie jest wysyłane do usługi dostarczania licencji w celu uzyskania licencji. Jeśli usługa licencji zatwierdzi żądanie, wystawia licencję, która jest wysyłana do klienta i jest używana do odszyfrowywania i odtwarzania określonej zawartości.

Licencje PlayReady zawierają prawa i ograniczenia, które mają być wymuszane w czasie wykonywania środowiska wykonawczego zarządzania prawami cyfrowymi (DRM) PlayReady, gdy użytkownik próbuje odtworzyć chronioną zawartość. Oto kilka przykładów ograniczeń licencji PlayReady, które można określić:

* Data i godzina ważności licencji.
* Wartość DateTime po wygaśnięciu licencji. 
* Aby licencja została zapisana w magazynie trwałym na kliencie. Licencje trwałe są zwykle używane do zezwalania na odtwarzanie zawartości w trybie offline.
* Minimalny poziom zabezpieczeń, jaki gracz musi mieć do odtwo nie więcej niż w twojej zawartości. 
* Poziom ochrony danych wyjściowych dla elementów sterujących wyjściem dla zawartości audio\video. 
* Aby uzyskać więcej informacji, zobacz sekcję "Kontrolki danych wyjściowych" (3.5) w dokumencie [Reguły zgodności PlayReady.](https://www.microsoft.com/playready/licensing/compliance/)

> [!NOTE]
> Obecnie można skonfigurować tylko playright licencji PlayReady. To prawo jest wymagane. PlayRight daje klientowi możliwość odtwarzania zawartości. Za pomocą funkcji PlayRight można również skonfigurować ograniczenia specyficzne dla odtwarzania. 
> 

W tym temacie opisano sposób konfigurowania licencji PlayReady za pomocą usługi Media Services.

## <a name="basic-streaming-license-example"></a>Przykład podstawowej licencji przesyłania strumieniowego

W poniższym przykładzie przedstawiono najprostszy (i najbardziej typowy) szablon, który konfiguruje podstawową licencję przesyłania strumieniowego. Dzięki tej licencji klienci mogą odtwarzać zawartość chronioną przez PlayReady. 

Kod XML jest zgodny ze schematem XML szablonu licencji PlayReady zdefiniowanym w sekcji [schematu XML szablonu licencji PlayReady.](#schema)


    <?xml version="1.0" encoding="utf-8"?>
    <PlayReadyLicenseResponseTemplate xmlns:i="https://www.w3.org/2001/XMLSchema-instance" 
                                      xmlns="http://schemas.microsoft.com/Azure/MediaServices/KeyDelivery/PlayReadyTemplate/v1">
      <LicenseTemplates>
        <PlayReadyLicenseTemplate>
          <ContentKey i:type="ContentEncryptionKeyFromHeader" />
          <PlayRight />
        </PlayReadyLicenseTemplate>
      </LicenseTemplates>
    </PlayReadyLicenseResponseTemplate>


## <a name="use-media-services-apis-to-configure-license-templates"></a><a id="classes"></a>Konfigurowanie szablonów licencji za pomocą interfejsów API usług Media Services

Usługi Media Services udostępnia typy, których można użyć do skonfigurowania szablonu licencji PlayReady. 

Następujący fragment kodu używa klas platformy Media Services .NET do skonfigurowania szablonu licencji PlayReady. Klasy są zdefiniowane w obszarze nazw [Microsoft.Azure.Management.Media.Models.](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.media.models?view=azure-dotnet) Fragment kodu konfiguruje odtwarzanie licencji PlayReady. PlayRight daje użytkownikowi możliwość odtwarzania zawartości z zastrzeżeniem wszelkich ograniczeń skonfigurowanych w licencji i na playright się (dla zasad specyficznych dla odtwarzania). Wiele zasad na PlayRight dotyczy ograniczenia danych wyjściowych, które kontrolują typy wyjść, które mogą być odtwarzane przez zawartość. Zawiera również wszelkie ograniczenia, które muszą być wprowadzone, gdy używane jest dane wyjściowe. Na przykład jeśli DigitalVideoOnlyContentRestriction jest włączona, środowisko wykonawcze DRM umożliwia wyświetlanie wideo tylko za przewodami cyfrowymi. (Analogowe wyjścia wideo nie mogą przekazywać zawartości).

> [!IMPORTANT]
> Licencja PlayReady ma ograniczenia, które są potężne. Jeśli zabezpieczenia danych wyjściowych są zbyt restrykcyjne, zawartość może być niegrywalna na niektórych klientach. Aby uzyskać więcej informacji, zobacz [Zasady zgodności PlayReady](https://www.microsoft.com/playready/licensing/compliance/).

### <a name="configure-playready-license-template-with-net"></a>Konfigurowanie szablonu licencji PlayReady za pomocą platformy .NET

```csharp
ContentKeyPolicyPlayReadyLicense objContentKeyPolicyPlayReadyLicense;
objContentKeyPolicyPlayReadyLicense = new ContentKeyPolicyPlayReadyLicense
{
    AllowTestDevices = true,
    BeginDate = new DateTime(2016, 1, 1),
    ContentKeyLocation = new ContentKeyPolicyPlayReadyContentEncryptionKeyFromHeader(),
    ContentType = ContentKeyPolicyPlayReadyContentType.UltraVioletStreaming,
    LicenseType = drmSettings.EnableOfflineMode ? ContentKeyPolicyPlayReadyLicenseType.Persistent : ContentKeyPolicyPlayReadyLicenseType.NonPersistent,
    PlayRight = new ContentKeyPolicyPlayReadyPlayRight
    {
        ImageConstraintForAnalogComponentVideoRestriction = true,
        ExplicitAnalogTelevisionOutputRestriction = new ContentKeyPolicyPlayReadyExplicitAnalogTelevisionRestriction(true, 2),
        AllowPassingVideoContentToUnknownOutput = ContentKeyPolicyPlayReadyUnknownOutputPassingOption.Allowed,
        FirstPlayExpiration = TimeSpan.FromSeconds(20.0),
    }
};
```

## <a name="playready-license-template-xml-schema"></a><a id="schema"></a>Schemat XML szablonu licencji PlayReady
    <?xml version="1.0" encoding="utf-8"?>
    <xs:schema xmlns:tns="http://schemas.microsoft.com/Azure/MediaServices/KeyDelivery/PlayReadyTemplate/v1" xmlns:ser="http://schemas.microsoft.com/2003/10/Serialization/" elementFormDefault="qualified" targetNamespace="http://schemas.microsoft.com/Azure/MediaServices/KeyDelivery/PlayReadyTemplate/v1" xmlns:xs="https://www.w3.org/2001/XMLSchema">
      <xs:import namespace="http://schemas.microsoft.com/2003/10/Serialization/" />
      <xs:complexType name="AgcAndColorStripeRestriction">
        <xs:sequence>
          <xs:element minOccurs="0" name="ConfigurationData" type="xs:unsignedByte" />
        </xs:sequence>
      </xs:complexType>
      <xs:element name="AgcAndColorStripeRestriction" nillable="true" type="tns:AgcAndColorStripeRestriction" />
      <xs:simpleType name="ContentType">
        <xs:restriction base="xs:string">
          <xs:enumeration value="Unspecified" />
          <xs:enumeration value="UltravioletDownload" />
          <xs:enumeration value="UltravioletStreaming" />
        </xs:restriction>
      </xs:simpleType>
      <xs:element name="ContentType" nillable="true" type="tns:ContentType" />
      <xs:complexType name="ExplicitAnalogTelevisionRestriction">
        <xs:sequence>
          <xs:element minOccurs="0" name="BestEffort" type="xs:boolean" />
          <xs:element minOccurs="0" name="ConfigurationData" type="xs:unsignedByte" />
        </xs:sequence>
      </xs:complexType>
      <xs:element name="ExplicitAnalogTelevisionRestriction" nillable="true" type="tns:ExplicitAnalogTelevisionRestriction" />
      <xs:complexType name="PlayReadyContentKey">
        <xs:sequence />
      </xs:complexType>
      <xs:element name="PlayReadyContentKey" nillable="true" type="tns:PlayReadyContentKey" />
      <xs:complexType name="ContentEncryptionKeyFromHeader">
        <xs:complexContent mixed="false">
          <xs:extension base="tns:PlayReadyContentKey">
            <xs:sequence />
          </xs:extension>
        </xs:complexContent>
      </xs:complexType>
      <xs:element name="ContentEncryptionKeyFromHeader" nillable="true" type="tns:ContentEncryptionKeyFromHeader" />
      <xs:complexType name="ContentEncryptionKeyFromKeyIdentifier">
        <xs:complexContent mixed="false">
          <xs:extension base="tns:PlayReadyContentKey">
            <xs:sequence>
              <xs:element minOccurs="0" name="KeyIdentifier" type="ser:guid" />
            </xs:sequence>
          </xs:extension>
        </xs:complexContent>
      </xs:complexType>
      <xs:element name="ContentEncryptionKeyFromKeyIdentifier" nillable="true" type="tns:ContentEncryptionKeyFromKeyIdentifier" />
      <xs:complexType name="PlayReadyLicenseResponseTemplate">
        <xs:sequence>
          <xs:element name="LicenseTemplates" nillable="true" type="tns:ArrayOfPlayReadyLicenseTemplate" />
          <xs:element minOccurs="0" name="ResponseCustomData" nillable="true" type="xs:string">
            <xs:annotation>
              <xs:appinfo>
                <DefaultValue EmitDefaultValue="false" xmlns="http://schemas.microsoft.com/2003/10/Serialization/" />
              </xs:appinfo>
            </xs:annotation>
          </xs:element>
        </xs:sequence>
      </xs:complexType>
      <xs:element name="PlayReadyLicenseResponseTemplate" nillable="true" type="tns:PlayReadyLicenseResponseTemplate" />
      <xs:complexType name="ArrayOfPlayReadyLicenseTemplate">
        <xs:sequence>
          <xs:element minOccurs="0" maxOccurs="unbounded" name="PlayReadyLicenseTemplate" nillable="true" type="tns:PlayReadyLicenseTemplate" />
        </xs:sequence>
      </xs:complexType>
      <xs:element name="ArrayOfPlayReadyLicenseTemplate" nillable="true" type="tns:ArrayOfPlayReadyLicenseTemplate" />
      <xs:complexType name="PlayReadyLicenseTemplate">
        <xs:sequence>
          <xs:element minOccurs="0" name="AllowTestDevices" type="xs:boolean" />
          <xs:element minOccurs="0" name="BeginDate" nillable="true" type="xs:dateTime">
            <xs:annotation>
              <xs:appinfo>
                <DefaultValue EmitDefaultValue="false" xmlns="http://schemas.microsoft.com/2003/10/Serialization/" />
              </xs:appinfo>
            </xs:annotation>
          </xs:element>
          <xs:element name="ContentKey" nillable="true" type="tns:PlayReadyContentKey" />
          <xs:element minOccurs="0" name="ContentType" type="tns:ContentType">
            <xs:annotation>
              <xs:appinfo>
                <DefaultValue EmitDefaultValue="false" xmlns="http://schemas.microsoft.com/2003/10/Serialization/" />
              </xs:appinfo>
            </xs:annotation>
          </xs:element>
          <xs:element minOccurs="0" name="ExpirationDate" nillable="true" type="xs:dateTime">
            <xs:annotation>
              <xs:appinfo>
                <DefaultValue EmitDefaultValue="false" xmlns="http://schemas.microsoft.com/2003/10/Serialization/" />
              </xs:appinfo>
            </xs:annotation>
          </xs:element>
          <xs:element minOccurs="0" name="GracePeriod" nillable="true" type="ser:duration">
            <xs:annotation>
              <xs:appinfo>
                <DefaultValue EmitDefaultValue="false" xmlns="http://schemas.microsoft.com/2003/10/Serialization/" />
              </xs:appinfo>
            </xs:annotation>
          </xs:element>
          <xs:element minOccurs="0" name="LicenseType" type="tns:PlayReadyLicenseType" />
          <xs:element minOccurs="0" name="PlayRight" nillable="true" type="tns:PlayReadyPlayRight" />
        </xs:sequence>
      </xs:complexType>
      <xs:element name="PlayReadyLicenseTemplate" nillable="true" type="tns:PlayReadyLicenseTemplate" />
      <xs:simpleType name="PlayReadyLicenseType">
        <xs:restriction base="xs:string">
          <xs:enumeration value="Nonpersistent" />
          <xs:enumeration value="Persistent" />
        </xs:restriction>
      </xs:simpleType>
      <xs:element name="PlayReadyLicenseType" nillable="true" type="tns:PlayReadyLicenseType" />
      <xs:complexType name="PlayReadyPlayRight">
        <xs:sequence>
          <xs:element minOccurs="0" name="AgcAndColorStripeRestriction" nillable="true" type="tns:AgcAndColorStripeRestriction">
            <xs:annotation>
              <xs:appinfo>
                <DefaultValue EmitDefaultValue="false" xmlns="http://schemas.microsoft.com/2003/10/Serialization/" />
              </xs:appinfo>
            </xs:annotation>
          </xs:element>
          <xs:element minOccurs="0" name="AllowPassingVideoContentToUnknownOutput" type="tns:UnknownOutputPassingOption">
            <xs:annotation>
              <xs:appinfo>
                <DefaultValue EmitDefaultValue="false" xmlns="http://schemas.microsoft.com/2003/10/Serialization/" />
              </xs:appinfo>
            </xs:annotation>
          </xs:element>
          <xs:element minOccurs="0" name="AnalogVideoOpl" nillable="true" type="xs:int">
            <xs:annotation>
              <xs:appinfo>
                <DefaultValue EmitDefaultValue="false" xmlns="http://schemas.microsoft.com/2003/10/Serialization/" />
              </xs:appinfo>
            </xs:annotation>
          </xs:element>
          <xs:element minOccurs="0" name="CompressedDigitalAudioOpl" nillable="true" type="xs:int">
            <xs:annotation>
              <xs:appinfo>
                <DefaultValue EmitDefaultValue="false" xmlns="http://schemas.microsoft.com/2003/10/Serialization/" />
              </xs:appinfo>
            </xs:annotation>
          </xs:element>
          <xs:element minOccurs="0" name="CompressedDigitalVideoOpl" nillable="true" type="xs:int">
            <xs:annotation>
              <xs:appinfo>
                <DefaultValue EmitDefaultValue="false" xmlns="http://schemas.microsoft.com/2003/10/Serialization/" />
              </xs:appinfo>
            </xs:annotation>
          </xs:element>
          <xs:element minOccurs="0" name="DigitalVideoOnlyContentRestriction" type="xs:boolean">
            <xs:annotation>
              <xs:appinfo>
                <DefaultValue EmitDefaultValue="false" xmlns="http://schemas.microsoft.com/2003/10/Serialization/" />
              </xs:appinfo>
            </xs:annotation>
          </xs:element>
          <xs:element minOccurs="0" name="ExplicitAnalogTelevisionOutputRestriction" nillable="true" type="tns:ExplicitAnalogTelevisionRestriction">
            <xs:annotation>
              <xs:appinfo>
                <DefaultValue EmitDefaultValue="false" xmlns="http://schemas.microsoft.com/2003/10/Serialization/" />
              </xs:appinfo>
            </xs:annotation>
          </xs:element>
          <xs:element minOccurs="0" name="FirstPlayExpiration" nillable="true" type="ser:duration">
            <xs:annotation>
              <xs:appinfo>
                <DefaultValue EmitDefaultValue="false" xmlns="http://schemas.microsoft.com/2003/10/Serialization/" />
              </xs:appinfo>
            </xs:annotation>
          </xs:element>
          <xs:element minOccurs="0" name="ImageConstraintForAnalogComponentVideoRestriction" type="xs:boolean">
            <xs:annotation>
              <xs:appinfo>
                <DefaultValue EmitDefaultValue="false" xmlns="http://schemas.microsoft.com/2003/10/Serialization/" />
              </xs:appinfo>
            </xs:annotation>
          </xs:element>
          <xs:element minOccurs="0" name="ImageConstraintForAnalogComputerMonitorRestriction" type="xs:boolean">
            <xs:annotation>
              <xs:appinfo>
                <DefaultValue EmitDefaultValue="false" xmlns="http://schemas.microsoft.com/2003/10/Serialization/" />
              </xs:appinfo>
            </xs:annotation>
          </xs:element>
          <xs:element minOccurs="0" name="ScmsRestriction" nillable="true" type="tns:ScmsRestriction">
            <xs:annotation>
              <xs:appinfo>
                <DefaultValue EmitDefaultValue="false" xmlns="http://schemas.microsoft.com/2003/10/Serialization/" />
              </xs:appinfo>
            </xs:annotation>
          </xs:element>
          <xs:element minOccurs="0" name="UncompressedDigitalAudioOpl" nillable="true" type="xs:int">
            <xs:annotation>
              <xs:appinfo>
                <DefaultValue EmitDefaultValue="false" xmlns="http://schemas.microsoft.com/2003/10/Serialization/" />
              </xs:appinfo>
            </xs:annotation>
          </xs:element>
          <xs:element minOccurs="0" name="UncompressedDigitalVideoOpl" nillable="true" type="xs:int">
            <xs:annotation>
              <xs:appinfo>
                <DefaultValue EmitDefaultValue="false" xmlns="http://schemas.microsoft.com/2003/10/Serialization/" />
              </xs:appinfo>
            </xs:annotation>
          </xs:element>
        </xs:sequence>
      </xs:complexType>
      <xs:element name="PlayReadyPlayRight" nillable="true" type="tns:PlayReadyPlayRight" />
      <xs:simpleType name="UnknownOutputPassingOption">
        <xs:restriction base="xs:string">
          <xs:enumeration value="NotAllowed" />
          <xs:enumeration value="Allowed" />
          <xs:enumeration value="AllowedWithVideoConstriction" />
        </xs:restriction>
      </xs:simpleType>
      <xs:element name="UnknownOutputPassingOption" nillable="true" type="tns:UnknownOutputPassingOption" />
      <xs:complexType name="ScmsRestriction">
        <xs:sequence>
          <xs:element minOccurs="0" name="ConfigurationData" type="xs:unsignedByte" />
        </xs:sequence>
      </xs:complexType>
      <xs:element name="ScmsRestriction" nillable="true" type="tns:ScmsRestriction" />
    </xs:schema>


## <a name="next-steps"></a>Następne kroki

Sprawdź, jak [chronić za pomocą DRM](protect-with-drm.md)
