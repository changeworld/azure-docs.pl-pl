---
title: Wyświetlanie ważne dla Ciebie zaleceń usługi Azure Advisor
description: Wyświetlanie i filtrowanie zaleceń usługi Azure Advisor w celu zmniejszenia hałasu.
ms.topic: article
ms.date: 04/03/2019
ms.openlocfilehash: 10d7b16864f8e449dc51e870c5ff9f20d8c0dc87
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75422376"
---
# <a name="view-azure-advisor-recommendations-that-matter-to-you"></a>Wyświetlanie ważne dla Ciebie zaleceń usługi Azure Advisor

Usługa Azure Advisor zawiera zalecenia ułatwiające optymalizację wdrożeń platformy Azure. W ramach advisora masz dostęp do kilku funkcji, które pomogą Ci zawęzić rekomendacje tylko do tych, które są dla Ciebie ważne.

## <a name="configure-subscriptions-and-resource-groups"></a>Konfigurowanie subskrypcji i grup zasobów

Doradca daje możliwość wyboru subskrypcji i grup zasobów, które są ważne dla Ciebie i Twojej organizacji. Zobaczysz tylko zalecenia dotyczące wybranych subskrypcji i grup zasobów. Domyślnie wszystkie są zaznaczone. Ustawienia konfiguracji mają zastosowanie do subskrypcji lub grupy zasobów, więc te same ustawienia dotyczą wszystkich osób, które mają dostęp do tej subskrypcji lub grupy zasobów. Ustawienia konfiguracji można zmienić w witrynie Azure portal lub programowo.

Aby wprowadzić zmiany w witrynie Azure portal:

1. Otwórz [usługę Azure Advisor](https://aka.ms/azureadvisordashboard) w witrynie Azure portal.

1. Z menu **wybierz opcję Konfiguracja.**

   ![Menu konfiguracji doradcy](./media/view-recommendations/configuration.png)

1. Zaznacz pole wyboru w kolumnie **Dołączanie** dla wszystkich subskrypcji lub grup zasobów, aby otrzymywać rekomendacje klasyfikatora. Jeśli pole jest wyłączone, możesz nie mieć uprawnień do wprowadzania zmian konfiguracji w tej subskrypcji lub grupie zasobów. Dowiedz się więcej o [uprawnieniach w usłudze Azure Advisor](permissions.md).

1. Po dokonaniu zmiany kliknij przycisk **Zastosuj** u dołu.

## <a name="filtering-your-view-in-the-azure-portal"></a>Filtrowanie widoku w witrynie Azure portal

Ustawienia konfiguracji pozostają aktywne do momentu zmiany. Jeśli chcesz ograniczyć widok zaleceń dla pojedynczego wyświetlania, możesz użyć rozwijanych w górnej części panelu Doradca. W panelach Omówienie, Wysoka dostępność, Zabezpieczenia, Wydajność, Koszt i Wszystkie rekomendacje można wybrać subskrypcje, typy zasobów i stan rekomendacji, który ma być widoczny.

   ![Menu filtrowania doradcy](./media/view-recommendations/filtering.png)

## <a name="dismissing-and-postponing-recommendations"></a>Odrzucanie i odkładanie zaleceń

Usługa Azure Advisor umożliwia odrzucanie lub odkładanie zaleceń dotyczących pojedynczego zasobu. Jeśli odrzucisz zalecenie, nie zobaczysz go ponownie, chyba że zostanie ona aktywowana ręcznie. Jednak odroczenie zalecenia pozwala określić czas trwania, po którym zalecenie jest automatycznie aktywowany ponownie. Odroczenie można wykonać w witrynie Azure portal lub programowo.

### <a name="postpone-a-single-recommendation-in-the-azure-portal"></a>Odłożenie pojedynczej rekomendacji w witrynie Azure portal 

1. Otwórz [usługę Azure Advisor](https://aka.ms/azureadvisordashboard) w witrynie Azure portal.
1. Wybierz kategorię rekomendacji, aby wyświetlić rekomendacje
1. Wybierz rekomendację z listy zaleceń
1. Wybierz pozycję Odłóż lub Odrzuć dla zalecenia, które chcesz odroczyć lub odrzucić

     ![Menu filtrowania doradcy](./media/view-recommendations/postpone-dismiss.png)

### <a name="postpone-or-dismiss-a-multiple-recommendations-in-the-azure-portal"></a>Odłożenie lub odrzucenie wielu zaleceń w witrynie Azure portal

1. Otwórz [usługę Azure Advisor](https://aka.ms/azureadvisordashboard) w witrynie Azure portal.
1. Wybierz kategorię rekomendacji, aby wyświetlić rekomendacje.
1. Wybierz rekomendację z listy zaleceń.
1. Zaznacz pole wyboru po lewej stronie wiersza dla wszystkich zasobów, które chcesz odroczyć lub odrzucić zalecenie.
1. Wybierz pozycję **Odłóż** lub **Odrzuć** w lewym górnym rogu tabeli.

     ![Menu filtrowania doradcy](./media/view-recommendations/postpone-dismiss-multiple.png)

> [!NOTE]
> Aby odrzucić lub odroczyć rekomendację, musisz mieć uprawnienia współautora lub właściciela. Dowiedz się więcej o uprawnieniach w usłudze Azure Advisor.

> [!NOTE]
> Jeśli pola wyboru są wyłączone, zalecenia mogą być nadal ładowane. Poczekaj, aż wszystkie zalecenia się załadują, zanim spróbujesz odroczyć lub odrzucić.

### <a name="reactivate-a-postponed-or-dismissed-recommendation"></a>Ponowne aktywowanie przełożonego lub odrzuconego zalecenia

Można aktywować zalecenie, które zostało odroczone lub odrzucone. Tę akcję można wykonać w witrynie Azure portal lub programowo. W witrynie Azure Portal:

1. Otwórz [usługę Azure Advisor](https://aka.ms/azureadvisordashboard) w witrynie Azure portal.

1. Zmień filtr w panelu Przegląd na **Przełożony**. Następnie doradca wyświetla przełożone lub odrzucone zalecenia.

    ![Menu filtrowania doradcy](./media/view-recommendations/activate-postponed.png)

1. Wybierz kategorię, aby wyświetlić zalecenia **przełożone** i **odrzucone.**

1. Wybierz rekomendację z listy zaleceń. Spowoduje to otwarcie rekomendacji z **przełożoną kartę & odrzucone** już wybrane, aby wyświetlić zasoby, dla których to zalecenie zostało odroczone lub odrzucone.

1. Kliknij **przycisk Aktywuj** na końcu wiersza. Po kliknięciu zalecenie jest aktywne dla tego zasobu i tak usunięte z tej tabeli. Zalecenie jest teraz widoczne na karcie **Aktywne.**
 
     ![Menu filtrowania doradcy](./media/view-recommendations/activate-postponed-2.png)

## <a name="next-steps"></a>Następne kroki

W tym artykule wyjaśniono, jak można wyświetlić zalecenia, które są ważne dla Ciebie w usłudze Azure Advisor. Aby dowiedzieć się więcej o doradcy, zobacz: 

- [Co to jest usługa Azure Advisor?](advisor-overview.md)
- [Wprowadzenie do advisora](advisor-get-started.md)
- [Uprawnienia w usłudze Azure Advisor](permissions.md)



