---
title: Jak wdrożyć usługę sieci Web do wielu regionów | Dokumentacja firmy Microsoft
description: Kroki umożliwiające wdrożenie (kopiowanie) nową usługę sieci Web do innych regionów.
services: machine-learning
documentationcenter: ''
author: ericlicoding
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
ms.custom: (previous ms.author=aashishb, author=aashishb)
ms.author: amlstudiodocs
ms.openlocfilehash: cf63eb4d99de6e381caa911192b239268066ad7b
ms.sourcegitcommit: fa758779501c8a11d98f8cacb15a3cc76e9d38ae
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/20/2018
ms.locfileid: "52263805"
---
# <a name="how-to-deploy-a-web-service-to-multiple-regions"></a>Wdrażanie usługi sieci Web do wielu regionów
W nowych usługach sieci Web Azure umożliwiają łatwe wdrażanie usługi sieci web do wielu regionów, bez konieczności używania wielu subskrypcje i obszary robocze. 

Cennik jest w związku z tym należy zdefiniować planu rozliczeniowego dla każdego regionu, w której zostanie wdrożona usługa sieci web od regionu.

## <a name="to-create-a-plan-in-another-region"></a>Aby utworzyć plan w innym regionie
1. Zaloguj się do [Microsoft Azure Machine Learning Web Services](https://services.azureml.net/).
2. Kliknij przycisk **plany** opcji menu.
3. W przypadku planów za pośrednictwem strony widoku kliknij przycisk **New**.
4. Z **subskrypcji** listy rozwijanej wybierz subskrypcję, w którym będzie przechowywany nowy plan.
5. Z **Region** listę rozwijaną, wybierz region dla nowego planu. Zostaną wyświetlone opcje planu dla wybranego regionu **opcje planu** części strony.
6. Z **grupy zasobów** listy rozwijanej wybierz zasób grupy dla tego planu. Aby uzyskać więcej informacji na temat grup zasobów, zobacz [Omówienie usługi Azure Resource Manager](../../azure-resource-manager/resource-group-overview.md).
7. W **Nazwa planu** wpisz nazwę planu.
8. W obszarze **opcje planu**, kliknij odpowiedni poziom rozliczeń dla nowego planu.
9. Kliknij pozycję **Utwórz**.

## <a name="deploying-the-web-service-to-another-region"></a>Wdrażanie usługi sieci web do innego regionu
1. Kliknij przycisk **usług sieci Web** opcji menu.
2. Wybierz usługę sieci Web są wdrażane w nowym regionie.
3. Kliknij przycisk **kopiowania**.
4. W **nazwa usługi sieci Web**, wpisz nową nazwę dla usługi sieci web.
5. W **opisu usługi internetowej**, wpisz opis usługi sieci web.
6. Z **subskrypcji** listy rozwijanej wybierz subskrypcję, w której będą znajdować się nowej usługi sieci web.
7. Z **grupy zasobów** listy rozwijanej wybierz zasób grupy dla usługi sieci web. Aby uzyskać więcej informacji na temat grup zasobów, zobacz [Omówienie usługi Azure Resource Manager](../../azure-resource-manager/resource-group-overview.md).
8. Z **Region** listę rozwijaną, wybierz region, w której ma zostać wdrożona usługa sieci web.
9. Z **konta magazynu** listę rozwijaną, wybierz magazyn konta, w którym będzie przechowywany usługi sieci web.
10. Z **Plan cenowy** listę rozwijaną, wybierz plan w regionie, który został wybrany w kroku 8.
11. Kliknij przycisk **kopiowania**.

