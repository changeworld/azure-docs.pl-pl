---
title: Azure Cloud Services, definicji schematu (pliku cscfg) | Dokumentacja firmy Microsoft
services: cloud-services
ms.custom: ''
ms.date: 12/07/2016
ms.reviewer: ''
ms.service: cloud-services
ms.suite: ''
ms.tgt_pltfrm: ''
ms.topic: reference
ms.assetid: 3ddc7fea-3339-4fc0-bdf9-853c32b25f69
caps.latest.revision: 35
author: jpconnock
ms.author: jeconnoc
manager: timlt
ms.openlocfilehash: 424381e2c243420cc2a68dc776d249cb17574f98
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "62130317"
---
# <a name="azure-cloud-services-config-schema-cscfg-file"></a>Azure Cloud Services, schematu konfiguracji (plik cscfg)
Plik konfiguracji usługi określa liczbę wystąpień roli w celu wdrożenia dla każdej roli w usłudze, wartości ustawienia konfiguracji i odciski palców wszelkich certyfikatów skojarzone z rolami. Jeśli usługa jest częścią sieci wirtualnej, informacje o konfiguracji sieci musi być podana w pliku konfiguracji usługi, a także w sieci wirtualnej pliku konfiguracji. Domyślnym rozszerzeniem dla pliku konfiguracji usługi jest .cscfg.

Model usług jest opisana przez [usługa w chmurze (klasyczna) definicji schematu](schema-csdef-file.md).

Domyślnie plik schematu konfiguracji diagnostyki platformy Azure jest zainstalowany na `C:\Program Files\Microsoft SDKs\Windows Azure\.NET SDK\<version>\schemas` katalogu. Zastąp `<version>` z zainstalowaną wersją programu [zestawu Azure SDK](https://azure.microsoft.com/downloads/).

Aby uzyskać więcej informacji na temat konfigurowania ról w usłudze, zobacz [co to jest model usługi w chmurze](cloud-services-model-and-package.md).

## <a name="basic-service-configuration-schema"></a>Schemat konfiguracji usług w warstwie podstawowa
Podstawowy format pliku konfiguracji usługi jest w następujący sposób.

```xml
<ServiceConfiguration serviceName="<service-name>" osFamily="<osfamily-number>" osVersion="<os-version>" schemaVersion="<schema-version>">

  <Role …>
    …
  </Role>

  <NetworkConfiguration>
    …
  </NetworkConfiguration>

</ServiceConfiguration>
```

## <a name="schema-definitions"></a>Definicje schematu
W poniższych tematach opisano schemat `ServiceConfiguration` elementu:

- [Role — schemat](schema-cscfg-role.md)
- [NetworkConfiguration — schemat](schema-cscfg-networkconfiguration.md)

## <a name="service-configuration-namespace"></a>Namespace konfiguracji usługi
Przestrzeń nazw XML dla pliku konfiguracji usługi jest: `http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceConfiguration`.

##  <a name="ServiceConfiguration"></a> ServiceConfiguration Element
`ServiceConfiguration` Element jest elementem najwyższego poziomu w pliku konfiguracji usługi.

W poniższej tabeli opisano atrybuty `ServiceConfiguration` elementu. Wszystkie wartości atrybutów są typu ciągu.

| Atrybut | Opis |
| --------- | ----------- |
|serviceName|Wymagany. Nazwa usługi w chmurze. Nazwa podana w tym miejscu musi być zgodna z nazwą określoną w pliku definicji usługi.|
|osFamily|Opcjonalny. Określa system operacyjny gościa, który zostanie uruchomiony na wystąpieniach roli w usłudze w chmurze. Aby uzyskać informacje o obsługiwanych wersjach systemu operacyjnego gościa, zobacz [wersji systemu operacyjnego gościa platformy Azure i zgodności zestawów SDK](cloud-services-guestos-update-matrix.md).<br /><br /> Jeśli nie dołączysz `osFamily` nie ustawiono wartości i `osVersion` atrybut do określonej wersji systemu operacyjnego gościa, domyślną wartość 1 jest używany.|
|osVersion|Opcjonalny. Określa wersję systemu operacyjnego gościa, który zostanie uruchomiony na wystąpieniach roli w usłudze w chmurze. Aby uzyskać więcej informacji o wersjach systemu operacyjnego gościa, zobacz [wersji systemu operacyjnego gościa platformy Azure i zgodności zestawów SDK](cloud-services-guestos-update-matrix.md).<br /><br /> Można określić, czy system operacyjny gościa można automatycznie uaktualnić do najnowszej wersji. Aby to zrobić, ustaw wartość `osVersion` atrybutu `*`. Po ustawieniu `*`, wystąpienia roli są wdrażane przy użyciu najnowszej wersji systemu operacyjnego gościa dla określonej rodziny systemów operacyjnych i zostaną automatycznie uaktualnione, gdy wydawane są nowe wersje systemu operacyjnego gościa.<br /><br /> Aby ręcznie określić określonej wersji, użyj `Configuration String` z tabeli w **przyszłość, bieżących i przejściowego wersje systemów operacyjnych gościa** części [wersji systemu operacyjnego gościa platformy Azure i zgodności zestawów SDK](cloud-services-guestos-update-matrix.md) .<br /><br /> Wartością domyślną dla `osVersion` atrybut jest `*`.|
|schemaVersion|Opcjonalny. Określa wersję schematu konfiguracji usługi. Wersja schematu umożliwia programowi Visual Studio wybierz odpowiednie narzędzia zestawu SDK na potrzeby sprawdzania poprawności schematu, jeśli jest zainstalowana więcej niż jedna wersja zestawu SDK side-by-side. Aby uzyskać więcej informacji na temat schematu a zgodność wersji, zobacz [wersji systemu operacyjnego gościa platformy Azure i zgodności zestawów SDK](cloud-services-guestos-update-matrix.md)|

Plik konfiguracji usługi musi zawierać jeden `ServiceConfiguration` elementu. `ServiceConfiguration` Element może zawierać dowolną liczbę `Role` elementy i zero lub 1 `NetworkConfiguration` elementów.