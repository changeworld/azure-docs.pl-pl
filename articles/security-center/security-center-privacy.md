---
title: Zarządzanie danymi użytkownika w Centrum zabezpieczeń Azure | Dokumentacja firmy Microsoft
description: " Dowiedz się, jak zarządzać danymi użytkownika w Centrum zabezpieczeń Azure. "
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: ''
ms.assetid: 411d7bae-c9d4-4e83-be63-9f2f2312b075
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/23/2018
ms.author: terrylan
ms.openlocfilehash: 2495bae5c63cdafe049ec39f71ab53106c5f2df7
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34655725"
---
# <a name="manage-user-data-in-azure-security-center"></a>Zarządzanie danymi użytkownika w Centrum zabezpieczeń Azure
Ten artykuł zawiera informacje dotyczące sposobu zarządzania danych użytkownika w Centrum zabezpieczeń Azure. Zarządzanie danymi użytkownika umożliwia dostęp, usunąć lub eksportowania danych.

[!INCLUDE [gdpr-intro-sentence.md](../../includes/gdpr-intro-sentence.md)]

Centrum zabezpieczeń użytkownika przypisano rolę czytelnika, właściciela, współautora lub Administrator konta ma dostęp do danych klienta w ramach narzędzia. Zobacz [wbudowanych ról dla kontroli dostępu opartej na rolach na platformie Azure](../role-based-access-control/built-in-roles.md) Aby dowiedzieć się więcej o rolach czytnik, właściciela i współautor. Zobacz [Administratorzy subskrypcji platformy Azure](../billing/billing-add-change-azure-subscription-administrator.md) Aby dowiedzieć się więcej o roli administratora konta.

## <a name="searching-for-and-identifying-personal-data"></a>Wyszukiwanie i zidentyfikowaniu danych osobowych
Centrum zabezpieczeń użytkownika można wyświetlić jego danych osobowych za pośrednictwem portalu Azure. Centrum zabezpieczeń przechowuje dane tylko szczegóły dotyczące kontaktu zabezpieczeń, takie jak adresy e-mail i numerów telefonów. Zobacz [podać szczegóły dotyczące kontaktu zabezpieczeń w Centrum zabezpieczeń Azure](security-center-provide-security-contact-details.md) Aby uzyskać więcej informacji.

W portalu Azure użytkownik może przeglądać dozwolonych konfiguracje adresów IP przy użyciu Centrum zabezpieczeń tylko w funkcji czasu maszyny Wirtualnej. Aby uzyskać więcej informacji, zobacz [Manage virtual machine access using just in time](security-center-just-in-time.md) (Zarządzanie dostępem maszyny wirtualnej przy użyciu funkcji „dokładnie na czas”).

W portalu Azure użytkownik może przeglądać świadczona przez Centrum zabezpieczeń, łącznie z adresami IP i szczegóły atakująca alertów zabezpieczeń. Zobacz [reagowanie na alerty zabezpieczeń w Centrum zabezpieczeń Azure i zarządzanie nimi](security-center-managing-and-responding-alerts.md) Aby uzyskać więcej informacji.

## <a name="classifying-personal-data"></a>Klasyfikacja danych osobowych
Nie należy do klasyfikowania danych osobowych w funkcji kontaktu zabezpieczeń Centrum zabezpieczeń. Dane zapisane jest adres e-mail (lub wiele adresów e-mail), a numer telefonu. [Skontaktuj się z danych](security-center-provide-security-contact-details.md) jest zweryfikowany przez Centrum zabezpieczeń.

Nie należy do klasyfikowania adresów IP i zapisany przez Centrum zabezpieczeń numery portów [tylko w czasie](security-center-just-in-time.md) funkcji.

Tylko użytkownik przypisaną rolę administratora można klasyfikować danych osobowych przez [wyświetlanie alertów](security-center-managing-and-responding-alerts.md) w Centrum zabezpieczeń.

## <a name="securing-and-controlling-access-to-personal-data"></a>Zabezpieczanie i kontrolowanie dostępu do danych osobowych
Centrum zabezpieczeń użytkownika przypisano rolę czytelnika, właściciela, współautora lub konto administratora mogą uzyskiwać dostęp do [dane kontaktowe zabezpieczeń](security-center-provide-security-contact-details.md).

Centrum zabezpieczeń użytkownika przypisano rolę czytelnika, właściciela, współautora lub konto administratora mogą uzyskiwać dostęp do swoich [tylko w czasie](security-center-just-in-time.md) zasad.

Centrum zabezpieczeń użytkownika przypisano rolę czytelnika, właściciela, współautora lub Administrator konta może wyświetlać ich [alerty](security-center-managing-and-responding-alerts.md).

## <a name="updating-personal-data"></a>Aktualizowanie danych osobowych
Centrum zabezpieczeń użytkownika przypisaną rolę właściciela, współautora, lub Administrator konta może je aktualizować [dane kontaktowe zabezpieczeń](security-center-provide-security-contact-details.md) za pośrednictwem portalu Azure.

Centrum zabezpieczeń użytkownika przypisaną rolę właściciela, współautora, lub Administrator konta może je aktualizować ich [tylko w czasie zasady](security-center-just-in-time.md).

Administrator konta nie można edytować zdarzeń alertów. [Alertu zdarzenia](security-center-managing-and-responding-alerts.md) jest uznawany za zabezpieczeń danych i jest tylko do odczytu.

## <a name="deleting-personal-data"></a>Usuwanie danych osobowych
Centrum zabezpieczeń użytkownika przypisaną rolę właściciela, współautora, lub usunąć konto administratora [dane kontaktowe zabezpieczeń](security-center-provide-security-contact-details.md) za pośrednictwem portalu Azure.

Centrum zabezpieczeń użytkownika przypisaną rolę właściciela, współautora, lub usunąć konto administratora [tylko w czasie zasady](security-center-just-in-time.md) za pośrednictwem portalu Azure.

Użytkownik Centrum zabezpieczeń nie można usunąć zdarzenia alertu. Ze względu na potrzeby w zakresie zabezpieczeń [alertu zdarzenia](security-center-managing-and-responding-alerts.md) jest uznawany za odczytu tylko dane.

## <a name="exporting-personal-data"></a>Eksportowanie danych osobowych
Centrum zabezpieczeń użytkownika przypisano rolę czytelnika, właściciela, współautora lub administratora konta można wyeksportować [dane kontaktowe zabezpieczeń](security-center-provide-security-contact-details.md) przez:

- Wykonywania kopii w portalu Azure
- Wykonywanie wywołań interfejsu API REST Azure, GET HTTP:
```HTTP
GET https://<endpoint>/subscriptions/{subscriptionId}/providers/Microsoft.Security/securityContacts?api-version={api-version}
```

Centrum zabezpieczeń użytkownik przypisany do roli programu Administrator konta może eksportować [tylko w czasie zasady](security-center-just-in-time.md) adresów IP zawierający przez:

- Wykonywania kopii w portalu Azure
- Wykonywanie wywołań interfejsu API REST Azure, GET HTTP:
```HTTP
GET https://<endpoint>/subscriptions/{subscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.Security/locations/{location}/jitNetworkAccessPolicies/default?api-version={api-version}
```

Administrator konta może wyeksportować szczegóły alertu przez:

- Wykonywania kopii w portalu Azure
- Wykonywanie wywołań interfejsu API REST Azure, GET HTTP:
```HTTP
GET https://<endpoint>/subscriptions/{subscriptionId}/providers/microsoft.Security/alerts?api-version={api-version}
```

Zobacz [uzyskiwanie alertów zabezpieczeń (kolekcja GET)](https://msdn.microsoft.com/library/mt704050.aspx) Aby uzyskać więcej informacji.

## <a name="restricting-the-use-of-personal-data-for-profiling-or-marketing-without-consent"></a>Ograniczanie użycia danych osobowych dla profilowania lub marketingu bez zgody
Centrum zabezpieczeń użytkownik może zrezygnować przez usunięcie ich [dane kontaktowe zabezpieczeń](security-center-provide-security-contact-details.md).

[Tylko w czasie danych](security-center-just-in-time.md) jest uznawany za danych z systemem innym niż umożliwiających identyfikację użytkownika i jest zachowywane przez okres do 30 dni.

[Dane alertów](security-center-managing-and-responding-alerts.md) jest uznawany za zabezpieczeń danych i są przechowywane w danym okresie dwa lata.

## <a name="auditing-and-reporting"></a>Inspekcja i raportowanie
Dzienniki zabezpieczeń kontaktu, inspekcji tylko w czasie i alertów, aktualizacji są przechowywane w [Dzienniki aktywności Azure](../monitoring-and-diagnostics/monitoring-overview-activity-logs.md).

## <a name="next-steps"></a>Kolejne kroki
Aby uzyskać więcej informacji na temat zarządzania danymi użytkownika, zobacz [zarządzać danymi użytkownika w Centrum zabezpieczeń Azure dochodzenia](security-center-investigation-user-data.md).
