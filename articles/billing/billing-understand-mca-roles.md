---
title: Omówienie rozliczeń role administracyjne dla umowy klienta firmy Microsoft — Azure
description: Więcej informacji na temat rozliczeń ról na potrzeby rozliczeń kont na platformie Azure do umowy klienta firmy Microsoft.
services: billing
documentationcenter: ''
author: amberbhargava
manager: amberbhargava
editor: ''
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/28/2019
ms.author: banders
ms.openlocfilehash: 780870cc71e95507a52ba6a9338026f895a96ac1
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60370971"
---
# <a name="understand-microsoft-customer-agreement-administrative-roles-in-azure"></a>Zrozumienie umowy klienta Microsoft ról administracyjnych na platformie Azure

Aby zarządzać konta rozliczeniowego dla umowy klienta firmy Microsoft, należy użyć ról opisane w poniższych sekcjach. Te role są oprócz wbudowanych ról, platforma Azure ma kontrolować dostęp do zasobów. Aby uzyskać więcej informacji, zobacz [wbudowane role zasobów platformy Azure](../role-based-access-control/built-in-roles.md).

Ten artykuł dotyczy konta rozliczeniowego dla umowy klienta firmy Microsoft. Sprawdź, czy dostęp do umowy klienta firmy Microsoft.

## <a name="billing-role-definitions"></a>Definicje ról rozliczeń

W poniższej tabeli opisano role rozliczeń służy do zarządzania konta rozliczeniowego, rozliczeń profile i faktury sekcje.

|Rola|Opis|
|---|---|
|Właścicielem konta i rozliczenia|Zarządzanie wszystkim, aby uzyskać konto rozliczeniowe|
|Współautor konta rozliczeń|Zarządzanie wszystkim, z wyjątkiem uprawnienia do konta rozliczeniowego|
|Rozliczanie konta czytnika|Widok tylko do odczytu wszystkich elementów na konto rozliczeniowe|
|Właściciela profilu rozliczeń|Wszystko pod kątem rozliczeń profilu zarządzania|
|Współautor profilu rozliczenia|Zarządzanie wszystkim, z wyjątkiem uprawnienia profil rozliczeniowy|
|Czytelnik profilu rozliczeń|Tylko do odczytu widoku wszystko na rozliczeń profilu|
|Menedżer faktur|Wyświetlanie i płać faktur rozliczeń profilu|
|Właściciel sekcji faktury|Zarządzanie wszystkim, w sekcji faktury|
|Współautor sekcji faktury|Zarządzanie wszystkim, z wyjątkiem uprawnienia do sekcji faktury|
|Czytnik sekcji faktury|Widok tylko do odczytu wszystkich elementów w sekcji faktury|
|Twórca subskrypcji platformy Azure|Tworzenie subskrypcji platformy Azure|

## <a name="billing-account-roles-and-tasks"></a>Rozliczanie konta role i zadania

Konto rozliczeniowe umożliwia zarządzają rozliczeniami dla Twojej organizacji. Konto rozliczeniowe umożliwia organizowanie kosztów, opłaty za monitorowanie i faktur i kontroli dostępu rozliczeń dla Twojej organizacji. Aby uzyskać więcej informacji, zobacz [zrozumieć konto rozliczeniowe](billing-mca-overview.md#understand-billing-account).

W poniższych tabelach przedstawiono jaką rolę konieczne wykonywanie zadań w kontekście konta rozliczeniowego.

### <a name="manage-billing-account-permissions-and-properties"></a>Zarządzanie uprawnieniami konta rozliczeniowego i właściwości

|Zadanie|Właścicielem konta i rozliczenia|Współautor konta rozliczeń|Rozliczanie konta czytnika|
|---|---|---|---|
|Umożliwia wyświetlanie istniejących uprawnień konto rozliczeniowe|✔|✔|✔|
|Zezwól na uprawnienia do wyświetlania i zarządzania konto rozliczeniowe|✔|✘|✘|
|Właściwości konta rozliczeniowego widoku, takie jak nazwa firmy, adres i więcej|✔|✔|✔|

### <a name="manage-billing-profiles-for-billing-account"></a>Zarządzanie profilami rozliczeń dla konto rozliczeniowe

|Zadanie|Właścicielem konta i rozliczenia|Współautor konta rozliczeń|Rozliczanie konta czytnika|
|---|---|---|---|
|Wyświetl wszystkie profile rozliczeń na koncie|✔|✔|✔|

### <a name="manage-invoices-for-billing-account"></a>Zarządzanie faktur na konto rozliczeniowe

|Zadanie|Właścicielem konta i rozliczenia|Współautor konta rozliczeń|Rozliczanie konta czytnika|
|---|---|---|---|
|Wyświetl wszystkie faktury w ramach konta|✔|✔|✔|
|Pobieranie faktur, usługa Azure files użycia i opłat, arkuszy cen i podatkowych dokumenty na koncie|✔|✔|✔|

### <a name="manage-invoice-sections-for-billing-account"></a>Zarządzanie faktury sekcje, aby uzyskać konto rozliczeniowe

|Zadanie|Właścicielem konta i rozliczenia|Współautor konta rozliczeń|Rozliczanie konta czytnika|
|---|---|---|---|
|Wyświetl wszystkie sekcje faktury w ramach konta|✔|✔|✔|

### <a name="manage-transactions-for-billing-account"></a>Zarządzanie transakcjami na konto rozliczeniowe

|Zadanie|Właścicielem konta i rozliczenia|Współautor konta rozliczeń|Rozliczanie konta czytnika|
|---|---|---|---|
|Wyświetl wszystkie transakcje rozliczeń dla konta|✔|✔|✔|
|Wyświetlanie wszystkich produktów zakupionych dla konta|✔|✔|✔|

### <a name="manage-subscriptions-for-billing-account"></a>Zarządzaj subskrypcjami dla konto rozliczeniowe

|Zadanie|Właścicielem konta i rozliczenia|Współautor konta rozliczeń|Rozliczanie konta czytnika|
|---|---|---|---|
|Wyświetl wszystkie subskrypcje systemu Azure w ramach konta rozliczeniowego|✔|✔|✔|

## <a name="billing-profile-roles-and-tasks"></a>Karta profilu role i zadania

Profil rozliczeniowy pozwala zarządzać metodami płatności i faktur. Miesięcznej fakturze jest generowany dla subskrypcji platformy Azure i innych produktów zakupionych przy użyciu profilu rozliczeń. Możesz użyć metody płatności fakturę. Aby uzyskać więcej informacji, zobacz [zrozumieć profile rozliczeń](billing-mca-overview.md#understand-billing-profiles).

W poniższych tabelach przedstawiono jaką rolę konieczne wykonywanie zadań w kontekście profil rozliczeniowy.

### <a name="manage-billing-profile-permissions-properties-and-policies"></a>Zarządzanie rozliczeń uprawnienia profilu, właściwości i zasady

|Zadanie|Właściciela profilu rozliczeń|Współautor profilu rozliczenia|Czytelnik profilu rozliczeń|Menedżer faktur|Właścicielem konta i rozliczenia|Współautor konta rozliczeń|Rozliczanie konta czytnika
|---|---|---|---|---|---|---|---|
|Umożliwia wyświetlanie istniejących uprawnień profil rozliczeniowy|✔|✔|✔|✔|✔|✔|✔|
|Zezwól na uprawnienia do wyświetlania i zarządzania profil rozliczeniowy|✔|✘|✘|✘|✘|✘|✘|
|Wyświetl rozliczeń właściwości profilu, takich jak zamówienia zakupu numer, preferencji faktury poczty e-mail i innych|✔|✔|✔|✔|✔|✔|✔|
|Aktualizowanie właściwości profilu rozliczeń |✔|✔|✘|✘|✘|✘|✘|
|Wyświetl zasady stosowane na profil rozliczeniowy, takich jak włączyć zakupu rezerwacji platformy Azure, Włącz zakupy w portalu Azure marketplace i nie tylko|✔|✔|✔|✔|✔|✔|✔|
|Stosowanie zasad na profil rozliczeniowy |✔|✔|✘|✘|✘|✘|✘|

### <a name="manage-invoices-for-billing-profile"></a>Zarządzaj fakturami do rozliczeń, profilu

|Zadanie|Właściciela profilu rozliczeń|Współautor profilu rozliczenia|Czytelnik profilu rozliczeń|Menedżer faktur|Właścicielem konta i rozliczenia|Współautor konta rozliczeń|Rozliczanie konta czytnika
|---|---|---|---|---|---|---|---|
|Wyświetl wszystkie faktury dla profil rozliczeniowy|✔|✔|✔|✔|✔|✔|✔|
|Pobieranie faktur, usługa Azure files użycia i opłat, arkuszy cen i podatkowych dokumenty, aby profil rozliczeniowy|✔|✔|✔|✔|✔|✔|✔|

### <a name="manage-invoice-sections-for-billing-profile"></a>Zarządzanie sekcje faktury dla profil rozliczeniowy

|Zadanie|Właściciela profilu rozliczeń|Współautor profilu rozliczenia|Czytelnik profilu rozliczeń|Menedżer faktur|Właścicielem konta i rozliczenia|Współautor konta rozliczeń|Rozliczanie konta czytnika
|---|---|---|---|---|---|---|---|
|Wyświetl wszystkie sekcje faktury profil rozliczeniowy|✔|✔|✔|✔|✔|✔|✔|
|Utwórz nową sekcję faktury dla profil rozliczeniowy|✔|✔|✘|✘|✘|✘|✘|

### <a name="manage-transactions-for-billing-profile"></a>Zarządzanie transakcjami do rozliczeń, profilu

|Zadanie|Właściciela profilu rozliczeń|Współautor profilu rozliczenia|Czytelnik profilu rozliczeń|Menedżer faktur|Właścicielem konta i rozliczenia|Współautor konta rozliczeń|Rozliczanie konta czytnika
|---|---|---|---|---|---|---|---|
|Wyświetl wszystkie transakcje rozliczeń dla profil rozliczeniowy|✔|✔|✔|✔|✔|✔|✔|

### <a name="manage-payment-methods-for-billing-profile"></a>Zarządzaj metodami płatności na potrzeby rozliczeń profilu

|Zadanie|Właściciela profilu rozliczeń|Współautor profilu rozliczenia|Czytelnik profilu rozliczeń|Menedżer faktur|Właścicielem konta i rozliczenia|Współautor konta rozliczeń|Rozliczanie konta czytnika
|---|---|---|---|---|---|---|---|
|Wyświetl formy płatności dla profil rozliczeniowy|✔|✔|✔|✔|✔|✔|✔|
|Saldo środków platformy Azure dla profil rozliczeniowy śledzić|✔|✔|✔|✔|✔|✔|✔|

### <a name="manage-subscriptions-for-billing-profile"></a>Zarządzaj subskrypcjami dla rozliczeń profilu

|Zadanie|Właściciela profilu rozliczeń|Współautor profilu rozliczenia|Czytelnik profilu rozliczeń|Menedżer faktur|Właścicielem konta i rozliczenia|Współautor konta rozliczeń|Rozliczanie konta czytnika
|---|---|---|---|---|---|---|---|
|Wyświetl wszystkie subskrypcje systemu Azure dla profil rozliczeniowy|✔|✔|✔|✔|✔|✔|✔|

## <a name="invoice-section-roles-and-tasks"></a>Faktury sekcji role i zadania

Sekcja faktury umożliwia organizowanie kosztów na fakturze. Można utworzyć sekcję do organizowania koszty według działu, środowisko programistyczne, lub odpowiednio do potrzeb Twojej organizacji. Zezwól na uprawnienia do tworzenia subskrypcji platformy Azure, w sekcji. Opłaty za użycie i zakupów dla subskrypcji, a następnie show w sekcji faktury. Aby uzyskać więcej informacji, zobacz [sekcji faktury omówienie](billing-mca-overview.md#understand-invoice-sections).

W poniższych tabelach przedstawiono jaką rolę konieczne wykonywanie zadań w kontekście sekcje faktury.

### <a name="manage-invoice-section-permissions-and-properties"></a>Zarządzanie uprawnieniami sekcji faktury i właściwości

|Zadania|Właściciel sekcji faktury|Współautor sekcji faktury|Czytnik sekcji faktury|Twórca subskrypcji platformy Azure|Właścicielem konta i rozliczenia|Współautor konta rozliczeń|Rozliczanie konta czytnika | |
|---|---|---|---|---|---|---|---|---|
|Wyświetl wszystkie uprawnienia w sekcji faktury|✔|✔|✔|✔|✔|✔|✔| |
|Zezwól na uprawnienia do wyświetlania i zarządzania nimi w sekcji faktury|✔|✘|✘|✘|✘|✘|✘| |
|Wyświetlanie faktur sekcji właściwości|✔|✔|✔|✔|✔|✔|✔| |
|Aktualizowanie właściwości sekcji faktury|✔|✔|✘|✘|✘|✘|✘|✘|

### <a name="manage-products-for-invoice-section"></a>Zarządzanie produktami dla sekcji faktury

|Zadania|Właściciel sekcji faktury|Współautor sekcji faktury|Czytnik sekcji faktury|Twórca subskrypcji platformy Azure|Właścicielem konta i rozliczenia|Współautor konta rozliczeń|Rozliczanie konta czytnika
|---|---|---|---|---|---|---|---|
|Wyświetlanie wszystkich produktów zakupionych w sekcji faktury|✔|✔|✔|✘|✔|✔|✔|
|Zarządzają rozliczeniami dla produktów dla sekcji faktury, takich jak anulowanie, wyłączać funkcję automatycznego odnawiania i nie tylko|✔|✔|✘|✘|✘|✘|✘|
|Zmiana faktury dotyczącej produktów|✔|✔|✘|✘|✘|✘|✘|

### <a name="manage-subscriptions-for-invoice-section"></a>Zarządzaj subskrypcjami dla sekcji faktury

|Zadania|Właściciel sekcji faktury|Współautor sekcji faktury|Czytnik sekcji faktury|Twórca subskrypcji platformy Azure|Właścicielem konta i rozliczenia|Współautor konta rozliczeń|Rozliczanie konta czytnika
|---|---|---|---|---|---|---|---|
|Wyświetl wszystkie subskrypcje systemu Azure dla sekcji faktury|✔|✔|✔|✘|✔|✔|✔|
|Zmień sekcję faktury dla subskrypcji|✔|✔|✘|✘|✘|✘|✘|
|Własność rozliczeń subskrypcji należy poprosić użytkowników w innych kont rozliczeń|✔|✔|✘|✘|✘|✘|✘|

## <a name="subscription-billing-roles-and-tasks"></a>Role rozliczeń subskrypcji i zadania

W poniższej tabeli przedstawiono, jakie rola konieczne wykonywanie zadań w ramach subskrypcji.

|Zadania|Właściciel sekcji faktury|Współautor sekcji faktury|Czytnik sekcji faktury|Twórca subskrypcji platformy Azure|
|---|---|---|---|---|
|Tworzenie subskrypcji platformy Azure|✔|✔|✘|✔|
|Aktualizowanie Centrum kosztów dla subskrypcji|✔|✔|✘|✘|
|Zmień sekcję faktury dla subskrypcji|✔|✔|✘|✘|

## <a name="manage-billing-roles-in-the-azure-portal"></a>Zarządzanie rolami rozliczeń w witrynie Azure portal

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).

2. Wyszukiwanie **Cost Management + rozliczenia**.

   ![Zrzut ekranu pokazujący usługi Azure search w portalu](./media/billing-understand-mca-roles/billing-search-cost-management-billing.png)

3. Wybierz **kontrola dostępu (IAM)** w zakresie, takich jak konta rozliczeniowego, profil rozliczeniowy lub sekcji faktury, w którym chcesz udzielić dostępu.

4. Strony kontrola dostępu (IAM) zawiera listę użytkowników i grup, które są przypisane do każdej roli dla tego zakresu.

   ![Zrzut ekranu przedstawiający listę administratorów i przeznaczone do konta rozliczeniowego](./media/billing-understand-mca-roles/billing-list-admins.png)

5. Aby udzielić dostępu do użytkownika, wybierz **Dodaj** w górnej części strony. Na liście rozwijanej roli wybierz rolę. Wprowadź adres e-mail użytkownika, któremu chcesz udzielić dostępu. Wybierz **Zapisz** Aby przypisać rolę.

   ![Zrzut ekranu przedstawiający dodawanie administratora do konta rozliczeniowego](./media/billing-understand-mca-roles/billing-add-admin.png)

6. Aby usunąć dostęp użytkownika, wybierz użytkownika z przypisania roli, którą chcesz usunąć. Wybierz pozycję Usuń.

   ![Zrzut ekranu przedstawiający usuwanie przez administratora konta rozliczeniowego](./media/billing-understand-mca-roles/billing-remove-admin.png)

## <a name="check-access-to-a-microsoft-customer-agreement"></a>Sprawdź dostęp do umowy klienta firmy Microsoft
[!INCLUDE [billing-check-mca](../../includes/billing-check-mca.md)]

## <a name="need-help-contact-support"></a>Potrzebujesz pomocy? Skontaktuj się z pomocą techniczną
Jeśli potrzebujesz pomocy, [się z pomocą techniczną](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) można szybko rozwiązać swój problem.

## <a name="next-steps"></a>Kolejne kroki

Zobacz następujące artykuły, aby dowiedzieć się więcej na temat konta rozliczeniowego:

- [Rozpocznij pracę z konta rozliczeniowego dla umowy klienta firmy Microsoft](billing-mca-overview.md)
- [Tworzenie subskrypcji platformy Azure dla swojego konta rozliczeniowego dla umowy klienta firmy Microsoft](billing-mca-create-subscription.md)
