---
title: Schemat definicji usług w chmurze platformy Azure (plik cscfg) | Dokumenty firmy Microsoft
description: Plik konfiguracji usługi (cscfg) określa, ile wystąpień roli należy wdrożyć dla każdej roli, wartości konfiguracji i odcisków palców certyfikatu dla roli.
services: cloud-services
ms.custom: ''
ms.date: 12/07/2016
ms.service: cloud-services
ms.topic: reference
caps.latest.revision: 35
author: tgore03
ms.author: tagore
ms.openlocfilehash: cb77181e00c97b7f426429793f17af3cb5e84ebe
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79534749"
---
# <a name="azure-cloud-services-config-schema-cscfg-file"></a>Schemat konfiguracji usług w chmurze platformy Azure (plik cscfg)
Plik konfiguracji usługi określa liczbę wystąpień roli do wdrożenia dla każdej roli w usłudze, wartości wszystkich ustawień konfiguracji i odciski palców dla wszystkich certyfikatów skojarzonych z rolą. Jeśli usługa jest częścią sieci wirtualnej, informacje o konfiguracji sieci muszą być podane w pliku konfiguracji usługi, a także w pliku konfiguracji sieci wirtualnej. Domyślnym rozszerzeniem pliku konfiguracji usługi jest .cscfg.

Model usługi jest opisany przez [schemat definicji usługi w chmurze (klasyczny).](schema-csdef-file.md)

Domyślnie plik schematu konfiguracji diagnostyki platformy `C:\Program Files\Microsoft SDKs\Windows Azure\.NET SDK\<version>\schemas` Azure jest instalowany w katalogu. Zamień `<version>` na zainstalowaną wersję [zestawu SDK platformy Azure](https://azure.microsoft.com/downloads/).

Aby uzyskać więcej informacji na temat konfigurowania ról w usłudze, zobacz [Co to jest model usługi w chmurze](cloud-services-model-and-package.md).

## <a name="basic-service-configuration-schema"></a>Schemat konfiguracji usługi podstawowej
Podstawowy format pliku konfiguracji usługi jest następujący.

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
W poniższych tematach opisano `ServiceConfiguration` schemat elementu:

- [Role — schemat](schema-cscfg-role.md)
- [NetworkConfiguration — schemat](schema-cscfg-networkconfiguration.md)

## <a name="service-configuration-namespace"></a>Obszar nazw konfiguracji usługi
Obszar nazw XML dla pliku konfiguracji `http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceConfiguration`usługi to: .

##  <a name="serviceconfiguration-element"></a><a name="ServiceConfiguration"></a>Element konfiguracji usługi
Element `ServiceConfiguration` jest elementem najwyższego poziomu pliku konfiguracji usługi.

W poniższej tabeli `ServiceConfiguration` opisano atrybuty elementu. Wszystkie wartości atrybutów są typami ciągów.

| Atrybut | Opis |
| --------- | ----------- |
|Servicename|Wymagany. Nazwa usługi w chmurze. Nazwa podana w tym miejscu musi być zgodna z nazwą określoną w pliku definicji usługi.|
|osFamily|Element opcjonalny. Określa system operacyjny gościa, który będzie uruchamiany w wystąpieniach roli w usłudze w chmurze. Aby uzyskać informacje na temat obsługiwanych wersji systemu operacyjnego gościa, zobacz [Wersje systemu operacyjnego gościa platformy Azure i macierz zgodności zestawów SDK](cloud-services-guestos-update-matrix.md).<br /><br /> Jeśli nie zostanie `osFamily` uwzględniona wartość i `osVersion` nie ustawisz atrybutu do określonej wersji systemu operacyjnego gościa, używana jest domyślna wartość 1.|
|osVersion|Element opcjonalny. Określa wersję systemu operacyjnego gościa, który będzie uruchamiany w wystąpieniach roli w usłudze w chmurze. Aby uzyskać więcej informacji na temat wersji systemu operacyjnego gościa, zobacz [Wersje systemu operacyjnego gościa platformy Azure i macierz zgodności zestawów SDK](cloud-services-guestos-update-matrix.md).<br /><br /> Można określić, że system operacyjny gościa powinien zostać automatycznie uaktualniony do najnowszej wersji. Aby to zrobić, ustaw `osVersion` wartość atrybutu na `*`. Po ustawieniu `*`, wystąpienia roli są wdrażane przy użyciu najnowszej wersji systemu operacyjnego gościa dla określonej rodziny systemu operacyjnego i zostaną automatycznie uaktualnione po wydaniu nowych wersji systemu operacyjnego gościa.<br /><br /> Aby ręcznie określić określoną `Configuration String` wersję, należy użyć tej z tabeli w sekcji **Przyszłe, Bieżące i przejściowe wersje systemu operacyjnego gościa** [w wersjach systemu operacyjnego gościa platformy Azure i macierzy zgodności SDK](cloud-services-guestos-update-matrix.md).<br /><br /> Wartością domyślną `osVersion` atrybutu `*`jest .|
|wersja schematu|Element opcjonalny. Określa wersję schematu konfiguracji usługi. Wersja schematu umożliwia visual studio, aby wybrać odpowiednie narzędzia zestawu SDK do użycia do sprawdzania poprawności schematu, jeśli więcej niż jedna wersja zestawu SDK jest zainstalowany obok siebie. Aby uzyskać więcej informacji na temat zgodności schematu i wersji, zobacz [Wersje systemu operacyjnego gościa platformy Azure i macierz zgodności zestawów SDK](cloud-services-guestos-update-matrix.md)|

Plik konfiguracji usługi musi `ServiceConfiguration` zawierać jeden element. Element `ServiceConfiguration` może zawierać dowolną `Role` liczbę elementów `NetworkConfiguration` i zero lub 1 elementów.