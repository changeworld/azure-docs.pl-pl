---
title: Wyświetlanie i pobieranie informacji o użyciu i opłatach platformy Azure
description: Opisuje sposób pobierania lub wyświetlania danych dziennego użycia i opłat platformy Azure.
keywords: rozliczanie użycia, opłaty za użycie, pobieranie danych użycia, wyświetlanie użycia, faktura platformy azure, użycie platformy azure
author: bandersmsft
manager: jureid
tags: billing
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/01/2019
ms.author: banders
ms.openlocfilehash: 7d2d7be562eaaa7dd21e63735f5697ffe5a62f8a
ms.sourcegitcommit: 3e7646d60e0f3d68e4eff246b3c17711fb41eeda
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/11/2019
ms.locfileid: "67491445"
---
# <a name="view-and-download-your-azure-usage-and-charges"></a>Wyświetlanie i pobieranie danych na temat użycia i opłat na platformie Azure

Jeśli jesteś klientem z umową EA lub masz [umowę klienta firmy Microsoft](#check-your-access-to-a-microsoft-customer-agreement), możesz pobrać dane użycia i opłat platformy Azure w [witrynie Azure Portal](https://portal.azure.com/). W przypadku innych subskrypcji przejdź do [Centrum konta platformy Azure](https://account.azure.com/Subscriptions), aby pobrać dane użycia.

Tylko niektóre role, takie jak administrator konta i administrator przedsiębiorstwa, mają uprawnienie do pobierania danych użycia platformy Azure. Aby dowiedzieć się więcej na temat uzyskiwania dostępu do informacji dotyczących rozliczeń, zobacz [Manage access to Azure billing using roles (Zarządzanie dostępem do rozliczeń platformy Azure przy użyciu ról)](billing-manage-access.md).

Jeśli masz [umowę klienta firmy Microsoft](#check-your-access-to-a-microsoft-customer-agreement), musisz być właścicielem, współautorem, czytelnikiem lub menedżerem faktur dla profilu rozliczeniowego, aby móc wyświetlić informacje dotyczące użycia i opłat platformy Azure. Aby dowiedzieć się więcej o rolach rozliczeniowych na potrzeby umów klienta firmy Microsoft, zobacz [Zadania i role profilu rozliczeniowego](billing-understand-mca-roles.md#billing-profile-roles-and-tasks).

## <a name="download-usage-from-the-account-center-csv"></a>Pobieranie danych użycia z Centrum konta (CSV)

1. Zaloguj się do [Centrum konta platformy Azure](https://account.windowsazure.com/subscriptions) jako administrator konta.

2. Wybierz subskrypcję, dla której chcesz otrzymać fakturę i dane użycia.

3. Wybierz pozycję **HISTORIA ROZLICZEŃ**.

    ![Zrzut ekranu przedstawiający opcję historii rozliczeń](./media/billing-download-azure-invoice-daily-usage-date/Billinghisotry.png)

4. Dostępne są zestawienia za sześć ostatnich okresów rozliczeniowych i bieżący, nierozliczony okres.

    ![Zrzut ekranu pokazujący okresy rozliczeniowe, opcję pobierania faktury i danych dziennego użycia oraz łączne opłaty za każdy okres rozliczeniowy](./media/billing-download-azure-invoice-daily-usage-date/billingSum.png)

5. Wybierz pozycję **Wyświetl bieżące zestawienie**, aby zobaczyć oszacowanie opłat na dzień wygenerowania tego oszacowania. Te informacje są aktualizowane tylko raz dziennie i mogą nie uwzględniać całego użycia. Rachunek miesięczny może różnić się od podanego oszacowania.

    ![Zrzut ekranu przedstawiający opcję Wyświetl bieżące zestawienie](./media/billing-download-azure-invoice-daily-usage-date/billingSum2.png)

    ![Zrzut ekranu przedstawiający oszacowanie bieżących opłat](./media/billing-download-azure-invoice-daily-usage-date/billingSum3.png)

6. Wybierz pozycję **Pobierz zestawienie użycia**, aby pobrać dane dziennego użycia w postaci pliku CSV. Jeśli będą dostępne dwie wersje, pobierz wersję 2.

    ![Zrzut ekranu przedstawiający opcję Pobierz zestawienie użycia](./media/billing-download-azure-invoice-daily-usage-date/DLusage.png)

Tylko administrator konta może uzyskać dostęp do Centrum konta platformy Azure. Inni administratorzy rozliczeń, tacy jak właściciel, mogą uzyskać informacje dotyczące użycia za pomocą [interfejsów API rozliczeń](billing-usage-rate-card-overview.md).

Aby uzyskać więcej informacji na temat dziennego użycia, zobacz [Informacje o rachunku za korzystanie z platformy Microsoft Azure](billing-understand-your-bill.md). Aby uzyskać pomoc dotyczącą zarządzania kosztami, zobacz [Zapobieganie powstawaniu nieoczekiwanych kosztów w rozliczeniach platformy Azure i zarządzanie kosztami](billing-getting-started.md).

## <a name="download-usage-for-ea-customers"></a>Pobieranie zestawienia użycia dla klientów z umową EA

Aby przeglądać dane użycia jako klient z umową EA i pobierać je, musisz być administratorem przedsiębiorstwa, właścicielem konta lub administratorem działu z włączonymi zasadami wyświetlania opłat.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
1. Wyszukaj pozycję *Zarządzanie kosztami i rozliczenia*.

    ![Zrzut ekranu przedstawiający wyszukiwanie w witrynie Azure Portal](./media/billing-download-azure-invoice-daily-usage-date/portal-cm-billing-search.png)

1. Wybierz pozycję **Użycie i opłaty**.
1. Wybierz pozycję **Pobierz** dla miesiąca, dla którego chcesz pobrać dane.

## <a name="download-usage-for-your-microsoft-customer-agreement"></a>Pobieranie zestawienia użycia dla umowy klienta firmy Microsoft

Jeśli masz umowę klienta firmy Microsoft, możesz pobrać dane użycia i opłat platformy Azure dla swojego profilu rozliczeniowego. Aby pobierać dane użycia i opłat platformy Azure w formacie CSV z witryny Azure Portal, musisz być właścicielem, współautorem, czytelnikiem lub menedżerem faktur dla profilu rozliczeniowego.

### <a name="download-usage-for-billed-charges"></a>Pobieranie zestawienia użycia dla naliczonych opłat

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. Wyszukaj pozycję *Zarządzanie kosztami i rozliczenia*.
3. Wybierz profil rozliczeniowy. W zależności od praw dostępu może być konieczne wybranie najpierw konta rozliczeniowego.
4. Wybierz pozycję **Faktury**.
5. W siatce faktur znajdź wiersz faktury odpowiadający danym użycia, które chcesz pobrać.
6. Kliknij symbol wielokropka (`...`) na końcu wiersza.

    ![Zrzut ekranu pokazujący wielokropek na końcu wiersza](./media/billing-download-azure-invoice/billingprofile-invoicegrid.png)

7. W menu kontekstowym pobierania wybierz pozycję **Użycie i opłaty platformy Azure**.

     ![Zrzut ekranu przedstawiający wybraną pozycję Użycie i opłaty platformy Azure](./media/billing-download-azure-usage/contextmenu-usage.png)

### <a name="download-usage-for-pending-charges"></a>Pobieranie danych użycia dla oczekujących opłat

Możesz także pobrać dane miesięcznego użycia dla bieżącego okresu rozliczeniowego. Dotyczą one opłat za użycie, które nie zostały jeszcze rozliczone.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. Wyszukaj pozycję *Zarządzanie kosztami i rozliczenia*.
3. Wybierz profil rozliczeniowy. W zależności od praw dostępu może być konieczne wybranie najpierw konta rozliczeniowego.
4. W obszarze **Przegląd** znajdź linki pobierania pod opłatami za bieżący miesiąc.
5. Wybierz pozycję **Użycie i opłaty platformy Azure**.

    ![Zrzut ekranu przedstawiający pobieranie z obszaru Przegląd](./media/billing-download-azure-usage/open-usage.png)

## <a name="check-your-access-to-a-microsoft-customer-agreement"></a>Sprawdzanie dostępu do umowy klienta firmy Microsoft
[!INCLUDE [billing-check-mca](../../includes/billing-check-mca.md)]

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
