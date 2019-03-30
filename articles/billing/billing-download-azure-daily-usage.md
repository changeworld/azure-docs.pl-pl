---
title: Wyświetlanie i pobieranie Azure użycia i opłat | Dokumentacja firmy Microsoft
description: Opisuje sposób pobrania lub wyświetlenia Azure dziennego użycia i opłat.
keywords: rozliczenia użycia, opłaty za użycie, użycie pobrać, Wyświetl informacje o użyciu, użycie faktury platformy azure, azure
services: billing
documentationcenter: ''
author: genlin
manager: jureid
editor: ''
tags: billing
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 02/19/2019
ms.author: banders
ms.openlocfilehash: 520d3f6a45b44ba2023dee34642f796689f48221
ms.sourcegitcommit: 22ad896b84d2eef878f95963f6dc0910ee098913
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2019
ms.locfileid: "58648850"
---
# <a name="view-and-download-your-azure-usage-and-charges"></a>Przeglądanie i pobieranie swoje użycie platformy Azure oraz opłaty

Jeśli jesteś klientem z umową EA lub mieć [umowy klienta Microsoft](#check-your-access-to-a-microsoft-customer-agreement), możesz pobrać użycia platformy Azure i opłaty w [witryny Azure portal](https://portal.azure.com/). W przypadku innych subskrypcji, przejdź do [Centrum konta platformy Azure](https://account.azure.com/Subscriptions) można pobrać użycia.

Tylko niektóre role mają uprawnień do uzyskania informacji o użycie platformy Azure, takich jak konta administratora lub administratora przedsiębiorstwa. Aby dowiedzieć się więcej na temat uzyskiwania dostępu do informacji dotyczących rozliczeń, zobacz [Manage access to Azure billing using roles (Zarządzanie dostępem do rozliczeń platformy Azure przy użyciu ról)](billing-manage-access.md).

Jeśli masz [umowy klienta Microsoft](#check-your-access-to-a-microsoft-customer-agreement), musi być profil rozliczeniowy właściciel, współautor, Czytelnik, lub faktury manager, aby wyświetlać swoje użycie platformy Azure i opłaty. Aby dowiedzieć się więcej o rolach rozliczeń dla umowy klienta firmy Microsoft, zobacz [rozliczeń profilu role i zadania](billing-understand-mca-roles.md#billing-profile-roles-and-tasks).

## <a name="download-usage-from-the-account-center-csv"></a>Pobierz dane użycia z Centrum kont (.csv)

1. Zaloguj się do [Centrum konta platformy Azure](https://account.windowsazure.com/subscriptions) stanowisku administratora konta.

2. Wybierz subskrypcję, dla którego chcesz informacji faktur i danych użycia.

3. Wybierz **Historia rozliczeń**.

    ![Zrzut ekranu pokazujący opcja Historia rozliczeń](./media/billing-download-azure-invoice-daily-usage-date/Billinghisotry.png)

4. Widać zestawienia sześć ostatnich okresów rozliczeniowych i bieżący nierozliczony okres.

    ![Zrzut ekranu pokazujący okresów rozliczeniowych, opcje, aby pobrać faktury i dzienne użycie i łączne opłaty dla każdego okresu rozliczeniowego](./media/billing-download-azure-invoice-daily-usage-date/billingSum.png)

5. Wybierz pozycję **Wyświetl bieżące zestawienie**, aby zobaczyć oszacowanie opłat na dzień wygenerowania tego oszacowania. Te informacje są aktualizowane tylko raz dziennie i nie może zawierać Twojego użycia. Twoja faktura miesięczne mogą się różnić od tego oszacowania.

    ![Zrzut ekranu pokazujący opcję Wyświetl bieżące zestawienie](./media/billing-download-azure-invoice-daily-usage-date/billingSum2.png)

    ![Zrzut ekranu, który przedstawia oszacowanie opłaty bieżące](./media/billing-download-azure-invoice-daily-usage-date/billingSum3.png)

6. Wybierz pozycję **Pobierz zestawienie użycia**, aby pobrać dane dziennego użycia w postaci pliku CSV. Jeśli będą dostępne dwie wersje, pobierz wersję 2.

    ![Zrzut ekranu pokazujący opcję Pobierz dane użycia](./media/billing-download-azure-invoice-daily-usage-date/DLusage.png)

Tylko Administrator konta może uzyskać dostęp do Centrum konta platformy Azure. Innych administratorów rozliczeń, takich jak właściciel, można uzyskać informacji użycia za pomocą [interfejsów API rozliczeń](billing-usage-rate-card-overview.md).

Aby uzyskać więcej informacji na temat dziennego użycia, zobacz [Informacje o rachunku za korzystanie z platformy Microsoft Azure](billing-understand-your-bill.md). Aby uzyskać pomoc w zarządzaniu kosztami, zobacz [zapobieganie powstawaniu nieoczekiwanych kosztów za pomocą rozliczeń platformy Azure i zarządzania kosztami](billing-getting-started.md).

## <a name="download-usage-for-ea-customers"></a>Pobierz dane użycia dla klientów z umową EA

Administrator działu z widokiem opłaty włączone zasady ani na przeglądanie i pobieranie danych użycia jako klient korzystający z umową EA, musisz być administratorem przedsiębiorstwa, właściciel konta.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
1. Wyszukaj *Cost Management + rozliczenia*.

    ![Zrzut ekranu pokazujący usługi Azure search w portalu](./media/billing-download-azure-invoice-daily-usage-date/portal-cm-billing-search.png)

1. Wybierz **użycie i opłaty za**.
1. Na miesiąc, którą chcesz pobrać, zaznacz **Pobierz**.

## <a name="download-usage-for-your-microsoft-customer-agreement"></a>Pobierz dane użycia dla umowy klienta firmy Microsoft

Jeśli masz umowy klienta firmy Microsoft, możesz pobrać Twoje użycie platformy Azure i opłaty za dla swojego profilu rozliczeń. Musi być profil rozliczeniowy właściciel, współautor, Czytelnik, lub faktury Menedżera pobierania użycia platformy Azure i opłaty za CSV.

### <a name="download-usage-for-billed-charges"></a>Pobierz dane użycia opłaty rozliczane

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. Wyszukiwanie **Cost Management + rozliczenia**.
3. Wybierz profil rozliczeniowy. W zależności od dostęp może być konieczne najpierw wybrać konta rozliczeniowego.
4. Wybierz **faktur**.
5. W siatce faktury Znajdź wiersz faktury odpowiadający użycia, który chcesz pobrać.
6. Kliknij wielokropek (`...`) na końcu wiersza.

    ![Zrzut ekranu pokazujący Wielokropek na końcu wiersza.](./media/billing-download-azure-invoice/billingprofile-invoicegrid.png)

7. Wybierz z menu kontekstowego pobierania **użycia platformy Azure i opłaty za**.

     ![Zrzut ekranu pokazujący użycie platformy Azure i opłaty za wybrane](./media/billing-download-azure-usage/contextmenu-usage.png)

### <a name="download-usage-for-pending-charges"></a>Pobierz dane użycia dla oczekujących opłaty

Można również pobrać użycia data miesiąca dla bieżącego okresu rozliczeniowego. Te opłaty, które nie zostały jeszcze naliczane.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. Wyszukiwanie **Cost Management + rozliczenia**.
3. Wybierz profil rozliczeniowy. W zależności od dostęp może być konieczne najpierw wybrać konta rozliczeniowego.
4. W **Przegląd** bloku Znajdź łącza pobierania poniżej opłaty od początku miesiąca.
5. Wybierz **użycia platformy Azure i opłaty za**.

    ![Zrzut ekranu przedstawiający pobieranie z przeglądu](./media/billing-download-azure-usage/open-usage.png)

## <a name="check-your-access-to-a-microsoft-customer-agreement"></a>Sprawdź swój dostęp do umowy klienta firmy Microsoft
[!INCLUDE [billing-check-mca](../../includes/billing-check-mca.md)]

## <a name="need-help-contact-us"></a>Potrzebujesz pomocy? Skontaktuj się z nami

Jeśli masz pytania lub potrzebujesz pomocy, [Utwórz żądanie obsługi](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Kolejne kroki

Aby dowiedzieć się więcej na temat opłat faktur i danych użycia, zobacz:

- [Zrozumienie warunki na usługi Microsoft Azure szczegółowe użycia](billing-understand-your-usage.md)
- [Opis zawartości rachunku dla systemu Microsoft Azure](billing-understand-your-bill.md)
- [Wyświetl i Pobierz fakturę usługi Microsoft Azure](billing-download-azure-invoice.md)
- [Przeglądanie i pobieranie cen platformy Azure w Twojej organizacji](billing-ea-pricing.md)

Jeśli masz umowy klienta firmy Microsoft, zobacz:

- [Zrozumienie postanowienia umowy klienta znaczenie szczegółowe użycia](billing-mca-understand-your-usage.md)
- [Informacje o opłatach na fakturze umowy klienta firmy Microsoft](billing-mca-understand-your-bill.md)
- [Wyświetl i Pobierz fakturę usługi Microsoft Azure](billing-download-azure-invoice.md)
- [Przeglądanie i pobieranie dokumentów podatkowych dla umowy klienta firmy Microsoft](billing-mca-download-tax-document.md)
- [Przeglądanie i pobieranie cen platformy Azure w Twojej organizacji](billing-ea-pricing.md)
