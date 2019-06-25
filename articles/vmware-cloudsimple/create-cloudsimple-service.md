---
title: Tworzenie usługi Azure VMware Solution by CloudSimple — usługa
description: W tym artykule opisano sposób tworzenia usługi CloudSimple w witrynie Azure portal
author: sharaths-cs
ms.author: b-shsury
ms.date: 06/04/2019
ms.topic: article
ms.service: vmware
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: a0ccce6f298270b2751307868fdf85697cb7e8ee
ms.sourcegitcommit: 1289f956f897786090166982a8b66f708c9deea1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/17/2019
ms.locfileid: "67154965"
---
# <a name="create-azure-vmware-solution-by-cloudsimple---service"></a>Utwórz rozwiązanie VMware na platformie Azure przez CloudSimple - usługi

Aby rozpocząć pracę z rozwiązaniem VMware do platformy Azure przez CloudSimple, należy utworzyć rozwiązanie VMware Azure przez usługę CloudSimple w witrynie Azure portal.

> [!NOTE]
> Przed utworzeniem usługi CloudSimple, należy zarejestrować dostawcę zasobów Microsoft.VMwareCloudSimple, w ramach subskrypcji platformy Azure. Postępuj zgodnie z instrukcjami w [włączenia Microsoft.VMwareCloudSimple dostawcy zasobów w subskrypcji platformy Azure](enable-cloudsimple-service.md).

## <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

Zaloguj się do witryny Azure Portal pod adresem [https://portal.azure.com](https://portal.azure.com).

## <a name="create-the-service"></a>Tworzenie usługi

1. Wybierz pozycję **Wszystkie usługi**.

2. Wyszukaj **usług CloudSimple**.

    ![CloudSimple usługi wyszukiwania](media/create-cloudsimple-service-search.png)

3. Wybierz **usług CloudSimple**.

4. Kliknij przycisk **Dodaj** do utworzenia nowej usługi.

    ![Dodaj usługę CloudSimple](media/create-cloudsimple-service-add.png)

5. Wybierz subskrypcję, której chcesz utworzyć usługę CloudSimple.

6. Wybierz grupę zasobów dla usługi. Aby dodać nową grupę zasobów, kliknij **Utwórz nowy**.

7. Wprowadź nazwę do identyfikowania usługi.

8. Wprowadź CIDR usługi bramy. Określ o rozmiarze/28 podsieci, która nie pokrywa się z żadną z istniejącej podsieci.  Należą do podsieci lokalnej, podsieci platformy Azure lub dowolne planowane CloudSimple podsieci. Nie można zmienić CIDR, po utworzeniu usługi.

    ![Tworzenie usługi CloudSimple](media/create-cloudsimple-service.png)

9. Kliknij przycisk **OK**.

Usługa zostanie utworzony i dodany do listy usług.

## <a name="next-steps"></a>Kolejne kroki

* Dowiedz się, jak [utworzyć chmurę prywatną](https://docs.azure.cloudsimple.com/create-private-cloud/)
* Dowiedz się, jak [konfigurowania środowiska chmury prywatnej](quickstart-create-private-cloud.md)
