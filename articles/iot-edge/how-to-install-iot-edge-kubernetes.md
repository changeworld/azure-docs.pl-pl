---
title: Jak zainstalować usługę IoT Edge na Kubernetes | Dokumenty firmy Microsoft
description: Dowiedz się, jak zainstalować usługę IoT Edge w uprzeglądarce Kubernetes przy użyciu lokalnego środowiska klastra programistycznego
author: kgremban
manager: philmea
ms.author: veyalla
ms.date: 04/26/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 4b2068c3944f9e7616b0666c7bafcafc68ee0cd9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79471289"
---
# <a name="how-to-install-iot-edge-on-kubernetes-preview"></a>Jak zainstalować usługę IoT Edge w uprzeglądarce Kubernetes (wersja zapoznawcza)

Usługa IoT Edge może integrować się z umięktami, używając jej jako odpornej, wysoce dostępnej warstwy infrastruktury. Oto, gdzie ta obsługa pasuje do rozwiązania wysokiej klasy IoT Edge:

![k8s intro](./media/how-to-install-iot-edge-kubernetes/kubernetes-model.png)

>[!TIP]
>Dobrym modelem mentalnym dla tej integracji jest myślenie o Kubernetes jako innym środowisku operacyjnym aplikacje IoT Edge mogą działać oprócz linuksa i systemu Windows.

## <a name="architecture"></a>Architektura 
W uękierze Kubernetes usługa IoT Edge udostępnia *niestandardową definicję zasobów* (CRD) dla wdrożeń obciążenia brzegowej. Agent usługi IoT Edge przejmuje rolę *kontrolera CRD,* który uzgadnia żądany stan zarządzany w chmurze ze stanem klastra lokalnego.

Okres istnienia modułu jest zarządzany przez harmonogram Kubernetes, który zachowuje dostępność modułu i wybiera ich rozmieszczenie. Usługa IoT Edge zarządza platformą aplikacji brzegowej działającą na górze, stale uzgadniając żądany stan określony w U portalu IoT Hub ze stanem klastra brzegowego. Model aplikacji jest nadal znanym modelem opartym na modułach i trasach usługi IoT Edge. Kontroler usługi IoT Edge Agent wykonuje *automatyczne* tłumaczenie modelu aplikacji usługi IoT Edge do natywnych konstrukcji Kubernetes, takich jak zasobników, wdrożeń, usług itp.

Oto diagram architektury wysokiego poziomu:

![łuk kubernetes](./media/how-to-install-iot-edge-kubernetes/publicpreview-refresh-kubernetes.png)

Każdy składnik wdrożenia brzegowego jest objęty zakresem obszaru nazw Usługi Kubernetes specyficznego dla urządzenia, dzięki czemu można udostępniać te same zasoby klastra między wieloma urządzeniami brzegowymi i ich wdrożeniami.

>[!NOTE]
>Usługa IoT Edge w uprzeglądarce Kubernetes jest w [publicznej wersji zapoznawczej](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="tutorials-and-references"></a>Samouczki i odwołania 

Zobacz [IoT Edge na kubernetes podglądu docs mini-site,](https://aka.ms/edgek8sdoc) aby uzyskać więcej informacji, w tym szczegółowe samouczki i odwołania.
