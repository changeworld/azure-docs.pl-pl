---
title: Jak wdrożyć usługę sieci Web w wielu regionach | Dokumentacja firmy Microsoft
description: Kroki wdrażania (Kopiuj) nową usługę sieci Web do innych regionów.
services: machine-learning
documentationcenter: ''
author: aashishb
manager: hjerez
editor: cgronlun
ms.assetid: 36c60411-f2db-4ee2-9b66-b1f1d77a8f44
ms.service: machine-learning
ms.component: studio
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/19/2017
ms.author: aashishb
ms.openlocfilehash: 78b37f0e7ac554c1823a0607e43718e5a0ac0067
ms.sourcegitcommit: 944d16bc74de29fb2643b0576a20cbd7e437cef2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/07/2018
ms.locfileid: "34835138"
---
# <a name="how-to-deploy-a-web-service-to-multiple-regions"></a>Wdrażanie usługi sieci Web do wielu regionów
Usługi sieci Web platformy Azure umożliwiają łatwe wdrażanie usługi sieci web do wielu regionach, bez konieczności wielu subskrypcji lub obszarów roboczych. 

Cennik to region określonych, dlatego należy zdefiniować plan rozliczeniowy dla każdego regionu, w którym będą wdrażać usługi sieci web.

## <a name="to-create-a-plan-in-another-region"></a>Aby utworzyć plan w innym regionie
1. Zaloguj się do [platformy Microsoft Azure Machine Learning Web Services](https://services.azureml.net/).
2. Kliknij przycisk **plany** opcji menu.
3. W planie za pośrednictwem widoku strony, kliknij przycisk **nowy**.
4. Z **subskrypcji** listy rozwijanej wybierz subskrypcję, w której będą znajdować się nowy plan.
5. Z **Region** listy rozwijanej wybierz region nowego planu. Zostaną wyświetlone opcje planowania dla wybranego regionu **opcje planowania** części strony.
6. Z **grupy zasobów** listy rozwijanej wybierz zasób grupy planu. Aby uzyskać więcej informacji na temat grup zasobów, zobacz [Omówienie usługi Azure Resource Manager](../../azure-resource-manager/resource-group-overview.md).
7. W **Nazwa planu** wpisz nazwę planu.
8. W obszarze **opcje planu**, kliknij przycisk rozliczeń poziom nowego planu.
9. Kliknij przycisk **Utwórz**.

## <a name="deploying-the-web-service-to-another-region"></a>Wdrażanie usługi sieci web do innego regionu
1. Kliknij przycisk **usług sieci Web** opcji menu.
2. Wybierz usługę sieci Web jest wdrażany z nowego regionu.
3. Kliknij przycisk **kopiowania**.
4. W **nazwę usługi sieci Web**, wpisz nową nazwę dla usługi sieci web.
5. W **sieci Web opisu usługi**, wpisz opis usługi sieci web.
6. Z **subskrypcji** listy rozwijanej wybierz subskrypcję, w której będą znajdować się nową usługę sieci web.
7. Z **grupy zasobów** listy rozwijanej wybierz zasób grupy dla usługi sieci web. Aby uzyskać więcej informacji na temat grup zasobów, zobacz [Omówienie usługi Azure Resource Manager](../../azure-resource-manager/resource-group-overview.md).
8. Z **Region** listy rozwijanej wybierz region, w której chcesz wdrożyć usługę sieci web.
9. Z **konta magazynu** konta listy rozwijanej wybierz magazynu do przechowywania usługi sieci web.
10. Z **planu cen** listy rozwijanej wybierz plan w regionie wybranej w kroku 8.
11. Kliknij przycisk **kopiowania**.

