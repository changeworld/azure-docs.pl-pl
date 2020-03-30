---
title: Zamykanie konta służbowego w niezarządzanym katalogu usługi Azure AD
description: Jak zamknąć konto służbowe lub szkolne w niezarządzanej usłudze Azure Active Directory.
services: active-directory
author: rolyon
manager: mtillman
ms.service: active-directory
ms.subservice: users-groups-roles
ms.topic: article
ms.workload: identity
ms.date: 05/20/2019
ms.author: rolyon
ms.reviewer: ''
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3c101c0ef7932151e675c5c514ac558e6e0f94b2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73815720"
---
# <a name="close-your-work-or-school-account-in-an-unmanaged-directory"></a>Zamykanie konta służbowego w katalogu niezarządzanym

Jeśli jesteś użytkownikiem w niezarządzanej organizacji usługi Azure Active Directory (Azure AD) i nie musisz już używać aplikacji z tej organizacji ani utrzymywać żadnego skojarzenia z nią, możesz zamknąć konto w dowolnym momencie. Katalog niezarządzany nie ma administratora globalnego. Użytkownicy w katalogu niezarządzanym mogą samodzielnie zamykać swoje konta bez konieczności kontaktowych z administratorem.

Użytkownicy w katalogu niezarządzanym są często tworzone podczas rejestracji samoobsługowej. Przykładem może być pracownik informacji w organizacji, który zarejestruje się w celu uzyskania bezpłatnej usługi. Aby uzyskać więcej informacji na temat samoobsługowego rejestrowania się, zobacz [Co to jest samoobsługowe rejestrowanie się w usłudze Azure Active Directory?](directory-self-service-signup.md).

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

## <a name="before-you-begin"></a>Przed rozpoczęciem

Zanim będzie można zamknąć konto, należy potwierdzić następujące elementy:

* Upewnij się, że jesteś użytkownikiem niezarządzanego katalogu usługi Azure AD. Nie możesz zamknąć konta, jeśli należysz do katalogu zarządzanego. Jeśli należysz do katalogu zarządzanego i chcesz zamknąć konto, skontaktuj się z administratorem. Aby uzyskać informacje dotyczące sposobu określania, czy należysz do katalogu niezarządzanego, zobacz [Usuwanie użytkownika z dzierżawy niezarządzanej](https://docs.microsoft.com/flow/gdpr-dsr-delete#delete-the-user-from-unmanaged-tenant).

* Zapisz wszystkie dane, które chcesz zachować. Aby uzyskać informacje dotyczące sposobu przesyłania żądania eksportu, zobacz Uzyskiwanie dostępu do [dzienników generowanych przez system i eksportowanie ich dla dzierżaw niezarządzanych](https://docs.microsoft.com/power-platform/admin/powerapps-gdpr-dsr-guide-systemlogs#accessing-and-exporting-system-generated-logs-for-unmanaged-tenants).

> [!WARNING]
> Zamknięcie konta jest nieodwracalne. Po zamknięciu konta wszystkie dane osobowe zostaną usunięte. Nie będziesz już mieć dostępu do swojego konta i danych powiązanych z Twoim kontem.

## <a name="close-your-account"></a>Zamykanie konta

Aby zamknąć niezarządzane konto służbowe lub szkolne, wykonaj następujące czynności:

1. Zaloguj się, aby [zamknąć konto](https://go.microsoft.com/fwlink/?linkid=873123), używając konta, które chcesz zamknąć.

1. W **obszarze Moje żądania danych**wybierz pozycję Zamknij **konto**.

    ![Moje żądania danych - Zamknij konto](./media/users-close-account/close-account.png)

1. Przejrzyj wiadomość z potwierdzeniem, a następnie wybierz pozycję **Tak**.

    ![Moje żądania danych - Potwierdź zamknięcie](./media/users-close-account/confirm-close.png)

## <a name="next-steps"></a>Następne kroki

- [Co to jest samoobsługowe rejestrowanie się w usłudze Azure Active Directory?](directory-self-service-signup.md)
- [Usuwanie użytkownika z dzierżawy niezarządzanej](https://docs.microsoft.com/flow/gdpr-dsr-delete#delete-the-user-from-unmanaged-tenant)
- [Uzyskiwanie dostępu do dzienników generowanych przez system i ich eksportowanie dla dzierżaw niezarządzanych](https://docs.microsoft.com/power-platform/admin/powerapps-gdpr-dsr-guide-systemlogs#accessing-and-exporting-system-generated-logs-for-unmanaged-tenants)
