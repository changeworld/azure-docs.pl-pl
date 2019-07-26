---
title: Schemat roli usługi Azure Cloud Services | Microsoft Docs
ms.custom: ''
ms.date: 12/07/2016
services: cloud-services
ms.service: cloud-services
ms.topic: reference
caps.latest.revision: 12
author: georgewallace
ms.author: gwallace
ms.openlocfilehash: 481301333ada39297bf2813bbea5f096c2abd3ad
ms.sourcegitcommit: 4b647be06d677151eb9db7dccc2bd7a8379e5871
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/19/2019
ms.locfileid: "68360661"
---
# <a name="azure-cloud-services-config-role-schema"></a>Schemat roli konfiguracji Cloud Services platformy Azure

`Role` Element pliku konfiguracji określa liczbę wystąpień roli do wdrożenia dla każdej roli w usłudze, wartości ustawień konfiguracji i odcisków palców dla wszystkich certyfikatów skojarzonych z rolą.

Aby uzyskać więcej informacji na temat schematu konfiguracji usługi platformy Azure, zobacz [Schemat konfiguracji usługi w chmurze (klasycznej)](schema-cscfg-file.md). Aby uzyskać więcej informacji na temat schematu definicji usługi platformy Azure, zobacz [schemat definicji usługi w chmurze (klasycznej)](schema-csdef-file.md).

##  <a name="Role"></a>Element roli
Poniższy przykład pokazuje `Role` element i jego elementy podrzędne.

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
| name   | Wymagana. Określa nazwę roli. Nazwa musi być zgodna z nazwą podaną dla roli w pliku definicji usługi.|
| vmName | Opcjonalny. Określa nazwę DNS maszyny wirtualnej. Nazwa może składać się z maksymalnie 10 znaków.|

W poniższej tabeli opisano elementy `Role` podrzędne elementu.

| Element | Opis |
| ------- | ----------- |
| Wystąpienia | Wymagane. Określa liczbę wystąpień do wdrożenia dla roli. Liczba wystąpień jest definiowana przez liczbę całkowitą dla `count` atrybutu.|
| Ustawienie   | Opcjonalny. Określa nazwę i wartość ustawienia w kolekcji ustawień dla roli. Nazwa ustawienia jest definiowana przez ciąg dla `name` atrybutu, a wartość ustawienia jest definiowana przez ciąg `value` dla atrybutu.|
| Certyfikat | Opcjonalna. Określa nazwę, odcisk palca i algorytm certyfikatu usługi, który ma zostać skojarzony z rolą. Nazwa certyfikatu jest definiowana przez ciąg dla `name` atrybutu. Odcisk palca certyfikatu jest definiowany przez ciąg liczb szesnastkowych zawierających spacje dla `thumbprint` atrybutu. Liczby szesnastkowe muszą być reprezentowane przy użyciu cyfr i wielkich liter w znakach alfanumerycznych. Algorytm certyfikatu jest definiowany przez ciąg dla `thumbprintAlgorithm` atrybutu.|

## <a name="see-also"></a>Zobacz też
[Schemat konfiguracji usługi w chmurze (klasycznej)](schema-cscfg-file.md)