---
title: Metody uwierzytelniania dla ASC IoT (wersja zapoznawcza) | Dokumentacja firmy Microsoft
description: Dowiedz się więcej o różnych dostępnych metod uwierzytelniania w przypadku używania usługi ASC dla usługi IoT.
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
ms.openlocfilehash: 23bc4d0df1c8124ec225ac31239c7acb3f1ab546
ms.sourcegitcommit: cf971fe82e9ee70db9209bb196ddf36614d39d10
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2019
ms.locfileid: "58541815"
---
# <a name="security-agent-authentication-methods"></a>Metody uwierzytelniania agenta zabezpieczeń 

> [!IMPORTANT]
> ASC IoT jest obecnie dostępna w publicznej wersji zapoznawczej.
> Ta wersja zapoznawcza jest dostarczane bez umowy dotyczącej poziomu usług, a nie jest zalecane w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone. Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

W tym artykule opisano różnych metod uwierzytelniania służących z agentem AzureIoTSecurity do uwierzytelniania za pomocą usługi IoT Hub.

Dla każdego urządzenia jest dołączona do ASC IoT w usłudze IoT Hub modułu zabezpieczeń jest wymagany. W celu uwierzytelnienia urządzenia ASC IoT można użyć jednej z dwóch metod. Wybierz metodę, która jest najlepsza dla istniejącego rozwiązania IoT. 

> [!div class="checklist"]
> * Opcja modułu zabezpieczeń
> * Opcja urządzenia

## <a name="authentication-methods"></a>Metody uwierzytelniania

Te dwie metody dla agenta AzureIoTSecurity w celu przeprowadzenia uwierzytelniania:

 - **Moduł** tryb uwierzytelniania<br>
   Moduł jest uwierzytelniany niezależnie od bliźniaczej reprezentacji urządzenia.
   Informacje o wymaganych przez ten typ uwierzytelniania jest zdefiniowany w pliku Authentication.config dla C# i LocalConfiguration.json dla języka C.
        
 - **Urządzenie** tryb uwierzytelniania<br>
    W przypadku tej metody Security agent najpierw uwierzytelnia urządzenie. Po uwierzytelnieniu początkowej wykonuje ASC agenta IoT **Rest** wywołania do Centrum IoT przy użyciu interfejsu API Rest przy użyciu danych uwierzytelniania urządzenia. ASC agenta IoT następnie żąda metody uwierzytelniania modułu zabezpieczeń oraz danych z usługi IoT Hub. W ostatnim kroku ASC IoT agenta wykonuje uwierzytelnianie w usłudze ASC moduł IoT.    

Zobacz [parametry instalacji agenta zabezpieczeń](#security-agent-installation-parameters) dowiesz się, jak skonfigurować.
                                
## <a name="authentication-methods-known-limitations"></a>Metody uwierzytelniania, znane ograniczenia

- **Moduł** tryb uwierzytelniania obsługuje tylko uwierzytelnianie za pomocą klucza symetrycznego.
- Certyfikatu podpisanego przez urząd certyfikacji nie jest obsługiwana przez **urządzenia** tryb uwierzytelniania.  

## <a name="security-agent-installation-parameters"></a>Parametry instalacji agenta zabezpieczeń

Gdy [wdrażanie agenta zabezpieczeń](select-deploy-agent.md), szczegółów uwierzytelniania musi zostać podana jako argumenty.
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
- [Wdróż agenta zabezpieczeń](select-deploy-agent.md)
- [Dostęp do danych pierwotnych zabezpieczeń](how-to-security-data-access.md)
