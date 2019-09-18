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
ms.openlocfilehash: d6d4af28d79b2375e774da98d4fdb1ad9dc22063
ms.sourcegitcommit: 0fab4c4f2940e4c7b2ac5a93fcc52d2d5f7ff367
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/17/2019
ms.locfileid: "71035721"
---
# <a name="create-the-azure-vmware-solution-by-cloudsimple-service"></a>Tworzenie rozwiązania Azure VMware według usługi CloudSimple

Aby rozpocząć pracę z rozwiązaniem VMware platformy Azure przez CloudSimple, Utwórz rozwiązanie Azure VMware według usługi CloudSimple w Azure Portal.

> [!IMPORTANT]
> Przed utworzeniem usługi CloudSimple należy zarejestrować dostawcę zasobów Microsoft. VMwareCloudSimple w ramach subskrypcji platformy Azure. Wykonaj kroki opisane w temacie [Włączanie dostawcy zasobów Microsoft. VMwareCloudSimple w ramach subskrypcji platformy Azure](enable-cloudsimple-service.md).

## <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).

## <a name="create-the-service"></a>Tworzenie usługi

1. Wybierz pozycję **Wszystkie usługi**.
2. Wyszukaj **usługi CloudSimple Services**.
    ![Wyszukaj usługę CloudSimple](media/create-cloudsimple-service-search.png)
3. Wybierz pozycję **CloudSimple Services**.
4. Kliknij przycisk **Dodaj** , aby utworzyć nową usługę.
    ![Dodaj usługę CloudSimple](media/create-cloudsimple-service-add.png)
5. Wybierz subskrypcję, w której chcesz utworzyć usługę CloudSimple.
6. Wybierz grupę zasobów dla usługi. Aby dodać nową grupę zasobów, kliknij pozycję **Utwórz nową**.
7. Wprowadź nazwę identyfikującą usługę.
8. Wprowadź wartość CIDR dla bramy usługi. Określ podsieć/28, która nie nakłada się na żadną z podsieci lokalnych, podsieci platformy Azure lub podsieci planowanych CloudSimple. Po utworzeniu usługi nie można zmienić CIDR.

    ![Tworzenie usługi CloudSimple](media/create-cloudsimple-service.png)
9. Kliknij przycisk **OK**.

Usługa zostanie utworzona i dodana do listy usług.

## <a name="next-steps"></a>Następne kroki

* Dowiedz się, jak [utworzyć chmurę prywatną](create-private-cloud.md)
* Dowiedz się, jak [skonfigurować środowisko chmury prywatnej](quickstart-create-private-cloud.md)
