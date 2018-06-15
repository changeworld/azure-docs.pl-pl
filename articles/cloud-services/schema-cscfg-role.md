---
title: Schemat roli usług w chmurze platformy Azure | Dokumentacja firmy Microsoft
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
author: thraka
ms.author: adegeo
manager: timlt
ms.openlocfilehash: 2f5c657bb80ad0788bcc3dd19d962b3f21afa4a8
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/20/2018
ms.locfileid: "34359003"
---
# <a name="azure-cloud-services-config-role-schema"></a>Schemat konfiguracji roli usług w chmurze Azure

`Role` Elementem pliku konfiguracji określa liczbę wystąpień roli do wdrożenia dla każdej roli w usłudze wartości wszystkich ustawień konfiguracji oraz odciski palców wszelkich certyfikatów skojarzone z rolą.

Aby uzyskać więcej informacji o schemacie konfiguracji usługi Azure, zobacz [schemat konfiguracji usługi w chmurze (klasyczne)](schema-cscfg-file.md). Aby uzyskać więcej informacji o schemacie definicji usługi Azure, zobacz [schematu definicji usługi w chmurze (klasyczne)](schema-csdef-file.md).

##  <a name="Role"></a> Element roli
W poniższym przykładzie przedstawiono `Role` elementu i jego elementów podrzędnych.

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
| name   | Wymagany. Określa nazwę roli. Nazwa musi odpowiadać podana nazwa roli w pliku definicji usługi.|
| vmName | Opcjonalny. Określa nazwę DNS dla maszyny wirtualnej. Nazwa musi mieć 10 znaków lub mniej.|

W poniższej tabeli opisano elementy podrzędne `Role` elementu.

| Element | Opis |
| ------- | ----------- |
| Wystąpienia | Wymagany. Określa liczbę wystąpień do wdrożenia dla roli. Liczba wystąpień jest definiowana za pomocą for z liczbami całkowitymi `count` atrybutu.|
| Ustawienie   | Opcjonalny. Określa ustawienie nazwy i wartości w kolekcji ustawień dla roli. Nazwa ustawienia jest zdefiniowany przez ciąg `name` atrybut i wartość ustawienia jest definiowana za pomocą ciągu dla `value` atrybutu.|
| Certyfikat | Opcjonalny. Określa nazwę, odcisk palca i algorytm certyfikat usługi, który ma być skojarzone z tą rolą. Nazwa certyfikatu jest zdefiniowany przez ciąg `name` atrybutu. Odcisk palca certyfikatu jest definiowana za pomocą ciągu szesnastkowe zawierających spacje nie dla `thumbprint` atrybutu. Musi być reprezentowana cyfry szesnastkowe cyfr i wielkich liter alfa. Algorytm certyfikatu jest definiowana za pomocą ciągu dla `thumbprintAlgorithm` atrybutu.|

## <a name="see-also"></a>Zobacz też
[Schemat konfiguracji chmury usługi (klasyczne)](schema-cscfg-file.md)