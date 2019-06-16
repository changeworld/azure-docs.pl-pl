---
title: Usługa Azure Service Fabric odwrotnego serwera proxy bezpiecznej komunikacji | Dokumentacja firmy Microsoft
description: Konfigurowanie zwrotnego serwera proxy, aby umożliwić bezpieczną komunikację end-to-end.
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
ms.openlocfilehash: d8a11a3289037602535d1b5727d041e376012bd8
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60837848"
---
# <a name="connect-to-a-secure-service-with-the-reverse-proxy"></a>Łączenie z usługą bezpieczny przy użyciu zwrotnego serwera proxy

W tym artykule wyjaśniono, jak nawiązać bezpieczne połączenie między zwrotny serwer proxy i usług, umożliwiając w ten sposób bezpiecznego kanału typu end to end. Aby dowiedzieć się więcej na temat zwrotnego serwera proxy, zobacz [zwrotny serwer proxy w usłudze Azure Service Fabric](service-fabric-reverseproxy.md)

Łączenie się z usługami bezpieczne jest obsługiwana tylko wtedy, gdy zwrotny serwer proxy jest skonfigurowana do nasłuchiwania przy użyciu protokołu HTTPS. W tym artykule przyjęto założenie, że jest to możliwe.
Zapoznaj się [Instalatora zwrotny serwer proxy w usłudze Azure Service Fabric](service-fabric-reverseproxy-setup.md) na konfigurowanie zwrotnego serwera proxy w usłudze Service Fabric.

## <a name="secure-connection-establishment-between-the-reverse-proxy-and-services"></a>Ustanowienia bezpiecznego połączenia między zwrotny serwer proxy i usługami 

### <a name="reverse-proxy-authenticating-to-services"></a>Zwrotny serwer proxy, którzy uwierzytelniają się do usługi:
Zwrotny serwer proxy identyfikuje do usług za pomocą swojego certyfikatu. Azure klastrów certyfikatu jest określony za pomocą ***reverseProxyCertificate*** właściwość [ **Microsoft.ServiceFabric/clusters** ](https://docs.microsoft.com/azure/templates/microsoft.servicefabric/clusters) [zasobów Wpisz sekcji](../azure-resource-manager/resource-group-authoring-templates.md) szablonu usługi Resource Manager. W przypadku klastrów autonomicznych certyfikatu jest określony za pomocą albo ***ReverseProxyCertificate*** lub ***ReverseProxyCertificateCommonNames*** właściwość **zabezpieczeń**sekcji ClusterConfig.json. Aby dowiedzieć się więcej, zobacz [Włącz zwrotny serwer proxy na autonomicznych klastrów](service-fabric-reverseproxy-setup.md#enable-reverse-proxy-on-standalone-clusters). 

Usługi można zaimplementować logikę, aby zweryfikować certyfikat przedstawiony przez zwrotny serwer proxy. Usługi można określić szczegóły certyfikatu klienta akceptowane zgodnie z ustawieniami konfiguracji w pakiecie konfiguracji. To może być odczytywana w czasie wykonywania i służący do weryfikowania certyfikat przedstawiony przez zwrotny serwer proxy. Zapoznaj się [Zarządzanie parametrami aplikacji](service-fabric-manage-multiple-environment-app-configuration.md) można dodać ustawień konfiguracji. 

### <a name="reverse-proxy-verifying-the-services-identity-via-the-certificate-presented-by-the-service"></a>Weryfikowanie tożsamości usługi za pośrednictwem certyfikat przedstawiony przez usługę zwrotny serwer proxy:
Zwrotny serwer proxy obsługuje następujące zasady, przeprowadzenie weryfikacji certyfikatu serwera certyfikaty przedstawiane przez usługi: Brak, ServiceCommonNameAndIssuer i ServiceCertificateThumbprints.
Aby wybrać zasady dla zwrotnego serwera proxy do użycia, należy określić **ApplicationCertificateValidationPolicy** w **bramy ApplicationGateway/Http** sekcji [element fabricSettings](service-fabric-cluster-fabric-settings.md).

Następna sekcja zawiera szczegóły konfiguracji dla każdego z tych opcji.

### <a name="service-certificate-validation-options"></a>Opcje weryfikacji certyfikatu usługi 

- **Brak**: Zwrotny serwer proxy Pomija weryfikację certyfikatu usługi proxy oraz bezpiecznego połączenia. To zachowanie domyślne.
Określ **ApplicationCertificateValidationPolicy** wartością **Brak** w [ **bramy ApplicationGateway/Http** ](./service-fabric-cluster-fabric-settings.md#applicationgatewayhttp) sekcji.

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

- **ServiceCommonNameAndIssuer**: Zwrotny serwer proxy weryfikuje certyfikat przedstawiony przez usługę, na podstawie nazwa pospolita certyfikatu i odcisk palca wystawcy bezpośredniego: Określ **ApplicationCertificateValidationPolicy** wartością **ServiceCommonNameAndIssuer** w [ **bramy ApplicationGateway/Http** ](./service-fabric-cluster-fabric-settings.md#applicationgatewayhttp) sekcji.

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

   Aby określić listę Nazwa pospolita usługi i odciski palca wystawcy, Dodaj [ **bramy ApplicationGateway/Http/ServiceCommonNameAndIssuer** ](./service-fabric-cluster-fabric-settings.md#applicationgatewayhttpservicecommonnameandissuer) sekcji **element fabricSettings**, jak pokazano poniżej. Można dodać wiele certyfikatów typowych par nazw i Wystawca odcisk palca **parametry** tablicy. 

   Jeśli zwrotny serwer proxy punktu końcowego nawiązuje połączenie z przedstawia certyfikat, który jest często nazwy i Wystawca odcisk palca pasuje do żadnej z wartości określone w tym miejscu, kanału SSL zostanie nawiązane. 
   W przypadku awarii, aby dopasować szczegóły certyfikatu zwrotny serwer proxy nie powiodło się żądanie klienta z kodem stanu 502 (Zła brama). Wiersz stanu HTTP będzie również zawierać frazę "Nieprawidłowy certyfikat SSL." 

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

- **ServiceCertificateThumbprints**: Zwrotny serwer proxy zweryfikuje certyfikatu serwerem proxy usługi, w oparciu o jego odcisk palca. Można go tę trasę, gdy usługi są skonfigurowane przy użyciu samodzielnie podpisanych certyfikatów: Określ **ApplicationCertificateValidationPolicy** wartością **ServiceCertificateThumbprints** w [ **bramy ApplicationGateway/Http** ](./service-fabric-cluster-fabric-settings.md#applicationgatewayhttp) sekcji.

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

   Również określić odcisków palca z **ServiceCertificateThumbprints** wpis **bramy ApplicationGateway/Http** sekcji. Wiele odciski palców można określić jako listę rozdzielaną przecinkami, w polu wartość, jak pokazano poniżej:

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

   Jeśli odcisk palca certyfikatu serwera znajduje się w tym wpisie konfiguracji, zwrotny serwer proxy powiedzie się połączenia SSL. W przeciwnym razie przerywa połączenie i nie powiodło się żądanie klienta za pomocą 502 (Zła brama). Wiersz stanu HTTP będzie również zawierać frazę "Nieprawidłowy certyfikat SSL."

## <a name="endpoint-selection-logic-when-services-expose-secure-as-well-as-unsecured-endpoints"></a>Punkt końcowy logikę wyboru, gdy usługi prezentują bezpieczne, jak również niezabezpieczony punktów końcowych
Usługa Service fabric obsługuje konfigurowanie wielu punktów końcowych usługi. Aby uzyskać więcej informacji, zobacz [określanie zasobów w manifeście usługi](service-fabric-service-manifest-resources.md).

Zwrotny serwer proxy wybiera jeden z punktów końcowych do przekazywania żądań na podstawie **ListenerName** parametr w zapytania [identyfikator URI usługi](./service-fabric-reverseproxy.md#uri-format-for-addressing-services-by-using-the-reverse-proxy). Jeśli **ListenerName** parametr nie zostanie określony, zwrotny serwer proxy można wybrać dowolnego punktu końcowego z listy punktów końcowych. W zależności od punkty końcowe skonfigurowane dla usługi wybrany punkt końcowy może być punkt końcowy HTTP lub HTTPS. Może istnieć scenariuszy lub wymagań, w którym ma działać w trybie "tylko do bezpiecznego"; zwrotny serwer proxy oznacza to, że nie chcesz, bezpieczne zwrotny serwer proxy do przekazywania żądań do niezabezpieczonych punktów końcowych. Aby ustawić tryb tylko do bezpiecznego zwrotny serwer proxy, określ **SecureOnlyMode** pozycji konfiguracji z wartością **true** w [ **bramy ApplicationGateway/Http** ](./service-fabric-cluster-fabric-settings.md#applicationgatewayhttp) sekcji.   

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

> [!NOTE]
> Podczas pracy w **SecureOnlyMode**, jeśli klient ma określony **ListenerName** odpowiadający HTTP(unsecured) punktu końcowego, zwrotny serwer proxy nie powiodło się żądania z kodem stanu 404 (nie znaleziono) protokołu HTTP.

## <a name="setting-up-client-certificate-authentication-through-the-reverse-proxy"></a>Konfigurowanie uwierzytelniania certyfikatu klienta przy użyciu zwrotnego serwera proxy
Kończenie żądań SSL odbywa się na zwrotny serwer proxy, a wszystkie dane certyfikat klienta zostanie utracony. Dla usług do wykonywania uwierzytelniania certyfikatu klienta, należy określić **ForwardClientCertificate** w [ **bramy ApplicationGateway/Http** ](./service-fabric-cluster-fabric-settings.md#applicationgatewayhttp) sekcji.

1. Gdy **ForwardClientCertificate** jest ustawiona na **false**, odwrócić serwera proxy nie będzie żądać certyfikatu klienta podczas jego uzgadniania protokołu SSL przy użyciu klienta.
To zachowanie domyślne.

2. Podczas **ForwardClientCertificate** jest ustawiona na **true**, zwrotny serwer proxy wymaga certyfikatu klienta podczas jego procedury uzgadniania protokołu SSL za pomocą klienta programu.
Go następnie przekaże klienta danych certyfikatu w niestandardowy nagłówek HTTP o nazwie **certyfikat klienta X**. Wartość nagłówka jest ciągiem formatu PEM zakodowane w formacie base64, certyfikat klienta. Usługa może Powodzenie/Niepowodzenie żądania z kodem stanu odpowiednie po sprawdzeniu danych certyfikatu.
Jeśli klient nie przedstawić certyfikat, zwrotny serwer proxy przekazuje pusty nagłówek i pozwól usłudze obsłużyć przypadek.

> [!NOTE]
> Zwrotny serwer proxy to ledwie usługi przesyłania dalej. Nie wykonuje żadnych weryfikacji certyfikatu klienta.


## <a name="next-steps"></a>Kolejne kroki
* [Instalowanie i konfigurowanie zwrotnego serwera proxy w klastrze](service-fabric-reverseproxy-setup.md).
* Zapoznaj się [Konfigurowanie zwrotnego serwera proxy do nawiązywania bezpiecznej usługami](https://github.com/ChackDan/Service-Fabric/tree/master/ARM%20Templates/ReverseProxySecureSample#configure-reverse-proxy-to-connect-to-secure-services) dla usługi Azure Resource Manager przykłady szablonów, aby skonfigurować bezpieczny zwrotny serwer proxy, za pomocą certyfikatu usługi różne opcje weryfikacji.
* Zobacz przykład protokołu HTTP do komunikacji między usługami w [przykładowy projekt w witrynie GitHub](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started).
* [Zdalne wywołania procedur z wywołaniem funkcji zdalnych usług Reliable Services](service-fabric-reliable-services-communication-remoting.md)
* [Internetowy interfejs API, który używa OWIN usług Reliable Services](service-fabric-reliable-services-communication-webapi.md)
* [Zarządzanie certyfikatami klastra](service-fabric-cluster-security-update-certs-azure.md)
