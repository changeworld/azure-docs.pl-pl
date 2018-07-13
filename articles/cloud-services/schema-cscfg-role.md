---
title: Azure Cloud Services Role — schemat | Dokumentacja firmy Microsoft
ms.custom: ''
ms.date: 12/07/2016
services: cloud-services
ms.reviewer: ''
ms.service: cloud-services
ms.suite: ''
ms.tgt_pltfrm: ''
ms.topic: reference
ms.assetid: e4fbffc1-98eb-449c-971c-de415e45ab34
caps.latest.revision: 12
author: jpconnock
ms.author: jeconnoc
manager: timlt
ms.openlocfilehash: 20f4186426152d2dc9b445981a69881c35587eb6
ms.sourcegitcommit: e0a678acb0dc928e5c5edde3ca04e6854eb05ea6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/13/2018
ms.locfileid: "39005785"
---
# <a name="azure-cloud-services-config-role-schema"></a>Schemat konfiguracji roli usług w chmurze platformy Azure

`Role` Element pliku konfiguracji określa liczbę wystąpień roli w celu wdrożenia dla każdej roli w usłudze wartości wszystkie ustawienia konfiguracji i odciski palców wszelkich certyfikatów skojarzone z rolami.

Aby uzyskać więcej informacji na temat schematu konfiguracji usługi platformy Azure, zobacz [schematu konfiguracji usługi w chmurze (klasyczne)](schema-cscfg-file.md). Aby uzyskać więcej informacji na temat ze schematem definicji usługi Azure, zobacz [usługa w chmurze (klasyczna) definicji schematu](schema-csdef-file.md).

##  <a name="Role"></a> Element roli
W poniższym przykładzie przedstawiono `Role` elementu i jego elementy podrzędne.

```xml 
<ServiceConfiguration>
  <Role name="<role-name>" vmName="<vm-name>">
    <Instances count="<number-of-instances>"/>
    <ConfigurationSettings>
      <Setting name="<setting-name>" value="<setting-value>" />
    </ConfigurationSettings>
    <Certificates>
      <Certificate name="<certificate-name>" thumbprint="<certificate-thumbprint>" thumbprintAlgorithm="<algorithm>"/>
    </Certificates>
  </Role>
</ServiceConfiguration>
```

W poniższej tabeli opisano atrybuty `Role` elementu.

| Atrybut | Opis |
| --------- | ----------- |
| name   | Wymagany. Określa nazwę roli. Nazwa musi odpowiadać nazwa podana dla roli w pliku definicji usługi.|
| vmName | Opcjonalny. Określa nazwę DNS maszyny wirtualnej. Nazwa musi mieć 10 znaków lub mniej.|

W poniższej tabeli opisano elementy podrzędne `Role` elementu.

| Element | Opis |
| ------- | ----------- |
| Wystąpienia | Wymagany. Określa liczbę wystąpień do wdrożenia dla danej roli. Liczba wystąpień jest definiowany przez liczbę całkowitą dla `count` atrybutu.|
| Ustawienie   | Opcjonalny. Określa nazwę i wartość ustawienia w kolekcji ustawień dla roli. Nazwa ustawienia jest definiowany przez ciąg `name` atrybut i wartości ustawień, które jest definiowany przez ciąg `value` atrybutu.|
| Certyfikat | Opcjonalny. Określa nazwę, odcisk palca i algorytm certyfikat usługi, który ma być skojarzone z tą rolą. Nazwa certyfikatu jest definiowany przez ciąg `name` atrybutu. Odcisk palca certyfikatu jest definiowany przez ciąg liczb szesnastkowych, bez spacji, aby uzyskać `thumbprint` atrybutu. Liczby szesnastkowe musi być reprezentowana z cyfr i znaków alfanumerycznych. Algorytm certyfikatu jest definiowany przez ciąg `thumbprintAlgorithm` atrybutu.|

## <a name="see-also"></a>Zobacz też
[Schemat konfiguracji chmury usługi (model klasyczny)](schema-cscfg-file.md)