---
title: Zarządzanie zasobami platformy Azure za pomocą witryny Azure portal | Dokumentacja firmy Microsoft
description: Użyj witryny Azure portal i usługi Azure Resource Manager do zarządzania zasobami. Pokazuje, jak pracować z pulpitów nawigacyjnych do monitorowania zasobów.
services: azure-resource-manager,azure-portal
documentationcenter: ''
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: 0725bbf2-5913-4c07-af6e-24e11d957fbc
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 11/15/2016
ms.author: tomfitz
ms.openlocfilehash: 7398e01a46b5d296f26905e2063acdb98383f567
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/11/2018
ms.locfileid: "38600365"
---
# <a name="manage-azure-resources-through-portal"></a>Zarządzanie zasobami platformy Azure za pośrednictwem portalu

W tym artykule pokazano, jak [witryny Azure portal](https://portal.azure.com) z [usługi Azure Resource Manager](resource-group-overview.md) do zarządzania zasobami platformy Azure. Aby dowiedzieć się o wdrażanie zasobów za pośrednictwem portalu, zobacz [wdrażanie zasobów za pomocą szablonów usługi Resource Manager i witryny Azure portal](resource-group-template-deploy-portal.md).

[!INCLUDE [Handle personal data](../../includes/gdpr-intro-sentence.md)]

## <a name="manage-resource-groups"></a>Zarządzanie grupami zasobów

Grupa zasobów to kontener, który zawiera powiązane zasoby dla rozwiązania platformy Azure. Grupa zasobów może zawierać wszystkie zasoby dla rozwiązania lub tylko te zasoby, które mają być zarządzane jako grupa. Użytkownik decyduje o sposobie przydziału zasobów do grup zasobów pod kątem tego, co jest najbardziej odpowiednie dla danej organizacji. Ogólnie rzecz biorąc Dodaj zasoby, które współużytkują ten sam cykl życia w tej samej grupie zasobów, więc łatwe wdrażanie, aktualizowanie i usuwanie ich jako grupa. 

Grupa zasobów przechowuje metadane dotyczące zasobów. Z tego powodu określając lokalizację dla grupy zasobów, określasz miejsce, w którym przechowywane są metadane. Dla zachowania zgodności może być konieczne upewnienie się, że dane są przechowywane w odpowiednim regionie.

1. Aby wyświetlić wszystkie grupy zasobów w ramach subskrypcji, wybierz **grup zasobów**.
   
    ![Przeglądaj grupy zasobów](./media/resource-group-portal/browse-groups.png)
2. Aby utworzyć pustą grupę zasobów, wybierz **Dodaj**.
   
    ![Dodaj grupę zasobów](./media/resource-group-portal/add-resource-group.png)
3. Podaj nazwę i lokalizację dla nowej grupy zasobów. Wybierz pozycję **Utwórz**.
   
    ![Utwórz grupę zasobów](./media/resource-group-portal/create-empty-group.png)
4. Musisz wybrać **Odśwież** aby zobaczyć grupę ostatnio utworzonego zasobu.
   
    ![Odśwież grupę zasobów](./media/resource-group-portal/refresh-resource-groups.png)
5. Aby dostosować informacji wyświetlanych dla grup zasobów, wybierz opcję **kolumn**.
   
    ![Dostosowywanie kolumn](./media/resource-group-portal/select-columns.png)
6. Wybierz kolumny, które chcesz dodać, a następnie wybierz **aktualizacji**.
   
    ![Dodawanie kolumn](./media/resource-group-portal/add-columns.png)
7. Aby dowiedzieć się więcej na temat wdrażania zasobów do nowej grupy zasobów, zobacz [wdrażanie zasobów za pomocą szablonów usługi Resource Manager i witryny Azure portal](resource-group-template-deploy-portal.md).
8. Aby uzyskać szybki dostęp do grupy zasobów grupy zasobów można przypiąć do pulpitu nawigacyjnego.
   
    ![Grupa zasobów numeru PIN](./media/resource-group-portal/pin-group.png)
9. Pulpit nawigacyjny Wyświetla grupy zasobów i jego zasobów. Możesz wybrać grupy zasobów albo dowolnego z jego zasobów można przejść do elementu.
   
    ![Grupa zasobów numeru PIN](./media/resource-group-portal/show-resource-group-dashboard.png)

## <a name="tag-resources"></a>Tagowanie zasobów
Znaczniki można dodawać do grup zasobów i zasobów w celu logicznego uporządkowania Twoich zasobów. Aby uzyskać informacji dotyczących pracy z tagami, zobacz [porządkowanie zasobów na platformie Azure za pomocą tagów](resource-group-using-tags.md).

[!INCLUDE [resource-manager-tag-resource](../../includes/resource-manager-tag-resources.md)]

## <a name="monitor-resources"></a>Monitorowanie zasobów
Po wybraniu zasobu, w portalu jest wyświetlany domyślny wykresów i tabel do monitorowania tego typu zasobu.

1. Wybierz zasób i zwróć uwagę, **monitorowanie** sekcji. Zawiera wykresy, które są istotne dla typu zasobu. Na poniższej ilustracji przedstawiono domyślne danych monitorowania dla konta magazynu.
   
    ![Pokaż monitorowania](./media/resource-group-portal/show-monitoring.png)
2. Sekcja można przypiąć do pulpitu nawigacyjnego, wybierając wielokropek (...) powyżej sekcji. Można również dostosować rozmiar sekcji lub usuń go całkowicie. Na poniższej ilustracji przedstawiono, jak przypiąć, dostosowywania lub usuwania sekcji procesora CPU i pamięci.
   
    ![sekcja kodu PIN](./media/resource-group-portal/pin-cpu-section.png)
3. Po przypięciu sekcji do pulpitu nawigacyjnego, zobaczysz podsumowania na pulpicie nawigacyjnym. I wybierając ją od razu spowoduje przejście do szczegółów na temat danych.
   
    ![Wyświetl pulpit nawigacyjny](./media/resource-group-portal/view-startboard.png)
4. Aby całkowicie dostosować dane monitorowania za pośrednictwem portalu, przejdź do domyślnego pulpitu nawigacyjnego i wybierz **nowy pulpit nawigacyjny**.
   
    ![pulpit nawigacyjny](./media/resource-group-portal/dashboard.png)
5. Nazwij nowy pulpit nawigacyjny, a następnie przeciągnij Kafelki na pulpicie nawigacyjnym. Kafelki są filtrowane według różnych opcji.
   
    ![pulpit nawigacyjny](./media/resource-group-portal/create-dashboard.png)
   
     Aby dowiedzieć się więcej o pracy z pulpitów nawigacyjnych, zobacz [tworzenie i udostępnianie pulpitów nawigacyjnych w witrynie Azure portal](../azure-portal/azure-portal-dashboards.md).

## <a name="manage-resources"></a>Zarządzanie zasobami
Podczas przeglądania zasobu w portalu, zobaczysz opcje służące do zarządzania tego zasobu.

![Zarządzanie zasobami](./media/resource-group-portal/manage-resources.png)

Z tych opcji możesz wykonywać operacje, takie jak uruchamianie i zatrzymywanie maszyny wirtualnej lub ponownej konfiguracji właściwości maszyny wirtualnej.

## <a name="move-resources"></a>Przenoszenie zasobów
Jeśli zachodzi potrzeba przenoszenie zasobów do innej grupy zasobów lub innej subskrypcji, zobacz [przenoszenie zasobów do nowej grupy zasobów lub subskrypcji](resource-group-move-resources.md).

## <a name="lock-resources"></a>Blokowanie zasobów
Można zablokować subskrypcji, grupy zasobów lub zasobu, aby uniemożliwić innym użytkownikom w organizacji przypadkowo usuwanie i modyfikowanie zasoby o znaczeniu krytycznym. Aby uzyskać więcej informacji, zobacz [Lock resources with Azure Resource Manager](resource-group-lock-resources.md) (Blokowanie zasobów w usłudze Azure Resource Manager).

[!INCLUDE [resource-manager-lock-resources](../../includes/resource-manager-lock-resources.md)]

## <a name="view-your-subscription-and-costs"></a>Wyświetlanie subskrypcji i kosztów
Możesz przeglądać informacje o subskrypcji i zestawienia kosztów dla wszystkich zasobów. Wybierz **subskrypcje** i subskrypcję, którą chcesz wyświetlić. Może mieć tylko jedną subskrypcję, aby wybrać.

![subskrypcja](./media/resource-group-portal/select-subscription.png)

Zostanie wyświetlona ocena wydajności.

![współczynnik spalania](./media/resource-group-portal/burn-rate.png)

Ponadto rozbicie kosztów według typów zasobów.

![koszt zasobów](./media/resource-group-portal/cost-by-resource.png)

## <a name="export-template"></a>Eksportowanie szablonu
Po skonfigurowaniu grupy zasobów, można wyświetlić szablon usługi Resource Manager dla grupy zasobów. Eksportowanie szablonu oferuje dwie korzyści:

1. Można łatwo automatyzować przyszłych wdrożeń rozwiązania, ponieważ szablon zawiera całej infrastruktury.
2. Należy zaznajomić się ze składnią szablonu przez wyszukiwanie w JavaScript Object Notation (JSON) reprezentujący rozwiązania.

Aby uzyskać instrukcje, zobacz [szablonu eksportu usługi Azure Resource Manager z istniejących zasobów](resource-manager-export-template.md).

## <a name="delete-resource-group-or-resources"></a>Usuń grupę zasobów lub zasobów
Usunięcie grupy zasobów powoduje usunięcie wszystkich zasobów, które są w nim zawarte. Można również usunąć poszczególne zasoby w grupie zasobów. Usunięcie grupy zasobów, należy zachować ostrożność. Tej grupy zasobów może zawierać zasoby, które zależą od zasobów w innych grupach zasobów.

![Usuń grupę](./media/resource-group-portal/delete-group.png)

## <a name="next-steps"></a>Kolejne kroki
* Aby wyświetlić dzienniki aktywności, zobacz [inspekcji operacji przy użyciu usługi Resource Manager](resource-group-audit.md).
* Aby wyświetlić szczegóły dotyczące wdrożenia, zobacz [wyświetlanie operacji wdrażania](resource-manager-deployment-operations.md).
* Aby wdrożyć zasoby za pośrednictwem portalu, zobacz [wdrażanie zasobów za pomocą szablonów usługi Resource Manager i witryny Azure portal](resource-group-template-deploy-portal.md).
* Aby zarządzać dostępem do zasobów, zobacz [zarządzanie dostępem do zasobów subskrypcji platformy Azure za pomocą przypisań ról](../role-based-access-control/role-assignments-portal.md).
* Aby uzyskać instrukcje dla przedsiębiorstw dotyczące użycia usługi Resource Manager w celu efektywnego zarządzania subskrypcjami, zobacz [Azure enterprise scaffold - prescriptive subscription governance](/azure/architecture/cloud-adoption-guide/subscription-governance) (Szkielet platformy Azure dla przedsiębiorstwa — narzucony nadzór subskrypcji).

