---
title: Rozwiązanie VMware firmy Azure według CloudSimple — Tworzenie usługi CloudSimple
description: Opisuje sposób tworzenia usługi CloudSimple w Azure Portal
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/19/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 99194c42dbc6ef07301be517021bf0fb4b4e7c23
ms.sourcegitcommit: 42748f80351b336b7a5b6335786096da49febf6a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2019
ms.locfileid: "72173502"
---
# <a name="create-the-azure-vmware-solution-by-cloudsimple-service"></a>Tworzenie rozwiązania Azure VMware według usługi CloudSimple

Aby rozpocząć pracę z rozwiązaniem VMware platformy Azure przez CloudSimple, Utwórz rozwiązanie Azure VMware według usługi CloudSimple w Azure Portal.

> [!IMPORTANT]
> Przed utworzeniem usługi CloudSimple należy zarejestrować dostawcę zasobów Microsoft. VMwareCloudSimple w ramach subskrypcji platformy Azure. Wykonaj kroki opisane w temacie [Włączanie dostawcy zasobów Microsoft. VMwareCloudSimple w ramach subskrypcji platformy Azure](enable-cloudsimple-service.md).

## <a name="sign-in-to-azure"></a>Zaloguj się w usłudze Azure

Zaloguj się do [portalu Azure](https://portal.azure.com).

## <a name="create-the-service"></a>Tworzenie usługi

1. Wybierz pozycję **Wszystkie usługi**.
2. Wyszukaj **usługi CloudSimple Services**.
    ![Search CloudSimple Service @ no__t-1
3. Wybierz pozycję **CloudSimple Services**.
4. Kliknij przycisk **Dodaj** , aby utworzyć nową usługę.
    ![Add CloudSimple Service @ no__t-1
5. Wybierz subskrypcję, w której chcesz utworzyć usługę CloudSimple.
6. Wybierz grupę zasobów dla usługi. Aby dodać nową grupę zasobów, kliknij pozycję **Utwórz nową**.
7. Wprowadź nazwę identyfikującą usługę.
8. Wprowadź wartość CIDR dla bramy usługi. Określ podsieć/28, która nie nakłada się na żadną z podsieci lokalnych, podsieci platformy Azure lub podsieci planowanych CloudSimple. Po utworzeniu usługi nie można zmienić CIDR.

    ![Tworzenie usługi CloudSimple](media/create-cloudsimple-service.png)
9. Kliknij przycisk **OK**.

Usługa zostanie utworzona i dodana do listy usług.

## <a name="next-steps"></a>Następne kroki

* Dowiedz się, jak [inicjować obsługę węzłów](create-nodes.md)
* Dowiedz się, jak [utworzyć chmurę prywatną](create-private-cloud.md)
* Dowiedz się, jak [skonfigurować środowisko chmury prywatnej](quickstart-create-private-cloud.md)
