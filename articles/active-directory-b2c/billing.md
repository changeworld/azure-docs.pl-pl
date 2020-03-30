---
title: Model rozliczeń dla usługi Azure Active Directory B2C
description: Dowiedz się więcej o modelu rozliczeń miesięcznych aktywnych użytkowników usługi Azure AD B2C (MAU) i o tym, jak włączyć rozliczenia dla określonej subskrypcji platformy Azure.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.date: 10/25/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 30ecb1e6faa29482a8d69dd1d08e4f127f515596
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78190011"
---
# <a name="billing-model-for-azure-active-directory-b2c"></a>Model rozliczeń dla usługi Azure Active Directory B2C

Użycie usługi Azure Active Directory B2C (Azure AD B2C) jest rozliczane z połączonej subskrypcji platformy Azure i korzysta z modelu rozliczeń aktywnych użytkowników miesięcznie (MAU). Dowiedz się, jak połączyć zasób usługi Azure AD B2C z subskrypcją i jak działa model rozliczeń mau w poniższych sekcjach.

> [!IMPORTANT]
> Ten artykuł nie zawiera informacji o cenach. Aby uzyskać najnowsze informacje na temat rozliczeń i cen użycia, zobacz [Cennik B2C usługi Azure Active Directory](https://azure.microsoft.com/pricing/details/active-directory-b2c/).

## <a name="monthly-active-users-mau-billing"></a>Miesięczne rozliczanie aktywnych użytkowników (MAU)

Rozliczenia usługi Azure AD B2C są mierzone na podstawie liczby unikatowych użytkowników z aktywnością uwierzytelniania w ciągu miesiąca kalendarzowego, znanej jako miesięczne płatności aktywnych użytkowników (MAU).

Od **1 listopada 2019**r. wszyscy nowo utworzyli dzierżawy usługi Azure AD B2C są rozliczane za miesięcznych aktywnych użytkowników(MAU). Istniejący dzierżawcy, którzy są [połączeni z subskrypcją](#link-an-azure-ad-b2c-tenant-to-a-subscription) w dniu 01 listopada 2019 r. lub później, będą rozliczani za miesięcznych aktywnych użytkowników.Existing tenants that are linked to a subscription on or after 01 November 2019 will be billed per-monthly active users(MAU).

Jeśli masz istniejącą dzierżawę usługi Azure AD B2C, która została połączona z subskrypcją przed 01 listopada 2019 r., możesz wykonać jedną z następujących czynności:

* Uaktualnienie do modelu rozliczeń miesięcznych aktywnych użytkowników (MAU) lub
* Pozostań w modelu rozliczeń za uwierzytelnienie

### <a name="upgrade-to-monthly-active-users-billing-model"></a>Uaktualnienie do modelu rozliczeń aktywnych użytkowników miesięcznie

Właściciele subskrypcji platformy Azure, którzy mają dostęp administracyjny do zasobu usługi Azure AD B2C, mogą przełączyć się na model rozliczeń mau. Opcje rozliczeń są skonfigurowane w zasobie usługi Azure AD B2C.

Przejście na miesięczne aktywne płatności użytkowników (MAU) jest **nieodwracalne.** Po przekonwertowaniu zasobu usługi Azure AD B2C na model rozliczeń oparty na mau nie można przywrócić tego zasobu do modelu rozliczeń na uwierzytelnianie.

Poniżej opisano, jak przełączyć się na rozliczenia mau dla istniejącego zasobu usługi Azure AD B2C:

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com) jako właściciel subskrypcji.
1. Wybierz filtr **subskrypcja Katalog +** w górnym menu, a następnie wybierz katalog usługi Azure AD B2C, który chcesz uaktualnić do rozliczeń mau.<br/>
    ![Filtr katalogów i subskrypcji w witrynie Azure portal](./media/billing/portal-mau-01-select-b2c-directory.png)
1. W menu po lewej stronie wybierz pozycję **Azure AD B2C**. Możesz też wybrać **wszystkie usługi** i wyszukać i wybrać pozycję Azure **AD B2C**.
1. Na stronie **Przegląd** dzierżawy usługi Azure AD B2C wybierz łącze w obszarze **Nazwa zasobu**. Jesteś kierowany do zasobu usługi Azure AD B2C w dzierżawie usługi Azure AD.<br/>
    ![Wyróżnione łącze zasobów usługi Azure AD B2C w witrynie Azure portal](./media/billing/portal-mau-02-b2c-resource-link.png)
1. Na stronie **Przegląd** zasobu usługi Azure AD B2C w obszarze **Jednostki rozliczane**wybierz łącze **Na uwierzytelnianie (zmień na MAU).**<br/>
    ![Łącze zmień na mau wyróżnione w witrynie Azure portal](./media/billing/portal-mau-03-change-to-mau-link.png)
1. Wybierz **pozycję Potwierdź,** aby ukończyć uaktualnienie do rozliczeń mau.<br/>
    ![Okno dialogowe potwierdzenia rozliczeń oparte na mau w witrynie Azure portal](./media/billing/portal-mau-04-confirm-change-to-mau.png)

### <a name="what-to-expect-when-you-transition-to-mau-billing-from-per-authentication-billing"></a>Czego można się spodziewać po przejściu do rozliczeń mau z rozliczeń za uwierzytelnienie

Pomiar oparty na mau jest włączona, gdy tylko ty, właściciel subskrypcji/zasobu, potwierdzisz zmianę. Miesięczny rachunek będzie odzwierciedlał jednostki uwierzytelniania rozliczane aż do zmiany, a nowe jednostki mau począwszy od zmiany.

Użytkownicy nie są liczone dwukrotnie w miesiącu przejściowym. Unikatowy aktywni użytkownicy, którzy uwierzytelniają się przed zmianą, są obciążani stawką za uwierzytelnienie w miesiącu kalendarzowym. Ci sami użytkownicy nie są uwzględniane w obliczeniach mau dla pozostałej części cyklu rozliczeniowego subskrypcji. Przykład:

* Dzierżawa Contoso B2C ma 1000 użytkowników. 250 użytkowników jest aktywnych w danym miesiącu. Administrator subskrypcji zmienia się z uwierzytelniania na miesięcznych aktywnych użytkowników (MAU) w dniu 10 miesiąca.
* Rozliczenia za 1-10-te są rozliczane przy użyciu modelu uwierzytelniania.
  * Jeśli 100 użytkowników zaloguje się w tym okresie (1-10), ci użytkownicy są oznaczone jako *płatne za miesiąc*.
* Rozliczenia od 10 (efektywny czas przejścia) jest rozliczane według stawki MAU.
  * Jeśli dodatkowe 150 użytkowników zaloguje się w tym okresie (10-30), tylko dodatkowe 150 są rozliczane.
  * Ciągła aktywność pierwszych 100 użytkowników nie ma wpływu na rozliczenia przez pozostałą część miesiąca kalendarzowego.

W okresie rozliczeniowym przejścia właściciel subskrypcji prawdopodobnie zobaczy wpisy dla obu metod (uwierzytelniania na i na mau) są wyświetlane w zestawie rozliczeniowym subskrypcji platformy Azure:

* Wpis użycia do daty/godziny zmiany, która odzwierciedla uwierzytelniania.
* Wpis użycia po zmianie, który odzwierciedla miesięcznych aktywnych użytkowników (MAU).

Aby uzyskać najnowsze informacje na temat rozliczeń i cen użycia usługi Azure AD B2C, zobacz [Cennik B2C usługi Azure Active Directory](https://azure.microsoft.com/pricing/details/active-directory-b2c/).

## <a name="link-an-azure-ad-b2c-tenant-to-a-subscription"></a>Łączenie dzierżawy usługi Azure AD B2C z subskrypcją

Opłaty za użycie usługi Azure Active Directory B2C (Azure AD B2C) są naliczane do subskrypcji platformy Azure. Po utworzeniu dzierżawy usługi Azure AD B2C administrator dzierżawy musi jawnie połączyć dzierżawę usługi Azure AD B2C z subskrypcją platformy Azure.

Łącze subskrypcji jest osiągane przez utworzenie *zasobu* usługi Azure AD B2C w ramach docelowej subskrypcji platformy Azure. Kilka zasobów usługi Azure AD B2C można utworzyć w ramach jednej subskrypcji platformy Azure, wraz z innymi zasobami platformy Azure, takimi jak maszyny wirtualne, konta magazynu i aplikacje logiki. Możesz zobaczyć wszystkie zasoby w ramach subskrypcji, przechodząc do dzierżawy usługi Azure Active Directory (Azure AD), z którą jest skojarzona subskrypcja.

Subskrypcja powiązana z dzierżawą usługi Azure AD B2C może służyć do rozliczania użycia usługi Azure AD B2C lub innych zasobów platformy Azure, w tym dodatkowych zasobów usługi Azure AD B2C. Nie można użyć do dodawania innych usług opartych na licencji platformy Azure ani licencji usługi Office 365 w dzierżawie usługi Azure AD B2C.

### <a name="prerequisites"></a>Wymagania wstępne

* [Subskrypcja platformy Azure](https://azure.microsoft.com/free/)
* [Dzierżawa usługi Azure AD B2C,](tutorial-create-tenant.md) którą chcesz połączyć z subskrypcją
  * Musisz być administratorem dzierżawy
  * Dzierżawca nie może być już połączony z subskrypcją

### <a name="create-the-link"></a>Tworzenie łącza

1. Zaloguj się do [Portalu Azure](https://portal.azure.com).
1. Wybierz filtr **subskrypcja katalogu +** w górnym menu, a następnie wybierz katalog zawierający subskrypcję platformy Azure, której chcesz użyć (*nie* katalog zawierający dzierżawę usługi Azure AD B2C).
1. Wybierz **pozycję Utwórz zasób**, wprowadź `Active Directory B2C` w polu **Wyszukaj w portalu Marketplace,** a następnie wybierz pozycję **Azure Active Directory B2C**.
1. Wybierz **pozycję Utwórz**
1. Wybierz **pozycję Połącz istniejącą dzierżawę usługi Azure AD B2C z moją subskrypcją platformy Azure**.
1. Wybierz **dzierżawę usługi Azure AD B2C** z listy rozwijanej. Wyświetlane są tylko dzierżawy, dla których jesteś administratorem globalnym i które nie są jeszcze połączone z subskrypcją. Pole **Nazwa zasobu usługi Azure AD B2C** jest wypełniana nazwą domeny wybranej dzierżawy usługi Azure AD B2C.
1. Wybierz aktywną **subskrypcję** platformy Azure, której jesteś administratorem.
1. W obszarze **Grupa zasobów**wybierz pozycję **Utwórz nowy**, a następnie określ lokalizację grupy **zasobów**. Ustawienia grupy zasobów w tym miejscu nie mają wpływu na lokalizację dzierżawy usługi Azure AD B2C, wydajność lub stan rozliczeń.
1. Wybierz **pozycję Utwórz**.
    ![Strona tworzenia zasobów usługi Azure AD B2C w witrynie Azure portal](./media/billing/portal-01-create-b2c-resource-page.png)

Po wykonaniu tych kroków dla dzierżawy usługi Azure AD B2C subskrypcja platformy Azure jest naliczane zgodnie ze szczegółami usługi Azure Direct lub Enterprise Agreement, jeśli ma to zastosowanie.

### <a name="manage-your-azure-ad-b2c-tenant-resources"></a>Zarządzanie zasobami dzierżawy usługi Azure AD B2C

Po utworzeniu zasobu usługi Azure AD B2C w ramach subskrypcji platformy Azure powinien zostać wyświetlony nowy zasób typu "dzierżawa B2C" z innymi zasobami platformy Azure.

Zasób ten służy do:

* Przejdź do subskrypcji, aby przejrzeć informacje rozliczeniowe
* Pobierz identyfikator dzierżawy dzierżawy usługi Azure AD B2C w formacie GUID
* Przejdź do dzierżawy usługi Azure AD B2C
* Prześlij prośbę o pomoc techniczną
* Przenoszenie zasobu dzierżawy usługi Azure AD B2C do innej subskrypcji lub grupy zasobów platformy Azure

![Strona Ustawienia zasobów B2C w witrynie Azure portal](./media/billing/portal-02-b2c-resource-overview.png)

### <a name="regional-restrictions"></a>Ograniczenia regionalne

Jeśli ustalono ograniczenia regionalne dla tworzenia zasobów platformy Azure w ramach subskrypcji, to ograniczenie może uniemożliwić tworzenie zasobu usługi Azure AD B2C.

Aby rozwiązać ten problem, zrelaksuj ograniczenia regionalne.

## <a name="azure-cloud-solution-providers-csp-subscriptions"></a>Subskrypcje dostawców rozwiązań w chmurze (CSP) platformy Azure

Subskrypcje dostawców rozwiązań w chmurze platformy Azure (CSP) są obsługiwane w usłudze Azure AD B2C. Ta funkcja jest dostępna przy użyciu interfejsów API lub witryny Azure portal dla usługi Azure AD B2C i dla wszystkich zasobów platformy Azure. Administratorzy subskrypcji usług CSP mogą łączyć, przenosić i usuwać relacje z usługą Azure AD B2C w połączeniu z innymi zasobami platformy Azure.

Na zarządzanie usługą Azure AD B2C przy użyciu kontroli dostępu opartej na rolach nie ma wpływu skojarzenie między dzierżawą usługi Azure AD B2C a subskrypcją usługi Azure CSP. Kontrola dostępu oparta na rolach jest osiągana przy użyciu ról opartych na dzierżawie, a nie ról opartych na subskrypcji.

## <a name="change-the-azure-ad-b2c-tenant-billing-subscription"></a>Zmienianie subskrypcji rozliczeniowej dzierżawy usługi Azure AD B2C

Dzierżawy usługi Azure AD B2C można przenieść do innej subskrypcji, jeśli subskrypcje źródłowe i docelowe istnieją w tej samej dzierżawie usługi Azure Active Directory.

Aby dowiedzieć się, jak przenieść zasoby platformy Azure, takie jak dzierżawa usługi Azure AD B2C, do innej subskrypcji, zobacz [Przenoszenie zasobów do nowej grupy zasobów lub subskrypcji.](../azure-resource-manager/management/move-resource-group-and-subscription.md)

Przed zainicjowaniem przeprowadzki należy przeczytać cały artykuł, aby w pełni zrozumieć ograniczenia i wymagania dotyczące takiego ruchu. Oprócz instrukcji dotyczących przenoszenia zasobów zawiera krytyczne informacje, takie jak lista kontrolna przed przenoszeniem i sposób sprawdzania poprawności operacji przenoszenia.

## <a name="next-steps"></a>Następne kroki

Aby uzyskać najnowsze informacje o cenach, zobacz [Cennik B2C usługi Azure Active Directory](https://azure.microsoft.com/pricing/details/active-directory-b2c/).
