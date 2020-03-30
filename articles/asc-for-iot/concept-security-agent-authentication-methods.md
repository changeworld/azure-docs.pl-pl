---
title: Metody uwierzytelniania usługi Azure Security Center dla usługi IoT| Dokumenty firmy Microsoft
description: Dowiedz się więcej o różnych metodach uwierzytelniania dostępnych podczas korzystania z usługi Azure Security Center dla IoT.
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: 10b38f20-b755-48cc-8a88-69828c17a108
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/23/2019
ms.author: mlottner
ms.openlocfilehash: 16f7f91e02d118d9f9a295ebb79a6cd0187dd9fd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "68596471"
---
# <a name="security-agent-authentication-methods"></a>Metody uwierzytelniania agenta zabezpieczeń 

W tym artykule opisano różne metody uwierzytelniania, których można używać za pomocą agenta AzureIoTSecurity do uwierzytelniania za pomocą usługi IoT Hub.

Dla każdego urządzenia dołączanego do usługi Azure Security Center dla IoT w centrum IoT hub wymagany jest moduł zabezpieczeń. Aby uwierzytelnić urządzenie, usługa Azure Security Center dla IoT może użyć jednej z dwóch metod. Wybierz metodę, która najlepiej sprawdza się w istniejącym rozwiązaniu IoT. 

> [!div class="checklist"]
> * Opcja SecurityModule
> * Opcja urządzenia

## <a name="authentication-methods"></a>Metody uwierzytelniania

Dwie metody azureiotsecurity agenta do wykonywania uwierzytelniania:

 - Tryb uwierzytelniania **SecurityModule**<br>
   Agent jest uwierzytelniony przy użyciu tożsamości modułu zabezpieczeń niezależnie od tożsamości urządzenia.
   Użyj tego typu uwierzytelniania, jeśli chcesz, aby agent zabezpieczeń używał dedykowanej metody uwierzytelniania za pomocą modułu zabezpieczeń (tylko klucz symetryczny).
        
 - **Tryb** uwierzytelniania urządzenia<br>
    W tej metodzie agent zabezpieczeń najpierw uwierzytelnia się przy użyciu tożsamości urządzenia. Po uwierzytelnieniu początkowym agent Usługi Azure Security Center dla IoT wykonuje wywołanie **REST** do Centrum IoT przy użyciu interfejsu API REST z danymi uwierzytelniania urządzenia. Agent Usługi Azure Security Center dla IoT żąda następnie metody uwierzytelniania modułu zabezpieczeń i danych z Centrum IoT Hub. W ostatnim kroku agent Usługi Azure Security Center dla IoT wykonuje uwierzytelnianie względem modułu Usługi Azure Security Center dla IoT.
    
    Użyj tego typu uwierzytelniania, jeśli agent zabezpieczeń ma ponownie użyć istniejącej metody uwierzytelniania urządzenia (certyfikatu z podpisem własnym lub klucza symetrycznego). 

Zobacz [Parametry instalacji agenta zabezpieczeń,](#security-agent-installation-parameters) aby dowiedzieć się, jak skonfigurować.
                                
## <a name="authentication-methods-known-limitations"></a>Znane ograniczenia metod uwierzytelniania

- Tryb uwierzytelniania **SecurityModule** obsługuje tylko uwierzytelnianie za pomocą klucza symetrycznego.
- Certyfikat z podpisem urzędu certyfikacji nie jest obsługiwany w trybie uwierzytelniania **urządzenia.**  

## <a name="security-agent-installation-parameters"></a>Parametry instalacji agenta zabezpieczeń

Podczas [wdrażania agenta zabezpieczeń](how-to-deploy-agent.md)szczegóły uwierzytelniania muszą być podane jako argumenty.
Te argumenty są udokumentowane w poniższej tabeli.


|Nazwa parametru systemu Linux | Nazwa parametru systemu Windows | Skrócony parametr |Opis|Opcje|
|---------------------|---------------|---------|---------------|---------------|
|uwierzytelnianie- tożsamość|UwierzytelnianieTożerność|Aui|Tożsamość uwierzytelniania| **Urządzenie zabezpieczające lub** **urządzenie**|
|metoda uwierzytelniania|Authenticationmethod|Aum|Metoda uwierzytelniania|**Symetrycznykey** lub **SelfSignedCertificate**|
|ścieżka pliku|Filepath|k|Bezwzględna pełna ścieżka dla pliku zawierającego certyfikat lub klucz symetryczny| |
|nazwa hosta|HostName|Hn|FQDN centrum IoT Hub|Przykład: ContosoIotHub.azure-devices.net|
|identyfikator urządzenia|DeviceId|Di|Identyfikator urządzenia|Przykład: MyDevice1|
|certyfikat-lokalizacja-rodzaj|CertificateLocationKind|Cl|Lokalizacja przechowywania certyfikatów|**LocalFile** lub **Sklep**|
|


Podczas korzystania ze skryptu agenta zabezpieczeń instalacji następująca konfiguracja jest wykonywana automatycznie. Aby ręcznie edytować uwierzytelnianie agenta zabezpieczeń, edytuj plik konfiguracyjny. 

## <a name="change-authentication-method-after-deployment"></a>Zmień metodę uwierzytelniania po wdrożeniu

Podczas wdrażania agenta zabezpieczeń ze skryptem instalacyjnym plik konfiguracyjny jest tworzony automatycznie.

Aby zmienić metody uwierzytelniania po wdrożeniu, wymagana jest ręczna edycja pliku konfiguracyjnego.


### <a name="c-based-security-agent"></a>Agent zabezpieczeń oparty na języku C#

Edytuj _authentication.config_ z następującymi parametrami:

```xml
<Authentication>
  <add key="deviceId" value=""/>
  <add key="gatewayHostname" value=""/>
  <add key="filePath" value=""/>
  <add key="type" value=""/>
  <add key="identity" value=""/>
  <add key="certificateLocationKind" value="" />
</Authentication>
```

### <a name="c-based-security-agent"></a>Agent zabezpieczeń oparty na języku C

Edytuj _plik LocalConfiguration.json_ z następującymi parametrami:

```json
"Authentication" : {
    "Identity" : "",
    "AuthenticationMethod" : "",
    "FilePath" : "",
    "DeviceId" : "",
    "HostName" : ""
}
```

## <a name="see-also"></a>Zobacz też
- [Omówienie agentów zabezpieczeń](security-agent-architecture.md)
- [Wdrażanie agenta zabezpieczeń](how-to-deploy-agent.md)
- [Dostęp do nieprzetworzonych danych zabezpieczeń](how-to-security-data-access.md)
