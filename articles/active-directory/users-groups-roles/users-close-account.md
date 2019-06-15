---
title: Zamknij konto służbowe lub szkolne w katalogu niezarządzanego — usługi Azure Active Directory | Dokumentacja firmy Microsoft
description: Jak zamknąć konto służbowe lub szkolne w niezarządzanych usługi Azure Active Directory.
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
ms.openlocfilehash: 39b359ef7feeaec541ba17e98a5d1e9b74c6403a
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "65958003"
---
# <a name="close-your-work-or-school-account-in-an-unmanaged-directory"></a>Zamknij konto służbowe lub szkolne w katalogu niezarządzanego

Jeśli jesteś użytkownikiem w niezarządzanych organizacji usługi Azure Active Directory (Azure AD), a nie jest już konieczne korzystanie z aplikacji z tej organizacji lub obsługi wszystkie skojarzenia z, w dowolnym momencie można zamknąć konto. Niezarządzanego katalogu nie ma administratora globalnego. Użytkownicy w katalogu niezarządzanego zamknąć swoje konta, samodzielnie, bez konieczności kontaktowania się z administratorem.

Użytkownicy w katalogu niezarządzanego są często tworzone podczas samoobsługowego tworzenia konta. Przykładem może być Pracownik przetwarzający informacje w organizacji, który utworzył konto usługi w warstwie bezpłatna. Aby uzyskać więcej informacji na temat samoobsługowego tworzenia nowego konta, zobacz [co to jest samoobsługowego tworzenia konta usługi Azure Active Directory?](directory-self-service-signup.md).

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

## <a name="before-you-begin"></a>Przed rozpoczęciem

Przed zamknięciem Twoje konto, należy się upewnić, następujące elementy:

* Upewnij się, że jesteś użytkownikiem platformy Azure niezarządzane katalogu usługi AD. Nie można zamknąć konto, jeśli użytkownik należy do zarządzany katalog. Jeśli należą do zarządzany katalog, a chcesz zamknąć konto, skontaktuj się z administratorem. Aby uzyskać informacji dotyczących sposobu ustalenia, czy należysz do niezarządzanego katalogu, zobacz [usunąć użytkownika z niezarządzanej dzierżawy](https://docs.microsoft.com/flow/gdpr-dsr-delete#delete-the-user-from-unmanaged-tenant).

* Zapisz wszystkie dane, które chcesz zachować. Aby dowiedzieć się, jak przesłać żądanie eksportu, zobacz [dostęp i eksportowanie dzienników generowanych przez system dla niezarządzanych dzierżaw](https://docs.microsoft.com/power-platform/admin/powerapps-gdpr-dsr-guide-systemlogs#accessing-and-exporting-system-generated-logs-for-unmanaged-tenants).

> [!WARNING]
> Zamykanie konta usługi jest nieodwracalne. Po zamknięciu konta zostaną usunięte wszystkie dane osobowe. Nie jest już mają dostęp do swojego konta i danych skojarzonych z Twoim kontem.

## <a name="close-your-account"></a>Zamknij konto

Aby zamknąć niezarządzane pracy lub konta służbowego, wykonaj następujące kroki:

1. Zaloguj się do [zamknąć konto](https://go.microsoft.com/fwlink/?linkid=873123), przy użyciu konta, na którym chcesz zamknąć.

1. Na **Moje żądania danych**, wybierz opcję **zamknąć konto**.

    ![Moje żądania danych — Zamknij konto](./media/users-close-account/close-account.png)

1. Przejrzyj komunikat potwierdzający, a następnie wybierz pozycję **tak**.

    ![Moje żądania danych — upewnij się, zamknij](./media/users-close-account/confirm-close.png)

## <a name="next-steps"></a>Kolejne kroki

- [Co to jest samoobsługowego tworzenia konta usługi Azure Active Directory?](directory-self-service-signup.md)
- [Usuń użytkownika z niezarządzanej dzierżawy](https://docs.microsoft.com/flow/gdpr-dsr-delete#delete-the-user-from-unmanaged-tenant)
- [Uzyskiwanie dostępu do i eksportowanie dzienników generowanych przez system dla niezarządzanych dzierżaw](https://docs.microsoft.com/power-platform/admin/powerapps-gdpr-dsr-guide-systemlogs#accessing-and-exporting-system-generated-logs-for-unmanaged-tenants)
