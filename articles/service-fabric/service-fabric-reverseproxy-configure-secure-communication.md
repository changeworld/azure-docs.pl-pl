---
title: Bezpieczna komunikacja z serwerem proxy odwrotnej usługi Azure Service
description: Skonfiguruj odwrotny serwer proxy, aby włączyć bezpieczną komunikację end-to-end w aplikacji sieci szkieletowej usług Azure.
author: kavyako
ms.topic: conceptual
ms.date: 08/10/2017
ms.author: kavyako
ms.openlocfilehash: 4cfeaf34a39231ffa91ea970a61f66632bae40c7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79282252"
---
# <a name="connect-to-a-secure-service-with-the-reverse-proxy"></a>Connect to a secure service with the reverse proxy (Łączenie z bezpieczną usługą przy użyciu zwrotnego serwera proxy)

W tym artykule wyjaśniono, jak ustanowić bezpieczne połączenie między odwrotnym serwerem proxy a usługami, umożliwiając w ten sposób zakończenie bezpiecznego kanału. Aby dowiedzieć się więcej o odwrotnym serwerze proxy, zobacz [Odwracanie serwera proxy w sieci szkieletowej usług Azure](service-fabric-reverseproxy.md)

Łączenie się z usługami bezpiecznymi jest obsługiwane tylko wtedy, gdy serwer proxy odwrotny jest skonfigurowany do nasłuchiwania na https. W tym artykule przyjęto założenie, że tak jest.
Zapoznaj się z [instalatorem odwrotnego serwera proxy w sieci szkieletowej usług Azure,](service-fabric-reverseproxy-setup.md) aby skonfigurować odwrotny serwer proxy w sieci szkieletowej usług.

## <a name="secure-connection-establishment-between-the-reverse-proxy-and-services"></a>Bezpieczne połączenie między odwrotnym serwerem proxy a usługami 

### <a name="reverse-proxy-authenticating-to-services"></a>Odwróć uwierzytelnianie serwera proxy do usług:
Odwrotny serwer proxy identyfikuje się do usług przy użyciu certyfikatu. W przypadku klastrów platformy Azure certyfikat jest określony za pomocą właściwości ***reverseProxyCertificate*** w sekcji [**Microsoft.ServiceFabric/clusters**](https://docs.microsoft.com/azure/templates/microsoft.servicefabric/clusters) [Resource type szablonu](../azure-resource-manager/templates/template-syntax.md) Menedżera zasobów. W przypadku klastrów autonomicznych certyfikat jest określony za pomocą właściwości ***ReverseProxyCertificate*** lub ***ReverseProxyCertificateCommonNames*** w sekcji **Zabezpieczenia** w pliku ClusterConfig.json. Aby dowiedzieć się więcej, zobacz [Włączanie odwrotnego serwera proxy w klastrach autonomicznych](service-fabric-reverseproxy-setup.md#enable-reverse-proxy-on-standalone-clusters). 

Usługi można zaimplementować logikę, aby zweryfikować certyfikat przedstawiony przez odwrotnego serwera proxy. Usługi mogą określać szczegóły certyfikatu akceptowanego klienta jako ustawienia konfiguracji w pakiecie konfiguracyjnym. Można to odczytać w czasie wykonywania i służyć do sprawdzania poprawności certyfikatu przedstawionego przez odwrotny serwer proxy. Aby dodać ustawienia konfiguracji, zobacz [Zarządzanie parametrami aplikacji.](service-fabric-manage-multiple-environment-app-configuration.md) 

### <a name="reverse-proxy-verifying-the-services-identity-via-the-certificate-presented-by-the-service"></a>Odwróć serwer proxy weryfikujący tożsamość usługi za pomocą certyfikatu przedstawionego przez usługę:
Serwer proxy odwrotny obsługuje następujące zasady do sprawdzania poprawności certyfikatów serwera certyfikatów prezentowanych przez usługi: Brak, ServiceCommonNameAndIssuer i ServiceCertificateThumbprints.
Aby wybrać zasady dla odwrotnego serwera proxy do użycia, określ **ApplicationCertificateValidationPolicy** w **ApplicationGateway/Http** sekcji w [fabricSettings](service-fabric-cluster-fabric-settings.md).

W następnej sekcji przedstawiono szczegóły konfiguracji dla każdej z tych opcji.

### <a name="service-certificate-validation-options"></a>Opcje sprawdzania poprawności certyfikatu usługi 

- **Brak:** Odwrócony serwer proxy pomija weryfikację certyfikatu usługi proxy i ustanawia bezpieczne połączenie. Jest to zachowanie domyślne.
Określ **ApplicationCertificateValidationPolicy** z wartością **Brak** w [**ApplicationGateway/Http**](./service-fabric-cluster-fabric-settings.md#applicationgatewayhttp) sekcji.

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

- **ServiceCommonNameAndIssuer**: Reverse proxy weryfikuje certyfikat przedstawiony przez usługę na podstawie nazwy pospolitej certyfikatu i odcisku palca natychmiastowego wystawcy: Określ **ApplicationCertificateValidationPolicy** z wartością **ServiceCommonNameAndIssuer** w [**applicationgateway/http**](./service-fabric-cluster-fabric-settings.md#applicationgatewayhttp) sekcji.

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

   Aby określić listę nazwy pospolitej usługi i odciski palców wystawcy, dodaj [**applicationgateway/http/ServiceCommonNameAndIssuer**](./service-fabric-cluster-fabric-settings.md#applicationgatewayhttpservicecommonnameandissuer) sekcji w **fabricSettings**, jak pokazano poniżej. W tablicy **parametrów** można dodać wiele nazw pospolitych certyfikatów i par odcisków palców wystawcy. 

   Jeśli odwrotny serwer proxy punktu końcowego łączy się z przedstawia certyfikat, który ma wspólną nazwę i odcisk palca wystawcy pasuje do dowolnej wartości określonej w tym miejscu, ustanawia się kanał SSL. 
   W przypadku niepowodzenia dopasowania szczegółów certyfikatu odwrotny serwer proxy nie powiedzie się żądanie klienta z kodem stanu 502 (Bad Gateway). Wiersz stanu HTTP będzie również zawierał frazę "Nieprawidłowy certyfikat SSL". 

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

- **ServiceCertificateThumbprints:** Reverse proxy zweryfikuje certyfikat usługi proxy na podstawie jego odcisku palca. Można wybrać, aby przejść tę trasę, gdy usługi są skonfigurowane z certyfikatami z podpisem własnym: Określ **ApplicationCertificateValidationPolicy** z wartości **ServiceCertificateThumbprints** w [**ApplicationGateway/Http**](./service-fabric-cluster-fabric-settings.md#applicationgatewayhttp) sekcji.

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

   Należy również określić odciski palców z **servicecertificateThumbprints** wpis w **ApplicationGateway/Http** sekcji. W polu wartości można określić wiele odcisków palców jako listę oddzieloną przecinkami, jak pokazano poniżej:

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

   Jeśli odcisk palca certyfikatu serwera znajduje się na liście w tym wpisie konfiguracji, serwer proxy wsteczny zakończy się pomyślnie połączeniem SSL. W przeciwnym razie kończy połączenie i kończy pracę żądania klienta za pomocą 502 (Bad Gateway). Wiersz stanu HTTP będzie również zawierał frazę "Nieprawidłowy certyfikat SSL".

## <a name="endpoint-selection-logic-when-services-expose-secure-as-well-as-unsecured-endpoints"></a>Logika wyboru punktu końcowego, gdy usługi ujawniają bezpieczne i niezabezpieczone punkty końcowe
Sieci szkieletowej usług obsługuje konfigurowanie wielu punktów końcowych dla usługi. Aby uzyskać więcej informacji, zobacz [Określanie zasobów w manifeście usługi](service-fabric-service-manifest-resources.md).

Odwrócony serwer proxy wybiera jeden z punktów końcowych do przekazania żądania na podstawie parametru zapytania **ListenerName** w [usłudze URI](./service-fabric-reverseproxy.md#uri-format-for-addressing-services-by-using-the-reverse-proxy). Jeśli parametr **ListenerName** nie jest określony, reverse proxy można wybrać dowolny punkt końcowy z listy punktów końcowych. W zależności od punktów końcowych skonfigurowanych dla usługi wybrany punkt końcowy może być punktem końcowym HTTP lub HTTPS. Mogą istnieć scenariusze lub wymagania, w których serwer proxy odwrotnej ma działać w trybie "tylko bezpieczne"; oznacza to, że nie chcesz, aby bezpieczny serwer proxy wsteczny przesyłał dalej żądania do niezabezpieczonych punktów końcowych. Aby ustawić odwrotny serwer proxy do trybu bezpiecznego tylko, należy określić wpis konfiguracji **SecureOnlyMode** o wartości **true** w [**ApplicationGateway/Http**](./service-fabric-cluster-fabric-settings.md#applicationgatewayhttp) sekcji.   

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
> Podczas pracy w **SecureOnlyMode**, jeśli klient określił **ListenerName** odpowiadające http(niezabezpieczone) punkt końcowy, odwrotny serwer proxy nie powiedzie się żądanie z 404 (Nie znaleziono) kod stanu HTTP.

## <a name="setting-up-client-certificate-authentication-through-the-reverse-proxy"></a>Konfigurowanie uwierzytelniania certyfikatu klienta za pośrednictwem odwrotnego serwera proxy
Zakończenie SSL odbywa się na serwerze proxy odwrotnej i wszystkie dane certyfikatu klienta są tracone. Aby usługi przeprowadzały uwierzytelnianie certyfikatów klienta, określ ustawienie **ForwardClientCertificate** w sekcji [**ApplicationGateway/Http.**](./service-fabric-cluster-fabric-settings.md#applicationgatewayhttp)

1. Gdy **forwardClientCertificate** jest ustawiona na **false,** reverse proxy nie zażąda certyfikatu klienta podczas uzgadniania SSL z klientem.
Jest to zachowanie domyślne.

2. Gdy **forwardClientCertificate** jest ustawiona na **true,** reverse proxy żąda certyfikatu klienta podczas uzgadniania SSL z klientem.
Następnie przekaże dane certyfikatu klienta w niestandardowym nagłówku HTTP o nazwie **X-Client-Certificate**. Wartość nagłówka jest ciągiem formatu PEM zakodowanego w formacie PEM base64 certyfikatu klienta. Usługa może zakończyć się powodzeniem/niepowodzeniem żądania z odpowiednim kodem stanu po sprawdzeniu danych certyfikatu.
Jeśli klient nie przedstawi certyfikatu, reverse proxy przesyła dalej pusty nagłówek i zezwala na obsługę sprawy przez usługę.

> [!NOTE]
> Odwrócony serwer proxy jest zwykłym usługą przesyłania dalej. Nie będzie wykonywać sprawdzania poprawności certyfikatu klienta.


## <a name="next-steps"></a>Następne kroki
* [Konfigurowanie i konfigurowanie odwrotnego serwera proxy w klastrze](service-fabric-reverseproxy-setup.md).
* Zapoznaj się [z pozycję Konfigurowanie odwrotnego serwera proxy w celu łączenia się z bezpiecznymi usługami](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/Reverse-Proxy-Sample#configure-reverse-proxy-to-connect-to-secure-services)
* Zobacz przykład komunikacji HTTP między usługami w [przykładowym projekcie w usłudze GitHub](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started).
* [Zdalne wywołania procedury z obsługą komunikacji zdalnej z niezawodnymi usługami](service-fabric-reliable-services-communication-remoting.md)
* [Internetowy interfejs API, który używa OWIN w niezawodnych usługach](service-fabric-reliable-services-communication-webapi.md)
* [Zarządzanie certyfikatami klastra](service-fabric-cluster-security-update-certs-azure.md)
