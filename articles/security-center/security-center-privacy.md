---
title: Zarządzanie danymi użytkownika w usłudze Azure Security Center | Dokumentacja firmy Microsoft
description: " Dowiedz się, jak zarządzać danymi użytkownika w usłudze Azure Security Center. "
services: security-center
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: 411d7bae-c9d4-4e83-be63-9f2f2312b075
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/23/2018
ms.author: rkarlin
ms.openlocfilehash: fcec410df631a58b76878a4cb327ca2fb04a2105
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60703476"
---
# <a name="manage-user-data-in-azure-security-center"></a>Zarządzanie danymi użytkownika w usłudze Azure Security Center
Ten artykuł zawiera informacje dotyczące sposobu możesz zarządzać danymi użytkownika w usłudze Azure Security Center. Zarządzanie danymi użytkowników umożliwia dostęp, usunąć lub eksportowanie danych.

[!INCLUDE [gdpr-intro-sentence.md](../../includes/gdpr-intro-sentence.md)]

Użytkownika usługa Security Center przypisaną rolę Czytelnik, właściciela, współautora lub administratora konta mają dostęp do danych klienta w narzędziu. Zobacz [wbudowane role kontroli dostępu opartej na rolach na platformie Azure](../role-based-access-control/built-in-roles.md) Aby dowiedzieć się więcej o rolach czytnika, właściciel i współautor. Zobacz [Administratorzy subskrypcji platformy Azure](../billing/billing-add-change-azure-subscription-administrator.md) dowiedzieć się więcej o rolach administratora konta.

## <a name="searching-for-and-identifying-personal-data"></a>Wyszukiwanie i identyfikowanie danych osobowych
Użytkownik usługi Security Center może wyświetlić ich danych osobowych za pośrednictwem witryny Azure portal. Usługa Security Center są przechowywane szczegóły dotyczące kontaktu zabezpieczeń, takie jak adresy e-mail oraz numerów telefonów. Zobacz [podawanie szczegółów dotyczących kontaktu zabezpieczeń w usłudze Azure Security Center](security-center-provide-security-contact-details.md) Aby uzyskać więcej informacji.

W witrynie Azure portal użytkownik może wyświetlić dozwolone konfiguracje adresów IP przy użyciu Centrum zabezpieczeń dokładnie na czas maszyny Wirtualnej dostępu do funkcji. Aby uzyskać więcej informacji, zobacz [Manage virtual machine access using just in time](security-center-just-in-time.md) (Zarządzanie dostępem maszyny wirtualnej przy użyciu funkcji „dokładnie na czas”).

W witrynie Azure portal użytkownik może przeglądać alerty zabezpieczeń udostępniane przez usługę Security Center, w tym adresy IP i szczegóły osoby atakującej. Zobacz [reagowanie na alerty zabezpieczeń w Centrum zabezpieczeń Azure i zarządzanie nimi](security-center-managing-and-responding-alerts.md) Aby uzyskać więcej informacji.

## <a name="classifying-personal-data"></a>Klasyfikowanie danych osobowych
Nie trzeba klasyfikowanie danych osobowych w funkcji kontaktu zabezpieczeń Centrum zabezpieczeń. Dane zapisane jest adres e-mail (lub wiele adresów e-mail) i numer telefonu. [Dane kontaktowe](security-center-provide-security-contact-details.md) jest sprawdzana w usłudze Security Center.

Nie trzeba do klasyfikowania adresów IP i portu numery zapisane przez usługę Security Center [dokładnie na czas](security-center-just-in-time.md) funkcji.

Tylko użytkownik przypisany do roli administratora może klasyfikowanie danych osobowych przez [wyświetlanie alertów](security-center-managing-and-responding-alerts.md) w usłudze Security Center.

## <a name="securing-and-controlling-access-to-personal-data"></a>Zabezpieczanie i kontrolowanie dostępu do danych osobowych
Użytkownik usługi Security Center przypisaną rolę Czytelnik, właściciela, współautora lub konto administratora mogą uzyskiwać dostęp do [dane kontaktowe zabezpieczeń](security-center-provide-security-contact-details.md).

Użytkownik usługi Security Center przypisaną rolę Czytelnik, właściciela, współautora lub konto administratora mogą uzyskiwać dostęp do ich [dokładnie na czas](security-center-just-in-time.md) zasad.

Użytkownik usługi Security Center przypisaną rolę Czytelnik, właściciela, współautora lub administratora konta mogą wyświetlać ich [alerty](security-center-managing-and-responding-alerts.md).

## <a name="updating-personal-data"></a>Aktualizowanie danych osobowych
Użytkownik usługi Security Center przypisaną rolę właściciela, współautora, lub Administrator konta może aktualizować [dane kontaktowe zabezpieczeń](security-center-provide-security-contact-details.md) za pośrednictwem witryny Azure portal.

Użytkownik usługi Security Center przypisaną rolę właściciela, współautora, lub Administrator konta może aktualizować ich [dokładnie na czas zasady](security-center-just-in-time.md).

Administrator konta nie można edytować alertu zdarzenia. [Alertu zdarzenia](security-center-managing-and-responding-alerts.md) jest uznawany za bezpieczeństwo danych i jest tylko do odczytu.

## <a name="deleting-personal-data"></a>Usuwanie danych osobowych
Użytkownik usługi Security Center przypisaną rolę właściciela, współautora, lub Administrator konta może usunąć [dane kontaktowe zabezpieczeń](security-center-provide-security-contact-details.md) za pośrednictwem witryny Azure portal.

Użytkownik usługi Security Center przypisaną rolę właściciela, współautora, lub Administrator konta może usunąć [dokładnie na czas zasady](security-center-just-in-time.md) za pośrednictwem witryny Azure portal.

Użytkownik usługi Security Center nie można usunąć alertu zdarzenia. Ze względu na potrzeby w zakresie zabezpieczeń [alertu zdarzenia](security-center-managing-and-responding-alerts.md) jest uznawany za dane tylko odczytu.

## <a name="exporting-personal-data"></a>Eksportowanie danych osobowych
Użytkownik usługi Security Center przypisaną rolę Czytelnik, właściciela, współautora lub administratora konta można wyeksportować [dane kontaktowe zabezpieczeń](security-center-provide-security-contact-details.md) przez:

- Wykonywanie kopii w witrynie Azure portal
- Wykonywanie wywołania interfejsu API REST platformy Azure, Pobierz HTTP:
  ```HTTP
  GET https://<endpoint>/subscriptions/{subscriptionId}/providers/Microsoft.Security/securityContacts?api-version={api-version}
  ```

Użytkownik usługi Security Center z przypisaną rolą z Administrator konta może wyeksportować [dokładnie na czas zasady](security-center-just-in-time.md) zawierające adres IP adresów przez:

- Wykonywanie kopii w witrynie Azure portal
- Wykonywanie wywołania interfejsu API REST platformy Azure, Pobierz HTTP:
  ```HTTP
  GET https://<endpoint>/subscriptions/{subscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.Security/locations/{location}/jitNetworkAccessPolicies/default?api-version={api-version}
  ```

Administrator konta może wyeksportować szczegóły alertu przez:

- Wykonywanie kopii w witrynie Azure portal
- Wykonywanie wywołania interfejsu API REST platformy Azure, Pobierz HTTP:
  ```HTTP
  GET https://<endpoint>/subscriptions/{subscriptionId}/providers/microsoft.Security/alerts?api-version={api-version}
  ```

Zobacz [pobieranie alertów zabezpieczeń (pobieranie kolekcji)](https://msdn.microsoft.com/library/mt704050.aspx) Aby uzyskać więcej informacji.

## <a name="restricting-the-use-of-personal-data-for-profiling-or-marketing-without-consent"></a>Ograniczenie użytkowania danych osobowych dla profilowania i marketingowych bez ich zgody
Usługa Security Center można zrezygnować z nowego, usuwając ich [dane kontaktowe zabezpieczeń](security-center-provide-security-contact-details.md).

[Dokładnie na czas danych](security-center-just-in-time.md) jest uznawany za — do zidentyfikowania danych i są przechowywane przez okres 30 dni.

[Dane alertów](security-center-managing-and-responding-alerts.md) jest uznawany za danych zabezpieczeń i są przechowywane przez dwa lata.

## <a name="auditing-and-reporting"></a>Inspekcja i raportowanie
Dzienniki, skontaktuj się z zabezpieczeń, inspekcji dokładnie na czas i alertów, aktualizacje są obsługiwane w [dzienników aktywności platformy Azure](../azure-monitor/platform/activity-logs-overview.md).

## <a name="next-steps"></a>Kolejne kroki
Aby uzyskać więcej informacji na temat zarządzających danymi użytkowników zobacz [zarządzanie danymi użytkownika w Centrum zabezpieczeń Azure badanie](security-center-investigation-user-data.md).
