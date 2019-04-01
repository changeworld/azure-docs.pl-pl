---
title: Metody uwierzytelniania dla usługi Azure Security Center w wersji zapoznawczej IoT | Dokumentacja firmy Microsoft
description: Dowiedz się więcej o różnych dostępnych metod uwierzytelniania w przypadku używania usługi Azure Security Center dla usługi IoT.
services: ascforiot
documentationcenter: na
author: mlottner
manager: barbkess
editor: ''
ms.assetid: 10b38f20-b755-48cc-8a88-69828c17a108
ms.service: ascforiot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/26/2019
ms.author: mlottner
ms.openlocfilehash: ec87c2b65728d8ac29daa90de36271e24cd85c0e
ms.sourcegitcommit: 563f8240f045620b13f9a9a3ebfe0ff10d6787a2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/01/2019
ms.locfileid: "58758394"
---
# <a name="security-agent-authentication-methods"></a>Metody uwierzytelniania agenta zabezpieczeń 

> [!IMPORTANT]
> Centrum zabezpieczeń Azure dla IoT jest obecnie w publicznej wersji zapoznawczej.
> Ta wersja zapoznawcza jest dostarczane bez umowy dotyczącej poziomu usług, a nie jest zalecane w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone. Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

W tym artykule opisano różnych metod uwierzytelniania służących z agentem AzureIoTSecurity do uwierzytelniania za pomocą usługi IoT Hub.

Dla każdego urządzenia dołączone do usługi Azure Security Center (ASC) dotyczące IoT w usłudze IoT Hub modułu zabezpieczeń jest wymagany. W celu uwierzytelnienia urządzenia ASC IoT można użyć jednej z dwóch metod. Wybierz metodę, która jest najlepsza dla istniejącego rozwiązania IoT. 

> [!div class="checklist"]
> * Opcja modułu zabezpieczeń
> * Opcja urządzenia

## <a name="authentication-methods"></a>Metody uwierzytelniania

Te dwie metody dla agenta AzureIoTSecurity w celu przeprowadzenia uwierzytelniania:

 - **Moduł** tryb uwierzytelniania<br>
   Moduł jest uwierzytelniany niezależnie od bliźniaczej reprezentacji urządzenia.
   Użyj tego typu uwierzytelniania, jeśli chcesz, aby agent zabezpieczeń przy użyciu metody uwierzytelniania dedykowane, za pośrednictwem modułu zabezpieczeń (tylko w przypadku klucza symetrycznego).
        
 - **Urządzenie** tryb uwierzytelniania<br>
    W przypadku tej metody agent zabezpieczeń najpierw uwierzytelnia się za pomocą tożsamości urządzenia. Po uwierzytelnieniu początkowej wykonuje ASC agenta IoT **REST** wywołania do Centrum IoT przy użyciu interfejsu API REST przy użyciu danych uwierzytelniania urządzenia. ASC agenta IoT następnie żąda metody uwierzytelniania modułu zabezpieczeń oraz danych z usługi IoT Hub. W ostatnim kroku ASC IoT agenta wykonuje uwierzytelnianie w usłudze ASC moduł IoT.
    
    Użyj tego typu uwierzytelniania, jeśli chcesz, aby agent zabezpieczeń, aby ponownie wykorzystać istniejącą metodę uwierzytelniania urządzeń (z podpisem własnym certyfikatu lub klucza symetrycznego). 

Zobacz [parametry instalacji agenta zabezpieczeń](#security-agent-installation-parameters) dowiesz się, jak skonfigurować.
                                
## <a name="authentication-methods-known-limitations"></a>Metody uwierzytelniania, znane ograniczenia

- **Moduł** tryb uwierzytelniania obsługuje tylko uwierzytelnianie za pomocą klucza symetrycznego.
- Certyfikatu podpisanego przez urząd certyfikacji nie jest obsługiwana przez **urządzenia** tryb uwierzytelniania.  

## <a name="security-agent-installation-parameters"></a>Parametry instalacji agenta zabezpieczeń

Gdy [wdrażanie agenta zabezpieczeń](how-to-deploy-agent.md), szczegółów uwierzytelniania musi zostać podana jako argumenty.
W poniższej tabeli opisano te argumenty.


|Parametr|Opis|Opcje|
|---------|---------------|---------------|
|**Tożsamość**|Tryb uwierzytelniania| **Moduł** lub **urządzenia**|
|**type**|Typ uwierzytelniania|**SymmetricKey** lub **SelfSignedCertificate**|
|**filePath**|Pełne ścieżki bezwzględnej do pliku zawierającego certyfikat i klucz symetryczny| |
|**gatewayHostname**|Nazwa FQDN usługi IoT Hub|Przykład: ContosoIotHub.azure-devices.net|
|**deviceId**|Identyfikator urządzenia|Przykład: MyDevice1|
|**certificateLocationKind**|Lokalizacja magazynu certyfikatów|**Plik_lokalny** lub **Store**|


Korzystając z skrypt instalacji agenta zabezpieczeń, następującą konfigurację odbywa się automatycznie.

Aby ręcznie edytować uwierzytelnienia agenta zabezpieczeń, przeprowadź edycję pliku konfiguracji. 

## <a name="change-authentication-method-after-deployment"></a>Zmień metodę uwierzytelniania po wdrożeniu

Podczas wdrażania agenta zabezpieczeń, za pomocą skryptu instalacji, automatycznie tworzony jest plik konfiguracji.

Aby zmienić metodę uwierzytelniania, po wdrożeniu, ręczne edytowanie pliku konfiguracji jest wymagana.


### <a name="c-based-security-agent"></a>C#-agent zabezpieczenia oparty na

Edytuj _Authentication.config_ z następującymi parametrami:

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

### <a name="c-based-security-agent"></a>Agent zabezpieczenia oparty na C

Edytuj _LocalConfiguration.json_ z następującymi parametrami:

```json
"Authentication" : {
    "Identity" : "",
    "AuthenticationMethod" : "",
    "FilePath" : "",
    "DeviceId" : "",
    "HostName" : ""
}
```

## <a name="see-also"></a>Zobacz także
- [Omówienie czynników zabezpieczeń](security-agent-architecture.md)
- [Wdróż agenta zabezpieczeń](how-to-deploy-agent.md)
- [Dostęp do danych pierwotnych zabezpieczeń](how-to-security-data-access.md)
