---
title: Pobierz listę użytkowników (wersja zapoznawcza) w portalu Azure Active Directory | Microsoft Docs
description: Pobieranie rekordów użytkowników zbiorczo w centrum administracyjnym platformy Azure w Azure Active Directory.
services: active-directory
author: curtand
ms.author: curtand
manager: mtillman
ms.date: 07/15/2019
ms.topic: conceptual
ms.service: active-directory
ms.subservice: users-groups-roles
ms.workload: identity
ms.custom: it-pro
ms.reviewer: jeffsta
ms.collection: M365-identity-device-management
ms.openlocfilehash: fd0829afca05058892d3a0ceeb50c9955d792dc3
ms.sourcegitcommit: 12de9c927bc63868168056c39ccaa16d44cdc646
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/17/2019
ms.locfileid: "72517063"
---
# <a name="download-a-list-of-users-preview-in-azure-active-directory-portal"></a>Pobieranie listy użytkowników (wersja zapoznawcza) w portalu Azure Active Directory

Azure Active Directory (Azure AD) obsługuje operacje importowania użytkowników zbiorczych (tworzenia).

## <a name="required-permissions"></a>Wymagane uprawnienia

Aby pobrać listę użytkowników z centrum administracyjnego usługi Azure AD, należy zalogować się przy użyciu użytkownika przypisanego do co najmniej jednej roli administratora na poziomie organizacji w usłudze Azure AD. Osoby zapraszające gościa i deweloperzy aplikacji nie są uznawane za role administratorów.

## <a name="to-download-a-list-of-users"></a>Aby pobrać listę użytkowników

1. [Zaloguj się do swojej organizacji usługi Azure AD](https://aad.portal.azure.com) przy użyciu konta administratora użytkownika w organizacji.
1. W usłudze Azure AD wybierz pozycję **użytkownicy** > **Pobierz użytkowników**.
1. Na stronie **pobieranie użytkowników** wybierz pozycję **Rozpocznij** , aby wyświetlić plik CSV z listą właściwości profilu użytkownika. W przypadku wystąpienia błędów można pobrać i wyświetlić plik wyników na stronie wyników operacji zbiorczej. Plik zawiera przyczynę każdego błędu.

   ![Wybierz, gdzie chcesz wyświetlić listę użytkowników, których chcesz pobrać](./media/users-bulk-download/bulk-download.png)

   Plik do pobrania będzie zawierać przefiltrowaną listę użytkowników.

   Zostaną uwzględnione następujące atrybuty użytkownika: 

   - userPrincipalName
   - displayName
   - surname
   - mail (poczta)
   - GivenName
   - Obiektu
   - userType
   - Stanowiska
   - department
   - Menedżera
   - AccountEnabled
   - usageLocation
   - streetAddress
   - state
   - Trzeciego
   - physicalDeliveryOfficeName
   - city
   - Pocztowy
   - TelephoneNumber
   - urządzenie
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
