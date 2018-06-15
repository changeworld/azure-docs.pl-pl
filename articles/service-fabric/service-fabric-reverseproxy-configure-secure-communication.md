---
title: Sieć szkieletowa usług Azure odwrotna bezpiecznej komunikacji serwera proxy | Dokumentacja firmy Microsoft
description: Skonfiguruj zwrotnego serwera proxy, aby komunikacja zabezpieczona na trasie.
services: service-fabric
documentationcenter: .net
author: kavyako
manager: vipulm
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: required
ms.date: 08/10/2017
ms.author: kavyako
ms.openlocfilehash: 237a72fd282b29d3032675ccf3fb350f8db59ef7
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/16/2018
ms.locfileid: "34209175"
---
# <a name="connect-to-a-secure-service-with-the-reverse-proxy"></a>Połączyć z usługą bezpieczny z zwrotnego serwera proxy

W tym artykule wyjaśniono, jak można ustanowić bezpiecznego połączenia między zwrotnego serwera proxy i usług, umożliwiając kompleksowe bezpiecznego kanału.

Łączenie z usługami bezpiecznego jest obsługiwana tylko wtedy, gdy zwrotnego serwera proxy jest skonfigurowane do nasłuchiwania protokołu HTTPS. Pozostałej części dokumentu przyjęto założenie, że jest to możliwe.
Zapoznaj się [odwrotny serwer proxy w sieci szkieletowej usług Azure](https://docs.microsoft.com/azure/service-fabric/service-fabric-reverseproxy) skonfigurować zwrotnego serwera proxy w sieci szkieletowej usług.

## <a name="secure-connection-establishment-between-the-reverse-proxy-and-services"></a>Ustanawianie bezpiecznego połączenia między zwrotnego serwera proxy i usługami 

### <a name="reverse-proxy-authenticating-to-services"></a>Do usługi uwierzytelniania odwrotnego serwera proxy:
Zwrotny serwer proxy identyfikuje do usług za pomocą swojego certyfikatu określony za pomocą ***reverseProxyCertificate*** właściwości w **klastra** [sekcji Typ zasobu](../azure-resource-manager/resource-group-authoring-templates.md). Usługi można zaimplementować logiki, aby zweryfikować certyfikat przedstawiony przez zwrotny serwer proxy. Usługi można określić szczegóły certyfikatu klienta akceptowane jako ustawienia konfiguracji w pakiet konfiguracji. Może to odczytu w czasie wykonywania i używany do sprawdzania poprawności certyfikatu przedstawionego przez zwrotny serwer proxy. Zapoznaj się [Zarządzanie parametry aplikacji](service-fabric-manage-multiple-environment-app-configuration.md) Dodaj ustawień konfiguracji. 

### <a name="reverse-proxy-verifying-the-services-identity-via-the-certificate-presented-by-the-service"></a>Weryfikowanie tożsamości usługi za pośrednictwem certyfikat przedstawiony przez usługę zwrotnego serwera proxy:
Aby przeprowadzić weryfikacji certyfikatu serwera certyfikatów przedstawiony przez usługę, zwrotny serwer proxy obsługuje jedną z następujących opcji: None, ServiceCommonNameAndIssuer i ServiceCertificateThumbprints.
Aby wybrać jedną z tych trzech opcji, należy określić **ApplicationCertificateValidationPolicy** w sekcji parametrów elementu bramy aplikacji/Http w obszarze [fabricSettings](service-fabric-cluster-fabric-settings.md).

```json
{
"fabricSettings": [
          ...
          {
            "name": "ApplicationGateway/Http",
            "parameters": [
              {
                "name": "ApplicationCertificateValidationPolicy",
                "value": "None"
              }
            ]
          }
        ],
        ...
}
```

Zapoznaj się z sekcją dalej, aby uzyskać szczegółowe informacje o dodatkowych konfiguracji dla każdego z tych opcji.

### <a name="service-certificate-validation-options"></a>Opcje sprawdzania poprawności certyfikatu usługi 

- **Brak**: zwrotny serwer proxy Pomija weryfikację certyfikatu usługi proxy oraz ustanawianie bezpiecznego połączenia. Jest to zachowanie domyślne.
Określ **ApplicationCertificateValidationPolicy** z wartością **Brak** w sekcji parametrów elementu bramy aplikacji/Http.

- **ServiceCommonNameAndIssuer**: zwrotny serwer proxy sprawdza, czy certyfikat przedstawiony przez usługę, na podstawie nazwa pospolita certyfikatu i odcisk palca wystawcy natychmiastowego: Określ **ApplicationCertificateValidationPolicy** z wartością **ServiceCommonNameAndIssuer** w sekcji parametrów elementu bramy aplikacji/Http.

```json
{
"fabricSettings": [
          ...
          {
            "name": "ApplicationGateway/Http",
            "parameters": [
              {
                "name": "ApplicationCertificateValidationPolicy",
                "value": "ServiceCommonNameAndIssuer"
              }
            ]
          }
        ],
        ...
}
```

Aby określić listę typowych nazwę usługi i odcisków palca wystawcy, Dodaj **bramy aplikacji/Http/ServiceCommonNameAndIssuer** elementu w obszarze fabricSettings, jak pokazano poniżej. W elemencie tablicy parametrów można dodawać wiele par odcisk palca wystawcy i nazwa pospolita certyfikatu. 

Jeśli zwrotnego serwera proxy punktu końcowego jest połączenie przedstawia certyfikat, który jest wspólny nazwy i wystawców odcisk palca dopasowuje dowolną z wartości określone w tym miejscu, kanału SSL. W przypadku awarii odpowiadające szczegóły certyfikatu zwrotnego serwera proxy nie powiedzie się żądanie klienta z kodem stanu 502 (zły bramy). Wiersz stanu HTTP będzie również zawierać frazy "Nieprawidłowy certyfikat SSL." 

```json
{
"fabricSettings": [
          ...
          {
             "name": "ApplicationGateway/Http/ServiceCommonNameAndIssuer",
            "parameters": [
              {
                "name": "WinFabric-Test-Certificate-CN1",
                "value": "b3 44 9b 01 8d 0f 68 39 a2 c5 d6 2b 5b 6c 6a c8 22 b4 22 11"
              },
              {
                "name": "WinFabric-Test-Certificate-CN2",
                "value": "b3 44 9b 01 8d 0f 68 39 a2 c5 d6 2b 5b 6c 6a c8 22 11 33 44"
              }
            ]
          }
        ],
        ...
}
```


- **ServiceCertificateThumbprints**: zwrotny serwer proxy zweryfikuje oparte na jego odcisk palca certyfikatu usługi proxy. Użytkownik może przejść tej trasy, gdy usługi są skonfigurowane przy użyciu własnych podpisany certyfikaty: Określ **ApplicationCertificateValidationPolicy** z wartością **ServiceCertificateThumbprints** w sekcja parametrów elementu bramy aplikacji/Http.

```json
{
"fabricSettings": [
          ...
          {
            "name": "ApplicationGateway/Http",
            "parameters": [
              {
                "name": "ApplicationCertificateValidationPolicy",
                "value": "ServiceCertificateThumbprints"
              }
            ]
          }
        ],
        ...
}
```

Również określić odciski palców z **ServiceCertificateThumbprints** wpis w sekcji parametrów elementu bramy aplikacji/Http. Wiele odciski palców można określić jako listę rozdzielaną przecinkami, w polu wartość, jak pokazano poniżej:

```json
{
"fabricSettings": [
          ...
          {
            "name": "ApplicationGateway/Http",
            "parameters": [
                ...
              {
                "name": "ServiceCertificateThumbprints",
                "value": "78 12 20 5a 39 d2 23 76 da a0 37 f0 5a ed e3 60 1a 7e 64 bf,78 12 20 5a 39 d2 23 76 da a0 37 f0 5a ed e3 60 1a 7e 64 b9"
              }
            ]
          }
        ],
        ...
}
```

Jeśli odcisk palca certyfikatu serwera znajduje się w tej pozycji konfiguracji, zwrotny serwer proxy pomyślnego połączenia SSL. W przeciwnym razie zakończy połączenie i nie powiodło się żądanie klienta z 502 (zły bramy). Wiersz stanu HTTP będzie również zawierać frazy "Nieprawidłowy certyfikat SSL."

## <a name="endpoint-selection-logic-when-services-expose-secure-as-well-as-unsecured-endpoints"></a>Punkt końcowy zaznaczenia logiki podczas usług ekspozycji punktów końcowych, bezpieczne, jak również niezabezpieczona
Sieć szkieletowa usług obsługuje konfigurowanie wiele punktów końcowych dla usługi. Zobacz [Określanie zasobów w manifeście usługi](service-fabric-service-manifest-resources.md).

Zwrotny serwer proxy wybiera jeden z punktów końcowych do przekazywania żądań na podstawie **ListenerName** parametr zapytania. Jeśli nie zostanie określony, ją wybrać dowolnego punktu końcowego z listy punktów końcowych. Teraz, może to być punkt końcowy HTTP lub HTTPS. Mogą istnieć scenariusze/wymagania miejscu zwrotnego serwera proxy do działania w "tylko tryb bezpieczny", tj nie chcesz bezpiecznego zwrotnego serwera proxy do przekazywania żądań do punktów końcowych niezabezpieczona. Można to osiągnąć, określając **SecureOnlyMode** pozycji konfiguracji z wartością **true** w sekcji parametrów elementu bramy aplikacji/Http.   

```json
{
"fabricSettings": [
          ...
          {
            "name": "ApplicationGateway/Http",
            "parameters": [
                ...
              {
                "name": "SecureOnlyMode",
                "value": true
              }
            ]
          }
        ],
        ...
}
```

> 
> Podczas działania w **SecureOnlyMode**, jeśli określono klienta **ListenerName** odpowiadającej HTTP(unsecured) punktu końcowego, zwrotnego serwera proxy nie powiodło się żądanie z 404 kod stanu HTTP (nie znaleziono).

## <a name="setting-up-client-certificate-authentication-through-the-reverse-proxy"></a>Konfigurowanie uwierzytelniania certyfikatu klienta przy użyciu zwrotnego serwera proxy
Kończenie żądań SSL odbywa się na zwrotnego serwera proxy i nie zostały utracone wszystkie dane certyfikatu klienta. Usługi uwierzytelniania certyfikatu klienta, należy ustawić **ForwardClientCertificate** ustawienia w sekcji parametrów elementu bramy aplikacji/Http.

1. Gdy **ForwardClientCertificate** ustawiono **false**, odwrócić serwera proxy nie będą wymagane dla certyfikatu klienta podczas jego uzgadniania protokołu SSL z klientem.
Jest to zachowanie domyślne.

2. Gdy **ForwardClientCertificate** ma ustawioną wartość **true**, wstecznego żądania certyfikatu klienta serwera proxy podczas jego uzgadniania protokołu SSL przy użyciu klienta.
Następnie prześle je klienta danych certyfikatu w niestandardowy nagłówek HTTP o nazwie **certyfikat klienta X**. Wartość nagłówka jest ciągiem formatu PEM kodowanie base64 certyfikatu klienta. Usługa może powiodło się/Niepowodzenie żądania z kodem stanu odpowiednie po sprawdzeniu danych certyfikatu.
Jeśli klient nie przedstawić certyfikat, zwrotny serwer proxy przekazuje pusty nagłówek i zezwolić, w przypadku obsługi.

> Zwrotny serwer proxy jest tylko usługa przesyłania dalej. Nie będzie wykonywać żadnych weryfikacji certyfikatu klienta.


## <a name="next-steps"></a>Kolejne kroki
* Zapoznaj się [Konfiguruj zwrotnego serwera proxy do nawiązania bezpiecznego usług](https://github.com/ChackDan/Service-Fabric/tree/master/ARM%20Templates/ReverseProxySecureSample#configure-reverse-proxy-to-connect-to-secure-services) dla usługi Azure Resource Manager przykłady szablonu, aby skonfigurować zabezpieczenia zwrotny serwer proxy przy użyciu certyfikatu innej usługi opcji weryfikacji.
* Zobacz przykład protokołu HTTP do komunikacji między usługami w [przykładowy projekt w witrynie GitHub](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started).
* [Zdalne wywołania procedur z usług zdalnych niezawodne usługi](service-fabric-reliable-services-communication-remoting.md)
* [Interfejs API, który używa OWIN w niezawodnej usługi sieci Web](service-fabric-reliable-services-communication-webapi.md)
* [Zarządzanie certyfikatami klastra](service-fabric-cluster-security-update-certs-azure.md)
