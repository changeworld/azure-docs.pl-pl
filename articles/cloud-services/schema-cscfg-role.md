---
title: Schemat roli usług w chmurze platformy Azure | Dokumenty firmy Microsoft
description: Element Rola pliku konfiguracji usługi określa, ile wystąpień roli należy wdrożyć dla każdej roli, wartości konfiguracji i odcisków palców certyfikatu.
ms.custom: ''
ms.date: 12/07/2016
services: cloud-services
ms.service: cloud-services
ms.topic: reference
caps.latest.revision: 12
author: tgore03
ms.author: tagore
ms.openlocfilehash: b64f9d27e382a39b132593502fed32c565af473a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79528425"
---
# <a name="azure-cloud-services-config-role-schema"></a>Schemat roli konfiguracji usług w chmurze platformy Azure

Element `Role` pliku konfiguracji określa liczbę wystąpień roli do wdrożenia dla każdej roli w usłudze, wartości wszystkich ustawień konfiguracji i odciski palców dla wszystkich certyfikatów skojarzonych z rolą.

Aby uzyskać więcej informacji na temat schematu konfiguracji usługi Azure, zobacz [Schemat konfiguracji usługi w chmurze (klasyczny).](schema-cscfg-file.md) Aby uzyskać więcej informacji na temat schematu definicji usługi Azure, zobacz [Schemat definicji usługi w chmurze (klasyczny).](schema-csdef-file.md)

##  <a name="role-element"></a><a name="Role"></a>Element roli
Poniższy przykład `Role` pokazuje element i jego elementy podrzędne.

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

W poniższej tabeli `Role` opisano atrybuty elementu.

| Atrybut | Opis |
| --------- | ----------- |
| name   | Wymagany. Określa nazwę roli. Nazwa musi być zgodna z nazwą podana dla roli w pliku definicji usługi.|
| vmName | Element opcjonalny. Określa nazwę DNS maszyny wirtualnej. Nazwa musi zawierać 10 znaków lub mniej.|

W poniższej tabeli opisano `Role` elementy podrzędne elementu.

| Element | Opis |
| ------- | ----------- |
| Wystąpienia | Wymagany. Określa liczbę wystąpień do wdrożenia dla roli. Liczba wystąpień jest definiowana przez liczbę `count` całkowitą dla atrybutu.|
| Ustawienie   | Element opcjonalny. Określa nazwę ustawienia i wartość w kolekcji ustawień roli. Nazwa ustawienia jest definiowana przez `name` ciąg dla atrybutu, a wartość `value` ustawienia jest definiowana przez ciąg dla atrybutu.|
| Certyfikat | Element opcjonalny. Określa nazwę, odcisk palca i algorytm certyfikatu usługi, który ma być skojarzony z rolą. Nazwa certyfikatu jest definiowana `name` przez ciąg dla atrybutu. Odcisk palca certyfikatu jest definiowany przez ciąg liczb szesnastkowych, zawierający brak spacji dla atrybutu. `thumbprint` Liczby szesnastkowe muszą być reprezentowane przy użyciu cyfr i wielkich liter alfa. Algorytm certyfikatu jest definiowany `thumbprintAlgorithm` przez ciąg dla atrybutu.|

## <a name="see-also"></a>Zobacz też
[Schemat konfiguracji usługi w chmurze (klasyczny)](schema-cscfg-file.md)