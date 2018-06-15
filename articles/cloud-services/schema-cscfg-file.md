---
title: Azure Cloud Services definicję schematu (plik .cscfg) | Dokumentacja firmy Microsoft
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
author: thraka
ms.author: adegeo
manager: timlt
ms.openlocfilehash: 6347314e7f279356f4f3944f3238deda84f10fc0
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/20/2018
ms.locfileid: "34358221"
---
# <a name="azure-cloud-services-config-schema-cscfg-file"></a>Schemat konfiguracji (plik .cscfg) usług w chmurze Azure
Plik konfiguracyjny usługi określa liczbę wystąpień roli do wdrożenia dla każdej roli w usłudze, wartości wszystkich ustawień konfiguracji oraz odciski palców wszelkich certyfikatów skojarzone z rolą. Jeśli usługa jest częścią sieci wirtualnej, należy podać informacje o konfiguracji sieci, w pliku konfiguracji usługi, a także w pliku konfiguracji sieci wirtualnej. Domyślne rozszerzenie pliku konfiguracji usługi jest .cscfg.

Model usługi jest opisane przez [schematu definicji usługi w chmurze (klasyczne)](schema-csdef-file.md).

Domyślnie plik schematu konfiguracji diagnostyki Azure jest instalowany na `C:\Program Files\Microsoft SDKs\Windows Azure\.NET SDK\<version>\schemas` katalogu. Zastąp `<version>` z zainstalowaną wersją [zestawu Azure SDK](https://azure.microsoft.com/downloads/).

Aby uzyskać więcej informacji na temat konfigurowania ról w usłudze, zobacz [co to jest model usługi w chmurze](cloud-services-model-and-package.md).

## <a name="basic-service-configuration-schema"></a>Schemat konfiguracji podstawowej usługi
Podstawowy format pliku konfiguracji usługi ma następującą składnię.

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

## <a name="schema-definitions"></a>Definicje schematów
W poniższych tematach opisano schematu `ServiceConfiguration` elementu:

- [Role — schemat](schema-cscfg-role.md)
- [NetworkConfiguration — schemat](schema-cscfg-networkconfiguration.md)

## <a name="service-configuration-namespace"></a>Namespace konfiguracji usługi
Przestrzeń nazw XML dla pliku konfiguracji usługi: `http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceConfiguration`.

##  <a name="ServiceConfiguration"></a> Element ServiceConfiguration — Element
`ServiceConfiguration` Element to element najwyższego poziomu w pliku konfiguracji usługi.

W poniższej tabeli opisano atrybuty `ServiceConfiguration` elementu. Wszystkie wartości atrybutów są typu ciąg.

| Atrybut | Opis |
| --------- | ----------- |
|serviceName|Wymagany. Nazwa usługi w chmurze. Nazwa podane w tym miejscu musi odpowiadać nazwie określonej w pliku definicji usługi.|
|Rodzina systemów operacyjnych|Opcjonalny. Określa system operacyjny gościa, które zostanie uruchomione dla wystąpień roli w usłudze w chmurze. Aby uzyskać informacje o obsługiwanych wersjach systemu operacyjnego gościa, zobacz [wersje systemu operacyjnego gościa Azure i zgodność pakietu SDK](cloud-services-guestos-update-matrix.md).<br /><br /> Jeśli nie zostanie uwzględniony `osFamily` nie ustawiono wartości i `osVersion` jest używany atrybut do określonej wersji systemu operacyjnego gościa, wartość domyślną 1.|
|OsVersion|Opcjonalny. Określa wersję systemu operacyjnego gościa, który będzie uruchamiany w wystąpieniach ról w usłudze w chmurze. Aby uzyskać więcej informacji dotyczących wersji systemu operacyjnego gościa, zobacz [poszczególnych wersji systemu operacyjnego gościa Azure i zgodność pakietu SDK](cloud-services-guestos-update-matrix.md).<br /><br /> Można określić, że systemu operacyjnego gościa można automatycznie uaktualniony do najnowszej wersji. Aby to zrobić, ustaw wartość `osVersion` atrybutu `*`. Jeśli wartość `*`, wystąpień ról są wdrażane za pomocą najnowszej wersji systemu operacyjnego gościa dla określonej rodziny systemów operacyjnych i zostaną automatycznie uaktualnione po udostępnieniu nowej wersji systemu operacyjnego gościa.<br /><br /> Aby ręcznie określić określonej wersji, użyj `Configuration String` z tabeli w **przyszłych, bieżących i przejściowych wersji systemu operacyjnego gościa** sekcji [poszczególnych wersji systemu operacyjnego gościa Azure i zgodność pakietu SDK](cloud-services-guestos-update-matrix.md) .<br /><br /> Wartość domyślna dla `osVersion` atrybutu `*`.|
|Atrybut schemaVersion|Opcjonalny. Określa wersję schematu, konfiguracji usługi. Wersja schematu umożliwia programowi Visual Studio wybierz odpowiednie narzędzia zestawu SDK do użycia w celu weryfikacji schematu, jeśli zainstalowano więcej niż jedną wersję zestawu SDK side-by-side. Aby uzyskać więcej informacji o zgodności schematu i wersji, zobacz [poszczególnych wersji systemu operacyjnego gościa Azure i zgodność pakietu SDK](cloud-services-guestos-update-matrix.md)|

Plik konfiguracji usługi musi zawierać jeden `ServiceConfiguration` elementu. `ServiceConfiguration` Element może zawierać dowolną liczbę `Role` elementów i 0 lub 1 `NetworkConfiguration` elementów.