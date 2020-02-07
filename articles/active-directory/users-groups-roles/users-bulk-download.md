---
title: Pobierz listę użytkowników (wersja zapoznawcza) w portalu Azure Active Directory | Microsoft Docs
description: Pobieranie rekordów użytkowników zbiorczo w centrum administracyjnym platformy Azure w Azure Active Directory.
services: active-directory
author: curtand
ms.author: curtand
manager: daveba
ms.date: 02/06/2020
ms.topic: conceptual
ms.service: active-directory
ms.subservice: users-groups-roles
ms.workload: identity
ms.custom: it-pro
ms.reviewer: krbain
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4716ff9547f64dc6551b4d4adb0a8578da9fa83e
ms.sourcegitcommit: db2d402883035150f4f89d94ef79219b1604c5ba
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/07/2020
ms.locfileid: "77063836"
---
# <a name="download-a-list-of-users-preview-in-azure-active-directory-portal"></a>Pobieranie listy użytkowników (wersja zapoznawcza) w portalu Azure Active Directory

Azure Active Directory (Azure AD) obsługuje operacje importowania użytkowników zbiorczych (tworzenia).

## <a name="required-permissions"></a>Wymagane uprawnienia

Aby pobrać listę użytkowników z centrum administracyjnego usługi Azure AD, należy zalogować się przy użyciu użytkownika przypisanego do co najmniej jednej roli administratora na poziomie organizacji w usłudze Azure AD. Osoby zapraszające gościa i deweloperzy aplikacji nie są uznawane za role administratorów.

## <a name="to-download-a-list-of-users"></a>Aby pobrać listę użytkowników

1. [Zaloguj się do swojej organizacji usługi Azure AD](https://aad.portal.azure.com) przy użyciu konta administratora użytkownika w organizacji.
2. Przejdź do Azure Active Directory > użytkowników. Następnie wybierz użytkowników, którzy mają zostać dołączeni do pobierania, zaznaczając pole w lewej kolumnie obok każdego użytkownika. Uwaga: w tej chwili nie ma możliwości wybrania opcji Wszyscy użytkownicy do eksportowania. Każdy z nich musi być wybrany indywidualnie.
3. W usłudze Azure AD wybierz pozycję **użytkownicy** > **Pobierz użytkowników**.
4. Na stronie **pobieranie użytkowników** wybierz pozycję **Rozpocznij** , aby wyświetlić plik CSV z listą właściwości profilu użytkownika. W przypadku wystąpienia błędów można pobrać i wyświetlić plik wyników na stronie wyników operacji zbiorczej. Plik zawiera przyczynę każdego błędu.

   ![Wybierz, gdzie chcesz wyświetlić listę użytkowników, których chcesz pobrać](./media/users-bulk-download/bulk-download.png)

   Plik do pobrania będzie zawierać przefiltrowaną listę użytkowników.

   Uwzględniane są następujące atrybuty użytkownika:

   - userPrincipalName
   - displayName
   - surname
   - mail (poczta)
   - givenName
   - Identyfikator obiektu
   - userType
   - Stanowisko
   - department
   - accountEnabled
   - usageLocation
   - Adres
   - state
   - trzeciego
   - physicalDeliveryOfficeName
   - city
   - postalCode
   - telephoneNumber
   - Telefon komórkowy
   - authenticationPhoneNumber
   - authenticationAlternativePhoneNumber
   - authenticationEmail
   - alternateEmailAddress
   - Grupy wiekowej
   - consentProvidedForMinor
   - legalAgeGroupClassification

## <a name="check-status"></a>Sprawdzanie stanu

Stan oczekujących żądań zbiorczych można zobaczyć na stronie **wyników operacji zbiorczej (wersja zapoznawcza)** .

   ![Sprawdź stan przekazywania na stronie wyników operacji zbiorczych](./media/users-bulk-download/bulk-center.png)

## <a name="bulk-download-service-limits"></a>Limity usługi pobierania zbiorczego

Każde działanie zbiorcze służące do tworzenia listy użytkowników może być uruchamiane przez maksymalnie jedną godzinę. Umożliwia to utworzenie i pobranie listy dla co najmniej 500 000 użytkowników.

## <a name="next-steps"></a>Następne kroki

- [Zbiorcze dodawanie użytkowników](users-bulk-add.md)
- [Usuwanie zbiorczych użytkowników](users-bulk-delete.md)
- [Użytkownicy przywracania zbiorczego](users-bulk-restore.md)
