---
title: Zarządzanie danymi użytkownika w usłudze Azure Security Center | Dokumenty firmy Microsoft
description: Dowiedz się, jak zarządzać danymi użytkowników w usłudze Azure Security Center. Zarządzanie danymi użytkownika obejmuje możliwość uzyskiwania dostępu do danych, ich usuwania lub eksportowania.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 411d7bae-c9d4-4e83-be63-9f2f2312b075
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/23/2018
ms.author: memildin
ms.openlocfilehash: 6edea1d0de53e2dc9f764de26209dc1f3110556e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75978614"
---
# <a name="manage-user-data-in-azure-security-center"></a>Zarządzanie danymi użytkownika w usłudze Azure Security Center
Ten artykuł zawiera informacje dotyczące sposobu zarządzania danymi użytkownika w usłudze Azure Security Center. Zarządzanie danymi użytkownika obejmuje możliwość uzyskiwania dostępu do danych, ich usuwania lub eksportowania.

[!INCLUDE [gdpr-intro-sentence.md](../../includes/gdpr-intro-sentence.md)]

Użytkownik Centrum zabezpieczeń przypisany do roli administratora programu Reader, Owner, Contributor lub Account Administrator może uzyskać dostęp do danych klienta w narzędziu. Aby dowiedzieć się więcej o roli Administratora konta, zobacz [Wbudowane role dla kontroli dostępu opartej na rolach platformy Azure,](../role-based-access-control/built-in-roles.md) aby dowiedzieć się więcej o rolach czytnika, właściciela i współautora. Zobacz [Administratorzy subskrypcji platformy Azure](../cost-management-billing/manage/add-change-subscription-administrator.md).

## <a name="searching-for-and-identifying-personal-data"></a>Wyszukiwanie i identyfikowanie danych osobowych
Użytkownik usługi Security Center może wyświetlać swoje dane osobowe za pośrednictwem witryny Azure portal. Usługa Security Center przechowuje tylko dane kontaktowe zabezpieczeń, takie jak adresy e-mail i numery telefonów. Aby uzyskać więcej informacji, zobacz [Podaj szczegóły kontaktu zabezpieczeń w usłudze Azure Security Center](security-center-provide-security-contact-details.md).

W witrynie Azure portal użytkownik może wyświetlać dozwolone konfiguracje adresów IP przy użyciu funkcji dostępu do maszyn wirtualnych just-in-time usługi Security Center. Aby uzyskać więcej informacji, zobacz [Zarządzanie dostępem do maszyny wirtualnej przy użyciu funkcji just-in-time](security-center-just-in-time.md).

W witrynie Azure portal użytkownik może wyświetlać alerty zabezpieczeń dostarczane przez usługę Security Center, w tym adresy IP i szczegóły osoby atakującej. Aby uzyskać więcej informacji, zobacz [Zarządzanie alertami zabezpieczeń i odpowiadanie na nie w usłudze Azure Security Center](security-center-managing-and-responding-alerts.md).

## <a name="classifying-personal-data"></a>Klasyfikacja danych osobowych
Nie trzeba klasyfikować danych osobowych znalezionych w funkcji kontaktu zabezpieczeń usługi Security Center. Zapisane dane to adres e-mail (lub wiele adresów e-mail) oraz numer telefonu. [Dane kontaktowe](security-center-provide-security-contact-details.md) są weryfikowane przez centrum zabezpieczeń.

Nie trzeba klasyfikować adresów IP i numerów portów zapisanych przez funkcję [Just-in-Time](security-center-just-in-time.md) usługi Security Center.

Tylko użytkownik przypisany do roli administratora może klasyfikować dane osobowe, [wyświetlając alerty](security-center-managing-and-responding-alerts.md) w Centrum zabezpieczeń.

## <a name="securing-and-controlling-access-to-personal-data"></a>Zabezpieczenie i kontrolowanie dostępu do danych osobowych
Użytkownik Centrum zabezpieczeń przypisany do roli czytnika, właściciela, współautora lub administratora konta może uzyskać dostęp do [danych kontaktowych zabezpieczeń](security-center-provide-security-contact-details.md).

Użytkownik Centrum zabezpieczeń przypisany do roli administratora programu Reader, Owner, Contributor lub Account Administrator może uzyskać dostęp do swoich zasad [just-in-time.](security-center-just-in-time.md)

Użytkownik Centrum zabezpieczeń przypisany do roli administratora programu Reader, Owner, Contributor lub Account Administrator może wyświetlać swoje [alerty](security-center-managing-and-responding-alerts.md).

## <a name="updating-personal-data"></a>Aktualizowanie danych osobowych
Użytkownik Usługi Security Center przypisany roli właściciela, współautora lub administratora konta może aktualizować [dane kontaktowe zabezpieczeń](security-center-provide-security-contact-details.md) za pośrednictwem witryny Azure portal.

Użytkownik Usługi Security Center, której przypisano rolę właściciela, współautora lub administratora konta, może aktualizować swoje [zasady just-in-time](security-center-just-in-time.md).

Administrator konta nie może edytować zdarzeń alertów. [Zdarzenie alertu](security-center-managing-and-responding-alerts.md) jest uważane za dane zabezpieczeń i jest tylko do odczytu.

## <a name="deleting-personal-data"></a>Usuwanie danych osobowych
Użytkownik Usługi Security Center przypisany do roli właściciela, współautora lub administratora konta może usuwać [dane kontaktowe zabezpieczeń](security-center-provide-security-contact-details.md) za pośrednictwem witryny Azure portal.

Użytkownik Usługi Security Center przypisany do roli właściciela, współautora lub administratora konta może usunąć [zasady just-in-time](security-center-just-in-time.md) za pośrednictwem witryny Azure portal.

Użytkownik Centrum zabezpieczeń nie może usuwać zdarzeń alertów. Ze względów bezpieczeństwa [zdarzenie alertu](security-center-managing-and-responding-alerts.md) jest uważane za dane tylko do odczytu.

## <a name="exporting-personal-data"></a>Eksportowanie danych osobowych
Użytkownik Centrum zabezpieczeń przypisany do roli administratora programu Reader, Owner, Contributor lub Account Administrator może eksportować [dane kontaktowe zabezpieczeń](security-center-provide-security-contact-details.md) przez:

- Kopiowanie z witryny Azure portal
- Wykonywanie wywołania interfejsu API rest platformy Azure, POBIERZ HTTP:
  ```HTTP
  GET https://<endpoint>/subscriptions/{subscriptionId}/providers/Microsoft.Security/securityContacts?api-version={api-version}
  ```

Użytkownik Usługi Security Center, której przypisano rolę administratora konta, może wyeksportować [zasady just-in-time](security-center-just-in-time.md) zawierające adresy IP przez:

- Kopiowanie z witryny Azure portal
- Wykonywanie wywołania interfejsu API rest platformy Azure, POBIERZ HTTP:
  ```HTTP
  GET https://<endpoint>/subscriptions/{subscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.Security/locations/{location}/jitNetworkAccessPolicies/default?api-version={api-version}
  ```

Administrator konta może wyeksportować szczegóły alertu, korzystając z:

- Kopiowanie z witryny Azure portal
- Wykonywanie wywołania interfejsu API rest platformy Azure, POBIERZ HTTP:
  ```HTTP
  GET https://<endpoint>/subscriptions/{subscriptionId}/providers/microsoft.Security/alerts?api-version={api-version}
  ```

Aby uzyskać więcej informacji, zobacz [Pobieranie alertów zabezpieczeń (kolekcja GET)](https://msdn.microsoft.com/library/mt704050.aspx).

## <a name="restricting-the-use-of-personal-data-for-profiling-or-marketing-without-consent"></a>Ograniczenie wykorzystywania danych osobowych do profilowania lub marketingu bez zgody
Użytkownik Usługi Security Center może zrezygnować, usuwając swoje [dane kontaktowe zabezpieczeń](security-center-provide-security-contact-details.md).

[Dane just-in-time](security-center-just-in-time.md) są uważane za dane nieidentyfikowalne i są przechowywane przez okres 30 dni.

[Dane alertów](security-center-managing-and-responding-alerts.md) są uważane za dane zabezpieczające i są przechowywane przez okres dwóch lat.

## <a name="auditing-and-reporting"></a>Inspekcja i sprawozdawczość
Dzienniki inspekcji kontaktu zabezpieczeń, just-in-time i aktualizacje alertów są przechowywane w [dziennikach aktywności platformy Azure.](../azure-monitor/platform/platform-logs-overview.md)

## <a name="next-steps"></a>Następne kroki
Aby uzyskać więcej informacji na temat zarządzania danymi użytkowników, zobacz [Zarządzanie danymi użytkowników znalezionymi w badaniu Usługi Azure Security Center](security-center-investigation-user-data.md).
