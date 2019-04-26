---
title: Wyświetl zalecenia doradcy platformy Azure, ważne
description: Przeglądanie i filtrowanie zaleceń usługi Azure Advisor do zmniejszenia szumu.
services: advisor
author: kasparks
ms.service: advisor
ms.topic: article
ms.date: 04/03/2019
ms.author: kasparks
ms.openlocfilehash: 9f599a63fd5f52420f1b79e769d4f7bca9683b32
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60467910"
---
# <a name="view-azure-advisor-recommendations-that-matter-to-you"></a>Wyświetl zalecenia doradcy platformy Azure, ważne

Usługa Azure Advisor zawiera zalecenia ułatwiające Optymalizowanie wdrożeń platformy Azure. W ramach usługi Advisor masz dostęp do kilku funkcji, które ułatwiają zawęzić zalecenia tylko te, które mają znaczenie dla Ciebie.

## <a name="configure-subscriptions-and-resource-groups"></a>Konfigurowanie subskrypcji i grup zasobów

Advisor zapewnia możliwość wybrania subskrypcji i grup zasobów, które mają znaczenie dla Ciebie i Twojej organizacji. Widoczne tylko zaleceń dla subskrypcji i grup zasobów, które można wybierać. Domyślnie wszystkie zaznaczone. Ustawienia konfiguracji dotyczy subskrypcji lub grupy zasobów, dzięki czemu te same ustawienia dotyczą wszystkich użytkowników, które ma dostęp do tej subskrypcji lub grupy zasobów. Ustawienia konfiguracji można zmienić w witrynie Azure portal lub programowo.

Aby wprowadzić zmiany w witrynie Azure portal:

1. Otwórz [usługi Azure Advisor](https://aka.ms/azureadvisordashboard) w witrynie Azure portal.

1. Wybierz **konfiguracji** z menu.

   ![Menu konfiguracji usługi Advisor](./media/view-recommendations/configuration.png)

1. Zaznacz pole wyboru **Include** kolumny dla subskrypcji lub grupy zasobów, aby odbierać zalecenia usługi Advisor. Jeśli pole jest wyłączone, możesz nie mieć uprawnień do wprowadzania zmianę konfiguracji na tej subskrypcji lub grupy zasobów. Dowiedz się więcej o [uprawnień w usłudze Azure Advisor](permissions.md).

1. Kliknij przycisk **Zastosuj** u dołu po wprowadzeniu zmiany.

## <a name="filtering-your-view-in-the-azure-portal"></a>Filtrowanie widoku w witrynie Azure portal

Ustawienia konfiguracji pozostają aktywne, dopóki nie zmieniony. Jeśli chcesz ograniczyć widoku zalecenia dotyczące wyświetlania pojedynczej, można użyć listy rozwijane, podany w górnej części panelu Advisor. Z panele — omówienie, wysokiej dostępności, bezpieczeństwa, wydajności, koszt i wszystkich zaleceń można wybrać subskrypcje, typy zasobów i stan zalecenia, które mają być wyświetlane.

   ![Menu filtrowania Advisor](./media/view-recommendations/filtering.png)

## <a name="dismissing-and-postponing-recommendations"></a>Odrzucanie i odracza zalecenia

Usługa Azure Advisor umożliwia odrzucić lub odłożyć zaleceń w ramach jednego zasobu. Jeśli należy odrzucić zalecenia, nie ma go ponownie, chyba że ręcznie uaktywnić. Jednak odracza zalecenie pozwala określić czas, po którym zalecenie automatycznie uruchamiany ponownie. Odracza może odbywać się w witrynie Azure portal lub programowo.

### <a name="postpone-a-single-recommendation-in-the-azure-portal"></a>Odrocz zalecenie pojedynczego w witrynie Azure portal 

1. Otwórz [usługi Azure Advisor](https://aka.ms/azureadvisordashboard) w witrynie Azure portal.
1. Wybierz kategorię zalecenie, aby wyświetlić zalecenia
1. Wybierz zalecenie na liście zaleceń
1. Wybierz Odłóż lub Odrzuć zalecenia, które chcesz odłożyć lub Odrzuć

     ![Menu filtrowania Advisor](./media/view-recommendations/postpone-dismiss.png)

### <a name="postpone-or-dismiss-a-multiple-recommendations-in-the-azure-portal"></a>Odłóż lub odrzucać wiele zaleceń w witrynie Azure portal

1. Otwórz [usługi Azure Advisor](https://aka.ms/azureadvisordashboard) w witrynie Azure portal.
1. Wybierz kategorię zalecenie, aby wyświetlić zalecenia.
1. Wybierz zalecenie z listę zaleceń.
1. Zaznacz pole wyboru po lewej stronie wiersza dla wszystkich zasobów, które chcesz odłożyć lub odrzucać zalecenia.
1. Wybierz **Odłóż** lub **Odrzuć** w lewym górnym rogu tabeli.

     ![Menu filtrowania Advisor](./media/view-recommendations/postpone-dismiss-multiple.png)

> [!NOTE]
> Musisz mieć uprawnienie współautora lub właściciela do odrzucania lub Odrocz zalecenie. Dowiedz się więcej na temat uprawnień w usłudze Azure Advisor.

> [!NOTE]
> Jeśli pola wyboru są wyłączone, nadal może spowodować załadowanie zalecenia. Poczekaj, aż wszystkie zalecenia, aby załadować przed podjęciem próby odroczyć lub odrzucić.

### <a name="reactivate-a-postponed-or-dismissed-recommendation"></a>Ponowne uaktywnianie zalecenia odroczone lub odrzuconych

Można uaktywnić zalecenia zostało odroczone lub odrzucony. Ta akcja może odbywać się w witrynie Azure portal lub programowo. W witrynie Azure Portal:

1. Otwórz [usługi Azure Advisor](https://aka.ms/azureadvisordashboard) w witrynie Azure portal.

1. Zmień filtr na panelu Przegląd **przełożone**. Advisor następnie wyświetla odroczone lub odrzuconych zaleceń.

    ![Menu filtrowania Advisor](./media/view-recommendations/activate-postponed.png)

1. Wybierz kategorię, aby zobaczyć **przełożone** i **odrzucone** zalecenia.

1. Wybierz zalecenie z listę zaleceń. Spowoduje to otwarcie zalecenia z **odroczone i zwinięciu sekcji** kartę jeszcze wybrana, aby wyświetlić zasoby, dla których zalecenie zostało odroczone lub odrzucony.

1. Kliknij pozycję **Aktywuj** na końcu wiersza. Po kliknięciu, zalecane jest aktywny dla tego zasobu i dlatego usunięte z tej tabeli. Zalecenia są teraz widoczne w **Active** kartę.
 
     ![Menu filtrowania Advisor](./media/view-recommendations/activate-postponed-2.png)

## <a name="next-steps"></a>Kolejne kroki

W tym artykule opisano sposób wyświetlania zaleceń, które mają znaczenie dla użytkownika w usłudze Azure Advisor. Aby dowiedzieć się więcej o usłudze Advisor, zobacz: 

- [Co to jest usługa Azure Advisor?](advisor-overview.md)
- [Wprowadzenie do usługi Advisor](advisor-get-started.md)
- [Uprawnienia w usłudze Azure Advisor](permissions.md)



