---
title: Konfigurowanie interfejsu wiersza polecenia usługi Azure Service Fabric mesh | Microsoft Docs
description: Dowiedz się, jak skonfigurować interfejs wiersza polecenia usługi Azure Service Fabric mesh.
services: service-fabric-mesh
keywords: ''
author: dkkapur
ms.author: dekapur
ms.date: 11/28/2018
ms.topic: conceptual
ms.service: service-fabric-mesh
manager: timlt
ms.openlocfilehash: 46b2a9a0c4d8a1d9e0fa069d512670dffb94fc28
ms.sourcegitcommit: f2771ec28b7d2d937eef81223980da8ea1a6a531
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/20/2019
ms.locfileid: "71173766"
---
# <a name="set-up-service-fabric-mesh-cli"></a>Konfigurowanie interfejsu wiersza polecenia usługi Service Fabric Mesh
Interfejs wiersza polecenia usługi Service Fabric Mesh jest wymagany do wdrażania zasobów i zarządzania nimi lokalnie oraz w usłudze Azure Service Fabric Mesh. 

Istnieją trzy typy interfejsów wiersza polecenia, które mogą być używane. Ich podsumowanie znajduje się w poniższej tabeli. 

| Moduł interfejsu wiersza polecenia | Środowisko docelowe |  Opis | 
|---|---|---|
| az mesh | Azure Service Fabric Mesh | Podstawowy interfejs wiersza polecenia umożliwiający wdrażanie aplikacji i zarządzanie zasobami w ramach środowiska Azure Service Fabric Mesh. 
| sfctl | Klastry lokalne | Interfejs wiersza polecenia usługi Service Fabric umożliwiający wdrażanie i testowanie zasobów usługi Service Fabric w ramach klastrów lokalnych.  
| Interfejs wiersza polecenia programu Maven | Klastry lokalne i usługa Azure Service Fabric Mesh | Otoka `az mesh` w programie `sfctl` i umożliwiająca deweloperom języka Java używanie znanego środowiska wiersza polecenia na potrzeby środowiska projektowania lokalnego i platformy Azure.  

Na potrzeby wersji zapoznawczej interfejs wiersza polecenia usługi Azure Service Fabric mesh jest napisany jako rozszerzenie interfejsu wiersza polecenia platformy Azure. Zainstalować go można w usłudze Azure Cloud Shell lub w lokalnej instalacji wiersza polecenia platformy Azure. 

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)] 

## <a name="install-the-azure-service-fabric-mesh-cli"></a>Instalowanie interfejsu wiersza polecenia usługi Azure Service Fabric Mesh
1. Musisz zainstalować interfejs wiersza polecenia platformy Azure w wersji 2.0.67 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest używana. Aby zainstalować lub uaktualnić do najnowszej wersji interfejsu wiersza polecenia, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure][azure-cli-install].

2. Zainstaluj moduł rozszerzenia interfejsu wiersza polecenia usługi Azure Service Fabric Mesh za pomocą poniższego polecenia. 

    ```azurecli-interactive
    az extension add --name mesh
    ```

3. Zaktualizuj istniejący moduł interfejsu wiersza polecenia usługi Azure Service Fabric Mesh za pomocą poniższego polecenia.

    ```azurecli-interactive
    az extension update --name mesh
    ```

## <a name="install-the-service-fabric-cli-sfctl"></a>Instalowanie interfejsu wiersza polecenia usługi Service Fabric (sfctl) 

Postępuj zgodnie z instrukcjami w sekcji [Konfigurowanie interfejsu wiersza polecenia usługi Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-cli). Modułu **sfctl** można używać do wdrażania aplikacji opartych na modelu zasobów w ramach klastrów usługi Service Fabric na maszynie lokalnej. 

## <a name="install-the-maven-cli"></a>Instalowanie interfejsu wiersza polecenia programu Maven 

Aby korzystać z interfejsu wiersza polecenia programu Maven, na maszynie muszą być zainstalowane następujące elementy: 

* [Java](https://www.azul.com/downloads/zulu/)
* [Maven](https://maven.apache.org/download.cgi)
* [Usługa Git](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git)
* Interfejs wiersza polecenia usługi Azure Mesh (az mesh) — pod kątem usługi Azure Service Fabric Mesh 
* Interfejs SFCTL (sfctl) — pod kątem klastrów lokalnych 

Interfejs wiersza polecenia programu Maven dla usługi Service Fabric jest nadal w wersji zapoznawczej. 

Aby używać wtyczki programu Maven w aplikacji Java programu Maven, dodaj poniższy fragment kodu do pliku pom.xml:

```XML
<project>
  ...
  <build>
    ...
    <plugins>
      ...
      <plugin>
        <groupId>com.microsoft.azure</groupId>
          <artifactId>azure-sfmesh-maven-plugin</artifactId>
          <version>0.1.0</version>
          <configuration>
            ...
          </configuration>
      </plugin>
    </plugins>
  </build>
</project>
```

Zapoznaj się z sekcją zawierającą [dokumentację interfejsu wiersza polecenia programu Maven](service-fabric-mesh-reference-maven.md), aby uzyskać informacje dotyczące szczegółów użycia.

## <a name="next-steps"></a>Następne kroki

Skonfigurować możesz również [środowisko deweloperskie w systemie Windows](service-fabric-mesh-howto-setup-developer-environment-sdk.md).

Znajdź odpowiedzi na [typowe pytania i problemy](service-fabric-mesh-faq.md).

[azure-cli-install]: /cli/azure/install-azure-cli
