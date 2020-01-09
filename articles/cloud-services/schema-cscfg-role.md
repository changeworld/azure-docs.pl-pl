---
title: Schemat roli usługi Azure Cloud Services | Microsoft Docs
ms.custom: ''
ms.date: 12/07/2016
services: cloud-services
ms.service: cloud-services
ms.topic: reference
caps.latest.revision: 12
author: tgore03
ms.author: tagore
ms.openlocfilehash: 0f0e79b462726b1aa6a953a4b8c92334d6b16492
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75449082"
---
# <a name="azure-cloud-services-config-role-schema"></a>Schemat roli konfiguracji Cloud Services platformy Azure

`Role` element pliku konfiguracji określa liczbę wystąpień roli do wdrożenia dla każdej roli w usłudze, wartości dowolnych ustawień konfiguracji oraz odcisków palców dla wszystkich certyfikatów skojarzonych z rolą.

Aby uzyskać więcej informacji na temat schematu konfiguracji usługi platformy Azure, zobacz [Schemat konfiguracji usługi w chmurze (klasycznej)](schema-cscfg-file.md). Aby uzyskać więcej informacji na temat schematu definicji usługi platformy Azure, zobacz [schemat definicji usługi w chmurze (klasycznej)](schema-csdef-file.md).

##  <a name="Role"></a>Element roli
Poniższy przykład pokazuje element `Role` i jego elementy podrzędne.

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

W poniższej tabeli opisano atrybuty elementu `Role`.

| Atrybut | Opis |
| --------- | ----------- |
| name   | Wymagany. Określa nazwę roli. Nazwa musi być zgodna z nazwą podaną dla roli w pliku definicji usługi.|
| vmName | Element opcjonalny. Określa nazwę DNS maszyny wirtualnej. Nazwa może składać się z maksymalnie 10 znaków.|

W poniższej tabeli opisano elementy potomne elementu `Role`.

| Element | Opis |
| ------- | ----------- |
| Wystąpienia | Wymagany. Określa liczbę wystąpień do wdrożenia dla roli. Liczba wystąpień jest definiowana przez liczbę całkowitą dla atrybutu `count`.|
| Ustawienie   | Element opcjonalny. Określa nazwę i wartość ustawienia w kolekcji ustawień dla roli. Nazwa ustawienia jest definiowana przez ciąg dla atrybutu `name` i wartość ustawienia jest definiowana przez ciąg dla atrybutu `value`.|
| Certyfikat | Element opcjonalny. Określa nazwę, odcisk palca i algorytm certyfikatu usługi, który ma zostać skojarzony z rolą. Nazwa certyfikatu jest definiowana przez ciąg dla atrybutu `name`. Odcisk palca certyfikatu jest definiowany przez ciąg liczb szesnastkowych zawierających spacje dla atrybutu `thumbprint`. Liczby szesnastkowe muszą być reprezentowane przy użyciu cyfr i wielkich liter w znakach alfanumerycznych. Algorytm certyfikatu jest definiowany przez ciąg dla atrybutu `thumbprintAlgorithm`.|

## <a name="see-also"></a>Zobacz też
[Schemat konfiguracji usługi w chmurze (klasycznej)](schema-cscfg-file.md)