---
title: Wyświetlanie i pobieranie informacji o użyciu i opłatach platformy Azure
description: Opisuje sposób pobierania lub wyświetlania danych dziennego użycia i opłat platformy Azure.
keywords: rozliczanie użycia, opłaty za użycie, pobieranie danych użycia, wyświetlanie użycia, faktura platformy azure, użycie platformy azure
author: bandersmsft
manager: jureid
tags: billing
ms.service: cost-management-billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 10/01/2019
ms.author: banders
ms.openlocfilehash: dcf4e3b9d85909c8f1d149c9d1940a6755b431a1
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/20/2019
ms.locfileid: "74224010"
---
# <a name="view-and-download-your-azure-usage-and-charges"></a>Wyświetlanie i pobieranie danych na temat użycia i opłat na platformie Azure

Dzienny podział użycia platformy Azure i opłaty możesz pobrać w witrynie Azure Portal. Tylko niektóre role, takie jak administrator konta i administrator przedsiębiorstwa, mają uprawnienie do pobierania danych użycia platformy Azure. Aby dowiedzieć się więcej na temat uzyskiwania dostępu do informacji dotyczących rozliczeń, zobacz [Manage access to Azure billing using roles (Zarządzanie dostępem do rozliczeń platformy Azure przy użyciu ról)](billing-manage-access.md).

Jeśli masz umowę klienta firmy Microsoft, musisz być właścicielem, współautorem, czytelnikiem lub menedżerem faktur dla profilu rozliczeniowego, aby móc wyświetlić informacje dotyczące użycia i opłat platformy Azure.  Jeśli masz umowę partnerską firmy Microsoft, tylko administrator globalny lub agent administracyjny w organizacji partnerskiej firmy Microsoft może wyświetlać i pobierać użycie oraz opłaty platformy Azure. [Sprawdź typ konta rozliczeniowego w witrynie Azure Portal](#check-your-billing-account-type).

## <a name="download-usage-from-the-azure-portal-csv"></a>Pobieranie użycia z witryny Azure Portal (csv)

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
1. Wyszukaj pozycję *Zarządzanie kosztami i rozliczenia*.

    ![Zrzut ekranu przedstawiający wyszukiwanie w witrynie Azure Portal](./media/billing-download-azure-usage/portal-cm-billing-search.png)

1. W zależności od praw dostępu może być konieczne wybranie konta rozliczeniowego lub profilu rozliczeniowego.
1. W menu po lewej stronie wybierz pozycję **Faktury** w obszarze **Rozliczenia**.
1. W siatce faktur znajdź wiersz okresu rozliczeniowego odpowiadający danym użycia, które chcesz pobrać.
1. Kliknij ikonę pobierania lub symbol wielokropka (`...`) po prawej stronie.
1. Wybierz pozycję **Pobierz informacje o użyciu platformy Azure i opłatach** z menu pobierania.

## <a name="download-usage-for-ea-customers"></a>Pobieranie zestawienia użycia dla klientów z umową EA

Aby przeglądać dane użycia jako klient z umową EA i pobierać je, musisz być administratorem przedsiębiorstwa, właścicielem konta lub administratorem działu z włączonymi zasadami wyświetlania opłat.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
1. Wyszukaj pozycję *Zarządzanie kosztami i rozliczenia*.

    ![Zrzut ekranu przedstawiający wyszukiwanie w witrynie Azure Portal](./media/billing-download-azure-usage/portal-cm-billing-search.png)

1. Wybierz pozycję **Użycie i opłaty**.
1. Wybierz pozycję **Pobierz** dla miesiąca, dla którego chcesz pobrać dane.

## <a name="download-usage-for-pending-charges"></a>Pobieranie danych użycia dla oczekujących opłat

Jeśli masz umowę klienta firmy Microsoft, możesz pobrać użycie za bieżący miesiąc dla bieżącego okresu rozliczeniowego. Dotyczą one opłat za użycie, które nie zostały jeszcze rozliczone.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. Wyszukaj pozycję *Zarządzanie kosztami i rozliczenia*.
3. Wybierz profil rozliczeniowy. W zależności od praw dostępu może być konieczne wybranie najpierw konta rozliczeniowego.
4. W obszarze **Przegląd** znajdź linki pobierania pod opłatami za bieżący miesiąc.
5. Wybierz pozycję **Użycie i opłaty platformy Azure**.

    ![Zrzut ekranu przedstawiający pobieranie z obszaru Przegląd](./media/billing-download-azure-usage/open-usage.png)

## <a name="check-your-billing-account-type"></a>Sprawdzanie typu konta rozliczeniowego
[!INCLUDE [billing-check-account-type](../../includes/billing-check-account-type.md)]

## <a name="need-help-contact-us"></a>Potrzebujesz pomocy? Skontaktuj się z nami.

Jeśli masz pytania lub potrzebujesz pomocy, [utwórz wniosek o pomoc techniczną](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej na temat faktur i opłat za użycie, zobacz:

- [Omówienie terminów dotyczących szczegółów użycia platformy Microsoft Azure](billing-understand-your-usage.md)
- [Opis zawartości rachunku za korzystanie z platformy Microsoft Azure](billing-understand-your-bill.md)
- [Wyświetlanie i pobieranie faktury platformy Microsoft Azure](billing-download-azure-invoice.md)
- [Wyświetlanie i pobieranie cennika platformy Azure organizacji](billing-ea-pricing.md)

Jeśli masz umowę klienta firmy Microsoft, zobacz:

- [Omówienie terminów dotyczących szczegółów użycia platformy Azure w ramach umowy klienta firmy Microsoft](billing-mca-understand-your-usage.md)
- [Omówienie opłat na fakturze za umowę klienta firmy Microsoft](billing-mca-understand-your-bill.md)
- [Wyświetlanie i pobieranie faktury platformy Microsoft Azure](billing-download-azure-invoice.md)
- [Wyświetlanie i pobieranie dokumentów podatkowych dotyczących umowy klienta firmy Microsoft](billing-mca-download-tax-document.md)
- [Wyświetlanie i pobieranie cennika platformy Azure organizacji](billing-ea-pricing.md)
